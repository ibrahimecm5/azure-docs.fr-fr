---
title: FAQ sur Azure Bastion | Microsoft Docs
description: En savoir plus sur les questions fréquemment posées sur Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: cherylmc
ms.openlocfilehash: 251d1a034bafa3a9d77291be63fdc59145b2cc09
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131575804"
---
# <a name="azure-bastion-faq"></a>FAQ sur Azure Bastion

## <a name="faqs"></a>Foire aux questions

### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>Ai-je besoin d’une IP publique sur ma machine virtuelle pour me connecter via Azure Bastion ?

Non. Quand vous vous connectez à une machine virtuelle avec Azure Bastion, vous n’avez pas besoin d’une IP publique sur la machine virtuelle Azure à laquelle vous vous connectez. Le service Bastion va ouvrir la session/connexion RDP/SSH à votre machine virtuelle sur l’adresse IP privée de celle-ci, au sein de votre réseau virtuel.

### <a name="is-ipv6-supported"></a>IPv6 est-il pris en charge ?

IPv6 n’est actuellement pas pris en charge. Azure Bastion prend en charge IPv4 uniquement. Cela signifie que vous pouvez uniquement attribuer une IP publique IPv4 à votre ressource Bastion, et que vous pouvez utiliser votre Bastion pour vous connecter à des machines virtuelles cibles IPv4. Vous pouvez également utiliser votre Bastion pour vous connecter à des machines virtuelles cibles à double pile, mais vous ne pourrez envoyer et recevoir que du trafic IPv4 via Azure Bastion. 

### <a name="can-i-use-azure-bastion-with-azure-private-dns-zones"></a>Puis-je utiliser Azure Bastion avec Azure Private DNS Zones ?

Azure Bastion doit pouvoir communiquer avec certains points de terminaison internes pour se connecter correctement aux ressources cibles. Par conséquent, vous *pouvez* utiliser Azure Bastion avec des zones Azure DNS privé tant que le nom de zone que vous sélectionnez ne chevauche pas les noms de ces points de terminaison internes. Avant de déployer votre ressource Azure Bastion, assurez-vous que le réseau virtuel hôte n’est pas lié à une zone DNS privé dont le nom contient ce qui suit :
* core.windows.net
* azure.com

