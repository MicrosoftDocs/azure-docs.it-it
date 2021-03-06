---
title: 'Esercitazione: Integrazione di Azure Active Directory con M-Files | Documentazione Microsoft'
description: Informazioni su come configurare l'accesso Single Sign-On tra Azure Active Directory e M-Files.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 680380722746522a0eb3fe6518452472be952075
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482823"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Esercitazione: Integrazione di Azure Active Directory con M-Files

Questa esercitazione illustra come integrare M-Files con Azure Active Directory (Azure AD). Integrando M-Files con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a M-Files.
* Abilitare gli utenti per l'accesso automatico a M-Files con gli account Azure AD personali.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di M-Files abilitata per l'accesso Single Sign-On (SSO).

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* M-Files supporta l'accesso SSO avviato da **SP.**

## <a name="add-m-files-from-the-gallery"></a>Aggiungere M-Files dalla raccolta

Per configurare l'integrazione di M-Files in Azure AD, è necessario aggiungere M-Files dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta digitare** **M-Files** nella casella di ricerca.
1. Selezionare **M-Files nel** pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.

## <a name="configure-and-test-azure-ad-sso-for-m-files"></a>Configurare e testare Azure AD SSO per M-Files

Configurare e testare Azure AD SSO con M-Files usando un utente di test **di nome B.Simon**. Per il funzionamento dell'accesso Single Sign-On, è necessario stabilire una relazione di collegamento tra un Azure AD utente e l'utente correlato in M-Files.

Per configurare e testare Azure AD SSO con M-Files, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare l'accesso Single Sign-On di M-Files:](#configure-m-files-sso)** per configurare le impostazioni di Single Sign-On sul lato applicazione.
    1. **[Creare l'utente di test di M-Files:](#create-m-files-test-user)** per avere una controparte di B.Simon in M-Files collegata alla Azure AD dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina portale di Azure di integrazione **dell'applicazione M-Files** individuare la **sezione** Gestione e selezionare **Single Sign-On.**
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

4. Nella sezione **Configurazione SAML di base** seguire questa procedura:

    a. Nella casella di testo **URL di accesso** digitare un URL nel formato seguente: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. Nella casella di testo **Identificatore (ID entità)** digitare un URL nel formato seguente: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > Poiché questi non sono i valori reali, è necessario aggiornarli con l'ID e l'URL di accesso effettivi. Per ottenere tali valori, contattare il [team di supporto client di M-Files](mailto:support@m-files.com). È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

5. Nella pagina **Configura l'accesso Single Sign-On con SAML**, nella sezione **Certificato di firma SAML**, fare clic su **Scarica** per scaricare il file **XML metadati federazione** definito dalle opzioni specificate in base ai propri requisiti e salvarlo in questo computer.

    ![Collegamento di download del certificato](common/metadataxml.png)

6. Nella sezione **Configura M-Files** copiare gli URL appropriati in base alle esigenze.

    ![Copiare gli URL di configurazione](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creare un utente di test di Azure AD 

In questa sezione verrà creato un utente di test di nome B.Simon nel portale di Azure.

1. Nel riquadro sinistro del portale di Azure selezionare **Azure Active Directory**, **Utenti** e quindi **Tutti gli utenti**.
1. Selezionare **Nuovo utente** in alto nella schermata.
1. In **Proprietà utente** seguire questa procedura:
   1. Nel campo **Nome** immettere `B.Simon`.  
   1. Nel campo **Nome utente** immettere username@companydomain.extension. Ad esempio: `B.Simon@contoso.com`.
   1. Selezionare la casella di controllo **Mostra password** e quindi prendere nota del valore visualizzato nella casella **Password**.
   1. Fare clic su **Crea**.

### <a name="assign-the-azure-ad-test-user"></a>Assegnare l'utente di test di Azure AD

In questa sezione si abiliterà B.Simon all'uso dell'accesso Single Sign-On di Azure concedendo l'accesso a M-Files.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco di applicazioni selezionare **M-Files**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-m-files-sso"></a>Configurare l'accesso Single Sign-On di M-Files

1. Per ottenere la configurazione dell'accesso Single Sign-On per l'applicazione, contattare il [team di supporto di M-Files](mailto:support@m-files.com) e fornire i metadati scaricati.
   
    >[!NOTE]
    >Se si desidera configurare l'accesso Single Sign-On per l'applicazione desktop M-Files, attenersi alla procedura seguente. Non sono necessarie operazioni aggiuntive se si desidera configurare l'accesso Single Sign-On per la versione Web di M-Files.  

1. Per configurare l'applicazione desktop M-Files in modo da abilitare l'accesso Single Sign-On con Azure AD attenersi alla procedura seguente. Per scaricare M-Files, passare alla pagina di [download di M-Files](https://www.m-files.com/customers/product-downloads/download-update-links/).

1. Aprire la finestra **M-Files Desktop Settings** (Impostazioni M-Files Desktop). Fare quindi clic su **Aggiungi**.
   
    ![Screenshot che mostra la sezione M-Files Desktop Settings in cui è possibile selezionare Aggiungi.](./media/m-files-tutorial/settings.png)

1. Nella finestra **Document Vault Connection Properties** (Proprietà connessione insieme di credenziali del documento) eseguire le seguenti operazioni:
   
    ![Screenshot che mostra la finestra Document Vault Connection Properties in cui è possibile immettere i valori descritti.](./media/m-files-tutorial/general.png)  

    Nella sezione Server digitare i valori seguenti:  

    a. Per **Nome** digitare `<tenant-name>.cloudvault.m-files.com`. 
 
    b. Per **Numero porta** digitare **4466**. 

    c. Per **Protocollo** selezionare **HTTPS**. 

    d. Nel campo **Autenticazione** selezionare l'opzione relativa **Specific Windows user** (Utente specifico di Windows). Verrà visualizzata una pagina di accesso. Inserire le credenziali di Azure AD. 

    e. Per **Vault on Server** (Insieme di credenziali nel server) selezionare l'insieme di credenziali corrispondente nel server.
 
    f. Fare clic su **OK**.

### <a name="create-m-files-test-user"></a>Creare l'utente di test di M-Files

Questa sezione descrive come creare un utente chiamato Britta Simon in M-Files. Collaborare con il [team di supporto M-Files](mailto:support@m-files.com) per aggiungere gli utenti in M-Files.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà reindirizzato all'URL di accesso di M-Files in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso di M-Files e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro M-Files nel App personali, viene reindirizzato all'URL di accesso di M-Files. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato M-Files, è possibile applicare il controllo sessione che consente di proteggere in tempo reale l'esfiltrazione e l'infiltrazione dei dati sensibili dell'organizzazione. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
