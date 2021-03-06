---
title: Clonare un'immagine gestita in una versione dell'immagine con l'interfaccia della riga di comando di Azure
description: Informazioni su come clonare un'immagine gestita in una versione dell'immagine in una raccolta di immagini condivise usando l'interfaccia della riga di comando di Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d0644b9ec9009fe5d1db7701834cb9788f86ab0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790168"
---
# <a name="clone-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Clonare un'immagine gestita in una versione dell'immagine usando l'interfaccia della riga di comando di Azure
Se si dispone di un'immagine gestita esistente che si vuole clonare in una raccolta di immagini condivise, è possibile creare un'immagine della Raccolta immagini condivise direttamente dall'immagine gestita. Dopo aver testato la nuova immagine, è possibile eliminare l'immagine gestita di origine. È anche possibile eseguire la migrazione da un'immagine gestita a una Raccolta immagini condivise usando [PowerShell.](image-version-managed-image-powershell.md)

Le immagini in una raccolta di immagini hanno due componenti, che verranno creati in questo esempio:
- Una **definizione di immagine** contiene informazioni sull'immagine e i requisiti per usarla. Ciò include se l'immagine è Windows o Linux, specializzata o generalizzata, note sulla versione e requisiti minimi e massimi di memoria. Si tratta della definizione di un tipo di immagine. 
- Una **versione dell'immagine** viene usata per creare una macchina virtuale quando si usa una raccolta di immagini condivise. È possibile avere più versioni di un'immagine in base alle necessità del proprio ambiente. Quando si crea una macchina virtuale, la versione dell'immagine viene usata per creare nuovi dischi per la macchina virtuale. Le versioni delle immagini possono essere usate più volte.


## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, è necessario disporre di una raccolta [di immagini condivise esistente.](shared-images-cli.md) 

Per completare l'esempio in questo articolo, è necessario disporre di un'immagine gestita esistente di una macchina virtuale generalizzata. Per altre informazioni, vedere [Acquisire un'immagine gestita.](./linux/capture-image.md) Se l'immagine gestita contiene un disco dati, le dimensioni del disco dati non possono essere superiori a 1 TB.

Quando si esegue l'esercitazione, sostituire i nomi del gruppo di risorse e delle macchine virtuali dove necessario.



## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine

Poiché le immagini gestite sono sempre immagini generalizzate, si creerà una definizione di immagine usando `--os-state generalized` per un'immagine generalizzata.

I nomi delle definizioni di immagini possono essere costituiti da lettere maiuscole o minuscole, numeri, trattini e punti. 

Per altre informazioni sui valori che è possibile specificare per la definizione di immagine, vedere [Definizioni di immagini](./shared-image-galleries.md#image-definitions).

Creare una definizione di immagine nella raccolta usando [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

In questo esempio la definizione dell'immagine è *denominata myImageDefinition* ed è per un'immagine del sistema operativo Linux [generalizzata.](./shared-image-galleries.md#generalized-and-specialized-images) Per creare una definizione per le immagini usando un sistema operativo Windows, usare `--os-type Windows`. 

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state generalized
```


## <a name="create-the-image-version"></a>Creare una versione di immagine

Creare versioni usando [az image gallery create-image-version.](/cli/azure/sig/image-version#az_sig_image_version_create) È necessario passare l'ID dell'immagine gestita da usare come baseline per creare la versione dell'immagine. È possibile usare [az image list](/cli/azure/image?view#az_image_list) per ottenere gli ID per le immagini. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

In questo esempio la versione dell'immagine è *1.0.0* e si  creerà 1 replica nell'area Stati Uniti centro-meridionali e 1 replica nell'area Stati Uniti orientali *2* usando l'archiviazione con ridondanza della zona. Quando si scelgono le aree di destinazione per  la replica, tenere presente che è necessario includere anche l'area di origine come destinazione per la replica.

Passare l'ID dell'immagine gestita nel `--managed-image` parametro .


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> È necessario attendere che la creazione della versione dell'immagine venga interamente completata e replicata prima di poter usare la stessa immagine gestita o creare un'altra versione di immagine.
>
> È anche possibile archiviare tutte le repliche della versione dell'immagine in [Archiviazione con ridondanza](../storage/common/storage-redundancy.md) della zona aggiungendo `--storage-account-type standard_zrs` quando si crea la versione dell'immagine.
>

## <a name="next-steps"></a>Passaggi successivi

Creare una macchina virtuale da una [versione generalizzata dell'immagine](vm-generalized-image-version-cli.md).

Per informazioni su come fornire informazioni sul piano di acquisto, vedere Fornire Azure Marketplace informazioni sul piano di acquisto [durante la creazione di immagini.](marketplace-images.md)