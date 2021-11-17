---
title: Se connecter à et gérer Azure Files
description: Ce guide explique comment se connecter à Azure Files dans Azure Purview et utiliser les fonctionnalités de Purview pour analyser et gérer votre source Azure Files.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 95abf27060748255f24b089cfc4fb9229f33bfa5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848056"
---
# <a name="connect-to-and-manage-azure-files-in-azure-purview"></a>Se connecter à et gérer Azure Files dans Azure Purview

Cet article explique comment inscrire Azure Files et comment s’authentifier et interagir avec Azure Files dans Azure Purview. Pour plus d’informations sur Azure Purview, consultez l’[article d’introduction](overview.md).

## <a name="supported-capabilities"></a>Fonctionnalités prises en charge

|**Extraction des métadonnées**|  **Analyse complète**  |**Analyse incrémentielle**|**Analyse délimitée**|**Classification**|**Stratégie d'accès**|**Traçabilité**|
|---|---|---|---|---|---|---|
| [Oui](#register) | [Oui](#scan) | [Oui](#scan) | [Oui](#scan) | [Oui](#scan) | Non | Limité** |

\** La traçabilité est prise en charge si le jeu de données est utilisé en tant que source/récepteur dans une [activité de copie Data Factory](how-to-link-azure-data-factory.md). 

Azure Files prend en charge les analyses complètes et incrémentielles pour capturer les métadonnées et les classifications, basées sur un système par défaut et des règles de classification personnalisées.

Pour les types de fichiers comme CSV, TSV, PSV et SSV, le schéma est extrait quand les logiques suivantes sont en place :

1. Les valeurs de la première ligne ne sont pas vides
2. Les valeurs de la première ligne sont uniques
3. Les valeurs de la première ligne ne sont ni une date ni un nombre

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Une [ressource Purview](create-catalog-portal.md) active.

* Vous devez être un administrateur de source de données et un lecteur de données pour inscrire une source et la gérer dans Purview Studio. Pour plus d’informations, consultez notre [page d’autorisations Azure Purview](catalog-permissions.md).

## <a name="register"></a>Inscrire

Cette section explique comment inscrire Azure Files dans Azure Purview à l’aide de [Purview Studio](https://web.purview.azure.com/).

### <a name="authentication-for-registration"></a>Authentification pour l’inscription

Actuellement, il n’existe qu’une seule façon de configurer l’authentification pour le partage de fichiers Azure :

- Clé du compte

#### <a name="account-key-to-register"></a>Clé de compte à inscrire

Lorsque la méthode d’authentification sélectionnée est **Clé de compte**, vous devez récupérer votre clé d’accès et la stocker dans le coffre de clés :

1. Accédez à votre compte de stockage
1. Sélectionnez **Paramètres > Clés d’accès**
1. Copiez votre *clé* et enregistrez-la quelque part pour les étapes suivantes
1. Accédez à votre coffre de clés.
1. Sélectionnez **Paramètres > Secrets**.
1. Sélectionnez **+ Générer/importer**, puis entrez le **Nom** et la **Valeur** comme *clé* de votre compte de stockage.
1. Sélectionnez **Créer** pour terminer.
1. Si votre coffre de clés n’est pas encore connecté à Purview, vous devrez [créer une connexion de coffre de clés](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. Enfin, [créez des informations d’identification](manage-credentials.md#create-a-new-credential) à l’aide de la clé pour configurer votre analyse.

### <a name="steps-to-register"></a>Procédure d’inscription

Pour inscrire un nouveau compte Azure Files dans votre catalogue de données, suivez ces étapes :

1. Accédez à votre Purview Data Studio.
1. Sélectionnez **Mappage de données** dans le volet de navigation de gauche.
1. Sélectionnez **Inscrire**.
1. Sous **Inscrire des sources**, sélectionnez **Azure Files**.
1. Sélectionnez **Continue** (Continuer)

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="Inscription d’une nouvelle source de données" border="true":::

Sur l’écran **Inscrire des sources (Azure Files)** , suivez ces étapes :

1. Entrez le **Nom** sous lequel la source de données apparaîtra dans le catalogue.
2. Choisissez votre abonnement Azure pour filtrer les comptes Stockage Azure.
3. Sélectionnez un compte Stockage Azure.
4. Sélectionnez une collection ou créez-en une (facultatif).
5. Sélectionnez **Inscrire** pour inscrire la source de données.

:::image type="content" source="media/register-scan-azure-files/azure-file-register-source.png" alt-text="options pour inscrire des sources" border="true":::

## <a name="scan"></a>Analyser

Suivez les étapes ci-dessous pour analyser Azure Files pour identifier automatiquement les ressources et classer vos données. Pour plus d’informations sur l’analyse en général, consultez notre [Présentation des analyses et de l’ingestion](concept-scans-and-ingestion.md)

### <a name="create-and-run-scan"></a>Créer et exécuter des scripts

Pour créer et exécuter une nouvelle analyse, procédez comme suit :

1. Sélectionnez l’onglet **Data Map** dans le volet gauche de [Purview Studio](https://web.purview.azure.com/resource/).

1. Sélectionnez la source Azure Files que vous avez inscrite.

1. Sélectionnez **Nouvelle analyse**.

1. Sélectionnez les informations d’identification de la clé de compte pour vous connecter à votre source de données.

   :::image type="content" source="media/register-scan-azure-files/set-up-scan-azure-file.png" alt-text="Configurer l’analyse":::

1. Vous pouvez étendre votre analyse à des bases de données spécifiques en choisissant les éléments appropriés dans la liste.

   :::image type="content" source="media/register-scan-azure-files/azure-file-scope-your-scan.png" alt-text="Définir la portée de votre analyse":::

1. Sélectionnez ensuite un ensemble de règles pour l’analyse. Vous pouvez choisir entre l’ensemble système par défaut, les ensembles de règles personnalisés existants ou créer un ensemble de règles inline.

   :::image type="content" source="media/register-scan-azure-files/azure-file-scan-rule-set.png" alt-text="Ensemble de règles d’analyse":::

1. Choisissez votre déclencheur d’analyse. Vous pouvez configurer une planification pour qu’elle se reproduise ou exécuter l’analyse une seule fois.

   :::image type="content" source="media/register-scan-azure-files/trigger-scan.png" alt-text="trigger":::

1. Passez en revue votre analyse et sélectionnez **Enregistrer et exécuter**.

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez inscrit votre source, suivez les guides ci-dessous pour en savoir plus sur Purview et sur vos données.

- [Insights de données dans Azure Purview](concept-insights.md)
- [Lignage dans Azure Purview](catalog-lineage-user-guide.md)
- [Rechercher dans un catalogue de données](how-to-search-catalog.md)
