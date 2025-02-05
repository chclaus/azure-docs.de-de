---
title: Grafische Benutzeroberfläche
titleSuffix: Azure Machine Learning
description: Hier erhalten Sie Informationen zu Terminologie, Konzepten und Workflows der grafischen Benutzeroberfläche (Vorschauversion) für Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: f560887a48ce4754b26a54ef0e18093c5577af34
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71128804"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Was ist die grafische Benutzeroberfläche für Azure Machine Learning? 

Die grafische Benutzeroberfläche (Vorschauversion) für Azure Machine Learning unterstützt Sie bei der Datenaufbereitung für und beim Trainieren, Testen, Bereitstellen, Verwalten und Überwachen von Machine Learning-Modellen – ganz ohne Programmierung.

Für das Erstellen Ihres Modells ist keine Programmierung erforderlich, sondern einfach nur das visuelle Verbinden von [Datasets](#dataset) und [Modulen](#module).

Die grafische Benutzeroberfläche verwendet Ihren Azure Machine Learning-[Arbeitsbereich](concept-workspace.md) zu folgenden Zwecken:

+ Schreiben von Artefakten von [Experimentausführungen](#experiment) im Workspace
+ Zugriff auf [Datasets](#dataset)
+ Verwenden der [Computeressourcen](#compute) im Workspace, um das Experiment auszuführen 
+ Registrieren von [Modellen](concept-azure-machine-learning-architecture.md#models)
+ [Bereitstellen](#deployment) von Modellen als Webdienste auf Computeressourcen im Workspace

![Übersicht über die grafische Benutzeroberfläche](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Workflow

Im Rahmen der grafischen Benutzeroberfläche erhalten Sie eine interaktive, visuelle Canvas, um ein Modell schnell erstellen, testen und durchlaufen zu können. 

+ [Module](#module) lassen sich per Drag & Drop-Verfahren auf der Canvas platzieren.
+ Verbinden Sie die Module miteinander, um ein [Experiment](#experiment) zu erstellen.
+ Führen Sie das Experiment mithilfe der Computeressource des Machine Learning Service-Workspace aus.
+ Durchlaufen Sie Ihr Modellentwurf, indem Sie das Experiment bearbeiten und erneut ausführen.
+ Wenn Sie soweit sind, konvertieren Sie Ihr **Trainingsexperiment** in ein **Vorhersageexperiment**.
+ Dann können Sie Ihr Vorhersageexperiment als Webdienst [bereitstellen](#deployment), damit andere Personen auf Ihr Modell zugreifen können.

## <a name="experiment"></a>Experiment

Sie können ein Experiment von Grund auf neu erstellen, oder Sie können ein vorhandenes Beispielexperiment als Vorlage verwenden.  Jedes Mal, wenn Sie ein Experiment ausführen, werden Artefakte in Ihrem Workspace gespeichert.

Ein Experiment besteht aus Datasets und Analysemodulen, die Sie miteinander verbinden und so ein Modell konstruieren. Ein gültiges Experiment hat spezifisch folgende Eigenschaften:

* Datasets können nur mit Modulen verbunden sein.
* Module können entweder mit Datasets oder mit anderen Modulen verbunden sein.
* Alle Eingangsports für Module müssen eine Verbindung zum Datenfluss aufweisen.
* Alle erforderlichen Parameter der einzelnen Module müssen festgelegt sein.


Informationen zu den ersten Schritten mit der grafischen Benutzeroberfläche finden Sie unter [Tutorial: Prognostizieren von Automobilpreisen mithilfe der grafischen Benutzeroberfläche](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Datensatz

Ein Dataset besteht aus Daten, die in die grafische Benutzeroberfläche zur Verwendung im Modellierungsprozess hochgeladen wurden. Eine Reihe von Beispiel-Datasets sind bereits enthalten, mit denen Sie experimentieren können, und bei Bedarf lassen sich weitere hochladen.

## <a name="module"></a>Modul

Ein Modul ist ein Algorithmus, den Sie auf Ihre Daten anwenden können. Die grafische Benutzeroberfläche verfügt über eine Reihe von Modulen, die von Funktionen für die Dateneinspeisung bis zu Trainings-, Bewertungs- und Überprüfungsvorgängen reichen.

Ein Modul kann eine Reihe von Parametern haben, die Sie zum Konfigurieren der internen Algorithmen des Moduls einsetzen können. Wenn Sie ein Modul auf der Canvas auswählen, werden dessen Parameter rechts neben der Canvas im Bereich „Eigenschaften“ angezeigt. Sie können die Parameter in diesem Bereich zur Abstimmung Ihres Modells verändern.

![Moduleigenschaften](media/ui-concept-visual-interface/properties.png)

Unterstützung bei der Navigation durch die Bibliothek der verfügbaren Machine Learning-Algorithmen finden Sie unter [Algorithmen und Module: Referenzübersicht](../algorithm-module-reference/module-reference.md).

## <a name="compute"></a> Computeressourcen

Verwenden Sie Computeressourcen aus Ihrem Workspace, um Ihr Experiment auszuführen oder Ihre bereitgestellten Modelle als Webdienste zu hosten. Die unterstützten Computeziele lauten:


| Computeziel | Training | Bereitstellung |
| ---- |:----:|:----:|
| Azure Machine Learning Compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

Computeziele sind in Ihren Machine Learning-[Workspace](concept-workspace.md) integriert. Ihre Computeziele verwalten Sie in Ihrem Workspace im [Azure-Portal](https://portal.azure.com) oder auf der [Angebotsseite des Arbeitsbereichs (Vorschauversion)](https://ml.azure.com).

## <a name="deployment"></a>Bereitstellung

Wenn Ihr Predictive Analytics-Modell bereit ist, können Sie es als Webdienst direkt über die grafische Benutzeroberfläche bereitstellen.

Webdienste stellen eine Schnittstelle zwischen einer Anwendung und Ihrem Bewertungsmodell zur Verfügung. Eine externe Anwendung kann in Echtzeit mit einem Bewertungsmodell kommunizieren. Mit einem Aufruf eines Webdiensts werden Vorhersageergebnisse an eine externe Anwendung zurückgegeben. Zur Durchführung eines Webdienstaufrufs übergeben Sie einen API-Schlüssel, der beim Bereitstellen des Webdiensts erstellt wurde. Ein Webdienst basiert auf REST, einer verbreiteten Architektur für Webprogrammierungsprojekte.

Informationen dazu, wie Sie Ihr Modell bereitstellen, finden Sie unter [Tutorial: Bereitstellen eines Machine Learning-Modells mithilfe der grafischen Benutzeroberfläche](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Nächste Schritte

* Die Grundlagen von Predictive Analytics und maschinellem Lernen werden hier erläutert: [Tutorial: Prognostizieren von Automobilpreisen mithilfe der grafischen Benutzeroberfläche](ui-tutorial-automobile-price-train-score.md)
* Verwenden Sie eines der Beispiele und passen Sie es an Ihre Anforderungen an:
    * [Beispiel 1 – Regression: Preisprognose](how-to-ui-sample-regression-predict-automobile-price-basic.md)
    * [Beispiel 2 – Regression: Vorhersagen des Preises und Vergleichen von Algorithmen](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Beispiel 3 – Klassifizierung: Vorhersagen des Kreditrisikos](how-to-ui-sample-classification-predict-credit-risk-basic.md)
    * [Beispiel 4 – Klassifizierung: Vorhersagen des Kreditrisikos (kostensensibel)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Beispiel 5 – Klassifizierung: Vorhersage von Kundenabwanderung, Kauflust und Up-Selling](how-to-ui-sample-classification-predict-churn.md)
