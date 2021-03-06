---
title: 'Esercitazione: Eseguire il backup di database SAP HANA nelle VM di Azure'
description: Questa esercitazione illustra come eseguire il backup di database SAP HANA in esecuzione nelle VM di Azure in un insieme di credenziali di Servizi di ripristino di Backup di Azure.
ms.topic: tutorial
ms.date: 02/24/2020
ms.openlocfilehash: 00109de349c1fdfdbaff9de30d18f64d8b986a59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587645"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Esercitazione: Eseguire il backup di database SAP HANA in una VM di Azure

Questa esercitazione illustra come eseguire il backup di database SAP HANA in esecuzione nelle VM di Azure in un insieme di credenziali di Servizi di ripristino di Backup di Azure. In questo articolo viene spiegato come:

> [!div class="checklist"]
>
> * Creare e configurare un insieme di credenziali
> * Individuare i database
> * Configurare i backup

Per informazioni su tutti gli scenari attualmente supportati, vedere [qui](sap-hana-backup-support-matrix.md#scenario-support).

>[!NOTE]
>A partire dal 1° agosto 2020, il backup di SAP HANA per RHEL (7.4, 7.6, 7.7 e 8.1) è disponibile a livello generale.

## <a name="prerequisites"></a>Prerequisiti

Prima di configurare i backup, eseguire la procedura seguente:

* Identificare o creare un [insieme di credenziali di Servizi di ripristino](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) nella stessa area e nella stessa sottoscrizione della macchina virtuale che esegue SAP HANA.
* Consentire la connettività dalla VM a Internet, in modo che Azure sia raggiungibile, come descritto nella procedura [Configurare la connettività di rete](#set-up-network-connectivity) descritta di seguito.
* Verificare che la lunghezza combinata del nome della VM server SAP HANA e del nome del gruppo di risorse non superi 84 caratteri per le VM di Azure Resource Manager e 77 caratteri per le VM classiche. Questa limitazione è dovuta al fatto che alcuni caratteri sono riservati dal servizio.
* È necessario che sia presente una chiave in **hdbuserstore** che soddisfi i criteri seguenti:
  * Deve essere presente in **hdbuserstore** predefinito. Il valore predefinito è l'account `<sid>adm` con cui viene installato SAP HANA.
  * Per MDC, la chiave deve puntare alla porta SQL di **NAMESERVER**. Nel caso di SDC, deve puntare alla porta SQL di **INDEXSERVER**
  * Deve avere le credenziali per aggiungere ed eliminare gli utenti
  * Si noti che questa chiave può essere eliminata dopo l'esecuzione corretta dello script di pre-registrazione
* Eseguire lo script di configurazione di backup SAP HANA (script di pre-registrazione) come utente radice nella macchina virtuale in cui è installato HANA. [Questo script](https://aka.ms/scriptforpermsonhana) prepara il sistema HANA per il backup. Per altre informazioni sullo script di pre-registrazione, vedere la sezione [Funzionalità dello script di pre-registrazione](#what-the-pre-registration-script-does).
* Se il programma di installazione di HANA usa endpoint privati, eseguire lo [script di pre-registrazione](https://aka.ms/scriptforpermsonhana) con il parametro *-sn* o *--skip-network-checks*.

>[!NOTE]
>Lo script di preregistrazione installa **compat-unixODBC234** per i carichi di lavoro SAP HANA in esecuzione in RHEL (7.4, 7.6 e 7.7) e **unixODBC** per RHEL 8.1. [Questo pacchetto si trova nel repository RHEL for SAP HANA (per RHEL 7 Server) Update Services for SAP Solutions (RPMs)](https://access.redhat.com/solutions/5094721).  Per un'immagine RHEL di Azure Marketplace, il repository è **rhui-rhel-sap-hana-for-rhel-7-server-rhui-e4s-rpms**.

## <a name="set-up-network-connectivity"></a>Configurare la connettività di rete

Per tutte le operazioni, un database SAP HANA in esecuzione su una macchina virtuale di Azure richiede la connettività al servizio Backup di Azure, ad Archiviazione di Azure e ad Azure Active Directory. Questa operazione può essere eseguita usando endpoint privati oppure consentendo l'accesso agli indirizzi IP pubblici o FQDN richiesti. Non consentire una connettività corretta ai servizi di Azure necessari può causare errori nelle operazioni quali l'individuazione del database, la configurazione del backup, l'esecuzione di backup e il ripristino dei dati.

La tabella seguente elenca le varie alternative che è possibile usare per stabilire la connettività:

| **Opzione**                        | **Vantaggi**                                               | **Svantaggi**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Endpoint privati                 | Backup su indirizzi IP privati all'interno della rete virtuale  <br><br>   Offerta di un controllo granulare lato rete e lato insieme di credenziali | [Costi](https://azure.microsoft.com/pricing/details/private-link/) standard per endpoint privati |
| Tag del servizio NSG                  | Gestione semplificata perché le modifiche degli intervalli vengono unite automaticamente   <br><br>   Nessun costo aggiuntivo | Utilizzabile solo con i gruppi di sicurezza di rete  <br><br>    Accesso consentito all'intero servizio |
| Tag FQDN di Firewall di Azure          | Gestione semplificata perché i FQDN necessari vengono gestiti automaticamente | Utilizzabile solo con Firewall di Azure                         |
| Accesso consentito a FQDN/IP del servizio | Nessun costo aggiuntivo   <br><br>  Funziona con tutti i firewall e le appliance di sicurezza di rete | È possibile che sia necessario accedere a un ampio set di indirizzi IP o FQDN   |
| Usare un proxy HTTP                 | Singolo punto di accesso Internet alle VM                       | Costi aggiuntivi per l'esecuzione di una VM con il software proxy         |

Altre informazioni sull'uso di queste opzioni sono condivise di seguito:

### <a name="private-endpoints"></a>Endpoint privati

Gli endpoint privati consentono di connettersi in modo sicuro dai server all'interno di una rete virtuale nell'insieme di credenziali di Servizi di ripristino. L'endpoint privato usa un indirizzo IP dallo spazio indirizzi della rete virtuale per l'insieme di credenziali. Il traffico di rete tra le risorse all'interno della rete virtuale e l'insieme di credenziali si sposta attraverso la rete virtuale e un collegamento privato nella rete backbone Microsoft. In questo modo si elimina l'esposizione dalla rete Internet pubblica. Altre informazioni sugli endpoint privati per Backup di Azure sono disponibili [qui](./private-endpoints.md).

### <a name="nsg-tags"></a>Tag NSG

Se si usano gruppi di sicurezza di rete (NSG), usare il tag del servizio *AzureBackup* per consentire l'accesso in uscita a Backup di Azure. Oltre al tag di Backup di Azure, è necessario consentire la connettività per l'autenticazione e il trasferimento dei dati creando [regole NSG](../virtual-network/network-security-groups-overview.md#service-tags) simili per Azure AD (*AzureActiveDirectory*) e Archiviazione di Azure (*Storage*). I passaggi seguenti descrivono il processo di creazione di una regola per il tag di Backup di Azure:

1. In **Tutti i servizi**, passare a **Gruppi di sicurezza di rete** e selezionare il gruppo di sicurezza di rete.

1. In **Impostazioni** selezionare **Regole di sicurezza in uscita**.

1. Selezionare **Aggiungi**. Immettere tutti i dettagli necessari per la creazione di una nuova regola, come descritto nelle [impostazioni delle regole di sicurezza](../virtual-network/manage-network-security-group.md#security-rule-settings). Assicurarsi che l'opzione **Destinazione** sia impostata su *Tag del servizio* e che l'opzione **Tag del servizio di destinazione** sia impostata su *AzureBackup*.

1. Selezionare **Aggiungi** per salvare la regola di sicurezza in uscita appena creata.

È possibile creare in modo analogo le [regole di sicurezza in uscita NSG](../virtual-network/network-security-groups-overview.md#service-tags) per Archiviazione di Azure e Azure AD. Per altre informazioni sui tag di servizio, vedere [questo articolo](../virtual-network/service-tags-overview.md).

### <a name="azure-firewall-tags"></a>Tag del Firewall di Azure

Se si usa Firewall di Azure, creare una regola dell'applicazione usando il tag [FQDN di Firewall di Azure](../firewall/fqdn-tags.md) *AzureBackup*. In questo modo si consente l'accesso in uscita a Backup di Azure.

### <a name="allow-access-to-service-ip-ranges"></a>Consentire l'accesso agli intervalli IP del servizio

Se si decide di consentire l'accesso agli IP del servizio, fare riferimento agli intervalli IP nel file JSON disponibile [qui](https://www.microsoft.com/download/confirmation.aspx?id=56519). È necessario consentire l'accesso agli indirizzi IP corrispondenti a Backup di Azure, Archiviazione di Azure e Azure Active Directory.

### <a name="allow-access-to-service-fqdns"></a>Accesso consentito a FQDN del servizio

È anche possibile usare gli FQDN seguenti per consentire l'accesso ai servizi richiesti dai server:

| Service    | Nomi di dominio a cui accedere                             |
| -------------- | ------------------------------------------------------------ |
| Backup di Azure  | `*.backup.windowsazure.com`                             |
| Archiviazione di Azure | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Consentire l'accesso ai nomi di dominio completo (FQDN) nelle sezioni 56 e 59 come indicato in [questo articolo](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>Usare un server proxy HTTP per instradare il traffico

Quando si esegue il backup di un database SAP HANA in una macchina virtuale di Azure, l'estensione di backup nella VM usa le API HTTPS per inviare i comandi di gestione a Backup di Azure e i dati ad Archiviazione di Azure. L'estensione di backup usa anche Azure AD per l'autenticazione. Eseguire il routing del traffico di estensione per il backup di questi tre servizi attraverso il proxy HTTP. Usare l'elenco di indirizzi IP e FQDN indicati in precedenza per consentire l'accesso ai servizi necessari. I server proxy autenticati non sono supportati.

## <a name="understanding-backup-and-restore-throughput-performance"></a>Informazioni sulle prestazioni della velocità effettiva di backup e ripristino

I backup (log e non log) in SAP HANA macchine virtuali di Azure fornite tramite Backint sono flussi agli insiemi di credenziali dei servizi di ripristino di Azure ed è quindi importante comprendere questa metodologia di streaming.

Il componente Backint di HANA fornisce le "pipe" (una pipe da cui leggere e una pipe per la scrittura), connesse a dischi sottostanti in cui risiedono i file di database, che vengono quindi letti dal servizio backup di Azure e trasportati nell'insieme di credenziali di servizi di ripristino di Azure. Il servizio backup di Azure esegue anche un checksum per convalidare i flussi, oltre ai controlli di convalida nativi di backint. Queste convalide assicurano che i dati presenti nell'insieme di credenziali di servizi di ripristino di Azure siano effettivamente affidabili e recuperabili.

Poiché i flussi riguardano principalmente i dischi, è necessario comprendere le prestazioni del disco per misurare le prestazioni di backup e ripristino. Vedere [questo articolo](../virtual-machines/disks-performance.md) per una conoscenza approfondita della velocità effettiva e delle prestazioni dei dischi nelle macchine virtuali di Azure. Queste sono valide anche per le prestazioni di backup e ripristino.

**Il servizio backup di Azure tenta di ottenere fino a ~ 420 Mbps per i backup non di log (ad esempio, completo, differenziale e incrementale) e fino a 100 Mbps per i backup del log per Hana**. Come indicato in precedenza, le velocità non sono garantite e dipendono dai fattori seguenti:

* Numero massimo di velocità effettiva del disco non memorizzato nella cache della macchina virtuale
* Tipo di disco sottostante e relativa velocità effettiva
* Il numero di processi che tentano di leggere e scrivere nello stesso disco nello stesso momento.

> [!IMPORTANT]
> Nelle VM più piccole, in cui la velocità effettiva del disco non memorizzata nella cache è molto vicina o inferiore a 400 MBps, si potrebbe temere che l'intero IOPS del disco venga utilizzato dal servizio di backup che può influire sulle operazioni di SAP HANA relative alla lettura/scrittura dai dischi. In tal caso, se si desidera limitare o limitare l'utilizzo del servizio di backup al limite massimo, è possibile fare riferimento alla sezione successiva.

### <a name="limiting-backup-throughput-performance"></a>Limitazione delle prestazioni della velocità effettiva del backup

Se si vuole limitare il consumo di IOPS del disco del servizio di backup a un valore massimo, seguire questa procedura.

1. Vai alla cartella "opt/msawb/bin"
2. Creare un nuovo file JSON denominato "ExtensionSettingOverrides.JSON"
3. Aggiungere una coppia chiave-valore al file JSON come indicato di seguito:

    ```json
    {
    "MaxUsableVMThroughputInMBPS": 200
    }
    ```

4. Modificare le autorizzazioni e la proprietà del file come indicato di seguito:
    
    ```bash
    chmod 750 ExtensionSettingsOverrides.json
    chown root:msawb ExtensionSettingsOverrides.json
    ```

5. Non è necessario riavviare alcun servizio. Il servizio backup di Azure tenterà di limitare le prestazioni della velocità effettiva come indicato in questo file.

## <a name="what-the-pre-registration-script-does"></a>Funzionalità dello script di pre-registrazione

L'esecuzione dello script di pre-registrazione esegue le funzioni seguenti:

* In base alla distribuzione Linux, lo script installa o aggiorna tutti i pacchetti necessari richiesti dall'agente di Backup di Azure.
* Esegue i controlli della connettività di rete in uscita con i server di Backup di Azure e i servizi dipendenti come Azure Active Directory e Archiviazione di Azure.
* Accede al sistema HANA usando la chiave utente elencata come parte dei [prerequisiti](#prerequisites). La chiave utente viene usata per creare un utente di backup (AZUREWLBACKUPHANAUSER) nel sistema HANA e **può essere eliminata dopo l'esecuzione corretta dello script di pre-registrazione**.
* Ad AZUREWLBACKUPHANAUSER vengono assegnati questi ruoli e autorizzazioni necessari:
  * Per MDC: DATABASE ADMIN e BACKUP ADMIN (HANA 2.0 SPS05 e versioni successive): per creare nuovi database durante il ripristino.
  * Per SDC: BACKUP ADMIN: per creare nuovi database durante il ripristino.
  * CATALOG READ: per leggere il catalogo di backup.
  * SAP_INTERNAL_HANA_SUPPORT: per accedere ad alcune tabelle private. Richiesto solo per le versioni di SDC e MDC inferiori a HANA 2.0 SPS04 Rev 46. Non è richiesto per HANA 2.0 SPS04 Rev 46 e versioni successive perché ora le informazioni necessarie vengono recuperate da tabelle pubbliche con la correzione del team HANA.
* Lo script aggiunge una chiave a **hdbuserstore** per AZUREWLBACKUPHANAUSER per il plug-in di backup HANA per gestire tutte le operazioni (query su database, operazioni di ripristino, configurazione ed esecuzione del backup).

>[!NOTE]
> È possibile passare in modo esplicito la chiave utente elencata come parte dei [prerequisiti](#prerequisites) come parametro allo script di pre-registrazione: `-sk SYSTEM_KEY_NAME, --system-key SYSTEM_KEY_NAME` <br><br>
>Per informazioni sugli altri parametri accettati dallo script, usare il comando `bash msawb-plugin-config-com-sap-hana.sh --help`

Per confermare la creazione della chiave, eseguire il comando HDBSQL nella macchina virtuale HANA con le credenziali SIDADM:

```hdbsql
hdbuserstore list
```

L'output del comando dovrebbe visualizzare la chiave {SID}{DBNAME}, con l'utente visualizzato come AZUREWLBACKUPHANAUSER.

>[!NOTE]
> Assicurarsi di avere un set univoco di file SSFS in `/usr/sap/{SID}/home/.hdb/`. Questo percorso dovrebbe contenere un'unica cartella.

Di seguito è riportato un riepilogo dei passaggi necessari per completare l'esecuzione dello script di pre-registrazione.

|Chi  |Da  |Versione da eseguire  |Commenti  |
|---------|---------|---------|---------|
|```<sid>```ADM (sistema operativo)     |  SISTEMA OPERATIVO HANA       |   Leggere l'esercitazione e scaricare lo script di pre-registrazione      |   Leggere i [prerequisiti precedenti](#prerequisites)    scaricare lo script di pre-registrazione da [qui](https://aka.ms/scriptforpermsonhana)  |
|```<sid>```ADM (sistema operativo) e utente di sistema (HANA)    |      SISTEMA OPERATIVO HANA   |   Esegui comando hdbuserstore set      |   ad esempio, hdbuserstore impostare il nome host di sistema>:3 ```<Instance#>``` 13 ```<password>``` **Nota**  di sistema: assicurarsi di usare il nome host anziché l'indirizzo IP o il nome di dominio completo      |
|```<sid>```ADM (sistema operativo)    |   SISTEMA OPERATIVO HANA      |  Comando Esegui elenco hdbuserstore       |   Controllare se il risultato include l'archivio predefinito, come indicato di seguito: ```KEY SYSTEM  ENV : <hostname>:3<Instance#>13  USER: SYSTEM```      |
|Radice (sistema operativo)     |   SISTEMA OPERATIVO HANA        |    Eseguire lo script di pre-registrazione di backup di Azure HANA      |    ```./msawb-plugin-config-com-sap-hana.sh -a --sid <SID> -n <Instance#> --system-key SYSTEM```     |
|```<sid>```ADM (sistema operativo)    |  SISTEMA OPERATIVO HANA       |   Comando Esegui elenco hdbuserstore      |    Controllare se il risultato include nuove righe come indicato di seguito:  ```KEY AZUREWLBACKUPHANAUSER  ENV : localhost: 3<Instance#>13   USER: AZUREWLBACKUPHANAUSER```     |

Al termine dell'esecuzione dello script di pre-registrazione e della verifica, è possibile procedere alla verifica [dei requisiti di connettività](#set-up-network-connectivity) e quindi [configurare il backup](#discover-the-databases) dall'insieme di credenziali dei servizi di ripristino

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Un insieme di credenziali dei Servizi di ripristino è un'entità che archivia i backup e i punti di ripristino che sono stati creati nel corso del tempo. L'insieme di credenziali dei servizi di ripristino contiene anche i criteri di backup associati alle macchine virtuali protette.

Per creare un insieme di credenziali dei servizi di ripristino:

1. Accedere alla propria sottoscrizione nel [portale di Azure](https://portal.azure.com/).

2. Nel menu sinistro selezionare **Tutti i servizi**

   ![Selezionare tutti i servizi](./media/tutorial-backup-sap-hana-db/all-services.png)

3. Nella finestra di dialogo **Tutti i servizi** inserire **Servizi di ripristino**. L'elenco delle risorse filtra sulla base degli input. Nell'elenco delle risorse selezionare **Insieme di credenziali di Servizi di ripristino**.

   ![Selezionare gli insiemi di credenziali di Servizi di ripristino](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Nel dashboard degli insiemi di credenziali di **Servizi di ripristino** selezionare **Aggiungi**.

   ![Aggiungere l'insieme di credenziali di Servizi di ripristino](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Si apre la finestra di dialogo **Insieme di credenziali dei Servizi di ripristino**. Specificare i valori per **Nome, Sottoscrizione, Gruppo di risorse** e **Località**

   ![Creare un insieme di credenziali di Servizi di ripristino](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Name**: il nome viene usato per identificare l'insieme di credenziali di Servizi di ripristino e deve essere univoco per la sottoscrizione di Azure. Specificare un nome che contiene almeno due caratteri, ma non più di 50. Il nome deve iniziare con una lettera e deve contenere solo lettere, numeri e trattini. Per questa esercitazione viene usato il nome **SAPHanaVault**.
   * **Sottoscrizione** scegliere la sottoscrizione da usare. Se si è un membro di una sola sottoscrizione, verrà visualizzato tale nome. Se non si è certi della sottoscrizione da usare, scegliere quella predefinita (consigliato). Sono disponibili più opzioni solo se l'account aziendale o dell'istituto di istruzione è associato a più sottoscrizioni di Azure. In questa esercitazione è stata usata la sottoscrizione **SAP HANA solution lab subscription**.
   * **Gruppo di risorse**: Usare un gruppo di risorse esistente oppure crearne uno nuovo. In questa esercitazione è stato usato il gruppo **SAPHANADemo**.<br>
   Selezionare **Usa esistente** e nell'elenco a discesa scegliere una risorsa per visualizzare l'elenco di gruppi di risorse disponibili nella sottoscrizione. Per creare un nuovo gruppo di risorse, selezionare **Crea nuovo** e inserire il nome. Per informazioni complete sui gruppi di risorse, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/management/overview.md).
   * **Località**: selezionare l'area geografica per l'insieme di credenziali. L'insieme di credenziali deve trovarsi nella stessa area della macchina virtuale che esegue SAP HANA. In questa esercitazione è stata usata l'area **Stati Uniti orientali 2**.

5. Selezionare **Rivedi e crea**.

   ![Selezionare Rivedi e crea](./media/tutorial-backup-sap-hana-db/review-create.png)

L'insieme di credenziali di Servizi di ripristino è stato creato.

## <a name="discover-the-databases"></a>Individuare i database

1. Nell'insieme di credenziali, in **Attività iniziali**, selezionare **Backup**. In **Posizione di esecuzione del carico di lavoro** selezionare **SAP HANA in una macchina virtuale di Azure**.
2. Selezionare **Avvia individuazione**. Viene avviata l'individuazione delle macchine virtuali Linux non protette nell'area dell'insieme di credenziali. Verrà visualizzata la macchina virtuale di Azure che si vuole proteggere.
3. In **Seleziona macchine virtuali** selezionare il collegamento per scaricare lo script che fornisce le autorizzazioni per il servizio Backup di Azure per accedere alle macchine virtuali SAP HANA per l'individuazione del database.
4. Eseguire lo script nella macchina virtuale che ospita i database SAP HANA di cui eseguire il backup.
5. Dopo aver eseguito lo script nella macchina virtuale, selezionarla in **Seleziona macchine virtuali**. Selezionare quindi **Individua database**.
6. Backup di Azure individua tutti i database SAP HANA presenti nella macchina virtuale. Durante l'individuazione, Backup di Azure registra la VM con l'insieme di credenziali e installa l'estensione. Nel database non vengono installati agenti.

   ![Individuare i database](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Configurare il backup

Dopo aver individuato i database di cui eseguire il backup, abilitare il backup.

1. Selezionare **Configura backup**.

   ![Configurare il backup](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. In **Seleziona elementi per backup** selezionare uno o più database da proteggere e quindi selezionare **OK**.

   ![Seleziona elementi per backup](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. In **Criteri di backup > Scegliere i criteri di backup** creare un nuovo criterio di backup per i database, seguendo le istruzioni riportate nella sezione successiva.

   ![Scegliere i criteri di backup](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Dopo aver creato i criteri, scegliere **Abilita backup** dal menu **Backup**.

   ![Selezionare Abilita backup](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Per tenere traccia dello stato di avanzamento della configurazione di backup, vedere l'area **Notifiche** del portale.

## <a name="creating-a-backup-policy"></a>Creazione di un criterio di backup

Un criterio di backup definisce quando vengono acquisiti i backup e per quanto tempo vengono conservati.

* Un criterio viene creato a livello di insieme di credenziali.
* Più insiemi di credenziali possono usare gli stessi criteri di backup, ma è necessario applicare i criteri di backup a ogni insieme di credenziali.

Specificare le impostazioni del criterio come segue:

1. In **Nome criterio** immettere un nome per il nuovo criterio. In questo caso, immettere **SAPHANA**.

   ![Immettere un nome per il nuovo criterio](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. In **Criteri di backup completo** selezionare un valore per **Frequenza di backup**. È possibile scegliere **Giornaliera** o **Settimanale**. Per questa esercitazione, scegliere la frequenza di backup **Giornaliera**.

   ![Selezionare una frequenza di backup](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. In **Intervallo conservazione** configurare le impostazioni di conservazione per il backup completo.
   * Per impostazione predefinita, sono selezionate tutte le opzioni. Deselezionare gli eventuali limiti dell'intervallo di conservazione che non si vogliono usare e impostare quelli da usare.
   * Il periodo di conservazione minimo di qualsiasi tipo di backup (completo/differenziale/del log) è di sette giorni.
   * I punti di recupero vengono contrassegnati per la conservazione, in base al relativo intervallo. Ad esempio, se si seleziona un backup completo giornaliero, viene attivato solo un backup completo ogni giorno.
   * Il backup di un giorno specifico viene contrassegnato e conservato in base all'intervallo e all'impostazione di conservazione settimanale.
   * L'intervallo di conservazione mensile e annuale si comporta allo stesso modo.
4. Nel menu del **criterio Backup completo** selezionare **OK** per accettare le impostazioni.
5. Quindi selezionare **Backup differenziale** per aggiungere un criterio differenziale.
6. Nel **criterio Backup differenziale** selezionare **Abilita** per accedere alle opzioni di frequenza e conservazione. È stato abilitato un backup differenziale da eseguire ogni **Domenica** alle **2:00** e che viene conservato per **30 giorni**.

   ![Criteri di backup differenziale](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >È possibile scegliere un backup quotidiano differenziale o incrementale, ma non entrambi.

7. In **Criteri di backup incrementale** selezionare **Abilita** per aprire i controlli relativi a frequenza e conservazione.
    * È possibile attivare al massimo un backup differenziale al giorno.
    * I backup incrementali possono essere conservati per un massimo di 180 giorni. Se è necessario conservarli più a lungo, usare i backup completi.

    ![Criteri di backup incrementale](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

8. Selezionare **OK** per salvare il criterio e tornare al menu principale **Criteri di backup**.
9. Selezionare **Backup del log** per aggiungere un criterio per i backup del log delle transazioni.
   * Per impostazione predefinita, l'opzione **Backup del log** è impostata su **Abilita**. Questa opzione non può essere disabilitata perché SAP HANA gestisce tutti i backup del log.
   * Per la pianificazione del backup è stata impostata l'opzione **2 ore** e per il periodo di conservazione **15 giorni**.

    ![Criteri di backup del log](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > I backup del log iniziano a fluire solo dopo il corretto completamento di un backup completo.
   >

10. Selezionare **OK** per salvare il criterio e tornare al menu principale **Criteri di backup**.
11. Dopo aver completato la definizione dei criteri di backup, selezionare **OK**.

I backup per i database SAP HANA sono stati correttamente configurati.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [eseguire backup su richiesta dei database SAP HANA in esecuzione nelle VM di Azure](backup-azure-sap-hana-database.md#run-an-on-demand-backup)
* Informazioni su come [ripristinare i database SAP HANA in esecuzione nelle VM di Azure](sap-hana-db-restore.md)
* Informazioni su come [gestire i database SAP HANA di cui è stato eseguito il backup con Backup di Azure](sap-hana-db-manage.md)
* Informazioni su come [risolvere i problemi comuni che si verificano durante il backup di database SAP HANA](backup-azure-sap-hana-database-troubleshoot.md)