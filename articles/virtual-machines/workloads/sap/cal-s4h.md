---
title: Déploiement de SAP S/4HANA ou BW/4HANA sur une machine virtuelle Azure | Microsoft Docs
description: Déploiement de SAP S/4HANA ou BW/4HANA sur une machine virtuelle Azure
services: virtual-machines-linux
documentationcenter: ''
author: hobru
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/26/2021
ms.author: hobruche
ms.openlocfilehash: 0fff7d57e8a7dfa312b16accd15ef5990cdedf15
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853621"
---
# <a name="sap-cloud-appliance-library"></a>Bibliothèque d’appliances cloud SAP

La [Bibliothèque d’appliances cloud SAP](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) vous permet de créer rapidement un environnement de démonstration avec un système SAP entièrement préconfiguré. En quelques clics, vous pouvez disposer d’un système SAP opérationnel. Les liens suivants mettent en évidence plusieurs solutions que vous pouvez rapidement déployer sur Azure. Sélectionnez simplement le lien « Créer une instance ». 

Vous devez vous authentifier en tant qu’utilisateur S ou P. Vous pouvez créer un utilisateur P gratuit via la [Communauté SAP](https://community.sap.com/).  Plus d’informations vous sont proposées ci-dessous.

| Solution | Lien |
| -------------- | :--------- | 
| **SAP S/4HANA 2020 FPS02, appliance entièrement activée** 27 juillet 2021 | [Créer une instance](https://cal.sap.com/registration?sguid=d48af08b-e2c6-4409-82f8-e42d5610e918&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Cette appliance contient SAP S/4HANA 2020 (FPS02) avec les meilleures pratiques SAP préactivées pour les fonctions principales de SAP S/4HANA, ainsi que des scénarios de Service, Gouvernance des données de référence (MDG), Gestion du transport (TM), Gestion de portefeuilles de projets (PPM), Gestion du capital humain (HCM), Analytics, Migration Cockpit, et bien d’autres encore. L’accès utilisateur s’effectue via SAP Fiori, la GUI SAP, SAP HANA Studio, Bureau à distance Windows ou le système d’exploitation principal pour un accès administratif total. |  [Détails]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/d48af08b-e2c6-4409-82f8-e42d5610e918) |
| **SAP S/4HANA 2020 FPS01, appliance entièrement activée** 20 avril 2021 | [Créer une instance](https://cal.sap.com/registration?sguid=a0b63a18-0fd3-4d88-bbb9-4f02c13dc343&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Cette appliance contient SAP S/4HANA 2020 (FPS01) avec les meilleures pratiques SAP préactivées pour les fonctions principales de SAP S/4HANA, ainsi que des scénarios de Service, Gouvernance des données de référence (MDG), Gestion du transport (TM), Gestion de portefeuilles de projets (PPM), Gestion du capital humain (HCM), Analytics, Migration Cockpit, et bien d’autres encore. L’accès utilisateur s’effectue via SAP Fiori, la GUI SAP, SAP HANA Studio, Bureau à distance Windows ou le système d’exploitation principal pour un accès administratif total. |  [Détails](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/a0b63a18-0fd3-4d88-bbb9-4f02c13dc343) | 
| **SAP S/4HANA 2020 FPS02** 10 juin 2021 | [Créer une instance](https://cal.sap.com/registration?sguid=c7cff775-cbf7-4cd1-a907-6eeca95a0946&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
| Cette solution est proposée en tant qu’installation standard du système S/4HANA, et comprend un bureau à distance pour un accès frontal facile. Elle contient une interface utilisateur SAP S/4HANA Fiori préconfigurée et activée dans le client 100, avec des composants requis activés en fonction de la note SAP d’activation rapide 3045635 pour SAP Fiori dans SAP S/4HANA 2020 FPS02. Vous trouverez plus d’informations grâce au lien. | [Détails](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/c7cff775-cbf7-4cd1-a907-6eeca95a0946) | 
| **IDES EHP8 FOR SAP ERP 6.0 sur SAP ASE, juin 2021** 10 juin 2021  | [Créer une instance](https://cal.sap.com/registration?sguid=ed55a454-0b10-47c5-8644-475ecb8988a0&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Les systèmes IDES sont des copies des systèmes de démonstration internes de SAP, et constituent le terrain de jeu pour les opérations de personnalisation et de test. Ce système IDES spécifique peut être utilisé en tant que système source dans les scénarios de migration de données de l’appliance entièrement activée SAP S/4HANA (2020 FPS01 et versions supérieures). Par ailleurs, il contient des scénarios d’entreprise standard basés sur des données principales et transactionnelles prédéfinies. |  [Détails](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/ed55a454-0b10-47c5-8644-475ecb8988a0) |
| **SAP BW/4HANA 2.0 SP07 incluant du contenu BW/4HANA 2.0 SP06** 24 février 2020 | [Créer une instance](https://cal.sap.com/registration?sguid=0f2f20f4-d012-4f76-81af-6ff15063db66&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Cette solution vous fournit un aperçu de SAP BW/4HANA. SAP BW/4HANA est la Data Warehouse de nouvelle génération optimisé pour HANA. Outre les options BW/4HANA de base, la solution offre un ensemble de contenus HANA/4HANA optimisés pour HANA et l’étape suivante des scénarios hybrides avec SAP Data Warehouse Cloud. Le système étant préconfiguré, vous pouvez commencer à implémenter directement vos scénarios.| [Détails](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/0f2f20f4-d012-4f76-81af-6ff15063db66) | 
| **SAP Business One 10.0 PL02, version pour SAP HANA** 4 août 2020  | [Créer une instance](https://cal.sap.com/registration?sguid=371edc8c-56c6-4d21-acb4-2d734722c712&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Approuvé par plus de 70 000 petites et moyennes entreprises dans plus de 170 pays, SAP Business One est une solution ERP flexible, abordable et scalable dotée de la puissance de SAP HANA. La solution est préconfigurée avec une licence d’évaluation gratuite de 31 jours et une base de données de démonstration de votre choix préinstallée. Consultez le guide de démarrage pour en savoir plus sur l’étendue de la solution et sur la façon d’ajouter facilement de nouvelles bases de données de démonstration. |  [Détails](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/371edc8c-56c6-4d21-acb4-2d734722c712) |
| **Détecteur d’informations pour SAP Data Custodian v2106** 30 août 2021  | [Créer une instance](https://cal.sap.com/registration?sguid=db44680c-8a2a-405d-8963-838db38fa7dd&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Le détecteur d’informations pour SAP Data Custodian peut être utilisé pour automatiser l’étiquetage des données des ressources cloud. Les détecteurs d’informations fouillent vos ressources d’infrastructure et déterminent si elles contiennent certains types d’informations. |  [Détails](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/db44680c-8a2a-405d-8963-838db38fa7dd) |
| **SAP Yard Logistics 2009 pour SAP S/4HANA** 28 juillet 2021 | [Créer une instance](https://cal.sap.com/registration?sguid=9cdf4f13-73a5-4743-a213-82e0d1a68742&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Exécutez une logistique de chaîne d’approvisionnement plus efficace et rentable avec l’application SAP Yard Logistics. Optimisez votre visibilité sur tous les processus Yard et prévisualisez les charges de travail planifiées grâce à un large choix d’outils de visualisation et de création de rapports. Vous pouvez ainsi optimiser l’utilisation des ressources et prendre en charge la planification, l’exécution et la facturation avec un seul système.|  [Détails](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/9cdf4f13-73a5-4743-a213-82e0d1a68742) | 
| **SAP S/4HANA 2020 FPS02, appliance entièrement activée** 27 juillet 2021 | [Créer une instance](https://cal.sap.com/registration?sguid=d48af08b-e2c6-4409-82f8-e42d5610e918&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) | 
|Cette solution est proposée en tant qu’installation standard du système S/4HANA, et comprend un bureau à distance pour un accès frontal facile. Elle contient une interface utilisateur SAP S/4HANA Fiori préconfigurée et activée dans le client 100, avec des composants requis activés en fonction de la note SAP d’activation rapide 3045635 pour SAP Fiori dans SAP S/4HANA 2020 FPS02. Vous trouverez plus d’informations grâce au lien.| [Détails](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/d48af08b-e2c6-4409-82f8-e42d5610e918) | 
| **SAP Focused Run 3.0 FP01, non configuré** 21 juillet 2021 | [Créer une instance](https://cal.sap.com/registration?sguid=82bdb96e-3578-41aa-a3e1-a6d9a8335ae1&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Focused Run est conçu plus particulièrement pour les entreprises ayant besoin d’une surveillance, d’alertes et d’une analyse des applications et des systèmes volumineux. Il s’agit d’une solution puissante pour les fournisseurs de services qui souhaitent héberger tous leurs clients dans un environnement central, évolutif, sécurisé et automatisé. Elle s’adresse également aux clients ayant des besoins avancés de gestion du système, de surveillance des utilisateurs, de surveillance de l’intégration, ainsi que de configuration et d’analyse de la sécurité.|  [Détails](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/82bdb96e-3578-41aa-a3e1-a6d9a8335ae1) | 
| **SAP S/4HANA 2020 FPS01 Utilities Trial** 21 juillet 2021 | [Créer une instance](https://cal.sap.com/registration?sguid=68785eeb-a228-4aa8-8273-b4c30775590c&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Cette solution vous permet de créer votre propre système d’utilitaires SAP S/4HANA 2020 et de bénéficier d’une expérience pratique, notamment un accès administrateur complet à tous les domaines. Les visites guidées sélectionnées vous aideront à comprendre le traitement optimisé des données de mesure, le processus de facturation rationalisé par le biais d’interfaces utilisateur FIORI basées sur les rôles et le service client spécifique dédié à l’engagement client.|  [Détails](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/68785eeb-a228-4aa8-8273-b4c30775590c)| 
| **SAP Product Lifecycle Costing 4.0 SP3 Correctif 2** 1er août 2021 | [Créer une instance](https://cal.sap.com/registration?sguid=f2bf191a-7efc-48a2-b8ac-51756eb225bc&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Product Lifecycle Costing est une solution permettant de calculer les coûts et d’autres dimensions pour les nouveaux produits ou les devis liés aux produits dans une phase avancée du cycle de vie du produit, d’identifier rapidement les facteurs de coûts, et de simuler et comparer facilement les alternatives.|  [Détails](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/f2bf191a-7efc-48a2-b8ac-51756eb225bc)|
| **Plateforme SAP ABAP 1909, Édition Développeur** 21 juin 2021  | [Créer une instance](https://cal.sap.com/registration?sguid=7bd4548f-a95b-4ee9-910a-08c74b4f6c37&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|La plateforme SAP ABAP sur SAP HANA vous donne accès à la plateforme SAP ABAP 1909 Édition Développeur sur SAP HANA. Notez que cette solution est préconfigurée avec de nombreux éléments supplémentaires, à savoir : modèle de programmation d’applications RESTful SAP ABAP, SAP Fiori launchpad, SAP gCTS, SAP ABAP Test Cockpit, ainsi que des connexions frontales/principales préconfigurées, etc. Elle inclut également toute l’infrastructure ABAP AS standard : gestion des transactions, opérations/persistance de base de données, système de modification et de transport, Passerelle SAP, interopérabilité avec le kit de développement ABAP et SAP WebIDE, et bien plus encore. |  [Détails](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/7bd4548f-a95b-4ee9-910a-08c74b4f6c37) |
| **1 : système source SAP ERP (openSAP)**  17 septembre 2021 | [Créer une instance](https://cal.sap.com/registration?sguid=1a3556c0-0ee1-4a4c-8a5a-db08173df293&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Solution 1 pour effectuer une conversion système de SAP ERP vers l’état initial de SAP S/4HANA. Elle a été testée et préparée pour être convertie de SAP EHP6 pour SAP ERP 6.0 SPS13 vers SAP S/4HANA 2020 FPS00. |  [Détails]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/1a3556c0-0ee1-4a4c-8a5a-db08173df293) |
| **2 : système source SAP ERP après les étapes de préparation précédant l’exécution du gestionnaire des mises à jour logicielles (openSAP)**  17 octobre 2021 | [Créer une instance](https://cal.sap.com/registration?sguid=5eb92a4d-a704-48b8-b060-0647c63b667c&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Solution 2 pour effectuer une conversion système de SAP ERP vers SAP S/4HANA après les étapes de préparation précédant l’exécution du gestionnaire des mises à jour logicielles. Elle a été testée et préparée pour être convertie de SAP EHP6 pour SAP ERP 6.0 SPS13 vers SAP S/4HANA 2020 FPS00. |  [Détails]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/5eb92a4d-a704-48b8-b060-0647c63b667c) |
| **3 : système cible SAP S/4HANA après la conversion technique précédant une configuration supplémentaire** 22 septembre 2021  | [Créer une instance](https://cal.sap.com/registration?sguid=4336a3fb-2fc9-4a93-9500-c65101ffc9d7&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Solution 3 après avoir effectué une conversion de système technique de SAP ERP vers SAP S/4HANA précédant une configuration supplémentaire. Elle a été testée et préparée comme convertie de SAP EHP6 pour SAP ERP 6.0 SPS13 vers SAP S/4HANA 2020 FPS00. |  [Détails](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/4336a3fb-2fc9-4a93-9500-c65101ffc9d7) |
| **4 : système cible S/4HANA SAP incluant une configuration supplémentaire (openSAP)**  17 octobre 2021 | [Créer une instance](https://cal.sap.com/registration?sguid=f48f2b77-389f-488b-be2b-1c14a86b2e69&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Solution 4 après conversion d’un système technique de SAP ERP vers SAP S/4HANA, incluant une configuration supplémentaire. Elle a été testée et préparée comme convertie de SAP EHP6 pour SAP ERP 6.0 SPS13 vers SAP S/4HANA 2020 FPS00. |  [Détails]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/f48f2b77-389f-488b-be2b-1c14a86b2e69) |
 




---







## <a name="setup-and-get-started-with-sap-cloud-appliance-library"></a>Configuration et prise en main de SAP Cloud Appliance Library

> [!NOTE]
> Pour plus d’informations sur SAP CAL, accédez au site web [SAP Cloud Appliance Library](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8). Il existe également un blog SAP sur [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

> [!NOTE]
> Depuis le 29 mai 2017, vous pouvez utiliser le modèle de déploiement Azure Resource Manager en plus du modèle de déploiement Classic (moins recommandé) pour déployer SAP CAL. Nous vous recommandons d’utiliser le nouveau modèle de déploiement Resource Manager et d’ignorer le modèle de déploiement Classic.

## <a name="create-an-account-in-the-sap-cal"></a>Création d’un compte dans SAP CAL
1. Lorsque vous vous connectez à SAP CAL pour la première fois, utilisez votre identifiant SAP S-User ou un autre identifiant utilisateur enregistré auprès de SAP. Ensuite, définissez un compte SAP CAL utilisé par SAP CAL pour déployer des appliances sur Azure. Dans la définition du compte, procédez comme suit :

    a. Sélectionnez le modèle de déploiement sur Azure (Resource Manager ou Classic).

    b. Entrez votre abonnement Azure. Un compte SAP CAL ne peut être assigné qu’à un seul abonnement. Si vous avez besoin de plusieurs abonnements, vous devez créer un autre compte SAP CAL.

    c. Accordez à SAP CAL l’autorisation d’effectuer des déploiements dans votre abonnement Azure.

    > [!NOTE]
    > Les étapes suivantes montrent comment créer un compte SAP CAL pour les déploiements Resource Manager. Si vous disposez déjà d’un compte SAP CAL lié au modèle de déploiement Classic, vous *devez* suivre ces étapes pour créer un nouveau compte SAP CAL. Le nouveau compte SAP CAL doit être déployé dans le modèle Resource Manager.

2. Créez un compte SAP CAL. La page **Accounts** (Comptes) affiche trois possibilités pour Azure : 

    a. **Microsoft Azure (Classic)** est le modèle de déploiement classique et n’est plus recommandé.

    b. **Microsoft Azure** est le nouveau modèle de déploiement Resource Manager.

    c. **Windows Azure operated by 21Vianet** (Windows Azure géré par 21Vianet) est une option pour la Chine, qui utilise le modèle de déploiement Classic.

    Pour effectuer un déploiement dans le modèle Resource Manager, sélectionnez **Microsoft Azure**.

    ![Détails du compte SAP CAL](./media/cal-s4h/s4h-pic-2a.png)

3. Dans le champ **Subscription ID** (ID d’abonnement), entrez l’ID d’abonnement Azure indiqué dans le Portail Azure.

   ![Comptes SAP CAL](./media/cal-s4h/s4h-pic3c.png)

4. Pour autoriser le déploiement de SAP CAL dans l’abonnement Azure que vous avez défini, cliquez sur **Authorize** (Autoriser). La page suivante s’affiche dans l’onglet du navigateur :

   ![Connexion aux services cloud d’Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

5. Si plusieurs utilisateurs sont répertoriés, choisissez le compte Microsoft lié en tant que coadministrateur de l’abonnement Azure que vous avez sélectionné. La page suivante s’affiche dans l’onglet du navigateur :

   ![Confirmation des services cloud d’Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

6. Cliquez sur **Accepter**. Si l’autorisation est accordée, la définition du compte SAP CAL s’affiche à nouveau. Après une courte période, un message confirme que le processus d’autorisation a réussi.

7. Pour assigner le compte SAP CAL nouvellement créé à votre utilisateur, entrez votre identifiant utilisateur dans la zone de texte **User ID** (Identifiant utilisateur) à droite, puis cliquez sur **Add** (Ajouter).

   ![Association du compte à l’utilisateur](./media/cal-s4h/s4h-pic8a.png)

8. Pour associer votre compte à l’utilisateur que vous utilisez pour vous connecter à SAP CAL, cliquez sur **Review** (Revoir). 
 
9. Pour créer l’association entre votre utilisateur et le compte SAP CAL nouvellement créé, cliquez sur **Create** (Créer).

   ![Association d’un utilisateur au compte SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Vous avez correctement créé un compte SAP CAL capable d’effectuer les tâches suivantes :

- Utiliser le modèle de déploiement Resource Manager.
- Déployer des systèmes SAP dans votre abonnement Azure.

Vous pouvez à présent commencer à déployer S/4HANA dans votre abonnement utilisateur dans Azure.

> [!NOTE]
> Avant de poursuivre, déterminez si vous êtes lié à des quotas de cœurs Azure. SAP CAL utilise des machines virtuelles Azure série M pour déployer certaines solutions SAP HANA. Il se peut que votre abonnement Azure ne soit pas associé à des quotas de cœurs pour la série M. Dans ce cas, vous devrez peut-être contacter le support Azure pour obtenir un quota requis.

> [!NOTE]
> Lorsque vous déployez une solution sur Azure dans SAP CAL, vous constaterez peut-être que vous ne pouvez choisir qu’une seule région Azure. Pour effectuer un déploiement dans des régions Azure autres que celle suggérée par SAP CAL, vous devez acheter un abonnement CAL auprès de SAP. Vous devrez peut-être également ouvrir un message avec SAP pour que votre compte CAL puisse déployer dans des régions Azure autres que celles suggérées initialement.

## <a name="deploy-a-solution"></a>Déployer une solution

Nous allons à présent déployer une solution à partir de la page **Solutions** de SAP CAL. SAP CAL offre deux séquences de déploiement :

- Une séquence de base qui utilise une page pour définir le système à déployer
- Une séquence avancée qui vous offre différents choix en termes de tailles de machine virtuelle 

Nous présentons ici la séquence de déploiement de base.

1. Sur la page **Account Details** (Détails du compte), effectuez les tâches suivantes :

    a. Sélectionnez un compte SAP CAL. (Utilisez un compte associé au déploiement avec le modèle de déploiement Resource Manager.)

    b. Entrez le nom de l’instance dans le champ **Name** (Nom).

    c. Sélectionnez une région Azure dans le champ **Region** (Région). SAP CAL suggère une région. Si vous avez besoin d’une autre région Azure et que vous ne possédez pas d’abonnement SAP CAL, vous devez vous procurer un abonnement CAL auprès de SAP.

    d. Entrez un mot de passe principal pour la solution dans le champ **Password** (Mot de passe). Celui-ci doit comprendre huit ou neuf caractères. Le mot de passe est utilisé pour les administrateurs des différents composants.

   ![Mode de base SAP CAL : créer une instance](./media/cal-s4h/s4h-pic10a.png)

2. Cliquez sur **Create** (Créer), puis, dans la boîte de message qui s’affiche, cliquez sur **OK**.

   ![Tailles de machine virtuelle prises en charge par SAP CAL](./media/cal-s4h/s4h-pic10b.png)

3. Dans la boîte de dialogue **Private Key** (Clé privée), cliquez sur **Store** (Stocker) pour stocker la clé privée dans SAP CAL. Pour protéger la clé privée par mot de passe, cliquez sur **Download** (Télécharger). 

   ![Clé privée SAP CAL](./media/cal-s4h/s4h-pic10c.png)

4. Lisez le **message d’avertissement** SAP CAL, puis cliquez sur **OK**.

   ![Avertissement SAP CAL](./media/cal-s4h/s4h-pic10d.png)

    Le déploiement s’effectue. Après un délai variable en fonction de la taille et de la complexité de la solution (SAP CAL en donne une estimation), la solution apparaît comme active et prête à être utilisée.

5. Pour trouver les machines virtuelles collectées avec les autres ressources associées dans un groupe de ressources, accédez au Portail Azure : 

   ![Objets SAP CAL déployés dans le nouveau portail](./media/cal-s4h/sapcaldeplyment_portalview.png)

6. Sur le portail SAP CAL, l’état affiché est **Active**. Pour vous connecter à la solution, cliquez sur **Connect** (Connexion). Plusieurs options de connexion aux différents composants sont déployées au sein de cette solution.

   ![Instances SAP CAL](./media/cal-s4h/active_solution.png)

7. Pour pouvoir utiliser l’une des options de connexion aux systèmes déployés, cliquez sur **Getting Started Guide** (Guide de prise en main). 

   ![Connexion à l’instance](./media/cal-s4h/connect_to_solution.png)

    La documentation désigne les utilisateurs pour chacune des méthodes de connectivité. Le mot de passe principal défini au début du processus de déploiement définit les mots de passe de ces utilisateurs. La documentation répertorie d’autres utilisateurs plus fonctionnels avec leurs mots de passe. Vous pouvez les utiliser pour vous connecter au système déployé. 

    Par exemple, si vous utilisez l’interface graphique utilisateur SAP pré-installée sur l’ordinateur Bureau à distance Windows, le système S/4 peut ressembler à ceci :

   ![SM50 dans l’interface graphique utilisateur SAP pré-installée](./media/cal-s4h/gui_sm50.png)

    Si vous utilisez DBACockpit, l’instance peut ressembler à ceci :
 

   ![SM50 dans l’interface graphique utilisateur SAP DBACockpit](./media/cal-s4h/dbacockpit.png)

En quelques heures, une appliance SAP S/4 intègre est déployée dans Azure.

Si vous avez acheté un abonnement SAP CAL, SAP prend entièrement en charge les déploiements par le biais de SAP CAL sur Azure. La file d’attente de prise en charge est BC-VCM-CAL.







