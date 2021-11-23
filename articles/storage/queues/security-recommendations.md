---
title: Recommandations de sécurité pour le Stockage File d’attente
titleSuffix: Azure Storage
description: Découvrez les recommandations de sécurité pour le Stockage File d’attente. La mise en œuvre de ces conseils vous aidera à répondre à vos obligations de sécurité, comme décrit dans notre modèle de responsabilité partagée.
author: tamram
services: storage
ms.author: tamram
ms.date: 03/11/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: queues
ms.custom: security-recommendations
ms.openlocfilehash: 0fdd17b924fe8588e0ece24fc185456bd5dc3bc5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312146"
---
# <a name="security-recommendations-for-queue-storage"></a>Recommandations de sécurité pour le Stockage File d’attente

Cet article contient des recommandations de sécurité pour le Stockage File d’attente. Mettez en œuvre ces recommandations pour répondre à vos obligations de sécurité comme décrit dans notre modèle de responsabilité partagée. Pour plus d’informations sur les mesures prises par Microsoft pour répondre à ses responsabilités de fournisseur de services, lisez [Responsabilités partagées pour le cloud computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225366/1/Shared%20Responsibility%20for%20Cloud%20Computing-2019-10-25.pdf).

Certaines recommandations contenues dans cet article peuvent être supervisées automatiquement par Microsoft Defender pour le cloud. Microsoft Defender pour le cloud est la première ligne de défense dans la protection de vos ressources dans Azure. Pour en savoir plus sur Microsoft Defender pour le cloud, consultez [Qu’est-ce que Microsoft Defender pour le cloud ?](../../security-center/security-center-introduction.md).

Microsoft Defender pour le cloud analyse périodiquement l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités de sécurité potentielles. Il fournit ensuite des recommandations sur la façon de les corriger. Pour plus d’informations sur la manière d’appliquer des recommandations, consultez [Implémentation des recommandations de sécurité dans Microsoft Defender pour le cloud](../../security-center/security-center-recommendations.md).

## <a name="data-protection"></a>Protection de données

| Recommandation | Commentaires | Defender pour le cloud |
|-|----|--|
| Utiliser le Modèle de déploiement Azure Resource Manager | Créez des comptes de stockage en utilisant le modèle de déploiement Azure Resource Manager pour les améliorations de sécurité importantes, notamment les fonctionnalités de contrôle d’accès en fonction du rôle Azure (Azure RBAC) et d’audit de premier plan, le déploiement et la gouvernance basés sur Resource Manager, l’accès aux identités managées, l’accès pour Azure Key Vault pour les secrets, et l’authentification et l’autorisation basées sur Azure AD pour l’accès aux données et aux ressources de Stockage Azure. Si possible, migrez les comptes de stockage existants qui utilisent le Modèle de déploiement classique pour qu’ils utilisent Azure Resource Manager. Pour plus d’informations sur Azure Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](../../azure-resource-manager/management/overview.md). | - |
| Activez Advanced Threat Protection pour tous vos comptes de stockage | [Microsoft Defender pour stockage](../../security-center/defender-for-storage-introduction.md) fournit une couche supplémentaire d’informations de sécurité qui détecte les tentatives inhabituelles et potentiellement dangereuses d’accès ou d’exploitation des comptes de stockage. Les alertes de sécurité sont déclenchées dans Microsoft Defender pour le cloud quand des anomalies se produisent dans des activités ; elles sont également envoyées par e-mail aux administrateurs d’abonnement, avec les détails des activités suspectes et des recommandations sur la façon d’examiner et de remédier aux menaces. Pour plus d’informations, consultez [Protection de vos comptes Stockage Azure](../../security-center/defender-for-storage-introduction.md). | [Oui](../../security-center/security-center-remediate-recommendations.md) |
| Limiter les jetons de signature d’accès partagé (SAS) aux seules connexions HTTPS | Le fait d’exiger HTTPS lorsqu’un client utilise un jeton SAS pour accéder à des données de file d’attente permet de réduire le risque d’écoute. Pour plus d’informations, consultez [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAS)](../common/storage-sas-overview.md). | - |

