---
title: Neuerungen in Azure Site Recovery
description: Hier finden Sie eine Zusammenfassung der neuen Features in Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: raynew
ms.openlocfilehash: c54273e0ca8c88a6fe47d5611950acd38f50b4c0
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212803"
---
# <a name="whats-new-in-site-recovery"></a>Neuerungen in Site Recovery

Der [Azure Site Recovery](site-recovery-overview.md)-Dienst wird laufend aktualisiert und verbessert. Um Sie auf dem Laufenden zu halten, bietet Ihnen dieser Artikel Informationen zu den neuesten Releases, neuen Features und neuen Inhalten. Diese Seite wird regelmäßig aktualisiert.

Sie können Site Recovery-Updatebenachrichtigungen im Kanal für [Azure-Updates](https://azure.microsoft.com/updates/?product=site-recovery) nachverfolgen und abonnieren.

## <a name="supported-updates"></a>Unterstützte Updates

Für Site Recovery-Komponenten werden N-4 Versionen unterstützt, wobei N die zuletzt veröffentlichte Version ist. Eine Zusammenfassung finden Sie in der folgenden Tabelle:

**Aktualisieren** |  **Einheitliches Setup** | **OVA-Datei des Konfigurationsservers** | **Mobilitätsdienst-Agent** | **Site Recovery-Anbieter** | **Recovery Services-Agent**
--- | --- | --- | --- | --- | ---
[Rollup 40](https://support.microsoft.com/help/4517283/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0
[Rollup 39](https://support.microsoft.com/help/4517283/) | 9.27.5308.1 | 5.1.4600.0 | 9.27.5308.1 | 5.1.4600.0 | 2.0.9165.0
[Rollup 38](https://support.microsoft.com/help/4513507/) | 9.26.5269.1 | 5.1.4500.0 | 9.26.5269.1 | 5.1.4500.0 | 2.0.9165.0
[Rollup 37](https://support.microsoft.com/help/4508614/) | 9.25.5241.1 | 5.1.4300.0 | 9.25.5241.1 | 5.1.4300.0 | 2.0.9163.0
[Rollup 36](https://support.microsoft.com/help/4503156/) | 9.24.5211.1 | 5.1.4150.0 | 9.24.5211.1 | 5.1.4150.0 | 2.0.9160.0 
        

[Weitere Informationen](service-updates-how-to.md) zur Updateinstallation und -unterstützung


## <a name="updates-september-2019"></a>Updates (September 2019)

### <a name="update-rollup-40"></a>Updaterollup 40

Das [Updaterollup 40](h https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) umfasst folgende Updates.

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Updates für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben)
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben)




### <a name="azure-vm-disaster-recovery"></a>Notfallwiederherstellung für virtuelle Azure-Computer

Die neuen Features für die Notfallwiederherstellung von virtuellen Azure-Computern sind in der Tabelle zusammengefasst.

**Feature** | **Details**
--- | ---
**Bereinigung nach Failback** | Nach dem Failover zum sekundären Azure und dem anschließenden Failback zur primären Region, bereinigt Site Recovery automatisch Computer in der sekundären Region. Es ist nicht erforderlich, VMs und NICs manuell zu löschen.
**Testfailover behält die IP-Adresse bei** | Sie können jetzt die IP-Adresse der Quell-VM während einer Notfallwiederherstellungsübgung beibehalten und eine statische IP-Adresse für einen Testfailover auswählen.

### <a name="vmwarephysical-server-disaster-recovery"></a>Notfallwiederherstellung für VMware/physische Server

In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
Neue Warnungen zum Prozessserver | Wir haben neue Warnungen für Prozessserver hinzugefügt. [Weitere Informationen](vmware-physical-azure-monitor-process-server.md) 

### <a name="hyper-v-disaster-recovery"></a>Hyper-V-Notfallwiederherstellung

In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
Speicherkonto | Site Recovery unterstützt jetzt die Verwendung von Speicherkonten mit einer Firewall, die für die Notfallwiederherstellung von Hyper-V zu Azure aktiviert ist.  Sie können firewallfähige Speicherkonten als Zielkonto oder für den Cachespeicher auswählen. Wenn Sie ein firewallfähiges Konto verwenden, stellen Sie sicher, dass Sie die Option zum Zulassen vertrauenswürdiger Microsoft-Dienste aktivieren.


## <a name="updates-august-2019"></a>Updates (August 2019)

### <a name="update-rollup-39"></a>Updaterollup 39

Das [Updaterollup 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) umfasst folgenden Updates.

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Updates für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben)
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben)


### <a name="azure-vm-disaster-recovery"></a>Notfallwiederherstellung für virtuelle Azure-Computer

Die neuen Features für die Notfallwiederherstellung von virtuellen Azure-Computern sind in der Tabelle zusammengefasst.

**Feature** | **Details**
--- | ---
**Verschlüsselung ohne Azure AD** | Verschlüsselung ohne eine Azure AD-App wird jetzt für die Replikation virtueller Azure-Computer auf verwalteten Datenträgern unter Windows unterstützt.
**Netzwerkressourcen für Failover** | Wenn Sie ein Failover zu einer anderen Region vorgenommen haben, können Sie nun Netzwerkressourceneinstellungen (NSGs,Lastenausgleich, öffentliche IP-Adresse) zu einem virtuellen Computer zuordnen. 

## <a name="updates-july-2019"></a>Updates (Juli 2019)

### <a name="update-rollup-38"></a>Updaterollup 38

Das [Updaterollup 38](https://support.microsoft.com/help/4513507/) umfasst folgenden Updates:

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Updates für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben)
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben)


### <a name="general"></a>Allgemein

Site Recovery unterstützt nun die Verwendung universeller v2-Speicherkonten für Cachespeicher oder Zielspeicher. Bislang wurde nur v1 unterstützt.

### <a name="vmware-to-azure-disaster-recovery"></a>Notfallwiederherstellung: VMware zu Azure

Auf einem virtuellen Azure-Computer mit verwalteten Datenträgern können nun Datenträger mit bis zu 8 TB repliziert werden.


## <a name="updates-june-2019"></a>Updates (Juni 2019)

### <a name="update-rollup-37"></a>Updaterollup 37

Mit dem [Updaterollup 37](https://support.microsoft.com/help/4508614/) werden die folgenden Updates bereitgestellt.

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Updates für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben)
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben)


