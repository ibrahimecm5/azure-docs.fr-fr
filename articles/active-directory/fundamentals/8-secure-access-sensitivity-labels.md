---
title: Contrôlez l’accès externe aux ressources dans Azure Active Directory avec des étiquettes de confidentialité.
description: Utilisez des étiquettes de confidentialité dans le cadre de votre plan de sécurité global pour l’accès externe.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cbdd8f5e69fb21c59be5017a3c28e1067062968
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292602"
---
# <a name="control-access-with-sensitivity-labels"></a>Contrôler l’accès avec des étiquettes de confidentialité 

Les [étiquettes de confidentialité](/microsoft-365/compliance/sensitivity-labels) vous aident à contrôler l’accès à votre contenu dans les applications Office 365 et dans des conteneurs tels que Microsoft Teams, les groupes Microsoft 365 et les sites SharePoint. Elles peuvent protéger votre contenu sans nuire aux capacités de production et de collaboration de vos utilisateurs. Les étiquettes de confidentialité vous permettent d’envoyer le contenu de votre organisation entre des appareils, des applications et des services, tout en protégeant vos données et en respectant vos stratégies de conformité et de sécurité. 

Les étiquettes de confidentialité vous permettent d’effectuer les tâches suivantes :

* **Classifier le contenu sans ajouter de paramètres de protection**. Vous pouvez affecter une classification à du contenu (comme un autocollant) qui reste et suit votre contenu quand il est utilisé et partagé. Vous pouvez utiliser cette classification pour générer des rapports d’utilisation et voir les données d’activité de votre contenu sensible.

* **Appliquer des paramètres de protection, tels qu’un chiffrement, des filigranes ou des restrictions d’accès**. Par exemple, les utilisateurs peuvent appliquer une étiquette Confidentiel à un document ou à un e-mail, et cette étiquette peut [chiffrer le contenu](/microsoft-365/compliance/encryption-sensitivity-labels) et ajouter un filigrane « Confidentiel ». De plus, vous pouvez [appliquer une étiquette de confidentialité à un conteneur](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites), tel qu’un site SharePoint, et indiquer si les utilisateurs externes peuvent accéder au contenu qu’il contient.

Des étiquettes de confidentialité sur un e-mail et d’autres contenus accompagnent ce contenu. Une étiquette de confidentialité sur un conteneur peut limiter l’accès à ce conteneur, mais le contenu du conteneur n’hérite pas de l’étiquette. Par exemple, un utilisateur peut prendre du contenu d’un site protégé, le télécharger, puis le partager sans restrictions, sauf si le contenu contient également une étiquette de confidentialité.

 >[!NOTE]
>Pour appliquer des étiquettes de confidentialité, les utilisateurs doivent être connectés à leur compte professionnel ou scolaire Microsoft.

## <a name="permissions-necessary-to-create-and-manage-sensitivity-levels"></a>Autorisations nécessaires pour créer et gérer des niveaux de confidentialité

Les membres de votre équipe de conformité qui créeront des étiquettes de confidentialité ont besoin d’autorisations relatives au Centre de conformité Microsoft 365, au portail Microsoft 365 Defender et au Centre de sécurité et de conformité Office 365.

Par défaut, les administrateurs généraux de votre locataire ont accès à ces centres d’administration et peuvent fournir l’accès aux responsables de la conformité et à d’autres personnes, sans leur donner toutes les autorisations d’un administrateur de locataire. Pour cet accès administrateur limité délégué, ajoutez des utilisateurs au groupe de rôles Administrateur des données de conformité, Administrateur de conformité et Administrateur de la sécurité.

## <a name="determine-your-sensitivity-label-strategy"></a>Déterminer votre stratégie d’étiquettes de confidentialité

Lorsque vous réfléchissez à la gouvernance de l’accès externe à votre contenu, déterminez les éléments suivants :

**Pour l’ensemble du contenu et des conteneurs**

