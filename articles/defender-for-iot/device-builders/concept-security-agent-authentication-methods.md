---
title: Méthodes d'authentification de l’agent de sécurité
description: Découvrez les différentes méthodes d’authentification disponibles avec le service Defender pour IoT.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 1d5aa69b5a44b07f90b063f741e65236f74e7dbd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331563"
---
# <a name="security-agent-authentication-methods"></a>Méthodes d'authentification de l’agent de sécurité

Cet article décrit les différentes méthodes d’authentification utilisables avec l’agent AzureIoTSecurity pour s’authentifier auprès d’IoT Hub.

Un Defender-IoT-micro-agent est nécessaire pour chaque appareil intégré à Defender pour IoT dans IoT Hub. Pour authentifier l’appareil, Defender pour IoT peut appliquer deux méthodes. Choisissez la plus adaptée à votre solution IoT actuelle.

- Option SecurityModule
- Option Appareil

## <a name="authentication-methods"></a>Méthodes d’authentification

Il existe deux méthodes permettant à l’agent AzureIoTSecurity de Defender pour IoT d’effectuer l’authentification :

- Mode d’authentification **Defender-IoT-micro-agent**<br>
L’agent est authentifié à l’aide de l’identité Defender-IoT-micro-agent indépendamment de l’identité de l’appareil.
Utilisez ce type d’authentification si vous souhaitez que l’agent de sécurité utilise une méthode d’authentification dédiée par le biais de Defender-IoT-micro-agent (clé symétrique uniquement).

- Mode d'authentification **Appareil**<br>
Dans cette méthode, l’agent de sécurité s’authentifie tout d’abord avec l’identité d’appareil. Après l’authentification initiale, l’agent Defender pour IoT effectue un appel **REST** à IoT Hub avec l’API REST et les données d’authentification de l’appareil. L’agent Defender pour IoT demande ensuite à IoT Hub la méthode d’authentification Defender-IoT-micro-agent et les données. Dans la dernière étape, l’agent Defender pour IoT effectue une authentification vis-à-vis du module Defender pour IoT.

Utilisez ce type d’authentification si vous souhaitez que l’agent de sécurité réutilise une méthode d’authentification d’appareil existante (certificat auto-signé ou clé symétrique).

Pour connaître la configuration, voir [Paramètres d’installation de l’agent de sécurité](#security-agent-installation-parameters).

## <a name="authentication-methods-known-limitations"></a>Limitations connues des méthodes d’authentification

- Le mode d’authentification **SecurityModule** prend uniquement en charge l’authentification par clé symétrique.
- Le certificat signé par l’autorité de certification n’est pas pris en charge par le mode d’authentification **Appareil**.

## <a name="security-agent-installation-parameters"></a>Paramètres d’installation de l’agent de sécurité

Lors du [déploiement d’un agent de sécurité](how-to-deploy-agent.md), les informations d’authentification doivent être indiquées comme arguments,
documentés dans le tableau suivant.

|Nom du paramètre Linux | Nom du paramètre Windows | Paramètre de raccourci |Description|Options|
|---------------------|---------------|---------|---------------|---------------|
|authentication-identity|AuthenticationIdentity|aui|Identité d’authentification| **SecurityModule** ou **Appareil**|
|authentication-method|AuthenticationMethod|aum|Méthode d'authentification|**SymmetricKey** ou **SelfSignedCertificate**|
|file-path|FilePath|f|Chemin d’accès complet absolu du fichier contenant le certificat ou la clé symétrique| |
|host-name|HostName|hn|FQDN du hub IoT|Exemple : ContosoIotHub.azure-devices.net|
|device-id|deviceId|di|ID de périphérique|Exemple : MyDevice1|
|certificate-location-kind|CertificateLocationKind|cI|Emplacement de stockage des certificats|**LocalFile** ou **Store**|
|

Si vous utilisez le script d’installation de l’agent de sécurité, la configuration suivante est effectuée automatiquement. Pour changer manuellement l’authentification de l’agent de sécurité, modifiez le fichier de configuration.

## <a name="change-authentication-method-after-deployment"></a>Changer de méthode d’authentification après le déploiement

Si l’agent de sécurité est déployé avec un script d’installation, un fichier de configuration est automatiquement créé.

Pour changer de méthode d’authentification après le déploiement, il faut modifier manuellement ce fichier.

### <a name="c-based-security-agent"></a>Agent de sécurité C#

Modifiez _Authentication.config_ avec les paramètres suivants :

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>Agent de sécurité C

Modifiez _LocalConfiguration.json_ avec les paramètres suivants :

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble des agents de sécurité](security-agent-architecture.md)
- [Déployer un agent de sécurité](how-to-deploy-agent.md)
- [Accéder aux données de sécurité brutes](how-to-security-data-access.md)
