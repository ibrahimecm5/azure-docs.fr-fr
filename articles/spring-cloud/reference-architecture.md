---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: karlerickson
title: Architecture de référence Azure Spring Cloud
ms.author: akaleshian
ms.service: spring-cloud
description: Cette architecture de référence est une base qui utilise une conception hub-and-spoke typique des entreprises pour l’utilisation d’Azure Spring Cloud.
ms.openlocfilehash: a3e8cbb94500c92ba4d32e1fef4f02f1acf9c1c9
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486845"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Architecture de référence Azure Spring Cloud

Cette architecture de référence est une base qui utilise une conception hub-and-spoke typique des entreprises pour l’utilisation d’Azure Spring Cloud. Dans la conception, Azure Spring Cloud est déployé dans un seul spoke dépendant des services partagés hébergés dans le hub. L’architecture est conçue avec des composants permettant de réaliser les principes de [Microsoft Azure Well-Architected Framework][16].

Pour l’implémentation de cette architecture, consultez le référentiel [Architecture de référence Azure Spring Cloud][10] sur GitHub.

Les options de déploiement de cette architecture sont Azure Resource Manager (ARM), Terraform, Azure CLI et Bicep. Les artefacts de ce référentiel fournissent une base que vous pouvez adapter à votre environnement. Vous pouvez regrouper des ressources telles que Pare-feu Azure ou Application Gateway dans différents groupes de ressources ou abonnements. Ce regroupement permet de séparer différentes fonctions, telles que l’infrastructure informatique, la sécurité, les équipes chargées des applications commerciales, etc.

## <a name="planning-the-address-space"></a>Planification de l’espace d’adressage

Azure Spring Cloud requiert deux sous-réseaux dédiés :

* Runtime du service
* Applications Spring Boot

Chacun de ces sous-réseaux nécessite un cluster Azure Spring Cloud dédié. Les clusters multiples ne peuvent pas partager les mêmes sous-réseaux. La taille minimale de chaque sous-réseau est de /28. Le nombre d’instances d’application qu’Azure Spring Cloud peut prendre en charge varie en fonction de la taille du sous-réseau. Vous trouverez les détails de la configuration requise pour le réseau virtuel dans la section [Configuration requise pour le réseau virtuel][11] de [Déployer Azure Spring Cloud dans un réseau virtuel][17].

> [!WARNING]
> La taille du sous-réseau sélectionné ne peut pas chevaucher l’espace d’adressage du réseau virtuel existant et ne doit pas chevaucher les plages d’adresses de sous-réseau locales ou appairées.

## <a name="use-cases"></a>Cas d'utilisation

Utilisations courantes de cette architecture :

* Applications privées : applications internes déployées dans des environnements de cloud hybrides
* Applications publiques : applications accessibles de l’extérieur

Ces cas d’usage sont similaires, à l’exception de leurs règles de sécurité et de trafic. Cette architecture est conçue pour prendre en charge les nuances de chacun.

## <a name="private-applications"></a>Applications privées

La liste suivante décrit les exigences en matière d’infrastructure pour les applications privées. Ces exigences sont typiques des environnements hautement réglementés.

* Un sous-réseau ne doit avoir qu’une seule instance d’Azure Spring Cloud.
* Le respect d’au moins un point de référence de sécurité doit être appliqué.
* Les enregistrements DNS (Domain Name Service) de l’hôte d’application doivent être stockés dans DNS privé Azure.
* Les dépendances de service Azure doivent communiquer par l’intermédiaire de points de terminaison de service ou d’une liaison privée.
* Les données au repos doivent être chiffrées.
* Les données en transit doivent être chiffrées.
* Des pipelines de déploiement DevOps peuvent être utilisés (par exemple, Azure DevOps) et nécessitent une connectivité réseau avec Azure Spring Cloud.
* Le trafic de sortie doit traverser une appliance centrale de réseau virtuel (par exemple, Pare-feu Azure).
* Si le [composant Config Server d’Azure Spring Cloud][8] est utilisé pour charger des propriétés de configuration à partir d’un référentiel, le dépôt doit être privé.
* L’approche de sécurité Zero Trust de Microsoft exige que les secrets, les certificats et les informations d’identification soient stockés dans un coffre sécurisé. Le service recommandé est Azure Key Vault.
* La résolution de noms des hôtes locaux et dans le cloud doit être bidirectionnelle.
* Aucune sortie directe vers l’Internet public, à l’exception du trafic du plan de contrôle.
* Les groupes de ressources gérés par le déploiement d’Azure Spring Cloud ne doivent pas être modifiés.
* Les sous-réseaux gérés par le déploiement d’Azure Spring Cloud ne doivent pas être modifiés.

