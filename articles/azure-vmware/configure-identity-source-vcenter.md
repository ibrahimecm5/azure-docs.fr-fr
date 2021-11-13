---
title: Configurer une source d’identité externe pour vCenter
description: Découvrez comment configurer Active Directory via LDAP ou LDAP sécurisé pour vCenter en tant que source d’identité externe.
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: 37478b65572fba2815121b0712d1a0f2e43376dc
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131506293"
---
# <a name="configure-external-identity-source-for-vcenter"></a>Configurer une source d’identité externe pour vCenter



[!INCLUDE [vcenter-access-identity-description](includes/vcenter-access-identity-description.md)]

>[!NOTE]
>Les commandes d’exécution sont exécutées successivement dans l’ordre de leur envoi.

Nous expliquons ici comment :

> [!div class="checklist"]
> * Répertorier toutes les sources d’identité externes existantes intégrées à l’authentification unique vCenter
> * Ajouter Active Directory via LDAP, avec ou sans SSL 
> * Ajouter un groupe AD existant au groupe cloudadmin
> * Supprimer le groupe AD du rôle cloudadmin
> * Supprimer les sources d’identité externes existantes



## <a name="prerequisites"></a>Prérequis

- Établissez une connectivité à partir de votre réseau local vers votre cloud privé.

- Si vous disposez d’AD avec SSL, téléchargez le certificat pour l’authentification AD et chargez-le dans un compte stockage Azure en tant que stockage d’objets blob. Vous devrez ensuite [accorder un accès limité aux ressources Stockage Azure à l’aide des signatures d’accès partagé (SAP)](../storage/common/storage-sas-overview.md).  

- Si vous utilisez le nom de domaine complet, activez la résolution DNS sur votre annuaire AD local.

 

## <a name="list-external-identity"></a>Répertorier l’identité externe



Vous exécuterez l'`Get-ExternalIdentitySources`applet de commande pour répertorier toutes les sources d’identité externes déjà intégrées à VCENTER SSO.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Exécuter la commande** > **Packages** > **Get-ExternalIdentitySources**.

   :::image type="content" source="media/run-command/run-command-overview.png" alt-text="Capture d’écran montrant comment accéder aux commandes d’exécution disponibles." lightbox="media/run-command/run-command-overview.png":::

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   :::image type="content" source="media/run-command/run-command-get-external-identity-sources.png" alt-text="Capture d’écran montrant comment répertorier la source d’identité externe. ":::
   
   | **Champ** | **Valeur** |
   | --- | --- |
   | **Conserver jusqu’à**  |Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60 jours.   |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **getExternalIdentity**.  |
   | **Délai d'expiration**  |  Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Vérifiez les **notifications** ou le volet **Exécuter l’état d’exécution** pour afficher la progression.


## <a name="add-active-directory-over-ldap-with-ssl"></a>Ajouter Active Directory via LDAP avec SSL

Vous exécuterez l'`New-AvsLDAPSIdentitySource`applet de commande pour ajouter un AD sur LDAP avec SSL comme source d’identité externe à utiliser avec l’authentification unique dans vCenter. 

1. Téléchargez le certificat pour l’authentification AD et chargez-le dans un compte Stockage Azure en tant que stockage d’objets blob. Si plusieurs certificats sont requis, chargez chaque certificat individuellement.  

1. Pour chaque certificat, [accordez un accès aux ressources Stockage Azure à l’aide de signatures d’accès partagé (SAP)](../storage/common/storage-sas-overview.md). Ces chaînes SAP sont fournies à la cmdlet en tant que paramètre. 

   >[!IMPORTANT]
   >Veillez à copier chaque chaîne SAP, car elles ne seront plus disponibles une fois que vous aurez quitté cette page.  
   
1. Sélectionnez **Exécuter la commande** > **Packages** > **New-AvsLDAPSIdentitySource**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **Nom**  | Nom convivial de la source d’identité externe, par exemple, **avslap.local**.  |
   | **DomainName**  | Nom de domaine complet du domaine.   |
   | **DomainAlias**  | Pour les sources d’identité Active Directory, le nom NetBIOS du domaine. Ajoutez le nom NetBIOS du domaine AD en tant qu’alias de la source d’identité si vous utilisez des authentifications SSPI.     |
   | **PrimaryUrl**  | URL principale de la source d’identité externe, par exemple **ldap://yourserver:636**.  |
   | **SecondaryURL**  | URL de restauration secondaire en cas d’échec principal.  |
   | **BaseDNUsers**  |  Où rechercher des utilisateurs valides, par exemple, **CN=users,DC=yourserver,DC=internal**.  Le ND de base est nécessaire pour utiliser l’authentification LDAP.  |
   | **BaseDNGroups**  | Où rechercher des groupes, par exemple, **CN=users,DC=yourserver,DC=internal**. Le ND de base est nécessaire pour utiliser l’authentification LDAP.  |
   | **Informations d'identification**  | Les nom d’utilisateur et mot de passe utilisés pour l’authentification auprès de la source AD (et non cloudadmin).  |
   | **CertificateSAS** | Chemin d’accès aux chaînes SAS avec les certificats pour l’authentification auprès de la source AD. Si vous utilisez plusieurs certificats, séparez chaque chaîne SAP par une virgule. Par exemple, **pathtocert1,pathtocert2**.  |
   | **GroupName**  | Groupe dans la source d’identité externe qui donne l’accès cloudadmin. Par exemple, **avs-admins**.  |
   | **Conserver jusqu’à**  | Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60 jours.   |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **addexternalIdentity**.  |
   | **Délai d'expiration**  |  Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Vérifiez les **notifications** ou le volet **Exécuter l’état d’exécution** pour afficher la progression.



