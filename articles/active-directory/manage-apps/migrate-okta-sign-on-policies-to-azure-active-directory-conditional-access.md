---
title: Didacticiel pour migrer des stratégies de connexion Okta vers un accès conditionnel Azure Active Directory
titleSuffix: Active Directory
description: Dans ce tutoriel, vous apprenez à migrer les stratégies d’authentification Okta vers l’accès conditionnel Azure Active Directory.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 5cca89b361152d3a4a5635f008f70f145d5d68a0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459428"
---
# <a name="tutorial-migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access"></a>Didacticiel : Migrer des stratégies de connexion Okta vers l’accès conditionnel Azure Active Directory

Dans ce tutoriel, vous découvrirez la manière dont votre organisation peut migrer des stratégies d’authentification globales ou au niveau des applications dans Okta vers des stratégies d’accès conditionnel Azure Active Directory (Azure AD) pour sécuriser l’accès des utilisateurs dans Azure AD et les applications connectées.

Ce didacticiel part du principe que vous avez un locataire Office 365 fédéré à Okta pour la connexion et l’authentification multifacteur (MFA). Vous devez également disposer des agents d’approvisionnement du serveur Azure AD Connect ou du cloud Azure AD Connect configurés pour l’approvisionnement des utilisateurs sur Azure AD.

## <a name="prerequisites"></a>Prérequis

Lorsque vous passez de l’authentification Okta à un accès conditionnel, il est important de comprendre les exigences en matière de licence. Un accès conditionnel exige que les utilisateurs disposent d’une licence Azure AD Premium P1 attribuée avant l’inscription à Azure AD Multifactor Authentication.

Avant d’effectuer les étapes de jonction Azure AD Hybride, vous devez disposer d’informations d’identification d’administrateur d’entreprise dans la forêt locale pour configurer l’enregistrement du point de connexion de service (SCP).

## <a name="catalog-current-okta-sign-on-policies"></a>Cataloguer les stratégies d’authentification Okta actuelles

Pour réussir la transition vers l’accès conditionnel, évaluez les stratégies d’authentification Okta existantes afin de déterminer les cas d’usage et les exigences qui seront transférés vers Azure AD.

1. Vérifiez les stratégies d’authentification globales en accédant à **Sécurité** > **Authentification** > **Connexion**.

   ![Capture d’écran montrant les stratégies d’authentification globales.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies.png)

   Dans cet exemple, la stratégie d’authentification globale applique l’authentification multifacteur à toutes les sessions en dehors de nos zones réseau configurées.

   ![Capture d’écran montrant les stratégies d’authentification globales appliquant l’authentification multifacteur.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/global-sign-on-policies-enforce-mfa.png)

2. Accédez à **Applications** et vérifiez les stratégies d’authentification au niveau des applications. Sélectionnez **Applications** dans le sous-menu, puis votre instance connectée Office 365 dans la liste **Applications actives**.

3. Sélectionnez **Authentification** et faites défiler jusqu’au bas de la page.

Dans l’exemple suivant, la stratégie d’authentification pour l’application Office 365 présente quatre règles distinctes :

- **Appliquer l’authentification multifacteur pour les sessions mobiles** : Exige l’authentification multifacteur pour chaque session d’authentification moderne ou de navigateur sur iOS ou Android.
- **Autoriser les appareils Windows de confiance** : Empêche les appareils Okta de confiance d’être sollicités pour une vérification ou des facteurs supplémentaires.
- **Exiger l’authentification multifacteur sur les appareils Windows non approuvés** : Exige l’authentification multifacteur pour chaque session d’authentification moderne ou de navigateur sur les appareils Windows non approuvés.
- **Bloquer l’authentification héritée** : Empêche tout client d’authentification hérité de se connecter au service.

  ![Capture d’écran montrant les règles d’authentification d’Office 365.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-on-rules.png)

## <a name="configure-condition-prerequisites"></a>Configurer les conditions préalables

Les stratégies d’accès conditionnel peuvent être configurées pour correspondre aux conditions d’Okta pour la plupart des scénarios sans configuration supplémentaire.

Dans certains scénarios, vous pouvez avoir besoin d’une configuration supplémentaire avant de configurer les stratégies d’accès conditionnel. Les deux scénarios connus au moment de la rédaction de cet article sont les suivants :

