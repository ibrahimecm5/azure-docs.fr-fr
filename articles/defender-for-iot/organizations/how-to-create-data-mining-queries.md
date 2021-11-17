---
title: Créer des rapports d’exploration de données
description: Générez des informations complètes et granulaires sur vos appareils réseau dans différentes couches telles que les protocoles, les versions de microprogramme ou les commandes de programmation.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 4da8e9b25c700641f2e6ee49bf44f2e380386897
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284053"
---
# <a name="sensor-data-mining-queries"></a>Requêtes d’exploration de données de capteur

## <a name="about-sensor-data-mining-queries"></a>À propos des requêtes d’exploration de données de capteur

Les outils d’exploration de données vous permettent de générer des informations complètes et granulaires sur vos appareils réseau dans différentes couches. Par exemple, vous pouvez créer une requête basée sur :

- Périodes

- Connexions à Internet

- Ports

- Protocoles

- Versions de microprogramme

- Commandes de programmation

- Inactivité de l’appareil

Vous pouvez affiner le rapport en fonction des filtres. Par exemple, vous pouvez interroger un sous-réseau spécifique dans lequel le microprogramme a été mis à jour.

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="Liste des appareils actifs.":::

Divers outils sont disponibles pour gérer les requêtes. Par exemple, vous pouvez exporter et enregistrer.

> [!NOTE]
> Les administrateurs et les analystes de la sécurité ont accès aux options d’exploration de données.

### <a name="dynamic-updates"></a>Mises à jour dynamiques

Les requêtes d’exploration de données que vous créez sont mises à jour dynamiquement chaque fois que vous les ouvrez. Par exemple :

- Si vous créez un rapport pour les versions de microprogramme sur les appareils du 1er juin et que vous rouvrez le rapport le 10 juin, ce rapport sera mis à jour avec des informations correctes pour le 10 juin.

- Si vous créez un rapport pour détecter les nouveaux appareils découverts au cours des 30 derniers jours à partir du 1er juin et que vous ouvrez le rapport le 30 juin, les résultats s’afficheront pour les 30 derniers jours.

### <a name="data-mining-use-cases"></a>Cas d’utilisation de l’exploration de données

Vous pouvez utiliser des requêtes pour gérer un large éventail de besoins de sécurité pour différentes équipes de sécurité :

- **Réponse aux incidents SOC** : Générez un rapport en temps réel pour vous aider à répondre immédiatement aux incidents. Par exemple, l’exploration de données peut générer un rapport qui liste les appareils qui peuvent nécessiter une mise à jour corrective.

- **Forensique** : Générez un rapport basé sur les données d’historique des rapports d’investigation.

- **Intégrité du réseau informatique** : Générez un rapport qui contribue à améliorer la sécurité globale du réseau. Par exemple, un rapport peut être généré et répertorier les appareils ayant des informations d’authentification faibles.

- **Visibilité** : Générez un rapport qui couvre tous les éléments de requête pour afficher tous les paramètres de ligne de base de votre réseau.

- **Sécurité du contrôleur PLC** Améliorez la sécurité en détectant les PLC qui se trouvent dans des états non sécurisés, par exemple dans les états de programme ou les états distants.

## <a name="data-mining-storage"></a>Stockage de l’exploration de données

Les informations d’exploration de données sont enregistrées et stockées en continu, sauf lorsqu’un appareil est supprimé. Les résultats d’exploration de données peuvent être exportés et stockés en externe sur un serveur sécurisé. En outre, le capteur effectue des sauvegardes quotidiennes automatiques pour garantir la continuité du système et la préservation des données.

## <a name="predefined-data-mining-queries"></a>Requêtes d’exploration de données prédéfinies

Les requêtes prédéfinies suivantes sont disponibles. Ces requêtes sont générées en temps réel.

- **CVE** : Liste des appareils détectés avec des vulnérabilités connues au cours des dernières 24 heures.

- **CVE exclus** : Liste de tous les CVE qui ont été exclus manuellement. Pour obtenir des résultats plus précis dans les rapports VA et les vecteurs d’attaque, vous pouvez personnaliser manuellement la liste des CVE en incluant et en excluant des CVE.

- **Activité Internet** : Appareils connectés à Internet.

- **Appareils inactifs** : Appareils qui n’ont pas communiqué au cours des sept derniers jours.

- **Appareils actifs** : Appareils réseau actifs au cours des dernières 24 heures.

- **Accès à distance** : Appareils qui communiquent par le biais des protocoles de session à distance.

- **Commandes de programmation** : Appareils qui envoient la programmation industrielle.

Ces rapports sont automatiquement accessibles à partir de l’écran **Rapports**, où les utilisateurs en lecture seule et les autres utilisateurs peuvent les afficher. Les utilisateurs en lecture seule ne peuvent pas accéder aux rapports d’exploration de données.

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Écran d’exploration de données.":::

## <a name="create-a-data-mining-query"></a>Créer une requête d’exploration de données

