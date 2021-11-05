---
title: Utiliser Azure Chaos Studio pour répliquer une panne DNS Internet avec l’erreur de groupe de sécurité réseau
description: Démarrez avec Chaos Studio en créant une panne DNS avec l’erreur de groupe de sécurité réseau.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 08/26/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: dc7a04c7bd20f1b778aa23f163b57161c0fe7745
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096914"
---
# <a name="quickstart-internet-dns-outage-using-network-security-group-fault"></a>Démarrage rapide : panne DNS Internet avec l’erreur de groupe de sécurité réseau

L’erreur de groupe de sécurité réseau vous permet de modifier vos règles de groupe de sécurité réseau existantes dans le cadre d’une expérience de chaos. Avec cette erreur, vous pouvez bloquer le trafic réseau vers vos ressources Azure en simulant une perte de connectivité ou des pannes de ressources dépendantes. Dans ce guide de démarrage rapide, vous allez créer une expérience de chaos qui bloque tout le trafic vers des serveurs DNS (Internet) externes pendant 15 minutes. À l’aide de cette expérience, vous pouvez vérifier que les ressources connectées au réseau virtuel Azure associé au groupe de sécurité réseau cible n’ont pas de dépendance vis-à-vis des serveurs DNS externes. Vous pouvez ainsi vérifier l’une des exigences du modèle de menace de risque.

## <a name="prerequisites"></a>Prérequis

- Un groupe de sécurité réseau associé à la ressource Azure que vous souhaitez cibler dans votre expérience  
- Utilisation de l’environnement Bash dans [Azure Cloud Shell](../cloud-shell/quickstart.md)

## <a name="create-the-network-security-group-fault-provider"></a>Créer le fournisseur d’erreur de groupe de sécurité réseau

Tout d’abord, vous devez inscrire un fournisseur d’erreur sur l’abonnement hébergeant votre groupe de sécurité réseau pour Chaos Studio afin d’interagir avec celui-ci.

1. Créez un fichier nommé **AzureNetworkSecurityGroupChaos.json** avec le contenu suivant et enregistrez-le sur votre machine locale.

      ```json
      { 
        "properties": {
          "enabled": true,
          "providerConfiguration": {
            "type": "AzureNetworkSecurityGroupChaos"
          }
        }
      }
      ```

1. Lancez un interpréteur de commandes [Cloud Shell](https://shell.azure.com/).
1. Remplacez **$SUBSCRIPTION_ID** par l’ID de l’abonnement Azure contenant le groupe de sécurité réseau que vous souhaitez utiliser dans votre expérience et exécutez la commande suivante pour vous assurer que le fournisseur sera inscrit sur l’abonnement approprié.

    ```bash
    az account set --subscription $SUBSCRIPTION_ID
    ```

1. Glissez-déposez le fichier **AzureNetworkSecurityGroupChaos.json** dans la fenêtre Cloud Shell pour le charger.
1. Remplacez l’ID **$SUBSCRIPTION_ID** utilisé à l’étape précédente et exécutez la commande suivante pour inscrire le fournisseur d’erreur AzureNetworkSecurityGroupChaos.

    ```bash
    az rest --method put --url "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/microsoft.chaos/chaosProviderConfigurations/AzureNetworkSecurityGroupChaos?api-version=2021-06-21-preview" --body @AzureNetworkSecurityGroupChaos.json --resource "https://management.azure.com"
    ```

1. (Facultatif) Supprimez le fichier **AzureNetworkSecurityGroupChaos.json** que vous avez créé précédemment, car il n’est plus nécessaire, puis fermez la fenêtre Cloud Shell.

## <a name="create-a-chaos-experiment"></a>Créer une expérience de chaos

Une fois le fournisseur d’erreur de groupe de sécurité réseau créé, vous pouvez commencer à créer des expériences dans Chaos Studio.

1. Ouvrez le portail Azure avec l’indicateur de fonctionnalité Chaos Studio :
    * Si vous utilisez un compte @microsoft.com, [cliquez sur ce lien](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true).
    * Si vous utilisez un compte externe, [cliquez sur ce lien](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}).
1. Cliquez sur **Ajouter une expérience**.

    ![Ajouter une expérience dans le portail Azure](images/add-an-experiment.png)

1. Entrez le nom que vous souhaitez attribuer à l’expérience, puis sélectionnez l’abonnement, le groupe de ressources et l’emplacement (région) où l’expérience sera créée.
1. Cliquez sur **Suivant : Concepteur d’expériences >** .

    ![Créer une expérience dans le portail Azure](images/create-an-experiment-dns-outage.png)

1. Cliquez sur **Ajouter une erreur**.
1. Sélectionnez **Erreur de groupe de sécurité réseau** dans le menu déroulant des erreurs.

    ![Sélection de l’erreur NSG dans le portail Azure](images/network-security-group-fault.png)

