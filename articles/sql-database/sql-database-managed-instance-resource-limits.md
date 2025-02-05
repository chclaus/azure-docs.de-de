---
title: Ressourceneinschränkungen für Azure SQL-Datenbank – verwaltete Instanz | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht über die Ressourceneinschränkungen für verwaltete Azure SQL-Datenbank-Instanzen.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 10/02/2019
ms.openlocfilehash: c3f8189cf1b09b38b641b92b9234fde618839987
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828806"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Übersicht über Ressourceneinschränkungen für verwaltete Azure SQL-Datenbank-Instanzen

Dieser Artikel bietet eine Übersicht über die technischen Eigenschaften und Ressourceneinschränkungen für verwaltete Azure SQL-Datenbank-Instanzen und erläutert, wie Sie eine Anforderung zur Erhöhung dieser Grenzwerte erstellen.

> [!NOTE]
> Unterschiede bei den unterstützten Funktionen und T-SQL-Anweisungen sind unter [Funktionsunterschiede](sql-database-features.md) und [Unterstützung von T-SQL-Anweisungen](sql-database-managed-instance-transact-sql-information.md) zu finden. Allgemeine Unterschiede zwischen Dienstebenen in Einzeldatenbanken und verwalteten Instanzen finden Sie unter [Vergleich der Dienstebenen](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="instance-level-resource-limits"></a>Ressourceneinschränkungen auf Instanzebene

Eine verwaltete Instanz weist Merkmale und Ressourceneinschränkungen auf, die von der zugrunde liegende Infrastruktur und Architektur abhängen. Die Grenzwerte hängen von der Hardwaregeneration und der Dienstebene ab.

### <a name="hardware-generation-characteristics"></a>Merkmale der Hardwaregeneration

Eine verwaltete Azure SQL-Datenbank-Instanz kann auf zwei Hardwaregenerationen bereitgestellt werden: Gen4 und Gen5. Hardwaregenerationen weisen unterschiedliche Merkmale auf, wie in der folgenden Tabelle beschrieben:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardware | Intel E5-2673 v3-Prozessoren (Haswell) mit 2,4 GHz, angefügte SSD, virtueller Kern = 1 physischer Kern | Intel E5-2673 v4-Prozessoren (Broadwell) mit 2,3 GHz, schnelle NVMe-SSD, virtueller Kern = 1 LP (Hyperthread) |
| Anzahl der virtuellen Kerne | 8, 16, 24 virtuelle Kerne | 4, 8, 16, 24, 32, 40, 64, 80 virtuelle Kerne |
| Max. Arbeitsspeicher (Verhältnis Arbeitsspeicher/Kerne) | 7 GB pro V-Kern<br/>Fügen Sie weitere virtuelle Kerne hinzu, um mehr Arbeitsspeicher zu erhalten. | 5,1 GB pro virtuellem Kern<br/>Fügen Sie weitere virtuelle Kerne hinzu, um mehr Arbeitsspeicher zu erhalten. |
| Max. In-Memory-OLTP-Arbeitsspeicher | Grenzwert für Instanzen: 1–1,5 GB pro virtuellem Kern| Grenzwert für Instanzen: 0,8–1,65 GB pro virtuellem Kern |
| Maximal reservierter Instanzspeicher |  Allgemein: 8 TB<br/>Unternehmenskritisch: 1 TB | Allgemein: 8 TB<br/> Unternehmenskritisch: 1 TB, 2 TB oder 4 TB, je nach Anzahl der Kerne |

> [!IMPORTANT]
> - Gen4-Hardware wird eingestellt. Es wird empfohlen, neue verwaltete Instanzen auf Gen5-Hardware bereitzustellen.
> - Gen4-Hardware ist zu diesem Zeitpunkt weiterhin nur in den folgenden Regionen verfügbar: „Europa, Norden“, „Europa, Westen“, „USA, Osten“, „USA, Süden-Mitte“, „USA, Norden-Mitte“, „USA, Westen 2“, „USA, Mitte“, „Kanada, Mitte“, „Indien, Süden“, „Asien, Südosten“ und „Südkorea, Mitte“.

#### <a name="in-memory-oltp-available-space"></a>Verfügbarer Speicherplatz für In-Memory-OLTP 

Die Menge des für In-Memory-OLTP verfügbaren Speicherplatzes auf der Dienstebene [Unternehmenskritisch](sql-database-service-tier-business-critical.md) hängt von der Anzahl der virtuellen Kerne und der Hardwaraegeneration ab. In der folgenden Tabelle sind die Grenzwerte für den Arbeitsspeicher aufgelistet, die für In-Memory-OLTP-Objekte verwendet werden können.

| Arbeitsspeicher für In-Memory-OLTP  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 virtuelle Kerne  | 3,14 GB | |   
| 8 virtuelle Kerne  | 6,28 GB | 8 GB |
| 16 virtuelle Kerne | 15,77 GB | 20 GB |
| 24 virtuelle Kerne | 25,25 GB | 36 GB |
| 32 virtuelle Kerne | 37,94 GB | |
| 40 virtuelle Kerne | 52,23 GB | |
| 64 virtuelle Kerne | 99,9 GB    | |
| 80 virtuelle Kerne | 131,68 GB| |

### <a name="service-tier-characteristics"></a>Merkmale des Diensttarifs

Die verwaltete Instanz besitzt zwei Dienstebenen: [Universell](sql-database-service-tier-general-purpose.md) und [Unternehmenskritisch](sql-database-service-tier-business-critical.md). Diese Tarife bieten [verschiedene Funktionen](sql-database-service-tiers-general-purpose-business-critical.md), die in der folgenden Tabelle beschrieben sind:

| **Feature** | **Allgemeiner Zweck** | **Unternehmenskritisch** |
| --- | --- | --- |
| Anzahl der virtuellen Kerne\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 |
| Max. Arbeitsspeicherbelegung | Gen4: 56GB – 168GB (7GB/V-Kern)<br/>Gen5: 20,4 GB – 408 GB (5,1 GB/V-Kern)<br/>Fügen Sie weitere virtuelle Kerne hinzu, um mehr Arbeitsspeicher zu erhalten. | Gen4: 56GB – 168GB (7GB/V-Kern)<br/>Gen5: 20,4 GB – 408 GB (5,1 GB/V-Kern)<br/>Fügen Sie weitere virtuelle Kerne hinzu, um mehr Arbeitsspeicher zu erhalten. |
| Max. Instanzspeichergröße (reserviert) | – 2 TB für 4 virtuelle Kerne (nur Gen5)<br/>– 8 TB für andere Größen | Gen4: 1 TB <br/> Gen5: <br/>– 1 TB für 4, 8, 16 virtuelle Kerne<br/>- 2 TB für 24 virtuelle Kerne<br/>- 4 TB für 32, 40, 64, 80 virtuelle Kerne |
| Max. Datenbankgröße | Bis zur derzeit verfügbaren Instanzgröße (max. 2 TB – 8 TB, abhängig von der Anzahl der virtuellen Kerne). | Bis zur derzeit verfügbaren Instanzgröße (max. 1 TB – 4 TB, abhängig von der Anzahl der virtuellen Kerne). |
| Max. TempDB-Größe | Begrenzt auf 24 GB/V-Kern (96 – 1.920 GB) und die derzeit verfügbare Instanzspeichergröße.<br/>Fügen Sie weitere virtuelle Kerne hinzu, um mehr TempDB-Speicherplatz zu erhalten. | Bis zur aktuell verfügbaren Instanzspeichergröße. Die Größe der TempDB-Protokolldatei ist derzeit auf 24 GB pro virtuellem Kern beschränkt. |
| Max. Anzahl von Datenbanken pro Instanz | 100, außer wenn der Grenzwert für die Instanzspeichergröße erreicht wurde. | 100, außer wenn der Grenzwert für die Instanzspeichergröße erreicht wurde. |
| Max. Anzahl von Datenbankdateien pro Instanz | Bis zu 280, außer wenn die Instanzspeichergröße oder der Grenzwert für [Azure Premium Disk-Speicherbelegungsplatz](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files) erreicht wurde. | 32.767 Dateien pro Datenbank, außer wenn der Grenzwert für die Instanzspeichergröße erreicht wurde. |
| Maximale Größe der Datendatei | Begrenzt auf die derzeit verfügbare Instanzspeichergröße (max. 2 TB – 8 TB) und den [Azure Premium Disk-Speicherbelegungsplatz](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files). | Begrenzt auf die derzeit verfügbare Instanzspeichergröße (bis zu 1 TB – 4 TB). |
| Maximale Protokolldateigröße | Begrenzt auf 2 TB und die derzeit verfügbare Instanzspeichergröße. | Begrenzt auf 2 TB und die derzeit verfügbare Instanzspeichergröße. |
| Daten-/Protokoll-IOPS (ungefähr) | 500 bis 7.500 pro Datei<br/>\*[Erhöhen Sie die Dateigröße, um den IOPS-Wert zu erhöhen](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 5,5 K - 110 K (1.375/V-Kern)<br/>Fügen Sie weitere virtuelle Kerne hinzu, um die E/A-Leistung zu verbessern. |
| Grenzwert für den Protokollschreibdurchsatz (pro Instanz) | 3 MB/Sek. pro virtuellem Kern<br/>Max. 22 MB/Sek. | 4 MB/Sek. pro virtuellem Kern<br/>Max. 48 MB/Sek. |
| Datendurchsatz (ungefähr) | 100 bis 250 MB/Sek. pro Datei<br/>\*[Erhöhen Sie die Dateigröße, um die E/A-Leistung zu verbessern.](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | Nicht begrenzt. |
| E/A-Speicherlatenz (ungefähr) | 5 – 10 ms | 1 – 2 ms |
| In-Memory-OLTP | Nicht unterstützt | Verfügbar |
| Max. Sitzungen | 30000 | 30000 |
| [Schreibgeschützte Replikate](sql-database-read-scale-out.md) | 0 | 1 (im Preis inbegriffen) |

> [!NOTE]
> - Die **derzeit verfügbare Instanzspeichergröße** ist die Differenz zwischen der reservierten Instanzgröße und dem belegten Speicherplatz.
> - Sowohl die Daten- als auch die Protokolldateigröße in den Benutzer- und Systemdatenbanken sind in der Instanzspeichergröße enthalten, die mit dem Grenzwert für die maximale Speichergröße verglichen wird. Ermitteln Sie mithilfe der Systemansicht <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> den von Datenbanken verwendeten Gesamtspeicherplatz. Fehlerprotokolle werden nicht beibehalten und sind nicht in der Größe enthalten. Sicherungen sind nicht in der Speichergröße enthalten.
> - Durchsatz und IOPS hängen auch von der Seitengröße ab, die nicht explizit durch die verwaltete Instanz eingeschränkt wird.
> Sie können ein weiteres lesbares Replikat in einer anderen Azure-Region mithilfe von Autofailover-Gruppen erstellen.

> [!NOTE]
> Lesen Sie weitere Informationen zu den [Ressourceneinschränkungen in verwalteten Instanzenpools in diesem Artikel.](sql-database-instance-pools.md#instance-pools-resource-limitations)

## <a name="supported-regions"></a>Unterstützte Regionen

Eine verwaltete Instanz kann nur in [unterstützten Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all) erstellt werden. Wenn Sie eine verwaltete Instanz in einer Region erstellen möchten, die derzeit nicht unterstützt wird, können Sie eine [Supportanfrage über das Azure-Portal senden](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Unterstützte Abonnementtypen

Für eine verwaltete Instanz wird derzeit nur die Bereitstellung bei folgenden Abonnementtypen unterstützt:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Clouddienstanbieter (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Abonnements mit monatlicher Azure-Gutschrift für Visual Studio-Abonnenten](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Regionale Ressourcenbeschränkungen

Unterstützte Abonnementtypen können eine begrenzte Anzahl von Ressourcen pro Region umfassen. Abhängig vom Abonnementtyp gibt es bei der verwalteten Instanz zwei Standardgrenzwerte pro Azure-Region (die bei Bedarf durch das Erstellen einer speziellen [Supportanfrage im Azure-Portal](#obtaining-a-larger-quota-for-sql-managed-instance) erhöht werden können):

- **Subnetzlimit**: Die maximale Anzahl von Subnetzen bei Bereitstellung verwalteter Instanzen in einer einzelnen Region.
- **Grenzwert für virtuelle Kerneinheiten**: Die maximale Anzahl von virtuellen Kerneinheiten, die in einer einzelnen Region über alle Instanzen hinweg bereitgestellt werden können. Ein virtueller Kern „Universell“ verwendet eine einzige virtuelle Kerneinheit, und ein virtueller Kern „Unternehmenskritisch“ verwendet 4 virtuelle Kerneinheiten. Die Gesamtanzahl der Instanzen ist nicht begrenzt, solange sie innerhalb des Limits der virtuellen Kerneinheiten liegt.

> [!Note]
> Diese Limits sind Standardeinstellungen und keine technischen Einschränkungen. Diese Limits können bei Bedarf erhöht werden, indem Sie eine spezielle [Supportanfrage im Azure-Portal](#obtaining-a-larger-quota-for-sql-managed-instance) erstellen, falls Sie mehr verwaltete Instanzen in der aktuellen Region benötigen. Alternativ können Sie auch neue verwaltete Instanzen in einer anderen Azure-Region erstellen, ohne Supportanfragen zu senden.

In der folgenden Tabelle werden die **standardmäßigen regionalen Grenzwerte** für unterstützte Abonnementtypen angezeigt (standardmäßige Grenzwerte können mithilfe der unten beschriebenen Supportanfrage erweitert werden):

|Abonnementtyp| Max. Anzahl von Subnetzen für verwaltete Instanzen | Max number of vCore units* (Maximale Anzahl von virtuellen Kerneinheiten) |
| :---| :--- | :--- |
|Nutzungsbasierte Bezahlung|3|320|
|CSP |8 (15 in manchen Regionen**)|960 (1440 in manchen Regionen**)|
|Pay-as-you-go Dev/Test|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 in manchen Regionen**)|960 (1440 in manchen Regionen**)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional und MSDN Platforms|2|32|

\* Berücksichtigen Sie bei der Planung von Bereitstellungen, dass die Dienstebene „Unternehmenskritisch“ (Business Critical, BC) viermal (4-mal) mehr v-Kern-Kapazität erfordert als die Dienstebene „Universell“ (General Purpose, GP). Beispiel:  1 virtueller Kern „Universell“ = 1 V-Kern-Einheit, und 1 virtueller Kern „Unternehmenskritisch“ = 4 V-Kern-Einheiten. Um die Nutzungsanalyse hinsichtlich der Standardgrenzwerte zu vereinfachen, fassen Sie die virtuellen Kerneinheiten für alle Subnetze in der Region zusammen, in der verwaltete Instanzen bereitgestellt werden, und vergleichen Sie die Ergebnisse mit den Grenzwerten für Instanzeinheiten Ihres Abonnementtyps. Der Grenzwert **Max number of vCore units** (Maximale Anzahl von virtuellen Kerneinheiten) gilt für jedes Abonnement in einer Region. Es gibt keinen Grenzwert pro individuellem Subnetz, außer dass die Summe aller in mehreren Subnetzen bereitgestellten virtuellen Kerne niedriger oder gleich der **maximalen Anzahl von virtuellen Kerneinheiten** sein muss.

\*\* Höhere Grenzwerte für Subnetze und virtuelle Kerne sind in den folgenden Regionen verfügbar: „Australien, Osten“, „USA, Osten“, „USA, Osten 2“, „Europa, Norden“, „USA, Süden-Mitte“, „Asien, Südosten“, „Vereinigtes Königreich, Süden“, „Europa, Westen“, „USA, Westen 2“.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Abrufen eines größeren Kontingents für verwaltete SQL-Instanz

Wenn Sie mehr verwaltete Instanzen in Ihren aktuellen Regionen benötigen, senden Sie eine Supportanfrage zum Erweitern des Kontingents über das Azure-Portal.
Leiten Sie den Prozess zum Abrufen eines größeren Kontingents auf folgende Weise ein:

1. Öffnen Sie **Hilfe + Support**, und klicken Sie auf **Neue Supportanfrage**.

   ![Hilfe und Support](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Führen Sie auf der Registerkarte „Grundlagen“ für die neue Supportanfrage die folgenden Schritte aus:
   - Wählen Sie unter **Problemtyp** den Eintrag **Grenzwerte für Dienste und Abonnements (Kontingente)** aus.
   - Wählen Sie unter **Abonnement** Ihr Abonnement aus.
   - Wählen Sie unter **Kontingenttyp** den Eintrag **Verwaltete SQL-Datenbank-Instanz** aus.
   - Wählen Sie unter **Supportplan** Ihren Supportplan aus.

     ![Problemtyp „Kontingent“](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Klicken Sie auf **Weiter**.
4. Führen Sie auf der Registerkarte **Problem** für die neue Supportanfrage die folgenden Schritte aus:
   - Wählen Sie unter **Schweregrad** den Schweregrad des Problems aus.
   - Geben Sie unter **Details** zusätzliche Informationen zu Ihrem Problem an, einschließlich Fehlermeldungen.
   - Fügen Sie unter **Dateiupload** eine Datei mit weiteren Informationen an (bis zu 4 MB).

     ![Problemdetails](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Eine gültige Anforderung sollte Folgendes enthalten:
     > - Region, in der der Grenzwert für Abonnements erhöht werden muss.
     > - Erforderliche Anzahl von virtuellen Kernen pro Dienstebene in vorhandenen Subnetzen nach Erhöhung des Kontingents (wenn eines der vorhandenen Subnetze erweitert werden muss).
     > - Erforderliche Anzahl neuer Subnetze und Gesamtanzahl der virtuellen Kerne pro Dienstebene in den neuen Subnetzen (wenn Sie verwaltete Instanzen in neuen Subnetzen bereitstellen müssen).

5. Klicken Sie auf **Weiter**.
6. Geben Sie auf der Registerkarte „Kontaktinformationen“ für die neue Supportanfrage Ihre bevorzugte Kontaktmethode (E-Mail oder Telefon) und die Kontaktdetails ein.
7. Klicken Sie auf **Create**.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur verwalteten Instanz finden Sie unter [Was ist eine verwaltete Instanz?](sql-database-managed-instance.md).
- Preisinformationen finden Sie unter [Preise – verwaltete Azure SQL-Datenbank-Instanzen ](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Im [Schnellstarthandbuch](sql-database-managed-instance-get-started.md) erfahren Sie, wie Sie Ihre erste verwaltete Instanz erstellen.
