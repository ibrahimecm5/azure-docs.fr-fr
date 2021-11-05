---
title: Autorisation multi-utilisateur à l’aide de la protection des ressources
description: Une présentation de l’autorisation multi-utilisateur à l’aide de protection des ressources.
ms.topic: how-to
ms.date: 10/20/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: b02758f35c12355e401bc94028364df60004bb3e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096399"
---
# <a name="multi-user-authorization-using-resource-guard-preview"></a>Autorisation multi-utilisateur à l’aide de protection des ressources (version préliminaire)

L’autorisation multi-utilisateur (MUA) pour la sauvegarde Azure vous permet d’ajouter une couche supplémentaire de protection aux opérations critiques sur vos coffres Recovery Services. Pour MUA, Sauvegarde Azure utilise une autre ressource Azure appelée protection des ressources pour garantir que les opérations critiques sont effectuées uniquement avec l’autorisation applicable.

Ce document comporte les sections suivantes :

- Fonctionnement de MUA à l’aide de la protection des ressources
- Avant de commencer
- Scénarios de test
- Créer un groupe de ressources
- Activer MUA sur un coffre Recovery Services
- Protéger contre les opérations non autorisées sur un coffre
- Autoriser des opérations critiques sur un coffre
- Activer MUA sur un coffre Recovery Services

>[!NOTE]
>- L’autorisation multi-utilisateur pour la sauvegarde est actuellement en version préliminaire et est disponible dans toutes les régions Azure publiques.
>- Si vous utilisez la version préliminaire avant le 2 novembre 2021, inscrivez votre abonnement à l’aide de la fonctionnalité **AzureBackupResourceGuard** de fonctionnalité d'évaluation sous le fournisseur **Microsoft. RecoveryServices** dans le portail Azure pour commencer.

## <a name="how-does-mua-for-backup-work"></a>Comment fonctionne MUA pour la sauvegarde ?

La sauvegarde Azure utilise la protection des ressources comme service d’autorisation pour un coffre Recovery Services. Par conséquent, pour effectuer une opération critique (décrite ci-dessous), vous devez également disposer des autorisations suffisantes de protection des ressources associées.

> [!Important]
> Pour fonctionner comme prévu, la protection de ressources doit appartenir à un autre utilisateur et l’administrateur du coffre ne doit pas avoir d’autorisations de contributeur. Vous pouvez placer Protection de ressources dans un abonnement ou un locataire différent de celui contenant le coffre Recovery Services pour offrir une meilleure protection.

### <a name="critical-operations"></a>Opérations critiques

Le tableau suivant répertorie les opérations définies en tant qu’opérations critiques et peut être protégée par une protection des ressources. Vous pouvez choisir d’empêcher certaines opérations d’être protégées à l’aide de la protection de ressource lors de l’association de coffres à celle-ci. Notez que les opérations dénotées comme obligatoires ne peuvent pas être exclues de la protection à l’aide du service de protection des ressources pour les coffres qui lui sont associés. En outre, les opérations critiques exclues s’appliquent à tous les coffres associés à un service de protection des ressources.

**opération** | **Obligatoire/facultatif**
--- | ---
Désactiver la suppression réversible | Obligatoire
Désactiver la protection MUA | Obligatoire
Modifier la stratégie de sauvegarde | Facultatif : peut être exclu
Modifier la protection | Facultatif : peut être exclu
Arrêter la protection | Facultatif : peut être exclu
Modifier le code PIN de sécurité MARS | Facultatif : peut être exclu

### <a name="concepts"></a>Concepts
Les concepts et les processus impliqués lors de l’utilisation de MUA pour la sauvegarde sont expliqués ci-dessous.

Examinons les deux utilisateurs suivants pour une compréhension claire du processus et des responsabilités. Ces deux rôles sont référencés tout au long de cet article.

**Admin de sauvegarde**: propriétaire du coffre Recovery Services et effectue des opérations de gestion sur le coffre. Pour commencer, l’administrateur de sauvegarde ne doit pas avoir d’autorisations sur le service de protection des ressources.

**Administrateur de la sécurité** : propriétaire du service de protection des ressources et sert d’opérateur de sécurité pour les opérations critiques sur le coffre. Par conséquent, l’administrateur de la sécurité contrôle les autorisations dont l’administrateur de sauvegarde a besoin pour effectuer des opérations critiques sur le coffre.

