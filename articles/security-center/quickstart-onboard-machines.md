---
title: Connettere macchine virtuali non di Azure al Centro sicurezza di Azure
description: Informazioni su come connettere macchine virtuali non di Azure al Centro sicurezza di Azure
author: memildin
ms.author: memildin
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: non-azure-machines
ms.openlocfilehash: 68fcf8a8feb046fca2c26041d92264dd8b3a638e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103465498"
---
# <a name="connect-your-non-azure-machines-to-security-center"></a>Connettere macchine virtuali non di Azure al Centro sicurezza

Il Centro sicurezza può monitorare la postura di sicurezza dei computer non di Azure, ma è prima necessario connetterli ad Azure.

È possibile connettere i computer non di Azure in uno dei modi seguenti:

- Uso di server con abilitazione di Azure Arc (**scelta consigliata**)
- Dalle pagine del Centro sicurezza nel portale di Azure (**Attività iniziali** e **Inventario**)

Le relative procedure sono descritte in questa pagina.

::: zone pivot="azure-arc"

## <a name="add-non-azure-machines-with-azure-arc"></a>Aggiungere computer non Azure con Azure Arc

I server con abilitazione di Azure Arc rappresentano la scelta consigliata per aggiungere computer non di Azure al Centro sicurezza di Azure.

Un computer con i server con abilitazione di Azure Arc diventa una risorsa di Azure e viene visualizzato nel Centro sicurezza con le stesse raccomandazioni di altre risorse di Azure.

