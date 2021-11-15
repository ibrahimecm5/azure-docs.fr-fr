---
title: Module de fonction ABAP d’extraction des métadonnées dans SAP R3 – Azure Purview
description: Cet article décrit les étapes pour déployer le module de fonction ABAP dans le serveur SAP
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/07/2021
ms.openlocfilehash: 7894e187efedf91e3c586dd0d8caaf35e753be3c
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028575"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>Déployer le module de fonction ABAP d’extraction des métadonnées pour la famille de ponts SAP R3

Cet article décrit les étapes pour déployer le module de fonction ABAP dans le serveur SAP.

## <a name="overview"></a>Vue d’ensemble

Les ponts SAP Business Suite 4 HANA (S/4HANA), ECC et R/3 ERP peuvent être utilisés pour extraire des métadonnées à partir du serveur SAP. Pour ce faire, placez le module de fonction ABAP sur le serveur SAP. Ce module de fonction est accessible à distance par le pont pour interroger et télécharger (sous forme de fichier texte) les métadonnées contenues dans le serveur SAP.

Une fois exécuté, le pont :

1. Importe des métadonnées à partir d’un fichier existant déjà téléchargé localement à partir d’une exécution de pont précédente.

2. Appelle l’API du module ABAP, attend le téléchargement, puis importe les métadonnées à partir de ce fichier.

Ce document détaille les étapes requises pour déployer ce module.

> [!Note]
> Les instructions suivantes ont été compilées en se basant sur l’interface graphique utilisateur SAP v.7.2

## <a name="prerequisites"></a>Prérequis

Téléchargez le code source du module de fonction ABAP à partir de Purview Studio. Lorsque vous inscrivez une source, puis effectuez une nouvelle analyse pour [SAP ECC](register-scan-sapecc-source.md) ou [SAP S/4HANA](register-scan-saps4hana-source.md), vous pouvez trouver un lien de téléchargement en haut, comme suit. 

:::image type="content" source="media/abap-functions-deployment-guide/download-abap-code.png" alt-text="Télécharger le code source du module de fonction ABAP à partir de Purview Studio" border="true":::

## <a name="deployment-of-the-module"></a>Déploiement du module

### <a name="create-a-package"></a>Créer un package

Cette étape est facultative et un package existant peut être utilisé.

1. Connectez-vous au serveur SAP S/4HANA ou SAP ECC et ouvrez le **Navigateur d’objets** (transaction SE80).

2. Sélectionnez l’option **Package** dans la liste et entrez un nom pour le nouveau package (par exemple Z\_MITI), puis appuyez sur le bouton **Afficher**.

3. Appuyez sur **Oui** dans la fenêtre **Créer un package**. Une fenêtre **Générateur de package : Créer un package** s’ouvre. Entrez la valeur dans le champ **Description abrégée**, puis sélectionnez l’icône **Continuer**.

4. Sélectionnez **Mes requêtes** dans la fenêtre **Demande de requête locale Workbench**. Sélectionnez la requête **Développement**.

### <a name="create-a-function-group"></a>Créer un groupe de fonction

Dans le Navigateur d’objets, sélectionnez **Groupe de fonctions** dans la liste et tapez son nom dans le champ d’entrée ci-dessous (par exemple Z\_MITI\_FGROUP). Sélectionnez l’icône **Afficher**.

1. Dans la fenêtre **Créer un objet**, sélectionnez **Oui** pour créer un nouveau groupe de fonctions.

2. Spécifiez une description appropriée dans le champ **Texte abrégé** et appuyez sur le bouton **Enregistrer**.

3. Choisissez un package qui a été préparé à l’étape précédente **Créer un package**, puis sélectionnez **Enregistrer**.

4. Confirmez une requête en appuyant sur l’icône **Continuer**.

5. Activez le groupe de fonctions.

### <a name="create-the-abap-function-module"></a>Créer le module de fonction ABAP

1. Une fois le groupe de fonctions créé, sélectionnez-le.

