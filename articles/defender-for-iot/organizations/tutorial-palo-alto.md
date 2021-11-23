---
title: Intégrer Palo Alto à Microsoft Defender pour IoT
description: Defender pour IoT a intégré sa plateforme de surveillance continue des menaces ICS aux pare-feu de nouvelle génération de Palo Alto pour permettre le blocage des menaces critiques plus rapidement et plus efficacement.
ms.date: 11/09/2021
ms.topic: tutorial
ms.openlocfilehash: 5beec75d23fb940f35ded84f2a938dae7e3dd9bc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301454"
---
# <a name="tutorial-integrate-palo-alto-with-microsoft-defender-for-iot"></a>Tutoriel : Intégrer Palo Alto à Microsoft Defender pour IoT

Ce tutoriel va vous permettre de découvrir comment intégrer Palo Alto à Microsoft Defender pour IoT et comment l’utiliser.

Defender pour IoT a intégré sa plateforme de surveillance continue des menaces ICS aux pare-feu de nouvelle génération de Palo Alto pour permettre le blocage des menaces critiques plus rapidement et plus efficacement.

Les types d’intégration suivants sont disponibles :

- Option de blocage automatique : intégration directe de Defender pour IoT à Palo Alto.

- Envoyer des recommandations de blocage au système de gestion centralisée : intégration de Defender pour IoT à Panorama.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> - Configurer un blocage immédiat par un pare-feu Palo Alto spécifique
> - Créer des stratégies de blocage Panorama dans Defender pour IoT

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

### <a name="panorama-permissions"></a>Autorisations Panorama

- Confirmation par l’administrateur Panorama d’autoriser le blocage automatique.

## <a name="configure-immediate-blocking-by-a-specified-palo-alto-firewall"></a>Configurer un blocage immédiat par un pare-feu Palo Alto spécifique

Dans les cas tels que les alertes relatives aux programmes malveillants, vous pouvez activer le blocage automatique. Les règles de transfert Defender pour IoT sont utilisées pour envoyer une commande de blocage directement à un pare-feu Palo Alto spécifique.

Lorsque Defender pour IoT identifie une menace critique, la solution envoie une alerte qui comprend une option de blocage de la source infectée. La sélection de l’option **Bloquer la source** dans les détails de l’alerte active la règle de transfert, qui envoie la commande de blocage au pare-feu Palo Alto spécifié.

**Pour configurer un blocage immédiat** :

1. Dans le volet de gauche, sélectionnez **Forwarding** (Transfert).

1. Sélectionnez **Créer une règle de transfert**.

    :::image type="content" source="media/tutorial-palo-alto/forwarding.png" alt-text="Capture de l’écran de l’alerte de transfert.":::

1. Dans le menu déroulant Actions, sélectionnez **Envoyer au NGFW Palo Alto**.

   :::image type="content" source="media/tutorial-palo-alto/forward-rule.png" alt-text="Capture de l’écran Créer une règle de transfert.":::

1. Dans le volet Actions, définissez les paramètres suivants :

   - **Hôte** : Entrez l’adresse IP du serveur du NGFW.
   - **Port** : Entrez le port du serveur du NGFW.
   - **Nom d’utilisateur** : Entrez le nom d’utilisateur du serveur du NGFW.
   - **Mot de passe** : Entrez le mot de passe du serveur du NGFW.
   - **Configurer** : Configurez les options suivantes pour autoriser le blocage des sources suspectes par le pare-feu de Palo Alto :
     - **Bloquer les codes de fonction non conformes** : Violations de protocole, notamment une valeur de champ non conforme qui ne respecte pas la spécification du protocole ICS (possible code malveillant exploitant une faille de sécurité).
     - **Bloquer les mises à jour non autorisées des microprogrammes/programmes du PLC** : Modifications non autorisées du PLC.
     - **Bloquer l’arrêt non autorisé du PLC** : Arrêt du PLC (temps d’arrêt).
     - **Bloquer les alertes relatives aux programmes malveillants** : Blocage des tentatives de programmes malveillants industriels (TRITON, NotPetya, etc.). Vous pouvez sélectionner l’option de **blocage automatique**. Dans ce cas, le blocage est exécuté automatiquement et immédiatement.
     - **Bloquer l’analyse non autorisée** : Analyse non autorisée (reconnaissance potentielle).

    :::image type="content" source="media/tutorial-palo-alto/edit.png" alt-text="Capture de l’écran Modifier la règle de transfert.":::

1. Sélectionnez **Envoyer**.

Vous devrez alors bloquer toute source suspecte.

**Pour bloquer une source suspecte** :

1. Accédez au volet **Alertes**, puis sélectionnez l’alerte relative à l’intégration à Palo Alto.

1. Pour bloquer automatiquement la source suspecte, sélectionnez **Bloquer la source**. La boîte de dialogue **Veuillez confirmer**, s’affiche.

    :::image type="content" source="media/tutorial-palo-alto/unauthorized.png" alt-text="Capture d’écran du bouton Bloquer la source, qui permet de bloquer la source non autorisée.":::

