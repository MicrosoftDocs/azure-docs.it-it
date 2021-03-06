---
title: Rilevare le minacce con le regole di analisi predefinite in Sentinel di Azure | Microsoft Docs
description: Informazioni su come usare le regole di rilevamento delle minacce predefinite, basate su modelli predefiniti, che inviano notifiche quando si verifica un evento sospetto.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2021
ms.author: yelevin
ms.openlocfilehash: ba757474ab24006b7b8b514bda085522bd353ea8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312672"
---
# <a name="tutorial-detect-threats-out-of-the-box"></a>Esercitazione: Rilevamento predefinito delle minacce

Dopo aver [connesso le origini dati](quickstart-onboard.md) ad Azure Sentinel, sarà necessario ricevere una notifica quando si verifica un evento sospetto. Questo è il motivo per cui Azure Sentinel fornisce modelli predefiniti predefiniti che consentono di creare regole di rilevamento delle minacce. Questi modelli sono stati progettati dal team di esperti di sicurezza e analisti di Microsoft in base a minacce note, vettori di attacco comuni e catene di escalation delle attività sospette. Le regole create da questi modelli verranno ricercate automaticamente nell'ambiente in uso per qualsiasi attività che abbia un aspetto sospetto. Molti dei modelli possono essere personalizzati per cercare attività o filtrarle in base alle esigenze. Gli avvisi generati da queste regole creeranno eventi imprevisti che è possibile assegnare ed esaminare nell'ambiente in uso.

Questa esercitazione consente di rilevare le minacce con Azure Sentinel:

> [!div class="checklist"]
> * Usare i rilevamenti di minacce predefiniti
> * Automatizzare le risposte alle minacce

## <a name="about-out-of-the-box-detections"></a>Informazioni sui rilevamenti predefiniti

Per visualizzare tutti i rilevamenti predefiniti, passare ad **Analisi** e quindi scegliere **Rule templates** (Modelli di regole). Questa scheda contiene tutte le regole predefinite di Azure Sentinel.

   :::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Usare i rilevamenti predefiniti per individuare le minacce con Azure Sentinel":::

Le sezioni seguenti descrivono i tipi di modelli predefiniti disponibili:

### <a name="microsoft-security"></a>Microsoft Security

I modelli di sicurezza Microsoft creano automaticamente gli eventi imprevisti di Sentinel di Azure dagli avvisi generati in altre soluzioni di sicurezza Microsoft, in tempo reale. È possibile utilizzare le regole di sicurezza Microsoft come modello per creare nuove regole con logica simile.

Per ulteriori informazioni sulle regole di sicurezza, vedere [creazione automatica degli eventi imprevisti da avvisi di sicurezza Microsoft](create-incidents-from-alerts.md).

### <a name="fusion"></a>Fusion

Basato sulla tecnologia Fusion, il rilevamento degli attacchi multifase avanzati in Sentinel di Azure usa algoritmi di Machine Learning scalabili che possono correlare molti avvisi ed eventi a bassa fedeltà tra più prodotti in eventi imprevisti ad alta fedeltà e interattivi. Fusion è abilitato per impostazione predefinita. Poiché la logica è nascosta e pertanto non è personalizzabile, è possibile creare solo una regola con questo modello.

> [!IMPORTANT]
> Alcuni dei rilevamenti nel modello di regola Fusion sono attualmente in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.
>
> Per vedere quali rilevamenti sono in anteprima, vedere [rilevamento di attacchi multifase avanzati in Sentinel di Azure](fusion.md).

### <a name="machine-learning-behavioral-analytics"></a>Analisi del comportamento basata su Machine Learning

Questi modelli sono basati su algoritmi di Microsoft Machine Learning proprietari, pertanto non è possibile vedere la logica interna del modo in cui funzionano e quando vengono eseguiti. Poiché la logica è nascosta e pertanto non è personalizzabile, è possibile creare solo una regola con ogni modello di questo tipo.

> [!IMPORTANT]
> - I modelli di regole di analisi del comportamento di Machine Learning sono attualmente in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.
>
> - Creando e abilitando le regole in base ai modelli di analisi del comportamento di ML, **si concede a Microsoft l'autorizzazione per copiare i dati inseriti all'esterno dell'area geografica dell'area di lavoro di Azure Sentinel** , se necessario per l'elaborazione da parte dei motori e dei modelli di machine learning.

### <a name="scheduled"></a>Scheduled

Le regole di analisi pianificate sono basate su query predefinite scritte da esperti di sicurezza Microsoft. È possibile visualizzare la logica di query e apportare modifiche. È possibile utilizzare il modello regole pianificate e personalizzare la logica di query e le impostazioni di pianificazione per creare nuove regole.

> [!TIP]
> Le opzioni di pianificazione delle regole includono la configurazione della regola da eseguire ogni numero di minuti, ore o giorni specificato, con l'orologio che inizia quando si Abilita la regola.
>
> Si consiglia di tenere presente quando si abilita una regola di analisi nuova o modificata per assicurarsi che le regole ottengano il nuovo stack di eventi imprevisti nel tempo. Ad esempio, potrebbe essere necessario eseguire una regola sincronizzata con il momento in cui gli analisti SOC iniziano la giornata lavorativa e abilitano quindi le regole.
>

## <a name="use-out-of-the-box-detections"></a>Usare i rilevamenti predefiniti

1. Per usare un modello predefinito, fare clic sul nome del modello e quindi fare clic sul pulsante **Crea regola** nel riquadro dettagli per creare una nuova regola attiva basata su tale modello. Ogni modello include un elenco di origini dati obbligatorie. Quando si apre il modello, le origini dati vengono verificate automaticamente per la disponibilità. Se si verifica un problema di disponibilità, è possibile che il pulsante **Crea regola** sia disabilitato o che venga visualizzato un avviso relativo a tale effetto.

    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Pannello di anteprima della regola di rilevamento":::

1. Facendo clic sul pulsante **Crea regola** si apre la creazione guidata regola in base al modello selezionato. Tutti i dettagli sono riempiti automaticamente e, con i modelli di **sicurezza** **pianificati** o Microsoft, è possibile personalizzare la logica e altre impostazioni delle regole in base alle esigenze specifiche. È possibile ripetere questo processo per creare regole aggiuntive basate sul modello predefinito. Al termine della procedura guidata per la creazione della regola, sarà stata completata la creazione di una regola basata sul modello. Le nuove regole verranno visualizzate nella scheda **regole attive** .

    Per altre informazioni su come personalizzare le regole nella creazione guidata regola, vedere [esercitazione: creare regole di analisi personalizzate per rilevare le minacce](tutorial-detect-threats-custom.md).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come iniziare a rilevare le minacce usando Azure Sentinel.

Per informazioni su come automatizzare le risposte alle minacce, [configurare le risposte automatiche alle minacce in Sentinel di Azure](tutorial-respond-threats-playbook.md).
