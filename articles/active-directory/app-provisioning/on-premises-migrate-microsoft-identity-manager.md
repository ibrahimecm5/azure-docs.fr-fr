---
title: Exporter un connecteur Microsoft Identity Manager à utiliser avec l’Hôte de connecteur ECMA Azure AD
description: Décrit la procédure de création et d’exportation d’un connecteur depuis MIM Sync, à utiliser avec l’Hôte de connecteur ECMA Azure AD.
services: active-directory
author: billmath
manager: karenh444
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/11/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6659e28e1f2814f8bf2b0f08e21db25edae3dd66
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370685"
---
# <a name="export-a-microsoft-identity-manager-connector-for-use-with-the-azure-ad-ecma-connector-host"></a>Exporter un connecteur Microsoft Identity Manager à utiliser avec l’Hôte de connecteur ECMA Azure AD

>[!IMPORTANT]
> Actuellement, la préversion du provisionnement local n’est disponible que sur invitation. Pour demander l’accès à la fonctionnalité, utilisez le [formulaire de demande d’accès](https://aka.ms/onpremprovisioningpublicpreviewaccess). Nous allons ouvrir la préversion à un plus grand nombre de clients et de connecteurs au cours des prochains mois, car nous préparons la disponibilité générale.

Vous pouvez importer dans l’Hôte de connecteur ECMA Azure AD (Azure Active Directory) la configuration d’un connecteur spécifique à partir d’une installation du service de synchronisation de Forefront Identity Manager (FIM Sync) ou du service de synchronisation de Microsoft Identity Manager (MIM Sync). L’installation de MIM Sync est uniquement utilisée à des fins de configuration, et non pour la synchronisation continue à partir d’Azure AD.

>[!IMPORTANT]
>Pour le moment, seul les connecteur SQL et LDAP génériques sont pris en charge afin d’être utilisés avec l’hôte du connecteur ECMA Azure AD.

## <a name="create-and-export-a-connector-configuration-in-mim-sync"></a>Créer et exporter une configuration de connecteur dans MIM Sync
Si vous avez déjà configuré MIM Sync avec votre connecteur ECMA, passez à l’étape 10.

 1. Préparez un serveur Windows Server 2016, qui est différent du serveur qui sera utilisé pour l’exécution de l’Hôte de connecteur ECMA Azure AD. Ce serveur hôte doit avoir une base de données SQL Server 2016 colocalisée ou disposer d’une connectivité réseau vers une base de données SQL Server 2016. L’une des façons de configurer ce serveur consiste à déployer une machine virtuelle Azure avec l’image **SQL Server 2016 SP1 Standard sur Windows Server 2016**. Ce serveur n’a pas besoin d’une connectivité Internet, sauf pour l’accès au Bureau à distance à des fins de configuration.
 1. Créez un compte à utiliser lors de l’installation de la synchronisation MIM. Il peut s’agir d’un compte local sur cette instance de Windows Server. Pour créer un compte local, ouvrez **Panneau de configuration** > **Comptes d’utilisateur**, puis ajoutez le compte d’utilisateur **mimsync**.
 1. Ajoutez le compte créé à l’étape précédente au groupe Administrateurs local.
 1. Donnez au compte créé précédemment la possibilité d’exécuter un service. Démarrez **Stratégie de sécurité locale**, puis sélectionnez **Stratégies locales** > **Attribution des droits utilisateur** > **Ouvrir une session en tant que service**. Ajoutez le compte mentionné précédemment.
 1. Installez la synchronisation MIM sur cet hôte. Si vous ne disposez pas des fichiers binaires de MIM Sync, vous pouvez installer une version d’évaluation en téléchargeant le fichier zip à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=48244), en montant l’image ISO, puis en copiant le dossier **Service de synchronisation** vers l’hôte Windows Server. Exécutez ensuite le programme d’installation contenu dans ce dossier. Le logiciel d’évaluation est limité dans le temps et a une date d’expiration. Il n’est pas destiné à un usage en production.
 1. Une fois l’installation de MIM Sync effectuée, déconnectez-vous, puis reconnectez-vous.
 1. Installez votre connecteur sur le même serveur que celui de MIM Sync. À des fins d’illustration, ce guide de labo de test décrit l’utilisation de l’un des connecteurs fournis par Microsoft, que vous pouvez télécharger à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=51495).
 1. Démarrez l’IU du service de synchronisation. Sélectionnez **Agents de gestion**. Sélectionnez **Créer**, puis spécifiez l’agent de gestion de connecteur. Veillez à sélectionner un agent de gestion de connecteur basé sur ECMA.
 1. Donnez un nom au connecteur et configurez les paramètres nécessaires à l’importation et à l’exportation des données dans le connecteur. Veillez à spécifier que le connecteur peut importer et exporter des attributs de chaîne à valeur unique d’un type d’objet utilisateur ou personne.
 1. Sur l’ordinateur serveur de MIM Sync, démarrez l’IU du service de synchronisation, si elle n’est pas déjà en cours d’exécution. Sélectionnez **Agents de gestion**.
 1. Sélectionnez le connecteur, puis **Exporter l’agent de gestion**. Enregistrez le fichier XML ainsi que la DLL et les logiciels associés de votre connecteur sur le serveur Windows qui doit accueillir l’Hôte de connecteur ECMA.

À ce stade, le serveur de synchronisation MIM n’est plus nécessaire.

 1. Connectez-vous au serveur Windows en utilisant le compte sous lequel l’Hôte de connecteur ECMA Azure AD va s’exécuter.
 1. Accédez au répertoire C:\Program Files\Microsoft ECMA2host\Service\ECMA. Vérifiez qu’une ou plusieurs DLL sont déjà présentes dans ce répertoire. Ces DLL correspondent aux connecteurs fournis par Microsoft.
 1. Copiez la DLL MA de votre connecteur, ainsi que l’une de ses DLL prérequises, dans le même sous-répertoire ECMA du répertoire du service.
 1. Accédez au répertoire C:\Program Files\Microsoft ECMA2Host\Wizard. Exécutez le programme Microsoft.ECMA2Host.ConfigWizard.exe pour installer la configuration de l’Hôte de connecteur ECMA.
 1. Une nouvelle fenêtre apparaît avec une liste de connecteurs. Par défaut, aucun connecteur n’est présent. Sélectionnez **Nouveau connecteur**.
 1. Spécifiez le fichier XML de l’agent de gestion, qui a été exporté à partir de MIM Sync. Continuez en suivant les instructions de configuration et de mise en correspondance du schéma fournies dans la section qui explique comment configurer un connecteur.

## <a name="next-steps"></a>Étapes suivantes

- [Provisionnement d’applications](user-provisioning.md)
- [Connecteur SQL générique](on-premises-sql-connector-configure.md)
