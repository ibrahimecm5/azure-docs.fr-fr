---
title: Stratégies d’accès Azure Video Analyzer
description: Cet article explique comment Azure Video Analyzer utilise les jetons JWT dans les stratégies d’accès pour sécuriser les vidéos.
ms.topic: reference
ms.date: 11/04/2021
ms.openlocfilehash: 9d9a0f4db83d132fae5bafb2a25d25075296b4ad
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486132"
---
# <a name="access-policies"></a>Stratégies d'accès

Les stratégies d’accès définissent les autorisations et la durée d’accès à une ressource vidéo Video Analyzer spécifique. Ces stratégies d’accès offrent un contrôle et une flexibilité accrus. En effet, grâce à elles, les jetons JWT tiers (clients non AAD) peuvent autoriser les API clientes à effectuer les opérations suivantes : 

- Accès aux métadonnées de la vidéo. 
- Accès au streaming vidéo. 

## <a name="access-policy-definition"></a>Définition de la stratégie d’accès

```json
"name": "accesspolicyname1", 
"properties": { 
    "role": "Reader", 
    "authentication": { 
        "@type": "#Microsoft.VideoAnalyzer.JwtAuthentication", 
        "issuers": [ 
            "issuer1", 
            "issuer2" 
        ], 
        "audiences": [ 
            "audience1" 
        ], 
        "claims": [ 
            { 
                "name":"claimname1", 
                "value":"claimvalue1" 
            }, 
            { 
                "name":"claimname2", 
                "value":"claimvalue2" 
            } 
        ], 
        "keys": [ 
            { 
                "@type": "#Microsoft.VideoAnalyzer.RsaTokenKey", 
                "alg": "RS256", 
                "kid": "123", 
                "n": "YmFzZTY0IQ==", 
                "e": "ZLFzZTY0IQ==" 
            }, 
            { 
                "@type": "#Microsoft.VideoAnalyzer.EccTokenKey", 
                "alg": "ES256", 
                "kid": "124", 
                "x": "XX==", 
                "y": "YY==" 
            } 
        ] 
    } 
} 
```

> [!NOTE] 
> Un seul type de clé est nécessaire. 

### <a name="roles"></a>Rôles

Seul le rôle de lecteur est pris en charge.

### <a name="issuer-matching-rules"></a>Règles de correspondance de l’émetteur

Plusieurs problèmes peuvent être spécifiés dans la stratégie, un seul émetteur peut être spécifié dans le jeton.  L’émetteur correspond si l’émetteur du jeton fait partie des émetteurs spécifiés dans la stratégie.

### <a name="audience-matching-rules"></a>Règles de correspondance de l’audience

Si la valeur de l’audience est ${System.Runtime.BaseResourceUrlPattern} pour la ressource vidéo, l’audience fournie dans le jeton JWT doit correspondre à l’URL de la ressource de base. Sinon, l’audience du jeton doit correspondre à l’audience de la stratégie d’accès.

### <a name="claims-matching-rules"></a>Règles de correspondance des revendications

Plusieurs revendications peuvent être spécifiées dans la stratégie d’accès et dans le jeton JWT.  Toutes les revendications d’une stratégie d’accès doivent être fournies dans le jeton pour que la validation soit réussie. Toutefois, le jeton JWT peut avoir des revendications supplémentaires qui ne sont pas listées dans la stratégie d’accès.

### <a name="keys"></a>Keys

Deux types de clé sont pris en charge : RSA et ECC.

