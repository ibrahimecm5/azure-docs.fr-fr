---
title: Configurer Grafana pour visualiser les métriques émises à partir d’Azure Managed Instance pour Apache Cassandra
description: Découvrez comment installer et configurer Grafana dans une machine virtuelle pour visualiser les métriques émises à partir d’un cluster Azure Managed Instance pour Apache Cassandra.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 11/02/2021
ms.author: thvankra
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8fe10e1706667bba10133131f7a1d50a6e3fa110
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309807"
---
# <a name="configure-grafana-to-visualize-metrics-emitted-from-the-managed-instance-cluster"></a>Configurer Grafana pour visualiser les métriques émises à partir du cluster d’instance managée

Quand vous déployez un cluster Azure Managed Instance pour Apache Cassandra, le service provisionne un serveur qui héberge [Prometheus](https://prometheus.io/) qui peut être consommé par différents outils clients. Prometheus est une solution de supervision open source. L’instance managée émet des métriques et conserve 10 minutes ou 10 Go de données (premier des deux seuils atteint). Cet article explique comment configurer Grafana pour visualiser les métriques émises à partir du cluster d’instance managée. Les tâches suivantes sont nécessaires pour visualiser les métriques :

* Déployer une machine virtuelle Ubuntu sur le réseau virtuel Azure où est installée l’instance managée.
* Installer l’outil open source [Grafana](https://grafana.com/grafana/) pour générer des tableaux de bord et visualiser les métriques émises à partir de Prometheus.

## <a name="deploy-an-ubuntu-server"></a>Déployer un serveur Ubuntu

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Accédez au groupe de ressources dans lequel se trouve votre cluster d’instance managée. Sélectionnez **Ajouter** et recherchez l’image **Ubuntu Server 18.04 LTS** :

   :::image type="content" source="./media/visualize-prometheus-grafana/select-ubuntu-image.png" alt-text="Recherchez l’image de serveur Ubuntu à partir du portail Azure." border="true":::

1. Choisissez l’image et sélectionnez **Créer**.

1. Dans le panneau **Créer une machine virtuelle**, entrez des valeurs pour les champs suivants (vous pouvez conserver les valeurs par défaut pour les autres champs) :

   * **Nom de la machine virtuelle** : entrez le nom de votre machine virtuelle.
   * **Région** : sélectionnez la région dans laquelle votre réseau virtuel a été déployé.

   :::image type="content" source="./media/visualize-prometheus-grafana/create-vm-ubuntu.png" alt-text="Remplissez le formulaire pour créer une machine virtuelle avec une image de serveur Ubuntu." border="true":::

1. Sous l’onglet **Réseau**, sélectionnez le réseau virtuel sur lequel votre instance managée est déployée :

   :::image type="content" source="./media/visualize-prometheus-grafana/configure-networking-details.png" alt-text="Configurez les paramètres réseau du serveur Ubuntu." border="true":::

1. Pour finir, sélectionnez **Vérifier + créer** pour créer votre serveur Grafana.

## <a name="install-grafana"></a>Installer Grafana

1. À partir du portail Azure, ouvrez le réseau virtuel sur lequel vous avez déployé l’instance managée et le serveur Grafana. Vous devez voir une instance de groupe de machines virtuelles identiques nommée **cassandra-jump (instance 0)** . Les métriques Prometheus sont hébergées dans ce groupe de machines virtuelles identiques. Prenez note de l’adresse IP de cette instance :

   :::image type="content" source="./media/visualize-prometheus-grafana/prometheus-instance-address.png" alt-text="Récupérez l’adresse IP de l’instance Prometheus." border="true":::

1. Connectez-vous à votre nouveau serveur Ubuntu à l’aide d’[Azure CLI](../virtual-machines/linux/ssh-from-windows.md#ssh-clients) ou de l’outil client de votre choix pour vous connecter par le biais de SSH.

1. Après vous être connecté à la machine virtuelle, vous devez installer et configurer Grafana pour vous connecter au groupe de machines virtuelles identiques où les métriques sont hébergées. Ouvrez une invite de commandes et entrez la commande `nano` pour ouvrir un éditeur de texte Nano. Collez le script suivant dans l’éditeur de texte, en veillant à remplacer `<prometheus IP address>` par l’adresse IP notée à l’étape précédente :

   ```bash
   #!/bin/bash
   
   echo "Installing Grafana..."
   
   if ! $SSH dpkg -s grafana prometheus > /dev/null; then
       echo "Installing packages."
       echo 'deb https://packages.grafana.com/oss/deb stable main' | $SSH sudo tee /etc/apt/sources.list.d/grafana.list > /dev/null
       curl https://packages.grafana.com/gpg.key | $SSH sudo apt-key add -
       $SSH sudo apt-get update
       $SSH sudo apt-get install -y grafana prometheus
   else
       echo "Skipping package installation"
   fi
   
   echo "Configuring grafana"
   cat <<EOF | $SSH sudo tee /etc/grafana/provisioning/datasources/prometheus.yml
   apiVersion: 1
   datasources:
     - name: Prometheus
       type: prometheus
       url: https://<prometheus IP address>:9443
       jsonData:
         tlsSkipVerify: true
   EOF
   
   echo "Restarting Grafana"
   $SSH sudo systemctl enable grafana-server
   $SSH sudo systemctl restart grafana-server
   
   echo "Installing Grafana plugins"
   $SSH sudo grafana-cli plugins install natel-discrete-panel
   $SSH sudo grafana-cli plugins install grafana-polystat-panel
   $SSH sudo systemctl restart grafana-server
   ```

1. Tapez `ctrl + X` pour enregistrer le fichier. Vous pouvez nommer le fichier `grafana.sh`.

1. Entrez la commande `./grafana.sh` à l’invite de commandes pour installer Grafana.

1. Une fois l’installation terminée, Grafana est disponible sur le **port 3000** à l’adresse IP du serveur, comme indiqué dans la capture d’écran suivante :

   :::image type="content" source="./media/visualize-prometheus-grafana/open-grafana-port.png" alt-text="Exécutez Grafana sur le port 3000." border="true":::

1. Vous pouvez choisir parmi les tableaux de bord open source créés pour Apache Cassandra dans Grafana, tels que le fichier JSON de [vue d’ensemble du cluster](https://github.com/TheovanKraay/cassandra-exporter/blob/master/grafana/instaclustr/cluster-overview.json). Téléchargez et importez la définition JSON du tableau de bord dans Grafana :

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-import.png" alt-text="Importez la définition JSON Grafana." border="true":::

   :::image type="content" source="./media/visualize-prometheus-grafana/grafana-upload-json.png" alt-text="Chargez la définition JSON Grafana." border="true":::

1. Vous pouvez ensuite superviser votre cluster Cassandra d’instance managée avec le tableau de bord choisi :

   :::image type="content" source="./media/visualize-prometheus-grafana/monitor-cassandra-metrics.gif" alt-text="Affichez les métriques d’instance managée Cassandra dans le tableau de bord." border="true":::

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à configurer des tableaux de bord pour visualiser des métriques dans Prometheus à l’aide de Grafana. Explorez plus en détail Azure Managed Instance pour Apache Cassandra avec les articles suivants :

* [Vue d’ensemble d’Azure Managed Instance pour Apache Cassandra](introduction.md)
* [Déployer un cluster Apache Spark managé avec Azure Databricks](deploy-cluster-databricks.md)
