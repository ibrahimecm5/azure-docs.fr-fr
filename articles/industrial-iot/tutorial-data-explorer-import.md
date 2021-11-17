---
title: Utilisation de l’IoT industriel pour tirer (pull) des données dans Azure Data Explorer
description: Dans ce tutoriel, vous découvrez comment déplacer des données IIoT dans ADX et comment créer un tableau de bord pour les visualiser.
author: eross-msft
ms.author: lizross
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 6/16/2021
ms.openlocfilehash: 4ddd2e14d3be90c211d0e278a11e45bd51154fa1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550018"
---
# <a name="tutorial-using-industrial-iot-to-pull-data-into-azure-data-explorer"></a>Tutoriel : Utilisation d’IoT Industrial pour extraire des données dans Azure Data Explorer

La plateforme Azure IIoT (Industrial IoT) combine des modules de périphérie et des microservices cloud avec des services PaaS Azure afin de fournir des fonctionnalités de découverte de ressources industrielles et d’acquisition de données des ressources. Dans un paramétrage industriel, le standard de facto pour cette tâche est OPC UA. Ce tutoriel explique comment configurer Azure pour l’utiliser avec OPC UA, avec un cas d’usage simple comme exemple, à savoir une supervision des conditions du cloud.

Dans ce didacticiel, vous apprendrez à :
- Obtenir un compte Azure et déployer un hub IoT.
- Déployer IoT Edge
- Installer et configurer OPC Publisher
- Configurer une base de données de série chronologique.
- Créer un tableau de bord.
- Créer un graphique des données entrantes.

## <a name="requirements"></a>Spécifications

