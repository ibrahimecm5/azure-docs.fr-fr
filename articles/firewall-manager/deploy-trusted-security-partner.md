---
title: Déployer un fournisseur de partenaire de sécurité Azure Firewall Manager
description: Découvrez comment déployer un fournisseur de partenaire de sécurité Azure Firewall Manager à l’aide du portail Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 11/10/2021
ms.author: victorh
ms.openlocfilehash: 252a4e71a5fdcc823ab357e8528a50bd737ff1c2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283557"
---
# <a name="deploy-a-security-partner-provider"></a>Déployer un fournisseur de partenaire de sécurité

Dans Azure Firewall Manager, les *fournisseurs de partenaire de sécurité* vous permettent d’utiliser les meilleures offres SECaaS (sécurité en tant que service) tierces afin de protéger l’accès à Internet de vos utilisateurs.

Pour en savoir plus sur les scénarios pris en charge et les bonnes pratiques, consultez [Présentation des partenaires de sécurité](trusted-security-partners.md)


Les partenaires SECaaS (sécurité en tant que service) tiers intégrés sont désormais disponibles : 

- **Zscaler**
- **[Check Point](check-point-overview.md)**
- **iboss**

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Déployer un fournisseur de sécurité tiers dans un nouveau hub

Ignorez cette section si vous déployez un fournisseur tiers dans un hub existant.

1. Connectez-vous au portail Azure sur https://portal.azure.com.
2. Dans **Recherche**, tapez **Firewall Manager** et sélectionnez-le sous **Services**.
3. Accédez à **Prise en main**. Sélectionnez **Voir les hubs virtuels sécurisés**.
4. Sélectionnez **Créer un nouvel hub virtuel sécurisé**.
5. Entrez votre abonnement et votre groupe de ressources, sélectionnez une région prise en charge et ajoutez vos informations de réseau étendu virtuel et de hub. 
6. Sélectionnez **Inclure une passerelle VPN pour activer les fournisseurs de partenaire de sécurité**.
7. Sélectionnez les **Unités d’échelle de la passerelle** adaptées à vos besoins.
8. Sélectionnez **Suivant : Pare-feu Azure**
   > [!NOTE]
   > Les partenaires de sécurité se connectent à votre hub à l’aide de tunnels de passerelle VPN. Si vous supprimez la passerelle VPN, les connexions à vos partenaires de sécurité seront perdues.
