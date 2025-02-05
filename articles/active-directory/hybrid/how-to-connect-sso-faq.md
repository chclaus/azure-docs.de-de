---
title: 'Azure AD Connect: Nahtloses einmaliges Anmelden – häufig gestellte Fragen | Microsoft-Dokumentation'
description: Antworten auf häufig gestellte Fragen zum nahtlosen einmaligen Anmelden mit Azure Active Directory.
services: active-directory
keywords: Was ist Azure AD Connect, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, Single Sign-On, einmaliges Anmelden
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96d0253e1e656f4bdb5180af0d57824aceb4f0dd
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2019
ms.locfileid: "71176661"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Nahtloses einmaliges Anmelden mit Azure Active Directory: Häufig gestellte Fragen

In diesem Artikel werden häufig gestellte Fragen zum nahtlosen einmaligen Anmelden (Seamless Single Sign-On, Seamless SSO) mit Azure Active Directory behandelt. Schauen Sie öfter vorbei, da wir regelmäßig neue Fragen hinzufügen.

**F: Welche Anmeldemethoden werden von Seamless SSO unterstützt?**

Die nahtlose SSO kann mit den Anmeldemethoden [Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md) oder [Passthrough-Authentifizierung](how-to-connect-pta.md) kombiniert werden. Dieses Feature kann jedoch nicht mit Active Directory-Verbunddiensten (AD FS) verwendet werden.

**F: Ist Seamless SSO eine kostenlose Funktion?**

Die nahtlose SSO ist eine kostenlose Funktion, sodass Sie für deren Verwendung keine kostenpflichtigen Versionen von Azure AD benötigen.

