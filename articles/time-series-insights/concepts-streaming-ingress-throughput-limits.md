---
title: Limitazioni della velocità effettiva di inserimento di flussi-Azure Time Series Insights Gen2 | Microsoft Docs
description: Informazioni sui limiti della velocità effettiva in ingresso in Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: d86cc6af34036f5bd638b4fc78abdb54d71e6859
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306518"
---
# <a name="streaming-ingestion-throughput-limits"></a>Limiti della velocità effettiva di inserimento dei flussi

Di seguito sono riportate le limitazioni di ingresso dei dati in streaming Gen2 Azure Time Series Insights.

> [!TIP]
> Per un elenco completo di tutti i limiti, vedere [pianificare l'ambiente Gen2 Azure Time Series Insights](./how-to-plan-your-environment.md#review-azure-time-series-insights-gen2-limits) .

## <a name="per-environment-limitations"></a>Limitazioni per ambiente

In generale, la velocità di ingresso viene visualizzata come il fattore che determina il numero di dispositivi all'interno dell'organizzazione, la frequenza di emissione degli eventi e le dimensioni di ogni evento:

* **Numero di dispositivi** × **Frequenza di emissione degli eventi** × **Dimensioni di ogni evento**.

Per impostazione predefinita, Azure Time Series Insights Gen2 è in grado di inserire i dati in ingresso a una velocità di un **massimo di 1 megabyte al secondo (Mbps) per Azure Time Series Insights ambiente Gen2**. [Per ogni partizione dell'hub](./concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits) ci sono altre limitazioni.

> [!TIP]
>
> * Il supporto dell'ambiente per l'inserimento di velocità fino a 2 MBps può essere fornito tramite richiesta.
> * Contattaci se è necessaria una velocità effettiva maggiore inviando un ticket di supporto tramite il portale di Azure.

* **Esempio 1:**

    In Contoso Shipping ci sono 100.000 dispositivi che emettono un evento tre volte al minuto. Le dimensioni di un evento sono pari a 200 byte. Usano un hub Internet delle cose con quattro partizioni come Azure Time Series Insights origine evento Gen2.

  * La velocità di inserimento per l'ambiente Gen2 Azure Time Series Insights è: **100.000 dispositivi * 200 byte/evento * (3/60 evento/sec) = 1 Mbps**.
    * Supponendo che le partizioni bilanciate, la velocità di inserimento per partizione è 0,25 MBps.
    * Il tasso di inserimento di Contoso Shipping sarà entro i limiti di scalabilità.

* **Esempio 2:**

    Contoso Fleet Analytics ha 10.000 dispositivi che emettono un evento ogni secondo. Usano un hub eventi con un numero di partizioni pari a 2 come Azure Time Series Insights origine evento Gen2. Le dimensioni di un evento sono pari a 200 byte.

  * La velocità di inserimento dell'ambiente è: **10.000 dispositivi * 200 byte/evento * 1 evento/sec = 2 Mbps**.
    * Supponendo che le partizioni bilanciate, il relativo tasso per partizione sarebbe 1 MBps.
    * La velocità di inserimento di Contoso Fleet Analytics supera i limiti dell'ambiente e della partizione. Possono inviare una richiesta di Azure Time Series Insights Gen2 tramite il portale di Azure per aumentare la velocità di inserimento per il proprio ambiente e creare un hub eventi con più partizioni entro i limiti.

## <a name="hub-partitions-and-per-partition-limits"></a>Partizioni dell'hub e limiti per partizione

Quando si pianifica la Azure Time Series Insights ambiente Gen2, è importante prendere in considerazione la configurazione delle origini eventi a cui ci si connetterà Azure Time Series Insights Gen2. Sia l'hub IoT che l'hub eventi di Azure usano le partizioni per abilitare la scalabilità orizzontale per l'elaborazione degli eventi.

Una *partizione* è una sequenza ordinata di eventi contenuta in un hub. Il numero di partizioni viene impostato durante la fase di creazione dell'hub e non può essere modificato.

Per le procedure consigliate di partizionamento degli hub eventi, vedere [Quante partizioni sono necessarie?](../event-hubs/event-hubs-faq.yml#how-many-partitions-do-i-need-)

> [!NOTE]
> Per la maggior parte degli hub Internet usati con Azure Time Series Insights Gen2 sono necessarie solo quattro partizioni.

Sia che si stia creando un nuovo hub per l'ambiente Gen2 Azure Time Series Insights o che ne venga usato uno esistente, è necessario calcolare la velocità di inserimento per partizione per determinare se è entro i limiti.

Azure Time Series Insights Gen2 dispone attualmente di un **limite generale per partizione di 0,5 Mbps**.

### <a name="iot-hub-specific-considerations"></a>Considerazioni specifiche sull'hub IoT

Quando un dispositivo viene creato nell'hub IoT, viene assegnato in modo permanente a una partizione. In questo modo, l'hub IoT è in grado di garantire l'ordine degli eventi, poiché l'assegnazione non cambia mai.

Un'assegnazione di partizione fissa influisca anche Azure Time Series Insights istanze di Gen2 che inseriscono i dati inviati dall'hub Internet a valle. Quando i messaggi provenienti da più dispositivi vengono inoltrati all'hub con lo stesso ID dispositivo del gateway, possono arrivare nella stessa partizione contemporaneamente e possono superare potenzialmente i limiti di scalabilità per partizione.

**Impatto**:

* Se una singola partizione ha una velocità sostenuta di inserimento oltre il limite, è possibile che Azure Time Series Insights Gen2 non sincronizza tutti i dati di telemetria dei dispositivi prima del superamento del periodo di conservazione dei dati dell'hub. Di conseguenza, i dati inviati possono andare perduti se i limiti di inserimento vengono superati in modo costante.

Per attenuare questa circostanza, è consigliabile attenersi alle procedure consigliate seguenti:

* Calcolare le velocità di inserimento per ambiente e per partizione prima di distribuire la soluzione.
* Verificare che il carico dei dispositivi dell'hub IoT sia bilanciato il più possibile.

> [!IMPORTANT]
> Per gli ambienti che usano l'hub Internet come origine eventi, calcolare la velocità di inserimento usando il numero di dispositivi hub usati per assicurarsi che la frequenza scende al di sotto del limite di 0,5 MBps per partizione.
>
> * Anche se più eventi arrivano simultaneamente, il limite non verrà superato.

  ![Diagramma delle partizioni dell'hub IoT](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Per altre informazioni sull'ottimizzazione della velocità effettiva e delle partizioni dell'hub, vedere le risorse seguenti:

* [Scalabilità dell'hub IoT](../iot-hub/iot-hub-scaling.md)
* [Scalabilità dell'hub eventi](../event-hubs/event-hubs-scalability.md#throughput-units)
* [Partizioni dell'hub eventi](../event-hubs/event-hubs-features.md#partitions)

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più sull' [archiviazione](./concepts-storage.md) dei dati
