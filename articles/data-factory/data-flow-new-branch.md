---
title: 'Azure Data Factory Mapping Data Flow: Transformation für neue Verzweigung'
description: 'Azure Data Factory Mapping Data Flow: Transformation für neue Verzweigung'
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 3f443396627a4bbaba2a3eeb0a3ac05cc1597c85
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029289"
---
# <a name="azure-data-factory-mapping-data-flow-new-branch-transformation"></a>Azure Data Factory Mapping Data Flow: Transformation für neue Verzweigung



![Verzweigungsoptionen](media/data-flow/menu.png "Menü")

Beim Verzweigen wird der aktuelle Datenstrom in Ihrem Datenfluss übernommen und in einen anderen Datenstrom repliziert. Verwenden Sie „Neue Verzweigung“, um mehrere Vorgänge und Transformationen für den gleichen Datenstrom auszuführen.

Beispiel: Ihr Datenfluss verfügt über eine Quelltransformation mit einer ausgewählten Gruppe von Spalten und Datentypkonvertierungen. Sie platzieren dann eine abgeleitete Spalte unmittelbar nach dieser Quelle. Sie haben in „Abgeleitete Spalte“ ein neues Feld erstellt, das Vor- und Nachname kombiniert, sodass ein neues Feld „Vollständiger Name“ entsteht.

Sie können auf diesen neuen Datenstrom eine Reihe von Transformationen und eine Senke in einer Zeile anwenden und „Neue Verzweigung“ verwenden, um eine Kopie dieses Datenstroms zu erstellen. Darin können Sie dann dieselben Daten mit unterschiedlichen Transformationen transformieren. Durch Transformieren dieser kopierten Daten in einer separaten Verzweigung können Sie diese Daten anschließend an einen anderen Ort übertragen.

> [!NOTE]
> Im Menü „+ Transformation“ wird „Neue Verzweigung“ nur als Aktion angezeigt, wenn eine nachfolgende Transformation nach der aktuellen Position vorhanden ist, an der Sie versuchen, die Verzweigung vorzunehmen. D. h., dass hier am Ende nur eine Option „Neue Verzweigung“ angezeigt wird, wenn Sie nach der Auswahltransformation eine andere Transformation hinzufügen.

![Verzweigung](media/data-flow/branch2.png "Verzweigung 2")
