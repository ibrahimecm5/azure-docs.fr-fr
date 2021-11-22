---
title: Définir le contrôle d’accès utilisateur global
description: Dans les grandes organisations, les autorisations utilisateur peuvent être complexes et peuvent être déterminées par une structure organisationnelle globale, en plus du site standard et de la structure de zone.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 70437ceeadba0b1c8af9559037ccf718d7a251d4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283825"
---
# <a name="define-global-access-control"></a>Définir le contrôle d’accès global

Dans les grandes organisations, les autorisations utilisateur peuvent être complexes et peuvent être déterminées par une structure organisationnelle globale, en plus du site standard et de la structure de zone.

Pour prendre en charge la demande d’autorisations d’accès utilisateur globales et plus complexes, vous pouvez créer une topologie d’entreprise globale basée sur les divisions, les régions et les sites. Vous pouvez ensuite définir des autorisations d’accès utilisateur autour de ces entités.

L’utilisation des outils d’accès pour la topologie d’entreprise permet aux organisations d’implémenter des stratégies de « confiance zéro » en contrôlant mieux les emplacements où les utilisateurs gèrent et analysent des appareils dans la plateforme Microsoft Defender pour IoT.

## <a name="about-access-groups"></a>À propos des groupes d’accès

Le contrôle d’accès global est établi par le biais de la création de groupes d’accès utilisateur. Les groupes d’accès sont composés de règles déterminant les utilisateurs qui peuvent accéder à des entités spécifiques de l’entreprise. L’utilisation de groupes vous permet de contrôler l’accès à l’affichage et à la configuration de Defender pour IoT pour des rôles d’utilisateur spécifiques dans les divisions, régions et sites appropriés.

Par exemple, vous pouvez autoriser les analystes de la sécurité d’un groupe Active Directory à accéder à toutes les lignes de production de verre et d’automobile dans la région Europe Ouest, ainsi qu’à une autre ligne dédiée au plastique dans une région donnée.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="Diagramme du groupe Active Directory de l’analyste de la sécurité.":::

Avant de créer des groupes d’accès, nous vous recommandons d’effectuer les opérations suivantes :

- Configurez soigneusement votre topologie d’entreprise. Pour plus d’informations sur la topologie d’entreprise, consultez [Utiliser les vues de plan de site](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views).

- Définissez quels utilisateurs sont associés aux groupes d’accès que vous créez. Deux options sont disponibles pour affecter des utilisateurs aux groupes d’accès :

  - **Affecter des groupes de groupes Azure Active Directory** : vous devez avoir configuré une instance Active Directory à intégrer à la console de gestion locale.
  
  - **Affecter des utilisateurs locaux** : vous devez avoir créé des utilisateurs. Pour plus d’informations, consultez [Définir des utilisateurs](how-to-create-and-manage-users.md#define-users).

Les utilisateurs administrateurs ne peuvent pas être affectés à des groupes d’accès. Ces utilisateurs ont accès à toutes les entités de la topologie d’entreprise par défaut.

## <a name="create-access-groups"></a>Créer des groupes d’accès

Cette section décrit la création de groupes d’accès. Les divisions et les régions globales par défaut sont créées pour le premier groupe que vous créez. Vous pouvez modifier les entités par défaut lorsque vous définissez votre premier groupe.

Pour créer des groupes :

1. Sélectionnez **Groupes d’accès** dans le menu latéral de la console de gestion locale.

2. Sélectionnez :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::. Dans la boîte de dialogue **Ajouter un groupe d’accès**, entrez un nom pour le groupe d’accès. La console accepte 64 caractères. Choisissez un nom qui vous permette de reconnaître facilement le groupe.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Boîte de dialogue Ajouter un groupe d’accès dans laquelle vous créez des groupes d’accès.":::

3. Si l’option **Affecter un groupe Active Directory** apparaît, vous pouvez affecter un groupe d’utilisateurs Active Directory à ce groupe d’accès.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Affecter un groupe Active Directory dans la boîte de dialogue Créer un groupe d’accès.":::

   Si vous ne voyez pas cette option et que vous souhaitez inclure des groupes Active Directory dans des groupes d’accès, sélectionnez **Paramètres système**. Dans le volet **Intégrations**, définissez les groupes. Entrez un nom de groupe tel qu’il apparaît exactement dans les configurations Active Directory et en minuscules.

5. Dans le volet **Utilisateurs**, affectez autant d’utilisateurs que nécessaire au groupe. Vous pouvez également affecter des utilisateurs à différents groupes. Si vous utilisez cette méthode, vous devez d’abord créer et enregistrer le groupe d’accès et les règles, puis affecter des utilisateurs au groupe à partir du volet **Utilisateurs**.

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="Gérez les rôles de vos utilisateurs et affectez-les en fonction des besoins.":::

6. Créez des règles dans la boîte de dialogue **Ajouter des règles pour *nom*** en fonction des exigences de votre topologie d’entreprise en termes d’accès. Les options qui s’affichent ici sont basées sur la topologie que vous avez conçue dans les fenêtres **Vue Entreprise** et **Gestion de site**. 

   Vous pouvez créer plusieurs règles par groupe. Vous devrez peut-être créer plusieurs règles par groupe si vous utilisez une granularité d’accès complexe sur plusieurs sites. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="Ajouter une règle à votre système.":::

Les règles que vous créez s’affichent dans la boîte de dialogue **Ajouter un groupe d’accès**, dans laquelle vous pouvez également les supprimer ou les modifier.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="Affichez et modifiez tous vos groupes d’accès dans cette fenêtre.":::

### <a name="about-rules"></a>À propos des règles

Prenez en compte les informations suivantes lorsque vous créez des règles :

- Lorsqu’un groupe d’accès contient plusieurs règles, la logique de la règle regroupe toutes les règles. Par exemple, les règles utilisent la logique ET, et non la logique OU.

- Pour qu’une règle soit appliquée correctement, vous devez affecter des capteurs à des zones dans la fenêtre **Gestion de site**.

- Vous ne pouvez affecter qu’un seul élément par règle. Par exemple, vous pouvez affecter une division, une région et un site pour chaque règle. Créez d’autres règles pour le groupe si par exemple vous souhaitez que les utilisateurs d’un groupe Active Directory aient accès à différentes divisions dans différentes régions.

- Si vous modifiez une entité et que la modification affecte la logique de la règle, la règle sera supprimée. Si les modifications apportées à une entité de topologie affectent la logique de la règle de telle sorte que toutes les règles sont supprimées, le groupe d’accès est conservé mais les utilisateurs ne peuvent pas se connecter à la console de gestion locale. Les utilisateurs sont invités à contacter leur administrateur pour se connecter.

- Si aucune division ou région n’est sélectionnée, les utilisateurs auront accès à toutes les divisions et régions définies.

## <a name="see-also"></a>Voir aussi

[À propos des utilisateurs de la console Defender pour IoT](how-to-create-and-manage-users.md)
