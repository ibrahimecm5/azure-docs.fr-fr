---
title: Renforcement de réseau adaptatif dans Microsoft Defender pour le cloud | Microsoft Docs
description: Découvrez comment utiliser des modèles de trafic réels pour renforcer vos règles de groupes de sécurité réseau (NSG) et améliorer votre position de sécurité.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: b26068a7e8400fa6167dbe91d2e7db039bcc68d9
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526374"
---
# <a name="improve-your-network-security-posture-with-adaptive-network-hardening"></a>Améliorer votre posture de sécurité réseau avec le durcissement de réseau adaptatif

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Le renforcement de réseau adaptatif est une fonctionnalité sans agent de Microsoft Defender pour le cloud : rien ne doit être installé sur vos ordinateurs pour tirer parti de cet outil de renforcement de réseau.

Cette page explique comment configurer et gérer le renforcement de réseau adaptatif dans Defender pour le cloud.

## <a name="availability"></a>Disponibilité
|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|Nécessite [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md)|
|Rôles et autorisations obligatoires :|Autorisations en écriture sur les groupes de sécurité de la machine|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/no-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-is-adaptive-network-hardening"></a>Qu’est-ce que le renforcement du réseau adaptatif ?
L’application de [groupes de sécurité réseau (NSG)](../virtual-network/network-security-groups-overview.md) pour filtrer le trafic vers et depuis des ressources améliore votre posture de sécurité réseau. Il peut toutefois rester des cas dans lesquels le trafic réel qui transite via le groupe de sécurité réseau est un sous-ensemble des règles NSG définies. Dans ces cas, une amélioration supplémentaire de la posture de sécurité est possible en renforçant les règles NSG en fonction des modèles de trafic réel.

Le renforcement du réseau adaptatif fournit des recommandations visant à renforcer davantage les règles NSG. Il utilise un algorithme de Machine Learning factorisé dans le trafic réel, appelé une configuration approuvée, l’intelligence des menaces et d’autres indicateurs de compromis, puis fournit des suggestions pour autoriser uniquement le trafic provenant de tuples IP/port spécifiques.

