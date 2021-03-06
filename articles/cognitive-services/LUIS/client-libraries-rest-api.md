---
title: 'Avvio rapido: Librerie client e API REST di Language Understanding (LUIS) SDK'
description: Creare un'app LUIS ed eseguire query con le librerie client e l'API REST di LUIS SDK.
ms.topic: quickstart
ms.date: 03/29/2021
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: Azure, intelligenza artificiale, ia, elaborazione del linguaggio naturale, nlp, LUIS, azure luis, natural language understanding, chatbot di ia, strumento di creazione di chatbot, comprensione del linguaggio naturale
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: ca45266ce4b8ca784c3d54aafb80a66efaf2a1da
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278916"
---
# <a name="quickstart-language-understanding-luis-client-libraries-and-rest-api"></a>Avvio rapido: Librerie client e API REST di Language Understanding (LUIS)

Guida di avvio rapido su come creare ed eseguire query su un'app di intelligenza artificiale (IA) di Azure LUIS con le librerie client di LUIS SDK con C#, Python o JavaScript. È anche possibile usare cURL per inviare richieste con l'API REST.

Language Understanding (LUIS) consente di applicare l'elaborazione del linguaggio naturale (NLP, Natural Language Processing) al testo in linguaggio naturale della conversazione di un utente per prevedere il significato generale ed estrarre informazioni pertinenti e dettagliate.

* La libreria client e l'API REST di **creazione** consentono di creare, modificare, eseguire il training e pubblicare l'app LUIS.
* La libreria client e l'API REST del **runtime di previsione** consentono di eseguire query sull'app pubblicata.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

::: zone pivot="rest-api"
[!INCLUDE [LUIS development with REST API](./includes/rest-api.md)]
::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eliminare l'app dal [portale LUIS](https://www.luis.ai) ed eliminare le risorse di Azure dal [portale di Azure](https://portal.azure.com/).

Se si usa l'API REST, eliminare il file `ExampleUtterances.JSON` dal file system una volta completata la guida di avvio rapido.

## <a name="troubleshooting"></a>Risoluzione dei problemi

* Autenticazione alla libreria client: gli errori di autenticazione indicano in genere che sono stati usati un endpoint e una chiave non corretti. Per comodità, questa guida di avvio rapido usa la chiave di creazione e l'endpoint per il runtime di previsione, ma funziona solo se non è già stata usata la quota mensile. Se non è possibile usare la chiave di creazione e l'endpoint, è necessario usare la chiave e l'endpoint del runtime di previsione per accedere alla libreria client di Prediction Runtime SDK.
* Creazione di entità: se viene restituito un errore durante la creazione dell'entità di Machine Learning annidata usata in questa esercitazione, assicurarsi di aver copiato il codice senza averlo modificato per creare un'altra entità.
* Creazione di espressioni di esempio: se viene restituito un errore durante la creazione dell'espressione di esempio etichettata usata in questa esercitazione, assicurarsi di aver copiato il codice senza averlo modificato per creare un altro esempio etichettato.
* Training: un eventuale errore di training in genere indica un'app vuota (senza finalità con espressioni di esempio) o un'app con finalità o entità in formato non valido.
* Errori vari: poiché il codice chiama le librerie client con oggetti JSON e di testo, assicurarsi di non aver modificato il codice.

Altri errori: se viene ricevuto un errore non incluso nell'elenco precedente, segnalarlo inviando un feedback nella parte inferiore della pagina. Includere il linguaggio di programmazione e la versione delle librerie client installate.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Sviluppo di app iterative per LUIS](./luis-concept-app-iteration.md)