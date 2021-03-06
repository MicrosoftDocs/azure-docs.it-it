---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c3f913aa3de1b723cd4eae70ff8e578a8abbec12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95554638"
---
#### <a name="to-create-a-new-service"></a>Per creare un nuovo servizio
1. Usare le credenziali dell'account Microsoft per accedere al [portale di Microsoft Azure per enti pubblici](https://portal.azure.us/).
2. Nel portale per enti pubblici fare clic su **+** e quindi nel Marketplace fare clic su **Visualizza tutto**. Cercare _StorSimple Physical_ (Dispositivi fisici StorSimple). Selezionare e fare clic su **Serie di dispositivi fisici StorSimple** e quindi fare clic su **Crea**. In alternativa, nel portale per enti pubblici fare clic su **+** e quindi in **archiviazione** fare clic su **serie di dispositivi fisici StorSimple**.
3. Nel pannello **Gestione dispositivi StorSimple** seguire questa procedura:
   
   1. Specificare un **Nome della risorsa** univoco per il servizio. Si tratta di un nome descrittivo che può essere usato per identificare il servizio. Il nome può contenere da 2 a 50 caratteri che possono essere lettere, numeri e trattini. Il nome deve iniziare e terminare con una lettera o un numero.
   2. Selezionare una sottoscrizione nell'elenco a discesa **Sottoscrizione**. La sottoscrizione viene collegata all'account di fatturazione. Questo campo non è presente se si dispone di una sola sottoscrizione.
   3. Per **Gruppo di risorse**, selezionare **Usa esistente** o **Crea nuovo**. Per altre informazioni, vedere [Azure resource groups](../articles/azure-resource-manager/management/manage-resource-groups-portal.md) (Gruppi di risorse di Azure).
   4. In **Località** specificare una località per il servizio. Per Località si intende l'area geografica in cui si desidera distribuire il dispositivo. Selezionare **USGov Iowa** o **USGov Virginia**.
   5. Selezionare **Crea un nuovo account di archiviazione** per creare automaticamente un account di archiviazione con il servizio. Specificare un nome per questo account di archiviazione. Se è necessario che i dati siano in un percorso diverso, deselezionare questa casella.
   6. Selezionare **Aggiungi al dashboard** se si vuole inserire un collegamento rapido al servizio nel dashboard.
   7. Fare clic su **Crea** per creare il servizio Gestione dispositivi StorSimple. La creazione del servizio richiede alcuni minuti. Al termine della creazione del servizio, verrà visualizzata una notifica e verrà aperto il pannello del nuovo servizio.