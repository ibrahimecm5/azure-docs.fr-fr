---
title: Création d’une classification et d’une règle de classification personnalisées
description: Découvrez comment créer des classifications personnalisées pour définir des types de données propres à votre organisation dans Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8e1e43b1c1f11ae6eb37ab599f9636bc47423f8b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442138"
---
# <a name="custom-classifications-in-azure-purview"></a>Classifications personnalisées dans Azure Purview

Cet article vous explique comment créer des classifications personnalisées pour définir des types de données propres à votre organisation dans votre patrimoine de données. Il décrit également la création de règles de classification personnalisées permettant de trouver les données spécifiées dans l’ensemble du patrimoine de données.

## <a name="default-system-classifications"></a>Classifications système par défaut

Le catalogue de données Azure Purview propose un grand nombre de classifications système par défaut qui représentent des types de données personnelles typiques, que vous possédez peut-être dans votre patrimoine de données. Pour obtenir la liste complète des classifications système disponibles, consultez [classifications prises en charge dans Azure Purview](supported-classifications.md).

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="Sélection de Classifications" border="true":::

Vous avez également la possibilité de créer des classifications personnalisées, si aucune des classifications par défaut ne répond à vos besoins.

> [!Note]
> Nos [règles d'échantillonnage de données](sources-and-scans.md#sampling-within-a-file) sont appliquées à la fois aux classifications système et personnalisées.  

> [!NOTE]
> Les classifications personnalisées Purview sont appliquées uniquement aux sources de données structurées telles que SQL et CosmosDB, ainsi qu’aux types de fichiers structurés tels que CSV, JSON et Parquet. La classification personnalisée n’est pas appliquée aux types de fichiers de données non structurés tels que DOC, PDF et XLSX.

## <a name="steps-to-create-a-custom-classification"></a>Procédure de création d’une classification personnalisée

Pour créer une classification personnalisée, procédez comme suit :

1. Dans votre catalogue, sélectionnez **Data Map** dans le menu de gauche.

2. Sélectionnez **Classifications** sous **Gestion des annotations**.

3. Sélectionnez **+Nouveau**.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="Nouvelle classification" border="true":::

Le volet **Ajouter une nouvelle classification** s’ouvre. Il vous permet d’attribuer un nom et une description à votre classification. Il est conseillé d’utiliser une convention d’espacement des noms du type `your company name.classification name`.

Les classifications système de Microsoft sont regroupées sous l’espace de noms réservé `MICROSOFT.`, Par exemple **MICROSOFT.GOVERNMENT.US.SOCIAL\_SECURITY\_NUMBER**.

Le nom de la classification doit commencer par une lettre, suivie d’une séquence de lettres, de chiffres, de points (.) et de traits de soulignement. Les espaces ne sont pas autorisés. À mesure que vous tapez, l’expérience utilisateur génère automatiquement un nom convivial. Le nom convivial est celui que voient les utilisateurs quand vous l’appliquez à une ressource dans le catalogue.

Pour que le nom convivial reste concis, le système le crée suivant la logique ci-dessous :

- Tous les segments de l’espace de noms sont découpés, à l’exception des deux derniers.

- La casse est ajustée de sorte que la première lettre de chaque mot soit en majuscule.

- Tous les traits de soulignement (\_) sont remplacés par des espaces.

Par exemple, si vous avez nommé votre classification **CONTOSO.HR.EMPLOYEE\_ID**, le nom convivial stocké dans le système est **Hr.Employee ID**.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="CONTOSO.HR.EMPLOYEE_ID" border="true":::

Sélectionnez **OK** : votre nouvelle classification est ajoutée à la liste de vos classifications.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="Classification personnalisée" border="true":::

Si vous sélectionnez la classification dans la liste, la page Détails de la classification s’ouvre. Vous y trouverez tous les détails de la classification :

nombre d’instances, nom formel, règles de classification associées (le cas échéant) et nom du propriétaire.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="Sélection de la classification" border="true":::

## <a name="custom-classification-rules"></a>Règles de classification personnalisées

Le service de catalogue fournit un ensemble de règles de classification par défaut, utilisées par l’analyseur pour détecter automatiquement certains types de données. Vous pouvez également ajouter vos propres règles de classification personnalisées pour détecter d’autres types de données qui vous intéressent. Cette fonctionnalité peut être puissante si vous recherchez certaines données dans votre patrimoine de données.

Prenons par exemple une société nommée Contoso. Les ID employés sont standardisés dans l’ensemble de l’entreprise avec le mot \"Employee\" suivi d’un GUID, soit EMPLOYEE{GUID}. Par exemple, une instance d’un ID d’employé se présente comme ceci : `EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55`.

Contoso est en mesure de configurer le système d’analyse de façon à rechercher des instances de ces ID en créant une règle de classification personnalisée. L’entreprise peut fournir une expression régulière qui correspond au modèle de données, en l’occurrence `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$`. Éventuellement, si les données se trouvent généralement dans une colonne dont le nom est connu, comme Employee\_ID ou EmployeeID, il est possible d’ajouter une expression régulière de modèle de colonne pour rendre l’analyse encore plus précise. Voici un exemple d’expression régulière : Employee\_ID\|EmployeeID.

Le système d’analyse peut alors, avec cette règle, examiner les données réelles et le nom de la colonne pour tenter d’identifier chacune des instances où le modèle d’ID d’employé est trouvé.

## <a name="steps-to-create-a-custom-classification-rule"></a>Procédure de création d’une règle de classification personnalisée

Pour créer une règle de classification personnalisée, procédez comme suit :

1. Créez une classification personnalisée en suivant les instructions de la section précédente. Vous l’ajouterez dans la configuration de la règle de classification afin que le système l’applique lorsqu’il trouve une correspondance dans la colonne.

2. Sélectionnez l’icône de **Data Map**.

3. Sélectionnez la section **Règles de classification**.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification-rules.png" alt-text="Vignette Règles de classification" border="true":::

4. Sélectionnez **Nouveau**.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification-rule.png" alt-text="Ajout d’une nouvelle règle de classification" border="true":::

5. La boîte de dialogue **Nouvelle règle de classification** s’ouvre. Renseignez les champs et déterminez si vous souhaitez créer une **règle d’expression régulière** ou une **règle de dictionnaire**.

   |Champ     |Description  |
   |---------|---------|
   |Nom   |    Obligatoire. 100 caractères maximum.    |
   |Description      |facultatif. 256 caractères maximum.    |
   |Nom de la classification    | Obligatoire. Sélectionnez le nom de la classification dans la liste déroulante pour indiquer à l’analyseur de l’appliquer si une correspondance est trouvée.        |
   |État   |  Obligatoire. Les options sont activées ou désactivées. Elles sont par défaut activées.    |

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-classification-rule.png" alt-text="Création d’une règle de classification" border="true":::

### <a name="creating-a-regular-expression-rule"></a>Création d’une règle d’expression régulière

1. Si vous créez une règle d’expression régulière, vous verrez l’écran suivant. Vous pouvez éventuellement télécharger un fichier qui sera utilisé afin de générer des **modèles regex suggérés** pour votre règle.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-regex-rule.png" alt-text="Créer une règle regex" border="true":::

1. Si vous décidez de générer un modèle regex suggéré, après avoir téléchargé un fichier, sélectionnez l’un des modèles suggérés, puis **Ajouter aux modèles** pour utiliser les modèles de données et de colonnes suggérés. Vous pouvez modifier les modèles suggérés ou faire vos propres modèles sans télécharger de fichier.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/suggested-regex.png" alt-text="Générer une expression régulière suggérée" border="true":::

   |Champ     |Description  |
   |---------|---------|
   |Modèle de données    |facultatif. Expression régulière représentant les données stockées dans le champ de données. La limite est très élevée. Dans l’exemple précédent, les modèles de données testent un ID d’employé correspondant littéralement au mot `Employee{GUID}`.  |
   |Modèle de colonne    |facultatif. Expression régulière représentant les noms de colonnes à faire correspondre. La limite est très élevée. |

1. Sous **Modèle de données**, vous pouvez utiliser le **Seuil de correspondances minimales** pour définir le pourcentage minimal de correspondances de valeurs de données distinctes dans une colonne qui doivent être trouvées par l’analyseur pour que la classification soit appliquée. La valeur suggérée est 60 %. Si vous spécifiez plusieurs modèles de données, ce paramètre est désactivé et la valeur fixée à 60 %.

   > [!Note]
   > Le seuil de correspondance minimal doit être au moins égal à 1%.

1. Vous pouvez maintenant vérifier votre règle et la **créer**.
1. Testez la règle de classification avant de terminer le processus de création pour vérifier qu’elle appliquera des balises à vos ressources. Les classifications de la règle seront appliquées aux exemples de données chargées de la même façon que dans une analyse. Ainsi, toutes les classifications système et votre classification personnalisée seront mises en correspondance avec les données de votre fichier.

   Les fichiers d’entrée peuvent inclure des fichiers délimités (CSV, PSV, SSV, TSV), JSON ou du contenu XML. Le contenu sera analysé en fonction de l’extension du fichier d’entrée. Les données délimitées peuvent porter une extension de fichier correspondant à l’un des types mentionnés. Par exemple, un fichier nommé MySampleData.csv peut contenir des données TSV. Le contenu délimité doit également présenter un minimum de 3 colonnes.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/test-rule-screen.png" alt-text="Tester la règle avant de la créer" border="true":::

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/test-rule-uploaded-file-result-screen.png" alt-text="Afficher les classifications appliquées après avoir chargé un fichier de test" border="true":::

### <a name="creating-a-dictionary-rule"></a>Création d’une règle de dictionnaire

1. Si vous créez une règle de dictionnaire, l’écran suivant s’affiche. Téléchargez un fichier qui contient toutes les valeurs possibles pour la classification que vous créez dans une seule colonne.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-rule.png" alt-text="Créer une règle de dictionnaire" border="true":::

1. Une fois le dictionnaire généré, vous pouvez ajuster le seuil de correspondance minimal, puis soumettre la règle.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Créer une règle de dictionnaire, avec la coche de dictionnaire généré." border="true":::

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous l’avez créé, votre règle de classification est prête à être ajoutée à un ensemble de règles d’analyse afin d’être utilisée par votre analyse. Pour plus d’informations, consultez [Création d’un ensemble de règles d’analyse](create-a-scan-rule-set.md).
