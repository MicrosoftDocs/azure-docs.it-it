---
title: 'Peering privato di Azure ExpressRoute: configurare la modalità di trasporto IPsec-host Windows'
description: Come abilitare la modalità trasporto IPSec tra macchine virtuali Windows di Azure e host Windows locali tramite il peering privato ExpressRoute usando oggetti Criteri di gruppo e unità organizzative.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: cfcc515787cee2bc90a2100e84337a3c96219d8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98017273"
---
# <a name="configure-ipsec-transport-mode-for-expressroute-private-peering"></a>Configurare la modalità trasporto IPSec per il peering privato ExpressRoute

Questo articolo consente di creare tunnel IPsec in modalità trasporto tramite peering privato di ExpressRoute. Il tunnel viene creato tra macchine virtuali di Azure che eseguono Windows e host Windows locali. La procedura descritta in questo articolo per questa configurazione utilizza oggetti Criteri di gruppo. Sebbene sia possibile creare questa configurazione senza usare unità organizzative e oggetti Criteri di gruppo (GPO). La combinazione di unità organizzative e oggetti Criteri di gruppo consente di semplificare il controllo dei criteri di sicurezza e di scalare rapidamente. I passaggi descritti in questo articolo presuppongono che si disponga già di una configurazione Active Directory e che si abbia familiarità con l'uso di ou e GPO.

## <a name="about-this-configuration"></a>Informazioni sulla configurazione

La configurazione nei passaggi seguenti usa una singola rete virtuale di Azure (VNet) con peering privato ExpressRoute. Tuttavia, questa configurazione può estendersi su altre reti locali e reti virtuali di Azure. Questo articolo consente di definire un criterio di crittografia IPsec che è possibile applicare a un gruppo di macchine virtuali di Azure o host locali. Queste macchine virtuali di Azure o host locali fanno parte della stessa unità organizzativa. Viene configurata la crittografia tra macchine virtuali di Azure (vm1 e vm2) e l'host locale host1 solo per il traffico HTTP destinato alla porta 8080. In base ai requisiti, è possibile creare tipi diversi di criteri IPSec.

### <a name="working-with-ous"></a>Uso di unità organizzative 

I criteri di sicurezza associati a un'unità organizzativa vengono inviati ai computer tramite un oggetto Criteri di gruppo. Ecco alcuni vantaggi che derivano dall'usare unità organizzative al posto di applicare criteri a un singolo host:

* L'associazione di criteri a un'unità organizzativa garantisce che i computer che appartengono alla stessa unità organizzativa ottengano gli stessi criteri.
* Se si modificano i criteri di sicurezza associati all'unità organizzativa, le modifiche vengono applicate a tutti gli host nell'unità organizzativa.

### <a name="diagrams"></a>Diagrammi

Il diagramma seguente illustra l'interconnessione e lo spazio indirizzi IP assegnato. Le macchine virtuali di Azure e l'host locale sono in esecuzione in Windows 2016. Le macchine virtuali di Azure e l'host locale host1 fanno parte dello stesso dominio. Le macchine virtuali di Azure e gli host locali possono risolvere correttamente i nomi tramite DNS.

[![1]][1]

Questo diagramma illustra i tunnel IPSec in transito nel peering privato ExpressRoute.

[![4]][4]

### <a name="working-with-ipsec-policy"></a>Uso di criteri IPSec

In Windows la crittografia è associata ai criteri IPSec. I criteri IPSec determinano il tipo di traffico IP che viene protetto e il meccanismo di sicurezza applicato ai pacchetti IP.
I **criteri IPSec** sono composti dagli elementi seguenti: **elenchi di filtri**, **operazioni di filtro** e **regole di sicurezza**.

Quando si configurano i criteri IPSec, è importante comprendere la terminologia seguente:

