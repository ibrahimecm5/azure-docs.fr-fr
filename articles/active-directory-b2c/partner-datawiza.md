---
title: Tutoriel pour configurer Azure Active Directory B2C avec Datawiza
titleSuffix: Azure AD B2C
description: Découvrez comment intégrer l’authentification Azure AD B2C avec Datawiza pour un accès hybride sécurisé
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 7/07/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c33694c624deafa8cdc7ef37ac1dc39d98b5a4f9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044738"
---
# <a name="tutorial-configure-azure-ad-b2c-with-datawiza-to-provide-secure-hybrid-access"></a>Tutoriel : Configurer Azure AD B2C avec Datawiza pour fournir un accès hybride sécurisé

Dans cet exemple de tutoriel, découvrez comment intégrer Azure Active Directory (AD) B2C avec [Datawiza](https://www.datawiza.com/).
[Datawiza Access Broker (DAB)](https://www.datawiza.com/access-broker) de Datawiza active l’authentification unique (SSO) et le contrôle d’accès granulaire, ce qui étend Azure AD B2C pour la protection des applications héritées locales. Cette solution permet aux entreprises de passer rapidement d’une instance héritée à Azure AD B2C sans réécrire d’applications.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, vérifiez que vous disposez des éléments suivants :

- Un abonnement Azure AD Si vous ne disposez d’aucun abonnement, vous pouvez obtenir [un compte gratuit](https://azure.microsoft.com/free/).

- Un [locataire Azure AD B2C](./tutorial-create-tenant.md) lié à votre abonnement Azure.

- [Docker](https://docs.docker.com/get-docker/) est obligatoire pour exécuter DAB. Vos applications peuvent s’exécuter sur n’importe quelle plateforme, par exemple une machine virtuelle et du matériel nu.

- Une application locale que vous allez faire passer d’un système d’identité hérité à Azure AD B2C. Dans cet exemple, DAB est déployé sur le serveur où se trouve l’application. L’application s’exécute sur localhost : 3001 et DAB dirige par proxy le trafic vers l’application via localhost : 9772. Le trafic vers l’application atteint d’abord DAB, puis est dirigé par proxy vers l’application.

## <a name="scenario-description"></a>Description du scénario

L’intégration Datawiza inclut les composants suivants :

- **Azure AD B2C** : serveur d’autorisation chargé de vérifier les informations d’identification de l’utilisateur. Les utilisateurs authentifiés peuvent accéder aux applications locales à l’aide d’un compte local stocké dans l’annuaire Azure AD B2C.

- **Datawiza Access Broker (DAB)**  : l’utilisateur du service se connecte et transmet de manière transparente l’identité aux applications via les en-têtes HTTP.

- **Datawiza Cloud Management Console (DCMC)**  : console de gestion centralisée qui gère DAB. La console DCMC fournit aux administrateurs une interface utilisateur et des API RESTful pour gérer les configurations de DAB et ses stratégies de contrôle d’accès.

Le diagramme d’architecture suivant illustre l’implémentation.

![Image montrant l’architecture d’une intégration Azure AD B2C avec Datawiza pour sécuriser l’accès aux applications hybrides](./media/partner-datawiza/datawiza-architecture-diagram.png)

| Étapes | Description |
|:-------|:---------------|
| 1. | L’utilisateur effectue une demande d’accès à l’application hébergée localement. DAB dirige par proxy la demande de l’utilisateur vers l’application.|
| 2. | DAB vérifie l’état d’authentification de l’utilisateur. S’il ne reçoit pas de jeton de session ou si le jeton de session fourni n’est pas valide, il achemine l’utilisateur vers Azure AD B2C à des fins d’authentification.|
| 3. | Azure AD B2C envoie la demande de l’utilisateur au point de terminaison spécifié lors de l’inscription de l’application DAB dans le locataire Azure AD B2C.|
| 4. | DAB évalue les stratégies d’accès et calcule les valeurs d’attribut à inclure dans les en-têtes HTTP transférés à l’application. Au cours de cette étape, DAB peut appeler le fournisseur d’identité afin de récupérer les informations nécessaires pour définir correctement les valeurs d’en-tête. DAB définit les valeurs d’en-tête et envoie la demande à l’application. |
|5.  | L’utilisateur est maintenant authentifié et a accès à l’application.|

## <a name="onboard-with-datawiza"></a>Intégration avec Datawiza

Pour intégrer votre application locale héritée avec Azure AD B2C, contactez [Datawiza](https://login.datawiza.com/df3f213b-68db-4966-bee4-c826eea4a310/b2c_1a_linkage/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile&client_id=4f011d0f-44d4-4c42-ad4c-88c7bbcd1ac8&redirect_uri=https%3A%2F%2Fconsole.datawiza.com%2Fhome&state=eyJpZCI6Ijk3ZjI5Y2VhLWQ3YzUtNGM5YS1hOWU2LTg1MDNjMmUzYWVlZCIsInRzIjoxNjIxMjg5ODc4LCJtZXRob2QiOiJyZWRpcmVjdEludGVyYWN0aW9uIn0%3D&nonce=08e1b701-6e42-427b-894b-c5d655a9a6b0&client_info=1&x-client-SKU=MSAL.JS&x-client-Ver=1.3.3&client-request-id=3ac285ba-2d4d-4ae5-8dc2-9295ff6047c6&response_mode=fragment).

## <a name="configure-your-azure-ad-b2c-tenant"></a>Configurer votre locataire Azure AD B2C

1. [Inscrivez](https://docs.datawiza.com/idp/azureb2c.html#microsoft-azure-ad-b2c-configuration) votre application web dans le locataire Azure AD B2C.

2. [Configurez un flux utilisateur d’inscription et de connexion](https://docs.datawiza.com/idp/azureb2c.html#configure-a-user-flow) dans le portail de gestion Azure.

  >[!NOTE]
  >Plus tard, vous aurez besoin du nom du locataire, du nom du flux utilisateur, de l’ID client et du secret client lorsque vous configurerez DAB dans la console DCMC.

## <a name="create-an-application-on-dcmc"></a>Créer une application dans la console DCMC

1. [Créez une application](https://docs.datawiza.com/step-by-step/step2.html) et générez une paire de clés de `PROVISIONING_KEY` et `PROVISIONING_SECRET` pour cette application dans la console DCMC.

2. [Configurer Azure AD B2C](https://docs.datawiza.com/tutorial/web-app-azure-b2c.html#part-i-azure-ad-b2c-configuration) en tant que fournisseur d’identité

![Image affichant les valeurs pour configurer le fournisseur d’identité](./media/partner-datawiza/configure-idp.png)

## <a name="run-dab-with-a-header-based-application"></a>Exécuter DAB avec une application basée sur un en-tête

1. Vous pouvez utiliser Docker ou Kubernetes pour exécuter DAB. L’image Docker est nécessaire pour que les utilisateurs puissent créer un exemple d’application basée sur un en-tête. Consultez les instructions sur la façon de [configurer l’intégration DAB et SSO](https://docs.datawiza.com/step-by-step/step3.html) pour obtenir plus de détails et sur la façon de [déployer DAB avec Kubernetes](https://docs.datawiza.com/tutorial/web-app-AKS.html) pour obtenir des instructions spécifiques à Kubernetes. Vous pouvez télécharger et utiliser un exemple d’image Docker `docker-compose.yml file`. Connectez-vous au registre de conteneurs pour télécharger les images de DAB et de l’application basée sur un en-tête. Suivez [ces instructions](https://docs.datawiza.com/step-by-step/step3.html#important-step).
 
   ```yaml
   version: '3'

    services:
    datawiza-access-broker:
    image: registry.gitlab.com/datawiza/access-broker
    container_name: datawiza-access-broker
    restart: always
    ports:
      - "9772:9772"
    environment:
      PROVISIONING_KEY: #############################
      PROVISIONING_SECRET: #############################

    header-based-app:
    image: registry.gitlab.com/datawiza/header-based-app
    container_name: ab-demo-header-app
    restart: always
    environment:
      CONNECTOR: B2C
    ports:
      - "3001:3001"
    ```

 2. Après l’exécution de `docker-compose -f docker-compose.yml up`, l’authentification unique avec Azure AD B2C doit être activée pour l’application basée sur un en-tête. Ouvrez un navigateur et saisissez `http://localhost:9772/`.

3. Une page de connexion Azure AD B2C s’affiche.

## <a name="pass-user-attributes-to-the-header-based-application"></a>Passer des attributs utilisateur à l’application basée sur un en-tête

1. DAB obtient les attributs utilisateur auprès du fournisseur d’identité et peut transmettre les attributs utilisateur à l’application via un en-tête ou un cookie. Consultez les instructions sur la façon de [passer des attributs utilisateur](https://docs.datawiza.com/step-by-step/step4.html), comme l’adresse de messagerie, le prénom et le nom, à l’application basée sur un en-tête. 

2. Une fois les attributs utilisateur correctement configurés, une coche verte doit s’afficher en regard de chaque attribut utilisateur.

 ![Image montrant les attributs utilisateur transmis](./media/partner-datawiza/pass-user-attributes.png)

## <a name="test-the-flow"></a>Tester le flux

1. Accédez à l’URL de l’application locale.

2. DAB doit vous rediriger vers la page que vous avez configurée dans votre flux utilisateur.

3. Sélectionnez le fournisseur d’identité dans la liste affichée sur la page.

4. Une fois que vous êtes redirigé vers le fournisseur d’identité, entrez vos informations d’identification demandées, y compris un jeton MFA Azure AD, si cela vous est demandé.

5. Une fois l’authentification réussie, vous devez être redirigé vers Azure AD B2C, qui transfère la demande d’application à l’URI de redirection DAB.

6. DAB évalue les stratégies, calcule les en-têtes et envoie l’utilisateur à l’application en amont.  

7. Vous devez voir l’application demandée.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les articles suivants :

- [Stratégies personnalisées dans Azure AD B2C](./custom-policy-overview.md)

- [Bien démarrer avec les stratégies personnalisées dans Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
