---
title: Migrieren von Azure-Verwaltungstools von Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Verwaltungstools von Azure Deutschland zu Azure weltweit.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: a9c3ca3a4047e69efedaa0baa93d3f1923c78c22
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272529"
---
# <a name="migrate-management-tool-resources-to-global-azure"></a>Migrieren von Verwaltungstoolressourcen zu Azure weltweit

Dieser Artikel enthält Informationen dazu, wie Sie Azure-Verwaltungstools von Azure Deutschland zu Azure weltweit migrieren können.

## <a name="traffic-manager"></a>Traffic Manager

Azure Traffic Manager kann Sie beim Ausführen einer reibungslosen Migration unterstützen. E ist aber nicht möglich, Traffic Manager-Profile, die Sie in Azure Deutschland erstellt haben, nach Azure weltweit zu migrieren. (Während einer Migration migrieren Sie Traffic Manager-Endpunkte zur Zielumgebung, so dass Sie das Traffic Manager-Profil ohnehin aktualisieren müssen.)

Sie können zusätzliche Endpunkte in der Zielumgebung definieren, indem Sie den Traffic Manager verwenden, während er noch in der Quellumgebung ausgeführt wird. Wenn Traffic Manager in der neuen Umgebung ausgeführt wird, können Sie in der Quellumgebung weiterhin Endpunkte definieren, die Sie noch nicht migriert haben. Dieses Szenario wird als das [Blau-Grün-Szenario](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/) bezeichnet. Dieses Szenario umfasst die folgenden Schritte:

