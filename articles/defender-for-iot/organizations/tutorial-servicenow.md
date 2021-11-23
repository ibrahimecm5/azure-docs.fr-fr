---
title: Intégrer ServiceNow à Microsoft Defender pour IoT
description: Dans ce tutoriel, vous allez découvrir comment intégrer ServiceNow à Microsoft Defender pour IoT.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 9b01aa92848bc69bff37dc75be8e59350bc9f3f3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343550"
---
# <a name="tutorial-integrate-servicenow-with-microsoft-defender-for-iot"></a>Tutoriel : Intégrer ServiceNow à Microsoft Defender pour IoT

Ce tutoriel va vous permettre de découvrir comment intégrer ServiceNow à Microsoft Defender pour IoT et comment l’utiliser.

L’intégration de Defender pour IoT avec ServiceNow offre un nouveau niveau de visibilité, de surveillance et de contrôle centralisés pour le paysage IoT et OT. Ces plateformes reliées par un pont activent la visibilité automatisée des appareils et la gestion des menaces pour les appareils ICS et IoT qui n’étaient pas accessibles précédemment.

La base de données de gestion de la configuration (CMDB) de ServiceNow est enrichie et complétée par un ensemble complet d’attributs d’appareil qui sont envoyés (push) par la plateforme Defender pour IoT. Ceci garantit une visibilité complète et continue du paysage des appareils. Cette visibilité vous permet de superviser et de répondre depuis un volet unique. 

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Télécharger l’application Defender pour IoT dans ServiceNow
> * Configurer Defender pour IoT pour qu’il communique avec ServiceNow
> * Créer des jetons d’accès dans ServiceNow
> * Envoyer des attributs d’appareil Defender pour IoT à ServiceNow
> * Configurer l’intégration en utilisant un proxy HTTPS
> * Afficher les détections Defender pour IoT dans ServiceNow
> * Afficher les unités connectées

## <a name="prerequisites"></a>Prérequis

### <a name="software-requirements"></a>Configuration logicielle requise

Accès à ServiceNow et à Defender pour IoT 

- ServiceNow Service Management version 3.0.2.

- Correctif Defender pour IoT 2.8.11.1 ou ultérieur.

> [!Note]
> Si vous utilisez déjà une intégration Defender pour IoT et ServiceNow et que vous effectuez une mise à niveau à l’aide de la console de gestion locale, les données précédemment reçues des capteurs Defender pour IoT devraient être effacées dans ServiceNow.

### <a name="architecture"></a>Architecture

- **Architecture de la console de gestion locale** : Configurez une console de gestion locale pour communiquer avec une instance de ServiceNow. La console de gestion locale envoie (Push) les données de capteur à l’application Defender pour IoT à l’aide de l’API REST.

    Pour configurer votre système pour qu’il fonctionne avec une console de gestion locale, vous devez désactiver les configurations de la synchronisation ServiceNow, des règles de transfert et du proxy sur les capteurs où elles ont été effectuées.

- **Architecture des capteurs** : Si vous voulez configurer votre environnement pour inclure une communication directe entre les capteurs et ServiceNow, pour chaque capteur, définissez la configuration de la synchronisation ServiceNow, des règles de transfert et du proxy (si un proxy est nécessaire).

## <a name="download-the-defender-for-iot-application-in-servicenow"></a>Télécharger l’application Defender pour IoT dans ServiceNow

Pour accéder à l’application Defender for IoT dans ServiceNow, vous devez télécharger l’application auprès du magasin d’applications ServiceNow. 

**Pour accéder à l’application Defender pour IoT dans ServiceNow** :

