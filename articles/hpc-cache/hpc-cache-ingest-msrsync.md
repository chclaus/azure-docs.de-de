---
title: 'Azure HPC Cache-Datenerfassung (Vorschau): msrsync'
description: Verwenden von msrsync zum Verschieben von Daten in ein Blobspeicherziel in Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: v-erkell
ms.openlocfilehash: 265ec55a6e013a37cf963b6256e900c070311f72
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180932"
---
# <a name="azure-hpc-cache-preview-data-ingest---msrsync-method"></a>Azure HPC Cache-Datenerfassung (Vorschau): msrsync-Methode

Dieser Artikel enthält detaillierte Anweisungen zum Verwenden des ``msrsync``-Hilfsprogramms zum Kopieren von Daten in einen Azure-Blobspeichercontainer für die Verwendung mit Azure HPC Cache.

Weitere Informationen zum Verschieben von Daten in einen Blobspeicher für Ihren Azure HPC Cache finden Sie unter [Verschieben von Daten in Azure Blob Storage für Azure HPC Cache](hpc-cache-ingest.md).

Das ``msrsync``-Tool kann verwendet werden, um Daten in ein Back-End-Speicherziel für den Azure HPC Cache zu verschieben. Dieses Tool wurde entwickelt, um die Bandbreitenauslastung durch die Ausführung mehrerer paralleler ``rsync``-Prozesse zu optimieren. Es ist bei GitHub unter https://github.com/jbd/msrsync erhältlich.

``msrsync`` unterteilt das Quellverzeichnis in separate „Buckets“ und führt dann einzelne ``rsync``-Prozesse für die einzelnen Buckets aus.

Vorläufige Tests mit einem virtuellen Computer mit vier Kernen zeigten die höchste Effizienz bei der Verwendung von 64 Prozessen. Verwenden Sie die ``msrsync``-Option ``-p``, um die Anzahl der Prozesse auf 64 festzulegen.

Beachten Sie, dass ``msrsync`` nur auf und von lokalen Volumes schreiben kann. Der Zugriff auf Quelle und Ziel muss auf der Arbeitsstation, die für die Ausgabe des Befehls verwendet wird, als lokal eingebundene Ressourcen möglich sein.

Befolgen Sie diese Anweisungen, um ``msrsync`` zum Auffüllen des Azure-Blobspeichers mit Azure HPC Cache zu verwenden:

1. Installieren Sie ``msrsync`` und seine Voraussetzungen (``rsync`` und Python 2.6 oder höher)
1. Bestimmen Sie die Gesamtzahl der zu kopierenden Dateien und Verzeichnisse.

   Verwenden Sie beispielsweise das Hilfsprogramm ``prime.py`` mit den Argumenten ```prime.py --directory /path/to/some/directory``` (als Download verfügbar<https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Wenn Sie ``prime.py`` nicht verwenden, können Sie die Anzahl der Elemente mit dem GNU-Tool ``find`` wie folgt berechnen:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Teilen Sie die Anzahl der Elemente durch 64, um die Anzahl der Elemente pro Prozess zu ermitteln. Verwenden Sie diese Zahl zusammen mit der Option ``-f``, um die Größe der Buckets festzulegen, wenn Sie den Befehl ausführen.

1. Führen Sie den Befehl ``msrsync`` aus, um Dateien zu kopieren:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Beispielsweise ist dieser Befehl so konzipiert, dass 11.000 Dateien in 64 Prozessen aus „/test/source-repository“ nach „/mnt/hpccache/repository“ verschoben werden:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
