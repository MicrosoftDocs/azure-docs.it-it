---
title: Creare la risorsa di Personalizza esperienze
description: Questo articolo illustra come creare una risorsa di personalizzazione nel portale di Azure per ogni ciclo di feedback.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0c75d917f1abe72af2f4aa56b0f67dbb7bcd24a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "91303557"
---
# <a name="create-a-personalizer-resource"></a>Creare una risorsa di personalizzazione

Una risorsa di personalizzazione è la stessa del ciclo di apprendimento della personalizzazione. Viene creata una singola risorsa, o un ciclo di apprendimento, per ogni dominio soggetto o area di contenuto. Non usare più aree di contenuto nello stesso ciclo perché questa operazione consentirà di confondere il ciclo di apprendimento e fornire stime insufficienti.

Se si vuole che il Personalizzatore selezioni il contenuto migliore per più di un'area di contenuto di una pagina Web, usare un ciclo di apprendimento diverso per ciascuno di essi.


## <a name="create-a-resource-in-the-azure-portal"></a>Creare una risorsa nel portale di Azure

Creare una risorsa di Personalizza esperienze per ogni ciclo di feedback.

1. Accedere al [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Il collegamento precedente consente di accedere alla pagina **Crea** per il servizio Personalizza esperienze.
1. Immettere il nome del servizio, selezionare una sottoscrizione, una località, un piano tariffario e un gruppo di risorse.

    > [!div class="mx-imgBorder"]
    > ![Usare portale di Azure per creare una risorsa di personalizzazione, definita anche ciclo di apprendimento.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Selezionare **Crea** per creare la risorsa.

1. Dopo che la risorsa è stata distribuita, selezionare il pulsante **Vai alla risorsa** per passare alla risorsa di personalizzazione.

1. Selezionare la pagina **avvio rapido** per la risorsa, quindi copiare i valori per l'endpoint e la chiave. Per usare le API Rank e Reward sono necessari sia l'endpoint che la chiave della risorsa.

1. Selezionare la pagina di **configurazione** per la nuova risorsa per [configurare il ciclo di apprendimento](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Creare una risorsa con l'interfaccia della riga di comando di Azure

1. Accedere all'interfaccia della riga di comando di Azure con il comando seguente:

    ```azurecli-interactive
    az login
    ```

1. Creare un gruppo di risorse, un raggruppamento logico per gestire tutte le risorse di Azure che si intende usare con la risorsa di personalizzazione.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Creare una nuova risorsa di personalizzazione, _Learning loop_, con il comando seguente per un gruppo di risorse esistente.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Viene restituito un oggetto JSON, che include l' **endpoint della risorsa**.

1. Usare il comando dell'interfaccia della riga di comando di Azure seguente per ottenere la **chiave di risorsa**.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Per usare le API Rank e Reward sono necessari sia l'endpoint che la chiave della risorsa.

## <a name="next-steps"></a>Passaggi successivi

* [Configura](how-to-settings.md) Ciclo di apprendimento della personalizzazione
