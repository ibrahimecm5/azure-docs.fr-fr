---
title: Utiliser Analyse de texte pour les conteneurs d’intégrité
titleSuffix: Azure Cognitive Services
description: Découvrez comment extraire et étiqueter des informations médicales localement à l’aide d’Analyse de texte pour les conteneurs d’intégrité Docker.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: b3ab4a3a3261fbd53d5294cf213823a369adbd3c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097579"
---
# <a name="use-text-analytics-for-health-containers"></a>Utiliser Analyse de texte pour les conteneurs d’intégrité

Les conteneurs vous permettent d’héberger Analyse de texte pour l’API d’intégrité sur votre propre infrastructure. Si vous avez des exigences de sécurité ou de gouvernance des données qui ne peuvent pas être satisfaites en appelant Analyse de texte pour l’intégrité à distance, les conteneurs peuvent être une bonne option.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Vous devez respecter les prérequis suivants avant d’utiliser les conteneurs Analyse de texte pour l’intégrité. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services/) avant de commencer.

* [Docker](https://docs.docker.com/) installé sur un ordinateur hôte. Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation. 
    * Sous Windows, vous devez également configurer Docker pour prendre en charge les conteneurs Linux.
    * Vous devez disposer d’une connaissance élémentaire des [concepts liés à Docker](https://docs.docker.com/get-started/overview/). 
* Une ressource de langue <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Créer une ressource de langue"  target="_blank"></a> avec le [niveau tarifaire](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) gratuit (F0) ou standard (S).

[!INCLUDE [Gathering required parameters](../../../containers/includes/container-gathering-required-parameters.md)]

## <a name="host-computer-requirements-and-recommendations"></a>Spécifications et recommandations relatives à l’ordinateur hôte

[!INCLUDE [Host Computer requirements](../../../../../includes/cognitive-services-containers-host-computer.md)]

Le tableau suivant décrit les spécifications minimales et recommandées pour les conteneurs Analyse de texte pour l’intégrité. Chaque processeur doit être cadencé à au moins 2,6 gigahertz (GHz). Les transactions par seconde (TPS) autorisées sont également indiquées.

|  | Configuration minimale pour l’hôte | Configuration recommandée pour l’hôte | TPS minimales | TPS maximales|
|---|---------|-------------|--|--|
| **1 document/requête**   |  4 cœurs, 10 Go de mémoire | 6 cœurs, 12 Go de mémoire |15 | 30|
| **10 documents/requêtes**   |  6 cœurs, 16 Go de mémoire | 8 cœurs, 20 Go de mémoire |15 | 30|

Le cœur d’UC et la quantité de mémoire correspondent aux paramètres `--cpus` et `--memory` qui sont utilisés dans le cadre de la commande `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Obtenir l’image conteneur avec `docker pull`

Utilisez la commande [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) pour télécharger cette image conteneur à partir du registre de conteneurs public de Microsoft.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```


[!INCLUDE [Tip for using docker list](../../../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="run-the-container-with-docker-run"></a>Exécuter le conteneur avec `docker run`

Une fois le conteneur sur l’ordinateur hôte, utilisez la commande [docker run](https://docs.docker.com/engine/reference/commandline/run/) pour exécuter les conteneurs. Chaque conteneur continuera à s’exécuter jusqu’à ce que vous l’arrêtiez.

> [!IMPORTANT]
> * les commandes docker dans les sections suivantes utilisent la barre oblique inverse, `\`, comme caractère de continuation de ligne. Remplacez-la ou supprimez-la en fonction des exigences de votre système d’exploitation hôte. 
> * Vous devez spécifier les options `Eula`, `Billing` et `ApiKey` pour exécuter le conteneur, sinon il ne démarrera pas.  Pour plus d'informations, consultez [Facturation](#billing).
>   * La reconnaissance de l'[IA responsable](/legal/cognitive-services/text-analytics/transparency-note-health) (RAI) doit également être présente avec une valeur de `accept`.
> * Les conteneurs d’analyse des sentiments et de détection de la langue utilisent la version v3 de l’API et sont en disponibilité générale. Le conteneur d’extraction de phrases clés utilise la version 2 de l’API et est en préversion.

Il existe plusieurs façons d’installer et d’exécuter le conteneur Analyse de texte pour la santé. 

- Utilisez le portail Azure pour créer une ressource linguistique, et utilisez Docker pour obtenir votre conteneur.
- Utilisez une machine virtuelle Azure avec Docker pour exécuter le conteneur. Reportez-vous à [Docker sur Azure](../../../../docker/index.yml).
- Utilisez les scripts PowerShell et Azure CLI suivants pour automatiser le déploiement des ressources et la configuration du conteneur.

### <a name="run-the-container-locally"></a>Exécutez localement le conteneur

Pour exécuter le conteneur dans votre propre environnement après avoir téléchargé l’image conteneur, exécutez la commande `docker run` suivante. Remplacez les espaces réservés suivants par vos valeurs :

| Espace réservé | Valeur | Format ou exemple |
|-------------|-------|---|
| **{API_KEY}** | Clé pour votre ressource Language. Cette information est disponible dans le portail Azure, sur la page **Key and endpoint** (Clé et point de terminaison) de votre ressource. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Point de terminaison pour accéder à l’API. Cette information est disponible dans le portail Azure, sur la page **Key and endpoint** (Clé et point de terminaison) de votre ressource. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest \
Eula=accept \
rai_terms=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Cette commande :

- Exécute le conteneur Analyse de texte pour la santé à partir de l’image conteneur
- Alloue 6 cœurs de processeur et 12 gigaoctets (Go) de mémoire
- Expose le port TCP 5000 et alloue un pseudo-TTY pour le conteneur
- Accepte le contrat de licence utilisateur final (CLUF) et les conditions générales de l’IA responsable
- Supprime automatiquement le conteneur après sa fermeture. L’image conteneur est toujours disponible sur l’ordinateur hôte.

### <a name="demo-ui-to-visualize-output"></a>Interface utilisateur de démonstration pour visualiser la sortie

Le conteneur fournit des API de point de terminaison de prédiction de requête basées sur REST.  Nous avons également fourni un outil de visualisation dans le conteneur qui est accessible en ajoutant `/demo` au point de terminaison du conteneur. Par exemple :

```
http://<serverURL>:5000/demo
```

Utilisez l’exemple de requête cURL ci-dessous pour envoyer une requête au conteneur que vous avez déployé en remplaçant la variable `serverURL` par la valeur appropriée.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Installer le conteneur à l’aide d’Azure Web App pour conteneurs

Azure [Web App pour conteneurs](https://azure.microsoft.com/services/app-service/containers/) est une ressource Azure dédiée à l’exécution de conteneurs dans le Cloud. Il offre des fonctionnalités prêtes à l’emploi, telles que la mise à l’échelle automatique, la prise en charge des conteneurs Docker et la composition Docker, la prise en charge de HTTPS et bien plus encore.

> [!NOTE]
> À l’aide d’Azure Web App, vous obtiendrez automatiquement un domaine sous la forme `<appservice_name>.azurewebsites.net`

Exécutez ce script PowerShell au moyen de la Azure CLI pour créer un Web App pour conteneurs, à l’aide de votre abonnement et de l’image de conteneur sur HTTPS. Attendez la fin du script (environ 25 à 30 minutes) avant d’envoyer la première requête.

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Language resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Language resource.
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME 
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Installer le conteneur à l’aide d’Azure Container Instance

Vous pouvez également utiliser Azure Container Instance (ACI) pour faciliter le déploiement. ACI est une ressource qui vous permet d’exécuter des conteneurs Docker à la demande dans un environnement Azure serverless géré. 

Consultez [Comment utiliser Azure Container Instances](../../../containers/azure-container-instance-recipe.md) pour connaître les étapes de déploiement d’une ressource ACI à l’aide du Portail Azure. Vous pouvez également utiliser le script PowerShell ci-dessous avec Azure CLI, ce qui crée une ACI sur votre abonnement à l’aide de l’image conteneur.  Attendez la fin du script (environ 25 à 30 minutes) avant d’envoyer la première requête.  En raison de la limite du nombre maximal de processeurs par ressource ACI, ne sélectionnez pas cette option si vous envisagez de soumettre plus de 5 documents volumineux (environ 5 000 caractères chacun) par requête.
Pour plus d’informations sur la disponibilité, consultez l’article [Support régional ACI](../../../../container-instances/container-instances-region-availability.md). 

> [!NOTE] 
> Azure Container Instances n’inclue pas la prise en charge HTTPS pour les domaines intégrés. Si vous avez besoin de HTTPS, vous devez le configurer manuellement, y compris créer un certificat et l’inscription d’un domaine. Vous trouverez des instructions pour effectuer cette opération avec NGINX ci-dessous.

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Language resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Language resource.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_IMAGE_NAME = "mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept rai_terms=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Sécuriser la connectivité ACI

Par défaut, aucune sécurité n’est fournie lors de l’utilisation d’ACI avec l’API de conteneur. Cela est dû au fait que les conteneurs s’exécute généralement dans le cadre d’un pod qui est protégé de l’extérieur par un pont réseau. Vous pouvez toutefois modifier un conteneur avec un composant frontal, en gardant le point de terminaison de conteneur privé. Les exemples suivants utilisent [NGINX](https://www.nginx.com) en tant que passerelle d’entrée pour prendre en charge le protocole HTTPS/SSL et l’authentification client-certificat.

> [!NOTE]
> NGINX est un serveur et un proxy HTTP open source à hautes performances. Un conteneur NGINX peut être utilisé pour mettre fin à une connexion TLS pour un seul conteneur. Des solutions de terminaison TLS basées sur l’entrée NGINX plus complexes sont également possibles.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Configurer NGINX en tant que passerelle d’entrée

NGINX utilise des [fichiers config](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) pour activer les fonctionnalités lors du runtime. Pour activer la terminaison TLS pour un autre service, vous devez spécifier un certificat SSL pour mettre fin à la connexion TLS et `proxy_pass` pour spécifier une adresse pour le service. Un exemple est fourni ci-dessous.


> [!NOTE]
> `ssl_certificate` attend la spécification d’un chemin d’accès dans le système de fichiers local du conteneur NGINX. L’adresse spécifiée pour `proxy_pass` doit être disponible dans le réseau du conteneur NGINX.

Le conteneur NGINX chargera tous les fichiers dans `_.conf_` qui sont montés sous `/etc/nginx/conf.d/` dans le chemin de configuration HTTP.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Exemple de fichier Docker Compose

L’exemple ci-dessous montre comment créer un fichier [Docker Compose](https://docs.docker.com/compose/reference/overview) pour déployer les conteneurs NGINX et les conteneurs d’intégrité :

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Pour initier ce fichier Docker Compose, exécutez la commande suivante à partir d’une console au niveau racine du fichier :

```bash
docker-compose up
```

Pour plus d’informations, consultez la documentation de NGINX sur la [terminaison SSL NGINX](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).

[!INCLUDE [Running multiple containers on the same host](../../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Interroger le point de terminaison de prédiction du conteneur

Le conteneur fournit des API de point de terminaison de prédiction de requête basées sur REST.

Utilisez l’hôte, `http://localhost:5000`, pour les API de conteneur.

[!INCLUDE [Container's API documentation](../../../../../includes/cognitive-services-containers-api-documentation.md)]


### <a name="structure-the-api-request-for-the-container"></a>Structurer la requête d’API pour le conteneur

Vous pouvez utiliser Postman ou l’exemple de requête cURL ci-dessous pour envoyer une requête au conteneur que vous avez déployé, en remplaçant la variable `serverURL` par la valeur appropriée.  Notez que la version de l’API dans l’URL du conteneur est différente de celle de l’API hébergée.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Le code JSON suivant est un exemple de fichier JSON joint au corps POST de la requête d’Analyse de texte pour l’intégrité :

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Corps de la réponse du conteneur

Le code JSON suivant est un exemple de corps de la réponse de l’Analyse de texte pour la santé à l’issue de l’appel synchrone conteneurisé :

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "name": "remdesivir",
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "DRUGBANK",
                            "id": "DB14761"
                        },
                        {
                            "dataSource": "GS",
                            "id": "6192"
                        },
                        {
                            "dataSource": "MEDCIN",
                            "id": "398132"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "d09540"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "MTHSPL",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        },
                        {
                            "dataSource": "NDDF",
                            "id": "018308"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "2284718"
                        },
                        {
                            "dataSource": "SNOMEDCT_US",
                            "id": "870592005"
                        },
                        {
                            "dataSource": "VANDF",
                            "id": "4039395"
                        }
                    ]
                },
                {
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0
                },
                {
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.94
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/0",
                            "role": "Dosage"
                        },
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        }
                    ]
                },
                {
                    "relationType": "RouteOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/2",
                            "role": "Route"
                        }
                    ]
                },
                {
                    "relationType": "TimeOfMedication",
                    "entities": [
                        {
                            "ref": "#/documents/0/entities/1",
                            "role": "Medication"
                        },
                        {
                            "ref": "#/documents/0/entities/3",
                            "role": "Time"
                        }
                    ]
                }
            ],
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2021-03-01"
}
```

## <a name="run-the-container-with-client-library-support"></a>Exécuter le conteneur avec prise en charge des bibliothèques de client

À partir de la version du conteneur `3.0.017010001-onprem-amd64` (ou si vous utilisez le conteneur`latest`), vous pouvez exécuter l’Analyse de texte pour le conteneur d’intégrité à l’aide de la [bibliothèque cliente](../quickstart.md). Pour ce faire, ajoutez le paramètre suivant à la commande `docker run` :

`enablelro=true`

Par la suite, lorsque vous authentifiez l’objet client, utilisez le point de terminaison sur lequel votre conteneur s’exécute :

`http://localhost:5000`

