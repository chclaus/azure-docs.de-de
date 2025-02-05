---
title: Installieren und Verwenden der Azure Terraform-Erweiterung für Visual Studio Code
description: Erfahren Sie, wie Sie die Azure Terraform-Erweiterung in Visual Studio Code installieren und verwenden.
services: terraform
ms.service: azure
keywords: Terraform, Azure, DevOps, Visual Studio Code, Erweiterung
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 7dd87f38ca5dfb1c2fdd38b56b314e0cbbe3ce12
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169919"
---
# <a name="install-and-use-the-azure-terraform-visual-studio-code-extension"></a>Installieren und Verwenden der Azure Terraform-Erweiterung für Visual Studio Code

Die Microsoft Azure Terraform-Erweiterung für Visual Studio Code wurde entwickelt, um die Produktivität des Entwicklers beim Erstellen und Testen mithilfe von Terraform mit Azure zu verbessern. Die Erweiterung bietet Unterstützung für Terraform-Befehle, die Visualisierung von Ressourcendiagrammen und die Cloud Shell-Integration in Visual Studio Code.

In diesem Artikel werden folgende Vorgehensweisen behandelt:
> [!div class="checklist"]
> * Verwenden von Terraform zum Automatisieren und Vereinfachen der Bereitstellung von Azure-Diensten
> * Installieren und Verwenden der Microsoft Terraform-Erweiterung für Visual Studio Code für Azure-Dienste
> * Verwenden von Visual Studio Code zum Schreiben, Planen und Ausführen von Terraform-Plänen

## <a name="prerequisites"></a>Voraussetzungen
- **Azure-Abonnement**: Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

- **Terraform**: [Installieren und konfigurieren Sie Terraform](/azure/virtual-machines/linux/terraform-install-configure).

