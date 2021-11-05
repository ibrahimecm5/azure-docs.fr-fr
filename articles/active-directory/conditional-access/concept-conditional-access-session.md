---
title: Contrôles de session dans une stratégie d’accès conditionnel - Azure Active Directory
description: Que sont les contrôles de session dans une stratégie d’accès conditionnel Azure AD ?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e33f2c7393a9c7b91dcd6fd9188bd9a89f190215
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050707"
---
# <a name="conditional-access-session"></a>Accès conditionnel : session

Dans une stratégie d’accès conditionnel, un administrateur peut utiliser des contrôles de session pour autoriser des expériences limitées dans des applications cloud spécifiques.

![Stratégie d’accès conditionnel avec un contrôle d’octroi nécessitant l’authentification multifacteur](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>Restrictions appliquées par l’application

Les organisations peuvent utiliser ce contrôle pour demander à Azure AD de transmettre les informations d’appareil aux applications cloud sélectionnées. Les informations d’appareil permettent aux applications cloud de savoir si une connexion est établie à partir d’un appareil conforme ou joint au domaine et de changer l’expérience de la session. Ce contrôle prend uniquement en charge SharePoint Online et Exchange Online comme applications cloud sélectionnées. Lorsque cette option est sélectionnée, l’application cloud utilise les informations de l’appareil pour fournir aux utilisateurs, en fonction de l’état de l’appareil, une expérience limitée (lorsque l’appareil n’est pas géré) ou complète (lorsque l’appareil est géré et conforme).

Pour plus d’informations sur l’utilisation et la configuration de restrictions appliquées par l’application, consultez les articles suivants :

- [Activation d’un accès limité avec SharePoint Online](/sharepoint/control-access-from-unmanaged-devices)
- [Activation d’un accès limité avec Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>Contrôle d’application par accès conditionnel

Le contrôle d’application par accès conditionnel utilise une architecture de proxy inverse et est intégré de manière unique à l’accès conditionnel Azure AD. L’accès conditionnel Azure AD vous permet d’appliquer des contrôles d’accès aux applications de votre organisation en fonction de certaines conditions. Les conditions définissent à qui (utilisateur ou groupe d’utilisateurs), à quoi (quelles applications cloud) et où (quels emplacements et réseaux) s’applique une stratégie d’accès conditionnel. Une fois que vous avez déterminé les conditions, vous pouvez router les utilisateurs vers [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) afin de protéger les données avec un contrôle d’application par accès conditionnel en appliquant des contrôles d’accès et de session.

Le contrôle d’application par accès conditionnel permet de superviser et de contrôler en temps réel les sessions et accès utilisateur aux applications en fonction des stratégies d’accès et de session. Les stratégies d’accès et de session s’utilisent dans le portail Cloud App Security pour affiner davantage les filtres et définir les actions à effectuer sur un utilisateur. Avec les stratégies d’accès et de session, vous pouvez :

- Empêcher l’exfiltration de données : vous pouvez bloquer le téléchargement, les opérations couper, les opérations copier et l’impression des documents à contenu sensible sur les appareils non managés, par exemple.
- Protéger au téléchargement : au lieu de bloquer le téléchargement de documents à contenu sensible, vous pouvez exiger l’étiquetage et la protection des documents avec Azure Information Protection. Cette action garantit que les documents sont protégés et que l’accès utilisateur est limité dans une session potentiellement à risque.
- Empêcher le chargement de fichiers sans étiquette : avant qu’un fichier à contenu sensible puisse être chargé, distribué et utilisé par d’autres personnes, il est important de s’assurer que le fichier est configuré avec l’étiquette et la protection appropriées. Vous pouvez bloquer le chargement des fichiers sans étiquette qui ont du contenu sensible tant que l’utilisateur n’a pas classifié leur contenu.
- Vérifier la conformité des sessions utilisateur (préversion) : les utilisateurs à risque font l’objet d’une supervision lorsqu’ils se connectent à des applications et les actions qu’ils effectuent durant la session sont journalisées. Vous pouvez examiner et analyser le comportement des utilisateurs pour comprendre où et dans quelles conditions les stratégies de session doivent être appliquées à l’avenir.
- Bloquer l’accès (préversion) : vous pouvez bloquer l’accès de façon précise pour des applications et des utilisateurs spécifiques en fonction de plusieurs facteurs de risque. Par exemple, vous pouvez les bloquer s’ils utilisent des certificats clients comme moyen de gérer les appareils.
- Bloquer des activités personnalisées : certaines applications ont des scénarios uniques qui comportent un risque, par exemple, l’envoi de messages avec du contenu sensible dans des applications telles que Microsoft Teams ou Slack. Dans ce genre de scénarios, vous pouvez analyser les messages pour rechercher la présence de contenu sensible et les bloquer en temps réel.

Pour plus d’informations, consultez l’article [Déployer le contrôle d’application par accès conditionnel pour les applications proposées](/cloud-app-security/proxy-deployment-aad).

## <a name="sign-in-frequency"></a>Fréquence de connexion

La fréquence de connexion définit la durée à l’issue de laquelle un utilisateur est invité à se reconnecter lorsqu’il tente d’accéder à une ressource.

Le paramètre de fréquence de connexion fonctionne avec les applications qui ont implémenté les protocoles OAUTH2 ou OIDC conformément aux standards. La plupart des applications natives de Microsoft pour Windows, Mac et Mobile, notamment les applications web suivantes, appliquent le paramètre.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portail d’administration Microsoft 365
- Exchange Online
- SharePoint et OneDrive
- Client web Teams
- Dynamics CRM en ligne
- Portail Azure

Pour plus d’informations, consultez l’article [Configurer la gestion de session d’authentification avec l’accès conditionnel](howto-conditional-access-session-lifetime.md#user-sign-in-frequency).

## <a name="persistent-browser-session"></a>Session de navigateur persistante

Une session de navigateur persistante permet aux utilisateurs de rester connectés après la fermeture et la réouverture de la fenêtre du navigateur.

Pour plus d’informations, consultez l’article [Configurer la gestion de session d’authentification avec l’accès conditionnel](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions).

## <a name="customize-continuous-access-evaluation"></a>Personnaliser l’évaluation continue de l’accès

L’[évaluation continue de l’accès](concept-continuous-access-evaluation.md) est activée automatiquement dans le cadre des stratégies d’accès conditionnel d’une organisation. Pour les organisations qui souhaitent désactiver ou appliquer strictement l’évaluation continue de l’accès, cette configuration est désormais une option dans le contrôle de session au sein de l’accès conditionnel. Les stratégies d’évaluation continue de l’accès peuvent être étendues à tous les utilisateurs ou à des utilisateurs et groupes spécifiques. Les administrateurs peuvent effectuer les sélections suivantes lors de la création d’une stratégie ou de la modification d’une stratégie d’accès conditionnel existante.

- La **désactivation** est effectuée lorsque l’option **Toutes les applications cloud** est sélectionnée, qu’aucune condition n’est sélectionnée et que **Désactiver** est sélectionné sous **Session** > **Personnaliser l’évaluation continue de l’accès** dans une stratégie d’accès conditionnel.
- L’**application stricte** signifie que tout événement critique et toute stratégie seront appliqués en temps réel. Tous les services compatibles avec l’évaluation continue de l’accès obtiennent toujours des jetons d’évaluation continue de l’accès, indépendamment de ce que le client ou l’utilisateur peut demander ou faire. Il existe deux scénarios où l’évaluation continue de l’accès n’entre pas en jeu lorsque le mode d’application stricte est activé :
   - Les clients non compatibles avec l’évaluation continue de l’accès ne doivent pas obtenir de jeton standard pour les services compatibles avec l’évaluation continue de l’accès.
   - Rejeter lorsque l’adresse IP détectée par le fournisseur de ressources ne se trouve pas dans la plage autorisée.

:::image type="content" source="media/concept-conditional-access-session/continuous-access-evaluation-session-controls.png" alt-text="Paramètres d’évaluation continue de l’accès dans une nouvelle stratégie d’accès conditionnel dans le portail Azure." lightbox="media/concept-conditional-access-session/continuous-access-evaluation-session-controls.png":::

## <a name="disable-resilience-defaults-preview"></a>Désactiver les valeurs par défaut de la résilience (préversion)

Pendant une panne, Azure AD étend l’accès aux sessions existantes tout en appliquant les stratégies d’accès conditionnel. Si une stratégie ne peut pas être évaluée, l’accès est déterminé par les paramètres de résilience. 

Si les valeurs par défaut de résilience sont désactivées, l’accès est refusé une fois les sessions existantes expirées. Pour plus d’informations, consultez l’article [Accès conditionnel : Valeurs de résilience par défaut](resilience-defaults.md).

## <a name="next-steps"></a>Étapes suivantes

- [Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

- [Mode Rapport uniquement](concept-conditional-access-report-only.md)
