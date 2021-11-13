---
title: Résoudre les problèmes de connectivité SMTP sortante dans Azure | Microsoft Docs
description: Découvrez la méthode recommandée pour l’envoi des e-mails et la manière de résoudre les problèmes de connectivité SMTP sortante dans Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/28/2021
ms.author: genli
ms.openlocfilehash: 5c4c129514ccf2fe3b55fc173d3fd4b87fd50715
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248960"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Résoudre les problèmes de connectivité SMTP sortante dans Azure

L’envoi d’e-mails sortants directement à des domaines externes (comme outlook.com et gmail.com) sur le port TCP 25 à partir d’une machine virtuelle est possible uniquement lorsque la machine virtuelle est déployée dans certains types d’abonnement.

> [!IMPORTANT]
> Pour tous les exemples ci-dessous, le processus s’applique *seulement* aux ressources de machines virtuelles et de groupes de machines virtuelles identiques (`Microsoft.Compute/virtualMachines` & `Microsoft.Compute/virtualMachineScaleSets`). L’envoi d’e-mails sur le port 25 n’est pris en charge depuis aucune des ressources PaaS (Platform-as-a-service) Azure, y compris [Azure App Service](https://azure.microsoft.com/services/app-service) et [Azure Functions](https://azure.microsoft.com/services/functions).

## <a name="recommended-method-of-sending-email"></a>Méthode recommandée de l’envoi d’e-mails

Nous vous recommandons d’utiliser des services de relais SMTP authentifiés pour envoyer des e-mails à partir de machines virtuelles Azure ou d’Azure App Service. (Ces services de relais se connectent généralement via le port TCP 587, mais ils prennent en charge d’autres ports.) Ces services sont utilisés pour maintenir la réputation de l’adresse IP et du domaine afin de minimiser la possibilité que des domaines externes rejettent vos messages ou les placent dans le dossier des courriers indésirables. [SendGrid](https://sendgrid.com/partners/azure/) est un service de relais SMTP, mais il en existe d’autres. Vous pouvez également disposer d’un service de relais SMTP authentifié sur vos serveurs locaux.

L’utilisation de ces services de remise d’e-mails n’est pas limitée dans Azure, quel que soit le type d’abonnement.

## <a name="enterprise-agreement"></a>Contrat Entreprise

Pour les machines virtuelles qui sont déployées dans des abonnements Contrat Entreprise, les connexions SMTP sortantes sur le port TCP 25 ne sont pas bloquées. Toutefois, il n’y a aucune garantie que les domaines externes accepteront les e-mails entrants provenant des machines virtuelles. Si vos e-mails sont rejetés ou filtrés par les domaines externes, vous devez contacter les fournisseurs de services de messagerie des domaines externes pour résoudre les problèmes. Ces problèmes ne sont pas couverts par le support Azure.

## <a name="pay-as-you-go"></a>Paiement à l’utilisation

La plateforme Azure bloque les connexions SMTP sortantes sur le port TCP 25 pour les machines virtuelles qui sont déployées dans les abonnements Paiement à l’utilisation. Il est possible de supprimer ce blocage si votre abonnement Azure est en règle et présente un historique de paiement suffisant. Vous pouvez demander la suppression de cette restriction en accédant à la section **Impossible d’envoyer un e-mail (SMTP/Port 25)** du panneau **Diagnostiquer et résoudre** pour une ressource Réseau virtuel Microsoft Azure dans le [portail Azure](https://portal.azure.com). 

Une fois qu’un abonnement Paiement à l’utilisation est exempté de ce blocage et que les machines virtuelles sont arrêtées et redémarrées dans le portail Azure, toutes les machines virtuelles de cet abonnement sont exemptées. L’exemption s’applique uniquement à l’abonnement demandé et au trafic de machine virtuelle acheminé directement vers Internet.

> [!NOTE]
> Microsoft se réserve le droit de révoquer ces exemptions s’il s’avère qu’une violation des conditions d’utilisation du service s’est produite.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Pass Azure, Azure dans Open, Azure for Education, Microsoft Azure for Students, Visual Studio et Essai gratuit

La plateforme Azure bloque les connexions SMTP sortantes sur le port TCP 25 pour les machines virtuelles déployées dans les types d’abonnement suivants :

- MSDN
- Pass Azure
- Azure dans Open
- Education
- Azure for Students
- Version d’évaluation gratuite
- Tout abonnement Visual Studio  

Les restrictions sont mises en place pour éviter les abus. Aucune demande de suppression de ces restrictions ne sera accordée.

Si vous utilisez ces types d’abonnements, nous vous invitons à utiliser un service de relais SMTP authentifié, comme indiqué plus haut dans cet article, ou à changer votre type d’abonnement.

## <a name="cloud-solution-provider"></a>Fournisseur de solutions cloud

La plateforme Azure bloque les connexions SMTP sortantes sur le port TCP 25 pour les machines virtuelles déployées dans des abonnement de fournisseur de solutions cloud. Il est possible de supprimer ce blocage. Pour demander la suppression du blocage, accédez à la section **Impossible d’envoyer un e-mail (SMTP/Port 25)** du panneau **Diagnostiquer et résoudre** pour une ressource Réseau virtuel Microsoft Azure dans le portail Azure et ouvrez une demande de support.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network, BizSpark Plus ou Azure Sponsorship

La plateforme Azure bloque les tentatives de livraison SMTP sortantes sur le port TCP 25 pour les machines virtuelles déployées dans les abonnement suivants :

- Microsoft Partner Network (MPN)
- BizSpark Plus
- Azure Sponsorship

Il est possible de supprimer ce blocage. Pour demander la suppression du blocage, accédez à la section **Impossible d’envoyer un e-mail (SMTP/Port 25)** du panneau **Diagnostiquer et résoudre** pour une ressource Réseau virtuel Microsoft Azure dans le portail Azure et ouvrez une demande de support.

Une fois que l’abonnement est exempté de ce blocage et que les machines virtuelles sont arrêtées et redémarrées, toutes les machines virtuelles de cet abonnement sont exemptées. L’exemption s’applique uniquement à l’abonnement demandé et au trafic de machine virtuelle acheminé directement vers Internet.

> [!NOTE]
> Microsoft se réserve le droit de révoquer ces exemptions s’il s’avère qu’une violation des conditions d’utilisation du service a eu lieu.

## <a name="changing-subscription-type"></a>Modification du type d’abonnement

Si vous remplacez votre type d’abonnement Contrat Entreprise par un autre type d’abonnement, les modifications apportées à vos déploiements peuvent entraîner le blocage du trafic SMTP sortant. Si vous prévoyez de remplacer votre type d’abonnement Contrat Entreprise par un autre type d’abonnement et que vous avez besoin du trafic SMTP sortant sur le port TCP 25, veillez à collaborer avec le support pour débloquer votre abonnement avant de modifier votre type d’abonnement.

## <a name="need-help-contact-support"></a>Vous avez besoin d’aide ? Contacter le support technique

Si vous avez encore besoin d’aide, [contactez le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour résoudre rapidement votre problème. Utilisez ce type de problème : **Technique** > **Réseau virtuel** > **Impossible d’envoyer un e-mail (SMTP/Port 25)** .
