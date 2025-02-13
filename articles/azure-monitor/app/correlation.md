---
title: Korrelation der Azure Application Insights-Telemetrie | Microsoft-Dokumentation
description: Korrelation der Application Insights-Telemetrie
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: fe52fe51b347b232e03bad943906413b90c853c0
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338175"
---
# <a name="telemetry-correlation-in-application-insights"></a>Telemetriekorrelation in Application Insights

In der Welt der Mikroservices müssen für jeden logischen Vorgang Aufgaben mit den verschiedenen Dienstkomponenten durchgeführt werden. Jede dieser Komponenten kann separat von [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) überwacht werden. Die Web-App-Komponente kommuniziert mit der Authentifizierungsanbieterkomponente, um die Anmeldeinformationen des Benutzers zu überprüfen, und mit der API-Komponente, um Daten zur Visualisierung abzurufen. Die API-Komponente kann Daten von anderen Diensten abfragen und mithilfe von Cacheanbieterkomponenten die Abrechnungskomponente über diesen Aufruf benachrichtigen. Application Insights unterstützt verteilte Telemetriekorrelation, mit der Sie erkennen können, welche Komponente für Fehler oder Leistungsbeeinträchtigungen verantwortlich ist.

In diesem Artikel wird das von Application Insights verwendete Datenmodell erläutert, mit dem die von mehreren Komponenten gesendeten Telemetriedaten korreliert werden. Es werden Techniken und Protokolle zur Kontextpropagierung erläutert. Darüber hinaus wird auch die Implementierung von Korrelationskonzepten in verschiedenen Sprachen und auf verschiedenen Plattformen behandelt.

## <a name="data-model-for-telemetry-correlation"></a>Datenmodell für Telemetriekorrelation

Application Insights definiert ein [Datenmodell](../../azure-monitor/app/data-model.md) für die verteilte Telemetriekorrelation. Um die Telemetrie mit dem logischen Vorgang zu verknüpfen, ist jedes Telemetrieelement mit einem Kontextfeld namens `operation_Id` versehen. Dieser Bezeichner wird von jedem Telemetrieelement in der verteilten Ablaufverfolgung gemeinsam genutzt. Selbst bei einem Verlust von Telemetriedaten von einer einzelnen Ebene können Sie weiterhin Telemetriedaten zuordnen, die von anderen Komponenten gemeldet wurden.

Der verteilte logische Vorgang besteht in der Regel aus einem Satz von kleineren Vorgängen, nämlich den Anforderungen, die von einer der Komponenten verarbeitet werden. Diese Vorgänge werden von einer [Anforderungstelemetrie](../../azure-monitor/app/data-model-request-telemetry.md) definiert. Jede Anforderungstelemetrie verfügt über eine eigene `id`, die diese eindeutig und global identifiziert. Für sämtliche Telemetrieelemente (z.B. Ablaufverfolgungen und Ausnahmen), die dieser Anforderung zugeordnet sind, sollte die `operation_parentId` auf den Wert der Anforderungs-`id` festgelegt werden.

Alle ausgehenden Vorgänge wie HTTP-Aufrufe an andere Komponenten werden durch eine [Abhängigkeitstelemetrie](../../azure-monitor/app/data-model-dependency-telemetry.md) dargestellt. Abhängigkeitstelemetrien definieren zudem ihre eigene `id`, die global eindeutig ist. Anforderungsabhängigkeiten, die durch diese Anforderungstelemetrie initiiert werden, verwenden diese `id` als `operation_parentId`.

Sie können eine Ansicht des verteilten logischen Vorgangs mit `operation_Id`, `operation_parentId` und `request.id` mit `dependency.id` erstellen. Diese Felder definieren auch die Kausalitätsreihenfolge der Telemetrieaufrufe.

In Microserviceumgebungen können Ablaufverfolgungen von Komponenten an unterschiedliche Speicherelemente weitergeleitet werden. Jede Komponente kann einen eigenen Instrumentierungsschlüssel in Application Insights aufweisen. Um Telemetriedaten für den logischen Vorgang abzurufen, ruft die Benutzeroberfläche von Application Insights Daten von jedem Speicherelement ab. Wenn die Anzahl der Speicherelemente sehr groß ist, benötigen Sie einen Hinweis, wo Sie als Nächstes suchen sollen. Zur Behebung dieses Problems definiert das Application Insights-Datenmodell zwei Felder: `request.source` und `dependency.target`. Das erste Feld identifiziert die Komponente, die die Abhängigkeitsanforderung initiiert hat, und das zweite Feld identifiziert die Komponente, die die Antwort des Abhängigkeitsaufrufs zurückgegeben hat.

