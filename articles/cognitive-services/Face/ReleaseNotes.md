---
title: Novità del servizio Viso
titleSuffix: Azure Cognitive Services
description: Le note sulla versione per il servizio viso includono una cronologia delle modifiche di versione per varie versioni.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
ms.custom: contperf-fy21q3
ms.openlocfilehash: c580828d29e92ecef7ecc73b8f3e5843c3ecd23d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078881"
---
# <a name="whats-new-in-face-service"></a>Novità del servizio Viso

Il servizio Azure Face viene aggiornato su base continuativa. Usare questo articolo per rimanere sempre aggiornati sui miglioramenti delle funzionalità, sulle correzioni e sugli aggiornamenti della documentazione.

## <a name="february-2021"></a>2021 febbraio

### <a name="new-face-api-detection-model"></a>Nuovo modello di rilevamento API Viso
* Il nuovo modello di rilevamento 03 è il modello di rilevamento più accurato attualmente disponibile. Se sei un nuovo cliente, ti consigliamo di usare questo modello. Rilevamento 03 migliora sia il richiamo che la precisione sui visi più piccoli presenti nelle immagini (64x64 pixel). Altri miglioramenti includono una riduzione complessiva nei falsi positivi e un miglioramento del rilevamento sugli orientamenti a faccia ruotati. La combinazione del rilevamento 03 con il nuovo modello di riconoscimento 04 fornirà anche un'accuratezza del riconoscimento migliorata. Per altri dettagli, vedere [specificare un modello di rilevamento viso](./face-api-how-to-topics/specify-detection-model.md) .
### <a name="new-detectable-face-attributes"></a>Nuovi attributi viso rilevabili
* L' `faceMask` attributo è disponibile con il modello di rilevamento 03 più recente, insieme all'attributo aggiuntivo `"noseAndMouthCovered"` che rileva se la maschera viso viene indossata come previsto, coprendo sia il muso che la bocca. Per usare la funzionalità di rilevamento più recente della maschera, gli utenti devono specificare il modello di rilevamento nella richiesta dell'API: assegnare la versione del modello con il parametro _detectionModel_ a `detection_03` . Per altri dettagli, vedere [specificare un modello di rilevamento viso](./face-api-how-to-topics/specify-detection-model.md) .
### <a name="new-face-api-recognition-model"></a>Nuovo modello di riconoscimento API Viso
* Il nuovo modello di riconoscimento 04 è il modello di riconoscimento più accurato attualmente disponibile. Se sei un nuovo cliente, ti consigliamo di usare questo modello per la verifica e l'identificazione. Migliora l'accuratezza del riconoscimento 03, incluso un riconoscimento migliorato per gli utenti registrati che indossano i visi (maschere chirurgiche, maschere N95, maschere di abbigliamento). Ora i clienti possono creare esperienze utente sicure e senza problemi che rilevano se un utente registrato sta indossando una copertura con il modello di rilevamento 03 più recente e riconosce chi è il modello di riconoscimento 04 più recente. Per altri dettagli, vedere [specificare un modello per il riconoscimento della faccia](./face-api-how-to-topics/specify-recognition-model.md) .


## <a name="january-2021"></a>Gennaio 2021
### <a name="mitigate-latency"></a>Attenuazione della latenza
* Il team viso ha pubblicato un nuovo articolo che illustra in dettaglio le potenziali cause della latenza quando si usa il servizio e le possibili strategie di mitigazione. Vedere [attenuare la latenza quando si usa il servizio Face](./face-api-how-to-topics/how-to-mitigate-latency.md).

## <a name="december-2020"></a>Dicembre 2020
### <a name="customer-configuration-for-face-id-storage"></a>Configurazione del cliente per l'archiviazione con ID viso
* Mentre il servizio viso non archivia le immagini dei clienti, le funzionalità della faccia estratte verranno archiviate nel server. L'ID del viso è un identificatore della funzionalità della faccia e verrà usato in un tipo di identificazione [della faccia,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)una [Verifica della faccia](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)e un [aspetto simile](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237). Le funzionalità dei visi archiviati scadranno e verranno eliminate 24 ore dopo la chiamata di rilevamento originale. I clienti possono ora determinare il periodo di memorizzazione nella cache degli ID di questi visi. Il valore massimo è ancora fino a 24 ore, ma ora è possibile impostare un valore minimo di 60 secondi. I nuovi intervalli di tempo per gli ID facciali memorizzati nella cache sono valori compresi tra 60 secondi e 24 ore. Per altri dettagli, vedere la Guida di riferimento alle API per il [rilevamento dei visi](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (il parametro *faceIdTimeToLive* ).

