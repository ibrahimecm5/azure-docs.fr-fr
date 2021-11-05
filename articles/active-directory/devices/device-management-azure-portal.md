---
title: Gérer les appareils dans Azure AD à l’aide du portail Azure
description: Cet article explique comment utiliser le portail Azure pour gérer les identités des appareils et superviser les informations relatives aux événements associés.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 10/14/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9cdb24ac332530a8294cd6a39b5fe58ab2727ed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049935"
---
# <a name="manage-device-identities-by-using-the-azure-portal"></a>Gérer les identités d’appareil en utilisant le portail Azure

Azure Active Directory (Azure AD) vous fournit un emplacement central pour gérer les identités d’appareil et superviser les informations des événements associés.

[![Capture d’écran montrant la vue d’ensemble des appareils dans le portail Azure.](./media/device-management-azure-portal/devices-azure-portal.png)](./media/device-management-azure-portal/devices-azure-portal.png#lightbox)

Vous pouvez accéder à la vue d’ensemble des appareils en effectuant ces étapes :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à **Azure Active Directory** > **Appareils**.

À partir de la vue d’ensemble des appareils, vous pouvez voir le nombre total d’appareils, les appareils obsolètes, les appareils non conformes et les appareils non gérés. Vous trouvez aussi des liens vers Intune, Accès conditionnel, Clés BitLocker et la supervision de base. 

Les nombres d’appareils sur la page vue d’ensemble ne sont pas mis à jour en temps réel. Les modifications doivent être reflétées à une fréquence de quelques heures.

À partir de là, vous pouvez accéder à **Tous les appareils** pour :

- Identifier les appareils, à savoir :
   - appareils qui ont été joints ou inscrits dans Azure AD ;
   - Les appareils déployés via [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot).
   - Les imprimantes utilisant [Impression universelle](/universal-print/fundamentals/universal-print-getting-started).
- Effectuer des tâches de gestion des identités d’appareil comme l’activation, la désactivation, la suppression ou la gestion.
   - Les options de gestion pour les [imprimantes](/universal-print/fundamentals/) et [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) sont limitées dans Azure AD. Ces appareils doivent être gérés à partir de leurs interfaces d’administration respectives.
- Configurer les paramètres d’identité de votre appareil.
- Activer ou désactiver Enterprise State Roaming.
- Examiner les journaux d’audit liés aux appareils.
- Télécharger des appareils (préversion).

[![Capture d’écran montrant la vue Tous les appareils dans le portail Azure.](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

> [!TIP]
> - Les appareils Windows 10 joints à Azure AD Hybride n’ont pas de propriétaire. Si vous recherchez un appareil par propriétaire et que vous ne le trouvez pas, recherchez par ID d’appareil.
>
> - Si vous voyez un appareil qui est **Joint à Azure AD Hybride** avec l’état **En attente** dans la colonne **Inscrit**, cela signifie que l’appareil a été synchronisé à partir d’Azure AD Connect et qu’il attend d’effectuer l’inscription à partir du client. Consultez [Guide pratique pour planifier votre implémentation de la jonction à Azure AD Hybride](hybrid-azuread-join-plan.md). Pour plus d’informations, consultez [Questions fréquentes (FAQ) sur la gestion des appareils](faq.yml).
>
> - Pour certains appareils iOS, les noms d’appareil qui contiennent des apostrophes peuvent utiliser des caractères différents qui ressemblent à des apostrophes. La recherche de ces appareils est donc un peu délicate. Si vous ne voyez pas les résultats de recherche corrects, vérifiez que la chaîne de recherche contient le caractère d’apostrophe correspondant.

## <a name="manage-an-intune-device"></a>Gérer un appareil Intune

Si vous disposez des droits nécessaires pour gérer des appareils dans Intune, vous pouvez gérer les appareils pour lesquels la gestion des appareils mobiles indique **Microsoft Intune**. Si l’appareil n’est pas inscrit auprès de Microsoft Intune, l’option **Gérer** n’est pas disponible.

## <a name="enable-or-disable-an-azure-ad-device"></a>Activer ou désactiver un appareil Azure AD

Il existe deux façons d’activer ou de désactiver des appareils :

- La barre d’outils dans la page **Tous les appareils**, après avoir sélectionné un ou plusieurs appareils.
- La barre d’outils, après la sélection d’un appareil spécifique.

> [!IMPORTANT]
> - Vous devez être administrateur général, administrateur Intune ou administrateur d’appareil cloud dans Azure AD pour activer ou désactiver un appareil. 
> - La désactivation d’un appareil l’empêche de s’authentifier via Azure AD. Ceci l’empêche d’accéder à vos ressources Azure AD qui sont protégées par un accès conditionnel basé sur l’appareil et d’utiliser des informations d’identification Windows Hello Entreprise.
> - La désactivation d’un appareil entraîne la révocation du jeton d’actualisation principal et des éventuels jetons d’actualisation sur l’appareil.
> - Les imprimantes ne peuvent pas être activées ou désactivées dans Azure AD.

## <a name="delete-an-azure-ad-device"></a>Supprimer un appareil Azure AD

Il existe deux façons de supprimer un appareil :

- La barre d’outils dans la page **Tous les appareils**, après avoir sélectionné un ou plusieurs appareils.
- La barre d’outils, après la sélection d’un appareil spécifique.

> [!IMPORTANT]
> - Pour supprimer un appareil, vous devez être administrateur d’appareil cloud, administrateur Intune ou administrateur général dans Azure AD.
> - Les imprimantes et les appareils Windows Autopilot ne peuvent pas être supprimés dans Azure AD.
> - La suppression d’un appareil :
>    - Empêche celui-ci d’accéder à vos ressources Azure AD.
>    - Supprime tous les détails attachés à l’appareil. Par exemple, les clés BitLocker pour les appareils Windows.  
>    - Est une activité non récupérable. Nous ne la recommandons pas, sauf si elle est nécessaire.

Si un appareil est géré par une autre autorité de gestion, comme Microsoft Intune, veillez à qu’il soit réinitialisé ou mis hors service avant de le supprimer. Consultez [Comment gérer les appareils obsolètes](manage-stale-devices.md) avant de supprimer un appareil.

## <a name="view-or-copy-a-device-id"></a>Visualiser ou copier un ID d’appareil

Vous pouvez utiliser un ID d’appareil pour vérifier les informations d’ID de l’appareil ou résoudre les problèmes via PowerShell. Pour accéder à l’option de copie, sélectionnez l’appareil.

![Capture d’écran montrant un ID d’appareil et le bouton Copier.](./media/device-management-azure-portal/35.png)
  
## <a name="view-or-copy-bitlocker-keys"></a>Afficher ou copier des clés BitLocker

Vous pouvez visualiser et copier des clés BitLocker pour permettre aux utilisateurs de récupérer leurs lecteurs chiffrés. Ces clés sont disponibles seulement pour les appareils Windows chiffrés qui stockent leurs clés dans Azure AD. Vous pouvez trouver ces clés quand vous visualisez les détails d’un appareil en sélectionnant **Afficher la clé de récupération**. La sélection d’**Afficher la clé de récupération** génère un journal d’audit que vous pouvez trouver dans la catégorie `KeyManagement`.

![Capture d’écran montrant comment visualiser les clés BitLocker.](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

Pour visualiser ou copier des clés BitLocker, vous devez être le propriétaire de l’appareil ou avoir un des rôles suivants :

- Administrateur d’appareil cloud
- Administrateur général
- Administrateur du support technique
- Administrateur de services Intune
- Security Administrator
- Lecteur de sécurité

## <a name="device-list-filtering-preview"></a>Filtrage de la liste des appareils (préversion)

Auparavant, vous pouviez filtrer la liste des appareils seulement par activité et par état activé. Dans cette préversion, vous pouvez filtrer la liste des appareils selon ces attributs des appareils :

- État activé
- État conforme
- Type de jonction (jonction Azure AD, jonction Azure AD Hybride, inscrit sur Azure AD)
- Timestamp d’activité
- Système d’exploitation
- Type d’appareil (imprimante, machine virtuelle sécurisée, appareil partagé, appareil inscrit)

Pour activer la fonctionnalité de filtrage en préversion dans la vue **Tous les appareils** :

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez à **Azure Active Directory** > **Appareils**.
1. Sélectionnez la bannière qui indique **Essayez les améliorations apportées au filtrage des appareils. Cliquez pour activer la préversion.**

   ![Activer la fonctionnalité de filtrage en préversion](./media/device-management-azure-portal/device-filter-preview-enable.png)

Vous pouvez désormais ajouter des filtres à votre vue **Tous les appareils**.

## <a name="download-devices-preview"></a>Télécharger les appareils (préversion)

Les administrateurs d’appareil cloud, les administrateurs Intune et les administrateurs généraux peuvent utiliser l’option **Télécharger les appareils (préversion)**  pour exporter un fichier CSV qui liste les appareils. Vous pouvez appliquer des filtres pour déterminer les appareils à lister. Si vous n’appliquez aucun filtre, tous les appareils sont listés. Une tâche d’exportation peut s’exécuter pendant jusqu’à une heure, en fonction de vos sélections.

La liste exportée comprend ces attributs d’identité d’appareil :

`accountEnabled, approximateLastLogonTimeStamp, deviceOSType, deviceOSVersion, deviceTrustType, dirSyncEnabled, displayName, isCompliant, isManaged, lastDirSyncTime, objectId, profileType, registeredOwners, systemLabels, registrationTime, mdmDisplayName`

## <a name="configure-device-settings"></a>Configurer les paramètres de l’appareil

Si vous voulez gérer les identités d’appareil en utilisant le portail Azure, ces appareils doivent être [inscrits ou joints](overview.md) à Azure AD. En tant qu’administrateur, vous pouvez contrôler le processus d’inscription et de jonction d’appareils en configurant les paramètres d’appareil suivants.

Pour afficher ou gérer les paramètres d’un appareil dans le portail Azure, vous devez être titulaire de l’un des rôles suivants :

- Administrateur général
- Administrateur d’appareil cloud
- Lecteur général
- Lecteur de répertoire

![Capture d’écran montrant les paramètres de l’appareil en rapport avec Azure AD.](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Les utilisateurs peuvent joindre des appareils à Azure AD** : ce paramètre vous permet de sélectionner les utilisateurs qui peuvent inscrire leurs appareils en tant qu’appareils joints à Azure AD. La valeur par défaut est **Tous**.

   > [!NOTE]
   > Le paramètre **Les utilisateurs peuvent joindre des appareils à Azure AD** s’applique seulement à la jonction à Azure AD sous Windows 10. Il ne s’applique pas aux appareils joints à Azure AD Hybride, aux [machines virtuelles jointes à Azure AD dans Azure](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) et aux appareils joints à Azure AD qui utilisent le [mode de déploiement automatique Windows Autopilot](/mem/autopilot/self-deploying), car ces méthodes fonctionnent dans un contexte sans utilisateur.

- **Administrateurs locaux supplémentaires sur les appareils joints à Azure AD** : ce paramètre vous permet de sélectionner les utilisateurs auxquels sont octroyés les droits d’administrateur local sur un appareil. Ces utilisateurs sont ajoutés au rôle Administrateurs d’appareils dans Azure AD. Les administrateurs généraux dans Azure AD et les propriétaires d’appareils bénéficient des droits d’administrateur local par défaut. Cette option est une fonctionnalité de l’édition Premium disponible via des produits comme Azure AD Premium ou Enterprise Mobility + Security.
- **Les utilisateurs peuvent inscrire leurs appareils sur Azure AD** : vous devez configurer ce paramètre pour permettre aux utilisateurs d’inscrire des appareils Windows 10 personnels, iOS, Android et macOS auprès d’Azure AD. Si vous sélectionnez **Aucun**, les appareils ne peuvent pas s’inscrire auprès d’Azure AD. L’inscription auprès de Microsoft Intune ou de la Gestion des appareils mobiles (MDM) pour Microsoft 365 nécessite l’enregistrement. Si vous avez configuré un de ces services, l’option **TOUS** est sélectionnée et l’option **AUCUN** n’est pas disponible.
- **Exiger une authentification multifacteur pour inscrire ou joindre des appareils à Azure AD** : ce paramètre vous permet de spécifier si les utilisateurs doivent fournir un autre facteur d’authentification pour joindre ou inscrire leur appareil à Azure AD. La valeur par défaut est **No**. Nous vous recommandons d’exiger l’authentification multifacteur quand un appareil est inscrit ou joint. Avant d’activer l’authentification multifacteur pour ce service, vous devez vérifier que l’authentification multifacteur est configurée pour les utilisateurs qui inscrivent leurs appareils. Pour plus d’informations sur les services d’authentification multifacteur Azure AD, consultez [Bien démarrer avec l’authentification multifacteur Azure AD](../authentication/concept-mfa-howitworks.md). 

   > [!NOTE]
   > Le paramètre **Exiger une authentification multifacteur pour inscrire ou joindre des appareils à Azure AD** s’applique aux appareils joints à Azure AD (avec quelques exceptions) et aux appareils inscrits auprès d’Azure AD. Il ne s’applique pas aux appareils joints à Azure AD Hybride, aux [machines virtuelles jointes à Azure AD dans Azure](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) et aux appareils joints à Azure AD qui utilisent le [mode de déploiement automatique Windows Autopilot](/mem/autopilot/self-deploying).

   > [!IMPORTANT]
   > - Nous vous recommandons d’utiliser l’action utilisateur [Inscrire ou joindre des appareils](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) dans l’accès conditionnel pour appliquer l’authentification multifacteur pour la jonction ou l’inscription d’un appareil. 
   > - Vous devez configurer ce paramètre sur **Non** si vous utilisez la stratégie d’accès conditionnel pour exiger l’authentification multifacteur. 

- **Nombre maximal d’appareils** : ce paramètre vous permet de sélectionner le nombre maximal d’appareils joints à Azure AD ou inscrits auprès d’Azure AD qu’un utilisateur peut avoir dans Azure AD. Si un utilisateur atteint cette limite, il ne peut plus ajouter d’appareils tant qu’un ou plusieurs appareils existants n’ont pas été supprimés. La valeur par défaut est de **50**. Vous pouvez augmenter la valeur jusqu’à 100. Si vous entrez une valeur supérieure à 100, Azure AD la définit néanmoins sur 100. Vous pouvez aussi utiliser **Illimité** pour n’appliquer aucune limite autre que les limites de quotas existantes.

   > [!NOTE]
   > Le paramètre **Nombre maximal d’appareils** s’applique aux appareils joints à Azure AD ou inscrits auprès d’Azure AD. Ce paramètre ne s’applique pas aux appareils joints à Azure AD hybride.

- **Enterprise State Roaming** : pour plus d’informations sur ce paramètre, consultez [l’article Vue d’ensemble](enterprise-state-roaming-overview.md).

## <a name="audit-logs"></a>Journaux d’audit

Les activités des appareils sont visibles dans les journaux d’activité. Ces journaux comprennent les activités déclenchées par le service d’inscription des appareils et par les utilisateurs :

- Création d’un appareil et ajout de propriétaires/d’utilisateurs sur l’appareil
- Modification des paramètres de l’appareil
- Opérations concernant les appareils, comme la suppression ou la mise à jour d’un appareil

Le point d’entrée des données d’audit est **Journaux d’audit** dans la section **Activité** de la page **Appareils**.

Un journal d’audit inclut une vue Liste par défaut, qui indique les informations suivantes :

- La date et l’heure de l’occurrence.
- Les cibles.
- L’initiateur/acteur d’une activité.
- Activité.

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="Capture d’écran montrant un tableau dans la section Activité de la page Appareils. Le tableau liste la date, la cible, l’acteur et l’activité pour quatre journaux d’audit." border="false":::

Vous pouvez personnaliser la vue de liste en sélectionnant **Colonnes** dans la barre d’outils :

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="Capture d’écran montrant la barre d’outils de la page Appareils." border="false":::

Pour réduire les données affichées à un niveau qui vous convient, vous pouvez les filtrer en utilisant ces champs :

- **Catégorie**
- **Type de ressource d’activité**
- **Activité**
- **Plage de dates**
- **Cible**
- **Initié par (intervenant)**

Vous pouvez également rechercher des entrées spécifiques.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="Capture d’écran montrant les contrôles de filtrage des données d’audit." border="false":::

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour gérer les appareils obsolètes dans Azure AD](manage-stale-devices.md)
- [Résoudre les problèmes d’état des appareils en attente](/troubleshoot/azure/active-directory/pending-devices)
