---
title: Erstellen und Konfigurieren von Clustern mit dem Enterprise-Sicherheitspaket in Azure HDInsight
description: Erfahren Sie, wie Sie Cluster mit dem Enterprise-Sicherheitspaket in Azure HDInsight erstellen und konfigurieren.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/09/2019
ms.openlocfilehash: 9b9071eae4ec18cb1d5fd277f6f5403ce3997f48
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261741"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Erstellen und Konfigurieren von Clustern mit dem Enterprise-Sicherheitspaket in Azure HDInsight

Das Enterprise-Sicherheitspaket (ESP) für Azure HDInsight bietet Ihnen Zugriff auf die Active Directory-basierte Authentifizierung, Mehrbenutzerunterstützung und rollenbasierte Zugriffssteuerung für Ihre Apache Hadoop-Cluster in Azure. HDInsight-Cluster mit dem Enterprise-Sicherheitspaket ermöglichen Organisationen mit strengen unternehmensinternen Sicherheitsrichtlinien die sichere Verarbeitung vertraulicher Daten.

In diesem Leitfaden wird gezeigt, wie Sie einen Azure HDInsight-Cluster mit aktiviertem Enterprise-Sicherheitspaket erstellen. Sie erfahren außerdem, wie Sie eine Windows-IaaS-VM erstellen, für die Active Directory und Domain Name System (DNS) aktiviert sind. Dieser Leitfaden soll Sie dabei unterstützen, die erforderlichen Ressourcen zu konfigurieren und lokalen Benutzern die Anmeldung bei einem HDInsight-Cluster mit aktiviertem Enterprise-Sicherheitspaket zu ermöglichen.

Der Server, den Sie erstellen, ersetzt Ihre *tatsächliche* lokale Umgebung. Sie verwenden ihn für die Setup- und Konfigurationsschritte. Später wiederholen Sie die Schritte in ihrer eigenen Umgebung. 

In diesem Leitfaden wird auch beschrieben, wie Sie eine Umgebung mit Hybrididentitäten unter Verwendung der Kennworthashsynchronisierung mit Azure Active Directory (Azure AD) erstellen. Der Leitfaden dient als Ergänzung zu [Verwendung des Enterprise-Sicherheitspakets in HDInsight](apache-domain-joined-architecture.md).

Vor Verwendung dieses Prozesses in Ihrer eigenen Umgebung:
* Richten Sie Active Directory und DNS ein.
* Aktivieren Sie Azure AD.
* Synchronisieren Sie lokale Benutzerkonten mit Azure AD.

![Diagramm zur Azure AD-Architektur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0002.png)

## <a name="create-an-on-premises-environment"></a>Erstellen einer lokalen Umgebung

In diesem Abschnitt verwenden Sie eine Schnellstartvorlage für die Azure-Bereitstellung, um neue VMs zu erstellen, DNS zu konfigurieren und eine neue Active Directory-Gesamtstruktur hinzuzufügen.

