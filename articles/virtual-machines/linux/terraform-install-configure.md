---
title: Installieren und Konfigurieren von Terraform, um Azure-Ressourcen bereitzustellen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Terraform zum Erstellen von Azure-Ressourcen installieren und konfigurieren
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tomarchermsft
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: cd3c8d7d862788f626356b4cfcdccccca36227b3
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168739"
---
# <a name="install-and-configure-terraform-to-provision-azure-resources"></a>Installieren und Konfigurieren von Terraform, um Azure-Ressourcen bereitzustellen
 
Terraform bietet eine einfache Möglichkeit, Cloudinfrastruktur mit der [einfachen Vorlagensprache](https://www.terraform.io/docs/configuration/syntax.html) zu definieren, eine Vorschau anzuzeigen und bereitzustellen. In diesem Artikel werden die notwendigen Schritte zum Bereitstellen von Ressourcen in Azure mithilfe von Terraform beschrieben.

Weitere Informationen zur Verwendung von Terraform mit Azure finden Sie im [Terraform-Hub](/azure/terraform).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Terraform wird standardmäßig in [Cloud Shell](/azure/terraform/terraform-cloud-shell) installiert. Wenn Sie Terraform lokal installieren, führen Sie den nächsten Schritt aus. Andernfalls fahren Sie mit [Einrichten des Terraform-Zugriffs auf Azure](#set-up-terraform-access-to-azure) fort.

## <a name="install-terraform"></a>Installieren von Terraform

[Laden](https://www.terraform.io/downloads.html) Sie zum Installieren von Terraform das entsprechende Paket für Ihr Betriebssystem in ein separates Installationsverzeichnis herunter. Der Download enthält eine einzelne ausführbare Datei, für die Sie auch einen globalen Pfad definieren müssen. [Auf dieser Webseite](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux) finden Sie Anweisungen für das Einrichten des Pfads unter Linux und Macintosh. [Auf dieser Webseite](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows) finden Sie Anweisungen für das Einrichten des Pfads unter Windows.

Überprüfen Sie Ihre Pfadkonfiguration mit dem Befehl `terraform`. Es wird eine Liste der verfügbaren Terraform-Optionen angezeigt, wie in der folgenden Beispielausgabe veranschaulicht:

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Einrichten des Terraform-Zugriffs auf Azure

Erstellen Sie einen [Azure AD-Dienstprinzipal](/cli/azure/create-an-azure-service-principal-azure-cli), damit Terraform Ressourcen in Azure bereitstellen kann. Der Dienstprinzipal gewährt Ihren Terraform-Skripts Die Berechtigung, in Ihrem Azure-Abonnement Ressourcen bereitstellen zu können.

Wenn Sie über mehrere Azure-Abonnements verfügen, fragen Sie zuerst Ihr Konto mit [az account list](/cli/azure/account#az-account-list) ab, um eine Liste der Abonnement-ID- und Mandanten-ID-Werte zu erhalten:

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

Um ein ausgewähltes Abonnement zu verwenden, legen Sie das Abonnement für diese Sitzung mit [az account set](/cli/azure/account#az-account-set) fest. Legen Sie die `SUBSCRIPTION_ID`-Umgebungsvariable so fest, dass sie den Wert des zurückgegebenen `id`-Felds für das gewünschte Abonnement enthält:

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Jetzt können Sie einen Dienstprinzipal für die Verwendung mit Terraform erstellen. Verwenden Sie [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac), und legen Sie den *Bereich* wie folgt auf Ihr Abonnement fest:

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

Die Werte für `appId`, `password`, `sp_name` und `tenant` werden zurückgegeben. Notieren Sie sich `appId` und `password`.

## <a name="configure-terraform-environment-variables"></a>Konfigurieren der Terraform-Umgebungsvariablen

Um Terraform für die Verwendung Ihres Azure AD-Dienstprinzipals zu konfigurieren, legen Sie die folgenden Umgebungsvariablen fest, die dann von den [Azure-Terraform-Modulen](https://registry.terraform.io/modules/Azure) verwendet werden. Sie können die Umgebung auch festlegen, wenn Sie mit einer anderen als der öffentlichen Azure-Cloud arbeiten.

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

Sie können das folgende Beispiel-Shell-Skript dazu verwenden, diese Variablen festzulegen:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>Ausführen eines Beispielskripts

Erstellen Sie die Datei `test.tf` in einem leeren Verzeichnis, und fügen Sie das folgende Skript ein.

```hcl
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Speichern Sie die Datei, und initialisieren Sie die Terraform-Bereitstellung. Mit diesem Schritt werden die Azure-Module heruntergeladen, die zum Erstellen einer Azure-Ressourcengruppe erforderlich sind.

```bash
terraform init
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Sie können mit `terraform plan` eine Vorschau der Aktionen anzeigen, die vom Terraform-Skript ausgeführt werden sollen. Wenn Sie die Ressourcengruppe erstellen, wenden Sie Ihren Terraform-Plan folgendermaßen an:

```bash
terraform apply
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```console
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Terraform installiert oder die Cloud Shell verwendet, um Azure-Anmeldeinformationen zu konfigurieren und mit dem Erstellen von Ressourcen in Ihrem Azure-Abonnement zu beginnen. Informationen zum Erstellen einer umfassenderen Terraform-Bereitstellung in Azure finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Erstellen eines virtuellen Azure-Computers mit Terraform](terraform-create-complete-vm.md)
