---
title: Bereitstellungspläne – Azure Active Directory | Microsoft-Dokumentation
description: Umfassende Anleitungen für die Bereitstellung vieler Azure Active Directory-Funktionen
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7c851c2d865a7b553be2cd0f619ad09985115a3
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336876"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory-Bereitstellungspläne
Sie suchen nach umfassenden Anleitungen für die Bereitstellung von Azure Active Directory-Funktionen (Azure AD)? In Azure AD-Bereitstellungsplänen werden der Geschäftswert, Planungsüberlegungen und Betriebsverfahren erläutert, die Sie zur erfolgreichen Bereitstellung gängiger Azure AD-Funktionen benötigen.

Verwenden Sie auf einer der Planseiten die Funktion Ihres Browsers zum Drucken als PDF-Datei, um eine aktuelle Offlineversion der Dokumentation zu erstellen.
## <a name="include-the-right-stakeholders"></a>Einbeziehen der richtigen Projektbeteiligten

Wenn Sie beginnen, eine neue Funktion für Ihre Bereitstellung zu planen, ist es wichtig, die wichtigsten Projektbeteiligten in Ihrer Organisation einzubeziehen. Wir empfehlen, die Person bzw. Personen zu identifizieren, die jede der folgenden Rollen erfüllen, und in Zusammenarbeit mit ihnen deren Beteiligung am Projekt zu bestimmen.  

Dazu zählen u.a. die folgenden Rollen: 

|Role |BESCHREIBUNG |
|-|-|
|Endbenutzer|Eine repräsentative Gruppe von Benutzern, für welche die Funktion implementiert wird. Erkundet häufig vorher die Änderungen in einem Pilotprogramm.
|IT-Supportmanager|Ein Vertreter der IT-Supportorganisation, der zu den Unterstützungsmöglichkeiten dieser Änderung aus einer Helpdesk-Perspektive Stellung nehmen kann.  
|Identitätsarchitekt oder globaler Azure-Administrator|Vertreter des Identitätsverwaltungsteams, der definieren muss, wie diese Änderung an die Kerninfrastruktur für die Identitätsverwaltung in Ihrer Organisation gekoppelt ist.|
|Geschäftlicher Anwendungsbesitzer |Der allgemeine geschäftliche Besitzer der betroffenen Anwendung(en), ggf. mit Zugriffsverwaltung.  Kann auch Aussagen zur Benutzerfreundlichkeit und Zweckmäßigkeit dieser Änderung aus Sicht eines Endbenutzers machen.
|Sicherheitsbesitzer|Ein Vertreter des Sicherheitsteams, der abzeichnen kann, dass der Plan die Sicherheitsanforderungen Ihrer Organisation erfüllt.|
|Compliance-Manager|Die Person in Ihrem Unternehmen, die für die Sicherstellung der Konformität mit Unternehmens-, Branchen- oder behördlichen Anforderungen verantwortlich ist.|

**Die Ebenen der Beteiligung und Verantwortlichkeiten können wie folgt definiert werden:**

- **R**esponsible – Verantwortlich für die Implementierung von Projektplan und Ergebnis 

- **A**ccountable – Rechenschaftspflichtig. Genehmigt Projektplan und Ergebnis 

- **C**onsulted – Konsultiert. Wird zu Projektplan und Ergebnis befragt 

- **I**nformed – Zu informieren. Wird über Projektplan und Ergebnis informiert


## <a name="best-practices-for-a-pilot"></a>Bewährte Methoden für einen Pilotversuch
Bei einem Pilotversuch können Sie eine Funktion mit einer kleinen Gruppe testen, bevor Sie die Funktion für alle Benutzer aktivieren. Sorgen Sie im Rahmen Ihrer Tests dafür, dass die einzelnen Anwendungsfälle in Ihrer Organisation sorgfältig getestet werden. Es empfiehlt sich, eine bestimmte Gruppe von Pilotbenutzern als Zielgruppe auszuwählen, bevor Sie die Funktion in der gesamten Organisation einführen.

