---
title: Effectuer la rotation des informations d’identification cloudadmin pour Azure VMware Solution
description: Découvrez comment effectuer la rotation des informations d’identification de vCenter Server pour votre cloud privé Azure VMware Solution.
ms.topic: how-to
ms.date: 09/10/2021
ms.openlocfilehash: f8ab35888d2d298d1dcb6acd2abd6d659b782fd7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461763"
---
# <a name="rotate-the-cloudadmin-credentials-for-azure-vmware-solution"></a>Effectuer la rotation des informations d’identification cloudadmin pour Azure VMware Solution

>[!IMPORTANT]
>Actuellement, la rotation de vos informations d’identification d’*administrateur* de NSX-T Manager n’est pas prise en charge.  Pour effectuer une rotation de votre mot de passe NSX-T Manager, envoyez une [demande de support](https://rc.portal.azure.com/#create/Microsoft.Support). Ce processus peut affecter l’exécution des services HCX.

Dans cet article, vous allez effectuer la rotation des informations d’identification cloudadmin (informations d’identification *CloudAdmin* vCenter Server) pour votre cloud privé Azure VMware Solution.  Bien que le mot de passe de ce compte n’expire pas, vous pouvez en générer un nouveau à tout moment.

>[!CAUTION]
>Si vous utilisez vos informations d’identification cloudadmin pour connecter des services à vCenter dans votre cloud privé, ces connexions cesseront de fonctionner une fois que vous aurez effectué la rotation de votre mot de passe. Ces connexions verrouilleront par ailleurs le compte cloudadmin, sauf si vous arrêtez ces services avant d’effectuer la rotation du mot de passe.

## <a name="prerequisites"></a>Prérequis

Examinez et déterminez les services qui se connectent à vCenter tels que *cloudadmin@vsphere.local* avant d’effectuer la rotation du mot de passe. Ces services peuvent inclure des services VMware tels que HCX, vRealize Orchestrator, vRealize Operations Manager, VMware Horizon ou d’autres outils tiers utilisés pour la surveillance ou l’approvisionnement. 

Une façon de déterminer les services qui s’authentifient auprès de vCenter à l’aide de l’utilisateur cloudadmin consiste à inspecter les événements vSphere à l’aide du client vSphere pour votre cloud privé. Après avoir identifié ces services et avant d’effectuer la rotation du mot de passe, vous devez arrêter ces services, sans quoi ceux-ci ne fonctionneront pas une fois la rotation du mot de passe effectuée. Vous rencontrerez également des verrouillages temporaires sur votre compte vCenter CloudAdmin, car ces services essaient continuellement de s’authentifier à l’aide d’une version mise en cache des anciennes informations d’identification. 

Au lieu d’utiliser l’utilisateur cloudadmin pour connecter des services à vCenter, nous recommandons d’utiliser des comptes individuels pour chaque service. Pour plus d’informations sur la configuration de comptes distincts pour les services connectés, consultez la page sur les [concepts d’accès et d’identité](./concepts-identity.md).

## <a name="reset-your-vcenter-credentials"></a>Réinitialiser vos informations d’identification vCenter

### <a name="portal"></a>[Portail](#tab/azure-portal)
 
1. Dans votre cloud privé Azure VMware Solution, sélectionnez **Identité**.

1. Sélectionnez **Générer un nouveau mot de passe**.

   :::image type="content" source="media/rotate-cloudadmin-credentials/reset-vcenter-credentials-1.png" alt-text="Capture d’écran montrant les informations d’identification vCenter et un moyen de les copier ou de générer un nouveau mot de passe." lightbox="media/rotate-cloudadmin-credentials/reset-vcenter-credentials-1.png":::

1. Activez la case à cocher de confirmation, puis sélectionnez **Générer un mot de passe**.


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour commencer avec Azure CLI :

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

1. Dans votre cloud privé Azure VMware Solution, ouvrez une session Azure Cloud Shell.

2. Mettez à jour vos informations d’identification vCenter *CloudAdmin*.  N’oubliez pas de remplacer **{SubscriptionID}** , **{ResourceGroup}** et **{PrivateCloudName}** par les informations de votre cloud privé. 

   ```azurecli-interactive
   az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
   ```

---




 
## <a name="update-hcx-connector"></a>Mettre à jour le connecteur HCX 

1. Accédez au connecteur HCX local à l’adresse à l’adresse https://{ip of the HCX connector appliance}:443 et connectez-vous à l’aide des nouvelles informations d’identification.

   Veillez à utiliser le port **443**. 

2. Dans le tableau de bord VMware HCX, sélectionnez **Site Pairing (Jumelage de sites)** .
    
   :::image type="content" source="media/tutorial-vmware-hcx/site-pairing-complete.png" alt-text="Capture d’écran du tableau de bord VMware HCX avec l’option Site Pairing mise en surbrillance.":::
 
3. Sélectionnez la connexion à Azure VMware Solution qui convient, puis **Modifier la connexion**.
 
4. Fournissez les nouvelles informations d’identification d’utilisateur vCenter et sélectionnez **Modifier**, ce qui permet d’enregistrer les informations d’identification. L’enregistrement doit s’afficher correctement.


## <a name="next-steps"></a>Étapes suivantes

À présent que vous savez réinitialiser vos informations d’identification vCenter pour Azure VMware Solution, vous pouvez en apprendre davantage sur les sujets suivants :

- [Intégration des services natifs Azure dans Azure VMware Solution](integrate-azure-native-services.md)
- [Déploiement d’une récupération d’urgence pour les charges de travail Azure VMware Solution à l’aide de VMware HCX](deploy-disaster-recovery-using-vmware-hcx.md)
