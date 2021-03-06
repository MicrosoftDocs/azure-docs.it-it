---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con FAX. PIÙ | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e FAX. Più.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 528e1056574379f922b5de15f442b7fd92d8cf8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592456"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-faxplus"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con FAX. PIÙ

In questa esercitazione si apprenderà come integrare il FAX. PIÙ con Azure Active Directory (Azure AD). Quando si integra il FAX. Con Azure AD è inoltre possibile:

* Controllare in Azure AD chi ha accesso al FAX. Più.
* Consentire agli utenti di accedere automaticamente ai FAX. Con l'account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* FAX. Sottoscrizione di più Single Sign-On abilitata per l'accesso SSO.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* FAX. PLUS supporta il protocollo SSO avviato da **SP e IDP** .
* FAX. PLUS supporta il provisioning degli utenti **just-in-Time** .

> [!NOTE]
> Dal momento che l'identificatore di questa applicazione è un valore stringa fisso, è possibile configurare una sola istanza in un solo tenant.

## <a name="add-faxplus-from-the-gallery"></a>Aggiungi FAX. PIÙ dalla raccolta

Per configurare l'integrazione del FAX. Inoltre, in Azure AD, è necessario aggiungere FAX. Dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **fax. Nella casella di ricerca** .
1. Selezionare **fax. Dal pannello dei risultati** e quindi Aggiungi l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-faxplus"></a>Configurare e testare Azure AD SSO per FAX. PIÙ

Configurare e testare Azure AD SSO con FAX. Con l'uso di un utente test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in FAX. Più.

Per configurare e testare Azure AD SSO con FAX. Eseguire inoltre la procedura seguente:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare il fax. PIÙ SSO](#configure-faxplus-sso)** : per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Crea fax. Utente test PLUS](#create-faxplus-test-user)** : per avere una controparte di B. Simon in fax. È inoltre collegato alla rappresentazione Azure AD dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nel portale di Azure, sul **fax.** Nella pagina di integrazione dell'applicazione Plus trovare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** l'utente non deve eseguire alcuna operazione perché l'app è già preintegrata in Azure.

1. Fare clic su **Impostare URL aggiuntivi** e seguire questa procedura se si vuole configurare l'applicazione in modalità avviata da **SP**:

    Nella casella di testo **URL di accesso** digitare l'URL: `https://www.fax.plus/login`

1. Fare clic su **Salva**.

1. FAX. L'applicazione PLUS prevede un formato specifico per le asserzioni SAML. è quindi necessario aggiungere mapping di attributi personalizzati alla configurazione degli attributi del token SAML. Lo screenshot seguente mostra l'elenco degli attributi predefiniti.

    ![image](common/default-attributes.png)

1. Oltre al servizio FAX, L'applicazione PLUS prevede che vengano passati altri attributi nella risposta SAML, illustrati di seguito. Anche questi attributi vengono prepopolati, ma è possibile esaminarli in base ai requisiti.
    
    | Nome | Attributo di origine|
    | --------------- | --------- |
    | firstname  | user.givenname |
    | lastname   | user.surname   |

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** individuare **Certificato (Base64)** e selezionare **Scarica** per scaricare il certificato e salvarlo nel computer.

    ![Collegamento di download del certificato](common/certificatebase64.png)

1. Sul **fax di installazione. Sezione più** , copiare gli URL appropriati in base ai requisiti.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Creare un utente di test di Azure AD

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Assegnare l'utente di test di Azure AD

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso al FAX. Più.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **fax. PIÙ**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è &quot;Accesso predefinito&quot;.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name=&quot;configure-faxplus-sso&quot;></a>Configurare il FAX. PIÙ SSO

1. Accedere al FAX. PIÙ sito aziendale come amministratore.

2. Passare alla sezione **sicurezza** del profilo di amministrazione e scorrere fino a **Avanzate**.

3. Nel pannello **configurazione** fare clic sul pulsante **Attiva Single Sign-on** e seguire questa procedura.
    
    ![Account](./media/fax.plus-tutorial/configuration.png &quot;Account") 

    a. Nella casella di testo **ID entità** incollare il valore di **Identificatore Azure AD** copiato dal portale di Azure.

    b. Nella casella di testo **Single Sign-On URL** (URL di accesso SSO) incollare il valore di **URL di accesso** copiato dal portale di Azure.

    c. Aprire nel Blocco note il file **Certificato (Base64)** scaricato dal portale di Azure e incollarne il contenuto nella casella di testo **Certificato X.509**.

    d. Se si desidera eseguire l'accesso tramite SSO, abilitare **la casella di controllo Consenti accesso SSO solo per l'utente amministratore** . 

    e. Fare clic su **Conferma**.

### <a name="create-faxplus-test-user"></a>Crea FAX. Utente test PLUS

In questa sezione viene creato un utente di nome Britta Simon in FAX. Più. FAX. PLUS supporta il provisioning degli utenti JIT, che è abilitato per impostazione predefinita. Non è necessario alcun intervento dell'utente in questa sezione. Se un utente non esiste già nel FAX. Ne viene creato uno nuovo dopo l'autenticazione.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

#### <a name="sp-initiated"></a>Avviato da SP:

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà reindirizzato al FAX. PIÙ URL di accesso in cui è possibile avviare il flusso di accesso.  

* Passare a FAX. Con l'URL di accesso diretto e avviare il flusso di accesso da questa posizione.

#### <a name="idp-initiated"></a>Avviato da IDP:

* Fare clic su **test questa applicazione** in portale di Azure e si dovrebbe accedere automaticamente al fax. PIÙ per i quali si configura l'accesso SSO. 

È anche possibile usare App personali Microsoft per testare l'applicazione in qualsiasi modalità. Quando si fa clic sul FAX. Riquadro più nelle app personali, se configurato in modalità SP, si verrà reindirizzati alla pagina di accesso dell'applicazione per avviare il flusso di accesso e, se configurato in modalità IDP, si dovrebbe accedere automaticamente al FAX. PIÙ per i quali si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato il FAX. Inoltre, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).