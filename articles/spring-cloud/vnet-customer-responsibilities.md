---
title: Responsabilités du client pour l’exécution d’Azure Spring Cloud dans un réseau virtuel
description: Cet article décrit les responsabilités des clients exécutant Azure Spring Cloud dans un réseau virtuel.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: devx-track-java
ms.openlocfilehash: efcff17cd867deb885e8591db48dd23424a445a2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452430"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Responsabilités du client pour l’exécution d’Azure Spring Cloud dans un réseau virtuel

Cet article comprend les spécifications relatives à l’utilisation d’Azure Spring Cloud dans un réseau virtuel.

Quand Azure Spring Cloud est déployé dans votre réseau virtuel, il présente des dépendances sortantes sur des services extérieurs au réseau virtuel. Pour la gestion et à des fins opérationnelles, Azure Spring Cloud doit accéder à certains ports et noms de domaine complet (FQDN). Ces points de terminaison sont nécessaires pour communiquer avec le plan de gestion d’Azure Spring Cloud et pour télécharger et installer les principaux composants du cluster Kubernetes et les mises à jour de sécurité.

Par défaut, Azure Spring Cloud dispose d’un accès Internet sortant (sortie) illimité. Ce niveau d’accès réseau permet aux applications que vous exécutez d’accéder aux ressources externes en fonction des besoins. Si vous souhaitez restreindre le trafic de sortie, un nombre limité de ports et d’adresses doit être accessible pour les tâches de maintenance. La solution la plus simple pour sécuriser les adresses sortantes consiste à utiliser un dispositif de pare-feu permettant de contrôler le trafic sortant en fonction des noms de domaine. Le Pare-feu Azure, par exemple, peut restreindre le trafic HTTP et HTTPS sortant en fonction du nom FQDN de la destination. Vous pouvez également configurer les règles de pare-feu et de sécurité de votre choix pour autoriser ces ports et adresses requis.

## <a name="azure-spring-cloud-resource-requirements"></a>Conditions requises concernant les ressources Azure Spring Cloud

La liste suivante montre les ressources nécessaires aux services Azure Spring Cloud. En règle générale, vous ne devez pas modifier les groupes de ressources créés par Azure Spring Cloud, ni les ressources réseau sous-jacentes.

- Ne modifiez pas les groupes de ressources créés et appartenant à Azure Spring Cloud.
  - Par défaut, ces groupes de ressources se nomment `ap-svc-rt_[SERVICE-INSTANCE-NAME]_[REGION]*` et `ap_[SERVICE-INSTANCE-NAME]_[REGION]*`.
  - N’empêchez pas Azure Spring Cloud de mettre à jour les ressources de ces groupes de ressources.
- Ne modifiez pas les sous-réseaux utilisés par Azure Spring Cloud.
- Ne créez pas plusieurs instances de service Azure Spring Cloud dans le même sous-réseau.
- Quand vous utilisez un pare-feu pour contrôler le trafic, ne bloquez pas le trafic de sortie suivant vers les composants Azure Spring Cloud qui assurent le fonctionnement, la maintenance et la prise en charge de l’instance de service.

## <a name="azure-spring-cloud-network-requirements"></a>Conditions requises concernant le réseau d’Azure Spring Cloud

