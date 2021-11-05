---
title: Exécuter des notebooks Jupyter dans votre espace de travail
titleSuffix: Azure Machine Learning
description: Découvrez comment exécuter un notebook Jupyter sans quitter votre espace de travail dans Azure Machine Learning studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 07/22/2021
ms.openlocfilehash: 1bbc831b32d2a29cf590f36ea0f2c436f739ee6b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076696"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>Exécuter des notebooks Jupyter dans votre espace de travail

Découvrez comment exécuter vos Jupyter notebooks Jupyter directement dans votre espace de travail dans Azure Machine Learning studio. En plus de la possibilité de lancer [Jupyter](https://jupyter.org/) ou [JupyterLab](https://jupyterlab.readthedocs.io), vous pouvez modifier et exécuter vos blocs-notes sans quitter l’espace de travail.

Pour plus d’informations sur la création et la gestion de fichiers, y compris de notebooks, consultez [Créer et gérer des fichiers dans votre espace de travail](how-to-manage-files.md).

> [!IMPORTANT]
> Les fonctionnalités marquées (préversion) étant fournies sans contrat de niveau de service, elles sont déconseillées pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Un espace de travail Machine Learning. Consultez [Créer un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md).

## <a name="edit-a-notebook"></a>Exécuter un bloc-notes

Pour modifier un bloc-notes, ouvrez n’importe quel bloc-notes situé dans la section **Fichiers utilisateur** de votre espace de travail. Cliquez sur la cellule à modifier.  Si vous n’avez pas de notebooks dans cette section, consultez [Créer et gérer des fichiers dans votre espace de travail](how-to-manage-files.md).

Vous pouvez modifier le bloc-notes sans vous connecter à une instance de calcul.  Lorsque vous souhaitez exécuter les cellules du bloc-notes, sélectionnez ou créez une instance de calcul.  Si vous sélectionnez une instance de calcul arrêtée, elle démarre automatiquement lorsque vous exécutez la première cellule.

Quand une instance de calcul est en cours d’exécution, vous pouvez également utiliser la saisie semi-automatique du code, optimisée par [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), dans n’importe quel notebook Python.

Vous pouvez également lancer Jupyter ou JupyterLab à partir de la barre d’outils du notebook.  Azure Machine Learning ne fournit pas de mises à jour et ne corrige pas les bogues de Jupyter ou JupyterLab, car il s’agit de produits open source qui sortent des limites du Support Microsoft.

## <a name="focus-mode"></a>Mode focus

Utilisez le mode focus pour développer votre affichage actuel afin de pouvoir vous concentrer sur vos onglets actifs. Le mode focus masque l’explorateur de fichiers Notebooks.

1. Dans la barre d’outils de la fenêtre du terminal, sélectionnez **Mode focus** pour activer le mode focus. Selon la largeur de votre fenêtre, l’outil peut se trouver sous l’élément de menu **…** dans la barre d’outils.
1. En mode focus, revenez à l’affichage standard en sélectionnant **Affichage standard**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Activer/désactiver le mode focus et l’affichage standard":::

## <a name="code-completion-intellisense"></a>Saisie semi-automatique du code (IntelliSense)

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) est une aide à la saisie semi-automatique de code, qui inclut de nombreuses fonctionnalités : Liste des membres, Informations sur les paramètres, Informations rapides et Compléter le mot. L’action de quelques touches suffit pour :
* En savoir plus sur le code que vous utilisez
* Suivre les paramètres que vous tapez
* Ajouter des appels aux propriétés et aux méthodes 

### <a name="insert-code-snippets-preview"></a>Insérer des extraits de code (préversion)

Utilisez **Ctrl + Espace** pour déclencher les extraits de code IntelliSense.  Faites défiler les suggestions ou commencez à taper pour rechercher le code à insérer.  Une fois que vous avez inséré du code, déplacez-vous dans les arguments afin de personnaliser le code pour votre propre usage.

