---
title: Créer des rapports de vecteur d’attaque
description: Les rapports de vecteur d’attaque fournissent une représentation graphique d’une chaîne de vulnérabilité d’appareils exploitables.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: a100363cd974bd820e859ca2456fcfa5c347e7eb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293285"
---
# <a name="attack-vector-reporting"></a>Création de rapports de vecteurs d’attaque

## <a name="about-attack-vector-reports"></a>À propos des rapports de vecteur d’attaque

Les rapports de vecteur d’attaque fournissent une représentation graphique d’une chaîne de vulnérabilité d’appareils exploitables. Ces vulnérabilités peuvent permettre à une personne malveillante d’accéder à des appareils réseau clés. Le Simulateur de vecteur d’attaque calcule les vecteurs d’attaque en temps réel et analyse tous les vecteurs d’attaque pour une cible spécifique.

L’utilisation du vecteur d’attaque vous permet d’évaluer l’effet des activités d’atténuation dans la séquence d’attaque. Vous pouvez ensuite déterminer si une mise à niveau du système perturbe le chemin d’accès de l’attaquant en interrompant la chaîne d’attaque ou si un autre chemin d’accès d’attaque demeure. Ces informations vous aident à hiérarchiser les activités de correction et d’atténuation.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Affichez vos alertes dans le centre de contrôle.":::

> [!NOTE]
> Les administrateurs et les analystes de la sécurité peuvent exécuter les procédures décrites dans cette section.

## <a name="create-an-attack-vector-report"></a>Créer un rapport de vecteur d’attaque

Pour créer une simulation de vecteur d’attaque :

1. Sélectionnez :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="Signe plus"::: dans le menu latéral pour ajouter une simulation.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="La simulation de vecteur d’attaque.":::

2. Entrer les propriétés de la simulation :

   - **Nom** : Nom de la simulation.

   - **Nombre maximal de vecteurs** : Nombre maximal de vecteurs dans une seule simulation.

   - **Afficher dans la table des appareils** : Affichez le vecteur d’attaque sous la forme d’un filtre dans la table des appareils.

   - **Tous les appareils sources** : Le vecteur d’attaque considère tous les appareils comme une source d’attaque.

   - **Source de l’attaque** : Le vecteur d’attaque va considérer uniquement les appareils spécifiés comme une source d’attaque.

   - **Tous les appareils cibles** : Le vecteur d’attaque considère tous les appareils comme une cible d’attaque.

   - **Cible de l’attaque** : Le vecteur d’attaque va considérer uniquement les appareils spécifiés comme une cible d’attaque.

   - **Exclure les appareils** : Les appareils spécifiés seront exclus de la simulation de vecteur d’attaque.

   - **Exclure sous-réseaux** : Les sous-réseaux spécifiés seront exclus de la simulation de vecteur d’attaque.

3. Sélectionnez **Ajouter la simulation**. La simulation sera ajoutée à la liste des simulations.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Ajoutez une nouvelle simulation.":::

4. Sélectionnez :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: si vous souhaitez modifier la simulation.

   Sélectionnez :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: si vous souhaitez supprimer la simulation.

   Sélectionnez :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: si vous souhaitez marquer la simulation comme favorite.

5. Une liste de vecteurs d’attaque apparaît et comprend le score du vecteur (sur 100), l’appareil source de l’attaque et l’appareil cible de l’attaque. Sélectionnez une attaque spécifique pour la représentation graphique des vecteurs d’attaque.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Vecteurs d’attaque.":::

## <a name="see-also"></a>Voir aussi

[Création de rapports de vecteurs d’attaque](how-to-create-attack-vector-reports.md)
