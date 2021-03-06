---
title: Limitare il contenuto della rete CDN di Azure per paese/area geografica
description: Informazioni su come limitare l'accesso in base al paese/area geografica al contenuto della rete CDN di Azure usando la funzionalità di filtro geografico.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 01/16/2021
ms.author: allensu
ms.openlocfilehash: 8901dffb752409acd7fb08a2025bed9a4cc70132
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98539521"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Limitare il contenuto della rete CDN di Azure per paese/area geografica

## <a name="overview"></a>Panoramica
Quando un utente richiede il contenuto, il contenuto viene servito agli utenti in tutte le posizioni. È possibile limitare l'accesso al contenuto in base al paese o all'area geografica. 

Con la funzionalità di *filtro geografico* è possibile creare regole per percorsi specifici nell'endpoint della rete CDN. È possibile impostare le regole per consentire o bloccare il contenuto in paesi o aree geografiche selezionate.

> [!IMPORTANT]
> I profili di **rete CDN Standard di Azure con tecnologia Microsoft** non supportano il filtro geografico basato sul percorso.
> 

## <a name="standard-profiles"></a>Profili standard

Queste istruzioni sono per la rete **CDN standard di Azure di Akamai** e i profili di rete **CDN standard di Azure da Verizon** .

Per i profili della **rete CDN Premium di Azure fornita da Verizon** è necessario usare il portale **Gestione** per attivare il filtro geografico. Per altre informazioni, vedere [Profili della rete CDN Premium di Azure fornita da Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definire il percorso di directory
Per accedere alla funzionalità di filtro geografico, selezionare l'endpoint della rete CDN all'interno del portale e quindi selezionare **Filtro geografico** in Impostazioni nel menu a sinistra. 

![Screenshot che mostra i filtri geografici selezionati dal menu di un endpoint.](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Nella casella **Percorso** specificare il percorso relativo della posizione a cui gli utenti potranno o meno accedere. 

È possibile applicare il filtro geografico a tutti i file con una barra (/) o selezionare cartelle specifiche indicando i percorsi di directory, ad esempio */foto/*. È anche possibile applicare il filtro geografico a un singolo file (ad esempio */foto/città.png*). Sono consentite più regole. Dopo aver immesso una regola, viene visualizzata una riga vuota per poter immettere la regola successiva.

Ad esempio, tutti i filtri di percorso di directory seguenti sono validi:   
*/*                                 
*/Foto/*     
*/Photos/Strasbourg/*     
*/Foto/Strasburgo/città.png*

### <a name="define-the-type-of-action"></a>Definire il tipo di azione

Nell'elenco **Azione** selezionare **Consenti** o **Blocca**: 

- **Consenti**: solo agli utenti dei paesi o delle aree specificate è consentito l'accesso agli asset richiesti dal percorso ricorsivo.

- **Blocco**: agli utenti dei paesi specificati viene negato l'accesso agli asset richiesti dal percorso ricorsivo. Se non sono state configurate altre opzioni di filtro per paese/area geografica per tale percorso, a tutti gli altri utenti verrà consentito l'accesso.

Ad esempio, una regola di filtro geografico per bloccare il percorso */Foto/Strasburgo/* filtra i file seguenti:     
*http: \/ / \<endpoint> . azureedge.NET/Photos/Strasbourg/1000.jpg* 
 *http: \/ / \<endpoint> . azureedge.NET/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Definire i paesi/aree geografiche

Dall'elenco dei **codici paese** selezionare i paesi o le aree geografiche che si desidera bloccare o consentire per il percorso. 

Al termine della selezione dei paesi/aree geografiche, selezionare **Salva** per attivare la nuova regola di filtro geografico. 

![Screenshot mostra i codici paese da usare per bloccare o consentire paesi o aree geografiche.](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare una regola, selezionarla nell'elenco nella pagina **Filtro geografico** e quindi scegliere **Elimina**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Profili della rete CDN Premium di Azure fornita da Verizon

Per i profili della rete **CDN Premium di Azure di Verizon** , l'interfaccia utente per la creazione di una regola di filtro geografico è diversa:

1. Nel menu principale nel profilo della rete CDN di Azure selezionare **Gestisci**.

2. Dal portale di Verizon selezionare **HTTP Large** (HTTP esteso) e quindi selezionare **Country Filtering** (Filtro paese).

    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium.png" alt-text="Screenshot mostra come selezionare il filtro del paese nella rete CDN di Azure" border="true":::
  
3. Selezionare **Add Country Filter** (Aggiungi filtro paese).

4. Nel **passaggio uno:** immettere il percorso della directory. Selezionare **blocca** o **Aggiungi**, quindi fare clic su **Avanti**.

    > [!IMPORTANT]
    > Il nome dell'endpoint deve essere nel percorso.  Esempio: **/myendpoint8675/MyFolder**.  Sostituire **myendpoint8675** con il nome dell'endpoint.
    > 
    
5. Nel **passaggio due** selezionare uno o più paesi o aree geografiche dall'elenco. Selezionare **fine** per attivare la regola. 
    
    La nuova regola viene visualizzata nella tabella nella pagina **Country Filtering** (Filtro paese).
    
    :::image type="content" source="./media/cdn-filtering/cdn-geo-filtering-premium-rules.png" alt-text="Screenshot mostra la posizione in cui viene visualizzata la regola nel filtro dei paesi." border="true":::
 
### <a name="clean-up-resources"></a>Pulire le risorse
Nella tabella delle regole di filtro per paese/area geografica selezionare l'icona di eliminazione accanto a una regola per eliminarla o l'icona di modifica per modificarla.

## <a name="considerations"></a>Considerazioni
* Le modifiche apportate alla configurazione del filtro geografico non hanno effetto immediato:
   * La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Microsoft** viene in genere completata in 10 minuti. 
   * La propagazione dei profili di **rete CDN Standard di Azure con tecnologia Akamai** viene in genere completata entro un minuto. 
   * La propagazione dei profili della **rete CDN Standard di Azure con tecnologia Verizon** e della **rete CDN Premium di Azure con tecnologia Verizon** viene in genere completata in 10 minuti. 
 
* Questa funzionalità non supporta i caratteri jolly (ad esempio, *).

* La configurazione del filtro geografico associata al percorso relativo viene applicata in modo ricorsivo a tale percorso.

* È possibile applicare una sola regola allo stesso percorso relativo. Ovvero non è possibile creare più filtri di paese/area geografica che puntano allo stesso percorso relativo. Tuttavia, poiché i filtri paese/area geografica sono ricorsivi, una cartella può avere più filtri di paese/area geografica. In altre parole, è possibile assegnare a una sottocartella di una cartella configurata in precedenza un filtro di paese/area geografica diverso.

* La funzionalità di filtro geografico usa i codici paese per definire i paesi o le aree da cui una richiesta è consentita o bloccata per una directory protetta. Anche se i profili Akamai e Verizon supportano la maggior parte degli stessi codici paese, esistono alcune differenze. Per altre informazioni, vedere [codici paese](/previous-versions/azure/mt761717(v=azure.100))della rete CDN di Azure. 

