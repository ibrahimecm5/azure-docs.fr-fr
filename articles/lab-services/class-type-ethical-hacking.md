---
title: Configurer un labo de piratage éthique avec Azure Lab Services | Microsoft Docs
description: Découvrez comment configurer un labo à l’aide d’Azure Lab Services pour enseigner le piratage éthique.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 649646e560906c499cacda13c7745be3f2f3f758
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176906"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Configurer un labo pour enseigner le piratage éthique

Cet article explique comment organiser un cours autour de l’analyse forensique du piratage éthique. Des tests d’intrusion, une pratique utilisée par la communauté de piratage éthique, sont effectués quand quelqu’un tente d’accéder au système ou au réseau pour détecter les vulnérabilités qu’un attaquant malveillant pourrait exploiter.

Dans une classe sur le piratage éthique, les étudiants apprennent les techniques modernes de défense face aux vulnérabilités. Chaque étudiant a accès à une machine virtuelle hôte Windows Server qui comporte deux machines virtuelles imbriquées : une avec une image [Metasploitable3](https://github.com/rapid7/metasploitable3) et une autre avec une image [Kali Linux](https://www.kali.org/). La machine virtuelle Metasploitable est utilisée pour les besoins de fonctionnement, et la machine virtuelle Kali sert à accéder aux outils nécessaires pour exécuter les tâches forensiques.

Cet article comprend deux sections principales. La première section explique comment créer le laboratoire. La deuxième section explique comment créer la machine modèle à l’aide de la virtualisation imbriquée, ainsi que des outils et des images nécessaires. Dans ce cas, il s’agit d’une image Metasploitable et d’une image Linux Kali présentes sur une machine où Hyper-V est activé pour héberger les images.

## <a name="lab-configuration"></a>Configuration du laboratoire

Pour configurer ce labo, vous avez besoin d’un abonnement Azure pour commencer. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer. Une fois que vous disposez d’un abonnement Azure, vous pouvez créer un compte lab dans Azure Lab Services ou utiliser un compte existant. Pour créer un compte lab, consultez le tutoriel suivant : [Tutoriel pour configurer un compte lab](tutorial-setup-lab-account.md).

Suivez [ce tutoriel](tutorial-setup-classroom-lab.md) pour créer un labo et appliquer les paramètres suivants :

| Taille de la machine virtuelle | Image |
| -------------------- | ----- |
| Moyenne (virtualisation imbriquée) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Machine modèle

Une fois la machine modèle créée, démarrez-la et connectez-vous à celle-ci pour effectuer les trois tâches principales suivantes.

1. Configurez la machine pour la virtualisation imbriquée. Cela active toutes les fonctionnalités Windows nécessaires, comme Hyper-V, et configure le réseau pour que les images Hyper-V puissent communiquer entre elles et avec Internet.
2. Configurez l’image Linux [Kali](https://www.kali.org/). Kali est une distribution Linux qui comprend des outils pour le test d’intrusion et l’audit de sécurité.
3. Configurez l’image Metasploitable. Dans cet exemple, l’image [Metasploitable3](https://github.com/rapid7/metasploitable3) est utilisée. Cette image a été créée à dessein avec des failles de sécurité.

Le reste de cet article aborde les étapes manuelles permettant d’effectuer les tâches ci-dessus.  Vous pouvez également exécuter les [scripts Hyper-V Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) et les [scripts Lab Services Ethical Hacking](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking).

### <a name="prepare-template-machine-for-nested-virtualization"></a>Préparer la machine modèle pour la virtualisation imbriquée

Suivez les instructions permettant d’[activer la virtualisation imbriquée](how-to-enable-nested-virtualization-template-vm.md) afin de préparer votre machine virtuelle modèle pour la virtualisation imbriquée.

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Configurer une machine virtuelle imbriquée avec une image Linux Kali

Kali est une distribution Linux qui comprend des outils pour le test d’intrusion et l’audit de sécurité.

1. Téléchargez l’image à partir de [la page de téléchargement d’images de machine virtuelle Linux Kali sur le site Offensive Security](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/).  Notez le nom d’utilisateur par défaut et le mot de passe indiqués dans la page de téléchargement.
    1. Téléchargez l’image **Kali Linux VMware 64 bits (7z)** pour VMware.
    1. Extrayez le fichier .7z.  Si vous ne disposez pas de 7-Zip, téléchargez-le ici : [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html). N’oubliez pas où se trouve le dossier extrait, car vous en aurez besoin plus tard.
1. Convertissez le fichier vmdk extrait en fichier vhdx afin de pouvoir utiliser ce dernier avec Hyper-V. Plusieurs outils permettent de convertir les images VMware en images Hyper-V.  Nous utiliserons le [convertisseur StarWind V2V](https://www.starwindsoftware.com/starwind-v2v-converter).  Pour le télécharger, consultez la [page de téléchargement du convertisseur StarWind V2V](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Démarrez le **convertisseur StarWind V2V**.
    1. Dans **Select location of image to convert** (Sélectionner l’emplacement de l’image à convertir), choisissez **Local file** (Fichier local).  Sélectionnez **Suivant**.
    1. Dans la page **Image source**, accédez au fichier Kali Linux vmdk extrait à l’étape précédente pour le paramètre **Nom de fichier** et sélectionnez-le.  Le fichier est au format Kali-Linux-{version}-vmware-amd64.vmdk.  Sélectionnez **Suivant**.
    1. Dans **Select location of destination image** (Sélectionner l’emplacement de l’image de destination), choisissez **Local file** (Fichier local).  Sélectionnez **Suivant**.
    1. Dans la page **Select destination image format** (Sélectionner le format d’image de destination), choisissez **VHD/VHDX**.  Sélectionnez **Suivant**.
    1. Dans la page **Select option for VHD/VHDX image format** (Sélectionner l’option du format d’imageVHD/VHDX), choisissez **VHDX growable image** (Image VHDX ajustable).  Sélectionnez **Suivant**.
    1. Dans la page **Select destination file name** (Sélectionner le nom du fichier de destination), acceptez le nom de fichier par défaut.  Sélectionnez **Convertir**.
    1. Dans la page **Conversion en cours** , attendez que l’image soit convertie.  Cette opération peut prendre plusieurs minutes.  Sélectionnez **Terminer** lorsque la conversion a abouti.
1. Créez une machine virtuelle Hyper-V.
    1. Ouvrez le **Gestionnaire Hyper-V**.
    1. Choisissez **Action** -> **Nouveau** -> **Machine virtuelle**.
    1. Dans la page **Avant de commencer** de l’**Assistant Nouvelle machine virtuelle**, sélectionnez **Suivant**.
    1. Dans la page **Spécifier le nom et l’emplacement**, entrez **Kali-Linux** pour le **nom**, puis sélectionnez **Suivant**.
    1. Dans la page **Spécifier la génération**, acceptez les valeurs par défaut, puis sélectionnez **Suivant**.
    1. Dans la page **Affecter la mémoire**, entrez **2048 Mo** pour la **mémoire de démarrage**, puis sélectionnez **Suivant**.
    1. Dans la page **Configurer le réseau**, conservez la valeur **Non connecté** pour la connexion. Vous configurerez la carte réseau plus tard.
    1. Dans la page **Connecter un disque dur virtuel**, sélectionnez **Utiliser un disque dur virtuel existant**. Accédez à l’emplacement du fichier **Kali-Linux-{version}-vmware-amd64.vmdk** que vous avez créé à l’étape précédente, puis sélectionnez **Suivant**.
    1. Dans la page **Fin de l’Assistant Nouvelle machine virtuelle**, sélectionnez **Terminer**.
    1. Une fois la machine virtuelle créée, sélectionnez-la dans le Gestionnaire Hyper-V. N’activez pas encore la machine.  
    1. Choisissez **Action** -> **Paramètres**.
    1. Dans la boîte de dialogue **Paramètres pour Kali-Linux**, sélectionnez **Ajouter un matériel**.
    1. Sélectionnez **Carte réseau héritée**, puis **Ajouter**.
    1. Dans la page **Carte réseau héritée**, sélectionnez **LabServicesSwitch** pour le paramètre **Commutateur virtuel**, puis sélectionnez **OK**. LabServicesSwitch a été créé lors de la préparation de la machine modèle pour Hyper-V, dans la section **Préparer la machine modèle pour la virtualisation imbriquée**.
    1. L’image Kali-Linux est maintenant prête à être utilisée. Dans le **Gestionnaire Hyper-V**, choisissez **Action** -> **Démarrer**, puis choisissez **Action** -> **Se connecter** pour vous connecter à la machine virtuelle.  Le nom d’utilisateur par défaut est **kali** et le mot de passe **kali**.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Configurer une machine virtuelle imbriquée avec une image Metasploit  

L’image Rapid7 Metasploit est une image intentionnellement configurée avec des failles de sécurité. Vous utiliserez cette image pour tester et détecter les problèmes. Les instructions suivantes expliquent comment utiliser une image Metasploit précréée. Toutefois, si une version plus récente de l’image Metasploit est nécessaire, consultez [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3).

1. Téléchargez l’image Metasploitable.
    1. Accédez à [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html). Remplissez le formulaire pour télécharger l’image, puis sélectionnez le bouton **Submit** (Envoyer).
    2. Sélectionnez le bouton **Download Metasploitable Now** (Télécharger Metasploitable).
    3. Une fois le fichier zip téléchargé, extrayez-le et notez l’emplacement du fichier Metasploitable.vmdk.
1. Convertissez le fichier vmdk extrait en fichier vhdx afin de pouvoir utiliser ce dernier avec Hyper-V. Plusieurs outils permettent de convertir les images VMware en images Hyper-V.  Nous réutiliserons le [convertisseur StarWind V2V](https://www.starwindsoftware.com/starwind-v2v-converter).  Pour le télécharger, consultez la [page de téléchargement du convertisseur StarWind V2V](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Démarrez le **convertisseur StarWind V2V**.
    1. Dans **Select location of image to convert** (Sélectionner l’emplacement de l’image à convertir), choisissez **Local file** (Fichier local).  Sélectionnez **Suivant**.
    1. Sur la page **Image source** , accédez au fichier Metasploitable.vmdk extrait à l’étape précédente pour le paramètre **Nom de fichier** et sélectionnez-le.  Sélectionnez **Suivant**.
    1. Dans **Select location of destination image** (Sélectionner l’emplacement de l’image de destination), choisissez **Local file** (Fichier local).  Sélectionnez **Suivant**.
    1. Dans la page **Select destination image format** (Sélectionner le format d’image de destination), choisissez **VHD/VHDX**.  Sélectionnez **Suivant**.
    1. Dans la page **Select option for VHD/VHDX image format** (Sélectionner l’option du format d’imageVHD/VHDX), choisissez **VHDX growable image** (Image VHDX ajustable).  Sélectionnez **Suivant**.
    1. Dans la page **Select destination file name** (Sélectionner le nom du fichier de destination), acceptez le nom de fichier par défaut.  Sélectionnez **Convertir**.
    1. Dans la page **Conversion en cours** , attendez que l’image soit convertie.  Cette opération peut prendre plusieurs minutes.  Sélectionnez **Terminer** lorsque la conversion a abouti.
1. Créez une machine virtuelle Hyper-V.
    1. Ouvrez le **Gestionnaire Hyper-V**.
    1. Choisissez **Action** -> **Nouveau** -> **Machine virtuelle**.
    1. Dans la page **Avant de commencer** de l’**Assistant Nouvelle machine virtuelle**, sélectionnez **Suivant**.
    1. Dans la page **Spécifier le nom et l’emplacement**, entrez **Metasploitable** pour le **nom**, puis sélectionnez **Suivant**.

        ![Assistant Nouvelle image de machine virtuelle](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Dans la page **Spécifier la génération**, acceptez les valeurs par défaut, puis sélectionnez **Suivant**.
    1. Dans la page **Affecter la mémoire**, entrez **512 Mo** pour la **mémoire de démarrage**, puis sélectionnez **Suivant**.

        ![Page Affecter la mémoire](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. Dans la page **Configurer le réseau**, conservez la valeur **Non connecté** pour la connexion. Vous configurerez la carte réseau plus tard.
    1. Dans la page **Connecter un disque dur virtuel**, sélectionnez **Utiliser un disque dur virtuel existant**. Accédez à l’emplacement du fichier **metasploitable.vhdx** que vous avez créé dans l’étape précédente, puis sélectionnez **Suivant**.

        ![Page Connecter un disque dur virtuel](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Dans la page **Fin de l’Assistant Nouvelle machine virtuelle**, sélectionnez **Terminer**.
    1. Une fois la machine virtuelle créée, sélectionnez-la dans le Gestionnaire Hyper-V. N’activez pas encore la machine.  
    1. Choisissez **Action** -> **Paramètres**.
    1. Dans la boîte de dialogue **Paramètres pour Metasploitable**, sélectionnez **Ajouter un matériel**.
    1. Sélectionnez **Carte réseau héritée**, puis **Ajouter**.

        ![Page Carte réseau](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. Dans la page **Carte réseau héritée**, sélectionnez **LabServicesSwitch** pour le paramètre **Commutateur virtuel**, puis sélectionnez **OK**. LabServicesSwitch a été créé lors de la préparation de la machine modèle pour Hyper-V, dans la section **Préparer la machine modèle pour la virtualisation imbriquée**.

        ![Page Carte réseau héritée](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. L’image Metasploitable est maintenant prête à être utilisée. Dans le **Gestionnaire Hyper-V**, choisissez **Action** -> **Démarrer**, puis choisissez **Action** -> **Se connecter** pour vous connecter à la machine virtuelle.  Le nom d’utilisateur par défaut est **msfadmin** et le mot de passe est **msfadmin**.

Le modèle est maintenant mis à jour et contient les images nécessaires au cours sur le test d’intrusion dans le cadre d’un piratage éthique. Il comprend également une image avec les outils nécessaires aux tests d’intrusion et une autre image avec les failles de sécurité à découvrir. L’image du modèle peut à présent être publiée pour la classe. Sélectionnez le bouton **Publier** dans la page du modèle pour publier le modèle dans le labo.

## <a name="cost"></a>Coût  

Si vous souhaitez estimer le coût de ce labo, vous pouvez utiliser l’exemple suivant :

Pour une classe de 25 étudiants avec 20 heures de cours planifiées et un quota de 10 heures pour le travail à la maison ou les devoirs, le prix du labo est le suivant :

25 étudiants \* (20 + 10) heures \* 55 unités Lab \* 0,01 USD par heure = 412,50 USD

>[!IMPORTANT]
>L’estimation du coût est fournie à titre d’exemple uniquement. Pour en savoir plus sur les tarifs actuels, consultez [Tarification Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusion

Cet article vous a présenté les étapes de création d’un labo pour un cours sur le piratage éthique. Il comprend les étapes de configuration de la virtualisation imbriquée qui sont nécessaires pour créer deux machines virtuelles dans une machine virtuelle hôte en vue d’exécuter les tests d’intrusion.

## <a name="next-steps"></a>Étapes suivantes

Les étapes suivantes sont communes à la configuration de n’importe quel labo :

- [Ajout d'utilisateurs](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Définir un quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Définir un calendrier](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Envoyer par mail des liens d’inscription aux étudiants](how-to-configure-student-usage.md#send-invitations-to-users)
