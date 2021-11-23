---
title: Rôles intégrés Azure AD – Azure Active Directory
description: Décrit les rôles et autorisations intégrés Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/10/2021
ms.author: rolyon
ms.reviewer: abhijeetsinha
ms.custom: generated, it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: e936e457c03c56ee64edfec8177bb7a565d0fb98
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371654"
---
# <a name="azure-ad-built-in-roles"></a>Rôles intégrés Azure AD

Dans Azure Active Directory (Azure AD), si un autre administrateur ou utilisateur non administrateur doit gérer les ressources Azure AD, attribuez-lui un rôle Azure AD qui fournit les autorisations dont il a besoin. Par exemple, vous pouvez attribuer des rôles pour permettre l’ajout ou la modification d’utilisateurs, la réinitialisation des mots de passe des utilisateurs, la gestion des licences d’utilisation ou la gestion des noms de domaine.

Cet article répertorie les rôles intégrés Azure AD que vous pouvez attribuer pour permettre la gestion des ressources Azure AD. Pour obtenir des informations sur la procédure d’attribution de rôles, consultez [Attribuer des rôles Azure AD aux utilisateurs](manage-roles-portal.md).

## <a name="all-roles"></a>Tous les rôles

> [!div class="mx-tableFixed"]
> | Role | Description | ID de modèle |
> | --- | --- | --- |
> | [Administrateur d’application](#application-administrator) | Peut créer et gérer tous les aspects des inscriptions d’applications et des applications d’entreprise. | 9b895d92-2cd3-44c7-9d02-a6ac2d5ea5c3 |
> | [Développeur d’applications](#application-developer) | Peut créer des inscriptions d’applications indépendamment du paramètre « Les utilisateurs peuvent inscrire des applications ». | cf1c38e5-3621-4004-a7cb-879624dced7c |
> | [Auteur de charge utile d'attaque](#attack-payload-author) | Peut créer des charges utiles d’attaque qu’un administrateur peut lancer plus tard. | 9c6df0f2-1e7c-4dc3-b195-66dfbd24aa8f |
> | [Administrateur de simulation d'attaque](#attack-simulation-administrator) | Peut créer et gérer tous les aspects des campagnes de simulation d'attaque. | c430b396-e693-46cc-96f3-db01bf8bb62a |
> | [Administrateur d’authentification](#authentication-administrator) | Peut accéder pour afficher, définir et réinitialiser les informations de méthode d’authentification pour tout utilisateur non administrateur. | c4e39bd9-1100-46d3-8c65-fb160da0071f |
> | [Administrateur de la stratégie d’authentification](#authentication-policy-administrator) | Peut créer et gérer la stratégie relative aux méthodes d'authentification, les paramètres d'authentification multifacteur à l'échelle du locataire, la stratégie de protection par mot de passe et les justificatifs vérifiables. | 0526716b-113d-4c15-b2c8-68e3c22b9f80 |
> | [Administrateur local de l’appareil joint Azure AD](#azure-ad-joined-device-local-administrator) | Les utilisateurs dotés de ce rôle sont ajoutés au groupe d’administrateurs locaux sur des appareils joints à AD Azure. | 9f06204d-73c1-4d4c-880a-6edb90606fd8 |
> | [Administrateur Azure DevOps](#azure-devops-administrator) | Peut gérer la stratégie et les paramètres de l’organisation Azure DevOps. | e3973bdf-4987-49ae-837a-ba8e231c7286 |
> | [Administrateur Azure Information Protection](#azure-information-protection-administrator) | Peut gérer tous les aspects du produit Azure Information Protection. | 7495fdc4-34c4-4d15-a289-98788ce399fd |
> | [Administrateur de jeux de clés B2C IEF](#b2c-ief-keyset-administrator) | Peut gérer les secrets pour la fédération et le chiffrement dans Identity Experience Framework (IEF). | aaf43236-0c0d-4d5f-883a-6955382ac081 |
> | [Administrateur de stratégies B2C IEF](#b2c-ief-policy-administrator) | Peut créer et gérer les stratégies de framework de confiance dans Identity Experience Framework (IEF). | 3edaf663-341e-4475-9f94-5c398ef6c070 |
> | [Administrateur de facturation](#billing-administrator) | Peut effectuer des tâches de facturation courantes, comme la mise à jour des informations de paiement. | b0f54661-2d74-4c50-afa3-1ec803f12efe |
> | [Administrateur de sécurité d’application cloud](#cloud-app-security-administrator) | Peut gérer tous les aspects du produit Sécurité des applications cloud. | 892c5842-a9a6-463a-8041-72aa08ca3cf6 |
> | [Administrateur d’application cloud](#cloud-application-administrator) | Peut créer et gérer tous les aspects des inscriptions d’applications et des applications d’entreprise, à l’exception du proxy d’application. | 158c047a-c907-4556-b7ef-446551a6b5f7 |
> | [Administrateur d’appareil cloud](#cloud-device-administrator) | Accès limité pour gérer des appareils dans Azure AD. | 7698a772-787b-4ac8-901f-60d6b08affd2 |
> | [Administrateur de conformité](#compliance-administrator) | Peut lire et gérer la configuration de la conformité et les rapports dans Azure AD et Microsoft 365. | 17315797-102d-40b4-93e0-432062caca18 |
> | [Administrateur des données de conformité](#compliance-data-administrator) | Crée et gère le contenu de conformité. | e6d1a23a-da11-4be4-9570-befc86d067a7 |
> | [Administrateur de l’accès conditionnel](#conditional-access-administrator) | Peut gérer les fonctionnalités d’accès conditionnel. | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9 |
> | [Approbateur d’accès à Customer LockBox](#customer-lockbox-access-approver) | Peut approuver les demandes de support Microsoft pour accéder aux données organisationnelles du client. | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91 |
> | [Administrateur Desktop Analytics](#desktop-analytics-administrator) | Peut utiliser et gérer des services et outils de gestion de bureau. | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4 |
> | [Lecteurs de répertoire](#directory-readers) | Peut lire les informations d’annuaire de base. Couramment utilisé pour accorder à l’annuaire l’accès en lecture aux applications et aux invités. | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b |
> | [Comptes de synchronisation d’annuaires](#directory-synchronization-accounts) | Utilisés uniquement par le service Azure AD Connect. | d29b2b05-8046-44ba-8758-1e26182fcf32 |
> | [Enregistreurs de répertoire](#directory-writers) | Peut lire et écrire des informations d’annuaire de base. Pour accorder l’accès aux applications, non destiné aux utilisateurs. | 9360feb5-f418-4baa-8175-e2a00bac4301 |
> | [Administrateur de nom de domaine](#domain-name-administrator) | Peut gérer les noms de domaine dans le cloud et localement. | 8329153b-31d0-4727-b945-745eb3bc5f31 |
> | [Administrateur Dynamics 365](#dynamics-365-administrator) | Peut gérer tous les aspects du produit Dynamics 365. | 44367163-eba1-44c3-98af-f5787879f96a |
> | [Administrateur Edge](#edge-administrator) | Gérez tous les aspects de Microsoft Edge. | 3f1acade-1e04-4fbc-9b69-f0302cd84aef |
> | [Administrateur Exchange](#exchange-administrator) | Peut gérer tous les aspects du produit Exchange. | 29232cdf-9323-42fd-ade2-1d097af3e4de |
> | [Administrateur des destinataires Exchange](#exchange-recipient-administrator) | Peut créer ou mettre à jour des destinataires Exchange Online dans l’organisation Exchange Online. | 31392ffb-586c-42d1-9346-e59415a2cc4e |
> | [Administrateur de flux d’utilisateurs ID externe](#external-id-user-flow-administrator) | Peut créer et gérer tous les aspects des flux utilisateur. | 6e591065-9bad-43ed-90f3-e9424366d2f0 |
> | [Administrateur d’attribut de flux d’utilisateurs ID externe](#external-id-user-flow-attribute-administrator) | Peut créer et gérer le schéma d’attribut disponible pour tous les flux utilisateur. | 0f971eea-41eb-4569-a71e-57bb8a3eff1e |
> | [Administrateur de fournisseurs d’identité externes](#external-identity-provider-administrator) | Peut configurer les fournisseurs d’identité pour une utilisation dans la fédération directe. | be2f45a1-457d-42af-a067-6ec1fa63bc45 |
> | [Administrateur général](#global-administrator) | Peut gérer tous les aspects d’Azure AD et des services Microsoft qui utilisent des identités Azure AD. | 62e90394-69f5-4237-9190-012177145e10 |
> | [Lecteur général](#global-reader) | Peut lire tous les éléments comme un administrateur général, mais ne peut pas mettre à jour. | f2ef992c-3afb-46b9-b7cf-a126ee74c451 |
> | [Administrateur de groupes](#groups-administrator) | Les membres de ce rôle peuvent créer/gérer des groupes, créer/gérer des paramètres de groupes tels que des stratégies de nommage et d’expiration, ainsi qu’afficher des rapports d’activité et d’audit de groupes. | fdd7a751-b60b-444a-984c-02652fe8fa1c |
> | [Inviteur d’invités](#guest-inviter) | Peut inviter des utilisateurs invités indépendamment du paramètre « Les membres peuvent inviter des invités ». | 95e79109-95c0-4d8e-aee3-d01accf2d47b |
> | [Administrateur du support technique](#helpdesk-administrator) | Peut réinitialiser des mots de passe pour les utilisateurs non-administrateurs et les administrateurs du support technique. | 729827e3-9c14-49f7-bb1b-9608f156bbb8 |
> | [Administrateur d’identité hybride](#hybrid-identity-administrator) | Peut gérer les paramètres d’approvisionnement d’AD au cloud Azure AD, d’Azure AD Connect et de fédération. | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2 |
> | [Administrateur Identity Governance](#identity-governance-administrator) | Gérez l'accès à l'aide d'Azure AD pour les scénarios de gouvernance des identités. | 45d8d3c5-c802-45c6-b32a-1d70b5e1e86e |
> | [Administrateur Insights](#insights-administrator) | Dispose d’un accès administratif dans l’application Microsoft 365 Insights. | eb1f4a8d-243a-41f0-9fbd-c7cdf6c5ef7c |
> | [Leader d’entreprise Insights](#insights-business-leader) | Peut afficher et partager des tableaux de bord et des insights par le biais de l’application Microsoft 365 Insights. | 31e939ad-9672-4796-9c2e-873181342d2d |
> | [Administrateur Intune](#intune-administrator) | Peut gérer tous les aspects du produit Intune. | 3a2c62db-5318-420d-8d74-23affee5d9d5 |
> | [Administrateur Kaizala](#kaizala-administrator) | Peut gérer les paramètres de Microsoft Kaizala. | 74ef975b-6605-40af-a5d2-b9539d836353 |
> | [Administrateur des connaissances](#knowledge-administrator) | Peut configurer les connaissances, l’apprentissage et d’autres fonctionnalités intelligentes. | b5a8dcf3-09d5-43a9-a639-8e29ef291470 |
> | [Gestionnaire des connaissances](#knowledge-manager) | Peut organiser, créer, gérer et promouvoir des rubriques et des connaissances. | 744ec460-397e-42ad-a462-8b3f9747a02c |
> | [Administrateur de licence](#license-administrator) | Peut gérer les licences de produit pour les utilisateurs et les groupes. | 4d6ac14f-3453-41d0-bef9-a3e0c569773a |
> | [Lecteur de confidentialité du Centre de messages](#message-center-privacy-reader) | Peut lire les messages et les mises à jour de sécurité uniquement dans le Centre de messages Office 365. | ac16e43d-7b2d-40e0-ac05-243ff356ab5b |
> | [Lecteur du Centre de messages](#message-center-reader) | Peut lire les messages et les mises à jour de son organisation dans le Centre de messages Office 365 uniquement. | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b |
> | [Utilisateur de Commerce moderne](#modern-commerce-user) | Peut gérer les achats commerciaux d’une société, d’un service ou d’une équipe. | d24aef57-1500-4070-84db-2666f29cf966 |
> | [Administrateur réseau](#network-administrator) | Peut gérer les emplacements réseau et passer en revue les insights sur la conception réseau de l’entreprise pour les applications Software as a Service Microsoft 365. | d37c8bed-0711-4417-ba38-b4abe66ce4c2 |
> | [Administrateur d’applications Office](#office-apps-administrator) | Peut gérer les services cloud des applications Office, notamment la gestion des stratégies et des paramètres, et gérer la possibilité de sélectionner, de désélectionner et de publier le contenu de la fonctionnalité « Nouveautés » sur les appareils de l’utilisateur final. | 2b745bdf-0803-4d80-aa65-822c4493daac |
> | [Prise en charge de niveau 1 de partenaire](#partner-tier1-support) | Ne pas utiliser - non destiné à une utilisation générale. | 4ba39ca4-527c-499a-b93d-d9b492c50246 |
> | [Prise en charge de niveau 2 de partenaire](#partner-tier2-support) | Ne pas utiliser - non destiné à une utilisation générale. | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8 |
> | [Administrateur de mots de passe](#password-administrator) | Peut réinitialiser les mots de passe pour les utilisateurs non administrateurs et les administrateurs de mot de passe. | 966707d0-3269-4727-9be2-8c3a10f19b9d |
> | [Administrateur Power BI](#power-bi-administrator) | Peut gérer tous les aspects du produit Power BI. | a9ea8996-122f-4c74-9520-8edcd192826c |
> | [Administrateur de plateforme Power](#power-platform-administrator) | Peut créer et gérer tous les aspects de Microsoft Dynamics 365, Power Apps et Power Automate. | 11648597-926c-4cf3-9c36-bcebb0ba8dcc |
> | [Administrateur d’imprimantes](#printer-administrator) | Peut gérer tous les aspects des imprimantes et des connecteurs d’imprimantes. | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f |
> | [Technicien en charge des imprimantes](#printer-technician) | Peut inscrire et désinscrire des imprimantes et mettre à jour l’état de l’imprimante. | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477 |
> | [Administrateur d’authentification privilégié](#privileged-authentication-administrator) | Peut afficher, définir et réinitialiser les informations de méthode d’authentification pour tout utilisateur (administrateur ou non administrateur). | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13 |
> | [Administrateur de rôle privilégié](#privileged-role-administrator) | Peut gérer les attributions de rôles dans Azure AD, et tous les aspects de Privileged Identity Management. | e8611ab8-c189-46e8-94e1-60213ab1f814 |
> | [Lecteur de rapports](#reports-reader) | Peut lire les rapports d’audit et sur les connexions. | 4a5d8f65-41da-4de4-8968-e035b65339cf |
> | [Administrateur de recherche](#search-administrator) | Peut créer et gérer tous les aspects des paramètres de Recherche Microsoft. | 0964bb5e-9bdb-4d7b-ac29-58e794862a40 |
> | [Éditeur de recherche](#search-editor) | Peut créer et gérer le contenu éditorial comme les signets, les questions et réponses, les emplacements et les plans d’étage. | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9 |
> | [Administrateur de la sécurité](#security-administrator) | Peut lire des rapports et des informations de sécurité, ainsi que gérer la configuration dans Azure AD et Office 365. | 194ae4cb-b126-40b2-bd5b-6091b380977d |
> | [Opérateur de sécurité](#security-operator) | Crée et gère les événements de sécurité. | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f |
> | [Lecteur de sécurité](#security-reader) | Peut lire des rapports et des informations de sécurité dans Azure AD et Office 365. | 5d6b6bb7-de71-4623-b4af-96380a352509 |
> | [Administrateur de support de service](#service-support-administrator) | Peut lire des informations sur l’intégrité du service et gérer les tickets de support. | f023fd81-a637-4b56-95fd-791ac0226033 |
> | [Administrateur SharePoint](#sharepoint-administrator) | Peut gérer tous les aspects du service SharePoint. | f28a1f50-f6e7-4571-818b-6a12f2af6b6c |
> | [Administrateur Skype Entreprise](#skype-for-business-administrator) | Peut gérer tous les aspects du produit Skype Entreprise. | 75941009-915a-4869-abe7-691bff18279e |
> | [Administrateur Teams](#teams-administrator) | Peut gérer le service Microsoft Teams. | 69091246-20e8-4a56-aa4d-066075b2a7a8 |
> | [Administrateur des communications Teams](#teams-communications-administrator) | Peut gérer les fonctionnalités d’appel et de réunion au sein du service Microsoft Teams. | baf37b3a-610e-45da-9e62-d9d1e5e8914b |
> | [Ingénieur de support des communications Teams](#teams-communications-support-engineer) | Peut résoudre les problèmes de communication au sein de Teams à l’aide d’outils avancés. | f70938a0-fc10-4177-9e90-2178f8765737 |
> | [Spécialiste du support des communications Teams](#teams-communications-support-specialist) | Peut résoudre les problèmes de communication au sein de Teams à l’aide d’outils de base. | fcf91098-03e3-41a9-b5ba-6f0ec8188a12 |
> | [Administrateur d’appareils Teams](#teams-devices-administrator) | Peut effectuer des tâches d’administration sur des appareils certifiés Teams. | 3d762c5a-1b6c-493f-843e-55a3b42923d4 |
> | [Lecteur de rapports de synthèse sur l'utilisation](#usage-summary-reports-reader) | Ne peut voir que les agrégats au niveau locataire dans les rapports Analyse de l’utilisation et Score de productivité de Microsoft 365. | 75934031-6c7e-415a-99d7-48dbd49e875e |
> | [Administrateur d’utilisateurs](#user-administrator) | Peut gérer tous les aspects des utilisateurs et groupes, notamment la réinitialisation des mots de passe pour les administrateurs limités. | fe930be7-5e62-47db-91af-98c3a49a38b1 |
> | [Administrateur Windows 365](#windows-365-administrator) | Peut provisionner et gérer tous les aspects des PC cloud. | 11451d60-acb2-45eb-a7d6-43d0f0125c13 |
> | [Administrateur des déploiements de mise à jour Windows](#windows-update-deployment-administrator) | Créez et gérez tous les aspects des déploiements de mise à jour Windows par le biais du service de déploiement de mise à jour Windows pour entreprises. | 32696413-001a-46ae-978c-ce0f6b3620d2 |

## <a name="application-administrator"></a>Administrateur d’application

Les utilisateurs dans ce rôle peuvent créer et gérer tous les aspects des applications d’entreprise, des inscriptions d’application et des paramètres de proxy d’application. Notez que les utilisateurs affectés à ce rôle ne sont pas ajoutés en tant que propriétaires lorsque des inscriptions d’applications ou des applications d’entreprise sont créées.

Ce rôle permet également de consentir des autorisations déléguées et autorisations d’application, à l’exclusion des autorisations d’application pour Microsoft Graph et Azure AD Graph.

> [!IMPORTANT]
> Cette exception signifie que vous pouvez toujours accepter les autorisations pour _d’autres applications_ (par exemple les applications Microsoft ou les applications que vous avez inscrites). Vous pouvez toujours _demander_ ces autorisations dans le cadre de l’inscription d’application, mais l’_octroi_ de ces autorisations requiert un administrateur plus privilégié, tel un administrateur général.
>
>Ce rôle permet de gérer des informations d’identification d’application. Les utilisateurs auxquels ce rôle a été attribué peuvent ajouter des informations d’identification à une application et les utiliser pour emprunter l’identité de l’application. Si l’identité de l’application a obtenu l’accès à une ressource, telle que la possibilité de créer ou de mettre à jour un utilisateur ou d’autres objets, un utilisateur auquel ce rôle a été attribué peut effectuer ces actions en empruntant l’identité de l’application. Cette capacité à emprunter l’identité de l’application peut correspondre une élévation de privilèges que l’utilisateur réalise dans ses attributions de rôle. Il est important de comprendre que l’affectation d’un utilisateur au rôle Administrateur d’applications lui donne la possibilité d’emprunter l’identité d’une application.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/applications/create | Créer tous les types d’applications |
> | microsoft.directory/applications/delete | Supprimer tous les types d’applications |
> | microsoft.directory/applications/applicationProxy/read | Lire toutes les propriétés du proxy d’application |
> | microsoft.directory/applications/applicationProxy/update | Mettre à jour toutes les propriétés du proxy d’application |
> | microsoft.directory/applications/applicationProxyAuthentication/update | Mettre à jour l’authentification sur tous les types d’applications |
> | microsoft.directory/applications/applicationProxySslCertificate/update | Mettre à jour les paramètres de certificat SSL pour le proxy d’application |
> | microsoft.directory/applications/applicationProxyUrlSettings/update | Mettre à jour les paramètres d’URL pour le proxy d’application |
> | microsoft.directory/applications/appRoles/update | Mettre à jour la propriété appRoles sur tous les types d’applications |
> | microsoft.directory/applications/audience/update | Mettre à jour la propriété audience pour les applications |
> | microsoft.directory/applications/authentication/update | Mettre à jour l’authentification sur tous les types d’applications |
> | microsoft.directory/applications/basic/update | Mettre à jour les propriétés de base pour les applications |
> | microsoft.directory/applications/credentials/update | Mettre à jour les informations d’identification d’application |
> | microsoft.directory/applications/extensionProperties/update | Mettre à jour les propriétés d’extension sur les applications |
> | microsoft.directory/applications/notes/update | Mettre à jour les notes d’applications |
> | microsoft.directory/applications/owners/update | Mettre à jour les propriétaires d’applications |
> | microsoft.directory/applications/permissions/update | Mettre à jour les autorisations exposées et les autorisations requises sur tous les types d’applications |
> | microsoft.directory/applications/policies/update | Mettre à jour les stratégies d’applications |
> | microsoft.directory/applications/tag/update | Mettre à jour les balises d’applications |
> | microsoft.directory/applications/verification/update | Mettre à jour la propriété applicationsverification |
> | microsoft.directory/applications/synchronization/standard/read | Lire les paramètres de provisionnement associés à l’objet application |
> | microsoft.directory/applicationTemplates/instantiate | Instancier des applications de la galerie à partir de modèles d’application |
> | microsoft.directory/auditLogs/allProperties/read | Lire toutes les propriétés dans les journaux d’audit, y compris les propriétés privilégiées |
> | microsoft.directory/connectors/create | Créer des connecteurs de proxy d’application |
> | microsoft.directory/connectors/allProperties/read | Lire toutes les propriétés des connecteurs de proxy d’application |
> | microsoft.directory/connectorGroups/create | Créer des groupes de connecteurs de proxy d’application |
> | microsoft.directory/connectorGroups/delete | Supprimer des groupes de connecteurs de proxy d’application |
> | microsoft.directory/connectorGroups/allProperties/read | Lire toutes les propriétés des groupes de connecteurs de proxy d’application |
> | microsoft.directory/connectorGroups/allProperties/update | Mettre à jour toutes les propriétés des groupes de connecteurs de proxy d’application |
> | microsoft.directory/deletedItems.applications/delete | Supprimer définitivement les applications qui ne peuvent plus être restaurées |
> | microsoft.directory/deletedItems.applications/restore | Restaurer les applications supprimées de manière réversible à l’état d’origine |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Créer et supprimer des octrois d’autorisations OAuth 2.0, et lire et mettre à jour toutes les propriétés |
> | microsoft.directory/applicationPolicies/create | Créer des stratégies d’application |
> | microsoft.directory/applicationPolicies/delete | Supprimer des stratégies d’application |
> | microsoft.directory/applicationPolicies/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.directory/applicationPolicies/owners/read | Lire les propriétaires des stratégies d’application |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Lire les stratégies d’application appliquées à la liste d’objets |
> | microsoft.directory/applicationPolicies/basic/update | Mettre à jour les propriétés standard des stratégies d’application |
> | microsoft.directory/applicationPolicies/owners/update | Mettre à jour la propriété Owner des stratégies d’application |
> | microsoft.directory/provisioningLogs/allProperties/read | Lire toutes les propriétés des journaux de provisionnement. |
> | microsoft.directory/servicePrincipals/create | Créer des principaux de service |
> | microsoft.directory/servicePrincipals/delete | Supprimer des principaux de service |
> | microsoft.directory/servicePrincipals/disable | Désactiver des principaux de service |
> | microsoft.directory/servicePrincipals/enable | Activer des principaux de service |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Gérer les informations d’identification de l’authentification unique par mot de passe sur les principaux de service |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Gérer les secrets et les informations d’identification de l’approvisionnement des applications |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Démarrer, redémarrer et suspendre les travaux de synchronisation d’approvisionnement des applications |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Créer et gérer le schéma et les tâches de synchronisation de l’approvisionnement des applications |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Lire les informations d’identification de l’authentification unique par mot de passe sur les principaux de service |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Consentir des autorisations d’application et des autorisations déléguées pour le compte d’un utilisateur ou de tous les utilisateurs, à l’exclusion des autorisations d’application pour Microsoft Graph et Azure AD Graph |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Mettre à jour les attributions de rôles des principaux de service |
> | microsoft.directory/servicePrincipals/audience/update | Mettre à jour les propriétés d’audience sur les principaux de service |
> | microsoft.directory/servicePrincipals/authentication/update | Mettre à jour les propriétés d’authentification sur les principaux de service |
> | microsoft.directory/servicePrincipals/basic/update | Mettre à jour les propriétés de base sur les principaux de service |
> | microsoft.directory/servicePrincipals/credentials/update | Mettre à jour informations d’identification des principaux de service |
> | microsoft.directory/servicePrincipals/notes/update | Mettre à jour les notes de principaux de service |
> | microsoft.directory/servicePrincipals/owners/update | Mettre à jour les propriétaires de principaux de service |
> | microsoft.directory/servicePrincipals/permissions/update | Mettre à jour les autorisations des principaux de service |
> | microsoft.directory/servicePrincipals/policies/update | Mettre à jour les stratégies des principaux de service |
> | microsoft.directory/servicePrincipals/tag/update | Mettre à jour la propriété Tag pour les principaux de service |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Lire les paramètres de provisionnement associés au principal de service |
> | microsoft.directory/signInReports/allProperties/read | Lire toutes les propriétés dans les rapports de connexion, y compris les propriétés privilégiées |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="application-developer"></a>Développeur d’applications

Les utilisateurs dans ce rôle peuvent créer des inscriptions d’application quand le paramètre « Les utilisateurs peuvent inscrire des applications » est défini sur Non. Ce rôle octroie aussi l’autorisation de donner son consentement en son propre nom lorsque le paramètre « Les utilisateurs peuvent autoriser les applications à accéder aux données de l’entreprise en leur nom » est défini sur Non. Les utilisateurs affectés à ce rôle sont ajoutés en tant que propriétaires lorsque de nouvelles inscriptions d’applications sont créées.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/applications/createAsOwner | Créer tous les types d’applications, le créateur étant ajouté comme premier propriétaire |
> | microsoft.directory/oAuth2PermissionGrants/createAsOwner | Créer des octrois d’autorisation OAuth 2.0, avec le créateur comme premier propriétaire |
> | microsoft.directory/servicePrincipals/createAsOwner | Créer des principaux de service, avec le créateur comme premier propriétaire |

## <a name="attack-payload-author"></a>Auteur de charge utile d'attaque

Les utilisateurs disposant de ce rôle peuvent créer des charges utiles d'attaque, mais pas les lancer ou les programmer. Les charges utiles d'attaque sont ensuite à la disposition de tous les administrateurs du locataire, qui peuvent les utiliser pour créer une simulation.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Créer et gérer des charges utiles d’attaque dans le Simulateur d’attaques |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Lire les rapports de simulation d’attaque, les réponses et la formation associée |

## <a name="attack-simulation-administrator"></a>Administrateur de simulation d'attaque

Les utilisateurs disposant de ce rôle peuvent créer et gérer tous les aspects de la création d'une simulation d'attaque, du lancement ou de la planification d'une simulation, et de l'examen des résultats d'une simulation. Les membres de ce rôle ont accès à toutes les simulations du locataire.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Créer et gérer des charges utiles d’attaque dans le Simulateur d’attaques |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Lire les rapports de simulation d’attaque, les réponses et la formation associée |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Créer et gérer des modèles de simulation d’attaque dans le Simulateur d’attaques |

## <a name="authentication-administrator"></a>Administrateur d’authentification

Les utilisateurs disposant de ce rôle peuvent définir ou réinitialiser toute méthode d’authentification (y compris les mots de passe) pour les non-administrateurs et certains rôles. Les Administrateurs d’authentification peuvent obliger les utilisateurs qui ne sont pas administrateurs ou affectés à certains rôles à se réinscrire avec des informations d’identification sans mot de passe existantes (par exemple, MFA, FIDO) et peuvent également révoquer la **mémorisation de l’authentification multifacteur sur l’appareil**, ce qui permet de demander une authentification multifacteur lors de la prochaine connexion. Pour obtenir la liste des rôles pour lesquels un administrateur d’authentification peut lire ou mettre à jour des méthodes d’authentification, consultez [Autorisations de réinitialisation de mot de passe](#password-reset-permissions).

Le rôle [Administrateur d’authentification privilégié](#privileged-authentication-administrator) est autorisé à forcer la réinscription et l’authentification multifacteur pour tous les utilisateurs.

Le rôle [Administrateur de la stratégie d’authentification](#authentication-policy-administrator) est autorisé à définir la stratégie de méthode d’authentification du locataire déterminant les méthodes que chaque utilisateur peut enregistrer et utiliser.

| Role | Gérer les méthodes d’authentification de l’utilisateur | Gérer l’authentification multifacteur par utilisateur | Gérer les paramètres de l’authentification multifacteur | Gérer la stratégie de méthode d’authentification | Gérer la stratégie de protection par mot de passe |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Administrateur d’authentification | Oui pour certains utilisateurs (voir ci-dessus) | Oui pour certains utilisateurs (voir ci-dessus) | Non | Non | Non |
| Administrateur d’authentification privilégié| Oui pour tous les utilisateurs | Oui pour tous les utilisateurs | Non | Non | Non |
| Administrateur de la stratégie d’authentification | Non |Non | Oui | Oui | Oui |

> [!IMPORTANT]
> Les utilisateurs auxquels ce rôle est assigné peuvent changer les informations d’identification des personnes susceptibles d’avoir accès à des informations sensibles ou privées ou à des configurations critiques à l’intérieur et à l’extérieur d’Azure Active Directory. Changer les informations d’identification d’un utilisateur peut signifier la capacité d’assumer l’identité et les autorisations de cet utilisateur. Par exemple :
>
>* Les propriétaires d’inscription d’application et d’application d’entreprise, qui peuvent gérer les informations d’identification des applications qu’ils possèdent. Ces applications peuvent disposer d’autorisations privilégiées dans Azure AD et ailleurs qui ne sont pas accordées aux administrateurs d’authentification. Par ce biais, un administrateur d’authentification peut assumer l’identité d’un propriétaire d’application, puis d’assumer l’identité d’une application privilégiée en mettant à jour les identifiants de celle-ci.
>* Propriétaires d’abonnement Azure, qui peuvent avoir accès à des informations confidentielles ou privées ou à une configuration critique dans Azure.
>* Propriétaires de groupe de sécurité et de groupe Microsoft 365, qui peuvent gérer l’appartenance à un groupe. Ces groupes peuvent accorder l’accès à des informations sensibles ou privées ou à une configuration critique dans Azure AD et ailleurs.
>* Administrateurs d’autres services en dehors d’Azure AD, tels qu’Exchange Online, le Centre de sécurité et de conformité Office 365 et les systèmes de ressources humaines.
>* Les non-administrateurs comme les cadres supérieurs, les conseillers juridiques et les employés des ressources humaines qui peuvent avoir accès à des informations sensibles ou privées.

> [!IMPORTANT]
> Ce rôle ne peut pas gérer les paramètres de MFA dans le portail de gestion de MFA hérité ou les jetons OATH matériels. Les mêmes fonctions peuvent être effectuées à l’aide du module Azure AD Powershell de l’applet de commande [Set-MsolUser](/powershell/module/msonline/set-msoluser).

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/users/authenticationMethods/create | Créer des méthodes d’authentification pour les utilisateurs |
> | microsoft.directory/users/authenticationMethods/delete | Supprimer les méthodes d’authentification pour les utilisateurs |
> | microsoft.directory/users/authenticationMethods/standard/restrictedRead | Lire les propriétés standard des méthodes d’authentification qui n’incluent pas d’informations d’identification personnelle d’utilisateurs |
> | microsoft.directory/users/authenticationMethods/basic/update | Mettre à jour les propriétés de base des méthodes d’authentification pour les utilisateurs |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forcer la déconnexion en invalidant les jetons d’actualisation de l’utilisateur |
> | microsoft.directory/users/password/update | Réinitialiser les mots de passe de tous les utilisateurs |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="authentication-policy-administrator"></a>Administrateur de la stratégie d’authentification

Les utilisateurs disposant de ce rôle peuvent configurer la stratégie de méthodes d’authentification, les paramètres d’authentification multifacteur à l’ensemble du locataire et la stratégie de protection par mot de passe. Ce rôle accorde l’autorisation de gérer les paramètres de protection par mot de passe : les configurations de verrouillage intelligent et la mise à jour de la liste des mots de passe interdits personnalisés.

Les rôles[Administrateur d’authentification](#authentication-administrator) et [Administrateur d’authentification privilégié](#privileged-authentication-administrator) sont autorisés à gérer les méthodes d’authentification inscrites sur les utilisateurs et peuvent forcer la réinscription et l’authentification multifacteur pour tous les utilisateurs.

| Role | Gérer les méthodes d’authentification de l’utilisateur | Gérer l’authentification multifacteur par utilisateur | Gérer les paramètres de l’authentification multifacteur | Gérer la stratégie de méthode d’authentification | Gérer la stratégie de protection par mot de passe |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Administrateur d’authentification | Oui pour certains utilisateurs (voir ci-dessus) | Oui pour certains utilisateurs (voir ci-dessus) | Non | Non | Non |
| Administrateur d’authentification privilégié| Oui pour tous les utilisateurs | Oui pour tous les utilisateurs | Non | Non | Non |
| Administrateur de la stratégie d’authentification | Non | Non | Oui | Oui | Oui |

> [!IMPORTANT]
> Ce rôle ne peut pas gérer les paramètres de MFA dans le portail de gestion de MFA hérité ou les jetons OATH matériels.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/organization/strongAuthentication/allTasks | Gérer tous les aspects des propriétés d’authentification fortes d’une organisation |
> | microsoft.directory/userCredentialPolicies/create | Créer des stratégies d’informations d’identification pour les utilisateurs |
> | microsoft.directory/userCredentialPolicies/delete | Supprimer des stratégies d’informations d’identification pour les utilisateurs |
> | microsoft.directory/userCredentialPolicies/standard/read | Lire les propriétés standard des stratégies d’informations d’identification pour les utilisateurs |
> | microsoft.directory/userCredentialPolicies/owners/read | Lire les propriétaires des stratégies d’informations d’identification pour les utilisateurs |
> | microsoft.directory/userCredentialPolicies/policyAppliedTo/read | Lire le lien de navigation policy.appliesTo |
> | microsoft.directory/userCredentialPolicies/basic/update | Mettre à jour les stratégies de base pour les utilisateurs |
> | microsoft.directory/userCredentialPolicies/owners/update | Mettre à jour les propriétaires de stratégies d’informations d’identification pour les utilisateurs |
> | microsoft.directory/userCredentialPolicies/tenantDefault/update | Mettre à jour la propriété policy.isOrganizationDefault |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/allProperties/read | Lire une carte de justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/revoke | Révoquer une carte de justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/contracts/create | Créer un contrat de justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/read | Lire un contrat de justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/update | Mettre à jour un contrat de justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/create | Créer la configuration requise pour créer et gérer des justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/delete | Supprimer la configuration requise pour créer et gérer des justificatifs vérifiables et supprimer tous ses justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/read | Lire la configuration requise pour créer et gérer des justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/update | Mettre à jour la configuration requise pour créer et gérer des justificatifs vérifiables |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |

## <a name="azure-ad-joined-device-local-administrator"></a>Administrateur local de l’appareil joint Azure AD

Ce rôle peut uniquement être attribué comme administrateur local supplémentaire dans [Paramètres de l’appareil](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Les utilisateurs dotés de ce rôle deviennent des administrateurs de l'ordinateur local pour tous les appareils Windows 10 qui sont joints à Azure Active Directory. Ils ne peuvent pas gérer des objets appareil dans Azure Active Directory.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/groupSettings/standard/read | Lire les propriétés de base sur des paramètres de groupe |
> | microsoft.directory/groupSettingTemplates/standard/read | Lire les propriétés de base sur des modèles de paramètres de groupe |

## <a name="azure-devops-administrator"></a>Administrateur Azure DevOps

Les utilisateurs disposant de ce rôle peuvent gérer la stratégie Azure DevOps pour restreindre la création d’une nouvelle organisation Azure DevOps à un ensemble d’utilisateurs ou de groupes configurables. Les utilisateurs auxquels ce rôle est attribué peuvent gérer cette stratégie par le biais de n’importe quelle organisation Azure DevOps prise en charge par l’organisation Azure AD de l’entreprise. Ce rôle n’accorde aucune autre autorisation spécifique à Azure DevOps (par exemple, administrateur de collections de projets) au sein de l’une des organisations Azure DevOps prises en charge par l’organisation Azure AD de l’entreprise.

Toutes les stratégies Azure DevOps d’entreprise peuvent être gérées par les utilisateurs de ce rôle.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.azure.devOps/allEntities/allTasks | Lire et configurer Azure DevOps |

## <a name="azure-information-protection-administrator"></a>Administrateur Azure Information Protection

Les utilisateurs avec ce rôle ont toutes les autorisations dans le service Azure Information Protection. Ce rôle permet de configurer les étiquettes pour la stratégie Azure Information Protection, de gérer les modèles de protection et d’activer la protection. Il n’octroie aucune autorisation dans Identity Protection Center, Privileged Identity Management, Monitor Microsoft 365 Service Health ni dans le Centre de sécurité et de conformité Office 365.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.azure.informationProtection/allEntities/allTasks | Gérer tous les aspects d’Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="b2c-ief-keyset-administrator"></a>Administrateur de jeux de clés B2C IEF

L’utilisateur peut créer et gérer des clés et secrets de stratégie pour le chiffrement des jetons, la signatures des jetons et le chiffrement/déchiffrement des revendications. En ajoutant de nouvelles clés aux conteneurs de clés existants, cet administrateur limité peut substituer des secrets en fonction des besoins sans affecter les applications existantes.  Cet utilisateur peut voir le contenu complet de ces secrets et leurs dates d’expiration, même après leur création.

> [!IMPORTANT]
> Il s’agit d’un rôle sensible.  Le rôle d’administrateur de jeux de clés doit être soigneusement audité et attribué avec précaution pendant les périodes de préproduction et de production.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkKeySet/allProperties/allTasks | Lire et mettre à jour toutes les propriétés des stratégies d’autorisation |

## <a name="b2c-ief-policy-administrator"></a>Administrateur de stratégies B2C IEF

Les utilisateurs assignés à ce rôle ont la possibilité de créer, lire, mettre à jour et supprimer toutes les stratégies personnalisées dans Azure AD B2C, et donc de contrôler totalement la plateforme Identity Experience Framework dans l’organisation Azure AD B2C concernée. En modifiant les stratégies, cet utilisateur peut établir une fédération directe avec des fournisseurs d’identité externes, modifier le schéma d’annuaire, modifier tout le contenu visible par l’utilisateur (HTML, CSS et JavaScript), modifier les exigences liées à une authentification, créer des utilisateurs, envoyer des données utilisateur à des systèmes externes, notamment des migrations complètes, et modifier toutes les informations utilisateur, notamment des champs sensibles comme les mots de passe et les numéros de téléphone. À l’inverse, ce rôle ne peut pas modifier les clés de chiffrement ou modifier les secrets utilisés pour la fédération dans l’organisation.

> [!IMPORTANT]
> L’administrateur de stratégies B2C IEF est un rôle très sensible qui doit être attribué de manière très limitée pour les organisations en production.  Les activités de ces utilisateurs doivent faire l’objet d’un audit précis, en particulier pour les organisations en production.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/b2cTrustFrameworkPolicy/allProperties/allTasks | Lire et configurer des jeux de clés dans Azure Active Directory B2C |

## <a name="billing-administrator"></a>Administrateur de facturation

Effectue les achats, gère les abonnements, gère les tickets de support et supervise l’intégrité du service.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/organization/basic/update | Mettre à jour des propriétés de base sur une organisation |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.commerce.billing/allEntities/allTasks | Gérer tous les aspects de la facturation Office 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="cloud-app-security-administrator"></a>Administrateur de sécurité d’application cloud

Les utilisateurs disposant de ce rôle disposent d’autorisations complètes dans la Sécurité d’application cloud. Ils peuvent ajouter des administrateurs, ajouter des stratégies et des paramètres de Microsoft Cloud App Security (MCAS), télécharger des journaux et effectuer des actions de gouvernance.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Créer et supprimer toutes les ressources, ainsi que lire et mettre à jour les propriétés standard dans Microsoft Cloud App Security |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="cloud-application-administrator"></a>Administrateur d'applications cloud

Les utilisateurs dans ce rôle ont les mêmes autorisations que celles du rôle Administrateur d’application, sans la possibilité de gérer le proxy d’application. Ce rôle permet de créer et de gérer tous les aspects des applications d’entreprise et des inscriptions d’applications. Les utilisateurs affectés à ce rôle ne sont pas ajoutés en tant que propriétaires lorsque des inscriptions d’applications ou des applications d’entreprise sont créées.

Ce rôle permet également de consentir des autorisations déléguées et autorisations d’application, à l’exclusion des autorisations d’application pour Microsoft Graph et Azure AD Graph.

> [!IMPORTANT]
> Cette exception signifie que vous pouvez toujours accepter les autorisations pour _d’autres applications_ (par exemple les applications Microsoft ou les applications que vous avez inscrites). Vous pouvez toujours _demander_ ces autorisations dans le cadre de l’inscription d’application, mais l’_octroi_ de ces autorisations requiert un administrateur plus privilégié, tel un administrateur général.
>
>Ce rôle permet de gérer des informations d’identification d’application. Les utilisateurs auxquels ce rôle a été attribué peuvent ajouter des informations d’identification à une application et les utiliser pour emprunter l’identité de l’application. Si l’identité de l’application a obtenu l’accès à une ressource, telle que la possibilité de créer ou de mettre à jour un utilisateur ou d’autres objets, un utilisateur auquel ce rôle a été attribué peut effectuer ces actions en empruntant l’identité de l’application. Cette capacité à emprunter l’identité de l’application peut correspondre une élévation de privilèges que l’utilisateur réalise dans ses attributions de rôle. Il est important de comprendre que l’affectation d’un utilisateur au rôle Administrateur d’applications lui donne la possibilité d’emprunter l’identité d’une application.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/applications/create | Créer tous les types d’applications |
> | microsoft.directory/applications/delete | Supprimer tous les types d’applications |
> | microsoft.directory/applications/appRoles/update | Mettre à jour la propriété appRoles sur tous les types d’applications |
> | microsoft.directory/applications/audience/update | Mettre à jour la propriété audience pour les applications |
> | microsoft.directory/applications/authentication/update | Mettre à jour l’authentification sur tous les types d’applications |
> | microsoft.directory/applications/basic/update | Mettre à jour les propriétés de base pour les applications |
> | microsoft.directory/applications/credentials/update | Mettre à jour les informations d’identification d’application |
> | microsoft.directory/applications/extensionProperties/update | Mettre à jour les propriétés d’extension sur les applications |
> | microsoft.directory/applications/notes/update | Mettre à jour les notes d’applications |
> | microsoft.directory/applications/owners/update | Mettre à jour les propriétaires d’applications |
> | microsoft.directory/applications/permissions/update | Mettre à jour les autorisations exposées et les autorisations requises sur tous les types d’applications |
> | microsoft.directory/applications/policies/update | Mettre à jour les stratégies d’applications |
> | microsoft.directory/applications/tag/update | Mettre à jour les balises d’applications |
> | microsoft.directory/applications/verification/update | Mettre à jour la propriété applicationsverification |
> | microsoft.directory/applications/synchronization/standard/read | Lire les paramètres de provisionnement associés à l’objet application |
> | microsoft.directory/applicationTemplates/instantiate | Instancier des applications de la galerie à partir de modèles d’application |
> | microsoft.directory/auditLogs/allProperties/read | Lire toutes les propriétés dans les journaux d’audit, y compris les propriétés privilégiées |
> | microsoft.directory/deletedItems.applications/delete | Supprimer définitivement les applications qui ne peuvent plus être restaurées |
> | microsoft.directory/deletedItems.applications/restore | Restaurer les applications supprimées de manière réversible à l’état d’origine |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Créer et supprimer des octrois d’autorisations OAuth 2.0, et lire et mettre à jour toutes les propriétés |
> | microsoft.directory/applicationPolicies/create | Créer des stratégies d’application |
> | microsoft.directory/applicationPolicies/delete | Supprimer des stratégies d’application |
> | microsoft.directory/applicationPolicies/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.directory/applicationPolicies/owners/read | Lire les propriétaires des stratégies d’application |
> | microsoft.directory/applicationPolicies/policyAppliedTo/read | Lire les stratégies d’application appliquées à la liste d’objets |
> | microsoft.directory/applicationPolicies/basic/update | Mettre à jour les propriétés standard des stratégies d’application |
> | microsoft.directory/applicationPolicies/owners/update | Mettre à jour la propriété Owner des stratégies d’application |
> | microsoft.directory/provisioningLogs/allProperties/read | Lire toutes les propriétés des journaux de provisionnement. |
> | microsoft.directory/servicePrincipals/create | Créer des principaux de service |
> | microsoft.directory/servicePrincipals/delete | Supprimer des principaux de service |
> | microsoft.directory/servicePrincipals/disable | Désactiver des principaux de service |
> | microsoft.directory/servicePrincipals/enable | Activer des principaux de service |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Gérer les informations d’identification de l’authentification unique par mot de passe sur les principaux de service |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Gérer les secrets et les informations d’identification de l’approvisionnement des applications |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Démarrer, redémarrer et suspendre les travaux de synchronisation d’approvisionnement des applications |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Créer et gérer le schéma et les tâches de synchronisation de l’approvisionnement des applications |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Lire les informations d’identification de l’authentification unique par mot de passe sur les principaux de service |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-application-admin | Consentir des autorisations d’application et des autorisations déléguées pour le compte d’un utilisateur ou de tous les utilisateurs, à l’exclusion des autorisations d’application pour Microsoft Graph et Azure AD Graph |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Mettre à jour les attributions de rôles des principaux de service |
> | microsoft.directory/servicePrincipals/audience/update | Mettre à jour les propriétés d’audience sur les principaux de service |
> | microsoft.directory/servicePrincipals/authentication/update | Mettre à jour les propriétés d’authentification sur les principaux de service |
> | microsoft.directory/servicePrincipals/basic/update | Mettre à jour les propriétés de base sur les principaux de service |
> | microsoft.directory/servicePrincipals/credentials/update | Mettre à jour informations d’identification des principaux de service |
> | microsoft.directory/servicePrincipals/notes/update | Mettre à jour les notes de principaux de service |
> | microsoft.directory/servicePrincipals/owners/update | Mettre à jour les propriétaires de principaux de service |
> | microsoft.directory/servicePrincipals/permissions/update | Mettre à jour les autorisations des principaux de service |
> | microsoft.directory/servicePrincipals/policies/update | Mettre à jour les stratégies des principaux de service |
> | microsoft.directory/servicePrincipals/tag/update | Mettre à jour la propriété Tag pour les principaux de service |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Lire les paramètres de provisionnement associés au principal de service |
> | microsoft.directory/signInReports/allProperties/read | Lire toutes les propriétés dans les rapports de connexion, y compris les propriétés privilégiées |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="cloud-device-administrator"></a>Administrateur d’appareil cloud

Les utilisateurs dans ce rôle peuvent activer, désactiver et supprimer des appareils dans Azure AD, et lire des clés BitLocker Windows 10 (le cas échéant) dans le portail Azure. Ce rôle ne permet pas de gérer d’autres propriétés sur l’appareil.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Lire toutes les propriétés dans les journaux d’audit, y compris les propriétés privilégiées |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.directory/bitlockerKeys/key/read | Lire les métadonnées et la clé BitLocker sur les appareils |
> | microsoft.directory/devices/delete | Supprimer des appareils d’Azure AD |
> | microsoft.directory/devices/disable | Désactiver des appareils dans Azure AD |
> | microsoft.directory/devices/enable | Activer des appareils dans Azure AD |
> | microsoft.directory/deviceManagementPolicies/standard/read | Lire les propriétés standard sur les stratégies d’application de gestion des appareils |
> | microsoft.directory/deviceManagementPolicies/basic/update | Mettre à jour les propriétés de base des stratégies d’application de gestion des appareils |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Lire les propriétés standard sur les stratégies d’inscription des appareils |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | Mettre à jour les propriétés de base des stratégies d’inscription d’appareil |
> | microsoft.directory/signInReports/allProperties/read | Lire toutes les propriétés dans les rapports de connexion, y compris les propriétés privilégiées |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |

## <a name="compliance-administrator"></a>Administrateur de conformité

Les utilisateurs avec ce rôle disposent des autorisations pour gérer les fonctionnalités liées à la conformité dans le centre de conformité Microsoft 365, le centre d'administration Microsoft 365, Azure et le Centre de sécurité et conformité Office 365. Les personnes responsables peuvent également gérer toutes les fonctionnalités dans le centre d’administration Exchange et le centre d’administration Teams et Skype Entreprise, et créer des tickets de support pour Azure et Microsoft 365. Pour plus d’informations, consultez [À propos des rôles d’administrateur Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

Dans | Peut
----- | ----------
[Centre de conformité Microsoft 365](https://protection.office.com) | Protéger et gérer les données de votre organisation dans les services Microsoft 365<br>Gérer les alertes de conformité
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Suivre, affecter et vérifier les activités de conformité réglementaire de votre organisation
[Centre de sécurité et conformité Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gérer la gouvernance des données<br>Procéder à l'examen juridique des données<br>Gérer une requête d'objet de données<br><br>Ce rôle a les mêmes autorisations que le [RoleGroup Administrateur de conformité](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) dans le contrôle d’accès en fonction du rôle du Centre de sécurité et conformité Office 365.
[Intune](/intune/role-based-access-control) | Afficher toutes les données d’audit Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Dispose d'autorisations en lecture seule et peut gérer les alertes<br>Peut créer et modifier les stratégies de fichier et autoriser des actions de gouvernance de fichier<br>Peut afficher tous les rapports intégrés sous Gestion des données

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.directory/entitlementManagement/allProperties/read | Lire toutes les propriétés dans la fonctionnalité de gestion des droits d’utilisation Azure AD |
> | microsoft.office365.complianceManager/allEntities/allTasks | Gérez tous les aspects d’Office 365 Compliance Manager |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="compliance-data-administrator"></a>Administrateur des données de conformité

Les utilisateurs dotés de ce rôle sont autorisés à suivre les données dans le centre de conformité Microsoft 365, le centre d’administration Microsoft 365 et Azure. Les utilisateurs peuvent également suivre les données de conformité dans le centre d’administration Exchange, le Gestionnaire de compatibilité, le centre d’administration Teams et Skype Entreprise, et créer des tickets de support pour Azure et Microsoft 365. [Cette documentation](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) contient des détails sur les différences entre Administrateur de conformité et Administrateur des données de conformité.

Dans | Peut
----- | ----------
[Centre de conformité Microsoft 365](https://protection.office.com) | Superviser les stratégies de conformité au sein des services Microsoft 365<br>Gérer les alertes de conformité
[Compliance Manager](/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Suivre, affecter et vérifier les activités de conformité réglementaire de votre organisation
[Centre de sécurité et conformité Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gérer la gouvernance des données<br>Procéder à l'examen juridique des données<br>Gérer une requête d'objet de données<br><br>Ce rôle a les mêmes autorisations que le [RoleGroup Administrateur de données de conformité](/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) dans le contrôle d’accès en fonction du rôle du Centre de sécurité et conformité Office 365.
[Intune](/intune/role-based-access-control) | Afficher toutes les données d’audit Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Dispose d'autorisations en lecture seule et peut gérer les alertes<br>Peut créer et modifier les stratégies de fichier et autoriser des actions de gouvernance de fichier<br>Peut afficher tous les rapports intégrés sous Gestion des données

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Créer et supprimer toutes les ressources, ainsi que lire et mettre à jour les propriétés standard dans Microsoft Cloud App Security |
> | microsoft.azure.informationProtection/allEntities/allTasks | Gérer tous les aspects d’Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.complianceManager/allEntities/allTasks | Gérez tous les aspects d’Office 365 Compliance Manager |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="conditional-access-administrator"></a>Administrateur de l’accès conditionnel

Les utilisateurs avec ce rôle ont la possibilité de gérer les paramètres d’accès conditionnel Azure Active Directory.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/conditionalAccessPolicies/create | Créer des stratégies d’accès conditionnel |
> | microsoft.directory/conditionalAccessPolicies/delete | Supprimer des stratégies d’accès conditionnel |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Accès conditionnel en lecture pour les stratégies |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Lire les propriétaires des stratégies d’accès conditionnel |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Lire la propriété « appliqué à » pour les stratégies d’accès conditionnel |
> | microsoft.directory/conditionalAccessPolicies/basic/update | Mettre à jour les propriétés de base pour les stratégies d’accès conditionnel |
> | microsoft.directory/conditionalAccessPolicies/owners/update | Mettre à jour les propriétaires des stratégies d’accès conditionnel |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | Mettre à jour le locataire par défaut pour les stratégies d’accès conditionnelles |
> | microsoft.directory/crossTenantAccessPolicies/create | Créer des stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/delete | Supprimer des stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/standard/read | Lire les propriétés de base des stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/owners/read | Lire les propriétaires de stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/policyAppliedTo/read | Lire la propriété policyAppliedTo des stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/basic/update | Mettre à jour les propriétés de base des stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/owners/update | Mettre à jour les propriétaires de stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/tenantDefault/update | Mettre à jour le locataire par défaut pour les stratégies d’accès inter-locataires |

## <a name="customer-lockbox-access-approver"></a>Approbateur d’accès à Customer LockBox

Gère les [requêtes Customer Lockbox](/office365/admin/manage/customer-lockbox-requests) dans votre organisation. Il reçoit par e-mail des notifications ayant trait aux requêtes Customer Lockbox, et peut approuver ou refuser ces requêtes depuis le centre d'administration Microsoft 365. Il peut activer ou désactiver la fonctionnalité Customer Lockbox. Seuls les administrateurs généraux peuvent réinitialiser les mots de passe des personnes dotées de ce rôle.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.office365.lockbox/allEntities/allTasks | Gérer tous les aspects de Customer Lockbox |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="desktop-analytics-administrator"></a>Administrateur Desktop Analytics

Les utilisateurs ayant ce rôle peuvent gérer le service Desktop Analytics. Ils ont notamment la possibilité d’afficher l’inventaire des ressources, de créer des plans de déploiement et de visualiser l’état du déploiement et de l’intégrité.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Gérer tous les aspects de Desktop Analytics |

## <a name="directory-readers"></a>Lecteurs de répertoires

Les utilisateurs de ce rôle peuvent lire des informations de base relatives aux répertoires. Ce rôle doit être utilisé pour :

* Accorder à un ensemble spécifique d’utilisateurs invités un accès en lecture au lieu de l’accorder à tous les utilisateurs invités.
* Accorder à un ensemble spécifique d’utilisateurs non-administrateurs l’accès au portail Azure quand l’option « Restreindre l’accès au portail Azure AD aux seuls administrateurs » a la valeur « Oui ».
* Accorder un accès aux principaux de service au répertoire quand Directory.Read.All n’est pas possible.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/administrativeUnits/standard/read | Lire les propriétés de base sur les unités administratives |
> | microsoft.directory/administrativeUnits/members/read | Lire les membres des unités administratives |
> | microsoft.directory/applications/standard/read | Lire les propriétés standard des applications |
> | microsoft.directory/applications/owners/read | Lire les propriétaires d’applications |
> | microsoft.directory/applications/policies/read | Lire les stratégies d’applications |
> | microsoft.directory/contacts/standard/read | Lire des propriétés de base sur les contacts dans Azure AD |
> | microsoft.directory/contacts/memberOf/read | Lire l’appartenance à un groupe pour tous les contacts dans Azure AD |
> | microsoft.directory/contracts/standard/read | Lire les propriétés de base sur les contrats de partenaire |
> | microsoft.directory/devices/standard/read | Lire les propriétés de base sur les appareils |
> | microsoft.directory/devices/memberOf/read | Lire les appartenances aux appareils |
> | microsoft.directory/devices/registeredOwners/read | Lire les propriétaires inscrits d’appareils |
> | microsoft.directory/devices/registeredUsers/read | Lire les utilisateurs inscrits d’appareils |
> | microsoft.directory/directoryRoles/standard/read | Mettre à jour les propriétés de base dans les rôles Azure AD |
> | microsoft.directory/directoryRoles/eligibleMembers/read | Lire les membres éligibles des rôles Azure AD |
> | microsoft.directory/directoryRoles/members/read | Lire tous les membres des rôles Azure AD |
> | microsoft.directory/domains/standard/read | Lire les propriétés de base sur les domaines |
> | microsoft.directory/groups/standard/read | Lire les propriétés standard des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groups/appRoleAssignments/read | Lire les attributions de rôles d’application des groupes |
> | microsoft.directory/groups/memberOf/read | Lire la propriété memberOf des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groups/members/read | Lire la propriété membres des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groups/owners/read | Lire la propriété propriétaires des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groups/settings/read | Lire les paramètres des groupes |
> | microsoft.directory/groupSettings/standard/read | Lire les propriétés de base sur des paramètres de groupe |
> | microsoft.directory/groupSettingTemplates/standard/read | Lire les propriétés de base sur des modèles de paramètres de groupe |
> | microsoft.directory/oAuth2PermissionGrants/standard/read | Lire les propriétés de base sur les octrois d’autorisations OAuth 2.0 |
> | microsoft.directory/organization/standard/read | Lire les propriétés de base sur une organisation |
> | microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Lire les autorités de certification approuvées pour l’authentification par mot de passe |
> | microsoft.directory/applicationPolicies/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.directory/roleAssignments/standard/read | Lire les propriétés de base sur les attributions de rôles |
> | microsoft.directory/roleDefinitions/standard/read | Lire les propriétés de base sur les définitions de rôles |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Lire les attributions de rôles des principaux de service |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Lire les attributions de rôles affectées aux principaux de service |
> | microsoft.directory/servicePrincipals/standard/read | Lire toutes les propriétés des principaux du service |
> | microsoft.directory/servicePrincipals/memberOf/read | Lire les appartenances aux groupes sur les principaux de service |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Lire les octrois d’autorisations déléguées sur les principaux de service |
> | microsoft.directory/servicePrincipals/owners/read | Lire les propriétaires des principaux de service |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Lire les objets détenus des principaux de service |
> | microsoft.directory/servicePrincipals/policies/read | Lire les stratégies des principaux de service |
> | microsoft.directory/subscribedSkus/standard/read | Lire les propriétés de base sur les abonnements |
> | microsoft.directory/users/standard/read | Lire les propriétés de base sur les utilisateurs |
> | microsoft.directory/users/appRoleAssignments/read | Lire les attributions de rôle d’application des utilisateurs |
> | microsoft.directory/users/deviceForResourceAccount/read | Lire deviceForResourceAccount des utilisateurs |
> | microsoft.directory/users/directReports/read | Lire les collaborateurs directs des utilisateurs |
> | microsoft.directory/users/licenseDetails/read | Lire les détails de licence des utilisateurs |
> | microsoft.directory/users/manager/read | Lire les gestionnaires d’utilisateurs |
> | microsoft.directory/users/memberOf/read | Lire les appartenances aux groupes des utilisateurs |
> | microsoft.directory/users/oAuth2PermissionGrants/read | Lire les octrois d’autorisations déléguées sur les utilisateurs |
> | microsoft.directory/users/ownedDevices/read | Lire les appareils détenus des utilisateurs |
> | microsoft.directory/users/ownedObjects/read | Lire les objets détenus des utilisateurs |
> | microsoft.directory/users/photo/read | Lire la photo des utilisateurs |
> | microsoft.directory/users/registeredDevices/read | Lire les appareils inscrits des utilisateurs |
> | microsoft.directory/users/scopedRoleMemberOf/read | Lire l’appartenance d’un utilisateur à un rôle Azure AD étendu à une unité administrative |

## <a name="directory-synchronization-accounts"></a>Comptes de synchronisation d’annuaires

Ne pas utiliser. Ce rôle est automatiquement attribué au service Azure AD Connect et n’est pas prévu ni pris en charge pour une autre utilisation.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/applications/create | Créer tous les types d’applications |
> | microsoft.directory/applications/delete | Supprimer tous les types d’applications |
> | microsoft.directory/applications/appRoles/update | Mettre à jour la propriété appRoles sur tous les types d’applications |
> | microsoft.directory/applications/audience/update | Mettre à jour la propriété audience pour les applications |
> | microsoft.directory/applications/authentication/update | Mettre à jour l’authentification sur tous les types d’applications |
> | microsoft.directory/applications/basic/update | Mettre à jour les propriétés de base pour les applications |
> | microsoft.directory/applications/credentials/update | Mettre à jour les informations d’identification d’application |
> | microsoft.directory/applications/notes/update | Mettre à jour les notes d’applications |
> | microsoft.directory/applications/owners/update | Mettre à jour les propriétaires d’applications |
> | microsoft.directory/applications/permissions/update | Mettre à jour les autorisations exposées et les autorisations requises sur tous les types d’applications |
> | microsoft.directory/applications/policies/update | Mettre à jour les stratégies d’applications |
> | microsoft.directory/applications/tag/update | Mettre à jour les balises d’applications |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.directory/organization/dirSync/update | Mettre à jour la propriété de synchronisation d’annuaire d’organisation |
> | microsoft.directory/policies/create | Créer des stratégies dans Azure AD |
> | microsoft.directory/policies/delete | Supprimer des stratégies Azure AD |
> | microsoft.directory/policies/standard/read | Lire les propriétés de base sur les stratégies |
> | microsoft.directory/policies/owners/read | Lire les propriétaires de stratégies |
> | microsoft.directory/policies/policyAppliedTo/read | Lire la propriété policies.policyAppliedTo |
> | microsoft.directory/policies/basic/update | Mettre à jour les propriétés de base sur les stratégies |
> | microsoft.directory/policies/owners/update | Mettre à jour les propriétaires de stratégies |
> | microsoft.directory/policies/tenantDefault/update | Mettre à jour les stratégies d’organisation par défaut |
> | microsoft.directory/servicePrincipals/create | Créer des principaux de service |
> | microsoft.directory/servicePrincipals/delete | Supprimer des principaux de service |
> | microsoft.directory/servicePrincipals/enable | Activer des principaux de service |
> | microsoft.directory/servicePrincipals/disable | Désactiver des principaux de service |
> | microsoft.directory/servicePrincipals/getPasswordSingleSignOnCredentials | Gérer les informations d’identification de l’authentification unique par mot de passe sur les principaux de service |
> | microsoft.directory/servicePrincipals/managePasswordSingleSignOnCredentials | Lire les informations d’identification de l’authentification unique par mot de passe sur les principaux de service |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Lire les attributions de rôles des principaux de service |
> | microsoft.directory/servicePrincipals/appRoleAssignments/read | Lire les attributions de rôles affectées aux principaux de service |
> | microsoft.directory/servicePrincipals/standard/read | Lire toutes les propriétés des principaux du service |
> | microsoft.directory/servicePrincipals/memberOf/read | Lire les appartenances aux groupes sur les principaux de service |
> | microsoft.directory/servicePrincipals/oAuth2PermissionGrants/read | Lire les octrois d’autorisations déléguées sur les principaux de service |
> | microsoft.directory/servicePrincipals/owners/read | Lire les propriétaires des principaux de service |
> | microsoft.directory/servicePrincipals/ownedObjects/read | Lire les objets détenus des principaux de service |
> | microsoft.directory/servicePrincipals/policies/read | Lire les stratégies des principaux de service |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Mettre à jour les attributions de rôles des principaux de service |
> | microsoft.directory/servicePrincipals/audience/update | Mettre à jour les propriétés d’audience sur les principaux de service |
> | microsoft.directory/servicePrincipals/authentication/update | Mettre à jour les propriétés d’authentification sur les principaux de service |
> | microsoft.directory/servicePrincipals/basic/update | Mettre à jour les propriétés de base sur les principaux de service |
> | microsoft.directory/servicePrincipals/credentials/update | Mettre à jour informations d’identification des principaux de service |
> | microsoft.directory/servicePrincipals/notes/update | Mettre à jour les notes de principaux de service |
> | microsoft.directory/servicePrincipals/owners/update | Mettre à jour les propriétaires de principaux de service |
> | microsoft.directory/servicePrincipals/permissions/update | Mettre à jour les autorisations des principaux de service |
> | microsoft.directory/servicePrincipals/policies/update | Mettre à jour les stratégies des principaux de service |
> | microsoft.directory/servicePrincipals/tag/update | Mettre à jour la propriété Tag pour les principaux de service |

## <a name="directory-writers"></a>Enregistreurs de répertoire

Les utilisateurs de ce rôle peuvent lire et mettre à jour les informations de base des utilisateurs, des groupes et des principaux de service. Attribuez ce rôle uniquement aux applications qui ne prennent pas en charge [l’infrastructure de consentement](../develop/quickstart-register-app.md). Vous ne devez pas l’affecter à tous les utilisateurs.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/applications/extensionProperties/update | Mettre à jour les propriétés d’extension sur les applications |
> | microsoft.directory/groups/assignLicense | Attribuer des licences de produits à des groupes pour la gestion des licences basée sur un groupe |
> | microsoft.directory/groups/create | Créer des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/reprocessLicenseAssignment | Retraiter les attributions de licence pour la gestion des licences basée sur un groupe |
> | microsoft.directory/groups/basic/update | Actualiser les propriétés de base des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/classification/update | Actualiser les propriétés de classification des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/dynamicMembershipRule/update | Actualiser la règle d’adhésion dynamique des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/groupType/update | Actualiser les propriétés qui affectent le type de groupe des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/members/update | Actualiser la propriété membres des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/onPremWriteBack/update | Mettre à jour les groupes Azure Active Directory pour être réécrits en local avec Azure AD Connect |
> | microsoft.directory/groups/owners/update | Actualiser la propriété propriétaires des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/settings/update | Mettre à jour les paramètres des groupes |
> | microsoft.directory/groups/visibility/update | Actualiser la propriété de visibilité des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groupSettings/create | Créer des paramètres de groupe |
> | microsoft.directory/groupSettings/delete | Supprimer des paramètres de groupe |
> | microsoft.directory/groupSettings/basic/update | Mettre à jour les propriétés de base sur les paramètres de groupe |
> | microsoft.directory/oAuth2PermissionGrants/create | Créer des octrois d’autorisations OAuth 2.0 |
> | microsoft.directory/oAuth2PermissionGrants/basic/update | Mettre à jour les octrois d’autorisations OAuth 2.0 |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Gérer les secrets et les informations d’identification de l’approvisionnement des applications |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Démarrer, redémarrer et suspendre les travaux de synchronisation d’approvisionnement des applications |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Créer et gérer le schéma et les tâches de synchronisation de l’approvisionnement des applications |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Accorder à un principal de service l’accès direct aux données d’un groupe |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Mettre à jour les attributions de rôles des principaux de service |
> | microsoft.directory/users/assignLicense | Gérer les licences utilisateur |
> | microsoft.directory/users/create | Ajouter des utilisateurs |
> | microsoft.directory/users/disable | Désactiver des utilisateurs |
> | microsoft.directory/users/enable | Activer des utilisateurs |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forcer la déconnexion en invalidant les jetons d’actualisation de l’utilisateur |
> | microsoft.directory/users/inviteGuest | Inviter des utilisateurs |
> | microsoft.directory/users/reprocessLicenseAssignment | Retraiter les attributions de licence pour les utilisateurs |
> | microsoft.directory/users/basic/update | Mettre à jour les propriétés de base sur les utilisateurs |
> | microsoft.directory/users/manager/update | Mettre à jour le gestionnaire pour les utilisateurs |
> | microsoft.directory/users/photo/update | Actualiser la photo des utilisateurs |
> | microsoft.directory/users/userPrincipalName/update | Mettre à jour le nom d’utilisateur principal des utilisateurs |

## <a name="domain-name-administrator"></a>Administrateur de nom de domaine

Les utilisateurs disposant de ce rôle peuvent gérer (lire, ajouter, vérifier, mettre à jour et supprimer) des noms de domaine. Ils peuvent également lire les informations du répertoire sur les utilisateurs, les groupes et les applications, car ces objets possèdent des dépendances de domaine. Pour les environnements locaux, les utilisateurs disposant de ce rôle peuvent configurer des noms de domaine pour la fédération afin que les utilisateurs associés soient toujours authentifiés localement. Ces utilisateurs peuvent ensuite se connecter à des services Azure AD avec leurs mots de passe locaux par le biais de l’authentification unique. Les paramètres de fédération doivent être synchronisés via Azure AD Connect, afin que les utilisateurs disposent également des autorisations nécessaires pour gérer Azure AD Connect.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/domains/allProperties/allTasks | Créer et supprimer des domaines, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="dynamics-365-administrator"></a>Administrateur Dynamics 365

Les utilisateurs auxquels ce rôle est assigné disposent d’autorisations générales dans Microsoft Dynamics 365 Online, lorsque le service est présent. Ils ont aussi la possibilité de gérer les tickets de support et de surveiller l’intégrité du service. Pour plus d’informations, consultez la page [Utiliser le rôle d’administrateur de service pour gérer votre organisation Azure AD](/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

> [!NOTE]
> Dans l’API Microsoft Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de service Dynamics 365 ». Il est appelé « Administrateur Dynamics 365 » dans le [portail Azure](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.dynamics365/allEntities/allTasks | Gérez tous les aspects de Dynamics 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="edge-administrator"></a>Administrateur Edge

Les utilisateurs ayant ce rôle peuvent créer et gérer la liste des sites d’entreprise requise pour le mode Internet Explorer sur Microsoft Edge. Ce rôle accorde des autorisations pour créer, modifier et publier la liste de sites, ainsi qu’un accès pour gérer les tickets de support. [En savoir plus](https://go.microsoft.com/fwlink/?linkid=2165707)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.edge/allEntities/allProperties/allTasks | Gérer tous les aspects de Microsoft Edge |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="exchange-administrator"></a>Administrateur Exchange

Les utilisateurs avec ce rôle ont des autorisations générales dans Microsoft Exchange Online, quand le service est présent. Ils ont aussi la possibilité de créer et de gérer tous les groupes Microsoft 365, de gérer les tickets de support et de surveiller l’état d’intégrité des services. Plus d’informations sur les [Rôles d’administrateur dans Microsoft 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Dans l’API Microsoft Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de service Exchange ». Il est appelé « Administrateur Exchange » dans le [portail Azure](https://portal.azure.com). Il est « Administrateur Exchange en ligne » dans le [centre d’administration Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144).

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/deletedItems.groups/delete | Supprimer définitivement les groupes qui ne peuvent plus être restaurés |
> | microsoft.directory/deletedItems.groups/restore | Restaurer les groupes supprimés de manière réversible à l’état d’origine |
> | microsoft.directory/groups/hiddenMembers/read | Lire la propriété membres masqués des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groups.unified/create | Créer des groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/delete | Supprimer des groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/restore | Restaurer des groupes Microsoft 365 |
> | microsoft.directory/groups.unified/basic/update | Actualiser les propriétés de base sur les groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/members/update | Actualiser les membres de groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/owners/update | Actualiser les propriétaires de groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Gérez tous les aspects d’Exchange Online |
> | microsoft.office365.network/performance/allProperties/read | Lire toutes les propriétés de performances réseau dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lire les rapports d’utilisation d’Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="exchange-recipient-administrator"></a>Administrateur des destinataires Exchange

Les utilisateurs dotés de ce rôle disposent d’un accès en lecture aux destinataires et d’un accès en écriture aux attributs de ces destinataires dans Exchange Online. Plus d’informations sur les [destinataires Exchange](/exchange/recipients/recipients).

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.office365.exchange/allRecipients/allProperties/allTasks | Créez et supprimez tous les destinataires, et lisez et mettez à jour toutes les propriétés des destinataires dans Exchange Online |
> | microsoft.office365.exchange/messageTracking/allProperties/allTasks | Gérez toutes les tâches pour le suivi des messages dans Exchange Online |
> | microsoft.office365.exchange/migration/allProperties/allTasks | Gérez toutes les tâches liées à la migration des destinataires dans Exchange Online |

## <a name="external-id-user-flow-administrator"></a>Administrateur de flux d’utilisateurs ID externe

Les utilisateurs dotés de ce rôle peuvent créer et gérer des flux d’utilisateurs (également appelés stratégies « intégrées ») sur le Portail Azure. Ces utilisateurs peuvent personnaliser le contenu HTML/CSS/JavaScript, modifier les conditions d’authentification multifacteur, sélectionner des revendications dans le jeton, gérer les connecteurs d’API et configurer les paramètres de session pour tous les flux d’utilisateurs de l’organisation Azure AD. En revanche, ce rôle ne prévoit pas la possibilité de consulter les données utilisateur ni d’apporter des modifications aux attributs inclus dans le schéma de l’organisation. La modification des stratégies Identity Experience Framework (également appelées stratégies personnalisées) sort également de la portée de ce rôle.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/b2cUserFlow/allProperties/allTasks | Lire et configurer les attributs utilisateur dans Azure Active Directory B2C |

## <a name="external-id-user-flow-attribute-administrator"></a>Administrateur d’attribut de flux d’utilisateurs ID externe

Les utilisateurs ayant ce rôle peuvent ajouter ou supprimer des attributs personnalisés disponibles pour tous les flux d’utilisateurs dans l’organisation Azure AD.  Ainsi, ils peuvent modifier ou ajouter de nouveaux éléments au schéma d’utilisateur final, influer sur le comportement de tous les flux d’utilisateurs et entraîner indirectement des modifications des données demandées aux utilisateurs finaux et envoyées sous forme de revendications aux applications.  Ce rôle ne peut pas modifier des flux d’utilisateurs.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/b2cUserAttribute/allProperties/allTasks | Lire et configurer les stratégies personnalisées dans Azure Active Directory B2C |

## <a name="external-identity-provider-administrator"></a>Administrateur de fournisseurs d’identité externes

Cet administrateur gère la fédération entre les organisations Azure AD et les fournisseurs d’identité externes.  Avec ce rôle, les utilisateurs peuvent ajouter de nouveaux fournisseurs d’identité et configurer tous les paramètres disponibles (par exemple, chemin d’authentification, ID de service, conteneurs de clés attribués).  Cet utilisateur peut permettre à l’organisation d’approuver des authentifications de fournisseurs d’identité externes.  L’impact produit sur les expériences d’utilisateur final varie selon le type d’organisation :

* Organisations Azure AD pour les employés et les partenaires : L’ajout d’une fédération (par exemple, avec Gmail) impacte immédiatement toutes les invitations d’invités qui ne sont pas encore utilisées. Consultez [Ajout de Google comme fournisseur d’identité pour les utilisateurs invités B2B](../external-identities/google-federation.md).
* Organisations Azure Active Directory B2C : L’ajout d’une fédération (par exemple, avec Facebook ou une autre organisation Azure AD) n’impacte pas immédiatement les flux d’utilisateurs finaux tant que le fournisseur d’identité n’est pas ajouté comme option dans un flux d’utilisateurs (aussi appelé stratégie intégrée). Consultez [Configuration d’un compte Microsoft comme fournisseur d’identité](../../active-directory-b2c/identity-provider-microsoft-account.md) pour obtenir un exemple.  Pour modifier des flux d’utilisateurs, le rôle limité d’« administrateur de flux d’utilisateurs B2C » est nécessaire.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/identityProviders/allProperties/allTasks | Lire et configurer les fournisseurs d’identité dans Azure Active Directory B2C |

## <a name="global-administrator"></a>Administrateur général

Les utilisateurs avec ce rôle ont accès à toutes les fonctionnalités d’administration dans Azure Active Directory, ainsi qu’aux services qui utilisent des identités Azure Active Directory comme le portail Microsoft 365 Defender, le centre de conformité Microsoft 365, Exchange Online, SharePoint Online et Skype Entreprise Online. Par ailleurs, les administrateurs généraux peuvent [élever leur accès](../../role-based-access-control/elevate-access-global-admin.md) pour gérer tous les abonnements et groupes d’administration Azure. Ils obtiennent ainsi un accès complet à toutes les ressources Azure à l’aide du locataire Azure AD correspondant. La personne qui s’inscrit pour l’organisation Azure AD devient administrateur général. Une entreprise peut comprendre plusieurs administrateurs généraux. Les administrateurs généraux peuvent réinitialiser le mot de passe des utilisateurs et de tous les autres administrateurs.

> [!NOTE]
> À titre de meilleure pratique, Microsoft recommande d’attribuer le rôle Administrateur général à moins de cinq personnes dans votre organisation. Pour plus d’informations, consultez [Meilleures pratiques pour les rôles Azure AD](best-practices.md).

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/allTasks | Créer et supprimer les révisions d’accès, lire et actualiser toutes les propriétés des révisions d’accès ainsi que gérer les révisions d’accès des groupes dans Azure AD |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Créez et gérez des unités administratives (y compris les membres). |
> | microsoft.directory/applications/allProperties/allTasks | Créer et supprimer des applications, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/applications/synchronization/standard/read | Lire les paramètres de provisionnement associés à l’objet application |
> | microsoft.directory/applicationTemplates/instantiate | Instancier des applications de la galerie à partir de modèles d’application |
> | microsoft.directory/auditLogs/allProperties/read | Lire toutes les propriétés dans les journaux d’audit, y compris les propriétés privilégiées |
> | microsoft.directory/users/authenticationMethods/create | Créer des méthodes d’authentification pour les utilisateurs |
> | microsoft.directory/users/authenticationMethods/delete | Supprimer les méthodes d’authentification pour les utilisateurs |
> | microsoft.directory/users/authenticationMethods/standard/read | Lire les propriétés standard des méthodes d’authentification pour les utilisateurs |
> | microsoft.directory/users/authenticationMethods/basic/update | Mettre à jour les propriétés de base des méthodes d’authentification pour les utilisateurs |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | Gérez tous les aspects des stratégies d’autorisation |
> | microsoft.directory/bitlockerKeys/key/read | Lire les métadonnées et la clé BitLocker sur les appareils |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Créer et supprimer toutes les ressources, ainsi que lire et mettre à jour les propriétés standard dans Microsoft Cloud App Security |
> | microsoft.directory/connectors/create | Créer des connecteurs de proxy d’application |
> | microsoft.directory/connectors/allProperties/read | Lire toutes les propriétés des connecteurs de proxy d’application |
> | microsoft.directory/connectorGroups/create | Créer des groupes de connecteurs de proxy d’application |
> | microsoft.directory/connectorGroups/delete | Supprimer des groupes de connecteurs de proxy d’application |
> | microsoft.directory/connectorGroups/allProperties/read | Lire toutes les propriétés des groupes de connecteurs de proxy d’application |
> | microsoft.directory/connectorGroups/allProperties/update | Mettre à jour toutes les propriétés des groupes de connecteurs de proxy d’application |
> | microsoft.directory/contacts/allProperties/allTasks | Créer et supprimer des contacts, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/contracts/allProperties/allTasks | Créer et supprimer des contrats de partenaire, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/deletedItems/delete | Supprimer définitivement les objets qui ne peuvent plus être restaurés |
> | microsoft.directory/deletedItems/restore | Restaurer les objets supprimés de manière réversible à l’état d’origine |
> | microsoft.directory/devices/allProperties/allTasks | Créer et supprimer des appareils, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/deviceManagementPolicies/standard/read | Lire les propriétés standard sur les stratégies d’application de gestion des appareils |
> | microsoft.directory/deviceManagementPolicies/basic/update | Mettre à jour les propriétés de base des stratégies d’application de gestion des appareils |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Lire les propriétés standard sur les stratégies d’inscription des appareils |
> | microsoft.directory/deviceRegistrationPolicy/basic/update | Mettre à jour les propriétés de base des stratégies d’inscription d’appareil |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Créer et supprimer des rôles d’annuaire, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Créer et supprimer des définitions de rôle Azure AD, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/domains/allProperties/allTasks | Créer et supprimer des domaines, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Créer et supprimer des ressources, ainsi que lire et mettre à jour toutes les propriétés dans la gestion des droits d’utilisation Azure AD |
> | microsoft.directory/groups/allProperties/allTasks | Créer et supprimer des groupes, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/groupsAssignableToRoles/create | Créer des groupes pouvant se voir attribuer des rôles |
> | microsoft.directory/groupsAssignableToRoles/delete | Supprimer des groupes assignables à un rôle |
> | microsoft.directory/groupsAssignableToRoles/restore | Restaurer des groupes assignables à un rôle |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | Mettre à jour des groupes assignables à un rôle |
> | microsoft.directory/groupSettings/allProperties/allTasks | Créer et supprimer des paramètres de groupe, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/groupSettingTemplates/allProperties/allTasks | Créer et supprimer des modèles de paramètre de groupe, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/identityProtection/allProperties/allTasks | Créer et supprimer toutes les ressources, ainsi que lire et mettre à jour les propriétés standard dans Azure AD Identity Protection |
> | microsoft.directory/loginOrganizationBranding/allProperties/allTasks | Créer et supprimer loginTenantBranding, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Créer et supprimer des octrois d’autorisations OAuth 2.0, et lire et mettre à jour toutes les propriétés |
> | microsoft.directory/organization/allProperties/allTasks | Lire et actualiser toutes les propriétés d’une organisation |
> | microsoft.directory/policies/allProperties/allTasks | Créer et supprimer des stratégies, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/conditionalAccessPolicies/allProperties/allTasks | Mettre à jour toutes les propriétés de stratégies d’accès conditionnel |
> | microsoft.directory/crossTenantAccessPolicies/allProperties/allTasks | Gérer les stratégies d’accès entre locataires |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Lire toutes les ressources dans Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lire toutes les propriétés des journaux de provisionnement. |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Créer et supprimer des attributions de rôle, et lire et mettre à jour toutes les propriétés des attributions de rôle |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Créer et supprimer des définitions de rôle, et lire et mettre à jour toutes les propriétés |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Créer et supprimer des scopedRoleMemberships, et lire et mettre à jour toutes les propriétés |
> | microsoft.directory/serviceAction/activateService | Effectuer l’action « activer le service » pour un service |
> | microsoft.directory/serviceAction/disableDirectoryFeature | Effectuer l’action de service « désactiver la fonctionnalité d’annuaire » |
> | microsoft.directory/serviceAction/enableDirectoryFeature | Effectuer l’action de service « activer la fonctionnalité d’annuaire » |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | Effectuer l’action de service getAvailableExtentionProperties |
> | microsoft.directory/servicePrincipals/allProperties/allTasks | Créer et supprimer des principaux de service, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | Consentir à toute autorisation sur toute application |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Accorder à un principal de service l’accès direct aux données d’un groupe |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Lire les paramètres de provisionnement associés au principal de service |
> | microsoft.directory/signInReports/allProperties/read | Lire toutes les propriétés dans les rapports de connexion, y compris les propriétés privilégiées |
> | microsoft.directory/subscribedSkus/allProperties/allTasks | Acheter et gérer des abonnements, ainsi que supprimer des abonnements |
> | microsoft.directory/users/allProperties/allTasks | Créer et supprimer des utilisateurs, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/permissionGrantPolicies/create | Créer des stratégies d’octroi d’autorisation |
> | microsoft.directory/permissionGrantPolicies/delete | Supprimer les stratégies d’octroi d’autorisation |
> | microsoft.directory/permissionGrantPolicies/standard/read | Lire les propriétés standard des stratégies d’octroi d’autorisation |
> | microsoft.directory/permissionGrantPolicies/basic/update | Mettre à jour les propriétés de base des stratégies d’octroi d’autorisation |
> | microsoft.directory/servicePrincipalCreationPolicies/create | Créer des stratégies de création de principal de service |
> | microsoft.directory/servicePrincipalCreationPolicies/delete | Supprimer des stratégies de création de principal de service |
> | microsoft.directory/servicePrincipalCreationPolicies/standard/read | Lire les propriétés standard de stratégies de création de principal de service |
> | microsoft.directory/servicePrincipalCreationPolicies/basic/update | Mettre à jour les propriétés de base de stratégies de création de principal de service |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/allProperties/read | Lire une carte de justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/revoke | Révoquer une carte de justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/contracts/create | Créer un contrat de justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/read | Lire un contrat de justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/update | Mettre à jour un contrat de justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/create | Créer la configuration requise pour créer et gérer des justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/delete | Supprimer la configuration requise pour créer et gérer des justificatifs vérifiables et supprimer tous ses justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/read | Lire la configuration requise pour créer et gérer des justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/update | Mettre à jour la configuration requise pour créer et gérer des justificatifs vérifiables |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Gérer tous les aspects d’Azure Advanced Threat Protection |
> | microsoft.azure.informationProtection/allEntities/allTasks | Gérer tous les aspects d’Azure Information Protection |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.cloudPC/allEntities/allProperties/allTasks | Gérer tous les aspects de Windows 365 |
> | microsoft.commerce.billing/allEntities/allTasks | Gérer tous les aspects de la facturation Office 365 |
> | microsoft.dynamics365/allEntities/allTasks | Gérez tous les aspects de Dynamics 365 |
> | microsoft.edge/allEntities/allProperties/allTasks | Gérer tous les aspects de Microsoft Edge |
> | microsoft.flow/allEntities/allTasks | Gérez tous les aspects de Microsoft Power Automate |
> | microsoft.intune/allEntities/allTasks | Gérez tous les aspects de Microsoft Intune |
> | microsoft.office365.complianceManager/allEntities/allTasks | Gérez tous les aspects d’Office 365 Compliance Manager |
> | microsoft.office365.desktopAnalytics/allEntities/allTasks | Gérer tous les aspects de Desktop Analytics |
> | microsoft.office365.exchange/allEntities/basic/allTasks | Gérer tous les aspects d’Exchange Online |
> | microsoft.office365.knowledge/contentUnderstanding/allProperties/allTasks | Lire et mettre jour toutes les propriétés de la compréhension du contenu dans le centre d’administration Microsoft 365 |
> | microsoft.office365.knowledge/contentUnderstanding/analytics/allProperties/read | Lire les rapports analytiques de la compréhension du contenu dans le Centre d'administration Microsoft 365 |
> | microsoft.office365.knowledge/knowledgeNetwork/allProperties/allTasks | Lire et mettre jour toutes les propriétés du réseau de connaissances dans le centre d’administration Microsoft 365 |
> | microsoft.office365.knowledge/knowledgeNetwork/topicVisibility/allProperties/allTasks | Gérer la visibilité des rubriques du réseau de connaissances dans le Centre d'administration Microsoft 365 |
> | microsoft.office365.knowledge/learningSources/allProperties/allTasks | Gérer les sources d'apprentissage et toutes leurs propriétés dans l'application d'apprentissage |
> | microsoft.office365.lockbox/allEntities/allTasks | Gérer tous les aspects de Customer Lockbox |
> | microsoft.office365.messageCenter/messages/read | Lire les messages dans le Centre de messages du Centre d’administration Microsoft 365, à l’exclusion des messages de sécurité |
> | microsoft.office365.messageCenter/securityMessages/read | Lire les messages de sécurité dans le Centre de messages du Centre d’administration Microsoft 365 |
> | microsoft.office365.network/performance/allProperties/read | Lire toutes les propriétés de performances réseau dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.protectionCenter/allEntities/allProperties/allTasks | Gérer tous les aspects des centres de sécurité et de conformité |
> | microsoft.office365.search/content/manage | Créer et supprimer du contenu, ainsi que lire et mettre à jour toutes les propriétés dans Recherche Microsoft |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Créer et supprimer toutes les ressources, ainsi que lire et mettre à jour les propriétés standard dans le Centre de sécurité et conformité Office 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.sharePoint/allEntities/allTasks | Créer et supprimer toutes les ressources, ainsi que lire et mettre à jour les propriétés standard SharePoint |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gérez tous les aspects de Skype Entreprise Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lire les rapports d’utilisation d’Office 365 |
> | microsoft.office365.userCommunication/allEntities/allTasks | Lire et mettre à jour la visibilité des messages Nouveautés |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |
> | microsoft.powerApps/allEntities/allTasks | Gérez tous les aspects de Power Apps |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Gérez tous les aspects de Power BI |
> | microsoft.teams/allEntities/allProperties/allTasks | Gérez toutes les ressources dans Teams |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | Gérer tous les aspects de Microsoft Defender pour point de terminaison |
> | microsoft.windows.updatesDeployments/allEntities/allProperties/allTasks | Lire et configurer tous les aspects du Service Windows Update |

## <a name="global-reader"></a>Lecteur général

Les utilisateurs affectés à ce rôle peuvent lire les paramètres et les informations d’administration entre les services Microsoft 365, mais ne peuvent pas entreprendre d’actions de gestion. Le lecteur général est l’équivalent en lecture seule de l’Administrateur général. Affecter un rôle de Lecteur général plutôt que d’Administrateur général pour la planification, les audits ou les investigations. Utiliser le Lecteur global conjointement à d’autres rôles d’administrateur limités comme celui de l’Administrateur Exchange pour faciliter le travail sans affecter le rôle d’Administrateur général. Le Lecteur général fonctionne avec le centre d’administration Microsoft 365, le centre d’administration Exchange, le centre d’administration SharePoint, le centre d’administration Teams, le centre de Sécurité, le centre de Conformité, le centre d’administration Azure AD et le centre d’administration de Gestion des appareils.

> [!NOTE]
> Le rôle de Lecteur général comporte peu de restrictions pour le moment -
>
>- [Centre d’administration OneDrive](https://admin.onedrive.com/) : le Centre d’administration OneDrive ne prend pas en charge le rôle de Lecteur général
>- [Centre d'administration Microsoft 365](https://admin.microsoft.com/Adminportal/Home#/homepage) : le Lecteur général ne peut pas lire les applications intégrées. Vous ne trouverez pas l'onglet **Applications intégrées** sous **Paramètres** dans le volet gauche du Centre d'administration Microsoft 365.
>- [Centre de Sécurité et de Conformité Office](https://sip.protection.office.com/homepage) : le Lecteur général ne peut pas lire les journaux d'audit SCC, effectuer de recherche de contenu ou visualiser le Degré de sécurisation.
>- [Centre d’administration teams](https://admin.teams.microsoft.com) : Le Lecteur général ne peut pas lire le **cycle de vie Teams**, les **Analyses et rapports**, la **gestion des appareils téléphoniques IP** et le **catalogue d’applications**.
>- La [Gestion de l’accès privilégié (PAM) ](/office365/securitycompliance/privileged-access-management-overview) ne prend pas en charge le rôle de Lecteur général.
>- [Protection des informations Azure](/azure/information-protection/what-is-information-protection) : le Lecteur général est pris en charge uniquement pour la [création centralisée de rapports](/azure/information-protection/reports-aip) et lorsque votre organisation Azure AD n’est pas sur la [plateforme d’étiquetage unifiée](/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform).
> - [SharePoint](https://admin.microsoft.com/sharepoint) : le Lecteur général ne peut actuellement pas accéder à SharePoint à l’aide de PowerShell.
>
> Ces fonctionnalités sont actuellement en cours de développement.
>

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/read | Lire toutes les propriétés des révisions d’accès |
> | microsoft.directory/administrativeUnits/allProperties/read | Lire toutes les propriétés des unités administratives |
> | microsoft.directory/applications/allProperties/read | Lire toutes les propriétés (y compris les propriétés privilégiées) sur tous les types d’applications |
> | microsoft.directory/applications/synchronization/standard/read | Lire les paramètres de provisionnement associés à l’objet application |
> | microsoft.directory/auditLogs/allProperties/read | Lire toutes les propriétés dans les journaux d’audit, y compris les propriétés privilégiées |
> | microsoft.directory/users/authenticationMethods/standard/restrictedRead | Lire les propriétés standard des méthodes d’authentification qui n’incluent pas d’informations d’identification personnelle d’utilisateurs |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.directory/bitlockerKeys/key/read | Lire les métadonnées et la clé BitLocker sur les appareils |
> | microsoft.directory/cloudAppSecurity/allProperties/read | Lire toutes les propriétés de la sécurité d’application cloud |
> | microsoft.directory/connectors/allProperties/read | Lire toutes les propriétés des connecteurs de proxy d’application |
> | microsoft.directory/connectorGroups/allProperties/read | Lire toutes les propriétés des groupes de connecteurs de proxy d’application |
> | microsoft.directory/contacts/allProperties/read | Lire toutes les propriétés des contacts |
> | microsoft.directory/devices/allProperties/read | Lire toutes les propriétés des appareils |
> | microsoft.directory/directoryRoles/allProperties/read | Lire toutes les propriétés des rôles Azure AD |
> | microsoft.directory/directoryRoleTemplates/allProperties/read | Lire toutes les propriétés des modèles de rôle |
> | microsoft.directory/domains/allProperties/read | Lire toutes les propriétés des domaines |
> | microsoft.directory/entitlementManagement/allProperties/read | Lire toutes les propriétés dans la fonctionnalité de gestion des droits d’utilisation Azure AD |
> | microsoft.directory/groups/allProperties/read | Lire toutes les propriétés (notamment les propriétés privilégiées) des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groupSettings/allProperties/read | Lire toutes les propriétés des paramètres de groupe |
> | microsoft.directory/groupSettingTemplates/allProperties/read | Lire toutes les propriétés des modèles de paramètre de groupe |
> | microsoft.directory/identityProtection/allProperties/read | Lire toutes les ressources dans Azure AD Identity Protection |
> | microsoft.directory/loginOrganizationBranding/allProperties/read | Lire toutes les propriétés de la page de connexion de votre organisation |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/read | Lire toutes les propriétés des octrois d'autorisation OAuth 2.0 |
> | microsoft.directory/organization/allProperties/read | Lire toutes les propriétés d’une organisation |
> | microsoft.directory/permissionGrantPolicies/standard/read | Lire les propriétés standard des stratégies d’octroi d’autorisation |
> | microsoft.directory/policies/allProperties/read | Lire toutes les propriétés des stratégies |
> | microsoft.directory/conditionalAccessPolicies/allProperties/read | Lire toutes les propriétés des stratégies d’accès conditionnel |
> | microsoft.directory/crossTenantAccessPolicies/allProperties/read | Lire toutes les propriétés des stratégies inter-client |
> | microsoft.directory/deviceManagementPolicies/standard/read | Lire les propriétés standard sur les stratégies d’application de gestion des appareils |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Lire les propriétés standard sur les stratégies d’inscription des appareils |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Lire toutes les ressources dans Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lire toutes les propriétés des journaux de provisionnement. |
> | microsoft.directory/roleAssignments/allProperties/read | Lire toutes les propriétés des attributions de rôle |
> | microsoft.directory/roleDefinitions/allProperties/read | Lire toutes les propriétés des définitions de rôle |
> | microsoft.directory/scopedRoleMemberships/allProperties/read | Afficher les membres des unités administratives |
> | microsoft.directory/serviceAction/getAvailableExtentionProperties | Effectuer l’action de service getAvailableExtentionProperties |
> | microsoft.directory/servicePrincipals/allProperties/read | Lire toutes les propriétés (y compris les propriétés privilégiées) sur servicePrincipals |
> | microsoft.directory/servicePrincipalCreationPolicies/standard/read | Lire les propriétés standard de stratégies de création de principal de service |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Lire les paramètres de provisionnement associés au principal de service |
> | microsoft.directory/signInReports/allProperties/read | Lire toutes les propriétés dans les rapports de connexion, y compris les propriétés privilégiées |
> | microsoft.directory/subscribedSkus/allProperties/read | Lire toutes les propriétés des abonnements produits |
> | microsoft.directory/users/allProperties/read | Lire toutes les propriétés des utilisateurs |
> | microsoft.directory/verifiableCredentials/configuration/contracts/cards/allProperties/read | Lire une carte de justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/contracts/allProperties/read | Lire un contrat de justificatifs vérifiables |
> | microsoft.directory/verifiableCredentials/configuration/allProperties/read | Lire la configuration requise pour créer et gérer des justificatifs vérifiables |
> | microsoft.cloudPC/allEntities/allProperties/read | Lire tous les aspects de Windows 365 |
> | microsoft.commerce.billing/allEntities/read | Lire toutes les ressources de la facturation d’Office 365 |
> | microsoft.edge/allEntities/allProperties/read | Lire tous les aspects de Microsoft Edge |
> | microsoft.office365.exchange/allEntities/standard/read | Lire toutes les ressources d’Exchange Online |
> | microsoft.office365.messageCenter/messages/read | Lire les messages dans le Centre de messages du Centre d’administration Microsoft 365, à l’exclusion des messages de sécurité |
> | microsoft.office365.messageCenter/securityMessages/read | Lire les messages de sécurité dans le Centre de messages du Centre d’administration Microsoft 365 |
> | microsoft.office365.network/performance/allProperties/read | Lire toutes les propriétés de performances réseau dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.protectionCenter/allEntities/allProperties/read | Lire toutes les propriétés dans les centres de sécurité et de conformité |
> | microsoft.office365.securityComplianceCenter/allEntities/read | Lire les propriétés standard dans le Centre de conformité et de sécurité Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lire les rapports d’utilisation d’Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |
> | microsoft.teams/allEntities/allProperties/read | Lire tous les aspects de Microsoft Teams |
> | microsoft.windows.updatesDeployments/allEntities/allProperties/read | Lire tous les aspects du Service Windows Update |

## <a name="groups-administrator"></a>Administrateur de groupes

Les utilisateurs de ce rôle peuvent créer/gérer des groupes et ses paramètres, comme les stratégies d’attribution de noms et d’expiration. Il est important de comprendre que l’affectation d’un utilisateur à ce rôle lui donne la possibilité de gérer tous les groupes au sein de l’organisation dans diverses charges de travail telles que Teams, SharePoint et Yammer en plus d’Outlook. De même, l’utilisateur pourra gérer les différents paramètres des groupes à travers divers portails d’administration, comme le centre d’administration Microsoft, le portail Azure, ainsi que des charges de travail spécifiques telles que les centres d’administration Teams et SharePoint.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/deletedItems.groups/delete | Supprimer définitivement les groupes qui ne peuvent plus être restaurés |
> | microsoft.directory/deletedItems.groups/restore | Restaurer les groupes supprimés de manière réversible à l’état d’origine |
> | microsoft.directory/groups/assignLicense | Attribuer des licences de produits à des groupes pour la gestion des licences basée sur un groupe |
> | microsoft.directory/groups/create | Créer des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/delete | Supprimer des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/hiddenMembers/read | Lire la propriété membres masqués des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groups/reprocessLicenseAssignment | Retraiter les attributions de licence pour la gestion des licences basée sur un groupe |
> | microsoft.directory/groups/restore | Restauration de groupes supprimés |
> | microsoft.directory/groups/basic/update | Actualiser les propriétés de base des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/classification/update | Actualiser les propriétés de classification des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/dynamicMembershipRule/update | Actualiser la règle d’adhésion dynamique des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/groupType/update | Actualiser les propriétés qui affectent le type de groupe des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/members/update | Actualiser la propriété membres des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/onPremWriteBack/update | Mettre à jour les groupes Azure Active Directory pour être réécrits en local avec Azure AD Connect |
> | microsoft.directory/groups/owners/update | Actualiser la propriété propriétaires des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/settings/update | Mettre à jour les paramètres des groupes |
> | microsoft.directory/groups/visibility/update | Actualiser la propriété de visibilité des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Accorder à un principal de service l’accès direct aux données d’un groupe |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="guest-inviter"></a>Inviteur

les utilisateurs auxquels ce rôle est assigné peuvent gérer les invitations d’utilisateurs invités Azure Active Directory B2B lorsque le paramètre utilisateur **Les membres peuvent inviter** est défini sur Non. Pour plus d’informations sur B2B Collaboration, consultez la page [À propos d’Azure AD B2B Collaboration](../external-identities/what-is-b2b.md). Il n’inclut pas d’autres autorisations.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/users/inviteGuest | Inviter des utilisateurs |
> | microsoft.directory/users/standard/read | Lire les propriétés de base sur les utilisateurs |
> | microsoft.directory/users/appRoleAssignments/read | Lire les attributions de rôle d’application des utilisateurs |
> | microsoft.directory/users/deviceForResourceAccount/read | Lire deviceForResourceAccount des utilisateurs |
> | microsoft.directory/users/directReports/read | Lire les collaborateurs directs des utilisateurs |
> | microsoft.directory/users/licenseDetails/read | Lire les détails de licence des utilisateurs |
> | microsoft.directory/users/manager/read | Lire les gestionnaires d’utilisateurs |
> | microsoft.directory/users/memberOf/read | Lire les appartenances aux groupes des utilisateurs |
> | microsoft.directory/users/oAuth2PermissionGrants/read | Lire les octrois d’autorisations déléguées sur les utilisateurs |
> | microsoft.directory/users/ownedDevices/read | Lire les appareils détenus des utilisateurs |
> | microsoft.directory/users/ownedObjects/read | Lire les objets détenus des utilisateurs |
> | microsoft.directory/users/photo/read | Lire la photo des utilisateurs |
> | microsoft.directory/users/registeredDevices/read | Lire les appareils inscrits des utilisateurs |
> | microsoft.directory/users/scopedRoleMemberOf/read | Lire l’appartenance d’un utilisateur à un rôle Azure AD étendu à une unité administrative |

## <a name="helpdesk-administrator"></a>Administrateur du support technique

Les utilisateurs avec ce rôle peuvent changer des mots de passe, rendre non valides les jetons d’actualisation, gérer les demandes de service et superviser l’intégrité du service. L’invalidation d’un jeton d’actualisation oblige l’utilisateur à se reconnecter. Le rôle attribué à un utilisateur détermine si un administrateur du support technique peut réinitialiser le mot de passe de l’utilisateur et invalider les jetons d’actualisation. Pour obtenir la liste des rôles pour lesquels un administrateur du support technique peut réinitialiser les mots de passe et invalider les jetons d’actualisation, consultez [Autorisations de réinitialisation de mot de passe](#password-reset-permissions).

> [!IMPORTANT]
> Les utilisateurs auxquels ce rôle est assigné peuvent changer les mots de passe des personnes susceptibles d’avoir accès à des informations sensibles ou privées ou à des configurations critiques à l’intérieur et à l’extérieur d’Azure Active Directory. Changer le mot de passe d’un utilisateur peut signifier la capacité d’assumer l’identité et les autorisations de cet utilisateur. Par exemple :
>
>- Les propriétaires d’inscription d’application et d’application d’entreprise, qui peuvent gérer les informations d’identification des applications qu’ils possèdent. Ces applications peuvent disposer d’autorisations privilégiées dans Azure AD et ailleurs qui ne sont pas accordées aux administrateurs du support technique. Grâce à ce chemin d’accès, un administrateur de support technique peut être en mesure d’assumer l’identité d’un propriétaire d’application, puis d’assumer l’identité d’une application privilégiée en mettant à jour les identifiants de celle-ci.
>- Propriétaires d’abonnement Azure, qui peuvent avoir accès à des informations confidentielles ou privées ou à une configuration critique dans Azure.
>- Propriétaires de groupe de sécurité et de groupe Microsoft 365, qui peuvent gérer l’appartenance à un groupe. Ces groupes peuvent accorder l’accès à des informations sensibles ou privées ou à une configuration critique dans Azure AD et ailleurs.
>- Administrateurs d’autres services en dehors d’Azure AD, tels qu’Exchange Online, le Centre de sécurité et de conformité Office et les systèmes de ressources humaines.
>- Les non-administrateurs comme les cadres supérieurs, les conseillers juridiques et les employés des ressources humaines qui peuvent avoir accès à des informations sensibles ou privées.

La délégation d’autorisations administratives à des sous-ensembles d’utilisateurs et l’application de stratégies à un sous-ensemble d’utilisateurs sont possibles avec des [unités administratives](administrative-units.md).

Ce rôle était auparavant appelé « administrateur de mot de passe » dans le [portail Azure](https://portal.azure.com/). Le nom « Administrateur du support » dans Azure AD correspond maintenant à celui utilisé dans Azure AD PowerShell et l’API Microsoft Graph.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | Lire les métadonnées et la clé BitLocker sur les appareils |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forcer la déconnexion en invalidant les jetons d’actualisation de l’utilisateur |
> | microsoft.directory/users/password/update | Réinitialiser les mots de passe de tous les utilisateurs |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="hybrid-identity-administrator"></a>Administrateur d’identité hybride

Les utilisateurs de ce rôle peuvent créer, gérer et déployer la configuration de l’approvisionnement d’AD vers Azure AD via le cloud, ainsi que gérer Azure AD Connect et les paramètres de fédération. Les utilisateurs peuvent également dépanner et superviser les journaux à l’aide de ce rôle.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/applications/create | Créer tous les types d’applications |
> | microsoft.directory/applications/delete | Supprimer tous les types d’applications |
> | microsoft.directory/applications/appRoles/update | Mettre à jour la propriété appRoles sur tous les types d’applications |
> | microsoft.directory/applications/audience/update | Mettre à jour la propriété audience pour les applications |
> | microsoft.directory/applications/authentication/update | Mettre à jour l’authentification sur tous les types d’applications |
> | microsoft.directory/applications/basic/update | Mettre à jour les propriétés de base pour les applications |
> | microsoft.directory/applications/notes/update | Mettre à jour les notes d’applications |
> | microsoft.directory/applications/owners/update | Mettre à jour les propriétaires d’applications |
> | microsoft.directory/applications/permissions/update | Mettre à jour les autorisations exposées et les autorisations requises sur tous les types d’applications |
> | microsoft.directory/applications/policies/update | Mettre à jour les stratégies d’applications |
> | microsoft.directory/applications/tag/update | Mettre à jour les balises d’applications |
> | microsoft.directory/applications/synchronization/standard/read | Lire les paramètres de provisionnement associés à l’objet application |
> | microsoft.directory/applicationTemplates/instantiate | Instancier des applications de la galerie à partir de modèles d’application |
> | microsoft.directory/auditLogs/allProperties/read | Lire toutes les propriétés dans les journaux d’audit, y compris les propriétés privilégiées |
> | microsoft.directory/cloudProvisioning/allProperties/allTasks | Lisez et configurez toutes les propriétés du service d’approvisionnement cloud Azure AD. |
> | microsoft.directory/deletedItems.applications/delete | Supprimer définitivement les applications qui ne peuvent plus être restaurées |
> | microsoft.directory/deletedItems.applications/restore | Restaurer les applications supprimées de manière réversible à l’état d’origine |
> | microsoft.directory/domains/allProperties/read | Lire toutes les propriétés des domaines |
> | microsoft.directory/domains/federation/update | Mettre à jour la propriété de fédération des domaines |
> | microsoft.directory/organization/dirSync/update | Mettre à jour la propriété de synchronisation d’annuaire d’organisation |
> | microsoft.directory/provisioningLogs/allProperties/read | Lire toutes les propriétés des journaux de provisionnement. |
> | microsoft.directory/servicePrincipals/create | Créer des principaux de service |
> | microsoft.directory/servicePrincipals/delete | Supprimer des principaux de service |
> | microsoft.directory/servicePrincipals/disable | Désactiver des principaux de service |
> | microsoft.directory/servicePrincipals/enable | Activer des principaux de service |
> | microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Gérer les secrets et les informations d’identification de l’approvisionnement des applications |
> | microsoft.directory/servicePrincipals/synchronizationJobs/manage | Démarrer, redémarrer et suspendre les travaux de synchronisation d’approvisionnement des applications |
> | microsoft.directory/servicePrincipals/synchronizationSchema/manage | Créer et gérer le schéma et les tâches de synchronisation de l’approvisionnement des applications |
> | microsoft.directory/servicePrincipals/audience/update | Mettre à jour les propriétés d’audience sur les principaux de service |
> | microsoft.directory/servicePrincipals/authentication/update | Mettre à jour les propriétés d’authentification sur les principaux de service |
> | microsoft.directory/servicePrincipals/basic/update | Mettre à jour les propriétés de base sur les principaux de service |
> | microsoft.directory/servicePrincipals/notes/update | Mettre à jour les notes de principaux de service |
> | microsoft.directory/servicePrincipals/owners/update | Mettre à jour les propriétaires de principaux de service |
> | microsoft.directory/servicePrincipals/permissions/update | Mettre à jour les autorisations des principaux de service |
> | microsoft.directory/servicePrincipals/policies/update | Mettre à jour les stratégies des principaux de service |
> | microsoft.directory/servicePrincipals/tag/update | Mettre à jour la propriété Tag pour les principaux de service |
> | microsoft.directory/servicePrincipals/synchronization/standard/read | Lire les paramètres de provisionnement associés au principal de service |
> | microsoft.directory/signInReports/allProperties/read | Lire toutes les propriétés dans les rapports de connexion, y compris les propriétés privilégiées |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.messageCenter/messages/read | Lire les messages dans le Centre de messages du Centre d’administration Microsoft 365, à l’exclusion des messages de sécurité |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="identity-governance-administrator"></a>Administrateur Identity Governance

Les utilisateurs dotés de ce rôle peuvent gérer la configuration de la gouvernance des identités Azure AD, notamment les packages d'accès, les révisions d'accès, les catalogues et les stratégies, afin que l'accès soit approuvé et révisé et que les utilisateurs invités qui n'ont plus besoin de l'accès soient supprimés.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/accessReviews/allProperties/allTasks | Créer et supprimer les révisions d’accès, lire et actualiser toutes les propriétés des révisions d’accès ainsi que gérer les révisions d’accès des groupes dans Azure AD |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Créer et supprimer des ressources, ainsi que lire et mettre à jour toutes les propriétés dans la gestion des droits d’utilisation Azure AD |
> | microsoft.directory/groups/members/update | Actualiser la propriété membres des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Mettre à jour les attributions de rôles des principaux de service |

## <a name="insights-administrator"></a>Administrateur Insights

Les utilisateurs ayant ce rôle peuvent accéder à l’ensemble des fonctionnalités d’administration de l’[application Microsoft 365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Ce rôle permet de lire les informations de répertoire, de contrôler l’intégrité du service, de soumettre des tickets de support et d’accéder aux aspects des paramètres Administrateur Insights.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.insights/allEntities/allTasks | Gérer tous les aspects de l’application Insights |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="insights-business-leader"></a>Leader d’entreprise Insights

Les utilisateurs ayant ce rôle peuvent accéder à un ensemble de tableaux de bord et d’insights par le biais de l’[application Microsoft 365 Insights](https://go.microsoft.com/fwlink/?linkid=2129521). Ceci inclut l’accès total à tous les tableaux de bord et aux insights présentés et la fonctionnalité d’exploration de données. Les utilisateurs ayant ce rôle n’ont pas accès aux paramètres de configuration du produit, lesquels sont de la responsabilité réservée au rôle Administrateur Insights.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.insights/reports/read | Visualiser les rapports et le tableau de bord dans l’application Insights |
> | microsoft.insights/programs/update | Déployer et gérer des programmes dans l’application Insights |

## <a name="intune-administrator"></a>Administrateur Intune

Les utilisateurs avec ce rôle ont des autorisations générales dans Microsoft Intune Online, quand le service est présent. Ce rôle donne aussi la possibilité de gérer les utilisateurs et les appareils afin d’associer la stratégie, ainsi que de créer et de gérer des groupes. Pour plus d’informations, consultez la page [Contrôle d’accès en fonction du rôle (RBAC) avec Microsoft Intune](/intune/role-based-access-control).

Ce rôle peut créer et gérer tous les groupes de sécurité. Cependant, l’Administrateur Intune ne dispose pas de droits d’administrateur sur les groupes Office. Cela signifie que l’administrateur ne peut pas mettre à jour les propriétaires ou les membres de tous les groupes Office au sein de l’organisation. Cependant, il peut gérer le groupe Office qu’il crée, ce qui fait partie de ses privilèges d’utilisateur final. Ainsi, tout groupe Office (et non un groupe de sécurité) qu’il crée doit être comptabilisé au titre de son quota de 250.

> [!NOTE]
> Dans l’API Microsoft Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de service Intune ». Il est « Administrateur Intune » dans le [portail Azure](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/bitlockerKeys/key/read | Lire les métadonnées et la clé BitLocker sur les appareils |
> | microsoft.directory/contacts/create | Créer des contacts |
> | microsoft.directory/contacts/delete | Supprimer des contacts |
> | microsoft.directory/contacts/basic/update | Mettre à jour les propriétés de base sur les contacts |
> | microsoft.directory/devices/create | Créer des appareils (inscrire dans Azure AD) |
> | microsoft.directory/devices/delete | Supprimer des appareils d’Azure AD |
> | microsoft.directory/devices/disable | Désactiver des appareils dans Azure AD |
> | microsoft.directory/devices/enable | Activer des appareils dans Azure AD |
> | microsoft.directory/devices/basic/update | Mettre à jour les propriétés de base sur les appareils |
> | microsoft.directory/devices/extensionAttributeSet1/update | Actualiser les propriétés extensionAttribute1 en extensionAttribute5 sur les appareils |
> | microsoft.directory/devices/extensionAttributeSet2/update | Actualiser les propriétés extensionAttribute6 en extensionAttribute10 sur les appareils |
> | microsoft.directory/devices/extensionAttributeSet3/update | Actualiser les propriétés extensionAttribute11 en extensionAttribute15 sur les appareils |
> | microsoft.directory/devices/registeredOwners/update | Mettre à jour les propriétaires inscrits d’appareils |
> | microsoft.directory/devices/registeredUsers/update | Mettre à jour les utilisateurs inscrits d’appareils |
> | microsoft.directory/deviceManagementPolicies/standard/read | Lire les propriétés standard sur les stratégies d’application de gestion des appareils |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Lire les propriétés standard sur les stratégies d’inscription des appareils |
> | microsoft.directory/groups/hiddenMembers/read | Lire la propriété membres masqués des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groups.security/create | Créer des groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/delete | Supprimer des groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/basic/update | Actualiser les propriétés de base de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/classification/update | Actualiser la propriété de classification de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | Mettre à jour la règle d'adhésion dynamique des groupes de sécurité, à l'exclusion des groupes attribuables à un rôle |
> | microsoft.directory/groups.security/members/update | Actualiser les membres de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/owners/update | Actualiser les propriétaires de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/visibility/update | Actualiser la propriété de visibilité de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/users/basic/update | Mettre à jour les propriétés de base sur les utilisateurs |
> | microsoft.directory/users/manager/update | Mettre à jour le gestionnaire pour les utilisateurs |
> | microsoft.directory/users/photo/update | Actualiser la photo des utilisateurs |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.cloudPC/allEntities/allProperties/allTasks | Gérer tous les aspects de Windows 365 |
> | microsoft.intune/allEntities/allTasks | Gérez tous les aspects de Microsoft Intune |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="kaizala-administrator"></a>Administrateur Kaizala

Les utilisateurs dotés de ce rôle ont des autorisations générales de gérer des paramètres au sein de Microsoft Kaizala, quand le service est présent, et ils peuvent gérer les tickets de support et superviser l’intégrité du service. De plus, l’utilisateur peut accéder à des rapports liés à l’adoption et à l’utilisation de Kaizala par les membres de l’organisation, ainsi qu’à des rapports d’entreprise générés avec les actions Kaizala.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="knowledge-administrator"></a>Administrateur des connaissances

Les utilisateurs dotés de ce rôle disposent d’un accès total à tous les paramètres de connaissances, d’apprentissage et de fonctionnalités intelligentes disponibles dans le centre d’administration Microsoft 365. Ils ont une compréhension générale de la suite de produits et des détails des licences et ont la responsabilité de contrôler l’accès. L’Administrateur des connaissances peut créer et gérer du contenu, comme des rubriques, des acronymes et des ressources d’apprentissage. De plus, ces utilisateurs peuvent créer des centres de contenu, superviser l’intégrité du service et créer des demandes de service.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/groups.security/create | Créer des groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/createAsOwner | Créer des groupes de Sécurité, à l’exclusion des groupes à attribution de rôle Le créateur est ajouté en tant que premier propriétaire. |
> | microsoft.directory/groups.security/delete | Supprimer des groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/basic/update | Actualiser les propriétés de base de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/members/update | Actualiser les membres de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/owners/update | Actualiser les propriétaires de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.office365.knowledge/contentUnderstanding/allProperties/allTasks | Lire et mettre jour toutes les propriétés de la compréhension du contenu dans le centre d’administration Microsoft 365 |
> | microsoft.office365.knowledge/knowledgeNetwork/allProperties/allTasks | Lire et mettre jour toutes les propriétés du réseau de connaissances dans le centre d’administration Microsoft 365 |
> | microsoft.office365.knowledge/learningSources/allProperties/allTasks | Gérer les sources d'apprentissage et toutes leurs propriétés dans l'application d'apprentissage |
> | microsoft.office365.protectionCenter/sensitivityLabels/allProperties/read | Lire toutes les propriétés des étiquettes de sensibilité dans le Centre de sécurité et conformité |
> | microsoft.office365.sharePoint/allEntities/allTasks | Créer et supprimer toutes les ressources, ainsi que lire et mettre à jour les propriétés standard SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="knowledge-manager"></a>Gestionnaire des connaissances

Les utilisateurs qui disposent de ce rôle peuvent créer et gérer du contenu, comme des rubriques, des acronymes et du contenu d'apprentissage. Ces utilisateurs sont principalement responsables de la qualité et de la structure des connaissances. Cet utilisateur dispose de tous les droits sur les actions de gestion des rubriques pour confirmer une rubrique, approuver des modifications ou supprimer une rubrique. Ce rôle peut également gérer les taxonomies dans le cadre de l'outil de gestion du magasin de termes et créer des centres de contenu.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/groups.security/create | Créer des groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/createAsOwner | Créer des groupes de Sécurité, à l’exclusion des groupes à attribution de rôle Le créateur est ajouté en tant que premier propriétaire. |
> | microsoft.directory/groups.security/delete | Supprimer des groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/basic/update | Actualiser les propriétés de base de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/members/update | Actualiser les membres de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/owners/update | Actualiser les propriétaires de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.office365.knowledge/contentUnderstanding/analytics/allProperties/read | Lire les rapports analytiques de la compréhension du contenu dans le Centre d'administration Microsoft 365 |
> | microsoft.office365.knowledge/knowledgeNetwork/topicVisibility/allProperties/allTasks | Gérer la visibilité des rubriques du réseau de connaissances dans le Centre d'administration Microsoft 365 |
> | microsoft.office365.sharePoint/allEntities/allTasks | Créer et supprimer toutes les ressources, ainsi que lire et mettre à jour les propriétés standard SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="license-administrator"></a>Administrateur de licence

Les utilisateurs dans ce rôle peuvent ajouter, supprimer et mettre à jour des attributions de licence pour les utilisateurs, les groupes (à l’aide de la gestion des licences par groupe) et gérer l’emplacement d’utilisation pour les utilisateurs. Le rôle ne permet pas d’acheter ou de gérer des abonnements, de créer ou de gérer des groupes, ni de créer ou de gérer les utilisateurs au-delà de leur emplacement d’utilisation. Ce rôle n’a pas d'accès pour afficher, créer ou gérer des tickets de support.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.directory/groups/assignLicense | Attribuer des licences de produits à des groupes pour la gestion des licences basée sur un groupe |
> | microsoft.directory/groups/reprocessLicenseAssignment | Retraiter les attributions de licence pour la gestion des licences basée sur un groupe |
> | microsoft.directory/users/assignLicense | Gérer les licences utilisateur |
> | microsoft.directory/users/reprocessLicenseAssignment | Retraiter les attributions de licence pour les utilisateurs |
> | microsoft.directory/users/usageLocation/update | Mettre à jour l’emplacement d’utilisation des utilisateurs |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="message-center-privacy-reader"></a>Lecteur de confidentialité du Centre de messages

Les utilisateurs dotés de ce rôle peuvent superviser toutes les notifications dans le Centre de messages, notamment les messages de confidentialité des données. Les lecteurs de confidentialité du Centre de messages reçoivent des notifications par e-mail, notamment celles liées à la confidentialité des données, et ils peuvent se désabonner à l’aide des préférences du Centre de messages. Seuls l’administrateur général et le lecteur de confidentialité du Centre de messages peuvent lire les messages de confidentialité des données. De plus, ce rôle permet de voir les groupes, les domaines et les abonnements. Il n’est pas autorisé à voir, créer ou gérer des demandes de service.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Lire les messages dans le Centre de messages du Centre d’administration Microsoft 365, à l’exclusion des messages de sécurité |
> | microsoft.office365.messageCenter/securityMessages/read | Lire les messages de sécurité dans le Centre de messages du Centre d’administration Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="message-center-reader"></a>Lecteur du Centre de messages

Les utilisateurs titulaires de ce rôle peuvent superviser les notifications et les mises à jour d’avis d’intégrité dans le [Centre de messages](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) pour leur organisation sur les services configurés comme Exchange, Intune et Microsoft Teams. Les lecteurs du Centre de messages reçoivent par e-mail des résumés hebdomadaires des publications, des mises à jour, et peuvent partager les messages publiés sur le Centre de messages dans Microsoft 365. Dans Azure AD, les utilisateurs affectés à ce rôle disposeront d’un accès en lecture seule aux services Azure AD comme les utilisateurs et les groupes. Ce rôle n’a pas d'accès pour afficher, créer ou gérer des tickets de support.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Lire les messages dans le Centre de messages du Centre d’administration Microsoft 365, à l’exclusion des messages de sécurité |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="modern-commerce-user"></a>Utilisateur de Commerce moderne

Ne pas utiliser. Ce rôle est attribué automatiquement à partir de Commerce, et n’est ni prévu, ni pris en charge pour toute autre utilisation. Consultez les détails ci-dessous.

Le rôle Utilisateur de Commerce moderne donne à certains utilisateurs l’autorisation d’accéder au centre d’administration Microsoft 365 et de consulter les entrées de navigation de gauche pour **Hébergement**, **Facturation** et **Support**. Le contenu disponible dans ces zones est contrôlé par des [rôles spécifiques du commerce](../../cost-management-billing/manage/understand-mca-roles.md) attribués aux utilisateurs pour gérer des produits qu’ils ont achetés pour eux-mêmes ou votre organisation. Cela peut inclure des tâches telles que le paiement de factures, ou l’accès à des comptes de facturation et à des profils de facturation.

Les utilisateurs ayant le rôle Utilisateur de Commerce moderne disposent généralement d’autorisations d’administration dans d’autres systèmes d’achat Microsoft, mais ne disposent pas des rôles Administrateur général ou Administrateur de facturation utilisés pour accéder au centre d’administration.

**Quand le rôle Utilisateur de Commerce moderne est-il attribué ?**

* **Achat en libre-service dans le Centre d’administration Microsoft 365** : l’achat en libre-service permet aux utilisateurs de tester de nouveaux produits en les achetant ou en s’y inscrivant eux-mêmes. Ces produits sont gérés dans le Centre d’administration. Les utilisateurs qui effectuent un achat en libre-service se voient attribuer un rôle dans le système Commerce et le rôle Utilisateur de Commerce moderne afin de pouvoir gérer leurs achats dans le centre d’administration. Les administrateurs peuvent bloquer les achats en libre-service (pour Power BI, Power Apps, Power automate) via [PowerShell](/microsoft-365/commerce/subscriptions/allowselfservicepurchase-powershell). Pour plus d’informations, consultez [FAQ sur l’achat en libre-service](/microsoft-365/commerce/subscriptions/self-service-purchase-faq).
* **Achats sur la place de marché commerciale Microsoft** : comme pour l’achat en libre-service, quand un utilisateur achète un produit ou un service sur Microsoft AppSource ou la Place de marché Azure, le rôle Utilisateur de Commerce moderne lui est attribué s’il n’a pas le rôle Administrateur général ou Administrateur de facturation. Dans certains cas, il peut arriver que des utilisateurs soient empêchés d’effectuer ces achats. Pour plus d’informations, consultez [Place de marché commerciale de Microsoft](../../marketplace/marketplace-faq-publisher-guide.yml#what-could-block-a-customer-from-completing-a-purchase-).
* **Propositions de Microsoft** : une proposition est une offre formelle de Microsoft à votre organisation pour l’achat de produits et services Microsoft. Quand la personne qui accepte la proposition n’a pas de rôle Administrateur général ou Administrateur de facturation dans Azure AD, elle se voit attribuer un rôle spécifique du commerce pour compléter la proposition et le rôle Utilisateur de Commerce moderne pour accéder au centre d’administration. Quand elle accède au Centre d’administration, elle ne peut utiliser que des fonctionnalités autorisées par son rôle spécifique du commerce.
* **Rôles spécifiques du commerce** : certains utilisateurs se voient attribuer des rôles spécifiques du commerce. Si un utilisateur n’est pas un Administrateur général ou un Administrateur de facturation, il obtient le rôle Administrateur de Commerce moderne qui lui permet d’accéder au centre d’administration.

En cas de suppression de l’attribution du rôle Utilisateur de Commerce moderne à un utilisateur, celui-ci perd son accès au centre d’administration Microsoft 365. S’il gérait des produits pour lui-même ou pour votre organisation, ils ne peut plus les gérer. Cette gestion peut inclure l’attribution de licences, la modification de modes de paiement, le paiement de factures ou d’autres tâches d’administration des abonnements.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.commerce.billing/partners/read | Lire la propriété partenaire de la facturation Microsoft 365 |
> | microsoft.commerce.volumeLicenseServiceCenter/allEntities/allTasks | Gérer tous les aspects du Centre de services de licences en volume |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/basic/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="network-administrator"></a>Administrateur réseau

Les utilisateurs de ce rôle peuvent passer en revue les recommandations de Microsoft relatives à l’architecture du périmètre réseau basées sur la télémétrie du réseau à partir de leur emplacement utilisateur. Les performances réseau pour Microsoft 365 s’appuient sur une architecture de périmètre de réseau client entreprise soigneuse, qui est généralement propre à la localisation de l’utilisateur. Ce rôle permet de modifier les emplacements utilisateur découverts et la configuration des paramètres réseau pour ces emplacements afin de faciliter les mesures de télémétrie et les suggestions de conception améliorées

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.office365.network/locations/allProperties/allTasks | Gérer tous les aspects des emplacements réseau |
> | microsoft.office365.network/performance/allProperties/read | Lire toutes les propriétés de performances réseau dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="office-apps-administrator"></a>Administrateur d’applications Office

Les utilisateurs titulaires de ce rôle peuvent gérer les paramètres cloud des applications Microsoft 365. Cela comprend la gestion des stratégies cloud, la gestion du téléchargement en libre-service et la possibilité d’afficher des rapports relatifs aux applications Office. Ce rôle accorde en outre la possibilité de gérer les tickets de support et de surveiller l’intégrité du service au sein du centre d’administration principal. Les utilisateurs affectés à ce rôle peuvent également gérer la communication de nouvelles fonctionnalités dans les applications Office.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.messageCenter/messages/read | Lire les messages dans le Centre de messages du Centre d’administration Microsoft 365, à l’exclusion des messages de sécurité |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.userCommunication/allEntities/allTasks | Lire et mettre à jour la visibilité des messages Nouveautés |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="partner-tier1-support"></a>Prise en charge de niveau 1 de partenaire

Ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.

> [!IMPORTANT]
> Ce rôle peut réinitialiser les mots de passe et invalider les jetons d’actualisation uniquement pour les non-administrateurs. Ce rôle ne doit pas être utilisé, car il est déconseillé et ne sera plus réintégré dans l’API.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | Mettre à jour la propriété appRoles sur tous les types d’applications |
> | microsoft.directory/applications/audience/update | Mettre à jour la propriété audience pour les applications |
> | microsoft.directory/applications/authentication/update | Mettre à jour l’authentification sur tous les types d’applications |
> | microsoft.directory/applications/basic/update | Mettre à jour les propriétés de base pour les applications |
> | microsoft.directory/applications/credentials/update | Mettre à jour les informations d’identification d’application |
> | microsoft.directory/applications/notes/update | Mettre à jour les notes d’applications |
> | microsoft.directory/applications/owners/update | Mettre à jour les propriétaires d’applications |
> | microsoft.directory/applications/permissions/update | Mettre à jour les autorisations exposées et les autorisations requises sur tous les types d’applications |
> | microsoft.directory/applications/policies/update | Mettre à jour les stratégies d’applications |
> | microsoft.directory/applications/tag/update | Mettre à jour les balises d’applications |
> | microsoft.directory/contacts/create | Créer des contacts |
> | microsoft.directory/contacts/delete | Supprimer des contacts |
> | microsoft.directory/contacts/basic/update | Mettre à jour les propriétés de base sur les contacts |
> | microsoft.directory/deletedItems.groups/restore | Restaurer les groupes supprimés de manière réversible à l’état d’origine |
> | microsoft.directory/groups/create | Créer des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/delete | Supprimer des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/restore | Restauration de groupes supprimés |
> | microsoft.directory/groups/members/update | Actualiser la propriété membres des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/owners/update | Actualiser la propriété propriétaires des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Créer et supprimer des octrois d’autorisations OAuth 2.0, et lire et mettre à jour toutes les propriétés |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Mettre à jour les attributions de rôles des principaux de service |
> | microsoft.directory/users/assignLicense | Gérer les licences utilisateur |
> | microsoft.directory/users/create | Ajouter des utilisateurs |
> | microsoft.directory/users/delete | Suppression d’utilisateurs |
> | microsoft.directory/users/disable | Désactiver des utilisateurs |
> | microsoft.directory/users/enable | Activer des utilisateurs |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forcer la déconnexion en invalidant les jetons d’actualisation de l’utilisateur |
> | microsoft.directory/users/restore | Restaurer des utilisateurs supprimés |
> | microsoft.directory/users/basic/update | Mettre à jour les propriétés de base sur les utilisateurs |
> | microsoft.directory/users/manager/update | Mettre à jour le gestionnaire pour les utilisateurs |
> | microsoft.directory/users/password/update | Réinitialiser les mots de passe de tous les utilisateurs |
> | microsoft.directory/users/photo/update | Actualiser la photo des utilisateurs |
> | microsoft.directory/users/userPrincipalName/update | Mettre à jour le nom d’utilisateur principal des utilisateurs |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="partner-tier2-support"></a>Prise en charge de niveau 2 de partenaire

Ne pas utiliser. Ce rôle est déconseillé et sera prochainement supprimé d’Azure AD. Il s’adresse à un petit nombre de partenaires revendeurs Microsoft et n’est pas destiné à une utilisation générale.

> [!IMPORTANT]
> Ce rôle peut réinitialiser les mots de passe et invalider les jetons d’actualisation pour l’ensemble des non-administrateurs et des administrateurs (y compris les administrateurs généraux). Ce rôle ne doit pas être utilisé, car il est déconseillé et ne sera plus réintégré dans l’API.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/applications/appRoles/update | Mettre à jour la propriété appRoles sur tous les types d’applications |
> | microsoft.directory/applications/audience/update | Mettre à jour la propriété audience pour les applications |
> | microsoft.directory/applications/authentication/update | Mettre à jour l’authentification sur tous les types d’applications |
> | microsoft.directory/applications/basic/update | Mettre à jour les propriétés de base pour les applications |
> | microsoft.directory/applications/credentials/update | Mettre à jour les informations d’identification d’application |
> | microsoft.directory/applications/notes/update | Mettre à jour les notes d’applications |
> | microsoft.directory/applications/owners/update | Mettre à jour les propriétaires d’applications |
> | microsoft.directory/applications/permissions/update | Mettre à jour les autorisations exposées et les autorisations requises sur tous les types d’applications |
> | microsoft.directory/applications/policies/update | Mettre à jour les stratégies d’applications |
> | microsoft.directory/applications/tag/update | Mettre à jour les balises d’applications |
> | microsoft.directory/contacts/create | Créer des contacts |
> | microsoft.directory/contacts/delete | Supprimer des contacts |
> | microsoft.directory/contacts/basic/update | Mettre à jour les propriétés de base sur les contacts |
> | microsoft.directory/deletedItems.groups/restore | Restaurer les groupes supprimés de manière réversible à l’état d’origine |
> | microsoft.directory/domains/allProperties/allTasks | Créer et supprimer des domaines, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/groups/create | Créer des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/delete | Supprimer des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/restore | Restauration de groupes supprimés |
> | microsoft.directory/groups/members/update | Actualiser la propriété membres des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/owners/update | Actualiser la propriété propriétaires des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Créer et supprimer des octrois d’autorisations OAuth 2.0, et lire et mettre à jour toutes les propriétés |
> | microsoft.directory/organization/basic/update | Mettre à jour des propriétés de base sur une organisation |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Créer et supprimer des attributions de rôle, et lire et mettre à jour toutes les propriétés des attributions de rôle |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Créer et supprimer des définitions de rôle, et lire et mettre à jour toutes les propriétés |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Créer et supprimer des scopedRoleMemberships, et lire et mettre à jour toutes les propriétés |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Mettre à jour les attributions de rôles des principaux de service |
> | microsoft.directory/subscribedSkus/standard/read | Lire les propriétés de base sur les abonnements |
> | microsoft.directory/users/assignLicense | Gérer les licences utilisateur |
> | microsoft.directory/users/create | Ajouter des utilisateurs |
> | microsoft.directory/users/delete | Suppression d’utilisateurs |
> | microsoft.directory/users/disable | Désactiver des utilisateurs |
> | microsoft.directory/users/enable | Activer des utilisateurs |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forcer la déconnexion en invalidant les jetons d’actualisation de l’utilisateur |
> | microsoft.directory/users/restore | Restaurer des utilisateurs supprimés |
> | microsoft.directory/users/basic/update | Mettre à jour les propriétés de base sur les utilisateurs |
> | microsoft.directory/users/manager/update | Mettre à jour le gestionnaire pour les utilisateurs |
> | microsoft.directory/users/password/update | Réinitialiser les mots de passe de tous les utilisateurs |
> | microsoft.directory/users/photo/update | Actualiser la photo des utilisateurs |
> | microsoft.directory/users/userPrincipalName/update | Mettre à jour le nom d’utilisateur principal des utilisateurs |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="password-administrator"></a>Administrateur de mots de passe

Les utilisateurs disposant de ce rôle ont une capacité limitée à gérer les mots de passe. Ce rôle n’accorde pas la possibilité de gérer les demandes de service ou de surveiller l’intégrité du service. Le rôle attribué à un utilisateur détermine si un administrateur de mot de passe peut réinitialiser le mot de passe de l’utilisateur. Pour obtenir la liste des rôles pour lesquels un administrateur de mot de passe peut réinitialiser les mots de passe, consultez [Autorisations de réinitialisation de mot de passe](#password-reset-permissions).

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/users/password/update | Réinitialiser les mots de passe de tous les utilisateurs |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="power-bi-administrator"></a>Administrateur Power BI

Les utilisateurs avec ce rôle ont des autorisations générales dans Microsoft Power BI, quand le service est présent, et peuvent gérer les tickets de support et superviser l’intégrité du service. Pour plus d’informations, consultez la page [Présentation du rôle Administrateur Power BI](/power-bi/service-admin-role).

> [!NOTE]
> Dans l’API Microsoft Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de service Power BI ». Il est appelé « Administrateur Power BI » dans le [portail Azure](https://portal.azure.com).

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |
> | microsoft.powerApps.powerBI/allEntities/allTasks | Gérez tous les aspects de Power BI |

## <a name="power-platform-administrator"></a>Administrateur de plateforme Power

Les utilisateurs de ce rôle peuvent créer et gérer tous les aspects des environnements, Power Apps, Flows et les stratégies de protection contre la perte de données. Les utilisateurs avec ce rôle sont aussi en mesure de gérer les tickets de support et de surveiller l’état des services.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.dynamics365/allEntities/allTasks | Gérez tous les aspects de Dynamics 365 |
> | microsoft.flow/allEntities/allTasks | Gérez tous les aspects de Microsoft Power Automate |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |
> | microsoft.powerApps/allEntities/allTasks | Gérez tous les aspects de Power Apps |

## <a name="printer-administrator"></a>Administrateur d’imprimantes

Les utilisateurs ayant ce rôle peuvent inscrire des imprimantes et gérer tous les aspects de toutes les configurations d’imprimante dans la solution d’impression universelle Microsoft, y compris les paramètres du connecteur d’impression universel. Ils peuvent consentir à toutes les demandes d’autorisation d’impression déléguée. Les administrateurs d’imprimantes ont également accès aux rapports d’impression.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.azure.print/allEntities/allProperties/allTasks | Créer et supprimer des imprimantes et des connecteurs, ainsi que lire et mettre à jour toutes les propriétés dans Microsoft Print |

## <a name="printer-technician"></a>Technicien en charge des imprimantes

Les utilisateurs ayant ce rôle peuvent inscrire des imprimantes et gérer leur état dans la solution d’impression universelle Microsoft. Ils peuvent également lire toutes les informations du connecteur. Une tâche clé qu’un technicien en charge des imprimantes ne peut pas accomplir est la définition d’autorisations utilisateur sur des imprimantes et le partage d’imprimantes.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.azure.print/connectors/allProperties/read | Lire toutes les propriétés des connecteurs dans Microsoft Print |
> | microsoft.azure.print/printers/allProperties/read | Lire toutes les propriétés des imprimantes dans Microsoft Print |
> | microsoft.azure.print/printers/register | Inscrire des imprimantes dans Microsoft Print |
> | microsoft.azure.print/printers/unregister | Désinscrire des imprimantes dans Microsoft Print |
> | microsoft.azure.print/printers/basic/update | Mettre à jour les propriétés de base des imprimantes dans Microsoft Print |

## <a name="privileged-authentication-administrator"></a>Administrateur d’authentification privilégié

Les utilisateurs disposant de ce rôle peuvent définir ou réinitialiser toute méthode d’authentification (y compris les mots de passe) pour n’importe quel utilisateur, y compris les administrateurs généraux. Les administrateurs d’authentification privilégiés peuvent forcer les utilisateurs à se réinscrire avec des informations d’identification sans mot de passe existantes (par exemple, MFA, FIDO) et à révoquer la mémorisation de l’authentification multifacteur sur l’appareil, ce qui a pour effet de demander une authentification multifacteur lors de la prochaine connexion de tous les utilisateurs.

Le rôle [Administrateur d’authentification](#authentication-administrator) est autorisé à forcer la réinscription et l’authentification multifacteur pour les utilisateurs standard et les utilisateurs avec des rôles d’administrateur.

Le rôle [Administrateur de la stratégie d’authentification](#authentication-policy-administrator) est autorisé à définir la stratégie de méthode d’authentification du locataire déterminant les méthodes que chaque utilisateur peut enregistrer et utiliser.

| Role | Gérer les méthodes d’authentification de l’utilisateur | Gérer l’authentification multifacteur par utilisateur | Gérer les paramètres de l’authentification multifacteur | Gérer la stratégie de méthode d’authentification | Gérer la stratégie de protection par mot de passe |
| ---- | ---- | ---- | ---- | ---- | ---- |
| Administrateur d’authentification | Oui pour certains utilisateurs (voir ci-dessus) | Oui pour certains utilisateurs (voir ci-dessus) | Non | Non | Non |
| Administrateur d’authentification privilégié| Oui pour tous les utilisateurs | Oui pour tous les utilisateurs | Non | Non | Non |
| Administrateur de la stratégie d’authentification | Non | Non | Oui | Oui | Oui |

> [!IMPORTANT]
> Les utilisateurs auxquels ce rôle est assigné peuvent changer les informations d’identification des personnes susceptibles d’avoir accès à des informations sensibles ou privées ou à des configurations critiques à l’intérieur et à l’extérieur d’Azure Active Directory. Changer les informations d’identification d’un utilisateur peut signifier la capacité d’assumer l’identité et les autorisations de cet utilisateur. Par exemple :
>
>* Les propriétaires d’inscription d’application et d’application d’entreprise, qui peuvent gérer les informations d’identification des applications qu’ils possèdent. Ces applications peuvent disposer d’autorisations privilégiées dans Azure AD et ailleurs qui ne sont pas accordées aux administrateurs d’authentification. Par ce biais, un administrateur d’authentification peut assumer l’identité d’un propriétaire d’application, puis d’assumer l’identité d’une application privilégiée en mettant à jour les identifiants de celle-ci.
>* Propriétaires d’abonnement Azure, qui peuvent avoir accès à des informations confidentielles ou privées ou à une configuration critique dans Azure.
>* Propriétaires de groupe de sécurité et de groupe Microsoft 365, qui peuvent gérer l’appartenance à un groupe. Ces groupes peuvent accorder l’accès à des informations sensibles ou privées ou à une configuration critique dans Azure AD et ailleurs.
>* Administrateurs d’autres services en dehors d’Azure AD, tels qu’Exchange Online, le Centre de sécurité et de conformité Office et les systèmes de ressources humaines.
>* Les non-administrateurs comme les cadres supérieurs, les conseillers juridiques et les employés des ressources humaines qui peuvent avoir accès à des informations sensibles ou privées.


> [!IMPORTANT]
> Ce rôle ne peut actuellement pas gérer l’authentification multifacteur par utilisateur dans le portail de gestion d’authentification multifacteur hérité. Les mêmes fonctions peuvent être effectuées à l’aide du module Azure AD Powershell de l’applet de commande [Set-MsolUser](/powershell/module/msonline/set-msoluser).

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/users/authenticationMethods/create | Créer des méthodes d’authentification pour les utilisateurs |
> | microsoft.directory/users/authenticationMethods/delete | Supprimer les méthodes d’authentification pour les utilisateurs |
> | microsoft.directory/users/authenticationMethods/standard/read | Lire les propriétés standard des méthodes d’authentification pour les utilisateurs |
> | microsoft.directory/users/authenticationMethods/basic/update | Mettre à jour les propriétés de base des méthodes d’authentification pour les utilisateurs |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forcer la déconnexion en invalidant les jetons d’actualisation de l’utilisateur |
> | microsoft.directory/users/password/update | Réinitialiser les mots de passe de tous les utilisateurs |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="privileged-role-administrator"></a>Administrateur de rôle privilégié

Les utilisateurs avec ce rôle peuvent gérer les attributions de rôles dans Azure Active Directory et Azure AD Privileged Identity Management. Ils peuvent créer et gérer des groupes qui peuvent être affectés à des rôles Azure AD. De plus, ce rôle permet de gérer tous les aspects de Privileged Identity Management et des unités administratives.

> [!IMPORTANT]
> Ce rôle donne la possibilité de gérer les attributions de tous les rôles Azure AD, y compris le rôle d’administrateur général. Ce rôle n’inclut pas d’autres capacités privilégiées dans Azure AD, comme la création ou la mise à jour des utilisateurs. Toutefois, les utilisateurs affectés à ce rôle peuvent s’accorder ou accorder à d’autres des privilèges supplémentaires en leur attribuant des rôles supplémentaires.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/administrativeUnits/allProperties/allTasks | Créez et gérez des unités administratives (y compris les membres). |
> | microsoft.directory/authorizationPolicy/allProperties/allTasks | Gérez tous les aspects des stratégies d’autorisation |
> | microsoft.directory/directoryRoles/allProperties/allTasks | Créer et supprimer des rôles d’annuaire, ainsi que lire et mettre à jour toutes les propriétés |
> | microsoft.directory/groupsAssignableToRoles/create | Créer des groupes pouvant se voir attribuer des rôles |
> | microsoft.directory/groupsAssignableToRoles/delete | Supprimer des groupes assignables à un rôle |
> | microsoft.directory/groupsAssignableToRoles/restore | Restaurer des groupes assignables à un rôle |
> | microsoft.directory/groupsAssignableToRoles/allProperties/update | Mettre à jour des groupes assignables à un rôle |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Créer et supprimer des octrois d’autorisations OAuth 2.0, et lire et mettre à jour toutes les propriétés |
> | microsoft.directory/privilegedIdentityManagement/allProperties/allTasks | Créer et supprimer toutes les ressources, ainsi que lire et mettre à jour les propriétés standard dans Privileged Identity Management |
> | microsoft.directory/roleAssignments/allProperties/allTasks | Créer et supprimer des attributions de rôle, et lire et mettre à jour toutes les propriétés des attributions de rôle |
> | microsoft.directory/roleDefinitions/allProperties/allTasks | Créer et supprimer des définitions de rôle, et lire et mettre à jour toutes les propriétés |
> | microsoft.directory/scopedRoleMemberships/allProperties/allTasks | Créer et supprimer des scopedRoleMemberships, et lire et mettre à jour toutes les propriétés |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Mettre à jour les attributions de rôles des principaux de service |
> | microsoft.directory/servicePrincipals/permissions/update | Mettre à jour les autorisations des principaux de service |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForAll.microsoft-company-admin | Consentir à toute autorisation sur toute application |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="reports-reader"></a>Lecteur de rapports

Les utilisateurs avec ce rôle peuvent voir les données de rapports sur l’utilisation et le tableau de bord des rapports dans le centre d’administration Microsoft 365 et le pack du contexte d’adoption dans Power BI. En outre, ce rôle donne accès aux rapports de connexion et aux activités dans Azure AD, ainsi qu’aux données renvoyées par l’API de création de rapports Microsoft Graph. Un utilisateur disposant du rôle Lecteur de rapports peut uniquement accéder au métriques d’utilisation et d’adoption pertinentes. Il n’a pas les autorisations administrateur permettant de configurer les paramètres ou d’accéder aux centres d’administration propres au produit comme Exchange. Ce rôle n’a pas d'accès pour afficher, créer ou gérer des tickets de support.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Lire toutes les propriétés dans les journaux d’audit, y compris les propriétés privilégiées |
> | microsoft.directory/provisioningLogs/allProperties/read | Lire toutes les propriétés des journaux de provisionnement. |
> | microsoft.directory/signInReports/allProperties/read | Lire toutes les propriétés dans les rapports de connexion, y compris les propriétés privilégiées |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.office365.network/performance/allProperties/read | Lire toutes les propriétés de performances réseau dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lire les rapports d’utilisation d’Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="search-administrator"></a>Administrateur de recherche

Les utilisateurs dotés de ce rôle disposent d’un accès total à toutes les fonctionnalités de gestion de Recherche Microsoft disponibles dans le centre d’administration Microsoft 365. De plus, ces utilisateurs peuvent voir le Centre de messages, superviser l’intégrité du service et créer des demandes de service.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Lire les messages dans le Centre de messages du Centre d’administration Microsoft 365, à l’exclusion des messages de sécurité |
> | microsoft.office365.search/content/manage | Créer et supprimer du contenu, ainsi que lire et mettre à jour toutes les propriétés dans Recherche Microsoft |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="search-editor"></a>Éditeur de recherche

Les utilisateurs dotés de ce rôle peuvent créer, gérer et supprimer du contenu pour Recherche Microsoft dans le centre d’administration Microsoft 365, notamment des signets, des questions et réponses, ainsi que des emplacements.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.office365.messageCenter/messages/read | Lire les messages dans le Centre de messages du Centre d’administration Microsoft 365, à l’exclusion des messages de sécurité |
> | microsoft.office365.search/content/manage | Créer et supprimer du contenu, ainsi que lire et mettre à jour toutes les propriétés dans Recherche Microsoft |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="security-administrator"></a>Administrateur de sécurité

Les utilisateurs disposant de ce rôle sont autorisés à gérer les fonctionnalités liées à la sécurité dans le portail Microsoft 365 Defender, Azure Active Directory Identity Protection, Azure Active Directory Authentication, Azure Information Protection et le Centre de sécurité et conformité Office 365. Pour plus d’informations sur les autorisations Office 365, consultez [Autorisations dans le Centre de sécurité et de conformité](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

Dans | Peut
--- | ---
[Centre de sécurité Microsoft 365](https://protection.office.com) | Surveiller les stratégies de sécurité au sein des services Microsoft 365<br>Gérer les menaces et les alertes liées à la sécurité<br>Afficher les rapports
Identity Protection Center | Toutes les autorisations du rôle lecteur de sécurité<br>En outre, possibilité d’effectuer toutes les opérations du centre de protection de l'identité, à l’exception de la réinitialisation des mots de passe.
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Toutes les autorisations du rôle lecteur de sécurité<br>**Ne peut pas** gérer les paramètres ou les attributions de rôle Azure AD
[Centre de sécurité et conformité Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gérer les stratégies de sécurité<br>Afficher, examiner et répondre aux menaces de sécurité<br>Afficher les rapports
Azure Advanced Threat Protection | Surveiller et répondre aux activités de sécurité suspectes
Windows Defender ATP et EDR | Attribuer des rôles<br>Gérer des groupes de machines<br>Configurer la détection des menaces liées aux points de terminaison et leur correction automatisée<br>Afficher, examiner et répondre aux alertes
[Intune](/intune/role-based-access-control) | Affiche des informations relatives à l'utilisateur, l'appareil, l'inscription, la configuration et l'application<br>Ne peut apporter de modifications à Intune
[Cloud App Security](/cloud-app-security/manage-admins) | Ajouter des administrateurs, des stratégies et des paramètres, charger des journaux d’activité et effectuer des actions de gouvernance
[Intégrité des services Microsoft 365](/office365/enterprise/view-service-health) | Afficher l’intégrité des services Microsoft 365
[Verrouillage intelligent](../authentication/howto-password-smart-lockout.md) | Définissez le seuil et la durée des verrouillages lorsque des événements d’échec de connexion se produisent.
[Protection par mot de passe](../authentication/concept-password-ban-bad.md) | Configurez une liste personnalisée de mots de passe interdits ou une protection par mot de passe locale.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/applications/policies/update | Mettre à jour les stratégies d’applications |
> | microsoft.directory/auditLogs/allProperties/read | Lire toutes les propriétés dans les journaux d’audit, y compris les propriétés privilégiées |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.directory/bitlockerKeys/key/read | Lire les métadonnées et la clé BitLocker sur les appareils |
> | microsoft.directory/entitlementManagement/allProperties/read | Lire toutes les propriétés dans la fonctionnalité de gestion des droits d’utilisation Azure AD |
> | microsoft.directory/identityProtection/allProperties/read | Lire toutes les ressources dans Azure AD Identity Protection |
> | microsoft.directory/identityProtection/allProperties/update | Mettre à jour toutes les ressources dans Azure AD Identity Protection |
> | microsoft.directory/policies/create | Créer des stratégies dans Azure AD |
> | microsoft.directory/policies/delete | Supprimer des stratégies Azure AD |
> | microsoft.directory/policies/basic/update | Mettre à jour les propriétés de base sur les stratégies |
> | microsoft.directory/policies/owners/update | Mettre à jour les propriétaires de stratégies |
> | microsoft.directory/policies/tenantDefault/update | Mettre à jour les stratégies d’organisation par défaut |
> | microsoft.directory/conditionalAccessPolicies/create | Créer des stratégies d’accès conditionnel |
> | microsoft.directory/conditionalAccessPolicies/delete | Supprimer des stratégies d’accès conditionnel |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Accès conditionnel en lecture pour les stratégies |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Lire les propriétaires des stratégies d’accès conditionnel |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Lire la propriété « appliqué à » pour les stratégies d’accès conditionnel |
> | microsoft.directory/conditionalAccessPolicies/basic/update | Mettre à jour les propriétés de base pour les stratégies d’accès conditionnel |
> | microsoft.directory/conditionalAccessPolicies/owners/update | Mettre à jour les propriétaires des stratégies d’accès conditionnel |
> | microsoft.directory/conditionalAccessPolicies/tenantDefault/update | Mettre à jour le locataire par défaut pour les stratégies d’accès conditionnelles |
> | microsoft.directory/crossTenantAccessPolicies/create | Créer des stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/delete | Supprimer des stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/standard/read | Lire les propriétés de base des stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/owners/read | Lire les propriétaires de stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/policyAppliedTo/read | Lire la propriété policyAppliedTo des stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/basic/update | Mettre à jour les propriétés de base des stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/owners/update | Mettre à jour les propriétaires de stratégies d’accès inter-locataires |
> | microsoft.directory/crossTenantAccessPolicies/tenantDefault/update | Mettre à jour le locataire par défaut pour les stratégies d’accès inter-locataires |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Lire toutes les ressources dans Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lire toutes les propriétés des journaux de provisionnement. |
> | microsoft.directory/servicePrincipals/policies/update | Mettre à jour les stratégies des principaux de service |
> | microsoft.directory/signInReports/allProperties/read | Lire toutes les propriétés dans les rapports de connexion, y compris les propriétés privilégiées |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Lire les propriétés standard de toutes les ressources dans les centres de sécurité et de conformité |
> | microsoft.office365.protectionCenter/allEntities/basic/update | Mettre à jour les propriétés de base de toutes les ressources dans les centres de sécurité et de conformité |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/allTasks | Créer et gérer des charges utiles d’attaque dans le Simulateur d’attaques |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Lire les rapports de simulation d’attaque, les réponses et la formation associée |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/allTasks | Créer et gérer des modèles de simulation d’attaque dans le Simulateur d’attaques |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="security-operator"></a>Opérateur de sécurité

Les utilisateurs dotés de ce rôle peuvent gérer les alertes et disposent d’un accès global en lecture seule aux fonctionnalités liées à la sécurité, notamment à toutes les informations disponibles dans le centre de sécurité Microsoft 365, Azure Active Directory, Identity Protection, Privileged Identity Management et le Centre de sécurité et de conformité Office 365. Pour plus d’informations sur les autorisations Office 365, consultez [Autorisations dans le Centre de sécurité et de conformité](/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

| Dans | Peut |
| --- | --- |
| [Centre de sécurité Microsoft 365](https://protection.office.com) | Toutes les autorisations du rôle lecteur de sécurité<br/>Voir, examiner et répondre aux alertes sur les menaces de sécurité<br/>Gérer les paramètres de sécurité dans le centre de sécurité |
| [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) | Toutes les autorisations du rôle lecteur de sécurité<br>En outre, possibilité d’effectuer toutes les opérations du centre de protection de l’identité, à l’exception de la réinitialisation des mots de passe et de la configuration des e-mails d’alerte. |
| [Privileged Identity Management](../privileged-identity-management/pim-configure.md) | Toutes les autorisations du rôle lecteur de sécurité |
| [Centre de sécurité et conformité Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Toutes les autorisations du rôle lecteur de sécurité<br>Voir, examiner et répondre aux alertes de sécurité |
| Windows Defender ATP et EDR | Toutes les autorisations du rôle lecteur de sécurité<br>Voir, examiner et répondre aux alertes de sécurité |
| [Intune](/intune/role-based-access-control) | Toutes les autorisations du rôle lecteur de sécurité |
| [Cloud App Security](/cloud-app-security/manage-admins) | Toutes les autorisations du rôle lecteur de sécurité |
| [Intégrité des services Microsoft 365](/microsoft-365/enterprise/view-service-health) | Afficher l’intégrité des services Microsoft 365 |

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Lire toutes les propriétés dans les journaux d’audit, y compris les propriétés privilégiées |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.directory/cloudAppSecurity/allProperties/allTasks | Créer et supprimer toutes les ressources, ainsi que lire et mettre à jour les propriétés standard dans Microsoft Cloud App Security |
> | microsoft.directory/identityProtection/allProperties/allTasks | Créer et supprimer toutes les ressources, ainsi que lire et mettre à jour les propriétés standard dans Azure AD Identity Protection |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Lire toutes les ressources dans Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lire toutes les propriétés des journaux de provisionnement. |
> | microsoft.directory/signInReports/allProperties/read | Lire toutes les propriétés dans les rapports de connexion, y compris les propriétés privilégiées |
> | microsoft.azure.advancedThreatProtection/allEntities/allTasks | Gérer tous les aspects d’Azure Advanced Threat Protection |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.intune/allEntities/read | Lire toutes les ressources dans Microsoft Intune |
> | microsoft.office365.securityComplianceCenter/allEntities/allTasks | Créer et supprimer toutes les ressources, ainsi que lire et mettre à jour les propriétés standard dans le Centre de sécurité et conformité Office 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.windows.defenderAdvancedThreatProtection/allEntities/allTasks | Gérer tous les aspects de Microsoft Defender pour point de terminaison |

## <a name="security-reader"></a>Lecteur Sécurité

Les utilisateurs dotés de ce rôle ont un accès en lecture seule au niveau global à la fonctionnalité liée à la sécurité, notamment à toutes les informations dans le centre de sécurité Microsoft 365, Azure Active Directory, Identity Protection, Privileged Identity Management. Ils peuvent aussi lire les rapports sur les connexions Azure Active Directory et les journaux d’audit, ainsi que dans le centre de sécurité et de conformité Office 365. Pour plus d’informations sur les autorisations Office 365, consultez [Autorisations dans le Centre de sécurité et de conformité](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

Dans | Peut
--- | ---
[Centre de sécurité Microsoft 365](https://protection.office.com) | Afficher les stratégies de sécurité au sein des services Microsoft 365<br>Afficher les menaces et les alertes liées à la sécurité<br>Afficher les rapports
Identity Protection Center | Lire tous les rapports de sécurité et informations de paramètres pour les fonctionnalités de sécurité<br><ul><li>Anti-spam<li>Chiffrement<li>Prévention contre la perte de données<li>Anti-programme malveillant<li>Détection avancée des menaces<li>Anti-hameçonnage<li>Règles de flux courrier
[Privileged Identity Management](../privileged-identity-management/pim-configure.md) | A un accès en lecture seule à toutes les informations disponibles dans Azure AD Privileged Identity Management : Stratégies et rapports pour les attributions de rôles Azure AD et révisions de sécurité.<br>**Ne peut pas** s’inscrire auprès d’Azure AD Privileged Identity Management ni y apporter des modifications. Dans le portail Privileged Identity Management ou par le biais de PowerShell, un membre ayant ce rôle peut activer des rôles supplémentaires (par exemple, un administrateur général ou un administrateur de rôle privilégié), s’il est éligible.
[Centre de sécurité et conformité Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Afficher les stratégies de sécurité<br>Afficher et examiner les menaces de sécurité<br>Afficher les rapports
Windows Defender ATP et EDR | Afficher et examiner les alertes. Lorsque vous activez le contrôle d’accès en fonction du rôle dans Windows Defender ATP, les utilisateurs disposant d’autorisations en lecture seule, comme le rôle Lecteur de Sécurité Azure AD, perdent l’accès jusqu’à ce qu’ils soient affectés à un rôle Windows Defender ATP.
[Intune](/intune/role-based-access-control) | Affiche des informations relatives à l'utilisateur, l'appareil, l'inscription, la configuration et l'application. Ne peut pas apporter de modifications à Intune.
[Cloud App Security](/cloud-app-security/manage-admins) | Dispose d'autorisations en lecture et peut gérer les alertes
[Intégrité des services Microsoft 365](/office365/enterprise/view-service-health) | Afficher l’intégrité des services Microsoft 365

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/auditLogs/allProperties/read | Lire toutes les propriétés dans les journaux d’audit, y compris les propriétés privilégiées |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.directory/bitlockerKeys/key/read | Lire les métadonnées et la clé BitLocker sur les appareils |
> | microsoft.directory/entitlementManagement/allProperties/read | Lire toutes les propriétés dans la fonctionnalité de gestion des droits d’utilisation Azure AD |
> | microsoft.directory/identityProtection/allProperties/read | Lire toutes les ressources dans Azure AD Identity Protection |
> | microsoft.directory/policies/standard/read | Lire les propriétés de base sur les stratégies |
> | microsoft.directory/policies/owners/read | Lire les propriétaires de stratégies |
> | microsoft.directory/policies/policyAppliedTo/read | Lire la propriété policies.policyAppliedTo |
> | microsoft.directory/conditionalAccessPolicies/standard/read | Accès conditionnel en lecture pour les stratégies |
> | microsoft.directory/conditionalAccessPolicies/owners/read | Lire les propriétaires des stratégies d’accès conditionnel |
> | microsoft.directory/conditionalAccessPolicies/policyAppliedTo/read | Lire la propriété « appliqué à » pour les stratégies d’accès conditionnel |
> | microsoft.directory/privilegedIdentityManagement/allProperties/read | Lire toutes les ressources dans Privileged Identity Management |
> | microsoft.directory/provisioningLogs/allProperties/read | Lire toutes les propriétés des journaux de provisionnement. |
> | microsoft.directory/signInReports/allProperties/read | Lire toutes les propriétés dans les rapports de connexion, y compris les propriétés privilégiées |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.office365.protectionCenter/allEntities/standard/read | Lire les propriétés standard de toutes les ressources dans les centres de sécurité et de conformité |
> | microsoft.office365.protectionCenter/attackSimulator/payload/allProperties/read | Lire toutes les propriétés des charges utiles d’attaque dans le Simulateur d’attaques |
> | microsoft.office365.protectionCenter/attackSimulator/reports/allProperties/read | Lire les rapports de simulation d’attaque, les réponses et la formation associée |
> | microsoft.office365.protectionCenter/attackSimulator/simulation/allProperties/read | Lire toutes les propriétés des modèles de simulation d’attaque dans le Simulateur d’attaques |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="service-support-administrator"></a>Administrateur de support de service

Les utilisateurs titulaires de ce rôle peuvent ouvrir des demandes de support auprès de Microsoft pour les services Azure et Microsoft 365, ainsi que voir le tableau de bord des services et le Centre de messages dans le [portail Azure](https://portal.azure.com) et le [centre d’administration Microsoft 365](https://admin.microsoft.com). Plus d’informations sur les [Rôles d’administrateur](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Auparavant, ce rôle était nommé « Administrateur de services » dans [Portail Azure](https://portal.azure.com) et le [Centre d’administration Microsoft 365](https://admin.microsoft.com). Nous l’avons renommé « Administrateur de support de service » pour l’aligner sur le nom existant dans l’API Microsoft Graph, l’API Azure AD Graph et Azure AD PowerShell.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.network/performance/allProperties/read | Lire toutes les propriétés de performances réseau dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="sharepoint-administrator"></a>Administrateur SharePoint

Les utilisateurs avec ce rôle ont des autorisations générales dans Microsoft SharePoint Online, quand le service est présent. Ils peuvent également créer et gérer tous les groupes Microsoft 365, gérer les tickets de support et superviser l’intégrité des services. Plus d’informations sur les [Rôles d’administrateur](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Dans l’API Microsoft Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de service SharePoint ». Il est « Administrateur SharePoint » dans le [portail Azure](https://portal.azure.com).

> [!NOTE]
> Ce rôle accorde également des autorisations délimitées à l’API Microsoft Graph pour Microsoft Intune, d’où la possibilité de gérer et de configurer des stratégies liées aux ressources SharePoint et Onedrive.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/deletedItems.groups/delete | Supprimer définitivement les groupes qui ne peuvent plus être restaurés |
> | microsoft.directory/deletedItems.groups/restore | Restaurer les groupes supprimés de manière réversible à l’état d’origine |
> | microsoft.directory/groups.unified/create | Créer des groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/delete | Supprimer des groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/restore | Restaurer des groupes Microsoft 365 |
> | microsoft.directory/groups.unified/basic/update | Actualiser les propriétés de base sur les groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/members/update | Actualiser les membres de groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/owners/update | Actualiser les propriétaires de groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.network/performance/allProperties/read | Lire toutes les propriétés de performances réseau dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.sharePoint/allEntities/allTasks | Créer et supprimer toutes les ressources, ainsi que lire et mettre à jour les propriétés standard SharePoint |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lire les rapports d’utilisation d’Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="skype-for-business-administrator"></a>Administrateur Skype Entreprise

Les utilisateurs avec ce rôle ont des autorisations générales dans Microsoft Skype Entreprise, quand le service est présent. Ils peuvent également gérer les attributs utilisateur propres à Skype dans Azure Active Directory. En outre, ce rôle permet de gérer les tickets de support et de surveiller l’intégrité des services, ainsi que d’accéder au Centre d’administration Teams et Skype Entreprise. Le compte doit également disposer d’une licence Teams ; dans le cas contraire, il ne pourra pas exécuter les applets de commande PowerShell Teams. Pour plus d’informations, consultez la page [About the Skype for Business admin role](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) (À propos du rôle d’administrateur Skype Entreprise) ; pour plus d’informations sur les licences Teams, consultez l’article [Licences de compléments pour Skype Entreprise et Microsoft Teams](/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing).

> [!NOTE]
> Dans l’API Microsoft Graph et Azure AD PowerShell, ce rôle est identifié comme « Administrateur de service SharePoint ». Il est appelé « Administrateur Skype Entreprise » dans le [portail Azure](https://portal.azure.com/).

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gérez tous les aspects de Skype Entreprise Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lire les rapports d’utilisation d’Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="teams-administrator"></a>Administrateur Teams

Les utilisateurs dans ce rôle peuvent gérer tous les aspects de la charge de travail Microsoft Teams via le centre d’administration Microsoft Teams et Skype Entreprise et les modules PowerShell respectifs. Cela inclut notamment tous les outils de gestion liés à la téléphonie, à la messagerie, aux réunions et aux équipes proprement dites. En outre, ce rôle permet de créer et de gérer tous les groupes Microsoft 365, de gérer les tickets de support et de surveiller l’état d’intégrité des services.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.directory/deletedItems.groups/delete | Supprimer définitivement les groupes qui ne peuvent plus être restaurés |
> | microsoft.directory/deletedItems.groups/restore | Restaurer les groupes supprimés de manière réversible à l’état d’origine |
> | microsoft.directory/groups/hiddenMembers/read | Lire la propriété membres masqués des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groups.unified/create | Créer des groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/delete | Supprimer des groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/restore | Restaurer des groupes Microsoft 365 |
> | microsoft.directory/groups.unified/basic/update | Actualiser les propriétés de base sur les groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/members/update | Actualiser les membres de groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.unified/owners/update | Actualiser les propriétaires de groupes Microsoft 365, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/servicePrincipals/managePermissionGrantsForGroup.microsoft-all-application-permissions | Accorder à un principal de service l’accès direct aux données d’un groupe |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.network/performance/allProperties/read | Lire toutes les propriétés de performances réseau dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gérez tous les aspects de Skype Entreprise Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lire les rapports d’utilisation d’Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |
> | microsoft.teams/allEntities/allProperties/allTasks | Gérez toutes les ressources dans Teams |

## <a name="teams-communications-administrator"></a>Administrateur des communications Teams

Les utilisateurs dans ce rôle peuvent gérer les aspects de la charge de travail Microsoft Teams liés à la voix et à la téléphonie. Cela comprend les outils de gestion pour l’attribution des numéros de téléphone, les stratégies de voix et de réunion, ainsi que l’accès total à un ensemble d’outils d’analyse des appels.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gérez tous les aspects de Skype Entreprise Online |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lire les rapports d’utilisation d’Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |
> | microsoft.teams/callQuality/allProperties/read | Lire toutes les données du Tableau de bord de qualité des appels |
> | microsoft.teams/meetings/allProperties/allTasks | Gérer les réunions, notamment les stratégies de réunion, les configurations et les ponts de conférence |
> | microsoft.teams/voice/allProperties/allTasks | Gérer la voix, notamment les stratégies d’appel, ainsi que l’inventaire et l’attribution des numéros de téléphone |

## <a name="teams-communications-support-engineer"></a>Ingénieur de support des communications Teams

Les utilisateurs dans ce rôle peuvent résoudre les problèmes de communication dans Microsoft Teams et Skype Entreprise à l’aide des outils de résolution de problèmes des appels utilisateur dans le centre d’administration Microsoft Teams et Skype Entreprise. Les utilisateurs disposant de ce rôle peuvent visualiser l’intégralité des informations d’enregistrement des appels pour l’ensemble des participants impliqués. Ce rôle n’a pas d'accès pour afficher, créer ou gérer des tickets de support.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gérez tous les aspects de Skype Entreprise Online |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |
> | microsoft.teams/callQuality/allProperties/read | Lire toutes les données du Tableau de bord de qualité des appels |

## <a name="teams-communications-support-specialist"></a>Spécialiste du support des communications Teams

Les utilisateurs dans ce rôle peuvent résoudre les problèmes de communication dans Microsoft Teams et Skype Entreprise à l’aide des outils de résolution de problèmes des appels utilisateur dans le centre d’administration Microsoft Teams et Skype Entreprise. Les utilisateurs disposant de ce rôle ne peuvent visualiser les détails utilisateur d’un appel que pour l’utilisateur qu’ils ont spécifiquement recherché. Ce rôle n’a pas d'accès pour afficher, créer ou gérer des tickets de support.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/authorizationPolicy/standard/read | Lire les propriétés standard des stratégies d’application |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.skypeForBusiness/allEntities/allTasks | Gérez tous les aspects de Skype Entreprise Online |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |
> | microsoft.teams/callQuality/standard/read | Lire les données de base du Tableau de bord de qualité des appels |

## <a name="teams-devices-administrator"></a>Administrateur d’appareils Teams

Les utilisateurs disposant de ce rôle peuvent gérer des [appareils certifiés par Teams](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) à partir du centre d’administration Teams. Ce rôle permet d’afficher tous les appareils en un seul coup d’œil, avec la possibilité de rechercher et de filtrer les appareils. L’utilisateur peut vérifier les détails de chaque appareil, notamment le compte de connexion, la marque et le modèle de l’appareil. L’utilisateur peut modifier les paramètres sur l’appareil et mettre à jour les versions des logiciels. Ce rôle n’accorde pas d’autorisations pour vérifier l’activité Teams et la qualité d’appel de l’appareil.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |
> | microsoft.teams/devices/standard/read | Gérer tous les aspects des appareils certifiés Teams, notamment les stratégies de configuration |

## <a name="usage-summary-reports-reader"></a>Lecteur de rapports de synthèse sur l'utilisation

Les utilisateurs disposant de ce rôle peuvent accéder aux données agrégées au niveau locataire et aux insights associés disponibles dans le centre d'administration Microsoft 365 pour le degré d’utilisation et de productivité, mais n'ont accès à aucune information ni à aucun insight au niveau utilisateur. Dans le centre d'administration Microsoft 365, concernant les deux rapports, nous faisons une distinction entre les données agrégées au niveau locataire et les informations au niveau utilisateur. Ce rôle offre une couche supplémentaire de protection sur les données d'identification des utilisateurs individuels, comme demandé par les clients et par les équipes juridiques.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.office365.network/performance/allProperties/read | Lire toutes les propriétés de performances réseau dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/standard/read | Lire les rapports d’utilisation Office 365 agrégés au niveau locataire |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="user-administrator"></a>Administrateur d'utilisateurs

Les utilisateurs dotés de ce rôle peuvent créer des utilisateurs, gérer tous les aspects des utilisateurs avec certaines restrictions (voir le tableau) et mettre à jour les stratégies d’expiration de mot de passe. De plus, les utilisateurs dotés de ce rôle peuvent créer et gérer tous les groupes. Ce rôle inclut également la possibilité de créer et de gérer des affichages utilisateur, de gérer les tickets de support et de surveiller l’état d’intégrité des services. Les Administrateurs d’utilisateurs ne sont pas autorisés à gérer certaines propriétés utilisateur pour les utilisateurs dans la plupart des rôles administrateur. Les administrateurs ayant ce rôle ne sont pas autorisés à gérer l’authentification multifacteur ou à gérer les boîtes aux lettres partagées. Les rôles qui sont des exceptions à cette restriction sont listés dans le tableau suivant.

| Autorisation d’administrateur d’utilisateurs | Notes |
| --- | --- |
| Créer des utilisateurs et des groupes<br/>Créer et gérer des vues utilisateur<br/>Gérer les tickets de support Office<br/>Mettre à jour les stratégies d’expiration de mot de passe |  |
| Gérer les licences<br/>Gérer toutes les propriétés de l’utilisateur, sauf le nom d’utilisateur principal | S’applique à tous les utilisateurs, notamment les administrateurs |
| Supprimer et restaurer<br/>Désactiver et activer<br/>Gérer toutes les propriétés de l’utilisateur, y compris le nom d’utilisateur principal<br/>Mettre à jour les clés d’appareil (FIDO) | S’applique aux utilisateurs qui ne sont pas administrateurs ou qui ont l’un des rôles suivants :<ul><li>Administrateur du support technique</li><li>Utilisateur sans rôle</li><li>Administrateur d'utilisateurs</li></ul> |
| Invalider les jetons d’actualisation<br/>Réinitialiser le mot de passe | Pour obtenir la liste des rôles pour lesquels un administrateur d’utilisateurs peut réinitialiser les mots de passe et invalider les jetons d’actualisation, consultez [Autorisations de réinitialisation de mot de passe](#password-reset-permissions). |

> [!IMPORTANT]
> Les utilisateurs auxquels ce rôle est assigné peuvent changer les mots de passe des personnes susceptibles d’avoir accès à des informations sensibles ou privées ou à des configurations critiques à l’intérieur et à l’extérieur d’Azure Active Directory. Changer le mot de passe d’un utilisateur peut signifier la capacité d’assumer l’identité et les autorisations de cet utilisateur. Par exemple :
>
>- Les propriétaires d’inscription d’application et d’application d’entreprise, qui peuvent gérer les informations d’identification des applications qu’ils possèdent. Ces applications peuvent disposer d’autorisations privilégiées dans Azure AD et ailleurs qui ne sont pas accordées aux administrateurs utilisateurs. Grâce à ce chemin d’accès, un administrateur utilisateur peut être en mesure d’assumer l’identité d’un propriétaire d’application, puis d’assumer l’identité d’une application privilégiée en mettant à jour les identifiants de celle-ci.
>- Propriétaires d’abonnement Azure, qui peuvent avoir accès à des informations confidentielles ou privées ou à une configuration critique dans Azure.
>- Propriétaires de groupe de sécurité et de groupe Microsoft 365, qui peuvent gérer l’appartenance à un groupe. Ces groupes peuvent accorder l’accès à des informations sensibles ou privées ou à une configuration critique dans Azure AD et ailleurs.
>- Administrateurs d’autres services en dehors d’Azure AD, tels qu’Exchange Online, le Centre de sécurité et de conformité Office et les systèmes de ressources humaines.
>- Les non-administrateurs comme les cadres supérieurs, les conseillers juridiques et les employés des ressources humaines qui peuvent avoir accès à des informations sensibles ou privées.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/contacts/create | Créer des contacts |
> | microsoft.directory/contacts/delete | Supprimer des contacts |
> | microsoft.directory/contacts/basic/update | Mettre à jour les propriétés de base sur les contacts |
> | microsoft.directory/deletedItems.groups/restore | Restaurer les groupes supprimés de manière réversible à l’état d’origine |
> | microsoft.directory/entitlementManagement/allProperties/allTasks | Créer et supprimer des ressources, ainsi que lire et mettre à jour toutes les propriétés dans la gestion des droits d’utilisation Azure AD |
> | microsoft.directory/groups/assignLicense | Attribuer des licences de produits à des groupes pour la gestion des licences basée sur un groupe |
> | microsoft.directory/groups/create | Créer des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/delete | Supprimer des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/hiddenMembers/read | Lire la propriété membres masqués des groupes de Sécurité et des groupes Microsoft 365, notamment les groupes à attribution de rôle |
> | microsoft.directory/groups/reprocessLicenseAssignment | Retraiter les attributions de licence pour la gestion des licences basée sur un groupe |
> | microsoft.directory/groups/restore | Restauration de groupes supprimés |
> | microsoft.directory/groups/basic/update | Actualiser les propriétés de base des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/classification/update | Actualiser les propriétés de classification des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/dynamicMembershipRule/update | Actualiser la règle d’adhésion dynamique des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/groupType/update | Actualiser les propriétés qui affectent le type de groupe des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/members/update | Actualiser la propriété membres des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/onPremWriteBack/update | Mettre à jour les groupes Azure Active Directory pour être réécrits en local avec Azure AD Connect |
> | microsoft.directory/groups/owners/update | Actualiser la propriété propriétaires des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/groups/settings/update | Mettre à jour les paramètres des groupes |
> | microsoft.directory/groups/visibility/update | Actualiser la propriété de visibilité des groupes de Sécurité et des groupes Microsoft 365, à l’exception des groupes à attribution de rôle |
> | microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Créer et supprimer des octrois d’autorisations OAuth 2.0, et lire et mettre à jour toutes les propriétés |
> | microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Mettre à jour les attributions de rôles des principaux de service |
> | microsoft.directory/users/assignLicense | Gérer les licences utilisateur |
> | microsoft.directory/users/create | Ajouter des utilisateurs |
> | microsoft.directory/users/delete | Suppression d’utilisateurs |
> | microsoft.directory/users/disable | Désactiver des utilisateurs |
> | microsoft.directory/users/enable | Activer des utilisateurs |
> | microsoft.directory/users/inviteGuest | Inviter des utilisateurs |
> | microsoft.directory/users/invalidateAllRefreshTokens | Forcer la déconnexion en invalidant les jetons d’actualisation de l’utilisateur |
> | microsoft.directory/users/reprocessLicenseAssignment | Retraiter les attributions de licence pour les utilisateurs |
> | microsoft.directory/users/restore | Restaurer des utilisateurs supprimés |
> | microsoft.directory/users/basic/update | Mettre à jour les propriétés de base sur les utilisateurs |
> | microsoft.directory/users/manager/update | Mettre à jour le gestionnaire pour les utilisateurs |
> | microsoft.directory/users/password/update | Réinitialiser les mots de passe de tous les utilisateurs |
> | microsoft.directory/users/photo/update | Actualiser la photo des utilisateurs |
> | microsoft.directory/users/userPrincipalName/update | Mettre à jour le nom d’utilisateur principal des utilisateurs |
> | microsoft.azure.serviceHealth/allEntities/allTasks | Lire et configurer Azure Service Health |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.office365.serviceHealth/allEntities/allTasks | Lire et configurer Service Health dans le Centre d’administration Microsoft 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="windows-365-administrator"></a>Administrateur Windows 365

Les utilisateurs avec ce rôle ont des autorisations générales sur les ressources Windows 365, lorsque le service est présent. Ce rôle donne aussi la possibilité de gérer les utilisateurs et les appareils afin d’associer la stratégie, ainsi que de créer et de gérer des groupes.

Ce rôle peut créer et gérer des groupes de sécurité, mais il ne dispose pas de droits d’administrateur sur les groupes Microsoft 365. Cela signifie que les administrateurs ne peuvent pas mettre à jour les propriétaires ou les appartenances des groupes Microsoft 365 dans l’organisation. Ils peuvent toutefois gérer le groupe Microsoft 365 qu’ils créent, ce qui fait partie de leurs privilèges d’utilisateur final. Ainsi, tout groupe Microsoft 365 (hormis les groupes de sécurité) qu’ils créent est compté dans leur quota de 250.

Affectez le rôle d’administrateur Windows 365 aux utilisateurs qui doivent effectuer les tâches suivantes :

- Gérer les PC cloud Windows 365 dans Microsoft Endpoint Manager
- Inscrire et gérer les appareils dans Azure AD, notamment l’attribution d’utilisateurs et de stratégies
- Créer et gérer des groupes de sécurité, mais pas des groupes où des rôles peuvent être attribués
- Afficher les propriétés de base dans le Centre d’administration Microsoft 365
- Lire les rapports d’utilisation dans le Centre d’administration Microsoft 365
- Créer et gérer des tickets de support dans Azure AD et le Centre d’administration Microsoft 365

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.directory/devices/create | Créer des appareils (inscrire dans Azure AD) |
> | microsoft.directory/devices/delete | Supprimer des appareils d’Azure AD |
> | microsoft.directory/devices/disable | Désactiver des appareils dans Azure AD |
> | microsoft.directory/devices/enable | Activer des appareils dans Azure AD |
> | microsoft.directory/devices/basic/update | Mettre à jour les propriétés de base sur les appareils |
> | microsoft.directory/devices/extensionAttributeSet1/update | Actualiser les propriétés extensionAttribute1 en extensionAttribute5 sur les appareils |
> | microsoft.directory/devices/extensionAttributeSet2/update | Actualiser les propriétés extensionAttribute6 en extensionAttribute10 sur les appareils |
> | microsoft.directory/devices/extensionAttributeSet3/update | Actualiser les propriétés extensionAttribute11 en extensionAttribute15 sur les appareils |
> | microsoft.directory/devices/registeredOwners/update | Mettre à jour les propriétaires inscrits d’appareils |
> | microsoft.directory/devices/registeredUsers/update | Mettre à jour les utilisateurs inscrits d’appareils |
> | microsoft.directory/groups.security/create | Créer des groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/delete | Supprimer des groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/basic/update | Actualiser les propriétés de base de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/classification/update | Actualiser la propriété de classification de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/dynamicMembershipRule/update | Mettre à jour la règle d'adhésion dynamique des groupes de sécurité, à l'exclusion des groupes attribuables à un rôle |
> | microsoft.directory/groups.security/members/update | Actualiser les membres de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/owners/update | Actualiser les propriétaires de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/groups.security/visibility/update | Actualiser la propriété de visibilité de groupes de Sécurité, à l’exclusion des groupes à attribution de rôle |
> | microsoft.directory/deviceManagementPolicies/standard/read | Lire les propriétés standard sur les stratégies d’application de gestion des appareils |
> | microsoft.directory/deviceRegistrationPolicy/standard/read | Lire les propriétés standard sur les stratégies d’inscription des appareils |
> | microsoft.azure.supportTickets/allEntities/allTasks | Créer et gérer les tickets de support Azure |
> | microsoft.cloudPC/allEntities/allProperties/allTasks | Gérer tous les aspects de Windows 365 |
> | microsoft.office365.supportTickets/allEntities/allTasks | Créer et gérer des demandes de service Microsoft 365 |
> | microsoft.office365.usageReports/allEntities/allProperties/read | Lire les rapports d’utilisation d’Office 365 |
> | microsoft.office365.webPortal/allEntities/standard/read | Lire les propriétés de base sur toutes les ressources dans le Centre d’administration Microsoft 365 |

## <a name="windows-update-deployment-administrator"></a>Administrateur des déploiements de mise à jour Windows

Les utilisateurs disposant de ce rôle peuvent créer et gérer tous les aspects des déploiements de mise à jour Windows par le biais du service des déploiements de mise à jour Windows pour entreprises. Le service des déploiement permet aux utilisateurs de définir des paramètres pour déterminer quand et comment les mises à jour sont déployées, et spécifier les mises à jour qui sont proposées aux groupes d’appareils de leur locataire. Il permet également aux utilisateurs de surveiller la progression des mises à jour.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | microsoft.windows.updatesDeployments/allEntities/allProperties/allTasks | Lire et configurer tous les aspects du Service Windows Update |

## <a name="how-to-understand-role-permissions"></a>Comment comprendre les autorisations des rôles

Le schéma pour les autorisations suit vaguement le format REST de Microsoft Graph :

`<namespace>/<entity>/<propertySet>/<action>`

Par exemple :

`microsoft.directory/applications/credentials/update`

| Élément d’autorisation | Description |
| --- | --- |
| espace de noms | Produit ou service qui expose la tâche, précédé de `microsoft`. Par exemple, toutes les tâches dans Azure AD utilisent l’espace de noms `microsoft.directory`. |
| entité | Fonctionnalité logique ou composant exposé par le service dans Microsoft Graph. Par exemple, Azure AD expose les utilisateurs et les groupes, OneNote expose des ,notes et Exchange expose des boîtes aux lettres et des calendriers. Il existe un mot clé spécial, `allEntities`, pour spécifier toutes les entités dans un espace de noms. Il est souvent utilisé dans des rôles accordent l’accès à un produit entier. |
| propertySet | Propriétés ou aspects spécifiques de l’entité pour laquelle l’accès est accordé. Par exemple, `microsoft.directory/applications/authentication/read` offre la possibilité de lire l’URL de réponse, l’URL de déconnexion et la propriété de flux implicite sur l’objet application dans Azure AD.<ul><li>`allProperties` désigne toutes les propriétés de l’entité, y compris les propriétés privilégiées.</li><li>`standard` désigne des propriétés communes, mais exclut les propriétés privilégiées associées à l’action `read`. Par exemple, `microsoft.directory/user/standard/read` offre la possibilité de lire des propriétés standard telles que le numéro de téléphone et l’adresse e-mail public, mais pas le numéro de téléphone ou l’adresse e-mail secondaires privés utilisés pour l’authentification multifacteur.</li><li>`basic` désigne des propriétés communes, mais exclut les propriétés privilégiées associées à l’action `update`. L’ensemble de propriétés que vous pouvez lire peut différer de ce que vous pouvez mettre à jour. Les mots clés `standard` et `basic` permettent de refléter cela.</li></ul> |
| action | Opération octroyée, généralement créer, lire, mettre à jour ou supprimer (CRUD). Il existe un mot clé spécial, `allTasks`, pour spécifier toutes les fonctionnalités ci-dessus (créer, lire, mettre à jour et supprimer). |

## <a name="deprecated-roles"></a>Rôles déconseillés

Les rôles suivants ne doivent pas être utilisés. Ils sont déconseillés et seront prochainement supprimés d’Azure AD.

* Administrateur de licences ad hoc
* Jonction d’appareils
* Gestionnaires d’appareils
* Utilisateurs d’appareils
* Créateur d’utilisateur vérifié par e-mail
* Administrateur de boîte aux lettres
* Jonction d’appareils d’espace de travail

## <a name="roles-not-shown-in-the-portal"></a>Rôles non affichés dans le portail

Les rôles retournés par PowerShell ou l’API MS Graph ne sont pas tous visibles dans le portail Azure. Le tableau suivant organise ces différences.

Nom de l’API | Nom du portail Azure | Notes
-------- | ------------------- | -------------
Jonction d’appareils | Déprécié | [Documentation sur les rôles déconseillés](#deprecated-roles)
Gestionnaires d’appareils | Déprécié | [Documentation sur les rôles déconseillés](#deprecated-roles)
Utilisateurs d’appareils | Déprécié | [Documentation sur les rôles déconseillés](#deprecated-roles)
Comptes de synchronisation d’annuaires | Non affiché, car il ne doit pas être utilisé | [Documentation sur les comptes de synchronisation d’annuaires](#directory-synchronization-accounts)
Utilisateur invité | Non affiché, car il ne peut pas être utilisé | N/D
Support de niveau 1 partenaire | Non affiché, car il ne doit pas être utilisé | [Documentation sur la prise en charge du niveau 1 de partenaire](#partner-tier1-support)
Support de niveau 2 partenaire | Non affiché, car il ne doit pas être utilisé | [Documentation sur la prise en charge du niveau 2 de partenaire](#partner-tier2-support)
Utilisateur invité restreint | Non affiché, car il ne peut pas être utilisé | N/D
Utilisateur | Non affiché, car il ne peut pas être utilisé | N/D
Jonction d’appareils d’espace de travail | Déprécié | [Documentation sur les rôles déconseillés](#deprecated-roles)

## <a name="password-reset-permissions"></a>Autorisations de réinitialisation de mot de passe

Les en-têtes de colonne représentent les rôles qui peuvent réinitialiser les mots de passe. Les lignes du tableau contiennent les rôles dont le mot de passe peut être réinitialisé.

Le mot de passe peut être réinitialisé | Administrateur de mot de passe | Administrateur du support technique | Administrateur d’authentification | User Admin | Administrateur d’authentification privilégié | Administrateur général
------ | ------ | ------ | ------ | ------ | ------ | ------
Administrateur d’authentification | &nbsp; | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Lecteurs de répertoires | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrateur général | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:\*
Administrateur de groupes | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Inviteur | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrateur du support technique | &nbsp; | :heavy_check_mark: | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Lecteur du Centre de messages | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrateur de mot de passe | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Administrateur d’authentification privilégié | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Administrateur de rôle privilégié | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
Lecteur de rapports | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Utilisateur<br/>(aucun rôle administrateur) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Utilisateur<br/>(aucun rôle administrateur, mais membre d’un groupe à attribution de rôle) | &nbsp; | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark:
User Admin | &nbsp; | &nbsp; | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:
Lecteur de rapports de synthèse sur l'utilisation | &nbsp; | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:

\* Un administrateur général ne peut pas supprimer sa propre attribution d’administrateur général. Cela permet d’éviter une situation dans laquelle une organisation a 0 administrateur général.

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles Azure AD aux groupes](groups-assign-role.md)
- [Comprendre les différents rôles](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Attribuer à un utilisateur le rôle d’administrateur d’un abonnement Azure](../../role-based-access-control/role-assignments-portal-subscription-admin.md)
