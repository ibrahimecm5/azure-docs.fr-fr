---
title: Guide pratique pour télécharger les journaux dans Azure Active Directory | Microsoft Docs
description: Découvrez comment télécharger les journaux d’activité dans Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/14/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bbf95fe288bb81ac8a1580e56244bea11e52821
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995064"
---
# <a name="how-to-download-logs-in-azure-active-directory"></a>Procédure : Télécharger les journaux dans Azure Active Directory

À partir du portail Azure Active Directory (Azure AD), vous pouvez accéder à trois types de journaux d’activité :

- **[Connexions](concept-sign-ins.md)**  : Informations sur les connexions et la manière dont vos ressources sont utilisées par vos utilisateurs.
- **[Audit](concept-audit-logs.md)**  : Informations sur les modifications appliquées à votre locataire, telles que la gestion des utilisateurs et des groupes ou les mises à jour appliquées aux ressources de votre locataire.
- **[Approvisionnement](concept-provisioning-logs.md)**  : Activités réalisées par le service d’approvisionnement, telles que la création d’un groupe dans ServiceNow ou l’importation d’un utilisateur à partir de Workday.

Azure AD stocke les données dans ces journaux pendant une période limitée. En tant qu’administrateur informatique, vous pouvez télécharger vos journaux d’activité pour disposer d’une sauvegarde à long terme.

Cet article explique comment télécharger les journaux d’activité dans Azure AD.  

## <a name="what-you-should-know"></a>Ce que vous devez savoir

- Dans le portail Azure AD, vous trouverez plusieurs points d’entrée dans les journaux d’activité. Par exemple, la section **Activité** dans la page [Utilisateurs](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/MsGraphUsers) ou [Groupes](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups). Toutefois, un seul endroit offre une vue des journaux initialement non filtrée : la section **Supervision** dans la page [Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview).    

- Azure AD conserve les journaux d’activité uniquement pendant une certaine période. Pour plus d’informations, consultez [Pendant combien de temps les données de rapport sont-elles conservées par Azure AD ?](reference-reports-data-retention.md) 

- Lorsque vous téléchargez les journaux, vous pouvez définir la durée de leur stockage. 

- Vous pouvez télécharger jusqu’à 250 000 enregistrements. Si vous souhaitez télécharger davantage de données, utilisez l’API de création de rapports.

- Votre téléchargement est basé sur le filtre que vous avez défini. 

- Azure AD prend en charge les formats suivants pour votre téléchargement :

    - **CSV** 

    - **JSON** 

- Les horodatages dans les fichiers téléchargés sont toujours basés sur le temps universel coordonné (UTC).



## <a name="what-license-do-you-need"></a>De quelle licence avez-vous besoin ?

L’option permettant de télécharger les données d’un journal d’activité est disponible dans toutes les éditions d’Azure AD.

Vous pouvez également télécharger les journaux d’activité à l’aide de Microsoft Graph. Toutefois, pour télécharger les journaux grammaticalement, vous avez besoin d’une licence Premium.


## <a name="who-can-do-it"></a>Qui peut faire cette tâche ?

Le rôle d’administrateur général permet d’effectuer cette tâche, mais utilisez un compte avec des privilèges inférieurs. L’accès aux journaux d’audit est possible avec les rôles suivants :

- Lecteur de rapport
- Lecteur général
- Security Administrator
- Lecteur de sécurité


## <a name="how-to-do-it"></a>Comment procéder


**Pour télécharger un journal d’activité :**

1. Accédez à la vue du journal d’activité qui vous intéresse :
 
    - [Journal des connexions](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)
    
    - [Journal d’audit](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)    
       
    - [Journal de provisionnement](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/ProvisioningEvents)    
   

2.  **Ajoutez** le filtre nécessaire.  

    ![Ajouter un filtre](./media/\howto-download-logs/add-filter.png)    

3. **Téléchargez** les données.

    ![Télécharger le journal](./media/\howto-download-logs/download-log.png)

## <a name="next-steps"></a>Étapes suivantes

- [Journaux des connexions dans Azure AD](concept-sign-ins.md)
- [Journaux d’audit dans Azure AD](concept-audit-logs.md)