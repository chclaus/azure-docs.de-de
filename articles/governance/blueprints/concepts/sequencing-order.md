---
title: Verstehen der Bereitstellungsreihenfolge
description: Erfahren Sie mehr über den Lebenszyklus einer Blaupausendefinition und die einzelnen Phasen.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: bda7a6caea931a993a6ddd6731688792bf0b3948
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981017"
---
# <a name="understand-the-deployment-sequence-in-azure-blueprints"></a>Verstehen der Bereitstellungsreihenfolge in Azure Blueprint

Azure Blueprints bestimmt mit einer **Reihenfolge**, die Abfolge der Ressourcenerstellung beim Verarbeiten der Zuweisung einer Blaupausendefinition. In diesem Artikel werden die folgenden Konzepte erläutert:

- Verwendete Standardreihenfolge
- Vorgehensweise zum Anpassen der Reihenfolge
- Verarbeitung der angepassten Reihenfolge

In den JSON-Beispielen gibt es Variablen, die Sie durch Ihre eigenen Werte ersetzen müssen:

- Ersetzen Sie `{YourMG}` durch den Namen Ihrer Verwaltungsgruppe.

## <a name="default-sequencing-order"></a>Standardreihenfolge

Wenn die Blaupausendefinition keine Anweisung zum Bereitstellen von Artefakten enthält oder gleich NULL ist, wird die folgende Reihenfolge verwendet:

- **Rollenzuweisungsartefakte** auf Abonnementebene werden nach Artefaktnamen sortiert
- **Richtlinienzuweisungsartefakte** auf Abonnementebene werden nach Artefaktnamen sortiert
- **Azure Resource Manager-Vorlagenartefakte** auf Abonnementebene werden nach Artefaktnamen sortiert
- **Ressourcengruppenartefakte** (und untergeordnete Artefakte) werden nach Platzhalternamen sortiert

In jedem Artefakt vom Typ **Ressourcengruppe** wird für Artefakte, die innerhalb dieser Ressourcengruppe erstellt werden, die folgende Reihenfolge verwendet:

- Einer Ressourcengruppe untergeordnete **Rollenzuweisungsartefakte** werden nach Artefaktnamen sortiert
- Einer Ressourcengruppe untergeordnete **Richtlinienzuweisungsartefakte** werden nach Artefaktnamen sortiert
- Einer Ressourcengruppe untergeordnete **Azure Resource Manager-Vorlagenartefakte** werden nach Artefaktnamen sortiert

