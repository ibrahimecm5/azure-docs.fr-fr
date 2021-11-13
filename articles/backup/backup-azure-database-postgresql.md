---
title: Sauvegarder Azure Database pour PostgreSQL
description: Découvrir la sauvegarde Azure Database pour PostgreSQL avec conservation à long terme (préversion)
ms.topic: conceptual
ms.date: 11/02/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 4121326bf36c71c3f98894bd8413dc1ae5f57a06
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458922"
---
# <a name="azure-database-for-postgresql-backup-with-long-term-retention-preview"></a>Sauvegarde Azure Database pour PostgreSQL avec conservation à long terme (préversion)

Cet article explique comment sauvegarder un serveur Azure Database pour PostgreSQL.

## <a name="configure-backup-on-azure-postgresql-databases"></a>Configurer la sauvegarde sur des bases de données Azure PostgreSQL

Vous pouvez configurer une sauvegarde sur plusieurs bases de données sur plusieurs serveurs Azure PostgreSQL. Pour configurer la sauvegarde sur les bases de données Azure PostgreSQL à l’aide de Sauvegarde Azure, procédez comme suit :

1. Accédez à **Coffre de sauvegarde** ->  **+Sauvegarde**.

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-inline.png" alt-text="Capture d’écran montrant l’option permettant d’ajouter une sauvegarde." lightbox="./media/backup-azure-database-postgresql/adding-backup-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/adding-backup-details-inline.png" alt-text="Capture d’écran montrant l’option permettant d’ajouter des informations de sauvegarde." lightbox="./media/backup-azure-database-postgresql/adding-backup-details-expanded.png":::

   Vous pouvez également accéder à cette page à partir du [Centre de sauvegarde](./backup-center-overview.md). 