[RSA](https://wikipedia.org/wiki/RSA_(cryptosystem))

* @type- \#Microsoft.VideoAnalyzer.RsaTokenKey
* alg - Algorithme.  Peut prendre les valeurs 256, 384 ou 512 
* kid - ID de clé
* n - Modulo
* e - Exposant public 

[ECC](https://wikipedia.org/wiki/Elliptic-curve_cryptography)        

* @type- \#Microsoft.VideoAnalyzer.EccTokenKey
* alg - Algorithme.  Peut prendre les valeurs 256, 384 ou 512
* kid - ID de clé
* x - Valeur de la coordonnée.
* y - Valeur de la coordonnée.

### <a name="token-validation-process"></a>Processus de validation des jetons

Les clients doivent créer leurs propres jetons JWT, lesquels sont validés via la méthode suivante :

- Dans la liste des stratégies qui correspondent à l’ID de clé, nous validons :
  - Signature de jeton
  - Expiration du jeton
  - Émetteur
  - Public visé
  - Revendications supplémentaires

### <a name="policy-audience-and-token-matching-examples"></a>Exemples d’audience de stratégie et de correspondance des jetons :

| **Audience de stratégie**                      | URL demandée                         | URL du jeton                            | Résultats |
| ---------------------------------------- | ------------------------------------- | ------------------------------------ | ------ |
| (Tout littéral)                            | (TOUT)                                 | (Correspondance)                              | Accorder  |
| (Tout littéral)                            | (TOUT)                                 | (Aucune correspondance)                          | Deny   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/*                | Accorder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos                   | https://fqdn/videos/{videoName}      | Deny   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/vid*                    | Accorder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/*                | Accorder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{baseVideoName}* | Accorder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}       | https://fqdn/videos/{videoName}      | Accorder  |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{videoName}Suffix | https://fqdn/videos/{videoName}      | Deny   |
| ${System.Runtime.BaseResourceUrlPattern} | https://fqdn/videos/{otherVideoName}  | https://fqdn/videos/{videoName}      | Deny   |

> [!NOTE]  
> Video Analyzer prend en charge 20 stratégies au maximum.  ${System.Runtime.BaseResourceUrlPattern} permet une plus grande flexibilité d’accès à des ressources spécifiques via l’utilisation d’une stratégie d’accès et de plusieurs jetons.  Ces jetons autorisent ensuite l’accès à différentes ressources Video Analyzer en fonction de l’audience. 

## <a name="creating-a-token"></a>Création d’un jeton

Dans cette section, nous allons créer un jeton JWT que nous utiliserons ultérieurement dans l’article.  Nous allons utiliser un exemple d’application qui va générer le jeton JWT et vous fournir tous les champs requis pour créer la stratégie d’accès.

> [!NOTE] 
> Si vous savez comment générer un jeton JWT basé sur un certificat RSA ou ECC, vous pouvez ignorer cette section.

1. Clonez le [dépôt des exemples C# AVA](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp). Ensuite, accédez au dossier d’application JWTTokenIssuer *src/jwt-token-issuer* et recherchez l’application JWTTokenIssuer.
2. Ouvrez Visual Studio Code, puis accédez au dossier dans lequel vous avez téléchargé l’application JWTTokenIssuer. Ce dossier doit contenir le fichier *\*.csproj*.
3. Dans le volet de l’explorateur, accédez au fichier *program.cs*.
4. À la ligne 77, remplacez l’audience par le point de terminaison Video Analyzer, suivi de /videos/\*, de sorte qu’elle ressemble à ceci :

   ```
   https://{Azure Video Analyzer Account ID}.api.{Azure Long Region Code}.videoanalyzer.azure.net/videos/*
   ```

   > [!NOTE] 
   > Le point de terminaison Video Analyzer se trouve dans la section de vue d’ensemble de la ressource Video Analyzer dans le portail Azure.

   :::image type="content" source="media/player-widget/client-api-url.png" alt-text="Capture d’écran montrant le point de terminaison du widget du lecteur.":::
    
5. À la ligne 78, remplacez l’émetteur par la valeur de l’émetteur de votre certificat. Exemple : `https://contoso.com`
6. Enregistrez le fichier .    

   > [!NOTE]
   > Vous pouvez être invité à sélectionner le message `Required assets to build and debug are missing from 'jwt token issuer'. Add them?`. Sélectionnez `Yes`.
   
   :::image type="content" source="media/player-widget/visual-studio-code-required-assets.png" alt-text="Capture d’écran montrant l’invite relative aux ressources nécessaires dans Visual Studio Code.":::
   
7. Ouvrez une fenêtre d’invite de commandes et accédez au dossier contenant les fichiers JWTTokenIssuer. Exécutez les deux commandes suivantes : `dotnet build`, suivie de `dotnet run`. Si vous avez l’extension C# sur Visual Studio Code, vous pouvez également appuyer sur F5 pour exécuter l’application JWTTokenIssuer.

L’application est générée, puis exécutée. Une fois générée, elle crée un certificat auto-signé et génère les informations de jeton JWT à partir de ce certificat. Vous pouvez également exécuter le fichier JWTTokenIssuer.exe qui se trouve dans le dossier de débogage du répertoire à partir duquel JWTTokenIssuer a été généré. L’avantage de l’exécution de l’application est que vous pouvez spécifier des options d’entrée comme suit :

- `JwtTokenIssuer [--audience=<audience>] [--issuer=<issuer>] [--expiration=<expiration>] [--certificatePath=<filepath> --certificatePassword=<password>]`

JWTTokenIssuer crée le jeton JWT et les composants requis suivants :

- `Issuer`, `Audience`, `Key Type`, `Algorithm`, `Key Id`, `RSA Key Modulus`, `RSA Key Exponent`, `Token`

Veillez à copier ces valeurs pour une utilisation ultérieure.


## <a name="creating-an-access-policy"></a>Création d’une stratégie d’accès

Il existe deux façons de créer une stratégie d’accès.

### <a name="in-the-azure-portal"></a>Dans le portail Azure

1. Connectez-vous au portail Azure et accédez au groupe de ressources où se trouve votre compte Video Analyzer.
1. Sélectionnez la ressource Video Analyzer.
1. Sous **Video Analyzer**, sélectionnez **Stratégies d’accès**.

   :::image type="content" source="./media/player-widget/portal-access-policies.png" alt-text="Widget de lecteur – Stratégies d’accès au portail.":::
   
1. Sélectionnez **Nouveau** et entrez les informations suivantes :

   > [!NOTE] 
   > Ces valeurs proviennent de l’application JWTTokenIssuer créée à l’étape précédente.

   - Nom de la stratégie d’accès : n’importe quel nom

   - Émetteur : doit correspondre à l’émetteur du jeton JWT 

   - Audience : audience pour le jeton JWT -- `${System.Runtime.BaseResourceUrlPattern}` est la valeur par défaut.

   - Type de clé : RSA 

   - Algorithme : les valeurs prises en charge sont RS256, RS384, RS512

   - ID de clé : généré à partir de votre certificat. Pour plus d’informations, consultez [Créer un jeton](#creating-a-token).

   - Module clé RSA : généré à partir de votre certificat. Pour plus d’informations, consultez [Créer un jeton](#creating-a-token).

   - Exposant clé RSA : généré à partir de votre certificat. Pour plus d’informations, consultez [Créer un jeton](#creating-a-token).

   :::image type="content" source="./media/player-widget/access-policies-portal.png" alt-text="Widget de lecteur – Portail des stratégies d’accès"::: 
   
1. Sélectionnez **Enregistrer**.
### <a name="create-access-policy-via-api"></a>Créer une stratégie d’accès via l’API

Consultez l’API Azure Resource Manager (ARM) 

## <a name="next-steps"></a>Étapes suivantes

[Vue d'ensemble](overview.md)
