---
title: Service d’Azure Machine Learning et connecteur IoT-api Azure Healthcare
description: dans cet article, vous allez apprendre à utiliser le connecteur IoT et le Service Azure Machine Learning
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 8150734243fda78805b5ef4cbf3ab318222b21cb
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894761"
---
# <a name="iot-connector-and-azure-machine-learning-service"></a>connecteur IoT et Service de Azure Machine Learning

dans cet article, nous allons explorer l’utilisation du connecteur IoT et du Service Azure Machine Learning.

## <a name="iot-connector-and-azure-machine-learning-service-reference-architecture"></a>architecture de référence des services IoT connector et Azure Machine Learning

Le connecteur IoT permet l’intégration transparente des appareils IoT avec les services FHIR&#174; (Fast Healthcare Interoperability Resources). Cette architecture de référence est conçue pour accélérer l’adoption de projets IoMT (Internet of Medical Things). cette solution utilise Azure Databricks pour le calcul Machine Learning (ML). toutefois, Azure ML Services avec Kubernetes ou une solution ML partenaire peut s’intégrer à l’environnement de notation Machine Learning.

Les couleurs à quatre lignes montrent les différentes parties du parcours des données.

- **Blue** = données IOT vers le service FHIR.
- **Vert** = chemin d’accès aux données d’évaluation des données IOT
- **Rouge** = chemin réactif des données pour informer les praticiens du risque des patients. L’objectif du chemin réactif doit être aussi proche que possible en temps réel.
- **Orange** = chemin d’accès à chaud pour les données. Toujours en charge des médecins dans le soin des patients. Les requêtes de données sont généralement déclenchées manuellement ou selon une planification d’actualisation.

:::image type="content" source="media/iot-concepts/iot-connector-machine-learning.png" alt-text="capture d’écran du connecteur IoT et de l’architecture de référence de Service Machine Learning." lightbox="media/iot-concepts/iot-connector-machine-learning.png":::

**Réception de données – étapes 1 à 5**

1. les données de l’appareil IoT ou via la passerelle de l’appareil sont envoyées à Azure IoT Hub/Azure IoT Edge.
2. données de Azure IoT Edge envoyées à Azure IoT Hub.
3. Copie des données d’appareil IoT brutes envoyées à un environnement de stockage sécurisé pour l’administration des appareils.
4. la charge utile IoMT PHI passe de Azure IoT Hub au connecteur IoT. Plusieurs services Azure sont représentés par une icône 1 connecteur IoT.
5. Trois parties à Number 5 : a. Le connecteur IoT demande une ressource patient du service FHIR. b. Le service FHIR envoie de nouveau la ressource patient au connecteur IoT. c. L’observation des patients IoT est enregistrée dans le service FHIR.

**itinéraire des données Machine Learning et ia – étapes 6 à 11**

6. flux de données non groupées normalisé envoyé à Azure Function (entrée ML).
7. Azure Function (entrée de ML) demande à la ressource Patient de fusionner avec la charge utile IoMT.
8. la charge utile IoMT avec PHI est envoyée au Hub d’événements pour la distribution à Machine Learning le calcul et le stockage.
9. la charge utile IoMT PHI est envoyée à Azure Data Lake Storage génération 2 pour l’observation des scores sur les fenêtres de temps plus longues.
10. La charge utile IoMT PHI est envoyée à Azure Databricks pour la fenêtrage, l’ajustement des données et la notation des données.
11. Le Azure Databricks demande plus de données de patients à Data Lake en fonction des besoins. a. Azure Databricks envoie également une copie des données notées à Data Lake.

**Coordination des notifications et des soins-étapes 12-18**

**Chemin à chaud**

12. Azure Databricks envoie une charge utile à une fonction Azure (sortie ML).
13. RiskAssessment et/ou indicateur de ressource soumis au service FHIR. a. Pour chaque fenêtre d’observation, une ressource RiskAssessment est envoyée au service FHIR. b. Pour les fenêtres d’observation pour lesquelles l’évaluation des risques est en dehors de la plage acceptable, une ressource de l’indicateur doit également être envoyée au service FHIR.
14. Données notées envoyées au référentiel de données pour le routage vers l’équipe de soins appropriée. Azure SQL Server est le référentiel de données utilisé dans cette conception en raison de son interaction native avec Power BI.
15. Power BI Le tableau de bord est mis à jour avec la sortie de l’évaluation des risques en moins de 15 minutes.

**Chemin d’accès chaud**

16. Power BI actualise le tableau de bord lors de la planification de l’actualisation des données. En général, plus de 15 minutes entre les actualisations.
17. Remplir l’application d’équipe de soins avec les données actuelles.
18. Coordination des soins par le biais de Microsoft Teams pour la santé application Patient.

## <a name="next-steps"></a>Étapes suivantes

dans cet article, vous avez appris le connecteur IoT et l’intégration du service Machine Learning. Pour obtenir une vue d’ensemble du connecteur IoT, consultez

>[!div class="nextstepaction"]
>[Vue d’ensemble du connecteur IoT](iot-connector-overview.md)

(FHIR&#174;) est une marque déposée de [HL7](https://hl7.org/fhir/) qui est utilisée avec l’autorisation de HL7.