Zielen Sie in der ersten Phase auf IT und Benutzerfreundlichkeit ab, und wählen Sie zum Testen andere geeignete Benutzer aus, die Feedback bereitstellen können. Anhand dieses Feedbacks sollten Sie die Kommunikation mit Ihren Benutzern und die entsprechenden Anweisungen weiterentwickeln. Außerdem erhalten Sie Erkenntnisse über die Art von Problemen, mit denen Ihr Supportpersonal möglicherweise konfrontiert ist. 

Die Erweiterung des Rollouts auf größere Benutzergruppen sollte durch eine Ausweitung der Zielgruppen vorgenommen werden. Dies kann über eine [dynamische Gruppenmitgliedschaft](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) oder durch manuelles Hinzufügen von Benutzern zu den Zielgruppen erfolgen.


## <a name="deploy-authentication"></a>Bereitstellen der Authentifizierung

| Funktion | BESCHREIBUNG|
| -| -|
| [Multi-Factor Authentication](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-getstarted)| Azure Multi-Factor Authentication (MFA) ist die Lösung von Microsoft für die zweistufige Überprüfung. Mit vom Administrator genehmigten Authentifizierungsmethoden trägt Azure MFA zum Schutz des Zugriffs auf Ihre Daten und Anwendungen bei, während gleichzeitig das Ziel eines einfachen Anmeldevorgangs erreicht wird. |
| [Bedingter Zugriff](https://aka.ms/deploymentplans/ca)| Mit dem bedingten Zugriff können Sie basierend auf bestimmten Bedingungen automatisierte Entscheidungen hinsichtlich der Zugriffssteuerung für den Zugriff auf Ihre Cloud-Apps implementieren. |
| [Self-Service-Kennwortzurücksetzung](https://aka.ms/deploymentplans/sspr)| Mit der Self-Service-Kennwortzurücksetzung können Ihre Benutzer jederzeit ihr Kennwort zurücksetzen, ohne dass ein Administrator eingreifen muss. |

## <a name="deploy-application-management"></a>Bereitstellen der Anwendungsverwaltung

| Funktion | BESCHREIBUNG|
| -| - |
| [Einmaliges Anmelden](https://aka.ms/deploymentplans/sso)| Einmaliges Anmelden ermöglicht Ihren Benutzern mit nur einer Anmeldung den Zugriff auf sämtliche Apps und Ressourcen, die sie für ihre Geschäftsaktivitäten benötigen. Nachdem sie sich angemeldet haben, können sie von Microsoft Office zu Salesforce, zu Box und zu internen Anwendungen wechseln, ohne ihre Anmeldeinformationen ein zweites Mal eingeben zu müssen. |
| [Zugriffsbereich](https://aka.ms/deploymentplans/accesspanel)| Stellen Sie für Ihre Benutzer einen einfachen zentralen Ort zur Verfügung, über den diese alle Anwendungen ermitteln und darauf zugreifen können. Steigern Sie ihre Produktivität mit Self-Service-Funktionen wie dem Anfordern des Zugriffs auf Apps und Gruppen oder dem Verwalten des Zugriffs auf Ressourcen im Namen von anderen Personen. |


## <a name="deploy-hybrid-scenarios"></a>Bereitstellen von Hybridszenarien

| Funktion | BESCHREIBUNG|
| -| -|
| [Verteiltes Azure-Dateisystem (ADFS) für Kennworthashsynchronisierung](https://aka.ms/deploymentplans/adfs2phs)| Mit der Kennworthashsynchronisierung werden Hashes von Benutzerkennwörtern aus dem lokalen Active Directory mit Azure AD synchronisiert, sodass Azure AD Benutzer ohne Interaktion mit dem lokalen Active Directory authentifizieren kann. |
| [Verteiltes Azure-Dateisystem (ADFS) für Passthrough-Authentifizierung](https://aka.ms/deploymentplans/adfs2pta)| Mit der Azure AD-Pass-Through-Authentifizierung können sich Benutzer mit denselben Kennwörtern sowohl bei lokalen als auch bei cloudbasierten Anwendungen anmelden. Diese Funktion stellt den Benutzern eine benutzerfreundlichere Oberfläche zur Verfügung, da ein Kennwort wegfällt, und sie reduziert die Kosten des IT-Helpdesks, da Benutzer seltener vergessen, wie sie sich anmelden. Wenn Benutzer sich mithilfe von Azure AD anmelden, überprüft dieses Feature die Benutzerkennwörter direkt anhand Ihres lokalen Active Directory. |
| [Azure AD-Anwendungsproxy](https://aka.ms/deploymentplans/appproxy)| Heutzutage möchten Mitarbeiter an jedem Ort, zu jeder Zeit und mit jedem Gerät produktiv arbeiten können. Sie müssen auf SaaS-Apps in der Cloud und lokal auf Unternehmensanwendungen zugreifen. Der Azure AD-Anwendungsproxy ermöglicht diesen stabilen Zugriff ohne kostspielige und komplexe virtuelle private Netzwerke (VPNs) oder demilitarisierte Zonen (DMZs). |
| [Nahtloses einmaliges Anmelden](https://aka.ms/SeamlessSSODPDownload)| Die nahtlose einmalige Anmeldung mit Azure Active Directory (nahtlose SSO mit Azure AD) meldet Benutzer automatisch auf ihren Unternehmensgeräten an, die mit dem Unternehmensnetzwerk verbunden sind. Mit diesem Feature müssen Benutzer zur Anmeldung bei Azure AD kein Kennwort und in der Regel nicht einmal einen Benutzernamen eingeben. Dieses Feature ermöglicht autorisierten Benutzern einen einfachen Zugriff auf Ihre cloudbasierten Anwendungen, ohne dass zusätzliche lokale Komponenten erforderlich sind. |

## <a name="deploy-user-provisioning"></a>Bereitstellen der Benutzerbereitstellung

| Funktion | BESCHREIBUNG|
| -| -|
| [Benutzerbereitstellung](https://aka.ms/UserProvisioningDPDownload)| Mit Azure AD können Sie die Erstellung, Wartung und Entfernung von Benutzeridentitäten in Cloudanwendungen (SaaS) wie Dropbox, Salesforce, ServiceNow usw. automatisieren. |
| [Workday-driven Inbound User Provisioning](https://aka.ms/WorkdayDeploymentPlan) (Workday-gesteuerte eingehende Benutzerbereitstellung)| Bei der Workday-gesteuerten eingehenden Benutzerbereitstellung für Active Directory wird die Grundlage für eine fortlaufende Identitätsgovernance geschaffen und die Qualität von Geschäftsprozessen verbessert, die auf autoritativen Identitätsdaten basieren. Mit diesem Feature können Sie den Identitätslebenszyklus von Mitarbeitern und Zeitarbeitern nahtlos verwalten, indem Sie Regeln konfigurieren, mit denen Mitarbeiterprozesse (z. B. Neueinstellung, Kündigung, Wechsel) IT-Bereitstellungsaktionen (z. B. Erstellen, Aktivieren, Deaktivieren) zugeordnet werden. |

## <a name="deploy-governance-and-reporting"></a>Bereitstellen von Governance und Berichterstellung

| Funktion | BESCHREIBUNG|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PIM) dient zur Verwaltung von privilegierten Administratorrollen in Azure AD, Azure-Ressourcen und anderen Microsoft-Onlinediensten. PIM bietet Lösungen wie Just-In-Time-Zugriff, Workflows für die Anforderungsgenehmigung und vollständig integrierte Zugriffsüberprüfungen, damit Sie schädliche Aktivitäten von privilegierten Rollen in Echtzeit identifizieren, aufdecken und verhindern können. |
| [Berichterstellung und Überwachung](https://aka.ms/deploymentplans/reporting)| Der Entwurf Ihrer Azure AD-Lösung für die Berichterstellung und Überwachung hängt von gesetzlichen, betrieblichen und sicherheitstechnischen Anforderungen sowie von vorhandenen Umgebungen und Prozessen ab. In diesem Artikel werden die unterschiedlichen Entwurfsoptionen vorgestellt. Zudem dient er als Richtlinie zur Ausarbeitung der geeigneten Bereitstellungsstrategie. |
