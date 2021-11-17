---
title: Azure Automanage pour machines virtuelles
description: Découvrez Azure Automanage pour machines virtuelles.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: b93169ced916f5d16adcbd11fcc2d2217a07b643
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451176"
---
# <a name="preview-azure-automanage-for-machine-best-practices"></a>Préversion : Meilleures pratiques Azure Automanage pour les machines

Cet article contient des informations sur les meilleures pratiques Azure Automanage pour machines, qui offre les avantages suivants :

- intègre intelligemment des machines virtuelles afin de sélectionner les meilleures pratiques pour les services Azure ;
- configure automatiquement chaque service conformément aux meilleures pratiques Azure ;
- Prend en charge la personnalisation des services de meilleures pratiques
- surveille la dérive et la corrige en cas de détection ;
- offre une interface simple (pointer, cliquer, définir, oublier).

## <a name="overview"></a>Vue d’ensemble

Les meilleures pratiques Azure Automanage pour machines est un service qui élimine la nécessité de découvrir, de savoir comment intégrer et de configurer certains services Azure susceptibles de tirer parti de votre machine virtuelle. Ces services sont considérés comme des services liés aux bonnes pratiques Azure. Ils contribuent à améliorer la fiabilité, la sécurité et la gestion des machines virtuelles. Voici des exemples de services : [Azure Update Management](../automation/update-management/overview.md) et [Sauvegarde Azure](../backup/backup-overview.md).

