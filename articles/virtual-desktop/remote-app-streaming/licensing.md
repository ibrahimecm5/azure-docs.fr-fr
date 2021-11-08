---
title: Présentation de la tarification par accès utilisateur d’Azure Virtual Desktop pour la diffusion en continu d’applications distantes – Azure
description: Une présentation des licences d’Azure Virtual Desktop pour le streaming d’applications distantes.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 563d49ff66bdcd81a37a0bf1b12e910f16f2f5ff
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036691"
---
# <a name="understanding-licensing-and-per-user-access-pricing"></a>Compréhension de la tarification des licences et de l’accès par utilisateur

Cet article explique les conditions de licence pour l’utilisation du bureau virtuel Azure pour diffuser des applications distantes vers des utilisateurs externes. Dans cet article, vous apprendrez comment la gestion des licences des bureaux virtuels Azure pour les utilisateurs externes est différente de celle des utilisateurs internes, la tarification de l’accès par utilisateur et la licence d’autres produits que vous prévoyez d’utiliser avec le bureau virtuel Azure.

## <a name="internal-users-and-external-users"></a>Utilisateurs internes et externes

Dans le contexte de la fourniture d’une infrastructure virtualisée avec Azure Virtual Desktop, les **utilisateurs internes** font référence aux membres de votre propre organisation, par exemple les employés d’une entreprise ou les élèves d’un établissement scolaire. Les **utilisateurs externes**, eux, ne sont pas membres de votre organisation (par exemple les clients d’une entreprise).

>[!NOTE]
>Veillez à ne pas confondre les *utilisateurs* externes avec les *identités* externes. À l’heure actuelle, Azure Virtual Desktop ne prend pas en charge les identités externes, notamment les comptes Invités et les identités B2B (Business-to-Business). Que vous traitiez des utilisateurs internes ou externes avec Azure Virtual Desktop, vous devez créer et gérer des identités pour ces utilisateurs. La tarification par accès utilisateur ne constitue pas un moyen d’activer les comptes d’utilisateurs Invités avec Azure Virtual Desktop. Pour plus d’informations, consultez [Recommandations architecturales](architecture-recs.md).

Les licences Azure Virtual Desktop fonctionnent différemment pour les utilisateurs internes et externes. Penchez-vous sur les exemples suivants :

- Une société de fabrication du nom de Fabrikam, Inc. peut utiliser Azure Virtual Desktop pour fournir à ses employés (utilisateurs internes) un accès aux stations de travail virtuelles et aux applications métier. Puisqu’elle traite des utilisateurs internes, elle doit acheter l’une des licences éligibles de la liste [Tarification d’Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) pour chaque employé qui devra accéder à Azure Virtual Desktop.

- Un éditeur de logiciels du nom de Contoso peut utiliser Azure Virtual Desktop pour vendre des flux distants de son application de productivité à ses clients (utilisateurs externes). Puisqu’il traite des utilisateurs externes, il doit s’inscrire à la tarification par accès utilisateur d’Azure Virtual Desktop. Cela lui permet de s’acquitter des droits d’accès à Azure Virtual Desktop pour le compte des utilisateurs externes qui se connectent au déploiement de Contoso. Les utilisateurs n’ont pas besoin d’une licence distincte comme Microsoft 365 pour accéder à Azure Virtual Desktop. Contoso doit dans tous les cas créer et gérer des identités pour ces utilisateurs externes.

## <a name="per-user-access-pricing-for-azure-virtual-desktop"></a>Tarification par accès utilisateur pour Azure Virtual Desktop

La tarification de l’accès par utilisateur vous permet de payer les droits d’accès à Azure Virtual Desktop pour le compte d’utilisateurs externes. Vous devez vous inscrire à la tarification par accès utilisateur pour créer un déploiement conforme pour les utilisateurs externes. Pour en savoir plus, consultez [S'inscrire à la tarification de l'accès par utilisateur](per-user-access-pricing.md).

Vous payez pour la tarification par accès utilisateur par le biais de votre abonnement Azure inscrit ou de vos abonnements en plus de vos frais pour les machines virtuelles, le stockage et d’autres services Azure. Chaque cycle de facturation, vous payez uniquement pour les utilisateurs qui ont réellement utilisé le service. Seuls les utilisateurs qui se connectent au moins une fois à Azure Virtual Desktop, ce mois-ci, entraînent des frais d’accès.

