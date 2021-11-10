---
title: Accéder aux synthèses stockées dans Registre confidentiel Azure
description: Accédez aux synthèses stockées dans le Registre confidentiel Azure avec un registre Azure SQL Database.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: rothja
ms.author: jroth
ms.openlocfilehash: f011494412b6f9cf1ac186f171b62fe82d02aaaa
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061736"
---
# <a name="access-the-digests-stored-in-confidential-ledger"></a>Accéder aux synthèses stockées dans le Registre confidentiel

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Le Registre Azure SQL Database est actuellement en préversion publique.

Cet article vous montre comment accéder à une synthèse de [registre Azure SQL Database](ledger-overview.md) stockée dans le [Registre confidentiel Azure](../../confidential-ledger/index.yml) pour bénéficier de garanties de sécurité et d’intégrité de bout en bout. Dans cet article, nous expliquerons comment vérifier l’intégrité des informations stockées.

## <a name="prerequisites"></a>Configuration requise

- Python 2.7, 3.5.3 ou version ultérieure.
- Azure SQL Database avec registre activé. Si vous n’avez pas encore créé de base de données dans SQL Database, consultez [Démarrage rapide : Créer une base de données dans Azure SQL Database avec le registre activé](ledger-create-a-single-database-with-ledger-enabled.md).
- [Bibliothèque de client du Registre confidentiel Azure pour Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger).
- Une instance en cours d’exécution du [Registre confidentiel](../../confidential-ledger/index.yml).

## <a name="how-does-the-integration-work"></a>Fonctionnement du travail d’intégration

