---
title: Hotpatch pour Windows Server Azure Edition (préversion)
description: Découvrez comment fonctionne Hotpatch pour Windows Server Azure Edition, et comment l’activer
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 32441732385ebd16c2edaa9b77a75382051b6ee1
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502879"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Patch à chaud pour les nouvelles machines virtuelles (préversion)

> [!IMPORTANT]
> Le patch à chaud est actuellement en préversion publique. Une procédure de consentement est requise pour utiliser la fonctionnalité de Patch à chaud (Hotpatch) décrite ci-dessous.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La Mise à jour corrective à chaud est une nouvelle façon d’installer les mises à jour sur les nouvelles machines virtuelles de l’_Edition du serveur Windows Azure_ ne nécessitant pas de redémarrage après l’installation. Cet article contient des informations relatives à Hotpatch pour les machines virtuelles de l’_Edition du serveur Windows Azure_ présentant les avantages suivants :
* Impact plus faible sur la charge de travail avec moins de redémarrages
* Déploiement plus rapide des mises à jour, car les packages sont plus petits, s’installent plus rapidement et offrent une orchestration des patchs plus facile à mettre en œuvre avec Azure Update Manager
* Meilleure protection, car les packages de mise à jour Hotpatch se limitent à l’étendue des mises à jour de sécurité Windows, qui s’installent plus rapidement et sans redémarrage

## <a name="how-hotpatch-works"></a>Fonctionnement du patch à chaud

Hotpatch fonctionne en établissant d’abord une base de référence avec la dernière mise à jour cumulative de Windows Update. Des patchs à chaud sont publiés régulièrement (par exemple, le deuxième mardi du mois) en s’appuyant sur cette base de référence. Les patchs à chaud contiennent des mises à jour qui ne nécessitent pas de redémarrage. Régulièrement (tous les trois mois au minimum), la base de référence est actualisée avec la dernière mise à jour cumulative.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Exemple de calendrier Hotpatch.":::

Il existe deux types de base de référence : **les bases de référence planifiées** et les **bases de référence non planifiées**.
*  Les **bases de référence planifiées** sont publiées à une cadence régulière. Dans l’intervalle, des patchs à chaud sont également publiés.  Les bases de référence planifiées incluent toutes les mises à jour de la _dernière mise à jour cumulative_ comparable pour le mois considéré, et nécessitent un redémarrage.
    * L’exemple de calendrier ci-dessus montre quatre publications de bases de référence planifiées au cours d’une année civile (cinq au total dans le diagramme), et huit publications de patchs à chaud.
* Les **bases de référence non planifiées** sont publiées quand une mise à jour importante (par exemple un correctif zero-day) est publiée, et que cette mise à jour particulière ne peut pas être publiée en tant que patch à chaud Hotpatch.  Quand des bases de référence non planifiées sont publiées, la publication d’un patch à chaud est remplacée par celle d’une base de référence non planifiée ce mois-là.  Les bases de référence non planifiées incluent également toutes les mises à jour de la _dernière mise à jour cumulative_ comparable pour le mois considéré, et nécessitent également un redémarrage.
    * L’exemple de calendrier ci-dessus montre deux bases de référence non planifiées qui remplacent les publications de patchs à chaud pour les mois considérés (le nombre réel de bases de référence non planifiées dans une année n’est pas connu à l’avance).

## <a name="regional-availability"></a>Disponibilité régionale
Hotpatch est disponible dans toutes les régions Azure du monde, en préversion. Les régions Azure Government ne sont pas prises en charge dans la préversion.

## <a name="how-to-get-started"></a>Pour commencer

