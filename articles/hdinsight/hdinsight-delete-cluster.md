---
title: Come eliminare un cluster HDInsight - Azure
description: Informazioni sui vari modi in cui è possibile eliminare un cluster HDInsight di Azure
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-azurecli
ms.date: 11/29/2019
ms.openlocfilehash: 2160cfcd1f12f5effe7bb182eb665f85fec863af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863395"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Eliminare un cluster HDInsight tramite browser, PowerShell o l'interfaccia della riga di comando di Azure

La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione è pro-rated al minuto, quindi è sempre necessario eliminare il cluster quando non è più in uso. Questo documento illustra come eliminare un cluster usando il [portale di Azure](https://portal.azure.com), [Azure PowerShell AZ Module](/powershell/azure/)e l'interfaccia della riga di comando di [Azure](/cli/azure/).

> [!IMPORTANT]  
> L'eliminazione di un cluster HDInsight non comporta l'eliminazione degli account di archiviazione di Azure o Data Lake Storage associati al cluster. È possibile usare nuovamente in futuro i dati archiviati in questi servizi.

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Dal menu a sinistra passare a **tutti i servizi**  >  **analisi**  >  **HDInsight cluster** e selezionare il cluster.

3. Nella visualizzazione predefinita selezionare l'icona **Elimina** . Seguire la richiesta per eliminare il cluster.

    :::image type="content" source="./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png" alt-text="Pulsante Elimina cluster HDInsight":::

## <a name="azure-powershell"></a>Azure PowerShell

Sostituire `CLUSTERNAME` con il nome del cluster HDInsight nel codice riportato di seguito. Da un prompt di PowerShell immettere il comando seguente per eliminare il cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Sostituire `CLUSTERNAME` con il nome del cluster HDInsight e `RESOURCEGROUP` con il nome del gruppo di risorse nel codice riportato di seguito.  Da un prompt dei comandi immettere quanto segue per eliminare il cluster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
