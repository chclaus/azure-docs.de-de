---
title: Erstellen einer Azure Time Series Insights-Umgebung | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie eine neue Time Series Insights-Umgebung über das Azure-Portal erstellen.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/27/2019
ms.custom: seodec18
ms.openlocfilehash: 584172a9b248a9d151ba9a980bf4e52ed1e1b926
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141996"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Erstellen einer neuen Time Series Insights-Umgebung über das Azure-Portal

In diesem Artikel wird beschrieben, wie Sie eine neue Time Series Insights-Umgebung über das Azure-Portal erstellen.

Time Series Insights ermöglicht Ihnen den Einstieg in die Visualisierung und Abfrage des Dateneingangs in Azure IoT Hubs und Event Hubs in wenigen Minuten, sodass Sie große Mengen an Zeitreihendaten innerhalb von Sekunden abfragen können.  Dieser Dienst wurde für die Skalierung des Internets der Dinge (Internet of Things, IoT) entwickelt und kann Daten im Terabytebereich verarbeiten.

## <a name="steps-to-create-the-environment"></a>Schritte zum Erstellen der Umgebung

Führen Sie zum Erstellen einer Umgebung die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie die Schaltfläche **+ Ressource erstellen** aus.

1. Wählen Sie die Kategorie **Internet der Dinge (IoT)** und dann **Time Series Insights** aus.

   [![Erstellen der Time Series Insights-Umgebung](media/time-series-insights-get-started/1-new-tsi.png)](media/time-series-insights-get-started/1-new-tsi.png#lightbox)

1. Wählen Sie auf der Seite **Time Series Insights** die Option **Erstellen** aus.

1. Füllen Sie die erforderlichen Parameter aus. In der folgenden Tabelle werden die einzelnen Parameter erläutert:
   
   [![Erstellen der Time Series Insights-Ressourcengruppe](media/time-series-insights-get-started/2-create-tsi.png)](media/time-series-insights-get-started/2-create-tsi.png#lightbox)
   
   Einstellung|Empfohlener Wert|BESCHREIBUNG
   ---|---|---
   Umgebungsname | Ein eindeutiger Name | Unter diesem Namen wird die Umgebung im [Time Series-Explorer](https://insights.timeseries.azure.com) dargestellt.
   Subscription | Ihr Abonnement | Wenn Sie über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, das die Ereignisquelle vorzugsweise enthält. Time Series Insights kann Azure IoT Hub- und Event Hub-Ressourcen im gleichen Abonnement automatisch erkennen.
   Resource group | Erstellen einer neuen oder Verwenden einer vorhandenen Ressourcengruppe | Eine Ressourcengruppe ist eine Sammlung von Azure-Ressourcen, die zusammen verwendet werden. Sie können eine vorhandene Ressourcengruppe auswählen, z.B. die Gruppe, die Ihren Event Hub oder IoT Hub enthält. Oder Sie können eine neue Ressourcengruppe erstellen, wenn die Ressource nicht mit den anderen Ressourcen verbunden ist.
   Location | Am nächsten zu Ihrer Ereignisquelle gelegen | Wählen Sie vorzugsweise denselben Rechenzentrumsstandort aus, der die Daten Ihrer Ereignisquelle enthält, um so beim Verschieben von Daten außerhalb der Region zusätzliche regionsübergreifende und zonenübergreifende Breitbandkosten und eine erhöhte Latenz zu vermeiden.
   Tarif | S1 | Wählen Sie den erforderlichen Durchsatz aus. Wählen Sie für die niedrigsten Kosten und die Anfangskapazität „S1“ aus.
   Capacity | 1 | Die Kapazität ist der Multiplikator, der auf die Eingangsrate, Speicherkapazität und Kosten der ausgewählten SKU angewendet wird.  Die Kapazität einer Umgebung kann nach der Erstellung geändert werden. Wählen Sie für die niedrigsten Kosten die Kapazität „1“ aus. 
  
1. Wählen Sie **Erstellen** aus, um den Bereitstellungsprozess zu starten. Dies kann einige Minuten dauern.

1. Wählen Sie das Symbol **Benachrichtigungen** (Glockensymbol) aus, um den Bereitstellungsprozess zu überwachen.

   [![Überwachen der Benachrichtigungen](media/time-series-insights-get-started/3-notifications.png)](media/time-series-insights-get-started/3-notifications.png#lightbox)

    Wenn die Bereitstellung erfolgreich verlaufen ist, können Sie **Zu Ressource wechseln** auswählen, um andere Eigenschaften zu konfigurieren, die Sicherheit mit Datenzugriffsrichtlinien festzulegen, Ereignisquellen hinzuzufügen und weitere Aktionen auszuführen.

1. Wählen Sie in der **Übersicht** der Ressource rechts oben das **Stecknadelsymbol** aus, um künftig ganz einfach auf Ihre Time Series Insights Umgebung zugreifen zu können.

   [![Anheften von Time Series Insights an das Dashboard](media/time-series-insights-get-started/4-pin-create.png)](media/time-series-insights-get-started/4-pin-create.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

* [Definieren von Datenzugriffsrichtlinien](time-series-insights-data-access.md) zum Schützen Ihrer Umgebung

* [Hinzufügen einer Event Hub-Ereignisquelle ](time-series-insights-how-to-add-an-event-source-eventhub.md) zu Ihrer Azure Time Series Insights-Umgebung

* [Senden von Ereignissen](time-series-insights-send-events.md) an die Ereignisquelle

* Anzeigen Ihrer Umgebung im [Time Series Insights-Explorer](https://insights.timeseries.azure.com)