> [!NOTE]
> Pendant la phase de préversion, vous pouvez démarrer en utilisant [ce lien](https://aka.ms/ws2022ae-portal-preview) dans le portail Azure.

Pour commencer à utiliser Hotpatch sur une nouvelle machine virtuelle, suivez les étapes ci-dessous :
1.  Activer l’accès à la préversion
    * Une seule activation de l’accès à la préversion est nécessaire par abonnement.
    * L’accès à la préversion peut être activé via une API, PowerShell ou l’interface CLI, comme décrit dans la section « Activation de l’accès en préversion » ci-après.
1.  Commencer à créer une machine virtuelle à partir du portail Azure
    * Pendant la phase de préversion, vous devez démarrer en utilisant [ce lien](https://aka.ms/ws2022ae-portal-preview).
1.  Fournir des détails lors de la création de la machine virtuelle
    * Assurez-vous qu’une image _Windows Server Azure Edition_ prise en charge est sélectionnée dans la liste déroulante Image.  Suivez [ce guide](automanage-windows-server-services-overview.md#getting-started-with-windows-server-azure-edition) pour déterminer les images prises en charge.
    * Sous l’onglet Gestion dans la section « Mises à jour du système d’exploitation invité », cochez la case « Activer les patchs à chaud » pour évaluer le patch à chaud en préversion.  Les options d’orchestration du patch sont définies sur « Orchestré par Azure ». 
    * Sous l’onglet Gestion dans la section « Azure Automanage », sélectionnez « Dev/Test » ou « Production » pour « Environnement Azure Automanage » afin d’évaluer les meilleures pratiques des machines Automanage pendant la préversion.
    
1. Créer votre machine virtuelle

## <a name="enabling-preview-access"></a>Activation de l’accès en préversion

### <a name="rest-api"></a>API REST

L’exemple suivant décrit comment activer la préversion pour votre abonnement :

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

L’inscription de la fonctionnalité peut prendre jusqu’à 15 minutes. Pour vérifier l’état de l’inscription :

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

Une fois que la fonctionnalité a été enregistrée pour votre abonnement, effectuez le processus d’inscription en propageant la modification dans le fournisseur de ressources de calcul.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

Utilisez l’applet de commande ```Register-AzProviderFeature``` pour activer la préversion pour votre abonnement.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

L’inscription de la fonctionnalité peut prendre jusqu’à 15 minutes. Pour vérifier l’état de l’inscription :

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Une fois que la fonctionnalité a été enregistrée pour votre abonnement, effectuez le processus d’inscription en propageant la modification dans le fournisseur de ressources de calcul.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

Utilisez ```az feature register``` pour activer la préversion pour votre abonnement.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

L’inscription de la fonctionnalité peut prendre jusqu’à 15 minutes. Pour vérifier l’état de l’inscription :
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Une fois que la fonctionnalité a été enregistrée pour votre abonnement, effectuez le processus d’inscription en propageant la modification dans le fournisseur de ressources de calcul.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Installation du patch

Durant la préversion, la [Mise à jour corrective automatique invité de la machine virtuelle](../virtual-machines/automatic-vm-guest-patching.md) est activée automatiquement pour toutes les machines virtuelles créées avec une image prise en charge par le _Serveur Windows Edition Azure_. Quand la mise à jour corrective automatique de la machine virtuelle invitée est activée :
* Les patchs classés comme Critique ou Sécurité sont automatiquement téléchargés et appliqués sur la machine virtuelle.
* Les patchs sont appliqués pendant les heures creuses dans le fuseau horaire de la machine virtuelle.
* L’orchestration des patchs est gérée par Azure et les patchs sont appliqués selon les [principes de première disponibilité](../virtual-machines/automatic-vm-guest-patching.md#availability-first-updates).
* L’intégrité des machines virtuelles, telle que déterminée par les signaux d’intégrité de la plateforme, est surveillée pour détecter les échecs de mise à jour corrective.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Fonctionnement de la mise à jour corrective automatique de l’invité de machine virtuelle

Quand la [mise à jour corrective automatique de la machine virtuelle invitée](../virtual-machines/automatic-vm-guest-patching.md) est activée sur une machine virtuelle, les patchs critiques et de sécurité disponibles sont téléchargés et appliqués automatiquement. Ce processus se lance automatiquement chaque mois quand de nouveaux patchs sont publiés. L’évaluation et l’installation des patchs sont automatiques, et le processus comprend le redémarrage de la machine virtuelle le cas échéant.

Une fois Hotpatch activé sur les machines virtuelles prises en charge par le _Serveur Windows Edition Azure_, la plupart des mises à jour de sécurité mensuelles sont fournies sous forme de patchs à chaud ne nécessitant pas de redémarrage. Les dernières mises à jour cumulatives envoyées au cours des mois où sont publiées des bases de référence planifiées ou non nécessitent le redémarrage de la machine virtuelle. Des patchs critiques ou de sécurité supplémentaires peuvent également être disponibles périodiquement, ce qui nécessite parfois le redémarrage de la machine virtuelle.

La machine virtuelle est évaluée automatiquement, régulièrement et plusieurs fois sur une période de 30 jours pour déterminer les patchs applicables. Cette évaluation automatique garantit que tous les patchs manquants sont découverts dès que possible.

Les patchs sont installés dans les 30 jours qui suivent les publications de patchs mensuelles, selon les [principes de première disponibilité](../virtual-machines/automatic-vm-guest-patching.md#availability-first-updates). Les patchs sont installés uniquement pendant les heures creuses de la machine virtuelle, en fonction de son fuseau horaire. La machine virtuelle doit fonctionner pendant les heures creuses pour que les patchs soient installés automatiquement. Si une machine virtuelle est mise hors tension pendant une évaluation périodique, elle est évaluée, et les patchs applicables sont installés automatiquement au cours de la prochaine évaluation périodique au moment de la mise sous tension de la machine virtuelle. La prochaine évaluation périodique a lieu généralement quelques jours après.

Les mises à jour de définitions et autres patchs non classifiés comme critiques ou de sécurité ne sont pas installés via la mise à jour corrective automatique de la machine virtuelle invitée.

## <a name="understanding-the-patch-status-for-your-vm"></a>Compréhension de l’état des patchs pour votre machine virtuelle

Pour voir l’état du patch de votre machine virtuelle, accédez à la section **Guest + host updates** (Mises à jour de l’hôte et de l’invité) pour votre machine virtuelle dans le portail Azure. Dans la section **Guest OS updates** (Mises à jour du système d’exploitation invité), cliquez sur Go to Hotpatch (Preview) (Accéder à Hotpatch (préversion)) pour voir l’état du dernier patch de votre machine virtuelle.

Sur cet écran, vous verrez l’état de Hotpatch pour votre machine virtuelle. Vous pouvez également vérifier s’il existe des patchs pour votre machine virtuelle qui n’ont pas été installés. Comme indiqué dans la section « Installation des patchs » ci-dessus, toutes les mises à jour de sécurité et critiques sont automatiquement installées sur votre machine virtuelle via la fonctionnalité de [mise à jour corrective automatique de la machine virtuelle invitée](../virtual-machines/automatic-vm-guest-patching.md). Aucune action supplémentaire n’est nécessaire. Les patchs ayant d’autres classifications de mise à jour ne sont pas installés automatiquement. À la place, ils sont consultables dans la liste des patchs disponibles sous l’onglet Update Compliance. Vous pouvez également voir l’historique des déploiements de mises à jour sur votre machine virtuelle via l’historique des mises à jour. L’historique des mises à jour des 30 derniers jours s’affiche avec les détails relatifs à l’installation des patchs.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Gestion des patchs à chaud.":::

Avec la mise à jour corrective automatique de la machine virtuelle invitée, votre machine virtuelle est régulièrement et automatiquement évaluée pour identifier les mises à jour disponibles. Ces évaluations périodiques vous permettent d’avoir la garantie que les patchs disponibles sont détectés. Vous pouvez voir les résultats de l’évaluation sur l’écran Mises à jour ci-dessus, notamment l’heure de la dernière évaluation. Vous pouvez également déclencher une évaluation des patchs à la demande pour votre machine virtuelle à tout moment via l’option Assess now (Évaluer maintenant), et passer en revue les résultats une fois l’évaluation effectuée.

Comme pour l’évaluation à la demande, vous pouvez également installer des patchs à la demande pour votre machine virtuelle à l’aide de l’option Install updates now (Installer les mises à jour maintenant). Ici, vous pouvez choisir d’installer toutes les mises à jour selon des classifications de patch spécifiques. Vous pouvez également spécifier les mises à jour à inclure ou à exclure en fournissant une liste d’articles individuels de la Base de connaissances. Les patchs installés à la demande ne sont pas installés selon les principes de première disponibilité. Ils peuvent nécessiter davantage de redémarrages et de temps d’arrêt de la part de la machine virtuelle pour l’installation des mises à jour.

## <a name="supported-updates"></a>Mises à jour prises en charge

Hotpatch couvre les mises à jour de sécurité Windows et maintient la parité avec le contenu des mises à jour de sécurité émises dans le canal des mises à jour Windows standard (hors Hotpatch).

Il existe des points importants à prendre en compte pour l’exécution d’une machine virtuelle prise en charge par le _Serveur Windows Edition Azure_ avec activation du Patch à chaud. Des redémarrages sont toujours nécessaires pour installer les mises à jour qui ne sont pas incluses dans le programme Hotpatch. Des redémarrages sont également nécessaires périodiquement après l’installation d’une nouvelle base de référence. Ces redémarrages permettent de maintenir la machine virtuelle synchronisée par rapport aux patchs non liés à la sécurité, et qui sont inclus dans la dernière mise à jour cumulative.
* Les patchs qui ne sont pas inclus dans le programme Hotpatch incluent les mises à jour non liées à la sécurité et publiées pour Windows ainsi que les mises à jour non Windows (par exemple les patchs .NET).  Ces types de patch doivent être installés durant la publication mensuelle d’une base de référence et nécessitent un redémarrage.

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="what-is-hotpatching"></a>Qu’est-ce que la mise à jour corrective à chaud ?

* La mise à jour corrective à chaud est une nouvelle façon d’installer les mises à jour sur une machine virtuelle prise en charge par le _Serveur Windows Edition Azure_ ne nécessitant pas de redémarrage après l’installation. Elle consiste à effectuer une mise à jour corrective du code en mémoire pour les processus en cours d’exécution, sans qu’il soit nécessaire de les redémarrer.

### <a name="how-does-hotpatching-work"></a>Comment fonctionne la mise à jour corrective à chaud ?

* La mise à jour corrective à chaud établit une base de référence avec la dernière mise à jour cumulative de Windows Update. Elle s’appuie ensuite sur cette base de référence afin de fournir des mises à jour qui ne nécessitent pas de redémarrage pour prendre effet.  La base de référence est mise à jour régulièrement avec une nouvelle mise à jour cumulative. La mise à jour cumulative comprend toutes les mises à jour de sécurité et de qualité, et nécessite un redémarrage.

### <a name="why-should-i-use-hotpatch"></a>Pourquoi dois-je utiliser Hotpatch ?

* Quand vous utilisez le Patch à chaud sur une image prise en charge par le _Serveur Windows Edition Azure_, votre machine virtuelle a une disponibilité plus élevée (moins de redémarrages) et bénéficie de mises à jour plus rapides (packages plus petits qui sont installés plus rapidement sans qu’il ne soit nécessaire de redémarrer les processus). Il en résulte une machine virtuelle toujours à jour et sécurisée.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Quels sont les types de mise à jour couverts par Hotpatch ?

* Hotpatch couvre les mises à jour de sécurité Windows.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>Quand recevrai-je la première mise à jour Hotpatch ?

* Les mises à jour Hotpatch sont généralement publiées le deuxième mardi de chaque mois. Pour plus d’informations, voir plus bas.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>À quoi va ressembler le calendrier Hotpatch ?

* La mise à jour corrective à chaud établit une base de référence avec la dernière mise à jour cumulative de Windows Update. Elle s’appuie ensuite sur cette base de référence en incluant des mises à jour Hotpatch publiées tous les mois.  Durant la préversion, les bases de référence sont publiées tous les trois mois au minimum. Consultez l’image ci-dessous pour un exemple de calendrier de publications trimestrielles sur une année (inclut des exemples de bases de référence non planifiées liées à des correctifs zero-day).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Exemple de calendrier Hotpatch.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>Les redémarrages sont-ils toujours nécessaires pour une machine virtuelle inscrite au programme Hotpatch ?

* Les redémarrages sont toujours nécessaires pour installer des mises à jour non incluses dans le programme Hotpatch. Elles sont nécessaires périodiquement après l’installation d’une base de référence (dernière mise à jour cumulative de Windows Update). Ce redémarrage permet de maintenir votre machine virtuelle synchronisée par rapport à tous les patchs inclus dans la mise à jour cumulative. Au début, les bases de référence (qui nécessitent un redémarrage) ont une cadence fixée à tous les trois mois. Celle-ci augmente ensuite.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Mes applications sont-elles affectées quand une mise à jour Hotpatch est installée ?

* Dans la mesure où Hotpatch corrige le code en mémoire pour les processus en cours d’exécution sans qu’il soit nécessaire de les redémarrer, vos applications ne sont pas affectées par le processus de mise à jour corrective. Notez que cela est indépendant des implications potentielles liées aux performances et aux fonctionnalités du patch lui-même.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>Puis-je désactiver Hotpatch sur ma machine virtuelle ?

* Vous pouvez désactiver Hotpatch sur une machine virtuelle via le portail Azure.  La désactivation de Hotpatch entraîne la désinscription de la machine virtuelle de Hotpatch. Le comportement classique de mise à jour de Windows Server est restauré sur la machine virtuelle.  Une fois que vous vous êtes désinscrit de Hotpatch sur une machine virtuelle, vous pouvez réinscrire cette dernière quand la prochaine base de référence Hotpatch est publiée.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>Puis-je effectuer une mise à niveau à partir de mon OS Windows Server existant ?

* Oui, la mise à niveau à partir de versions existantes du Serveur Windows (à savoir, Windows Server 2016 ou 2019) vers le _Centre de données Windows Server 2022 : Azure Edition_  est prise en charge. 

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>Puis-je utiliser Hotpatch pour les charges de travail de production durant la préversion ?

* Les préversions sont destinées à des tests uniquement et non à une utilisation dans des environnements de production.

### <a name="will-i-be-charged-during-the-preview"></a>Vais-je être facturé durant la phase de préversion ?

* La licence pour le _Serveur Windows Edition Azure_ est gratuite durant la préversion. Toutefois, le coût des infrastructures sous-jacentes configurées pour votre machine virtuelle (stockage, calcul, réseau, etc.) est toujours facturé sur votre abonnement.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Comment puis-je obtenir un support pour la résolution des problèmes de mise à jour corrective à chaud ?

* Vous pouvez créer un [ticket de cas de support technique](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Pour l’option Service, recherchez et sélectionnez **Machine virtuelle exécutant Windows** sous Calcul. Sélectionnez **Azure Features** (Fonctionnalités Azure) pour le type de problème, et **Automatic VM Guest Patching** (Mise à jour corrective automatique de la machine virtuelle invitée) pour le sous-type de problème.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez [Azure Update Management](../automation/update-management/overview.md)
* Apprenez-en davantage sur la [Mise à jour corrective automatique d’invité de machine virtuelle](../virtual-machines/automatic-vm-guest-patching.md)
* Apprenez-en davantage sur [Automanage pour Windows Server](automanage-windows-server-services-overview.md)