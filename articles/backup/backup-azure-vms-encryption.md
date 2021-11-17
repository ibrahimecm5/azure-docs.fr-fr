---
title: Sauvegarder et restaurer des machines virtuelles Azure chiffrées
description: Décrit comment sauvegarder et restaurer des machines virtuelles Azure chiffrées avec le service Sauvegarde Azure.
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: ebd8280b24c0f99474f3847d7549db0da9a27516
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157516"
---
# <a name="back-up-and-restore-encrypted-azure-virtual-machines"></a>Sauvegarder et Restaurer des machines virtuelles Azure chiffrées

Cet article explique comment sauvegarder et restaurer des machines virtuelles Azure Windows ou Linux avec des disques chiffrés à l’aide du service [Sauvegarde Azure](backup-overview.md). Pour plus d'informations, consultez [Chiffrement des sauvegardes de machines virtuelles Azure](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups).

## <a name="encryption-using-platform-managed-keys"></a>Chiffrement à l’aide de clés gérées par la plateforme

Par défaut, tous les disques de vos machines virtuelles sont automatiquement chiffrés au repos à l’aide de clés PMK (Platform-Managed Keys) qui utilisent [Storage Service Encryption](../storage/common/storage-service-encryption.md). Vous pouvez sauvegarder ces machines virtuelles à l’aide de Sauvegarde Azure sans aucune action spécifique pour prendre en charge le chiffrement de votre côté. Pour plus d’informations sur le chiffrement avec des clés gérées par la plateforme, [consultez cet article](../virtual-machines/disk-encryption.md#platform-managed-keys).

![Disques chiffrés](./media/backup-encryption/encrypted-disks.png)

## <a name="encryption-using-customer-managed-keys"></a>Chiffrement à l’aide de clés gérées par le client

Lorsque vous chiffrez des disques avec des clés gérées par le client (CMK), la clé utilisée pour chiffrer les disques est stockée dans Azure Key Vault et elle est gérée par vous. Le chiffrement Storage Service Encryption (SSE) avec CMK est différent du chiffrement Azure Disk Encryption (ADE). ADE utilise les outils de chiffrement du système d’exploitation. SSE chiffre les données dans le service de stockage, ce qui vous permet d’utiliser le système d’exploitation ou l’image de votre choix pour vos machines virtuelles.

Vous n’avez pas besoin d’effectuer d’actions explicites pour la sauvegarde ou la restauration de machines virtuelles qui utilisent des clés gérées par le client pour chiffrer leurs disques. Les données de sauvegarde de ces machines virtuelles stockées dans le coffre sont chiffrées avec les mêmes méthodes que le [chiffrement utilisé dans le coffre](encryption-at-rest-with-cmk.md).

Pour plus d’informations sur le chiffrement de disques managés avec des clés gérées par la plateforme, consultez [cet article](../virtual-machines/disk-encryption.md#customer-managed-keys).

## <a name="encryption-support-using-ade"></a>Prise en charge du chiffrement avec ADE

Sauvegarde Azure prend en charge la sauvegarde des machines virtuelles Azure dont le disque de système d’exploitation/données est chiffré avec Azure Disk Encryption (ADE). ADE utilise BitLocker pour le chiffrement des machines virtuelles Windows et la fonctionnalité dm-crypt pour les machines virtuelles Linux. ADE s’intègre à Azure Key Vault pour gérer les secrets et clés de chiffrement de disque. Les clés de chiffrement de clé Azure Key Vault (KEK) permettent d’ajouter une couche de sécurité, en chiffrant les secrets de chiffrement avant de les écrire dans le coffre de clés.

Sauvegarde Azure peut sauvegarder et restaurer des machines virtuelles Azure à l’aide d’ADE avec et sans l’application Azure AD, comme le récapitule le tableau suivant.

**Type de disque de machine virtuelle** | **ADE (BEK/dm-crypt)** | **ADE et clé KEK**
--- | --- | ---
**Non managé** | Oui | Oui
**Managé**  | Oui | Oui

- Découvrez-en plus sur [ADE](../security/fundamentals/azure-disk-encryption-vms-vmss.md), [Key Vault](../key-vault/general/overview.md) et les clés [KEK](../virtual-machine-scale-sets/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek).
- Consultez les [Questions fréquentes (FAQ)](../security/fundamentals/azure-disk-encryption-vms-vmss.md) sur le chiffrement des disques de machines virtuelles Azure.

### <a name="limitations"></a>Limites

- Vous pouvez sauvegarder et restaurer des machines virtuelles chiffrées par ADE dans le même abonnement.
- Sauvegarde Azure prend en charge les machines virtuelles chiffrées à l’aide de clés autonomes. Aucune clé appartenant à un certificat utilisé pour chiffrer une machine virtuelle n’est prise en charge.
- Sauvegarde Azure prend en charge la restauration inter-région de machines virtuelles Azure chiffrées vers les régions appairées Azure. Pour plus d’informations, consultez la [matrice de prise en charge](./backup-support-matrix.md#cross-region-restore).
- Les machines virtuelles chiffrées par ADE ne peuvent pas être récupérées au niveau du fichier/dossier. Vous devez récupérer la machine virtuelle entière pour restaurer des fichiers et des dossiers.
- Quand vous restaurez une machine virtuelle, vous ne pouvez pas utiliser l’option de [remplacement de la machine virtuelle existante](backup-azure-arm-restore-vms.md#restore-options) pour les machines virtuelles chiffrées par ADE. Cette option est uniquement prise en charge pour les disques managés non chiffrés.

## <a name="before-you-start"></a>Avant de commencer

Avant de commencer, procédez comme suit :

1. Veillez à disposer d’une ou plusieurs machines virtuelles [Windows](../virtual-machines/linux/disk-encryption-overview.md) ou [Linux](../virtual-machines/linux/disk-encryption-overview.md) sur lesquelles ADE est activé.
2. [Passez en revue la matrice de prise en charge](backup-support-matrix-iaas.md) de la sauvegarde des machines virtuelles Azure.
3. [Créez](backup-create-rs-vault.md) un coffre de sauvegarde Recovery Services si vous n’en avez pas.
4. Si vous activez le chiffrement pour des machines virtuelles qui sont déjà activées pour la sauvegarde, vous devez simplement fournir à Sauvegarde des autorisations pour accéder au coffre de clés afin que les sauvegardes puissent se poursuivre sans interruption. [Découvrez-en plus](#provide-permissions) sur l’affectation de ces autorisations.

Par ailleurs, vous risquez de devoir faire deux choses dans certaines circonstances :

- **Installer l’agent de machine virtuelle sur la machine virtuelle** : Sauvegarde Azure sauvegarde les machines virtuelles Azure en installant une extension à l’agent de machine virtuelle Azure en cours d’exécution sur l’ordinateur. Si votre machine virtuelle a été créée à partir d’une image de la Place de marché Azure, l’agent est installé et en cours d’exécution. Si vous créez une machine virtuelle personnalisée ou que vous migrez une machine locale, vous devrez peut-être [installer l’agent manuellement](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Configurer une stratégie de sauvegarde

1. Si vous n’avez pas encore créé de coffre de sauvegarde Recovery Services, suivez [ces instructions](backup-create-rs-vault.md).
1. Accédez au Centre de sauvegarde, puis, sous l’onglet **Vue d’ensemble**, cliquez sur **+Sauvegarde**.

    ![Volet Sauvegarde](./media/backup-azure-vms-encryption/select-backup.png)

1. Sélectionnez **Machines virtuelles Azure** comme **Type de source de données**, sélectionnez le coffre que vous avez créé, puis cliquez sur **Continuer**.

     ![Volet Scénario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

1. Sélectionnez la stratégie que vous souhaitez associer au coffre, puis choisissez **OK**.
   - Une stratégie de sauvegarde spécifie le moment auquel les sauvegardes sont effectuées ainsi que leur durée de stockage.
   - Les détails de la stratégie par défaut sont répertoriés dans le menu déroulant à l’écran.

   ![Choisir une stratégie de sauvegarde](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
    
1. Si vous ne souhaitez pas utiliser la stratégie par défaut, sélectionnez **Créer** et [Créer une stratégie personnalisée](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

1. Sous **Machines virtuelles**, sélectionnez **Ajouter**.

    ![Ajouter des machines virtuelles](./media/backup-azure-vms-encryption/add-virtual-machines.png)

1. Cliquez sur les machines virtuelles chiffrées que vous souhaitez sauvegarder à l’aide de la stratégie sélectionnée, puis sélectionnez **OK**.

      ![Sélectionner des machines virtuelles chiffrées](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

1. Si vous utilisez Azure Key Vault, dans la page du coffre, vous voyez un message indiquant que Sauvegarde Azure a besoin d’un accès en lecture seule aux clés et secrets dans le coffre de clés.

    - Si vous recevez le message ci-après, aucune action n’est requise.

        ![Accès OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Si vous recevez le message ci-après, vous devez définir des autorisations comme décrit dans la [procédure ci-dessous](#provide-permissions).

        ![Avertissement concernant l’accès](./media/backup-azure-vms-encryption/access-warning.png)

1. Sélectionnez **Activer la sauvegarde** pour déployer la stratégie de sauvegarde dans le coffre et activer la sauvegarde pour les machines virtuelles sélectionnées.

## <a name="trigger-a-backup-job"></a>Déclencher une tâche de sauvegarde

La sauvegarde initiale s’exécutera conformément à la planification, mais vous pouvez l’exécuter immédiatement comme suit :

1. Accédez au **Centre de sauvegarde** et sélectionnez l’option de menu **Instances de sauvegarde**.
1. Sélectionnez **Machines virtuelles Azure** comme **Type de source de source**, puis recherchez la machine virtuelle que vous avez configurée pour la sauvegarde.
1. Cliquez avec le bouton droit sur la ligne appropriée ou sélectionnez l’icône plus (...), puis cliquez sur **Sauvegarder maintenant**.
1. Dans **Sauvegarder maintenant**, utilisez le contrôle de calendrier pour sélectionner le dernier jour de rétention du point de récupération. Sélectionnez ensuite **OK**.
1. Surveiller les notifications du portail.
   Pour surveiller la progression du travail, accédez au **Centre de sauvegarde** > **Travaux de sauvegarde**, puis filtrez la liste des travaux **en cours**.
   Selon la taille de votre machine virtuelle, la création de la sauvegarde initiale peut prendre un certain temps.

## <a name="provide-permissions"></a>Fournir des autorisations

Sauvegarde Azure a besoin d’un accès en lecture seule pour sauvegarder les clés et secrets ainsi que les machines virtuelles associées.

- Votre coffre de clés est associé au locataire Azure AD de l’abonnement Azure. Si vous êtes **utilisateur membre**, Sauvegarde Azure acquiert l’accès au coffre de clés sans qu’aucune autre action soit nécessaire.
- Si vous êtes **utilisateur invité**, vous devez accorder des autorisations afin que Sauvegarde Azure puisse accéder au coffre de clés. Pour configurer la sauvegarde des machines virtuelles chiffrées, vous devez avoir accès aux coffres de clés.

Pour définir des autorisations :

1. Dans le portail Azure, sélectionnez **Tous les services**, puis recherchez **Coffres de clés**.
1. Sélectionnez le coffre de clés associé à la machine virtuelle chiffrée en cours de sauvegarde.

    >[!TIP]
    >Pour identifier un coffre de clés associé à une machine virtuelle, utilisez la commande PowerShell suivante. Remplacez par le nom de votre groupe de ressources et le nom de votre machine virtuelle :
    >
    >`Get-AzVm -ResourceGroupName "MyResourceGroup001" -VMName "VM001" -Status`
    >
    > Recherchez le nom du coffre de clés dans cette ligne :
    >
    >`SecretUrl            : https://<keyVaultName>.vault.azure.net`
    >

1. Sélectionnez **Stratégies d’accès** > **Ajouter une stratégie d’accès**.

    ![Ajouter une stratégie d’accès](./media/backup-azure-vms-encryption/add-access-policy.png)

1. Dans **Ajouter une stratégie d’accès** > **Configurer à partir du modèle (facultatif)** , sélectionnez **Sauvegarde Azure**.
    - Les autorisations requises sont préremplies dans les listes déroulantes **Autorisations de clé** et **Autorisations du secret**.
    - Si votre machine virtuelle est chiffrée à l’aide de **BEK uniquement**, supprimez la sélection pour **Autorisations de clé** dans la mesure où vous avez uniquement besoin d’autorisations pour les secrets.

    ![Sélection de Sauvegarde Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Sélectionnez **Ajouter**. **Service de gestion des sauvegardes** est ajouté à **Stratégies d’accès**.

    ![Stratégies d’accès](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. Sélectionnez **Enregistrer** pour fournir les autorisations à Sauvegarde Azure.

## <a name="next-steps"></a>Étapes suivantes

[Restaurer des machines virtuelles Azure chiffrées](restore-azure-encrypted-virtual-machines.md)

Si vous rencontrez des problèmes, consultez les articles suivants :

- [Erreurs courantes](backup-azure-vms-troubleshoot.md) liées à la sauvegarde et à la restauration de machines virtuelles Azure chiffrées.
- Problèmes liés à l’[extension de sauvegarde ou à l’agent de machine virtuelle Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md).