### <a name="vmwarephysical-server-disaster-recovery"></a>Notfallwiederherstellung für VMware/physische Server

In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
**GPT-Partitionen** | Ab dem Updaterollup 37 (Mobility Service-Version 9.25.5241.1) werden bis zu fünf GPT-Partitionen in der UEFI unterstützt. Vor diesem Update waren es vier.



## <a name="updates-may-2019"></a>Updates (Mai 2019)

### <a name="update-rollup-36"></a>Updaterollup 36

Mit dem [Updaterollup 36](https://support.microsoft.com/help/4503156) werden die folgenden Updates bereitgestellt.

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Ein Update für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben)
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben)

### <a name="azure-vm-disaster-recovery"></a>Notfallwiederherstellung für virtuelle Azure-Computer

In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
**Replizieren von hinzugefügten Datenträgern** | Mit diesem Feature können Sie die Replikation für Datenträger aktivieren, die einer bereits für die Notfallwiederherstellung aktivierten Azure-VM hinzugefügt wurden. [Weitere Informationen](azure-to-azure-enable-replication-added-disk.md)
**Automatische Updates** | Beim Konfigurieren von automatischen Updates für die Mobility Service-Erweiterung, die auf für die Notfallwiederherstellung aktivierten Azure-VMs ausgeführt wird, können Sie jetzt ein vorhandenes Automation-Konto auswählen, statt das von Site Recovery erstellte Standardkonto zu verwenden. [Weitere Informationen](azure-to-azure-autoupdate.md)


### <a name="vmwarephysical-server-disaster-recovery"></a>Notfallwiederherstellung für VMware/physische Server

