---
title: Activer un bureau à distance graphique pour Linux dans Azure Lab Services | Microsoft Docs
description: Découvrez comment activer le Bureau à distance pour les machines virtuelles Linux dans un lab dans Azure Lab Services.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: aaeec2cc74e24061c9c022fdc4526732030c1f97
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232676"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Activer un bureau à distance graphique pour des machines virtuelles Linux dans Azure Lab Services
Cet article explique comment effectuer les tâches suivantes :

- Activer des sessions de bureau à distance graphique pour une machine virtuelle Linux.
- Se connecter à une machine virtuelle Linux à l’aide du protocole RDP (Remote Desktop Protocol) ou de clients de bureau à distance X2Go.

## <a name="set-up-graphical-remote-desktop-solution"></a>Configurer une solution de bureau à distance graphique
Lors de la création d’un laboratoire à partir d’une image **Linux**, un accès **SSH** (Secure Shell) est automatiquement configuré pour permettre à l’enseignant de se connecter au modèle de machine virtuelle à partir de la ligne de commande à l’aide de SSH.  De même, lors de la publication du modèle de machine virtuelle, les étudiants peuvent également se connecter à leurs machines virtuelles à l’aide de SSH.

Pour vous connecter à une machine virtuelle Linux à l’aide d’une **GUI** (interface graphique utilisateur), nous vous recommandons d’utiliser le protocole **RDP** ou **X2Go**.  Le reste de cet article décrit les étapes à effectuer pour configurer RDP ou X2Go sur le modèle de machine virtuelle d’un lab.

