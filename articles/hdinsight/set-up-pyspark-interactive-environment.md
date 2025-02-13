---
title: Microsoft Azure HDInsight – Interaktive PySpark-Umgebung für Visual Studio Code
description: Hier erfahren Sie, wie Sie mithilfe von Azure HDInsight Tools for Visual Studio Code Abfragen und Skripts erstellen und übermitteln.
keywords: VScode, Azure HDInsight Tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, Interactive Query
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f62f7994818e487202e35e4931472355fb6ac3d2
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130188"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Einrichten einer interaktiven PySpark-Umgebung für Visual Studio Code

Die folgenden Schritte zeigen Ihnen, wie Sie die interaktive PySpark-Umgebung in VS Code einrichten.

Mit dem Befehl **python/pip** wird die virtuelle Umgebung in Ihrem Home-Pfad erstellt. Wenn Sie eine andere Version verwenden möchten, müssen Sie die Standardversion des Befehls **python/pip** manuell ändern. Weitere Informationen finden Sie unter [update-alternatives](https://linux.die.net/man/8/update-alternatives).

1. Installieren Sie [Python](https://www.python.org/downloads/) und [pip](https://pip.pypa.io/en/stable/installing/).

   + Installieren Sie Python über [https://www.python.org/downloads/](https://www.python.org/downloads/).
   + Installieren Sie pip über [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (wenn nicht aus der Python-Installation installiert).
   + Überprüfen Sie mit den folgenden Befehlen, ob Python und pip erfolgreich installiert wurden. (Optional)

        ![Befehl zum Überprüfen der Python pip-Version](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Sie sollten Python manuell installieren, anstatt die Standardversion für macOS zu verwenden.

2. Installieren Sie **virtualenv** mit dem folgenden Befehl.

   ```
   pip install virtualenv
   ```

## <a name="other-packages"></a>Andere Pakete

Wenn Sie eine Fehlermeldung erhalten, installieren Sie die erforderlichen Pakete, indem Sie die folgenden Befehle ausführen:

   ![Installieren des libkrb5-Pakets für Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```
sudo apt-get install libkrb5-dev
```

```
sudo apt-get install python-dev
```

Starten Sie VS Code neu, und navigieren Sie zurück zum Skript-Editor, der **HDInsight: PySpark Interactive** ausführt.

## <a name="next-steps"></a>Nächste Schritte

### <a name="demo"></a>Demo
* HDInsight for VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Tools und Erweiterungen
* [Verwenden von Azure HDInsight-Tools für Visual Studio Code](hdinsight-for-vscode.md)
* [Verwenden des Azure-Toolkits für IntelliJ zum Erstellen und Übermitteln von Apache Spark Scala-Anwendungen](spark/apache-spark-intellij-tool-plugin.md)
* [Verwenden des Azure-Toolkits für IntelliJ zum Remotedebuggen von Apache Spark-Anwendungen über SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Verwenden des Azure-Toolkits für IntelliJ zum Remotedebuggen von Apache Spark-Anwendungen über VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Verwenden der HDInsight-Tools im Azure-Toolkit für Eclipse zum Erstellen von Apache Spark-Anwendungen](spark/apache-spark-eclipse-tool-plugin.md)
* [Verwenden von Apache Zeppelin Notebooks mit einem Apache Spark-Cluster unter HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Verfügbare Kernels für Jupyter Notebooks in einem Apache Spark-Cluster für HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualisieren von Apache Hive-Daten mit Microsoft Power BI in Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Verwenden von Apache Zeppelin zum Ausführen von Apache Hive-Abfragen in Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
