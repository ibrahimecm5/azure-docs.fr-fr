---
title: Configurer la surveillance des points de terminaison Windows
description: Enrichissez les données résolues sur les appareils en utilisant la surveillance des points de terminaison Windows (WMI).
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 7b59c0b5dcb14b57ee5e4e12c2dc3e925e30d37d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281832"
---
# <a name="configure-windows-endpoint-monitoring-wmi"></a>Configurer la surveillance des points de terminaison Windows (WMI)

Grâce à la capacité de surveillance des points de terminaison Windows, vous pouvez configurer Microsoft Defender pour IoT afin que la solution sonde de manière sélective les systèmes Windows. Vous disposez ainsi d’informations plus ciblées et précises sur vos appareils, telles que les niveaux de Service Pack.

Vous pouvez configurer la détection avec des plages et des hôtes spécifiques et la configurer pour qu’elle soit exécutée aussi souvent que vous le souhaitez. Effectuez une détection sélective à l’aide de Windows Management Instrumentation (WMI), le langage standard de script de Microsoft pour la gestion des systèmes Windows.

> [!NOTE]
> - Vous ne pouvez exécuter qu’une seule analyse à la fois.
> - Le résultat est optimal avec les utilisateurs disposant de privilèges d’administrateur local ou de domaine.
> - Avant de commencer la configuration de WMI, configurez une règle de pare-feu qui ouvre le trafic sortant du capteur vers le sous-réseau analysé à l’aide du port UDP 135 et de tous les ports TCP au-dessus de 1024.

Lorsque la détection est terminée, un fichier journal contenant toutes les tentatives est disponible à partir de l’option permettant d’exporter un journal. Le journal contient toutes les adresses IP qui ont été sondées. Pour chaque adresse IP, le journal affiche les informations relatives aux réussites et aux échecs. Il y a également un code d’erreur, qui est une chaîne gratuite dérivée de l’exception. Le système conserve uniquement l’analyse du dernier journal.

Vous pouvez effectuer des analyses planifiées ou des analyses manuelles. Une fois une analyse terminée, vous pouvez afficher les résultats dans un fichier CSV.

**Composants requis**

Configurez une règle de pare-feu qui ouvre le trafic sortant du capteur vers le sous-réseau analysé à l’aide du port UDP 135 et de tous les ports TCP au-dessus de 1024.

## <a name="perform-an-automatic-scan"></a>Effectuer une analyse automatique

Cette section décrit comment effectuer une analyse automatique.

**Pour effectuer une analyse automatique :**

1. Dans le menu latéral, sélectionnez **Paramètres système**.

2. Sélectionnez **Surveillance des points de terminaison Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false":::.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Capture d’écran illustrant la sélection de Surveillance des points de terminaison Windows.":::

3. Dans le volet **Planifier l’analyse**, configurez les options comme suit :

      - **Par intervalles fixes (en heures)** : planifiez l’analyse en respectant des intervalles en heures.

      - **Par heures précises** : planifiez l’analyse en respectant des heures précises et sélectionnez **Enregistrer l’analyse**.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="Capture d’écran montrant le bouton Enregistrer l’analyse.":::

4. Pour définir la plage d’analyse, sélectionnez **Définir des plages d’analyse**.

5. Définissez la plage d’adresses IP et ajoutez un utilisateur et votre mot de passe.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="Capture d’écran qui illustre l’ajout d’un nouvel utilisateur et d’un mot de passe.":::

6. Pour exclure une plage d’adresses IP d’une analyse, sélectionnez **Désactiver** à côté de la plage.

7. Pour supprimer une plage, sélectionnez :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: à côté de la plage.

8. Sélectionnez **Enregistrer**. La boîte de dialogue **Modifier la configuration des plages d’analyse** se ferme et le nombre de plages s’affiche dans le volet **Plages d’analyse**.

## <a name="perform-a-manual-scan"></a>Effectuer une analyse manuelle

**Pour effectuer une analyse manuelle :**

1. Dans le menu latéral, sélectionnez **Paramètres système**.

2. Sélectionnez **Surveillance des points de terminaison Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false":::.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Capture de l’écran de configuration de la surveillance des points de terminaison Windows.":::

3. Dans le volet **Actions**, sélectionnez **Démarrer la sauvegarde**. Une barre d’état apparaît dans le volet **Actions** et affiche la progression du processus d’analyse.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="Capture d’écran du bouton Enregistrer l’analyse.":::

## <a name="view-scan-results"></a>Afficher les résultats de l’analyse

**Pour afficher les résultats de l’analyse :**

1. Une fois l’analyse terminée, dans le volet **Actions**, sélectionnez **Afficher les résultats de l’analyse**. Le fichier CSV contenant les résultats de l’analyse est téléchargé sur votre ordinateur.
