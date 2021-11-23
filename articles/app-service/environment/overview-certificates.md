---
title: Certificats dans App Service Environment
description: Décrit certains aspects des certificats dans un environnement App Service. Découvrez comment fonctionnent les liaisons de certificats dans les applications à locataire unique au sein d’un environnement ASE.
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: a86a5e67faafc88c837f7401db473b0164125a82
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526413"
---
# <a name="certificates-and-the-app-service-environment"></a>Certificats et l’environnement App Service 
> [!NOTE]
> Cet article concerne la fonctionnalité App Service Environment v3 qui est utilisée avec les plans App Service v2 Isolé
>

L’environnement App Service (ASE) est un déploiement d’Azure App Service qui s’exécute dans votre réseau virtuel Azure. Il peut être déployé avec un point de terminaison d’application accessible sur Internet ou un point de terminaison d’application dans votre réseau virtuel. Si vous déployez l’environnement ASE avec un point de terminaison accessible par Internet, ce déploiement est appelé ASE externe. Si vous déployez l’ASE avec un point de terminaison dans votre réseau virtuel, ce déploiement est appelé ASE ILB. Vous pouvez en savoir plus sur l’environnement ASE ILB dans le document [Créer et utiliser un équilibreur de charge interne avec un environnement App Service](./creation.md).

## <a name="application-certificates"></a>Certificats d’application

Les applications qui sont hébergées dans un environnement ASE peuvent utiliser les fonctionnalités de certificat orientées application qui sont disponibles dans le service App Service multilocataire. Ces fonctionnalités incluent :

- Certificats SNI
- Certificats hébergés KeyVault

Les exigences et les instructions pour charger et gérer ces certificats sont disponibles dans [Ajouter un certificat TLS/SSL dans Azure App Service](../configure-ssl-certificate.md).

Une fois le certificat ajouté à votre application App Service ou à votre application de fonction, vous pouvez [l’utiliser pour sécuriser un nom de domaine personnalisé](../configure-ssl-bindings.md) ou [l’utiliser dans votre code d’application](../configure-ssl-certificate-in-code.md).

## <a name="tls-settings"></a>Paramètres TLS

Vous pouvez [configurer le paramètre TLS](../configure-ssl-bindings.md#enforce-tls-versions) au niveau de l’application.

## <a name="private-client-certificate"></a>Certificat client privé

Un cas d’usage courant consiste à configurer votre application en tant que client dans un modèle client-serveur. Si vous sécurisez votre serveur avec un certificat d’autorité de certification privé, vous devez charger le certificat client sur votre application. Les instructions suivantes permettent de charger les certificats dans le truststore des travailleurs sur lesquels votre application s’exécute. Si vous chargez le certificat sur une seule application, vous pouvez l’utiliser avec vos autres applications du même plan App Service sans avoir à recharger le certificat.

>[!NOTE]
> Les certificats clients privés ne sont pas pris en charge en dehors de l’application. Cela limite l’utilisation à des scénarios de type : tirage de l’image conteneur de l’application à partir d’un registre en utilisant un certificat privé et validation TLS avec les serveurs front-end en utilisant un certificat privé.

Suivez ces étapes pour charger le certificat (fichier *.cer*) dans votre application dans votre ASE. Le fichier *.cer* peut être exporté à partir de votre certificat. Pour les tests, un exemple PowerShell vous est proposé à la fin pour générer un certificat auto-signé temporaire :

1. Accédez à l’application qui nécessite le certificat dans le portail Azure.
1. Accédez aux **Paramètres TLS/SSL** dans l’application. Cliquez sur **Certificat à clé publique (.cer)** . Sélectionnez **Charger un certificat à clé publique**. Donnez-lui un nom. Recherchez et sélectionnez votre fichier *.cer*. Sélectionnez Télécharger. 
1. Copiez l’empreinte numérique.
1. Accédez à **Paramètres d’application**. Créez le paramètre d’application WEBSITE_LOAD_ROOT_CERTIFICATES avec l’empreinte comme valeur. Si vous avez plusieurs certificats, vous pouvez les placer dans le même paramètre, séparés par des virgules et sans espace comme 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

Le certificat est accessible par toutes les applications du même plan App Service que l’application qui a configuré ce paramètre. Si vous avez besoin qu’il soit disponible pour les applications d’un autre plan App Service, vous devez répéter l’opération de paramètre d’application dans une application de ce plan App Service. Pour vérifier que le certificat est défini, accédez à la console Kudu et émettez la commande suivante dans la console de débogage de PowerShell :

```azurepowershell-interactive
dir Cert:\LocalMachine\Root
```

Pour effectuer le test, vous pouvez créer un certificat auto-signé et générer un fichier *.cer* avec le code PowerShell suivant : 

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -CertStoreLocation "Cert:\LocalMachine\My" -DnsName "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "Cert:\LocalMachine\My\" + $certificate.Thumbprint
$fileName = "exportedcert.cer"
Export-Certificate -Cert $certThumbprint -FilePath $fileName -Type CERT
```

## <a name="next-steps"></a>Étapes suivantes

* Informations sur l’[utilisation des certificats dans le code d’application](../configure-ssl-certificate-in-code.md)