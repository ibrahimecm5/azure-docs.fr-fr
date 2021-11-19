---
title: Analyse de l’intégrité des fichiers dans Microsoft Defender pour le cloud
description: Découvrez comment configurer la Supervision de l’intégrité des fichiers (FIM) dans Microsoft Defender pour le cloud à l’aide de cette procédure pas à pas.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 8ceb46b1a70397d73379df3f2d9f1027feabf49b
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526379"
---
# <a name="file-integrity-monitoring-in-microsoft-defender-for-cloud"></a>Analyse de l’intégrité des fichiers dans Microsoft Defender pour le cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Découvrez comment configurer la Supervision de l’intégrité des fichiers (FIM) dans Microsoft Defender pour le cloud à l’aide de cette procédure pas à pas.


## <a name="availability"></a>Disponibilité

|Aspect|Détails|
|----|:----|
|État de sortie :|Disponibilité générale|
|Prix :|Nécessite [Microsoft Defender pour les serveurs](defender-for-servers-introduction.md).<br>À l’aide de l’agent Log Analytics, FIM charge des données dans l’espace de travail Log Analytics. Des frais de données seront appliqués en fonction de la quantité de données que vous téléchargez. Pour en savoir plus, consultez l’article [Tarification - Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).|
|Rôles et autorisations obligatoires :|Le **propriétaire de l’espace de travail** peut activer/désactiver FIM (pour plus d’informations, consultez [Rôles Azure pour Log Analytics](/services-hub/health/azure-roles#azure-roles)).<br>Le **lecteur** peut visualiser les résultats.|
|Clouds :|:::image type="icon" source="./media/icons/yes-icon.png"::: Clouds commerciaux<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National (Azure Government, Azure China 21Vianet)<br>Pris en charge uniquement dans les régions où la solution de suivi des modifications d’Azure Automation est disponible.<br>:::image type="icon" source="./media/icons/yes-icon.png":::Appareils avec [Azure Arc](../azure-arc/servers/overview.md).<br>Consultez [Régions prises en charge pour l’espace de travail Log Analytics lié](../automation/how-to/region-mappings.md).<br>[En savoir plus sur le suivi des modifications](../automation/change-tracking/overview.md).|
|||

## <a name="what-is-fim-in-defender-for-cloud"></a>Qu’est-ce que FIM dans Defender pour le cloud ?
Le monitoring d’intégrité de fichier (FIM), également appelé Monitoring des modifications, recherche les modifications qui sont apportées aux fichiers du système d’exploitation, registres Windows, logiciels d’application, fichiers du système Linux et bien d’autres encore, et qui peuvent indiquer une attaque. 

Defender pour le cloud recommande des entités à surveiller avec FIM, et vous pouvez également définir vos propres stratégies ou entités FIM à surveiller. FIM vous informe des activités suspectes telles que :

- La création ou la suppression de fichiers et de clés de Registre
- Les modifications de fichiers (modifications apportées à la taille du fichier, aux listes de contrôle d’accès et au hachage du contenu)
- Les modifications de registre (modifications apportées à la taille du registre, aux listes de contrôle d’accès, au type d’entrées et au contenu)

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Passer en revue la liste des entités suggérées à surveiller avec FIM
> * Définir vos propres règles FIM personnalisées
> * Auditer les modifications apportées à vos entités surveillées
> * Utiliser des caractères génériques pour simplifier le suivi entre les répertoires


## <a name="how-does-fim-work"></a>Comment FIM fonctionne-t-il ?

L’agent Log Analytics charge des données dans l’espace de travail Log Analytics. FIM compare l’état actuel de ces éléments avec celui identifié pendant l’analyse précédente et vous indique si des modifications suspectes ont été apportées.

La fonctionnalité FIM utilise la solution Azure Change Tracking pour identifier les modifications apportées dans votre environnement. Quand la fonctionnalité FIM est activée, vous disposez d’une ressource **Change Tracking** de type **Solution**. Pour plus d’informations sur la fréquence de collecte de données, consultez [Détails de la collecte de données de suivi des modifications](../automation/change-tracking/overview.md#change-tracking-and-inventory-data-collection).

> [!NOTE]
> Si vous supprimez la ressource **Change Tracking**, vous désactivez également la fonctionnalité FIM dans Defender pour le cloud.

## <a name="which-files-should-i-monitor"></a>Quels fichiers dois-je surveiller ?

Lors de la sélection des fichiers à surveiller, pensez aux fichiers essentiels au fonctionnement de votre système et de vos applications. Surveillez des fichiers qui ne sont pas susceptibles d’être modifiés sans planification. Si vous choisissez des fichiers qui sont fréquemment modifiés par des applications ou le système d’exploitation (par exemple, les fichiers journaux et les fichiers texte), cela crée une surcharge et compromet la détection des attaques.

Defender pour le cloud fournit la liste suivante d’éléments recommandés à surveiller en fonction de modèles d’attaque connus.

|Fichiers Linux|Fichiers Windows|Clés de Registre Windows (HKLM = HKEY_LOCAL_MACHINE)|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*.conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini\boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Program Files\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini\boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>Activer le Monitoring d’intégrité de fichier 

FIM est uniquement disponible à partir des pages Defender pour le cloud dans le portail Azure. Il n’existe actuellement aucune API REST pour l’utilisation de FIM.

1. Dans la zone **Protection avancée** du tableau de bord **Protections de charge de travail**, sélectionnez **Analyse de l’intégrité du fichier**.

   :::image type="content" source="./media/file-integrity-monitoring-overview/open-file-integrity-monitoring.png" alt-text="Lancement de FIM." lightbox="./media/file-integrity-monitoring-overview/open-file-integrity-monitoring.png":::

    La page de configuration **Analyse de l’intégrité du fichier** apparaît.

    Chaque espace de travail contient les informations suivantes :

    - Nombre total de modifications apportées au cours de la dernière semaine (un trait d’union « - » apparaît lorsque la fonctionnalité FIM n’est pas activée dans l’espace de travail)
    - Nombre total d’ordinateurs et de machines virtuelles envoyant des rapports à l’espace de travail
    - Emplacement géographique de l’espace de travail
    - Abonnement Azure auquel appartient l’espace de travail

1. Utilisez cette page pour :

    - Accéder à l’état et aux paramètres de chaque espace de travail, et y accéder

    - ![Icône Modifier le plan.][4] Mettez à niveau l’espace de travail pour utiliser les fonctionnalités de sécurité améliorées. Cette icône indique que l’espace de travail ou l’abonnement n’est pas protégé par Microsoft Defender pour les serveurs. Pour utiliser les fonctionnalités FIM, votre abonnement doit être protégé par ce plan. Pour plus d’informations, consultez [Fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud](enhanced-security-features-overview.md).

    - ![Icône Activer][3] Activez FIM sur tous les ordinateurs de l’espace de travail et configurez les options FIM. Cette icône indique que la fonctionnalité FIM n’est pas activée pour l’espace de travail.

        :::image type="content" source="./media/file-integrity-monitoring-overview/workspace-list-fim.png" alt-text="Activation de FIM pour un espace de travail spécifique.":::


    > [!TIP]
    > S’il n’existe pas de bouton Activer ou Mettre à niveau et que l’espace est vide, cela signifie que FIM est déjà activé sur l’espace de travail.


1. Sélectionnez **ACTIVER**. Les détails de l’espace de travail, notamment le nombre d’ordinateurs Windows et Linux sous l’espace de travail, s’affichent.

    :::image type="content" source="./media/file-integrity-monitoring-overview/workspace-fim-status.png" alt-text="Page Détails de l’espace de travail FIM.":::

   Les paramètres recommandés pour Windows et Linux sont également affichés.  Développez les champs **Fichiers Windows**, **Registre** et **fichiers Linux** pour afficher la liste complète des éléments recommandés.

1. Désactivez les cases à cocher pour les entités recommandées que vous ne souhaitez pas analyser par FIM.

1. Sélectionnez **Appliquer le Monitoring d’intégrité de fichier** pour activer la fonctionnalité FIM.

> [!NOTE]
> Vous pouvez modifier les paramètres à tout moment. Consultez la section [Modifier des entités surveillées](#edit-monitored-entities) ci-dessous pour en savoir plus.



## <a name="audit-monitored-workspaces"></a>Auditer les espaces de travail analysés 

Le tableau de bord **Monitoring d’intégrité de fichier** affiche les espaces de travail sur lesquels la fonctionnalité FIM est activée. Le tableau de bord FIM s’ouvre lorsque vous avez activé la fonctionnalité FIM dans un espace de travail ou que vous sélectionnez un espace de travail dans la fenêtre **monitoring d’intégrité de fichier** pour lequel la fonctionnalité est activée.

:::image type="content" source="./media/file-integrity-monitoring-overview/fim-dashboard.png" alt-text="Tableau de bord FIM et ses différents panneaux d’information.":::

Le tableau de bord FIM d’un espace de travail affiche les détails suivants :

- Nombre total d’ordinateurs connectés à l’espace de travail
- Nombre total de modifications apportées au cours de la période sélectionnée
- Détails des types de modification (fichiers, registre)
- Détails des catégories de modification (modification, ajout, suppression)

Sélectionnez **Filtrer** en haut du tableau de bord pour modifier la période pendant laquelle les modifications sont affichées.

:::image type="content" source="./media/file-integrity-monitoring-overview/dashboard-filter.png" alt-text="Filtre période pour le tableau de bord FIM.":::

L’onglet **Serveurs** répertorie les ordinateurs qui sont en rapport avec cet espace de travail. Pour chaque ordinateur, le tableau de bord affiche :

- Le nombre total de modifications apportées au cours de la période sélectionnée
- Une répartition des modifications selon leur type (modifications de fichier ou de registre)

Lorsque vous sélectionnez un ordinateur, la requête apparaît avec les résultats qui identifient les modifications apportées au cours de la période sélectionnée pour l'ordinateur. Vous pouvez développer chaque modification pour afficher des informations supplémentaires.

:::image type="content" source="./media/file-integrity-monitoring-overview/query-machine-changes.png" alt-text="Requête Log Analytics montrant les modifications identifiées par la surveillance de l’intégrité des fichiers de Microsoft Defender pour le cloud" lightbox="./media/file-integrity-monitoring-overview/query-machine-changes.png":::

L’onglet **Modifications** (illustré ci-dessous) répertorie toutes les modifications associées à l’espace de travail pendant la période sélectionnée. Pour chaque entité qui a été modifiée, le tableau de bord affiche les informations suivantes :

- L'ordinateur sur lequel la modification a été apportée
- Le type de modification (registre ou fichier)
- La catégorie de modification (modification, ajout, suppression)
- La date et l’heure de modification

:::image type="content" source="./media/file-integrity-monitoring-overview/changes-tab.png" alt-text="Onglet Modifications de l’analyse de l’intégrité des fichiers de Microsoft Defender pour le cloud" lightbox="./media/file-integrity-monitoring-overview/changes-tab.png":::

La fenêtre **Détails des modifications** s’ouvre lorsque vous saisissez une modification dans le champ de recherche ou que vous sélectionnez une entité répertoriée dans l’onglet **Modifications**.

:::image type="content" source="./media/file-integrity-monitoring-overview/change-details.png" alt-text="Surveillance de l’intégrité des fichiers Microsoft Defender pour le cloud, montrant le volet d’informations pour une modification" lightbox="./media/file-integrity-monitoring-overview/change-details.png":::

## <a name="edit-monitored-entities"></a>Modifier des entités surveillées

1. Dans le **tableau de bord de la fonctionnalité FIM** d'un espace de travail, sélectionnez **Paramètres** sur la barre d'outils. 

    :::image type="content" source="./media/file-integrity-monitoring-overview/file-integrity-monitoring-dashboard-settings.png" alt-text="Accès à Paramètres dans le tableau de bord de la fonctionnalité FIM d'un espace de travail." lightbox="./media/file-integrity-monitoring-overview/file-integrity-monitoring-dashboard-settings.png":::

   La fenêtre **Configuration de l'espace de travail** s'ouvre avec des onglets pour chaque type d'élément qui peut être surveillé :

      - Registre Windows
      - Fichiers Windows
      - Fichiers Linux
      - le contenu d’un fichier ;
      - Services Windows

      Chaque onglet répertorie les entités que vous pouvez modifier dans cette catégorie. Pour chaque entité répertoriée, Defender pour le cloud identifie si la fonctionnalité FIM est activée (true) ou désactivée (false). Modifiez l’entité pour activer ou désactiver la fonctionnalité FIM.

    :::image type="content" source="./media/file-integrity-monitoring-overview/file-integrity-monitoring-workspace-configuration.png" alt-text="Configuration de l’espace de travail pour la surveillance de l’intégrité des fichiers dans Microsoft Defender pour le cloud.":::

1. Sélectionnez une entrée dans l'un des onglets et modifiez les champs disponibles dans le volet **Modifier pour Change Tracking**. Les options sont les suivantes :

    - Activer (True) ou désactiver (False) le Monitoring d’intégrité de fichier
    - Saisir ou modifier le nom de l’entité
    - Saisir ou modifier la valeur ou le chemin d’accès
    - Supprimer l'entité

1. Ignorez ou enregistrez vos modifications.


## <a name="add-a-new-entity-to-monitor"></a>Ajouter une nouvelle entité à surveiller

1. Dans le **tableau de bord de la fonctionnalité FIM** d'un espace de travail, sélectionnez **Paramètres** sur la barre d'outils. 

    La fenêtre **Configuration de l'espace de travail** s'ouvre.

1. Dans la fenêtre **Configuration de l'espace de travail** :

    1. Sélectionnez l'onglet correspondant au type d'entité que vous souhaitez ajouter : Registre Windows, fichiers Windows, fichiers Linux, contenu de fichiers, ou services Windows. 
    1. Sélectionnez **Ajouter**. 

        Dans cet exemple, nous avons sélectionné **Fichiers Linux**.

        :::image type="content" source="./media/file-integrity-monitoring-overview/file-integrity-monitoring-add-element.png" alt-text="Ajout d’un élément à surveiller dans la surveillance de l’intégrité des fichiers de Microsoft Defender pour le cloud" lightbox="./media/file-integrity-monitoring-overview/file-integrity-monitoring-add-element.png":::

1. Sélectionnez **Ajouter**. La fenêtre **Ajout pour Change Tracking** s’affiche.

1. Entrez les informations nécessaires, puis sélectionnez **Enregistrer**.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Supervision de dossiers et chemins d’accès à l’aide de caractères génériques

Utilisez des caractères génériques pour simplifier le suivi au sein des répertoires. Les règles suivantes s’appliquent lorsque vous configurez la supervision d’un dossier à l’aide de caractères génériques :
-   Les caractères génériques sont requis pour effectuer le suivi de plusieurs fichiers.
-   Les caractères génériques ne peuvent être utilisés que dans le dernier segment d’un chemin d’accès, comme C:\folder\file ou /etc/*.conf
-   Si le chemin d’accès d’une variable d’environnement n’est pas valide, la validation réussit, mais le chemin d’accès échoue lors de l’exécution de l’inventaire.
-   Lors de la définition du chemin d’accès, évitez les chemins d’accès généraux comme c:\*.*, sinon un trop grand nombre de dossiers sont parcourus.

## <a name="disable-fim"></a>Désactiver la fonctionnalité FIM
Vous pouvez désactiver la fonctionnalité FIM. La fonctionnalité FIM utilise la solution Azure Change Tracking pour identifier les modifications apportées dans votre environnement. En la désactivant, vous supprimez la solution Change Tracking de l’espace de travail sélectionné.

Pour désactiver la fonctionnalité FIM :

1. Dans le **tableau de bord de la fonctionnalité FIM** d'un espace de travail, sélectionnez **Désactiver**.

    :::image type="content" source="./media/file-integrity-monitoring-overview/disable-file-integrity-monitoring.png" alt-text="Désactivez la fonctionnalité FIM à partir de la page des paramètres.":::

1. Sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à utiliser la fonctionnalité Monitoring d’intégrité de fichier (FIM) dans Defender pour le cloud. Pour en savoir plus sur Defender pour le cloud, consultez les pages suivantes :

* [Définition des stratégies de sécurité](tutorial-security-policy.md) : découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion des recommandations de sécurité](review-security-recommendations.md) : découvrez la façon dont les recommandations peuvent vous aider à protéger vos ressources Azure.
* [Blog sur la sécurité Azure](/archive/blogs/azuresecurity/): découvrez les dernières nouvelles et informations sur la sécurité Azure.

<!--Image references-->
[3]: ./media/file-integrity-monitoring-overview/enable.png
[4]: ./media/file-integrity-monitoring-overview/upgrade-plan.png
