---
title: Utiliser des Conteneurs Azure Cognitive Services localement
titleSuffix: Azure Cognitive Services
description: Découvrez comment utiliser les conteneurs Docker afin d’utiliser Cognitive Services localement.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: cog-serv-seo-aug-2020, ignite-fall-2021
ms.service: cognitive-services
ms.topic: article
ms.date: 09/24/2021
ms.author: aahi
keywords: local, Docker, conteneur, Kubernetes
ms.openlocfilehash: 0057888a85acc2356660fecbf8a77071401e8e3c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033472"
---
# <a name="azure-cognitive-services-containers"></a>Conteneurs Azure Cognitive Services

Azure Cognitive Services offre plusieurs [conteneurs Docker](https://www.docker.com/what-container) qui vous permettent d’utiliser localement les mêmes API que celles disponibles dans Azure. Grâce à ces conteneurs, Cognitive Services se trouve au plus près de vos données du point de vue de la conformité, de la sécurité et de l’exploitation. La prise en charge des conteneurs est actuellement disponible pour une partie d’Azure Cognitive Services.

> [!VIDEO https://www.youtube.com/embed/hdfbn4Q8jbo]

La mise en conteneur est une méthode de distribution de logiciels dans laquelle une application ou un service, y compris les dépendances et la configuration, est packagé en tant qu’image conteneur. L’image conteneur peut être déployée sur un hôte conteneur avec peu ou pas de modifications. Les conteneurs sont isolés les uns des autres et du système d’exploitation sous-jacent, avec une empreinte inférieure à celle d’une machine virtuelle. Vous pouvez instancier des conteneurs à partir d’images conteneurs pour les tâches à court terme et les supprimer quand vous n’en avez plus besoin.

## <a name="features-and-benefits"></a>Fonctionnalités et avantages

- **Infrastructure immuable** : Permettez aux équipes DevOps de tirer parti d’un ensemble cohérent et fiable de paramètres système connus, tout en étant en mesure de s’adapter aux changements. Les conteneurs offrent la flexibilité nécessaire pour évoluer dans un écosystème prévisible et éviter la dérive de configuration.
- **Contrôle des données** : Choisissez l’endroit où vos données sont traitées par Cognitive Services. Cela peut être important si vous ne pouvez pas envoyer de données au cloud, mais que vous avez besoin d’accéder aux API Cognitive Services. Cohérence de la prise en charge dans les environnements hybrides : entre les données, la gestion, l’identité et la sécurité.
- **Contrôle des mises à jour du modèle** : Flexibilité dans le contrôle de version et la mise à jour des modèles déployés dans leurs solutions.
- **Architecture portable** : Permet la création d’une architecture d’application portable qui peut être déployée dans le cloud, localement et en périphérie. Les conteneurs peuvent être déployés directement sur [Azure Kubernetes Service](../aks/index.yml), sur [Azure Container Instances](../container-instances/index.yml) ou sur un cluster [Kubernetes](https://kubernetes.io/) déployé sur [Azure Stack](/azure-stack/operator). Pour plus d’informations, consultez [Déployer Kubernetes sur Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).
- **Débit élevé / faible latence** : fournir aux clients la possibilité de mettre à l’échelle pour des exigences de débit élevé et de faible latence en permettant l’exécution de Cognitive Services physiquement proche de leur logique d’application et de leurs données. Les conteneurs ne limitent pas les transactions par seconde (TPS) et peuvent être configurés pour effectuer un scale-up ou un scale-out afin de gérer la demande si vous fournissez les ressources matérielles nécessaires.
- **Scalabilité** : Avec la popularité croissante des logiciels d’orchestration de conteneur et de conteneurisation, comme Kubernetes, la scalabilité se retrouve au premier plan des avancées technologiques. Fort d’une base de cluster scalable, le développement d’applications satisfait à la haute disponibilité.

## <a name="containers-in-azure-cognitive-services"></a>Conteneurs dans Azure Cognitive Services

Les conteneurs Azure Cognitive Services fournissent l’ensemble suivant de conteneurs Docker, chacun contenant un sous-ensemble de fonctionnalités provenant des services d’Azure Cognitive Services. Vous trouverez des instructions et des emplacements d’images dans les tableaux ci-dessous. Une liste d’[images conteneur](containers/container-image-tags.md) est également disponible.

### <a name="decision-containers"></a>Conteneurs de décisions

| Service |  Conteneur | Description | Disponibilité |
|--|--|--|--|
| [Détecteur d’anomalies][ad-containers] | **Détecteur d’anomalies** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-decision-anomaly-detector))  | L’API Détecteur d’anomalies vous permet de surveiller et de détecter des anomalies dans vos données de série chronologique grâce à l’apprentissage automatique. | Mise à la disposition générale |

### <a name="language-containers"></a>Conteneurs de langues

| Service |  Conteneur | Description | Disponibilité |
|--|--|--|--|
| [LUIS][lu-containers] |  **LUIS** ([image](https://go.microsoft.com/fwlink/?linkid=2043204&clcid=0x409)) | Charge un modèle Language Understanding entraîné ou publié, également connu sous le nom d’application LUIS, dans un conteneur docker et fournit l’accès aux prédictions de requête à partir des points de terminaison d’API du conteneur. Vous pouvez collecter les journaux d’activité de requêtes du conteneur et les charger sur le [portail LUIS](https://www.luis.ai) pour améliorer la précision de prédiction de l’application. | Mise à la disposition générale |
| [Service Language][ta-containers-keyphrase] | **Extraction de phrases clés** ([image](https://go.microsoft.com/fwlink/?linkid=2018757&clcid=0x409)) | Extraire des expressions clés pour identifier les points principaux. Par exemple, pour le texte d’entrée « Le repas était délicieux et le personnel adorable », l’API renvoie les principaux points de discussion : « repas » et « personnel adorable ». | Préversion |
| [Service Language][ta-containers-language] |  **Détection de la langue du texte** ([image](https://go.microsoft.com/fwlink/?linkid=2018759&clcid=0x409)) | Détecter la langue (parmi 120 langues maximum) dans laquelle le texte d’entrée est rédigé et générer un code de langue unique pour chaque document envoyé dans la demande. Le code de langue est associé à un score indiquant la puissance de l’analyse. | Mise à la disposition générale |
| [Service Language][ta-containers-sentiment] | **Analyse des sentiments** ([image](https://go.microsoft.com/fwlink/?linkid=2018654&clcid=0x409)) | Analyser le texte brut pour obtenir des indices sur un sentiment positif ou négatif. Cette version d’Analyse des sentiments renvoie des étiquettes de sentiment (par exemple, *positif* ou *négatif*) pour chaque document et phrase qu’elle contient. |  Mise à la disposition générale |
| [Service Language][ta-containers-health] |  **Analyse de texte pour la santé** | Extraire et étiqueter des informations médicales à partir de textes cliniques non structurés. | Préversion |
| [Translator][tr-containers] | **Translator** | Traduisez du texte en plusieurs langues et dialectes. | Préversion contrôlée. [Demandez l’accès](https://aka.ms/csgate-translator). | 

### <a name="speech-containers"></a>Conteneurs Speech

> [!NOTE]
> Pour utiliser des conteneurs Speech, vous devez remplir un [formulaire de requête en ligne](https://aka.ms/csgate).

| Service |  Conteneur | Description | Disponibilité |
|--|--|--|
| [API Speech Service][sp-containers-stt] |  **Reconnaissance vocale** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Retranscrit une voix en un texte de façon continue et en temps réel. | Mise à la disposition générale |
| [API Speech Service][sp-containers-cstt] | **Reconnaissance vocale personnalisée** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-speech-to-text)) | Retranscrit une voix en un texte de façon continue et en temps réel à l’aide d’un modèle personnalisé. | Mise à la disposition générale |
| [API Speech Service][sp-containers-tts] | **Synthèse vocale** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-text-to-speech)) | Convertit le texte en paroles naturelles. | Mise à la disposition générale |
| [API Speech Service][sp-containers-ctts] | **Synthèse vocale personnalisée** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-custom-text-to-speech)) | Convertit le texte en paroles naturelles à l’aide d’un modèle personnalisé. | Préversion contrôlée |
| [API Speech Service][sp-containers-ntts] | **Synthèse vocale neuronale** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-neural-text-to-speech)) | Convertit du texte en parole naturelle grâce à la technologie de réseau neuronal profond qui permet d’obtenir une parole synthétisée plus naturelle. | Mise à la disposition générale |
| [API Speech Service][sp-containers-lid] | **Détection de langue vocale** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-speechservices-language-detection)) | Détermine la langue de l’audio parlé. | Préversion contrôlée |

### <a name="vision-containers"></a>Conteneurs de vision

> [!WARNING]
> Le 11 juin 2020, Microsoft a annoncé qu’il ne vendra pas de technologie de reconnaissance faciale aux services de police des États-Unis tant qu’un règlement strict fondé sur les droits de l’homme n’aura pas été promulgué. Par conséquent, les clients ne doivent pas utiliser les fonctionnalités de reconnaissance faciale ni les fonctionnalités comprises dans les services Azure comme Visage ou Video Indexer, s’ils représentent, ou autorisent l’utilisation de ces services par ou pour, un service de police des États-Unis.

| Service |  Conteneur | Description | Disponibilité |
|--|--|--|--|
| [Vision par ordinateur][cv-containers] | **Lire OCR** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-read)) | Le conteneur Read OCR vous permet d’extraire du texte imprimé et manuscrit à partir d’images et de documents avec la prise en charge des formats de fichier JPEG, PNG, BMP, PDF et TIFF. Pour plus d’informations, consultez la [documentation sur l’API Read](./computer-vision/overview-ocr.md). | Préversion contrôlée. [Demandez l’accès][request-access]. |
| [Analyse spatiale][spa-containers] | **Analyse spatiale** ([image](https://hub.docker.com/_/microsoft-azure-cognitive-services-vision-spatial-analysis)) | Analyser le flux vidéo en temps réel afin de comprendre les relations spatiales entre les personnes, leurs déplacements et les interactions avec des objets dans des environnements physiques. | Préversion |
| [Visage][fa-containers] | **Visage** | Détecter les visages humains sur des images et identifier les attributs, y compris les caractéristiques (par exemple, le nez et les yeux), le sexe, l’âge et d’autres caractéristiques du visage prévues par la machine. En plus de la détection, le conteneur Visage peut vérifier si deux visages sur la même image ou sur des images différentes sont identiques à l’aide d’un score de confiance, ou de comparer des visages par rapport à une base de données pour voir si un visage similaire ou identique existe déjà. Il peut également regrouper des visages similaires à l’aide de caractéristiques visuelles partagées. | Non disponible |

<!--
|[Personalizer](./personalizer/what-is-personalizer.md) |F0, S0|**Personalizer** ([image](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409))|Azure Personalizer is a cloud-based API service that allows you to choose the best experience to show to your users, learning from their real-time behavior.|
-->

En outre, certains conteneurs sont pris en charge dans l’offre de [ressources à plusieurs services](cognitive-services-apis-create-account.md) de Cognitive Services. Vous pouvez créer une ressource Cognitive Services tout-en-un et utiliser la même clé de facturation entre les services pris en charge pour les services suivants :

* Vision par ordinateur
* Face
* LUIS
* Analyse de texte

## <a name="prerequisites"></a>Prérequis

Vous devez respecter les prérequis suivants avant d’utiliser des conteneurs Azure Cognitive Services :

**Moteur Docker** : le moteur Docker doit être installé localement. Docker fournit des packages qui configurent l’environnement Docker sur [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms) et [Windows](https://docs.docker.com/docker-for-windows/). Sur Windows, vous devez configurer Docker pour prendre en charge les conteneurs Linux. Les conteneurs Docker peuvent également être déployés directement sur [Azure Kubernetes Service](../aks/index.yml) ou sur [Azure Container Instances](../container-instances/index.yml).

Vous devez configurer Docker pour permettre aux conteneurs de se connecter à Azure et de lui envoyer des données de facturation.

**Connaissance de Microsoft Container Registry et de Docker** : vous devez avoir une compréhension élémentaire des concepts de Microsoft Container Registry et de Docker, notamment les registres, dépôts, conteneurs et images conteneur, ainsi qu’une maîtrise des commandes `docker` de base.

Pour apprendre les principes de base de Docker et des conteneurs, consultez la [vue d’ensemble de Docker](https://docs.docker.com/engine/docker-overview/).

Les conteneurs individuels peuvent aussi avoir leurs propres exigences, notamment sur le serveur et l’allocation de mémoire.

[!INCLUDE [Cognitive Services container security](containers/includes/cognitive-services-container-security.md)]

## <a name="developer-samples"></a>Exemples de développement

Des exemples de développement sont disponibles sur notre [dépôt GitHub](https://github.com/Azure-Samples/cognitive-services-containers-samples).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [recettes de conteneur](containers/container-reuse-recipe.md) que vous pouvez utiliser avec Cognitive Services.

Installer et explorer les fonctionnalités fournies par les conteneurs dans Azure Cognitive Services :

* [Conteneurs Détecteur d’anomalies][ad-containers]
* [Conteneurs Vision par ordinateur][cv-containers]
* [Conteneurs Visage][fa-containers]
* [Conteneurs Language Understanding (LUIS)][lu-containers]
* [Conteneurs API Speech Service][sp-containers]
* [Conteneurs de services Language][ta-containers]
* [Conteneurs Translator][tr-containers]

<!--* [Personalizer containers](https://go.microsoft.com/fwlink/?linkid=2083928&clcid=0x409)
-->

[ad-containers]: anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/overview.md
[lu-containers]: luis/luis-container-howto.md
[sp-containers]: speech-service/speech-container-howto.md
[spa-containers]: ./computer-vision/spatial-analysis-container.md
[sp-containers-lid]: speech-service/speech-container-howto.md?tabs=lid
[sp-containers-stt]: speech-service/speech-container-howto.md?tabs=stt
[sp-containers-cstt]: speech-service/speech-container-howto.md?tabs=cstt
[sp-containers-tts]: speech-service/speech-container-howto.md?tabs=tts
[sp-containers-ctts]: speech-service/speech-container-howto.md?tabs=ctts
[sp-containers-ntts]: speech-service/speech-container-howto.md?tabs=ntts
[ta-containers]: language-service/overview.md#deploy-on-premises-using-docker-containers
[ta-containers-keyphrase]: language-service/key-phrase-extraction/how-to/use-containers.md
[ta-containers-language]: language-service/language-detection/how-to/use-containers.md
[ta-containers-sentiment]: language-service/sentiment-opinion-mining/how-to/use-containers.md
[ta-containers-health]: language-service/text-analytics-for-health/how-to/use-containers.md
[tr-containers]: translator/containers/translator-how-to-install-container.md
[request-access]: https://aka.ms/csgate
