---
title: Utiliser Azure Active Directory – Azure Database pour MySQL
description: Découvrez comment configurer Azure Active Directory (Azure AD) pour l’authentification avec Azure Database pour MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 07/23/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d05b48432fd976bf7e5b8add01532aae361968ec
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167199"
---
# <a name="use-azure-active-directory-for-authentication-with-mysql"></a>Utiliser Azure Active Directory pour l’authentification avec MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Cet article vous détaille les étapes de configuration de l’accès à Azure Active Directory avec Azure Database pour MySQL ainsi que la manière de vous connecter à l’aide d’un jeton Azure AD.

> [!IMPORTANT]
> L’authentification Azure Active Directory est disponible uniquement pour MySQL 5.7 et versions ultérieures.

## <a name="setting-the-azure-ad-admin-user"></a>Configuration de l’utilisateur Administrateur Azure AD

Seul un utilisateur Administrateur Azure AD peut créer/activer des utilisateurs pour l’authentification basée sur Azure AD. Pour créer un utilisateur Administrateur Azure AD, procédez comme suit

1. Dans le Portail Azure, sélectionnez l’instance Azure Database pour MySQL que vous souhaitez activer pour Azure AD.
2. Sous Paramètres, sélectionnez Administrateur Active Directory :

![configurer l’administrateur azure ad][2]

3. Sélectionnez un utilisateur Azure AD valide dans le locataire client pour qu’il devienne l’administrateur Azure AD.

> [!IMPORTANT]
> Lorsque vous définissez l’administrateur, un nouvel utilisateur est ajouté au serveur Azure Database pour MySQL avec toutes les autorisations d’administrateur.

Un seul administrateur Azure AD peut être créé par serveur MySQL et la sélection d’un autre administrateur remplacera l’administrateur Azure AD existant configuré pour le serveur.

Après avoir configuré l’administrateur, vous pouvez vous connecter :

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Connexion à Azure Database pour MySQL à l’aide d’Azure AD

Le diagramme de niveau élevé suivant résume le workflow de l’utilisation de l’authentification Azure AD avec Azure Database pour MySQL :

![flux d’authentification][1]

Nous avons conçu l’intégration Azure AD pour qu’elle fonctionne avec des outils MySQL communs, tels que l’interface de ligne de commande mysql, qui sont indépendants d’Azure AD et ne prennent en charge que la spécification du nom d’utilisateur et du mot de passe lors de la connexion à MySQL. Nous transmettons le jeton Azure AD en tant que mot de passe, comme indiqué dans l’image ci-dessus.

Pour le moment, nous avons testé les clients suivants :

- MySQLWorkbench 
- Interface CLI MySQL

Nous avons également testé la plupart des pilotes d’application courants. Vous pouvez consulter les détails à la fin de cette page.

Voici les étapes nécessaires à l’authentification d’un utilisateur ou d’une application avec Azure AD :

### <a name="prerequisites"></a>Prérequis

Vous pouvez poursuivre dans Azure Cloud Shell, une machine virtuelle Azure ou sur votre ordinateur local. Assurez-vous que [l’interface Azure CLI est installée](/cli/azure/install-azure-cli).

### <a name="step-1-authenticate-with-azure-ad"></a>Étape 1 : S’authentifier avec Azure AD

Commencez par vous authentifier auprès d’Azure AD à l’aide de l’outil Azure CLI. Cette étape n’est pas obligatoire dans Azure Cloud Shell.

```
az login
```

La commande lance une fenêtre de navigateur sur la page d’authentification Azure AD. Pour ce faire, vous devez fournir votre ID d’utilisateur Azure AD et le mot de passe.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Étape 2 : Récupérer un jeton d’accès Azure AD

Appelez l’outil Azure CLI pour obtenir un jeton d’accès pour l’utilisateur authentifié auprès d’Azure AD à l’étape 1 afin d’accéder à Azure Database pour MySQL.

Exemple (pour le cloud public) :

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```
La valeur de la ressource ci-dessus doit être spécifiée exactement comme indiqué. Pour les autres clouds, la valeur de la ressource peut être recherchée à l’aide de ce qui suit :

```azurecli-interactive
az cloud show
```

Pour Azure CLI version 2.0.71 et les versions ultérieures, la commande peut être spécifiée dans la version plus pratique suivante pour tous les clouds :

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```
Avec PowerShell, vous pouvez utiliser la commande suivante pour obtenir le jeton d’accès :

```azurepowershell-interactive
$accessToken = Get-AzAccessToken -ResourceUrl https://ossrdbms-aad.database.windows.net
$accessToken.Token | out-file C:\temp\MySQLAccessToken.txt
```


Une fois l’authentification réussie, Azure AD retourne un jeton d’accès :

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Le jeton est une chaîne base 64 qui code toutes les informations relatives à l’utilisateur authentifié et qui est destinée au service Azure Database pour MySQL.

La validité du jeton d’accès est comprise entre ***5 minutes et 60 minutes***. Nous vous recommandons d’obtenir le jeton d’accès juste avant de lancer la connexion à Azure Database pour MySQL. Vous pouvez utiliser la commande PowerShell suivante pour voir la validité du jeton. 

```azurepowershell-interactive
$accessToken.ExpiresOn.DateTime
```

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Étape 3 : Utiliser un jeton comme mot de passe pour la connexion avec MySQL

Lors de la connexion, vous devez utiliser le jeton d’accès comme mot de passe utilisateur MySQL. Lorsque vous utilisez des clients GUI tels que MySQLWorkbench, vous pouvez utiliser la méthode décrite ci-dessus pour récupérer le jeton. 

#### <a name="using-mysql-cli"></a>Utilisation de l’interface CLI MySQL
Lorsque vous utilisez l’interface CLI, vous pouvez utiliser ce raccourci pour vous connecter : 

