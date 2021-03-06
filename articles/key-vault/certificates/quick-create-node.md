---
title: Avvio rapido - Libreria client dei certificati di Azure Key Vault per JavaScript (versione 4)
description: Informazioni su come creare, recuperare ed eliminare certificati da Azure Key Vault con la libreria client JavaScript
author: msmbaldwin
ms.author: mbaldwin
ms.date: 12/6/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 3f933a564e62bf3aae1ec05c6dde048100c22967
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815847"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-javascript-version-4"></a>Avvio rapido: Libreria client dei certificati di Azure Key Vault per JavaScript (versione 4)

Introduzione alla libreria client dei certificati di Azure Key Vault per JavaScript. [Azure Key Vault ](../general/overview.md) è un servizio cloud che offre un archivio sicuro per i certificati. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. È possibile creare e gestire istanze di Azure Key Vault tramite il portale di Azure. Questa guida di avvio rapido descrive come creare, recuperare ed eliminare certificati da Azure Key Vault usando la libreria client JavaScript

Risorse per la libreria client di Key Vault:

[Documentazione di riferimento sull'API](/javascript/api/overview/azure/key-vault-index) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault) | [Pacchetto (npm)](https://www.npmjs.com/package/@azure/keyvault-certificates)

Per altre informazioni su Key Vault e sui certificati, vedere:
- [Panoramica di Key Vault](../general/overview.md)
- [Panoramica dei certificati](about-certificates.md).

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Versione aggiornata di [Node.js](https://nodejs.org) per il sistema operativo in uso.
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
- Un insieme di credenziali delle chiavi. È possibile crearne uno con il [portale di Azure](../general/quick-create-portal.md), l'[interfaccia della riga di comando di Azure](../general/quick-create-cli.md) o [Azure PowerShell](../general/quick-create-powershell.md).

Questa guida di avvio rapido presuppone che si usi l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

1. Eseguire il comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

    In caso contrario, aprire una pagina del browser all'indirizzo [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e immettere il codice di autorizzazione visualizzato nel terminale.

2. Accedere con le credenziali dell'account nel browser.

## <a name="create-new-nodejs-application"></a>Creare una nuova applicazione Node.js

Creare quindi un'applicazione Node.js che possa essere distribuita nel cloud. 

1. In una shell dei comandi creare una cartella denominata `key-vault-node-app`:

```azurecli
mkdir key-vault-node-app
```

1. Passare alla directory *key-vault-node-app* appena creata ed eseguire il comando init per inizializzare il progetto Node:

```azurecli
cd key-vault-node-app
npm init -y
```

## <a name="install-key-vault-packages"></a>Installare i pacchetti di Key Vault

Nella finestra della console installare la [libreria dei certificati](https://www.npmjs.com/package/@azure/keyvault-certificates) di Azure Key Vault per Node.js.

```azurecli
npm install @azure/keyvault-certificates
```

Installare il pacchetto [azure.identity](https://www.npmjs.com/package/@azure/identity) per eseguire l'autenticazione in un insieme di credenziali delle chiavi

```azurecli
npm install @azure/identity
```

## <a name="set-environment-variables"></a>Impostare le variabili di ambiente

Questa applicazione usa il nome dell'insieme di credenziali delle chiavi come variabile di ambiente denominata `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS o Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="grant-access-to-your-key-vault"></a>Concedere l'accesso all'insieme di credenziali delle chiavi

Creare un criterio di accesso per l'insieme di credenziali delle chiavi che concede le autorizzazioni per i certificati all'account utente

```azurecli
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create purge
```

## <a name="code-examples"></a>Esempi di codice

Gli esempi di codice riportati di seguito illustrano come creare un client e come impostare, recuperare ed eliminare un certificato. 

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

1. Creare un nuovo file di testo e salvarlo con il nome 'index.js'

1. Aggiungere le chiamate necessarie per caricare i moduli di Azure e Node.js

1. Creare la struttura per il programma, inclusa la gestione delle eccezioni di base

```javascript
const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {
    
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
```

### <a name="add-directives"></a>Aggiungere le direttive

Aggiungere le direttive seguenti all'inizio del codice:

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { CertificateClient } = require("@azure/keyvault-certificates");
```

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

In questo argomento di avvio rapido viene usato l'utente connesso per eseguire l'autenticazione in Key Vault, che è il metodo preferito per lo sviluppo locale. Per le applicazioni distribuite in Azure, l'identità gestita deve essere assegnata al servizio app o alla macchina virtuale. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Nell'esempio seguente, il nome dell'insieme di credenziali delle chiavi viene esteso al relativo URI, nel formato "https://\<your-key-vault-name\>.vault.azure.net". Questo esempio usa la classe ['DefaultAzureCredential()'](https://docs.microsoft.com/javascript/api/@azure/identity/defaultazurecredential) della [libreria di identità di Azure](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme), che consente di usare lo stesso codice in ambienti diversi con opzioni diverse per specificare l'identità. Per altre informazioni sull'autenticazione nell'insieme di credenziali delle chiavi, vedere la [Guida per sviluppatori](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

Aggiungere il codice seguente alla funzione 'main()'

```javascript
const keyVaultName = process.env["KEY_VAULT_NAME"];
const KVUri = "https://" + keyVaultName + ".vault.azure.net";

const credential = new DefaultAzureCredential();
const client = new Certificate(KVUri, credential);
```

### <a name="save-a-certificate"></a>Salvare un certificato

Ora che l'applicazione è stata autenticata, è possibile inserire un certificato nell'insieme di credenziali delle credenziali usando il [metodo beginCreateCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#beginCreateCertificate_string__CertificatePolicy__BeginCreateCertificateOptions_). È necessario specificare un nome per il certificato e i [criteri del certificato](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicy) con le [proprietà dei criteri del certificato](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificatepolicyproperties)

```javascript
const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
};
const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
const certificate = await poller.pollUntilDone();
```

> [!NOTE]
> Se il nome del certificato esiste, il codice precedente ne crea una nuova versione.
### <a name="retrieve-a-certificate"></a>Recuperare un certificato

È ora possibile recuperare il valore impostato in precedenza con il [metodo getCertificate](/javascript/api/@azure/keyvault-certificates/certificateclient?#getCertificate_string__GetCertificateOption).

```javascript
const retrievedCertificate = await client.getCertificate(certificateName);
 ```

### <a name="delete-a-certificate"></a>Eliminare un certificato

Infine, eliminare e rimuovere definitivamente il certificato dall'insieme di credenziali delle chiavi con i metodi [beginDeleteCertificate]https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#beginDeleteCertificate_string__BeginDeleteCertificateOptions_) e [purgeDeletedCertificate](https://docs.microsoft.com/javascript/api/@azure/keyvault-certificates/certificateclient?#purgeDeletedCertificate_string__PurgeDeletedCertificateOptions_).

```javascript
const deletePoller = await client.beginDeleteCertificate(certificateName);
await deletePoller.pollUntilDone();
await client.purgeDeletedCertificate(certificateName);
```

## <a name="sample-code"></a>Codice di esempio

```javascript
const { DefaultAzureCredential } = require("@azure/identity");
const { CertificateClient } = require("@azure/keyvault-certificates");

const readline = require('readline');

function askQuestion(query) {
    const rl = readline.createInterface({
        input: process.stdin,
        output: process.stdout,
    });

    return new Promise(resolve => rl.question(query, ans => {
        rl.close();
        resolve(ans);
    }))
}

async function main() {

  const string certificateName = "myCertificate";
  const keyVaultName = process.env["KEY_VAULT_NAME"];
  const KVUri = "https://" + keyVaultName + ".vault.azure.net";

  const credential = new DefaultAzureCredential();
  const client = new CertificateClient(KVUri, credential);

  console.log("Creating a certificate in " + keyVaultName + " called '" + certificateName +  "` ...");
  const certificatePolicy = {
  issuerName: "Self",
  subject: "cn=MyCert"
  };
  const createPoller = await client.beginCreateCertificate(certificateName, certificatePolicy);
  const certificate = await poller.pollUntilDone();

  console.log("Done.");

  console.log("Retrieving your certificate from " + keyVaultName + ".");

  const retrievedCertificate = await client.getCertificate(certificateName);

  console.log("Your certificate version is '" + retrievedCertificate.properties.version + "'.");

  console.log("Deleting your certificate from " + keyVaultName + " ...");
  const deletePoller = await client.beginDeleteCertificate(certificateName);
  await deletePoller.pollUntilDone();
  console.log("Done.");
  
  console.log("Purging your certificate from {keyVaultName} ...");
  await client.purgeDeletedCertificate(certificateName);
  
}

main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));

```

## <a name="test-and-verify"></a>Test e verifica

Eseguire i comandi seguenti per eseguire l'app.

```azurecli
npm install
npm index.js
```

Compare una variante dell'output seguente:

```azurecli
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done 
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un insieme di credenziali delle chiavi e quindi è stato archiviato e recuperato un certificato. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere una [panoramica dei certificati](about-certificates.md)
- Seguire un'[esercitazione sull'accesso a Key Vault da un'applicazione del servizio app](../general/tutorial-net-create-vault-azure-web-app.md)
- Seguire un'[esercitazione sull'accesso a Key Vault da una macchina virtuale](../general/tutorial-net-virtual-machine.md)
- Vedere la [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- Vedere [Panoramica della sicurezza di Azure Key Vault](../general/security-features.md)
