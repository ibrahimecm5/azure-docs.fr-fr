---
title: 'Tutoriel : Optimiser Azure Hybrid Benefit géré de manière centralisée pour SQL Server'
description: Ce tutoriel vous explique pas à pas comment attribuer de manière proactive des licences SQL Server sur Azure pour gérer et optimiser Azure Hybrid Benefit.
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 6031bd83a5a32ffc5e76a4a967e305324f8d7ee8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218860"
---
# <a name="tutorial-optimize-centrally-managed-azure-hybrid-benefit-for-sql-server"></a>Tutoriel : Optimiser Azure Hybrid Benefit géré de manière centralisée pour SQL Server

Ce tutoriel vous montre comment attribuer de manière proactive des licences SQL Server dans Azure afin de gérer et optimiser [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) de manière centralisée. L’optimisation de votre avantage vous permettra de réduire les coûts d’exécution d’Azure SQL.

Dans ce tutoriel, vous apprenez à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Collecter les détails de l’utilisation et de la disponibilité des licences
> * Acheter d’autres licences si nécessaire
> * Attribuer des licences sur Azure
> * Superviser et ajuster l’utilisation
> * Planifier la gestion

## <a name="prerequisites"></a>Prérequis

Avant de commencer, veillez à :

Avoir lu et compris l’article [Qu’est-ce que Azure Hybrid Benefit géré de manière centralisée ?](overview-azure-hybrid-benefit-scope.md). Cet article décrit les types de licences SQL Server qui sont éligibles à l’avantage Azure Hybrid Benefit. Il explique aussi comment activer l’avantage Azure Hybrid Benefit et l’appliquer à l’étendue choisie au niveau de l’abonnement ou du compte de facturation global.

> [!NOTE]
> Actuellement, la gestion d’Azure Hybrid Benefit sur une étendue est en préversion publique et limitée aux clients Entreprise.

Avant de commencer à utiliser la nouvelle expérience, vérifiez que vos machines virtuelles installées automatiquement qui exécutent SQL Server dans Azure sont inscrites. Vous vous assurez ainsi que les ressources Azure exécutant SQL Server sont visibles par vous et par Azure. Pour plus d’informations sur l’inscription des machines virtuelles SQL dans Azure, consultez [Inscrire une machine virtuelle SQL Server auprès de l’extension SQL IaaS Agent](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) et [Inscrire plusieurs machines virtuelles SQL dans Azure avec l’extension SQL IaaS Agent](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-vms-bulk.md).

## <a name="gather-license-usage-and-availability-details"></a>Collecter les détails de l’utilisation et de la disponibilité des licences

_La première étape est la préparation._ Prenez des renseignements dans les autres services de votre organisation pour répondre à ces deux questions :

- Quelles sont les utilisations actuelle et planifiée d’Azure SQL et de SQL Server pour l’année à venir ?
- Combien de licences cœur SQL Server sont disponibles pour être attribuées sur Azure ?

Ensuite, déterminez les utilisations actuelle _et planifiée_ d’Azure SQL pour l’année à venir ou toute autre période prolongée (au moins un mois).

Les détails de l’utilisation d’Azure SQL s’affichent lorsque [vous créez des attributions de licences SQL Server pour Azure Hybrid Benefit](create-sql-license-assignments.md).

Nous vous conseillons de faire valider les informations ci-dessus par les personnes compétentes de votre organisation. Vérifiez notamment avec elles votre utilisation _planifiée_ et la croissance prévue de l’utilisation de SQL Server. Ces données de planification peuvent être centralisées par une personne ou un groupe qui en a la propriété, ou être réparties entre plusieurs équipes.

Vous pouvez aussi déterminer votre utilisation d’Azure SQL (y compris l’utilisation d’Azure Hybrid Benefit au niveau ressource) à l’aide du [script PowerShell sql-license-usage](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit) d’Azure Hybrid Benefit. C’est une autre méthode utile. Ce script analyse et trace l’utilisation combinée des licences SQL Server attribuées à toutes les ressources SQL dans un abonnement spécifique ou un compte global.

### <a name="determine-the-number-of-sql-server-core-licenses-available-to-assign-to-azure"></a>Déterminer le nombre de licences cœur SQL Server disponibles pour être attribuées sur Azure