Pour créer un rapport d’exploration de données :

1. Sélectionnez **Exploration de données** dans le menu latéral. Les rapports suggérés prédéfinis s’affichent automatiquement.

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Sélectionnez l’exploration de données à partir du volet latéral.":::

2. Sélectionnez :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::.

3. Sélectionnez **Nouveau rapport** et définissez le rapport.

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="Créez un nouveau rapport en complétant cet écran.":::

   Les paramètres suivants sont disponibles :

   - Fournissez un nom et une description pour le rapport.

   - Pour les catégories, sélectionnez l’une des catégories suivantes :

      - **Catégories (tout)** pour afficher tous les résultats de rapport sur tous les appareils de votre réseau.

      - **Générique** pour choisir des catégories standard.

   - Sélectionnez des paramètres de rapport spécifiques qui vous intéressent.

   - Choisissez un ordre de tri (**Trier par**). Triez les résultats en fonction de l’activité ou de la catégorie.

   - Sélectionnez **Enregistrer dans les pages de rapport** pour enregistrer les résultats de rapport sous la forme d’un rapport accessible à partir de la page **Rapport**. Cela permettra aux utilisateurs en lecture seule d’exécuter le rapport que vous avez créé.

   - Sélectionnez **Filtres (Ajouter)** pour ajouter d’autres filtres. Les demandes génériques sont prises en charge.

   - Spécifiez un groupe d’appareils (défini dans la table des appareils).

   - Spécifiez une adresse IP.

   - Spécifiez un port.

   - Spécifiez une adresse MAC.

4. Sélectionnez **Enregistrer**. Les résultats du rapport s’ouvrent sur la page **Exploration de données**.

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="Les résultats du rapport tels que vus sur la page Exploration de données.":::

### <a name="manage-data-mining-reports"></a>Gérer les rapports d’exploration de données

Le tableau suivant décrit les options de gestion pour l’exploration de données :

| Image d'icône | Description |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | Modifiez les paramètres de rapport. |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | Exportez au format PDF. |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |Exportez au format CSV. |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | Affichez des informations supplémentaires telles que la date de dernière modification. Utilisez cette fonctionnalité pour créer un instantané des résultats de la requête. Vous devrez peut-être le faire pour approfondir vos investigations auprès des chefs d’équipe ou des analystes SOC, par exemple. |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | Affichez sur la page **Rapports** ou masquez sur la page **Rapports**. :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="Masquez ou affichez vos rapports."::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | Supprimez le rapport. |

#### <a name="create-customized-directories"></a>Créer des répertoires personnalisés 

Vous pouvez organiser les informations étendues pour les requêtes d’exploration de données en créant des répertoires pour les catégories. Par exemple, vous pouvez créer des répertoires pour les protocoles ou les emplacements.

Pour créer un répertoire :

1. Sélectionnez :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: pour ajouter un nouveau répertoire.

2. Sélectionnez **Nouveau répertoire** pour afficher le nouveau formulaire de répertoire.

3. Nommez le nouveau répertoire.

4. Faites glisser les rapports requis dans le nouveau répertoire. À tout moment, vous pouvez faire glisser le rapport vers la vue principale.

5. Cliquez avec le bouton droit sur le nouveau répertoire pour l’ouvrir, le modifier ou le supprimer.

#### <a name="create-snapshots-of-report-results"></a>Créer des instantanés de résultats de rapport

Vous devrez peut-être enregistrer certains résultats de requête pour approfondir votre investigation. Par exemple, vous devrez peut-être partager les résultats avec différentes équipes de sécurité.

Les instantanés sont enregistrés dans les résultats du rapport et ne génèrent pas de requêtes dynamiques.

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="Instantanés.":::

Pour créer un instantané :

1. Ouvrez le rapport requis.

2. Sélectionnez l’icône d’informations :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false":::.

3. Sélectionnez **Prendre un nouveau**.

4. Entrez un nom pour l’instantané et sélectionnez **Enregistrer**.

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="Prenez un instantané.":::

#### <a name="customize-the-cve-list"></a>Personnaliser la liste CVE

Vous pouvez personnaliser manuellement la liste CVE comme suit :

  - Inclure/exclure des CVE

  - Modifier le score de CVE

Pour effectuer des modifications manuelles dans le rapport CVE :

1.  Dans le menu latéral, sélectionnez **Exploration de données**.

2. Sélectionnez :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: dans l’angle supérieur gauche de la fenêtre **Exploration de données**. Sélectionnez ensuite **Nouveau rapport**.

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="Créez un nouveau rapport.":::

3. Dans le volet gauche, sélectionnez l’une des options suivantes :

   - **Vulnérabilités connues** : Sélectionne les deux options et présente les résultats dans les deux tables du rapport, une avec les CVE et l’autre avec les CVE exclus.

   - **CVE** : Sélectionnez cette option pour présenter une liste de tous les CVE.

   - **CVE exclus** : Sélectionnez cette option pour afficher une liste de tous les CVE exclus.

