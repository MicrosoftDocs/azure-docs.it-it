---
title: Notifiche degli eventi
titleSuffix: Azure Digital Twins
description: Vedere come interpretare diversi tipi di evento e i relativi messaggi di notifica.
author: baanders
ms.author: baanders
ms.date: 4/8/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q4
ms.openlocfilehash: 42842b00120b7e918ca5b790cce92a74ab1b99d5
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259987"
---
# <a name="event-notifications"></a>Notifiche degli eventi

Eventi diversi nei dispositivi gemelli digitali di Azure producono **notifiche** che consentono al back-end della soluzione di essere informati quando si verificano azioni diverse. Questi vengono quindi [indirizzati](concepts-route-events.md) a posizioni diverse all'interno e all'esterno dei dispositivi gemelli digitali di Azure che possono usare queste informazioni per intervenire.

Esistono diversi tipi di notifiche che possono essere generate e i messaggi di notifica possono avere un aspetto diverso a seconda del tipo di evento che li ha generati. In questo articolo vengono fornite informazioni dettagliate sui diversi tipi di messaggi e sull'aspetto che potrebbero essere simili.

Questo grafico mostra i diversi tipi di notifica:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="notification-structure"></a>Struttura di notifica

In generale, le notifiche sono costituite da due parti: l'intestazione e il corpo. 

### <a name="event-notification-headers"></a>Intestazioni delle notifiche degli eventi

Le intestazioni dei messaggi di notifica sono rappresentate da coppie chiave-valore. A seconda del protocollo utilizzato (MQTT, AMQP o HTTP), le intestazioni dei messaggi verranno serializzate in modo diverso. In questa sezione vengono illustrate le informazioni generali sull'intestazione per i messaggi di notifica, indipendentemente dal protocollo specifico e dalla serializzazione scelta.

