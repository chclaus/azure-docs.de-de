---
title: 'Schützen der Kommunikation von OPC UA-Geräten mit OPC Vault: Azure | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie OPC UA-Anwendungen registrieren und signierte Anwendungszertifikate für Ihre OPC UA-Geräte mit OPC Vault ausstellen.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b5c886625c944e2f5501859e78506ca89ec3d765
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203691"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Verwenden des OPC Vault-Zertifikatverwaltungsdiensts

In diesem Artikel wird erläutert, wie Sie Anwendungen registrieren und signierte Anwendungszertifikate für Ihre OPC UA-Geräte ausstellen.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="deploy-the-certificate-management-service"></a>Bereitstellen des Zertifikatverwaltungsdiensts

Stellen Sie zunächst den Dienst in der Azure-Cloud bereit. Einzelheiten dazu finden Sie unter [Bereitstellen des OPC Vault-Zertifikatverwaltungsdiensts](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Erstellen des ZS-Zertifikats des Zertifikatausstellers

Erstellen Sie das ZS-Zertifikat des Zertifikatausstellers, falls dies noch nicht geschehen ist. Einzelheiten dazu finden Sie unter [Erstellen und Verwalten des Ausstellerzertifikats für OPC Vault](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Schützen von OPC UA-Anwendungen

### <a name="step-1-register-your-opc-ua-application"></a>Schritt 1: Registrieren Ihrer OPC UA-Anwendung 

> [!IMPORTANT]
> Die Rolle „Writer“ ist erforderlich, um eine Anwendung registrieren zu können.

1. Öffnen Sie Ihren Zertifikatdienst unter `https://myResourceGroup-app.azurewebsites.net`, und melden Sie sich an.
2. Wechseln Sie zu **Jetzt registrieren**. Für eine Anwendungsregistrierung muss einem Benutzer mindestens die Rolle „Writer“ zugewiesen sein.
2. Für das Eingabeformular gelten die Namenskonventionen in OPC UA. Im nachstehenden Screenshot beispielsweise sind die Einstellungen für das Beispiel [OPC UA-Referenzserver](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/Reference) im OPC UA .NET Standard-Stapel dargestellt:

   ![Screenshot der Registrierung des UA-Referenzservers](media/howto-opc-vault-secure/reference-server-registration.png "Registrierung des UA-Referenzservers")

5. Wählen Sie **Registrieren** aus, um die Anwendung in der Anwendungsdatenbank des Zertifikatdiensts zu registrieren. Im Workflow wird der Benutzer direkt zum nächsten Schritt weitergeleitet, um für die Anwendung ein signiertes Zertifikat anzufordern.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Schritt 2: Schützen Ihrer Anwendung mit einem von einer Zertifizierungsstelle signierten Anwendungszertifikat

Sichern Sie Ihre OPC UA-Anwendung durch Ausstellen eines signierten Zertifikats auf der Grundlage einer Zertifikatsignieranforderung (Certificate Signing Request, CSR). Alternativ können Sie ein neues Schlüsselpaar anfordern, das einen neuen privaten Schlüssel im PFX- oder PEM-Format enthält. Informationen darüber, welche Methode bei Ihrer Anwendung unterstützt wird, finden Sie in der Dokumentation zu Ihrem OPC UA-Gerät. Im Allgemeinen wird die Methode mit CSR empfohlen, weil hierfür kein privater Schlüssel über eine Leitung übertragen werden muss.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Anfordern eines neuen Zertifikats mit einem neuen Schlüsselpaar

1. Wechseln Sie zu **Anwendungen**.
3. Wählen Sie bei einer der aufgelisteten Anwendungen **Neue Anforderung** aus.

   ![Screenshot von „Neues Zertifikat anfordern“](media/howto-opc-vault-secure/request-new-certificate.png "Neues Zertifikat anfordern")

3. Wählen Sie **Request new KeyPair and Certificate** (Neues Schlüsselpaar und Zertifikat anfordern) aus, um einen privaten Schlüssel und ein neues signiertes Zertifikat mit dem öffentlichen Schlüssel für Ihre Anwendung anzufordern.

   ![Screenshot von „Generate a New KeyPair and Certificate“ (Neues Schlüsselpaar und Zertifikat generieren)](media/howto-opc-vault-secure/generate-new-key-pair.png "Neues Schlüsselpaar generieren")

4. Füllen Sie das Formular mit einem Betreff und den Domänennamen aus. Wählen Sie für den privaten Schlüssel „PEM“ oder „PFX“ mit Kennwort aus. Wählen Sie **Generate New KeyPair** (Neues Schlüsselpaar generieren) aus, um die Zertifikatanforderung zu erstellen.

   ![Screenshot von „View Certificate Request Details“ (Zertifikatanforderungsdetails anzeigen)](media/howto-opc-vault-secure/approve-reject.png "Zertifikat genehmigen")

5. Für die Genehmigung ist ein Benutzer mit der Rolle „Genehmigende Person“ und Signierberechtigungen in Azure Key Vault erforderlich. In einem typischen Workflow sollten die Rollen „Genehmigende Person“ bzw. „Anfordernde Person“ verschiedenen Benutzern zugewiesen werden. Wählen Sie **Genehmigen** oder **Ablehnen** aus, um die eigentliche Erstellung des Schlüsselpaars und den Signiervorgang zu starten bzw. abzubrechen. Das neue Schlüsselpaar wird erstellt und in Azure Key Vault sicher gespeichert, bis es vom Anforderer des Zertifikats heruntergeladen wird. Das sich ergebende Zertifikat mit öffentlichem Schlüssel wird von der Zertifizierungsstelle signiert. Es kann einige Sekunden dauern, bis diese Vorgänge abgeschlossen sind.

   ![Screenshot von „View Certificate Request Details“ (Zertifikatanforderungsdetails anzeigen), mit Genehmigungsmeldung unten](media/howto-opc-vault-secure/view-key-pair.png "Schlüsselpaar anzeigen")

7. Der sich ergebende private Schlüssel (PFX oder PEM) und das Zertifikat (DER) können hier in dem Format heruntergeladen werden, das für den Download von Binärdateien ausgewählt wurde. Es ist auch eine Base64-codierte Version verfügbar, z.B. zum Kopieren und Einfügen des Zertifikats in eine Befehlszeile oder einen Texteintrag. 
8. Nachdem der private Schlüssel heruntergeladen und sicher gespeichert wurde, können Sie **Delete Private Key** (Privaten Schlüssel löschen) auswählen. Das Zertifikat mit dem öffentlichen Schlüssel ist für die spätere Verwendung weiterhin verfügbar.
9. Aufgrund der Verwendung eines von der Zertifizierungsstelle signierten Zertifikats sollten hier auch das ZS-Zertifikat und die Zertifikatsperrliste (Certificate Revocation List, CRL) heruntergeladen werden.

Jetzt liegt es am OPC UA-Gerät, wie das neue Schlüsselpaar angewendet wird. Normalerweise werden das ZS-Zertifikat und die Zertifikatsperrliste in einen Ordner des Typs `trusted` kopiert, während die öffentlichen und privaten Schlüssel des Anwendungszertifikats auf einen Ordner des Typs `own` im Zertifikatspeicher angewendet werden. Einige Geräte unterstützen möglicherweise bereits einen „Serverpush“-Vorgang für Zertifikatupdates. Informationen hierzu finden Sie in der Dokumentation Ihres OPC UA-Geräts.

#### <a name="request-a-new-certificate-with-a-csr"></a>Anfordern eines neuen Zertifikats mit einer Zertifikatsignieranforderung 

1. Wechseln Sie zu **Anwendungen**.
3. Wählen Sie bei einer der aufgelisteten Anwendungen **Neue Anforderung** aus.

   ![Screenshot von „Neues Zertifikat anfordern“](media/howto-opc-vault-secure/request-new-certificate.png "Neues Zertifikat anfordern")

3. Wählen Sie **Request new Certificate with Signing Request** (Neues Zertifikat mit Signieranforderung anfordern) aus, um für Ihre Anwendung ein neues signiertes Zertifikat anzufordern.

   ![Screenshot von „Neues Zertifikat generieren“](media/howto-opc-vault-secure/generate-new-certificate.png "Neues Zertifikat generieren")

4. Laden Sie die Zertifikatsignieranforderung hoch, indem Sie eine lokale Datei auswählen oder eine Base64-codierte Zertifikatsignieranforderung in das Formular einfügen. Wählen Sie **Neues Zertifikat generieren** aus.

   ![Screenshot von „View Certificate Request Details“ (Zertifikatanforderungsdetails anzeigen)](media/howto-opc-vault-secure/approve-reject-csr.png "Zertifikatanforderung genehmigen")

5. Für die Genehmigung ist ein Benutzer mit der Rolle „Genehmigende Person“ und Signierberechtigungen in Azure Key Vault erforderlich. Wählen Sie **Genehmigen** oder **Ablehnen** aus, um den eigentlichen Signiervorgang zu starten bzw. abzubrechen. Das sich ergebende Zertifikat mit öffentlichem Schlüssel wird von der Zertifizierungsstelle signiert. Es kann einige Sekunden dauern, bis dieser Vorgang abgeschlossen ist.

   ![Screenshot von „View Certificate Request Details“ (Zertifikatanforderungsdetails anzeigen), mit der Genehmigungsmeldung unten](media/howto-opc-vault-secure/view-cert-csr.png "Zertifikat anzeigen")

6. Das sich ergebende Zertifikat (DER) kann hier als Binärdatei heruntergeladen werden. Es ist auch eine Base64-codierte Version verfügbar, z.B. zum Kopieren und Einfügen des Zertifikats in eine Befehlszeile oder einen Texteintrag. 
10. Nachdem das Zertifikat heruntergeladen und sicher gespeichert wurde, können Sie **Zertifikat löschen** auswählen.
11. Aufgrund der Verwendung eines von der Zertifizierungsstelle signierten Zertifikats sollten hier auch das ZS-Zertifikat und die Zertifikatsperrliste heruntergeladen werden.

Jetzt liegt es am OPC UA-Gerät, wie das neue Zertifikat angewendet wird. Normalerweise werden das ZS-Zertifikat und die Zertifikatsperrliste in einen Ordner des Typs `trusted` kopiert, während das Anwendungszertifikat auf einen Ordner des Typs `own` im Zertifikatspeicher angewendet wird. Einige Geräte unterstützen möglicherweise bereits einen „Serverpush“-Vorgang für Zertifikatupdates. Informationen hierzu finden Sie in der Dokumentation Ihres OPC UA-Geräts.

### <a name="step-4-device-secured"></a>Schritt 4: Gerät geschützt

Das OPC UA-Gerät ist jetzt für die Kommunikation mit anderen OPC UA-Geräten, die mit von einer Zertifizierungsstelle signierten Zertifikaten geschützt sind, ohne weitere Konfiguration bereit.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie OPC UA-Geräte geschützt werden, können Sie sich jetzt zu Folgendem informieren:

> [!div class="nextstepaction"]
> [Ausführen eines sicheren Zertifikatverwaltungsdiensts](howto-opc-vault-secure-ca.md)