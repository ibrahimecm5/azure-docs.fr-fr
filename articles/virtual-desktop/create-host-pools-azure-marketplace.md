---
title: Pool d’hôtes Azure Virtual Desktop Portail Azure - Azure
description: Guide pratique pour créer un pool d’hôtes Azure Virtual Desktop à l’aide du portail Azure.
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 3b95302be3eda412f6941abe359f6da63e235d24
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492035"
---
# <a name="tutorial-create-a-host-pool"></a>Tutoriel : créer un pool d’hôtes

>[!IMPORTANT]
>Ce contenu s’applique à Azure Virtual Desktop avec des objets Azure Virtual Desktop pour Azure Resource Manager. Si vous utilisez Azure Virtual Desktop (Classic) sans objets Azure Resource Manager, consultez [cet article](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md). Les objets que vous créez avec Azure Virtual Desktop (classique) ne peuvent pas être gérés avec le portail Azure.

Les pools d’hôtes sont des ensembles d’une ou plusieurs machines virtuelles identiques, aussi appelés « hôtes de session », dans des environnements Azure Virtual Desktop. Chaque pool d’hôtes peut contenir un groupe d’applications avec lequel les utilisateurs peuvent interagir comme ils le feraient sur un ordinateur de bureau physique. Si vous souhaitez en savoir plus sur l’architecture de déploiement, consultez la présentation de l'[environnement d’Azure Virtual Desktop](environment-setup.md). Si vous êtes développeur d’applications et que vous utilisez la diffusion d’applications à distance pour Azure Virtual Desktop, vos clients ou utilisateurs peuvent utiliser vos applications comme des applications locales sur un appareil physique. Pour plus d’informations sur l’utilisation d’Azure Virtual Desktop en tant que développeur d’applications, consultez notre documentation sur la [diffusion en continu d’applications d’Azure Virtual Desktop](./remote-app-streaming/custom-apps.md).

>[!NOTE]
>Si vous êtes développeur d’applications et que vous utilisez la diffusion d’applications à distance pour Azure Virtual Desktop et que les utilisateurs de votre application se trouvent dans la même organisation que votre déploiement, vous pouvez utiliser votre locataire Azure existant pour créer votre pool hôte. Si vos utilisateurs sont en dehors de votre organisation, pour des raisons de sécurité, vous devez créer des locataires Azure distincts avec au moins un pool hôte pour chaque organisation. En savoir plus sur les pratiques que nous vous recommandons de suivre pour garantir la sécurité de votre déploiement aux [recommandations d’architecture](./remote-app-streaming/architecture-recs.md).

Cet article vous guide tout au long du processus de création d’un pool d’hôtes pour un environnement Azure Virtual Desktop par le biais du portail Azure. Cette méthode offre une interface basée sur un navigateur pour créer un pool d’hôtes dans Azure Virtual Desktop, créer un groupe de ressources avec des machines virtuelles dans un abonnement Azure, joindre ces machines virtuelles au domaine Azure Active Directory (AD) ou au locataire Azure Active Directory (Azure AD) et inscrire les machines virtuelles auprès d’Azure Virtual Desktop.

## <a name="prerequisites"></a>Prérequis

Il existe deux ensembles d’exigences différents selon que vous êtes un professionnel de l’informatique qui configure un déploiement pour votre organisation ou un développeur d’applications qui dessert des applications pour les clients.

### <a name="requirements-for-it-professionals"></a>Exigences pour les professionnels de l’informatique

Vous devez entrer les paramètres suivants pour créer un pool d’hôtes :

- Nom de l’image de machine virtuelle
- Configuration des machines virtuelles
- Propriétés du domaine et du réseau
- Propriétés du pool d’hôtes Azure Virtual Desktop

Vous devez également connaître les éléments suivants :

- Emplacement de la source de l’image que vous voulez utiliser. Provient-elle de la galerie Azure ou s’agit-il d’une image personnalisée ?
- Informations d’identification de jonction de domaine.

### <a name="requirements-for-app-developers"></a>Exigences pour les développeurs d’applications

Si vous êtes un développeur d’applications qui utilise la diffusion d’applications à distance pour Azure Virtual Desktop pour fournir des applications à vos clients, voici ce dont vous aurez besoin pour commencer :

