---
title: Présentation de l’agent Connected Machine
description: Cet article fournit une présentation détaillée de l’agent des serveurs avec Azure Arc disponible, qui prend en charge la surveillance de machines virtuelles hébergées dans des environnements hybrides.
ms.date: 11/03/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 30c9a32cabf24e54dd41d45d40a8ff07920fd919
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546915"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Présentation de l’agent des serveurs avec Azure Arc

L’agent Connected Machine des serveurs avec Azure Arc vous permet de gérer vos machines Windows et Linux hébergées en dehors d’Azure sur votre réseau d’entreprise ou un autre fournisseur de cloud. Cet article propose une présentation détaillée des exigences en matière d'agent, de système et de réseau, ainsi que des différentes méthodes de déploiement.

>[!NOTE]
> L’[agent Azure Monitor](../../azure-monitor/agents/azure-monitor-agent-overview.md) (AMA) ne remplace pas l’agent Connected Machine. Il remplace en revanche l’agent Log Analytics, l’extension Diagnostics et l’agent Telegraf pour les machines Windows et Linux. Pour plus d’informations, consultez la documentation Azure Monitor sur le nouvel agent.

## <a name="agent-component-details"></a>Détails du composant Agent

:::image type="content" source="media/agent-overview/connected-machine-agent.png" alt-text="Vue d’ensemble de l’agent de serveurs avec Azure Arc." border="false":::

Le package d’agent Azure Connected Machine contient plusieurs composants logiques qui sont regroupés ensemble.

* Le service HIMDS (Hybrid Instance Metadata service) gère la connexion à Azure et l’identité Azure de l’ordinateur connecté.

* L’agent de configuration d’invité fournit des fonctionnalités, comme l’évaluation de la conformité de l’ordinateur avec les stratégies requises et l’implémentation de la conformité.

    Notez le comportement suivant avec la [configuration d’invité](../../governance/policy/concepts/guest-configuration.md) Azure Policy pour un ordinateur déconnecté :

  * Une attribution Azure Policy qui cible les ordinateurs déconnectés n’est pas affectée.
  * L’attribution d’invité est stockée localement pendant 14 jours. Pendant la période de 14 jours, si l’agent Connected Machine se reconnecte au service, les attributions de stratégie sont réappliquées.
  * Les attributions sont supprimées au bout de 14 jours et ne sont pas réattribuées à l’ordinateur après la période de 14 jours.

* L’agent Extension gère les extensions de machine virtuelle, notamment l’installation, la désinstallation et la mise à niveau. Les extensions sont téléchargées à partir d’Azure et copiées dans le dossier `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` sur Windows et `/opt/GC_Ext/downloads` pour Linux. Sur Windows, l’extension est installée dans le chemin suivant `%SystemDrive%\Packages\Plugins\<extension>` et sur Linux, l’extension est installée dans `/var/lib/waagent/<extension>`.

## <a name="instance-metadata"></a>Métadonnées d’instance

Les informations de métadonnées sur la machine connectée sont collectées une fois que l’agent Connected Machine s’est inscrit auprès des serveurs avec Azure Arc. Plus précisément :

* Nom, type et version du système d’exploitation
* Nom de l'ordinateur
* Fabricant et modèle de l’ordinateur
* Nom de domaine complet (FQDN) de l’ordinateur
* Nom de domaine (s’il est joint à un domaine Active Directory)
* Version de l’agent Machine connectée
* Nom de domaine complet (FQDN) Active Directory et DNS
* UUID (ID BIOS)
* Pulsation de l'agent Connected Machine
* Version de l’agent Machine connectée
* Clé publique pour l’identité managée
* État de conformité de la stratégie et détails (si vous utilisez des stratégies de configuration invitées)
* SQL Server installé (valeur booléenne)
* ID de ressource de cluster (pour les nœuds Azure Stack HCI)

