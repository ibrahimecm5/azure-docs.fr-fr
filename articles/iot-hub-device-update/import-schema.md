---
title: Importation de mises à jour dans Device Update pour IoT Hub – Schéma et autres informations | Microsoft Docs
description: Schéma et informations associées (dont des objets) utilisés lors de l’importation de mises à jour dans Device Update pour IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 09dc0bd3afb2b9bfc99313ad38d5c7ad19086cb8
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061037"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Importation de mises à jour dans Device Update pour IoT Hub – Schéma et autres informations
Si vous souhaitez importer une mise à jour dans Device Update pour IoT Hub, veillez à consulter d’abord les [concepts](import-concepts.md) et le [guide pratique](import-update.md). Pour des détails sur le schéma utilisé lors de la construction d’un manifeste d’importation, ou des informations sur les objets associés, voir ci-dessous.

## <a name="import-manifest-schema"></a>Schéma de manifeste d’importation

| Nom | Type | Description | Restrictions |
| --------- | --------- | --------- | --------- |
| UpdateId | l'objet `UpdateId` | Identité de la mise à jour. |
| UpdateType | string | Type de mise à jour : <br/><br/> * Spécifiez `microsoft/apt:1` quand vous effectuez une mise à jour basée sur un package à l’aide d’un agent de référence.<br/> * Spécifiez `microsoft/swupdate:1` quand vous effectuez une mise à jour basée sur une image à l’aide d’un agent de référence.<br/> * Spécifiez `microsoft/simulator:1` quand vous utilisez un exemple de simulateur d’agent.<br/> * Spécifiez un type personnalisé si vous développez un agent personnalisé. | Format: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> Longueur maximale totale de 32 caractères |
| InstalledCriteria | string | Chaîne interprétée par l’agent pour déterminer si la mise à jour a été appliquée avec succès :  <br/> * Spécifiez la **valeur** de SWVersion pour le type de mise à jour `microsoft/swupdate:1`.<br/> * Spécifiez `{name}-{version}` pour le type de mise à jour `microsoft/apt:1`, dont le nom et la version proviennent du fichier APT.<br/> * Spécifiez une chaîne personnalisée si vous développez un agent personnalisé.<br/> | 64 caractères maximum |
| Compatibilité | Tableau d’`CompatibilityInfo` [objets](#compatibilityinfo-object) | Informations de compatibilité de l’appareil compatible avec cette mise à jour. | Maximum de 10 éléments |
| CreatedDateTime | date/heure | Date et heure de création de la mise à jour. | Format de date et heure ISO 8601 délimité, en UTC |
| ManifestVersion | string | Version du schéma du manifeste d’importation. Spécifiez `2.0`, qui sera compatible avec l’interface `urn:azureiot:AzureDeviceUpdateCore:1` et l’interface `urn:azureiot:AzureDeviceUpdateCore:4`. | Doit être `2.0` |
| Fichiers | Tableau d’objets `File` | Mettre à jour les fichiers de charge utile | Maximum cinq fichiers |

## <a name="updateid-object"></a>Objet UpdateId

| Nom | Type | Description | Restrictions |
| --------- | --------- | --------- | --------- |
| Fournisseur | string | Partie fournisseur de l’identité de mise à jour. | 1 à 64 caractères, alphanumériques, point et tiret. |
| Nom | string | Partie nom de l’identité de mise à jour. | 1 à 64 caractères, alphanumériques, point et tiret. |
| Version | version | Partie version de l’identité de mise à jour. | 2 à 4 parties, numéro de version séparé par des points. Le nombre total de _chaque_ partie séparée par un point peut être compris entre 0 et 2147483647. Les zéros non significatifs ne sont pas pris en charge.

## <a name="file-object"></a>Objet File

| Nom | Type | Description | Restrictions |
| --------- | --------- | --------- | --------- |
| Nom de fichier | string | Nom du fichier | Ne doit pas dépasser 255 caractères. Doit être unique au sein d’une mise à jour |
| SizeInBytes | Int64 | Taille du fichier en octets. | Afficher les [Limites de mise à jour d’appareil](./device-update-limits.md) pour la taille maximale par fichier individuel et collectivement par mise à jour |
| Codes de hachage | l'objet `Hashes` | Objet JSON contenant le ou les hachages du fichier |

## <a name="compatibilityinfo-object"></a>Objet CompatibilityInfo

| Nom | Type | Description | Restrictions |
| --- | --- | --- | --- |
| DeviceManufacturer | string | Fabricant de l’appareil avec lequel la mise à jour est compatible. | 1 à 64 caractères, alphanumériques, point et tiret. |
| DeviceModel | string | Modèle de l’appareil avec lequel la mise à jour est compatible. | 1 à 64 caractères, alphanumériques, point et tiret. |

## <a name="hashes-object"></a>Objet Hashes

| Nom | Obligatoire | Type | Description |
| --------- | --------- | --------- | --------- |
| Sha256 | True | string | Hachage encodé en base64 du fichier à l’aide de l’algorithme SHA-256. |

## <a name="example-import-request-body"></a>Exemple d’importation de corps de requête

Si vous utilisez l’exemple de sortie du manifeste d’importation à partir de la page [Guide pratique pour ajouter une nouvelle mise à jour](./import-update.md#review-the-generated-import-manifest), et que vous souhaitez appeler l’[API REST](/rest/api/deviceupdate/updates) de mise à jour de l’appareil directement pour effectuer l’importation, le corps de la requête correspondant doit ressembler à ceci :

```json
{
  "importManifest": {
    "url": "http://<your Azure Storage location file path>/importManifest.json",
    "sizeInBytes": <size of import manifest file>,
    "hashes": {
      "sha256": "<hash of import manifest file>"
    }
  },
  "files": [
    {
      "filename": "file1.json",
      "url": "http://<your Azure Storage location file path>/file1.json"
    },
    {
          "filename": "file2.zip",
          "url": "http://<your Azure Storage location file path>/file2.zip"
    },
  ]
}
```

## <a name="oauth-authorization-when-calling-device-update-apis"></a>Autorisation OAuth lors de l’appel des API Device Update

**azure_auth**

Type de flux OAuth2 Azure Active Directory : flux OAuth2 : any 

URL d’autorisation : https://login.microsoftonline.com/common/oauth2/authorize

**Étendues**

| Nom | Description |
| --- | --- |
| `https://api.adu.microsoft.com/user_impersonation` | Emprunter l’identité de votre compte d’utilisateur |
| `https://api.adu.microsoft.com/.default`  | Flux d’informations d’identification du client |


**autorisations**

Si une application Azure AD est utilisée pour connecter l’utilisateur, l’étendue doit inclure /user_impersonation. 

Vous devrez ajouter des autorisations à votre application Azure AD (dans l’onglet Autorisations d’API de la vue Application Azure AD) pour utiliser l’API Azure Device Update. Demandez une autorisation d’API à Azure Device Update (dans « API utilisées par mon organisation ») et accordez l’autorisation déléguée user_impersonation.

ADU accepte les jetons acquérant des jetons à l’aide de l’un des flux pris en charge par Azure AD pour les utilisateurs, les applications ou les identités managées. Toutefois, certains flux nécessitent une configuration d’application Azure AD supplémentaire : 

* Pour les flux de clients publics, veillez à activer les flux mobiles et de bureau.
* Pour les flux implicites, veillez à ajouter une plateforme web et à sélectionner « Jetons d’accès » pour le point de terminaison d’autorisation.

**Exemple utilisant Azure CLI :**

```azurecli
az login

az account get-access-token --resource 'https://api.adu.microsoft.com/'
```

**Exemples d’acquisition d’un jeton à l’aide de la bibliothèque MSAL PowerShell :**

_Utilisation des informations d’identification de l’utilisateur_ 

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/user_impersonation'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope
```

_Utilisation des informations d’identification de l’utilisateur avec le code de l’appareil_

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/user_impersonation'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope -Interactive -DeviceCode
```

_Utilisation des informations d’identification de l’application_

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$cert = '<client_certificate>'
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/.default'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope -ClientCertificate $cert
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [concepts d’importation](./import-concepts.md).

Si vous êtes prêt, suivez le [Guide pratique d’importation](./import-update.md), qui vous guidera pas à pas tout au long du processus d’importation.
