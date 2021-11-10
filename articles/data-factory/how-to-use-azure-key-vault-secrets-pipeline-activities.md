---
title: Utiliser des secrets Azure Key Vault dans les activités de pipeline
description: Découvrez comment récupérer des informations d’identification stockées à partir d’Azure Key Vault, et comment les utiliser lors de l’exécution de pipelines Data Factory.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 10/22/2021
ms.openlocfilehash: 09a2c057cbefefc02e40d326eea094e37b092cc5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851151"
---
# <a name="use-azure-key-vault-secrets-in-pipeline-activities"></a>Utiliser des secrets Azure Key Vault dans les activités de pipeline

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Vous pouvez stocker des informations d’identification ou des valeurs secrètes dans Azure Key Vault et les utiliser pendant l’exécution du pipeline pour les transmettre à vos activités.

## <a name="prerequisites"></a>Prérequis

Cette fonctionnalité repose sur l’identité managée de la fabrique de données.  Découvrez comment cela fonctionne dans la section [Identité managée pour Data Factory](./data-factory-service-identity.md) et vérifiez que votre fabrique de données est bien associée à une identité managée.

## <a name="steps"></a>Étapes

1. Ouvrez les propriétés de votre fabrique de données et copiez la valeur ID de l’application d’identité managée.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/managedidentity.png" alt-text="Valeur de l’identité managée":::

2. Ouvrez les stratégies d’accès Key Vault et ajoutez les autorisations d’identité managée pour récupérer et répertorier les secrets.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies.png" alt-text="Capture d’écran représentant la page &quot;Stratégies d’accès&quot;, sur laquelle l’action &quot;Ajouter une stratégie d’accès&quot; est en surbrillance.":::

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/akvaccesspolicies-2.png" alt-text="Stratégies d’accès Key Vault":::

    Cliquez sur **Ajouter**, puis sur **Enregistrer**.

3. Accédez à votre secret Key Vault et copiez l’identificateur de secret.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/secretidentifier.png" alt-text="Identificateur de secret":::

    Prenez note de l’URI de secret que vous souhaitez obtenir lors de l’exécution de votre pipeline Data Factory.

4. Dans votre pipeline Data Factory, ajoutez une nouvelle activité web et configurez-la comme suit.  

    |Propriété  |Valeur  |
    |---------|---------|
    |Sortie sécurisée     |True         |
    |URL     |[Valeur de votre URI de secret]?api-version=7.0         |
    |Méthode     |GET         |
    |Authentification     |MSI         |
    |Ressource        |https://vault.azure.net       |

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/webactivity.png" alt-text="Activité Web":::

    > [!IMPORTANT]
    > Vous devez ajouter la chaîne **?api-version=7.0** à la fin de votre URI de secret.  

    > [!CAUTION]
    > Définissez l’option de sortie sécurisée sur « True » pour empêcher la journalisation de la valeur secrète en texte brut.  Pour toutes les autres activités qui consomment cette valeur, l’option d’entrée sécurisée doit être définie sur « True ».

5. Pour utiliser la valeur dans une autre activité, utilisez l’expression de code suivante : **@activity('Web1').output.value**.

    :::image type="content" source="media/how-to-use-azure-key-vault-secrets-pipeline-activities/usewebactivity.png" alt-text="Expression de code":::

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment utiliser Azure Key Vault pour stocker les informations d’identification des magasins de données et des calculs, voir [Informations d’identification dans Azure Key Vault](./store-credentials-in-key-vault.md).