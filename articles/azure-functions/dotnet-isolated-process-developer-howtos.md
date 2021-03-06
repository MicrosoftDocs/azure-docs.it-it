---
title: Sviluppare e pubblicare funzioni .NET 5 usando Funzioni di Azure
description: Informazioni su come creare ed eseguire il debug di funzioni C# usando .NET 5.0, quindi distribuire il progetto locale nell'hosting serverless in Funzioni di Azure.
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: c76fde9a61ca60171ac094ef541e8c5841846aab
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866270"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>Sviluppare e pubblicare funzioni .NET 5 usando Funzioni di Azure 

Questo articolo illustra come usare le funzioni C# usando .NET 5.0, che vengono eseguite out-of-process dal runtime Funzioni di Azure runtime. Si apprenderà come creare, eseguire il debug in locale e pubblicare queste funzioni di processo isolato .NET in Azure. In Azure queste funzioni vengono eseguite in un processo isolato che supporta .NET 5.0. Per altre informazioni, vedere [Guida all'esecuzione di funzioni in .NET 5.0 in Azure.](dotnet-isolated-process-guide.md)

Se non è necessario supportare .NET 5.0 o eseguire le funzioni out-of-process, è possibile creare invece una funzione della libreria di [classi C#.](functions-create-your-first-function-visual-studio.md)

>[!NOTE]
>Lo sviluppo di funzioni di processo isolato .NET portale di Azure non è attualmente supportato. È necessario usare l'interfaccia della riga di comando di Azure o la pubblicazione Visual Studio Code per creare un'app per le funzioni in Azure che supporta l'esecuzione out-of-process di app .NET 5.0.   

## <a name="prerequisites"></a>Prerequisiti

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [.NET 5.0 SDK](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) versione 3.0.3381 o successiva.

+ [Interfaccia della riga di](/cli/azure/install-azure-cli) comando di Azure versione 2.20 o successiva.  
::: zone pivot="development-environment-vscode"
+ [Visual Studio Code](https://code.visualstudio.com/) in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  

+ [Estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) per Visual Studio Code.  

+ Estensione [Funzioni di Azure per](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) Visual Studio Code, versione 1.3.0 o successiva.
::: zone-end
::: zone pivot="development-environment-vs"
+ [Visual Studio 2019, incluso](https://azure.microsoft.com/downloads/)il carico **di lavoro Sviluppo di Azure.**  
I modelli di progetto di funzioni isolate .NET e la pubblicazione non sono attualmente disponibili in Visual Studio.
::: zone-end

## <a name="create-a-local-function-project"></a>Creare un progetto di funzione locale

In Funzioni di Azure un progetto di funzione è un contenitore per una o più funzioni singole che rispondono ognuna a un trigger specifico. Tutte le funzioni di un progetto condividono le stesse configurazioni locali e di hosting. In questa sezione viene creato un progetto di funzione che contiene una singola funzione.

::: zone pivot="development-environment-vs"

>[!NOTE]  
> Al momento non sono disponibili modelli di Visual Studio che supportano la creazione di progetti di funzioni isolate .NET. Questo articolo illustra come usare Core Tools per creare il progetto C#, che è quindi possibile eseguire in locale ed eseguire il debug in Visual Studio.  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. Selezionare l'icona di Azure nella barra attività, quindi nell'area **Azure: Funzioni** selezionare l'icona **Crea nuovo progetto...** .

    ![Scegliere Crea nuovo progetto](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Scegliere una posizione della directory per l'area di lavoro del progetto e quindi scegliere **Seleziona**.

    > [!NOTE]
    > Questa procedura è stata progettata per il completamento all'esterno di un'area di lavoro. In questo caso, non selezionare una cartella di progetto inclusa in un'area di lavoro.

1. Quando richiesto, immettere le informazioni seguenti:

    + **Selezionare un linguaggio per il progetto di funzione**: Scegliere `C#`.

    + **Selezionare un runtime .NET:** scegliere `.NET 5 isolated` .

    + **Selezionare un modello per la prima funzione del progetto**: Scegliere `HTTP trigger`.

    + **Specificare un nome di funzione**: Digitare `HttpExample`.

    + **Specificare uno spazio dei nomi**: Digitare `My.Functions`.

    + **Livello di autorizzazione**: Scegliere `Anonymous`, che consente a chiunque di chiamare l'endpoint della funzione. Per informazioni sul livello di autorizzazione, vedere [Chiavi di autorizzazione](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Selezionare come si vuole aprire il progetto**: Scegliere `Add to workspace`.

1. Usando queste informazioni, Visual Studio Code genera un progetto di Funzioni di Azure con un trigger HTTP. È possibile visualizzare i file di progetto locali in Explorer. Per altre informazioni sui file che vengono creati, vedere [File di progetto generati](functions-develop-vs-code.md#generated-project-files).
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. Eseguire il `func init` comando , come indicato di seguito, per creare un progetto di funzioni in una cartella denominata *LocalFunctionProj*:  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    Se si `dotnetisolated` specifica , viene creato un progetto che viene eseguito in .NET 5.0.


1. Passare alla cartella del progetto:

    ```console
    cd LocalFunctionProj
    ```

    Questa cartella contiene vari file per il progetto, incluso illocal.settings.js[ su](functions-run-local.md#local-settings-file) e [host.jssui file](functions-host-json.md) di configurazione. Poiché *local.settings.json* può contenere segreti scaricati da Azure, per impostazione predefinita il file viene escluso dal controllo del codice sorgente nel file con estensione *gitignore*.

1. Aggiungere una funzione al progetto usando il comando seguente, in cui l'argomento `--name` è il nome univoco della funzione (HttpExample) e l'argomento `--template` specifica il trigger della funzione (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` crea un file di codice HttpExample.cs.
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

A questo punto, è possibile eseguire il comando dalla radice della cartella del progetto per compilare `func start` ed eseguire il progetto di funzioni isolate C#. Attualmente, se si vuole eseguire il debug del codice della funzione out-of-process in Visual Studio, è necessario collegare manualmente un debugger al processo di runtime di Funzioni in esecuzione usando la procedura seguente:  

1. Aprire il file di progetto (con estensione csproj) in Visual Studio. È possibile esaminare e modificare il codice del progetto e impostare eventuali punti di interruzione desiderati nel codice. 

1. Dalla cartella radice del progetto usare il comando seguente dal terminale o da un prompt dei comandi per avviare l'host di runtime:

    ```console
    func start --dotnet-isolated-debug
    ```

    `--dotnet-isolated-debug`L'opzione indica al processo di attendere la connessione di un debugger prima di continuare. Verso la fine dell'output, dovrebbe essere visualizzata una riga simile alla seguente: 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    indica `PID: XXXXXX` l'ID processo (PID) del processo dotnet.exe che è l'host di Funzioni in esecuzione.
 
1. Nell'output Funzioni di Azure runtime prendere nota dell'ID processo del processo host a cui verrà collegato un debugger. Si noti anche l'URL della funzione locale.

1. Dal menu **Debug** in Visual Studio selezionare Collega **a processo...**, individuare il processo che corrisponde all'ID del processo e selezionare **Collega**. 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="Collegare il debugger al processo host di Funzioni":::    

    Con il debugger collegato è possibile eseguire il debug del codice della funzione come di consueto.

1. Nella barra degli indirizzi del browser digitare l'URL della funzione locale, simile al seguente, ed eseguire la richiesta. 

    `http://localhost:7071/api/HttpExample`

    Verrà visualizzato l'output di traccia della richiesta scritta nel terminale in esecuzione. L'esecuzione del codice si interrompe in corrispondenza di qualsiasi punto di interruzione impostato nel codice della funzione.

1. Al termine, passare al terminale e premere CTRL+C per arrestare il processo host.
 
Dopo aver verificato la corretta esecuzione della funzione nel computer locale, è possibile pubblicare il progetto in Azure.

> [!NOTE]  
> Visual Studio la pubblicazione non è attualmente disponibile per le app di processo isolato .NET. Dopo aver completato lo sviluppo del progetto in Visual Studio, è necessario usare l'interfaccia della riga di comando di Azure per creare le risorse di Azure remote. È quindi possibile usare nuovamente Azure Functions Core Tools dalla riga di comando per pubblicare il progetto in Azure.
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>Creare le risorse di Azure di supporto per la funzione

Prima di distribuire il codice della funzione in Azure, è necessario creare tre risorse:

- Gruppo [di risorse](../azure-resource-manager/management/overview.md), che è un contenitore logico per le risorse correlate.
- Un [account di archiviazione](../storage/common/storage-account-create.md), usato per mantenere lo stato e altre informazioni sulle funzioni.
- Un'app per le funzioni, che fornisce l'ambiente per l'esecuzione del codice della funzione. Un'app per le funzioni si collega al progetto di funzione locale e consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione di risorse.

Usare i comandi seguenti per creare questi elementi.

1. Se non è già stato fatto, accedere ad Azure:

    ```azurecli
    az login
    ```

    Il comando [az login](/cli/azure/reference-index#az_login) consente di accedere all'account Azure.

1. Creare un gruppo di risorse denominato `AzureFunctionsQuickstart-rg` nell'area `westeurope`:

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Il comando [az group create](/cli/azure/group#az_group_create) crea un gruppo di risorse. In genere, il gruppo di risorse e le risorse vengono creati in un'area vicina alla propria, usando un'area disponibile restituita dal comando `az account list-locations`.

1. Creare un account di archiviazione per utilizzo generico nel gruppo di risorse e nell'area:

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Il comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) crea l'account di archiviazione. 

    Nell'esempio precedente sostituire `<STORAGE_NAME>` con un nome appropriato e univoco in Archiviazione di Azure. I nomi devono contenere da tre a 24 caratteri costituiti esclusivamente da numeri e lettere in minuscolo. `Standard_LRS` specifica un account per utilizzo generico, che è [supportato da Funzioni](storage-considerations.md#storage-account-requirements).

1. Creare l'app per le funzioni in Azure:
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    Il comando [az functionapp create](/cli/azure/functionapp#az_functionapp_create) crea l'app per le funzioni in Azure. 
    
    Nell'esempio precedente, sostituire `<STORAGE_NAME>` con il nome dell'account usato nel passaggio precedente e sostituire `<APP_NAME>` con un nome univoco a livello globale appropriato. `<APP_NAME>` è anche il dominio DNS predefinito per l'app per le funzioni. 
    
    Questo comando crea un'app per le funzioni che esegue .NET 5.0 nel [piano Funzioni di Azure a consumo](consumption-plan.md). Questo piano deve essere gratuito per la quantità di utilizzo sostenuta in questo articolo. Il comando effettua anche il provisioning di un'applicazione Azure Insights associata nello stesso gruppo di risorse. Usare questa istanza per monitorare l'app per le funzioni e visualizzare i log. Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md). L'istanza non comporta costi finché non viene attivata.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Pubblicare il progetto in Azure

In questa sezione verrà creata un'app per le funzioni con le risorse correlate nella sottoscrizione di Azure e quindi verrà distribuito il codice.

> [!IMPORTANT]
> La pubblicazione in un'app per le funzioni esistente sovrascrive il contenuto di tale app in Azure.


1. Selezionare l'icona di Azure nella barra attività, quindi nell'area **Azure: Funzioni** scegliere il pulsante **Deploy to function app...** (Distribuisci nell'app per le funzioni...).

    ![Pubblicare il progetto in Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Quando richiesto, immettere le informazioni seguenti:

    - **Selezionare la cartella**: scegliere una cartella dall'area di lavoro o selezionarne una che contenga l'app per le funzioni. Questa richiesta non verrà visualizzata quando è già aperta un'app per le funzioni valida.

    - **Selezionare la sottoscrizione**: scegliere la sottoscrizione da usare. Questa richiesta non verrà visualizzata quando si ha una sola sottoscrizione.

    - **Selezionare l'app per le funzioni in Azure**: Scegliere `- Create new Function App`. Non scegliere l'opzione `Advanced`, che non è illustrata in questo articolo.
      
    - **Immettere un nome univoco a livello globale per l'app per le funzioni**: digitare un nome valido in un percorso URL. Il nome digitato viene convalidato per assicurarsi che sia univoco in Funzioni di Azure.
    
    - **Selezionare uno stack di runtime:** scegliere `.NET 5 (non-LTS)` . 
    
    - **Selezionare una località per le nuove risorse**:  per prestazioni ottimali, scegliere un'[area](https://azure.microsoft.com/regions/) vicina. 
    
    Nell'area di notifica viene visualizzato lo stato delle singole risorse durante la creazione in Azure.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Notifica della creazione di risorse di Azure":::
    
1.  Al termine, nella sottoscrizione vengono create le risorse di Azure seguenti con i nomi basati sul nome dell'app per le funzioni:
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Dopo la creazione dell'app per le funzioni e dopo l'applicazione del pacchetto di distribuzione viene visualizzata una notifica. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Selezionare **Visualizza output** nelle notifiche per visualizzare i risultati della creazione e della distribuzione, incluse le risorse di Azure create. Se non si riesce a visualizzare la notifica, selezionare l'icona della campana nell'angolo in basso a destra per visualizzarla di nuovo.

    ![Notifica di creazione completata](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>Pulire le risorse

Sono state create risorse per completare questo articolo. Per tali risorse potrebbero venire addebitati costi, a seconda dello [stato dell'account](https://azure.microsoft.com/account/) e dei [prezzi dei servizi](https://azure.microsoft.com/pricing/). 

::: zone pivot="development-environment-cli"  
Usare il comando seguente per eliminare il gruppo di risorse e tutte le risorse contenute per evitare costi aggiuntivi.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
Usare la procedura seguente per eliminare l'app per le funzioni e le relative risorse correlate per evitare costi aggiuntivi.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
Usare la procedura seguente per eliminare l'app per le funzioni e le relative risorse correlate per evitare costi aggiuntivi.

1. In Cloud Explorer espandere la sottoscrizione > **Servizi app**, fare clic con il pulsante destro del mouse sull'app per le funzioni e scegliere **Apri nel portale**. 

1. Nella pagina dell'app per le funzioni selezionare la scheda **Panoramica** e quindi il collegamento sotto **Gruppo di risorse**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Selezionare il gruppo di risorse da eliminare nella pagina dell'app per le funzioni.":::

2. Nella pagina **Gruppo di risorse** esaminare l'elenco delle risorse incluse e verificare che siano quelle da eliminare.
 
3. Selezionare **Elimina gruppo di risorse** e seguire le istruzioni.

   L'eliminazione potrebbe richiedere alcuni minuti. Al termine, viene visualizzata una notifica per pochi secondi. È anche possibile selezionare l'icona a forma di campana nella parte superiore della pagina per visualizzare la notifica.
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Altre informazioni sulle funzioni isolate .NET](dotnet-isolated-process-guide.md)