**F: Ist Seamless SSO in der Cloud [Microsoft Azure Deutschland](https://www.microsoft.de/cloud-deutschland) und der Cloud [Microsoft Azure Government](https://azure.microsoft.com/features/gov/) verfügbar?**

Nein. Das nahtlose einmalige Anmelden ist nur in der weltweiten Instanz von Azure AD verfügbar.

**F: Welche Anwendungen nutzen die `domain_hint`- oder `login_hint`-Parameterfunktion von Seamless SSO?**

Nachfolgend finden Sie eine nicht erschöpfende Liste von Anwendungen, die diese Parameter an Azure AD senden und Benutzern so eine unbeaufsichtigte Anmeldung über das nahtlose einmalige Anmelden ermöglichen können (d.h. Ihre Benutzer müssen ihre Benutzernamen oder Kennwörter nicht eingeben):

| Anwendungsname | Zu verwendende Anwendungs-URL |
| -- | -- |
| Zugriffsbereich | https:\//myapps.microsoft.com/contoso.com |
| Outlook im Web | https:\//outlook.office365.com/contoso.com |
| Office 365-Portale | https:\//portal.office.com?domain_hint=contoso.com, https:\//www.office.com?domain_hint=contoso.com |

Darüber hinaus erhalten Benutzer eine automatische Anmeldeerfahrung, wenn eine Anwendung Anmeldeanforderungen an die als Mandanten eingerichteten Endpunkte von Azure AD sendet - also https:\//login.microsoftonline.com/contoso.com/<..> oder https:\//login.microsoftonline.com/<tenant_ID>/<..> - anstelle des gemeinsamen Endpunkts von Azure AD - also https:\//login.microsoftonline.com/common/<...>. Nachfolgend finden Sie eine nicht erschöpfende Liste von Anwendungen, die diese Arten von Anmeldeanforderungen vornehmen.

| Anwendungsname | Zu verwendende Anwendungs-URL |
| -- | -- |
| SharePoint Online | https:\//contoso.sharepoint.com |
| Azure-Portal | https:\//portal.azure.com/contoso.com |

Ersetzen Sie „contoso.com“ in den Tabellen oben durch Ihren Domänennamen, um die richtigen Anwendungs-URLs für Ihren Mandanten zu erhalten.

Wenn unsere automatische Anmeldung für andere Anwendungen verwenden möchten, teilen Sie uns dies im Feedbackabschnitt mit.

**F: Unterstützt Seamless SSO `Alternate ID` als Benutzername anstelle von `userPrincipalName`?**

Ja. Die nahtlose SSO unterstützt `Alternate ID` als Benutzername, wenn dies in Azure AD Connect wie [hier](how-to-connect-install-custom.md) beschrieben entsprechend konfiguriert ist. Nicht alle Office 365-Anwendungen unterstützen `Alternate ID`. Angaben zur Supporterklärung finden Sie in der Dokumentation der jeweiligen Anwendung.

**F: Was ist der Unterschied zwischen dem von [Azure AD Join](../active-directory-azureadjoin-overview.md) bereitgestellten einmaligen Anmelden und Seamless SSO?**

[Azure AD Join](../active-directory-azureadjoin-overview.md) stellt SSO für Benutzer bereit, wenn deren Geräte bei Azure AD registriert sind. Diese Geräte müssen nicht unbedingt in eine Domäne eingebunden sein. SSO wird über *primäre Aktualisierungstoken* oder *PRTs* (Primary Refresh Token) bereitgestellt und nicht per Kerberos. Die Benutzeroberfläche ist für Windows 10-Geräte optimiert. Das einmalige Anmelden erfolgt im Microsoft Edge-Browser automatisch. Es kann mithilfe einer Browsererweiterung auch in Chrome genutzt werden.

Sie können sowohl Azure AD Join als auch das nahtlose einmalige Anmelden für Ihren Mandanten verwenden. Diese beiden Features ergänzen einander. Wenn beide Features aktiviert sind, hat das einmalige Anmelden aus Azure AD Join eine höhere Priorität als das nahtlose einmalige Anmelden.

**F: Ich möchte Geräte ohne Windows 10 mit Azure AD registrieren, ohne AD FS zu verwenden. Kann ich stattdessen Seamless SSO verwenden?**

Ja. Für dieses Szenario benötigen Sie Version 2.1 oder höher des [Clients für die Arbeitsplatzeinbindung](https://www.microsoft.com/download/details.aspx?id=53554).

**F: Wie kann ich ein Rollover des Kerberos-Entschlüsselungsschlüssels des `AZUREADSSOACC`-Computerkontos durchführen?**

Es ist wichtig, häufig ein Rollover des Kerberos-Entschlüsselungsschlüssels des `AZUREADSSOACC`-Computerkontos durchzuführen, das Azure AD repräsentiert und in Ihrer lokalen AD-Gesamtstruktur erstellt wurde.

>[!IMPORTANT]
>Es wird dringend empfohlen, das Rollover des Kerberos-Entschlüsselungsschlüssels mindestens alle 30 Tage durchzuführen.

Führen Sie diese Schritte auf dem lokalen Server durch, auf dem Azure AD Connect ausgeführt wird:

    **Step 1. Get list of AD forests where Seamless SSO has been enabled**

    1. Laden Sie zuerst [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview) herunter, und installieren Sie die Software.
    2. Navigieren Sie zum Ordner `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Importieren Sie das PowerShell-Modul „Nahtlose SSO“ mit folgendem Befehl: `Import-Module .\AzureADSSO.psd1`.
    4. Führen Sie PowerShell als Administrator aus. Rufen Sie in PowerShell `New-AzureADSSOAuthenticationContext` auf. Mit diesem Befehl sollte ein Popupfenster geöffnet werden, in dem Sie die Anmeldeinformationen des globalen Administrators Ihres Mandanten eingeben können.
    5. Rufen Sie `Get-AzureADSSOStatus | ConvertFrom-Json` auf. Dadurch erhalten Sie die Liste der AD-Gesamtstrukturen (siehe die Liste „Domänen“), in denen diese Funktion aktiviert ist.

    **Schritt 2: Aktualisieren des Kerberos-Entschlüsselungsschlüssels für jede AD-Gesamtstruktur, in der er eingerichtet wurde**

    1. Rufen Sie `$creds = Get-Credential` auf. Wenn Sie dazu aufgefordert werden, geben Sie die Anmeldeinformationen des Domänenadministrators für die vorgesehene AD-Gesamtstruktur ein.

    > [!NOTE]
    > Um die gewünschte AD-Gesamtstruktur zu finden, verwenden wir den Domänenadministrator-Benutzernamen, der im Format der Benutzerprinzipalnamen (User Principal Names, UPN) (johndoe@contoso.com) oder qualifizierten SAM-Kontodomänennamen (contoso\johndoe oder contoso.com\johndoe) bereitgestellt wird. Wenn Sie qualifizierte SAM-Kontodomänennamen verwenden, verwenden wir den Domänenteil des Benutzernamens, um [den Domänencontroller des Domänenadministrators mithilfe des DNS zu suchen](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Wenn Sie stattdessen den UPN verwenden, [übersetzen wir ihn in einen qualifizierten SAM-Kontodomänennamen ](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa), bevor der entsprechende Domänencontroller gesucht wird.

    2. Rufen Sie `Update-AzureADSSOForest -OnPremCredentials $creds` auf. Dieser Befehl aktualisiert den Kerberos-Entschlüsselungsschlüssel für das `AZUREADSSOACC`-Computerkonto in dieser bestimmten AD-Gesamtstruktur und in Azure AD.
    3. Wiederholen Sie die oben stehenden Schritte für jede AD-Gesamtstruktur, für die Sie das Feature eingerichtet haben.

    >[!IMPORTANT]
    >Stellen Sie sicher, dass Sie den `Update-AzureADSSOForest`-Befehl _nur_ einmal ausführen. Andernfalls funktioniert das Feature erst wieder, wenn die Kerberos-Tickets Ihrer Benutzer ablaufen und von Ihrem lokalen Active Directory neu ausgestellt werden.

**F: Wie kann ich Seamless SSO deaktivieren?**

    **Step 1. Disable the feature on your tenant**

        **Option A: Disable using Azure AD Connect**

        1. Run Azure AD Connect, choose **Change user sign-in page** and click **Next**.
        2. Uncheck the **Enable single sign on** option. Continue through the wizard.

        After completing the wizard, Seamless SSO will be disabled on your tenant. However, you will see a message on screen that reads as follows:

        "Single sign-on is now disabled, but there are additional manual steps to perform in order to complete clean-up. Learn more"

        To complete the clean-up process, follow steps 2 and 3 on the on-premises server where you are running Azure AD Connect.

        **Option B: Disable using PowerShell**

        Run the following steps on the on-premises server where you are running Azure AD Connect:

        1. First, download, and install [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
        2. Navigate to the `%programfiles%\Microsoft Azure Active Directory Connect` folder.
        3. Import the Seamless SSO PowerShell module using this command: `Import-Module .\AzureADSSO.psd1`.
        4. Run PowerShell as an Administrator. In PowerShell, call `New-AzureADSSOAuthenticationContext`. This command should give you a popup to enter your tenant's Global Administrator credentials.
        5. Call `Enable-AzureADSSO -Enable $false`.

        >[!IMPORTANT]
        >Disabling Seamless SSO using PowerShell will not change the state in Azure AD Connect. Seamless SSO will show as enabled in the **Change user sign-in** page.

    **Step 2. Get list of AD forests where Seamless SSO has been enabled**

    Follow tasks 1 through 4 below if you have disabled Seamless SSO using Azure AD Connect. If you have disabled Seamless SSO using PowerShell instead, jump ahead to task 5 below.

    1. Laden Sie zuerst [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview) herunter, und installieren Sie die Software.
    2. Navigieren Sie zum Ordner `%programfiles%\Microsoft Azure Active Directory Connect`.
    3. Importieren Sie das PowerShell-Modul „Nahtlose SSO“ mit folgendem Befehl: `Import-Module .\AzureADSSO.psd1`.
    4. Führen Sie PowerShell als Administrator aus. Rufen Sie in PowerShell `New-AzureADSSOAuthenticationContext` auf. Mit diesem Befehl sollte ein Popupfenster geöffnet werden, in dem Sie die Anmeldeinformationen des globalen Administrators Ihres Mandanten eingeben können.
    5. Rufen Sie `Get-AzureADSSOStatus | ConvertFrom-Json` auf. Dadurch erhalten Sie die Liste der AD-Gesamtstrukturen (siehe die Liste „Domänen“), in denen diese Funktion aktiviert ist.

    **Schritt 3: Löschen Sie das Computerkonto `AZUREADSSOACCT` manuell aus jeder in der Liste aufgeführten AD-Gesamtstruktur.**

## <a name="next-steps"></a>Nächste Schritte

- [**Schnellstart**](how-to-connect-sso-quick-start.md): Einrichten und Ausführen der nahtlosen SSO mit Azure AD
- [**Technische Einzelheiten**](how-to-connect-sso-how-it-works.md) – Funktionsweise dieses Features verstehen
- [**Problembehandlung**](tshoot-connect-sso.md) – Beheben von häufig auftretenden Problemen mit diesem Feature
- [**UserVoice:** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) Verfassen neuer Feature-Anforderungen
