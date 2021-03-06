---
title: Incorporare i team del Framework dell'interfaccia utente
titleSuffix: An Azure Communication Services quickstart
description: In questo documento si apprenderà come i team del Framework dell'interfaccia utente di servizi di comunicazione di Azure incorporano funzionalità che possono essere usate per creare esperienze di chiamata chiavi in mano.
author: tophpalmer
ms.author: chpalm
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 8301f81b21db50814df1bc764cc1fae38b4f14de
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307640"
---
# <a name="teams-embed"></a>Incorporare i team

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]


I team incorporati sono una funzionalità di servizi di comunicazione di Azure incentrata sulle interazioni comuni tra le chiamate business-to-consumer e business-to-business. Il nucleo del sistema di incorporamento dei team è la [chiamata a video e voce](../voice-video-calling/calling-sdk-features.md), ma i team incorporano il sistema in base alle primitive chiamanti di Azure per offrire un'esperienza utente completa basata sulle riunioni dei team Microsoft.

I team incorporano gli SDK sono closed source e rendono disponibili tali funzionalità in un formato composito e chiavi in mano. È possibile eliminare i team incorporati nell'area di disegno dell'app e l'SDK genera un'esperienza utente completa. Poiché questa esperienza utente è molto simile alle riunioni dei team Microsoft, è possibile sfruttare i vantaggi offerti da:

- Riduzione del tempo di sviluppo e della complessità della progettazione
- Familiarità con l'utente finale con i team
- Possibilità di riutilizzare il [contenuto della formazione per gli utenti finali del team](https://support.microsoft.com/office/meetings-in-teams-e0b0ae21-53ee-4462-a50d-ca9b9e217b67)

L'incorporamento dei team fornisce la maggior parte delle funzionalità supportate nelle riunioni dei team, tra cui:

- Esperienza di pre-riunione in cui un utente configura i dispositivi audio e video
- Esperienza di riunione per la configurazione di dispositivi audio e video
- [Background video](https://support.microsoft.com/office/change-your-background-for-a-teams-meeting-f77a2381-443a-499d-825e-509a140f4780): consentire ai partecipanti di sfocare o sostituire i loro background
- [Opzioni multiple per la raccolta video di](https://support.microsoft.com/office/using-video-in-microsoft-teams-3647fc29-7b92-4c26-8c2d-8a596904cdae) grandi dimensioni, modalità insieme, stato attivo, blocco e Spotlight
- [Condivisione di contenuto](https://support.microsoft.com/en-us/office/share-content-in-a-meeting-in-teams-fcc2bf59-aecd-4481-8f99-ce55dd836ce8): consentire ai partecipanti di condividere lo schermo

Per altre informazioni su questa interfaccia utente rispetto ad altri SDK per la comunicazione di Azure, vedere l' [Introduzione al concetto di SDK dell'interfaccia utente](ui-sdk-overview.md). 
