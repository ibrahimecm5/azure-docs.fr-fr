---
title: API Horizon
description: Ce guide décrit les méthodes Horizon couramment utilisées.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: db3fd241593f2d5c3e43485bcca4790a73f975fd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331316"
---
# <a name="horizon-api"></a>API Horizon

Ce guide décrit les méthodes Horizon couramment utilisées.

## <a name="getting-more-information"></a>Obtenir des informations complémentaires

Les API Defender pour IoT sont régies par les [conditions d’utilisation et la licence d’API Microsoft](/legal/microsoft-apis/terms-of-use).

Pour plus d’informations sur l’utilisation d’Horizon et de la plateforme Defender pour IoT, consultez les informations suivantes :

- Pour le Kit de développement logiciel (SDK) Horizon Open Development Environment (ODE), contactez votre représentant Defender pour IoT.

- Pour obtenir des informations de support et de dépannage, contactez <support@cyberx-labs.com>.

- Pour accéder au guide de l’utilisateur Defender pour IoT à partir de la console Defender pour IoT, sélectionnez :::image type="icon" source="media/references-horizon-api/profile.png":::, puis **Télécharger le guide de l’utilisateur**.

## `horizon::protocol::BaseParser`

Résumé pour tous les plug-ins. Cela regroupe deux méthodes :

- Pour le traitement des filtres de plug-in définis au-dessus. De cette manière, Horizon sait comment communiquer avec l’analyseur.
- Pour le traitement des données réelles.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

La première fonction appelée pour votre plug-in crée une instance de l’analyseur pour qu’Horizon le reconnaisse et l’enregistre.

### <a name="parameters"></a>Paramètres

Aucun.

### <a name="return-value"></a>Valeur renvoyée

shared_ptr à votre instance d’analyseur.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

Cette fonction sera appelée pour chaque plug-in inscrit ci-dessus.

Dans la plupart des cas, elle sera vide. Levez une exception pour qu’Horizon sache qu’une erreur s’est produite.

### <a name="parameters"></a>Paramètres

- Carte contenant la structure de dissect_as, telle que définie dans le fichier config.json d’un autre plug-in qui veut être inscrit.

### <a name="return-value"></a>Valeur retournée

Tableau d’uint64_t, qui correspond à l’inscription traitée dans un genre d’uint64_t. Cela signifie que dans la carte, vous disposez d’une liste de ports dont les valeurs correspondront aux uin64_t.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

Fonction principale. Plus précisément, la logique du plug-in, chaque fois qu’un nouveau paquet parvient à votre analyseur. Cette fonction sera appelée, tout ce qui est lié au traitement des paquets doit être effectué ici.

### <a name="considerations"></a>Considérations

Votre plug-in doit être thread-safe, car cette fonction peut être appelée à partir de différents threads. Une bonne approche consiste à tout définir sur la pile.

### <a name="parameters"></a>Paramètres

- Unité de contrôle du Kit de développement logiciel (SDK) responsable du stockage des données et de la création d’objets associés au SDK, notamment ILayer et les champs.
- Assistance pour la lecture des données du paquet brut. Elle est déjà configurée avec l’ordre d’octet que vous avez défini dans le fichier config.json.

### <a name="return-value"></a>Valeur retournée

Résultat du traitement. Le résultat peut être *Succès*, *Malformé* ou *Intégrité*.

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

Marque le traitement comme un échec d’assainissement, ce qui signifie que le paquet n’est pas reconnu par le protocole actuel et qu’Horizon doit le transmettre à un autre analyseur, s’il est inscrit sur les mêmes filtres.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

Constructeur

### <a name="parameters"></a>Paramètres

- Définit le code d’erreur utilisé par Horizon pour l’enregistrement, comme défini dans le fichier config.json.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

Résultat malformé, dans la mesure où nous avons déjà reconnu le paquet comme étant notre protocole, mais une erreur de validation s’est produite (les bits réservés sont activés ou un champ est manquant).

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

Constructeur

### <a name="parameters"></a>Paramètres

- Code d’erreur, tel que défini dans le fichier config.json.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

Indique à Horizon que le traitement a réussi. En cas de réussite, le paquet a été accepté, les données nous appartiennent et toutes les données ont été extraites.

## `horizon::protocol::SuccessResult()`

Constructeur. A créé un résultat de base réussi. Cela signifie que nous ne connaissons pas la direction ou les autres métadonnées du paquet.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

Constructeur.

### <a name="parameters"></a>Paramètres

- Direction du paquet, si elle a été identifiée. Les valeurs peuvent être *REQUEST* ou *RESPONSE*.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

Constructeur.

### <a name="parameters"></a>Paramètres

- La direction du paquet, si elle a été identifiée, peut être *REQUEST* ou *RESPONSE*.
- Avertissements. Ces événements ne seront pas en échec, mais Horizon sera averti.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

Constructeur.

### <a name="parameters"></a>Paramètres

- Avertissements. Ces événements ne seront pas en échec, mais Horizon sera averti.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

Convertit une référence basée sur une chaîne à un nom de champ (par exemple, function_code) en HorizonID.

### <a name="parameters"></a>Paramètres

- Chaîne à convertir.

