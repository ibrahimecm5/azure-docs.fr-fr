---
title: Créer des plans pour une offre de machine virtuelle sur la Place de marché Azure
description: Créer des plans pour une offre de machine virtuelle sur la Place de marché Azure.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 11/10/2021
ms.openlocfilehash: b8464f8f919b05647ef053c78573692975fd0b63
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305899"
---
# <a name="create-plans-for-a-virtual-machine-offer"></a>Créer des plans pour une offre de machine virtuelle

Dans la page **Vue d’ensemble du plan** (à partir du menu de navigation de gauche dans Espace partenaires), vous pouvez proposer différentes options de plan au sein d’une même offre. Une offre nécessite au moins un plan (précédemment appelé SKU), qui peut varier en termes de public de monétisation, de région Azure, de fonctionnalités ou d’images de machines virtuelles.

Vous pouvez créer jusqu’à 100 plans pour chaque offre, dont jusqu’à 45 peuvent être privés. En savoir plus sur les offres privées dans les [offres du marketplace commercial de Microsoft](private-offers.md).

Une fois que vous avez créé vos plans, sélectionnez l’onglet **Vue d’ensemble du plan** pour afficher :

- Noms des plans
- Modèles de licences
- Audience (publique ou privée)
- État actuel de la publication
- Actions disponibles

Les actions disponibles dans ce volet varient selon l’état actuel de votre plan.

- Si l’état du plan est Brouillon, sélectionnez **Supprimer le brouillon**.
- Si l’état du plan est Publié, sélectionnez **Déprécier un plan** ou **Synchroniser le public privé**.

## <a name="create-a-new-plan"></a>Créer un plan

Sélectionnez **+ Créer un plan** en haut de la page.

Dans la boîte de dialogue **Nouveau plan**, entrez un **ID de plan** unique pour chaque plan contenu dans cette offre. Cet ID sera visible par les clients dans l’adresse web du produit. Utilisez uniquement des lettres minuscules et des chiffres, des tirets ou des traits de soulignement, dans la limite de 50 caractères.

> [!NOTE]
> L’ID de plan ne peut pas être changé une fois que vous avez sélectionné **Créer**.

Entrez un **nom de plan**. Les clients voient ce nom quand ils décident du plan à sélectionner dans votre offre. Créez un nom unique qui identifie clairement les différences entre chaque plan. Par exemple, vous pouvez entrer **Windows Server** avec des plans *Paiement à l’utilisation*, *BYOL (apportez votre propre licence)* , *Avancé* et *Entreprise*.

Sélectionnez **Create** (Créer). Cela ouvre la page **Configuration du plan**.

## <a name="plan-setup"></a>Configuration du plan

Définissez la configuration générale du type de plan, indiquez si le plan réutilisera la configuration technique d’un autre plan, et identifiez les régions Azure où le plan doit être disponible. Les options que vous choisissez ici déterminent les champs affichés dans les autres volets du même plan.

### <a name="azure-regions"></a>Régions Azure

Votre plan doit être rendu disponible dans au moins une région Azure.

Sélectionnez **Azure Global** pour mettre votre plan à la disposition des clients de toutes les régions Azure qui disposent d’une intégration au marketplace commercial. Pour plus d’informations, consultez [Disponibilité géographique et prise en charge des devises](marketplace-geo-availability-currencies.md).

Sélectionnez **Azure Government** pour proposer votre plan dans la région [Azure Government](../azure-government/documentation-government-welcome.md). Cette région fournit un accès contrôlé aux clients d’entités gouvernementales fédérales, étatiques, locales ou tribales des États-Unis, ainsi qu’aux partenaires éligibles pour les servir. En tant qu’éditeur, il vous incombe de mettre en place l’ensemble des contrôles de conformité, des mesures de sécurité et des bonnes pratiques nécessaires. Azure Government utilise des réseaux et des centres de données qui sont physiquement isolés (situés aux États-Unis uniquement).

