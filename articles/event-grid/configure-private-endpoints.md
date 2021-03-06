---
title: Configurare endpoint privati per Griglia di eventi di Azure o domini
description: Questo articolo descrive come configurare endpoint privati per Griglia di eventi di Azure o dominio.
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 85546e99a8c431dc75b1af3d5044e06a18cf226d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770508"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Configurare endpoint privati per Griglia di eventi di Azure o domini
È possibile usare [endpoint](../private-link/private-endpoint-overview.md) privati per consentire l'ingresso di eventi direttamente dalla [](../private-link/private-link-overview.md) rete virtuale agli argomenti e ai domini in modo sicuro tramite un collegamento privato senza passare attraverso la rete Internet pubblica. L'endpoint privato usa un indirizzo IP dallo spazio indirizzi della rete virtuale per l'argomento o il dominio. Per altre informazioni concettuali, vedere [Sicurezza di rete](network-security.md).

Questo articolo descrive come configurare endpoint privati per argomenti o domini.

## <a name="use-azure-portal"></a>Usare il portale di Azure 
Questa sezione illustra come usare il portale di Azure per creare un endpoint privato per un argomento o un dominio.

> [!NOTE]
> I passaggi illustrati in questa sezione sono principalmente per gli argomenti. È possibile usare passaggi simili per creare endpoint privati per **i domini**. 

