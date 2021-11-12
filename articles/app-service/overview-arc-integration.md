---
title: App Service sur Azure Arc
description: Introduction à l’intégration d’App Service avec Azure arc pour opérateurs Azure.
ms.topic: article
ms.date: 11/02/2021
ms.openlocfilehash: 74cf4063d92aa5d563df881f2210e2ca5d08543e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440352"
---
# <a name="app-service-functions-and-logic-apps-on-azure-arc-preview"></a>App Service, Functions et Logic Apps sur Azure Arc (préversion)

Vous pouvez exécuter App Service, Functions et Logic Apps sur un cluster Kubernetes avec Azure Arc. Le cluster Kubernetes peut être local ou hébergé dans un cloud tiers. Cette approche permet aux développeurs d’applications de tirer parti des fonctionnalités d’App Service. En même temps, elle permet à leurs administrateurs informatiques de maintenir la conformité de l’entreprise en hébergeant les applications App Service sur une infrastructure interne. Elle permet également à d’autres opérateurs informatiques de protéger leurs investissements antérieurs dans d’autres fournisseurs de cloud en exécutant App Service sur des clusters Kubernetes existants.

> [!NOTE]
> Pour savoir comment configurer votre cluster Kubernetes pour App Service, Functions et Logic Apps, consultez [Créer un environnement Kubernetes App Service (préversion)](manage-create-arc-environment.md).

Dans la plupart des cas, les développeurs d’applications n’ont besoin de savoir que comment déployer vers la région Azure appropriée qui représente l’environnement Kubernetes déployé. Les opérateurs qui fournissent l’environnement et gèrent l’infrastructure Kubernetes sous-jacente doivent connaître les ressources Azure suivantes :

