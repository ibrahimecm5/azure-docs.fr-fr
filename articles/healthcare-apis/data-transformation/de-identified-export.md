---
title: Exportation de données déidentifiées (version préliminaire) pour le service FHIR
description: Cet article explique comment configurer et utiliser une exportation anonymisée
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 9/28/2020
ms.author: ranku
ms.openlocfilehash: a7757830dfb75f7ad111913ee4a8ea41926db600
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132867371"
---
# <a name="exporting-de-identified-data-preview"></a>Exportation de données anonymisées (préversion)

> [!Note] 
> Les résultats obtenus lors de l’utilisation de l’exportation anonymisée varient en fonction de facteurs tels que les données entrées et les fonctions sélectionnées par le client. Microsoft est dans l’impossibilité d’évaluer les résultats d’une exportation anonymisée ou de déterminer l’acceptabilité pour les cas d’utilisation et les besoins de conformité du client. Aucune garantie n’est fournie quant au respect par l’exportation anonymisée d’exigences légales, réglementaires ou de conformité spécifiques.

La commande $export peut également être utilisée pour exporter des données anonymisées à partir du serveur FHIR. Elle utilise le moteur d’anonymisation des [outils FHIR pour l’anonymisation](https://github.com/microsoft/FHIR-Tools-for-Anonymization), et accepte les détails de configuration de l’anonymisation dans les paramètres de requête. Vous pouvez créer votre propre fichier de configuration de l’anonymisation ou utiliser l’[exemple de fichier de configuration](https://github.com/microsoft/Tools-for-Health-Data-Anonymization/blob/master/docs/FHIR-anonymization.md#sample-configuration-file) pour la méthode HIPAA Safe Harbor comme point de départ. 

## <a name="configuration-file"></a>Fichier de configuration

le moteur d’anonymisation est fourni avec un exemple de fichier de configuration pour répondre aux exigences de la méthode de la « sphère de Coffre HIPAA ». Le fichier de configuration est un fichier JSON avec 4 sections : `fhirVersion` , `processingErrors` , `fhirPathRules` , `parameters` . 
* `fhirVersion` spécifie la version de FHIR pour le moteur d’anonymisation.
* `processingErrors` Spécifie l’action à entreprendre pour les erreurs de traitement qui peuvent survenir lors de l’anonymisation. Vous pouvez _déclencher_ ou _conserver_ les exceptions en fonction de vos besoins.
* `fhirPathRules` spécifie la méthode d’anonymisation à utiliser. Les règles sont exécutées dans l’ordre d’apparition dans le fichier de configuration.
* `parameters` définit des règles pour les comportements d’anonymisation spécifiés dans _fhirPathRules_.

Voici un exemple de fichier de configuration pour R4 :

```json
{
  "fhirVersion": "R4",
  "processingError":"raise",
  "fhirPathRules": [
    {"path": "nodesByType('Extension')", "method": "redact"},
    {"path": "Organization.identifier", "method": "keep"},
    {"path": "nodesByType('Address').country", "method": "keep"},
    {"path": "Resource.id", "method": "cryptoHash"},
    {"path": "nodesByType('Reference').reference", "method": "cryptoHash"},
    {"path": "Group.name", "method": "redact"}
  ],
  "parameters": {
    "dateShiftKey": "",
    "cryptoHashKey": "",
    "encryptKey": "",
    "enablePartialAgesForRedact": true
  }
}
```

Pour obtenir des informations plus détaillées sur chacune de ces 4 sections du fichier de configuration, consultez [cette page](https://github.com/microsoft/Tools-for-Health-Data-Anonymization/blob/master/docs/FHIR-anonymization.md#configuration-file-format).
## <a name="using-export-command-for-the-de-identified-data"></a>Utilisation de la commande $export pour les données déidentifiées
 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

> [!Note] 
> Pour le moment, le service FHIR prend uniquement en charge l’exportation désidentifiée au niveau du système ($export).

|Paramètre de requête.            | Exemple |Caractère facultatif| Description|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.json|Requis pour l’exportation anonymisée |Nom du fichier de configuration. Consultez le format du fichier de configuration [ici](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format). Ce fichier doit être conservé à l’intérieur d’un conteneur nommé **anonymisation** au sein du compte de stockage Azure configuré comme emplacement d’exportation. |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|Facultatif pour l’exportation anonymisée|Il s’agit de l’ETag du fichier de configuration. Vous pouvez récupérer l’ETag à l’aide de l’explorateur de stockage Azure à partir de la propriété blob|

> [!IMPORTANT]
> L’exportation brute et l’exportation anonymisée écrivent toutes deux dans le compte de stockage Azure spécifié dans le cadre de la configuration de l’exportation. Il est recommandé d’utiliser différents conteneurs correspondant à différentes configurations anonymisées, et de gérer l’accès utilisateur au niveau du conteneur.
