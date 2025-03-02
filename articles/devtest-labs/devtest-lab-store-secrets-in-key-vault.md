---
title: Stocker des secrets dans un coffre de clés
description: Découvrez comment stocker des secrets dans un coffre Azure Key Vault et les utiliser pour créer une machine virtuelle, une formule ou un environnement.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 2c1849b6bf2ab5876fcbe0960df9bb8a7004bb78
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398095"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Stocker des secrets dans un coffre de clés dans Azure DevTest Labs
Vous devrez peut-être entrer un secret complexe lorsque vous utilisez Azure DevTest Labs. Les secrets incluent des mots de passe pour vos machines virtuelles Windows, des clés SSH publiques pour vos machines virtuelles Linux ou un jeton d’accès personnel pour cloner votre référentiel Git via un artefact. Les secrets sont souvent longs et ont des caractères aléatoires. La saisie d’un secret peut être difficile et peu pratique, surtout si vous l’utilisez plusieurs fois.

Pour résoudre ce problème tout en conservant vos secrets dans un emplacement sûr, DevTest Labs prend en charge le stockage des secrets dans un [coffre de clés Azure](../key-vault/general/overview.md). La première fois qu’un utilisateur enregistre un secret, DevTest Labs crée automatiquement un coffre de clés dans le même groupe de ressources que le laboratoire et stocke le secret. DevTest Labs crée un coffre de clés distinct pour chaque utilisateur. 

Les utilisateurs du laboratoire doivent créer une machine virtuelle lab avant de pouvoir créer un secret dans le coffre de clés. Cette exigence est due au fait que DevTest Labs doit associer des utilisateurs de lab à des documents utilisateur valides. DevTest Labs permet ensuite aux utilisateurs de créer et de stocker des secrets dans leurs coffres de clés.


## <a name="save-a-secret-in-azure-key-vault"></a>Enregistrer un secret dans Azure Key Vault
Pour enregistrer votre secret dans Azure Key Vault, procédez comme suit :

1. Sélectionnez **Mes secrets** dans le menu de gauche.
2. Entrez un **nom** pour le secret. Vous voyez ce nom dans la liste déroulante lorsque vous créez une machine virtuelle, une formule ou un environnement. 
3. Entrez le secret en tant que **valeur**.

    ![Capture d’écran montrant le stockage d’une clé secrète.](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Utiliser un secret à partir d’Azure Key Vault
Lorsque vous entrez un secret pour créer une machine virtuelle, une formule ou un environnement, vous pouvez entrer le secret manuellement ou sélectionner un secret enregistré dans le coffre de clés. Pour utiliser un secret stocké dans votre coffre de clés, effectuez les actions suivantes :

1. Sélectionnez **Utiliser un secret enregistré**. 
2. Sélectionnez votre secret dans la liste déroulante pour **Choisir un secret**. 

    ![Capture d’écran illustrant l’utilisation d’un secret lors de la création d’une machine virtuelle.](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Utiliser un secret dans un modèle Azure Resource Manager
Vous pouvez spécifier le nom de votre secret dans un modèle Azure Resource Manager qui est utilisé pour créer une machine virtuelle, comme illustré dans l’exemple suivant :

![Capture d’écran illustrant l’utilisation d’un secret dans une formule ou un environnement.](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Étapes suivantes

- [Créer une machine virtuelle à l’aide du secret](devtest-lab-add-vm.md) 
- [Créer une formule à l’aide du secret](devtest-lab-manage-formulas.md)
- [Créer un environnement à l’aide du secret](devtest-lab-create-environment-from-arm.md)
