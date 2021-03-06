---
title: Registrazione delle risorse di diagnostica per un gruppo di sicurezza di rete
titlesuffix: Azure Virtual Network
description: Informazioni su come abilitare i log delle risorse di diagnostica dei contatori di eventi e regole per un gruppo di sicurezza di rete di Azure.
services: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 0d171dee87a391c5e1d66db10363e6823ef387c1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774162"
---
# <a name="resource-logging-for-a-network-security-group"></a>Registrazione delle risorse per un gruppo di sicurezza di rete

Un gruppo di sicurezza di rete (NSG) include regole che consentono o negano il traffico verso una subnet della rete virtuale, un'interfaccia di rete o entrambe. 

Quando si abilita la registrazione per un gruppo di protezione di rete, è possibile raccogliere i tipi seguenti di informazioni sul log delle risorse:

* **Evento:** vengono registrate voci relative alle regole dei gruppi di sicurezza di rete applicate alle macchine virtuali in base all'indirizzo MAC.
* **Contenitore di regole:** contiene voci per sapere quante volte ogni regola dei gruppi di sicurezza di rete è stata applicata per rifiutare o consentire il traffico. Lo stato di queste regole viene raccolto ogni 300 secondi.

I log delle risorse sono disponibili solo per i gruppi di protezione di rete distribuiti tramite il Azure Resource Manager di distribuzione. Non è possibile abilitare la registrazione delle risorse per i gruppi di protezione di rete distribuiti tramite il modello di distribuzione classica. Per altre informazioni sui due modelli, vedere le [informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

La registrazione delle risorse è abilitata *separatamente per ogni* gruppo di protezione di rete per cui si vogliono raccogliere dati di diagnostica. Se invece si è interessati ai log attività (operativi), vedere Registrazione [attività di](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Azure . Se si è interessati al flusso del traffico IP attraverso i NSG, vedere Log del flusso del gruppo di sicurezza di rete Network Watcher [Azure](../network-watcher/network-watcher-nsg-flow-logging-overview.md) 

## <a name="enable-logging"></a>Abilitazione della registrazione

È possibile usare il portale [di Azure,](#azure-portal) [PowerShell](#powershell)o l'interfaccia della riga [di comando di Azure](#azure-cli) per abilitare la registrazione delle risorse.

### <a name="azure-portal"></a>Portale di Azure

1. Accedere al [portale](https://portal.azure.com).
2. Selezionare **Tutti i servizi** e quindi digitare *gruppi di sicurezza di rete*. Selezionare la voce **Gruppi di sicurezza di rete** quando viene visualizzata nei risultati della ricerca.
3. Selezionare il gruppo di sicurezza di rete per cui si vuole abilitare l'accesso.
4. In **Monitoraggio** selezionare **Log di diagnostica** e quindi selezionare **Abilita diagnostica**, come illustrato nell'immagine seguente:

   ![Attivare la diagnostica](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. In **Impostazioni di diagnostica** immettere o selezionare le informazioni seguenti e quindi selezionare **Salva**:

    | Impostazione                                                                                     | Valore                                                          |
    | ---------                                                                                   |---------                                                       |
    | Nome                                                                                        | Un nome di propria scelta.  Ad esempio: *DiagnosticaGsr*      |
    | **Archivia in un account di archiviazione**, **Streaming in un hub eventi** e **Invia a Log Analytics** | È possibile selezionare tutte le destinazioni desiderate. Per altre informazioni su ognuna, vedere [Destinazioni dei log](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Selezionare una o entrambe le categorie di log. Per altre informazioni su dati registrati per ogni categoria, vedere [Categorie di log](#log-categories).                                                                                                                                             |
6. Visualizzare e analizzare i log. Per altre informazioni, vedere [Visualizzare e analizzare i log](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile eseguire i comandi seguenti in [Azure Cloud Shell](https://shell.azure.com/powershell) oppure in PowerShell dal computer. Azure Cloud Shell è una shell interattiva gratuita. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Se si esegue PowerShell dal computer, è necessario il modulo Azure PowerShell versione 1.0.0 o successiva. Per trovare la versione installata, eseguire `Get-Module -ListAvailable Az` nel computer. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è necessario eseguire anche per accedere ad Azure con un account con `Connect-AzAccount` le [autorizzazioni necessarie.](virtual-network-network-interface.md#permissions)

Per abilitare la registrazione delle risorse, è necessario l'ID di un gruppo di protezione di rete esistente. Se non si dispone di un gruppo di sicurezza di rete esistente, è possibile crearne uno con [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Recuperare il gruppo di sicurezza di rete per cui si vuole abilitare la registrazione delle risorse [con Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Ad esempio, per recuperare un gruppo di sicurezza di rete denominato *myNsg* esistente in un gruppo di risorse denominato *myResourceGroup*, immettere il comando seguente:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

È possibile scrivere log delle risorse in tre tipi di destinazione. Per altre informazioni, vedere [Destinazioni dei log](#log-destinations). In questo articolo, i log vengono inviati alla destinazione *Log Analytics*, a titolo di esempio. Recuperare un'area di lavoro Log Analytics esistente [con Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Ad esempio, per recuperare un'area di lavoro esistente denominata *myWorkspace* in un gruppo di risorse denominato *myWorkspaces*, immettere il comando seguente:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Se non si dispone di un'area di lavoro esistente, è possibile crearne una [con New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Esistono due categorie di registrazione per cui è possibile abilitare i log. Per altre informazioni, vedere [Categorie di log](#log-categories). Abilitare la registrazione delle risorse per il gruppo di protezione di rete [con Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). L'esempio seguente registra sia i dati della categoria evento che quelli della categoria contatore nell'area di lavoro per un gruppo sicurezza di rete, usando gli ID per il gruppo sicurezza di rete e l'area di lavoro recuperati in precedenza:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Se si vogliono registrare i dati solo per una categoria o l'altra, anziché entrambe, aggiungere l'opzione `-Categories` al comando precedente, seguita da *NetworkSecurityGroupEvent* o *NetworkSecurityGroupRuleCounter*. Se si vuole accedere a una [destinazione](#log-destinations) diversa rispetto a un'area di lavoro Log Analytics, usare i parametri appropriati per un [account di archiviazione](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) di Azure oppure per [Hub eventi](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).

Visualizzare e analizzare i log. Per altre informazioni, vedere [Visualizzare e analizzare i log](#view-and-analyze-logs).

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

È possibile eseguire i comandi seguenti in [Azure Cloud Shell](https://shell.azure.com/bash) oppure l'interfaccia della riga di comando di Azure sul computer. Azure Cloud Shell è una shell interattiva gratuita. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Se si esegue l'interfaccia della riga di comando sul computer, è necessaria la versione 2.0.38 o successiva. Per trovare la versione installata, eseguire `az --version` nel computer. Se è necessario eseguire l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando in locale, è necessario eseguire anche `az login` per accedere ad Azure con un account con le [autorizzazioni necessarie](virtual-network-network-interface.md#permissions).

Per abilitare la registrazione delle risorse, è necessario l'ID di un gruppo di protezione di rete esistente. Se non è disponibile un gruppo di sicurezza di rete esistente, è possibile crearne uno con [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create).

Recuperare il gruppo di sicurezza di rete per cui si vuole abilitare la registrazione delle risorse [con az network nsg show](/cli/azure/network/nsg#az_network_nsg_show). Ad esempio, per recuperare un gruppo di sicurezza di rete denominato *myNsg* esistente in un gruppo di risorse denominato *myResourceGroup*, immettere il comando seguente:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

È possibile scrivere log delle risorse in tre tipi di destinazione. Per altre informazioni, vedere [Destinazioni dei log](#log-destinations). In questo articolo, i log vengono inviati alla destinazione *Log Analytics*, a titolo di esempio. Per altre informazioni, vedere [Categorie di log](#log-categories).

Abilitare la registrazione delle risorse per il gruppo di sicurezza di rete [con az monitor diagnostic-settings create.](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) L'esempio seguente registra i dati delle categorie evento e contatore in un'area di lavoro esistente denominata *myWorkspace*, che si trova in un gruppo di risorse denominato *myWorkspaces*, e l'ID del gruppo di sicurezza di rete recuperato in precedenza:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Se non si dispone di un'area di lavoro, è possibile crearne una usando il [portale di Azure](../azure-monitor/logs/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Esistono due categorie di registrazione per cui è possibile abilitare i log.

Per registrare i dati di una sola categoria, rimuovere la categoria per la quale non si vogliono registrare i dati nel comando precedente. Se si vuole accedere a una [destinazione](#log-destinations) diversa rispetto a un'area di lavoro Log Analytics, usare i parametri appropriati per un [account di archiviazione](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) di Azure oppure per [Hub eventi](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).

Visualizzare e analizzare i log. Per altre informazioni, vedere [Visualizzare e analizzare i log](#view-and-analyze-logs).

## <a name="log-destinations"></a>Destinazioni dei log

I dati di diagnostica possono essere:
- [Scritti in un account di archiviazione di Azure](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage) per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione (in giorni) tramite le impostazioni di diagnostica delle risorse.
- [Trasmessi in streaming a un hub eventi](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs) per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata come Power BI.
- [Scritto nei log Monitoraggio di Azure .](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)

## <a name="log-categories"></a>Categorie di log

Per le categorie di log seguenti vengono scritti dati in formato JSON:

### <a name="event"></a>Evento

Il registro eventi contiene informazioni su quali regole del gruppo di sicurezza di rete vengono applicate alle macchine virtuali, in base all'indirizzo MAC. I dati seguenti vengono registrati per ogni evento. Nell'esempio seguente, i dati vengono registrati per una macchina virtuale con indirizzo IP 192.168.1.4 e indirizzo MAC 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Contatore regole

Il log contatore regole contiene informazioni su ogni regola applicata alle risorse. I dati di esempio seguenti vengono registrati ogni volta che viene applicata una regola. Nell'esempio seguente, i dati vengono registrati per una macchina virtuale con indirizzo IP 192.168.1.4 e indirizzo MAC 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> L'indirizzo IP di origine per la comunicazione non viene registrato. È comunque possibile abilitare la [registrazione dei flussi per i gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md) per un gruppo di sicurezza di rete, che registra tutte le informazioni sul contatore regole, oltre all'indirizzo IP di origine che ha avviato la comunicazione. I dati del log del flusso di NSG vengono scritti in un account di archiviazione di Azure. È possibile analizzare i dati con la funzionalità di [analisi del traffico](../network-watcher/traffic-analytics.md) di Azure Network Watcher.

## <a name="view-and-analyze-logs"></a>Visualizzare e analizzare i log

Per informazioni su come visualizzare i dati dei log delle risorse, vedere Panoramica dei log [della piattaforma Azure.](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Se si inviano i dati di diagnostica a:
- **Monitoraggio di Azure: è** possibile usare la soluzione di analisi dei gruppi di [sicurezza di](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) rete per ottenere informazioni dettagliate avanzate. La soluzione offre visualizzazioni per le regole dei gruppi di sicurezza di rete che consentono o negano il traffico, in base all'indirizzo MAC, dell'interfaccia di rete in una macchina virtuale.
- **Account di archiviazione Azure**: i dati vengono scritti in un file PT1H.json. È possibile trovare il:
  - Registro eventi nel percorso seguente: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Log contatore regole nel percorso seguente: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [registrazione delle attività.](../azure-monitor/essentials/platform-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) La registrazione delle attività è abilitata per impostazione predefinita per i gruppi di sicurezza di rete creati tramite qualsiasi modello di distribuzione di Azure. Per determinare quali operazioni sono state completate nei NGS nel log attività, cercare le voci che contengono i tipi di risorsa seguenti:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Per informazioni su come registrare le informazioni di diagnostica, per includere l'indirizzo IP di origine per ogni flusso, vedere [NSG flow logging](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Registrazione dei flussi per il gruppo di sicurezza di rete).
