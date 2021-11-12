---
title: En savoir plus sur Azure Image Builder
description: Découvrez Azure Image Builder pour les machines virtuelles Azure.
author: sumit-kalra
ms.author: sukalra
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 07481838f5fca77d7e634003e04169a95d944117
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452069"
---
# <a name="azure-image-builder-overview"></a>Vue d’ensemble du Générateur d’images Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Machines virtuelles Windows :heavy_check_mark: Groupes identiques flexibles :heavy_check_mark: Groupes identiques uniformes

Des images de machine virtuelle standardisées permettent aux organisations de migrer vers le cloud en veillant à la cohérence de leurs déploiements. Les images incluent généralement des paramètres de sécurité et de configuration prédéfinis, et les logiciels nécessaires. La configuration de votre propre pipeline d’acquisition d’images nécessite du temps, une infrastructure et une configuration. Avec Azure VM Image Builder (Image Builder), il vous suffit de créer une configuration décrivant votre image et de l’envoyer au service, après quoi l’image est générée et distribuée.

Avec Image Builder, vous pouvez migrer votre pipeline de personnalisation d’images existant vers Azure tout en continuant à utiliser des scripts, des commandes et des processus existants pour personnaliser les images. À l’aide d’Image Builder, vous pouvez intégrer vos applications principales dans une image de machine virtuelle afin que vos machines virtuelles puissent prendre en charge les charges de travail immédiatement après leur création. Vous pouvez même ajouter des configurations afin de générer des images pour Azure Virtual Desktop ou en tant que VHD pour une utilisation dans Azure Stack ou pour faciliter l’exportation.

Image Builder vous permet de commencer avec des images Windows ou Linux, issues de la Place de marché Azure ou avec des images personnalisées existantes, et d’ajouter vos propres personnalisations. Vous pouvez également spécifier où vous souhaitez que vos images obtenues soient hébergées dans [Azure Compute Gallery](shared-image-galleries.md) (précédemment Shared Image Gallery), en tant qu’images managées ou disque dur virtuel.

## <a name="features"></a>Fonctionnalités

