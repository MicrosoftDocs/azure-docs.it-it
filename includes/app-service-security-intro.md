---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/15/2020
ms.author: cephalin
ms.openlocfilehash: 0fccb1ed092190f88d197c9bf2cd17678a4d1731
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649043"
---
Ai componenti della piattaforma di Servizio app di Azure, incluse le macchine virtuali di Azure, l'archiviazione, le connessioni di rete, i framework Web e le funzionalità di gestione e integrazione, viene applicata attivamente una protezione avanzata. Servizio app di Azure viene sottoposto continuamente a rigorosi controlli della conformità per assicurarsi che:

- Le risorse dell'app siano [protette](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) dalle risorse di Azure degli altri clienti.
- [Le istanze di macchine virtuali e il software di runtime vengano regolarmente aggiornati](../articles/app-service/overview-patch-os-runtime.md) in modo da contrastare le vulnerabilità appena individuate. 
- La comunicazione di segreti (ad esempio stringhe di connessione) tra l'app e altre risorse di Azure (ad esempio [database SQL](https://azure.microsoft.com/services/sql-database/)) rimanga all'interno di Azure e non superi i confini di rete. I segreti siano sempre crittografati quando vengono archiviati.
- Tutte le comunicazioni tramite le funzionalità di connettività di Servizio app di Azure, ad esempio la [connessione ibrida](../articles/app-service/app-service-hybrid-connections.md), siano crittografate. 
- Tutte le connessioni con strumenti di gestione remota, come Azure PowerShell, l'interfaccia della riga di comando di Azure, Azure SDK e le API REST, siano crittografate.
- La gestione delle minacce 24 ore su 24 protegga l'infrastruttura e la piattaforma da malware, attacchi Distributed Denial of Service (DDoS), attacchi man-in-the-middle (MITM) e altre minacce.

Per altre informazioni sulla sicurezza della piattaforma e dell'infrastruttura in Azure, vedere [Centro protezione di Azure](https://azure.microsoft.com/overview/trusted-cloud/).