:::image type="content" source="media/how-to-run-jupyter-notebooks/insert-snippet.gif" alt-text="Insérer un extrait de code":::

Ces mêmes extraits de code sont disponibles lorsque vous ouvrez votre notebook dans VS Code. Pour obtenir la liste complète des extraits de code disponibles, consultez [Extraits de code VS de Azure Machine Learning](https://github.com/Azure/azureml-snippets/blob/main/snippets/snippets.md).

Vous pouvez parcourir liste et y rechercher des extraits de code à l’aide de la barre d’outils du notebook pour ouvrir le panneau d’extrait de code.

:::image type="content" source="media/how-to-run-jupyter-notebooks/open-snippet-panel.png" alt-text="Ouvrir l’outil du panneau d’extrait de code dans la barre d’outils du notebook":::

À partir du panneau des extraits de code, vous pouvez également envoyer une demande d’ajout d’extrait de code.

:::image type="content" source="media/how-to-run-jupyter-notebooks/propose-new-snippet.png" alt-text="Le panneau d’extrait de code vous permet de proposer un nouvel extrait de code":::

## <a name="collaborate-with-notebook-comments-preview"></a>Collaborer avec des commentaires de notebook (préversion)

Utilisez un commentaire de notebook pour collaborer avec d’autres personnes qui ont accès à votre notebook.

Activez ou désactivez le volet des commentaires avec l’outil **Commentaires de notebook** en haut du notebook.  Si votre écran n’est pas assez large, recherchez cet outil en sélectionnant d’abord **...** à la fin de l’ensemble d’outils.

:::image type="content" source="media/how-to-run-jupyter-notebooks/notebook-comments-tool.png" alt-text="Capture d’écran de l’outil de commentaires de notebook dans la barre d’outils supérieure.":::  

Que le volet des commentaires soit visible ou non, vous pouvez ajouter un commentaire dans n’importe quelle cellule de code :

1. Sélectionnez du texte dans la cellule de code.  Vous pouvez uniquement commenter le texte dans une cellule de code.
1. Utilisez l’outil **Nouveau thread de commentaires** pour créer votre commentaire.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/comment-from-code.png" alt-text="Capture d’écran de l’outil d’ajout d’un commentaire à une cellule de code.":::
1. Si le volet des commentaires était masqué, il s’ouvre.  
1. Tapez votre commentaire et publiez-le avec l’outil ou utilisez **Ctrl + Entrée**.
1. Une fois qu’un commentaire a été publié, sélectionnez **...** en haut à droite pour :
    * Modifier le commentaire
    * Résoudre le thread
    * Supprimer le thread

Le texte qui a été commenté apparaît avec une mise en surbrillance violette dans le code. Quand vous sélectionnez un commentaire dans le volet des commentaires, votre notebook défile jusqu’à la cellule qui contient le texte mis en surbrillance.

> [!NOTE]
> Les commentaires sont enregistrés dans les métadonnées de la cellule de code.

## <a name="clean-your-notebook-preview"></a>Nettoyer votre notebook (préversion)

Au cours de la création d’un notebook, vous récupérez généralement les cellules que vous avez utilisées pour l’exploration des données ou le débogage. La fonctionnalité *Gather (Assembler)* vous permet de créer un notebook propre sans ces cellules superflues.

1. Exécutez toutes les cellules de votre notebook.
1. Sélectionnez la cellule qui contient le code que vous souhaitez que le nouveau notebook exécute. Par exemple, le code qui soumet une expérience ou le code qui inscrit un modèle.
1. Sélectionnez l’icône **Gather (Assembler)** qui apparaît dans la barre d’outils de la cellule.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Capture d’écran : sélectionner l’icône Gather (Assembler)":::
1. Entrez le nom de votre nouveau notebook « assemblé ».  

Le nouveau notebook contient uniquement des cellules de code, ainsi que toutes les cellules requises pour produire les mêmes résultats que ceux obtenus dans la cellule que vous avez sélectionnée pour l’assemblage.

## <a name="save-and-checkpoint-a-notebook"></a>Enregistrement et point de contrôle d’un bloc-notes

Azure Machine Learning crée un fichier de point de contrôle lorsque vous créez un fichier *ipynb*.

Dans la barre d’outils du bloc-notes, sélectionnez le menu, puis **Fichier&gt;Enregistrer et effectuer un point de contrôle** pour enregistrer manuellement le bloc-notes et ajouter un fichier de point de contrôle associé au bloc-notes.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Capture d’écran de l’outil d’enregistrement dans la barre d’outils du bloc-notes":::

Chaque bloc-notes est enregistré de façon automatique toutes les 30 secondes. L’enregistrement automatique met à jour uniquement le fichier *ipynb* initial, pas le fichier de point de contrôle.
 
Sélectionnez **Points de contrôle** dans le menu du bloc-notes pour créer un point de contrôle nommé et restaurer le bloc-notes à un point de contrôle enregistré.

## <a name="export-a-notebook"></a>Exporter un notebook

Dans la barre d’outils du notebook, sélectionnez le menu, puis **Exporter en tant que** pour exporter le notebook en tant qu’un des types pris en charge :

* Notebook
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="Exporter un notebook sur votre ordinateur":::

Le fichier exporté est enregistré sur votre ordinateur.

## <a name="run-a-notebook-or-python-script"></a>Exécuter un notebook ou un script Python

Pour exécuter un notebook ou un script Python, vous devez d’abord vous connecter à une [instance de calcul](concept-compute-instance.md) en cours d’exécution.

* Si vous n’avez pas d’instance de calcul, procédez comme suit pour en créer une :

    1. Dans la barre d’outils du notebook ou du script, à droite de la liste déroulante Calcul, sélectionnez **+ Nouveau calcul**. Selon la taille de votre écran, cette option peut se trouver dans un menu **...** .
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="Créer un calcul":::
    1. Nommez l’instance de calcul, puis choisissez une **taille de machine virtuelle**. 
    1. Sélectionnez **Create** (Créer).
    1. L’instance de calcul est connectée automatiquement au fichier.  Vous pouvez maintenant exécuter les cellules du notebook ou du script Python à l’aide de l’outil situé à gauche de l’instance de calcul.

* Si vous avez une instance de calcul arrêtée, sélectionnez **Démarrer le calcul** à droite de la liste déroulante Calcul. Selon la taille de votre écran, cette option peut se trouver dans un menu **...** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="Démarrer l’instance de calcul":::
    
Une fois que vous êtes connecté à une instance de calcul, utilisez la barre d’outils pour exécuter toutes les cellules du notebook ou Ctrl + Entrée pour exécuter une seule cellule sélectionnée. 

Vous seul pouvez voir et utiliser les instances de calcul que vous créez.  Vos **Fichiers utilisateur** sont stockés séparément de la machine virtuelle, et partagés entre toutes les instances de calcul dans l’espace de travail.

### <a name="view-logs-and-output"></a>Afficher les journaux et la sortie

Utilisez des [widgets de notebook](/python/api/azureml-widgets/azureml.widgets) pour afficher la progression de l’exécution et les journaux. Un widget est asynchrone et fournit des mises à jour jusqu’à ce que l’apprentissage se termine. Les widgets d’Azure Machine Learning sont également pris en charge dans Jupyter et JupterLab.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="Capture d’écran : Widget de notebook Jupyter ":::

## <a name="explore-variables-in-the-notebook"></a>Explorer les variables dans le notebook

Dans la barre d’outils du notebook, utilisez l’outil **Explorateur de variables** pour afficher le nom, le type, la longueur et les exemples de valeurs pour toutes les variables qui ont été créées dans votre notebook.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="Capture d’écran : Outil Explorateur de variables":::

Sélectionnez l’outil pour afficher la fenêtre de l’Explorateur de variables.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="Capture d’écran : Fenêtre de l’Explorateur de variables":::

## <a name="navigate-with-a-toc"></a>Naviguer avec une table des matières

Dans la barre d’outils du notebook, utilisez l’outil **Table des matières** pour afficher ou masquer la table des matières.  Démarrez une cellule Markdown avec un titre pour l’ajouter à la table des matières. Cliquez sur une entrée de la table pour atteindre cette cellule dans le notebook.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="Capture d’écran : Table des matières dans le notebook":::

## <a name="change-the-notebook-environment"></a>Modifier l’environnement du bloc-notes

La barre d’outils du notebook vous permet de modifier l’environnement d’exécution de votre notebook.  

Ces actions ne modifient pas l’état du bloc-notes ou les valeurs des variables dans celui-ci :

|Action  |Résultats  |
|---------|---------| --------|
|Arrêter le noyau     |  Arrête toute cellule en cours d’exécution. L’exécution d’une cellule entraîne automatiquement le redémarrage du noyau. |
|Naviguer vers une autre section de l’espace de travail     |     Les cellules en cours d’exécution sont arrêtées. |

Les actions ci-après ont pour effet de réinitialiser l’état du bloc-notes et toutes les variables dans celui-ci.

|Action  |Résultat  |
|---------|---------| --------|
| Modifier le noyau | Le bloc-notes utilise le nouveau noyau. |
| Changer d’instance de calcul    |     Le bloc-notes utilise automatiquement la nouvelle instance de calcul. |
| Réinitialiser l’instance de calcul | Redémarre quand vous tentez d’exécuter une cellule. |
| Arrêter l’instance de calcul     |    Aucune cellule ne s’exécute.  |
| Ouvrir le bloc-notes dans Jupyter ou JupyterLab     |    Le bloc-notes s’ouvre dans un nouvel onglet.  |

## <a name="add-new-kernels"></a>Ajouter de nouveaux noyaux

[Utilisez le terminal ](how-to-access-terminal.md#add-new-kernels) pour créer des noyaux et les ajouter à votre instance de calcul. Le notebook trouve automatiquement tous les noyaux Jupyter installés sur l’instance de calcul connectée.

Utilisez la liste déroulante des noyaux à droite pour passer à l’un des noyaux installés.  


### <a name="status-indicators"></a>Indicateurs d’état

Un indicateur en regard de la liste déroulante **Calcul** affiche son état.  L’État est également indiqué dans la liste déroulante elle-même.  

|Couleur |État du calcul |
|---------|---------| 
| Vert | Calcul en cours d’exécution |
| Rouge |Échec du calcul | 
| Noir | Calcul arrêté |
|  Bleu clair |Calcul en cours de création, de démarrage, de redémarrage ou de configuration |
|  Gris |Calcul en cours d’arrêt ou de suppression |

Un indicateur en regard de la liste déroulante **Noyau** affiche son état.

|Couleur |État du noyau |
|---------|---------|
|  Vert |Noyau connecté, inactif, occupé|
|  Gris |Tunnel non connecté. |

## <a name="find-compute-details"></a>Rechercher les détails d’un calcul

Pour plus de détails sur vos instances de calcul, consultez la page **Calcul** dans de [Studio](https://ml.azure.com).

## <a name="useful-keyboard-shortcuts"></a>Raccourcis clavier utiles
À l’instar des notebooks Jupyter, les notebooks Azure Machine Learning Studio disposent d’une interface utilisateur modale. Le clavier effectue des actions différentes selon le mode dans lequel se trouve la cellule du bloc-notes. Les notebooks Azure Machine Learning Studio prennent en charge les deux modes suivants pour une cellule de code donnée : le mode de commande et le mode d’édition.

### <a name="command-mode-shortcuts"></a>Raccourcis du mode de commande

Une cellule est en mode de commande quand elle n’affiche aucun curseur texte vous invitant à saisir. Quand une cellule est en mode de commande, vous pouvez modifier le bloc-notes entier, mais pas taper dans des cellules individuelles. Entrez en mode de commande en appuyant sur `ESC` ou en utilisant la souris pour sélectionner en dehors de la zone de l’éditeur d’une cellule.  La bordure gauche de la cellule active est bleue et unie, et son bouton **Exécuter** est bleu.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="Cellule de notebook en mode de commande ":::

| Raccourci                      | Description                          |
| ----------------------------- | ------------------------------------|
| Entrez                         | Passer au mode Édition             |        
| Maj+Entrée                 | Exécuter la cellule, sélectionner en dessous         |     
| Contrôle/Commande + Entrée       | Exécuter la cellule                            |
| Alt + Entrée                   | Exécuter la cellule, insérer la cellule de code en dessous    |
| Contrôle/Commande + Alt + Entrée | Exécuter la cellule, insérer la cellule Markdown en dessous|
| Alt + R                       | Exécuter tout      |                       
| O                             | Convertir la cellule en code    |                         
| M                             | Convertir la cellule en Markdown  |                       
| Haut/K                          | Sélectionner la cellule au-dessus    |               
| Bas/J                        | Sélectionner la cellule en dessous    |               
| Un                             | Insérer une cellule de code au-dessus  |            
| B                             | Insérer une cellule de code au-dessous   |           
| Contrôle/Commande + Maj + A   | Insérer une cellule Markdown au-dessus    |      
| Contrôle/Commande + Maj + B   | Insérer une cellule Markdown au-dessous   |       
| X                             | Couper la cellule sélectionnée    |               
| C                             | Copier la cellule sélectionnée   |               
| Maj + V                     | Coller la cellule sélectionnée au-dessus           |
| V                             | Coller la cellule sélectionnée au-dessous    |       
| D D                           | Supprimer la cellule sélectionnée|                
| O                             | Activer/désactiver la sortie         |              
| Maj + O                     | Activer/désactiver le défilement de sortie   |          
| I I                           | Interrompre le noyau |                   
| 0 0                           | Redémarrer le noyau |                     
| Maj + espace                 | Faire défiler vers le haut  |                         
| Espace                         | Faire défiler vers le bas|
| Onglet                           | Définir le focus sur le prochain élément pouvant être actif (quant le recouvrement par tabulation est désactivé)|
| Contrôle/Commande + S           | Enregistrer le notebook |                      
| 1                             | Remplacer par h1|                       
| 2                             | Remplacer par h2|                        
| 3                             | Remplacer par h3|                        
| 4                             | Remplacer par h4 |                       
| 5                             | Remplacer par h5 |                       
| 6                             | Remplacer par h6 |                       

### <a name="edit-mode-shortcuts"></a>Raccourcis du mode d’édition

Le mode d’édition est indiqué par un curseur texte qui vous invite à taper dans la zone de l’éditeur. Quand une cellule est en mode d’édition, vous pouvez saisir dans la cellule. Entrez en mode édition en appuyant sur `Enter` ou en utilisant la souris pour sélectionner la zone de l’éditeur d’une cellule. La bordure gauche de la cellule active est verte et en pointillés, et son bouton **Exécuter** est vert. Vous voyez également l’invite de curseur dans la cellule en mode d’édition.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="Cellule de notebook en mode d’édition":::

Les raccourcis clavier suivants vous permettent de naviguer et d’exécuter du code plus facilement dans des notebooks Azure Machine Learning en mode d’édition.

| Raccourci                      | Description|                                     
| ----------------------------- | ----------------------------------------------- |
| Caractère d'échappement                        | Entrer en mode de commande|  
| Contrôle/Commande + espace       | Activer IntelliSense |
| Maj+Entrée                 | Exécuter la cellule, sélectionner en dessous |                         
| Contrôle/Commande + Entrée       | Exécuter la cellule  |                                      
| Alt + Entrée                   | Exécuter la cellule, insérer la cellule de code en dessous  |              
| Contrôle/Commande + Alt + Entrée | Exécuter la cellule, insérer la cellule Markdown en dessous  |          
| Alt + R                       | Exécuter toutes les cellules     |                              
| Haut                            | Déplacer le curseur vers le haut ou vers la cellule précédente    |             
| Descendre                          | Déplacer le curseur vers le bas ou la cellule suivante |                  
| Contrôle/Commande + S           | Enregistrer le notebook   |                                
| Contrôle/Commande + haut          | Atteindre le début de la cellule   |                             
| Contrôle/Commande + bas        | Atteindre la fin de la cellule |                                 
| Onglet                           | Saisie semi-automatique de code ou mise en retrait (si le recouvrement par tabulation est activé) |
| Contrôle/Commande + M           | Activer/désactiver le recouvrement par tabulation  |                       
| Contrôle/Commande + ]           | Retrait |                                         
| Contrôle/Commande + [           | Retrait négatif  |                                        
| Contrôle/Commande + A           | Sélectionner tout|                                      
| Contrôle/Commande + Z           | Annuler |                                           
| Contrôle/Commande + Maj + Z   | Rétablir |                                           
| Contrôle/Commande + Y           | Rétablir |                                           
| Contrôle/Commande + début        | Atteindre le début de la cellule|                                
| Contrôle/Commande + fin         | Atteindre la fin de la cellule   |                               
| Contrôle/Commande + gauche        | Atteindre le mot à gauche |                               
| Contrôle/Commande + droite       | Atteindre le mot à droite |                              
| Contrôle/Commande + retour arrière   | Supprimer le mot précédent |                             
| Contrôle/Commande + Suppr      | Supprimer le mot suivant |                              
| Contrôle/Commande + /           | Afficher/masquer le commentaire sur la cellule

## <a name="troubleshooting"></a>Dépannage

* Si vous ne pouvez pas vous connecter à un notebook, vérifiez que la communication avec le socket web n’est **pas** désactivée. Pour que la fonctionnalité d’instance de calcul Jupyter fonctionne, la communication avec le socket web doit être activée. Vérifiez que votre [réseau autorise les connexions WebSocket](how-to-access-azureml-behind-firewall.md?tabs=ipaddress#microsoft-hosts) à *.instances.azureml.net et *.instances.azureml.ms. 

* Quand une instance de calcul est déployée dans un espace de travail avec un point de terminaison privé, elle est uniquement [accessible à partir d’un réseau virtuel](./how-to-secure-training-vnet.md). Si vous utilisez un DNS ou un fichier d’hôtes personnalisé, ajoutez une entrée pour < nom-instance >.< région >.instances.azureml.ms avec l’adresse IP privée du point de terminaison privé de votre espace de travail. Pour plus d’informations, consultez l’article [DNS personnalisé](./how-to-custom-dns.md?tabs=azure-cli).

* Si votre noyau a planté et a été redémarré, vous pouvez exécuter la commande suivante pour consulter le journal jupyter et obtenir plus de détails: `sudo journalctl -u jupyter`. Si les problèmes de noyau persistent, n’hésitez pas à utiliser une instance de calcul offrant davantage de mémoire.

* Si vous rencontrez un problème de jeton expiré, déconnectez-vous du studio Azure Machine Learning, reconnectez-vous, puis redémarrez le noyau de notebook.
    
## <a name="next-steps"></a>Étapes suivantes

* [Exécuter votre première expérience](tutorial-1st-experiment-sdk-train.md)
* [Sauvegarder votre stockage de fichiers avec des captures instantanées](../storage/files/storage-snapshots-files.md)
* [Utilisation des environnements sécurisés](./how-to-secure-training-vnet.md#compute-cluster)