La liste suivante répertorie les composants qui constituent la conception :

* Réseau local
  * Service de nom de domaine (DNS)
  * Passerelle
* Abonnement au hub
  * Sous-réseau d’Application Gateway
  * Sous-réseau de Pare-feu Azure
  * Sous-réseau de services partagés
* Abonnement connecté
  * Sous-réseau d’Azure Bastion
  * Appairage de réseaux virtuels

La liste suivante décrit les services Azure dans cette architecture de référence :

* [Azure Key Vault][2] : service de gestion des informations d’identification soutenu par matériel informatique qui est étroitement intégré aux services d’identité et aux ressources de calcul de Microsoft.

* [Azure Monitor][3] : suite complète de services de surveillance pour les applications déployées sur Azure et localement.

* [Azure Pipelines][5] : service complet d’intégration continue et de livraison continue (CI/CD) qui peut déployer automatiquement des applications Spring Boot mises à jour sur Azure Spring Cloud.

* [Microsoft Defender pour le cloud][4] : système unifié de gestion de la sécurité et de protection contre les menaces pour les charges de travail localement, sur plusieurs clouds et sur Azure.

* [Azure Spring Cloud][1] : service géré conçu et optimisé spécifiquement pour les applications Spring Boot en Java et les applications [Steeltoe][9] basées sur .NET.

Le diagramme suivant représente une architecture hub-and-spoke bien conçue qui répond aux exigences ci-dessus :

