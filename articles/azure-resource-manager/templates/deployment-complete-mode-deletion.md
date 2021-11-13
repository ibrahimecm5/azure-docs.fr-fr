---
title: Effectuer la suppression du mode
description: Montre comment les types de ressources gèrent la suppression du mode complet dans les modèles Azure Resource Manager.
ms.topic: conceptual
ms.date: 10/20/2021
ms.openlocfilehash: ea3ea4de33a1680961f078e8e3cc8815da45956e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229896"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Suppression de ressources Azure pour les déploiements en mode complet

Cet article décrit la manière dont les types de ressources gèrent la suppression en dehors d'un modèle déployé en mode complet.

Les types de ressources marqués d’un **Oui** sont supprimés lorsque le type n’est pas présent dans le modèle déployé en mode complet.

Les types de ressources marqués d’un **Non** ne sont pas automatiquement supprimés lorsqu'ils ne sont pas présents dans le modèle, mais sont supprimés si la ressource parente est supprimée. Pour une description complète du comportement, consultez [Modes de déploiement Azure Resource Manager](deployment-modes.md).

Si vous déployez sur [plusieurs groupes de ressources dans un modèle](./deploy-to-resource-group.md), les ressources du groupe de ressources spécifié dans le déploiement peuvent être supprimées. Les ressources dans les groupes de ressources secondaires ne sont pas supprimées.

Les ressources sont listées par espace de noms de fournisseur de ressources. Pour faire correspondre un espace de noms de fournisseur de ressources avec son nom de service Azure, consultez [Fournisseurs de ressources pour les services Azure](../management/azure-services-resource-providers.md).

> [!NOTE]
> Effectuez toujours l’[opération de simulation](./deploy-what-if.md) avant de déployer un modèle en mode complet. La simulation vous indique les ressources qui seront créées, supprimées ou modifiées. Effectuez une simulation pour éviter la suppression accidentelle de ressources.

