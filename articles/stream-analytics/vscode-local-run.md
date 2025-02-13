---
title: Lokales Testen von Azure Stream Analytics-Abfragen mit Visual Studio Code (Vorschau)
description: Dieser Artikel beschreibt, wie Abfragen mit den Azure Stream Analytics-Tools für Visual Studio Code lokal getestet werden.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 751cdf50fccc654dfab06b4d18428531312d08e6
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673027"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Lokales Testen von Stream Analytics-Abfragen mit Visual Studio Code

Mithilfe von Azure Stream Analytics-Tools für Visual Studio Code können Sie Ihre Stream Analytics-Aufträge lokal mit Beispieldaten testen.

In diesem [Schnellstarttutorial](quick-create-vs-code.md) erfahren Sie, wie ein Stream Analytics-Auftrags mithilfe von Visual Studio Code erstellt wird.

## <a name="prerequisites"></a>Voraussetzungen
* Installieren Sie das [.NET Core SDK](https://dotnet.microsoft.com/download).
* Starten Sie Visual Studio Code neu.
 
## <a name="run-queries-locally"></a>Lokales Ausführen von Abfragen

Mithilfe von Azure Stream Analytics-Erweiterungen für Visual Studio Code können Sie Ihre Stream Analytics-Aufträge lokal mit Beispieldaten testen.

1. Nachdem Sie Ihren Stream Analytics-Auftrag erstellt haben, verwenden Sie **STRG+UMSCHALT+P** zum Öffnen der Befehlspalette. Geben Sie dann **ASA ein, und wählen Sie den Eintrag aus: Hinzufügen der Eingabe**.

    ![Hinzufügen von ASA-Eingaben in Visual Studio Code](./media/vscode-local-run/add-input.png)

2. Wählen Sie **Local Input** (Lokale Eingabe) aus.

    ![Hinzufügen von lokalen ASA-Eingaben in Visual Studio Code](./media/vscode-local-run/add-local-input.png)

3. Wählen Sie **+ New Local Input** (+ Neue lokale Eingabe) aus.

    ![Hinzufügen einer neuen lokalen ASA-Eingabe in Visual Studio Code](./media/vscode-local-run/add-new-local-input.png)

4. Geben Sie die gleichen Eingabealias ein, die Sie in Ihrer Abfrage verwendet haben.

    ![Hinzufügen eines neuen lokalen ASA-Eingabealias](./media/vscode-local-run/new-local-input-alias.png)

5. Geben Sie in der Datei **LocalInput_DefaultLocalStream.json** den Dateipfad ein, in dem sich Ihre lokale Datendatei befindet.

    ![Eingeben von lokalen Dateipfaden in Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Gehen Sie zurück zum Abfrage-Editor, und wählen Sie **Lokal ausführen** aus.

    ![Auswählen von „Lokal ausführen“ im Abfrage-Editor](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Azure Stream Analytics-Cloudauftrags in Visual Studio Code (Vorschauversion)](quick-create-vs-code.md)

* [Erkunden von Azure Stream Analytics-Aufträgen mit Visual Studio Code (Vorschauversion)](vscode-explore-jobs.md)
