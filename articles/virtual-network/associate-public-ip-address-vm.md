---
title: Associare un indirizzo IP pubblico a una macchina virtuale
titlesuffix: Azure Virtual Network
description: Informazioni su come associare un indirizzo IP pubblico a una macchina virtuale usando il portale di Azure o l'interfaccia della riga di comando di Azure.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 6e8fe92f88a5934c55febf42a0768274211ed76f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767710"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Associare un indirizzo IP pubblico a una macchina virtuale

Questo articolo illustra come associare un indirizzo IP pubblico a una macchina virtuale esistente. Se si vuole connettersi a una macchina virtuale da Internet, è necessario che alla macchina virtuale sia associato un indirizzo IP pubblico. Se si vuole creare una nuova macchina virtuale con un indirizzo IP pubblico, è possibile usare [portale di Azure](virtual-network-deploy-static-pip-arm-portal.md), l'interfaccia della riga di comando di [Azure](virtual-network-deploy-static-pip-arm-cli.md)o [PowerShell](virtual-network-deploy-static-pip-arm-ps.md). Per gli indirizzi IP pubblici è prevista una tariffa nominale. Per informazioni dettagliate, vedere [prezzi](https://azure.microsoft.com/pricing/details/ip-addresses/). È previsto un limite al numero di indirizzi IP pubblici che è possibile usare per ogni sottoscrizione. Per informazioni dettagliate, vedere [limiti.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)

