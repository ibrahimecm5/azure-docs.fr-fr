---
title: Mettre à jour les données de renseignement sur les menaces
description: Le paquet de données pour le renseignement sur les menaces est fourni avec chaque nouvelle version de Defender pour IoT, ou entre deux versions si nécessaire.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: ac743c9e8c53e40743bda0c50017885f40381971
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293190"
---
# <a name="threat-intelligence-research-and-packages"></a>Recherche et packages de renseignement sur les menaces #
## <a name="overview"></a>Vue d’ensemble ##

Les équipes de sécurité de Microsoft mènent des recherches exclusives sur les menaces et les vulnérabilités des ICS. Ces équipes incluent le centre Microsoft de renseignement sur les menaces (Microsoft Threat Intelligence Center, MSTIC), l’équipe Microsoft de détection et de réponse (Microsoft Detection and Response Team, DART), l’unité des crimes numériques (Digital Crimes Unit, DCU) et Section 52 (experts de domaine IoT/OT/ICS qui effectuent le suivi des programmes malveillants, des campagnes et des adversaires de l’ingénierie à rebours zero-day spécifiques à l’ICS).

Les équipes assurent la détection de sécurité, l’analyse et la réponse aux services et produits de Microsoft :

- Infrastructure et services cloud.
- Produits et appareils traditionnels.
- Ressources internes de l’entreprise.

Les équipes de sécurité bénéficient des avantages suivants :

- Protection contre les menaces connues et pertinentes.
- Insights qui permettent de trier et de classer par ordre de priorité.
- Compréhension du contexte complet des menaces avant d’être concerné par ces dernières.
- Données plus pertinentes, précises et exploitables.

Ces renseignements fournissent des informations contextuelles qui enrichissent l’analyse de la plateforme Microsoft et prennent en charge les services gérés de l’entreprise pour la réponse aux incidents et les enquêtes sur les violations. Les packages de renseignement sur les menaces contiennent des signatures (y compris des signatures de programmes malveillants), des CVE et d’autres contenus de sécurité.

## <a name="when-are-packages-delivered"></a>Quand les packages sont-ils remis ? ##

Les packages de renseignement sur les menaces sont fournis environ une fois par mois, ou plus fréquemment si nécessaire. Les annonces concernant les nouveaux packages sont disponibles à l’adresse suivante : https://techcommunity.microsoft.com/t5/azure-defender-for-iot/bd-p/AzureDefenderIoT.

Vous pouvez également voir le package livré le plus récent à partir de la section **Mise à jour du renseignement sur les menaces** de la page **Mises à jour** de Defender pour IoT sur le Portail Azure.

## <a name="update-threat-intelligence-packages-to-your-sensors"></a>Mettre à jour les packages de renseignement sur les menaces sur vos capteurs ##

Trois options sont disponibles pour mettre à jour les packages de renseignement sur les menaces sur vos capteurs :

- Envoyer (push) automatiquement les packages aux capteurs lorsqu’ils sont distribués par Defender pour IoT.
- Envoyer (push) manuellement le package de renseignement sur les menaces aux capteurs, selon les besoins.
- Télécharger un package, puis le charger sur un ou plusieurs capteurs.

Les utilisateurs disposant des autorisations Lecteur de sécurité Defender pour IoT peuvent envoyer (push) automatiquement et manuellement des packages aux capteurs.

### <a name="automatically-push-threat-intelligence-updates-to-sensors"></a>Envoyer (push) automatiquement les mises à jour de renseignement sur les menaces aux capteurs ###

