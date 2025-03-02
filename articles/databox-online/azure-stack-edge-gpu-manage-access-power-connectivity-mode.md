---
title: Accès, puissance et mode de connectivité des appareils avec GPU Azure Stack Edge Pro | Microsoft Docs
description: Explique comment gérer l’accès, la puissance et le mode de connectivité de l’appareil avec GPU Azure Stack Edge Pro qui permet de transférer des données sur Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/08/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 97ba43602899ee60a6a2d24cdd699b12b8a1c687
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219706"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge-pro-gpu"></a>Gérer l’accès, la puissance et le mode de connectivité de votre GPU Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article explique comment gérer l’accès, la puissance et le mode de connectivité de l’appareil avec GPU Azure Stack Edge Pro. Ces opérations sont effectuées sur l’interface utilisateur web locale ou le Portail Azure.

Cet article s'applique aux appareils Azure Stack Edge Pro GPU, Azure Stack Edge Pro R et Azure Stack Edge Mini R.


Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Gérer l’accès aux appareils
> * Gérer l’accès aux ressources
> * Gérer le mode de connectivité
> * Gérer la puissance


## <a name="manage-device-access"></a>Gérer l’accès aux appareils

L’accès à votre appareil Azure Stack Edge Pro est contrôlé par l’utilisation d’un mot de passe d’appareil. Vous pouvez changer ce mot de passe à l’aide de l’interface utilisateur web locale. Vous pouvez également le réinitialiser sur le portail Azure.

L'accès aux données figurant sur les disques de l'appareil est également contrôlé par des clés de chiffrement au repos. 

Vous pouvez accéder à l’appareil en ouvrant une session Remote PowerShell sur HTTP ou HTTPS à partir de l’interface utilisateur web locale de l’appareil.

### <a name="change-device-password"></a>Modifier le mot de passe d’un appareil

Suivez ces étapes sur l’interface utilisateur locale pour changer le mot de passe de l’appareil.

1. Sur l’interface utilisateur web locale, accédez à **Maintenance > Mot de passe**.
2. Entrez le mot de passe actuel, puis le nouveau. Le mot de passe indiqué doit comporter entre 8 et 16 caractères, d’au moins trois types : majuscule, minuscule, chiffre et caractère spécial. Confirmez le nouveau mot de passe.

    ![Modifier le mot de passe](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/change-password-1.png)

3. Sélectionnez **Modifier le mot de passe**.
 
### <a name="reset-device-password"></a>Réinitialiser le mot de passe de l’appareil

Le workflow de réinitialisation n’implique pas de se rappeler l’ancien mot de passe, ce qui est utile en cas de perte. Il s’effectue sur le Portail Azure.

1. Sur le Portail Azure, accédez à **Vue d’ensemble > Réinitialiser le mot de passe d’administrateur**.

    ![Capture d’écran montrant Réinitialisation du mot de passe de l’appareil sélectionné.](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/reset-password-1.png)


