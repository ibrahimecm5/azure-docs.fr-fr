---
title: Résoudre des problèmes de réseau avec un registre
description: Symptômes, causes et résolution de problèmes courants lors de l’accès à un registre de conteneurs Azure dans un réseau virtuel ou derrière un pare-feu
ms.topic: article
ms.date: 05/10/2021
ms.openlocfilehash: 4d3962a99fd462cfe3b613a4f0a9409b309b462f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287131"
---
# <a name="troubleshoot-network-issues-with-registry"></a>Résoudre des problèmes de réseau avec un registre

Cet article vous aide à résoudre les problèmes que vous pouvez rencontrer lors de l’accès à un registre de conteneurs Azure dans un réseau virtuel ou derrière un pare-feu ou serveur proxy. 

## <a name="symptoms"></a>Symptômes

Peuvent inclure un ou plusieurs des symptômes suivants :

* Impossibilité d’envoyer ou d’extraire des images et affichage de l’erreur `dial tcp: lookup myregistry.azurecr.io`.
* Impossibilité d’envoyer ou d’extraire des images et affichage de l’erreur `Client.Timeout exceeded while awaiting headers`.
* Impossibilité d’envoyer ou d’extraire des images et affichage de l’erreur Azure CLI `Could not connect to the registry login server`.
* Impossibilité d’extraire des images du registre vers Azure Container Service ou un autre service Azure.
* Impossibilité d’accéder au registre derrière un proxy HTTPS et réception de l’erreur `Error response from daemon: login attempt failed with status: 403 Forbidden` ou `Error response from daemon: Get <registry>: proxyconnect tcp: EOF Login failed`
* Impossibilité de configurer les paramètres de réseau virtuel et réception de l’erreur `Failed to save firewall and virtual network settings for container registry`
* Impossibilité de consulter ou d’afficher des paramètres du registre dans le portail Azure ou de gérer le registre à l’aide d’Azure CLI.
* Impossibilité d’ajouter ou de modifier des paramètres de réseau virtuel ou des règles d’accès public.
* La solution ACR Tasks ne peut pas envoyer ou extraire des images.
* Microsoft Defender pour le cloud ne peut pas analyser les images dans le registre, ou les résultats d’analyse n’apparaissent pas dans Microsoft Defender pour le cloud
* Vous recevez une erreur `host is not reachable` lorsque vous tentez d’accéder à un registre configuré avec un point de terminaison privé.

## <a name="causes"></a>Causes

