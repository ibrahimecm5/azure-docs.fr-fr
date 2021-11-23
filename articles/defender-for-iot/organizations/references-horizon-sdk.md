---
title: Kit SDK Horizon
description: Le Kit de développement logiciel (SDK) Horizon permet aux développeurs Microsoft Defender pour IoT de concevoir des plug-ins de dissecteur qui décodent le trafic réseau afin que les programmes d’analyse de réseau de Defender pour IoT puissent le traiter.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 9affe81bc905e2852732b3892aaac26003c1e8f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286751"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Dissecteur de protocole propriétaire Horizon

Horizon est un environnement de développement ouvert (ODE) utilisé pour sécuriser les appareils IoT et ICS exécutant des protocoles propriétaires.

Cet environnement fournit les solutions suivantes pour les clients et les partenaires technologiques :

- Une prise en charge complète et illimitée des protocoles courants, propriétaires et personnalisés ou des protocoles qui s’écartent de toute norme. 

- Un nouveau niveau de flexibilité et de possibilité pour le développement de plug-ins de dissecteur.

- Un outil qui étend de façon exponentielle la visibilité et le contrôle de l’OT, sans nécessité de mettre à niveau les versions de plateforme Defender pour IoT.

- La sécurité de permettre un développement propriétaire sans divulguer d’informations sensibles.

Le Kit de développement logiciel (SDK) Horizon permet aux développeurs Microsoft Defender pour IoT de concevoir des plug-ins de dissecteur qui décodent le trafic réseau afin que les programmes d’analyse de réseau de Defender pour IoT puissent le traiter.

Les dissecteurs de protocole sont développés en tant que plug-ins externes et intégrés avec une vaste gamme de services Defender pour IoT. Par exemple, les services qui fournissent des fonctionnalités de surveillance, d’alerte et de création de rapports.

## <a name="secure-development-environment"></a>Environnement de développement sécurisé 

L’ODE Horizon permet le développement de protocoles personnalisés ou propriétaires qui ne peuvent pas être partagés en dehors d’une organisation. Par exemple, en raison de réglementations légales ou de stratégies d’entreprise.

Développez des plug-ins de dissecteur sans : 

- divulguer d’informations propriétaires sur la façon dont vos protocoles sont définis ;

- partager vos PCAP sensibles ;

- violer de réglementations de conformité.

Envoyez un e-mail à l’adresse <ms-horizon-support@microsoft.com> pour obtenir des informations sur le développement de plug-ins de protocole.
## <a name="customization-and-localization"></a>Globalisation et localisation  