Azure SQL Server calcule régulièrement les synthèses des [bases de données de registre](ledger-overview.md#ledger-database) et les stocke dans le Registre confidentiel. Vous pouvez vérifier l’intégrité des données à tout moment. Téléchargez les synthèses à partir du Registre confidentiel et comparez-les à celles stockées dans un registre SQL Database. Ce processus est expliqué ci-dessous.

## <a name="1-find-the-digest-location"></a>1. Rechercher l’emplacement des synthèses

> [!NOTE]
> La requête retourne plus d’une ligne si plusieurs instances du Registre confidentiel ont été utilisées pour stocker la synthèse. Pour chaque ligne, répétez les étapes 2 à 6 pour télécharger les synthèses à partir de toutes les instances du Registre confidentiel.

Utilisez [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) pour exécuter la requête suivante. La sortie indique le point de terminaison de l’instance du Registre confidentiel dans laquelle les synthèses sont stockées.

```sql
SELECT * FROM sys.database_ledger_digest_locations WHERE path like '%.confidential-ledger.azure.com%'
```

## <a name="2-determine-the-subledgerid"></a>2. Déterminer le subledgerid

Nous cherchons à connaître la valeur dans la colonne path de la sortie de la requête. Elle se compose de deux parties : `host name` et `subledgerid`. Par exemple, dans l’URL `https://contoso-ledger.confidential-ledger.azure.com/sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000`, `host name` est `https://contoso-ledger.confidential-ledger.azure.com` et `subledgerid` est `sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000`. Nous allons l’utiliser à l’étape 4 pour télécharger les synthèses.

## <a name="3-obtain-an-azure-ad-token"></a>3. Obtenir un jeton Azure AD

L’API Registre confidentiel accepte un jeton de porteur Azure AD (Active Directory) comme identité de l’appelant. Cette identité a besoin d’accéder au Registre confidentiel par le biais d’Azure Resource Manager pendant le provisionnement. Quand vous activez le registre dans SQL Database, vous bénéficiez automatiquement d’un accès administrateur au Registre confidentiel. Pour obtenir un jeton, vous devez vous authentifier à l’aide d’[Azure CLI](/cli/azure/install-azure-cli) avec le même compte que celui utilisé avec le portail Azure. Après vous être authentifié, vous pouvez utiliser [AzureCliCredential](/python/api/azure-identity/azure.identity.azureclicredential) pour récupérer un jeton de porteur et appeler l’API Registre confidentiel.

Connectez-vous à Azure AD à l’aide de l’identité avec accès au Registre confidentiel.

```azure-cli
az login
```

Récupérez le jeton du porteur.

```python
from azure.identity import AzureCliCredential
credential = AzureCliCredential()
```

## <a name="4-download-the-digests-from-confidential-ledger"></a>4. Télécharger les synthèses à partir du Registre confidentiel

Le script Python suivant télécharge les synthèses à partir du Registre confidentiel. Le script utilise la [bibliothèque de client du Registre confidentiel pour Python.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/confidentialledger/azure-confidentialledger)

```python
from azure.identity import AzureCliCredential
from azure.confidentialledger import ConfidentialLedgerClient
from azure.confidentialledger.identity_service import ConfidentialLedgerIdentityServiceClient

ledger_id = "contoso-ledger"
identity_server_url = "https://identity.confidential-ledger.core.azure.com"
sub_ledger_id = "sqldbledgerdigests/ledgersvr2/ledgerdb/2021-04-13T21:20:51.0000000"
ledger_host_url = f"https://{ledger_id}.confidential-ledger.azure.com"
initial_path = f"/app/transactions?api-version=0.1-preview&subLedgerId={sub_ledger_id}"

identity_client = ConfidentialLedgerIdentityServiceClient(identity_server_url)
network_identity = identity_client.get_ledger_identity(
    ledger_id=ledger_id
)

ledger_tls_cert_file_name = f"{ledger_id}_certificate.pem"
with open(ledger_tls_cert_file_name, "w") as cert_file:
    cert_file.write(network_identity.ledger_tls_certificate)

credential = AzureCliCredential()
ledger_client = ConfidentialLedgerClient(
    endpoint=ledger_host_url, 
    credential=credential,
    ledger_certificate_path=ledger_tls_cert_file_name
)

ranged_result = ledger_client.get_ledger_entries(
    sub_ledger_id=sub_ledger_id
)

entries = 0

for entry in ranged_result:
    entries += 1
    print(f"\nTransaction id {entry.transaction_id} contents: {entry.contents}")

if entries == 0:
    print("\n***No digests are found for the supplied SubledgerID.")
else:
    print("\n***No more digests were found for the supplied SubledgerID.")
```

## <a name="5-download-the-digests-from-the-sql-server"></a>5. Télécharger les synthèses à partir de SQL Server

> [!NOTE]
> Cette étape offre un moyen de vérifier que les hachages stockés dans le registre SQL Database n’ont pas changé au fil du temps. Pour un audit complet de l’intégrité du registre SQL Database, consultez [Vérifier une table du registre pour détecter la falsification](ledger-verify-database.md).

Utilisez [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) pour exécuter la requête suivante. La requête retourne les synthèses des blocs de Genesis.

```sql
SELECT * FROM sys.database_ledger_blocks
```

## <a name="6-comparison"></a>6. Comparaison

Comparez la synthèse récupérée à partir du Registre confidentiel à celle retournée par votre base de données dans SQL Database en utilisant `block_id` comme clé. Par exemple, la synthèse de `block_id` = `1` est la valeur de la colonne `previous_block_hash` dans la ligne `block_id`= `2`. De même, pour `block_id` = `3`, il s’agit de la valeur de la colonne `previous_block_id` dans la ligne `block_id` = `4`. Une non-correspondance dans la valeur de hachage est un indicateur de falsification de données potentielle.

Si vous suspectez une falsification de données, consultez [Vérifier une table du registre pour détecter la falsification](ledger-verify-database.md) afin d’effectuer un audit complet du registre SQL Database.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du registre Azure SQL Database](ledger-overview.md)
- [Registre de base de données](ledger-database-ledger.md)
- [Gestion des synthèses et vérification de base de données](ledger-digest-management-and-database-verification.md)
- [Tables de registre d’ajout uniquement](ledger-append-only-ledger-tables.md)
- [Tables de registre pouvant être mises à jour](ledger-updatable-ledger-tables.md)
- [Vérifier une table du registre pour détecter une falsification](ledger-verify-database.md)