| Point de terminaison de destination                                         | Port             | Utilisation                                       | Remarque                                                         |
| ------------------------------------------------------------ | ---------------- | ----------------------------------------- | ------------------------------------------------------------ |
| \*:1194 *ou* [ÉtiquetteService](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:1194 | UDP:1194         | Gestion sous-jacente des clusters Kubernetes. |                                                              |
| \*:443 *ou* [ÉtiquetteService](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:443 | TCP:443          | Management des services d'Azure Spring Cloud.    | Les informations de l’instance de service « requiredTraffics » peuvent être connues dans la charge utile de la ressource, sous la section « networkProfile ». |
| \*:9000 *ou* [ÉtiquetteService](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:9000 | TCP:9000         | Gestion sous-jacente des clusters Kubernetes. |                                                              |
| \*:123 *ou* ntp.ubuntu.com:123                                | UDP:123          | Synchronisation de l’heure NTP sur les nœuds Linux.  |                                                              |
| \*.azure.io:443 *ou* [ÉtiquetteService](../virtual-network/service-tags-overview.md#available-service-tags) - AzureContainerRegistry:443 | TCP:443          | Azure Container Registry.                 | Peut être remplacé par l’activation du [point de terminaison de service d’*Azure Container Registry* dans le réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md). |
| \*.core.windows.net:443 et \*.core.windows.net:445 *ou* [ÉtiquetteService](../virtual-network/service-tags-overview.md#available-service-tags) - Storage:443 et Storage:445 | TCP:443, TCP:445 | Azure Files                        | Peut être remplacé par l’activation du [point de terminaison de service de *Stockage Azure* dans le réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md). |
| \*.servicebus.windows.net:443 *ou* [ÉtiquetteService](../virtual-network/service-tags-overview.md#available-service-tags) - EventHub:443 | TCP:443          | Azure Event Hubs.                          | Peut être remplacé par l’activation du [point de terminaison de service d’*Azure Event Hubs* dans le réseau virtuel](../virtual-network/virtual-network-service-endpoints-overview.md). |

## <a name="azure-spring-cloud-fqdn-requirementsapplication-rules"></a>Conditions requises/règles d’application concernant le nom de domaine complet d’Azure Spring Cloud

Pare-feu Azure fournit la balise de nom de domaine complet (FQDN) **AzureKubernetesService** pour simplifier les configurations suivantes :

| Nom FQDN de destination                  | Port      | Utilisation                                                          |
| --------------------------------- | --------- | ------------------------------------------------------------ |
| <i>*.azmk8s.io</i>                | HTTPS:443 | Gestion sous-jacente des clusters Kubernetes.                    |
| <i>mcr.microsoft.com</i>          | HTTPS:443 | Microsoft Container Registry (MCR).                          |
| <i>*.cdn.mscr.io</i>              | HTTPS:443 | Stockage de MCR s’appuyant sur le réseau de distribution de contenu.                         |
| <i>*.data.mcr.microsoft.com</i>   | HTTPS:443 | Stockage de MCR s’appuyant sur le réseau de distribution de contenu.                         |
| <i>management.azure.com</i>       | HTTPS:443 | Gestion sous-jacente des clusters Kubernetes.                    |
| <i>*login.microsoftonline.com</i> | HTTPS:443 | Authentification Azure Active Directory.                       |
| <i>*login.microsoft.com</i>       | HTTPS:443 | Authentification Azure Active Directory.                       |
| <i>packages.microsoft.com</i>     | HTTPS:443 | Référentiel de packages Microsoft.                               |
| <i>acs-mirror.azureedge.net</i>   | HTTPS:443 | Référentiel requis pour installer les fichiers binaires requis comme kubenet et Azure CNI. |
| *mscrl.microsoft.com*<sup>1</sup> | HTTPS:80  | Chemins de chaîne de certificats Microsoft requis.                  |
| *crl.microsoft.com*<sup>1</sup>   | HTTPS:80  | Chemins de chaîne de certificats Microsoft requis.                  |
| *crl3.digicert.com*<sup>1</sup>   | HTTPS:80  | Chemins de chaîne de certificats TLS/SSL tiers.                 |

<sup>1</sup> Notez que ces FQDN (noms de domaine complets) ne sont pas inclus dans l’étiquette FQDN.


## <a name="azure-spring-cloud-optional-fqdn-for-third-party-application-performance-management"></a>Nom de domaine complet facultatif d’Azure Spring Cloud pour la gestion des performances des applications tierces

Pare-feu Azure fournit la balise de nom de domaine complet (FQDN) **AzureKubernetesService** pour simplifier les configurations suivantes :

| Nom FQDN de destination                   | Port       | Utilisation                                                          |
| ---------------------------------- | ---------- | ------------------------------------------------------------ |
| <i>collector*.newrelic.com</i>     | TCP:443/80 | Réseaux requis des agents APM de New Relic de la région États-Unis ; voir aussi les [réseaux d’agents APM](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |
| <i>collector*.eu01.nr-data.net</i> | TCP:443/80 | Réseaux requis des agents APM de New Relic de la région Europe ; voir aussi les [réseaux d’agents APM](https://docs.newrelic.com/docs/using-new-relic/cross-product-functions/install-configure/networks/#agents). |
| <i>*.live.dynatrace.com</i>        | TCP:443    | Réseau d’agents Dynatrace APM nécessaire.                    |
| <i>*.live.ruxit.com</i>            | TCP:443    | Réseau d’agents Dynatrace APM nécessaire.                    |
| <i>*.saas.appdynamics.com</i>      | TCP:443/80 | Réseau d’agents APM AppDynamics nécessaire. Consultez également les informations relatives aux [domaines et plages d’adresses IP SaaS](https://docs.appdynamics.com/display/PAA/SaaS+Domains+and+IP+Ranges). |

## <a name="see-also"></a>Voir aussi

* [Accéder à votre application sur un réseau privé](access-app-virtual-network.md)
* [Exposer des applications à l’aide d’Application Gateway et de Pare-feu Azure](expose-apps-gateway-azure-firewall.md)
