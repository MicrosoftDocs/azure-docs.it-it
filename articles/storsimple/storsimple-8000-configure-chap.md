---
title: Configurare CHAP per il dispositivo StorSimple serie 8000 | Documentazione Microsoft
description: Viene descritto come configurare Challenge Handshake Authentication Protocol su un dispositivo StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: TBD
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 694d63dacedd8ffcd075e73bb91b5ab0a089b58c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91766494"
---
# <a name="configure-chap-for-your-storsimple-device"></a>Configurare CHAP per il dispositivo StorSimple

In questa esercitazione viene illustrato come configurare CHAP per il dispositivo StorSimple. La procedura descritta in questo articolo si applica ai dispositivi StorSimple serie 8000.

CHAP è l'acronimo di Challenge Handshake Authentication Protocol. È uno schema di autenticazione utilizzato dal server per convalidare l'identità dei client remoti. La verifica si basa su una password condivisa o un segreto. CHAP può essere unidirezionale o bidirezionale (reciproco). L'autenticazione CHAP unidirezionale avviene quando la destinazione autentica un iniziatore. Per l'autenticazione CHAP reciproca è necessario che la destinazione autentichi l'iniziatore e viceversa. È possibile implementare l'autenticazione dell'iniziatore senza autenticazione della destinazione. L’autenticazione della destinazione, tuttavia, può essere implementata solo se viene implementata anche l'autenticazione dell'iniziatore.

Come procedura consigliata, si raccomanda l’uso di CHAP per migliorare la sicurezza iSCSI.

> [!NOTE]
> Tenere presente che IPSEC non è attualmente supportato in dispositivi StorSimple.

Le impostazioni CHAP nel dispositivo StorSimple possono essere configurate nei modi seguenti:

* Autenticazione unidirezionale 
* Autenticazione bidirezionale, reciproca o inversa 

In ognuno di questi casi, è necessario configurare il portale del dispositivo e il software dell'iniziatore iSCSI del server. Nell'esercitazione seguente sono descritti i passaggi dettagliati per questa configurazione.

## <a name="unidirectional-or-one-way-authentication"></a>Autenticazione unidirezionale 

Nell’autenticazione unidirezionale, la destinazione autentica l'iniziatore. Questa autenticazione richiede che si configurino le impostazioni  dell’iniziatore CHAP sul dispositivo StorSimple e il software dell’iniziatore iSCSI nell'host. Le procedure dettagliate per il dispositivo StorSimple e l’host di Windows sono descritte di seguito.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Per configurare il dispositivo per l'autenticazione unidirezionale