Notez que si vous utilisez une zone Azure DNS privé intégrée à un point de terminaison privé, le [nom de zone DNS recommandé](../private-link/private-endpoint-dns.md#azure-services-dns-zone-configuration) pour plusieurs services Azure se chevauche avec les noms répertoriés ci-dessus. L’utilisation d’Azure Bastion n’est *pas* prise en charge avec ces configurations.

L’utilisation d’Azure Bastion n’est pas non plus prise en charge avec les zones Azure DNS privé dans les clouds nationaux.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Ai-je besoin d’un client RDP ou SSH ?

Non. Vous n’avez pas besoin d’un client RDP ou SSH pour accéder par RDP/SSH à votre machine virtuelle Azure dans votre portail Azure. Utilisez le [portail Azure](https://portal.azure.com) pour obtenir un accès RDP/SSH à votre machine virtuelle directement dans le navigateur.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Ai-je besoin d’un agent exécuté sur la machine virtuelle Azure ?

Non. Vous n’avez pas besoin d’installer un agent ou un logiciel sur votre navigateur ou sur votre machine virtuelle Azure. Le service Bastion est sans agent et ne nécessite aucun logiciel supplémentaire pour RDP/SSH.

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Quelles sont les fonctionnalités prises en charge dans une session RDP ?

À l’heure actuelle, seul le copier-coller de texte est pris en charge. Les fonctionnalités telles que la copie de fichiers ne sont pas prises en charge. N’hésitez pas à nous faire part de vos commentaires sur les nouvelles fonctionnalités dans la [page des commentaires Azure Bastion](https://feedback.azure.com/d365community/forum/8ae9bf04-8326-ec11-b6e6-000d3a4f0789?c=c109f019-8326-ec11-b6e6-000d3a4f0789).

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Le durcissement Bastion fonctionne-t-il avec des machines virtuelles jointes à l’extension de machine virtuelle AADJ ?

Cette fonctionnalité ne fonctionne pas avec les ordinateurs joints à l’extension de machine virtuelle AADJ à l’aide d’utilisateurs Azure AD. Pour plus d’informations, consultez [Machines virtuelles Windows et Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements).

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Quels sont les navigateurs pris en charge ?

Le navigateur doit prendre en charge HTML 5. Utilisez le navigateur Microsoft Edge ou Google Chrome sous Windows. Pour Apple Mac, utilisez le navigateur Google Chrome. Microsoft Edge Chromium est également pris en charge sur Windows et Mac, respectivement.

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Quel est la tarification ?

Pour plus d’informations, consultez la [page relative aux prix appliqués](https://aka.ms/BastionHostPricing).

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Où Azure bastion stocke-t-il les données client ?

Azure Bastion ne déplace pas et ne stocke pas les données client en dehors de la région dans laquelle il est déployé.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Certains rôles sont-ils obligatoires pour accéder à une machine virtuelle ?

Pour établir une connexion, les rôles suivants sont nécessaires :

* Rôle de lecteur sur la machine virtuelle.
* Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle.
* Rôle de lecteur sur la ressource Azure Bastion.
* Rôle de lecteur sur le réseau virtuel (inutile s’il n’y a pas de réseau virtuel appairé).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Est-ce qu’Azure Bastion nécessite une licence d’accès client aux services Bureau à distance à des fins d’administration sur les machines virtuelles hébergées par Azure ?

Non, l’accès aux machines virtuelles Windows Server par Azure Bastion ne nécessite pas de [licence d’accès client aux services Bureau à distance](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) quand elles sont utilisées exclusivement à des fins d’administration.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Quelles sont les dispositions de clavier prises en charge pendant la session à distance Bastion ?

Azure Bastion prend actuellement en charge les dispositions de clavier suivantes à l’intérieur de la machine virtuelle :
* en-us-qwerty
* en-gb-qwerty
* de-ch-qwertz
* de-de-qwertz
* fr-be-azerty
* fr-fr-azerty
* fr-ch-qwertz
* hu-hu-qwertz
* it-it-qwerty
* ja-jp-qwerty
* pt-br-qwerty
* es-es-qwerty
* es-latam-qwerty
* sv-se-qwerty
* tr-tr-qwerty

Pour que la correspondance des touches soit correcte pour votre langue cible, vous devez définir la langue locale de votre ordinateur ou la langue distante de la machine virtuelle sur en-us-qwerty. Autrement dit, la langue locale de votre ordinateur doit être réglée sur l’anglais (États-Unis) tandis que la langue distante de votre machine virtuelle est réglée sur votre langue cible, ou vice versa. Vous pouvez ajouter la langue anglaise (États-Unis) à votre machine dans les paramètres de votre ordinateur.

### <a name="does-azure-bastion-support-timezone-configuration-or-timezone-redirection-for-target-vms"></a><a name="timezone"></a>Azure Bastion prend-il en charge la configuration du fuseau horaire ou la redirection des fuseaux horaires pour les machines virtuelles cibles ?

Actuellement, Azure Bastion ne prend pas en charge la redirection de fuseau horaire et n’est pas configurable par fuseau horaire.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Le routage défini par l’utilisateur (UDR) est-il pris en charge sur un sous-réseau Azure Bastion ?

Non. Le routage UDR n’est pas pris en charge sur un sous-réseau Azure Bastion.

Pour les scénarios qui incluent Azure Bastion et Pare-feu Azure/Appliance virtuelle réseau (NVA) dans le même réseau virtuel, vous n’avez pas besoin de forcer le trafic d’un sous-réseau Azure Bastion vers le Pare-feu Azure, car la communication entre Azure Bastion et vos machines virtuelles est privée. Pour plus d’informations, consultez [Accès aux machines virtuelles derrière le pare-feu Azure avec Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="can-i-upgrade-from-a-basic-sku-to-a-standard-sku"></a><a name="upgradesku"></a> Puis-je mettre à niveau une référence SKU De base vers une référence SKU Standard ?

Oui. Pour les étapes, voir [Mettre à niveau une référence SKU](upgrade-sku.md). Pour plus d’informations sur les références SKU, consultez l’article [Paramètres de configuration](configuration-settings.md#skus).

### <a name="can-i-downgrade-from-a-standard-sku-to-a-basic-sku"></a><a name="downgradesku"></a> Puis-je passer à une version antérieure d’une référence SKU Standard vers une référence SKU De base ?

Non. La rétrogradation d’une référence SKU Standard vers De base n’est pas prise en charge. Pour plus d’informations sur les références SKU, consultez l’article [Paramètres de configuration](configuration-settings.md#skus).

### <a name="can-i-deploy-multiple-azure-resources-in-my-azure-bastion-subnet"></a><a name="subnet"></a> Puis-je déployer plusieurs ressources Azure dans mon sous-réseau Azure Bastion ?

Non. Le sous-réseau Azure Bastion (*AzureBastionSubnet*) est réservé uniquement au déploiement de votre ressource Azure Bastion.

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Pourquoi le message d’erreur « Votre session a expiré » s’affiche avant le démarrage de la session Bastion ?

Une session ne doit être lancée qu’à partir du portail Azure. Connectez-vous au portail Azure, puis redémarrez votre session. Si vous accédez à l’URL directement à partir d’une autre session de navigateur ou d’un autre onglet, cette erreur est normale. Cela permet de sécuriser votre session, en évitant qu’elle soit accessible en dehors du portail Azure.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Comment gérer les échecs de déploiement ?

Passez en revue les messages d’erreur et [déclenchez une demande de support dans le portail Azure](../azure-portal/supportability/how-to-create-azure-support-request.md) en fonction des besoins. Les échecs de déploiement peuvent être dus aux [limites, quotas et contraintes de l’abonnement Azure](../azure-resource-manager/management/azure-subscription-service-limits.md). Plus précisément, les clients peuvent rencontrer une limite relative au nombre d’adresses IP publiques autorisées par abonnement, qui entraîne l’échec du déploiement Azure Bastion.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>Comment incorporer Azure Bastion dans mon plan de reprise d’activité après sinistre ?

Azure Bastion est déployé sur des réseaux virtuels ou des réseaux virtuels appairés, et il est associé à une région Azure. Il vous revient de déployer Azure Bastion sur un réseau virtuel de site de reprise d’activité après sinistre. En cas de défaillance d’une région Azure, basculez vos machines virtuelles vers la région de reprise d’activité. Ensuite, utilisez l’hôte Azure Bastion qui est déployé dans la région de reprise d’activité pour vous connecter aux machines virtuelles qui y sont désormais maintenant déployées.

## <a name="vnet-peering"></a><a name="peering">Peering VNet</a>

### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>Puis-je toujours déployer plusieurs hôtes bastions sur des réseaux virtuels appairés ?

Oui. Par défaut, un utilisateur voit l’hôte bastion qui est déployé dans le même réseau virtuel dans lequel réside la machine virtuelle. Toutefois, dans le menu **Connexion**, un utilisateur peut voir plusieurs hôtes bastions détectés sur des réseaux appairés. Il peut sélectionner l’hôte bastion qu’il préfère utiliser pour se connecter à la machine virtuelle déployée dans le réseau virtuel.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>Si mes réseaux virtuels appairés sont déployés dans des abonnements différents, la connectivité via Bastion fonctionnera-t-elle ?

Oui, la connectivité via Bastion continue à fonctionner pour les réseaux virtuels appairés sur différents abonnements pour un seul locataire. Elle n’est pas prise en charge pour les abonnements qui se trouvent dans deux locataires différents. Pour afficher Bastion dans le menu déroulant **Connexion**, l’utilisateur doit sélectionner les abonnements auxquels il a accès dans **Abonnement > abonnement global**.

:::image type="content" source="./media/bastion-faq/global-subscriptions.png" alt-text="Filtre des abonnements globaux." lightbox="./media/bastion-faq/global-subscriptions.png":::

### <a name="does-bastion-support-connectivity-to-azure-virtual-desktop"></a>Bastion prend-il en charge la connectivité à Azure Virtual Desktop ?
Non, la connectivité entre Bastion et Azure Virtual Desktop n’est pas prise en charge.

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>J’ai accès au réseau virtuel appairé, mais je ne peux pas y voir la machine virtuelle déployée.

Assurez-vous que l’utilisateur dispose d’un accès **en lecture** à la machine virtuelle et au réseau virtuel appairé. En outre, vérifiez sous IAM que l’utilisateur dispose d’un accès **en lecture** aux ressources suivantes :

* Rôle de lecteur sur la machine virtuelle.
* Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle.
* Rôle de lecteur sur la ressource Azure Bastion.
* Rôle de lecteur sur le réseau virtuel (inutile s’il n’y a pas de réseau virtuel appairé).

|Autorisations|Description|Type d'autorisation|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |Obtient un hôte Bastion|Action|
|Microsoft.Network/virtualNetworks/BastionHosts/action |Obtient des références d’hôte bastion dans un réseau virtuel.|Action|
|Microsoft.Network/virtualNetworks/bastionHosts/default/action|Obtient des références d’hôte bastion dans un réseau virtuel.|Action|
|Microsoft.Network/networkInterfaces/read|Obtient une définition d’interface réseau.|Action|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|Obtient une définition de la configuration de l’adresse IP de l’interface réseau.|Action|
|Microsoft.Network/virtualNetworks/read|Obtenir la définition de réseau virtuel.|Action|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|Obtient les références à toutes les machines virtuelles dans un sous-réseau de réseau virtuel.|Action|
|Microsoft.Network/virtualNetworks/virtualMachines/read|Obtient les références à toutes les machines virtuelles dans un réseau virtuel.|Action|
