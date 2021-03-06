---
title: includere file
description: File di inclusione
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/17/2020
ms.openlocfilehash: 3eb5ea468a234aea228539c2390ab6cae9352948
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629966"
---
**Le destinazioni di calcolo possono essere riutilizzate per più processi di training**. Ad esempio, dopo aver collegato una macchina virtuale remota all'area di lavoro, è possibile riutilizzarla per più processi. Per le pipeline di Machine Learning, usare il [passaggio della pipeline](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) appropriato per ogni destinazione di calcolo.

Per la maggior parte dei processi, è possibile usare qualsiasi risorsa seguente come destinazione di calcolo. Non tutte le risorse possono essere usate per Machine Learning automatizzato, pipeline di Machine Learning o progettazione.

|Destinazioni di&nbsp;training|[Machine Learning automatizzato](../articles/machine-learning/concept-automated-ml.md) | [Pipeline di apprendimento automatico](../articles/machine-learning/concept-ml-pipelines.md) | [Finestra di progettazione di Azure Machine Learning](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Computer locale](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Sì | &nbsp; | &nbsp; |
|[Cluster di calcolo di Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Sì | Sì | Sì |
|[Istanza di calcolo di Azure Machine Learning](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Sì (tramite SDK)  | Sì |  |
|[Macchina virtuale remota](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Sì  | Sì | &nbsp; |
|[Azure&nbsp;Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Sì (solo modalità locale SDK) | Sì | &nbsp; |
|[Azure Data Lake Analytics.](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Sì | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Sì | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Sì | &nbsp; |

> [!TIP]
> L'istanza di calcolo ha un disco del sistema operativo da 120 GB. Se lo spazio su disco è esaurito, [usare il terminale](../articles/machine-learning/how-to-access-terminal.md) per cancellare almeno 1-2 GB prima di [arrestare o riavviare](../articles/machine-learning/how-to-create-manage-compute-instance.md#manage) l'istanza di calcolo.