4. Renseignez le **Nom** et la **Description**, puis sélectionnez **Enregistrer**. Le nouveau rapport s’affiche dans la fenêtre **Exploration de données**.

5. Pour exclure les CVE, ouvrez le rapport d’exploration de données pour les CVE. La liste de tous les CVE s’affiche.

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="Rapport C V E.":::

6. Pour activer la sélection des éléments dans la liste, sélectionnez :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: et sélectionnez les CVE que vous souhaitez personnaliser. La barre **Operations** s’affiche en bas.

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="Capture d’écran de la barre des opérations d’exploration de données.":::

7. Sélectionnez les CVE que vous souhaitez exclure, puis sélectionnez **Supprimer les enregistrements**. Les CVE que vous avez sélectionnés n’apparaissent pas dans la liste des CVE et s’affichent dans la liste des CVE exclus lorsque vous en générez un.

8. Pour inclure les CVE exclus dans la liste des CVE, générez le rapport pour les CVE exclus et supprimez de cette liste les éléments que vous souhaitez inclure dans la liste des CVE.

9. Sélectionnez les CVE dans lequel vous souhaitez modifier le score, puis sélectionnez **Mettre à jour le score de CVE**.

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="Mettez à jour le score de CVE.":::

10. Entrez le nouveau score et sélectionnez **OK**. Le score mis à jour s’affiche dans les CVE que vous avez sélectionnés.



## <a name="sensor-reports-based-on-data-mining"></a>Rapports de capteur basés sur l’exploration de données

Les rapports standard, accessibles à partir de l’option **Rapports**, sont des rapports d’exploration de données prédéfinis. Ce ne sont pas des requêtes dynamiques telles que celles disponibles dans l’exploration de données, mais une représentation statique des résultats de la requête d’exploration de données.

Les résultats de la requête d’exploration de données ne sont pas disponibles pour les utilisateurs en lecture seule. Les administrateurs et les analystes de la sécurité qui veulent que les utilisateurs en lecture seule puissent accéder aux informations générées par les requêtes d’exploration de données doivent enregistrer les informations en tant que rapport.

Les rapports reflètent les informations générées par les résultats d’une requête d’exploration de données. Cela comprend les rapports d’exploration de données par défaut, qui sont disponibles dans la vue Rapports. Les administrateurs et les analystes de la sécurité peuvent également générer des requêtes d’exploration de données personnalisées et les enregistrer sous forme de rapports. Ces rapports sont également disponibles pour les utilisateurs en lecture seule.

Pour générer un rapport :

1. Sélectionnez **Rapports** dans le menu latéral.

2. Choisissez le rapport requis à afficher. Le choix peut être des rapports **personnalisés** ou **générés automatiquement**, tels que **Commandes de programmation** et **Accès à distance**.

3. Vous pouvez exporter le rapport en sélectionnant l’une des icônes en haut à droite de l’écran :

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: Exportez vers un fichier PDF.

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: Exportez vers un fichier CSV.

> [!NOTE] 
> L’utilisateur en lecture seule peut voir uniquement les rapports créés pour eux.

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="Sélectionnez le rapport à générer.":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="Rapport d’accès à distance généré.":::

## <a name="on-premises-management-console-reports-based-on-data-mining-reports"></a>Rapports de la console de gestion locale basés sur les rapports d’exploration de données

La console de gestion locale vous permet de générer des rapports pour chaque capteur qui y est connecté. Les rapports sont basés sur des requêtes d’exploration de données de capteur effectuées.

Vous pouvez générer les rapports suivants :

- **Appareils actifs (dernières 24 heures)**  : Affiche la liste des appareils présentant une activité réseau sur une période de 24 heures.

- **Appareils inactifs (derniers 7 jours)**  : Affiche la liste des appareils qui n’indiquent aucune activité réseau au cours des sept derniers jours.

- **Commandes de programmation** : Affiche la liste des appareils qui ont envoyé des commandes de programmation au cours des dernières 24 heures.

- **Accès à distance** : Affiche la liste des appareils auxquels les sources distantes ont accédé au cours des dernières 24 heures.

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="Capture d’écran de la vue des rapports.":::

Lorsque vous choisissez le capteur à partir de la console de gestion locale, tous les rapports personnalisés configurés sur ce capteur s’affichent dans la liste des rapports. Pour chaque capteur, vous pouvez générer un rapport par défaut ou un rapport personnalisé configuré sur ce capteur.

Pour générer un rapport :

1. Dans le volet gauche, sélectionnez **Rapports**. La fenêtre **Rapports** apparaît.

2. Dans la liste déroulante **Capteurs**, sélectionnez le capteur pour lequel vous souhaitez générer le rapport.

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="Capture d’écran de la vue des capteurs.":::

3. Dans la liste déroulante de droite, sélectionnez le rapport que vous souhaitez générer.

4. Pour créer un PDF des résultats du rapport, sélectionnez :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false":::.
