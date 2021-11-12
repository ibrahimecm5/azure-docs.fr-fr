---
title: Limiter les valeurs
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant clip values dans Azure Machine Learning pour détecter les valeurs hors norme et découper ou remplacer leurs valeurs.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: f93026b6b0cc0add6d2b30e13e2ba0c91740a647
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566932"
---
# <a name="clip-values"></a>Limiter les valeurs

Cet article décrit un composant du concepteur Azure Machine Learning.

Utilisez le composant Limiter les valeurs pour identifier et éventuellement remplacer des valeurs de données qui sont au-dessus ou en dessous d’un seuil spécifié par une moyenne, une constante ou une autre valeur de remplacement.  

Vous connectez le composant à un jeu de données qui contient les nombres à limiter, choisissez les colonnes à utiliser, puis définissez un seuil ou une plage de valeurs ainsi qu’une méthode de remplacement. Le composant peut générer uniquement les résultats ou générer les valeurs changées ajoutées au jeu de données d’origine.

## <a name="how-to-configure-clip-values"></a>Comment configurer le module Limiter les valeurs

Avant de commencer, identifiez les colonnes à limiter et la méthode à utiliser. Nous vous recommandons de tester d’abord la méthode de limitation sur un petit sous-ensemble de données.

Le composant applique les mêmes critères et la même méthode de remplacement à **toutes** les colonnes que vous incluez dans la sélection. Par conséquent, veillez à exclure les colonnes que vous ne voulez pas changer.

Si vous devez appliquer des méthodes de limitation ou des critères différents à certaines colonnes, vous devez utiliser une nouvelle instance du module **Limiter les valeurs** pour chaque ensemble de colonnes similaires.

1.  Ajoutez le composant **Limiter les valeurs** à votre pipeline et connectez-le au jeu de données à modifier. Ce composant est disponible sous **Transformation des données**, dans la catégorie **Mettre à l’échelle et réduire**. 
  
1.  Dans **Liste des colonnes**, utilisez le sélecteur de colonne pour choisir les colonnes auxquelles appliquer les **valeurs limites**.  
  
1.  Pour **Ensemble de seuils**, choisissez l’une des options suivantes dans la liste déroulante. Ces options déterminent comment vous définissez les limites supérieure et inférieure des valeurs acceptables par rapport aux valeurs qui doivent être limitées.  
  
    - **ClipPeaks** : quand vous détourez des valeurs par pic, vous spécifiez uniquement une limite supérieure. Les valeurs supérieures à cette valeur limite sont remplacées.
  
    -  **ClipSubpeaks** : quand vous détourez des valeurs par creux, vous spécifiez uniquement une limite inférieure. Les valeurs inférieures à cette valeur limite sont remplacées.  
  
    - **ClipPeaksAndSubpeaks** : quand vous détourez des valeurs par pic et creux, vous pouvez spécifier les limites supérieure et inférieure. Les valeurs qui se trouvent en dehors de cette plage sont remplacées. Les valeurs qui correspondent aux valeurs limites ne sont pas changées.
  
1.  En fonction de votre sélection à l’étape précédente, vous pouvez définir les valeurs de seuil suivantes : 

    + **Seuil inférieur** : affiché uniquement si vous choisissez **ClipSubPeaks**
    + **Seuil supérieur** : affiché uniquement si vous choisissez **ClipPeaks**
    + **Seuil** : affiché uniquement si vous choisissez **ClipPeaksAndSubPeaks**

    Pour chaque type de seuil, choisissez **Constante** ou **Centile**.

1. Si vous sélectionnez **Constante**, tapez la valeur maximale ou minimale dans la zone de texte. Supposons, par exemple, que la valeur 999 a été utilisée comme valeur d’espace réservé. Vous pouvez choisir **Constante** pour le seuil supérieur, et taper 999 dans **Valeur constante du seuil supérieur**.
  
