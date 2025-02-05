---
title: Zugreifen auf Apache Hadoop YARN-Anwendungsprotokolle unter Linux-basiertem HDInsight – Azure
description: Erfahren Sie, wie Sie auf einem Linux-basierten HDInsight-Cluster (Apache Hadoop) mithilfe der Befehlszeile und eines Webbrowsers auf YARN-Anwendungsprotokolle zugreifen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: 6eb24e85d1d7ffa4f3377d4c2fe8b168303c15f0
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091505"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Zugreifen auf Apache Hadoop YARN-Anwendungsprotokolle unter Linux-basiertem HDInsight

Erfahren Sie, wie Sie auf Protokolle für [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)-Anwendungen (Yet Another Resource Negotiator) in einem [Apache Hadoop](https://hadoop.apache.org/) Cluster in Azure HDInsight zugreifen.

## <a name="YARNTimelineServer"></a>YARN Timeline Server

Der [Apache Hadoop YARN Timeline Server](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) bietet allgemeine Informationen zu abgeschlossenen Anwendungen.

YARN Timeline Server umfasst die folgenden Arten von Daten:

* Die Anwendungs-ID, ein eindeutiger Bezeichner einer Anwendung
* Der Benutzer, der die Anwendung gestartet hat
* Informationen zu den erfolgten Versuchen, die Anwendung abzuschließen
* Die bei Anwendungsversuchen verwendeten Container

## <a name="YARNAppsAndLogs"></a>YARN-Anwendungen und -Protokolle

YARN unterstützt mehrere Programmierungsmodelle (u. a.[Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)), indem die Ressourcenverwaltung von der Zeitplanung/Überwachung von Anwendungen getrennt wird. YARN verwendet einen globalen *ResourceManager* (RM), workerknotenbezogene *NodeManager* (NMs) und anwendungsbezogene *ApplicationMaster* (AMs). Der anwendungsbezogene AM handelt Ressourcen (CPU, Arbeitsspeicher, Datenträger, Netzwerk) für die Ausführung Ihrer Anwendung mit dem RM aus. Der RM arbeitet mit NMs zusammen, um diese Ressourcen zu gewähren, die als *Container* zugewiesen werden. Der AM ist zuständig für die Nachverfolgung des Status der Container, die ihm vom RM zugewiesen wurden. Je nach Art der Anwendung kann diese viele Container benötigen.

Jede Anwendung kann aus mehreren *Anwendungsversuchen* bestehen. Tritt bei einer Anwendung ein Fehler auf, kann ein neuer Versuch unternommen werden. Jeder Versuch wird in einem Container ausgeführt. In gewisser Weise stellt ein Container den Kontext für die Standardeinheit für Aufgaben, die von einer YARN-Anwendung ausgeführt werden. Alle Aufgaben, die im Kontext eines Containers erledigt werden, erfolgen auf dem einzelnen Workerknoten, auf dem der Container zugeordnet wurde. Weitere Informationen finden Sie unter [Apache Hadoop YARN-Konzepte](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html).

Anwendungsprotokolle (und dazugehörige Containerprotokolle) sind für das Beheben von Problemen bei Hadoop-Anwendungen besonders wichtig. YARN bietet mit seinem Feature [Log Aggregation](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) ein nützliches Gerüst für das Sammeln, Zusammenführen und Speichern von Anwendungsprotokollen. Durch die Protokollaggregationsfunktion wird der Zugriff auf Anwendungsprotokolle deterministischer. Sie aggregiert Protokolle in allen Containern auf einem Workerknoten und speichert sie als eine aggregierte Protokolldatei pro Workerknoten. Das Protokoll wird, nachdem eine Anwendung beendet wurde, im Standarddateisystem gespeichert. Ihre Anwendung mag Hunderte oder Tausende von Containern verwenden, doch Protokolle für alle auf einem einzelnen Workerknoten vorhandenen Container werden immer zu einer zentralen Datei zusammengeführt. Daher wird nur ein Protokoll pro Workerknoten von Ihrer Anwendung genutzt. Die Protokollaggregation ist für HDInsight-Cluster ab Version 3.0 standardmäßig aktiviert. Aggregierte Protokolle befinden sich im Standardspeicher für den Cluster. Der folgende Pfad ist der HDFS-Pfad für die Protokolle:

    /app-logs/<user>/logs/<applicationId>

`user` steht hier für den Namen des Benutzers, der die Anwendung gestartet hat. `applicationId` ist der eindeutige Bezeichner, der einer Anwendung durch den YARN-RM zugewiesen wird.

Die zusammengeführten Protokolle sind nicht unmittelbar lesbar, da sie in einem [TFile][T-file]-[Binärformat ][binary-format] mit Indizierung nach Container geschrieben werden. Verwenden Sie die YARN-ResourceManager-Protokolle oder CLI-Tools, um diese Protokolle für relevante Anwendungen oder Container im Nur-Text-Format anzuzeigen.

## <a name="yarn-cli-tools"></a>YARN-CLI-Tools

Zur Verwendung der YARN CLI-Tools müssen Sie zuerst über SSH eine Verbindung mit dem HDInsight-Cluster herstellen. Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

Sie können diese Protokolle im Nur-Text-Format anzeigen, indem Sie einen der folgenden Befehle ausführen:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Geben Sie beim Ausführen dieser Befehle die &lt;AnwendungsID>, den &lt;Benutzer,-der-die-Anwendung-gestartet-hat>, die &lt;ContainerID> und die &lt;Workerknotenadresse> an.

## <a name="yarn-resourcemanager-ui"></a>YARN-ResourceManager-Benutzeroberfläche

Die YARN-ResourceManager-Benutzeroberfläche wird im Cluster-Hauptknoten ausgeführt. Der Zugriff erfolgt über die Ambari-Webbenutzeroberfläche. Führen Sie die folgenden Schritte aus, um die YARN-Protokolle anzeigen:

1. Navigieren Sie in Ihrem Webbrowser zu https://CLUSTERNAME.azurehdinsight.net. Ersetzen Sie CLUSTERNAME durch den Namen Ihres HDInsight-Clusters.
2. Wählen Sie aus der Liste der Dienste auf der linken Seite den Dienst **YARN**aus.

    ![Ausgewählter Apache Ambari Yarn-Dienst](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarn-service-selected.png)

3. Wählen Sie aus der Dropdownliste **QuickLinks** einen der Clusterhauptknoten und dann **ResourceManager-Protokoll** aus.

    ![Quicklinks für Apache Ambari Yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/hdi-yarn-quick-links.png)

    Eine Liste mit Links zu YARN-Protokollen wird angezeigt.

[YARN-timeline-server]:https://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
