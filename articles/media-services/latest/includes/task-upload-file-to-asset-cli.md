---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88657083"
---
<!-- ### Upload files with the CLI -->

Il comando seguente carica un file singolo.  

Modificare i valori seguenti:

1. Modificare `/path/to/file` con il percorso locale del file.  
1. Modificare `MyContainer` con un asset creato in precedenza usando l'ID asset, non il nome.
1. Modificare `MyBlob` con il nome che si vuole usare per il file caricato.
1. Modificare `MyStorageAccountName` con il nome dell'account di archiviazione usato.
1. Modificare `MyStorageAccountKey` con la chiave di accesso per l'account di archiviazione.

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```
