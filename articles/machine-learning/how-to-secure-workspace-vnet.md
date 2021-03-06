---
title: Proteggere un'Azure Machine Learning di lavoro con reti virtuali
titleSuffix: Azure Machine Learning
description: Usare una rete virtuale di Azure isolata per proteggere l'area Azure Machine Learning lavoro e le risorse associate.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/17/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 20f75580c425cee9128f9c94123dcf902642eac4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531033"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Proteggere un'Azure Machine Learning di lavoro con reti virtuali

Questo articolo illustra come proteggere un'area di lavoro Azure Machine Learning e le risorse associate in una rete virtuale.

Questo articolo è la seconda parte di una serie in cinque parti che illustra come proteggere un flusso Azure Machine Learning lavoro. È consigliabile leggere prima la prima [parte:](how-to-network-security-overview.md) Panoramica della rete virtuale per comprendere l'architettura complessiva. 

Vedere gli altri articoli di questa serie:

[1. Panoramica della rete virtuale](how-to-network-security-overview.md)  >  **2. Proteggere l'area di**  >  [lavoro 3. Proteggere l'ambiente di training](how-to-secure-training-vnet.md)  >  [4. Proteggere l'ambiente di inferenza](how-to-secure-inferencing-vnet.md)  >  [5. Abilitare le funzionalità di Studio](how-to-enable-studio-virtual-network.md)

Questo articolo illustra come abilitare le risorse delle aree di lavoro seguenti in una rete virtuale:
> [!div class="checklist"]
> - Area di lavoro di Azure Machine Learning
> - Account di archiviazione di Azure
> - Azure Machine Learning di dati e set di dati
> - Azure Key Vault
> - Registro Azure Container

## <a name="prerequisites"></a>Prerequisiti

+ Leggere [l'articolo Panoramica della sicurezza di](how-to-network-security-overview.md) rete per comprendere gli scenari di rete virtuale comuni e l'architettura di rete virtuale complessiva.

+ Una rete virtuale e una subnet esistenti da usare con le risorse di calcolo.

+ Per distribuire le risorse in una rete virtuale o in una subnet, l'account utente deve avere le autorizzazioni per le azioni seguenti nel controllo degli accessi in base al ruolo di Azure:

    - "Microsoft.Network/virtualNetworks/join/action" nella risorsa di rete virtuale.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" nella risorsa subnet.

    Per altre informazioni sul controllo degli accessi in base al ruolo di Azure con funzionalità di rete, vedere Ruoli [predefiniti di rete](../role-based-access-control/built-in-roles.md#networking)


## <a name="secure-the-workspace-with-private-endpoint"></a>Proteggere l'area di lavoro con un endpoint privato

collegamento privato di Azure consente di connettersi all'area di lavoro usando un endpoint privato. L'endpoint privato è un set di indirizzi IP privati all'interno della rete virtuale. È quindi possibile limitare l'accesso all'area di lavoro in modo che si verifichi solo tramite gli indirizzi IP privati. Collegamento privato consente di ridurre il rischio di esfiltrazione dei dati.

Per altre informazioni sulla configurazione di un'area di lavoro Collegamento privato, vedere [Come configurare il collegamento privato.](how-to-configure-private-link.md)

> [!Warning]
> La protezione di un'area di lavoro con endpoint privati non garantisce da sola la sicurezza end-to-end. Per proteggere singoli componenti della soluzione, è necessario seguire i passaggi descritti nel resto di questo articolo e nella serie di reti virtuali.

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Proteggere gli account di archiviazione di Azure con gli endpoint di servizio

Azure Machine Learning supporta gli account di archiviazione configurati per l'uso di endpoint di servizio o di endpoint privati. Questa sezione illustra come proteggere un account di archiviazione di Azure usando gli endpoint di servizio. Per gli endpoint privati, vedere la sezione successiva.

Per usare un account di archiviazione di Azure per l'area di lavoro in una rete virtuale, seguire questa procedura:

1. Nel portale di Azure passare al servizio di archiviazione che si vuole usare nell'area di lavoro.

   [![Archiviazione collegata all'area di lavoro di Azure Machine Learning](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Nella pagina account del servizio di archiviazione selezionare __Rete__.

   ![Area di rete nella pagina Archiviazione di Azure nella portale di Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Nella scheda __Firewall e reti virtuali__ eseguire le azioni seguenti:
    1. Selezionare __Reti selezionate__.
    1. In __Reti virtuali__ selezionare il collegamento __Aggiungi rete virtuale esistente__. Questa azione aggiunge la rete virtuale in cui si trova il calcolo (vedere il passaggio 1).

        > [!IMPORTANT]
        > L'account di archiviazione deve trovarsi nella stessa rete virtuale e nella stessa subnet delle istanze di calcolo o dei cluster di elaborazione usati per il training o l'inferenza.

    1. Selezionare la casella di controllo __Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione__. Questa modifica non consente a tutti i servizi di Azure di accedere all'account di archiviazione.
    
        * Le risorse di alcuni servizi, **registrate nella sottoscrizione,** possono accedere all'account di **archiviazione nella stessa sottoscrizione per** le operazioni di selezione. Ad esempio, la scrittura di log o la creazione di backup.
        * Alle risorse di alcuni servizi è possibile concedere l'accesso esplicito all'account di archiviazione assegnando un ruolo __di Azure__ all'identità gestita assegnata dal sistema.

        Per altre informazioni, vedere [Configurare i firewall e le reti virtuali di Archiviazione di Azure](../storage/common/storage-network-security.md#trusted-microsoft-services).

    > [!IMPORTANT]
    > Quando si usa Azure Machine Learning SDK, l'ambiente di sviluppo deve essere in grado di connettersi all'account di archiviazione di Azure. Se l'account di archiviazione si trova all'interno di una rete virtuale, il firewall deve consentire l'accesso dall'indirizzo IP dell'ambiente di sviluppo.
    >
    > Per abilitare l'accesso all'account di archiviazione, vedere i __firewall e le reti virtuali__ per l'account di archiviazione *da un Web browser nel client di sviluppo*. Usare quindi la casella di controllo __Aggiungere l'indirizzo IP client__ per aggiungere l'indirizzo IP del client all'__intervallo di indirizzi__. È anche possibile usare il campo __INTERVALLO DI INDIRIZZI__ per immettere manualmente l'indirizzo IP dell'ambiente di sviluppo. Dopo aver aggiunto l'indirizzo IP per il client, è possibile accedere all'account di archiviazione usando l'SDK.

   [![Il riquadro "Firewall e reti virtuali" nel portale di Azure](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Proteggere gli account di archiviazione di Azure con endpoint privati

Azure Machine Learning supporta gli account di archiviazione configurati per l'uso di endpoint di servizio o di endpoint privati. Se l'account di archiviazione usa endpoint privati, è necessario configurare due endpoint privati per l'account di archiviazione predefinito:
1. Endpoint privato con una sottorisorsa **di** destinazione BLOB.
1. Endpoint privato con una sottorisorsa di destinazione **file** (condivisione file).

![Screenshot che mostra la pagina di configurazione dell'endpoint privato con le opzioni blob e file](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Per configurare un endpoint privato per  un account di  archiviazione che non sia l'archiviazione predefinita, selezionare il tipo di sottorisorsa di destinazione corrispondente all'account di archiviazione che si vuole aggiungere.

Per altre informazioni, vedere [Usare endpoint privati per Archiviazione di Azure](../storage/common/storage-private-endpoints.md)

## <a name="secure-datastores-and-datasets"></a>Proteggere gli archivi dati e i set di dati

In questa sezione si apprenderà come usare l'archivio dati e i set di dati nell'esperienza SDK con una rete virtuale. Per altre informazioni sull'esperienza di studio, vedere Usare studio di Azure Machine Learning [in una rete virtuale.](how-to-enable-studio-virtual-network.md)

Per accedere ai dati tramite l'SDK, è necessario usare il metodo di autenticazione richiesto dal singolo servizio in cui sono archiviati i dati. Ad esempio, se si registra un archivio dati per accedere ad Azure Data Lake Store Gen2, è comunque necessario usare un'entità servizio come documentato in [Connettersi](how-to-access-data.md#azure-data-lake-storage-generation-2)ai servizi di archiviazione di Azure .

### <a name="disable-data-validation"></a>Disabilitare la convalida dei dati

Per impostazione predefinita, Azure Machine Learning la validità dei dati e i controlli delle credenziali quando si tenta di accedere ai dati usando l'SDK. Se i dati sono dietro una rete virtuale, Azure Machine Learning non è possibile completare questi controlli. Per ignorare questo controllo, è necessario creare archivi dati e set di dati che ignorano la convalida.

### <a name="use-datastores"></a>Usare gli archivi dati

 Azure Data Lake Store Gen1 e Azure Data Lake Store Gen2 ignorano la convalida per impostazione predefinita, quindi non sono necessarie altre azioni. Tuttavia, per i servizi seguenti è possibile usare una sintassi simile per ignorare la convalida dell'archivio dati:

- Archiviazione BLOB di Azure
- Condivisione file di Azure
- PostgreSQL
- Database SQL di Azure

L'esempio di codice seguente crea un nuovo archivio dati BLOB di Azure e imposta `skip_validation=True` .

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Usare i set di dati

La sintassi per ignorare la convalida del set di dati è simile ai tipi di set di dati seguenti:
- File delimitato
- JSON 
- Parquet
- SQL
- File

Il codice seguente crea un nuovo set di dati JSON e imposta `validate=False` .

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>Sicurezza Azure Key Vault

Azure Machine Learning usa un'Key Vault associata per archiviare le credenziali seguenti:
* La stringa di connessione all'account di archiviazione associato
* Le password per le istanze del repository del contenitore di Azure
* Le stringhe di connessione agli archivi dati

Per usare le funzionalità di sperimentazione di Azure Machine Learning con Azure Key Vault in una rete virtuale, seguire questa procedura:

1. Passare alla Key Vault associata all'area di lavoro.

1. Nella pagina __Key Vault,__ nel riquadro sinistro, selezionare __Rete__.

1. Nella scheda __Firewall e reti virtuali__ eseguire le azioni seguenti:
    1. In __Consenti accesso da__ selezionare Endpoint privato e reti __selezionate.__
    1. In __Reti virtuali__ selezionare __Aggiungi reti virtuali esistenti__ per aggiungere la rete virtuale in cui risiede il calcolo della sperimentazione.
    1. In __Consentire ai servizi Microsoft attendibili di ignorare il firewall__ scegliere __Sì__.

   [![Sezione "Firewall e reti virtuali" nel riquadro Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Abilitare Registro Azure Container (ACR)

Per usare Registro Azure Container all'interno di una rete virtuale, è necessario soddisfare i requisiti seguenti:

* Il Registro Azure Container deve essere versione Premium. Per altre informazioni sull'aggiornamento, vedere [Cambiare SKU](../container-registry/container-registry-skus.md#changing-tiers).

* Registro Azure Container deve essere nella stessa rete virtuale e nella stessa subnet dell'account di archiviazione e delle destinazioni di calcolo usati per il training o l'inferenza.

* L'area di lavoro di Azure Machine Learning deve contenere un [cluster di elaborazione di Azure Machine Learning](how-to-create-attach-compute-cluster.md).

    Quando Registro Azure Container è in una rete virtuale, Azure Machine Learning non può usarlo per compilare direttamente le immagini Docker. In questo caso viene usato il cluster di elaborazione per compilare le immagini.

    > [!IMPORTANT]
    > Il cluster di calcolo usato per creare immagini Docker deve essere in grado di accedere ai repository dei pacchetti usati per eseguire il training e la distribuzione dei modelli. Potrebbe essere necessario aggiungere regole di sicurezza di rete che consentono l'accesso a repository pubblici, usare pacchetti [Python](how-to-use-private-python-packages.md)privati o usare immagini [Docker](how-to-train-with-custom-image.md) personalizzate che includono già i pacchetti.

Dopo aver soddisfatto questi requisiti, seguire questa procedura per abilitare Registro Azure Container.

> [!TIP]
> Se durante la creazione dell'area di lavoro non è stata Registro Azure Container un'area di lavoro esistente, è possibile che non ne esista una. Per impostazione predefinita, l'area di lavoro non creerà un'istanza ACR finché non ne avrà bisogno. Per forzare la creazione di un modello, eseguire il training o distribuire un modello usando l'area di lavoro prima di usare la procedura descritta in questa sezione.

1. Trovare il nome del Registro Azure Container'area di lavoro usando uno dei metodi seguenti:

    __Azure portal__

    Nella sezione Panoramica dell'area di lavoro il valore __Registro__ è collegato al Registro Azure Container.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Registro Azure Container per l'area di lavoro" border="true":::

    __Interfaccia della riga di comando di Azure__

    Se è stata installata [l'estensione Machine Learning per l'interfaccia della riga di comando di Azure](reference-azure-machine-learning-cli.md), è possibile usare il comando `az ml workspace show` per visualizzare le informazioni sull'area di lavoro.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Il comando restituisce un valore analogo a `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"`. L'ultima parte della stringa è il nome del Registro Azure Container per l'area di lavoro.

1. Limitare l'accesso alla rete virtuale usando la procedura descritta in [Configurare l'accesso alla rete per il Registro di sistema.](../container-registry/container-registry-vnet.md#configure-network-access-for-registry) Quando si aggiunge la rete virtuale, selezionare la rete virtuale e la subnet per le risorse di Azure Machine Learning.

1. Configurare il controllo di accesso per l'area di lavoro [in Modo da consentire l'accesso da parte di servizi attendibili.](../container-registry/allow-access-trusted-services.md)

1. Usare Python SDK di Azure Machine Learning per configurare un cluster di elaborazione per compilare immagini Docker. Nel frammento di codice riportato di seguito viene illustrato come effettuare questa operazione:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = '')
    ```

    > [!IMPORTANT]
    > L'account di archiviazione, il cluster di elaborazione e Registro Azure Container devono essere tutti nella stessa subnet della rete virtuale.
    
    Per altre informazioni, vedere il riferimento per il metodo [update()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-).

## <a name="next-steps"></a>Passaggi successivi

Questo articolo è la seconda parte di una serie di reti virtuali in cinque parti. Vedere gli altri articoli per informazioni su come proteggere una rete virtuale:

* [Parte 1: Panoramica della rete virtuale](how-to-network-security-overview.md)
* [Parte 3: Proteggere l'ambiente di training](how-to-secure-training-vnet.md)
* [Parte 4: Proteggere l'ambiente di inferenza](how-to-secure-inferencing-vnet.md)
* [Parte 5: Abilitare le funzionalità di Studio](how-to-enable-studio-virtual-network.md)

Vedere anche l'articolo sull'uso [del DNS personalizzato per](how-to-custom-dns.md) la risoluzione dei nomi.
