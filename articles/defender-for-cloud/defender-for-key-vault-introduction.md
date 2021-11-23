---
title: 'Microsoft Defender pour Key Vault : avantages et fonctionnalités'
description: Découvrez les avantages et les fonctionnalités de Microsoft Defender pour Key Vault.
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: overview
ms.service: defender-for-cloud
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 2c77fa861a27cd0277ea501ae68f5d35e0cd59bf
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526420"
---
# <a name="introduction-to-microsoft-defender-for-key-vault"></a>Présentation de Microsoft Defender pour Key Vault

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Azure Key Vault est un service cloud qui protège les clés et secrets de chiffrement comme les certificats, chaînes de connexion et mots de passe. 

Activez **Microsoft Defender pour Key Vault** afin de bénéficier d’une protection avancée native Azure contre les menaces pour Azure Key Vault, qui fournit une couche supplémentaire de veille de sécurité. 

## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|**Microsoft Defender pour Key Vault** est facturé comme indiqué dans la [page des tarifs](https://azure.microsoft.com/pricing/details/security-center/)|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/no-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-key-vault"></a>Quels sont les avantages de Microsoft Defender pour Key Vault ?

Microsoft Defender pour Key Vault détecte les tentatives inhabituelles et potentiellement dangereuses d’accès ou d’exploitation des comptes Key Vault. Cette couche de protection vous permet de traiter les menaces sans pour autant être un expert en sécurité ni avoir besoin de gérer des systèmes tiers de monitoring de la sécurité.

Quand des activités anormales se produisent, Defender pour Key Vault affiche des alertes et les envoie éventuellement par e-mail aux membres concernés de votre organisation. Ces alertes fournissent des détails sur l’activité suspecte, ainsi que des suggestions pour examiner et corriger les menaces. 

## <a name="microsoft-defender-for-key-vault-alerts"></a>Alertes Microsoft Defender pour Key Vault
Quand vous recevez une alerte de Microsoft Defender pour Key Vault, nous vous recommandons de l’examiner et d’y répondre en suivant les instructions de [Répondre aux alertes Microsoft Defender pour Key Vault](defender-for-key-vault-usage.md). Microsoft Defender pour Key Vault protège les applications et les informations d’identification. Même si vous connaissez l’application ou l’utilisateur qui a déclenché l’alerte, vérifiez bien la situation dans laquelle s’inscrit cette dernière.

Les alertes s’affichent dans la page **Sécurité** de Key Vault, dans la page Protections de charge de travail et la page des alertes Defender pour le cloud.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Page de sécurité d’Azure Key Vault":::


> [!TIP]
> Vous pouvez simuler des alertes Microsoft Defender pour Key Vault en suivant les instructions de [Validation de la détection des menaces d’Azure Key Vault dans Microsoft Defender pour le cloud](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336).


## <a name="respond-to-microsoft-defender-for-key-vault-alerts"></a>Répondre aux alertes Microsoft Defender pour Key Vault
Quand vous recevez une alerte de [Microsoft Defender pour Key Vault](defender-for-key-vault-introduction.md), nous vous recommandons de l’examiner et d’y répondre de la façon suivante. Microsoft Defender pour Key Vault protège les applications et les informations d’identification. Même si vous connaissez l’application ou l’utilisateur qui a déclenché l’alerte, vérifiez bien la situation dans laquelle s’inscrit cette dernière.  

Les alertes de Microsoft Defender pour Key Vault comprennent ces éléments :

- ID objet
- Nom d’utilisateur principal ou adresse IP de la ressource suspecte 

Selon le *type* d’accès qui s’est produit, certains champs peuvent ne pas être disponibles. Par exemple, si c’est une application qui accède à votre coffre de clés, vous ne verrez pas de nom d’utilisateur principal associé. Si le trafic provient de l’extérieur d’Azure, il n’y aura aucun ID objet.

> [!TIP]
> Des adresses IP Microsoft sont attribuées aux machines virtuelles Azure. Cela signifie qu’une alerte peut contenir une adresse IP Microsoft même si elle se rapporte à l’activité effectuée en dehors de Microsoft. Ainsi, même si une alerte a une adresse IP Microsoft, vous devez tout de même procéder à une investigation comme décrit dans cette page.

