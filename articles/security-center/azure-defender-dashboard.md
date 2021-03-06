---
title: Dashboard di Azure Defender e relative funzionalità
description: Informazioni sulle funzionalità di Azure Defender dashboard.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 16379380fc35bb2355c496dc857e9de3b41347f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102096905"
---
# <a name="the-azure-defender-dashboard"></a>Il dashboard di Azure Defender

Il dashboard di Azure Defender fornisce:

- Visibilità della copertura di Azure Defender nei diversi tipi di risorse
- Collegamenti per configurare le funzionalità di protezione avanzata dalle minacce
- Stato di onboarding e installazione dell'agente
- Avvisi di rilevamento minacce di Azure Defender 

Per accedere al dashboard di Azure Defender, selezionare **Azure Defender** dalla sezione sicurezza cloud del menu del Centro sicurezza.

## <a name="whats-shown-on-the-dashboard"></a>Cosa viene visualizzato nel dashboard?

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Esempio del dashboard di Azure Defender" lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

Il dashboard include le sezioni seguenti:

1. **Azure Defender coverage** : qui è possibile visualizzare i tipi di risorse presenti nella sottoscrizione e idonei per la protezione da Azure Defender. Laddove pertinente, sarà possibile anche eseguire l'aggiornamento. Se si desidera aggiornare tutte le possibili risorse idonee, selezionare **Aggiorna tutto**.

2. **Avvisi di sicurezza** : quando Azure Defender rileva una minaccia in qualsiasi area dell'ambiente, viene generato un avviso. Questi avvisi descrivono i dettagli sulle risorse interessate, le procedure di correzione consigliate e, in alcuni casi, un'opzione per attivare un'app per la logica in risposta. Selezionando un punto qualsiasi del grafico si apre la **pagina avvisi di sicurezza**.

3. **Protezione avanzata** : Azure Defender include molte funzionalità avanzate di protezione dalle minacce per macchine virtuali, database SQL, contenitori, applicazioni Web, rete e altro ancora. In questa sezione relativa alla protezione avanzata è possibile visualizzare lo stato delle risorse nelle sottoscrizioni selezionate per ognuna di queste protezioni. Selezionare uno di essi per passare direttamente all'area di configurazione per il tipo di protezione.

4. **Insights** : questo riquadro in sequenza di notizie, letture consigliate e avvisi con priorità alta offre informazioni dettagliate sul centro sicurezza per la pressione di argomenti di sicurezza rilevanti per l'utente e la sottoscrizione. Che si tratti di un elenco di CVEs di gravità elevato individuate sulle VM tramite uno strumento di analisi delle vulnerabilità o di un nuovo post di Blog di un membro del team del Centro sicurezza, questo è disponibile nel riquadro Insights del **dashboard di Azure Defender**.




## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato il dashboard di Azure Defender. 

Per altre informazioni su Azure Defender, vedere [Introduzione ad Azure Defender](azure-defender.md)

> [!div class="nextstepaction"]
> [Abilitare Azure Defender](enable-azure-defender.md)