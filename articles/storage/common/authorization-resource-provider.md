---
title: Usare il provider di risorse di archiviazione di Azure per accedere alle risorse di gestione
description: Il provider di risorse di archiviazione di Azure è un servizio che fornisce l'accesso alle risorse di gestione per archiviazione di Azure. È possibile usare il provider di risorse di archiviazione di Azure per creare, aggiornare, gestire ed eliminare risorse quali account di archiviazione, endpoint privati e chiavi di accesso all'account.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcf3e9228c8e651efb8f97067f7ba9eead5959db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92789676"
---
# <a name="use-the-azure-storage-resource-provider-to-access-management-resources"></a>Usare il provider di risorse di archiviazione di Azure per accedere alle risorse di gestione

Azure Resource Manager è il servizio di distribuzione e gestione di Azure. Il provider di risorse di archiviazione di Azure è un servizio basato su Azure Resource Manager e che fornisce l'accesso alle risorse di gestione per archiviazione di Azure. È possibile usare il provider di risorse di archiviazione di Azure per creare, aggiornare, gestire ed eliminare risorse quali account di archiviazione, endpoint privati e chiavi di accesso all'account. Per ulteriori informazioni su Azure Resource Manager, vedere [Panoramica di Azure Resource Manager](../../azure-resource-manager/management/overview.md).

È possibile usare il provider di risorse di archiviazione di Azure per eseguire azioni, ad esempio la creazione o l'eliminazione di un account di archiviazione o l'ottenimento di un elenco di account di archiviazione in una sottoscrizione. Per autorizzare le richieste nel provider di risorse di archiviazione di Azure, usare Azure Active Directory (Azure AD). Questo articolo descrive come assegnare le autorizzazioni alle risorse di gestione e punta ad esempi che illustrano come effettuare richieste nel provider di risorse di archiviazione di Azure.

## <a name="management-resources-versus-data-resources"></a>Risorse di gestione rispetto alle risorse di dati

Microsoft fornisce due API REST per l'uso delle risorse di archiviazione di Azure. Queste API costituiscono la base di tutte le azioni che è possibile eseguire nell'archiviazione di Azure. L'API REST di archiviazione di Azure consente di usare i dati nell'account di archiviazione, inclusi i dati di BLOB, code, file e tabelle. L'API REST del provider di risorse di archiviazione di Azure consente di lavorare con l'account di archiviazione e le risorse correlate.

Una richiesta di lettura o scrittura di dati BLOB richiede autorizzazioni diverse rispetto a una richiesta che esegue un'operazione di gestione. Il controllo degli accessi in base al ruolo di Azure offre un controllo dettagliato sulle autorizzazioni per entrambi i tipi di risorse. Quando si assegna un ruolo di Azure a un'entità di sicurezza, verificare di avere compreso le autorizzazioni concesse all'entità di sicurezza. Per un riferimento dettagliato che descrive le azioni associate a ogni ruolo predefinito di Azure, vedere [ruoli predefiniti di Azure](../../role-based-access-control/built-in-roles.md).

Archiviazione di Azure supporta l'uso di Azure AD per autorizzare le richieste sull'archiviazione BLOB e di Accodamento. Per informazioni sui ruoli di Azure per le operazioni di dati BLOB e di Accodamento, vedere [autorizzare l'accesso a BLOB e code usando Active Directory](storage-auth-aad.md).

## <a name="assign-management-permissions-with-azure-role-based-access-control-azure-rbac"></a>Assegnare le autorizzazioni di gestione con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)

A ogni sottoscrizione di Azure è associato un Azure Active Directory che gestisce utenti, gruppi e applicazioni. Un utente, un gruppo o un'applicazione viene definito anche entità di sicurezza nel contesto della [piattaforma di identità Microsoft](../../active-directory/develop/index.yml). È possibile concedere l'accesso alle risorse in una sottoscrizione a un'entità di sicurezza definita nella Active Directory usando il controllo degli accessi in base al ruolo di Azure (RBAC di Azure).

Quando si assegna un ruolo di Azure a un'entità di sicurezza, viene indicato anche l'ambito in cui sono attive le autorizzazioni concesse dal ruolo. Per le operazioni di gestione, è possibile assegnare un ruolo a livello di sottoscrizione, gruppo di risorse o account di archiviazione. È possibile assegnare un ruolo di Azure a un'entità di sicurezza usando il [portale di Azure](https://portal.azure.com/), gli [strumenti dell'interfaccia](/cli/azure/install-classic-cli)della riga di comando di Azure, [PowerShell](/powershell/azure/)o l' [API REST del provider di risorse di archiviazione di Azure](/rest/api/storagerp).