* **Criteri IPSec:** raccolta di regole. In un determinato momento può essere attivo ("assegnato") un solo criterio. Ogni criterio può avere una o più regole, che possono essere attive contemporaneamente. A un computer può venire assegnato solo un criterio IPSec attivo in un determinato momento. Tuttavia, all'interno di un criterio IPSec, è possibile definire più operazioni che possono essere eseguite in situazioni diverse. Ogni set di regole IPSec è associato a un elenco di filtri che influiscono sul tipo di traffico di rete a cui si applica la regola.

* **Elenchi di filtri:** gli elenchi di filtri sono gruppi di uno o più filtri. Un elenco può contenere più filtri. Un filtro definisce se la comunicazione viene bloccata, consentita o protetta in base ai criteri seguenti: intervalli di indirizzi IP, protocolli o anche porte specifiche. Ogni filtro corrisponde a un particolare set di condizioni, ad esempio i pacchetti inviati da una subnet specifica a un determinato computer su una porta di destinazione specifica. Quando le condizioni di rete corrispondono a uno o più filtri, l'elenco di filtri viene attivato. Ogni filtro è definito all'interno di un elenco di filtri specifico. I filtri non possono essere condivisi tra elenchi di filtri. Un determinato elenco di filtri può tuttavia essere incorporato in diversi criteri IPSec. 

* **Operazioni di filtro:** un metodo di sicurezza definisce un set di algoritmi di sicurezza, protocolli e chiavi offerti da un computer durante le negoziazioni IKE. Le operazioni di filtro sono elenchi di metodi di sicurezza, classificati in ordine di preferenza.  Quando un computer negozia una sessione IPSec, accetta o invia proposte in base all'impostazione di sicurezza archiviata nell'elenco di operazioni di filtro.

* **Regole di sicurezza:** le regole controllano come e quando i criteri IPSec proteggono la comunicazione. Vengono usati l'**elenco di filtri** e le **operazioni di filtro** per generare una regola IPSec per creare la connessione IPSec. Ogni criterio può avere una o più regole, che possono essere attive contemporaneamente. Ogni regola contiene un elenco di filtri IP e una raccolta di operazioni di sicurezza che vengono eseguite quando si verifica una corrispondenza con tale elenco di filtri:
  * Operazioni di filtro IP
  * Metodi di autenticazione
  * Impostazioni tunnel IP
  * Tipi di connessioni

[![5]][5]

## <a name="before-you-begin"></a>Prima di iniziare

Accertarsi che siano soddisfatti i prerequisiti seguenti:

* È necessario disporre di una configurazione funzionante di Active Directory da usare per implementare le impostazioni di Criteri di gruppo. Per altre informazioni sugli oggetti Criteri di gruppo, vedere [Group Policy Objects](/previous-versions/windows/desktop/Policy/group-policy-objects) (Oggetti Criteri di gruppo).