* Comment allez-vous définir à quoi correspondent un impact élevé (HBI), un impact moyen (MBI) et un impact faible (LBI) sur l’entreprise ? Tenez compte de l’impact sur votre organisation si des types spécifiques de contenu sont partagés de façon inappropriée.

   * Contenu avec des types spécifiques de [contenu sensible](/microsoft-365/compliance/apply-sensitivity-label-automatically) par nature, tels que des numéros de cartes de crédit ou de passeports

   * Contenu créé par des groupes ou des personnes spécifiques (par exemple, des responsables de la mise en conformité, des responsables financiers ou des cadres)

   * Contenu figurant dans des bibliothèques ou des sites spécifiques. Par exemple, des conteneurs hébergeant la stratégie organisationnelle ou des données financières privées

   * Autres critères

* Quelles catégories de contenu (par exemple, le contenu HBI) doivent être interdites d’accès aux utilisateurs externes ?

   * Les restrictions peuvent inclure des actions telles que la restriction de l’accès aux conteneurs et le chiffrement du contenu.

* Quels paramètres par défaut doivent être en place pour les données HBI, les sites et les groupes Microsoft 365 ?

* Où allez-vous utiliser des étiquettes de confidentialité pour [étiqueter et surveiller](/microsoft-365/compliance/sensitivity-labels), au lieu d’[appliquer un chiffrement](/microsoft-365/compliance/encryption-sensitivity-labels) ou d’[appliquer des restrictions d’accès au conteneur](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites) ?

**Pour un e-mail et du contenu**

* Voulez-vous [appliquer automatiquement des étiquettes de confidentialité](/microsoft-365/compliance/apply-sensitivity-label-automatically) au contenu ou le faire manuellement ?

   * Si vous choisissez de le faire manuellement, voulez-vous [recommander aux utilisateurs d’appliquer une étiquette](/microsoft-365/compliance/apply-sensitivity-label-automatically) ?

**Pour les conteneurs**

* Quels sont les critères qui déterminent si des groupes M365, Teams ou des sites SharePoint nécessitent un accès restreint au moyen d’étiquettes de confidentialité ?

* Voulez-vous uniquement étiqueter le contenu de ces conteneurs ou voulez-vous [étiqueter automatiquement](/microsoft-365/compliance/apply-sensitivity-label-automatically) les fichiers existants dans SharePoint et OneDrive ?

Consultez ces [scénarios courants d’étiquettes de confidentialité](/microsoft-365/compliance/get-started-with-sensitivity-labels) pour d’autres idées sur la façon d’utiliser les étiquettes de confidentialité.

### <a name="sensitivity-labels-on-email-and-content"></a>Étiquettes de confidentialité sur un e-mail et du contenu

Lorsque vous affectez une étiquette de confidentialité à un document ou à un e-mail, elle s’apparente à un tampon appliqué au contenu qui est personnalisable, en texte clair et persistant. 

* **Personnalisable** signifie que vous pouvez créer des étiquettes appropriées pour votre organisation et déterminer ce qui se passe quand elles sont appliquées.

* **Texte clair** signifie qu’il fait partie des métadonnées de l’élément et qu’il est accessible en lecture par les applications et les services pour que ceux-ci puissent appliquer leurs propres actions de protection.

* **Persistant** signifie que l’étiquette et toutes les protections associées accompagnent le contenu et deviennent la base de l’application et de la mise en œuvre des stratégies.

 

> [!NOTE]
> Une étiquette de confidentialité unique peut être appliquée à chaque élément de contenu.


### <a name="sensitivity-labels-on-containers"></a>Étiquettes de confidentialité sur les conteneurs

