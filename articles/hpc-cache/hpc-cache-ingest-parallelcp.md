---
title: 'Azure HPC Cache-Datenerfassung (Vorschau): Skript für paralleles Kopieren'
description: Verwenden eines Skript für paralleles Kopieren zum Verschieben von Daten in ein Blobspeicherziel in Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: v-erkell
ms.openlocfilehash: 852b4e692a4316c7701c8c179039104bee561949
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180959"
---
# <a name="azure-hpc-cache-preview-data-ingest---parallel-copy-script-method"></a>Azure HPC Cache-Datenerfassung (Vorschau): Methode mit Skript für paralleles Kopieren

Dieser Artikel enthält Anweisungen zum Erstellen des ``parallelcp``-Skripts und für seine Verwendung zum Verschieben von Daten in einen Blobspeichercontainer für die Verwendung mit Azure HPC Cache.

Weitere Informationen zum Verschieben von Daten in einen Blobspeicher für Ihren Azure HPC Cache finden Sie unter [Verschieben von Daten in Azure Blob Storage für Azure HPC Cache](hpc-cache-ingest.md).

## <a name="create-the-parallelcp-script"></a>Erstellen des parallelcp-Skripts

Das folgende Skript fügt die ausführbare Datei `parallelcp` hinzu. (Dieses Skript ist für Ubuntu vorgesehen. Wenn Sie eine andere Distribution verwenden, müssen Sie ``parallel`` separat installieren.)

```bash
sudo touch /usr/bin/parallelcp && sudo chmod 755 /usr/bin/parallelcp && sudo sh -c "/bin/cat >/usr/bin/parallelcp" <<EOM 
#!/bin/bash

display_usage() { 
    echo -e "\nUsage: \$0 SOURCE_DIR DEST_DIR\n" 
} 

if [  \$# -le 1 ] ; then 
    display_usage
    exit 1
fi 
 
if [[ ( \$# == "--help") ||  \$# == "-h" ]] ; then 
    display_usage
    exit 0
fi 

SOURCE_DIR="\$1"
DEST_DIR="\$2"

if [ ! -d "\$SOURCE_DIR" ] ; then
    echo "Source directory \$SOURCE_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -d "\$DEST_DIR" ] && ! mkdir -p \$DEST_DIR ; then
    echo "Destination directory \$DEST_DIR does not exist, or is not a directory"
    display_usage
    exit 2
fi

if [ ! -w "\$DEST_DIR" ] ; then
    echo "Destination directory \$DEST_DIR is not writeable, or is not a directory"
    display_usage
    exit 3
fi

if ! which parallel > /dev/null ; then
    sudo apt-get update && sudo apt install -y parallel
fi

DIRJOBS=225
JOBS=225
find \$SOURCE_DIR -mindepth 1 -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$DIRJOBS -0 "mkdir -p \$DEST_DIR/{}"
find \$SOURCE_DIR -mindepth 1 ! -type d -print0 | sed -z "s/\$SOURCE_DIR\///" | parallel --will-cite -j\$JOBS -0 "cp -P \$SOURCE_DIR/{} \$DEST_DIR/{}"
EOM
```

## <a name="parallel-copy-example"></a>Beispiel zum parallelen Kopieren

In diesem Beispiel wird das Skript zum parallelen Kopieren verwendet, um ``glibc`` mit Quelldateien im Azure HPC Cache zu kompilieren.

Die Quelldateien werden am Bereitstellungspunkt des Azure HPC Cache zwischengespeichert, und die Objektdateien werden auf der lokalen Festplatte gespeichert.

In diesem Beispiel wird das Skript zum parallelen Kopieren mit den Optionen ``-j`` und ``make`` zum Erreichen von Parallelisierung verwendet.

```bash
sudo apt-get update
sudo apt install -y gcc bison gcc binutils make parallel
cd
wget https://mirrors.kernel.org/gnu/libc/glibc-2.27.tar.bz2
tar jxf glibc-2.27.tar.bz2
ln -s /nfs/cache1 hpccache
time parallelcp glibc-2.27 avere/glibc-2.27
cd
mkdir obj
mkdir usr
cd obj
/home/azureuser/avere/glibc-2.27/configure --prefix=/home/azureuser/usr
time make -j
```