1. Sélectionnez [Créer une stratégie de sauvegarde](#create-backup-policy) qui définit la planification de sauvegarde et la durée de rétention.

   :::image type="content" source="./media/backup-azure-database-postgresql/create-or-add-backup-policy-inline.png" alt-text="Capture d’écran montrant l’option permettant d’ajouter une stratégie de sauvegarde." lightbox="./media/backup-azure-database-postgresql/create-or-add-backup-policy-expanded.png":::

1. **Sélectionnez les bases de données Azure Postgres à sauvegarder** : Choisissez l’un des serveurs Azure PostgreSQL parmi les abonnements s’ils se trouvent dans la même région que celle du coffre. Développez la flèche pour voir la liste des bases de données au sein d’un serveur.

   >[!Note]
   >Vous ne pouvez pas (et n’avez pas besoin de) sauvegarder les bases de données *azure_maintenance* et *azure_sys*. En outre, vous ne pouvez pas sauvegarder une base de données déjà sauvegardée dans un coffre de sauvegarde.

   :::image type="content" source="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-inline.png" alt-text="Capture d’écran montrant l’option permettant de sélectionner une base de données Azure PostgreSQL." lightbox="./media/backup-azure-database-postgresql/select-azure-postgresql-databases-to-back-up-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-inline.png" alt-text="Capture d’écran montrant comment choisir un serveur Azure PostgreSQL." lightbox="./media/backup-azure-database-postgresql/choose-an-azure-postgresql-server-expanded.png":::


1. **Attribuez le coffre de clés Azure** qui stocke les informations d’identification pour se connecter à la base de données sélectionnée. Pour attribuer le coffre de clés au niveau de chaque ligne, cliquez sur **Sélectionner un coffre de clés et un secret**. Vous pouvez également attribuer le coffre de clés en sélectionnant plusieurs lignes et en cliquant sur Attribuer un coffre de clés dans le menu supérieur de la grille. 

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-azure-key-vault-inline.png" alt-text="Capture d’écran montrant comment attribuer un coffre de clés Azure." lightbox="./media/backup-azure-database-postgresql/assign-azure-key-vault-expanded.png"::: 

1. Pour spécifier les informations relatives au secret, utilisez l’une des options suivantes : 

   1. **Entrer l’URI de secret** : Utilisez cette option si l’URI de secret est partagé ou connu par vous. Vous pouvez copier l’**URI de secret à partir de coffre de clés** -> **Secrets (sélectionnez un secret)**  -> **Identificateur du secret**.

      :::image type="content" source="./media/backup-azure-database-postgresql/enter-secret-uri-inline.png" alt-text="Capture d’écran montrant comment entrer l’URI de secret." lightbox="./media/backup-azure-database-postgresql/enter-secret-uri-expanded.png":::  

      Toutefois, avec cette option, Sauvegarde Azure n’a aucune visibilité sur le coffre de clés que vous avez référencé. Par conséquent, les autorisations d’accès sur le coffre de clés ne peuvent pas être accordées en ligne. L’administrateur de la sauvegarde ainsi que l’administrateur Postgres et/ou du coffre de clés doivent s’assurer que [l’accès du coffre de sauvegarde au coffre de clés est accordé manuellement](backup-azure-database-postgresql-overview.md#access-permissions-on-the-azure-key-vault-associated-with-the-postgresql-server) en dehors du flux de configuration de la sauvegarde pour que l’opération de sauvegarde réussisse.

   1. **Sélectionner le coffre de clés** : Utilisez cette option si vous connaissez le nom du coffre de clés et du secret. Grâce à cette option, vous (administrateur de la sauvegarde ayant un accès en écriture sur le coffre de clés) pouvez accorder les autorisations d’accès au coffre de clés en ligne. Le coffre de clés et le secret peuvent être préexistants ou créés en cours de route. Assurez-vous que le secret désigne la chaîne de connexion du serveur PG au format ADO.net, mise à jour avec les informations d’identification de l’utilisateur de base de données qui a reçu les privilèges de « sauvegarde » sur le serveur. En savoir plus sur [la création de secrets dans le coffre de clés](#create-secrets-in-the-key-vault).

      :::image type="content" source="./media/backup-azure-database-postgresql/assign-secret-store-inline.png" alt-text="Capture d’écran montrant comment attribuer un magasin de secrets." lightbox="./media/backup-azure-database-postgresql/assign-secret-store-expanded.png":::

      :::image type="content" source="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-inline.png" alt-text="Capture d’écran montrant la sélection du secret à partir d’Azure Key Vault." lightbox="./media/backup-azure-database-postgresql/select-secret-from-azure-key-vault-expanded.png":::   

1. Lorsque la mise à jour des informations de secret est terminée, la validation démarre après la mise à jour des informations du coffre de clés. Ici, le service de sauvegarde vérifie qu’il dispose de toutes les [autorisations d’accès](backup-azure-database-postgresql-overview.md#key-vault-based-authentication-model) nécessaires pour lire les détails du secret dans le coffre de clés et se connecter à la base de données. Si une ou plusieurs autorisations d’accès sont manquantes, l’un des messages d’erreur suivants s’affiche : _« Attribution de rôle non effectuée » ou « L’utilisateur ne peut pas attribuer de rôles »_ .

   :::image type="content" source="./media/backup-azure-database-postgresql/validation-of-secret-inline.png" alt-text="Capture d’écran montrant la validation du secret." lightbox="./media/backup-azure-database-postgresql/validation-of-secret-expanded.png":::   

   1. **L’utilisateur ne peut pas attribuer de rôles** : Ce message s’affiche lorsque vous (l’administrateur de sauvegarde) ne disposez pas de l’accès en écriture sur le serveur PostgreSQL et/ou le coffre de clés pour attribuer les autorisations manquantes, comme indiqué sous **Afficher les détails**. Téléchargez le modèle d’attribution à partir du bouton d’action et faites-le exécuter par l’administrateur PostgreSQL et/ou du coffre de clés. Il s’agit d’un modèle ARM qui vous permet d’attribuer les autorisations nécessaires sur les ressources requises. Une fois le modèle exécuté avec succès, cliquez sur **Revalider** sur la page Configurer la sauvegarde.

      :::image type="content" source="./media/backup-azure-database-postgresql/download-role-assignment-template-inline.png" alt-text="Capture d’écran montrant l’option permettant de télécharger le modèle d’attribution de rôle." lightbox="./media/backup-azure-database-postgresql/download-role-assignment-template-expanded.png":::    

   1. **Attribution de rôle non effectuée** : Ce message s’affiche lorsque vous (l’administrateur de sauvegarde) disposez de l’accès en écriture sur le serveur PostgreSQL et/ou le coffre de clés pour attribuer les autorisations manquantes, comme indiqué sous **Afficher les détails**. Utilisez le bouton d’action **Attribuer les rôles manquants** dans le menu d’action supérieur pour accorder des autorisations sur le serveur PostgreSQL et/ou le coffre de clés en ligne.

      :::image type="content" source="./media/backup-azure-database-postgresql/role-assignment-not-done-inline.png" alt-text="Capture d’écran montrant l’erreur concernant l’attribution de rôle non effectuée." lightbox="./media/backup-azure-database-postgresql/role-assignment-not-done-expanded.png":::     

1. Sélectionnez **Attribuer les rôles manquants** dans le menu supérieur et attribuer des rôles. Une fois le processus démarré, les [autorisations d’accès manquantes](backup-azure-database-postgresql-overview.md#azure-backup-authentication-with-the-postgresql-server) sur le coffre de clés et/ou le serveur PG sont accordées au coffre de sauvegarde. Vous pouvez définir l’étendue sur laquelle les autorisations d’accès doivent être accordées. Une fois l’action terminée, la revalidation démarre.

   :::image type="content" source="./media/backup-azure-database-postgresql/assign-missing-roles-inline.png" alt-text="Capture d’écran montrant l’option permettant d’attribuer des rôles manquants." lightbox="./media/backup-azure-database-postgresql/assign-missing-roles-expanded.png":::

   :::image type="content" source="./media/backup-azure-database-postgresql/define-scope-of-access-permission-inline.png" alt-text="Capture d’écran montrant comment définir l’étendue de l’autorisation d’accès." lightbox="./media/backup-azure-database-postgresql/define-scope-of-access-permission-expanded.png":::     

   - Le coffre de sauvegarde accède aux secrets du coffre de clés et exécute une connexion de test sur la base de données pour valider si les informations d’identification ont été entrées correctement. Les privilèges de l’utilisateur de base de données sont également vérifiés pour déterminer [si l’utilisateur de base de données dispose d’autorisations liées à la sauvegarde sur la base de données](backup-azure-database-postgresql-overview.md#database-users-backup-privileges-on-the-database).

   - Par défaut, l’administrateur PostgreSQL aura toutes les autorisations de sauvegarde et de restauration sur la base de données. Par conséquent, les validations devraient réussir.
   - Il est possible qu’un utilisateur avec des privilèges bas n’ait pas d’autorisations de sauvegarde ou de restauration sur la base de données. Par conséquent, les validations échouent. Un script PowerShell est généré dynamiquement (un par enregistrement et par base de données sélectionnée). [Exécutez le script PowerShell pour accorder ces privilèges à l’utilisateur de base de données sur la base de données](#create-secrets-in-the-key-vault). Vous pouvez également attribuer ces privilèges à l’aide de l’administrateur PG ou de l’outil PSQL.

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-inline.png" alt-text="Capture d’écran montrant le coffre de sauvegarde accédant aux secrets du coffre de clés." lightbox="./media/backup-azure-database-postgresql/backup-vault-accesses-secrets-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/run-test-connection.png" alt-text="Capture d’écran montrant le processus de démarrage de la connexion de test.":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-inline.png" alt-text="Capture d’écran montrant comment fournir les informations d’identification de l’utilisateur pour exécuter le test." lightbox="./media/backup-azure-database-postgresql/user-credentials-to-run-test-connection-expanded.png":::      

1. Conservez les enregistrements dont la préparation à la sauvegarde est réussie pour passer à la dernière étape de l’envoi de l’opération.

   :::image type="content" source="./media/backup-azure-database-postgresql/backup-readiness-as-success-inline.png" alt-text="Capture d’écran montrant que la préparation de la sauvegarde est réussie." lightbox="./media/backup-azure-database-postgresql/backup-readiness-as-success-expanded.png":::      

   :::image type="content" source="./media/backup-azure-database-postgresql/review-backup-configuration-details-inline.png" alt-text="Capture d’écran montrant la page de vérification de la configuration de la sauvegarde." lightbox="./media/backup-azure-database-postgresql/review-backup-configuration-details-expanded.png":::      

1. Envoyez l’opération de configuration de la sauvegarde et suivez la progression sous **Instances de sauvegarde**.

   :::image type="content" source="./media/backup-azure-database-postgresql/submit-configure-backup-operation-inline.png" alt-text="Capture d’écran montrant l’envoi de la configuration de la sauvegarde et le suivi de la progression." lightbox="./media/backup-azure-database-postgresql/submit-configure-backup-operation-expanded.png":::      

## <a name="create-backup-policy"></a>Créer la stratégie de sauvegarde

Vous pouvez créer une stratégie de sauvegarde en cours de route pendant le flux de configuration de la sauvegarde. Vous pouvez également accéder à **Centre de sauvegarde** -> **Stratégies de sauvegarde** -> **Ajouter**.

1. Entrez un nom pour la nouvelle stratégie.

   :::image type="content" source="./media/backup-azure-database-postgresql/enter-name-for-new-policy-inline.png" alt-text="Capture d’écran montrant le processus permettant d’entrer un nom pour la nouvelle stratégie." lightbox="./media/backup-azure-database-postgresql/enter-name-for-new-policy-expanded.png":::

1. Définissez la planification de sauvegarde. Actuellement, seule l’option de sauvegarde hebdomadaire est disponible. Toutefois, vous pouvez planifier les sauvegardes sur plusieurs jours de la semaine.

1. Définissez les paramètres de **Conservation**. Vous pouvez ajouter une ou plusieurs règles de conservation. Chaque règle de conservation nécessite des entrées pour des sauvegardes spécifiques, et le magasin de données et la durée de conservation de ces sauvegardes.

1. Pour stocker vos sauvegardes dans l’un des deux magasins de données (ou niveaux), choisissez **Magasin de données des sauvegardes** (niveau standard) ou **Magasin de données des archives** (en préversion).

1. Choisissez **À l’expiration** si vous préférez déplacer la sauvegarde vers le magasin de données des archives quand elle expire dans le magasin de données des sauvegardes.

   La **règle de rétention par défaut** s’applique en l’absence d’une autre règle de rétention, et sa valeur par défaut est de trois mois.

   - Les durées de conservation vont de 7 jours à 10 ans dans le **Magasin de données des sauvegardes**.
   - Les durées de conservation vont de 6 mois à 10 ans dans le **Magasin de données des archives**.

   :::image type="content" source="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-inline.png" alt-text="Capture d’écran montrant le choix de À l’expiration pour déplacer la sauvegarde vers le magasin de données des archives quand elle expire." lightbox="./media/backup-azure-database-postgresql/choose-option-to-move-backup-to-archive-data-store-expanded.png":::

>[!Note]
>Les règles de conservation sont évaluées selon un ordre de priorité prédéterminé. La priorité est la plus élevée pour la règle annuelle, suivie de la règle mensuelle, puis de la règle hebdomadaire. Les paramètres de conservation par défaut sont appliqués quand aucune autre règle n’est éligible. Par exemple, le même point de récupération peut être la première sauvegarde réussie effectuée chaque semaine ainsi que la première sauvegarde réussie effectuée chaque mois. Cependant, comme la priorité de la règle mensuelle est supérieure à celle de la règle hebdomadaire, la rétention correspondant à la première sauvegarde réussie effectuée chaque mois s’applique.
## <a name="create-secrets-in-the-key-vault"></a>Créer des secrets dans le coffre de clés

Le secret désigne la chaîne de connexion du serveur PG au format _ADO.net_, mise à jour avec les informations d’identification de l’utilisateur de base de données qui a reçu les privilèges de **sauvegarde** sur le serveur. Copiez la chaîne de connexion à partir du serveur PG et modifiez-la dans un éditeur de texte pour mettre à jour _l’identifiant utilisateur et le mot de passe_. 

:::image type="content" source="./media/backup-azure-database-postgresql/pg-server-connection-string-inline.png" alt-text="Capture d’écran montrant la chaîne de connexion du serveur PG en tant que secret." lightbox="./media/backup-azure-database-postgresql/pg-server-connection-string-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/create-secret-inline.png" alt-text="Capture d’écran montrant l’option permettant de créer un secret à l’aide de la chaîne de connexion du serveur PG." lightbox="./media/backup-azure-database-postgresql/create-secret-expanded.png":::

## <a name="run-powershell-script-to-grant-privileges-to-database-users"></a>Exécuter le script PowerShell pour accorder des privilèges aux utilisateurs de base de données

Le script PowerShell généré dynamiquement pendant la configuration de la sauvegarde accepte l’utilisateur de base de données comme entrée, ainsi que les informations d’identification de l’administrateur PG, pour accorder les privilèges liés à la sauvegarde à l’utilisateur de base de données sur la base de données.

L’[outil PSQL](https://www.enterprisedb.com/download-postgresql-binaries) doit être présent sur la machine, et la variable d’environnement PATH doit être définie de manière appropriée sur le chemin d’accès des outils PSQL.

:::image type="content" source="./media/backup-azure-database-postgresql/psql-set-environment-inline.png" alt-text="Capture d’écran montrant l’option permettant de rechercher l’application des paramètres d’environnement." lightbox="./media/backup-azure-database-postgresql/psql-set-environment-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/system-properties-to-set-environment-inline.png" alt-text="Capture d’écran montrant l’option permettant de définir l’environnement sous les propriétés système." lightbox="./media/backup-azure-database-postgresql/system-properties-to-set-environment-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/adding-environment-variables-inline.png" alt-text="Capture d’écran montrant les variables d’environnement par défaut." lightbox="./media/backup-azure-database-postgresql/adding-environment-variables-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/editing-environment-variables-inline.png" alt-text="Capture d’écran montrant les variables d’environnement que vous devez définir." lightbox="./media/backup-azure-database-postgresql/editing-environment-variables-expanded.png":::

Assurez-vous que les **paramètres de sécurité de connexion** dans l’instance Azure PostgreSQL ajoutent l’adresse IP de la machine à la liste d’autorisation pour permettre la connectivité au réseau.

## <a name="generate-an-on-demand-backup"></a>Générer une sauvegarde à la demande

Pour déclencher une sauvegarde qui n’est pas dans la planification spécifiée dans la stratégie, accédez à **Instances de sauvegarde** -> **Sauvegarder maintenant**.
Choisissez dans la liste des règles de conservation qui ont été définies dans la stratégie de sauvegarde associée.

:::image type="content" source="./media/backup-azure-database-postgresql/navigate-to-retention-rules-inline.png" alt-text="Capture d’écran montrant l’option permettant d’accéder à la liste des règles de rétention qui ont été définies dans la stratégie de sauvegarde associée." lightbox="./media/backup-azure-database-postgresql/navigate-to-retention-rules-expanded.png":::

:::image type="content" source="./media/backup-azure-database-postgresql/choose-retention-rules-inline.png" alt-text="Capture d’écran montrant l’option permettant de choisir les règles de rétention qui ont été définies dans la stratégie de sauvegarde associée." lightbox="./media/backup-azure-database-postgresql/choose-retention-rules-expanded.png":::

## <a name="next-steps"></a>Étapes suivantes

[Résoudre les problèmes de sauvegarde de base de données PostgreSQL avec Sauvegarde Azure](backup-azure-database-postgresql-troubleshoot.md)