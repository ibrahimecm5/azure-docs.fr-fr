---
title: Exposition d’applications à Internet avec Application Gateway
titleSuffix: Azure Spring Cloud
description: Guide pratique pour exposer des applications à Internet avec Application Gateway.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: devx-track-java
ms.openlocfilehash: e23503c7e38009a8ac6b5da82dd17e26c7aeeaee
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132404352"
---
# <a name="expose-applications-to-the-internet-using-application-gateway"></a>Exposition d’applications à Internet avec Application Gateway

Cet article explique comment exposer des applications à Internet avec Application Gateway. Lorsqu’une instance de service Azure Spring Cloud est déployée dans votre réseau virtuel, les applications de l’instance de service sont accessibles uniquement dans le réseau privé. Pour rendre les applications accessibles sur Internet, vous devez effectuer une intégration avec Azure Application Gateway.

## <a name="prerequisites"></a>Prérequis

- [Azure CLI version 2.0.4 ou ultérieure](/cli/azure/install-azure-cli).
- Une instance de service Azure Spring Cloud déployée dans un réseau virtuel avec une application accessible sur le réseau privé à l’aide du suffixe de domaine `.private.azuremicroservices.io` par défaut. Pour plus d’informations, consultez [Déploiement d’Azure Spring Cloud dans un réseau virtuel](./how-to-deploy-in-azure-virtual-network.md).
- Un domaine personnalisé servant à accéder à l’application.
- Un certificat, stocké dans Key Vault, correspondant au domaine personnalisé à utiliser pour établir l’écouteur HTTPS. Pour plus d’informations, consultez [Tutoriel : Importation d’un certificat dans Azure Key Vault](../key-vault/certificates/tutorial-import-certificate.md).

## <a name="configure-application-gateway-for-azure-spring-cloud"></a>Configuration d’Application Gateway pour Azure Spring Cloud

Nous vous recommandons de faire en sorte que le nom de domaine visible par le navigateur soit le même que le nom d’hôte utilisé par Application Gateway pour diriger le trafic vers le backend Azure Spring Cloud. L’expérience est alors optimale lorsqu’il s’agit d’utiliser Application Gateway pour exposer des applications hébergées dans Azure Spring Cloud et résidant dans un réseau virtuel. Si le domaine exposé par Application Gateway est différent du domaine accepté par Azure Spring Cloud, les cookies et les URL de redirection générées (par exemple) risquent d’être rompus.

Pour configurer Application Gateway devant Azure Spring Cloud, procédez comme suit.

1. Suivez les instructions décrites dans [Déploiement d’Azure Spring Cloud dans un réseau virtuel](./how-to-deploy-in-azure-virtual-network.md).
1. Obtenez un certificat pour le domaine de votre choix et stockez-le dans Key Vault. Pour plus d’informations, consultez [Tutoriel : Importation d’un certificat dans Azure Key Vault](../key-vault/certificates/tutorial-import-certificate.md).
1. Configurez un domaine personnalisé et le certificat correspondant issu de Key Vault dans une application déployée sur Azure Spring Cloud. Pour plus d’informations, consultez [Tutoriel : Mappage d’un domaine personnalisé existant à Azure Spring Cloud](./tutorial-custom-domain.md).
1. Déployez Application Gateway dans un réseau virtuel configuré conformément à la liste suivante :
   - Utilisez Azure Spring Cloud dans le pool backend, indiqué par le suffixe de domaine `private.azuremicroservices.io`.
   - Incluez un écouteur HTTPS avec le même certificat issu de Key Vault.
   - Configurez le réseau virtuel avec des paramètres HTTP qui utilisent le nom de domaine personnalisé configuré sur Azure Spring Cloud au lieu du suffixe de domaine `private.azuremicroservices.io`.
1. Configurez votre DNS public de sorte qu’il pointe vers Application Gateway.

## <a name="define-variables"></a>Définir des variables

Maintenant, utilisez les commandes suivantes afin de définir des variables pour le groupe de ressources et le réseau virtuel créés (cf. [Déploiement d’Azure Spring Cloud dans un réseau virtuel](./how-to-deploy-in-azure-virtual-network.md)). Personnalisez les valeurs en fonction de votre environnement réel. Lorsque vous définissez `SPRING_APP_PRIVATE_FQDN`, supprimez `https://` de l’URI.

