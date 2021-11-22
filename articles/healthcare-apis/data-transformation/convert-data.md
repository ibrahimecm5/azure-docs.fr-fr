---
title: Conversion de données pour les API de santé Azure
description: Utiliser les modèles de point de terminaison de données de $convert et de convertisseur de personnalisation pour convertir des données dans les API de santé
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 11/16/2021
ms.author: ranku
ms.openlocfilehash: a40a8f44ebb8e4297544ccd49c75acedfeabf1d1
ms.sourcegitcommit: 6f30424a4ab8dffc4e690086e898ab52bc4da777
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2021
ms.locfileid: "132902099"
---
# <a name="converting-your-data-to-fhir"></a>Conversion de vos données en FHIR

> [!IMPORTANT]
> Cette fonctionnalité est disponible en version préliminaire publique et elle est fournie sans contrat de niveau de service. Il n’est pas recommandé pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Le point de terminaison personnalisé de données $convert dans le service FHIR est destiné à la conversion de données de différents types de données en FHIR. Il utilise le moteur de modèle Liquid et les modèles du projet [FHIR Converter](https://github.com/microsoft/FHIR-Converter) comme modèles par défaut. Vous pouvez personnaliser ces modèles de conversion en fonction de vos besoins. Actuellement, il prend en charge deux types de conversion, **C-CDA à FHIR** et **HL7v2 pour** la conversion FHIR.

## <a name="use-the-convert-data-endpoint"></a>Utiliser le point de terminaison $convert-data

L' `$convert-data` opération est intégrée au service FHIR pour s’exécuter dans le cadre du service. Après l’activation `$convert-data` de sur votre serveur, vous pouvez effectuer des appels d’API au serveur pour convertir vos données en FHIR :

`https://<<FHIR service base URL>>/$convert-data`

### <a name="parameter-resource"></a>Ressource de paramètre

$convert-Data accepte une ressource de [paramètre](http://hl7.org/fhir/parameters.html) dans le corps de la demande, comme décrit dans le tableau ci-dessous. Dans le corps de la demande d’appel d’API, vous devez inclure les paramètres suivants :

| Nom du paramètre      | Description | Valeurs acceptées |
| ----------- | ----------- | ----------- |
| inputData      | Données à convertir. | Pour `Hl7v2` : chaîne <br> Pour `Ccda` : XML|
| inputDataType   | Type de données de l’entrée. | ```HL7v2```, ``Ccda`` |
| templateCollectionReference | Référence à une collection de modèles d' [images OCI ](https://github.com/opencontainers/image-spec) sur [Azure Container Registry (ACR)](https://azure.microsoft.com/services/container-registry/). Il s’agit de l’image contenant des modèles Liquid à utiliser pour la conversion. Il peut s’agir d’une référence à des modèles par défaut ou à une image de modèle personnalisée inscrite dans le service FHIR. Voir ci-dessous pour en savoir plus sur la personnalisation des modèles, leur hébergement sur ACR et l’inscription au service FHIR. | Pour les modèles par défaut : <br> Pour les modèles par défaut **HL7v2** : <br>```microsofthealth/fhirconverter:default``` <br>``microsofthealth/hl7v2templates:default``<br>Pour les modèles par défaut **C-CDA** : ``microsofthealth/ccdatemplates:default`` <br><br>Pour les modèles personnalisés : <br> \<RegistryServer\>/\<imageName\>@\<imageDigest\>, \<RegistryServer\>/\<imageName\>:\<imageTag\> |
| rootTemplate | Modèle racine à utiliser lors de la transformation des données. | Pour **HL7v2**:<br>```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04```<br><br> Pour **C-CDA**:<br>```CCD```, `ConsultationNote`, `DischargeSummary`, `HistoryandPhysical`, `OperativeNote`, `ProcedureNote`, `ProgressNote`, `ReferralNote`, `TransferSummary` |

> [!WARNING]
> Les modèles par défaut sont publiés sous licence MIT et **ne sont pas** pris en charge par les support Microsoft.
>
> Les modèles par défaut sont fournis uniquement pour vous aider à démarrer rapidement. Ils peuvent être mis à jour lors de la mise à jour des versions de l’API Azure pour FHIR. Par conséquent, vous devez vérifier le comportement de la conversion et **héberger votre propre copie des modèles** sur un Azure Container Registry, les inscrire à l’API Azure pour FHIR et les utiliser dans vos appels d’API afin d’obtenir un comportement de conversion de données cohérent entre les différentes versions de l’API Azure pour FHIR.

#### <a name="sample-request"></a>Exemple de demande

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

#### <a name="sample-response"></a>Exemple de réponse

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>Personnaliser des modèles

Vous pouvez utiliser l’[extension FHIR Converter](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) pour Visual Studio Code afin de personnaliser les modèles en fonction de vos besoins. L’extension fournit une expérience d’édition interactive et facilite le téléchargement de modèles et d’exemples de données publiés par Microsoft. Pour plus d’informations, reportez-vous à la documentation de l’extension.

## <a name="host-and-use-templates"></a>Héberger et utiliser des modèles

Il est fortement recommandé d’héberger votre propre copie des modèles sur ACR. Quatre étapes sont nécessaires pour héberger votre propre copie des modèles et les utiliser dans l’opération de $convert de données :

1. Envoyer (push) les modèles à votre instance Azure Container Registry
1. Activez l’identité managée sur votre instance de service FHIR.
1. Fournir l’accès de ACR à l’identité gérée du service FHIR.
1. Inscrivez les serveurs ACR dans le service FHIR.
1. Éventuellement, configurez le pare-feu ACR pour un accès sécurisé.

### <a name="push-templates-to-azure-container-registry"></a>Envoyer (push) les modèles vers Azure Container Registry

Après avoir créé une instance d’ACR, vous pouvez utiliser la commande _FHIR Converter: Push Templates_ dans l’[extension FHIR Converter](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) pour envoyer les modèles personnalisés vers ACR. Vous pouvez également utiliser l’[outil Template Management CLI](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md) à cet effet.

### <a name="enable-managed-identity-on-fhir-service"></a>Activer l’identité managée sur le service FHIR

Accédez à votre instance du service FHIR service dans le Portail Azure, puis sélectionnez le panneau **identité** .
Définissez l’état **sur activé** pour activer l’identité gérée dans le service FHIR.

![Activer une identité managée](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-fhir-service"></a>Fournir l’accès du service ACR à FHIR

1. Accédez au **panneau de contrôle d’accès (IAM)** .

1. Sélectionnez **Ajouter**, puis sélectionnez **Ajouter une attribution de rôle** pour ouvrir la page Ajouter une attribution de rôle.

1. Affectez le rôle [AcrPull](../../role-based-access-control/built-in-roles.md#acrpull) . 

   ![Page Ajouter une attribution de rôle](../../../includes/role-based-access-control/media/add-role-assignment-page.png) 

Pour plus d’informations sur l’affectation de rôles dans le Portail Azure, consultez [rôles intégrés Azure](../../role-based-access-control/role-assignments-portal.md).

### <a name="register-the-acr-servers-in-fhir-service"></a>Inscrire les serveurs ACR dans le service FHIR

Vous pouvez inscrire le serveur ACR à l’aide de l’Portail Azure ou à l’aide de l’interface CLI.

#### <a name="registering-the-acr-server-using-azure-portal"></a>Inscription du serveur ACR à l’aide de Portail Azure
accédez au panneau **Artifacts** sous **transformation des données** dans votre instance de service FHIR. La liste des serveurs ACR actuellement inscrits s’affiche. Sélectionnez **Ajouter**, puis sélectionnez votre serveur de Registre dans le menu déroulant. Vous devez sélectionner **Enregistrer** pour que l’inscription prenne effet. L’application de la modification et le redémarrage de votre instance peuvent prendre quelques minutes.

#### <a name="registering-the-acr-server-using-cli"></a>Inscription du serveur ACR à l’aide de l’interface CLI
Vous pouvez inscrire jusqu’à 20 serveurs ACR dans le service FHIR.

installez l’interface CLI des api de santé à partir de Azure PowerShell si nécessaire :

```powershell
az extension add -n healthcareapis
```

Inscrivez les serveurs ACR dans le service FHIR en suivant les exemples ci-dessous :

##### <a name="register-a-single-acr-server"></a>Inscrire un serveur ACR unique

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

##### <a name="register-multiple-acr-servers"></a>Inscrire plusieurs serveurs ACR

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```
### <a name="configure-acr-firewall"></a>Configurer le pare-feu ACR

Sélectionnez **mise en réseau** du compte de stockage Azure à partir du portail.

![configurer le pare-feu ACR](media/convert-data/networking-container-registry.png)

Sélectionnez **Réseaux sélectionnés**. 

Dans la section **pare-feu** , spécifiez l’adresse IP dans la zone **plage d’adresses** . Ajoutez des plages d’adresses IP pour autoriser l’accès à partir d’Internet ou de vos réseaux locaux. 

Dans le tableau ci-dessous, vous trouverez l’adresse IP de la région Azure dans laquelle le service FHIR est approvisionné.

|**Région Azure**         |**Adresse IP publique** |
|:----------------------|:-------------------|
| Australie Est       | 20.53.44.80       |
| Centre du Canada       | 20.48.192.84      |
| USA Centre           | 52.182.208.31     |
| USA Est              | 20.62.128.148     |
| USA Est 2            | 20.49.102.228     |
| USA Est 2 (EUAP)       | 20.39.26.254      |
| Allemagne Nord        | 51.116.51.33      |
| Allemagne Centre-Ouest | 51.116.146.216    |
| Japon Est           | 20.191.160.26     |
| Centre de la Corée        | 20.41.69.51       |
| Centre-Nord des États-Unis     | 20.49.114.188     |
| Europe Nord         | 52.146.131.52     |
| Afrique du Sud Nord   | 102.133.220.197   |
| États-Unis - partie centrale méridionale     | 13.73.254.220     |
| Asie Sud-Est       | 23.98.108.42      |
| Suisse Nord    | 51.107.60.95      |
| Sud du Royaume-Uni             | 51.104.30.170     |
| Ouest du Royaume-Uni              | 51.137.164.94     |
| Centre-USA Ouest      | 52.150.156.44     |
| Europe Ouest          | 20.61.98.66       |
| USA Ouest 2            | 40.64.135.77      |


> [!NOTE]
> Les étapes ci-dessus sont similaires aux étapes de configuration décrites dans le document Comment exporter des données FHIR. pour plus d’informations, consultez [secure Export to stockage Azure](./export-data.md#secure-export-to-azure-storage)

Pour un accès réseau privé (par exemple, un lien privé), vous pouvez également désactiver l’accès réseau public de ACR.
* Sélectionnez le panneau mise en réseau du compte de stockage Azure à partir du portail.
* Sélectionnez `Disabled`.
* sélectionnez exception de pare-feu : autoriser les services Microsoft approuvés à accéder à ce registre de conteneurs.

![lien privé pour ACR](media/convert-data/configure-private-network-container-registry.png)

### <a name="verify"></a>Vérification

Appelez l’API $convert-data en spécifiant votre référence de modèle dans le paramètre templateCollectionReference.

`<RegistryServer>/<imageName>@<imageDigest>`
