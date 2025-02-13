---
title: Delegieren der am wenigsten privilegierten Rollen nach Administratoraufgabe – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie mehr über die zu delegierenden Rollen für Identitätsaufgaben in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5933fdd9932bd8c13ab0f231d677ce06112662ed
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169772"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Administratorrollen nach Administratoraufgabe in Azure Active Directory

In diesem Artikel finden Sie die erforderlichen Informationen, um Administratorrechte eines Benutzers einzuschränken, indem Sie die am wenigsten privilegierten Rollen in Azure Active Directory (Azure AD) zuweisen. Es werden Administratoraufgaben, die nach Featurebereichen organisiert sind, die am wenigsten privilegierte Rolle, die für die Ausführung der einzelnen Aufgaben erforderlich ist, sowie zusätzliche nicht globale Administratorrollen beschrieben, die die Aufgabe ausführen können.

## <a name="application-proxy"></a>Anwendungsproxy

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Konfigurieren einer Anwendungsproxy-App | Anwendungsadministrator | 
Konfigurieren von Connectorgruppeneigenschaften | Anwendungsadministrator | 
Erstellen einer Anwendungsregistrierung, wenn die Funktion für alle Benutzer deaktiviert ist | Anwendungsentwickler | Cloudanwendungs-, Anwendungsadministrator
Erstellen einer Connectorgruppe | Anwendungsadministrator | 
Löschen einer Connectorgruppe | Anwendungsadministrator | 
Anwendungsproxy deaktivieren | Anwendungsadministrator | 
Herunterladen eines Connectordiensts | Anwendungsadministrator | 
Lesen aller Konfigurationen | Anwendungsadministrator | 