### <a name="return-value"></a>Valeur retournée

- HorizonID créé à partir de la chaîne.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

Crée une couche de sorte qu’Horizon sache que le plug-in souhaite stocker des données. Il s’agit de l’unité de stockage de base que vous devez utiliser.

### <a name="return-value"></a>Valeur retournée

Référence à une couche créée, à laquelle vous pouvez ajouter des données.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

Obtient l’objet de gestion de champs, qui est chargé de créer des champs sur différents objets, par exemple, sur ILayer.

### <a name="return-value"></a>Valeur retournée

Référence au manager.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

Crée un nouveau champ numérique de 64 bits sur la couche avec l’ID demandé.

### <a name="parameters"></a>Paramètres

- Couche que vous avez créée précédemment.
- HorizonID créé par la macro **HORIZON_FIELD**.
- Valeur brute que vous souhaitez stocker.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

Crée un nouveau champ de chaîne sur la couche avec l’ID demandé. La mémoire sera déplacée. Nous vous recommandons donc de rester vigilant. Vous ne pourrez pas réutiliser cette valeur.

### <a name="parameters"></a>Paramètres

- Couche que vous avez créée précédemment.
- HorizonID créé par la macro **HORIZON_FIELD**.
- Valeur brute que vous souhaitez stocker.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

Crée un nouveau champ de valeur brute (tableau d’octets) sur la couche avec l’ID demandé. La mémoire sera déplacée. Nous vous recommandons donc de rester vigilant, car vous ne pourrez pas réutiliser cette valeur.

### <a name="parameters"></a>Paramètres

- Couche que vous avez créée précédemment.
- HorizonID créé par la macro **HORIZON_FIELD**.
- Valeur brute que vous souhaitez stocker.

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

Crée un champ de valeur de tableau (tableau) sur la couche du type spécifié avec l’ID demandé.

### <a name="parameters"></a>Paramètres

- Couche que vous avez créée précédemment.
- HorizonID créé par la macro **HORIZON_FIELD**.
- Type des valeurs qui seront stockées dans le tableau.

### <a name="return-value"></a>Valeur retournée

Référence à un tableau auquel vous devez ajouter des valeurs.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

Ajoute une nouvelle valeur entière au tableau créé précédemment.

### <a name="parameters"></a>Paramètres

- Tableau créé précédemment.
- Valeur brute à stocker dans le tableau.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

Ajoute une nouvelle valeur de chaîne au tableau créé précédemment. La mémoire sera déplacée. Nous vous recommandons donc de rester vigilant, car vous ne pourrez pas réutiliser cette valeur.

### <a name="parameters"></a>Paramètres

- Tableau créé précédemment.
- Valeur brute à stocker dans le tableau.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

Ajoute une nouvelle valeur brute au tableau créé précédemment. La mémoire sera déplacée. Nous vous recommandons donc de rester vigilant, car vous ne pourrez pas réutiliser cette valeur.

### <a name="parameters"></a>Paramètres

- Tableau créé précédemment.
- Valeur brute à stocker dans le tableau.

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

Vérifie que la mémoire tampon contient au moins X octets.

### <a name="parameters"></a>Paramètres

Nombre d’octets qui doivent exister.

### <a name="return-value"></a>Valeur retournée

Vrai si la mémoire tampon contient au moins X octets. Sinon, c’est `False`.

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

Lit la valeur uint8 (1 octet) à partir de la mémoire tampon, en fonction de l’ordre d’octet.

### <a name="return-value"></a>Valeur retournée

Valeur lue à partir de la mémoire tampon.

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

Lit la valeur uint16 (2 octets) à partir de la mémoire tampon, en fonction de l’ordre d’octet.

### <a name="return-value"></a>Valeur retournée

Valeur lue à partir de la mémoire tampon.

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

Lit la valeur uint32 (4 octets) à partir de la mémoire tampon, en fonction de l’ordre d’octet.

### <a name="return-value"></a>Valeur retournée

Valeur lue à partir de la mémoire tampon.

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

Lit la valeur uint64 (8 octets) à partir de la mémoire tampon, en fonction de l’ordre d’octet.

### <a name="return-value"></a>Valeur retournée

Valeur lue à partir de la mémoire tampon.

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

Les lectures dans la mémoire pré-allouée, d’une taille spécifiée, copient réellement les données dans la région de votre mémoire.

### <a name="parameters"></a>Paramètres 

- Région de la mémoire dans laquelle copier les données.
- Taille de la région de mémoire ; ce paramètre définit également le nombre d’octets qui seront copiés.

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

Lit une chaîne à partir de la mémoire tampon.

### <a name="parameters"></a>Paramètres 

- Nombre d'octets qui doivent être lus.

### <a name="return-value"></a>Valeur retournée

Référence à la région de mémoire de la chaîne.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

Indique le nombre d’octets restants dans la mémoire tampon.

### <a name="return-value"></a>Valeur retournée

Taille restante de la mémoire tampon.

## `void horizon::general::IDataBuffer::skip(size_t)`

Ignore X octets dans la mémoire tampon.

### <a name="parameters"></a>Paramètres

- Nombre d'octets à ignorer.
