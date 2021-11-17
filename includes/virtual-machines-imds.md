---
author: LittleBoxOfSunshine
manager: KumariSupriya
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: include
ms.date: 01/04/2021
ms.author: chhenk
ms.reviewer: azmetadatadev
ms.custom: references_regions
ms.openlocfilehash: c2d566e1ded80ed0d0d7ef33fb7218f77b39fe21
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132530083"
---
Azure Instance Metadata Service fournit des informations sur les instances de machine virtuelle en cours d’exécution. Vous pouvez l’utiliser pour gérer et configurer vos machines virtuelles.
Ces information comprennent la référence SKU, le stockage, les configurations réseau et les événements de maintenance à venir. Pour connaître la liste complète des données disponibles, consultez le [Résumé des catégories de points de terminaison](#endpoint-categories).

IMDS est disponible pour les instances de machine virtuelle en cours d’exécution et pour les instances de groupe de machines virtuelles identiques. Tous les points de terminaison prennent en charge les machines virtuelles créées et gérées avec [Azure Resource Manager](/rest/api/resources/). Seules la catégorie Attesté et la partie Réseau de la catégorie Instance prennent en charge les machines virtuelles créées avec le modèle de déploiement Classic. Le point de terminaison Attesté le fait seulement dans une certaine mesure.

IMDS est une API REST disponible à une adresse IP connue et non routable (`169.254.169.254`). On ne peut y accéder qu’à partir de la machine virtuelle. La communication entre la machine virtuelle et IMDS ne quitte jamais l’hôte.
Faites en sorte que vos clients HTTP contournent les proxys web au sein de la machine virtuelle lors de l’interrogation d’IMDS et traitent `169.254.169.254` de la même façon que [`168.63.129.16`](../articles/virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="usage"></a>Usage

### <a name="access-azure-instance-metadata-service"></a>Accéder à Azure Instance Metadata Service

Pour accéder à IMDS, créez une machine virtuelle à partir d’[Azure Resource Manager](/rest/api/resources/) ou du [portail Azure](https://portal.azure.com), puis utilisez les exemples suivants.
Pour obtenir plus d’exemples, consultez [Exemples Azure Instance Metadata](https://github.com/microsoft/azureimds).

Voici un exemple de code permettant de récupérer toutes les métadonnées d’une instance. Pour obtenir une vue d’ensemble de toutes les fonctionnalités disponibles dans le but d’accéder à une source de données spécifique, consultez [Catégories de points de terminaison](#endpoint-categories).

**Requête**

> [!IMPORTANT]
> Dans cet exemple, les proxys sont contournés. Vous **devez** contourner les proxys lorsque vous interrogez IMDS. Pour plus d’informations, consultez [Proxys](#proxies).

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2021-02-01" | ConvertTo-Json -Depth 64
```

`-NoProxy` nécessite PowerShell V6 ou version ultérieure. Consultez notre [référentiel d’échantillons](https://github.com/microsoft/azureimds) pour obtenir des exemples de versions PowerShell plus anciennes.

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2021-02-01" | jq
```

---

**Réponse**

> [!NOTE]
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

## <a name="security-and-authentication"></a>Sécurité et authentification

Instance Metadata Service n’est accessible qu’à partir d’une instance de machine virtuelle en cours d’exécution sur une adresse IP non routable. Les interactions des machines virtuelles sont limitées aux métadonnées/fonctionnalités qui se rapportent à elles-mêmes. L’API est HTTP uniquement. Elle ne quitte jamais l’hôte.

Les demandes doivent respecter les conditions suivantes pour être directement destinées à IMDS et éviter toute redirection involontaire ou indésirable :
- Elles **doivent** contenir l’en-tête `Metadata: true`.
- Elles ne doivent **pas** contenir l’en-tête `X-Forwarded-For`.

Toutes les demandes qui ne respectent pas **ces deux** exigences sont rejetées par le service.

> [!IMPORTANT]
> IMDS n’est **pas** un canal pour les données sensibles. L’API n’est pas authentifiée. Elle est ouverte à tous les processus de la machine virtuelle. Les informations exposées par le biais de ce service doivent être considérées comme des informations partagées pour toutes les applications exécutées dans la machine virtuelle.

S’il n’est pas nécessaire pour chaque processus sur la machine virtuelle d’accéder au point de terminaison IMDS, vous pouvez définir des règles de pare-feu local pour limiter l’accès. Par exemple, si seul un service système connu a besoin d’accéder au service de métadonnées d’instance, vous pouvez définir une règle de pare-feu sur le point de terminaison IMDS, en autorisant uniquement le ou les processus spécifiques auxquels accéder ou en refusant l’accès pour le reste des processus. 


## <a name="proxies"></a>Proxies

IMDS n’est **pas** destiné à être utilisé derrière un proxy, ce qui n’est pas pris en charge. La plupart des clients HTTP proposent une option vous permettant de désactiver les proxys sur vos demandes. Cette fonctionnalité doit être utilisée lors de la communication avec IMDS. Pour plus d’informations, consultez la documentation de votre client.

> [!IMPORTANT]
> Même si vous n’avez connaissance d’aucune configuration de proxy dans votre environnement, **vous devez toujours remplacer les paramètres de proxy client par défaut**. Les configurations de proxy peuvent être découvertes automatiquement. Si vous ne les contournez pas, vous vous exposez à des risques de panne en cas de modification ultérieure de la configuration de la machine.

## <a name="rate-limiting"></a>Limitation du débit

En général, les demandes adressées à IMDS sont limitées à 5 par seconde. Celles qui dépassent ce seuil sont rejetées avec une réponse 429. Les demandes adressées à la catégorie [Identité managée](#managed-identity) sont limitées à 20 par seconde et à 5 simultanément.

## <a name="http-verbs"></a>Verbes HTTP

Sont actuellement pris en charge les verbes HTTP suivants :

| Verbe | Description |
|------|-------------|
| `GET` | Récupère la ressource demandée

## <a name="parameters"></a>Paramètres

Les points de terminaison peuvent prendre en charge les paramètres obligatoires et facultatifs. Pour plus d’informations, consultez [Schéma](#schema) et la documentation du point de terminaison en question.

### <a name="query-parameters"></a>Paramètres de requête

Les points de terminaison IMDS prennent en charge les paramètres de chaîne de requête HTTP. Exemple : 

```
http://169.254.169.254/metadata/instance/compute?api-version=2021-01-01&format=json
```

Spécifie les paramètres :

| Nom | Valeur |
|------|-------|
| `api-version` | `2021-01-01`
| `format` | `json`

Les demandes comportant des noms de paramètres de requête en double sont rejetées.

### <a name="route-parameters"></a>Paramètres d’itinéraire

Dans le cas de certains points de terminaison qui retournent des objets blob JSON volumineux, l’ajout de paramètres d’itinéraire au point de terminaison de la demande est pris en charge pour filtrer la réponse sur un sous-ensemble : 

```
http://169.254.169.254/metadata/<endpoint>/[<filter parameter>/...]?<query parameters>
```
Les paramètres correspondent aux index/clés qui seraient utilisés pour parcourir l’objet JSON si l’on interagissait avec une représentation analysée.

Par exemple, `/metatadata/instance` retourne l’objet JSON suivant :
```json
{
    "compute": { ... },
    "network": {
        "interface": [
            {
                "ipv4": {
                   "ipAddress": [{
                        "privateIpAddress": "10.144.133.132",
                        "publicIpAddress": ""
                    }],
                    "subnet": [{
                        "address": "10.144.133.128",
                        "prefix": "26"
                    }]
                },
                "ipv6": {
                    "ipAddress": [
                     ]
                },
                "macAddress": "0011AAFFBB22"
            },
            ...
        ]
    }
}
```

Pour filtrer la réponse sur la propriété compute, la demande envoyée serait la suivante : 
```
http://169.254.169.254/metadata/instance/compute?api-version=<version>
```

De même, pour appliquer un filtre sur une propriété imbriquée ou un élément de tableau spécifique, des clés seraient encore ajoutées : 
```
http://169.254.169.254/metadata/instance/network/interface/0?api-version=<version>
```
Cette demande appliquerait un filtre sur le premier élément de la propriété `Network.interface` et retournerait la réponse suivante :

```json
{
    "ipv4": {
       "ipAddress": [{
            "privateIpAddress": "10.144.133.132",
            "publicIpAddress": ""
        }],
        "subnet": [{
            "address": "10.144.133.128",
            "prefix": "26"
        }]
    },
    "ipv6": {
        "ipAddress": [
         ]
    },
    "macAddress": "0011AAFFBB22"
}
```

> [!NOTE]
> En cas de filtrage sur un nœud terminal, `format=json` ne fonctionne pas. Pour ces requêtes, `format=text` doit être spécifié explicitement si le format par défaut est json.

## <a name="schema"></a>schéma

### <a name="data-format"></a>Format de données

Par défaut, IMDS retourne les données au format JSON (`Content-Type: application/json`). Toutefois, les points de terminaison qui prennent en charge le filtrage de réponse (cf. [Paramètres d’itinéraire](#route-parameters)) prennent également en charge le format `text`.

Pour accéder à un format de réponse autre que le format par défaut, spécifiez le format demandé en tant que paramètre de chaîne de requête dans la demande. Par exemple :

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

---

Dans les réponses JSON, toutes les primitives sont de type `string`. Les valeurs manquantes et les valeurs non applicables sont toujours incluses, mais définies sur une chaîne vide.

### <a name="versioning"></a>Contrôle de version

IMDS est versionné. Il est obligatoire de spécifier la version de l’API dans la requête HTTP. La seule exception à cette exigence est le point de terminaison [versions](#versions), qui peut être utilisé pour récupérer dynamiquement les versions d’API disponibles.

Au fur et à mesure que des versions plus récentes sont ajoutées, les versions antérieures sont toujours accessibles pour des questions de compatibilité si vos scripts ont des dépendances sur des formats de données spécifiques.

Si vous ne spécifiez pas de version, vous recevez une erreur accompagnée de la liste des dernières versions prises en charge :
```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

#### <a name="supported-api-versions"></a>Versions d'API prises en charge
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01
- 2020-12-01
- 2021-01-01
- 2021-02-01
- 2021-03-01
- 2021-05-01

### <a name="swagger"></a>Fichier Swagger

Une définition Swagger complète pour IMDS est disponible à l’adresse https://github.com/Azure/azure-rest-api-specs/blob/main/specification/imds/data-plane/readme.md.

## <a name="regional-availability"></a>Disponibilité régionale

Le service est **en disponibilité générale** dans tous les clouds Azure.

## <a name="root-endpoint"></a>Point de terminaison racine

Le point de terminaison racine est `http://169.254.169.254/metadata`.

## <a name="endpoint-categories"></a>Catégories de points de terminaison

L’API IMDS contient plusieurs catégories de points de terminaison qui représentent des sources de données différentes, chacune contenant un ou plusieurs points de terminaison. Examinez chaque catégorie pour plus de détails.

| Racine de la catégorie | Description | Version introduite |
|---------------|-------------|--------------------|
| `/metadata/attested` | Voir [Données attestées](#attested-data) | 2018-10-01
| `/metadata/identity` | Voir [Identité managée via IMDS](#managed-identity) | 2018-02-01
| `/metadata/instance` | Voir [Métadonnées d’instance](#instance-metadata) | 2017-04-02
| `/metadata/loadbalancer` | Voir [Récupérer les métadonnées de l’équilibreur de charge à l’aide d’IMDS](#load-balancer-metadata) | 2020-10-01
| `/metadata/scheduledevents` | Voir [Scheduled Events via IMDS](#scheduled-events) | 2017-08-01
| `/metadata/versions` | Voir [Versions](#versions) | N/A

## <a name="versions"></a>Versions

> [!NOTE]
> Cette fonctionnalité a été publiée avec la version 2020-10-01, qui est actuellement en cours de déploiement et n’est peut-être pas encore disponible dans toutes les régions.

### <a name="list-api-versions"></a>Liste des versions d’API

Retourne l’ensemble des versions d’API prises en charge.

```
GET /metadata/versions
```

#### <a name="parameters"></a>Paramètres

Aucun (ce point de terminaison n’est pas versionné).

#### <a name="response"></a>response

```json
{
  "apiVersions": [
    "2017-03-01",
    "2017-04-02",
    ...
  ]
}
```

## <a name="instance-metadata"></a>Métadonnées d’instance

### <a name="get-vm-metadata"></a>Récupération des métadonnées de machine virtuelle

Expose les métadonnées importantes pour l’instance de machine virtuelle, notamment le calcul, le réseau et le stockage. 

```
GET /metadata/instance
```

#### <a name="parameters"></a>Paramètres

| Nom | Obligatoire ou facultatif | Description |
|------|-------------------|-------------|
| `api-version` | Obligatoire | Version utilisée pour traiter la demande.
| `format` | Facultatif* | Format (`json` ou `text`) de la réponse. *Remarque : Peut être obligatoire si les paramètres de demande sont utilisés.

Ce point de terminaison prend en charge le filtrage de la réponse avec les [paramètres de routage](#route-parameters).

#### <a name="response"></a>response

[!INCLUDE [virtual-machines-imds-full-instance-response](./virtual-machines-imds-full-instance-response.md)]

Décomposition du schéma :

**Calcul**

| Données | Description | Version introduite |
|------|-------------|--------------------|
| `azEnvironment` | Environnement Azure dans lequel s’exécute la machine virtuelle | 2018-10-01
| `customData` | Cette fonctionnalité est déconseillée et désactivée [dans IMDS](#frequently-asked-questions). Elle a été remplacée par `userData`. | 2019-02-01
| `evictionPolicy` | Définit le mode de suppression d’une [machine virtuelle spot](../articles/virtual-machines/spot-vms.md). | 2020-12-01
| `extendedLocation.type` | Type de l’emplacement étendu de la machine virtuelle. | 2021-03-01
| `extendedLocation.name` | Nom de l’emplacement étendu de la machine virtuelle | 2021-03-01
| `isHostCompatibilityLayerVm` | Indique si la machine virtuelle s’exécute sur la couche de compatibilité de l’ordinateur hôte | 2020-06-01
| `licenseType` | Type de licence pour [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). Présent seulement pour les machines virtuelles dotées d’AHB | 2020-09-01
| `location` | Région Azure dans laquelle la machine virtuelle est en cours d’exécution | 2017-04-02
| `name` | Nom de la machine virtuelle | 2017-04-02
| `offer` | Fournit des informations pour l’image de machine virtuelle et ne sont présentes que pour les images déployées à partir de la galerie d’images Azure | 2017-04-02
| `osProfile.adminUsername` | Spécifie le nom du compte administrateur | 2020-07-15
| `osProfile.computerName` | Spécifie le nom de l’ordinateur | 2020-07-15
| `osProfile.disablePasswordAuthentication` | Spécifie si l'authentification par mot de passe est désactivée. Présent seulement pour les machines virtuelles Linux | 2020-10-01
| `osType` | Linux ou Windows | 2017-04-02
| `placementGroupId` | [Groupe de placement](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) de votre groupe de machines virtuelles identiques | 2017-08-01
| `plan` | [Plan](/rest/api/compute/virtualmachines/createorupdate#plan) contenant le nom, le produit et l’éditeur d’une machine virtuelle s’il s’agit d’une image de la Place de marché Azure | 2018-04-02
| `platformUpdateDomain` |  [Domaine de mise à jour](../articles/virtual-machines/availability.md) dans lequel la machine virtuelle est en cours d’exécution | 2017-04-02
| `platformFaultDomain` | [Domaine par défaut](../articles/virtual-machines/availability.md) dans lequel la machine virtuelle est en cours d’exécution | 2017-04-02
| `priority` | Priorité de la machine virtuelle. Pour en savoir plus, consultez la section [Machines virtuelles spot](../articles/virtual-machines/spot-vms.md) | 2020-12-01
| `provider` | Fournisseur de la machine virtuelle | 2018-10-01
| `publicKeys` | [Collection de clés publiques](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) affectée à la machine virtuelle et aux chemins | 2018-04-02
| `publisher` | Éditeur de l’image de machine virtuelle | 2017-04-02
| `resourceGroupName` | [Groupe de ressources](../articles/azure-resource-manager/management/overview.md) de votre machine virtuelle | 2017-08-01
| `resourceId` | L’ID [complet](/rest/api/resources/resources/getbyid) de la ressource | 2019-03-11
| `sku` | Référence (SKU) spécifique pour l’image de machine virtuelle | 2017-04-02
| `securityProfile.secureBootEnabled` | Indique si le démarrage sécurisé UEFI est activé sur la machine virtuelle | 2020-06-01
| `securityProfile.virtualTpmEnabled` | Indique si le Module de plateforme sécurisée (TPM) virtuel est activé sur la machine virtuelle | 2020-06-01
| `storageProfile` | Voir Profil de stockage ci-dessous | 2019-06-01
| `subscriptionId` | Abonnement Azure pour la machine virtuelle | 2017-08-01
| `tags` | [Étiquettes](../articles/azure-resource-manager/management/tag-resources.md) de votre machine virtuelle  | 2017-08-01
| `tagsList` | Balises mises en forme en tant que tableau JSON pour faciliter l’analyse programmatique  | 2019-06-04
| `userData` | Jeu de données spécifié lors de la création de la machine virtuelle à utiliser pendant ou après l’approvisionnement (encodé en base64)  | 2021-01-01
| `version` | Version de l’image de machine virtuelle | 2017-04-02
| `virtualMachineScaleSet.id` | ID du [groupe de machines virtuelles identiques](../articles/virtual-machine-scale-sets/overview.md) dont fait partie la machine virtuelle (si applicable) | 2021-03-01
| `vmId` | [Identificateur unique](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) de la machine virtuelle | 2017-04-02
| `vmScaleSetName` | [Nom du groupe de machines virtuelles identiques](../articles/virtual-machine-scale-sets/overview.md) de votre groupe de machines virtuelles identiques | 2017-12-01
| `vmSize` | [Taille de la machine virtuelle](../articles/virtual-machines/sizes.md) | 2017-04-02
| `zone` | [Zone de disponibilité](../articles/availability-zones/az-overview.md) de votre machine virtuelle | 2017-12-01

**Profil de stockage**

Le profil de stockage d’une machine virtuelle est divisé en trois catégories : la référence de l’image, le disque de système d’exploitation et les disques de données, plus un objet supplémentaire pour le disque temporaire local.

L’objet de référence d’image contient les informations suivantes sur l’image du système d’exploitation :

| Données | Description |
|------|-------------|
| `id` | ID de ressource
| `offer` | Offre de la plateforme ou de l’image de la Place de marché
| `publisher` | Éditeur de l’image
| `sku` | Référence SKU de l’image
| `version` | Version de la plateforme ou de l’image de la Place de marché

L’objet de disque du système d’exploitation contient les informations suivantes sur le disque du système d’exploitation utilisé par la machine virtuelle :

| Données | Description |
|------|-------------|
| `caching` | Exigences de la mise en cache
| `createOption` | Informations sur la façon dont la machine virtuelle a été créée
| `diffDiskSettings` | Paramètres du disque éphémère
| `diskSizeGB` | Taille du disque en Go
| `image`   | Disque dur virtuel de l’image utilisateur source
| `managedDisk` | Paramètres des disques managés
| `name`    | Nom du disque
| `vhd`     | Disque dur virtuel
| `writeAcceleratorEnabled` | Indique si writeAccelerator est activé ou non sur le disque

Le tableau des disques de données contient la liste des disques de données attachés à la machine virtuelle. Chaque objet de disque de données contient les informations suivantes :

Données | Description | Version introduite |
|------|-----------|--------------------|
| `bytesPerSecondThrottle`* | Quota de lecture/écriture sur le disque en octets | 2021-05-01
| `caching` | Exigences de la mise en cache | 2019-06-01
| `createOption` | Informations sur la façon dont la machine virtuelle a été créée | 2019-06-01
| `diffDiskSettings` | Paramètres du disque éphémère | 2019-06-01
| `diskCapacityBytes`* | Taille du disque en octets | 2021-05-01
| `diskSizeGB` | Taille du disque en Go | 2019-06-01
| `encryptionSettings` | Paramètres de chiffrement du disque | 2019-06-01
| `image` | Disque dur virtuel de l’image utilisateur source | 2019-06-01
| `isSharedDisk`* | Indique si le disque est partagé entre les ressources | 2021-05-01
| `isUltraDisk` | Indique si le disque de données est un disque Ultra | 2021-05-01
| `lun`     | Numéro d’unité logique du disque | 2019-06-01
| `managedDisk` | Paramètres des disques managés | 2019-06-01
| `name` | Nom du disque | 2019-06-01
| `opsPerSecondThrottle`* | Quota de lecture/écriture sur le disque en E/S par seconde | 2021-05-01
| `osType` | Type de système d’exploitation inclus dans le disque | 2019-06-01
| `vhd` | Disque dur virtuel | 2019-06-01
| `writeAcceleratorEnabled` | Indique si writeAccelerator est activé ou non sur le disque | 2019-06-01

\* Ces champs sont remplis uniquement pour les ultra-disques. Il s’agit de chaînes vides à partir de disques non-ultra.

L’objet de disque de ressources contient la taille du [disque temporaire local](../articles/virtual-machines/managed-disks-overview.md#temporary-disk) attaché à la machine virtuelle, le cas échéant, en kilo-octets.
S’il n’existe [aucun disque temporaire local pour la machine virtuelle](../articles/virtual-machines/azure-vms-no-temp-disk.yml), cette valeur est 0. 

| Données | Description | Version introduite |
|------|-------------|--------------------|
| `resourceDisk.size` | Taille du disque temporaire local de la machine virtuelle (en ko) | 2021-02-01

**Réseau**

| Données | Description | Version introduite |
|------|-------------|--------------------|
| `ipv4.privateIpAddress` | Adresse IPv4 locale de la machine virtuelle | 2017-04-02
| `ipv4.publicIpAddress` | Adresse IPv4 publique de la machine virtuelle | 2017-04-02
| `subnet.address` | Adresse de sous-réseau de la machine virtuelle | 2017-04-02
| `subnet.prefix` | Préfixe de sous-réseau, par exemple 24 | 2017-04-02
| `ipv6.ipAddress` | Adresse IPv6 locale de la machine virtuelle | 2017-04-02
| `macAddress` | Adresse MAC de la machine virtuelle | 2017-04-02

### <a name="get-user-data"></a>Obtenir les données utilisateur

Lorsque vous créez une machine virtuelle, vous pouvez spécifier un jeu de données à utiliser pendant ou après l’approvisionnement de la machine virtuelle, puis le récupérer via IMDS. Vérifiez [ici](../articles/virtual-machines/user-data.md) l’expérience de données d’utilisateur de bout en bout. 

Pour configurer les données utilisateur, utilisez le modèle de démarrage rapide fourni [ici](https://aka.ms/ImdsUserDataArmTemplate). L’exemple ci-dessous montre comment récupérer ces données par le biais d’IMDS. Cette fonctionnalité est proposée avec la version `2021-01-01` et versions supérieures.

> [!NOTE]
> Avis de sécurité : IMDS est ouvert à toutes les applications sur la machine virtuelle ; les données sensibles ne doivent pas être placées dans les données utilisateur.


#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
$userData = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text"
[System.Text.Encoding]::UTF8.GetString([Convert]::FromBase64String($userData))
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/userData?api-version=2021-01-01&format=text" | base64 --decode
```

---


#### <a name="sample-1-tracking-vm-running-on-azure"></a>Exemple 1 : Suivi de la machine virtuelle s’exécutant sur Azure

En tant que fournisseur de service, vous aurez peut-être besoin de suivre le nombre de machines virtuelles s’exécutant sur votre logiciel ou de disposer d’agents effectuant le suivi de l’unicité de la machine virtuelle. Pour pouvoir obtenir un ID unique pour une machine virtuelle, utilisez le champ `vmId` du service de métadonnées d’instance.

**Requête**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

---

**Réponse**

```
5c08b38e-4d57-4c23-ac45-aca61037f084
```

#### <a name="sample-2-placement-of-different-data-replicas"></a>Exemple 2 : Positionnement de différents réplicas de données

Pour certains scénarios, le positionnement des différents réplicas de données est de première importance. Par exemple, pour le [positionnement du réplica HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) ou le positionnement des conteneurs via un [orchestrateur](https://kubernetes.io/docs/user-guide/node-selection/), vous devez connaître les domaines `platformFaultDomain` et `platformUpdateDomain` sur lesquels la machine virtuelle s’exécute.
Vous pouvez également utiliser les [Zones de disponibilité](../articles/availability-zones/az-overview.md) pour les instances pour prendre ces décisions.
Vous pouvez interroger ces données directement via IMDS.

**Requête**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

---

**Réponse**

```
0
```

#### <a name="sample-3-get-vm-tags"></a>Exemple 3 : Obtenir les balises de machines virtuelles

Les balises de machine virtuelle sont incluses dans l’API d’instance sous le point de terminaison instance/compute/tags.
Des balises peuvent être appliqués à votre machine virtuelle Azure pour les organiser de manière logique dans une taxonomie. Les balises affectées à une machine virtuelle peuvent être récupérées à l’aide de la requête ci-dessous.

**Requête**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2017-08-01&format=text"
```

---

**Réponse**

```
Department:IT;ReferenceNumber:123456;TestStatus:Pending
```

Le champ `tags` est une chaîne dont les étiquettes sont délimitées par des points-virgules. Cette sortie peut constituer un problème si des points-virgules sont utilisés dans les étiquettes proprement dites. Si un analyseur est écrit pour extraire les balises par programmation, vous devez vous appuyer sur le champ `tagsList`. Le champ `tagsList` est un tableau JSON sans délimiteur et, par conséquent plus facile à analyser. Le tagsList attribué à une machine virtuelle peut être récupéré à l’aide de la requête ci-dessous.

**Requête**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04" | jq
```

---

**Réponse**

#### <a name="windows"></a>[Windows](#tab/windows/)

```json
{
    "value":  [
                  {
                      "name":  "Department",
                      "value":  "IT"
                  },
                  {
                      "name":  "ReferenceNumber",
                      "value":  "123456"
                  },
                  {
                      "name":  "TestStatus",
                      "value":  "Pending"
                  }
              ],
    "Count":  3
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "ReferenceNumber",
    "value": "123456"
  },
  {
    "name": "TestStatus",
    "value": "Pending"
  }
]
```

---


#### <a name="sample-4-get-more-information-about-the-vm-during-support-case"></a>Exemple 4 : Obtenir plus d’informations sur la machine virtuelle dans le cadre du support technique

En tant que fournisseur de services, vous recevrez peut-être un appel du support pour lequel vous devrez connaître les informations de la machine virtuelle. Demandez au client de partager les métadonnées de calcul afin que l’équipe du support dispose des informations de base concernant le type de machine virtuelle sur Azure.

**Requête**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01" | ConvertTo-Json -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
```

---

**Réponse**

> [!NOTE]
> La réponse est une chaîne JSON. L’exemple de réponse suivant est imprimé avec soin par souci de lisibilité.

#### <a name="windows"></a>[Windows](#tab/windows/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "extendedLocation": {
      "type": "edgeZone",
      "name": "microsoftlosangeles"
    },
    "evictionPolicy": "",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "WindowsServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Windows",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "priority": "Regular",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "2019-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "bytesPerSecondThrottle": "979202048",
            "caching": "None",
            "createOption": "Empty",
            "diskCapacityBytes": "274877906944",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "isSharedDisk": "false",
            "isUltraDisk": "true",
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/MicrosoftCompute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "opsPerSecondThrottle": "65280",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "WindowsServer",
            "publisher": "MicrosoftWindowsServer",
            "sku": "2019-Datacenter",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Windows",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        },
        "resourceDisk": {
            "size": "4096"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "virtualMachineScaleSet": {
      "id": "/subscriptions/xxxxxxxx-xxxxx-xxx-xxx-xxxx/resourceGroups/resource-group-name/providers/Microsoft.Compute/virtualMachineScaleSets/virtual-machine-scale-set-name"
    },
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

#### <a name="linux"></a>[Linux](#tab/linux/)
```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "extendedLocation": {
      "type": "edgeZone",
      "name": "microsoftlosangeles"
    },
    "evictionPolicy": "",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "UbuntuServer",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "Linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "Priority": "Regular",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "Canonical",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "18.04-LTS",
    "storageProfile": {
        "dataDisks": [{
            "bytesPerSecondThrottle": "979202048",
            "caching": "None",
            "createOption": "Empty",
            "diskCapacityBytes": "274877906944",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "isSharedDisk": "false",
            "isUltraDisk": "true",
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "opsPerSecondThrottle": "65280",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        },
        "resourceDisk": {
            "size": "4096"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "virtualMachineScaleSet": {
      "id": "/subscriptions/xxxxxxxx-xxxxx-xxx-xxx-xxxx/resourceGroups/resource-group-name/providers/Microsoft.Compute/virtualMachineScaleSets/virtual-machine-scale-set-name"
    },
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

---

#### <a name="sample-5-get-the-azure-environment-where-the-vm-is-running"></a>Exemple 5 : Obtenir l’environnement Azure dans lequel s’exécute la machine virtuelle

Azure dispose de plusieurs clouds souverains comme [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Vous avez parfois besoin de l’environnement Azure pour prendre certaines décisions liées au runtime. L’exemple suivant vous montre comment obtenir ce comportement.

**Requête**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

---

**Réponse**

```
AzurePublicCloud
```

Le cloud et les valeurs de l’environnement Azure sont listés ici.

| Cloud | Environnement Azure |
|-------|-------------------|
| [Toutes les régions Azure mondiales en disponibilité générale](https://azure.microsoft.com/regions/) | AzurePublicCloud
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | AzureUSGovernmentCloud
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | AzureChinaCloud
| [Azure Allemagne](https://azure.microsoft.com/overview/clouds/germany/) | AzureGermanCloud


#### <a name="sample-6-retrieve-network-information"></a>Exemple 6 : Récupérer des informations sur le réseau

**Requête**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01" | ConvertTo-Json  -Depth 64
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

---

**Réponse**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}
```

#### <a name="sample-7-retrieve-public-ip-address"></a>Exemple 7 : Récupérer l’IP publique

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

---

## <a name="attested-data"></a>Données attestées

### <a name="get-attested-data"></a>Récupération de données attestées

IMDS permet de garantir que les données fournies proviennent d’Azure. Microsoft signe une partie de ces informations : vous pouvez donc vérifier qu’une image de la Place de marché Azure est celle que vous exécutez sur Azure.

```
GET /metadata/attested/document
```

#### <a name="parameters"></a>Paramètres

| Nom | Obligatoire ou facultatif | Description |
|------|-------------------|-------------|
| `api-version` | Obligatoire | Version utilisée pour traiter la demande.
| `nonce` | Facultatif | Chaîne à 10 chiffres servant de nonce de chiffrement. Si aucune valeur n’est fournie, IMDS utilise l’horodatage UTC actuel.

#### <a name="response"></a>response

```json
{
    "encoding":"pkcs7",
    "signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

> [!NOTE]
> En raison du mécanisme de mise en cache d’IMDS, une valeur nonce précédemment mise en cache peut être retournée.

L’objet blob de signature est une version signée [pkcs7](https://aka.ms/pkcs7) du document. Il contient le certificat utilisé pour la signature, ainsi que certains détails spécifiques de la machine virtuelle.

Pour les machines virtuelles créées avec Azure Resource Manager, le document comprend `vmId`, `sku`, `nonce`, `subscriptionId` et `timeStamp` pour la création et l’expiration du document, et les informations de plan de l’image. Les informations du plan ne sont fournies que pour les images de la Place de marché Azure.

Pour les machines virtuelles créées avec le modèle de déploiement Classic, seul le remplissage de `vmId` est garanti. Vous pouvez extraire le certificat de la réponse et l’utiliser pour vérifier que la réponse est valide et qu’elle provient d’Azure.

Le document décodé contient les champs suivants :

| Données | Description | Version introduite |
|------|-------------|--------------------|
| `licenseType` | Type de licence pour [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit). Présent seulement pour les machines virtuelles dotées d’AHB | 2020-09-01
| `nonce` | Chaîne qui peut être éventuellement fournie avec la requête. Si la valeur de `nonce` n’a pas été fournie, l’horodatage UTC actuel est utilisé. | 2018-10-01
| `plan` | [Plan d’image de la Place de marché Azure](/rest/api/compute/virtualmachines/createorupdate#plan). Contient l’ID de plan (name), l’image de produit ou l’offre (product) et l’ID d’éditeur (publisher). | 2018-10-01
| `timestamp.createdOn` | Horodatage UTC de création du document signé. | 2018-20-01
| `timestamp.expiresOn` | Horodatage UTC d’expiration du document signé. | 2018-10-01
| `vmId` | [Identificateur unique](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) de la machine virtuelle | 2018-10-01
| `subscriptionId` | Abonnement Azure pour la machine virtuelle | 2019-04-30
| `sku` | Référence SKU spécifique pour l’image de machine virtuelle (correspond à la propriété `compute/sku` du point de terminaison Instance Metadata \[`/metadata/instance`\]) | 2019-11-01

> [!NOTE]
> Pour les machines virtuelles Classic (hors Azure Resource Manager), seul le remplissage de vmId est garanti.

Exemple de document :
```json
{
   "nonce":"20201130-211924",
   "plan":{
      "name":"planName",
      "product":"planProduct",
      "publisher":"planPublisher"
   },
   "sku":"Windows-Server-2012-R2-Datacenter",
   "subscriptionId":"8d10da13-8125-4ba9-a717-bf7490507b3d",
   "timeStamp":{
      "createdOn":"11/30/20 21:19:19 -0000",
      "expiresOn":"11/30/20 21:19:24 -0000"
   },
   "vmId":"02aab8a4-74ef-476e-8182-f6d2ba4166a6"
}
```

#### <a name="sample-1-validate-that-the-vm-is-running-in-azure"></a>Exemple 1 : Vérifier que la machine virtuelle s’exécute dans Azure

Les fournisseurs de la Place de marché Azure veulent être sûrs que la licence de leur logiciel autorise l’exécution seulement dans Azure. Si quelqu’un copie le disque dur virtuel dans un environnement local, le fournisseur doit pouvoir le détecter. Via IMDS, ces fournisseurs peuvent recevoir des données signées qui garantissent une réponse seulement depuis Azure.

> [!NOTE]
> Cet exemple nécessite l’installation de l’utilitaire jq.

**Validation**

#### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Vérifiez que la signature provient de Microsoft Azure, et recherchez les erreurs dans la chaîne de certificats.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

#### <a name="linux"></a>[Linux](#tab/linux/)

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Résultats**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Vérifiez que la signature provient de Microsoft Azure et recherchez les erreurs dans la chaîne de certificats.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

---

> [!NOTE]
> En raison du mécanisme de mise en cache d’IMDS, une valeur `nonce` précédemment mise en cache peut être retournée.

La valeur de `nonce` dans le document signé peut être comparée si vous avez fourni un paramètre `nonce` dans la requête initiale.

> [!NOTE]
> Le certificat est différent pour le cloud public et pour chaque cloud souverain.

| Cloud | Certificat |
|-------|-------------|
| [Toutes les régions Azure mondiales en disponibilité générale](https://azure.microsoft.com/regions/) | *.metadata.azure.com
| [Azure Government](https://azure.microsoft.com/overview/clouds/government/) | *.metadata.azure.us
| [Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/) | *.metadata.azure.cn
| [Azure Allemagne](https://azure.microsoft.com/overview/clouds/germany/) | *.metadata.microsoftazure.de

> [!NOTE]
> Les certificats peuvent ne pas avoir une correspondance exacte de `metadata.azure.com` pour le cloud public. Pour cette raison, la validation de la certification doit autoriser un nom commun provenant de n’importe quel sous-domaine de `.metadata.azure.com`.

Si le certificat intermédiaire ne peut pas être téléchargé en raison de contraintes réseau lors de la validation, vous pouvez épingler le certificat intermédiaire. Azure effectue une substitution des certificats, pratique standard dans l’infrastructure PKI. Vous devez mettre à jour les certificats épinglés au moment de la substitution. Si une modification pour mettre à jour le certificat intermédiaire est planifiée, le blog Azure est mis à jour et les clients Azure en sont informés. 

Vous pouvez trouver les certificats intermédiaires dans le référentiel [PKI](https://www.microsoft.com/pki/mscorp/cps/default.htm). Les certificats intermédiaires de chaque région peuvent être différents.

> [!NOTE]
> Le certificat intermédiaire pour Azure Chine 21Vianet sera issu de l’autorité de certification racine globale DigiCert, au lieu de Baltimore.
Si vous avez épinglé les certificats intermédiaires pour Azure Chine dans le cadre du changement de l’autorité de chaîne racine, les certificats intermédiaires doivent être mis à jour.


## <a name="managed-identity"></a>Identité managée

Une identité managée, affectée par le système, peut être activée sur la machine virtuelle. Vous pouvez aussi affecter une ou plusieurs identités managées affectées par l’utilisateur à la machine virtuelle.
Vous pouvez ensuite demander des jetons pour les identités gérées auprès d’IMDS. Utilisez ces jetons pour l’authentification auprès d’autres services Azure, comme Azure Key Vault.

Pour plus d’informations sur la procédure d’activation de cette fonctionnalité, consultez [Obtenir un jeton d’accès](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="load-balancer-metadata"></a>Métadonnées de l’équilibreur de charge
Lorsque vous placez des instances de machine virtuelle ou de groupe de machines virtuelles derrière Azure Standard Load Balancer, utilisez IMDS pour récupérer les métadonnées relatives à l’équilibreur de charge et aux instances. Pour en savoir plus, consultez la section [Récupérer les informations de l’équilibreur de charge](../articles/load-balancer/instance-metadata-service-load-balancer.md).

## <a name="scheduled-events"></a>Événements planifiés
Vous pouvez obtenir l’état des événements planifiés en utilisant IMDS. L’utilisateur peut ensuite spécifier un ensemble d’actions à exécuter pour ces événements. Pour plus d’informations, consultez [Événements planifiés pour Linux](../articles/virtual-machines/linux/scheduled-events.md) ou [Événements planifiés pour Windows](../articles/virtual-machines/windows/scheduled-events.md).


## <a name="sample-code-in-different-languages"></a>Exemple de code dans différents langages

Le tableau suivant liste des exemples d’appel d’IMDS avec différents langages dans la machine virtuelle :

| Langage | Exemple |
|----------|---------|
| Bash | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
| C# | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
| Go | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
| Java | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
| NodeJS | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
| Perl | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
| PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
| Puppet | https://github.com/keirans/azuremetadata
| Python | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
| Ruby | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="errors-and-debugging"></a>Erreurs et débogage

S’il existe un élément de données introuvable ou une requête mal formée, le service de métadonnées d’instance retourne des erreurs HTTP standard. Par exemple :

| Code d'état HTTP | Motif |
|------------------|--------|
| `200 OK` | La demande a abouti.
| `400 Bad Request` | En-tête `Metadata: true` manquant ou paramètre `format=json` manquant lors de l’interrogation d’un nœud terminal
| `404 Not Found` | L’élément demandé n’existe pas
| `405 Method Not Allowed` | La méthode HTTP (verbe) n’est pas prise en charge sur le point de terminaison.
| `410 Gone` | Recommencez l’opération dans un délai maximum de 70 secondes
| `429 Too Many Requests` | Les [limites de débit](#rate-limiting) de l’API ont été dépassées
| `500 Service Error` | Recommencez l’opération plus tard

## <a name="frequently-asked-questions"></a>Forum aux questions

- J’obtiens l’erreur `400 Bad Request, Required metadata header not specified`. Qu’est-ce que cela signifie ?
  - IMDS requiert que l’en-tête `Metadata: true` soit passé dans la requête. Passer cet en-tête dans l’appel REST permet d’accéder à IMDS.

- Pourquoi je n’obtiens pas les informations de calcul pour ma machine virtuelle ?
  - Actuellement, IMDS prend en charge seulement les instances créées avec Azure Resource Manager.

- J’ai créé ma machine virtuelle par l’intermédiaire d’Azure Resource Manager il y a quelque temps. Pourquoi ne puis-je pas voir les informations de métadonnées de calcul ?
  - Si vous avez créé votre machine virtuelle après septembre 2016, ajoutez une [étiquette](../articles/azure-resource-manager/management/tag-resources.md) pour commencer à voir les métadonnées de calcul. Si vous avez créé votre machine virtuelle avant septembre 2016, ajoutez ou supprimez des extensions ou des disques de données aux instances de machine virtuelle pour actualiser les métadonnées.

- Les données utilisateur sont-elles identiques aux données personnalisées ?
  - Les données utilisateur offrent une fonctionnalité similaire aux données personnalisées, vous permettant de transmettre vos propres métadonnées à l’instance de machine virtuelle. La différence est que les données utilisateur sont récupérées via IMDS et sont persistantes pendant toute la durée de vie de l’instance de machine virtuelle. La fonctionnalité existante de données personnalisées continuera à fonctionner comme décrit dans [cet article](../articles/virtual-machines/custom-data.md). Toutefois, vous pouvez uniquement récupérer des données personnalisées par le biais du dossier système local, et non par le biais d’IMDS.

- Pourquoi je ne vois pas toutes les données renseignées pour une nouvelle version ?
  - Si vous avez créé votre machine virtuelle après septembre 2016, ajoutez une [étiquette](../articles/azure-resource-manager/management/tag-resources.md) pour commencer à voir les métadonnées de calcul. Si vous avez créé votre machine virtuelle avant septembre 2016, ajoutez ou supprimez des extensions ou des disques de données aux instances de machine virtuelle pour actualiser les métadonnées.

- Pourquoi l’erreur `500 Internal Server Error` ou `410 Resource Gone` s’affiche-t-elle ?
  - Réessayez votre requête. Pour plus d’informations, consultez [Gestion des erreurs transitoires](/azure/architecture/best-practices/transient-faults). Si le problème persiste, créez un ticket de support dans le portail Azure pour la machine virtuelle.

- Cela fonctionne-t-il pour des instances de groupe de machines virtuelles identiques ?
  - Oui, IMDS est disponible pour les instances de groupe de machines virtuelles identiques.

- J’ai mis à jour mes étiquettes dans des groupes de machines virtuelles identiques, mais elles n’apparaissent pas dans les instances (contrairement aux machines virtuelles mono-instances). Ai-je fait une erreur quelque part ?
  - Actuellement, les étiquettes pour les groupes de machines virtuelles identiques apparaissent sur la machine virtuelle seulement lors d’un redémarrage, d’un réimageage ou d’une modification de disque pour l’instance.

- Pourquoi les informations de référence SKU n’apparaissent-elles pas pour ma machine virtuelle dans les détails `instance/compute` ?
  - Pour les images personnalisées créées à partir de la Place de marché Azure, la plateforme Azure ne conserve ni les informations de référence SKU de l’image personnalisée ni les détails des machines virtuelles créées à partir de cette image. Elle est conçue ainsi, d’où l’absence d’exposition dans les détails `instance/compute` de la machine virtuelle.

- Pourquoi ma requête dépasse le délai d’expiration pour mon appel au service ?
  - Les appels de métadonnées doivent être effectués à partir de l’adresse IP principale affectée à la carte réseau principale de la machine virtuelle. De plus, si vous avez changé vos routes, il doit y avoir une route pour l’adresse 169.254.169.254/32 dans la table de routage locale de votre machine virtuelle.

    ### <a name="windows"></a>[Windows](#tab/windows/)

    1. Produisez une image mémoire de votre table de routage locale et recherchez l’entrée IMDS. Par exemple :
        ```console
        > route print
        IPv4 Route Table
        ===========================================================================
        Active Routes:
        Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
            168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
        169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
        ... (continues) ...
        ```
    1. Vérifiez qu’il existe une route pour `169.254.169.254` et notez l’interface réseau correspondante (par exemple `172.16.69.7`).
    1. Produisez une image mémoire de la configuration de l’interface et recherchez l’interface qui correspond à celle référencée dans la table de routage, en notant l’adresse MAC (physique).
        ```console
        > ipconfig /all
        ... (continues) ...
        Ethernet adapter Ethernet:

        Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
        Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
        Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
        DHCP Enabled. . . . . . . . . . . : Yes
        Autoconfiguration Enabled . . . . : Yes
        Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
        IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
        Subnet Mask . . . . . . . . . . . : 255.255.255.0
        ... (continues) ...
        ```
    1. Vérifiez que l’interface correspond à la carte réseau principale et à l’adresse IP principale de la machine virtuelle. Vous pouvez trouver la carte réseau et l’adresse IP principales en examinant la configuration réseau dans le portail Azure ou en la recherchant avec Azure CLI. Notez les adresses IP privées (et l’adresse MAC si vous utilisez l’interface CLI). Voici un exemple PowerShell CLI :
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: wintest767 True 00-0D-3A-E5-1C-C0
        ```
    1. Si elles ne correspondent pas, mettez à jour la table de routage afin de cibler la carte réseau et l’adresse IP principales.

    ### <a name="linux"></a>[Linux](#tab/linux/)

    1. Videz votre table de routage locale à l’aide d’une commande telle que `netstat -r` et recherchez l’entrée IMDS (p. ex.) :
        ```console
        ~$ netstat -r
        Kernel IP routing table
        Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
        default         _gateway        0.0.0.0         UG        0 0          0 eth0
        168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
        169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
        172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
        ```
    1. Vérifiez qu’il existe une route pour `169.254.169.254` et notez l’interface réseau correspondante (par exemple `eth0`).
    1. Videz la configuration d’interface pour l’interface correspondante dans la table de routage (notez que le nom exact du fichier de configuration peut varier).
        ```console
        ~$ cat /etc/netplan/50-cloud-init.yaml
        network:
        ethernets:
            eth0:
                dhcp4: true
                dhcp4-overrides:
                    route-metric: 100
                dhcp6: false
                match:
                    macaddress: 00:0d:3a:e4:c7:2e
                set-name: eth0
        version: 2
        ```
    1. Si vous utilisez une adresse IP dynamique, notez l’adresse MAC. Si vous utilisez une adresse IP statique, vous pouvez noter la ou les adresses IP répertoriées et/ou l’adresse MAC.
    1. Vérifiez que l’interface correspond à la carte réseau principale et à l’adresse IP principale de la machine virtuelle. Vous pouvez trouver la carte réseau et l’adresse IP principales en examinant la configuration réseau dans le portail Azure ou en la recherchant avec Azure CLI. Notez les adresses IP privées (et l’adresse MAC si vous utilisez l’interface CLI). Voici un exemple PowerShell CLI :
        ```powershell
        $ResourceGroup = '<Resource_Group>'
        $VmName = '<VM_Name>'
        $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
        foreach($NicName in $NicNames)
        {
            $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
            Write-Host $NicName, $Nic.primary, $Nic.macAddress
        }
        # Output: ipexample606 True 00-0D-3A-E4-C7-2E
        ```
    1. Si elles ne correspondent pas, mettez à jour la table de routage afin que la carte réseau/adresse IP principale soit ciblée.

    ---

- Clustering de basculement dans Windows Server
  - Quand vous interrogez IMDS avec le clustering de basculement, il est parfois nécessaire d’ajouter une route à la table de routage. Voici comment faire :

    1. Ouvrez une invite de commandes avec les privilèges Administrateur.

    1. Exécutez la commande suivante et notez l’adresse de l’interface pour la destination réseau (`0.0.0.0`) dans la table de routage IPv4.

    ```bat
    route print
    ```

    > [!NOTE]
    > L’exemple de sortie suivant provient d’une machine virtuelle Windows Server avec le cluster de basculement activé. Pour simplifier, la sortie contient seulement la table de routage IPv4.

    ```
    IPv4 Route Table
    ===========================================================================
    Active Routes:
    Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
            10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
            10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
            10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
            127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
            127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
    127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
        169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
        169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
    169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
            224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
            224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
    255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
    255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
    ```

    Exécutez la commande suivante et notez l’adresse de l’interface pour la destination réseau (`0.0.0.0`) qui est (`10.0.1.10`) dans cet exemple.

    ```bat
    route add 169.254.169.254/32 10.0.1.10 metric 1 -p
    ```

## <a name="support"></a>Support

Si vous ne pouvez pas obtenir une réponse de métadonnées après plusieurs tentatives, vous pouvez créer un ticket de support dans le portail Azure.

## <a name="product-feedback"></a>Commentaires sur le produit

Vous pouvez formuler des commentaires sur le produit et des idées sur notre [canal de commentaires des utilisateurs](https://feedback.azure.com/d365community/forum/ec2f1827-be25-ec11-b6e6-000d3a4f0f1c?c=a60ebac8-c125-ec11-b6e6-000d3a4f0f1c), sous Machines virtuelles > Instance Metadata Service.

## <a name="next-steps"></a>Étapes suivantes

- [Acquérir un jeton d’accès pour la machine virtuelle](../articles/active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

- [Événements planifiés pour Linux](../articles/virtual-machines/linux/scheduled-events.md)

- [Événements planifiés pour Windows](../articles/virtual-machines/windows/scheduled-events.md)
