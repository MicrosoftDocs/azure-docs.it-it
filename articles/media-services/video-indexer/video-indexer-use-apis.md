---
title: Usare l'API Video Indexer
titleSuffix: Azure Media Services
description: Questo articolo descrive come iniziare a usare l'API Video Indexer di Servizi multimediali di Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/07/2021
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: ac0b206a86edf3157141b56e0c2623a8429b0c7a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785524"
---
# <a name="tutorial-use-the-video-indexer-api"></a>Esercitazione: Usare l'API Video Indexer

Video Indexer consolida diverse tecnologie di intelligenza artificiale audio e video offerte da Microsoft in un unico servizio integrato, semplificando così lo sviluppo. Le API sono progettate per consentire agli sviluppatori di concentrarsi sull'uso delle tecnologie di intelligenza artificiale per i contenuti multimediali senza doversi preoccupare della scalabilità, della portata globale, della disponibilità e dell'affidabilità delle piattaforme cloud. È possibile usare l'API per caricare file, ottenere informazioni dettagliate sui video, ottenere gli URL dei widget delle informazioni dettagliate e del lettore incorporabili e altro ancora.

Al momento della creazione di un account di Video Indexer, è possibile scegliere un account di valutazione gratuito (in cui si ottiene un certo numero di minuti di indicizzazione gratuiti) o un'opzione a pagamento (senza limiti di quota). Con la versione di valutazione gratuita, Video Indexer offre fino a 600 minuti di indicizzazione gratuita per gli utenti di siti Web e fino a 2.400 minuti di indicizzazione gratuita per gli utenti di API. Con un'opzione a pagamento, si crea un account di Video Indexer [collegato alla sottoscrizione di Azure e un account di Servizi multimediali di Azure](connect-to-azure.md). Si pagano i minuti indicizzati. Per altre informazioni, vedere [Prezzi di Servizi multimediali](https://azure.microsoft.com/pricing/details/media-services/).

Questo articolo illustra in che modo gli sviluppatori possono trarre vantaggio dall'[API Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="subscribe-to-the-api"></a>Eseguire la sottoscrizione all'API

1. Accedere al [portale per sviluppatori di Video Indexer](https://api-portal.videoindexer.ai/).

    Esaminare una nota sulla versione relativa alle [informazioni di accesso](release-notes.md#october-2020).
    
     ![Accedere al portale per sviluppatori di Video Indexer](./media/video-indexer-use-apis/sign-in.png)

   > [!Important]
   > * È necessario usare lo stesso provider usato per l'iscrizione a Video Indexer.
   > * Gli account Google e Microsoft personali (Outlook/Live) possono essere usati solo per gli account di valutazione. Gli account connessi ad Azure richiedono Azure AD.
   > * Può essere presente un solo account attivo per ogni indirizzo di posta elettronica. Se un utente cerca di effettuare l'accesso con user@gmail.com per LinkedIn e successivamente con user@gmail.com per Google, verrà visualizzata una pagina di errore che indica che l'utente esiste già.

2. Eseguire la sottoscrizione.

   Selezionare la scheda [Prodotti](https://api-portal.videoindexer.ai/products). Selezionare quindi Autorizzazione ed eseguire la sottoscrizione.
    
   ![Scheda Prodotti nel portale per sviluppatori di Video Indexer](./media/video-indexer-use-apis/authorization.png)

   > [!NOTE]
   > I nuovi utenti sono automaticamente sottoscritti per l'autorizzazione.
    
   Dopo aver effettuato la sottoscrizione, è possibile trovare la sottoscrizione in **Autorizzazione**  ->  **prodotti**. Nella pagina della sottoscrizione sono presenti le chiavi primaria e secondaria. Le chiavi devono essere protette. Le chiavi devono essere usate solo dal codice server. Non devono essere disponibili sul lato client (JS, HTML e così via).

   ![Sottoscrizione e chiavi nel portale per sviluppatori di Video Indexer](./media/video-indexer-use-apis/subscriptions.png)

> [!TIP]
> L'utente di Video Indexer può usare una chiave di sottoscrizione singola per connetterti a più account Video Indexer. È quindi possibile collegare questi account Video Indexer a diversi account Servizi multimediali.

## <a name="obtain-access-token-using-the-authorization-api"></a>Ottenere i token di accesso usando l'API di autorizzazione

Una volta eseguita la sottoscrizione per l'API di autorizzazione, è possibile ottenere i token di accesso. Questi token di accesso vengono usati per eseguire l'autenticazione con l'API delle operazioni.

Ogni chiamata all'API delle operazioni deve essere associata a un token di accesso, che corrisponde all'ambito di autorizzazione della chiamata.

- Livello utente: i token di accesso a livello di utente consentono di eseguire operazioni a livello **utente**. ad esempio ottenere gli account associati.
- Livello account: i token di accesso a livello di account consentono di eseguire operazioni a livello di **account** o a livello di **video**, ad esempio caricare video, elencare tutti i video, ottenere informazioni dettagliate sui video e così via.
- Livello video: i token di accesso a livello di video consentono di eseguire operazioni su uno specifico **video**, ad esempio ottenere informazioni dettagliate sul video, scaricare i sottotitoli, ottenere i widget e così via.

È possibile controllare il livello di autorizzazione dei token in due modi:

* Per **i** token account, è possibile usare l'API Get **Account Access Token With Permission** (Ottieni token di accesso account con autorizzazione) e specificare il tipo di autorizzazione (**Reader** / **Contributor** / **MyAccessManager** / **Owner**).
* Per tutti i tipi di token (inclusi i token **account),** è possibile specificare **allowEdit=true/false.** **false** è l'equivalente di un'autorizzazione **Lettore** (sola lettura) e **true** è l'equivalente di un'autorizzazione **Collaboratore** (lettura-scrittura).

Per la maggior parte degli scenari da server a server, in genere si userà lo stesso token di **account**, perché consente di eseguire sia le operazioni a livello di **account** che le operazioni a livello di **video**. Se però si prevede di effettuare chiamate lato client a Video Indexer (ad esempio da JavaScript), può essere preferibile usare un token di accesso **video**, in modo da impedire ai client di ottenere l'accesso all'intero account. È sempre per questo motivo che, quando si incorpora codice client di Video Indexer nel client, ad esempio usando **Get Insights Widget** (Ottieni widget informazioni dettagliate) o **Get Player Widget** (Ottieni widget lettore), è necessario fornire un token di accesso **video**.

Per semplificare le operazioni, è possibile usare l'API **Autorizzazione** > **GetAccounts** per ottenere gli account senza prima ottenere un token utente. È anche possibile richiedere di ottenere gli account con token validi, in modo da evitare una chiamata aggiuntiva per ottenere un token di account.

I token di accesso scadono dopo un'ora. Assicurarsi che il token di accesso sia valido prima di usare l'API delle operazioni. Se scade, chiamare di nuovo l'API di autorizzazione per ottenere un nuovo token di accesso.

A questo punto si è pronti per avviare l'integrazione con l'API. Leggere la [descrizione dettagliata di ogni API REST di Video Indexer](https://api-portal.videoindexer.ai/).

## <a name="account-id"></a>Account ID

Il parametro Account ID è obbligatorio in tutte le chiamate alle API delle operazioni. L'ID account è un GUID che può essere ottenuto in uno dei modi seguenti:

* Usare il **sito Web di Video Indexer** per ottenere l'ID account:

    1. Passare al sito Web di [Video Indexer](https://www.videoindexer.ai/) ed eseguire l'accesso.
    2. Passare alla pagina **Impostazioni**.
    3. Copiare l'ID account.

        ![Impostazioni e ID account di Video Indexer](./media/video-indexer-use-apis/account-id.png)

* Usare il **portale per sviluppatori di Video Indexer** per ottenere l'ID account a livello di codice.

    Usare l'API [Get account](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account) (Ottieni account).

    > [!TIP]
    > È possibile generare i token di accesso per gli account definendo `generateAccessTokens=true`.

* Ottenere l'ID account dall'URL di una pagina del lettore nel proprio account.

    Quando si guarda un video, l'ID viene visualizzato dopo la sezione `accounts` e prima della sezione `videos`.

    ```
    https://www.videoindexer.ai/accounts/00000000-f324-4385-b142-f77dacb0a368/videos/d45bf160b5/
    ```

## <a name="recommendations"></a>Consigli

In questa sezione sono elencati alcuni suggerimenti per l'uso dell'API Video Indexer.

- Se si prevede di caricare un video, è consigliabile inserire il file in un percorso di rete pubblico, ad esempio un account Archiviazione BLOB di Azure locale. Ottenere il collegamento del video e specificare l'URL come il parametro per il caricamento del file.

    L'URL fornito a Video Indexer deve puntare a un file multimediale (audio o video). Una semplice verifica dell'URL (o URL di firma di accesso condiviso) è incollarlo in un browser. Se il file inizia a riprodurre/scaricare, è probabile che sia un URL valido. Se il browser esegue il rendering di una visualizzazione, probabilmente il collegamento non è relativo a un file ma a una pagina HTML.

- Quando si chiama l'API che consente di ottenere informazioni dettagliate sui video per il video specificato, viene visualizzato un output JSON dettagliato come contenuto della risposta. [Consultare informazioni dettagliate sul codice JSON restituito in questo argomento](video-indexer-output-json-v2.md).

## <a name="code-sample"></a>Esempio di codice

Il frammento di codice C# seguente illustra l'uso di tutte le API di Video Indexer.

```csharp
var apiUrl = "https://api.videoindexer.ai";
var accountId = "..."; 
var location = "westus2"; // replace with the account's location, or with “trial” if this is a trial account
var apiKey = "..."; 

System.Net.ServicePointManager.SecurityProtocol = System.Net.ServicePointManager.SecurityProtocol | System.Net.SecurityProtocolType.Tls12;

// create the http client
var handler = new HttpClientHandler(); 
handler.AllowAutoRedirect = false; 
var client = new HttpClient(handler);
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey); 

// obtain account access token
var accountAccessTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/AccessToken?allowEdit=true").Result;
var accountAccessToken = accountAccessTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// upload a video
var content = new MultipartFormDataContent();
Debug.WriteLine("Uploading...");
// get the video from URL
var videoUrl = "VIDEO_URL"; // replace with the video URL

// as an alternative to specifying video URL, you can upload a file.
// remove the videoUrl parameter from the query string below and add the following lines:
  //FileStream video =File.OpenRead(Globals.VIDEOFILE_PATH);
  //byte[] buffer = new byte[video.Length];
  //video.Read(buffer, 0, buffer.Length);
  //content.Add(new ByteArrayContent(buffer));

var uploadRequestResult = client.PostAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos?accessToken={accountAccessToken}&name=some_name&description=some_description&privacy=private&partition=some_partition&videoUrl={videoUrl}", content).Result;
var uploadResult = uploadRequestResult.Content.ReadAsStringAsync().Result;

// get the video id from the upload result
var videoId = JsonConvert.DeserializeObject<dynamic>(uploadResult)["id"];
Debug.WriteLine("Uploaded");
Debug.WriteLine("Video ID: " + videoId);           

// obtain video access token
client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", apiKey);
var videoTokenRequestResult = client.GetAsync($"{apiUrl}/auth/{location}/Accounts/{accountId}/Videos/{videoId}/AccessToken?allowEdit=true").Result;
var videoAccessToken = videoTokenRequestResult.Content.ReadAsStringAsync().Result.Replace("\"", "");

client.DefaultRequestHeaders.Remove("Ocp-Apim-Subscription-Key");

// wait for the video index to finish
while (true)
{
  Thread.Sleep(10000);

  var videoGetIndexRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/Index?accessToken={videoAccessToken}&language=English").Result;
  var videoGetIndexResult = videoGetIndexRequestResult.Content.ReadAsStringAsync().Result;

  var processingState = JsonConvert.DeserializeObject<dynamic>(videoGetIndexResult)["state"];

  Debug.WriteLine("");
  Debug.WriteLine("State:");
  Debug.WriteLine(processingState);

  // job is finished
  if (processingState != "Uploaded" && processingState != "Processing")
  {
      Debug.WriteLine("");
      Debug.WriteLine("Full JSON:");
      Debug.WriteLine(videoGetIndexResult);
      break;
  }
}

// search for the video
var searchRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/Search?accessToken={accountAccessToken}&id={videoId}").Result;
var searchResult = searchRequestResult.Content.ReadAsStringAsync().Result;
Debug.WriteLine("");
Debug.WriteLine("Search:");
Debug.WriteLine(searchResult);

// get insights widget url
var insightsWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/InsightsWidget?accessToken={videoAccessToken}&widgetType=Keywords&allowEdit=true").Result;
var insightsWidgetLink = insightsWidgetRequestResult.Headers.Location;
Debug.WriteLine("Insights Widget url:");
Debug.WriteLine(insightsWidgetLink);

// get player widget url
var playerWidgetRequestResult = client.GetAsync($"{apiUrl}/{location}/Accounts/{accountId}/Videos/{videoId}/PlayerWidget?accessToken={videoAccessToken}").Result;
var playerWidgetLink = playerWidgetRequestResult.Headers.Location;
Debug.WriteLine("");
Debug.WriteLine("Player Widget url:");
Debug.WriteLine(playerWidgetLink);

```

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esercitazione, eliminare le risorse che non si prevede di usare.

## <a name="see-also"></a>Vedere anche

- [Panoramica di Video Indexer](video-indexer-overview.md)
- [Aree](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

## <a name="next-steps"></a>Passaggi successivi

- [Esaminare i dettagli del codice JSON di output](video-indexer-output-json-v2.md)
- Vedere il [codice di esempio](https://github.com/Azure-Samples/media-services-video-indexer) che illustra un aspetto importante del caricamento e dell'indicizzazione di un video. Seguendo il codice si avrà una buona idea di come usare l'API per le funzionalità di base. Assicurarsi di leggere i commenti inline e i suggerimenti per le procedure consigliate.