## <a name="example"></a>Beispiel

Sehen Sie sich ein Beispiel für eine Anwendung namens Stock Prices an, die den aktuellen Marktpreis eines Aktienkurses mithilfe der externen API `Stock` angibt. Die Anwendung Stock Prices enthält die Seite `Stock page`, die vom Clientwebbrowser mit `GET /Home/Stock` geöffnet wird. Die Anwendung fragt die API `Stock` mit dem HTTP-Aufruf `GET /api/stock/value` ab.

Sie können die daraus resultierenden Telemetriedaten durch Ausführen einer Abfrage analysieren:

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

Beachten Sie, dass in den Ergebnissen alle Telemetrieelemente die Stamm-`operation_Id` nutzen. Wenn ein Ajax-Aufruf über die Seite erfolgt, wird der Abhängigkeitstelemetrie eine neue eindeutige ID (`qJSXU`) zugewiesen, und die ID der pageView dient als `operation_ParentId`. Die Serveranforderung verwendet dann die Ajax-ID als `operation_ParentId`.

| itemType   | name                      | id           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

Wenn der Aufruf `GET /api/stock/value` an einen externen Dienst erfolgt, sollten Sie die Identität dieses Servers herausfinden, damit Sie das Feld `dependency.target` entsprechend festlegen können. Wenn der externe Dienst keine Überwachung unterstützt, wird `target` auf den Hostnamen des Diensts festgelegt (z.B. `stock-prices-api.com`). Wenn sich dieser Dienst jedoch durch Zurückgeben eines vordefinierten HTTP-Headers identifiziert, enthält `target` die Dienstidentität, mit der Application Insights verteilte Ablaufverfolgungen durch Abfragen von Telemetriedaten von diesem Dienst erstellen kann.

## <a name="correlation-headers"></a>Korrelations-Header

