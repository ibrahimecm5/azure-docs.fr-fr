---
title: Fonctionnalités de rendu
description: Les fonctionnalités Standard d’Azure Batch sont utilisées pour exécuter des applications et des charges de travail de rendu. Batch inclut des fonctionnalités spécifiques qui prennent en charge les charges de travail de rendu.
author: mscurrell
ms.author: markscu
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 57925df3babc22a6cfdff2f81d23bbbd60767cc6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461605"
---
# <a name="azure-batch-rendering-capabilities"></a>Fonctionnalités de rendu Azure Batch

Les fonctionnalités standard d’Azure Batch sont utilisées pour exécuter des applications et des charges de travail de rendu. Batch comprend également des fonctionnalités qui prennent en charge les charges de travail de rendu.

Pour une présentation des concepts Batch, tels que les pools, les travaux et les tâches, consultez [cet article](./batch-service-workflow-features.md).

## <a name="batch-pools-using-custom-vm-images-and-standard-application-licensing"></a>Pools Batch utilisant des images de machines virtuelles personnalisées et des licences d’application standard

Comme pour les autres charges de travail et types d’application, une image de machine virtuelle personnalisée peut être créée avec les applications de rendu et les plug-ins requis. L’image de machine virtuelle personnalisée est placée dans la galerie [Azure Compute Gallery](../virtual-machines/shared-image-galleries.md) et [peut être utilisée pour créer des pools Batch](batch-sig-images.md).

Les chaînes de ligne de commande de la tâche devront référencer les applications et les chemins d’accès utilisés lors de la création de l’image de machine virtuelle personnalisée.

La plupart des applications de rendu requièrent des licences obtenues auprès d’un serveur de licences. S’il existe déjà un serveur de licences local, le pool et le serveur de licences doivent se trouver sur le même [réseau virtuel](../virtual-network/virtual-networks-overview.md). Il est également possible d’exécuter un serveur de licences sur une machine virtuelle Azure, le pool Batch et la machine virtuelle du serveur de licences figurant sur le même réseau virtuel.

## <a name="batch-pools-using-rendering-vm-images"></a>Pools Batch utilisant des images de machines virtuelles de rendu