- **Emplacements réseau Okta vers des emplacements nommés dans Azure AD** : Suivez les instructions fournies dans [Utilisation de la condition d’emplacement dans une stratégie d’accès conditionnel](../conditional-access/location-condition.md#named-locations) pour configurer des emplacements nommés dans Azure AD.
- **Confiance des appareils Okta vers l’accès conditionnel basé sur les appareils** : L’accès conditionnel offre deux options possibles lorsque vous évaluez l’appareil d’un utilisateur :

  - [Utiliser Jonction Azure AD Hybride](#hybrid-azure-ad-join-configuration), qui est une fonctionnalité activée au sein du serveur Azure AD Connect qui synchronise les appareils actuels de Windows, tels que Windows 10, Windows Server 2016 et Windows Server 2019, avec Azure AD.
  - [Inscrire l’appareil dans Endpoint Manager](#configure-device-compliance) et attribuer une stratégie de conformité.

### <a name="hybrid-azure-ad-join-configuration"></a>Configuration de la jointure hybride Azure AD

Pour activer Jonction Azure AD Hybride sur votre serveur Azure AD Connect, exécutez l’Assistant de configuration. Vous devrez prendre des mesures après la configuration pour inscrire automatiquement les appareils.

>[!NOTE]
>La jointure hybride Azure AD n’est pas prise en charge avec les agents d’approvisionnement Azure AD Connect cloud.

1. Pour activer Jonction Azure AD Hybride, suivez ces [instructions](../devices/hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

1. Sur la page **Configuration SCP**, sélectionnez le menu déroulant **Service d’authentification**. Choisissez l’URL de votre fournisseur de fédération Okta, puis sélectionnez **Ajouter**. Entrez les informations d’identification de votre administrateur d’entreprise local, puis sélectionnez **Suivant**.

   ![Capture d’écran montrant Configuration SCP.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/scp-configuration.png)

1. Si vous avez bloqué l’authentification héritée sur les clients Windows dans la stratégie d’authentification globale ou au niveau des applications, créez une règle pour permettre au processus Jonction Azure AD Hybride de se terminer.

1. Autorise l’ensemble de la pile d’authentification héritée pour tous les clients Windows. Vous pouvez également contacter le support d’Okta pour activer sa chaîne cliente personnalisée sur vos stratégies d’application existantes.

### <a name="configure-device-compliance"></a>Configurer la conformité des appareils

Jonction Azure AD Hybride est un remplacement direct de la confiance des appareils Okta sur Windows. Les stratégies d’accès conditionnel peuvent également examiner la conformité des appareils pour les appareils qui ont été inscrits entièrement dans Endpoint Manager :

- **Aperçu de la conformité** : Reportez-vous aux [stratégies de conformité des appareils dans Intune](/mem/intune/protect/device-compliance-get-started#:~:text=Reference%20for%20non-compliance%20and%20Conditional%20Access%20on%20the,applicable%20%20...%20%203%20more%20rows).
- **Conformité des appareils** : Créez des [stratégies dans Intune](/mem/intune/protect/create-compliance-policy).
- **Inscription à Windows** : Si vous avez choisi de déployer Jonction Azure AD Hybride, vous pouvez déployer une autre stratégie de groupe pour terminer le [processus d’inscription automatique de ces appareils dans Intune](/windows/client-management/mdm/enroll-a-windows-10-device-automatically-using-group-policy).
- **Inscription iOS/iPadOS** : Avant d’inscrire un appareil iOS, vous devez effectuer [des configurations supplémentaires](/mem/intune/enrollment/ios-enroll) dans la console d’Endpoint Manager.
- **Inscription Android** : Avant d’inscrire un appareil Android, vous devez effectuer [des configurations supplémentaires](/mem/intune/enrollment/android-enroll) dans la console d’Endpoint Manager.

## <a name="configure-azure-ad-multi-factor-authentication-tenant-settings"></a>Configurer les paramètres de locataire Azure AD Multifactor Authentication

Avant de passer à l’accès conditionnel, vérifiez les paramètres de locataire Azure AD Multifactor Authentication de base pour votre organisation.

1. Accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec un compte Administrateur général.

1. Sélectionnez **Azure Active Directory** > **Utilisateurs** > **Multifactor Authentication** pour accéder au portail Azure AD Multifactor Authentication hérité.

   ![Capture d’écran montrant le portail Azure AD Multifactor Authentication hérité.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/legacy-azure-ad-portal.png)

   Vous pouvez également utiliser le lien hérité menant au [portail Azure AD Multifactor Authentication](https://aka.ms/mfaportal).

1. Dans le menu **Multifactor Authentication** hérité, modifiez le menu d’état en passant par **Activé** et **Appliqué** pour confirmer que l’authentification multifacteur héritée n’est activée pour aucun utilisateur. Si votre locataire a des utilisateurs dans les vues suivantes, vous devez les désactiver dans le menu hérité. Seules les stratégies d’accès conditionnel prendront effet sur leur compte.

   ![Capture d’écran montrant la désactivation d’un utilisateur dans le portail Azure AD Multifactor Authentication hérité.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/disable-user-legacy-azure-ad-portal.png)

   Le champ **Appliqué** doit également être vide.

1. Sélectionnez l’option **Paramètres de service**. Changez la sélection **Mots de passe d’application** en **Ne pas autoriser les utilisateurs à créer des mots de passe d’application pour se connecter à des applications sans navigateur**.

   ![Capture d’écran montrant les paramètres de mot de passe d’application qui ne permettent pas aux utilisateurs de créer des mots de passe d’application.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/app-password-selection.png)

1. Assurez-vous que les cases **Ignorer l’authentification multifacteur pour les demandes issues d’utilisateurs fédérés provenant de mon intranet** et **Permettre aux utilisateurs de mémoriser l’authentification multifacteur sur des appareils de confiance (entre 1 et 365 jours)** sont décochées, puis sélectionnez **Enregistrer**.

  >[!NOTE]
   >Consultez les [meilleures pratiques relatives à la configuration des paramètres d’invite de l’authentification multifacteur](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

![Capture d’écran montrant les cases à cocher non sélectionnées dans le portail Azure AD Multifactor Authentication hérité.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/uncheck-fields-legacy-azure-ad-portal.png)

## <a name="configure-conditional-access-policies"></a>Configurer des stratégies d’accès conditionnel

Une fois que vous avez configuré les éléments requis et établi les paramètres de base, il est temps de créer la première stratégie d’accès conditionnel.

1. Pour configurer des stratégies d’accès conditionnel dans Azure AD, rendez-vous sur le [portail Azure](https://portal.azure.com). Dans **Gérer Azure Active Directory**, sélectionnez **Afficher**.

   Configurez les stratégies d’accès conditionnel en respectant les [meilleures pratiques de déploiement et de conception de l’accès conditionnel](../conditional-access/plan-conditional-access.md#understand-conditional-access-policy-components).

1. Pour imiter la stratégie d’authentification multifacteur globale d’Okta, [créez une stratégie](../conditional-access/howto-conditional-access-policy-all-users-mfa.md).

1. Créez une [règle d’accès conditionnel basée sur la confiance des appareils](../conditional-access/require-managed-devices.md).

   Comme toutes les autres stratégies de ce tutoriel, cette stratégie peut cibler une application spécifique, un groupe de test d’utilisateurs, ou les deux.

   ![Capture d’écran montrant le test d’un utilisateur.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/test-user.png)

   ![Capture d’écran montrant la réussite du test d’un utilisateur.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/success-test-user.png)

1. Après avoir configuré la stratégie basée sur l’emplacement et la stratégie de confiance des appareils, il est temps de configurer la stratégie [Bloquer l’authentification héritée](../conditional-access/howto-conditional-access-policy-block-legacy.md) équivalente.

Grâce à ces trois stratégies d’accès conditionnel, l’expérience originale des stratégies d’authentification Okta a été reproduite dans Azure AD. Les étapes suivantes consistent à inscrire l’utilisateur via Azure AD Multifactor Authentication et à tester les stratégies.

## <a name="enroll-pilot-members-in-azure-ad-multi-factor-authentication"></a>Inscrire des membres du pilote à Azure AD Multifactor Authentication

Une fois que vous avez configuré les stratégies d’accès conditionnel, les utilisateurs doivent s’inscrire aux méthodes Azure AD Multifactor Authentication. Les utilisateurs peuvent être amenés à s’inscrire par le biais de différentes méthodes.

1. Pour une inscription individuelle, dirigez les utilisateurs vers le [volet de connexion Microsoft](https://aka.ms/mfasetup) pour qu’ils entrent manuellement les informations d’inscription.

1. Les utilisateurs peuvent se rendre sur la [page Informations de sécurité Microsoft](https://aka.ms/mysecurityinfo) pour entrer les informations ou gérer le formulaire d’inscription à l’authentification multifacteur.

Consultez [ce guide](../authentication/howto-registration-mfa-sspr-combined.md) pour bien comprendre la procédure d’inscription à l’authentification multifacteur.

Accédez au [volet de connexion Microsoft](https://aka.ms/mfasetup). Après vous être connecté avec l’authentification multifacteur Okta, vous êtes invité à vous inscrire à l’authentification multifacteur avec Azure AD.

>[!NOTE]
>Si l’inscription a déjà eu lieu pour un utilisateur, il est dirigé vers la page d’information **Ma sécurité** après avoir répondu à l’invite d’authentification multifacteur.
Voir la [documentation utilisateur pour l’inscription à l’authentification multifacteur](../user-help/security-info-setup-signin.md).

## <a name="enable-conditional-access-policies"></a>Activer des stratégies d’accès conditionnel

1. Pour déployer les tests, modifiez les stratégies créées dans les exemples précédents pour **Connexion d’utilisateur de test activée**.

   ![Capture d’écran montrant l’activation d’un utilisateur de test.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/enable-test-user.png)

1. Dans le volet **Connexion** Office 365 suivant, l’utilisateur de test John Smith est invité à se connecter avec l’authentification multifacteur Okta et Azure AD Multifactor Authentication.

   ![Capture d’écran montrant la page Connexion Azure.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/sign-in-through-okta.png)

1. Effectuez la vérification de l’authentification multifacteur par le biais d’Okta.

   ![Capture d’écran montrant la vérification de l’authentification multifacteur via Okta.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta.png)

1. Une fois que l’utilisateur a terminé la vérification de l’authentification multifacteur Okta, il est invité à accéder à l’accès conditionnel. Vérifiez que les stratégies ont été correctement configurées et qu’elles se trouvent dans les conditions pour être déclenchées pour l’authentification multifacteur.

   ![Capture d’écran montrant la vérification de l’authentification multifacteur via Okta pour l’accès conditionnel.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-verification-through-okta-prompted-ca.png)

## <a name="cut-over-from-sign-on-to-conditional-access-policies"></a>Basculer de stratégies d’authentification à des stratégies d’accès conditionnel

Après avoir effectué un test approfondi sur les membres du pilote afin de vous assurer que l’accès conditionnel fonctionne comme prévu, les autres membres de l’organisation peuvent être ajoutés aux stratégies d’accès conditionnel une fois l’inscription terminée.

Pour éviter les invites en double entre Azure AD Multifactor Authentication et l’authentification multifacteur Okta, refusez l’authentification multifacteur Okta en modifiant les stratégies d’authentification.

La dernière étape de migration vers l’accès conditionnel peut être effectuée par étapes ou par basculement.

1. Accédez à la console d’administration d’Okta, sélectionnez **Sécurité** > **Authentification**, puis rendez-vous dans **Stratégie de connexion**.

   >[!NOTE]
   > Définissez les stratégies globales sur **Inactive** uniquement si toutes les applications Okta sont protégées par leurs propres stratégies d’authentification.
1. Définissez la stratégie **Appliquer l’authentification multifacteur** sur **Inactive**. Vous pouvez également affecter la stratégie à un nouveau groupe qui n’inclut pas les utilisateurs Azure AD.

   ![Capture d’écran montrant la stratégie globale d’authentification multifacteur comme Inactive.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/mfa-policy-inactive.png)

1. Dans le volet de la stratégie d’authentification au niveau des applications, mettez à jour les stratégies sur **Inactive** en sélectionnant l’option **Désactiver la règle**. Vous pouvez également affecter la stratégie à un nouveau groupe qui n’inclut pas les utilisateurs Azure AD.

1. Assurez-vous qu’au moins une stratégie d’authentification au niveau des applications est activée pour l’application qui autorise l’accès sans authentification multifacteur.

   ![Capture d’écran montrant l’accès à l’application sans authentification multifacteur.](media/migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access/application-access-without-mfa.png)

1. Après avoir désactivé les stratégies d’authentification Okta ou exclu les utilisateurs Azure AD migrés des groupes d’application, *seule* une invite d’accès conditionnel est envoyée aux utilisateurs lors de leur prochaine connexion.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la migration d’Okta vers Azure AD, consultez :

- [Migrer des applications d’Okta vers Azure AD](migrate-applications-from-okta-to-azure-active-directory.md)
- [Migrer la fédération Okta vers Azure AD](migrate-okta-federation-to-azure-active-directory.md)
- [Migrer l’approvisionnement de synchronisation Okta vers la synchronisation basée sur Azure AD Connect](migrate-okta-sync-provisioning-to-azure-active-directory.md)
