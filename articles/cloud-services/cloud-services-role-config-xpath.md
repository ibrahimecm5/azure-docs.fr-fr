---
title: Aide-mémoire XPath de configuration d’un rôle Services cloud (classique) | Microsoft Docs
description: Les différents paramètres XPath que vous pouvez utiliser dans la configuration d’un rôle de service cloud pour exposer les paramètres sous la forme d'une variable d'environnement.
ms.topic: article
ms.service: cloud-services
ms.subservice: deployment-files
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 56dcf798ccaf96b316d978fa5d8a70f4cd99ae6d
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823929"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Exposer les paramètres de configuration de rôle comme variable d'environnement avec XPath

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Dans le fichier de définition de service du rôle Web ou du rôle de travail du service cloud, vous pouvez exposer les valeurs de configuration de l'exécution en tant que variables d'environnement. Les valeurs XPath suivantes sont prises en charge (qui correspondent aux valeurs de l'API).

Ces valeurs XPath sont également disponibles via la bibliothèque [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee773173(v=azure.100)) . 

## <a name="app-running-in-emulator"></a>Application qui s'exécute dans l'émulateur
Indique que l'application s'exécute dans l'émulateur.

| Type | Exemple |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| Code |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>ID de déploiement
Récupère l'ID de déploiement de l'instance.

| Type | Exemple |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| Code |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>ID de rôle
Récupère l'ID de rôle actuel de l'instance.

| Type | Exemple |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| Code |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Mettre à jour le domaine
Récupère le domaine de mise à jour de l'instance.

| Type | Exemple |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Code |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Domaine d'erreur
Récupère le domaine d’erreur de l'instance.

| Type | Exemple |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Code |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Nom de rôle
Récupère le nom de rôle des instances.

| Type | Exemple |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Code |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Paramètre de configuration
Récupère la valeur du paramètre de configuration spécifié.

| Type | Exemple |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Code |var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Chemin de stockage local
Récupère le chemin de stockage local de l'instance.

| Type | Exemple |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Code |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |

## <a name="local-storage-size"></a>Taille du stockage local
Récupère la taille du stockage local de l'instance.

| Type | Exemple |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Code |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protocole du point de terminaison
Récupère le protocole du point de terminaison de l'instance.

| Type | Exemple |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Code |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## <a name="endpoint-ip"></a>IP du point de terminaison
Récupère l'adresse IP du point de terminaison spécifié.

| Type | Exemple |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Code |var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## <a name="endpoint-port"></a>Port du point de terminaison
Récupère le port du point de terminaison de l'instance.

| Type | Exemple |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Code |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |

## <a name="example"></a>Exemple
Voici un exemple de rôle de travail qui crée une tâche de démarrage avec une variable d’environnement nommée `TestIsEmulated`, définie sur la [valeur xpath @emulated](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur le fichier [ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Créer un package [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) .

Activer le [Bureau à distance](cloud-services-role-enable-remote-desktop-new-portal.md) pour un rôle.




