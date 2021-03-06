---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 7a390c0d19a37ea18f9eac8636683ec35ecbc844
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880698"
---
## <a name="configure-your-local-environment"></a>Configurare l'ambiente locale

Per eseguire le procedure descritte è necessario:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java,programming-language-other"  
+ [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 o versione successiva.
::: zone-end  
::: zone pivot="programming-language-python"
+ La versione di Azure Functions Core Tools che corrisponde alla versione di Python installata:

   | Versione Python | Versione Core Tools |
   | -------------- | ------------------ |
   | Python 3.8     | [versione 3.x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3.6<br/>Python 3.7 | [Versione 2.7.1846 o versioni successive](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) versione 2.4 o successiva. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), versioni Active LTS e Maintenance LTS (8.11.1 e 10.14.1 consigliate).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64 bit)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64 bit)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64 bit)](https://www.python.org/downloads/release/python-368/), supportati da Funzioni di Azure. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versione 8 o 11. 

+ [Apache Maven](https://maven.apache.org), versione 3.0 o successiva.

::: zone-end
::: zone pivot="programming-language-other"
+ Strumenti di sviluppo per il linguaggio in uso. Questa esercitazione usa il [linguaggio di programmazione R](https://www.r-project.org/) come esempio.
::: zone-end