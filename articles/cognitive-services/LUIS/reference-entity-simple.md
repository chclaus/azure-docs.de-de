---
title: Entität vom Typ „Simple“ – LUIS
titleSuffix: Azure Cognitive Services
description: Eine einfache Entität ist eine generische Entität, die ein einzelnes Konzept beschreibt und im Kontext des maschinellen Lernens erworben wurde. Da es sich bei einfachen Entitäten normalerweise um Namen handelt, z.B. Unternehmensnamen, Produktnamen oder andere Namen, fügen Sie eine Liste mit Ausdrücken hinzu, wenn Sie eine einfache Entität verwenden, um das Signal für die verwendeten Namen zu verstärken.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 2eb3ff847f9bfc162adfb281d2ac1fad6f8c5093
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695126"
---
# <a name="simple-entity"></a>Entität vom Typ „Simple“ 

Eine einfache Entität ist eine generische Entität, die ein einzelnes Konzept beschreibt und im Kontext des maschinellen Lernens erworben wurde. Da es sich bei einfachen Entitäten normalerweise um Namen handelt, z.B. Unternehmensnamen, Produktnamen oder andere Namen, sollten Sie wie folgt vorgehen: Fügen Sie eine [Liste mit Ausdrücken](luis-concept-feature.md) hinzu, wenn Sie eine einfache Entität verwenden, um das Signal für die verwendeten Namen zu verstärken. 

**Diese Entität ist gut geeignet, wenn Folgendes gilt**:

* Die Daten sind nicht einheitlich formatiert, aber weisen auf denselben Sachverhalt hin. 

![Entität vom Typ „Simple“](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>JSON-Beispiel

`Bob Jones wants 3 meatball pho`

In der vorherigen Äußerung wird `Bob Jones` als die einfache Entität `Customer` bezeichnet.

Die vom Endpunkt zurückgegebenen Daten enthalten den Namen der Entität, den in der Äußerung ermittelten Text, den Speicherort des erkannten Texts und die Bewertung:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 – Antwort für Vorhersageendpunkt](#tab/V2)

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 – Antwort für Vorhersageendpunkt](#tab/V3)

Dies ist der JSON-Code, wenn `verbose=false` in der Abfragezeichenfolge festgelegt ist:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ]
}```

This is the JSON if `verbose=true` is set in the query string:

```json
"entities": {
    "Customer": [
        "Bob Jones"
    ],
    "$instance": {
        "Customer": [
            {
                "type": "Customer",
                "text": "Bob Jones",
                "startIndex": 0,
                "length": 9,
                "score": 0.9339134,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

|Datenobjekt|Name der Entität|Wert|
|--|--|--|
|Entität vom Typ „Simple“|`Customer`|`bob jones`|

## <a name="next-steps"></a>Nächste Schritte

In diesem [Tutorial](luis-quickstart-primary-and-secondary-data.md) werden maschinell gelernte Daten einer Stellenbezeichnung mithilfe einer **einfachen Entität** aus einer Äußerung extrahiert. Um die Genauigkeit beim Extrahieren zu erhöhen, fügen Sie der einfachen Entität eine [Begriffsliste](luis-concept-feature.md) mit spezifischen Ausdrücken hinzu.