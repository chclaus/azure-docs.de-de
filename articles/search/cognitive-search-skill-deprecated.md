---
title: Veraltete kognitive Qualifikationen – Azure Search
description: Diese Seite enthält eine Liste der Qualifikationen für die kognitive Suche, die als veraltet betrachtet und in naher Zukunft nicht mehr unterstützt werden.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 1e78852ec8b92f1a9e37a4dbcbbcb371c0ac0f97
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265434"
---
# <a name="deprecated-cognitive-search-skills"></a>Veraltete Qualifikationen für die kognitive Suche

In diesem Dokument werden die kognitiven Qualifikationen beschrieben, die als veraltet betrachtet werden. Verwenden Sie für den Inhalt die folgende Anleitung:

* Qualifikationsname: Name der Qualifikation, die als veraltet markiert wird und dem Attribut @odata.type zugeordnet ist.
* Letzte verfügbare API-Version: Die letzte Version der öffentlichen Azure Search-API, über die Qualifikationsgruppen mit der entsprechenden veralteten Qualifikation erstellt/aktualisiert werden können.
* Ende des Supports: Der letzte Tag, an dem die entsprechende Qualifikation unterstützt wird. Danach gilt sie als nicht mehr unterstützt. Vorher erstellte Qualifikationsgruppen sollten funktionsfähig bleiben. Benutzern wird jedoch empfohlen, von der als veraltet markierten Qualifikation zu einer anderen zu migrieren.
* Empfehlungen: Migrationspfad zum Verwenden einer unterstützten Qualifikation. Benutzern wird empfohlen, den Empfehlungen zu folgen, um weiterhin Support zu erhalten.

## <a name="microsoftskillstextnamedentityrecognitionskill"></a>Microsoft.Skills.Text.NamedEntityRecognitionSkill

### <a name="last-available-api-version"></a>Letzte verfügbare API-Version

Vorschauversion vom 11.11.2017

### <a name="end-of-support"></a>Ende des Supports

15. Februar 2019

### <a name="recommendations"></a>Empfehlungen 

Verwenden Sie stattdessen [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md). Diese Qualifikation enthält die meisten Funktionen von „NamedEntityRecognitionSkill“, jedoch mit höherer Qualität. Die komplexen Ausgabefelder enthalten zudem umfangreichere Informationen.

Um zur [Qualifikation „Entitätserkennung“ (EntityRecognitionSkill)](cognitive-search-skill-entity-recognition.md) zu migrieren, müssen Sie eine oder mehrere der folgenden Änderungen an Ihrer Qualifikationsdefinition vornehmen. Ein Update der Qualifikationsdefinition können Sie mit der [API zum Aktualisieren von Qualifikationsgruppen](https://docs.microsoft.com/rest/api/searchservice/update-skillset) vornehmen.

> [!NOTE]
> Derzeit wird die Zuverlässigkeitsbewertung als Konzept nicht unterstützt. Der Parameter `minimumPrecision` ist zur zukünftigen Verwendung und zur Bereitstellung der Abwärtskompatibilität in `EntityRecognitionSkill` enthalten.

1. *(Erforderlich)* Ändern Sie `@odata.type` von `"#Microsoft.Skills.Text.NamedEntityRecognitionSkill"` in `"#Microsoft.Skills.Text.EntityRecognitionSkill"`.

2. *(Optional)* Wenn Sie die `entities`-Ausgabe nutzen möchten, verwenden Sie stattdessen die komplexe Sammlungsausgabe `namedEntities` von `EntityRecognitionSkill`. Sie können `targetName` in der Qualifikationsdefinition verwenden, um ihn einer Anmerkung namens `entities` zuzuordnen.

3. *(Optional)* Wenn Sie den Parameter `categories` nicht explizit angeben, kann `EntityRecognitionSkill` neben den von `NamedEntityRecognitionSkill` unterstützten Kategorietypen einen anderen Typ zurückgeben. Wenn dieses Verhalten nicht erwünscht ist, müssen Sie den Parameter `categories` explizit auf `["Person", "Location", "Organization"]` festlegen.

    _Beispieldefinitionen für die Migration_

    * Einfache Migration

        _(Vorher) Definition „NamedEntityRecognitionSkill“_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
        _(Nachher) Definition „EntityRecognitionSkill“_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person"],
            "defaultLanguageCode": "en",
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            }
            ]
        }
        ```
    
    * Etwas kompliziertere Migration

        _(Vorher) Definition „NamedEntityRecognitionSkill“_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.NamedEntityRecognitionSkill",
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "entities"
            }
            ]
        }
        ```
        _(Nachher) Definition „EntityRecognitionSkill“_
        ```json
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": [ "Person", "Location", "Organization" ],
            "defaultLanguageCode": "en",
            "minimumPrecision": 0.1,
            "inputs": [
            {
                "name": "text",
                "source": "/document/content"
            }
            ],
            "outputs": [
            {
                "name": "persons",
                "targetName": "people"
            },
            {
                "name": "namedEntities",
                "targetName": "entities"
            }
            ]
        }
        ```

## <a name="see-also"></a>Weitere Informationen

+ [Vordefinierte Skills](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Die kognitive Qualifikation „Entitätserkennung“](cognitive-search-skill-entity-recognition.md)
