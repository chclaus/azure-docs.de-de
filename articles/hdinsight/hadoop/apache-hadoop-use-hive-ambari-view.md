---
title: Verwenden von Apache Ambari-Ansichten für die Arbeit mit Hive in HDInsight (Apache Hadoop) – Azure
description: Erfahren Sie, wie Sie die Hive-Ansicht im Webbrowser zum Übermitteln von Hive-Abfragen verwenden. Die Hive-Ansicht ist Teil der Ambari-Webbenutzeroberfläche, die mit dem Linux-basierten HDInsight-Cluster bereitgestellt wird.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: hrasheed
ms.openlocfilehash: da4d1ed7dec8b3b0bc61dd2959a868d03875039c
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077010"
---
# <a name="use-apache-ambari-hive-view-with-apache-hadoop-in-hdinsight"></a>Verwenden der Apache Ambari-Hive-Ansicht mit Apache Hadoop in HDInsight

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

In diesem Artikel wird das folgende Thema erläutert: Ausführen von Apache Hive-Abfragen mithilfe der Ambari-Hive-Ansicht. In der Hive-Ansicht können Sie Hive-Abfragen direkt in Ihrem Webbrowser erstellen, optimieren und ausführen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Schnellstart: Erste Schritte mit Apache Hadoop und Apache Hive in Azure HDInsight mit einer Resource Manager-Vorlage](./apache-hadoop-linux-tutorial-get-started.md).
* Aus einem Webbrowser

