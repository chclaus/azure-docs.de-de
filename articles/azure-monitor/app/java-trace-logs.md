---
title: Untersuchen von Java-Ablaufverfolgungsprotokollen in Azure Application Insights | Microsoft-Dokumentation
description: Durchsuchen von Log4J- oder Logback-Ablaufverfolgungen in Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/18/2019
ms.author: mbullwin
ms.openlocfilehash: ac9bd6021b5fcec36e3aadfdf4c30020971f3be5
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299250"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Untersuchen von Java-Ablaufverfolgungsprotokollen in Application Insights
Wenn Sie für die Ablaufverfolgung Logback oder Log4J (Version 1.2 bzw. 2.0) verwenden, werden Ihre Ablaufverfolgungsprotokolle automatisch an Application Insights gesendet. Hier können Sie sie durchsuchen und untersuchen.

## <a name="using-the-application-insights-java-agent"></a>Verwenden des Java-Agents von Application Insights

Sie können den Java-Agent von Application Insights so konfigurieren, dass Ihre Protokolle automatisch erfasst werden, indem Sie das Feature in der `AI-Agent.xml`-Datei aktivieren:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">
         <Logging enabled="true" />
      </BuiltIn>
   </Instrumentation>
   <AgentLogger />
</ApplicationInsightsAgent>
```

Alternativ können Sie die folgenden Anweisungen befolgen.

## <a name="install-the-java-sdk"></a>Installieren des Java SDK

Führen Sie die Anweisungen zum Installieren des [Application Insights SDK für Java][java] durch, sofern dies noch nicht geschehen ist.

## <a name="add-logging-libraries-to-your-project"></a>Hinzufügen von Protokollierungsbibliotheken zu Ihrem Projekt
*Wählen Sie die geeignete Methode für Ihr Projekt.*

#### <a name="if-youre-using-maven"></a>Wenn Sie Maven verwenden...
Wenn Ihr Projekt bereits für die Verwendung von Maven für den Buildprozess eingerichtet ist, fügen Sie einen der folgenden Codeabschnitte Ihrer Datei "pom.xml" hinzu:

Aktualisieren Sie dann die Projektabhängigkeiten, damit die Binärdateien heruntergeladen werden.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2. 0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1. 2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Wenn Sie Gradle verwenden...
Wenn Ihr Projekt bereits für die Verwendung von Gradle für Buildprozesse eingerichtet ist, fügen Sie eine der folgenden Zeilen der Gruppe `dependencies` in der Datei "build.gradle" hinzu:

Aktualisieren Sie dann die Projektabhängigkeiten, damit die Binärdateien heruntergeladen werden.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2. 0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1. 2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Andernfalls...
Befolgen Sie die Richtlinien für die manuelle Installation des Application Insights Java SDK, laden Sie die JAR-Datei für den entsprechenden Appender herunter (klicken Sie dazu auf der Maven-Hauptseite im Downloadabschnitt auf den Link „jar“), und fügen Sie die heruntergeladene JAR-Datei für den Appender zum Projekt hinzu.

| Protokollierungstool | Download | Bibliothek |
| --- | --- | --- |
| Logback |[JAR-Datei für Logback-Appender](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2. 0 |[JAR-Datei für Log4J-v2-Appender](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4J v1. 2 |[JAR-Datei für Log4J-v1.2-Appender](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Hinzufügen des Appenders zu Ihrem Protokollierungsframework
Zum Starten von Ablaufverfolgungen führen Sie den relevanten Codeausschnitt mit der Konfigurationsdatei für Log4J oder Logback zusammen: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2. 0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1. 2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Die Application Insights-Appender können von jeder konfigurierten Protokollierung und müssen nicht unbedingt von der Stammprotokollierung referenziert werden (siehe die obigen Codebeispiele).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Untersuchen Ihrer Ablaufverfolgungen im Application Insights-Portal
Nachdem Sie das Projekt so konfiguriert haben, dass Ablaufverfolgungen an Application Insights gesendet werden, können Sie diese Ablaufverfolgungen im Application Insights-Portal auf dem Blatt [Suche][diagnostic] anzeigen und durchsuchen.

Über Protokollierungen übermittelte Ausnahmen werden im Portal als Ausnahmetelemetrie angezeigt.

![Öffnen Sie im Application Insights-Portal das Blatt „Suche“.](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Nächste Schritte
[Diagnosesuche][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


