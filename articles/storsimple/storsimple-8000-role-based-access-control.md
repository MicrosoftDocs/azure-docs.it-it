---
title: Usare il controllo degli accessi in base al ruolo di Azure per StorSimple | Microsoft Docs
description: Viene descritto come usare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) nel contesto di StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: 49c38e23ddbbfe983ff82ad25363c744292d4d69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518977"
---
# <a name="azure-role-based-access-control-for-storsimple"></a>Controllo degli accessi in base al ruolo di Azure per StorSimple

Questo articolo fornisce una breve descrizione del modo in cui il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) può essere usato per il dispositivo StorSimple. Il controllo degli accessi in base al ruolo di Azure offre una gestione degli accessi granulare Usare il controllo degli accessi in base al ruolo di Azure per concedere solo la giusta quantità di accesso agli utenti di StorSimple per svolgere il proprio lavoro anziché concedere a tutti l'accesso illimitato. Per altre informazioni sulle nozioni di base sulla gestione degli accessi in Azure, vedere [che cos'è il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md).

Questo articolo si applica ai dispositivi StorSimple serie 8000 con Update 3.0 o successivo in esecuzione nel portale di Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-roles-for-storsimple"></a>Ruoli di Azure per StorSimple

È possibile assegnare RBAC di Azure in base ai ruoli. che assicurano determinati livelli di autorizzazione in base alle risorse disponibili nell'ambiente. Esistono due tipi di ruoli tra cui gli utenti di StorSimple possono scegliere: predefiniti o personalizzati.

* **Ruoli predefiniti**: possono essere i ruoli di proprietario, collaboratore, lettore o amministratore Accesso utenti. Per altre informazioni, vedere [ruoli predefiniti per il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/built-in-roles.md).

* **Ruoli personalizzati** : se i ruoli predefiniti non soddisfano le proprie esigenze, è possibile creare ruoli personalizzati di Azure per StorSimple. Per creare un ruolo personalizzato di Azure, iniziare con un ruolo predefinito, modificarlo e quindi importarlo di nuovo nell'ambiente. Il download e l'upload del ruolo vengono gestiti tramite Azure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Creare ruoli personalizzati per il controllo degli accessi in base al ruolo di Azure](../role-based-access-control/custom-roles.md).

Per visualizzare i diversi ruoli disponibili per un utente di un dispositivo StorSimple nel portale di Azure, passare al servizio Gestione dispositivi StorSimple e quindi selezionare **Controllo di accesso (IAM) > Ruoli**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Creare un ruolo personalizzato per Amministratore dell'infrastruttura StorSimple

Nell'esempio seguente si inizia con il ruolo predefinito **Lettore**, che consente agli utenti di visualizzare tutti gli ambiti di risorsa, ma non di modificarli o di crearne nuovi. Si estenderà quindi questo ruolo per creare un nuovo ruolo personalizzato amministratore dell'infrastruttura di StorSimple. Questo ruolo viene assegnato agli utenti che possono gestire l'infrastruttura per i dispositivi StorSimple.

1. Eseguire Windows PowerShell come amministratore.

2. Accedere ad Azure.

    `Connect-AzAccount`

3. Esportare il ruolo Lettore nel computer come modello JSON.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Aprire il file JSON in Visual Studio. Si noterà che un tipico ruolo di Azure è costituito da tre sezioni principali, **Actions**, **notacts** e **AssignableScopes**.

    Nella sezione **Action** vengono elencate tutte le operazioni consentite per questo ruolo. Ogni azione viene assegnata da un provider di risorse. Per il ruolo Amministratore dell'infrastruttura StorSimple, usare il provider di risorse `Microsoft.StorSimple`.

    Per visualizzare tutti i provider di risorse disponibili e registrati nella sottoscrizione, è possibile usare PowerShell.

    `Get-AzResourceProvider`

    È possibile anche cercare tutti i cmdlet di PowerShell disponibili per gestire i provider di risorse.

    Nelle sezioni **Notacts** vengono elencate tutte le azioni limitate per un particolare ruolo di Azure. In questo esempio non è vietata alcuna azione.
    
    In **AssignableScopes** vengono elencati gli ID di sottoscrizione. Verificare che il ruolo di Azure contenga l'ID sottoscrizione esplicito in cui viene usato. Se non viene specificato l'ID di sottoscrizione corretto, non è possibile importare il ruolo nella sottoscrizione.

    Modificare il file tenendo presenti le considerazioni precedenti.

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importare di nuovo il ruolo personalizzato di Azure nell'ambiente.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Il ruolo dovrebbe essere ora visualizzato nell'elenco dei ruoli nel pannello **Controllo di accesso**.

![Visualizzare i ruoli di Azure](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Per altre informazioni, passare a [Custom roles](../role-based-access-control/custom-roles.md) (Ruoli personalizzati).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Esempio di output per la creazione di un ruolo personalizzato mediante PowerShell

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>Aggiungere utenti al ruolo personalizzato

Si concede l'accesso dalla risorsa, dal gruppo di risorse o dalla sottoscrizione che costituisce l'ambito dell'assegnazione di ruolo. Quando si concede l'accesso, tenere presente che l'accesso concesso al nodo padre viene ereditato dal figlio. Per altre informazioni, vedere [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md).

1. Passare a **controllo di accesso (IAM)**. Fare clic su **+Aggiungi** nel pannello Controllo di accesso.

    ![Aggiunta dell'accesso al ruolo di Azure](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Selezionare il ruolo che si vuole assegnare, in questo caso **Amministratore dell'infrastruttura StorSimple**.

3. Selezionare l'utente, il gruppo o l'applicazione nella directory a cui si vuole concedere l'accesso. È possibile cercare nella directory usando nomi visualizzati, indirizzi di posta elettronica e identificatori di oggetto.

4. Scegliere **Salva** per creare l'assegnazione.

    ![Aggiungere autorizzazioni al ruolo di Azure](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

La notifica **Aggiunta dell'utente in corso** tiene traccia dello stato di avanzamento del processo di aggiunta. Al termine del processo, l'elenco degli utenti nel pannello Controllo degli accessi viene aggiornato.

## <a name="view-permissions-for-the-custom-role"></a>Visualizzare le autorizzazioni per il ruolo personalizzato

Dopo aver creato il ruolo, è possibile visualizzare le autorizzazioni associate al ruolo nel portale di Azure.

1. Per visualizzare le autorizzazioni associate a questo ruolo, passare a **controllo di accesso (IAM) ruoli di > > amministratore dell'infrastruttura StorSimple**. Viene visualizzato l'elenco degli utenti in questo ruolo.

2. Selezionare un utente Amministratore dell'infrastruttura StorSimple e fare clic su **Autorizzazioni**.

    ![Visualizzare le autorizzazioni per il ruolo Amministratore dell'infrastruttura StorSimple](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Vengono visualizzate le autorizzazioni associate al ruolo.

    ![Visualizzare gli utenti nel ruolo Amministratore dell'infrastruttura StorSimple](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [Assegnare ruoli personalizzati per utenti interni ed esterni](../role-based-access-control/role-assignments-external-users.md).
