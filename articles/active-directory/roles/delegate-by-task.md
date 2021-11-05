---
title: Déléguer des rôles par tâche administrateur – Azure Active Directory | Microsoft Docs
description: Rôles à déléguer pour des tâches d’identité dans Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: reference
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1b237e8347a6a9238dfef505993410413537324
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057188"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Rôles d'administrateur par tâche administrateur dans Azure Active Directory

Dans cet article, vous trouverez les informations nécessaires pour restreindre les autorisations d’administrateur d’un utilisateur en attribuant des rôles moins privilégiés dans Azure Active Directory (Azure AD). Il contient des tâches d’administrateur organisées par domaine de fonctionnalités et le rôle moins privilégié nécessaire pour effectuer chaque tâche, ainsi que d’autres rôles non-administrateur général qui peuvent effectuer la tâche.

## <a name="application-proxy"></a>Proxy d’application

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Configurer l’application de proxy d’application | Administrateur d’application |  |
> | Configurer les propriétés du groupe de connecteurs | Administrateur d’application |  |
> | Créer l’inscription de l’application quand la capacité est désactivée pour tous les utilisateurs | Développeur d’applications | Administrateur d'applications cloud<br/>Administrateur d’application |
> | Créer un groupe de connecteurs | Administrateur d’application |  |
> | Supprimer un groupe de connecteurs | Administrateur d’application |  |
> | Désactiver le proxy d’application | Administrateur d’application |  |
> | Télécharger le service de connecteur | Administrateur d’application |  |
> | Lire toute la configuration | Administrateur d’application |  |

## <a name="external-identitiesb2c"></a>Identités externes/B2C

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Créer des annuaires Azure AD B2C | Tous les utilisateurs non invités ([consultez la documentation](../fundamentals/users-default-permissions.md)) |  |
> | Créer des applications B2C | Administrateur général |  |
> | Créer des applications d’entreprise | Administrateur d'applications cloud | Administrateur d’application |
> | Créer, lire, mettre à jour et supprimer des stratégies B2C | Administrateur de stratégies B2C IEF |  |
> | Créer, lire, mettre à jour et supprimer des fournisseurs d’identité | Administrateur de fournisseurs d’identité externes |  |
> | Créer, lire, mettre à jour et supprimer des flux utilisateur de réinitialisation de mot de passe | Administrateur de flux d’utilisateurs ID externe |  |
> | Créer, lire, mettre à jour et supprimer des flux utilisateur de modification de profil | Administrateur de flux d’utilisateurs ID externe |  |
> | Créer, lire, mettre à jour et supprimer des flux utilisateur de connexion | Administrateur de flux d’utilisateurs ID externe |  |
> | Créer, lire, mettre à jour et supprimer des flux utilisateur d’inscription |Administrateur de flux d’utilisateurs ID externe |  |
> | Créer, lire, mettre à jour et supprimer des attributs utilisateur | Administrateur d’attribut de flux d’utilisateurs ID externe |  |
> | Créer, lire, mettre à jour et supprimer des utilisateurs | Administrateur d'utilisateurs |  |
> | Configurer les paramètres de collaboration B2B externe | Administrateur général |  |
> | Lire toute la configuration | Lecteur général |  |
> | Lire les journaux d’audit B2C | Lecteur général ([consultez la documentation](../../active-directory-b2c/faq.yml)) |  |

> [!NOTE]
> Des administrateurs généraux Azure AD B2C n’ont pas les mêmes autorisations que des administrateurs généraux Azure AD. Si vous disposez de privilèges d’administrateur général Azure AD B2C, vérifiez que vous vous trouvez dans un annuaire Azure AD B2C et non un annuaire Azure AD.

## <a name="company-branding"></a>Marque de société

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Configurer la marque de la société | Administrateur général |  |
> | Lire toute la configuration | Lecteurs d’annuaires | Rôle d’utilisateur par défaut ([consultez la documentation](../fundamentals/users-default-permissions.md)) |