1. Accédez au [Magasin d’applications ServiceNow](https://store.servicenow.com/).

1. Recherchez `Defender for IoT` ou `CyberX IoT/ICS Management`.

   :::image type="content" source="media/tutorial-servicenow/search-results.png" alt-text="Capture d’écran de l’écran de recherche dans ServiceNow.":::

1. Sélectionnez l’application.

   :::image type="content" source="media/tutorial-servicenow/cyberx-app.png" alt-text="Capture d’écran des résultats dans l’écran de recherche.":::

1. Sélectionnez **Request App** (Demander l’application).

   :::image type="content" source="media/tutorial-servicenow/sign-in.png" alt-text="Connectez-vous à l’application à l’aide de vos informations d’identification.":::

1. Connectez-vous et téléchargez l’application.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>Configurer Defender pour IoT pour qu’il communique avec ServiceNow

Configurez Defender pour IoT afin qu’il envoie les informations d’alerte aux tables ServiceNow. Les alertes Defender pour IoT vont apparaître dans ServiceNow en tant qu’incidents de sécurité. Pour cela, définissez une règle de transfert Defender pour IoT afin d’envoyer les informations d’alerte à ServiceNow.

**Pour envoyer les informations d’alerte aux tables ServiceNow** :

1. Connectez-vous à la console de gestion locale.

1. Dans le volet de gauche, sélectionnez **Forwarding** (Transfert).

1. Sélectionnez le bouton :::image type="icon" source="media/tutorial-servicenow/plus-icon.png" border="false":::.

    :::image type="content" source="media/tutorial-servicenow/forwarding-rule.png" alt-text="Capture d’écran de la fenêtre Create Forwarding Rule.":::

1. Entrez un nom pour la règle.

1. Définissez les critères selon lesquels Defender pour IoT déclenchera la règle de transfert. L’utilisation de critères de règle de transfert permet d’identifier et de gérer le volume d’informations envoyé à ServiceNow par Defender pour IoT. Les options suivantes sont disponibles :

    - **Niveaux de gravité :** Incident de niveau de sécurité minimal à transférer. Par exemple, si l’option **Minor** (Mineur) est sélectionnée, les alertes mineures et toute alerte avec un niveau de gravité supérieur seront transférées. Les niveaux sont prédéfinis par Defender pour IoT.

    - **Protocoles :** Déclenche uniquement la règle de transfert si le trafic détecté s’exécutait sur des protocoles spécifiques. Sélectionnez les protocoles requis dans la liste déroulante ou sélectionnez-les tous.

    - **Moteurs :** Sélectionnez les moteurs requis ou choisissez-les tous. Les alertes des moteurs sélectionnés seront envoyées.

1. Vérifiez que l’option **Notifications d’alerte de rapport** est sélectionnée.

1. Dans la section Actions, sélectionnez **Ajouter** puis **ServiceNow**.

    :::image type="content" source="media/tutorial-servicenow/select-servicenow.png" alt-text="Sélectionnez ServiceNow dans les options de la liste déroulante.":::

1. Entrez les paramètres d’action de ServiceNow :

    :::image type="content" source="media/tutorial-servicenow/parameters.png" alt-text="Renseignez les paramètres de l’action ServiceNow.":::

1. Dans le volet **Actions**, définissez les paramètres suivants :

      | Paramètre | Description |
      |--|--|
      | Domain | Entrez l’adresse IP du serveur ServiceNow. |
      | Nom d’utilisateur | Entrez le nom d’utilisateur du serveur ServiceNow. |
      | Mot de passe | Entrez le mot de passe du serveur ServiceNow. |
      | ID client | Entrez l’ID client que vous avez reçu pour Defender pour IoT dans la page **Registres des applications** dans ServiceNow. |
      | Clé secrète client | Entrez la chaîne de secret client que vous avez créée pour Defender pour IoT dans la page **Registres des applications** dans ServiceNow. |
      | Type de rapport | **Incidents** : Transférer une liste des alertes présentées dans ServiceNow avec un ID d’incident et une brève description de chaque alerte.<br /><br />**Application Defender pour IoT** : Transférer toutes les informations d’alerte, notamment les détails sur le capteur, le moteur, les adresses source et de destination. Les informations sont transférées à Defender pour IoT sur l’application ServiceNow. |

1. Sélectionnez **SAVE** (Enregistrer). 

Les alertes Defender pour IoT vont maintenant apparaître en tant qu’incidents dans ServiceNow.

## <a name="create-access-tokens-in-servicenow"></a>Créer des jetons d’accès dans ServiceNow

Un jeton est nécessaire pour autoriser à ServiceNow à communiquer avec Defender pour IoT.

Vous aurez besoin du `Client ID` et du `Client Secret` que vous avez entrés lors de la création des règles de transfert de Defender pour IoT. Les règles de transfert transfèrent les informations d’alerte à ServiceNow, et lors de la configuration de Defender pour IoT pour envoyer les attributs d’appareil aux tables ServiceNow.

## <a name="send-defender-for-iot-device-attributes-to-servicenow"></a>Envoyer des attributs d’appareil Defender pour IoT à ServiceNow

Configurez Defender pour IoT afin qu’il envoie un ensemble étendu d’attributs d’appareil aux tables ServiceNow. Pour envoyer des attributs à ServiceNow, vous devez mapper votre console de gestion locale à une instance ServiceNow. Cela garantit que la plateforme Defender pour IoT peut communiquer et s’authentifier auprès de l’instance.

**Pour ajouter une instance ServiceNow** :

1. Connectez-vous à votre console de gestion locale Defender pour IoT.

1. Sélectionnez **Paramètres système**, puis **ServiceNow** dans la section Intégration de la console de gestion locale.

      :::image type="content" source="media/tutorial-servicenow/servicenow.png" alt-text="Capture d’écran du bouton de sélection de ServiceNow.":::

1. Entrez les paramètres de synchronisation suivants dans la boîte de dialogue de synchronisation ServiceNow.

    :::image type="content" source="media/tutorial-servicenow/sync.png" alt-text="Capture d’écran de la boîte de dialogue de synchronisation de ServiceNow":::.

     Paramètre | Description |
    |--|--|
    | Activer la synchronisation | Activez et désactivez la synchronisation après avoir défini les paramètres. |
    | Fréquence de synchronisation (minutes) | Par défaut, les informations sont envoyées (Push) à ServiceNow toutes les 60 minutes. La valeur minimale est 5 minutes. |
    | Instance ServiceNow | Entrez l’URL de l’instance ServiceNow. |
    | ID client | Entrez l’ID client que vous avez reçu pour Defender pour IoT dans la page **Registres des applications** dans ServiceNow. |
    | Clé secrète client | Entrez la chaîne de secret client que vous avez créée pour Defender pour IoT dans la page **Registres des applications** dans ServiceNow. |
    | Nom d’utilisateur | Entrez le nom d’utilisateur pour cette instance. |
    | Mot de passe | Entrez le mot de passe pour cette instance. |

1. Sélectionnez **SAVE** (Enregistrer).

Vérifiez que la console de gestion locale est connectée à l’instance ServiceNow en examinant la date de la dernière synchronisation.

:::image type="content" source="media/tutorial-servicenow/sync-confirmation.png" alt-text="Capture d’écran de la communication qui se produit lors de l’examen de la dernière synchronisation.":::

## <a name="set-up-the-integrations-using-an-https-proxy"></a>Configurer les intégrations en utilisant un proxy HTTPS

Lors de la configuration de l’intégration de Defender pour IoT et ServiceNow, la console de gestion locale et le serveur ServiceNow communiquent à l’aide du port 443. Si le serveur ServiceNow se trouve derrière le proxy, le port par défaut ne peut pas être utilisé.

Defender pour IoT prend en charge un proxy HTTPS dans l’intégration ServiceNow en permettant la modification du port par défaut utilisé pour l’intégration.

**Pour configurer le proxy** :

1. Modifiez les propriétés globales sur la console de gestion locale en utilisant la commande suivante :

    ```bash
    sudo vim /var/cyberx/properties/global.properties
    ```

2. Ajoutez les paramètres suivants :

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Sélectionnez **Save and Exit** (Enregistrer et quitter).

4. Réinitialisez la console de gestion locale en utilisant la commande suivante : 

    ```bash
    sudo monit restart all
    ```

Une fois les configurations définies, toutes les données ServiceNow sont transférées en utilisant le proxy configuré.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>Afficher les détections Defender pour IoT dans ServiceNow

Cet article décrit les attributs d’appareil et les informations d’alerte présentés dans ServiceNow.

**Pour visualiser les attributs d’appareil** :

1. Connectez-vous à ServiceNow.

2. Accédez à **Plateforme CyberX**.

3. Accédez à **Inventory** (Inventaire) ou à **Alert** (Alerte).

   [:::image type="content" source="media/tutorial-servicenow/alert-list.png" alt-text="Capture d’écran de Inventory ou Alert.":::](media/tutorial-servicenow/alert-list.png#lightbox)

## <a name="view-connected-devices"></a>Afficher les unités connectées

Pour afficher les appareils connectés :

1. Sélectionnez un appareil, puis sélectionnez l’**Appliance** listée pour cet appareil.

    :::image type="content" source="media/tutorial-servicenow/appliance.png" alt-text="Capture d’écran de l’appliance souhaitée dans la liste.":::

1. Dans la boîte de dialogue **Détails de l’appareil**, sélectionnez **Appareils connectés**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Il n’y a pas de ressources à nettoyer.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment bien démarrer avec l’intégration de ServiceNow. Poursuivez pour en savoir plus sur notre[intégration de Cisco](./tutorial-forescout.md).

> [!div class="nextstepaction"]
> [Bouton Étapes suivantes](./tutorial-forescout.md)
