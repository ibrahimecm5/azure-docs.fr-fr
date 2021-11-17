---
title: Configuration d’Azure Functions avec un réseau virtuel
description: Article qui montre comment effectuer certaines tâches de mise en réseau virtuel pour Azure Functions.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: db0567456156f8ea74ba048e991000b57ae271b2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132332380"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Configuration d’Azure Functions avec un réseau virtuel

Cet article explique comment effectuer les tâches liées à la configuration de votre application de fonction pour la connecter à un réseau virtuel et l’exécuter sur celui-ci. Pour des informations détaillées sur la sécurisation de votre compte de stockage, consultez le didacticiel [Se connecter à un réseau virtuel](functions-create-vnet.md). Pour en savoir plus sur Azure Functions et la mise en réseau, consultez [Options de mise en réseau d’Azure Functions](functions-networking-options.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Restreindre votre compte de stockage à un réseau virtuel 

Quand vous créez une application de fonction, vous devez créer un compte de stockage Azure à usage général qui prend en charge le stockage Blob, File d’attente et Table, ou établir un lien vers un compte de ce type. Vous pouvez remplacer ce compte de stockage par un compte sécurisé avec des points de terminaison de service ou privés. Quand vous configurez votre compte de stockage avec des points de terminaison privés, l’accès public à votre application de fonction est automatiquement désactivé et l’application n’est accessible que via le réseau virtuel. 

> [!NOTE]  
> Cette fonctionnalité opère actuellement pour toutes les références (SKU) prises en charge par les réseaux virtuels Windows dans le plan (App Service) dédié, et pour les plans Premium élastiques Windows. ASEv3 n’est pas encore pris en charge. Elle est également prise en charge avec un DNS privé pour les références (SKU) prises en charge par les réseaux virtuels Linux. La consommation et le DNS personnalisé pour les plans Linux ne sont pas pris en charge. 

Pour configurer une fonction avec un compte de stockage limité à un réseau privé :

1. Créez une fonction avec un compte de stockage pour lequel les points de terminaison de service ne sont pas activés.

1. Configurez la fonction pour qu’elle se connecte à votre réseau virtuel.

1. Créez ou configurez un autre compte de stockage.  Il s’agit du compte de stockage que nous sécurisons avec les points de terminaison de service et auquel nous connectons notre fonction.

1. [Créez un partage de fichiers](../storage/files/storage-how-to-create-file-share.md#create-a-file-share) dans le compte de stockage sécurisé.

1. Activez les points de terminaison de service ou le point de terminaison privé pour le compte de stockage.  
    * Si vous utilisez des connexions de point de terminaison privé, le compte de stockage a besoin d’un point de terminaison privé pour les sous-ressources `file` et `blob`.  Si vous utilisez certaines capacités, telles que Durable Functions, `queue` et `table` doivent également être accessibles par le biais d’une connexion de point de terminaison privé.
    * Si vous utilisez des points de terminaison de service, activez le sous-réseau dédié à vos applications de fonction pour les comptes de stockage.

1. Copiez le fichier et le contenu de l’objet blob à partir du compte de stockage de l’application de fonction vers le compte de stockage sécurisé et le partage de fichiers.

1. Copiez la chaîne de connexion pour ce compte de stockage.

1. Mettez à jour les **Paramètres de l’application** sous **Configuration** pour l’application de fonction comme suit :

    | Nom du paramètre | Valeur | Commentaire |
    |----|----|----|
    | `AzureWebJobsStorage`| Chaîne de connexion de stockage | Il s’agit de la chaîne de connexion pour un compte de stockage sécurisé. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Chaîne de connexion de stockage | Il s’agit de la chaîne de connexion pour un compte de stockage sécurisé. |
    | `WEBSITE_CONTENTSHARE` | Partage de fichiers | Nom du partage de fichiers créé dans le compte de stockage sécurisé où résident les fichiers de déploiement du projet. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | La valeur 1 permet à votre application de fonction de se mettre à l’échelle lorsque votre compte de stockage est limité à un réseau virtuel. Vous devez activer ce paramètre lorsque vous limitez votre compte de stockage à un réseau virtuel. |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Force tout le trafic sortant via le réseau virtuel. Obligatoire lorsque le compte de stockage utilise des connexions de point de terminaison privé. |

1. Sélectionnez **Enregistrer** pour enregistrer les paramètres de l’application. La modification des paramètres de l’application entraîne le redémarrage de l’application.  

Après le redémarrage de l’application de fonction, elle sera connectée à un compte de stockage sécurisé.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Options de mise en réseau d’Azure Functions](functions-networking-options.md)
