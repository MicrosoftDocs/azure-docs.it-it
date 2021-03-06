---
title: Ripristinare le app eliminate
description: Informazioni su come ripristinare un'app eliminata nel Servizio app di Azure. Evitare i problemi relativi a un'app eliminata per errore.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e894e0a8bd20d6a1c3c833a4c0a3656c0dcd0f05
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833167"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Ripristinare un'app del Servizio app eliminata con PowerShell

Se l'app è stata eliminata per errore nel Servizio app di Azure, è possibile ripristinarla usando i comandi del [modulo Az di PowerShell](/powershell/azure/).

> [!NOTE]
> - Le app eliminate vengono rimosse definitivamente dal sistema 30 giorni dopo l'eliminazione iniziale. Dopo l'eliminazione, un'app non può essere ripristinata.
> - La funzionalità Annulla eliminazione non è supportata per il piano a consumo.
> - Le app del servizio app in ambiente del servizio app non supportano gli snapshot. Pertanto, la funzionalità di annullamento dell'eliminazione e la funzionalità di clonazione non sono supportate per le app del servizio app in esecuzione in un ambiente del servizio app.
>

## <a name="re-register-app-service-resource-provider"></a>Ripetere la registrazione del provider di risorse del Servizio app

Alcuni clienti potrebbero riscontrare un problema relativo al recupero non riuscito dell'elenco di app eliminate. Per risolvere il problema, eseguire il comando seguente:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Elencare le app eliminate

Per ottenere la raccolta delle app eliminate, è possibile usare `Get-AzDeletedWebApp`.

Per informazioni dettagliate su una specifica app eliminata, è possibile usare:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Le informazioni dettagliate includono:

- **DeletedSiteId**: Identificatore univoco per l'app, usato per gli scenari in cui sono state eliminate più app con lo stesso nome
- **SubscriptionID**: Sottoscrizione che contiene la risorsa eliminata
- **Località**: Posizione dell'app originale
- **ResourceGroupName**: Nome del gruppo di risorse originale
- **Name**: Nome dell’app originale.
- **Slot**: il nome dello slot.
- **Ora di eliminazione**: Quando è stata eliminata l'app  

## <a name="restore-deleted-app"></a>Ripristinare l’app eliminata

>[!NOTE]
> `Restore-AzDeletedWebApp` non è supportato per le app per le funzioni.

Una volta identificata l'app che si vuole ripristinare, è possibile ripristinarla usando `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -TargetResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Gli slot di distribuzione non vengono ripristinati come parte dell'app. Se è necessario ripristinare uno slot di staging, usare il `-Slot <slot-name>`  flag .
>

Gli input per il comando sono:

- **Gruppo di risorse di destinazione:** gruppo di risorse di destinazione in cui verrà ripristinata l'app
- **Name**: Nome dell’app, che deve essere globalmente univoco.
- **TargetAppServicePlanName**: Piano di servizio app collegato all'app

Per impostazione predefinita `Restore-AzDeletedWebApp` ripristina sia la configurazione dell'app che qualsiasi contenuto. Se si desidera ripristinare solo il contenuto, usare il flag `-RestoreContentOnly` con questo cmdlet.

> [!NOTE]
> Se l'app è stata ospitata in e quindi eliminata da un ambiente del servizio app, può essere ripristinata solo se il ambiente del servizio app esiste ancora.
>

Le informazioni complete di riferimento per i cmdlet sono disponibili qui: [Restore-AzDeletedWebApp](/powershell/module/az.websites/restore-azdeletedwebapp).
