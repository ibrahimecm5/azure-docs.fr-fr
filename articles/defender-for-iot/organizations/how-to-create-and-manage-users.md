---
title: Créer et gérer des utilisateurs
description: Créer et gérer des utilisateurs de capteurs et de la console de gestion locale. Les utilisateurs peuvent se voir attribuer le rôle d’administrateur, d’analyste de sécurité ou d’utilisateur en lecture seule.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 73e08c7cdca6e57445566a99cb779a322306f944
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318937"
---
# <a name="about-defender-for-iot-console-users"></a>À propos des utilisateurs de la console Defender pour IoT

Cet article présente comment créer et gérer des utilisateurs de capteurs et de la console de gestion locale. Les rôles d’utilisateur sont ceux d’administrateur, d’analyste de sécurité ou d’utilisateur en lecture seule. Chaque rôle est associé à une plage d’autorisations pour les outils du capteur ou la console de gestion locale. Les rôles sont conçus pour faciliter un accès granulaire et sécurisé à Microsoft Defender pour IoT.

Certaines fonctionnalités sont également disponibles pour suivre les activités des utilisateurs et activer la connexion Active Directory.

Par défaut, chaque capteur et chaque console de gestion locale comporte un utilisateur *CyberX et support*. Ces utilisateurs ont accès aux outils avancés pour la résolution des problèmes et la configuration. Les utilisateurs administrateurs doivent se connecter avec ces informations d’identification, créer un utilisateur administrateur, puis créer des utilisateurs supplémentaires pour les analystes de sécurité et les utilisateurs en lecture seule.

## <a name="role-based-permissions"></a>Autorisations basées sur les rôles
Les rôles utilisateur suivants sont disponibles :

- **Lecture seule** : Les utilisateurs en lecture seule effectuent des tâches telles que l’affichage des alertes et des appareils sur la carte des appareils. Ces utilisateurs ont accès aux options affichées sous **Navigation**.

- **Analyste de sécurité** : Les analystes de sécurité disposent d’autorisations d’utilisateur en lecture seule. Ils peuvent également effectuer des actions sur les appareils, accuser réception des alertes et utiliser les outils d’investigation. Ces utilisateurs ont accès aux options affichées sous **Navigation** et **Analyse**.

- **Administrateur** : Les administrateurs ont accès à tous les outils, notamment la définition des configurations système, la création et la gestion des utilisateurs, etc. Ces utilisateurs ont accès aux options sous **Navigation**, **Analyse** et **Administration**.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Autorisations basées sur les rôles pour les outils de la console de gestion locale

Cette section décrit les autorisations disponibles pour les administrateurs, les analystes de sécurité et les utilisateurs en lecture seule pour la console de gestion locale.  

| Autorisation | Lecture seule | Analyste de sécurité | Administrateur |
|--|--|--|--|
| Voir et filtrer la vue de carte d’entreprise | ✓ | ✓ | ✓ |
| Créer un site |  |  | ✓ |
| Gérer un site (ajouter et modifier des zones) |  |  | ✓ |
| Consulter l’inventaire des appareils et le filtrer | ✓ | ✓ | ✓ |
| Consulter et gérer les alertes : en accuser réception, les apprendre et les épingler | ✓ | ✓ | ✓ |
| Génération de rapports |  | ✓ | ✓ |
| Consulter les rapports d’évaluation des risques |  | ✓ | ✓ |
| Définir des exclusions d’alerte |  | ✓ | ✓ |
| Afficher ou définir des groupes d’accès |  |  | ✓ |
| Gérer les paramètres système |  |  | ✓ |
| Gestion des utilisateurs |  |  | ✓ |
| Envoyer des données d’alerte aux partenaires |  |  | ✓ |
| Gérer des certificats |  |  | ✓ |
| Délai d’expiration de session lorsque les utilisateurs ne sont pas actifs | 30 minutes | 30 minutes  | 30 minutes  |

#### <a name="assign-users-to-access-groups"></a>Assigner des utilisateurs à des groupes d’accès

Les administrateurs peuvent améliorer le contrôle d’accès utilisateur dans Defender pour IoT en affectant les utilisateurs à des *groupes d’accès*. Ces groupes d’accès sont affectés à des zones, des sites, des régions et des unités d’exploitation où se trouvent des capteurs. En affectant des utilisateurs à ces groupes, les administrateurs peuvent bénéficier d’un contrôle détaillé sur l’emplacement où les utilisateurs gèrent et analysent les détections. 

