---
title: Déployer IoT Connector dans le Portail Azure-API de santé Azure
description: Dans cet article, vous allez apprendre à déployer IoT Connector dans le Portail Azure.
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 11/10/2021
ms.author: jasteppe
ms.openlocfilehash: f4eaee6afb134513da55b7f39e2d1f6df6c547b7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345327"
---
# <a name="deploy-iot-connector-in-the-azure-portal"></a>Déployer IoT Connector dans le Portail Azure

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale.

Dans ce guide de démarrage rapide, vous allez apprendre à déployer IoT Connector dans le Portail Azure. La configuration d’un connecteur IoT vous permet d’ingérer des données de Internet des objets (IoT) dans votre service FHIR&#174; (Fast Healthcare Interoperability Resources) à l’aide d’un hub d’événements Azure pour les messages de l’appareil.

## <a name="prerequisites"></a>Prérequis

Il est important que les conditions préalables suivantes soient remplies avant de commencer les étapes de création d’une instance IoT Connector dans les API de santé Azure.

* [Compte Azure](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)
* [Groupe de ressources déployé dans le Portail Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md)
* [Espace de noms Event Hubs et hub d’événements déployés dans le Portail Azure](../../event-hubs/event-hubs-create.md)
* [Espace de travail déployé dans les API de santé Azure](../healthcare-apis-quickstart.md)  
* [Service FHIR déployé dans les API de santé Azure](../fhir/fhir-portal-quickstart.md) 

