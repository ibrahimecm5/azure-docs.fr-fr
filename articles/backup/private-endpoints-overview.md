---
title: Vue d’ensemble des points de terminaison privés
description: Comprenez comment utiliser des points de terminaison privés pour le service Sauvegarde Azure et découvrez les scénarios où l’utilisation des points de terminaison privés contribue à maintenir la sécurité de vos ressources.
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: devx-track-azurepowershell
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: c9ff432c1de5ebdb44ae8f5fa0d4889026a32252
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136468"
---
# <a name="overview-and-concepts-of-private-endpoints-for-azure-backup"></a>Vue d’ensemble et concepts des points de terminaison privés pour le service Sauvegarde Azure

Le service Sauvegarde Azure vous permet de sauvegarder vos données dans vos coffres Recovery Services pour les restaurer ultérieurement en toute sécurité en utilisant des [points de terminaison privés](../private-link/private-endpoint-overview.md). Les points de terminaison privés utilisent une ou plusieurs adresses IP privées de votre réseau virtuel Azure (VNet), plaçant de fait le service dans votre VNet.

Cet article vous aidera à comprendre comment fonctionnent les points de terminaison privés pour le service Sauvegarde Azure et les scénarios dans lesquels l’utilisation de points de terminaison privés contribue à maintenir la sécurité de vos ressources.

## <a name="before-you-start"></a>Avant de commencer

