---
title: Lier votre domaine à votre identificateur décentralisé (DID) (préversion) - Justificatifs vérifiables Azure Active Directory
description: Découvrir la liaison DNS
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 50954d4fef64ffabddf3ec8898ef81a030a77f95
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577343"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did-preview"></a>Lier votre domaine à votre identificateur décentralisé (DID) (préversion)

> [!IMPORTANT]
> Les justificatifs vérifiables Azure Active Directory sont actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Contenu de cet article :
> [!div class="checklist"]
> * Pourquoi avons-nous besoin de lier notre identificateur décentralisé (DID) à notre domaine ?
> * Comment lier des identificateurs décentralisés et des domaines ?
> * Comment fonctionne le processus de liaison de domaine ?
> * Comment fonctionne la logique de domaine verifié/non vérifié ?

## <a name="prerequisites"></a>Prérequis

Pour lier votre DID à votre domaine, vous devez avoir effectué les opérations suivantes.

- Suivre le tutoriel de [prise en main](get-started-verifiable-credentials.md) et l’[ensemble de tutoriels](enable-your-tenant-verifiable-credentials.md) suivant.

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Pourquoi avons-nous besoin de lier notre identificateur décentralisé (DID) à notre domaine ?

Un DID est démarré en tant qu’identificateur qui n’est pas ancré aux systèmes existants. Un DID est utile, car un utilisateur ou une organisation peut le posséder et le contrôler. Si une entité qui interagit avec l’organisation ne sait pas à qui appartient le DID, celui-ci n’est plus aussi utile.

La liaison d’un DID à un domaine résout le problème d’approbation initiale en permettant à toute entité de vérifier par chiffrement la relation entre un DID et un domaine.

## <a name="how-do-we-link-dids-and-domains"></a>Comment lier des identificateurs décentralisés et des domaines ?

Nous établissons un lien entre un domaine et un DID en implémentant une norme ouverte écrite par la Decentralized Identity Foundation appelée [Well-Known DID configuration](https://identity.foundation/.well-known/resources/did-configuration/) (configuration d’un identificateur décentralisé connu). Le service des informations d’identification vérifiables dans Azure Active Directory (Azure AD) permet à votre organisation de faire le lien entre le DID et le domaine en incluant les informations de domaine que vous avez fournies dans votre DID, et en générant le fichier de configuration bien connu :

1. Azure AD utilise les informations de domaine que vous fournissez lors de la configuration de l’organisation pour écrire un point de terminaison de service dans le document DID. Toutes les parties qui interagissent avec votre DID peuvent voir le domaine auquel celui-ci déclare être associé.  

    ```json
    "service": [
      {
        "id": "#linkeddomains",
        "type": "LinkedDomains",
        "serviceEndpoint": {
          "origins": [
            "https://www.contoso.com/"
          ]
        }
      }
    ]
    ```

2. Le service des justificatifs vérifiables dans Azure AD génère une ressource de configuration connue conforme que vous pouvez héberger sur votre domaine. Le fichier de configuration inclut des justificatifs vérifiables auto-émis de credentialType « DomainLinkageCredential » signé avec votre DID qui a une origine de votre domaine. Voici un exemple de document de configuration stocké à l’URL du domaine racine.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

Une fois que vous avez le fichier de configuration connu, vous devez rendre le fichier disponible en utilisant le nom de domaine que vous avez spécifié lors de l’activation de votre AAD pour les justificatifs vérifiables.

* Hébergez le fichier de configuration connu à la racine du domaine.
* N’utilisez pas de redirections.
* Utilisez le protocole HTTPS pour distribuer le fichier de configuration.

>[!IMPORTANT]
>Microsoft Authenticator ne respecte pas les redirections. L’URL spécifiée doit être l’URL de destination finale.

## <a name="user-experience"></a>Expérience utilisateur 

Lorsqu’un utilisateur parcourt un flux d’émission ou présente des justificatifs vérifiables, il doit connaître l’organisation et son identificateur décentralisé. Le domaine de notre portefeuille de justificatifs vérifiables, Microsoft Authenticator, valide la relation d’un identificateur décentralisé avec le domaine présent dans le document DID et présente à l’utilisateur deux expériences différentes en fonction du résultat.

## <a name="verified-domain"></a>Domaine vérifié

Certains éléments doivent être vrais pour que Microsoft Authenticator affiche une icône **Vérifié** :

* L’identificateur décentralisé qui signe la demande d’ID d’ouverture auto-émis (SIOP) doit avoir un point de terminaison de service pour le domaine lié.
* Le domaine racine n’utilise pas de redirection et utilise le protocole HTTPS.
* Le domaine répertorié dans le document DID a une ressource connue pouvant être résolue.
* Les justificatifs vérifiables de la ressource connue sont signés avec le DID utilisé pour signer le SIOP que Microsoft Authenticator a utilisé pour démarrer le flux.

Si toutes les conditions ci-dessus sont remplies, Microsoft Authenticator affiche une page de confirmation de vérification et inclut le domaine ainsi validé.

![Nouvelle demande d’autorisation](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Domaine non vérifié

Si l’une des conditions ci-dessus n’est pas remplie, Microsoft Authenticator affiche un avertissement sur une page entière indiquant que le domaine n’est pas vérifié, que l’utilisateur procède à une transaction risquée et qu’il doit rester vigilant. Nous avons choisi de prendre cet itinéraire pour les raisons suivantes :

* Le DID n’est pas ancré à un domaine.
* La configuration n’a pas été établie correctement.
* Le DID avec lequel l’utilisateur interagit est malveillant et ne peut pas prouver qu’il possède un domaine (puisque ce n’est réellement pas le cas). Pour cette dernière raison, vous devez impérativement lier votre DID au domaine que connaît l’utilisateur afin d’éviter l’apparition du message d’avertissement.

![Avertissement de domaine non vérifié dans l’écran d’ajout d’informations d’identification](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Distribuer une configuration connue

1. Accédez à la page Paramètres dans Justificatifs vérifiables et choisissez **Vérifier ce domaine**

   ![Vérifier ce domaine dans les paramètres](media/how-to-dnsbind/settings-verify.png) 

2. Téléchargez le fichier did-configuration.json affiché dans l’image ci-dessous.

   ![Télécharger le fichier de configuration connu](media/how-to-dnsbind/verify-download.png) 

3. Copiez la valeur linked_did (JWT), ouvrez [https://jwt.ms/](https://www.jwt.ms), collez le JWT et vérifiez que le domaine est correct.

4. Copiez votre DID et ouvrez l’[Explorateur de réseaux ION](https://identity.foundation/ion/explorer) pour vérifier que le même domaine est inclus dans le document DID. 

5. Hébergez la ressource de configuration connue à l’emplacement spécifié. Exemple : `https://www.example.com/.well-known/did-configuration.json`

6. Pour procéder à la validation, testez l’émission ou la présentation avec Microsoft Authenticator. Vérifiez que le paramètre d’Authenticator « Warn about unsafe apps » (Signaler les applications non sécurisées) est activé.

>[!NOTE]
>Par défaut, le paramètre est activé.

Félicitations, vous avez amorcé le web de confiance avec votre identificateur décentralisé !

## <a name="next-steps"></a>Étapes suivantes

Si, lors de l'intégration, vous entrez des informations de domaine erronées, ou si vous décidez de les modifier, vous devrez vous [désinscrire](how-to-opt-out.md). Nous ne prenons pas en charge la modification du document DID pour le moment. Le fait de se désinscrire puis de se réinscrire aura pour effet de créer un nouveau DID.