Avant de le publier dans [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md), testez et validez votre plan dans l’environnement, car certains points de terminaison peuvent être différents. Pour configurer et tester votre plan, demandez un compte d’essai à partir de la page [Microsoft Azure Government Trial](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Une fois votre plan publié et disponible dans une région Azure spécifique, vous ne pouvez pas supprimer cette région.

### <a name="azure-government-certifications"></a>Certifications Azure Government

Cette option est visible uniquement si vous avez sélectionné **Azure Government** comme région Azure dans la section précédente.

Les services Azure Government gèrent des données soumises à certaines réglementations et exigences gouvernementales. Par exemple, FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 et CJIS. Afin de renseigner les clients sur vos certifications pour ces programmes, vous pouvez fournir jusqu’à 100 liens décrivant ces certifications. Ces liens peuvent être des liens vers votre liste de plans directement sur le programme, ou des liens vers des descriptions de votre conformité avec ces certifications sur vos propres sites web. Ces liens sont visibles uniquement par les clients Azure Government.

Si vous effectuez des modifications, sélectionnez **Enregistrer le brouillon** avant de passer à l’onglet suivant du menu de navigation gauche **Référencement du plan**.

## <a name="plan-listing"></a>Liste des plans

Configurez les détails du plan. Ce volet affiche des informations spécifiques qui peuvent différer des autres plans de la même offre.

### <a name="plan-name"></a>Nom du plan

Ce champ est renseigné automatiquement avec le nom que vous avez donné à votre plan au moment de sa création. Ce nom apparaît sur la Place de marché Azure comme titre de ce plan. Il est limité à 100 caractères.

### <a name="plan-summary"></a>Résumé du plan

Fournissez un bref résumé de votre plan (pas de l’offre). Ce résumé est limité à 100 caractères.

### <a name="plan-description"></a>Description du plan

Décrivez ce qui rend ce plan logiciel unique, ainsi que les différences entre les plans de votre offre. Décrivez uniquement le plan (pas l’offre). La description du plan peut contenir jusqu’à 2 000 caractères.

Si vous effectuez des modifications, sélectionnez **Enregistrer le brouillon** avant de passer à l’onglet suivant du menu de navigation gauche **Tarification et disponibilité**.

## <a name="pricing-and-availability"></a>Tarification et disponibilité

Dans ce volet, vous configurez :

- Les marchés où ce plan est disponible. Chaque plan doit être disponible sur au moins un [marché](marketplace-geo-availability-currencies.md).
- Le tarif horaire.
- Si vous voulez que le plan soit visible à tous ou uniquement à des utilisateurs spécifiques (un public privé).

### <a name="markets"></a>Marchés

Chaque plan doit être disponible sur au moins un marché. La plupart des marchés sont sélectionnés par défaut. Pour modifier la liste, sélectionnez **Modifier les marchés** et cochez ou décochez les cases pour chaque emplacement de marché où ce plan doit (ou ne doit pas) être disponible à l’achat. Les utilisateurs des marchés sélectionnés peuvent toujours déployer l’offre dans toutes les régions Azure sélectionnées dans la section [« Configuration du plan »](#plan-setup).

Sélectionnez **Sélectionner uniquement les pays avec impôt versé par Microsoft** pour sélectionner les pays/régions dans lesquels Microsoft acquitte les taxes de vente et d’utilisation en votre nom. La publication en Chine se limite aux plans *gratuits* ou de type *Apportez votre propre licence* (BYOL).

Si vous avez déjà fixé des prix pour votre plan en dollar américain (USD) et ajouté un autre emplacement de marché, le prix de ce nouveau marché est calculé selon le taux de change en vigueur. Vérifiez toujours le prix de chaque Place de marché avant toute publication. Passez en revue la tarification en sélectionnant **Exporter les prix (xlsx)** après avoir enregistré vos modifications.

Quand vous supprimez un marché, les clients de ce marché utilisant des déploiements actifs ne pourront pas créer de nouveaux déploiements ou mettre à l’échelle leurs déploiements existants. Les déploiements existants ne sont pas affectés.

Cliquez sur **Save** (Enregistrer) pour continuer.

### <a name="pricing"></a>Tarifs

Pour le **modèle de licence**, sélectionnez **Plan avec facturation mensuelle à l’utilisation** afin de configurer les prix de ce plan, ou **BYOL (apportez votre propre licence)** pour permettre aux clients d’utiliser ce plan avec leur licence existante.

Pour un plan facturé mensuellement selon l’utilisation, utilisez l’une des trois options d’entrée de prix suivantes :

- **Par cœur** : indiquez le prix par cœur en USD. Microsoft calcule le prix par taille de cœur et le convertit dans les devises locales en appliquant le taux de change actuel.
- **Par taille de cœur** : indiquez les prix par taille de cœur en USD. Microsoft calcule le prix et le convertit dans les devises locales en appliquant le taux de change actuel.
- **Par marché et taille de cœur** : indiquez les prix de chaque taille de cœur pour tous les marchés. Vous pouvez importer les prix à partir d’une feuille de calcul.

Entrez un **prix par cœur**, puis sélectionnez **Prix par taille de cœur** pour afficher un tableau des calculs de prix/heure.

> [!NOTE]
> Enregistrez les modifications de la tarification pour autoriser l’exportation des données de tarification. Une fois qu’un prix pour un marché dans votre plan est publié, il ne peut pas être modifié ultérieurement. Pour vérifier que les prix sont corrects avant de les publier, exportez la feuille de calcul de prix et passez en revue les prix sur chaque marché.

### <a name="free-trial"></a>Version d’évaluation gratuite

Vous pouvez proposer à vos clients un **essai gratuit** d’un mois, de trois mois ou de six mois.

### <a name="plan-visibility"></a>Visibilité du plan

Vous pouvez concevoir chaque plan pour qu’il soit visible par tous les utilisateurs ou uniquement par un public présélectionné. Définissez l’appartenance à cette audience restreinte en utilisant des ID de locataire Azure, des ID d’abonnement ou les deux.

**Public** : votre plan peut être consulté par tout le monde.

**Privé** : votre plan ne peut être consulté que par un public présélectionné. Une fois votre plan publié en tant que plan privé, vous pouvez mettre à jour le public visé ou rendre le plan public. Une fois que vous avez rendu un plan public, il doit rester public. Vous ne pouvez pas le reconvertir en plan privé.

Définissez l’audience qui aura accès à ce plan privé en utilisant des *ID de locataire Azure*, des *ID d’abonnement* ou les deux. Le cas échéant, incluez une **description** de chaque ID de locataire ou ID d’abonnement Azure que vous attribuez. Ajoutez jusqu’à dix ID d’abonnement et ID de locataire manuellement, ou importez une feuille de calcul CSV si plus de dix ID sont nécessaires.

> [!NOTE]
> Un public privé ou restreint est différent du public de préversion que vous avez défini dans le volet **Préversion**. Un public de préversion peut accéder à votre offre *avant* sa publication sur la Place de marché Azure. La sélection d’un public privé ne s’applique qu’à un plan spécifique, tandis que le public de préversion peut voir tous les plans privés et publics dans le cadre de la validation.

Les offres privées ne sont pas prises en charge avec les abonnements Azure souscrits via un revendeur participant au programme des fournisseurs de solutions cloud (CSP).

### <a name="hide-plan"></a>Masquer le plan

Si vous comptez utiliser votre machine virtuelle uniquement de manière indirecte quand elle est référencée par un autre modèle de solution ou une application managée, cochez cette case pour publier la machine virtuelle tout en la masquant aux yeux des clients susceptibles de la rechercher ou d’y accéder directement.

Tout client Azure peut déployer l’offre à l’aide de PowerShell ou de l’interface CLI.  Si vous souhaitez mettre cette offre à disposition d’un nombre limité de clients, définissez le plan sur **Privé**.

Les plans masqués ne génèrent pas de liens d’aperçu. Toutefois, vous pouvez les tester [en procédant comme suit](azure-vm-create-faq.yml#how-do-i-test-a-hidden-preview-image-).

Si vous effectuez des modifications, sélectionnez **Enregistrer le brouillon** avant de passer à l’onglet suivant du menu de navigation gauche **Configuration technique**.

## <a name="technical-configuration"></a>Configuration technique

Fournissez les images et autres propriétés techniques associées à ce plan.

### <a name="reuse-technical-configuration"></a>Réutiliser la configuration technique

Cette option vous permet d’utiliser les mêmes paramètres de configuration technique pour tous les plans d’une même offre et donc de tirer parti du même ensemble d’images. Si vous activez l’option de _réutilisation de la configuration technique_, votre plan héritera des mêmes paramètres de configuration technique que le plan de base que vous sélectionnez.  Lorsque vous modifiez le plan de base, les modifications sont répercutées sur le plan réutilisant la configuration.

Voici quelques raisons courantes de réutiliser les paramètres de configuration technique d’un autre plan :

1. Les mêmes images sont disponibles pour *Paiement à l’utilisation* et *BYOL*.
2. Pour réutiliser la configuration technique d’un plan public pour un plan privé avec un prix différent. 
3. Votre solution se comporte différemment selon le plan que l’utilisateur choisit de déployer. Par exemple, le logiciel est le même, mais les fonctionnalités varient selon le plan.

Tirez parti d’[Azure Instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md) (IMDS) pour identifier le plan dans lequel votre solution est déployée afin de valider la licence ou l’activation des fonctionnalités appropriées.

Si vous décidez ultérieurement de publier des modifications différentes entre vos plans, vous pouvez les détacher. Pour détacher le plan qui réutilise la configuration technique, désélectionnez cette option pour le plan en question. Une fois détaché, votre plan aura les mêmes paramètres de configuration technique à la place de votre dernier paramètre, et la configuration de vos plans pourra varier. Un plan qui a été publié indépendamment par le passé ne peut pas réutiliser une configuration technique par la suite.

### <a name="operating-system"></a>Système d'exploitation

Sélectionnez la famille de systèmes d’exploitation **Windows** ou **Linux**.

Sélectionnez la **version** Windows ou le **fournisseur** Linux.

Entrez un **nom convivial** pour le système d’exploitation. Ce nom est visible par les clients.

### <a name="recommended-vm-sizes"></a>Tailles de machines virtuelles recommandées

Sélectionnez le lien pour choisir jusqu’à six tailles recommandées de machine virtuelle à afficher sur Place de marché Azure.

### <a name="open-ports"></a>Ouvrir les ports

Ajoutez des ports publics ou privés sur une machine virtuelle déployée.

### <a name="properties"></a>Propriétés

Voici une liste des propriétés qui peuvent être sélectionnées pour votre machine virtuelle.

- **Prend en charge la sauvegarde** : activez cette propriété si vos images prennent en charge la sauvegarde de machine virtuelle Azure. Apprenez-en davantage sur la [sauvegarde des machines virtuelles Azure](../backup/backup-azure-vms-introduction.md).

- **Prend en charge la mise en réseau accélérée** : activez cette propriété si les images de machine virtuelle pour ce plan prennent en charge la virtualisation d’e/s d’une racine unique (SR-IOV) sur une machine virtuelle, ce qui permet une faible latence et un débit élevé sur l’interface réseau. En savoir plus sur la [mise en réseau accélérée](https://go.microsoft.com/fwlink/?linkid=2124513).

- **Prend en charge la configuration Cloud-init** : activez cette propriété si les images de ce plan prennent en charge les scripts de publication Cloud-init. En savoir plus sur la [configuration cloud-init](../virtual-machines/linux/using-cloud-init.md).

- **Prend en charge hotpatch** : Windows Server Azure editions prend en charge la mise à jour corrective à chaud. En savoir plus sur la [correction à chaud](../automanage/automanage-hotpatch.md).

- **Prend en charge les extensions** : activez cette propriété si les images de ce plan prennent en charge les extensions. Les extensions sont de petites applications qui proposent une configuration post-déploiement et une automatisation sur les machines virtuelles Azure. En savoir plus sur les [extensions de machines virtuelles Azure](./azure-vm-create-certification-faq.yml#vm-extensions).

- **Est une appliance virtuelle réseau** : activez cette propriété si ce produit est une appliance virtuelle réseau. Une appliance virtuelle réseau est un produit qui exécute une ou plusieurs fonctions réseau, telles qu’un équilibreur de charge, une passerelle VPN, un pare-feu ou une passerelle d’application. En savoir plus sur les [appliances virtuelles réseau](https://go.microsoft.com/fwlink/?linkid=2155373).

- **Bureau à distance ou SSH désactivé** : activez cette propriété si les ordinateurs virtuels déployés avec ces images n’autorisent pas les clients à y accéder à l’aide du bureau à distance ou SSH. En savoir plus sur les [images de VM verrouillées](./azure-vm-create-certification-faq.yml#locked-down-or-ssh-disabled-offer).

- **Nécessite un modèle ARM personnalisé pour le déploiement** : activez cette propriété si les images de ce plan peuvent uniquement être déployées à l’aide d’un modèle ARM personnalisé. Pour plus d’informations, consultez la [section modèles personnalisés de résoudre les problèmes de certification des machines virtuelles](./azure-vm-create-certification-faq.yml#custom-templates).

### <a name="generations"></a>Générations

La génération d’une machine virtuelle définit le matériel virtuel qu’elle utilise. En fonction des besoins de votre client, vous pouvez publier une machine virtuelle de 1re génération, une machine virtuelle de 2e génération, ou les deux.

1. Lors de la création d’une offre, sélectionnez un **type de génération** et entrez les informations demandées :

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-1.png" alt-text="Vue de la section Détails de génération dans Espace partenaires.":::

2. Pour ajouter une autre génération à un plan, sélectionnez **Ajouter une génération**…

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="Vue du lien « Ajouter une génération ».":::

    …et entrez les informations demandées :

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-3.png" alt-text="Vue de la fenêtre des détails de la génération.":::

<!--    The **Generation ID** you choose will be visible to customers in places such as product URLs and ARM templates (if applicable). Use only lowercase, alphanumeric characters, dashes, or underscores; it cannot be modified once published.
-->
3. Pour mettre à jour une machine virtuelle existante pour laquelle une machine de 1re génération a déjà été publiée, modifiez les détails sur la page **Configuration technique**.

Pour en savoir plus sur les différences entre les capacités de la 1re génération et de la 2e génération, consultez [Prise en charge des machines virtuelles de génération 2 dans Azure](../virtual-machines/generation-2.md).

> [!NOTE]
> Une génération publiée requiert qu’au moins une version de l’image reste disponible pour les clients. Pour supprimer l’intégralité du plan (ainsi que l’ensemble des générations et images associées), sélectionnez **Deprecate plan (Déprécier le plan)** dans la page **Vue d’ensemble du plan** (voir la première section de cet article).

### <a name="vm-images"></a>Images de machine virtuelle

Spécifiez une version de disque et l’URI de signature d’accès partagé (SAS) pour les images de machine virtuelle. Ajoutez jusqu’à 16 disques de données pour chaque image de machine virtuelle. Spécifiez une seule nouvelle version d’image par plan à chaque envoi. Quand une image a été publiée, vous ne pouvez pas la modifier, mais vous pouvez la supprimer. La suppression d’une version empêche les utilisateurs nouveaux et existants de déployer une nouvelle instance de la version supprimée.

Ces deux champs obligatoires sont affichés dans l’image précédente ci-dessus :

- **Version du disque** : version de l’image que vous fournissez.
- **Lien du disque dur virtuel du système d’exploitation** : image stockée dans la galerie Azure Compute Gallery (anciennement Shared Image Gallery). Découvrez comment capturer votre image dans [Azure Compute Gallery](azure-vm-create-using-approved-base.md#capture-image).

Les disques de données [sélectionnez **Ajouter un disque de données (16 maximum)** ] sont également des URI de signature d’accès partagé VHD qui sont stockés dans leurs comptes de stockage Azure. Ajoutez une seule image par envoi dans un plan.

Quel que soit le système d’exploitation que vous utilisez, ajoutez uniquement le nombre minimal de disques de données dont la solution a besoin. Pendant le déploiement, les clients ne peuvent pas supprimer les disques qui font partie d’une image, mais ils peuvent toujours ajouter des disques pendant ou après le déploiement.

> [!NOTE]
> Si vous fournissez vos images à l’aide d’une signature d’accès partagé et que vous avez des disques de données, vous devez également les fournir en tant qu’URI SAS. Si vous utilisez une image partagée, ils sont capturés avec votre image dans Azure Compute Gallery. Une fois votre offre publiée sur la Place de marché Azure, vous pouvez supprimer l’image de votre stockage Azure ou d’Azure Compute Gallery.

Sélectionnez **Enregistrer le brouillon**, puis sélectionnez **← Vue d’ensemble du plan** en haut à gauche pour voir le plan que vous venez de créer.

Une fois l’image de machine virtuelle publiée, vous pouvez la supprimer de votre stockage Azure.

## <a name="next-steps"></a>Étapes suivantes

- [Revendre via des fournisseurs de solutions cloud](azure-vm-create-resell-csp.md)
