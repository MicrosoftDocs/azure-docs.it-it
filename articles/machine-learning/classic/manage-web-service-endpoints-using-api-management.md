---
title: 'ML Studio (classico): gestire i servizi Web con gestione API-Azure'
description: Guida che mostra come gestire i servizi Web di AzureML con Gestione API. Gestire gli endpoint dell'API REST definendo l'accesso utente, la limitazione all'utilizzo e il monitoraggio del dashboard.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: 293643ab5354c51142baaf281b1845cfc9fa6f23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518029"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>Gestire i servizi Web di Azure Machine Learning Studio (versione classica) con Gestione API

**SI APPLICA A:**  ![Si applica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (versione classica) ![Non si applica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


## <a name="overview"></a>Panoramica
Questa guida illustra le procedure per iniziare subito a usare Gestione API per gestire i servizi Web di Azure Machine Learning Studio (versione classica).

## <a name="what-is-azure-api-management"></a>Cos'è Gestione API di Azure?
Gestione API di Azure è un servizio di Azure che consente di gestire gli endpoint dell'API REST definendo l'accesso utente, la limitazione all'utilizzo e il monitoraggio del dashboard. Per altri dettagli, vedere il [sito per la gestione delle API di Azure ](https://azure.microsoft.com/services/api-management/). Per iniziare a usare Gestione API di Azure, vedere la [guida all'importazione e alla pubblicazione](../../api-management/import-and-publish.md). L'altra guida, su cui è basata questa, tratta più argomenti, tra cui le configurazioni delle notifiche, il livello di prezzo, la gestione delle risposte, l'autenticazione utente, la creazione di prodotti, le sottoscrizioni per sviluppatori e il dashboarding dell'uso.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:

* Un account Azure.
* Un account AzureML.
* L'area di lavoro, il servizio e l'api_key per un esperimento di AzureML distribuito come servizio web. Per informazioni dettagliate su come creare un esperimento di AzureML, vedere l'[argomento di avvio rapido di Studio](create-experiment.md). Per informazioni su come distribuire un esperimento di Studio (versione classica) come servizio Web, vedere le [Procedure per la distribuzione di Studio](deploy-a-machine-learning-web-service.md) contenenti informazioni dettagliate su come distribuire un esperimento di AzureML come servizio Web. In alternativa, l'Appendice A contiene le istruzioni per creare e testare un semplice esperimento di AzureML e distribuirlo come servizio Web.

## <a name="create-an-api-management-instance"></a>Creare un'istanza di Gestione API

È possibile gestire il servizio Web di Azure Machine Learning con un'istanza di Gestione API.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare **+ Crea una risorsa**.
3. Nella casella di ricerca digitare "Gestione API" e quindi selezionare la risorsa "Gestione API".
4. Fare clic su **Crea**.
5. Il valore del campo **Nome** verrà usato per creare un URL univoco (in questo esempio viene usato "demoazureml").
6. Scegliere un valore nei campi **Sottoscrizione**, **Gruppo di risorse** e **Area** per l'istanza del servizio.
7. Specificare un valore per **Nome organizzazione** (in questo esempio viene usato "demoazureml").
8. Immettere un valore nel campo **Indirizzo di posta elettronica dell'amministratore**. L'indirizzo specificato verrà usato per le notifiche del sistema Gestione API.
9. Fare clic su **Crea**.

La creazione del nuovo potrebbe richiedere fino a 30 minuti.

![Screenshot mostra la finestra di dialogo servizio di gestione P i con le opzioni necessarie per creare un servizio.](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>Creare l'API
Dopo aver creato l'istanza del servizio, l'operazione successiva consiste nel creare l'API. Un'API rappresenta un set di operazioni che possono essere richiamate da un'applicazione client. Le operazioni API vengono trasmesse tramite proxy ai servizi Web esistenti. Questa guida crea le API che usano un proxy per i servizi Web RRS e BES di AzureML esistenti.

Per creare l'API:

1. Nel portale di Azure aprire l'istanza del servizio appena creata.
2. Nel pannello di navigazione sinistro selezionare **API**.

   ![api-management-menu](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. Fare clic su **Aggiungi API**.
2. Immettere un valore nel campo **Nome API Web** (in questo esempio viene usato "AzureML Demo API").
3. Per **URL del servizio Web** immettere "`https://ussouthcentral.services.azureml.net`".
4. Immettere un valore per **Suffisso dell'URL dell'API Web". Il valore immesso diventerà l'ultima parte dell'URL che i clienti useranno per l'invio di richieste all'istanza del servizio (in questo esempio viene usato "azureml-demo").
5. Per **Schema URL API Web** selezionare **HTTPS**.
6. Per **Prodotti** selezionare **Starter** (Avviatore).
7. Fare clic su **Salva**.


## <a name="add-the-operations"></a>Aggiungere le operazioni

Le operazioni vengono aggiunte e configurate in un'API nel portale di pubblicazione. Per accedere al portale di pubblicazione, fare clic su **Portale di pubblicazione** nel portale di Azure per il servizio Gestione API, selezionare **API**, **Operazioni** e quindi fare clic su **Aggiungi operazione**.

![add-operation](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

Verrà visualizzata la finestra **Nuova operazione** in cui la scheda **Firma** è selezionata per impostazione predefinita.

## <a name="add-rrs-operation&quot;></a>Aggiungere un'operazione RRS
Creare prima un'operazione per il servizio RRS di AzureML:

1. Per **Verbo HTTP** selezionare **POST**.
2. Per **Modello di URL** digitare &quot;`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`&quot;.
3. Immettere un valore nel campo **Nome visualizzato** (in questo esempio viene usato &quot;RRS Execute").

   ![Screenshot mostra la pagina firma in cui è possibile immettere un nome visualizzato.](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Fare clic su **Risposte** > **AGGIUNGI** a sinistra e selezionare **200 OK**.
5. Fare clic su **Salva** per salvare questa operazione.

   ![Screenshot mostra la pagina di esecuzione di R R S con un pulsante Salva.](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>Aggiungere operazioni BES

> [!NOTE]
> Non sono incluse schermate per le operazioni di BES perché sono molto simili a quelle per l'aggiunta dell'operazione RRS.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Inviare (ma non avviare) un processo di esecuzione in batch

1. Fare clic su **aggiungi operazione** per aggiungere un'operazione BES all'API.
2. Per **Verbo HTTP** selezionare **POST**.
3. Per **Modello di URL** digitare "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`".
4. Immettere un valore nel campo **Nome visualizzato** (in questo esempio viene usato "BES Submit").
5. Fare clic su **Risposte** > **AGGIUNGI** a sinistra e selezionare **200 OK**.
6. Fare clic su **Salva**.

### <a name="start-a-batch-execution-job"></a>Avviare un processo di esecuzione in batch

1. Fare clic su **aggiungi operazione** per aggiungere un'operazione BES all'API.
2. Per **Verbo HTTP** selezionare **POST**.
3. Per **Verbo HTTP** digitare "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`".
4. Immettere un valore nel campo **Nome visualizzato** (in questo esempio viene usato "BES Start").
6. Fare clic su **Risposte** > **AGGIUNGI** a sinistra e selezionare **200 OK**.
7. Fare clic su **Salva**.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Ottenere lo stato o il risultato di un processo di esecuzione del Batch

1. Fare clic su **aggiungi operazione** per aggiungere un'operazione BES all'API.
2. Per **Verbo HTTP** selezionare **GET**.
3. Per **Modello di URL** digitare "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Immettere un valore nel campo **Nome visualizzato** (in questo esempio viene usato "BES Status").
6. Fare clic su **Risposte** > **AGGIUNGI** a sinistra e selezionare **200 OK**.
7. Fare clic su **Salva**.

### <a name="delete-a-batch-execution-job"></a>Eliminare un processo di esecuzione in batch

1. Fare clic su **aggiungi operazione** per aggiungere un'operazione BES all'API.
2. Per **Verbo HTTP** selezionare **DELETE**.
3. Per **Modello di URL** digitare "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`".
4. Immettere un valore nel campo **Nome visualizzato** (in questo esempio viene usato "BES Delete").
5. Fare clic su **Risposte** > **AGGIUNGI** a sinistra e selezionare **200 OK**.
6. Fare clic su **Salva**.

## <a name="call-an-operation-from-the-developer-portal"></a>Chiamare un'operazione dal portale per sviluppatori

È possibile chiamare le operazioni direttamente dal portale per sviluppatori, che consente di visualizzare e testare le operazioni di un'API in tutta comodità. In questa procedura si chiamerà il metodo **RRS Execute** aggiunto all'**AzureML Demo API**. 

1. Fare clic su **Portale per sviluppatori**.

   ![Screenshot che mostra il collegamento al portale per sviluppatori.](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Fare clic su **API** nel menu superiore e quindi su **AzureML Demo API** per visualizzare le operazioni disponibili.

   ![Screenshot che mostra il collegamento di Azure M L demo A P I.](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. Selezionare **RRS Execute** come operazione. Fare clic su **Prova**.

   ![Screenshot mostra la finestra di dialogo Azure M L demo A P I con post R R S Execute Selected e un pulsante try it.](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. Per i **parametri della richiesta** immettere l'**area di lavoro** e il **servizio**, digitare 2.0 per la **versione API** e "true" per i **dettagli**. È possibile trovare l'**area di lavoro** e il **servizio** nel dashboard del servizio Web di AzureML (vedere **Testare il servizio Web** nell'Appendice A).

   Per **Intestazioni della richiesta** fare clic su **Aggiungi intestazione** e digitare "Tipo contenuto" e "application/json". Fare di nuovo clic su **Aggiungi intestazione** e digitare "Autorizzazione" e "Token di connessione *\<your service API-KEY\>* ". È possibile trovare la chiave API nel dashboard del servizio Web di AzureML (vedere **Testare il servizio Web** nell'Appendice A).

   Nel riquadro **Corpo della richiesta** digitare `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`.

   ![Screenshot mostra i parametri della richiesta, le intestazioni delle richieste, il corpo della richiesta e l'autorizzazione di Azure M L demo A P I.](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. Fare clic su **Invia**.

   ![Screenshot che mostra un pulsante Invia.](./media/manage-web-service-endpoints-using-api-management/send.png)

Una volta richiamata un'operazione, nel portale per sviluppatori vengono visualizzati l'**URL richiesto** restituito dal servizio back-end, lo **Stato della risposta**, le **Intestazioni della risposta** e l'eventuale **Contenuto della risposta**.

![Screenshot mostra il portale per sviluppatori che visualizza lo stato della risposta, la latenza della risposta, le intestazioni di risposta e il contenuto della risposta.](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Appendice A - Creazione e test di un semplice servizio Web di AzureML
### <a name="creating-the-experiment"></a>Creazione di un esperimento
Di seguito sono riportati i passaggi per creare un semplice esperimento di AzureML e distribuirlo come servizio Web. Il servizio Web accetta come input una colonna di testo arbitrario e restituisce un set di funzionalità rappresentate come valori Integer. Ad esempio:

| Text | Testo con hash |
| --- | --- |
| This is a good day |1 1 2 2 0 2 0 1 |

Per prima cosa, usando il browser preferito, passare a [https://studio.azureml.net/](https://studio.azureml.net/) e immettere le credenziali di accesso. Quindi creare un nuovo esperimento vuoto.

![Screenshot mostra una nuova pagina con l'esperimento selezionato e una ricerca di testo.](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

Rinominarlo **SimpleFeatureHashingExperiment**. Espandere **Saved Datasets** (Set di dati salvati) e trascinare **Book Reviews from Amazon** (Recensioni di libri da Amazon) sull'esperimento.

![Screenshot Mostra gli esempi sul lato sinistro e un riquadro SimpleFeatureHashingExperiment a destra con l'istruzione per trascinare qui gli elementi.](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

Espandere **Data Transformation** (Trasformazioni di dati) e **Manipulation** (Manipolazione), quindi trascinare **Select Columns in Dataset** (Seleziona colonne in set di dati) sull'esperimento. Connettere **Book Reviews from Amazon** (Recensioni sul libro da Amazon) a **Select Columns in Dataset** (Seleziona colonne in set di dati).

![Connettere il modulo del set di dati di Book Reviews a un modulo Project Columns](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

Fare clic su **Select Columns in Dataset** (Seleziona colonne in set di dati), quindi fare clic su **Launch column selector** (Avvia selettore di colonna) e selezionare **Col2**. Fare clic sul segno di spunta per applicare queste modifiche.

![Selezionare le colonne usando i nomi di colonna](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

Espandere **Text Analytics** (Analisi testo) e trascinare **Feature Hashing** sull'esperimento. Connettere **Select Columns in Dataset** (Seleziona colonne in set di dati) a **Feature Hashing**.

![Screenshot mostra un elemento Feature hashing aggiunto all'area di lavoro.](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

Digitare **3** in **Hashing bitsize**. Verranno create 8 (23) colonne.

![Screenshot mostra le proprietà con l'hashing delle funzionalità selezionato ed è possibile immettere hash bitsize.](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

A questo punto, è possibile fare clic su **Run** per testare l'esperimento.

![Screenshot che mostra un pulsante Esegui.](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Creare un servizio Web
Ora creare un servizio Web. Espandere **Servizio Web** e trascinare **Input** sull'esperimento. Connettere **Input** a **Feature Hashing**. Trascinare anche **output** sull'esperimento. Connettere **Output** a **Feature Hashing**.

![Screenshot che mostra l'area di lavoro dopo le modifiche specificate.](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

Fare cli su **Publish web service**.

![Screenshot mostra un pulsante Pubblica servizio WEB.](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Fare clic su **Yes** per pubblicare l'esperimento.

![Screenshot Visualizza un messaggio di conferma e l'opzione per la pubblicazione.](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Testare il servizio Web
Un servizio Web di AzureML è costituito dagli endpoint RSS (servizio di richiesta/risposta) e BES (servizio di esecuzione batch). RSS è per l'esecuzione sincrona. BES è per l'esecuzione di processi asincrona. Per testare il servizio Web con la seguente origine Python di esempio, potrebbe essere necessario scaricare e installare Azure SDK per Python (vedere: [Come installare Python](/azure/developer/python/azure-sdk-install)).

Per la seguente origine di esempio, saranno necessari anche l'**area di lavoro**, il **servizio** e la **chiave API** dell'esperimento. È possibile trovare l'area di lavoro e il servizio facendo clic su **Richiesta/risposta** o su **Esecuzione batch** per l'esperimento nel dashboard del servizio Web.

![Screenshot che mostra il riquadro delle richieste in cui è possibile trovare i valori dell'area di lavoro e del servizio.](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

È possibile trovare la **chiave API** facendo clic sull'esperimento nel dashboard del servizio Web.

![Screenshot mostra l'esperimento nel dashboard del servizio Web in cui è possibile trovare la chiave A P I.](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Testare l'endpoint RRS
##### <a name="test-button"></a>Pulsante Test
Un modo semplice per testare l'endpoint RRS consiste nel fare clic su **Test** nel dashboard del servizio.

![Screenshot mostra l'esperimento nel dashboard del servizio Web con il pulsante test.](./media/manage-web-service-endpoints-using-api-management/test.png)

Digitare **This is a good day** in **col2**. Fare clic sul segno di spunta.

![Screenshot mostra la finestra di dialogo Immetti dati da stimare, in cui è possibile immettere testo come esempio. si tratta di un giorno valido.](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Verrà visualizzato qualcosa di simile a quanto segue

![Screenshot mostra il risultato dell'esperimento, che legge questo è un giorno e più cifre racchiuse tra virgolette.](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Codice di esempio
È possibile testare RRS anche dal codice client. Se si fa clic su **Richiesta/risposta** sul dashboard e si scorre fino in fondo, si vedrà il codice di esempio per C#, Python e R. Si vedrà anche la sintassi della richiesta RRS, incluso l'URI della richiesta, le intestazioni e il corpo.

Questa guida mostra un esempio di Python funzionante. Sarà necessario modificarlo con l'**area di lavoro**, il **servizio** e la **chiave API** dell'esperimento.

```python
import urllib2
import json
workspace = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE WORKSPACE ID>"
service = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE SERVICE ID>"
api_key = "<REPLACE WITH YOUR EXPERIMENT'S WEB SERVICE API KEY>"
data = {
"Inputs": {
    "input1": {
        "ColumnNames": ["Col2"],
        "Values": [ [ "This is a good day" ] ]
    },
},
"GlobalParameters": { }
}
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
body = str.encode(json.dumps(data))
try:
    req = urllib2.Request(url, body, headers)
    response = urllib2.urlopen(req)
    result = response.read()
    print "result:" + result
        except urllib2.HTTPError, error:
    print("The request failed with status code: " + str(error.code))
    print(error.info())
    print(json.loads(error.read()))
```

#### <a name="test-bes-endpoint"></a>Testare l'endpoint BES
Fare clic su **Esecuzione batch** sul dashboard e scorrere fino in fondo. Si vedrà il codice di esempio per C#, Python e R. Si vedrà anche la sintassi delle richieste BES per inviare un processo, avviare un processo, ottenere lo stato o i risultati di un processo ed eliminare un processo.

Questa guida mostra un esempio di Python funzionante. È necessario modificarlo con l'**area di lavoro**, il **servizio** e la **chiave API** dell'esperimento. È anche necessario modificare il **nome account di archiviazione**, la **chiave dell'account di archiviazione** e il **nome del contenitore di archiviazione**. Infine sarà necessario modificare il percorso del **file di input** e il percorso del **file di output**.

```python
import urllib2
import json
import time
from azure.storage import *
workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
service = "<REPLACE WITH YOUR SERVICE ID>"
api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
input_blob_name = "mydatablob.csv"
output_blob_name = "myresultsblob.csv"
def printHttpError(httpError):
print("The request failed with status code: " + str(httpError.code))
print(httpError.info())
print(json.loads(httpError.read()))
return
def saveBlobToFile(blobUrl, resultsLabel):
print("Reading the result from " + blobUrl)
try:
    response = urllib2.urlopen(blobUrl)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
with open(output_file, "wb+") as f:
    f.write(response.read())
print(resultsLabel + " have been written to the file " + output_file)
return
def processResults(result):
first = True
results = result["Results"]
for outputName in results:
    result_blob_location = results[outputName]
    sas_token = result_blob_location["SasBlobToken"]
    base_url = result_blob_location["BaseLocation"]
    relative_url = result_blob_location["RelativeLocation"]
    print("The results for " + outputName + " are available at the following Azure Storage location:")
    print("BaseLocation: " + base_url)
    print("RelativeLocation: " + relative_url)
    print("SasBlobToken: " + sas_token)
    if (first):
        first = False
        url3 = base_url + relative_url + sas_token
        saveBlobToFile(url3, "The results for " + outputName)
return

def invokeBatchExecutionService():
url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
print("Uploading the input to blob storage...")
data_to_upload = open(input_file, "r").read()
blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
print "Uploaded the input to blob storage"
input_blob_path = "/" + storage_container_name + "/" + input_blob_name
connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
payload =  {
    "Input": {
        "ConnectionString": connection_string,
        "RelativeLocation": input_blob_path
    },
    "Outputs": {
        "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
    },
    "GlobalParameters": {
    }
}
    body = str.encode(json.dumps(payload))
headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
print("Submitting the job...")
# submit the job
req = urllib2.Request(url + "?api-version=2.0", body, headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
result = response.read()
job_id = result[1:-1] # remove the enclosing double-quotes
print("Job ID: " + job_id)
# start the job
print("Starting the job...")
req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
try:
    response = urllib2.urlopen(req)
except urllib2.HTTPError, error:
    printHttpError(error)
    return
url2 = url + "/" + job_id + "?api-version=2.0"

while True:
    print("Checking the job status...")
    # If you are using Python 3+, replace urllib2 with urllib.request in the following code
    req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = json.loads(response.read())
    status = result["StatusCode"]
    print "status:" + status
    if (status == 0 or status == "NotStarted"):
        print("Job " + job_id + " not yet started...")
    elif (status == 1 or status == "Running"):
        print("Job " + job_id + " running...")
    elif (status == 2 or status == "Failed"):
        print("Job " + job_id + " failed!")
        print("Error details: " + result["Details"])
        break
    elif (status == 3 or status == "Cancelled"):
        print("Job " + job_id + " cancelled!")
        break
    elif (status == 4 or status == "Finished"):
        print("Job " + job_id + " finished!")
        processResults(result)
        break
    time.sleep(1) # wait one second
return
invokeBatchExecutionService()
```