1. Sélectionnez **OK**.

La source suspecte est désormais bloquée par le pare-feu de Palo Alto.

## <a name="create-panorama-blocking-policies-in-defender-for-iot"></a>Créer des stratégies de blocage Panorama dans Defender pour IoT

L’intégration de Defender pour IoT et Palo Alto Network crée automatiquement des stratégies dans Panorama et le système de gestion des réseaux de Palo Alto Network.

Le tableau suivant indique les incidents auxquels cette intégration est destinée :

| Type d’incident | Description |
|--|--|
|**Modifications non autorisées du PLC** | Mise à jour de la logique d’échelle ou du microprogramme d’un appareil. Cela peut représenter une activité légitime ou être une tentative visant à compromettre l’appareil. Par exemple, un code malveillant, tel qu’un cheval de Troie d’accès à distance (RAT), ou des paramètres qui obligent le processus physique, tel qu’une turbine tournante, à fonctionner de manière non sécurisée. |
|**Violation de protocole** | Structure de paquet ou valeur de champ qui ne respecte pas la spécification du protocole. Il peut s’agir d’une application mal configurée ou d’une tentative malveillante visant à compromettre l’appareil. Par exemple, en provoquant une condition de dépassement de la mémoire tampon dans l’appareil cible. |
|**Arrêt du PLC** | Commande qui entraîne l’arrêt de l’appareil, ce qui risque de compromettre le processus physique contrôlé par le PLC. |
|**Programme malveillant industriel détecté dans le réseau ICS** | Programme malveillant qui manipule les appareils ICS en utilisant leurs protocoles natifs, tels que TRITON et Industroyer. Defender pour IoT détecte également les programmes malveillants qui se sont déplacés latéralement dans l’environnement ICS et SCADA. Par exemple, Conficker, WannaCry et NotPetya. |
|**Programmes malveillants d’analyse** | Outils de reconnaissance qui collectent des données sur la configuration du système lors d’une phase de pré-attaque. Par exemple, le cheval de Troie Havex analyse les réseaux industriels à la recherche d’appareils utilisant OPC, un protocole standard utilisé par les systèmes SCADA sous Windows pour communiquer avec les appareils ICS. |

Quand Defender pour IoT détecte un cas d’usage préconfiguré, le bouton **Bloquer la source** est ajouté à l’alerte. Ensuite, quand l’utilisateur CyberX sélectionne le bouton **Bloquer la source**, Defender pour IoT crée des stratégies sur Panorama en envoyant la règle de transfert prédéfinie.

La stratégie est appliquée uniquement lorsque l’administrateur Panorama l’envoie (push) au NGFW approprié dans le réseau.

Dans les réseaux informatiques, il peut y avoir des adresses IP dynamiques. Par conséquent, pour ces sous-réseaux, la stratégie doit être basée sur le nom de domaine complet (nom DNS), et non sur l’adresse IP. Defender pour IoT effectue une recherche inversée et fait correspondre des appareils ayant une adresse IP dynamique à leur nom de domaine complet (nom DNS) chaque nombre d’heures configuré.

En outre, Defender pour IoT envoie un e-mail à l’utilisateur Panorama approprié pour l’informer qu’une nouvelle stratégie créée par Defender pour IoT est en attente d’approbation. La figure ci-dessous présente l’architecture d’intégration de Defender pour IoT avec Panorama.

:::image type="content" source="media/tutorial-palo-alto/structure.png" alt-text="Capture d’écran de l’architecture d’intégration de CyberX-Panorama.":::

La première étape de la création de stratégies de blocage Panorama dans Defender pour IoT consiste à configurer la recherche DNS.

**Pour configurer la recherche DNS** :

1. Dans le volet gauche, sélectionnez **Paramètres système**.

1. Sélectionnez le bouton **Paramètres DNS** :::image type="icon" source="media/tutorial-palo-alto/settings.png":::.

1. Dans la boîte de dialogue **Modifier les paramètres DNS**, définissez les paramètres suivants :

   - **État** : État du programme de résolution DNS.

   - **Adresse du serveur DNS** : Entrez l’adresse IP ou le nom de domaine complet du serveur DNS du réseau.
   - **Port du serveur DNS** : Entrez le port utilisé pour interroger le serveur DNS.
   - **Sous-réseaux** : Définissez la plage de sous-réseau d’adresses IP dynamiques. Plage dans laquelle Defender pour IoT inverse la recherche de l’adresse IP sur le serveur DNS pour qu’elle corresponde à son nom de domaine complet actuel.
   - **Planifier la recherche inversée** : Définissez les options de planification comme suit :
     - À des heures spécifiques : Spécifiez le moment où la recherche inversée doit être effectuée quotidiennement.
     - À intervalles fixes (en heures) : Définissez la fréquence d’exécution de la recherche inversée.
   - **Nombre d’étiquettes** : Demandez à Defender pour IoT de résoudre automatiquement les adresses IP réseau en noms de domaine complets d’appareil. <br />Pour configurer la résolution du nom de domaine complet DNS, ajoutez le nombre d’étiquettes de domaine à afficher. Jusqu’à 30 caractères s’affichent de gauche à droite.

    :::image type="content" source="media/tutorial-palo-alto/configuration.png" alt-text="Capture de l’écran de configuration des paramètres DNS.":::

