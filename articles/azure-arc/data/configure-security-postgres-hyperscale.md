---
title: Configurer la sécurité pour votre groupe de serveurs PostgreSQL Hyperscale activé par Azure Arc
description: Configurer la sécurité pour votre groupe de serveurs PostgreSQL Hyperscale activé par Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 92a8e44352d287cd6be1b48ed2c0aa3bc004b75d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294216"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Configurer la sécurité pour votre groupe de serveurs PostgreSQL Hyperscale activé par Azure Arc

Ce document décrit différents aspects liés à la sécurité de votre groupe de serveurs :

- Chiffrement au repos
- Gestion des rôles et des utilisateurs Postgres
   - Perspectives générales
   - Modifier le mot de passe de l’utilisateur administratif _postgres_
- Audit

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>Chiffrement au repos

Vous pouvez implémenter un chiffrement au repos en chiffrant les disques sur lesquels vous stockez vos bases de données et/ou en utilisant des fonctions de base de données pour chiffrer les données que vous insérez ou mettez à jour.

### <a name="hardware-linux-host-volume-encryption"></a>Matériel : Chiffrement du volume hôte Linux

Implémentez un chiffrement des données système pour sécuriser les données résidant sur les disques utilisés par votre configuration de Data Services activés par Azure Arc. Pour en savoir plus, consultez la rubrique suivante :

- [Chiffrement des données au repos](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) sur Linux en général 
- Chiffrement de disque avec la commande de chiffrement LUKS `cryptsetup` (Linux) (spécifiquement https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) ) Les Data Services activés par Azure Arc s’exécutant sur l’infrastructure physique que vous fournissez, vous êtes en charge de la sécurisation de l’infrastructure.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>Logiciels : Utiliser l’extension de `pgcrypto` PostgreSQL dans votre groupe de serveurs