1. Erstellen Sie ein neues Traffic Manager-Profil in Azure weltweit.
1. Definieren Sie die Endpunkte in Azure Deutschland.
1. Ändern Sie Ihren DNS CNAME-Eintrag in das neue Traffic Manager-Profil.
1. Deaktivieren Sie das alte Traffic Manager-Profil.
1. Migrieren und konfigurieren Sie Endpunkte. Für jeden Endpunkt in Azure Deutschland:
   1. Migrieren Sie den Endpunkt zu Azure weltweit.
   1. Ändern Sie das Traffic Manager-Profil, um den neuen Endpunkt zu verwenden.

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Traffic Manager-Tutorials](https://docs.microsoft.com/azure/traffic-manager) durcharbeiten.
- Lesen Sie [Was ist Traffic Manager?](../traffic-manager/traffic-manager-overview.md)
- Informieren Sie sich über das [Erstellen eines Traffic Manager-Profils](../traffic-manager/traffic-manager-create-profile.md).
- Erfahren Sie mehr über das [Blau-Grün-Szenario](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/).

## <a name="backup"></a>Backup

Azure Backup-Aufträge und -Momentaufnahmen von Azure Deutschland können nicht zu Azure weltweit migriert werden.

Weitere Informationen finden Sie unter: 

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Tutorials zu Backup](https://docs.microsoft.com/azure/backup/#step-by-step-tutorials) durcharbeiten.
- Lesen Sie die [Übersicht über die Funktionen in Azure Backup](../backup/backup-introduction-to-azure-backup.md).

## <a name="scheduler"></a>Scheduler

Azure Scheduler ist veraltet. Verwenden Sie Azure Logic Apps zum Erstellen von Planungsaufträgen.

Weitere Informationen finden Sie unter:

- Machen Sie sich mit Funktionen in Azure Logic Apps vertraut, indem Sie die [Logic Apps-Tutorials](https://docs.microsoft.com/azure/logic-apps/tutorial-build-schedule-recurring-logic-app-workflow) durcharbeiten.
- Lesen Sie [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="network-watcher"></a>Network Watcher

Das Migrieren einer Azure Network Watcher-Instanz von Azure Deutschland zu Azure weltweit wird derzeit nicht unterstützt. Es empfiehlt sich, dass Sie eine neue Network Watcher-Instanz in Azure weltweit erstellen und konfigurieren. Vergleichen Sie dann die Ergebnisse zwischen der alten und der neuen Umgebung. 

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie die [Network Watcher-Tutorials](https://docs.microsoft.com/azure/network-watcher) durcharbeiten.
- Lesen Sie [Was ist Azure Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md)
- Erfahren Sie mehr über [Datenflussprotokolle für Netzwerksicherheitsgruppen](../network-watcher/network-watcher-nsg-flow-logging-portal.md).
- Erfahren Sie mehr über[Verbindungsmonitor](../network-watcher/connection-monitor.md).

## <a name="site-recovery"></a>Site Recovery

Sie können Ihr aktuelles Azure Site Recovery-Setup nicht zu Azure weltweit migrieren. Sie müssen eine neue Site Recovery-Lösung in Azure weltweit einrichten.

Weitere Informationen zu Site Recovery und Informationen dazu, wie Sie virtuelle Computer von Azure Deutschland zu Azure weltweit migrieren, finden Sie unter [Migration von Compute-Ressourcen von Azure Deutschland zu Azure weltweit](./germany-migration-compute.md#compute-iaas).

Frischen Sie Ihre Kenntnisse auf, indem Sie die folgenden Schritt-für-Schritt-Tutorials durcharbeiten:

- [Azure zu Azure: Notfallwiederherstellung](https://docs.microsoft.com/azure/site-recovery/#azure-to-azure)
- [VMware zu Azure: Notfallwiederherstellung](https://docs.microsoft.com/azure/site-recovery/#vmware)
- [Hyper-V zu Azure: Notfallwiederherstellung](https://docs.microsoft.com/azure/site-recovery/#hyper-v)

## <a name="azure-policies"></a>Azure-Richtlinien

Sie können Richtlinien nicht direkt von Azure Deutschland zu Azure weltweit migrieren. Während einer Migration wird der Gültigkeitsbereich von zugewiesenen Richtlinien üblicherweise geändert. Dies trifft insbesondere zu, wenn sich das Abonnement in der Zielumgebung unterscheidet, wie in diesem Szenario. Sie haben aber die Möglichkeit, Richtliniendefinitionen beizubehalten und sie in Azure weltweit wiederzuverwenden.

Führen Sie in der Azure CLI den folgenden Befehl aus, um alle Richtlinien in Ihrer aktuellen Umgebung aufzulisten.

> [!NOTE]
> Wechseln Sie in der Azure CLI unbedingt in die Umgebung „AzureGermanCloud“, bevor Sie die folgenden Befehle ausführen.


```azurecli
az policy definition list --query '[].{Type:policyType,Name:name}' --output table
```

Exportieren Sie nur Richtlinien, für die **PolicyType** den Wert **Custom** hat. Exportieren Sie **policyRule** in eine Datei. Im folgenden Beispiel wird die benutzerdefinierte Richtlinie „Allow Germany Central Only“ (Kurzversion `allowgconly`) in eine Datei im aktuellen Ordner exportiert: 

```azurecli
az policy definition show --name allowgconly --output json --query policyRule > policy.json
```

Ihre Exportdatei entspricht etwa dem folgenden Beispiel:

```json
{
  "if": {
    "not": {
      "equals": "germanycentral",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

Wechseln Sie nun in Umgebung von Azure weltweit. Ändern Sie die Richtlinienregel, indem Sie die Datei bearbeiten. Ändern Sie beispielsweise `germanycentral` in `westeurope`.

```json
{
  "if": {
    "not": {
      "equals": "westeurope",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

Erstellen Sie die neue Richtlinie:

```azurecli
cat policy.json |az policy definition create --name "allowweonly" --rules @-
```

Sie haben nun eine neue Richtlinie namens `allowweonly`. Die Richtlinie lässt nur „Europa, Westen“ als Region zu.

Weisen Sie die Richtlinie den Bereichen in Ihrer neuen Umgebung entsprechend zu. Sie können die alten Zuordnungen in Azure Deutschland dokumentieren, indem Sie den folgenden Befehl ausführen:

```azurecli
az policy assignment list
```

Weitere Informationen finden Sie unter:

- Frischen Sie Ihre Kenntnisse auf, indem Sie das [Tutorial zu Azure-Richtlinien](../governance/policy/tutorials/create-and-manage.md) durcharbeiten.
- Erfahren Sie, wie Sie [Richtlinien über die Azure CLI anzeigen](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-azure-cli) oder [Richtlinien über PowerShell anzeigen](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-powershell).
- Erfahren Sie, wie Sie [eine Richtliniendefinition über die Azure CLI erstellen](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-azure-cli) oder [eine Richtliniendefinition über PowerShell erstellen](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-powershell).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Speicher](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Datenbanken](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Sicherheit](./germany-migration-security.md)
- [Medien](./germany-migration-media.md)