Per altre informazioni, vedere informazioni [sul controllo degli accessi in base al ruolo di Azure (RBAC di Azure).](../../role-based-access-control/overview.md) e i ruoli di [amministratore della sottoscrizione classica, i ruoli di Azure e](../../role-based-access-control/rbac-and-directory-admin-roles.md)i ruoli di amministratore Azure ad.

### <a name="built-in-roles-for-management-operations"></a>Ruoli predefiniti per le operazioni di gestione

Azure fornisce ruoli predefiniti che concedono le autorizzazioni per le operazioni di gestione delle chiamate. Archiviazione di Azure fornisce anche ruoli predefiniti specifici per l'uso con il provider di risorse di archiviazione di Azure.

I ruoli predefiniti che concedono le autorizzazioni per chiamare le operazioni di gestione dell'archiviazione includono i ruoli descritti nella tabella seguente:

|    Ruolo di Azure    |    Descrizione    |    Include l'accesso alle chiavi dell'account?    |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|
| **Proprietario** | Può gestire tutte le risorse di archiviazione e l'accesso alle risorse.  | Sì, fornisce le autorizzazioni per visualizzare e rigenerare le chiavi dell'account di archiviazione. |
| **Collaboratore**  | Consente di gestire tutte le risorse di archiviazione, ma non di gestire l'accesso alle risorse. | Sì, fornisce le autorizzazioni per visualizzare e rigenerare le chiavi dell'account di archiviazione. |
| **Lettore** | Consente di visualizzare le informazioni sull'account di archiviazione, ma non di visualizzare le chiavi dell'account. | No. |
| **Collaboratore account di archiviazione** | Può gestire l'account di archiviazione, ottenere informazioni sui gruppi di risorse e le risorse della sottoscrizione, nonché creare e gestire distribuzioni di gruppi di risorse di sottoscrizione. | Sì, fornisce le autorizzazioni per visualizzare e rigenerare le chiavi dell'account di archiviazione. |
| **Amministratore accessi utente** | Può gestire l'accesso all'account di archiviazione.   | Sì, consente a un'entità di sicurezza di assegnare autorizzazioni a se stessi e ad altri utenti. |
| **Collaboratore macchine virtuali** | Consente di gestire le macchine virtuali, ma non l'account di archiviazione a cui sono connesse.   | Sì, fornisce le autorizzazioni per visualizzare e rigenerare le chiavi dell'account di archiviazione. |

La terza colonna della tabella indica se il ruolo predefinito supporta **Microsoft. storage/storageAccounts/listkeys/Action**. Questa azione concede le autorizzazioni per la lettura e la rigenerazione delle chiavi dell'account di archiviazione. Le autorizzazioni per accedere alle risorse di gestione archiviazione di Azure non includono anche le autorizzazioni per accedere ai dati. Tuttavia, se un utente ha accesso alle chiavi dell'account, può usare le chiavi dell'account per accedere ai dati di archiviazione di Azure tramite l'autorizzazione della chiave condivisa.

### <a name="custom-roles-for-management-operations"></a>Ruoli personalizzati per le operazioni di gestione

Azure supporta anche la definizione dei ruoli personalizzati di Azure per l'accesso alle risorse di gestione. Per altre informazioni sui ruoli personalizzati, vedere [ruoli personalizzati di Azure](../../role-based-access-control/custom-roles.md).

## <a name="code-samples"></a>Esempi di codice

Per esempi di codice che illustrano come autorizzare e chiamare le operazioni di gestione dalle librerie di gestione di archiviazione di Azure, vedere gli esempi seguenti:

- [.NET](https://github.com/Azure-Samples/storage-dotnet-resource-provider-getting-started)
- [Java](https://github.com/Azure-Samples/storage-java-manage-storage-accounts)
- [Node.js](https://github.com/Azure-Samples/storage-node-resource-provider-getting-started)
- [Python](https://github.com/Azure-Samples/storage-python-manage)

## <a name="azure-resource-manager-versus-classic-deployments"></a>Azure Resource Manager rispetto alle distribuzioni classiche

I modelli di distribuzione classica e Resource Manager rappresentano due diversi modi di distribuire e gestire le soluzioni Azure. Microsoft consiglia di usare il modello di distribuzione Azure Resource Manager quando si crea un nuovo account di archiviazione. Se possibile, Microsoft consiglia di ricreare gli account di archiviazione classici esistenti con il modello di Gestione risorse. Sebbene sia possibile creare un account di archiviazione usando il modello di distribuzione classica, il modello di distribuzione classica è meno flessibile e alla fine verrà deprecato.

Per ulteriori informazioni sui modelli di distribuzione di Azure, vedere [Gestione risorse e distribuzione classica](../../azure-resource-manager/management/deployment-models.md).

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Che cos'è il controllo degli accessi in base al ruolo di Azure?](../../role-based-access-control/overview.md)
- [Obiettivi di scalabilità per il provider di risorse di archiviazione di Azure](scalability-targets-resource-provider.md)