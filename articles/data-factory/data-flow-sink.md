---
title: Einrichten einer Senkentransformation in der Mapping Data Flow-Funktion von Azure Data Factory
description: Erfahren Sie, wie Sie eine Senkentransformation in Mapping Data Flow einrichten.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: be2ab5605f7fa60ebb78493f714648d458e82a6c
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029240"
---
# <a name="sink-transformation-for-a-data-flow"></a>Senkentransformation für einen Datenfluss



Nach der Transformation Ihres Datenflusses können Sie die Daten in ein Zieldataset weiterleiten. Wählen Sie in der Senkentransformation die Datasetdefinition für die Zielausgabedaten aus. Sie können so viele Senkentransformationen einrichten, wie Ihr Datenfluss erfordert.

Um Schemaabweichungen und Änderungen an eingehenden Daten zu berücksichtigen, leiten Sie die Ausgabedaten ohne ein definiertes Schema im Ausgabedataset an einen Ordner weiter. Außerdem können Sie für eventuelle Spaltenänderungen in Ihren Quellen die Option **Schemaabweichung zulassen** auswählen. Führen Sie dann eine automatische Zuordnung aller Felder in der Senke aus.

![Optionen auf der Registerkarte „Senke“, einschließlich der Option „Automatische Zuordnung“](media/data-flow/sink1.png "Senke 1")

Wenn alle eingehenden Felder an diese Senke weitergeleitet werden sollen, aktivieren Sie **Automatische Zuordnung**. Um die Zielfelder am Ziel auszuwählen oder die Namen der Felder am Ziel zu ändern, deaktivieren Sie **Automatische Zuordnung**. Öffnen Sie dann die Registerkarte **Zuordnung**, um die Ausgabefelder zuzuordnen.

![Optionen auf der Registerkarte „Zuordnung“](media/data-flow/sink2.png "Senke 2")

## <a name="output"></a>Output 
Für Senken des Typs Azure Blob Storage oder Data Lake Storage geben Sie die transformierten Daten in einen Ordner aus. Spark generiert partitionierte Ausgabedatendateien basierend auf dem Partitionsschema, das in der Senkentransformation verwendet wird. 

Sie können das Partitionsschema auf der Registerkarte **Optimieren** einrichten. Wenn Sie Ihre Ausgabe mit Data Factory in einer einzelnen Datei zusammenführen möchten, wählen Sie **Einzelne Partition** aus.

![Optionen auf der Registerkarte „Optimieren“](media/data-flow/opt001.png "Senkenoptionen")

## <a name="field-mapping"></a>Feldzuordnung
Auf der Registerkarte **Zuordnung** Ihrer Senkentransformation können Sie die eingehenden Spalten auf der linken Seite dem jeweiligen Ziel auf der rechten Seite zuordnen. Wenn Sie Datenflüsse in Dateien weiterleiten, schreibt Data Factory immer neue Dateien in einen Ordner. Bei der Zuordnung zu einem Datenbank-Dataset wählen Sie Optionen für Datenbanktabellenvorgänge aus, um Einfüge-, Aktualisierungs-, Upsert- oder Löschvorgänge auszuführen.

![Registerkarte „Zuordnung“](media/data-flow/sink2.png "Senken")

Sie können mit der Mehrfachauswahl in der Zuordnungstabelle mehrere Spalten verknüpfen, die Verknüpfung mehrerer Spalten aufheben oder mehrere Zeilen demselben Spaltennamen zuordnen.

Um den eingehenden Satz von Feldern immer unverändert einem Ziel zuzuordnen und flexible Schemadefinitionen vollständig zu akzeptieren, wählen Sie **Schemaabweichung zulassen** aus.

![Registerkarte „Zuordnung“ mit Feldern, die Spalten im Dataset zugeordnet sind](media/data-flow/multi1.png "mehrere Optionen")

Wählen Sie zum Zurücksetzen Ihrer Spaltenzuordnungen **Re-map** (Neu zuordnen) aus.

![Registerkarte „Senke“](media/data-flow/sink1.png "Senke 1")

Wählen Sie **Schema überprüfen** aus, damit die Senke einen Fehler auslöst, wenn das Schema geändert wird.

Wählen Sie **Clear the folder** (Ordner leeren) aus, um den Inhalt des Senkenordners abzuschneiden, bevor die Zieldateien in diesen Zielordner geschrieben werden.

## <a name="rule-based-mapping"></a>Regelbasierte Zuordnung
Wenn Sie die automatische Zuordnung deaktivieren, können Sie eine spaltenbasierte Zuordnung (feste Zuordnung) oder eine regelbasierte Zuordnung hinzuzufügen. Bei Verwendung der regelbasierten Zuordnung können Sie Ausdrücke mit Musterabgleich schreiben. 

![Regelbasierte Zuordnung](media/data-flow/rules4.png "Regelbasierte Zuordnung")