## <a name="add-active-directory-over-ldap"></a>Ajouter Active Directory via LDAP

>[!NOTE]
>Nous ne recommandons pas cette méthode. Utilisez plutôt la méthode [Ajouter Active Directory via LDAP avec SSL](#add-active-directory-over-ldap-with-ssl).

Vous exécuterez l'`New-AvsLDAPIdentitySource`applet de commande pour ajouter ad sur LDAP comme source d’identité externe à utiliser avec l’authentification unique dans vCenter. 

1. Sélectionnez **Exécuter la commande** > **Packages** > **New-AvsLDAPIdentitySource**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.
   
   | **Champ** | **Valeur** |
   | --- | --- |
   | **Nom**  | Nom convivial de la source d’identité externe, par exemple, **avslap.local**.  |
   | **DomainName**  | Nom de domaine complet du domaine.    |
   | **DomainAlias**  | Pour les sources d’identité Active Directory, le nom NetBIOS du domaine. Ajoutez le nom NetBIOS du domaine AD en tant qu’alias de la source d’identité si vous utilisez des authentifications SSPI.      |
   | **PrimaryUrl**  | URL principale de la source d’identité externe, par exemple, **ldap://yourserver:389**.  |
   | **SecondaryURL**  | URL de restauration secondaire en cas d’échec principal.  |
   | **BaseDNUsers**  |  Où rechercher des utilisateurs valides, par exemple, **CN=users,DC=yourserver,DC=internal**.  Le ND de base est nécessaire pour utiliser l’authentification LDAP.  |
   | **BaseDNGroups**  | Où rechercher des groupes, par exemple, **CN=users,DC=yourserver,DC=internal**. Le ND de base est nécessaire pour utiliser l’authentification LDAP.  |
   | **Informations d'identification**  | Nom d’utilisateur et mot de passe utilisés pour l’authentification auprès de la source AD (et non cloudadmin).  |
   | **GroupName**  | Groupe pour permettre à l’administrateur cloud d’accéder à votre source d’identité externe, par exemple **avs-admins**.  |
   | **Conserver jusqu’à**  | Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60 jours.   |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **addexternalIdentity**.  |
   | **Délai d'expiration**  |  Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Vérifiez les **notifications** ou le volet **Exécuter l’état d’exécution** pour afficher la progression.


## <a name="add-existing-ad-group-to-cloudadmin-group"></a>Ajouter un groupe AD existant au groupe cloudadmin

Vous exécuterez l'`Add-GroupToCloudAdmins`applet de commande pour ajouter un groupe AD existant au groupe cloudadmin. Les utilisateurs de ce groupe ont des privilèges égaux au rôle cloudadmin (cloudadmin@vsphere.local) défini dans l’authentification unique vCenter.

1. Sélectionnez **Exécuter la commande** > **Packages** > **Add-GroupToCloudAdmins**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **GroupName**  | Nom du groupe à ajouter, par exemple, **VcAdminGroup**.  |
   | **Conserver jusqu’à**  | Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60 jours.   |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **addADgroup**.  |
   | **Délai d'expiration**  |  Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Vérifiez les **notifications** ou le volet **Exécuter l’état d’exécution** pour afficher la progression.



## <a name="remove-ad-group-from-the-cloudadmin-role"></a>Supprimer le groupe AD du rôle cloudadmin

Vous exécuterez l'`Remove-GroupFromCloudAdmins`applet de commande pour supprimer un groupe AD spécifié du rôle cloudadmin. 

1. Sélectionnez **Exécuter la commande** > **Packages** > **Remove-GroupFromCloudAdmins**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **GroupName**  | Nom du groupe à supprimer, par exemple, **VcAdminGroup**.  |
   | **Conserver jusqu’à**  | Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60 jours.   |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **removeADgroup**.  |
   | **Délai d'expiration**  |  Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Vérifiez les **notifications** ou le volet **Exécuter l’état d’exécution** pour afficher la progression.






## <a name="remove-existing-external-identity-sources"></a>Supprimer les sources d’identité externes existantes

Vous exécuterez l'`Remove-ExternalIdentitySources`applet de commande pour supprimer les sources d’identité externes existantes en bloc. 

1. Sélectionnez **Exécuter la commande** > **Packages** > **Get-ExternalIdentitySources**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **Conserver jusqu’à**  | Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60 jours.   |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **remove_externalIdentity**.  |
   | **Délai d'expiration**  |  Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Vérifiez les **notifications** ou le volet **Exécuter l’état d’exécution** pour afficher la progression.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à configurer LDAP et LDAPS, vous pouvez en savoir plus sur les éléments suivants :

- [Comment configurer une stratégie de stockage](configure-storage-policy.md) - Au moins une stratégie de stockage de machine virtuelle est associée à chaque machine virtuelle déployée sur un magasin de données vSAN. Vous pouvez attribuer une stratégie de stockage de machine virtuelle dans un déploiement initial de machine virtuelle ou lorsque vous effectuez d’autres opérations de machine virtuelle, telles que le clonage ou la migration.

- [Concepts d’identité d’Azure VMware Solution](concepts-identity.md) - Utilisez Center pour gérer les charges de travail des machines virtuelles, et NSX-T Manager pour gérer et étendre le cloud privé. La gestion des accès et des identités utilise le rôle CloudAdmin pour vCenter et des droits d’administrateur restreints pour NSX-T Manager. 

 
