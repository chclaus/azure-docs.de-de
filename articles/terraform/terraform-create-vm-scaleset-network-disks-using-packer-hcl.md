---
title: Verwenden von Terraform zum Erstellen einer Azure-VM-Skalierungsgruppe aus einem benutzerdefinierten Packer-Image
description: Verwenden Sie Terraform, um eine Azure-VM-Skalierungsgruppe aus einem benutzerdefinierten Packer-Image zu konfigurieren und mit einer Version zu versehen – einschließlich eines virtuellen Netzwerks und verwalteter angefügter Datenträger.
services: terraform
ms.service: azure
keywords: Terraform, DevOps, Skalierungsgruppe, virtueller Computer, Netzwerk, Speicher, Module, benutzerdefinierte Images, Packer
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 6feeab9b48715a8fe1f6c6fe11ae90b6be71a57a
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173484"
---
# <a name="use-terraform-to-create-an-azure-virtual-machine-scale-set-from-a-packer-custom-image"></a>Verwenden von Terraform zum Erstellen einer Azure-VM-Skalierungsgruppe aus einem benutzerdefinierten Packer-Image

In diesem Artikel verwenden Sie [Terraform](https://www.terraform.io/), um eine [Azure-VM-Skalierungsgruppe](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview), die mit einem mit [Packer](https://www.packer.io/intro/index.html) hergestellten benutzerdefinierten Image mit verwalteten Datenträgern unter Verwendung der [HashiCorp Configuration Language](https://www.terraform.io/docs/configuration/syntax.html) (HCL) erstellt wurde, zu erstellen und bereitzustellen.  

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten Ihrer Terraform-Bereitstellung
> * Verwenden von Variablen und Ausgaben für die Terraform-Bereitstellung 
> * Erstellen und Bereitstellen einer Netzwerkinfrastruktur
> * Erstellen eines benutzerdefinierten VM-Images mit Packer
> * Erstellen und Bereitstellen einer VM-Skalierungsgruppe mit dem benutzerdefinierten Image
> * Erstellen und Bereitstellen einer Jumpbox 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="before-you-begin"></a>Voraussetzungen
> * [Installieren Sie Terraform, und konfigurieren Sie den Zugriff auf Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/terraform-install-configure)
> * [Erstellen Sie ein SSH-Schlüsselpaar](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys), sofern Sie noch keines besitzen.
> * [Installieren Sie Packer](https://www.packer.io/docs/install/index.html), wenn Packer noch nicht auf dem lokalen Computer installiert ist.


## <a name="create-the-file-structure"></a>Erstellen der Dateistruktur

Erstellen Sie in einem leeren Verzeichnis drei neue Dateien mit folgenden Namen:

- `variables.tf` : Diese Datei enthält die Werte der in der Vorlage verwendeten Variablen.
- `output.tf` : Diese Datei beschreibt die Einstellungen, die nach der Bereitstellung angezeigt werden.
- `vmss.tf` : Diese Datei enthält den Code der Infrastruktur, die Sie bereitstellen.

##  <a name="create-the-variables"></a>Erstellen der Variablen 

In diesem Schritt definieren Sie Variablen zur Anpassung der durch Terraform erstellten Ressourcen.

Bearbeiten Sie die Datei `variables.tf`, kopieren Sie den folgenden Code, und speichern Sie die Änderungen.

```hcl
variable "location" {
  description = "The location where resources are created"
  default     = "East US"
}

variable "resource_group_name" {
  description = "The name of the resource group in which the resources are created"
  default     = ""
}

```

> [!NOTE]
> Da für die Variable resource_group_name kein Standardwert festgelegt ist, definieren Sie einen eigenen Wert.

Speichern Sie die Datei .

Wenn Sie Ihre Terraform-Vorlage bereitstellen, benötigen Sie den vollqualifizierten Domänennamen, der zum Zugreifen auf die Anwendung verwendet wird. Verwenden Sie den Ressourcentyp `output` von Terraform, und rufen Sie die `fqdn`-Eigenschaft der Ressource ab. 

Bearbeiten Sie die Datei `output.tf`, und kopieren Sie den folgenden Code, um den vollqualifizierten Domänennamen für die virtuellen Computer verfügbar zu machen. 

```hcl 
output "vmss_public_ip" {
    value = "${azurerm_public_ip.vmss.fqdn}"
}
```

## <a name="define-the-network-infrastructure-in-a-template"></a>Definieren der Netzwerkinfrastruktur in einer Vorlage 

In diesem Schritt erstellen Sie in einer neuen Azure-Ressourcengruppe die folgende Netzwerkinfrastruktur: 
  - Ein VNet mit dem Adressraum 10.0.0.0/16 
  - Ein Subnetz mit dem Adressraum 10.0.2.0/24
  - Zwei öffentliche IP-Adressen: eine für den Lastenausgleich der VM-Skalierungsgruppe, eine für die Verbindungsherstellung mit der SSH-Jumpbox

Darüber hinaus benötigen Sie eine Ressourcengruppe, in der alle Ressourcen erstellt werden. 

Bearbeiten und kopieren Sie den folgenden Code in der Datei `vmss.tf`: 

```hcl

resource "azurerm_resource_group" "vmss" {
  name     = "${var.resource_group_name}"
  location = "${var.location}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_network" "vmss" {
  name                = "vmss-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_subnet" "vmss" {
  name                 = "vmss-subnet"
  resource_group_name  = "${azurerm_resource_group.vmss.name}"
  virtual_network_name = "${azurerm_virtual_network.vmss.name}"
  address_prefix       = "10.0.2.0/24"
}

resource "azurerm_public_ip" "vmss" {
  name                         = "vmss-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  allocation_method            = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}"

  tags {
    environment = "codelab"
  }
}

``` 

> [!NOTE]
> Es wird empfohlen, die in Azure bereitgestellten Ressourcen zu kennzeichnen, um sie später einfacher identifizieren zu können.

## <a name="create-the-network-infrastructure"></a>Erstellen der Netzwerkinfrastruktur

Initialisieren Sie die Terraform-Umgebung, indem Sie in dem Verzeichnis, in dem Sie die Dateien vom Typ `.tf` erstellt haben, den folgenden Befehl ausführen:

```bash
terraform init 
```
 
Die Anbieter-Plug-Ins werden von der Terraform-Registrierung in den Ordner `.terraform` in dem Verzeichnis, in dem Sie den Befehl ausgeführt haben, heruntergeladen.

Führen Sie den folgenden Befehl aus, um die Infrastruktur in Azure bereitzustellen.

```bash
terraform apply
```

Stellen Sie sicher, dass der vollqualifizierte Domänenname der öffentlichen IP-Adresse Ihrer Konfiguration entspricht.

![Vollqualifizierter Terraform-Domänennamen der VM-Skalierungsgruppe für öffentliche IP-Adresse](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-fqdn.png)

Die Ressourcengruppe enthält die folgenden Ressourcen:

![Terraform-Netzwerkressourcen der VM-Skalierungsgruppe](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step4-rg.png)


## <a name="create-an-azure-image-using-packer"></a>Erstellen eines Azure-Images mit Packer
Erstellen Sie ein benutzerdefiniertes Linux-Image anhand der im Tutorial [Erstellen von Images von virtuellen Linux-Computern in Azure mit Packer](https://docs.microsoft.com/azure/virtual-machines/linux/build-image-with-packer) beschriebenen Schritte.
 
Führen Sie das Tutorial zum Erstellen eines Ubuntu-Images mit installiertem nginx mit aufgehobener Bereitstellung durch.

![Nachdem Sie die Packer-Image erstellt haben, verfügen Sie über ein Image.](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/packerimagecreated.png)

> [!NOTE]
> Im Rahmen dieses Tutorials wird im Packer-Image ein Befehl zum Installieren von nginx ausgeführt. Sie können auch bei der Erstellung Ihr eigenes Skript ausführen.

## <a name="edit-the-infrastructure-to-add-the-virtual-machine-scale-set"></a>Bearbeiten Sie die Infrastruktur, um die VM-Skalierungsgruppe hinzuzufügen.

In diesem Schritt erstellen Sie die folgenden Ressourcen im Netzwerk, das zuvor bereitgestellt wurde:
- Einen Azure-Lastenausgleich, um die Anwendung zu versorgen und an die zuvor bereitgestellte öffentliche IP-Adresse anzufügen
- Einen Azure-Lastenausgleich und Regeln, um die Anwendung zu versorgen und an die zuvor konfigurierte öffentliche IP-Adresse anzufügen.
- Einen Azure-Back-End-Adresspool, der dem Lastenausgleich zugewiesen werden muss
- Einen Integritätstestport, der von der Anwendung verwendet und für den Lastenausgleich konfiguriert wird
- Eine VM-Skalierungsgruppe, die sich hinter dem Lastenausgleich befindet und in dem zuvor bereitgestellten VNET ausgeführt wird
- Über das benutzerdefinierte Image installiertes [nginx](https://nginx.org/) auf den Knoten der VM-Skalierungsgruppe


Fügen Sie am Ende der Datei `vmss.tf` den folgenden Code hinzu:

```hcl

resource "azurerm_lb" "vmss" {
  name                = "vmss-lb"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = "${azurerm_public_ip.vmss.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_lb_backend_address_pool" "bpepool" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "BackEndAddressPool"
}

resource "azurerm_lb_probe" "vmss" {
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id     = "${azurerm_lb.vmss.id}"
  name                = "ssh-running-probe"
  port                = "${var.application_port}"
}

resource "azurerm_lb_rule" "lbnatrule" {
  resource_group_name            = "${azurerm_resource_group.vmss.name}"
  loadbalancer_id                = "${azurerm_lb.vmss.id}"
  name                           = "http"
  protocol                       = "Tcp"
  frontend_port                  = "${var.application_port}"
  backend_port                   = "${var.application_port}"
  backend_address_pool_id        = "${azurerm_lb_backend_address_pool.bpepool.id}"
  frontend_ip_configuration_name = "PublicIPAddress"
  probe_id                       = "${azurerm_lb_probe.vmss.id}"
}

data "azurerm_resource_group" "image" {
  name = "myResourceGroup"
}

data "azurerm_image" "image" {
  name                = "myPackerImage"
  resource_group_name = "${data.azurerm_resource_group.image.name}"
}

resource "azurerm_virtual_machine_scale_set" "vmss" {
  name                = "vmscaleset"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"
  upgrade_policy_mode = "Manual"

  sku {
    name     = "Standard_DS1_v2"
    tier     = "Standard"
    capacity = 2
  }

  storage_profile_image_reference {
    id="${data.azurerm_image.image.id}"
  }

  storage_profile_os_disk {
    name              = ""
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  storage_profile_data_disk {
    lun          = 0
    caching        = "ReadWrite"
    create_option  = "Empty"
    disk_size_gb   = 10
  }

  os_profile {
    computer_name_prefix = "vmlab"
    admin_username       = "azureuser"
    admin_password       = "Passwword1234"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  network_profile {
    name    = "terraformnetworkprofile"
    primary = true

    ip_configuration {
      name                                   = "IPConfiguration"
      subnet_id                              = "${azurerm_subnet.vmss.id}"
      load_balancer_backend_address_pool_ids = ["${azurerm_lb_backend_address_pool.bpepool.id}"]
      primary = true
    }
  }
  
  tags {
    environment = "codelab"
  }
}

```

Passen Sie die Bereitstellung an, indem Sie `variables.tf` den folgenden Code hinzufügen:

```hcl
variable "application_port" {
    description = "The port that you want to expose to the external load balancer"
    default     = 80
}

variable "admin_password" {
    description = "Default password for admin"
    default = "Passwwoord11223344"
}
``` 


## <a name="deploy-the-virtual-machine-scale-set-in-azure"></a>Bereitstellen der VM-Skalierungsgruppe in Azure

Führen Sie den folgenden Befehl aus, um die Bereitstellung der VM-Skalierungsgruppe zu visualisieren:

```bash
terraform plan
```

Die Ausgabe des Befehls sollte in etwa wie die folgende Abbildung aussehen:

![Terraform-Plan zum Hinzufügen einer VM-Skalierungsgruppe](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-plan.png)

Stellen Sie die zusätzlichen Ressourcen in Azure bereit: 

```bash
terraform apply 
```

Der Inhalt der Ressourcengruppe sieht wie in der folgenden Abbildung aus:

![Terraform-Ressourcengruppe für VM-Skalierungsgruppe](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-vmss-step6-apply.png)

Öffnen Sie einen Browser, und stellen Sie eine Verbindung mit dem vollqualifizierten Domänennamen her, der durch den Befehl zurückgegeben wurde. 


## <a name="add-a-jumpbox-to-the-existing-network"></a>Hinzufügen einer Jumpbox zum vorhandenen Netzwerk 

Mit diesem optionalen Schritt wird SSH-Zugriff auf die Instanzen der VM-Skalierungsgruppe mithilfe einer Jumpbox ermöglicht.

Fügen Sie Ihrer vorhandenen Bereitstellung die folgenden Ressourcen hinzu:
- Eine Netzwerkschnittstelle, die mit demselben Subnetz verbunden ist wie die VM-Skalierungsgruppe
- Einen virtuellen Computer mit dieser Netzwerkschnittstelle

Fügen Sie am Ende der Datei `vmss.tf` den folgenden Code hinzu:

```hcl 
resource "azurerm_public_ip" "jumpbox" {
  name                         = "jumpbox-public-ip"
  location                     = "${var.location}"
  resource_group_name          = "${azurerm_resource_group.vmss.name}"
  allocation_method            = "static"
  domain_name_label            = "${azurerm_resource_group.vmss.name}-ssh"

  tags {
    environment = "codelab"
  }
}

resource "azurerm_network_interface" "jumpbox" {
  name                = "jumpbox-nic"
  location            = "${var.location}"
  resource_group_name = "${azurerm_resource_group.vmss.name}"

  ip_configuration {
    name                          = "IPConfiguration"
    subnet_id                     = "${azurerm_subnet.vmss.id}"
    private_ip_address_allocation = "dynamic"
    public_ip_address_id          = "${azurerm_public_ip.jumpbox.id}"
  }

  tags {
    environment = "codelab"
  }
}

resource "azurerm_virtual_machine" "jumpbox" {
  name                  = "jumpbox"
  location              = "${var.location}"
  resource_group_name   = "${azurerm_resource_group.vmss.name}"
  network_interface_ids = ["${azurerm_network_interface.jumpbox.id}"]
  vm_size               = "Standard_DS1_v2"

  storage_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "16.04-LTS"
    version   = "latest"
  }

  storage_os_disk {
    name              = "jumpbox-osdisk"
    caching           = "ReadWrite"
    create_option     = "FromImage"
    managed_disk_type = "Standard_LRS"
  }

  os_profile {
    computer_name  = "jumpbox"
    admin_username = "azureuser"
    admin_password = "Password1234!"
  }

  os_profile_linux_config {
    disable_password_authentication = true

    ssh_keys {
      path     = "/home/azureuser/.ssh/authorized_keys"
      key_data = "${file("~/.ssh/id_rsa.pub")}"
    }
  }

  tags {
    environment = "codelab"
  }
}
```

Bearbeiten Sie `outputs.tf`, und fügen Sie den folgenden Code hinzu, der nach Abschluss der Bereitstellung den Hostnamen der Jumpbox anzeigt:

```
output "jumpbox_public_ip" {
    value = "${azurerm_public_ip.jumpbox.fqdn}"
}
```

## <a name="deploy-the-jumpbox"></a>Bereitstellen der Jumpbox

Stellen Sie die Jumpbox bereit.

```bash
terraform apply 
```

Nach Abschluss der Bereitstellung sieht der Inhalt der Ressourcengruppe wie die folgende Abbildung aus:

![Terraform-Ressourcengruppe für VM-Skalierungsgruppe](./media/terraform-create-vm-scaleset-network-disks-using-packer-hcl/tf-create-create-vmss-step8.png)

> [!NOTE]
> Die Anmeldung mit einem Kennwort ist für die bereitgestellte Jumpbox und für die bereitgestellte VM-Skalierungsgruppe deaktiviert. Melden Sie sich mit SSH an, um auf die virtuellen Computer zuzugreifen.

## <a name="clean-up-the-environment"></a>Bereinigen der Umgebung

Die folgenden Befehle löschen die in diesem Tutorial erstellten Ressourcen:

```bash
terraform destroy
```

Geben Sie `yes` ein, wenn Sie dazu aufgefordert werden, das Löschen der Ressourcen zu bestätigen. Die Bereinigung kann einige Minuten dauern.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie mithilfe von Terraform eine VM-Skalierungsgruppe und eine Jumpbox in Azure bereitgestellt. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Initialisieren Ihrer Terraform-Bereitstellung
> * Verwenden von Variablen und Ausgaben für die Terraform-Bereitstellung 
> * Erstellen und Bereitstellen einer Netzwerkinfrastruktur
> * Erstellen eines benutzerdefinierten VM-Images mit Packer
> * Erstellen und Bereitstellen einer VM-Skalierungsgruppe mit dem benutzerdefinierten Image
> * Erstellen und Bereitstellen einer Jumpbox 