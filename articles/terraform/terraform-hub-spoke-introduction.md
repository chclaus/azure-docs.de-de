---
title: Erstellen einer hybriden Hub-Spoke-Netzwerktopologie mit Terraform in Azure
description: Das Tutorial veranschaulicht die Erstellung einer vollständigen Referenzarchitektur für ein Hybridnetzwerk in Azure mit Terraform.
services: terraform
ms.service: azure
keywords: Terraform, Hub and Spoke, Netzwerke, Hybridnetzwerke, DevOps, virtueller Computer, Azure, vnet-Peering, virtuelles Netzwerkgerät
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 5c2a61dd9da6d233a4b1410042f2125a1c300758
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173441"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-with-terraform-in-azure"></a>Tutorial: Erstellen einer hybriden Hub-Spoke-Netzwerktopologie mit Terraform in Azure

Diese Tutorialserie zeigt, wie Sie mit Terraform in Azure eine [Hub-Spoke-Netzwerktopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) implementieren können. 

Eine Hub-Spoke-Topologie ist eine Möglichkeit zur Isolierung von Workloads bei der gemeinsamen Nutzung von allgemeinen Diensten. Zu diesen Diensten gehören Identität und Sicherheit. Bei einem Hub handelt es sich um ein virtuelles Netzwerk (VNet), das als zentraler Verbindungspunkt für Ihr lokales Netzwerk fungiert. Bei den Spokes handelt es sich um VNETs, die eine Peeringverbindung mit dem Hub herstellen. Gemeinsame Dienste werden im Hub bereitgestellt, während einzelne Workloads in Spoke-Netzwerken bereitgestellt werden.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Verwenden von HCL (HashiCorp Language) zum Implementieren von Ressourcen für die Referenzarchitektur für das Hub-Spoke-Hybridnetzwerk
> * Verwenden von Terraform zum Erstellen von Ressourcen für Hubnetzwerkgerät
> * Verwenden von Terraform zum Erstellen eines Hubnetzwerks in Azure als gemeinsamer Ausgangspunkt für alle Ressourcen
> * Verwenden von Terraform zum Erstellen einzelner Workloads als Spoke-VNets in Azure
> * Verwenden von Terraform zum Erstellen von Gateways und Verbindungen zwischen dem Standort und Azure-Netzwerken
> * Verwenden von Terraform zum Erstellen von VNet-Peerings zu Spoke-Netzwerken

## <a name="prerequisites"></a>Voraussetzungen

- **Azure-Abonnement**: Falls Sie noch kein Azure-Abonnement haben, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

- **Installieren und konfigurieren Sie Terraform**: Um VMs und anderen Infrastrukturen in Azure bereitzustellen, müssen Sie [Terraform installieren und konfigurieren](/azure/virtual-machines/linux/terraform-install-configure).

## <a name="hub-and-spoke-topology-architecture"></a>Architektur für Hub-Spoke-Topologie

In der Hub-Spoke-Topologie dient der Hub als VNet. Das VNet fungiert als ein zentraler Verbindungspunkt für Ihr lokales Netzwerk. Bei Speichen handelt es sich um VNETs, die eine Peerverbindung mit dem Hub herstellen und zur Isolierung von Workloads verwendet werden können. Der Datenverkehr wird über eine ExpressRoute- oder VPN-Gatewayverbindung zwischen dem lokalen Rechenzentrum und dem Hub weitergeleitet. Die folgende Abbildung veranschaulicht die Komponenten in einer Hub-Spoke-Topologie:

![Architektur für Hub-Spoke-Topologie in Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Vorteile einer Hub-Spoke-Topologie

Eine Hub-Spoke-Netzwerktopologie ist eine Möglichkeit zur Isolierung von Workloads bei der gemeinsamen Nutzung von allgemeinen Diensten. Zu diesen Diensten gehören Identität und Sicherheit. Bei einem Hub handelt es sich um ein VNet, das als zentraler Verbindungspunkt für Ihr lokales Netzwerk fungiert. Bei den Spokes handelt es sich um VNETs, die eine Peeringverbindung mit dem Hub herstellen. Gemeinsame Dienste werden im Hub bereitgestellt, während einzelne Workloads in Spoke-Netzwerken bereitgestellt werden. Hier sind einige Vorteile der Hub-Spoke-Netzwerktopologie:

- **Kosteneinsparungen** durch die Zentralisierung von Diensten an einem einzigen Standort, der von mehreren Workloads gemeinsam genutzt werden kann. Diese Workloads enthalten virtuelle Netzwerkgeräte und den DNS-Server.
- **Umgehung von Abonnementbeschränkungen** durch die Herstellung von Peeringverbindungen zwischen VNETs verschiedener Abonnements und dem zentralen Hub
- **Trennung der Belange** zwischen zentralen IT-Vorgängen (SecOps, InfraOps) und Workloads (DevOps)

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Typische Verwendungen für die Hub-Spoke-Architektur

Einige der typischen Verwendungen für eine Hub-Spoke-Architektur sind:

- Viele Kunden verfügen über Workloads, die in verschiedenen Umgebungen bereitgestellt sind. Diese Umgebungen umfassen Entwicklung, Tests und Produktion. In vielen Fällen müssen diese Workloads Dienste wie DNS, IDS, NTP oder AD DS gemeinsam nutzen. Diese gemeinsamen Dienste können im Hub-VNet platziert werden. Auf diese Weise wird jede Umgebung für einen Spoke bereitgestellt, um die Isolierung aufrechtzuerhalten.
- Workloads, bei denen keine Konnektivität untereinander bestehen muss, die jedoch Zugriff auf gemeinsame Dienste erfordern
- Unternehmen, die eine zentrale Steuerung von Sicherheitsaspekten benötigen.
- Unternehmen, die Workloads in jedem Spoke getrennt verwalten müssen.

## <a name="preview-the-demo-components"></a>Vorschau der Demokomponenten

Während Sie jedes Tutorial in dieser Serie durcharbeiten, werden verschiedene Komponenten in verschiedenen Terraform-Skripten definiert. Die erstellte und bereitgestellte Demoarchitektur besteht aus den folgenden Komponenten:

- **Lokales Netzwerk**. Ein in einer Organisation betriebenes privates lokales Netzwerk. Für die Hub-Spoke-Referenzarchitektur wird ein VNet in Azure verwendet, um ein lokales Netzwerk zu simulieren.

- **VPN-Gerät**: Ein VPN-Gerät oder ein Dienst, das bzw. der externe Konnektivität mit dem lokalen Netzwerk bereitstellt. Das VPN-Gerät kann ein Hardwaregerät oder eine Softwarelösung sein. 

- **Hub-VNET**: Der Hub ist ein zentraler Verbindungspunkt für Ihr lokales Netzwerk und der Ort zum Hosten von Diensten. Diese Dienste können von den verschiedenen Workloads in den Spoke-VNets genutzt werden.

- **Gatewaysubnetz**. Die VNet-Gateways befinden sich in demselben Subnetz.

- **Spoke-VNETs**: Spokes können verwendet werden, um Workloads in ihren eigenen VNETs, die getrennt von anderen Spokes verwaltet werden, zu isolieren. Jede Workload kann mehrere Schichten umfassen, wobei mehrere Subnetze über Azure Load Balancer verbunden sind. 

- **VNET-Peering**: Zwei VNETs können über eine Peeringverbindung miteinander verbunden werden. Peeringverbindungen sind nicht-transitive Verbindungen zwischen VNETs mit niedrigen Latenzen. Sobald eine Peeringverbindung hergestellt wurde, tauschen die VNETs ohne Einsatz eines Routers Datenverkehr über den Azure-Backbone aus. In einer Hub-Spoke-Netzwerktopologie wird durch VNET-Peering eine Verbindung zwischen dem Hub und den einzelnen Spokes hergestellt. Sie können VNets in derselben Region oder in verschiedenen Regionen per Peering verknüpfen.

## <a name="create-the-directory-structure"></a>Erstellen der Verzeichnisstruktur

Erstellen Sie das Verzeichnis, das Ihre Terraform-Konfigurationsdateien für die Demo enthält.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Öffnen Sie [Azure Cloud Shell](/azure/cloud-shell/overview). Falls Sie zuvor noch keine Umgebung ausgewählt haben, wählen Sie **Bash** als Umgebung aus.

    ![Cloud Shell-Eingabeaufforderung](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Wechseln Sie in das Verzeichnis `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Erstellen Sie ein Verzeichnis namens `hub-spoke`.

    ```bash
    mkdir hub-spoke
    ```

1. Wechseln Sie zum neuen Verzeichnis:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>Deklarieren des Azure-Anbieters

Erstellen Sie die Terraform-Konfigurationsdatei, die den Azure-Anbieter deklariert.

1. Öffnen Sie in Cloud Shell eine neue Datei namens `main.tf`.

    ```bash
    code main.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Speichern Sie die Datei, und beenden Sie den Editor.

## <a name="create-the-variables-file"></a>Erstellen der Variablendatei

Erstellen Sie die Terraform-Konfigurationsdatei für allgemeine Variablen, die in verschiedenen Skripten verwendet werden.

1. Öffnen Sie in Cloud Shell eine neue Datei namens `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Fügen Sie den folgenden Code in den Editor ein:

    ```hcl
    variable "location" {
      description = "Location of the network"
      default     = "centralus"
    }
    
    variable "username" {
      description = "Username for Virtual Machines"
      default     = "testadmin"
    }
    
    variable "password" {
      description = "Password for Virtual Machines"
      default     = "Password1234!"
    }
    
    variable "vmsize" {
      description = "Size of the VMs"
      default     = "Standard_DS1_v2"
    }
    ```

1. Speichern Sie die Datei, und beenden Sie den Editor.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"] 
> [Erstellen eines lokalen virtuellen Netzwerks mit Terraform in Azure](./terraform-hub-spoke-on-prem.md)