Bien qu’il soit possible de créer des images de machine virtuelle personnalisées manuellement ou avec d’autres outils, le processus peut être fastidieux et peu fiable. Azure VM Image Builder, qui est basé sur [HashiCorp Packer](https://www.packer.io/), vous offre les avantages d’un service managé.

### <a name="simplicity"></a>Simplicité

- Élimine la nécessité d’utiliser des outils, des processus complexes et des étapes manuelles pour créer une image de machine virtuelle. Image Builder simplifie tous ces détails et masque les exigences spécifiques à Azure, comme la nécessité de généraliser l’image (sysprep) tout en donnant aux utilisateurs plus avancés la possibilité de les remplacer.
- Image Builder peut s’intégrer à des pipelines de build d’images existants pour une expérience « click-and-go ». Vous pouvez simplement appeler Image Builder à partir de votre pipeline ou utiliser [Tâche DevOps du service Azure Image Builder (préversion)](./linux/image-builder-devops-task.md).
- Image Builder peut extraire des données de personnalisation à partir de diverses sources, ce qui évite de devoir les collecter dans un même endroit pour créer une image de machine virtuelle.
- L’intégration d’Image Builder avec la galerie Azure Compute Gallery vous offre un système de gestion des images qui vous permet de distribuer, de répliquer, de contrôler les versions et de mettre à l’échelle des images globalement. En outre, vous pouvez distribuer la même image obtenue en tant que disque dur virtuel ou en tant qu’une ou plusieurs images managées sans avoir à la recréer à partir de zéro.

### <a name="infrastructure-as-code"></a>Infrastructure en tant que code

- Il n’est pas nécessaire de gérer l’infrastructure à long terme (*comme les comptes de stockage pour conserver les données de personnalisation*) ou l’infrastructure temporaire (*comme une machine virtuelle temporaire pour créer l’image*). 
- Image Builder stocke vos artefacts de spécification et de personnalisation de création d’image de machine virtuelle en tant que ressources Azure, ce qui évite de conserver les définitions hors connexion et élimine le risque de dérives liées à l’environnement provoquées par des suppressions accidentelles ou des mises à jour.

### <a name="security"></a>Sécurité

- Image Builder permet la création d’images de référence (*qui peuvent inclure vos configurations minimales de sécurité et d’entreprise*) et permet à différents services d’affiner leur personnalisation. Ces images peuvent être maintenues sécurisées et conformes si vous utilisez Image Builder pour reconstruire rapidement une image de référence à l’aide de la dernière version corrigée d’une image source. Image Builder facilite également la création d’images qui respectent la Base de référence Azure Windows. Pour plus d’informations, consultez [Image Builder - Modèle de base Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/imagebuilder-windowsbaseline).
- Vous n’avez pas besoin de rendre vos artefacts de personnalisation accessibles publiquement pour permettre à Image Builder de les récupérer. Image Builder peut utiliser votre [identité managée Azure](../active-directory/managed-identities-azure-resources/overview.md) pour extraire ces ressources, et vous pouvez limiter les privilèges de cette identité aussi strictement que vous le souhaitez à l’aide d’Azure-RBAC. Cela signifie non seulement que vous pouvez garder vos artefacts secrets, mais qu’ils ne peuvent pas non plus être falsifiés par des intervenants non autorisés.
- Les copies des artefacts de personnalisation, des ressources de calcul et de stockage transitoires, et les images obtenues sont stockées en toute sécurité dans votre abonnement avec un accès contrôlé par Azure-RBAC. Cela comprend la machine virtuelle de build utilisée pour créer l’image personnalisée et garantit que vos scripts de personnalisation et vos fichiers ne sont pas copiés sur une machine virtuelle inconnue dans un abonnement inconnu. En outre, vous pouvez obtenir un niveau élevé d’isolation des charges de travail des autres clients à l’aide des [offres de machines virtuelles isolées](./isolation.md) pour la machine virtuelle de build.
- Vous pouvez connecter Image Builder à vos réseaux virtuels existants, afin de communiquer avec des serveurs de configuration existants (DSC, Chef, Puppet, etc.), des partages de fichiers ou tout autre serveur et service routable.

## <a name="regions"></a>Régions

Le service Azure Image Builder est disponible dans les régions suivantes : régions. 

>[!NOTE]
> Les images peuvent toujours être distribuées en dehors de ces régions.
> 
- USA Est
- USA Est 2
- Centre-USA Ouest
- USA Ouest
- USA Ouest 2
- États-Unis - partie centrale méridionale
- Europe Nord
- Europe Ouest
- Asie Sud-Est
- Sud-Australie Est
- Australie Est
- Sud du Royaume-Uni
- Ouest du Royaume-Uni

## <a name="os-support"></a>Prise en charge du système d’exploitation
Azure Image Builder prend en charge les images de système d’exploitation de base de la Place de marché Azure :
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise multisession/Professional
- Windows 2016
- Windows 2019

>[!IMPORTANT]
> Les systèmes d’exploitation listés ont été testés et fonctionnent désormais avec Azure Image Builder. Toutefois, Azure Image Builder doit fonctionner avec n’importe quelle Image Linux ou Windows sur le marketplace.

## <a name="how-it-works"></a>Fonctionnement

Azure VM Image Builder est un service Azure complètement managé qui est accessible à un fournisseur de ressources Azure. Fournissez au service une configuration qui spécifie l’image source, la personnalisation à effectuer et l’emplacement où la nouvelle image doit être distribuée. Le diagramme ci-dessous présente un workflow général :

![Tracé conceptuel du processus Azure Image Builder présentant les sources (Windows/Linux), les personnalisations (Shell, PowerShell, Redémarrage et mise à jour de Windows, ajout de fichiers) et la distribution mondiale avec Azure Compute Gallery](./media/image-builder-overview/image-builder-flow.png)

Les configurations de modèle peuvent être passées en utilisant PowerShell, Azure CLI, des modèles Azure Resource Manager et la Tâche DevOps du service Azure Image Builder. Quand vous les envoyez au service, nous créons une ressource de modèle d’image. Une fois la ressource de modèle d’image créée, vous voyez un groupe de ressources de mise en lots créé dans votre abonnement, au format : `IT_\<DestinationResourceGroup>_\<TemplateName>_\(GUID)`. Le groupe de ressources d’indexation contient des fichiers et des scripts référencés dans la personnalisation des fichiers, de l’interpréteur de commandes et de PowerShell dans la propriété ScriptURI.

Pour exécuter la build, vous appelez `Run` sur la ressource de modèle d’image. Le service déploie alors des ressources supplémentaires pour la build, comme une machine virtuelle, un réseau, un disque, une carte réseau, etc. Si vous générez une image sans utiliser un réseau virtuel existant, Image Builder déploie également une IP publique et un groupe de sécurité réseau. Le service se connecte à la machine virtuelle de la build à l’aide de SSH de WinRM. Si vous sélectionnez un réseau virtuel existant, le service est déployé avec Azure Private Link et aucune adresse IP publique n’est nécessaire. Pour plus d’informations, consultez [Vue d’ensemble du réseau Image Builder](./linux/image-builder-networking.md).

Une fois la build terminée, toutes les ressources sont supprimées, à l’exception du groupe de ressources d’indexation et du compte de stockage. Pour les supprimer, vous devez supprimer la ressource de modèle d’image. Vous pouvez également les conserver en vue de réexécuter la build.

Cette documentation contient plusieurs exemples et des guides pas à pas qui font référence à des modèles de configuration et à des solutions dans le [dépôt GitHub d’Azure image Builder](https://github.com/azure/azvmimagebuilder).

### <a name="move-support"></a>Prise en charge du déplacement
La ressource de modèle d’image est immuable et contient des liens vers les ressources et le groupe de ressources d’indexation. Par conséquent, le déplacement de ce type de ressource n’est pas pris en charge. Si vous voulez déplacer la ressource de modèle d’image, vérifiez que vous disposez d’une copie du modèle de configuration (extrayez la configuration existante de la ressource si vous ne l’avez pas), créez une ressource de modèle d’image dans le nouveau groupe de ressources sous un nouveau nom, puis supprimez la ressource de modèle d’image précédente. 

## <a name="permissions"></a>Autorisations
Lorsque vous vous inscrivez à AIB, cela permet au service AIB de créer, de gérer et de supprimer un groupe de ressources de mise en lots `(IT_*)`, et d’y ajouter des ressources requises pour la génération d’image. Cette opération est effectuée par un nom de principal du service AIB rendu disponible dans votre abonnement lors d’une inscription réussie.

Pour permettre à Azure VM Image Builder de distribuer des images aux images gérées ou à une instance Azure Compute Gallery, vous devez créer une identité attribuée par l’utilisateur Azure qui dispose des autorisations nécessaires pour lire et écrire des images. Si vous accédez au Stockage Azure, des autorisations sont nécessaires pour lire des conteneurs privés et publics.

Les autorisations sont expliquées plus en détail pour [PowerShell](./linux/image-builder-permissions-powershell.md)et [AZ CLI](./linux/image-builder-permissions-cli.md).

## <a name="costs"></a>Coûts
Nous vous facturons des coûts de calcul, de mise en réseau et de stockage lors de la création, de la génération et du stockage d’images avec le Générateur d’images Azure. Ces coûts sont similaires à ceux associés à la création manuelle d’images personnalisées. Pour les ressources, vous êtes facturé conformément aux tarifs Azure qui vous sont appliqués. 

Pendant le processus de création d’image, les fichiers sont téléchargés et stockés dans le groupe de ressources `IT_<DestinationResourceGroup>_<TemplateName>`, ce qui implique des coûts de stockage modiques. Si vous ne souhaitez pas conserver ces ressources, supprimez le **modèle d’image** une fois l’image générée.
 
Le Générateur d’images crée une machine virtuelle de taille D1v2, ainsi que le stockage et la mise en réseau nécessaires pour la machine virtuelle. Ces ressources durent pendant le temps d’exécution du processus de génération, puis sont supprimées une fois l’image créée par le Générateur d’images. 
 
Le Générateur d’images Azure distribue l’image aux régions choisies, ce qui peut occasionner des frais de sortie de réseau.

## <a name="hyper-v-generation"></a>Hyper-V Génération
Image Builder ne prend actuellement en charge que la création d’images Hyper-V de génération 1 (Gen1) dans Azure Compute Gallery. 
 
## <a name="next-steps"></a>Étapes suivantes 
 
Pour essayer le Générateur d’images Azure, voir les articles sur la génération d’images [Linux](./linux/image-builder.md) ou [Windows](./windows/image-builder.md).
