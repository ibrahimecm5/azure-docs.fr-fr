---
title: Surveiller et dépanner les connexions à l’aide de l’évaluation continue de l’accès dans Azure AD
description: Dépannez et répondez plus rapidement aux changements d’état des utilisateurs grâce à l’évaluation continue de l’accès dans Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/22/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jlu, shreyamalik
ms.collection: M365-identity-device-management
ms.openlocfilehash: 292860ed26a39205f221c213f392b4b120006d13
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344766"
---
# <a name="monitor-and-troubleshoot-continuous-access-evaluation"></a>Résoudre les problèmes rencontrés avec la fonctionnalité Évaluation continue de l’accès

Les administrateurs peuvent surveiller et dépanner les événements de connexion où l’[évaluation continue de l’accès (CAE)](concept-continuous-access-evaluation.md) est appliquée de plusieurs manières.

## <a name="continuous-access-evaluation-sign-in-reporting"></a>Rapports de l’évaluation continue de l’accès relatifs aux connexions

Les administrateurs ont la possibilité de surveiller les connexions des utilisateurs lorsque l’évaluation continue de l’accès est appliquée. Ce volet peut être localisé en suivant les instructions suivantes :

1.  Connectez-vous au **portail Azure** en tant qu’administrateur de l’accès conditionnel, administrateur de la sécurité ou administrateur général.
1.  Accédez à **Azure Active Directory** > **Connexions**. 
1.  Appliquez le filtre **Est un jeton CAE**. 

[![Ajouter un filtre au journal Sitn-ins pour voir où l’évaluation continue de l’accès est appliquée ou non](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png)](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png#lightbox)

À partir de là, les administrateurs obtiennent des informations sur les événements de connexion de leurs utilisateurs. Sélectionnez une connexion pour voir les détails de la session, comme les stratégies d’accès conditionnel qui ont été appliquées et si l’évaluation continue de l’accès est activée. 

Il existe plusieurs requêtes de connexion pour chaque authentification. Certaines s’affichent sous l’onglet interactif, tandis que d’autres s’affichent sous l’onglet non interactif. L’évaluation continue de l’accès s’affichera uniquement avec la valeur true pour l’une des requêtes, et cela peut se produire sur l’onglet interactif ou non interactif. Les administrateurs doivent consulter les deux onglets pour vérifier si l’évaluation continue de l’accès de l’utilisateur est activée. 

### <a name="searching-for-specific-sign-in-attempts"></a>Recherche de tentatives de connexion spécifiques

Utilisez des filtres pour affiner votre recherche. Par exemple, si un utilisateur s’est connecté à Teams, utilisez le filtre Application et définissez-le sur Teams. Les administrateurs devront peut-être vérifier les connexions à partir des onglets interactif et non interactif pour localiser la connexion spécifique. Pour affiner la recherche, les administrateurs peuvent appliquer plusieurs filtres.

## <a name="continuous-access-evaluation-workbooks"></a>Classeurs de l’évaluation continue de l’accès

Le classeur des insights de l’évaluation continue de l’accès permet aux administrateurs de visualiser et de surveiller les insights de l’évaluation continue de l’accès relatifs à l’utilisation de leurs locataires. Le tableau affiche les tentatives d’authentification avec des incompatibilités d’adresses IP. Ce classeur est accessible en tant que modèle sous la catégorie Accès conditionnel. 

### <a name="accessing-the-cae-workbook-template"></a>Accès au modèle de classeur de l’évaluation continue de l’accès

L’intégration de Log Analytics doit être terminée avant l’affichage des classeurs. Pour plus d’informations sur la diffusion en continu des journaux de connexion Azure AD vers un espace de travail Log Analytics, consultez l’article [Intégrer des journaux Azure AD avec aux journaux d’activité Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).
 
1.  Connectez-vous au **portail Azure** en tant qu’administrateur de l’accès conditionnel, administrateur de la sécurité ou administrateur général. 
1.  Accédez à **Azure Active Directory** > **Classeurs**.
1.  Sous **Modèles publics**, recherchez **Insights de l’évaluation continue de l’accès**.

[![Rechercher le classeur des insights de l’évaluation continue de l’accès dans la galerie pour poursuivre la surveillance](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png)](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png#lightbox)

Le classeur **Insights de l’évaluation continue de l’accès** contient le tableaux suivant :

### <a name="potential-ip-address-mismatch-between-azure-ad-and-resource-provider"></a>Incompatibilité potentielle d’adresse IP entre Azure AD et le fournisseur de ressources  

![Tableau 1 du classeur montrant les incompatibilités potentielles d’adresses IP](./media/howto-continuous-access-evaluation-troubleshoot/continuous-access-evaluation-insights-workbook-table-1.png)

Le tableau Incompatibilité potentielle d’adresse IP entre Azure AD et le fournisseur de ressources permet aux administrateurs d’examiner les sessions où l’adresse IP détectée par Azure AD ne correspond pas à l’adresse IP détectée par le fournisseur de ressources. 

Ce tableau du classeur fait la lumière sur ces scénarios en affichant les adresses IP respectives et en indiquant si un jeton CAE a été émis pendant la session. 

#### <a name="ip-address-configuration"></a>Configuration de l'adresse IP

Votre fournisseur d’identité et vos fournisseurs de ressources peuvent voir des adresses IP différentes. Cette incompatibilité peut se produire dans les cas suivants :

- Votre réseau implémente le tunneling fractionné.
- Votre fournisseur de ressources utilise une adresse IPv6 et Azure AD utilise une adresse IPv4.
- En raison des configurations réseau, Azure AD voit une adresse IP du client et votre fournisseur de ressources en voit une autre.

Si ce scénario existe dans votre environnement, afin d’éviter les boucles infinies, Azure AD émettra un jeton CAE d’une heure et n’appliquera pas le changement d’emplacement de client pendant cette période d’une heure. Même dans ce cas, la sécurité est améliorée par rapport aux jetons traditionnels d’une heure, car nous continuons d’évaluer les autres événements en plus des événements de changement d’emplacement de client.

Les administrateurs peuvent afficher les enregistrements filtrés par plage horaire et par application. Les administrateurs peuvent comparer le nombre d’adresses IP incompatibles détectées avec le nombre total de connexions au cours d’une période donnée. 

Pour débloquer des utilisateurs, les administrateurs peuvent ajouter des adresses IP spécifiques à un emplacement nommé approuvé.

1.  Connectez-vous au **portail Azure** en tant qu’administrateur de l’accès conditionnel, administrateur de la sécurité ou administrateur général. 
1.  Accédez à **Azure Active Directory** > **Sécurité** > **Accès conditionnel** > **Emplacements nommés**. Ici, vous pouvez créer ou mettre à jour des emplacements IP approuvés.

> [!NOTE]
> Avant d’ajouter une adresse IP en tant qu’emplacement nommé approuvé, vérifiez que l’adresse IP appartient bien à l’organisation visée.

Pour plus d’informations sur les emplacements nommés, consultez l’article [Utilisation de la condition d’emplacement](location-condition.md#named-locations).
 
## <a name="next-steps"></a>Étapes suivantes

- [Intégrer les journaux Azure AD avec les journaux Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
- [Utilisation de la condition d’emplacement](location-condition.md#named-locations)
- [Évaluation continue de l’accès](concept-continuous-access-evaluation.md)
