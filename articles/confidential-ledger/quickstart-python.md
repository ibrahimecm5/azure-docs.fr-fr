---
title: 'Démarrage rapide : Bibliothèque de client Python de Registre confidentiel Microsoft Azure'
description: En savoir plus sur l’utilisation de la bibliothèque de client de Registre confidentiel Microsoft Azure pour Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 04/27/2021
ms.service: confidential-ledger
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: 292c298f3af1b693574ecf9e053de14373d4d597
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476316"
---
# <a name="quickstart-microsoft-azure-confidential-ledger-client-library-for-python"></a>Démarrage rapide : Bibliothèque de client de Registre confidentiel Microsoft Azure pour Python

Prenez en main la bibliothèque de client de Registre confidentiel Microsoft Azure pour Python. Suivez les étapes ci-dessous pour installer le package et tester un exemple de code relatif à des tâches de base.

Registre confidentiel Microsoft Azure est un nouveau service, hautement sécurisé, pour la gestion des enregistrements de données sensibles. Sur la base d’un modèle blockchain donné, Registre confidentiel Azure offre des avantages uniques en termes d’intégrité des données, tels que l’immuabilité (en permettant uniquement l’ajout au registre) et l’inviolabilité (pour garantir que tous les enregistrements sont conservés intacts).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[Documentation de référence sur les API](/python/api/overview/azure/keyvault-secrets-readme) | [Code source de la bibliothèque](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/confidentialledger) | [Bibliothèque de gestion des packages (index de packages Python)](https://pypi.org/project/azure-mgmt-confidentialledger/)| [Bibliothèque cliente de packages (index de packages Python)](https://pypi.org/project/azure-confidentialledger/)

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure - [En créer un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python 3.6+](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="set-up"></a>Configurer

Ce guide de démarrage rapide utilise la bibliothèque Azure Identity ainsi qu’Azure CLI ou PowerShell pour authentifier l’utilisateur auprès des services Azure. Les développeurs peuvent également utiliser Visual Studio ou Visual Studio Code pour authentifier leurs appels. Pour plus d’informations, consultez [Authentifier le client avec la bibliothèque cliente Azure Identity](/python/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Connexion à Azure

[!INCLUDE [Sign in to Azure](../../includes/confidential-ledger-sign-in-azure.md)]

### <a name="install-the-packages"></a>Installer les packages

Dans un terminal ou une invite de commandes, créez un dossier de projet approprié, puis créez et activez un environnement virtuel Python comme décrit dans [Utiliser des environnements virtuels Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments).

Installez la bibliothèque cliente d’identités Azure Active Directory :

```terminal
pip install azure-identity
```

Installez la bibliothèque de client du plan de contrôle de Registre confidentiel.

```terminal
pip install azure.mgmt.confidentialledger
```

Installez la bibliothèque de client du plan de données de Registre confidentiel.

```terminal
pip install azure.confidentialledger 
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

[!INCLUDE [Create a resource group](../../includes/confidential-ledger-rg-create.md)]

### <a name="register-the-microsoftconfidentialledger-resource-provider"></a>Inscrivez le fournisseur de ressources microsoft.ConfidentialLedger

[!INCLUDE [Register the microsoft.ConfidentialLedger resource provider](../../includes/confidential-ledger-register-rp.md)]

## <a name="create-your-python-app"></a>Créer votre application Python

### <a name="initialization"></a>Initialisation

Nous pouvons maintenant commencer à écrire notre application Python.  Commençons par importer les packages requis.

```python
# Import the Azure authentication library

from azure.identity import DefaultAzureCredential

## Import the control plane sdk

from azure.mgmt.confidentialledger import ConfidentialLedger as ConfidentialLedgerAPI
from azure.mgmt.confidentialledger.models import ConfidentialLedger

# import the data plane sdk

from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient
```

Ensuite, nous allons utiliser la [classe DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential) pour authentifier l’application.

```python
credential = DefaultAzureCredential()
```

Nous allons terminer l’installation en définissant certaines variables à utiliser dans votre application : le groupe de ressources (myResourceGroup), le nom du registre que vous souhaitez créer et deux URL à utiliser par la bibliothèque cliente du plan de données.

  > [!Important]
  > Chaque registre doit avoir un nom globalement unique. Remplacez \<your-unique-keyvault-name\> par le nom de votre registre dans l’exemple suivant.

```python
resource_group = "myResourceGroup"
ledger_name = "<your-unique-ledger-name>"
subscription_id = "<azure-subscription-id>"

identity_url = "https://identity.confidential-ledger.core.azure.com"
ledger_url = "https://" + ledger_name + ".confidential-ledger.azure.com"
```

### <a name="use-the-control-plane-client-library"></a>Utiliser la bibliothèque cliente du plan de contrôle

La bibliothèque cliente du plan de contrôle (azure.mgmt.confidentialledger) autorise des opérations sur les registres, comme la création, la modification et la suppression, l’énumération des registres associés à un abonnement et l’obtention des détails d’un registre spécifique.

Dans notre code, nous allons tout d’abord créer un client de plan de contrôle en transmettant à ConfidentialLedgerAPI la variable d’identification et votre ID d’abonnement Azure (les deux étant définis ci-dessus).  

```python
confidential_ledger_mgmt = ConfidentialLedgerAPI(
    credential, subscription_id
)
```

Nous pouvons maintenant créer un registre à l’aide de `begin_create`. La fonction `begin_create` requiert trois paramètres : votre groupe de ressources, un nom pour le registre et un objet « properties ».  

Créez un dictionnaire `properties` avec les clés et les valeurs suivantes et affectez-le à une variable.

```python
properties = {
    "location": "eastus",
    "tags": {},
    "properties": {
        "ledgerType": "Public",
        "aadBasedSecurityPrincipals": [],
    },
}

ledger_properties = ConfidentialLedger(**properties)
```

À présent, transmettez le groupe de ressources, le nom de votre registre et l’objet properties à `begin_create`.

```python
confidential_ledger_mgmt.ledger.begin_create(resource_group, ledger_name, ledger_properties)
```

Pour vérifier que votre compte a été créé avec succès, affichez ses détails à l’aide de la fonction `get`.

```python
myledger = confidential_ledger_mgmt.ledger.get(resource_group, ledger_name)

print("Here are the details of your newly created ledger:")
print (f"- Name: {myledger.name}")
print (f"- Location: {myledger.location}")
print (f"- ID: {myledger.id}")

```

### <a name="use-the-data-plane-client-library"></a>Utiliser la bibliothèque cliente du plan de données

Maintenant que nous disposons d’un registre, nous interagissons avec lui à l’aide de la bibliothèque cliente du plan de données (azure.confidentialledger). 

Tout d’abord, nous allons générer et enregistrer un certificat Registre confidentiel.  

```python
identity_client = ConfidentialLedgerIdentityServiceClient(identity_url)
network_identity = identity_client.get_ledger_identity(
     ledger_id=ledger_name
)

ledger_tls_cert_file_name = "networkcert.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)
```

Nous pouvons maintenant utiliser le certificat réseau, ainsi que l’URL du registre et nos informations d’identification, pour créer un client de registre confidentiel.

```python
ledger_client = ConfidentialLedgerClient(
     endpoint=ledger_url, 
     credential=credential,
     ledger_certificate_path=ledger_tls_cert_file_name
)
```

Nous sommes prêts à écrire dans le registre.  Nous allons le faire à l’aide de la fonction `append_to_ledger`.

```python
append_result = ledger_client.append_to_ledger(entry_contents="Hello world!")
print(append_result.transaction_id)
```

La fonction print renvoie l’ID de transaction de votre écriture dans le registre, qui peut être utilisé pour récupérer le message que vous avez écrit dans le registre.

```python
entry = ledger_client.get_ledger_entry(transaction_id=append_result.transaction_id)
print(entry.contents)
```

La fonction print retourne « Hello world! », car c’est le message dans le registre qui correspond à l’ID de transaction.

## <a name="full-sample-code"></a>Exemple de code complet

```python
from azure.identity import DefaultAzureCredential

## Import control plane sdk

from azure.mgmt.confidentialledger import ConfidentialLedger as ConfidentialLedgerAPI
from azure.mgmt.confidentialledger.models import ConfidentialLedger

# import data plane sdk

from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient

# Set variables

rg = "myResourceGroup"
ledger_name = "<unique-ledger-name>"
subscription_id = "<azure-subscription-id>"

identity_url = "https://identity.confidential-ledger.core.azure.com"
ledger_url = "https://" + ledger_name + ".eastus.cloudapp.azure.com"

# Authentication

# Need to do az login to get default credential to work

credential = DefaultAzureCredential()

# Control plane (azure.mgmt.confidentialledger)
# 
# initialize endpoint with credential and subscription

confidential_ledger_mgmt = ConfidentialLedgerAPI(
    credential, "<subscription-id>"
)

# Create properties dictionary for begin_create call 

properties = {
    "location": "eastus",
    "tags": {},
    "properties": {
        "ledgerType": "Public",
        "aadBasedSecurityPrincipals": [],
    },
}

ledger_properties = ConfidentialLedger(**properties)

# Create a ledger

foo = confidential_ledger_mgmt.ledger.begin_create(rg, ledger_name, ledger_properties)

# Get the details of the ledger you just created

print(f"{rg} / {ledger_name}")
 
print("Here are the details of your newly created ledger:")
myledger = confidential_ledger_mgmt.ledger.get(rg, ledger_name)

print (f"- Name: {myledger.name}")
print (f"- Location: {myledger.location}")
print (f"- ID: {myledger.id}")

# Data plane (azure.confidentialledger)
#
# Create a CL client

identity_client = ConfidentialLedgerIdentityServiceClient(identity_url)
network_identity = identity_client.get_ledger_identity(
     ledger_id=ledger_name
)

ledger_tls_cert_file_name = "networkcert.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)


ledger_client = ConfidentialLedgerClient(
     endpoint=ledger_url, 
     credential=credential,
     ledger_certificate_path=ledger_tls_cert_file_name
)

# Write to the ledger
append_result = ledger_client.append_to_ledger(entry_contents="Hello world!")
print(append_result.transaction_id)

# Read from the ledger
entry = ledger_client.get_ledger_entry(transaction_id=append_result.transaction_id)
print(entry.contents)
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

D’autres articles relatifs à Registre confidentiel Azure peuvent se baser sur ce démarrage rapide. Si vous prévoyez d’utiliser d’autres démarrages rapides et didacticiels, il peut être utile de conserver ces ressources.

Sinon, quand vous en avez terminé avec les ressources créées dans cet article, utilisez la commande Azure CLI [az group delete](/cli/azure/group?#az_group_delete) pour supprimer le groupe de ressources et toutes les ressources qu’il contient :

```azurecli
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de Registre confidentiel Microsoft Azure](overview.md)
