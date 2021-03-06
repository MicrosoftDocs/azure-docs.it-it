---
title: Autenticare il recapito di eventi ai gestori eventi (Griglia di eventi di Azure)
description: Questo articolo descrive diversi modi per autenticare il recapito ai gestori eventi in Griglia di eventi di Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 7db258ee152e4b1c46362e74e0246b80513ca9f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777258"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Autenticare il recapito di eventi ai gestori eventi (Griglia di eventi di Azure)
Questo articolo fornisce informazioni sull'autenticazione del recapito degli eventi ai gestori eventi. Illustra anche come proteggere gli endpoint del webhook usati per ricevere eventi da Griglia di eventi usando Azure Active Directory (Azure AD) o un segreto condiviso.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Usare le identità assegnate dal sistema per il recapito degli eventi
È possibile abilitare un'identità gestita assegnata dal sistema per un argomento o un dominio e usare l'identità per inoltrare eventi a destinazioni supportate, ad esempio code e argomenti del bus di servizio, hub eventi e account di archiviazione.

Di seguito sono riportati i passaggi necessari: 

1. Creare un argomento o un dominio con un'identità assegnata dal sistema o aggiornare un argomento o un dominio esistente per abilitare l'identità. 
1. Aggiungere l'identità a un ruolo appropriato, ad esempio Mittente dati del bus di servizio, nella destinazione, ad esempio una coda del bus di servizio.
1. Quando si creano sottoscrizioni di eventi, abilitare l'utilizzo dell'identità per recapitare gli eventi alla destinazione. 

Per istruzioni dettagliate, vedere Recapito di eventi [con un'identità gestita.](managed-service-identity.md)


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Autenticare il recapito di eventi agli endpoint webhook
Le sezioni seguenti descrivono come autenticare il recapito di eventi agli endpoint webhook. È necessario usare un meccanismo di handshake di convalida indipendentemente dal metodo usato. Per informazioni dettagliate, vedere [Recapito eventi webhook](webhook-event-delivery.md). 


### <a name="using-azure-active-directory-azure-ad"></a>Uso di Azure Active Directory (Azure AD)
È possibile proteggere l'endpoint del webhook usato per ricevere eventi da Griglia di eventi usando Azure AD. È necessario creare un'applicazione Azure AD, creare un ruolo e un'entità servizio nell'applicazione che autorizza Griglia di eventi e configurare la sottoscrizione dell'evento per usare l'applicazione Azure AD. Informazioni su come [configurare Azure Active Directory con Griglia di eventi](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Uso del segreto client come parametro di query
È possibile proteggere l'endpoint webhook aggiungendo i parametri di query all'URL di destinazione del webhook indicato come parte della creazione di una sottoscrizione di eventi. Impostare uno dei parametri di query in modo che sia un segreto client, ad esempio un [token di accesso](https://en.wikipedia.org/wiki/Access_token) o un segreto condiviso. Il servizio Griglia di eventi includerà tutti questi parametri di query in ogni richiesta di recapito di eventi al webhook. Il servizio webhook può recuperare e convalidare il segreto. Se il segreto client viene aggiornato, è necessario aggiornare anche la sottoscrizione dell'evento. Per evitare errori di recapito durante questa rotazione del segreto, fare in modo che il webhook accetti sia i segreti vecchi che quelli nuovi per un periodo limitato prima di aggiornare la sottoscrizione con il nuovo segreto. 

Poiché i parametri di query potrebbero contenere segreti client, vengono gestiti con maggiore attenzione. Vengono archiviati come crittografati e non sono accessibili agli operatori del servizio. Non vengono registrati in log/tracce del servizio. Quando si recuperano le proprietà della sottoscrizione di eventi, i parametri delle query di destinazione non vengono restituiti per impostazione predefinita. Ad esempio, il parametro [--include-full-endpoint-URL](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_show) deve essere usato nell'[interfaccia della riga di comando](/cli/azure) di Azure.

Per altre informazioni su come recapitare gli eventi ai webhook, vedere [Recapito eventi webhook](webhook-event-delivery.md).

> [!IMPORTANT]
> Griglia di eventi di Azure supporta solo endpoint webhook **HTTPS**. 

## <a name="endpoint-validation-with-cloudevents-v10"></a>Convalida degli endpoint con CloudEvents v1.0
Se si ha già familiarità con Griglia di eventi, è possibile conoscere l'handshake di convalida dell'endpoint per evitare abusi. CloudEvents v1.0 implementa la propria [semantica di](webhook-event-delivery.md) protezione dagli abusi usando il **metodo HTTP OPTIONS.** Per altre informazioni, vedere Web Hook HTTP 1.1 per il recapito degli eventi [- Versione 1.0.](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) Quando si usa lo schema CloudEvents per l'output, Griglia di eventi usa la protezione dall'uso improprio di CloudEvents v1.0 al posto del meccanismo degli eventi di convalida di Griglia di eventi. Per altre informazioni, vedere [Usare lo schema CloudEvents v1.0 con Griglia di eventi](cloudevents-schema.md). 


## <a name="next-steps"></a>Passaggi successivi
Vedere [Autenticare i client di pubblicazione](security-authenticate-publishing-clients.md) per informazioni sull'autenticazione dei client che pubblicano eventi in argomenti o domini. 
