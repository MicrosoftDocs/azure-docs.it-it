---
title: Configurare indirizzi IP privati per le VM (classiche) - Portale di Azure | Documentazione Microsoft
description: Informazioni su come configurare indirizzi IP privati per macchine virtuali (classiche) mediante il portale di Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6986f6f16cbd32d44223bba4f4be4577fa11258c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222905"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Configurare indirizzi IP privati per una macchina virtuale (classica) mediante il portale di Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

In questo articolo viene illustrato il modello di distribuzione classica. È inoltre possibile [gestire un indirizzo IP statico privato nel modello di distribuzione di gestione delle risorse](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

La procedura di esempio seguente prevede che un ambiente semplice sia già creato. Se si desidera eseguire la procedura illustrata in questo documento, creare innanzitutto l'ambiente di prova descritto in [creare una rete virtuale](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Come specificare un indirizzo IP statico privato durante la creazione di una macchina virtuale
Per creare una VM denominata *DNS01* nella subnet *FrontEnd* di una rete virtuale denominata *TestVNet* con un indirizzo IP statico privato di *192.168.1.101*, seguire la procedura seguente:

1. In un browser passare a https://portal.azure.com e, se necessario, accedere con l'account Azure.
2. Selezionare **nuovo**  >  **calcolo**  >  **Windows Server 2012 R2 Datacenter**. si noti che l' **elenco selezionare un modello di distribuzione** Mostra già **classico**, quindi selezionare **Crea**.
   
    ![Screenshot che mostra il portale di Azure con il nuovo riquadro > calcolo > Windows Server 2012 R2 Datacenter evidenziato.](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. In **Crea macchina virtuale** immettere il nome della macchina virtuale da creare (*DNS01* nello scenario), l'account amministratore locale e la password.
   
    ![Screenshot che illustra come creare una macchina virtuale immettendo il nome della macchina virtuale, il nome utente dell'amministratore locale e la password.](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Selezionare **configurazione facoltativa**  >    >  **rete rete virtuale** e quindi selezionare **TestVNet**. Se **TestVNet** non è disponibile, verificare che si utilizzi la posizione *Stati Uniti centrali* e di aver creato l'ambiente di prova descritto all'inizio di questo articolo.
   
    ![Screenshot che mostra l'opzione di configurazione facoltativa > rete > rete virtuale > TestVNet evidenziata.](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. In **Rete** assicurarsi che la subnet attualmente selezionata sia *FrontEnd*, selezionare **Indirizzi IP**, in **Assegnazione indirizzi IP** selezionare **Statico** e quindi immettere *192.168.1.101* per **Indirizzo IP** come indicato di seguito.
   
    ![Schermata che evidenzia il campo indirizzi IP in cui si digita l'indirizzo IP statico.](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Selezionare **OK** in **Indirizzi IP**, selezionare **OK** in **Rete** e quindi selezionare **OK** in **Configurazione facoltativa**.
7. In **Crea macchina virtuale** selezionare **Crea**. Si noti il riquadro visualizzato di seguito nel dashboard:
   
    ![Screenshot che illustra il riquadro creazione di Windows Server 2012 R2 Datacenter.](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Come recuperare le informazioni relative all'indirizzo IP privato statico per una VM
Per visualizzare le informazioni dell’indirizzo IP privato statico per la macchina virtuale creata con la procedura descritta sopra, eseguire la procedura seguente.

1. Dal portale di Azure selezionare **Esplora tutto**  >  **macchine virtuali (versione classica)**  >  **DNS01**  >  **tutte le impostazioni**  >  **indirizzi** IP e notare l'assegnazione di indirizzi IP e l'indirizzo IP come illustrato di seguito.
   
    ![Creare VM nel portale di Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Come rimuovere un indirizzo IP statico privato da una macchina virtuale

In **Indirizzi IP** selezionare **Dinamica** a destra di **Assegnazione indirizzo IP**, selezionare **Salva** e quindi selezionare **Sì**, come illustrato nell'immagine seguente:
   
![Screenshot che illustra come rimuovere l'indirizzo IP statico privato da una macchina virtuale selezionando dinamico a destra dell'etichetta assegnazione indirizzi IP.](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Come aggiungere un indirizzo IP statico privato a una macchina virtuale esistente

1. In **Indirizzi IP**, come mostrato in precedenza, selezionare **Statica** a destra di **Assegnazione indirizzi IP**.
2. Digitare *192.168.1.101* per **Indirizzo IP**, selezionare **Salva** e quindi selezionare **Sì**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Impostare gli indirizzi IP all'interno del sistema operativo

È consigliabile non assegnare staticamente l'indirizzo IP privato assegnato alla macchina virtuale di Azure all'interno del sistema operativo di una macchina virtuale, se non necessario. Se si imposta manualmente l'indirizzo IP privato all'interno del sistema operativo, assicurarsi che sia uguale all'indirizzo IP privato assegnato alla macchina virtuale di Azure. In caso contrario, si può perdere la connettività alla macchina virtuale. Non assegnare mai manualmente l'indirizzo IP pubblico assegnato a una macchina virtuale di Azure all'interno del sistema operativo della macchina virtuale.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni su [indirizzi IP pubblici riservati](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) .
* Informazioni su [indirizzi IP pubblici a livello di istanza (ILPIP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) .
* Consultare le [API REST dell'indirizzo IP riservato](/previous-versions/azure/reference/dn722420(v=azure.100)).