---
title: Résoudre les erreurs courantes de déploiement Azure
description: Décrit les erreurs courantes pour les ressources Azure déployées avec des modèles Azure Resource Manager (modèles ARM) ou des fichiers Bicep.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 988d2e85475ed1c94580cc550630df82fceddb7a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470240"
---
# <a name="troubleshoot-common-azure-deployment-errors"></a>Résoudre les erreurs courantes de déploiement Azure

Cet article décrit les erreurs courantes de déploiement d’Azure et fournit des informations sur les solutions. Les ressources Azure peuvent être déployées avec des modèles Azure Resource Manager (modèles ARM) ou des fichiers Bicep. Si vous ne trouvez pas le code d’erreur correspondant à l’erreur de votre déploiement, consultez [Rechercher un code d’erreur](find-error-code.md).

Si votre code d’erreur ne figure pas dans la liste, envoyez un problème GitHub. Sur le côté droit de la page, sélectionnez **Commentaires**. En bas de la page, sous **Commentaires**, sélectionnez **Cette page**.

## <a name="error-codes"></a>Codes d’erreur

| Code d'erreur | Limitation des risques | Informations complémentaires |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Suivez les restrictions concernant l’attribution de noms pour les comptes de stockage. | [Résoudre les erreurs de nom du compte de stockage](error-storage-account-name.md) |
| AccountPropertyCannotBeSet | Vérifiez les propriétés disponibles du compte de stockage. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | Le cluster ou la région n’a pas de ressources disponibles ou ne prend pas en charge la taille de machine virtuelle demandée. Renouvelez la demande plus tard ou demandez une taille de machine virtuelle différente. | [Problèmes de provisionnement et d’allocation pour Linux](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux) <br><br> [Problèmes de provisionnement et d’allocation pour Windows](/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) <br><br> [Résoudre des échecs d’allocation](/troubleshoot/azure/virtual-machines/allocation-failure)|
| AnotherOperationInProgress | Attendez que l’opération simultanée soit terminée. | |
| AuthorizationFailed | Votre compte ou principal du service ne dispose pas de droits d’accès suffisants pour terminer le déploiement. Vérifiez le rôle auquel votre compte appartient et son accès dans le cadre du déploiement.<br><br>Cette erreur peut s’afficher quand un fournisseur de ressources requis n’est pas inscrit. | [Contrôle d’accès en fonction du rôle Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)<br><br>[Résoudre les erreurs d’inscription](error-register-resource-provider.md) |
| BadRequest | Vous avez envoyé des valeurs de déploiement qui ne correspondent pas aux valeurs attendues par Resource Manager. Vérifiez le message d’état interne pour résoudre plus facilement le problème. | [Référence de modèle](/azure/templates/) <br><br> [Emplacements pris en charge](../templates/resource-location.md) |
| Conflit | Vous demandez une opération qui n’est pas autorisée dans l’état actuel de la ressource. Par exemple, un redimensionnement de disque est autorisé uniquement durant la création ou la libération d’une machine virtuelle. | |
| DeploymentActiveAndUneditable | Attendez le déploiement simultané sur ce groupe de ressources soit terminé. | |
| DeploymentFailedCleanUp | Lorsque vous déployez en mode complet, toutes les ressources qui ne sont pas dans le modèle sont supprimées. Vous recevez cette erreur lorsque vous n’avez pas les autorisations appropriées pour supprimer toutes les ressources qui ne sont pas dans le modèle. Pour éviter cette erreur, remplacez le mode de déploiement par le mode incrémentiel. | [Modes de déploiement Azure Resource Manager](../templates/deployment-modes.md) |
| DeploymentNameInvalidCharacters | Le nom du déploiement ne peut contenir que des lettres, des chiffres, des traits d’union `(-)`, des points `(.)` et des traits de soulignement `(_)`. | |
| DeploymentNameLengthLimitExceeded | Les noms de déploiement sont limités à 64 caractères.  | |
| DeploymentFailed | L’erreur DeploymentFailed est une erreur générale qui ne fournit pas les détails dont vous avez besoin pour résoudre l’erreur. Pour en savoir plus, recherchez un code d’erreur dans les détails de l’erreur. | [Rechercher un code d’erreur](find-error-code.md) |
| DeploymentQuotaExceeded | Si vous atteignez la limite des 800 déploiements par groupe de ressources, supprimez les déploiements inutiles dans l’historique. | [Résoudre l’erreur de nombre de déploiements supérieur à 800](deployment-quota-exceeded.md) |
| DeploymentJobSizeExceeded | Simplifiez votre modèle pour réduire la taille. | [Résoudre des erreurs de taille de modèle](error-job-size-exceeded.md) |
| DnsRecordInUse | Le nom de l’enregistrement DNS doit être unique. Entrez un autre nom. | |
| ImageNotFound | Vérifiez les paramètres d’image de machine virtuelle. |  |
| InternalServerError | Erreur causée par un problème temporaire. Recommencez le déploiement. | |
| InUseSubnetCannotBeDeleted | Cette erreur peut se produire lorsque vous essayez de mettre à jour une ressource, si le processus de requête supprime et crée la ressource. Veillez à spécifier toutes les valeurs non modifiées. | [Mettre à jour une ressource](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Procurez-vous le jeton d’accès pour le client approprié. Vous pouvez uniquement obtenir le jeton auprès du client auquel appartient votre compte. | |
| InvalidContentLink | Vous avez probablement tenté d’établir une liaison avec un modèle imbriqué qui n’est pas disponible. Vérifiez l’URI que vous avez indiqué pour le modèle imbriqué. Si le modèle existe dans un compte de stockage, assurez-vous que l’URI est accessible. Vous devrez peut-être valider un jeton SAS. Vous ne pouvez pas établir un lien vers un modèle qui se trouve dans un compte de stockage derrière un [pare-feu Stockage Azure](../../storage/common/storage-network-security.md). Envisagez de déplacer votre modèle vers un autre dépôt, par exemple GitHub. | [Modèles liés](../templates/linked-templates.md) |
| InvalidDeploymentLocation | Lors du déploiement au niveau de l’abonnement, vous avez fourni un emplacement différent pour un nom de déploiement précédemment utilisé. | [Déploiements de niveau abonnement](../templates/deploy-to-subscription.md) |
| InvalidParameter | L’une des valeurs que vous avez fournies pour une ressource ne correspond pas à la valeur attendue. Cette erreur peut être due à de nombreuses conditions différentes. Par exemple, il se peut qu’un mot de passe soit insuffisant ou un nom d’objet blob incorrect. Le message d’erreur doit indiquer la valeur à corriger. | |
| InvalidRequestContent | Les valeurs de déploiement contiennent des valeurs inattendues ou n’incluent pas les valeurs requises. Vérifiez les valeurs pour votre type de ressource. | [Référence de modèle](/azure/templates/) |
| InvalidRequestFormat | Activez l’enregistrement du débogage durant l’exécution du déploiement et vérifiez le contenu de la demande. | [Activer l’enregistrement du débogage](enable-debug-logging.md) |
| InvalidResourceLocation | Attribuez un nom unique au compte de stockage. | [Résoudre les erreurs de nom du compte de stockage](error-storage-account-name.md) |
| InvalidResourceNamespace | Vérifiez l’espace de noms de ressources que vous avez spécifié dans la propriété **type**. | [Référence de modèle](/azure/templates/) |
| InvalidResourceReference | La ressource n’existe pas encore ou n’est pas correctement référencée. Vérifiez si vous devez ajouter une dépendance. Vérifiez que votre utilisation de la fonction **reference** inclut les paramètres requis pour votre scénario. | [Résoudre les erreurs de dépendance](error-not-found.md) |
| InvalidResourceType | Vérifiez le type de ressource que vous avez spécifié dans la propriété **type**. | [Référence de modèle](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Inscrivez votre abonnement auprès du fournisseur de ressources. | [Résoudre les erreurs d’inscription](error-register-resource-provider.md) |
| InvalidTemplateDeployment <br> InvalidTemplate | Vérifiez que la syntaxe de votre modèle ne contient pas d’erreurs. | [Résoudre les erreurs de modèle non valide](error-invalid-template.md) |
| InvalidTemplateCircularDependency | Supprimez les dépendances inutiles. | [Résoudre les dépendances circulaires](error-invalid-template.md#circular-dependency) |
| JobSizeExceeded | Simplifiez votre modèle pour réduire la taille. | [Résoudre des erreurs de taille de modèle](error-job-size-exceeded.md) |
| LinkedAuthorizationFailed | Vérifiez si votre compte appartient au même locataire que le groupe de ressources vers lequel vous effectuez le déploiement. | |
| LinkedInvalidPropertyId | L’ID de ressource pour une ressource particulière n’est pas résolu. Vérifiez que vous avez fourni toutes les valeurs requises pour l’ID de ressource. Par exemple, ID d’abonnement, le nom du groupe de ressources, le type de ressource, le nom de la ressource parente (si nécessaire) et le nom de la ressource. | |
| LocationRequired | Fournissez un emplacement pour la ressource. | [Définir un emplacement](../templates/resource-location.md) |
| MismatchingResourceSegments | Assurez-vous qu’une ressource imbriquée a le nombre correct de segments dans le nom et le type. | [Résoudre les segments de la ressource](error-invalid-template.md#incorrect-segment-lengths) |
| MissingRegistrationForLocation | Vérifiez l’état d’inscription du fournisseur de ressources, ainsi que les emplacements pris en charge. | [Résoudre les erreurs d’inscription](error-register-resource-provider.md) |
| MissingSubscriptionRegistration | Inscrivez votre abonnement auprès du fournisseur de ressources. | [Résoudre les erreurs d’inscription](error-register-resource-provider.md) |
| NoRegisteredProviderFound | Vérifier l’état d’inscription du fournisseur de ressources. | [Résoudre les erreurs d’inscription](error-register-resource-provider.md) |
| NotFound | Vous essayez peut-être de déployer une ressource dépendante en parallèle avec une ressource parente. Vérifiez si vous avez besoin d’ajouter une dépendance. | [Résoudre les erreurs de dépendance](error-not-found.md) |
| OperationNotAllowed | Le déploiement tente une opération qui dépasse le quota autorisé pour l’abonnement, le groupe de ressources ou la région. Si possible, modifiez votre déploiement pour respecter les quotas. Dans le cas contraire, vous pouvez demander une modification de vos quotas. | [Résoudre les erreurs de quota](error-resource-quota.md) |
| ParentResourceNotFound | Assurez-vous qu’il existe une ressource parente avant de créer des ressources enfants. | [Résoudre les erreurs de ressource parente](error-parent-resource.md) |
| PasswordTooLong | Vous avez peut-être choisi un mot de passe comportant trop de caractères ou converti la valeur de votre mot de passe en chaîne sécurisée avant de la transmettre en tant que paramètre. Si le modèle inclut un paramètre de **chaîne sécurisée**, vous n’avez pas besoin de convertir la valeur en chaîne sécurisée. Indiquez la valeur de mot de passe sous forme de texte. |  |
| PrivateIPAddressInReservedRange | L’adresse IP spécifiée inclut une plage d’adresses requise par Azure. Modifiez l’adresse IP pour éviter d’utiliser la plage réservée. | [Adresses IP](../../virtual-network/ip-services/public-ip-addresses.md) |
| PrivateIPAddressNotInSubnet | L’adresse IP spécifiée se trouve en dehors de la plage de sous-réseau. Modifiez l’adresse IP pour qu’elle se trouve dans la plage de sous-réseau. | [Adresses IP](../../virtual-network/ip-services/public-ip-addresses.md) |
| PropertyChangeNotAllowed | Certaines propriétés ne peuvent pas être changées sur une ressource déployée. Durant la mise à jour d’une ressource, limitez vos modifications aux propriétés autorisées. | [Mettre à jour une ressource](/azure/architecture/guide/azure-resource-manager/advanced-templates/update-resource) |
| RequestDisallowedByPolicy | Votre abonnement inclut une stratégie de ressource qui empêche une action que vous tentez d’exécuter au cours du déploiement. Recherchez la stratégie qui bloque l’action. Si possible, modifiez votre déploiement pour respecter les limitations de la stratégie. | [Résoudre les erreurs de stratégie](error-policy-requestdisallowedbypolicy.md) |
| ReservedResourceName | Spécifiez un nom de ressource qui n’inclut pas de nom réservé. | [Noms de ressource réservés](error-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Attendez que la suppression soit terminée. | |
| ResourceGroupNotFound | Vérifiez le nom du groupe de ressources cible pour le déploiement. Le groupe de ressources cible doit déjà exister dans votre abonnement. Vérifiez le contexte de votre abonnement. | [Azure CLI](/cli/azure/account?#az_account_set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | Votre déploiement fait référence à une ressource qui ne peut pas être résolue. Vérifiez que votre utilisation de la fonction **reference** inclut les paramètres requis pour votre scénario. | [Résoudre les erreurs de référence](error-not-found.md) |
| ResourceQuotaExceeded | Le déploiement tente de créer des ressources qui dépassent le quota autorisé pour l’abonnement, le groupe de ressources ou la région. Si possible, modifiez votre infrastructure pour respecter les quotas. Dans le cas contraire, vous pouvez demander une modification de vos quotas. | [Résoudre les erreurs de quota](error-resource-quota.md) |
| SkuNotAvailable | Sélectionnez la référence SKU (par exemple, la taille de la machine virtuelle) disponible pour l’emplacement que vous avez sélectionné. | [Résoudre les erreurs de référence SKU](error-sku-not-available.md) |
| StorageAccountAlreadyExists <br> StorageAccountAlreadyTaken | Attribuez un nom unique au compte de stockage. | [Résoudre les erreurs de nom du compte de stockage](error-storage-account-name.md)  |
| StorageAccountNotFound | Vérifiez l’abonnement, le groupe de ressources et le nom du compte de stockage que vous tentez d’utiliser. | |
| SubnetsNotInSameVnet | Une machine virtuelle ne peut avoir qu’un seul réseau virtuel. Si vous déployez plusieurs cartes réseau, assurez-vous qu’elles appartiennent au même réseau virtuel. | [Cartes réseau multiples](../../virtual-machines/windows/multiple-nics.md) |
| SubscriptionNotFound | Impossible d’accéder à un abonnement spécifié pour le déploiement. L’ID d’abonnement est peut-être incorrect, l’utilisateur qui déploie le modèle ne dispose pas des autorisations appropriées pour le déployer sur l’abonnement, ou l’ID d’abonnement n’est pas au bon format. Lorsque vous utilisez des déploiements imbriqués pour [déployer à travers des étendues](../templates/deploy-to-resource-group.md), indiquez le GUID de l’abonnement. | |
| SubscriptionNotRegistered | Lors du déploiement d’une ressource, le fournisseur de ressources doit être inscrit pour votre abonnement. Lorsque vous utilisez un modèle Resource Manager pour le déploiement, le fournisseur de ressources est automatiquement inscrit dans l’abonnement. Parfois, l’inscription automatique ne se termine pas à temps. Pour éviter cette erreur intermittente, inscrivez le fournisseur de ressources avant le déploiement. | [Résoudre les erreurs d’inscription](error-register-resource-provider.md) |
| TemplateResourceCircularDependency | Supprimez les dépendances inutiles. | [Résoudre les dépendances circulaires](error-invalid-template.md#circular-dependency) |
| TooManyTargetResourceGroups | Réduisez le nombre de groupes de ressources pour un déploiement unique. | [Déploiement inter-étendue](../templates/deploy-to-resource-group.md) |

## <a name="next-steps"></a>Étapes suivantes

- [Trouver les codes d’erreur](find-error-code.md)
- [Activer l’enregistrement du débogage](enable-debug-logging.md)
- [Créer un modèle de résolution des problèmes](create-troubleshooting-template.md)
