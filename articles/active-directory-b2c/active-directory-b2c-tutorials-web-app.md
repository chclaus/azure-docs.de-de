---
title: 'Tutorial: Aktivieren der Authentifizierung in einer Webanwendung – Azure Active Directory B2C'
description: Tutorial zur Verwendung von Azure Active Directory B2C zum Bereitstellen einer Benutzeranmeldung für eine ASP.NET-Webanwendung.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 09/19/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b42634aa86f210382adb1ae224c847a92d89109b
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103307"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Tutorial: Aktivieren der Authentifizierung in einer Webanwendung mit Azure Active Directory B2C

In diesem Tutorial erfahren Sie, wie Sie Azure Active Directory B2C (Azure AD B2C) für die Anmeldung und Registrierung von Benutzern in einer ASP.NET-Webanwendung verwenden. Mit Azure AD B2C können sich Ihre Anwendungen über offene Standardprotokolle bei Konten für soziale Netzwerke sowie bei Unternehmenskonten und Azure Active Directory-Konten authentifizieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktualisieren der Anwendung in Azure AD B2C
> * Konfigurieren des Beispiels für die Verwendung der Anwendung
> * Anmelden über den Benutzerflow

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

* [Erstellen Sie Benutzerflows](tutorial-create-user-flows.md), um Benutzererfahrungen in Ihrer Anwendung zu aktivieren.
* Installieren Sie [Visual Studio 2019](https://www.visualstudio.com/downloads/) mit der Workload **ASP.NET und Webentwicklung**.

## <a name="update-the-application"></a>Aktualisieren der Anwendung

In dem Tutorial, das Sie zur Vorbereitung absolviert haben, wurde eine Webanwendung in Azure AD B2C hinzugefügt. Um die Kommunikation mit dem Beispiel in diesem Tutorial zu aktivieren, müssen Sie der Anwendung in Azure AD B2C einen Umleitungs-URI hinzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Anwendungen** und anschließend die Anwendung *webapp1* aus.
1. Fügen Sie unter **Antwort-URL** Folgendes hinzu: `https://localhost:44316`.
1. Wählen Sie **Speichern** aus.
1. Notieren Sie sich auf der Eigenschaftenseite die Anwendungs-ID, die Sie beim Konfigurieren der Webanwendung verwenden.
1. Wählen Sie **Schlüssel** > **Schlüssel generieren** > **Speichern** aus. Notieren Sie sich den Schlüssel. Er wird beim Konfigurieren der Webanwendung verwendet.

## <a name="configure-the-sample"></a>Das Beispiel konfigurieren

In diesem Tutorial konfigurieren Sie ein Beispiel, das Sie von GitHub herunterladen können. In dem Beispiel wird mithilfe von ASP.NET eine einfache Aufgabenliste bereitgestellt. Das Beispiel verwendet [Microsoft OWIN-Middleware-Komponenten](https://docs.microsoft.com/aspnet/aspnet/overview/owin-and-katana/). [Laden Sie eine ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip), oder klonen Sie das Beispiel aus GitHub. Extrahieren Sie die Beispieldatei unbedingt in einem Ordner, dessen Pfad insgesamt maximal 259 Zeichen lang ist.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Die Beispielprojektmappe enthält zwei Projekte:

* **TaskWebApp:** Dient zum Erstellen und Bearbeiten einer Aufgabenliste. In dem Beispiel wird der **Registrierungs- oder Anmeldebenutzerflow** für die Registrierung und Anmeldung von Benutzern verwendet.
* **TaskService:** Unterstützt die Funktionen zum Erstellen, Lesen, Aktualisieren und Löschen der Aufgabenliste. Die API wird durch Azure AD B2C geschützt und von „TaskWebApp“ aufgerufen.

Sie ändern das Beispiel, um die in Ihrem Mandanten registrierte Anwendung zu verwenden. Dazu benötigen Sie die Anwendungs-ID und den Anwendungsschlüssel, den bzw. die Sie zuvor notiert haben. Außerdem konfigurieren Sie die von Ihnen erstellten Benutzerflows. Das Beispiel definiert die Konfigurationswerte als Einstellungen in der Datei *Web.config*.

Aktualisieren Sie die Einstellungen in der Datei „Web.config“ zur Verwendung mit Ihrem Benutzerflow:

1. Öffnen Sie die Projektmappe **B2C-WebAPI-DotNet** in Visual Studio.
1. Öffnen Sie im Projekt **TaskWebApp** die Datei **Web.config**.
    1. Aktualisieren Sie den Wert für `ida:Tenant` und `ida:AadInstance` mit dem Namen des von Ihnen erstellten Azure AD B2C-Mandanten. Ersetzen Sie beispielsweise `fabrikamb2c` durch `contoso`.
    1. Ersetzen Sie den Wert für `ida:ClientId` durch die notierte Anwendungs-ID.
    1. Ersetzen Sie den Wert für `ida:ClientSecret` durch den notierten Schlüssel. Sie müssen den geheimen Clientschlüssel mit XML codieren, bevor Sie ihn der Datei „web.config“ hinzufügen.
    1. Ersetzen Sie den Wert für `ida:SignUpSignInPolicyId` durch `b2c_1_signupsignin1`.
    1. Ersetzen Sie den Wert für `ida:EditProfilePolicyId` durch `b2c_1_profileediting1`.
    1. Ersetzen Sie den Wert für `ida:ResetPasswordPolicyId` durch `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **TaskWebApp**, und klicken Sie anschließend auf **Als Startprojekt festlegen**.
1. Drücken Sie **F5**. Der Standardbrowser wird mit der lokalen Websiteadresse `https://localhost:44316/` geöffnet.

### <a name="sign-up-using-an-email-address"></a>Registrieren mit einer E-Mail-Adresse

1. Wählen Sie **Registrieren/Anmelden**, um sich als Benutzer der Anwendung zu registrieren. Der Benutzerflow **b2c_1_signupsignin1** wird verwendet.
1. Azure AD B2C zeigt eine Anmeldeseite mit einem Registrierungslink an. Da Sie noch nicht über ein Konto verfügen, wählen Sie **Jetzt registrieren** aus. Der Registrierungsworkflow zeigt eine Seite an, über die die Identität des Benutzers mithilfe einer E-Mail-Adresse erfasst und überprüft wird. Darüber hinaus werden im Rahmen des Registrierungsworkflows auch das Kennwort des Benutzers sowie die angeforderten Attribute erfasst, die im Benutzerflow definiert wurden.
1. Verwenden Sie eine gültige E-Mail-Adresse, und bestätigen Sie sie mithilfe des Prüfcodes. Legen Sie ein Kennwort fest. Geben Sie Werte für die angeforderten Attribute ein.

    ![Die Registrierungsseite wird im Rahmen des Anmelde-/Registrierungsworkflows angezeigt.](media/active-directory-b2c-tutorials-web-app/sign-up-workflow.PNG)

1. Wählen Sie **Erstellen**, um im Azure AD B2C-Mandanten ein lokales Konto zu erstellen.

Der Anwendungsbenutzer kann sich jetzt mit seiner E-Mail-Adresse anmelden und die Webanwendung verwenden.

Das Feature **Aufgabenliste** funktioniert jedoch erst, wenn Sie das nächste Tutorial in der Reihe abgeschlossen haben, [Tutorial: Schützen einer ASP.NET-Web-API mithilfe von Azure AD B2C](active-directory-b2c-tutorials-web-api.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Aktualisieren der Anwendung in Azure AD B2C
> * Konfigurieren des Beispiels für die Verwendung der Anwendung
> * Anmelden über den Benutzerflow

Fahren Sie nun mit dem nächsten Tutorial fort, um das Feature **Aufgabenliste** der Webanwendung zu aktivieren. Darin registrieren Sie eine Web-API-Anwendung in Ihrem eigenen Azure AD B2C-Mandanten und ändern dann das Codebeispiel, um ihren Mandanten für die API-Authentifizierung zu verwenden.

> [!div class="nextstepaction"]
> [Tutorial: Schützen einer ASP.NET-Web-API mithilfe von Azure Active Directory B2C >](active-directory-b2c-tutorials-web-api.md)
