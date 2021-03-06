---
title: Caricamento in blocco per aggiungere o creare membri di un gruppo - Azure Active Directory | Microsoft Docs
description: Aggiungere membri del gruppo in blocco nell'interfaccia di amministrazione di Azure Active Directory.
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22d39a2ee66f2c63612ad2cb3cf9ae61f1660de3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547747"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>Aggiunta di membri del gruppo in blocco in Azure Active Directory

Tramite il portale di Azure Active Directory (Azure AD), è possibile aggiungere un numero elevato di membri a un gruppo usando un file con valori delimitati da virgole (CSV) per l'importazione in blocco dei membri del gruppo.

## <a name="understand-the-csv-template"></a>Informazioni sul modello CSV

Scaricare e compilare il modello CSV di caricamento in blocco per aggiungere i membri di un gruppo di Azure AD in una sola volta. Il modello CSV potrebbe essere simile a questo esempio:

![Foglio di calcolo per il caricamento con callout che illustrano lo scopo e i valori di ogni riga e colonna](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>Struttura del modello CSV

Un modello CSV scaricato contiene le righe descritte di seguito:

- **Numero di versione**: nel file CSV di caricamento deve essere inclusa la prima riga contenente il numero di versione.
- **Intestazioni di colonna**: il formato delle intestazioni di colonna è &lt;*Nome elemento*&gt; [NomeProprietà] &lt;*Required o vuoto*&gt;. Ad esempio: `Member object ID or user principal name [memberObjectIdOrUpn] Required`. Alcune versioni precedenti del modello potrebbero presentare lievi variazioni. Per le modifiche apportate all'appartenenza al gruppo, è possibile scegliere quale identificatore usare, tra ID oggetto membro e nome dell'entità utente.
- **Riga di esempi**: nel modello è stata inclusa una riga di esempi di valori accettabili per ogni colonna. È necessario rimuovere la riga degli esempi e sostituirla con le proprie voci.

### <a name="additional-guidance"></a>Indicazioni aggiuntive

- Le prime due righe del modello di caricamento non devono essere rimosse o modificate. In caso contrario, il caricamento non può essere elaborato.
- Le colonne obbligatorie sono riportate per prime.
- Non è consigliabile aggiungere nuove colonne al modello. Eventuali colonne aggiuntive aggiunte saranno ignorate e non verranno elaborate.
- È consigliabile scaricare la versione più recente del modello CSV il più spesso possibile.
- Per caricare correttamente il file, aggiungere almeno due ID oggetto o UPN dell'utente.

## <a name="to-bulk-import-group-members"></a>Per importare in blocco i membri del gruppo

1. Accedere al [portale di Azure](https://portal.azure.com) con un account amministratore utenti nell'organizzazione. I proprietari del gruppo possono anche importare in blocco i membri dei gruppi di cui sono proprietari.
1. In Azure AD selezionare **Gruppi** > **Tutti i gruppi**.
1. Aprire il gruppo a cui devono essere aggiunti i membri e quindi selezionare **Membri**.
1. Nella pagina **Membri** selezionare **Importa membri**.
1. Nella pagina **Importa in blocco i membri del gruppo** selezionare **Scarica** per scaricare il modello di file CSV con le proprietà obbligatorie per i membri del gruppo.

    ![Il comando Importa membri è disponibile nella pagina del profilo del gruppo](./media/groups-bulk-import-members/import-panel.png)

1. Aprire il file CSV e aggiungere una riga per ogni membro del gruppo da importare nel gruppo (i valori obbligatori sono **ID oggetto membro** o **Nome dell'entità utente**). Salvare quindi il file.

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="Il file CSV contiene i nomi e gli ID dei membri da importare":::

1. Nella pagina **Importa in blocco i membri del gruppo** individuare il file in **Caricare il file CSV**. Quando si seleziona il file, viene avviata la convalida del file CSV.
1. Dopo la convalida del contenuto del file, nella pagina di importazione in blocco viene visualizzato il messaggio **Il file è stato caricato**. Se sono presenti errori, è necessario correggerli prima di poter inviare il processo.
1. Dopo che il file ha superato la convalida, selezionare **Invia** per avviare l'operazione di Azure per l'importazione in blocco dei membri nel gruppo.
1. Al termine dell'operazione di importazione, verrà visualizzata una notifica per indicare la corretta esecuzione dell'operazione in blocco.

## <a name="check-import-status"></a>Controllare lo stato dell'importazione

È possibile visualizzare lo stato di tutte le richieste in blocco in sospeso nella pagina **Risultati dell'operazione in blocco**.

[![Verificare lo stato nella pagina Risultati operazioni bulk.](./media/groups-bulk-import-members/bulk-center.png)](./media/groups-bulk-import-members/bulk-center.png#lightbox)

Per informazioni dettagliate su ogni voce all'interno dell'operazione in blocco, selezionare i valori nelle colonne **N. operazioni riuscite**, **N. errori** o **Richieste totali**. Se si verificano errori, verranno elencati i motivi dell'errore.

## <a name="bulk-import-service-limits"></a>Limiti del servizio di importazione in blocco

Ogni attività in blocco per l'importazione di un elenco di membri del gruppo può essere eseguita per un massimo di un'ora. Questo consente l'importazione di un elenco di al massimo 40.000 membri.

## <a name="next-steps"></a>Passaggi successivi

- [Rimozione in blocco di membri del gruppo](groups-bulk-remove-members.md)
- [Scaricare i membri di un gruppo](groups-bulk-download-members.md)
- [Scaricare un elenco di tutti i gruppi](groups-bulk-download.md)
