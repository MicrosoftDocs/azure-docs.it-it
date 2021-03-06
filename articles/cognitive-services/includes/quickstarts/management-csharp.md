---
title: 'Avvio rapido: Libreria client di gestione di Azure per .NET'
description: Questo argomento di avvio rapido contiene un'introduzione alla libreria client di gestione di Azure per .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 1ecfd58e961e70182bfc2e0cc8eaf77f5053eb77
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879590"
---
[Documentazione di riferimento](/dotnet/api/overview/azure/cognitiveservices/management) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/) | [Esempi](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="c-prerequisites"></a>Prerequisiti di C#

* Una sottoscrizione di Azure valida: [crearne una gratuitamente](https://azure.microsoft.com/free/).
* Versione corrente di [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-c-application"></a>Creare una nuova applicazione C#

Creare una nuova applicazione .NET Core. In una finestra di una console, ad esempio cmd, PowerShell o Bash, usare il comando `dotnet new` per creare una nuova app console con il nome `azure-management-quickstart`. Questo comando crea un semplice progetto C# "Hello World" con un unico file di origine: *program.cs*. 

```console
dotnet new console -n azure-management-quickstart
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

```console
dotnet build
```

L'output di compilazione non deve contenere alcun avviso o errore. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installare la libreria client

Nella directory dell'applicazione installare la libreria client di gestione di Azure per .NET con il comando seguente:

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Se si usa l'ambiente di sviluppo integrato di Visual Studio, la libreria client è disponibile come pacchetto NuGet scaricabile.

### <a name="import-libraries"></a>Importare le librerie

Aprire *program.cs* e aggiungere le istruzioni `using` seguenti all'inizio del file:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>Autenticare il client

Aggiungere i campi seguenti alla radice di *program.cs* e inserire i relativi valori usando l'entità servizio creata e le informazioni sull'account Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Quindi, nel metodo **Main**, usare questi valori per creare un oggetto **CognitiveServicesManagementClient**. Questo oggetto è necessario per tutte le operazioni di gestione di Azure.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Chiamare i metodi di gestione

Aggiungere il codice seguente alla fine del metodo **Main** per elencare le risorse disponibili, creare una risorsa di esempio, elencare le risorse di cui si è proprietari e quindi eliminare la risorsa di esempio. Questi metodi verranno definiti nei passaggi seguenti.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-c"></a>Creare una risorsa di Servizi cognitivi (C#)

Per creare e sottoscrivere una nuova risorsa di Servizi cognitivi, usare il metodo **Crea**. Questo metodo aggiunge una nuova risorsa fatturabile al gruppo di risorse che viene passato. Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio che si vuole usare, oltre al piano tariffario (o SKU) e a una località di Azure. Il metodo seguente accetta tutti questi argomenti e crea una risorsa.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Scegliere un servizio e un piano tariffario

Quando si crea una nuova risorsa, è necessario conoscere il tipo di servizio da usare, oltre al [piano tariffario](https://azure.microsoft.com/pricing/details/cognitive-services/) (o SKU) desiderato. Questa e altre informazioni verranno usate come parametri per la creazione della risorsa. È possibile trovare un elenco di tipi di servizi cognitivi disponibili chiamando il metodo seguente nello script:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Visualizzare le risorse

Per visualizzare tutte le risorse nell'account Azure (in tutti i gruppi di risorse), usare il metodo seguente:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>Eliminare una risorsa

Il metodo seguente elimina la risorsa specificata dal gruppo di risorse specificato.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione dalla directory dell'applicazione con il comando `dotnet run`.

```dotnet
dotnet run
```

## <a name="see-also"></a>Vedere anche

* Vedere **[autenticare le richieste ai servizi cognitivi di Azure](../../authentication.md)** per informazioni su come lavorare in modo sicuro con servizi cognitivi.
* Vedere **[che cosa sono i servizi cognitivi di Azure?](../../what-are-cognitive-services.md)** per ottenere un elenco di categorie diverse all'interno di servizi cognitivi.
* Vedere **[supporto del linguaggio naturale](../../language-support.md)** per visualizzare l'elenco dei linguaggi naturali supportati da servizi cognitivi.
* Vedere **[usare servizi cognitivi come contenitori](../../cognitive-services-container-support.md)** per comprendere come usare i servizi cognitivi locali.
* Vedere **[pianificare e gestire i costi per i servizi cognitivi](../../plan-manage-costs.md)** per stimare i costi dell'uso di servizi cognitivi.
* Per altri dettagli sull'SDK di gestione, vedere la **[documentazione di riferimento di cognitive Services Management SDK](/dotnet/api/overview/azure/cognitiveservices/management)** .
