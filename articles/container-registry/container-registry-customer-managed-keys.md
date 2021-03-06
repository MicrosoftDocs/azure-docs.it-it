---
title: Crittografare il Registro di sistema con una chiave gestita dal cliente
description: Informazioni sulla crittografia in locale del registro Azure Container e su come crittografare il registro Premium con una chiave gestita dal cliente archiviata in Azure Key Vault
ms.topic: article
ms.date: 03/03/2021
ms.custom: ''
ms.openlocfilehash: 9ec32e32d187a3db07f023c78efbd301ef578cbc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817036"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Crittografare il registro usando una chiave gestita dal cliente

Quando si archiviano immagini e altri artefatti in un registro contenitori di Azure, Azure crittografa automaticamente il contenuto inattivo del registro con le [chiavi gestite dal servizio](../security/fundamentals/encryption-models.md). È possibile integrare la crittografia predefinita con un livello di crittografia aggiuntivo usando una chiave creata e gestita in Azure Key Vault (una chiave gestita dal cliente). Questo articolo illustra i passaggi tramite l'interfaccia della riga di comando di Azure, portale di Azure o un Resource Manager modello.

La crittografia lato server con chiavi gestite dal cliente è supportata tramite l'integrazione con [Azure Key Vault](../key-vault/general/overview.md): 

* È possibile creare chiavi di crittografia personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API di Azure Key Vault per generare le chiavi. 
* Con Azure Key Vault è anche possibile controllare l'utilizzo delle chiavi.
* Registro Azure Container supporta la rotazione automatica delle chiavi di crittografia del Registro di sistema quando è disponibile una nuova versione della chiave Azure Key Vault. È anche possibile ruotare manualmente le chiavi di crittografia del Registro di sistema.

Questa funzionalità è disponibile nel livello di servizio **Premium** del registro contenitori. Per informazioni sui livelli di servizio e sui limiti del registro contenitori, vedere [Livelli di servizio del Registro Azure Container](container-registry-skus.md).


## <a name="things-to-know"></a>Informazioni importanti

