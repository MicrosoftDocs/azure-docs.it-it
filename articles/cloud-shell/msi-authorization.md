---
title: Usare identità gestite per le risorse in Azure Cloud Shell
description: Autenticare il codice con l'identità del servizio gestito in Azure Cloud Shell
services: azure
author: maertendMSFT
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 04/14/2018
ms.openlocfilehash: 0fb19524079f84e92e1ddbc98a61917026492663
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89469899"
---
# <a name="use-managed-identities-for-azure-resources-in-azure-cloud-shell"></a>Usare identità gestite per le risorse di Azure in Azure Cloud Shell

Per le risorse di Azure, Azure Cloud Shell supporta l'autorizzazione con identità gestite. Consente di recuperare i token di accesso per comunicare in modo sicuro con i servizi di Azure.

## <a name="about-managed-identities-for-azure-resources"></a>Informazioni sulle identità gestite per le risorse di Azure
Una difficoltà comune durante la creazione di applicazioni cloud è rappresentata dalla gestione sicura delle credenziali che devono essere presenti nel codice per l'autenticazione ai servizi cloud. In Cloud Shell potrebbe essere necessario autenticare il recupero da Key Vault per le credenziali necessarie per uno script.

Le identità gestite per le risorse di Azure consentono di risolvere il problema in maniera più semplice, assegnando ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory (Azure AD). È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD, incluso Key Vault, senza inserire le credenziali nel codice.

## <a name="acquire-access-token-in-cloud-shell"></a>Acquisire un token di accesso in Cloud Shell

Eseguire i comandi seguenti per impostare un token di accesso dell'identità del servizio gestito come variabile di ambiente, `access_token`.
```
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Gestione della scadenza di token

Il sottosistema dell'identità del servizio gestito locale memorizza i token nella cache. È quindi possibile eseguire tutte le chiamate desiderate e una chiamata in transito ad Azure AD restituisce un risultato solo se:
- si verifica un mancato riscontro nella cache a causa dell'assenza di token nella cache
- il token è scaduto

Se si memorizza nella cache il token nel codice, è consigliabile essere preparati a gestire gli scenari in cui la risorsa indica che il token è scaduto.

Per gestire gli errori del token, visitare la [pagina dell'identità del servizio gestito relativa al curl dei token di accesso dell'identità del servizio gestito](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md#error-handling).

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sull'identità del servizio gestito](../active-directory/managed-identities-azure-resources/overview.md)  
[Acquisire i token di accesso dall'identità del servizio gestito delle macchine virtuali](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)