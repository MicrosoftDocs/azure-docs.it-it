---
title: Distribuire modelli di app per la logica
description: Informazioni su come distribuire modelli di Azure Resource Manager creati per app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 08/25/2020
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 5f5db3fd88f04e7fe569cd675936445dcf730288
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705337"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Implementare modelli di Azure Resource Manager per App per la logica di Azure

Dopo aver creato un modello di Azure Resource Manager per l'app per la logica, è possibile distribuire il modello nei modi seguenti:

* [Azure portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Interfaccia della riga di comando di Azure](#cli)
* [API REST di Azure Resource Manager](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Eseguire la distribuzione tramite portale di Azure

Per distribuire automaticamente un modello di app per la logica in Azure, è possibile scegliere il pulsante **Distribuisci in Azure** seguente, che consente di accedere al portale di Azure e di richiedere informazioni sull'app per la logica. È quindi possibile apportare le modifiche necessarie al modello o ai parametri dell'app per la logica.

[![Distribuzione in Azure](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Ad esempio, dopo aver effettuato l'accesso al portale di Azure vengono richieste le informazioni seguenti:

* Nome della sottoscrizione di Azure
* Gruppo di risorse da usare
* Percorso dell'app per la logica
* Nome dell'app per la logica
* Un URI di test
* Accettazione dei termini e delle condizioni specificati

Per altre informazioni, vedere gli argomenti seguenti:

* [Panoramica: automatizzare la distribuzione per le app per la logica con modelli di Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md)
* [Distribuire le risorse con i modelli di Azure Resource Manager e il portale di Azure](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Distribuire con Visual Studio

Per distribuire un modello di app per la logica da un progetto di gruppo di risorse di Azure creato con Visual Studio, seguire questa [procedura per distribuire manualmente l'app per la logica](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) in Azure.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Distribuire con Azure PowerShell

Per eseguire la distribuzione in un *gruppo di risorse di Azure* specifico, usare il comando seguente:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Per altre informazioni, vedere gli argomenti seguenti:

* [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [`New-AzResourceGroupDeployment`](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Distribuire con l'interfaccia della riga di comando di Azure

Per eseguire la distribuzione in un *gruppo di risorse di Azure* specifico, usare il comando seguente:

```azurecli
az deployment group create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Per altre informazioni, vedere gli argomenti seguenti:

* [Distribuire le risorse con i modelli di Azure Resource Manager e l'interfaccia della riga di comando di Azure](../azure-resource-manager/templates/deploy-cli.md)
* [`az deployment group create`](/cli/azure/deployment/group#az_deployment_group_create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Eseguire la distribuzione con Azure DevOps

Per distribuire modelli di app per la logica e gestire gli ambienti, i team usano comunemente uno strumento come [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) in [Azure DevOps](/azure/devops/user-guide/what-is-azure-devops-services). Azure Pipelines fornisce un' [attività di distribuzione del gruppo di risorse di Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) che è possibile aggiungere a qualsiasi pipeline di compilazione o versione. Per l'autorizzazione per la distribuzione e la generazione della pipeline di rilascio, è necessaria anche un' [entità servizio](../active-directory/develop/app-objects-and-service-principals.md)Azure Active Directory (ad). Altre informazioni sull' [uso delle entità servizio con Azure Pipelines](/azure/devops/pipelines/library/connect-to-azure).

Per ulteriori informazioni sull'integrazione continua e la distribuzione continua (CI/CD) per i modelli di Azure Resource Manager con Azure Pipelines, vedere gli argomenti e gli esempi seguenti:

* [Integrare modelli di Gestione risorse con Azure Pipelines](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Esercitazione: Integrazione continua dei modelli di Azure Resource Manager con Azure Pipelines](../azure-resource-manager/templates/deployment-tutorial-pipeline.md)
* [Esempio: connettersi alle code del bus di servizio di Azure da app per la logica di Azure e distribuirle con Azure Pipelines in Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: connettersi agli account di archiviazione di Azure da app per la logica di Azure e distribuirli con Azure Pipelines in Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: configurare un'azione dell'app per le funzioni per app per la logica di Azure e distribuirla con Azure Pipelines in Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Esempio: connettersi a un account di integrazione da app per la logica di Azure e distribuirlo con Azure Pipelines in Azure DevOps](/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Esempio: orchestrare Azure Pipelines usando app per la logica di Azure](/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Ecco i passaggi generali generali per l'uso di Azure Pipelines:

1. In Azure Pipelines creare una pipeline vuota.

1. Scegliere le risorse necessarie per la pipeline, ad esempio il modello di app per la logica e i file dei parametri del modello, generati manualmente o come parte del processo di compilazione.

1. Per il processo di Agent, trovare e aggiungere l'attività di **distribuzione gruppo di risorse di Azure** .

   ![Aggiungere l'attività "distribuzione gruppo di risorse di Azure"](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Configurare con un' [entità servizio](/azure/devops/pipelines/library/connect-to-azure).

1. Aggiungere i riferimenti ai file del modello e dei parametri del modello dell'app per la logica.

1. Continuare a compilare passaggi nel processo di rilascio per eventuali altri ambienti, test automatizzati o responsabili approvazione necessari.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>Autorizzare le connessioni OAuth

Dopo la distribuzione, l'app per la logica funziona end-to-end con parametri validi, ma per generare token di accesso validi per [autenticare le credenziali](../active-directory/develop/authentication-vs-authorization.md), è comunque necessario autorizzare o usare connessioni OAuth preautorizzate. Tuttavia, è necessario distribuire e autenticare le risorse di connessione API una sola volta, ovvero non è necessario includere le risorse di connessione nelle distribuzioni successive, a meno che non sia necessario aggiornare le informazioni di connessione. Se si usa una pipeline di integrazione continua e distribuzione continua, è necessario distribuire solo le risorse aggiornate delle app per la logica e non è necessario autorizzare nuovamente le connessioni ogni volta.

Ecco alcuni suggerimenti per gestire le connessioni di autorizzazione:

* Preautorizzare e condividere le risorse di connessione API tra app per la logica che si trovano nella stessa area. Le connessioni API sono disponibili come risorse di Azure indipendentemente dalle app per la logica. Mentre le app per la logica hanno dipendenze dalle risorse di connessione API, le risorse di connessione API non hanno dipendenze dalle app per la logica e rimangono dopo l'eliminazione delle app per la logica dipendenti. Inoltre, le app per la logica possono usare le connessioni API presenti in altri gruppi di risorse. Tuttavia, la finestra di progettazione delle app per la logica supporta la creazione di connessioni API solo nello stesso gruppo di risorse delle app per la logica.

  > [!NOTE]
  > Se si sta valutando la condivisione delle connessioni API, assicurarsi che la soluzione sia in grado di [gestire potenziali problemi di limitazione](../logic-apps/handle-throttling-problems-429-errors.md#connector-throttling). La limitazione si verifica a livello di connessione, quindi il riutilizzo della stessa connessione tra più app per la logica potrebbe aumentare il rischio di limitazione dei problemi.

* A meno che lo scenario non includa servizi e sistemi che richiedono l'autenticazione a più fattori, è possibile usare uno script di PowerShell per fornire il consenso per ogni connessione OAuth eseguendo un ruolo di lavoro di integrazione continua come account utente normale in una macchina virtuale con sessioni del browser attive con le autorizzazioni e il consenso già fornito. Ad esempio, è possibile riutilizzare lo script di esempio fornito dal [progetto LogicAppConnectionAuth nel repository GitHub delle app per la logica](https://github.com/logicappsio/LogicAppConnectionAuth).

* Autorizzare manualmente le connessioni OAuth aprendo l'app per la logica in progettazione app per la logica, nel portale di Azure o in Visual Studio.

* Se invece si usa un' [entità servizio](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure ad) per autorizzare le connessioni, informazioni su come [specificare i parametri dell'entità servizio nel modello di app per la logica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Monitorare le app per la logica](../logic-apps/monitor-logic-apps.md)