Vous trouverez ci-dessous une représentation schématique pour effectuer une opération critique sur un coffre sur lequel MUA est configuré à l’aide d’un service de protection des ressources.

:::image type="content" source="./media/multi-user-authorization/configure-mua-using-resource-card-diagram.png" alt-text="Représentation schématique de la configuration de MUA à l’aide d’un service de protection des ressources.":::
 

Voici le déroulement des événements dans un scénario classique :

1. L’administrateur de sauvegarde crée le coffre de Recovery Services.
1. L’administrateur de la sécurité crée la protection des ressources. La protection de ressource peut se trouver dans un autre abonnement ou un autre locataire que le coffre Recovery Services. Il doit être vérifié que l’administrateur de sauvegarde ne dispose pas des autorisations de contributeur sur le service de protection des ressources.
1. L’administrateur de la sécurité accorde le rôle **Lecteur** à l’administrateur de sauvegarde pour la protection de ressource (ou une étendue appropriée). L’administrateur de sauvegarde a besoin du rôle lecteur pour activer MUA sur le coffre.
1. L’administrateur de sauvegarde configure désormais le coffre Recovery Services pour qu’il soit protégé par MUA via le service de protection des ressources.
1. Désormais, si l’administrateur de sauvegarde souhaite effectuer une opération critique sur le coffre, il doit demander l’accès au service de protection des ressources. L’administrateur de sauvegarde peut contacter l’administrateur de la sécurité pour plus d’informations sur l’accès à ces opérations. Ils peuvent le faire à l’aide de Privileged Identity Management (PIM) ou d’autres processus tels que mandatés par l’organisation.
1. L’administrateur de la sécurité accorde temporairement le rôle **Contributeur** au service de protection des ressources à l’administrateur de sauvegarde pour effectuer des opérations critiques.
1. À présent, l’administrateur de sauvegarde lance l’opération critique.
1. Azure Resource Manager vérifie si l’administrateur de sauvegarde dispose des autorisations suffisantes. Dans la mesure où l’administrateur de sauvegarde dispose désormais du rôle Contributeur sur la protection des ressources, la demande est terminée.
   - Si l’administrateur de sauvegarde ne disposait pas des autorisations/rôles requis, la demande aurait échoué.
1. L’administrateur de la sécurité s’assure que les privilèges d’exécution des opérations critiques sont révoqués après l’exécution des actions autorisées ou après une durée définie. L’utilisation des outils JIT [Azure Active Directory Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure) peut être utile pour s’en assurer.

>[!NOTE]
>- MUA fournit une protection sur les opérations listées ci-dessus effectuées sur les coffres Recovery Services uniquement. Toutes les opérations effectuées directement sur la source de données (par exemple, la ressource/charge de travail Azure protégée) dépassent l’étendue de la protection des ressources. 
>- Cette fonctionnalité est actuellement disponible via le portail Azure uniquement.
>- Cette fonctionnalité est actuellement prise en charge pour les coffres Recovery Services uniquement et non disponible pour les coffres de sauvegarde.

## <a name="before-you-start"></a>Avant de commencer

-  La protection des ressources et le coffre Recovery Services doivent se trouver dans la même région Azure.
-  Comme indiqué dans la section précédente, assurez-vous que l’administrateur de sauvegarde **ne dispose pas** des autorisations de **Contributeur** sur le service de protection des ressources. Vous pouvez choisir de faire en sorte que le service de protection des ressources se trouve dans un autre abonnement du même répertoire ou dans un autre répertoire pour garantir un isolement maximal.

## <a name="usage-scenarios"></a>Scénarios d’usage

Le tableau suivant décrit les scénarios de création de votre service de protection des données et de votre coffre Recovery Services (coffre RS), ainsi que la protection relative offerte par chacun.

>[!Important]
> Dans tous les scénarios, l’administrateur de sauvegarde ne doit pas avoir d’autorisations de contributeur au service de protection des ressources.

