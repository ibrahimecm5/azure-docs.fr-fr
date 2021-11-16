---
title: 'Guide pratique : Se connecter à un service Relais Azure Fluid'
description: Guide pratique pour se connecter à un service Relais Azure Fluid avec la bibliothèque @fluidframework/azure-client.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/deployment/azure-frs/
ms.openlocfilehash: a2093b063e5c2e048bdf0a0a418a8046348032c8
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849574"
---
# <a name="how-to-connect-to-an-azure-fluid-relay-service"></a>Guide pratique : Se connecter à un service Relais Azure Fluid

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

Cet article décrit la procédure permettant de provisionner un service Relais Azure Fluid pour qu’il soit prêt à être utilisé. 

> [!IMPORTANT]
> Pour pouvoir connecter votre application à un serveur Relais Azure Fluid, vous devez [provisionner une ressource de type serveur Relais Azure Fluid](provision-fluid-azure-portal.md) sur votre compte Azure.

Le service Relais Azure Fluid est un service Fluid hébergé dans le cloud. Vous pouvez connecter votre application Fluid à une instance Relais Azure Fluid avec le client `AzureClient` du package `@fluidframework/azure-client`. Le client `AzureClient` gère la logique de connexion de votre [conteneur Fluid](https://fluidframework.com/docs/build/containers/) au service tout en maintenant l’objet conteneur proprement dit indépendant du service. Vous pouvez utiliser une seule instance de ce client pour gérer plusieurs conteneurs.

Les sections ci-dessous expliquent comment utiliser `AzureClient` dans votre propre application.

## <a name="connecting-to-the-service"></a>Connexion au service

Pour vous connecter à une instance Relais Azure Fluid, vous devez d’abord créer un client `AzureClient`. Vous devez fournir des paramètres de configuration, notamment l’ID de locataire, l’URL de l’ordonnanceur et du stockage, ainsi qu’un fournisseur de jetons permettant de générer le jeton JWT (JSON Web Token) qui sera utilisé pour autoriser l’utilisateur actuel sur le service. Le package `@fluidframework/test-client-utils` fournit un `InsecureTokenProvider` qui peut être utilisé à des fins de développement.

> [!CAUTION]
> `InsecureTokenProvider` ne doit être utilisé qu’à des fins de développement, car il **expose la clé secrète du locataire dans votre bundle de code côté client**. Il doit être remplacé par une implémentation de `ITokenProvider` qui extrait le jeton à partir de votre propre service back-end chargé de le signer avec la clé de locataire.

```javascript
const config = {
  tenantId: "myTenantId",
  tokenProvider: new InsecureTokenProvider("myTenantKey", { id: "userId" }),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};

const clientProps = {
  connection: config,
};

const client = new AzureClient(clientProps);
```

Maintenant que vous disposez d’une instance `AzureClient`, vous pouvez commencer à l’utiliser pour créer ou charger des conteneurs Fluid.

### <a name="token-providers"></a>Fournisseurs de jetons

[AzureFunctionTokenProvider](https://github.com/microsoft/FluidFramework/blob/main/packages/framework/azure-client/src/AzureFunctionTokenProvider.ts) est une implémentation de `ITokenProvider` qui garantit que la clé secrète de votre locataire n’est pas exposée dans votre code de bundle côté client. Il prend votre URL de fonction Azure, à laquelle est ajouté `/api/GetAzureToken`, avec l’objet utilisateur actuel. Plus tard, il envoie une requête `GET` à votre fonction Azure en passant tenantId, documentId et userId/userName comme paramètres optionnels.

```javascript
const config = {
  tenantId: "myTenantId",
  tokenProvider: new AzureFunctionTokenProvider(
    "myAzureFunctionUrl" + "/api/GetAzureToken",
    { userId: "userId", userName: "Test User" }
  ),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};

const clientProps = {
  connection: config,
};

const client = new AzureClient(clientProps);
```

### <a name="adding-custom-data-to-tokens"></a>Ajout de données personnalisées à des jetons

L’objet utilisateur peut également contenir des informations supplémentaires facultatives sur l’utilisateur. Par exemple :

```javascript
const userDetails = {
  email: "xyz@outlook.com",
  address: "Redmond",
};

const config = {
  tenantId: "myTenantId",
  tokenProvider: new AzureFunctionTokenProvider(
    "myAzureFunctionUrl" + "/api/GetAzureToken",
    { userId: "UserId", userName: "Test User", additionalDetails: userDetails }
  ),
  orderer: "https://myOrdererUrl",
  storage: "https://myStorageUrl",
};
```

Votre fonction Azure génère le jeton pour l’utilisateur signé avec la clé secrète du locataire et le retourne au client sans exposer le secret proprement dit.

## <a name="managing-containers"></a>Gestion des conteneurs

L’API `AzureClient` expose respectivement les fonctions `createContainer` et `getContainer` pour créer et récupérer des conteneurs. Elles prennent un _schéma de conteneur_ qui définit le modèle de données de conteneur. Pour la fonction `getContainer`, il existe un paramètre supplémentaire correspondant à la valeur `id` du conteneur que vous souhaitez extraire.

Dans le scénario de création de conteneur, vous pouvez appliquer le modèle suivant :

```javascript
const schema = {
  initialObjects: {
    /* ... */
  },
  dynamicObjectTypes: [
    /*...*/
  ],
};
const azureClient = new AzureClient(clientProps);
const { container, services } = await azureClient.createContainer(
  schema
);
const id = await container.attach();
```

L’appel `container.attach()` intervient lorsque le conteneur est réellement connecté au service et enregistré dans son stockage blob. Il retourne un `id` qui constitue l’identificateur unique de cette instance de conteneur.

Les clients qui souhaitent rejoindre la même session collaborative doivent appeler `getContainer` avec le même conteneur `id`.

```javascript
const { container, services } = await azureClient.getContainer(
  id,
  schema
);
```

Pour savoir comment commencer l’enregistrement des journaux émis par Fluid, consultez [Journalisation et télémétrie](https://fluidframework.com/docs/testing/telemetry/).

Le conteneur récupéré contiendra les `initialObjects` comme cela est défini dans le schéma de conteneur. Pour savoir comment établir le schéma et utiliser l’objet `container`, consultez [Modélisation des données](https://fluidframework.com/docs/build/data-modeling/) sur fluidframework.com.

## <a name="getting-audience-details"></a>Récupération des informations sur l’audience

Les appels à `createContainer` et à `getContainer` retournent deux valeurs : un `container` (décrit ci-dessus) et un objet `services`.

Le `container` contient le modèle de données Fluid. Il est indépendant du service. Tout le code que vous écrivez pour cet objet conteneur renvoyé par `AzureClient` est réutilisable avec le client pour un autre service. Par exemple, si vous avez prototypé votre scénario avec `TinyliciousClient`, tout votre code qui interagit avec les objets partagés au sein du conteneur Fluid peut être réemployé pour passer à `AzureClient`.

L’objet `services` contient des données propres au service Relais Azure Fluid. Il comporte une valeur `audience` qui peut permettre de gérer la liste des utilisateurs actuellement connectés au conteneur.

Le code suivant montre comment utiliser l’objet `audience` pour conserver une vue à jour de tous les membres qui se trouvent actuellement dans un conteneur.

```javascript
const { audience } = containerServices;
const audienceDiv = document.createElement("div");

const onAudienceChanged = () => {
  const members = audience.getMembers();
  const self = audience.getMyself();
  const memberStrings = [];
  const useAzure = process.env.FLUID_CLIENT === "azure";

  members.forEach((member) => {
    if (member.userId !== (self ? self.userId : "")) {
      if (useAzure) {
        const memberString = `${member.userName}: {Email: ${member.additionalDetails ? member.additionalDetails.email : ""},
                        Address: ${member.additionalDetails ? member.additionalDetails.address : ""}}`;
        memberStrings.push(memberString);
      } else {
        memberStrings.push(member.userName);
      }
    }
  });
  audienceDiv.innerHTML = `
            Current User: ${self ? self.userName : ""} <br />
            Other Users: ${memberStrings.join(", ")}
        `;
};

onAudienceChanged();
audience.on("membersChanged", onAudienceChanged);
```

`audience` fournit deux fonctions qui retournent des objets `AzureMember` possédant un identifiant utilisateur et un nom d’utilisateur :

- `getMembers` retourne la carte de tous les utilisateurs connectés au conteneur. Ces valeurs changent à chaque fois qu’un membre rejoint ou quitte le conteneur.
- `getMyself` retourne l’utilisateur actuel présent sur ce client.

`audience` émet également des événements lorsque la liste des membres change. `membersChanged` se déclenche pour toutes les modifications de la liste, tandis que `memberAdded` et `memberRemoved` se déclenchent respectivement pour les ajouts et suppressions avec les valeurs `clientId` et `member` modifiées. Une fois qu’un de ces événements est activé, un nouvel appel à `getMembers` retourne la liste des membres mise à jour.

Voici un exemple d’objet `AzureMember` :

```json
{
  "userId": "0e662aca-9d7d-4ff0-8faf-9f8672b70f15",
  "userName": "Test User",
  "connections": [
    {
      "id": "c699c3d1-a4a0-4e9e-aeb4-b33b00544a71",
      "mode": "write"
    },
    {
      "id": "0e662aca-9d7d-4ff0-8faf-9f8672b70f15",
      "mode": "write"
    }
  ],
  "additionalDetails": {
    "email": "xyz@outlook.com",
    "address": "Redmond"
  }
}
```

Outre l’identifiant utilisateur, le nom et des informations supplémentaires, les objets `AzureMember` contiennent également un tableau de `connections`. Si l’utilisateur est connecté à la session avec un seul client, `connections` ne comporte qu’une seule valeur avec l’ID du client et le mode (lecture/écriture ou non). Toutefois, si le même utilisateur est connecté à partir de plusieurs clients (c’est-à-dire à partir de différents appareils ou en ouvrant plusieurs onglets de navigateur avec le même conteneur), `connections` contient plusieurs valeurs pour chaque client. Dans l’exemple de données ci-dessus, un utilisateur portant le nom « Test User » et associé à l’ID « 0e662aca-9d7d-4ff0-8faf-9f8672b70f15 » a actuellement le conteneur ouvert à partir de deux clients différents. Les valeurs du champ `additionalDetails` correspondent aux valeurs fournies dans la génération de jeton `AzureFunctionTokenProvider`.

Ces fonctions et événements peuvent être combinés pour présenter une vue en temps réel des utilisateurs de la session active.

**Félicitations !** Vous avez connecté votre conteneur Fluid au service Relais Azure Fluid et récupéré les informations sur l’utilisateur pour les membres de votre session collaborative.