1. Wechseln Sie zur Schnellstartvorlage für die Bereitstellung, um [eine Azure-VM mit einer neuen Active Directory-Gesamtstruktur zu erstellen](https://azure.microsoft.com/resources/templates/active-directory-new-domain/).

1. Wählen Sie **Bereitstellung in Azure** aus.
1. Melden Sie sich bei Ihrem Azure-Abonnement an.
1. Gehen Sie auf der Seite **Create an Azure VM with a new AD Forest** (Azure-VM mit neuer AD-Gesamtstruktur erstellen) wie folgt vor:
    1. Wählen Sie in der Dropdownliste **Abonnement** das Abonnement aus, in dem die Ressourcen bereitgestellt werden sollen.
    1. Wählen Sie neben **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie den Namen *OnPremADVRG* ein.
    1. Geben Sie in die weiteren Vorlagenfelder folgende Details ein:

        * **Standort**: USA (Mitte)
        * **Administratorbenutzername**: HDIFabrikamAdmin
        * **Administratorkennwort**: \<IHR_KENNWORT>
        * **Domänenname**: HDIFabrikam.com
        * **DNS-Präfix**: hdifabrikam

        ![Vorlage zum Erstellen einer Azure-VM mit einer neuen Azure AD-Gesamtstruktur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Wählen Sie die Option **Kaufen**.
    1. Überwachen Sie die Bereitstellung, und warten Sie, bis sie abgeschlossen ist.
    1. Vergewissern Sie sich, dass die Ressourcen unter der richtigen Ressourcengruppe **OnPremADVRG** erstellt wurden.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurieren von Benutzern und Gruppen für den Clusterzugriff

In diesem Abschnitt erstellen Sie die Benutzer, die nach Abschluss dieses Leitfadens Zugriff auf den HDInsight-Cluster haben.

1. Stellen Sie mit Remotedesktop eine Verbindung mit dem Domänencontroller her.
    1. Wenn Sie die zu Beginn erwähnte Vorlage verwendet haben, ist der Domänencontroller eine VM namens **adVM** in der Ressourcengruppe **OnPremADVRG**.
    1. Wählen Sie im Azure-Portal **Ressourcengruppen** > **OnPremADVRG** > **adVM** > **Verbinden** aus.
    1. Wählen Sie auf der Registerkarte **RDP** die Option **RDP-Datei herunterladen** aus.
    1. Speichern Sie die Datei auf Ihrem Computer, und öffnen Sie sie.
    1. Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, verwenden Sie *HDIFabrikam\HDIFabrikamAdmin* als Benutzernamen. Geben Sie dann das Kennwort ein, das Sie für das Administratorkonto festgelegt haben.

1. Nachdem die Remotedesktopsitzung auf der Domänencontroller-VM geöffnet wurde, öffnen Sie über das **Server-Manager**-Dashboard die Option **Active Directory-Benutzer und -Computer**. Wählen Sie in der oberen rechten Ecke **Tools** > **Active Directory-Benutzer und -Computer** aus.

    ![Öffnen der Active Directory-Verwaltung im Server-Manager-Dashboard](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Erstellen Sie zwei neue Benutzer: **HDIAdmin** und **HDIUser**. Diese beiden Benutzer melden sich bei den HDInsight-Clustern an.

    1. Wählen Sie auf der Seite **Active Directory-Benutzer und -Computer** die Optionen **Aktion** > **Neu** > **Benutzer** aus.

        ![Erstellen eines neuen Active Directory-Benutzers](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-user.png)

    1. Geben Sie auf der Seite **Neues Objekt – Benutzer** in **Benutzeranmeldename** den Namen *HDIUser* ein. Klicken Sie anschließend auf **Weiter**.

        ![Erstellen des ersten Administratorbenutzerobjekts](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0020.png)

    1. Geben Sie im Popupfenster ein Kennwort für das neue Konto ein. Wählen Sie **Kennwort läuft nie ab** > **OK** aus.
    1. Wählen Sie **Fertig stellen** aus, um das neue Konto zu erstellen.
    1. Erstellen Sie einen weiteren Benutzer namens *HDIAdmin*.

        ![Erstellen des zweiten Administratorbenutzerobjekts](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0024.png)

1. Wählen Sie auf der Seite **Active Directory-Benutzer und -Computer** die Optionen **Aktion** > **Neu** > **Gruppe** aus. Fügen Sie eine Gruppe namens *HDIUserGroup* hinzu.

    ![Erstellen einer neuen Active Directory-Gruppe](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-active-directory-group.png)

    ![Erstellen eines neuen Objekts](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0028.png)

1. Fügen Sie **HDIUserGroup** den Benutzer **HDIUser** als Gruppenmitglied hinzu.

    1. Klicken Sie mit der rechten Maustaste auf **HDIUserGroup**, und wählen Sie **Eigenschaften** aus.
    1. Klicken Sie auf der Registerkarte **Mitglieder** auf **Hinzufügen**.
    1. Geben Sie im Feld **Geben Sie die zu verwendenden Objektnamen ein** den Namen *HDIUser* ein. Wählen Sie dann **OK**aus.
    1. Wiederholen Sie die vorherigen Schritte für das Konto **HDIAdmin**.

        ![Hinzufügen des Mitglieds „HDIUser“ zur Gruppe „HDIUserGroup“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Sie haben nun Ihre Active Directory-Umgebung erstellt. Sie haben zwei Benutzer und eine Benutzergruppe hinzugefügt, die auf den HDInsight-Cluster zugreifen können.

Die Benutzer werden mit Azure AD synchronisiert.

### <a name="create-an-azure-ad-directory"></a>Erstellen eines Azure AD-Verzeichnisses

1. Melden Sie sich beim Azure-Portal an.
1. Wählen Sie **Ressource erstellen** aus, und geben Sie *directory* ein. Wählen Sie **Azure Active Directory** > **Erstellen** aus.
1. Geben Sie unter **Organisationsname** den Namen *HDIFabrikam* ein.
1. Geben Sie unter *Name der Anfangsdomäne* den Namen **HDIFabrikamoutlook** ein.
1. Klicken Sie auf **Erstellen**.

    ![Erstellen eines Azure AD-Verzeichnisses](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

1. Wählen Sie links im Azure-Portal **Azure Active Directory** aus.
1. Wählen Sie ggf. **Verzeichnis wechseln** aus, um in das neue Verzeichnis **HDIFabrikamoutlook** zu wechseln.
1. Wählen Sie unter **Verwalten** die Option **Benutzerdefinierte Domänennamen** > **Benutzerdefinierte Domäne hinzufügen** aus.
1. Geben Sie unter **Benutzerdefinierter Domänenname** den Namen *HDIFabrikam.com* ein, und wählen Sie **Domäne hinzufügen** aus.

![Erstellen einer benutzerdefinierten Domäne](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Konfigurieren Ihres Azure AD-Mandanten

Jetzt konfigurieren Sie Ihren Azure AD-Mandanten, damit Sie Benutzer und Gruppen aus der lokalen Active Directory-Instanz mit der Cloud synchronisieren können.

1. Erstellen Sie einen Active Directory-Mandantenadministrator.
    1. Melden Sie sich beim Azure-Portal an, und wählen Sie Ihren Azure AD-Mandanten **HDIFabrikam** aus.
    1. Wählen Sie unter **Verwalten** die Optionen **Benutzer** > **Neuer Benutzer** aus.
    1. Geben Sie die folgenden Details für den neuen Benutzer ein:
        * **Name**: fabrikamazureadmin
        * **Benutzername**: fabrikamazureadmin@hdifabrikam.com
        * **Kennwort**: Ein beliebiges sicheres Kennwort

    1. Suchen Sie im Abschnitt **Gruppen** nach **AAD DC-Administratoren**, und klicken Sie auf **Auswählen**.

        ![Dialogfeld „Azure AD-Gruppen“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0038.png)

    1. Öffnen Sie den Abschnitt **Verzeichnisrolle**, und wählen Sie auf der rechten Seite **Globaler Administrator** > **OK** aus.

        ![Dialogfeld „Azure AD-Rolle“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0040.png)

    1. Geben Sie ein Kennwort für den Benutzer ein. Klicken Sie anschließend auf **Erstellen**.

1. Wenn Sie das Kennwort für den neu erstellten Benutzer \<fabrikamazureadmin@hdifabrikam.com> ändern möchten, verwenden Sie die Identität, um sich beim Azure-Portal anzumelden. Anschließend werden Sie aufgefordert, das Kennwort zu ändern.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchronisieren lokaler Benutzer mit Azure AD

### <a name="download-and-install-azure-ad-connect"></a>Herunterladen und Installieren von Azure AD Connect

1. [Azure AD Connect herunterladen](https://www.microsoft.com/download/details.aspx?id=47594).

1. Installieren Sie Azure AD Connect auf dem Domänencontroller.

    1. Öffnen Sie die ausführbare Datei, die Sie heruntergeladen haben, und akzeptieren Sie die Lizenzbedingungen. Wählen Sie **Weiter**.

        ![Seite „Willkommen bei Azure AD Connect“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0052.png)

    1. Wählen Sie **Express-Einstellungen verwenden** aus, und schließen Sie die Installation ab.

        ![Azure AD Connect-Express-Einstellungen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0054.png)

### <a name="configure-a-sync-with-the-on-premises-domain-controller"></a>Konfigurieren der Synchronisierung mit dem lokalen Domänencontroller

1. Geben Sie auf der Seite **Mit Azure AD verbinden** den Benutzernamen und das Kennwort des globalen Administrators für Azure AD ein. Verwenden Sie den Benutzernamen `fabrikamazureadmin@hdifabrikam.com`, den Sie beim Konfigurieren Ihres Active Directory-Mandanten erstellt haben. Wählen Sie dann  **Weiter** aus. 

    ![Seite „Mit Azure AD verbinden“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0058.png)

1. Geben Sie auf der Seite **Mit Active Directory Domain Services verbinden** den Benutzernamen und das Kennwort für ein Unternehmensadministratorkonto ein. Verwenden Sie den Benutzernamen `HDIFabrikam\HDIFabrikamAdmin` und das zugehörige Kennwort, die Sie zuvor erstellt haben. Wählen Sie dann  **Weiter** aus. 

   ![Seite „Mit Azure AD verbinden“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0060.png)
1. Wählen Sie auf der Seite **Azure AD-Anmeldungskonfiguration** die Option **Weiter** aus.
   ![Seite „Azure AD-Anmeldungskonfiguration“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0062.png)

1. Wählen Sie auf der Seite **Bereit zur Konfiguration** die Option  **Installieren** aus.

   ![Seite „Bereit zur Konfiguration“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0064.png)

1. Wählen Sie auf der Seite **Konfiguration abgeschlossen** die Option **Beenden** aus.
   ![Seite „Konfiguration abgeschlossen“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0078.png)

1. Vergewissern Sie sich nach Abschluss der Synchronisierung, dass die im IaaS-Verzeichnis erstellten Benutzer mit Azure AD synchronisiert werden.
   1. Melden Sie sich beim Azure-Portal an.
   1. Wählen Sie **Azure Active Directory** > **HDIFabrikam** > **Benutzer** aus.

### <a name="create-a-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität

Erstellen Sie eine benutzerseitig zugewiesene verwaltete Identität, die Sie zum Konfigurieren von Azure AD Domain Services (Azure AD DS) verwenden können. Weitere Informationen finden Sie unter [Erstellen, Auflisten, Löschen oder Zuweisen einer Rolle zu einer benutzerseitig zugewiesenen verwalteten Identität über das Azure-Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Melden Sie sich beim Azure-Portal an.
1. Wählen Sie **Ressource erstellen** aus, und geben Sie *verwaltete Identität* ein. Wählen Sie **Benutzerseitig zugewiesene verwaltete Identität** > **Erstellen** aus.
1. Geben Sie in **Ressourcenname** den Namen *HDIFabrikamManagedIdentity* ein.
1. Wählen Sie Ihr Abonnement aus.
1. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus, und geben Sie *HDIFabrikam-CentralUS* ein.
1. Wählen Sie unter **Standort** die Option **USA, Mitte** aus.
1. Klicken Sie auf **Erstellen**.

![Erstellen einer neuen benutzerseitig zugewiesenen verwalteten Identität](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0082.png)

### <a name="enable-azure-ad-ds"></a>Aktivieren von Azure AD DS

Gehen Sie wie folgt vor, um Azure AD DS zu aktivieren. Weitere Informationen finden Sie im Thema zum [Aktivieren von Azure AD DS über das Azure-Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Erstellen Sie ein virtuelles Netzwerk zum Hosten von Azure AD DS. Führen Sie den folgenden PowerShell-Code aus.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Melden Sie sich beim Azure-Portal an.
1. Wählen Sie **Ressource erstellen** aus, geben Sie *Domain services* ein, und wählen Sie **Azure AD Domain Services** aus.
1. Gehen Sie auf der Seite **Grundlagen** wie folgt vor:
    1. Wählen Sie unter **Verzeichnisname** das erstellte Azure AD-Verzeichnis aus: **HDIFabrikam**
    1. Geben Sie unter **DNS-Domänenname** den Namen *HDIFabrikam.com* ein.
    1. Wählen Sie Ihr Abonnement aus.
    1. Geben Sie die Ressourcengruppe **HDIFabrikam-CentralUS** an. Wählen Sie **USA, Mitte** als **Standort** aus.

        ![Azure AD DS: Grundlegende Informationen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0084.png)

1. Wählen Sie auf der Seite **Netzwerk** das Netzwerk (**HDIFabrikam-VNET**) und das Subnetz (**AADDS-subnet**) aus, die Sie mit dem PowerShell-Skript erstellt haben. Oder wählen Sie **Neu erstellen** aus, um jetzt ein virtuelles Netzwerk zu erstellen.

    ![Schritt „Virtuelles Netzwerk erstellen“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0086.png)

1. Auf der Seite **Administratorgruppe** sollte eine Benachrichtigung angezeigt werden, dass bereits eine Gruppe mit dem Namen **AAD DC-Administratoren** zur Verwaltung dieser Gruppe erstellt wurde. Sie können die Mitgliedschaft dieser Gruppe ändern, was in diesem Fall aber nicht nötig ist. Klicken Sie auf **OK**.

    ![Anzeigen der Azure AD-Administratorgruppe](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0088.png)

1. Aktivieren Sie auf der Seite **Synchronisierung** die vollständige Synchronisierung, indem Sie **Alle** > **OK** auswählen.

    ![Aktivieren der Azure AD DS-Synchronisierung](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0090.png)

1. Überprüfen Sie auf der Seite **Zusammenfassung** die Details für Azure AD DS, und wählen Sie **OK** aus.

    ![Zusammenfassung von „Azure AD Domain Services aktivieren“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0092.png)

Nachdem Sie Azure AD DS aktiviert haben, wird auf den Azure AD-VMs ein lokaler DNS-Server ausgeführt.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurieren des virtuellen Azure AD DS-Netzwerks

In den folgenden Schritten konfigurieren Sie Ihr virtuelles Azure AD DS-Netzwerk (**HDIFabrikam-AADDSVNET**) zur Verwendung Ihrer benutzerdefinierten DNS-Server.

1. Suchen Sie nach den IP-Adressen Ihrer benutzerdefinierten DNS-Server. 
    1. Wählen Sie die Azure AD DS-Ressource **HDIFabrikam.com** aus. 
    1. Wählen Sie unter **Verwalten** die Option  **Eigenschaften** aus. 
    1. Sie finden die IP-Adressen unter  **IP-Adresse im virtuellen Netzwerk**.

    ![Suchen der benutzerdefinierten DNS-IP-Adressen für Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0096.png)

1. Konfigurieren Sie **HDIFabrikam-AADDSVNET** für die Verwendung der benutzerdefinierten IP-Adressen 10.0.0.4 und 10.0.0.5.

    1. Wählen Sie unter **Einstellungen** die Option **DNS-Server** aus. 
    1. Wählen Sie **Benutzerdefiniert** aus.
    1. Geben Sie im Textfeld die erste IP-Adresse ein (*10.0.0.4*).
    1. Wählen Sie **Speichern**.
    1. Wiederholen Sie die Schritte, um die andere IP-Adresse (*10.0.0.5*) hinzuzufügen.

In unserem Szenario wurde Azure AD DS für die Verwendung der IP-Adressen 10.0.0.4 und 10.0.0.5 konfiguriert. Dabei wurde dieselbe IP-Adresse für das virtuelle Azure AD DS-Netzwerk festgelegt:

![Seite für benutzerdefinierte DNS-Server](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0098.png)

## <a name="securing-ldap-traffic"></a>Sichern des LDAP-Datenverkehrs

Das Lightweight Directory Access Protocol (LDAP) wird zum Lesen und Schreiben in Azure Active Directory verwendet. Sie können die Vertraulichkeit und Sicherheit von LDAP-Datenverkehr mithilfe der Secure Sockets Layer (SSL)- oder Transport Layer Security (TLS)-Technologie gewährleisten. Sie können LDAP über SSL (Secure LDAP, LDAPS) aktivieren, indem Sie ein korrekt formatiertes Zertifikat installieren.

Weitere Informationen zu Secure LDAP finden Sie unter [Konfigurieren von Secure LDAP (LDAPS) für eine verwaltete Azure AD Domain Services-Domäne](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

In diesem Abschnitt erstellen Sie ein selbstsigniertes Zertifikat, laden das Zertifikat herunter und konfigurieren LDAPS für die verwaltete Azure AD DS-Domäne **HDIFabrikam**.

Mit dem folgenden Skript wird ein Zertifikat für **HDIFabrikam** erstellt. Das Zertifikat wird unter dem Pfad *LocalMachine* gespeichert.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

> [!NOTE] 
> Zum Erstellen der SSL-Zertifikatanforderung kann ein Hilfsprogramm oder eine Anwendung verwendet werden, von dem bzw. der eine gültige Public Key Cryptography Standards (PKCS) \#10-Anforderung erstellt wird.

Überprüfen Sie, ob das Zertifikat im **persönlichen** Speicher auf dem Computer installiert wurde:

1. Starten Sie die Microsoft Management Console (MMC).
1. Fügen Sie das Snap-In **Zertifikate** hinzu, mit dem Zertifikate auf dem lokalen Computer verwaltet werden.
1. Erweitern Sie **Zertifikate (lokaler Computer)**  > **Persönlich** > **Zertifikate**. Der **persönliche** Speicher sollte ein neues Zertifikat enthalten. Dieses Zertifikat ist auf den vollständig qualifizierten Hostnamen ausgestellt.

    ![Überprüfen der lokalen Zertifikaterstellung](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0102.png)

1. Klicken Sie im rechten Bereich mit der rechten Maustaste auf das erstellte Zertifikat. Zeigen Sie auf **Alle Aufgaben**, und wählen Sie dann **Exportieren** aus.

1. Wählen Sie auf der Seite **Privaten Schlüssel exportieren** die Option  **Ja, privaten Schlüssel exportieren** aus. Der Computer, auf den der Schlüssel importiert wird, benötigt den privaten Schlüssel, um die verschlüsselten Nachrichten zu lesen.

    ![Seite „Privaten Schlüssel exportieren“ des Zertifikatexport-Assistenten](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0103.png)

1. Übernehmen Sie auf der Seite **Exportdateiformat** die Standardeinstellungen, und wählen Sie dann **Weiter** aus. 
1. Geben Sie auf der Seite **Kennwort** ein Kennwort für den privaten Schlüssel ein. Wählen Sie als **Verschlüsselung** den Typ **TripleDES-SHA1** aus. Klicken Sie anschließend auf **Weiter**.
1. Geben Sie auf der Seite **Exportdatei** den Pfad und Namen der exportierten Zertifikatsdatei ein, und wählen Sie dann **Weiter** aus. Der Dateiname muss die Erweiterung „.pfx“ aufweisen. Die Datei wird im Azure-Portal konfiguriert, um eine sichere Verbindung herzustellen.
1. Aktivieren Sie LDAPS für eine verwaltete Azure AD DS-Domäne.
    1. Wählen Sie im Azure-Portal die Domäne **HDIFabrikam.com** aus.
    1. Wählen Sie unter **Verwalten** die Option **Secure LDAP** aus.
    1. Wählen Sie auf der Seite **Secure LDAP** unter **Secure LDAP** die Einstellung **Aktivieren** aus.
    1. Suchen Sie nach der PFX-Zertifikatsdatei, die Sie auf Ihren Computer exportiert haben.
    1. Geben Sie das Kennwort für das Zertifikat ein.

    ![Aktivieren von sicherem LDAP](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0113.png)

1. Nachdem Sie LDAPS aktiviert haben, aktivieren Sie Port 636, um die Erreichbarkeit sicherzustellen.
    1. Wählen Sie in der Ressourcengruppe **HDIFabrikam-CentralUS** die Netzwerksicherheitsgruppe **AADDS-HDIFabrikam.com-NSG** aus.
    1. Wählen Sie unter **Einstellungen** die Optionen **Eingangssicherheitsregeln** > **Hinzufügen** aus.
    1. Geben Sie auf der Seite **Eingangssicherheitsregel hinzufügen** die folgenden Eigenschaften ein, und wählen Sie **Hinzufügen** aus:

        | Eigenschaft | Wert |
        |---|---|
        | `Source` | Any |
        | Source port ranges | * |
        | Destination | Any |
        | Destination port range | 636 |
        | Protocol | Any |
        | Action | Allow |
        | Priority | \<Gewünschte Zahl> |
        | NAME | Port_LDAP_636 |

    ![Dialogfeld „Eingangssicherheitsregel hinzufügen“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

**HDIFabrikamManagedIdentity** ist die benutzerseitig zugewiesene verwaltete Identität. Für die verwaltete Identität ist die Rolle „HDInsight-Domänendienste: Mitwirkender“ aktiviert, sodass damit Domänendienstvorgänge gelesen, erstellt, geändert und gelöscht werden können.

![Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0117.png)

## <a name="create-an-esp-enabled-hdinsight-cluster"></a>Erstellen eines HDInsight-Clusters mit aktiviertem Enterprise-Sicherheitspaket

Für diesen Schritt müssen vorab die folgenden Aufgaben ausgeführt werden:

1. Erstellen Sie die neue Ressourcengruppe *HDIFabrikam-WestUS* am Standort **USA, Westen**.
1. Erstellen Sie ein virtuelles Netzwerk, das den HDInsight-Cluster mit dem aktivierten Enterprise-Sicherheitspaket hostet.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Erstellen Sie eine Peerbeziehung zwischen dem virtuellen Netzwerk, das Azure AD DS hostet (`HDIFabrikam-AADDSVNET`), und dem virtuellen Netzwerk, das den HDInsight-Cluster mit dem aktivierten Enterprise-Sicherheitspaket hostet (`HDIFabrikam-HDIVNet`). Führen Sie mit dem folgenden PowerShell-Code das Peering dieser beiden virtuellen Netzwerke durch.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Erstellen Sie ein neues Azure Data Lake Storage Gen2-Konto namens **Hdigen2store**. Konfigurieren Sie das Konto mit der benutzerseitig verwalteten Identität **HDIFabrikamManagedIdentity**. Weitere Informationen finden Sie unter [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Richten Sie im virtuellen Netzwerk **HDIFabrikam-AADDSVNET** ein benutzerdefiniertes DNS ein.
    1. Navigieren Sie im Azure-Portal zu **Ressourcengruppen** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS-Server**.
    1. Wählen Sie **Benutzerdefiniert** aus, und geben Sie *10.0.0.4* und *10.0.0.5* ein.
    1. Wählen Sie **Speichern** aus.

        ![Speichern benutzerdefinierter DNS-Einstellungen für ein virtuelles Netzwerk](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0123.png)

1. Erstellen Sie einen neuen HDInsight Spark-Cluster mit aktiviertem Enterprise-Sicherheitspaket.
    1. Wählen Sie **Benutzerdefiniert (Größe, Einstellungen, Apps)** aus.
    2. Geben Sie Details für **Grundlagen** (Abschnitt 1) ein. Der **Clustertyp** muss **Spark 2.3 (HDI 3.6)** lauten. Die **Ressourcengruppe** muss **HDIFabrikam-CentralUS** lauten.

    1. Geben Sie für **Sicherheit + Netzwerkbetrieb** (Abschnitt 2) folgende Details ein:
        * Wählen Sie unter **Enterprise-Sicherheitspaket** die Einstellung **Aktiviert** aus.
        * Wählen Sie **Clusteradministratorbenutzer** und das Konto **HDIAdmin** aus, das Sie als lokalen Administratorbenutzer erstellt haben. Klicken Sie auf **Auswählen**.
        * Wählen Sie **Clusterzugriffsgruppe** > **HDIUserGroup** aus. Jeder Benutzer, den Sie dieser Gruppe später hinzufügen, kann auf HDInsight-Cluster zugreifen.

            ![Auswählen der Clusterzugriffsgruppe „HDIUserGroup“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0129.jpg)

    1. Führen Sie die weiteren Schritte der Clusterkonfiguration aus, und überprüfen Sie die Details in der **Clusterübersicht**. Klicken Sie auf **Erstellen**.

1. Melden Sie sich bei der Ambari-Benutzeroberfläche für den neu erstellten Cluster unter `https://CLUSTERNAME.azurehdinsight.net` an. Verwenden Sie Ihren Administratorbenutzernamen `hdiadmin@hdifabrikam.com` und das zugehörige Kennwort.

    ![Anmeldefenster der Apache Ambari-Benutzeroberfläche](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0135.jpg)

1. Wählen Sie im Cluster-Dashboard **Roles** (Rollen) aus.
1. Geben Sie auf der Seite **Roles** (Rollen) unter **Assign roles to these** (Rollen zuweisen zu) neben der Rolle **Cluster Administrator** (Clusteradministrator) die Gruppe *hdiusergroup* ein. 

    ![Zuweisen der Clusteradministratorrolle zu „hdiusergroup“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0137.jpg)

1. Öffnen Sie den Secure Shell (SSH)-Client, und melden Sie sich beim Cluster an. Verwenden Sie den **hdiuser**, den Sie in der lokalen Active Directory-Instanz erstellt haben.

    ![Anmelden beim Cluster mithilfe des SSH-Clients](./media/apache-domain-joined-create-configure-enterprise-security-cluster/hdinsight-image-0139.jpg)

Wenn Sie sich mit diesem Konto anmelden können, ist der Cluster mit dem Enterprise-Sicherheitspaket ordnungsgemäß für die Synchronisierung mit Ihrer lokalen Active Directory-Instanz konfiguriert.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie [Einführung in die Apache Hadoop-Sicherheit mit dem Enterprise-Sicherheitspaket](hdinsight-security-overview.md).
