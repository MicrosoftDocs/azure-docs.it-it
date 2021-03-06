---
title: Connettersi a una macchina virtuale Windows Server
description: Informazioni su come connettersi e accedere a una macchina virtuale Windows tramite il portale di Azure e il modello di distribuzione di Resource Manager.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/26/2018
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 007719469eb19809d9e64bccfef9589e1fe491f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607314"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows
Per avviare una sessione di Desktop remoto (RDP) da un desktop di Windows, sarà necessario usare il pulsante **Connetti** nel portale di Azure. Connettersi prima di tutto alla macchina virtuale e quindi eseguire l'accesso.

Per connettersi a una macchina virtuale Windows da un Mac, sarà necessario installare un client RDP per Mac come [Desktop remoto Microsoft](https://aka.ms/rdmac).

## <a name="connect-to-the-virtual-machine"></a>Connettersi alla macchina virtuale
1. Passare al [portale di Azure](https://portal.azure.com/) per connettersi a una macchina virtuale. Cercare e selezionare **Macchine virtuali**.
2. Selezionare la macchina virtuale dall'elenco.
3. Selezionare **Connetti** all'inizio della pagina della macchina virtuale.
4. Nella pagina **Connetti a macchina virtuale** selezionare **RDP** e quindi l'**Indirizzo IP** e il **Numero porta** appropriati. Nella maggior parte dei casi, è consigliabile usare l'indirizzo IP e la porta predefiniti. Selezionare **Scarica file RDP**. Se per la macchina virtuale è impostato un criterio JIT, selezionare prima il pulsante **Richiedi accesso** per richiedere l'accesso e poter quindi scaricare il file RDP. Per altre informazioni sui criteri JIT, vedere [Gestire l'accesso alle macchine virtuali con la funzionalità JIT (Just-in-Time)](../../security-center/security-center-just-in-time.md).
5. Aprire il file RDP scaricato e selezionare **Connetti** quando richiesto. Verrà visualizzato un avviso per segnalare che l'autore del file `.rdp` è sconosciuto. Si tratta di un comportamento previsto. Nella finestra **Connessione Desktop remoto** selezionare **Connetti** per continuare.
   
    ![Screenshot di un avviso relativo a un autore sconosciuto.](./media/connect-logon/rdp-warn.png)
3. Nella finestra **Sicurezza di Windows** selezionare **Altre opzioni** e quindi **Usa un altro account**. Immettere le credenziali per un account nella macchina virtuale e quindi selezionare **OK**.
   
     **Account locale**: corrisponde in genere al nome utente e alla password dell'account locale specificati al momento della creazione della macchina virtuale. In questo caso, il dominio è il nome della macchina virtuale e viene immesso come *nomevm*&#92;*nomeutente*.  
   
    **Macchina virtuale aggiunta a un dominio**: se la macchina virtuale appartiene a un dominio, immettere il nome utente nel formato *Dominio*&#92;*Nomeutente*. L'account deve anche appartenere al gruppo degli amministratori o deve possedere privilegi di accesso remoto alla VM.
   
    **Controller di dominio**: se la macchina virtuale è un controller di dominio, immettere nome utente e password di un account amministratore di dominio per tale dominio.
4. Selezionare **Sì** per verificare l'identità della macchina virtuale e terminare la procedura di accesso.
   
   ![Screenshot che visualizza un messaggio sulla verifica dell'identità della VM.](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > Se il pulsante **Connetti** nel portale è disattivato e non si è connessi ad Azure tramite una connessione [Express Route](../../expressroute/expressroute-introduction.md) o [VPN da sito a sito](../../vpn-gateway/tutorial-site-to-site-portal.md), per poter usare il protocollo RDP sarà necessario creare un indirizzo IP pubblico e assegnarlo alla macchina virtuale. Per altre informazioni, vedere [Indirizzi IP pubblici in Azure](../../virtual-network/public-ip-addresses.md).
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>Connettersi alla macchina virtuale tramite PowerShell

 

Se si usa PowerShell e il modulo Azure PowerShell è installato, è anche possibile connettersi usando il cmdlet `Get-AzRemoteDesktopFile`, come illustrato di seguito.

In questo esempio verrà avviata immediatamente la connessione RDP, con prompt simili ai precedenti.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

È anche possibile salvare il file RDP per un uso futuro.

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>Passaggi successivi
Se si ha difficoltà con la connessione, vedere [Risolvere i problemi relativi alle connessioni Desktop remoto](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).