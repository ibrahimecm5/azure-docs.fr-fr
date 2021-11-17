---
title: Tutoriel - Utiliser des scripts Microsoft afin de créer des certificats de test X.509 pour Azure IoT Hub | Microsoft Docs
description: Tutoriel - Utiliser des scripts personnalisés afin de créer des certificats d'AC et d'appareil pour Azure IoT Hub
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 06/26/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 65d511cabda3595828db9926603b6eb20efd2e83
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549942"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Tutoriel : Utiliser les scripts fournis par Microsoft pour créer des certificats de test

Microsoft fournit des scripts PowerShell et Bash pour vous aider à créer vos propres certificats X.509 et à les authentifier auprès d'une instance d'IoT Hub. Les scripts se trouvent dans un [référentiel](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) GitHub. Ils sont fournis à des fins de démonstration uniquement. Les certificats créés par ces scripts ne doivent pas être utilisés pour la production. Les certificats contiennent des mots de passe codés en dur (« 1234 ») et expirent au bout de 30 jours. Pour un environnement de production, vous devrez appliquer vos propres bonnes pratiques en matière de création de certificats et de gestion de leur durée de vie.

## <a name="powershell-scripts"></a>Scripts PowerShell

### <a name="step-1---setup"></a>Étape 1 : Configurer

Obtenez OpenSSL pour Windows. Consultez <https://www.openssl.org/docs/faq.html#MISC4> pour savoir où le télécharger ou <https://www.openssl.org/source/> pour le créer à partir de la source. Exécutez ensuite les scripts préliminaires :

1. Copiez les scripts de ce [référentiel](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) GitHub vers le répertoire local dans lequel vous souhaitez travailler. Tous les fichiers seront créés en tant qu'enfants de ce répertoire.

1. Démarrez PowerShell en tant qu'administrateur.

1. Accédez au répertoire dans lequel vous avez chargé les scripts.

1. À partir de la ligne de commande, définissez la variable d'environnement `$ENV:OPENSSL_CONF` sur le répertoire dans lequel se trouve le fichier de configuration OpenSSL (openssl.cnf).

1. Exécutez `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` pour permettre à PowerShell d'exécuter les scripts.

1. Exécutez `. .\ca-certs.ps1`. Cela fait entrer les fonctions du script dans l'espace de noms global de PowerShell.

1. Exécutez `Test-CACertsPrerequisites`. PowerShell utilise le magasin de certificats Windows pour gérer les certificats. Cette commande vérifie qu'aucune collision de noms ne se produira ultérieurement avec les certificats existants, et qu'OpenSSL est correctement configuré.

### <a name="step-2---create-certificates"></a>Étape 2 : Créer des certificats

Exécutez `New-CACertsCertChain [ecc|rsa]`. ECC est recommandé, mais pas obligatoire, pour les certificats d'autorité de certification (AC). Ce script met à jour votre répertoire et votre magasin de certificats Windows avec les certificats d'AC et les certificats intermédiaires suivants :

* intermediate1.pem
* intermediate2.pem
* intermediate3.pem
* RootCA.cer
* RootCA.pem

Après avoir exécuté le script, ajoutez le nouveau certificat d'AC (RootCA.pem) à votre instance d'IoT Hub :

1. Ouvrez votre instance d'IoT Hub et accédez à Certificats.

1. Sélectionnez **Ajouter**.

1. Entrez un nom d'affichage pour le certificat d'AC.

1. Pour ignorer la preuve de possession, cochez la case en regard de **Définir l’état du certificat sur vérifié lors du chargement**.

1. Chargez le certificat d'AC.

1. Sélectionnez **Enregistrer**.

### <a name="optional-step-3---prove-possession"></a>(Facultatif) Étape 3 : Fournir une preuve de possession

Si vous n’avez pas choisi de vérifier automatiquement le certificat lors du chargement, vous prouvez manuellement la possession :

1. Sélectionnez le nouveau certificat d'AC.

1. Sélectionnez **Générer un code de vérification** dans la boîte de dialogue **Détails du certificat**. Pour plus d'informations, consultez [Preuve de possession d'un certificat d'AC](tutorial-x509-prove-possession.md).

1. Créez un certificat contenant le code de vérification. Par exemple, si le code de vérification est `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`, exécutez la commande suivante pour créer un nouveau certificat dans le répertoire de travail contenant l'objet `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288`. Le script crée un certificat nommé `VerifyCert4.cer`.

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Chargez `VerifyCert4.cer` dans votre instance d'IoT Hub à partir de la boîte de dialogue **Détails du certificat**.

1. Sélectionnez **Vérifier**.

### <a name="step-4---create-a-new-device"></a>Étape 4 : Créer un appareil