1. Accedere al portale di Azure [e](https://portal.azure.com) passare all'argomento o al dominio.
2. Passare alla **scheda Rete** della pagina dell'argomento. Selezionare **+ Endpoint privato sulla** barra degli strumenti.

    ![Aggiungere un endpoint privato](./media/configure-private-endpoints/add-button.png)
2. Nella pagina **Informazioni di base** seguire questa procedura: 
    1. Selezionare una **sottoscrizione di Azure** in cui si vuole creare l'endpoint privato. 
    2. Selezionare un **gruppo di risorse di Azure** per l'endpoint privato. 
    3. Immettere un **nome** per l'endpoint. 
    4. Selezionare **l'area** per l'endpoint. L'endpoint privato deve essere nella stessa area della rete virtuale, ma può essere in un'area diversa dalla risorsa di collegamento privato (in questo esempio, un argomento di Griglia di eventi). 
    5. Selezionare quindi **Il pulsante >** risorsa nella parte inferiore della pagina. 

      ![Endpoint privato - pagina Informazioni di base](./media/configure-private-endpoints/basics-page.png)
3. Nella pagina **Risorsa** seguire questa procedura: 
    1. Per il metodo di connessione, se si seleziona **Connetti a una risorsa di Azure nella directory**, seguire questa procedura. Questo esempio illustra come connettersi a una risorsa di Azure nella directory . 
        1. Selezionare la **sottoscrizione di Azure** in cui si trova **l'argomento o il** dominio. 
        1. Per **Tipo di risorsa** selezionare **Microsoft.EventGrid/topics** o **Microsoft.EventGrid/domains** per **Tipo di risorsa.**
        2. Per **Risorsa** selezionare un argomento/dominio dall'elenco a discesa. 
        3. Verificare che la **sottorisorsa di** destinazione sia impostata **su argomento** o **dominio** (in base al tipo di risorsa selezionato).    
        4. Selezionare **Avanti: Configurazione >** nella parte inferiore della pagina. 

            ![Screenshot che mostra la pagina "Creare un endpoint privato - Risorsa".](./media/configure-private-endpoints/resource-page.png)
    2. Se si seleziona **Connetti a una risorsa usando un ID risorsa o un alias,** seguire questa procedura:
        1. Immettere l'ID della risorsa. Ad esempio: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Per **Risorsa** immettere l'argomento **o** **il dominio**. 
        3. (facoltativo) Aggiungere un messaggio di richiesta. 
        4. Selezionare **Avanti: Configurazione >** nella parte inferiore della pagina. 

            ![Endpoint privato - pagina delle risorse](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Nella pagina **Configurazione** si seleziona la subnet in una rete virtuale in cui si vuole distribuire l'endpoint privato. 
    1. Selezionare una **rete virtuale**. Nell'elenco a discesa sono elencate solo le reti virtuali nella sottoscrizione e nella località attualmente selezionate. 
    2. Selezionare una **subnet** nella rete virtuale selezionata. 
    3. Selezionare **Avanti: Tag >** nella parte inferiore della pagina. 

    ![Endpoint privato - pagina di configurazione](./media/configure-private-endpoints/configuration-page.png)
5. Nella pagina **Tag** creare i tag (nomi e valori) da associare alla risorsa endpoint privato. Selezionare quindi il pulsante **Rivedi e crea** nella parte inferiore della pagina. 
6. In **Rivedi e crea** rivedere tutte le impostazioni e selezionare **Crea** per creare l'endpoint privato. 

    ![Endpoint privato: esaminare & pagina di creazione](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Gestire una connessione di collegamento privato

Quando si crea un endpoint privato, la connessione deve essere approvata. Se la risorsa per cui si sta creando un endpoint privato si trova nella propria directory, è possibile approvare la richiesta di connessione purché si abbiano autorizzazioni sufficienti. Se ci si sta connettendo a una risorsa di Azure in un'altra directory, è necessario attendere che il proprietario della risorsa approvi la richiesta di connessione.

Sono disponibili quattro stati di provisioning:

| Azione del servizio | Stato dell'endpoint privato del consumer del servizio | Descrizione |
|--|--|--|
| nessuno | In sospeso | La connessione viene creata manualmente ed è in attesa di approvazione dal proprietario della risorsa collegamento privato. |
| Approvazione | Approved | La connessione è stata approvata automaticamente o manualmente ed è pronta per essere usata. |
| Rifiuto | Rifiutato | La connessione è stata rifiutata dal proprietario della risorsa di collegamento privato. |
| Rimuovere | Disconnesso | La connessione è stata rimossa dal proprietario della risorsa di collegamento privato, l'endpoint privato diventa informativo e deve essere eliminato per la pulizia. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Come gestire una connessione endpoint privato
Le sezioni seguenti illustrano come approvare o rifiutare una connessione endpoint privato. 

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nella barra di ricerca digitare argomenti di **Griglia di eventi o** Domini di Griglia di **eventi.**
1. Selezionare **l'argomento** **o il** dominio da gestire.
1. Selezionare la scheda **Rete**.
1. Se sono presenti connessioni in sospeso, verrà visualizzata una connessione con **In** sospeso nello stato di provisioning. 

### <a name="to-approve-a-private-endpoint"></a>Per approvare un endpoint privato
È possibile approvare un endpoint privato nello stato in sospeso. Per approvare, seguire questa procedura: 

> [!NOTE]
> I passaggi illustrati in questa sezione sono principalmente per gli argomenti. È possibile usare passaggi simili per approvare gli endpoint privati per **i domini**. 

1. Selezionare **l'endpoint privato** che si vuole approvare e selezionare **Approva** sulla barra degli strumenti.

    ![Endpoint privato - stato in sospeso](./media/configure-private-endpoints/pending.png)
1. Nella finestra **di dialogo** Approva connessione immettere un commento (facoltativo) e selezionare **Sì**. 

    ![Endpoint privato - Approvare](./media/configure-private-endpoints/approve.png)
1. Verificare che lo stato dell'endpoint sia **Approvato.** 

    ![Endpoint privato : stato approvato](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Per rifiutare un endpoint privato
È possibile rifiutare un endpoint privato nello stato in sospeso o approvato. Per rifiutare, seguire questa procedura: 

> [!NOTE]
> I passaggi illustrati in questa sezione sono relativi agli argomenti. È possibile usare passaggi simili per rifiutare gli endpoint privati per **i domini**. 

1. Selezionare **l'endpoint privato** che si vuole rifiutare e selezionare **Rifiuta** sulla barra degli strumenti.

    ![Screenshot che mostra "Rete - Connessioni endpoint private" con l'opzione "Rifiuta" selezionata.](./media/configure-private-endpoints/reject-button.png)
1. Nella finestra **di dialogo** Rifiuta connessione immettere un commento (facoltativo) e selezionare **Sì**. 

    ![Endpoint privato - Rifiuta](./media/configure-private-endpoints/reject.png)
1. Verificare che lo stato dell'endpoint sia **Rifiutato.** 

    ![Endpoint privato : stato rifiutato](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Non è possibile approvare un endpoint privato nel portale di Azure dopo che è stato rifiutato. 


## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
Per creare un endpoint privato, usare [il metodo az network private-endpoint create,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) come illustrato nell'esempio seguente:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Per le descrizioni dei parametri usati nell'esempio, vedere la documentazione [per az network private-endpoint create.](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) Ecco alcuni punti da notare in questo esempio: 

- Per `private-connection-resource-id` , specificare l'ID risorsa dell'argomento **o** del **dominio**. Nell'esempio precedente viene utilizzato il tipo : argomento.
- per `group-ids` , specificare o `topic` `domain` . Nell'esempio precedente viene `topic` usato . 

Per eliminare un endpoint privato, usare [il metodo az network private-endpoint delete,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) come illustrato nell'esempio seguente:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> I passaggi illustrati in questa sezione sono relativi agli argomenti. È possibile usare una procedura simile per creare endpoint privati per **i domini**. 



### <a name="prerequisites"></a>Prerequisiti
Aggiornare l'Griglia di eventi di Azure per l'interfaccia della riga di comando eseguendo il comando seguente: 

```azurecli-interactive
az extension update -n eventgrid
```

Se l'estensione non è installata, eseguire il comando seguente per installarla: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Creare un endpoint privato
Per creare un endpoint privato, usare [il metodo az network private-endpoint create,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) come illustrato nell'esempio seguente:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Per le descrizioni dei parametri usati nell'esempio, vedere la documentazione [per az network private-endpoint create.](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) Ecco alcuni punti da notare in questo esempio: 

- Per `private-connection-resource-id` , specificare l'ID risorsa dell'argomento **o** del **dominio**. Nell'esempio precedente viene utilizzato il tipo : argomento.
- per `group-ids` , specificare o `topic` `domain` . Nell'esempio precedente viene `topic` usato . 

Per eliminare un endpoint privato, usare [il metodo az network private-endpoint delete,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) come illustrato nell'esempio seguente:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> I passaggi illustrati in questa sezione sono relativi agli argomenti. È possibile usare passaggi simili per creare endpoint privati per **i domini**. 

#### <a name="sample-script"></a>Script di esempio
Ecco uno script di esempio che crea le risorse di Azure seguenti:

- Resource group
- Rete virtuale
- Subnet nella rete virtuale
- Griglia di eventi di Azure argomento
- Endpoint privato per l'argomento

> [!NOTE]
> I passaggi illustrati in questa sezione sono relativi agli argomenti. È possibile usare passaggi simili per creare endpoint privati per i domini.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>Approvare un endpoint privato
Il frammento di codice dell'interfaccia della riga di comando di esempio seguente illustra come approvare una connessione all'endpoint privato. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Rifiutare un endpoint privato
Il frammento di codice dell'interfaccia della riga di comando di esempio seguente illustra come rifiutare una connessione di endpoint privato. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Disabilitare l'accesso alla rete pubblica
Per impostazione predefinita, l'accesso alla rete pubblica è abilitato per un argomento o un dominio di Griglia di eventi. Per consentire l'accesso solo tramite endpoint privati, disabilitare l'accesso alla rete pubblica eseguendo il comando seguente:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>Usare PowerShell
Questa sezione illustra come creare un endpoint privato per un argomento o un dominio usando PowerShell. 

### <a name="prerequisite"></a>Prerequisito
Seguire le istruzioni fornite in [Procedura:](../active-directory/develop/howto-create-service-principal-portal.md) Usare il portale per creare un'applicazione Azure AD e un'entità servizio in grado di accedere alle risorse per creare un'applicazione Azure Active Directory e annotare i valori per Id directory **(tenant),** ID applicazione **(client)** e Segreto **applicazione (client).** 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Preparare il token e le intestazioni per le chiamate API REST 
Eseguire i comandi prerequisiti seguenti per ottenere un token di autenticazione da usare con le chiamate API REST e l'autorizzazione e altre informazioni di intestazione. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Creare una subnet con i criteri di rete degli endpoint disabilitati

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Creare un argomento di Griglia di eventi con un endpoint privato

> [!NOTE]
> I passaggi illustrati in questa sezione sono relativi agli argomenti. È possibile usare una procedura simile per creare endpoint privati per **i domini**. 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Quando si verifica che l'endpoint sia stato creato, il risultato dovrebbe essere simile al seguente:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Approvare una connessione endpoint privato
Il frammento di codice di PowerShell di esempio seguente illustra come approvare un endpoint privato. 

> [!NOTE]
> I passaggi illustrati in questa sezione sono relativi agli argomenti. È possibile usare una procedura simile per approvare gli endpoint privati per **i domini**. 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Rifiutare una connessione endpoint privato
L'esempio seguente illustra come rifiutare un endpoint privato usando PowerShell. È possibile ottenere il GUID per l'endpoint privato dal risultato del comando GET precedente. 

> [!NOTE]
> I passaggi illustrati in questa sezione sono relativi agli argomenti. È possibile usare una procedura simile per rifiutare gli endpoint privati per **i domini**. 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

È possibile approvare la connessione anche dopo che è stata rifiutata tramite API. Se si usa portale di Azure, non è possibile approvare un endpoint che è stato rifiutato. 

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni su come configurare le impostazioni del firewall IP, vedere Configurare il firewall IP per Griglia di eventi di Azure [o domini.](configure-firewall.md)
* Per risolvere i problemi di connettività di rete, vedere Risolvere [i problemi di connettività di rete](troubleshoot-network-connectivity.md)