Cette méthode aide les grandes organisations dans lesquelles les autorisations des utilisateurs peuvent être complexes ou déterminées par une stratégie globale de sécurité au niveau de l’organisation. Pour plus d’informations, consultez [Définir le contrôle d’accès global](how-to-define-global-user-access-control.md).

### <a name="role-based-permissions-to-sensor-tools"></a>Autorisations basées sur les rôles pour les outils de capteur

Cette section décrit les autorisations disponibles pour les administrateurs des capteurs, les analystes de sécurité et les utilisateurs en lecture seule.  

| Autorisation | Lecture seule | Analyste de sécurité | Administrateur |
|--|--|--|--|
| Afficher le tableau de bord | ✓ | ✓ | ✓ |
| Contrôler les affichages de zoom sur la carte |  |  | ✓ |
| Afficher les alertes | ✓ | ✓ | ✓ |
| Gérer les alertes : en accuser réception, les apprendre et les épingler |  | ✓ | ✓ |
| Voir les événements dans une chronologie |  | ✓ | ✓ |
| Autoriser des appareils, des appareils d’analyse connus et des appareils de programmation |  | ✓ | ✓ |
| Consulter les données d’investigation | ✓ | ✓ | ✓ |
| Gérer les paramètres système |  |  | ✓ |
| Gestion des utilisateurs |  |  | ✓ |
| Serveurs DNS pour la recherche inversée |  |  | ✓ |
| Envoyer des données d’alerte aux partenaires |  | ✓ | ✓ |
| Créer des commentaires sur les alertes |  | ✓ | ✓ |
| Voir l’historique des modifications de programmation | ✓ | ✓ | ✓ |
| Créer des règles d’alerte personnalisées |  | ✓ | ✓ |
| Gérer simultanément de nombreuses notifications |  | ✓ | ✓ |
| Gérer des certificats |  |  | ✓ |
| Délai d’expiration de session lorsque les utilisateurs ne sont pas actifs | 30 minutes | 30 minutes | 30 minutes |

## <a name="define-users"></a>Définir des utilisateurs

Cette section décrit comment définir des utilisateurs. Les utilisateurs CyberX, support et administrateur peuvent ajouter, supprimer et mettre à jour les définitions des autres utilisateurs.

**Pour définir un utilisateur** :

