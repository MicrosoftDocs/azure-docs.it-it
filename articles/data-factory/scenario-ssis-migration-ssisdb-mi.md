---
title: Migrazione SSIS con SQL di Azure Istanza gestita come destinazione del carico di lavoro del database
description: Migrazione SSIS con SQL di Azure Istanza gestita come destinazione del carico di lavoro del database.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 3d2bc60f8ba7120f8d962500c06be50e905c11a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373590"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Migrazione SSIS con SQL di Azure Istanza gestita come destinazione del carico di lavoro del database

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Quando si esegue la migrazione dei carichi di lavoro del database da un'istanza di SQL Server al Istanza gestita SQL di Azure, è necessario avere familiarità con il [servizio di migrazione dei dati di Azure](../dms/dms-overview.md)e le [topologie di rete per le migrazioni SQL istanza gestita con DMS](../dms/resource-network-topologies.md).

Questo articolo è incentrato sulla migrazione dei pacchetti di SQL Server Integration Service (SSIS) archiviati nel catalogo SSIS (SSISDB) e SQL Server Agent processi che pianificano le esecuzioni del pacchetto SSIS.

## <a name="migrate-ssis-catalog-ssisdb"></a>Eseguire la migrazione del catalogo SSIS (SSISDB)

La migrazione di SSISDB può essere eseguita tramite DMS, come descritto nell'articolo [eseguire la migrazione di pacchetti SSIS a SQL istanza gestita](../dms/how-to-migrate-ssis-packages-managed-instance.md).

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>Processi SSIS a SQL Istanza gestita Agent

SQL Istanza gestita dispone di un'utilità di pianificazione nativa di prima classe esattamente come SQL Server Agent locale.  È possibile [eseguire pacchetti SSIS tramite SQL istanza gestita Agent di Azure](how-to-invoke-ssis-package-managed-instance-agent.md).

Poiché uno strumento di migrazione per i processi SSIS non è ancora disponibile, è necessario eseguirne la migrazione da SQL Server Agent locale a SQL Istanza gestita Agent tramite script/copia manuale.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Azure Data Factory](./introduction.md)
- [Runtime di integrazione Azure-SSIS](./create-azure-ssis-integration-runtime.md)
- [Servizio Migrazione del database di Azure](../dms/dms-overview.md)
- [Topologie di rete per le migrazioni di SQL Istanza gestita tramite DMS](../dms/resource-network-topologies.md)
- [Eseguire la migrazione di pacchetti SSIS a un Istanza gestita SQL](../dms/how-to-migrate-ssis-packages-managed-instance.md)

## <a name="next-steps"></a>Passaggi successivi

- [Connettersi al catalogo SSIS (SSISDB) in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Eseguire pacchetti SSIS distribuiti in Azure](/sql/integration-services/lift-shift/ssis-azure-run-packages)