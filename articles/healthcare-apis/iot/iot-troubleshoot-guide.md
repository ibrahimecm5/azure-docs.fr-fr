---
title: Guide de résolution des problèmes du connecteur IoT et méthodes de santé Azure
description: Cet article aide les utilisateurs à résoudre les problèmes courants liés aux messages d’erreur, aux conditions et à la façon de copier des fichiers de mappage.
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: troubleshooting
ms.date: 11/16/2021
ms.author: jasteppe
ms.openlocfilehash: 6a3af00eaca90c2ecdbbdcc4f6cce5231ffefcc0
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719444"
---
# <a name="iot-connector-troubleshooting-guide"></a>Guide de résolution des problèmes du connecteur IoT

> [!IMPORTANT]
> Les API Azure Healthcare sont actuellement en version préliminaire. L’[Avenant aux conditions d’utilisation pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) contient des conditions légales supplémentaires qui s’appliquent aux fonctionnalités Azure en version bêta, en préversion ou pas encore en disponibilité générale. 

> [!TIP]
> Lors de l’ouverture d’un ticket de [support technique Azure](https://azure.microsoft.com/support/create-ticket/) pour le connecteur IOT, incluez des copies de votre appareil et des mappages de destination FHIR pour faciliter le processus de dépannage.

Cet article explique comment résoudre les problèmes courants liés aux messages d’erreur et aux conditions d’un connecteur IoT. Vous apprendrez également à créer des copies des mappages de destination de l’appareil et des ressources FHIR&#174;) du connecteur IoT. En outre, vous pouvez utiliser les copies de mappage de destination Device et FHIR pour la modification et l’archivage en dehors de la Portail Azure. 

## <a name="device-and-fhir-destination-mappings-validations"></a>Validations des mappages de destination des appareils et des FHIR

Cette section décrit le processus de validation que fait le connecteur IoT. Le processus de validation valide les mappages de destination de l’appareil et de la FHIR avant de les autoriser à être enregistrés pour utilisation. Ces éléments sont requis dans les mappages de destination Device et FHIR.

> [!TIP]
> Consultez l’outil [Mappeur de données du connecteur IoMT](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper) pour la modification, le test et la résolution des problèmes des mappages de destination FHIR et des appareils IOT Connector. Exportez les mappages de chargement vers le connecteur IoT dans le Portail Azure ou utilisez avec la [version open source](https://github.com/microsoft/iomt-fhir) du connecteur IOT.

**Mappages d’appareils**

|Élément|Obligatoire|
|:-------|:------|
|TypeName|True|
|TypeMatchExpression|True|
|DeviceIdExpression|True|
|TimestampExpression|True|
|Values[].ValueName|True|
|Values[].ValueExpression|True|

> [!NOTE]
> `Values[].ValueName and Values[].ValueExpression` les éléments sont requis uniquement si vous avez une entrée de valeur dans le tableau. Il est possible de n’avoir aucune valeur mappée. Cela est utilisé lorsque la télémétrie envoyée est un événement. 
>
>Par exemple :
> 
>Lorsqu’un appareil IoMT portable est mis en place ou supprimé, les éléments n’ont pas de valeurs à l’exception du nom que le connecteur IoT correspond et émet. Lors de la conversion FHIR, IoT Connector le mappe à un concept de code pouvant être basé sur le type sémantique. Cela signifie qu’aucune valeur réelle n’est remplie.

**Mappages de destination FHIR**

|Élément|Obligatoire|
|:------|:-------|
|TypeName|True|

> [!NOTE]
> Il s’agit du seul élément de mappage de destination FHIR requis validé pour l’instant.

## <a name="error-messages-and-fixes"></a>Messages d’erreur et correctifs

### <a name="iot-connector-resource"></a>Ressource de connecteur IoT

|Message|Affiché|Condition|Fix| 
|-------|---------|---------|---|
|Le nombre maximal de types de ressources `iotconnectors` a été atteint.|API et portail Azure|Le quota d’abonnement du connecteur IoT est atteint (la valeur par défaut est 25 par abonnement).|Supprimez l’une des instances existantes du connecteur IoT. Utilisez un autre abonnement qui n’a pas atteint le quota d’abonnement. Demandez une augmentation du quota d’abonnement.
|Mappage non valide `deviceMapping` . Erreurs de validation : {liste d’erreurs}|API et portail Azure|Le `properties.deviceMapping` fourni dans la demande de configuration des ressources du connecteur IOT n’est pas valide.|Corrigez les erreurs dans le mappage JSON fourni dans la `properties.deviceMapping` propriété.
|`fullyQualifiedEventHubNamespace` a une valeur null, est vide ou n’est pas correctement mise en forme.|API et portail Azure|La demande d’approvisionnement du connecteur IoT `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` n’est pas valide.|Mettez à jour le connecteur IoT `properties.ingestionEndpointConfiguration.fullyQualifiedEventHubNamespace` au format approprié. Doit être `{YOUR_NAMESPACE}.servicebus.windows.net` .
|Les ressources ancêtres doivent être entièrement approvisionnées pour qu’une ressource enfant puisse être approvisionnée.|API|L’espace de travail parent est toujours en cours d’approvisionnement.|Attendez la fin de l’approvisionnement de l’espace de travail parent et soumettez à nouveau la demande d’approvisionnement.
|`Location` la propriété des ressources enfants doit correspondre à la `Location` propriété des ressources parentes.|API|La propriété de la demande d’approvisionnement du connecteur IoT `location` est différente de la propriété de l’espace de travail parent `location` .|Définissez la `location` propriété du connecteur IOT dans la demande d’approvisionnement sur la même valeur que la propriété de l’espace de travail parent `location` .

### <a name="destination-resource"></a>Ressource de destination

|Message|Affiché|Condition|Fix| 
|-------|---------|---------|---|
|Le nombre maximal de types de ressources `iotconnectors/destinations` a été atteint.|API et portail Azure|Le quota de ressources de destination du connecteur IoT est atteint et la valeur par défaut est 1 par connecteur IoT.|Supprimez l’instance existante de la ressource de destination du connecteur IoT. Une seule ressource de destination est autorisée par connecteur IoT.
|Le `fhirServiceResourceId` fourni n’est pas valide.|API et portail Azure|Le `properties.fhirServiceResourceId` fourni dans la demande de configuration de la ressource de destination n’est pas un ID de ressource valide pour une instance du service FHIR des API de santé Azure.|Assurez-vous que l’ID de ressource est correctement mis en forme et assurez-vous que l’ID de ressource est destiné à une instance FHIR Azure Healthcare API. Le format doit être : `/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/services/{FHIR_SERVER_NAME} or /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP_NAME}/providers/Microsoft.HealthcareApis/workspaces/{WORKSPACE_NAME}/`
|Les ressources ancêtres doivent être entièrement approvisionnées pour qu’une ressource enfant puisse être approvisionnée.|API|L’espace de travail parent ou le connecteur IoT parent est toujours en cours d’approvisionnement.|Attendez la fin de l’approvisionnement de l’espace de travail parent ou du connecteur IoT parent, puis soumettez à nouveau la demande d’approvisionnement.
|`Location` la propriété des ressources enfants doit correspondre à la `Location` propriété des ressources parentes.|API|La `location` propriété de la demande d’approvisionnement de destination est différente de la propriété du connecteur IOT parent `location` .|Affectez `location` à la propriété de la destination de la demande d’approvisionnement la même valeur que la propriété du connecteur IOT parent `location` .

## <a name="why-is-iot-connector-data-not-showing-up-in-the-fhir-service"></a>Pourquoi les données du connecteur IoT ne sont-elles pas affichées dans le service FHIR ?

|Problèmes potentiels|Correctifs|
|----------------|-----|
|Les données sont toujours en cours de traitement.|Les données sont sortie au service FHIR par lots (toutes les 15 minutes).  Il est possible que les données soient toujours en cours de traitement et que des temps supplémentaires soient nécessaires pour que les données soient rendues persistantes dans le service FHIR.|
|Le mappage de l’appareil n’a pas été configuré.|Configurez et enregistrez le mappage des appareils conformes.|
|Le mappage de destination FHIR n’a pas été configuré.|Configurez et enregistrez le mappage de destination FHIR conforme.|
|Le message de l’appareil ne contient pas d’expression attendue définie dans le mappage de l’appareil.|Vérifiez `JsonPath` les expressions définies dans les jetons de correspondance de mappage d’appareil définis dans le message de l’appareil.|
|Une ressource d’appareil n’a pas été créée dans le service FHIR (type de résolution : recherche uniquement) *.|Créez une ressource d’appareil valide dans le service FHIR. Vérifiez que la ressource de l’appareil contient un identificateur qui correspond à l’identificateur de l’appareil fourni dans le message entrant.|
|Une ressource patient n’a pas été créée dans le service FHIR (type de résolution : recherche uniquement) *.|Créez une ressource patient valide dans le service FHIR.|
|La `Device.patient` référence n’est pas définie, ou la référence n’est pas valide (type de résolution : recherche uniquement) *.|Assurez-vous que la ressource d’appareil contient une référence [valide](https://www.hl7.org/fhir/device-definitions.html#Device.patient) à une ressource patient.| 

* [Démarrage rapide : déployer un connecteur IOT à l’aide de portail Azure](deploy-iot-connector-in-azure.md) pour obtenir une description fonctionnelle des types de résolution du connecteur IOT (par exemple : recherche ou création).

### <a name="the-operation-performed-by-iot-connector"></a>Opération effectuée par le connecteur IoT

Cette propriété représente l’opération exécutée par le connecteur IoT lorsque l’erreur s’est produite. Une opération représente généralement l’étape de flux de données lors du traitement d’un message d’appareil. Vous trouverez ci-dessous une liste des valeurs possibles pour cette propriété.

> [!NOTE]
> Pour plus d’informations sur les différentes étapes du processus de transmission de données dans le connecteur IoT, consultez le lien de [données du connecteur IOT](iot-data-flow.md).

|Étape de flux de données|Description|
|---------------|-----------|
|Programme d’installation|L’étape de transmission de données d’installation est l’opération spécifique à la configuration de votre instance du connecteur IoT.|
|Normalisation|La normalisation est l’étape de transmission de données dans laquelle les données de l’appareil sont normalisées.|
|Regroupement|Étape de transfert de données de regroupement dans laquelle les données normalisées sont regroupées.|
|FHIRConversion|FHIRConversion est l’étape de transmission de données dans laquelle les données regroupées normalisées sont transformées en ressource FHIR.|
|Unknown|Inconnu est le type d’opération inconnu lorsqu’une erreur se produit.|

### <a name="the-severity-of-the-error"></a>Gravité de l’erreur

Cette propriété représente la gravité de l’erreur qui s’est produite. Vous trouverez ci-dessous une liste des valeurs possibles pour cette propriété.

|Gravité|Description|
|---------------|-----------|
|Avertissement|Il existe un problème mineur dans le processus de distribution de données, mais le traitement du message de l’appareil ne s’arrête pas.|
|Erreur|Ce message s’affiche lorsque le traitement d’un message d’appareil spécifique a rencontré une erreur et que d’autres messages peuvent continuer de s’exécuter comme prévu.|
|Critique|Cette erreur se présente quand un problème au niveau du système existe avec le connecteur IoT et qu’aucun message n’est censé être traité.|

### <a name="the-type-of-error"></a>Type d’erreur

Cette propriété signifie une catégorie pour une erreur donnée, laquelle représente fondamentalement un regroupement logique pour des types d’erreurs similaires. Vous trouverez ci-dessous une liste des valeurs possibles pour cette propriété.

|Type d’erreur|Description|
|----------|-----------|
|`DeviceTemplateError`|Ce type d’erreur est lié au mappage de l’appareil.|
|`DeviceMessageError`|Ce type d’erreur se produit lors du traitement d’un message d’appareil spécifique.|
|`FHIRTemplateError`|Ce type d’erreur est lié au mappage de destination FHIR|
|`FHIRConversionError`|Ce type d’erreur se produit lors de la transformation d’un message en ressource FHIR.|
|`FHIRResourceError`|Ce type d’erreur est lié aux ressources existantes dans le service FHIR qui sont référencées par le connecteur IoT.|
|`FHIRServerError`|Ce type d’erreur se produit lors de la communication avec le service FHIR.|
|`GeneralError`|Ce type d’erreur concerne tous les autres types d’erreurs.|

### <a name="the-name-of-the-error"></a>Nom de l’erreur

Cette propriété fournit le nom d’une erreur spécifique. Vous trouverez ci-dessous la liste de tous les noms d’erreur avec leur description et les types d’erreur, la gravité et la ou les étapes de transmission de données associés.

|Nom de l’erreur|Description|Type(s) d’erreur|Gravité de l’erreur|Étape(s) de flux de données|
|----------|-----------|-------------|--------------|------------------|
|`MultipleResourceFoundException`|Cette erreur se produit quand plusieurs ressources de patient ou de périphérique sont trouvées dans le service FHIR pour les identificateurs respectifs présents dans le message de l’appareil.|`FHIRResourceError`|Erreur|`FHIRConversion`|
|`TemplateNotFoundException`|Un périphérique ou un mappage de destination FHIR qui n’est pas configuré avec l’instance du connecteur IoT.|`DeviceTemplateError`, `FHIRTemplateError`|`Critical|Normalization`, `FHIRConversion`|
|`CorrelationIdNotDefinedException`|L’ID de corrélation n’est pas spécifié dans le mappage de l’appareil. `CorrelationIdNotDefinedException` est une erreur conditionnelle qui se produit uniquement lorsque l’observation FHIR doit regrouper les mesures de l’appareil à l’aide d’un ID de corrélation, car elle n’est pas configurée correctement.|`DeviceMessageError`|Error|Normalisation|
|`PatientDeviceMismatchException`|Cette erreur se produit lorsque la ressource de l’appareil sur le service FHIR a une référence à une ressource patient. Ce type d’erreur signifie qu’il ne correspond pas à l’identificateur de patient présent dans le message.|`FHIRResourceError`|Erreur|`FHIRConversionError`|
|`PatientNotFoundException`|Aucune ressource FHIR de patient n’est référencée par la ressource FHIR d’appareil associée à l’identificateur d’appareil présent dans le message de l’appareil. Remarque Cette erreur se produit uniquement lorsque l’instance de connecteur IoT est configurée avec le type de résolution de *recherche* .|`FHIRConversionError`|Erreur|`FHIRConversion`|
|`DeviceNotFoundException`|Il n’existe aucune ressource d’appareil sur le service FHIR associé à l’identificateur de périphérique présent dans le message de l’appareil.|`DeviceMessageError`|Error|Normalisation|
|`PatientIdentityNotDefinedException`|Cette erreur se produit lorsque l’expression pour analyser l’identificateur du patient à partir du message de l’appareil n’est pas configurée sur le mappage de l’appareil ou que l’identificateur du patient n’est pas présent dans le message de l’appareil. Remarque Cette erreur se produit uniquement lorsque le type de résolution du connecteur IoT est défini sur *créer*.|`DeviceTemplateError`|Critique|Normalisation|
|`DeviceIdentityNotDefinedException`|Cette erreur se produit lorsque l’expression qui analyse l’identificateur d’appareil à partir du message de l’appareil n’est pas configurée sur le mappage de l’appareil ou que l’identificateur de l’appareil n’est pas présent dans le message de l’appareil.|`DeviceTemplateError`|Critique|Normalisation|
|`NotSupportedException`|Une erreur s’est produite lors de la réception d’un message d’appareil avec un format non pris en charge.|`DeviceMessageError`|Error|Normalisation|

## <a name="creating-copies-of-iot-connector-device-and-fhir-destination-mappings"></a>Création de copies des mappages de l’appareil IoT Connector et de la destination FHIR

La copie des mappages de connecteur IoT peut être utile pour la modification et l’archivage en dehors du site Web Portail Azure.

Les copies de mappage doivent être fournies au support technique Azure lors de l’ouverture d’un ticket de support pour faciliter le processus de dépannage.

> [!NOTE]
> JSON est le seul format pris en charge pour les mappages de destination appareil et FHIR pour l’instant.

> [!TIP]
> En savoir plus sur les [mappages d’appareils](how-to-use-device-mappings.md) IOT Connector et les [mappages de destination FHIR](how-to-use-fhir-mappings.md)

1. Sélectionnez **« connecteurs IOT »** sur le côté gauche de l’espace de travail API de santé.

   :::image type="content" source="media/iot-troubleshoot/iot-connector-blade.png" alt-text="Sélectionnez connecteurs IoT." lightbox="media/iot-troubleshoot/iot-connector-blade.png":::

2. Sélectionnez le nom du **connecteur IOT** à partir duquel vous allez copier l’appareil et les mappages de destination FHIR.

   :::image type="content" source="media/iot-troubleshoot/map-files-select-connector-with-box.png" alt-text="IoT connector2" lightbox="media/iot-troubleshoot/map-files-select-connector-with-box.png":::

   > [!NOTE]
   > Ce processus peut également être utilisé pour copier et enregistrer le contenu du mappage de destination FHIR de **« destination »** .

3. Sélectionnez le contenu du JSON et effectuez une opération de copie (par exemple, appuyez sur **Ctrl + C**). 

   :::image type="content" source="media/iot-troubleshoot/map-files-select-device-json-with-box.png" alt-text="IoT connector4" lightbox="media/iot-troubleshoot/map-files-select-device-json-with-box.png":::

4. effectuez une opération de collage (par exemple, appuyez sur **Ctrl + V**) dans un nouveau fichier dans un éditeur comme Microsoft Visual Studio Code ou Bloc-notes. Veillez à enregistrer le fichier avec l’extension **. JSON** .

> [!TIP]
> Si vous ouvrez un ticket de [support technique Azure](https://azure.microsoft.com/support/create-ticket/) pour le connecteur IOT, veillez à inclure des copies de votre appareil et des mappages de destination FHIR pour faciliter le processus de dépannage.

## <a name="next-steps"></a>Étapes suivantes

>[!div class="nextstepaction"]
>[Vue d’ensemble du connecteur IoT](iot-connector-overview.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
