---
title: Usare l'analisi del comportamento dell'entità per rilevare le minacce avanzate | Microsoft Docs
description: Abilitare l'analisi del comportamento di utenti ed entità in Sentinel di Azure e configurare le origini dati
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: yelevin
ms.openlocfilehash: 3f3e945a00ec7bba75deebb56118d45aa7ff571d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100530723"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Abilitare l'analisi del comportamento dell'utente e dell'entità (offrono dati) in Sentinel di Azure 

> [!IMPORTANT]
>
> - Le funzionalità di offrono dati e delle pagine di entità sono ora disponibili a livello **generale** in **_tutte_** le aree geografiche e le aree di Sentinel di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per abilitare o disabilitare questa funzionalità (questi prerequisiti non sono necessari per usare la funzionalità):

- L'utente deve essere un membro dell'Azure Active Directory dell'organizzazione e non un utente Guest.

- All'utente deve essere assegnato il ruolo di amministratore **globale** o **amministratore della sicurezza** in Azure ad.

- È necessario che all'utente sia assegnato almeno uno dei seguenti **ruoli di Azure** ([altre informazioni su RBAC di Azure](roles.md)):
    - **Collaboratore sentinella di Azure** a livello di area di lavoro o di gruppo di risorse.
    - **Log Analytics collaboratore** a livello di gruppo di risorse o di sottoscrizione.

- All'area di lavoro non deve essere applicato alcun blocco di risorse di Azure. [Altre informazioni sul blocco delle risorse di Azure](../azure-resource-manager/management/lock-resources.md).

> [!NOTE]
> Non è necessaria alcuna licenza speciale per aggiungere la funzionalità offrono dati a Sentinel di Azure, ma potrebbero essere applicati **addebiti aggiuntivi** .

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Come abilitare l'analisi del comportamento dell'utente e dell'entità

1. Dal menu di navigazione di Azure Sentinel selezionare **comportamento entità**.

1. Sotto l'intestazione **attiva**, impostare l'interruttore **su on**.

1. Fare clic sul pulsante **Seleziona origini dati** .

1. Nel riquadro di **selezione dell'origine dati** selezionare le caselle di controllo accanto alle origini dati in cui si desidera abilitare offrono dati, quindi selezionare **applica**.

    > [!NOTE]
    >
    > Nella metà inferiore del riquadro di **selezione dell'origine dati** viene visualizzato un elenco di origini dati supportate da offrono dati che non sono ancora state abilitate. 
    >
    > Dopo aver abilitato offrono dati, si avrà l'opzione, quando si connettono nuove origini dati, per abilitarle per offrono dati direttamente dal riquadro connettore dati, se sono compatibili con offrono dati.

1. Selezionare **Vai a ricerca entità**. Verrà visualizzato il riquadro ricerca entità, che da ora in poi sarà ciò che viene visualizzato quando si sceglie **comportamento entità** dal menu principale di Azure Sentinel.

## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come abilitare e configurare l'analisi del comportamento dell'utente e dell'entità (offrono dati) in Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
