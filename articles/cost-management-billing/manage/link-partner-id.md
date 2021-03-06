---
title: Collegare un account Azure a un ID partner
description: Tener traccia degli engagement con i clienti di Azure collegando un ID partner all'account utente usato per gestire le risorse del cliente.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 10/05/2020
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.openlocfilehash: a214e91307308e191ce92b6461c1454d2cc7dd2b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370479"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Collegare un ID partner agli account Azure

I partner Microsoft offrono servizi che aiutano i clienti a raggiungere i loro obiettivi in termini di business e mission usando i prodotti Microsoft. Per occuparsi per conto del cliente delle attività di gestione, configurazione e supporto dei servizi di Azure, gli utenti del partner dovranno avere accesso all'ambiente del cliente. Usando Partner Admin Link (PAL), i partner possono associare il loro ID rete alle credenziali usate per la distribuzione di servizi.

PAL consente a Microsoft di identificare e riconoscere i partner che contribuiscono al successo dei clienti di Azure. Microsoft può attribuire all'organizzazione un determinato livello di influenza e proventi per l'utilizzo di Azure a seconda delle autorizzazioni dell'account (ruolo di Azure) e dell'ambito (sottoscrizione, gruppo di risorse, risorsa).

## <a name="get-access-from-your-customer"></a>Ottenere l'accesso dal cliente

Prima di collegare l'ID partner, il cliente deve consentire l'accesso alle risorse di Azure tramite una delle opzioni seguenti:

