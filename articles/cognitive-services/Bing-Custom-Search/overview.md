---
title: Informazioni sull'API Ricerca personalizzata Bing
titleSuffix: Azure Cognitive Services
description: L'API Ricerca personalizzata Bing permette di creare esperienze di ricerca personalizzate per specifici argomenti di interesse.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: e70d4d83fcd9641fa86f013688b5a3a6e3652919
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338402"
---
# <a name="what-is-the-bing-custom-search-api"></a>Informazioni sull'API Ricerca personalizzata Bing

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020**, è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

L'API Ricerca personalizzata Bing permette di creare esperienze di ricerca personalizzate senza annunci per specifici argomenti di interesse. È possibile specificare i domini e le pagine Web per la ricerca in Bing, nonché evidenziare o innalzare o abbassare di livello contenuti specifici in modo da creare una visualizzazione personalizzata del Web e aiutare gli utenti a trovare rapidamente i risultati pertinenti. 

## <a name="features"></a>Funzionalità

|Funzionalità  |Descrizione  |
|---------|---------|
|[Suggerimenti per la ricerca in tempo reale personalizzati](define-custom-suggestions.md)     | Suggerimenti per la ricerca che possono essere visualizzati come elenchi a discesa man mano che l'utente digita.       | 
|[Esperienze di ricerca di immagini personalizzate](get-images-from-instance.md)     | Permettono agli utenti di cercare immagini dai domini e dai siti Web specificati nell'istanza di ricerca personalizzata.        |        
|[Esperienze di ricerca di video personalizzate](get-videos-from-instance.md)     | Permettono agli utenti di cercare video dai domini e dai siti specificati nell'istanza di ricerca personalizzata.        |    
|[Condivisione dell'istanza di ricerca personalizzata](share-your-custom-search.md)     | Possibilità di collaborare alla modifica e alla verifica dell'istanza di ricerca condividendola con i membri del team.        | 
|[Configurare un'interfaccia utente per le applicazioni e i siti Web](hosted-ui.md)     | Offre un'interfaccia utente ospitata che è possibile integrare facilmente in applicazioni Web e pagine Web come frammento di codice JavaScript.        | 
## <a name="workflow"></a>Flusso di lavoro

È possibile creare un'istanza di ricerca personalizzata usando il portale [Ricerca personalizzata Bing](https://customsearch.ai). Il portale permette di creare un'istanza di ricerca personalizzata che specifica i domini, i siti Web e le pagine Web in cui si vuole eseguire la ricerca con Bing, insieme a quelli da escludere per la ricerca. È anche possibile usare il portale per: visualizzare in anteprima l'esperienza di ricerca, modificare le priorità di ricerca fornite dall'API e, facoltativamente, configurare un'interfaccia utente da visualizzare nei siti Web e nelle applicazioni.

Dopo aver creato l'istanza di ricerca, è possibile integrarla (insieme, facoltativamente, a un'interfaccia utente) nel sito Web o nell'applicazione chiamando l'API Ricerca personalizzata Bing:

![Immagine che mostra che è possibile connettersi a Ricerca personalizzata Bing tramite l'API](media/BCS-Overview.png "Come funziona Ricerca personalizzata Bing.")


## <a name="next-steps"></a>Passaggi successivi

Per iniziare rapidamente, vedere [Create your first Bing Custom Search instance](quick-start.md) (Creare la prima istanza di Ricerca personalizzata Bing).

Per informazioni dettagliate sulla personalizzazione dell'istanza di ricerca, vedere [Definire un'istanza di ricerca personalizzata](define-your-custom-view.md).

Per informazioni sull'uso dei risultati delle ricerche nei servizi e nelle applicazioni, vedere [Requisiti per l'uso e la visualizzazione di Bing](../bing-web-search/use-display-requirements.md).

Per esplorare le altre API disponibili, visitare la [pagina dell'hub dell'API Ricerca Bing](../bing-web-search/overview.md).

È consigliabile acquisire familiarità con il contenuto di riferimento per ognuno degli endpoint di ricerca personalizzata. La documentazione di riferimento contiene gli endpoint, le intestazioni e i parametri di query da usare per richiedere risultati della ricerca. Include anche le definizioni degli oggetti della risposta.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [API Ricerca personalizzata](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [API Ricerca personalizzata immagini](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [API Video personalizzata](/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [API Suggerimenti automatici personalizzati](/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)