- Si vous envisagez de servir l’application de votre organisation aux utilisateurs finaux, assurez-vous que cette application est bien prête. Pour en savoir plus, consultez [Comment héberger des applications personnalisées avec Azure Virtual Desktop](./remote-app-streaming/custom-apps.md).
- Si les options existantes de l’image de la Galerie Azure ne répondent pas à vos besoins, vous devrez également créer votre propre image personnalisée pour les hôtes de session de vos machines virtuelles. Pour en savoir plus sur la création d’images de machines virtuelles, voir [Préparer un disque dur virtuel Windows à charger sur Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) et [Créer une image managée d’une machine virtuelle généralisée dans Azure](../virtual-machines/windows/capture-image-resource.md).
- Informations d’identification de jonction de domaine. Si vous ne disposez pas déjà d’un système de gestion des identités compatible avec Azure Virtual Desktop, vous devez configurer la gestion des identités pour votre pool hôte. Pour en savoir plus, consultez [Configurer des identités managées](./remote-app-streaming/identities.md).

### <a name="final-requirements"></a>Exigences finales

Enfin, assurez-vous d’avoir inscrit le fournisseur de ressources Microsoft.DesktopVirtualization. Si vous ne l’avez pas encore fait, accédez à **Abonnements**, sélectionnez le nom de votre abonnement, puis **Fournisseurs de ressources**. Recherchez **DesktopVirtualization**, sélectionnez **Microsoft.DesktopVirtualization**, puis **Inscrire**.

Si vous êtes un professionnel de l’informatique et que vous créer un réseau, lorsque vous créez un pool d’hôtes Azure Virtual Desktop avec le modèle Azure Resource Manager, vous pouvez créer un ordinateur virtuel à partir de la Galerie Azure, d’une image managée ou d’une image non managée. Pour en savoir plus sur la création d’images de machines virtuelles, voir [Préparer un disque dur virtuel Windows à charger sur Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) et [Créer une image managée d’une machine virtuelle généralisée dans Azure](../virtual-machines/windows/capture-image-resource.md). (Si vous êtes développeur d’applications, vous n’avez pas à vous soucier de cette partie.)