È possibile usare [il](#azure-portal)portale di Azure , l'interfaccia della riga di comando [di](#azure-cli) Azure o [PowerShell](#powershell) per associare un indirizzo IP pubblico a una macchina virtuale.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Individuare o cercare la macchina virtuale a cui si vuole aggiungere l'indirizzo IP pubblico e quindi selezionarla.
3. In **Impostazioni** selezionare **Rete** e quindi selezionare l'interfaccia di rete a cui si vuole aggiungere l'indirizzo IP pubblico, come illustrato nell'immagine seguente:

   ![Selezionare l'interfaccia di rete](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > Gli indirizzi IP pubblici sono associati alle interfacce di rete collegate a una macchina virtuale. Nell'immagine precedente la macchina virtuale ha una sola interfaccia di rete. Se la macchina virtuale ha più interfacce di rete, vengono visualizzate tutte e si seleziona l'interfaccia di rete a cui si vuole associare l'indirizzo IP pubblico.

4. Selezionare **Configurazioni IP** e quindi una configurazione IP, come illustrato nell'immagine seguente:

   ![Selezionare La configurazione IP](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > Gli indirizzi IP pubblici sono associati alle configurazioni IP per un'interfaccia di rete. Nell'immagine precedente, l'interfaccia di rete ha una configurazione IP. Se l'interfaccia di rete ha più configurazioni IP, verranno tutte visualizzate nell'elenco e si selezionerà la configurazione IP a cui si vuole associare l'indirizzo IP pubblico.

5. Selezionare **Abilitato,** quindi selezionare **Indirizzo IP ( Configura impostazioni *obbligatorie*).** Scegliere un indirizzo IP pubblico esistente, che chiude automaticamente la **casella Scegli indirizzo IP** pubblico. Se non sono elencati indirizzi IP pubblici disponibili, è necessario crearne uno. Per informazioni su come, vedere [Creare un indirizzo IP pubblico.](virtual-network-public-ip-address.md#create-a-public-ip-address) Selezionare **Salva**, come illustrato nell'immagine seguente, quindi chiudere la casella per la configurazione IP.

   ![Abilitare l'indirizzo IP pubblico](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > Gli indirizzi IP pubblici visualizzati sono quelli presenti nella stessa area della macchina virtuale. Se nell'area sono stati creati più indirizzi IP pubblici, verranno visualizzati tutti qui. Se sono disattivati, l'indirizzo è già associato a una risorsa diversa.

6. Visualizzare l'indirizzo IP pubblico assegnato alla configurazione IP, come illustrato nell'immagine seguente. La visualizzazione di un indirizzo IP potrebbe richiedere alcuni secondi.

   ![Visualizzare l'indirizzo IP pubblico assegnato](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > L'indirizzo viene assegnato da un pool di indirizzi usati in ogni area di Azure. Per visualizzare un elenco dei pool di indirizzi usati in ogni area, vedere Microsoft Azure [IP del data center](https://www.microsoft.com/download/details.aspx?id=41653). L'indirizzo assegnato può essere qualsiasi indirizzo nei pool usati per l'area. Se è necessario assegnare l'indirizzo da un pool specifico nell'area, usare un [prefisso dell'indirizzo IP pubblico](public-ip-address-prefix.md).

7. [Consentire il traffico di rete alla macchina virtuale](#allow-network-traffic-to-the-vm) con regole di sicurezza in un gruppo di sicurezza di rete.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Installare l'[interfaccia della riga di comando di Azure ](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json) o usare Azure Cloud Shell. Azure Cloud Shell è una shell Bash gratuita che può essere eseguita direttamente nel portale di Azure. Include l'interfaccia della riga di comando di Azure preinstallata e configurata per l'uso con l'account. Selezionare il pulsante **Prova** nei comandi dell'interfaccia della riga di comando che seguono. Selezionando **Prova** viene richiamata un'istanza di Cloud Shell, a cui è possibile accedere con l'account Azure.

1. Se si usa l'interfaccia della riga di comando in locale in Bash, accedere ad Azure con `az login`.
2. Un indirizzo IP pubblico è associato a una configurazione IP di un'interfaccia di rete collegata a una macchina virtuale. Usare il [comando az network nic-ip-config update](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) per associare un indirizzo IP pubblico a una configurazione IP. L'esempio seguente associa un indirizzo IP pubblico esistente denominato *myVMPublicIP* alla configurazione IP *denominata ipconfigmyVM* di un'interfaccia di rete esistente denominata *myVMVMNic* presente in un gruppo di risorse *denominato myResourceGroup*.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Se non si ha un indirizzo IP pubblico esistente, usare [il comando az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) per crearne uno. Ad esempio, il comando seguente crea un indirizzo IP pubblico denominato *myVMPublicIP* in un gruppo di risorse denominato *myResourceGroup*.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Il comando precedente crea un indirizzo IP pubblico con valori predefiniti per diverse impostazioni che è possibile personalizzare. Per altre informazioni su tutte le impostazioni degli indirizzi IP pubblici, vedere [Creare un indirizzo IP pubblico.](virtual-network-public-ip-address.md#create-a-public-ip-address) L'indirizzo viene assegnato da un pool di indirizzi IP pubblici usati per ogni area di Azure. Per visualizzare un elenco dei pool di indirizzi usati in ogni area, vedere l'Microsoft Azure IP del data [center.](https://www.microsoft.com/download/details.aspx?id=41653)

   - Se non si conosce il nome di un'interfaccia di rete collegata alla macchina virtuale, usare il comando [az vm nic list](/cli/azure/vm/nic#az_vm_nic_list) per visualizzarle. Ad esempio, il comando seguente elenca i nomi delle interfacce di rete collegate a una macchina virtuale denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     L'output include una o più righe simili all'esempio seguente:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Nell'esempio precedente *myVMVMNic* è il nome dell'interfaccia di rete.

   - Se non si conosce il nome di una configurazione IP per un'interfaccia di rete, usare il comando [az network nic ip-config list](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) per recuperarle. Ad esempio, il comando seguente elenca i nomi delle configurazioni IP per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Visualizzare l'indirizzo IP pubblico assegnato alla configurazione IP con [il comando az vm list-ip-addresses.](/cli/azure/vm#az_vm_list_ip_addresses) L'esempio seguente mostra gli indirizzi IP assegnati a una macchina virtuale esistente denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > L'indirizzo viene assegnato da un pool di indirizzi usati in ogni area di Azure. Per visualizzare un elenco dei pool di indirizzi usati in ogni area, vedere l'Microsoft Azure IP del data [center.](https://www.microsoft.com/download/details.aspx?id=41653) L'indirizzo assegnato può essere qualsiasi indirizzo nei pool usati per l'area. Se è necessario assegnare l'indirizzo da un pool specifico nell'area, usare un [prefisso di indirizzo IP pubblico](public-ip-address-prefix.md).

4. [Consentire il traffico di rete verso la macchina virtuale](#allow-network-traffic-to-the-vm) con regole di sicurezza in un gruppo di sicurezza di rete.

## <a name="powershell"></a>PowerShell

Installare [PowerShell](/powershell/azure/install-az-ps) o usare Azure Cloud Shell. Azure Cloud Shell è una shell gratuita che può essere eseguita direttamente nel portale di Azure. Include PowerShell preinstallato e configurato per l'uso con l'account. Selezionare il pulsante **Prova** nei comandi di PowerShell che seguono. Selezionando **Prova** viene richiamata un'istanza di Cloud Shell, a cui è possibile accedere con l'account Azure.

1. Se si usa PowerShell in locale, accedere ad Azure con `Connect-AzAccount`.
2. Un indirizzo IP pubblico è associato a una configurazione IP di un'interfaccia di rete collegata a una macchina virtuale. Usare i [comandi Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) e [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) per ottenere la rete virtuale e la subnet in cui si trova l'interfaccia di rete. Usare quindi il [comando Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) per ottenere un'interfaccia di rete e il [comando Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) per ottenere un indirizzo IP pubblico esistente. Usare quindi il [comando Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) per associare l'indirizzo IP pubblico alla configurazione IP e il [comando Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) per scrivere la nuova configurazione IP nell'interfaccia di rete.

   L'esempio seguente associa un indirizzo IP pubblico esistente denominato *myVMPublicIP* alla configurazione IP denominata *ipconfigmyVM* di un'interfaccia di rete esistente denominata *myVMVMNic* presente in una subnet denominata *myVMSubnet* in una rete virtuale denominata *myVMVNet*. Tutte le risorse si trovano in un gruppo di risorse denominato *myResourceGroup*.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Se non si ha un indirizzo IP pubblico esistente, usare il [comando New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) per crearne uno. Ad esempio, il comando  seguente crea un indirizzo IP pubblico dinamico denominato *myVMPublicIP* in un gruppo di risorse denominato *myResourceGroup* nell'area *eastus.*
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Il comando precedente crea un indirizzo IP pubblico con valori predefiniti per diverse impostazioni che è possibile personalizzare. Per altre informazioni su tutte le impostazioni degli indirizzi IP pubblici, vedere [Creare un indirizzo IP pubblico.](virtual-network-public-ip-address.md#create-a-public-ip-address) L'indirizzo viene assegnato da un pool di indirizzi IP pubblici usati per ogni area di Azure. Per visualizzare un elenco dei pool di indirizzi usati in ogni area, vedere Microsoft Azure [IP del data center](https://www.microsoft.com/download/details.aspx?id=41653).

   - Se non si conosce il nome di un'interfaccia di rete collegata alla macchina virtuale, usare il comando [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) per visualizzarle. Ad esempio, il comando seguente elenca i nomi delle interfacce di rete collegate a una macchina virtuale denominata *myVM* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     L'output include una o più righe simili all'esempio che segue. Nell'output di esempio *myVMVMNic* è il nome dell'interfaccia di rete.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Se non si conosce il nome della rete virtuale o della subnet in cui si trova l'interfaccia di rete, usare il `Get-AzNetworkInterface` comando per visualizzare le informazioni. Ad esempio, il comando seguente ottiene le informazioni sulla rete virtuale e sulla subnet per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     L'output include una o più righe simili all'esempio che segue. Nell'output di esempio *myVMVNET* è il nome della rete virtuale e *myVMSubnet* è il nome della subnet.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Se non si conosce il nome di una configurazione IP per un'interfaccia di rete, usare il comando [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) per recuperarlo. Ad esempio, il comando seguente elenca i nomi delle configurazioni IP per un'interfaccia di rete denominata *myVMVMNic* in un gruppo di risorse denominato *myResourceGroup*:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     L'output include una o più righe simili all'esempio che segue. Nell'output di esempio *ipconfigmyVM* è il nome di una configurazione IP.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Visualizzare l'indirizzo IP pubblico assegnato alla configurazione IP con [il comando Get-AzPublicIpAddress.](/powershell/module/az.network/get-azpublicipaddress) L'esempio seguente mostra l'indirizzo assegnato a un indirizzo IP pubblico *denominato myVMPublicIP* in un gruppo di risorse denominato *myResourceGroup*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Se non si conosce il nome dell'indirizzo IP pubblico assegnato a una configurazione IP, eseguire i comandi seguenti per ottenerlo:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   L'output include una o più righe simili all'esempio che segue. Nell'output di esempio *myVMPublicIP* è il nome dell'indirizzo IP pubblico assegnato alla configurazione IP.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > L'indirizzo viene assegnato da un pool di indirizzi usati in ogni area di Azure. Per visualizzare un elenco dei pool di indirizzi usati in ogni area, vedere l'Microsoft Azure IP del data [center.](https://www.microsoft.com/download/details.aspx?id=41653) L'indirizzo assegnato può essere qualsiasi indirizzo nei pool usati per l'area. Se è necessario assegnare l'indirizzo da un pool specifico nell'area, usare un [prefisso di indirizzo IP pubblico](public-ip-address-prefix.md).

4. [Consentire il traffico di rete verso la macchina virtuale](#allow-network-traffic-to-the-vm) con regole di sicurezza in un gruppo di sicurezza di rete.

## <a name="allow-network-traffic-to-the-vm"></a>Consentire il traffico di rete verso la macchina virtuale

Prima di connettersi all'indirizzo IP pubblico da Internet, assicurarsi che le porte necessarie siano aperte in tutti i gruppi di sicurezza di rete che potrebbero essere associati all'interfaccia di rete, alla subnet in cui si trova l'interfaccia di rete o a entrambe. Anche se i gruppi di sicurezza filtrano il traffico verso l'indirizzo IP privato dell'interfaccia di rete, quando il traffico Internet in ingresso arriva all'indirizzo IP pubblico, Azure converte l'indirizzo pubblico nell'indirizzo IP privato, quindi se un gruppo di sicurezza di rete impedisce il flusso del traffico, la comunicazione con l'indirizzo IP pubblico ha esito negativo. È possibile visualizzare le regole di sicurezza effettive per un'interfaccia di rete e la relativa subnet usando il [portale](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), l'[interfaccia della riga di comando](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli) o [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell).

## <a name="next-steps"></a>Passaggi successivi

Consentire il traffico Internet in ingresso alla macchina virtuale con un gruppo di sicurezza di rete. Per informazioni su come creare un gruppo di sicurezza di rete, vedere [Usare i gruppi di sicurezza di rete.](manage-network-security-group.md#work-with-network-security-groups) Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza.](./network-security-groups-overview.md)
