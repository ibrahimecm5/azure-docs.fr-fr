---
title: Gérer des inscriptions des services IoT Hub Device Provisioning sur le Portail Azure
description: Guide pratique de gestion des inscriptions d’appareil avec votre service Device Provisioning (DPS) dans le Portail Azure
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/25/2021
ms.topic: how-to
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.openlocfilehash: 7f0fdcf8a3e51405861082b3c2ad8a6385f5a6e9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843359"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Guide pratique pour gérer les inscriptions d’appareils avec le portail Azure

Une *inscription d’appareil* crée un enregistrement d’un appareil ou d’un groupe d’appareils susceptibles d’être inscrits au service Azure IoT Hub Device Provisioning Service à un moment donné. L’enregistrement contient la configuration initiale pour le ou les appareils dans le cadre de cette inscription. La configuration inclut le hub IoT auquel un appareil est affecté ou une stratégie d’allocation qui configure le hub à partir d’un ensemble de hubs. Cet article explique comment gérer les inscriptions d’appareil dans votre service d’approvisionnement.

Le service Azure IoT Device Provisioning prend en charge deux types d’inscriptions :

* [Groupes d’inscription](concepts-service.md#enrollment-group) : utilisés pour inscrire plusieurs appareils connexes.
* [Inscriptions individuelles](concepts-service.md#individual-enrollment) : utilisées pour inscrire un seul appareil.

## <a name="create-an-enrollment-group"></a>Création d’un groupe d’inscription

 Un groupe d’inscription est une entrée pour un groupe d’appareils qui partagent un mécanisme d’attestation commun. Nous vous recommandons d’utiliser un groupe d’inscription pour un grand nombre d’appareils qui partagent une configuration initiale ou pour des appareils destinés au même locataire. Les appareils qui utilisent une attestation de [clé symétrique](concepts-symmetric-key-attestation.md) ou de [certificats X.509](concepts-x509-attestation.md) sont pris en charge.

### <a name="create-a-symmetric-key-enrollment-group"></a>Créer un groupe d’inscription de clé symétrique

Pour créer et utiliser des groupes d’inscription avec des clés symétriques, consultez le didacticiel [Provisionner des appareils avec des clés symétriques](how-to-legacy-device-symm-key.md).

Pour créer un groupe d’inscription de clé symétrique :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.

3. Sélectionnez le service Device Provisioning Service auquel vous souhaitez inscrire votre appareil.

4. Dans le menu **Paramètres**, sélectionnez **Gérer les inscriptions**.

5. En haut de la page, sélectionnez **+ Ajouter un groupe d’inscriptions**.

6. Dans la page **Ajouter un groupe d’inscriptions**, entrez les informations suivantes :

   | Champ | Description |
    | :--- | :--- |
    | **Nom du groupe** | Nom du groupe d’appareils.|
    | **Type d’attestation** |sélectionnez **Clé symétrique**.|
    | **Générer automatiquement des clés** |cochez cette case.|
    | **Sélectionner le mode d’affectation des appareils aux hubs** |Sélectionnez *Configuration statique* afin de les affecter à un hub spécifique.|
    | **Sélectionner les hubs IoT auxquels ce groupe peut être attribué** |sélectionnez un de vos hubs.|

    Laissez les autres champs à leurs valeurs par défaut.

    :::image type="content" source="./media/how-to-manage-enrollments/add-enrollment-group-symm-key.png" alt-text="Ajoutez un groupe d’inscriptions pour l’attestation de clé symétrique":::.

7. Sélectionnez **Enregistrer**.

### <a name="create-a-x509-certificate-enrollment-group"></a>Créer un groupe d’inscription de certificats X.509

Pour créer un groupe d’inscriptions de certificats X.509 :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.

3. Sélectionnez le service Device Provisioning Service auquel vous souhaitez inscrire votre appareil.

4. Dans le menu **Paramètres**, sélectionnez **Gérer les inscriptions**.

5. En haut de la page, sélectionnez **+ Ajouter un groupe d’inscriptions**.

6. Dans la page **Ajouter un groupe d’inscriptions**, entrez les informations suivantes :

    | Champ | Description |
    | :--- | :--- |
    | **Nom du groupe** | Nom du groupe d’appareils.|
    | **Type d’attestation** |Sélectionnez **Certificate**.|
    | **Type de certificat** | Sélectionnez **Certificat d’autorité de certification** ou **Intermédiaire** en fonction du certificat qui a signé vos certificats d’appareil.|
    | **Certificat principal**| Si vous signez vos certificats d’appareil avec un certificat d’autorité de certification, le certificat d’autorité de certification racine doit afficher le statut Terminé pour la [preuve de possession](how-to-verify-certificates.md). Si vous signez vos certificats d’appareil avec un certificat intermédiaire, un bouton Charger sera disponible pour vous permettre de charger votre certificat intermédiaire. Le certificat qui a signé le certificat intermédiaire doit également disposer d’une [preuve de possession](how-to-verify-certificates.md).

    Laissez les autres champs à leurs valeurs par défaut.

    :::image type="content" source="./media/how-to-manage-enrollments/add-enrollment-group-cert.png" alt-text="Ajoutez un groupe d’inscriptions pour l’attestation de certificat d’autorité de certification.":::

7. Sélectionnez **Enregistrer**.

## <a name="create-an-individual-enrollment"></a>Créer une inscription individuelle

Une inscription individuelle est une entrée permettant de connecter un seul appareil qui peut être assigné à un hub IoT. Les appareils qui utilisent une [clé symétrique](concepts-symmetric-key-attestation.md), des [certificats X.509](concepts-x509-attestation.md) et une [attestation TPM](concepts-tpm-attestation.md) sont pris en charge.

### <a name="create-a-symmetric-key-individual-enrollment"></a>Créer une inscription individuelle à clé symétrique

>[!TIP]
>Pour obtenir des instructions plus détaillées sur la façon de créer et d’utiliser des inscriptions individuelles avec des clés symétriques, consultez [Démarrage rapide : provisionner un appareil de clé symétrique simulé](quick-create-simulated-device-symm-key.md#create-a-device-enrollment).

Pour créer une inscription individuelle à clé symétrique :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.

3. Sélectionnez le service Device Provisioning Service auquel vous souhaitez inscrire votre appareil.

4. Dans le menu **Paramètres**, sélectionnez **Gérer les inscriptions**.

5. En haut de la page, sélectionnez **+ Ajouter une inscription individuelle**.

6. Dans la page **Ajouter une inscription**, entrez les informations suivantes.

    | Champ | Description |
    | :--- | :--- |
    | **Mécanisme** | Sélectionnez *Clé symétrique* |
    | **Générer automatiquement des clés** |cochez cette case. |
    | **ID d'inscription** | Tapez un ID d’inscription unique.|
    | **ID de l’appareil IoT Hub** |  Cet ID représente votre appareil. Les utilisateurs doivent se conformer aux règles en vigueur concernant l’ID d’appareil. Pour plus d’informations, consultez [Propriétés de l’identité d’appareil](.. /iot-hub/iot-hub-devguide-identity-registry. Si l’ID d’appareil n’est pas spécifié, l’ID d’inscription est utilisé.|
    | **Sélectionner le mode d’affectation des appareils aux hubs** |Sélectionnez *Configuration statique* afin de les affecter à un hub spécifique.|
    | **Sélectionner les hubs IoT auxquels ce groupe peut être attribué** |sélectionnez un de vos hubs.|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-symm-key.png" alt-text="Ajoutez une inscription individuelle pour l’attestation de clé symétrique.":::

7. Sélectionnez **Enregistrer**.

### <a name="create-a-x509-certificate-individual-enrollment"></a>Créer un inscription individuelle à certificat X.509

>[!TIP]
>Pour obtenir des instructions plus détaillées sur la façon de créer et d’utiliser des inscriptions individuelles avec des certificats X.509, consultez [Démarrage rapide : provisionner un appareil à certificat X.509](quick-create-simulated-device-x509.md#create-a-device-enrollment).

Pour créer un inscription individuelle à certificat X.509 :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.

3. Sélectionnez le service Device Provisioning Service auquel vous souhaitez inscrire votre appareil.

4. Dans le menu **Paramètres**, sélectionnez **Gérer les inscriptions**.

5. En haut de la page, sélectionnez **+ Ajouter une inscription individuelle**.

6. Dans la page **Ajouter une inscription**, entrez les informations suivantes.

    | Champ | Description |
    | :--- | :--- |
    | **Mécanisme** | Sélectionnez *X.509*. |
    | **Fichier .pem ou .cer du certificat principal** | Chargez un certificat à partir duquel vous pouvez générer des certificats feuille. Si vous choisissez un fichier .cer, seul le certificat codé en base 64 est accepté. |
    | **ID de l’appareil IoT Hub** |  Cet ID représente votre appareil. Les utilisateurs doivent se conformer aux règles en vigueur concernant l’ID d’appareil. Pour plus d’informations, consultez [Propriétés de l’identité d’appareil](.. /iot-hub/iot-hub-devguide-identity-registry. L’ID d’appareil doit être le nom du sujet sur le certificat d’appareil que vous chargez pour l’inscription. Ce nom d’objet doit être conforme aux règles applicables à l’ID d’un appareil.|
    | **Sélectionner le mode d’affectation des appareils aux hubs** |Sélectionnez *Configuration statique* afin de les affecter à un hub spécifique.|
    | **Sélectionner les hubs IoT auxquels ce groupe peut être attribué** |sélectionnez un de vos hubs.|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-cert.png" alt-text="Ajoutez une inscription individuelle pour l’attestation de certificat X.509.":::

7. Sélectionnez **Enregistrer**.

### <a name="create-a-tpm-individual-enrollment"></a>Créer une inscription individuelle de TPM

>[!TIP]
>Pour obtenir des instructions détaillées sur la création et l’utilisation d’inscriptions individuelles à l’aide de l’attestation TPM, consultez l’un des exemples [Provisionner un appareil TPM simulé](quick-create-simulated-device-tpm.md#create-a-device-enrollment-entry).

Créer une inscription individuelle à TPM

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.

3. Sélectionnez le service Device Provisioning Service auquel vous souhaitez inscrire votre appareil.

4. Dans le menu **Paramètres**, sélectionnez **Gérer les inscriptions**.

5. En haut de la page, sélectionnez **+ Ajouter une inscription individuelle**.

6. Dans la page **Ajouter une inscription**, entrez les informations suivantes.

    | Champ | Description |
    | :--- | :--- |
    | **Mécanisme** | Sélectionnez *TPM* |
    | **EK (Endorsement Key)** | Paire de clés de type EK (Endorsement Key) du périphérique TPM. |
    | **ID d'inscription** | Tapez un ID d’inscription unique.|
    | **ID de l’appareil IoT Hub** |  Cet ID représente votre appareil. Les utilisateurs doivent se conformer aux règles en vigueur concernant l’ID d’appareil. Pour plus d’informations, consultez [Propriétés de l’identité d’appareil](.. /iot-hub/iot-hub-devguide-identity-registry. Si l’ID d’appareil n’est pas spécifié, l’ID d’inscription est utilisé.|
    | **Sélectionner le mode d’affectation des appareils aux hubs** |Sélectionnez *Configuration statique* afin de les affecter à un hub spécifique.|
    | **Sélectionner les hubs IoT auxquels ce groupe peut être attribué** |sélectionnez un de vos hubs.|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-tpm.png" alt-text="Ajoutez une inscription individuelle pour l’attestation TPM.":::

7. Sélectionnez **Enregistrer**.

## <a name="update-an-enrollment-entry"></a>Mettre à jour une entrée d’inscription

Pour mettre à jour une entrée d’inscription existante :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.

3. Sélectionnez le service Device Provisioning Service auquel vous souhaitez inscrire votre appareil.

4. Dans le menu **Paramètres**, sélectionnez **Gérer les inscriptions**.

5. Sélectionnez l’entrée d’inscription que vous souhaitez modifier.

6. Sur la page Détails de l’entrée d’inscription, vous pouvez mettre à jour tous les éléments, à l’exception du type de sécurité et des informations d’identification.

7. Une fois que vous avez terminé, sélectionnez **Enregistrer**.

## <a name="remove-a-device-enrollment"></a>Supprimer une inscription d’appareil

Pour supprimer une entrée d’inscription :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu de gauche ou dans la page du portail, sélectionnez **Toutes les ressources**.

3. Sélectionnez le service Device Provisioning Service auquel vous souhaitez inscrire votre appareil.

4. Dans le menu **Paramètres**, sélectionnez **Gérer les inscriptions**.

5. Sélectionnez l’entrée d’inscription que vous souhaitez supprimer. 

6. En haut de la page, sélectionnez **Supprimer**.

7. À l’invite de confirmation, sélectionnez **Oui**.

8. Lorsque l’opération est terminée, votre entrée a été supprimée de la liste des inscriptions d’appareil.