Wir gehen zum [W3C Trace-Context](https://w3c.github.io/trace-context/) über, der Folgendes definiert:

- `traceparent`: Trägt die global eindeutige Vorgangs-ID und den eindeutigen Bezeichner des Aufrufs.
- `tracestate`: Trägt einen für das Ablaufverfolgungssystem spezifischen Kontext.

Die neuesten Versionen von Application Insights SDKs unterstützen das „Trace-Context“-Protokoll, aber möglicherweise müssen Sie sich explizit dafür entscheiden (die Abwärtskompatibilität mit dem alten Korrelationsprotokoll, das von Application Insights SDKs unterstützt wird, bleibt erhalten).

Das [Korrelations-HTTP-Protokoll, auch als „Request-ID“ bezeichnet,](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) befindet sich im Pfad für die Einstellung. Dieses Protokoll definiert zwei Header:

- `Request-Id`: Enthält die global eindeutige ID des Aufrufs.
- `Correlation-Context`: Enthält die Sammlung von Name/Wert-Paaren der Eigenschaften von verteilten Ablaufverfolgungen.

Application Insights definiert ferner die [Erweiterung](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) für das Korrelations-HTTP-Protokoll. Er verwendet Name/Wert-Paare für `Request-Context`, die die vom unmittelbaren Aufrufer oder Aufgerufenen verwendete Sammlung von Eigenschaften propagieren. Das Application Insights SDK legt mithilfe dieses Header die Felder `dependency.target` und `request.source` fest.

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>Aktivieren der Unterstützung für die verteilte Ablaufverfolgung von W3C für klassische ASP.NET-Apps
 
  > [!NOTE]
  > Ab `Microsoft.ApplicationInsights.Web` und `Microsoft.ApplicationInsights.DependencyCollector` ist keine Konfiguration mehr erforderlich. 

Die Unterstützung für den W3C Trace-Context erfolgt in abwärtskompatibler Weise, und es wird erwartet, dass die Korrelation mit Anwendungen funktioniert, die mit früheren Versionen des SDK instrumentiert sind (ohne W3C-Unterstützung). 

Wenn Sie aus irgendeinem Grund das weiterhin das `Request-Id`-Legacyprotokoll verwenden möchten, können Sie den Trace-Context mit folgender Konfiguration *deaktivieren*.

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Wenn Sie eine ältere Version des SDK ausführen, empfiehlt es sich, dieses zu aktualisieren oder die folgende Konfiguration anzuwenden, um den Trace-Context zu ermöglichen.
Dieses Feature steht ab Version 2.8.0-beta1 in den Paketen `Microsoft.ApplicationInsights.Web` und `Microsoft.ApplicationInsights.DependencyCollector` zur Verfügung.
Standardmäßig ist es deaktiviert. Um es zu aktivieren, ändern Sie `ApplicationInsights.config`:

- Fügen Sie unter `RequestTrackingTelemetryModule` das `EnableW3CHeadersExtraction`-Element mit dem Wert `true` hinzu.
- Fügen Sie unter `DependencyTrackingTelemetryModule` das `EnableW3CHeadersInjection`-Element mit dem Wert `true` hinzu.
- Fügen Sie `W3COperationCorrelationTelemetryInitializer` unter `TelemetryInitializers` hinzu, ähnlich wie im Folgenden: 

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers> 
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>Aktivieren der Unterstützung für die verteilte Ablaufverfolgung von W3C für ASP.NET Core-Apps

 > [!NOTE]
  > Ab `Microsoft.ApplicationInsights.AspNetCore`, Version 2.8.0, ist keine Konfiguration mehr erforderlich.
 
Die Unterstützung für den W3C Trace-Context erfolgt in abwärtskompatibler Weise, und es wird erwartet, dass die Korrelation mit Anwendungen funktioniert, die mit früheren Versionen des SDK instrumentiert sind (ohne W3C-Unterstützung). 

Wenn Sie aus irgendeinem Grund das weiterhin das `Request-Id`-Legacyprotokoll verwenden möchten, können Sie den Trace-Context mit folgender Konfiguration *deaktivieren*.

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

Wenn Sie eine ältere Version des SDK ausführen, empfiehlt es sich, dieses zu aktualisieren oder die folgende Konfiguration anzuwenden, um den Trace-Context zu ermöglichen.

Dieses Feature befindet sich in `Microsoft.ApplicationInsights.AspNetCore` Version 2.5.0-beta1 und `Microsoft.ApplicationInsights.DependencyCollector` Version 2.8.0-beta1.
Standardmäßig ist es deaktiviert. Um es zu aktivieren, legen Sie `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` auf `true` fest:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Aktivieren der Unterstützung für die verteilte Ablaufverfolgung von W3C für Java-Apps

- **Eingangskonfiguration**

  - Fügen Sie für Java EE-Apps dem Tag `<TelemetryModules>` in „ApplicationInsights.xml“ Folgendes hinzu:

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
  - Für Spring Boot-Apps fügen Sie die folgenden Eigenschaften hinzu:

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **Ausgangskonfiguration**

  Fügen Sie der Datei „AI-Agent.xml“ Folgendes hinzu:

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > Der Abwärtskompatibilitätsmodus ist standardmäßig aktiviert, und der Parameter `enableW3CBackCompat` ist optional. Verwenden sie ihn nur, wenn Sie die Abwärtskompatibilität deaktivieren möchten.
  >
  > Idealerweise deaktivieren Sie ihn, wenn alle Dienste auf neuere Versionen von SDKs aktualisiert wurden, die das W3C-Protokoll unterstützen. Es wird dringend empfohlen, so bald wie möglich zu diesen neueren SDKs zu wechseln.

> [!IMPORTANT]
> Stellen Sie sicher, dass die Eingangs- und Ausgangskonfiguration genau gleich sind.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>Aktivieren der Unterstützung der verteilten W3C-Ablaufverfolgung für Web-Apps

Dieses Feature befindet sich in `Microsoft.ApplicationInsights.JavaScript`. Standardmäßig ist es deaktiviert. Um es zu aktivieren, verwenden Sie die Konfiguration von `distributedTracingMode`. AI_AND_W3C wird für Abwärtskompatibilität mit beliebigen mit älteren Application Insights-Versionen instrumentierten Diensten bereitgestellt:

- **NPM-Setup (bei Verwendung von Snippet-Setup ignorieren)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...Other Configuration Options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **Snippet-Setup (bei Verwendung von NPM-Setup ignorieren)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...Other Configuration Options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing und Application Insights

Die [OpenTracing-Datenmodellspezifikation](https://opentracing.io/) und Application Insights-Datenmodelle sind in folgender Weise zugeordnet:

| Application Insights                  | OpenTracing                                       |
|------------------------------------   |-------------------------------------------------  |
| `Request`, `PageView`                 | `Span` mit `span.kind = server`                  |
| `Dependency`                          | `Span` mit `span.kind = client`                  |
| `Id` der `Request` und `Dependency`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | `Reference` vom Typ `ChildOf` (die übergeordnete Spanne)   |

Weitere Informationen finden Sie unter [Application Insights-Telemetriedatenmodell](../../azure-monitor/app/data-model.md). 

Definitionen von OpenTracing-Konzepten finden Sie in den [Spezifikationen](https://github.com/opentracing/specification/blob/master/specification.md) und [semantischen Konventionen](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) für OpenTracing.

## <a name="telemetry-correlation-in-net"></a>Telemetriekorrelation in .NET

Im Lauf der Zeit wurden in .NET verschiedene Möglichkeiten zur Korrelation von Telemetrie- und Diagnoseprotokollen definiert:

- `System.Diagnostics.CorrelationManager` ermöglicht die Nachverfolgung von [LogicalOperationStack und ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx). 
- `System.Diagnostics.Tracing.EventSource` und die Ereignisablaufverfolgung für Windows (ETW) definieren die [SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx)-Methode.
- `ILogger` verwendet [Protokollbereiche](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes). 
- Windows Communication Foundation (WCF) und HTTP bieten eine Propagierung des Kontexts „aktuell“.

Allerdings wurde durch diese Methoden nicht die Unterstützung für automatische verteilte Ablaufverfolgung aktiviert. `DiagnosticSource` ist eine Möglichkeit zur Unterstützung von automatischen computerübergreifenden Korrelationen. .NET-Bibliotheken unterstützen Diagnosequellen und ermöglichen die automatische computerübergreifende Propagierung des Korrelationskontexts durch die Übertragungsmethode, z.B. HTTP.

Im [Leitfaden zu Aktivitäten](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) in `DiagnosticSource` werden die Grundlagen von Nachverfolgungsaktivitäten erläutert.

ASP.NET Core 2.0 unterstützt die Extraktion von HTTP-Headern und das Starten einer neuen Aktivität.

`System.Net.Http.HttpClient` ab Version 4.1.0 unterstützt die automatische Injektion der Korrelations-HTTP-Header und die Nachverfolgung des HTTP-Aufrufs als Aktivität.

Für das klassische ASP.NET ist das neue HTTP-Modul [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) verfügbar. Dieses Modul implementiert Telemetriekorrelationen mithilfe von `DiagnosticSource`. Es startet Aktivitäten basierend auf den eingehenden Anforderungsheadern. Außerdem korreliert es Telemetriedaten aus den verschiedenen Phasen der Anforderungsverarbeitung, selbst in Fällen, in denen jede Phase der Verarbeitung von Internetinformationsdienste (Internet Information Services, IIS) in einem anderen verwalteten Thread ausgeführt wird.

Das Application Insights SDK ab Version 2.4.0-beta1 verwendet `DiagnosticSource` und `Activity`, um Telemetriedaten zu sammeln und mit der aktuellen Aktivität zu verknüpfen.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Telemetriekorrelation im Java SDK

Das [Application Insights SDK für Java](../../azure-monitor/app/java-get-started.md) unterstützt ab Version 2.0.0 die automatische Korrelation von Telemetriedaten. Das SDK füllt `operation_id` automatisch für alle Telemetriedaten (z.B. Ablaufverfolgungen, Ausnahmen und benutzerdefinierte Ereignisse) auf, die im Zusammenhang mit einer Anforderung ausgegeben werden. Es sorgt auch dafür, dass die Korrelationsheader (weiter oben beschrieben) für Dienst-zu-Dienst-Aufrufe über HTTP weitergeben werden, wenn der [Java SDK-Agent](../../azure-monitor/app/java-agent.md) konfiguriert ist.

> [!NOTE]
> Für die Korrelationsfunktion werden nur Aufrufe unterstützt, die über Apache HttpClient erfolgen. Wenn Sie mit Spring Rest Template oder Feign arbeiten, können beide aufgesetzt auf Apache HttpClient verwendet werden.

Derzeit wird die automatische Kontextweitergabe über verschiedene Messagingtechnologien (z.B. Kafka, RabbitMQ oder Azure Service Bus) nicht unterstützt. Es ist jedoch möglich, solche Szenarien manuell mithilfe der APIs `trackDependency` und `trackRequest` zu schreiben. In diesen APIs entsprechen Telemetriedaten den Abhängigkeiten einer Nachricht, die von einem Producer in die Warteschlange eingereiht wird, und die Anforderung entspricht einer Nachricht, die von einem Consumer verarbeitet wird. In diesem Fall müssen sowohl `operation_id` als auch `operation_parentId` in den Eigenschaften der Nachricht weitergegeben werden.

### <a name="telemetry-correlation-in-asynchronous-java-application"></a>Telemetriekorrelation in asynchroner Java-Anwendung

Zum Korrelieren von Telemetriedaten in einer asynchronen Spring Boot-Anwendung folgen Sie [diesem](https://github.com/Microsoft/ApplicationInsights-Java/wiki/Distributed-Tracing-in-Asynchronous-Java-Applications) ausführlichen Artikel. Er enthält Anweisungen für das Instrumentieren von [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) und [ThreadPoolTaskScheduler](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html) für Spring. 


<a name="java-role-name"></a>
## <a name="role-name"></a>Rollenname

Gelegentlich möchten Sie möglicherweise die Art und Weise anpassen, wie Komponentennamen in der [Anwendungsübersicht](../../azure-monitor/app/app-map.md) angezeigt werden. Dazu können Sie `cloud_RoleName` manuell festlegen, indem Sie eine der folgenden Aktionen ausführen:

- Bei Verwendung von Spring Boot mit dem Application Insights Spring Boot-Startprogramm besteht die einzige erforderliche Änderung darin, Ihren benutzerdefinierten Namen für die Anwendung in der Datei „application.properties“ festzulegen.

  `spring.application.name=<name-of-app>`

  Der Spring Boot-Starter weist `cloudRoleName` automatisch den Wert zu, den Sie für die `spring.application.name`-Eigenschaft eingeben.

- Wenn Sie `WebRequestTrackingFilter` verwenden, legt der `WebAppNameContextInitializer` automatisch den Anwendungsnamen fest. Fügen Sie Folgendes zu Ihrer Konfigurationsdatei (ApplicationInsights.xml) hinzu:

  ```XML
  <ContextInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
  </ContextInitializers>
  ```

- Wenn Sie die Cloudkontextklasse verwenden:

  ```Java
  telemetryClient.getContext().getCloud().setRole("My Component Name");
  ```

## <a name="next-steps"></a>Nächste Schritte

- Schreiben Sie [benutzerdefinierte Telemetriedaten](../../azure-monitor/app/api-custom-events-metrics.md).
- Informationen zu Szenarien mit erweiterter Korrelation in ASP.NET Core und ASP.NET finden Sie im Artikel zum [Nachverfolgen von benutzerdefinierten Vorgängen](custom-operations-tracking.md).
- Erfahren Sie mehr über das [Festlegen von cloud_RoleName](../../azure-monitor/app/app-map.md#set-cloud-role-name) für andere SDKs.
- Integrieren Sie alle Komponenten Ihres Microservices in Application Insights. Sehen Sie sich die [unterstützten Plattformen](../../azure-monitor/app/platforms.md) an.
- Lesen Sie die Informationen zu den Application Insights-Typen im [Datenmodell](../../azure-monitor/app/data-model.md).
- Informationen zum [Erweitern und Filtern von Telemetriedaten](../../azure-monitor/app/api-filtering-sampling.md).
- Lesen Sie die [Konfigurationsreferenz für Application Insights](configuration-with-applicationinsights-config.md).
