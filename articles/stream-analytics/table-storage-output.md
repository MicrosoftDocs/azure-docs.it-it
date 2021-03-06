---
title: Output dell'archiviazione tabelle da analisi di flusso di Azure
description: Questo articolo descrive l'archiviazione tabelle di Azure come output per analisi di flusso di Azure.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 558ddf065d33a552034c5b129ea70bc144c494ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013890"
---
# <a name="table-storage-output-from-azure-stream-analytics"></a>Output dell'archiviazione tabelle da analisi di flusso di Azure

[archiviazione tabelle di Azure](../storage/common/storage-introduction.md) offre un archivio a disponibilità elevata e altamente scalabile, per consentire la scalabilità automatica di un'applicazione in base alle richieste degli utenti. Archiviazione tabelle è l'archivio di chiavi/attributi NoSQL di Microsoft che è possibile usare per i dati strutturati con meno vincoli allo schema. Archivio tabelle di Azure consente di archiviare i dati per il salvataggio permanente e il recupero efficiente.

La tabella seguente elenca i nomi delle proprietà e la relativa descrizione per la creazione di un output della tabella.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output |Nome descrittivo usato nelle query per indirizzare l'output delle query a questa archiviazione tabelle. |
| Account di archiviazione |Nome dell'account di archiviazione a cui si sta inviando l'output. |
| Chiave dell'account di archiviazione |Chiave di accesso associata all'account di archiviazione. |
| Nome tabella |Nome della tabella. Se non esiste, viene creata una nuova tabella. |
| Chiave di partizione |Nome della colonna di output contenente la chiave di partizione. La chiave di partizione è un identificatore univoco per la partizione di una tabella che costituisce la prima parte della chiave primaria di un'entità. Si tratta di un valore stringa le cui dimensioni massime sono di 1 KB. |
| Chiave di riga |Nome della colonna di output che contiene la chiave di riga. La chiave di riga è un identificatore univoco per un'entità all'interno di una partizione. Costituisce la seconda parte della chiave primaria di un'entità. La chiave di riga è un valore stringa le cui dimensioni massime sono di 1 KB. |
| Dimensioni dei batch |Numero di record per un'operazione batch. Il valore predefinito (100) è sufficiente per la maggior parte dei processi. Per altre informazioni sulla modifica di questa impostazione, vedere la [specifica relativa alle operazioni batch su tabella](/java/api/com.microsoft.azure.storage.table.tablebatchoperation). |

## <a name="partitioning"></a>Partizionamento

La chiave di partizione è qualsiasi colonna di output. Il numero di writer di output segue il partizionamento dell'input per le [query completamente parallele](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Dimensione del batch di output

Per la dimensione massima dei messaggi, vedere [limiti di archiviazione di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Il valore predefinito è 100 entità per singola transazione, ma è possibile configurarlo su un valore inferiore in base alle esigenze.

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con l'interfaccia della riga di comando di Azure](quick-create-azure-cli.md)
* [Avvio rapido: Creare un processo di Analisi di flusso di Azure con un modello di Resource Manager](quick-create-azure-resource-manager.md)
* [Guida introduttiva: creare un processo di analisi di flusso usando Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Guida introduttiva: Creare un processo di Analisi di flusso di Azure con Visual Studio Code](stream-analytics-quick-create-vs.md)
* [Guida introduttiva: creare un processo di analisi di flusso di Azure in Visual Studio Code](quick-create-visual-studio-code.md)