---
title: Caricare dati per processi Apache Hadoop in HDInsight
description: Informazioni su come caricare e accedere ai dati per Apache Hadoop processi in HDInsight. Usare l'interfaccia della riga di comando classica di Azure, Azure Storage Explorer, Azure PowerShell, la riga di comando hadoop o Sqoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020, devx-track-azurecli
ms.date: 04/27/2020
ms.openlocfilehash: d58d194e8dbf011eec949602e4f8cd2e084a0d98
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482110"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Caricare dati per processi Apache Hadoop in HDInsight

HDInsight offre un file system distribuito Hadoop (HDFS) su Archiviazione di Azure e Azure Data Lake Storage. Questa archiviazione include Gen1 e Gen2. Archiviazione di Azure e Data Lake Storage Gen1 e Gen2 sono progettate come estensioni HDFS. Consentono al set completo di componenti nell'ambiente Hadoop di operare direttamente sui dati gestiti. Archiviazione di Azure, Data Lake Storage Gen1 e Gen2 sono file system distinti. I sistemi sono ottimizzati per l'archiviazione di dati e calcoli su questi dati. Per informazioni sui vantaggi dell'uso di Archiviazione di Azure, vedere Usare Archiviazione di Azure [con HDInsight](hdinsight-hadoop-use-blob-storage.md). Vedere anche Usare [Data Lake Storage Gen1 con HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)e Usare Data Lake Storage Gen2 [con HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, tenere presenti i requisiti seguenti:

* Disporre di un cluster HDInsight di Azure. Per istruzioni, vedere [Introduzione a Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Conoscenza degli articoli seguenti:
    * [Usare Archiviazione di Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Usare Data Lake Store con cluster Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [Usare l'anteprima di Archiviazione di Azure Data Lake Gen2 con cluster Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Caricare i dati in Archiviazione di Azure

### <a name="utilities"></a>Utilità

Microsoft fornisce le utilità seguenti da usare con Archiviazione di Azure:

| Strumento | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) |âââ" |âââ" |âââ" |
| [Interfaccia della riga di comando di Azure](../storage/blobs/storage-quickstart-blobs-cli.md) |âââ" |âââ" |âââ" |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |âââ" |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |âââ" | |âââ" |
| [Comando Hadoop](#hadoop-command-line) |âââ" |âââ" |âââ" |

> [!NOTE]  
> Il comando Hadoop è disponibile solo nel cluster HDInsight. Il comando consente solo il caricamento dei dati dal file system locale in Archiviazione di Azure.  

### <a name="hadoop-command-line"></a>Riga di comando di Hadoop

La riga di comando di Hadoop è utile solo per archiviare i dati nel BLOB di archiviazione di Azure quando i dati sono già presenti nel nodo head del cluster.

Per usare il comando Hadoop, è prima necessario connettersi al nodo head usando [SSH o PuTTY.](hdinsight-hadoop-linux-use-ssh-unix.md)

Dopo essersi connessi, è possibile usare la sintassi seguente per caricare un file nell'archiviazione.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Ad esempio: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Poiché l'file system predefinita per HDInsight è in Archiviazione di Azure, /example/data/data.txt si trova in Archiviazione di Azure. È inoltre possibile fare riferimento al file come segue:

`wasbs:///example/data/data.txt`

oppure

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

Per un elenco di altri comandi hadoop che funzionano con i file, vedere [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Nei cluster Apache HBase la dimensione di blocco predefinita usata per la scrittura dei dati è 256 KB. Questa impostazione non costituisce un problema quando si usano API HBase o REST, ma l'uso dei comandi `hadoop` o `hdfs dfs` per scrivere dati di dimensioni superiori a ~12 GB provoca un errore. Per altre informazioni, vedere Eccezione [di archiviazione per la scrittura nel BLOB.](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob)

### <a name="graphical-clients"></a>Client con interfaccia grafica

Esistono diverse applicazioni che forniscono un'interfaccia grafica per usare Archiviazione di Azure. Nella tabella seguente è riportato un elenco di alcune di queste applicazioni:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools per HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |âââ" |âââ" |âââ" |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |âââ" |âââ" |âââ" |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |âââ" |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |âââ" |
| [CloudBerry Explorer per Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |âââ" |
| [Cyberduck](https://cyberduck.io/) | |âââ" |âââ" |

## <a name="mount-azure-storage-as-local-drive"></a>Montare Archiviazione di Azure come unità locale

Vedere [Montare Archiviazione di Azure come unità locale](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive).

## <a name="upload-using-services"></a>Caricamento tramite servizi

### <a name="azure-data-factory"></a>Azure Data Factory

Il Azure Data Factory è un servizio completamente gestito per la composizione di dati: servizi di archiviazione, elaborazione e spostamento in pipeline di produzione dati semplificate, adattabili e affidabili.

|Tipo di archiviazione|Documentazione|
|----|----|
|Archiviazione BLOB di Azure|[Copiare dati da e in Archiviazione BLOB di Azure usando Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Copiare dati da e in Azure Data Lake Storage di 1° generazione usando Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Caricare dati in Azure Data Lake Storage Gen2 con Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop è uno strumento progettato per il trasferimento di dati tra Hadoop e i database relazionali. Usarlo per importare dati da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL Server, MySQL o Oracle. Quindi, nella distribuzione di Hadoop file system (HDFS). Trasformare i dati in Hadoop con MapReduce o Hive e quindi esportare di nuovo i dati in un sistema RDBMS.

Per altre informazioni, vedere [Usare Sqoop con HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>SDK di sviluppo

È possibile accedere ad Archiviazione di Microsoft Azure anche tramite un SDK di Azure dai linguaggi di programmazione seguenti:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Per altre informazioni sull'installazione di SDK di Azure, vedere [Download di Azure](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come ottenere dati in HDInsight, leggere gli articoli seguenti per informazioni sull'analisi:

* [Introduzione ad Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Inviare processi Apache Hadoop a livello di codice](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Usare Apache Hive con HDInsight](hadoop/hdinsight-use-hive.md)