1. Nel portale di Azure passare al servizio Gestione dispositivi StorSimple. Fare clic su **Dispositivi** e selezionare il dispositivo per il quale configurare il protocollo CHAP. Passare a **Impostazioni dispositivo > Sicurezza**. Nel pannello **Impostazioni di sicurezza** fare clic su **CHAP**.
   
    ![Iniziatore CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Nel pannello **CHAP** e nella sezione **Iniziatore CHAP**:
   
   1. Specificare un nome utente per l'iniziatore CHAP.
   2. Specificare una password per l'iniziatore CHAP.
      
      > [!IMPORTANT]
      > Il nome dell'utente CHAP deve contenere meno di 233 caratteri. La password CHAP deve essere compresa tra 12 e 16 caratteri. Se si prova a usare un nome utente o una password più lunga si verifica un errore di autenticazione sull'host Windows.
   
   3. Confermare la password.

       ![Iniziatore CHAP 2](./media/storsimple-8000-configure-chap/configure-chap6.png)
3. Fare clic su **Salva**. Viene visualizzato un messaggio di conferma. Fare clic su **OK** per salvare le modifiche.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Per configurare l'autenticazione unidirezionale nel server host di Windows
1. Nel server host di Windows, avviare l'iniziatore iSCSI.
2. Nella finestra **Proprietà iniziatore iSCSI** seguire questa procedura:
   
   1. Fare clic sulla scheda **Individuazione**.
      
       ![Proprietà iniziatore iSCSI](./media/storsimple-configure-chap/IC740944.png)
   2. Fare clic su **Individua portale**.
3. Nella finestra di dialogo **Individua portale destinazione** eseguire le operazioni seguenti:
   
   1. Specificare l'indirizzo IP del dispositivo:
   2. Fare clic su **Avanzate**.
      
       ![Individua portale destinazione](./media/storsimple-configure-chap/IC740945.png)
4. Nella finestra di dialogo **Impostazioni avanzate** eseguire le operazioni seguenti:
   
   1. Selezionare la casella di controllo **Abilita accesso CHAP**.
   2. Nel campo **Nome** inserire il nome utente specificato per l'iniziatore CHAP nel portale di Azure.
   3. Nel campo **Segreto destinazione** inserire la password specificata per l'iniziatore CHAP nel portale di Azure.
   4. Fare clic su **OK**.
      
       ![Impostazioni avanzate - Generale](./media/storsimple-configure-chap/IC740946.png)
5. Nella scheda **Destinazioni** della finestra **iSCSI Initiator Properties** (Proprietà iniziatore iSCSI) lo stato del dispositivo deve essere visualizzato come **Connesso**. Se si usa un dispositivo StorSimple 1200, ogni volume viene montato come destinazione iSCSI. Di conseguenza, i passaggi 3 e 4 dovranno essere ripetuti per ogni volume.
   
    ![Volumi montati come destinazioni separate](./media/storsimple-configure-chap/chap4.png)
   
   > [!IMPORTANT]
   > Se si modifica il nome iSCSI, il nuovo nome viene usato per le nuove sessioni iSCSI. Le nuove impostazioni non vengono utilizzate per le sessioni esistenti fino a quando non si esegue la disconnessione e il nuovo accesso.

Per ulteriori informazioni sulla configurazione di CHAP nel server host di Windows, andare a [Ulteriori considerazioni](#additional-considerations).

## <a name="bidirectional-or-mutual-authentication"></a>Autenticazione bidirezionale o reciproca 

Nell'autenticazione bidirezionale la destinazione autentica l'iniziatore e poi l’iniziatore autentica la destinazione. A tal fine è necessario che l'utente configuri le impostazioni dell'iniziatore CHAP e le impostazioni di autenticazione CHAP inversa sul dispositivo e nel software dell'iniziatore iSCSI nell'host. Le procedure seguenti descrivono i passaggi per configurare l'autenticazione reciproca nel dispositivo e nell'host Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Per configurare il dispositivo per l'autenticazione reciproca

1. Nel portale di Azure passare al servizio Gestione dispositivi StorSimple. Fare clic su **Dispositivi** e selezionare il dispositivo per il quale configurare il protocollo CHAP. Passare a **Impostazioni dispositivo > Sicurezza**. Nel pannello **Impostazioni di sicurezza** fare clic su **CHAP**.
   
    ![Destinazione CHAP](./media/storsimple-8000-configure-chap/configure-chap5.png)
2. Scorrere verso il basso nella pagina e nella sezione **Destinazione CHAP** eseguire le operazioni seguenti:
   
   1. Fornire un **nome utente per l’autenticazione CHAP inversa** per il dispositivo.
   2. Fornire una **password per l’autenticazione CHAP inversa** per il dispositivo.
   3. Confermare la password.
3. Nella sezione **Iniziatore CHAP** eseguire le operazioni seguenti:
   
   1. Specificare un **nome utente** per il dispositivo.
   2. Specificare una **password** per il dispositivo.
   3. Confermare la password.

       ![Iniziatore CHAP 3](./media/storsimple-8000-configure-chap/configure-chap11.png)
4. Fare clic su **Salva**. Viene visualizzato un messaggio di conferma. Fare clic su **OK** per salvare le modifiche.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Per configurare l'autenticazione bidirezionale nel server host di Windows

1. Nel server host di Windows, avviare l'iniziatore iSCSI.
2. Nella finestra **Proprietà iniziatore iSCSI** fare clic sulla scheda **Configurazione**.
3. Fare clic su **CHAP**.
4. Nella finestra di dialogo **Segreto autenticazione CHAP reciproca iniziatore iSCSI** eseguire le operazioni seguenti:
   
   1. Immettere la **password dell'autenticazione CHAP inversa** configurata nel portale di Azure.
   2. Fare clic su **OK**.
      
       ![Segreto autenticazione CHAP reciproca iniziatore iSCSI](./media/storsimple-configure-chap/IC740949.png)
5. Fare clic sulla scheda **Destinazioni**.
6. Fare clic sul pulsante **Connetti**. 
7. Nella finestra di dialogo **Connetti a destinazione** fare clic su **Avanzate**.
8. Nella finestra di dialogo **Proprietà avanzate** eseguire le operazioni seguenti:
   
   1. Selezionare la casella di controllo **Abilita accesso CHAP**.
   2. Nel campo **Nome** inserire il nome utente specificato per l'iniziatore CHAP nel portale di Azure.
   3. Nel campo **Segreto destinazione** inserire la password specificata per l'iniziatore CHAP nel portale di Azure.
   4. Selezionare la casella di controllo **Esegui autenticazione reciproca**.
      
       ![Impostazioni avanzate - Autenticazione reciproca](./media/storsimple-configure-chap/IC740950.png)
   5. Fare clic su **OK** per completare la configurazione di CHAP.

Per ulteriori informazioni sulla configurazione di CHAP nel server host di Windows, andare a [Ulteriori considerazioni](#additional-considerations).

## <a name="additional-considerations"></a>Altre considerazioni

La funzionalità **Connessione rapida** non supporta le connessioni con CHAP attivato. Quando CHAP è abilitato, assicurarsi di usare il pulsante **Connetti** disponibile nella scheda **Destinazioni** per la connessione a una destinazione.

![Connessione a destinazione](./media/storsimple-configure-chap/IC740947.png)

Nella finestra di dialogo **Connetti a destinazione** visualizzata selezionare la casella di controllo **Aggiungi connessione all'elenco di destinazioni preferite**. Ciò garantisce che ogni volta che il computer viene riavviato, viene effettuato un tentativo di ripristinare la connessione alle destinazioni preferite iSCSI.

## <a name="errors-during-configuration"></a>Errori durante la configurazione

Se la configurazione CHAP non è corretta, può essere visualizzato un messaggio di errore di autenticazione non riuscita.

## <a name="verification-of-chap-configuration"></a>Verifica della configurazione di CHAP

È possibile verificare che CHAP sia in uso completando i passaggi seguenti.

#### <a name="to-verify-your-chap-configuration"></a>Per verificare la configurazione di CHAP
1. Fare clic sulla scheda **Destinazioni preferite**.
2. Selezionare la destinazione per cui è abilitata l'autenticazione.
3. Fare clic su **Dettagli**.
   
    ![Proprietà iniziatore iSCSI - Destinazioni preferite](./media/storsimple-configure-chap/IC740951.png)
4. Nella finestra di dialogo **Dettagli destinazione preferita** esaminare la voce nel campo **Autenticazione**. Se la configurazione è corretta, nel campo deve essere presente il valore **CHAP**.
   
    ![Dettagli destinazione preferita](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sulla [sicurezza di StorSimple](storsimple-8000-security.md).
* Altre informazioni sull'[uso del servizio Gestione dispositivi StorSimple per amministrare il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

