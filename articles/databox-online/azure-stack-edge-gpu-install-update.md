---
title: Installer une mise à jour sur un appareil Azure Stack Edge Pro avec GPU | Microsoft Docs
description: Décrit comment utiliser le portail Azure et l’interface utilisateur web locale pour appliquer des mises à jour sur un appareil Azure Stack Edge Pro avec GPU et le cluster Kubernetes associé sur l’appareil
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/26/2021
ms.author: alkohli
ms.openlocfilehash: 13fc1ad892509d04750e51b593cc7aaf07f0c91a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082927"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>Mettre à jour votre GPU Azure Stack Edge Pro 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article décrit les étapes à effectuer pour installer des mises à jour sur votre appareil Azure Stack Edge Pro avec GPU en utilisant le portail Azure et l’interface utilisateur web locale. Vous appliquez les mises à jour logicielles ou les correctifs logiciels pour maintenir à jour votre appareil Azure Stack Edge Pro et le cluster Kubernetes associé sur l’appareil.

La procédure décrite dans cet article a été effectuée à l’aide d’une version différente du logiciel, mais le processus reste le même pour la version actuelle du logiciel.

## <a name="about-latest-update"></a>À propos de la dernière mise à jour

La mise à jour actuelle est la mise à jour 2110. Cette mise à jour installe deux types de mise à jour, la mise à jour de l’appareil suivie des mises à jour de Kubernetes. Les versions associées à cette mise à jour sont les suivantes :

- Version du logiciel de l’appareil - **2.2.1758.4034**
- Version du serveur Kubernetes - **v1.20.9**
- Version d’IoT Edge : **0.1.0-beta15**
- Version du pilote GPU : **460.32.03**
- Version CUDA : **11.2**

Pour plus d’informations sur les nouveautés de cette mise à jour, consultez [Notes de publication](azure-stack-edge-gpu-2110-release-notes.md).

**Pour permettre l’application de la mise à jour 2110, votre appareil doit exécuter la version 2106.** 

- Si vous n’exécutez pas la version minimale prise en charge, vous verrez cette erreur : *Impossible d’installer le package de mise à jour, car ses dépendances ne sont pas satisfaites*. 
- Vous pouvez effectuer la mise à jour vers la version 2106 à partir d’une ancienne version, puis installer la version 2110.

N’oubliez pas que l’installation d’une mise à jour ou d’un correctif logiciel nécessite le redémarrage de votre appareil. Comme l'appareil Azure Stack Edge Pro est un appareil à nœud unique, les E/S en cours sont interrompues et votre appareil subit un temps d'arrêt qui peut durer jusqu'à 1,5 heure pour la mise à jour.

Pour installer les mises à jour sur votre appareil, vous devez suivre les étapes ci-dessous :

1. Configurez l’emplacement du serveur de mise à jour. 
1. Appliquez les mises à jour via l’IU du portail Azure ou l’IU web locale.

Chacune de ces étapes est décrite dans les sections suivantes.

## <a name="configure-update-server"></a>Configurer le serveur de mise à jour