Les informations de métadonnées suivantes sont demandées par l’agent à partir d’Azure :

* Emplacement de la ressource (ressource)
* ID de machine virtuelle
* Balises
* Certificat Managed Identity Azure Active Directory
* Affectations de la stratégie de configuration invitée
* Demandes d’extension : installation, mise à jour et suppression.

## <a name="download-agents"></a>Télécharger les agents

Vous pouvez télécharger le package de l’agent Azure Connected Machine pour Windows et Linux à partir des emplacements listés ci-dessous.

* [Package Windows Installer de l’agent pour Windows](https://aka.ms/AzureConnectedMachineAgent) à partir du centre de téléchargement Microsoft.

* Le package de l’agent pour Linux est distribué à partir du [dépôt de packages](https://packages.microsoft.com/) de Microsoft à l’aide du format de package préféré pour la distribution (.RPM ou .DEB).

L’agent Azure Connected Machine pour Windows et Linux peut être mis à niveau vers la dernière version de façon manuelle ou automatique selon vos besoins. Vous pourrez trouver plus d’informations [ici](manage-agent.md).

## <a name="prerequisites"></a>Prérequis

### <a name="supported-environments"></a>Environnements pris en charge

Les serveurs avec Azure Arc prennent en charge l’installation de l’agent Connected Machine sur tous les serveurs physiques et machines virtuelles hébergés *en dehors* d’Azure. Cela comprend la prise en charge des machines virtuelles s’exécutant sur des plateformes comme :

* VMware
* Azure Stack HCI
* Autres environnements cloud

Les serveurs avec Azure Arc *ne prennent pas en charge* l’installation de l’agent sur les machines virtuelles qui s’exécutent dans Azure, ni sur les machines virtuelles qui s’exécutent sur Azure Stack Hub ou Azure Stack Edge, car elles sont déjà modélisées comme des machines virtuelles Azure.

### <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge

Les versions suivantes des systèmes d’exploitation Windows et Linux sont officiellement prises en charge pour l’agent Azure Connected Machine :

* Windows Server 2008 R2 SP1, Windows Server 2012 R2, 2016, 2019, et 2022 (dont Server Core)
* Ubuntu 16.04, 18.04 et 20.04 LTS (x64)
* CentOS Linux 7 et 8 (x64)
* SUSE Linux Enterprise Server (SLES) 12 et 15 (x64)
* Red Hat Enterprise Linux (RHEL) 7 et 8 (x64)
* Amazon Linux 2 (x64)
* Oracle Linux 7

> [!WARNING]
> Le nom d’hôte Linux ou le nom de l’ordinateur Windows ne peuvent pas contenir de mots réservés ni de marques dans le nom. Dans le cas contraire, la tentative d’inscription de la machine connectée auprès d’Azure se solde par un échec. Consultez [Résoudre les erreurs de nom de ressource réservé](../../azure-resource-manager/templates/error-reserved-resource-name.md) pour obtenir la liste des mots réservés.

> [!NOTE]
> Alors que les serveurs avec Azure Arc prennent en charge Amazon Linux, les composants suivants ne prennent pas en charge cette distribution :
>
> * L’agent de dépendances utilisé par Azure Monitor VM Insights
> * Azure Automation Update Management

### <a name="software-requirements"></a>Configuration logicielle requise

* .NET Framework 4.6 ou version ultérieure est requis. [Téléchargez .NET Framework](/dotnet/framework/install/guide-for-developers).
* Windows PowerShell 5.1 est requis. [Téléchargez Windows Management Framework 5.1](https://www.microsoft.com/download/details.aspx?id=54616).

### <a name="required-permissions"></a>Autorisations requises

* Pour intégrer des ordinateurs, vous devez être membre du rôle **Intégration Azure Connected Machine** ou [Contributeur](../../role-based-access-control/built-in-roles.md#contributor) dans le groupe de ressources.

* Pour lire, modifier et supprimer un ordinateur, vous devez être membre du rôle **Administrateur des ressources Azure Connected Machine** dans le groupe de ressources.

* Pour sélectionner un groupe de ressources dans la liste déroulante lors de l’utilisation de la méthode de **génération de script**, vous devez être au minimum membre du rôle [Lecteur](../../role-based-access-control/built-in-roles.md#reader) pour ce groupe de ressources.

### <a name="azure-subscription-and-service-limits"></a>Limites du service et de l’abonnement Azure

Avant de configurer vos machines avec des serveurs avec Azure Arc, examinez les [limites de l’abonnement](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) et les [limites des groupes de ressources](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) d’Azure Resource Manager pour planifier le nombre de machines à connecter.

Les serveurs avec Azure Arc prennent en charge jusqu’à 5 000 instances de machine dans un groupe de ressources.

### <a name="register-azure-resource-providers"></a>Inscrire des fournisseurs de ressources Azure

Pour utiliser ce service, les serveurs avec Azure Arc dépendent des fournisseurs de ressources Azure suivants de votre abonnement :

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

S’ils ne sont pas inscrits, vous pouvez les inscrire à l’aide des commandes suivantes :

Azure PowerShell :

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI :

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Vous pouvez également inscrire les fournisseurs de ressources dans le portail Azure en suivant les étapes décrites sous [Portail Azure](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="transport-layer-security-12-protocol"></a>Protocole Transport Layer Security 1.2

Pour garantir la sécurité des données en transit vers Azure, nous vous encourageons vivement à configurer la machine de manière à utiliser le protocole TLS (Transport Layer Security) 1.2. Les versions antérieures de TLS/SSL (Secure Sockets Layer) se sont avérées vulnérables et bien qu’elles fonctionnent encore pour assurer la compatibilité descendante, elles sont **déconseillées**.

|Plateforme/Langage | Support | Informations complémentaires |
| --- | --- | --- |
|Linux | Les distributions de Linux s’appuient généralement sur [OpenSSL](https://www.openssl.org) pour la prise en charge de TLS 1.2. | Vérifiez [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) pour vous assurer que votre version d’OpenSSL est prise en charge.|
| Windows Server 2012 R2 et versions ultérieures | Pris en charge, activé par défaut. | Pour confirmer que vous utilisez toujours les [paramètres par défaut](/windows-server/security/tls/tls-registry-settings).|

## <a name="networking-configuration"></a>Configuration de la mise en réseau

L’agent Connected Machine pour Linux et Windows communique en sortie de manière sécurisée vers Azure Arc sur le port TCP 443. Si la machine doit se connecter par l’intermédiaire d’un pare-feu ou d’un serveur proxy pour communiquer sur Internet, l’agent communique à sa place en utilisant le protocole HTTP. Les serveurs proxy ne sécurisent pas davantage l’agent Connected Machine, car le trafic est déjà chiffré.

Pour sécuriser davantage votre connectivité réseau à Azure Arc, au lieu d’utiliser des réseaux publics et des serveurs proxy, vous pouvez implémenter une [étendue de liaison privée Azure Arc](private-link-security.md) (préversion).

> [!NOTE]
> Les serveurs avec Azure Arc ne prennent pas en charge l’utilisation d’une [passerelle Log Analytics](../../azure-monitor/agents/gateway.md) comme proxy pour l’agent Connected Machine.
>

Si la connectivité sortante est restreinte par votre pare-feu ou votre serveur proxy, vérifiez que les URL listées ci-dessous ne sont pas bloquées. Lorsque vous n’autorisez que les plages d’adresses IP ou les noms de domaine nécessaires pour que l’agent communique avec le service, vous devez également autoriser l’accès aux URL et étiquettes de service suivantes.

Balises de service :

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure
* Stockage

URL :

| Ressource de l’agent | Description |
|---------|---------|
|`azgn*.servicebus.windows.net`|Plateforme Azure Arc Connectivity|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`pas.windows.net`|Azure Active Directory|
|`*.guestconfiguration.azure.com` |Services d’extension et de configuration Invité|
|`*.his.arc.azure.com`|Services de métadonnées et d’identité hybride|
|`*.blob.core.windows.net`|Source de téléchargement pour les extensions de serveurs avec Azure Arc|
|`dc.services.visualstudio.com`|Télémétrie de l’agent|

Pour obtenir la liste d’adresses IP de chaque balise de service/région, consultez le fichier JSON - [Plages d’adresses IP Azure et balises de service – Cloud Public](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft publie chaque semaine une mise à jour contenant chacun des services Azure et les plages d’adresses IP qu’il utilise. Ces informations dans le fichier JSON constituent la liste actuelle des plages d’adresses IP qui correspondent à chaque balise de service. Les adresses IP sont sujettes à modification. Si des plages d’adresses IP sont requises par la configuration de votre pare-feu, utilisez l’étiquette de service **AzureCloud** pour autoriser l’accès à tous les services Azure. Ne désactivez pas la supervision ou l’inspection de la sécurité de ces URL ; autorisez-les comme vous le feriez pour tout autre trafic Internet.

Pour plus d’informations, consultez [Vue d’ensemble des étiquettes de service](../../virtual-network/service-tags-overview.md).

## <a name="installation-and-configuration"></a>Installation et configuration

Selon vos besoins, plusieurs méthodes vous permettent de connecter des machines de votre environnement hybride directement à Azure. Le tableau suivant décrit chacune d’entre elle, pour vous permettre d’identifier la plus adaptée à votre organisation.

> [!IMPORTANT]
> L’agent Connected Agent ne peut pas être installé sur une machine virtuelle Windows Azure. Si vous tentez d’effectuer cette opération, l’installation la détecte et la restaure.

| Méthode | Description |
|--------|-------------|
| de manière interactive, | Installer manuellement l’agent sur une seule machine ou un petit nombre de machines en suivant les étapes décrites dans [Connecter des machines à partir du portail Azure](onboard-portal.md).<br> À partir du portail Azure, vous pouvez générer un script et l’exécuter sur la machine pour automatiser les étapes d’installation et de configuration de l’agent.|
| À grande échelle | Installer et configurer l’agent pour plusieurs machines en suivant les instructions indiquées dans [Connecter des machines à l’aide d’un principal de service](onboard-service-principal.md).<br> Cette méthode crée un principal de service pour connecter des machines de manière non interactive.|
| À grande échelle | Installez et configurez l’agent pour plusieurs machines en suivant la méthode [Connecter des machines hybrides à Azure à partir d’Automation Update Management](onboard-update-management-machines.md).<br> Cette méthode crée un principal de service, installe et configure l’agent pour plusieurs machines gérées avec Azure Automation Update Management pour connecter des machines de manière non interactive. |
| À grande échelle | Installer et configurer l’agent pour plusieurs machines en suivant les instructions de la méthode [Utilisation de Windows PowerShell DSC](onboard-dsc.md).<br> Cette méthode utilise un principal de service pour connecter des machines de manière non interactive à PowerShell DSC. |

## <a name="connected-machine-agent-technical-overview"></a>Présentation technique de l’agent Machine connectée

### <a name="windows-agent-installation-details"></a>Détails de l’installation de l’agent Windows

Vous pouvez installer l’agent Connected Machine pour Windows à l’aide de l’une des trois méthodes suivantes :

* En double-cliquant sur le fichier `AzureConnectedMachineAgent.msi`.
* Manuellement en exécutant le package Windows Installer `AzureConnectedMachineAgent.msi` à partir de l’interpréteur de commandes.
* À partir d’une session PowerShell à l’aide d’une méthode de script.

Une fois l’agent Connected Machine pour Windows installé, les modifications de configuration système suivantes sont appliquées.

* Les dossiers d’installation suivants sont créés lors de la configuration.

    |Dossier |Description |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |CLI azcmagent et exécutables du service de métadonnées d’instance.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\GC | Exécutables du service d’extension.|
    |%ProgramFiles%\AzureConnectedMachineAgent\GuestConfig\GC | Exécutables du service de configuration Invité (stratégie).|
    |%ProgramData%\AzureConnectedMachineAgent |Fichiers de configuration, de journal et de jeton d’identité pour azcmagent CLI et service de métadonnées d’instance.|
    |%ProgramData%\GuestConfig |Téléchargements de packages d’extension, téléchargements de configuration Invité (stratégie) et journaux pour les services d’extension et de configuration Invité.|

* Les services Windows suivants sont créés sur la machine cible lors de l’installation de l’agent.

    |Nom du service |Nom complet |Nom du processus |Description |
    |-------------|-------------|-------------|------------|
    |himds |Azure Hybrid Instance Metadata Service |himds |Ce service implémente le service Hybrid Instance Metadata Service pour gérer la connexion à Azure et l’identité Azure de l’ordinateur connecté.|
    |GCArcService |Service Arc de configuration d’invité |gc_service |Analyse la configuration de l’état souhaité de la machine.|
    |ExtensionService |Service d’extension de la configuration d’invité | gc_service |Installe les extensions requises ciblant la machine.|

* Les variables d’environnement suivantes sont créées lors de l’installation de l’agent.

    |Nom |Valeur par défaut |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |<`http://localhost:40342/metadata/identity/oauth2/token`> ||
    |IMDS_ENDPOINT |<`http://localhost:40342`> ||

* Plusieurs fichiers journaux sont disponibles pour la résolution des problèmes. Ils sont décrits dans le tableau suivant.

    |Journal |Description |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Enregistre les détails de la pulsation et du composant de l’agent d’identité.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Contient la sortie des commandes de l’outil azcmagent.|
    |%ProgramData%\GuestConfig\arc_policy_logs\ |Enregistre des détails concernant le composant d’agent de configuration Invité (stratégie).|
    |%ProgramData%\GuestConfig\ext_mgr_logs|Enregistre les détails concernant le composant de l’agent Extension.|
    |%ProgramData%\GuestConfig\extension_logs\\\<Extension>|Enregistre les détails de l’extension installée.|

* Le groupe de sécurité local **Applications d’extension de l’agent hybride** est créé.

* Lors de la désinstallation de l’agent, les artefacts suivants ne sont pas supprimés.

  * %ProgramData%\AzureConnectedMachineAgent\Log
  * %ProgramData%\AzureConnectedMachineAgent et sous-répertoires
  * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Détails de l’installation de l’agent Linux

L’agent Connected Machine pour Linux est fourni dans le format de package par défaut pour la distribution (. RPM ou. DEB) qui est hébergée dans le [dépôt de packages](https://packages.microsoft.com/) de Microsoft. L’agent est installé et configuré avec le groupe de scripts d’interpréteur de commandes [Install_linux_azcmagent.sh](https://aka.ms/azcmagent).

Une fois l’agent Connected Machine pour Linux installé, les modifications de configuration système suivantes sont appliquées.

* Les dossiers d’installation suivants sont créés lors de la configuration.

    |Dossier |Description |
    |-------|------------|
    |/opt/azcmagent/ |CLI azcmagent et exécutables du service de métadonnées d’instance.|
    |/opt/GC_Ext/ | Exécutables du service d’extension.|
    |/opt/GC_Service/ |Exécutables du service de configuration Invité (stratégie).|
    |/var/opt/azcmagent/ |Fichiers de configuration, de journal et de jeton d’identité pour azcmagent CLI et service de métadonnées d’instance.|
    |/var/lib/GuestConfig/ |Téléchargements de packages d’extension, téléchargements de configuration Invité (stratégie) et journaux pour les services d’extension et de configuration Invité.|

* Les démons suivants sont créés sur la machine cible lors de l’installation de l’agent.

    |Nom du service |Nom complet |Nom du processus |Description |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Service Azure Connected Machine Agent |himds |Ce service implémente le service Hybrid Instance Metadata Service pour gérer la connexion à Azure et l’identité Azure de l’ordinateur connecté.|
    |gcad.service |Service Arc GC |gc_linux_service |Analyse la configuration de l’état souhaité de la machine. |
    |extd.service |Service d’extension |gc_linux_service | Installe les extensions requises ciblant la machine.|

* Plusieurs fichiers journaux sont disponibles pour la résolution des problèmes. Ils sont décrits dans le tableau suivant.

    |Journal |Description |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Enregistre les détails de la pulsation et du composant de l’agent d’identité.|
    |/var/opt/azcmagent/log/azcmagent.log |Contient la sortie des commandes de l’outil azcmagent.|
    |/var/lib/GuestConfig/arc_policy_logs |Enregistre des détails concernant le composant d’agent de configuration Invité (stratégie).|
    |/var/lib/GuestConfig/ext_mgr_logs |Enregistre les détails concernant le composant de l’agent d’extension.|
    |/var/lib/GuestConfig/extension_logs|Enregistre les détails des opérations d’installation, de mise à jour et de désinstallation de l’extension.|

* Les variables d’environnement suivantes sont créées lors de l’installation de l’agent. Les variables suivantes sont définies dans `/lib/systemd/system.conf.d/azcmagent.conf`.

    |Nom |Valeur par défaut |Description |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |<`http://localhost:40342/metadata/identity/oauth2/token`> ||
    |IMDS_ENDPOINT |<`http://localhost:40342`> ||

* Lors de la désinstallation de l’agent, les artefacts suivants ne sont pas supprimés.

  * /var/opt/azcmagent
  * /var/lib/GuestConfig

### <a name="agent-resource-governance"></a>Gouvernance des ressources de l’agent

L’agent Connected Machine des serveurs avec Azure Arc est conçu pour gérer la consommation des ressources de l’agent et du système. L’agent approche la gouvernance des ressources dans les conditions suivantes :

* L’agent de configuration Invité limite l’utilisation du processeur à 5 % au maximum lors de l’évaluation des stratégies.
* L’agent du service d’extension limite l’utilisation du processeur à 5 % au maximum lors de l’installation et la gestion des extensions.

  * Une fois installé, chaque extension limite l’utilisation du processeur à 5 % au maximum lors de l’exécution. Par exemple, si vous avez installé deux extensions, elles peuvent utiliser un total combiné de 10 % du processeur.
  * L’agent Log Analytics et l’agent Azure Monitor sont autorisés à utiliser jusqu’à 60 % du processeur pendant leurs opérations d’installation/de mise à niveau/de désinstallation sur Red Hat Linux, CentOS et d’autres variantes d’Enterprise Linux. La limite est plus élevée pour cette combinaison d’extensions et de systèmes d’exploitation afin de tenir compte de l’impact sur les performances de [SELinux](https://www.redhat.com/en/topics/linux/what-is-selinux) sur ces systèmes.

## <a name="next-steps"></a>Étapes suivantes

* Pour commencer l’évaluation des serveurs avec Azure Arc, suivez les instructions de l’article [Connecter des machines hybrides à des serveurs avec Azure Arc](learn/quick-enable-hybrid-vm.md).

* Avant de déployer l’agent des serveurs avec Azure Arc et de l’intégrer à d’autres services de gestion et de supervision Azure, consultez le [Guide de planification et de déploiement](plan-at-scale-deployment.md).

* Pour plus d’informations sur la résolution des problèmes, consultez le [guide Résoudre les problèmes de l’agent Connected Machine](troubleshoot-agent-onboard.md).