In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
**Überwachen des Prozessservers** | Zur Notfallwiederherstellung von lokalen VMware-VMs und physischen Servern können Sie Probleme auf Prozessservern überwachen und beheben. Gleichzeitig haben Sie Zugriff auf verbesserte Berichte und Benachrichtigungen zur Serverintegrität. [Weitere Informationen](vmware-physical-azure-monitor-process-server.md) 





## <a name="updates-march-2019"></a>Updates (März 2019)

### <a name="update-rollup-35"></a>Updaterollup 35

Mit dem [Updaterollup 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) werden die folgenden Updates bereitgestellt.

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Ein Update für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben)
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben)

### <a name="vmwarephysical-server-disaster-recovery"></a>Notfallwiederherstellung für VMware/physische Server

In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
**Verwaltete Datenträger** | Replikation von lokalen virtuelle VMware-Computer und physische Server erfolgt jetzt direkt auf verwaltete Datenträger in Azure. Lokale Daten werden an ein Cachespeicherkonto in Azure gesendet, und Wiederherstellungspunkte werden auf verwalteten Datenträgern am Zielspeicherort erstellt. Daher müssen Sie nicht mehrere Zielspeicherkonten verwalten.
**Konfigurationsserver** | Site Recovery unterstützt jetzt Konfigurationsserver mit mehreren NICs. Fügen Sie der Konfigurationsserver-VM zusätzliche Adapter hinzu, bevor Sie den Konfigurationsserver im Tresor registrieren. Wenn Sie die Adapter danach hinzufügen, müssen Sie den Server erneut beim Tresor registrieren.


## <a name="updates-february-2019"></a>Updates (Februar 2019)

### <a name="update-rollup-34"></a>Updaterollup 34 

Mit dem [Updaterollup 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) werden die folgenden Updates bereitgestellt.

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Ein Update für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben).
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben).


### <a name="update-rollup-33"></a>Updaterollup 33 

Mit dem [Updaterollup 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) werden die folgenden Updates bereitgestellt.

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Ein Update für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben).
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben).


### <a name="azure-vm-disaster-recovery"></a>Notfallwiederherstellung für virtuelle Azure-Computer 
In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
**Netzwerkzuordnung** | Sie können nun ein beliebiges Zielnetzwerk beim Aktivieren der Replikation für die Azure-VM-Notfallwiederherstellung verwenden. 
**SSD Standard** | Sie können nun die Notfallwiederherstellung für Azure-VMs mit [SSD Standard-Datenträgern](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd) einrichten.
**Direkte Speicherplätze** | Sie können nun die Notfallwiederherstellung für Apps auf Azure-VMs mit [direkten Speicherplätzen](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) für Hochverfügbarkeit einrichten.  Die Verwendung direkten Speicherplätzen (S2D) in Verbindung mit Site Recovery bietet einen umfassenden Schutz von Azure-VM-Workloads. Mit S2D können Sie einen Gastcluster in Azure hosten. Dies ist besonders nützlich, wenn eine VM eine kritische Anwendung hostet, wie z.B. SAP ASCS Layer, SQL Server oder den Dateiserver für die horizontale Skalierung.


### <a name="vmwarephysical-server-disaster-recovery"></a>Notfallwiederherstellung für VMware/physische Server
In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
**Linux-BRTFS-Dateisystem** | Site Recovery unterstützt nun die Replikation von VMware-VMs mit dem BRTFS-Dateisystem. Die Replikation wird nicht unterstützt, wenn:<br/><br/>- Das untergeordnete Volume des BTRFS-Dateisystems nach dem Aktivieren der Replikation geändert wird.<br/><br/>- Das Dateisystem über mehrere Datenträger verteilt wird.<br/><br/>– Die BTRFS-Dateisystem RAID unterstützt.
**Windows Server 2019** | Unterstützung für Computer mit Windows Server 2019 hinzugefügt.


## <a name="updates-january-2019"></a>Updates (Januar 2019)


### <a name="accelerated-networking-azure-vms"></a>Beschleunigter Netzwerkbetrieb (Azure-VMs)