* Un pare-feu ou proxy de client empêche l’accès : [solution](#configure-client-firewall-access).
* Les règles d’accès au réseau public dans le registre empêchent l’accès : [solution](#configure-public-access-to-registry).
* La configuration du réseau virtuel ou du point de terminaison privé empêche l’accès : [solution](#configure-vnet-access)
* Vous tentez d’intégrer Microsoft Defender pour le cloud ou certains autres services Azure avec un registre qui a un point de terminaison privé, un point de terminaison de service ou des règles d’accès aux IP publiques : [solution](#configure-service-access)

## <a name="further-diagnosis"></a>Diagnostics plus poussés 

Exécutez la commande [az acr check-health](/cli/azure/acr#az_acr_check_health) pour obtenir des informations supplémentaires sur l’intégrité de l’environnement de registre, et éventuellement l’accès à un registre cible. Par exemple, diagnostiquez certains problèmes de connectivité ou de configuration de réseau. 

Consultez [Vérifier l’intégrité d’un registre de conteneurs Azure](container-registry-check-health.md) pour obtenir des exemples de commandes. Si des erreurs sont signalées, examinez les [Informations de référence sur les erreurs](container-registry-health-error-reference.md) et les sections suivantes pour obtenir les solutions recommandées.

Si vous rencontrez des problèmes lors de l’utilisation d’un service Azure Kubernetes avec un registre intégré, exécutez la commande [az aks check-acr ](/cli/azure/aks#az_aks_check_acr) pour valider que le cluster AKS peut atteindre le registre.

> [!NOTE]
> Certains symptômes de connectivité réseau peuvent également se produire en cas de problème avec l’authentification ou l’autorisation du registre. Consultez [Résoudre les problèmes de connexion au registre](container-registry-troubleshoot-login.md).

## <a name="potential-solutions"></a>Solutions possibles

### <a name="configure-client-firewall-access"></a>Configurer l’accès au pare-feu du client

Pour accéder à un registre à partir d’un serveur proxy ou d’un emplacement situé derrière un pare-feu de client, configurez des règles de pare-feu pour accéder aux points de terminaison REST public et de données du registre. Si des [points de terminaison de données dédiés](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) sont activés, vous avez besoin de règles pour accéder aux éléments suivants :

* Point de terminaison REST : `<registryname>.azurecr.io`
* Point de terminaison de données : `<registry-name>.<region>.data.azurecr.io`

Pour un registre géorépliqué, configurez l’accès au point de terminaison de données pour chaque réplica régional.

Derrière un proxy HTTPS, vérifiez que vos client et démon Docker sont configurés pour le comportement du proxy. Si vous modifiez vos paramètres de proxy pour le démon Docker, assurez-vous de redémarrer le démon. 

Les journaux de ressources de registre dans la table ContainerRegistryLoginEvents peuvent aider à diagnostiquer une tentative de connexion bloquée.

Liens connexes :

* [Configurer des règles pour accéder à un registre de conteneurs Azure derrière un pare-feu](container-registry-firewall-access-rules.md)
* [Configuration du proxy HTTP/HTTPS](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Géoréplication Azure Container Registry](container-registry-geo-replication.md)
* [Surveiller Azure Container Registry](monitor-service.md)

### <a name="configure-public-access-to-registry"></a>Configurer un accès public à un registre

Si vous accédez à un registre via Internet, vérifiez que le registre autorise l’accès au réseau public à partir de votre client. Par défaut, un registre de conteneurs Azure permet d’accéder aux points de terminaison de registre publics à partir de tous les réseaux. Un registre peut limiter l’accès à des réseaux sélectionnés ou à des adresses IP sélectionnées. 

Si le registre est configuré pour un réseau virtuel avec un point de terminaison de service, la désactivation de l’accès au réseau public a également pour effet de désactiver l’accès via le point de terminaison de service. Si votre registre est configuré pour un réseau virtuel avec une liaison privée, les règles de réseau IP ne s’appliquent pas aux points de terminaison privés du registre. 

Liens connexes :

* [Configurer des règles de réseau IP public](container-registry-access-selected-networks.md)
* [Connexion privée à un registre de conteneurs Azure à l’aide d’Azure Private Link](container-registry-private-link.md)
* [Restreindre l’accès à un registre de conteneurs à l’aide d’un point de terminaison de service dans un réseau virtuel Azure](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>Configurer l’accès au réseau virtuel

Vérifiez que le réseau virtuel est configuré avec un point de terminaison privé pour une liaison privée ou un point de terminaison de service (préversion). Actuellement, un point de terminaison Azure Bastion n’est pas pris en charge.

Si un point de terminaison privé est configuré, vérifiez que DNS résout le nom de domaine complet public du registre, par exemple *myregistry.azurecr.io* à l’adresse IP privée du registre.

  * Exécutez la commande [az acr check-health](/cli/azure/acr#az_acr_check_health) avec le paramètre `--vnet` pour vérifier le routage DNS vers le point de terminaison privé dans le réseau virtuel.
  * Utilisez un utilitaire réseau comme `dig` ou `nslookup` pour la recherche DNS. 
  * Vérifiez que les [enregistrements DNS sont configurés](container-registry-private-link.md#dns-configuration-options) pour le nom de domaine complet du registre et pour chacun des noms de domaine complets du point de terminaison de données. 

Examinez les règles de groupe de sécurité réseau et les balises de service utilisées pour limiter le trafic à partir d’autres ressources du réseau vers le registre. 

Si un point de terminaison de service est configuré pour le registre, vérifiez qu’une règle de réseau est ajoutée au registre qui autorise l’accès à partir de ce sous-réseau. Le point de terminaison de service prend uniquement en charge l’accès à partir des machines virtuelles et de clusters AKS dans le réseau.

Si vous souhaitez restreindre l’accès au registre à l’aide d’un réseau virtuel dans un autre abonnement Azure, veillez à inscrire le fournisseur de ressources `Microsoft.ContainerRegistry` dans cet abonnement. [Inscrivez le fournisseur de ressources](../azure-resource-manager/management/resource-providers-and-types.md) pour Azure Container Registry à l’aide du portail Azure, de l’interface de ligne de commande Azure ou d’autres outils Azure.

Si le Pare-feu Azure ou une solution similaire sont configurés dans le réseau, vérifiez que le trafic sortant à partir d’autres ressources telles qu’un cluster AKS est activé pour atteindre les points de terminaison du registre.

Liens connexes :

* [Connexion privée à un registre de conteneurs Azure à l’aide d’Azure Private Link](container-registry-private-link.md)
* [Résoudre les problèmes de connectivité d’Azure Private Endpoint](../private-link/troubleshoot-private-endpoint-connectivity.md)
* [Restreindre l’accès à un registre de conteneurs à l’aide d’un point de terminaison de service dans un réseau virtuel Azure](container-registry-vnet.md)
* [Règles de réseau sortantes et noms FQDN requis pour les clusters AKS](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: Débogage de résolution DNS](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Balises de service du réseau virtuel](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Configurer l’accès au service

Actuellement, l’accès à un registre de conteneurs ayant des restrictions réseau n’est pas autorisé à partir de plusieurs services Azure :

* Microsoft Defender pour le cloud ne peut pas effectuer l’[analyse de vulnérabilité des images](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) dans un registre qui restreint l’accès aux points de terminaison privés, aux sous-réseaux sélectionnés ou aux adresses IP. 
* Les ressources de certains services Azure ne peuvent pas accéder à un registre de conteneurs ayant des restrictions réseau, y compris Azure App Service et Azure Container Instances.

Si l’accès ou l’intégration de ces services Azure à votre registre de conteneurs est nécessaire, supprimez la restriction réseau. Par exemple, supprimez les points de terminaison privés du registre, ou supprimez ou modifiez les règles d’accès publiques du registre.

Depuis janvier 2021, vous pouvez configurer un registre dont l’accès réseau est restreint pour [autoriser l’accès](allow-access-trusted-services.md) à partir de certains services de confiance.

Liens connexes :

* [Analyse d’image Azure Container Registry par Microsoft Defender pour les registres de conteneurs](../security-center/defender-for-container-registries-introduction.md)
* Fournir des [commentaires](https://feedback.azure.com/d365community/idea/cbe6351a-0525-ec11-b6e6-000d3a4f07b8)
* [Permettre à des services de confiance d’accéder en toute sécurité à un registre de conteneurs ayant un accès réseau restreint](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>Dépannage avancé

Si la [collecte des journaux de ressources](monitor-service.md) est activée dans le registre, consultez le journal ContainterRegistryLoginEvents. Ce journal stocke les événements d’authentification et l’état, y compris l’identité et l’adresse IP entrantes. Interrogez le journal sur les [échecs d’authentification du registre](monitor-service.md#registry-authentication-failures). 

Liens connexes :

* [Journaux pour l’évaluation et l’audit de diagnostics](./monitor-service.md)
* [FAQ sur le registre de conteneurs](container-registry-faq.yml)
* [Ligne de base de sécurité Azure pour Azure Container Registry](security-baseline.md)
* [Meilleures pratiques pour Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Étapes suivantes

Si vous ne résolvez pas votre problème ici, consultez les options suivantes.

* Les autres rubriques de dépannage du registre sont les suivantes :
  * [Résoudre les problèmes de connexion au registre](container-registry-troubleshoot-login.md) 
  * [Résoudre les problèmes de performances de registre](container-registry-troubleshoot-performance.md)
* Options de [support de la communauté](https://azure.microsoft.com/support/community/)
* [Microsoft Q&A](/answers/products/)
* [Ouvrir un ticket de support](https://azure.microsoft.com/support/create-ticket/)