```bash
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_CLOUD_NAME='name-of-spring-cloud-instance'
APPNAME='name-of-app-in-azure-spring-cloud'
SPRING_APP_PRIVATE_FQDN='$APPNAME.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="sign-in-to-azure"></a>Connexion à Azure

Utilisez la commande suivante pour vous connecter à Azure CLI et choisir votre abonnement actif.

```azurecli
az login
az account set --subscription $SUBSCRIPTION
```

## <a name="acquire-a-certificate"></a>Acquisition d’un certificat

### <a name="use-a-publicly-signed-certificate"></a>[Utiliser un certificat signé publiquement](#tab/public-cert)

Pour les déploiements en production, vous utiliserez très probablement un certificat signé publiquement. Dans ce cas, importez le certificat dans Azure Key Vault. Pour plus d’informations, consultez [Tutoriel : Importation d’un certificat dans Azure Key Vault](../key-vault/certificates/tutorial-import-certificate.md). Assurez-vous que le certificat comprend la totalité de la chaîne de certificats.

### <a name="use-a-self-signed-certificate"></a>[Utiliser un certificat auto-signé](#tab/self-signed-cert)

Lorsque vous avez besoin d’un certificat auto-signé à des fins de test ou de développement, vous devez le créer. Il vous faut également veiller à ce que la liste des « Autres noms de l’objet » présente dans le certificat contienne le nom de domaine sur lequel vous exposerez l’application. Dans le cas de la création d’un certificat auto-signé avec Azure Key Vault, vous pouvez vous en assurer par le biais du Portail Azure. Si en revanche vous utilisez Azure CLI, vous aurez besoin d’un fichier JSON de stratégie.

Pour demander la stratégie par défaut, utilisez la commande suivante :

```azurecli
az keyvault certificate get-default-policy
```

Ensuite, adaptez le fichier JSON de stratégie comme dans l’exemple suivant, en indiquant les valeurs `subject` et `SubjectAlternativeNames` :

```json
{
  // ...
    "subject": "C=US, ST=WA, L=Redmond, O=Contoso, OU=Contoso HR, CN=myapp.mydomain.com",
    "subjectAlternativeNames": {
        "dnsNames": [
            "myapp.mydomain.com",
            "*.myapp.mydomain.com"
        ],
        "emails": [
            "hello@contoso.com"
        ],
          "upns": []
      }
  // ...
}
```

Une fois que vous avez terminé la mise à jour du fichier JSON de stratégie (cf. [Mise à jour de la stratégie de certificat](/rest/api/keyvault/update-certificate-policy/update-certificate-policy)), vous pouvez créer un certificat auto-signé dans Key Vault avec les commandes suivantes :

```azurecli
KV_NAME='name-of-key-vault'
CERT_NAME_IN_KV='name-of-certificate-in-key-vault'

az keyvault certificate create \
    --vault-name $KV_NAME \
    --name $CERT_NAME_IN_KV \
    --policy "$KV_CERT_POLICY"
```

---

## <a name="configure-the-public-domain-name-on-azure-spring-cloud"></a>Configuration du nom de domaine public sur Azure Spring Cloud

Le trafic entre dans l’application déployée sur Azure Spring Cloud avec le nom de domaine public. Pour configurer votre application de sorte qu’elle écoute ce nom d’hôte, et ce, à l’aide du protocole HTTPS, utilisez les commandes suivantes, qui vous permettent d’ajouter un domaine personnalisé à votre application :

```azurecli
KV_NAME='name-of-key-vault'
KV_RG='resource-group-name-of-key-vault'
CERT_NAME_IN_ASC='name-of-certificate-in-Azure-Spring-Cloud'
CERT_NAME_IN_KV='name-of-certificate-with-intermediaries-in-key-vault'
DOMAIN_NAME=myapp.mydomain.com

# provide permissions to ASC to read the certificate from Key Vault:
VAULTURI=$(az keyvault show -n $KV_NAME -g $KV_RG --query properties.vaultUri -o tsv)

# get the object id for the Azure Spring Cloud Domain-Management Service Principal:
ASCDM_OID=$(az ad sp show --id 03b39d0f-4213-4864-a245-b1476ec03169 --query objectId --output tsv)

# allow this Service Principal to read and list certificates and secrets from Key Vault:
az keyvault set-policy -g $KV_RG -n $KV_NAME  --object-id $ASCDM_OID --certificate-permissions get list --secret-permissions get list

# add custom domain name and configure TLS using the certificate:
az spring-cloud certificate add \
    --resource-group $RESOURCE_GROUP \
    --service $SPRING_CLOUD_NAME \
    --name $CERT_NAME_IN_ASC \
    --vault-certificate-name $CERT_NAME_IN_KV \
    --vault-uri $VAULTURI