![Diagramme d’architecture de référence pour les applications privées](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>Applications publiques

La liste suivante décrit les exigences en matière d’infrastructure pour les applications publiques. Ces exigences sont typiques des environnements hautement réglementés. Ces exigences sont un sur-ensemble de celles de la section précédente. Les éléments supplémentaires sont indiqués en italiques.

* Un sous-réseau ne doit avoir qu’une seule instance d’Azure Spring Cloud.
* Le respect d’au moins un point de référence de sécurité doit être appliqué.
* Les enregistrements DNS (Domain Name Service) de l’hôte d’application doivent être stockés dans DNS privé Azure.
* _La Protection Azure DDoS standard doit être activée._
* Les dépendances de service Azure doivent communiquer par l’intermédiaire de points de terminaison de service ou d’une liaison privée.
* Les données au repos doivent être chiffrées.
* Les données en transit doivent être chiffrées.
* Des pipelines de déploiement DevOps peuvent être utilisés (par exemple, Azure DevOps) et nécessitent une connectivité réseau avec Azure Spring Cloud.
* Le trafic de sortie doit traverser une appliance centrale de réseau virtuel (par exemple, Pare-feu Azure).
* _Le trafic d’entrée doit être géré au moins par Application Gateway ou Azure Front Door._
* _Les adresses Internet routables doivent être stockées dans DNS public Azure._
* L’approche de sécurité Zero Trust de Microsoft exige que les secrets, les certificats et les informations d’identification soient stockés dans un coffre sécurisé. Le service recommandé est Azure Key Vault.
* La résolution de noms des hôtes locaux et dans le cloud doit être bidirectionnelle.
* Aucune sortie directe vers l’Internet public, à l’exception du trafic du plan de contrôle.
* Les groupes de ressources gérés par le déploiement d’Azure Spring Cloud ne doivent pas être modifiés.
* Les sous-réseaux gérés par le déploiement d’Azure Spring Cloud ne doivent pas être modifiés.

La liste suivante répertorie les composants qui constituent la conception :

* Réseau local
  * Service de nom de domaine (DNS)
  * Passerelle
* Abonnement au hub
  * Sous-réseau d’Application Gateway
  * Sous-réseau de Pare-feu Azure
  * Sous-réseau de services partagés
* Abonnement connecté
  * Sous-réseau d’Azure Bastion
  * Appairage de réseaux virtuels

La liste suivante décrit les services Azure dans cette architecture de référence :

* _[Pare-feu de l’Application Azure][7] : fonctionnalité de la Passerelle d’application Azure qui protège les applications de manière centralisée contre les vulnérabilités et codes malveillants exploitant une faille de sécurité les plus courants._

* _[Passerelle d’application Azure][6] : équilibreur de charge responsable du trafic d’application avec déchargement TLS (Transport Layer Security) fonctionnant au niveau de la couche 7._

* [Azure Key Vault][2] : service de gestion des informations d’identification soutenu par matériel informatique qui est étroitement intégré aux services d’identité et aux ressources de calcul de Microsoft.

* [Azure Monitor][3] : suite complète de services de surveillance pour les applications déployées sur Azure et localement.

* [Azure Pipelines][5] : service complet d’intégration continue et de livraison continue (CI/CD) qui peut déployer automatiquement des applications Spring Boot mises à jour sur Azure Spring Cloud.

* [Microsoft Defender pour le cloud][4] : système unifié de gestion de la sécurité et de protection contre les menaces pour les charges de travail localement, sur plusieurs clouds et sur Azure.

* [Azure Spring Cloud][1] : service géré conçu et optimisé spécifiquement pour les applications Spring Boot en Java et les applications [Steeltoe][9] basées sur .NET.

Le diagramme suivant représente une architecture hub-and-spoke bien conçue qui répond aux exigences ci-dessus.  Notez que seul le réseau virtuel hub communique avec Internet :

![Diagramme d’architecture de référence pour les applications publiques](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Connectivité locale d’Azure Spring Cloud

Les applications dans Azure Spring Cloud peuvent communiquer avec différentes ressources Azure, locales et externes. En utilisant la conception hub-and-spoke, les applications peuvent acheminer le trafic vers l’extérieur ou vers le réseau local à l’aide d’Express Route ou d’un réseau privé virtuel (VPN) site à site.

## <a name="azure-well-architected-framework-considerations"></a>Considérations relatives à Azure Well-Architected Framework

[Azure Well-Architected Framework][16] est un ensemble de principes directeurs à suivre pour l’établissement d’une base d’infrastructure solide. Le Framework contient les catégories suivantes : optimisation des coûts, excellence opérationnelle, efficacité des performances, fiabilité et sécurité.

### <a name="cost-optimization"></a>Optimisation des coûts

En raison de la nature de la conception des systèmes distribués, l’expansion de l’infrastructure est une réalité. Cette réalité se traduit par des coûts inattendus et incontrôlables. Azure Spring Cloud est construit à l’aide de composants qui sont mis à l’échelle de manière à pouvoir répondre à la demande et à optimiser les coûts. Azure Kubernetes Service (AKS) est au cœur de cette architecture. Le service est conçu pour réduire la complexité et la surcharge opérationnelle de la gestion de Kubernetes, ce qui inclut des gains d’efficacité dans le coût opérationnel du cluster.

Vous pouvez déployer différentes applications et différents types d’applications sur une instance unique d’Azure Spring Cloud. Le service prend en charge la mise à l’échelle automatique des applications déclenchées par des métriques ou des planifications qui peuvent améliorer l’utilisation et la rentabilité.

Vous pouvez également utiliser Application Insights et Azure Monitor pour réduire le coût d’exploitation. Grâce à la visibilité fournie par la solution complète de journalisation, vous pouvez implémenter l’automatisation pour mettre à l’échelle les composants du système en temps réel. Vous pouvez également analyser les données de journal pour révéler des inefficacités dans le code d’application que vous pouvez corriger pour améliorer le coût et les performances du système dans leur ensemble.

### <a name="operational-excellence"></a>Excellence opérationnelle

Azure Spring Cloud traite plusieurs aspects de l’excellence opérationnelle. Vous pouvez combiner ces aspects pour vous assurer que le service fonctionne efficacement dans les environnements de production, comme décrit dans la liste suivante :

* Vous pouvez utiliser Azure Pipelines pour garantir la fiabilité et la cohérence des déploiements tout en vous aidant à éviter les erreurs humaines.
* Vous pouvez utiliser Azure Monitor et Application Insights pour stocker les données de journal et de télémétrie.
  Vous pouvez évaluer les données de journal et de métrique collectées pour garantir l’intégrité et les performances de vos applications. L’analyse des performances des applications (APM) est entièrement intégrée au service par le biais d’un agent Java. Cet agent fournit une visibilité sur toutes les applications déployées et les dépendances sans nécessiter de code supplémentaire. Pour plus d’informations, consultez le billet de blog [Surveiller sans effort les applications et les dépendances dans Azure Spring Cloud][15].
* Vous pouvez utiliser Microsoft Defender pour le cloud pour vous assurer que les applications maintiennent la sécurité en fournissant une plateforme permettant d’analyser et d’évaluer les données fournies.
* Le service prend en charge différents modèles de déploiement. Pour plus d’informations, consultez [Configurer un environnement intermédiaire dans Azure Spring Cloud][14].

### <a name="reliability"></a>Fiabilité

Azure Spring Cloud est basé sur AKS. Bien qu’AKS offre un niveau de résilience grâce au clustering, cette architecture de référence intègre des services et des considérations architecturales pour accroître la disponibilité de l’application en cas de défaillance d’un composant.

En s’appuyant sur une conception hub-and-spoke bien définie, la base de cette architecture garantit que vous pouvez la déployer dans plusieurs régions. En cas d’utilisation d’une application privée, l’architecture utilise DNS privé Azure pour garantir la disponibilité continue pendant une défaillance géographique. En cas d’utilisation d’une application publique, Azure Front Door et Azure Application Gateway garantissent la disponibilité.

### <a name="security"></a>Sécurité

La sécurité de cette architecture est assurée par son respect des contrôles et des points de référence définis par le secteur d’activité. Dans ce contexte, le « contrôle » désigne une pratique recommandée concise et bien définie, telle que « utiliser le principe de privilège minimum lors de l’implémentation de l’accès au système d’information. IAM-05 » Les contrôles de cette architecture proviennent de la [Matrice CCM ][19](Cloud Control Matrix) via le[Cloud Security Alliance][18] (CSA) et du[Microsoft Azure Foundations MAFB][20] (MAFB) via le [Centre de Sécurité Internet][21] (CIS). Dans les contrôles appliqués, l’accent est mis sur les principaux principes de conception de la sécurité, à savoir la gouvernance, la mise en réseau et la sécurité des applications. Il vous appartient de gérer les principes de conception de l’identité, de la gestion des accès et du stockage en fonction de votre infrastructure cible.

#### <a name="governance"></a>Gouvernance

Le principal aspect de la gouvernance que cette architecture aborde est la séparation par l’isolation des ressources réseau. Dans la CCM, DCS-08 recommande le contrôle des entrées et des sorties sur le centre de données. Pour satisfaire ce contrôle, l’architecture utilise une conception hub-and-spoke utilisant des groupes de sécurité réseau (NSG) pour filtrer le trafic est-ouest entre les ressources. L’architecture filtre également le trafic entre les services centraux dans le hub et les ressources dans le spoke. L’architecture utilise une instance de Pare-feu Azure pour gérer le trafic entre Internet et les ressources au sein de l’architecture.

La liste suivante montre le contrôle qui traite de la sécurité du centre de données dans cette référence :

| ID du contrôle CSA CCM | Domaine du contrôle CSA CCM |
| :----------------- | :----------------------|
| DCS-08 | Sécurité du centre de sécurité – Entrée de personnes non autorisées |

#### <a name="network"></a>Réseau

La conception du réseau prenant en charge cette architecture est dérivée du modèle hub-and-spoke traditionnel. Cette décision garantit que l’isolement réseau est une construction fondamentale. Le contrôle CCM IVS-06 recommande que le trafic entre les réseaux et les machines virtuelles soit restreint et surveillé entre les environnements approuvés et non approuvés. Cette architecture adopte le contrôle en implémentant les NSG pour le trafic est-ouest (au sein du « centre de données ») et le Pare-feu Azure pour le trafic nord-sud (hors du « centre de données »). Le contrôle CCM IPY-04 recommande que l’infrastructure utilise des protocoles réseau sécurisés pour l’échange de données entre les services. Les services Azure qui prennent en charge cette architecture utilisent tous des protocoles sécurisés standard tels que TLS pour HTTP et SQL.

La liste suivante répertorie les contrôles CCM qui traitent de la sécurité du réseau dans cette référence :

| ID du contrôle CSA CCM | Domaine du contrôle CSA CCM |
| :----------------- | :----------------------|
| IPY-04             | Protocoles réseau      |
| IVS-06             | Sécurité réseau       |

L’implémentation du réseau est en outre sécurisée par la définition de contrôles du MAFB. Ces contrôles garantissent que le trafic dans l’environnement est limité à partir de l’Internet public.

La liste suivante répertorie les contrôles CIS qui traitent de la sécurité du réseau dans cette référence :

| ID du contrôle CIS | Description du contrôle CIS |
| :------------- | :---------------------- |
| 6.2 | Vérifie que l’accès SSH à partir d’Internet est restreint. |
| 6.3 | Vérifie qu’aucune base de données SQL n’autorise l’accès à 0.0.0.0/0 (N’IMPORTE QUELLE ADRESSE IP). |
| 6.5 | Vérifie que Network Watcher est défini sur « Activé ». |
| 6.6 | Vérifie que l’entrée à l’aide d’UDP est restreinte à partir d’Internet. |

Azure Spring Cloud nécessite une gestion du trafic pour permettre les sorties d’Azure lorsque le service est déployé dans un environnement sécurisé. Pour ce faire, vous devez autoriser les règles de réseau et d’application mentionnées dans [Responsabilités du client pour l’exécution d’Azure Spring Cloud dans un réseau virtuel](./vnet-customer-responsibilities.md).

#### <a name="application-security"></a>Sécurité des applications

Ce principal de conception couvre les composants fondamentaux de l’identité, de la protection des données, de la gestion des clés et de la configuration des applications. De par sa conception, une application déployée dans Azure Spring Cloud s’exécute avec les privilèges minimum requis pour fonctionner. L’ensemble des contrôles d’autorisation est directement lié à la protection des données lors de l’utilisation du service. La gestion des clés renforce cette approche de la sécurité des applications par couches.

La liste suivante répertorie les contrôles CCM qui traitent de la gestion des clés dans cette référence :

| ID du contrôle CSA CCM | Domaine du contrôle CSA CCM |
| :----------------- | :--------------------- |
| EKM-01 | Chiffrement et gestion des clés – Droit d’utilisation |
| EKM-02 | Chiffrement et gestion des clés – Génération de clés |
| EKM-03 | Chiffrement et gestion des clés – Protection des données sensibles |
| EKM-04 | Chiffrement et gestion des clés – Stockage et accès |

Dans la CCM, EKM-02 et EKM-03 recommandent des stratégies et des procédures pour gérer les clés et utiliser les protocoles de chiffrement afin de protéger les données sensibles. EKM-01 recommande que toutes les clés de chiffrement aient des propriétaires identifiables afin qu’elles puissent être gérées. EKM-04 recommande l’utilisation d’algorithmes standard.

La liste suivante répertorie les contrôles CIS qui traitent de la gestion des clés dans cette référence :

| ID du contrôle CIS | Description du contrôle CIS |
| :------------- | :---------------------- |
| 8.1 | Vérifie que la date d’expiration est définie sur toutes les clés. |
| 8,2 | Vérifie que la date d’expiration est définie sur tous les secrets. |
| 8,4 | Vérifie que le coffre de clés est récupérable. |

Les contrôles CIS 8.1 et 8.2 recommandent que des dates d’expiration soient définies pour les informations d’identification afin de garantir l’application de la rotation. Le contrôle CIS 8.4 s’assure que le contenu du coffre de clés peut être restauré pour garantir la continuité de l’activité.

Ces aspects de la sécurité des applications constituent une base pour l’utilisation de cette architecture de référence afin de prendre en charge une charge de travail Spring dans Azure.

## <a name="next-steps"></a>Étapes suivantes

Explorez cette architecture de référence via les déploiements ARM, Terraform et Azure CLI disponibles dans le référentiel [Architecture de référence Azure Spring Cloud][10].

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./how-to-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./how-to-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./how-to-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: /azure/security/benchmarks/v2-cis-benchmark
[21]: https://www.cisecurity.org/
