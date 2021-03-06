---
title: 'Esercitazione: Integrazione di Azure Active Directory con Lucidchart | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e Lucidchart.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 5d5b07e761d5ed38cb2083054708265189bdd72f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651578"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con LucidChart

Questa esercitazione descrive come integrare Lucidchart con Azure Active Directory (Azure AD). Integrando Lucidchart con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a Lucidchart.
* Abilitare gli utenti per l'accesso automatico a Lucidchart con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di Lucidchart abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* Lucidchart supporta l'accesso SSO avviato da **SP**
* Lucidchart supporta il provisioning utenti **JIT**

## <a name="add-lucidchart-from-the-gallery"></a>Aggiungere LucidChart dalla raccolta

Per configurare l'integrazione di Lucidchart in Azure AD, è necessario aggiungere Lucidchart dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **Lucidchart** nella casella di ricerca.
1. Selezionare **Lucidchart** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-lucidchart"></a>Configurare e testare Azure AD SSO per LucidChart

Configurare e testare l'accesso SSO di Azure AD con Lucidchart usando un utente di test di nome **B.Simon**. Per consentire il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un utente di Azure AD e l'utente correlato in Lucidchart.

Per configurare e testare Azure AD SSO con LucidChart, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    * **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    * **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di Lucidchart](#configure-lucidchart-sso)**: per configurare le impostazioni di Single Sign-On sul lato applicazione.
    * **[Creare l'utente di test di Lucidchart](#create-lucidchart-test-user)**: per avere una controparte di B.Simon in Lucidchart collegata alla rappresentazione dell'utente in Azure AD.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **LucidChart** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

   Nella casella di testo **URL di accesso** digitare l'URL: `https://chart2.office.lucidchart.com/saml/sso/azure`

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura Lucidchart** copiare gli URL appropriati in base alle esigenze.

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

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendole l'accesso a Lucidchart.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **Lucidchart**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è &quot;Accesso predefinito&quot;.
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name=&quot;configure-lucidchart-sso&quot;></a>Configurare l'accesso Single Sign-On di Lucidchart

1. In un'altra finestra del Web browser accedere al sito aziendale di Lucidchart come amministratore.

2. Scegliere **Team** dal menu in alto.

    ![Team](./media/lucidchart-tutorial/ic791190.png &quot;Team")

3. Fare clic su **Applications (Applicazioni) \> Manage SAML (Gestisci SAML)**.

    ![Manage SAML](./media/lucidchart-tutorial/ic791191.png "Gestisci SAML")

4. Nella pagina **SAML Authentication Settings** eseguire la procedura seguente:

    a. Selezionare **Enable SAML Authentication** e quindi fare clic su **Optional**.

    ![Impostazioni di autenticazione SAML](./media/lucidchart-tutorial/ic791192.png "Impostazioni di autenticazione SAML")

    b. Nella casella di testo **Domain** digitare il nome di dominio e quindi fare clic su **Change Certificate**.

    ![Cambia certificato](./media/lucidchart-tutorial/ic791193.png "Change Certificate")

    c. Aprire il file di metadati scaricato, copiare il contenuto e incollarlo nella casella di testo **Upload Metadata**.

    ![Carica metadati](./media/lucidchart-tutorial/ic791194.png "Upload Metadata")

    d. Selezionare **Automatically Add new users to the team** (Aggiungi automaticamente nuovi utenti al team) e quindi fare clic su **Save changes** (Salva modifiche).

    ![Salva modifiche](./media/lucidchart-tutorial/ic791195.png "Salva modifiche")

### <a name="create-lucidchart-test-user"></a>Creare l'utente di test di Lucidchart

Non è richiesta alcuna operazione per configurare il provisioning degli utenti in Lucidchart.  Quando un utente assegnato tenta di accedere a Lucidchart utilizzando il pannello di accesso, Lucidchart controlla se l'utente esiste.  

Se l’account utente non è ancora disponibile, viene creato automaticamente da Lucidchart.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento all'URL di accesso di LucidChart, in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di LucidChart e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro LucidChart in My Apps (app personali), si dovrebbe accedere automaticamente al LucidChart per il quale si configura l'accesso SSO. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

 Dopo aver configurato Lucidchart, è possibile applicare i controlli sessione che consentono di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. I controlli sessione costituiscono un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)