**Scénario d’usage** | **Protection due à MUA** | **Simplicité d’implémentation** | **Remarques**
--- | --- |--- |--- |
Le coffre RS et le service de protection des ressources se trouvent **dans le même abonnement.** </br> L’administrateur de sauvegarde n’a pas accès à la protection des ressources. | Isolement minimal entre l’administrateur de sauvegarde et l’administrateur de la sécurité. | Relativement facile à implémenter, car un seul abonnement est requis. | Il est nécessaire de vérifier que les autorisations/rôles au niveau des ressources sont correctement attribués.
Le coffre RS et la protection des ressources se trouvent **dans des abonnements différents, mais le même locataire.** </br> L’administrateur de sauvegarde n’a pas accès à la protection des ressources ou à l’abonnement correspondant. | Isolement moyen entre l’administrateur de sauvegarde et l’administrateur de la sécurité. | La facilité d’implémentation est relativement moyenne, car deux abonnements (mais un seul locataire) sont requis. | Vérifiez que les autorisations/rôles sont correctement attribués pour la ressource ou l’abonnement.
Le coffre RS et le service de protection des ressources se trouvent **dans des locataires différents.** </br> L’administrateur de sauvegarde n’a pas accès à la protection des ressources ou à l’abonnement correspondant, ou au locataire correspondant.| Isolement maximal entre l’administrateur de sauvegarde et l’administrateur de la sécurité, par conséquent, une sécurité maximale. | Relativement difficile à tester, car nécessite deux locataires ou répertoires pour le test. | Vérifiez que les autorisations/rôles sont correctement attribués pour la ressource, l’abonnement ou le répertoire.

 >[!NOTE]
 > Pour cet article, nous allons présenter la création du service de protection des ressources dans un locataire différent qui offre une protection maximale. En ce qui concerne la demande et l’approbation des demandes d’exécution d’opérations critiques, cet article présente les mêmes en utilisant [Azure Active Directory Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure) dans le locataire qui héberge la protection des ressources. Vous pouvez éventuellement utiliser d’autres mécanismes pour gérer les autorisations JIT sur le service de protection des ressources en fonction de votre configuration.

## <a name="creating-a-resource-guard"></a>Création d’un groupe de ressources

L’**administrateur de la sécurité** crée la protection des ressources. Nous vous recommandons de la créer dans un **autre abonnement** ou un **autre locataire** que le coffre. Cependant, elle doit se trouver dans la **même région** que le coffre. L’administrateur de sauvegarde **NE DOIT PAS** avoir d’accès *Contributeur* sur la protection de ressource ou l’abonnement qui le contient.

Dans l’exemple suivant, créez la protection de ressource dans un locataire différent du locataire du coffre.
1. Dans le portail Azure, accédez au répertoire dans lequel vous souhaitez créer la protection de ressource.
   
   :::image type="content" source="./media/multi-user-authorization/portal-settings-directories-subscriptions.png" alt-text="Capture d’écran montrant les paramètres du portail.":::
     
1. Recherchez des **protections de ressources** dans la barre de recherche et sélectionnez l’élément correspondant dans la liste déroulante.
    
   :::image type="content" source="./media/multi-user-authorization/resource-guards-preview-inline.png" alt-text="Capture d’écran montrant les protections de ressources en préversion." lightbox="./media/multi-user-authorization/resource-guards-preview-expanded.png":::
    
   - Cliquez sur **Créer** pour commencer à créer une protection des ressources.
   - Dans le panneau créer, renseignez les informations requises pour cette protection des ressources.
       - Assurez-vous que la protection des ressources se trouve dans les mêmes régions Azure que le coffre Recovery Services.
       - En outre, il est utile d’ajouter une description de l’obtention ou de la demande d’accès pour effectuer des actions sur les coffres associés, si nécessaire. Cette description apparaît également dans les coffres associés pour guider l’administrateur de sauvegarde sur l’obtention des autorisations requises. Vous pouvez modifier la description ultérieurement si nécessaire, mais il est recommandé d’avoir une description bien définie à tout moment.
       
        :::image type="content" source="./media/multi-user-authorization/create-resource-guard.png" alt-text="Capture d’écran montrant comment créer une protection de ressource.":::
                
1. Si vous le souhaitez, ajoutez des balises à la protection des ressources conformément aux exigences
1. Cliquez sur **Vérifier + créer**.
1. Suivez les notifications d’état et de création réussie de la protection des ressources.

### <a name="select-operations-to-protect-using-resource-guard"></a>Sélectionner les opérations à protéger à l’aide de la protection des ressources

