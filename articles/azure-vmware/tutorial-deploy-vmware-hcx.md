---
title: Esercitazione - Distribuire e configurare VMware HCX
description: Informazioni su come distribuire e configurare una soluzione VMware HCX per il cloud privato della soluzione Azure VMware.
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: 9ee451ce4be59c51299d66e4042ed344026100cf
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011001"
---
# <a name="deploy-and-configure-vmware-hcx"></a>Distribuire e configurare VMware HCX

Questo articolo illustra come distribuire e configurare l'istanza locale di VMware HCX Connector per il cloud privato della soluzione Azure VMware. Con VMware HCX è possibile eseguire la migrazione dei carichi di lavoro VMware alla soluzione Azure VMware e ad altri siti connessi tramite diversi tipi di migrazione. Poiché la soluzione Azure VMware distribuisce e configura HCX Cloud Manager, è necessario scaricare, attivare e configurare HCX Connector nel data center VMware locale.

VMware HCX Advanced Connector è predistribuito nella soluzione Azure VMware. Supporta fino a tre connessioni al sito (da locale al cloud o da cloud a cloud). Se servono più di tre connessioni al sito, inviare una [richiesta di supporto](https://portal.azure.com/#create/Microsoft.Support) per abilitare il componente aggiuntivo [VMware HCX Enterprise](https://cloud.vmware.com/community/2019/08/08/introducing-hcx-enterprise/).  

>[!TIP]
>Sebbene lo strumento VMware Configuration Maximum descriva le coppie di siti massime da 25 tra on-premises Connector e Cloud Manager, la licenza limita questa a tre per HCX Advanced e 10 per HCX Enterprise Edition.

>[!NOTE]
>VMware HCX Enterprise è disponibile con la soluzione Azure VMware come servizio in anteprima. È gratuito e soggetto alle condizioni per un servizio in anteprima. Quando il servizio VMware HCX Enterprise diventerà disponibile a livello generale, si riceverà un preavviso di 30 giorni relativo al cambio di fatturazione. Si potrà anche disattivare o rifiutare esplicitamente il servizio. Il downgrade da HCx Enterprise a HCX Advanced è possibile senza ridistribuzione, ma è necessario registrare un ticket di supporto per l'azione da eseguire. Se si pianifica un downgrade, assicurarsi che non siano pianificate migrazioni e che non siano in uso funzionalità come RAV, MON.

Vedere innanzitutto le sezioni [Prima di iniziare](#before-you-begin), [Requisiti della versione software](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html) e [Prerequisiti](#prerequisites). 

Vengono poi illustrate tutte le procedure necessarie per:

> [!div class="checklist"]
> * Scaricare il file OVA di VMware HCX Connector.
> * Distribuire il file OVA di VMware HCX Connector in locale.
> * Attivare VMware HCX Connector.
> * Associare l'istanza locale di VMware HCX Connector all'appliance HCX Cloud Manager della soluzione Azure VMware.
> * Configurare l'interconnessione (profilo di rete, profilo di calcolo e mesh di servizi).
> * Completare la configurazione controllando lo stato dell'appliance e verificando che la migrazione sia possibile.

Al termine, seguire i passaggi successivi consigliati alla fine dell'articolo.  

## <a name="before-you-begin"></a>Prima di iniziare

Per la preparazione dell'ambiente, è consigliabile esaminare la documentazione di VMware seguente:

* [Manuale dell'utente di VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-E456F078-22BE-494B-8E4B-076EF33A9CF4.html)
* [Migrazione delle macchine virtuali con VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Considerazioni sulla distribuzione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/install-checklist/GUID-C0A0E820-D5D0-4A3D-AD8E-EEAA3229F325.html)
* [Serie di blog su VMware: migrazione al cloud](https://blogs.vmware.com/vsphere/2019/10/cloud-migration-series-part-2.html) 
* [Porte di rete necessarie per VMware HCX](https://ports.vmware.com/home/VMware-HCX)


## <a name="prerequisites"></a>Prerequisiti

Se si prevede di usare VMware HCX Enterprise, assicurarsi di averne richiesto l'attivazione tramite i canali di supporto della soluzione Azure VMware.


### <a name="on-premises-vsphere-environment"></a>Ambiente vSphere locale

Assicurarsi che l'ambiente vSphere locale (ambiente di origine) soddisfi i [requisiti minimi](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html). 

### <a name="network-and-ports"></a>Rete e porte

* [Azure ExpressRoute copertura globale](tutorial-expressroute-global-reach-private-cloud.md) viene configurato tra i circuiti ExpressRoute del cloud privato e della soluzione VMware di Azure in locale.

* [Tutte le porte necessarie](https://ports.vmware.com/home/VMware-HCX) sono aperte per la comunicazione tra i componenti locali e la soluzione Azure VMware privata.

### <a name="ip-addresses"></a>Indirizzi IP

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]
   
## <a name="download-the-vmware-hcx-connector-ova"></a>Scaricare il file OVA di VMware HCX Connector

Prima di distribuire l'appliance virtuale nell'istanza locale di vCenter, occorre scaricare il file OVA di VMware HCX Connector.  

1. Nel portale di Azure selezionare il cloud privato della soluzione Azure VMware. 

1. Selezionare **Gestisci** > **Connettività** e quindi la scheda **HCX** per identificare l'indirizzo IP di HCX Manager della soluzione Azure VMware. 

   :::image type="content" source="media/tutorial-vmware-hcx/find-hcx-ip-address.png" alt-text="Screenshot dell'indirizzo IP di VMware HCX." lightbox="media/tutorial-vmware-hcx/find-hcx-ip-address.png":::

1. Selezionare **Gestisci**  >  **identità**. 

   Vengono visualizzati gli URL e le credenziali dell'utente per vCenter e NSX-T Manager del cloud privato.

   > [!TIP]
   > La password di vCenter è stata definita durante la configurazione del cloud privato. Si tratta della stessa password che si userà per accedere a HCX Manager della soluzione Azure VMware. È possibile selezionare **genera una nuova password** per generare nuove password vCenter e NSX-T.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Visualizzazione di URL e credenziali di vCenter e NSX Manager del cloud privato." border="true":::


1. Aprire una finestra del browser, accedere a HCX Manager della soluzione Azure VMware sulla porta 443 di `https://x.x.x.9` con le credenziali utente **cloudadmin\@vsphere.local**

1. Selezionare **Amministrazione** > **Aggiornamenti del sistema** e quindi **Ottieni collegamento per il download**.

1. Selezionare un'opzione a scelta per scaricare il file OVA di VMware HCX Connector.

## <a name="deploy-the-vmware-hcx-connector-ova-on-premises"></a>Distribuire il file OVA di VMware HCX Connector in locale

1. Nell'istanza locale di vCenter selezionare un [modello OVF](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) per la distribuzione di VMware HCX Connector. 

   > [!TIP]
   > Si selezionerà il file OVA scaricato nella sezione precedente.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-ovf-template.png" alt-text="Screenshot che mostra come passare a un modello OVF." lightbox="media/tutorial-vmware-hcx/select-ovf-template.png":::


1. Selezionare un nome e un percorso, quindi selezionare una risorsa o un cluster in cui distribuire VMware HCX Connector. Quindi, esaminare i dettagli e le risorse necessarie e selezionare **Avanti**.  

1. Esaminare le condizioni di licenza. Se si accettano, selezionare le risorse di archiviazione e di rete necessarie e infine selezionare **Next** (Avanti).

1. Selezionare la risorsa di archiviazione e quindi **Avanti**.

1. Selezionare il segmento della rete di gestione di VMware HCX definito in precedenza nella sezione sui [prerequisiti per gli indirizzi IP](#ip-addresses).  Quindi selezionare **Avanti**.

1. In **Personalizza modello** immettere tutte le informazioni necessarie, quindi selezionare **Avanti**. 

   :::image type="content" source="media/tutorial-vmware-hcx/customize-template.png" alt-text="Screenshot delle caselle relative alla personalizzazione di un modello." lightbox="media/tutorial-vmware-hcx/customize-template.png":::

1. Verificare la configurazione, quindi selezionare **Fine** per distribuire il file OVA di VMware HCX Connector.
   
   > [!IMPORTANT]
   > Sarà necessario accendere manualmente l'appliance virtuale.  Dopo l'accensione, attendere 10-15 minuti prima di procedere con il passaggio successivo.

Per una panoramica completa di questa procedura, guardare il video [Soluzione Azure VMware: Distribuzione dell'appliance HCX](https://www.youtube.com/embed/UKmSTYrL6AY). 


## <a name="activate-vmware-hcx"></a>Attivare VMware HCX

Dopo aver distribuito il file OVA di VMware HCX Connector in locale e aver avviato l'appliance, è possibile eseguire l'attivazione. Prima di tutto occorre ottenere un codice di licenza dal portale della soluzione Azure VMware.

1. Nel portale della soluzione Azure VMware passare a **Manage (Gestisci)**  > **Connectivity (Connettività)** , selezionare la scheda **HCX** e infine selezionare **Add** (Aggiungi).

1. Usare le credenziali di **amministratore** per accedere all'istanza locale di VMware HCX Manager all'indirizzo `https://HCXManagerIP:9443`. 

   > [!TIP]
   > La password dell'utente **amministratore** è stata definita durante la distribuzione del file OVA di VMware HCX Manager.

   > [!IMPORTANT]
   > Assicurarsi di includere il numero di porta `9443` con l'indirizzo IP di VMware HCX Manager.

1. In **Licenze** immettere la chiave per **HCX Advanced Key** e selezionare **Attiva**.  
   
    > [!NOTE]
    > VMware HCX Manager deve avere un accesso a Internet aperto o un proxy configurato.

1. In **Datacenter Location** (Località del data center) specificare la località più vicina in cui installare VMware HCX Manager in locale. Selezionare quindi **Continua**.

1. In **Nome del sistema** modificare il nome o accettare quello predefinito, quindi selezionare **Continua**.
   
1. Selezionare **Yes, Continue** (Sì, continua).

1. In **Connect your vCenter** (Connettere vCenter) specificare il nome di dominio completo e l'indirizzo IP del server vCenter e le credenziali appropriate e quindi selezionare **Continue** (Continua).
   
   > [!TIP]
   > Il server vCenter è quello in cui è stato distribuito VMware HCX Connector nel data center.

1. In **Configure SSO/PSC** (Configura SSO/PSC) specificare il nome di dominio completo o l'indirizzo IP di Platform Services Controller e quindi selezionare **Continue** (Continua).
   
   > [!NOTE]
   > In genere corrisponde al nome di dominio completo o all'indirizzo IP di vCenter.

1. Verificare che le informazioni immesse siano corrette e selezionare **Riavvia**.
    
   > [!NOTE]
   > Si noterà un ritardo dopo il riavvio prima che venga richiesto il passaggio successivo.

Dopo il riavvio dei servizi, viene visualizzata una schermata con l'opzione vCenter associata a un contrassegno verde. vCenter e SSO devono avere i parametri di configurazione appropriati, che dovrebbero corrispondere a quelli della schermata precedente.

:::image type="content" source="media/tutorial-vmware-hcx/activation-done.png" alt-text="Screenshot del dashboard con lo stato di vCenter verde." lightbox="media/tutorial-vmware-hcx/activation-done.png":::  

Per una panoramica completa di questa procedura, guardare il video [Soluzione Azure VMware: Attivare HCX](https://www.youtube.com/embed/PnVg6SZkQsY?rel=0&amp;vq=hd720).

   > [!IMPORTANT]
   > Se si usa HCX Advanced o HCX Enterprise, potrebbe essere necessario installare la patch dall' [articolo 81558 della Knowledge](https://kb.vmware.com/s/article/81558)base di VMware. 

## <a name="configure-the-vmware-hcx-connector"></a>Configurare VMware HCX Connector

A questo punto occorre aggiungere un'associazione del sito, creare un profilo di rete e di calcolo e abilitare servizi come la migrazione, l'estensione di rete o il ripristino di emergenza. 

### <a name="add-a-site-pairing"></a>Aggiungere un'associazione del sito

È possibile connettere o associare VMware HCX Cloud Manager nella soluzione Azure VMware con VMware HCX Connector nel data center. 

1. Accedere all'istanza locale di vCenter e in **Home** selezionare **HCX**.

1. In **Infrastructure** (Infrastruttura) selezionare **Site Pairing** (Associazione del sito), quindi selezionare l'opzione **Connect To Remote Site** (Connetti al sito remoto) al centro della schermata. 

1. Immettere l'URL o l'indirizzo IP di HCX Cloud Manager della soluzione VMware di Azure annotato in precedenza `https://x.x.x.9` , la soluzione VMware di Azure cloudadmin \@ vSphere. local username e la password. Selezionare **Connetti**.

   > [!NOTE]
   > Per stabilire correttamente una coppia di siti:
   > * VMware HCX Connector deve essere in grado di eseguire il routing all'indirizzo IP di HCX Cloud Manager sulla porta 443.
   >
   > * Usare la stessa password usata per accedere a vCenter. Questa password è stata definita nella schermata di distribuzione iniziale.

   Viene visualizzata una schermata che mostra la connessione (associazione) tra VMware HCX Cloud Connector nella soluzione Azure VMware e l'istanza locale di VMware HCX Connector.

   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Screenshot che mostra l'associazione tra HCX Manager nella soluzione Azure VMware e VMware HCX Connector.":::

Per una panoramica completa di questa procedura, guardare il video [Soluzione Azure VMware: Associazione del sito di VMware HCX](https://www.youtube.com/embed/jXOmYUnbWZY?rel=0&amp;vq=hd720).

### <a name="create-network-profiles"></a>Creare profili di rete

VMware HCX Connector distribuisce un subset di appliance virtuali (automatizzate) che richiedono più segmenti IP. Quando si creano i profili di rete, si usano i segmenti IP identificati durante la [fase di preparazione e pianificazione pre-distribuzione dei segmenti di rete di VMware HCX](production-ready-deployment-steps.md#vmware-hcx-network-segments).

Si creeranno quattro profili di rete:

   - Gestione
   - vMotion
   - Replica
   - Uplink

1. In **Infrastructure** (Infrastruttura) selezionare **Interconnect (Interconnessione)**  > **Multi-Site Service Mesh (Mesh del servizio multisito)**  > **Network Profiles (Profili di rete)**  > **Create Network Profile (Crea profilo di rete)** .

   :::image type="content" source="media/tutorial-vmware-hcx/network-profile-start.png" alt-text="Screenshot delle opzioni da selezionare per iniziare a creare un profilo di rete." lightbox="media/tutorial-vmware-hcx/network-profile-start.png":::

1. Per ogni profilo di rete, selezionare la rete e il gruppo di porte, specificare un nome e creare il pool IP per tale segmento. Selezionare quindi **Crea**. 

   :::image type="content" source="media/tutorial-vmware-hcx/example-configurations-network-profile.png" alt-text="Screenshot dei dettagli di un nuovo profilo di rete.":::

Per una panoramica completa di questa procedura, guardare il video [Soluzione Azure VMware: Profilo di rete HCX](https://www.youtube.com/embed/O0rU4jtXUxc).


### <a name="create-a-compute-profile"></a>Creare un profilo di calcolo

1. In **Infrastruttura** selezionare **Interconnect** > **Compute Profiles** > **Create Compute Profile** (Interconnessione > Profili di calcolo > Crea profilo di calcolo).

   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-create.png" alt-text="Screenshot che mostra le opzioni da selezionare per iniziare a creare un profilo di calcolo." lightbox="media/tutorial-vmware-hcx/compute-profile-create.png":::

1. Immettere un nome per il profilo e selezionare **Continue** (Continua).  

   :::image type="content" source="media/tutorial-vmware-hcx/name-compute-profile.png" alt-text="Screenshot che mostra il nome di un profilo di calcolo e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/name-compute-profile.png":::

1. Selezionare i servizi da abilitare, come la migrazione, l'estensione di rete o il ripristino di emergenza, e quindi selezionare **Continue** (Continua).
  
   > [!NOTE]
   > In genere, non viene apportata alcuna modifica in questo passaggio.

1. In **Select Service Resources** (Seleziona risorse del servizio) selezionare una o più risorse del servizio (cluster) per abilitare i servizi VMware HCX selezionati.  

1. Quando i cluster vengono visualizzati nel data center locale, selezionare **Continue** (Continua).

   :::image type="content" source="media/tutorial-vmware-hcx/select-service-resource.png" alt-text="Screenshot che mostra le risorse di servizio selezionate e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/select-service-resource.png":::

1. In **Select Datastore** (Seleziona archivio dati) selezionare la risorsa di archiviazione dell'archivio dati per la distribuzione delle appliance VMware HCX Interconnect. Selezionare quindi **Continua**.

   Quando vengono selezionate più risorse, VMware HCX usa la prima risorsa selezionata fino a quando non ne viene esaurita la capacità.   

   :::image type="content" source="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png" alt-text="Screenshot che mostra una risorsa di archiviazione dati selezionata e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/deployment-resources-and-reservations.png":::  

1. In **Select Management Network Profile** (Seleziona il profilo di rete di gestione) selezionare il profilo di rete di gestione creato nei passaggi precedenti. Selezionare quindi **Continua**.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-management-network-profile.png" alt-text="Screenshot che mostra la selezione di un profilo di rete di gestione e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/select-management-network-profile.png":::

1. In **Select Uplink Network Profile** (Seleziona profilo di rete uplink) selezionare il profilo di rete uplink creato nella procedura precedente. Selezionare quindi **Continua**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-uplink-network-profile.png" alt-text="Screenshot che mostra la selezione di un profilo di rete uplink e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/select-uplink-network-profile.png":::

1. In **Select vMotion Network Profile** (Seleziona profilo di rete vMotion) selezionare il profilo di rete vMotion creato nei passaggi precedenti. Selezionare quindi **Continua**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-vmotion-network-profile.png" alt-text="Screenshot che mostra la selezione di un profilo di rete vMotion e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/select-vmotion-network-profile.png":::

1. In **Select vSphere Replication Network Profile** (Seleziona profilo di rete di replica vSphere) selezionare il profilo di rete di replica creato nei passaggi precedenti. Selezionare quindi **Continua**.

   :::image type="content" source="media/tutorial-vmware-hcx/select-replication-network-profile.png" alt-text="Screenshot che mostra la selezione di un profilo di rete di replica e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/select-replication-network-profile.png":::

1. In **Select Distributed Switches for Network Extensions** (Seleziona switch distribuiti per le estensioni di rete) selezionare gli switch che contengono le macchine virtuali di cui eseguire la migrazione alla soluzione Azure VMware in una rete estesa di livello 2. Selezionare quindi **Continua**.

   > [!NOTE]
   > Se non si esegue la migrazione di macchine virtuali in reti estese di livello 2 (L2), è possibile ignorare questo passaggio.
   
   :::image type=" content" source="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png" alt-text="Screenshot che mostra la selezione di commutatori virtuali distribuiti e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/select-layer-2-distributed-virtual-switch.png":::

1. Esaminare le regole di connessione e selezionare **Continue** (Continua).  

   :::image type="content" source="media/tutorial-vmware-hcx/review-connection-rules.png" alt-text="Screenshot che mostra le regole di connessione e il pulsante Continue." lightbox="media/tutorial-vmware-hcx/review-connection-rules.png":::

1. Selezionare **Finish** (Fine) per creare il profilo di calcolo.


   :::image type="content" source="media/tutorial-vmware-hcx/compute-profile-done.png" alt-text="Screenshot che mostra le informazioni sul profilo di calcolo." lightbox="media/tutorial-vmware-hcx/compute-profile-done.png":::

Per una panoramica completa di questa procedura, guardare il video [Soluzione Azure VMware: Profilo di calcolo](https://www.youtube.com/embed/e02hsChI3b8).

### <a name="create-a-service-mesh"></a>Creare una rete mesh di servizi

A questo punto è possibile configurare un servizio mesh tra la rete locale e il cloud privato della soluzione VMware di Azure.



> [!NOTE]
> Per stabilire correttamente una rete mesh di servizi con la soluzione Azure VMware:
>
> * Devono essere aperte le porte UDP 500/4500 tra gli indirizzi del profilo di rete uplink dell'istanza locale di VMware HCX Connector e gli indirizzi del profilo di rete uplink di HCX Cloud Manager della soluzione Azure VMware.
>
> * Assicurarsi di esaminare la sezione sulle [porte necessarie per VMware HCX](https://ports.vmware.com/home/VMware-HCX).

1. In **Infrastructure** (Infrastruttura) selezionare **Interconnect (Interconnessione)**  > **Service Mesh (Mesh del servizio)**  > **Create Service Mesh (Crea mesh del servizio)** .    

   :::image type="content" source="media/tutorial-vmware-hcx/create-service-mesh.png" alt-text="Screenshot delle opzioni da selezionare per iniziare a creare una rete mesh di servizi." lightbox="media/tutorial-vmware-hcx/create-service-mesh.png":::

1. Esaminare i siti già popolati e quindi selezionare **Continue** (Continua). 

   > [!NOTE]
   > Se questa è la prima configurazione di mesh del servizio, non sarà necessario modificare questa schermata.  

1. Selezionare i profili di calcolo di origine e remoti dall'elenco a discesa e quindi selezionare **Continue** (Continua).  

   Le selezioni definiscono le risorse in cui le VM possono utilizzare i servizi di VMware HCX.  

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-source.png" alt-text="Screenshot che mostra la selezione del profilo di calcolo di origine." lightbox="media/tutorial-vmware-hcx/select-compute-profile-source.png":::

   :::image type="content" source="media/tutorial-vmware-hcx/select-compute-profile-remote.png" alt-text="Screenshot che mostra la selezione del profilo di calcolo remoto." lightbox="media/tutorial-vmware-hcx/select-compute-profile-remote.png":::

1. Verificare i servizi che verranno abilitati e quindi selezionare **Continue** (Continua).  

1. In **Advanced Configuration - Override Uplink Network profiles** (Configurazione avanzata - Override dei profili di rete uplink) selezionare **Continue** (Continua).  

   I profili di rete uplink si connettono alla rete attraverso la quale è possibile raggiungere le appliance di interconnessione del sito remoto.  
  
1. In **Advanced Configuration - Network Extension Appliance Scale Out** (Configurazione avanzata - Aumento appliance estensione di rete) esaminare le opzioni disponibili e selezionare **Continue** (Continua). 

   È possibile avere fino a otto VLAN per appliance, ma si può distribuire un'altra appliance per aggiungere altre otto VLAN. È inoltre necessario avere uno spazio IP per tenere conto delle appliance aggiuntive, ovvero un IP per appliance.  Per altre informazioni, vedere [Limiti di configurazione di VMware HCX](https://configmax.vmware.com/guest?vmwareproduct=VMware%20HCX&release=VMware%20HCX&categories=41-0,42-0,43-0,44-0,45-0).
   
   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png" alt-text="Screenshot che mostra dove aumentare il numero di VLAN." lightbox="media/tutorial-vmware-hcx/extend-networks-increase-vlan.png":::

1. In **Advanced Configuration - Traffic Engineering** (Configurazione avanzata - Progettazione traffico) rivedere le opzioni e apportare eventuali modifiche necessarie, quindi selezionare **Continue** (Continua).

1. Esaminare l'anteprima della topologia e selezionare **Continue** (Continua).

1. Immettere un nome descrittivo per la mesh del servizio e selezionare **Finish** (Fine) per completare l'operazione.  

1. Selezionare **View Tasks** (Visualizza attività) per monitorare la distribuzione. 

   :::image type="content" source="media/tutorial-vmware-hcx/monitor-service-mesh.png" alt-text="Screenshot che mostra il pulsante per la visualizzazione delle attività.":::

   Al termine della distribuzione della rete mesh di servizi, i servizi verranno visualizzati in verde.

   :::image type="content" source="media/tutorial-vmware-hcx/service-mesh-green.png" alt-text="Screenshot che mostra gli indicatori verdi per i servizi." lightbox="media/tutorial-vmware-hcx/service-mesh-green.png":::

1. Verificare l'integrità della rete mesh di servizi controllando lo stato dell'appliance. 

1. Selezionare **Interconnect (Interconnessione)**  > **Appliances (Appliance)** .

   :::image type="content" source="media/tutorial-vmware-hcx/interconnect-appliance-state.png" alt-text="Screenshot che mostra le opzioni da selezionare per verificare lo stato dell'appliance." lightbox="media/tutorial-vmware-hcx/interconnect-appliance-state.png":::

Per una panoramica completa di questa procedura, guardare il video [Soluzione Azure VMware: Mesh di servizi](https://www.youtube.com/embed/COY3oIws108).

### <a name="optional-create-a-network-extension"></a>(Facoltativo) Creare un'estensione di rete

Se si vogliono estendere reti dall'ambiente locale alla soluzione Azure VMware, seguire questa procedura:

1. In **Servizi** selezionare **Network Extension** > **Create a Network Extension** (Estensione di rete > Crea un'estensione di rete).

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="Screenshot che mostra le opzioni da selezionare per iniziare a creare un'estensione di rete." lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. Selezionare ogni rete da estendere alla soluzione Azure VMware e quindi selezionare **Next** (Avanti).

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="Screenshot che mostra la selezione di una rete.":::

1. Immettere l'IP del gateway locale per ogni rete da estendere e quindi selezionare **Submit** (Invia). 

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="Screenshot che mostra un indirizzo IP del gateway.":::

   Per completare l'estensione della rete sono necessari alcuni minuti. Al termine, lo stato cambierà in **Extension complete** (Estensione completata).

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="Screenshot che mostra lo stato completato dell'estensione." lightbox="media/tutorial-vmware-hcx/extension-complete.png":::

Per una panoramica completa di questa procedura, guardare il video [Soluzione Azure VMware: Estensione di rete](https://www.youtube.com/embed/gYR0nftKui0).


## <a name="next-steps"></a>Passaggi successivi

Se lo stato del tunnel di interconnessione HCX è **attivo** e verde, è possibile eseguire la migrazione e proteggere le macchine virtuali della soluzione VMware di Azure usando VMware HCx. La soluzione Azure VMware supporta le migrazioni dei carichi di lavoro con o senza un'estensione di rete. È comunque possibile eseguire la migrazione dei carichi di lavoro nell'ambiente vSphere, creare reti in locale e distribuire VM in tali reti.  

Per altre informazioni sull'uso di HCX, vedere la documentazione tecnica di VMware:

* [Documentazione di VMware HCX](https://docs.vmware.com/en/VMware-HCX/index.html)
* [Migrazione delle macchine virtuali con VMware HCX](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-D0CD0CC6-3802-42C9-9718-6DA5FEC246C6.html?hWord=N4IghgNiBcIBIGEAaACAtgSwOYCcwBcMB7AOxAF8g)
* [Porte HCX necessarie](https://ports.vmware.com/home/VMware-HCX)
* [Configurare un server proxy HCX prima di approvare il codice di licenza](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-920242B3-71A3-4B24-9ACF-B20345244AB2.html?hWord=N4IghgNiBcIA4CcD2APAngAgBIGEAaIAvkA)
