---
title: Pubblicare un'offerta di servizio gestito in Azure Marketplace
description: Informazioni su come pubblicare un'offerta di servizio gestito che carica i clienti nel Faro di Azure.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: f59d9708925b52bcf9432663fe6d3137a54b37ad
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107449"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Pubblicare un'offerta di servizio gestito in Azure Marketplace

In questo articolo si apprenderà come pubblicare un'offerta di servizio gestito pubblica o privata in [Azure Marketplace](https://azuremarketplace.microsoft.com) usando il programma [Commercial Marketplace](../../marketplace/overview.md) nel centro per i partner. I clienti che acquistano l'offerta delegheranno le sottoscrizioni o i gruppi di risorse, consentendo di gestirli tramite [Azure Lighthouse](../overview.md).

## <a name="publishing-requirements"></a>Requisiti per la pubblicazione

Per creare e pubblicare le offerte, è necessario disporre di un [account valido nel centro](../../marketplace/create-account.md) per i partner. Se non si dispone già di un account, il [processo di iscrizione](https://aka.ms/joinmarketplace) consentirà di creare un account nel centro per i partner e di iscriversi al programma Commercial Marketplace.

In base ai [requisiti di certificazione dell'offerta del servizio gestito](/legal/marketplace/certification-policies#700-managed-services), è necessario disporre di un livello di competenza della [piattaforma Cloud Silver o Gold](/partner-center/learn-about-competencies) oppure essere un [msp esperto di Azure](https://partner.microsoft.com/membership/azure-expert-msp) per pubblicare un'offerta di servizio gestito. È anche necessario [immettere una destinazione lead che creerà un record nel sistema CRM](../../marketplace/plan-managed-service-offer.md#customer-leads) ogni volta che un cliente distribuisce l'offerta.

Se non si vuole pubblicare un'offerta in Azure Marketplace o non soddisfare tutti i requisiti, è possibile caricare i clienti manualmente usando modelli di Azure Resource Manager. Per altre informazioni, vedere [onboarding an Customer to Azure Lighthouse](onboard-customer.md).

La tabella seguente consente di determinare se eseguire l'onboarding dei clienti pubblicando un'offerta del servizio gestito o utilizzando Azure Resource Manager modelli.

|**Considerazioni**  |**Offerta di servizio gestito**  |**Modelli di Gestione risorse di Azure**  |
|---------|---------|---------|
|Richiede l' [account del centro](../../marketplace/create-account.md) per i partner   |Sì         |No        |
|Richiede il [livello di competenza Silver o Gold per la piattaforma cloud](/partner-center/learn-about-competencies) o [Azure Expert msp](https://partner.microsoft.com/membership/azure-expert-msp)      |Sì         |No         |
|Disponibile per i nuovi clienti tramite Azure Marketplace     |Sì     |No       |
|È possibile limitare l'offerta a clienti specifici     |Sì (solo con offerte private, che non possono essere usate con le sottoscrizioni stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP))         |Sì         |
|Richiede l'accettazione del cliente in portale di Azure     |Sì     |No   |
|Può usare l'automazione per caricare più sottoscrizioni, gruppi di risorse o clienti |No     |Sì    |
|Accesso immediato ai nuovi ruoli predefiniti e alle funzionalità di Azure Lighthouse     |Non sempre (disponibile a livello generale dopo un certo ritardo)         |Sì         |
|I clienti possono esaminare e accettare le offerte aggiornate nella portale di Azure | Sì | No |

> [!NOTE]
> Le offerte del servizio gestito potrebbero non essere disponibili in Azure per enti pubblici e in altri cloud nazionali.

## <a name="create-your-offer"></a>Creare l'offerta

Per istruzioni dettagliate su come creare l'offerta, incluse tutte le informazioni e gli asset che è necessario fornire, vedere [creare un'offerta di servizio gestito](../../marketplace/create-managed-service-offer.md).

Per informazioni sul processo di pubblicazione generale, vedere la [documentazione sul Marketplace commerciale](../../marketplace/overview.md). È anche necessario esaminare i [criteri di certificazione del Marketplace commerciale](/legal/marketplace/certification-policies), in particolare la sezione [Servizi gestiti](/legal/marketplace/certification-policies#700-managed-services).

Una volta che un cliente ha aggiunto l'offerta, sarà in grado di delegare una o più sottoscrizioni o gruppi di risorse, che verranno quindi caricati [in Azure Lighthouse](#the-customer-onboarding-process).

> [!IMPORTANT]
> Ogni piano in un'offerta di servizio gestito include una sezione dei **Dettagli del manifesto** , in cui si definiscono le entità Azure Active Directory (Azure ad) nel tenant che avranno accesso ai gruppi di risorse e/o alle sottoscrizioni delegate per i clienti che acquistano il piano. È importante tenere presente che qualsiasi gruppo, o utente o entità servizio, incluso avrà le stesse autorizzazioni per ogni cliente che acquista il piano. Per assegnare gruppi diversi per lavorare con ogni cliente, è possibile pubblicare un [piano privato](../../marketplace/private-offers.md) separato che è esclusivo per ogni cliente. Tenere presente che i piani privati non sono supportati con le sottoscrizioni stabilite tramite un rivenditore del programma Cloud Solution Provider (CSP).

## <a name="publish-your-offer"></a>Pubblicare l'offerta

Dopo aver completato tutte le sezioni, il passaggio successivo consiste nel pubblicare l'offerta in Azure Marketplace. Selezionare il pulsante **Pubblica** per attivare l'offerta. Altre informazioni su questo processo sono disponibili [qui](../../marketplace/review-publish-offer.md).

È possibile [pubblicare una versione aggiornata dell'offerta](../..//marketplace/partner-center-portal/update-existing-offer.md) in qualsiasi momento. Ad esempio, potrebbe essere necessario aggiungere una nuova definizione di ruolo a un'offerta pubblicata in precedenza. Quando si esegue questa operazione, i clienti che hanno già aggiunto l'offerta vedranno un'icona nella pagina [**Provider di servizi**](view-manage-service-providers.md) del portale di Azure che consente di sapere che è disponibile un aggiornamento. Ogni cliente sarà in grado di [esaminare le modifiche](view-manage-service-providers.md#update-service-provider-offers) e decidere se desiderano eseguire l'aggiornamento alla nuova versione. 

## <a name="the-customer-onboarding-process"></a>Il processo di onboarding del cliente

Dopo che un cliente ha aggiunto l'offerta, potrà [delegare una o più sottoscrizioni o gruppi di risorse specifici](view-manage-service-providers.md#delegate-resources), che verranno quindi caricati in Azure Lighthouse. Se un cliente ha accettato un'offerta, ma non ha ancora delegato alcuna risorsa, vedrà una nota nella parte superiore della sezione **Provider offers** (Offerte di provider) della pagina [**Provider di servizi**](view-manage-service-providers.md) del portale di Azure.

> [!IMPORTANT]
> La delega deve essere eseguita da un account non Guest nel tenant del cliente che ha un ruolo con l' `Microsoft.Authorization/roleAssignments/write` autorizzazione, ad esempio [owner](../../role-based-access-control/built-in-roles.md#owner), per la sottoscrizione da caricare (o che contiene i gruppi di risorse di cui è in corso l'onboarding). Per trovare gli utenti che possono delegare la sottoscrizione, un utente nel tenant del cliente può selezionare la sottoscrizione nella portale di Azure, aprire il **controllo di accesso (IAM)** e [visualizzare tutti gli utenti con il ruolo proprietario](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Quando il cliente delega una sottoscrizione (o uno o più gruppi di risorse all'interno di una sottoscrizione), il provider di risorse **Microsoft. ManagedServices** verrà registrato per tale sottoscrizione e gli utenti nel tenant potranno accedere alle risorse delegate in base alle autorizzazioni dell'offerta.

Se si pubblica una versione aggiornata dell'offerta, il cliente potrà [esaminare le modifiche apportate all'portale di Azure e accettare la nuova versione](view-manage-service-providers.md#update-service-provider-offers).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Marketplace commerciale](../../marketplace/overview.md).
- [Collegare l'ID partner](partner-earned-credit.md) per tenere traccia dell'effetto tra gli impegni dei clienti.
- Informazioni sulle [esperienze di gestione tra tenant](../concepts/cross-tenant-management-experience.md).
- [Visualizzare e gestire i clienti](view-manage-customers.md) passando a **Clienti personali** nel portale di Azure.