1. Renseignez les paramètres suivants.

    | Paramètre | Valeur |
    | -- | -- |
    | Duration | `15` |
    | Nom | `block_internet_dns` |
    | Préfixe d’adresse source | `*` |
    | Préfixe d’adresse de destination | `Internet` |
    | Access | `Deny` |
    | Plage de ports sources | `*` |
    | Plage de ports de destination | `53` |
    | Priority | `1001` |
    | Sens | `Outbound` |

    ![Paramètres de groupe de sécurité réseau dans le portail Azure](images/qs-network-outage-dns-parameters.png)

> [!NOTE]
> Vous devrez peut-être modifier les champs de nom et de priorité s’ils existent déjà dans la règle préexistante du groupe de sécurité réseau cible.

> [!NOTE]
> La priorité doit peut-être être abaissée s’il existe des règles qui autorisent explicitement le trafic du port 53 vers Internet.

1. Cliquez sur **Suivant : Ressources cibles >** .
1. Sélectionnez les groupes de sécurité réseau qui doivent être ciblés par cette expérience.
1. Cliquez sur **Ajouter**

    ![Sélection des ressources cibles](images/nsg-fault-targets.png)

1. Cliquez sur **Vérifier + créer**.

    ![Vérification et création de l’expérience](images/review-create.png)

1. Cliquez sur **Créer**

    ![Bouton de création de l’expérience](images/create.png)

## <a name="grant-the-chaos-experiment-access-to-the-network-security-group"></a>Accorder à l’expérience de chaos l’accès au groupe de sécurité réseau

Pour des raisons de sécurité, toutes les expériences de chaos doivent avoir accès aux ressources Azure ciblées dans l’expérience.

1. Accédez aux ressources ciblées dans l’expérience.
1. Cliquez sur **Contrôle d’accès (IAM)** .

    ![Modification de l’accès](images/access-control.png)

1. Cliquez sur **Ajouter**

    ![Bouton Ajouter](images/add.png)

1. Cliquez sur **Ajouter une attribution de rôle**.

    ![Bouton Ajouter une attribution de rôle](images/add-role-assignment.png)

1. Sous **Rôle**, sélectionnez `Network Contributor`.

    ![Sélection du rôle Contributeur de réseaux](images/role-network-contributor.png)

1. Sous **Sélectionner**, entrez le nom de l’expérience de chaos, puis cliquez dessus.

    ![Sélection du nom de l’expérience](images/qs-network-outage-dns-select.png)

1. Cliquez sur **Enregistrer**.

    ![Enregistrer les modifications](images/save-discard.png)

## <a name="run-the-chaos-experiment"></a>Exécuter l’expérience de chaos

1. Ouvrez le portail Azure avec l’indicateur de fonctionnalité Chaos Studio :
    * Si vous utilisez un compte @microsoft.com, [cliquez sur ce lien](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true).
    * Si vous utilisez un compte externe, [cliquez sur ce lien](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}).
1. Cochez la case en regard du nom de l’expérience, puis cliquez sur **Démarrer l’expérience**.

    ![Démarrer l’expérience](images/qs-network-outage-dns-start.png)

1. Cliquez sur **Oui** pour confirmer le démarrage de l’expérience de chaos.

    ![Confirmer que vous souhaitez démarrer l’expérience](images/start-experiment-confirmation.png)
1. (Facultatif) Cliquez sur le nom de l’expérience pour afficher une vue détaillée de l’état d’exécution de l’expérience.
1. Une fois l’expérience démarrée, vous pouvez utiliser vos outils de supervision, de télémétrie et/ou de journalisation existants pour vérifier l’impact de l’exécution de l’expérience de chaos sur votre service.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Effectuez les étapes suivantes si vous n’envisagez pas de continuer à utiliser cette expérience et que vous souhaitez la supprimer.

Ouvrez le portail Azure avec l’indicateur de fonctionnalité Chaos Studio :
    * Si vous utilisez un compte @microsoft.com, [cliquez sur ce lien](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true).
    * Si vous utilisez un compte externe, [cliquez sur ce lien](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}).
1. Cochez la case en regard du nom de l’expérience, puis cliquez sur **Supprimer**.
1. Cliquez sur **Oui** pour confirmer la suppression de l’expérience.

Effectuez les étapes suivantes si vous n’envisagez pas de continuer à utiliser des erreurs liées aux groupes de sécurité réseau.

1. Lancez un interpréteur de commandes [Cloud Shell](https://shell.azure.com/).
1. Remplacez **$SUBSCRIPTION_ID** par l’ID de l’abonnement Azure dans lequel le fournisseur d’erreur de groupe de sécurité réseau a été provisionné, puis exécutez la commande suivante.

    ```bash
    az rest --method delete --url "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/microsoft.chaos/chaosProviderConfigurations/AzureNetworkSecurityGroupChaos?api-version=2021-06-21-preview" --resource "https://management.azure.com"
    ```
