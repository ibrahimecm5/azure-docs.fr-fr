---
title: 'Tutoriel : Mapper un nom DNS personnalisé existant'
description: Découvrez comment ajouter un nom de domaine DNS (domaine personnel) à une application web, au back-end d’une application mobile ou à une application API dans Azure App Service.
keywords: app service, azure app service, mappage de domaine, nom de domaine, domaine existant, nom d’hôte, domaine personnel
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.topic: tutorial
ms.date: 05/27/2021
ms.custom: mvc, seodec18, devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: 607a1e8e12fa3840235e3158e98fb7dfb34a73fe
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447813"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>Tutoriel : Mapper un nom DNS personnalisé existant à Azure App Service

[Azure App Service](overview.md) offre un service d’hébergement web hautement évolutif appliquant des mises à jour correctives automatiques. Ce tutoriel vous montre comment mapper un nom DNS personnalisé existant à App Service.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Mapper un sous-domaine à l’aide d’un [enregistrement CNAME](https://en.wikipedia.org/wiki/CNAME_record).
> * Mapper un domaine racine à l’aide d’un [enregistrement A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A).
> * Mapper un [domaine générique](https://en.wikipedia.org/wiki/Wildcard_DNS_record) à l’aide d’un enregistrement CNAME.
> * Rediriger l’URL par défaut vers un répertoire personnalisé


## <a name="1-prepare-your-environment"></a>1. Préparation de votre environnement

* [Créez une application App Service](./index.yml), ou utilisez une application créée pour un autre didacticiel.
* Vérifiez que vous pouvez modifier les enregistrements DNS de votre domaine personnalisé. Si vous n’avez pas encore de domaine personnalisé, vous pouvez [acheter un domaine App Service](manage-custom-dns-buy-domain.md).

    > [!NOTE]
    > Pour modifier les enregistrements DNS, vous devez accéder au registre DNS de votre fournisseur de domaine, comme GoDaddy. Par exemple, pour ajouter des entrées DNS pour `contoso.com` et `www.contoso.com`, vous devez pouvoir configurer les paramètres DNS du domaine racine `contoso.com`.


## <a name="2-prepare-the-app"></a>2. Préparer l’application

Pour mapper un nom DNS personnalisé à une application web, le [plan App Service](overview-hosting-plans.md) de l’application web doit être un niveau payant (pas **Gratuit (F1)** ).

#### <a name="sign-in-to-azure"></a>Connexion à Azure

Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

#### <a name="select-the-app-in-the-azure-portal"></a>Sélectionner l’application dans le portail Azure

1. Recherchez et sélectionnez **App Services**.

   ![Capture d’écran montrant la sélection d’App Services.](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. Dans la page **App Services**, sélectionnez le nom de votre application Azure.

   ![Capture d’écran montrant la navigation dans le portail vers une application Azure.](./media/app-service-web-tutorial-custom-domain/select-app.png)

    La page de gestion de l’application App Service s’affiche.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>Vérification du niveau tarifaire

1. Dans le volet gauche de la page de l’application, faites défiler jusqu’à la section **Paramètres**, puis sélectionnez **Effectuer un scale-up (plan App Service)** .

   ![Capture d’écran montrant le menu Effectuer un scale-up (plan App Service).](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. Le niveau actuel de l’application est encadré d’un rectangle bleu. Vérifiez que l’application ne se trouve pas dans le niveau **F1**. Les DNS personnalisés ne sont pas disponibles dans le niveau **F1**.

   ![Capture d’écran montrant les niveaux tarifaires recommandés.](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. Si le plan App Service ne relève pas du niveau **F1**, fermez la page **Effectuer un scale-up**, puis passez à [3. Obtenir l’ID de vérification du domaine](#3-get-a-domain-verification-id).

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>Monter en puissance le plan App Service

1. Sélectionnez un niveau payant (**D1**, **B1**, **B2**, **B3** ou n’importe quel niveau dans la catégorie **Production**). Pour obtenir des options supplémentaires, sélectionnez **Afficher d’autres options**.

1. Sélectionnez **Appliquer**.

   ![Capture d’écran montrant la vérification du niveau tarifaire.](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   Lorsque la notification suivante s’affiche, cela signifie que l’opération est terminée.

   ![Capture d’écran montrant la confirmation de l’opération de mise à l’échelle](./media/app-service-web-tutorial-custom-domain/scale-notification.png)


## <a name="3-get-a-domain-verification-id"></a>3. Obtenir l’ID de vérification d’un domaine

Pour ajouter un domaine personnalisé à votre application, vous devez vérifier votre propriété du domaine, en ajoutant un ID de vérification sous forme d’enregistrement TXT avec votre fournisseur de domaine. 

1. Dans le volet gauche de la page de votre application, sélectionnez **Domaines personnalisés**. 
1. Pour l’étape suivante, copiez l’ID du champ **ID de vérification des domaines personnalisés** dans la page **Domaines personnalisés**.

    ![Capture d’écran montrant l’ID dans le champ ID de vérification des domaines personnalisés.](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    > [!WARNING]
    > L’ajout d’ID de vérification de domaines à votre domaine personnalisé peut empêcher les entrées DNS non résolues et éviter les acquisitions de sous-domaine. Pour les domaines personnalisés que vous avez précédemment configurés sans cet ID de vérification, vous devez les protéger du même risque en ajoutant l’ID de vérification à votre enregistrement DNS. Pour plus d’informations sur cette menace critique courante, consultez [Acquisition de sous-domaine](../security/fundamentals/subdomain-takeover.md).
    
<a name="info"></a>

3. **(Enregistrement A uniquement)** Pour mapper un [enregistrement A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A), vous avez besoin de l’adresse IP externe de l’application. Dans la page **Domaines personnalisés**, copiez la valeur d’**Adresse IP**.

   ![Capture d’écran montrant la navigation dans le portail vers une application Azure.](./media/app-service-web-tutorial-custom-domain/mapping-information.png)


## <a name="4-create-the-dns-records"></a>4. Créer les enregistrements DNS

1. Connectez-vous au site web de votre fournisseur de domaine.

    > [!NOTE]
    > Si vous le souhaitez, vous pouvez utiliser Azure DNS pour gérer les enregistrements DNS de votre domaine et configurer un nom DNS personnalisé pour Azure App Service. Pour plus d’informations, consultez [Didacticiel : Héberger votre domaine dans Azure DNS](../dns/dns-delegate-domain-azure-dns.md).

1. Trouvez la page de gestion des enregistrements DNS. 

    > [!NOTE]
    > Chaque fournisseur de domaine ayant sa propre interface d’enregistrements DNS, consultez la documentation de votre fournisseur. Recherchez les zones du site qui portent les mentions **Nom de domaine**, **DNS** ou **Gestion du nom de serveur**.
    >
    > Vous trouvez généralement la page des enregistrements DNS en affichant vos informations de compte, puis en recherchant un lien comme **Mes domaines**. Accédez à cette page, puis recherchez un lien nommé comme **Fichier de zone**, **Enregistrements DNS**, ou **Configuration avancée**.

   La capture d’écran suivante est un exemple d’une page d’enregistrements DNS :

   ![Capture d’écran montrant un exemple de page d’enregistrements DNS.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. Sélectionnez **Ajouter** ou le widget approprié pour créer un enregistrement. 

1. Sélectionnez le type d’enregistrement à créer et suivez les instructions. Vous pouvez utiliser un [enregistrement CNAME](https://en.wikipedia.org/wiki/CNAME_record) ou un [enregistrement A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) pour mapper un nom DNS personnalisé à App Service.

    > [!NOTE]
    > **L’enregistrement à choisir**
    > 
    > * Pour mapper le domaine racine (par exemple, `contoso.com`), utilisez un [enregistrement A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A). N’utilisez pas l’enregistrement CNAME pour l’enregistrement racine (pour plus d’informations, consultez [RFC 1912 Section 2.4](https://datatracker.ietf.org/doc/html/rfc1912#section-2.4)).
    > * Pour mapper un sous-domaine (par exemple, `www.contoso.com`), utilisez un [enregistrement CNAME](https://en.wikipedia.org/wiki/CNAME_record).
    > * Vous pouvez mapper un sous-domaine à l’adresse IP de l’application directement avec un enregistrement A, mais il est possible que [l’adresse IP change](overview-inbound-outbound-ips.md#when-inbound-ip-changes). L’enregistrement CNAME est quant à lui mappé au nom d’hôte par défaut de l’application, ce qui offre moins de risque de changement.
    > * Pour mapper un [domaine générique](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (par exemple, `*.contoso.com`), utilisez un enregistrement CNAME.
    
# <a name="cname"></a>[CNAME](#tab/cname)

Pour un sous-domaine comme `www` dans `www.contoso.com`, créez deux enregistrements conformément au tableau suivant :

| Type d’enregistrement | Host | Valeur | Commentaires |
| - | - | - |
| CNAME | `<subdomain>` (par exemple, `www`) | `<app-name>.azurewebsites.net` | Le mappage de domaine lui-même. |
| TXT | `asuid.<subdomain>` (par exemple, `asuid.www`) | [L’ID de vérification que vous avez obtenu précédemment](#3-get-a-domain-verification-id). | App Service accède à l’enregistrement TXT `asuid.<subdomain>` pour vérifier votre propriété du domaine personnalisé. |

![Capture d’écran montrant la navigation dans le portail vers une application Azure.](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

- Pour un domaine racine comme `contoso.com`, créez deux enregistrements conformément au tableau suivant :

    | Type d’enregistrement | Host | Valeur | Commentaires |
    | - | - | - |
    | Un | `@` | Adresse IP de [Copier l’adresse IP de l’application](#3-get-a-domain-verification-id) | Le mappage de domaine lui-même (`@` représente généralement le domaine racine). |
    | TXT | `asuid` | [L’ID de vérification que vous avez obtenu précédemment](#3-get-a-domain-verification-id). | App Service accède à l’enregistrement TXT `asuid.<subdomain>` pour vérifier votre propriété du domaine personnalisé. Pour le domaine racine, utilisez `asuid`. |
    
    ![Capture d’écran montrant une page d’enregistrements DNS.](./media/app-service-web-tutorial-custom-domain/a-record.png)
    
- Pour permettre le mappage d’un sous-domaine tel que `www.contoso.com` à un enregistrement A au lieu d’un enregistrement CNAME recommandé, votre enregistrement A et votre enregistrement TXT doivent plutôt se présenter comme dans le tableau suivant :

    |Type d’enregistrement|Host|Valeur|
    |--- |--- |--- |
    |Un|\<subdomain\> (par exemple, www)|Adresse IP de Copier l’adresse IP de l’application|
    |TXT|asuid.\<subdomain\> (par exemple, asuid.www)|L’ID de vérification que vous avez obtenu précédemment.|
    
# <a name="wildcard-cname"></a>[Générique (CNAME)](#tab/wildcard)

Pour un nom générique comme `*` dans `*.contoso.com`, créez deux enregistrements conformément au tableau suivant :

| Type d’enregistrement | Host | Valeur | Commentaires |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | Le mappage de domaine lui-même. |
| TXT | `asuid` | [L’ID de vérification que vous avez obtenu précédemment](#3-get-a-domain-verification-id). | App Service accède à l’enregistrement TXT `asuid` pour vérifier votre propriété du domaine personnalisé. |

![Capture d’écran montrant la navigation vers une application Azure.](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

> [!NOTE]
> Pour certains fournisseurs tels que GoDaddy, les modifications apportées aux enregistrements DNS n’entrent en vigueur que lorsque vous cliquez sur un lien **Enregistrer les modifications** distinct.

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

<a name="wildcard" aria-hidden="true"></a>

<a name="cname" aria-hidden="true"></a>

## <a name="5-enable-the-mapping-in-your-app"></a>5. Activer le mappage dans votre application

1. Dans le volet gauche de la page d’application du portail Azure, sélectionnez **Domaines personnalisés**.

    ![Capture d’écran montrant le menu Domaines personnalisés.](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. Sélectionnez **Ajouter un domaine personnalisé**.

    ![Capture d’écran montrant l’élément Ajouter un nom d’hôte.](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. Tapez le nom de domaine complet que vous avez ajouté pour un enregistrement CNAME, tel que `www.contoso.com`.

1. Sélectionnez **Valider**. La page **Ajouter un domaine personnalisé** s’affiche.

1. Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **CNAME (www\.exemple.com ou tout sous-domaine)** . Sélectionnez **Ajouter un domaine personnalisé**.

    ![Capture d’écran montrant le bouton Ajouter un domaine personnalisé.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Un certain temps peut être nécessaire pour que le nouveau domaine personnalisé s’affiche dans la page **Domaines personnalisés** de votre application. Actualisez le navigateur pour mettre à jour les données.

    ![Capture d’écran montrant l’ajout de l’enregistrement CNAME.](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > Si un avertissement s’affiche concernant votre domaine personnalisé, cela signifie qu’il n’est pas encore lié à un certificat TLS/SSL. Toute requête HTTPS envoyée par un navigateur à votre domaine personnalisé générera une erreur ou un avertissement, selon le navigateur utilisé. Pour ajouter une liaison TLS/SSL, consultez [Sécuriser un nom DNS personnalisé avec une liaison TLS/SSL dans Azure App Service](configure-ssl-bindings.md).

    Si vous avez raté une étape ou si vous avez fait une faute de frappe, une erreur de vérification s’affichera au bas de la page.

    ![Capture d’écran montrant une erreur de vérification.](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

# <a name="a"></a>[A](#tab/a)

3. Tapez le nom de domaine complet pour lequel vous avez configuré l’enregistrement A, tel que `contoso.com`. 

1. Sélectionnez **Valider**. La page **Ajouter un domaine personnalisé** s’affiche.

1. Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **Enregistrement A (exemple.com)** . Sélectionnez **Ajouter un domaine personnalisé**.

    ![Capture d’écran montrant l’ajout d’un nom DNS à l’application.](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    Un certain temps peut être nécessaire pour que le nouveau domaine personnalisé s’affiche dans la page **Domaines personnalisés** de votre application. Actualisez le navigateur pour mettre à jour les données.

    ![Capture d’écran montrant l’ajout d’un enregistrement A.](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > Si un avertissement s’affiche concernant votre domaine personnalisé, cela signifie qu’il n’est pas encore lié à un certificat TLS/SSL. Toute requête HTTPS envoyée par un navigateur à votre domaine personnalisé générera une erreur ou un avertissement, selon le navigateur utilisé. Pour ajouter une liaison TLS/SSL, consultez [Sécuriser un nom DNS personnalisé avec une liaison TLS/SSL dans Azure App Service](configure-ssl-bindings.md).
    
    Si vous avez raté une étape ou si vous avez fait une faute de frappe, une erreur de vérification s’affichera au bas de la page.
    
    ![Capture d’écran montrant une erreur de vérification.](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
# <a name="wildcard-cname"></a>[Générique (CNAME)](#tab/wildcard)

3. Tapez un nom de domaine complet qui correspond au domaine générique. Par exemple, pour `*.contoso.com`, vous pouvez utiliser `sub1.contoso.com`, `sub2.contoso.com`, `*.contoso.com` ou toute autre chaîne qui correspond au modèle générique. Ensuite, sélectionnez **Valider**.

    Le bouton **Ajouter un domaine personnalisé** est activé.

1. Assurez-vous que **Type d’enregistrement du nom d’hôte** est défini sur **Enregistrement CNAME (www\.exemple.com ou tout sous-domaine)** . Sélectionnez **Ajouter un domaine personnalisé**.

    ![Capture d’écran qui montre l’ajout d’un nom DNS à l’application.](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    Un certain temps peut être nécessaire pour que le nouveau domaine personnalisé s’affiche dans la page **Domaines personnalisés** de votre application. Actualisez le navigateur pour mettre à jour les données.

    > [!NOTE]
    > Si un avertissement s’affiche concernant votre domaine personnalisé, cela signifie qu’il n’est pas encore lié à un certificat TLS/SSL. Toute requête HTTPS envoyée par un navigateur à votre domaine personnalisé générera une erreur ou un avertissement, selon le navigateur utilisé. Pour ajouter une liaison TLS/SSL, consultez [Sécuriser un nom DNS personnalisé avec une liaison TLS/SSL dans Azure App Service](configure-ssl-bindings.md).

-----


## <a name="6-test-in-a-browser"></a>6. Test dans un navigateur

Accédez aux noms DNS que vous avez configurés précédemment.

![Capture d’écran montrant la navigation vers une application Azure.](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>

Si vous recevez une erreur HTTP 404 (Introuvable) lorsque vous accédez à l’URL de votre domaine personnalisé, les deux causes les plus courantes sont les suivantes :

* Un enregistrement A et/ou un enregistrement CNAME est manquant dans le domaine personnalisé configuré. Vous avez peut-être supprimé l’enregistrement DNS une fois que vous avez activé le mappage dans votre application. Vérifiez si les enregistrements DNS sont correctement configurés à l’aide d’un outil de <a href="https://www.nslookup.io/">recherche DNS en ligne</a>.
* Le client du navigateur a mis en cache l'ancienne adresse IP de votre domaine. Effacez le cache et testez à nouveau la résolution DNS. Sur une machine Windows, effacez le cache avec `ipconfig /flushdns`.


## <a name="migrate-an-active-domain"></a>Migrer un domaine actif

Pour migrer un site actif et son nom de domaine DNS vers App Service sans interruption de service, voir [Migrer un nom DNS actif vers Azure App Service](manage-custom-dns-migrate-domain.md).

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>Rediriger vers un répertoire personnalisé

> [!NOTE]
> Par défaut, App Service dirige les demandes web au répertoire racine du code de votre application. Cependant, certains frameworks web ne démarrent pas dans le répertoire racine. Par exemple, [Laravel](https://laravel.com/) démarre dans le sous-répertoire `public`. Pour continuer avec l’exemple DNS `contoso.com`, une telle application est accessible à partir de l’adresse `http://contoso.com/public`, mais il est préférable de diriger `http://contoso.com` vers le répertoire `public`.

Même si ce scénario est courant, il n’implique en fait pas de mappage DNS personnalisé, mais il a trait à la personnalisation du répertoire virtuel au sein de votre application.

1. Sélectionnez **Paramètres d’application** dans le volet gauche de la page de votre application web.

1. En bas de la page, le répertoire virtuel racine `/` pointe par défaut vers `site\wwwroot`, qui correspond au répertoire racine de votre code d'application. Modifiez-le afin qu'il pointe vers `site\wwwroot\public`, par exemple, puis enregistrez vos modifications.

    ![Capture d’écran montrant la personnalisation d’un répertoire virtuel.](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. Une fois l’opération terminée, vérifiez en accédant au chemin racine de votre application dans le navigateur (par exemple, `http://contoso.com` ou `http://<app-name>.azurewebsites.net`).


## <a name="automate-with-scripts"></a>Automatiser des tâches à l’aide de scripts

Vous pouvez automatiser la gestion des domaines personnalisés à l’aide de scripts, en utilisant [Azure CLI](/cli/azure/install-azure-cli) ou [Azure PowerShell](/powershell/azure/).

#### <a name="azure-cli"></a>Azure CLI

La commande suivante ajoute un nom DNS personnalisé configuré à une application App Service.

```azurecli 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

Pour plus d’informations, consultez [Mapper un nom de domaine personnalisé à une application web](scripts/cli-configure-custom-domain.md).

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

La commande suivante ajoute un nom DNS personnalisé configuré à une application App Service.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

Pour plus d’informations, consultez [Attribuer un domaine personnalisé à une application web](scripts/powershell-configure-custom-domain.md).


## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant pour découvrir comment lier un certificat TLS/SSL personnalisé à une application web.

> [!div class="nextstepaction"]
> [Sécuriser un nom DNS personnalisé avec une liaison TLS/SSL dans Azure App Service](configure-ssl-bindings.md)
