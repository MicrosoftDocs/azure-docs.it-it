---
title: Che cos'è il provisioning basato su risorse umane con Azure Active Directory? | Microsoft Docs
description: Panoramica del provisioning basato su risorse umane.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ffe8c1397525348e472e965a407909fee36152d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168448"
---
# <a name="what-is-hr-driven-provisioning"></a>Che cos'è il provisioning basato su risorse umane?

![Provisioning basato su risorse umane](./media/what-is-hr-driven-provisioning/cloud2a.png)

Il provisioning basato su risorse umane è il processo di creazione di identità digitali basate su un sistema di gestione delle risorse umane (RU).  I sistemi RU diventano lo Start of Authority per queste nuove identità digitali create e rappresentano spesso il punto iniziale per numerosi processi di provisioning.  Ad esempio, un nuovo dipendente che entra in azienda viene creato nel sistema di gestione delle risorse umane.  La creazione attiva il provisioning di un account utente in Active Directory, quindi Azure AD Connect ne effettua il provisioning in Azure AD e così via.

Il provisioning basato su risorse umane può essere locale o sul cloud.

## <a name="on-premises-based-hr-provisioning"></a>Provisioning basato su risorse umane in locale
Il provisioning basato su risorse umane in locale viene effettuato con un sistema RU locale e come mezzo per effettuare il provisioning di nuove identità digitali.

I sistemi RU sono disponibili in diversi pacchetti o bundle software e possono usare server SQL, directory LDAP e così via.

Attualmente, le soluzioni di provisioning basato su risorse umane in locale usano Microsoft Identity Manager per attivare il provisioning quando viene creata una nuova identità in questi sistemi RU.

Con Microsoft Identity Manager è possibile effettuare il provisioning degli utenti dai sistemi RU ad Active Directory o Azure AD.

Per informazioni su Microsoft Identity Manager e sui sistemi supportati, vedere la documentazione di [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016).

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>Passaggi successivi 
- [Che cos'è la gestione del ciclo di vita delle identità?](what-is-identity-lifecycle-management.md)
- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning di app?](what-is-app-provisioning.md)
- [Che cos'è il provisioning tra directory?](what-is-inter-directory-provisioning.md)