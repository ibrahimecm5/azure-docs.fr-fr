---
title: Intégrer ClearPass à Microsoft Defender pour IoT
description: Dans ce tutoriel, vous allez découvrir comment intégrer Microsoft Defender pour IoT à ClearPass.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 536e45171d8769980080d81c59c9f07192addab0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343626"
---
# <a name="tutorial-integrate-clearpass-with-microsoft-defender-for-iot"></a>Tutoriel : Intégrer ClearPass à Microsoft Defender pour IoT

Ce tutoriel va vous aider à intégrer ClearPass Policy Manager (CPPM) à Microsoft Defender pour IoT.

La plateforme Defender pour IoT offre des fonctionnalités de détection d’appareils et de supervision continue des menaces liées aux systèmes ICS. Elle associe une compréhension approfondie des applications, appareils et protocoles du secteur d’activité à la détection d’anomalie comportementale, au renseignement sur les menaces, à l’analyse des risques et à la modélisation automatisée des menaces spécifiques aux systèmes ICS.

Defender pour IoT détecte, identifie et classifie les points de terminaison OT (technologie opérationnelle) et ICS. Cette plateforme partage également les informations directement avec ClearPass à l’aide du framework ClearPass Security Exchange et d’une interface open API.

Defender pour IoT met automatiquement à jour la base de données de points de terminaison ClearPass Policy Manager avec les données de classification des points de terminaison et plusieurs attributs de sécurité personnalisés.

L’intégration permet d’effectuer les opérations suivantes :

- Visualisation des menaces de sécurité ICS et SCADA identifiées par les moteurs de sécurité Defender pour IoT.

- Visualisation des informations d’inventaire des appareils détectés par le capteur Defender pour IoT. Le capteur offre une visibilité centralisée de tous les appareils réseau ainsi que des points de terminaison des infrastructures IT (technologie de l’information) et OT (technologie opérationnelle). Vous pouvez ensuite définir et administrer un point de terminaison centralisé et une stratégie de sécurité de périphérie dans le système ClearPass.

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> - Créer un utilisateur d’API ClearPass
> - Créer un profil d’opérateur ClearPass
> - Créer un client d’API OAuth ClearPass
> - Configurer Defender pour IoT afin de l’intégrer à ClearPass
> - Définir la règle de réacheminement ClearPass
> - Superviser la communication entre ClearPass et Defender pour IoT

## <a name="prerequisites"></a>Prérequis

### <a name="aruba-clearpass-requirements"></a>Conditions requises d’Aruba ClearPass

CPPM s’exécute sur des appliances matérielles avec des logiciels préinstallés ou en tant que machine virtuelle sur les hyperviseurs suivants. Les hyperviseurs qui s’exécutent sur un ordinateur client tel que VMware Player ne sont pas pris en charge.

- VMware ESXi 5.5, 6.0, 6.5, 6.6 ou version ultérieure.

- Microsoft Hyper-V Server 2012 R2 ou 2016 R2.

- Hyper-V sur Microsoft Windows Server 2012 R2 ou 2016 R2.

- KVM sur CentOS 7.5 ou version ultérieure.

### <a name="defender-for-iot-requirements"></a>Conditions relatives à Defender pour IoT

- Defender pour IoT 2.5.1 ou version ultérieure.

