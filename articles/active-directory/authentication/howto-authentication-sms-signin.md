---
title: Accesso utente basato su SMS per Azure Active Directory
description: Informazioni su come configurare e consentire agli utenti di accedere a Azure Active Directory tramite SMS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rateller
ms.collection: M365-identity-device-management
ms.openlocfilehash: b84d55e2d3a2f49a870c1e57eeed3c5c0caeba4a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530418"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory"></a>Configurare e abilitare gli utenti per l'autenticazione basata su SMS usando Azure Active Directory 

Per semplificare e proteggere l'accesso ad applicazioni e servizi, Azure Active Directory (Azure AD) offre più opzioni di autenticazione. L'autenticazione basata su SMS consente agli utenti di accedere senza fornire o addirittura conoscere il nome utente e la password. Dopo aver creato l'account da un amministratore delle identità, può immettere il numero di telefono al prompt di accesso. Ricevono un codice di autenticazione tramite SMS che possono fornire per completare l'accesso. Questo metodo di autenticazione semplifica l'accesso ad applicazioni e servizi, in particolare per i frontline worker.

Questo articolo descrive come abilitare l'autenticazione basata su SMS per utenti o gruppi selezionati in Azure AD.

## <a name="before-you-begin"></a>Prima di iniziare

Per completare le procedure descritte in questo articolo, sono necessari i privilegi e le risorse seguenti:

* Una sottoscrizione di Azure attiva.
    * Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un tenant di Azure Active Directory associato alla sottoscrizione.
    * Se necessario, [creare un tenant di Azure Active Directory][create-azure-ad-tenant] o [associare una sottoscrizione di Azure al proprio account][associate-azure-ad-tenant].
* Per abilitare l'autenticazione basata su SMS, sono necessari privilegi di *amministratore globale* nel tenant di Azure AD.
* A ogni utente abilitato nei criteri del metodo di autenticazione tramite SMS deve essere concessa una licenza, anche a chi non usa questo metodo. Ogni utente abilitato deve avere una delle licenze seguenti di Azure AD, EMS o Microsoft 365:
    * [Microsoft 365 (M365) F1 o F3][m365-firstline-workers-licensing]
    * [Enterprise Mobility + Security (EMS) E3 o E5][ems-licensing] o [Microsoft 365 (M365) E3 o E5][m365-licensing]
    * [Office 365 F3][o365-f3]

## <a name="limitations"></a>Limitazioni

All'autenticazione basata su SMS si applicano le limitazioni seguenti:

* L'autenticazione basata su SMS non è attualmente compatibile con Azure AD Multi-Factor Authentication.
* Ad eccezione di Teams, l'autenticazione basata su SMS non è compatibile con le applicazioni native di Office.
* L'autenticazione basata su SMS non è consigliata per account B2B.
* Gli utenti federati non eseguono l'autenticazione nel tenant principale. Eseguono solo l'autenticazione nel cloud.

## <a name="enable-the-sms-based-authentication-method"></a>Abilitare il metodo di autenticazione basato su SMS

Per abilitare e usare l'autenticazione basata su SMS nell'organizzazione, è necessario eseguire tre passaggi principali:

* Abilitare i criteri del metodo di autenticazione.
* Selezionare gli utenti o i gruppi che possono usare il metodo di autenticazione basato su SMS.
* Assegnare un numero di telefono per ogni account utente.
    * Questo numero di telefono può essere assegnato nel portale di Azure (illustrato in questo articolo) e *in* My Staff o *Account personale*.

Prima di tutto, è necessario abilitare l'autenticazione basata su SMS per il tenant di Azure AD.

