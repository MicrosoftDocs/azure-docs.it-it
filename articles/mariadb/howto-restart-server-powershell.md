---
title: Riavviare il server Azure PowerShell-database di Azure per MariaDB
description: Questo articolo descrive come riavviare un database di Azure per il server MariaDB usando PowerShell.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 14cde5b1302c46b819bb7d841fb5b84a43c580c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664869"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>Riavviare il database di Azure per il server MariaDB con PowerShell

Questo argomento descrive come riavviare un server di Database di Azure per MariaDB. Potrebbe essere necessario riavviare il server per motivi di manutenzione, causando una breve interruzione durante l'operazione.

Il riavvio del server è bloccato se il servizio è occupato. È ad esempio possibile che il servizio stia elaborando un'operazione richiesta in precedenza, come il ridimensionamento di vCore.

La quantità di tempo necessaria per completare un riavvio dipende dal processo di ripristino MariaDB. Per ridurre il tempo di riavvio, è consigliabile ridurre al minimo la quantità di attività che si verifica sul server prima del riavvio.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario:

- Il [modulo AZ PowerShell](/powershell/azure/install-az-ps) installato localmente o [Azure cloud Shell](https://shell.azure.com/) nel browser
- Un [database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Durante la fase di anteprima del modulo Az.MariaDb PowerShell, è necessario installarlo separatamente dal modulo Az PowerShell usando il comando seguente: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Quando il modulo Az.MariaDb di PowerShell sarà disponibile a livello generale, diventerà parte delle future versioni del modulo Az PowerShell e disponibile in modo nativo dall'interno di Azure Cloud Shell.

Se si sceglie di usare PowerShell in locale, connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Riavviare il server

Riavviare il server con il comando seguente:

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un database di Azure per il server MariaDB usando PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)