Choisissez les opérations que vous souhaitez protéger à l’aide de la protection des ressources en dehors de toutes les opérations critiques prises en charge. Par défaut, toutes les opérations critiques prises en charge sont activées. Toutefois, vous pouvez exempter certaines opérations de tomber sous le portée de MUA à l’aide de la protection des ressources. L’administrateur de la sécurité peut effectuer les opérations suivantes :

1. Dans la protection de ressource créée ci-dessus, accédez à **Propriétés**.
2. Sélectionnez **Désactiver** pour les opérations que vous souhaitez exclure de l’autorisation à l’aide de la protection des ressources. Notez que les opérations **Désactiver la suppression réversible** et **Retirer la protection MUA** ne peuvent pas être désactivées.
3. Si vous le souhaitez, vous pouvez également mettre à jour la description de la protection des ressources à l’aide de ce panneau. 
4. Cliquez sur **Enregistrer**.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-properties.png" alt-text="Capture d’écran montrant les propriétés de protection des ressources de démonstration.":::

## <a name="assign-permissions-to-the-backup-admin-on-the-resource-guard-to-enable-mua"></a>Affecter des autorisations à l’administrateur de sauvegarde sur le service de protection des ressources pour activer MUA

Pour activer MUA sur un coffre, l’administrateur du coffre doit avoir le rôle **Lecteur** sur le service de protection des ressources ou l’abonnement contenant la protection des ressources. Pour affecter le rôle **Lecteur** sur la protection de ressource :

1. Dans la protection de ressource créée ci-dessus, accédez au panneau Access Control (IAM), puis cliquez sur **Ajouter une attribution de rôle**.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-access-control.png" alt-text="Capture d’écran montrant la protection des ressources de démonstration – contrôle d'accès.":::
    
1. Sélectionnez **Lecteur** dans la liste des rôles intégrés, puis cliquez sur **suivant** en bas de l’écran.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-add-role-assignment-inline.png" alt-text="Capture d’écran montrant la protection des ressources de démonstration – ajouter une attribution de rôle." lightbox="./media/multi-user-authorization/demo-resource-guard-add-role-assignment-expanded.png":::

1. Cliquez sur **Sélectionner des membres** et ajoutez l’ID de messagerie de l’administrateur de sauvegarde pour l’ajouter en tant que **Lecteur**. Étant donné que l’administrateur de sauvegarde se trouve dans un autre locataire dans ce cas, il sera ajouté en tant qu’invité au locataire contenant la protection des ressources.

1. Cliquez sur **Sélectionner**, puis passez à **Réviser + affecter** pour terminer l’attribution de rôle.

   :::image type="content" source="./media/multi-user-authorization/demo-resource-guard-select-members-inline.png" alt-text="Capture d’écran montrant la protection des ressources de démonstration – sélectionner des membres." lightbox="./media/multi-user-authorization/demo-resource-guard-select-members-expanded.png":::

## <a name="enable-mua-on-a-recovery-services-vault"></a>Activer MUA sur un coffre Recovery Services

Maintenant que l’administrateur de sauvegarde dispose du rôle lecteur sur la protection des ressources, il peut facilement activer l’autorisation multi-utilisateur sur les coffres gérés par ces derniers. Les étapes à exécuter sont effectuées par l’**administrateur de sauvegarde**.

1. Accédez au coffre Recovery Services. Accédez à **Propriétés** dans le volet de navigation gauche, puis à **Autorisation multi-utilisateur**, puis cliquez sur **Mettre à jour**.

1. Vous avez maintenant la possibilité d’activer MUA et de choisir une protection de ressource à l’aide de l’une des méthodes suivantes :

   1. Vous pouvez spécifier l’URI de la protection des ressources, veillez à spécifier l’URI d’une protection de ressource à laquelle vous avez accès en tant que **Lecteur** et qui correspond aux mêmes régions que le coffre. Vous pouvez trouver l’URI (ID de protection de ressource) de la protection de ressource dans l’écran **Présentation** :

      :::image type="content" source="./media/multi-user-authorization/resource-guard-rg-inline.png" alt-text="Capture d’écran montrant la protection des ressources." lightbox="./media/multi-user-authorization/resource-guard-rg-expanded.png":::
    
   1. Ou vous pouvez sélectionner la protection de ressource dans la liste des protections des ressources auxquelles vous avez accès en tant que **Lecteur**, et celles qui sont disponibles dans la région.

      1. Cliquer sur **Sélectionner la protection de ressources**
      1. Cliquez sur la liste déroulante et sélectionnez le répertoire dans lequel se trouve la protection des ressources.
      1. Cliquez sur **Authentifier** pour valider votre identité et votre accès.
      1. Après l’authentification, choisissez la **protection des ressources** dans la liste affichée.

      :::image type="content" source="./media/multi-user-authorization/testvault1-multi-user-authorization-inline.png" alt-text="Capture d’écran montrant l’autorisation multi-utilisateur." lightbox="./media/multi-user-authorization/testvault1-multi-user-authorization-expanded.png" :::

