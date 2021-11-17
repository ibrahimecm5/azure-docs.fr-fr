---
title: Gérer les événements d’alerte
description: Gérez les événements d’alerte détectés au sein de votre réseau.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 64f8abc405b80c86107b8e90503d148286a1ea8c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339798"
---
# <a name="manage-alert-events"></a>Gérer les événements d’alerte

Les options suivantes sont disponibles pour gérer les événements d’alerte :

 | Action | Description |
 |--|--|
 | **Learn** | Autoriser l’événement détecté. Pour plus d’informations, consultez la section [Apprendre et désapprendre des événements](#about-learning-and-unlearning-events). |
 | **Reconnaître** | Masquer l’alerte une seule fois pour l’événement détecté. L’alerte sera de nouveau déclenchée si l’événement est de nouveau détecté. Pour plus d’informations, consultez la section [Reconnaitre et cesser de reconnaître des événements](#about-acknowledging-and-unacknowledging-events). |
 | **Désactiver le son** | Ignorer continuellement l’activité avec des appareils identiques et un trafic comparable. Pour plus d’informations, consultez la section [Désactiver et réactiver des événements](#about-muting-and-unmuting-events). |
 
Vous pouvez également exporter des informations sur l’alerte.
## <a name="about-learning-and-unlearning-events"></a>Apprendre et désapprendre des événements

Les événements qui indiquent des écarts du réseau appris peuvent refléter des modifications valides du réseau. Lorsque vous souhaitez approuver ces modifications, vous pouvez demander à Defender pour IoT d’*apprendre* le comportement. Les exemples doivent inclure :

- Une nouvelle activité a été détectée sur l’appareil existant. Par exemple, un appareil autorisé a tenté d’accéder à une nouvelle ressource sur un autre appareil.

- Un nouvel appareil est ajouté au réseau.  

- Modifications de la version du microprogramme suivant les procédures de maintenance standard.

- Un nouvel appareil a effectué une opération de lecture/d’écriture sur un contrôleur de destination.

- Un nouvel appareil effectue une opération de lecture/d’écriture sur un contrôleur de destination et doit être défini comme un appareil de programmation.

- Une nouvelle analyse légitime est effectuée, et l’appareil doit être défini comme un appareil d’analyse.

**Pour en savoir plus sur un événement** : 

1. Accédez à l’onglet **Alertes**.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-tab.png" alt-text="Sélectionnez l’onglet Alertes dans la barre de navigation à gauche de l’écran.":::

1. Sélectionnez une alerte dans la liste des alertes.

1. Sélectionnez **En savoir plus**.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/learn.png" alt-text="Fenêtre Analyse d’adresse détectée.":::

L’activité reflétée dans les alertes est calculée lorsque vous générez des rapports d’exploration de données, d’évaluation des risques et de vecteur d’attaque. Lorsque vous gérez ces événements, le capteur met à jour les rapports en conséquence.

Lorsque vous sélectionnez **Apprendre**, le capteur considère le trafic, les configurations ou l’activité comme valides. L’alerte n’est plus déclenchée pour l’événement. Par ailleurs, l’événement ne sera pas calculé lorsque le capteur génère les rapports d’évaluation des risques, des vecteurs d’attaque, etc.

Les événements appris peuvent être désappris. Quand le capteur désapprend un événement, il redéclenche les alertes liées à cet événement.

**Pour ne rien savoir sur un événement**

1. Accédez à l’onglet **Alertes**.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-tab.png" alt-text="Sélectionnez l’onglet Alertes dans la barre de navigation à gauche de l’écran.":::

1. Dans le menu déroulant d’affichage, sélectionnez **Reconnu**.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/view-acknowledged.png" alt-text="Sélectionnez Reconnu dans le menu déroulant de la section d’affichage." lightbox="media/how-to-work-with-alerts-sensor/view-acknowledged-expanded.png":::

1. Sélectionnez une alerte dans la liste des alertes.

1. Sélectionnez **Désapprendre**.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/unlearn.png" alt-text="Sélectionnez Désapprendre pour ne rien savoir sur un événement.":::

Une fois qu’un événement est désappris, il revient sur la page Vue principale.

**Pour voir si une alerte a été apprise ou reconnue** :

1. Accédez à l’onglet Chronologie de l’événement :::image type="icon" source="media/how-to-work-with-alerts-sensor/event-timeline.png" border="false":::

1. Localisez l’alerte dans la chronologie.

    :::image type="content" source="media/how-to-work-with-alerts-sensor/event-timeline-acknowledged.png" alt-text="Vous pouvez rechercher les événements reconnus dans la chronologie des événements.":::

    Vous verrez dans la fenêtre de l’alerte si l’alerte a été apprise ou reconnue.
 
## <a name="about-acknowledging-and-unacknowledging-events"></a>Reconnaitre et cesser de reconnaître des événements

Dans certaines situations, il peut arriver que vous ne souhaitiez pas qu’un capteur apprenne un événement détecté ou que l’option ne soit pas disponible. L’incident peut nécessiter une atténuation à la place. Par exemple :

- **Atténuer une configuration réseau ou un appareil** : vous recevez une alerte indiquant qu’un nouvel appareil a été détecté sur le réseau. Après enquête, vous découvrez qu’il s’agit d’un appareil réseau non autorisé. Vous gérez l’incident en déconnectant l’appareil du réseau.
- **Mettre à jour une configuration de capteur** : vous recevez une alerte indiquant qu’un serveur a initié un nombre excessif de connexions distantes. Cette alerte a été déclenchée car les seuils d’anomalie du capteur ont été définis pour déclencher des alertes au-delà d’un certain nombre de sessions par minute. Vous gérez l’incident en mettant à jour les seuils.

Après atténuation ou enquête, vous pouvez demander au capteur de masquer l’alerte en sélectionnant **Reconnaître**. L’alerte sera de nouveau déclenchée si l’événement est de nouveau détecté.

Pour effacer l’alerte :

  - Sélectionnez **Reconnaître**.

Pour afficher de nouveau l’alerte :

  - Accédez à l’alerte, puis sélectionnez **Cesser de reconnaître**.

Cessez de reconnaître des alertes s’il est nécessaire de mener une enquête plus approfondie.

## <a name="about-muting-and-unmuting-events"></a>Désactiver et réactiver des événements

Dans certaines circonstances, vous pouvez demander à votre capteur d’ignorer un scénario spécifique sur votre réseau. Par exemple :

  - Le moteur d’**anomalie** déclenche une alerte suite à l’observation d’un pic d’utilisation de la bande passante entre deux appareils, mais le pic est valide pour les appareils.

  - Le moteur de **violation de protocole** déclenche une alerte sur un écart de protocole détecté entre deux appareils, mais l’écart est valide entre les appareils.

Dans ces situations, l’apprentissage n’est pas disponible. Lorsqu’il n’est pas possible de procéder à l’apprentissage et que vous souhaitez supprimer l’alerte et retirer l’appareil du calcul des risques et vecteurs d’attaque, vous pouvez désactiver l’événement d’alerte à la place.

> [!NOTE] 
> Vous ne pouvez pas désactiver les événements pour lesquels un appareil Internet est défini comme source ou destination.

### <a name="what-alert-activity-is-muted"></a>Pour quelle activité d’alerte le son a-t-il été coupé ?

Un scénario de désactivation inclut les appareils réseau et le trafic détectés pour un événement. Le titre de l’alerte décrit le trafic en cours de désactivation.

Le ou les appareils en cours de désactivation apparaissent sous forme d’images dans l’alerte. Si deux appareils apparaissent, le trafic d’alerte spécifique entre eux est désactivé.

**Exemple 1**

Lorsqu’un événement est désactivé, il est ignoré chaque fois que l’appareil principal (source) envoie à l’appareil secondaire (destination) un code de fonction illégal tel que défini par le fournisseur.

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="Appareil secondaire reçu.":::

**Exemple 2**

Lorsqu’un événement est désactivé, il est ignoré chaque fois que la source envoie un en-tête HTTP avec un contenu illégal, quelle que soit la destination.

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="Capture d’écran d’un contenu d’en-tête HTTP illégal.":::

**Lorsqu’un événement est désactivé :**

- l’alerte reste accessible dans l’affichage des alertes **reconnues** jusqu’à sa réactivation.

- L’action de désactivation apparaît dans la **chronologie des événements**.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="Événement détecté et désactivé.":::

- Le capteur recalcule les appareils lors de la génération des rapports d’évaluation des risques, des vecteurs d’attaque, etc. Par exemple, si vous avez désactivé une alerte ayant détecté du trafic malveillant sur un appareil, ce dernier ne sera pas calculé dans le rapport d’évaluation des risques.

**Pour désactiver et réactiver une alerte :**

- Sélectionnez l’icône **Désactiver** sur l’alerte.

**Pour afficher les alertes désactivées :**

1. Sélectionnez l’option **Reconnues** dans l’écran principal des **alertes**.

2. Pointez sur une alerte pour voir si elle est désactivée.  

## <a name="export-alert-information"></a>Envoyer des informations d’alerte

Exportez les informations d’alerte dans un fichier .csv. Vous pouvez exporter les informations de toutes les alertes détectées ou exporter des informations en fonction de la vue filtrée. Les informations suivantes sont exportées :

- Adresse source
- Adresse de destination
- Titre de l’alerte
- Gravité de l’alerte
- Message d’alerte
- Informations supplémentaires
- État de l’accusé de réception
- Disponibilité de PCAP

Pour exporter :

1. Sélectionnez Alertes dans le menu latéral.

1. Sélectionnez Exporter.

1. Sélectionnez Exporter les alertes étendues pour exporter les informations d’alerte dans des lignes distinctes pour chaque alerte qui couvre plusieurs appareils. Lorsque l’option Exporter les alertes étendues est sélectionnée, le fichier .csv crée une ligne dupliquée de l’événement d’alerte avec les éléments uniques de chaque ligne. Grâce à cette option, il est plus facile d’examiner les événements d’alerte exportés.

## <a name="see-also"></a>Voir aussi

[Types et descriptions d’alertes](alert-engine-messages.md)

[Contrôler quel trafic est surveillé](how-to-control-what-traffic-is-monitored.md)
