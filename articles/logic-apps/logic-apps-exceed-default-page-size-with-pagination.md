---
title: Ottenere più elementi o record con la paginazione
description: Configurare la paginazione per superare il limite di dimensioni di pagina predefinito per le azioni del connettore nelle App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: d46bf711a46e27b81a1284b5fc55cf403b7da048
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87090264"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Ottenere più dati, elementi o record usando la paginazione in App per la logica di Azure

Quando si recuperano dati, elementi o record usando un'azione del connettore in [App per la logica di Azure](../logic-apps/logic-apps-overview.md), è possibile ottenere set di risultati talmente ampio che l'azione non restituisce tutti i risultati nello stesso momento. Con alcune azioni, il numero di risultati può superare le dimensioni di pagina predefinite del connettore. In questo caso, l'azione restituisce solo la prima pagina di risultati. Ad esempio, le dimensioni di pagina predefinite per l'azione **Ottieni righe** del connettore di SQL Server sono 2048, ma potrebbero essere diverse in base ad altre impostazioni.

Alcune azioni consentono di attivare un'impostazione di *paginazione* in modo che l'app per la logica possa recuperare più risultati fino al limite di paginazione come un unico messaggio al termine dell'azione. Quando si usa la paginazione, è necessario specificare un valore *soglia*, ovvero il numero di destinazione dei risultati che si vuole che l'azione restituisca. L'azione recupera i risultati fino a raggiungere la soglia specificata. Quando il numero totale di elementi è inferiore alla soglia specificata, l'azione recupera tutti i risultati.

Quando si attiva l'impostazione di paginazione, vengono recuperate le pagine dei risultati in base alle dimensioni di pagina di un connettore. Questo comportamento significa che talvolta è possibile ottenere più risultati rispetto alla soglia specificata. Ad esempio, quando si usa l'azione SQL Server **Ottieni righe**, che supporta l'impostazione di paginazione:

* Le dimensioni di pagina predefinite dell'azione sono pari a 2048 record per pagina.
* Si supponga di avere 10.000 record e di specificare come minimo 5000 record.
* La paginazione ottiene pagine di record, quindi per ottenere almeno il valore minimo specificato l'azione restituisce 6144 record (3 pagine x 2048 record), non 5000.

Di seguito viene fornito un elenco con solo alcuni dei connettori in cui è possibile superare le dimensioni di pagina predefinite per azioni specifiche:

* [Archiviazione BLOB di Azure](/connectors/azureblob/)
* [Dynamics 365](/connectors/dynamicscrmonline/)
* [Excel](/connectors/excel/)
* [HTTP](../connectors/connectors-native-http.md)
* [IBM DB2](/connectors/db2/)
* [Microsoft Teams](/connectors/teams/)
* [Database Oracle](/connectors/oracle/)
* [Salesforce](/connectors/salesforce/)
* [SharePoint](/connectors/sharepointonline/)
* [SQL Server](/connectors/sql/)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* L'app per la logica e l'azione in cui si desidera attivare la paginazione. Se non si dispone di un'app per la logica, vedere [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Attivare la paginazione

Per determinare se un'azione supporta la paginazione nella finestra di progettazione dell'app per la logica, controllare le impostazioni dell'azione per l'impostazione **Paginazione**. Questo esempio illustra come attivare la paginazione nell'azione **Ottieni righe** di SQL Server.

1. Nell'angolo superiore destro dell'azione scegliere il pulsante con puntini di sospensione ( **...** ), quindi scegliere **Impostazioni**.

   ![Aprire le impostazioni per l'azione](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Se l'azione supporta la paginazione, mostrerà l'impostazione **Paginazione**.

1. Modificare l'impostazione **Paginazione** da **Off** a **On**. Nella proprietà **Soglia** specificare un valore intero per il numero di risultati di destinazione che si desidera venga restituito dall'azione.

   ![Specificare il numero massimo di risultati da restituire](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Al termine, scegliere **Fine**.

## <a name="workflow-definition---pagination"></a>Definizione del flusso di lavoro: paginazione

Quando si attiva la paginazione per un'azione che supporta questa funzionalità, la definizione del flusso di lavoro dell'app per la logica include la proprietà `"paginationPolicy"` insieme alla proprietà `"minimumItemCount"` nella proprietà `"runtimeConfiguration"` di tale azione, ad esempio:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Supporto

Per eventuali domande, visitare la [pagina Microsoft delle domande e risposte per App per la logica di Azure](/answers/topics/azure-logic-apps.html).