1. Sélectionnez **SAVE** (Enregistrer).

Pour vous assurer que vos paramètres DNS sont corrects, sélectionnez **Test de recherche**. Le test garantit que l’adresse IP et le port du serveur DNS sont correctement définis.

## <a name="block-suspicious-traffic-with-the-palo-alto-firewall"></a>Bloquer le trafic suspect avec le pare-feu Palo Alto

Le trafic suspect doit être bloqué avec le pare-feu Palo Alto. Vous pouvez bloquer le trafic suspect en utilisant des règles de transfert dans Defender pour IoT.

**Pour bloquer le trafic suspect avec le pare-feu Palo Alto en utilisant une règle de transfert Defender pour IoT** :

1. Dans le volet de gauche, sélectionnez **Forwarding** (Transfert).

1. Sélectionnez **Créer une règle de transfert**.

1. Dans le menu déroulant **Actions**, sélectionnez **Envoyer à Palo Alto Panorama**.

1. Dans le volet Actions, définissez les paramètres suivants :

   - **Hôte** : Entrez l’adresse IP du serveur Panorama.

   - **Port** : Entrez le port du serveur Panorama.

   - **Nom d’utilisateur** : Entrez le nom d’utilisateur du serveur Panorama.

   - **Mot de passe** : Entrez le mot de passe du serveur Panorama.

   - **Adresse du rapport** : Définissez la manière dont le blocage est exécuté, comme suit :

     - **Par adresse IP** : Crée toujours des stratégies de blocage sur Panorama en fonction de l’adresse IP.

     - **Par nom de domaine complet ou adresse IP** : Crée des stratégies de blocage sur Panorama en fonction du nom de domaine complet, s’il en existe un ; sinon, en fonction de l’adresse IP.

   - **E-mail** : Définissez l’adresse e-mail de notification de stratégie.

    > [!NOTE]
    > Vérifiez que vous avez configuré un serveur de messagerie dans Defender pour IoT. Si aucune adresse e-mail n’est entrée, Defender pour IoT n’envoie pas d’e-mail de notification.

   - **Exécuter une recherche DNS lors de la détection d’une alerte (case à cocher)**  : quand l’option « Par nom de domaine complet ou adresse IP » est définie dans l’adresse du rapport. Cette case à cocher est activée par défaut. Si seule l’adresse IP est définie, cette option est désactivée.

   - **Configurer** : Configurez les options suivantes pour autoriser le blocage des sources suspectes par Palo Alto Panorama :

     - **Bloquer les codes de fonction non conformes** : Violations de protocole, notamment une valeur de champ non conforme qui ne respecte pas la spécification du protocole ICS (possible code malveillant exploitant une faille de sécurité).

     - **Bloquer les mises à jour non autorisées des microprogrammes/programmes du PLC** : Modifications non autorisées du PLC.

     - **Bloquer l’arrêt non autorisé du PLC** : Arrêt du PLC (temps d’arrêt).

     - **Bloquer les alertes relatives aux programmes malveillants** : Blocage des tentatives de programmes malveillants industriels (TRITON, NotPetya, etc.). Vous pouvez sélectionner l’option de **blocage automatique**. Dans ce cas, le blocage est exécuté automatiquement et immédiatement.

     - **Bloquer l’analyse non autorisée** : Analyse non autorisée (reconnaissance potentielle).

    :::image type="content" source="media/tutorial-palo-alto/details.png" alt-text="Capture de l’écran permettant de sélectionner une action.":::

1. Sélectionnez **Envoyer**.

Vous devrez alors bloquer la source suspecte.

**Pour bloquer la source suspecte** :

1. Dans le volet **Alertes**, sélectionnez l’alerte relative à l’intégration à Palo Alto. La boîte de dialogue **Détails de l’alerte** s’affiche.

  :::image type="content" source="media/tutorial-palo-alto/unauthorized.png" alt-text="Capture de l’écran de l’alerte associée à Palo Alto, avec sélection de l’option Bloquer la source.":::

1. Pour bloquer automatiquement la source suspecte, sélectionnez **Block Source** (Bloquer la source).

1. Sélectionnez **OK**.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Il n’y a pas de ressources à nettoyer.

## <a name="next-step"></a>Étape suivante

Dans ce tutoriel, vous avez découvert comment bien démarrer avec l’intégration de Palo Alto.

> [!div class="nextstepaction"]
> [Bouton Étapes suivantes](tutorial-splunk.md)