Der beschleunigte Netzwerkbetrieb ermöglicht die E/A-Virtualisierung mit Einzelstamm (Single Root I/O Virtualization, SR-IOV) auf einem virtuellen Computer und somit eine Steigerung der Netzwerkleistung. Wenn Sie die Replikation für eine Azure-VM aktivieren, erkennt Site Recovery, ob beschleunigter Netzwerkbetrieb aktiviert ist. Wenn dies der Fall ist, konfiguriert Site Recovery nach dem Failover automatisch beschleunigten Netzwerkbetrieb auf der Azure-Zielreplikat-VM, und zwar sowohl für [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) als auch für [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Weitere Informationen](azure-vm-disaster-recovery-with-accelerated-networking.md)

### <a name="update-rollup-32"></a>Updaterollup 32 

Mit dem [Updaterollup 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) werden die folgenden Updates bereitgestellt.

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Ein Update für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben).
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben).

### <a name="azure-vm-disaster-recovery"></a>Notfallwiederherstellung für virtuelle Azure-Computer

In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
**Linux-Unterstützung** | Es wurde Unterstützung für Red Hat Workstation 6/7 und für neue Kernelversionen für Ubuntu, Debian und SUSE hinzugefügt.
**Direkte Speicherplätze** | Site Recovery unterstützt Azure-VMs mit direkten Speicherplätzen (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>Notfallwiederherstellung von VMware-VMs und physischen Servern

In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.
 
**Feature** | **Details**
--- | ---
**Linux-Unterstützung** | Es wurde Unterstützung für Red Hat Enterprise Linux 7.6, Red Hat Workstation 6/7, Oracle Linux 6.10/7.6 und für neue Kernelversionen für Ubuntu, Debian und SUSE hinzugefügt.


### <a name="update-rollup-31"></a>Updaterollup 31 

Mit dem [Updaterollup 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) werden die folgenden Updates bereitgestellt.

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Ein Update für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben).
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben).

### <a name="vmware-vmsphysical-servers-replication"></a>Replikation von VMware-VMs/physischen Servern 
In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.
**Feature** | **Details**
--- | ---
**Linux-Unterstützung** | Es wurde Unterstützung für Oracle Linux 6.8 und 6.9/7.0 und für den UEK5-Kernel hinzugefügt.
**LVM** | Unterstützung für LVM- und LVM2-Volumes wurde hinzugefügt.<br/><br/> Das Verzeichnis „/boot“ wird jetzt auf Datenträgerpartitionen und LVM-Volumes unterstützt.
**Verzeichnisse** | Die Unterstützung der folgenden Verzeichnisse wurde für separate Partitionen oder Dateisysteme eingerichtet, die sich nicht auf dem gleichen Systemdatenträger befinden:<br/><br/> „/(root)“, „/boot“, „/usr“, „/usr“, „/local“, „/var“ usw.
**Windows Server 2008** | Dynamische Datenträger werden nun unterstützt.
**Failover** | Verbesserte Failoverzeit für VMware-VMs, bei denen „storvsc“ und „vsbus“ keine Starttreiber sind.
**UEFI-Unterstützung** | Azure-VMs unterstützen den Starttyp UEFI nicht. Sie können lokale physische Server mit UEFI nun mithilfe von Site Recovery zu Azure migrieren. Site Recovery migriert den Server, indem der Starttyp vor der Migration in BIOS konvertiert wird. Site Recovery hat diese Konvertierung zuvor nur für VMs unterstützt. Physische Server mit Windows Server 2012 oder höher werden nun unterstützt.