1. Si vous choisissez **Centile**, vous limitez les valeurs de colonne à une plage de centiles. 

    Par exemple, supposons que vous voulez conserver uniquement les valeurs de la plage de centiles 10-80 et remplacer toutes les autres. Vous devez choisir **Centile**, puis taper 10 pour **Valeur de centile du seuil inférieur** et 80 pour **Valeur de centile du seuil supérieur**. 

    Consultez la section sur les [centiles](#examples-for-clipping-using-percentiles) pour avoir des exemples d’utilisation des plages de centiles.  
  
1.  Définissez une valeur de remplacement.

    Les nombres qui correspondent exactement aux limites que vous avez spécifiées sont considérés comme étant compris dans la plage de valeurs autorisées et ne sont donc pas remplacés. Tous les nombres qui se trouvent en dehors de la plage spécifiée sont remplacés par la valeur de remplacement. 
  
    + **Valeur de substitution des pics** : définit la valeur de substitution pour toutes les valeurs de colonne supérieures au seuil spécifié.  
    + **Valeur de substitution des creux** : définit la valeur de substitution à utiliser pour toutes les valeurs de colonne inférieures au seuil spécifié.  
    + Si vous utilisez l’option **ClipPeaksAndSubpeaks**, vous pouvez spécifier des valeurs de remplacement distinctes pour les valeurs limitées supérieure et inférieure.  

    Les valeurs de remplacement suivantes sont prises en charge :  
  
    -   **Seuil** : remplace les valeurs détourées par la valeur de seuil spécifiée.  
  
    -   **Moyenne** : remplace les valeurs détourées par la moyenne des valeurs de colonne. La moyenne est calculée avant la limitation des valeurs.  
  
    -   **Médiane** : remplace les valeurs détourées par la médiane des valeurs de colonne. La médiane est calculée avant la limitation des valeurs.   
  
    -   **Valeur manquante**. Remplace les valeurs limitées par la valeur manquante (vide).  
  
1.  **Ajouter des colonnes d’indicateurs** : sélectionnez cette option si vous voulez générer une nouvelle colonne qui indique si l’opération d’écrêtage spécifiée est appliquée aux données de cette ligne. Cette option est utile quand vous testez un nouvel ensemble de valeurs de limitation et de remplacement.  
  
1. **Remplacer l’indicateur** : indiquez comment les nouvelles valeurs doivent être générées. Par défaut, le module **Limiter les valeurs** construit une nouvelle colonne avec les valeurs de pic limitées au seuil souhaité. Les nouvelles valeurs remplacent la colonne d’origine.  
  
    Pour conserver la colonne d’origine et ajouter une nouvelle colonne avec les valeurs limitées, désélectionnez cette option.  
  
1.  Envoyez le pipeline.  
  
    Cliquez avec le bouton droit sur le composant **Limiter les valeurs** et sélectionnez **Visualiser** ou sélectionnez le composant et basculez vers l’onglet **Sorties** dans le panneau droit, cliquez sur l’icône d’histogramme dans **Port outputs** (Sorties de port) pour examiner les valeurs et vérifier que l’opération de limitation répond à vos attentes.  
 
### <a name="examples-for-clipping-using-percentiles"></a>Exemples de limitation à l’aide de centiles

Pour comprendre le fonctionnement de la limitation par centile, prenons un jeu de données de 10 lignes, chacune ayant une instance des valeurs 1-10.  
  
- Si vous utilisez le centile comme seuil supérieur, à la valeur du 90e centile, 90 % de toutes les valeurs du jeu de données doivent être inférieures à cette valeur.  
  
- Si vous utilisez le centile comme seuil inférieur, à la valeur du 10e centile, 10 % de toutes les valeurs du jeu de données doivent être inférieures à cette valeur.  
  
1.  Pour **Ensemble de seuils**, choisissez **ClipPeaksAndSubPeaks**.  
  
1.  Pour l'option **Seuil supérieur**, choisissez **Centile**, puis, pour **Nombre de centiles**, tapez 90.  
  
1.  Pour l'option **Valeur de remplacement supérieure**, choisissez **Valeur manquante**.  
  
1.  Pour l'option **Seuil inférieur**, choisissez **Centile**, puis, pour **Nombre de centiles**, tapez 10.  
  
1.  Pour l'option **Valeur de remplacement inférieure**, choisissez **Valeur manquante**.  
  
1.  Désélectionnez l'option **Indicateur de remplacement** et sélectionnez l'option **Ajouter une colonne d'indicateur**.  
  
Essayez à présent le même pipeline en utilisant 60 comme seuil de centile supérieur et 30 comme seuil de centile inférieur, puis utilisez la valeur de seuil comme valeur de remplacement. Le tableau suivant compare ces deux résultats :  
  
1.  Remplacer par la valeur manquante ; Seuil supérieur = 90 ; Seuil inférieur = 20  
  
1.  Remplacer par le seuil ; Centile supérieur = 60 ; Centile inférieur = 40  
  
|Données d’origine|Remplacer par la valeur manquante|Remplacer par le seuil|  
|-------------------|--------------------------|----------------------------|  
|1<br /><br /> 2<br /><br /> 3<br /><br /> 4<br /><br /> 5<br /><br /> 6<br /><br /> 7<br /><br /> 8<br /><br /> 9<br /><br /> 10|TRUE<br /><br /> TRUE<br /><br /> 3, FALSE<br /><br /> 4, FALSE<br /><br /> 5, FALSE<br /><br /> 6, FALSE<br /><br /> 7, FALSE<br /><br /> 8, FALSE<br /><br /> 9, FALSE<br /><br /> TRUE|4, TRUE<br /><br /> 4, TRUE<br /><br /> 4, TRUE<br /><br /> 4, TRUE<br /><br /> 5, FALSE<br /><br /> 6, FALSE<br /><br /> 7, TRUE<br /><br /> 7, TRUE<br /><br /> 7, TRUE<br /><br /> 7, TRUE| 
 
## <a name="next-steps"></a>Étapes suivantes

Consultez les [composants disponibles](component-reference.md) pour Azure Machine Learning. 
