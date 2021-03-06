---
title: 'Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con NAVEX One | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure Active Directory e NAVEX uno.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/28/2021
ms.author: jeedes
ms.openlocfilehash: c6eac316a8d1849e193add2d74e81f8652c7a676
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99090588"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-navex-one"></a>Esercitazione: integrazione di Azure Active Directory Single Sign-On (SSO) con NAVEX One

Questa esercitazione illustra come integrare NAVEX One con Azure Active Directory (Azure AD). Quando si integra NAVEX One con Azure AD, è possibile:

* Controllare in Azure AD chi può accedere a NAVEX One.
* Abilitare gli utenti per l'accesso automatico a NAVEX One con i relativi account Azure AD.
* Gestire gli account in un'unica posizione centrale: il portale di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Sottoscrizione di NAVEX One Single Sign-On (SSO) abilitata.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

* NAVEX One supporta SSO avviato da **SP**

## <a name="adding-navex-one-from-the-gallery"></a>Aggiunta di NAVEX One dalla raccolta

Per configurare l'integrazione di NAVEX One in Azure AD, è necessario aggiungere NAVEX One dalla raccolta al proprio elenco di app SaaS gestite.

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro di spostamento a sinistra selezionare il servizio **Azure Active Directory**.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere una nuova applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** digitare **NAVEX One** nella casella di ricerca.
1. Selezionare **NAVEX One** nel pannello dei risultati e quindi aggiungere l'app. Attendere alcuni secondi che l'app venga aggiunta al tenant.


## <a name="configure-and-test-azure-ad-sso-for-navex-one"></a>Configurare e testare Azure AD SSO per NAVEX One

Configurare e testare Azure AD SSO con NAVEX One usando un utente test di nome **B. Simon**. Per il funzionamento dell'accesso SSO, è necessario stabilire una relazione di collegamento tra un utente Azure AD e l'utente correlato in NAVEX One.

