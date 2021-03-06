---
title: 'Guida introduttiva: libreria client per il riconoscimento ottico dei caratteri per Python'
description: Questa Guida introduttiva illustra come iniziare a usare la libreria client per il riconoscimento ottico dei caratteri per Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 230934ea3d4a8a970ca1fca46160b96e3f2234e8
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073434"
---
<a name="HOLTop"></a>

Usare la libreria client per il riconoscimento ottico dei caratteri per leggere testo stampato e scritto con l'API Read.

[Documentazione di riferimento](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-computervision) | [Pacchetto (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-computervision/) | [Esempi](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
  * L'installazione di Python deve includere [PIP](https://pip.pypa.io/en/stable/). È possibile verificare se PIP è stato installato eseguendo `pip --version` nella riga di comando. Ottenere PIP installando la versione più recente di Python.
* Dopo aver creato la sottoscrizione di Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" creare una risorsa di visione artificiale "  target="_blank"> creare una risorsa Visione artificiale </a> nel portale di Azure per ottenere la chiave e l'endpoint. Al termine della distribuzione, fare clic su **Vai alla risorsa**.

    * La chiave e l'endpoint della risorsa creata sono necessari per connettere l'applicazione al servizio Visione artificiale. La chiave e l'endpoint verranno incollati nel codice riportato di seguito nell'argomento di avvio rapido.
    * È possibile usare il piano tariffario gratuito (`F0`) per provare il servizio ed eseguire in un secondo momento l'aggiornamento a un livello a pagamento per la produzione.

## <a name="setting-up"></a>Configurazione

### <a name="install-the-client-library"></a>Installare la libreria client

È possibile installare la libreria client con:

```console
pip install --upgrade azure-cognitiveservices-vision-computervision
```

Installare anche la libreria Pillow.

```console
pip install pillow
```

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

> [!TIP]
> Si vuole visualizzare l'intero file di codice dell'argomento di avvio rapido? È possibile trovarlo [in GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py), che contiene gli esempi di codice di questo argomento.

Creare un nuovo file Python, ad esempio &mdash;*quickstart-file.py*. Aprirlo quindi nell'editor preferito o nell'IDE.

### <a name="find-the-subscription-key-and-endpoint"></a>Trovare la chiave e l'endpoint della sottoscrizione

Accedere al portale di Azure. Se la risorsa Visione artificiale creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. È possibile trovare la chiave e l'endpoint della sottoscrizione nella pagina **chiave ed endpoint** della risorsa in **Gestione risorse**. 

Creare variabili per la chiave e l'endpoint della sottoscrizione Visione artificiale. Incollare la chiave e l'endpoint della sottoscrizione nel codice seguente, dove indicato. Il formato dell'endpoint del Visione artificiale è `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Ricordarsi di rimuovere la chiave di sottoscrizione dal codice al termine dell'operazione senza pubblicarla. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, ad esempio [Azure Key Vault](../../../../key-vault/general/overview.md).

> [!div class="nextstepaction"]
> [Il client è stato configurato](?success=set-up-client#object-model) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=set-up-client)

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di OCR Python SDK.

|Nome|Descrizione|
|---|---|
|[ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin)| Questa classe gestisce direttamente tutte le operazioni per le immagini, come l'analisi, il rilevamento del testo e la generazione di anteprime.|
| [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient) | Questa classe è necessaria per tutte le funzionalità di Visione artificiale. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per produrre istanze di altre classi. Implementa **ComputerVisionClientOperationsMixin**.|
|[VisualFeatureTypes](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Questa enumerazione definisce i diversi tipi di analisi delle immagini che è possibile eseguire in un'operazione di analisi standard. Specificare un set di valori di **VisualFeatureTypes** a seconda delle esigenze. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice illustrano come eseguire le attività seguenti con la libreria client OCR per Python:

* [Autenticare il client](#authenticate-the-client)
* [Leggere il testo stampato e scritto a mano](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di un client con l'endpoint e la chiave. Creare un oggetto [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials) con la chiave e usarlo con l'endpoint per creare un oggetto [ComputerVisionClient](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient).

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_client)]

> [!div class="nextstepaction"]
> [Il client è stato autenticato](?success=authenticate-client#read-printed-and-handwritten-text) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Leggere il testo stampato e scritto a mano

Il servizio OCR può leggere il testo visibile in un'immagine e convertirlo in un flusso di caratteri. Questa operazione viene eseguita in due parti.

### <a name="call-the-read-api"></a>Chiamare l'API di lettura

Prima di tutto, usare il codice seguente per chiamare il metodo **read** per l'immagine specificata. Viene restituito un ID operazione e viene avviato un processo asincrono per leggere il contenuto dell'immagine.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_call)]

> [!TIP]
> È anche possibile leggere il testo da un'immagine locale. Vedere i metodi [ComputerVisionClientOperationsMixin](/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.operations.computervisionclientoperationsmixin), ad esempio **read_in_stream**. In alternativa, per gli scenari con immagini locali, vedere il codice di esempio in [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).

### <a name="get-read-results"></a>Ottenere risultati della lettura

Ottenere quindi l'ID operazione restituito dalla chiamata a **read** e usarlo per eseguire una query sul servizio per ottenere i risultati dell'operazione. Il codice seguente controlla l'operazione a intervalli di un secondo fino a quando non vengono restituiti i risultati. Stampa quindi i dati di testo estratti nella console.

[!code-python[](~/cognitive-services-quickstart-code/python/ComputerVision/ComputerVisionQuickstart.py?name=snippet_read_response)]

> [!div class="nextstepaction"]
> [Il testo è stato letto](?success=read-printed-handwritten-text#run-the-application) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `python` nel file quickstart.

```console
python quickstart-file.py
```

> [!div class="nextstepaction"]
> [L'applicazione è stata eseguita](?success=run-the-application#clean-up-resources) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=run-the-application)

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Le risorse sono state pulite](?success=clean-up-resources#next-steps) [Si è verificato un problema](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Section=clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva si è appreso come installare la libreria client OCR e usare l'API Read. Quindi, altre informazioni sulle funzionalità dell'API di lettura.

> [!div class="nextstepaction"]
>[Chiamare l'API di lettura](../../Vision-API-How-to-Topics/call-read-api.md)

* [Panoramica dell'OCR](../../overview-ocr.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/ComputerVision/ComputerVisionQuickstart.py).
