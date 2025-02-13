---
title: Verwalten des Azure Storage-Lebenszyklus
description: Erfahren Sie, wie Sie Regeln für Lebenszyklusrichtlinien erstellen, um alternde Daten von heißen zu kalten und zu Archivebenen zu übertragen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: e4d961603ab0ade1bb175161fffd7f085a1f644b
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934082"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Verwalten des Azure Blob Storage-Lebenszyklus

Datasets haben eindeutige Lebenszyklen. Früh im Lebenszyklus greifen Benutzer häufig auf einige Daten zu. Aber der Zugriffsbedarf sinkt mit zunehmendem Alter der Daten drastisch. Außerdem gibt es Daten, die in der Cloud lediglich vorgehalten werden und auf die nach der Speicherung nur selten zugegriffen wird. Einige Daten laufen Tage oder Monate nach der Erstellung ab, während andere Daten im Verlauf ihres gesamten Lebens aktiv gelesen und geändert werden. Die Azure Blob Storage-Lebenszyklusverwaltung bietet eine umfassende, regelbasierte Richtlinie für GPv2- und Blob Storage-Konten. Verwenden Sie die Richtlinie, um Ihre Daten in die entsprechenden Zugriffsebenen zu übertragen oder am Ende des Lebenszyklus der Daten ablaufen zu lassen.

Mit der Richtlinie für die Lebenszyklusverwaltung können Sie die folgenden Aufgaben ausführen:

- Überführen von Blobs in eine kältere Speicherebene (heiß in kalt, heiß in Archiv oder kalt in Archiv) zur Optimierung von Leistung und Kosten
- Löschen von Blobs am Ende ihres Lebenszyklus
- Definieren von Regeln, die ein Mal täglich auf Speicherkontoebene ausgeführt werden
- Anwenden von Regeln auf Container oder eine Teilmenge von Blobs (durch Einsatz von Präfixen als Filter)

Stellen Sie sich ein Szenario vor, bei dem in den frühen Phasen des Lebenszyklus häufig auf Daten zugegriffen wird, nach zwei Wochen aber nur noch gelegentlich. Nach dem ersten Monat wird auf das Dataset nur noch selten zugegriffen. In diesem Szenario empfiehlt sich in den frühen Phasen heißer Speicher. Die kalte Speicherebene eignet sich am besten für den gelegentlichen Zugriff. Die Archivspeicherebene ist die beste Option, wenn die Daten mehr als einen Monat alt sind. Durch Anpassen der Speicherebenen im Hinblick auf das Alter der Daten können Sie die kostengünstigsten Speicheroptionen für Ihre Anforderungen entwerfen. Für diesen Übergang stehen Richtlinienregeln für die Lebenszyklusverwaltung zur Verfügung, um alternde Daten in kühlere Ebenen zu verschieben.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>Speicherkontounterstützung

Die Richtlinien zur Lebenszyklusverwaltung sind für GPv2-Konten (General Purpose v2), Blob Storage-Konten und Premium-Blockblob-Speicherkonten verfügbar. Für ein vorhandenes GPv1-Konto (Universell V1) kann in einem einfachen Prozess im Azure-Portal ein Upgrade auf ein GPv2-Konto erfolgen. Weitere Informationen zu Speicherkonten finden Sie unter [Azure-Speicherkonto – Übersicht](../common/storage-account-overview.md).  

## <a name="pricing"></a>Preise