> [!NOTE]
> Linux utilise une version open source de RDP appelée [Xrdp](https://en.wikipedia.org/wiki/Xrdp).  Par souci de simplicité, nous utilisons le terme RDP tout au long de cet article.

### <a name="rdp-setup"></a>Configuration du protocole RDP
Pour utiliser le protocole RDP, l’enseignant doit :
  - Activer une connexion Bureau à distance. Celle-ci est nécessaire pour ouvrir le port de la machine virtuelle pour le protocole RDP.
  - Installer le serveur de bureau à distance RDP.
  - Installer un environnement de bureau graphique Linux.

### <a name="x2go-setup"></a>Configuration de X2Go
Pour utiliser X2Go, l’enseignant doit :
- Installer le serveur de bureau à distance X2Go.
- Installer un environnement de bureau graphique Linux.

X2Go utilise le port déjà activé pour SSH.  Par conséquent, aucune configuration supplémentaire n’est requise pour ouvrir un port sur la machine virtuelle pour X2Go.

> [!NOTE]
> Dans certains cas, comme avec Ubuntu LTS 18.04, X2Go est plus performant.  Si vous utilisez RDP et constatez une latence lors de l’interaction avec l’environnement de bureau graphique, envisagez d’essayer X2Go, car cela peut améliorer les performances.

> [!IMPORTANT]
>  Des images de la Place de marché disposent déjà d’un environnement de bureau graphique et d’un serveur de bureau à distance installés.  Par exemple, la [Data Science Virtual Machine pour Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) possède déjà un [serveur XFCE et X2Go installé et configuré pour accepter les connexions clientes](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#x2go).

## <a name="enable-remote-desktop-connection-for-rdp"></a>Activer une connexion Bureau à distance pour RDP

Cette étape n’est nécessaire que pour se connecter à l’aide de RDP.  Si vous envisagez plutôt d’utiliser X2Go, vous pouvez passer à la section suivante, car X2Go utilise le port SSH.

1.  Pendant la création du laboratoire, l’enseignant a la possibilité d’**activer une connexion Bureau à distance**.  L’enseignant doit **activer** cette option pour ouvrir sur la machine virtuelle Linux le port nécessaire pour une session Bureau à distance RDP.  Autrement, si cette option est laissée **désactivée**, seul le port pour SSH est ouvert.
  
    ![Capture d’écran de la fenêtre « Nouveau labo » avec l’option « Activer la Connexion Bureau à distance »](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. Dans la zone de message **Activation de la connexion Bureau à distance**, sélectionnez **Continuer avec le Bureau à distance**. 

    ![Activer la connexion Bureau à distance pour une image Linux](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>Installer RDP ou X2Go

Une fois le laboratoire créé, l’enseignant doit s’assurer qu’un environnement de bureau graphique et un serveur Bureau à distance sont installés sur le modèle de machine virtuelle.  L’instructeur doit d’abord se connecter au modèle de machine virtuelle en utilisant SSH afin d’installer les packages pour :
- le serveur Bureau à distance RDP ou X2Go ;
- un environnement de bureau graphique comme [GNOME](https://www.gnome.org/), [MATE](https://mate-desktop.org/), [XFCE](https://www.xfce.org/), [Xubuntu](https://xubuntu.org/), etc.

Une fois cette configuration terminée, l’enseignant peut se connecter au modèle de machine virtuelle en utilisant le client **Bureau à distance Microsoft (RDP)** ou le client **X2Go**.

Pour configurer le modèle de machine virtuelle, suivez les étapes ci-dessous :

1. Si vous voyez l’option **Personnaliser le modèle** dans la barre d’outils, sélectionnez-la. Ensuite, sélectionnez **Continuer** dans la boîte de dialogue **Personnaliser le modèle**. Cette action démarre le modèle de machine virtuelle.  

    ![Personnaliser le modèle](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. Une fois le modèle de machine virtuelle démarré, vous pouvez sélectionner **Se connecter au modèle** puis **Se connecter via le protocole SSH** dans la barre d’outils. 

    ![Se connecter au modèle via RDP une fois le lab créé](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. La boîte de dialogue **Se connecter à votre machine virtuelle** suivante s’affiche. Sélectionnez le bouton **Copier** en regard de la zone de texte pour la copier dans le Presse-papiers. Enregistrez les informations de connexion SSH. Utilisez ces informations de connexion dans un terminal SSH (tel que [Putty](https://www.putty.org/)) pour vous connecter à la machine virtuelle.
 
    ![Chaîne de connexion SSH](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

1. Enfin, installez RDP ou X2Go ainsi que l’environnement de bureau graphique de votre choix.

  Pour des performances optimales, nous vous recommandons généralement d’utiliser le bureau graphique XFCE et conseillons aux utilisateurs de se connecter au bureau avec X2Go.  Pour configurer XFCE avec X2Go sur Ubuntu, utilisez les instructions suivantes :
    - [Installer et configurer le protocole X2Go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/LinuxGraphicalDesktopSetup/XFCE_Xubuntu/ReadMe.md)

  Dans les cas où vous exigez que les utilisateurs se connectent au bureau graphique avec RDP, utilisez les instructions suivantes pour Ubuntu :
    - [Installer et configurer le protocole RDP](../virtual-machines/linux/use-remote-desktop.md)

### <a name="using-gnome-or-mate-graphical-desktops"></a>Utilisation d’environnements de bureau graphique GNOME ou MATE

Pour les environnements de bureau graphique GNOME et MATE, un conflit réseau peut se produire avec l’agent Linux Azure nécessaire au bon fonctionnement des machines virtuelles dans Azure Lab Services.  Ce conflit réseau provoque les effets secondaires suivants quand Ubuntu 18.04 LTS est utilisé dans un environnement avec GNOME ou MATE installé :
 - La création d’un labo à partir de l’image échoue, avec le message d’erreur **Impossible d’établir la communication avec l’agent de machine virtuelle. Vérifiez que l’agent de machine virtuelle est activé et qu’il fonctionne.**  
 - La publication des machines virtuelles des étudiants ne répond plus si les paramètres d’arrêt automatique sont activés.
 - La réinitialisation du mot de passe des machines virtuelles des étudiants cesse de répondre.

Nous vous recommandons d’utiliser les instructions suivantes pour configurer les bureaux graphiques GNOME ou MATE sur Ubuntu.  Ces instructions incluent un correctif pour le conflit réseau qui existe avec Ubuntu 18.04 LTS.  L’installation de GNOME et de MATE sur Ubuntu 20.04 LTS et 21.04 LTS est également prise en charge :
 - [Installer et configurer GNOME/RDP et MATE/X2go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/LinuxGraphicalDesktopSetup/GNOME_MATE/ReadMe.md)

## <a name="connect-to-the-template-vm-via-the-gui"></a>Se connecter au modèle de machine virtuelle via l’interface graphique utilisateur (GUI)

Une fois le modèle de machine virtuelle configuré, l’enseignant peut se connecter via l’interface utilisateur graphique à l’aide du client **Bureau à distance Microsoft (RDP)** ou du client **X2Go**.  Le client que vous utilisez varie selon que le protocole RDP ou X2Go est configuré en tant que serveur Bureau à distance sur le modèle de machine virtuelle.  

### <a name="microsoft-remote-desktop-rdp-client"></a>Client Bureau à distance Microsoft (RDP)

Le client Bureau à distance Microsoft (RDP) est utilisé pour se connecter à un modèle de machine virtuelle sur lequel le protocole RDP est configuré.  Vous pouvez utiliser le client Bureau à distance sur Windows, Chromebooks, Mac et bien plus encore.  Pour plus d’informations, consultez l’article [Clients Bureau à distance](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Suivez les étapes ci-dessous en fonction du type d’ordinateur utilisé pour la connexion au modèle de machine virtuelle :

- Windows
  1. Cliquez sur **Se connecter au modèle** dans la barre d’outils de votre laboratoire et sélectionnez **Se connecter via RDP** pour vous connecter au modèle de machine virtuelle. 
  1. Enregistrez le fichier RDP et utilisez-le pour vous connecter au modèle de machine virtuelle à l’aide du client Bureau à distance. 
  1. En règle générale, le client Bureau à distance est déjà installé et configuré sur Windows.  Par conséquent, il vous suffit de cliquer sur le fichier RDP pour l’ouvrir et démarrer la session à distance.

- Mac
  1. Cliquez sur **Se connecter au modèle** dans la barre d’outils de votre laboratoire, puis sélectionnez **Se connecter via RDP** pour enregistrer le fichier RDP.  
  1. Ensuite, consultez l’article de procédure [Se connecter à une machine virtuelle avec RDP sur un Mac](connect-virtual-machine-mac-remote-desktop.md).

- Chromebook
  1. Cliquez sur **Se connecter au modèle** dans la barre d’outils de votre laboratoire, puis sélectionnez **Se connecter via RDP** pour enregistrer le fichier RDP.  
  1. Ensuite, consultez l’article de procédure [Se connecter à une machine virtuelle avec RDP sur un Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).

### <a name="x2go-client"></a>Client X2Go

Le client X2Go est utilisé pour se connecter à un modèle de machine virtuelle sur lequel X2Go est configuré.  À l’aide des informations de connexion SSH du modèle de machine virtuelle, suivez les étapes décrites dans l’article de procédure [Se connecter à une machine virtuelle avec X2Go](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go).

## <a name="next-steps"></a>Étapes suivantes
Quand un enseignant configure le protocole RDP ou X2Go sur son modèle de machine virtuelle, puis publie, les étudiants peuvent se connecter à leurs machines virtuelles via le Bureau à distance de l’interface graphique utilisateur ou SSH.

Pour plus d'informations, consultez les pages suivantes :
 - [Se connecter à une machine virtuelle Linux](how-to-use-remote-desktop-linux-student.md)