> [!IMPORTANT]
> Les images de rendu des machines virtuelles et les licences de paiement à l’utilisation ont été [déconseillées et seront supprimées le 29 février 2024](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/). Pour utiliser Batch pour le rendu, vous devez utiliser [une image de machine virtuelle personnalisée et une licence d’application standard.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

### <a name="rendering-application-installation"></a>Installation de l’application de rendu

Pour spécifier une image de machine virtuelle de rendu issue de la Place de marché Azure dans la configuration du pool, seules les applications préinstallées doivent être utilisées.

Il existe une image Windows et une image CentOS.  Dans la [Place de marché Azure](https://azuremarketplace.microsoft.com), vous pouvez rechercher les images de machine virtuelle à l’aide de la requête « batch rendering ».

Le portail Azure et Batch Explorer fournissent des outils GUI permettant de sélectionner une image de machine virtuelle de rendu lorsque vous créez un pool.  Si vous utilisez une API Batch, spécifiez les valeurs de propriété suivantes pour [ImageReference](/rest/api/batchservice/pool/add#imagereference) quand vous créez un pool :

| Serveur de publication | Offre | Sku | Version |
|---------|---------|---------|--------|
| lot | rendering-centos73 | rendu | latest |
| lot | rendering-windows2016 | rendu | latest |

D’autres options sont disponibles si des applications supplémentaires sont nécessaires sur les machines virtuelles du pool :

* Une image personnalisée provenant de la galerie Azure Compute Gallery :
  * À l’aide de cette option, vous pouvez configurer votre machine virtuelle avec les applications et versions spécifiques dont vous avez besoin. Pour plus d’informations, consultez [Créer un pool avec Azure Compute Gallery](batch-sig-images.md). Autodesk et Chaos Group ont modifié les logiciels Arnold et V-Ray de manière à effectuer la validation par rapport à un service de gestion des licences Azure Batch. Veillez à avoir les versions de ces applications comprenant cette prise en charge, sinon le service de licence avec paiement à l’utilisation ne fonctionnera pas. Les versions actuelles de Maya et de 3ds Max ne nécessitent pas de serveur de licences lorsqu’elles sont exécutées sans périphérique de contrôle (en mode batch ou ligne de commande). Contactez le support Azure si vous ne savez pas comment utiliser cette option.
* [Packages d’applications](./batch-application-packages.md) :
  * Empaquetez les fichiers d’application dans un ou plusieurs fichiers ZIP, chargez-les via le portail Azure et spécifiez le package dans la configuration du pool. Lorsque des machines virtuelles de pool sont créées, les fichiers ZIP sont téléchargés et les fichiers extraits.
* Les fichiers de ressources :
  * Les fichiers d’application sont chargés dans le Stockage Blob Azure, et vous spécifiez des références de fichier dans la [tâche de démarrage du pool](/rest/api/batchservice/pool/add#starttask). Lorsque des machines virtuelles de pool sont créées, les fichiers de ressources sont téléchargés sur chaque machine virtuelle.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Licences avec paiement à l’utilisation pour les applications préinstallées

Les applications qui seront utilisées et pour lesquelles il existe des frais de licence doivent être spécifiées dans la configuration du pool.

* Spécifiez la propriété `applicationLicenses` lors de la [création d’un pool](/rest/api/batchservice/pool/add#request-body).  Les valeurs suivantes peuvent être spécifiées dans le tableau de chaînes : « vray », « arnold », « 3dsmax », « maya ».
* Lorsque vous spécifiez une ou plusieurs applications, leur coût est ajouté à celui des machines virtuelles.  Les prix des applications sont listés dans la [page des prix Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

> [!NOTE]
> Si, au lieu de cela, vous vous connectez à un serveur de licences pour utiliser les applications de rendu, ne spécifiez pas la propriété `applicationLicenses`.

Vous pouvez utiliser le portail Azure ou Batch Explorer pour sélectionner des applications et afficher leur prix.

Si vous tentez d’utiliser une application alors que celle-ci n’a pas été spécifiée dans la propriété `applicationLicenses` de la configuration du pool ou qu’elle n’atteint pas un serveur de licences, l’exécution de l’application échoue avec une erreur de licence et un code de sortie différent de zéro.

### <a name="environment-variables-for-pre-installed-applications"></a>Variables d’environnement pour applications préinstallées

Si vous souhaitez créer la ligne de commande pour les tâches de rendu, vous devez spécifier l’emplacement d’installation des fichiers exécutables des applications de rendu.  Les variables d’environnement système ont été créées dans les images de machine virtuelle Place de marché Azure, qui peuvent être utilisées pour ne pas avoir à spécifier des chemins.  Ces variables d’environnement viennent s’ajouter aux [variables d’environnement standard de Batch](./batch-compute-node-environment-variables.md), qui sont créées pour chaque tâche.

|Application|Exécutable de l’application|Variable d’environnement|
|---------|---------|---------|
|Autodesk 3ds Max 2021|3dsmaxcmdio.exe|3DSMAX_2021_EXEC|
|Autodesk Maya 2020|render.exe|MAYA_2020_EXEC|
|Chaos Group V-Ray (version autonome)|vray.exe|VRAY_4.10.03_EXEC|
|Ligne de commande Arnold 2020|kick.exe|ARNOLD_2020_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

## <a name="azure-vm-families"></a>Familles de machines virtuelles Azure

Comme pour les autres charges de travail, les exigences système des applications de rendu varient, et les exigences de performances varient selon les travaux et les projets.  De nombreuses familles de machines virtuelles sont disponibles dans Azure, selon vos besoins : faible coût, meilleur rapport prix/performances, meilleures performances, etc.
Certaines applications de rendu, telles qu’Arnold, sont basées sur le processeur. D’autres, telles que V-Ray et Blender Cycles, peuvent utiliser des UC et/ou des GPU.
Pour obtenir la description des familles de machines virtuelles disponibles et de la taille des machines virtuelles, consultez [Types et tailles des machines virtuelles](../virtual-machines/sizes.md).

## <a name="low-priority-vms"></a>Machines virtuelles de faible priorité

Comme avec les autres charges de travail, les machines virtuelles de faible priorité peuvent être utilisées dans des pools Batch pour le rendu.  Les machines virtuelles de faible priorité fonctionnent comme des machines virtuelles dédiées normales, à la différence qu’elles utilisent la capacité Azure excédentaire et peuvent faire l’objet d’une remise importante.  La contrepartie à l’utilisation de machines virtuelles de faible priorité est que ces machines virtuelles risquent de ne pas pouvoir être réaffectées ou d’être reportées à tout moment, selon la capacité disponible. Pour cette raison, les machines virtuelles de faible priorité ne sont pas adaptées à tous les travaux de rendu. Par exemple, si des images mettent plusieurs heures à s’afficher, il est évidemment inacceptable d’interrompre et de redémarrer le rendu de ces images en raison de machines virtuelles anticipées.

Pour plus d’informations sur les caractéristiques des machines virtuelles de faible priorité et les différentes façons de les configurer à l’aide de Batch, consultez [Utiliser des machines virtuelles de faible priorité avec Batch](./batch-low-pri-vms.md).

## <a name="jobs-and-tasks"></a>Travaux et tâches

Aucune prise en charge de rendu n’est nécessaire pour les travaux et les tâches.  Le principal élément de configuration est la ligne de commande de tâche, qui doit référencer l’application nécessaire.
Lorsque des images de machine virtuelle issues de la Place de marché Azure sont utilisées, une bonne pratique consiste à utiliser les variables d’environnement pour spécifier le chemin et l’exécutable de l’application.

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur [l’utilisation des applications de rendu avec batch](batch-rendering-applications.md).
* En savoir plus sur [les options de stockage et de déplacement des données pour le rendu de ressource et les fichiers de sortie](batch-rendering-storage-data-movement.md).