## <a name="company-properties"></a>Propriétés de l’entreprise

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Configurer les propriétés de l’entreprise | Administrateur général |  |

## <a name="connect"></a>Se connecter

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Authentification directe | Administrateur général |  |
> | Lire toute la configuration | Lecteur général | Administrateur général |
> | Authentification unique homogène | Administrateur général |  |

## <a name="cloud-provisioning"></a>Provisionnement cloud

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Authentification directe | Administrateur d’identité hybride |  |
> | Lire toute la configuration | Lecteur général | Administrateur d’identité hybride |
> | Authentification unique homogène | Administrateur d’identité hybride |  |

## <a name="connect-health"></a>Connect Health

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Ajouter ou supprimer des services | Propriétaire ([consultez la documentation](../hybrid/how-to-connect-health-operations.md)) |  |
> | Appliquer des correctifs à une erreur de synchronisation | Contributeur ([consultez la documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Propriétaire |
> | Configurer les notifications | Contributeur ([consultez la documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Propriétaire |
> | Configurer les paramètres | Propriétaire ([consultez la documentation](../hybrid/how-to-connect-health-operations.md)) |  |
> | Configurer les notifications de synchronisation | Contributeur ([consultez la documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Propriétaire |
> | Lire les rapports de sécurité ADFS | Lecteur de sécurité | Contributeur<br/>Propriétaire
> | Lire toute la configuration | Lecteur ([consultez la documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Contributeur<br/>Propriétaire |
> | Lire les erreurs de synchronisation | Lecteur ([consultez la documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Contributeur<br/>Propriétaire |
> | Lire les services de synchronisation | Lecteur ([consultez la documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Contributeur<br/>Propriétaire |
> | Afficher les métriques et les alertes | Lecteur ([consultez la documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Contributeur<br/>Propriétaire |
> | Afficher les métriques et les alertes | Lecteur ([consultez la documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Contributeur<br/>Propriétaire |
> | Afficher les métriques et les alertes de service de synchronisation | Lecteur ([consultez la documentation](../fundamentals/users-default-permissions.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)) | Contributeur<br/>Propriétaire |

## <a name="custom-domain-names"></a>Noms de domaine personnalisés

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Gérer des domaines | Administrateur de nom de domaine |  |
> | Lire toute la configuration | Lecteurs d’annuaires | Rôle d’utilisateur par défaut ([consultez la documentation](../fundamentals/users-default-permissions.md)) |

## <a name="domain-services"></a>Services de domaine

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Créer une instance Azure AD Domain Services | Administrateur général |  |
> | Effectuer toutes les tâches Azure AD Domain Services | Groupe Administrateurs de contrôleur de domaine Azure AD ([consultez la documentation](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-a-managed-domain)) |  |
> | Lire toute la configuration | Lecteur sur l’abonnement Azure contenant le service AD DS |  |

## <a name="devices"></a>Appareils

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Désactiver un appareil | Administrateur d’appareil cloud |  |
> | Activer un appareil | Administrateur d’appareil cloud |  |
> | Lire la configuration de base | Rôle d’utilisateur par défaut ([consultez la documentation](../fundamentals/users-default-permissions.md)) |  |
> | Lire les clés BitLocker | Lecteur de sécurité | Administrateur de mots de passe<br/>Administrateur de sécurité |

## <a name="enterprise-applications"></a>Applications d’entreprise

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Donner son consentement pour les autorisations déléguées | Administrateur d'applications cloud | Administrateur d’application |
> | Donner son consentement à des autorisations d’application n’incluant pas Microsoft Graph | Administrateur d'applications cloud | Administrateur d’application |
> | Donner son consentement à des autorisations d’application pour Microsoft Graph | Administrateur de rôle privilégié |  |
> | Donner son consentement pour les applications propriétaires de données | Rôle d’utilisateur par défaut ([consultez la documentation](../fundamentals/users-default-permissions.md)) |  |
> | Créer une application d’entreprise | Administrateur d'applications cloud | Administrateur d’application |
> | Gérer le proxy d’application | Administrateur d’application |  |
> | Gérer les paramètres utilisateur | Administrateur général |  |
> | Lire la révision d’accès d’un groupe ou d’une application | Lecteur de sécurité | Security Administrator<br/>Administrateur d'utilisateurs |
> | Lire toute la configuration | Rôle d’utilisateur par défaut ([consultez la documentation](../fundamentals/users-default-permissions.md)) |  |
> | Mettre à jour les attributions d’application d’entreprise | Propriétaire d’une application d’entreprise ([consultez la documentation](../fundamentals/users-default-permissions.md)) | Administrateur d'applications cloud<br/>Administrateur d’application |
> | Mettre à jour les propriétaires d’application d’entreprise | Propriétaire d’une application d’entreprise ([consultez la documentation](../fundamentals/users-default-permissions.md)) | Administrateur d'applications cloud<br/>Administrateur d’application |
> | Mettre à jour les propriétés d’une application d’entreprise | Propriétaire d’une application d’entreprise ([consultez la documentation](../fundamentals/users-default-permissions.md)) | Administrateur d'applications cloud<br/>Administrateur d’application |
> | Mettre à jour le provisionnement d’une application d’entreprise | Propriétaire d’une application d’entreprise ([consultez la documentation](../fundamentals/users-default-permissions.md)) | Administrateur d'applications cloud<br/>Administrateur d’application |
> | Mettre à jour le libre-service d’une application d’entreprise | Propriétaire d’une application d’entreprise ([consultez la documentation](../fundamentals/users-default-permissions.md)) | Administrateur d'applications cloud<br/>Administrateur d’application |
> | Mettre à jour les propriétés de l’authentification unique | Propriétaire d’une application d’entreprise ([consultez la documentation](../fundamentals/users-default-permissions.md)) | Administrateur d'applications cloud<br/>Administrateur d’application |

## <a name="entitlement-management"></a>Gestion des droits d’utilisation

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Ajouter des ressources à un catalogue | Administrateur Identity Governance | Avec la gestion des droits d’utilisation, vous pouvez déléguer cette tâche au propriétaire du catalogue ([voir la documentation](../governance/entitlement-management-catalog-create.md#add-more-catalog-owners)) |
> | Ajouter des sites SharePoint Online au catalogue | Administrateur SharePoint |  |

## <a name="groups"></a>Groupes

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Affecter une licence | Administrateur d'utilisateurs |  |
> | Créer un groupe | Administrateur de groupes | Administrateur d'utilisateurs |
> | Créer, mettre à jour ou supprimer la révision d’accès d’un groupe ou d’une application | Administrateur d'utilisateurs |  |
> | Gérer l’expiration des groupes | Administrateur d'utilisateurs |  |
> | Gérer les paramètres de groupe | Administrateur de groupes | Administrateur d'utilisateurs |
> | Lire toutes les configurations (à l’exception de l’appartenance masquée) | Lecteurs d’annuaires | Rôle d’utilisateur par défaut ([consultez la documentation](../fundamentals/users-default-permissions.md)) |
> | Lire l’appartenance masquée | Membre de groupe | Propriétaire de groupe<br/>Administrateur de mots de passe<br/>Administrateur Exchange<br/>Administrateur SharePoint<br/>Administrateur Teams<br/>Administrateur d'utilisateurs |
> | Lire l’appartenance des groupes avec une appartenance masquée | Administrateur du support technique | Administrateur d'utilisateurs<br/>Administrateur Teams |
> | Révoquer une licence | Administrateur de licence | Administrateur d'utilisateurs |
> | Mettre à jour l’appartenance à un groupe | Propriétaire de groupe ([consultez la documentation](../fundamentals/users-default-permissions.md)) | Administrateur d'utilisateurs |
> | Mettre à jour les propriétaires de groupe | Propriétaire de groupe ([consultez la documentation](../fundamentals/users-default-permissions.md)) | Administrateur d'utilisateurs |
> | Mettre à jour les propriétés de groupe | Propriétaire de groupe ([consultez la documentation](../fundamentals/users-default-permissions.md)) | Administrateur d'utilisateurs |
> | Supprimer un groupe | Administrateur de groupes | Administrateur d'utilisateurs |

## <a name="identity-protection"></a>Identity Protection

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Configurer des notifications d’alerte| Security Administrator |  |
> | Configurer et activer ou désactiver la stratégie MFA| Security Administrator |  |
> | Configurer et activer ou désactiver la stratégie de connexion à risque| Security Administrator |  |
> | Configurer et activer ou désactiver la stratégie d’utilisateur à risque | Security Administrator |  |
> | Configurer des synthèses hebdomadaires | Security Administrator |  |
> | Ignorer toutes les détections de risques | Security Administrator |  |
> | Corriger ou ignorer des vulnérabilités | Security Administrator |  |
> | Lire toute la configuration | Lecteur de sécurité |  |
> | Lire toutes les détections de risques | Lecteur de sécurité |  |
> | Lire les vulnérabilités | Lecteur de sécurité |  |

## <a name="licenses"></a>Licences

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Affecter une licence | Administrateur de licence | Administrateur d'utilisateurs |
> | Lire toute la configuration | Lecteurs d’annuaires | Rôle d’utilisateur par défaut ([consultez la documentation](../fundamentals/users-default-permissions.md)) |
> | Révoquer une licence | Administrateur de licence | Administrateur d'utilisateurs |
> | Tester ou acheter un abonnement | Administrateur de facturation |  |

## <a name="monitoring---audit-logs"></a>Supervision - Journaux d’audit

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Lire les journaux d’audit | Lecteur de rapports | Lecteur de sécurité<br/>Security Administrator |

## <a name="monitoring---sign-ins"></a>Surveillance - Connexions

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Lire les journaux d’activité de connexion | Lecteur de rapports | Lecteur de sécurité<br/>Security Administrator<br/> Lecteur général |

## <a name="multi-factor-authentication"></a>Authentification multifacteur

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Supprimer tous les mots de passe d’application existants qui ont été générés par les utilisateurs sélectionnés | Administrateur général |  |
> | Désactiver MFA | Administrateur d’authentification (par le biais de PowerShell) | Administrateur d’authentification privilégié (par le biais de PowerShell) |
> | Activer la MFA | Administrateur d’authentification (par le biais de PowerShell) | Administrateur d’authentification privilégié (par le biais de PowerShell) | 
> | Gérer les paramètres du service MFA | Administrateur de la stratégie d’authentification |  |
> | Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact | Administrateur d’authentification |  |
> | Restaurer l’authentification multifacteur pour tous les appareils mémorisés  | Administrateur d’authentification |  |

## <a name="mfa-server"></a>Serveur MFA

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Blocage/déblocage des utilisateurs | Administrateur de la stratégie d’authentification |  |
> | Configurer le verrouillage de compte | Administrateur de la stratégie d’authentification |  |
> | Configurer les règles de mise en cache | Administrateur de la stratégie d’authentification |  |
> | Configurer l’alerte de fraude | Administrateur de la stratégie d’authentification |  |
> | Configurer les notifications | Administrateur de la stratégie d’authentification |  |
> | Configurer un contournement à usage unique | Administrateur de la stratégie d’authentification |  |
> | Configurer les paramètres d’appel téléphonique | Administrateur de la stratégie d’authentification |  |
> | Configurer des fournisseurs | Administrateur de la stratégie d’authentification |  |
> | Configurez les paramètres du serveur | Administrateur de la stratégie d’authentification |  |
> | Lire un rapport d’activité | Lecteur général |  |
> | Lire toute la configuration | Lecteur général |  |
> | Lire l’état du serveur | Lecteur général |  |

## <a name="organizational-relationships"></a>Relations organisationnelles

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Gérer les fournisseurs d’identité | Administrateur de fournisseurs d’identité externes |  |
> | Gérer les paramètres | Administrateur général |  |
> | Gérer les conditions d’utilisation | Administrateur général |  |
> | Lire toute la configuration | Lecteur général |  |

## <a name="password-reset"></a>Réinitialisation du mot de passe

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Configurer les méthodes d’authentification | Administrateur général |  |
> | Configurer la personnalisation | Administrateur général |  |
> | Configurer la notification | Administrateur général |  |
> | Configurer l’intégration locale | Administrateur général |  |
> | Configurer les propriétés de la réinitialisation de mot de passe | Administrateur d'utilisateurs | Administrateur général |
> | Configurer l’inscription | Administrateur général |  |
> | Lire toute la configuration | Security Administrator | Administrateur d'utilisateurs |

## <a name="privileged-identity-management"></a>Privileged Identity Management

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Affecter des utilisateurs aux rôles | Administrateur de rôle privilégié |  |
> | Configurer les paramètres de rôle | Administrateur de rôle privilégié |  |
> | Afficher l’activité d’audit | Lecteur de sécurité |  |
> | Afficher les appartenances aux rôles | Lecteur de sécurité |  |

## <a name="roles-and-administrators"></a>Rôles et administrateurs

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Gérer les attributions de rôles | Administrateur de rôle privilégié |  |
> | Lire la révision d’accès d’un rôle d’Azure AD  | Lecteur de sécurité | Security Administrator<br/>Administrateur de rôle privilégié |
> | Lire toute la configuration | Rôle d’utilisateur par défaut ([consultez la documentation](../fundamentals/users-default-permissions.md)) |  |

## <a name="security---authentication-methods"></a>Sécurité - Méthodes d’authentification

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Configurer les méthodes d’authentification | Administrateur général |  |
> | Configurer la protection par mot de passe | Administrateur de sécurité |  |
> | Configurer le verrouillage intelligent | Security Administrator |
> | Lire toute la configuration | Lecteur général |  |

## <a name="security---conditional-access"></a>Sécurité – Accès conditionnel

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Configurer des adresses IP approuvées MFA | Administrateur de l’accès conditionnel |  |
> | Créer des contrôles personnalisés | Administrateur de l’accès conditionnel | Security Administrator |
> | Créer des emplacements nommés | Administrateur de l’accès conditionnel | Security Administrator |
> | Création des stratégies | Administrateur de l’accès conditionnel | Security Administrator |
> | Créer des conditions d’utilisation | Administrateur de l’accès conditionnel | Security Administrator |
> | Créer un certificat de connectivité VPN | Administrateur de l’accès conditionnel | Security Administrator |
> | Supprimer une stratégie classique | Administrateur de l’accès conditionnel | Security Administrator |
> | Supprimer des conditions d’utilisation | Administrateur de l’accès conditionnel | Security Administrator |
> | Supprimer un certificat de connectivité VPN | Administrateur de l’accès conditionnel | Security Administrator |
> | Désactiver une stratégie classique | Administrateur de l’accès conditionnel | Security Administrator |
> | Gérer des contrôles personnalisés | Administrateur de l’accès conditionnel | Security Administrator |
> | Gérer des emplacements nommés | Administrateur de l’accès conditionnel | Security Administrator |
> | Gérer les conditions d’utilisation | Administrateur de l’accès conditionnel | Security Administrator |
> | Lire toute la configuration | Lecteur de sécurité | Security Administrator |
> | Lire des emplacements nommés | Lecteur de sécurité | Administrateur de l’accès conditionnel<br/>Security Administrator |

## <a name="security---identity-security-score"></a>Sécurité - Score de sécurité d’identité

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles | 
> | ---- | --------------------- | ---------------- |
> | Lire toute la configuration | Lecteur de sécurité | Security Administrator |
> | Lire un score de sécurité | Lecteur de sécurité | Security Administrator |
> | Mettre à jour l’état d’un événement | Administrateur de sécurité |  |

## <a name="security---risky-sign-ins"></a>Sécurité - Connexions à risque

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Lire toute la configuration | Lecteur de sécurité |  |
> | Lire les connexions à risque | Lecteur de sécurité |  |

## <a name="security---users-flagged-for-risk"></a>Sécurité - Utilisateurs avec indicateur de risque

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Ignorer tous les événements | Security Administrator |  |
> | Lire toute la configuration | Lecteur de sécurité |  |
> | Lire les utilisateurs avec indicateur de risque | Lecteur de sécurité |  |

## <a name="users"></a>Utilisateurs

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Ajouter un utilisateur à un rôle d’annuaire | Administrateur de rôle privilégié |  |
> | Ajouter un utilisateur à un groupe | Administrateur d'utilisateurs |  |
> | Affecter une licence | Administrateur de licence | Administrateur d'utilisateurs |
> | Créer un utilisateur invité | Inviteur d’invités | Administrateur d'utilisateurs |
> | Réinitialiser l’invitation d’un utilisateur invité | Administrateur d'utilisateurs | Administrateur général |
> | Créer un utilisateur | Administrateur d'utilisateurs |  |
> | Suppression d’utilisateurs | Administrateur d'utilisateurs |  |
> | Invalider les jetons d’actualisation des administrateurs limités (consultez la documentation) | Administrateur d'utilisateurs |  |
> | Invalider les jetons d’actualisation des non-administrateurs (consultez la documentation) | Administrateur de mots de passe | Administrateur d'utilisateurs |
> | Invalider les jetons d’actualisation des administrateurs privilégiés (consultez la documentation) | Administrateur d’authentification privilégié |  |
> | Lire la configuration de base | Rôle d’utilisateur par défaut ([consultez la documentation](../fundamentals/users-default-permissions.md) |  |
> | Réinitialiser le mot de passe pour les administrateurs limités (consultez la documentation) | Administrateur d'utilisateurs |  |
> | Réinitialiser le mot de passe des non-administrateurs (consultez la documentation) | Administrateur de mots de passe | Administrateur d'utilisateurs |
> | Réinitialiser le mot de passe des administrateurs privilégiés | Administrateur d’authentification privilégié |  |
> | Révoquer une licence | Administrateur de licence | Administrateur d'utilisateurs |
> | Mettre à jour toutes les propriétés, sauf le nom d’utilisateur principal | Administrateur d'utilisateurs |  |
> | Mettre à jour le nom d’utilisateur principal pour les administrateurs limités (consultez la documentation) | Administrateur d'utilisateurs |  |
> | Mettre à jour la propriété Nom d’utilisateur principal sur les administrateurs privilégiés (consultez la documentation) | Administrateur général |  |
> | Mettre à jour les paramètres utilisateur | Administrateur général |  |
> | Mettre à jour les méthodes d’authentification | Administrateur d’authentification | Administrateur d’authentification privilégié<br/>Administrateur général |

## <a name="support"></a>Support

> [!div class="mx-tableFixed"]
> | Tâche | Rôle moins privilégié | Autres rôles |
> | ---- | --------------------- | ---------------- |
> | Envoyer un ticket de support | Administrateur de support de service | Administrateur d’application<br/>Administrateur Azure Information Protection<br/>Administrateur de facturation<br/>Administrateur d'applications cloud<br/>Administrateur de conformité<br/>Administrateur Dynamics 365<br/>Administrateur Desktop Analytics<br/>Administrateur Exchange<br/>Administrateur Intune<br/>Administrateur de mots de passe<br/>Administrateur Power BI<br/>Administrateur d’authentification privilégié<br/>Administrateur SharePoint<br/>Administrateur Skype Entreprise<br/>Administrateur Teams<br/>Administrateur des communications Teams<br/>Administrateur d'utilisateurs<br/>Administrateur du service Analyse du temps de travail |

## <a name="next-steps"></a>Étapes suivantes

* [Guide pratique pour attribuer ou supprimer des rôles d’administrateur Azure AD](manage-roles-portal.md)
* [Rôles intégrés Azure AD](permissions-reference.md)
