---
title: Diviser un répertoire d’images
titleSuffix: Azure Machine Learning
description: Découvrez comment utiliser le composant Diviser un répertoire d’images dans le concepteur pour diviser les images d’un répertoire d’images en deux jeux distincts.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 5adf44b76632af7e1d37189e9b81cc323b2e1216
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566945"
---
# <a name="split-image-directory"></a>Diviser un répertoire d’images

Cette rubrique explique comment utiliser le composant Diviser un répertoire d’images dans Azure Machine Learning Designer pour diviser les images d’un répertoire d’images en deux jeux distincts.

Ce composant est particulièrement utile quand vous devez séparer des données d’image en jeux de formation et de test. 

## <a name="how-to-configure-split-image-directory"></a>Comment configurer le module Diviser un répertoire d’images

1. Ajoutez le composant **Diviser un répertoire d’images** à votre pipeline. Vous trouverez ce composant dans la catégorie « Vision par ordinateur/Transformation de données d’image ».

2. Connectez-le au composant dont la sortie est le répertoire d’images.

3. Entrez **Fraction d’images dans la première sortie** pour spécifier le pourcentage de données à placer dans le fractionnement de gauche, par défaut 0.9. Si le résultat de la fraction n’est pas un entier, le composant utilise le plus petit entier proche.


## <a name="technical-notes"></a>Notes techniques

### <a name="expected-inputs"></a>Entrées attendues

| Nom                  | Type           | Description              |
| --------------------- | -------------- | ------------------------ |
| Répertoire d’images d’entrée | ImageDirectory | Répertoire d’images à fractionner |

### <a name="component-parameters"></a>Paramètres de composant

| Nom                                   | Type  | Plage | Facultatif | Description                            | Default |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Fraction d’images dans la première sortie | Float | 0-1   | Obligatoire | Fraction d’images dans la première sortie | 0.9     |

### <a name="outputs"></a>Sorties

| Nom                    | Type           | Description                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Répertoire d’images de sortie 1 | ImageDirectory | Répertoire d’images qui contient les images sélectionnées |
| Répertoire d’images de sortie 2 | ImageDirectory | Répertoire d’images qui contient toutes les autres images |

## <a name="next-steps"></a>Étapes suivantes

Consultez les [composants disponibles](component-reference.md) pour Azure Machine Learning. 