Le Kit de développement logiciel (SDK) prend en charge différentes options de personnalisation, à savoir :

  - texte pour les codes de fonction ; 

  - texte entièrement localisé pour les alertes, les événements et les paramètres de protocole. Pour plus d’informations, consultez [Créer des fichiers de mappage (JSON)](#create-mapping-files-json).

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Afficher les alertes entièrement localisées.":::

## <a name="horizon-architecture"></a>Architecture d’Horizon

Le modèle architectural comprend trois couches de produit.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Couche de plateforme Defender pour IoT

Permet une intégration immédiate et une surveillance en temps réel des plug-ins de dissecteurs personnalisés dans la plateforme Defender pour IoT, sans avoir à mettre à niveau la version de celle-ci.

## <a name="defender-for-iot-services-layer"></a>Couche de services Defender pour IoT

Chaque service est conçu comme un pipeline, dissocié d’un protocole spécifique, ce qui permet un développement indépendant, plus efficace.

Chaque service est conçu comme un pipeline, dissocié d’un protocole spécifique. Les services écoutent le trafic sur le pipeline. Ils interagissent avec les données du plug-in et le trafic capturé par les capteurs pour indexer les protocoles déployés et analyser la charge utile du trafic, permettant ainsi un développement indépendant, plus efficace.

## <a name="custom-dissector-layer"></a>Couche de dissecteur personnalisé 

Permet de créer des plug-ins à l’aide du Kit de développement logiciel (SDK) propriétaire Defender pour IoT (incluant l’implémentation C++ et la configuration JSON) pour : 

- définir comment identifier le protocole ;

- définir comment mapper les champs que vous souhaitez extraire du trafic et les extraire. 

- définir comment intégrer avec les services Defender pour IoT.

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="Couches intégrées.":::

Defender pour IoT fournit des dissecteurs de base pour les protocoles courants. Vous pouvez créer vos dissecteurs en plus de ces protocoles.

## <a name="before-you-begin"></a>Avant de commencer

## <a name="what-this-sdk-contains"></a>Contenu du Kit de développement logiciel (SDK) 

Ce kit contient les fichiers d’en-tête nécessaires pour le développement. Le processus de développement nécessite des étapes de base et des étapes avancées facultatives, décrites dans ce Kit de développement logiciel (SDK).

Pour plus d’informations sur la réception des fichiers d’en-tête et d’autres ressources, contactez <ms-horizon-support@microsoft.com>.

## <a name="about-the-environment-and-setup"></a>À propos de l’environnement et de la configuration 

### <a name="requirements"></a>Conditions requises

- L’environnement de développement préconisé est Linux. Si vous développez dans un environnement Windows, envisagez d’utiliser une machine virtuelle avec un système Linux.

- Pour le processus de compilation, utilisez un GCC 7.4.0 ou supérieur. Utilisez n’importe quelle classe standard de stdlib prise en charge en C++17.

- Defender pour IoT versions 3.0 et ultérieures.

### <a name="process"></a>Process

1. [Téléchargez](https://www.eclipse.org/) l’environnement de développement intégré (IDE) Eclipse pour développeurs C/C++. Vous pouvez utiliser tout autre IDE de votre choix. Ce document vous guide tout au long de la configuration à l’aide de l’environnement de développement intégré Eclipse.

1. Après avoir lancé l’environnement de développement intégré Eclipse et configuré l’espace de travail (où vos projets seront stockés), appuyez sur **Ctrl + n**, et créez-le en tant que projet C++.

1. Dans l’écran suivant, définissez le nom sur le protocole que vous souhaitez développer et sélectionnez le type de projet `Shared Library` et `AND Linux GCC`.

1. Modifiez les propriétés du projet, sous **C/C++ Build (Build C/C++)**  > **Settings (Paramètres)**  > **Tool Settings (Paramètres d’outil)**  > **GCC C++ Compiler (Compilateur GCC C++)**  > **Miscellaneous (Divers)**  > **Tick Position Independent Code (Code indépendant de la position de graduation)** .

1. Collez les exemples de code que vous avez reçus avec le Kit de développement logiciel (SDK), puis compilez.

1. Ajoutez les artefacts (bibliothèque, config.json et métadonnées) à un fichier tar.gz, puis modifiez l’extension de fichier en \<XXX>.hdp, où \<XXX> est le nom du plug-in.

### <a name="research"></a>Recherche 

Avant de commencer :

- Lisez la spécification du protocole si elle est disponible.

- Identifiez les champs de protocole que vous envisagez d’extraire.

- Planifiez les objectifs de votre mappage.

## <a name="about-plugin-files"></a>À propos des fichiers de plug-in 

Trois fichiers sont définis au cours du processus de développement.

### <a name="json-configuration-file-required"></a>Fichier de configuration JSON (obligatoire) 

Ce fichier doit définir l’ID du dissecteur, ainsi que les déclarations, les dépendances, les exigences d’intégration, les paramètres de validation et les définitions de mappage pour traduire des valeurs en noms, et les nombres en texte. Pour plus d’informations, suivez les liens suivants :

- [Préparer le fichier de configuration (JSON)](#prepare-the-configuration-file-json)

- [Préparer les validations du code d’implémentation](#prepare-implementation-code-validations)

- [Extraire les métadonnées de l’appareil](#extract-device-metadata)

- [Se connecter à un service d’indexation (ligne de base)](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>Code d’implémentation : C++ (obligatoire)

Le code d’implémentation (CPP) analyse le trafic brut et le mappe à des valeurs telles que des services, des classes et des codes de fonction. Il extrait les champs de couche et les mappe à leurs noms d’index à partir des fichiers de configuration JSON. Les champs à extraire du CPP sont définis dans le fichier de configuration. Pour plus d’informations, consultez la section [Préparer le code d’implémentation (C++)](#prepare-the-implementation-code-c).

### <a name="mapping-files-optional"></a>Fichiers de mappage (facultatifs)

Vous pouvez personnaliser le texte de sortie du plug-in pour répondre aux besoins de votre environnement d’entreprise.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migration":::

Vous pouvez définir et mettre à jour les fichiers de mappage pour mettre à jour le texte sans modifier le code. Chaque fichier peut mapper un ou plusieurs champs :

  - Mappage de valeurs de champ à des noms, par exemple, 1:Reset, 2:Start, 3:Stop.

  - Mappage de texte pour prendre en charge plusieurs langues.

Pour plus d’informations, consultez la section [Créer des fichiers de mappage (JSON)](#create-mapping-files-json).

## <a name="create-a-dissector-plugin-overview"></a>Créer un plug-in de dissecteur (vue d’ensemble)

1. Consultez la section [À propos de l’environnement et de la configuration](#about-the-environment-and-setup).

2.  [Préparez le code d’implémentation (C++)](#prepare-the-implementation-code-c). Copiez le fichier **template.json** et modifiez-le en fonction de vos besoins. Ne modifiez pas les clés. 

3. [Préparez le fichier de configuration (JSON)](#prepare-the-configuration-file-json). Copiez le fichier **template.cpp** et implémentez une méthode override. Pour plus d’informations, consultez la section [horizon::protocol::BaseParser](#horizonprotocolbaseparser).

4. [Préparez les validations du code d’implémentation](#prepare-implementation-code-validations).

## <a name="prepare-the-implementation-code-c"></a>Préparer le code d’implémentation (C++)

Le fichier CPP est un analyseur responsable des opérations suivantes :

- validation de l’en-tête de paquet et de la charge utile (par exemple, longueur de l’en-tête ou structure de la charge utile) ;

- extraction des données de l’en-tête et de la charge utile dans des champs définis ;

- implémentation de l’extraction des champs configurés par le fichier JSON.

### <a name="what-to-do"></a>Procédure à suivre

Copiez le fichier **.cpp** du modèle et implémentez une méthode override. Pour plus d’informations, consultez la section [horizon::protocol::BaseParser](#horizonprotocolbaseparser).

### <a name="basic-c-template-sample"></a>Exemple de modèle C++ de base 

Cette section fournit le modèle de protocole de base, avec des fonctions standard pour un exemple de protocole Defender pour IoT Horizon.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>Description de modèle C++ de base  

Cette section fournit le modèle de protocole de base, avec une description des fonctions standard pour un exemple de protocole Defender pour IoT Horizon. 

### <a name="include-pluginpluginh"></a>#include “plugin/plugin.h”

Définition que le plug-in utilise. Le fichier d’en-tête contient tout ce qui est nécessaire pour effectuer le développement.

### <a name="horizonprotocolbaseparser"></a>horizon::protocol::BaseParser

Interface de communication entre l’infrastructure Horizon et la couche de plug-in. Pour plus d’informations, consultez la section [Architecture Horizon](#horizon-architecture) qui donne une vue d’ensemble des couches.

ProcessLayer est la méthode utilisée pour traiter les données.

- Le premier paramètre dans le code de la fonction est l’utilitaire de traitement utilisé pour récupérer des données traitées précédemment, et créer des champs et des couches.

- Le deuxième paramètre dans le code de la fonction correspond aux données actuelles transmises à partir de l’analyseur précédent.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

Utilisez pour créer l’instance de votre analyseur.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>Exemple de code de fonction de protocole 

Cette section fournit un exemple de la façon dont le numéro de code (2 octets) et la longueur du message (4 octets) sont extraits.

Cette opération est effectuée en fonction du mode Endian fourni dans le fichier de configuration JSON. Cela signifie que, si le protocole est *mode Little Endian* et que le capteur s’exécute sur un ordinateur avec le mode Little Endian, il sera converti.

Une couche est également créée pour stocker des données. Utilisez le *fieldsManager* des utilitaires de traitement pour créer des champs. Un champ ne peut avoir que l’un des types suivants : *STRING*, *NUMBER*, *RAW DATA*, *ARRAY* (de type spécifique) ou *COMPLEX*. Cette couche peut contenir un type number, raw ou string avec un ID.

Dans l’exemple ci-dessous, les deux champs suivants sont extraits :

- `function_code_number`

- `headerLength`

Une nouvelle couche est créée, dans laquelle le champ extrait est copié.

L’exemple ci-dessous décrit une fonction spécifique qui est la logique principale implémentée pour le traitement du plug-in.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>Champ JSON associé 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="Champ JSON associé.":::

## <a name="prepare-the-configuration-file-json"></a>Préparer le fichier de configuration (JSON)

Le Kit de développement logiciel (SDK) Horizon utilise le format JSON (JavaScript Object Notation) standard, un format léger pour le stockage et le transport des données ne nécessitant pas de langage de script propriétaire.

Cette section décrit les déclarations de configuration JSON minimales ainsi que la structure associée, et fournit un exemple de fichier de configuration qui définit un protocole. Ce protocole est automatiquement intégré avec le service de découverte d’appareil.

## <a name="file-structure"></a>Structure de fichiers

L’exemple ci-dessous décrit la structure de fichier.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="Exemple de la structure de fichier.":::

### <a name="what-to-do"></a>Procédure à suivre

Copiez le fichier `config.json` modèle et modifiez-le en fonction de vos besoins. Ne modifiez pas la clé. Les clés sont marquées en rouge dans l’[exemple de fichier de configuration JSON](#sample-json-configuration-file). 

### <a name="file-naming-requirements"></a>Exigences relatives à la dénomination du fichier 

Le fichier de configuration JSON doit être enregistré sous le nom `config.json`.

### <a name="json-configuration-file-fields"></a>Champs du fichier de configuration JSON

Cette section décrit les champs de configuration JSON que vous allez définir. Ne modifiez pas les champs *labels*.

### <a name="basic-parameters"></a>Paramètres de base

Cette section décrit les paramètres de base.

| Étiquette du paramètre | Description | Type |
|--|--|--|
| **Identifiant** | Nom du protocole. Supprimez la valeur par défaut et ajoutez le nom de votre protocole tel qu’il apparaît. | String |
| **endianess** | Définit la manière dont les données multioctets sont encodées. Utilisez le terme « little » ou « big » uniquement. Issu de la spécification du protocole ou de l’enregistrement du trafic. | String |
| **sanity_failure_codes** | Il s’agit des codes renvoyés par l’analyseur en présence d’un conflit de validité concernant l’identité du code. Consultez la validation par nombre magique dans la section C++. | String |
| **malformed_codes** | Il s’agit de codes qui ont été correctement identifiés, mais une erreur est détectée. Par exemple, si la longueur du champ est insuffisante ou excessive, ou si une valeur n’est pas valide. | String |
| **dissect_as** | Tableau définissant où le trafic de protocole spécifique doit arriver. | TCP/UDP, port, etc. |
| **fields** | Déclaration des champs qui seront extraits du trafic. Chaque champ a ses propres ID (nom) et type (numeric, string, raw, array, complex). Par exemple, le champ [function](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) extrait dans le fichier de l’analyseur d’implémentation. Les champs écrits dans le fichier de configuration sont les seuls qui peuvent être ajoutés à la couche. |  |

### <a name="other-advanced-fields"></a>Autres champs avancés 

Cette section décrit d’autres champs.

| Étiquette du paramètre | Description |
|-----------------|--------|
| **allow_lists** | Vous pouvez indexer les valeurs de protocole et les afficher dans des rapports d’exploration de données. Ces rapports reflètent la ligne de base de votre réseau. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Exemple de vue d’exploration de données."::: <br /> Pour plus d’informations, consultez [Se connecter à un service d’indexation (ligne de base)](#connect-to-an-indexing-service-baseline). |
| **firmware** | Vous pouvez extraire des informations du microprogramme, définir des valeurs d’index et déclencher des alertes de microprogramme pour le protocole de plug-in. Pour plus d’informations, consultez la section [Extraire les données du microprogramme](#extract-firmware-data). |
| **value_mapping** | Vous pouvez personnaliser le texte de sortie du plug-in pour répondre aux besoins de votre environnement d’entreprise en définissant et en mettant à jour les fichiers de mappage. Par exemple, mappez à des fichiers de langue. Vous pouvez facilement implémenter des modifications dans du texte sans modifier ou affecter le code. Pour plus d’informations, consultez la section [Créer des fichiers de mappage (JSON)](#create-mapping-files-json). |

## <a name="sample-json-configuration-file"></a>Exemple de fichier de configuration JSON

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>Préparer les validations du code d’implémentation

Cette section décrit les fonctions de validation de code C++ d’implémentation et fournit un exemple de code. Deux couches de validation sont disponibles :

- validité ;

- code mal formé.

Vous n’avez pas besoin de créer un code de validation pour générer un plug-in fonctionnel. Si vous ne préparez pas de code de validation, les rapports d’exploration de données de capteur fournissent une indication de traitement réussi.

Les valeurs de champ peuvent être mappées au texte dans les fichiers de mappage et mises à jour en toute transparence sans incidence sur le traitement.

## <a name="sanity-code-validations"></a>Validations du code de validité 

Elles vérifient que le paquet transmis correspond aux paramètres de validation du protocole, ce qui vous aide à identifier celui-ci dans le trafic.

Par exemple, utilisez les 8 premiers octets en tant que *nombre magique*. En cas de non-validité, une réponse d’échec de validité est retournée.

Par exemple :

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

Si d’autres plug-ins pertinents ont été déployés, le paquet est validé par rapport à ceux-ci.

## <a name="malformed-code-validations"></a>Validations de code mal formé 

Validations malformées utilisées après que le protocole a été validé positivement.

En cas d’échec du traitement des paquets basé sur le protocole, une réponse d’échec est retournée.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="code mal formé":::

## <a name="c-sample-with-validations"></a>Exemple C++ avec des validations

Selon la fonction, le processus est exécuté comme dans l’exemple ci-dessous.

### <a name="function-20"></a>Fonction 20 

- Elle est traitée en tant que microprogramme.

- Les champs sont lus selon la fonction.

- Les champs sont ajoutés à la couche.

### <a name="function-10"></a>Fonction 10 

- La fonction contient une sous-fonction qui est une opération plus spécifique.

- La sous-fonction est lue et ajoutée à la couche.

Cela fait, le traitement est terminé. La valeur de retour indique si la couche de dissecteur a été correctement traitée. Si c’est le cas, la couche devient utilisable.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>Extraire les métadonnées de l’appareil

Vous pouvez extraire les métadonnées suivantes sur des ressources :

  - `Is_distributed_control_system` : indique si le protocole fait partie du système de contrôle distribué (exemple : le protocole SCADA doit retourner false).

  - `Has_protocol_address` : indique s’il existe une adresse de protocole, à savoir l’adresse spécifique pour le protocole actuel, par exemple, l’identificateur d’unité MODBUS.

  - `Is_scada_protocol` : indique si le protocole est spécifique des réseaux OT.

  - `Is_router_potential` : indique si le protocole est principalement utilisé par des routeurs. Par exemple, LLDP, CDP ou STP.

Pour ce faire, le fichier de configuration JSON doit être mis à jour à l’aide de la propriété de métadonnées.

## <a name="json-sample-with-metadata"></a>Exemple JSON avec des métadonnées 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>Extraire le code de programmation 

Lorsque l’événement de programmation se produit, vous pouvez extraire le contenu de code. Le contenu extrait vous permet d’effectuer les opérations suivantes :

- comparer le contenu du fichier de code dans différents événements de programmation ;

- déclencher une alerte en cas de programmation non autorisée ;  

- déclencher un événement pour la réception du fichier de code de programmation.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="Journal des modifications de programmation.":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="Affichez la programmation en cliquant sur le bouton.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="Alerte de programmation PLC non autorisée.":::

Pour ce faire, le fichier de configuration JSON doit être mis à jour à l’aide de la propriété `code_extraction`. 

### <a name="json-configuration-fields"></a>Champs de configuration JSON 

Cette section décrit les champs de configuration JSON. 

- **method**

  Indique la façon dont les fichiers d’événements de programmation sont reçus. 

  ALL (chaque action de programmation entraîne la réception de tous les fichiers de code, même non modifiés).

- **file_type**  

  Indique le type de contenu de code.  

  TEXT (chaque fichier de code contient des informations textuelles).

- **code_data_field**
  
  Indique le champ d’implémentation à utiliser pour fournir le contenu de code.

  FIELD.

- **code_name_field**

  Indique le champ d’implémentation à utiliser pour fournir le nom du fichier de codage.

  FIELD.

- **size_limit**

  Indique la limite de taille de chaque contenu de fichier de codage en octets. Si un fichier de code dépasse la limite définie, il est écarté. Si ce champ n’est pas spécifié, la valeur par défaut est 15 000 000, soit 15 Mo.

  Number.

- **métadonnées**

  Fournit des informations supplémentaires pour un fichier de code.

  Tableau contenant des objets avec deux propriétés :

    - name (String) : indique que la clé de métadonnées XSense prend actuellement en charge uniquement la clé de nom d’utilisateur.
 
    - value (Field) : indique le champ d’implémentation à utiliser pour fournir les données de métadonnées.

## <a name="json-sample-with-programming-code"></a>Exemple JSON avec code de programmation

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>Déclencher des alertes Horizon

Le code de fonction de certains peut indiquer une erreur. Par exemple, si le protocole contrôle un conteneur avec un produit chimique spécifique qui doit toujours être stocké à une température spécifique. Dans ce cas, il peut y avoir un code de fonction indiquant une erreur dans le thermomètre. Par exemple, si le code de la fonction est 25, vous pouvez déclencher une alerte dans la console web, indiquant qu’il y a un problème avec le conteneur. Dans ce cas, vous pouvez définir des alertes de paquets approfondies.

Ajoutez le paramètre **alertes** au `config.json` du plug-in.

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>Champs de configuration JSON

Cette section décrit les champs de configuration JSON. 

| Nom du champ | Description | Valeurs possibles |
|--|--|--|
| **Identifiant** | ID d’alerte unique. Il doit être unique dans ce contexte. | Valeur numérique comprise entre 0 et 10 000. |
| **message** | Informations affichées à l’utilisateur. Ce champ vous permet d’utiliser différents champs. | Utilisez n’importe quel champ de votre protocole ou n’importe quel protocole de couche inférieure. |
| **title** | Titre de l’alerte. |  |
| **expression** | Lorsque vous souhaitez que cette alerte s’affiche. | Utilisez n’importe quel champ numérique trouvé dans les couches inférieures ou la couche active.</br></br> Chaque champ doit être un wrapper avec `{}`, de sorte que le Kit de développement logiciel (SDK) le détecte comme un champ. Les opérateurs logiques pris en charge sont :</br> == : égal</br> <= : inférieur ou égal</br> >= : supérieur ou égal</br> > : supérieur</br> < : inférieur</br> ~= : pas égal |

## <a name="more-about-expressions"></a>Informations supplémentaires sur les expressions

Chaque fois que le Kit de développement logiciel (SDK) Horizon appelle l’expression et qu’il devient *vrai*, une alerte est déclenchée dans le capteur.

Plusieurs expressions peuvent être incluses sous la même alerte. Par exemple,

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

Cette expression valide le code de fonction uniquement lorsque le paquet IPv4 src est 10.10.10.10. Ce qui est une représentation brute de l’adresse IP dans la représentation numérique.

Vous pouvez utiliser `and` ou `or` pour connecter des expressions.

## <a name="json-sample-custom-horizon-alerts"></a>Exemples JSON d’alertes Horizon personnalisées

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>Se connecter à un service d’indexation (ligne de base)

Vous pouvez indexer les valeurs de protocole et les afficher dans des rapports d’exploration de données.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Vue de l’option d’exploration de données.":::

Ces valeurs peuvent être mappées ultérieurement à des textes spécifiques, par exemple, en mappant des nombres en tant que textes ou en ajoutant des informations dans n’importe quel langue.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migration":::

Pour plus d’informations, consultez la section [Créer des fichiers de mappage (JSON)](#create-mapping-files-json).

Vous pouvez également utiliser des valeurs des protocoles précédemment analysés pour extraire des informations supplémentaires.

Par exemple, pour la valeur basée sur TCP, vous pouvez utiliser les valeurs de la couche IPv4. À partir de cette couche, vous pouvez extraire des valeurs telles que la source du paquet et la destination.

Pour ce faire, le fichier de configuration JSON doit être mis à jour à l’aide de la propriété `whitelists`.

## <a name="allow-list-data-mining-fields"></a>Champs de la liste verte (exploration de données)

Les champs Allowlist suivants sont disponibles :

- name : nom utilisé pour l’indexation.

- alert_title : titre unique abrégé expliquant l’événement.

- alert_text : informations supplémentaires.

Vous pouvez ajouter plusieurs listes d’autorisation, ce qui permet une flexibilité totale de l’indexation.

## <a name="json-sample-with-indexing"></a>Exemple JSON avec indexation 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>Extraire les données du microprogramme

Vous pouvez extraire des informations du microprogramme, définir des valeurs d’index et déclencher des alertes de microprogramme pour le protocole de plug-in. Par exemple,

- Extraire le modèle ou la version du microprogramme. Ces informations permettent d’identifier des CVE.

- Déclencher une alerte lors de la détection d’une nouvelle version de microprogramme.

Pour ce faire, le fichier de configuration JSON doit être mis à jour à l’aide de la propriété du microprogramme.

## <a name="firmware-fields"></a>Champs du microprogramme

Cette section décrit les champs de configuration du microprogramme JSON.

- **name**
  
  Indique comment le champ est présenté dans la console du capteur.

- **value**

 Indique le champ d’implémentation à utiliser pour fournir les données. 

- **firmware_index :**

  Choisissez une des options suivantes :  
  - **Modèle** : Le modèle de l’appareil. Active la détection des CVE.
  - **serial** : Le numéro de série de l’appareil. Le numéro de série n’est pas toujours disponible pour tous les protocoles. Cette valeur est unique par appareil.
  - **rack** : affiche l’identificateur du rack si l’appareil fait partie d’un rack.
  - **slot** : identificateur de l’emplacement si l’appareil fait partie d’un rack.  
  - **module_address** : sert à présenter une hiérarchie si le module peut être présenté derrière un autre appareil. Applicable à la place d’une combinaison d’emplacements de rack, qui est une présentation plus simple.  
  - **firmware_version** : indique la version l’appareil. Active la détection des CVE.
  - **alert_text** : texte décrivant les écarts du microprogramme, par exemple, les modifications de version.  
  - **index_by** : indique les champs utilisés pour identifier et indexer l’appareil. Dans l’exemple ci-dessous, l’appareil est identifié par son adresse IP. Certains protocoles permettent d’utiliser un index plus complexe. Par exemple, quand un autre appareil est connecté, si vous savez comment extraire son chemin d’accès interne. Par exemple, l’ID d’unité MODBUS peut être utilisé dans l’index, en tant que combinaison alternative d’adresse IP et d’identificateur d’unité.
  - **firmware_fields** : indique les champs qui sont des champs de métadonnées d’appareil. Dans cet exemple, les champs utilisés sont les suivants : model, revision et name. Chaque protocole peut définir ses propres données de microprogramme.

## <a name="json-sample-with-firmware"></a>Exemple JSON avec microprogramme 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>Extraire les attributs de l’appareil 

Vous pouvez améliorer les informations disponibles dans les rapports sur l’appareil en inventaire et l’exploration de données, ainsi que dans d’autres rapports.

- Nom

- Type

- Fournisseur

- Système d’exploitation

Pour ce faire, le fichier de configuration JSON nécessite une mise à jour à l’aide de la propriété **Properties**. 

Vous pouvez faire cela après avoir écrit le plug-in de base et extrait les champs requis.

## <a name="properties-fields"></a>Champs de propriétés

Cette section décrit les champs de configuration des propriétés JSON. 

**config_file** 

Contient le nom de fichier qui définit le mode de traitement de chaque clé dans les champs `key`. Le fichier de configuration lui-même doit être au format JSON et être inclus dans le dossier du protocole de plug-in.

Chaque clé dans le JSON définit l’ensemble d’actions à effectuer lors de l’extraction de cette clé d’un paquet.

Chaque clé peut contenir les informations suivantes :

- **Packet Data** : propriétés qui doivent être mises à jour en fonction des données extraites du paquet (champ d’implémentation utilisé pour fournir ces données).

- **Static Data** : ensemble prédéfini d’actions de `property-value` à mettre à jour.

Les propriétés qui peuvent être configurées dans ce fichier sont les suivantes : 

- **Name** : nom de l’appareil.

- **Type** : type de l’appareil.

- **Vendor** : fournisseur de l’appareil.

- **Description** : modèle de microprogramme de l’appareil (priorité plus faible que « model »).

- **operatingSystem** : système d’exploitation de l’appareil.

### <a name="fields"></a>Champs

| Champ | Description |
|--|--|
| key | Indique la clé. |
| value | Indique le champ d’implémentation à utiliser pour fournir les données. |
| is_static_key | Indique si le champ `key` est dérivé en tant que valeur du paquet ou s’il s’agit d’une valeur prédéfinie. |

### <a name="working-with-static-keys-only"></a>Utilisation uniquement de clés statiques

Si vous utilisez des clés statiques, vous n’avez pas besoin de configurer le fichier `config.file`. Vous pouvez configurer uniquement le fichier JSON.

## <a name="json-sample-with-properties"></a>Exemple JSON avec des propriétés 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>Créer des fichiers de mappage (JSON)

Vous pouvez personnaliser le texte de sortie du plug-in en fonction des besoins de votre environnement d’entreprise en définissant et en mettant à jour des fichiers de mappage. Vous pouvez facilement implémenter des modifications dans du texte sans modifier ou affecter le code. Chaque fichier peut mapper un ou plusieurs champs.

- Mappage de valeurs de champ à des noms, par exemple, 1:Reset, 2:Start, 3:Stop.

- Mappage de texte pour prendre en charge plusieurs langues.

Vous pouvez définir deux types de fichiers de mappage.

  - [Fichier de mappage simple](#simple-mapping-file).

  - [Fichier de mappage de dépendance](#dependency-mapping-file).

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="ether net":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="Vue des alertes non gérées.":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="Liste des violations de stratégie connues.":::

## <a name="file-naming-and-storage-requirements"></a>Exigences en matière de nommage et de stockage des fichiers 

Vous devez enregistrer les fichiers de mappage dans le dossier metadata.

Le nom du fichier doit correspondre à l’ID du fichier de configuration JSON.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="Exemple de fichier de configuration JSON.":::

## <a name="simple-mapping-file"></a>Fichier de mappage simple 

L’exemple suivant présente un fichier JSON de base sous la forme d’une valeur de clé.

Lorsque vous créez une liste d’autorisation contenant un ou plusieurs champs mappés, la valeur est convertie à partir d’un nombre, d’une chaîne ou de n’importe quel type dans le texte mis en forme présenté dans le fichier de mappage.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>Fichier de mappage de dépendance 

Pour indiquer que le fichier est un fichier de dépendance, ajoutez le mot clé `dependency` à la configuration du mappage.

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

Le fichier contient un mappage entre le champ de dépendance et le champ de fonction. Par exemple, entre la fonction et une sous-fonction. La sous-fonction change en fonction de la fonction fournie.

La liste d’autorisation configurée précédemment ne comporte aucune configuration de dépendance, comme indiqué ci-dessous.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

La dépendance peut être basée sur une valeur ou un champ spécifiques. Dans l’exemple ci-dessous, elle est basée sur un champ. Si vous la basez sur une valeur, définissez la valeur d’extraction à lire à partir du fichier de mappage.

Dans l’exemple ci-dessous, la dépendance se présente comme suit pour la même valeur du champ.

Par exemple, dans la sous-fonction cinq, la signification est modifiée selon la fonction.

  - S’il s’agit d’une fonction de lecture, 5 signifie la lecture de la mémoire.

  - S’il s’agit d’une fonction d’écriture, la même valeur est utilisée pour lire à partir d’un fichier.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>Exemple de fichier

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>Exemple JSON avec mappage

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>Empaqueter, charger et surveiller le plug-in 

Cette section décrit comment

  - empaqueter votre plug-in ;

  - charger votre plug-in ;

  - surveiller et déboguer le plug-in pour évaluer ses performances.

Pour empaqueter le plug-in :

1. Ajoutez l’**artefact** (peut être, library, config.json ou metadata) à un fichier `tar.gz`.

1. Remplacez l’extension de fichier par \<XXX.hdp>, où \<XXX> est le nom du plug-in.

Pour vous connecter à la console Horizon :

1.  Connectez-vous à l’interface de ligne de commande de votre capteur en tant qu’administrateur, CyberX ou utilisateur de support.

2.  Dans le fichier `/var/cyberx/properties/horizon.properties`, définissez la propriété **ui.enabled** sur **true** (`horizon.properties:ui.enabled=true`).

3.  Connectez-vous à la console du capteur.

4.  Sélectionnez l’option **Horizon** dans le menu principal.

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="Sélectionnez l’option Horizon dans le volet latéral gauche.":::

    La console Horizon s’ouvre.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="Vue de la console Horizon et de tous ses plug-ins.":::

## <a name="plugins-pane"></a>Volet des plug-ins

Le volet des plug-ins répertorie les éléments suivants :

  - Plug-ins d’infrastructure : plug-ins d’infrastructure installés par défaut avec Defender pour IoT.

  - Plug-ins d’application : plug-ins d’application installés par défaut avec Defender pour IoT, et autres plug-ins développés par Defender pour IoT ou des développeurs externes.
    
Activez et désactivez les plug-ins chargés à l’aide du bouton bascule.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="Bouton bascule CIP.":::

### <a name="uploading-a-plugin"></a>Chargement d’un plug-in

Un fois votre plug-in créé et empaqueté, vous pouvez le charger sur le capteur Defender pour IoT. Pour obtenir une couverture complète de votre réseau, vous devez charger le plug-in sur chaque capteur de votre organisation.

Pour charger :

1.  Connectez-vous à votre capteur.


2. Sélectionnez **Télécharger**.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="Chargez votre plug-in.":::

3. Accédez à votre plug-in, puis faites-le glisser vers la boîte de dialogue Plug-in. Vérifiez que le préfixe est `.hdp`. Le plug-in est chargé.

## <a name="plugin-status-overview"></a>Vue d’ensemble de l’état du plug-in 

La fenêtre **Vue d’ensemble** de la console Horizon fournit des informations sur le plug-in que vous avez chargé, et vous permet de l’activer ainsi que de la désactiver.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="Vue d’ensemble de la console Horizon.":::

| Champ | Description |
|--|--|
| Application | Nom du plug-in que vous avez chargé. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="Commutateur activé/désactivé."::: | **Activez** ou **désactivez** le plug-in. Defender pour IoT ne gère pas le trafic de protocole défini dans le plug-in lorsque vous désactivez celui-ci. |
| Heure | Heure à laquelle les données ont été analysées pour la dernière fois. Actualisée toutes les 5 secondes. |
| PPS | Nombre de paquets par seconde. |
| Bande passante | Bande passante moyenne détectée au cours des 5 dernières secondes. |
| Malformations | Validations malformées utilisées après que le protocole a été validé positivement. En cas d’échec du traitement des paquets basé sur le protocole, une réponse d’échec est retournée.   <br><br>Cette colonne indique le nombre d’erreurs de malformation au cours des 5 dernières secondes. Pour plus d’informations, consultez [Validations de code mal formé](#malformed-code-validations). |
| Avertissements | Les paquets correspondent à la structure et à la spécification, mais le comportement est inattendu et basé sur la configuration d’avertissement du plug-in. |
| Erreurs | Nombre de paquets dont les validations de protocole de base ont échoué. Valide le fait que le paquet correspond aux définitions de protocole. Le nombre affiché ici indique le nombre d’erreurs détectées au cours des 5 dernières secondes. Pour plus d’informations, consultez [Validations du code de validité](#sanity-code-validations). |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="Icône du moniteur."::: | Passez en revue les détails sur les malformations et les avertissements détectés pour votre plug-in. |

## <a name="plugin-details"></a>Détails du plug-in

Vous pouvez surveiller les performances du plug-in en temps réel en analysant le nombre de *malformations* et d’*avertissements* détectés pour votre plug-in. Une option est disponible pour geler l’écran et l’exporter pour une enquête plus approfondie.

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="Écran du moniteur SNMP.":::

Pour surveiller :

sélectionnez le bouton Surveiller pour votre plug-in dans la Vue d’ensemble.

Étapes suivantes

Configurer votre [API Horizon](references-horizon-api.md)