Vous pouvez appliquer des étiquettes de confidentialité sur des conteneurs tels que les [groupes Microsoft 365](../enterprise-users/groups-assign-sensitivity-labels.md), [Microsoft Teams](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites) et les [sites SharePoint](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites). Lorsque vous appliquez cette étiquette de confidentialité à un conteneur pris en charge, l’étiquette applique automatiquement les paramètres de classification et de protection au site ou groupe connecté. Les étiquettes de confidentialité sur ces conteneurs peuvent contrôler les aspects suivants des conteneurs :

* **Confidentialité**. Vous pouvez choisir qui peut voir le site : des utilisateurs spécifiques, tous les utilisateurs internes ou tout le monde.

* **Accès des utilisateurs externes**. Contrôle si le propriétaire du groupe peut ajouter des invités dans le groupe.

* **Accès à partir d’appareils non managés**. Détermine si et comment les appareils non managés peuvent accéder au contenu.

 

![Capture d’écran de la modification des étiquettes de confidentialité](media/secure-external-access/8-edit-label.png)

 

Lorsque vous appliquez une étiquette de confidentialité à un conteneur tel qu’un site SharePoint, elle n’est pas appliquée à son contenu : les étiquettes de confidentialité sur les conteneurs contrôlent l’accès au contenu dans le conteneur. 

* Si vous souhaitez appliquer automatiquement des étiquettes au contenu dans le conteneur, consultez [Appliquer automatiquement une étiquette de confidentialité au contenu](/microsoft-365/compliance/apply-sensitivity-label-automatically).

* Si vous souhaitez que les utilisateurs puissent appliquer manuellement des étiquettes à ce contenu, veillez à [activer les étiquettes de confidentialité pour les fichiers Office dans SharePoint et OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files).

### <a name="plan-to-implement-sensitivity-labels"></a>Planifier l’implémentation des étiquettes de confidentialité

Une fois que vous avez déterminé la manière dont vous souhaitez utiliser les étiquettes de confidentialité, ainsi que le contenu et les sites auxquels vous souhaitez les appliquer, consultez la documentation suivante qui vous aidera à effectuer votre implémentation.

1. [Bien démarrer avec les étiquettes de confidentialité](/microsoft-365/compliance/get-started-with-sensitivity-labels)

2. [Créer une stratégie de déploiement](/microsoft-365/compliance/get-started-with-sensitivity-labels)

3. [Créer et publier des étiquettes de confidentialité](/microsoft-365/compliance/create-sensitivity-labels)

4. [Restreindre l’accès à du contenu à l’aide d’étiquettes de confidentialité pour appliquer un chiffrement](/microsoft-365/compliance/encryption-sensitivity-labels)

5. [Utiliser des étiquettes de confidentialité avec des équipes, des groupes et des sites](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)

6. [Activer les étiquettes de confidentialité pour les fichiers Office dans SharePoint et OneDrive](/microsoft-365/compliance/sensitivity-labels-sharepoint-onedrive-files)

### <a name="next-steps"></a>Étapes suivantes

Consultez les articles suivants sur la sécurisation de l’accès externe aux ressources. Nous vous recommandons d’effectuer les actions dans l’ordre indiqué.

1. [Déterminer votre posture de sécurité souhaitée pour l’accès externe](1-secure-access-posture.md)

2. [Découvrir votre état actuel](2-secure-access-current-state.md)

3. [Créer un plan de gouvernance](3-secure-access-plan.md)

4. [Utiliser des groupes pour la sécurité](4-secure-access-groups.md)

5. [Transition vers Azure AD B2B](5-secure-access-b2b.md)

6. [Sécuriser l’accès avec la gestion des droits d’utilisation](6-secure-access-entitlement-managment.md)

7. [Sécuriser l’accès avec les stratégies d’accès conditionnel](7-secure-access-conditional-access.md)

8. [Sécuriser l’accès avec des étiquettes de confidentialité](8-secure-access-sensitivity-labels.md) (Vous êtes ici.)

9. [Sécuriser l’accès à Microsoft Teams, OneDrive et SharePoint](9-secure-access-teams-sharepoint.md)
