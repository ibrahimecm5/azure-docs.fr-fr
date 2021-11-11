---
title: Questions fréquentes (FAQ) sur l’appliance Azure Migrate
description: Retrouvez les réponses aux questions courantes sur l’appliance Azure Migrate.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: 446a59d6234219425bb24e62ecfc4796cd7fec6d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131429581"
---
# <a name="azure-migrate-appliance-common-questions"></a>Appliance Azure Migrate : Questions courantes

Cet article répond à des questions courantes sur l’appliance Azure Migrate. Si vous avez d’autres questions, consultez les ressources suivantes :

- [Questions générales](resources-faq.md) sur Azure Migrate
- Questions sur la [découverte, l’évaluation et la visualisation des dépendances](common-questions-discovery-assessment.md)
- Questions sur la [migration de serveur](common-questions-server-migration.md)
- Obtenez des réponses à vos questions sur le [forum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Qu’est-ce que l’appliance Azure Migrate ?

L’appliance Azure Migrate est une appliance légère utilisée par l’outil Azure Migrate: découverte et évaluation pour détecter et évaluer des serveurs physiques ou virtuels à partir d’un environnement local ou de n’importe quel cloud. L’outil Azure Migrate : migration du serveur utilise aussi l’appliance pour la migration sans agent de serveurs locaux s’exécutant dans un environnement VMware.

Voici plus d’informations sur l’appliance Azure Migrate :

- L’appliance est déployée localement en tant que serveur physique ou serveur virtualisé.
- L’appliance découvre les serveurs locaux et envoie continuellement les métadonnées et données de performances des serveurs à Azure Migrate.
- La découverte de l’appliance se fait sans agent. Rien n’est installé sur les serveurs découverts.

[En savoir plus](migrate-appliance.md) sur l’appliance.

## <a name="how-can-i-deploy-the-appliance"></a>Comment puis-je déployer l’appliance ?

L'appliance peut être déployée à l’aide de deux méthodes :

- L’appliance peut être déployée à l’aide d’un modèle pour les serveurs s’exécutant dans un environnement VMware ou Hyper-V ([modèle OVA pour VMware](how-to-set-up-appliance-vmware.md) ou [VHD pour Hyper-V](how-to-set-up-appliance-hyper-v.md)).
- Si vous ne souhaitez pas utiliser de modèle, vous pouvez déployer l’appliance pour un environnement VMware ou Hyper-V à l’aide d’un [script d’installation PowerShell](deploy-appliance-script.md).
- Dans Azure Government, vous devez déployer l’appliance à l’aide d’un script d’installation PowerShell. Reportez-vous aux étapes de déploiement décrites [ici](deploy-appliance-script-government.md).
- Pour des serveurs physiques ou virtualisés locaux ou cloud, vous déployez toujours l’appliance à l’aide d’un script d’installation PowerShell. Découvrez les étapes de déploiement [ici](how-to-set-up-appliance-physical.md).

## <a name="how-does-the-appliance-connect-to-azure"></a>Comment l’appliance se connecte-t-elle à Azure ?

L’appliance peut se connecter via Internet ou en utilisant Azure ExpressRoute. 

- Vérifiez que l’appliance peut se connecter à ces [URL Azure](./migrate-appliance.md#url-access). 
- Vous pouvez utiliser Azure ExpressRoute avec le peering Microsoft. Le peering public, déconseillé, n’est pas disponible pour les nouveaux circuits ExpressRoute.
- Le peering privé seul n’est pas prise en charge.

## <a name="does-appliance-analysis-affect-performance"></a>L’analyse de l’appliance affecte-t-elle les performances ?

L’appliance Azure Migrate profile les serveurs locaux en continu pour mesurer les données de performances. Ce profilage n’a quasiment aucun impact sur les performances des serveurs profilés.

## <a name="can-i-harden-the-appliance"></a>Puis-je renforcer l’appliance ?

Lorsque vous utilisez le modèle téléchargé pour créer l’appliance, vous pouvez ajouter des composants (antivirus, par exemple) au modèle. Assurez-vous que vous avez autorisé l’accès aux [URL](migrate-appliance.md#public-cloud-urls) correctes via le pare-feu Azure et que le dossier *%ProgramData%\MicrosoftAzure* est exclu de l’analyse antivirus.

## <a name="what-network-connectivity-is-required"></a>Quelle est la connectivité réseau nécessaire ?

L’appliance doit avoir accès aux URL Azure. [Examinez](migrate-appliance.md#url-access) la liste des URL.

## <a name="what-data-does-the-appliance-collect"></a>Quelles données l’appliance collecte-t-elle ?

Pour plus d’informations sur les données que l’appliance Azure Migrate collecte sur les serveurs, consultez les articles suivants :

- **Serveurs dans un environnement VMware**: [révision](migrate-appliance.md#collected-data---vmware) des données collectées.
- **Serveurs dans un environnement Hyper-V**: [révision](migrate-appliance.md#collected-data---hyper-v) des données collectées.
- **Serveurs physiques ou virtuels** : [révision](migrate-appliance.md#collected-data---physical) des données collectées.

## <a name="how-is-data-stored"></a>Comment les données sont stockées ?

Les données collectées par l’appliance Azure Migrate sont stockées dans l’emplacement Azure où vous avez créé le projet.

Voici plus d’informations sur la façon dont les données sont stockées :

- Les données recueillies sont stockées de façon sécurisée dans CosmosDB dans un abonnement Microsoft. Les données sont supprimées quand vous supprimez le projet. Le stockage est géré par Azure Migrate. Vous ne pouvez pas choisir un compte de stockage spécifique pour les données recueillies.
- Si vous utilisez la [visualisation des dépendances](concepts-dependency-visualization.md), les données recueillies sont stockées dans un espace de travail Azure Log Analytics créé dans l’abonnement Azure. Ces données sont supprimées quand vous supprimez l’espace de travail Log Analytics de votre abonnement.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Quelle est la quantité de données chargée pendant le profilage continu ?

Le volume de données envoyées à Azure Migrate dépend de plusieurs paramètres. Par exemple, un projet incluant 10 serveurs (chacun avec un disque et une carte réseau) envoie environ 50 Mo de données par jour. Cette valeur est approximative ; la valeur réelle varie en fonction du nombre de points de données pour les disques et les cartes réseau. Si le nombre de serveurs, de disques ou de cartes réseau augmente, l’augmentation des données envoyées est non linéaire.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Les données sont-elles chiffrées au repos et en transit ?

Oui, elles le sont dans les deux cas :

- Les métadonnées sont envoyées de façon sécurisée au service Azure Migrate sur Internet par le biais du protocole HTTPS.
- Les métadonnées sont stockées dans une base de données [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) et dans le [Stockage Blob Azure](../storage/common/storage-service-encryption.md) au sein d’un abonnement Microsoft. Les métadonnées sont chiffrées au repos pour le stockage.
- Les données pour l’analyse des dépendances sont également chiffrées en transit (par HTTPS sécurisé). Elles sont stockées dans un espace de travail Log Analytics dans votre abonnement. Les données sont chiffrées au repos pour l’analyse des dépendances.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Comment l’appliance se connecte-t-elle au serveur vCenter ?

Ces étapes décrivent comment l’appliance se connecte au serveur VMware vCenter :

1. L’appliance se connecte vCenter Server (port 443) en utilisant les informations d’identification fournies lors de sa configuration.
2. L’appliance utilise VMware PowerCLI pour interroger vCenter Server afin de recueillir des métadonnées sur les serveurs managés par vCenter Server.
3. L’appliance collecte des données de configuration sur les serveurs (cœurs, mémoire, disques, cartes réseau) ainsi que l’historique des performances de chaque serveur pour le mois dernier.
4. Les métadonnées recueillies sont envoyées à l’outil Azure Migrate: découverte et évaluation (sur Internet par le biais du protocole HTTPS) pour l’évaluation.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>L’appliance Azure Migrate peut-elle se connecter à plusieurs serveurs vCenter ?

Non. Il existe un mappage un-à-un entre une [appliance Azure Migrate](migrate-appliance.md) et vCenter Server. Pour découvrir des serveurs sur plusieurs instances vCenter Server, vous devez déployer plusieurs appliances.

## <a name="can-a-project-have-multiple-appliances"></a>Un projet peut-il avoir plusieurs appliances ?

Plusieurs appliances peuvent être inscrites pour un même projet. Toutefois, une appliance ne peut être inscrite qu’auprès d’un seul projet.

## <a name="how-do-i-find-the-azure-migrate-appliances-registered-to-the-project"></a>Comment trouver les appliances Azure Migrate inscrites dans le projet ?
1. Dans le portail Azure, accédez à la [page d’accueil d’Azure Migrate](https://portal.azure.com/?feature.customportal=false&feature.showassettypes=Microsoft_Azure_Migrate_AzureMigrationHub&feature.smsMigrationTool=true&feature.cloudamizeAssessmentTool=true&feature.sasAssessmentTool=true&feature.firstPartyDiscoveredMachines=true#blade/Microsoft_Azure_Migrate/AmhResourceMenuBlade/getStarted), puis, dans le menu de gauche, sélectionnez **Serveurs, bases de données et applications web**.
1. Sélectionnez **Modifier** dans l’angle supérieur droit pour choisir votre projet.
1. Dans le projet Azure Migrate, dans Azure Migrate : Détection et évaluation, sélectionnez **Vue d’ensemble**.
1. Dans **Vue d’ensemble**, dans le menu de gauche, sélectionnez **Appliances** pour afficher les appliances inscrites avec le projet et l’état de connectivité des agents sur les appliances.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>L’appliance Azure Migrate/de réplication peut-elle se connecter au même vCenter ?

Oui. Vous pouvez ajouter à la fois l’appliance Azure Migrate (utilisée pour l’évaluation et la migration VMware sans agent) et l’appliance de réplication (utilisée pour la migration basée sur agent des serveurs s’exécutant dans VMware) au même serveur vCenter. Mais assurez-vous que vous ne configurez pas les deux appliances sur le même serveur, car cela n’est pas pris en charge actuellement.

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Combien de serveurs puis-je découvrir avec une appliance ?

Vous pouvez détecter jusqu’à 10 000 serveurs dans un environnement VMware, jusqu’à 5 000 serveurs dans un environnement Hyper-V et jusqu’à 1 000 serveurs physiques avec une seule appliance. Si vous avez davantage de serveurs dans votre environnement local, découvrez comment [mettre à l’échelle une évaluation Hyper-V](scale-hyper-v-assessment.md), [mettre à l’échelle une évaluation VMware](scale-vmware-assessment.md) et [mettre à l’échelle une évaluation de serveur physique](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Peut-on supprimer une appliance ?

La suppression d’une appliance du projet n’est actuellement pas prise en charge.

La seule façon de supprimer l’appliance consiste à supprimer le groupe de ressources contenant le projet associé à l’appliance.

Toutefois, la suppression du groupe de ressources a pour effet de supprimer également les autres appliances inscrites, l’inventaire détecté, les évaluations et tous les autres composants Azure du groupe de ressources associés au projet.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Puis-je utiliser l’appliance avec un autre abonnement ou projet ?

Pour utiliser l’appliance avec un autre abonnement ou projet, vous devez reconfigurer l’appliance existante en exécutant le script du programme d’installation PowerShell pour le scénario spécifique (VMware/Hyper-V/Physical) sur l’appliance. Le script nettoie les composants et les paramètres de l’appliance existante pour déployer une nouvelle appliance. Veillez à effacer le cache du navigateur avant de commencer à utiliser le gestionnaire de configuration de l’appliance nouvellement déployée.

En outre, vous ne pouvez pas réutiliser une clé de projet existante sur une appliance reconfigurée. Veillez à générer une nouvelle clé à partir de l’abonnement ou du projet souhaité pour terminer l’inscription de l’appliance.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Puis-je configurer l’appliance sur une machine virtuelle Azure ?

Non. Actuellement, cette option n’est pas prise en charge.

## <a name="can-i-discover-on-an-esxi-host"></a>Puis-je lancer une détection sur un hôte ESXi ?

Non. Pour découvrir des serveurs dans un environnement VMware, vous devez disposer de vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Comment mettre à jour l’appliance ?

Par défaut, l’appliance et ses agents installés sont mis à jour automatiquement. L’appliance vérifie les mises à jour toutes les 24 heures. Les mises à jour qui échouent sont retentées.

Seuls l’appliance et les agents de l’appliance sont mis à jour par ces mises à jour automatiques. Le système d’exploitation n’est pas mis à jour par les mises à jour automatiques Azure Migrate. Utilisez Windows Updates pour tenir le système d’exploitation à jour.

## <a name="can-i-check-agent-health"></a>Puis-je vérifier l’intégrité de l’agent ?

Oui. Dans le portail, accédez à la page **Intégrité de l’agent** pour l’outil Azure Migrate: découverte et évaluation ou Azure Migrate : migration de serveur. Vous pouvez y vérifier l’état de la connexion entre Azure et les agents de détection et d’évaluation sur l’appliance.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>Puis-je ajouter plusieurs informations d’identification de serveur sur une appliance VMware ?

Oui, nous prenons désormais en charge plusieurs informations d’identification de serveur pour effectuer l’inventaire logiciel (découverte des applications installées), l’analyse des dépendances sans agent et la découverte des instances et bases de données SQL Server. [Apprenez-en davantage](tutorial-discover-vmware.md#provide-server-credentials) sur la façon de fournir des informations d’identification sur le gestionnaire de configuration de l’appliance.

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>Quels types d’informations d’identification de serveur puis-je ajouter à l’appliance VMware ?

Vous pouvez fournir des informations d’identification d’authentification de domaine/Windows (hors domaine)/Linux (hors domaine)/SQL Server sur le gestionnaire de configuration de l’appliance. [Apprenez-en davantage](add-server-credentials.md) sur la manière de fournir des informations d’identification et la façon dont nous les traitons.

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>Quels types de propriétés de connexion SQL Server Azure Migrate prend-il en charge pour la découverte SQL ?

Azure Migrate chiffre la communication entre l’appliance Azure Migrate et les instances SQL Server sources (avec la propriété Chiffrer la connexion définie sur TRUE). Ces connexions sont chiffrées avec [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (défini sur TRUE). La couche transport utilise le protocole SSL pour chiffrer le canal et contourner la chaîne de certificats afin de valider l’approbation. Le serveur d’appliance doit être configuré pour [approuver l’autorité racine du certificat](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Si aucun certificat n'est présent sur le serveur au démarrage, SQL Server génère un certificat auto-signé qui servira au chiffrement des paquets de connexion. [Plus d’informations](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)

## <a name="next-steps"></a>Étapes suivantes

Consultez la [vue d’ensemble d’Azure Migrate](migrate-services-overview.md).
