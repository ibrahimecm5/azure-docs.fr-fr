---
title: Connecter les capteurs avec un proxy
description: Découvrez comment configurer Microsoft Defender pour IoT afin qu’il communique avec un capteur via un proxy sans accès direct à Internet.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 0a6e2ef526985ca5e8bee208989310ed01f18267
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278794"
---
# <a name="connect-microsoft-defender-for-iot-sensors-without-direct-internet-access-by-using-a-proxy"></a>Connecter des capteurs Microsoft Defender pour IoT sans accès direct à Internet à l’aide d’un proxy 

Cet article explique comment configurer Microsoft Defender pour IoT afin qu’il communique avec un capteur via un proxy sans accès direct à Internet. Connectez le capteur en utilisant un proxy de transfert qui utilise le tunneling HTTP et prend en charge la commande HTTP CONNECT pour la connectivité. Les instructions fournies ici utilisent le proxy Squid open source, mais vous pouvez choisir tout autre proxy qui prend en charge la commande CONNECT. 

Le proxy utilise un tunnel SSL chiffré pour transférer les données entre les capteurs et le service. Le proxy n’inspecte pas, n’analyse pas et ne met pas en cache de données. 

Le diagramme suivant illustre le transfert des données du capteur Microsoft Defender pour le cloud dans le segment OT vers le cloud via un proxy situé dans le réseau informatique et une zone DMZ industrielle.

:::image type="content" source="media/how-to-connect-sensor-by-proxy/cloud-access.png" alt-text="Connecter le capteur à un proxy via le cloud.":::

## <a name="set-up-your-system"></a>Configurer votre système

Pour ce scénario, nous allons installer et configurer la dernière version de [Squid](http://www.squid-cache.org/) sur un serveur Ubuntu 18.

> [!Note]
> Microsoft Defender pour IoT ne prend pas en charge Squid ni aucun autre service proxy.

**Pour installer le proxy Squid sur un serveur Ubuntu 18** :

1. Connectez-vous à votre machine Ubuntu proxy désignée.

1. Lancez une fenêtre de terminal.
 
1. Mettez à jour votre logiciel vers la dernière version à l’aide de la commande suivante.

    ```bash
    sudo apt-get update 
    ```

1. Installez le package Squid avec la commande suivante.

    ```bash
    sudo apt-get install squid 
    ```

1. Recherchez le fichier de configuration de Squid qui se trouve dans `/etc/squid/squid.conf` et `/etc/squid/conf.d/`.

1. Effectuez une sauvegarde du fichier d’origine à l’aide de la commande suivante.

    ```bash
    sudo cp -v /etc/squid/squid.conf{,.factory}'/etc/squid/squid.conf' -> '/etc/squid/squid.conf.factory sudo nano /etc/squid/squid.conf
    ```

1. Ouvrez `/etc/squid/squid.conf` dans un éditeur de texte.

1. Recherchez `# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS`.

1. Ajoutez `acl sensor1 src <sensor-ip>` et `http_access allow sensor1` dans le fichier.

    :::image type="content" source="media/how-to-connect-sensor-by-proxy/add-lines.png" alt-text="Ajouter les deux lignes suivantes dans le texte et enregistrer le fichier.":::

1. (Facultatif) Ajoutez des capteurs supplémentaires en ajoutant une ligne pour chaque capteur.

1. Activez le service Squid pour amorcer le lancement en utilisant la commande suivante.

    ```bash
    sudo systemctl enable squid 
    ```

## <a name="set-up-a-sensor-to-use-squid"></a>Configurer un capteur pour utiliser Squid

**Pour configurer un capteur pour utiliser Squid** :

1. Connectez-vous au capteur.

1. Accédez à **Paramètres système** > **Réseau**.

1. Sélectionnez **Activer le proxy**.

    :::image type="content" source="media/how-to-connect-sensor-by-proxy/enable-proxy.png" alt-text="Sélectionnez Activer le proxy dans la fenêtre Configuration réseau du capteur.":::

1. Entrez l’adresse du proxy.

1. Entrez un port. Le port par défaut est `3128`.

1. (Facultatif) Entrez un nom d’utilisateur et un mot de passe pour le proxy.

1. Sélectionnez **Enregistrer**.

## <a name="see-also"></a>Voir aussi

[Gérer vos abonnements](how-to-manage-subscriptions.md).