## <a name="run-a-hive-query"></a>Ausführen einer Hive-Abfrage

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren Cluster aus.  Anweisungen dazu finden Sie unter [Auflisten und Anzeigen von Clustern](../hdinsight-administer-use-portal-linux.md#showClusters). Der Cluster wird in einem neuen Blatt des Portals geöffnet.

2. Wählen Sie aus **Clusterdashboards** die Option **Ambari-Ansichten** aus. Wenn Sie aufgefordert werden, sich zu authentifizieren, verwenden Sie den Kontonamen und das Kennwort der Clusteranmeldung (standardmäßig `admin`), die Sie bei der Erstellung des Clusters angegeben haben.

3. Wählen Sie aus der Liste der Ansichten die __Hive-Ansicht__ aus.

    ![Apache Ambari – Auswahl der Apache Hive-Ansicht](./media/apache-hadoop-use-hive-ambari-view/select-apache-hive-view.png)

    Die Seite „Hive-Ansicht“ ähnelt der folgenden Abbildung:

    ![Abbildung des Arbeitsblatts „Abfragen“ für die Hive-Ansicht](./media/apache-hadoop-use-hive-ambari-view/ambari-worksheet-view.png)

4. Kopieren Sie folgende HiveQL-Anweisungen aus der Registerkarte __Query__ (Abfrage) in das Arbeitsblatt:

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' 
        GROUP BY t4;
    ```

    Diese Anweisungen führen die folgenden Aktionen aus:

   * `DROP TABLE`: Löscht Tabelle und Datendatei, falls die Tabelle bereits existiert.

   * `CREATE EXTERNAL TABLE`: Erstellt eine neue „externe“ Tabelle in Hive.
     Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben an ihrem ursprünglichen Speicherort.

   * `ROW FORMAT`: Gibt an, wie die Daten formatiert werden. In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.

   * `STORED AS TEXTFILE LOCATION`: Zeigt den Speicherort für die Daten an, und dass sie als Text gespeichert sind.

   * `SELECT`: Wählt die Anzahl aller Zeilen aus, bei denen die Spalte t4 den Wert [ERROR] enthält.

   > [!IMPORTANT]  
   > Belassen Sie die Auswahl der __Database__ (Datenbank) bei __default__ (Standard). Die Beispiele in diesem Dokument verwenden die Standarddatenbank, die in HDInsight enthalten ist.

5. Wählen Sie zum Starten der Abfrage **Execute** (Ausführen) unter dem Arbeitsblatt aus. Die Schaltfläche wird nun orange angezeigt, und der Text ändert sich in **Stop** (Beenden).

6. Nach Abschluss der Abfrage werden in der Registerkarte **Results** (Ergebnisse) die Ergebnisse des Vorgangs angezeigt. Der folgende Text ist das Ergebnis der Abfrage:

        loglevel       count
        [ERROR]        3

    Sie können auf der Registerkarte **LOG** die vom Auftrag erstellten Protokollinformationen anzeigen.

   > [!TIP]  
   > Laden Sie Ergebnisse aus dem Dropdown-Dialogfeld **Actions** (Aktionen) unter der Registerkarte **Results** (Ergebnisse) herunter oder speichern Sie sie.

### <a name="visual-explain"></a>Visuelle Erläuterung

Klicken Sie auf die Registerkarte **Visual Explain** (Visuelle Erläuterung) unter dem Arbeitsblatt, um eine Visualisierung des Abfrageplans anzuzeigen.

Die Ansicht **Visuelle Erläuterung** der Abfrage kann das Verständnis des Ablaufs komplexer Abfragen erleichtern.

### <a name="tez-ui"></a>Tez-Benutzeroberfläche

Klicken Sie auf die Registerkarte **Tez UI** unter dem Arbeitsblatt, um die Tez-Benutzeroberfläche für die Abfrage anzuzeigen.

> [!IMPORTANT]  
> Tez wird nicht für die Auflösung aller Abfragen verwendet. Viele Abfragen können ohne Tez aufgelöst werden.

## <a name="view-job-history"></a>Anzeige des Auftragsverlaufs

Die Registerkarte __Jobs__ (Aufträge) zeigt einen Verlauf der Hive-Abfragen an.

![Apache Hive-Ansicht mit Verlauf auf der Registerkarte für Aufträge](./media/apache-hadoop-use-hive-ambari-view/apache-hive-job-history.png)

## <a name="database-tables"></a>Datenbanktabellen

Sie können die Registerkarte __Tables__ (Tabellen) verwenden, um innerhalb einer Hive-Datenbank mit Tabellen zu arbeiten.

![Abbildung der Registerkarte mit Apache Hive-Tabellen](./media/apache-hadoop-use-hive-ambari-view/hdinsight-tables-tab.png)

## <a name="saved-queries"></a>Gespeicherte Abfragen

In der Registerkarte **Query** können Sie optional auch Abfragen speichern. Nachdem Sie eine Abfrage gespeichert haben, können Sie diese von der Registerkarte __Saved Queries__ aus wiederverwenden.

![Apache Hive-Ansicht mit Registerkarte für gespeicherte Abfragen](./media/apache-hadoop-use-hive-ambari-view/ambari-saved-queries.png)

> [!TIP]  
> Gespeicherte Abfragen werden im Standard-Clusterspeicher gespeichert. Sie finden die gespeicherten Abfragen im Pfad `/user/<username>/hive/scripts`. Diese werden als Textdateien vom Typ `.hql` gespeichert.
>
> Wenn Sie den Cluster löschen, den Speicher jedoch beibehalten, können Sie die Abfragen mit einem Hilfsprogramm wie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) oder Data Lake Storage-Explorer (aus dem [Azure-Portal](https://portal.azure.com)) abrufen.

## <a name="user-defined-functions"></a>Benutzerdefinierte Funktionen

Sie können Hive über benutzerdefinierte Funktionen (UDF) erweitern. Mit einer UDF-Datei können Sie Funktionen oder Logik implementieren, die sich nicht einfach in HiveQL modellieren lässt.

Deklarieren und speichern Sie eine Gruppe von UDFs mit der Registerkarte **UDF** ganz oben in der Hive-Ansicht. Diese UDFs können im **Abfrage-Editor** verwendet werden.

![Apache Hive-Ansicht mit Anzeige der UDF-Registerkarte](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

Nachdem Sie der Hive-Ansicht eine benutzerdefinierte Funktion hinzugefügt haben, wird ganz unten im **Abfrage-Editor** die Schaltfläche **Insert udfs** angezeigt. Wenn Sie diesen Eintrag wählen, wird eine Dropdownliste mit den UDFs angezeigt, die in der Hive-Ansicht definiert sind. Durch das Auswählen einer UDF werden Ihrer Abfrage HiveQL-Anweisungen hinzugefügt, um die UDF zu aktivieren.

Beispielsweise können Sie eine UDF mit den folgenden Eigenschaften definieren:

* Ressourcenname: myudfs

* Ressourcenpfad: /myudfs.jar

* UDF-Name: myawesomeudf

* UDF-Klassenname: com.myudfs.Awesome

Mit der Schaltfläche **Insert udfs** wird ein Eintrag mit dem Namen **myudfs** angezeigt, der über eine weitere Dropdownliste für jede UDF verfügt, die für diese Ressource definiert ist. In diesem Fall ist das **myawesomeudf**. Wenn Sie diesen Eintrag auswählen, wird am Anfang der Abfrage Folgendes hinzugefügt:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Sie können die UDF dann in Ihrer Abfrage verwenden. Beispiel: `SELECT myawesomeudf(name) FROM people;`.

Weitere Informationen zur Verwendung von UDFs mit Hive unter HDInsight finden Sie in den folgenden Artikeln:

* [Verwenden von Python mit Apache Hive und Apache Pig in Azure HDInsight](python-udf-hdinsight.md)
* [Hinzufügen einer benutzerdefinierten Apache Hive UDF zu HDInsight](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Hive-Einstellungen

Sie können verschiedene Hive-Einstellungen ändern, z.B. die Ausführungs-Engine für Hive von Tez (Standard) in MapReduce.

## <a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu Hive in HDInsight:

* [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](hdinsight-use-hive.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Apache Pig mit Apache Hadoop in HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce mit Apache Hadoop in HDInsight](hdinsight-use-mapreduce.md)