* Attualmente è possibile abilitare una chiave gestita dal cliente solo quando si crea un registro. Quando si abilita la chiave, si configura *un'identità gestita assegnata dall'utente* per accedere all'insieme di credenziali delle chiavi.
* Dopo aver abilitato la crittografia con una chiave gestita dal cliente in un registro, non è possibile disabilitare la crittografia.  
* Registro Azure Container supporta solo chiavi RSA o RSA-HSM. Le chiavi di curva ellittica non sono attualmente supportate.
* L'[attendibilità del contenuto](container-registry-content-trust.md) non è attualmente supportata in un registro crittografato con una chiave gestita dal cliente.
* In un registro crittografato con una chiave gestita dal cliente, i log di esecuzione per le [Attività del Registro Azure Container](container-registry-tasks-overview.md) attualmente vengono conservati solo per 24 ore. Se è necessario conservare i log per un periodo di tempo più lungo, vedere le istruzioni per [esportare e archiviare i log di esecuzione delle attività](container-registry-tasks-logs.md#alternative-log-storage).


> [!IMPORTANT]
> Se si prevede di archiviare la chiave di crittografia del Registro di sistema in un insieme di credenziali delle chiavi di Azure esistente che nega l'accesso pubblico e consente solo l'endpoint privato o le reti virtuali selezionate, sono necessari passaggi di configurazione aggiuntivi. Vedere [Scenario avanzato: Key Vault firewall](#advanced-scenario-key-vault-firewall) in questo articolo.

## <a name="automatic-or-manual-update-of-key-versions"></a>Aggiornamento automatico o manuale delle versioni chiave

Una considerazione importante per la sicurezza di un registro crittografato con una chiave gestita dal cliente è la frequenza con cui si aggiorna (ruota) la chiave di crittografia. L'organizzazione potrebbe avere criteri di [](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) conformità che richiedono l'aggiornamento regolare delle versioni chiave archiviate in Azure Key Vault quando vengono usate come chiavi gestite dal cliente. 

Quando si configura la crittografia del Registro di sistema con una chiave gestita dal cliente, sono disponibili due opzioni per aggiornare la versione della chiave usata per la crittografia:

*  Aggiornare automaticamente la versione della chiave: per aggiornare automaticamente una chiave gestita dal cliente quando è disponibile una nuova versione in Azure Key Vault, omettere la versione della chiave quando si abilita la crittografia del Registro di sistema con una chiave gestita dal cliente. Quando un registro viene crittografato con una chiave senza controllo delle versioni, Registro Azure Container controlla regolarmente la presenza di una nuova versione della chiave nell'insieme di credenziali delle chiavi e aggiorna la chiave gestita dal cliente entro 1 ora. Registro Azure Container usa automaticamente la versione più recente della chiave.

* **Aggiornare manualmente la versione della chiave:** per usare una versione specifica di una chiave per la crittografia del Registro di sistema, specificare la versione della chiave quando si abilita la crittografia del Registro di sistema con una chiave gestita dal cliente. Quando un registro viene crittografato con una versione della chiave specifica, Registro Azure Container usa tale versione per la crittografia fino a quando non si ruota manualmente la chiave gestita dal cliente.

Per informazioni dettagliate, vedere [Scegliere l'ID chiave con o senza la versione della chiave](#choose-key-id-with-or-without-key-version) e Aggiornare la versione della [chiave](#update-key-version)più avanti in questo articolo.

## <a name="prerequisites"></a>Prerequisiti

Per usare i passaggi dell'interfaccia della riga di comando di Azure descritti in questo articolo, è necessaria l'interfaccia della riga di comando di Azure versione 2.2.0 o successiva o Azure Cloud Shell. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Abilitare la chiave gestita dal cliente - interfaccia della riga di comando

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Se necessario, eseguire il comando [az group create][az-group-create] per creare un gruppo di risorse per la creazione dell'insieme di credenziali delle chiavi, del registro contenitori e di altre risorse richieste.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Creare un'identità gestita assegnata dall'utente

Creare un'[identità gestita assegnata dall'utente per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) con il comando [az identity create][az-identity-create]. Questa identità verrà usata dal registro per accedere al servizio Key Vault.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

Nell'output del comando prendere nota dei valori seguenti: `id` e `principalId`. Questi valori sono necessari nei passaggi successivi per configurare l'accesso del registro all'insieme di credenziali delle chiavi.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Per praticità, archiviare questi valori nelle variabili di ambiente:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Creare un insieme di credenziali delle chiavi con [az keyvault create][az-keyvault-create] per archiviare una chiave gestita dal cliente per la crittografia del registro. 

Per impostazione predefinita, **l'impostazione di eliminazione soft** viene abilitata automaticamente in un nuovo insieme di credenziali delle chiavi. Per evitare la perdita di dati causata da eliminazioni accidentali di chiavi o insiemi di credenziali delle chiavi, abilitare anche **l'impostazione di protezione dall'eliminazione.**

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

Per l'uso nei passaggi successivi, ottenere l'ID risorsa dell'insieme di credenziali delle chiavi:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Abilitare l'accesso all'insieme di credenziali delle chiavi

Configurare un criterio per l'insieme di credenziali delle chiavi in modo che l'identità possa accedervi. Nel comando [az keyvault set-policy][az-keyvault-set-policy] che segue viene passato l'ID entità di sicurezza dell'identità gestita creata, archiviato in precedenza in una variabile di ambiente. Impostare le autorizzazioni delle chiavi per **get**, **unwrapKey** e **wrapKey**.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

In alternativa, usare il controllo [degli accessi in](../key-vault/general/rbac-guide.md) base al ruolo di Azure Key Vault per assegnare le autorizzazioni all'identità per accedere all'insieme di credenziali delle chiavi. Ad esempio, assegnare il Key Vault Crittografia del servizio di crittografia all'identità usando il [comando az role assignment create:](/cli/azure/role/assignment#az_role_assignment_create)

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Creare la chiave e ottenere l'ID chiave

Per creare una chiave nell'insieme di credenziali delle chiavi, eseguire il comando [az keyvault key create][az-keyvault-key-create].

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Nell'output del comando prendere nota dell'ID della chiave, `kid`. Questo ID verrà usato nel passaggio successivo:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>Scegliere l'ID chiave con o senza la versione della chiave

Per praticità, archiviare il formato scelto per l'ID chiave nella $keyID di ambiente. È possibile usare un ID chiave con una versione o una chiave senza una versione.

#### <a name="manual-key-rotation---key-id-with-version"></a>Rotazione manuale delle chiavi : ID chiave con versione

Se usata per crittografare un registro con una chiave gestita dal cliente, questa chiave consente solo la rotazione manuale delle chiavi Registro Azure Container.

In questo esempio viene archiviata la proprietà `kid` della chiave:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Rotazione automatica della chiave: ID chiave che omette la versione 

Se usata per crittografare un registro con una chiave gestita dal cliente, questa chiave consente la rotazione automatica delle chiavi quando viene rilevata una nuova versione della chiave Azure Key Vault.

Questo esempio rimuove la versione dalla proprietà della `kid` chiave:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Creare un registro con la chiave gestita dal cliente

Eseguire il comando [az acr create][az-acr-create] per creare un registro nel livello di servizio Premium e abilitare la chiave gestita dal cliente. Passare l'ID identità gestita e l'ID chiave, archiviati in precedenza nelle variabili di ambiente:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Visualizzare lo stato della crittografia

Per indicare se la crittografia del registro con una chiave gestita dal cliente è abilitata, eseguire il comando [az acr encryption show][az-acr-encryption-show]:

```azurecli
az acr encryption show --name <registry-name>
```

A seconda della chiave usata per crittografare il Registro di sistema, l'output è simile al seguente:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
  },
  "status&quot;: &quot;enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Abilitare la chiave gestita dal cliente - portale

### <a name="create-a-managed-identity"></a>Creare un'identità gestita

Creare un'[identità gestita assegnata dall'utente per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) nel portale di Azure. Per la procedura, vedere [Creare un'identità assegnata dall'utente](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

Il nome dell'identità verrà usato nei passaggi successivi.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Creare un'identità assegnata dall'utente nel portale di Azure":::

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Per la procedura di creazione di un insieme di credenziali delle chiavi, vedere [Avvio rapido: Creare un insieme](../key-vault/general/quick-create-portal.md)di credenziali delle chiavi usando l'portale di Azure .

Quando si crea un insieme di credenziali delle chiavi per una chiave gestita dal cliente, nella scheda **Informazioni** di base abilitare l'impostazione **Protezione ripulitura.** Questa impostazione consente di evitare la perdita di dati causata da eliminazioni accidentali della chiave o dell'insieme di credenziali delle chiavi.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Creare un insieme di credenziali delle chiavi nel portale di Azure":::

### <a name="enable-key-vault-access"></a>Abilitare l'accesso all'insieme di credenziali delle chiavi

Configurare un criterio per l'insieme di credenziali delle chiavi in modo che l'identità possa accedervi.

1. Passare all'insieme di credenziali delle chiavi.
1. Selezionare **Impostazioni** > **Criteri di accesso > +Aggiungi un criterio di accesso**.
1. Selezionare **Autorizzazioni chiave**, quindi **Recupera**, **Annulla il wrapping della chiave** ed **Esegui il wrapping della chiave**.
1. In **Seleziona entità** selezionare il nome della risorsa dell'identità gestita assegnata dall'utente.  
1. Selezionare **Aggiungi** e quindi **Salva**.

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Creare un criterio di accesso dell'insieme di credenziali delle chiavi":::

In alternativa, usare il controllo [degli accessi in](../key-vault/general/rbac-guide.md) base al ruolo di Azure Key Vault per assegnare le autorizzazioni all'identità per accedere all'insieme di credenziali delle chiavi. Ad esempio, assegnare il Key Vault crittografia del servizio Crypto all'identità.

1. Passare all'insieme di credenziali delle chiavi.
1. Selezionare **Controllo di accesso (IAM)**  >  **+Aggiungi Aggiungi** assegnazione di  >  **ruolo**.
1. Nella finestra **Aggiungi assegnazione di** ruolo:
    1. Selezionare **Key Vault ruolo utente Crittografia servizio** Crypto. 
    1. Assegnare l'accesso **all'identità gestita assegnata dall'utente**.
    1. Selezionare il nome della risorsa dell'identità gestita assegnata dall'utente e selezionare **Salva**.

### <a name="create-key-optional"></a>Crea chiave (facoltativo)

Facoltativamente, creare una chiave nell'insieme di credenziali delle chiavi da usare per crittografare il Registro di sistema. Seguire questa procedura se si vuole selezionare una versione della chiave specifica come chiave gestita dal cliente. 

1. Passare all'insieme di credenziali delle chiavi.
1. Selezionare **Impostazioni** > **Chiavi**.
1. Selezionare **+Genera/Importa** e immettere un nome univoco per la chiave.
1. Accettare i rimanenti valori predefiniti e selezionare **Crea**.
1. Dopo la creazione, selezionare la chiave e quindi selezionare la versione corrente. Copiare **l'identificatore di** chiave per la versione della chiave.

### <a name="create-azure-container-registry"></a>Creare un'istanza di Registro Azure Container

1. Selezionare **Crea una risorsa** > **Contenitori** > **Registro Container**.
1. Nella scheda **Nozioni di base** selezionare o creare un gruppo di risorse e immettere un nome di registro. In **SKU** selezionare **Premium**.
1. Nella scheda **Crittografia**, selezionare **Abilitata** in **Chiave gestita dal cliente**.
1. In **Identità** selezionare l'identità gestita creata.
1. In **Crittografia** scegliere una delle opzioni seguenti:
    * Selezionare **Seleziona da Key Vault** e selezionare un insieme di credenziali delle chiavi e una chiave esistenti oppure Crea **nuovo.** La chiave selezionata non ha il controllo delle versioni e abilita la rotazione automatica delle chiavi.
    * Selezionare **Immettere l'URI della** chiave e specificare direttamente un identificatore di chiave. È possibile specificare un URI di chiave con controllo delle versioni (per una chiave che deve essere ruotata manualmente) o un URI di chiave senza versione (che abilita la rotazione automatica delle chiavi). 
1. Nella scheda **Crittografia** selezionare **Rivedi e crea**.
1. Selezionare **Crea** per distribuire l'istanza del registro.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Creare un registro crittografato nel portale di Azure":::

Per visualizzare lo stato della crittografia del registro nel portale, passare al registro. In **Impostazioni** selezionare **Crittografia**.

## <a name="enable-customer-managed-key---template"></a>Abilitare la chiave gestita dal cliente - modello

È anche possibile usare un modello di Resource Manager per creare un registro e abilitare la crittografia con una chiave gestita dal cliente.

Il modello seguente crea un nuovo registro contenitori e un'identità gestita assegnata dall'utente. Copiare il contenuto seguente in un nuovo file e salvarlo usando un nome file, ad esempio `CMKtemplate.json`.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}
```

Seguire i passaggi delle sezioni precedenti per creare le risorse seguenti:

* Insieme di credenziali delle chiavi, identificato dal nome
* Insieme di credenziali delle chiavi, identificato dall'ID chiave

Eseguire il comando [az deployment group create seguente][az-deployment-group-create] per creare il Registro di sistema usando il file modello precedente. Dove indicato, specificare un nuovo nome del registro e il nome dell'identità gestita, nonché il nome dell'insieme di credenziali delle chiavi e l'ID chiave creato.

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Visualizzare lo stato della crittografia

Per visualizzare lo stato della crittografia del registro, eseguire il comando [az acr encryption show][az-acr-encryption-show]:

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>Usare il registro

Dopo aver abilitato una chiave gestita dal cliente in un registro, è possibile eseguire le stesse operazioni del registro che si eseguono in un registro non crittografato con una chiave gestita dal cliente. Ad esempio, è possibile eseguire l'autenticazione con il registro ed effettuare il push delle immagini Docker. Vedere i comandi di esempio in [Effettuare il push e il pull di un'immagine](container-registry-get-started-docker-cli.md).

## <a name="rotate-key"></a>Ruotare la chiave

Aggiornare la versione della chiave in Azure Key Vault o creare una nuova chiave, quindi aggiornare il Registro di sistema per crittografare i dati usando la chiave. È possibile eseguire questi passaggi usando l'interfaccia della riga di comando di Azure o il portale.

Quando si ruota una chiave, in genere si specifica la stessa identità usata durante la creazione del registro. Facoltativamente, configurare una nuova identità assegnata dall'utente per l'accesso alla chiave o abilitare e specificare l'identità assegnata dal sistema del registro.

> [!NOTE]
> Assicurarsi che l'accesso [all'insieme di credenziali delle](#enable-key-vault-access) chiavi necessario sia impostato per l'identità configurata per l'accesso alla chiave.

### <a name="update-key-version"></a>Aggiornare la versione della chiave

Uno scenario comune consiste nell'aggiornare la versione della chiave usata come chiave gestita dal cliente. A seconda della configurazione della crittografia del Registro di sistema, la chiave gestita dal cliente in Registro Azure Container viene aggiornata automaticamente o deve essere aggiornata manualmente.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare i comandi [az keyvault key][az-keyvault-key] per creare o gestire le chiavi dell'insieme di credenziali delle chiavi. Per creare una nuova versione della chiave, eseguire [il comando az keyvault key create:][az-keyvault-key-create]

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

Il passaggio successivo dipende dalla configurazione della crittografia del Registro di sistema:

* Se il Registro di sistema è configurato per rilevare gli aggiornamenti delle versioni delle chiavi, la chiave gestita dal cliente viene aggiornata automaticamente entro 1 ora.

* Se il Registro di sistema è configurato per richiedere l'aggiornamento manuale per una nuova versione della chiave, eseguire il [comando az acr encryption rotate-key,][az-acr-encryption-rotate-key] passando il nuovo ID chiave e l'identità che si vuole configurare:

Per aggiornare manualmente la versione della chiave gestita dal cliente:

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

> [!TIP]
> Quando si esegue , è possibile passare un ID chiave con controllo delle versioni o un ID chiave `az acr encryption rotate-key` senza versione. Se si usa un ID chiave senza controllo delle versioni, il Registro di sistema viene quindi configurato per rilevare automaticamente gli aggiornamenti delle versioni delle chiavi successivi.

### <a name="portal"></a>Portale

Usare le impostazioni di crittografia **del** Registro di sistema per aggiornare le impostazioni dell'insieme di credenziali delle chiavi, della chiave o dell'identità usate per la chiave gestita dal cliente.

Ad esempio, per configurare una nuova chiave:

1. Passare al registro nel portale.
1. In **Impostazioni** selezionare **Crittografia** > **Modifica chiave**.

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Ruotare la chiave nel portale di Azure":::
1. In **Crittografia** scegliere una delle opzioni seguenti:
    * Selezionare **Seleziona da Key Vault** e selezionare un insieme di credenziali delle chiavi e una chiave esistenti oppure Crea **nuovo**. La chiave selezionata non ha il controllo delle versioni e abilita la rotazione automatica delle chiavi.
    * Selezionare **Immettere l'URI della chiave** e specificare direttamente un identificatore di chiave. È possibile fornire un URI di chiave con controllo delle versioni (per una chiave che deve essere ruotata manualmente) o un URI di chiave senza controllo delle versioni (che abilita la rotazione automatica della chiave).
1. Completare la selezione della chiave e selezionare **Salva**.

## <a name="revoke-key"></a>Revocare la chiave

Revocare la chiave di crittografia gestita dal cliente modificando i criteri di accesso o le autorizzazioni nell'insieme di credenziali delle chiavi o eliminando la chiave. Ad esempio, usare il comando [az keyvault delete-policy][az-keyvault-delete-policy] per modificare il criterio di accesso dell'identità gestita usata dal registro:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

La revoca della chiave blocca efficacemente l'accesso a tutti i dati del registro, poiché il registro non può accedere alla chiave di crittografia. Se l'accesso alla chiave è abilitato o viene ripristinata la chiave eliminata, il registro selezionerà la chiave in modo che sia possibile accedere di nuovo ai dati crittografati del registro.

## <a name="advanced-scenario-key-vault-firewall"></a>Scenario avanzato: Key Vault firewall

È possibile archiviare la chiave di crittografia usando un insieme di credenziali delle chiavi di Azure esistente configurato con un [firewall Key Vault , che](../key-vault/general/network-security.md)nega l'accesso pubblico e consente solo l'endpoint privato o le reti virtuali selezionate. 

Per questo scenario, creare prima di tutto una nuova identità assegnata dall'utente, un insieme di [](#enable-customer-managed-key---portal)credenziali delle chiavi e un registro contenitori crittografati con una chiave gestita dal cliente, usando l'interfaccia della riga di comando di [Azure,](#enable-customer-managed-key---cli)il portale o [il modello](#enable-customer-managed-key---template). I passaggi dettagliati sono descritti nelle sezioni precedenti di questo articolo.
   > [!NOTE]
   > Il nuovo insieme di credenziali delle chiavi viene distribuito all'esterno del firewall. Viene usato solo temporaneamente per archiviare la chiave gestita dal cliente.

Dopo la creazione del Registro di sistema, continuare con i passaggi seguenti. I dettagli sono disponibili nelle sezioni seguenti.

1. Abilitare l'identità assegnata dal sistema del Registro di sistema.
1. Concedere all'identità assegnata dal sistema le autorizzazioni per accedere alle chiavi nell'insieme di credenziali delle chiavi limitato Key Vault firewall.
1. Assicurarsi che il firewall Key Vault consenta il bypass da parte di servizi attendibili. Attualmente, un Registro Azure Container può ignorare il firewall solo quando si usa l'identità gestita dal sistema. 
1. Ruotare la chiave gestita dal cliente selezionando una chiave nell'insieme di credenziali delle chiavi con restrizioni Key Vault firewall.
1. Quando non è più necessario, è possibile eliminare l'insieme di credenziali delle chiavi creato all'esterno del firewall.


### <a name="step-1---enable-registrys-system-assigned-identity"></a>Passaggio 1: Abilitare l'identità assegnata dal sistema del Registro di sistema

1. Passare al registro nel portale.
1. Selezionare **Impostazioni** >  **Identità**.
1. In **Assegnata dal sistema** impostare **Stato** su **Attiva**. Selezionare **Salva**.
1. Copiare l'**ID oggetto** dell'identità.

### <a name="step-2---grant-system-assigned-identity-access-to-your-key-vault"></a>Passaggio 2: Concedere l'accesso all'insieme di credenziali delle chiavi all'identità assegnata dal sistema

1. Nel portale passare all'insieme di credenziali delle chiavi.
1. Selezionare **Impostazioni** > **Criteri di accesso > +Aggiungi un criterio di accesso**.
1. Selezionare **Autorizzazioni chiave**, quindi **Recupera**, **Annulla il wrapping della chiave** ed **Esegui il wrapping della chiave**.
1. Scegliere **Seleziona entità e** cercare l'ID oggetto dell'identità gestita assegnata dal sistema o il nome del registro.  
1. Selezionare **Aggiungi** e quindi **Salva**.

### <a name="step-3---enable-key-vault-bypass"></a>Passaggio 3: Abilitare il bypass dell'insieme di credenziali delle chiavi

Per accedere a un insieme di credenziali delle chiavi configurato con un firewall Key Vault, il Registro di sistema deve ignorare il firewall. Assicurarsi che l'insieme di credenziali delle chiavi sia configurato per consentire l'accesso da parte di [qualsiasi servizio attendibile.](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services) Registro Azure Container è uno dei servizi attendibili.

1. Nel portale passare all'insieme di credenziali delle chiavi.
1. Selezionare **Impostazioni**  >  **Rete.**
1. Confermare, aggiornare o aggiungere le impostazioni della rete virtuale. Per la procedura dettagliata, vedere [Configurare reti virtuali e firewall di Azure Key Vault](../key-vault/general/network-security.md).
1. In Allow Microsoft Trusted Services to bypass this firewall (Consenti a Servizi attendibili **Microsoft di ignorare questo firewall)** selezionare **Sì.** 

### <a name="step-4---rotate-the-customer-managed-key"></a>Passaggio 4: Ruotare la chiave gestita dal cliente

Dopo aver completato i passaggi precedenti, eseguire la rotazione in una chiave archiviata nell'insieme di credenziali delle chiavi dietro un firewall.

1. Passare al registro nel portale.
1. In **Impostazioni** selezionare **Modifica chiave** di  >  **crittografia.**
1. In **Identità** selezionare **Assegnata dal sistema.**
1. Selezionare **Seleziona da Key Vault** e selezionare il nome dell'insieme di credenziali delle chiavi dietro un firewall.
1. Selezionare una chiave esistente o **Crea nuovo**. La chiave selezionata non ha il controllo delle versioni e abilita la rotazione automatica delle chiavi.
1. Completare la selezione della chiave e selezionare **Salva**.

## <a name="troubleshoot"></a>Risolvere problemi

### <a name="removing-managed-identity"></a>Rimozione dell'identità gestita


Se si tenta di rimuovere un'identità gestita assegnata dall'utente o assegnata dal sistema da un registro usato per configurare la crittografia, è possibile che venga visualizzato un messaggio di errore simile al seguente:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
Non sarà inoltre possibile modificare (ruotare) la chiave di crittografia. I passaggi di risoluzione dipendono dal tipo di identità usato per la crittografia.

**Identità assegnata dall'utente**

Se questo problema si verifica con un'identità assegnata dall'utente, riassegnare prima l'identità usando il GUID visualizzato nel messaggio di errore. Ad esempio:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Quindi, dopo aver modificato la chiave e aver assegnato un'identità diversa, è possibile rimuovere l'identità assegnata dall'utente originale.

**Identità assegnata dal sistema**

Se questo problema si verifica con un'identità assegnata dal sistema, creare un ticket supporto tecnico di Azure [assistenza](https://azure.microsoft.com/support/create-ticket/) per ripristinare l'identità.


## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [crittografia dei dati inattivi in Azure](../security/fundamentals/encryption-atrest.md).
* Altre informazioni sui criteri di accesso e su come [proteggere l'accesso a un insieme di credenziali delle chiavi](../key-vault/general/security-features.md).


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-show]: /cli/azure/feature#az_feature_show
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-keyvault-create]: /cli/azure/keyvault#az_keyvault_create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az_keyvault_key_create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az_keyvault_delete_policy
[az-resource-show]: /cli/azure/resource#az_resource_show
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az_acr_encryption_rotate_key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az_acr_encryption_show