Par exemple, si vous utilisez C#, vous pouvez utiliser le code suivant :

```csharp
var client = new TextAnalyticsClient("http://localhost:5000", "your-text-analytics-key");
```

## <a name="stop-the-container"></a>Arrêter le conteneur

[!INCLUDE [How to stop the container](../../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Dépannage

Si vous exécutez le conteneur avec un [montage](configure-containers.md#mount-settings) de sortie et la journalisation activée, il génère des fichiers journaux qui sont utiles pour résoudre les problèmes qui se produisent lors du démarrage ou de l’exécution du conteneur.

[!INCLUDE [Cognitive Services FAQ note](../../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Facturation

Les conteneurs Analyse de texte pour l’intégrité envoient des informations de facturation à Azure à l’aide d’une ressource _Language_ sur votre compte Azure. 

[!INCLUDE [Container's Billing Settings](../../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

## <a name="summary"></a>Résumé

Dans cet article, vous avez découvert des concepts et le flux de travail pour le téléchargement, l’installation et l’exécution des conteneurs Analyse de texte pour l’intégrité. En résumé :

* Analyse de texte pour l’intégrité fournit un conteneur Linux pour l’arrimeur
* Les images conteneurs sont téléchargées à partir de Microsoft Container Registry (MCR).
* Les images conteneurs s’exécutent dans Docker.
* Vous pouvez utiliser l’API REST ou le kit SDK pour appeler des opérations dans des conteneurs Analyse de texte pour l’intégrité en spécifiant l’URI hôte du conteneur.
* Vous devez spécifier les informations de facturation lors de l’instanciation d’un conteneur.

> [!IMPORTANT]
> Les conteneurs Cognitives Services ne sont pas concédés sous licence pour s’exécuter sans être connectés à Azure pour le contrôle. Les clients doivent configurer les conteneurs de manière à ce qu’ils communiquent les informations de facturation au service de contrôle à tout moment. Les conteneurs Cognitive Services ne transmettent aucune donnée client (p. ex., le texte analysé) à Microsoft.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir les paramètres de configuration, consultez [Configurer des conteneurs](configure-containers.md).
