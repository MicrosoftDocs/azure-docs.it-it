---
title: IT Service Management Connector-esportazione sicura in monitoraggio di Azure-configurazione con ServiceNow
description: Questo articolo illustra come connettere i prodotti/servizi ITSM con ServiceNow nell'esportazione sicura in monitoraggio di Azure.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: cf19911bd8126bfb73f848c086aa817a7d7adb00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563692"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Connettere ServiceNow a monitoraggio di Azure

Le sezioni seguenti forniscono informazioni dettagliate su come connettere il prodotto ServiceNow ed esportare in modo sicuro in Azure.

## <a name="prerequisites"></a>Prerequisiti

Assicurarsi di aver soddisfatto i prerequisiti seguenti:

* Azure AD è registrato.
* Si dispone della versione supportata di ServiceNow Event Management-ITOM (versione New York o successiva).
* [Applicazione](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.3.1?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3DEvent%2520Management%2520Connectors&sl=sh) installata nell'istanza di ServiceNow.

## <a name="configure-the-servicenow-connection"></a>Configurare la connessione ServiceNow

1. Usare il collegamento https://(nome istanza). Service-Now. com/API/sn_em_connector/em/inbound_event? source = azuremonitor l'URI per la definizione di esportazione protetta.

2. Seguire le istruzioni in base alla versione:
   * [Parigi](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/concept/azure-integration.html)