Alcune notifiche sono conformi allo standard [CloudEvents](https://cloudevents.io/) . La conformità di CloudEvents è la seguente.
* Le notifiche emesse dai dispositivi continuano a seguire le specifiche esistenti per le notifiche
* Le notifiche elaborate e emesse dall'hub Internet continuano a seguire le specifiche esistenti per la notifica, tranne nel caso in cui l'hub Internet sceglie di supportare CloudEvents, ad esempio tramite griglia di eventi.
* Notifiche emesse dai dispositivi [gemelli digitali](concepts-twins-graph.md) con un [modello](concepts-models.md) conforme a CloudEvents
* Le notifiche elaborate e emesse dai dispositivi gemelli digitali di Azure sono conformi a CloudEvents

I servizi devono aggiungere un numero di sequenza per tutte le notifiche per indicare l'ordine o mantenere il proprio ordinamento in altro modo. 

Le notifiche emesse da dispositivi gemelli digitali di Azure a griglia di eventi verranno automaticamente formattate nello schema CloudEvents o EventGridEvent, a seconda del tipo di schema definito nell'argomento griglia di eventi. 

Gli attributi di estensione sulle intestazioni verranno aggiunti come proprietà nello schema di griglia di eventi all'interno del payload. 

### <a name="event-notification-bodies"></a>Corpi delle notifiche degli eventi

I corpi dei messaggi di notifica sono descritti in JSON. A seconda della serializzazione desiderata per il corpo del messaggio, ad esempio with JSON, CBOR, protobuf e così via, il corpo del messaggio può essere serializzato in modo diverso.

Il set di campi contenuti nel corpo varia a seconda del tipo di notifica.

Le sezioni seguenti illustrano in modo più dettagliato i diversi tipi di notifiche emesse dall'hub e dai dispositivi gemelli digitali di Azure (o altri servizi di Azure. Vengono fornite informazioni sugli elementi che attivano ogni tipo di notifica e sul set di campi inclusi in ogni tipo di corpo della notifica.

## <a name="digital-twin-change-notifications"></a>Notifiche di modifiche del dispositivo gemello digitale

Quando si aggiorna un dispositivo gemello digitale, vengono attivate le notifiche di modifica dei dispositivi **gemelli digitali** , ad esempio:
* Quando vengono modificati i valori o i metadati della proprietà.
* Quando vengono modificati i metadati del componente o del dispositivo digitale. Un esempio di questo scenario è la modifica del modello di un dispositivo gemello digitale.

### <a name="properties"></a>Proprietà

Ecco i campi nel corpo di una notifica di modifica del dispositivo gemello digitale.

| Nome    | Valore |
| --- | --- |
| `id` | Identificatore della notifica, ad esempio un UUID o un contatore gestito dal servizio. `source` + `id` univoco per ogni evento distinto |
| `source` | Nome dell'hub Internet delle cose o dell'istanza di Azure Digital gemelli, ad esempio *MyHub.Azure-Devices.NET* o *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `data` | Documento di patch JSON che descrive l'aggiornamento apportato al dispositivo gemello. Per informazioni dettagliate, vedere la sezione relativa ai [Dettagli del corpo](#body-details) . |
| `specversion` | *1,0*<br>Il messaggio è conforme a questa versione della [specifica CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | ID del dispositivo gemello digitale |
| `time` | Timestamp per il momento in cui l'operazione si è verificata nel dispositivo gemello digitale |
| `traceparent` | Contesto di traccia W3C per l'evento |

### <a name="body-details"></a>Dettagli corpo

All'interno del messaggio, il `data` campo contiene un documento di patch JSON contenente l'aggiornamento al dispositivo gemello digitale.

Si immagini ad esempio che un dispositivo gemello digitale è stato aggiornato con la seguente patch.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

I dati nella notifica corrispondente (se eseguita in modo sincrono dal servizio, ad esempio i gemelli digitali di Azure che aggiornano un dispositivo gemello digitale) hanno un corpo simile al seguente:

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

Si tratta delle informazioni che verranno inserite nel `data` campo del messaggio di notifica del ciclo di vita.

## <a name="digital-twin-lifecycle-notifications"></a>Notifiche del ciclo di vita gemello digitale

Tutti i dispositivi [gemelli digitali](concepts-twins-graph.md) emettono notifiche, indipendentemente dal fatto che rappresentino i [dispositivi dell'hub Internet o meno nei dispositivi gemelli digitali di Azure](how-to-ingest-iot-hub-data.md) . Questo è dovuto alle **notifiche del ciclo** di vita, che sono relative al dispositivo gemello digitale.

Le notifiche del ciclo di vita vengono attivate quando:
* Viene creato un dispositivo gemello digitale
* Un dispositivo gemello digitale è stato eliminato

### <a name="properties"></a>Proprietà

Di seguito sono riportati i campi nel corpo di una notifica del ciclo di vita.

| Nome | Valore |
| --- | --- |
| `id` | Identificatore della notifica, ad esempio un UUID o un contatore gestito dal servizio. `source` + `id` è univoco per ogni evento distinto. |
| `source` | Nome dell'hub Internet delle cose o dell'istanza di Azure Digital gemelli, ad esempio *MyHub.Azure-Devices.NET* o *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `data` | Dati del dispositivo gemello che riscontrano l'evento del ciclo di vita. Per informazioni dettagliate, vedere la sezione relativa ai [Dettagli del corpo](#body-details-1) . |
| `specversion` | *1,0*<br>Il messaggio è conforme a questa versione della [specifica CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | ID del dispositivo gemello digitale |
| `time` | Timestamp per il momento in cui l'operazione si è verificata nel dispositivo gemello |
| `traceparent` | Contesto di traccia W3C per l'evento |

### <a name="body-details"></a>Dettagli corpo

Di seguito è riportato un esempio di un messaggio di notifica del ciclo di vita: 

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

All'interno del messaggio, il `data` campo contiene i dati del gemello digitale interessato, rappresentato in formato JSON. Lo schema per questo è la risorsa dei dispositivi *gemelli digitali 7,1*.

Per gli eventi di creazione, il `data` payload riflette lo stato del gemello dopo la creazione della risorsa, quindi deve includere tutti gli elementi generati dal sistema esattamente come una `GET` chiamata.

Di seguito è riportato un esempio di dati per un dispositivo [plug and Play (PNP)](../iot-pnp/overview-iot-plug-and-play.md) , con componenti e senza proprietà di primo livello. Le proprietà che non hanno senso per i dispositivi, ad esempio le proprietà segnalate, devono essere omesse. Si tratta delle informazioni che verranno inserite nel `data` campo del messaggio di notifica del ciclo di vita.

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Di seguito è riportato un altro esempio di dati gemelli digitali. Questo si basa su un [modello](concepts-models.md)e non supporta i componenti:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

## <a name="digital-twin-relationship-change-notifications"></a>Notifiche di modifica della relazione tra dispositivi gemelli digitali

Le **notifiche di modifica delle relazioni** vengono attivate quando viene creata, aggiornata o eliminata una relazione di un dispositivo gemello digitale. 

### <a name="properties"></a>Proprietà

Di seguito sono riportati i campi nel corpo di una notifica di modifica della relazione.

| Nome    | Valore |
| --- | --- |
| `id` | Identificatore della notifica, ad esempio un UUID o un contatore gestito dal servizio. `source` + `id` univoco per ogni evento distinto |
| `source` | Nome dell'istanza di Azure Digital Twins, ad esempio *mydigitaltwins.westus2.azuredigitaltwins.NET* |
| `data` | Payload della relazione modificata. Per informazioni dettagliate, vedere la sezione relativa ai [Dettagli del corpo](#body-details-2) . |
| `specversion` | *1,0*<br>Il messaggio è conforme a questa versione della [specifica CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | ID della relazione, ad esempio `<twinID>/relationships/<relationshipID>` |
| `time` | Timestamp relativo al momento in cui si è verificata l'operazione sulla relazione |
| `traceparent` | Contesto di traccia W3C per l'evento |

### <a name="body-details"></a>Dettagli corpo

All'interno del messaggio, il `data` campo contiene il payload di una relazione, in formato JSON. USA lo stesso formato di una `GET` richiesta per una relazione tramite l' [API DigitalTwins](/rest/api/digital-twins/dataplane/twins). 

Di seguito è riportato un esempio dei dati per una notifica di aggiornamento della relazione. "Aggiornamento di una relazione" indica che le proprietà della relazione sono state modificate, quindi i dati visualizzano la proprietà aggiornata e il nuovo valore. Si tratta delle informazioni che verranno inserite nel `data` campo del messaggio di notifica della relazione digitale gemello.

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

Di seguito è riportato un esempio dei dati per una notifica di creazione o eliminazione di una relazione. Per `Relationship.Delete` , il corpo è lo stesso della `GET` richiesta e ottiene lo stato più recente prima dell'eliminazione.

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

## <a name="digital-twin-telemetry-messages"></a>Messaggi di telemetria di dispositivi gemelli digitali

**I messaggi di telemetria** vengono ricevuti nei dispositivi gemelli digitali di Azure da dispositivi connessi che raccolgono e inviano misurazioni.

### <a name="properties"></a>Proprietà

Ecco i campi nel corpo di un messaggio di telemetria.

| Nome    | Valore |
| --- | --- |
| `id` | Identificatore della notifica, fornito dal cliente quando si chiama l'API di telemetria. |
| `source` | Nome completo del dispositivo gemello a cui è stato inviato l'evento di telemetria. Usa il formato seguente: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>` . |
| `specversion` | *1,0*<br>Il messaggio è conforme a questa versione della [specifica CloudEvents](https://github.com/cloudevents/spec). |
| `type` | `microsoft.iot.telemetry` |
| `data` | Messaggio di telemetria inviato a dispositivi gemelli. Il payload non è stato modificato e potrebbe non essere allineato allo schema del dispositivo gemello a cui sono stati inviati i dati di telemetria. |
| `dataschema` | Lo schema di dati è l'ID modello del dispositivo gemello o il componente che genera i dati di telemetria. Ad esempio: `dtmi:example:com:floor4;2`. |
| `datacontenttype` | `application/json` |
| `traceparent` | Contesto di traccia W3C per l'evento. |

### <a name="body-details"></a>Dettagli corpo

Il corpo contiene la misurazione della telemetria insieme ad alcune informazioni contestuali sul dispositivo.

Di seguito è riportato un esempio di corpo del messaggio di telemetria: 

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come consegnare gli eventi a destinazioni diverse, usando endpoint e route:
* [*Concetti: route degli eventi*](concepts-route-events.md)
