---
title: Verknüpfen von Symantec AWS-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Symantec AWS-Daten mit Azure Sentinel verknüpfen.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 46ad8f6478e632cd57c009700de182993ce01be1
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240860"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Herstellen einer Verbindung zwischen Azure Sentinel und AWS CloudTrail

Verwenden Sie den AWS-Connector, um alle Ihre AWS CloudTrail-Ereignisse in Azure Sentinel zu streamen. Dieser Verbindungsprozess delegiert den Zugriff für Azure Sentinel auf Ihre AWS-Ressourcenprotokolle und schafft so eine Vertrauensbeziehung zwischen AWS CloudTrail und Azure Sentinel. Dies erfolgt in AWS durch Erstellen einer Rolle, die Azure Sentinel die Berechtigung erteilt, auf Ihre AWS-Protokolle zuzugreifen.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Schreibzugriff auf den Azure Sentinel-Arbeitsbereich.

> [!NOTE]
> Azure Sentinel sammelt CloudTrail-Ereignisse aus allen Regionen. Es wird davon abgeraten, Ereignisse aus einer Region in eine andere zu streamen.

## <a name="connect-aws"></a>Herstellen einer Verbindung mit AWS 


1. Wählen Sie in Azure Sentinel **Datenconnectors** und dann in der Tabelle die Zeile **Amazon Web Services** aus. Klicken Sie anschließend rechts im Bereich „AWS“ auf **Open connector page** (Connectorseite öffnen).

1. Befolgen Sie die Anleitungen unter **Konfiguration**, und führen Sie die folgenden Schritte aus.
 
1.  Wählen Sie in Ihrer Amazon Web Services-Konsole unter **Sicherheit, Identität und Compliance** den Eintrag **IAM** aus.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Wählen Sie **Rollen** und dann **Rolle erstellen** aus.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Wählen Sie **Anderes AWS-Konto** aus. Geben Sie im Feld **Konto-ID** die **Microsoft-Konto-ID** (**123412341234**) ein, die Sie auf der AWS Connector-Seite im Azure Sentinel-Portal finden.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Vergewissern Sie sich, dass **Externe ID erforderlich** ausgewählt ist, und geben Sie dann die externe ID (Arbeitsbereich-ID) ein, die Sie auf der AWS Connector-Seite im Azure Sentinel-Portal finden.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  Wählen Sie unter **Berechtigungsrichtlinie anfügen** **AWSCloudTrailReadOnlyAccess** aus.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Geben Sie ein Tag (optional) ein.

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Geben Sie anschließend einen Wert in **Rollenname** ein, und wählen Sie die Schaltfläche **Rolle erstellen** aus.

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Wählen Sie in der Liste der Rollen die Rolle aus, die Sie erstellt haben.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Kopieren Sie den Wert **Rollen-ARN**. Fügen Sie ihn im Azure Sentinel-Portal im Bildschirm „Amazon Web Services Connector“ in das Feld **Role to add** (Hinzuzufügende Rolle) ein, und klicken Sie auf **Hinzufügen**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Um das relevante Schema in Log Analytics für die AWS-Ereignisse zu verwenden, suchen Sie nach **AWSCloudTrail**.



## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument wurde beschrieben, wie Sie AWS CloudTrail mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).