Wenn Sie die regelbasierte Zuordnung auswählen, weisen Sie ADF an, Ihren Abgleichsausdruck auszuwerten, um passende Eingangsmusterregeln zu ermitteln und die Ausgangsfeldnamen zu definieren. Sie können eine beliebige Kombination aus feld- und regelbasierten Zuordnungen hinzufügen. Feldnamen werden dann von ADF zur Laufzeit auf der Grundlage eingehender Metadaten aus der Quelle generiert. Sie können die Namen der generierten Felder während des Debuggens sowie im Datenvorschaubereich anzeigen.

Details zum Musterabgleich finden Sie in der [Dokumentation zu Spaltenmustern](concepts-data-flow-column-pattern.md).

## <a name="file-name-options"></a>Dateinamenoptionen

Richten Sie die Dateibenennung ein: 

   * **Standard:** Lassen Sie zu, dass Spark Dateien basierend auf den PART-Standards benennt.
   * **Muster:** Geben Sie ein Muster für Ihre Ausgabedateien ein. Zum Beispiel erstellt **loans[n]** die Dateien „loans1.csv“, „loans2.csv“ usw.
   * **Pro Partition:** Geben Sie einen Dateinamen pro Partition ein.
   * **Wie Daten in Spalte:** Legen Sie die Ausgabedatei auf den Wert einer Spalte fest.
   * **Ausgabe in eine einzelne Datei:** Mit dieser Option kombiniert ADF die partitionierten Ausgabedateien in einer einzelnen Datei. Um diese Option verwenden zu können, sollte Ihr Dataset in einen Ordnernamen aufgelöst werden. Bedenken Sie auch, dass dieser Zusammenführungsvorgang je nach Knotengröße zu Fehlern führen kann.

> [!NOTE]
> Dateivorgänge werden nur gestartet, wenn Sie die Aktivität zur Datenflussausführung ausführen. Im Datenfluss-Debugmodus werden sie nicht gestartet.

## <a name="database-options"></a>Datenbankoptionen

Wählen Sie die Datenbankeinstellungen aus:

![Registerkarte „Einstellungen“ mit Optionen für SQL-Senken](media/data-flow/alter-row2.png "SQL-Optionen")

* **Updatemethode:** Die Standardeinstellung ist das Zulassen von Einfügevorgängen. Deaktivieren Sie **Allow insert** (Einfügen zulassen), wenn keine neuen Zeilen aus der Quelle mehr eingefügt werden sollen. Wenn Sie für Zeilen Update-, Upsert- oder Löschvorgänge verwenden möchten, fügen Sie zunächst eine Transformation zur Änderung von Zeilen hinzu, um Zeilen für diese Aktionen zu kennzeichnen. 
* **Recreate table** (Tabelle neu erstellen): Löschen oder erstellen Sie die Zieltabelle, bevor der Datenfluss abgeschlossen wird.
* **Truncate table** (Tabelle abschneiden): Entfernen Sie alle Zeilen aus der Zieltabelle, bevor der Datenfluss abgeschlossen wird.
* **Batchgröße**: Eine Zahl eingeben, um Schreibvorgänge in Blöcke zu unterteilen. Verwenden Sie diese Option für große Datenmengen. 
* **Enable staging** (Staging aktivieren): Verwenden Sie bei Azure Data Warehouse als Senkendataset PolyBase.
* **Pre- und Post SQL-Skripts**: Geben Sie mehrzeilige SQL-Skripts ein, die ausgeführt werden, bevor Daten in die Senkendatenbank geschrieben werden (Vorverarbeitung) und danach (Nachbearbeitung).

![Vorverarbeitungs- und Nachbearbeitungs-SQL-Skripts](media/data-flow/prepost1.png "SQL-Verarbeitungsskripts")

> [!NOTE]
> Sie können Data Factory im Datenfluss anweisen, eine neue Tabellendefinition in der Zieldatenbank zu erstellen. Um die Tabellendefinition zu erstellen, legen Sie ein Dataset in der Senkentransformation mit einem neuen Tabellennamen fest. Wählen Sie im SQL-Dataset unter dem Tabellennamen **Bearbeiten** aus, und geben Sie einen neuen Tabellennamen ein. Aktivieren Sie dann in der Senkentransformation **Schemaabweichung zulassen**. Legen Sie **Schema importieren** auf **Nein** fest.

![SQL-Dataseteinstellungen mit Option für das Bearbeiten des Tabellennamens](media/data-flow/dataset2.png "SQL-Schema")

> [!NOTE]
> Beim Aktualisieren oder Löschen von Zeilen in Ihrer Datenbanksenke müssen Sie die Schlüsselspalte festlegen. Diese Einstellung ermöglicht die Transformation zur Zeilenänderung, um die eindeutige Zeile in der Datenverschiebungsbibliothek (Data Movement Library, DML) zu bestimmen.

## <a name="next-steps"></a>Nächste Schritte
Da Sie nun Ihren Datenfluss erstellt haben, fügen Sie [Ihrer Pipeline eine Aktivität zur Ausführung eines Datenflusses](concepts-data-flow-overview.md) hinzu.
