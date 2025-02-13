---
title: Bewährte Methoden für die Verwendung von Power BI zum Abfragen und Visualisieren von Azure Data Explorer-Daten
description: In diesem Artikel lernen Sie die bewährten Methoden kennen, die für die Verwendung von Power BI zum Abfragen und Visualisieren von Azure Data Explorer-Daten empfohlen werden.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: 53bed3fe50afef260ac44f73a9f82e6894015c90
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349012"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Bewährte Methoden für die Verwendung von Power BI zum Abfragen und Visualisieren von Azure Data Explorer-Daten

Azure-Daten-Explorer ist ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Daten (Protokoll- und Telemetriedaten). [Power BI](https://docs.microsoft.com/power-bi/) ist eine Business Analytics-Lösung, mit der Sie Ihre Daten visualisieren und die Ergebnisse unternehmensweit teilen können. Azure Data Explorer bietet drei Möglichkeiten, um eine Verbindung mit Daten in Power BI herzustellen. Verwenden Sie den [integrierten Connector](power-bi-connector.md), [importieren Sie eine Abfrage aus Azure Data Explorer in Power BI](power-bi-imported-query.md), oder verwenden Sie eine [SQL-Abfrage](power-bi-sql-query.md). In den in diesem Artikel enthaltenen Tipps wird erläutert, wie Sie Azure Data Explorer-Daten mit Power BI abfragen und visualisieren. 

## <a name="best-practices-for-using-power-bi"></a>Bewährte Methoden für die Verwendung von Power BI 

Wenn Sie mit neuen Rohdaten im Terabytebereich arbeiten, befolgen Sie diese Anweisungen, damit Ihre Power BI-Dashboards und -Berichte immer gut organisiert und auf dem neuesten Stand sind:

* **Nur das Nötigste** – Laden Sie nur die Daten in Power BI, die Sie für Ihre Berichte benötigen. Verwenden Sie für umfassende interaktive Analysen die [Azure Data Explorer-Webbenutzeroberfläche](web-query-data.md), die für die Ad-hoc-Exploration mit der Kusto-Abfragesprache optimiert ist.

* **Zusammengesetztes Modell** – Verwenden Sie ein [zusammengesetztes Modell](https://docs.microsoft.com/power-bi/desktop-composite-models), um aggregierte Daten für Dashboards der obersten Ebene mit gefilterten, operativen Rohdaten zu kombinieren. Sie können klar definieren, wann Rohdaten und wann eine aggregierte Ansicht verwendet werden sollen. 

* **Importmodus oder DirectQuery-Modus** – Verwenden Sie den **Importmodus** für Interaktionen mit kleineren Datasets. Verwenden Sie den **DirectQuery-Modus** für große, häufig aktualisierte Datasets. Erstellen Sie beispielsweise Dimensionstabellen mit dem **Importmodus**, da sie klein sind und sich nicht häufig ändern. Legen Sie das Aktualisierungsintervall entsprechend der erwarteten Datenaktualisierungsrate fest. Erstellen Sie mit dem **DirectQuery-Modus** Faktentabellen, da diese Tabellen groß sind und Rohdaten enthalten. Verwenden Sie diese Tabellen, um gefilterte Daten mithilfe von Power BI-[Drillthroughs](https://docs.microsoft.com/power-bi/desktop-drillthrough) darzustellen.

* **Parallelität** – Der Azure Data Explorer ist eine linear skalierbare Datenplattform. Daher können Sie die Leistung beim Dashboardrendering verbessern, indem Sie die Parallelität des End-to-End-Flows wie folgt erhöhen:

   * Erhöhen Sie die Anzahl [gleichzeitiger Verbindungen in DirectQuery in Power BI](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * Verwenden Sie eine [schwache Konsistenz, um die Parallelität zu verbessern](/azure/kusto/concepts/queryconsistency). Dies kann sich auf die Aktualität der Daten auswirken.

* **Effektive Slicer** – Sie können [Synchronisierungsslicer](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) verwenden, um zu verhindern, dass Daten in Berichte geladen werden, bevor Sie bereit sind. Nachdem Sie das Dataset strukturiert, alle visuellen Elemente platziert und alle Slicer gekennzeichnet haben, können Sie den Synchronisierungsslicer auswählen, um nur die benötigten Daten zu laden.

* **Verwendung von Filtern** – Verwenden Sie so viele Power BI-Filter wie möglich, um die Azure Data Explorer-Suche auf die relevanten Datenshards zu beschränken.

* **Effiziente visuelle Elemente** – Wählen Sie die leistungsfähigsten visuellen Elemente für Ihre Daten.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Tipps zur Datenabfrage mit dem Azure Data Explorer-Connector für Power BI

Der folgende Abschnitt enthält Tipps und Tricks zur Verwendung der Kusto-Abfragesprache mit Power BI. Verwenden Sie den [Azure Data Explorer-Connector für Power BI](power-bi-connector.md), um Daten zu visualisieren.

### <a name="complex-queries-in-power-bi"></a>Komplexe Abfragen in Power BI

Komplexe Abfragen lassen sich einfacher in Kusto als in Power Query ausdrücken. Sie sollten als [Kusto-Funktionen](/azure/kusto/query/functions) implementiert und in Power BI aufgerufen werden. Diese Methode ist erforderlich, wenn Sie **DirectQuery** mit `let`-Anweisungen in der Kusto-Abfrage verwenden. Da Power BI zwei Abfragen miteinander verknüpft und `let`-Anweisungen nicht mit dem `join`-Operator verwendet werden können, treten möglicherweise Syntaxfehler auf. Daher sollten Sie jeden Teil der Verknüpfung als Kusto-Funktion speichern und zulassen, dass diese beiden Funktionen von Power BI verknüpft werden.

### <a name="how-to-simulate-a-relative-data-time-operator"></a>Simulieren eines relativen DateTime-Operators

Power BI enthält keinen *relativen* DateTime-Operator wie `ago()`.
Verwenden Sie zum Simulieren von `ago()` eine Kombination aus den Power BI-Funktionen `DateTime.FixedLocalNow()` und `#duration`.

Verwenden Sie anstelle dieser Abfrage, die den `ago()`-Operator enthält:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Die folgende äquivalente Abfrage:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Erreichen der Kusto-Abfragegrenzwerte 

Kusto-Abfragen geben standardmäßig bis zu 500.000 Zeilen oder 64 MB zurück, wie unter [Abfragegrenzwerte](/azure/kusto/concepts/querylimits) beschrieben. Sie können diese Standardwerte außer Kraft setzen, indem Sie im Verbindungsfenster von **Azure Data Explorer (Kusto)** die Option **Erweiterte Optionen** verwenden:

![Erweiterte Optionen](media/power-bi-best-practices/advanced-options.png)

Durch diese Optionen werden mit Ihrer Abfrage [SET-Anweisungen](/azure/kusto/query/setstatement) ausgegeben, um die standardmäßigen Abfragegrenzwerte zu ändern:

  * **Datensatzanzahl für Abfrageergebnisse einschränken** generiert `set truncationmaxrecords`.
  * **Datengröße in Bytes für Abfrageergebnisse einschränken** generiert `set truncationmaxsize`.
  * **Abschneiden von Resultsets deaktivieren** generiert `set notruncation`.

### <a name="using-query-parameters"></a>Verwenden von Abfrageparametern

Mit [Abfrageparametern](/azure/kusto/query/queryparametersstatement) können Sie die Abfrage dynamisch ändern. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Verwenden eines Abfrageparameters in den Verbindungsdetails

Verwenden Sie einen Abfrageparameter, um Informationen in der Abfrage zu filtern und die Abfrageleistung zu optimieren.
 
Öffnen Sie im Fenster **Abfragen bearbeiten** -> **Home** > **Erweiterter Editor**.

1. Suchen Sie den folgenden Abschnitt in der Abfrage:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Beispiel:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Ersetzen Sie den relevanten Teil der Abfrage durch Ihren Parameter. Unterteilen Sie die Abfrage in mehrere Teile, und verketten Sie diese wieder mit dem &-Zeichen und dem Parameter.

   In der Abfrage oben verwenden wir beispielsweise den Teil `State == 'ALABAMA'`, unterteilen ihn in `State == '` und `'` und fügen den `State`-Parameter dazwischen ein:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Wenn die Abfrage Anführungszeichen enthält, codieren Sie sie ordnungsgemäß. Beispielsweise wird die folgende Abfrage: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   im **Erweiterten Editor** mit zwei Anführungszeichen wie folgt angezeigt:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Sie sollte durch die folgende Abfrage mit drei Anführungszeichen ersetzt werden:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Verwenden eines Abfrageparameters in den Abfrageschritten

Abfrageparameter können in beliebigen Abfrageschritten verwendet werden, vorausgesetzt, sie werden unterstützt. Filtern Sie die Ergebnisse z. B. basierend auf dem Wert eines Parameters.

![Filtern von Ergebnissen mithilfe eines Parameters](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Verwenden Sie keinen Power BI-Datenaktualisierungsplaner, um Steuerungsbefehle für Kusto auszugeben

Power BI enthält einen Datenaktualisierungsplaner, der regelmäßig Abfragen für eine Datenquelle ausgeben kann. Dieser Mechanismus sollte nicht verwendet werden, um Steuerungsbefehle für Kusto zu planen, da Power BI voraussetzt, dass alle Abfragen schreibgeschützt sind.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Von Power BI können nur kurze Abfragen (&lt;2.000 Zeichen) an Kusto gesendet werden.

Wenn die Ausführung einer Abfrage in Power BI folgenden Fehler verursacht: _„DataSource. Fehler: Fehler beim Abrufen von Inhalten von ... durch „Web.Contents“_ , ist die Abfrage möglicherweise länger als 2.000 Zeichen. Power BI verwendet **PowerQuery** zum Abfragen von Kusto. Dabei wird eine HTTP GET-Anforderung ausgegeben, durch die die Abfrage als Teil des abgerufenen URIs codiert wird. Daher sind von Power BI ausgegebene Kusto-Abfragen auf die maximale Länge eines Anforderungs-URI beschränkt (2.000 Zeichen abzüglich eines kleinen Puffers). Um dieses Problem zu umgehen, können Sie eine [gespeicherte Funktion](/azure/kusto/query/schema-entities/stored-functions) in Kusto definieren und Power BI diese Funktion in der Abfrage verwenden lassen.

## <a name="next-steps"></a>Nächste Schritte

[Visualisieren von Daten mithilfe des Azure Data Explorer-Connectors für Power BI](power-bi-connector.md)