Die Funktion zur Lebenszyklusverwaltung ist kostenlos. Kunden werden die gewöhnlichen Betriebskosten für die API-Aufrufe [Blobs auflisten](https://docs.microsoft.com/rest/api/storageservices/list-blobs) und [Blobtarif festlegen](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) in Rechnung gestellt. Löschvorgänge sind kostenlos. Weitere Informationen zu den Preisen finden Sie unter [Preise für Blockblobs](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Die Funktion zur Lebenszyklusverwaltung ist in allen Azure-Regionen verfügbar.

## <a name="add-or-remove-a-policy"></a>Hinzufügen oder Entfernen einer Richtlinie

Sie können eine Richtlinie hinzufügen, bearbeiten oder entfernen, indem Sie eine der folgenden Methoden verwenden:

* [Azure-Portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [REST-APIs](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

In diesem Artikel wird die Verwaltung einer Richtlinie über das Portal und über PowerShell erläutert.  

> [!NOTE]
> Wenn Sie Firewallregeln für Ihr Speicherkonto aktivieren, werden Anforderungen für die Lebenszyklusverwaltung möglicherweise blockiert. Sie können die Sperre dieser Anforderungen durch Bereitstellen von Ausnahmen für vertrauenswürdige Microsoft-Dienste aufheben. Weitere Informationen finden Sie im Abschnitt „Ausnahmen“ unter [Konfigurieren von Firewalls und virtuellen Netzwerken](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

### <a name="azure-portal"></a>Azure-Portal

Es gibt zwei Möglichkeiten zum Hinzufügen einer Richtlinie über das Azure-Portal. 

* [Listenansicht des Azure-Portals](#azure-portal-list-view)
* [Codeansicht des Azure-Portals](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Listenansicht des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie die Option **Alle Ressourcen** und dann Ihr Speicherkonto aus.

3. Wählen Sie unter **Blobdienst** die Option **Lebenszyklusverwaltung** aus, um Ihre Regeln anzuzeigen oder zu ändern.

4. Wählen Sie die Registerkarte **Listenansicht** aus.

5. Wählen Sie **Regel hinzufügen** aus, und füllen Sie dann die Felder des Formulars **Aktionssatz** aus. Im nachstehenden Beispiel werden Blobs in den kalten Speicher verschoben, wenn sie während 30 Tagen nicht geändert wurden.

   ![Seite mit dem Aktionssatz für Lebenszyklusverwaltung im Azure-Portal](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Wählen Sie **Filtersatz** aus, um einen optionalen Filter hinzuzufügen. Wählen Sie dann **Durchsuchen** aus, um den Container und Ordner anzugeben, nach dem gefiltert werden soll.

   ![Seite mit dem Filtersatz für Lebenszyklusverwaltung im Azure-Portal](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Wählen Sie **Überprüfen + hinzufügen** aus, um die Richtlinieneinstellungen zu überprüfen.

9. Wählen Sie **Hinzufügen** aus, um die neue Richtlinie hinzuzufügen.

#### <a name="azure-portal-code-view"></a>Codeansicht des Azure-Portals
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie die Option **Alle Ressourcen** und dann Ihr Speicherkonto aus.

3. Wählen Sie unter **Blobdienst** die Option **Lebenszyklusverwaltung** aus, um Ihre Richtlinie anzuzeigen oder zu ändern.

4. Der folgende JSON-Code ist ein Beispiel für eine Richtlinie, die in die Registerkarte **Codeansicht** eingefügt werden kann.

   ```json
   {
     "rules": [
       {
         "name": "ruleFoo",
         "enabled": true,
         "type": "Lifecycle",
         "definition": {
           "filters": {
             "blobTypes": [ "blockBlob" ],
             "prefixMatch": [ "container1/foo" ]
           },
           "actions": {
             "baseBlob": {
               "tierToCool": { "daysAfterModificationGreaterThan": 30 },
               "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
               "delete": { "daysAfterModificationGreaterThan": 2555 }
             },
             "snapshot": {
               "delete": { "daysAfterCreationGreaterThan": 90 }
             }
           }
         }
       }
     ]
   }
   ```

5. Wählen Sie **Speichern** aus.

6. Weitere Informationen zu diesem JSON-Beispiel finden Sie in den Abschnitten [Richtlinie](#policy) und [Regeln](#rules).

### <a name="powershell"></a>PowerShell

Das folgende PowerShell-Skript kann verwendet werden, um Ihrem Speicherkonto eine Richtlinie hinzuzufügen. Die Variable `$rgname` muss mit Ihrem Ressourcengruppennamen initialisiert werden. Die Variable `$accountName` muss mit Ihrem Speicherkontonamen initialisiert werden.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

## <a name="azure-resource-manager-template-with-lifecycle-management-policy"></a>Azure Resource Manager-Vorlage mit Richtlinie für die Lebenszyklusverwaltung

Sie können die Lebenszyklusverwaltung definieren, indem Sie Azure Resource Manager-Vorlagen verwenden. Hier ist eine Beispielvorlage für das Bereitstellen eines GPv2-Speicherkontos mit RA-GRS mit einer Richtlinie für die Lebenszyklusverwaltung angegeben.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

## <a name="policy"></a>Richtlinie

Eine Richtlinie zur Lebenszyklusverwaltung ist eine Sammlung von Regeln in einem JSON-Dokument:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

Eine Richtlinie ist eine Sammlung von Regeln:

| Parametername | Parametertyp | Notizen |
|----------------|----------------|-------|
| `rules`        | Ein Array von Regelobjekten | Eine Richtlinie muss mindestens eine Regel enthalten. Sie können in einer Richtlinie bis zu 100 Regeln definieren.|

Jede Regel in der Richtlinie umfasst mehrere Parameter:

| Parametername | Parametertyp | Notizen | Erforderlich |
|----------------|----------------|-------|----------|
| `name`         | Zeichenfolge |Ein Regelname kann bis zu 256 alphanumerische Zeichen enthalten. Bei Regelnamen wird die Groß-/Kleinschreibung unterschieden.  Er muss innerhalb einer Richtlinie eindeutig sein. | True |
| `enabled`      | Boolean | Ein optionaler boolescher Wert, über den eine Regel temporär deaktiviert werden kann. Der Standardwert ist „True“, wenn dieser Wert nicht festgelegt wurde. | False | 
| `type`         | Ein Enumerationswert | Aktuell ist `Lifecycle` der gültige Typ. | True |
| `definition`   | Ein Objekt, das die Lebenszyklusregel definiert | Jede Definition beinhaltet einen Filtersatz und einen Aktionssatz. | True |

## <a name="rules"></a>Regeln

Jede Regeldefinition enthält einen Filtersatz und einen Aktionssatz. Der [Filtersatz](#rule-filters) schränkt Regelaktionen auf eine bestimmte Menge von Objekten innerhalb eines Containers oder auf Objektnamen ein. Der [Aktionssatz](#rule-actions) wendet die Ebenen- oder Löschaktionen auf den gefilterten Satz von Objekten an.

### <a name="sample-rule"></a>Beispielregel

Mit der folgenden Beispielregel wird das Konto so gefiltert, dass Aktionen für Objekte ausgeführt werden, die sich in `container1` befinden und mit `foo` beginnen.  

- Blob 30 Tage nach der letzten Änderung in die kalte Ebene verschieben
- Blob 90 Tage nach der letzten Änderung in die Archivebene verschieben
- Blob 2.555 Tage (sieben Jahre) nach der letzten Änderung löschen
- Blob-Momentaufnahmen 90 Tage nach der Erstellung der Momentaufnahmen löschen

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Regelfilter

Filter schränken Regelaktionen auf eine Teilmenge der Blobs innerhalb des Speicherkontos ein. Wenn mehrere Filter definiert sind, wird ein logisches `AND` für alle Filter ausgeführt.

Filter umfassen Folgendes:

| Filtername | Filtertyp | Notizen | Ist erforderlich |
|-------------|-------------|-------|-------------|
| blobTypes   | Ein Array von vordefinierten Enumerationswerten. | In der aktuellen Version wird `blockBlob` unterstützt. | Ja |
| prefixMatch | Ein Array von Zeichenfolgen für Präfixe, mit denen Übereinstimmung erzielt werden soll. In jeder Regel können bis zu 10 Präfixe definiert werden. Eine Präfixzeichenfolge muss mit einem Containernamen beginnen. Wenn Sie für eine Regel beispielsweise eine Übereinstimmung mit allen Blobs unter „`https://myaccount.blob.core.windows.net/container1/foo/...`“ erzielen möchten, lautet der prefixMatch-Wert `container1/foo`. | Wenn Sie prefixMatch nicht definieren, gilt die Regel für alle Blobs im Speicherkonto.  | Nein |

### <a name="rule-actions"></a>Regelaktionen

Aktionen werden auf die gefilterten Blobs angewandt, wenn die Ausführungsbedingung erfüllt ist.

Bei der Lebenszyklusverwaltung werden die Ebenenverschiebung und das Löschen von Blobs sowie von Blobmomentaufnahmen unterstützt. Definieren Sie mindestens eine Aktion für jede Regel für Blobs oder Blobmomentaufnahmen.

| Aktion        | Basisblob                                   | Momentaufnahme      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Unterstützt Blobs, die sich aktuell in der heißen Ebene befinden.         | Nicht unterstützt |
| tierToArchive | Unterstützt Blobs, die sich aktuell in der heißen oder der kalten Ebene befinden. | Nicht unterstützt |
| delete        | Unterstützt                                   | Unterstützt     |

>[!NOTE]
>Wenn für das gleiche Blob mehrere Aktionen definiert sind, wendet die Lebenszyklusverwaltung die am wenigsten teure Aktion auf das Blob an. Beispielsweise ist die Aktion `delete` kostengünstiger als die Aktion `tierToArchive`. Die Aktion `tierToArchive` ist kostengünstiger als die Option `tierToCool`.

Die Ausführungsbedingungen basieren auf dem Alter. Basisblobs verwenden den Zeitpunkt der letzten Änderung, um das Alter nachzuverfolgen, während Blobmomentaufnahmen für den gleichen Zweck den Erstellungszeitpunkt der Momentaufnahme verwenden.

| Aktionsausführungsbedingung             | Wert der Bedingung                          | BESCHREIBUNG                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Ganzzahliger Wert, der das Alter in Tagen angibt | Bedingung für Basisblobaktionen     |
| daysAfterCreationGreaterThan     | Ganzzahliger Wert, der das Alter in Tagen angibt | Bedingung für Aktionen für Blobmomentaufnahmen |

## <a name="examples"></a>Beispiele

Die folgenden Beispiele veranschaulichen die Behandlung von gängigen Szenarien mithilfe von Regeln zur Lebenszyklusverwaltung.

### <a name="move-aging-data-to-a-cooler-tier"></a>Verschieben von alternden Daten auf eine kühlere Ebene

Dieses Beispiel zeigt den Übergang von Blockblobs mit dem Präfix `container1/foo` oder `container2/bar`. Die Richtlinie überführt Blobs, die mehr als 30 Tage nicht verändert wurden, in den kalten Speicher und Blobs, die in 90 Tagen nicht verändert wurden, in die Archivebene:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-at-ingest"></a>Archivieren von Daten bei der Erfassung

Außerdem gibt es Daten, die in der Cloud lediglich vorgehalten werden und auf die nach der Speicherung nur sehr selten oder gar nicht zugegriffen wird. Die folgende Lebenszyklusrichtlinie ist so konfiguriert, dass Daten nach der Erfassung archiviert werden. In diesem Beispiel werden Blockblobs im Speicherkonto im Container `archivecontainer` an eine Archivebene überführt. Die Umstellung wird durch die Ausführung der Aktion für Blobs 0 Tage nach dem Zeitpunkt der letzten Änderung erreicht:

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Ablauf von Daten nach dem Alter

Für einige Daten wird erwartet, dass sie einige Tage oder Monate nach der Erstellung ablaufen. Sie können eine Richtlinie zur Lebenszyklusverwaltung so einrichten, dass Daten durch Löschung auf der Grundlage ihres Alters ablaufen. Das folgende Beispiel zeigt eine Richtlinie, mit der alle Blockblobs gelöscht werden, die älter als 365 Tage sind.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>Löschen von alten Momentaufnahmen

Bei Daten, die regelmäßig geändert und auf die während ihrer Lebensdauer regelmäßig zugegriffen wird, werden Momentaufnahmen häufig verwendet, um ältere Versionen der Daten nachzuverfolgen. Sie können eine Richtlinie erstellen, die alte Momentaufnahmen auf der Grundlage des Alters der Momentaufnahme löscht. Das Alter der Momentaufnahme wird durch Auswertung der Erstellungszeit der Momentaufnahme bestimmt. Diese Richtlinie löscht Blockblob-Momentaufnahmen in Container `activedata`, die mindestens 90 Tage alt sind (ab dem Zeitpunkt der Erstellung der Momentaufnahme).

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="faq"></a>Häufig gestellte Fragen

**Ich habe eine neue Richtlinie erstellt. Warum werden die Aktionen nicht sofort ausgeführt?**  
Die Plattform führt die Lebenszyklusrichtlinie ein Mal täglich aus. Nachdem Sie eine neue Richtlinie konfiguriert haben, kann es bis zu 24 Stunden dauern, bis einige Aktionen das erste Mal ausgeführt werden.  

**Ich will ein archiviertes Blob manuell aktivieren. Wie kann ich verhindern, dass es vorübergehend zurück auf die Archivspeicherebene verschoben wird?**  
Wenn ein Blob von einer Zugriffsebene auf eine andere verschoben wird, ändert sich der Zeitpunkt der letzten Änderung nicht. Wenn Sie ein archiviertes Blob auf der heißen Ebene manuell aktivieren, würde es vom Modul für die Lebenszyklusverwaltung zurück auf die Archivspeicherebene verschoben. Deaktivieren Sie vorübergehend die Regel, die sich auf dieses Blob auswirkt, um zu verhindern, dass es erneut archiviert wird. Kopieren Sie das Blob an einen anderen Speicherort, falls es sich dauerhaft auf einer heißen Ebene befinden muss. Aktivieren Sie die Regel erneut, wenn das Blob sicher zurück auf die Archivspeicherebene verschoben werden kann. 


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Daten nach versehentlichem Löschen wiederhergestellt werden:

- [Vorläufiges Löschen für Azure Storage-Blobs](../blobs/storage-blob-soft-delete.md)
