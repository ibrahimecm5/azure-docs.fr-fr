---
title: Configurer une solution basée sur un agent Microsoft Defender pour IoT
description: Découvrez comment configurer la collecte de données dans une solution Microsoft Defender pour IoT basée sur un agent
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 65e3febe4a1715ee1a55b377a5c54cbb4f3f88c6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306203"
---
# <a name="configure-microsoft-defender-for-iot-agent-based-solution"></a>Configurer une solution basée sur un agent Microsoft Defender pour IoT  

Cet article explique comment configurer la collecte de données dans une solution Microsoft Defender pour IoT basée sur un agent.

## <a name="configure-data-collection"></a>Configurer la collecte de données

Pour configurer la collecte de données dans une solution Microsoft Defender pour IoT basée sur un agent : 

1. Accédez au portail Azure, puis sélectionnez le hub IoT auquel Defender pour IoT est attaché. 

1. Dans le menu  **Sécurité** , sélectionnez **Paramètres**. 

1. Sélectionnez  **Collecte de données**. 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="Sélectionnez une collecte de données dans les paramètres du menu Sécurité.":::

## <a name="geolocation-and-ip-address-handling"></a>Gestion de la géolocalisation et des adresses IP 

Pour sécuriser votre solution IoT, les adresses IP des connexions entrantes et sortantes de vos appareils IoT, IoT Edge ainsi que des hubs IoT, sont collectées et stockées par défaut. Ces informations sont essentielles et servent à détecter une connectivité anormale à partir de sources d’adresses IP suspectes. Par exemple lorsque des tentatives sont effectuées pour établir des connexions à partir d’une source d’adresses IP d’un botnet connu ou d’une source d’adresses IP en dehors de votre géolocalisation. Le service Defender pour IoT offre la flexibilité nécessaire pour activer et désactiver la collecte des données d’adresses IP à tout moment. 

Pour activer ou désactiver la collecte des données d’adresses IP : 

1. Ouvrez votre hub IoT, puis sélectionnez  **Paramètres**  dans le menu  **Sécurité** . 

1. Sélectionnez l’écran  **Collecte de données**  et modifiez la géolocalisation ainsi que les paramètres de gestion des adresses IP selon vos besoins. 

## <a name="log-analytics-creation"></a>Création de Log Analytics 

Defender pour IoT vous permet de stocker les alertes de sécurité, les recommandations et les données de sécurité brutes dans votre espace de travail Log Analytics. L’ingestion Log Analytics dans IoT Hub est définie par défaut sur **off** (désactivée) dans la solution Defender pour IoT. Il est possible d’attacher Defender pour IoT à un espace de travail Log Analytics et d’y stocker les données de sécurité. 

Par défaut, deux types d’informations sont stockés par Defender pour IoT dans votre espace de travail Log Analytics :
 
- Alertes de sécurité.

- Recommandations. 

Vous pouvez choisir d’ajouter le stockage d’informations supplémentaires de type `raw events`. 

> [!Note] 
> Le stockage `raw events`  dans Log Analytics engendre des frais de stockage supplémentaires. 

Pour permettre à Log Analytics de fonctionner avec le micro-agent : 

1. Accédez à **Configuration de l’espace de travail** >  **Collecte de données**, puis placez le commutateur sur  **Activé**. 

1. Créez un espace de travail Log Analytics, ou attachez-en un existant. 

1. Vérifiez que l’option  **Accéder aux données de sécurité brutes**  est sélectionnée.  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="Assurez-vous que l’option Accéder aux données de sécurité brutes est sélectionnée.":::

1. Sélectionnez  **Enregistrer**.

Chaque mois, les cinq 5 premiers gigaoctets de données ingérées par client sur le service Azure Log Analytics sont gratuits. Chaque gigaoctet de données ingérées dans votre espace de travail Azure Log Analytics est conservé gratuitement pendant les 31 premiers jours. Pour plus d’informations sur la tarification, consultez [Tarification de Log Analytics](https://azure.microsoft.com/pricing/details/monitor/). 

Pour modifier la configuration de l’espace de travail de Log Analytics : 

1. Dans le menu **Sécurité** de votre hub IoT, sélectionnez  **Paramètres**. 

1. Sélectionnez l’écran  **Collecte de données**  et modifiez les paramètres de configuration de l’espace de travail de Log Analytics selon vos besoins. 

Pour accéder à vos alertes dans votre espace de travail Log Analytics après la configuration :

1. Sélectionnez une alerte dans Defender pour IoT.

1. Sélectionnez **Examiner les alertes dans l’espace de travail Log Analytics**.

Pour accéder à vos alertes dans votre espace de travail Log Analytics après la configuration :

1. Sélectionnez une recommandation dans Defender pour IoT.

1. Sélectionnez **Examiner les recommandations dans l’espace de travail Log Analytics**. 
 
Pour plus d’informations sur l’interrogation des données à partir de Log Analytics, consultez  [Bien démarrer avec les requêtes de journal dans Azure Monitor](../../azure-monitor/logs/get-started-queries.md). 

## <a name="turn-off-defender-for-iot"></a>Désactiver Defender pour IoT 

Pour activer ou désactiver un service Defender pour IoT sur un hub IoT spécifique : 

1. Dans le menu **Sécurité** de votre hub IoT, sélectionnez  **Paramètres**.

1. Sélectionnez l’écran  **Collecte de données**  et modifiez les paramètres de configuration de l’espace de travail de Log Analytics selon vos besoins.

## <a name="next-steps"></a>Étapes suivantes 

Passez à l’article suivant pour [configurer votre solution](quickstart-configure-your-solution.md).
