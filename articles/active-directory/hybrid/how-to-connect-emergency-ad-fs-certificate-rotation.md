---
title: Rotation d’urgence des certificats AD FS | Microsoft Docs
description: Cet article explique comment révoquer et mettre à jour des certificats AD FS immédiatement.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 3b03dc507a76254f8568989af27f76aa75ad4e20
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222435"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>Rotation d’urgence des certificats AD FS
Si vous devez faire pivoter immédiatement les certificats AD FS, vous pouvez suivre les étapes décrites ci-dessous dans cette section.

> [!IMPORTANT]
> En effectuant les étapes ci-dessous dans l’environnement AD FS, vous révoquez immédiatement les anciens certificats.  Cette opération étant effectuée immédiatement, le temps normal généralement accordé à vos partenaires de fédération pour consommer votre nouveau certificat est contourné. Une panne de service peut entraîner une interruption de la mise à jour des approbations pour utiliser les nouveaux certificats.  Cela devrait se résoudre une fois que tous les partenaires de fédération disposent des nouveaux certificats.

> [!NOTE]
> Microsoft recommande vivement d’utiliser un module de sécurité matériel (HSM) pour protéger et sécuriser les certificats.
> Pour plus d’informations, consultez [Module de sécurité matériel](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) dans les meilleures pratiques pour la sécurisation des AD FS.

## <a name="determine-your-token-signing-certificate-thumbprint"></a>Déterminer l’empreinte de votre certificat de signature de jetons
Pour révoquer l’ancien certificat de signature de jetons qu’AD FS utilise actuellement, vous devez déterminer l’empreinte du certificat de signature de jetons.  Pour ce faire, effectuez les étapes suivantes ci-dessous :

 1.    Se connecter au service en ligne Microsoft `PS C:\>Connect-MsolService`
 2.    Documentez les dates d’expiration et d’empreinte du certificat de signature de jetons Cloud et local.
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  Copiez l’empreinte.  Elle sera utilisée ultérieurement pour supprimer les certificats existants.

Vous pouvez également obtenir l’empreinte à l’aide de la Gestion AD FS, en accédant à Service/Certificats, en cliquant avec le bouton droit sur le certificat, puis en sélectionnant Afficher le certificat, puis en sélectionnant Détails. 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>Déterminer si AD FS renouvelle automatiquement les certificats
Par défaut, AD FS est configuré pour générer automatiquement des certificats de signature et de déchiffrement de jetons, lors de la configuration initiale et lorsque les certificats approchent de leur date d’expiration.

Vous pouvez exécuter la commande Windows PowerShell suivante : `PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`.