Il existe deux niveaux tarifaires pour la tarification de l’accès à Azure Virtual Desktop par utilisateur. Les frais sont déterminés automatiquement à chaque cycle de facturation en fonction du type de [groupes d'applications](../environment-setup.md#app-groups) auxquels un utilisateur est connecté.

- Le premier niveau de prix est appelé « Apps ». Ce prix forfaitaire est facturé pour chaque utilisateur qui accède à au moins un groupe d’applications RemoteApp et à aucun groupe d’applications de bureau.
- Le deuxième niveau est « Apps and Desktops ». Ce prix forfaitaire est facturé pour chaque utilisateur qui accède à au moins un groupe d’applications de bureau.
- Si un utilisateur n’accède à aucun groupe d’applications, il n’y a pas de frais.

Pour plus d’informations sur les tarifs, voir [Tarification d’Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).

Chaque niveau tarifaire a des frais d’accès forfaitaires par utilisateur. Par exemple, un utilisateur assume les mêmes frais pour votre abonnement, quel que soit le nombre d’heures pendant lequel il a utilisé le service pendant ce cycle de facturation.

Azure Virtual Desktop met également à la disposition des utilisateurs avec des licences attribuées distinctes qui leur donnent accès à Azure Virtual Desktop. Si vous avez des utilisateurs internes pour lesquels vous achetez des licences éligibles, nous vous recommandons de leur accorder l’accès à Azure Virtual Desktop à l’aide d’un abonnement distinct qui n’est pas inscrit au tarif de l’accès par utilisateur pour éviter de payer deux fois pour ces utilisateurs.

Azure Virtual Desktop émet au plus un coût d’accès pour un utilisateur donné au cours d’une période de facturation donnée. Par conséquent, si votre déploiement accorde à l’utilisateur Alice l’accès aux ressources d’Azure Virtual Desktop sur deux abonnements Azure différents dans le même locataire, seul le premier abonnement auquel Alice a accès est facturé.

## <a name="comparing-licensing-options"></a>Comparaison des options de licence

Voici un résumé des deux types de licences pour Azure Virtual Desktop que vous pouvez choisir :

- Une licence Windows ou Microsoft 365 éligible :
  - Accorde des droits d’accès à Azure Virtual Desktop pour les utilisateurs internes uniquement
  - Réglée à l’avance sous la forme d’un abonnement
  - Même coût par utilisateur chaque mois, quel que soit le comportement de l’utilisateur
  - Comprend des droits à d’autres produits et services Microsoft

- Tarification de l’accès par utilisateur :
  - Octroie des droits d’accès à Azure Virtual Desktop pour les utilisateurs externes uniquement
  - Paiement à l’aide d’un compteur Azure
  - Coût par utilisateur et par mois variable selon le comportement utilisateur
  - Comprend uniquement des droits d’accès à Azure Virtual Desktop

## <a name="licensing-other-products-and-services-for-use-with-azure-virtual-desktop"></a>Licences d’autres produits et services à utiliser avec Azure Virtual Desktop

La licence d’accès par utilisateur d’Azure Virtual Desktop n’est pas entièrement remplacée par une licence Windows ou Microsoft 365. Les licences par utilisateur accordent uniquement des droits d’accès à Azure Virtual Desktop et n’incluent pas Microsoft Office, Microsoft Defender ou l’impression universelle. Cela signifie que si vous choisissez une licence par utilisateur, vous devez accorder une licence distincte à d’autres produits et services pour permettre à vos utilisateurs d’y accéder dans votre environnement Azure Virtual Desktop.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous êtes familiarisé avec les options de tarification de votre licence, vous pouvez commencer à planifier votre environnement Azure Virtual Desktop. Voici quelques articles qui pourraient vous aider :

- [Inscrire votre abonnement à la tarification d’accès par utilisateur](per-user-access-pricing.md)
- [Estimer les coûts de diffusion d’application par utilisateur pour Azure Virtual Desktop](streaming-costs.md)
- Si vous êtes prêt à commencer la configuration de votre premier déploiement, prenez en main nos [didacticiels](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json).
