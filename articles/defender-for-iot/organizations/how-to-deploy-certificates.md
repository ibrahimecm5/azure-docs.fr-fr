---
title: Déployer des certificats
description: Découvrez comment configurer et déployer des certificats pour Defender pour IoT.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: df2b0484d9f9302443dc37870a6a97de31ded3b1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343702"
---
# <a name="about-certificates"></a>À propos des certificats

Cet article fournit les informations nécessaires à la création et au déploiement de certificats pour Microsoft Defender pour IoT. Un responsable sécurité, infrastructure de clé publique ou un autre responsable de certificat qualifié doit gérer la création et le déploiement des certificats.

Defender pour IoT utilise des certificats SSL/TLS pour sécuriser les communications entre les composants système suivants : 

- Entre les utilisateurs et la console web de l’appliance. 
- Entre les capteurs et une console de gestion locale. 
- Entre une console de gestion et une console de gestion haute disponibilité.
- Avec l’API REST sur le capteur et la console de gestion locale. 

Les administrateurs de Defender pour IoT peuvent télécharger un certificat sur les consoles des capteurs et leur console de gestion locale dans la boîte de dialogue des certificats SSL/TLS.

:::image type="content" source="media/how-to-deploy-certificates/certificate-upload.png" alt-text="Boîte de dialogue de chargement des certificats.":::

## <a name="about-certificate-generation-methods"></a>À propos des méthodes de génération des certificats

Toutes les méthodes de génération des certificats sont prises en charge à l’aide des éléments suivants :  

- Infrastructures à clé privée et d’entreprise (PKI privée) 
- Infrastructures à clé publique (PKI publique) 
- Certificats générés localement sur l’appliance (auto-signé localement) 

> [!Important]
> Il n’est pas recommandé d’utiliser des certificats auto-signés localement. Cette connexion n’est pas sécurisée et doit être utilisée uniquement pour les environnements de test. Étant donné que le propriétaire du certificat ne peut pas être validé et que la sécurité de votre système ne peut pas être maintenue, les certificats auto-signés ne doivent jamais être utilisés pour les réseaux de production.

## <a name="about-certificate-validation"></a>À propos de la validation du certificat SSL

En plus de sécuriser la communication entre les composants du système, les utilisateurs peuvent également effectuer la validation des certificats.  

La validation est évaluée par rapport aux points suivants :

- Une liste de révocation des certificats (CRL)
- La date d’expiration du certificat.  
- La chaîne d’approbation du certificat.

La validation est effectuée deux fois :

