---
title: Configurare un dominio personalizzato in App Web statiche di Azure
description: Informazioni su come eseguire il mapping di un dominio personalizzato ad App Web statiche di Azure
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: buhollan
ms.openlocfilehash: 578860883a108bba4b4bcd8cd04e8c08f484d474
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92173692"
---
# <a name="setup-a-custom-domain-in-azure-static-web-apps-preview"></a>Configurare un dominio personalizzato in App Web statiche di Azure (anteprima)

Per impostazione predefinita, App Web statiche di Azure fornisce un nome di dominio generato automaticamente. Questo articolo illustra come eseguire il mapping di un nome di dominio personalizzato a un'applicazione di App Web statiche di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un nome di dominio acquistato
- Accesso alle proprietà di configurazione DNS per il dominio

Quando si configurano i nomi di dominio, i record "A" vengono usati per eseguire il mapping dei domini radice (ad esempio, `example.com`) a un indirizzo IP. Il mapping dei domini radice deve essere eseguito direttamente a un indirizzo IP, perché la specifica DNS non consente il mapping di un dominio a un altro.

## <a name="dns-configuration-options"></a>Opzioni di configurazione DNS

Per un'applicazione sono disponibili diversi tipi di configurazioni DNS.

| Se si vuole | Risultato |
|--|--|
| Supporto tecnico `www.example.com` o `blog.example.net` | [Esecuzione del mapping di un record CNAME](#map-a-cname-record) |
| Supporto di `example.com` | [Configurare un dominio radice](#configure-a-root-domain) |
| Fare in modo che tutti i sottodomini puntino a `www.example.com` | [Eseguire il mapping di un carattere jolly](#map-a-wildcard-domain) |

## <a name="map-a-cname-record"></a>Esecuzione del mapping di un record CNAME

Un record CNAME esegue il mapping di un dominio a un altro. È possibile usare un record CNAME per eseguire `www.example.com` il mapping di, `blog.example.com` o qualsiasi altro sottodominio al dominio generato automaticamente fornito dalle app Web statiche di Azure.

1. Aprire il [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

1. Cercare e selezionare **App Web statiche**

1. Nella pagina _App Web statiche_ selezionare il nome dell'app.

1. Nel menu fare clic su **Domini personalizzati**.

1. Fare clic sul pulsante **Aggiungi**

1. Nella finestra _Domini personalizzati_ copiare l'URL nel campo **Valore**.

### <a name="configure-dns-provider"></a>Configurare il provider DNS

1. Accedere al sito Web del provider di dominio.

2. Individuare la pagina relativa alla gestione dei record DNS. Poiché ogni provider di dominio ha una propria interfaccia per i record DNS, vedere la documentazione del provider. Cercare le aree del sito denominate **Domain Name** (Nome di dominio), **DNS** o **Name Server Management** (Gestione server dei nomi).

3. È spesso possibile visualizzare la pagina dei record DNS visualizzando le informazioni dell'account e cercando un collegamento come **My domains** (Domini personali). Passare alla pagina e quindi cercare un collegamento con un titolo simile a **File di zona**, **Record DNS** o **Configurazione avanzata**.

    La schermata seguente è un esempio di pagina di record DNS:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Configurazione del provider DNS di esempio":::

4. Creare un nuovo **record CNAME** con i valori seguenti...

    | Impostazione             | Valore                     |
    | ------------------- | ------------------------- |
    | Tipo                | CNAME                     |
    | Host                | www                       |
    | Valore               | Incollare dagli Appunti |
    | TTL (se applicabile) | Lasciare il valore predefinito    |

5. Salvare le modifiche apportate al provider DNS.

### <a name="validate-cname"></a>Convalidare il record CNAME

1. Tornare alla finestra _Domini personalizzati_ nel portale di Azure.

1. Immettere il dominio, inclusa la parte `www` nella sezione _Convalidare il dominio personalizzato_.

1. Fare clic sul pulsante **Convalida**.

Ora che il dominio personalizzato è configurato, potrebbero essere necessarie diverse ore prima che il provider DNS propaghi le modifiche in tutto il mondo. Per controllare lo stato della propagazione, accedere a [dnspropagation.net](https://dnspropagation.net). Immettere il dominio personalizzato, incluso la `www`, selezionare CNAME nell'elenco a discesa e selezionare **Avvia**.

Se le modifiche DNS sono state popolate, il sito Web restituisce l'URL generato automaticamente dell'app Web statica, ad esempio _random-name-123456789c.azurestaticapps.net_.

## <a name="configure-a-root-domain"></a>Configurare un dominio radice

I domini radice corrispondono al dominio cui sono stati sottratti eventuali sottodomini, incluso `www`. Ad esempio, il dominio radice di `www.example.com` è `example.com`. Tale dominio è noto anche come dominio "APEX".

Anche se il supporto del dominio radice non è disponibile nella versione di anteprima, vedere il post di blog su come [configurare i domini radice in App Web statiche di Azure](https://burkeholland.github.io/posts/static-app-root-domain) per informazioni dettagliate su come configurare il supporto del dominio radice con un'app Web statica.

## <a name="map-a-wildcard-domain"></a>Esecuzione del mapping di un dominio con caratteri jolly

A volte si preferisce che tutto il traffico inviato a un sottodominio venga reindirizzato a un altro dominio. Un esempio comune è dato dal mapping di tutto il traffico del sottodominio a `www.example.com`. In questo modo, anche se un utente digita `w.example.com` invece di `www.example.com`, la richiesta viene inviata a `www.example.com`.

### <a name="configure-dns-provider"></a>Configurare il provider DNS

1. Accedere al sito Web del provider di dominio.

2. Individuare la pagina relativa alla gestione dei record DNS. Poiché ogni provider di dominio ha una propria interfaccia per i record DNS, vedere la documentazione del provider. Cercare le aree del sito denominate **Domain Name** (Nome di dominio), **DNS** o **Name Server Management** (Gestione server dei nomi).

3. È spesso possibile visualizzare la pagina dei record DNS visualizzando le informazioni dell'account e cercando un collegamento come **My domains** (Domini personali). Passare alla pagina e quindi cercare un collegamento con un titolo simile a **File di zona**, **Record DNS** o **Configurazione avanzata**.

    La schermata seguente è un esempio di pagina di record DNS:

    :::image type="content" source="media/custom-domain/example-record-ui.png" alt-text="Configurazione del provider DNS di esempio":::

4. Creare un nuovo record **CNAME** con i valori seguenti, sostituendo `www.example.com` con il nome di dominio personalizzato.

    | Impostazione | Valore                  |
    | ------- | ---------------------- |
    | Tipo    | CNAME                  |
    | Host    | \*                     |
    | Valore   | www.example.com        |
    | TTL     | Lasciare il valore predefinito |

5. Salvare le modifiche apportate al provider DNS.

Ora che il dominio con caratteri jolly è configurato, la propagazione delle modifiche in tutto il mondo potrebbe richiedere diverse ore. Per controllare lo stato della propagazione, accedere a [dnspropagation.net](https://dnspropagation.net). Immettere il dominio personalizzato del dominio con l'eventuale sottodominio (diverso da `www`), selezionare CNAME nell'elenco a discesa e selezionare **Avvia**.

Se le modifiche DNS sono state popolate, il sito Web restituisce il dominio personalizzato configurato per l'app Web statica, ad esempio `www.example.com`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare le impostazioni dell'app](application-settings.md)
