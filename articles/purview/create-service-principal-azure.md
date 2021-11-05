---
title: Créer un principal de service dans Azure
description: Cet article décrit comment vous pouvez créer un principal de service dans Azure.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/15/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d2be616d19ec862a7bf27d55f0a35ed1737fbf86
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097902"
---
# <a name="creating-a-service-principal"></a>Création d’un principal de service

Vous pouvez créer un principal de service ou en utiliser un existant dans votre locataire Azure Active Directory.

## <a name="app-registration"></a>Inscription d'application

1. Accédez au [portail Azure](https://portal.azure.com).
2. Dans le menu de gauche, sélectionnez **Azure Active Directory**.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-aad.png" alt-text="Capture d’écran montrant le lien vers Azure Active Directory":::

3. Sélectionnez **Inscriptions d’applications** et **+ Nouvelle inscription**
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-reg.png" alt-text="Capture d’écran qui montre le lien vers Nouvelle inscription":::

4. Entrez un nom pour l’**application** (nom du principal de service).

5. Sélectionnez **Comptes dans ce répertoire organisationnel uniquement**.

6. Pour **URI de redirection**, sélectionnez **Web** et entrez l’URL de votre choix. Il n’est pas nécessaire qu’elle soit réelle ou qu’elle fonctionne.

7. Sélectionnez ensuite **Inscription**.
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-register.png" alt-text="Capture d’écran montrant les détails de l’inscription d’une nouvelle application":::
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-app.png" alt-text="Capture d’écran montrant l’application nouvellement créée":::

## <a name="adding-a-secret-to-the-client-credentials"></a>Ajout d’un secret aux informations d’identification du client

1. Sélectionnez l’application dans **Inscriptions d’applications**
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-app-select.png" alt-text="Capture d’écran Inscriptions d’applications qui montre l’application pour l’inscription":::

2. Cliquez sur **Ajouter un certificat ou un secret**
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-add-secret.png" alt-text="Capture d’écran qui montre l’application":::

3. Cliquez sur **+ Nouvelle clé secrète client** sous **Secrets clients**
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-new-client-secret.png" alt-text="Capture d’écran qui montre le menu Clé secrète client":::

4. Fournissez une **description** et définissez la **date d’expiration** du secret :::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-secret-desc.png" alt-text="Capture d’écran qui montre les détails de la clé secrète client":::
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-client-secret.png" alt-text="Capture d’écran montrant la clé secrète client":::

5. Copiez la valeur de **Informations d’identification du client** à partir de **Présentation**
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-client-cred.png" alt-text="Capture d’écran qui montre la vue d’ensemble de l’application":::

## <a name="adding-the-secret-to-the-key-vault"></a>Ajout de la clé secrète au coffre de clés

1. Accédez à votre **coffre de clés**
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-key-vault.png" alt-text="Capture d’écran qui montre le coffre de clés":::

2. Sélectionnez **Paramètres** --> **Secrets** -->  **+ Générer/Importer**  
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-generate-secret.png" alt-text="Capture d’écran des options du coffre de clés":::

3. Entrez le **nom** de votre choix et la **valeur** de la **clé secrète client** de votre principal de service  
:::image type="content" source="media/create-service-principal-azure/create-service-principal-azure-sp-secret.png" alt-text="Capture d’écran le coffre de clés permettant de créer un secret":::

4. Sélectionnez **Créer** pour terminer.