1. Dans l’interface utilisateur web locale, accédez à **Configuration** > **Serveur de mise à jour**.
   
    ![Configurer les mises à jour 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. Dans **Sélectionner le type de serveur de mise à jour**, dans la liste déroulante, choisissez entre Serveur Microsoft Update (par défaut) et Windows Server Update Services.  
   
    Si vous mettez à jour à partir de Windows Server Update Services, spécifiez l’URI du serveur. Le serveur de cet URI déploiera les mises à jour sur tous les appareils connectés à ce serveur.

    ![Configurer les mises à jour 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    Le serveur WSUS est utilisé pour gérer et distribuer les mises à jour par le biais d’une console de gestion. Un serveur WSUS peut également être la source de mises à jour d’autres serveurs WSUS de l’organisation. Le serveur WSUS qui agit en tant que source des mises à jour est appelé serveur en amont. Dans une implémentation WSUS, au moins un serveur WSUS de votre réseau doit se connecter à Microsoft Update pour obtenir les informations sur les mises à jour disponibles. En tant qu’administrateur, vous pouvez déterminer le nombre d’autres serveurs WSUS se connectant directement à Microsoft Update en fonction de la configuration et de la sécurité du réseau.
    
    Pour plus d’informations, consultez [Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

## <a name="use-the-azure-portal"></a>Utilisation du portail Azure

Nous vous recommandons d’installer les mises à jour par le biais du portail Azure. L’appareil recherche automatiquement les mises à jour une fois par jour. Une fois les mises à jour disponibles, vous verrez une notification dans le portail. Vous pouvez alors télécharger et installer les mises à jour.

> [!NOTE]
> Assurez-vous que l’appareil est sain et que l’état indique que **Votre appareil fonctionne correctement !** avant de procéder à l’installation des mises à jour.

Selon la version du logiciel que vous exécutez, le processus d’installation peut varier légèrement. 

- Si vous effectuez une mise à jour de la version 2106 vers la version 2110, vous avez une installation en un seul clic. Pour obtenir des instructions, consultez l’onglet **version 2106**.
- Si vous effectuez une mise à jour vers des versions antérieures à la version 2110, vous avez une installation en deux clics. Pour obtenir des instructions, consultez l’onglet **version 2105 et versions antérieures**.

### <a name="version-2106"></a>[version 2106](#tab/version-2106)

[!INCLUDE [azure-stack-edge-install-2110-updates](../../includes/azure-stack-edge-install-2110-updates.md)]

### <a name="version-2105-and-earlier"></a>[version 2105 et versions antérieures](#tab/version-2105-and-earlier)

1. Quand les mises à jour sont disponibles pour votre appareil, une notification s’affiche dans la page **Vue d’ensemble** de votre ressource Azure Stack Edge. Vous pouvez sélectionner la notification ou cliquer sur **Mettre à jour l’appareil** dans la barre de commandes supérieure. Cela vous permettra d’appliquer les mises à jour logicielles de l’appareil.

    ![Version logicielle après la mise à jour](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. Dans le panneau **Mises à jour de l’appareil**, consultez les termes du contrat de licence associés aux nouvelles fonctionnalités dans les notes de publication.

    Vous pouvez choisir de **Télécharger et installer** les mises à jour ou simplement de les **Télécharger**. Vous pourrez ensuite installer ces mises à jour ultérieurement.

    ![Version logicielle après la mise à jour 2](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    Si vous souhaitez télécharger et installer les mises à jour, sélectionnez l’option qui met à jour l’installation automatiquement une fois le téléchargement terminé.

    ![Version logicielle après la mise à jour 3](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. Le téléchargement des mises à jour démarre. Un message de notification indique que le téléchargement est en cours.

    ![Version logicielle après la mise à jour 4](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    Une bannière de notification s’affiche également dans le portail Azure. Celle-ci indique la progression du téléchargement.

    ![Version logicielle après la mise à jour 5](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    Vous pouvez sélectionner cette notification ou sélectionner **Mettre à jour l’appareil** pour afficher le détail de la mise à jour.

    ![Version logicielle après la mise à jour 6](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. Une fois le téléchargement terminé, la bannière de notification est mise à jour pour indiquer la fin de l’opération. Si vous avez choisi de télécharger et d’installer les mises à jour, l’installation démarre automatiquement.

    Si vous avez choisi de télécharger uniquement les mises à jour, sélectionnez la notification pour ouvrir le panneau **Mises à jour de l’appareil**. Sélectionnez **Installer**.
  
5. Un message de notification indique que l’installation est en cours. Le portail affiche également une alerte d’information pour indiquer que l’installation est en cours. L’appareil est mis hors connexion et est en mode maintenance.
   
    ![Version logicielle après la mise à jour 10](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. Comme il s'agit d'un appareil à nœud unique, il redémarre après l'installation des mises à jour. L'alerte critique qui s'affiche pendant le redémarrage indique que la pulsation de l'appareil a été perdue.

    ![Version logicielle après la mise à jour 11](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    Sélectionnez l’alerte pour afficher l’événement d’appareil correspondant.
    
    ![Version logicielle après la mise à jour 12](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)

7. Après le redémarrage, le logiciel de l’appareil terminera la mise à jour. Une fois la mise à jour terminée, vous pouvez vérifier à partir de l’interface utilisateur web locale que le logiciel de l’appareil est mis à jour. La version du logiciel Kubernetes n’a pas été mise à jour.

    ![Version logicielle après la mise à jour 13](./media/azure-stack-edge-gpu-install-update/portal-update-12.png)

8. Une bannière de notification s’affiche et indique que des mises à jour de l’appareil sont disponibles. Sélectionnez cette bannière pour démarrer la mise à jour du logiciel Kubernetes sur votre appareil. 

    ![Version logicielle après la mise à jour 13a](./media/azure-stack-edge-gpu-install-update/portal-update-13.png) 


    ![Version logicielle après la mise à jour 14](./media/azure-stack-edge-gpu-install-update/portal-update-14-a.png) 

    Si vous sélectionnez **Mettre à jour l’appareil** à partir de la barre de commandes supérieure, vous pouvez voir la progression des mises à jour.  

    ![Version logicielle après la mise à jour 15](./media/azure-stack-edge-gpu-install-update/portal-update-14-b.png) 


8. L’état de l’appareil se met sur **Votre appareil fonctionne correctement** après l’installation des mises à jour. 

    ![Version logicielle après la mise à jour 16](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

    Accédez à l’interface utilisateur web locale, puis accédez à la page **Mise à jour logicielle**. Vérifiez que la mise à jour de Kubernetes a bien été installée et que la version du logiciel de l’appareil reflète la mise à jour.

    ![Version logicielle après la mise à jour 17](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)


Une fois le logiciel de l'appareil et les mises à jour de Kubernetes correctement installés, la notification de bannière disparaît. 

---

Vous disposez désormais de la dernière version du logiciel d’appareil et de Kubernetes.


## <a name="use-the-local-web-ui"></a>Utilisation de l’interface utilisateur web locale

La procédure de mise à jour à l’aide de l’interface utilisateur web locale se déroule en deux étapes :

* Téléchargement de la mise à jour ou du correctif
* Installation de la mise à jour ou du correctif

Chacune de ces étapes est décrite en détail dans les sections suivantes.


### <a name="download-the-update-or-the-hotfix"></a>Téléchargement de la mise à jour ou du correctif

Effectuez les étapes suivantes pour télécharger la mise à jour logicielle. Vous pouvez télécharger la mise à jour à partir de l’emplacement fourni par Microsoft ou à partir du catalogue Microsoft Update.


Effectuez les étapes suivantes pour télécharger la mise à jour à partir du catalogue Microsoft Update.

1. Lancez le navigateur et accédez à [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com).

    ![Rechercher dans le catalogue](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. Dans la zone de recherche du catalogue Microsoft Update, entrez le numéro KB (Base de connaissances) du correctif ou des mots clés relatifs à la mise à jour que vous souhaitez télécharger. Par exemple, entrez **Azure Stack Edge**, puis cliquez sur **Rechercher**.
   
    La liste des mises à jour se présente sous le nom **Azure Stack Edge Update 2110**.
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. Sélectionnez **Télécharger**. Il y a deux packages à télécharger : <!--KB 4616970 and KB 4616971--> un pour les mises à jour logicielles de l’appareil (*SoftwareUpdatePackage.exe*) et un autre pour les mises à jour Kubernetes (*Kubernetes_Package.exe*), respectivement. Téléchargez les packages dans un dossier du système local. Vous pouvez également copier le dossier sur un partage réseau accessible à partir de l’appareil.

### <a name="install-the-update-or-the-hotfix"></a>Installation de la mise à jour ou du correctif

Avant d’installer la mise à jour ou le correctif logiciel, vérifiez les points suivants :

 - La mise à jour ou le correctif logiciel est téléchargé localement sur votre hôte ou accessible via un partage réseau.
 - L’état de votre appareil est sain, comme l’indique la page **Vue d’ensemble** de l’interface utilisateur web locale.

   ![mettre à jour l'appareil](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

Cette procédure prend environ 20 minutes. Effectuez les opérations suivantes pour installer la mise à jour ou le correctif logiciel.

1. Dans l’interface utilisateur web locale, accédez à **Maintenance** > **Mise à jour logicielle**. Prenez note de la version du logiciel que vous exécutez. 
   
   ![mettre à jour l’appareil 2](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Indiquez le chemin du fichier de mise à jour. Vous pouvez également accéder au fichier d'installation de la mise à jour si celui-ci a été placé sur un partage réseau. Sélectionnez le fichier de mise à jour logicielle dont le nom se termine par *SoftwareUpdatePackage.exe*.

   ![mettre à jour l’appareil 3](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. Sélectionnez **Appliquer la mise à jour**.

   <!--![update device 4](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)-->

4. Quand vous êtes invité à confirmer l’opération, sélectionnez **Oui** pour continuer. Puisqu’il s’agit d’un appareil à nœud unique, l’application de la mise à jour entraîne le redémarrage de l’appareil et provoque un temps d’arrêt. 
   
   ![mettre à jour l’appareil 5](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. La mise à jour démarre. Une fois l’appareil correctement mis à jour, il est redémarré. L’interface utilisateur locale n’est pas accessible pendant cet intervalle.
   
6. Une fois le redémarrage effectué, vous êtes redirigé vers la page **Se connecter** . Pour vérifier que le logiciel de l’appareil a été mis à jour, accédez à **Maintenance** > **Mise à jour logicielle** dans l’interface utilisateur web locale. Pour la mise en production actuelle, la version logicielle affichée doit être **Azure Stack Edge 2110**. 


7. Vous allez maintenant mettre à jour la version logicielle de Kubernetes. Répétez les étapes ci-dessus. Fournissez un chemin d’accès au fichier de mise à jour Kubernetes avec le suffixe *Kubernetes_Package.exe*.  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. Sélectionnez **Appliquer la mise à jour**.

   <!--![update device 7](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)-->

9. Quand vous êtes invité à confirmer l’opération, sélectionnez **Oui** pour continuer.

10. Une fois la mise à jour Kubernetes installée, aucune modification n’est apportée au logiciel affiché dans **Maintenance** > **Mise à jour logicielle**.

    ![mettre à jour l’appareil 6](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’[administration des appareils Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).
