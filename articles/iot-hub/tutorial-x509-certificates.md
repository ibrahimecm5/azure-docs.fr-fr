---
title: Tutoriel - Comprendre les certificats à clé publique X.509 pour Azure IoT Hub | Microsoft Docs
description: Tutoriel - Comprendre les certificats à clé publique X.509 pour Azure IoT Hub
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 718ed8c89fbd5bccaff94b41e80b9b462faba1ee
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555657"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Tutoriel : Compréhension des certificats à clé publique X.509

Les certificats X.509 sont des documents numériques qui représentent un utilisateur, un ordinateur, un service ou un appareil. Ils sont émis par une autorité de certification, une autorité de certification secondaire ou une autorité d’inscription, et contiennent la clé publique du sujet du certificat. Ils ne contiennent pas la clé privée du sujet, qui doit être stockée de manière sécurisée. Les certificats à clé publique sont documentés par la spécification [RFC 5280](https://tools.ietf.org/html/rfc5280). Ils sont signés numériquement et, en général, contiennent les informations suivantes :

* Informations sur le sujet du certificat
* Clé publique correspondant à la clé privée du sujet
* Informations sur l’autorité de certification émettrice
* Algorithmes de chiffrement et/ou de signature numérique pris en charge
* Informations pour déterminer l’état de révocation et de validité du certificat

## <a name="certificate-fields"></a>Champs Certificat

Au fil du temps, trois versions de certificat se sont succédé. Chaque version ajoute des champs à celle qui précède. La version 3 est la version actuelle et contient les champs des versions 1 et 2, en plus des champs qui lui sont propres. La version 1 définissait les champs suivants :

* **Version** : valeur (1, 2 ou 3) qui identifie le numéro de version du certificat
* **Serial Number** (Numéro de série) : numéro unique pour chaque certificat émis par une autorité de certification
* **CA Signature Algorithm** (Algorithme de signature de l’autorité de certification) : nom de l’algorithme utilisé par l’autorité de certification pour signer le contenu du certificat
* **Issuer Name** (Nom de l’émetteur) : nom unique de l’autorité de certification émettrice du certificat
* **Validity Period** (Période de validité) : période pendant laquelle le certificat est considéré comme valide
* **Subject Name** (Nom du sujet) : nom de l’entité représentée par le certificat
* **Subject Public Key Info** (Informations sur la clé publique du sujet) : clé publique appartenant au sujet du certificat

La version 2 a ajouté les champs suivants contenant des informations sur l’émetteur du certificat. Toutefois, ces champs sont rarement utilisés.

* **Issuer Unique ID** (ID unique de l’émetteur) : identificateur unique de l’autorité de certification émettrice, tel que défini par cette dernière
* **Subject Unique ID** (ID unique du sujet) : identificateur unique du sujet du certificat tel que défini par l’autorité de certification émettrice

Les certificats version 3 ont ajouté les extensions suivantes :

* **Authority Key Identifier** (Identificateur de clé d’autorité) : Deux valeurs sont possibles :
  * Le sujet de l’autorité de certification et le numéro de série du certificat de l’autorité de certification qui a émis ce certificat
  * Code de hachage de la clé publique de l’autorité de certification qui a émis ce certificat
* **Subject Key Identifier** (Identificateur de la clé du sujet) : code de hachage de la clé publique du certificat actuel
* **Key Usage** (Utilisation de la clé) : définit le service pour lequel un certificat peut être utilisé. Il peut s’agir de l’une ou plusieurs des valeurs suivantes :
  * **Signature numérique**
  * **Non-répudiation**
  * **Chiffrement de la clé**
  * **Data Encipherment** (Chiffrement des données)
  * **Key Agreement** (Accord d’échange de clé)
  * **Key Cert Sign** (Signature de certificat de clé)
  * **CRL Sign** (Signature de liste de révocation de certificats)
  * **Encipher Only** (Chiffrer uniquement)
  * **Decipher Only** (Déchiffrer uniquement)
* **Private Key Usage Period** (Période d’utilisation de la clé privée) : période de validité de la partie clé privée d’une paire de clés
* **Certificate Policies** (Stratégies de certificat) : stratégies utilisées pour valider le sujet du certificat
* **Policy Mappings** (Mappages de stratégie) : mappe une stratégie dans une organisation à une stratégie dans une autre
* **Subject Alternative Name** (Autre nom du sujet) : liste des noms de remplacement pour le sujet
* **Issuer Alternative Name** (Autre nom de l’émetteur) : liste des noms de remplacement pour l’autorité de certification émettrice
* **Subject Dir Attribute** (Attribut d’annuaire du sujet) : attributs d’un annuaire X.500 ou LDAP
* **Basic Constraints** (Contraintes de base) : permet au certificat d’indiquer s’il est délivré à une autorité de certification ou à un utilisateur, un ordinateur, un appareil ou un service. Cette extension comprend également une contrainte de longueur de chemin qui limite le nombre d’autorités de certification secondaires qui peuvent exister.
* **Name Constraints** (Contraintes de nom) : désigne les espaces de noms autorisés dans un certificat émis par une autorité de certification
* **Policy Constraints** (Contraintes de stratégie) : peuvent être utilisées pour interdire les mappages de stratégie entre autorités de certification
* **Extended Key Usage** (Utilisation étendue de la clé) : indique comment la clé publique d’un certificat peut être utilisée au-delà des finalités identifiées dans l’extension **Key Usage**.
* **CRL Distribution Points** (Points de distribution de liste de révocation de certificats) : contient une ou plusieurs URL où la liste de révocation de certificats de base est publiée.
* **Inhibit anyPolicy** (Inhiber toute stratégie) : empêche l’utilisation de l’OID **All Issuance Policies** (Toutes les stratégies d’émission) (2.5.29.32.0) dans les certificats d’autorité de certification secondaires.
* **Freshest CRL** (Liste de révocation de certificats la plus récente) : contient une ou plusieurs URL où la liste de révocation de certificats delta de l’autorité de certification émettrice est publiée
* **Authority Information Access** (Accès aux informations de l’autorité) : contient une ou plusieurs URL où le certificat de l’autorité de certification émettrice est publié
* **Subject Information Access** (Accès aux informations du sujet) : contient des informations sur la façon de récupérer des détails supplémentaires pour un sujet de certificat

## <a name="certificate-formats"></a>Formats de certificat

Les certificats peuvent être enregistrés dans différents formats. L’authentification Azure IoT Hub utilise généralement les formats PEM et PFX.

### <a name="binary-certificate"></a>Certificat binaire

Il contient un certificat binaire à l’état brut utilisant l’encodage DER ASN.1.

### <a name="ascii-pem-format"></a>Format PEM ASCII

Un certificat PEM (extension .pem) contient un certificat encodé en base64 qui commence par -----BEGIN CERTIFICATE----- et se termine par -----END CERTIFICATE-----. Le format PEM est très courant et est demandé par IoT Hub lors du chargement de certains certificats.

### <a name="ascii-pem-key"></a>Clé ASCII (PEM)

Contient une clé DER encodée en base64 contenant éventuellement des métadonnées supplémentaires sur l’algorithme utilisé pour la protection par mot de passe.

### <a name="pkcs7-certificate"></a>Certificat PKCS#7

Format conçu pour le transport des données signées ou chiffrées. Il est défini par la spécification [RFC 2315](https://tools.ietf.org/html/rfc2315). Il peut inclure la totalité de la chaîne de certificats.

### <a name="pkcs8-key"></a>Clé PKCS#8

Format d’un magasin de clés privées défini par la spécification [RFC 5208](https://tools.ietf.org/html/rfc5208).

### <a name="pkcs12-key-and-certificate"></a>Clé et certificat PKCS#12

Format complexe qui peut stocker et protéger une clé et la totalité de la chaîne de certificats. Il est couramment utilisé avec une extension .pfx. PKCS#12 est synonyme du format PFX.

## <a name="for-more-information"></a>Informations supplémentaires

Pour plus d'informations, voir les rubriques suivantes :

* [Guide pour profane du jargon des certificats X.509](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [Informations conceptuelles sur les certificats de l’autorité de certification X.509 dans l’industrie IoT](./iot-hub-x509ca-concept.md)

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez générer des certificats de test que vous pouvez utiliser pour authentifier des appareils auprès de votre hub IoT, consultez les rubriques suivantes :

* [Utilisation de scripts fournis par Microsoft pour créer des certificats de test](tutorial-x509-scripts.md)
* [Utilisation d’OpenSSL pour créer des certificats de test](tutorial-x509-openssl.md)
* [Utilisation d’OpenSSL pour créer des certificats de test auto-signés](tutorial-x509-self-sign.md)

Si vous avez un certificat d’autorité de certification ou un certificat d’autorité de certification secondaire et que vous souhaitez le charger sur votre hub IoT et prouver que vous en êtes le propriétaire, consultez [Prouver la possession d’un certificat d’autorité de certification](tutorial-x509-prove-possession.md).