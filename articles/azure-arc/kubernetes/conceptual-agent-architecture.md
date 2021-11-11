---
title: Architecture des agents Kubernetes compatibles avec Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Cet article offre une vue d’ensemble architecturale des agents Kubernetes compatibles avec Azure Arc
keywords: Kubernetes, Arc, Azure, conteneurs
ms.openlocfilehash: 6bb1b035e1c64e82f89804928854ca019ad22201
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450834"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Architecture des agents Kubernetes compatibles avec Azure Arc

Tel quel, [Kubernetes](https://kubernetes.io/) peut déployer des charges de travail conteneurisées de manière cohérente dans des environnements hybrides et à plusieurs clouds. Toutefois, Kubernetes avec Azure Arc opère en tant que plan de contrôle cohérent centralisé gérant la stratégie, la gouvernance et la sécurité dans des environnements hétérogènes. Cet article fournit :

* Présentation architecturale de la connexion d’un cluster à Azure Arc.
* Modèle de connectivité suivi par les agents.
* Description des données échangées entre l’environnement de cluster et Azure.

## <a name="deploy-agents-to-your-cluster"></a>Déployer des agents sur votre cluster

La plupart des centres de données locaux appliquent des règles de réseau strictes qui empêchent les communications entrantes sur le pare-feu de limite du réseau. Kubernetes avec Azure Arc respecte ces restrictions en n’exigeant pas de ports entrants sur le pare-feu et en activant uniquement des points de terminaison de sortie sélectifs pour la communication sortante. Les agents Kubernetes compatibles avec Azure Arc initient cette communication sortante. 

![Vue d’ensemble de l’architecture](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>Connecter un cluster à Azure Arc

1. Créez un cluster Kubernetes sur l’infrastructure de votre choix (VMware vSphere, Amazon Web Services, Google Cloud Platform, etc.). 

    > [!NOTE]
    > Kubernetes avec Azure Arc prenant uniquement en charge l’attachement de clusters Kubernetes existants à Azure Arc, les clients doivent créer et gérer eux-mêmes le cycle de vie du cluster Kubernetes.  

1. Démarrez l’inscription Azure Arc pour votre cluster à l’aide d’Azure CLI.
    * Azure CLI utilise Helm pour déployer le graphique Helm de l’agent sur le cluster.
    * Les nœuds de cluster initient une communication sortante vers [Microsoft Container Registry](https://github.com/microsoft/containerregistry) et extraient les images nécessaires à la création des agents suivants dans l’espace de noms `azure-arc` :

        | Agent | Description |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Actuellement, Kubernetes compatible avec Azure Arc prend uniquement en charge les [identités affectées par le système](../../active-directory/managed-identities-azure-resources/overview.md). `clusteridentityoperator` initie la première communication sortante. La première communication extrait le certificat MSI (Managed Service Identity) utilisé par d’autres agents pour la communication avec Azure. |
        | `deployment.apps/config-agent` | Surveille le cluster connecté pour les ressources de configuration du contrôle de code source appliquées au cluster. Met à jour l’état de conformité. |
        | `deployment.apps/controller-manager` | Opérateur d’opérateurs orchestrant les interactions entre les composants Azure Arc. |    
        | `deployment.apps/metrics-agent` | Collecte les métriques d’autres agents Arc pour vérifier que les performances sont optimales. |
        | `deployment.apps/cluster-metadata-operator` | Recueille les métadonnées du cluster, notamment la version du cluster, le nombre de nœuds et la version de l’agent Azure Arc. |
        | `deployment.apps/resource-sync-agent` | Synchronise les métadonnées de cluster mentionnées ci-dessus dans Azure. |
        | `deployment.apps/flux-logs-agent` | Collecte des journaux des opérateurs de flux déployés dans le cadre de la configuration du contrôle de code source. |
        | `deployment.apps/extension-manager` | Installe et gère le cycle de vie des graphiques Helm d’extension |  
        | `deployment.apps/clusterconnect-agent` | Agent de proxy inverse qui active la fonctionnalité de connexion de cluster pour fournir l’accès à l’`apiserver` du cluster. Il s’agit d’un composant facultatif déployé uniquement si la fonctionnalité `cluster-connect` est activée sur le cluster   |
        | `deployment.apps/guard` | Serveur webhook d’authentification et d’autorisation utilisé pour la fonctionnalité RBAC AAD. Il s’agit d’un composant facultatif déployé uniquement si la fonctionnalité `azure-rbac` est activée sur le cluster   |

1. Une fois tous les pods d’agent Kubernetes avec Azure Arc dans l’état `Running`, vérifiez que votre cluster est connecté à Azure Arc. Vous devriez voir :
    * Une ressource Kubernetes compatible avec Azure Arc dans [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Azure suit cette ressource en tant que projection du cluster Kubernetes managé par le client, et comme le cluster Kubernetes réel.
    * Des métadonnées de cluster (telles que la version Kubernetes, la version de l’agent et le nombre de nœuds) apparaissent sur la ressource Kubernetes compatible avec Azure Arc sous forme de métadonnées.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>Échange de données entre l’environnement de cluster et Azure

| Type de données | Scénario | Mode de communication |
| --------- | -------- | ------------------ |
| Version du cluster Kubernetes | Métadonnées du cluster | Envoi (push) de l’agent vers Azure |
| Nombre de nœuds du cluster | Métadonnées du cluster | Envoi (push) de l’agent vers Azure |
| Version de l’agent | Métadonnées du cluster | Envoi (push) de l’agent vers Azure |
| Type de distribution Kubernetes | Métadonnées du cluster | Envoi (push) d’Azure CLI vers Azure |
| Type d’infrastructure (AWS/GCP/vSphere/...) | Métadonnées du cluster | Envoi (push) d’Azure CLI vers Azure |
| Nombre de processeurs virtuels de nœuds virtuels du cluster | Facturation | Envoi (push) d’Azure CLI vers Azure |
| Pulsation de l'agent | Intégrité des ressources | Envoi (push) de l’agent vers Azure |
| Consommation des ressources (mémoire/UC) par les agents | Diagnostics et prise en charge | Envoi (push) de l’agent vers Azure |
| Journaux de tous les conteneurs d’agent | Diagnostics et prise en charge | Envoi (push) de l’agent vers Azure |
| Disponibilité de la mise à niveau de l’agent | Mise à niveau de l'agent | Tirage (pull) de l’agent depuis Azure |
| État souhaité de la configuration : URL du référentiel Git, paramètres d’opérateur de flux, clé privée, contenu des hôtes connus, nom d’utilisateur HTTPS, jeton/mot de passe | Configuration | Tirage (pull) de l’agent depuis Azure |
| État d’installation de l’opérateur de flux | Configuration | Envoi (push) de l’agent vers Azure |
| Affectations Azure Policy nécessitant l’application de Gatekeeper au sein du cluster | Azure Policy | Tirage (pull) de l’agent depuis Azure |
| Audit et état de conformité des applications de stratégie dans le cluster | Azure Policy | Envoi (push) de l’agent vers Azure |
| Métriques et journaux des charges de travail client | Azure Monitor | Envoi (push) de l’agent vers ressource de l’espace de travail Log Analytics dans le locataire et l’abonnement du client |

## <a name="connectivity-status"></a>État de la connectivité

| Statut | Description |
| ------ | ----------- |
| Connecting | Une ressource Kubernetes avec Azure Arc est créée dans Azure Resource Manager, mais le service n’a pas encore reçu de pulsation de l’agent. |
| Connecté | Le service Kubernetes compatible avec Azure Arc a reçu une pulsation de l’agent au cours des 15 dernières minutes. |
| Hors connexion | La ressource Kubernetes compatible avec Azure Arc était précédemment connectée, mais le service n’a pas reçu de pulsation d’agent pendant 15 minutes. |
| Expiré | Le certificat MSI (Managed Service Identity) présente une fenêtre d’expiration de 90 jours après son émission. Une fois ce certificat expiré, la ressource est considérée comme `Expired`, et toutes les fonctionnalités telles que la configuration, la surveillance et la stratégie cessent de fonctionner sur ce cluster. Pour plus d’informations sur la façon de gérer les ressources Kubernetes avec Azure Arc expirées, consultez l’[articles du FAQ](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources). |

## <a name="understand-connectivity-modes"></a>Comprendre les modes de connectivité

| Mode de connectivité | Description |
| ----------------- | ----------- |
| Entièrement connecté | Les agents peuvent communiquer sans cesse avec Azure avec peu de retard de propagation des configurations GitOps, en appliquant des stratégies Azure Policy et Gatekeeper, et en recueillant des métriques et journaux de charge de travail dans Azure Monitor. |
| Semi-connecté | Le certificat MSI extrait par le `clusteridentityoperator` est valide pendant jusqu’à 90 jours avant d’expirer. Ensuite, la ressource Kubernetes avec Azure Arc cesse de fonctionner. Pour réactiver toutes les fonctionnalités Azure Arc sur le cluster, supprimez et recréez la ressource Kubernetes avec Azure Arc, ainsi que les agents. Pendant les 90 jours, connectez le cluster au moins une fois tous les 30 jours. |
| Déconnecté | Actuellement, les clusters Kubernetes dans des environnements déconnectés sont incapables d’accéder à Azure et ne sont pas pris en charge par Kubernetes avec Azure Arc. Si cette fonctionnalité vous intéresse, envoyez ou votez pour une idée sur le [forum UserVoice d’Azure Arc](https://feedback.azure.com/d365community/forum/5c778dec-0625-ec11-b6e6-000d3a4f0858).

## <a name="next-steps"></a>Étapes suivantes

* Parcourez notre guide de démarrage rapide pour [connecter un cluster Kubernetes à Azure Arc](./quickstart-connect-cluster.md).
* Apprenez-en davantage sur la création de connexions entre votre cluster et un référentiel Git en guise de [ressource de configuration intégrant Kubernetes avec Azure Arc](./conceptual-configurations.md).
