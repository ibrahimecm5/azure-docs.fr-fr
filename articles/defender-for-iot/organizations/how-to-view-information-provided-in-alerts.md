---
title: À propos des messages d’alerte
description: Sélectionnez une alerte dans la fenêtre Alertes pour passer en revue les détails.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: d76b10943932825388075b7aef44f0f5b94c1d53
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278566"
---
# <a name="about-alert-messages"></a>À propos des messages d’alerte

Sélectionnez une alerte dans la fenêtre **Alertes** pour consulter les détails de l’alerte. Les détails contiennent les informations suivantes :

- Métadonnées d’alerte

- Informations sur le trafic, les appareils et l’événement

- Liens vers les appareils connectés dans la carte des appareils

- Commentaires définis par les analystes et administrateurs de sécurité

- Recommandations pour l’examen de l’événement

## <a name="alert-metadata"></a>Métadonnées d’alerte

Les détails de l’alerte incluent les métadonnées d’alerte suivantes :

  - ID de l’alerte

  - Moteur de stratégie qui a déclenché l’alerte

  - Date et heure du déclenchement de l’alerte

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="Connectivité Internet non autorisée détectée.":::

## <a name="information-about-devices-traffic-and-the-event"></a>Informations sur les appareils, le trafic et l’événement

Le message d’alerte fournit des informations sur les éléments suivants :

  - Appareils détectés.

  - Le trafic détecté entre les appareils, comme les protocoles et les codes de fonction.

  - Aperçu des implications de l’événement.

Vous pouvez utiliser ces informations pour décider comment gérer l’événement d’alerte.

## <a name="links-to-connected-devices-in-the-device-map"></a>Liens vers les appareils connectés dans la carte des appareils

Pour en savoir plus sur les appareils connectés aux appareils détectés, vous pouvez sélectionner une image d’appareil dans l’alerte et afficher les appareils connectés dans la carte.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="L’opération RCP a échoué.":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="Capture d’écran des appareils.":::

La carte filtre sur l’appareil que vous avez sélectionné et les autres appareils qui y sont connectés. La carte affiche également la boîte de dialogue **Propriétés rapides** pour les appareils détectés dans les alertes.

## <a name="comments-defined-by-security-analysts-and-administrators"></a>Commentaires définis par les analystes et administrateurs de sécurité 

Les alertes peuvent inclure une liste de commentaires prédéfinis. Par exemple, les commentaires peuvent être des instructions pour les actions d’atténuation à entreprendre, ou les noms des personnes à contacter à propos de l’événement.

Lorsque vous gérez un événement d’alerte, vous pouvez choisir le ou les commentaires qui reflètent le mieux l’état de l’événement ou les étapes que vous avez suivies pour examiner l’alerte.

Les commentaires sélectionnés sont enregistrés dans le message d’alerte. L’utilisation de commentaires améliore la communication entre les individus et les équipes lors de l’investigation d’un événement d’alerte. Par conséquent, les commentaires peuvent accélérer le temps de réponse aux incidents.

Un administrateur ou un analyste de la sécurité prédéfinit les commentaires. Les commentaires sélectionnés ne sont pas transférés aux systèmes partenaires définis dans les règles de transfert.

Une fois que vous avez consulté les informations d’une alerte, vous pouvez effectuer différentes étapes d’investigation pour vous aider à gérer l’événement d’alerte. Par exemple :

- Analyser l’activité récente des appareils (rapport d’exploration de données). 

- Analyser les autres événements qui se sont produits en même temps (chronologie des événements). 

- Analyser le trafic complet des événements (fichier PCAP).

## <a name="pcap-files"></a>Fichiers PCAP

Dans certains cas, vous pouvez accéder à un fichier PCAP à partir du message d’alerte. Cela peut être utile si vous souhaitez obtenir des informations plus détaillées sur le trafic réseau associé.

Pour télécharger un fichier PCAP, sélectionnez l’:::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="icône de téléchargement"::: en haut à droite de la boîte de dialogue **Détails de l’alerte**.

## <a name="recommendations-for-investigating-an-event"></a>Recommandations pour l’examen d’un événement 

La zone **Recommandation** d’une alerte affiche des informations qui peuvent vous aider à mieux comprendre un événement. Passez en revue ces informations avant de gérer l’événement d’alerte ou d’entreprendre une action sur l’appareil ou le réseau.

## <a name="see-also"></a>Voir aussi

[Accélérer les workflows d’alerte](how-to-accelerate-alert-incident-response.md)

[Gérer l’événement d’alerte](how-to-manage-the-alert-event.md)