## <a name="deploy-iot-connector"></a>Déployer le connecteur IoT 

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis entrez votre nom de ressource d’espace de travail API Healthcare dans le champ barre de **recherche** .
 
   ![Capture d’écran de la saisie du nom de ressource de l’espace de travail dans le champ de la barre de recherche.](media/select-workspace-resource-group.png#lightbox)

2. Sélectionnez **déployer les connecteurs IOT**.

   ![Capture d’écran du panneau connecteurs IoT.](media/iot-connector-blade.png#lightbox)

3. Sélectionnez ensuite **Ajouter un connecteur IOT**.

   ![Capture d’écran de l’ajout de connecteurs IoT.](media/add-iot-connector.png#lightbox)

## <a name="configure-iot-connector-to-ingest-data"></a>Configurer le connecteur IoT pour la réception de données

Sous l’onglet **notions de base** , renseignez les champs requis sous détails de l' **instance**.

![Capture d’écran de la configuration des détails de l’instance IoT.](media/basics-instance-details.png#lightbox)

1. Entrez le **nom du connecteur IOT**.

   Le **nom du connecteur IOT** est un nom convivial pour le connecteur IOT. Entrez un nom unique pour votre IoT Connector. Par exemple, vous pouvez le nommer `healthdemo-iot` .

2. Entrez le **nom du hub d’événements**.

   Le nom du hub d’événements est le nom de l' **instance de Event hubs** que vous avez déployée. 

   Pour plus d’informations sur les Event Hubs Azure, consultez [démarrage rapide : créer un Event Hub à l’aide de portail Azure](../../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace).

3. Entrez le **groupe de consommateurs**.

   Le nom du groupe de consommateurs se trouve à l’aide de la barre de **recherche** pour accéder à l’instance de Event hubs que vous avez déployée et en sélectionnant le panneau  **groupes de consommateurs** .

   ![Capture d’écran du nom du groupe de consommateurs.](media/consumer-group-name.png#lightbox)

   Pour plus d’informations sur les groupes de consommateurs, consultez [fonctionnalités et terminologie dans Azure Event hubs](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#event-consumers).

4. Entrez le nom de l' **espace de noms** complet.

    L' **espace de noms** complet est le **nom d’hôte** situé sur la page **vue d’ensemble** de votre espace de noms Event hubs.

    ![Capture d’écran de l’espace de noms complet.](media/event-hub-hostname.png#lightbox)  

    Pour plus d’informations sur les espaces de noms Event Hubs, consultez [espace de noms](../../event-hubs/event-hubs-features.md?WT.mc_id=Portal-Microsoft_Healthcare_APIs#namespace) dans le document fonctionnalités et terminologie dans Azure Event hubs.

5. Sélectionnez **suivant : mappage** de l’appareil. 
  
## <a name="configure-device-mapping-properties"></a>Configurer les propriétés de mappage d’appareil

> [!TIP]
> Le mappeur de données du connecteur IoMT est un outil open source qui permet de visualiser la configuration de mappage pour normaliser les données d’entrée d’un appareil, puis de les transformer en ressources FHIR. Les développeurs peuvent utiliser cet outil pour modifier et tester les périphériques et les mappages de destination FHIR, et pour exporter les données à charger vers un connecteur IoT dans le Portail Azure. Cet outil permet également aux développeurs de comprendre les configurations de mappage de l’appareil et de la destination FHIR.
>
> Pour plus d’informations, consultez la documentation Open Source :
>
> [Mappeur de données du connecteur IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)
>
> [Mappage du contenu de l’appareil](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#device-content-mapping)

1. Sous l’onglet **mappage de périphérique** , entrez le code JSON de mappage de périphérique associé à votre connecteur IOT.

   ![Capture d’écran de la configuration de la mise en correspondance des appareils.](media/configure-device-mapping.png#lightbox)

2. Sélectionnez **suivant : destination >** pour configurer les propriétés de destination associées à votre connecteur IOT.

## <a name="configure-fhir-destination-mapping-properties"></a>Configurer les propriétés de mappage de destination FHIR

Sous l’onglet **destination** , entrez les propriétés de destination associées au connecteur IOT.

   ![Capture d’écran des propriétés de configuration de la destination.](media/configure-destination-properties.png#lightbox)

1. Entrez l’ID de ressource Azure du **service FHIR**.

   Le nom du **serveur FHIR** (également appelé **service FHIR**) est localisé à l’aide de la barre de **recherche** pour accéder au service FHIR que vous avez déployé et en sélectionnant le panneau **Propriétés** . Copiez et collez la chaîne d' **ID de ressource** dans le champ de texte **serveur FHIR** .

    ![Capture d’écran de entrez le nom du serveur FHIR.](media/fhir-service-resource-id.png#lightbox) 

2. Entrez le **nom** de la destination.

   Le **nom de destination** est un nom convivial pour la destination. Entrez un nom unique pour votre destination. Par exemple, vous pouvez le nommer `iotmedicdevice` .

3. Sélectionnez **créer** ou **Rechercher** pour le **type de résolution**.

    > [!NOTE]
    > Pour que la destination du connecteur IoT crée une ressource d’observation valide dans le service FHIR, une ressource d’appareil et une ressource de patient **doivent** exister sur le serveur FHIR. l’observation peut donc référencer correctement l’appareil qui a créé les données et le patient à partir duquel les données ont été mesurées. Le connecteur IoT peut utiliser deux modes pour résoudre les ressources des appareils et des patients.

   **Créer**

     La destination du connecteur IoT tente de récupérer une ressource d’appareil à partir du serveur FHIR à l’aide de l’identificateur d’appareil inclus dans le message de concentrateur d’événements. Il tente également de récupérer une ressource de patient à partir du serveur FHIR à l’aide de l’identificateur de patient inclus dans le message de hub d’événements. Si l’une des ressources est introuvable, les nouvelles ressources sont créées (appareil, patient ou les deux) contenant uniquement l’identificateur contenu dans le message du concentrateur d’événements. Lorsque vous utilisez l’option **Create** , un identificateur d’appareil et un identificateur de patient peuvent être configurés dans le mappage de l’appareil. en d’autres termes, lorsque le IoT Connector destination est en mode **création** , il peut fonctionner normalement **sans** ajouter des ressources de l’appareil et du patient au serveur FHIR.

   **Lookup**

     La destination du connecteur IoT tente de récupérer une ressource d’appareil à partir du serveur FHIR à l’aide de l’identificateur d’appareil inclus dans le message de Event Hub. Si la ressource de l’appareil est introuvable, cela génère une erreur et les données ne sont pas traitées. Pour que la fonction de **recherche** fonctionne correctement, une ressource d’appareil avec un identificateur correspondant à l’identificateur de l’appareil inclus dans le Event Hub message **doit** exister et la ressource de l’appareil **doit** avoir une référence à une ressource de patient qui existe également. En d’autres termes, lorsque la destination du connecteur IoT est en mode de recherche, les ressources des appareils et des patients **doivent** être ajoutées au serveur FHIR avant que les données puissent être traitées.

   Pour plus d’informations, consultez la documentation Open source [mappage de destination FHIR](https://github.com/microsoft/iomt-fhir/blob/master/docs/Configuration.md#fhir-mapping).

4. Sous **mappage de destination**, entrez le code JSON dans l’éditeur de code.

   Pour plus d’informations sur l’outil Mappeur, consultez [outil mappeur de données du connecteur IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper).

5. Vous pouvez sélectionner **passer en revue + créer**, ou vous pouvez sélectionner **suivant : balises >** si vous souhaitez configurer des balises.  

## <a name="optional-configure-tags"></a>Facultatif Configurer des balises

Les balises sont des paires nom/valeur utilisées pour la catégorisation des ressources. Pour plus d’informations sur les balises, consultez [utiliser des balises pour organiser vos ressources Azure et votre hiérarchie de gestion](../../azure-resource-manager/management/tag-resources.md).

Sous l’onglet **balises** , entrez les propriétés de balise associées au connecteur IOT.

   ![Capture d’écran des propriétés de balise.](media/tag-properties.png#lightbox)
 
1. Saisissez un **Nom**.
2. Entrez une **valeur**.
3. Sélectionnez **Revoir + créer**.

   Vous devez voir un message de **réussite de validation** comme ce qui est affiché dans l’image ci-dessous. 

   ![Capture d’écran du message de réussite de la validation.](media/iot-connector-validation-success.png#lightbox) 

   > [!NOTE]
   > Si votre connecteur IoT n’a pas été validé, examinez le message d’échec de validation et résolvez le problème. Il est recommandé de passer en revue les propriétés sous chaque onglet IoT Connector que vous avez configuré.

4. Ensuite, sélectionnez **Créer**.

   Le connecteur IoT que vous venez de déployer s’affichera à l’intérieur de votre page groupes de ressources Azure.

   ![Capture d’écran du connecteur IoT déployé répertorié dans la liste des ressources Azure récentes.](media/azure-resources-iot-connector-deployed.png#lightbox)  

    Maintenant que votre connecteur IoT a été déployé, nous allons parcourir les étapes de l’attribution d’autorisations d’accès au hub d’événements et au service FHIR. 

## <a name="granting-iot-connector-access"></a>Octroi de l’accès au connecteur IoT

Pour garantir le bon fonctionnement de votre connecteur IoT, il doit disposer d’autorisations d’accès au hub d’événements et au service FHIR. 

### <a name="accessing-the-iot-connector-from-the-event-hub"></a>Accès au connecteur IoT à partir du hub d’événements

1. Dans la liste **groupe de ressources Azure** , sélectionnez le nom de votre **Event hubs espace de noms**.

2. Sélectionnez le **panneau contrôle d’accès (IAM)** , puis sélectionnez **+ Ajouter**.   

   ![Capture d’écran du contrôle d’accès de Event Hubs espace de noms.](media/access-control-blade-add.png#lightbox)

3. Sélectionnez **Ajouter une attribution de rôle**.

   ![Capture d’écran de l’ajout d’une attribution de rôle.](media/event-hub-add-role-assignment.png#lightbox)
 
4. Sélectionnez le **rôle**, puis sélectionnez **Azure Event hubs récepteur de données**.

   ![Capture d’écran des champs requis pour l’ajout d’une attribution de rôle.](media/event-hub-add-role-assignment-fields.png#lightbox)

   Le rôle de récepteur de données Azure Event Hubs permet au connecteur IoT auquel ce rôle est attribué de recevoir des données de ce hub d’événements.

   Pour plus d’informations sur les rôles d’application, consultez [authentification & autorisation pour les API de santé (version préliminaire)](.././authentication-authorization.md).

5. Sélectionnez **attribuer l’accès à**, puis conservez l’option par défaut **utilisateur, groupe ou principal de service** sélectionné.

6. Dans le champ **Sélectionner** , entrez le principal de sécurité de votre connecteur IOT.  

   `<your workspace name>/iotconnectors/<your IoT connector name>`
 
   Lorsque vous déployez un connecteur IoT, il crée une identité gérée. Le nom de l’identifiant géré est une concaténation du nom de l’espace de travail, du type de ressource (le connecteur IoT) et du nom du connecteur IoT.

7. Sélectionnez **Enregistrer**.

   Une fois l’attribution de rôle correctement ajoutée au concentrateur d’événements, une notification affiche une coche verte avec le texte « ajouter une attribution de rôle ».  Ce message indique que le connecteur IoT peut maintenant lire à partir du hub d’événements.

   ![Capture d’écran du message d’attribution de rôle ajouté.](media/event-hub-added-role-assignment.png#lightbox)

Pour plus d’informations sur la création d’un accès aux ressources Event Hubs, consultez [autoriser l’accès avec Azure Active Directory](../../event-hubs/authorize-access-azure-active-directory.md).  

### <a name="accessing-the-iot-connector-from-the-fhir-service"></a>Accès au connecteur IoT à partir du service FHIR

1. Dans la **Liste groupe de ressources Azure**, sélectionnez le nom de votre  **service FHIR**.
 
2. Sélectionnez le **panneau contrôle d’accès (IAM)** , puis sélectionnez **+ Ajouter**. 

3. Sélectionnez **Ajouter une attribution de rôle**.

  ![Capture d’écran de l’ajout de l’attribution de rôle pour le service FHIR.](media/fhir-service-add-role-assignment.png#lightbox)

4. Sélectionnez le **rôle**, puis sélectionnez **FHIR Data Writer**.

   Le rôle FHIR Data Writer fournit un accès en lecture et en écriture que le connecteur IoT utilise pour fonctionner. Étant donné que le connecteur IoT est déployé en tant que ressource distincte, le service FHIR reçoit les demandes du connecteur IoT. Si le service FHIR ne sait pas qui effectue la demande, ou s’il n’a pas le rôle affecté, il refuse la demande comme non autorisée.

   Pour plus d’informations sur les rôles d’application, consultez [authentification & autorisation pour les API de santé (version préliminaire)](.././authentication-authorization.md).

5. Dans le champ **Sélectionner** , entrez le principal de sécurité de votre connecteur IOT.  

    `<your workspace name>/iotconnectors/<your IoT connector name>`

6. Sélectionnez **Enregistrer**.

   ![Capture d’écran du message d’attribution de rôle ajouté au service FHIR.](media/fhir-service-added-role-assignment.png#lightbox)

   Pour plus d’informations sur l’affectation de rôles au service FHIR, consultez [configurer Azure RBAC pour le service FHIR](../fhir/configure-azure-rbac-for-fhir.md).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à déployer un connecteur IoT dans le Portail Azure. Pour obtenir une vue d’ensemble du connecteur IoT, consultez

>[!div class="nextstepaction"]
>[Vue d’ensemble du connecteur IoT](iot-connector-overview.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
