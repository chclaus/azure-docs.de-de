---
title: Erstellen eines lokalen virtuellen Netzwerks mit Terraform in Azure
description: In diesem Tutorial erfahren Sie, wie Sie in Azure ein lokales VNET für lokale Ressourcen implementieren.
services: terraform
ms.service: azure
keywords: Terraform, Hub-Spoke, Netzwerke, Hybridnetzwerke, DevOps, virtueller Computer, Azure, VNET-Peering, lokal
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 98c7c2450b4aa828f544ecab4c1e320eb74bab45
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169789"
---
# <a name="tutorial-create-on-premises-virtual-network-with-terraform-in-azure"></a>Tutorial: Erstellen eines lokalen virtuellen Netzwerks mit Terraform in Azure

In diesem Tutorial implementieren Sie ein lokales Netzwerk unter Verwendung eines virtuellen Azure-Netzwerks (VNET). Ein Azure VNET kann durch Ihr eigenes privates virtuelles Netzwerk ersetzt werden. Ordnen Sie hierzu die entsprechenden IP-Adressen in den Subnetzen zu.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Verwenden von HCL (HashiCorp Language), um ein lokales VNET in einer Hub-Spoke-Topologie zu implementieren
> * Erstellen von Ressourcen für Hub-Netzwerkappliances mithilfe von Terraform
> * Erstellen eines lokalen virtuellen Computers mithilfe von Terraform
> * Erstellen eines lokalen VPN-Gateways mithilfe von Terraform

## <a name="prerequisites"></a>Voraussetzungen

1. [Erstellen einer hybriden Hub-Spoke-Netzwerktopologie mit Terraform in Azure](./terraform-hub-spoke-introduction.md)

## <a name="create-the-directory-structure"></a>Erstellen der Verzeichnisstruktur

Erstellen Sie ein virtuelles Azure-Netzwerk, um ein lokales Netzwerk zu simulieren. Das virtuelle Demonetzwerk wird anstelle eines echten privaten lokalen Netzwerks verwendet. Wenn Sie stattdessen Ihr vorhandenes lokales Netzwerk verwenden möchten, ordnen Sie die entsprechenden IP-Adressen in den Subnetzen zu.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Öffnen Sie [Azure Cloud Shell](/azure/cloud-shell/overview). Falls Sie zuvor noch keine Umgebung ausgewählt haben, wählen Sie **Bash** als Umgebung aus.

    ![Cloud Shell-Eingabeaufforderung](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Wechseln Sie in das Verzeichnis `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Wechseln Sie zum neuen Verzeichnis:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-on-premises-vnet"></a>Deklarieren des lokalen VNET

Erstellen Sie die Terraform-Konfigurationsdatei, um ein lokales VNET zu deklarieren.

1. Öffnen Sie in Cloud Shell eine neue Datei namens `on-prem.tf`.

    ```bash
    code on-prem.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

    ```hcl
    locals {
      onprem-location       = "SouthCentralUS"
      onprem-resource-group = "onprem-vnet-rg"
      prefix-onprem         = "onprem"
    }

    resource "azurerm_resource_group" "onprem-vnet-rg" {
      name     = "${local.onprem-resource-group}"
      location = "${local.onprem-location}"
    }

    resource "azurerm_virtual_network" "onprem-vnet" {
      name                = "onprem-vnet"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"
      address_space       = ["192.168.0.0/16"]

      tags {
        environment = "${local.prefix-onprem}"
      }
    }

    resource "azurerm_subnet" "onprem-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = "${azurerm_resource_group.onprem-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.onprem-vnet.name}"
      address_prefix       = "192.168.255.224/27"
    }

    resource "azurerm_subnet" "onprem-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.onprem-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.onprem-vnet.name}"
      address_prefix       = "192.168.1.128/25"
    }

    resource "azurerm_public_ip" "onprem-pip" {
        name                         = "${local.prefix-onprem}-pip"
        location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
        resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"
        allocation_method   = "Dynamic"

        tags {
            environment = "${local.prefix-onprem}"
        }
    }

    resource "azurerm_network_interface" "onprem-nic" {
      name                 = "${local.prefix-onprem}-nic"
      location             = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.onprem-vnet-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-onprem}"
        subnet_id                     = "${azurerm_subnet.onprem-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.onprem-pip.id}"
      }
    }

    # Create Network Security Group and rule
    resource "azurerm_network_security_group" "onprem-nsg" {
        name                = "${local.prefix-onprem}-nsg"
        location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
        resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"

        security_rule {
            name                       = "SSH"
            priority                   = 1001
            direction                  = "Inbound"
            access                     = "Allow"
            protocol                   = "Tcp"
            source_port_range          = "*"
            destination_port_range     = "22"
          source_address_prefix      = "*"
            destination_address_prefix = "*"
        }

        tags {
            environment = "onprem"
        }
    }

    resource "azurerm_subnet_network_security_group_association" "mgmt-nsg-association" {
      subnet_id                 = "${azurerm_subnet.onprem-mgmt.id}"
      network_security_group_id = "${azurerm_network_security_group.onprem-nsg.id}"
    }

    resource "azurerm_virtual_machine" "onprem-vm" {
      name                  = "${local.prefix-onprem}-vm"
      location              = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.onprem-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.onprem-nic.id}"]
      vm_size               = "${var.vmsize}"

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-onprem}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-onprem}"
      }
    }

    resource "azurerm_public_ip" "onprem-vpn-gateway1-pip" {
      name                = "${local.prefix-onprem}-vpn-gateway1-pip"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "onprem-vpn-gateway" {
      name                = "onprem-vpn-gateway1"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = "${azurerm_public_ip.onprem-vpn-gateway1-pip.id}"
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = "${azurerm_subnet.onprem-gateway-subnet.id}"
      }
      depends_on = ["azurerm_public_ip.onprem-vpn-gateway1-pip"]

    }
    ```

1. Speichern Sie die Datei, und beenden Sie den Editor.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines virtuellen Hub-Netzwerks mit Terraform in Azure](./terraform-hub-spoke-hub-network.md)