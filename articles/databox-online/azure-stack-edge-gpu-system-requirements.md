---
title: Configuration système requise pour Microsoft Azure Stack Edge | Microsoft Docs
description: Découvrez la configuration système pour votre solution Microsoft Azure Stack Edge et pour les clients se connectant à Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: b79f878b7149bb41732f924c657f711f9bdf3128
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033111"
---
# <a name="system-requirements-for-azure-stack-edge-pro-with-gpu"></a>Configuration système exigée pour Azure Stack Edge Pro avec GPU 

Cet article décrit la configuration système requise pour votre solution Microsoft Azure Stack Edge Pro GPU et pour les clients se connectant à Azure Stack Edge Pro. Nous vous recommandons de lire attentivement les informations suivantes avant de déployer votre solution Azure Stack Edge Pro. Reportez-vous aussi souvent que nécessaire à ces informations pendant le déploiement, et après, pour son fonctionnement.

La configuration système requise pour Azure Stack Edge Pro inclut ce qui suit :

- **Configuration logicielle pour les hôtes** : décrit les plateformes prises en charge, les navigateurs pour l’interface utilisateur de configuration locale, les clients SMB et les exigences supplémentaires pour les clients qui accèdent à l’appareil.
- **Configuration réseau pour l’appareil** : fournit des informations sur la configuration réseau nécessaire au fonctionnement de l’appareil physique.

## <a name="supported-os-for-clients-connected-to-device"></a>Systèmes d’exploitation pris en charge pour les clients connectés à l’appareil

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocoles pris en charge pour l’accès des clients à l’appareil

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-azure-storage-accounts"></a>Comptes Stockage Azure pris en charge

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-edge-storage-accounts"></a>Comptes de stockage Edge pris en charge