Par exemple, supposons que la règle NSG existante consiste à autoriser le trafic provenant de 140.20.30.10/24 sur le port 22. En se basant sur l’analyse du trafic, le renforcement de réseau adaptatif peut recommander de limiter la plage pour autoriser le trafic provenant de 140.23.30.10/29 et de refuser tout autre trafic sur ce port. Pour obtenir la liste complète des ports pris en charge, consultez l’entrée FAQ [Quels sont les ports pris en charge ?](#which-ports-are-supported).

## <a name="view-hardening-alerts-and-recommended-rules"></a>Consulter les règles recommandées et les alertes de renforcement

1. Dans le menu de Defender pour le cloud, ouvrez le tableau de bord **Protections des charges de travail**.
1. Sélectionnez la vignette de renforcement de réseau adaptatif (1) ou l’élément du panneau d’insights relatif au renforcement du réseau adaptatif (2). 

    :::image type="content" source="./media/adaptive-network-hardening/traffic-hardening.png" alt-text="Accès aux outils de renforcement de réseau adaptatif." lightbox="./media/adaptive-network-hardening/traffic-hardening.png":::

    > [!TIP]
    > Le panneau des insights affiche le pourcentage de vos machines virtuelles actuellement protégées par le renforcement du réseau adaptatif. 

1. La page de détails de la recommandation **Les recommandations de renforcement de réseau adaptatif doivent être appliquées sur les machines virtuelles accessibles à partir d’Internet** s’ouvre avec vos machines virtuelles réseau regroupées sous trois onglets :
   * **Ressources non saines** : machines virtuelles ayant actuellement des recommandations et des alertes qui ont été déclenchées en exécutant l’algorithme de renforcement du réseau adaptatif. 
   * **Ressources saines** : Machines virtuelles sans alertes ni suggestions.
   * **Ressources non analysées** : machines virtuelles dont l’algorithme de renforcement du réseau adaptatif ne peut pas être exécuté pour l’une des raisons suivantes :
      * **Les machines virtuelles sont des machines virtuelles Classic** : Seules les ressources Azure Resource Manager sont prises en charge.
      * **Les données disponibles sont insuffisantes** : afin de générer des recommandations de renforcement du trafic précises, Defender pour le cloud requiert au moins 30 jours de données de trafic.
      * **La machine virtuelle n’est pas protégée par Microsoft Defender pour les serveurs** : seules les machines virtuelles protégées par [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md) sont éligibles pour cette fonctionnalité.

    :::image type="content" source="./media/adaptive-network-hardening/recommendation-details-page.png" alt-text="Page de détails de la recommandation « Les recommandations de renforcement de réseau adaptatif doivent être appliquées sur les machines virtuelles accessibles sur Internet ».":::

1. Dans l’onglet **Ressources non saines**, sélectionnez une machine virtuelle pour afficher ses alertes et les règles de renforcement recommandées à appliquer.

    - L’onglet **Règles** liste les règles que le renforcement du réseau adaptatif vous recommande d’ajouter.
    - L’onglet **Alertes** répertorie les alertes qui ont été générées en raison de trafic, qui traverse la ressource, et qui ne se trouve pas dans la plages d’adresses IP autorisée dans les règles recommandées.

1. Vous pouvez éventuellement modifier les règles :

    - [Modifier une règle](#modify-rule)
    - [Supprimer une règle](#delete-rule) 
    - [Ajouter une règle](#add-rule)

3. Sélectionnez les règles que vous souhaitez appliquer sur le groupe de sécurité réseau, puis **Appliquer**.

    > [!TIP]
    > Si la valeur « Aucune » est indiquée pour les plages d’adresses IP sources autorisées, cela signifie que la règle recommandée est une règle de *refus* ; sinon, il s’agit d’une règle d’*autorisation*.

    :::image type="content" source="./media/adaptive-network-hardening/hardening-alerts.png" alt-text="Gestion des règles de renforcement de réseau adaptatif.":::

      > [!NOTE]
      > Les règles appliquées sont ajoutées aux groupes de sécurité réseau qui protègent la machine virtuelle. (Une machine virtuelle peut être protégée par un groupe de sécurité réseau qui est associé à sa carte réseau, ou le sous-réseau dans lequel réside la machine virtuelle, ou les deux)

## <a name="modify-a-rule"></a>Modifier une règle <a name ="modify-rule"></a>

Vous souhaiterez peut-être modifier les paramètres d’une règle qui a été recommandée. Par exemple, vous souhaiterez modifier les plages d’IP recommandées.

Voici des instructions importantes relatives à la modification d’une règle de renforcement du réseau adaptatif :

- Vous ne pouvez pas transformer des règles **allow** en règles **deny**. 

- Vous pouvez modifier les paramètres de règles **allow** uniquement. 

    La création et la modification de règles « deny » sont effectuées directement sur le groupe de sécurité réseau. Pour plus d’informations, consultez [Créer, changer ou supprimer un groupe de sécurité réseau](../virtual-network/manage-network-security-group.md).

- Une règle **Deny all traffic** (Refuser tout le trafic) est la seule règle de type « deny » qui serait répertoriée ici, et elle ne peut pas être modifiée. Vous pouvez toutefois la supprimer (consultez [Supprimer une règle](#delete-rule)). Pour en savoir plus sur ce type de règle, consultez l’entrée FAQ [Quand dois-je utiliser une règle « Refuser tout le trafic » ?](#when-should-i-use-a-deny-all-traffic-rule).

Pour modifier une règle de renforcement du réseau adaptatif :

1. Pour modifier certains paramètres d’une règle, sous l’onglet **Règles**, sélectionnez les trois points (…) à la fin de la ligne de la règle, puis **Modifier**.

   ![Modification d’une règle.](./media/adaptive-network-hardening/edit-hard-rule.png)

1. Dans la fenêtre **Modifier une règle**, mettez à jour les détails que vous souhaitez changer, puis sélectionnez **Enregistrer**.

   > [!NOTE]
   > Après avoir sélectionné **Enregistrer**, vous avez correctement modifié la règle. *Toutefois, vous ne l’avez pas appliquée au groupe de sécurité réseau.* Pour l’appliquer, vous devez sélectionner la règle dans la liste, puis sélectionnez **Appliquer** (comme expliqué dans l’étape suivante).

   ![Sélection de l’option Enregistrer.](./media/adaptive-network-hardening/edit-hard-rule3.png)

3. Pour appliquer la règle mise à jour, sélectionnez-la dans la liste, puis cliquez sur **Appliquer**.

    ![Appliquer une règle.](./media/adaptive-network-hardening/enforce-hard-rule.png)

## <a name="add-a-new-rule"></a>Ajouter une nouvelle règle <a name ="add-rule"></a>

Vous pouvez ajouter une règle « allow » qui n’a pas été recommandée par Defender pour le cloud.

> [!NOTE]
> Seules des règles « allow » peuvent être ajoutées ici. Si vous souhaitez ajouter des règles « deny », vous pouvez le faire directement sur le groupe de sécurité réseau. Pour plus d’informations, consultez [Créer, changer ou supprimer un groupe de sécurité réseau](../virtual-network/manage-network-security-group.md).

Pour ajouter une règle de renforcement du réseau adaptatif :

1. Dans la barre d’outils supérieure, sélectionnez **Ajouter une règle**.

   ![Ajouter une règle.](./media/adaptive-network-hardening/add-hard-rule.png)

1. Dans la fenêtre **Nouvelle règle**, entrez les détails et sélectionnez **Ajouter**.

   > [!NOTE]
   > Après avoir sélectionné **Ajouter**, vous avez correctement ajouté la règle et elle est listée avec les autres règles recommandées. Toutefois, vous ne l’avez pas *appliquée* au groupe de sécurité réseau. Pour l’activer, vous devez sélectionner la règle dans la liste, puis **Appliquer** (comme expliqué dans l’étape suivante).

3. Pour appliquer la nouvelle règle, sélectionnez-la dans la liste, puis sélectionnez **Appliquer**.

    ![Appliquer une règle.](./media/adaptive-network-hardening/enforce-hard-rule.png)


## <a name="delete-a-rule"></a>Supprimer une règle <a name ="delete-rule"></a>

Si nécessaire, vous pouvez supprimer une règle recommandée pour la session active. Par exemple, vous pouvez déterminer que l’application d’une règle suggérée est susceptible de bloquer du trafic légitime.

Pour supprimer une règle de renforcement du réseau adaptatif pour votre session actiuelle :

- Sous l’onglet **Règles**, sélectionnez les trois points (…) à la fin de la ligne de la règle, puis **Supprimer**.  

    ![Suppression d’une règle.](./media/adaptive-network-hardening/delete-hard-rule.png)


## <a name="faq---adaptive-network-hardening"></a>FAQ - Renforcement de réseau adaptatif

- [Quels sont les ports pris en charge ?](#which-ports-are-supported)
- [Existe-t-il des prérequis ou des extensions de machine virtuelle nécessaires pour le renforcement de réseau adaptatif ?](#are-there-any-prerequisites-or-vm-extensions-required-for-adaptive-network-hardening)

### <a name="which-ports-are-supported"></a>Quels sont les ports pris en charge ?

Les recommandations de renforcement du réseau adaptatif sont uniquement prises en charge sur les ports spécifiques suivants (pour UDP et TCP) : 

13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379, 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215

### <a name="are-there-any-prerequisites-or-vm-extensions-required-for-adaptive-network-hardening"></a>Existe-t-il des prérequis ou des extensions de machine virtuelle nécessaires pour le renforcement de réseau adaptatif ?

Le renforcement de réseau adaptatif est une fonctionnalité sans agent de Microsoft Defender pour le cloud : rien ne doit être installé sur vos ordinateurs pour tirer parti de cet outil de renforcement de réseau.

### <a name="when-should-i-use-a-deny-all-traffic-rule"></a>Quand utiliser une règle « Refuser tout le trafic » ?

Une règle **Deny all traffic** (Refuser tout le trafic) est recommandée lorsque, suite à l’exécution de l’algorithme, Defender pour le cloud n’identifie pas de trafic qui doit être autorisé en fonction de la configuration de groupe de sécurité réseau existante. Par conséquent, la règle recommandée consiste à refuser tout le trafic vers le port spécifié. Le nom de ce type de règle apparaît comme « *System Generated* » (Généré par le système). Après l’application de cette règle, son nom réel dans le groupe de sécurité réseau sera une chaîne constituée du protocole, de la direction du trafic, de « DENY » et d’un nombre aléatoire.
