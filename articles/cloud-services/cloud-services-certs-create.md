---
title: Cloud Services (classique) et certificats de gestion | Microsoft Docs
description: Découvrez comment créer et déployer des certificats pour les services cloud et pour l’authentification avec l’API de gestion dans Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: e96d3219668475760556c209b3d7a4d59da1b275
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423353"
---
# <a name="certificates-overview-for-azure-cloud-services-classic"></a>Vue d’ensemble des certificats pour Azure Cloud Services (classique)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Dans Azure, des certificats sont utilisés pour les services cloud ([certificats de service](#what-are-service-certificates)) et pour l’authentification auprès de l’API de gestion ([certificats de gestion](#what-are-management-certificates)). Cette rubrique offre une vue d’ensemble de ces deux types de certificats et vous explique comment les [créer](#create) et les déployer dans Azure.

Les certificats utilisés dans Azure sont des certificats x.509 v3 et peuvent être signés par un autre certificat approuvé ou être auto-signés. Un certificat auto-signé est signé par son propre créateur et n’est donc pas approuvé par défaut. La plupart des navigateurs peuvent ignorer ce problème. Les certificats auto-signés ne doivent être utilisés que par vous au moment où vous développez et testez vos services cloud. 

Les certificats utilisés par Azure peuvent contenir une clé publique. Les certificats comportent une empreinte numérique qui permet de les identifier sans ambiguïté. Cette empreinte numérique est utilisée dans le [fichier de configuration](cloud-services-configure-ssl-certificate-portal.md) Azure pour identifier le certificat qu’un service cloud doit utiliser. 

>[!Note]
>Azure Cloud Services n’accepte pas de certificat chiffré AES256-SHA256.

## <a name="what-are-service-certificates"></a>Que sont les certificats de service ?
Les certificats de service sont associés aux services cloud et sécurisent les communications à destination et en provenance du service. Par exemple, si vous avez déployé un rôle web, vous pouvez fournir un certificat qui peut authentifier un point de terminaison HTTPS exposé. Les certificats de service, définis dans votre définition de service, sont déployés automatiquement sur la machine virtuelle qui exécute une instance de votre rôle. 

Vous pouvez charger les certificats de service dans Azure par l’intermédiaire du portail Azure ou à l’aide du modèle de déploiement classique. Les certificats de service sont associés à un service cloud spécifique. Ils sont attribués à un déploiement dans le fichier de définition de service.

Les certificats de service peuvent être gérés séparément de vos services et par différentes personnes. Par exemple, un développeur peut charger un package de services qui fait référence à un certificat précédemment chargé dans Azure par un responsable informatique. Un responsable informatique peut gérer et renouveler ce certificat (en modifiant la configuration du service) sans avoir à charger un nouveau package de services. La mise à jour sans nouveau package de service est possible par le fait que le nom logique, ainsi que le nom et l’emplacement du magasin du certificat sont spécifiés dans le fichier de définition de service, alors que l’empreinte numérique du certificat est spécifiée dans le fichier de configuration de service. Pour mettre à jour le certificat, il est uniquement nécessaire de charger un nouveau certificat et de modifier la valeur d’empreinte numérique dans le fichier de configuration de service.

>[!Note]
>L’article [Forum aux questions sur Azure Cloud Services - Configuration et gestion](cloud-services-configuration-and-management-faq.yml) comporte des informations utiles sur les certificats.

## <a name="what-are-management-certificates"></a>Que sont les certificats de gestion ?
Les certificats de gestion vous permettent de vous authentifier dans le modèle de déploiement classique. De nombreux programmes et outils (tels que Visual Studio ou le Kit de développement logiciel (SDK) Azure) utilisent ces certificats pour automatiser la configuration et le déploiement de divers services Azure. Ces certificats ne sont pas réellement associés aux services cloud. 

> [!WARNING]
> Soyez prudent ! Ces types de certificat permettent à toute personne qui s’authentifie par leur biais de gérer l’abonnement auquel ils sont associés. 
> 
> 

### <a name="limitations"></a>Limites
Le nombre de certificats de gestion est limité à 100 par abonnement. Il existe également une limite de 100 certificats de gestion pour l’ensemble des abonnements figurant sous un identificateur d’utilisateur d’administrateur de service spécifique. Si l’identificateur d’utilisateur de l’administrateur de compte a déjà été utilisé pour ajouter 100 certificats de gestion et que d’autres certificats sont nécessaires, vous pouvez ajouter un coadministrateur pour disposer des certificats supplémentaires. 

En outre, les certificats de gestion ne peuvent pas être utilisés avec les abonnements CSP, car les abonnements CSP prennent uniquement en charge le modèle de déploiement Azure Resource Manager et les certificats de gestion utilisent le modèle de déploiement classique. Pour plus d’informations sur les options disponibles pour les abonnements CSP, consultez les références [Azure Resource Manager vs modèle de déploiement classique](/azure/azure-resource-manager/management/deployment-models) et [Comprendre l’authentification avec le kit de développement logiciel (SDK) Azure pour .NET](/dotnet/azure/sdk/authentication).

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>Création d’un certificat auto-signé
Vous pouvez créer un certificat auto-signé au moyen de n’importe quel outil disponible, à condition qu’il remplisse les conditions suivantes :

* Certificat X.509.
* Contient une clé publique.
* Créé pour l’échange de clés (fichier .pfx).
* Le nom du sujet doit correspondre au domaine servant à accéder au service cloud.

    > Vous ne pouvez pas acquérir un certificat SSL/TLS pour le domaine cloudapp.net (ou pour tout domaine lié à Azure). Le nom d’objet du certificat doit correspondre au nom de domaine personnalisé utilisé pour accéder à votre application. Par exemple, **contoso.net**, mais pas **contoso.cloudapp.net**.

* Chiffrement à 2 048 bits au minimum.
* **Certificat de service uniquement** : le certificat côté client doit résider dans le magasin de certificats *personnel* .

Vous disposez de deux méthodes simples pour créer un certificat sur Windows : avec l’utilitaire `makecert.exe` ou avec IIS.

### <a name="makecertexe"></a>Makecert.exe
Cet utilitaire a été déconseillé et n’est plus documenté ici. Pour plus d’informations, consultez [cet article MSDN](/windows/desktop/SecCrypto/makecert).

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> Pour utiliser le certificat avec une adresse IP au lieu d’un domaine, utilisez l’adresse IP dans le paramètre -DnsName.


Si vous souhaitez utiliser ce [certificat avec le portail de gestion](/previous-versions/azure/azure-api-management-certs), exportez-le vers un fichier **.cer** :

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>Internet Information Services (IIS)
De nombreuses pages sur Internet vous expliquent comment procéder avec IIS. [ici](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) une procédure détaillée particulièrement claire. 

### <a name="linux"></a>Linux
[Cet](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) article décrit comment créer des certificats avec SSH.

## <a name="next-steps"></a>Étapes suivantes
[Pour télécharger votre certificat de service sur le portail Azure](cloud-services-configure-ssl-certificate-portal.md).

Chargez un [certificat d’API de gestion](/previous-versions/azure/azure-api-management-certs) dans le portail Azure.
