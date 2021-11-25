---
title: Comment créer, importer et exporter des termes de glossaire
description: Découvrez comment créer, importer et exporter des termes de glossaire dans Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: e39641317cc02c12666adf622ccb931ef57d9339
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493682"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>Comment créer, importer et exporter des termes de glossaire

Cet article explique comment utiliser le glossaire métier dans Azure Purview. Vous découvrirez les étapes pour créer un terme de glossaire métier dans un catalogue de données Azure Purview, puis importer et exporter des termes de glossaire à l’aide de fichiers. csv.

## <a name="create-a-new-term"></a>Créez un terme

Pour créer un terme de glossaire, procédez comme suit :

1. Sélectionnez **Catalogue de données** dans le volet de navigation gauche de la page d’accueil, puis le bouton **Gérer le glossaire** au centre de la page.

    :::image type="content" source="media/how-to-create-import-export-glossary/find-glossary.png" alt-text="Capture d’écran montrant le catalogue de données avec le glossaire mis en surbrillance." border="true":::

2. Dans la page **Termes du glossaire**, sélectionnez **+ Nouveau terme**. Une page s’ouvre avec le modèle **Système par défaut** sélectionné. Choisissez le modèle avec lequel vous souhaitez créer un terme de glossaire, puis sélectionnez **Continuer**.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="Capture d’écran de la création de nouveau terme." border="true":::

3. Donnez à votre nouveau terme un nom qui doit être unique dans le catalogue. Le nom du terme respecte la casse, ce qui signifie que vous pouvez avoir un terme appelé **Exemple** et **exemple** dans le catalogue.

4. Ajoutez une **Définition**.

5. Définissez l’**État** du terme. Les nouveaux termes ont par défaut l’état **Brouillon**.

   :::image type="content" source="media/how-to-create-import-export-glossary/overview-tab.png" alt-text="Capture d’écran des choix d’état.":::

   Ces marqueurs d’état sont des métadonnées associées au terme. Actuellement, vous pouvez définir l’état suivant sur chaque terme :

   - **Brouillon** : ce terme n’est pas encore implémenté officiellement.
   - **Approuvé** : ce terme est officiel/standard/approuvé.
   - **Expiré** : ce terme ne doit plus être utilisé.
   - **Alerte** : ce terme requiert votre attention.

6. Ajoutez des **Ressources** et un **Acronyme**. Si le terme fait partie de la hiérarchie, vous pouvez ajouter des termes parents à **Parent** sous l’onglet de vue d’ensemble.

7. Ajoutez des **Synonymes** et des **Termes associés** sous l’onglet associé.

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="Capture d’écran de l’onglet Nouveau terme > Anglet." border="true":::

8. Si vous le souhaitez, sélectionnez l’onglet **Contacts** pour ajouter des Experts et des Gestionnaires à votre terme.

9. Sélectionnez **Créer** pour votre terme.

## <a name="import-terms-into-the-glossary"></a>Importer des termes dans le glossaire

L’Azure Purview Data Catalog fournit un modèle de fichier .csv pour importer vos termes dans votre glossaire.

Vous pouvez importer des termes dans le catalogue. Les termes en double dans le fichier sont remplacés.

Notez que les noms de terme respectent la casse. Par exemple, les termes `Sample` et `saMple` peuvent figurer tous deux dans le même glossaire.

### <a name="to-import-terms-follow-these-steps"></a>Pour importer des termes, procédez comme suit

1. Lorsque vous vous trouvez dans la page **Termes de glossaire**, sélectionnez **Importer les termes**.

2. La page de modèle de terme s’ouvre. Faites correspondre le modèle de terme au type de fichier .CSV que vous souhaitez importer.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="Capture d’écran de la page de termes de glossaire, bouton Importer les termes.":::

3. Téléchargez le modèle csv et utilisez-le pour entrer les termes que vous souhaitez ajouter. Lorsque vous nommez votre fichier modèle CSV, le nom doit commencer par une lettre et ne peut contenir que des lettres, des chiffres, des espaces, « _ » ou d’autres caractères Unicode non-ASCII. Les caractères spéciaux dans le nom de fichier génèrent une erreur.

   > [!Important]
   > Le système prend uniquement en charge l’importation des colonnes disponibles dans le modèle. Le modèle « Système par défaut » aura tous les attributs par défaut.
   > Toutefois, les modèles de terme personnalisés ont les attributs prêts à l’emploi et les attributs personnalisés supplémentaires définis dans le modèle. Par conséquent, le fichier .CSV diffère du nombre total de colonnes et de noms de colonnes en fonction du modèle de terme sélectionné. Vous pouvez également consulter le fichier pour examiner d’éventuels problèmes après le chargement.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="Capture d’écran de la page Termes de glossaire, avec sélection d’un fichier à Importer.":::

4. Une fois que vous avez terminé de remplir votre fichier. csv, sélectionnez votre fichier à importer, puis **OK**.

5. Le système charge le fichier et ajoute tous les termes à votre catalogue.
 
   > [!Important]
   > L’adresse de messagerie des gestionnaires et des experts doit être l’adresse principale de l’utilisateur du groupe AAD. L’adresse de messagerie alternative, le nom d’utilisateur principal et les e-mails non AAD ne sont pas encore pris en charge. 

## <a name="export-terms-from-glossary-with-custom-attributes"></a>Exporter des termes d’un glossaire avec des attributs personnalisés

Vous devez être en mesure d’exporter des termes d’un glossaire tant que les termes sélectionnés appartiennent au même modèle de terme.

1. Lorsque vous vous trouvez dans le glossaire, par défaut, le bouton **Exporter** est désactivé. Une fois que vous avez sélectionné les termes à exporter, le bouton **Exporter** est activé si les termes sélectionnés appartiennent au même modèle.

2. Sélectionnez **Exporter** pour télécharger les termes sélectionnés.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-export.png" lightbox="media/how-to-create-import-export-glossary/select-term-template-for-export.png" alt-text="Capture d’écran de la page Termes de glossaire, avec sélection d’un fichier à Exporter.":::

   > [!Important]
   > Si les termes dans une hiérarchie appartiennent à des modèles de termes différents, vous devez les fractionner dans différents fichiers .CSV pour l’importation. En outre, la mise à jour d’un parent d’un terme n’est actuellement pas prise en charge par le processus d’importation.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les termes du glossaire, consultez la [Référence du glossaire](reference-purview-glossary.md)