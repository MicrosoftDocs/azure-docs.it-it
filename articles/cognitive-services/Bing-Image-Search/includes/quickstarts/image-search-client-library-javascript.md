---
title: Avvio rapido della libreria client JavaScript di Ricerca immagini Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 037137cf5a6e4ddd66fc15e8ad9775ea77177ef6
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94625227"
---
Usare questa guida di avvio rapido per effettuare la prima ricerca di immagini tramite la libreria client di Ricerca immagini Bing, che consiste in un wrapper per l'API e contiene le stesse funzionalità. Questa semplice applicazione JavaScript invia una query di ricerca immagine, analizza la risposta JSON e visualizza l'URL della prima immagine restituita.

Il codice sorgente per questo esempio è disponibile in [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) con altre annotazioni e informazioni sulla gestione degli errori.

## <a name="prerequisites"></a>Prerequisiti

* La versione più recente di [Node.js](https://nodejs.org/en/download/).
* L'[SDK di Ricerca immagini Bing per JavaScript](https://www.npmjs.com/package/@azure/cognitiveservices-imagesearch)
     *  Per installarlo, eseguire `npm install @azure/cognitiveservices-imagesearch`
* La classe `CognitiveServicesCredentials` del pacchetto `@azure/ms-rest-azure-js` per autenticare il client.
     * Per installarlo, eseguire `npm install @azure/ms-rest-azure-js`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creare e inizializzare l'applicazione

1. Creare un nuovo file JavaScript nell'IDE o editor preferito e impostare severità, https e altri requisiti.

    ```javascript
    'use strict';
    const ImageSearchAPIClient = require('@azure/cognitiveservices-imagesearch');
    const CognitiveServicesCredentials = require('@azure/ms-rest-azure-js').CognitiveServicesCredentials;
    ```

2. Nel metodo principale del progetto, creare variabili per la chiave di sottoscrizione valida, i risultati immagini che devono essere restituiti da Bing e un termine di ricerca. Creare quindi un'istanza del client di ricerca delle immagini usando la chiave.

    ```javascript
    //replace this value with your valid subscription key.
    let serviceKey = "ENTER YOUR KEY HERE";

    //the search term for the request
    let searchTerm = "canadian rockies";

    //instantiate the image search client
    let credentials = new CognitiveServicesCredentials(serviceKey);
    let imageSearchApiClient = new ImageSearchAPIClient(credentials);

    ```

## <a name="create-an-asynchronous-helper-function"></a>Creare una funzione helper asincrone

1. Creare una funzione per chiamare il client in modo asincrono e restituire la risposta dal servizio di ricerca immagini Bing.
    ```javascript
    //a helper function to perform an async call to the Bing Image Search API
    const sendQuery = async () => {
        return await imageSearchApiClient.imagesOperations.search(searchTerm);
    };
    ```
   ## <a name="send-a-query-and-handle-the-response"></a>Inviare una query e gestire la risposta

1. Chiamare la funzione helper e gestire il relativo `promise` per analizzare i risultati di immagine restituiti con la risposta.

    Se la risposta contiene i risultati della ricerca, archiviare il primo risultato e stamparne i dettagli, ad esempio l'URL di anteprima e l'URL originale, insieme al numero totale di immagini restituite.
    ```javascript
    sendQuery().then(imageResults => {
        if (imageResults == null) {
        console.log("No image results were found.");
        }
        else {
            console.log(`Total number of images returned: ${imageResults.value.length}`);
            let firstImageResult = imageResults.value[0];
            //display the details for the first image result. After running the application,
            //you can copy the resulting URLs from the console into your browser to view the image.
            console.log(`Total number of images found: ${imageResults.value.length}`);
            console.log(`Copy these URLs to view the first image returned:`);
            console.log(`First image thumbnail url: ${firstImageResult.thumbnailUrl}`);
            console.log(`First image content url: ${firstImageResult.contentUrl}`);
        }
      })
      .catch(err => console.error(err))
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione sull'app a singola pagina di Ricerca immagini Bing](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Vedere anche

* [Informazioni su Ricerca immagini Bing](../../overview.md)
* [Provare una demo interattiva online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)
* [Esempi Node.js per SDK di Servizi cognitivi di Azure](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
* [Documentazione di Servizi cognitivi di Azure](../../../index.yml)
* [Informazioni di riferimento per l'API Ricerca immagini Bing](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)