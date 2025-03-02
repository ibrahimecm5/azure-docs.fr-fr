---
title: Fichier Include
description: Fichier include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/06/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7f4a2e5a7186032995ee277dfb9f2c226853163b
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112573978"
---
## <a name="create-an-image-gallery"></a>Créer une galerie d’images

Une galerie d’images est la ressource principale utilisée pour activer le partage d’image. Les caractères autorisés pour le nom de galerie sont les lettres majuscules ou minuscules, les chiffres et les points. Le nom de galerie ne peut pas contenir de tirets.  Les noms de galerie doivent être uniques dans votre abonnement. 

L’exemple suivant crée une galerie nommée *myGallery* dans le groupe de ressources *myGalleryRG*.

1. Connectez-vous au portail Azure sur https://portal.azure.com.
1. Utilisez le type **Galerie d’images partagées** dans la zone de recherche, puis sélectionnez **Galerie d’images partagées** dans les résultats.
1. Sur la page **Galerie d’images partagées**, cliquez sur **Ajouter**.
1. Dans la page **Créer une galerie Shared Image Gallery**, sélectionnez l’abonnement approprié.
1. Dans **Groupe de ressources**, sélectionnez **Créer** et saisissez *myResourceGroup* comme nom.
1. Dans **Nom**, saisissez *myGallery* comme nom de la galerie.
1. Laissez la valeur par défaut pour **Région**.
1. Vous pouvez saisir une brève description de la galerie, par exemple *Ma galerie d’images pour le test.* Cliquez ensuite sur **Vérifier + créer**.
1. Une fois la validation réussie, sélectionnez **Créer**.
1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.


## <a name="create-an-image-definition"></a>Créer une définition d’image 

Les définitions d’image créent un regroupement logique des images. Elles sont utilisées pour gérer les informations sur les versions d’image créées au sein de celles-ci. 