1. Dans le volet gauche du capteur ou sur la console de gestion locale, sélectionnez **Utilisateurs**.
1. Dans la fenêtre **Utilisateurs**, sélectionnez **Créer un utilisateur**.
1. Dans le volet **Créer un utilisateur**, définissez les paramètres suivants :

   - **Nom d’utilisateur** : Entrez un nom d’utilisateur.
   - **E-mail** : Saisissez l’adresse de messagerie de l’utilisateur.
   - **Prénom** : Entrez le prénom de l’utilisateur.
   - **Nom** : Entrez le nom de famille de l’utilisateur.
   - **Rôle** : Définissez le rôle de l’utilisateur. Consultez [Autorisations basées sur les rôles](#role-based-permissions).
   - **Groupe d’accès** : Si vous créez un utilisateur pour la console de gestion locale, définissez le groupe d’accès de l’utilisateur. Consultez [Définir le contrôle d’accès global](how-to-define-global-user-access-control.md).
   - **Mot de passe** : Sélectionnez le type d’utilisateur comme suit :
     - **Utilisateur local** : Définissez un mot de passe pour l’utilisateur d’un capteur ou d’une console de gestion locale. Le mot de passe doit comprendre au moins six caractères et doit comprendre des chiffres et des lettres.
     - **Utilisateur Active Directory**: Vous pouvez autoriser les utilisateurs à se connecter au capteur ou à la console de gestion à l’aide des informations d’identification d’Active Directory. Les groupes Active Directory définis peuvent être associés à des niveaux d’autorisation spécifiques. Par exemple, configurez un groupe Active Directory spécifique et attribuez à tous les utilisateurs du groupe le type d’utilisateur en lecture seule.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Gérer vos utilisateurs.":::

## <a name="user-session-timeout"></a>Délai d’expiration de la session utilisateur

Si les utilisateurs n’activent pas le clavier ou la souris pendant un certain temps, ils sont déconnectés de la session et doivent se reconnecter.

Lorsque les utilisateurs n’ont pas utilisé la souris ou le clavier de la console pendant 30 minutes, la déconnexion est forcée.

Cette fonctionnalité est activée par défaut et peut être améliorée, mais elle ne peut pas être désactivée. En outre, les heures de comptage des sessions peuvent être mises à jour. La durée des session est définie en secondes. Les définitions sont appliquées par capteur et par console de gestion locale.

Un message de délai d’expiration de session s’affiche sur la console lorsque le délai d’inactivité a été dépassé.

### <a name="control-inactivity-sign-out"></a>Contrôler la déconnexion en cas d’inactivité

Les utilisateurs administrateurs peuvent activer et désactiver la déconnexion en cas d’inactivité et ajuster les seuils d’inactivité.

**Pour accéder à la commande** :

1. Connectez-vous à l’interface de ligne de commande du capteur ou de la console de gestion locale en utilisant Defender pour IoT avec les informations d’identification d’administrateur.

1. Entrez `sudo nano /var/cyberx/properties/authentication`.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

Pour désactiver la fonctionnalité, remplacez `infinity_session_expiration = true` par `infinity_session_expiration = false`.

Pour mettre à jour les périodes de déconnexion, réglez la valeur de `= <number>` selon la durée requise.

## <a name="track-user-activity"></a>Suivre les activités des utilisateurs

Vous pouvez suivre les activités des utilisateurs dans la chronologie des événements sur chaque capteur. La chronologie affiche l’événement ou l’appareil affecté, ainsi que l’heure et la date auxquelles l’utilisateur a effectué l’activité.

**Pour consulter les activités des utilisateur** :

1. Connectez-vous au capteur.

1. Dans la chronologie des événements, activez l’option **Opérations sur l’utilisateur**.

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="Voir les activités d’un utilisateur.":::

## <a name="integrate-with-active-directory-servers"></a>Intégration avec les serveurs Active Directory

Configurez le capteur ou la console de gestion locale pour qu’il fonctionne avec Active Directory. Ceci permet aux utilisateurs Active Directory d’accéder à Defender pour IoT à l’aide de leurs informations d’identification Active Directory.

> [!Note]
> LDAP v3 est pris en charge.

Deux types d’authentifications LDAP sont pris en charge :

- **Authentification complète** : les détails de l’utilisateur sont récupérés sur le serveur LDAP. Il peut s’agir par exemple du prénom, du nom, de l’adresse e-mail et des autorisations de l’utilisateur.

- **Utilisateur approuvé** : seul le mot de passe de l’utilisateur est récupéré. Les autres détails utilisateur récupérés sont basés sur les utilisateurs définis dans le capteur.

### <a name="active-directory-and-defender-for-iot-permissions"></a>Autorisations Active Directory et Defender pour IoT

Vous pouvez associer les groupes Active Directory définis ici à des niveaux d’autorisation spécifiques. Par exemple, configurez un groupe Active Directory spécifique et attribuez des autorisations de lecture seule à tous les utilisateurs du groupe.

**Pour configurer Active Directory** :

1. Dans le volet gauche, sélectionnez **Paramètres du système**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Consulter les paramètres de votre Active Directory":::.

1. Dans le volet **Paramètres système**, sélectionnez **Active Directory**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Modifiez vos configurations Active Directory.":::

1. Dans la boîte de dialogue **Modifier la configuration Active Directory**, sélectionnez **Active Directory intégration activée** > **Enregistrer**. La boîte de dialogue **Modifier la configuration Active Directory** se développe et vous pouvez maintenant entrer les paramètres pour configurer Active Directory.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Entrer les paramètres pour configurer Active Directory.":::

> [!NOTE]
> - Vous devez définir les paramètres LDAP exactement tels qu’ils apparaissent dans Active Directory.
> - Pour tous les paramètres d’Active Directory, utilisez uniquement des minuscules. Utilisez des minuscules même lorsque les configurations d’Active Directory utilisent des majuscules.
> - Vous ne pouvez pas configurer à la fois LDAP et LDAP sécurisé pour le même domaine. Toutefois, vous pouvez utiliser les deux pour des domaines différents en même temps.

1. Définissez les paramètres du serveur Active Directory comme suit :

   | Paramètre serveur | Description |
   |--|--|
   | Nom de domaine complet du contrôleur de domaine | Définissez le nom de domaine complet exactement tel qu’il apparaît sur votre serveur LDAP. Par exemple, entrez `host1.subdomain.domain.com`. |
   | Port du contrôleur de domaine | Définissez le port sur lequel votre LDAP est configuré. |
   | Domaine principal | Définissez le nom de domaine (par exemple, `subdomain.domain.com`) et le type de connexion en fonction de votre configuration LDAP. |
   | Groupes Active Directory | Entrez les noms des groupes qui sont définis dans la configuration de votre Active Directory sur le serveur LDAP. |
   | Domaines approuvés | Pour ajouter un domaine approuvé, ajoutez le nom du domaine et le type de connexion d’un domaine approuvé. <br />Vous ne pouvez configurer des domaines approuvés que pour les utilisateurs qui ont été définis dans les utilisateurs. |

#### <a name="activedirectory-groups-for-the-on-premises-management-console"></a>Groupes ActiveDirectory pour la console de gestion locale

Si vous créez des groupes Active Directory pour les utilisateurs de la console de gestion locale, vous devez créer une règle de groupe d’accès pour chaque groupe Active Directory. Les informations d’identification Active Directory de la console de gestion locale ne fonctionneront pas si une règle de groupe d’accès n’existe pas pour le groupe d’utilisateurs Active Directory. Consultez [Définir le contrôle d’accès global](how-to-define-global-user-access-control.md).

1. Sélectionnez **Enregistrer**.

1. Pour ajouter un serveur approuvé, sélectionnez **Ajouter un serveur** et configurez un autre serveur.

## <a name="change-a-users-password"></a>Changer le mot de passe d’un utilisateur

L’administrateur peut modifier le mot de passe de l’analyste de la sécurité et du rôle en lecture seule. L’utilisateur qui a le rôle d’administrateur ne peut pas modifier son propre mot de passe et doit contacter un rôle de niveau supérieur. L’analyste de la sécurité et les rôles en lecture seule ne peuvent pas réinitialiser leur mot de passe ou celui d’un utilisateur ayant un autre rôle. L’analyste de la sécurité et les rôles en lecture seule doivent contacter un utilisateur disposant d’un niveau de rôle supérieur pour réinitialiser leurs mots de passe. Le rôle CyberX peut modifier le mot de passe pour tous les rôles d’utilisateur. Le rôle de support peut modifier le mot de passe pour un rôle de support, d’administrateur, d’analyste de la sécurité et d’utilisateur en lecture seule.  

**Pour réinitialiser le mot de passe d’un utilisateur sur le capteur** :

1. Connectez-vous au capteur à l’aide d’un utilisateur ayant le rôle Administrateur, Support ou CyberX.

1. Sélectionnez **Utilisateurs** dans le panneau de gauche.

   :::image type="content" source="media/password-recovery-images/sensor-page.png" alt-text="Sélectionnez l’option Utilisateurs dans le volet gauche.":::

1. Localisez l’utilisateur et sélectionnez **Modifier** dans le menu déroulant **Actions**.

   :::image type="content" source="media/password-recovery-images/edit.png" alt-text="Sélectionnez Modifier dans le menu déroulant Actions.":::

1. Entrez le nouveau mot de passe dans les champs **Nouveau mot de passe** et **Confirmer le mot de passe**.

    > [!NOTE]
    > Les mots de passe doivent comporter au moins 16 caractères, contenir des caractères alphabétiques minuscules et majuscules, des chiffres et l’un des symboles suivants : #% * +,-./ : = ? @ [] ^ _ {} ~

1. Sélectionnez **Mettre à jour**.

**Pour réinitialiser le mot de passe d’un utilisateur sur la console de gestion locale** :

1. Connectez-vous à la console de gestion locale à l’aide d’un utilisateur avec le rôle Administrateur, Support ou CyberX.

1. Sélectionnez **Utilisateurs** dans le panneau de gauche.

   :::image type="content" source="media/password-recovery-images/console-page.png" alt-text="Dans le volet gauche, sélectionnez l’option Utilisateurs.":::

1. Localisez votre utilisateur et sélectionnez l’icône de modification :::image type="icon" source="media/password-recovery-images/edit-icon.png" border="false":::.

1. Entrez le nouveau mot de passe dans les champs **Nouveau mot de passe** et **Confirmer le mot de passe**.

    > [!NOTE]
    > Les mots de passe doivent comporter au moins 16 caractères, contenir des caractères alphabétiques minuscules et majuscules, des chiffres et l’un des symboles suivants : #% * +,-./ : = ? @ [] ^ _ {} ~

1. Sélectionnez **Mettre à jour**.

## <a name="recover-the-password-for-the-on-premises-management-console-or-the-sensor"></a>Récupérer le mot de passe pour la console de gestion locale ou le capteur

Vous pouvez récupérer le mot de passe de la console de gestion locale ou du capteur à l’aide de la fonctionnalité de récupération du mot de passe. Seuls les utilisateurs CyberX et Support ont accès à la fonctionnalité Récupération du mot de passe.

**Pour récupérer le mot de passe pour la console de gestion locale ou le capteur :**

1. Sur l’écran de connexion de la console de gestion locale, ou le capteur, sélectionnez **Récupération du mot de passe**. L’écran **Récupération du mot de passe** s’ouvre.

    :::image type="content" source="media/how-to-create-and-manage-users/password-recovery.png" alt-text="Sélectionnez Récupération du mot de passe dans l’écran de connexion de la console de gestion locale ou le capteur.":::

1. Sélectionnez **CyberX** ou **Support** dans le menu déroulant, puis copiez le code d’identificateur unique.

    :::image type="content" source="media/how-to-create-and-manage-users/password-recovery-screen.png" alt-text="Dans le menu déroulant, sélectionnez l’utilisateur CyberX ou l’utilisateur Support.":::

1. Accédez au portail Azure et sélectionnez **Sites et capteurs**.  

1. Sélectionnez l’icône **Filtre d’abonnement** :::image type="icon" source="media/password-recovery-images/subscription-icon.png" border="false"::: dans la barre d’outils supérieure, puis sélectionnez l’abonnement auquel votre capteur est connecté.

1. Sélectionnez le menu déroulant **Autres actions**, puis sélectionnez **Récupérer le mot de passe de la console de gestion locale**.

    :::image type="content" source="media/how-to-create-and-manage-users/recover-password.png" alt-text="Sélectionnez votre capteur et sélectionnez l’option Récupérer le mot de passe de la console de gestion locale.":::   

1. Entrez l’identificateur unique que vous avez reçu sur l’écran **Récupération du mot de passe**, puis sélectionnez **Récupérer**. Le fichier `password_recovery.zip` est téléchargé.

    :::image type="content" source="media/how-to-create-and-manage-users/enter-identifier.png" alt-text="Entrez l’identificateur unique et sélectionnez Récupérer.":::

    > [!NOTE]
    > Ne modifiez pas le fichier de récupération du mot de passe. Il s’agit d’un fichier signé qui ne fonctionnera pas s’il est falsifié.

1. Sur l’écran Récupération du mot de passe, sélectionnez **Charger**. La fenêtre **Charger le fichier de récupération du mot de passe** s’ouvre.

1. Sélectionnez **Parcourir** pour localiser votre fichier `password_recovery.zip` ou faites glisser `password_recovery.zip` sur la fenêtre.

    > [!NOTE]
    > Un message d’erreur peut s’afficher, indiquant que le fichier n’est pas valide. Pour corriger ce message d’erreur, assurez-vous d’avoir sélectionné l’abonnement approprié avant de télécharger le fichier `password_recovery.zip` et téléchargez-le à nouveau.  

1. Sélectionnez **Suivant**, et votre utilisateur et le mot de passe généré par le système pour votre console de gestion s’affichent alors.

## <a name="next-steps"></a>Étapes suivantes

[Activer et configurer votre capteur](how-to-activate-and-set-up-your-sensor.md)
[Activer et configurer votre console de gestion locale](how-to-activate-and-set-up-your-on-premises-management-console.md)
[Suive l’activité du capteur](how-to-track-sensor-activity.md)
