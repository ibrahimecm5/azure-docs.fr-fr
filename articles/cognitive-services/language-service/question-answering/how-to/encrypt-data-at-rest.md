---
title: Question personnalisée répondant au chiffrement des données au repos
titleSuffix: Azure Cognitive Services
description: Microsoft propose des clés de chiffrement gérées par Microsoft et vous permet également de gérer vos abonnements Cognitive Services à l’aide de vos propres clés, appelées clés gérées par le client (CMK). Cet article traite du chiffrement des données au repos pour la réponse aux questions personnalisées et de l’activation et de la gestion de CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: egeaney
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 8570a1160eddbed5b372daa529339ea3672482cb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097735"
---
# <a name="custom-question-answering-encryption-of-data-at-rest"></a>Question personnalisée répondant au chiffrement des données au repos

Le service de réponses aux questions chiffre automatiquement vos données lorsqu'elles sont conservées dans le cloud, ce qui vous permet de satisfaire aux exigences de votre organisation en matière de sécurité et de conformité.

## <a name="about-encryption-key-management"></a>À propos de la gestion des clés de chiffrement

Par défaut, votre abonnement utilise des clés de chiffrement gérées par Microsoft. Il existe aussi la possibilité de gérer votre abonnement avec vos propres clés appelées clés gérées par le client (CMK). Les CMK offrent plus de flexibilité pour créer, alterner, désactiver et révoquer des contrôles d’accès. Vous pouvez également effectuer un audit sur les clés de chiffrement utilisées pour protéger vos données. Si une CMK est configurée pour votre abonnement, un double chiffrement est fourni, ce qui offre une deuxième couche de protection, tout en vous permettant de contrôler la clé de chiffrement par le biais de votre coffre de clés Azure.

La fonctionnalité Réponses aux questions utilise la [prise en charge de CMK par Recherche Azure](../../../../search/search-security-manage-encryption-keys.md) et associe automatiquement la CMK fournie pour chiffrer les données stockées dans l’index de recherche Azure.

> [!IMPORTANT]
> Votre ressource de service Azure Search doit avoir été créée après le janvier 2019 et ne peut pas se trouver dans le niveau gratuit (partagé). Il n’existe aucune prise en charge pour configurer des clés gérées par le client dans le portail Azure.

## <a name="enable-customer-managed-keys"></a>Activer des clés gérées par le client

Procédez comme suit pour activer des clés CMK :

1.  Accédez à l’onglet **Chiffrement** de votre ressource de langue en activant les réponses aux questions personnalisées.
2.  Sélectionnez l’option **Clés gérées par le client**. Fournissez les détails de vos [clés gérées par le client](../../../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal), puis sélectionnez **Enregistrer**.

> [!div class="mx-imgBorder"]
> ![Réponses aux questions CMK](../media/encrypt-data-at-rest/question-answering-cmk.png)
   
3.  Quand l’enregistrement aboutit, CMK est utilisé pour chiffrer les données stockées dans l’index de recherche Azure.

> [!IMPORTANT]
> Nous vous recommandons de définir votre CMK dans un nouveau service Recherche cognitive Azure avant de créer des bases de connaissances. Si vous définissez CMK dans une ressource de langue avec les bases de connaissances existantes, vous risquez de perdre l’accès à ceux-ci. Découvrez-en plus sur l’[utilisation de contenu chiffré](../../../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content) dans Recherche cognitive Azure.

> [!NOTE]
> Pour demander la possibilité d’utiliser des clés gérées par le client, complétez et envoyez le [formulaire de demande de clé gérée par le client de Cognitive Services](https://aka.ms/cogsvc-cmk).

## <a name="regional-availability"></a>Disponibilité régionale

Les clés gérées par le client sont disponibles dans toutes les régions Azure Search.

## <a name="encryption-of-data-in-transit"></a>Chiffrement des données en transit

Le portail Language Studio s’exécute dans le navigateur de l’utilisateur. Chaque action déclenche un appel direct à l’API Cognitive Services respective. Par conséquent, les réponses aux questions sont conformes aux données en transit.

## <a name="next-steps"></a>Étapes suivantes

* [Chiffrement dans Azure Search à l’aide de clés CMK dans Azure Key Vault](../../../../search/search-security-manage-encryption-keys.md)
* [Chiffrement des données au repos](../../../../security/fundamentals/encryption-atrest.md)
* [En savoir plus sur Azure Key Vault](../../../../key-vault/general/overview.md)
