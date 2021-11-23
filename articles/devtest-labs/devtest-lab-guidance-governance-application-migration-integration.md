---
title: Intégration et migration d’applications
description: Cet article fournit des conseils de gouvernance pour l’infrastructure Azure DevTest Labs. Le contexte est la migration et l’intégration d’applications.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 0c80db4ef4d9fa6b3b58d84d663a84b89f93219f
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397620"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Gouvernance de l’infrastructure d’Azure DevTest Labs - Migration et intégration d’applications
Une vois votre environnement de développement/test en place, posez-vous les questions suivantes :

- Comment utilisez-vous l’environnement dans votre équipe de projet ?
- Que faites-vous pour respecter les stratégies organisationnelles requises et préserver l’agilité pour valoriser votre application ?

## <a name="azure-marketplace-images-vs-custom-images"></a>Images Place de marché Microsoft Azure ou images personnalisées

### <a name="question"></a>Question
Quand dois-je utiliser une image Place de marché Microsoft Azure ou une image organisationnelle personnalisée ?

### <a name="answer"></a>Réponse
Par défaut, utilisez une image Place de marché Azure, sauf si vous avez des impératifs particuliers ou des exigences organisationnelles. Voici quelques exemples communs :

- Configuration logicielle complexe qui requiert l’intégration d’une application dans l’image de base.
- L’installation et la configuration d’une application peut prendre plusieurs heures, ce qui retarde d’autant plus le temps d’ajout d’une image Place de marché Microsoft Azure.
- Les développeurs et les testeurs ont besoin d’accéder rapidement à une machine virtuelle et souhaitent minimiser la durée de configuration d’une nouvelle machine virtuelle.
- Conformité ou conditions réglementaires (par exemple, stratégies de sécurité) qui doivent être en place pour toutes les machines.

Songez à utiliser les images personnalisées avec précaution. Elles ajoutent de la complexité, car vous devez maintenant gérer les fichiers VHD de ces images de base sous-jacentes. Vous devez également appliquer des mises à jour logicielles à ces images de base. Celles-ci incluent les nouvelles mises à jour du système d’exploitation ainsi que toutes les mises à jour ou modifications de configuration nécessaires au package logiciel lui-même.

## <a name="formula-vs-custom-image"></a>Formule ou image personnalisée

### <a name="question"></a>Question
Quand dois-je utiliser une formule ou une image personnalisée ?

### <a name="answer"></a>Réponse
En général, les facteurs décisifs dans ce scénario sont le coût et la réutilisation.

Vous pouvez réduire le coût en créant une image personnalisée dans les cas suivants :
- De nombreux utilisateurs ou laboratoires requièrent l’image.
- L’image requise présente un grand nombre de logiciels par-dessus l’image de base.

Cette solution signifie que vous créez l’image une seule fois. Une image personnalisée réduit le temps de configuration de la machine virtuelle. Vous n’avez pas à exposer de coûts pour l’exécution de la machine virtuelle pendant la configuration.

Un autre facteur est la fréquence des modifications apportées à votre package logiciel. Si vous générez des builds quotidiennes et s’il faut que les logiciels résident sur les machines virtuelles de vos utilisateurs, envisagez d’utiliser une formule au lieu d’une image personnalisée.

## <a name="use-custom-organizational-images"></a>Utiliser des images organisationnelles personnalisées

### <a name="question"></a>Question
Comment puis-je créer un processus facilement répétable pour intégrer mes images organisationnelles personnalisées dans un environnement DevTest Labs ?

