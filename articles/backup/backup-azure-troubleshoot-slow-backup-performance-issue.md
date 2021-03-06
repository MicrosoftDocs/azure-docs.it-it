---
title: Risolvere i problemi relativi al rallentamento delle prestazioni di backup di file e cartelle
description: Fornisce indicazioni sulla risoluzione dei problemi per diagnosticare la causa del rallentamento delle prestazioni di backup per file e cartelle di Backup di Azure
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 791f0edf5f50d27147e402f09e7a3e4c2ea7ca43
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518525"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Risolvere i problemi di rallentamento delle prestazioni di backup di file e cartelle in Backup di Azure

Questo articolo fornisce indicazioni sulla risoluzione dei problemi per diagnosticare la causa del rallentamento delle prestazioni di backup per file e cartelle quando si usa Backup di Azure. Quando si usa l'agente di Backup di Azure per eseguire il backup dei file, è possibile che il processo richieda più tempo del previsto. Questo ritardo può dipendere da una o più delle cause seguenti:

* [Sono presenti colli di bottiglia delle prestazioni nel computer di cui viene eseguito il backup.](#cause1)
* [Un altro processo o un software antivirus interferisce con il processo di Backup di Azure.](#cause2)
* [L'agente di Backup è in esecuzione in una macchina virtuale di Azure.](#cause3)  
* [Viene eseguito il backup di un numero elevato di file (milioni).](#cause4)

Prima di iniziare a risolvere i problemi, è consigliabile scaricare e installare l' [agente di Backup di Azure più recente](https://aka.ms/azurebackup_agent). L'agente di Backup viene aggiornato di frequente per risolvere diversi problemi, aggiungere funzionalità e migliorare le prestazioni.

È anche consigliabile vedere l'articolo [Servizio Backup di Azure: Domande frequenti](backup-azure-backup-faq.yml) per assicurarsi che non si tratti di problemi di configurazione comuni.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Causa: Il processo di backup viene eseguito in modalità non ottimizzata

* L'agente MARS può eseguire il processo di backup in **modalità ottimizzata** usando il journal delle modifiche USN (numero di sequenza di aggiornamento) o la **modalità non ottimizzata** controllando la presenza di modifiche nelle directory o nei file eseguendo la scansione dell'intero volume.
* La modalità non ottimizzata è lenta perché l'agente deve analizzare tutti i file nel volume e confrontarli con i metadati per determinare i file modificati.
* Per eseguire la verifica, aprire **Dettagli processo** nella console dell'agente MARS e controllare lo stato per verificare che sia **Transferring data (unoptimized, may take more time)** (Trasferimento dati in corso, non ottimizzato può richiedere più tempo) come illustrato di seguito:

    ![Esecuzione in modalità non ottimizzata](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* A causa delle condizioni seguenti è possibile che l'esecuzione del processo di backup avvenga in modalità non ottimizzata:
  * Il primo backup, noto anche come Replica iniziale, viene eseguito sempre in modalità non ottimizzata
  * Se il processo di backup precedente ha esito negativo, il successivo processo di backup pianificato verrà eseguito come non ottimizzato.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Causa: colli di bottiglia delle prestazioni nel computer

I colli di bottiglia nel computer in cui viene eseguito il backup possono causare ritardi. Ad esempio, i colli di bottiglia possono essere causati dalla capacità del computer di leggere o scrivere su disco o dalla larghezza di banda disponibile per inviare dati in rete.

Per rilevare i colli di bottiglia, Windows offre uno strumento predefinito chiamato [Monitor prestazioni](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (Perfmon).

Ecco alcuni contatori delle prestazioni e intervalli che possono essere utili per diagnosticare i colli di bottiglia e ottenere backup ottimali.

| Contatore | Stato |
| --- | --- |
| Disco logico (disco fisico) - % inattività |<li> Da 100% inattivo a 50% inattivo = Integro</br><li> Da 49% inattivo a 20% inattivo = Avviso o Monitoraggio</br><li> Da 19% inattivo a 0% inattivo = critico o non in base alle specifiche |
| Disco logico (disco fisico) - % Media letture o scritture disco/sec |<li> Da 0,001 ms a 0,015 ms = Integro</br><li> Da 0,015 ms a 0,025 ms = avviso o monitoraggio</br><li> 0,026 ms o più = Critico o Fuori specifica |
| Disco logico (disco fisico) - Lunghezza corrente coda del disco (per tutte le istanze) |80 richieste per più di 6 minuti |
| Memoria - Byte del pool non di paging |<li> Meno del 60% del pool utilizzato = integro<br><li> Dal 61% all'80% del pool utilizzato = Avviso o Monitoraggio</br><li> Maggiore dell'80% del pool utilizzato = Critico o Non specificato |
| Memoria - Byte del pool di paging |<li> Meno del 60% del pool utilizzato = integro</br><li> Dal 61% all'80% del pool utilizzato = Avviso o Monitoraggio</br><li> Maggiore dell'80% del pool utilizzato = Critico o Non specificato |
| Memoria - MByte disponibili |<li> 50% della memoria disponibile o superiore = Integro</br><li> 25% della memoria disponibile = Monitoraggio</br><li>10% della memoria disponibile = Avviso</br><li> Meno di 100 MB o il 5% della memoria disponibile = Critico o Fuori specifica |
| Processore - \%Tempo processore (tutte le istanze) |<li> Meno del 60% utilizzato = Integro</br><li> Dal 61% al 90% utilizzato = Monitoraggio o attenzione</br><li> Dal 91% al 100% utilizzato = Critico |

> [!NOTE]
> Se si determina che l'infrastruttura è la causa del problema, è consigliabile deframmentare i dischi a intervalli regolari per ottenere prestazioni migliori.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Causa: un altro processo o un software antivirus interferisce con Backup di Azure

Sono state identificate diverse istanze in cui altri processi nel sistema Windows hanno influito negativamente sulle prestazioni del processo dell'agente di Backup di Azure. Ad esempio, se si usa l'agente di Backup di Azure e un altro programma per eseguire il backup dei dati o se è in esecuzione un software antivirus con un blocco sui file di cui eseguire il backup, i diversi blocchi sui file possono causare conflitti. In questo caso, il backup può non riuscire oppure il processo può richiedere più tempo del previsto.

L'indicazione migliore in questo scenario consiste nel disattivare l'altro programma di backup per vedere se cambia il tempo di backup per l'agente di Backup di Azure. In genere, è sufficiente assicurarsi che non siano in esecuzione più processi di backup contemporaneamente per evitare che interferiscano reciprocamente.

Per i programmi antivirus è consigliabile escludere i file e percorsi seguenti:

* C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure\bin\cbengine.exe come processo
* Cartelle in C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure\
* Spazio di lavoro, se non si usa il percorso standard precedente

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Causa: l'agente di Backup viene eseguito in una macchina virtuale di Azure

Se si esegue l'agente di Backup in una macchina virtuale, le prestazioni risulteranno rallentate rispetto all'esecuzione in un computer fisico. Questo è un comportamento previsto a causa delle limitazioni delle operazioni di I/O al secondo.  È tuttavia possibile ottimizzare le prestazioni trasferendo le unità dati da includere nel backup ad Archiviazione Premium di Azure. La correzione di questo problema è in fase di risoluzione e sarà disponibile nelle versioni future.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Causa: viene eseguito il backup di un numero elevato di file (milioni)

Lo spostamento di un grande volume di dati richiederà più tempo rispetto allo spostamento di un volume di dati inferiore. In alcuni casi, i tempi di backup sono correlati non solo alle dimensioni dei dati, ma anche al numero di file o cartelle. Ciò vale soprattutto quando sono sottoposti a backup milioni di file di piccole dimensioni (da pochi byte a pochi kilobyte).

Questo comportamento è dovuto al fatto che mentre è attivo il backup e lo spostamento dei dati in Azure, Azure cataloga contemporaneamente i file. In alcuni scenari rari, questa operazione può richiedere più tempo del previsto.

Gli indicatori seguenti possono risultare utili per individuare il collo di bottiglia e agire di conseguenza nei passaggi successivi:

* **L'interfaccia utente visualizza lo stato di avanzamento per il trasferimento dei dati**. I dati sono ancora in fase di trasferimento. La larghezza di banda di rete o le dimensioni dei dati potrebbero causare ritardi.
* **L'interfaccia utente non mostra lo stato di avanzamento per il trasferimento dei dati.** Aprire i log disponibili in "C:\Programmi\Agente di Servizi di ripristino di Microsoft Azure\Temp" e quindi cercare la voce FileProvider::EndData nei log. Questa voce indica che il trasferimento dei dati è stato completato e che l'operazione di catalogo è in corso. Non annullare i processi di backup. Al contrario, attendere ancora qualche istante che l'operazione di catalogo venga completata. Se il problema persiste, contattare il [supporto di Azure](https://portal.azure.com/#create/Microsoft.Support).

Se si sta tentando di eseguire il backup di dischi [](./offline-backup-azure-data-box.md) di grandi dimensioni, è consigliabile usare Azure Data Box per il primo backup (replica iniziale).  Se non è possibile usare Data Box, eventuali problemi di rete temporanei che si verificano nell'ambiente durante i trasferimenti di dati di lunga durata sulla rete possono causare errori di backup.  Per evitare questi errori, è possibile aggiungere alcune cartelle al backup iniziale e continuare ad aggiungerle in modo incrementale fino a quando non viene completato il backup di tutte le cartelle in Azure.  I successivi backup incrementali saranno relativamente più veloci.

## <a name="next-steps"></a>Passaggi successivi

* [Domande frequenti sul backup di file e cartelle](backup-azure-file-folder-backup-faq.yml)
