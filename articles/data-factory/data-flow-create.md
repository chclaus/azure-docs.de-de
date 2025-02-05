---
title: Erstellen eines Azure Data Factory-Zuordnungsdatenflusses
description: Sie erfahren, wie Sie einen Azure Data Factory-Mappingdatenfluss erstellen.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: d8ebc026add9dfd85d7cbe312a8cfffd3e365f82
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026882"
---
# <a name="create-azure-data-factory-data-flow"></a>Erstellen eines Azure Data Factory-Datenflusses



Mit Mapping Data Flow in ADF können Sie Daten nach Maß transformieren, ohne Code schreiben zu müssen. Sie können einen Datentransformationsauftrag im Datenfluss-Designer entwerfen, indem Sie eine Reihe von Transformationen erstellen. Beginnen Sie mit einer beliebigen Anzahl von Quelltransformationen, gefolgt von Datentransformationsschritten. Vervollständigen Sie dann Ihren Datenfluss mit einer Senke, damit Ihre Ergebnisse an ein Ziel gelangen.

Erstellen Sie zuerst im Azure-Portal eine neue Data Factory (V2). Klicken Sie nach dem Erstellen der neuen Factory auf die Kachel „Erstellen und überwachen“, um die Data Factory-Benutzeroberfläche zu starten.

![Datenflussoptionen](media/data-flow/v2portal.png "Datenfluss erstellen")

Sobald Sie sich auf der Data Factory-Benutzeroberfläche befinden, können Sie die Beispieldatenflüsse verwenden. Die Beispiele sind im ADF-Vorlagenkatalog verfügbar. Erstellen Sie in ADF eine „Pipeline aus Vorlage“, und wählen Sie im Vorlagenkatalog die Kategorie „Datenfluss“ aus.

![Datenflussoptionen](media/data-flow/template.png "Datenfluss erstellen")

Sie werden zur Eingabe Ihrer Azure Blob Storage-Kontoinformationen aufgefordert.

![Datenflussoptionen](media/data-flow/template2.png "Datenfluss erstellen 2")

[Die für diese Beispiele verwendeten Daten finden Sie hier](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Laden Sie die Beispieldaten herunter, und speichern Sie die Dateien in Ihren Azure Blob Storage-Konten, damit Sie die Beispiele ausführen können.

## <a name="create-new-data-flow"></a>Erstellen des neuen Datenflusses

Verwenden Sie auf der ADF-Benutzeroberfläche die Schaltfläche „Ressource erstellen“ mit dem Pluszeichen (+), um Datenflüsse zu erstellen.

![Datenflussoptionen](media/data-flow/newresource.png "Neue Ressource")

## <a name="next-steps"></a>Nächste Schritte

Beginnen Sie die Erstellung Ihrer Datentransformation mit einer [Quelltransformation](data-flow-source.md).