**Exemple (Linux/macOS) :**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```
#### <a name="using-mysql-workbench"></a>Utilisation de MySQL Workbench
* Lancez MySQL Workbench, cliquez sur l’option Base de données, puis sur « Se connecter à la base de données »
* Dans le champ nom d’hôte, entrez le nom de domaine complet MySQL, par exemple mydb.mysql.database.azure.com
* Dans le champ nom d’utilisateur, entrez le nom de l’administrateur Azure Active Directory MySQL et ajoutez-le avec le nom du serveur MySQL, et non le nom de domaine complet, par exemple user@tenant.onmicrosoft.com@mydb
* Dans le champ mot de passe, cliquez sur « Stocker dans le coffre » et collez le jeton d’accès à partir du fichier, par exemple C:\temp\MySQLAccessToken.txt
* Cliquez sur l’onglet Avancé et n’oubliez pas de cocher « Activer le plug-in d’authentification en texte clair »
* Cliquez sur OK pour vous connecter à la base de données

#### <a name="important-considerations-when-connecting"></a>Considérations importantes à prendre en compte lors de la connexion :

* `user@tenant.onmicrosoft.com` est le nom de l’utilisateur ou du groupe Azure AD auquel vous essayez de vous connecter
* Ajoutez toujours le nom du serveur après le nom de groupe/utilisateur Azure AD (par exemple, `@mydb`)
* Veillez à utiliser exactement la façon dont le nom d’utilisateur ou de groupe Azure AD est épelé
* Les noms d’utilisateurs et de groupes Azure AD respectent la casse
* Quand vous vous connectez en tant que groupe, utilisez uniquement le nom du groupe (par exemple, `GroupName@mydb`)
* Si le nom contient des espaces, utilisez `\` avant chaque espace pour le placer dans une séquence d’échappement

Notez le paramètre « enable-cleartext-plugin » : vous devez utiliser une configuration similaire avec d’autres clients pour vous assurer que le jeton est envoyé au serveur sans être haché.

Vous êtes maintenant authentifié auprès de votre serveur MySQL à l’aide de l’authentification Azure AD.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Création d’utilisateurs Azure AD dans Azure Database pour MySQL

Pour ajouter un utilisateur Azure AD à votre base de données Azure Database pour MySQL, procédez comme suit après la connexion (voir la section suivante sur la connexion) :

1. Tout d’abord, assurez-vous que l’utilisateur Azure AD `<user>@yourtenant.onmicrosoft.com` est un utilisateur valide dans le locataire Azure AD.
2. Connectez-vous à votre instance Azure Database pour MySQL en tant qu’utilisateur administrateur Azure AD.
3. Créer l’utilisateur `<user>@yourtenant.onmicrosoft.com` dans Azure Database pour MySQL.

**Exemple :**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Pour les noms d’utilisateur dépassant 32 caractères, il est recommandé d’utiliser un alias à la place, à utiliser lors de la connexion : 

Exemple :

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```
> [!NOTE]
> 1. MySQL ignore les espaces de début et de fin. Le nom d’utilisateur ne doit donc pas en contenir. 
> 2. L’authentification d’un utilisateur par le biais d’Azure AD ne donne pas à l’utilisateur des autorisations d’accès aux objets dans la base de données Azure Database pour MySQL. Vous devez accorder manuellement les autorisations requises à l’utilisateur.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Création de groupes Azure AD dans Azure Database pour MySQL

Pour permettre à un groupe Azure AD d’accéder à votre base de données, utilisez le même mécanisme que pour les utilisateurs, mais spécifiez à la place le nom du groupe :

**Exemple :**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Lors de la connexion, les membres du groupe utilisent leurs jetons d’accès personnels, mais se connectent avec le nom du groupe spécifié comme nom d’utilisateur.

## <a name="token-validation"></a>Validation du jeton

L’authentification Azure AD dans Azure Database pour MySQL garantit que l’utilisateur existe dans le serveur MySQL et vérifie la validité du jeton en validant le contenu du jeton. Les étapes de validation de jeton suivantes sont exécutées :

-   Le jeton est signé par Azure AD et n’a pas été falsifié
-   Le jeton a été émis par Azure AD pour le locataire associé au serveur
-   Le jeton n’a pas expiré
-   Le jeton est destiné à la ressource Azure Database pour MySQL (et non pour une autre ressource Azure)

## <a name="compatibility-with-application-drivers"></a>Compatibilité avec des pilotes d’application

La plupart des pilotes sont pris en charge, mais veillez à utiliser les paramètres d’envoi du mot de passe en texte clair, afin que le jeton soit envoyé sans modification.

* C/C++
  * libmysqlclient : Prise en charge
  * mysql-connector-c++ : Prise en charge
* Java
  * Connecteur/J (mysql-connector-java) : Pris en charge, doit utiliser le paramètre `useSSL`
* Python
  * Connecteur/Python : Prise en charge
* Ruby
  * mysql2 : Prise en charge
* .NET
  * mysql-connector-net : Pris en charge, il faut ajouter un plug-in pour mysql_clear_password
  * mysql-net/MySqlConnector : Prise en charge
* Node.js
  * mysqljs : Non pris en charge (n’envoie pas de jeton en texte clair sans correctif)
  * node-mysql2 : Prise en charge
* Perl
  * DBD::mysql : Prise en charge
  * Net::MySQL : Non pris en charge
* Go
  * go-sql-driver : Pris en charge, ajouter `?tls=true&allowCleartextPasswords=true` à la chaîne de connexion

## <a name="next-steps"></a>Étapes suivantes

* Passez en revue les concepts généraux pour [Authentification Azure Active Directory avec Azure Database pour MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
