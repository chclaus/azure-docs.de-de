---
title: Erweiterungen und Features für virtuelle Azure-Computer | Microsoft-Dokumentation
description: Es wird beschrieben, was Erweiterungen für virtuelle Azure-Computer sind und wie Sie sie mit virtuellen Azure-Computern verwenden.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: akjosh
ms.openlocfilehash: deb49267a262705370e48e150cc5ed6c4dc04247
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168889"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Erweiterungen und Features für virtuelle Azure-Computer
Erweiterungen für virtuelle Azure-Computer sind kleine Anwendungen, mit denen die Erfüllung von Aufgaben zur Konfiguration und Automatisierung nach der Bereitstellung auf virtuellen Azure-Computern ermöglicht wird. Sie können vorhandene Bilder verwenden und diese dann als Teil Ihrer Bereitstellungen anpassen, sodass Sie keine benutzerdefinierten Images mehr erstellen müssen.

Auf der Azure-Plattform werden Erweiterungen für viele Zwecke gehostet, z.B. VM-Konfiguration, Überwachung, Sicherheit und Hilfsprogramme. Herausgeber umschließen eine Anwendung mit einer Erweiterung und vereinfachen die Installation, sodass Sie nur noch die obligatorischen Parameter angeben müssen. 

 Es ist eine große Auswahl von Erst- und Drittanbietererweiterungen verfügbar. Falls die Anwendung im Repository mit den Erweiterungen nicht vorhanden ist, können Sie die Erweiterung „Benutzerdefiniertes Skript“ verwenden und Ihre VM mit Ihren eigenen Skripts und Befehlen konfigurieren.

Beispiele für wichtige Szenarien, für die Erweiterungen verwendet werden:
* Für die VM-Konfiguration können Sie Erweiterungen vom Typ PowerShell DSC (Desired State Configuration), Chef, Puppet und Benutzerdefiniertes Skript verwenden, um Agents für die VM-Konfiguration zu installieren und Ihre VM zu konfigurieren. 
* AV-Produkte, z.B. Symantec, ESET.
* Tools für die Bewertung von VM-Sicherheitsrisiken, z.B. Qualys, Rapid7, HPE.
* Tools für die VM- und App-Überwachung, z.B. DynaTrace, Azure Network Watcher, Site24x7 und Stackify.

Erweiterungen können mit einer neuen VM-Bereitstellung gebündelt werden. Beispielsweise können sie Teil einer größeren Bereitstellung sein, die Anwendungen können bei der VM-Bereitstellung konfiguriert werden, oder sie können nach der Bereitstellung für alle unterstützten Systeme ausgeführt werden, die für Erweiterungen geeignet sind.

## <a name="how-can-i-find-what-extensions-are-available"></a>Wie kann ich ermitteln, welche Erweiterungen verfügbar sind?
Sie können verfügbare Erweiterungen im Portal auf dem Blatt der VM unter „Erweiterungen“ anzeigen. Dies ist aber nur ein kleiner Ausschnitt. Sie können die vollständige Liste anzeigen, indem Sie die CLI-Tools verwenden (siehe [VM-Erweiterungen und -Features für Linux](features-linux.md) und [VM-Erweiterungen und -Features für Windows](features-windows.md)).

## <a name="how-can-i-install-an-extension"></a>Wie kann ich eine Erweiterung installieren?
Azure VM-Erweiterungen können über Azure PowerShell, Azure Resource Manager-Vorlagen, die Azure CLI und das Azure-Portal verwaltet werden. Zum Ausprobieren einer Erweiterung können Sie im Azure-Portal die Erweiterung „Benutzerdefiniertes Skript“ auswählen, einen Befehl bzw. ein Skript übergeben und die Erweiterungen ausführen.

Wenn Sie dieselbe Erweiterung, die Sie im Portal hinzugefügt haben, für die CLI oder eine Resource Manager-Vorlage verwenden möchten, hilft Ihnen beispielsweise die Dokumentation zu Erweiterungen unter [Custom Script Extension for Windows](custom-script-windows.md) (Erweiterung „Benutzerdefiniertes Skript“ für Windows) und [Use the Azure Custom Script Extension Version 2 with Linux virtual machines](custom-script-linux.md) (Verwenden der Azure-Erweiterung „Benutzerdefiniertes Skript“ Version 2 mit virtuellen Linux-Computern) weiter.

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Wie kann ich den Anwendungslebenszyklus einer Erweiterung verwalten?
Sie müssen keine direkte Verbindung mit einer VM herstellen, um die Erweiterung zu installieren oder zu löschen. Da der Anwendungslebenszyklus für Azure-Erweiterungen außerhalb der VM verwaltet und in die Azure-Plattform integriert wird, wird für die Erweiterung der integrierte Status erzielt.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Was muss in Bezug auf Erweiterungen sonst noch bedacht werden?
Für Erweiterungen werden Anwendungen installiert, und wie für alle anderen Anwendungen auch, gelten einige bestimmte Anforderungen. Es ist eine Liste mit unterstützten Windows- und Linux-Betriebssystemen vorhanden, und Sie müssen die Azure-VM-Agents installieren. Einige einzelne VM-Erweiterungsanwendungen verfügen ggf. über eigene Umgebungsvoraussetzungen, z.B. den Zugriff auf einen Endpunkt.

## <a name="troubleshoot-extensions"></a>Problembehandlung bei Erweiterungen

Informationen zur Problembehandlung für jede Erweiterung finden Sie im Abschnitt **Problembehandlung und Support** in der Übersicht für die jeweilige Erweiterung. Es folgt eine Liste der verfügbaren Problembehandlungsinformatonen:

| Namespace | Problembehandlung |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [Azure Monitor Dependency-VM-Erweiterung für Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagentwindows | [Azure Monitor Dependency-VM-Erweiterung für Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [Azure Disk Encryption für Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption | [Azure Disk Encryption für Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [CustomScript-Erweiterung für Windows](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [DSC-Erweiterung für Linux](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [DSC-Erweiterung für Windows](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [NVIDIA-GPU-Treibererweiterung für Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [NVIDIA-GPU-Treibererweiterung für Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Microsoft Antimalware-Erweiterung für Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Azure Monitor für Linux](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Azure Monitor für Windows](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Stackify Retrace-Agent-Erweiterung für Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [Zurücksetzen des Kennworts (VMAccess) für Linux](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Momentaufnahme für Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Momentaufnahme für Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zur Funktionsweise des Linux-Agents und der Erweiterungen finden Sie unter [Virtual machine extensions and features for Linux](features-linux.md) (Informationen zu Erweiterungen und Features für virtuelle Computer für Linux).
* Weitere Informationen zur Funktionsweise des Windows-Gast-Agents und der Erweiterungen finden Sie unter [Azure VM-Erweiterungen und -Features für Windows](features-windows.md).  
* Informationen zur Installation des Windows-Gast-Agents finden Sie unter [Azure Virtual Machine Agent overview](agent-windows.md) (Übersicht über den Agent für virtuelle Azure-Computer).  
* Informationen zur Installation des Linux-Agents finden Sie unter [Understanding and using the Azure Linux Agent](agent-linux.md) (Grundlegendes zum Azure Linux-Agent und seiner Verwendung).  