1. Accedere al [portale di Azure][azure-portal] come *amministratore globale*.
1. Cercare e selezionare **Azure Active Directory**.
1. Nel menu di spostamento sul lato sinistro della finestra di Azure Active Directory selezionare Security > Authentication methods > Authentication method policy (Metodi di autenticazione > **metodo di autenticazione).**

    [![Individuare e selezionare la finestra Criteri del metodo di autenticazione nel portale di Azure.](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Nell'elenco dei metodi di autenticazione disponibili selezionare **SMS**.
1. Impostare **Abilita** su *Sì*.

    ![Abilitare l'autenticazione tramite SMS nella finestra Criterio del metodo di autenticazione](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    È possibile scegliere di abilitare l'autenticazione basata su SMS per *Tutti gli utenti* oppure selezionare gli utenti e i gruppi desiderati in *Seleziona utenti*. Nella sezione successiva viene abilitata l'autenticazione basata su SMS per un utente di test.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Assegnare il metodo di autenticazione a utenti e gruppi

Dopo aver abilitato l'autenticazione basata su SMS nel tenant di Azure AD, selezionare ora alcuni utenti o gruppi cui consentire questo metodo di autenticazione.

1. Nella finestra dei criteri di autenticazione basata su SMS impostare **Destinazione** su *Selezionare utenti*.
1. Scegliere **Aggiungi utenti o gruppi**, quindi selezionare un utente o un gruppo di test, ad esempio *Contoso User* o *Contoso SMS Users*.

    [![Scegliere gli utenti o i gruppi da abilitare per l'autenticazione basata su SMS nel portale di Azure.](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Dopo aver selezionato gli utenti o i gruppi, scegliere **Seleziona**, quindi **Salva** per salvare i criteri del metodo di autenticazione aggiornati.

A ogni utente abilitato nei criteri del metodo di autenticazione tramite SMS deve essere concessa una licenza, anche a chi non usa questo metodo. Assicurarsi di avere le licenze appropriate per gli utenti abilitati nei criteri del metodo di autenticazione, in particolare quando si abilita la funzionalità per gruppi di utenti di grandi dimensioni.

## <a name="set-a-phone-number-for-user-accounts"></a>Impostare un numero di telefono per gli account utente

Gli utenti sono ora abilitati per l'autenticazione basata su SMS, ma il numero di telefono deve essere associato al profilo utente in Azure AD prima che possano eseguire l'accesso. L'utente [può impostare questo numero di telefono](../user-help/sms-sign-in-explainer.md) in Account *personale* oppure è possibile assegnarlo usando il portale di Azure. I numeri di telefono possono essere impostati da *amministratori globali*, *amministratori dell'autenticazione* o *amministratori dell'autenticazione con privilegi*.

Quando un numero di telefono è impostato per l'accesso tramite SMS, è disponibile anche per l'uso con Azure AD [Multi-Factor Authentication][tutorial-azure-mfa] e la reimpostazione [della password self-service.][tutorial-sspr]

1. Cercare e selezionare **Azure Active Directory**.
1. Dal menu di spostamento sul lato sinistro della finestra di Azure Active Directory selezionare **Utenti**.
1. Selezionare l'utente abilitato per l'autenticazione basata su SMS nella sezione precedente, ad esempio *Contoso User*, quindi selezionare **Metodi di autenticazione**.
1. Selezionare **+ Aggiungi metodo di autenticazione,** quindi nel menu *a* discesa Scegli metodo scegliere Numero **di telefono.**

    Immettere il numero di telefono dell'utente, incluso il prefisso internazionale, ad esempio *+ 1 xxxxxxxxx*. Il portale di Azure verifica che il numero di telefono sia nel formato corretto.

    Quindi, dal menu a discesa *Tipo* di telefono selezionare *Mobile*, *Alternate mobile* o Other in *base* alle esigenze.

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="Impostare un numero di telefono per un utente nel portale di Azure da usare con l'autenticazione basata su SMS":::

    Il numero di telefono deve essere univoco nel tenant. Se si tenta di usare lo stesso numero di telefono per più utenti, viene visualizzato un messaggio di errore.

1. Per applicare il numero di telefono all'account di un utente, selezionare **Aggiungi**.

Una volta effettuato il provisioning, viene visualizzato un segno di spunta per *SMS Sign-in enabled* (Acceso tramite SMS abilitato).

## <a name="test-sms-based-sign-in"></a>Testare l'accesso basato su SMS

Per testare l'account utente che ora è abilitato per l'accesso basato su SMS, completare questa procedura:

1. Aprire una nuova finestra del Web browser InPrivate o in incognito all'indirizzo [https://www.office.com][office]
1. Nell'angolo superiore destro selezionare **Accedi**.
1. Nella richiesta di accesso immettere il numero di telefono associato all'utente nella sezione precedente, quindi selezionare **Avanti**.

    ![Immettere un numero di telefono nella richiesta di accesso per l'utente di test](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Viene inviato un SMS al numero di telefono fornito. Per completare il processo di accesso, immettere il codice a 6 cifre fornito nell'SMS nella richiesta di accesso.

    ![Immettere il codice di conferma inviato tramite SMS al numero di telefono dell'utente](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. L'utente è ora connesso senza avere dovuto fornire un nome utente o una password.

## <a name="troubleshoot-sms-based-sign-in"></a>Risolvere i problemi di accesso basato su SMS

Gli scenari e le procedure di risoluzione dei problemi seguenti possono essere usati in caso di problemi relativi all'abilitazione e all'uso dell'accesso basato su SMS.

### <a name="phone-number-already-set-for-a-user-account"></a>Il numero di telefono è già impostato per un account utente

Se un utente ha già registrato per Azure AD Multi-Factor Authentication e/o la reimpostazione della password self-service (SSPR), ha già un numero di telefono associato al proprio account. Questo numero di telefono non è automaticamente disponibile per l'uso con l'accesso basato su SMS.

Per un utente che ha già un numero di telefono impostato per il proprio account, viene visualizzato il pulsante *Enable for SMS sign-in* (Abilita per accesso tramite SMS) nella pagina **Profilo personale**. Selezionare questo pulsante e l'account è abilitato per l'uso con l'accesso basato su SMS e la registrazione Azure AD Multi-Factor Authentication o SSPR.

Per altre informazioni sull'esperienza dell'utente finale, vedere Esperienza utente di accesso sms [per il numero di telefono](../user-help/sms-sign-in-explainer.md).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Errore durante il tentativo di impostare un numero di telefono per l'account di un utente

Se viene visualizzato un errore quando si tenta di impostare un numero di telefono per un account utente nel portale di Azure, esaminare i passaggi di risoluzione dei problemi seguenti:

1. Assicurarsi di essere abilitati per l'accesso basato su SMS.
1. Verificare che l'account utente sia abilitato nei criteri del metodo di autenticazione *SMS*.
1. Assicurarsi di impostare il numero di telefono con il formato corretto, come convalidato nel portale di Azure (ad esempio, *+ 1 4251234567*).
1. Verificare che il numero di telefono non venga usato altrove nel tenant.
1. Verificare che non sia stato impostato un numero di casella vocale per l'account. Se viene impostato un numero di casella vocale, eliminarlo e riprovare a impostare il numero di telefono.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su altri metodi per accedere ad Azure AD senza una password, ad esempio l'app Microsoft Authenticator o le chiavi di sicurezza FIDO2, vedere [Opzioni di autenticazione senza password per Azure AD][concepts-passwordless].

È anche possibile usare l'API REST Microsoft Graph per [abilitare][rest-enable] o [disabilitare l'accesso][rest-disable] basato su SMS.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
