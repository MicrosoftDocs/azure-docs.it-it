---
title: Protocolli File di Azure disponibili-NFS e SMB
description: Informazioni sui protocolli disponibili prima di creare una condivisione file di Azure, tra cui Server Message Block (SMB) e NFS (Network File System).
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 0ed41cc01fcf5aa35b3d2195518b8e2bb0f3b9c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104588240"
---
# <a name="azure-file-share-protocols"></a>Protocolli di condivisione file di Azure

File di Azure offre due protocolli per la connessione e il montaggio delle condivisioni file di Azure. Protocollo [SMB (Server Message Block](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) ) e [protocollo NFS (Network File System](https://en.wikipedia.org/wiki/Network_File_System) ) (anteprima). File di Azure attualmente non supporta l'accesso a più protocolli, una condivisione può essere solo una condivisione NFS o una condivisione SMB. Per questo motivo, è consigliabile determinare il protocollo più adatto alle proprie esigenze prima di creare condivisioni file di Azure.

## <a name="differences-at-a-glance"></a>Differenze a colpo d'occhio

|Funzionalità  |NFS (anteprima)  |SMB  |
|---------|---------|---------|
|Protocolli di accesso     |NFS 4,1         |SMB 2,1, SMB 3,0         |
|Sistema operativo consigliato     |Kernel Linux versione 4.3 +         |Windows 2008 R2 +, versione kernel Linux 4.11 +         |
|[Livelli disponibili](storage-files-planning.md#storage-tiers)     |Archiviazione Premium         |Archiviazione Premium, ottimizzato per le transazioni, accesso frequente, ad accesso sporadico         |
|Modello di fatturazione         |[Pagamento per la capacità sottoposta a provisioning](./understanding-billing.md#provisioned-model)         |[Pagamento per la capacità con provisioning per il livello Premium](./understanding-billing.md#provisioned-model), con [pagamento in base al consumo per il livello standard](./understanding-billing.md#pay-as-you-go-model)         |
|[Ridondanza](storage-files-planning.md#redundancy)     |LRS, ZRS         |LRS, ZRS, GRS         |
|Authentication     |Solo autenticazione basata su host        |Autenticazione basata sull'identità, autenticazione basata sull'utente         |
|Autorizzazioni     |Autorizzazioni di tipo UNIX         |Autorizzazioni di tipo NTFS         |
|Semantica del file System     |Conforme a POSIX         |Non conforme a POSIX         |
|Maiuscole/minuscole     |Fa distinzione tra maiuscole e minuscole.         |Senza distinzione maiuscole/minuscole         |
|Supporto per collegamenti reali     |Supportato         |Non supportato         |
|Supporto di collegamenti simbolici     |Supportato         |Non supportato         |
|Eliminazione o modifica di file aperti     |Supportato         |Non supportato         |
|Blocco     |Gestione blocco di rete consultivo per l'intervallo di byte         |Supportato         |
|Elenco sicuro IP pubblico | Non supportato | Supportato|
|Interoperabilità del protocollo| Non supportato | Filerest|

## <a name="nfs-shares-preview"></a>Condivisioni NFS (anteprima)

Il montaggio di condivisioni file di Azure con NFS 4,1 è attualmente in fase di anteprima. Offre un'integrazione più stretta con Linux. Si tratta di un'offerta completamente conforme a POSIX che è uno standard tra le varianti di UNIX e altri sistemi operativi basati su * nix. Questo servizio di archiviazione file di livello aziendale è scalabile per soddisfare le esigenze di archiviazione ed è possibile accedervi simultaneamente da migliaia di istanze di calcolo.

### <a name="limitations"></a>Limitazioni

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>Disponibilità a livello di area

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>Più adatto

NFS con File di Azure è ideale per:

- Carichi di lavoro che richiedono condivisioni file conformi a POSIX, distinzione maiuscole/minuscole o autorizzazioni di stile UNIX (UID/GID).
- Carichi di lavoro incentrati su Linux che non richiedono l'accesso a Windows.

### <a name="security"></a>Sicurezza

Tutti i dati File di Azure vengono crittografati a riposo. Per la crittografia in transito, Azure offre un livello di crittografia per tutti i dati in transito tra i Data Center di Azure con [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE). A questo proposito, la crittografia esiste quando i dati vengono trasferiti tra i Data Center di Azure. A differenza File di Azure usando il protocollo SMB, le condivisioni file che usano il protocollo NFS non offrono l'autenticazione basata sull'utente. L'autenticazione per le condivisioni NFS si basa sulle regole di sicurezza di rete configurate. Per questo motivo, per garantire che alla condivisione NFS vengano stabilite solo connessioni sicure, è necessario usare gli endpoint di servizio o gli endpoint privati. Se si desidera accedere alle condivisioni dall'ambiente locale, oltre a un endpoint privato, è necessario configurare una VPN o ExpressRoute. Le richieste che non provengono dalle origini seguenti verranno rifiutate:

- [Un endpoint privato](storage-files-networking-overview.md#private-endpoints)
- [Gateway VPN di Azure](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [VPN da punto a sito (P2S)](../../vpn-gateway/point-to-site-about.md)
    - [Da sito a sito](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [Un endpoint pubblico con restrizioni](storage-files-networking-overview.md#storage-account-firewall-settings)

Per ulteriori informazioni sulle opzioni di rete disponibili, vedere [file di Azure considerazioni sulla rete](storage-files-networking-overview.md).

## <a name="smb-shares"></a>Condivisioni SMB

Le condivisioni file di Azure montate con SMB offrono altre funzionalità di File di Azure e non hanno restrizioni relative alle funzionalità File di Azure perché sono disponibili a livello generale.

### <a name="features"></a>Funzionalità

- Sincronizzazione file di Azure
- Autenticazione basata sull'identità
- Supporto di Backup di Azure
- Snapshot
- Eliminazione temporanea
- Crittografia in transito e crittografia inattiva

### <a name="best-suited"></a>Più adatto

SMB con File di Azure è ideale per:

- Ambienti di produzione
- Clienti che richiedono le funzionalità elencate in [funzionalità](#features)

## <a name="next-steps"></a>Passaggi successivi

- [Creare una condivisione file NFS](storage-files-how-to-create-nfs-shares.md)
- [Creare una condivisione file SMB](storage-how-to-create-file-share.md)