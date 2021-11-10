---
title: Guide de sécurité pour les applications inter-organisations Azure Virtual Desktop - Azure
description: Un guide sur la manière de sécuriser les applications que vous hébergez dans Azure Virtual Desktop au sein de plusieurs organisations.
author: Heidilohr
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 3216a8f82bc066cded33f133d9fe8823484d50b0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463142"
---
# <a name="security-guidelines-for-cross-organizational-apps"></a>Instructions de sécurité pour les applications inter-organisations

Lorsque vous hébergez ou diffusez des applications sur Azure Virtual Desktop, vous touchez un large éventail d’utilisateurs à l’intérieur et à l’extérieur de votre organisation. Par conséquent, il est extrêmement important de savoir comment sécuriser votre déploiement afin d’assurer la sécurité de votre organisation et de vos clients. Ce guide vous permettra de comprendre les problèmes de sécurité possibles et la manière de les résoudre.

## <a name="shared-responsibility"></a>Responsabilité partagée

Avant Azure Virtual Desktop, les solutions de virtualisation locales telles que les services Bureau à distance exigeaient d’accorder aux utilisateurs l'accès à des rôles tels que Passerelle, Répartiteur, Accès web, etc. Ces rôles devaient être entièrement redondants et capables de gérer les pics de capacité. Les administrateurs devaient installer ces rôles dans le système d’exploitation Windows Server, et ils devaient être joints à un domaine avec des ports spécifiques accessibles aux connexions publiques. Pour assurer la sécurité des déploiements, les administrateurs devaient constamment s’assurer que tous les éléments de l’infrastructure étaient mis à jour.

Depuis, Azure Virtual Desktop gère des parties des services pour le client. Plus précisément, Microsoft héberge et gère les parties de l’infrastructure dans le cadre du service. Les partenaires et les clients n’ont plus besoin de gérer manuellement l’infrastructure requise pour permettre aux utilisateurs d’accéder aux machines virtuelles de l’hôte de session. Le service intègre également des fonctionnalités de sécurité avancées, telles que la connexion inversée, qui réduisent le risque lié au fait que les bureaux à distance sont accessibles depuis n’importe où.

Pour garantir la flexibilité du service, les hôtes de session sont hébergés dans l’abonnement Azure du partenaire ou du client. Cela permet aux clients d’intégrer le service à d’autres services Azure et de connecter l’infrastructure réseau locale avec ExpressRoute ou un réseau privé virtuel (VPN).

Comme pour de nombreux services cloud, les [responsabilités en matière de sécurité sont partagées](../../security/fundamentals/shared-responsibility.md) entre vous et Microsoft. Lorsque vous utilisez Azure Virtual Desktop, il est important de comprendre que si certaines parties du service sont sécurisées pour vous, il en existe d’autres que vous devrez configurer vous-même en fonction des besoins de sécurité de votre organisation.

Vous devrez configurer la sécurité dans les domaines suivants :

- Appareils pour les utilisateurs
- Sécurité des applications
- Systèmes d’exploitation de l’hôte de session
- Configuration du déploiement
- Contrôles de réseau

Pour plus d’informations sur la configuration de chacun de ces domaines, consultez nos [meilleures pratiques en matière de sécurité](./../security-guide.md).

## <a name="combined-microsoft-security-platform"></a>Plateforme de sécurité Microsoft combinée

Vous pouvez protéger les charges de travail à l’aide des contrôles et des fonctionnalités de sécurité de Microsoft 365, Azure et Azure Virtual Desktop.

Lorsque l’utilisateur se connecte au service via internet, Azure Active Directory (Azure AD) authentifie les informations d’identification de l’utilisateur, en activant des fonctionnalités de protection telles que l’[accès conditionnel](../../active-directory/conditional-access/overview.md) et l’[authentification multifacteur](../../active-directory/authentication/concept-mfa-howitworks.md). Ces fonctionnalités réduisent considérablement le risque de compromission des identités utilisateur.