- **Utente guest**: il cliente può aggiungere il partner come utente guest e assegnare tutti i ruoli di Azure. Per altre informazioni, vedere [Aggiungere utenti guest da un'altra directory](../../active-directory/external-identities/what-is-b2b.md).

- **Account directory**: il cliente può creare nella propria directory un account utente per il partner e assegnare qualsiasi ruolo di Azure.

- **Entità servizio**: il cliente può aggiungere nella propria directory un'app o uno script dell'organizzazione del partner e assegnare qualsiasi ruolo di Azure. L'identità dell'app o dello script viene definita entità servizio.

- **Azure Lighthouse**: il cliente può delegare una sottoscrizione (o un gruppo di risorse) in modo che gli utenti possano usarla all'interno del tenant. Per altre informazioni, vedere [Gestione delle risorse delegata di Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

## <a name="link-to-a-partner-id"></a>Collegamento a un ID partner

Quando si ha accesso alle risorse del cliente, usare il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per collegare l'ID Microsoft Partner Network (ID MPN) all'ID utente o all'entità servizio. Collegare l'ID partner in ogni tenant del cliente.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Usare il portale di Azure per creare il collegamento a un nuovo ID partner

1. Passare a [Collega a un ID partner](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) nel portale di Azure.

2. Accedere al portale di Azure.

3. Immettere l'ID partner Microsoft. L'ID partner è l'ID [Microsoft Partner Network](https://partner.microsoft.com/) dell'organizzazione. Assicurarsi di usare l'**ID MPN associato** visualizzato nel profilo del partner.

   ![Screenshot che mostra Collega a un ID partner](./media/link-partner-id/link-partner-id01.png)

4. Per collegare un ID partner per un altro cliente, cambiare la directory. In **Cambia directory** selezionare la directory.

   ![Screenshot che mostra Cambia directory](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Usare PowerShell per creare il collegamento a un nuovo ID partner

1. Installare il modulo [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) di PowerShell.

2. Accedere al tenant del cliente con l'account utente o l'entità servizio. Per altre informazioni, vedere [Sign in with PowerShell](/powershell/azure/authenticate-azureps) (Accedere con PowerShell).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Creare il collegamento al nuovo ID partner. L'ID partner è l'ID [Microsoft Partner Network](https://partner.microsoft.com/) dell'organizzazione. Assicurarsi di usare l'**ID MPN associato** visualizzato nel profilo del partner.


    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Ottenere l'ID partner collegato
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Aggiornare l'ID partner collegato
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Eliminare l'ID partner collegato
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Usare l'interfaccia della riga di comando di Azure per creare il collegamento a un nuovo ID partner
1. Installare l'estensione dell'interfaccia della riga di comando di Azure.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Accedere al tenant del cliente con l'account utente o l'entità servizio. Per altre informazioni, vedere [Accedere tramite l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Creare il collegamento al nuovo ID partner. L'ID partner è l'ID [Microsoft Partner Network](https://partner.microsoft.com/) dell'organizzazione.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Ottenere l'ID partner collegato
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Aggiornare l'ID partner collegato
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Eliminare l'ID partner collegato
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Passaggi successivi

Partecipare alla discussione [Community Partner Microsoft](https://aka.ms/PALdiscussion) per ricevere aggiornamenti o inviare commenti.

## <a name="frequently-asked-questions"></a>Domande frequenti

**Chi può collegare l'ID partner?**

Tutti gli utenti dell'organizzazione partner che gestiscono le risorse di Azure del cliente possono collegare l'ID partner all'account.

**Un ID partner può essere modificato dopo essere stato collegato?**

Sì. Un ID partner collegato può essere modificato, aggiunto o rimosso.

**Cosa accade se un utente ha un account in più di un tenant del cliente?**

Il collegamento tra l'ID partner e l'account viene eseguito per ogni tenant del cliente. Collegare l'ID partner in ogni tenant del cliente.

Se tuttavia le risorse dei clienti vengono gestite tramite Azure Lighthouse, è consigliabile creare il collegamento nel tenant del provider di servizi, usando un account che abbia accesso a tali risorse. Per altre informazioni, vedere [Collegare l'ID partner per verificare l'impatto sulle risorse delegate](../../lighthouse/how-to/partner-earned-credit.md).

**Altri partner o clienti possono modificare o rimuovere il collegamento all'ID partner?**

Il collegamento è associato a livello di account utente. Solo il titolare dell'account può modificare o rimuovere il collegamento all'ID partner. Il cliente e altri partner non possono modificare il collegamento all'ID partner.

**Quale ID MPN è necessario usare se l'azienda ne ha più di uno?**

Assicurarsi di usare l'**ID MPN associato** visualizzato nel profilo partner.

**Dove è possibile trovare report sui ricavi influenzati per l'ID partner collegato?**

I report sulle prestazioni dei prodotti cloud sono disponibili per i partner nel [dashboard di informazioni dettagliate](https://partner.microsoft.com/membership/reports/myinsights) del Centro per i partner. È necessario selezionare il collegamento amministratore partner come tipo di associazione partner.

**Perché non è possibile visualizzare un cliente nei report?**

Il cliente non viene visualizzato nei report per i motivi seguenti

1. L'account utente collegato non ha [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md) per nessuna sottoscrizione o risorsa di Azure del cliente.

2. La sottoscrizione di Azure in cui l'utente ha [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md) non include dati di utilizzo.

**Il collegamento dell'ID partner funziona con Azure Stack?**

Sì, è possibile collegare l'ID partner per Azure Stack.

**Come si collega il proprio ID partner se l'azienda usa [Azure Lighthouse](../../lighthouse/overview.md) per accedere alle risorse dei clienti?**

Affinché le attività del Faro di Azure vengano riconosciute, è necessario associare l'ID MPN ad almeno un account utente che ha accesso a ogni sottoscrizione caricata. Si noti che sarà necessario eseguire questa associazione nel tenant del provider di servizi invece che nel tenant di ogni cliente. Per semplicità, è consigliabile creare un account di entità servizio nel tenant, associarlo all'ID MPN, quindi concedergli l'accesso a tutti i clienti di cui si esegue l'onboarding con un [ruolo predefinito di Azure idoneo per ricevere il credito ottenuto dai partner](/partner-center/azure-roles-perms-pec). Per altre informazioni, vedere [Collegare l'ID partner per verificare l'impatto sulle risorse delegate](../../lighthouse/how-to/partner-earned-credit.md).

**Come si spiega il collegamento amministrazione partner al cliente?**

Il collegamento amministrazione partner consente a Microsoft di identificare e riconoscere i partner che aiutano i clienti a raggiungere obiettivi aziendali e realizzare valore sul cloud. I clienti devono prima di tutto fornire a un partner l'accesso alla propria risorsa di Azure. Dopo la concessione dell'accesso, verrà associato l'ID MPN (Microsoft Partner Network) del partner. Questa associazione consente a Microsoft di ottenere informazioni sull'ecosistema di provider di servizi IT e di perfezionare gli strumenti e i programmi necessari per supportare al meglio i clienti comuni.

**Quali dati vengono raccolti dal collegamento amministrazione partner?**

L'associazione del collegamento amministrazione partner alle credenziali esistenti non fornisce alcun nuovo dato del cliente a Microsoft. Fornisce semplicemente a Microsoft i dati di telemetria per cui un partner è attivamente coinvolto nell'ambiente di Azure di un cliente. Microsoft può attribuire un determinato livello di influenza e proventi per l'utilizzo di Azure dall'ambiente del cliente all'organizzazione del partner in base alle autorizzazioni dell'account (ruolo di Azure) e all'ambito (gruppo di gestione, sottoscrizione, gruppo di risorse, risorsa) fornito al partner dal cliente. 

**Questo approccio influisce sulla sicurezza dell'ambiente di Azure di un cliente?**

L'associazione del collegamento amministrazione partner aggiunge solo l'ID MPN del partner alle credenziali già sottoposte a provisioning e non modifica alcuna autorizzazione (ruolo di Azure) né fornisce dati aggiuntivi sui servizi di Azure al partner o a Microsoft.