### <a name="azure-vm-disaster-recovery"></a>Notfallwiederherstellung für virtuelle Azure-Computer
In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
**Linux-Unterstützung** | Es wurde Unterstützung für Oracle Linux 6.8 und 6.9/7.0 und für den UEK5-Kernel hinzugefügt.
**Linux-BRTFS-Dateisystem** | Unterstützung für Azure-VMs
**Azure-VMs in Verfügbarkeitszonen** | Sie können die Replikation in anderen Regionen für Azure-VMs aktivieren, die in Verfügbarkeitszonen bereitgestellt wurden. Sie können nun Replikation für eine Azure-VM aktivieren und das Ziel für das Failover auf eine einzelne VM-Instanz, eine VM in einer Verfügbarkeitsgruppe oder eine VM in einer Verfügbarkeitszone festlegen. Die Einstellung besitzt keine Auswirkungen auf die Replikation. Ankündigung [lesen](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/).
**Firewall-fähiger Speicher (Portal/PowerShell)** | [Speicherkonten mit aktivierter Firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security) werden nun unterstützt.<br/><br/> Im Falle einer Notfallwiederherstellung können Sie Azure-VMs mit nicht verwalteten Datenträgern in Speicherkonten mit aktivierter Firewall in eine andere Azure-Region replizieren.<br/><br/> Sie können Speicherkonten mit aktivierter Firewall als Zielspeicherkonten für nicht verwaltete Datenträger verwenden.<br/><br/> Wird im Portal und mit PowerShell unterstützt.

## <a name="updates-december-2018"></a>Updates (Dezember 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Automatische Updates für die Mobility Service (Azure-VMs)

Site Recovery hat eine Option für automatische Updates der Mobility Service-Erweiterung hinzugefügt. Die Mobility Service-Erweiterung wird auf jeder Azure-VM installiert, die von Site Recovery repliziert wird. Wenn Sie die Replikation aktivieren, wählen Sie aus, ob Site Recovery Updates für die Erweiterung verwalten soll.

Updates erfordern keinen Neustart der VM und wirken sich nicht auf die Replikation aus. [Weitere Informationen](azure-to-azure-autoupdate.md)

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Preisrechner für die Notfallwiederherstellung von Azure-VMs

Notfallwiederherstellung für Azure-VMs verursacht VM-Lizenzierungskosten sowie Netzwerk- und Speicherkosten. Azure bietet einen [Preisrechner](https://aka.ms/a2a-cost-estimator), mit dem Sie diese Kosten herausfinden können. Site Recovery bietet nun eine [Beispielpreisschätzung](https://aka.ms/a2a-cost-estimator), die eine Beispielbereitstellung basierend auf einer Drei-Schichten-App mit sechs VMs mit 12 HDD Standard-Datenträgern und 6 SSD Premium-Datenträgern bewertet.

- Das Beispiel geht von einer Datenänderung von 10 GB pro Tag für Standard und 20 GB für Premium aus.
- Für Ihre individuelle Bereitstellung können Sie die Variablen ändern, um die Kosten zu schätzen.
- Sie können die Anzahl der VMs, die Anzahl und den Typ der verwalteten Datenträger und die erwartete Datenänderungs-Gesamtrate angeben, die für die VMs erwartet wird.
- Darüber hinaus können Sie einen Komprimierungsfaktor anwenden, um die Kosten für Bandbreite zu schätzen.

Ankündigung [lesen](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).


## <a name="updates-october-2018"></a>Updates (Oktober 2018)

### <a name="update-rollup-30"></a>Updaterollup 30 

Mit dem [Updaterollup 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) werden die folgenden Updates bereitgestellt.

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Ein Update für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben).
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben).

### <a name="azure-vm-disaster-recovery"></a>Notfallwiederherstellung für virtuelle Azure-Computer
In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
**Unterstützung für Regionen** | Die Site Recovery-Unterstützung wurde für Australien, Mitte und Australien, Mitte 2 hinzugefügt.
**Unterstützung für die Datenträgerverschlüsselung** | Die Unterstützung der Notfallwiederherstellung für Azure-VMs, die mit Azure Disk Encryption (ADE) mit der Azure AD-App verschlüsselt wurden, wurde hinzugefügt. [Weitere Informationen](azure-to-azure-how-to-enable-replication-ade-vms.md)
**Ausschluss von Datenträgern** | Nicht initialisierte Datenträger werden während der Replikation von Azure-VMs jetzt automatisch ausgeschlossen.
**Firewall-fähiger Speicher (PowerShell)** | [Speicherkonten mit aktivierter Firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security) werden nun unterstützt.<br/><br/> Im Falle einer Notfallwiederherstellung können Sie Azure-VMs mit nicht verwalteten Datenträgern in Speicherkonten mit aktivierter Firewall in eine andere Azure-Region replizieren.<br/><br/> Sie können Speicherkonten mit aktivierter Firewall als Zielspeicherkonten für nicht verwaltete Datenträger verwenden.<br/><br/> Dies wird nur mit PowerShell unterstützt.


