---
title: Gérer le coût et la propriété
description: Cet article vous aidera à optimiser les coûts et à aligner l’appartenance au sein de votre environnement.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: a62ba95203ffea3162f7e79360bd36eb0f8f9733
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397600"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Gouvernance de l’infrastructure Azure DevTest Labs – Gérer le coût et l’appartenance
Le coût et l’appartenance sont les principaux éléments à prendre en compte lors de la création de vos environnements de développement et de test. Dans cette section, vous trouvez des informations qui vous aideront à optimiser les coûts et à aligner l’appartenance au sein de votre environnement.

## <a name="optimize-for-cost"></a>Optimiser le coût

### <a name="question"></a>Question
Comment puis-je optimiser le coût au sein de mon environnement DevTest Labs ?

### <a name="answer"></a>Réponse
Plusieurs fonctionnalités intégrées de DevTest Labs vous aident à optimiser les coûts. Consultez les articles sur [la gestion du coût, les seuils](devtest-lab-configure-cost-management.md) [et les stratégies](devtest-lab-set-lab-policy.md) afin de limiter des activités des utilisateurs. 

Si vous utilisez DevTest Labs pour des charges de travail de développement et de test, songez à tirer parti de l’[avantage de l’abonnement Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) inclus dans votre Contrat Entreprise. Ou bien, si vous payez à l’utilisation, vous pouvez également considérer l’[offre DevTest assortie d’un paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0023p/).

Cette approche offre plusieurs avantages :

- Tarifs Dev/Test inférieurs préférentiels sur les machines virtuelles, les services cloud, HDInsight, App Service et Logic Apps sous Windows
- Tarifs de contrat Entreprise (EA) avantageux sur d’autres services Azure
- Accès aux images Dev/Test exclusives de la galerie, par exemple Windows 8.1 et Windows 10
 
Seuls les abonnés Visual Studio actifs (abonnements standard, abonnements cloud annuels et mensuels) peuvent utiliser les ressources Azure exécutées dans le cadre d’un abonnement Enterprise Dev/Test. Toutefois, les utilisateurs finaux peuvent accéder à l’application pour formuler des commentaires ou effectuer des tests d’acceptation. Vous pouvez utiliser les ressources de cet abonnement uniquement à des fins de développement et de test d’applications. Il n’existe aucune garantie quant à la durée de bon fonctionnement.

Si vous décidez d’utiliser l’offre DevTest, n’utilisez cet avantage qu’à des fins de développement et de test de vos applications. L’utilisation dans le cadre de l’abonnement n’est pas associée à un contrat de niveau de service avec compensations financières, sauf au sein d’Azure DevOps et de HockeyApp.

## <a name="define-role-based-access-across-your-organization"></a>Définir un accès en fonction du rôle au sein de votre organisation
### <a name="question"></a>Question
Comment définir le contrôle d’accès en fonction du rôle Azure pour les environnements DevTest Labs afin de garantir la gouvernance du service informatique pendant le travail des développeurs/testeurs ? 

### <a name="answer"></a>Réponse
Il existe un modèle large, mais le détail dépend de votre organisation.

L’équipe informatique centrale doit posséder uniquement ce qui est nécessaire, et permettre aux équipes de projet et d’application de disposer du niveau de contrôle dont elles ont besoin. En règle générale, cela signifie que le centre informatique possède l’abonnement et gère les fonctions informatiques clés telles que les configurations de mise en réseau. L’ensemble de **propriétaires** pour un abonnement doit être réduit. Ces propriétaires peuvent désigner d’autres propriétaires si nécessaire, ou appliquer des stratégies de niveau abonnement, par exemple « Aucune adresse IP publique ».

Un sous-ensemble d’utilisateurs peut nécessiter un accès à un abonnement, comme le support de niveau 1 ou 2. Dans ce cas, nous vous recommandons d’accorder à ces utilisateurs l’accès **contributeur** afin qu’ils puissent gérer les ressources, mais pas attribuer des accès aux utilisateurs ou ajuster les stratégies.

Les propriétaires de ressources DevTest Labs doivent être proches de l’équipe du projet ou de l’application. Ils comprennent les exigences liées à la machine et aux logiciels. Dans la plupart des organisations, le propriétaire de cette ressource DevTest Labs dirige également le projet ou le développement. Ce propriétaire peut gérer les utilisateurs et les stratégies dans l’environnement de laboratoire, ainsi que toutes les machines virtuelles dans l’environnement DevTest Labs.

Ajoutez des membres de l’équipe du projet et de l’application au rôle Utilisateurs de DevTest Labs. Ces utilisateurs peuvent créer des machines virtuelles conformes aux stratégies du laboratoire et de l’abonnement. Les utilisateurs peuvent également gérer leurs propres machines virtuelles, mais pas des machines virtuelles appartenant à d’autres utilisateurs.

Pour plus d’informations, consultez [Structure d’entreprise Azure : gouvernance normative de l’abonnement](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


## <a name="next-steps"></a>Étapes suivantes
Voir [Politique et conformité d’entreprise](devtest-lab-guidance-governance-policy-compliance.md).