- Les points de terminaison privés ne peuvent être créés que pour les nouveaux coffres Recovery Services (qui ne contiennent pas d’éléments). Les points de terminaison privés doivent donc être créés avant que vous ne tentiez de protéger vos éléments en les plaçant dans un coffre.
- Un réseau virtuel peut contenir des points de terminaison privés pour plusieurs coffres Recovery Services. De son côté, un coffre Recovery Services peut être associé à plusieurs points de terminaison privés dans plusieurs réseaux virtuels. Toutefois, vous ne pouvez pas associer 1 coffre à plus de 12 points de terminaison privés.
- Lorsque vous associez un point de terminaison privé à un coffre, ce dernier est verrouillé. Cela veut dire qu’un coffre sera uniquement accessible (pour les sauvegardes et restaurations) sur les réseaux qui contiennent un point de terminaison privé associé à ce coffre. Si tous les points de terminaison privés associés au coffre sont supprimés, le coffre redevient accessible sur tous les réseaux.
- Une connexion de point de terminaison privée pour la sauvegarde utilise un total de 11 adresses IP privées dans votre sous-réseau, y compris celles utilisées par Sauvegarde Azure pour le stockage. Ce nombre peut être plus élevé (jusqu’à 25) pour certaines régions Azure. Nous vous suggérons donc d’avoir suffisamment d’adresses IP privées disponibles lorsque vous tentez de créer des points de terminaison privés pour la sauvegarde.
- Les coffres Recovery Services sont compatibles avec les services Sauvegarde Azure et Azure Site Recovery. Cependant, cet article traite uniquement de l’utilisation des points de terminaison privés pour le service Sauvegarde Azure.
- Les points de terminaison privés pour le service Sauvegarde Azure n’incluent pas l’accès à Azure Active Directory (Azure AD). Vous devez vous occuper de cela séparément. Par conséquent, les adresses IP et les noms de domaine complets requis pour le bon fonctionnement du service Azure AD dans une région doivent bénéficier d’une autorisation d’accès sortant sur le réseau sécurisé lors de la sauvegarde de bases de données dans des machines virtuelles Azure et de la sauvegarde à l’aide de l’agent MARS. Vous pouvez aussi utiliser des balises de groupe de sécurité réseau (NSG) et des balises du service Pare-feu Azure pour autoriser l’accès à Azure AD, le cas échéant.
- Les réseaux virtuels avec des stratégies réseau ne sont pas compatibles avec les points de terminaison privés. Vous devez donc [désactiver les stratégies réseau](../private-link/disable-private-endpoint-network-policy.md) avant de continuer.
- Vous devrez réinscrire le fournisseur de ressources Recovery Services auprès de l’abonnement si vous l’avez enregistré avant le 1er mai 2020. Pour réinscrire le fournisseur, accédez à votre abonnement dans le portail Azure, accédez à **Fournisseur de ressources** dans la barre de navigation de gauche, sélectionnez **Microsoft.RecoveryServices**, puis sélectionnez **Réinscrire**.
- Les [restaurations inter-régions](backup-create-rs-vault.md#set-cross-region-restore) pour les sauvegardes de bases de données SQL et SAP HANA ne sont pas prises en charge si les points de terminaison privés sont activés dans le coffre.
- Lorsque vous déplacez un coffre Recovery Services utilisant déjà des points de terminaison privés vers un nouveau locataire, vous devez le mettre à jour pour recréer et reconfigurer son identité managée et créer des points de terminaison privés si nécessaire (qui doivent se trouver dans le nouveau locataire). Si cela n’est pas fait, les opérations de sauvegarde et de restauration échoueront. De plus, toutes les autorisations de contrôle d'accès basé sur les rôles Azure (Azure RBAC) configurées dans l'abonnement devront être reconfigurées.

## <a name="recommended-and-supported-scenarios"></a>Scénarios recommandés et pris en charge

Même si des points de terminaison privés sont activés dans un coffre, ils sont uniquement utilisés pour la sauvegarde et la restauration des charges de travail SQL et SAP HANA en cas de sauvegarde de machines virtuelles Azure et de sauvegarde à l’aide de l’agent MARS. Vous pouvez également utiliser le coffre pour la sauvegarde d’autres charges de travail (cependant, cela ne nécessiterait pas de point de terminaison privé). En plus de la sauvegarde des charges de travail SQL et SAP HANA et de la sauvegarde à l’aide de l’agent MARS, les points de terminaison privés servent également à effectuer des récupérations de fichiers pour la sauvegarde de machines virtuelles Azure. Pour plus d’informations, voir le tableau suivant :

| Scénarios | Recommandations |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Sauvegarde de charges de travail dans une machine virtuelle Azure (SQL, SAP HANA) à l’aide de l’agent MARS | Nous vous recommandons d’utiliser des points de terminaison privés pour permettre la sauvegarde et la restauration sans avoir à ajouter à une liste d’autorisation les adresses IP ou les noms de domaine complets pour Sauvegarde Azure ou Stockage Azure depuis vos réseaux virtuels. Dans ce scénario, assurez-vous que les machines virtuelles hébergeant des bases de données SQL peuvent atteindre des adresses IP ou des noms de domaine complets Azure AD. |
| **Sauvegarde des machines virtuelles Azure**                                         | La sauvegarde de machine virtuelle ne vous oblige pas à autoriser l’accès à des adresses IP ou des noms de domaine complets. Ainsi, les points de terminaison privés ne sont pas requis pour la sauvegarde et la restauration des disques.  <br><br>   Toutefois, la récupération de fichiers à partir d’un coffre contenant des points de terminaison privés est limitée aux réseaux virtuels qui contiennent un point de terminaison privé associé au coffre. <br><br>    Lorsque vous utilisez des disques non managés ACL, assurez-vous que le compte de stockage contenant les disques autorise l’accès à des **services Microsoft approuvés** s’il est ACL. |
| **Sauvegarde Azure Files**                                      | Les sauvegardes du service Azure Files sont stockées dans le compte de stockage local. Ainsi, vous n’avez pas besoin de points de terminaison privés pour la sauvegarde et la restauration. |

>[!Note]
>Les points de terminaison privés ne sont pas pris en charge avec les serveurs DPM et MABS. 

## <a name="difference-in-network-connections-due-to-private-endpoints"></a>Différences dans les connexions réseau en raison des points de terminaison privés

Comme indiqué ci-dessus, les points de terminaison privés sont particulièrement utiles pour la sauvegarde des charges de travail (SQL, SAP HANA) dans des machines virtuelles Azure et des sauvegardes d’agent MARS. Dans tous les scénarios (avec ou sans points de terminaison privés), les extensions de charges de travail (pour la sauvegarde des instances SQL et SAP HANA s’exécutant dans les machines virtuelles Azure) et l’agent MARS effectuent des appels de connexion à AAD (aux noms de domaine complets mentionnés aux points 56 et 59 dans la section [Services communs Microsoft 365 et Office Online](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)).

En plus de ces connexions lorsque l’extension de charge de travail ou l’agent MARS est installé pour le coffre Recovery Services _sans points de terminaison privés_, la connectivité aux domaines suivants est également requise :

| Service | Noms de domaine |
| ------- | ------------ |
| Sauvegarde Azure  | *.backup.windowsazure.com |
| Stockage Azure | *.blob.core.windows.net <br> *.queue.core.windows.net <br> *.blob.storage.azure.net |

Lorsque l’extension de charge de travail ou l’agent MARS est installé pour le coffre Recovery Services avec un point de terminaison privé, les points de terminaison suivants sont atteints :

| Service | Noms de domaine |
| ------- | ------------ |
| Sauvegarde Azure  | `*.privatelink.<geo>.backup.windowsazure.com` |
| Stockage Azure | *.blob.core.windows.net <br> *.queue.core.windows.net <br> *.blob.storage.azure.net |

>[!Note]
>Dans le texte ci-dessus, `<geo>` fait référence au code de région (par exemple, **eus** pour la région USA Est et **ne** pour la région Europe Nord). Consultez les listes suivantes pour connaître les codes de régions :
>- [Tous les clouds publics](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
>- [Chine](/azure/china/resources-developer-guide#check-endpoints-in-azure)
>- [Allemagne](../germany/germany-developer-guide.md#endpoint-mapping)
>- [Gouvernement des États-Unis](../azure-government/documentation-government-developer-guide.md)

L’accès aux noms de domaine complets de stockage dans les deux scénarios est le même. Toutefois, pour un coffre Recovery Services avec une configuration de point de terminaison privé, la résolution de noms pour ceux-ci doit retourner une adresse IP privée. Pour ce faire, vous pouvez utiliser des zones de DNS privé en créant des entrées DNS pour le compte de stockage dans les fichiers d’hôte, ou en utilisant des redirecteurs conditionnels pour les DNS personnalisés avec des entrées DNS respectives. Les mappages d’adresses IP privées pour le compte de stockage sont répertoriés dans le panneau du point de terminaison privé pour le compte de stockage dans le portail.

>Les points de terminaison privés pour les objets blob et les files d’attente suivent un modèle d’attribution de nom standard. Ils commencent par  **\<the name of the private endpoint>_ecs** ou **\<the name of the private endpoint>_prot**, et se terminent respectivement par  **\_blob** et  **\_queue** .

Les points de terminaison du service Sauvegarde Azure sont modifiés pour les coffres activés pour les points de terminaison privés.  
Si vous avez configuré un serveur proxy DNS à l’aide de serveurs proxy et de pare-feu tiers, les noms de domaine ci-dessus doivent être autorisés et redirigés vers un DNS personnalisé (avec des mappages d’adresses IP privées) ou vers 168.63.129.16 avec une liaison de réseau virtuel vers une zone DNS privée avec ces mappages d’adresses IP privées.

L’exemple suivant montre le pare-feu Azure utilisé comme proxy DNS pour rediriger vers l’adresse 168.63.129.16 les requêtes de nom de domaine pour le coffre Recovery Services, l’objet BLOB, les files d’attente et AAD.

:::image type="content" source="./media/private-endpoints-overview/azure-firewall-used-as-dns-proxy-inline.png" alt-text="Diagramme montrant l’utilisation du pare-feu Azure comme proxy DNS pour rediriger les requêtes de nom de domaine." lightbox="./media/private-endpoints-overview/azure-firewall-used-as-dns-proxy-expanded.png":::

Pour plus d'informations, consultez la page sur [la création et l’utilisation de points de terminaisons privés](private-endpoints.md).

## <a name="network-connectivity-setup-for-vault-with-private-endpoints"></a>Configuration de la connectivité réseau pour le coffre avec des points de terminaison privés

Le point de terminaison privé pour Recovery Services est associé à une interface réseau (NIC) dotée d’une adresse IP privée. Pour que les connexions à un point de terminaison privé fonctionnent (acheminer tout le trafic vers le service via la structure principale Azure et limiter l’accès au service aux clients de votre VNet), il est nécessaire que tout le trafic de communication pour le service soit redirigé vers cette interface réseau. Pour ce faire, vous pouvez utiliser le DNS lié au réseau virtuel ou aux entrées du fichier hôte sur l’ordinateur sur lequel l’extension/agent est en cours d’exécution.

L’extension de sauvegarde de la charge de travail et l’agent MARS s’exécutent sur une machine virtuelle Azure dans un VNet ou une machine virtuelle locale appairée avec un VNet. Lorsqu’il est inscrit auprès d’un coffre de Recovery Services avec un point de terminaison privé joint à ce réseau virtuel, l’URL des services cloud Sauvegarde Azure pour l’extension et l’agent passe de `<azure_backup_svc >.<geo>.backup.windowsazure.com` à `<vault_id>.<azure_backup_svc>.privatelink.<geo>.backup`.windowsazure.com**.

>[!Note]
>Dans le texte ci-dessus, `<geo>` fait référence au code de région (par exemple, **eus** pour la région USA Est et **ne** pour la région Europe Nord). Consultez les listes suivantes pour connaître les codes de régions :
>- [Tous les clouds publics](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
>- [Chine](/azure/china/resources-developer-guide#check-endpoints-in-azure)
>- [Allemagne](../germany/germany-developer-guide.md#endpoint-mapping)
>- [Gouvernement des États-Unis](../azure-government/documentation-government-developer-guide.md)

Les URL modifiées sont spécifiques à un coffre.  Regardez la valeur `<vault_id>` dans le nom de l’URL. Seuls les agents et les extensions inscrits dans ce coffre peuvent communiquer avec le service Sauvegarde Azure via ces points de terminaison. Cela limite l’accès aux clients au sein de ce VNet. L’extension/l’agent communique via `*.privatelink.<geo>.backup.windowsazure.com`, qui doit résoudre l’adresse IP privée correspondante dans la carte d’interface réseau.

Lorsque le point de terminaison privé pour les coffres Recovery Services est créé via le portail Azure avec l’option **intégrer avec la zone DNS privée**, les entrées DNS requises pour les adresses IP privées pour les services de Sauvegarde Microsoft Azure (`*.privatelink.<geo>backup.windowsazure.com`) sont créées automatiquement chaque fois que la ressource est allouée. Dans le cas contraire, vous devez créer les entrées DNS manuellement pour ces noms de domaine complets dans le DNS personnalisé ou dans les fichiers hôtes.

Pour découvrir comment gérer manuellement des enregistrements DNS après la découverte de machines virtuelles pour le canal de communication BLOB/file d’attente, consultez la section [Enregistrements DNS pour les blobs et les files d’attente (uniquement pour les fichiers d’hôtes/serveurs DNS personnalisés) après la première inscription.](./private-endpoints.md#dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration) Pour la gestion manuelle des enregistrements DNS après la première sauvegarde de l’objet blob de compte de stockage de sauvegarde, consultez [enregistrements DNS pour les objets BLOB (uniquement pour les fichiers hôtes/serveurs DNS personnalisés) après la première sauvegarde](./private-endpoints.md#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup).

>Des adresses IP privées pour les noms de domaine complets sont disponibles dans le panneau du point de terminaison privé pour le point de terminaison privé créé pour le coffre Recovery Services.

Le diagramme suivant illustre le fonctionnement de la résolution lors de l’utilisation d’une zone DNS privée pour résoudre ces noms de domaine complets de service modifiés. 

:::image type="content" source="./media/private-endpoints-overview/use-private-dns-zone-to-resolve-modified-service-fqdns-inline.png" alt-text="Diagramme illustrant le fonctionnement de la résolution à l’aide d’une zone DNS privée pour résoudre les noms de domaine complets du service modifiés." lightbox="./media/private-endpoints-overview/use-private-dns-zone-to-resolve-modified-service-fqdns-expanded.png":::

En plus de la connexion aux services cloud de Sauvegarde Azure, l’extension et l’agent de charge de travail requièrent une connexion aux comptes de stockage Azure et à Azure Active Directory. L’extension de charge de travail en cours d’exécution sur la machine virtuelle Azure doit être connectée à au moins deux comptes de stockage. Le premier est utilisé comme canal de communication (via les messages de file d’attente) et le second pour le stockage des données de sauvegarde. L’agent MARS requiert l’accès à un compte de stockage utilisé pour le stockage des données de sauvegarde.

Dans le cas d’un coffre de point de terminaison privé activé, le service Sauvegarde Azure crée un point de terminaison privé pour ces comptes de stockage qui acheminent le trafic pour le canal de communication et les données de sauvegarde via le réseau principal Azure. Cela empêche tout trafic réseau lié au service Sauvegarde Azure de quitter le réseau virtuel.

Comme condition préalable, le coffre Recovery Services requiert des autorisations pour créer des points de terminaison privés supplémentaires dans le même groupe de ressources. Nous vous recommandons également de fournir au coffre Recovery Services les autorisations nécessaires pour créer des entrées DNS dans les zones DNS privées (privatelink.blob.core.windows.net, privatelink.queue.core.windows.net). Le coffre Recovery Services recherche les zones DNS privées dans les groupes de ressources où le VNet et le point de terminaison privé sont créés. S’il dispose des autorisations nécessaires pour ajouter des entrées DNS dans ces zones, celles-ci seront créées par le coffre. Sinon, vous devez laisser l’utilisateur les créer manuellement dans son DNS personnalisé ou dans une zone DNS privée liée au VNet.

>Vous pouvez consulter les mappages d’adresses IP privées dans le panneau du point de terminaison privé pour les objets BLOB et les files d’attente sur le portail.

Le diagramme suivant illustre le fonctionnement de la résolution de noms pour les comptes de stockage à l’aide d’une zone DNS privée.

:::image type="content" source="./media/private-endpoints-overview/name-resolution-works-for-storage-accounts-using-private-dns-zone-inline.png" alt-text="Diagramme suivant illustre le fonctionnement de la résolution de noms pour les comptes de stockage à l’aide d’une zone DNS privée." lightbox="./media/private-endpoints-overview/name-resolution-works-for-storage-accounts-using-private-dns-zone-expanded.png":::

## <a name="next-steps"></a>Étapes suivantes

- [Créer et utiliser des points de terminaison privés](private-endpoints.md).