Une fois vos machines intégrées avec Azure Automanage, chaque service lié aux bonnes pratiques est configuré selon ses paramètres recommandés. Toutefois, si vous souhaitez personnaliser les services et les paramètres des meilleures pratiques, vous pouvez utiliser l’option [Profil personnalisé](#custom-profiles). 

Le service Azure Automanage surveille également automatiquement la dérive et la corrige en cas de détection. Cela signifie que, si votre machine virtuelle ou serveur compatible Arc est intégrée au service Azure Automanage, nous allons la surveiller en veillant à ce qu’elle reste conforme à son [Profil de configuration](#configuration-profile) tout au long de son cycle de vie. Si votre machine virtuelle dévie ou s’écarte du profil (par exemple, si un service est débarqué), nous le corrigeons et nous ramenons votre machine à l’état souhaité.

Automanage ne stocke ni ne traite les données client en dehors de la zone géographique où se trouvent vos machines virtuelles. Par exemple, dans la région Asie Sud-Est, Automanage ne stocke ni ne traite les données en dehors de l’Asie Sud-Est.

> [!NOTE]
> Automanage peut être activé sur les machines virtuelles Azure, ainsi que sur les serveurs Azure avec Arc. La gestion automanage n’est pas disponible dans le cloud du gouvernement des États-Unis pour le moment.

## <a name="prerequisites"></a>Prérequis

Il existe plusieurs conditions préalables à prendre en compte avant d’essayer d’activer le service Azure Automanage sur vos machines virtuelles.

- [Versions de Windows Server](automanage-windows-server.md#supported-windows-server-versions) et [Distributions Linux](automanage-linux.md#supported-linux-distributions-and-versions) prises en charge
- Les machines virtuelles doivent se trouver dans une région prise en charge (voir ci-dessous)
- L’utilisateur doit disposer des autorisations appropriées (voir ci-dessous)
- Automanage ne prend pas en charge les abonnements sandbox à ce stade
- La gestion automanage ne prend pas en charge Windows 10 à ce stade

### <a name="supported-regions"></a>Régions prises en charge
Automanage prend uniquement en charge les machines virtuelles situées dans les régions suivantes :
* Europe Ouest
* Europe Nord
* USA Centre
* USA Est
* USA Est 2
* USA Ouest
* USA Ouest 2
* Centre du Canada
* Centre-USA Ouest
* États-Unis - partie centrale méridionale
* Japon Est
* Sud du Royaume-Uni
* Australie Est
* Australie Sud-Est
* Asie Sud-Est

### <a name="required-rbac-permissions"></a>Autorisations RBAC nécessaires
Pour intégrer, Automanage requiert des rôles RBAC légèrement différents selon que vous activez Automanage pour la première fois dans un abonnement.

Si vous activez Automanage pour la première fois dans un abonnement :
* Rôle **Propriétaire** pour le ou les abonnements contenant vos machines, _**ou**_
* Rôles **Contributeur** et **Administrateur de l’accès utilisateur** pour le ou les abonnements contenant vos machines

Si vous activez Automanage sur une machine dans un abonnement qui a déjà des machines Automanage :
* Rôle **Contributeur** pour le groupe de ressources contenant vos machines

Le service Automanage accorde l’autorisation de **Contributeur** à cette application interne (ID d’application de l’API Automanage : d828acde-4B48-47F5-a6e8-52460104a052) pour effectuer des actions sur les machines Automanage.

> [!NOTE]
> Si vous souhaitez utiliser Automanage sur une machine virtuelle qui est connectée à un espace de travail dans un autre abonnement, vous devez disposer des autorisations décrites ci-dessus pour chaque abonnement.

## <a name="participating-services"></a>Services participant

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="Services intégrés de manière intelligente.":::

Pour obtenir la liste complète des services Azure concernés ainsi que leur profil pris en charge, consultez :
- [Automanage pour Linux](automanage-linux.md)
- [Automanage pour Windows Server](automanage-windows-server.md)

 Nous vous intégrerons automatiquement à ces services participants lorsque vous utiliserez les profils de configuration des meilleures pratiques. Ils sont essentiels pour notre livre blanc sur les meilleures pratiques, que vous pouvez trouver dans notre [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management).


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Activation du service Automanage pour machines virtuelles dans le portail Azure

Dans le Portail Azure, vous pouvez activer Automanage sur une machine virtuelle existante. Pour les étapes concises de ce processus, consultez le [Démarrage rapide du service Automanage pour machines virtuelles](quick-create-virtual-machines-portal.md).

Si c’est la première fois que vous activez le service Automanage pour votre machine virtuelle, vous pouvez rechercher dans le portail Azure **Automanage – Meilleures pratiques pour les machines Azure**. Cliquez sur **Activer sur une machine virtuelle existante**, sélectionnez le [profil de configuration](#configuration-profile) que vous souhaitez utiliser, puis sélectionnez les machines que vous souhaitez intégrer. Cliquez sur **Activer**. Vous avez terminé.

La seule situation dans laquelle vous pourriez être amené à interagir avec cette machine pour gérer ces services serait si nous tentions de corriger votre machine virtuelle sans succès. Quand nous parvenons à corriger correctement votre machine virtuelle, nous rétablissons sa conformité sans même vous en informer. Pour plus d’informations, consultez [État des machines virtuelles](#status-of-vms).

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Activation du service Automanage pour machines virtuelles à l’aide d’Azure Policy
Vous pouvez également activer le service Automanage sur les machines virtuelles à grande échelle à l’aide de la stratégie intégrée Azure Policy. La stratégie a un effet DeployIfNotExists, ce qui signifie que toutes les machines virtuelles éligibles situées dans l’étendue de la stratégie seront automatiquement intégrées à Automanage - Bonnes pratiques pour les machines virtuelles.

Vous trouverez [ici](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3) un lien direct vers la stratégie.

Pour plus d’informations, consultez Comment activer la [stratégie intégrée Automanage](virtual-machines-policy-enable.md). 

## <a name="configuration-profile"></a>Profil de configuration

Lorsque vous activez le service Automanage pour votre machine, un profil de configuration est requis. Les profils de configuration constituent le fondement de ce service. Ils définissent les services que nous intégrons sur vos machines et, dans une certaine mesure, la configuration de ces services.

### <a name="best-practice-configuration-profiles"></a>Profils de configuration des meilleures pratiques

Deux profils de configuration des meilleures pratiques sont actuellement disponibles.

- Le profil **Dev/Test** est conçu pour les machines de dev/test.
- Le profil **Production** est destiné à la production.

La raison de cette différentiation est que certains services sont recommandés en fonction de la charge de travail en cours d’exécution. Par exemple, sur une machine de Production, nous vous intégrons automatiquement au service Sauvegarde Azure. En revanche, pour une machine de Dev/Test, un service de sauvegarde occasionnerait un coût inutile, car les machines de Dev/Test ont généralement une moindre incidence sur l’activité.

### <a name="custom-profiles"></a>Profils personnalisés

Les profils personnalisés vous permettent de personnaliser les services et les paramètres que vous souhaitez appliquer à vos machines. Il s’agit d’une option intéressante si vos exigences informatiques diffèrent des meilleures pratiques. Par exemple, si vous ne souhaitez pas utiliser la solution Microsoft Antimalware car votre service informatique vous oblige à utiliser une autre solution anti-programme malveillant, vous pouvez simplement désactiver Microsoft Antimalware lors de la création d’un profil personnalisé.

> [!NOTE]
> Dans le profil de configuration Dev/Test des meilleures pratiques, nous ne sauvegardons pas du tout la machine virtuelle.

> [!NOTE]
> Si vous souhaitez modifier le profil de configuration d’une machine, vous pouvez simplement le réactiver avec le profil de configuration souhaité. Toutefois, si l’état de votre machine est « Nécessite une mise à niveau », vous devez d’abord le désactiver, puis réactiver Automanage. 

Pour obtenir la liste complète des services Azure concernés et pour savoir s’ils prennent en charge les préférences, consultez :
- [Automanage pour Linux](automanage-windows-server.md)
- [Automanage pour Windows Server](automanage-windows-server.md)


## <a name="status-of-vms"></a>État des machines virtuelles

Dans le Portail Azure, accédez à la page **Automanage – Meilleurs pratiques pour les machines Azure**, qui répertorie toutes vos machines gérées automatiquement. Vous y verrez l’état global de chaque machine.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Liste des machines virtuelles configurées.":::

Pour chaque machine répertoriée, les détails suivants s’affichent : nom, profil de configuration, état, type de ressource, groupe de ressources, abonnement.

La colonne **État** peut afficher les états suivants :
- *En cours* : la machine virtuelle vient d’être activée et est en cours de configuration
- *Conforme* : la machine virtuelle est configurée et aucune dérive n’est détectée
- *Non conforme* : la machine virtuelle a été dérivée et nous n’avons pas pu la corriger, ou la machine est hors tension et Automanage tente d’intégrer ou de corriger la machine virtuelle lors de sa prochaine exécution
- *Mise à niveau nécessaire* : la machine virtuelle est intégrée à une version antérieure d’Automanage et doit être [mise à niveau](automanage-upgrade.md) vers la dernière version

Si vous voyez l'**État** comme *Non conforme*, vous pouvez résoudre le problème en cliquant sur l’état dans le portail et en utilisant les liens de résolution des problèmes fournis


## <a name="disabling-automanage-for-vms"></a>Désactivation du service Automanage pour machines virtuelles

Vous pouvez décider un jour de désactiver le service Automanage sur certaines machines virtuelles. Par exemple, si votre machine exécute une charge de travail sécurisée extrêmement sensible, et si vous devez la verrouiller encore plus que ne le ferait Azure naturellement, vous devez configurer la machine en dehors des meilleures pratiques Azure.

Pour ce faire, dans le Portail Azure, accédez à la page **Automanage – Meilleures pratiques pour les machines Azure** qui répertorie toutes vos machines virtuelles gérées automatiquement. Activez la case à cocher en regard de la machine virtuelle que vous souhaitez désactiver dans le service Automanage, puis cliquez sur le bouton **Désactiver la gestion automatique**.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Désactivation du service Automanage sur une machine virtuelle.":::

Lisez attentivement le message dans la fenêtre contextuelle qui s’affiche avant d’accepter de **Désactiver**.

> [!NOTE]
> La désactivation de l’autogestion dans une machine virtuelle entraîne le comportement suivant :
>
> - La configuration de la machine virtuelle et des services qu’elle contient ne change pas.
> - Tous les frais associés à ces services resteront facturables et seront accumulés.
> - La surveillance de la dérive d’Automanage s’arrête immédiatement.


Tout d’abord, nous n’annulons l’intégration de la machine virtuelle à l’un des services auxquels nous l’avons intégrée et sur lesquels nous l’avons configurée. Par conséquent, les frais associés à ces services resteront facturables. Vous devez donc annuler l’intégration si nécessaire. Dans ce cas, tout comportement du service Automanage s’arrête immédiatement. Par exemple, nous cessons surveiller la dérive de la machine virtuelle.

## <a name="automanage-and-azure-disk-encryption"></a>Automanage et Azure Disk Encryption
Automanage est compatible avec les machines virtuelles sur lesquelles Azure Disk Encryption (ADE) est activé.

Si vous utilisez l’environnement de production, vous serez également intégré à Sauvegarde Azure. Il existe une condition préalable à l’utilisation d’ADE et de Sauvegarde Azure :
* Avant d’intégrer votre machine virtuelle avec ADE à l’environnement de production d’Automanage, assurez-vous d’avoir suivi les étapes décrites dans la section **Avant de commencer** de [ce document](../backup/backup-azure-vms-encryption.md#before-you-start).

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a expliqué que le service Automanage pour machines vous évite d’avoir à connaître, intégrer et configurer les meilleures pratiques pour les services Azure. En outre, si une machine que vous avez intégrée au service Automanage pour machines virtuelles dérive des profils de configuration, nous rétablissons automatiquement la conformité.

Essayez d’activer la Automanage pour les machines virtuelles Azure ou les serveurs compatibles Arc dans le Portail Azure.

> [!div class="nextstepaction"]
> [Activer le service Automanage pour machines virtuelles dans le portail Azure](quick-create-virtual-machines-portal.md)