## <a name="november-2020"></a>Novembre 2020
### <a name="sample-face-enrollment-app"></a>App di registrazione Face di esempio
* Il team ha pubblicato un'app di iscrizione di esempio per illustrare le procedure consigliate per la definizione di un consenso significativo e la creazione di sistemi di riconoscimento viso ad alta precisione tramite registrazioni di alta qualità. L'esempio Open Source è disponibile nella Guida all'app per la [creazione di una registrazione](build-enrollment-app.md) e in [GitHub](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample), pronto per gli sviluppatori per la distribuzione o la personalizzazione. 

## <a name="august-2020"></a>Agosto 2020
### <a name="customer-managed-encryption-of-data-at-rest"></a>Crittografia dei dati inattivi gestita dal cliente
* Il servizio viso crittografa automaticamente i dati in modo permanente nel cloud. La crittografia del servizio viso protegge i dati per soddisfare gli impegni di sicurezza e conformità dell'organizzazione. Per impostazione predefinita, la sottoscrizione usa chiavi di crittografia gestite da Microsoft. È disponibile anche una nuova opzione per gestire la sottoscrizione con chiavi personalizzate denominate chiavi gestite dal cliente (CMK). Ulteriori informazioni sono disponibili in [chiavi gestite dal cliente](./encrypt-data-at-rest.md).

## <a name="april-2020"></a>Aprile 2020
### <a name="new-face-api-recognition-model"></a>Nuovo modello di riconoscimento API Viso
* Il nuovo modello di riconoscimento 03 è il modello più accurato attualmente disponibile. Se sei un nuovo cliente, ti consigliamo di usare questo modello. Il riconoscimento 03 fornirà una maggiore accuratezza per i confronti di somiglianza e i confronti con corrispondenza di persona. Per ulteriori informazioni, vedere [specificare un modello di riconoscimento viso](./face-api-how-to-topics/specify-recognition-model.md).

## <a name="june-2019"></a>Giugno 2019

### <a name="new-face-api-detection-model"></a>Nuovo modello di rilevamento API Viso
* Il nuovo modello di rilevamento 02 offre una maggiore precisione sui visi piccoli, a visualizzazione laterale, bloccati e sfocati. Usare il tipo di rilevamento [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), facet [-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-Add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)face, [gruppo person-Add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) Face e [LargePersonGroup person-Add visi](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) specificando il nuovo nome del modello di rilevamento viso `detection_02` nel `detectionModel` parametro. Ulteriori informazioni su [come specificare un modello di rilevamento](Face-API-How-to-Topics/specify-detection-model.md).

## <a name="april-2019"></a>Aprile 2019

### <a name="improved-attribute-accuracy"></a>Accuratezza attributi migliorata
* Accuratezza complessiva migliorata degli `age` `headPose` attributi e. `headPose`Anche l'attributo viene aggiornato con il `pitch` valore abilitato ora. Usare questi attributi specificando questi attributi nel `returnFaceAttributes` parametro del parametro [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` . 
### <a name="improved-processing-speeds"></a>Velocità di elaborazione migliorate
* Velocità ottimizzate di [rilevamento viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), facet [-Add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)face, [LargeFaceList-Add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)face, [gruppo person-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) Add Face e [LargePersonGroup person-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) Operation.

## <a name="march-2019"></a>Marzo 2019

### <a name="new-face-api-recognition-model"></a>Nuovo modello di riconoscimento API Viso
* L'accuratezza del modello di riconoscimento 02 è stata migliorata. Usare il [rilevamento delle facce](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [facet-create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList-create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [gruppo-create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) e [LargePersonGroup-create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) specificando il nuovo nome del modello di riconoscimento viso `recognition_02` nel `recognitionModel` parametro. Ulteriori informazioni su [come specificare un modello di riconoscimento](Face-API-How-to-Topics/specify-recognition-model.md).

## <a name="january-2019"></a>Gennaio 2019