## <a name="identity-and-access-management"></a>Gestion de l’identité et de l’accès

| Recommandation | Commentaires | Defender pour le cloud |
|-|----|--|
| Utiliser Azure Active Directory (Azure AD) pour autoriser l’accès aux données d’objet de file d’attente | Azure AD offre une sécurité et une facilité d’utilisation supérieures à celles de l’autorisation Clé partagée pour l’autorisation des requêtes envoyées au Stockage File d’attente. Pour plus d’informations, consultez [Autoriser l’accès aux données dans Stockage Azure](../common/authorize-data-access.md). | - |
| Garder à l’esprit le principe du moindre privilège lors de l’attribution d’autorisations à un principal de sécurité Azure AD via Azure RBAC | Quand vous attribuez un rôle à un utilisateur, un groupe ou une application, accordez à ce principal de sécurité seulement les autorisations nécessaires pour effectuer ses tâches. La limitation de l’accès aux ressources permet d’éviter une mauvaise utilisation accidentelle et malveillante de vos données. | - |
| Sécuriser vos clés d’accès de compte avec Azure Key Vault | Microsoft recommande d’utiliser Azure AD pour autoriser les demandes auprès de Stockage Azure. Cependant, si vous devez utiliser l’autorisation Clé partagée, sécurisez vos clés de compte avec Azure Key Vault. Vous pouvez récupérer les clés du coffre de clés au Moment de l’exécution, au lieu de les enregistrer avec votre application. | - |
| Regénérer régulièrement vos clés de compte | Effectuer une rotation des clés de compte régulièrement réduit le risque d’exposer vos données à des acteurs malveillants. | - |
| Gardez à l’esprit le principe du Moindre privilège lors de l’attribution d’autorisations à une signature d’accès partagé | Lors de la création d’une signature d’accès partagé, spécifiez seulement les autorisations nécessaires au client pour remplir sa fonction. La limitation de l’accès aux ressources permet d’éviter une mauvaise utilisation accidentelle et malveillante de vos données. | - |
| Disposer d’un plan de révocation pour les signatures d’accès partagé que vous émettez pour les clients | Si une signature d’accès partagé est compromise, vous voudrez révoquer cette signature dès que possible. Pour révoquer une signature d’accès partagé de délégation d’utilisateur, révoquez la clé de délégation d’utilisateur pour invalider rapidement toutes les signatures associées à cette clé. Pour révoquer une signature d’accès partagé de service associée à une stratégie d’accès stockée, vous pouvez supprimer la stratégie d’accès stockée, renommer la stratégie ou changer son délai d’expiration pour le fixer à un Moment du passé. Pour plus d’informations, consultez [Accorder un accès limité aux ressources du Stockage Azure à l’aide des signatures d’accès partagé (SAS)](../common/storage-sas-overview.md).  | - |
| Si une signature d’accès partagé de service n’est pas associée à une stratégie d’accès stockée, définissez le délai d’expiration sur une heure ou Moins. | Une signature d’accès partagé de service qui n’est pas associée à une stratégie d’accès stockée ne peut pas être révoquée. Pour cette raison, il est recommandé de limiter le délai d’expiration afin que la signature d’accès partagé soit valide pendant une heure au maximum. | - |

## <a name="networking"></a>Mise en réseau