Per configurare e testare Azure AD SSO con NAVEX One, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** : per consentire agli utenti di usare questa funzionalità.
    1. **[Creare un utente di test di Azure AD](#create-an-azure-ad-test-user)** : per testare l'accesso Single Sign-On di Azure AD con l'utente B.Simon.
    1. **[Assegnare l'utente di test di Azure AD](#assign-the-azure-ad-test-user)** : per abilitare B.Simon all'uso dell'accesso Single Sign-On di Azure AD.
1. **[Configurare NAVEX One SSO](#configure-navex-one-sso)** per configurare le impostazioni di Single Sign-on sul lato applicazione.
    1. **[Creare un utente di test di NAVEX One](#create-navex-one-test-user)** : per avere una controparte di B. Simon in NAVEX One collegata alla rappresentazione Azure ad dell'utente.
1. **[Testare l'accesso Single Sign-On](#test-sso)** : per verificare se la configurazione funziona.

## <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

Per abilitare l'accesso Single Sign-On di Azure AD nel portale di Azure, seguire questa procedura.

1. Nella pagina di integrazione dell'applicazione **NAVEX One** del portale di Azure individuare la sezione **Gestisci** e selezionare **Single Sign-on**.
1. Nella pagina **Selezionare un metodo di accesso Single Sign-On** selezionare **SAML**.
1. Nella pagina **Configura l'accesso Single Sign-On con SAML** fare clic sull'icona della matita per modificare le impostazioni di **Configurazione SAML di base**.

   ![Modificare la configurazione SAML di base](common/edit-urls.png)

1. Nella sezione **Configurazione SAML di base** immettere i valori per i campi seguenti:

    a. Nella casella di testo **URL di accesso** digitare un URL in uno dei formati seguenti:

    | URL di accesso |
    |--------------|
    | `https://<CLIENT_KEY>.navexglobal.com` |
    | `https://<CLIENT_KEY>.navexglobal.eu` |
    |

    b. Nella casella di testo **Identificatore** digitare uno degli URL seguenti:

    | Identificatore |
    |--------------|
    | `https://doorman.navexglobal.com/Shibboleth` |
    | `https://doorman.navexglobal.eu/Shibboleth` |
    |

    c. Nella casella di testo **URL di risposta** digitare uno degli URL seguenti:

    | URL di risposta |
    |--------------|
    | `https://doorman.navexglobal.com/Shibboleth.sso/SAML2/POST` |
    | `https://doorman.navexglobal.eu/Shibboleth.sso/SAML2/POST` |
    |

    > [!NOTE]
    > Poiché il valore di URL accesso non è reale, è necessario aggiornare questo valore con l'URL di accesso effettivo. Per ottenere il valore, contattare il [team di supporto clienti di NAVEX One](mailto:ethicspoint@navexglobal.com) . È anche possibile fare riferimento ai modelli mostrati nella sezione **Configurazione SAML di base** del portale di Azure.

1. Nella sezione **Certificato di firma SAML** della pagina **Configura l'accesso Single Sign-On con SAML** fare clic sul pulsante Copia per copiare l'**URL dei metadati di federazione dell'app** e salvarlo nel computer.

    ![Collegamento di download del certificato](common/copy-metadataurl.png)
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

In questa sezione si consentirà a B. Simon di usare Azure Single Sign-On concedendo l'accesso a NAVEX One.

1. Nel portale di Azure selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.
1. Nell'elenco delle applicazioni selezionare **NAVEX One**.
1. Nella pagina di panoramica dell'app trovare la sezione **Gestione** e selezionare **Utenti e gruppi**.
1. Selezionare **Aggiungi utente** e quindi **Utenti e gruppi** nella finestra di dialogo **Aggiungi assegnazione**.
1. Nella finestra di dialogo **Utenti e gruppi** selezionare **B.Simon** dall'elenco degli utenti e quindi fare clic sul pulsante **Seleziona** nella parte inferiore della schermata.
1. Se si prevede che agli utenti venga assegnato un ruolo, è possibile selezionarlo nell'elenco a discesa **Selezionare un ruolo**. Se per questa app non è stato configurato alcun ruolo, il ruolo selezionato è "Accesso predefinito".
1. Nella finestra di dialogo **Aggiungi assegnazione** fare clic sul pulsante **Assegna**.

## <a name="configure-navex-one-sso"></a>Configurare NAVEX One SSO

Per configurare Single Sign-On su **NAVEX un** lato, è necessario inviare l' **URL dei metadati di Federazione dell'app** al team di supporto di [NAVEX One](mailto:ethicspoint@navexglobal.com). La configurazione viene eseguita in modo che la connessione SSO SAML sia impostata correttamente su entrambi i lati.

### <a name="create-navex-one-test-user"></a>Creare un utente di test di NAVEX One

In questa sezione viene creato un utente di nome Britta Simon in NAVEX One. Collaborare con il [team di supporto di NAVEX One](mailto:ethicspoint@navexglobal.com) per aggiungere gli utenti nella piattaforma NAVEX One. Gli utenti devono essere creati e attivati prima di usare l'accesso Single Sign-On.

## <a name="test-sso"></a>Testare l'accesso SSO 

In questa sezione viene testata la configurazione dell'accesso Single Sign-On di Azure AD con le opzioni seguenti. 

* Fare clic su **Test this application** (Testa questa applicazione) nel portale di Azure. Verrà eseguito il reindirizzamento a NAVEX un URL di accesso in cui è possibile avviare il flusso di accesso. 

* Passare direttamente all'URL di accesso a NAVEX e avviare il flusso di accesso da qui.

* È possibile usare App personali Microsoft. Quando si fa clic sul riquadro NAVEX One in app personali, questo verrà reindirizzato a NAVEX un URL di accesso. Per altre informazioni su App personali, vedere l'[introduzione ad App personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Una volta configurato NAVEX, è possibile applicare il controllo della sessione, che protegge exfiltration e infiltrando i dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
