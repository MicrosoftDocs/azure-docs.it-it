---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: bb004afe6cdd6992f742877318426052d1bead6d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073401"
---
#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/) 

1. Selezionare **Registrazioni per l'app**.
1. Selezionare l'applicazione *webapi1* per aprire la pagina **Panoramica**.
1. In **Gestisci** selezionare **Esponi un'API**.
1. Accanto a **URI ID applicazione** selezionare il collegamento **Imposta**.
1. Sostituire il valore predefinito (un GUID) con `api`, quindi selezionare **Salva**. Viene visualizzato l'URI completo, che deve essere nel formato `https://your-tenant-name.onmicrosoft.com/api`. Quando l'applicazione Web richiede un token di accesso per l'API, deve aggiungere questo URI come prefisso per ogni ambito definito per l'API.
1. In **Ambiti definiti da questa API** selezionare **Aggiungi un ambito**.
1. Immettere i valori seguenti per creare un ambito che definisce l'accesso in lettura all'API, quindi selezionare **Aggiungi ambito**:
    1. **Nome ambito**: `demo.read`
    1. **Nome visualizzato per il consenso amministratore**: `Read access to demo API`
    1. **Descrizione del consenso amministratore**: `Allows read access to the demo API`
1. Selezionare **Aggiungi un ambito** , immettere i valori seguenti per aggiungere un ambito che definisce l'accesso in lettura all'API, quindi selezionare **Aggiungi ambito**:
    1. **Nome ambito**: `demo.write`
    1. **Nome visualizzato per il consenso amministratore**: `Write access to demo API`
    1. **Descrizione del consenso amministratore**: `Allows write access to the demo API`

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Selezionare **Applicazioni (legacy)** .
1. Selezionare l'applicazione *webapi1* per aprire la pagina **Proprietà**.
1. Selezionare **Ambiti pubblicati**. Gli ambiti pubblicati possono essere usati per concedere a un'applicazione client determinate autorizzazioni per l'API Web.
1. Immettere `demo.read` come **AMBITO** e `Read access to the web API` come **DESCRIZIONE**.
1. Immettere `demo.write` come **AMBITO** e `Write access to the web API` come **DESCRIZIONE**.
1. Selezionare **Salva**.