## <a name="b2c"></a>B2C

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Erstellen von Azure AD B2C-Verzeichnissen | Alle Benutzer außer Gäste (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Erstellen von B2C-Anwendungen | Globaler Administrator | 
Erstellen von Unternehmensanwendungen | Cloudanwendungsadministrator | Anwendungsadministrator
Erstellen, Lesen, Aktualisieren und Löschen von B2C-Richtlinien | Globaler Administrator | 
Erstellen, Lesen, Aktualisieren und Löschen von Identitätsanbietern | Globaler Administrator | 
Erstellen, Lesen, Aktualisieren und Löschen von Benutzerflows zur Kennwortzurücksetzung | Globaler Administrator | 
Erstellen, Lesen, Aktualisieren und Löschen von Benutzerflows zur Profilbearbeitung | Globaler Administrator | 
Erstellen, Lesen, Aktualisieren und Löschen von Benutzerflows zur Anmeldung | Globaler Administrator | 
Erstellen, Lesen, Aktualisieren und Löschen von Benutzerflows zur Registrierung |Globaler Administrator | 
Erstellen, Lesen, Aktualisieren und Löschen von Benutzerattributen | Globaler Administrator | 
Erstellen, Lesen, Aktualisieren und Löschen von Benutzern | Globaler Administrator (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
Lesen aller Konfigurationen | Globaler Administrator | 
Lesen von B2C-Überwachungsprotokollen | Globaler Administrator (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

> [!NOTE]
> Globale Azure AD B2C-Administratoren haben nicht die gleichen Berechtigungen wie globale Azure AD-Administratoren. Wenn Sie über globale Azure AD B2C-Administratorrechte verfügen, stellen Sie sicher, dass Sie sich in einem Azure AD B2C-Verzeichnis und nicht in einem Azure AD-Verzeichnis befinden.

## <a name="company-branding"></a>Unternehmensbranding

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Konfigurieren des Unternehmensbrandings | Globaler Administrator | 
Lesen aller Konfigurationen | Rolle „Verzeichnis lesen“ | Standardbenutzerrolle (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Unternehmenseigenschaften

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Konfigurieren von Unternehmenseigenschaften | Globaler Administrator | 

## <a name="connect"></a>Verbinden

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Pass-Through-Authentifizierung | Globaler Administrator | 
Lesen aller Konfigurationen | Globaler Administrator | 
Nahtloses einmaliges Anmelden | Globaler Administrator | 

## <a name="connect-health"></a>Connect Health

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Hinzufügen oder Löschen von Diensten | Besitzer (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Anwenden von Fehlerbehebungen zum Synchronisieren von Fehlern | Mitwirkender (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Owner (Besitzer)
Konfigurieren von Benachrichtigungen | Mitwirkender (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Owner (Besitzer)
Konfigurieren von Einstellungen | Besitzer (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Konfigurieren von Synchronisierungsbenachrichtigungen | Mitwirkender (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Owner (Besitzer)
Lesen von AD FS-Sicherheitsberichten | Sicherheitsleseberechtigter | Mitwirkender, Besitzer
Lesen aller Konfigurationen | Leser (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Mitwirkender, Besitzer
Lesen von Synchronisierungsfehlern | Leser (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Mitwirkender, Besitzer
Lesen von Synchronisierungsdiensten | Leser (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Mitwirkender, Besitzer
Anzeigen von Metriken und Warnungen | Leser (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Mitwirkender, Besitzer
Anzeigen von Metriken und Warnungen | Leser (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Mitwirkender, Besitzer
Anzeigen von Metriken und Warnungen zum Synchronisierungsdienst | Leser (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Mitwirkender, Besitzer


## <a name="custom-domain-names"></a>Benutzerdefinierte Domänennamen

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Verwalten von Domänen | Globaler Administrator | 
Lesen aller Konfigurationen | Rolle „Verzeichnis lesen“ | Standardbenutzerrolle (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Domänendienste

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Erstellen einer Azure AD Domain Services-Instanz | Globaler Administrator | 
Ausführen aller Azure AD Domain Services-Aufgaben | Administratorengruppe für Azure AD-Domänencontroller (siehe [Dokumentation](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain)) | 
Lesen aller Konfigurationen | Leser für Azure-Abonnements, die den AD DS-Dienst umfassen | 

## <a name="devices"></a>Geräte

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Deaktivieren eines Geräts | Cloudgeräteadministrator | 
Aktivieren eines Geräts | Cloudgeräteadministrator | 
Lesen einer Standardkonfiguration | Standardbenutzerrolle (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Lesen von BitLocker-Schlüsseln | Sicherheitsleseberechtigter | Kennwort-, Sicherheitsadministrator

## <a name="enterprise-applications"></a>Unternehmensanwendungen

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Erteilen der Zustimmung zu delegierten Berechtigungen | Cloudanwendungsadministrator | Anwendungsadministrator
Erteilen der Zustimmung zu Anwendungsberechtigungen, jedoch nicht für Microsoft Graph oder Azure AD Graph | Cloudanwendungsadministrator | Anwendungsadministrator
Erteilen der Zustimmung zu Anwendungsberechtigungen für Microsoft Graph oder Azure AD Graph | Globaler Administrator | 
Erteilen der Zustimmung für Anwendungen, die auf eigene Daten zugreifen | Standardbenutzerrolle (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Erstellen einer Unternehmensanwendung | Cloudanwendungsadministrator | Anwendungsadministrator
Verwalten eines Anwendungsproxys | Anwendungsadministrator | 
Verwalten von Benutzereinstellungen | Globaler Administrator | 
Überprüfung des Lesezugriffs einer Gruppe oder einer App | Sicherheitsleseberechtigter | Sicherheits-, Benutzeradministrator
Lesen aller Konfigurationen | Standardbenutzerrolle (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Aktualisieren von Enterprise-Anwendungszuweisungen | Besitzer einer Enterprise-Anwendung (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloudanwendungs-, Anwendungsadministrator
Aktualisieren von Enterprise-Anwendungsbesitzern | Besitzer einer Enterprise-Anwendung (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloudanwendungs-, Anwendungsadministrator
Aktualisieren von Enterprise-Anwendungseigenschaften | Besitzer einer Enterprise-Anwendung (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloudanwendungs-, Anwendungsadministrator
Aktualisieren der Enterprise-Anwendungsbereitstellung | Besitzer einer Enterprise-Anwendung (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloudanwendungs-, Anwendungsadministrator
Aktualisieren des Self-Service-Zugriffs auf Enterprise-Anwendungen | Besitzer einer Enterprise-Anwendung (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloudanwendungs-, Anwendungsadministrator
Aktualisieren von Eigenschaften für das einmalige Anmelden | Besitzer einer Enterprise-Anwendung (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloudanwendungs-, Anwendungsadministrator

## <a name="entitlement-management"></a>Berechtigungsverwaltung
Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Hinzufügen von Ressourcen zu einem Katalog | Benutzeradministrator | Mit der Berechtigungsverwaltung können Sie diese Aufgabe an den Katalogbesitzer delegieren ([siehe Dokumentation](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners)).
Hinzufügen von SharePoint Online-Websites zum Katalog | Globaler Administrator


## <a name="groups"></a>Gruppen

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Lizenz zuweisen | Benutzeradministrator | 
Erstellen einer Gruppe | Benutzeradministrator | 
Erstellen, Aktualisieren oder Löschen der Zugriffsüberprüfung einer Gruppe oder einer App | Benutzeradministrator | 
Verwalten des Gruppenablaufs | Benutzeradministrator | 
Verwalten von Gruppeneinstellungen | Globaler Administrator | 
Lesen der gesamten Konfiguration (mit Ausnahme der ausgeblendeten Mitgliedschaft) | Rolle „Verzeichnis lesen“ | Standardbenutzerrolle (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Lesen der ausgeblendeten Mitgliedschaft | Gruppenmitglied | Gruppenbesitzer, Kennwortadministrator, Exchange-Administrator, SharePoint-Administrator, Teams-Administrator, Benutzeradministrator
Lesen der Mitgliedschaft von Gruppen mit ausgeblendeter Mitgliedschaft | Helpdeskadministrator | Benutzeradministrator, Teams-Administrator
Widerrufen von Lizenzen | Lizenzadministrator | Benutzeradministrator
Aktualisieren der Gruppenmitgliedschaft | Gruppenbesitzer (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Benutzeradministrator
Aktualisieren von Gruppenbesitzern | Gruppenbesitzer (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Benutzeradministrator
Aktualisieren von Gruppeneigenschaften | Gruppenbesitzer (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Benutzeradministrator

## <a name="identity-protection"></a>Schutz der Identität (Identity Protection)

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Konfigurieren von Warnungsbenachrichtigungen| Sicherheitsadministrator | 
Konfigurieren und Aktivieren oder Deaktivieren einer MFA-Richtlinie| Sicherheitsadministrator | 
Konfigurieren und Aktivieren oder Deaktivieren einer Richtlinie zum Anmelderisiko| Sicherheitsadministrator | 
Konfigurieren und Aktivieren oder Deaktivieren einer Richtlinie zum Benutzerrisiko | Sicherheitsadministrator | 
Konfigurieren von wöchentlichen Digests | Sicherheitsadministrator| 
Schließen aller Risikoerkennungen | Sicherheitsadministrator | 
Beheben oder Ausschließen von Sicherheitsrisiken | Sicherheitsadministrator | 
Lesen aller Konfigurationen | Sicherheitsleseberechtigter | 
Lesen aller Risikoerkennungen | Sicherheitsleseberechtigter | 
Lesen von Sicherheitsrisiken | Sicherheitsleseberechtigter | 

## <a name="licenses"></a>Lizenzen

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Lizenz zuweisen | Lizenzadministrator | Benutzeradministrator
Lesen aller Konfigurationen | Rolle „Verzeichnis lesen“ | Standardbenutzerrolle (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Widerrufen von Lizenzen | Lizenzadministrator | Benutzeradministrator
Testen oder Erwerben von Abonnements | Rechnungsadministrator | 


## <a name="monitoring---audit-logs"></a>Überwachung – Überwachungsprotokolle

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Lesen von Überwachungsprotokollen | Berichtsleser | Sicherheitsleseberechtigter, Sicherheitsadministrator

## <a name="monitoring---sign-ins"></a>Überwachung – Anmeldungen

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Lesen von Anmeldeprotokollen | Berichtsleser | Sicherheitsleseberechtigter, Sicherheitsadministrator

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Löschen aller vorhandenen App-Kennwörter, die von den ausgewählten Benutzern erstellt wurden | Globaler Administrator | 
Deaktivieren der MFA | Globaler Administrator | 
MFA aktivieren | Globaler Administrator | 
Verwalten von MFA-Diensteinstellungen | Globaler Administrator | 
Ausgewählte Benutzer müssen Kontaktmethoden erneut bereitstellen. | Authentifizierungsadministrator | 
Wiederherstellen der mehrstufigen Authentifizierung für alle gespeicherten Geräte  | Authentifizierungsadministrator | 

## <a name="mfa-server"></a>MFA-Server

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Benutzer sperren/zulassen | Globaler Administrator | 
Konfigurieren der Kontosperrung | Globaler Administrator | 
Konfigurieren von Cacheregeln | Globaler Administrator | 
Konfigurieren der Betrugswarnung | Globaler Administrator
Konfigurieren von Benachrichtigungen | Globaler Administrator | 
Konfigurieren einer Einmalumgehung | Globaler Administrator | 
Konfigurieren von Einstellungen für Telefonanrufe | Globaler Administrator | 
Konfigurieren von Anbietern | Globaler Administrator | 
Konfigurieren der Servereinstellungen | Globaler Administrator | 
Lesen eines Aktivitätsberichts | Globaler Administrator | 
Lesen aller Konfigurationen | Globaler Administrator | 
Lesen eines Serverstatus | Globaler Administrator |  

## <a name="organizational-relationships"></a>Organisationsbeziehungen

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Verwalten von Identitätsanbietern | Globaler Administrator | 
Verwalten von Einstellungen | Globaler Administrator | 
Verwalten von Nutzungsbedingungen | Globaler Administrator | 
Lesen aller Konfigurationen | Globaler Administrator | 

## <a name="password-reset"></a>Zurücksetzen des Kennworts

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Konfigurieren von Authentifizierungsmethoden | Globaler Administrator |
Konfigurieren der Anpassung | Globaler Administrator |
Konfigurieren einer Benachrichtigung | Globaler Administrator |
Konfigurieren einer lokalen Integration | Globaler Administrator |
Konfigurieren der Eigenschaften der Kennwortzurücksetzung | Benutzeradministrator | Globaler Administrator
Konfigurieren der Registrierung | Globaler Administrator |
Lesen aller Konfigurationen | Sicherheitsadministrator | Benutzeradministrator |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Zuweisen von Benutzern zu Rollen | Administrator für privilegierte Rollen | 
Konfigurieren von Rolleneinstellungen | Administrator für privilegierte Rollen | 
Anzeigen der Überwachungsaktivität | Sicherheitsleseberechtigter | 
Anzeigen von Rollenmitgliedschaften | Sicherheitsleseberechtigter | 

## <a name="roles-and-administrators"></a>Rollen und Administratoren

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Verwalten von Rollenzuweisungen | Administrator für privilegierte Rollen | 
Überprüfung des Lesezugriffs einer Azure AD-Rolle  | Sicherheitsleseberechtigter | Sicherheitsadministrator, Administrator für privilegierte Rollen
Lesen aller Konfigurationen | Standardbenutzerrolle (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Sicherheit – Authentifizierungsmethoden

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Konfigurieren von Authentifizierungsmethoden | Globaler Administrator | 
Lesen aller Konfigurationen | Globaler Administrator | 

## <a name="security---conditional-access"></a>Sicherheit: bedingter Zugriff

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Konfigurieren von durch MFA bestätigten IP-Adressen | Administrator für den bedingten Zugriff | 
Erstellen von benutzerdefinierten Steuerelementen | Administrator für den bedingten Zugriff | Sicherheitsadministrator
Erstellen von benannten Standorten | Administrator für den bedingten Zugriff | Sicherheitsadministrator
Erstellen von Richtlinien | Administrator für den bedingten Zugriff | Sicherheitsadministrator
Erstellen von Nutzungsbedingungen | Administrator für den bedingten Zugriff | Sicherheitsadministrator
Erstellen von VPN-Konnektivitätszertifikaten | Administrator für den bedingten Zugriff | Sicherheitsadministrator
Löschen einer klassischen Richtlinie | Administrator für den bedingten Zugriff | Sicherheitsadministrator
Löschen von Nutzungsbedingungen | Administrator für den bedingten Zugriff | Sicherheitsadministrator
Löschen eines VPN-Konnektivitätszertifikats | Administrator für den bedingten Zugriff | Sicherheitsadministrator
Deaktivieren einer klassischen Richtlinie | Administrator für den bedingten Zugriff | Sicherheitsadministrator
Verwalten von benutzerdefinierten Steuerelementen | Administrator für den bedingten Zugriff | Sicherheitsadministrator
Verwalten von benannten Standorten | Administrator für den bedingten Zugriff | Sicherheitsadministrator
Verwalten von Nutzungsbedingungen | Administrator für den bedingten Zugriff | Sicherheitsadministrator
Lesen aller Konfigurationen | Sicherheitsleseberechtigter | Sicherheitsadministrator
Lesen von benannten Standorten | Sicherheitsleseberechtigter | Administrator für bedingten Zugriff, Sicherheitsadministrator

## <a name="security---identity-security-score"></a>Sicherheit – Identity Security Score

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen | 
---- | --------------------- | ----------------
Lesen aller Konfigurationen | Sicherheitsleseberechtigter | Sicherheitsadministrator
Lesen des Security Score | Sicherheitsleseberechtigter | Sicherheitsadministrator
Aktualisieren des Ereignisstatus | Sicherheitsadministrator | 

## <a name="security---risky-sign-ins"></a>Sicherheit – Riskante Anmeldungen

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Lesen aller Konfigurationen | Sicherheitsleseberechtigter | 
Lesen riskanter Anmeldevorgänge | Sicherheitsleseberechtigter | 

## <a name="security---users-flagged-for-risk"></a>Sicherheit – Benutzer mit Risikokennzeichnung

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Verwerfen aller Ereignisse | Sicherheitsadministrator | 
Lesen aller Konfigurationen | Sicherheitsleseberechtigter | 
Lesen von Benutzern mit Risikokennzeichnung | Sicherheitsleseberechtigter | 

## <a name="users"></a>Benutzer

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Hinzufügen von Benutzern zur Verzeichnisrolle | Administrator für privilegierte Rollen | 
Hinzufügen von Benutzern zur Gruppe | Benutzeradministrator | 
Lizenz zuweisen | Lizenzadministrator | Benutzeradministrator
Erstellen eines Gastbenutzers | Gasteinladender | Benutzeradministrator
Benutzer erstellen | Benutzeradministrator | 
Löschen von Benutzern | Benutzeradministrator | 
Ungültige Aktualisierungstoken von Administratoren mit eingeschränkten Berechtigungen (siehe Dokumentation) | Benutzeradministrator | 
Ungültige Aktualisierungstoken von Benutzern ohne Administratorrechte (siehe Dokumentation) | Kennwortadministrator | Benutzeradministrator
Ungültige Aktualisierungstoken von privilegierten Administratorrollen (siehe Dokumentation) | Globaler Administrator | 
Lesen einer Standardkonfiguration | Standardbenutzerrolle (siehe [Dokumentation](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Zurücksetzen des Kennworts für Administratoren mit eingeschränkten Berechtigungen (siehe Dokumentation) | Benutzeradministrator | 
Zurücksetzen des Kennworts für Benutzer ohne Administratorrechte (siehe Dokumentation) | Kennwortadministrator | Benutzeradministrator
Zurücksetzen des Kennworts von privilegierten Administratoren | Globaler Administrator | 
Widerrufen von Lizenzen | Lizenzadministrator | Benutzeradministrator
Verwalten aller Eigenschaften mit Ausnahme des Benutzerprinzipalnamens | Benutzeradministrator | 
Aktualisieren des Benutzerprinzipalnamens für Administratoren mit eingeschränkten Berechtigungen (siehe Dokumentation) | Benutzeradministrator | 
Aktualisieren der Benutzerprinzipalnamens-Eigenschaft für Administratoren mit eingeschränkten Berechtigungen (siehe Dokumentation) | Globaler Administrator | 
Aktualisieren von Benutzereinstellungen | Globaler Administrator | 


## <a name="support"></a>Support

Aufgabe | Am wenigsten privilegierte Rolle | Zusätzliche Rollen
---- | --------------------- | ----------------
Einreichen eines Supporttickets | Dienstadministrator | Anwendungsadministrator, Azure Information Protection-Administrator, Rechnungsadministrator, Cloudanwendungsadministrator, Complianceadministrator, Dynamics 365-Administrator, Desktop Analytics-Administrator, Exchange-Administrator, Kennwortadministrator, Intune-Administrator, Skype for Business-Administrator, Power BI-Administrator, Administrator für die privilegierte Authentifizierung, SharePoint-Administrator, Teams Communications-Administrator, Teams-Administrator, Benutzeradministrator, Workplace Analytics-Administrator

## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen und Zuweisen von Administratorrollen in Azure Active Directory](directory-manage-roles-portal.md)
* [Berechtigungen von Administratorrollen in Azure Active Directory](directory-assign-admin-roles.md)
