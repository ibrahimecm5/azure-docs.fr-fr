---
title: Nouveautés d’Azure Database pour MySQL – Serveur unique
description: Découvrez les récentes mises à jour d’Azure Database pour MySQL - Serveur unique, un service de base de données relationnelle du cloud Microsoft qui repose sur MySQL Community Edition.
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: f28b30be15ea4808490851b6fd29c642438a43f8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472653"
---
# <a name="whats-new-in-azure-database-for-mysql---single-server"></a>Nouveautés d’Azure Database pour MySQL – Serveur unique

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL est un service de base de données relationnelle dans Microsoft Cloud. Le service est basé sur le moteur de base de données [MySQL Community Edition](https://www.mysql.com/products/community/) (disponible sous licence GPLv2) et prend en charge les versions 5.6, 5.7 et 8.0. [Azure Database pour MySQL - Serveur unique](./overview.md#azure-database-for-mysql---single-server) est un mode de déploiement qui fournit un service de base de données complètement managé avec des exigences minimales pour les personnalisations de base de données. La plateforme de serveur unique est conçue pour prendre en charge la plupart des fonctions de gestion de base de données, comme les correctifs, les sauvegardes, la haute disponibilité et la sécurité, avec un minimum de configuration et de contrôle de la part de l’utilisateur.

Cet article résume les nouvelles versions et fonctionnalités d’Azure Database pour MySQL - Serveur unique à compter de janvier 2021. Les éléments s’affichent dans l’ordre chronologique inverse, avec les mises à jour les plus récentes en premier.

## <a name="october-2021"></a>Octobre 2021

- **Problèmes connus**

Le client MySQL 8.0.27 est incompatible avec Azure Database pour MySQL – Serveur unique. Toutes les connexions du client MySQL 8.0.27 créées via mysql.exe ou Workbench échouent. Pour contourner ce problème, envisagez d’utiliser une version antérieure du client (antérieure à MySQL 8.0.27) ou de créer une instance d’[Azure Database pour MySQL – Serveur flexible](https://docs.microsoft.com/azure/mysql/flexible-server/overview) à la place.

## <a name="june-2021"></a>Juin 2021
  
Cette version d’Azure Database pour MySQL - Serveur unique comprend les mises à jour suivantes.

- **Activation de la possibilité de modifier le paramètre de serveur`activate_all_roles_on_login` à partir du portail/de l’interface CLI pour MySQL 8.0**

  Les utilisateurs peuvent maintenant changer la valeur du paramètre activate_all_roles_on_login en utilisant le portail Azure et l’interface CLI. Ce paramètre permet de configurer s’il faut permettre l’activation automatique de tous les rôles accordés quand les utilisateurs se connectent au serveur. Pour plus d’informations, consultez [Variables système du serveur](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html).

- **Résolution des bogues #29596969 et #94668 signalés par la communauté MySQL**

  Cette version corrige un problème où l’expression par défaut est ignorée dans une requête CREATE TABLE si le champ a été marqué comme clé primaire pour MySQL 8.0. (Bogue #29596969 et bogue #94668 signalés par la communauté MySQL). Pour plus d’informations, consultez [Bogues MySQL : #94668 : l’expression par défaut prend la valeur NULL lors de l’exécution de la requête CREATE TABLE si le champ est défini comme clé primaire](https://bugs.mysql.com/bug.php?id=94668)

- **Résolution d’un problème où des noms de tables sont en double dans la requête « SHOW TABLE »**

  Nous avons introduit une nouvelle fonction pour donner un contrôle précis du cache des tables pendant une opération sur des tables. En raison d’un défaut du code dans la nouvelle fonctionnalité, l’entrée dans le cache des répertoires peut être mal configurée ou être ajoutée, et provoquer un comportement inattendu comme retourner deux tables avec le même nom. Le cache des répertoires fonctionne seulement pour la requête liée à « SHOW TABLE » ; elle n’a pas d’impact sur les requêtes DML ou DDL. Ce problème a été complètement résolu dans cette version.

- **Augmentation de la valeur par défaut pour le paramètre de serveur`max_heap_table_size` pour aider à réduire les débordements des tables temporaires sur le disque**

  Avec cette version, la valeur maximale autorisée pour le paramètre `max_heap_table_size` a été changée en 8589934592 pour Usage général - 64 vCores et Mémoire optimisée 32 vCores.

- **Résolution d’un problème lié à la définition de la valeur du paramètre `sql_require_primary_key` à partir du portail**

  Les utilisateurs peuvent désormais modifier la valeur du paramètre `sql_require_primary_key` directement à partir du portail Azure.

- **Disponibilité générale de la notification de maintenance planifiée**

  Cette version fournit la disponibilité générale des notifications de maintenance planifiée dans Azure Database pour MySQL - Serveur unique. Pour plus d’informations, consultez l’article [Notification de maintenance planifiée](concepts-planned-maintenance-notification.md).

- **Activation par défaut du paramètre `redirect_enabled`**

  Avec cette version, le paramètre `redirect_enabled` est activé par défaut. La redirection vise à réduire la latence réseau entre les applications clientes et les serveurs MySQL en permettant aux applications de se connecter directement aux nœuds du serveur back-end. La prise en charge de la redirection dans les applications PHP est disponible dans l’extension [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure), développée par Microsoft. Pour plus d’informations, consultez l’article [Se connecter à Azure Database pour MySQL avec une redirection](howto-redirection.md).

>[!Note]
> * La redirection ne fonctionne pas avec la configuration de Private Link. Si vous utilisez Private Link pour la base de données Azure Database pour MySQL, vous pouvez rencontrer un problème de connexion. Pour résoudre le problème, vérifiez que le paramètre redirect_enabled est défini sur « DÉSACTIVÉ » et que l’application cliente est redémarrée.</br>
> * Si vous avez une application PHP qui utilise le pilote de redirection mysqlnd_azure pour se connecter à Azure DB pour MySQL (avec la redirection activée par défaut), vous pouvez être confronté à un problème d’encodage des données qui a un impact sur vos transactions d’insertion.</br>
> Pour résoudre ce problème, deux solutions sont proposées :
>    - Dans le portail Azure, désactivez la redirection en définissant le paramètre redirect_enabled sur « DÉSACTIVÉ », puis redémarrez l’application PHP pour effacer le cache du pilote après la modification.
>     - Définissez explicitement les paramètres liés au jeu de caractères au niveau de la session, en fonction de vos paramètres après l’établissement de la connexion (par exemple « set names utf8mb4 »).

## <a name="february-2021"></a>Février 2021

Cette version d’Azure Database pour MySQL - Serveur unique comprend les mises à jour suivantes.

- Ajout de nouvelles procédures stockées pour prendre en charge l’identificateur de transaction global (GTID) pour les données pour les serveurs de stockage volumineux version 5.7 et 8.0.
- Mise à jour pour prendre en charge les versions MySQL 5.6.50 et 5.7.32.

## <a name="january-2021"></a>Janvier 2021

Cette version d’Azure Database pour MySQL - Serveur unique comprend les mises à jour suivantes.

- Activation de « Réinitialiser le mot de passe » pour corriger automatiquement la première autorisation administrateur.
- Exposition du paramètre de serveur `auto_increment_increment/auto_increment_offset` et de `session_track_gtids`.
- Ajout de nouvelles procédures stockées pour contrôler le vidage/la restauration du pool de tampons InnoDB.
- Exposition du paramètre de serveur de préchauffage InnoDB pour le serveur de stockage volumineux.

## <a name="contacts"></a>Contacts

Pour toute question ou suggestion au sujet de l’utilisation d’Azure Database pour MySQL, contactez l’équipe Azure Database pour MySQL ([@Ask Azure DB pour MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Cette adresse e-mail n’est pas un alias du support technique.

En outre, tenez compte des points de contact suivants le cas échéant :

- Pour contacter le support technique Azure, [émettez un ticket à partir du Portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Pour résoudre un problème relatif à votre compte, enregistrez une [demande de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) sur le portail Azure.
- Pour donner votre avis ou demander de nouvelles fonctionnalités, créez une entrée via [UserVoice](https://feedback.azure.com/d365community/forum/47b1e71d-ee24-ec11-b6e6-000d3a4f0da0).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [tarification d’Azure Database pour MySQL](https://azure.microsoft.com/pricing/details/mysql/server/).
- Parcourez la [documentation publique](./single-server/index.yml) d’Azure Database pour MySQL – Serveur unique.
- Passez en revue les détails sur la [résolution des erreurs courantes](./howto-troubleshoot-common-errors.md).
