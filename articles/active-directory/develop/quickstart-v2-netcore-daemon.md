---
title: "Avvio rapido: Ottenere un token e chiamare Microsoft Graph in un'app console | Azure"
titleSuffix: Microsoft identity platform
description: Questa guida di avvio rapido illustra come un'app di esempio .NET Core può usare il flusso di credenziali client per ottenere un token e chiamare Microsoft Graph.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: a31bf345f523eea940be5d56495890e8ab5c6dbd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861644"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Guida introduttiva: Ottenere un token e chiamare l'API Microsoft Graph usando l'identità di un'app console

In questa guida di avvio rapido si scarica e si esegue un esempio di codice che illustra come un'applicazione console .NET Core può ottenere un token di accesso per chiamare l'API Microsoft Graph e visualizzare un [elenco di utenti](/graph/api/user-list) nella directory. L'esempio di codice illustra anche come un processo o un servizio windows può essere eseguito con un'identità dell'applicazione, anziché con l'identità di un utente. L'applicazione console di esempio in questa guida introduttiva è anche un'applicazione daemon, quindi si tratta di un'applicazione client riservata.

> [!div renderon="docs"]
> Il diagramma seguente illustra il funzionamento dell'app di esempio:
>
> ![Diagramma che mostra come funziona l'app di esempio generata da questa guida di avvio rapido.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva richiede [.NET Core 3.1 SDK,](https://dotnet.microsoft.com/download) ma funzionerà anche con .NET 5.0 SDK.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>Registrare e scaricare l'app

> [!div renderon="docs" class="sxs-lookup"]
>
> Sono disponibili due opzioni per iniziare a compilare l'applicazione: la configurazione automatica o manuale.
>
> ### <a name="automatic-configuration"></a>Configurazione automatica
>
> Se si vuole registrare e configurare automaticamente l'app e quindi scaricare l'esempio di codice, seguire questa procedura:
>
> 1. Passare alla pagina portale di Azure <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">per la registrazione dell'app.</a>
> 1. Immettere un nome per l'applicazione e fare clic su **Registra**.
> 1. Seguire le istruzioni per scaricare e configurare automaticamente la nuova applicazione con un solo clic.
>
> ### <a name="manual-configuration"></a>Configurazione manuale
>
> Se si desidera configurare manualmente l'applicazione e l'esempio di codice, utilizzare le procedure seguenti.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Passaggio 1: Registrare l'applicazione
> Per registrare l'applicazione e aggiungere manualmente le informazioni di registrazione dell'app alla soluzione, seguire questa procedura:
>
> 1. Accedere al <a href="https://portal.azure.com/" target="_blank">portale di Azure</span></a>.
> 1. Se si ha accesso a più tenant, usare il filtro **Directory e** sottoscrizione nel menu in alto per selezionare il tenant in cui si :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: vuole registrare l'applicazione.
> 1. Cercare e selezionare **Azure Active Directory**.
> 1. In **Gestisci** selezionare **Registrazioni app** > **Nuova registrazione**.
> 1. Per **Nome** immettere un nome per l'applicazione. Ad esempio, immettere **Daemon-console**. Gli utenti dell'app visualizzano questo nome ed è possibile modificarlo in un secondo momento.
> 1. Selezionare **Registra** per creare l'applicazione.
> 1. In **Gestisci**, selezionare **Certificati e segreti**.
> 1. In **Segreti client** selezionare **Nuovo segreto client**, immettere un nome e quindi selezionare **Aggiungi**. Registrare il valore del segreto in una posizione sicura per usarlo in un passaggio successivo.
> 1. In **Gestisci** selezionare **Autorizzazioni API** > **Aggiungi un'autorizzazione**. Selezionare **Microsoft Graph**.
> 1. Selezionare **Autorizzazioni applicazione**.
> 1. Nel nodo **Utente** selezionare **User.Read.All** e quindi selezionare **Aggiungi autorizzazioni.**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Scaricare e configurare l'app della guida introduttiva
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Passaggio 1: Configurare l'applicazione nel portale di Azure
> Per il funzionamento dell'esempio di codice in questa guida introduttiva, creare un segreto client e aggiungere l'autorizzazione **user.Read.All** dell'applicazione del API Graph.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Apporta queste modifiche per me]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Già configurata](media/quickstart-v2-netcore-daemon/green-check.png) L'applicazione è configurata con questi attributi.

#### <a name="step-2-download-your-visual-studio-project"></a>Passaggio 2: Scaricare il progetto di Visual Studio

> [!div renderon="docs"]
> [Scaricare il progetto di Visual Studio](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> È possibile eseguire il progetto fornito in Visual Studio o in Visual Studio per Mac.


> [!div class="sxs-lookup" renderon="portal"]
> Eseguire il progetto usando Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Scaricare il codice di esempio](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Passaggio 3: Configurare il progetto di Visual Studio
>
> 1. Estrarre il file ZIP in una cartella locale vicina alla radice del disco. Ad esempio, estrarre in *C:\Azure-Samples*.
>
>    È consigliabile estrarre l'archivio in una directory vicina alla radice dell'unità per evitare errori causati da limitazioni di lunghezza del percorso in Windows.
>
> 1. Aprire la soluzione in Visual Studio: *1-Call-MSGraph\daemon-console.sln* (facoltativo).
> 1. In *appsettings.jssu*, sostituire i valori di , e `Tenant` `ClientId` `ClientSecret` :
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    In questo codice:
>    - `Enter_the_Application_Id_Here` è l'ID applicazione (client) per l'applicazione registrata.
        Per trovare i valori per l'ID applicazione (client) e l'ID  directory (tenant), passare alla pagina Panoramica dell'app nel portale di Azure.
>    - Sostituire `Enter_the_Tenant_Id_Here` con l'ID tenant o il nome del tenant (ad esempio, `contoso.microsoft.com` ).
>    - Sostituire `Enter_the_Client_Secret_Here` con il segreto client creato nel passaggio 1.
    Per generare una nuova chiave, passare alla pagina **Certificati & segreti.**

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Passaggio 3: Consenso dell'amministratore

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Passaggio 4: Consenso dell'amministratore

Se si tenta di eseguire l'applicazione a questo punto, si riceverà un errore *HTTP 403 - Accesso* negato: "Privilegi insufficienti per completare l'operazione". Questo errore si verifica perché qualsiasi autorizzazione solo app richiede un amministratore globale della directory per fornire il consenso all'applicazione. Selezionare una delle opzioni seguenti, a seconda del ruolo.

##### <a name="global-tenant-administrator"></a>Amministratore del tenant globale

> [!div renderon="docs"]
> Se si è un amministratore tenant globale, passare ad **Applicazioni aziendali** nel portale di Azure. Selezionare la registrazione dell'app e **selezionare Autorizzazioni** nella **sezione Sicurezza** del riquadro sinistro. Selezionare quindi il pulsante grande Concedi consenso amministratore **per {Nome tenant}** (dove **{Nome tenant}** è il nome della directory).

> [!div renderon="portal" class="sxs-lookup"]
> Se si è un amministratore globale, passare alla pagina **Autorizzazioni API** e selezionare Concedi il consenso **dell'amministratore per** Enter_the_Tenant_Name_Here .
> > [!div id="apipermissionspage"]
> > [Passare alla pagina Autorizzazioni API]()

##### <a name="standard-user"></a>Utente standard

Se si è un utente standard del tenant, chiedere a un amministratore globale di concedere il consenso dell'amministratore per l'applicazione. A tale scopo, assegnare l'URL seguente all'amministratore:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> In tale URL:
> * Sostituire `Enter_the_Tenant_Id_Here` con l'ID tenant o il nome del tenant (ad esempio, `contoso.microsoft.com` ).
> * `Enter_the_Application_Id_Here` è l'ID applicazione (client) per l'applicazione registrata.

È possibile che venga visualizzato l'errore "AADSTS50011: Nessun indirizzo di risposta registrato per l'applicazione" dopo aver concesso il consenso all'app usando l'URL precedente. Questo errore si verifica perché l'applicazione e l'URL non hanno un URI di reindirizzamento. È possibile ignorarla.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Passaggio 4: Eseguire l'applicazione

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Passaggio 5: Eseguire l'applicazione

Se si usa Visual Studio o Visual Studio per Mac, premere **F5** per eseguire l'applicazione. In caso contrario, eseguire l'applicazione tramite prompt dei comandi, console o terminale:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
In questo codice:
* `{ProjectFolder}` è la cartella in cui è stato estratto il file zip. Un esempio è `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Verrà visualizzato un elenco di utenti in Azure Active Directory di conseguenza.

L'applicazione di questa guida di avvio rapido usa un segreto client per identificarsi come client riservato. Il segreto client viene aggiunto come file di testo normale ai file di progetto. Per motivi di sicurezza, è consigliabile usare un certificato anziché un segreto client prima di considerare l'applicazione come applicazione di produzione. Per altre informazioni su come usare un certificato, vedere [queste istruzioni](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) nel repository GitHub per questo esempio.

## <a name="more-information"></a>Ulteriori informazioni
Questa sezione include una panoramica del codice necessario per consentire l'accesso degli utenti. Questa panoramica può essere utile per comprendere il funzionamento del codice, quali sono gli argomenti principali e come aggiungere l'accesso a un'applicazione console .NET Core esistente.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Funzionamento dell'esempio
>
> ![Diagramma che mostra come funziona l'app di esempio generata da questa guida di avvio rapido.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Microsoft Authentication Library (MSAL, nel pacchetto [Microsoft.Identity.Client)](https://www.nuget.org/packages/Microsoft.Identity.Client) è la libreria usata per accedere agli utenti e richiedere token per l'accesso a un'API protetta da Microsoft Identity Platform. Questa guida introduttiva richiede i token usando l'identità dell'applicazione anziché le autorizzazioni delegate. Il flusso di autenticazione in questo caso è noto come flusso OAuth delle credenziali [client.](v2-oauth2-client-creds-grant-flow.md) Per altre informazioni su come usare MSAL.NET con un flusso di credenziali client, vedere [questo articolo.](https://aka.ms/msal-net-client-credentials)

 È possibile installare MSAL.NET eseguendo il comando seguente nella console di Visual Studio Gestione pacchetti:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Inizializzazione della libreria MSAL

È possibile aggiungere il riferimento per la libreria MSAL aggiungendo il codice seguente:

```csharp
using Microsoft.Identity.Client;
```

Inizializzare quindi MSAL usando il codice seguente:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | Elemento | Descrizione |
 |---------|---------|
 | `config.ClientSecret` | Segreto client creato per l'applicazione nel portale di Azure. |
 | `config.ClientId` | ID applicazione (client) per l'applicazione registrata nel portale di Azure. È possibile trovare questo valore  nella pagina Panoramica dell'app nel portale di Azure. |
 | `config.Authority`    | (Facoltativo) Endpoint del servizio token di sicurezza per l'autenticazione dell'utente. In genere è per il cloud pubblico, dove è il nome del `https://login.microsoftonline.com/{tenant}` tenant o `{tenant}` l'ID tenant.|

Per altre informazioni, vedere la documentazione [di riferimento per `ConfidentialClientApplication` ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication).

### <a name="requesting-tokens"></a>Richiesta di token

Per richiedere un token usando l'identità dell'app, usare il `AcquireTokenForClient` metodo :

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Elemento| Descrizione |
|---------|---------|
| `scopes` | Contiene gli ambiti richiesti. Per i client riservati, questo valore deve usare un formato simile a `{Application ID URI}/.default` . Questo formato indica che gli ambiti richiesti sono quelli definiti in modo statico nell'oggetto app impostato nel portale di Azure. Per Microsoft Graph, `{Application ID URI}` punta a `https://graph.microsoft.com` . Per le API Web personalizzate, è definito nel portale di Azure, in Registrazione `{Application ID URI}` **applicazione (anteprima)**  >  **Esporre un'API**. |

Per altre informazioni, vedere la documentazione [di riferimento per `AcquireTokenForClient` ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle applicazioni daemon, vedere la panoramica dello scenario:

> [!div class="nextstepaction"]
> [Applicazione daemon che chiama le API Web](scenario-daemon-overview.md)