1. Cliquez sur **Enregistrer** une fois terminé pour activer MUA

   :::image type="content" source="./media/multi-user-authorization/testvault1-enable-mua.png" alt-text="Capture d’écran montrant comment activer l’authentification multi-utilisateur.":::

## <a name="protect-against-unauthorized-protected-operations"></a>Protéger contre les opérations non autorisées (protégées)

Une fois que vous avez activé MUA, les opérations dans l’étendue sont limitées dans le coffre, si l’administrateur de sauvegarde tente de les exécuter sans avoir le rôle requis (c’est-à-dire le rôle Contributeur) sur la protection de ressource.

 >[!NOTE]
 >Il est fortement recommandé de tester votre installation après l’activation de MUA pour vous assurer que les opérations protégées sont bloquées comme prévu et pour s’assurer que MUA est correctement configuré.

Voici une illustration de ce qui se produit lorsque l’administrateur de sauvegarde tente d’effectuer une telle opération protégée (par exemple, la désactivation de la suppression réversible est décrite ici. D’autres opérations protégées ont une expérience similaire). Les étapes suivantes sont effectuées par un administrateur de sauvegarde sans autorisations requises.

1. Pour désactiver la suppression réversible, accédez au coffre Recovery Services > Propriétés > Paramètres de sécurité, puis cliquez sur **Mettre à jour**, ce qui permet d’accéder au Paramètres de sécurité.
1. Désactivez la suppression réversible à l’aide du curseur. Vous êtes informé qu’il s’agit d’une opération protégée et que vous devez vérifier leur accès à la protection des ressources.
1. Sélectionnez le répertoire contenant la protection des ressources et authentifiez-vous. Cette étape n’est peut-être pas nécessaire si la protection des ressources se trouve dans le même répertoire que le coffre.
1. Cliquez sur **Enregistrer**. La requête échoue avec une erreur informant de l’absence d’autorisations suffisantes sur le service de protection des ressources pour vous permettre d’effectuer cette opération.

## <a name="authorize-critical-protected-operations-using-azure-ad-privileged-identity-management"></a>Autoriser les opérations critiques (protégées) à l’aide d’Azure AD Privileged Identity Management

Les sous-sections suivantes traitent de l’autorisation de ces requêtes à l’aide de PIM. Dans certains cas, vous devrez peut-être effectuer des opérations critiques sur vos sauvegardes et MUA peut vous aider à vous assurer qu’elles sont exécutées uniquement lorsque les approbations ou les autorisations appropriées existent. Comme nous l’avons vu précédemment, l’administrateur de sauvegarde doit avoir un rôle Collaborateur sur le service de protection des ressources pour effectuer des opérations critiques qui se trouvent dans l’étendue de la protection des ressources. L’une des façons d’autoriser l’exécution juste-à-temps pour ces opérations consiste à utiliser [Azure Active Directory (Azure AD) Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure).

>[!NOTE]
> Bien que l’utilisation d’Azure AD PIM soit l’approche recommandée, vous pouvez utiliser des méthodes manuelles ou personnalisées pour gérer l’accès de l’administrateur de sauvegarde sur la protection des ressources. Pour gérer manuellement l’accès à la protection des ressources, utilisez le paramètre Contrôle d’accès (IAM) dans la barre de navigation gauche de la protection de ressource et accordez le rôle **Collaborateur** à l’administrateur de sauvegarde.

### <a name="create-an-eligible-assignment-for-the-backup-admin-if-using-azure-ad-privileged-identity-management"></a>Créer une attribution éligible pour l’administrateur de sauvegarde (si vous utilisez Azure AD Privileged Identity Management)