1. Lors du chargement du certificat sur les capteurs et les consoles de gestion locales. Si la validation échoue, le certificat ne peut pas être chargé.
1. Lors du lancement d’une communication chiffrée entre :

    - Les composants du système Defender pour IoT, par exemple, un capteur et une console de gestion locale.

    - Defender pour IoT et certains serveurs tiers définis dans les règles de transfert.  Consultez [À propos des informations d’alerte transférées](how-to-forward-alert-information-to-partners.md#about-forwarded-alert-information) pour plus d’informations.  

Si la validation échoue, la communication entre les composants concernés est interrompue et une erreur de validation s’affiche sur la console.

## <a name="about-certificate-upload-to-defender-for-iot"></a>À propos du chargement de certificats sur Defender pour IoT

Après l’installation du capteur et de la console de gestion locale, un certificat auto-signé local est généré et utilisé pour accéder au capteur et à l’application web de la console de gestion locale.

Lorsqu’ils se connectent au capteur et à la console de gestion locale pour la première fois, les administrateurs sont invités à télécharger un certificat SSL/TLS. L’utilisation de certificats SSL/TLS est fortement recommandée.

Si le certificat n’est pas créé correctement par le responsable des certificats ou s’il y a des problèmes de connexion, le certificat ne peut pas être chargé et les utilisateurs sont obligés d’utiliser un certificat signé localement.  

L’option permettant de valider le certificat chargé et les certificats tiers est automatiquement activée, mais peut être désactivée. Lorsqu’elle est désactivée, les communications chiffrées entre les composants se poursuivent, même si un certificat n’est pas valide.

## <a name="certificate-deployment-tasks"></a>Tâches de déploiement de certificats

Cette section décrit les étapes à suivre pour s’assurer que le déploiement des certificats s’exécute correctement.

**Pour déployer les certificats, vérifiez les points suivants :**

- Un spécialiste sécurité, infrastructure à clé publique ou un spécialiste des certificats crée ou supervise la création des certificats. 
- Vous créez un certificat unique pour chaque capteur, chaque console de gestion et chaque machine à haute disponibilité.
- Vous respectez les exigences de création des certificats. Consultez  [Exigences de création des certificats](#certificate-creation-requirements).
- Les administrateurs qui se connectent à chaque capteur Defender pour IoT, ainsi qu’à la console de gestion locale et à la machine haute disponibilité peuvent accéder au certificat.

## <a name="certificate-creation-requirements"></a>Exigences de création des certificats

Cette section traite de la création de certificats, notamment :

- [Conditions d’accès au port pour la validation de certificat](#port-access-requirements-for-certificate-validation)

- [Exigences relatives aux types de données](#file-type-requirements)

- [Exigences relatives au fichier de clé](#key-file-requirements)

- [Exigences relatives au fichier de chaîne de certificats (si vous utilisez .pem)](#certificate-chain-file-requirements-if-pem-is-used)

### <a name="port-access-requirements-for-certificate-validation"></a>Conditions d’accès au port pour la validation de certificat

Si vous utilisez la validation de certificat, vérifiez que l’accès au port 80 est disponible.

La validation du certificat est évaluée par rapport à une liste de révocation de certificats et à la date d’expiration du certificat. Ceci implique que l’appliance doit être en mesure d’établir la connexion au serveur de liste de révocation de certificats défini par le certificat. Par défaut, le certificat fait référence à l’URL CRL sur le port HTTP 80. 

Certaines stratégies de sécurité de l’organisation peuvent bloquer l’accès à ce port. Si votre organisation n’a pas accès au port 80, vous pouvez : 

1. Définir une autre URL et un port spécifique dans le certificat.

    - L’URL doit être définie en tant que http:// plutôt que https://.

    - Vérifiez que le serveur CRL de destination peut écouter sur le port que vous avez défini. 

1. Utiliser un serveur proxy qui accédera à la liste de révocation de certificats sur le port 80.

### <a name="file-type-requirements"></a>Exigences relatives aux types de données

Defender pour IoT exige que chaque certificat signé par une autorité de certification contienne un fichier .key et un fichier .crt. Ces fichiers sont chargés sur le capteur et la console de gestion locale après la connexion. Certaines organisations peuvent nécessiter un fichier .pem. Defender pour IoT ne nécessite pas ce type de fichier.

**.crt : fichier de conteneur de certificat**

Fichier au format .pem ou .der avec une autre extension. Le fichier est reconnu par l’Explorateur Windows comme un certificat. Le fichier .pem n’est pas reconnu par l’Explorateur Windows.

**.key : fichier de clé privée**

Un fichier de clé est au même format qu’un fichier PEM, mais son extension est différente.

**.pem : fichier de conteneur de certificat (facultatif)**

PEM est un fichier texte qui contient l’encodage en base64 du texte du certificat, un en-tête et un pied de page en texte brut qui marquent le début et la fin du certificat.

Vous devrez peut-être convertir les types de fichiers existants en types de fichiers compatibles. Pour plus de détails, consultez [Convertir des fichiers existants en fichiers pris en charge](#convert-existing-files-to-supported-files).

### <a name="certificate-file-parameter-requirements"></a>Exigences relatives aux paramètres du fichier de certificat

Vérifiez que vous respectez les exigences suivantes avant de créer un certificat :

- [Exigences relatives au fichier CRT](#crt-file-requirements)
- [Exigences relatives au fichier de clé](#key-file-requirements)
- [Exigences relatives au fichier de chaîne de certificats (si vous utilisez .pem)](#certificate-chain-file-requirements-if-pem-is-used)

### <a name="crt-file-requirements"></a>Exigences relatives au fichier CRT

Cette section traite des exigences relatives aux champs .CRT.

- Algorithme de signature = SHA256RSA 
- Algorithme de hachage de signature = SHA256 
- Valide à partir du = date passée valide 
- Valide jusqu’au = date future valide 
- Clé publique = RSA 2048 bits (minimum) ou 4096 bits 
- Point de distribution de liste de révocation de certificats = URL vers le fichier .crl 
- Subject CN (nom commun) = nom de domaine de l’appliance, par exemple Sensor.contoso.com ou *.contoso.com.  
- Pays (C) de l’objet = défini, par exemple, US 
- Unité d’organisation (OU) de l’objet = définie, par exemple, Contoso Labs 
- Organisation (O) de l’objet = définie, par exemple, Contoso Inc 

Les certificats avec d’autres paramètres peuvent fonctionner, mais Microsoft ne les prend pas en charge.  

### <a name="key-file-requirements"></a>Exigences relatives au fichier de clé

Utilisez le RSA 2048 bits ou 4096 bits.

Lorsque vous utilisez une longueur de clé de 4096 bits, la négociation SSL au début de chaque connexion est plus lente. En outre, l’utilisation du processeur augmente pendant l’établissement des liaisons.

### <a name="certificate-chain-file-requirements-if-pem-is-used"></a>Exigences relatives au fichier de chaîne de certificats (si vous utilisez .pem)

Le fichier. pem contient les certificats de toutes les autorités de certification de la chaîne d’approbation qui conduit à votre certificat.  

Les attributs de conteneur sont pris en charge dans le fichier de chaîne de certificats.

## <a name="create-certificates"></a>Créer des certificats

Utilisez une plateforme de gestion des certificats pour créer un certificat, par exemple, une plateforme de gestion d’infrastructure à clé publique automatisée. Vérifiez que les certificats répondent aux exigences du fichier de certificat. Consultez Tester les certificats pour plus d’informations sur le test des fichiers que vous créez.

Si vous n’effectuez pas la validation des certificats, supprimez la référence de l’URL de la liste de révocation des certificats dans le certificat. Pour plus d’informations sur ce paramètre, consultez [Exigences relatives aux fichiers CRT](#crt-file-requirements).

Consultez un responsable de sécurité, d’infrastructure à clé publique ou tout autre responsable certificat qualifié si vous n’avez pas d’application capable de créer automatiquement des certificats.

Vous pouvez [tester les certificats que vous créez](#test-certificates-you-create).  

Vous pouvez également convertir des fichiers de certificat existants si vous ne souhaitez pas en créer de nouveaux. Pour plus de détails, consultez [Convertir des fichiers existants en fichiers pris en charge](#convert-existing-files-to-supported-files).

### <a name="sample-certificate"></a>Exemple de certificat

Vous pouvez comparer votre certificat à l’exemple de certificat ci-dessous. Vérifiez que les mêmes champs se ferment et que l’ordre des champs est le même.

:::image type="content" source="media/how-to-deploy-certificates/sample-certificate.png" alt-text="sample-certificate.":::

## <a name="test-certificates-you-create"></a>Tester les certificats que vous créez

Vous pouvez tester les certificats avant de les déployer sur vos capteurs et sur les consoles de gestion locales. Si vous souhaitez vérifier les informations dans le fichier .csr ou le fichier de clé privée du certificat, utilisez les commandes suivantes :

| **Test** | **Commande CLI** |
|--|--|
| Vérifier une demande de signature de certificat (CSR) | openssl req -text -noout -verify -in CSR.csr |
| Vérifier une clé privée  | openssl rsa -in privateKey.key -check  |
| Vérifier un certificat  | openssl x509 -in certificate.crt -text -noout |

Si ces tests échouent, passez en revue les [Exigences relatives aux paramètres du fichier de certificat](#certificate-file-parameter-requirements) pour vérifier que les paramètres du fichier sont corrects ou consultez votre responsable des certificats.

## <a name="convert-existing-files-to-supported-files"></a>Convertir des fichiers existants en fichiers pris en charge 

Cette section décrit comment convertir des fichiers de certificats existants en formats pris en charge.

|**Description** | **Commande CLI** |
|--|--|
| Convertir un fichier .crt en fichier .pem   | `openssl x509 -inform PEM -in <full path>/<pem-file-name>.pem -out <fullpath>/<crt-file-name>.crt`  | 
| Convertir un fichier .pem en fichier .crt   | `openssl x509 -inform PEM -in <full path>/<pem-file-name>.pem -out <fullpath>/<crt-file-name>.crt` |  
| Convertir un fichier PKCS#12 (.pfx .p12) contenant une clé privée et des certificats en .pem   | `openssl pkcs12 -in keyStore.pfx -out keyStore.pem -nodes`. Vous pouvez ajouter -nocerts pour générer la privée uniquement ou ajouter -nokeys pour générer des certificats uniquement.  |  

## <a name="troubleshooting"></a>Résolution des problèmes  

Cette section traite des différents problèmes qui peuvent se produire lors du chargement et de la validation des certificats, ainsi que les étapes à suivre pour résoudre les problèmes.

### <a name="troubleshoot-ca-certificate-upload"></a>Résoudre les problèmes de chargement de certificat  

Les administrateurs qui essaient de se connecter au capteur ou à la console de gestion locale pour la première fois ne peuvent pas charger le certificat signé par une autorité de certification si ce certificat n’est pas créé correctement ou n’est pas valide. Si le chargement du certificat échoue, un ou plusieurs messages d’erreur s’affichent :

| **Erreur de validation du certificat** | **Recommandation** |
|--|--|
| La phrase secrète ne correspond pas à la clé | Vérifiez que vous avez tapé la bonne phrase secrète. Si le problème persiste, essayez de recréer le certificat à l’aide de la phrase secrète correcte. |
| Impossible de valider la chaîne de confiance. Le certificat et l’autorité de certification racine fournis ne correspondent pas.  | Assurez-vous que le fichier .pem est en corrélation avec le fichier .crt. Si le problème persiste, essayez de recréer le certificat à l’aide de la chaîne d’approbation correcte (définie par le fichier .pem). |
| Ce certificat SSL a expiré et n’est pas considéré comme valide.  | Créez un certificat avec des dates valides.|
| Ce certificat SSL a expiré et n’est pas considéré comme valide.  | Créez un certificat avec des dates valides.|
|Ce certificat a été révoqué par la liste de révocation de certificats et ne peut pas être approuvé pour une connexion sécurisée | Créez un certificat non révoqué. |
|L’emplacement de la liste de révocation de certificats n’est pas accessible. Vérifiez que l’URL est accessible à partir de cette appliance. | Assurez-vous que la configuration de votre réseau permet à l’appliance de contacter le serveur de liste de révocation de certificats défini dans le certificat. Vous pouvez utiliser un serveur proxy en cas de limitations lors de l’établissement d’une connexion directe.  
|Échec de la validation du certificat  | Ceci indique une erreur générale dans l’appliance. Contactez le [support Microsoft](https://support.microsoft.com/supportforbusiness/productselection?sapId=82c8f35-1b8e-f274-ec11-c6efdd6dd099).|

### <a name="troubleshoot-file-conversions"></a>Résoudre les problèmes de conversion de fichiers  

La conversion de votre fichier peut ne pas créer de certificat valide. Par exemple, la structure du fichier peut être incorrecte.

Si la conversion échoue :  

- Utilisez les commandes de conversion décrites dans [Convertir des fichiers existants en fichiers pris en charge](#convert-existing-files-to-supported-files).
- Assurez-vous que les paramètres du fichier sont exacts. Pour plus de détails, consultez [Exigences relatives aux types de fichier](#file-type-requirements) et [Exigences relatives aux paramètres de fichier de certificat](#certificate-file-parameter-requirements).  
- Consultez votre responsable des certificats.
