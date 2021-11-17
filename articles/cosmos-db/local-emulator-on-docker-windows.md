---
title: Exécution de l’émulateur sur Docker pour Windows
itleSuffix: Running the Azure Cosmos DB emulator on Docker for Windows
description: Découvrez comment exécuter et utiliser l’émulateur Azure Cosmos DB sur Docker pour Windows. L’émulateur vous permet de développer et de tester votre application localement, sans créer d’abonnement Azure et sans frais.
ms.service: cosmos-db
ms.topic: how-to
author: StefArroyo
ms.author: esarroyo
ms.date: 04/20/2021
ms.openlocfilehash: a083efb897c0ce3c0628134da0ea3d29720ac0e7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054882"
---
# <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>Utiliser l’émulateur sur Docker pour Windows
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Vous pouvez exécuter l’émulateur Azure Cosmos DB dans un conteneur Docker pour Windows. Pour plus d’informations, notamment sur le `Dockerfile`, consultez [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker). Actuellement, l’émulateur ne fonctionne pas sur Docker pour Oracle Linux. Pour exécuter l’émulateur sur Docker pour Windows, suivez les instructions ci-dessous :

1. Une fois [Docker pour Windows](https://www.docker.com/docker-windows) installé, basculez vers les conteneurs Windows en double-cliquant sur l’icône Docker dans la barre d’outils et en sélectionnant **Switch to Windows containers (Basculer vers les conteneurs Windows)** .

1. Ensuite, extrayez l’image de l’émulateur à partir de Docker Hub en exécutant la commande suivante à partir de l’interpréteur de commandes de votre choix.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. Pour démarrer l’image, exécutez les commandes suivantes en fonction de la ligne de commande ou de l’environnement PowerShell :

   # <a name="command-line"></a>[Ligne de commande](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   Les images Docker basées sur Windows peuvent ne pas être compatibles avec chaque système d’exploitation hôte Windows. Par exemple, l’image par défaut de l’émulateur Azure Cosmos DB est compatible uniquement avec Windows 10 et Windows Server 2016. Si vous avez besoin d’une image qui est compatible avec Windows Server 2019, exécutez la commande suivante à la place :

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   La réponse ressemble à ce qui suit :

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > Lors de l’exécution de la commande `docker run`, si vous voyez une erreur de conflit de port (c’est-à-dire si le port spécifié est déjà utilisé), transmettez un port personnalisé en modifiant les numéros de port. Par exemple, vous pouvez remplacer le paramètre « -p 8081:8081 » par « -p 443:8081 »

1. À présent, utilisez le point de terminaison de l’émulateur et la clé primaire de la réponse, puis importez le certificat TLS/SSL dans votre hôte. Pour importer le certificat TLS/SSL, procédez comme suit à partir d’une invite de commandes administrateur :

   # <a name="command-line"></a>[Ligne de commande](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. Si vous fermez l’interpréteur de commandes interactif après le démarrage de l’émulateur, celui-ci arrêtera le conteneur de l’émulateur. Pour rouvrir l’Explorateur de données, accédez à l’URL suivante dans votre navigateur. Le point de terminaison de l’émulateur est fourni dans le message de réponse ci-dessus.

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Si vous avez une application cliente .NET en cours d’exécution sur un conteneur Docker pour Linux et si vous exécutez l’émulateur Azure Cosmos DB sur une machine hôte, suivez les instructions de la prochaine section afin d’importer le certificat dans le conteneur Docker pour Linux.

## <a name="regenerate-the-emulator-certificates"></a>Régénérer les certificats de l’émulateur

Lors de l’exécution de l’émulateur dans un conteneur Docker, les certificats associés à l’émulateur sont régénérés chaque fois que vous arrêtez et redémarrez le conteneur respectif. Pour cette raison, vous devez réimporter les certificats après chaque démarrage de conteneur. Pour contourner cette limitation, vous pouvez utiliser un fichier Docker Compose pour lier le conteneur Docker à une adresse IP spécifique et à une image conteneur.

Par exemple, vous pouvez utiliser la configuration suivante dans le fichier Docker Compose. Veillez à le formater selon vos besoins : 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à utiliser l’émulateur local pour un développement local gratuit. Vous pouvez maintenant passer aux articles suivants :

* [Exporter les certificats de l’émulateur Azure Cosmos DB pour une utilisation avec des applications Java, Python et Node.js](local-emulator-export-ssl-certificates.md)
* [Utiliser des paramètres de ligne de commande et des commandes PowerShell pour contrôler l’émulateur](emulator-command-line-parameters.md)
* [Problèmes de débogage avec l’émulateur](troubleshoot-local-emulator.md)
