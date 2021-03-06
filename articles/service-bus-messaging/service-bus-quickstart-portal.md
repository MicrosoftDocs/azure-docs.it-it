---
title: Usare il portale di Azure per creare una coda del bus di servizio
description: Questo argomento di avvio rapido illustra come creare uno spazio dei nomi del bus di servizio e una coda nello spazio dei nomi con il portale di Azure.
author: spelluru
ms.author: spelluru
ms.date: 08/12/2020
ms.topic: quickstart
ms.custom:
- mode-portal
ms.openlocfilehash: e38d32c93453737060f654add58f09902b05ee45
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537205"
---
# <a name="use-azure-portal-to-create-a-service-bus-namespace-and-a-queue"></a>Usare il portale di Azure per creare uno spazio dei nomi del bus di servizio e una coda
Questo argomento di avvio rapido illustra come creare uno spazio dei nomi del bus di servizio e una coda con il [portale di Azure][Azure portal]. Spiega anche come ottenere le credenziali di autorizzazione che un'applicazione client può utilizzare per inviare/ricevere messaggi da e verso la coda. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido, assicurarsi di avere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito][] prima di iniziare.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato creato uno spazio dei nomi del bus di servizio e una coda nello spazio dei nomi. Per informazioni su come inviare/ricevere messaggi da e verso la coda, vedere uno degli argomenti di avvio rapido nella sezione **Inviare e ricevere messaggi**. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