### <a name="update-rollup-29"></a>Updaterollup 29 

Mit dem [Updaterollup 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) werden die folgenden Updates bereitgestellt.

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Ein Update für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben).
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben).


## <a name="updates-august-2018"></a>Updates (August 2018)

### <a name="update-rollup-28"></a>Updaterollup 28 

Mit dem [Updaterollup 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) werden die folgenden Updates bereitgestellt.

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Ein Update für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben).
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben).

### <a name="azure-vm-disaster-recovery"></a>Notfallwiederherstellung für virtuelle Azure-Computer 
In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
**Linux-Unterstützung** | Die Unterstützung für Red Hat Enterprise Linux 6.10 und CentOS 6.10 wurde hinzugefügt.<br/><br/>
**Unterstützung für Clouds** | Die Notfallwiederherstellung für Azure-VMs in der Azure Deutschland-Cloud wird nun unterstützt.
**Abonnementübergreifende Notfallwiederherstellung** | Die Replikation von Azure-VMs von einer Region zu einer anderen Region in einem anderen Abonnement innerhalb desselben Azure Active Directory-Mandanten wird nun unterstützt. [Weitere Informationen](https://aka.ms/cross-sub-blog)

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Notfallwiederherstellung für VMware-VM/physische Server 
In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
**Linux-Unterstützung** | Es wurde Unterstützung für Red Hat Enterprise Linux 6.10 und CentOS 6.10 hinzugefügt.<br/><br/> Linux-basierte VMs, die den Partitionsstil der GUID-Partitionstabelle (GPT) im BIOS-Legacykompatibilitätsmodus verwenden, werden jetzt unterstützt. Lesen Sie die [häufig gestellten Fragen zu Azure-VM](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) für weitere Informationen. 
**Notfallwiederherstellung für VMs nach der Migration** | Unterstützung für die Notfallwiederherstellung in einer sekundären Region für eine lokale VMware-VM, die zu Azure migriert wurde, ohne dass der Mobility-Dienst auf der VM deinstalliert werden muss, bevor die Replikation aktiviert wird.
**Windows Server 2008** | Die Migration von Computern mit Windows Server 2008 R2/2008 64-Bit und 32-Bit wird nun unterstützt.<br/><br/> Es wird lediglich die Migration unterstützt (Replikation und Failover). Failbacks werden nicht unterstützt.

## <a name="updates-july-2018"></a>Updates (Juli 2018)

### <a name="update-rollup-27-july-2018"></a>Updaterollup 27 (Juli 2018)

Mit dem [Updaterollup 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) werden die folgenden Updates bereitgestellt.

**Aktualisieren** | **Details**
--- | ---
**Anbieter und Agents** | Ein Update für Site Recovery-Agents und -Anbieter (wie im Rollup beschrieben).
**Problemkorrekturen/Verbesserungen** | Mehrere Fehlerbehebungen und Verbesserungen (wie im Rollup beschrieben).

### <a name="azure-vm-disaster-recovery"></a>Notfallwiederherstellung für virtuelle Azure-Computer 

In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
**Linux-Unterstützung** | Es wurde Unterstützung für Red Hat Enterprise Linux 7.5 hinzugefügt.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Notfallwiederherstellung für VMware-VM/physische Server 

In der folgenden Tabelle werden die Features aufgeführt, die in diesem Monat neu hinzugefügt wurden.

**Feature** | **Details**
--- | ---
**Linux-Unterstützung** | Es wurde Unterstützung für Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12 hinzugefügt.



## <a name="next-steps"></a>Nächste Schritte

Auf dem neuesten Stand mit unseren Updates auf der Seite [Azure-Updates](https://azure.microsoft.com/updates/?product=site-recovery) bleiben.
