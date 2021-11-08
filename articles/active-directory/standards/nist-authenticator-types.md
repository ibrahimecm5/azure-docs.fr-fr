---
title: Types d’authentificateurs NIST et alignement des méthodes Azure Active Directory
description: Explications sur la façon dont les méthodes d’authentification Azure Active Directory s’alignent sur les types d’authentificateurs NIST.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3ee6cc0c913533b83be1eb389e7d7402875e4dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012413"
---
# <a name="nist-authenticator-types-and-aligned-azure-active-directory-methods"></a>Types d’authentificateurs NIST et alignement des méthodes Azure Active Directory

Le processus d’authentification commence lorsqu’un demandeur déclare son contrôle d’un ou plusieurs authentificateurs associés à un abonné. L’abonné peut être une personne ou une autre entité.

| Type d’authentificateur NIST (National Institute of Standards and Technology)| Méthodes d’authentification Azure Active Directory (Azure AD) |
| - | - |
|  Secret mémorisé <br> (quelque chose que vous savez)|  Mot de passe (comptes Cloud)  <br>Mot de passe (fédéré)<br> Mot de passe (synchronisation du hachage de mot de passe)<br>Mot de passe (authentification directe) |
|Secret de recherche <br> (quelque chose que vous avez)| Aucun. Par définition, une recherche de secret est constituée de données qui ne sont pas contenues dans un système. |
|Hors bande <br>(quelque chose que vous avez)| Téléphone (SMS) - non recommandé |
| Mot de passe monofacteur à usage unique <br>‎(quelque chose que vous avez)| Application Microsoft Authenticator (mot de passe à usage unique)  <br>Mot de passe monofacteur à usage unique (par l’intermédiaire de fabricants de mots de passe à usage unique)<sup data-htmlnode="">1</sup> | 
| Mot de passe multifacteur à usage unique<br>(quelque chose que vous avez + quelque chose que vous savez ou quelque chose que vous êtes)| Mot de passe multifacteur à usage unique (par l’intermédiaire de fabricants de mots de passe à usage unique)<sup data-htmlnode="">1</sup>| 
|Logiciel de chiffrement monofacteur<br>(quelque chose que vous avez)|Appareil mobile conforme <br> Application Microsoft Authenticator (notification) <br> Jointure hybride Azure AD<sup data-htmlnode="">2</sup> avec module TPM logiciel<br> Jointure Azure AD<sup data-htmlnode="">2</sup> avec module TPM logiciel |
| Matériel de chiffrement monofacteur <br>(quelque chose que vous avez) | Jointure Azure AD<sup data-htmlnode="">2</sup> avec module TPM matériel <br> Jointure hybride Azure AD<sup data-htmlnode="">2</sup> avec module TPM matériel|
|Logiciel de chiffrement multifacteur<br>(quelque chose que vous avez + quelque chose que vous savez ou quelque chose que vous êtes) | Application Microsoft Authenticator pour iOS (sans mot de passe)<br> Windows Hello Entreprise avec le module TPM logiciel |
|Matériel de chiffrement multifacteur <br>(quelque chose que vous avez + quelque chose que vous savez ou quelque chose que vous êtes) |Application Microsoft Authenticator pour Android (sans mot de passe)<br> Windows Hello Entreprise avec module TPM matériel<br> Carte à puce (fournisseur d’identité fédéré) <br> Clé de sécurité FIDO 2 |


<sup data-htmlnode="">1</sup> Jetons OATH-TOTP SHA-1 de 30 secondes ou de 60 secondes.

<sup data-htmlnode="">2</sup> Pour plus d’informations sur les états des jonctions d’appareils, consultez [Documentation sur les identités des appareils Azure AD](../devices/index.yml). 

## <a name="why-sms-isnt-recommended"></a>Pourquoi le SMS n’est pas recommandé 

Les SMS sont conformes à la norme NIST, mais NIST ne les recommande pas. Les risques liés à l’échange d’appareils, aux modifications de carte SIM, au transfert de numéro et à d’autres comportements peuvent occasionner des problèmes. Si ces actions sont effectuées dans une intention malveillante, elles peuvent déboucher sur une expérience non sécurisée. Bien que les SMS ne soient pas recommandés, ils sont préférables à la seule utilisation d’un mot de passe, car ils demandent plus d’efforts aux hackers. 

## <a name="next-steps"></a>Étapes suivantes 

[Présentation du NIST](nist-overview.md)

[En savoir plus sur les niveaux AAL](nist-about-authenticator-assurance-levels.md)

[Principes fondamentaux de l’authentification](nist-authentication-basics.md)

[Types d'authentificateurs NIST](nist-authenticator-types.md)

[Atteindre le niveau AAL1 NIST avec Azure AD](nist-authenticator-assurance-level-1.md)

[Atteindre le niveau AAL2 NIST avec Azure AD](nist-authenticator-assurance-level-2.md)

[Atteindre le niveau AAL3 NIST avec Azure AD](nist-authenticator-assurance-level-3.md)