Enfin, si vous n’avez pas encore d’abonnement Azure, veillez à [créer un compte](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer à suivre ces instructions.

## <a name="begin-the-host-pool-setup-process"></a>Démarrer le processus de création du pool d’hôtes

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour commencer à créer votre nouveau pool d’hôtes

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com/).
   
   >[!NOTE]
   > Si vous vous connectez au portail US Gov, accédez à [https://portal.azure.us/](https://portal.azure.us/) à la place.
   > 
   >Si vous accédez au portail Azure Chine, accédez à [https://portal.azure.cn/](https://portal.azure.cn/).

2. Entrez **Azure Virtual Desktop** dans la barre de recherche, puis recherchez et sélectionnez **Azure Virtual Desktop** sous Services.

3. Dans la page de vue d’ensemble d’**Azure Virtual Desktop**, sélectionnez **Créer un pool d’hôtes**.

4. Sous l’onglet **De base**, sélectionnez l’abonnement approprié sous Détails du projet.

5. Sélectionnez un groupe de ressources existant dans le menu déroulant ou sélectionnez **Créer** pour créer un groupe de ressources.

6. Entrez un nom unique pour votre pool d’hôtes.

7. Dans le champ Emplacement, sélectionnez dans le menu déroulant la région dans laquelle vous souhaitez créer le pool d’hôtes.

   La zone géographique Azure associée aux régions sélectionnées est l’emplacement où seront stockées les métadonnées de ce pool d’hôtes et ses objets connexes. Veillez à choisir les régions à l’intérieur de la zone géographique dans laquelle vous souhaitez stocker les métadonnées de service.

     > [!div class="mx-imgBorder"]
     > ![Capture d’écran du portail Azure montrant le champ Emplacement avec l’emplacement USA Est sélectionné. En regard du champ figure le texte « Les métadonnées seront stockées dans USA Est ».](media/portal-location-field.png)
  
   >[!NOTE]
   > Si vous souhaitez créer votre pool d’hôtes dans [une région prise en charge](data-locations.md) en dehors des États-Unis, vous devez réinscrire le fournisseur de ressources. Après la réinscription, les autres régions doivent figurer dans la liste déroulante où est sélectionné l’emplacement. Découvrez comment vous réinscrire dans notre article de résolution des problèmes de [création de pools d’hôtes](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects).

8. Sous Type de pool d’hôtes, indiquez si votre pool d’hôtes sera **Personnel** ou **Groupé**.

    - Si vous choisissez **Personnel**, sélectionnez **Automatique** ou **Direct** dans le champ Type d’affectation.

      > [!div class="mx-imgBorder"]
      > ![Capture d’écran du menu déroulant du champ Type d’affectation. L’utilisateur a sélectionné Automatique.](media/assignment-type-field.png)

9.  Si vous choisissez **Groupé**, entrez les informations suivantes :

     - Pour **Limite de session maximale**, entrez le nombre maximal d’utilisateurs dont vous souhaitez équilibrer la charge sur un seul hôte de session.
     - Pour **Algorithme d’équilibrage de charge**, choisissez l’équilibrage de charge en profondeur d’abord ou en largeur d’abord, en fonction de votre modèle d’utilisation. En savoir plus sur la signification de chacune de ces options sur les [méthodes d’équilibrage de charge des pools hôtes](host-pool-load-balancing.md).

       > [!div class="mx-imgBorder"]
       > ![Capture d’écran du champ Type d’affectation avec l’option « Groupé » sélectionnée. L’utilisateur pointe son curseur sur Largeur d’abord dans le menu déroulant de l’équilibrage de charge.](media/pooled-assignment-type.png)

10. Sélectionnez **Suivant : Machines virtuelles >** .

11. Si vous avez déjà créé des machines virtuelles et que vous souhaitez les utiliser avec le nouveau pool d’hôtes, sélectionnez **Non**, **Suivant : Espace de travail >** , puis accédez à la section [Informations sur l’espace de travail](#workspace-information). Si vous souhaitez créer des machines virtuelles et les inscrire auprès du nouveau pool d’hôtes, sélectionnez **Oui**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Commencez par préparer votre environnement pour Azure CLI :

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Après vous être connecté, utilisez la commande [az desktopvirtualization hostpool create](/cli/azure/desktopvirtualization#az_desktopvirtualization_hostpool_create) pour créer le nouveau pool d’hôtes, en créant éventuellement un jeton d’inscription pour les hôtes de session pour rejoindre le pool d’hôtes :

```azurecli
az desktopvirtualization hostpool create --name "MyHostPool" \
    --resource-group "MyResourceGroup" \ 
    --location "MyLocation" \
    --host-pool-type "Pooled" \
    --load-balancer-type "BreadthFirst" \
    --max-session-limit 999 \
    --personal-desktop-assignment-type "Automatic"  \
    --registration-info expiration-time="2022-03-22T14:01:54.9571247Z" registration-token-operation="Update" \
    --sso-context "KeyVaultPath" \
    --description "Description of this host pool" \
    --friendly-name "Friendly name of this host pool" \
    --tags tag1="value1" tag2="value2" 
```

Si vous souhaitez créer des machines virtuelles et les inscrire auprès du nouveau pool d’hôtes, continuez avec la section suivante. Si vous avez déjà créé des machines virtuelles et que vous souhaitez les utiliser avec le nouveau pool d’hôtes, sautez à la section [Informations sur l’espace de travail](#workspace-information). 

---

Maintenant que vous avez créé un pool d’hôtes, passons à la partie suivante du processus de configuration, où nous allons créer la machine virtuelle.

## <a name="virtual-machine-details"></a>Détails de machine virtuelle

La première partie étant terminée, vous allez devoir configurer votre machine virtuelle.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour configurer votre machine virtuelle durant le processus de configuration du pool d’hôtes du Portail Azure :

1. Sous **Groupe de ressources**, choisissez le groupe de ressources dans lequel vous souhaitez créer les machines virtuelles. Il peut s’agir d’un groupe de ressources différent de celui que vous avez utilisé pour le pool d’hôtes.

2. Après cela, spécifiez un **Préfixe de nom** pour nommer les machines virtuelles créées par le processus de création. Le suffixe sera `-` avec des nombres commençant à 0.

3. Choisissez l’**emplacement de machine virtuelle** où vous souhaitez créer les machines virtuelles. Elle peut être identique ou différente de la région que vous avez sélectionnée pour le pool d’hôtes. N’oubliez pas que les prix des machines virtuelles varient selon la région et que les emplacements des machines virtuelles doivent être proches de leurs utilisateurs si possible pour optimiser les performances. En savoir plus sur les [Emplacements de données pour Azure Virtual Desktop](data-locations.md).
   
4. Ensuite, choisissez l’option de disponibilité qui correspond le mieux à vos besoins. Pour en découvrir plus sur l’option qui vous convient, consultez [Options de disponibilité pour les machines virtuelles dans Azure](../virtual-machines/availability.md) et [notre Forum aux questions](./faq.yml#which-availability-option-is-best-for-me-).
   
   > [!div class="mx-imgBorder"]
   > ![Capture d’écran du menu déroulant des options de disponibilité. L’option « Zone de disponibilité » est mise en évidence.](media/availability-zone.png)

5. Ensuite, choisissez l’image qui doit être utilisée pour créer la machine virtuelle. Vous pouvez choisir **Galerie** ou **Objet blob de stockage**.

    - Si vous choisissez **Galerie**, sélectionnez l’une des images recommandées dans le menu déroulant :

      - Windows 10 Entreprise multisession, version 1909
      - Windows 10 Entreprise multisession, version 1909 + Microsoft 365 Apps
      - Windows Server 2019 Datacenter
      - Windows 10 Entreprise multisession, version 2004
      - Windows 10 Entreprise multisession, version 2004 + Microsoft 365 Apps

      Si vous ne voyez pas l’image souhaitée, sélectionnez **Voir toutes les images** afin de pouvoir sélectionner une autre image dans votre galerie ou une image fournie par Microsoft ou d’autres éditeurs. Vérifiez que l’image choisie fait partie des [images de système d’exploitation prises en charge](overview.md#supported-virtual-machine-os-images).

      > [!div class="mx-imgBorder"]
      > ![Capture d’écran du portail Azure avec une liste d’images Microsoft.](media/marketplace-images.png)

      Vous pouvez également accéder à **Mes éléments** et choisir une image personnalisée que vous avez déjà chargée.

      > [!div class="mx-imgBorder"]
      > ![Capture d’écran de l’onglet Mes éléments.](media/my-items.png)

    - Si vous choisissez **Objet blob de stockage**, vous pouvez utiliser votre propre build d’image via Hyper-V ou sur une machine virtuelle Azure. Il vous suffit d’entrer l’emplacement de l’image dans l’objet blob de stockage sous la forme d’un URI.
   
   L’emplacement de l’image est indépendant de l’option de disponibilité, mais la résilience de zone de l’image détermine si cette image peut être utilisée avec une zone de disponibilité. Si vous sélectionnez une zone de disponibilité lors de la création de votre image, veillez à utiliser une image de la galerie avec la résilience de zone activée. Pour en découvrir plus sur l’option de résilience de zone que vous devez utiliser, consultez le [Forum aux questions](./faq.yml#which-availability-option-is-best-for-me-).

6. Ensuite, choisissez la **taille de machine virtuelle** que vous souhaitez utiliser. Vous pouvez soit conserver la taille par défaut, soit sélectionner **Modifier la taille** pour changer de taille. Si vous sélectionnez **Modifier la taille**, dans la fenêtre qui s’affiche, choisissez la taille de la machine virtuelle adaptée à votre charge de travail. Pour en savoir plus sur les tailles de machine virtuelle et la taille que vous devez choisir, consultez les [Instructions de dimensionnement de machine virtuelle](/windows-server/remote/remote-desktop-services/virtual-machine-recs?context=/azure/virtual-desktop/context/context).

7. Sous **Nombre de machines virtuelles**, indiquez le nombre de machines virtuelles que vous souhaitez créer pour votre pool d’hôtes.

    >[!NOTE]
    >Le processus de création peut créer jusqu’à 400 machines virtuelles lors de la configuration de votre pool d’hôtes, et chaque processus de création de machine virtuelle crée quatre objets dans votre groupe de ressources. Étant donné que le processus de création ne vérifie pas le quota de votre abonnement, vérifiez que le nombre de machines virtuelles que vous entrez se trouve dans les limites d’API et de machines virtuelles Azure pour votre groupe de ressources et votre abonnement. Vous pourrez ajouter d’autres machines virtuelles une fois que vous autre terminé la création de votre pool d’hôtes.

8. Choisissez le type de disques de système d’exploitation que vous souhaitez que vos machines virtuelles utilisent : SSD Standard, SSD Premium ou HDD Standard.

9. Sous Réseau et sécurité, sélectionnez le **réseau virtuel** et le **sous-réseau** où vous souhaitez placer les machines virtuelles que vous créez. Vérifiez que le réseau virtuel peut se connecter au contrôleur de domaine, car vous devrez joindre les machines virtuelles qui se trouvent sur le réseau virtuel au domaine. Les serveurs DNS du réseau virtuel que vous avez sélectionné doivent être configurés pour utiliser l’adresse IP du contrôleur de domaine.

10. Sélectionnez le type de groupe de sécurité souhaité : **De base**, **Avancé** ou **Aucun**.

    Si vous sélectionnez **De base**, vous devez choisir si vous souhaitez que des ports entrants soient ouverts. Si vous sélectionnez **Oui**, choisissez dans la liste les ports standard sur lesquels autoriser les connexions entrantes.

    >[!NOTE]
    >Pour une sécurité accrue, nous vous recommandons de ne pas ouvrir de ports entrants publics.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la page du groupe de sécurité qui montre la liste des ports disponibles dans un menu déroulant.](media/available-ports.png)

    Si vous choisissez **Avancé**, sélectionnez un groupe de sécurité réseau existant que vous avez déjà configuré.

11. Ensuite, sélectionnez si vous voulez que les machines virtuelles soient jointes à **Active Directory** ou à **Azure Active Directory** (préversion).

    - Pour Active Directory, fournissez un compte pour joindre le domaine et choisissez si vous souhaitez joindre un domaine et une unité d’organisation spécifiques.

        - Pour le nom d'utilisateur principal de la jonction de domaine d’AD, entrez les informations d’identification de l’administrateur de domaine Active Directory du réseau virtuel que vous avez sélectionné. L’authentification multifacteur (MFA) ne peut pas être activée sur le compte que vous utilisez. Lors de la jointure à un domaine Azure Active Directory Domain Services (Azure AD DS), le compte doit faire partie du groupe d’administrateurs Azure AD DC et le mot de passe du compte doit fonctionner dans Azure AD DS.

        - Pour spécifier un domaine, sélectionnez **Oui**, puis saisissez le nom du domaine que vous souhaitez rejoindre. Si vous le souhaitez, vous pouvez également ajouter une unité d’organisation spécifique dans laquelle vous souhaitez placer les machines virtuelles en entrant le chemin d’accès complet (nom unique) et sans guillemets. Si vous ne souhaitez pas spécifier de domaine, sélectionnez **Non**. Les machines virtuelles sont automatiquement jointes au domaine qui correspond au suffixe de l'**UPN de jonction de domaine Active Directory**.
  
    - Pour Azure Active Directory, vous pouvez sélectionner **inscrire la machine virtuelle auprès d’Intune** pour mettre automatiquement la machine virtuelle à la disposition de la gestion après son déploiement.

12. Sous **compte d’administrateur de l’ordinateur virtuel**, entrez les informations d’identification du compte administrateur local à ajouter lors de la création de la machine virtuelle. Vous pouvez utiliser ce compte à des fins de gestion à la fois dans les machines virtuelles AD et Azure AD.

13. Sous **Configuration personnalisée post-mise à jour**, vous pouvez entrer l’emplacement d’un modèle Azure Resource Manager pour effectuer des configurations personnalisées sur vos hôtes de session une fois que vous les avez créées. Vous devez entrer les URL du fichier de modèle Azure Resource Manager et du fichier de paramètres du modèle Azure Resource Manager. 

      >[!NOTE]
      >Azure Virtual Desktop ne prend pas en charge le provisionnement de ressources Azure dans le modèle.

14. Sélectionnez **Suivant : Espace de travail >** .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pour créer une nouvelle machine virtuelle Azure, utilisez la commande [az vm create](/cli/azure/vm#az_vm_create) :

```azurecli
az vm create --name "MyVMName" \
    --resource-group "MyResourceGroup" \
    --image "MyImage" \
    --generate-ssh-keys
```

Pour plus d’informations sur l’utilisation d’Azure CLI pour créer des machines virtuelles Azure, consultez :
- Windows
    - [Créer une machine virtuelle Windows à l’aide de l’interface de ligne de commande Azure]( /azure/virtual-machines/windows/quick-create-cli)
    - [Tutoriel : créer et gérer des machines virtuelles Windows avec Azure CLI](/cli/azure/azure-cli-vm-tutorial)
- Linux
    - [Création d'une machine virtuelle Linux à l’aide de l'interface de ligne de commande Azure (CLI)](../virtual-machines/linux/quick-create-cli.md)
    - [Tutoriel : Créer et gérer des machines virtuelles Linux avec l’interface Azure CLI]( /azure/virtual-machines/linux/tutorial-manage-vm) 
---

Nous sommes maintenant prêts à commencer la phase suivante de la configuration de votre pool d’hôtes : l’inscription de votre groupe d’applications auprès d’un espace de travail.

## <a name="workspace-information"></a>Informations sur l’espace de travail

Le processus de création du pool d’hôtes crée un groupe d’applications de bureau par défaut. Pour que le pool d’hôtes fonctionne comme prévu, vous devez publier ce groupe d’applications sur des utilisateurs ou des groupes d’utilisateurs, et vous devez inscrire le groupe d’applications auprès d’un espace de travail.

>[!NOTE]
>Si vous êtes un développeur d’applications qui tente de publier les applications de votre organisation, vous pouvez attacher dynamiquement des applications MSIX à des sessions utilisateur ou ajouter vos packages d’application à une image de machine virtuelle personnalisée. Pour plus d’informations, consultez Comment faire pour servir votre application personnalisée avec Azure Virtual Desktop.

### <a name="portal"></a>[Portail](#tab/azure-portal)

Pour inscrire le groupe d’applications de bureau auprès d’un espace de travail

1. Sélectionnez **Oui**.

   Si vous sélectionnez **Non**, vous pourrez inscrire le groupe d’applications ultérieurement, mais nous vous recommandons d’effectuer l’inscription auprès de l’espace de travail dès que vous le pouvez afin que votre pool d’hôtes fonctionne correctement.

2. Ensuite, choisissez si vous souhaitez créer un espace de travail ou en sélectionner un parmi les espaces de travail existants. Vous ne pourrez inscrire le groupe d’applications qu’auprès d’un espace de travail créé au même emplacement que le pool d’hôtes.

3. Vous pouvez éventuellement sélectionner **Suivant : Étiquettes >** .

    Ici, vous pouvez ajouter des étiquettes pour pouvoir regrouper les objets avec des métadonnées afin de faciliter la tâche des administrateurs.

4. Quand vous avez terminé, sélectionnez **Vérifier + créer**.

     >[!NOTE]
     >Le processus de validation Vérifier + créer ne vérifie pas si votre mot de passe répond aux normes de sécurité ou si votre architecture est correcte. Vous devez donc vérifier ces éléments vous-même.

5. Passez en revue les informations relatives à votre déploiement pour vérifier que tout semble correct. Sélectionnez **Créer** lorsque vous avez terminé. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Utilisez la commande [az desktopvirtualization workspace create](/cli/azure/desktopvirtualization#az_desktopvirtualization_workspace_create) pour créer le nouvel espace de travail :

```azurecli
az desktopvirtualization workspace create --name "MyWorkspace" \
    --resource-group "MyResourceGroup" \
    --location "MyLocation" \
    --tags tag1="value1" tag2="value2" \
    --friendly-name "Friendly name of this workspace" \
    --description "Description of this workspace" 
```
---

Cela démarre le processus de déploiement, qui crée les objets suivants :

- Votre nouveau pool d’hôtes
- Un groupe d’applications de bureau
- Un espace de travail, si vous avez choisi d’en créer un
- Si vous avez choisi d’inscrire le groupe d’applications de bureau, l’inscription aboutit.
- Des machines virtuelles, si vous avez choisi d’en créer, qui sont jointes au domaine et inscrites auprès du nouveau pool d’hôtes
- Un lien de téléchargement pour un modèle Azure Resource Management basé sur votre configuration

Après cela, vous avez terminé !

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>Exécuter le modèle Azure Resource Manager pour provisionner le nouveau pool d’hôtes

Si vous préférez utiliser un processus automatisé, [téléchargez notre modèle Azure Resource Manager](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) pour provisionner votre nouveau pool d’hôtes.

>[!NOTE]
>Si vous utilisez un processus automatisé pour créer votre environnement, vous aurez besoin de la dernière version du fichier JSON de configuration. Le fichier JSON est disponible [ici](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list).

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez créé votre pool d’hôtes, vous pouvez le remplir avec des programmes RemoteApps. Pour en savoir plus sur la façon de gérer des applications dans Azure Virtual Desktop, consultez le tutoriel suivant :

> [!div class="nextstepaction"]
> [Gérer les groupes d’applications](./manage-app-groups.md)