- **Visual Studio Code**: Installieren Sie die für Ihre Umgebung geeignete Version von [Visual Studio Code](https://code.visualstudio.com/download).

## <a name="prepare-your-dev-environment"></a>Vorbereiten Ihrer Entwicklungsumgebung

### <a name="install-git"></a>Installation von Git

Für die Übungen in diesem Artikel müssen Sie [Git installieren](https://git-scm.com/).

### <a name="install-hashicorp-terraform"></a>Installieren von HashiCorp Terraform

Befolgen Sie die Anweisungen auf der HashiCorp-Webseite zum [Installieren von Terraform](https://www.terraform.io/intro/getting-started/install.html). Diese behandeln Folgendes:

- Herunterladen von Terraform
- Das Installieren von Terraform
- Das Überprüfen, ob Terraform ordnungsgemäß installiert wurde

>[!Tip]
>Befolgen Sie in jedem Fall die Anweisungen zum Festlegen Ihrer PATH-Systemvariable.

### <a name="install-nodejs"></a>Installieren von Node.js

Sie müssen [Node.js 6.0 oder höher](https://nodejs.org/) installieren, um Terraform in Cloud Shell zu verwenden.

>[!NOTE]
>Wenn Sie überprüfen möchten, ob Node.js installiert ist, öffnen Sie ein Terminalfenster, und geben Sie `node -v` ein.

### <a name="install-graphviz"></a>Installieren von GraphViz

Sie müssen [GraphViz installieren](https://graphviz.org/), um die Visualisierungsfunktion von Terraform zu verwenden.

>[!NOTE]
>Wenn Sie überprüfen möchten, ob GraphViz installiert ist, öffnen Sie ein Terminalfenster, und geben Sie `dot -V` ein.

### <a name="install-the-azure-terraform-visual-studio-code-extension"></a>Installieren der Azure Terraform-Erweiterung für Visual Studio Code

1. Starten Sie Visual Studio Code.

1. Wählen Sie **Erweiterungen**.

    ![Schaltfläche „Erweiterungen“](media/terraform-vscode-extension/tf-vscode-extensions-button.png)

1. Verwenden Sie das Textfeld zum **Suchen nach Erweiterungen in Marketplace**, um nach der Azure Terraform-Erweiterung zu suchen:

    ![Suchen nach Visual Studio Code-Erweiterungen in Marketplace](media/terraform-vscode-extension/tf-search-extensions.png)

1. Wählen Sie **Installieren** aus.

    >[!NOTE]
    >Wenn Sie für die Azure Terraform-Erweiterung auf **Installieren** klicken, installiert Visual Studio Code automatisch die Azure-Kontoerweiterung. Azure Account ist eine Abhängigkeitsdatei für die Azure Terraform-Erweiterung, die verwendet wird, um Authentifizierungen für Azure-Abonnements sowie auf Azure bezogene Codeerweiterungen durchzuführen.

#### <a name="verify-the-terraform-extension-is-installed-in-visual-studio-code"></a>Überprüfen, ob die Terraform-Erweiterung in Visual Studio Code installiert ist

1. Wählen Sie **Erweiterungen**.

1. Geben Sie `@installed` in das Suchtextfeld ein.

    ![Installierte Erweiterungen](media/terraform-vscode-extension/tf-installed-extensions.png)

Die Azure Terraform-Erweiterung wird in der Liste der installierten Erweiterungen angezeigt.

![Installierte Terraform-Erweiterungen](media/terraform-vscode-extension/tf-installed-terraform-extension-button.png)

Sie können nun alle unterstützten Terraform-Befehle in Ihrer Cloud Shell-Umgebung über Visual Studio Code ausführen.

## <a name="exercise-1-basic-terraform-commands-walk-through"></a>Übung 1: Exemplarische Vorgehensweise für grundlegende Terraform-Befehle

In dieser Übung erstellen Sie eine grundlegende Terraform-Konfigurationsdatei, die eine neue Azure-Ressourcengruppe bereitstellt, und führen diese Datei aus.

### <a name="prepare-a-test-plan-file"></a>Vorbereiten einer Testplandatei

1. Klicken Sie in Visual Studio Code auf der Menüleiste auf **Datei > Neue Datei**.

1. Navigieren Sie in Ihrem Browser zur Seite [Terraform azurerm_resource_group](https://www.terraform.io/docs/providers/azurerm/r/resource_group.html#), und kopieren Sie den Code aus dem Codeblock **Example Usage** (Beispielverwendung):

    ![Beispielverwendung](media/terraform-vscode-extension/tf-azurerm-resource-group-example-usage.png)

1. Fügen Sie den kopierten Code in die neue Datei ein, die Sie in Visual Studio Code erstellt haben.

    ![Einfügen des Beispielverwendungscodes](media/terraform-vscode-extension/tf-paste-example-usage-code.png)

    >[!NOTE]
    >Sie können den Wert **name** der Ressourcengruppe ändern, dieser muss jedoch für Ihr Azure-Abonnement eindeutig sein.

1. Klicken Sie auf der Menüleiste auf **Datei > Speichern unter**.

1. Navigieren Sie im Dialogfeld **Speichern unter** zu einem Speicherort Ihrer Wahl, und klicken Sie dann auf **Neuer Ordner**. (Ändern Sie den Namen des neuen Ordners in einen aussagekräftigeren Namen als *Neuer Ordner*.)

    >[!NOTE]
    >In diesem Beispiel wird der Ordner „TERRAFORM-TEST-PLAN“ genannt.

1. Vergewissern Sie sich, dass der neue Ordner ausgewählt (hervorgehoben) ist, und klicken Sie dann auf **Öffnen**.

1. Ändern Sie im Dialogfeld **Speichern unter** den Standardnamen der Datei in *main.tf*.

    ![Speichern unter; „main.tf“](media/terraform-vscode-extension/tf-save-as-main.png)

1. Wählen Sie **Speichern** aus.
1. Klicken Sie auf der Menüleiste auf **Datei > Ordner öffnen**. Navigieren Sie zum neu erstellten Ordner, und wählen Sie diesen aus.

### <a name="run-terraform-init-command"></a>Ausführen des Terraform-Befehls *init*

1. Starten Sie Visual Studio Code.

1. Klicken Sie auf der Menüleiste von Visual Studio Code auf **Datei > Ordner öffnen**, suchen Sie nach der Datei *main.tf*, und wählen Sie diese aus.

    ![main.tf-Datei](media/terraform-vscode-extension/tf-main-tf.png)

1. Wählen Sie auf der Menüleiste **Ansicht > Befehlspalette > Azure Terraform: Init** aus.

1. Klicken Sie in der angezeigten Bestätigung auf **OK**.

    ![Möchten Sie Cloud Shell öffnen?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloud-shell.png)

1. Wenn Sie Cloud Shell zum ersten Mal über einen neuen Ordner starten, werden Sie dazu aufgefordert, die Webanwendung einzurichten. Wählen Sie **Open**(Öffnen).

    ![Erster Start von Cloud Shell](media/terraform-vscode-extension/tf-first-launch-of-cloud-shell.png)

1. Die Seite „Willkommen bei Azure Cloud Shell“ wird geöffnet. Wählen Sie „Bash“ oder „PowerShell“ aus.

    ![Willkommen bei Azure Cloud Shell](media/terraform-vscode-extension/tf-welcome-to-azure-cloud-shell.png)

    >[!NOTE]
    >In diesem Beispiel wurde „Bash“ (Linux) ausgewählt.

1. Wenn Sie noch kein Azure-Speicherkonto eingerichtet haben, wird folgender Bildschirm angezeigt. Klicken Sie auf **Speicher erstellen**.

    ![Für Sie wurde kein Speicher bereitgestellt.](media/terraform-vscode-extension/tf-you-have-no-storage-mounted.png)

1. Azure Cloud Shell wird in der Shell gestartet, die Sie zuvor ausgewählt haben, und zeigt Informationen zu dem Cloudlaufwerk an, das gerade für Sie erstellt wurde.

    ![Cloudlaufwerk wurde erstellt](media/terraform-vscode-extension/tf-your-cloud-drive-has-been-created-in.png)

1. Sie können Cloud Shell nun beenden.

1. Klicken Sie auf der Menüleiste auf **Ansicht** > **Befehlspalette** > **Azure Terraform: Init**.

    ![Terraform wurde erfolgreich initialisiert.](media/terraform-vscode-extension/tf-terraform-has-been-successfully-initialized.png)

### <a name="visualize-the-plan"></a>Visualisieren des Plans

Im bisherigen Verlauf dieses Tutorials haben Sie GraphViz installiert. Terraform kann GraphViz verwenden, um eine visuelle Darstellung einer Konfiguration oder eines Ausführungsplans zu generieren. Die Azure Terraform-Erweiterung für Visual Studio Code implementiert dieses Feature über den Befehl *visualize*.

- Wählen Sie auf der Menüleiste **Ansicht > Befehlspalette > Azure Terraform: Visualisieren** aus.

    ![Visualisieren des Plans](media/terraform-vscode-extension/tf-graph.png)

### <a name="run-terraform-plan-command"></a>Ausführen des Terraform-Befehls *plan*

Der Terraform-Befehl *plan* wird verwendet, um zu überprüfen, ob der Ausführungsplan für Änderungen wie gewünscht funktioniert.

>[!NOTE]
>Der Terraform-Befehl *plan* nimmt keine Änderungen an Ihren Azure-Ressourcen vor. Wenn Sie die Änderungen in Ihrem Plan tatsächlich anwenden möchten, verwenden Sie den Terraform-Befehl *apply*.

- Klicken Sie auf der Menüleiste auf **Ansicht** > **Befehlspalette** > **Azure Terraform: Plan**.

    ![Terraform-Befehl „plan“](media/terraform-vscode-extension/tf-terraform-plan.png)

### <a name="run-terraform-apply-command"></a>Ausführen des Terraform-Befehls *apply*

Wenn Sie mit den Ergebnissen des Terraform-Befehls *plan* zufrieden sind, können Sie den Befehl *apply* ausführen.

1. Klicken Sie auf der Menüleiste auf **Ansicht** > **Befehlspalette** > **Azure Terraform: Apply**.

    ![Terraform-Befehl „apply“](media/terraform-vscode-extension/tf-terraform-apply.png)

1. Geben Sie `yes` ein.

    ![Terraform-Befehl „apply“: Wert „yes“](media/terraform-vscode-extension/tf-terraform-apply-yes.png)

### <a name="verify-your-terraform-plan-was-executed"></a>Überprüfen, ob Ihr Terraform-Plan ausgeführt wurde

So stellen Sie fest, ob Ihre neue Azure-Ressourcengruppe erfolgreich erstellt wurde:

1. Öffnen Sie das Azure-Portal.

1. Klicken Sie im linken Navigationsbereich auf **Ressourcengruppen**.

    ![Überprüfen Ihrer neuen Ressource](media/terraform-vscode-extension/tf-verify-resource-group-created.png)

Ihre neue Ressourcengruppe sollte in der Spalte **NAME** aufgeführt werden.

>[!NOTE]
>Sie können das Fenster für das Azure-Portal geöffnet lassen, denn es wird im nächsten Schritt verwendet.

### <a name="run-terraform-destroy-command"></a>Ausführen des Terraform-Befehls *destroy*

1. Klicken Sie auf der Menüleiste auf **Ansicht** > **Befehlspalette** > **Azure Terraform: Destroy**.

    ![Terraform-Befehl „destroy“](media/terraform-vscode-extension/tf-terraform-destroy.png)

1. Geben Sie *yes* (Ja) ein.

    ![Terraform-Befehl „destroy“: Wert „yes“](media/terraform-vscode-extension/tf-terraform-destroy-yes.png)

### <a name="verify-your-resource-group-was-destroyed"></a>Überprüfen, ob Ihre Ressourcengruppe gelöscht wurde

So bestätigen Sie, dass Terraform Ihre neue Ressourcengruppe erfolgreich gelöscht hat:

1. Klicken Sie auf der Seite **Ressourcengruppen** des Azure-Portals auf **Aktualisieren**.

1. Ihre Ressourcengruppe wird nicht mehr aufgeführt.

    ![Überprüfen, ob eine Ressourcengruppe gelöscht wurde](media/terraform-vscode-extension/tf-refresh-resource-groups-button.png)

## <a name="exercise-2-terraform-compute-module"></a>Übung 2: Terraform-*Computemodul*

In dieser Übung lernen Sie, wie Sie das Terraform-*Computemodul* in die Visual Studio Code-Umgebung laden.

### <a name="clone-the-terraform-azurerm-compute-module"></a>Klonen des Moduls „terraform-azurerm-compute“

1. Verwenden Sie [diesen Link](https://github.com/Azure/terraform-azurerm-compute), um auf das Modul „terraform-azurerm-compute“ auf GitHub zuzugreifen.

1. Wählen Sie **Clone or download**aus.

    ![Klonen oder herunterladen](media/terraform-vscode-extension/tf-clone-with-https.png)

    >[!NOTE]
    >In diesem Beispiel wurde der Ordner *terraform-azurerm-compute* genannt.

### <a name="open-the-folder-in-visual-studio-code"></a>Öffnen des Ordners in Visual Studio Code

1. Starten Sie Visual Studio Code.

1. Klicken Sie auf der Menüleiste auf **Datei > Ordner öffnen**, navigieren Sie zu dem Ordner, den Sie im vorherigen Schritt erstellt haben, und öffnen Sie ihn.

    ![Ordner „terraform-azurerm-compute“](media/terraform-vscode-extension/tf-terraform-azurerm-compute-folder.png)

### <a name="initialize-terraform"></a>Initialisieren von Terraform

Bevor Sie die Terraform-Befehle in Visual Studio Code verwenden können, müssen Sie die Plug-Ins für zwei Azure-Anbieter herunterladen: „random“ und „azurerm“.

1. Geben Sie im Terminalbereich der Visual Studio Code-IDE `terraform init` ein.

    ![Terraform-Befehl „init“](media/terraform-vscode-extension/tf-terraform-init-command.png)

1. Geben Sie `az login` ein, drücken Sie die **EINGABETASTE**, und folgen Sie den Anweisungen auf dem Bildschirm.

### <a name="module-test-lint"></a>Modultest: *lint*

1. Wählen Sie auf der Menüleiste **Ansicht > Befehlspalette > Azure Terraform: Test ausführen** aus.

1. Wählen Sie in der Liste der Testtypoptionen **lint** aus.

    ![Auswählen des Testtyps](media/terraform-vscode-extension/tf-select-type-of-test-lint.png)

1. Klicken Sie in der angezeigten Bestätigung auf **OK**, und folgen Sie den Anweisungen auf dem Bildschirm.

    ![Möchten Sie Cloud Shell öffnen?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Wenn Sie den **lint**- oder den **end to end**-Test durchführen, verwendet Azure einen Containerdienst, um einen Testcomputer für den eigentlichen Test bereitzustellen. Deshalb kann es mehrere Minuten dauern, bis Ihre Testergebnisse zurückgegeben werden.

Nach einigen Augenblicken wird eine Liste im Terminalbereich angezeigt, die folgendem Beispiel ähnelt:

![Testergebnisse von „lint“](media/terraform-vscode-extension/tf-lint-test-results.png)

### <a name="module-test-end-to-end"></a>Modultest: *end to end*

1. Wählen Sie auf der Menüleiste **Ansicht > Befehlspalette > Azure Terraform: Test ausführen** aus.

1. Wählen Sie aus der Liste der Testtypoptionen **end to end** aus.

    ![Auswählen des Testtyps](media/terraform-vscode-extension/tf-select-type-of-test-end-to-end.png)

1. Klicken Sie in der angezeigten Bestätigung auf **OK**, und folgen Sie den Anweisungen auf dem Bildschirm.

    ![Möchten Sie Cloud Shell öffnen?](media/terraform-vscode-extension/tf-do-you-want-to-open-cloudshell-small.png)

>[!NOTE]
>Wenn Sie den **lint**- oder den **end to end**-Test durchführen, verwendet Azure einen Containerdienst, um einen Testcomputer für den eigentlichen Test bereitzustellen. Deshalb kann es mehrere Minuten dauern, bis Ihre Testergebnisse zurückgegeben werden.

Nach einigen Augenblicken wird eine Liste im Terminalbereich angezeigt, die folgendem Beispiel ähnelt:

![Testergebnisse von „end to end“](media/terraform-vscode-extension/tf-end-to-end-test-results.png)

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Liste der für Azure (und andere unterstützte Anbieter) verfügbaren Terraform-Module](https://registry.terraform.io/)