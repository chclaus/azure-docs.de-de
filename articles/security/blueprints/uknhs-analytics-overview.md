---
title: Azure Security and Compliance Blueprint – Datenanalyse gemäß NHS (Vereinigtes Königreich)
description: Azure Security and Compliance Blueprint – Datenanalyse gemäß NHS (Vereinigtes Königreich)
services: security
author: jomolesk
ms.assetid: 103dff31-e262-44a6-9b50-3b3467c0cb3a
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: ad42bb7738e483b6bba6fa601a054e466b8d87c6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259573"
---
# <a name="azure-security-and-compliance-blueprint-data-analytics-for-uk-nhs"></a>Azure-Blaupause für Sicherheit und Compliance: Datenanalysen für UK NHS

## <a name="overview"></a>Übersicht

Dieser Azure Security and Compliance Blueprint bietet eine Referenzarchitektur und Anleitungen für eine geeignete Datenanalyselösung für das Sammeln, Speichern und Abrufen von Daten des Gesundheitswesens. Diese Lösung veranschaulicht Möglichkeiten, wie Kunden die Hinweise im von [NHS Digital](https://digital.nhs.uk/) als Partner für das Department of Health and Social Care (DHSC) des Vereinigten Königreichs veröffentlichten [Cloud Security Good Practice Guide](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide) (Leitfaden für bewährte Methoden in der Cloudsicherheit) einhalten können. Der Leitfaden für bewährte Methoden in der Cloudsicherheit basiert auf den 14 [Cloudsicherheitsprinzipien](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles), die vom britischen National Cyber Security Center (NCSC) veröffentlicht wurden.

Diese Referenzarchitektur, die Implementierungsanleitung und das Bedrohungsmodell sollen Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollten nicht ohne weitere Konfiguration in einer Produktionsumgebung eingesetzt werden. Der Kunde ist für die Durchführung einer angemessenen Sicherheits- und Konformitätsbewertung jeder Lösung verantwortlich, die mit dieser Architektur erstellt wurde, da die Anforderungen je nach den Besonderheiten der jeweiligen Implementierung des Kunden unterschiedlich sein können.

## <a name="architecture-diagram-and-components"></a>Architekturdiagramm und Komponenten

Diese Lösung umfasst eine Analyseplattform, auf der Kunden ihre eigenen Analysetools aufbauen können. Die Referenzarchitektur stellt einen generischen Anwendungsfall dar. Dabei geben Kunden Daten über einen Massenimport durch den SQL- bzw. den Datenadministrator oder über betriebliche Datenupdates durch einen betrieblichen Benutzer ein. Bei beiden Möglichkeiten wird Azure Functions für den Import von Daten in Azure SQL-Datenbank integriert, und für alle externen Verbindungen ist TLS v1.2 erforderlich. Azure Functions muss vom Kunden über das Azure-Portal konfiguriert werden, damit die Importaufgaben entsprechend den jeweiligen Analyseanforderungen des Kunden verarbeitet werden können.

Azure bietet den Kunden verschiedene Dienste zur Berichterstellung und Analyse. Diese Lösung integriert Azure Analysis Services in Azure SQL-Datenbank, damit Daten schnell durchsucht werden und durch eine bessere Modellierung von Kundendaten schneller Ergebnisse geliefert werden können. Azure Analysis Services ist eine Form von maschinellem Lernen und ist darauf ausgerichtet, die Abfragegeschwindigkeit zu erhöhen, indem neue Beziehungen zwischen Datasets ermittelt werden. Wenn die Daten über verschiedene statistische Funktionen trainiert wurden, können bis zu sieben zusätzliche Abfragepools (mit dem Kundenserver acht) mit denselben tabellarischen Modellen synchronisiert werden, um die Abfrageworkload auszuweiten und die Antwortzeiten zu reduzieren.

Für erweiterte Analysen und Berichterstattungen können Azure SQL-Datenbanken mit Columnstore-Indizes konfiguriert werden. Azure Analytics Services und Azure SQL-Datenbanken können abhängig von der Nutzung durch den Kunden zentral hoch- bzw. herunterskaliert oder vollständig ausgeschaltet werden. Jeglicher SQL-Datenverkehr wird mit SSL über selbstsignierte Zertifikate verschlüsselt. Azure empfiehlt als bewährte Methode die Verwendung einer vertrauenswürdigen Zertifizierungsstelle für erweiterte Sicherheit.

Sobald Daten in Azure SQL-Datenbank hochgeladen und von Azure Analysis Services trainiert wurden, werden sie sowohl von dem betrieblichen Benutzer als auch vom SQL- bzw. Datenadministrator über Power BI verarbeitet. Power BI zeigt Daten intuitiv an und erfasst Informationen für mehrere Datasets, um zusätzliche Erkenntnisse zu erhalten. Power BI weist ein hohes Maß an Flexibilität auf und kann in Azure SQL-Datenbank integriert werden. Dadurch wird sichergestellt, dass Kunden eine Konfiguration vornehmen können, damit dieser Dienst entsprechend der jeweiligen Geschäftsanforderungen die verschiedensten Szenarios verarbeiten kann.

Die Lösung verwendet Azure Storage-Konten, die Kunden für die Verwendung von Speicherdienstverschlüsselung konfigurieren können, um die Vertraulichkeit ruhender Daten zu wahren. Azure speichert drei Kopien der Daten im ausgewählten Rechenzentrum eines Kunden, um die Resilienz zu gewährleisten. Die geografisch redundante Speicherung stellt sicher, dass die Daten in ein hunderte von Kilometern entferntes sekundäres Rechenzentrum repliziert und dort wiederum als drei Kopien gespeichert werden, um zu verhindern, dass ein unerwünschtes Ereignis im primären Rechenzentrum des Kunden zu einem Datenverlust führt.

Für höhere Sicherheit werden alle Ressourcen in dieser Lösung als Ressourcengruppe über Azure Resource Manager verwaltet. Die rollenbasierte Zugriffssteuerung von Azure Active Directory dient zur Steuerung des Zugriffs auf die bereitgestellten Ressourcen, einschließlich ihrer Schlüssel im Azure Key Vault. Die Systemintegrität wird durch Azure Security Center und Azure Monitor überwacht. Der Kunde konfiguriert das Überwachen von Diensten zum Speichern von Protokollen und Anzeigen der Systemintegration in nur einem Dashboard, durch das man leicht navigieren kann.

Azure SQL-Datenbank wird in der Regel über SQL Server Management Studio (SSMS) verwaltet. SSMS wird über einen lokalen Computer ausgeführt, der für den Zugriff auf Azure SQL-Datenbank über eine sichere VPN- oder ExpressRoute-Verbindung konfiguriert ist. **Microsoft empfiehlt das Konfigurieren einer VPN- oder ExpressRoute-Verbindung für die Verwaltung und den Datenimport in die Ressourcengruppe der Referenzarchitektur**.

![Abbildung der Referenzarchitektur für Analytics gemäß NHS (Vereinigtes Königreich)](images/uknhs-analytics-architecture.png?raw=true "Abbildung der Referenzarchitektur für Analytics gemäß NHS (Vereinigtes Königreich)")

Diese Lösung verwendet die folgenden Azure-Dienste. Details zur Bereitstellungsarchitektur finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).