| Recommandation | Commentaires | Defender pour le cloud |
|-|----|--|
| Configurer la version minimale requise du protocole TLS (Transport Layer Security) pour un compte de stockage.  | Exiger que les clients utilisent une version plus sécurisée de TLS pour effectuer des requêtes sur un compte de stockage Azure en configurant la version minimale de TLS pour ce compte. Pour plus d’informations, consultez [Configure minimum required version of Transport Layer Security (TLS) for a storage account](../common/transport-layer-security-configure-minimum-version.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) (Configurer la version minimale requise du protocole TLS pour un compte de stockage)| - |
| Activer l’option **Transfert sécurisé requis** sur tous vos comptes de stockage | Quand vous activez l’option **Transfert sécurisé requis**, toutes les demandes effectuées auprès du compte de stockage doivent se faire via des connexions sécurisées. Toutes les demandes effectuées sur HTTP échouent. Pour plus d’informations, consultez [Exiger un transfert sécurisé dans Stockage Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | [Oui](../../security-center/security-center-remediate-recommendations.md) |
| Activer des règles de pare-feu | Configurez des règles de pare-feu pour limiter l’accès à votre compte de stockage aux requêtes provenant d’adresses IP ou de plages d’adresses IP spécifiées, ou d’une liste de sous-réseaux dans un réseau virtuel Azure (VNet). Pour plus d’informations sur la configuration des règles de pare-feu, consultez [Configurer les pares-feu et réseaux virtuels dans le stockage Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). | - |
| Autoriser les services Microsoft approuvés à accéder au compte de stockage | L’activation des règles de pare-feu pour votre compte de stockage bloque les requêtes entrantes pour les données par défaut, sauf si les requêtes proviennent d’un service qui fonctionne au sein d’un réseau virtuel (VNet) Azure ou d’adresses IP publiques autorisées. Les demandes qui sont bloquées comprennent les demandes émanant d’autres services Azure, du portail Azure, des services de journalisation et de métriques, etc. Vous pouvez autoriser les demandes provenant d’autres services Azure en ajoutant une exception pour autoriser les services Microsoft approuvés à accéder au compte de stockage. Pour plus d’informations sur l’ajout d’une exception pour les services Microsoft approuvés, consultez [Configurer les pares-feu et réseaux virtuels dans le stockage Azure](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).| - |
| Utiliser des points de terminaison privés | Un point de terminaison privé affecte une adresse IP privée de votre réseau virtuel Azure au compte de stockage. Il sécurise tout le trafic entre votre réseau virtuel et le compte de stockage via Private Link. Pour plus d’informations sur les points de terminaison privés, consultez [Connexion privée à un compte de stockage à l’aide d’Azure Private Endpoint](../../private-link/tutorial-private-endpoint-storage-portal.md). | - |
| Utiliser des balises de service de réseau virtuel | Une balise de service représente un groupe de préfixes d’adresses IP d’un service Azure donné. Microsoft gère les préfixes d’adresse englobés par la balise de service et met à jour automatiquement la balise de service quand les adresses changent. Pour plus d’informations sur les balises de service prises en charge par Stockage Azure, consultez [Vue d’ensemble des balises de service Azure](../../virtual-network/service-tags-overview.md). Pour un tutoriel qui montre comment utiliser les balises de service pour créer des règles de réseau sortant, consultez [Restreindre l’accès aux ressources PaaS](../../virtual-network/tutorial-restrict-network-access-to-resources.md). | - |
| Limiter l’accès réseau à des réseaux spécifiques | La limitation de l’accès réseau aux réseaux hébergeant des clients nécessitant un accès réduit l’exposition de vos ressources aux attaques réseau. | [Oui](../../security-center/security-center-remediate-recommendations.md) |

## <a name="logging-and-monitoring"></a>Enregistrement et surveillance

| Recommandation | Commentaires | Defender pour le cloud |
|-|----|--|
| Suivre les autorisations des demandes | Activez la journalisation de Stockage Azure pour suivre comment chaque demande effectuée auprès de Stockage Azure était autorisée. Les journaux indiquent si une requête a été effectuée anonymement, en utilisant un jeton OAuth 2.0, avec une clé partagée ou avec une signature d’accès partagé (SAS). Pour plus d’informations, consultez [Journalisation Azure Storage Analytics](../common/storage-analytics-logging.md). | - |

## <a name="next-steps"></a>Étapes suivantes

- [Documentation sur la sécurité Azure](../../security/index.yml)
- [Documentation sur le développement sécurisé](../../security/develop/index.yml).
