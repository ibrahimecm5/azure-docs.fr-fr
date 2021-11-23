---
title: Bonnes pratiques pour Red Hat JBoss EAP sur Azure
description: Ce guide fournit des informations sur les bonnes pratiques d’utilisation de Red Hat JBoss Enterprise Application Platform (EAP) sur Microsoft Azure.
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 195a0bfa-dff1-429b-b030-19ca95ee6abe
ms.date: 06/08/2021
ms.openlocfilehash: aaaa6fb269178954ade464745c6c640845457612
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289221"
---
# <a name="red-hat-jboss-eap-on-azure-best-practices"></a>Bonnes pratiques pour Red Hat JBoss EAP sur Azure

Ce guide fournit des informations sur les bonnes pratiques d’utilisation de Red Hat JBoss Enterprise Application Platform (EAP) sur Microsoft Azure. JBoss EAP peut être utilisé avec la plateforme Microsoft Azure, à condition d’être utilisé dans les configurations prises en charge spécifiques pour l’exécution de JBoss EAP sur Azure. Si vous configurez manuellement un environnement JBoss EAP en cluster, appliquez les configurations spécifiques nécessaires à l’utilisation des fonctionnalités de clustering JBoss EAP sur Azure. Ce guide détaille les configurations prises en charge de l’utilisation de JBoss EAP sur Microsoft Azure.

JBoss EAP est une implémentation compatible Jakarta Enterprise Edition (EE) 8 pour les spécifications de plateforme complète et de profil web. Il s’agit également d’une implémentation certifiée de la spécification Java EE 8. Les versions majeures de JBoss EAP sont extraites du projet communautaire WildFly à certains points où le projet communautaire a atteint le niveau d’élaboration de fonctionnalités souhaité. Après cela, une période prolongée de tests et de mise en production permet de stabiliser, certifier et améliorer JBoss EAP pour une utilisation en production. Pendant la durée de vie d’une version majeure de JBoss EAP, les fonctionnalités sélectionnées peuvent être prélevées par cherry-picking et rétroportées à partir du projet communautaire. Ces fonctionnalités sont ensuite rendues disponibles dans une série de versions mineures d’amélioration des fonctionnalités au sein de la même version majeure.

## <a name="supported-and-unsupported-configurations-of-jboss-eap-on-azure"></a>Configurations prises en charge et non prises en charge de JBoss EAP sur Azure