- Le cluster connecté, qui est une projection Azure de votre infrastructure Kubernetes. Pour plus d’informations, consultez [Qu’est-ce que Kubernetes avec Azure Arc ?](../azure-arc/kubernetes/overview.md).
- Une extension de cluster, qui est une sous-ressource de la ressource de cluster connectée. L’extension App Service [installe les pods requis dans votre cluster connecté](#pods-created-by-the-app-service-extension). Pour plus d’informations sur les extensions de cluster, consultez [Extensions de cluster sur Kubernetes avec Azure Arc](../azure-arc/kubernetes/conceptual-extensions.md).
- Un emplacement personnalisé qui rassemble un groupe d’extensions et mappe celles-ci à un espace de noms pour les ressources créées. Pour plus d’informations, consultez [Emplacements personnalisés sur Kubernetes avec Azure Arc](../azure-arc/kubernetes/conceptual-custom-locations.md).
- Un environnement Kubernetes App Service qui permet une configuration commune à toutes les applications, mais non lié aux opérations de cluster. D’un point de vue conceptuel, il est déployé dans la ressource d’emplacement personnalisée et les développeurs d’applications créent des applications dans cet environnement. Cela est décrit plus en détail dans [Environnement Kubernetes App Service](#app-service-kubernetes-environment).

## <a name="public-preview-limitations"></a>Limitations de la version préliminaire publique

Les limitations de préversion publique suivantes s’appliquent aux environnements Kubernetes App Service. Elles seront mises à jour à mesure que des modifications seront rendues disponibles.

| Limitation                                              | Détails                                                                               |
|---------------------------------------------------------|---------------------------------------------------------------------------------------|
| Régions Azure prises en charge                                 | USA Est, Europe Ouest                                                                  |
| Exigence de mise en réseau du cluster                          | Doit prendre en charge le type de service `LoadBalancer` et fournir une adresse IP statique adressable publiquement |
| Exigences de stockage du cluster                             | Une classe de stockage attachée au cluster doit être disponible pour être utilisée par l’extension pour prendre en charge le déploiement et la génération d’applications basées sur du code, le cas échéant                      |
| Fonctionnalité : mise en réseau                                     | [Non disponible (dépend de la mise en réseau du cluster)](#are-networking-features-supported)      |
| Fonctionnalité : identités managées                             | [Non disponible](#are-managed-identities-supported)                                    |
| Fonctionnalité : références de coffre de clés                           | Non disponible (dépend d’identités managées)                                         |
| Fonctionnalité : extraire des images d’ACR avec une identité managée     | Non disponible (dépend d’identités managées)                                         |
| Fonctionnalité : modification dans le portail pour Functions et Logic Apps | Non disponible                                                                         |
| Fonctionnalité : publication FTP                                 | Non disponible                                                                         |
| Journaux d’activité                                                    | Log Analytics doit être configuré avec l’extension de cluster, pas par site                 |

## <a name="pods-created-by-the-app-service-extension"></a>Pod créés par l’extension App Service

Quand l’extension App Service est installée sur le cluster Kubernetes avec Azure Arc, vous voyez plusieurs pods créés dans l’espace de noms de version qui a été spécifié. Ces pods permettent à votre cluster Kubernetes d’être une extension du fournisseur de ressources `Microsoft.Web` dans Azure et de prendre en charge la gestion et l’exploitation de vos applications. Si vous le souhaitez, vous pouvez choisir que l’extension installe [KEDA](https://keda.sh/) pour la mise à l’échelle pilotée par des événements.
 <!-- You can only have one installation of KEDA on the cluster. If you have one already, you must disable this behavior during installation of the cluster extension `TODO`. -->

Le tableau suivant décrit le rôle de chaque pod créé par défaut :

| Pod                                   | Description                                                                                                                       |
|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| `<extensionName>-k8se-app-controller` | Pod opérateur principal qui crée des ressources sur le cluster et gère l’état des composants.                                |
| `<extensionName>-k8se-envoy`          | Couche proxy frontale pour toutes les demandes de plan de données. Elle achemine le trafic entrant vers les applications correctes.                           |
| `<extensionName>-k8se-activator`      | Autre destination de routage pour aider les applications qui ont été mises à l’échelle zéro alors que le système reçoit la première instance disponible. |
| `<extensionName>-k8se-build-service`  | Prend en charge les opérations de déploiement et sert la [fonctionnalité Outils avancés](resources-kudu.md).                                        |
| `<extensionName>-k8se-http-scaler`    | Surveille le volume de demandes entrantes pour fournir des informations de mise à l’échelle à [KEDA](https://keda.sh).                               |
| `<extensionName>-k8se-img-cacher`     | Extrait les images d’espace réservé et d’application dans un cache local sur le nœud.                                                                  |
| `<extensionName>-k8se-log-processor`  | Recueille les journaux d’applications et d’autres composants, puis les envoie à Log Analytics.                                                      |
| `placeholder-azure-functions-*`       | Utilisé pour accélérer les démarrages à froid pour Azure Functions.                                                                                 |

## <a name="app-service-kubernetes-environment"></a>Environnement Kubernetes App Service

La ressource d’environnement Kubernetes App Service est requise pour la création d’applications. Elle permet une configuration commune aux applications dans l’emplacement personnalisé, par exemple, le suffixe DNS par défaut.

Une seule ressource d’environnement Kubernetes peut être créée dans un emplacement personnalisé. Dans la plupart des cas, un développeur qui crée et déploie des applications n’a pas besoin de connaître directement la ressource. Il est possible de l’inférer directement à partir de l’ID d’emplacement personnalisé fourni. Toutefois, lors de la définition de modèles Azure Resource Manager, toute ressource de plan doit faire référence directement à l’ID de ressource de l’environnement. Les valeurs d’emplacement personnalisées du plan et de l’environnement spécifié doivent correspondre.

## <a name="faq-for-app-service-functions-and-logic-apps-on-azure-arc-preview"></a>FAQ pour App Service, Functions et Logic Apps sur Azure Arc (préversion)

- [Combien ça coûte ?](#how-much-does-it-cost)
- [Les applications Windows et Linux sont-elles prises en charge ?](#are-both-windows-and-linux-apps-supported)
- [Quelles sont les piles d’applications intégrées prises en charge ?](#which-built-in-application-stacks-are-supported)
- [Tous les types de déploiement d’application sont-ils pris en charge ?](#are-all-app-deployment-types-supported)
- [Quelles sont les fonctionnalités d’App Service prises en charge ?](#which-app-service-features-are-supported)
- [Les fonctionnalités réseau sont-elles prises en charge ?](#are-networking-features-supported)
- [Les identités gérées sont-elles prises en charge ?](#are-managed-identities-supported)
- [Existe-t-il des limites de mise à l’échelle ?](#are-there-any-scaling-limits)
- [Quels journaux sont collectés ?](#what-logs-are-collected)
- [Que dois-je faire si une erreur d’inscription du fournisseur s’affiche ?](#what-do-i-do-if-i-see-a-provider-registration-error)
- [Puis-je déployer l’extension des Services d’application sur un cluster basé sur ARM64 ?](#can-i-deploy-the-application-services-extension-on-an-arm64-based-cluster)

### <a name="how-much-does-it-cost"></a>Quel est son coût ?

App Service sur Azure Arc est gratuit pendant la période de préversion publique.

### <a name="are-both-windows-and-linux-apps-supported"></a>Les applications Windows et Linux sont-elles prises en charge ?

Seules les applications basées sur Linux sont prises en charge, tant pour le code que les conteneurs personnalisés. Le applications Windows ne sont pas prises en charge.

### <a name="which-built-in-application-stacks-are-supported"></a>Quelles sont les piles d’applications intégrées prises en charge ?

Toutes les piles Linux intégrées sont prises en charge.

### <a name="are-all-app-deployment-types-supported"></a>Tous les types de déploiements d’applications sont-ils pris en charge ?

Le déploiement FTP n’est pas pris en charge. Actuellement, `az webapp up` n’est pas non plus pris en charge. D’autres méthodes de déploiement sont prises en charge, dont Git, ZIP, CI/CD, Visual Studio et Visual Studio Code.

### <a name="which-app-service-features-are-supported"></a>Quelles sont les fonctionnalités d’App Service prises en charge ?

Pendant la période de préversion, certaines fonctionnalités d’App Service sont en cours de validation. Une fois qu’elle sont prises en charge, leurs options de navigation à gauche dans le portail Azure sont activées. Les fonctionnalités qui ne sont pas encore prises en charge restent grisées.

### <a name="are-networking-features-supported"></a>Les fonctionnalités réseau sont-elles prises en charge ?

Non. Les fonctionnalités de mise en réseau, telles que les connexions hybrides, l’intégration de réseau virtuel ou les restrictions d’adresse IP, ne sont pas prises en charge. La mise en réseau doit être gérée directement dans les règles de mise en réseau dans le cluster Kubernetes.

### <a name="are-managed-identities-supported"></a>Les identités managées sont-elles prises en charge ?

Non. Les applications ne peuvent pas recevoir d’identités managées lors de l’exécution dans Azure Arc. Si votre application a besoin d’une identité pour travailler avec une autre ressource Azure, envisagez d’utiliser un [principal de service d’application](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) à la place.

### <a name="are-there-any-scaling-limits"></a>Existe-t-il des limites de mise à l’échelle ?

Toutes les applications déployées avec Azure App Service sur Kubernetes avec Azure Arc peuvent être mises à l’échelle dans les limites du cluster Kubernetes sous-jacent.  Si le cluster Kubernetes sous-jacent ne dispose pas des ressources de calcul disponibles (principalement du processeur et de la mémoire), les applications peuvent alors uniquement mettre à l’échelle le nombre d’instances de l’application que Kubernetes peut planifier avec la ressource disponible.

### <a name="what-logs-are-collected"></a>Quels journaux sont collectés ?

Les journaux des composants système et de vos applications sont écrits dans une sortie standard. Il est possible de collecter les deux types de journaux à des fins d’analyse à l’aide d’outils Kubernetes standard. Vous pouvez également configurer l’extension de cluster App Service avec un [espace de travail Log Analytics](../azure-monitor/logs/log-analytics-overview.md) afin d’envoyer tous les journaux à cet espace de travail.

Par défaut, les journaux des composants système sont envoyés à l’équipe Azure. Les journaux des applications ne sont pas envoyés. Vous pouvez empêcher le transfert de ces journaux en définissant `logProcessor.enabled=false` comme paramètre de configuration d’extension. Cela a également pour effet de désactiver le transfert de l’application vers votre espace de travail Log Analytics. La désactivation du processeur de journal peut avoir un impact sur le temps nécessaire pour les cas de support, et vous serez invité à collecter les journaux de la sortie standard via d’autres moyens.

### <a name="what-do-i-do-if-i-see-a-provider-registration-error"></a>Que dois-je faire si une erreur d’inscription du fournisseur s’affiche ?

Lors de la création d’une ressource d’environnement Kubernetes, certains abonnements peuvent recevoir un message d’erreur indiquant qu’Aucun fournisseur de ressources inscrit n’a été trouvé. Les détails de l’erreur peuvent inclure un ensemble d’emplacements et de versions d’API considérés comme valides. Dans ce cas, il se peut que l’abonnement doive être réinscrit auprès du fournisseur Microsoft.Web. Cette opération n’a aucune incidence sur les applications ou API existantes. Pour vous réinscrire, dans Azure CLI, exécutez la commande `az provider register --namespace Microsoft.Web --wait`. Réexécutez ensuite la commande d’environnement Kubernetes.

### <a name="can-i-deploy-the-application-services-extension-on-an-arm64-based-cluster"></a>Puis-je déployer l’extension des Services d’application sur un cluster basé sur ARM64 ?

Les clusters basés sur ARM64 ne sont pas pris en charge pour l’instant.  

## <a name="extension-release-notes"></a>Notes de publication de l’extension

### <a name="application-services-extension-v-090-may-2021"></a>Extension des services d’application v0.9.0 (mai 2021)

- Préversion publique initiale de l’extension des services d’application.
- Prise en charge du code et des déploiements basés sur des conteneurs d’applications web, de fonction et logiques.
- Prise en charge du runtime d’application web : .NET 3.1 et 5.0 ; Node JS 12 et 14 ; Python 3.6, 3.7 et 3.8 ; PHP 7.3 et 7.4; Ruby 2.5, 2.5.5, 2.6 et 2.6.2 ; Java SE 8u232, 8u242, 8u252, 11.05, 11.06 et 11.07 ; Tomcat 8.5, 8.5.41, 8.5.53, 8.5.57, 9.0, 9.0.20, 9.0.33 et 9.0.37.

### <a name="application-services-extension-v-0100-november-2021"></a>Extension des services d’application v0.10.0 (novembre 2021)

Si votre extension se trouvait dans la version stable et que auto-upgrade-minor-version est défini sur activé, l’extension est automatiquement mise à niveau.  Pour mettre à niveau manuellement l’extension vers la dernière version, vous pouvez exécuter la commande ci-dessous

- Suppression de la spécification d’une adresse IP statique préaffectée requise pour l’affectation au point de terminaison Envoy
- Mettre à niveau Keda vers la v2.4.0
- Mettre à niveau l’envoi vers la v1.19.0
- Mettre à niveau le runtime Azure Function vers la v3.3.1
- Définir le nombre de réplicas par défaut du contrôleur d’application et le contrôleur Envoy sur 2 pour augmenter la stabilité

Si votre extension se trouvait dans la version stable et que auto-upgrade-minor-version est défini sur true, l’extension est automatiquement mise à niveau.  Pour mettre à niveau manuellement l’extension vers la dernière version, vous pouvez exécuter la commande ci-dessous :

```azurecli-interactive
    az k8s-extension update --cluster-type connectedClusters -c <clustername> -g <resource group> -n <extension name> --release-train stable --version 0.10.0
```


## <a name="next-steps"></a>Étapes suivantes

[Créer un environnement Kubernetes App Service (préversion)](manage-create-arc-environment.md)