Les packages de renseignement sur les menaces peuvent être automatiquement mis à jour sur les capteurs *connectés au cloud* dès qu’ils sont publiés par Defender pour IoT. Assurez la mise à jour automatique des packages en intégrant votre capteur connecté au cloud avec l’option **Mises à jour automatiques du renseignement sur les menaces** activée. Pour plus d’informations, consultez [Intégrer un capteur](getting-started.md#onboard-a-sensor).

### <a name="manually-push-threat-intelligence-updates-to-sensors"></a>Envoyer (push) manuellement les mises à jour de renseignement sur les menaces aux capteurs ###

Vos capteurs *connectés au cloud* peuvent être automatiquement mis à jour avec des packages de renseignement sur les menaces. Toutefois, si vous souhaitez adopter une approche plus conservatrice, vous pouvez envoyer (push) des packages aux capteurs à partir de Defender pour IoT uniquement lorsque vous estimez que cela est nécessaire. Vous avez ainsi la possibilité de contrôler le moment où un package est installé, sans avoir à le télécharger, puis à le charger sur vos capteurs.

**Pour envoyer (push) manuellement des packages :**

1. Accédez à la page **Sites et capteurs** de Microsoft Defender pour IoT.
1. Sélectionnez le bouton de sélection (…) pour un capteur, puis sélectionnez **Envoyer (push) la mise à jour du renseignement sur les menaces**. Le champ **État de la mise à jour du renseignement sur les menaces** affiche la progression de la mise à jour.

#### <a name="change-the-threat-intelligence-update-mode"></a>Modifier le mode de mise à jour du renseignement sur les menaces ####

Vous pouvez modifier le mode de mise à jour du renseignement sur les menaces du capteur après l’intégration initiale.

**Pour modifier le mode de mise à jour :**

1. Sélectionnez le bouton de sélection (…) pour un capteur, puis sélectionnez **Modifier**.
1. Activez ou désactivez le bouton bascule **Mises à jour automatiques du renseignement sur les menaces**.

### <a name="download-packages-and-upload-to-sensors"></a>Télécharger les packages et les charger sur les capteurs ###

Les packages peuvent être téléchargés par le Portail Azure et chargés manuellement vers des capteurs individuels. Si la console de gestion locale gère vos capteurs, vous pouvez télécharger des packages de renseignement sur les menaces sur la console de gestion et les envoyer (push) simultanément à plusieurs capteurs.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Téléchargez les mises à jour dans le Portail Azure.":::

Cette option est disponible pour les capteurs *connectés au cloud* et *gérés localement*.

**Pour charger sur un seul capteur :**

1. Accédez à la page **Mises à jour** de Microsoft Defender pour IoT.

2. Téléchargez et enregistrez le package **Renseignement sur les menaces**.

3. Connectez-vous à la console du capteur.

4. Dans le menu latéral, sélectionnez **Paramètres système**.

5. Sélectionnez **Données de renseignement sur les menaces**, puis sélectionnez **Mettre à jour**.

6. Chargez le nouveau package.

**Pour charger simultanément sur plusieurs capteurs :**

1. Accédez à la page **Mises à jour** de Microsoft Defender pour IoT.

2. Téléchargez et enregistrez le package **Renseignement sur les menaces**.

3. Connectez-vous à la console de gestion.

4. Dans le menu latéral, sélectionnez **Paramètres système**.

5. Dans la section **Configuration des moteurs des capteurs**, sélectionnez les capteurs qui doivent recevoir les packages mis à jour.  

6. Dans la section **Sélectionner les données de renseignement sur les menaces**, sélectionnez le signe plus ( **+** ).

7. Chargez le package.

## <a name="review-package-update-status-on-the-sensor"></a>Vérifier l’état de mise à jour du package sur le capteur ##

Les informations sur la version et l’état de mise à jour du package s’affichent dans la section **Paramètres système**, **Renseignement sur les menaces** du capteur.  

## <a name="review-package-information-for-cloud-connected-sensors"></a>Vérifier les informations des packages pour les capteurs connectés au cloud ##

Vérifiez les informations suivantes sur les packages de renseignement sur les menaces pour vos capteurs connectés au cloud :

- Version de package installée
- Mode de mise à jour de Threat Intelligence
- État de la mise à jour du renseignement sur les menaces

Pour vérifiez les informations relatives au renseignement sur les menaces :

1. Accédez à la page **Sites et capteurs** de Microsoft Defender pour IoT.
1. Vérifiez la **version de renseignement sur les menaces** installée sur chaque capteur. Le nom de la version est basé sur le jour où le package a été généré par Defender pour IoT.
1. Vérifiez le **mode de renseignement sur les menaces**. *Automatique* indique que les packages nouvellement disponibles seront automatiquement installés sur les capteurs au fur et à mesure de leur publication par Defender pour IoT. *Manuel* indique que vous pouvez envoyer (push) des packages nouvellement disponibles directement aux capteurs en fonction des besoins.
1. Examinez l’**état de mise à jour du renseignement sur les menaces**. Les états suivants peuvent s’afficher :

- Failed
- En cours
- Mise à jour disponible
- OK

Si les mises à jour du renseignement sur les menaces des capteurs connectés au cloud échouent, passez en revue les informations de connexion dans les colonnes **État du capteur** et **Dernière connexion (UTC)** dans la page **Sites et capteurs**. 

## <a name="see-also"></a>Voir aussi

[Intégrer un capteur](getting-started.md#onboard-a-sensor)

[Gérer les capteurs depuis la console de gestion](how-to-manage-sensors-from-the-on-premises-management-console.md)