La propriété AutoCertificateRollover indique si AD FS est configuré pour renouveler automatiquement les certificats de signature de jetons et de déchiffrement de jeton.  Si AutoCertificateRollover a la valeur TRUE, suivez les instructions indiquées ci-dessous dans [Génération d’un nouveau certificat auto-signé si AutoCertificateRollover a la valeur TRUE](#generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true).  Si AutoCertificateRollover a la valeur FALSE, suivez les instructions indiquées ci-dessous dans [Génération d’un nouveau certificat manuellement si AutoCertificateRollover a la valeur FALSE](#generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false).


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>Génération d’un nouveau certificat auto-signé si AutoCertificateRollover a la valeur TRUE
Dans cette section, vous allez créer **deux** certificats de signature de jetons.  Le premier utilisera l’indicateur **-urgent**, qui remplacera immédiatement le certificat principal actuel.  Le second sera utilisé pour le certificat secondaire.  

>[!IMPORTANT]
>La raison pour laquelle nous créons deux certificats est qu’Azure conserve les informations relatives au certificat précédent.  En créant un deuxième certificat, nous forçons Azure à publier des informations sur l’ancien certificat et à les remplacer par des informations sur le deuxième certificat.
>
>Si vous ne créez pas le deuxième certificat et que vous mettez à jour Azure avec, il est possible que l’ancien certificat de signature de jetons authentifie les utilisateurs.

Vous pouvez utiliser les étapes suivantes pour générer les nouveaux certificats de signature de jetons.

 1. Vérifiez que vous êtes connecté au serveur AD FS principal.
 2. Ouvrez Windows PowerShell en tant qu’administrateur. 
 3. Vérifiez que votre AutoCertificateRollover a la valeur true.
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. Pour générer un nouveau certificat de signature de jetons : `Update-ADFSCertificate –CertificateType token-signing -Urgent`.
 5. Vérifiez la mise à jour en exécutant la commande suivante : `Get-ADFSCertificate –CertificateType token-signing`
 6. Générez maintenant le deuxième certificat de signature de jetons : `Update-ADFSCertificate –CertificateType token-signing`.
 7. Vous pouvez vérifier la mise à jour en réexécutant la commande suivante : `Get-ADFSCertificate –CertificateType token-signing`


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>Génération manuelle de nouveaux certificats si AutoCertificateRollover a la valeur FALSE
Si vous n’utilisez pas les certificats de signature de jetons et de déchiffrement de jetons auto-signés générés automatiquement par défaut, vous devez renouveler et configurer ces certificats manuellement.  Cela implique de créer deux nouveaux certificats de signature de jetons et de les importer.  Vous en promouvez ensuite un en principal, vous révoquez l’ancien certificat et configurez le deuxième certificat comme certificat secondaire.

Tout d’abord, vous devez obtenir deux nouveaux certificats auprès de votre autorité de certification et les importer dans le magasin de certificats personnels de l’ordinateur local sur chaque serveur de fédération. Pour obtenir des instructions, consultez l'article [Importer un certificat](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754489(v=ws.11)).

>[!IMPORTANT]
>La raison pour laquelle nous créons deux certificats est qu’Azure conserve les informations relatives au certificat précédent.  En créant un deuxième certificat, nous forçons Azure à publier des informations sur l’ancien certificat et à les remplacer par des informations sur le deuxième certificat.
>
>Si vous ne créez pas le deuxième certificat et que vous mettez à jour Azure avec, il est possible que l’ancien certificat de signature de jetons authentifie les utilisateurs.

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>Pour configurer un nouveau certificat en tant que certificat secondaire
Vous devez ensuite configurer un certificat comme certificat de signature ou de déchiffrement de jetons secondaire AD FS puis le promouvoir en tant que certificat principal

1. Après avoir importé le certificat, Ouvrez la console de **gestion AD FS**.
2. Développez **Service**, puis sélectionnez **Certificats**.
3. Dans le volet Actions, cliquez sur **Ajouter un certificat de signature de jetons**.
4. Sélectionnez le nouveau certificat dans la liste de certificats affichés, puis cliquez sur OK.

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>Pour promouvoir le nouveau certificat secondaire en certificat principal
Maintenant que le nouveau certificat a été importé et configuré dans AD FS, vous devez le définir en tant que certificat principal.
1. Ouvrez la console de **gestion AD FS**.
2. Développez **Service**, puis sélectionnez **Certificats**.
3. Cliquez sur le certificat de signature de jetons secondaire.
4. Dans le volet **Actions**, cliquez sur **Définir comme principal**. Cliquez sur Oui à l'invite de confirmation.
5. Une fois que vous avez promu le nouveau certificat en tant que certificat principal, vous devez supprimer l’ancien certificat, car il peut toujours être utilisé. Consultez la section [Supprimer vos anciens certificats](#remove-your-old-certificates) ci-dessous.  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>Pour configurer le deuxième certificat en tant que certificat secondaire
Maintenant que vous avez ajouté le premier certificat, que vous l’avez passé en tant que certificat principal et que vous avez supprimé l’ancien, importez le deuxième certificat.  Vous devez ensuite configurer le certificat comme le certificat de signature de jetons secondaire AD FS

1. Après avoir importé le certificat, Ouvrez la console de **gestion AD FS**.
2. Développez **Service**, puis sélectionnez **Certificats**.
3. Dans le volet Actions, cliquez sur **Ajouter un certificat de signature de jetons**.
4. Sélectionnez le nouveau certificat dans la liste de certificats affichés, puis cliquez sur OK.

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>Mettre à jour Azure AD avec le nouveau certificat de signature de jetons
Ouvrez le Module Microsoft Azure Active Directory pour Windows PowerShell. Vous pouvez également ouvrir Windows PowerShell, puis exécuter la commande `Import-Module msonline`

Connectez-vous à Azure AD en exécutant la commande suivante : `Connect-MsolService`, puis entrez vos informations d’identification d’administrateur général.

>[!Note]
> Si vous exécutez ces commandes sur un ordinateur qui n'est pas le serveur de fédération principal, entrez d'abord la commande suivante : `Set-MsolADFSContext –Computer <servername>`. Remplacez \<servername\> par le nom du serveur AD FS. Ensuite, entrez les informations d'identification d'administrateur pour le serveur AD FS lorsque vous y êtes invité.

Vous pouvez également vérifier si une mise à jour est requise en vérifiant les informations de certificat en cours dans Azure AD. Pour ce faire, exécutez la commande suivante : `Get-MsolFederationProperty`. Entrez le nom du domaine fédéré lorsque vous y êtes invité.

Pour mettre à jour les informations de certificat dans Azure AD, exécutez la commande suivante : `Update-MsolFederatedDomain`, puis entrez le nom de domaine lorsque vous y êtes invité.

>[!Note]
> Si vous voyez une erreur lors de l’exécution de cette commande, exécutez la commande suivante : `Update-MsolFederatedDomain –SupportMultipleDomain`, puis entrez le nom de domaine lorsque vous y êtes invité.

## <a name="replace-ssl-certificates"></a>Remplacer des certificats SSL
Dans le cas où vous devez remplacer votre certificat de signature de jetons en raison d’une compromission, vous devez également révoquer et remplacer les certificats SSL pour AD FS et vos serveurs WAP.  

La révocation de vos certificats SSL doit être effectuée au niveau de l’autorité de certification qui a émis le certificat.  Ces certificats sont souvent émis par des fournisseurs tiers tels que GoDaddy.  Pour obtenir un exemple, consultez (Révoquer un certificat | Certificats SSL - GoDaddy Help US).  Pour plus d’informations, consultez [Fonctionnement de la révocation de certificats](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10)).

Une fois que l’ancien certificat SSL a été révoqué et qu’un nouveau certificat a été émis, vous pouvez remplacer les certificats SSL. Pour plus d’informations, consultez [Remplacement du certificat SSL pour AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs).

## <a name="remove-your-old-certificates"></a>Supprimer vos anciens certificats
Une fois que vous avez remplacé vos anciens certificats, vous devez supprimer l’ancien certificat, car il peut toujours être utilisé. Pour ce faire, procédez comme suit :

1. Vérifiez que vous êtes connecté au serveur AD FS principal.
2. Ouvrez Windows PowerShell en tant qu’administrateur. 
4. Pour supprimer l’ancien certificat de signature de jetons : `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>`.

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>Mise à jour des partenaires de fédération qui peuvent consommer les métadonnées de fédération
Si vous avez renouvelé et configuré un nouveau certificat de signature de jetons ou de déchiffrement de jetons, vous devez vous assurer que tous les partenaires de votre fédération (organisation de ressources ou partenaires d’organisation de compte représentés dans votre AD FS par les approbations de partie de confiance et les approbations de fournisseur de revendications) ont récupéré les nouveaux certificats.

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>Mise à jour des partenaires de fédération qui NE peuvent PAS consommer les métadonnées de fédération
Si vos partenaires de fédération ne peuvent pas consommer vos métadonnées de fédération, vous devez leur envoyer manuellement la clé publique de votre nouveau certificat de signature de jetons/déchiffrement de jetons. Envoyez votre nouvelle clé publique de certificat (fichier .cer ou .p7b si vous souhaitez inclure la chaîne entière) à l’ensemble de vos partenaires d’organisation de ressources ou d’organisation de compte (représentés dans votre AD FS par des approbations de partie de confiance et des approbations de fournisseur de revendications). Demandez aux partenaires d’implémenter les modifications de leur côté pour approuver les nouveaux certificats.



## <a name="revoke-refresh-tokens-via-powershell"></a>Révoquer les jetons d’actualisation via PowerShell
À présent, nous voulons révoquer les jetons d’actualisation pour les utilisateurs qui peuvent les avoir et les forcer à se reconnecter et à obtenir de nouveaux jetons.  Cela permet de déconnecter les utilisateurs de leur téléphone, les sessions webmail actuelles, ainsi que d’autres éléments qui utilisent des jetons et des jetons d’actualisation.  Vous trouverez des informations [ici](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?preserve-view=true&view=azureadps-2.0) et vous pouvez également consulter comment [Révoquer l’accès utilisateur dans Azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md).

## <a name="next-steps"></a>Étapes suivantes

- [Gestion des certificats SSL dans AD FS et WAP dans Windows Server 2016](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [Obtenir et configurer des certificats de signature de jetons et de déchiffrement de jetons pour AD FS](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Renouveler des certificats de fédération pour Microsoft 365 et Azure Active Directory](how-to-connect-fed-o365-certs.md)