az spring-cloud app custom-domain bind \
    --resource-group $RESOURCE_GROUP \
    --service $SPRING_CLOUD_NAME \
    --domain-name $DOMAIN_NAME \
    --certificate $CERT_NAME_IN_ASC \
    --app $APPNAME
```

> [!NOTE]
> Lorsque, à des fins de développement et de test, vous souhaitez effectuer une terminaison TLS sur Application Gateway, il n’est pas nécessaire de configurer un certificat sur Azure Spring Cloud ni de fournir à Azure Spring Cloud des autorisations d’accès en lecture à Key Vault. Vous devez simplement lier le nom de domaine, ce que vous pouvez faire avec la commande suivante :
>
> ```azurecli
> az spring-cloud app custom-domain bind \
>     --resource-group $RESOURCE_GROUP \
>     --service $SPRING_CLOUD_NAME \
>     --domain-name $DOMAIN_NAME \
>     --app $APPNAME
> ```

## <a name="create-network-resources"></a>Créer des ressources réseau

La passerelle applicative Azure qui sera créée rejoindra le même réseau virtuel que l’instance de service Azure Spring Cloud, ou un réseau virtuel qui lui est appairé. Commencez par créer un sous-réseau pour la passerelle applicative dans le réseau virtuel en utilisant `az network vnet subnet create`, puis créez une IP publique en tant que serveur frontal de la passerelle applicative en utilisant `az network public-ip create`.

```azurecli
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name $APPLICATION_GATEWAY_SUBNET_NAME \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VIRTUAL_NETWORK_NAME \
    --address-prefix $APPLICATION_GATEWAY_SUBNET_CIDR
az network public-ip create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --name $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-a-managed-identity-for-application-gateway"></a>Création d’une identité managée pour Application Gateway

Application Gateway doit avoir accès à Key Vault pour lire le certificat. Il utilise à cette fin une [identité managée](../active-directory/managed-identities-azure-resources/overview.md) affectée par l’utilisateur. Créez l’identité managée avec la commande suivante :

```azurecli
APPGW_IDENTITY_NAME='name-for-appgw-managed-identity'
az identity create \
    --resource-group $RESOURCE_GROUP \
    --name $APPGW_IDENTITY_NAME
```

Ensuite, récupérez la valeur objectId de l’identité managée, car elle sera utilisée ultérieurement pour accorder des droits d’accès au certificat dans Key Vault :

```azurecli
APPGW_IDENTITY_CLIENTID=$(az identity show --resource-group $RESOURCE_GROUP --name $APPGW_IDENTITY_NAME --query clientId --output tsv)
APPGW_IDENTITY_OID=$(az ad sp show --id $APPGW_IDENTITY_CLIENTID --query objectId --output tsv)
```

## <a name="set-policy-on-key-vault"></a>Définition de la stratégie sur Key Vault

Configurez Key Vault avec la commande suivante afin que l’identité managée pour Application Gateway soit autorisée à accéder au certificat stocké dans Key Vault :

```azurecli
az keyvault set-policy \
    --name $KV_NAME \
    --resource-group $KV_RG \
    --object-id $APPGW_IDENTITY_OID \
    --secret-permissions get list \
    --certificate-permissions get list
```

## <a name="create-application-gateway"></a>Créer une passerelle Application Gateway

Créez une passerelle applicative en utilisant `az network application-gateway create` et spécifiez le nom de domaine complet (FQDN) privé de votre application en tant que serveurs dans le pool principal. Veillez à utiliser l’identité managée affectée par l’utilisateur et à pointer sur le certificat dans Key Vault avec l’ID de secret du certificat. Ensuite, mettez à jour le paramètre HTTP avec la commande `az network application-gateway http-settings update` pour utiliser le nom d’hôte public.

```azurecli
APPGW_NAME='name-for-application-gateway'

KEYVAULT_SECRET_ID_FOR_CERT=$(az keyvault certificate show --name $CERT_NAME_IN_KV --vault-name $KV_NAME --query sid --output tsv)

az network application-gateway create \
    --name $APPGW_NAME \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --capacity 2 \
    --sku Standard_v2 \
    --frontend-port 443 \
    --http-settings-cookie-based-affinity Disabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --vnet-name $VIRTUAL_NETWORK_NAME \
    --subnet $APPLICATION_GATEWAY_SUBNET_NAME \
    --servers $SPRING_APP_PRIVATE_FQDN \
    --key-vault-secret-id $KEYVAULT_SECRET_ID_FOR_CERT \
    --identity $APPGW_IDENTITY_NAME
```