Pour plus d’informations sur les systèmes d’exploitation, les plateformes Java et les autres plateformes prises en charge sur lesquelles EAP peut être utilisé, consultez la documentation [Configurations prises en charge de JBoss EAP](https://access.redhat.com/articles/2026253).

Le programme Red Hat Cloud Access vous permet d’utiliser un abonnement JBoss EAP pour installer JBoss EAP sur votre machine virtuelle Azure, qui sont des systèmes d’exploitation PAYG (paiement à l’utilisation) à la demande provenant de la Place de marché Microsoft Azure. Les abonnements du système d’exploitation de machine virtuelle, dans ce cas Red Hat Enterprise Linux (RHEL), sont distincts d’un abonnement JBoss EAP. Red Hat Cloud Access est une fonctionnalité d’abonnement Red Hat qui fournit la prise en charge de JBoss EAP sur les fournisseurs d’infrastructure cloud certifiés Red Hat, tels que Microsoft Azure. Red Hat Cloud Access vous permet de déplacer vos abonnements entre les serveurs locaux traditionnels et les ressources cloud publiques d’une manière simple et économique.

Vous trouverez plus d’informations sur [Red Hat Cloud Access sur le portail client](https://www.redhat.com/en/technologies/cloud-computing/cloud-access). Rappel : vous n’avez pas besoin de Red Hat Cloud Access pour les offres avec paiement à l’utilisation sur la Place de marché Azure. 

Chaque version de Red Hat JBoss EAP est testée et prise en charge sur divers systèmes d’exploitation, machines virtuelles Java (JVM) et combinaisons de bases de données leaders du marché. Red Hat fournit une prise en charge en développement et production pour les configurations prises en charge et les intégrations testées en fonction de votre contrat d’abonnement. La prise en charge s’applique aux environnements physiques et virtuels. Outre les restrictions du système d’exploitation ci-dessus, vérifiez les [configurations prises en charge pour JBoss EAP](https://access.redhat.com/articles/2026253), telles que les fournisseurs et versions de Java Development Kit (JDK) pris en charge. Cet article fournit des informations sur les configurations prises en charge des différentes versions de JBoss EAP, telles que 7.0, 7.1, 7.2 et 7.3. Consultez le [tableau des produits et configurations pour Microsoft Azure](https://access.redhat.com/articles/product-configuration-for-azure) pour les systèmes d’exploitation RHEL pris en charge, les besoins minimaux en capacité de machine virtuelle et des informations sur d’autres produits Red Hat pris en charge. Consultez la page [Fournisseur de services cloud certifié Microsoft Azure](https://access.redhat.com/ecosystem/cloud-provider/2068823) pour voir les produits logiciels Red Hat certifiés pour fonctionner dans Microsoft Azure.

Certaines fonctionnalités ne sont pas prises en charge lors de l’utilisation de JBoss EAP dans un environnement Microsoft Azure, notamment :

- **Domaines managés** – Cette fonctionnalité permet la gestion de plusieurs instances JBoss EAP à partir d’un point de contrôle unique. Les domaines managés JBoss EAP ne sont actuellement pas pris en charge dans Microsoft Azure. Seules les instances de serveur JBoss EAP autonomes sont prises en charge. La configuration des clusters JBoss EAP avec des serveurs JBoss EAP autonomes est prise en charge dans Azure.

- **ActiveMQ Artemis High Availability (HA) avec magasin partagé** – La fonctionnalité de messagerie JBoss EAP à haute disponibilité avec des magasins partagés Artemis n’est pas prise en charge dans Microsoft Azure.

- **Publication `mod_custer`** – Vous ne pouvez pas utiliser JBoss EAP comme équilibreur de charge de proxy `mod_cluster` d’Undertow. La fonctionnalité de publication mod_cluster n’est pas prise en charge en raison des limitations de multidiffusion UDP (User Datagram Protocol) d’Azure.

## <a name="other-features-of-jboss-eap-on-azure"></a>Autres fonctionnalités de JBoss EAP sur Azure

JBoss EAP fournit des options préconfigurées pour les fonctionnalités telles que le clustering à haute disponibilité (HA), la messagerie et la mise en cache distribuée. Il permet également aux utilisateurs d’écrire, de déployer et d’exécuter des applications à l’aide des différents API et services fournis par JBoss EAP.

- **Compatibilité Jakarta EE** – Compatibilité Jakarta EE 8 pour les spécifications de plateforme complète et de profil web.

- **Compatibilité Java EE** – Compatibilité Java EE 8 pour les spécifications de plateforme complète et de profil web.

- **Console de gestion et interface CLI de gestion** – Destinées aux interfaces de gestion des serveurs autonomes. L’interface CLI de gestion comprend également un mode de traitement par lots qui peut générer des scripts et automatiser les tâches de gestion. Il n’est pas recommandé de modifier directement les fichiers XML de configuration de JBoss EAP.

- **Disposition simplifiée des répertoires** – Le répertoire de modules contient tous les modules de serveur d’applications. Les répertoires autonomes contiennent les artefacts et les fichiers de configuration pour les déploiements autonomes.

- **Mécanisme de chargement de classes modulaire**  – Les modules sont chargés et déchargés à la demande. Le mécanisme de chargement de classes modulaire améliore les performances, offre des avantages en matière de sécurité et réduit les durées de démarrage et de redémarrage.

- **Gestion rationalisée des sources de données** – Les pilotes de base de données sont déployés comme d’autres services. De plus, les sources de source sont créées et gérées à l’aide de la console de gestion et de l’interface CLI de gestion.

- **Infrastructure de sécurité unifiée** – Elytron fournit une infrastructure unifiée unique capable de gérer et de configurer l’accès aux serveurs autonomes. Elle permet également de configurer l’accès de sécurité pour les applications déployées sur des serveurs JBoss EAP.

## <a name="creating-your-microsoft-azure-environment"></a>Création de votre environnement Microsoft Azure

Créez les machines virtuelles qui hébergeront vos instances JBoss EAP dans votre environnement Microsoft Azure. Utilisez la taille de machine virtuelle Azure Standard_A2 ou une plus grande taille. Vous pouvez utiliser les images Azure Premium avec paiement à l’utilisation pour créer vos machines virtuelles ou créer manuellement vos propres machines virtuelles. Par exemple, vous pouvez déployer des machines virtuelles RHEL comme suit :

* Utilisation de l’image RHEL de la Place de marché à la demande dans Azure – Plusieurs offres sur la Place de marché Azure permettent de sélectionner la machine virtuelle RHEL que vous voulez pour configurer JBoss EAP. Consultez [Déployer une machine virtuelle RHEL 8 à partir de la Place de marché Azure](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/assembly_deploying-a-rhel-image-as-a-virtual-machine-on-microsoft-azure_cloud-content). Vous avez deux options pour choisir une licence de système d’exploitation RHEL sur la Place de marché Azure. Choisissez entre le mode PAYG (paiement à l’utilisation) ou BYOS (apportez votre propre abonnement) via le modèle Image Red Hat Gold. Notez que si vous avez déployé la machine virtuelle RHEL à l’aide du plan PAYG, seuls les détails de votre abonnement JBoss EAP sont utilisés pour inscrire le déploiement obtenu à un abonnement Red Hat.

* [Création et provisionnement manuels d’une image RHEL pour Azure](https://access.redhat.com/articles/uploading-rhel-image-to-azure). Utilisez la dernière version mineure de chaque version majeure de RHEL.

Pour les machines virtuelles Microsoft Windows Server, consultez la [documentation Microsoft Azure](../../windows/overview.md) sur la création d’une machine virtuelle Windows Server dans Azure.

## <a name="jboss-eap-installation"></a>Installation de JBoss EAP

> [!NOTE]
>  Si vous utilisez l’offre JBoss EAP sur RHEL via la Place de marché Azure, JBoss EAP est automatiquement installé et configuré pour l’environnement Azure.

Les étapes ci-dessous s’appliquent si vous déployez manuellement EAP sur une machine virtuelle RHEL déployée sur Microsoft Azure.

Une fois votre machine virtuelle configurée, vous pouvez installer JBoss EAP. Vous avez besoin d’un accès au [portail client Red Hat](https://access.redhat.com), qui est la plateforme centralisée de la base de connaissances Red Hat et des ressources d’abonnement. Si vous n’avez pas d’abonnement EAP, inscrivez-vous pour bénéficier d’un abonnement développeur gratuit par le biais de l’[abonnement Red Hat Developer pour les particuliers](https://developers.redhat.com/register). Une fois inscrit, recherchez les informations d’identification (ID de pool) dans la section Subscription (Abonnement) du [portail client Red Hat](https://access.redhat.com/management/). Notez que cet abonnement n’est pas destiné à une utilisation en production.

Nous avons utilisé la variable *EAP_HOME* pour indiquer le chemin d’accès à l’installation de JBoss EAP. Remplacez cette variable par le chemin d’accès réel à votre installation de JBoss EAP.

> [!IMPORTANT]
> Il existe différentes façons d’installer JBoss EAP. Chaque méthode est particulièrement adaptée à certaines situations. Si vous utilisez une machine virtuelle à la demande RHEL issue de la Place de marché Microsoft Azure, installez JBoss EAP à l’aide de la méthode ZIP ou avec programme d’installation. **N’inscrivez pas une machine virtuelle RHEL à la demande à Red Hat Subscription Management (RHSM), car cette machine virtuelle vous sera facturée deux fois, étant donné qu’elle utilise la méthode de facturation PAYG.

* **Installation ZIP** – L’archive ZIP peut être installée sur tous les systèmes d’exploitation pris en charge. La méthode d’installation ZIP doit être utilisée si vous souhaitez extraire l’instance manuellement. L’installation ZIP fournit une installation par défaut de JBoss EAP et toutes les configurations à effectuer après l’installation. Si vous envisagez d’utiliser le serveur JBoss Operations Network (ON) pour déployer et installer les correctifs JBoss EAP, vous devez installer les instances JBoss EAP cibles à l’aide de la méthode d’installation ZIP. Pour plus d’informations, reportez-vous à l’[installation zip](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#zip_installation).

* **Programme d’installation JAR** – Le programme d’installation JAR peut être exécuté dans une console ou en tant qu’assistant graphique. Les deux options fournissent des instructions pas à pas pour l’installation et la configuration de l’instance de serveur. Le programme d’installation JAR est la méthode recommandée pour installer JBoss EAP sur toutes les plateformes prises en charge. Pour plus d’informations, reportez-vous à [Installation avec le programme d’installation JAR](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#installer_installation).

* **Installation RPM** – JBoss EAP peut être installé à l’aide de packages RPM sur les installations prises en charge de RHEL 6, RHEL 7 et RHEL 8. La méthode d’installation RPM est idéale lorsque vous envisagez d’automatiser l’installation d’EAP sur votre machine virtuelle RHEL sur Azure. L’installation RPM de JBoss EAP installe tout ce qui est nécessaire pour exécuter JBoss EAP en tant que service. Pour plus d’informations, reportez-vous à [Installation RPM](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#rpm_installation).

## <a name="other-offers-by-azure-and-red-hat"></a>Autres offres avec Azure et Red Hat

Red Hat et Microsoft travaillent en partenariat pour proposer un ensemble de modèles de solutions Azure dans la Place de marché Azure visant à fournir un point de départ solide pour migrer vers Azure. Consultez cette documentation pour obtenir la liste des offres et choisissez un environnement approprié.

### <a name="azure-marketplace-offers"></a>Offres de la Place de marché Azure

Vous pouvez accéder à ces offres dans la [Place de marché Azure](https://aka.ms/AMP-JBoss-EAP).

Cette offre de la place de marché comprend diverses combinaisons des versions de JBoss EAP et de RHEL avec des modèles d’abonnement de support flexibles. JBoss EAP est toujours disponible en mode BYOS, mais pour le système d’exploitation RHEL, vous pouvez choisir entre les modes BYOS et PAYG. L’offre de la Place de marché Azure comprend des options de plan pour JBoss EAP sur RHEL en tant que machines virtuelles autonomes, machines virtuelles en cluster et groupes de machines virtuelles identiques en cluster. 

Les six plans sont les suivants :

- Machine virtuelle autonome JBoss EAP 7.3 sur RHEL 8.3 (PAYG)
- Machine virtuelle autonome JBoss EAP 7.3 sur RHEL 8.3 (BYOS)
- Machine virtuelle en cluster JBoss EAP 7.3 sur RHEL 8.3 (PAYG)
- Machine virtuelle en cluster JBoss EAP 7.3 sur RHEL 8.3 (BYOS)
- Groupes de machines virtuelles identiques en cluster JBoss EAP 7.3 sur RHEL 8.3 (PAYG)
- Groupes de machines virtuelles identiques clusterisés JBoss EAP 7.3 sur RHEL 8.3 (BYOS)

:::image type="content" source="./media/red-hat-marketplace-image-1.png" alt-text="Image montrant l’option permettant de déployer l’image Red Hat à l’aide du lien de déploiement GitHub.":::

### <a name="azure-quickstart-templates"></a>Modèles de démarrage rapide Microsoft Azure

Outre les offres de la Place de marché Azure, des modèles de démarrage rapide vous sont proposés comme version d’évaluation d’EAP sur Azure. Ces modèles de démarrage rapide incluent des modèles ARM et un script prédéfinis pour le déploiement de JBoss EAP sur Azure dans différentes configurations et combinaisons de versions. 

L’architecture de solution inclut :

* Machine virtuelle autonome JBoss EAP sur RHEL
* Machines virtuelles en cluster JBoss EAP sur RHEL
* Groupes de machines virtuelles identiques en cluster JBoss EAP sur RHEL

    :::image type="content" source="./media/red-hat-marketplace-image.png" alt-text="Image montrant les offres Red Hat disponibles via la Place de marché Azure":::

Vous pouvez choisir entre RHEL 7.7 et 8.0 et JBoss EAP 7.2 et 7.3. Vous pouvez sélectionner l’une des combinaisons suivantes pour le déploiement :

- JBoss EAP 7.2 sur RHEL 7.7
- JBoss EAP 7.2 sur RHEL 8.0
- JBoss EAP 7.3 sur RHEL 8.0

Pour commencer, sélectionnez un modèle de démarrage rapide avec une combinaison JBoss EAP sur RHEL correspondant à votre objectif de déploiement. Voici la liste des modèles de démarrage rapide disponibles.

* [Machine virtuelle autonome JBoss EAP sur RHEL](https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/) – Le modèle Azure déploie une application web nommée JBoss-EAP sur Azure sur JBoss EAP (version 7.2 ou 7.3) qui s’exécute sur une machine virtuelle RHEL (version 7.7 ou 8.0).

* [Machines virtuelles en cluster JBoss EAP sur RHEL](https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/) – Le modèle Azure déploie une application web appelée eap-session-replication sur un cluster JBoss EAP utilisant « n » machines virtuelles RHEL. « n » est le nombre initial de machines virtuelles que vous avez défini au début. Toutes les machines virtuelles sont ajoutées au pool de backends d’un équilibreur de charge.

* [Groupes de machines virtuelles identiques en cluster JBoss EAP sur RHEL](https://azure.microsoft.com/resources/templates/jboss-eap-clustered-vmss-rhel/) – Le modèle Azure déploie une application web appelée eap-session-replication sur un cluster JBoss EAP 7.2 ou 7.3 exécuté sur des instances de groupe de machines virtuelles identiques RHEL 7.7 ou 8.0.

## <a name="configuring-jboss-eap-to-work-on-cloud-platforms"></a>Configuration de JBoss EAP pour l’utiliser sur des plateformes cloud

Une fois que vous avez installé JBoss EAP sur votre machine virtuelle, vous pouvez configurer JBoss EAP pour qu’il s’exécute en tant que service. La configuration de JBoss EAP pour s’exécuter en tant que service dépend de la méthode d’installation de JBoss EAP et du type de système d’exploitation de machine virtuelle. Notez que l’installation RPM de JBoss EAP installe tout ce qui est nécessaire pour exécuter JBoss EAP en tant que service. Pour plus d’informations, consultez [Configuration de JBoss EAP pour l’exécuter en tant que service](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#configuring_jboss_eap_to_run_as_a_service).

### <a name="starting-and-stopping-jboss-eap"></a>Démarrage et arrêt de JBoss EAP

#### <a name="starting-jboss-eap"></a>Démarrage de JBoss EAP

JBoss EAP est pris en charge sur RHEL et Windows Server, et s’exécute uniquement en mode de fonctionnement de serveur autonome. La commande spécifique pour démarrer JBoss EAP dépend de la plateforme sous-jacente. Les serveurs sont initialement démarrés dans un état suspendu et n’acceptent aucune demande tant que tous les services requis n’ont pas démarré. À ce stade, les serveurs sont placés dans un état d’exécution normal et peuvent commencer à accepter des demandes. Voici la commande permettant de démarrer JBoss EAP en tant que serveur autonome :

- Commande pour démarrer JBoss EAP (installé via la méthode ZIP ou avec programme d’installation) en tant que serveur autonome sur la machine virtuelle RHEL :
    ```
    $EAP_HOME/bin/standalone.sh
    ```
- Pour Windows Server, utilisez le script `EAP_HOME\bin\standalone.bat` pour démarrer JBoss EAP en tant que serveur autonome.
- Le démarrage de JBoss EAP est différent pour une installation RPM par rapport à une installation par programme d’installation JAR ou ZIP. Par exemple, pour RHEL 7 et versions ultérieures, utilisez la commande suivante :
    ```
    systemctl start eap7-standalone.service
    ```
Le script de démarrage utilisé pour démarrer JBoss EAP (installé via la méthode ZIP ou avec programme d’installation) utilise le fichier `EAP_HOME/bin/standalone.conf`, ou `standalone.conf.bat` pour Windows Server, pour définir certaines préférences par défaut, telles que les options JVM. Personnalisez les paramètres dans ce fichier. JBoss EAP utilise le fichier de configuration `standalone.xml` par défaut, mais peut être démarré à l’aide d’un autre fichier. Pour modifier le fichier de configuration par défaut utilisé pour démarrer JBoss EAP installé via la méthode RPM, utilisez `/etc/opt/rh/eap7/wildfly/eap7-standalone.conf`. Utilisez le même fichier eap7-standalone.conf pour apporter d’autres modifications de configuration, telles que l’adresse de liaison WildFly.

Pour plus d’informations sur les fichiers de configuration autonomes disponibles et leur utilisation, consultez les [fichiers de configuration du serveur autonome](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#standalone_server_configuration_files).

Pour démarrer JBoss EAP avec une configuration différente, utilisez l’argument --server-config. Pour obtenir la liste complète de tous les arguments de script de démarrage disponibles et leurs objectifs, utilisez l’argument --help ou consultez [Arguments d’exécution du serveur](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#reference_of_switches_and_arguments_to_pass_at_server_runtime).

#### <a name="stopping-jboss-eap"></a>Arrêt de JBoss EAP

La façon dont vous arrêtez JBoss EAP dépend de la façon dont il a été démarré. Appuyez sur `Ctrl+C` dans le terminal où JBoss EAP a démarré pour arrêter une instance interactive de JBoss EAP. Pour arrêter l’instance en arrière-plan de JBoss EAP, utilisez l’interface CLI de gestion pour vous connecter à l’instance en cours d’exécution et arrêter le serveur. Pour plus d’informations, consultez [Arrêt de JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#stopping_jboss_eap).

L’arrêt de JBoss EAP est différent pour une installation RPM par rapport à une installation par programme d’installation ou ZIP. La commande d’arrêt d’une installation RPM de JBoss EAP dépend du mode de fonctionnement que vous souhaitez démarrer et de la version RHEL que vous exécutez. Le mode autonome est le seul mode pris en charge dans Azure. 

- Par exemple, pour RHEL 7 et versions ultérieures, utilisez la commande suivante :
    ```
    systemctl stop eap7-standalone.service
    ```
JBoss EAP peut également être suspendu ou arrêté correctement, ce qui permet de terminer normalement les demandes actives, sans accepter de nouvelles demandes. Une fois que le serveur a été interrompu, il peut être arrêté, ramené à l’état En cours d’exécution ou laissé dans un état suspendu pour effectuer la maintenance. Quand le serveur est suspendu, les demandes de gestion continuent d’être traitées. La suspension et la reprise du serveur sont possibles via la console de gestion ou l’interface CLI de gestion.

### <a name="configuring-jboss-eap-subsystems-to-work-on-cloud-platforms"></a>Configuration des sous-systèmes JBoss EAP pour les utiliser sur des plateformes cloud

Un grand nombre des API et des fonctionnalités exposées aux applications déployées sur JBoss EAP sont organisées en sous-systèmes. Ces sous-systèmes peuvent être configurés par les administrateurs pour fournir un comportement différent, en fonction de l’objectif de l’application. Pour plus d’informations sur les sous-systèmes, consultez [Sous-systèmes JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#jboss_eap_subsystems).

Certains sous-systèmes JBoss EAP nécessitent une configuration pour fonctionner correctement sur les plateformes cloud. Une configuration est requise car un serveur JBoss EAP est lié à l’adresse IP privée d’une machine virtuelle cloud. L’adresse IP privée n’est visible que depuis la plateforme cloud. Pour certains sous-systèmes, l’adresse IP privée doit être mappée à l’adresse IP publique du serveur, qui est visible depuis l’extérieur du cloud. Pour plus d’informations sur la modification de ces sous-systèmes, consultez [Configuration des sous-systèmes JBoss EAP pour les utiliser sur des plateformes cloud](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/using_jboss_eap_in_microsoft_azure/index#configuring_subsystems_for_cloud_platforms).

## <a name="using-jboss-eap-high-availability-in-microsoft-azure"></a>Utilisation de la haute disponibilité de JBoss EAP dans Microsoft Azure

Azure ne prend pas en charge les protocoles de découverte JGroups basés sur la multidiffusion UDP. JGroups utilise la pile UDP par défaut. Veillez à la remplacer par TCP, car Azure ne prend pas en charge le protocole UDP. Vous pouvez utiliser d’autres protocoles de découverte JGroups comme TCPPING ou JDBC_PING, mais nous vous recommandons le protocole de découverte de fichiers partagés développé pour Azure, *Azure_PING*.

*AZURE_PING* utilise un conteneur d’objets blob commun dans un compte de stockage Microsoft Azure. Si vous n’avez pas encore de conteneur d’objets blob qu’AZURE_PING peut utiliser, créez-en un accessible par votre machine virtuelle. Pour plus d’informations, consultez [Configuration de la haute disponibilité de JBoss EAP dans Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/using_jboss_eap_in_microsoft_azure/index#using_jboss_eap_high_availability_in_microsoft_azure).

Configurez JBoss EAP avec l’environnement d’équilibrage de charge. Assurez-vous que tous les équilibreurs et les rôles de travail sont liés à des adresses IP accessibles dans votre réseau virtuel Microsoft Azure (VNet) interne. Pour plus d’informations sur la configuration de l’équilibrage de charge, consultez [Installation et configuration d’un cluster à haute disponibilité Red Hat Enterprise Linux 7.4 (et versions ultérieures) sur Microsoft Azure](https://access.redhat.com/articles/3252491).

## <a name="other-best-practices"></a>Autres bonnes pratiques

- En tant qu’administrateur d’une installation JBoss EAP sur une machine virtuelle, il est important de vous assurer que votre machine virtuelle est sécurisée. Cela réduit de manière significative le risque de voir vos systèmes d’exploitation invités et hôtes infectés par des logiciels malveillants. La sécurisation de votre machine virtuelle réduit les attaques sur JBoss EAP et les dysfonctionnements des applications hébergées sur JBoss EAP. Contrôlez l’accès aux machines virtuelles Azure à l’aide de fonctionnalités telles qu’[Azure Policy](https://azure.microsoft.com/services/azure-policy/) et les [rôles intégrés Azure](../../../role-based-access-control/built-in-roles.md) dans le [Contrôle d’accès en fonction du rôle (RBAC)](../../../role-based-access-control/overview.md). Protégez votre machine virtuelle contre les programmes malveillants en installant Microsoft Antimalware ou une solution de protection de point de terminaison des partenaires Microsoft, et en intégrant votre solution anti-programme malveillant avec [Microsoft Defender pour le cloud](https://azure.microsoft.com/services/security-center/) pour surveiller l’état de votre protection. Sur les machines virtuelles RHEL, vous pouvez la protéger en bloquant le transfert de port et la connexion racine, qui peut être désactivée dans */


- Utilisez des variables d’environnement pour faciliter et lisser votre expérience avec JBoss EAP sur les machines virtuelles Azure. Par exemple, vous pouvez utiliser EAP_HOME pour désigner le chemin vers l’installation de JBoss EAP, qui sera utilisé plusieurs fois. Dans ce cas, les variables d’environnement sont utiles. Les variables d’environnement sont également un moyen courant de configurer les services et de gérer les secrets des applications web. Lorsqu’une variable d’environnement est définie à partir de l’interpréteur de commandes à l’aide de la commande d’exportation, son existence prend fin quand la session de l’utilisateur se termine. Cela pose problème quand la variable doit être conservée entre les sessions. Pour rendre un environnement persistant pour l’environnement d’un utilisateur, nous exportons la variable à partir du script de profil de l’utilisateur. Ajoutez la commande d’exportation pour chaque variable d’environnement que vous souhaitez conserver dans bash_profile. Si vous souhaitez définir une variable d’environnement globale permanente pour tous les utilisateurs qui ont accès à la machine virtuelle, vous pouvez l’ajouter au profil par défaut. Il est recommandé de stocker les variables d’environnement globales dans un répertoire nommé `/etc/profile.d`. Ce répertoire contient la liste des fichiers utilisés pour définir les variables d’environnement de l’ensemble du système. L’utilisation de la commande set pour définir une variable d’environnement système dans une invite de commandes Windows Server ne définit pas de façon permanente la variable d’environnement. Utilisez la commande *setx* ou l’interface Système dans le Panneau de configuration.

- Gérez les mises à jour et les mises à niveau de vos machines virtuelles. Utilisez la solution [Update Management](../../../automation/update-management/overview.md) dans Azure Automation pour gérer les mises à jour des systèmes d’exploitation pour vos ordinateurs Windows et Linux déployés dans Azure. Évaluez rapidement l’état des mises à jour disponibles sur tous les ordinateurs d’agent et gérez le processus d’installation des mises à jour requises pour les serveurs. La mise à jour du logiciel de machine virtuelle garantit que les correctifs Microsoft Azure importants, les pilotes d’hyperviseur et les packages logiciels restent à jour. Les mises à niveau sur place sont possibles pour les versions mineures. Par exemple, de RHEL 6.9 à RHEL 6.10 ou de RHEL 7.3 à RHEL 7.4. Une mise à niveau de type sur place peut être effectuée en exécutant la commande *yum update*. Microsoft Azure ne prend pas en charge la mise à niveau sur place d’une version majeure, par exemple, de RHEL 6 à RHEL 7.

- Utilisez [Azure Monitor](../../../azure-monitor/data-platform.md) pour obtenir plus de visibilité sur l’intégrité de votre ressource. Les fonctionnalités Azure Monitor incluent les [fichiers journaux de diagnostic des ressources](../../../azure-monitor/essentials/platform-logs-overview.md). Ils vous permettent de surveiller les ressources de vos machines virtuelles et d’identifier les problèmes potentiels susceptibles de nuire à la disponibilité et aux performances. L’[extension Diagnostics Azure](../../../azure-monitor/agents/diagnostics-extension-overview.md) fournit des fonctionnalités de surveillance et de diagnostic sur les machines virtuelles Windows. Activez ces fonctionnalités en intégrant l’extension dans le modèle Azure Resource Manager. Activez les diagnostics de démarrage, qui constituent un outil important à utiliser lors du dépannage d’une machine virtuelle qui ne démarre pas. La sortie de la console et le journal de démarrage peuvent grandement aider les techniciens du support technique de Red Hat lors de la résolution d’un problème de démarrage. Activez les diagnostics de démarrage dans le portail Microsoft Azure lors de la création d’une machine virtuelle ou sur une machine virtuelle existante. Une fois activés, vous pouvez afficher la sortie de la console pour la machine virtuelle et télécharger le journal de démarrage pour la résolution des problèmes.

- Une autre façon de garantir une communication sécurisée consiste à utiliser un point de terminaison privé dans votre [réseau virtuel (VNet)](../../../virtual-network/virtual-networks-overview.md) et vos [réseaux privés virtuels (VPN)](../../../vpn-gateway/vpn-gateway-about-vpngateways.md). Les réseaux ouverts sont accessibles au monde extérieur et, en tant que tels, vulnérables aux attaques d’utilisateurs malveillants. Le réseau virtuel (VNet) et le VPN restreignent l’accès aux utilisateurs sélectionnés. Le réseau virtuel utilise une IP privée pour établir des canaux de communication isolés entre les serveurs au sein de la même plage. La communication isolée permet à plusieurs serveurs sous le même compte d’échanger des informations et des données sans les exposer à un espace public. Connectez-vous à un serveur distant si vous procédez localement via un réseau privé. Il existe des méthodes différentes, telles que l’utilisation d’une JumpVM/JumpBox dans le même réseau virtuel que celui du serveur d’applications ou l’utilisation du [peering de réseaux virtuels Azure](../../../virtual-network/virtual-network-peering-overview.md), d’[Azure Application Gateway](../../../application-gateway/overview.md), d’[Azure Bastion](https://azure.microsoft.com/services/azure-bastion), etc. Toutes ces méthodes permettent une connexion entièrement privée et sécurisée et peuvent connecter plusieurs serveurs distants.

- Utilisez des [groupes de sécurité réseau (NSG) Azure](../../../virtual-network/network-security-groups-overview.md) pour filtrer le trafic réseau vers et depuis le serveur d’applications dans le réseau virtuel Azure. Un groupe de sécurité réseau contient des règles de sécurité qui autorisent ou refusent le trafic réseau entrant ou sortant vers/depuis plusieurs types de ressources Azure. Pour chaque règle, vous pouvez spécifier la source et la destination, le port et le protocole. Protégez votre application sur JBoss EAP en utilisant ces règles NSG et bloquez ou autorisez les ports sur Internet.

- Pour de meilleures fonctionnalités de l’application exécutée sur JBoss EAP sur Azure, vous pouvez utiliser la fonctionnalité de haute disponibilité disponible dans Azure. La haute disponibilité dans Azure peut être obtenue à l’aide de ressources Azure telles qu’un équilibreur de charge, une passerelle applicative ou un [groupe de machines virtuelles identiques](../../../virtual-machine-scale-sets/overview.md). Ces méthodes de haute disponibilité assurent la redondance et améliorent les performances, ce qui vous permet d’effectuer facilement les tâches de maintenance et de mettre à jour une instance d’application, en distribuant la charge à une autre instance d’application disponible. Pour satisfaire la demande de clients supplémentaires, vous devrez peut-être augmenter le nombre d’instances d’application qui exécutent votre application. Le groupe de machines virtuelles identiques dispose également d’une fonctionnalité de mise à l’échelle automatique, qui permet à votre application d’effectuer automatiquement un scale-up ou un scale-down quand la demande varie.

## <a name="optimizing-the-jboss-eap-server-configuration"></a>Optimisation de la configuration du serveur JBoss EAP

Une fois que vous avez installé le serveur JBoss EAP et que vous avez créé un utilisateur de gestion, vous pouvez optimiser la configuration de votre serveur. Veillez à consulter le [Guide d’optimisation des performances](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/performance_tuning_guide/index) pour savoir comment optimiser la configuration du serveur afin d’éviter les problèmes courants lors du déploiement d’applications dans un environnement de production.

## <a name="resource-links-and-support"></a>Liens vers les ressources et support

Pour toute question relative au support technique, aux problèmes ou aux exigences de personnalisation, contactez le [support technique Red Hat](https://access.redhat.com/support) ou le [support Microsoft Azure](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

* En savoir plus sur [JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html/getting_started_with_jboss_eap_for_openshift_online/index)
* Red Hat Subscription Manager (RHSM) – [Cloud Access](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html-single/red_hat_cloud_access_reference_guide/index)
* [Azure Red Hat OpenShift (ARO)](https://azure.microsoft.com/services/openshift/)
* Documentation Microsoft sur [Red Hat sur Azure](./overview.md)
* [Images RHEL BYOS Gold dans Azure](./byos.md)
* [Vidéo de tutoriel de démarrage rapide](https://www.youtube.com/watch?v=3DgpVwnQ3V4) pour JBoss EAP sur Azure 

## <a name="next-steps"></a>Étapes suivantes

* [Demande d’informations sur la migration vers JBoss EAP sur Azure](https://aka.ms/JavaCloud)
* Exécution de JBoss EAP dans [Azure App Service](/azure/developer/java/ee/jboss-on-azure)
* Déployer JBoss EAP sur une machine virtuelle ou un groupe de machines virtuelles identiques RHEL à partir de la [Place de marché Azure](https://aka.ms/AMP-JBoss-EAP)
* Déployer JBoss EAP sur une machine virtuelle ou un groupe de machines virtuelles identiques RHEL à partir d’un [modèle de démarrage rapide Azure](https://aka.ms/Quickstart-JBoss-EAP)
* Utiliser l’[Assistant Migration d’Azure App Service](https://azure.microsoft.com/services/app-service/migration-assistant/)
* Utiliser Red Hat [Migration Toolkit for Applications](https://developers.redhat.com/products/mta)
