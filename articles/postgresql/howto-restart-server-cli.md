---
title: Riavviare il server-interfaccia della riga di comando di Azure-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come riavviare un database di Azure per PostgreSQL-singolo server usando l'interfaccia della riga di comando di Azure
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: af870c495ae707dd8319645eca979f0906234b4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608392"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Riavviare database di Azure per PostgreSQL-server singolo con l'interfaccia della riga di comando di Azure
In questo argomento viene descritta la procedura per riavviare un server di Database di Azure per PostgreSQL. Potrebbe essere necessario riavviare il server per motivi di manutenzione, causando così una breve interruzione del servizio mentre il server esegue l'operazione.

Il riavvio del server verrà bloccato se il servizio è occupato. È ad esempio possibile che il servizio stia elaborando un'operazione richiesta in precedenza, come il ridimensionamento di vCore.
 
> [!NOTE] 
> Il tempo necessario per completare un riavvio dipende dal processo di ripristino di PostgreSQL. Per ridurre i tempi di riavvio, è consigliabile ridurre al minimo l'attività in corso nel server prima del riavvio. Potrebbe inoltre essere necessario aumentare la frequenza di checkpoint. È anche possibile ottimizzare i valori dei parametri correlati ai checkpoint, tra cui `max_wal_size` . È inoltre consigliabile eseguire `CHECKPOINT` il comando prima di riavviare il server.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida pratica:
- Creare un [database di Azure per il server PostgreSQL](quickstart-create-server-up-azure-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Questo articolo richiede la versione 2.0 dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="restart-the-server"></a>Riavviare il server

Riavviare il server con il comando seguente:

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come impostare i parametri nel database di Azure per PostgreSQL](howto-configure-server-parameters-using-cli.md)
