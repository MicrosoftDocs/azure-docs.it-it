---
title: "Interfaccia della riga di comando: Caricare un certificato TLS/SSL e associarlo a un'app"
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per automatizzare la distribuzione e la gestione dell'app Servizio app. Questo esempio illustra come associare un certificato TLS/SSL personalizzato a un'app.
tags: azure-service-management
ms.assetid: eb95d350-81ea-4145-a1e2-6eea3b7469b2
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 50f5c0f00daf025791eb0982ea4c5f626ded02ad
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782496"
---
# <a name="bind-a-custom-tlsssl-certificate-to-an-app-service-app-using-cli"></a>Associare un certificato TLS/SSL personalizzato a un'app del servizio app usando l'interfaccia della riga di comando

Questo script di esempio crea un'app nel servizio app con le risorse correlate, quindi associa a essa il certificato TLS/SSL di un nome di dominio personalizzato. Per questo esempio è necessario:

* Accesso alla pagina di configurazione DNS del registrar.
* File PFX valido e relativa password per il certificato TLS/SSL da caricare e associare.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, è necessaria la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Per trovare la versione, eseguire `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to an app")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Consente di creare un piano di servizio app. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Consente di creare un'app del servizio app. |
| [`az webapp config hostname add`](/cli/azure/webapp/config/hostname#az_webapp_config_hostname_add) | Esegue il mapping di un dominio personalizzato a un'app del servizio app. |
| [`az webapp config ssl upload`](/cli/azure/webapp/config/ssl#az_webapp_config_ssl_upload) | Carica un certificato TLS/SSL in un'app del servizio app. |
| [`az webapp config ssl bind`](/cli/azure/webapp/config/ssl#az_webapp_config_ssl_bind) | Associa un certificato TLS/SSL caricato a un'app del servizio app. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../samples-cli.md).
