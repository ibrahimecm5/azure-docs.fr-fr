---
title: 'Guide pratique : Écrire un TokenProvider avec une fonction Azure'
description: Comment écrire un fournisseur de jetons personnalisé en tant que fonction Azure et le déployer.
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
fluid.url: https://fluidframework.com/docs/build/tokenproviders/
ms.openlocfilehash: 80524d6ab2da2e805e1107755cef4cfb367f6d2a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039446"
---
# <a name="how-to-write-a-tokenprovider-with-an-azure-function"></a>Guide pratique : Écrire un TokenProvider avec une fonction Azure

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

Dans l’[infrastructure Fluid](https://fluidframework.com/), les fournisseurs de jetons sont chargés de créer et signer des jetons que `@fluidframework/azure-client` utilise pour adresser des demandes au service Relais Azure Fluid. L’infrastructure Fluid inclut un fournisseur de jetons simple non sécurisé à des fins de développement, judicieusement nommé **InsecureTokenProvider**. Chaque service Fluid doit implémenter un fournisseur de jetons personnalisé en fonction des considérations relatives à l’authentification et à la sécurité du service particulier.

Chaque locataire du service Relais Azure Fluid que vous créez se voit attribuer un **ID de locataire** et sa propre **clé secrète de locataire** unique. La clé secrète est un **secret partagé**. Votre application/service la connaît, et le service Relais Azure Fluid la connaît également. Les fournisseurs de jetons doivent connaître la clé secrète pour signer des demandes, mais cette clé ne peut pas être incluse dans le code client.

## <a name="implement-an-azure-function-to-sign-tokens"></a>Implémentation d’une fonction Azure pour signer des jetons

L’une des options possibles pour générer un fournisseur de jetons sécurisé consiste à créer un point de terminaison HTTPS et une implémentation de TokenProvider qui adresse des demandes HTTPS authentifiées à ce point de terminaison pour récupérer des jetons. Cela vous permet de stocker la *clé secrète du locataire* dans un emplacement sécurisé, comme [Azure Key Vault](../../key-vault/general/overview.md).

La solution complète se compose de deux éléments :

1. Un point de terminaison HTTPS qui accepte les demandes et retourne des jetons Relais Azure Fluid
1. Une implémentation de ITokenProvider qui accepte l’URL d’un point de terminaison, puis adresse des demandes à ce point de terminaison pour récupérer des jetons

### <a name="create-an-endpoint-for-your-tokenprovider-using-azure-functions"></a>Création d’un point de terminaison pour un TokenProvider avec Azure Functions

Les [fonctions Azure Functions](../../azure-functions/functions-overview.md) représentent un moyen rapide de créer un tel point de terminaison HTTPS. Dans l’exemple suivant, ce modèle est implémenté dans une classe nommée **AzureFunctionTokenProvider**. Il accepte l’URL de votre fonction Azure, `userId` et `userName`. Cette implémentation spécifique vous est également fournie en tant qu’exportation à partir du package `@fluidframework/azure-client`.

Cet exemple montre comment créer votre propre **fonction Azure HTTPTrigger** qui récupère le jeton en passant votre clé de locataire.

```typescript
import { AzureFunction, Context, HttpRequest } from "@azure/functions";
import { ScopeType } from "@fluidframework/azure-client";
import { generateToken } from "@fluidframework/azure-service-utils";

// NOTE: retrieve the key from a secure location.
const key = "myTenantKey";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
    // tenantId, documentId, userId and userName are required parameters
    const tenantId = (req.query.tenantId || (req.body && req.body.tenantId)) as string;
    const documentId = (req.query.documentId || (req.body && req.body.documentId)) as string;
    const userId = (req.query.userId || (req.body && req.body.userId)) as string;
    const userName = (req.query.userName || (req.body && req.body.userName)) as string;
    const scopes = (req.query.scopes || (req.body && req.body.scopes)) as ScopeType[];

    if (!tenantId) {
        context.res = {
            status: 400,
            body: "No tenantId provided in query params",
        };
        return;
    }

    if (!key) {
        context.res = {
            status: 404,
            body: `No key found for the provided tenantId: ${tenantId}`,
        };
        return;
    }

    if (!documentId) {
        context.res = {
            status: 400,
            body: "No documentId provided in query params"
        };
        return;
    }

    let user = { name: userName, id: userId };

    // Will generate the token and returned by an ITokenProvider implementation to use with the AzureClient.
    const token = generateToken(
        tenantId,
        documentId,
        key,
        scopes ?? [ScopeType.DocRead, ScopeType.DocWrite, ScopeType.SummaryWrite],
        user
    );

    context.res = {
        status: 200,
        body: token
    };
};

export default httpTrigger;
```

La fonction `generateToken`, qui se trouve dans le package `@fluidframework/azure-service-utils`, génère un jeton pour l’utilisateur connecté avec la clé secrète du locataire. Cela permet de retourner le jeton au client sans exposer le secret. Le jeton est en effet généré côté serveur en utilisant le secret pour fournir un accès délimité au document en question. L’exemple d’ITokenProvider suivant adresse des requêtes HTTP à cette fonction Azure pour récupérer des jetons.

### <a name="deploy-the-azure-function"></a>Déployer la fonction Azure

Les fonctions Azure Functions peuvent être déployées de plusieurs façons. Pour plus d’informations sur le déploiement de fonctions Azure Functions, consultez la section **Déploiement** de la documentation [Azure Functions](../../azure-functions/functions-continuous-deployment.md).

### <a name="implement-the-tokenprovider"></a>Implémentation du TokenProvider

Les TokenProvider peuvent être implémentés de plusieurs façons. Ils doivent cependant mettre en œuvre deux appels d’API distincts : `fetchOrdererToken` et `fetchStorageToken`. Ces API sont respectivement chargées d’extraire les jetons pour les services d’ordonnanceur et de stockage Fluid. Les deux fonctions retournent des `TokenResponse` objets représentant la valeur du jeton. Le runtime de l’Infrastructure Fluid appelle ces deux API en fonction des besoins pour récupérer les jetons.


Pour garantir la sécurité de la clé secrète du locataire, celle-ci est stockée dans un emplacement principal sécurisé et n’est accessible qu’à partir de la fonction Azure. Pour récupérer des jetons, vous devez adresser une demande `GET` ou `POST` à la fonction Azure déployée, en fournissant les valeurs `tenantID`, `documentId` et `userID`/`userName`. La fonction Azure assure le mappage entre l’ID de locataire et la clé secrète d’un locataire pour générer et signer le jeton de manière appropriée.

Dans l’exemple d’implémentation suivant, c’est la bibliothèque [axios](https://www.npmjs.com/package/axios) qui est utilisée pour effectuer des requêtes HTTP. Vous pouvez opter pour d’autres bibliothèques ou d’autres approches afin d’adresser une requête HTTP à partir du code serveur.

```typescript
import { ITokenProvider, ITokenResponse } from "@fluidframework/routerlicious-driver";
import axios from "axios";
import { AzureMember } from "./interfaces";

/**
 * Token Provider implementation for connecting to an Azure Function endpoint for
 * Azure Fluid Relay token resolution.
 */
export class AzureFunctionTokenProvider implements ITokenProvider {
    /**
     * Creates a new instance using configuration parameters.
     * @param azFunctionUrl - URL to Azure Function endpoint
     * @param user - User object
     */
    constructor(
        private readonly azFunctionUrl: string,
        private readonly user?: Pick<AzureMember, "userId" | "userName" | "additionalDetails">,
    ) { }

    public async fetchOrdererToken(tenantId: string, documentId?: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    public async fetchStorageToken(tenantId: string, documentId: string): Promise<ITokenResponse> {
        return {
            jwt: await this.getToken(tenantId, documentId),
        };
    }

    private async getToken(tenantId: string, documentId: string | undefined): Promise<string> {
        const response = await axios.get(this.azFunctionUrl, {
            params: {
                tenantId,
                documentId,
                userId: this.user?.userId,
                userName: this.user?.userName,
                additionalDetails: this.user?.additionalDetails,
            },
        });
        return response.data as string;
    }
}
```
## <a name="see-also"></a>Voir aussi

- [Ajouter des données personnalisées à un jeton d’authentification](connect-fluid-azure-service.md#adding-custom-data-to-tokens)
- [Guide pratique pour déployer des applications Fluid avec Azure Static Web Apps](deploy-fluid-static-web-apps.md)
