---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Amazon Web Services (AWS) | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Amazon Web Services (AWS) konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed1f1b6973d96fdc0bce560877720adfc5e33081
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170892"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Amazon Web Services (AWS)

In diesem Tutorial erfahren Sie, wie Sie Amazon Web Services (AWS) in Azure Active Directory (Azure AD) integrieren. Durch die Integration von Amazon Web Services (AWS) mit Azure Active Directory haben Sie folgende Möglichkeiten:

* Sie können in Azure AD steuern, wer auf Amazon Web Services (AWS) Zugriff hat.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch für Amazon Web Services (AWS) anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

![Diagramm der Beziehung zwischen Azure AD und AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Sie können mehrere Bezeichner für mehrere Instanzen konfigurieren. Beispiel:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Mit diesen Werten entfernt Azure AD den Wert **#** und sendet den korrekten Wert (`https://signin.aws.amazon.com/saml`) als Zielgruppen-URL im SAML-Token.

Dieser Ansatz wird aus folgenden Gründen empfohlen:

- Mit jeder Anwendung erhalten Sie ein individuelles X509-Zertifikat. Jede Instanz einer AWS App-Instanz kann dann ein anderes Zertifikatablaufdatum haben, das auf individueller AWS-Kontenbasis verwaltet werden kann. In diesem Fall gestaltet sich das Zertifikatrollover einfacher.

- Sie können die Benutzerbereitstellung mit der AWS-App in Azure AD aktivieren, woraufhin unser Dienst alle Rollen aus diesem AWS-Konto abruft. Sie müssen die AWS-Rollen nicht manuell in der App hinzufügen oder aktualisieren.

- Sie können den App-Besitzer für die App individuell zuweisen. Diese Person kann die App direkt in Azure AD verwalten.

> [!Note]
> Achten Sie darauf, nur eine Kataloganwendung zu verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein SSO-fähiges AWS-Abonnement

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Amazon Web Services (AWS) unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Hinzufügen von Amazon Web Services (AWS) aus dem Katalog

Zum Konfigurieren der Integration von Amazon Web Services (AWS) in Azure AD müssen Sie Amazon Web Services (AWS) aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Amazon Web Services (AWS)** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich die Option **Amazon Web Services (AWS)** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Amazon Web Services (AWS)

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Amazon Web Services (AWS) mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Amazon Web Services (AWS) eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Amazon Web Services (AWS) müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Amazon Web Services (AWS)](#configure-amazon-web-services-aws-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Testbenutzers für Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)** , um in Amazon Web Services (AWS) ein Pendant von B. Simon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Amazon Web Services (AWS)** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Im Abschnitt **SAML-Basiskonfiguration** ist die Anwendung vorkonfiguriert, und die notwendigen URLs sind bereits mit Azure aufgefüllt. Der Benutzer muss die Konfiguration speichern, indem er die Schaltfläche **Speichern** auswählt.

1. Falls Sie mehre Instanzen konfigurieren, geben Sie einen Bezeichnerwert an. Verwenden Sie ab der zweiten Instanz das folgende Format – einschließlich eines **#** -Zeichens zum Angeben eines eindeutigen SPN-Werts:

    `https://signin.aws.amazon.com/saml#2`

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Amazon Web Services (AWS) einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Amazon Web Services (AWS) gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Amazon Web Services (AWS)** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-amazon-web-services-aws-sso"></a>Konfigurieren des einmaligen Anmeldens für Amazon Web Services (AWS)

1. Melden Sie sich in einem anderen Browserfenster bei Ihrer AWS-Unternehmenswebsite als Administrator an.

2. Wählen Sie **AWS Home** (AWS-Startseite) aus.

    ![Screenshot: AWS-Unternehmenswebsite mit hervorgehobenem Symbol für „AWS Home“][11]

3. Wählen Sie **Identitäts- und Zugriffsverwaltung**.

    ![Screenshot: Seite mit den AWS-Diensten mit Hervorhebung von IAM][12]

4. Wählen Sie **Identity Providers** > **Create Provider** (Identitätsanbieter > Anbieter erstellen) aus.

    ![Screenshot: IAM-Seite mit den hervorgehobenen Optionen „Identity Providers“ (Identitätsanbieter) und „Create Provider“ (Anbieter erstellen)][13]

5. Führen Sie auf der Seite **Configure Provider** (Anbieter konfigurieren) die folgenden Schritte aus:

    ![Screenshot: „Configure Provider“ (Anbieter konfigurieren)][14]

    a. Wählen Sie für **Anbietertyp** die Option **SAML**.

    b. Geben Sie unter **Provider Name** (Anbietername) einen Anbieternamen ein (zum Beispiel: *WAAD*).

    c. Wählen Sie **Datei auswählen**, um die aus dem Azure-Portal heruntergeladene **Metadatendatei** hochzuladen.

    d. Wählen Sie **Nächster Schritt**.

6. Wählen Sie auf der Seite **Verify Provider Information** (Anbieterinformationen überprüfen) die Option **Create** (Erstellen) aus.

    ![Screenshot: „Verify Provider Information“ (Anbieterinformationen überprüfen) mit hervorgehobener Erstellungsoption][15]

7. Wählen Sie **Roles** > **Create role** (Rollen > Rolle erstellen) aus.

    ![Screenshot: Seite „Roles“ (Rollen)][16]

8. Führen Sie auf der Seite **Rolle erstellen** die folgenden Schritte aus:  

    ![Screenshot: Seite „Create role“ (Rolle erstellen)][19]

    a. Wählen Sie unter **Select type of trusted entity** (Art der vertrauenswürdigen Entität auswählen) die Option **SAML 2.0 federation** (SAML 2.0-Verbund) aus.

    b. Wählen Sie unter **Choose a SAML 2.0 Provider** (SAML 2.0-Anbieter auswählen) den **SAML-Anbieter** aus, den Sie zuvor erstellt haben (Beispiel: *WAAD*).

    c. Wählen Sie **Programmgesteuerten und AWS Management Console-Zugriff zulassen** aus.
  
    d. Klicken Sie auf **Weiter: Permissions** (Weiter: Berechtigungen).

9. Fügen Sie im Dialogfeld **Attach permissions policies** (Berechtigungsrichtlinien anfügen) die entsprechende Richtlinie für Ihre Organisation an. Wählen Sie anschließend **Next: Review** (Weiter: Überprüfen).  

    ![Screenshot: Dialogfeld „Attach permissions policies“ (Berechtigungsrichtlinien anfügen)][33]

10. Führen Sie im Dialogfeld **Review** (Überprüfen) die folgenden Schritte aus:

    ![Screenshot: Dialogfeld „Review“ (Überprüfen)][34]

    a. Geben Sie unter **Role name** (Rollenname) Ihren Rollennamen ein.

    b. Geben Sie unter **Role description** (Rollenbeschreibung) die Beschreibung ein.

    c. Wählen Sie **Create role** (Rolle erstellen) aus.

    d. Erstellen Sie so viele Rollen, wie Sie benötigen, und ordnen Sie sie dem Identitätsanbieter zu.

11. Verwenden Sie die Anmeldeinformationen des AWS-Dienstkontos, um die Rollen aus dem AWS-Konto in der Azure AD-Benutzerbereitstellung abzurufen. Öffnen Sie hierzu die Startseite der AWS-Konsole.

12. Klicken Sie auf **Dienste**. Wählen Sie unter **Security, Identity & Compliance** (Sicherheit, Identität und Compliance) die Option **IAM** aus.

    ![Screenshot: Startseite der AWS-Konsole mit den hervorgehobenen Optionen „Services“ (Dienste) und „IAM“](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Wählen Sie im IAM-Abschnitt die Option **Policies** (Richtlinien) aus.

    ![Screenshot: IAM-Abschnitt mit hervorgehobener Option „Policies“ (Richtlinien)](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Erstellen Sie durch Auswählen von **Create policy** (Richtlinie erstellen) eine neue Richtlinie, um die Rollen aus dem AWS-Konto in der Azure AD-Benutzerbereitstellung abzurufen.

    ![Screenshot: Seite „Create role“ (Rolle erstellen) mit hervorgehobener Option „Create policy“ (Richtlinie erstellen)](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Erstellen Sie eine eigene Richtlinie, um alle Rollen aus AWS-Konten abzurufen.

    ![Screenshot: Seite „Create policy“ (Richtlinie erstellen) mit hervorgehobener Registerkarte „JSON“](./media/amazon-web-service-tutorial/policy1.png)

    a. Wählen Sie unter **Create policy** (Richtlinie erstellen) die Registerkarte **JSON** aus.

    b. Fügen Sie in diesem Richtliniendokument den folgenden JSON-Code hinzu:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Wählen Sie **Review policy** (Richtlinie überprüfen) aus, um die Richtlinie zu überprüfen.

    ![Screenshot: Seite „Create policy“ (Richtlinie erstellen)](./media/amazon-web-service-tutorial/policy5.png)

16. Definieren Sie die neue Richtlinie.

    ![Screenshot: Seite „Create policy“ (Richtlinie erstellen) mit hervorgehobenen Feldern für Name und Beschreibung](./media/amazon-web-service-tutorial/policy2.png)

    a. Geben Sie unter **Name** die Zeichenfolge **AzureAD_SSOUserRole_Policy** ein.

    b. Geben Sie unter **Description** (Beschreibung) Folgendes ein: **This policy will allow to fetch the roles from AWS accounts** (Diese Richtlinie ermöglicht das Abrufen der Rollen aus AWS-Konten).

    c. Wählen Sie **Create policy** (Richtlinie erstellen) aus.

17. Erstellen Sie im AWS-IAM-Dienst ein neues Benutzerkonto.

    a. Wählen Sie in der AWS-IAM-Konsole die Option **Users** (Benutzer) aus.

    ![Screenshot: AWS-IAM-Konsole mit hervorgehobener Option „Users“ (Benutzer)](./media/amazon-web-service-tutorial/policy3.png)

    b. Wählen Sie zum Erstellen eines neuen Benutzers die Option **Add user** (Benutzer hinzufügen) aus.

    ![Screenshot: Schaltfläche „Add user“ (Benutzer hinzufügen)](./media/amazon-web-service-tutorial/policy4.png)

    c. Gehen Sie im Abschnitt **Add user** (Benutzer hinzufügen) wie folgt vor:

    ![Screenshot: Seite „Add user“ (Benutzer hinzufügen) mit den hervorgehobenen Bereichen für Benutzername und Zugriffstyp](./media/amazon-web-service-tutorial/adduser1.png)

    * Geben Sie **AzureADRoleManager** als Benutzernamen ein.

    * Wählen Sie für den Zugriffstyp die Option **Programmatic access** (Programmgesteuerter Zugriff) aus. Dadurch kann der Benutzer die APIs aufrufen und die Rollen aus dem AWS-Konto abrufen.

    * Wählen Sie **Next: Permissions** (Weiter: Berechtigungen) aus.

18. Erstellen Sie eine neue Richtlinie für diesen Benutzer.

    ![Screenshot: „Add user“ (Benutzer hinzufügen)](./media/amazon-web-service-tutorial/adduser2.png)

    a. Wählen Sie **Attach existing policies directly** (Vorhandene Richtlinien direkt anfügen) aus.

    b. Suchen Sie im Filterabschnitt **AzureAD_SSOUserRole_Policy** nach der neu erstellte Richtlinie.

    c. Wählen Sie die Richtlinie und anschließend **Next: Review** (Weiter: Überprüfen).

19. Überprüfen Sie die Richtlinie für den angefügten Benutzer.

    ![Screenshot: Seite „Add user“ (Benutzer hinzufügen) mit hervorgehobener Option für die Benutzererstellung](./media/amazon-web-service-tutorial/adduser3.png)

    a. Überprüfen Sie den Benutzernamen, den Zugriffstyp und die Richtlinie, die dem Benutzer zugeordnet wurde.

    b. Wählen Sie **Create User** (Benutzer erstellen) aus.

20. Laden Sie die Anmeldeinformationen eines Benutzers herunter.

    ![Screenshot: „Add user“ (Benutzer hinzufügen)](./media/amazon-web-service-tutorial/adduser4.png)

    a. Kopieren Sie **Access key ID** (Zugriffsschlüssel-ID) und **Secret access key** (Geheimer Zugriffsschlüssel) des Benutzers.

    b. Geben Sie diese Anmeldeinformationen im Azure AD-Abschnitt für die Benutzerbereitstellung ein, um die Rollen aus der AWS-Konsole abzurufen.

    c. Klicken Sie auf **Schließen**.

21. Navigieren Sie im Azure AD-Verwaltungsportal in der AWS-App zu **Bereitstellung**.

    ![Screenshot: AWS-App mit hervorgehobener Bereitstellungsoption](./media/amazon-web-service-tutorial/provisioning.png)

22. Geben Sie den Zugriffsschlüssel und das Geheimnis in das Feld **clientsecret** (Clientgeheimnis) bzw. **Geheimes Token** ein.

    ![Screenshot: Dialogfeld „Administratoranmeldeinformationen“](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Geben Sie in das Feld **clientsecret** (Geheimer Clientschlüssel) den AWS-Benutzerzugriffsschlüssel ein.

    b. Geben Sie in das Feld **Secret Token** (Geheimes Token) die geheimen AWS-Benutzerdaten ein.

    c. Wählen Sie **Verbindung testen** aus.

    d. Wählen Sie **Speichern** aus, um das Manifest zu speichern.

23. Wählen Sie im Abschnitt **Einstellungen** für **Bereitstellungsstatus** die Option **Ein** aus. Klicken Sie dann auf **Speichern**.

    ![Screenshot: Abschnitt „Einstellungen“ mit hervorgehobener Option „Ein“](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> Der Bereitstellungsdienst importiert nur Rollen aus AWS in Azure AD. Von diesem Dienst werden keine Benutzer und Gruppen aus Azure AD in AWS bereitgestellt.

### <a name="create-amazon-web-services-aws-test-user"></a>Erstellen eines Testbenutzers für Amazon Web Services (AWS)

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens B. Simon in Amazon Web Services (AWS). Bei Amazon Web Services (AWS) muss für das einmalige Anmelden nicht eigens ein Benutzer im AWS-System erstellt werden, daher ist hier keine Aktion erforderlich.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Amazon Web Services (AWS)“ klicken, sollten Sie automatisch bei der AWS-Anwendung angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Bekannte Probleme

 * Im Abschnitt **Bereitstellung** ist im Unterabschnitt **Zuordnungen** eine Lademeldung zu sehen, die Attributzuordnungen werden aber nicht angezeigt. Aktuell wird als Bereitstellungsworkflow lediglich das Importieren von Rollen aus AWS in Azure AD für die Auswahl im Rahmen einer Benutzer- oder Gruppenzuweisung unterstützt. Die Attributzuordnungen hierfür sind vorgegeben und nicht konfigurierbar.

 * Im Abschnitt **Bereitstellung** kann immer nur ein einzelner Satz von Anmeldeinformationen für einen einzelnen AWS-Mandanten eingegeben werden. Alle importierten Rollen werden in die Eigenschaft `appRoles` des Azure AD-Objekts [`servicePrincipal` ](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)für den AWS-Mandanten geschrieben.

   Azure AD können über den Katalog mehrere AWS-Mandanten (dargestellt durch `servicePrincipals`) für die Bereitstellung hinzugefügt werden. Es gibt jedoch ein bekanntes Problem, das dazu führt, dass nicht alle importierten Rollen aus den verschiedenen, für die Bereitstellung verwendeten AWS-Dienstprinzipalen (`servicePrincipals`) automatisch in den einzelnen Dienstprinzipal (`servicePrincipal`) für das einmalige Anmelden geschrieben werden können.

   Zur Umgehung dieses Problems können Sie mithilfe der [Microsoft Graph-API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) alle App-Rollen (`appRoles`) extrahieren, die in die einzelnen AWS-Dienstprinzipale (`servicePrincipal`) mit konfigurierter Bereitstellung importiert wurden. Anschließend können Sie diese Rollenzeichenfolgen dem AWS-Dienstprinzipal (`servicePrincipal`) hinzufügen, für den SSO konfiguriert ist.

* Rollen müssen die folgenden Anforderungen erfüllen, damit sie von AWS in Azure AD importiert werden können:

  * Für Rollen muss in AWS genau ein SAML-Anbieter festgelegt sein.

  * Der Rollen-ARN und der SAML-Anbieter-ARN für eine zu importierende Rolle dürfen zusammen maximal 119 Zeichen lang sein.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Amazon Web Services (AWS) mit Azure AD ausprobieren](https://aad.portal.azure.com/)

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png