À l’aide de PIM, l’administrateur de la sécurité peut créer une attribution éligible pour l’administrateur de sauvegarde en tant que Contributeur à la protection des ressources. Cela permet à l’administrateur de sauvegarde de déclencher une demande (pour le rôle Contributeur) lorsqu’il doit effectuer une opération protégée. Pour ce faire, l'**administrateur de la sécurité** effectue les opérations suivantes :

1. Dans le locataire de sécurité (qui contient la protection de ressource), accédez à **Privileged Identity Management** (recherchez-le dans la barre de recherche du portail Azure), puis accédez à **Ressources Azure** (sous **Gérer** dans le menu de gauche).
1. Sélectionnez la ressource (la protection de ressource ou l’abonnement/protection des ressources conteneur) à laquelle vous souhaitez affecter le rôle **Contributeur**.

   1. Si vous ne voyez pas la ressource correspondante dans la liste des ressources, assurez-vous d’ajouter l’abonnement conteneur à gérer par PIM.

1. Dans la ressource sélectionnée, accédez à **Affectations** (sous **Gérer** dans le menu de gauche) et accédez à **Ajouter des affectations**.

    :::image type="content" source="./media/multi-user-authorization/add-assignments.png" alt-text="Capture d’écran montrant comment ajouter des attributions.":::

1. Dans Ajouter des attributions
   1. Sélectionnez le rôle de Contributeur.
   1. Accédez à Sélectionner des membres et ajoutez le nom d’utilisateur (ou les ID d’e-mail) de l’administrateur de sauvegarde.
   1. Cliquez sur Suivant.

   :::image type="content" source="./media/multi-user-authorization/add-assignments-membership.png" alt-text="Capture d’écran montrant comment ajouter des attributions – appartenance.":::

1. Dans l’écran suivant
   1. Sous Type d’affectation, choisissez **Éligible**.
   1. Spécifiez la durée de validité de l’autorisation éligible.
   1. Cliquez sur **Attribuer** pour terminer la création de l’attribution éligible.

   :::image type="content" source="./media/multi-user-authorization/add-assignments-setting.png" alt-text="Capture d’écran montrant comment ajouter des attributions – paramètre.":::

### <a name="set-up-approvers-for-activating-contributor-role"></a>Configurer des approbateurs pour l’activation du rôle Contributeur

Par défaut, la configuration ci-dessus ne dispose peut-être pas d’un approbateur (et d’une exigence de flux d’approbation) configuré dans PIM. Pour vous assurer que les approbateurs sont requis pour autoriser uniquement les demandes autorisées, l’administrateur de la sécurité doit effectuer les étapes suivantes.
Notez que si cela n’est pas configuré, toutes les demandes sont approuvées automatiquement sans passer par les administrateurs de la sécurité ou la révision d’un approbateur désigné. Des informations supplémentaires sont disponibles [ici](../active-directory/privileged-identity-management/pim-resource-roles-configure-role-settings.md).

1. Dans Azure AD PIM, sélectionnez **Ressources Azure** dans la barre de navigation de gauche, puis sélectionnez votre protection de ressource.

1. Accédez à **Paramètres** puis accédez au rôle **Contributeur**.

   :::image type="content" source="./media/multi-user-authorization/add-contributor.png" alt-text="Capture d’écran montrant comment ajouter un contributeur.":::

1. Si le paramètre nommé **Approbateurs** affiche Aucun ou affiche des approbateurs incorrects, cliquez sur **Modifier** pour ajouter les réviseurs qui doivent examiner et approuver la demande d’activation pour le rôle Contributeur.

1. Dans l’onglet **Activation**, sélectionnez **Demander une approbation pour activation** et ajouter le ou les approbateurs qui doivent approuver chaque demande. Vous pouvez également sélectionner d’autres options de sécurité telles que l’utilisation de l’authentification MFA et mandater des options de ticket pour activer le rôle Contributeur. Si vous le souhaitez, sélectionnez les paramètres pertinents dans les onglets **Attribution** et **Notification** selon vos besoins.

   :::image type="content" source="./media/multi-user-authorization/edit-role-settings.png" alt-text="Capture d’écran montrant comment modifier le paramètre d’un rôle.":::

1. Cliquez sur **Mettre à jour** une fois terminé.

### <a name="request-activation-of-an-eligible-assignment-to-perform-critical-operations"></a>Demander l’activation d’une attribution éligible pour effectuer des opérations critiques

