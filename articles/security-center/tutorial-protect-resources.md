---
title: Tutoriel sur les contrôles d’accès et d’application - Microsoft Defender pour le cloud
description: Ce tutoriel montre comment configurer une stratégie d’accès juste-à-temps aux machines virtuelles et une stratégie de contrôle d’applications.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: e8656730d0edf3fe08a217b5acd5c94d886dadea
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475252"
---
# <a name="tutorial-protect-your-resources-with-microsoft-defender-for-cloud"></a>Tutoriel : Protéger vos ressources avec Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender pour le cloud limite votre exposition aux menaces en utilisant des contrôles d’accès et d’application afin de bloquer les activités malveillantes. L’accès juste-à-temps (JIT) aux machines virtuelles réduit votre exposition aux attaques en vous permettant de refuser l’accès persistant aux machines virtuelles. À la place, vous fournissez un accès contrôlé et audité aux machines virtuelles uniquement en cas de besoin. Les contrôles d’applications adaptatifs permettent de renforcer la protection contre les logiciels malveillants en contrôlant les applications qui peuvent s’exécuter sur les machines virtuelles. Defender pour le cloud utilise le machine learning pour analyser les processus en cours d’exécution sur la machine virtuelle et exploite ces informations pour vous aider à appliquer les règles de mise en liste d’autorisation.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer une stratégie d’accès juste-à-temps aux machines virtuelles
> * Configurer une stratégie de contrôle d’applications

## <a name="prerequisites"></a>Prérequis
Pour parcourir les fonctionnalités abordées dans ce tutoriel, vous devez activer les fonctionnalités de sécurité renforcée de Defender pour le cloud. Un essai gratuit est disponible. Pour effectuer la mise à niveau, consultez [Activer les protections renforcées](enable-enhanced-security.md).

## <a name="manage-vm-access"></a>Gérer l’accès aux machines virtuelles
L’accès JIT aux machines virtuelles peut être utilisé pour verrouiller le trafic entrant vers vos machines virtuelles Azure, réduire l’exposition aux attaques et faciliter la connexion aux machines virtuelles si nécessaire.

Les ports de gestion n’ont pas besoin d’être toujours ouverts. Ils doivent uniquement être ouverts lorsque vous êtes connecté à la machine virtuelle, par exemple pour effectuer des tâches de maintenance ou de gestion. Quand l’accès juste-à-temps est activé, Defender pour le cloud utilise des règles de groupe de sécurité réseau (NSG) qui limitent l’accès aux ports de gestion pour qu’ils ne soient pas la cible d’attaquants.

Suivez les instructions fournies dans [Sécuriser vos ports de gestion avec un accès juste-à-temps](just-in-time-access-usage.md).

## <a name="harden-vms-against-malware"></a>Renforcer la protection des machines virtuelles contre les logiciels malveillants
Les contrôles d’applications adaptatifs vous aident à définir un ensemble d’applications autorisées à s’exécuter sur des groupes de ressources configurés, ce qui permet, entre autres, de renforcer la protection de vos machines virtuelles contre les logiciels malveillants. Defender pour le cloud utilise le machine learning pour analyser les processus en cours d’exécution sur la machine virtuelle et exploite ces informations pour vous aider à appliquer les règles de mise en liste d’autorisation.

Suivez les instructions fournies dans [Utiliser des contrôles d’application adaptatifs pour réduire les surfaces d’attaque de vos machines](adaptive-application-controls.md).

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à limiter votre exposition aux menaces en effectuant les tâches suivantes :

> [!div class="checklist"]
> * Configuration d’une stratégie d’accès juste-à-temps aux machines virtuelles pour fournir un accès contrôlé et audité aux machines virtuelles uniquement en cas de besoin
> * Configuration d’une stratégie de contrôles d’applications adaptatifs pour contrôler les applications qui peuvent s’exécuter sur vos machines virtuelles

Passez au didacticiel suivant pour en savoir plus sur la façon de répondre aux incidents de sécurité.

> [!div class="nextstepaction"]
> [Tutoriel : Répondre à des incidents de sécurité](tutorial-security-incident.md)