2. Sélectionnez et maintenez (ou cliquez avec le bouton droit de la souris) sur le nom du groupe de fonctions dans le navigateur de référentiel, puis sélectionnez **Créer**, puis **Module de fonctions**.

3. Dans le champ **Module de fonction**, entrez `Z_MITI_DOWNLOAD`. Remplissez l’entrée **Texte abrégé** avec une description appropriée.

Une fois le module créé, spécifiez les informations suivantes :

1. Accédez à l’onglet **Attributs**.

2. Définissez **Type de traitement** sur **Module de fonction activé à distance**.

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="Option des sources de registre : Module de fonction activé à distance" border="true":::

3. Accédez à l’onglet **Code source**. Il existe deux façons de déployer du code pour la fonction :

   a. Dans le menu principal, chargez le fichier texte que vous avez téléchargé à partir de Purview Studio, comme décrit dans la section [Prérequis](#prerequisites). Pour ce faire, sélectionnez **Utilitaires**, **Autres utilitaires**, **Charger/Télécharger**, puis **Charger**.

   b. Vous pouvez également ouvrir le fichier, copier son contenu et le coller dans la zone de **Code source**.

4. Accédez à l’onglet **Importer** et créez les paramètres suivants :

   a.  P\_AREA TYPE DD02L-TABNAME (facultatif = True)

   b.  *P\_LOCAL\_PATH TYPE STRING* (facultatif = True)

   c.  *P\_LANGUAGE TYPE L001TAB-DATA DEFAULT \'E\'*

   d.  *ROWSKIPS TYPE SO\_INT DEFAULT 0*

   e.  *ROWCOUNT TYPE SO\_INT DEFAULT 0*

   > [!Note]
   > Choisir une **Valeur de passage** pour tous

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="Option des sources de registre : Importer des paramètres" border="true":::

5. Accédez à l’onglet « Tables » et définissez les éléments suivants :

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="Option des sources de registre : Onglet Tables" border="true":::

6. Accédez à l’onglet **Exceptions** et définissez l’exception suivante : `E_EXP_GUI_DOWNLOADFAILED`.

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="Option des sources de registre : Onglet Exceptions" border="true":::

7. Enregistrez la fonction (appuyez sur Ctrl+S ou choisissez **Module de fonction**, puis **Enregistrer** dans le menu principal).

8. Cliquez sur l’icône **Activer** dans la barre d’outils (Ctrl+F3) et sélectionnez le bouton **Continuer** dans la boîte de dialogue. Si vous y êtes invité, vous devez sélectionner les fichiers Include générés à activer avec le module de fonction principal.

### <a name="testing-the-function"></a>Test de la fonction

Une fois toutes les étapes précédentes effectuées, suivez les étapes ci-dessous pour tester la fonction :

1. Ouvrez le module de fonction Z\_MITI\_DOWNLOAD.

2. Choisissez **Module de fonction**, **Tester**, puis **Tester le module de fonction** dans le menu principal (ou appuyez sur F8).

3. Entrez un chemin d’accès au dossier sur le système de fichiers local dans le paramètre P\_LOCAL\_PATH et appuyez sur l’icône **Exécuter** dans la barre d’outils (ou appuyez sur F8).

4. Placez le nom de la zone d’intérêt dans le champ P\_AREA si un fichier contenant des métadonnées doit être téléchargé ou mis à jour. Quand la fonction a terminé, le dossier qui a été indiqué dans le paramètre P\_LOCAL\_PATH doit contenir plusieurs fichiers avec des métadonnées. Les noms des fichiers imitent les zones qui peuvent être spécifiées dans le champ P\_AREA.

L’exécution de la fonction et le téléchargement des métadonnées sont beaucoup plus rapides si la fonction est lancée sur la machine qui dispose d’une connexion réseau à haut débit avec le serveur SAP S/4HANA ou ECC.

## <a name="next-steps"></a>Étapes suivantes

- [Inscrire et analyser la source SAP ECC](register-scan-sapecc-source.md)
- [Inscrire et analyser la source SAP S/4HANA](register-scan-saps4hana-source.md)
