---
title: Accesso alle informazioni utente in App Web statiche di Azure
description: Informazioni su come leggere i dati utente restituiti dal provider di autorizzazione.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 9aca1e76c825de52744da817f6a0bf236eef617c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313607"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Accesso alle informazioni utente in App Web statiche di Azure (anteprima)

App Web statiche di Azure fornisce informazioni sull'utente correlate all'autenticazione tramite un [endpoint di accesso diretto](#direct-access-endpoint) e a [funzioni API](#api-functions).

Molte interfacce utente si basano principalmente sui dati di autenticazione utente. L'endpoint di accesso diretto è un'API di utilità che espone informazioni utente senza dover implementare una funzione personalizzata. Senza considerare la praticità, l'endpoint di accesso diretto non è soggetto a ritardi di avvio a freddo associati all'architettura serverless.

## <a name="client-principal-data"></a>Dati dell'entità di sicurezza client

L'oggetto dati dell'entità di sicurezza client espone all'app le informazioni identificabili dall'utente. L'oggetto entità di sicurezza client prevede le proprietà seguenti:

| Proprietà           | Descrizione                                                                                                                                                                                                                                                                                                                                                        |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `identityProvider` | Nome del [provider di identità](authentication-authorization.md).                                                                                                                                                                                                                                                                                              |
| `userId`           | Identificatore univoco specifico di App Web statiche di Azure per l'utente. <ul><li>Il valore è univoco in base alla singola app. Ad esempio, lo stesso utente restituisce un valore `userId` diverso in un'altra risorsa di App Web statiche.<li>Il valore viene mantenuto per la durata di un utente. Se si elimina e si aggiunge di nuovo lo stesso utente all'app, viene generato un nuovo `userId`.</ul> |
| `userDetails`      | Nome utente o indirizzo di posta elettronica dell'utente. Alcuni provider restituiscono l'[indirizzo di posta elettronica dell'utente](authentication-authorization.md), mentre altri inviano il [nome utente](authentication-authorization.md).                                                                                                                                                                    |
| `userRoles`        | Matrice dei [ruoli assegnati dell'utente](authentication-authorization.md).                                                                                                                                                                                                                                                                                          |

L'esempio seguente si riferisce a un oggetto entità di sicurezza client di esempio:

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": ["anonymous", "authenticated"]
}
```

## <a name="direct-access-endpoint"></a>Endpoint di accesso diretto

È possibile inviare una richiesta `GET` alla route `/.auth/me` e ricevere l'accesso diretto ai dati dell'entità di sicurezza client. Quando lo stato della visualizzazione si basa sui dati di autorizzazione, questo approccio garantisce prestazioni ottimali.

Per gli utenti connessi, la risposta contiene un oggetto entità di sicurezza client JSON. Le richieste che provengono da utenti non autenticati restituiscono `null`.

Usando l'API [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup>, è possibile accedere ai dati dell'entità di sicurezza client con la sintassi seguente.

```javascript
async function getUserInfo() {
  const response = await fetch('/.auth/me');
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>Funzioni API

Le funzioni API disponibili nelle app Web statiche tramite il back-end funzioni di Azure hanno accesso alle stesse informazioni utente di un'applicazione client. Mentre l'API riceve informazioni identificabili dall'utente, non esegue controlli personalizzati se l'utente è autenticato o se corrisponde a un ruolo obbligatorio. Le regole di controllo di accesso vengono definite nel [`staticwebapp.config.json`](routes.md) file.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

I dati dell'entità di sicurezza client vengono passati alle funzioni API nell'intestazione della richiesta `x-ms-client-principal`. I dati dell'entità di sicurezza client vengono inviati come stringa con codifica [Base 64](https://www.wikipedia.org/wiki/Base64) che contiene un oggetto JSON serializzato.

La funzione di esempio seguente mostra come leggere e restituire informazioni utente.

```javascript
module.exports = async function (context, req) {
  const header = req.headers['x-ms-client-principal'];
  const encoded = Buffer.from(header, 'base64');
  const decoded = encoded.toString('ascii');

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded),
    },
  };
};
```

Presupponendo che la funzione sopra indicata sia denominata `user`, è possibile usare l'API [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> del browser per accedere alla risposta dell'API con la sintassi seguente.

```javascript
async function getUser() {
  const response = await fetch('/api/user');
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(await getUser());
```

# <a name="c"></a>[C#](#tab/csharp)

In una funzione C#, le informazioni sull'utente sono disponibili nell' `x-ms-client-principal` intestazione che può essere deserializzata in un `ClaimsPrincipal` oggetto o in un tipo personalizzato. Nel codice seguente viene illustrato come decomprimere l'intestazione in un tipo intermediario, `ClientPrincipal` , che viene quindi convertito in un' `ClaimsPrincipal` istanza di.

```csharp
  public static class StaticWebAppsAuth
  {
    private class ClientPrincipal
    {
        public string IdentityProvider { get; set; }
        public string UserId { get; set; }
        public string UserDetails { get; set; }
        public IEnumerable<string> UserRoles { get; set; }
    }

    public static ClaimsPrincipal Parse(HttpRequest req)
    {
        var principal = new ClientPrincipal();

        if (req.Headers.TryGetValue("x-ms-client-principal", out var header))
        {
            var data = header[0];
            var decoded = Convert.FromBase64String(data);
            var json = Encoding.ASCII.GetString(decoded);
            principal = JsonSerializer.Deserialize<ClientPrincipal>(json, new JsonSerializerOptions { PropertyNameCaseInsensitive = true });
        }

        principal.UserRoles = principal.UserRoles?.Except(new string[] { "anonymous" }, StringComparer.CurrentCultureIgnoreCase);

        if (!principal.UserRoles?.Any() ?? true)
        {
            return new ClaimsPrincipal();
        }

        var identity = new ClaimsIdentity(principal.IdentityProvider);
        identity.AddClaim(new Claim(ClaimTypes.NameIdentifier, principal.UserId));
        identity.AddClaim(new Claim(ClaimTypes.Name, principal.UserDetails));
        identity.AddClaims(principal.UserRoles.Select(r => new Claim(ClaimTypes.Role, r)));

        return new ClaimsPrincipal(identity);
    }
  }
```

---

<sup>1</sup> L'API [fetch](https://caniuse.com/#feat=fetch) e l'operatore [await](https://caniuse.com/#feat=mdn-javascript_operators_await) non sono supportati in Internet Explorer.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare le impostazioni dell'app](application-settings.md)