### <a name="step-1-identify-the-source"></a>Étape 1. Identifier la source

1. Vérifiez si le trafic provient de votre locataire Azure. Si le pare-feu du coffre de clés est activé, il est probable que vous ayez donné accès à l’utilisateur ou à l’application qui a déclenché cette alerte.
1. Si vous ne pouvez pas vérifier la source du trafic, passez à l'[Etape 2. Répondre en conséquence](#step-2-respond-accordingly).
1. Si vous pouvez identifier la source du trafic dans votre locataire, contactez l’utilisateur ou le propriétaire de l’application. 

> [!CAUTION]
> Microsoft Defender pour Key Vault est conçu pour aider à identifier les activités suspectes provoquées par le vol d’informations d’identification. **N’ignorez pas** l’alerte simplement parce que vous reconnaissez l’utilisateur ou l’application. Contactez le propriétaire de l’application ou l’utilisateur pour vérifier que l’activité est légitime. Vous pouvez créer une règle de suppression pour éliminer le bruit si nécessaire. Pour plus d’informations, consultez [Supprimer les alertes de sécurité](alerts-suppression-rules.md).


### <a name="step-2-respond-accordingly"></a>Étape 2. Répondre en conséquence 
Si vous ne reconnaissez pas l’utilisateur ni l’application, ou si vous pensez que l’accès n’aurait pas dû être autorisé :

- Si le trafic provient d’une adresse IP non reconnue :
    1. Activez le pare-feu Azure Key Vault conformément aux indications de [Configuration des pare-feu et réseaux virtuels Azure Key Vault](../key-vault/general/network-security.md).
    1. Configurez le pare-feu avec des ressources et des réseaux virtuels approuvés.

- Si la source de l’alerte était une application non autorisée ou un utilisateur suspect :
    1. Ouvrez les paramètres de stratégie d’accès du coffre de clés.
    1. Supprimez le principal de sécurité correspondant ou restreignez les opérations qu’il peut effectuer.  

- Si la source de l’alerte possède un rôle Azure Active Directory dans votre locataire :
    1. Contactez votre administrateur.
    1. Déterminez s’il est nécessaire de réduire ou de révoquer les autorisations Azure Active Directory.

### <a name="step-3-measure-the-impact"></a>Étape 3. Evaluer l’impact
Une fois l’impact atténué, examinez les secrets affectés dans votre coffre de clés :
1. Ouvrez la page **Sécurité** de votre coffre de clés Azure et consultez l’alerte déclenchée.
1. Sélectionnez l’alerte spécifique qui a été déclenchée et passez en revue la liste des secrets qui ont fait l’objet d’un accès et l’horodatage.
1. Éventuellement, si vous avez activé les journaux de diagnostic du coffre de clés, examinez les opérations précédentes pour l’adresse IP de l’appelant, l’utilisateur principal ou l’ID objet correspondant.  

### <a name="step-4-take-action"></a>Étape 4. Effectuer une action 
Une fois que vous avez compilé la liste des secrets, des clés et des certificats qui ont été consultés par l’application ou l’utilisateur suspect, vous devez renouveler immédiatement ces objets.

1. Les secrets affectés doivent être désactivés ou supprimés de votre coffre de clés.
1. Si les informations d’identification ont été utilisées pour une application spécifique :
    1. Contactez l’administrateur de l’application et demandez-lui d’auditer son environnement pour identifier toute utilisation des informations d’identification depuis qu’elles ont été compromises.
    1. Si les informations d’identification compromises ont été utilisées, le propriétaire de l’application doit identifier les informations qui ont été consultées et atténuer l’impact.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez découvert Microsoft Defender pour Key Vault.

Pour des informations connexes, consultez les articles suivants : 

- [Alertes de sécurité Key Vault](alerts-reference.md#alerts-azurekv) : section Key Vault de la table de référence pour toutes les alertes de Microsoft Defender pour le cloud
- [Exportation continue des données Defender pour le cloud](continuous-export.md)
- [Supprimer les alertes de sécurité](alerts-suppression-rules.md)
