---
title: Criteri di gestione API di esempio-indirizzare la richiesta in base alle dimensioni del corpo del messaggio
titleSuffix: Azure API Management
description: Esempio di criteri di Gestione API di Azure che illustra come instradare le richieste in base alle dimensioni del corpo della richiesta.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: e59aece0be39bf671e3d1e62f1a0e4c2a42a1ec8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076471"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Instradare la richiesta in base alle dimensioni del corpo

Questo articolo offre un esempio di criteri di Gestione API di Azure che illustra come instradare le richieste in base alle dimensioni del corpo della richiesta. Per impostare o modificare il codice dei criteri, seguire la procedura descritta nell'articolo su come [impostare o modificare criteri](../set-edit-policies.md). Per altri esempi, vedere l'articolo relativo agli [esempi di criteri](../policy-reference.md).

## <a name="policy"></a>Criteri

Incollare il codice nel blocco **inbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri di Gestione API:

+ [Criteri di trasformazione](../api-management-transformation-policies.md)
+ [Esempi di criteri](../policy-reference.md)