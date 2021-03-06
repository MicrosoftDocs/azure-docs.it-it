---
title: URL Rewriter di Azure Media Player
description: Azure Media Player riscriverà un determinato URL di Servizi multimediali di Azure per fornire flussi per SMOOTH, DASH, HLS v3 e HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: efa77ce7416b94331dce2bb4e7f77dd50c20d450
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448577"
---
# <a name="url-rewriter"></a>URL Rewriter #

Per impostazione predefinita, Azure Media Player riscriverà un determinato URL di Servizi multimediali di Azure per fornire i flussi per SMOOTH, DASH, HLS v3 e HLS v4. Se ad esempio l'origine viene specificata come segue, Azure Media Player proverà a riprodurre tutti i protocolli precedenti:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Se tuttavia non si vuole usare l'URL Rewriter, è possibile aggiungere la proprietà `disableUrlRewriter` al parametro. Questo significa che le informazioni passate alle origini vengono passate direttamente al lettore senza modifica.  Ecco un esempio di aggiunta di due origini al lettore, DASH e SMOOTH Streaming.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

o

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

Inoltre, se si desidera, è possibile specificare i formati di streaming specifici che dovranno essere riscritti da Azure Media Player usando il parametro `streamingFormats`. Le opzioni includono `DASH`, `SMOOTH`, `HLSv3`, `HLSv4`, `HLS`. La differenza tra HLS e HLS v3 e v4 è che il formato HLS supporta la riproduzione di contenuto FairPlay. v3 e v4 non supportano FairPlay. Questa opzione è utile se non sono disponibili criteri di distribuzione per uno specifico protocollo.  Ecco un esempio di protocollo DASH non abilitato con l'asset.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

o

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

I due esempi precedenti possono essere usati in combinazione in più circostanze, in base allo specifico asset in uso.

> [!NOTE]
> Le informazioni sulla protezione Widevine vengono mantenute solo nel protocollo DASH.

## <a name="next-steps"></a>Passaggi successivi ##

- [Avvio rapido su Azure Media Player](azure-media-player-quickstart.md)