- Azure Active Directory
- Azure Analysis Services
- Azure-Automatisierung
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure-Funktionen
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure SQL-Datenbank
- Azure Storage
- Virtuelles Azure-Netzwerk
    - (1) /16 Netzwerk
    - (2) /24 Netzwerke
    - (2) Netzwerksicherheitsgruppen
- Power BI-Dashboard

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur

Im folgenden Abschnitt werden die Elemente für Entwicklung und Implementierung beschrieben.

**Azure Functions**: [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) ist ein serverloser Computedienst, mit dem Benutzer Code bedarfsgesteuert ausführen können, ohne eine explizite Infrastruktur bereitstellen oder verwalten zu müssen. Verwenden Sie Azure Functions, um ein Skript oder einen Codeabschnitt als Reaktion auf verschiedene Ereignisse auszuführen.

**Azure Analysis Services**: [Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) umfasst Funktionen zum Modellieren von Unternehmensdaten und zur Integration in Datenplattformdienste für Azure. Azure Analysis Services beschleunigt das Durchsuchen von sehr großen Datenmengen, indem Daten aus mehreren Quellen in ein einzelnes Datenmodell zusammengefasst werden.

### <a name="virtual-network"></a>Virtuelles Netzwerk

Die Architektur definiert ein privates virtuelles Netzwerk mit dem Adressraum 10.200.0.0/16.