Créez un appareil pour votre instance d'IoT Hub :

1. Dans votre instance d'IoT Hub, accédez à la section **Appareils IoT**.

1. Ajoutez un nouvel appareil et attribuez-lui l'ID `mydevice`.

1. Choisissez le type d'authentification **X.509 signé par une AC**.

1. Exécutez `New-CACertsDevice mydevice` pour créer un certificat d'appareil. Les fichiers suivants sont créés dans votre répertoire de travail :

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>Étape 5 : Tester votre certificat d'appareil

Accédez à [Test de l'authentification par certificat](tutorial-x509-test-certificate.md) pour déterminer si votre certificat d'appareil permet l'authentification auprès de votre instance d'IoT Hub. Vous devez utiliser la version PFX de votre certificat, `mydevice.pfx`.

### <a name="step-6---cleanup"></a>Étape  6 : Nettoyer

À partir du menu Démarrer, ouvrez **Gérer les certificats d'ordinateur** et accédez à **Certificats - Ordinateur local > personnel**. Supprimez les certificats délivrés par « Azure IoT CA TestOnly ». De même, supprimez les certificats appropriés à partir de **>Autorité de certification racine de confiance > Certificats et de >Autorités de certification intermédiaires > Certificats**.

## <a name="bash-scripts"></a>Scripts Bash

### <a name="step-1---setup"></a>Étape 1 : Configurer

1. Démarrez Bash.

1. Accédez au répertoire dans lequel vous souhaitez travailler. Tous les fichiers seront créés dans ce répertoire.

1. Copiez `*.cnf` et `*.sh` dans votre répertoire de travail.

### <a name="step-2---create-certificates"></a>Étape 2 : Créer des certificats

1. Exécutez `./certGen.sh create_root_and_intermediate`. Les fichiers suivants sont créés dans le répertoire **certs** :

    * azure-iot-test-only.chain.ca.cert.pem
    * azure-iot-test-only.intermediate.cert.pem
    * azure-iot-test-only.root.ca.cert.pem

1. Ouvrez votre instance d'IoT Hub et accédez à **Certificats**.

1. Sélectionnez **Ajouter**.

1. Entrez un nom d'affichage pour le certificat d'AC.

1. Chargez uniquement le certificat d'AC dans votre l'instance IoT Hub. Le nom du certificat est `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Sélectionnez **Enregistrer**.

### <a name="step-3---prove-possession"></a>Étape 3 : Fournir une preuve de possession

1. Sélectionnez le nouveau certificat d'AC créé à l'étape précédente.

1. Sélectionnez **Générer un code de vérification** dans la boîte de dialogue **Détails du certificat**. Pour plus d'informations, consultez [Preuve de possession d'un certificat d'AC](tutorial-x509-prove-possession.md).

1. Créez un certificat contenant le code de vérification. Par exemple, si le code de vérification est `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`, exécutez la commande suivante pour créer un nouveau certificat dans le répertoire de travail `verification-code.cert.pem` contenant l'objet `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288`.

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Chargez le certificat dans votre instance d'IoT Hub à partir de la boîte de dialogue **Détails du certificat**.

1. Sélectionnez **Vérifier**.

### <a name="step-4---create-a-new-device"></a>Étape 4 : Créer un appareil

Créez un appareil pour votre instance d'IoT Hub :

1. Dans votre instance d'IoT Hub, accédez à la section Appareils IoT.

1. Ajoutez un nouvel appareil et attribuez-lui l'ID `mydevice`.

1. Choisissez le type d'authentification **X.509 signé par une AC**.

1. Exécutez `./certGen.sh create_device_certificate mydevice` pour créer un certificat d'appareil. Deux fichiers nommés `new-device.cert.pem` et `new-device.cert.pfx` sont créés dans votre répertoire de travail.

### <a name="step-5---test-your-device-certificate"></a>Étape 5 : Tester votre certificat d'appareil

Accédez à [Test de l'authentification par certificat](tutorial-x509-test-certificate.md) pour déterminer si votre certificat d'appareil permet l'authentification auprès de votre instance d'IoT Hub. Vous devez utiliser la version PFX de votre certificat, `new-device.cert.pfx`.

### <a name="step-6---cleanup"></a>Étape  6 : Nettoyer

Comme le script Bash se contente de créer des certificats dans votre répertoire de travail, il vous suffit de les supprimer lorsque vous avez terminé les tests.

## <a name="next-steps"></a>Étapes suivantes

Pour tester votre certificat et déterminer s'il permet d'authentifier votre appareil auprès de votre instance d'IoT Hub, accédez à [Test de l'authentification par certificat](tutorial-x509-test-certificate.md).
