---
title: Collegamento privato - Interfaccia della riga di comando di Azure - Database di Azure per MySQL
description: Informazioni su come configurare il collegamento privato per Database di Azure per MySQL dall'interfaccia della riga di comando di Azure
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 01/09/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: e2fbf95dcf2d5e3447197bc71105e415cce6681e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763326"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-using-cli"></a>Creare e gestire un collegamento privato per Database di Azure per MySQL tramite l'interfaccia della riga di comando

Un endpoint privato è il blocco predefinito fondamentale per il collegamento privato in Azure. Consente alle risorse di Azure, come le macchine virtuali (VM), di comunicare privatamente con risorse Collegamento privato. Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare una macchina virtuale in una rete virtuale di Azure e un server di Database di Azure per MySQL con un endpoint privato di Azure.

> [!NOTE]
> La funzionalità di collegamento privato è disponibile solo per i server di Database di Azure per MySQL nei piani per utilizzo generico o ottimizzati per la memoria. Verificare che il server di database sia in uno di questi piani tariffari.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Per poter creare le risorse, è prima necessario creare un gruppo di risorse in cui ospitare la rete virtuale. Come prima cosa creare un gruppo di risorse con [az group create](/cli/azure/group). Questo esempio crea un gruppo di risorse *denominato myResourceGroup* nella *località westeurope:*

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Creare una rete virtuale
Creare una rete virtuale con il comando [az network vnet create](/cli/azure/network/vnet). Questo esempio crea una rete virtuale predefinita denominata *myVirtualNetwork* con una subnet denominata *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Disabilitare i criteri per gli endpoint privati della subnet 
Azure distribuisce le risorse in una subnet all'interno di una rete virtuale, quindi è necessario creare o aggiornare la subnet per disabilitare i criteri di rete degli endpoint [privati.](../private-link/disable-private-endpoint-network-policy.md) Aggiornare una configurazione di subnet denominata *mySubnet* con [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Creare la VM 
Creare una macchina virtuale con il comando az vm create. Quando richiesto, specificare la password da usare come credenziali di accesso per la macchina virtuale. Questo esempio crea una macchina virtuale denominata *myVM*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
Prendere nota dell'indirizzo IP pubblico della macchina virtuale. Questo indirizzo verrà usato per connettersi alla VM da Internet nel passaggio successivo.

## <a name="create-an-azure-database-for-mysql-server"></a>Creare un server Database di Azure per MySQL 
Creare un database di Azure per MySQL con il comando az mysql server create. Tenere presente che il nome del server MySQL deve essere univoco in Azure, quindi sostituire il valore segnaposto tra parentesi quadre con il proprio valore univoco: 

```azurecli-interactive
# Create a server in the resource group 
az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

> [!NOTE]
> In alcuni casi, Database di Azure per MySQL e la subnet della rete virtuale sono in sottoscrizioni diverse. In questi casi è necessario garantire le configurazioni seguenti:
> - Assicurarsi che per entrambe le sottoscrizioni sia registrato il provider di risorse **Microsoft.DBforMySQL.** Per altre informazioni, fare riferimento a [resource-manager-registration][resource-manager-portal].

## <a name="create-the-private-endpoint"></a>Creare l'endpoint privato 
Creare un endpoint privato per il server MySQL nella rete virtuale: 

```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id $(az resource show -g myResourcegroup -n mydemoserver --resource-type "Microsoft.DBforMySQL/servers" --query "id" -o tsv) \    
    --group-id mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Configurare la zona DNS privato 
Creare una DNS privato per il dominio del server MySQL e creare un collegamento di associazione con la rete virtuale. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mysql.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mysql.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> L'FQDN nell'impostazione DNS del cliente non si risolve nell'indirizzo IP privato configurato. Sarà necessario configurare una zona DNS per il nome di dominio completo configurato, come illustrato [di seguito.](../dns/dns-operations-recordsets-portal.md)

## <a name="connect-to-a-vm-from-the-internet"></a>Connettersi a una VM da Internet

Connettersi alla macchina virtuale *myVm* da Internet come indicato di seguito:

1. Nella barra di ricerca del portale immettere *myVm*.

1. Scegliere il pulsante **Connetti**. Dopo aver selezionato il pulsante **Connetti** viene aperta la finestra **Connetti alla macchina virtuale**.

1. Selezionare **Scarica file RDP**. Azure crea e scarica nel computer un file Remote Desktop Protocol con estensione *.rdp*.

1. Aprire il file *downloaded.rdp*.

    1. Quando richiesto, selezionare **Connetti**.

    1. Immettere il nome utente e la password specificati al momento della creazione della macchina virtuale.

        > [!NOTE]
        > Potrebbe essere necessario selezionare **Altre opzioni** > **Usa un altro account** per specificare le credenziali immesse al momento della creazione della macchina virtuale.

1. Selezionare **OK**.

1. Durante il processo di accesso potrebbe essere visualizzato un avviso relativo al certificato. Se si riceve un avviso relativo al certificato, selezionare **Sì** oppure **Continua**.

1. Quando viene visualizzato il desktop della macchina virtuale, ridurlo a icona per tornare al desktop locale.  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Accedere al server MySQL privatamente dalla macchina virtuale

1. Nel desktop remoto di  *myVM* aprire PowerShell.

2. Immettere  `nslookup mydemomysqlserver.privatelink.mysql.database.azure.com`. 

    Verrà visualizzato un messaggio simile al seguente:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.privatelink.mysql.database.azure.com
    Address:  10.1.3.4
    ```

3. Testare la connessione di collegamento privato per il server MySQL usando qualsiasi client disponibile. Nell'esempio seguente è stato usato [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) per eseguire l'operazione.


4. In **Nuova connessione** immettere o selezionare queste informazioni:

    | Impostazione | Valore |
    | ------- | ----- |
    | Connection Name (Nome connessione)| Selezionare il nome di connessione desiderato.|
    | Nome host | Selezionare *mydemoserver.privatelink.mysql.database.azure.com* |
    | Username | Immettere il nome *username@servername* utente fornito durante la creazione del server MySQL. |
    | Password | Immettere una password specificata durante la creazione del server MySQL. |
    ||

5. Selezionare Connetti.

6. Esplorare i database dal menu a sinistra.

7. (Facoltativamente) Creare o eseguire query sulle informazioni dal database MySQL.

8. Chiudere la connessione desktop remoto a myVm.

## <a name="clean-up-resources"></a>Pulire le risorse 
Quando non sono più necessari, è possibile usare il comando az group delete per rimuovere il gruppo di risorse e tutte le risorse in esso contenute: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni [sull'endpoint privato di Azure](../private-link/private-endpoint-overview.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md