Avant de démarrer ce tutoriel, vous avez besoin d’un compte Azure. Si vous n’en avez pas, vous pouvez en obtenir un gratuitement en [cliquant ici](https://azure.microsoft.com/free/).

## <a name="deploy-an-s1-iot-hub"></a>Déployer un hub IoT S1

La première chose à faire avec votre abonnement Azure est d’accéder [au portail](../azure-portal/azure-portal-quickstart-center.md) et de déployer un [hub IoT S1](../iot-hub/iot-hub-create-through-portal.md). Votre compte Azure gratuit vous donne droit à 400 000 messages de 4 Ko par jour. Comme vous pouvez faire tenir environ 15 étiquettes de données PubSub OPC UA dans un même message de 4 Ko, vous aurez 6 millions d’étiquettes de données par jour, ce qui est suffisant pour ce cas d’usage. Une fois le hub IoT déployé, copiez la chaîne de connexion principale de *iothubowner* (sous stratégies d’accès partagé, iothubowner). Vous en avez besoin pour l’installation d’IoT Edge.

## <a name="deploy-iot-edge"></a>Déployer IoT Edge

Déployez IoT Edge en utilisant le [programme d’installation d’IoT Edge](https://lnkd.in/ga6Ew4X) sur un PC Windows 10 ou sur une machine virtuelle. Ce PC ou cette machine virtuelle doit disposer d’une connexion Internet et être en mesure d’accéder aux machines ou au logiciel d’adaptateur de connectivité industriel (dataFEED, Zenon, Kepware, etc.) que vous voulez utiliser. Vous pouvez imbriquer (c’est-à-dire « connecter en chaîne ») des appareils IoT Edge si votre installation utilise plusieurs couches réseau de votre réseau OT (où se trouvent vos machines) sur votre connexion Internet. Vous pouvez également exécuter IoT Edge en mode natif sur Linux en utilisant le programme d’installation sur [GitHub](https://github.com/Azure/Industrial-IoT-Gateway-Installer/blob/master/Releases/Linux.zip).

Quand vous exécutez le programme d’installation d’IoT Edge, activez la case à cocher **Utiliser Docker Desktop**, laissez sélectionnée la case d’option **Configurer IoT Edge pour la plateforme PaaS Azure Industrial Cloud Platform**, puis collez la chaîne de connexion de *iothubowner* dans la zone de texte fournie. Le programme d’installation prérenseigne automatiquement le nom du PC que vous exécutez avec le nom de l’instance Edge, mais vous pouvez le modifier si nécessaire. Cliquez sur **Installer**. Si Docker Desktop n’est pas détecté, le programme d’installation le télécharge automatiquement. Installez-le et veillez à le configurer avec Sous-système Windows pour Linux V2 (WSL2) pour obtenir de meilleures performances. Une fois l’installation de Docker Desktop terminée, redémarrez le programme d’installation d’IoT Edge, reconfigurez-le, puis recliquez sur Installer. Accédez ensuite à l’onglet **IoT Edge** de votre page **IoT Hub** dans le portail Azure et attendez que votre appareil IoT Edge que vous venez d’installer apparaisse (vous devrez peut-être appuyer plusieurs fois sur Actualiser).

## <a name="install-and-configure-opc-publisher"></a>Installer et configurer OPC Publisher

Ensuite, installez et configurez OPC Publisher, qui est utilisé pour standardiser et normaliser toutes les données de télémétrie en utilisant PubSub OPC UA à la périphérie. Nous allons utiliser le codage JSON d’OPC UA, car il peut être ingéré directement dans les bases de données cloud sans nécessiter d’abord un service cloud distinct pour retraduire le format binaire en un fichier que la base de données cloud peut comprendre. L’utilisation de ce format standard réduit la charge de gestion et le coût dans le cloud. Il s’agit d’un point important : beaucoup de gens vont vous dire qu’ils ont trouvé un codage de télémétrie plus efficace, mais ils sous-estiment invariablement le coût et la gestion de la traduction de la télémétrie dans un format prêt pour une base de données cloud à grande échelle dans le cloud. 

À présent, accédez à la page **Bien démarrer** pour OPC Publisher et suivez les instructions. Pour **Options de création de conteneur**, spécifiez ce qui suit.

```
{
    "Hostname": "OPCPublisher",
    "Cmd": [
        "PkiRootPath=/appdata/pki", 
        "--lf=/appdata/publisher.log", 
        "--pf=/appdata/publishednodes.json",
        "--aa",
        "--di=60",
        "--me=Json",
        "--mm=PubSub"
    ],
    "HostConfig": {
        "Binds": [
            "/c/IoTEdgeMapping:/appdata"
        ]
    }
}
```

Ensuite, configurez les nœuds de données OPC UA de vos machines (ou de votre logiciel d’adaptateur de connectivité) qui génèrent des messages de télémétrie de série chronologique. Pour ce faire, copiez le fichier de modèle ***publishednodes.json** _ depuis ici vers _*_C:\IoTEdgeMapping_*_ et placez-y le serveur *EndpointURL* et les ID de nœud OPC UA des données que vous voulez envoyer dans le cloud. Si vous ne savez pas quoi envoyer, le nœud de données avec l’ID ns=0;i=2258 est l’heure du serveur, qui change à chaque seconde. OPC UA publie seulement les modifications de données si les données ont changé. Cependant, OPC Publisher prend également en charge la lecture et l’envoi de données à des intervalles spécifiques même si elles n’ont pas changé.

Une fois que vous avez fini de modifier le fichier, redémarrez OPC Publisher en utilisant la commande de ligne de commande « **iotedge restart OPCPublisher** » : OPC Publisher va alors essayer de se connecter à tous les serveurs OPC UA que vous avez spécifiés et publier tous les nœuds listés. L’expérience a montré que vous devez d’abord utiliser un client OPC UA comme UA Expert depuis le PC exécutant IoT Edge pour garantir que vous pouvez vous connecter aux serveurs OPC UA et aussi pour essayer l’adresse IP du serveur OPC UA dans EndpointURL au lieu du nom d’hôte, au cas où votre DNS ne fonctionnerait pas. Revenez maintenant à la page de votre **hub IoT** dans le portail Azure et vérifiez que vous pouvez voir des messages **Appareil-à-cloud** qui sont reçus dans l’onglet **Vue d’ensemble**.

## <a name="set-up-the-time-series-database"></a>Configurer la base de données de série chronologique

À présent, configurez la base de données de série chronologique dans le cloud qui recevra la télémétrie OPC UA. Il est préférable d’utiliser pour cela Azure Data Explorer, car il utilise plusieurs couches de base de données pour des performances optimales, offre des fonctionnalités avancées d’analytique et de tableau de bord, et prend même en charge les prédictions et la détection des anomalies. Déployez une instance à partir du portail Azure, sélectionnez la référence SKU de charge de travail dev/test et n’oubliez pas d’activer l’ingestion en streaming dans les configurations.

OPC UA identifie de façon univoque une étiquette de données de télémétrie par le **DatasetWriterID** du serveur OPC UA et par le **ExpandedNodeID** (qui contient à la fois l’espace de noms et l’ID de nœud). Par ailleurs, l’horodatage indiquant quand les données sont lues par le serveur OPC UA avant de les rendre disponibles pour un client connecté est appelé *SourceTimeStamp*. Pour créer une série chronologique de données de télémétrie OPC UA, nous voulons fournir le **DatasetWriterID**, le **ExpandedNodeID**, la valeur des données lues et le **SourceTimeStamp** dans une table de base de données. C’est ce que vous allez créer maintenant, en utilisant Azure Data Explorer. Notez que le type des valeurs de données (float, int, etc.) peut également être inclus dans les messages de PubSub OPC UA ; ceci s’appelle *codage réversible* dans la spécification OPC UA.

Tout d’abord, créez une base de données avec la période de conservation des données par défaut de 10 ans, ce qui devrait suffire pour ce cas d’usage. Ensuite, configurez l’ingestion en streaming directement à partir de votre hub IoT en cliquant sur **Créer une connexion de données** et en sélectionnant **IoT Hub**, donnez un nom à la connexion, puis sélectionnez votre hub IoT dans la liste déroulante. Sélectionnez *iothubowner* sous **Stratégie d’accès partagé** et *$default* pour le **Groupe de consommateurs**. Laissez le **Nom de la table**  vide pour le moment, mais sélectionnez *JSON MULTILIGNE* sous **Format des données** et entrez *opcua_mapping* pour le **Nom du mappage**. Sélectionnez **Create** (Créer).

## <a name="create-the-database-tables"></a>Créer les tables de base de données

Créez les tables nécessaires dans la nouvelle base de données. Commencez par créer une table intermédiaire avec une seule colonne pour recevoir les messages de télémétrie JSON de PubSub OPC UA. Azure Data Explorer utilise le langage de requête Kusto intégré (KQL), qui est documenté ici. Sélectionnez l’onglet **Requête**, puis entrez ce qui suit.

```
.create table opcua_raw(payload: dynamic)

```

Créez un mappage sur l’ingestion de télémétrie *opcua_mapping* en utilisant le hub IoT vous avez configuré précédemment en utilisant la commande suivante.

```
.create table opcua_raw ingestion json mapping "opcua_mapping" @'[{"column":"payload","path":"$","datatype":"dynamic"}]'

```

Mettez à jour l’ingestion de télémétrie avec le nom de table de la table que vous venez de créer en sélectionnant l’onglet **Ingestion de données**, en sélectionnant le nom de votre ingestion de données, sélectionnez **Modifier**, entrez *opcua_raw* dans le **Nom de la table**, puis cliquez sur **Mettre à jour**. Ensuite, revenez à l’onglet **Requête** et attendez quelques minutes pour voir les premiers messages de télémétrie entrer dans la table. Vous pouvez vérifier cela en effectuant une requête pour demander 10 lignes de la table.

```
opcua_raw
| take 10
```

PubSub OPC UA prend en charge le traitement par lot de la télémétrie d’un même message via l’utilisation de jeux de données, qui sont identifiés par le **DataSetWriterID**. Ces jeux de données ne doivent pas être « mis en lots » et figurer dans des lignes distinctes de votre base de données. Pour ce faire, vous pouvez utiliser l’opérateur *mv-expand*, en développant le JSON ingéré dans une nouvelle table intermédiaire que vous devez créer en premier.

```
.create table opcua_intermediate(DataSetWriterID: string, payload: dynamic)
```

À présent, créez la fonction suivante pour effectuer l’expansion.

```
.create-or-alter function OPCUARawExpand() {
    opcua_raw
    |mv-expand records = payload.Messages
    |project 
        DataSetWriterID = tostring(records["DataSetWriterId"]),
        Payload = todynamic(records["Payload"])
}
```

Ensuite, mettez à jour la table intermédiaire en appliquant la nouvelle fonction.

```
.alter table opcua_intermediate policy update @'[{"Source": "opcua_raw", "Query": "OPCUARawExpand()", "IsEnabled": "True"}]'
```

Vérifiez que la nouvelle table est remplie correctement.

```
opcua_intermediate
| take 10
```

 Créez la table de télémétrie OPC UA finale et remplissez-la en développant plus avant chaque entrée dans le jeu de données OPC UA.

```
.create table opcua_telemetry (DataSetWriterID: string, ExpandedNodeID: string, Value: dynamic, SourceTimestamp: datetime)
```

Créez la fonction montrée ci-dessous.

```
.create-or-alter function OPCUADatasetExpand() {
    opcua_intermediate
    | mv-apply payload on (
        extend key = tostring(bag_keys(payload)[0])
        | extend p = payload[key]
        | project ExpandedNodeId = key, Value = todynamic(p.Value), SourceTimestamp = todatetime(p.SourceTimestamp)
    )
}
```

Mettez à jour la troisième et la dernière table en appliquant la nouvelle fonction.

```
.alter table opcua_telemetry policy update @'[{"Source": "opcua_intermediate", "Query": "OPCUADatasetExpand()", "IsEnabled": "True"}]'
```

Pour consulter la table de télémétrie OPC UA, utilisez la commande suivante.

```
opcua_telemetry
| take 10
```

## <a name="create-line-graph-of-the-data"></a>Créer un graphique en courbes des données

À présent, nous allons créer un graphique en courbes des données en les castant toutes en nombres à virgule flottante. Si le cast échoue, les données sont ignorées. Utilisez pour cela le tableau de bord Azure Data Explorer hébergé. Cliquez sur **Ouvrir dans l’interface utilisateur web** et, dans la nouvelle fenêtre, cliquez sur **Tableaux de bord**, sélectionnez **Créer un tableau de bord**, puis choisissez **Ajouter une vignette**. Sélectionnez ensuite **Source de données**, puis entrez le nom de notre instance Azure Data Explorer au format suivant : `https://<YourInstanceName>.<Your RegionName>.kusto.windows.net`. Sélectionnez votre base de données, puis cliquez sur **Appliquer**. Entrez ensuite la requête suivante.

```
opcua_telemetry
| project SourceTimestamp, yaxis = todouble(Value), DataSetWriterID,  ExpandedNodeID
```

Cliquez sur l’onglet **Visuel** et sélectionnez *YAxis* pour les **colonnes Y**, *SourceTimeStamp* pour les **colonnes X** et *DataSetWriterID* pour les **colonnes des séries**.

Vous disposez maintenant d’un beau tableau de bord de télémétrie pour les données de votre cas d’usage de supervision des conditions dans le cloud.