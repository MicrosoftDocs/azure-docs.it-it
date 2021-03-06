---
title: Dischi ultra per le macchine virtuali-Azure Managed Disks
description: Informazioni sui dischi ultra per le macchine virtuali di Azure
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/16/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 43dac1692dd6ee4ed1ab67a9b18ca69738e0a0f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580504"
---
# <a name="using-azure-ultra-disks"></a>Uso di Azure ultra Disks

Questo articolo illustra come distribuire e usare un disco Ultra, per informazioni concettuali sui dischi Ultra, vedere [quali tipi di disco sono disponibili in Azure?](disks-types.md#ultra-disk).

Azure ultra Disks offre velocità effettiva elevata, IOPS elevate e archiviazione su disco a bassa latenza coerente per macchine virtuali IaaS di Azure. Questa nuova offerta fornisce prestazioni all'avanguardia con gli stessi livelli di disponibilità delle offerte di dischi esistenti. Uno dei vantaggi principali di ultra disks è la possibilità di modificare dinamicamente le prestazioni dell'unità SSD insieme ai carichi di lavoro senza dover riavviare le macchine virtuali. I dischi Ultra sono idonei per carichi di lavoro a elevato utilizzo di dati, come SAP HANA, database di alto livello e carichi di lavoro con numerose transazioni.

## <a name="ga-scope-and-limitations"></a>Ambito e limitazioni di GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Determinare la disponibilità di dimensioni e aree della macchina virtuale

### <a name="vms-using-availability-zones"></a>VM che usano le zone di disponibilità

Per sfruttare i dischi Ultra, è necessario determinare la zona di disponibilità in cui ci si trova. Non ogni area supporta tutte le dimensioni delle macchine virtuali con dischi Ultra. Per determinare se la regione, la zona e le dimensioni della macchina virtuale supportano i dischi Ultra, eseguire uno dei comandi seguenti, assicurarsi di sostituire prima i valori di **Region**, **vmSize** e **Subscription** :

#### <a name="cli"></a>CLI

```azurecli
subscription="<yourSubID>"
# example value is southeastasia
region="<yourLocation>"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
$sku = (Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})
if($sku){$sku[0].LocationInfo[0].ZoneDetails} Else {Write-host "$vmSize is not supported with Ultra Disk in $region region"}
```

La risposta sarà simile a quella riportata di seguito, dove X è la zona da usare per la distribuzione nell'area scelta. X può essere 1, 2 o 3.

Mantenere il valore **Zones** , che rappresenta la zona di disponibilità e sarà necessario per distribuire un disco Ultra.

|ResourceType  |Nome  |Location  |Zone  |Restrizione  |Funzionalità  |Valore  |
|---------|---------|---------|---------|---------|---------|---------|
|disks     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Se non è stata trovata alcuna risposta dal comando, le dimensioni della macchina virtuale selezionate non sono supportate con i dischi ultra nell'area selezionata.

Ora che si conosce la zona in cui eseguire la distribuzione, seguire i passaggi di distribuzione in questo articolo per distribuire una macchina virtuale con un disco Ultra collegato o collegare un disco Ultra a una macchina virtuale esistente.

### <a name="vms-with-no-redundancy-options"></a>VM senza opzioni di ridondanza

Per il momento, i dischi Ultra distribuiti in aree selezionate devono essere distribuiti senza opzioni di ridondanza. Tuttavia, non tutte le dimensioni del disco che supportano i dischi Ultra possono trovarsi in questa area. Per determinare quali dimensioni dei dischi supportano dischi Ultra, è possibile usare uno dei frammenti di codice seguenti. Assicurarsi di sostituire prima i `vmSize` `subscription` valori e:

```azurecli
subscription="<yourSubID>"
region="westus"
# example value is Standard_E64s_v3
vmSize="<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

La risposta sarà simile alla seguente: `UltraSSDAvailable   True` indica se le dimensioni della macchina virtuale supportano dischi ultra in questa area.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Distribuire un disco Ultra usando Azure Resource Manager

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. Per un elenco delle dimensioni delle VM supportate, vedere la sezione [ambito e limitazioni di GA](#ga-scope-and-limitations) .

Se si vuole creare una VM con più dischi Ultra, vedere l'esempio [creare una macchina virtuale con più dischi Ultra](https://aka.ms/ultradiskArmTemplate).

Se si intende usare un modello personalizzato, assicurarsi che **apiVersion** per `Microsoft.Compute/virtualMachines` e `Microsoft.Compute/Disks` sia impostato su `2018-06-01` (o versione successiva).

Impostare lo SKU del disco su **UltraSSD_LRS**, quindi impostare la capacità del disco, IOPS, la zona di disponibilità e la velocità effettiva in Mbps per creare un disco Ultra.

Dopo aver effettuato il provisioning della macchina virtuale, è possibile partizionare e formattare i dischi dati e configurarli per i carichi di lavoro.


## <a name="deploy-an-ultra-disk"></a>Distribuire un disco Ultra

# <a name="portal"></a>[Portale](#tab/azure-portal)

In questa sezione viene illustrata la distribuzione di una macchina virtuale dotata di disco rigido come disco dati. Si presuppone che l'utente abbia familiarità con la distribuzione di una macchina virtuale. in caso contrario, vedere [la Guida introduttiva: creare una macchina virtuale Windows nel portale di Azure](./windows/quick-create-portal.md).

1. Accedere al [portale di Azure](https://portal.azure.com/) e passare a distribuire una macchina virtuale (VM).
1. Assicurarsi di scegliere [le dimensioni e l'area della VM supportate](#ga-scope-and-limitations).
1. Selezionare **zona di disponibilità** in **Opzioni di disponibilità**.
1. Inserire le voci rimanenti con le selezioni preferite.
1. Selezionare **Dischi**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-create.png" alt-text="Screenshot del flusso di creazione della macchina virtuale, pannello nozioni di base." lightbox="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-create.png":::

1. Nel pannello dischi selezionare **Sì** per **Abilita compatibilità dischi Ultra**.
1. Selezionare **Crea e alleghi un nuovo disco** per alleghi ora un disco Ultra.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-vm-disk-enable.png" alt-text="Screenshot del flusso di creazione della macchina virtuale, del pannello del disco, della funzionalità ultra abilitata e della creazione e del allineamento di un nuovo disco." :::

1. Nel pannello **Crea un nuovo disco** immettere un nome, quindi selezionare **modifica dimensioni**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-create-disk.png" alt-text="Screenshot del pannello Crea nuovo disco, modifica dimensioni evidenziate.":::


1. Modificare lo **SKU del disco** in **ultra disk**.
1. Modificare i valori delle **dimensioni del disco personalizzato (GIB)**, **IOPS del disco** e la **velocità effettiva del disco** in uno di loro scelta.
1. Selezionare **OK** in entrambi i pannelli.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-select-ultra-disk-size.png" alt-text="Screenshot del pannello selezionare una dimensione del disco, disco Ultra selezionato per tipo di archiviazione, altri valori evidenziati.":::

1. Continuare con la distribuzione della macchina virtuale, sarà uguale a quella di qualsiasi altra macchina virtuale.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. Per un elenco delle dimensioni delle VM supportate, vedere la sezione [ambito e limitazioni di GA](#ga-scope-and-limitations) .

È necessario creare una macchina virtuale in grado di usare dischi Ultra, per poter alleghiare un disco Ultra.

Sostituire o impostare le variabili **$VMName**, **$RgName**, **$DiskName**, **$location**, $password **$User con** valori personalizzati.  Impostare **$zone**  sul valore della zona di disponibilità ottenuta dall' [inizio di questo articolo](#determine-vm-size-and-region-availability). Eseguire quindi il comando dell'interfaccia della riga di comando seguente per creare una macchina virtuale ultra abilitata:

```azurecli-interactive
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. Per un elenco delle dimensioni delle VM supportate, vedere la sezione [ambito e limitazioni di GA](#ga-scope-and-limitations) .

Per usare dischi Ultra, è necessario creare una macchina virtuale in grado di usare dischi Ultra. Sostituire o impostare le variabili **$ResourceGroup** e **$vmName** con valori personalizzati. Impostare **$zone** sul valore della zona di disponibilità ottenuta dall' [inizio di questo articolo](#determine-vm-size-and-region-availability). Eseguire quindi il comando [New-AzVm](/powershell/module/az.compute/new-azvm) seguente per creare una macchina virtuale ultra abilitata:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="create-and-attach-the-disk"></a>Creazione e alconnessione del disco

Una volta distribuita la VM, è possibile creare e alleghirvi un disco Ultra, usare lo script seguente:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---

## <a name="deploy-an-ultra-disk---512-byte-sector-size"></a>Distribuire una dimensione del settore del disco Ultra-512 byte

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com/), quindi cercare e selezionare **dischi**.
1. Selezionare **+ nuovo** per creare un nuovo disco.
1. Selezionare un'area che supporta i dischi Ultra e selezionare una zona di disponibilità, inserire il resto dei valori desiderati.
1. Selezionare **Modifica dimensioni**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/create-managed-disk-basics-workflow.png" alt-text="Screenshot del pannello Crea disco, area, zona di disponibilità e dimensioni delle modifiche evidenziate.":::

1. Per lo **SKU del disco** selezionare **ultra disk**, quindi immettere i valori per le prestazioni desiderate e selezionare **OK**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-disk-size-ultra.png" alt-text="Screenshot della creazione del disco Ultra.":::

1. Nel pannello **nozioni di base** selezionare la scheda **Avanzate** .
1. Selezionare **512** per **dimensioni settore logico**, quindi selezionare **Verifica + crea**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-different-sector-size-ultra.png" alt-text="Screenshot del selettore per la modifica delle dimensioni del settore logico del disco Ultra a 512.":::

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. Per un elenco delle dimensioni delle VM supportate, vedere la sezione [ambito e limitazioni di GA](#ga-scope-and-limitations) .

È necessario creare una macchina virtuale in grado di usare dischi Ultra, per poter alleghiare un disco Ultra.

Sostituire o impostare le variabili **$VMName**, **$RgName**, **$DiskName**, **$location**, $password **$User con** valori personalizzati.  Impostare **$zone**  sul valore della zona di disponibilità ottenuta dall' [inizio di questo articolo](#determine-vm-size-and-region-availability). Eseguire quindi il comando CLI seguente per creare una macchina virtuale con un disco Ultra con dimensioni di settore di 512 byte:

```azurecli
#create an ultra disk with 512 sector size
az disk create --subscription $subscription -n $diskname -g $rgname --size-gb 1024 --location $location --sku UltraSSD_LRS --disk-iops-read-write 8192 --disk-mbps-read-write 400 --logical-sector-size 512
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location --attach-data-disks $diskname
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Determinare prima di tutto le dimensioni della macchina virtuale da distribuire. Per un elenco delle dimensioni delle VM supportate, vedere la sezione [ambito e limitazioni di GA](#ga-scope-and-limitations) .

Per usare dischi Ultra, è necessario creare una macchina virtuale in grado di usare dischi Ultra. Sostituire o impostare le variabili **$ResourceGroup** e **$vmName** con valori personalizzati. Impostare **$zone** sul valore della zona di disponibilità ottenuta dall' [inizio di questo articolo](#determine-vm-size-and-region-availability). Eseguire quindi il comando [New-AzVm](/powershell/module/az.compute/new-azvm) seguente per creare una macchina virtuale ultra abilitata:

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
```

Per creare e alleghi un disco Ultra con dimensioni di settore di 512 byte, è possibile usare lo script seguente:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-LogicalSectorSize 512 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
---
## <a name="attach-an-ultra-disk"></a>Alleghi un disco Ultra

# <a name="portal"></a>[Portale](#tab/azure-portal)

In alternativa, se la macchina virtuale esistente si trova in un'area geografica o in una zona di disponibilità in grado di usare dischi Ultra, è possibile usare dischi Ultra senza dover creare una nuova macchina virtuale. Abilitando i dischi Ultra nella VM esistente, quindi li alleghi come dischi dati. Per abilitare la compatibilità con dischi Ultra, è necessario arrestare la macchina virtuale. Dopo l'arresto della macchina virtuale, è possibile abilitare la compatibilità, quindi riavviare la macchina virtuale. Una volta abilitata la compatibilità, è possibile aggiungere un disco Ultra:

1. Passare alla macchina virtuale e arrestarla e attenderne la deallocazione.
1. Dopo la deallocazione della macchina virtuale, selezionare **dischi**.
1. Seleziona **Impostazioni aggiuntive**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-ultra-disk-additional-settings.png" alt-text="Screenshot del pannello del disco, impostazioni aggiuntive evidenziate.":::

1. Selezionare **Sì** per **Enable ultra disk Compatibility**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="Screenshot di Abilita compatibilità disco Ultra.":::

1. Selezionare **Salva**.
1. Selezionare **Crea e alleghi un nuovo disco** e immettere un nome per il nuovo disco.
1. Per **tipo di archiviazione** selezionare **ultra disk**.
1. Modificare i valori delle **dimensioni (GIB)**, il **numero massimo di IOPS** e la **velocità effettiva massima** in quelli scelti.
1. Dopo che si è tornati al pannello del disco, selezionare **Salva**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/new-create-ultra-disk-existing-vm.png" alt-text="Screenshot del pannello del disco, con l'aggiunta di un nuovo disco Ultra.":::

1. Avviare di nuovo la macchina virtuale.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

In alternativa, se la macchina virtuale esistente si trova in un'area geografica o in una zona di disponibilità in grado di usare dischi Ultra, è possibile usare dischi Ultra senza dover creare una nuova macchina virtuale.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---cli"></a>Abilitare la compatibilità del disco ultra in una macchina virtuale esistente-interfaccia della riga di comando

Se la macchina virtuale soddisfa i requisiti descritti nell' [ambito e nelle limitazioni di GA](#ga-scope-and-limitations) e si trova nell' [area appropriata per l'account](#determine-vm-size-and-region-availability), è possibile abilitare la compatibilità del disco Ultra nella macchina virtuale.

Per abilitare la compatibilità con dischi Ultra, è necessario arrestare la macchina virtuale. Dopo l'arresto della macchina virtuale, è possibile abilitare la compatibilità, quindi riavviare la macchina virtuale. Una volta abilitata la compatibilità, è possibile aggiungere un disco Ultra:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk---cli"></a>Creare un disco Ultra-interfaccia della riga di comando

Ora che si dispone di una macchina virtuale in grado di alleghire dischi Ultra, è possibile creare e collegarvi un disco Ultra.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

### <a name="attach-the-disk---cli"></a>Connetti disco-interfaccia della riga di comando

```azurecli
rgName="<yourResourceGroupName>"
vmName="<yourVMName>"
diskName="<yourDiskName>"
subscriptionId="<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In alternativa, se la macchina virtuale esistente si trova in un'area geografica o in una zona di disponibilità in grado di usare dischi Ultra, è possibile usare dischi Ultra senza dover creare una nuova macchina virtuale.

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm---powershell"></a>Abilitare la compatibilità del disco ultra in una macchina virtuale esistente-PowerShell

Se la macchina virtuale soddisfa i requisiti descritti nell' [ambito e nelle limitazioni di GA](#ga-scope-and-limitations) e si trova nell' [area appropriata per l'account](#determine-vm-size-and-region-availability), è possibile abilitare la compatibilità del disco Ultra nella macchina virtuale.

Per abilitare la compatibilità con dischi Ultra, è necessario arrestare la macchina virtuale. Dopo l'arresto della macchina virtuale, è possibile abilitare la compatibilità, quindi riavviare la macchina virtuale. Una volta abilitata la compatibilità, è possibile aggiungere un disco Ultra:

```azurepowershell
#Stop the VM
Stop-AzVM -Name $vmName -ResourceGroupName $rgName
#Enable ultra disk compatibility
$vm1 = Get-AzVM -name $vmName -ResourceGroupName $rgName
Update-AzVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled $True
#Start the VM
Start-AzVM -Name $vmName -ResourceGroupName $rgName
```

### <a name="create-and-attach-an-ultra-disk---powershell"></a>Creare e alleghi un disco Ultra-PowerShell

Ora che si dispone di una macchina virtuale in grado di usare dischi Ultra, è possibile creare e alleghirvi un disco Ultra:

```powershell
# Set parameters and select subscription
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Connect-AzAccount -SubscriptionId $subscription

# Create the disk
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName $diskName `
-Disk $diskconfig;

# add disk to VM
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

---
## <a name="adjust-the-performance-of-an-ultra-disk"></a>Regolare le prestazioni di un disco Ultra

# <a name="portal"></a>[Portale](#tab/azure-portal)

I dischi Ultra offrono una funzionalità univoca che consente di regolare le prestazioni. Queste modifiche possono essere apportate dal portale di Azure sui dischi.

1. Passare alla macchina virtuale e selezionare **dischi**.
1. Selezionare il disco Ultra a cui si desidera modificare le prestazioni.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/select-ultra-disk-to-modify.png" alt-text="Screenshot del pannello dischi nella macchina virtuale, il disco Ultra è evidenziato.":::

1. Selezionare **dimensioni e prestazioni** e quindi apportare le modifiche.
1. Selezionare **Salva**.

    :::image type="content" source="media/virtual-machines-disks-getting-started-ultra-ssd/modify-ultra-disk-performance.png" alt-text="Screenshot del pannello di configurazione su disco Ultra, dimensioni del disco, IOPS e velocità effettiva sono evidenziati. il salvataggio viene evidenziato.":::

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

I dischi Ultra offrono una funzionalità univoca che consente di regolare le prestazioni. il comando seguente illustra come usare questa funzionalità:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>Regolare le prestazioni di un disco Ultra con PowerShell

I dischi Ultra hanno una funzionalità univoca che consente di regolare le prestazioni. il comando seguente è un esempio che consente di modificare le prestazioni senza dover scollegare il disco:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
---

## <a name="next-steps"></a>Passaggi successivi

- [Usare Azure ultra disks nel servizio Azure Kubernetes (anteprima)](../aks/use-ultra-disks.md).
- [Eseguire la migrazione del disco di log a un disco Ultra](../azure-sql/virtual-machines/windows/storage-migrate-to-ultradisk.md).
