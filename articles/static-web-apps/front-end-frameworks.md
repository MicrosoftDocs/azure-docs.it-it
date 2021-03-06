---
title: Configurare i Framework front-end con l'anteprima delle app Web statiche di Azure
description: Impostazioni per i Framework front-end più diffusi necessari per le app Web statiche di Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 9f02c9ad10e2b03bddc7c3ca2cfb54932464b69a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731752"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Configurare Framework e librerie front-end con l'anteprima di app Web statiche di Azure

Per le app Web statiche di Azure è necessario disporre dei valori di configurazione appropriati nel [file di configurazione della compilazione](github-actions-workflow.md) per la libreria o il Framework front-end.

## <a name="configuration"></a>Configurazione

La tabella seguente elenca le impostazioni per una serie di Framework e librerie<sup>1</sup>.

Lo scopo delle colonne della tabella è illustrato dagli elementi seguenti:

- **Percorso di output**: elenca il valore per `output_location` , ovvero la [cartella per le versioni compilate dei file dell'applicazione](github-actions-workflow.md#build-and-deploy).

- **Comando di compilazione personalizzato**: quando il Framework richiede un comando diverso da `npm run build` o `npm run azure:build` , è possibile definire un [comando di compilazione personalizzato](github-actions-workflow.md#custom-build-commands).

| Framework | Percorso degli artefatti dell'app | Comando di compilazione personalizzato |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | n/a <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Universale angolare](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | n/d |
| [Backbone.js](https://backbonejs.org/) | `/` | n/d |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | n/d |
| [Ember](https://emberjs.com/) | `dist` | n/d |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Barlume](https://glimmerjs.com/) | `dist` | n/d |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | n/d |
| [Hyperapp](https://hyperapp.dev/) | `/` | n/d |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | n/d |
| [jQuery](https://jquery.com/) | `/` | n/d |
| [Knockout](https://knockoutjs.com/) | `dist` | n/d |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | n/d |
| [Marko](https://markojs.com/) | `public` | n/d |
| [Meteora](https://www.meteor.com/) | `bundle` | n/d |
| [Mithril](https://mithril.js.org/) | `dist` | n/d |
| [Polymer](https://www.polymer-project.org/) | `build/default` | n/d |
| [Preact](https://preactjs.com/) | `build` | n/d |
| [React](https://reactjs.org/) | `build` | n/d |
| [RedwoodJS](https://redwoodjs.com/) | `web/dist` | `yarn rw build` |
| [Stencil](https://stenciljs.com/) | `www` | n/d |
| [Svelte](https://svelte.dev/) | `public` | n/d |
| [Three.js](https://threejs.org/) | `/` | n/d |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | n/d |
| [Vue.js](https://vuejs.org/) | `dist` | n/d |

<sup>1</sup> la tabella precedente non è destinata a essere un elenco completo di Framework e librerie che funzionano con le app Web statiche di Azure.

<sup>2</sup> non applicabile

## <a name="next-steps"></a>Passaggi successivi

- [Configurazione di compilazioni e flussi di lavoro](github-actions-workflow.md)