### <a name="answer"></a>Réponse
Regardez [cette vidéo sur le modèle Image Factory](./devtest-lab-faq.yml#blog-post). Ce scénario est avancé et les scripts fournis le sont à titre d’exemple. Si des modifications s’imposent, vous devez gérer et actualiser les scripts utilisés dans votre environnement.

Utilisation d’Azure DevTest Labs pour créer un pipeline d’images personnalisées dans Azure Pipelines :

- [Introduction: Get VMs ready in minutes by setting up an image factory in Azure DevTest Labs (Introduction : Rendre les machines virtuelles opérationnelles en quelques minutes en configuration une fabrique d’images dans Azure DevTest Labs)](./devtest-lab-faq.yml#blog-post)
- [Image Factory – Part 2! Setup Azure Pipelines and Factory Lab to Create VMs (Fabrique d’images – Partie 2 : Configurer VSTS et Factory Lab pour créer des machines virtuelles)](./devtest-lab-faq.yml#blog-post)
- [Image Factory – Part 3: Save Custom Images and Distribute to Multiple Labs (Fabrique d’images – Partie 3 : Enregistrer les images personnalisées et les distribuer à plusieurs laboratoires)](./devtest-lab-faq.yml#blog-post)
- [Video: Custom Image Factory with Azure DevTest Labs (Vidéo : Fabrique d’images personnalisées avec Azure DevTest Labs)](./devtest-lab-faq.yml#blog-post)

## <a name="patterns-to-set-up-network-configuration"></a>Modèles de configuration du réseau

### <a name="question"></a>Question
Comment puis-je m’assurer que les machines virtuelles de développement et de test sont incapables d’accéder à l’Internet public ? Y a-t-il des recommandations à suivre pour configurer le réseau ?

### <a name="answer"></a>Réponse
Oui. Deux aspects sont à prendre en compte : le trafic entrant et le trafic sortant.

**Trafic entrant** : si la machine virtuelle n’a pas d’adresse IP publique, elle n’est pas accessible via Internet. Une approche courante consiste à définir une stratégie au niveau abonnement pour qu’aucun utilisateur ne puisse créer une adresse IP publique.

**Trafic sortant** : si vous voulez éviter que des machines virtuelles accèdent directement à l’Internet public, et forcer le trafic à passer par un pare-feu d’entreprise, vous pouvez acheminer le trafic localement via Azure ExpressRoute ou un VPN, en utilisant un routage forcé.

> [!NOTE]
> Si vous avez un serveur proxy qui bloque le trafic sans paramètres proxy, n’oubliez pas d’ajouter des exceptions dans le compte de stockage d’artefacts du labo.

Vous pouvez également utiliser des groupes de sécurité réseau pour les machines virtuelles ou les sous-réseaux. Cette étape ajoute une autre couche de protection pour autoriser ou bloquer le trafic.

## <a name="new-vs-existing-virtual-network"></a>Réseau virtuel nouveau ou existant

### <a name="question"></a>Question
Quand dois-je créer un réseau virtuel pour mon environnement DevTest Labs et quand dois-je utiliser un réseau virtuel existant ?

### <a name="answer"></a>Réponse
Si vos machines virtuelles doivent interagir avec l’infrastructure existante, envisagez d’utiliser un réseau virtuel existant dans votre environnement DevTest Labs. Si vous utilisez ExpressRoute, réduisez au minimum le nombre de réseaux virtuels et de sous-réseaux afin de ne pas fragmenter l’espace d’adressage IP affecté à vos abonnements. Songez également à utiliser l’appairage de réseaux virtuels ici (modèle Hub-Spoke). Cette approche permet la communication via réseau virtuel et sous-réseau entre abonnements au sein d’une région donnée.

Chaque environnement DevTest Labs pourrait avoir son propre réseau virtuel, mais il existe des [limites](../azure-resource-manager/management/azure-subscription-service-limits.md) au nombre de réseaux virtuels par abonnement. Le nombre par défaut est de 50, mais cette limite peut être portée à 100.

## <a name="shared-public-or-private-ip"></a>IP partagée, publique ou privée

### <a name="question"></a>Question
Quand dois-je utiliser une adresse IP partagée, une adresse IP publique ou une adresse IP privée ?

### <a name="answer"></a>Réponse
Si vous utilisez un VPN de site à site ou Express Route, privilégiez les adresses IP privées pour que vos machines soient accessibles via votre réseau interne et inaccessibles par l’Internet publiques.

> [!NOTE]
> Les propriétaires de labo peuvent modifier cette stratégie de sous-réseau pour que personne ne crée accidentellement d’adresses IP publiques pour ses machines virtuelles. Le propriétaire de l’abonnement doit créer une stratégie d’abonnement empêchant la création d’adresses IP publiques.

Si vous utilisez des adresses IP publiques partagées, les machines virtuelles d’un labo partagent une adresse IP publique. Cette approche peut être utile pour éviter de dépasser les nombres limites d’adresses IP publiques pour un abonnement donné.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Limites du nombre de machines virtuelles par utilisateur ou labo

### <a name="question"></a>Question
Existe-t-il une règle régissant le nombre de machines virtuelles à définir par utilisateur ou par labo ?

### <a name="answer"></a>Réponse
Pour évaluer le nombre de machines virtuelles par utilisateur ou par labo, trois facteurs principaux sont à prendre en compte :

- Le **coût total** que l’équipe peut consacrer aux ressources du labo. Avec de nombreuses machines, il est facile de mettre en place une rotation. Pour maîtriser les coûts, il est possible de limiter le nombre de machines virtuelles par utilisateur ou par labo
- Le nombre totale de machines virtuelles dans un labo est fonction des [quotas d’abonnement](../azure-resource-manager/management/azure-subscription-service-limits.md) disponibles. L’une des limites supérieures est de 800 groupes de ressources par abonnement. Actuellement, DevTest Labs crée un groupe de ressources pour chaque machine virtuelle (sauf si des adresses IP publiques partagées sont utilisées). Si un abonnement compte 10 labos, ceux-ci peuvent accueillir chacun environ 79 machines virtuelles (limite supérieure de 800 – 10 groupes de ressources pour les 10 labos) = 79 machines virtuelles par labo.
- Si le labo est connecté localement par Express Route (par exemple), des **espaces d’adressage IP sont définis** pour le réseau virtuel/sous-réseau. Pour vérifier que les machines virtuelles du labo sont effectivement créés (erreur : impossible d’obtenir l’adresse IP), les propriétaires de labo peuvent spécifier un nombre maximum de machines virtuelles par labo correspondant à l’espace d’adressage IP disponible.

## <a name="use-resource-manager-templates"></a>Utiliser des modèles Resource Manager

### <a name="question"></a>Question
Comment puis-utiliser des modèles Resource Manager dans mon environnement DevTest Labs ?

### <a name="answer"></a>Réponse
Déployez vos modèles Resource Manager en suivant les étapes décrites dans [Utiliser Azure DevTest Labs pour des environnements de test](devtest-lab-test-env.md). Fondamentalement, vous intégrez vos modèles Resource Manager dans un référentiel Azure Repos ou un dépôt Git GitHub, puis ajoutez un [référentiel privé pour vos modèles](devtest-lab-test-env.md) au labo.

Ce scénario peut ne pas être utile si vous utilisez DevTest Labs pour héberger des machines de développement. Utilisez-le pour créer un environnement intermédiaire représentatif de la production.

L’option du nombre de machines virtuelles par laboratoire ou par utilisateur ne limite que le nombre de machines créées en mode natif dans le laboratoire proprement dit. Elle ne limite pas leur création par des environnements quelconques avec des modèles Resource Manager.

## <a name="next-steps"></a>Étapes suivantes
Consultez [Utiliser Azure DevTest Labs pour les environnements de test de machine virtuelle et PaaS](devtest-lab-test-env.md).