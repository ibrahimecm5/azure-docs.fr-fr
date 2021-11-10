---
author: naiteeks
ms.topic: include
ms.service: azure-video-analyzer
ms.date: 11/04/2021
ms.author: juliako
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5a834a4c33ebbffcf4b335e7116b134164941231
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860881"
---
Le processus de déploiement prend environ **20 minutes**. À la fin de l’opération, certaines ressources Azure sont déployées dans l’abonnement Azure, notamment :

1. **Compte Video Analyzer** : ce [service cloud](../../../overview.md) est utilisé pour inscrire le module de périphérie Video Analyzer ainsi que pour la lecture des vidéos enregistrées et l’analytique vidéo.
1. **Compte de stockage** : pour stocker les vidéos enregistrées et l’analytique vidéo.
1. **Identité managée** : il s’agit de l’[identité managée](../../../../../active-directory/managed-identities-azure-resources/overview.md) affectée par l’utilisateur qui permet de gérer l’accès au compte de stockage ci-dessus.
1. **Machine virtuelle** : il s’agit d’une machine virtuelle qui sert d’appareil de périphérie simulé.
1. **IoT Hub** : fait office de hub de messagerie centralisé pour la communication bidirectionnelle entre votre application IoT, les modules IoT Edge et les appareils qu'il gère.

En plus des ressources mentionnées ci-dessus, les éléments suivants sont également créés dans le partage de fichiers « deployment-output » dans votre compte de stockage, pour pouvoir les utiliser dans les tutoriels et les guides de démarrage rapide :

- **_appsettings.json_**  : Ce fichier contient la **chaîne de connexion d’appareil** et d’autres propriétés nécessaires pour exécuter l’exemple d’application dans Visual Studio Code.
- **_env.txt_**  : Ce fichier contient les variables d’environnement dont vous avez besoin pour générer les manifestes de déploiement à l’aide de Visual Studio Code.
- **_deployment.json_**  : Iil s’agit du manifeste de déploiement utilisé par le modèle pour déployer des modules périphériques sur l’appareil périphérique simulé.

<!-- TODO: provide a link to the readme.md in github.com/azure-video-analyzer/setup/readme.md where we can list out all resources like virtual network etc. -->

> [!TIP]
> Si vous rencontrez des problèmes lors de la création de toutes les ressources Azure requises, utilisez les étapes manuelles de ce guide de [démarrage rapide](../../get-started-detect-motion-emit-events-portal.md).
