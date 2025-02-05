---
title: 'Tutorial: Gewähren des Zugriffs auf eine Node.js-Web-API über eine Desktop-App – Azure Active Directory B2C | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie mit Active Directory B2C eine Node.js-Web-API schützen und sie über eine .NET-Desktop-App aufrufen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8c4b2d818549da07faf9ecd28f61b4ed5315f745
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679319"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Tutorial: Gewähren des Zugriffs auf eine Node.js-Web-API über eine Desktop-App unter Verwendung von Azure Active Directory B2C

In diesem Tutorial erfahren Sie, wie Sie eine durch Azure Active Directory B2C (Azure AD B2C) geschützte Node.js-Web-API-Ressource über eine WPF-Desktop-App (Windows Presentation Foundation) aufrufen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen einer Web-API-Anwendung
> * Konfigurieren von Bereichen für eine Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Aktualisieren des Beispiels für die Verwendung der Anwendung

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die erforderlichen Schritte unter [Tutorial: Aktivieren der Authentifizierung von Desktop-Apps mit Konten unter Verwendung von Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md) aus.

## <a name="add-a-web-api-application"></a>Hinzufügen einer Web-API-Anwendung

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Konfigurieren von Bereichen

Bereiche ermöglichen die Steuerung des Zugriffs auf geschützte Ressourcen. Bereiche werden von der Web-API verwendet, um eine bereichsbasierte Zugriffssteuerung zu implementieren. So können beispielsweise einige Benutzer Lese- und Schreibzugriff haben, während andere Benutzer nur über Leseberechtigungen verfügen. In diesem Tutorial definieren Sie Leseberechtigungen für die Web-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Erteilen von Berechtigungen

Wenn Sie über eine Anwendung eine geschützte Web-API aufrufen möchten, müssen Sie Ihrer Anwendung Berechtigungen für die API erteilen. Im vorbereitenden Tutorial haben Sie in Azure AD B2C eine Webanwendung namens *app1* erstellt. Sie verwenden diese Anwendung für den Aufruf der Web-API.

1. Wählen Sie **Anwendungen** und dann *nativeapp1* aus.
1. Wählen Sie **API-Zugriff** und dann **Hinzufügen** aus.
1. Wählen Sie in der Dropdownliste **API auswählen** die Web-API *webapi1* aus.
1. Wählen Sie in der Dropdownliste **Bereiche auswählen** die Bereiche aus, die Sie zuvor definiert haben. Beispiel: *demo.read* und *demo.write*
1. Klicken Sie auf **OK**.

Ein Benutzer authentifiziert sich mit Azure AD B2C, um die WPF-Desktopanwendung zu verwenden. Die Desktopanwendung bezieht eine Autorisierungsgewährung von Azure AD B2C, um auf die geschützte Web-API zuzugreifen.

## <a name="configure-the-sample"></a>Das Beispiel konfigurieren

Nach dem Registrieren der Web-API und dem Definieren von Bereichen konfigurieren Sie den Web-API-Code für die Verwendung Ihres Azure AD B2C-Mandanten. In diesem Tutorial konfigurieren Sie ein Beispiel für eine Node.js-Webanwendung, die Sie von GitHub herunterladen können.

[Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip), oder klonen Sie die Beispiel-Web-App aus GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Das Node.js-Web-API-Beispiel verwendet die Bibliothek „Passport.js“, um Azure AD B2C zum Schützen von API-Aufrufen zu aktivieren.

1. Öffnen Sie die Datei `index.js` .
2. Konfigurieren Sie das Beispiel mit den Registrierungsinformationen für den Azure AD B2C-Mandanten. Ändern Sie die folgenden Codezeilen:

    ```nodejs
    var tenantID = "<your-tenant-name>.onmicrosoft.com";
    var clientID = "<application-ID>";
    var policyName = "B2C_1_signupsignin1";
    ```

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Starten Sie eine Node.js-Eingabeaufforderung.
2. Wechseln Sie zum Verzeichnis mit dem Node.js-Beispiel. Beispiel: `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Führen Sie die folgenden Befehle aus:
    ```
    npm install && npm update
    ```
    ```
    node index.js
    ```

### <a name="run-the-desktop-application"></a>Ausführen der Desktopanwendung

1. Öffnen Sie die Projektmappe **active-directory-b2c-wpf** in Visual Studio.
2. Drücken Sie **F5**, um die Desktop-App auszuführen.
3. Melden Sie sich mit der E-Mail-Adresse und dem Kennwort an, die Sie im Tutorial [Aktivieren der Authentifizierung von Desktop-Apps mit Konten unter Verwendung von Azure Active Directory B2C](active-directory-b2c-tutorials-desktop-app.md) verwendet haben.
4. Klicken Sie auf die Schaltfläche **API aufrufen**.

Die Desktopanwendung sendet eine Anforderung an die Web-API und erhält eine Antwort mit dem Anzeigenamen des angemeldeten Benutzers. Ihre geschützte Desktopanwendung ruft die geschützte Web-API in Ihrem Azure AD B2C-Mandanten auf.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hinzufügen einer Web-API-Anwendung
> * Konfigurieren von Bereichen für eine Web-API
> * Gewähren von Berechtigungen für die Web-API
> * Aktualisieren des Beispiels für die Verwendung der Anwendung

> [!div class="nextstepaction"]
> [Tutorial: Hinzufügen von Identitätsanbietern zu Ihren Anwendungen in Azure Active Directory B2C](tutorial-add-identity-providers.md)