2. Entrez le nouveau mot de passe, puis confirmez-le. Le mot de passe indiqué doit comporter entre 8 et 16 caractères, d’au moins trois types : majuscule, minuscule, chiffre et caractère spécial. Sélectionnez **Réinitialiser**.

    ![Réinitialiser le mot de passe 2](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

### <a name="manage-access-to-device-data"></a>Gérer l'accès aux données des appareils

L'accès aux données des appareils Azure Stack Edge Pro R et Azure Stack Edge Mini R est contrôlé à l'aide de clés de chiffrement au repos pour les lecteurs. Une fois que vous avez correctement configuré l'appareil pour le chiffrement au repos, l'option de rotation des clés de chiffrement au repos devient disponible dans l'interface utilisateur locale de l'appareil. 

Cette opération vous permet de modifier les clés des volumes BitLocker `HcsData` et `HcsInternal` et de tous les lecteurs à chiffrement automatique de votre appareil.

Procédez comme suit pour effectuer une rotation des clés de chiffrement au repos.

1. Dans l'interface utilisateur locale de l'appareil, accédez à la page **Démarrage**. Sur la vignette **Sécurité**, sélectionnez **Chiffrement au repos : Rotation des clés**. Cette option n'est disponible qu'après avoir correctement configuré les clés de chiffrement au repos.

    ![Sur la page Démarrage, sélectionnez Rotation des clés pour le chiffrement au repos.](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-1.png)

1. Vous pouvez utiliser vos propres clés BitLocker ou utiliser les clés générées par le système.  

    Pour fournir votre propre clé, entrez une chaîne codée en base 64 de 32 caractères. L'entrée est semblable à celle que vous pourriez fournir lors de la toute première configuration du chiffrement au repos.

    ![Fournir votre propre clé de chiffrement au repos](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-2.png)

    Vous pouvez également choisir d'utiliser une clé générée par le système.

    ![Utiliser la clé de chiffrement au repos générée par le système](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-3.png)

1. Sélectionnez **Appliquer**. Les protecteurs de clés font l'objet d'une rotation.

    ![Appliquer la nouvelle clé de chiffrement au repos](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-4.png)

1. Lorsque vous êtes invité à télécharger et à enregistrer le fichier de clé, sélectionnez **Télécharger et continuer**. 

    ![Télécharger le fichier de clé et continuer](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-5.png)

    Enregistrez le fichier de clé `.json` à un emplacement sécurisé. Ce fichier est destiné à faciliter une éventuelle récupération future de l'appareil.

    ![Capture d’écran montrant la boîte de dialogue Réinitialisation du mot de passe de l’appareil.](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="enable-device-access-via-remote-powershell-over-http"></a>Activer l’accès de l’appareil via Remote PowerShell sur HTTP

Vous pouvez ouvrir une session Remote PowerShell à partir de votre appareil sur HTTP ou HTTPS. Par défaut, vous accédez à l’appareil via une session Remote PowerShell sur HTTPS. Dans des réseaux de confiance, il est acceptable d’activer Remote PowerShell sur HTTP.

Pour activer Remote PowerShell sur HTTP, dans l’interface utilisateur locale, procédez comme suit :

1. Dans l’interface utilisateur locale de votre appareil, dans l’angle supérieur droit de la page, accédez à **Paramètres**.
1. Sélectionnez **Activer** afin de pouvoir ouvrir une session Remote PowerShell sur HTTP à partir de votre appareil. N’activez ce paramètre que dans des réseaux de confiance.

    ![Capture d’écran montrant le paramètre permettant d’activer Remote PowerShell sur HTTP.](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/enable-remote-powershell-http-1.png)

1. Sélectionnez **Appliquer**.

Vous pouvez maintenant vous connecter sur HTTP à l’interface PowerShell de l’appareil. Pour plus de détails, consultez [Connexion à l’interface PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

## <a name="manage-resource-access"></a>Gérer l’accès aux ressources

Pour créer votre ressource Azure Stack Edge/Data Box Gateway, IoT Hub et Stockage Azure, vous devez disposer d’autorisations en tant que contributeur ou d’autorisations plus élevées au niveau du groupe de ressources. Les fournisseurs de ressources correspondants doivent également être inscrits. Pour toutes les opérations qui impliquent des informations d’identification et une clé d’activation, des autorisations sur l’API Microsoft Graph sont également exigées. Elles sont décrites dans les sections suivantes. 

### <a name="manage-microsoft-graph-api-permissions"></a>Gérer les autorisations pour l’API Microsoft Graph

Quand vous générez la clé d’activation pour l’appareil Azure Stack Edge Pro ou que vous effectuez des opérations qui nécessitent des informations d’identification, vous devez disposer d’autorisations sur l’API Graph d’Azure Active Directory. Les opérations nécessitant des informations d’identification peuvent être les suivantes :

-  Création d’un partage avec un compte de stockage associé.
-  Création d’un utilisateur qui peut accéder aux partages se trouvant sur l’appareil.

Vous devez avoir un accès `User` sur le locataire Active Directory car vous devez être en mesure de `Read all directory objects`. Vous ne pouvez pas être un utilisateur Invité car ce type d’utilisateur ne dispose pas d’autorisations pour `Read all directory objects`. Si vous êtes un invité, les opérations telles que la génération d’une clé d’activation, la création d’un partage sur votre appareil Azure Stack Edge Pro, la création d’un utilisateur, la configuration d’un rôle de calcul Edge et la réinitialisation du mot de passe de l’appareil sont toutes vouées à l’échec.

Pour plus d’informations sur la façon de fournir aux utilisateurs un accès à l’API Microsoft Graph, consultez [Informations de référence sur les autorisations Microsoft Graph](/graph/permissions-reference).

### <a name="register-resource-providers"></a>Inscrire des fournisseurs de ressources

Pour provisionner une ressource dans Azure (dans le modèle Azure Resource Manager), vous avez besoin d’un fournisseur de ressources qui prend en charge la création de cette ressource. Par exemple, pour approvisionner une machine virtuelle, vous devez avoir un fournisseur de ressources « Microsoft.Compute » disponible dans l’abonnement.
 
Les fournisseurs de ressources sont inscrits sur le niveau de l’abonnement. Par défaut, tout nouvel abonnement Azure est préinscrit auprès d’une liste de fournisseurs de ressources couramment utilisés. Le fournisseur de ressources pour « Microsoft.DataBoxEdge » n’est pas inclus dans cette liste.

Vous n’avez pas besoin d’accorder des autorisations d’accès au niveau de l’abonnement pour que les utilisateurs puissent créer des ressources comme « Microsoft.DataBoxEdge » dans leurs groupes de ressources sur lesquels ils disposent de droits de propriétaire, à condition que les fournisseurs de ces ressources soient déjà inscrits.

Avant d’essayer de créer une ressource, vérifiez que le fournisseur de ressources est inscrit dans l’abonnement. Si le fournisseur de ressources n’est pas inscrit, vous devez vérifier que l’utilisateur qui crée la ressource dispose de droits suffisants pour inscrire le fournisseur de ressources nécessaire sur le niveau de l’abonnement. Si vous ne l’avez pas fait également, l’erreur suivante s’affiche :

*L’abonnement \<Subscription name> ne dispose pas des autorisations nécessaires pour inscrire le fournisseur de ressources : Microsoft.DataBoxEdge.*


Pour obtenir une liste des fournisseurs de ressources inscrits dans l’abonnement actuel, exécutez la commande suivante :

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Pour un appareil Azure Stack Edge Pro, `Microsoft.DataBoxEdge` doit être inscrit. Pour inscrire `Microsoft.DataBoxEdge`, l’administrateur de l’abonnement doit exécuter la commande suivante :

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Pour plus d’informations sur la façon d’inscrire un fournisseur de ressources, consultez [Résoudre les erreurs d’inscription de fournisseurs de ressources](../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="manage-connectivity-mode"></a>Gérer le mode de connectivité

Hormis le mode entièrement connecté par défaut, votre appareil peut également s’exécuter en mode partiellement déconnecté ou entièrement déconnecté. :

- **Entièrement connecté** : c’est le mode normal par défaut dans lequel l’appareil fonctionne. Le chargement et le téléchargement cloud de données sont tous les deux activés dans ce mode. Vous pouvez utiliser le portail Azure ou l’interface utilisateur web locale pour gérer l’appareil. 

    > [!NOTE]
    > Pour les déploiements de Network Function Manager, l’appareil Azure Stack Edge doit être **en ligne** et en mode Entièrement connecté.

- **Partiellement déconnecté** : dans ce mode, l’appareil ne peut pas charger ni télécharger de données de partage. Vous pouvez gérer l’appareil via le portail Azure.

    Ce mode est généralement utilisé sur un réseau satellite limité, dans l’objectif de réduire la consommation de bande passante réseau. Une consommation réseau minimale peut subsister pour les opérations de monitoring des appareils.

- **Déconnecté** – Dans ce mode, l’appareil est entièrement déconnecté du cloud ; les chargements et téléchargements cloud sont désactivés. L’appareil n’est gérable que sur l’interface utilisateur web locale.

    Ce mode est généralement utilisé pour mettre l’appareil hors connexion.

Pour modifier le mode de l’appareil, suivez ces étapes :

1. Sur l’interface utilisateur web locale de votre appareil, accédez à **Configuration > Cloud**.
2. Dans la liste déroulante, sélectionnez le mode dans lequel vous voulez utiliser l’appareil. Vous pouvez choisir entre **Entièrement connecté**, **Partiellement connecté** et **Entièrement déconnecté**. Pour exécuter l’appareil en mode partiellement déconnecté, activez **Gestion sur le Portail Azure**.

 
## <a name="manage-power"></a>Gérer la puissance

Vous pouvez arrêter ou redémarrer votre appareil physique à l’aide de l’interface utilisateur web locale. Nous vous recommandons de mettre les partages hors connexion sur le serveur de données avant de redémarrer. Cette action réduit les risques d’altération des données.

1. Sur l’interface utilisateur web locale, accédez à **Maintenance > Alimentation**.
2. Sélectionnez **Arrêter** ou **Redémarrer** selon ce que vous voulez faire.

    ![Paramètres d’alimentation](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quand vous êtes invité à confirmer l’opération, sélectionnez **Oui** pour continuer.

> [!NOTE]
> Si vous arrêtez l’appareil physique, vous devez appuyer sur le bouton d’alimentation situé sur l’appareil pour le mettre sous tension.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [gérer les partages](azure-stack-edge-manage-shares.md).
