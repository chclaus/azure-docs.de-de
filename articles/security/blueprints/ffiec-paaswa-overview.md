---
title: Azure Security and Compliance Blueprint – PaaS-Webanwendungen gemäß FFIEC
description: Azure Security and Compliance Blueprint – PaaS-Webanwendungen gemäß FFIEC
services: security
author: meladie
ms.assetid: a552e313-2b46-4001-b6e2-bed0b7757ac5
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: 5f6cde099c72313df9f0e9a1a7a517c195a6da12
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257210"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-ffiec-financial-services"></a>Azure-Blaupause für Sicherheit und Compliance: PaaS-Webanwendung für FFIEC-Finanzdienste

## <a name="overview"></a>Übersicht

Diese Azure Security and Compliance Blueprint-Automatisierung bietet Leitlinien für die Bereitstellung einer PaaS-Umgebung (Plattform als Service), die für die Erfassung, Speicherung und Abfrage von Finanzdaten geeignet ist, die der Regulierung des Federal Financial Institution Examination Council (FFIEC) unterliegen. Diese Lösung automatisiert die Bereitstellung und Konfiguration von Azure-Ressourcen für eine allgemeine Referenzarchitektur. Sie zeigt Wege auf, wie Kunden spezifische Sicherheits- und Konformitätsanforderungen erfüllen können und dient als Grundlage für Kunden, um ihre eigenen Lösungen in Azure zu entwickeln und zu konfigurieren. Die Lösung implementiert eine Teilmenge von Anforderungen aus den FFIEC-Handbüchern. Weitere Informationen zu den FFIEC-Anforderungen und zu dieser Lösung finden Sie im Abschnitt [Dokumentation zur Konformität](#compliance-documentation).

Diese Azure Security and Compliance Blueprint-Automatisierung stellt eine Referenzarchitektur für PaaS-Webanwendungen mit vorkonfigurierten Sicherheitsregulierungen bereit, um Kunden bei der Einhaltung der FFIEC-Anforderungen zu unterstützen. Die Lösung besteht aus Azure Resource Manager-Vorlagen und PowerShell-Skripts, die die Ressourcenbereitstellung und -konfiguration steuern.

Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden. Sollen die FFIEC-Ziele vollständig erfüllt werden, genügt es nicht, eine Anwendung ohne Änderungen in dieser Umgebung bereitzustellen. Beachten Sie Folgendes:
- Diese Architektur bietet eine Grundlage, um Kunden bei der Verwendung von Azure in FFIEC-konformer Weise zu unterstützen.
- Der Kunde ist für die Durchführung einer angemessenen Sicherheits- und Konformitätsbewertung jeder Lösung verantwortlich, die mit dieser Architektur erstellt wurde, da die Anforderungen je nach den Besonderheiten der jeweiligen Implementierung des Kunden unterschiedlich sein können.

Um die FFIEC-Konformität zu erreichen, müssen Produktionskundenlösungen von qualifizierten Prüfern zertifiziert werden. Die Prüfungen werden von Prüfern der Mitgliedsorganisationen des FFIEC überwacht, darunter der Board of Governors des Federal Reserve System (FRB), die Federal Deposit Insurance Corporation (FDIC), die National Credit Union Administration (NCUA), das Office of the Comptroller of the Currency (OCC) und das Consumer Financial Protection Bureau (CFPB). Diese Prüfer bescheinigen, dass die Prüfungen von Gutachtern durchgeführt werden, die von der geprüften Stelle unabhängig sind. Der Kunde ist für die Durchführung einer angemessenen Sicherheits- und Konformitätsbewertung jeder Lösung verantwortlich, die mit dieser Architektur erstellt wurde, da die Anforderungen je nach den Besonderheiten der jeweiligen Implementierung des Kunden unterschiedlich sein können.

Klicken Sie [hier](https://aka.ms/ffiec-paaswa-repo), um Anleitungen zur Bereitstellung zu erhalten.

## <a name="architecture-diagram-and-components"></a>Architekturdiagramm und Komponenten

Diese Azure Security and Compliance Blueprint-Automatisierung stellt eine Referenzarchitektur für eine PaaS-Webanwendung mit einem Azure SQL-Datenbank-Back-End bereit. Die Webanwendung wird in einer isolierten Azure App Service-Umgebung gehostet. Hierbei handelt es sich um eine private, dedizierte Umgebung in einem Azure-Datencenter. In der Umgebung wird ein Lastenausgleich für den Datenverkehr der Webanwendung für die virtuellen Computer durchgeführt, die von Azure verwaltet werden. Diese Architektur umfasst auch Netzwerksicherheitsgruppen, ein Application Gateway, Azure DNS und einen Load Balancer.

Für erweiterte Analysen und Berichterstattungen können Azure SQL-Datenbanken mit Columnstore-Indizes konfiguriert werden. Azure SQL-Datenbanken können abhängig von der Nutzung durch den Kunden zentral hoch- bzw. herunterskaliert oder vollständig ausgeschaltet werden. Jeglicher SQL-Datenverkehr wird mit SSL über selbstsignierte Zertifikate verschlüsselt. Azure empfiehlt als bewährte Methode die Verwendung einer vertrauenswürdigen Zertifizierungsstelle für erweiterte Sicherheit.

Die Lösung verwendet Azure Storage-Konten, die Kunden für die Verwendung von Speicherdienstverschlüsselung konfigurieren können, um die Vertraulichkeit ruhender Daten zu wahren. Azure speichert drei Kopien der Daten im ausgewählten Rechenzentrum eines Kunden, um Resilienz zu gewährleisten. Die geografisch redundante Speicherung stellt sicher, dass die Daten in ein Hunderte von Kilometern entferntes sekundäres Rechenzentrum repliziert und dort wieder als drei Kopien gespeichert werden, um zu verhindern, dass ein unerwünschtes Ereignis im primären Rechenzentrum des Kunden zu einem Datenverlust führt.

Für mehr Sicherheit werden alle Ressourcen in dieser Lösung als Ressourcengruppe über den Azure Resource Manager verwaltet. Die rollenbasierte Zugriffskontrolle von Azure Active Directory dient zur Kontrolle des Zugriffs auf die bereitgestellten Ressourcen, einschließlich ihrer Schlüssel im Azure Key Vault. Die Systemintegrität wird durch Azure Monitor überwacht. Der Kunde konfiguriert das Überwachen von Diensten zum Speichern von Protokollen und Anzeigen der Systemintegration in nur einem Dashboard, durch das man leicht navigieren kann.

Azure SQL-Datenbank wird in der Regel über SQL Server Management Studio (SSMS) verwaltet. SSMS wird über einen lokalen Computer ausgeführt, der für den Zugriff auf Azure SQL-Datenbank über eine sichere VPN- oder ExpressRoute-Verbindung konfiguriert ist.

Darüber hinaus ermöglicht Application Insights über Azure Monitor-Protokolle die Echtzeit-Anwendungsleistungsverwaltung und entsprechende Analysen. **Microsoft empfiehlt das Konfigurieren einer VPN- oder ExpressRoute-Verbindung für die Verwaltung und den Datenimport in das Subnetz der Referenzarchitektur.**

![PaaS-Webanwendung für FFIEC – Diagramm zur Referenzarchitektur](images/ffiec-paaswa-architecture.png "PaaS-Webanwendung für FFIEC – Diagramm zur Referenzarchitektur")

Diese Lösung verwendet die folgenden Azure-Dienste. Details zur Bereitstellungsarchitektur finden Sie im Abschnitt [Bereitstellungsarchitektur](#deployment-architecture).

- Application Gateway
    - Web Application Firewall
        - Firewallmodus: Prävention
        - Regelsatz: OWASP
        - Listenerport: 443
- Application Insights
- Azure Active Directory
- Azure Application Service-Umgebung v2
- Azure-Automatisierung
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (Protokolle)
- Azure Resource Manager
- Azure Security Center
- Azure SQL-Datenbank
- Azure Storage
- Virtuelles Azure-Netzwerk
    - (1) /16 Netzwerk
    - (4) /24 Netzwerke
    - Netzwerksicherheitsgruppen
- Azure App Service

## <a name="deployment-architecture"></a>Bereitstellungsarchitektur

Im folgenden Abschnitt werden die Elemente für Entwicklung und Implementierung beschrieben.

**Azure Resource Manager**: Mit [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) können Kunden mit den Ressourcen der Lösung als Gruppe arbeiten. Kunden können alle Ressourcen für die Lösung in einem einzigen koordinierten Vorgang bereitstellen, aktualisieren oder löschen. Kunden verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z.B. Testing, Staging und Produktion. Resource Manager umfasst Sicherheits-, Überwachungs- und Kennzeichnungsfunktionen, mit denen Kunden ihre Ressourcen nach der Bereitstellung verwalten können.

**Bastionhost**: Der Bastionhost ist der einzelne Zugangspunkt, über den Benutzer Zugriff auf die bereitgestellten Ressourcen in dieser Umgebung erhalten. Der Bastionhost bietet eine sichere Verbindung mit den bereitgestellten Ressourcen, indem nur Remotedatenverkehr von öffentlichen IP-Adressen auf einer Sicherheitsliste zugelassen wird. Damit RDP-Datenverkehr (Remotedesktopprotokoll) zugelassen wird, muss die Quelle des Datenverkehrs in der Netzwerksicherheitsgruppe definiert sein.

Diese Lösung erstellt einen virtuellen Computer als Bastionhost in der Domäne mit den folgenden Konfigurationen:
-   [Antimalware-Erweiterung](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure-Diagnoseerweiterung](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) mit Azure Key Vault
-   Eine [Richtlinie zum automatischen Herunterfahren](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/), um die Nutzung der Ressourcen virtueller Computer zu verringern, wenn sie nicht benötigt werden
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) ist aktiviert, damit Anmeldeinformationen und andere Geheimnisse in einer geschützten Umgebung, die vom ausgeführten Betriebssystem isoliert ist, ausgeführt werden.

**App Service-Umgebung v2**: Die Azure App Service-Umgebung ist ein App Service-Feature, das eine vollständig isolierte und dedizierte Umgebung zur sicheren Ausführung von App Service-Anwendungen in großem Umfang bereitstellt. Dieses Isolationsfeature ist erforderlich, um die FFIEC-Complianceanforderungen zu erfüllen.

Aufgrund der Isolierung werden in App Service-Umgebungen nur Anwendungen eines einzelnen Kunden ausgeführt. Die Umgebungen werden zudem immer in einem virtuellen Netzwerk bereitgestellt. Dieses Isolationsfeature ermöglicht der Referenzarchitektur eine vollständige Mandantenisolierung und entfernt sie aus Azure-Umgebungen mit mehreren Mandanten, sodass diese Mandanten nicht mehr in der Lage sind, die bereitgestellten Ressourcen der App Service-Umgebung aufzulisten. Kunden haben eine genauere Kontrolle über den eingehenden und ausgehenden Anwendungs-Netzwerkdatenverkehr, und Anwendungen können über virtuelle Netzwerke sichere Hochgeschwindigkeitsverbindungen mit lokalen Unternehmensressourcen herstellen. Kunden können mit der App Service-Umgebung auf Basis von Lastmetriken, verfügbarem Budget oder einem definierten Zeitplan "automatisch skalieren".

Die Verwendung der App Service-Umgebung für diese Architektur ist für die folgenden Konfigurationen zulässig:

- Hosten in einem geschützten virtuellen Azure-Netzwerk und in Netzwerksicherheitsregeln
- Selbstsigniertes Zertifikat des internen Lastenausgleichsmoduls für die HTTPS-Kommunikation. Microsoft empfiehlt als bewährte Methode die Verwendung einer vertrauenswürdigen Zertifizierungsstelle für erweiterte Sicherheit.
- [Interner Lastenausgleichsmodus](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- Deaktivieren von [TLS 1.0](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Ändern von [TLS-Verschlüsselung](../../app-service/environment/app-service-app-service-environment-custom-settings.md)
- Steuern der [N/W-Port für eingehenden Datenverkehr](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)
- [Web Application Firewall – Daten einschränken](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- Zulassen von [Azure SQL-Datenbank-Datenverkehr](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)

**Azure App Service**: [Azure App Service](https://docs.microsoft.com/azure/app-service/) ermöglicht Kunden das Erstellen und Hosten von Webanwendungen in der Programmiersprache ihrer Wahl, ohne eine Infrastruktur verwalten zu müssen. Der Dienst bietet automatische Skalierung und Hochverfügbarkeit, unterstützt Windows und Linux und ermöglicht automatisierte Bereitstellungen über GitHub, Azure DevOps oder ein anderes beliebiges Git-Repository.

### <a name="virtual-network"></a>Virtual Network

Die Architektur definiert ein privates virtuelles Netzwerk mit dem Adressraum 10.200.0.0/16.

**Netzwerksicherheitsgruppen**: [Netzwerksicherheitsgruppen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) enthalten Zugriffssteuerungslisten, die den Datenverkehr in einem virtuellen Netzwerk zulassen oder ablehnen. Netzwerksicherheitsgruppen können verwendet werden, um Datenverkehr auf der Ebene eines Subnetzes oder eines einzelnen Computers zu schützen. Es gibt die folgenden Netzwerksicherheitsgruppen:

- 1 Netzwerksicherheitsgruppe für Application Gateway
- 1 Netzwerksicherheitsgruppe für die App Service-Umgebung
- 1 Netzwerksicherheitsgruppe für die Azure SQL-Datenbank
- 1 Netzwerksicherheitsgruppe für den Bastionhost

Jede der Netzwerksicherheitsgruppen verfügt über bestimmte offene Ports und Protokolle, damit die Lösung sicher und richtig ausgeführt werden kann. Darüber hinaus werden die folgenden Konfigurationen für jede Netzwerksicherheitsgruppe aktiviert:

- [Diagnoseprotokolle und -ereignisse](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) sind aktiviert und werden im Speicherkonto gespeichert.
- Azure Monitor-Protokolle ist mit den [Diagnoseprotokollen der Netzwerksicherheitsgruppe](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json) verbunden.

**Subnetze**: Jedes Subnetz ist seiner entsprechenden Netzwerksicherheitsgruppe zugeordnet.

**Azure DNS**: Das Domain Name System (DNS) ist für die Übersetzung (oder Auflösung) eines Website- oder Dienstnamens in die IP-Adresse verantwortlich. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) ist ein Hostingdienst für DNS-Domänen, der die Namensauflösung mithilfe der Azure-Infrastruktur durchführt. Durch das Hosten von Domänen in Azure können Benutzer DNS-Einträge mithilfe der gleichen Anmeldeinformationen, APIs, Tools und Abrechnungsschritte wie für die anderen Azure-Dienste verwalten. Azure DNS unterstützt auch private DNS-Domänen.

**Azure Load Balancer**: Mit [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) können Kunden ihre Anwendungen skalieren und Hochverfügbarkeit für Dienste erzielen. Load Balancer unterstützt sowohl Szenarien mit eingehenden als auch ausgehenden Verbindungen, bietet niedrige Latenzen und einen hohen Durchsatz und unterstützt Millionen Datenflüsse für alle TCP- und UDP-Anwendungen.

### <a name="data-in-transit"></a>Daten während der Übertragung

Azure verschlüsselt standardmäßig die gesamte Kommunikation zu und von Azure-Rechenzentren. Alle Transaktionen für Azure Storage über das Azure-Portal werden per HTTPS durchgeführt.

### <a name="data-at-rest"></a>Ruhende Daten

Die Architektur schützt ruhende Daten durch die Verwendung von Verschlüsselung, Datenbanküberwachung und anderen Maßnahmen.

**Azure Storage**: Um die Anforderungen für verschlüsselte ruhende Daten zu erfüllen, wird überall in [Azure Storage](https://azure.microsoft.com/services/storage/) die [Speicherdienstverschlüsselung](../../storage/common/storage-service-encryption.md) verwendet. Dadurch werden Daten geschützt, um die Zusagen und Anforderungen der Organisation im Hinblick auf Sicherheit und Konformität gemäß der FFIEC einzuhalten.

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
- Alle Schlüssel in Key Vault werden durch spezielle Hardwaresicherheitsmodule geschützt. Der Schlüsseltyp ist ein HSM-geschützter 2.048-Bit-RSA-Schlüssel.
- Allen Benutzern und Identitäten werden mithilfe von rollenbasierter Zugriffssteuerung die erforderlichen Mindestberechtigungen erteilt.
- Diagnoseprotokolle für Key Vault werden mit einer Aufbewahrungsdauer von mindestens 365 Tagen aktiviert.
- Zulässige kryptographische Vorgänge für Schlüssel sind auf die erforderlichen Vorgänge beschränkt.

**Azure Security Center**: Mit [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) können Kunden Sicherheitsrichtlinien über Workloads hinweg zentral anwenden und verwalten, die Angriffsfläche verringern sowie Angriffe erkennen und darauf reagieren. Darüber hinaus greift Azure Security Center auf die vorhandenen Konfigurationen der Azure-Dienste zu, um Empfehlungen für die Konfiguration und Verwaltung zu geben und zur Verbesserung der Sicherheit und zum Schutz Daten beizutragen.

Azure Security Center verwendet eine Vielzahl von Erkennungsfunktionen, um Kunden vor potenziellen Angriffen auf ihre Umgebungen zu warnen. Diese Warnungen enthalten wichtige Informationen zum Auslöser der Warnung, zu den möglicherweise betroffenen Ressourcen und zur Quelle des Angriffs. In Azure Security Center steht eine Reihe vordefinierter [Sicherheitswarnungen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) zur Verfügung, die ausgelöst werden, wenn eine Bedrohung oder eine verdächtige Aktivität erkannt wird. Mithilfe [benutzerdefinierter Warnungsregeln](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) in Azure Security Center können Kunden neue Sicherheitswarnungen auf der Grundlage von Daten definieren, die bereits in ihrer Umgebung gesammelt werden.

Azure Security Center bietet priorisierte Sicherheitswarnungen und Incidents, sodass es für Kunden einfacher wird, potenzielle Sicherheitsprobleme zu erkennen und zu beheben. Ein [Bericht über Bedrohungen](https://docs.microsoft.com/azure/security-center/security-center-threat-report) wird zu jeder erkannten Bedrohung generiert, der den Teams, die für die Bearbeitung von Incidents zuständig sind, beim Untersuchen und Beheben von Bedrohungen als Hilfe dient.

**Azure Application Gateway**: Die Architektur verringert die Gefahr von Sicherheitsrisiken durch ein Azure Application Gateway mit einer konfigurierten Web Application Firewall und aktiviertem OWASP-Regelsatz. Weitere Funktionen:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- Aktivierte [SSL-Auslagerung](../../application-gateway/create-ssl-portal.md)
- Deaktivieren von [TLS v1.0 und v1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [Web Application Firewall](../../application-gateway/waf-overview.md) (Schutzmodus)
- [Präventionsmodus](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) mit OWASP 3.0-Regelsatz
- Aktivieren der [Diagnoseprotokollierung](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)
- [Benutzerdefinierte Integritätstests](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) und [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) bieten zusätzlichen Schutz und zusätzliche Benachrichtigungen. Azure Security Center stellt außerdem ein Reputationssystem bereit.

### <a name="logging-and-auditing"></a>Protokollierung und Überwachung

Azure-Dienste protokollieren umfassend die System- und Benutzeraktivitäten sowie die Systemintegrität:
- **Aktivitätsprotokolle**: [Aktivitätsprotokolle](../../azure-monitor/platform/activity-logs-overview.md) bieten Einblicke in Vorgänge, die für Ressourcen in einem Abonnement ausgeführt wurden. Aktivitätsprotokolle können dabei helfen, den Initiator eines Vorgangs, die Zeit des Auftretens und den Status zu bestimmen.
- **Diagnoseprotokolle**: [Diagnoseprotokolle](../../azure-monitor/platform/resource-logs-overview.md) umfassen sämtliche Protokolle, die von jeder Ressource ausgegeben werden. Zu diesen Protokollen gehören Windows-Ereignissystemprotokolle, Azure Storage-Protokolle, Key Vault-Überwachungsprotokolle sowie Application Gateway-Zugriffs- und -Firewallprotokolle. Alle Diagnoseprotokolle werden für die Archivierung in ein zentrales und verschlüsseltes Azure Storage-Konto geschrieben. Die Aufbewahrung kann vom Benutzer konfiguriert werden (bis zu 730 Tage), um den unternehmensspezifischen Aufbewahrungsanforderungen gerecht zu werden.

**Azure Monitor-Protokolle:** Diese Protokolle werden dann zur Verarbeitung, Speicherung und Dashboardanzeige in [Azure Monitor-Protokolle](https://azure.microsoft.com/services/log-analytics/) konsolidiert. Nachdem die Daten gesammelt wurden, werden sie in separaten Tabellen für die einzelnen Datentypen in Log Analytics-Arbeitsbereichen angeordnet, damit alle Daten unabhängig von der ursprünglichen Quelle zusammen analysiert werden können. Darüber hinaus ist das Azure Security Center in Azure Monitor-Protokolle integriert, sodass Kunden mit Kusto-Abfragen auf ihre Sicherheitsereignisdaten zugreifen und diese mit Daten aus anderen Diensten kombinieren können.

In dieser Architektur sind die folgenden Azure-[Überwachungslösungen](../../monitoring/monitoring-solutions.md) enthalten:
-   [Active Directory-Bewertung](../../azure-monitor/insights/ad-assessment.md): Die Lösung „Active Directory-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt eine priorisierte Liste mit spezifischen Empfehlungen für die bereitgestellte Serverinfrastruktur.
- [SQL-Bewertung](../../azure-monitor/insights/sql-assessment.md): Die Lösung „SQL-Integritätsüberprüfung“ bewertet die Risiken und die Integrität von Serverumgebungen in regelmäßigen Abständen und erstellt für die Kunden eine priorisierte Liste mit spezifischen Empfehlungen für die bereitgestellte Serverinfrastruktur.
- [Agent-Integritätsdiagnose](../../monitoring/monitoring-solution-agenthealth.md): Die Lösung „Agent-Integritätsdiagnose“ meldet die Anzahl bereitgestellter Agents sowie deren geografische Verteilung. Außerdem meldet sie, wie viele Agents nicht mehr reagieren und wie viele Agents Betriebsdaten übermitteln.
-   [Aktivitätsprotokollanalyse](../../azure-monitor/platform/collect-activity-logs.md): Die Lösung „Aktivitätsprotokollanalyse“ hilft bei der Analyse der Azure-Aktivitätsprotokolle in allen Azure-Abonnements eines Kunden.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) dient zum Speichern, Ausführen und Verwalten von Runbooks. Bei dieser Lösung werden Protokolle über Runbooks für Azure SQL-Datenbank erfasst. Die [Änderungsnachverfolgung](../../automation/change-tracking.md) von Automation ermöglicht Kunden das Identifizieren von Änderungen in der Umgebung auf einfache Weise.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) bietet Überwachungsfunktionen für Organisationen und ermöglicht die Erstellung von Warnungen sowie die Archivierung von Daten (einschließlich Nachverfolgung von API-Aufrufen in ihren Azure-Ressourcen), um Benutzer beim Nachverfolgen der Leistung, beim Aufrechterhalten der Sicherheit und beim Identifizieren von Trends zu unterstützen.

**Application Insights**: [Application Insights](../../azure-monitor/app/app-insights-overview.md) ist ein erweiterbarer, für Webentwickler konzipierter Dienst zur Verwaltung der Anwendungsleistung auf mehreren Plattformen. Mit Application Insights werden Leistungsanomalien erkannt, und Kunden können die Lösung für die Liveüberwachung der Webanwendung nutzen. Sie verfügt über leistungsstarke Analysetools, mit denen Kunden Probleme diagnostizieren und nachvollziehen können, wie ihre App von den Benutzern verwendet wird. Der Dienst unterstützt Kunden bei der kontinuierlichen Verbesserung der Leistung und Benutzerfreundlichkeit.

## <a name="threat-model"></a>Bedrohungsmodell

Das Datenflussdiagramm für diese Referenzarchitektur steht zum [Download](https://aka.ms/ffiec-paaswa-tm) bereit und ist unten angegeben. Dieses Modell kann Kunden dabei helfen, bei Änderungen die potenziellen Risikopunkte in der Systeminfrastruktur zu verstehen.

![PaaS-Webanwendung für das FFIEC-Bedrohungsmodell](images/ffiec-paaswa-threat-model.png "PaaS-Webanwendung für das FFIEC-Bedrohungsmodell")

## <a name="compliance-documentation"></a>Konformitätsdokumentation

In [Azure Security and Compliance Blueprint – FFIEC-Zuständigkeitsmatrix für Kunden](https://aka.ms/ffiec-crm) sind alle von FFIEC geforderten Sicherheitsziele aufgeführt. In dieser Matrix ist angegeben, ob die Implementierung der einzelnen Ziele Aufgabe von Microsoft, des Kunden oder beider Seiten ist.

[Azure Security and Compliance Blueprint – Implementierungsmatrix für FFIEC-konforme PaaS-Webanwendungen](https://aka.ms/ffiec-paaswa-cim) enthält Informationen darüber, auf welche FFIEC-Anforderungen durch die Architektur der PaaS-Webanwendung verwiesen wird, sowie eine detaillierte Beschreibung, wie die Implementierung die Anforderungen der einzelnen Ziele erfüllt.

## <a name="deploy-this-solution"></a>Stellen Sie diese Lösung bereit
Diese Azure Security and Compliance Blueprint-Automatisierung besteht aus JSON-Konfigurationsdateien und PowerShell-Skripts, die vom API-Dienst von Azure Resource Manager verwaltet werden, um Ressourcen innerhalb von Azure bereitzustellen. Ausführlichere Bereitstellungsanweisungen finden Sie [hier](https://aka.ms/ffiec-paaswa-repo).

#### <a name="quickstart"></a>Schnellstart
1. Klonen oder laden Sie [dieses](https://aka.ms/ffiec-paaswa-repo) GitHub-Repository auf die lokale Arbeitsstation herunter.

2. Überprüfen Sie „0-Setup-AdministrativeAccountAndPermission.md“, und führen Sie die angegebenen Befehle aus.

3. Stellen Sie eine Testlösung mit Contoso-Beispieldaten oder eine Pilotlösung einer anfänglichen Produktionsumgebung bereit.
    - 1A-ContosoWebStoreDemoAzureResources.ps1
        - Dieses Skript stellt die Azure-Ressourcen für die Demonstration eines Webstores mithilfe von Contoso-Beispieldaten bereit.
    - 1-DeployAndConfigureAzureResources.ps1
        - Dieses Skript stellt die Azure-Ressourcen bereit, die zur Unterstützung einer Produktionsumgebung für eine vom Kunden betriebene Webanwendung erforderlich sind. Diese Umgebung sollte vom Kunden auf Basis der organisatorischen Anforderungen weiter angepasst werden.

## <a name="guidance-and-recommendations"></a>Anleitungen und Empfehlungen

### <a name="vpn-and-expressroute"></a>VPN und ExpressRoute

Zur Erzielung von sicheren Verbindungen mit Ressourcen, die als Teil dieser Referenzarchitektur einer PaaS-Webanwendung bereitgestellt werden, muss ein sicherer VPN-Tunnel oder eine [ExpressRoute-Verbindung](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) konfiguriert werden. Durch die korrekte Einrichtung von VPN oder ExpressRoute können Kunden eine zusätzliche Sicherheitsebene für Daten während der Übertragung hinzufügen.

Mit der Implementierung eines sicheren VPN-Tunnels mit Azure kann eine virtuelle private Verbindung zwischen einem lokalen Netzwerk und einem virtuellen Azure-Netzwerk hergestellt werden. Diese Verbindung wird über das Internet hergestellt und ermöglicht Kunden das Übertragen von Informationen in einem &quot;Tunnel&quot; innerhalb eines verschlüsselten Links zwischen dem Kundennetzwerk und Azure. Eine Site-to-Site-VPN-Verbindung ist eine sichere, ausgereifte Technologie, die seit Jahrzehnten von Unternehmen aller Größen eingesetzt wird. Der [IPsec-Tunnelmodus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wird bei dieser Option als Verschlüsselungsmechanismus verwendet.

Da Datenverkehr im VPN-Tunnel über ein Site-to-Site-VPN über das Internet übertragen wird, bietet Microsoft eine weitere Verbindungsoption an, die noch sicherer ist. Azure ExpressRoute ist ein dedizierter WAN-Link zwischen Azure und einem lokalen Standort oder einem Exchange-Hostinganbieter. Da ExpressRoute-Verbindungen nicht über das Internet verlaufen, bieten sie eine höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit bei geringerer Latenz als herkömmliche Verbindungen über das Internet. Da es sich außerdem um eine direkte Verbindung vom Telekommunikationsanbieter des Kunden handelt, werden die Daten nicht über das Internet übertragen und dort nicht verfügbar gemacht.

Bewährte Methoden für das Implementieren eines sicheren hybriden Netzwerks, mit dem ein lokales Netzwerk auf Azure erweitert wird, finden Sie [hier](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Haftungsausschluss

- Dieses Dokument dient nur zu Informationszwecken. MICROSOFT ÜBERNIMMT KEINE AUSDRÜCKLICHEN, STILLSCHWEIGENDEN ODER GESETZLICH VORGEGEBENEN GARANTIEN HINSICHTLICH DER INFORMATIONEN IN DIESEM DOKUMENT. Dieses Dokument wird so bereitgestellt, &quot;wie es ist&quot;. Informationen und Stellungnahmen in diesem Dokument, einschließlich URLs und anderer Verweise auf Internetwebsites, können ohne vorherige Ankündigung geändert werden. Kunden, die dieses Dokument lesen, tragen das Risiko, es zu verwenden.
- Dieses Dokument gibt Kunden keine Rechte an geistigem Eigentum an Microsoft-Produkten oder -Lösungen.
- Kunden dürfen dieses Dokument zu internen Referenzzwecken kopieren und verwenden.
- Bestimmte Empfehlungen in diesem Dokument können zu einer erhöhten Nutzung von Daten, Netzwerken oder Computeressourcen in Azure sowie zu höheren Azure-Lizenz- oder -Abonnementkosten für den Kunden führen.
- Diese Architektur soll Kunden als Grundlage für die Anpassung an ihre spezifischen Anforderungen dienen. Sie sollte nicht unverändert in einer Produktionsumgebung verwendet werden.
- Dieses Dokument wurde als Referenz entwickelt und sollte nicht dazu verwendet werden, alle Mittel zu definieren, mit denen ein Kunde bestimmte Konformitätsanforderungen und -vorschriften erfüllen kann. Kunden sollten bei genehmigten Kundenimplementierungen rechtliche Unterstützung von ihrer Organisation einholen.