### <a name="face-snapshot-feature"></a>Funzionalità snapshot viso
* Questa funzionalità consente al servizio di supportare la migrazione dei dati tra sottoscrizioni: [snapshot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Altre informazioni su [come eseguire la migrazione dei dati volti a una sottoscrizione di un altro viso](Face-API-How-to-Topics/how-to-migrate-face-data.md).

## <a name="october-2018"></a>Ottobre 2018

### <a name="api-messages"></a>Messaggi API
* Perfezionata la descrizione per `status`, `createdDateTime`, `lastActionDateTime` e `lastSuccessfulTrainingDateTime` in [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) e [LargeFaceList - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

## <a name="may-2018"></a>Maggio 2018

### <a name="improved-attribute-accuracy"></a>Accuratezza attributi migliorata
* L'attributo `gender` è stato migliorato significativamente, inoltre sono stati modificati anche gli attributi `age`, `glasses`, `facialHair`, `hair` e `makeup`. Usarli tramite il parametro [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`.
### <a name="increased-file-size-limit"></a>Aumento del limite delle dimensioni del file
* Il limite delle dimensioni del file di immagine di input è stato aumentato da 4 MB a 6 MB in [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) e [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

## <a name="march-2018"></a>Marzo 2018

### <a name="new-data-structure"></a>Nuova struttura dei dati
* [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) e [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Maggiori dettagli sono descritti in [Come usare la funzionalità su larga scala](Face-API-How-to-Topics/how-to-use-large-scale.md).
* Il parametro [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` è stato aumentato da [1, 5] a [1, 100] e il valore predefinito è stato impostato su 10.

## <a name="may-2017"></a>Maggio 2017

### <a name="new-detectable-face-attributes"></a>Nuovi attributi viso rilevabili
* Sono stati aggiunti gli attributi `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure` e `noise` nel parametro [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`.
* PersonGroup e [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) supportano 10.000 persone.
* È supportata la paginazione in [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) con i parametri facoltativi: `start` e `top`.
* È supportata la concorrenza nell'aggiunta/nell'eliminazione di volti in FaceList diversi e persone diverse in PersonGroup.

## <a name="march-2017"></a>Marzo 2017

### <a name="new-detectable-face-attribute"></a>Nuovo attributo viso rilevabile
* È stato aggiunto l'attributo `emotion` nel parametro [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`.
### <a name="fixed-issues"></a>Problemi risolti
* Non è stato possibile ririlevare la faccia con rettangolo restituito da [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) come `targetFace` in [facet-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [gruppo person-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).
* La dimensione del volto rilevabile viene impostata in modo da garantire che sia strettamente compresa tra 36x36 e 4096x4096 pixel.

## <a name="november-2016"></a>Novembre 2016
### <a name="new-subscription-tier"></a>Nuovo livello di sottoscrizione
* È stata aggiunta la sottoscrizione standard ad Archiviazione visi per archiviare visi persistenti aggiuntivi quando si usa [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) o [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) per l'identificazione o la ricerca di analogie. Il costo delle immagini archiviate è di 0,5 dollari per 1.000 visi e alla tariffa viene applicata la ripartizione proporzionale giornaliera. Le sottoscrizioni del livello gratuito continuano a essere limitate a un totale di 1.000 persone.

## <a name="october-2016"></a>Ottobre 2016
### <a name="api-messages"></a>Messaggi API
* È stato modificato il messaggio di errore di più di una faccia nell'oggetto `targetFace` da' sono presenti più visi nell'immagine ' a'. nella figura sono presenti più facce nell'immagine ' in [facet-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [gruppo person-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

## <a name="july-2016"></a>Luglio 2016
### <a name="new-features"></a>Nuove funzionalità
* È supportata l'autenticazione dell'oggetto Face to Person in [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).
* È stato aggiunto il parametro `mode` facoltativo che consente la selezione di due modalità di lavoro: `matchPerson` e `matchFace` in [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), mentre il parametro predefinito è `matchPerson`.
* È stato aggiunto il parametro `confidenceThreshold` facoltativo per consentire all'utente di impostare la soglia che definisce se un viso appartiene a un oggetto Person in [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
* Sono stati aggiunti i parametri `start` e `top` facoltativi in [PersonGroup - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) per consentire all'utente di specificare il punto iniziale e il numero totale di PersonGroups all'elenco.

## <a name="v10-changes-from-v0"></a>Modifiche da V1.0 a V0

* L'endpoint radice del servizio è stato aggiornato da ```https://westus.api.cognitive.microsoft.com/face/v0/``` a ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Sono state apportate modifiche a: [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).
* Le dimensioni minime rilevabili per i visi sono state aggiornate a 36 x 36 pixel. I visi di dimensioni inferiori a 36 x 36 pixel non vengono rilevati.
* I dati di PersonGroup e Person in Viso V0 sono deprecati. Non è possibile accedere a tali dati con il servizio Viso V1.0.
* L'endpoint V0 dell'API Viso è deprecato dal 30 giugno 2016.