* È necessario avere un circuito ExpressRoute attivo.
  * Per informazioni sulla creazione di un circuito ExpressRoute, vedere [Creare un circuito ExpressRoute ](expressroute-howto-circuit-arm.md). 
  * Verificare che il circuito sia abilitato dal provider di connettività. 
  * Verificare che per il circuito sia configurato il peering privato Azure. Vedere l'articolo relativo alla [configurazione del routing](expressroute-howto-routing-arm.md) per istruzioni relative al routing. 
  * Verificare di disporre di una rete virtuale e di un gateway di rete virtuale e che ne sia stato effettuato il provisioning completo. Seguire le istruzioni per [creare un gateway di rete virtuale per ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Un gateway di rete virtuale per ExpressRoute usa 'ExpressRoute' come GatewayType, non VPN.

* Il gateway di rete virtuale ExpressRoute deve essere connesso al circuito ExpressRoute. Per altre informazioni, vedere [Connettere una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).

* Verificare che le macchine virtuali Windows di Azure siano distribuite nella rete virtuale.

* Verificare che sia disponibile la connettività tra gli host locali e le macchine virtuali di Azure.

* Verificare che le macchine virtuali Windows di Azure e gli host locali possano usare DNS per risolvere i nomi in modo corretto.

### <a name="workflow"></a>Flusso di lavoro

1. Creare un oggetto Criteri di gruppo e associarlo all'unità organizzativa.
2. Definire un'**operazione di filtro** IPSec.
3. Definire un **elenco di filtri** IPSec.
4. Creare un criterio IPSec con **regole di sicurezza**.
5. Assegnare l'oggetto Criteri di gruppo IPSec all'unità organizzativa.

### <a name="example-values"></a>Valori di esempio

* **Nome di dominio:** ipsectest.com

* **Unità organizzativa:** IPSecOU

* **Computer Windows locale:** host1

* **Macchine virtuali Windows di Azure:** vm1, vm2

## <a name="1-create-a-gpo"></a><a name="creategpo"></a>1. creare un oggetto Criteri di gruppo

1. Creare un nuovo oggetto Criteri di gruppo collegato a un'unità organizzativa aprendo lo snap-in Gestione Criteri di gruppo. Individuare quindi l'unità organizzativa a cui verrà collegato l'oggetto Criteri di gruppo. Nell'esempio il nome dell'unità organizzativa è **IPSecOU**. 

   [![9]][9]
2. Nello snap-in Gestione Criteri di gruppo selezionare l'unità organizzativa e fare clic con il pulsante destro del mouse. Nell'elenco a discesa selezionare "**Crea un oggetto Criteri di gruppo in questo dominio e collegarlo qui...**".

   [![10]][10]
3. Assegnare all'oggetto Criteri di gruppo un nome intuitivo in modo che sia possibile individuarlo facilmente in seguito. Selezionare **OK** per creare e collegare l'oggetto Criteri di gruppo.

   [![11]][11]

## <a name="2-enable-the-gpo-link"></a><a name="enablelink"></a>2. abilitare il collegamento dell'oggetto Criteri di gruppo

Per applicare l'oggetto Criteri di gruppo all'unità organizzativa, non è sufficiente che l'oggetto Criteri di gruppo sia collegato all'unità organizzativa, ma è anche necessario abilitare il collegamento.

1. Individuare l'oggetto Criteri di gruppo creato, fare clic con pulsante destro del mouse e scegliere **Modifica** dal menu a discesa.
2. Per applicare l'oggetto Criteri di gruppo all'unità organizzativa, selezionare **Collegamento abilitato**.

   [![12]][12]

## <a name="3-define-the-ip-filter-action"></a><a name="filteraction"></a>3. definire l'azione del filtro IP

1. Dall'elenco a discesa, fare clic con il pulsante destro del mouse **su criteri di sicurezza IP in Active Directory**, quindi selezionare **Gestisci elenchi di filtri IP e azioni filtro...**.

   [![15]][15]
2. Nella scheda "**Gestisci azioni filtro**" selezionare **Aggiungi**.

   [![16]][16]

3. Nella **procedura guidata per l'azione filtro sicurezza IP** selezionare **Avanti**.

   [![17]][17]
4. Assegnare all'operazione di filtro un nome intuitivo in modo che sia possibile trovarla in seguito. In questo esempio il nome dell'operazione di filtro è **myEncryption**. È anche possibile aggiungere una descrizione. Quindi selezionare **Avanti**.

   [![18]][18]
5. L'opzione **Negozia sicurezza** consente di definire il comportamento se non è possibile abilitare IPSec per la comunicazione con un altro computer. Selezionare **Negozia sicurezza**, quindi fare clic su **Avanti**.

   [![19]][19]
6. Nella pagina **comunicazione con i computer che non supportano IPSec** selezionare **non consentire comunicazioni non protette**, quindi selezionare **Avanti**.

   [![20]][20]
7. Nella pagina **traffico IP e sicurezza** selezionare **personalizzato**, quindi selezionare **impostazioni.**

   [![21]][21]
8. Nella pagina **Impostazioni personalizzate metodo di sicurezza** selezionare **Integrità dati con crittografia (ESP): SHA1, 3DES**. Quindi selezionare **OK**.

   [![22]][22]
9. Nella pagina **Gestione operazioni filtro** è possibile vedere che il filtro **myEncryption** è stato aggiunto correttamente. Selezionare **Chiudi**.

   [![23]][23]

## <a name="4-define-an-ip-filter-list"></a><a name="filterlist1"></a>4. definire un elenco di filtri IP

Creare un elenco di filtri che specifica il traffico HTTP crittografato destinato alla porta 8080.

1. Per definire il tipo di traffico che deve essere crittografato, usare un **elenco di filtri IP**. Nella scheda **Gestisci elenchi di filtri IP** selezionare **Aggiungi** per aggiungere un nuovo elenco di filtri IP.

   [![24]][24]
2. Nel campo **Nome** digitare un nome per l'elenco di filtri IP. Ad esempio, **azure-onpremises-HTTP8080**. Quindi selezionare **Aggiungi**.

   [![25]][25]
3. Nella pagina **Descrizione filtro IP e proprietà Speculare** selezionare **Speculare**. L'impostazione speculare consente di definire una corrispondenza con i pacchetti che viaggiano in entrambe le direzioni, permettendo la comunicazione bidirezionale. Selezionare quindi **Avanti**.

   [![26]][26]
4. Nella pagina **Origine traffico IP**, nell'elenco a discesa **Indirizzo origine** scegliere **Subnet o indirizzo IP specifico**. 

   [![27]][27]
5. Specificare l'indirizzo **IP o la subnet** dell'indirizzo di origine: del traffico IP, quindi fare clic su **Avanti**.

   [![28]][28]
6. Specificare il valore di Subnet o indirizzo IP in **Indirizzo di destinazione**. Quindi selezionare **Avanti**.

   [![29]][29]
7. Nella pagina **Tipo protocollo IP** selezionare **TCP**. Quindi selezionare **Avanti**.

   [![30]][30]
8. Nella pagina **Porta protocollo IP** selezionare **Da qualsiasi porta** e **A questa porta**. Digitare **8080** nella casella di testo. Queste impostazioni specificano che solo il traffico HTTP destinato alla porta 8080 verrà crittografato. Quindi selezionare **Avanti**.

   [![31]][31]
9. Visualizzare l'elenco di filtri IP.  La configurazione dell'elenco di filtri IP **azure-onpremises-HTTP8080** attiva la crittografia per tutto il traffico che soddisfa i criteri seguenti:

   * Qualsiasi indirizzo di origine in 10.0.1.0/24 (Subnet2 Azure)
   * Qualsiasi indirizzo di destinazione in 10.2.27.0/25 (subnet locale)
   * Protocollo TCP
   * Porta di destinazione 8080

   [![32]][32]

## <a name="5-edit-the-ip-filter-list"></a><a name="filterlist2"></a>5. modificare l'elenco di filtri IP

Per crittografare lo stesso tipo di traffico dall'host locale alla macchina virtuale di Azure, è necessario un secondo filtro IP. Seguire gli stessi passaggi usati per configurare il primo filtro IP e creare un nuovo filtro IP. Le uniche differenze sono la subnet di origine e la subnet di destinazione.

1. Per aggiungere un nuovo filtro IP all'elenco di filtri IP, selezionare **Modifica**.

   [![33]][33]
2. Nella pagina dell' **elenco di filtri IP** selezionare **Aggiungi**.

   [![34]][34]
3. Creare un secondo filtro IP usando le impostazioni nell'esempio seguente:

   [![35]][35]
4. Dopo aver creato il secondo filtro IP, l'elenco di filtri IP sarà simile al seguente:

   [![36]][36]

Se è necessaria la crittografia tra un percorso locale e una subnet di Azure per proteggere un'applicazione. Invece di modificare l'elenco di filtri IP esistente, è possibile aggiungere un nuovo elenco di filtri IP. L'associazione di due o più elenchi di filtri IP allo stesso criterio IPsec offrirà maggiore flessibilità. È possibile modificare o rimuovere un elenco di filtri IP senza influire sugli altri elenchi di filtri IP.

## <a name="6-create-an-ipsec-security-policy"></a><a name="ipsecpolicy"></a>6. creare un criterio di sicurezza IPsec 

Creare un criterio IPSec con regole di sicurezza.

1. Selezionare la voce **Criteri di sicurezza IP su Active Directory** associata all'unità organizzativa. Fare clic con il pulsante destro del mouse e scegliere **Crea criterio di sicurezza IP**.

   [![37]][37]
2. Assegnare un nome al criterio di sicurezza. Ad esempio, **policy-azure-onpremises**. Quindi selezionare **Avanti**.

   [![38]][38]
3. Selezionare **Avanti** senza selezionare la casella di controllo.

   [![39]][39]
4. Verificare che la casella di controllo **modifica proprietà** sia selezionata, quindi fare clic su **fine**.

   [![40]][40]

## <a name="7-edit-the-ipsec-security-policy"></a><a name="editipsec"></a>7. modificare i criteri di sicurezza IPsec

Aggiungere i criteri IPSec all'**elenco di filtri IP** e all'**operazione di filtro** configurati in precedenza.

1. Nella scheda **regole** proprietà criteri HTTP selezionare **Aggiungi**.

   [![41]][41]
2. Nella pagina di benvenuto selezionare **Avanti**.

   [![42]][42]
3. Una regola consente di definire la modalità IPSec: modalità tunnel o modalità trasporto.

   * In modalità tunnel il pacchetto originale viene incapsulato da un set di intestazioni IP. La modalità tunnel protegge le informazioni di routing interne crittografando l'intestazione IP del pacchetto originale. La modalità tunnel viene spesso implementata tra i gateway in scenari VPN da sito a sito. La modalità tunnel nella maggior parte dei casi viene usata per la crittografia end-to-end tra host.

   * La modalità trasporto crittografa solo il payload e il trailer ESP, mentre l'intestazione IP del pacchetto originale non viene crittografata. Nella modalità trasporto, l'origine IP e la destinazione IP dei pacchetti restano invariate.

   Selezionare **questa regola non specifica un tunnel**, quindi selezionare **Avanti**.

   [![43]][43]
4. **Tipo di rete** definisce la connessione di rete associata ai criteri di sicurezza. Selezionare **tutte le connessioni di rete** e quindi fare clic su **Avanti**.

   [![44]][44]
5. Selezionare l'elenco di filtri IP creato in precedenza,  **Azure-onlocale-HTTP8080** e quindi fare clic su **Avanti**.

   [![45]][45]
6. Selezionare l'operazione di filtro esistente **myEncryption** creata in precedenza.

   [![46]][46]
7. Windows supporta quattro tipi diversi di autenticazioni: Kerberos, certificati, NTLMv2 e chiave già condivisa. Poiché gli host aggiunti a un dominio sono in uso, selezionare **Active Directory predefinito (protocollo Kerberos V5)**, quindi selezionare **Avanti**.

   [![47]][47]
8. Il nuovo criterio crea la regola di sicurezza: **azure-onpremises-HTTP8080**. Selezionare **OK**.

   [![48]][48]

Il criterio IPSec richiede che tutte le connessioni HTTP sulla porta di destinazione 8080 usino la modalità trasporto IPSec. Poiché HTTP è un protocollo di testo non crittografato, con i criteri di sicurezza abilitati, assicura che i dati vengano crittografati quando vengono trasferiti tramite il peering privato di ExpressRoute. I criteri IPsec per Active Directory sono più complessi da configurare rispetto a Windows Firewall con sicurezza avanzata. Tuttavia, consente una maggiore personalizzazione della connessione IPsec.

## <a name="8-assign-the-ipsec-gpo-to-the-ou"></a><a name="assigngpo"></a>8. assegnare l'oggetto Criteri di gruppo IPsec all'unità organizzativa

1. Visualizzare il criterio. Il criterio di gruppo di sicurezza è definito ma non ancora assegnato.

   [![49]][49]
2. Per assegnare il criterio di gruppo di sicurezza all'unità organizzativa **IPSecOU**, fare clic con il pulsante destro del mouse sul criterio di sicurezza e scegliere **Assegna**.
   Il criterio di gruppo di sicurezza verrà assegnato a ogni computer appartenente all'unità organizzativa.

   [![50]][50]

## <a name="check-traffic-encryption"></a><a name="checktraffic"></a>Controllare la crittografia del traffico

Per testare la crittografia applicata dall'oggetto Criteri di gruppo all'unità organizzativa, installare IIS in tutte le macchine virtuali di Azure e in host1. Ogni istanza di IIS è personalizzata per rispondere alle richieste HTTP sulla porta 8080.
Per verificare la crittografia, è possibile installare uno sniffer di rete (ad esempio Wireshark) in tutti i computer nell'unità organizzativa.
Uno script di PowerShell funziona come un client HTTP per generare richieste HTTP sulla porta 8080:

```powershell
$url = "http://10.0.1.20:8080"
while ($true) {
try {
[net.httpWebRequest]
$req = [net.webRequest]::create($url)
$req.method = "GET"
$req.ContentType = "application/x-www-form-urlencoded"
$req.TimeOut = 60000

$start = get-date
[net.httpWebResponse] $res = $req.getResponse()
$timetaken = ((get-date) - $start).TotalMilliseconds

Write-Output $res.Content
Write-Output ("{0} {1} {2}" -f (get-date), $res.StatusCode.value__, $timetaken)
$req = $null
$res.Close()
$res = $null
} catch [Exception] {
Write-Output ("{0} {1}" -f (get-date), $_.ToString())
}
$req = $null

# uncomment the line below and change the wait time to add a pause between requests
#Start-Sleep -Seconds 1
}

```
L'acquisizione di rete seguente mostra i risultati per l'host locale host1 con il filtro ESP in modo da attivare la corrispondenza solo con il traffico crittografato:

[![51]][51]

Se si esegue lo script di PowerShell in locale (client HTTP), l'acquisizione di rete nella macchina virtuale di Azure Mostra una traccia simile.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).

<!--Image References-->

[1]: ./media/expressroute-howto-ipsec-transport-private-windows/network-diagram.png "Diagramma di rete della modalità trasporto IPSec tramite ExpressRoute"
[4]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-interesting-traffic.png "Traffico IPSec"
[5]: ./media/expressroute-howto-ipsec-transport-private-windows/windows-ipsec.png "Criteri IPSec Windows"
[9]: ./media/expressroute-howto-ipsec-transport-private-windows/ou.png "Unità organizzativa in Criteri di gruppo"
[10]: ./media/expressroute-howto-ipsec-transport-private-windows/create-gpo-ou.png "Creazione di un oggetto Criteri di gruppo associato all'unità organizzativa"
[11]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-name.png "Assegnazione di un nome all'oggetto Criteri di gruppo associato all'unità organizzativa"
[12]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-gpo.png "Modifica dell'oggetto Criteri di gruppo"
[15]: ./media/expressroute-howto-ipsec-transport-private-windows/manage-ip-filter-list-filter-actions.png "Gestione di elenchi di filtri IP e operazioni di filtro"
[16]: ./media/expressroute-howto-ipsec-transport-private-windows/add-filter-action.png "Aggiunta di un'operazione di filtro"
[17]: ./media/expressroute-howto-ipsec-transport-private-windows/action-wizard.png "Impostazione guidata operazione"
[18]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-name.png "Nome operazione di filtro"
[19]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action.png "Operazione di filtro"
[20]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-action-no-ipsec.png "Definizione del comportamento se viene stabilita una connessione non sicura"
[21]: ./media/expressroute-howto-ipsec-transport-private-windows/security-method.png "Meccanismo di sicurezza"
[22]: ./media/expressroute-howto-ipsec-transport-private-windows/custom-security-method.png "Metodo di sicurezza personalizzato"
[23]: ./media/expressroute-howto-ipsec-transport-private-windows/filter-actions-list.png "Elenco operazioni di filtro"
[24]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-ip-filter.png "Aggiunta di un nuovo elenco di filtri IP"
[25]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-http-traffic.png "Aggiunta del traffico HTTP al filtro IP"
[26]: ./media/expressroute-howto-ipsec-transport-private-windows/match-both-direction.png "Corrispondenza con pacchetti in entrambe le direzioni"
[27]: ./media/expressroute-howto-ipsec-transport-private-windows/source-address.png "Selezione della subnet di origine"
[28]: ./media/expressroute-howto-ipsec-transport-private-windows/source-network.png "Rete di origine"
[29]: ./media/expressroute-howto-ipsec-transport-private-windows/destination-network.png "Rete di destinazione"
[30]: ./media/expressroute-howto-ipsec-transport-private-windows/protocol.png "Protocollo"
[31]: ./media/expressroute-howto-ipsec-transport-private-windows/source-port-and-destination-port.png "Porta di origine e porta di destinazione"
[32]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list.png "Elenco di filtri"
[33]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-for-http.png "Elenco di filtri IP con traffico HTTP"
[34]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-add-second-entry.png "Aggiunta di un secondo filtro IP"
[35]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-second-entry.png "Elenco di filtri IP - seconda voce"
[36]: ./media/expressroute-howto-ipsec-transport-private-windows/ip-filter-list-2entries.png "Elenco di filtri IP - seconda voce"
[37]: ./media/expressroute-howto-ipsec-transport-private-windows/create-ip-security-policy.png "Creazione criterio di sicurezza IP"
[38]: ./media/expressroute-howto-ipsec-transport-private-windows/ipsec-policy-name.png "Nome del criterio IPSec"
[39]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-wizard.png "Creazione guidata criterio IPSec"
[40]: ./media/expressroute-howto-ipsec-transport-private-windows/edit-security-policy.png "Modifica del criterio IPSec"
[41]: ./media/expressroute-howto-ipsec-transport-private-windows/add-new-rule.png "Aggiunta di una nuova regola di sicurezza al criterio IPSec"
[42]: ./media/expressroute-howto-ipsec-transport-private-windows/create-security-rule.png "Creazione di una nuova regola di sicurezza"
[43]: ./media/expressroute-howto-ipsec-transport-private-windows/transport-mode.png "Modalità trasporto"
[44]: ./media/expressroute-howto-ipsec-transport-private-windows/network-type.png "Tipo di rete"
[45]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-list.png "Selezione dell'elenco di filtri IP esistente"
[46]: ./media/expressroute-howto-ipsec-transport-private-windows/selection-filter-action.png "Selezione dell'operazione di filtro esistente"
[47]: ./media/expressroute-howto-ipsec-transport-private-windows/authentication-method.png "Selezione del metodo di autenticazione"
[48]: ./media/expressroute-howto-ipsec-transport-private-windows/security-policy-completed.png "Fine del processo di creazione del criterio di sicurezza"
[49]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-not-assigned.png "Criterio IPSec collegato all'oggetto Criteri di gruppo ma non assegnato"
[50]: ./media/expressroute-howto-ipsec-transport-private-windows/gpo-assigned.png "Criterio IPSec assegnato all'oggetto Criteri di gruppo"
[51]: ./media/expressroute-howto-ipsec-transport-private-windows/encrypted-traffic.png "Acquisizione del traffico IPSec crittografato"