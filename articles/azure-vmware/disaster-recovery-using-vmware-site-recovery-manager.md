---
title: Déployez la récupération d’urgence avec un site VMware Recovery Manager
description: Déployez la récupération d’urgence avec VMware Site Recovery Manager (SRM) dans votre cloud privé Azure VMware Solution.
ms.topic: how-to
ms.date: 10/04/2021
ms.openlocfilehash: 116c7b672874c1e012b4d9abadacb10bc4ca895f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547242"
---
# <a name="deploy-disaster-recovery-with-vmware-site-recovery-manager"></a>Déployez la récupération d’urgence avec un site VMware Recovery Manager

Cet article explique comment implémenter la récupération d’urgence pour les machines virtuelles VMware locales ou les machines virtuelles basées sur Azure VMware Solution.  La solution de cet article utilise la solution [Vmware Site Recovery Manager (SRM)](https://docs.vmware.com/en/Site-Recovery-Manager/index.html) et la réplication vSphere avec Azure VMware Solution. Les instances de serveurs de SRM et de réplication sont déployées sur les sites protégés et de récupération.       

SRM est une solution de récupération d’urgence conçue pour réduire le temps d’arrêt des machines virtuelles dans un environnement Azure VMware Solution en cas de sinistre. SRM automatise et orchestre le basculement et la restauration automatique, garantissant ainsi un temps d’arrêt minimal en cas de sinistre. En outre, les tests sans interruption intégrés garantissent que vos objectifs de temps de récupération sont atteints. En général, SRM simplifie la gestion grâce à l’automatisation et garantit des temps de récupération rapides et extrêmement prévisibles.  

La réplication vSphere est la technologie de réplication basée sur hyperviseur de VMware pour les machines virtuelles vSphere. Il protège les machines virtuelles contre les défaillances partielles ou complètes du site. En outre, elle simplifie la protection de la récupération d’urgence grâce à la réplication indépendante du stockage basée sur les machines virtuelles. La réplication vSphere est configurée sur la base de chaque machine virtuelle, ce qui permet de mieux contrôler les machines virtuelles répliquées.

Cet article explique comment implémenter la récupération d’urgence pour les machines virtuelles VMware locales ou les machines virtuelles basées sur Azure VMware Solution.


## <a name="supported-scenarios"></a>Scénarios pris en charge

SRM vous aide à planifier, tester et exécuter la récupération des machines virtuelles entre un site vCenter Server protégé et un site de vCenter Server de récupération. Vous pouvez utiliser la solution SRM avec Azure VMware Solution avec les deux scénarios de récupération d’urgence suivants : 

- Récupération d'urgence	de VMware sur site vers le cloud privé Azure VMware Solution 
- Azure VMware Solution principale vers la solution de récupération d’urgence du Cloud privé Azure VMware Solution secondaire 

Le diagramme illustre le déploiement de Azure VMware Solution principale dans le scénario de Azure VMware Solution secondaire.

:::image type="content" source="media/vmware-srm-vsphere-replication/vmware-site-recovery-manager-diagram.png" alt-text="Diagramme montrant la solution de récupération d’urgence de la Recovery Manager de Site vmware (SRM) dans Azure VMware Solution." border="false":::

Vous pouvez utiliser SRM pour implémenter différents types de récupération, par exemple :

- La **migration planifiée** commence lorsque les sites de Azure VMware Solution principale et secondaire sont en cours d’exécution et entièrement fonctionnels. Il s’agit d’une migration ordonnée des machines virtuelles du site protégé vers le site de récupération, où aucune perte de données n’est attendue lors de la migration des charges de travail de manière ordonnée. 

- La **récupération d’urgence** à l’aide de SRM peut être appelée lorsque le site de Azure VMware Solution protégé se met hors connexion de façon inattendue. Site Recovery Manager orchestre le processus de récupération avec les mécanismes de réplication pour réduire la perte de données et les temps d’arrêt du système.

   Dans Azure VMware Solution, seules les machines virtuelles individuelles peuvent être protégées sur un ordinateur hôte à l’aide de SRM en association avec la réplication vSphere.

- La **protection bidirectionnelle** utilise un ensemble unique de sites SRM jumelés pour protéger les machines virtuelles dans les deux sens. Chaque site peut être simultanément un site protégé et un site de récupération, mais pour un autre ensemble de machines virtuelles.

>[!IMPORTANT]
>Azure VMware Solution ne prend pas en charge : 
>
>- Réplication basée sur les baies et groupes de protection de stratégie de stockage 
>- Groupes de protection VVOLS 
>- Personnalisation de SRM IP à l’aide des outils en ligne de commande SRM
>- Topologie un-à-plusieurs et plusieurs-à-un 
>- Identificateur de plug-in ou ID d’extension SRM personnalisé


## <a name="deployment-workflow"></a>Workflow du déploiement

Le diagramme de flux de travail montre Azure VMware Solution principale au flux de travail secondaire. En outre, il montre les étapes à suivre au sein du Portail Azure et les environnements VMware de Azure VMware Solution pour obtenir la protection de bout en bout des machines virtuelles. 

:::image type="content" source="media/vmware-srm-vsphere-replication/site-recovery-manager-workflow.png" alt-text="Diagramme montrant le flux de travail de déploiement de VMware Site Recovery Manager sur Azure VMware Solution." border="false":::

## <a name="prerequisites"></a>Configuration requise

Assurez-vous d’avoir explicitement fourni à l’utilisateur distant les rôles Administrateur VRM et SRM dans le vCenter distant.

### <a name="scenario-on-premises-to-azure-vmware-solution"></a>Scénario : Trafic local vers Azure VMware Solution 

- Cloud privé de Azure VMware Solution déployé en tant que région secondaire.

- [Résolution DNS](configure-dns-azure-vmware-solution.md) sur des appliances SRM et Cloud virtuelles locales.

   >[!NOTE]
   >Pour les clouds privés créés le 1er juillet 2021 ou après, vous pouvez configurer la résolution DNS privée. Pour les nuages privés créés avant le 1er juillet 2021, qui ont besoin d'une résolution DNS privée, ouvrez une [demande d'assistance](https://rc.portal.azure.com/#create/Microsoft.Support) pour demander une **configuration DNS privée**.

- Connectivité ExpressRoute entre une installation locale et Azure VMware Solution 2 Gbit/s.

### <a name="scenario-primary-azure-vmware-solution-to-secondary"></a>Scénario : Azure VMware Solution principale vers la base de données secondaire

- Le cloud privé de Azure VMware Solution doit être déployé dans la région primaire et la région secondaire.

   :::image type="content" source="media/vmware-srm-vsphere-replication/two-private-clouds-different-regions.png" alt-text="Capture d’écran montrant deux clouds privés de Azure VMware Solution dans des régions distinctes.":::
 
- La connectivité, comme ExpressRoute Global Reach, entre le cloud privé de Azure VMware Solution source et cible.

   :::image type="content" source="media/vmware-srm-vsphere-replication/global-reach-connectity-to-on-premises.png" alt-text="Capture d’écran montrant la connectivité entre les clouds privés source et cible.":::

 
## <a name="install-srm-in-azure-vmware-solution"></a>Installer SRM dans Azure VMware Solution

1. Dans votre centre de données local, installez VMware SRM et vSphere.

   >[!NOTE]
   >Utilisez la [topologie à deux sites avec une instance de vCenter Server par modèle de déploiement PSC](https://docs.vmware.com/en/Site-Recovery-Manager/8.4/com.vmware.srm.install_config.doc/GUID-F474543A-88C5-4030-BB86-F7CC51DADE22.html). Assurez-vous également que les [ports requis de vSphere Replication Network](https://kb.VMware.com/s/article/2087769) sont ouverts.

1. Dans votre cloud privé Azure VMware Solution, sous **Manage**, sélectionnez **Modules complémentaires** > **Récupération d'urgence**.

   L’utilisateur CloudAdmin par défaut dans le cloud privé de Azure VMware Solution ne dispose pas des privilèges suffisants pour installer la réplication VMware SRM ou vSphere. Le processus d'installation comprend plusieurs étapes décrites dans la section [Conditions préalables](#prerequisites). Au lieu de cela, vous pouvez installer VMware SRM avec la réplication vSphere comme un service complémentaire à partir de votre Cloud privé de Azure VMware Solution.

   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-add-ons.png" alt-text="Capture d’écran du Cloud privé de Azure VMware Solution pour installer le SRM VMware avec la réplication vSphere en tant que module complémentaire" border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-add-ons.png":::

1. Dans le menu déroulant **Solution de reprise après sinistre**, sélectionnez **VMware Site Recovery Manager (SRM) - vSphere Replication**. 

   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-add-on.png" alt-text="Capture d’écran montrant l’onglet récupération d’urgence sous composants additionnels avec VMware Site Recovery Manager (SRM)-réplication vSphere sélectionnée." border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-add-on.png":::

1. Fournissez la clé de licence, sélectionnez accepter les conditions générales, puis sélectionnez **Installer**.

   >[!NOTE]
   >Si vous ne fournissez pas la clé de licence, SRM est installé en mode évaluation. La licence est utilisée uniquement pour activer VMware SRM.
   
   :::image type="content" source="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-licence.png" alt-text="Capture d’écran montrant l’onglet récupération d’urgence sous composants additionnels avec le champ clé de licence sélectionné." border="true" lightbox="media/VMware-srm-vsphere-replication/disaster-recovery-solution-srm-licence.png":::


## <a name="install-the-vsphere-replication-appliance"></a>Installer l’appliance de réplication vSphere

Une fois l’appliance SRM correctement installée, vous devez installer les appliances de réplication vSphere. Chaque serveur de réplication prend en charge jusqu’à 200 machines virtuelles protégées. Augmentez ou mettez à l’échelle en fonction de vos besoins. 

1. Dans la liste déroulante **Utilisation de la réplication**, sur l'onglet **Reprise après sinistre**, sélectionnez **Réplication vSphere**.

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-1.png" alt-text="Capture d’écran montrant la réplication vSphere sélectionnée pour la réplication à l’aide de l’option.":::

1. Déplacez le curseur serveur vSphere pour indiquer le nombre de serveurs de réplication que vous souhaitez en fonction du nombre de machines virtuelles à protéger. Sélectionnez **Installer**.

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-2.png" alt-text="Capture d’écran montrant comment augmenter ou réduire le nombre de serveurs de réplication.":::

1. Une fois l’installation terminée, vérifiez que SRM et les appliances de réplication vSphere sont installés.

   >[!TIP]
   >Le bouton désinstaller indique que SRM et les appliances de réplication vSphere sont actuellement installés.

   :::image type="content" source="media/vmware-srm-vsphere-replication/vsphere-replication-3.png" alt-text="Capture d’écran montrant que SRM et l’appliance de réplication sont installés.":::
  

## <a name="configure-site-pairing-in-vcenter"></a>Configurer le jumelage de sites dans vCenter

Après l’installation de la réplication VMware SRM et vSphere, vous devez effectuer la configuration et le jumelage de sites dans vCenter.

1. Se connecter à vCenter en tant que cloudadmin@vsphere.local.

1. Accédez à **Site Recovery**, vérifiez l'état de vSphere Replication et de VMware SRM, puis sélectionnez **OPEN Site Recovery** pour lancer le client.

   :::image type="content" source="media/vmware-srm-vsphere-replication/open-site-recovery.png" alt-text="Capture d’écran montrant le Client vSphere avec la réplication vSphere et l’état de l’installation du Site Recovery Manager en tant que OK." border="true":::


1. Sélectionnez **NOUVELLE PAIRE DE SITE** dans le client Site Recovery (SR) dans le nouvel onglet qui s'ouvre.
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/new-site-pair.png" alt-text="Capture d’écran montrant le client vSphere avec le bouton nouveau couple de sites sélectionné pour Site Recovery." border="true":::

1. Entrez les détails de site distant et sélectionnez **SUIVANT**.

   >[!NOTE]
   >Un Cloud privé de Azure VMware Solution fonctionne avec un contrôleur de services de plateforme (PSC) incorporé, de sorte qu’un seul vCenter local peut être sélectionné. Si le vCenter distant utilise un contrôleur PSC, utilisez le nom de domaine complet (FQDN) de vCenter (ou son adresse IP) et le port pour spécifier le PSC. 
   >
   >L'utilisateur distant doit disposer de permissions suffisantes pour effectuer les couplages. Un moyen simple de s’en assurer consiste à fournir à cet utilisateur les rôles d’administrateur du module VRM et d’administrateur SRM dans le vCenter distant. Dans le cas de Azure VMware Solution distante Cloud privé, cloudadmin est configuré avec ces rôles.

   :::image type="content" source="media/vmware-srm-vsphere-replication/pair-the-sites-specify-details.png" alt-text="Capture d'écran montrant les détails du site pour la nouvelle paire de sites." border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-specify-details.png":::

1. Sélectionnez **CONNEXION** pour accepter le certificat du vCenter distant.

   À ce stade, le client doit découvrir les appliances VRM et SRM des deux côtés en tant que services à coupler.

1. Sélectionnez les appareils à coupler, puis sélectionnez **SUIVANT**.
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/pair-the-sites-new-site.png" alt-text="Capture d’écran montrant les détails du vCenter Server et des services pour la nouvelle paire de sites." border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-new-site.png":::

1. Sélectionnez **CONNEXION** pour accepter les certificats du VMware SRM distant et du vCenter distant (à nouveau).

1. Sélectionnez **CONNEXION** pour accepter les certificats du VMware SRM local et du vCenter distant.

1. Passez en revue les paramètres, puis sélectionnez **TERMINER**.

   En cas de réussite, le client affiche un autre panneau pour l’appariement. Toutefois, en cas d’échec, une alarme est signalée.

1. En bas, dans le coin droit, sélectionnez la flèche double vers le haut pour développer le panneau et afficher les **Tâches récentes** et **Alarmes récentes**.

   >[!NOTE]
   >L’actualisation du client SR prend parfois beaucoup de temps. Si une opération semble trop longue ou s’affiche « bloqué », sélectionnez l’icône Actualiser dans la barre de menus. 

1. Sélectionnez **AFFICHER LES DÉTAILS** pour ouvrir le panneau de couplage de sites distants, qui ouvre une boîte de dialogue permettant de se connecter au vCenter distant.

   :::image type="content" source="media/vmware-srm-vsphere-replication/view-details-remote-pairing.png" alt-text="Capture d’écran montrant les détails de la nouvelle paire de sites pour la réplication de site Recovery Manager et vSphere." border="true" lightbox="media/vmware-srm-vsphere-replication/view-details-remote-pairing.png":::

1. Entrez le nom d’utilisateur disposant des autorisations suffisantes pour effectuer la réplication et la récupération de site, puis sélectionnez **LOG IN**. 

   Pour le couplage, la connexion, qui est souvent un autre utilisateur, est une action ponctuelle pour établir un jumelage. Le client SR requiert cette connexion chaque fois que le client est lancé pour fonctionner avec le jumelage.

   >[!NOTE] 
   >L'utilisateur disposant des permissions suffisantes doit avoir les **rôles d'administrateur VRM** et d'**administrateur SRM** dans le vCenter distant. L’utilisateur doit également avoir accès à l’inventaire vCenter distant, comme les dossiers et les magasins de stockage. Pour un Cloud privé distant de Azure VMware Solution, l’utilisateur cloudadmin dispose des autorisations et d’un accès appropriés. 
   
   :::image type="content" source="media/vmware-srm-vsphere-replication/sign-into-remote-vcenter.png" alt-text="Capture d’écran montrant les informations d’identification de vCenter Server." border="true":::

   Vous verrez un message d’avertissement indiquant que le VRS incorporé dans le module VRM local n’est pas en cours d’exécution.  Cela est dû au fait que Azure VMware Solution n’utilise pas le VRS incorporé dans un Cloud privé Azure solution VMware.  Au lieu de cela, il utilise des appliances VRS. 

   :::image type=" content" source=" media/vmware-srm-vsphere-replication/pair-the-sites-summary.png" alt-text="Capture d’écran montrant les détails de la nouvelle paire de sites pour la réplication Site Recovery Manager et vSphere." border="true" lightbox="media/vmware-srm-vsphere-replication/pair-the-sites-summary.png":::

## <a name="srm-protection-reprotection-and-failback"></a>Protection SRM, reprotection et restauration automatique

Une fois que vous avez créé le jumelage de sites, suivez la documentation VMware mentionnée ci-dessous pour la protection de bout en bout des machines virtuelles à partir de la Portail Azure.

- [Utilisation de la réplication vSphere avec Recovery Manager de Site (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-2C77C830-892D-45FF-BA4F-80AC10085DBE.html)

- [Mappages d’inventaire pour Array-Based les groupes de protection de réplication et les groupes de protection de réplication vSphere (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-2E2B4F84-D388-456B-AA3A-57FA8D47063D.html)

- [À propos des machines virtuelles d’espace réservé (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-EFE73B20-1C68-4D2C-8C86-A6E3C6214F07.html)

- [Groupes de protection de réplication vSphere (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-CCF2E768-736E-4EAA-B3BE-50182635BC49.html)

- [Création, test et exécution des plans de récupération (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-AF6BF11B-4FB7-4543-A873-329FDF1524A4.html)

- [Configuration d’un plan de récupération (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-FAC499CE-2994-46EF-9164-6D97EAF52C68.html)

- [Personnalisation des propriétés IP pour les machines virtuelles (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-25B33730-14BE-4268-9D88-1129011AFB39.html)

- [Comment le Site Recovery Manager reprotège les Machines virtuelles avec la réplication vSphere (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-1DE0E76D-1BA7-44D8-AEA2-5B2218E219B1.html)

- [Effectuer une restauration automatique (vmware.com)](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.admin.doc/GUID-556E84C0-F8B7-4F9F-AAB0-0891C084EDE4.html)

   >[!NOTE]
   >Si des règles de personnalisation des adresses IP ont été définies pour les mappages réseau entre l’environnement AVS et l’environnement local, ces règles ne sont pas appliquées lors de la restauration automatique de l’environnement AVS à l’environnement local en raison d’un [problème connu](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-releasenotes-8-3.html#knownissues) avec SRM 8.3.0. Vous pouvez contourner cette limitation en supprimant la protection de toutes les machines virtuelles du groupe de protection, puis en reconfigurant la protection avant de lancer la restauration automatique.


## <a name="ongoing-management-of-your-srm-solution"></a>Gestion continue de votre solution SRM

Bien que Microsoft ait l’objectif de simplifier l’installation de la réplication VMware SRM et vSphere sur un Cloud privé Azure VMware Solution, vous êtes responsable de la gestion de votre licence et du fonctionnement quotidien de la solution de récupération d’urgence. 

## <a name="scale-limitations"></a>Limitations relatives à la mise à l’échelle

Les limitations de mise à l’échelle sont par Cloud privé.

| Configuration | Limite |
| --- | --- |
| Nombre de machines virtuelles protégées  | 1 000  |
| Nombre de machines virtuelles par plan de récupération  | 1 000  |
| Nombre de groupes de protection par plan de récupération  | 250  |
| Valeurs de RPO  | 5 min ou supérieure*  |
| Nombre total de machines virtuelles par groupe de protection  | 500  |
| Nombre total de plans de récupération  | 250  |

\*Pour plus d'informations sur l'objectif de point de récupération (RPO) inférieur à 15 minutes, consultez la section [Fonctionnement de l'objectif de point de récupération de 5 minutes](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-9E17D567-A947-49CD-8A84-8EA2D676B55A.html) dans le _Guide d'administration de vSphere Replication_.



## <a name="srm-licenses"></a>Licences SRM

Vous pouvez installer VMware SRM à l’aide d’une licence d’évaluation ou d’une licence de production.  La licence d’évaluation est valide pendant 60 jours. Après la période d’évaluation, vous serez invité à obtenir une licence de production de VMware SRM. 

Vous ne pouvez pas utiliser de licences VMware SRM locales existantes pour votre Cloud privé Azure VMware Solution. Collaborez avec vos équipes de vente et VMware pour acquérir une nouvelle licence de production basée sur les termes de VMware SRM. 

Une fois que vous avez acquis une licence de production de SRM, vous pouvez utiliser le portail de Azure VMware Solution pour mettre à jour SRM avec la nouvelle licence de production. 


## <a name="uninstall-srm"></a>Désinstaller SRM 

Si vous n’avez plus besoin de SRM, vous devez le désinstaller de manière propre. Avant de désinstaller SRM, vous devez supprimer toutes les configurations SRM des deux sites dans le bon ordre. Si vous ne supprimez pas toutes les configurations avant de désinstaller SRM, certains composants de SRM, comme les machines virtuelles d’espace réservé, peuvent rester dans l’infrastructure Azure VMware Solution.

1. Dans le client vSphere ou le client Web vSphere, sélectionnez **Site Recovery** > **Ouvrir Site Recovery**.

2. Dans l'onglet Accueil de **Site Recovery**, sélectionnez une paire de sites et sélectionnez **Afficher les détails**.

3. Sélectionnez l'onglet **Plans de récupération**, cliquez avec le bouton droit de la souris sur un plan de récupération et sélectionnez **Supprimer**.

   >[!NOTE]
   >Vous ne pouvez pas supprimer les plans de récupération en cours d’exécution.

4. Sélectionnez l'onglet **Groupes de protection**, sélectionnez un groupe de protection, puis l'onglet **Machines virtuelles**.

5. Sélectionnez toutes les machines virtuelles, cliquez avec le bouton droit et sélectionnez **Supprimer la protection**.

   La suppression de la protection d’une machine virtuelle entraîne la suppression de l’espace réservé sur le site de récupération. Répétez cette opération pour tous les groupes de protection.

6. Dans l'onglet **Groupes de protection**, cliquez avec le bouton droit de la souris sur un groupe de protection et sélectionnez **Supprimer**.

   >[!NOTE] 
   >Vous ne pouvez pas supprimer un groupe de protection inclus dans un plan de récupération. Vous ne pouvez pas supprimer les groupes de protection de réplication vSphere qui contiennent des ordinateurs virtuels sur lesquels la protection est encore configurée.

7. Sélectionnez **Configurer** la  > **Paire de sites** et supprimez tous les mappages d'inventaire.

   a. Sélectionnez chacun des onglets **Mappages de réseau**, **Mappages de dossiers** et **Mappages de ressources**.

   b. Dans chaque onglet, sélectionnez un site, cliquez avec le bouton droit sur un mappage, puis sélectionnez **Supprimer**.

8. Pour les deux sites, sélectionnez **Magasin de données d’espace réservé**, cliquez avec le bouton droit de la souris sur le datastore de remplacement, puis sélectionnez **Supprimer**.

9. Sélectionnez **Paires de sites** > **Résumé**, puis **Arrêter les paires de sites**.

   >[!NOTE] 
   >L’arrêt du jumelage de sites supprime toutes les informations relatives à l’inscription des Recovery Manager de site avec Recovery Manager de site, vCenter Server et le contrôleur de Services de plateforme sur le site distant.

10. Dans votre cloud privé, sous **Gérer**, sélectionnez **Modules complémentaires** > **Récupération d'urgence**, puis sélectionnez **Désinstaller les appliances de réplication**.

11. Une fois les appliances de réplication désinstallées, dans l'onglet **Récupération d’urgence**, sélectionnez **Désinstaller pour le Site Recovery Manager**.

12. Répétez ces étapes sur le site de solution Azure VMware Solution secondaire.


## <a name="support"></a>Support 

VMware SRM est une solution de récupération d’urgence de VMware.  

Microsoft prend uniquement en charge l’installation/la désinstallation de SRM et vSphere Replication Manager, et augmente/diminue les appliances de réplication vSphere au sein d’Azure VMware Solution. 

Pour tous les autres problèmes liés à la configuration et à la réplication, contactez VMware pour obtenir de l’aide.

Les équipes de support VMware et Microsoft se contacteront les unes des autres si nécessaire pour résoudre les problèmes liés à SRM sur Azure VMware Solution.


## <a name="references"></a>References

- [Documentation VMware Site Recovery Manager](https://docs.vmware.com/en/Site-Recovery-Manager/index.html)
- [Matrices de compatibilité pour le Site VMware Recovery Manager 8.3](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-compat-matrix-8-3.html)
- [Notes de publication de VMware SRM 8.3](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/rn/srm-releasenotes-8-3.html)
- [Documentation sur la réplication VMware vSphere](https://docs.vmware.com/en/vSphere-Replication/index.html)
- [Matrices de compatibilité pour la réplication vSphere 8.3](https://docs.vmware.com/en/vSphere-Replication/8.3/rn/vsphere-replication-compat-matrix-8-3.html)
- [Limites opérationnelles pour Site Recovery Manager 8.3](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-3AD7D565-8A27-450C-8493-7B53F995BB14.html)
- [Limites opérationnelles pour la réplication vSphere 8.3](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-E114BAB8-F423-45D4-B029-91A5D551AC47.html)
- [Calculer la bande passante pour la réplication vSphere](https://docs.vmware.com/en/vSphere-Replication/8.3/com.vmware.vsphere.replication-admin.doc/GUID-4A34D0C9-8CC1-46C4-96FF-3BF7583D3C4F.html)
- [Installation et configuration de SRM](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-B3A49FFF-E3B9-45E3-AD35-093D896596A0.html)
- [Administration de la réplication vSphere](https://docs.vmware.com/en/vSphere-Replication/8.2/com.vmware.vsphere.replication-admin.doc/GUID-35C0A355-C57B-430B-876E-9D2E6BE4DDBA.html)
- [Conditions préalables et meilleures pratiques pour l’installation de SRM](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-BB0C03E4-72BE-4C74-96C3-97AC6911B6B8.html)
- [Ports réseau pour SRM](https://docs.vmware.com/en/Site-Recovery-Manager/8.3/com.vmware.srm.install_config.doc/GUID-499D3C83-B8FD-4D4C-AE3D-19F518A13C98.html)
- [Ports réseau pour la réplication vSphere](https://kb.vmware.com/s/article/2087769)