La quantité dépend du nombre de licences que vous avez achetées et du nombre de licences déjà utilisées à la fois sur les serveurs locaux et les machines virtuelles Azure.

Vous bénéficiez de 180 jours de droits d’utilisation double de la licence SQL Server afin de pouvoir effectuer les migrations sans problème. À l’issue de la période de 180 jours, vous ne pourrez utiliser la licence SQL Server que sur Azure. Prenez ce point en compte lorsque vous planifiez la disponibilité des licences. Par exemple, les licences en cours de migration peuvent être considérées comme des licences à attribuer disponibles.

Vos services d’achat ou de gestion des logiciels disposent généralement de ces informations.

## <a name="buy-more-licenses-if-needed"></a>Acheter d’autres licences si nécessaire

Après avoir examiné les informations collectées, si vous êtes sûr que le nombre de licences SQL Server disponibles est insuffisant pour couvrir l’utilisation planifiée d’Azure SQL, faites une demande à votre service achats pour acheter d’autres licences cœur SQL Server avec Software Assurance (ou des licences d’abonnement).

L’achat de licences SQL Server et l’application d’Azure Hybrid Benefit sont moins onéreux que le paiement à l’heure pour SQL Server sur Azure. En achetant suffisamment de licences pour couvrir en totalité l’utilisation planifiée d’Azure SQL, votre organisation profite de la réduction de coûts maximale offerte par l’avantage.

## <a name="assign-licenses-to-azure"></a>Attribuer des licences sur Azure

1. Suivez les conseils donnés dans le portail Azure et la documentation pour choisir une ou plusieurs étendues et leur attribuer des licences SQL Server. Pour plus d’informations, consultez [Créer des attributions de licences SQL Server pour Azure Hybrid Benefit](create-sql-license-assignments.md).
2. Quand vous attribuez des licences, réexaminez l’utilisation d’Azure SQL détectée pour vérifier que les données sont cohérentes avec les autres informations collectées.

## <a name="monitor-usage-and-adjust"></a>Superviser et ajuster l’utilisation

1. Accédez à **Cost Management + Facturation** > **Réservations + Hybrid Benefit**.
1. Un tableau s’affiche ; il indique toutes les attributions de licences Azure Hybrid Benefit que vous avez effectuées ainsi que le pourcentage d’utilisation de chacune de ces licences.
1. Si l’un des pourcentages d’utilisation atteint 100 %, cela signifie que votre organisation paie certaines ressources SQL Server sur la base de tarifs horaires. Renseignez-vous auprès des autres groupes de votre organisation pour déterminer si les niveaux d’utilisation actuels sont temporaires ou s’ils vont perdurer. Dans ce dernier cas, votre organisation doit envisager d’acheter des licences supplémentaires et de les attribuer sur Azure pour réduire les coûts.
1. Si l’utilisation atteint presque 100 %, renseignez-vous pour savoir si l’utilisation est prévue de croître à court terme. Si c’est le cas, vous pouvez acheter et attribuer de manière proactive davantage de licences.

## <a name="establish-a-management-schedule"></a>Planifier la gestion

La section précédente traite de la supervision régulière. Nous vous conseillons également d’établir une planification annuelle ou trimestrielle que vous répéterez à une fréquence régulière. La planification comprend les principales étapes décrites dans l’article :

- Collecter les détails de l’utilisation et de la disponibilité des licences.
- Acheter d’autres licences si nécessaire pour couvrir la future croissance de l’utilisation.
- Attribuer des licences sur Azure.
- Superviser et ajuster l’utilisation dès que nécessaire.
- Répétez le processus chaque année ou selon une autre fréquence adaptée à vos besoins.

## <a name="example-walkthrough"></a>Exemple de procédure pas à pas

Dans l’exemple suivant, imaginez que vous êtes l’administrateur de facturation pour la compagnie d’assurance Contoso. Vous gérez l’avantage Azure Hybrid Benefit pour SQL Server de Contoso.

