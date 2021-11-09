---
author: alkohli
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 09/27/2021
ms.openlocfilehash: 24cc360da36e49726cc23b407ada5a3d88c3a2a1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082967"
---
1. Quand les mises à jour sont disponibles pour votre appareil, une notification s’affiche dans la page **Vue d’ensemble** de votre ressource Azure Stack Edge. Vous pouvez sélectionner la notification ou cliquer sur **Mettre à jour l’appareil** dans la barre de commandes supérieure. Cela vous permettra d’appliquer les mises à jour logicielles de l’appareil.

    ![Sélectionner Mettre à jour l’appareil](media/azure-stack-edge-install-2110-updates/install-updates-portal-1.png)

2. Dans le panneau **Mises à jour de l’appareil**, consultez les termes du contrat de licence associés aux nouvelles fonctionnalités dans les notes de publication.

    Une fois les mises à jour téléchargées sur l’appareil, vous pouvez choisir de les **installer automatiquement**. 

    ![Sélectionner l’option Installer automatiquement les mises à jour](media/azure-stack-edge-install-2110-updates/install-updates-portal-2.png)    

    Vous pouvez également télécharger les mises à jour, puis **installer manuellement les mises à jour ultérieurement**.

    ![Sélectionner l’option Installer manuellement les mises à jour ultérieurement](media/azure-stack-edge-install-2110-updates/install-updates-portal-3.png)

3. Le téléchargement des mises à jour démarre. Un message de notification indique que le téléchargement est en cours.

    ![Notification affichant le téléchargement en cours des mises à jour](media/azure-stack-edge-install-2110-updates/install-updates-portal-4.png)

    Une bannière de notification s’affiche également dans le portail Azure. Celle-ci indique la progression du téléchargement. Vous pouvez sélectionner cette notification ou sélectionner **Mettre à jour l’appareil** pour afficher le détail de la mise à jour.

    ![Afficher l’état détaillé des mises à jour dans le panneau Mises à jour de l’appareil](media/azure-stack-edge-install-2110-updates/install-updates-portal-5.png)


4. Une fois le téléchargement terminé, la bannière de notification est mise à jour pour indiquer la fin de l’opération. Si vous avez choisi d’installer automatiquement les mises à jour, l’installation démarre automatiquement.

    Si vous avez choisi d’installer manuellement les mises à jour ultérieurement, sélectionnez la notification pour ouvrir le panneau **Mises à jour de l’appareil**. Sélectionnez **Installer la mise à jour**.
 
    ![Sélectionner Installer la mise à jour après le téléchargement des mises à jour](media/azure-stack-edge-install-2110-updates/install-updates-portal-6.png)
 
5. Un message de notification indique que l’installation est en cours. Le portail affiche également une alerte d’information pour indiquer que l’installation est en cours. L’appareil est mis hors connexion et est en mode maintenance.
   
    ![Affichage d’une bannière indiquant que l’appareil est en cours de maintenance ](media/azure-stack-edge-install-2110-updates/install-updates-portal-7.png)

6. Comme il s'agit d'un appareil à nœud unique, il redémarre après l'installation des mises à jour. 

    ![Affichage d’une bannière indiquant que l’appareil est en train de redémarrer](media/azure-stack-edge-install-2110-updates/install-updates-portal-8.png)

7. Après le redémarrage, le logiciel de l’appareil terminera la mise à jour. La mise à jour du logiciel Kubernetes démarrera automatiquement. L’appareil est à nouveau mis hors connexion et est en mode maintenance.

    ![Affichage d’une bannière indiquant que l’appareil est en mode maintenance](media/azure-stack-edge-install-2110-updates/install-updates-portal-9.png)   


8. Une fois le logiciel de l'appareil et les mises à jour de Kubernetes correctement installés, la notification de bannière disparaît. L’état de l’appareil est mis à jour et devient **Votre appareil est en ligne**. 

    ![Mise à jour terminée et l’appareil est en ligne](media/azure-stack-edge-install-2110-updates/install-updates-portal-10.png)

    Accédez à l’interface utilisateur web locale, puis accédez à la page **Mise à jour logicielle**. Vérifiez que le logiciel de l’appareil et Kubernetes sont correctement mis à jour et que la version du logiciel reflète cet état.

    ![Afficher la version actualisée du logiciel dans l’interface utilisateur locale](media/azure-stack-edge-install-2110-updates/install-updates-portal-11.png)


