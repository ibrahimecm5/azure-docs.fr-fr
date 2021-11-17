---
title: Se connecter avec Managed Identity – Azure Database pour PostgreSQL – Serveur unique
description: En savoir plus sur la connexion et l’authentification à l’aide de Managed Identity pour l’authentification avec Azure Database pour PostgreSQL
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 8d7e05ba1e4a21ded5d90de6bea301e4cd94451a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547645"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Se connecter avec Managed Identity auprès d’Azure Database pour PostgreSQL

Vous pouvez utiliser des identités managées affectées par le système et par l’utilisateur pour l’authentification auprès d’Azure Database pour PostgreSQL. Cet article explique comment utiliser une identité managée affectée par le système à une machine virtuelle Azure pour accéder à un serveur Azure Database pour PostgreSQL. Les identités managées sont gérées automatiquement par Azure et vous permettent de vous authentifier auprès des services prenant en charge l’authentification Azure AD sans avoir à insérer des informations d’identification dans votre code.

Vous allez apprendre à effectuer les actions suivantes :
- Accorder à votre machine virtuelle l’accès à un serveur Azure Database pour PostgreSQL
- Créer un utilisateur dans la base de données représentant l’identité affectée par le système de la machine virtuelle
- Obtenir un jeton d’accès à l’aide de l’identité de la machine virtuelle et l’utiliser pour interroger un serveur Azure Database pour PostgreSQL
- Implémenter la récupération de jetons dans un exemple d’application C#

## <a name="prerequisites"></a>Prérequis

- Si vous n’êtes pas familiarisé de la fonctionnalité identités managées pour ressources Azure, consultez cette [Vue d’ensemble](../../articles/active-directory/managed-identities-azure-resources/overview.md). Si vous n’avez pas encore de compte Azure, [Inscrivez-vous sur un compte gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les opérations nécessaires de création de ressources et de gestion de rôles, votre compte doit disposer des autorisations « Propriétaire » au niveau de l’étendue appropriée (votre abonnement ou groupe de ressources). Si vous avez besoin d’aide concernant l’attribution de rôle, consultez [Affecter des rôles Azure pour gérer l’accès à vos ressources d’abonnement Azure](../../articles/role-based-access-control/role-assignments-portal.md).
- Vous avez besoin d’une machine virtuelle Azure (exécutant par exemple Ubuntu Linux) que vous souhaitez utiliser pour accéder à votre base de données à l’aide de Managed Identity
- Vous avez besoin d’un serveur de base de données Azure Database pour PostgreSQL sur lequel [Azure AD Authentication](howto-configure-sign-in-aad-authentication.md) est configurée
- Pour suivre cet exemple C#, vous devez d’abord suivre le guide pratique [Se connecter avec C#](connect-csharp.md)

## <a name="creating-a-system-assigned-managed-identity-for-your-vm"></a>Création d’une identité managée affectée par le système pour votre machine virtuelle

Utilisez [az vm identity assign](/cli/azure/vm/identity/) avec la commande `identity assign` pour activer l’identité affectée par le système sur une machine virtuelle existante :

```azurecli-interactive
az vm identity assign -g myResourceGroup -n myVm
```

Récupérez l’ID d’application de l’identité managée affectée par le système. Vous en aurez besoin dans les étapes suivantes :

```azurecli
# Get the client ID (application ID) of the system-assigned managed identity
az ad sp list --display-name vm-name --query [*].appId --out tsv
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Création d’un utilisateur PostgreSQL pour votre identité managée

Connectez-vous à présent en tant qu’utilisateur administrateur Azure AD à votre base de données PostgreSQL, puis exécutez les instructions SQL suivantes en remplaçant `CLIENT_ID` par l’ID client que vous avez récupéré pour votre identité managée affectée par le système :

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

L’identité managée dispose désormais d’un accès lors de l’authentification avec le nom d’utilisateur `myuser` (remplacez-le par le nom de votre choix).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Récupération du jeton d’accès à partir d’Azure Instance Metadata Service

Votre application peut désormais récupérer un jeton d’accès à partir d’Azure Instance Metadata Service et l’utiliser pour l’authentification auprès de la base de données.

Vous effectuez cette récupération de jeton en envoyant une requête HTTP à `http://169.254.169.254/metadata/identity/oauth2/token` et en transmettant les paramètres suivants :

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (que vous avez récupérés précédemment)

Vous obtenez un résultat JSON qui contient un champ `access_token`. Cette valeur de texte longue est le jeton d’accès Managed Identity que vous devez utiliser comme mot de passe lors de la connexion à la base de données.

À des fins de test, vous pouvez exécuter les commandes suivantes dans votre interpréteur de commandes. Notez que `curl`, `jq` et le client `psql` doivent être installés.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

Vous êtes maintenant connecté à la base de données que vous avez configurée précédemment.

## <a name="connecting-using-managed-identity-in-c"></a>Connexion à l’aide de Managed Identity dans C#

Cette section montre comment obtenir un jeton d’accès à l’aide de l’identité managée affectée par l’utilisateur de la machine virtuelle et comment l’utiliser pour appeler Azure Database pour PostgreSQL. Azure Database pour PostgreSQL prenant en charge Azure AD Authentication en mode natif, il peut accepter directement des jetons d’accès obtenus à l’aide d’identités managées pour les ressources Azure. Lors de la création d’une connexion à PostgreSQL, vous transmettez le jeton d’accès dans le champ du mot de passe.

Voici un exemple de code .NET pour l’ouverture d’une connexion à PostgreSQL à l’aide d’un jeton d’accès. Ce code doit s’exécuter sur la machine virtuelle pour utiliser l’identité managée affectée par le système et obtenir un jeton d’accès auprès d’Azure AD. Remplacez les valeurs de HOST, USER, DATABASE et CLIENT_ID.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;
using Azure.Identity;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal for use in the following variables
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";

        static async Task Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure AD...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            string accessToken = null;

            try
            {
                // Call managed identities for Azure resources endpoint.
                var sqlServerTokenProvider = new DefaultAzureCredential();
                accessToken = (await sqlServerTokenProvider.GetTokenAsync(
                    new Azure.Core.TokenRequestContext(scopes: new string[] { "https://ossrdbms-aad.database.windows.net/.default" }) { })).Token;

            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4}; SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

Quand elle est exécutée, cette commande donne une sortie semblable à celle-ci :

```
Getting access token from Azure AD...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.11, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>Étapes suivantes

* Passez en revue les concepts généraux pour l’[authentification Azure Active Directory avec Azure Database pour PostgreSQL](concepts-aad-authentication.md)