**Netzwerksicherheitsgruppen**: [Netzwerksicherheitsgruppen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) enthalten Zugriffssteuerungslisten, die den Datenverkehr in einem virtuellen Netzwerk zulassen oder ablehnen. Netzwerksicherheitsgruppen können verwendet werden, um Datenverkehr auf der Ebene eines Subnetzes oder eines einzelnen Computers zu schützen. Es gibt die folgenden Netzwerksicherheitsgruppen:

  - 1 Netzwerksicherheitsgruppe für Active Directory
  - 1 Netzwerksicherheitsgruppe für die Workload

Jede der Netzwerksicherheitsgruppen verfügt über bestimmte offene Ports und Protokolle, damit die Lösung sicher und richtig ausgeführt werden kann. Darüber hinaus werden die folgenden Konfigurationen für jede Netzwerksicherheitsgruppe aktiviert:

- [Diagnoseprotokolle und -ereignisse](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sind aktiviert und werden im Speicherkonto gespeichert.
- Azure Monitor-Protokolle ist mit den [Diagnoseprotokollen der Netzwerksicherheitsgruppe](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) verbunden.

**Subnetze**: Jedes Subnetz ist seiner entsprechenden Netzwerksicherheitsgruppe zugeordnet.

### <a name="data-in-transit"></a>Daten während der Übertragung

Azure verschlüsselt standardmäßig die gesamte Kommunikation zu und von Azure-Rechenzentren. Alle Transaktionen für Azure Storage über das Azure-Portal werden per HTTPS durchgeführt.

### <a name="data-at-rest"></a>Ruhende Daten

Die Architektur schützt ruhende Daten durch die Verwendung von Verschlüsselung, Datenbanküberwachung und anderen Maßnahmen.

**Azure Storage**: Um die Anforderungen für verschlüsselte ruhende Daten zu erfüllen, wird überall in [Azure Storage](https://azure.microsoft.com/services/storage/) die [Speicherdienstverschlüsselung](../../storage/common/storage-service-encryption.md) verwendet. Dadurch werden Daten geschützt, um die organisatorischen Sicherheitszusagen und Konformitätsanforderungen gemäß der Definition von NHS Digital einzuhalten.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) nutzt das BitLocker-Feature von Windows, um eine Volumeverschlüsselung für Datenträger bereitzustellen. Die Lösung ist in Azure Key Vault integriert, um die Steuerung und Verwaltung der Datenträgerverschlüsselungsschlüssel zu erleichtern.

**Azure SQL-Datenbank**: In der Azure SQL-Datenbankinstanz werden die folgenden Datenbanksicherheitsmaßnahmen verwendet:

- Die [Active Directory-Authentifizierung und -Autorisierung](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) ermöglicht die zentrale Verwaltung der Identitäten von Datenbankbenutzern und anderen Microsoft-Diensten.
- Die [SQL-Datenbank-Überprüfung](../../sql-database/sql-database-auditing.md) verfolgt Datenbankereignisse nach und schreibt diese in ein Überwachungsprotokoll in einem Azure Storage-Konto.
- Azure SQL-Datenbank ist so konfiguriert, dass [transparente Datenverschlüsselung](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) (Transparent Data Encryption) für die Echtzeitverschlüsselung und -entschlüsselung der Datenbank, der zugehörigen Sicherungen und der Transaktionsprotokolldateien verwendet wird, um ruhende Informationen zu schützen. Die transparente Datenverschlüsselung gewährleistet, dass gespeicherte Daten keinen unbefugten Zugriffen ausgesetzt sind.
- [Firewallregeln](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) verhindern sämtlichen Zugriff auf Datenbankserver, bis die richtigen Berechtigungen erteilt werden. Die Firewall gewährt den Datenbankzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.
- Die [SQL-Bedrohungserkennung](../../sql-database/sql-database-threat-detection.md) ermöglicht die Erkennung und Reaktion auf potenzielle Bedrohungen, sobald sie auftreten. Dazu werden Sicherheitswarnungen bei verdächtigen Datenbankaktivitäten, potenziellen Sicherheitslücken, Angriffen durch Einschleusung von SQL-Befehlen und anormalen Mustern beim Datenbankzugriff ausgegeben.
- [Encrypted-Spalten](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) stellen sicher, dass vertrauliche Daten im Datenbanksystem niemals im Klartextformat angezeigt werden. Nach dem Aktivieren der Datenverschlüsselung können nur Clientanwendungen oder Anwendungsserver, die Zugriff auf die Schlüssel haben, auf Klartextdaten zugreifen.
- Die [dynamische Datenmaskierung in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) schränkt die Offenlegung vertraulicher Daten ein, indem diese Daten für nicht berechtigte Benutzer maskiert werden. Die dynamische Datenmaskierung kann automatisch potenziell vertrauliche Daten ermitteln und eine geeignete Maskierung vorschlagen. Dies hilft, den Zugriff auf die Daten zu identifizieren und so zu reduzieren, dass sie die Datenbank nicht durch unberechtigten Zugriff verlassen. Kunden sind dafür verantwortlich, die Einstellungen für die dynamische Datenmaskierung an ihr Datenbankschema anzupassen.

### <a name="identity-management"></a>Identitätsverwaltung

Die folgenden Technologien enthalten Funktionen zum Verwalten des Zugriffs auf Daten in der Azure-Umgebung:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) ist der mehrinstanzenfähige cloudbasierte Verzeichnis- und Identitätsverwaltungsdienst von Microsoft. Alle Benutzer für diese Lösung werden in Azure Active Directory erstellt, so auch die Benutzer, die auf die Azure SQL-Datenbank zugreifen.
- Authentifizierung für die Anwendung erfolgt mithilfe von Azure Active Directory. Weitere Informationen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Darüber hinaus wird für Verschlüsselung der Datenbankspalten Azure Active Directory verwendet, um die Anwendung in der Azure SQL-Datenbank zu authentifizieren. Weitere Informationen finden Sie unter [Schützen von vertraulichen Daten in SQL-Datenbank](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Die [rollenbasierte Zugriffssteuerung in Azure](../../role-based-access-control/role-assignments-portal.md) (Role-Based Access Control) ermöglicht Administratoren das Definieren präziser Zugriffsberechtigungen, um jeweils nur den Zugriff zu gewähren, den Benutzer zum Erledigen ihrer Arbeit benötigen. Anstatt jedem Benutzer uneingeschränkte Benutzerberechtigungen für Azure-Ressourcen zu gewähren, können Administratoren auch nur bestimmte Aktionen für den Zugriff auf Daten erlauben. Der Abonnementzugriff ist auf den Abonnementadministrator beschränkt.
- Mit [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) können Kunden die Anzahl von Benutzern mit Zugriff auf bestimmte Informationen einschränken. Administratoren können mit Azure Active Directory Privileged Identity Management privilegierte Identitäten und den damit verbundenen Zugriff auf Ressourcen erkennen, einschränken und überwachen. Diese Funktionalität kann auch verwendet werden, um einen bedarfsgesteuerten Just-in-Time-Administratorzugriff zu erzwingen.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) erkennt potenzielle Sicherheitsrisiken, die sich auf die Identitäten einer Organisation auswirken, konfiguriert automatisierte Antworten auf erkannte verdächtige Aktionen, die im Zusammenhang mit Identitäten der Organisation stehen, untersucht verdächtige Vorfälle und führt die entsprechenden Aktionen zu deren Behebung aus.

### <a name="security"></a>Sicherheit

**Geheimnisverwaltung**: Die Lösung verwendet [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) für die Verwaltung von Schlüsseln und Geheimnissen. Azure Key Vault unterstützt Sie dabei, kryptografische Schlüssel und Geheimnisse zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Die folgenden Funktionen von Azure Key Vault unterstützen Kunden beim Schützen von und Zugreifen auf diese Daten:

- Erweiterte Zugriffsrichtlinien werden auf Bedarfsbasis konfiguriert.
- Key Vault-Zugriffsrichtlinien werden mit minimalen erforderlichen Berechtigungen für Schlüssel und Geheimnisse definiert.
- Alle Schlüssel und Geheimnisse in Key Vault besitzen ein Ablaufdatum.
- Alle Schlüssel in Key Vault werden durch spezielle Hardwaresicherheitsmodule geschützt. Der Schlüsseltyp ist ein Hardwaresicherheitsmodul, das von einem 2.048-Bit-RSA-Schlüssel geschützt wird.
- Allen Benutzern und Identitäten werden mithilfe von rollenbasierter Zugriffssteuerung die erforderlichen Mindestberechtigungen erteilt.
- Diagnoseprotokolle für Key Vault werden mit einer Aufbewahrungsdauer von mindestens 365 Tagen aktiviert.
- Zulässige kryptographische Vorgänge für Schlüssel sind auf die erforderlichen Vorgänge beschränkt.

**Azure Security Center**: Mit [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) können Kunden Sicherheitsrichtlinien über Workloads hinweg zentral anwenden und verwalten, die Angriffsfläche verringern sowie Angriffe erkennen und darauf reagieren. Darüber hinaus greift Azure Security Center auf die vorhandenen Konfigurationen der Azure-Dienste zu, um Empfehlungen für die Konfiguration und Verwaltung zu geben und zur Verbesserung der Sicherheit und zum Schutz Daten beizutragen.

Azure Security Center verwendet eine Vielzahl von Erkennungsfunktionen, um Kunden vor potenziellen Angriffen auf ihre Umgebungen zu warnen. Diese Warnungen enthalten wichtige Informationen zum Auslöser der Warnung, zu den möglicherweise betroffenen Ressourcen und zur Quelle des Angriffs. In Azure Security Center steht eine Reihe vordefinierter [Sicherheitswarnungen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) zur Verfügung, die ausgelöst werden, wenn eine Bedrohung oder eine verdächtige Aktivität erkannt wird. Mithilfe [benutzerdefinierter Warnungsregeln](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) in Azure Security Center können Kunden neue Sicherheitswarnungen auf der Grundlage von Daten definieren, die bereits in ihrer Umgebung gesammelt werden.

Azure Security Center bietet priorisierte Sicherheitswarnungen und Incidents, sodass es für Kunden einfacher wird, potenzielle Sicherheitsprobleme zu erkennen und zu beheben. Ein [Bericht über Bedrohungen](https://docs.microsoft.com/azure/security-center/security-center-threat-report) wird zu jeder erkannten Bedrohung generiert, der den Teams, die für die Bearbeitung von Incidents zuständig sind, beim Untersuchen und Beheben von Bedrohungen als Hilfe dient.

### <a name="logging-and-auditing"></a>Protokollierung und Überwachung

Azure-Dienste protokollieren umfassend die System- und Benutzeraktivitäten sowie die Systemintegrität:
- **Aktivitätsprotokolle**: [Aktivitätsprotokolle](../../azure-monitor/platform/activity-logs-overview.md) bieten Einblicke in Vorgänge, die für Ressourcen in einem Abonnement ausgeführt wurden. Aktivitätsprotokolle können dabei helfen, den Initiator eines Vorgangs, die Zeit des Auftretens und den Status zu bestimmen.
- **Diagnoseprotokolle**: [Diagnoseprotokolle](../../azure-monitor/platform/resource-logs-overview.md) umfassen sämtliche Protokolle, die von jeder Ressource ausgegeben werden. Zu diesen Protokollen gehören Windows-Ereignissystemprotokolle, Azure Storage-Protokolle, Key Vault-Überwachungsprotokolle sowie Application Gateway-Zugriffs- und -Firewallprotokolle. Alle Diagnoseprotokolle werden für die Archivierung in ein zentrales und verschlüsseltes Azure Storage-Konto geschrieben. Die Aufbewahrung kann vom Benutzer konfiguriert werden (bis zu 730 Tage), um den unternehmensspezifischen Aufbewahrungsanforderungen gerecht zu werden.

**Azure Monitor-Protokolle:** Diese Protokolle werden dann zur Verarbeitung, Speicherung und Dashboardanzeige in [Azure Monitor-Protokolle](https://azure.microsoft.com/services/log-analytics/) konsolidiert. Nachdem die Daten gesammelt wurden, werden sie in separaten Tabellen für die einzelnen Datentypen angeordnet, damit alle Daten unabhängig von der ursprünglichen Quelle zusammen analysiert werden können. Darüber hinaus ist das Azure Security Center in Azure Monitor-Protokolle integriert, sodass Kunden mit Kusto-Abfragen auf ihre Sicherheitsereignisdaten zugreifen und diese mit Daten aus anderen Diensten kombinieren können.

In dieser Architektur sind die folgenden Azure-[Überwachungslösungen](../../monitoring/monitoring-solutions.md) enthalten:
-   [Active Directory-Bewertung](../../azure-monitor/insights/ad-assessment.md): Die Lösung „Active Directory-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt eine priorisierte Liste mit spezifischen Empfehlungen für die bereitgestellte Serverinfrastruktur.
- [SQL-Bewertung](../../azure-monitor/insights/sql-assessment.md): Die Lösung „SQL-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt für die Kunden eine priorisierte Liste mit spezifischen Empfehlungen für die bereitgestellte Serverinfrastruktur.
- [Agent-Integritätsdiagnose](../../monitoring/monitoring-solution-agenthealth.md): Die Lösung „Agent-Integritätsdiagnose“ meldet die Anzahl bereitgestellter Agents sowie deren geografische Verteilung. Außerdem meldet sie, wie viele Agents nicht mehr reagieren und wie viele Agents Betriebsdaten übermitteln.
-   [Aktivitätsprotokollanalyse](../../azure-monitor/platform/collect-activity-logs.md): Die Lösung „Aktivitätsprotokollanalyse“ hilft bei der Analyse der Azure-Aktivitätsprotokolle in allen Azure-Abonnements eines Kunden.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) dient zum Speichern, Ausführen und Verwalten von Runbooks. Bei dieser Lösung werden Protokolle über Runbooks für Azure SQL-Datenbank erfasst. Die [Änderungsnachverfolgung](../../automation/change-tracking.md) von Automation ermöglicht Kunden das Identifizieren von Änderungen in der Umgebung auf einfache Weise.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) bietet Überwachungsfunktionen für Organisationen und ermöglicht die Erstellung von Warnungen sowie die Archivierung von Daten (einschließlich Nachverfolgung von API-Aufrufen in ihren Azure-Ressourcen), um Benutzer beim Nachverfolgen der Leistung, beim Aufrechterhalten der Sicherheit und beim Identifizieren von Trends zu unterstützen.

## <a name="threat-model"></a>Bedrohungsmodell

Das Datenflussdiagramm für diese Referenzarchitektur steht zum [Download](https://aka.ms/uknhs-analytics-tm) bereit und ist unten angegeben. Dieses Modell kann Kunden dabei helfen, bei Änderungen die potenziellen Risikopunkte in der Systeminfrastruktur zu verstehen.

![Analytics für das Bedrohungsmodell gemäß NHS (Vereinigtes Königreich)](images/uknhs-analytics-threat-model.png?raw=true "Analytics für das Bedrohungsmodell gemäß NHS (Vereinigtes Königreich)")

## <a name="compliance-documentation"></a>Konformitätsdokumentation

In [Azure Security and Compliance Blueprint – NHS-Zuständigkeitsmatrix (Vereinigtes Königreich) für Kunden](https://aka.ms/uknhs-crm) sind alle von NHS geforderten Sicherheitsprinzipien aufgeführt. In dieser Matrix ist angegeben, ob die Implementierung der einzelnen Prinzipien Aufgabe von Microsoft, des Kunden oder beider Seiten ist.

[Azure Security and Compliance Blueprint – Implementierungsmatrix für Datenanalyse gemäß NHS (Vereinigtes Königreich)](https://aka.ms/uknhs-analytics-cim) enthält Informationen darüber, auf welche NHS-Anforderungen durch die Datenanalysearchitektur verwiesen wird, sowie eine ausführliche Beschreibung, wie die Implementierung die Anforderungen der einzelnen Steuerelemente erfüllt.


## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen

### <a name="vpn-and-expressroute"></a>VPN und ExpressRoute

[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) oder ein sicherer VPN-Tunnel müssen für eine sichere Verbindung mit Ressourcen, die als Teil dieser Referenzarchitektur für die Datenanalyse bereitgestellt werden, konfiguriert werden. Durch die korrekte Einrichtung von VPN oder ExpressRoute können Kunden eine zusätzliche Sicherheitsebene für Daten während der Übertragung hinzufügen.

Mit der Implementierung eines sicheren VPN-Tunnels mit Azure kann eine virtuelle private Verbindung zwischen einem lokalen Netzwerk und einem virtuellen Azure-Netzwerk hergestellt werden. Diese Verbindung wird über das Internet hergestellt und ermöglicht Kunden das Übertragen von Informationen in einem &quot;Tunnel&quot; innerhalb eines verschlüsselten Links zwischen dem Kundennetzwerk und Azure. Eine Site-to-Site-VPN-Verbindung ist eine sichere, ausgereifte Technologie, die seit Jahrzehnten von Unternehmen aller Größen eingesetzt wird. Der [IPsec-Tunnelmodus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wird bei dieser Option als Verschlüsselungsmechanismus verwendet.

Da Datenverkehr im VPN-Tunnel über ein Site-to-Site-VPN über das Internet übertragen wird, bietet Microsoft eine weitere Verbindungsoption an, die noch sicherer ist. Azure ExpressRoute ist ein dedizierter WAN-Link zwischen Azure und einem lokalen Standort oder einem Exchange-Hostinganbieter. Da ExpressRoute-Verbindungen nicht über das Internet verlaufen, bieten sie eine höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit bei geringerer Latenz als herkömmliche Verbindungen über das Internet. Da es sich außerdem um eine direkte Verbindung vom Telekommunikationsanbieter des Kunden handelt, werden die Daten nicht über das Internet übertragen und dort nicht verfügbar gemacht.

Bewährte Methoden für das Implementieren eines sicheren hybriden Netzwerks, mit dem ein lokales Netzwerk auf Azure erweitert wird, finden Sie [hier](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>ETL-Prozess (Extrahieren, Transformieren, Laden)

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kann Daten in die Azure SQL-Datenbank laden, ohne ein separates Tool für Extrahieren, Transformieren, Laden oder für den Import zu verwenden. PolyBase ermöglicht den Zugriff auf Daten über T-SQL-Abfragen. Microsoft Business Intelligence und der Analysestapel sowie mit SQL Server kompatible Drittanbietertools können mit PolyBase verwendet werden.

### <a name="azure-active-directory-setup"></a>Azure Active Directory-Einrichtungen
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) ist wichtig für die Verwaltung der Bereitstellung von Zugriff für Mitarbeiter, die mit der Umgebung interagieren. Ein bereits vorhandenes Windows Server Active Directory-Verzeichnis kann über [vier Klicks](../../active-directory/hybrid/how-to-connect-install-express.md) in Azure Active Directory integriert werden. Die Kunden können außerdem die bereitgestellte Active Directory-Infrastruktur (Domänencontroller) an ein bereits vorhandenes Azure Active Directory-Verzeichnis binden, indem sie die bereitgestellte Active Directory-Infrastruktur als untergeordnete Domäne für eine Azure Active Directory-Struktur festlegen.

## <a name="disclaimer"></a>Haftungsausschluss

 - Dieses Dokument dient nur zu Informationszwecken. MICROSOFT ÜBERNIMMT KEINE AUSDRÜCKLICHEN, STILLSCHWEIGENDEN ODER GESETZLICH VORGEGEBENEN GARANTIEN HINSICHTLICH DER INFORMATIONEN IN DIESEM DOKUMENT. Dieses Dokument wird so bereitgestellt, wie es ist. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Kunden, die dieses Dokument lesen, tragen das Risiko, es zu verwenden.
 - Dieses Dokument gibt Kunden keine Rechte an geistigem Eigentum an Microsoft-Produkten oder -Lösungen.
 - Kunden dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.
 - Bestimmte Empfehlungen in diesem Dokument können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen in Azure sowie zu höheren Azure-Lizenz- oder -Abonnementkosten für den Kunden führen.
 - Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden.
 - Dieses Dokument wurde als Referenz entwickelt und sollte nicht dazu verwendet werden, alle Mittel zu definieren, mit denen ein Kunde bestimmte Konformitätsanforderungen und -vorschriften erfüllen kann. Kunden sollten bei genehmigten Kundenimplementierungen rechtliche Unterstützung von ihrer Organisation einholen.
