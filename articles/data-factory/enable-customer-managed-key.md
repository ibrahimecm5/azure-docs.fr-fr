---
title: Chiffrer Azure Data Factory avec une clé gérée par le client
description: Améliorer la sécurité de Data Factory avec Bring Your Own Key (BYOK)
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.subservice: security
ms.topic: quickstart
ms.date: 10/22/2021
ms.reviewer: mariozi
ms.openlocfilehash: b2af772d4f61f933fc6801827233038f8c8919ad
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849320"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Chiffrer Azure Data Factory avec des clés gérées par le client

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory chiffre les données au repos, y compris les définitions d’entités et les données mises en cache pendant les exécutions. Par défaut, les données sont chiffrées à l’aide d’une clé générée de manière aléatoire et gérée par Microsoft, qui est affectée de manière unique à votre fabrique de données. Pour plus de sécurité, vous pouvez maintenant activer la fonctionnalité Bring Your Own Key (BYOK) avec des clés gérées par le client dans Azure Data Factory. Lorsque vous spécifiez une clé gérée par le client, Data Factory utilise __à la fois__ la clé système Data Factory et la clé CMK pour chiffrer les données client. L’absence d’une de ces clés entraîne le refus d’accès aux données et à la fabrique.

Azure Key Vault est nécessaire pour stocker les clés gérées par le client. Vous pouvez créer vos propres clés et les stocker dans un coffre de clés, ou utiliser les API d’Azure Key Vault pour générer des clés. Le coffre de clés et Data Factory doivent se trouver dans le même locataire Azure Active Directory (Azure AD) et dans la même région, mais ils peuvent être dans des abonnements différents. Pour plus d’informations sur le coffre de clés Azure, consultez la page [Présentation du coffre de clés Azure](../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>À propos des clés gérées par le client

Le diagramme suivant montre comment Data Factory utilise Azure Active Directory et Azure Key Vault pour effectuer des requêtes à l’aide de la clé gérée par le client :

  :::image type="content" source="media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png" alt-text="Diagramme montrant le fonctionnement des clés gérées par le client dans Azure Data Factory":::

La liste suivante décrit les étapes numérotées dans le diagramme :

1. Un administrateur Azure Key Vault accorde des autorisations d’accès à des clés de chiffrement à l’identité managée associée à la fabrique de données
1. Un administrateur Data Factory active la fonctionnalité de clé gérée par le client dans la fabrique
1. Data Factory utilise l’identité managée associée à la fabrique pour authentifier l’accès à Azure Key Vault par le biais d’Azure Active Directory
1. Data Factory encapsule la clé de chiffrement de la fabrique avec la clé du client dans Azure Key Vault
1. Pour les opérations de lecture/écriture, Data Factory envoie des requêtes à Azure Key Vault pour désencapsuler la clé de chiffrement du compte afin d’effectuer des opérations de chiffrement et de déchiffrement

Il existe deux façons d’ajouter un chiffrement à clé gérée par le client aux fabriques de données. L’une consiste à procéder au moment de la création des fabriques dans le portail Azure, l’autre après leur création, dans l’interface utilisateur de Data Factory.

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Conditions préalables : configurer Azure Key Vault et générer des clés

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Activer Suppression réversible et Ne pas vider dans Azure Key Vault

Pour utiliser des clés gérées par le client avec Data Factory, deux propriétés doivent être configurées sur le coffre de clés, __Suppression réversible__ et __Ne pas vider__. Ces propriétés peuvent être activées à l’aide de PowerShell ou d’Azure CLI sur un coffre de clés nouveau ou existant. Pour savoir comment activer ces propriétés sur un coffre de clés existant, consultez [Gestion de la récupération d’Azure Key Vault avec suppression réversible et protection contre la suppression définitive](../key-vault/general/key-vault-recovery.md).

Si vous créez un coffre de clés Azure par le biais du portail Azure, les options __Suppression réversible__ et __Ne pas vider__ peuvent être activées comme suit :

  :::image type="content" source="media/enable-customer-managed-key/01-enable-purge-protection.png" alt-text="Capture d’écran montrant comment activer les options Suppression réversible et Protection contre la suppression définitive lors de la création du coffre de clés":::

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Accorder à Data Factory l’accès à Azure Key Vault

Assurez-vous qu’Azure Key Vault et Azure Data Factory se trouvent dans le même locataire Azure Active Directory (Azure AD) et dans la _même région_. À partir du contrôle d’accès Azure Key Vault, accordez à la fabrique de données les autorisations suivantes : _Obtenir_, _Ne pas inclure la clé_ et _Inclure la clé_. Ces autorisations sont requises pour activer des clés gérées par le client dans Data Factory.

* Si vous souhaitez ajouter le chiffrement à clé gérée par le client [après la création de la fabrique dans l’interface utilisateur de Data Factory](#post-factory-creation-in-data-factory-ui), assurez-vous que l’identité du service managé (MSI) de la fabrique de données dispose des trois autorisations pour Key Vault.
* Si vous souhaitez ajouter le chiffrement à clé gérée par le client [pendant la création de la fabrique dans le portail Azure](#during-factory-creation-in-azure-portal), assurez-vous que l’identité managée affectée par l’utilisateur (UA-MI) dispose des trois autorisations pour Key Vault.

  :::image type="content" source="media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png" alt-text="Capture d’écran montrant comment activer l’accès de Data Factory à Key Vault":::

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Générer ou charger une clé gérée par le client sur Azure Key Vault

Vous pouvez créer vos propres clés et les stocker dans un coffre de clés. Si vous préférez, vous pouvez utiliser les API Azure Key Vault pour générer des clés. Seules les clés RSA 2048 bits sont prises en charge avec le chiffrement Data Factory. Pour plus d’informations, consultez [À propos des clés, des secrets et des certificats](../key-vault/general/about-keys-secrets-certificates.md).

  :::image type="content" source="media/enable-customer-managed-key/03-create-key.png" alt-text="Capture d’écran montrant comment générer une clé gérée par le client":::

## <a name="enable-customer-managed-keys"></a>Activer des clés gérées par le client

### <a name="post-factory-creation-in-data-factory-ui"></a>Après la création de la fabrique dans l’interface utilisateur de Data Factory

Cette section explique comment ajouter le chiffrement à clé gérée par le client dans l’interface utilisateur de Data Factory, _après_ la création de la fabrique.

> [!NOTE]
> Une clé gérée par le client ne peut être configurée que sur une fabrique de données vide. La fabrique de données ne peut pas contenir de ressources telles que des services liés, des pipelines et des flux de données. Il est recommandé d’activer la clé gérée par le client juste après la création de la fabrique.

> [!IMPORTANT]
> Cette approche ne fonctionne pas avec les fabriques managées compatibles avec les réseaux virtuels. Si vous souhaitez chiffrer de telles fabriques, envisagez l’[autre procédure](#during-factory-creation-in-azure-portal).

1. Assurez-vous que l’identité MSI de la fabrique de données dispose des autorisations _Obtenir_, _Ne pas inclure la clé_ et _Inclure la clé_ pour Key Vault.

1. Vérifiez que la fabrique de données est vide. La fabrique de données ne peut pas contenir de ressources telles que des services liés, des pipelines et des flux de données. Pour le moment, le déploiement d’une clé gérée par le client vers une fabrique non vide génère une erreur.

1. Pour localiser l’URI de la clé dans le portail Azure, naviguez jusqu'à Azure Key Vault, puis sélectionnez le paramètre Clés. Sélectionnez la clé souhaitée, puis resélectionnez-la pour afficher ses versions. Sélectionner une version de clé pour afficher les paramètres

1. Copiez la valeur du champ Identificateur de clé, qui fournit l’URI. :::image type="content" source="media/enable-customer-managed-key/04-get-key-identifier.png" alt-text="Capture d’écran montrant comment obtenir l’URI de la clé à partir de Key Vault":::

1. Lancez le portail Azure Data Factory, puis, à l’aide de la barre de navigation sur la gauche, accédez au portail de gestion Data Factory.

1. Cliquez sur l’icône __Clé gérée par le client__. :::image type="content" source="media/enable-customer-managed-key/05-customer-managed-key-configuration.png" alt-text="Capture d’écran montrant comment activer une clé gérée par le client dans l’interface utilisateur de Data Factory.":::

1. Entrer l’URI de la clé gérée par le client que vous avez copiée précédemment

1. Cliquez sur __Enregistrer__ ; le chiffrement de la clé gérée par le client est activé pour Data Factory

### <a name="during-factory-creation-in-azure-portal"></a>Pendant la création de la fabrique dans le portail Azure

Cette section explique comment ajouter le chiffrement à clé gérée par le client dans le portail Azure, _pendant_ le déploiement de la fabrique.

Pour chiffrer la fabrique, Data Factory doit d’abord récupérer la clé gérée par le client de Key Vault. Étant donné que le déploiement de la fabrique est toujours en cours, l’identité MSI n’est pas encore disponible pour effectuer l’authentification auprès de Key Vault. Ainsi, pour utiliser cette approche, le client doit attribuer une identité UA-MI à la fabrique de données. Nous supposons que les rôles sont définis dans l’identité UA-MI et procédons à l’authentification auprès de Key Vault.

Pour en savoir plus sur les identités managées affectées par l’utilisateur, consultez [Types d’identités managées](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) et [Attribution de rôle pour une identité managée affectée par l’utilisateur](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Assurez-vous que l’identité managée affectée par l’utilisateur (UA-MI) dispose des autorisations _Obtenir_, _Ne pas inclure la clé_ et _Inclure la clé_ pour Key Vault.

1. Sous l’onglet __Avancé__, cochez la case _Activer le chiffrement avec une clé gérée par le client_
  :::image type="content" source="media/enable-customer-managed-key/06-user-assigned-managed-identity.png" alt-text="Capture d’écran de l’onglet Avancé illustrant la création d’une fabrique de données dans le portail Azure":::

1. Fournissez l’URL de la clé gérée par le client stockée dans Key Vault.

1. Sélectionnez une identité managée affectée par l’utilisateur appropriée pour effectuer l’authentification auprès de Key Vault.

1. Poursuivez le déploiement de la fabrique.

## <a name="update-key-version"></a>Mettre à jour la version de la clé

Lors de la création d’une nouvelle version d’une clé, mettez à jour la fabrique de données afin qu’elle utilise cette nouvelle version. Suivez les étapes similaires décrites dans la section consacrée à l’[interface utilisateur de Data Factory](#post-factory-creation-in-data-factory-ui), notamment :

1. Rechercher l’URI de la nouvelle version de clé via le portail Azure Key Vault

1. Accéder au paramètre __Clé gérée par le client__

1. Remplacer et coller l’URI de la nouvelle clé

1. Cliquer sur __Enregistrer__ ; Data Factory chiffrera à présent les données avec la nouvelle version de clé

## <a name="use-a-different-key"></a>Utiliser une autre clé

Pour modifier la clé utilisée pour le chiffrement Data Factory, vous devez mettre à jour manuellement les paramètres dans Data Factory. Suivez les étapes similaires décrites dans la section consacrée à l’[interface utilisateur de Data Factory](#post-factory-creation-in-data-factory-ui), notamment :

1. Rechercher l’URI de la nouvelle clé via le portail Azure Key Vault

1. Accéder au paramètre __Clé gérée par le client__

1. Remplacer et coller l’URI de la nouvelle clé

1. Cliquer sur __Enregistrer__ ; Data Factory chiffrera à présent les données avec la nouvelle clé

## <a name="disable-customer-managed-keys"></a>Désactiver les clés gérées par le client

Par défaut, une fois que la fonctionnalité de clé gérée par le client est activée, vous ne pouvez pas supprimer l’étape de sécurité supplémentaire. Le système attendra toujours une clé fournie par le client pour chiffrer les fabriques et les données.

## <a name="customer-managed-key-and-continuous-integration-and-continuous-deployment"></a>Clé gérée par le client et intégration et déploiement continus

Par défaut, la configuration de CMK n’est pas incluse dans le modèle ARM (Azure Resource Manager). Pour inclure les paramètres de chiffrement à clé gérée par le client dans le modèle ARM pour l’intégration continue (CI/CD) :

1. S’assurer que la fabrique est en mode Git
1. Accéder au portail de gestion - section clé gérée par le client
1. Sélectionner l’option _Inclure dans le modèle ARM_

  :::image type="content" source="media/enable-customer-managed-key/07-include-in-template.png" alt-text="Capture d’écran montrant l’inclusion du paramètre de clé gérée par le client dans le modèle ARM.":::

Les paramètres suivants seront ajoutés dans le modèle ARM. Ces propriétés peuvent être paramétrées dans des pipelines d’intégration et de livraison continues en modifiant la [configuration des paramètres Azure Resource Manager](continuous-integration-delivery-resource-manager-custom-parameters.md).

  :::image type="content" source="media/enable-customer-managed-key/08-template-with-customer-managed-key.png" alt-text="Capture d’écran montrant l’inclusion du paramètre de clé gérée par le client dans le modèle Azure Resource Manager.":::

> [!NOTE]
> L’ajout du paramètre de chiffrement aux modèles ARM ajoute un paramètre de niveau fabrique qui remplace les autres paramètres de niveau fabrique, tels que les configurations git, dans d’autres environnements. Si ces paramètres sont activés dans un environnement avec privilèges élevés, comme UAT ou PROD, reportez-vous à [Paramètres globaux dans CI/CD](author-global-parameters.md#cicd).

## <a name="next-steps"></a>Étapes suivantes

Consultez les [didacticiels](tutorial-copy-data-dot-net.md) pour en savoir plus sur l’utilisation de Data Factory dans d’autres scénarios.
