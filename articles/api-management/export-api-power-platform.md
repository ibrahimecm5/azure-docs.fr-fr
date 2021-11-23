---
title: Exporter des API depuis la gestion des API Azure vers Microsoft Power Platform | Microsoft Docs
description: Découvrez comment exporter une API depuis la gestion des API en tant que connecteur personnalisé vers Power Apps et Power Automate dans Microsoft Power Platform.
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 07/27/2021
ms.author: danlep
ms.openlocfilehash: fb27e1a96152885446da2e77e0901680affe3569
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494840"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>Exporter des API depuis Gestion des API Azure vers Power Platform 

Les développeurs citoyens qui utilisent Microsoft [Power Platform](https://powerplatform.microsoft.com) doivent souvent faire appel à des fonctionnalités métier développées par des développeurs professionnels et déployées dans Azure. La [gestion des API Azure](https://aka.ms/apimrocks) permet aux développeurs professionnels de publier leur service back-end en tant qu’API et d’exporter facilement ces API vers Power Platform ([Power Apps](/powerapps/powerapps-overview) et [Power Automate](/power-automate/getting-started)) en tant que connecteurs personnalisés destinés à être découverts et consommés par des développeurs citoyens. 

Cet article présente les étapes à suivre dans le portail Azure pour créer un connecteur Power Platform personnalisé à une API dans la gestion des API. Grâce à cette fonctionnalité, les développeurs citoyens peuvent utiliser Power Platform pour créer et distribuer des applications basées sur des API internes et externes gérées par la gestion des API.

## <a name="prerequisites"></a>Prérequis

+ Suivez ce guide de démarrage rapide : [Créer une instance du service Gestion des API Azure](get-started-create-service-instance.md)
+ Vérifiez qu’il existe une API dans votre instance de Gestion des API que vous souhaitez exporter vers Power Platform
+ Veillez à disposer d’un [environnement](/powerapps/powerapps-overview#power-apps-for-admins) Power Apps ou Power Automate 

## <a name="create-a-custom-connector-to-an-api"></a>Créer un connecteur personnalisé à une API

1. Accédez au service Gestion des API dans le portail Azure.
1. Dans le menu, sous **API**, sélectionnez **Power Platform**.
1. Sélectionnez **Créer un connecteur**.
1. Dans la fenêtre **Créer un connecteur**, effectuez les opérations suivantes :
    1. Sélectionnez une API à publier dans Power Platform.
    1. Sélectionnez un environnement Power Platform dans lequel publier l’API. 
    1. Entrez un nom d’affichage, qui sera utilisé comme nom du connecteur personnalisé.  
    1. Éventuellement, si l’API est [protégée par un serveur OAuth 2.0](api-management-howto-protect-backend-with-aad.md), fournissez des détails incluant l’**ID client**, le **secret client**, l’**URL d’autorisation**, l’**URL de jeton** et l’**URL d’actualisation**.  
1. Sélectionnez **Create** (Créer). 

    :::image type="content" source="media/export-api-power-platform/create-custom-connector.png" alt-text="Créer un connecteur personnalisé pour l’API dans la gestion des API":::

Une fois le connecteur créé, accédez à votre environnement [Power Apps](https://make.powerapps.com) ou [Power Automate](https://flow.microsoft.com). Vous verrez l’API listée sous **Données > Connecteurs personnalisés**.

:::image type="content" source="media/export-api-power-platform/custom-connector-power-app.png" alt-text="Connecteur personnalisé dans Power Platform":::

> [!NOTE]
> Pour appeler l’API à partir de la console de test PowerApps, vous devez ajouter l’URL « https://flow.microsoft.com  » en tant qu’origine à la [stratégie CORS](api-management-cross-domain-policies.md#CORS) dans votre instance de gestion des API.

## <a name="next-steps"></a>Étapes suivantes

* [Découvrir plus d’informations sur Power Platform](https://powerplatform.microsoft.com/)
* [Découvrir la création et l’utilisation de connecteurs personnalisés](/connectors/custom-connectors/)
* [Découvrir les tâches courantes dans Gestion des API en suivant les tutoriels](./import-and-publish.md)
