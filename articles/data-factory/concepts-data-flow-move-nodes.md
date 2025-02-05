---
title: 'Azure Data Factory-Datenfluss: Verschieben von Knoten'
description: Verschieben von Knoten in einem Diagramm zum Azure Data Factory-Mappingdatenfluss
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 853fff955a953052b94ad4e7e1dd55371069c30b
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030188"
---
# <a name="mapping-data-flow-move-nodes"></a>Mapping Data Flow: Verschieben von Knoten



![Optionen für die Transformation zum Aggregieren](media/data-flow/agghead.png "Aggregatorheader")

Die Azure Data Factory-Datenfluss-Entwurfsoberfläche ist eine „Konstruktionsoberfläche“, mit der Sie Datenflüsse von oben nach unten und von links nach rechts erstellen können. Jeder Transformation mit einem Pluszeichen (+) ist eine Toolbox angefügt. Konzentrieren Sie sich auf Ihre Geschäftslogik, statt auf das Verbinden von Knoten über Edges in einer Freiform-DAG-Umgebung.

Ohne ein Drag & Drop-Paradigma besteht die Möglichkeit zum „Verschieben“ eines Transformationsknotens also darin, den eingehenden Datenstrom zu ändern. Stattdessen verschieben Sie Transformationen, indem Sie den „eingehenden Datenstrom“ ändern.

## <a name="streams-of-data-inside-of-data-flow"></a>Datenströme innerhalb des Datenflusses

In Azure Data Factory-Datenfluss stellen Datenströme den Fluss der Daten dar. Im Bereich der Transformationseinstellungen wird ein Feld „Eingehender Datenstrom“ angezeigt. Daraus geht hervor, welcher eingehende Datenstrom die Transformation speist. Sie können den physischen Speicherort Ihres Transformationsknotens im Diagramm ändern, indem Sie auf den Namen des eingehenden Datenstroms klicken und einen anderen Datenstrom auswählen. Dann werden die aktuelle Transformation sowie alle nachfolgenden Transformationen in diesem Datenstrom an den neuen Speicherort verschoben.

Wenn Sie eine Transformation mit einer oder mehreren nachfolgenden Transformationen verschieben, wird der neue Speicherort im Datenfluss über eine neue Verzweigung verknüpft.

Wenn es nach dem von Ihnen ausgewählten Knoten keine nachfolgenden Transformationen gibt, wird nur diese Transformation an den neuen Speicherort verschoben.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihren Datenflussentwurf abgeschlossen haben, aktivieren Sie die Debug-Schaltfläche und testen Sie sie im Debugmodus entweder direkt im [Datenfluss-Designer](concepts-data-flow-debug-mode.md) oder über die Option zum [Debuggen der Pipeline](control-flow-execute-data-flow-activity.md).
