---
title: Endlose Orchestrierungen in Durable Functions – Azure
description: Es wird beschrieben, wie Sie endlose Orchestrierungen implementieren, indem Sie die Erweiterung „Durable Functions“ für Azure Functions verwenden.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: d96229bb5e3d288915b64e5a7ce29a8651f2a181
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177379"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Endlose Orchestrierungen in Durable Functions (Azure Functions)

*Endlose Orchestrierungen* sind Orchestratorfunktionen, die niemals enden. Sie sind nützlich, wenn Sie [Durable Functions](durable-functions-overview.md) für Aggregatoren und Szenarien einsetzen möchten, für die eine Endlosschleife erforderlich ist.

## <a name="orchestration-history"></a>Orchestrierungsverlauf

Wie im Thema [Orchestrierungsverlauf](durable-functions-orchestrations.md#orchestration-history) beschrieben wird, verfolgt das Durable Task-Framework den Verlauf jeder einzelnen Funktionsorchestrierung nach. Dieser Verlauf nimmt ständig an Umfang zu, solange die Orchestratorfunktion neue Arbeit plant. Wenn die Orchestratorfunktion in eine Endlosschleife eintritt und fortlaufend Arbeit plant, kann dieser Verlauf eine kritische Größe erreichen und zu erheblichen Leistungsproblemen führen. Das Konzept der *endlosen Orchestrierung* wurde entworfen, um diese Art von Problemen für Anwendungen zu lösen, die Endlosschleifen benötigen.

## <a name="resetting-and-restarting"></a>Zurücksetzen und Neustarten

Anstatt Endlosschleifen zu verwenden, setzen Orchestratorfunktionen ihren Status zurück, indem sie die [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_)-Methode aufrufen. Bei dieser Methode wird ein einzelner Parameter verwendet, der per JSON serialisierbar ist. Er wird zur neuen Eingabe für die nächste Generierung einer Orchestratorfunktion.

Wenn `ContinueAsNew` aufgerufen wird, reiht die Instanz vor dem Beenden eine Nachricht an sich selbst in die Warteschlange ein. Die Nachricht bewirkt, dass die Instanz mit dem neuen Eingabewert neu gestartet wird. Es wird dieselbe Instanz-ID beibehalten, aber der Verlauf der Orchestratorfunktion wird praktisch abgeschnitten.

> [!NOTE]
> Das Durable Task Framework behält dieselbe Instanz-ID bei, aber intern wird eine neue *Ausführungs-ID* für die Orchestratorfunktion erstellt, die mit `ContinueAsNew` zurückgesetzt wird. Diese Ausführungs-ID wird im Allgemeinen nicht extern verfügbar gemacht, aber es kann hilfreich sein, sie zu kennen, wenn das Debuggen der Orchestrierungsausführung erfolgen soll.

## <a name="periodic-work-example"></a>Beispiel für regelmäßige Arbeit

Ein Anwendungsfall für endlose Orchestrierungen ist Code, der unendlich lange regelmäßig Arbeitsschritte ausführen muss.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (nur Functions 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

Der Unterschied zwischen diesem Beispiel und einer per Timer ausgelösten Funktion besteht darin, dass Bereinigungstriggerzeiten hier nicht auf einem Zeitplan basieren. Beispiel: Ein CRON-Zeitplan, für den stündlich eine Funktion ausgeführt wird (um 1:00, 2:00, 3:00 Uhr usw.), können ggf. Probleme mit Überlappungen auftreten. Wenn die Bereinigung in diesem Beispiel aber 30 Minuten dauert, wird sie um 1:00, 2:30, 4:00 Uhr usw. geplant, sodass es nicht zu Überlappungen kommt.

## <a name="starting-an-eternal-orchestration"></a>Starten einer endlosen Orchestrierung
Verwenden Sie die [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)-Methode, um eine endlose Orchestrierung zu starten. Dies unterscheidet sich nicht vom Auslösen einer beliebigen anderen Orchestrierungsfunktion.  

> [!NOTE]
> Wenn Sie sicherstellen müssen, dass eine ewige Singleton-Orchestrierung ausgeführt wird, ist es wichtig, dass Sie beim Starten der Orchestrierung dieselbe `id` der beibehalten. Weitere Informationen finden Sie unter [Instanzverwaltung](durable-functions-instance-management.md).

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [OrchestrationClient] DurableOrchestrationClientBase client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Beenden einer endlosen Orchestrierung

Wenn eine Orchestratorfunktion schließlich abgeschlossen werden soll, müssen Sie lediglich *nicht* `ContinueAsNew` aufrufen und die Funktion enden lassen.

Falls sich eine Orchestratorfunktion in einer Endlosschleife befindet und beendet werden muss, können Sie die [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)-Methode verwenden. Weitere Informationen finden Sie unter [Instanzverwaltung](durable-functions-instance-management.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Singleton-Orchestratoren in Durable Functions (Azure Functions)](durable-functions-singletons.md)