En plus de chiffrer les disques utilisés pour héberger votre installation d’Azure Arc, vous pouvez configurer votre groupe de serveurs PostgreSQL Hyperscale activé par Azure Arc pour exposer des mécanismes que vos applications peuvent utiliser pour chiffrer les données figurant dans vos bases de données. L’extension `pgcrypto` fait partie des extensions `contrib` de Postgres et est disponible dans votre groupe de serveurs PostgreSQL Hyperscale activé par Azure Arc. Vous trouverez plus d’informations sur l’extension `pgcrypto` [ici](https://www.postgresql.org/docs/current/pgcrypto.html).
En résumé, les commandes suivantes vous permettent d’activer l’extension, de la créer et de l’utiliser :

#### <a name="create-the-pgcrypto-extension"></a>Créer l’extension `pgcrypto`

Connectez-vous à votre groupe de serveurs avec l’outil client de votre choix, et exécutez la requête PostgreSQL standard :

```console
CREATE EXTENSION pgcrypto;
```

> Des détails sur la connexion se trouvent [ici](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>Vérifier la liste des extensions prêtes à l’emploi dans votre groupe de serveurs

Vous pouvez vérifier que l’extension de `pgcrypto` est prête à l’emploi en affichant la liste des extensions disponibles dans votre groupe de serveurs.
Connectez-vous à votre groupe de serveurs avec l’outil client de votre choix, et exécutez la requête PostgreSQL standard :

```console
select * from pg_extension;
```
Consultez `pgcrypto` si vous l’avez créée et activée avec les commandes indiquées ci-dessus.

#### <a name="use-the-pgcrypto-extension"></a>Utiliser l’extension `pgcrypto`

Vous pouvez maintenant ajuster le code de vos applications afin qu’elles utilisent tout fonction offerte par `pgcrypto`:

- fonctions de hachage générales ;
- fonctions de hachage de mot de passe ;
- fonctions de chiffrement PGP ;
- Fonctions de chiffrement brut ;
- fonctions de données aléatoires.

Par exemple, pour générer des valeurs de hachage. Exécutez la commande suivante :

```console
select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

Retourne le hachage suivant :

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

Ou, par exemple :

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

Retourne le hachage suivant :

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

Ou, par exemple, pour stocker des données chiffrées telles qu’un mot de passe :

- Une application stocke des secrets dans le tableau suivant :

   ```console
   create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
   ```

- Chiffrez son mot de passe pendant la création d’un utilisateur :

   ```console
   insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
   ```

- Notez que le mot de passe est chiffré :

   ```console
   select * from mysecrets;
   ```

Sortie :

```output
- USERid: 1
- USERname: Me
- USERpassword: $1$Uc7jzZOp$NTfcGo7F10zGOkXOwjHy31
```

Quand vous vous connectez à l’application et entrez un mot de passe, l’application cherche dans la table `mysecrets` et retourne le nom de l’utilisateur s’il y a une correspondance entre le mot de passe fourni à l’application et les mots de passe stockés dans la table. Par exemple :


- Entrez le mauvais mot de passe :
   
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   Output 

   ```output
    USERname
   ---------
   (0 rows)
   ```

- Entrez le bon mot de passe :

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ```

   Sortie :

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

Ce petit exemple montre que vous pouvez chiffrer des données au repos (stocker des données chiffrées) dans PostgreSQL Hyperscale activé par Azure Arc à l’aide de l’extension `pgcrypto` Postgres, et que vos applications peuvent utiliser les fonctions offertes par `pgcrypto` pour manipuler ces données chiffrées.

## <a name="postgres-roles-and-users-management"></a>Gestion des rôles et des utilisateurs Postgres

### <a name="general-perspectives"></a>Perspectives générales

Pour configurer des rôles et des utilisateurs dans votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc, utilisez la méthode Postgres standard pour gérer les rôles et les utilisateurs. Pour plus d’informations, consultez [cette page](https://www.postgresql.org/docs/12/user-manag.html). La définition et la configuration des rôles sont propagées automatiquement à toutes les instances Postgres qui constituent votre groupe de serveurs PostgreSQL Hyperscale avec Azure Arc.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>Modifier le mot de passe de l’utilisateur administratif _postgres_

PostgreSQL Hyperscale avec Azure Arc est fourni avec l’utilisateur administratif Postgres standard, _postgres_, pour lequel vous définissez le mot de passe lors de la création de votre groupe de serveurs.
Le format général de la commande pour modifier le mot de passe est le suivant :

```azurecli
az postgres arc-server edit --name <server group name> --admin-password --k8s-namespace <namespace> --use-k8s
```

Où `--admin-password` est une valeur booléenne qui a trait à la présence d’une valeur dans la variable d’environnement de **session** AZDATA_PASSWORD.

Si la variable d’environnement de **session** AZDATA_PASSWORD existe et qu’elle a une valeur, l’exécution de la commande ci-dessus définit le mot de passe de l’utilisateur postgres sur la valeur de cette variable d’environnement.

Si la variable d’environnement de **session** AZDATA_PASSWORD existe mais n’a pas de valeur ou si la variable d’environnement de **session** AZDATA_PASSWORD n’existe pas, l’exécution de la commande ci-dessus invite l’utilisateur à entrer un mot de passe de manière interactive.

#### <a name="change-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>Modifier le mot de passe de l’utilisateur administratif postgres de manière interactive

1. Supprimer la variable d’environnement de **session** AZDATA_PASSWORD ou supprimer sa valeur
2. Exécutez la commande suivante :

   ```azurecli
   az postgres arc-server edit --name <server group name> --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   Par exemple
   ```azurecli
   az postgres arc-server edit -n postgres01 --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   Vous serez invité à entrer le mot de passe, puis à le confirmer :
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   Le mot de passe étant mis à jour, la sortie de la commande est la suivante :
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="change-the-password-of-the-postgres-administrative-user-using-the-azdata_password-session-environment-variable"></a>Modifier le mot de passe de l’utilisateur administratif postgres à l’aide de la variable d’environnement de **session** AZDATA_PASSWORD :

1. Définissez la valeur de la variable d’environnement de **session** AZDATA_PASSWORD sur le mot de passe que vous souhaitez.
2. Exécutez la commande :
   ```azurecli
   az postgres arc-server edit --name <server group name> --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   Par exemple
   ```azurecli
   az postgres arc-server edit -n postgres01 --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   
   Le mot de passe étant mis à jour, la sortie de la commande est la suivante :
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> Pour vérifier que la variable d’environnement de la session AZDATA_PASSWORD existe, ainsi que sa valeur, exécutez la commande suivante :
> - Sur un client Linux :
>
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - Sur un client Windows avec PowerShell :
>
> ```console
> echo $env:AZDATA_PASSWORD
> ```

## <a name="audit"></a>Audit

Pour les scénarios d’audit, configurez votre groupe de serveurs de façon à ce qu’il utilise les extensions `pgaudit` de Postgres. Pour plus d’informations sur `pgaudit`, consultez le [`pgAudit` projet GitHub](https://github.com/pgaudit/pgaudit/blob/master/README.md). Pour activer l’extension `pgaudit` dans votre groupe de serveurs, consultez [Utiliser les extensions PostgreSQL](using-extensions-in-postgresql-hyperscale-server-group.md).

## <a name="next-steps"></a>Étapes suivantes
- Voir l’extension [`pgcrypto` ](https://www.postgresql.org/docs/current/pgcrypto.html)
- Voir [Utiliser les extensions PostgreSQL](using-extensions-in-postgresql-hyperscale-server-group.md)
