---
title: 'Avvio rapido: Creare un profilo e un endpoint - Modello di Resource Manager'
titleSuffix: Azure Content Delivery Network
description: Questa guida introduttiva illustra come creare un profilo di rete per la distribuzione di contenuti di Azure ed eseguire l'endpoint di un modello Resource Manager
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: 31f0510ed50c9d48a46524a353d7c872b368f75c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779040"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Avvio rapido: Creare un profilo e un endpoint della rete CDN di Azure - Modello di Resource Manager

Informazioni su come iniziare a usare Rete di distribuzione dei contenuti di Azure con un modello di Azure Resource Manager (modello ARM). Con il modello vengono distribuiti un profilo e un endpoint.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/).

Questo modello è configurato per la creazione di uno degli elementi seguenti:

* Profilo
* Endpoint

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

Nel modello è definita una risorsa di Azure:

* **[Microsoft.Cdn/profiles](/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>Distribuire il modello

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>Portale

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Esaminare le risorse distribuite

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Gruppi di risorse** nel riquadro sinistro.

3. Selezionare il gruppo di risorse creato nella sezione precedente. Il nome del gruppo di risorse predefinito è **myResourceGroupCDN**

4. Verificare che le risorse seguenti siano state create nel gruppo di risorse:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Gruppo di risorse della rete CDN di Azure" border="true":::

## <a name="clean-up-resources"></a>Pulire le risorse

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Quando il gruppo di risorse e tutte le risorse al suo interno non sono più necessari, usare il comando [az group delete](/cli/azure/group#az_group_delete) per rimuoverli.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

Quando il gruppo di risorse e tutte le risorse al suo interno non sono più necessari, usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuoverli.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portale

Quando il gruppo di risorse, il profilo della rete CDN e tutte le risorse correlate non sono più necessari, è possibile eliminarli. Selezionare il gruppo di risorse **myResourceGroupCDN** che contiene il profilo e l'endpoint della rete CDN e quindi selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido sono stati creati questi componenti:

* Profilo CDN
* Endpoint

Per altre informazioni su Rete CDN di Azure e Azure Resource Manager, continuare con gli articoli seguenti.

> [!div class="nextstepaction"]
> [Esercitazione: Usare la rete CDN per gestire contenuto statico da un'app Web](cdn-add-to-web-app.md)
