---
title: Types de ressources Azure Resource Manager prises en charge
description: Fournit la liste des types de ressources Azure Resource Manager prises en charge par Azure Resource Graph et l’historique des modifications.
ms.date: 10/12/2021
ms.topic: reference
ms.custom: generated
ms.openlocfilehash: 9b3ce54d9554164b375ed1a08fdbcce8429dbb21
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054806"
---
# <a name="azure-resource-graph-table-and-resource-type-reference"></a>Informations de référence sur le type de ressource et la table Azure Resource Graph

Azure Resource Graph prend en charge les **types de ressources** suivants [d’Azure Resource Manager](../../../azure-resource-manager/management/overview.md). Chaque **type de ressource** fait partie d’une **table** dans Resource Graph.

## <a name="advisorresources"></a>advisorresources

Pour obtenir des exemples de requêtes pour cette table, consultez [Exemples de requêtes Azure Resource Graph pour advisorresources](../samples/samples-by-table.md#advisorresources).

- microsoft.advisor/configurations
- microsoft.advisor/recommendations
  - Exemple de requête : [Obtenir le récapitulatif des économies d’Azure Advisor](../samples/samples-by-category.md#get-cost-savings-summary-from-azure-advisor)
  - Exemple de requête : [liste des serveurs compatibles Arc n’exécutant pas la dernière version de l’agent libéré](../samples/samples-by-category.md#list-arc-enabled-servers-not-running-latest-released-agent-version)
- microsoft.advisor/recommendations/suppressions
- microsoft.advisor/suppressions

## <a name="alertsmanagementresources"></a>alertsmanagementresources

- microsoft.alertsmanagement/alerts

## <a name="desktopvirtualizationresources"></a>desktopvirtualizationresources

- microsoft.chaos/experiments/statuses
- microsoft.chaos/targets
- microsoft.chaos/targets/capabilities
- microsoft.desktopvirtualization/hostpools/sessionhosts

## <a name="extendedlocationresources"></a>extendedlocationresources

Pour obtenir des exemples de requêtes pour cette table, consultez [Exemples de requêtes Azure Resource Graph pour extendedlocationresources](../samples/samples-by-table.md#extendedlocationresources).

- microsoft.extendedlocation/customlocations/enabledresourcetypes
  - Exemple de requête : [Obtenir les types de ressources activés pour les emplacements personnalisés compatibles avec Azure Arc](../samples/samples-by-category.md#get-enabled-resource-types-for-azure-arc-enabled-custom-locations)
  - Exemple de requête :[Lister les emplacements personnalisés compatibles avec Azure Arc avec activation de VMware ou de SCVMM](../samples/samples-by-category.md#list-azure-arc-enabled-custom-locations-with-vmware-or-scvmm-enabled)

## <a name="guestconfigurationresources"></a>guestconfigurationresources

Pour obtenir des exemples de requêtes pour cette table, consultez [Exemples de requêtes Azure Resource Graph pour guestconfigurationresources](../samples/samples-by-table.md#guestconfigurationresources).

- microsoft.guestconfiguration/guestconfigurationassignments
  - Exemple de requête : [Compter les machines dans l’étendue des stratégies de configuration d’invité](../samples/samples-by-category.md#count-machines-in-scope-of-guest-configuration-policies)
  - Exemple de requête : [Nombre d’affectations de configuration d’invité non conformes](../samples/samples-by-category.md#count-of-non-compliant-guest-configuration-assignments)
  - Exemple de requête : [Rechercher tous les motifs pour lesquels une machine n’est pas conforme aux affectations de configuration d’invité](../samples/samples-by-category.md#find-all-reasons-a-machine-is-non-compliant-for-guest-configuration-assignments)

## <a name="healthresources"></a>healthresources

Pour obtenir des exemples de requêtes pour cette table, consultez [Exemples de requêtes Azure Resource Graph pour healthresources](../samples/samples-by-table.md#healthresources).

- microsoft.resourcehealth/availabilitystatuses
  - Exemple de requête : [Nombre de machines virtuelles par état de disponibilité et ID d’abonnement](../samples/samples-by-category.md#count-of-virtual-machines-by-availability-state-and-subscription-id)
  - Exemple de requête : [Liste des machines virtuelles et états de disponibilité associés par ID de ressource](../samples/samples-by-category.md#list-of-virtual-machines-and-associated-availability-states-by-resource-ids)
  - Exemple de requête : [Liste des machines virtuelles par état de disponibilité et état d’alimentation avec les ID de ressource et les groupes de ressources](../samples/samples-by-category.md#list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups)
  - Exemple de requête : [Liste des machines virtuelles qui ne sont pas disponibles par ID de ressource](../samples/samples-by-category.md#list-of-virtual-machines-that-are-not-available-by-resource-ids)

## <a name="iotsecurityresources"></a>IoTSecurityResources

- microsoft.iotsecurity/locations/devicegroups/alerts
  - Exemple de requête : [Obtenir toutes les nouvelles alertes des 30 derniers jours](../samples/samples-by-category.md#get-all-new-alerts-from-the-past-30-days)
- microsoft.iotsecurity/locations/devicegroups/devices
  - Exemple de requête : [Compter le nombre d’appareils IoT dans votre réseau, par système d’exploitation](../samples/samples-by-category.md#count-how-many-iot-devices-there-are-in-your-network-by-operation-system)
- microsoft.iotsecurity/locations/devicegroups/recommendations
  - Exemple de requête : [Obtenir toutes les recommandations avec un niveau de gravité élevé](../samples/samples-by-category.md#get-all-high-severity-recommendations)
- microsoft.iotsecurity/onpremisesensors
- microsoft.iotsecurity/sensors
  - Exemple de requête : [Compter tous les capteurs par type](../samples/samples-by-category.md#count-all-sensors-by-type)
- microsoft.iotsecurity/sites
  - Exemple de requête : [Lister les sites avec une valeur d’étiquette spécifique](../samples/samples-by-category.md#list-sites-with-a-specific-tag-value)

## <a name="kubernetesconfigurationresources"></a>kubernetesconfigurationresources

Pour obtenir des exemples de requêtes pour cette table, consultez [Exemples de requêtes Azure Resource Graph pour kubernetesconfigurationresources](../samples/samples-by-table.md#kubernetesconfigurationresources).

- microsoft.kubernetesconfiguration/extensions
  - Exemple de requête : [Lister tous les clusters Kubernetes compatibles avec Azure Arc avec l’extension Azure Monitor](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-with-azure-monitor-extension)
  - Exemple de requête : [Lister tous les clusters Kubernetes compatibles avec Azure Arc sans l’extension Azure Monitor](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-without-azure-monitor-extension)
- microsoft.kubernetesconfiguration/fluxconfigurations
- microsoft.kubernetesconfiguration/sourcecontrolconfigurations

## <a name="maintenanceresources"></a>maintenanceresources

- microsoft.maintenance/applyupdates
- microsoft.maintenance/configurationassignments
- microsoft.maintenance/updates

## <a name="patchassessmentresources"></a>patchassessmentresources

Pour obtenir des exemples de requêtes pour cette table, consultez [Exemples de requêtes Azure Resource Graph pour patchassessmentresources](../samples/samples-by-table.md#patchassessmentresources).

- microsoft.compute/virtualmachines/patchassessmentresults
- microsoft.compute/virtualmachines/patchassessmentresults/softwarepatches
- microsoft.hybridcompute/machines/patchassessmentresults
- microsoft.hybridcompute/machines/patchassessmentresults/softwarepatches

## <a name="patchinstallationresources"></a>patchinstallationresources

- microsoft.compute/virtualmachines/patchinstallationresults
- microsoft.compute/virtualmachines/patchinstallationresults/softwarepatches
- microsoft.hybridcompute/machines/patchinstallationresults
- microsoft.hybridcompute/machines/patchinstallationresults/softwarepatches

## <a name="policyresources"></a>policyresources

Pour obtenir des exemples de requêtes pour cette table, consultez [Exemples de requêtes Azure Resource Graph pour policyresources](../samples/samples-by-table.md#policyresources).

- microsoft.policyinsights/policystates
  - Exemple de requête : [Conformité par attribution de stratégie](../samples/samples-by-category.md#compliance-by-policy-assignment)
  - Exemple de requête : [Conformité par type de ressource](../samples/samples-by-category.md#compliance-by-resource-type)
  - Exemple de requête : [Lister toutes les ressources non conformes](../samples/samples-by-category.md#list-all-non-compliant-resources)
  - Exemple de requête : [Résumer la conformité des ressources par état](../samples/samples-by-category.md#summarize-resource-compliance-by-state)
  - Exemple de requête : [Résumer la conformité des ressources par état et par emplacement](../samples/samples-by-category.md#summarize-resource-compliance-by-state-per-location)

## <a name="recoveryservicesresources"></a>recoveryservicesresources

- microsoft.dataprotection/backupvaults/backupinstances
- microsoft.dataprotection/backupvaults/backupjobs
- microsoft.dataprotection/backupvaults/backuppolicies
- microsoft.recoveryservices/vaults/alerts
- Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems (Éléments de sauvegarde)
- microsoft.recoveryservices/vaults/backupjobs
- microsoft.recoveryservices/vaults/backuppolicies

## <a name="resourcecontainers"></a>resourcecontainers

Pour obtenir des exemples de requêtes pour cette table, consultez [Exemples de requêtes Azure Resource Graph pour resourcecontainers](../samples/samples-by-table.md#resourcecontainers).

- microsoft.management/managementgroups
  - Exemple de requête : [Nombre d’abonnements par groupe d’administration](../samples/samples-by-category.md#count-of-subscriptions-per-management-group)
  - Exemple de requête : [Lister tous les ancêtres du groupe d’administration pour un groupe d’administration spécifié](../samples/samples-by-category.md#list-all-management-group-ancestors-for-a-specified-management-group)
- Microsoft.Resources/subscriptions/resourceGroups (Groupes de ressources)
  - Exemple de requête : [Combiner les résultats de deux requêtes en un résultat unique](../samples/samples-by-category.md)
  - Exemple de requête : [Rechercher les comptes de stockage avec une étiquette spécifique qui ne respecte pas la casse sur le groupe de ressources](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group)
  - Exemple de requête : [Rechercher les comptes de stockage avec une étiquette spécifique qui respecte la casse sur le groupe de ressources](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group)

## <a name="resources"></a>les ressources

Pour obtenir des exemples de requêtes pour cette table, consultez [Exemples de requêtes Azure Resource Graph pour resources](../samples/samples-by-table.md#resources).

- 84codes.CloudAMQP/servers (CloudAMQP)
- Citrix.Services/XenAppEssentials (Citrix Virtual Apps Essentials)
- Citrix.Services/XenDesktopEssentials (Citrix Virtual Desktops Essentials)
- conexlink.mycloudit/accounts
- crypteron.datasecurity/apps
- Dynatrace.Observability/monitors (Dynatrace)
- GitHub.Enterprise/accounts (GitHub AE)
- gridpro.evops/accounts
- gridpro.evops/accounts/eventrules
- gridpro.evops/accounts/requesttemplates
- gridpro.evops/accounts/views
- hive.streaming/services
- incapsula.waf/accounts
- LiveArena.Broadcast/services (LiveArena Broadcast)
- Mailjet.Email/services (Service de messagerie Mailjet)
- micorosft.web/kubeenvironments
- Microsoft.AAD/domainServices (Azure AD Domain Services)
- microsoft.aadiam/azureadmetrics
- microsoft.aadiam/privateLinkForAzureAD (Liaison privée pour Azure AD)
- microsoft.aadiam/tenants
- Microsoft.AgFoodPlatform/farmBeats (Azure FarmBeats)
- microsoft.aisupercomputer/accounts
- microsoft.aisupercomputer/accounts/jobgroups
- microsoft.aisupercomputer/accounts/jobgroups/jobs
- microsoft.alertsmanagement/actionrules
- microsoft.alertsmanagement/resourcehealthalertrules
- microsoft.alertsmanagement/smartdetectoralertrules
- Microsoft.AnalysisServices/servers (Analysis Services)
- Microsoft.AnyBuild/clusters (Clusters AnyBuild)
- Microsoft.ApiManagement/service (Services Gestion des API)
- microsoft.appassessment/migrateprojects
- Microsoft.AppConfiguration/configurationStores (App Configuration)
- Microsoft.AppPlatform/Spring (Azure Spring Cloud)
- microsoft.archive/collections
- Microsoft.Attestation/attestationProviders (Fournisseurs d’attestation)
- microsoft.authorization/elevateaccessroleassignment
- Microsoft.Authorization/resourceManagementPrivateLinks (Liaisons privées de gestion des ressources)
- microsoft.automanage/accounts
- microsoft.automanage/configurationprofilepreferences
- microsoft.automanage/configurationprofiles
- Microsoft.Automation/AutomationAccounts (Comptes Automation)
- microsoft.automation/automationaccounts/configurations
- Microsoft.Automation/automationAccounts/runbooks (Runbook)
- microsoft.autonomousdevelopmentplatform/accounts
- Microsoft.AutonomousSystems/workspaces (Bonsai)
- Microsoft.AVS/privateClouds (Clouds privés AVS)
- microsoft.azconfig/configurationstores
- Microsoft.AzureActiveDirectory/b2cDirectories (Locataires B2C)
- Microsoft.AzureActiveDirectory/guestUsages (Utilisations des invités)
- Microsoft.AzureArcData/dataControllers (Contrôleurs de données Azure Arc)
- Microsoft.AzureArcData/postgresInstances (Groupes de serveurs PostgreSQL Hyperscale avec Azure Arc)
- Microsoft.AzureArcData/sqlManagedInstances (Instances managées SQL – Azure Arc)
- Microsoft.AzureArcData/sqlServerInstances (SQL Server – Azure Arc)
- microsoft.azurecis/autopilotenvironments
- microsoft.azurecis/dstsserviceaccounts
- microsoft.azurecis/dstsserviceclientidentities
- microsoft.azuredata/datacontrollers
- microsoft.azuredata/hybriddatamanagers
- microsoft.azuredata/postgresinstances
- microsoft.azuredata/sqlbigdataclusters
- microsoft.azuredata/sqlinstances
- microsoft.azuredata/sqlmanagedinstances
- microsoft.azuredata/sqlserverinstances
- Microsoft.AzureData/sqlServerRegistrations (Registres SQL Server)
- Microsoft.AzurePercept/accounts (Comptes Azure Percept)
- microsoft.azuresphere/catalogs
- microsoft.azuresphere/catalogs/products
- microsoft.azuresphere/catalogs/products/devicegroups
- microsoft.azurestack/edgesubscriptions
- microsoft.azurestack/linkedsubscriptions
- Microsoft.Azurestack/registrations (Instances Azure Stack Hub)
- Microsoft.AzureStackHCI/clusters (Azure Stack HCI)
- microsoft.azurestackhci/galleryimages
- microsoft.azurestackhci/networkinterfaces
- microsoft.azurestackhci/virtualharddisks
- Microsoft.AzureStackHci/virtualMachines (machine virtuelle Azure Stack HCI – Azure Arc)
- microsoft.azurestackhci/virtualmachines/extensions
- microsoft.azurestackhci/virtualnetworks
- microsoft.backupsolutions/vmwareapplications
- microsoft.baremetal/consoleconnections
- Microsoft.BareMetal/crayServers (Serveurs Cray)
- Microsoft.BareMetal/monitoringServers (Serveurs de supervision)
- Microsoft.BareMetalInfrastructure/bareMetalInstances (Instances bareMetal)
- Microsoft.Batch/batchAccounts (Comptes Batch)
- microsoft.batchai/clusters
- microsoft.batchai/fileservers
- microsoft.batchai/jobs
- microsoft.batchai/workspaces
- Microsoft.Bing/accounts (Ressources Bing)
- microsoft.bingmaps/mapapis
- microsoft.biztalkservices/biztalk
- Microsoft.Blockchain/blockchainMembers (Service Azure Blockchain)
- Microsoft.Blockchain/cordaMembers (Corda)
- Microsoft.Blockchain/watchers (Blockchain Data Manager)
- Microsoft.BotService/botServices (Services Bot)
- Microsoft.Cache/Redis (Azure Cache pour Redis)
- Microsoft.Cache/RedisEnterprise (Redis Enterprise)
- microsoft.cascade/sites
- Microsoft.Cdn/CdnWebApplicationFirewallPolicies (Stratégies Web Application Firewall (WAF))
- microsoft.cdn/profiles (Front Door Standard/Premium [préversion])
- Microsoft.Cdn/Profiles/AfdEndpoints (Points de terminaison)
- microsoft.cdn/profiles/endpoints (Points de terminaison)
- Microsoft.CertificateRegistration/certificateOrders (Certificats App Service)
- microsoft.chaos/chaosexperiments (Expériences de chaos (classiques))
- microsoft.chaos/experiments (Expériences de chaos)
- microsoft.classicCompute/domainNames (Services cloud (classiques))
- Microsoft.ClassicCompute/VirtualMachines (Machines virtuelles (classiques))
- Microsoft.ClassicNetwork/networkSecurityGroups (Groupes de sécurité réseau (classiques))
- Microsoft.ClassicNetwork/reservedIps (Adresses IP réservées (classiques))
- Microsoft.ClassicNetwork/virtualNetworks (Réseaux virtuels (classiques))
- Microsoft.ClassicStorage/StorageAccounts (Comptes de stockage (classiques))
- microsoft.cloudes/accounts
- microsoft.cloudsearch/indexes
- Microsoft.CloudTest/accounts (Comptes CloudTest)
- Microsoft.CloudTest/hostedpools (Pools hébergés 1ES)
- Microsoft.CloudTest/images (Images CloudTest)
- Microsoft.CloudTest/pools (Pools CloudTest)
- Microsoft.ClusterStor/nodes (ClusterStors)
- microsoft.codesigning/codesigningaccounts
- microsoft.codespaces/plans
- Microsoft.Cognition/syntheticsAccounts (Comptes Synthetics)
- Microsoft.CognitiveServices/accounts (Cognitive Services)
- Microsoft.Compute/availabilitySets (Groupes à haute disponibilité)
- Microsoft.Compute/capacityReservationGroups (Groupes de réservations de capacités)
- microsoft.compute/capacityreservationgroups/capacityreservations
- microsoft.compute/capacityreservations
- Microsoft.Compute/cloudServices (Services cloud (support étendu))
- Microsoft.Compute/diskAccesses (Accès de disque)
- Microsoft.Compute/diskEncryptionSets (Jeux de chiffrement de disque)
- Microsoft.Compute/disks (Disques)
- Microsoft.Compute/galleries (Galeries d’images partagées)
- Microsoft.Compute/galleries/applications (Applications de machine virtuelle)
- Microsoft.Compute/galleries/applications/versions (versions d’applications de machine virtuelle)
- Microsoft.Compute/galleries/images (Définitions d’image)
- Microsoft.Compute/galleries/images/versions (Versions d’images)
- Microsoft.Compute/hostgroups (Groupes hôtes)
- Microsoft.Compute/hostgroups/hosts (Hôtes)
- Microsoft.Compute/images (Images)
- Microsoft.Compute/ProximityPlacementGroups (Groupes de placements de proximité)
- Microsoft.Compute/restorePointCollections (Collections de points de restauration)
- microsoft.compute/sharedvmextensions
- microsoft.compute/sharedvmextensions/versions
- microsoft.compute/sharedvmimages
- microsoft.compute/sharedvmimages/versions
- Microsoft.Compute/snapshots (Instantanés)
- Microsoft.Compute/sshPublicKeys (Clés SSH)
- microsoft.compute/swiftlets
- Microsoft.Compute/VirtualMachines (Machines virtuelles)
  - Exemple de requête : [Nombre de machines virtuelles par état d’alimentation](../samples/samples-by-category.md#count-of-virtual-machines-by-power-state)
  - Exemple de requête : [Compter les machines virtuelles par type de système d’exploitation](../samples/samples-by-category.md#count-virtual-machines-by-os-type)
  - Exemple de requête : [Compter les machines virtuelles par type de système d’exploitation avec extension](../samples/samples-by-category.md#count-virtual-machines-by-os-type-with-extend)
  - Exemple de requête : [Lister toutes les extensions installées sur une machine virtuelle](../samples/samples-by-category.md#list-all-extensions-installed-on-a-virtual-machine)
  - Exemple de requête : [Lister les machines qui ne sont pas en cours d’exécution et le dernier état de conformité](../samples/samples-by-category.md#list-machines-that-are-not-running-and-the-last-compliance-status)
  - Exemple de requête : [Liste des machines virtuelles par état de disponibilité et état d’alimentation avec les ID de ressource et les groupes de ressources](../samples/samples-by-category.md#list-of-virtual-machines-by-availability-state-and-power-state-with-resource-ids-and-resource-groups)
  - Exemple de requête : [Lister les machines virtuelles avec leur interface réseau et leur adresse IP publique](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
  - Exemple de requête : [Afficher toutes les machines virtuelles classées par nom par ordre décroissant](../samples/samples-by-category.md#show-all-virtual-machines-ordered-by-name-in-descending-order)
  - Exemple de requête : [Afficher les cinq premières machines virtuelles par nom et leur type de système d’exploitation](../samples/samples-by-category.md#show-first-five-virtual-machines-by-name-and-their-os-type)
  - Exemple de requête : [Résumer la machine virtuelle par la propriété étendue des états d’alimentation](../samples/samples-by-category.md#summarize-virtual-machine-by-the-power-states-extended-property)
  - Exemple de requête : [Machines virtuelles mises en correspondance par expression régulière](../samples/samples-by-category.md#virtual-machines-matched-by-regex)
- microsoft.compute/virtualmachines/extensions
  - Exemple de requête : [Lister toutes les extensions installées sur une machine virtuelle](../samples/samples-by-category.md#list-all-extensions-installed-on-a-virtual-machine)
- microsoft.compute/virtualmachines/runcommands
- Microsoft.Compute/virtualMachineScaleSets (Groupes de machines virtuelles identiques)
  - Exemple de requête : [Obtenir une capacité et une taille de groupe de machines virtuelles identiques](../samples/samples-by-category.md#get-virtual-machine-scale-set-capacity-and-size)
- Microsoft.ConfidentialLedger/ledgers (Registres confidentiels)
- Microsoft.Confluent/organizations (Organisations Confluent)
- Microsoft.ConnectedCache/cacheNodes (Ressources de cache connecté)
- Microsoft. ConnectedVehicle/platformAccounts (Plateformes de véhicules connectée)
- microsoft.connectedvmwarevsphere/clusters
- microsoft.connectedvmwarevsphere/datastores
- microsoft.connectedvmwarevsphere/hosts
- microsoft.connectedvmwarevsphere/resourcepools
- Microsoft.connectedVMwareVSphere/vCenters (VMware vCenters)
- Microsoft.ConnectedVMwarevSphere/VirtualMachines (VMware + machines virtuelles AVS)
- microsoft.connectedvmwarevsphere/virtualmachines
- microsoft.connectedvmwarevsphere/virtualmachinetemplates
- microsoft.connectedvmwarevsphere/virtualnetworks
- Microsoft.ContainerInstance/containerGroups (Instances de conteneur)
- Microsoft.ContainerRegistry/registries (Registres de conteneurs)
- microsoft.containerregistry/registries/agentpools
- microsoft.containerregistry/registries/buildtasks
- Microsoft.ContainerRegistry/registries/replications (Réplications du registre de conteneurs)
- microsoft.containerregistry/registries/taskruns
- microsoft.containerregistry/registries/tasks
- Microsoft.ContainerRegistry/registries/webhooks (Webhooks du registre de conteneurs)
- microsoft.containerservice/containerservices
- Microsoft.ContainerService/managedClusters (Services Kubernetes)
  - Exemple de requête : [ Lister les ressources impactées lors du transfert d’un abonnement Azure](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.containerservice/openshiftmanagedclusters
- microsoft.contoso/clusters
- microsoft.contoso/employees
- microsoft.contoso/installations
- microsoft.contoso/towers
- microsoft.costmanagement/connectors
- microsoft.customproviders/resourceproviders
- microsoft.d365customerinsights/instances
- Microsoft.Dashboard/grafana (Espaces de travail Grafana)
- Microsoft.DataBox/jobs (Azure Data Box)
- Microsoft.DataBoxEdge/dataBoxEdgeDevices (Azure Stack Edge / Data Box Gateway)
- Microsoft.Databricks/workspaces (Services Azure Databricks)
- Microsoft.DataCatalog/catalogs (Catalogue de données)
- microsoft.datacatalog/datacatalogs
- Microsoft.DataCollaboration/workspaces (Projet CI)
- Microsoft.Datadog/monitors (Datadog)
- Microsoft.DataFactory/dataFactories (Fabriques de données)
- Microsoft.DataFactory/factories (Fabriques de données (V2))
- Microsoft.DataLakeAnalytics/accounts (Data Lake Analytics)
- Microsoft.DataLakeStore/accounts (Data Lake Storage Gen1)
  - Exemple de requête : [ Lister les ressources impactées lors du transfert d’un abonnement Azure](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.datamigration/controllers
- Microsoft.DataMigration/services (Services Azure Database Migration)
- Microsoft.DataMigration/services/projects (Projets Azure Database Migration)
- microsoft.datamigration/slots
- microsoft.datamigration/sqlmigrationservices
- Microsoft.DataProtection/BackupVaults (Coffres de sauvegarde)
- Microsoft.DataProtection/resourceGuards (Protecteurs de ressources (préversion))
- microsoft.dataprotection/resourceoperationgatekeepers
- microsoft.datareplication/replicationfabrics
- Microsoft.DataReplication/replicationVaults (coffres Site Recovery)
- Microsoft.DataShare/accounts (Partages de données)
- Microsoft.DBforMariaDB/servers (Serveurs Azure Database pour MariaDB)
- Microsoft.DBforMySQL/flexibleServers (Serveurs flexibles Azure Database pour MySQL)
- Microsoft.DBforMySQL/servers (Serveurs Azure Database pour MySQL)
- Microsoft.DBforPostgreSQL/flexibleServers (Serveurs flexibles Azure Database pour PostgreSQL)
- Microsoft.DBforPostgreSQL/serverGroups (Groupes de serveurs Azure Database pour PostgreSQL)
- Microsoft.DBforPostgreSQL/serverGroupsv2 (Groupes de serveurs Azure Database pour PostgreSQL)
- Microsoft.DBforPostgreSQL/servers (Serveurs Azure Database pour PostgreSQL)
- Microsoft.DBforPostgreSQL/serversv2 (Serveurs v2 Azure Database pour PostgreSQL)
- microsoft.dbforpostgresql/singleservers
- microsoft.delegatednetwork/controller
- microsoft.delegatednetwork/delegatedsubnets
- microsoft.delegatednetwork/orchestratorinstances
- microsoft.delegatednetwork/orchestrators
- microsoft.deploymentmanager/artifactsources
- Microsoft.DeploymentManager/Rollouts (Déploiements)
- microsoft.deploymentmanager/servicetopologies
- microsoft.deploymentmanager/servicetopologies/services
- microsoft.deploymentmanager/servicetopologies/services/serviceunits
- microsoft.deploymentmanager/steps
- Microsoft.DesktopVirtualization/ApplicationGroups (Groupes d’applications)
- Microsoft.DesktopVirtualization/HostPools (Pools d’hôtes)
- Microsoft.DesktopVirtualization/ScalingPlans (Plans de mise à l’échelle)
- Microsoft.DesktopVirtualization/Workspaces (Espaces de travail)
- microsoft.devices/elasticpools
- microsoft.devices/elasticpools/iothubtenants
- Microsoft.Devices/IotHubs (Hub IoT)
- Microsoft.Devices/ProvisioningServices (Services de provisionnement d’appareil)
- Microsoft.DeviceUpdate/Accounts (Device Update pour hubs IoT)
- microsoft.deviceupdate/accounts/instances
- microsoft.devops/pipelines (Démarrage DevOps)
- microsoft.devspaces/controllers
- microsoft.devtestlab/labcenters
- Microsoft.DevTestLab/labs (Labos DevTest)
- microsoft.devtestlab/labs/servicerunners
- Microsoft.DevTestLab/labs/virtualMachines (Machines virtuelles)
- microsoft.devtestlab/schedules
- Microsoft.DigitalTwins/digitalTwinsInstances (Azure Digital Twins)
- Microsoft.DocumentDB/cassandraClusters (Azure Managed Instance pour Apache Cassandra)
- Microsoft.DocumentDb/databaseAccounts (Comptes Azure Cosmos DB)
  - Exemple de requête : [Lister les ressources Azure Cosmos DB avec des emplacements d’écriture spécifiques](../samples/samples-by-category.md#list-azure-cosmos-db-with-specific-write-locations)
- Microsoft.DomainRegistration/domains (Domaines App Service)
- microsoft.dynamics365fraudprotection/instances
- Microsoft.EdgeOrder/addresses (Azure Edge Hardware Center)
- microsoft.edgeorder/ordercollections
- Microsoft.EdgeOrder/orderItems (Azure Edge Hardware Center)
- microsoft.edgeorder/orders
- Microsoft.Elastic/monitors (Elasticsearch (cloud élastique))
- microsoft.enterpriseknowledgegraph/services
- Microsoft.EventGrid/domains (Domaines Event Grid)
- Microsoft.EventGrid/partnerNamespaces (Espaces de noms des partenaires Event Grid)
- Microsoft.EventGrid/partnerRegistrations (Inscriptions des partenaires Event Grid)
- Microsoft.EventGrid/partnerTopics (Rubriques de partenaire Event Grid)
- Microsoft.EventGrid/systemTopics (Rubriques système Event Grid)
- Microsoft.EventGrid/topics (Rubriques Event Grid)
- Microsoft.EventHub/clusters (Clusters Event Hubs)
- Microsoft.EventHub/namespaces (Espaces de noms Event Hubs)
- Microsoft.Experimentation/experimentWorkspaces (Espaces de travail d’expérience)
- Microsoft.ExtendedLocation/CustomLocations (Emplacements personnalisés)
  - Exemple de requête :[Lister les emplacements personnalisés compatibles avec Azure Arc avec activation de VMware ou de SCVMM](../samples/samples-by-category.md#list-azure-arc-enabled-custom-locations-with-vmware-or-scvmm-enabled)
- microsoft.falcon/namespaces
- Microsoft.Fidalgo/devcenters (Fidalgo DevCenters)
- microsoft.fidalgo/machinedefinitions
- microsoft.fidalgo/networksettings
- Microsoft.Fidalgo/projects (Projets Fidalgo)
- Microsoft.Fidalgo/projects/environments (Environnements Fidalgo)
- Microsoft.FluidRelay/fluidRelayServers (Relais Fluid)
- microsoft.footprintmonitoring/profiles
- microsoft.gaming/titles
- Microsoft.Genomics/accounts (Comptes Genomics)
- microsoft.guestconfiguration/automanagedaccounts
- Microsoft.HanaOnAzure/hanaInstances (SAP HANA sur Azure)
- Microsoft.HanaOnAzure/sapMonitors (Instances Azure Monitor pour solutions SAP)
- microsoft.hardwaresecuritymodules/dedicatedhsms
- Microsoft.HDInsight/clusterpools (Pool de clusters HDInsight)
- Microsoft.HDInsight/clusterpools/clusters (Clusters HDInsight Gen2)
- microsoft.hdinsight/clusterpools/clusters/sessionclusters
- Microsoft.HDInsight/clusters (Clusters HDInsight)
- Microsoft.HealthBot/healthBots (Azure Health Bot)
- Microsoft.HealthcareApis/services (API Azure pour FHIR)
- microsoft.healthcareapis/services/privateendpointconnections
- Microsoft.HealthcareApis/workspaces (Espaces de travail Healthcare APIs)
- Microsoft.HealthcareApis/workspaces/dicomservices (Services DICOM)
- Microsoft.HealthcareApis/workspaces/fhirservices (Services FHIR)
- Microsoft.HealthcareApis/workspaces/iotconnectors (Connecteurs IoT)
- Microsoft.HpcWorkbench/instances (HPC Workbench (préversion))
- Microsoft.HybridCompute/machines (Serveurs – Azure Arc)
  - Exemple de requête : [Obtenir le nombre et le pourcentage de serveurs avec Arc par domaine](../samples/samples-by-category.md#get-count-and-percentage-of-arc-enabled-servers-by-domain)
  - Exemple de requête : [Répertorier toutes les extensions installées sur un serveur avec Azure Arc](../samples/samples-by-category.md#list-all-extensions-installed-on-an-azure-arc-enabled-server)
  - Exemple de requête : [liste des serveurs compatibles Arc n’exécutant pas la dernière version de l’agent libéré](../samples/samples-by-category.md#list-arc-enabled-servers-not-running-latest-released-agent-version)
- microsoft.hybridcompute/machines/extensions
  - Exemple de requête : [Répertorier toutes les extensions installées sur un serveur avec Azure Arc](../samples/samples-by-category.md#list-all-extensions-installed-on-an-azure-arc-enabled-server)
- Microsoft.HybridCompute/privateLinkScopes (Étendues de liaison privée Azure Arc)
- microsoft.hybridcontainerservice/provisionedclusters
- Microsoft.HybridData/dataManagers (Gestionnaires de données StorSimple)
- Microsoft.HybridNetwork/devices (Gestionnaire des fonctions réseau Azure – Appareils [préversion])
- Microsoft.HybridNetwork/networkFunctions (Gestionnaire des fonctions réseau Azure – Fonctions réseau [préversion])
- microsoft.hybridnetwork/virtualnetworkfunctions
- Microsoft.ImportExport/jobs (Travaux d’importation/exportation)
- microsoft.industrydatalifecycle/basemodels
- microsoft.industrydatalifecycle/custodiancollaboratives
- microsoft.industrydatalifecycle/dataconsumercollaboratives
- microsoft.industrydatalifecycle/derivedmodels
- microsoft.industrydatalifecycle/membercollaboratives
- microsoft.industrydatalifecycle/modelmappings
- microsoft.industrydatalifecycle/pipelinesets
- microsoft.insights/actiongroups
- microsoft.insights/activitylogalerts
- microsoft.insights/alertrules
- microsoft.insights/autoscalesettings
- microsoft.insights/components (Application Insights)
- microsoft.insights/datacollectionendpoints (Points de terminaison de collecte de données)
- microsoft.insights/datacollectionrules (Règles de collecte des données)
- microsoft.insights/guestdiagnosticsettings
- microsoft.insights/metricalerts
- microsoft.insights/notificationgroups
- microsoft.insights/notificationrules
- Microsoft.Insights/privateLinkScopes (Étendues de liaison privée Azure Monitor)
- microsoft.insights/querypacks
- microsoft.insights/scheduledqueryrules
- microsoft.insights/webtests (Tests de disponibilité)
- microsoft.insights/workbooks (Classeurs Azure)
- microsoft.insights/workbooktemplates (Modèles de classeur Azure)
- Microsoft.IntelligentITDigitalTwin/digitalTwins (Minervas)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/assets (Ressources)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/executionPlans (Déploiements)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/testPlans (Suites)
- Microsoft.IntelligentITDigitalTwin/digitalTwins/tests (Scripts)
- Microsoft.IoTCentral/IoTApps (Applications IoT Central)
- microsoft.iotspaces/graph
- microsoft.keyvault/hsmpools
- microsoft.keyvault/managedhsms
- Microsoft.KeyVault/vaults (Coffres de clés)
  - Exemple de requête : [Compter les ressources de coffre de clés](../samples/samples-by-category.md#count-key-vault-resources)
  - Exemple de requête : [Coffre de clés avec nom d’abonnement](../samples/samples-by-category.md#key-vaults-with-subscription-name)
  - Exemple de requête : [ Lister les ressources impactées lors du transfert d’un abonnement Azure](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- Microsoft.Kubernetes/connectedClusters (Kubernetes – Azure Arc)
  - Exemple de requête : [Lister tous les clusters Kubernetes compatibles avec Azure Arc sans l’extension Azure Monitor](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-clusters-without-azure-monitor-extension)
  - Exemple de requête : [Lister toutes les ressources Kubernetes avec Azure Arc](../samples/samples-by-category.md#list-all-azure-arc-enabled-kubernetes-resources)
- Microsoft.Kusto/clusters (Clusters Azure Data Explorer)
- Microsoft.Kusto/clusters/databases (Bases de données Azure Data Explorer)
- Microsoft.LabServices/labAccounts (Lab Services)
- microsoft.labservices/labplans
- microsoft.labservices/labs
- Microsoft.LoadTestService/LoadTests (Tests de chargement natif cloud)
- Microsoft.Logic/integrationAccounts (Comptes d’intégration)
- Microsoft.Logic/integrationServiceEnvironments (Environnements de service d’intégration)
- Microsoft.Logic/integrationServiceEnvironments/managedApis (Connecteur managé)
- Microsoft.Logic/workflows (Applications logiques)
- Microsoft.Logz/monitors (Compte principal Logz)
- Microsoft.Logz/monitors/accounts (Sous-compte Logz)
- Microsoft.MachineLearning/commitmentPlans (Plans de service web Machine Learning Studio (classique))
- Microsoft.MachineLearning/webServices (Services web Machine Learning Studio (classique))
- Microsoft.MachineLearning/workspaces (Espaces de travail Machine Learning Studio (classique))
- microsoft.machinelearningcompute/operationalizationclusters
- microsoft.machinelearningexperimentation/accounts/workspaces
- microsoft.machinelearningservices/aisysteminventories
- microsoft.machinelearningservices/modelinventories
- microsoft.machinelearningservices/modelinventory
- microsoft.machinelearningservices/virtualclusters
- Microsoft.MachineLearningServices/workspaces (Machine Learning)
- microsoft.machinelearningservices/workspaces/batchendpoints
- microsoft.machinelearningservices/workspaces/batchendpoints/deployments
- microsoft.machinelearningservices/workspaces/inferenceendpoints
- microsoft.machinelearningservices/workspaces/inferenceendpoints/deployments
- Microsoft.MachineLearningServices/workspaces/onlineEndpoints (Points de terminaison en ligne Machine Learning)
- Microsoft.MachineLearningServices/workspaces/onlineEndpoints/deployments (Déploiement en ligne Machine Learning)
- Microsoft.Maintenance/maintenanceConfigurations (Configurations de maintenance)
- microsoft.maintenance/maintenancepolicies
- microsoft.managedidentity/groups
- Microsoft.ManagedIdentity/userAssignedIdentities (Identités managées)
  - Exemple de requête : [ Lister les ressources impactées lors du transfert d’un abonnement Azure](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.managednetwork/managednetworkgroups
- microsoft.managednetwork/managednetworkpeeringpolicies
- microsoft.managednetwork/managednetworks
- microsoft.managednetwork/managednetworks/managednetworkgroups
- microsoft.managednetwork/managednetworks/managednetworkpeeringpolicies
- Microsoft.Maps/accounts (Comptes Azure Maps)
- Microsoft.Maps/accounts/creators (Ressources Creator Azure Maps)
- microsoft.maps/accounts/privateatlases
- Microsoft.MarketplaceApps/classicDevServices (Services de développement classiques)
- microsoft.media/mediaservices (Media Services)
- microsoft.media/mediaservices/liveevents (Événements en direct)
- microsoft.media/mediaservices/streamingendpoints (Points de terminaison de streaming)
- microsoft.media/mediaservices/transforms
- microsoft.media/videoanalyzers (Analyseurs vidéo)
- microsoft.microservices4spring/appclusters
- microsoft.migrate/assessmentprojects
- microsoft.migrate/migrateprojects
- microsoft.migrate/movecollections
- Microsoft.Migrate/projects (Projets de migration)
- Microsoft.MixedReality/holographicsBroadcastAccounts (Comptes Holographics Broadcast)
- Microsoft.MixedReality/objectAnchorsAccounts (Comptes Object Anchors)
- Microsoft.MixedReality/objectUnderstandingAccounts (Comptes Object Understanding)
- Microsoft.MixedReality/remoteRenderingAccounts (Comptes Remote Rendering)
- Microsoft.MixedReality/spatialAnchorsAccounts (Comptes Spatial Anchors)
- microsoft.mixedreality/surfacereconstructionaccounts
- Microsoft.MobileNetwork/mobileNetworks (Réseaux mobiles)
- microsoft.mobilenetwork/mobilenetworks/datanetworks
- microsoft.mobilenetwork/mobilenetworks/services
- microsoft.mobilenetwork/mobilenetworks/simpolicies
- Microsoft.MobileNetwork/mobileNetworks/sites (Sites de réseau mobile)
- microsoft.mobilenetwork/mobilenetworks/slices
- microsoft.mobilenetwork/networks
- microsoft.mobilenetwork/networks/sites
- Microsoft.MobileNetwork/packetCoreControlPlanes (Arc pour fonctions réseau - Cœurs de paquets)
- microsoft.mobilenetwork/packetcorecontrolplanes/packetcoredataplanes
- microsoft.mobilenetwork/packetcorecontrolplanes/packetcoredataplanes/attacheddatanetworks
- Microsoft.MobileNetwork/sims (Sims)
- microsoft.mobilenetwork/sims/simprofiles
- microsoft.monitor/accounts
- Microsoft.NetApp/netAppAccounts (Comptes NetApp)
- microsoft.netapp/netappaccounts/backuppolicies
- Microsoft.NetApp/netAppAccounts/capacityPools (Pools de capacité)
- Microsoft.NetApp/netAppAccounts/capacityPools/Volumes (Volumes)
- microsoft.netapp/netappaccounts/capacitypools/volumes/mounttargets
- Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots (Instantanés)
- microsoft.netapp/netappaccounts/capacitypools/volumes/subvolumes
- Microsoft.NetApp/netAppAccounts/snapshotPolicies (Stratégies d’instantané)
- Microsoft.Network/applicationGateways (Passerelles d’application)
- Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies (Stratégies Web Application Firewall (WAF))
- Microsoft.Network/applicationSecurityGroups (Groupes de sécurité d’application)
- Microsoft.Network/azureFirewalls (Pare-feu)
- Microsoft.Network/bastionHosts (Bastions)
- Microsoft.Network/connections (Connexions)
- Microsoft.Network/customIpPrefixes (Préfixes d’adresses IP personnalisés)
- microsoft.network/ddoscustompolicies
- Microsoft.Network/ddosProtectionPlans (Plans de protection DDoS)
- microsoft.network/dnsforwardingrulesets
- microsoft.network/dnsresolvers
- Microsoft.Network/dnsZones (Zones DNS)
- microsoft.network/dscpconfigurations
- Microsoft.Network/expressRouteCircuits (Circuits ExpressRoute)
- microsoft.network/expressroutecrossconnections
- microsoft.network/expressroutegateways
- Microsoft.Network/expressRoutePorts (ExpressRoute Direct)
- Microsoft.Network/firewallPolicies (Stratégies de pare-feu)
- Microsoft.Network/frontdoors (Portes d’entrée)
- Microsoft.Network/FrontDoorWebApplicationFirewallPolicies (Stratégies Web Application Firewall (WAF))
- microsoft.network/ipallocations
- Microsoft.Network/ipGroups (Groupes IP)
- Microsoft.Network/LoadBalancers (Équilibreurs de charge)
- Microsoft.Network/localnetworkgateways (Passerelles de réseau local)
- microsoft.network/mastercustomipprefixes
- Microsoft.Network/natGateways (Passerelles NAT)
- Microsoft.Network/NetworkExperimentProfiles (Profils d’analyseurs Internet)
- microsoft.network/networkintentpolicies
- Microsoft.Network/networkinterfaces (Interfaces réseau)
  - Exemple de requête :[Obtenir les réseaux virtuels et les sous-réseaux des interfaces réseau](../samples/samples-by-category.md#get-virtual-networks-and-subnets-of-network-interfaces)
  - Exemple de requête : [Lister les machines virtuelles avec leur interface réseau et leur adresse IP publique](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
- Microsoft.Network/networkManagers (Gestionnaires de réseau)
- microsoft.network/networkprofiles
- Microsoft.Network/NetworkSecurityGroups (Groupes de sécurité réseau)
  - Exemple de requête : [Afficher les groupes de sécurité réseau non associés](../samples/samples-by-category.md#show-unassociated-network-security-groups)
- microsoft.network/networksecurityperimeters
- microsoft.network/networkvirtualappliances
- microsoft.network/networkwatchers (Observateurs réseau)
- microsoft.network/networkwatchers/connectionmonitors
- microsoft.network/networkwatchers/flowlogs (Journaux de flux NSG)
- microsoft.network/networkwatchers/lenses
- microsoft.network/networkwatchers/pingmeshes
- microsoft.network/p2svpngateways
- Microsoft.Network/privateDnsZones (Zones DNS privées)
- microsoft.network/privatednszones/virtualnetworklinks
- microsoft.network/privateendpointredirectmaps
- Microsoft.Network/privateEndpoints (Points de terminaison privés)
- Microsoft.Network/privateLinkServices (Services de liaison privée)
- Microsoft.Network/PublicIpAddresses (Adresses IP publiques)
  - Exemple de requête : [Lister les machines virtuelles avec leur interface réseau et leur IP publique](../samples/samples-by-category.md#list-virtual-machines-with-their-network-interface-and-public-ip)
- Microsoft.Network/publicIpPrefixes (Préfixes d’adresses IP publiques)
- Microsoft.Network/routeFilters (Filtres de routage)
- Microsoft.Network/routeTables (Tables de routage)
- microsoft.network/sampleresources
- microsoft.network/securitypartnerproviders
- Microsoft.Network/serviceEndpointPolicies (Stratégies de points de terminaison de service)
- Microsoft.Network/trafficmanagerprofiles (Profils Traffic Manager)
- microsoft.network/virtualhubs
- microsoft.network/virtualhubs/bgpconnections
- microsoft.network/virtualhubs/ipconfigurations
- Microsoft.Network/virtualNetworkGateways (Passerelles de réseau virtuel)
- Microsoft.Network/virtualNetworks (Réseaux virtuels)
- microsoft.network/virtualnetworktaps
- microsoft.network/virtualrouters
- Microsoft.Network/virtualWans (Réseaux WAN virtuels)
- microsoft.network/vpngateways
- microsoft.network/vpnserverconfigurations
- microsoft.network/vpnsites
- microsoft.networkfunction/azuretrafficcollectors
- Microsoft.NotificationHubs/namespaces (Espaces de noms du hub de notification)
- Microsoft.NotificationHubs/namespaces/notificationHubs (Hubs de notification)
- microsoft.nutanix/interfaces
- microsoft.nutanix/nodes
- microsoft.objectstore/osnamespaces
- microsoft.offazure/hypervsites
- microsoft.offazure/importsites
- microsoft.offazure/mastersites
- microsoft.offazure/serversites
- microsoft.offazure/vmwaresites
- microsoft.openlogisticsplatform/applicationworkspaces
- Microsoft.OpenLogisticsPlatform/workspaces (Plateforme logistique ouverte)
- microsoft.operationalinsights/clusters
- Microsoft.OperationalInsights/querypacks (Packs de requêtes Log Analytics)
- Microsoft.OperationalInsights/workspaces (Espaces de travail Log Analytics)
- Microsoft.OperationsManagement/solutions (Solutions)
- microsoft.operationsmanagement/views
- Microsoft.Orbital/contactProfiles (ContactProfiles)
- Microsoft.Orbital/GroundStations (GroundStations)
- microsoft.orbital/orbitalendpoints
- microsoft.orbital/orbitalgateways
- microsoft.orbital/orbitalgateways/orbitall2connections
- microsoft.orbital/orbitalgateways/orbitall3connections
- Microsoft.Orbital/spacecrafts (engins spatiaux)
- Microsoft.Peering/peerings (Peerings)
- Microsoft.Peering/peeringServices (Services de peering)
- Microsoft.Portal/dashboards (Tableaux de bord partagés)
- microsoft.portalsdk/rootresources
- microsoft.powerbi/privatelinkservicesforpowerbi
- microsoft.powerbi/tenants
- microsoft.powerbi/workspacecollections
- microsoft.powerbidedicated/autoscalevcores
- Microsoft.PowerBIDedicated/capacities (Power BI Embedded)
- microsoft.powerplatform/accounts
- microsoft.powerplatform/enterprisepolicies
- microsoft.projectbabylon/accounts
- microsoft.providerhubdevtest/regionalstresstests
- Microsoft.Purview/Accounts (Comptes Purview)
- Microsoft.Quantum/Workspaces (Espaces de travail Quantum)
- Microsoft.RecommendationsService/accounts (Comptes de recommandations intelligentes)
- Microsoft.RecommendationsService/accounts/modeling (Modélisation)
- Microsoft.RecommendationsService/accounts/serviceEndpoints (Points de terminaison de service)
- Microsoft.RecoveryServices/vaults (Coffres Recovery Services)
- Microsoft.RedHatOpenShift/openShiftClusters (Clusters OpenShift)
- Microsoft.Relay/namespaces (Relais)
- microsoft.remoteapp/collections
- microsoft.resiliency/chaosexperiments
- Microsoft.ResourceConnector/Appliances (Ponts de ressource)
- Microsoft.resourcegraph/queries (Requêtes Resource Graph)
- Microsoft.Resources/deploymentScripts (Scripts de déploiement)
- Microsoft.Resources/templateSpecs (Specs de modèle)
- microsoft.resources/templatespecs/versions
- Microsoft.SaaS/applications (Logiciel en tant que service (classique))
- Microsoft.SaaS/resources (SaaS)
- Microsoft.Scheduler/jobCollections (Collections de tâches du planificateur)
- Microsoft.Scom/managedInstances (Aquila Instances)
- microsoft.scvmm/clouds
- Microsoft.scvmm/virtualMachines (Machine virtuelle SCVMM – Azure Arc)
- microsoft.scvmm/virtualmachinetemplates
- microsoft.scvmm/virtualnetworks
- microsoft.scvmm/vmmservers
- Microsoft.Search/searchServices (Services de recherche)
- microsoft.security/assignments
- microsoft.security/automations
- microsoft.security/customentitystoreassignments
- microsoft.security/iotsecuritysolutions
- microsoft.security/securityconnectors
- Microsoft.SecurityDetonation/chambers (Chambres de détonation de sécurité)
- Microsoft.ServiceBus/namespaces (Espaces de noms Service Bus)
- Microsoft.ServiceFabric/clusters (Clusters Service Fabric)
- microsoft.servicefabric/containergroupsets
- Microsoft.ServiceFabric/managedclusters (Clusters managés Service Fabric)
- microsoft.servicefabricmesh/applications
- microsoft.servicefabricmesh/gateways
- microsoft.servicefabricmesh/networks
- microsoft.servicefabricmesh/secrets
- microsoft.servicefabricmesh/volumes
- Microsoft.ServicesHub/connectors (Connecteurs de hub de services)
- Microsoft.SignalRService/SignalR (SignalR)
- Microsoft.SignalRService/WebPubSub (Service web PubSub)
- microsoft.singularity/accounts
- microsoft.skytap/nodes
- microsoft.solutions/appliancedefinitions
- microsoft.solutions/appliances
- Microsoft.Solutions/applicationDefinitions (Définitions d’application managée du catalogue de services)
- Microsoft.Solutions/applications (Applications managées)
- microsoft.solutions/jitrequests
- microsoft.spoolservice/spools
- Microsoft.Sql/instancePools (Pools d’instances)
- Microsoft.Sql/managedInstances (Instances managées SQL)
- Microsoft.Sql/managedInstances/databases (Bases de données managées)
- Microsoft.Sql/servers (Serveurs SQL)
- Microsoft.Sql/servers/databases (Bases de données SQL)
  - Exemple de requête : [ Lister les ressources impactées lors du transfert d’un abonnement Azure](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
  - Exemple de requête : [Lister les bases de données SQL et leurs pools élastiques](../samples/samples-by-category.md#list-sql-databases-and-their-elastic-pools)
- Microsoft.Sql/servers/elasticpools (Pools élastiques SQL)
  - Exemple de requête : [Lister les bases de données SQL et leurs pools élastiques](../samples/samples-by-category.md#list-sql-databases-and-their-elastic-pools)
- microsoft.sql/servers/jobaccounts
- Microsoft.Sql/servers/jobAgents (Agents de travail élastique)
- Microsoft.Sql/virtualClusters (Clusters virtuels)
- microsoft.sqlvirtualmachine/sqlvirtualmachinegroups
- Microsoft.SqlVirtualMachine/SqlVirtualMachines (Machines virtuelles SQL)
- microsoft.sqlvm/dwvm
- Microsoft.Storage/StorageAccounts (Comptes de stockage)
  - Exemple de requête : [Rechercher les comptes de stockage avec une étiquette spécifique qui ne respecte pas la casse sur le groupe de ressources](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-insensitive-tag-on-the-resource-group)
  - Exemple de requête : [Rechercher les comptes de stockage avec une étiquette spécifique qui respecte la casse sur le groupe de ressources](../samples/samples-by-category.md#find-storage-accounts-with-a-specific-case-sensitive-tag-on-the-resource-group)
  - Exemple de requête : [Lister tous les comptes de stockage avec une valeur d’étiquette spécifique](../samples/samples-by-category.md#list-all-storage-accounts-with-specific-tag-value)
  - Exemple de requête : [ Lister les ressources impactées lors du transfert d’un abonnement Azure](../samples/samples-by-category.md#list-impacted-resources-when-transferring-an-azure-subscription)
- microsoft.storagecache/amlfilesystems
- Microsoft.StorageCache/caches (Caches HPC)
- Microsoft.StoragePool/diskPools (Pools de disques)
- Microsoft.StorageSync/storageSyncServices (Services de synchronisation de stockage)
- Microsoft.StorageSyncDev/storageSyncServices (Services de synchronisation de stockage)
- Microsoft.StorageSyncInt/storageSyncServices (Services de synchronisation de stockage)
- Microsoft.StorSimple/Managers (Gestionnaires d’appareils StorSimple)
- Microsoft.StreamAnalytics/clusters (Clusters Stream Analytics)
- Microsoft.StreamAnalytics/StreamingJobs (Travaux Stream Analytics)
- microsoft.swiftlet/virtualmachines
- microsoft.swiftlet/virtualmachinesnapshots
- Microsoft.Synapse/privateLinkHubs (Azure Synapse Analytics (hubs de liaisons privées))
- Microsoft.Synapse/workspaces (Azure Synapse Analytics)
- Microsoft.Synapse/workspaces/bigDataPools (Pools Apache Spark)
- microsoft.synapse/workspaces/eventstreams
- Microsoft.Synapse/workspaces/kustopools (Pools Data Explorer (préversion))
- microsoft.synapse/workspaces/sqldatabases
- Microsoft.Synapse/workspaces/sqlPools (Pools SQL dédiés)
- microsoft.terraformoss/providerregistrations
- Microsoft.TestBase/testBaseAccounts (Comptes de base de test)
- microsoft.testbase/testbaseaccounts/packages
- microsoft.testbase/testbases
- Microsoft.TimeSeriesInsights/environments (Environnements Time Series Insights)
- Microsoft.TimeSeriesInsights/environments/eventsources (Sources d’événements Time Series Insights)
- Microsoft.TimeSeriesInsights/environments/referenceDataSets (Jeux de données de référence Time Series Insights)
- microsoft.token/stores
- microsoft.tokenvault/vaults
- Microsoft.VideoIndexer/accounts (Video Analyzer for Media)
- Microsoft.VirtualMachineImages/imageTemplates (Modèles d’image)
- microsoft.visualstudio/account (Organisations Azure DevOps)
- microsoft.visualstudio/account/extension
- microsoft.visualstudio/account/project (Démarrage DevOps)
- microsoft.vmware/arczones
- microsoft.vmware/resourcepools
- microsoft.vmware/vcenters
- microsoft.vmware/virtualmachines
- microsoft.vmware/virtualmachinetemplates
- microsoft.vmware/virtualnetworks
- Microsoft.VMwareCloudSimple/dedicatedCloudNodes (Nœuds CloudSimple)
- Microsoft.VMwareCloudSimple/dedicatedCloudServices (Services CloudSimple)
- Microsoft.VMwareCloudSimple/virtualMachines (Machines virtuelles CloudSimple)
- microsoft.vmwareonazure/privateclouds
- microsoft.vmwarevirtustream/privateclouds
- microsoft.vsonline/accounts
- Microsoft.VSOnline/Plans (Plans Visual Studio Online)
- microsoft.web/apimanagementaccounts
- microsoft.web/apimanagementaccounts/apis
- microsoft.web/certificates
- Microsoft.Web/connectionGateways (Passerelles de données locales)
- Microsoft.Web/connections (Connexions d’API)
- microsoft.web/containerapps
- Microsoft.Web/customApis (Connecteur personnalisé Logic Apps)
- Microsoft.Web/HostingEnvironments (Environnements App Service)
- Microsoft.Web/KubeEnvironments (Environnements App Service Kubernetes)
- Microsoft.Web/serverFarms (Plans App Service)
- Microsoft.Web/sites (App Services)
- microsoft.web/sites/premieraddons
- Microsoft.Web/sites/slots (App Service (emplacements))
- Microsoft.Web/StaticSites (Static Web Apps)
- Microsoft.Web/WorkerApps (Container Apps)
- Microsoft.WindowsESU/multipleActivationKeys (Clés d’activation multiple Windows)
- Microsoft.WindowsIoT/DeviceServices (Windows 10 IoT Core Services)
- microsoft.workloadbuilder/migrationagents
- microsoft.workloadbuilder/workloads
- myget.packagemanagement/services
- NGINX.NGINXPLUS/nginxDeployments (Déploiement NGINX)
- Paraleap.CloudMonix/services (CloudMonix)
- Pokitdok.Platform/services (Plateforme PokitDok)
- private.contoso/employees
- Providers.Test/statefulIbizaEngines (Mes ressources)
- RavenHq.Db/databases (RavenHQ)
- Raygun.CrashReporting/apps (Raygun)
- Sendgrid.Email/accounts (Comptes SendGrid)
- sparkpost.basic/services
- stackify.retrace/services
- test.shoebox/testresources
- test.shoebox/testresources2
- TrendMicro.DeepSecurity/accounts (SaaS Deep Security)
- u2uconsult.theidentityhub/services
- Wandisco.Fusion/fusionGroups (Plans LiveData)
- Wandisco.Fusion/fusionGroups/azureZones (Zones Azure)
- Wandisco.Fusion/fusionGroups/azureZones/plugins (Plug-ins)
- Wandisco.Fusion/fusionGroups/hiveReplicationRules (Règles de réplication Hive)
- Wandisco.Fusion/fusionGroups/managedOnPremZones (Zones locales)
- wandisco.fusion/fusiongroups/onpremzones
- Wandisco.Fusion/fusionGroups/replicationRules (Règles de réplication)
- Wandisco.Fusion/migrators (Instances LiveData Migrator)
- Wandisco.Fusion/migrators/exclusionTemplates (Exclusions)
- Wandisco.Fusion/migrators/liveDataMigrations (Migrations)
- Wandisco.Fusion/migrators/metadataMigrations (Migrations de métadonnées)
- Wandisco.Fusion/migrators/metadataTargets (Cibles de métadonnées)
- Wandisco.Fusion/migrators/pathMappings (Mappages de chemin)
- Wandisco.Fusion/migrators/targets (Cibles)

## <a name="securityresources"></a>securityresources

Pour obtenir des exemples de requêtes pour cette table, consultez [Exemples de requêtes Azure Resource Graph pour securityresources](../samples/samples-by-table.md#securityresources).

- microsoft.security/assessments
  - Exemple de requête : [Compter les ressources saines, défectueuses et non applicables par recommandation](../samples/samples-by-category.md#count-healthy-unhealthy-and-not-applicable-resources-per-recommendation)
  - Exemple de requête : [Lister les recommandations Azure Security Center](../samples/samples-by-category.md#list-azure-security-center-recommendations)
  - Exemple de requête : [Lister les résultats de l’évaluation des vulnérabilités Container Registry](../samples/samples-by-category.md#list-container-registry-vulnerability-assessment-results)
  - Exemple de requête : [Lister les résultats de l’évaluation des vulnérabilités Qualys](../samples/samples-by-category.md#list-qualys-vulnerability-assessment-results)
- microsoft.security/assessments/subassessments
  - Exemple de requête : [Lister les résultats de l’évaluation des vulnérabilités Container Registry](../samples/samples-by-category.md#list-container-registry-vulnerability-assessment-results)
  - Exemple de requête : [Lister les résultats de l’évaluation des vulnérabilités Qualys](../samples/samples-by-category.md#list-qualys-vulnerability-assessment-results)
- microsoft.security/insights/classification (Insights sur la sécurité de la sensibilité des données (préversion))
- microsoft.security/iotalerts
  - Exemple de requête : [Obtenir toutes les alertes IoT sur le hub, filtrées par type](../samples/samples-by-category.md#get-all-iot-alerts-on-hub-filtered-by-type)
  - Exemple de requête : [Obtenir une alerte IoT spécifique](../samples/samples-by-category.md#get-specific-iot-alert)
- microsoft.security/locations/alerts (Alertes de sécurité)
- microsoft.security/pricings
  - Exemple de requête : [Afficher le niveau tarifaire d’Azure Defender par abonnement](../samples/samples-by-category.md#show-azure-defender-pricing-tier-per-subscription)
- microsoft.security/regulatorycompliancestandards
  - Exemple de requête : [État de conformité réglementaire par norme de conformité](../samples/samples-by-category.md#regulatory-compliance-state-per-compliance-standard)
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols
- microsoft.security/regulatorycompliancestandards/regulatorycompliancecontrols/regulatorycomplianceassessments
  - Exemple de requête : [État des évaluations de conformité réglementaire](../samples/samples-by-category.md#regulatory-compliance-assessments-state)
- microsoft.security/securescores
  - Exemple de requête : [Score sécurisé par groupe d’administration](../samples/samples-by-category.md#secure-score-per-management-group)
  - Exemple de requête : [Degré de sécurisation par abonnement](../samples/samples-by-category.md#secure-score-per-subscription)
- microsoft.security/securescores/securescorecontrols
  - Exemple de requête : [Degré de sécurisation des contrôles par abonnement](../samples/samples-by-category.md#controls-secure-score-per-subscription)
- microsoft.security/softwareinventories
- microsoft.security/softwareinventory

## <a name="servicehealthresources"></a>servicehealthresources

Pour obtenir des exemples de requêtes pour cette table, consultez [Exemples de requêtes Azure Resource Graph pour servicehealthresources](../samples/samples-by-table.md#servicehealthresources).

- microsoft.resourcehealth/events
  - Exemple de requête : [Impact de l’abonnement aux événements Service Health actifs](../samples/samples-by-category.md#active-service-health-event-subscription-impact)
  - Exemple de requête : [Tous les événements d’avis d’intégrité actifs](../samples/samples-by-category.md#all-active-health-advisory-events)
  - Exemple de requête : [Tous les événements de maintenance planifiée actifs](../samples/samples-by-category.md#all-active-planned-maintenance-events)
  - Exemple de requête : [Tous les événements Service Health actifs](../samples/samples-by-category.md#all-active-service-health-events)
  - Exemple de requête : [Tous les événements de problèmes de service actifs](../samples/samples-by-category.md#all-active-service-issue-events)

## <a name="workloadmonitorresources"></a>workloadmonitorresources

- microsoft.workloadmonitor/monitors

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus en détails le [langage de requête](../concepts/query-language.md).
- Découvrez plus en détails comment [explorer des ressources](../concepts/explore-resources.md).
- Consulter des exemples de [requêtes de démarrage](../samples/starter.md).
