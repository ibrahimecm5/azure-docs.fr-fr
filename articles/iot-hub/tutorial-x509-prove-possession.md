---
title: "Tutoriel : Prouver que vous êtes propriétaire d'un certificat d'autorité de certification dans Azure IoT Hub | Microsoft Docs"
description: "Tutoriel : Prouver que vous êtes propriétaire d'un certificat d'autorité de certification dans Azure IoT Hub"
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 06/25/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 79b55e938d832602dca87af8ee366cd44aa44a91
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547162"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Tutoriel : Prouver que vous êtes propriétaire d'un certificat d'autorité de certification

Après avoir chargé votre certificat d'autorité de certification (AC) racine ou votre certificat d'AC subordonné sur votre instance d'IoT Hub, vous pouvez le configurer pour vérifier automatiquement ou manuellement vous êtes propriétaire du certificat.

## <a name="verify-certificate-automatically"></a>Vérifier le certificat automatiquement 

1. Sur le portail Azure, accédez à votre instance d'IoT Hub et sélectionnez **Paramètres > Certificats**.

2. Sélectionnez **Ajouter** pour ajouter un nouveau certificat d'AC.

3. Entrez un nom d'affichage dans le champ **Nom du certificat**, puis sélectionnez le certificat PEM à ajouter.

4. Pour vérifier automatiquement le certificat, cochez la case en regard de **Définir l’état du certificat sur vérifié lors du chargement**.

  :::image type="content" source="media/tutorial-x509-prove-possession/skip-pop.png" alt-text="Capture d’écran montrant où la case à cocher pour ignorer la preuve de possession est":::

5. Sélectionnez **Enregistrer**.  Votre certificat apparaît dans la section des certificats avec l'état **Vérifié**.

## <a name="verify-certificate-manually-after-upload"></a>Vérifier manuellement le certificat après le chargement

1. Si vous n’avez pas choisi de vérifier automatiquement le certificat pendant le téléchargement, votre certificat est affiché dans la liste des certificats avec l’état **Non vérifié**. 

2. Sélectionnez le certificat pour afficher la boîte de dialogue **Détails du certificat**.

3. Sélectionnez **Générer un code de vérification** dans la boîte de dialogue.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Certificate details dialog}":::

4. Copiez le code de vérification dans le Presse-papiers. Vous devez définir le code de vérification en tant qu'objet du certificat. Par exemple, si le code de vérification est 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3, ajoutez-le en tant qu'objet de votre certificat, comme indiqué à l'étape suivante.

5. Un certificat de vérification peut être généré de trois façons :

    * Si vous utilisez le script PowerShell fourni par Microsoft, exécutez `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` pour créer un certificat nommé `VerifyCert4.cer`. Pour plus d'informations, consultez [Utilisation des scripts fournis par Microsoft](tutorial-x509-scripts.md).

    * Si vous utilisez le script Bash fourni par Microsoft, exécutez `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` pour créer un certificat nommé `verification-code.cert.pem`. Pour plus d'informations, consultez [Utilisation des scripts fournis par Microsoft](tutorial-x509-scripts.md).

    * Si vous utilisez OpenSSL pour générer vos certificats, vous devez d'abord générer une clé privée, puis une demande de signature de certificat (CSR) :

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      Créez ensuite un certificat à l'aide du fichier de configuration de l'AC racine (voir ci-dessous), ou du fichier de configuration de l'AC subordonnée et de la demande de signature de certificat.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Pour plus d'informations, consultez [Utilisation d'OpenSSL pour créer des certificats de test](tutorial-x509-openssl.md).

6. Sélectionnez le nouveau certificat dans l'affichage **Détails du certificat**.

7. Une fois le certificat chargé, sélectionnez **Vérifier**. L'état du certificat d'AC doit passer à **Vérifié**.
