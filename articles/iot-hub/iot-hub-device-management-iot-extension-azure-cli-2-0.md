---
title: Gestione dei dispositivi Azure IoT con l'estensione IoT dell'interfaccia della riga di comando di Azure | Microsoft Docs
description: Usare l'estensione IoT dell'interfaccia della riga di comando di Azure per la gestione dei dispositivi dell'hub IoT di Azure, con opzioni di gestione come i metodi diretti e le proprietà desiderate in dispositivi gemelli.
author: chrissie926
manager: ''
keywords: gestione dispositivi iot azure, gestione dispositivi hub iot azure, gestione dispositivi iot, gestione dispositivi hub iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: a5bc7e195efd62f430fdf2aa0cb606dbcff79528
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567197"
---
# <a name="use-the-iot-extension-for-azure-cli-for-azure-iot-hub-device-management"></a>Usare l'estensione IoT per l'interfaccia della riga di comando di Azure per la gestione dei dispositivi dell'hub IoT di Azure

![Diagramma end-to-end](media/iot-hub-get-started-e2e-diagram/2.png)

Questo articolo illustra come usare l'estensione IoT per l'interfaccia della riga di comando di Azure con varie opzioni di gestione nel computer di sviluppo. [L'estensione IoT per l'interfaccia della riga di](https://github.com/Azure/azure-iot-cli-extension) comando di Azure è un'estensione IoT open source che aggiunge alle funzionalità dell'interfaccia della riga di comando di [Azure.](/cli/azure/overview) L'interfaccia della riga di comando di Azure include comandi per l'interazione con Azure Resource Manager e gli endpoint di gestione. Ad esempio, è possibile usare l'interfaccia della riga di comando di Azure per creare una macchina virtuale o un hub IoT di Azure. Un'estensione dell'interfaccia della riga di comando di Azure consente a un servizio di Azure di aumentare le potenzialità dell'interfaccia e all'utente di accedere a funzionalità aggiuntive specifiche del servizio. L'estensione IoT offre agli sviluppatori IoT l'accesso da riga di comando a tutte le funzionalità del servizio Device Provisioning in hub IoT, IoT Edge e Device Provisioning in hub IoT.

| Opzione di gestione          | Attività  |
|----------------------------|-----------|
| Metodi diretti             | Far eseguire un'attività al dispositivo, quale l'avvio o l'arresto dell'invio di messaggi o il riavvio del dispositivo.                                        |
| Proprietà desiderate del dispositivo gemello    | Impostare determinati stati di un dispositivo, ad esempio impostare la luce verde per un LED o impostare l'intervallo di invio dei dati di telemetria su 30 minuti.         |
| Proprietà segnalate del dispositivo gemello   | Ottenere lo stato restituito da un dispositivo. Ad esempio, il dispositivo segnala che il LED sta lampeggiando.                                    |
| Tag dei dispositivi gemelli                  | Archiviare i metadati specifici del dispositivo nel cloud, ad esempio il percorso di distribuzione di un distributore automatico.                         |
| Query del dispositivo gemello        | Eseguire una query su tutti i dispositivi gemelli per recuperare i gemelli con condizioni arbitrarie, ad esempio identificando i dispositivi disponibili per l'uso. |

Per altre informazioni sulle differenze e sull'uso di queste opzioni, vedere [Device-to-cloud communication guidance](iot-hub-devguide-d2c-guidance.md) (Indicazioni sulla comunicazione da dispositivo a cloud) e [Cloud-to-device communication guidance](iot-hub-devguide-c2d-guidance.md) (Indicazioni sulla comunicazione da cloud a dispositivo).

I dispositivi gemelli sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei dispositivi (metadati, configurazioni e condizioni). L'hub IoT rende permanente un dispositivo gemello per ogni dispositivo che si connette. Per altre informazioni sui dispositivi gemelli, vedere [Introduzione ai dispositivi gemelli](iot-hub-node-node-twin-getstarted.md).

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="prerequisites"></a>Prerequisiti

* Completare [l'esercitazione sul simulatore online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) o una delle esercitazioni sul dispositivo. Ad esempio, è possibile passare a [Raspberry Pi con node.js](iot-hub-raspberry-pi-kit-node-get-started.md) o a una delle guide introduttive [Inviare](quickstart-send-telemetry-dotnet.md) dati di telemetria. Questi articoli riguardano i requisiti seguenti:

  * Una sottoscrizione di Azure attiva.
  * Un hub IoT di Azure nella sottoscrizione.
  * Un'applicazione client che invia messaggi ad Azure IoT hub.

* Verificare che il dispositivo sia in esecuzione con l'applicazione client durante questa esercitazione.

* [Python 2.7x o Python 3.x](https://www.python.org/downloads/)

* L'interfaccia della riga di comando di Azure. Se è necessario eseguire l'installazione, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). La versione dell'interfaccia della riga di comando di Azure deve essere 2.0.70 o successiva. Usare il comando `az –version` per verificare.

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

* Installare l'estensione IoT. Il modo più semplice è quello di eseguire `az extension add --name azure-iot`. Il [file Leggimi dell'estensione IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) illustra diverse modalità per installare l'estensione.

## <a name="sign-in-to-your-azure-account"></a>Accedere con l'account Azure

Accedere al proprio account di Azure usando il comando seguente:

```azurecli
az login
```

## <a name="direct-methods"></a>Metodi diretti

```azurecli
az iot hub invoke-device-method --device-id <your device id> \
  --hub-name <your hub name> \
  --method-name <the method name> \
  --method-payload <the method payload>
```

## <a name="device-twin-desired-properties"></a>Proprietà desiderate nel dispositivo gemello

Impostare l'intervallo di proprietà desiderato su 3000 eseguendo il comando seguente:

```azurecli
az iot hub device-twin update -n <your hub name> \
  -d <your device id> --set properties.desired.interval = 3000
```

Questa proprietà può essere letta dal dispositivo.

## <a name="device-twin-reported-properties"></a>Proprietà segnalate del dispositivo gemello

Ottenere le proprietà segnalate del dispositivo eseguendo il comando seguente:

```azurecli
az iot hub device-twin show -n <your hub name> -d <your device id>
```

Una delle proprietà segnalate dai dispositivi gemelli è $metadata.$lastUpdated, che mostra l'ultima volta che l'app per dispositivi ha aggiornato il set di proprietà segnalato.

## <a name="device-twin-tags"></a>Tag del dispositivo gemello

Visualizzare i tag e le proprietà del dispositivo eseguendo il comando seguente:

```azurecli
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Aggiungere il campo role = temperature&humidity al dispositivo eseguendo il comando seguente:

```azurecli
az iot hub device-twin update \
  --hub-name <your hub name> \
  --device-id <your device id> \
  --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="device-twin-queries"></a>Query del dispositivo gemello

Eseguire una query su tutti i dispositivi con il tag role = 'temperature&humidity' eseguendo il comando seguente:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Eseguire una query su tutti i dispositivi meno quelli con il tag role = 'temperature&humidity' eseguendo il comando seguente:

```azurecli
az iot hub query --hub-name <your hub name> \
  --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Passaggi successivi

È stato appreso come monitorare i messaggi da dispositivo a cloud e inviare messaggi da cloud a dispositivo tra il dispositivo IoT e l'hub IoT di Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]