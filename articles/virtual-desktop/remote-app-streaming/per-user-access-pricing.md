---
title: Azure Virtual Desktop - S’inscrire au tarif de l’accès par utilisateur - Azure
description: Guide pratique pour s’inscrire au tarif de l’accès par utilisateur pour Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 67e16d2cf0a9ddbc7b4447b22207c908be4a3ac9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036653"
---
# <a name="enroll-your-subscription-in-per-user-access-pricing"></a>Inscrire votre abonnement à la tarification d’accès par utilisateur

Pour que des utilisateurs externes puissent se connecter à votre déploiement, vous devez inscrire votre abonnement au tarif de l’accès par utilisateur. La tarification de l’accès par utilisateur permet aux utilisateurs extérieurs à votre organisation d’accéder aux applications et aux ordinateurs de bureau de votre abonnement à l’aide des identités que vous fournissez et gérez. Votre abonnement inscrit sera facturé chaque mois en fonction du nombre d’utilisateurs distincts qui se connectent aux ressources Azure Virtual Desktop.

>[!NOTE]
>Veillez à ne pas confondre *les utilisateurs* externes avec des *identités* externes. Azure Virtual Desktop ne prend actuellement pas en charge les identités externes, notamment les comptes invités ou les identités B2B (Business-to-Business). Que vous utilisiez des utilisateurs internes ou des utilisateurs externes avec Azure Virtual Desktop, vous devez créer et gérer des identités pour ces utilisateurs. La tarification par accès utilisateur n’est pas un moyen d’activer les comptes d’utilisateurs invités avec Azure Virtual Desktop. Pour plus d’informations, consultez [Compréhension de la tarification des licences et de l’accès par utilisateur](licensing.md).

## <a name="how-to-enroll"></a>Procédure d'inscription

Pour inscrire votre abonnement Azure au tarif de l’accès par utilisateur :

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).

2. Entrez **Azure Virtual Desktop** dans la barre de recherche, puis recherchez et sélectionnez **Azure Virtual Desktop** sous Services.

3. Dans la page de vue d’ensemble d’**Azure Virtual Desktop**, sélectionnez **Tarif de l’accès par utilisateur**.

4. Dans la liste des abonnements, sélectionnez l’abonnement dans lequel vous allez déployer des ressources Azure Virtual Desktop.

5. Sélectionnez **Inscrire**.

6. Passez en revue les conditions d’utilisation du produit, puis sélectionnez **Inscrire** pour commencer l’inscription. L’achèvement du processus d’inscription peut prendre jusqu’à une heure.

7. Une fois l’inscription terminée, vérifiez que la valeur affichée dans la colonne **Tarif de l’accès par utilisateur** de la liste d’abonnements est passée de « Inscription » à « Inscrit ».

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le tarif de l’accès par utilisateur, consultez [Compréhension du tarif des licences et de l’accès par utilisateur](licensing.md). Si vous voulez savoir comment estimer les coûts du streaming d’applications par utilisateur pour votre déploiement, consultez [Estimer les coûts du streaming d’applications par utilisateur pour Azure Virtual Desktop](streaming-costs.md). Pour estimer les coûts de déploiement totaux, consultez [Estimer le coût total d’un déploiement Azure Virtual Desktop](total-costs.md).