La création de la passerelle d’application par Azure peut prendre jusqu’à 30 minutes.

>[!NOTE]
> Lorsque, à des fins de développement et de test, vous souhaitez effectuer une terminaison TLS sur Application Gateway, remplacez la valeur de `http-settings-port` par `80` et la valeur de `http-settings-protocol` par `Https`. Continuez à suivre les instructions ci-dessous pour « Utiliser un certificat signé publiquement ».

### <a name="update-http-settings-to-use-the-domain-name-towards-the-backend"></a>Mise à jour des paramètres HTTP pour utiliser le nom de domaine vers le backend

#### <a name="use-a-publicly-signed-certificate"></a>[Utiliser un certificat signé publiquement](#tab/public-cert-2)

Mettez à jour les paramètres HTTP de façon à utiliser le nom de domaine public comme nom d’hôte au lieu du suffixe de domaine « .private.azuremicroservices.io » pour envoyer le trafic vers Azure Spring Cloud.

```azurecli
az network application-gateway http-settings update \
    --resource-group $RESOURCE_GROUP \
    --gateway-name $APPGW_NAME \
    --host-name-from-backend-pool false \
    --host-name $DOMAIN_NAME \
    --name appGatewayBackendHttpSettings
```

#### <a name="use-a-self-signed-certificate"></a>[Utiliser un certificat auto-signé](#tab/self-signed-cert-2)

Mettez à jour les paramètres HTTP de façon à utiliser le nom de domaine public comme nom d’hôte au lieu du suffixe de domaine « .private.azuremicroservices.io » pour envoyer le trafic vers Azure Spring Cloud. Le certificat auto-signé utilisé doit être placé dans la liste d’autorisation dans les paramètres HTTP d’Application Gateway.

Pour placer le certificat dans la liste d’autorisation, commencez par extraire sa partie publique de Key Vault avec la commande suivante :

```azurecli
az keyvault certificate download \
    --vault-name $KV_NAME \
    --name $CERT_NAME_IN_KV \
    --file ./selfsignedcert.crt \
    --encoding DER
```

Ensuite, chargez-le sur Application Gateway avec la commande suivante :

```azurecli
az network application-gateway root-cert create \
    --resource-group $RG \
    --cert-file ./selfsignedcert.crt \
    --gateway-name $APPGW_NAME \
    --name MySelfSignedTrustedRootCert
```

Vous pouvez maintenant mettre à jour les paramètres HTTP pour approuver le nouveau certificat racine (auto-signé) avec cette commande :

```azurecli
az network application-gateway http-settings update \
    --resource-group $RG \
    --gateway-name $APPGW_NAME \
    --host-name-from-backend-pool false \
    --host-name $DOMAIN_NAME \
    --name appGatewayBackendHttpSettings \
    --root-certs MySelfSignedTrustedRootCert
```

---

### <a name="check-the-deployment-of-application-gateway"></a>Vérification du déploiement d’Application Gateway

Après sa création, vérifiez l’intégrité du backend avec la commande suivante. La sortie de cette commande vous permet de déterminer si la passerelle applicative atteint votre application par le biais de son nom de domaine complet privé.

```azurecli
az network application-gateway show-backend-health \
    --name $APPGW_NAME \
    --resource-group $RESOURCE_GROUP
```

La sortie indique un état sain du pool principal, comme le montre l’exemple suivant :

```output
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="configure-dns-and-access-the-application"></a>Configuration du DNS et accès à l’application

À présent, configurez le DNS public de sorte qu’il pointe vers Application Gateway à l’aide d’un enregistrement CNAME ou A. Vous trouverez l’adresse publique d’Application Gateway avec la commande suivante :

```azurecli
az network public-ip show \
    --resource-group $RESOURCE_GROUP \
    --name $APPLICATION_GATEWAY_PUBLIC_IP_NAME \
    --query [ipAddress] \
    --output tsv
```

Vous pouvez maintenant accéder à l’application avec le nom de domaine public.

## <a name="see-also"></a>Voir aussi

- [Résolution des problèmes Azure Spring Cloud dans un réseau virtuel](./troubleshooting-vnet.md)
- [Responsabilités du client pour l’exécution d’Azure Spring Cloud dans un réseau virtuel](./vnet-customer-responsibilities.md)