Azure Virtual Desktop propose des fonctionnalités telles que la [connexion inversée](../network-connectivity.md#reverse-connect-transport) qui permettent aux utilisateurs d’accéder à l’hôte de session sans avoir à ouvrir de ports entrants. Cette fonctionnalité étant conçue dans un souci de scalabilité et de service, elle ne devrait pas limiter votre capacité à étendre les hôtes de session. Vous pouvez également utiliser des GPO existants avec cette fonctionnalité pour encore renforcer la sécurité avec la prise en charge de machines virtuelles jointes à Active Directory ou, pour les hôtes de session Windows 10 qui peuvent impliquer des scénarios de jointure Azure Active Directory, [Microsoft Endpoint Manager](/mem/intune/fundamentals/windows-virtual-desktop-multi-session).

## <a name="defense-in-depth"></a>Défense en profondeur

Le paysage des menaces d’aujourd'hui exige des conceptions tenant compte des approches de sécurité. Dans l’idéal, vous souhaiterez créer une série de contrôles et de mécanismes de sécurité répartis sur l’ensemble de votre réseau informatique afin de protéger vos données et votre réseau contre toute compromission ou attaque. Ce type de conception de la sécurité est ce que l’Agence américaine pour la cybersécurité et la sécurité des infrastructures (CISA) appelle la « défense en profondeur ». Pour en savoir plus sur la défense en profondeur, consultez le [site web de la CISA](https://us-cert.cisa.gov/bsi/articles/knowledge/principles/defense-in-depth).

## <a name="session-host-operating-systems"></a>Systèmes d’exploitation de l’hôte de session

Azure Virtual Desktop prend en charge les systèmes d’exploitation suivants :

- Windows 7
- Windows 10 Entreprise
- Windows Server 2012 R2, 2016, 2019 et 2022
- Windows 10 Entreprise multisession

Windows 10 Entreprise multisession est un système d’exploitation exclusif à Azure qui fonctionne comme un hôte de session Bureau à distance (RDSH) et autorise plusieurs sessions interactives simultanées, à l’instar de Windows Server. L’environnement Windows 10 offre aux utilisateurs une expérience familière et cohérente. Les licences Windows par utilisateur du service réduisent également les coûts pour l’ensemble de l’organisation, car il n’est plus nécessaire d’acheter des licences d’accès client pour les sessions Bureau à distance. En raison de ces avantages, Windows 10 Entreprise multisession est le système d’exploitation le plus largement déployé pour les hôtes de session dans Azure Virtual Desktop.

## <a name="security-boundaries"></a>Limites de sécurité

Les limites de sécurité séparent le code et les données des domaines de sécurité avec différents niveaux de confiance. Par exemple, il existe généralement une limite de sécurité entre le mode noyau et le mode utilisateur. La plupart des logiciels et services Microsoft dépendent de plusieurs limites de sécurité pour isoler les appareils sur les réseaux, les machines virtuelles et les applications sur les appareils. Le tableau suivant répertorie chaque limite de sécurité pour Windows et sa contribution à la sécurité globale.

| Limite de sécurité             | Contribution de la limite      |
|-----------------------------------|--------------------------------------|
| Limite de réseau              | Un point de terminaison réseau non autorisé ne peut pas accéder au code ni aux données sur l’appareil d’un client, ni les falsifier.                                                                   |
| Limite de noyau               | Un processus en mode utilisateur non administratif ne peut pas accéder au code ni aux données du noyau, ni les falsifier. Administrateur-vers-noyau n’est pas une limite de sécurité.                             |
| Limite de processus               | Un processus en mode utilisateur non autorisé ne peut pas accéder au code ni aux données d’un autre processus, ni les falsifier.                                                                      |
| Limite de bac à sable AppContainer  | Un processus de bac à sable basé sur AppContainer ne peut pas accéder au code ni aux données, ni les falsifier, en dehors du bac à sable en fonction des capacités du conteneur.                               |
| Limite d’utilisateur                  | Un utilisateur ne peut pas accéder au code ni aux données d’un autre utilisateur, ni les falsifier, sans y être autorisé.                                                                           |
| Limite de session               | Une session utilisateur ne peut pas accéder au code ni aux données d’une autre session utilisateur, ni les falsifier, sans y être autorisé.                                                                    |
| Limite de navigateur web           | Un site web non autorisé ne peut pas violer la stratégie d’origine identique, ni accéder au code natif ni aux données du bac à sable du navigateur web Microsoft Edge, ni les falsifier.       |
| Limite de machine virtuelle       | Une machine virtuelle invitée Hyper-V non autorisée ne peut pas accéder au code ni aux données d’une autre machine virtuelle invitée, ni les falsifier. Les conteneurs isolés Hyper-V sont également concernés. |
| Limite du mode sécurisé virtuel (VSM)  | Le code qui s’exécute en dehors de l’enclave ou du processus de confiance VSM ne peut pas accéder au code ni aux données du processus de confiance, ni les falsifier.                              |

## <a name="security-features"></a>Fonctionnalités de sécurité

Les fonctionnalités de sécurité s’ajoutent aux limites de sécurité pour renforcer la protection contre des menaces spécifiques. Toutefois, dans certains cas, des limitations de conception peuvent empêcher la fonctionnalité de sécurité de protéger totalement le déploiement.

Azure Virtual Desktop prend en charge la plupart des fonctionnalités de sécurité disponibles dans Windows. Les fonctionnalités de sécurité qui reposent sur des caractéristiques matérielles, telles que (v)TPM ou la virtualisation imbriquée, peuvent nécessiter une déclaration de support distincte de la part de l’équipe Azure Virtual Desktop.

Pour en savoir plus sur la prise en charge et la maintenance des fonctionnalités de sécurité, consultez nos [Critères de maintenance de la sécurité Microsoft pour Windows](https://www.microsoft.com/msrc/windows-security-servicing-criteria).

## <a name="recommended-security-boundaries-for-azure-virtual-desktop-scenarios"></a>Limites de sécurité recommandées pour les scénarios Azure Virtual Desktop

Vous devrez également faire certains choix concernant les limites de sécurité au cas par cas. Par exemple, si un utilisateur de votre organisation a besoin de privilèges d’administrateur local pour installer des applications, vous devrez lui attribuer un bureau personnel plutôt qu’un RDSH partagé. Nous déconseillons de fournir aux utilisateurs des privilèges d’administrateur local dans les scénarios de pool multisessions, car ces utilisateurs peuvent franchir les limites de sécurité pour les sessions ou les autorisations de données NTFS, arrêter des machines virtuelles multisessions ou effectuer d’autres opérations susceptibles d’interrompre le service ou d’entraîner des pertes de données.

Les utilisateurs d’une même organisation, comme les travailleurs du savoir qui utilisent des applications ne nécessitant pas de privilèges d’administrateur, sont d’excellents candidats pour les hôtes de session Bureau à distance multisessions, comme Windows 10 Entreprise multisession. Ces hôtes de session réduisent les coûts pour votre organisation, car plusieurs utilisateurs peuvent partager une seule machine virtuelle, avec uniquement les frais généraux d’un seul système d’exploitation. Avec une technologie de profil comme FSLogix, les utilisateurs peuvent se voir attribuer n’importe quelle machine virtuelle dans un pool d’hôtes sans remarquer les éventuelles interruptions de service. Cette fonctionnalité vous permet également d’optimiser les coûts en arrêtant, par exemple, les machines virtuelles pendant les heures creuses.

Si, dans votre cas, des utilisateurs de différentes organisations doivent se connecter à votre déploiement, nous vous recommandons d’avoir un locataire distinct pour les services d’identité comme Active Directory et Azure AD. Nous vous conseillons également d’avoir un abonnement distinct pour l’hébergement des ressources Azure, comme Azure Virtual Desktop et les machines virtuelles.

Le tableau suivant répertorie nos recommandations pour chaque scénario.

| Scénario de niveau de confiance                                 | Solution recommandée                |
|------------------------------------------------------|-------------------------------------|
| Utilisateurs d’une même organisation avec des privilèges standard | Windows 10 Entreprise multisession |
| Les utilisateurs nécessitent des privilèges d’administrateur             | Postes de travail personnels (VDI)             |
| Utilisateurs de différentes organisations qui se connectent        | Abonnement Azure distinct         |

Examinons nos recommandations pour quelques exemples de scénarios.

### <a name="should-i-share-identity-resources-to-reduce-costs"></a>Dois-je partager des ressources d’identité pour réduire les coûts ?

À l’heure actuelle, nous ne recommandons pas l’utilisation d’un système d’identités partagées dans Azure Virtual Desktop. Nous vous recommandons de disposer de ressources d’identité distinctes que vous déployez dans un abonnement Azure séparé. Ces ressources incluent des instances Active Directory, Azure AD et des charges de travail de machine virtuelle. Chaque utilisateur qui travaille pour une organisation spécifique aura besoin d’une infrastructure supplémentaire avec des coûts de maintenance associés, mais il s’agit actuellement de la solution la plus réalisable pour des raisons de sécurité. 

### <a name="should-i-share-a-multi-session-remote-desktop-rd-session-host-vm-to-reduce-costs"></a>Dois-je partager une machine virtuelle d’hôte de session Bureau à distance multisession pour réduire les coûts ?

Les hôtes de session Bureau à distance multisession permettent de réduire les coûts, grâce au partage de ressources matérielles telles que le processeur et la mémoire entre les utilisateurs. Ce partage des ressources vous permet de concevoir une capacité maximale, même s’il est peu probable que tous les utilisateurs aient besoin de ressources maximales en même temps. Dans un environnement multisession partagé, les ressources matérielles sont partagées et allouées de manière à réduire l’écart entre l’utilisation et les coûts.

Dans de nombreux cas, l’utilisation de plusieurs sessions est un moyen acceptable de réduire les coûts, mais le fait de la recommander ou non dépend du niveau de confiance entre les utilisateurs possédant un accès simultané à une instance multisession partagée. En règle générale, les utilisateurs qui appartiennent à la même organisation ont une relation de confiance suffisante et approuvée. Par exemple, un service ou un groupe de travail où les utilisateurs collaborent et peuvent accéder aux informations personnelles de chacun est une organisation où le niveau de confiance est élevé.

Windows utilise des limites et des contrôles de sécurité pour s’assurer que les processus et les données des utilisateurs sont isolés entre les sessions. Cependant, Windows donne toujours accès à l’instance sur laquelle l’utilisateur travaille.

Ce déploiement pourrait tirer parti d’une stratégie de sécurité en profondeur qui ajoute des limites de sécurité supplémentaires empêchant les utilisateurs internes et externes à l’organisation d’obtenir un accès non autorisé aux informations personnelles d’autres utilisateurs. L’accès non autorisé aux données est lié à une erreur dans le processus de configuration par l’administrateur système, telle qu’une faille de sécurité non divulguée ou une vulnérabilité connue qui n’a pas encore été corrigée.

En revanche, Microsoft ne permet pas d’accorder aux utilisateurs qui travaillent pour des entreprises différentes ou concurrentes l’accès au même environnement multisession. Ces scénarios comportent plusieurs limites de sécurité qui peuvent être attaquées ou utilisées abusivement, telles que le réseau, le noyau, le processus, l’utilisateur ou les sessions. Une seule vulnérabilité de sécurité peut entraîner le vol d’informations d’identification et de données non autorisées, la fuite d’informations personnelles, l’usurpation d’identité et d’autres problèmes. Les fournisseurs d’environnements virtualisés sont tenus de proposer des systèmes bien conçus, dotés de plusieurs limites de sécurité fortes et de fonctionnalités de sécurité supplémentaires si possible activées.

Pour réduire ces menaces potentielles, il faut une configuration sans faille, un processus de conception de la gestion des correctifs et des calendriers réguliers pour le déploiement de ces correctifs. Il est préférable de respecter les principes de défense en profondeur et de séparer les environnements.

## <a name="next-steps"></a>Étapes suivantes

Consultez nos recommandations pour la configuration de la sécurité de votre déploiement Azure Virtual Desktop dans nos [meilleures pratiques en matière de sécurité](./../security-guide.md).