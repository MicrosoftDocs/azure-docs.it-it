---
title: Distribuire il modulo GPU sul dispositivo Microsoft Azure Stack Edge Pro da Azure Marketplace | Microsoft Docs
description: Viene descritto come distribuire il modulo Internet delle cose abilitate per la GPU sul dispositivo GPU Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: e22014380d568b12e1e3bec751a75180d0760ab7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568410"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>Distribuire un modulo Internet delle cose abilitate per la GPU da Azure Marketplace sul dispositivo GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Questo articolo descrive come distribuire una GPU (Graphics Processing Unit) abilitata IoT Edge modulo da Azure Marketplace sul dispositivo Azure Stack Edge Pro. 

In questo articolo vengono illustrate le operazioni seguenti:
  - Preparare Azure Stack Edge Pro per eseguire un modulo GPU.
  - Scaricare e distribuire il modulo Internet delle cose abilitate per la GPU da Azure Marketplace.
  - Monitorare l'output del modulo.

## <a name="about-sample-module"></a>Informazioni sul modulo di esempio

Il modulo di esempio GPU in questo articolo include il codice di esempio PyTorch e TensorFlow benchmarking per la CPU rispetto alla GPU.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di avere:

- È possibile accedere a un dispositivo Azure Stack Edge a 1 nodo abilitato per GPU. Questo dispositivo viene attivato con una risorsa in Azure. 
- Il calcolo è stato configurato in questo dispositivo. Eseguire la procedura descritta in [esercitazione: configurare il calcolo nel dispositivo Azure stack Edge](azure-stack-edge-gpu-deploy-configure-compute.md).
- Le risorse di sviluppo seguenti in un client Windows:
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Estensione Azure IOT Edge per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).   


## <a name="get-module-from-azure-marketplace"></a>Ottenere il modulo da Azure Marketplace

1. Esplora tutte le [app in Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

    ![Esplora le app in Azure Marketplace](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. Cercare Introduzione **alle GPU**.

    ![Modulo Cerca GPU di esempio](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. Selezionare **Scarica adesso**.

    ![Ottenere il modulo di esempio](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. Selezionare **continua** per confermare le condizioni per l'utilizzo e l'informativa sulla privacy del provider. 

    ![Ottenere il modulo 2 di esempio](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Scegliere la sottoscrizione usata per distribuire il dispositivo Azure Stack Edge Pro.

    ![Seleziona sottoscrizione](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Immettere il nome del servizio hub Internet che è stato creato al momento della configurazione del dispositivo Azure Stack Edge Pro. Per trovare il nome del servizio hub Internet delle cose, passare alla risorsa Azure Stack Edge associata al dispositivo in portale di Azure. 

    1. Nel riquadro a sinistra opzioni menu passare a **servizi perimetrali > IOT Edge**. 

        ![Visualizza la configurazione di calcolo](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. Passare a **Proprietà**. 

        1. Prendere nota del servizio hub Internet che è stato creato quando è stato configurato il calcolo nel dispositivo Azure Stack Edge Pro.
        2. Prendere nota del nome del dispositivo IoT Edge creato durante la configurazione di calcolo. Questo nome verrà usato nel passaggio successivo.

        ![Configurazione di calcolo Edge](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. Scegliere **Distribuisci in un dispositivo**.

11. Immettere il nome del dispositivo IoT Edge o selezionare **trova dispositivo** per cercare tra i dispositivi registrati con l'hub.

    ![Trova dispositivo](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. Selezionare **Crea** per continuare il processo standard di configurazione di un manifesto di distribuzione, ad esempio l'aggiunta di altri moduli, se lo si desidera. I dettagli per il nuovo modulo, ad esempio URI dell'immagine, opzioni di creazione e proprietà desiderate, sono predefiniti, ma possono essere modificati.

    ![Selezionare Crea](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. Verificare che il modulo sia distribuito nell'hub Internet delle cose nel portale di Azure. Selezionare il dispositivo, selezionare **imposta moduli** per elencare il modulo nella sezione **moduli IOT Edge** .

    ![Selezionare Crea 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>Monitorare il modulo  

1. Nel riquadro comandi di VS Code eseguire **Azure IoT Hub: Select IoT Hub** (Hub IoT di Azure: Seleziona l'hub IoT).

2. Scegliere la sottoscrizione e l'hub IoT che contiene il dispositivo IoT Edge che si vuole configurare. In questo caso, selezionare la sottoscrizione usata per distribuire il dispositivo Azure Stack Edge Pro e selezionare il dispositivo IoT Edge creato per il dispositivo Azure Stack Edge Pro. Questo errore si verifica quando si configura il calcolo tramite il portale di Azure nei passaggi precedenti.

3. In Esplora VS Code espandere la sezione Hub Azure. In **dispositivi** dovrebbe essere visualizzato il dispositivo IOT Edge corrispondente al dispositivo Azure stack Edge Pro. 

    1. Selezionare il dispositivo, fare clic con il pulsante destro del mouse e scegliere **Avvia monitoraggio endpoint evento predefinito**.
  
        ![Avviare il monitoraggio](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Passare a **dispositivi > moduli** e dovrebbe essere visualizzato il **modulo GPU** in esecuzione.

    3. Il terminale VS Code dovrebbe anche visualizzare gli eventi dell'hub Internet come output di monitoraggio per il dispositivo Pro Azure Stack Edge.

        ![Monitoraggio dell'output](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        È possibile notare che il tempo impiegato per eseguire lo stesso set di operazioni (5000 iterazioni della trasformazione forma) dalla GPU è molto inferiore rispetto a quello per la CPU.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come [configurare GPU per l'uso di un modulo](./azure-stack-edge-gpu-configure-gpu-modules.md).