Le service d’achats vous informe que vous pouvez gérer Azure Hybrid Benefit de manière centralisée pour SQL Server au niveau d’un compte global. Il a lui-même reçu cette information de l’équipe des comptes Microsoft. Cette opportunité vous intéresse, car vous avez récemment rencontré des difficultés avec la gestion d’Azure Hybrid Benefit. Ces difficultés étaient en partie dues au fait que les développeurs ont activé l’avantage (ou ne l’ont pas activé) sur les ressources de manière arbitraire en partageant des scripts entre eux.

Vous accédez à la nouvelle expérience Azure Hybrid Benefit dans la zone Cost Management + Facturation du portail Azure.

Après avoir lu les instructions précédentes de l’article, voici ce que vous avez compris :

  - Contoso doit inscrire les machines virtuelles SQL Server avant d’effectuer d’autres actions.
  - La méthode idéale pour bénéficier de l’avantage est d’attribuer des licences de manière proactive de manière à couvrir l’utilisation prévue.

Vous effectuez ensuite les étapes ci-dessous.

1. Vous suivez les instructions précédentes pour vérifier que les machines virtuelles SQL installées automatiquement sont bien inscrites. Dans ce cadre, vous vous rapprochez des propriétaires d’abonnements pour effectuer l’inscription des abonnements sur lesquels vous n’avez pas les autorisations requises.
1. Vous examinez les données d’utilisation des ressources Azure au cours des derniers mois passés et vous discutez avec vos collègues chez Contoso. Vous déterminez que 2 000 licences cœur SQL Server Entreprise et 750 licences cœur SQL Server Standard, ou 8 750 licences NCL, sont nécessaires pour couvrir l’utilisation prévue d’Azure SQL l’année prochaine. L’utilisation prévue inclut également la migration des charges de travail (1 500 licences SQL Server Entreprise + 750 licences SQL Server Standard = 6 750 licences NCL) et les nouvelles charges de travail Azure SQL (soit 500 licences cœur SQL Server Entreprise ou 2 000 licences NCL).
1. Ensuite, vous vérifiez auprès de votre service achats que les licences nécessaires sont déjà disponibles ou qu’elles seront bientôt achetées. Cette confirmation vous garantit qu’il y a des licences disponibles à attribuer sur Azure.
   - Les licences qui sont déjà utilisées localement peuvent être considérées comme disponibles pour être attribuées sur Azure si les charges de travail associées sont migrées vers Azure. Comme nous l’avons vu précédemment, Azure Hybrid Benefit autorise une double utilisation jusqu’à 180 jours.
   - Vous déterminez que 1 800 licences SQL Server Entreprise et 2 000 licences SQL Server Standard sont disponibles pour être attribuées sur Azure. Les licences disponibles équivalent à 9 200 licences NCL. C’est un peu plus que les 8 750 licences nécessaires (2 000 x 4 + 750 = 8 750).
1. Ensuite, vous attribuez 1 800 licences SQL Server Entreprise et 2 000 licences SQL Server Standard sur Azure. Au terme de cette opération, vous disposez de 9 200 licences NCL que vous pouvez attribuer aux ressources Azure SQL par heure d’exécution. En attribuant un nombre de licences supérieur au nombre dont vous avez besoin, vous anticipez une éventuelle croissance beaucoup plus rapide que celle prévue.

Après cela, vous supervisez l’utilisation des licences attribuées selon une fréquence définie, idéalement tous les mois. Dix mois se sont écoulés et vous voyez que l’utilisation approche les 95 %, ce qui indique une croissance de l’utilisation d’Azure SQL plus rapide que prévu. Vous discutez avec votre service achats pour obtenir des licences supplémentaires à attribuer.

Enfin, vous adoptez un calendrier de revue annuelle des licences. Ce processus de revue comprend les actions suivantes :

- Collecter et analyser les données d’utilisation des licences.
- Vérifier la disponibilité des licences.
- Collaborer avec votre service achats pour obtenir davantage de licences, si nécessaire.
- Mettre à jour les attributions de licences.
- Assurer une surveillance dans le temps.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [migrer vers Azure Hybrid Benefit géré de manière centralisée](transition-existing.md).
- Passez en revue le [FAQ sur Azure Hybrid Benefit géré de manière centralisée](faq-azure-hybrid-benefit-scope.yml).