Une fois que l’administrateur de la sécurité crée une attribution éligible, l’administrateur de sauvegarde doit activer l’attribution pour le rôle Contributeur pour pouvoir effectuer des actions protégées. Les actions suivantes sont effectuées par l'**administrateur de sauvegarde** pour activer l’attribution de rôle.

1. Accédez à [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure). Si la protection de ressource se trouve dans un autre répertoire, basculez vers ce répertoire, puis accédez à [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure).
1. Accédez à Mes rôles > Ressources Azure dans le menu de gauche.
1. L’administrateur de sauvegarde peut voir une attribution Éligible pour le rôle Contributeur. Cliquez sur **Activer** pour l’activer.
1. L’administrateur de sauvegarde est informé via une notification du portail que la demande est envoyée pour approbation.

   :::image type="content" source="./media/multi-user-authorization/identity-management-myroles-inline.png" alt-text="Capture d’écran montrant comment activer les attributions éligibles." lightbox="./media/multi-user-authorization/identity-management-myroles-expanded.png":::

### <a name="approve-activation-of-requests-to-perform-critical-operations"></a>Approuver l’activation des demandes pour effectuer des opérations critiques

Une fois que l’administrateur de sauvegarde a déclenché une demande d’activation du rôle Contributeur, la demande doit être examinée et approuvée par l’**administrateur de la sécurité**.
1. Dans le locataire de sécurité, accédez à [Azure AD Privileged Identity Management.](/azure/active-directory/privileged-identity-management/pim-configure)
1. Accédez à **Approuver les demandes**.
1. Sous **Ressources Azure**, la demande émise par l’administrateur de sauvegarde qui demande l’activation en tant que **Contributeur** peut être consultée.
1. Examinez la demande. Si elle est authentique, sélectionnez la demande, puis cliquez sur **Approuver** pour l’approuver.
1. L’administrateur de sauvegarde est informé par e-mail (ou d’autres mécanismes d’alerte organisationnels) que la demande est désormais approuvée.
1. Une fois approuvé, l’administrateur de sauvegarde peut effectuer des opérations protégées pour la période demandée.

## <a name="performing-a-protected-operation-after-approval"></a>Exécution d’une opération protégée après l’approbation

Une fois que la demande du rôle contributeur de l’administrateur de sauvegarde sur la protection de ressource est approuvée, elle peut effectuer des opérations protégées sur le coffre associé. Si la protection de ressource se trouve dans un autre répertoire, l’administrateur de sauvegarde doit s’authentifier.

>[!NOTE]
> Si l’accès a été attribué à l’aide d’un mécanisme JIT, le rôle Contributeur est retiré à la fin de la période approuvée. Sinon, l’administrateur de la sécurité supprime manuellement le rôle **Contributeur** affecté à l’administrateur de sauvegarde pour effectuer l’opération critique.

## <a name="disable-mua-on-a-recovery-services-vault"></a>Activer MUA sur un coffre Recovery Services

La désactivation de MUA est une opération protégée qui, par conséquent, est protégée à l’aide de MUA. Cela signifie que l’administrateur de sauvegarde doit avoir le rôle Contributeur requis dans la protection de ressource. Des détails sur l’obtention de ce rôle sont décrits ici. Voici un résumé des étapes de désactivation de MUA sur un coffre.
1. L’administrateur de sauvegarde demande le rôle d’administrateur de la sécurité pour le rôle **Contributeur** sur la protection des ressources. Il peut demander à cela d’utiliser les méthodes approuvées par l’organisation, telles que les procédures JIT, comme [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/pim-configure) ou d’autres outils et procédures internes. 
1. L’administrateur de la sécurité approuve la demande (si elle est digne d’être approuvée) et informe l’administrateur de la sauvegarde. À présent, l’administrateur de sauvegarde a le rôle Contributeur sur la protection des ressources.
1. L’administrateur de sauvegarde accède au coffre > Propriétés > Autorisation multi-utilisateur
1. Cliquez sur **Mettre à jour**
   1. Désactiver la case à cocher Protéger avec la protection des ressources
   1. Choisissez le répertoire qui contient la protection des ressources et vérifiez l’accès à l’aide du bouton Authentifier (le cas échéant).
   1. Après **Authentification**, cliquez sur **Enregistrer**. Avec l’accès approprié, la requête doit être correctement effectuée.