Les noms de définition d’image peuvent contenir des lettres majuscules ou minuscules, des chiffres, des tirets et des points. Pour plus d’informations sur les valeurs que vous pouvez spécifier pour une définition d’image, consultez [Définitions d’image](../articles/virtual-machines/shared-image-galleries.md#image-definitions).

Créez la définition de l’image de galerie à l’intérieur de votre galerie. 

1. Sur la page de votre nouvelle galerie d’images, sélectionnez **Ajouter une nouvelle définition d’image** à partir du haut de la page. 
1. Sous **Ajouter une nouvelle définition d’image à la galerie Shared Image Gallery**, pour **Région**, sélectionnez *USA Est*.
1. Pour **Nom de la définition d’image**, tapez un nom comme *myImageDefinition*.
1. Pour **Système d’exploitation**, sélectionnez l’option appropriée en fonction de votre machine virtuelle source.  
1. Pour **Génération de machine virtuelle**, sélectionnez l'option en fonction de votre machine virtuelle source. Dans la plupart des cas, ce sera *Gen 1*. Pour plus d'informations, voir [Prise en charge des machines virtuelles de 2e génération](../articles/virtual-machines/generation-2.md).
1. Pour **État du système d’exploitation**, sélectionnez l’option appropriée en fonction de votre machine virtuelle source. Pour plus d’informations, voir [Machines virtuelles généralisées et spécialisées](../articles/virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).
1. Pour **Éditeur**, saisissez *myPublisher*. 
1. Pour **Offre**, saisissez *myOffer*.
1. Pour **SKU**, saisissez *mySKU*.
1. Quand vous avez terminé, sélectionnez **Vérifier + créer**.
1. Une fois la définition de l’image validée, sélectionnez **Créer**.
1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.


## <a name="create-an-image-version"></a>Créer une version d’image

 Lors du choix des régions cibles pour la réplication, n’oubliez pas que vous devez également inclure la région *source* en tant que cible pour la réplication.

Les étapes de création d'une version d'image sont légèrement différentes, selon que la source est une image généralisée ou une capture instantanée d'une machine virtuelle spécialisée. 


1. Sur la page de votre définition d’image, sélectionnez **Ajouter une version** à partir du haut de la page.
1. Dans **Région**, sélectionnez la région où créer l’image.
1. Pour **Numéro de version**, tapez un nombre comme *1.0.0*. Le nom de version d’image doit respecter le format *version majeure*.*version mineure*.*correctif* en utilisant des nombres entiers. 
1. Dans **Image source**, sélectionnez votre image managée source à partir de la liste déroulante. Consultez le tableau ci-dessous pour obtenir des détails spécifiques pour chaque type de source.

    | Source | Autres champs |
    |---|---|
    | Disques ou instantanés | - Pour **Disque de système d’exploitation**, sélectionnez le disque ou l’instantané dans la liste déroulante. <br> - Pour ajouter un disque de données, tapez le numéro de LUN, puis sélectionnez le disque de données dans la liste déroulante. |
    | Version d’image | - Sélectionnez la **galerie source** dans la liste déroulante. <br> - Sélectionnez la définition d’image appropriée dans la liste déroulante. <br>- Sélectionnez la version de l’image existante que vous souhaitez utiliser dans la liste déroulante. |
    | Image managée | Sélectionnez l’**image source** dans la liste déroulante. <br>L’image managée doit se trouver dans la même région que celle que vous avez choisie dans **Détails de l’instance**.
    | VHD dans un compte de stockage | Sélectionnez **Parcourir** pour choisir le compte de stockage du VHD. |

1. Dans **Exclure de la plus récente**, conservez la valeur par défaut *Non*.
1. Pour **Date de fin de vie**, sélectionnez une date dans le calendrier dans quelques mois.
1. Dans l’onglet **Réplication**, sélectionnez le type de stockage dans la liste déroulante.
1. Définissez le **nombre de réplicas par défaut**. Vous pouvez le remplacer pour chaque région que vous ajoutez. 
1. Vous devez effectuer une réplication vers la région source, de sorte que le premier réplica de la liste sera dans la région où vous avez créé l’image. Vous pouvez ajouter d’autres réplicas en sélectionnant la région dans la liste déroulante et en ajustant le nombre de réplicas si nécessaire.
1. Quand vous avez terminé, sélectionnez **Vérifier + créer**. Azure validera la configuration.
1. Une fois la définition d’image validée, sélectionnez **Créer**.
1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

La réplication de l’image à l’ensemble des régions cibles peut prendre un certain temps.

Vous pouvez également capturer une machine virtuelle existante sous forme d’image à partir du portail. Pour plus d’informations, consultez [Créer une image d’une machine virtuelle dans le portail](../articles/virtual-machines/capture-image-portal.md).

## <a name="share-the-gallery"></a>Partager la galerie

Nous vous recommandons de partager l’accès au niveau de la galerie d’image. Les procédures suivantes vous guident dans les étapes de partage de la galerie que vous venez de créer.

1. Sur la page de votre nouvelle galerie d’images, sélectionnez **contrôle d’accès (IAM)** dans le menu de gauche. 
1. Sous **Ajouter une attribution de rôle**, sélectionnez **Ajouter**. Le volet **Ajouter une attribution de rôle** s’ouvre. 
1. Sous **Rôle**, sélectionnez **Lecteur**.
1. Sous **Attribuer l’accès à**, laissez la valeur par défaut **Utilisateur, groupe ou principal du service Azure AD**.
1. Sous **Sélectionner**, saisissez l’adresse de messagerie de la personne que vous souhaitez inviter.
1. Si l’utilisateur se trouve en dehors de votre organisation, vous verrez le message **Cet utilisateur recevra un e-mail qui lui permettra de collaborer avec Microsoft.** Sélectionnez l’utilisateur avec son adresse e-mail, puis cliquez sur **Enregistrer**.

Si l’utilisateur se trouve en dehors de votre organisation, il recevra un message d’invitation à rejoindre l’organisation. L’utilisateur doit accepter l’invitation, puis il pourra voir la galerie et toutes les définitions et versions d’image versions dans sa liste de ressources.