Accédez à un espace de noms du fournisseur de ressources :
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.AnyBuild](#microsoftanybuild)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppAssessment](#microsoftappassessment)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureArcData](#microsoftazurearcdata)
> - [Microsoft.AzureCIS](#microsoftazurecis)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzurePercept](#microsoftazurepercept)
> - [Microsoft.AzureSphere](#microsoftazuresphere)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.BackupSolutions](#microsoftbackupsolutions)
> - [Microsoft.BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BillingBenefits](#microsoftbillingbenefits)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cascade](#microsoftcascade)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClusterStor](#microsoftclusterstor)
> - [Microsoft.CodeSigning](#microsoftcodesigning)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.ConfidentialLedger](#microsoftconfidentialledger)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft.ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.Dashboard](#microsoftdashboard)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DelegatedNetwork](#microsoftdelegatednetwork)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.Diagnostics](#microsoftdiagnostics)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EdgeOrder](#microsoftedgeorder)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Fidalgo](#microsoftfidalgo)
> - [Microsoft.FluidRelay](#microsoftfluidrelay)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.Graph](#microsoftgraph)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthBot](#microsofthealthbot)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HpcWorkbench](#microsofthpcworkbench)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridConnectivity](#microsofthybridconnectivity)
> - [Microsoft.HybridContainerService](#microsofthybridcontainerservice)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSecurity](#microsoftiotsecurity)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceNotifications](#microsoftmarketplacenotifications)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.MobileNetwork](#microsoftmobilenetwork)
> - [Microsoft.Monitor](#microsoftmonitor)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.NetworkFunction](#microsoftnetworkfunction)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PlayFab](#microsoftplayfab)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.Quota](#microsoftquota)
> - [Microsoft.RecommendationsService](#microsoftrecommendationsservice)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceConnector](#microsoftresourceconnector)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scom](#microsoftscom)
> - [Microsoft.ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.ServiceLinker](#microsoftservicelinker)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Singularity](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TestBase](#microsofttestbase)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VideoIndexer](#microsoftvideoindexer)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | DomainServices | Oui |
> | DomainServices / oucontainer | Non |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | supportProviders | Non |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | aadsupportcases | Non |
> | addsservices | Non |
> | agents | Non |
> | anonymousapiusers | Non |
> | configuration | Non |
> | logs | Non |
> | reports | Non |
> | servicehealthmetrics | Non |
> | services | Non |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | advisorScore | Non |
> | configurations | Non |
> | generateRecommendations | Non |
> | metadata | Non |
> | de films | Non |
> | suppressions | Non |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | farmBeats | Oui |
> | farmBeats / eventGridFilters | Non |
> | farmBeats / extensions | Non |
> | farmBeatsExtensionDefinitions | Non |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | actionRules | Oui |
> | alertes | Non |
> | alertsList | Non |
> | alertsMetaData | Non |
> | alertsSummary | Non |
> | alertsSummaryList | Non |
> | migrateFromSmartDetection | Non |
> | resourceHealthAlertRules | Oui |
> | smartDetectorAlertRules | Oui |
> | smartGroups | Non |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | servers | Oui |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | Non |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | deletedServices | Non |
> | getDomainOwnershipIdentifier | Non |
> | reportFeedback | Non |
> | service | Oui |
> | service / eventGridFilters | Non |
> | validateServiceName | Non |

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | migrateProjects | Non |
> | migrateProjects / assessments | Non |
> | migrateProjects / assessments / assessedApplications | Non |
> | migrateProjects / assessments / assessedApplications / machines | Non |
> | migrateProjects / assessments / assessedMachines | Non |
> | migrateProjects / assessments / assessedMachines / applications | Non |
> | migrateProjects / assessments / machinesToAssess | Non |
> | migrateProjects / sites | Non |
> | migrateProjects / sites / applianceConfigurations | Non |
> | migrateProjects / sites / machines | Non |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | configurationStores | Oui |
> | configurationStores / eventGridFilters | Non |
> | configurationStores / keyValues | Non |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Spring | Oui |
> | Spring / apps | Non |
> | Spring / apps / deployments | Non |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | attestationProviders | Oui |
> | defaultProviders | Non |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | Non |
> | accessReviewScheduleSettings | Non |
> | batchResourceCheckAccess | Non |
> | classicAdministrators | Non |
> | dataAliases | Non |
> | dataPolicyManifests | Non |
> | denyAssignments | Non |
> | diagnosticSettings | Non |
> | diagnosticSettingsCategories | Non |
> | elevateAccess | Non |
> | eligibleChildResources | Non |
> | findOrphanRoleAssignments | Non |
> | locks | Non |
> | autorisations | Non |
> | policyAssignments | Non |
> | policyDefinitions | Non |
> | policyExemptions | Non |
> | policySetDefinitions | Non |
> | privateLinkAssociations | Non |
> | providerOperations | Non |
> | resourceManagementPrivateLinks | Oui |
> | roleAssignmentApprovals | Non |
> | roleAssignments | Non |
> | roleAssignmentScheduleInstances | Non |
> | roleAssignmentScheduleRequests | Non |
> | roleAssignmentSchedules | Non |
> | roleAssignmentsUsageMetrics | Non |
> | roleDefinitions | Non |
> | roleEligibilityScheduleInstances | Non |
> | roleEligibilityScheduleRequests | Non |
> | roleEligibilitySchedules | Non |
> | roleManagementPolicies | Non |
> | roleManagementPolicyAssignments | Non |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |
> | bestPractices | Non |
> | bestPractices / versions | Non |
> | configurationProfileAssignmentIntents | Non |
> | configurationProfileAssignments | Non |
> | configurationProfilePreferences | Oui |
> | configurationProfiles | Oui |
> | configurationProfiles / versions | Oui |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | automationAccounts | Oui |
> | automationAccounts / configurations | Oui |
> | automationAccounts / hybridRunbookWorkerGroups | Non |
> | automationAccounts / hybridRunbookWorkerGroups / hybridRunbookWorkers | Non |
> | automationAccounts / jobs | Non |
> | automationAccounts / privateEndpointConnectionProxies | Non |
> | automationAccounts / privateEndpointConnections | Non |
> | automationAccounts / privateLinkResources | Non |
> | automationAccounts / runbooks | Oui |
> | automationAccounts / softwareUpdateConfigurations | Non |
> | automationAccounts / webhooks | Non |

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Clouds privés | Oui |
> | Clouds privés / modules complémentaires | Non |
> | Clouds privés / autorisations | Non |
> | privateClouds / cloudLinks | Non |
> | Clouds privés / clusters | Non |
> | privateClouds / clusters / datastores | Non |
> | privateClouds / clusters / placementPolicies | Non |
> | privateClouds / clusters / virtualMachines | Non |
> | privateClouds / globalReachConnections | Non |
> | Clouds privés / hcxEnterpriseSites | Non |
> | privateClouds / scriptExecutions | Non |
> | privateClouds / scriptPackages | Non |
> | privateClouds / scriptPackages / scriptCmdlets | Non |
> | privateClouds / workloadNetworks | Non |
> | privateClouds / workloadNetworks / dhcpConfigurations | Non |
> | privateClouds / workloadNetworks / dnsServices | Non |
> | privateClouds / workloadNetworks / dnsZones | Non |
> | privateClouds / workloadNetworks / gateways | Non |
> | privateClouds / workloadNetworks / portMirroringProfiles | Non |
> | privateClouds / workloadNetworks / publicIPs | Non |
> | privateClouds / workloadNetworks / segments | Non |
> | privateClouds / workloadNetworks / virtualMachines | Non |
> | privateClouds / workloadNetworks / vmGroups | Non |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | environments | Non |
> | environments / accounts | Non |
> | environments / accounts / namespaces | Non |
> | environments / accounts / namespaces / configurations | Non |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | b2cDirectories | Oui |
> | b2ctenants | Non |
> | guestUsages | Oui |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | DataControllers | Non |
> | DataWarehouseInstances | Non |
> | PostgresInstances | Non |
> | SqlManagedInstances | Non |
> | SqlServerInstances | Non |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | autopilotEnvironments | Non |
> | dstsServiceAccounts | Non |
> | dstsServiceClientIdentities | Non |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | sqlServerRegistrations | Oui |
> | sqlServerRegistrations / sqlServers | Non |

## <a name="microsoftazurepercept"></a>Microsoft.AzurePercept

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Non |
> | accounts / devices | Non |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | catalogs | Non |
> | catalogs / certificates | Non |
> | catalogs / deployments | Non |
> | catalogs / devices | Non |
> | catalogs / images | Non |
> | catalogs / products | Non |
> | catalogs / products / devicegroups | Non |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | cloudManifestFiles | Non |
> | linkedSubscriptions | Oui |
> | registrations | Oui |
> | registrations / customerSubscriptions | Non |
> | registrations / products | Non |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | Non |
> | clusters / arcsettings | Non |
> | clusters / arcsettings / extensions | Non |
> | galleryImages | Non |
> | networkInterfaces | Non |
> | virtualHardDisks | Non |
> | virtualmachines | Non |
> | virtualmachines / extensions | Non |
> | virtualmachines / hybrididentitymetadata | Non |
> | virtualNetworks | Non |

## <a name="microsoftbackupsolutions"></a>Microsoft.BackupSolutions

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | VMwareApplications | Oui |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | bareMetalInstances | Oui |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | batchAccounts | Oui |
> | batchAccounts / certificates | Non |
> | batchAccounts / pools | Non |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | billingAccounts | Non |
> | billingAccounts / agreements | Non |
> | billingAccounts / appliedReservationOrders | Non |
> | billingAccounts / billingPermissions | Non |
> | billingAccounts / billingProfiles | Non |
> | billingAccounts / billingProfiles / billingPermissions | Non |
> | billingAccounts / billingProfiles / billingRoleAssignments | Non |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Non |
> | billingAccounts / billingProfiles / billingSubscriptions | Non |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Non |
> | billingAccounts / billingProfiles / customers | Non |
> | billingAccounts / billingProfiles / instructions | Non |
> | billingAccounts / billingProfiles / invoices | Non |
> | billingAccounts / billingProfiles / invoices / pricesheet | Non |
> | billingAccounts / billingProfiles / invoices / transactions | Non |
> | billingAccounts / billingProfiles / invoiceSections | Non |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Non |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Non |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Non |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Non |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Non |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Non |
> | billingAccounts / billingProfiles / invoiceSections / products | Non |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Non |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Non |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Non |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Non |
> | billingAccounts / billingProfiles / invoiceSections / validateDeleteInvoiceSectionEligibility | Non |
> | billingAccounts / BillingProfiles / patchOperations | Non |
> | billingAccounts / billingProfiles / paymentMethodLinks | Non |
> | billingAccounts / billingProfiles / paymentMethods | Non |
> | billingAccounts / billingProfiles / policies | Non |
> | billingAccounts / billingProfiles / pricesheet | Non |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Non |
> | billingAccounts / billingProfiles / products | Non |
> | billingAccounts / billingProfiles / reservations | Non |
> | billingAccounts / billingProfiles / transactions | Non |
> | billingAccounts / billingProfiles / validateDeleteBillingProfileEligibility | Non |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | Non |
> | billingAccounts / billingRoleAssignments | Non |
> | billingAccounts / billingRoleDefinitions | Non |
> | billingAccounts / billingSubscriptions | Non |
> | billingAccounts / billingSubscriptions / elevateRole | Non |
> | billingAccounts / billingSubscriptions / invoices | Non |
> | billingAccounts / createBillingRoleAssignment | Non |
> | billingAccounts / createInvoiceSectionOperations | Non |
> | billingAccounts / customers | Non |
> | billingAccounts / customers / billingPermissions | Non |
> | billingAccounts / customers / billingSubscriptions | Non |
> | billingAccounts / customers / initiateTransfer | Non |
> | billingAccounts / customers / policies | Non |
> | billingAccounts / customers / products | Non |
> | billingAccounts / customers / transactions | Non |
> | billingAccounts / customers / transfers | Non |
> | billingAccounts / customers / transferSupportedAccounts | Non |
> | billingAccounts / departments | Non |
> | billingAccounts / departments / billingPermissions | Non |
> | billingAccounts / departments / billingRoleAssignments | Non |
> | billingAccounts / departments / billingRoleDefinitions | Non |
> | billingAccounts / departments / billingSubscriptions | Non |
> | billingAccounts / departments / enrollmentAccounts | Non |
> | billingAccounts / enrollmentAccounts | Non |
> | billingAccounts / enrollmentAccounts / billingPermissions | Non |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Non |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Non |
> | billingAccounts / enrollmentAccounts / billingSubscriptions | Non |
> | billingAccounts / invoices | Non |
> | billingAccounts / invoices / transactions | Non |
> | billingAccounts / invoices / transactionSummary | Non |
> | billingAccounts / invoiceSections | Non |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Non |
> | billingAccounts / invoiceSections / billingSubscriptions | Non |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Non |
> | billingAccounts / invoiceSections / elevate | Non |
> | billingAccounts / invoiceSections / initiateTransfer | Non |
> | billingAccounts / invoiceSections / patchOperations | Non |
> | billingAccounts / invoiceSections / productMoveOperations | Non |
> | billingAccounts / invoiceSections / products | Non |
> | billingAccounts / invoiceSections / products / transfer | Non |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Non |
> | billingAccounts / invoiceSections / transactions | Non |
> | billingAccounts / invoiceSections / transfers | Non |
> | billingAccounts / lineOfCredit | Non |
> | billingAccounts / patchOperations | Non |
> | billingAccounts / payableOverage | Non |
> | billingAccounts / paymentMethods | Non |
> | billingAccounts / payNow | Non |
> | billingAccounts / policies | Non |
> | billingAccounts / products | Non |
> | billingAccounts / promotionalCredits | Non |
> | billingAccounts / reservations | Non |
> | billingAccounts / savingsPlanOrders | Non |
> | billingAccounts / savingsPlanOrders / savingsPlans | Non |
> | billingAccounts / savingsPlans | Non |
> | billingAccounts / transactions | Non |
> | billingPeriods | Non |
> | billingPermissions | Non |
> | billingProperty | Non |
> | billingRoleAssignments | Non |
> | billingRoleDefinitions | Non |
> | createBillingRoleAssignment | Non |
> | departments | Non |
> | enrollmentAccounts | Non |
> | invoices | Non |
> | paymentMethods | Non |
> | promotionalCredits | Non |
> | promotions | Non |
> | transfers | Non |
> | transfers / acceptTransfer | Non |
> | transfers / declineTransfer | Non |
> | transfers / operationStatus | Non |
> | transfers / validateTransfer | Non |
> | validateAddress | Non |

## <a name="microsoftbillingbenefits"></a>Microsoft.BillingBenefits

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | savingsPlanOrderAliases | Non |
> | validate | Non |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | blockchainMembers | Oui |
> | cordaMembers | Oui |
> | watchers | Oui |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | TokenServices | Oui |
> | TokenServices / BlockchainNetworks | Non |
> | TokenServices / Groups | Non |
> | TokenServices / Groups / Accounts | Non |
> | TokenServices / TokenTemplates | Non |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | blueprintAssignments | Non |
> | blueprintAssignments / assignmentOperations | Non |
> | blueprintAssignments / operations | Non |
> | blueprints | Non |
> | blueprints / artifacts | Non |
> | blueprints / versions | Non |
> | blueprints / versions / artifacts | Non |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | botServices | Oui |
> | botServices / channels | Non |
> | botServices / connections | Non |
> | botServices / privateEndpointConnectionProxies | Non |
> | botServices / privateEndpointConnections | Non |
> | botServices / privateLinkResources | Non |
> | hostSettings | Non |
> | languages | Non |
> | modèles | Non |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Redis | Oui |
> | Redis / EventGridFilters | Non |
> | Redis / privateEndpointConnectionProxies | Non |
> | Redis / privateEndpointConnectionProxies / validate | Non |
> | Redis / privateEndpointConnections | Non |
> | Redis / privateLinkResources | Non |
> | redisEnterprise | Oui |
> | redisEnterprise / databases | Non |
> | RedisEnterprise / privateEndpointConnectionProxies | Non |
> | RedisEnterprise / privateEndpointConnectionProxies / validate | Non |
> | RedisEnterprise / privateEndpointConnections | Non |
> | RedisEnterprise / privateLinkResources | Non |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | appliedReservations | Non |
> | autoQuotaIncrease | Non |
> | calculateExchange | Non |
> | calculatePrice | Non |
> | calculatePurchasePrice | Non |
> | catalogs | Non |
> | commercialReservationOrders | Non |
> | change | Non |
> | ownReservations | Non |
> | placePurchaseOrder | Non |
> | reservationOrders | Non |
> | reservationOrders / calculateRefund | Non |
> | reservationOrders / merge | Non |
> | reservationOrders / reservations | Non |
> | reservationOrders / reservations / revisions | Non |
> | reservationOrders / return | Non |
> | reservationOrders / split | Non |
> | reservationOrders / swap | Non |
> | reservations | Non |
> | resourceProviders | Non |
> | les ressources | Non |
> | validateReservationOrder | Non |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | sites | Non |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Non |
> | CdnWebApplicationFirewallPolicies | Oui |
> | edgenodes | Non |
> | profiles | Oui |
> | profiles / afdendpoints | Oui |
> | profiles / afdendpoints / routes | Non |
> | profiles / customdomains | Non |
> | profiles / endpoints | Oui |
> | profiles / endpoints / customdomains | Non |
> | profiles / endpoints / origingroups | Non |
> | profiles / endpoints / origins | Non |
> | profiles / origingroups | Non |
> | profiles / origingroups / origins | Non |
> | profiles / rulesets | Non |
> | profiles / rulesets / rules | Non |
> | profiles / secrets | Non |
> | profiles / securitypolicies | Non |
> | validateProbe | Non |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | certificateOrders | Oui |
> | certificateOrders / certificates | Non |
> | validateCertificateRegistrationInformation | Non |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | modifications | Non |
> | changeSnapshots | Non |
> | computeChanges | Non |
> | profile | Non |
> | resourceChanges | Non |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | capabilities | Non |
> | domainNames | Oui |
> | domainNames / capabilities | Non |
> | domainNames / internalLoadBalancers | Non |
> | domainNames / serviceCertificates | Non |
> | domainNames / slots | Non |
> | domainNames / slots / roles | Non |
> | domainNames / slots / roles / metricDefinitions | Non |
> | domainNames / slots / roles / metrics | Non |
> | moveSubscriptionResources | Non |
> | operatingSystemFamilies | Non |
> | operatingSystems | Non |
> | quotas | Non |
> | resourceTypes | Non |
> | validateSubscriptionMoveAvailability | Non |
> | virtualMachines | Oui |
> | virtualMachines / diagnosticSettings | Non |
> | virtualMachines / metricDefinitions | Non |
> | virtualMachines / metrics | Non |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | classicInfrastructureResources | Non |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | capabilities | Non |
> | expressRouteCrossConnections | Non |
> | expressRouteCrossConnections / peerings | Non |
> | gatewaySupportedDevices | Non |
> | networkSecurityGroups | Oui |
> | quotas | Non |
> | reservedIps | Oui |
> | virtualNetworks | Oui |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Non |
> | virtualNetworks / virtualNetworkPeerings | Non |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | capabilities | Non |
> | disks | Non |
> | images | Non |
> | osImages | Non |
> | osPlatformImages | Non |
> | publicImages | Non |
> | quotas | Non |
> | storageAccounts | Oui |
> | storageAccounts / blobServices | Non |
> | storageAccounts / fileServices | Non |
> | storageAccounts / metricDefinitions | Non |
> | storageAccounts / metrics | Non |
> | storageAccounts / queueServices | Non |
> | storageAccounts / services | Non |
> | storageAccounts / services / diagnosticSettings | Non |
> | storageAccounts / services / metricDefinitions | Non |
> | storageAccounts / services / metrics | Non |
> | storageAccounts / tableServices | Non |
> | storageAccounts / vmImages | Non |
> | vmImages | Non |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | nœuds | Oui |

## <a name="microsoftcodesigning"></a>Microsoft.CodeSigning

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | codeSigningAccounts | Non |
> | codeSigningAccounts / certificateProfiles | Non |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | plans | Oui |
> | registeredSubscriptions | Non |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |
> | accounts / privateEndpointConnectionProxies | Non |
> | accounts / privateEndpointConnections | Non |
> | accounts / privateLinkResources | Non |
> | deletedAccounts | Non |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | availabilitySets | Oui |
> | cloudServices | Oui |
> | cloudServices / networkInterfaces | Non |
> | cloudServices / publicIPAddresses | Non |
> | cloudServices / roleInstances | Non |
> | cloudServices / roleInstances / networkInterfaces | Non |
> | cloudServices / roles | Non |
> | diskAccesses | Oui |
> | diskEncryptionSets | Oui |
> | disks | Oui |
> | galleries | Oui |
> | galleries / applications | Non |
> | galleries / applications / versions | Non |
> | galleries / images | Oui |
> | galleries / images / versions | Oui |
> | hostGroups | Oui |
> | hostGroups / hosts | Oui |
> | images | Oui |
> | proximityPlacementGroups | Oui |
> | restorePointCollections | Oui |
> | restorePointCollections / restorePoints | Non |
> | sharedVMExtensions | Oui |
> | sharedVMExtensions / versions | Non |
> | sharedVMImages | Oui |
> | sharedVMImages / versions | Non |
> | snapshots | Oui |
> | sshPublicKeys | Oui |
> | virtualMachines | Oui |
> | virtualMachines / extensions | Oui |
> | virtualMachines / metricDefinitions | Non |
> | virtualMachines / runCommands | Oui |
> | virtualMachineScaleSets | Oui |
> | virtualMachineScaleSets / extensions | Non |
> | virtualMachineScaleSets / networkInterfaces | Non |
> | virtualMachineScaleSets / publicIPAddresses | Non |
> | virtualMachineScaleSets / virtualMachines | Non |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Non |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | RateCard | Non |
> | UsageAggregates | Non |

## <a name="microsoftconfidentialledger"></a>Microsoft.ConfidentialLedger

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Ledgers | Non |

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | CacheNodes | Non |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | platformAccounts | Non |
> | registeredSubscriptions | Non |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Clusters | Non |
> | Magasins de données | Non |
> | Hôtes | Non |
> | ResourcePools | Non |
> | VCenters | Non |
> | VCenters / InventoryItems | Non |
> | VirtualMachines | Non |
> | VirtualMachines / Extensions | Oui |
> | VirtualMachines / GuestAgents | Non |
> | VirtualMachines / HybridIdentityMetadata | Non |
> | VirtualMachineTemplates | Non |
> | VirtualNetworks | Non |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | AggregatedCost | Non |
> | Balances | Non |
> | Budgets | Non |
> | Charges | Non |
> | CostTags | Non |
> | credits | Non |
> | événements | Non |
> | Prévisions | Non |
> | lots | Non |
> | Marketplaces | Non |
> | Pricesheets | Non |
> | products | Non |
> | ReservationDetails | Non |
> | ReservationRecommendationDetails | Non |
> | ReservationRecommendations | Non |
> | ReservationSummaries | Non |
> | ReservationTransactions | Non |
> | Balises | Non |
> | tenants | Non |
> | Termes | Non |
> | UsageDetails | Non |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | containerGroups | Oui |
> | serviceAssociationLinks | Non |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | registries | Oui |
> | registries / agentPools | Oui |
> | registries / builds | Non |
> | registries / builds / cancel | Non |
> | registries / builds / getLogLink | Non |
> | registries / buildTasks | Oui |
> | registries / buildTasks / steps | Non |
> | registries / connectedRegistries | Non |
> | registries / connectedRegistries / deactivate | Non |
> | registries / eventGridFilters | Non |
> | registries / exportPipelines | Non |
> | registries / generateCredentials | Non |
> | registries / getBuildSourceUploadUrl | Non |
> | registries / GetCredentials | Non |
> | registries / importImage | Non |
> | registries / importPipelines | Non |
> | registries / pipelineRuns | Non |
> | registries / privateEndpointConnectionProxies | Non |
> | registries / privateEndpointConnectionProxies / validate | Non |
> | registries / privateEndpointConnections | Non |
> | registries / privateLinkResources | Non |
> | registries / queueBuild | Non |
> | registries / regenerateCredential | Non |
> | registries / regenerateCredentials | Non |
> | registries / replications | Oui |
> | registries / runs | Non |
> | registries / runs / cancel | Non |
> | registries / scheduleRun | Non |
> | registries / scopeMaps | Non |
> | registries / taskRuns | Non |
> | registries / tasks | Oui |
> | registries / tokens | Non |
> | registries / updatePolicies | Non |
> | registries / webhooks | Oui |
> | registries / webhooks / getCallbackConfig | Non |
> | registries / webhooks / ping | Non |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | containerServices | Oui |
> | managedclusters | Oui |
> | ManagedClusters / eventGridFilters | Non |
> | openShiftManagedClusters | Oui |
> | snapshots | Oui |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Alertes | Non |
> | BenefitUtilizationSummaries | Non |
> | BillingAccounts | Non |
> | Budgets | Non |
> | calculatePrice | Non |
> | CloudConnectors | Non |
> | Connecteurs | Oui |
> | costAllocationRules | Non |
> | Departments | Non |
> | Dimensions | Non |
> | EnrollmentAccounts | Non |
> | Exports | Non |
> | ExternalBillingAccounts | Non |
> | ExternalBillingAccounts / Alerts | Non |
> | ExternalBillingAccounts / Dimensions | Non |
> | ExternalBillingAccounts / Forecast | Non |
> | ExternalBillingAccounts / Query | Non |
> | ExternalSubscriptions | Non |
> | ExternalSubscriptions / Alerts | Non |
> | ExternalSubscriptions / Dimensions | Non |
> | ExternalSubscriptions / Forecast | Non |
> | ExternalSubscriptions / Query | Non |
> | fetchPrices | Non |
> | Forecast | Non |
> | GenerateDetailedCostReport | Non |
> | GenerateReservationDetailsReport | Non |
> | Insights | Non |
> | Requête | Non |
> | inscription | Non |
> | Reportconfigs | Non |
> | Rapports | Non |
> | ScheduledActions | Non |
> | Paramètres | Non |
> | showbackRules | Non |
> | Les vues | Non |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | DisableLockbox | Non |
> | EnableLockbox | Non |
> | requêtes | Non |
> | TenantOptedIn | Non |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | associations | Non |
> | resourceProviders | Oui |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | instances | Oui |

## <a name="microsoftdashboard"></a>Microsoft.Dashboard

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | grafana | Non |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | jobs | Oui |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Oui |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | workspaces | Oui |
> | workspaces / dbWorkspaces | Non |
> | workspaces / virtualNetworkPeerings | Non |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | catalogs | Oui |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | dataFactories | Oui |
> | dataFactories / diagnosticSettings | Non |
> | dataFactories / metricDefinitions | Non |
> | dataFactorySchema | Non |
> | factories | Oui |
> | factories / integrationRuntimes | Non |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |
> | accounts / dataLakeStoreAccounts | Non |
> | accounts / storageAccounts | Non |
> | accounts / storageAccounts / containers | Non |
> | accounts / transferAnalyticsUnits | Non |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |
> | accounts / eventGridFilters | Non |
> | accounts / firewallRules | Non |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | DatabaseMigrations | Non |
> | services | Oui |
> | services / projects | Oui |
> | SqlMigrationServices | Oui |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | BackupVaults | Oui |
> | ResourceGuards | Oui |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |
> | accounts / shares | Non |
> | accounts / shares / datasets | Non |
> | accounts / shares / invitations | Non |
> | accounts / shares / providersharesubscriptions | Non |
> | accounts / shares / synchronizationSettings | Non |
> | accounts / sharesubscriptions | Non |
> | accounts / sharesubscriptions / consumerSourceDataSets | Non |
> | accounts / sharesubscriptions / datasetmappings | Non |
> | accounts / sharesubscriptions / triggers | Non |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | servers | Oui |
> | servers / advisors | Non |
> | servers / keys | Non |
> | servers / privateEndpointConnectionProxies | Non |
> | servers / privateEndpointConnections | Non |
> | servers / privateLinkResources | Non |
> | servers / queryTexts | Non |
> | servers / recoverableServers | Non |
> | servers / resetQueryPerformanceInsightData | Non |
> | servers / start | Non |
> | servers / stop | Non |
> | servers / topQueryStatistics | Non |
> | servers / virtualNetworkRules | Non |
> | servers / waitStatistics | Non |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | flexibleServers | Oui |
> | getPrivateDnsZoneSuffix | Non |
> | servers | Oui |
> | servers / advisors | Non |
> | servers / keys | Non |
> | servers / privateEndpointConnectionProxies | Non |
> | servers / privateEndpointConnections | Non |
> | servers / privateLinkResources | Non |
> | servers / queryTexts | Non |
> | servers / recoverableServers | Non |
> | servers / resetQueryPerformanceInsightData | Non |
> | servers / start | Non |
> | servers / stop | Non |
> | servers / topQueryStatistics | Non |
> | servers / upgrade | Non |
> | servers / virtualNetworkRules | Non |
> | servers / waitStatistics | Non |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | flexibleServers | Oui |
> | getPrivateDnsZoneSuffix | Non |
> | serverGroups | Oui |
> | serverGroupsv2 | Oui |
> | servers | Oui |
> | servers / advisors | Non |
> | servers / keys | Non |
> | servers / privateEndpointConnectionProxies | Non |
> | servers / privateEndpointConnections | Non |
> | servers / privateLinkResources | Non |
> | servers / queryTexts | Non |
> | servers / recoverableServers | Non |
> | servers / resetQueryPerformanceInsightData | Non |
> | servers / topQueryStatistics | Non |
> | servers / virtualNetworkRules | Non |
> | servers / waitStatistics | Non |
> | serversv2 | Oui |

## <a name="microsoftdelegatednetwork"></a>Microsoft.DelegatedNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | contrôleur | Oui |
> | delegatedSubnets | Oui |
> | orchestrators | Oui |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | artifactSources | Oui |
> | rollouts | Oui |
> | serviceTopologies | Oui |
> | serviceTopologies / services | Oui |
> | serviceTopologies / services / serviceUnits | Oui |
> | steps | Oui |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applicationgroups | Oui |
> | applicationgroups / applications | Non |
> | applicationgroups / desktops | Non |
> | applicationgroups / startmenuitems | Non |
> | hostpools | Oui |
> | hostpools / msixpackages | Non |
> | hostpools / sessionhosts | Non |
> | hostpools / sessionhosts / usersessions | Non |
> | hostpools / usersessions | Non |
> | scalingplans | Oui |
> | workspaces | Oui |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | ElasticPools | Oui |
> | ElasticPools / IotHubTenants | Oui |
> | ElasticPools / IotHubTenants / securitySettings | Non |
> | IoTHubs | Oui |
> | IotHubs / eventGridFilters | Non |
> | IotHubs / failover | Non |
> | IotHubs / securitySettings | Non |
> | ProvisioningServices | Oui |
> | usages | Non |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Non |
> | accounts / instances | Non |
> | registeredSubscriptions | Non |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | pipelines | Oui |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | controllers | Oui |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | labcenters | Oui |
> | labs | Oui |
> | labs / environments | Oui |
> | labs / serviceRunners | Oui |
> | labs / virtualMachines | Oui |
> | schedules | Oui |

## <a name="microsoftdiagnostics"></a>Microsoft.Diagnostics

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | AzureKB | Non |
> | InsightDiagnostics | Non |
> | solutions | Non |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | digitalTwinsInstances | Oui |
> | digitalTwinsInstances / endpoints | Non |
> | digitalTwinsInstances / timeSeriesDatabaseConnections | Non |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | cassandraClusters | Oui |
> | databaseAccountNames | Non |
> | databaseAccounts | Oui |
> | restorableDatabaseAccounts | Non |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | domaines | Oui |
> | domains / domainOwnershipIdentifiers | Non |
> | generateSsoRequest | Non |
> | topLevelDomains | Non |
> | validateDomainRegistrationInformation | Non |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | lcsprojects | Non |
> | lcsprojects / clouddeployments | Non |
> | lcsprojects / connectors | Non |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Adresses | Oui |
> | orderItems | Oui |
> | orders | Non |
> | productFamiliesMetadata | Non |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | services | Oui |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | domaines | Oui |
> | domains / topics | Non |
> | eventSubscriptions | Non |
> | extensionTopics | Non |
> | partnerNamespaces | Oui |
> | partnerNamespaces / eventChannels | Non |
> | partnerRegistrations | Oui |
> | partnerTopics | Oui |
> | partnerTopics / eventSubscriptions | Non |
> | systemTopics | Oui |
> | systemTopics / eventSubscriptions | Non |
> | topics | Oui |
> | topicTypes | Non |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | Oui |
> | espaces de noms | Oui |
> | namespaces / authorizationrules | Non |
> | namespaces / disasterrecoveryconfigs | Non |
> | namespaces / eventhubs | Non |
> | namespaces / eventhubs / authorizationrules | Non |
> | namespaces / eventhubs / consumergroups | Non |
> | namespaces / networkrulesets | Non |
> | namespaces / privateEndpointConnections | Non |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | experimentWorkspaces | Oui |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | espaces de noms | Oui |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | featureConfigurations | Non |
> | featureProviderNamespaces | Non |
> | featureProviders | Non |
> | features | Non |
> | fournisseurs | Non |
> | subscriptionFeatureRegistrations | Non |

## <a name="microsoftfidalgo"></a>Microsoft.Fidalgo

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | devcenters | Non |
> | devcenters / catalogs | Non |
> | devcenters / catalogs / items | Non |
> | devcenters / environmentTypes | Non |
> | devcenters / mappings | Non |
> | machinedefinitions | Non |
> | networksettings | Non |
> | projects | Non |
> | projects / catalogItems | Non |
> | projects / environments | Non |
> | projects / environments / deployments | Non |
> | projects / environmentTypes | Non |
> | projects / pools | Non |

## <a name="microsoftfluidrelay"></a>Microsoft.FluidRelay

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | fluidRelayServers | Non |
> | fluidRelayServers / fluidRelayContainers | Non |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | enroll | Non |
> | galleryitems | Non |
> | generateartifactaccessuri | Non |
> | myareas | Non |
> | myareas / areas | Non |
> | myareas / areas / areas | Non |
> | myareas / areas / areas / galleryitems | Non |
> | myareas / areas / galleryitems | Non |
> | myareas / galleryitems | Non |
> | inscription | Non |
> | les ressources | Non |
> | retrieveresourcesbyid | Non |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |

## <a name="microsoftgraph"></a>Microsoft.Graph

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | AzureAdApplication | Non |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | autoManagedAccounts | Oui |
> | autoManagedVmConfigurationProfiles | Oui |
> | configurationProfileAssignments | Non |
> | guestConfigurationAssignments | Non |
> | software | Non |
> | softwareUpdateProfile | Non |
> | softwareUpdates | Non |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | hanaInstances | Oui |
> | sapMonitors | Oui |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | dedicatedHSMs | Oui |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusterPools | Oui |
> | clusterPools / clusters | Oui |
> | clusterPools / clusters / instanceViews | Non |
> | clusterPools / clusters / serviceConfigs | Non |
> | clusterPools / clusters / sessionClusters | Oui |
> | clusterPools / clusters / sessionClusters / instanceViews | Non |
> | clusterPools / clusters / sessionClusters / serviceConfigs | Non |
> | clusters | Oui |
> | clusters / applications | Non |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | healthBots | Non |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | services | Oui |
> | services / iomtconnectors | Non |
> | services / iomtconnectors / connections | Non |
> | services / iomtconnectors / mappings | Non |
> | services / privateEndpointConnectionProxies | Non |
> | services / privateEndpointConnections | Non |
> | services / privateLinkResources | Non |
> | workspaces | Oui |
> | workspaces / dicomservices | Oui |
> | workspaces / eventGridFilters | Non |
> | workspaces / fhirservices | Oui |
> | workspaces / iotconnectors | Oui |
> | workspaces / iotconnectors / destinations | Non |
> | workspaces / iotconnectors / fhirdestinations | Non |
> | workspaces / privateEndpointConnectionProxies | Non |
> | workspaces / privateEndpointConnections | Non |
> | workspaces / privateLinkResources | Non |

## <a name="microsofthpcworkbench"></a>Microsoft.HpcWorkbench

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | instances | Non |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | machines | Oui |
> | machines / assessPatches | Non |
> | machines / extensions | Oui |
> | machines / installPatches | Non |
> | machines / privateLinkScopes | Non |
> | privateLinkScopes | Oui |
> | privateLinkScopes / privateEndpointConnectionProxies | Non |
> | privateLinkScopes / privateEndpointConnections | Non |

## <a name="microsofthybridconnectivity"></a>Microsoft.HybridConnectivity

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | points de terminaison | Non |

## <a name="microsofthybridcontainerservice"></a>Microsoft.HybridContainerService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | provisionedClusters | Non |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | dataManagers | Oui |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | périphériques | Non |
> | networkFunctions | Non |
> | networkFunctionVendors | Non |
> | registeredSubscriptions | Non |
> | vendors | Non |
> | vendors / vendorSkus | Non |
> | vendors / vendorskus / previewSubscriptions | Non |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | components | Oui |
> | networkScopes | Oui |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | jobs | Oui |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | diagnosticSettings | Non |
> | diagnosticSettingsCategories | Non |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | appTemplates | Non |
> | IoTApps | Oui |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | alertTypes | Non |
> | defenderSettings | Non |
> | onPremiseSensors | Non |
> | recommendationTypes | Non |
> | sensors | Non |
> | sites | Non |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Graph | Oui |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | deletedManagedHSMs | Non |
> | deletedVaults | Non |
> | hsmPools | Oui |
> | managedHSMs | Oui |
> | vaults | Oui |
> | vaults / accessPolicies | Non |
> | vaults / eventGridFilters | Non |
> | vaults / keys | Non |
> | vaults / keys / versions | Non |
> | vaults / secrets | Non |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | connectedClusters | Non |
> | registeredSubscriptions | Non |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | extensions | Non |
> | fluxConfigurations | Non |
> | sourceControlConfigurations | Non |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | Oui |
> | clusters / attacheddatabaseconfigurations | Non |
> | clusters / databases | Non |
> | clusters / databases / dataconnections | Non |
> | clusters / databases / eventhubconnections | Non |
> | clusters / databases / principalassignments | Non |
> | clusters / databases / scripts | Non |
> | clusters / dataconnections | Non |
> | clusters / principalassignments | Non |
> | clusters / sharedidentities | Non |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | labaccounts | Oui |
> | labplans | Oui |
> | labs | Oui |
> | users | Non |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | hostingEnvironments | Oui |
> | integrationAccounts | Oui |
> | integrationServiceEnvironments | Oui |
> | integrationServiceEnvironments / managedApis | Oui |
> | isolatedEnvironments | Oui |
> | workflows | Oui |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | commitmentPlans | Oui |
> | webServices | Oui |
> | Workspaces | Oui |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | aisysteminventories | Oui |
> | virtualclusters | Oui |
> | workspaces | Oui |
> | workspaces / batchEndpoints | Oui |
> | workspaces / batchEndpoints / deployments | Oui |
> | workspaces / batchEndpoints / deployments / jobs | Non |
> | workspaces / batchEndpoints / jobs | Non |
> | workspaces / codes | Non |
> | workspaces / codes / versions | Non |
> | workspaces / components | Non |
> | workspaces / components / versions | Non |
> | workspaces / computes | Non |
> | workspaces / data | Non |
> | workspaces / datasets | Non |
> | workspaces / datastores | Non |
> | workspaces / environments | Non |
> | workspaces / eventGridFilters | Non |
> | workspaces / jobs | Non |
> | workspaces / labelingJobs | Non |
> | workspaces / linkedServices | Non |
> | workspaces / models | Non |
> | workspaces / models / versions | Non |
> | workspaces / onlineEndpoints | Oui |
> | workspaces / onlineEndpoints / deployments | Oui |

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applyUpdates | Non |
> | configurationAssignments | Non |
> | maintenanceConfigurations | Oui |
> | publicMaintenanceConfigurations | Non |
> | updates | Non |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Identities | Non |
> | userAssignedIdentities | Oui |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Non |
> | registrationAssignments | Non |
> | registrationDefinitions | Non |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | getEntities | Non |
> | managementGroups | Non |
> | managementGroups / settings | Non |
> | les ressources | Non |
> | startTenantBackfill | Non |
> | tenantBackfillStatus | Non |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |
> | accounts / creators | Oui |
> | accounts / eventGridFilters | Non |
> | accounts / privateAtlases | Oui |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | macc | Non |
> | offers | Non |
> | offerTypes | Non |
> | offerTypes / publishers | Non |
> | offerTypes / publishers / offers | Non |
> | offerTypes / publishers / offers / plans | Non |
> | offerTypes / publishers / offers / plans / agreements | Non |
> | offerTypes / publishers / offers / plans / configs | Non |
> | offerTypes / publishers / offers / plans / configs / importImage | Non |
> | privategalleryitems | Non |
> | privateStoreClient | Non |
> | privateStores | Non |
> | privateStores / AdminRequestApprovals | Non |
> | privateStores / billingAccounts | Non |
> | privateStores / bulkCollectionsAction | Non |
> | privateStores / collections | Non |
> | privateStores / collections / offers | Non |
> | privateStores / collections / transferOffers | Non |
> | privateStores / collectionsToSubscriptionsMapping | Non |
> | privateStores / offers | Non |
> | privateStores / offers / acknowledgeNotification | Non |
> | privateStores / queryApprovedPlans | Non |
> | privateStores / queryNotificationsState | Non |
> | privateStores / queryOffers | Non |
> | privateStores / RequestApprovals | Non |
> | privateStores / requestApprovals / query | Non |
> | privateStores / requestApprovals / withdrawPlan | Non |
> | products | Non |
> | publishers | Non |
> | publishers / offers | Non |
> | publishers / offers / amendments | Non |
> | inscription | Non |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | classicDevServices | Oui |
> | updateCommunicationPreference | Non |

## <a name="microsoftmarketplacenotifications"></a>Microsoft.MarketplaceNotifications

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | reviewsnotifications | Non |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | agreements | Non |
> | offertypes | Non |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | mediaservices | Oui |
> | mediaservices / accountFilters | Non |
> | mediaservices / assets | Non |
> | mediaservices / assets / assetFilters | Non |
> | mediaservices / contentKeyPolicies | Non |
> | mediaservices / eventGridFilters | Non |
> | mediaservices / graphInstances | Non |
> | mediaservices / graphTopologies | Non |
> | mediaservices / liveEventOperations | Non |
> | mediaservices / liveEvents | Oui |
> | mediaservices / liveEvents / liveOutputs | Non |
> | mediaservices / liveOutputOperations | Non |
> | mediaservices / mediaGraphs | Non |
> | mediaservices / privateEndpointConnectionOperations | Non |
> | mediaservices / privateEndpointConnectionProxies | Non |
> | mediaservices / privateEndpointConnections | Non |
> | mediaservices / streamingEndpointOperations | Non |
> | mediaservices / streamingEndpoints | Oui |
> | mediaservices / streamingLocators | Non |
> | mediaservices / streamingPolicies | Non |
> | mediaservices / transforms | Non |
> | mediaservices / transforms / jobs | Non |
> | videoAnalyzers | Oui |
> | videoAnalyzers / accessPolicies | Non |
> | videoAnalyzers / edgeModules | Non |
> | videoAnalyzers / livePipelines | Non |
> | videoAnalyzers / pipelineJobs | Non |
> | videoAnalyzers / pipelineTopologies | Non |
> | videoAnalyzers / videos | Non |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | assessmentProjects | Oui |
> | migrateprojects | Oui |
> | moveCollections | Oui |
> | projects | Oui |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Oui |
> | objectAnchorsAccounts | Oui |
> | objectUnderstandingAccounts | Oui |
> | remoteRenderingAccounts | Oui |
> | spatialAnchorsAccounts | Oui |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | mobileNetworks | Non |
> | mobileNetworks / dataNetworks | Non |
> | mobileNetworks / services | Non |
> | mobileNetworks / simPolicies | Non |
> | mobileNetworks / sites | Non |
> | mobileNetworks / slices | Non |
> | networks | Non |
> | networks / sites | Non |
> | packetCoreControlPlanes | Non |
> | packetCoreControlPlanes / packetCoreDataPlanes | Non |
> | packetCoreControlPlanes / packetCoreDataPlanes / attachedDataNetworks | Non |
> | packetCores | Non |
> | sims | Non |
> | sims / simProfiles | Non |

## <a name="microsoftmonitor"></a>Microsoft.Monitor

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | netAppAccounts | Oui |
> | netAppAccounts / accountBackups | Non |
> | netAppAccounts / capacityPools | Oui |
> | netAppAccounts / capacityPools / volumes | Oui |
> | netAppAccounts / capacityPools / volumes / snapshots | Non |
> | netAppAccounts / capacityPools / volumes / subvolumes | Non |
> | netAppAccounts / snapshotPolicies | Oui |
> | netAppAccounts / volumeGroups | Non |

## <a name="microsoftnetworkfunction"></a>Microsoft.NetworkFunction

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | azureTrafficCollectors | Oui |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applicationGateways | Oui |
> | applicationGatewayWebApplicationFirewallPolicies | Oui |
> | applicationSecurityGroups | Oui |
> | azureFirewallFqdnTags | Non |
> | azureFirewalls | Oui |
> | bastionHosts | Oui |
> | bgpServiceCommunities | Non |
> | connections | Oui |
> | ddosCustomPolicies | Oui |
> | ddosProtectionPlans | Oui |
> | dnsOperationStatuses | Non |
> | dnszones | Oui |
> | dnszones / A | Non |
> | dnszones / AAAA | Non |
> | dnszones / all | Non |
> | dnszones / CAA | Non |
> | dnszones / CNAME | Non |
> | dnszones / MX | Non |
> | dnszones / NS | Non |
> | dnszones / PTR | Non |
> | dnszones / recordsets | Non |
> | dnszones / SOA | Non |
> | dnszones / SRV | Non |
> | dnszones / TXT | Non |
> | expressRouteCircuits | Oui |
> | expressRouteCrossConnections | Oui |
> | expressRouteGateways | Oui |
> | expressRoutePorts | Oui |
> | expressRouteServiceProviders | Non |
> | firewallPolicies | Oui |
> | frontdoors | Oui |
> | frontdoorWebApplicationFirewallManagedRuleSets | Non |
> | frontdoorWebApplicationFirewallPolicies | Oui |
> | getDnsResourceReference | Non |
> | internalNotify | Non |
> | ipGroups | Oui |
> | loadBalancers | Oui |
> | localNetworkGateways | Oui |
> | natGateways | Oui |
> | networkIntentPolicies | Oui |
> | networkInterfaces | Oui |
> | networkProfiles | Oui |
> | networkSecurityGroups | Oui |
> | networkWatchers | Oui |
> | networkWatchers / connectionMonitors | Oui |
> | networkWatchers / flowLogs | Oui |
> | networkWatchers / lenses | Oui |
> | networkWatchers / pingMeshes | Oui |
> | p2sVpnGateways | Oui |
> | privateDnsOperationStatuses | Non |
> | privateDnsZones | Oui |
> | privateDnsZones / A | Non |
> | privateDnsZones / AAAA | Non |
> | privateDnsZones / all | Non |
> | privateDnsZones / CNAME | Non |
> | privateDnsZones / MX | Non |
> | privateDnsZones / PTR | Non |
> | privateDnsZones / SOA | Non |
> | privateDnsZones / SRV | Non |
> | privateDnsZones / TXT | Non |
> | privateDnsZones / virtualNetworkLinks | Oui |
> | privateEndpoints | Oui |
> | privateLinkServices | Oui |
> | publicIPAddresses | Oui |
> | publicIPPrefixes | Oui |
> | routeFilters | Oui |
> | routeTables | Oui |
> | serviceEndpointPolicies | Oui |
> | trafficManagerGeographicHierarchies | Non |
> | trafficmanagerprofiles | Oui |
> | trafficmanagerprofiles / heatMaps | Non |
> | trafficManagerUserMetricsKeys | Non |
> | virtualHubs | Oui |
> | virtualNetworkGateways | Oui |
> | virtualNetworks | Oui |
> | virtualnetworks / subnets | Non |
> | virtualNetworkTaps | Oui |
> | virtualWans | Oui |
> | vpnGateways | Oui |
> | vpnSites | Oui |
> | webApplicationFirewallPolicies | Oui |

## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | NotebookProxies | Non |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | espaces de noms | Oui |
> | namespaces / notificationHubs | Oui |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | osNamespaces | Non |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | HyperVSites | Oui |
> | ImportSites | Oui |
> | MasterSites | Oui |
> | ServerSites | Oui |
> | VMwareSites | Oui |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | Oui |
> | deletedWorkspaces | Non |
> | linkTargets | Non |
> | querypacks | Oui |
> | storageInsightConfigs | Non |
> | workspaces | Oui |
> | workspaces / dataExports | Non |
> | workspaces / dataSources | Non |
> | workspaces / linkedServices | Non |
> | workspaces / linkedStorageAccounts | Non |
> | workspaces / metadata | Non |
> | workspaces / query | Non |
> | workspaces / scopedPrivateLinkProxies | Non |
> | workspaces / storageInsightConfigs | Non |
> | workspaces / tables | Non |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | managementassociations | Non |
> | managementconfigurations | Oui |
> | solutions | Oui |
> | views | Oui |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | cdnPeeringPrefixes | Non |
> | legacyPeerings | Non |
> | lookingGlass | Non |
> | peerAsns | Non |
> | peerings | Oui |
> | peeringServiceCountries | Non |
> | peeringServiceProviders | Non |
> | peeringServices | Oui |

## <a name="microsoftplayfab"></a>Microsoft.PlayFab

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | PlayerAccountPools | Non |
> | Titres | Non |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | attestations | Non |
> | eventGridFilters | Non |
> | policyEvents | Non |
> | policyMetadata | Non |
> | policyStates | Non |
> | policyTrackedResources | Non |
> | remediations | Non |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | consoles | Non |
> | dashboards | Oui |
> | tenantconfigurations | Non |
> | userSettings | Non |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Oui |
> | tenants | Oui |
> | locataires / espaces de travail | Non |
> | workspaceCollections | Oui |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | autoScaleVCores | Oui |
> | capacities | Oui |
> | servers | Oui |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |
> | enterprisePolicies | Oui |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |
> | deletedAccounts | Non |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | providerRegistrations | Non |
> | providerRegistrations / customRollouts | Non |
> | providerRegistrations / defaultRollouts | Non |
> | providerRegistrations / resourceActions | Non |
> | providerRegistrations / resourceTypeRegistrations | Non |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |
> | deletedAccounts | Non |
> | getDefaultAccount | Non |
> | removeDefaultAccount | Non |
> | setDefaultAccount | Non |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | Workspaces | Non |

## <a name="microsoftquota"></a>Microsoft.Quota

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | quotaRequests | Non |
> | quotas | Non |
> | usages | Non |

## <a name="microsoftrecommendationsservice"></a>Microsoft.RecommendationsService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Non |
> | accounts / modeling | Non |
> | accounts / serviceEndpoints | Non |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | backupProtectedItems | Non |
> | vaults | Oui |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | OpenShiftClusters | Oui |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | espaces de noms | Oui |
> | namespaces / authorizationrules | Non |
> | namespaces / hybridconnections | Non |
> | namespaces / hybridconnections / authorizationrules | Non |
> | namespaces / privateEndpointConnections | Non |
> | namespaces / wcfrelays | Non |
> | namespaces / wcfrelays / authorizationrules | Non |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | appliances | Oui |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | queries | Oui |
> | resourceChangeDetails | Non |
> | resourceChanges | Non |
> | les ressources | Non |
> | resourcesHistory | Non |
> | subscriptionsStatus | Non |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | availabilityStatuses | Non |
> | childAvailabilityStatuses | Non |
> | childResources | Non |
> | emergingissues | Non |
> | événements | Non |
> | impactedResources | Non |
> | metadata | Non |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | deployments | Non |
> | deployments / operations | Non |
> | deploymentScripts | Oui |
> | deploymentScripts / logs | Non |
> | deploymentStacks | Non |
> | deploymentStacks / snapshots | Non |
> | liens | Non |
> | fournisseurs | Non |
> | resourceGroups | Non |
> | subscriptions | Non |
> | templateSpecs | Oui |
> | templateSpecs / versions | Oui |
> | tenants | Non |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applications | Oui |
> | les ressources | Oui |
> | saasresources | Non |

## <a name="microsoftscom"></a>Microsoft.Scom

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | managedInstances | Non |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clouds | Non |
> | VirtualMachines | Non |
> | VirtualMachineTemplates | Non |
> | VirtualNetworks | Non |
> | vmmservers | Non |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | resourceHealthMetadata | Non |
> | searchServices | Oui |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Non |
> | advancedThreatProtectionSettings | Non |
> | alertes | Non |
> | alertsSuppressionRules | Non |
> | allowedConnections | Non |
> | antiMalwareSettings | Non |
> | applicationWhitelistings | Non |
> | assessmentMetadata | Non |
> | assessments | Non |
> | attributions | Oui |
> | autoDismissAlertsRules | Non |
> | automations | Oui |
> | AutoProvisioningSettings | Non |
> | Compliances | Non |
> | connectedContainerRegistries | Non |
> | connecteurs | Non |
> | customAssessmentAutomations | Oui |
> | customEntityStoreAssignments | Oui |
> | dataCollectionAgents | Non |
> | deviceSecurityGroups | Non |
> | discoveredSecuritySolutions | Non |
> | externalSecuritySolutions | Non |
> | InformationProtectionPolicies | Non |
> | ingestionSettings | Non |
> | insights | Non |
> | iotSecuritySolutions | Oui |
> | iotSecuritySolutions / analyticsModels | Non |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Non |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Non |
> | iotSecuritySolutions / iotAlerts | Non |
> | iotSecuritySolutions / iotAlertTypes | Non |
> | iotSecuritySolutions / iotRecommendations | Non |
> | iotSecuritySolutions / iotRecommendationTypes | Non |
> | jitNetworkAccessPolicies | Non |
> | jitPolicies | Non |
> | stratégies | Non |
> | pricings | Non |
> | regulatoryComplianceStandards | Non |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Non |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Non |
> | secureScoreControlDefinitions | Non |
> | secureScoreControls | Non |
> | secureScores | Non |
> | secureScores / secureScoreControls | Non |
> | securityConnectors | Oui |
> | securityContacts | Non |
> | securitySolutions | Non |
> | securitySolutionsReferenceData | Non |
> | securityStatuses | Non |
> | securityStatusesSummaries | Non |
> | serverVulnerabilityAssessments | Non |
> | paramètres | Non |
> | sqlVulnerabilityAssessments | Non |
> | standards | Oui |
> | subAssessments | Non |
> | tâches | Non |
> | topologies | Non |
> | workspaceSettings | Non |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | diagnosticSettings | Non |
> | diagnosticSettingsCategories | Non |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | aggregations | Non |
> | alertRules | Non |
> | alertRuleTemplates | Non |
> | automationRules | Non |
> | bookmarks | Non |
> | cas | Non |
> | dataConnectors | Non |
> | dataConnectorsCheckRequirements | Non |
> | Enrichissement | Non |
> | entities | Non |
> | entityQueries | Non |
> | entityQueryTemplates | Non |
> | incidents | Non |
> | metadata | Non |
> | officeConsents | Non |
> | onboardingStates | Non |
> | paramètres | Non |
> | sourceControls | Non |
> | threatIntelligence | Non |
> | watchlists | Non |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | consoleServices | Non |
> | serialPorts | Non |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | espaces de noms | Oui |
> | namespaces / authorizationrules | Non |
> | namespaces / disasterrecoveryconfigs | Non |
> | namespaces / eventgridfilters | Non |
> | namespaces / networkrulesets | Non |
> | namespaces / privateEndpointConnections | Non |
> | namespaces / queues | Non |
> | namespaces / queues / authorizationrules | Non |
> | namespaces / topics | Non |
> | namespaces / topics / authorizationrules | Non |
> | namespaces / topics / subscriptions | Non |
> | namespaces / topics / subscriptions / rules | Non |
> | premiumMessagingRegions | Non |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applications | Oui |
> | clusters | Oui |
> | clusters / applications | Non |
> | containerGroups | Oui |
> | containerGroupSets | Oui |
> | edgeclusters | Oui |
> | edgeclusters / applications | Non |
> | managedclusters | Oui |
> | managedclusters / applications | Non |
> | managedclusters / applications / services | Non |
> | managedclusters / applicationTypes | Non |
> | managedclusters / applicationTypes / versions | Non |
> | managedclusters / nodetypes | Non |
> | networks | Oui |
> | secretstores | Oui |
> | secretstores / certificates | Non |
> | secretstores / secrets | Non |
> | volumes | Oui |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applications | Oui |
> | containerGroups | Oui |
> | gateways | Oui |
> | networks | Oui |
> | secrets | Oui |
> | volumes | Oui |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | linkers | Non |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | providerRegistrations | Non |
> | providerRegistrations / resourceTypeRegistrations | Non |
> | rollouts | Oui |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | SignalR | Oui |
> | SignalR / eventGridFilters | Non |
> | WebPubSub | Oui |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |
> | accounts / accountQuotaPolicies | Non |
> | accounts / groupPolicies | Non |
> | accounts / jobs | Non |
> | accounts / models | Non |
> | accounts / storageContainers | Non |
> | images | Non |
> | quotas | Non |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | hybridUseBenefits | Non |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | applicationDefinitions | Oui |
> | applications | Oui |
> | jitRequests | Oui |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | managedInstances | Oui |
> | managedInstances / databases | Oui |
> | managedInstances / databases / backupShortTermRetentionPolicies | Non |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Non |
> | managedInstances / databases / vulnerabilityAssessments | Non |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Non |
> | managedInstances / encryptionProtector | Non |
> | managedInstances / keys | Non |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Non |
> | managedInstances / vulnerabilityAssessments | Non |
> | servers | Oui |
> | servers / administrators | Non |
> | servers / communicationLinks | Non |
> | servers / databases | Oui |
> | servers / encryptionProtector | Non |
> | servers / firewallRules | Non |
> | servers / keys | Non |
> | servers / restorableDroppedDatabases | Non |
> | servers / serviceobjectives | Non |
> | servers / tdeCertificates | Non |
> | virtualClusters | Non |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Oui |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Non |
> | SqlVirtualMachines | Oui |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | deletedAccounts | Non |
> | storageAccounts | Oui |
> | storageAccounts / blobServices | Non |
> | storageAccounts / encryptionScopes | Non |
> | storageAccounts / fileServices | Non |
> | storageAccounts / queueServices | Non |
> | storageAccounts / services | Non |
> | storageAccounts / services / metricDefinitions | Non |
> | storageAccounts / tableServices | Non |
> | usages | Non |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | amlFilesystems | Oui |
> | caches | Oui |
> | caches / storageTargets | Non |
> | usageModels | Non |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | replicationGroups | Non |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | storageSyncServices | Oui |
> | storageSyncServices / registeredServers | Non |
> | storageSyncServices / syncGroups | Non |
> | storageSyncServices / syncGroups / cloudEndpoints | Non |
> | storageSyncServices / syncGroups / serverEndpoints | Non |
> | storageSyncServices / workflows | Non |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | managers | Oui |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | clusters | Oui |
> | clusters / privateEndpoints | Non |
> | streamingjobs | Oui |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | acceptChangeTenant | Non |
> | acceptOwnership | Non |
> | acceptOwnershipStatus | Non |
> | aliases | Non |
> | annuler | Non |
> | changeTenantRequest | Non |
> | changeTenantStatus | Non |
> | CreateSubscription | Non |
> | enable | Non |
> | stratégies | Non |
> | renommer | Non |
> | SubscriptionDefinitions | Non |
> | SubscriptionOperations | Non |
> | subscriptions | Non |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | kustoOperations | Non |
> | privateLinkHubs | Oui |
> | workspaces | Oui |
> | workspaces / bigDataPools | Oui |
> | workspaces / kustoPools | Oui |
> | workspaces / kustoPools / attacheddatabaseconfigurations | Non |
> | workspaces / kustoPools / databases | Non |
> | workspaces / kustoPools / databases / dataconnections | Non |
> | workspaces / operationStatuses | Non |
> | workspaces / sqlDatabases | Oui |
> | workspaces / sqlPools | Oui |

## <a name="microsofttestbase"></a>Microsoft.TestBase

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | testBaseAccounts | Non |
> | testBaseAccounts / customerEvents | Non |
> | testBaseAccounts / emailEvents | Non |
> | testBaseAccounts / flightingRings | Non |
> | testBaseAccounts / packages | Non |
> | testBaseAccounts / packages / favoriteProcesses | Non |
> | testBaseAccounts / packages / osUpdates | Non |
> | testBaseAccounts / testSummaries | Non |
> | testBaseAccounts / testTypes | Non |
> | testBaseAccounts / usages | Non |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | environments | Oui |
> | environments / accessPolicies | Non |
> | environments / eventsources | Oui |
> | environments / privateEndpointConnectionProxies | Non |
> | environments / privateEndpointConnections | Non |
> | environments / privateLinkResources | Non |
> | environments / referenceDataSets | Oui |

## <a name="microsoftvideoindexer"></a>Microsoft.VideoIndexer

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Non |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | imageTemplates | Oui |
> | imageTemplates / runOutputs | Non |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | arczones | Non |
> | resourcepools | Non |
> | vcenters | Non |
> | VCenters / InventoryItems | Non |
> | virtualmachines | Non |
> | virtualmachinetemplates | Non |
> | virtualnetworks | Non |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Oui |
> | dedicatedCloudServices | Oui |
> | virtualMachines | Oui |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | accounts | Oui |
> | plans | Oui |
> | registeredSubscriptions | Non |
## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | apiManagementAccounts | Non |
> | apiManagementAccounts / apiAcls | Non |
> | apiManagementAccounts / apis | Non |
> | apiManagementAccounts / apis / apiAcls | Non |
> | apiManagementAccounts / apis / connectionAcls | Non |
> | apiManagementAccounts / apis / connections | Non |
> | apiManagementAccounts / apis / connections / connectionAcls | Non |
> | apiManagementAccounts / apis / localizedDefinitions | Non |
> | apiManagementAccounts / connectionAcls | Non |
> | apiManagementAccounts / connections | Non |
> | billingMeters | Non |
> | certificates | Oui |
> | connectionGateways | Oui |
> | connections | Oui |
> | customApis | Oui |
> | deletedSites | Non |
> | functionAppStacks | Non |
> | generateGithubAccessTokenForAppserviceCLI | Non |
> | hostingEnvironments | Oui |
> | hostingEnvironments / eventGridFilters | Non |
> | hostingEnvironments / multiRolePools | Non |
> | hostingEnvironments / workerPools | Non |
> | kubeEnvironments | Oui |
> | publishingUsers | Non |
> | de films | Non |
> | resourceHealthMetadata | Non |
> | runtimes | Non |
> | serverFarms | Oui |
> | serverFarms / eventGridFilters | Non |
> | serverFarms / firstPartyApps | Non |
> | serverFarms / firstPartyApps / keyVaultSettings | Non |
> | sites | Oui |
> | sites/config  | Non |
> | sites / eventGridFilters | Non |
> | sites / hostNameBindings | Non |
> | sites / networkConfig | Non |
> | sites / premieraddons | Oui |
> | sites / slots | Oui |
> | sites / slots / eventGridFilters | Non |
> | sites / slots / hostNameBindings | Non |
> | sites / slots / networkConfig | Non |
> | sourceControls | Non |
> | staticSites | Oui |
> | validate | Non |
> | verifyHostingEnvironmentVnet | Non |
> | webAppStacks | Non |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | diagnosticSettings | Non |
> | diagnosticSettingsCategories | Non |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | multipleActivationKeys | Oui |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | DeviceServices | Oui |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | migrationAgents | Non |
> | workloads | Non |
> | workloads / instances | Non |
> | workloads / versions | Non |
> | workloads / versions / artifacts | Non |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Type de ressource | Effectuer la suppression du mode |
> | ------------- | ----------- |
> | monitors | Non |

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir les mêmes données qu’un fichier de valeurs séparées par des virgules, téléchargez [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).