> [!NOTE]
> Die Verwendung von [artifacts()](../reference/blueprint-functions.md#artifacts) erzeugt eine implizite Abhängigkeit von dem betreffenden Artefakt.

## <a name="customizing-the-sequencing-order"></a>Anpassen der Reihenfolge

Beim Erstellen großer Blaupausendefinitionen müssen Ressourcen ggf. in einer bestimmten Reihenfolge erstellt werden. Im gängigsten Muster dieses Szenarios enthält eine Blaupausendefinition mehrere Azure Resource Manager-Vorlagen. Für diesen Fall ermöglicht Blueprints das Definieren der Reihenfolge.

Zum Angeben der Reihenfolge wird im JSON-Code eine Eigenschaft vom Typ `dependsOn` definiert. Die Blaupausendefinition für Ressourcengruppen und Artefaktobjekte unterstützen diese Eigenschaft. `dependsOn` ist ein Zeichenfolgenarray von Artefaktnamen, das das jeweilige Artefakt im Vorfeld für seine Erstellung benötigt.

> [!NOTE]
> Beim Erstellen von Blaupausenobjekten erhält jede Artefaktressource ihren Namen vom Dateinamen (bei Verwendung von [PowerShell](/powershell/module/az.blueprint/new-azblueprintartifact)) oder dem URL-Endpunkt (bei Verwendung der [REST-API](/rest/api/blueprints/artifacts/createorupdate)).
> _resourceGroup_-Verweise in Artefakten müssen mit denen in der Blaupausendefinition übereinstimmen.

### <a name="example---ordered-resource-group"></a>Beispiel: Sortierte Ressourcengruppe

Diese exemplarische Blaupausendefinition verfügt über eine Ressourcengruppe mit benutzerdefinierter Reihenfolge (mittels Deklarierung eines Werts für `dependsOn`) sowie über eine Standardressourcengruppe. In diesem Fall wird das Artefakt namens **assignPolicyTags** vor der Ressourcengruppe **ordered-rg** verarbeitet.
**standard-rg** wird gemäß der Standardreihenfolge verarbeitet.

```json
{
    "properties": {
        "description": "Example blueprint with custom sequencing order",
        "resourceGroups": {
            "ordered-rg": {
                "dependsOn": [
                    "assignPolicyTags"
                ],
                "metadata": {
                    "description": "Resource Group that waits for 'assignPolicyTags' creation"
                }
            },
            "standard-rg": {
                "metadata": {
                    "description": "Resource Group that follows the standard sequence ordering"
                }
            }
        },
        "targetScope": "subscription"
    },
    "type": "Microsoft.Blueprint/blueprints"
}
```

### <a name="example---artifact-with-custom-order"></a>Beispiel: Artefakt mit benutzerdefinierter Reihenfolge

Bei diesem Beispiel handelt es sich um ein Richtlinienartefakt, das von einer Azure Resource Manager-Vorlage abhängt. Standardmäßig wird ein Richtlinienartefakt vor der Azure Resource Manager-Vorlage erstellt. Bei Verwendung dieser Reihenfolge kann das Richtlinienartefakt warten, bis die Azure Resource Manager-Vorlage erstellt wurde.

```json
{
    "properties": {
        "displayName": "Assigns an identifying tag",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "resourceGroup": "standard-rg",
        "dependsOn": [
            "customTemplate"
        ]
    },
    "kind": "policyAssignment",
    "type": "Microsoft.Blueprint/artifacts"
}
```

### <a name="example---subscription-level-template-artifact-depending-on-a-resource-group"></a>Beispiel: Von einer Ressourcengruppe abhängiges Vorlagenartefakt auf Abonnementebene

In diesem Beispiel wird eine Resource Manager-Vorlage, die auf Abonnementebene bereitgestellt wird, von einer Ressourcengruppe abhängig gemacht. Bei der Standardreihenfolge werden die Artefakte auf Abonnementebene vor allen Ressourcengruppen und untergeordneten Artefakten in diesen Ressourcengruppen erstellt. Die Ressourcengruppe wird in der Blaupausendefinition wie folgt definiert:

```json
"resourceGroups": {
    "wait-for-me": {
        "metadata": {
            "description": "Resource Group that is deployed prior to the subscription level template artifact"
        }
    }
}
```

Das Vorlagenartefakt auf Abonnementebene, das von der Ressourcengruppe **wait-for-me** abhängig ist, wird wie folgt definiert:

```json
{
    "properties": {
        "template": {
            ...
        },
        "parameters": {
            ...
        },
        "dependsOn": ["wait-for-me"],
        "displayName": "SubLevelTemplate",
        "description": ""
    },
    "kind": "template",
    "type": "Microsoft.Blueprint/blueprints/artifacts"
}
```

## <a name="processing-the-customized-sequence"></a>Verarbeiten der benutzerdefinierten Reihenfolge

Beim Erstellen wird eine topologische Sortierung verwendet, um das Abhängigkeitsdiagramm der Blaupausenartefakte zu erstellen. Die Überprüfung stellt sicher, dass alle Abhängigkeitsebenen zwischen Ressourcengruppen und Artefakten unterstützt werden.

Wird eine Artefaktabhängigkeit deklariert, die keine Änderung der Standardreihenfolge zur Folge hat, wird keine Änderung vorgenommen. Ein Beispiel wäre etwa eine Ressourcengruppe, die von einer Richtlinie auf Abonnementebene abhängt. Ein weiteres Beispiel wäre eine untergeordnete Richtlinienzuweisung der Ressourcengruppe „standard-rg“, die von der untergeordneten Rollenzuweisung der Ressourcengruppe „standard-rg“ abhängt. In beiden Fällen würde `dependsOn` die Standardreihenfolge nicht ändern, sodass keine Änderungen anfallen würden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](parameters.md) vertraut.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../how-to/update-existing-assignments.md).
- Beheben Sie Probleme bei der Blaupausenzuweisung mithilfe des [allgemeinen Leitfadens zur Problembehandlung](../troubleshoot/general.md).