9. Si vous souhaitez déployer Pare-feu Azure pour filtrer le trafic privé et le fournisseur de services tiers pour filtrer le trafic Internet, sélectionnez une stratégie pour Pare-feu Azure. Consultez les scénarios [pris en charge](trusted-security-partners.md#key-scenarios).
10. Si vous souhaitez déployer uniquement un fournisseur de sécurité tiers dans le hub, définissez l’option **Pare-feu Azure : Activé/désactivé** sur **Désactivé**. 
11. Sélectionnez **Suivant : Fournisseur de partenaire de sécurité**.
12. Réglez le **Fournisseur de partenaire de sécurité** sur **Activé**. 
13. Sélectionnez un partenaire. 
14. Sélectionnez **Suivant : Vérifier + créer**. 
15. Passez en revue le contenu, puis sélectionnez **Créer**.

Le déploiement de la passerelle VPN peut prendre plus de 30 minutes.

Pour vérifier que le hub a été créé, accédez à Azure Firewall Manager->Hubs sécurisés. Sélectionnez le hub->page Vue d’ensemble pour afficher le nom du partenaire et l’état (**Connexion sécurisée en attente**).

Une fois le hub créé et le partenaire de sécurité configuré, connectez le fournisseur de sécurité au hub.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Déployer un fournisseur de sécurité tiers dans un hub existant

Vous pouvez également sélectionner un hub existant dans un réseau étendu virtuel et le convertir en *hub virtuel sécurisé*.

1. Dans **Prise en main**, sélectionnez **Voir les hubs virtuels sécurisés**.
2. Sélectionnez **Convertir les hubs existants**.
3. Sélectionnez un abonnement et un hub existant. Suivez les étapes restantes pour déployer un fournisseur tiers dans un nouveau hub.

N’oubliez pas qu’une passerelle VPN doit être déployée pour convertir un hub existant en hub sécurisé avec des fournisseurs tiers.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Configurer des fournisseurs de sécurité tiers pour qu’ils se connectent à un hub sécurisé

Pour que vous puissiez configurer des tunnels vers la passerelle VPN de votre hub virtuel, les fournisseurs tiers doivent disposer de droits d’accès à votre hub. Pour ce faire, associez un principal de service à votre abonnement ou groupe de ressources, puis accordez des droits d’accès. Vous devez ensuite fournir ces informations d’identification au tiers à l’aide de son portail.

> [!NOTE]
> Les fournisseurs de sécurité tiers créent un site VPN en votre nom. Ce site VPN n’apparaît pas dans le portail Azure.

### <a name="create-and-authorize-a-service-principal"></a>Créer et autoriser un principal de service

1. Créer un principal de service Azure Active Directory (AD) : vous pouvez ignorer l’URL de redirection. 

   [Procédure : Utiliser le portail pour créer une application Azure AD et un principal de service ayant accès aux ressources](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
2. Ajoutez des droits d’accès et une étendue pour le principal de service.
   [Procédure : Utiliser le portail pour créer une application Azure AD et un principal de service ayant accès aux ressources](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)

   > [!NOTE]
   > Pour un contrôle plus précis, vous pouvez limiter l’accès à votre groupe de ressources.

### <a name="visit-partner-portal"></a>Visiter le portail partenaires

1. Suivez les instructions fournies par votre partenaire pour terminer l’installation. Cela comprend l’envoi d’informations AAD pour détecter le hub et s’y connecter, mettre à jour les stratégies de sortie et vérifier l’état de la connectivité et les journaux d'activité.

   - [Zscaler : configurer une intégration Microsoft Azure Virtual WAN](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration).
   - [Check Point : Configurer une intégration Microsoft Azure Virtual WAN](https://sc1.checkpoint.com/documents/Infinity_Portal/WebAdminGuides/EN/CloudGuard-Connect-Azure-Virtual-WAN/Default.htm).
   - [iboss : Configurer une intégration Microsoft Azure Virtual WAN](https://www.iboss.com/blog/securing-microsoft-azure-with-iboss-saas-network-security). 
   
2. Vous pouvez consulter l’état de la création du tunnel sur le portail Azure Virtual WAN dans Azure. Une fois que l’état des tunnels est **connecté** à la fois sur Azure et sur le portail partenaire, passez aux étapes suivantes pour configurer des routes afin de sélectionner les filiales et les réseaux virtuels qui doivent envoyer le trafic Internet au partenaire.

## <a name="configure-security-with-firewall-manager"></a>Configurer la sécurité avec Firewall Manager

1. Accédez à Azure Firewall Manager -> Hubs sécurisés. 
2. Sélectionnez un hub. L’état du hub doit maintenant indiquer **Provisionné** au lieu de **Connexion sécurisée en attente**.

   Vérifiez que le fournisseur tiers peut se connecter au hub. Les tunnels sur la passerelle VPN doivent se trouver dans un état **connecté**. Cet état est plus révélateur de l’intégrité de la connexion entre le hub et le partenaire tiers que l’état précédent.
3. Sélectionnez le hub et accédez à **Configurations de sécurité**.

   Quand vous déployez un fournisseur tiers sur le hub, il convertit ce dernier en *hub virtuel sécurisé*. Ainsi, le fournisseur tiers publie une route 0.0.0.0/0 (par défaut) vers le hub. Toutefois, les connexions de réseau virtuel et les sites connectés au hub n’obtiennent pas cette route par défaut, sauf si vous choisissez les connexions qui doivent l’obtenir.

   > [!NOTE]
   > Ne créez pas manuellement un itinéraire 0.0.0.0/0 (par défaut) sur le protocole BGP pour les publications de branche. Cela s’effectue automatiquement pour les déploiements sécurisés de hub virtuel avec les fournisseurs de sécurité tiers. Cela risque de perturber le processus de déploiement.

4. Configurez la sécurité WAN virtuelle en définissant le **Trafic Internet** via le Pare-feu Azure et le **Trafic privé** via un partenaire de sécurité approuvé. Cela sécurise automatiquement les connexions individuelles dans le WAN virtuel.

   :::image type="content" source="media/deploy-trusted-security-partner/security-configuration.png" alt-text="Configuration de la sécurité":::
5. De plus, si votre organisation utilise des plages d’adresses IP publiques dans des réseaux virtuels et des filiales, vous devez spécifier ces préfixes IP explicitement à l’aide de **Préfixes de trafic privé**. Les adresses IP publiques peuvent être spécifiées individuellement ou en tant qu’agrégats.

   Si vous utilisez des adresses non RFC1918 pour vos préfixes de trafic privé, vous devrez peut-être configurer des stratégies SNAT pour votre pare-feu pour désactiver SNAT pour le trafic privé non RFC1918. Par défaut, le pare-feu Azure SNATs considère tout le trafic non RFC1918.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Trafic Internet de filiale ou de réseau virtuel via un service tiers

Ensuite, vous pouvez vérifier si des machines virtuelles de réseau virtuel ou le site de filiale peuvent accéder à Internet et vérifier que le trafic est acheminé vers le service tiers.

Une fois les étapes du paramétrage des itinéraires terminées, les machines virtuelles de réseau virtuel ainsi que les sites de filiale reçoivent une route 0/0 vers le service tiers. Vous ne pouvez pas utiliser le protocole RDP ou SSH dans ces machines virtuelles. Pour vous connecter, vous pouvez déployer le service [Azure Bastion](../bastion/bastion-overview.md) dans un réseau virtuel appairé.

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : Sécuriser votre réseau cloud avec Azure Firewall Manager à l’aide du Portail Azure](secure-cloud-network.md)