Les comptes de stockage Edge suivants sont pris en charge avec l’interface REST de l’appareil. Les comptes de stockage Edge sont créés sur l’appareil. Pour plus d’informations, consultez [Comptes de stockage Edge](azure-stack-edge-gpu-manage-storage-accounts.md#about-edge-storage-accounts).

|Type  |Compte de stockage  |Commentaires  |
|---------|---------|---------|
|standard     |GPv1 : Objet blob de blocs         |         |

*Les objets blob de pages et Azure Files ne sont actuellement pas pris en charge.

## <a name="supported-local-azure-resource-manager-storage-accounts"></a>Comptes de stockage Azure Resource Manager locaux pris en charge

Ces comptes de stockage sont créés via les API locales de l’appareil lorsque vous vous connectez à une instance Azure Resource Manager locale. Les comptes de stockage suivants sont pris en charge :

|Type  |Compte de stockage  |Commentaires  |
|---------|---------|---------|
|standard     |GPv1 : Objet blob de blocs, Objet blob de pages        | Le type SKU est Standard_LRS       |
|Premium     |GPv1 : Objet blob de blocs, Objet blob de pages        | Le type SKU est Premium_LRS        |


## <a name="supported-storage-types"></a>Types de stockage pris en charge

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>Navigateurs pris en charge pour l’interface utilisateur web locale

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Configuration requise du port réseau

### <a name="port-requirements-for-azure-stack-edge-pro"></a>Configuration des ports requise pour Azure Stack Edge Pro

Le tableau ci-dessous répertorie les ports qui doivent être ouverts dans votre pare-feu pour autoriser le trafic SMB, cloud ou de gestion. Dans ce tableau, *entrée* ou *entrant* représente la direction à partir de laquelle les requêtes clientes entrantes accèdent à votre appareil. *Sortie* ou *sortant* représente la direction vers laquelle votre appareil Azure Stack Edge Pro envoie des données de façon externe, au-delà du déploiement : par exemple, sortant vers Internet.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Configuration requise du port pour IoT Edge

Azure IoT Edge permet les communications sortantes entre un appareil Edge local et le cloud Azure au moyen des protocoles IoT Hub pris en charge. Les communications entrantes sont uniquement requises pour des scénarios spécifiques où Azure IoT Hub a besoin d’envoyer (push) des messages à l’appareil Azure IoT Edge (par exemple, messagerie cloud vers appareil).

Utilisez le tableau suivant pour configurer les ports des serveurs hébergeant le runtime Azure IoT Edge :

| N° de port | Entrant ou sortant | Étendue de ports | Obligatoire | Guidance |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Sortie       | WAN        | Oui      | Sortie ouverte pour le déploiement de IoT Edge. Cette configuration est requise en cas d’utilisation de scripts manuels ou du service Azure IoT Device Provisioning.|

Pour plus d'informations, consultez [Règles de configuration du pare-feu et des ports pour le déploiement d’IoT Edge](../iot-edge/troubleshoot.md).


### <a name="port-requirements-for-kubernetes-on-azure-stack-edge"></a>Configuration de port requise pour Kubernetes sur Azure Stack Edge

| N° de port | Entrant ou sortant | Étendue de ports | Obligatoire | Guidance |
|----------|-----------|------------|----------|----------|
| TCP 31000 (HTTPS)| Dans       | LAN        | Dans certains cas. <br> Consultez les remarques.      |Ce port est requis uniquement si vous vous connectez au tableau de bord Kubernetes pour surveiller votre appareil. |
| TCP 6443 (HTTPS)| Dans       | LAN        | Dans certains cas. <br> Consultez les remarques.       |Ce port est requis par le serveur d’API Kubernetes uniquement si vous utilisez `kubectl` pour accéder à votre appareil. |

> [!IMPORTANT]
> Si le pare-feu de votre centre de données restreint ou filtre le trafic en fonction des adresses IP ou MAC sources, assurez-vous que les adresses IP de calcul (adresses IP des nœuds Kubernetes) et les adresses MAC figurent dans la liste des adresses autorisées. Les adresses MAC peuvent être spécifiées en exécutant la cmdlet `Set-HcsMacAddressPool` sur l’interface PowerShell de l’appareil.

## <a name="url-patterns-for-firewall-rules"></a>Modèles d’URL pour règles de pare-feu

Les administrateurs réseau peuvent souvent configurer des règles de pare-feu avancées basées sur des modèles d’URL afin de filtrer le trafic entrant et sortant. Votre appareil Azure Stack Edge Pro et le service dépendent d’autres applications Microsoft comme Azure Service Bus, Azure Active Directory Access Control, des comptes de stockage et des serveurs Microsoft Update. Les modèles d’URL associés à ces applications peuvent être utilisés pour configurer des règles de pare-feu. Il est important de comprendre que les modèles d’URL associés à ces applications peuvent changer. Ces modifications impliquent que l’administrateur réseau contrôle et mette à jour les règles de pare-feu pour votre appareil Azure Stack Edge Pro, si nécessaire.

Dans la plupart des cas, nous vous recommandons de définir librement les règles de pare-feu pour le trafic sortant en fonction des adresses IP fixes Azure Stack Edge Pro. Toutefois, vous pouvez utiliser les informations ci-dessous pour définir les règles de pare-feu avancées qui sont nécessaires à la création d’environnements sécurisés.

> [!NOTE]
> - Les adresses IP d’appareil (sources) doivent toujours être définies sur l’ensemble des interfaces réseau activées pour le cloud.
> - Les adresses IP de destination doivent être définies sur les [plages d’adresses IP Azure Datacenter](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Modèles d’URL pour la fonctionnalité de passerelle

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Modèles d’URL pour la fonctionnalité de calcul

| Modèle d’URL                      | Composant ou fonctionnalité                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Registre de conteneurs Microsoft (obligatoire)               |
| https://\*.azurecr.io                     | Registres de conteneurs personnels et tiers (facultatif) | 
| https://\*.azure-devices.net              | Accès IoT Hub (obligatoire)                             | 
| https://\*.docker.com              | StorageClass (obligatoire)                             | 

### <a name="url-patterns-for-monitoring"></a>Modèles d’URL pour la surveillance

Ajoutez les modèles d’URL suivants pour Azure Monitor si vous utilisez la version en conteneur de l’agent Log Analytics pour Linux.

| Modèle d’URL | Port | Composant ou fonctionnalité |
|-------------|-------------|----------------------------|
| https://\*ods.opinsights.azure.com | 443 | Ingestion de données |
| https://\*.oms.opinsights.azure.com | 443 | Intégration d’Operations Management Suite (OMS) |
| https://\*.dc.services.visualstudio.com | 443 | Télémétrie de l’agent qui utilise Azure Public Cloud Application Insights |

Pour plus d’informations, consultez [Configuration requise du pare-feu réseau pour la surveillance des insights de conteneur](../azure-monitor/containers/container-insights-onboard.md#network-firewall-requirements).

### <a name="url-patterns-for-gateway-for-azure-government"></a>Modèles d’URL pour la passerelle pour Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Modèles d’URL pour le calcul pour Azure Government

| Modèle d’URL                      | Composant ou fonctionnalité                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Registre de conteneurs Microsoft (obligatoire)               |
| https://\*.azure-devices.us              | Accès IoT Hub (obligatoire)           |
| https://\*.azurecr.us                    | Registres de conteneurs personnels et tiers (facultatif) | 

### <a name="url-patterns-for-monitoring-for-azure-government"></a>Modèles d’URL pour la surveillance avec Azure Government

Ajoutez les modèles d’URL suivants pour Azure Monitor si vous utilisez la version en conteneur de l’agent Log Analytics pour Linux.

| Modèle d’URL | Port | Composant ou fonctionnalité |
|-------------|-------------|----------------------------|
| https://\*ods.opinsights.azure.us | 443 | Ingestion de données |
| https://\*.oms.opinsights.azure.us | 443 | Intégration d’Operations Management Suite (OMS) |
| https://\*.dc.services.visualstudio.com | 443 | Télémétrie de l’agent qui utilise Azure Public Cloud Application Insights |


## <a name="internet-bandwidth"></a>Bande passante Internet

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Considérations relatives au dimensionnement de la capacité de calcul

Utilisez votre expérience lors des phases de développement et de test de votre solution afin de vous assurer que votre appareil Azure Stack Edge Pro dispose d’une capacité suffisante et que vous en tirez des performances optimales.

Tenez compte des facteurs suivants :

- **Caractéristiques de l’objet conteneur**. Posez-vous les questions suivantes :

    - Quelle est l’empreinte mémoire de votre conteneur ? Quelles quantités de mémoire, de stockage et de processeur votre conteneur consomme-t-il ?
    - Combien de conteneurs votre charge de travail compte-t-elle ? Vous pourriez avoir un grand nombre de conteneurs légers ou seulement quelques-uns gourmands en ressources.
    - Quelles sont les ressources allouées à ces conteneurs, et quelles sont les ressources qu’ils consomment (l’empreinte) ?
    - Combien de couches vos conteneurs partagent-ils ? Les images conteneur sont constituées d’un ensemble de fichiers organisés en pile de couches. Pour votre image conteneur, déterminez le nombre de couches et leurs tailles respectives pour calculer la consommation des ressources.
    - Existe-t-il des conteneurs inutilisés ? Un conteneur arrêté occupe toujours de l’espace disque.
    - En quels langage vos conteneurs sont-ils écrits ?
- **Taille des données traitées**. Quelle quantité de données vos conteneurs traiteront-ils ? Ces données consommeront-elles de l’espace disque, ou seront-elles traitées en mémoire ?
- **Performances attendues**. Quelles sont les caractéristiques de performances souhaitées de votre solution ? 

Pour comprendre et affiner les performances de votre solution, vous pouvez utiliser :

- Les métriques de calcul disponibles dans le portail Azure. Accédez à votre ressource Azure Stack Edge, puis à **Supervision > Métriques**. Examinez les valeurs **Computing en périphérie - Utilisation de la mémoire** et **Computing en périphérie - Pourcentage du processeur** pour comprendre les ressources disponibles et comment elles sont utilisées.
- Pour superviser les modules de calcul et résoudre les problèmes associés, accédez à [Déboguer les problèmes Kubernetes](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge).

Pour finir, veillez à tester votre solution sur votre jeu de données et à quantifier les performances sur Azure Stack Edge Pro avant le déploiement en production.

## <a name="next-step"></a>Étape suivante

- [Déployer votre Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)