- Un compte Azure. Si vous n’avez pas encore de compte Azure, vous pouvez [créer un compte Azure gratuit dès aujourd’hui](https://azure.microsoft.com/free/).

## <a name="create-a-clearpass-api-user"></a>Créer un utilisateur d’API ClearPass

Dans le cadre du canal de communication entre les deux produits, Defender pour IoT utilise de nombreuses API (TIPS et REST). L’accès aux API TIPS est validé via des informations d’identification combinant un nom d’utilisateur et un mot de passe. Cet identifiant utilisateur doit disposer de niveaux d’accès minimaux. N’utilisez pas un profil de super administrateur. Utilisez plutôt le profil d’administrateur d’API comme indiqué ci-dessous.

**Pour créer un utilisateur d’API ClearPass** :

1. Dans le volet gauche, sélectionnez **Administration** > **Users and Privileges**, puis sélectionnez **ADD**.

1. Dans la boîte de dialogue **Add Admin User**, définissez les paramètres suivants :

    :::image type="content" source="media/tutorial-clearpass/policy-manager.png" alt-text="Capture d’écran montrant la boîte de dialogue d’ajout d’un utilisateur administrateur.":::

    | Paramètre | Description |
    |--|--|
    | **l'UserId** | Entrez l’identifiant utilisateur. |
    | **Nom** | Entrer le nom d'utilisateur. |
    | **Mot de passe** | Entrez le mot de passe. |
    | **Activer un utilisateur** | Vérifiez que cette option est activée. |
    | **Niveau de privilège** | Sélectionnez **API Administrator**. |

1. Sélectionnez **Ajouter**.

## <a name="create-a-clearpass-operator-profile"></a>Créer un profil d’opérateur ClearPass

Defender pour IoT utilise l’API REST dans le cadre de l’intégration. Les API REST sont authentifiées dans un framework OAuth. Pour effectuer une synchronisation avec Defender pour IoT, vous devez créer un client d’API.

Afin de sécuriser l’accès à l’API REST pour le client d’API, créez un profil d’opérateur à accès restreint.

**Pour créer un profil d’opérateur ClearPass** :

1. Accédez à la fenêtre **Edit Operator Profile**.

1. Affectez à toutes les options la valeur **No Access**, à l’exception de ce qui suit :

| Paramètre | Description |
|--|--|
| **API Services** | Affecter la valeur **Allow Access** |
| **Policy Manager** | Spécifiez les paramètres suivants : <br />- **Dictionaries**: **Attributes** défini à **Read, Write, Delete**<br />- **Dictionaries**: **Fingerprintsset** défini à **Read, Write, Delete**<br />- **Identity**: **Endpoints** défini à **Read, Write, Delete** |

:::image type="content" source="media/tutorial-clearpass/api-profile.png" alt-text="Capture d’écran de modification du profil d’opérateur.":::

:::image type="content" source="media/tutorial-clearpass/policy.png" alt-text="Capture d’écran des options de l’écran Policy Manager.":::

## <a name="create-a-clearpass-oauth-api-client"></a>Créer un client d’API OAuth ClearPass

1. Dans la fenêtre principale, sélectionnez **Administrator** > **API Services** > **API Clients**.

1. Sous l’onglet Create API Client, définissez les paramètres suivants :

    - **Operating Mode** : ce paramètre est utilisé pour les appels d’API à ClearPass. Sélectionnez **ClearPass REST API - Client**.

    - **Operator Profile** : utilisez le profil que vous avez créé.

    - **Grant Type** : définissez **Client credentials (grant_type = client_credentials)** .

1. Veillez à noter la valeur de **Client Secret** et l’ID client. Par exemple : `defender-rest`.

    :::image type="content" source="media/tutorial-clearpass/aruba.png" alt-text="Capture d’écran de la création d’un client d’API.":::

1. Dans Policy Manager, vérifiez que vous avez collecté la liste d’informations ci-dessous avant de passer à l’étape suivante.

    - UserID CPPM

    - Mot de passe du UserId CPPM

    - ID client de l’API OAuth2 CPPM

    - Secret client de l’API OAuth2 CPPM

## <a name="configure-defender-for-iot-to-integrate-with-clearpass"></a>Configurer Defender pour IoT afin de l’intégrer à ClearPass

Pour permettre l’affichage de l’inventaire des appareils dans ClearPass, vous devez configurer la synchronisation entre Defender pour IoT et ClearPass. Une fois la configuration de la synchronisation effectuée, la plateforme Defender pour IoT met à jour ClearPass Policy Manager EndpointDb au fur et à mesure que de nouveaux points de terminaison sont détectés.

**Pour configurer la synchronisation ClearPass sur le capteur Defender pour IoT** :

1. Dans le capteur Defender pour IoT, sélectionnez **Paramètres système** dans le panneau latéral gauche.

1. Dans le volet **Paramètres système**, sélectionnez :::image type="content" source="media/tutorial-clearpass/clearpass-icon.png" alt-text="Capture d’écran de l’icône ClearPass sur le côté gauche.":::.

1. Définissez les paramètres suivants :

    :::image type="content" source="media/tutorial-clearpass/settings.png" alt-text="Capture d’écran montrant les informations nécessaires à remplir dans le volet Paramètres système.":::

    - **Activer la synchronisation** : activez la synchronisation entre Defender pour IoT et ClearPass

    - **Fréquence de synchronisation** : définissez la fréquence de synchronisation en minutes. La valeur par défaut est de 60 minutes. La valeur minimale est 5 minutes.

    - **Adresse IP de ClearPass** : adresse IP du système ClearPass auquel Defender pour IoT est synchronisé.

    - **ID client** : ID client créé sur ClearPass pour la synchronisation des données avec Defender pour IoT.

    - **Secret client** : secret client créé sur ClearPass pour la synchronisation des données avec Defender pour IoT.

    - **Nom d’utilisateur** : nom d’utilisateur de l’administrateur ClearPass.

    - **Mot de passe** : mot de passe de l’administrateur ClearPass.

1. Sélectionnez **Enregistrer**.

## <a name="define-a-clearpass-forwarding-rule"></a>Définir une règle de réacheminement ClearPass

Pour permettre la visualisation des alertes détectées par Defender pour IoT dans Aruba, vous devez définir la règle de réacheminement. Cette règle définit les informations relatives aux menaces de sécurité ICS et SCADA identifiées par les moteurs de sécurité Defender pour IoT et envoyées à ClearPass.

**Pour définir une règle de réacheminement ClearPass sur le capteur Defender pour IoT** :

1. Dans le capteur Defender pour IoT, sélectionnez **Réacheminement** dans le panneau gauche.

1. Dans le volet **Transfert**, sélectionnez **Créer une règle de transfert**.

    :::image type="content" source="media/tutorial-clearpass/forwarding.png" alt-text="Capture d’écran du volet Réacheminement avec toutes ses options.":::

1. Ajoutez le nom et la gravité de la règle, puis dans la liste déroulante **Action**, sélectionnez **Envoyer à** > **ClearPass**.

    :::image type="content" source="media/tutorial-clearpass/rule.png" alt-text="Capture d’écran de la création d’une règle de réacheminement.":::

1. Dans le volet **Actions**, définissez les paramètres suivants :

    :::image type="content" source="media/tutorial-clearpass/actions.png" alt-text="Sélectionnez vos actions dans le volet Actions.":::

    | Paramètre | Description |
    |--|--|
    | **Hôte** | Tapez l’adresse IP du serveur ClearPass. |
    | **Port** | Tapez le port du serveur ClearPass sur lequel le réacheminement est effectué. |
    | **Configurer** | Configurez les options suivantes pour permettre la visualisation des alertes Defender pour IoT dans le système ClearPass : <br />- **Signaler les codes de fonction non conformes** : violations de protocole - valeur de champ non conforme qui ne respecte pas la spécification de protocole ICS (attaque potentielle via du code malveillant exploitant une faille de sécurité).<br />- **Signaler les mises à jour non autorisées des microprogrammes/programmes PLC** : changements non autorisés apportés aux contrôleurs PLC.<br />- **Signaler l’arrêt non autorisé du PLC** : arrêt du contrôleur PLC (temps d’arrêt).<br />- **Signaler les alertes liées aux programmes malveillants** : signalement des programmes malveillants bien connus du secteur (TRITON, NotPetya, etc.).<br />- **Signaler l’analyse non autorisée** : signalement de toute analyse non autorisée (reconnaissance potentielle). |

1. Sélectionnez **Envoyer**.

## <a name="monitor-clearpass-and-defender-for-iot-communication"></a>Superviser la communication entre ClearPass et Defender pour IoT

Une fois que la synchronisation a démarré, les données de point de terminaison sont renseignées directement dans Policy Manager EndpointDb. Vous pouvez voir l’heure de la dernière mise à jour dans l’écran de configuration de l’intégration.

**Pour consulter l’heure de dernière synchronisation avec ClearPass** :

1. Connectez-vous au capteur Defender pour IoT.

1. Sélectionnez **Paramètres système** dans le panneau latéral gauche.

1. Sélectionnez **ClearPass**.

    :::image type="content" source="media/tutorial-clearpass/last-sync.png" alt-text="Capture d’écran de la vue montrant l’heure et la date de la dernière synchronisation.":::

Si la synchronisation ne fonctionne pas ou affiche une erreur, cela signifie probablement que vous n’avez pas capturé certaines informations. Revérifiez les données enregistrées. De plus, vous pouvez voir les appels d’API entre Defender pour IoT et ClearPass via **Guest** > **Administration** > **Support** > **Application Log**.

Vous trouverez ci-dessous un exemple illustrant les journaux d’appels d’API entre Defender pour IoT et ClearPass.

:::image type="content" source="media/tutorial-clearpass/log.png" alt-text="Capture d’écran des journaux d’appels d’API entre Defender pour IoT et ClearPass.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Il n’y a pas de ressources à nettoyer.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez découvert comment démarrer avec l’intégration de ClearPass. Poursuivez pour en savoir plus sur l’intégration de CyberArk.

> [!div class="nextstepaction"]
> [Bouton Étapes suivantes](./tutorial-cyberark.md)