Inoltre, i server con abilitazione di Azure Arc offrono funzionalità avanzate, come l'opzione per abilitare i criteri di configurazione guest, distribuire l'agente di Log Analytics come estensione, semplificare la distribuzione con altri servizi di Azure e altro ancora. Per una panoramica dei vantaggi, vedere [Scenari supportati](../azure-arc/servers/overview.md#supported-scenarios).

Altre informazioni sui [server con abilitazione di Azure Arc](../azure-arc/servers/overview.md).

**Per distribuire Azure Arc:**

- Per un computer, seguire le istruzioni riportate nella [Guida introduttiva: connettere macchine ibride con i server abilitati per Azure Arc](../azure-arc/servers/learn/quick-enable-hybrid-vm.md).
- Per connettere più computer su larga scala ai server con abilitazione di Arc, vedere [Connettere macchine virtuali ibride ad Azure su larga scala](../azure-arc/servers/onboard-service-principal.md)

> [!TIP]
> Se si esegue l'onboarding di computer in esecuzione in AWS, il connettore del Centro sicurezza per AWS gestisce in modo trasparente e automatico la distribuzione di Azure Arc. Per altre informazioni, vedere [Connettere gli account AWS a Centro sicurezza di Azure](quickstart-onboard-aws.md).

::: zone-end

::: zone pivot="azure-portal"

## <a name="add-non-azure-machines-from-the-azure-portal"></a>Aggiungere computer non di Azure dal portale di Azure

1. Dal menu del Centro sicurezza aprire la pagina **Attività iniziali**.
1. Selezionare la scheda **Introduzione**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Scheda Attività iniziali della pagina Attività iniziali" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. In **Aggiungi server non Azure** selezionare **Configura**.

    > [!TIP]
    > È anche possibile aprire Aggiungi computer tramite il pulsante **Aggiungi server non di Azure** della pagina **Inventario**.
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="Aggiunta di computer non Azure dalla pagina dell'inventario degli asset":::

    Viene visualizzato un elenco delle aree di lavoro di Log Analytics. L'elenco include, se applicabile, l'area di lavoro predefinita creata dal Centro sicurezza quando è stato abilitato il provisioning automatico. Selezionare l'area di lavoro che si desidera usare.

    È possibile aggiungere computer a un'area di lavoro esistente o creare una nuova area di lavoro.

1. Facoltativamente, per creare una nuova area di lavoro, selezionare **Crea una nuova area di lavoro**.

1. Nell'elenco di aree di lavoro selezionare **Aggiungi server** per l'area di lavoro pertinente.
    Viene visualizzata la pagina **Gestione agenti**.

    Da qui scegliere la procedura appropriata tra quelle riportate di seguito, in base al tipo di computer di cui eseguire l'onboarding:

    - [Caricare le VM dell'hub Azure Stack](#onboard-your-azure-stack-hub-vms)
    - [Eseguire l'onboarding di computer Linux](#onboard-your-linux-machines)
    - [Eseguire l'onboarding di computer Windows](#onboard-your-windows-machines)

### <a name="onboard-your-azure-stack-hub-vms"></a>Caricare le VM dell'hub Azure Stack

Per aggiungere Azure Stack macchine virtuali dell'hub, sono necessarie le informazioni nella pagina di **gestione degli agenti** e per configurare l'estensione di macchina virtuale di **monitoraggio, aggiornamento e gestione della configurazione di Azure** nelle macchine virtuali in esecuzione nell'istanza dell'hub Azure stack.

1. Nella pagina **Gestione agenti** copiare i valori di **ID area di lavoro** e **Chiave primaria** nel Blocco note.
1. Accedere al portale di **Azure stack Hub** e aprire la pagina **macchine virtuali** .
1. Selezionare la macchina virtuale da proteggere con il Centro sicurezza.
    >[!TIP]
    > Per informazioni su come creare una macchina virtuale nell'hub Azure Stack, vedere [questa Guida introduttiva per le macchine virtuali Windows](/azure-stack/user/azure-stack-quick-windows-portal) o [questa Guida introduttiva per le macchine virtuali Linux](/azure-stack/user/azure-stack-quick-linux-portal).
1. Selezionare **Estensioni**. Viene visualizzato l'elenco delle estensioni macchina virtuale installate in questa macchina virtuale.
1. Selezionare la scheda **Aggiungi**. Il menu **Nuova risorsa** contiene un elenco delle estensioni macchina virtuale disponibili.
1. Selezionare l'estensione **Azure Monitor, Update and Configuration Management** (Monitoraggio di Azure, Gestione aggiornamenti e configurazione) e fare clic su **Crea**. Viene visualizzata la pagina di configurazione **Installa estensione**.
    >[!NOTE]
    > Se il monitoraggio di Azure, l'estensione di **Gestione aggiornamenti e configurazione** non è elencato nel Marketplace, contattare l'operatore Azure stack hub per renderlo disponibile.
1. Nella pagina di configurazione **Installa estensione** incollare i valori di **ID area di lavoro** e **Chiave dell'area di lavoro (chiave primaria)** copiati nel Blocco note nella procedura precedente.
1. Dopo aver completato la configurazione, selezionare **OK**. Lo stato dell'estensione viene visualizzato come **Provisioning completato**. Può essere necessaria fino a un'ora prima che la macchina virtuale venga visualizzata nel Centro sicurezza.

### <a name="onboard-your-linux-machines"></a>Eseguire l'onboarding di computer Linux

Per aggiungere computer Linux, è necessario il comando WGET della pagina **Gestione agenti**.

1. Nella pagina **Gestione agenti** copiare il comando **WGET** nel Blocco note. Salvare questo file in un percorso accessibile dal computer Linux.
1. Nel computer Linux aprire il file con il comando WGET. Selezionare l'intero contenuto, quindi copiarlo e incollarlo in una console di terminale.
1. Al termine dell'installazione, è possibile verificare che *omsagent* sia installato eseguendo il comando *pgrep*. Il comando restituirà il PID *omsagent*.
    I log per l'agente sono reperibili in: */var/opt/microsoft/omsagent/\<workspace id>/log/* . Possono essere necessari fino a 30 minuti prima che il computer Linux venga visualizzato nel Centro sicurezza.

### <a name="onboard-your-windows-machines"></a>Eseguire l'onboarding di computer Windows

Per aggiungere computer Windows, è necessario avere le informazioni riportate nella pagina **Gestione agenti** e scaricare il file dell'agente appropriato (32/64 bit).
1. Selezionare il collegamento **Scarica agente Windows** applicabile al tipo di processore del computer per scaricare il file di installazione.
1. Nella pagina **Gestione agenti** copiare i valori di **ID area di lavoro** e **Chiave primaria** nel Blocco note.
1. Copiare nel computer di destinazione il file di configurazione scaricato ed eseguirlo.
1. Seguire l'installazione guidata (**Avanti**, **Accetto**, **Avanti**, **Avanti**).
    1. Nella pagina **Azure Log Analytics** incollare i valori di **ID area di lavoro** e **Chiave dell'area di lavoro (chiave primaria)** copiati nel Blocco note.
    1. Se il computer deve fare riferimento a un'area di lavoro Log Analytics nel cloud di Azure per enti pubblici, selezionare **Azure US Government** nell'elenco a discesa **Cloud di Azure**.
    1. Se il computer deve comunicare tramite un server proxy con il servizio Log Analytics, scegliere **Avanzate** e specificare l'URL e il numero di porta del server proxy.
    1. Dopo aver immesso tutte le impostazioni di configurazione, selezionare **Avanti**.
    1. Nella pagina **Pronto per l'installazione** esaminare le impostazioni da applicare e selezionare **Installa**.
    1. Nella pagina **Configurazione completata** fare clic su **Fine**.

Al termine, verrà visualizzato **Microsoft Monitoring Agent** nel **Pannello di controllo**. È possibile rivedere la configurazione e verificare che l'agente sia connesso.

Per altre informazioni sull'installazione e la configurazione dell'agente, vedere [Connettere computer Windows](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard).

::: zone-end

## <a name="verifying"></a>Verifica

Congratulazioni! A questo punto è possibile visualizzare i computer Azure e non Azure in un'unica posizione. Aprire la pagina inventario dell'[inventario degli asset](asset-inventory.md) e filtrare in base ai tipi di risorsa appropriati. Queste icone distinguono i tipi:

  ![Icona di Azure Spring Cloud per il computer non Azure](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Computer non Azure

  ![Icona di Azure Spring Cloud per il computer Azure](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Macchina virtuale di Azure

  ![Icona del Centro sicurezza di Azure per il server Azure Arc](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) Server con abilitazione di Azure Arc

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha illustrato come aggiungere computer non Azure al Centro sicurezza di Azure. Per monitorarne lo stato, usare gli strumenti di inventario come illustrato nella pagina seguente:

- [Esplorare e gestire le risorse con l'inventario degli asset](asset-inventory.md)