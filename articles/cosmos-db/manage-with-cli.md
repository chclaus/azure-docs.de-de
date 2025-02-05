---
title: Verwalten von Azure Cosmos DB-Ressourcen mit der Azure CLI
description: Verwenden Sie die Azure CLI zum Verwalten Ihrer Konten, Datenbanken und Containern für Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mjbrown
ms.openlocfilehash: 59f1a678b7a2edc64a2079eff0e819e206c2e509
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71811702"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Verwalten von Azure Cosmos-Ressourcen mit der Azure CLI

Der folgende Leitfaden erläutert die gängigen Befehle zum Automatisieren der Verwaltung Ihrer Azure Cosmos DB-Konten und -Container mithilfe von Azure CLI. Referenzseiten für alle CLI-Befehle für Azure Cosmos DB sind in der [Referenz zur Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb) verfügbar. Weitere Beispiele finden Sie auch in [Azur CLI-Beispiele für Azure Cosmos DB](cli-samples.md), einschließlich der Erstellung und Verwaltung von Cosmos DB-Konten, Datenbanken und Containern für MongoDB, Gremlin, Cassandra und die Tabellen-API.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Thema die Azure CLI in Version 2.0 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Erstellen eines Azure Cosmos DB-Kontos

Erstellen Sie ein Azure Cosmos DB-Konto mit SQL-API und Sitzungskonsistenz für die Regionen „USA, Westen 2“ und „USA, Osten 2“:

> [!IMPORTANT]
> Der Name des Azure Cosmos-Kontos muss in Kleinbuchstaben geschrieben sein und darf maximal 31 Zeichen umfassen.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 31 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

## <a name="add-or-remove-regions"></a>Hinzufügen oder Entfernen von Regionen

Erstellen Sie ein Azure Cosmos-Konto mit zwei Regionen, fügen Sie eine Region hinzu, und entfernen Sie eine Region.

> [!NOTE]
> Sie können nicht gleichzeitig Regionselemente vom Typ `locations` hinzufügen oder entfernen und andere Eigenschaften für ein Azure Cosmos-Konto ändern. Die Anpassung von Regionen muss getrennt von anderen Änderungen an der Kontoressource vorgenommen werden.
> [!NOTE]
> Mit diesem Befehl können Sie Regionen hinzufügen und entfernen, aber weder Failoverprioritäten anpassen noch ein manuelles Failover auslösen. Weitere Informationen finden Sie unter [Festlegen der Failoverpriorität](#set-failover-priority) und [Auslösen eines manuellen Failovers](#trigger-manual-failover).

```azurecli-interactive
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount' # must be lower case and <31 characters

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName= "South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False
```

## <a name="enable-multiple-write-regions"></a>Aktivieren mehrerer Schreibregionen

Aktivieren von Multimaster für ein Cosmos-Konto

```azurecli-interactive
# Update an Azure Cosmos account from single to multi-master
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

## <a name="set-failover-priority"></a>Festlegen der Failoverpriorität

Festlegen der Failoverpriorität für ein Azure Cosmos-Konto, das für automatische Failover konfiguriert ist

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2'=0 'South Central US'=1 'East US 2'=2
```

## <a name="enable-automatic-failover"></a>Aktivieren des automatischen Failovers

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

## <a name="trigger-manual-failover"></a>Auslösen eines manuellen Failovers

> [!CAUTION]
> Wenn die Priorität einer Region in „0“ geändert wird, wird ein manuelles Failover für ein Azure Cosmos-Konto ausgelöst. Bei anderen Prioritätsänderungen wird kein Failover ausgelöst.

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2'=0 'South Central US'=1 'West US 2'=2
```

## <a id="list-account-keys"></a> Auflisten aller Kontoschlüssel

Rufen Sie alle Schlüssel für ein Cosmos-Konto ab.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

## <a name="list-read-only-account-keys"></a>Auflisten schreibgeschützter Kontoschlüssel

Rufen Sie schreibgeschützte Schlüssel für ein Cosmos-Konto ab.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb list-read-only-keys \
   -n $accountName \
   -g $resourceGroupName
```

## <a name="list-connection-strings"></a>Auflisten von Verbindungszeichenfolgen

Rufen Sie die Verbindungszeichenfolgen für ein Cosmos-Konto ab.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb list-connection-strings \
    -n $accountName \
    -g $resourceGroupName
```

## <a name="regenerate-account-key"></a>Erneutes Generieren eines Kontoschlüssels

Generieren Sie einen neuen Schlüssel für ein Cosmos-Konto erneut.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb regenerate-key \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="create-a-database"></a>Erstellen einer Datenbank

Erstellen Sie eine Cosmos-Datenbank.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

## <a name="create-a-database-with-shared-throughput"></a>Erstellen einer Datenbank mit gemeinsam genutztem Durchsatz

Erstellen Sie eine Cosmos-Datenbank mit gemeinsam genutztem Durchsatz.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

## <a name="change-the-throughput-of-a-database"></a>Ändern des Durchsatzes einer Datenbank

Erhöhen Sie den Durchsatz einer Cosmos-Datenbank um 1.000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
increaseRU=1000

originalRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query throughput -o tsv)

newRU=$((originalRU + increaseRU))

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

## <a name="create-a-container"></a>Erstellen eines Containers

Erstellen Sie einen Cosmos-Container mit standardmäßiger Indexrichtlinie, Partitionsschlüssel und einem Durchsatz von 400 RU/s.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

## <a name="create-a-container-with-ttl"></a>Erstellen eines Containers mit Gültigkeitsdauer

Erstellen Sie einen Cosmos-Container mit aktivierter Gültigkeitsdauer.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'
databaseName = 'database1'
containerName = 'container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl = 86400
```

## <a name="create-a-container-with-a-custom-index-policy"></a>Erstellen eines Containers mit benutzerdefinierter Indexrichtlinie

Erstellen Sie einen Cosmos-Container mit einer benutzerdefinierten Indexrichtlinie, einem räumlichen Index, einem zusammengesetzten Index, einem Partitionsschlüssel und einem Durchsatz von 400 RU/s.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

## <a name="change-the-throughput-of-a-container"></a>Ändern des Durchsatzes eines Containers

Erhöhen Sie den Durchsatz eines Cosmos-Containers um 1.000 RU/s.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
increaseRU=1000

originalRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query throughput -o tsv)

newRU=$((originalRU + increaseRU))

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure CLI finden Sie in:

- [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)
- [Azure-CLI-Referenz](https://docs.microsoft.com/cli/azure/cosmosdb)
- [Zusätzliche Azure CLI-Beispiele für Azure Cosmos DB](cli-samples.md)
