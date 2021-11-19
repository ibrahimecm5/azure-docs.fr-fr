---
title: Rôles intégrés à Azure - Azure RBAC
description: Cet article décrit les rôles intégrés à Azure pour le contrôle d’accès en fonction du rôle Azure (Azure RBAC). Il dresse la liste des Actions, NotActions, DataActions et NotDataActions.
services: active-directory
ms.service: role-based-access-control
ms.topic: reference
ms.workload: identity
author: rolyon
ms.author: rolyon
ms.date: 11/12/2021
ms.custom: generated
ms.openlocfilehash: 81b12ad1ae52c290e5c3ef573bf091fb64595701
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494407"
---
# <a name="azure-built-in-roles"></a>Rôles intégrés Azure

[Le contrôle d’accès en fonction du rôle (RBAC) Azure](overview.md) a plusieurs rôles intégrés Azure que vous pouvez affecter aux utilisateurs, groupes, principaux de service et identités managées. Les attributions de rôles vous permettent de contrôler l’accès aux ressources Azure. Si les rôles intégrés ne répondent pas aux besoins spécifiques de votre organisation, vous pouvez créer vos propres [rôles personnalisés Azure](custom-roles.md). Pour plus d'informations sur l'attribution des rôles, consultez [Procédure d'attribution d'un rôle Azure](role-assignments-steps.md).

Cet article répertorie les rôles intégrés à Azure. Si vous recherchez des rôles d'administrateur pour Azure Active Directory (Azure AD), consultez [Rôles intégrés à Azure](../active-directory/roles/permissions-reference.md).

Le tableau ci-après fournit une brève description de chaque rôle intégré. Cliquez sur le nom d’un rôle pour voir la liste de `Actions`, `NotActions`, `DataActions` et `NotDataActions` concernant ce rôle. Pour obtenir des informations sur la signification de ces actions et la manière dont elles s’appliquent en termes de contrôle et de données, consultez [Comprendre les définitions de rôle Azure](role-definitions.md).

## <a name="all"></a>Tous

> [!div class="mx-tableFixed"]
> | Rôle intégré | Description | id |
> | --- | --- | --- |
> | **Généralités** |  |  |
> | [Contributeur](#contributor) | Accorde un accès total pour gérer toutes les ressources, mais ne vous permet pas d’affecter des rôles dans Azure RBAC, de gérer des affectations dans Azure Blueprints ou de partager des galeries d’images. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Propriétaire](#owner) | Octroie un accès total pour gérer toutes les ressources, notamment la possibilité d’attribuer des rôles dans Azure RBAC. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Lecteur](#reader) | Affiche toutes les ressources, mais ne vous autorise pas à apporter des modifications. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Administrateur de l'accès utilisateur](#user-access-administrator) | Vous permet de gérer l'accès utilisateur aux ressources Azure. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Calcul** |  |  |
> | [Contributeur de machine virtuelle classique](#classic-virtual-machine-contributor) | Permet de gérer des machines virtuelles classiques, mais pas d’y accéder, ni au réseau virtuel ou au compte de stockage auquel elles sont connectées. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Connexion de l’administrateur aux machines virtuelles](#virtual-machine-administrator-login) | Afficher les machines virtuelles dans le portail et se connecter en tant qu’administrateur | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Contributeur de machine virtuelle](#virtual-machine-contributor) | Créer et gérer des machines virtuelles, gérer des disques et des instantanés de disques, installer et exécuter des logiciels, réinitialiser le mot de passe de l’utilisateur racine de la machine virtuelle à l’aide d’extensions de machine virtuelle et gérer les comptes d’utilisateurs locaux à l’aide d’extensions de machine virtuelle. Ce rôle ne vous accorde pas l’accès de gestion au réseau virtuel ou au compte de stockage auquel les machines virtuelles sont connectées. Ce rôle ne vous permet pas d’attribuer des rôles dans Azure RBAC. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Connexion de l’utilisateur aux machines virtuelles](#virtual-machine-user-login) | Affichez les machines virtuelles dans le portail et connectez-vous en tant qu’utilisateur normal. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Mise en réseau** |  |  |
> | [Contributeur de point de terminaison CDN](#cdn-endpoint-contributor) | Peut gérer les points de terminaison CDN, mais ne peut pas accorder l’accès à d’autres utilisateurs. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [Lecteur de point de terminaison CDN](#cdn-endpoint-reader) | Peut afficher des points de terminaison CDN, mais ne peut pas effectuer de modifications. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Contributeur de profil CDN](#cdn-profile-contributor) | Peut gérer des profils CDN et leurs points de terminaison, mais ne peut pas accorder l’accès à d’autres utilisateurs. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [Lecteur de profil CDN](#cdn-profile-reader) | Peut afficher des profils CDN et leurs points de terminaison, mais ne peut pas y apporter des modifications. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Contributeur de réseau classique](#classic-network-contributor) | Permet de gérer des réseaux classiques, mais pas d’y accéder. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Contributeur de Zone DNS](#dns-zone-contributor) | Permet de gérer des zones DNS et des jeux d’enregistrements dans Azure DNS, mais pas de contrôler qui y a accès. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Contributeur de réseau](#network-contributor) | Permet de gérer des réseaux, mais pas d’y accéder. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Collaborateur de zone DNS privée](#private-dns-zone-contributor) | Permet de gérer les ressources de zone DNS privée, mais pas les réseaux virtuels auxquels elles sont liées. | b12aa53e-6015-4669-85d0-8515ebb3ae7f |
> | [Contributeur Traffic Manager](#traffic-manager-contributor) | Permet de gérer des profils Traffic Manager, mais pas de contrôler qui y a accès. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Stockage** |  |  |
> | [Contributeur Avere](#avere-contributor) | Peut créer et gérer un cluster Avere vFXT. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Opérateur Avere](#avere-operator) | Utilisé par le cluster Avere vFXT pour gérer le cluster | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Contributeur de sauvegarde](#backup-contributor) | Permet de gérer le service de sauvegarde, mais pas de créer des coffres, ni d’accorder l’accès à d’autres personnes | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Opérateur de sauvegarde](#backup-operator) | Permet de gérer des services de sauvegarde, à l’exception de la suppression de la sauvegarde, de la création de coffres et de l’octroi d’autorisations d’accès à d’autres personnes | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Lecteur de sauvegarde](#backup-reader) | Peut afficher des services de sauvegarde, mais pas apporter des modifications | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Contributeur de compte de stockage classique](#classic-storage-account-contributor) | Permet de gérer des comptes de stockage classiques, mais pas d’y accéder. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Rôle de service d’opérateur de clé de compte de stockage classique](#classic-storage-account-key-operator-service-role) | Les opérateurs de clés de comptes de stockage classiques sont autorisés à lister et à régénérer des clés sur des comptes de stockage classiques | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Contributeur Data Box](#data-box-contributor) | Permet de gérer toutes les opérations sous le service Data Box à l’exception de l’octroi d’accès à d’autres personnes. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Lecteur Data Box](#data-box-reader) | Permet de gérer le service Data Box, mais ne permet pas de créer une commande, de modifier les détails d’une commande ou d’octroyer l’accès à d’autres personnes. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Développeur Data Lake Analytics](#data-lake-analytics-developer) | Permet d’envoyer, de surveiller et de gérer vos propres travaux, mais pas de créer ni de supprimer des comptes Data Lake Analytics. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Lecteur et accès aux données](#reader-and-data-access) | Permet d’afficher tous les éléments, mais pas de supprimer ou de créer un compte de stockage ou une ressource contenue. En outre, autorise l’accès en lecture/écriture à toutes les données contenues dans un compte de stockage via l’accès aux clés de compte de stockage. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Contributeur de compte de stockage](#storage-account-contributor) | Permet la gestion des comptes de stockage. Fournit l’accès à la clé de compte, qui peut être utilisée pour accéder aux données par le biais de l’autorisation de clé partagée. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Rôle de service d’opérateur de clé de compte de stockage](#storage-account-key-operator-service-role) | Permet de répertorier et de régénérer les clés d’accès au compte de stockage. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Contributeur aux données Blob du stockage](#storage-blob-data-contributor) | Lire, écrire et supprimer des conteneurs et objets blob du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Propriétaire des données Blob du stockage](#storage-blob-data-owner) | Fournit un accès total aux conteneurs d’objets blob et aux données du Stockage Azure, notamment l’attribution du contrôle d’accès POSIX. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Lecteur des données blob du stockage](#storage-blob-data-reader) | Lire et répertorier des conteneurs et objets blob du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Délégation du Stockage Blob](#storage-blob-delegator) | Obtenez une clé de délégation d’utilisateur qui peut être utilisée pour créer une signature d’accès partagé pour un conteneur ou un objet blob signé avec les informations d’identification Azure AD. Pour en savoir plus, consultez [Créer une SAP de délégation d’utilisateur](/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Contributeur de partage SMB de données de fichier de stockage](#storage-file-data-smb-share-contributor) | Permet l'accès en lecture, en écriture et en suppression aux fichiers/répertoires des partages de fichiers Azure. Ce rôle n'a pas d'équivalent intégré sur les serveurs de fichiers Windows. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Contributeur élevé de partage SMB de données de fichier de stockage](#storage-file-data-smb-share-elevated-contributor) | Permet la lecture, l'écriture, la suppression et la modification des listes de contrôle d'accès sur les fichiers/répertoires des partages de fichiers Azure. Ce rôle équivaut à une liste de contrôle d'accès de partage de fichiers en modification sur les serveurs de fichiers Windows. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Lecteur de partage SMB de données de fichier de stockage](#storage-file-data-smb-share-reader) | Permet l'accès en lecture aux fichiers/répertoires des partages de fichiers Azure. Ce rôle équivaut à une liste de contrôle d'accès de partage de fichiers en lecture sur les serveurs de fichiers Windows. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Contributeur aux données en file d’attente du stockage](#storage-queue-data-contributor) | Lire, écrire et supprimer des files d'attente et messages en file d'attente du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Processeur de messages de données en file d’attente du stockage](#storage-queue-data-message-processor) | Récupérer et supprimer un message, ou en afficher un aperçu à partir d’une file d’attente Stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Expéditeur de messages de données en file d’attente du stockage](#storage-queue-data-message-sender) | Ajoutez des messages à une file d’attente de stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Lecteur des données en file d’attente du stockage](#storage-queue-data-reader) | Lire et répertorier des files d’attente et messages en file d’attente du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | [Contributeur aux données de table du stockage](#storage-table-data-contributor) | Permet l’accès en lecture, en écriture et en suppression aux tables et entités du stockage Azure | 0a9a7e1f-b9d0-4cc4-a60d-0319b160aaa3 |
> | [Lecteur de données de table du stockage](#storage-table-data-reader) | Permet l’accès en lecture aux tables et entités du stockage Azure | 76199698-9eea-4c19-bc75-cec21354c6b6 |
> | **Web** |  |  |
> | [Contributeur aux données Azure Maps](#azure-maps-data-contributor) | Accorde l’accès en lecture, en écriture et en suppression aux données liées aux cartes depuis un compte Azure Maps. | 8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204 |
> | [Lecteur de données Azure Maps](#azure-maps-data-reader) | Octroie un accès pour lire les données liées au mappage à partir d’un compte Azure Maps. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Contributeur au serveur Azure Spring Cloud Config](#azure-spring-cloud-config-server-contributor) | Autorisez l'accès en lecture, écriture et suppression au serveur Azure Spring Cloud Config | a06f5c24-21a7-4e1a-aa2b-f19eb6684f5b |
> | [Lecteur de serveur Azure Spring Cloud Config](#azure-spring-cloud-config-server-reader) | Autoriser l'accès en lecture au serveur Azure Spring Cloud Config | d04c6db6-4947-4782-9e91-30a88feb7be7 |
> | [Lecteur de données Azure Spring Cloud](#azure-spring-cloud-data-reader) | Autoriser l'accès en lecture aux données Azure Spring Cloud | b5537268-8956-4941-a8f0-646150406f0c |
> | [Contributeur au registre du service Azure Spring Cloud](#azure-spring-cloud-service-registry-contributor) | Autoriser l'accès en lecture, écriture et suppression au registre du service Azure Spring Cloud | f5880b48-c26d-48be-b172-7927bfa1c8f1 |
> | [Lecteur de registre du service Azure Spring Cloud](#azure-spring-cloud-service-registry-reader) | Autoriser l'accès en lecture au registre du service Azure Spring Cloud | cff1b556-2399-4e7e-856d-a8f754be7b65 |
> | [Administrateur de compte Media Services](#media-services-account-administrator) | Créer, lire, modifier et supprimer des comptes Media Services ; accès en lecture seule à d’autres ressources Media Services. | 054126f8-9a2b-4f1c-a9ad-eca461f08466 |
> | [Administrateur d’événement en direct Media Services](#media-services-live-events-administrator) | Créer, lire, modifier et supprimer des événements en direct, des ressources, des filtres de ressources et des localisateurs de streaming ; accès en lecture seule à d’autres ressources Media Services. | 532bc159-b25e-42c0-969e-a1d439f60d77 |
> | [Opérateur de média Media Services](#media-services-media-operator) | Créer, lire, modifier et supprimer des ressources, des filtres de ressources, des localisateurs de streaming et des tâches ; accès en lecture seule à d’autres ressources Media Services. | e4395492-1534-4db2-bedf-88c14621589c |
> | [Administrateur de stratégie Media Services](#media-services-policy-administrator) | Créer, lire, modifier et supprimer des filtres de compte, des stratégies de streaming, des stratégies de clés de contenu et des transformations ; accès en lecture seule à d’autres ressources Media Services. Impossible de créer des tâches, des ressources ou des ressources de streaming. | c4bba371-dacd-4a26-b320-7250bca963ae |
> | [Administrateur de points de terminaison de streaming Media Services](#media-services-streaming-endpoints-administrator) | Créer, lire, modifier et supprimer des points de terminaison de streaming ; accès en lecture seule à d’autres ressources Media Services. | 99dba123-b5fe-44d5-874c-ced7199a5804 |
> | [Contributeur de données d’index de la Recherche](#search-index-data-contributor) | Octroie un accès complet aux données d’index de la Recherche cognitive Azure. | 8ebe5a00-799e-43f5-93ac-243d3dce84a7 |
> | [Lecteur de données d’index de la Recherche](#search-index-data-reader) | Octroie un accès en lecture aux données d’index de la Recherche cognitive Azure. | 1407120a-92aa-4202-b7e9-c0e197c71c8f |
> | [Contributeur du service de recherche](#search-service-contributor) | Permet de gérer des services de recherche, mais pas d’y accéder. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Lecteur AccessKey SignalR](#signalr-accesskey-reader) | Lire les clés d’accès du service SignalR | 04165923-9d83-45d5-8227-78b77b0a687e |
> | [Serveur d’applications SignalR (préversion)](#signalr-app-server-preview) | Permet à votre serveur d’applications d’accéder au service SignalR avec les options d’authentification AAD. | 420fcaa2-552c-430f-98ca-3264be4806c7 |
> | [Propriétaire de l’API REST SignalR](#signalr-rest-api-owner) | Accès complet aux API REST du service Azure SignalR | fd53cd77-2268-407a-8f46-7e7863d0f521 |
> | [Lecteur de l’API REST SignalR](#signalr-rest-api-reader) | Accès en lecture seule aux API REST du service Azure SignalR | ddde6b66-c0df-4114-a159-3618637b3035 |
> | [Propriétaire SignalR Service](#signalr-service-owner) | Accès complet aux API REST du service Azure SignalR | 7e4f1700-ea5a-4f59-8f37-079cfe29dce3 |
> | [Contributeur SignalR/Web PubSub](#signalrweb-pubsub-contributor) | Créer, lire, mettre à jour et supprimer des ressources de service SignalR | 8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761 |
> | [Contributeur de plan web](#web-plan-contributor) | Gérez les plans web pour les sites web. Ne vous permet pas d’attribuer des rôles dans Azure RBAC. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Contributeur de site web](#website-contributor) | Gérez les sites web, mais pas les plans web. Ne vous permet pas d’attribuer des rôles dans Azure RBAC. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Containers** |  |  |
> | [AcrDelete](#acrdelete) | Supprimer des référentiels, des balises ou des manifestes d’un registre de conteneurs. | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | Envoyer (push) des images approuvées vers un registre de conteneurs activé pour l’approbation de contenu ou tirer (pull) des images approuvées d’un tel registre. | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | Tirer (pull) des artefacts à partir d’un registre de conteneurs. | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | Envoyer (push) des artefacts vers un registre de conteneurs ou tirer (pull) des artefacts d’un tel registre. | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | Tirer (pull) des images en quarantaine à partir d’un registre de conteneurs. | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | Envoyer (push) des images en quarantaine vers un registre de conteneurs ou extraire des images en quarantaine d’un tel registre. | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Rôle d’administrateur de cluster Azure Kubernetes Service](#azure-kubernetes-service-cluster-admin-role) | Répertorie les actions relatives aux informations d’identification de l’administrateur du cluster. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Rôle d’utilisateur de cluster Azure Kubernetes Service](#azure-kubernetes-service-cluster-user-role) | Répertorie les actions relatives aux informations d’identification de l’utilisateur du cluster. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Rôle Contributeur Azure Kubernetes Service](#azure-kubernetes-service-contributor-role) | Octroie l’accès en lecture et en écriture aux clusters Azure Kubernetes Service | ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8 |
> | [Azure Kubernetes Service RBAC Admin](#azure-kubernetes-service-rbac-admin) | Gérez toutes les ressources sous cluster/espace de noms, à l’exception de la mise à jour ou de la suppression de quotas de ressources et d’espaces de noms. | 3498e952-d568-435e-9b2c-8d77e338d7f7 |
> | [Azure Kubernetes Service RBAC Cluster Admin](#azure-kubernetes-service-rbac-cluster-admin) | Gérez toutes les ressources du cluster. | b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b |
> | [Azure Kubernetes Service RBAC Reader](#azure-kubernetes-service-rbac-reader) | Autorise l’accès en lecture seule pour voir la plupart des objets dans un espace de noms. Ce rôle n’autorise pas l’affichage des rôles ni des liaisons de rôles. Il n’autorise pas l’affichage des secrets, car la lecture du contenu de Secrets donne accès aux informations d’identification ServiceAccount dans l’espace de noms, ce qui permet l’accès aux API comme n’importe quel ServiceAccount dans l’espace de noms (une forme d’élévation de privilèges). L’application de ce rôle à l’étendue du cluster fournit un accès à tous les espaces de noms. | 7f6c6a51-bcf8-42ba-9220-52d62157d7db |
> | [Azure Kubernetes Service RBAC Writer](#azure-kubernetes-service-rbac-writer) | Autorise l’accès en lecture/écriture à la plupart des objets d’un espace de noms. Ce rôle n’autorise pas l’affichage ni la modification des rôles ou des liaisons de rôles. Toutefois, ce rôle permet d’accéder aux secrets et aux pods en cours d’exécution comme n’importe quel ServiceAccount de l’espace de noms. Il peut donc être utilisé pour obtenir les niveaux d’accès API de n’importe quel ServiceAccount dans l’espace de noms. L’application de ce rôle à l’étendue du cluster fournit un accès à tous les espaces de noms. | a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb |
> | **Bases de données** |  |  |
> | [Intégration de SQL Server connecté à Azure](#azure-connected-sql-server-onboarding) | Autorise l’accès en lecture et en écriture aux ressources Azure pour SQL Server sur les serveurs avec Arc. | e8113dce-c529-4d33-91fa-e9b972617508 |
> | [Rôle de lecteur de compte Cosmos DB](#cosmos-db-account-reader-role) | Lire les données de comptes Azure Cosmos DB. Consultez [Contributeur de compte DocumentDB](#documentdb-account-contributor) pour en savoir plus sur la gestion des comptes Azure Cosmos DB. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Opérateur Cosmos DB](#cosmos-db-operator) | Permet de gérer des comptes Azure Cosmos DB, mais pas d’accéder aux données qu’ils contiennent. Empêche d’accéder aux clés de compte et aux chaînes de connexion. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Peut envoyer une requête de restauration d’une base de données Cosmos DB ou d’un conteneur pour un compte | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [CosmosRestoreOperator](#cosmosrestoreoperator) | Peut effectuer une action de restauration pour un compte de base de données Cosmos DB avec le mode de sauvegarde continu | 5432c526-bc82-444a-b7ba-57c5b0b5b34f |
> | [Contributeur de compte DocumentDB](#documentdb-account-contributor) | Gérer des comptes Azure Cosmos DB. Azure Cosmos DB était auparavant appelé DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Contributeur Cache Redis](#redis-cache-contributor) | Permet de gérer des caches Redis, mais pas d’y accéder. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Contributeur de base de données SQL](#sql-db-contributor) | Permet de gérer des bases de données SQL, mais pas d’y accéder. Vous ne pouvez pas non plus gérer leurs stratégies de sécurité ni leurs serveurs SQL parents. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [Contributeur de SQL Managed Instance](#sql-managed-instance-contributor) | Permet de gérer des instances SQL Managed Instance et la configuration réseau requise, mais pas d’accorder l’accès à d’autres personnes. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [Gestionnaire de sécurité SQL](#sql-security-manager) | Permet de gérer les stratégies de sécurité des serveurs et bases de données SQL, mais pas d’y accéder. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [Contributeur SQL Server](#sql-server-contributor) | Permet de gérer des serveurs et bases de données SQL, mais pas d’y accéder, ni de gérer leurs stratégies de sécurité. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analyse** |  |  |
> | [Propriétaire de données Azure Event Hubs](#azure-event-hubs-data-owner) | Permet un accès complet aux ressources Azure Event Hubs. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Récepteur de données Azure Event Hubs](#azure-event-hubs-data-receiver) | Permet d’obtenir un accès en réception aux ressources Azure Event Hubs. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Expéditeur de données Azure Event Hubs](#azure-event-hubs-data-sender) | Permet d’obtenir un accès en envoi aux ressources Azure Event Hubs. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Contributeurs de fabrique de données](#data-factory-contributor) | Créer et gérer des fabriques de données, ainsi que les ressources enfants qu’elles contiennent. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Videur de données](#data-purger) | Supprimez des données privées à partir d’un espace de travail Log Analytics. | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Opérateur de cluster HDInsight](#hdinsight-cluster-operator) | Permet de lire et de modifier des configurations de cluster HDInsight. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Contributeur HDInsight Domain Services](#hdinsight-domain-services-contributor) | Peut lire, créer, modifier et supprimer les opérations Domain Services nécessaires pour le pack Sécurité Entreprise HDInsight | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Contributeur Log Analytics](#log-analytics-contributor) | Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. La modification des paramètres de supervision inclut l’ajout de l’extension de machine virtuelle aux machines virtuelles, la lecture des clés de comptes de stockage permettant de configurer la collection de journaux d’activité du stockage Azure, l’ajout de solutions et la configuration de diagnostics Azure sur toutes les ressources Azure. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Lecteur Log Analytics](#log-analytics-reader) | Peut afficher et rechercher toutes les données de surveillance, ainsi qu’afficher les paramètres de surveillance, notamment la configuration des diagnostics Azure sur toutes les ressources Azure. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Conservateur de données Purview (hérité)](#purview-data-curator-legacy) | Le rôle hérité Conservateur de données Microsoft.Purview peut créer, lire, modifier et supprimer des objets de données de catalogue et établir des relations entre les objets. Nous avons récemment déconseillé ce rôle pour l’accès en fonction du rôle Azure et introduit un nouveau conservateur de données dans le plan de données Azure Purview. Consultez [Contrôle d’accès dans Azure Purview - Rôles](../purview/catalog-permissions.md#roles) | 8a3c2885-9b38-4fd2-9d99-91af537c1347 |
> | [Lecteur de données Purview (hérité)](#purview-data-reader-legacy) | Le lecteur de données Microsoft. Purview est un rôle hérité qui peut lire les objets de données de catalogue. Nous avons récemment déconseillé ce rôle pour l’accès en fonction du rôle Azure et introduit un nouveau lecteur de données dans le plan de données Azure Purview. Consultez [Contrôle d’accès dans Azure Purview - Rôles](../purview/catalog-permissions.md#roles) | ff100721-1b9d-43d8-af52-42b69c1272db |
> | [Administrateur de source de données Purview (hérité)](#purview-data-source-administrator-legacy) | Le rôle hérité Administrateur de source de données Microsoft.Purview peut gérer les sources de données et les analyses de données. Nous avons récemment déconseillé ce rôle pour l’accès en fonction du rôle Azure et introduit un nouvel administrateur de source de données dans le plan de données Azure Purview. Consultez [Contrôle d’accès dans Azure Purview - Rôles](../purview/catalog-permissions.md#roles) | 200bba9e-f0c8-430f-892b-6f0794863803 |
> | [Contributeur du registre de schémas (préversion)](#schema-registry-contributor-preview) | Lire, écrire et supprimer des groupes de registres de schémas et des schémas. | 5dffeca3-4936-4216-b2bc-10343a5abb25 |
> | [Lecteur du registre de schémas (préversion)](#schema-registry-reader-preview) | Lire et répertorier les groupes de registres de schémas et les schémas. | 2c56ea50-c6b3-40a6-83c0-9d98858bc7d2 |
> | **Blockchain** |  |  |
> | [Accès au nœud du membre blockchain (préversion)](#blockchain-member-node-access-preview) | Permet d’accéder aux nœuds du membre blockchain | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **IA + machine learning** |  |  |
> | [Scientifique des données AzureML](#azureml-data-scientist) | Peut effectuer toutes les actions au sein d’un espace de travail Azure Machine Learning, à l’exception de la création ou de la suppression des ressources de calcul et de la modification de l’espace de travail lui-même. | f6c7c914-8db3-469d-8ca1-694a8f32e121 |
> | [Contributeur Cognitive Services](#cognitive-services-contributor) | Vous permet de créer, lire, mettre à jour, supprimer et gérer les clés de Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Contributeur Cognitive Services Custom Vision](#cognitive-services-custom-vision-contributor) | Accès complet au projet, y compris la possibilité de visualiser, créer, modifier et supprimer des projets. | c1ff6cc2-c111-46fe-8896-e0ef812ad9f3 |
> | [Déploiement de Cognitive Services Custom Vision](#cognitive-services-custom-vision-deployment) | Publier, dépublier ou exporter des modèles. Le déploiement peut visualiser le projet, mais ne peut pas le mettre à jour. | 5c4089e1-6d96-4d2f-b296-c1bc7137275f |
> | [Étiqueteur Cognitive Services Custom Vision](#cognitive-services-custom-vision-labeler) | Visualiser, modifier des images d’entraînement, et créer, ajouter, supprimer ou effacer les étiquettes des images. Les étiqueteurs peuvent visualiser le projet, mais ne peuvent pas mettre à jour autre chose que des images d’entraînement et des étiquettes. | 88424f51-ebe7-446f-bc41-7fa16989e96c |
> | [Lecteur Cognitive Services Custom Vision](#cognitive-services-custom-vision-reader) | Actions en lecture seule dans le projet. Les lecteurs ne peuvent pas créer ni mettre à jour le projet. | 93586559-c37d-4a6b-ba08-b9f0940c2d73 |
> | [Entraîneur Cognitive Services Custom Vision](#cognitive-services-custom-vision-trainer) | Afficher, modifier les projets et entraîner les modèles, avec la possibilité de publier, de dépublier, d’exporter les modèles. Les entraîneurs ne peuvent pas créer ni supprimer le projet. | 0a5ae4ab-0d65-4eeb-be61-29fc9b54394b |
> | [Lecteur de données Cognitive Services (préversion)](#cognitive-services-data-reader-preview) | Permet de lire des données Cognitive Services. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services : Face Recognizer](#cognitive-services-face-recognizer) | Vous permet de détecter, de vérifier, d’identifier, de regrouper et de rechercher des opérations similaires sur l’API Visage. Ce rôle n’autorise pas les opérations de création ou de suppression, ce qui en fait un bon choix pour les points de terminaison nécessitant uniquement des fonctionnalités d’inférence, en suivant les meilleures pratiques en matière de « privilèges minimum ». | 9894cab4-e18a-44aa-828b-cb588cd6f2d7 |
> | [Administrateur Cognitive Services Metrics Advisor](#cognitive-services-metrics-advisor-administrator) | Accès complet au projet, y compris la configuration au niveau du système. | cb43c632-a144-4ec5-977c-e80c4affc34a |
> | [Éditeur QnA Maker Cognitive Services](#cognitive-services-qna-maker-editor) | Vous permet de créer, modifier, importer et exporter une base de connaissances. Vous ne pouvez pas publier ni supprimer une base de connaissances. | f4cc2bf9-21be-47a1-bdf1-5c5804381025 |
> | [Lecteur QnA Maker Cognitive Services](#cognitive-services-qna-maker-reader) | Vous permet de seulement lire et tester une base de connaissances. | 466ccd10-b268-4a11-b098-b4849f024126 |
> | [Utilisateur Cognitive Services](#cognitive-services-user) | Vous permet de lire et de répertorier les clés de Cognitive Services. | a97b65f3-24C7-4388-baec-2e87135dc908 |
> | **Internet des objets** |  |  |
> | [Administrateur Device Update](#device-update-administrator) | Vous accorde l’accès complet aux opérations de gestion et de contenu | 02ca0879-e8e4-47a5-a61e-5c618b76e64a |
> | [Administrateur de contenu Device Update](#device-update-content-administrator) | Vous accorde l’accès complet aux opérations de contenu | 0378884a-3af5-44ab-8323-f5b22f9f3c98 |
> | [Lecteur du contenu Device Update](#device-update-content-reader) | Vous accorde l’accès en lecture aux opérations de contenu, mais ne vous permet pas d’effectuer des modifications | d1ee9a80-8b14-47f0-bdc2-f4a351625a7b |
> | [Administrateur des déploiements Device Update](#device-update-deployments-administrator) | Vous accorde l’accès complet aux opérations de gestion | e4237640-0e3d-4a46-8fda-70bc94856432 |
> | [Lecteur des déploiements Device Update](#device-update-deployments-reader) | Vous accorde l’accès en lecture aux opérations de gestion, mais ne vous permet pas d’effectuer des modifications | 49e2f5d2-7741-4835-8efa-19e1fe35e47f |
> | [Lecteur Device Update](#device-update-reader) | Vous accorde l’accès en lecture aux opérations de gestion et de contenu, mais ne vous permet pas d’effectuer des modifications | e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f |
> | [Contributeur de données IoT Hub](#iot-hub-data-contributor) | Permet un accès complet aux opérations du plan de données IoT Hub. | 4fc6c259-987e-4a07-842e-c321cc9d413f |
> | [Lecteur de données IoT Hub](#iot-hub-data-reader) | Permet un accès en lecture complet aux propriétés du plan de données IoT Hub. | b447c946-2db7-41ec-983d-d8bf3b1c77e3 |
> | [Contributeur du Registre IoT Hub](#iot-hub-registry-contributor) | Permet un accès complet au Registre de l’appareil IoT Hub. | 4ea46cd5-c1b2-4a8e-910b-273211f9ce47 |
> | [Contributeur de jumeau IoT Hub](#iot-hub-twin-contributor) | Autorise l’accès en lecture et en écriture à tous les appareils et jumeaux de module IoT Hub. | 494bdba2-168f-4f31-a0a1-191d2f7c028c |
> | **Réalité mixte** |  |  |
> | [Administrateur Remote Rendering](#remote-rendering-administrator) | Fournit à l’utilisateur des fonctionnalités de conversion, de gestion de session, de rendu et de diagnostic pour Azure Remote Rendering | 3df8b902-2a6f-47c7-8cc5-360e9b272a7e |
> | [Client Remote Rendering](#remote-rendering-client) | Fournit à l’utilisateur des fonctionnalités de gestion de session, de rendu et de diagnostic pour Azure Remote Rendering | d39065c4-c120-43c9-ab0a-63eed9795f0a |
> | [Contributeur de compte Spatial Anchors](#spatial-anchors-account-contributor) | Permet de gérer des ancres spatiales dans votre compte, mais pas de les supprimer | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Propriétaire de compte Spatial Anchors](#spatial-anchors-account-owner) | Permet de gérer des ancres spatiales dans votre compte, y compris de les supprimer | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Lecteur de compte Spatial Anchors](#spatial-anchors-account-reader) | Permet de localiser et de lire les propriétés d’ancres spatiales dans votre compte | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Intégration** |  |  |
> | [Contributeur du service Gestion des API](#api-management-service-contributor) | Peut gérer le service et les API | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Rôle d’opérateur du service Gestion des API](#api-management-service-operator-role) | Peut gérer le service, mais pas les API | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Rôle de lecteur du service Gestion des API](#api-management-service-reader-role) | Accès en lecture seule au service et aux API | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Propriétaire des données App Configuration](#app-configuration-data-owner) | Permet l’accès total aux données App Configuration. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Lecteur des données App Configuration](#app-configuration-data-reader) | Permet de lire les données App Configuration. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Écouteur Azure Relay](#azure-relay-listener) | Autorise l’accès en écoute aux ressources Azure Relay. | 26e0b698-aa6d-4085-9386-aadae190014d |
> | [Propriétaire Azure Relay](#azure-relay-owner) | Autorise l’accès total aux ressources Azure Relay. | 2787bf04-f1f5-4bfe-8383-c8a24483ee38 |
> | [Expéditeur Azure Relay](#azure-relay-sender) | Autorise l’accès en envoi aux ressources Azure Relay. | 26baccc8-eea7-41f1-98f4-1762cc7f685d |
> | [Propriétaire de données Azure Service Bus](#azure-service-bus-data-owner) | Permet un accès total aux ressources Azure Service Bus. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Récepteur de données Azure Service Bus](#azure-service-bus-data-receiver) | Permet d’obtenir un accès en réception aux ressources Azure Service Bus. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Expéditeur de données Azure Service Bus](#azure-service-bus-data-sender) | Permet d’obtenir un accès en envoi aux ressources Azure Service Bus. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Propriétaire de l’inscription Azure Stack](#azure-stack-registration-owner) | Permet de gérer les inscriptions Azure Stack. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Contributeur Eventgrid](#eventgrid-contributor) | Vous permet de gérer les opérations EventGrid. | 1e241071-0855-49ea-94dc-649edcd759de |
> | [Expéditeur de données EventGrid](#eventgrid-data-sender) | Autorise l’accès en envoi aux événements Event Grid. | d5a91429-5739-47e2-a06b-3470a27159e7 |
> | [Contributeur EventGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Vous permet de gérer les opérations d’abonnement aux événements EventGrid. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [Lecteur EventGrid EventSubscription](#eventgrid-eventsubscription-reader) | Vous permet de lire les abonnements aux événements EventGrid. | 2414bbcf-6497-4FAF-8c65-045460748405 |
> | [Contributeur aux données FHIR](#fhir-data-contributor) | Ce rôle accorde à l’utilisateur ou au principal un accès complet aux données FHIR | 5a1fc7df-4bf1-4951-a576-89034ee01acd |
> | [Exportateur de données FHIR](#fhir-data-exporter) | Ce rôle permet à l’utilisateur ou au principal de lire et d’exporter des données FHIR | 3db33094-8700-4567-8da5-1501d4e7e843 |
> | [Lecteur de données FHIR](#fhir-data-reader) | Ce rôle permet à l’utilisateur ou au principal de lire des données FHIR | 4c8d0bbc-75d3-4935-991f-5f3c56d81508 |
> | [Enregistreur de données FHIR](#fhir-data-writer) | Ce rôle permet à l’utilisateur ou au principal de lire et d’écrire des données FHIR | 3f88fce4-5892-4214-ae73-ba5294559913 |
> | [Contributeur de l’environnement de service d’intégration](#integration-service-environment-contributor) | Permet de gérer les environnements de service d’intégration, mais pas d’y accéder. | a41e2c5b-bd99-4a07-88f4-9bf657a760b8 |
> | [Développeur d’environnement de service d’intégration](#integration-service-environment-developer) | Permet aux développeurs de créer et de mettre à jour des workflows, des comptes d’intégration et des connexions d’API dans les environnements de service d’intégration. | c7aa55d3-1abb-444a-a5ca-5e51e485d6ec |
> | [Contributeur de compte Intelligent Systems](#intelligent-systems-account-contributor) | Permet de gérer des comptes Intelligent Systems, mais pas d’y accéder. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Contributeur d’application logique](#logic-app-contributor) | Permet de gérer des applications logiques, mais pas d’en modifier l’accès. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Opérateur d’application logique](#logic-app-operator) | Permet de lire, d’activer et de désactiver des applications logiques, mais pas de les modifier ou de les mettre à jour. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identité** |  |  |
> | [Contributeur d’identités gérées](#managed-identity-contributor) | Peut créer, lire, mettre à jour et supprimer une identité attribuée à l’utilisateur. | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Opérateur d’identités gérées](#managed-identity-operator) | Peut lire et assigner une identité attribuée à l’utilisateur. | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Sécurité** |  |  |
> | [Contributeur d’attestation](#attestation-contributor) | Peut lire, écrire ou supprimer l’instance du fournisseur d’attestations | bbf86eb8-f7b4-4cce-96e4-18cddf81d86e |
> | [Lecteur d’attestation](#attestation-reader) | Peut lire les propriétés du fournisseur d’attestations | fd1bd22b-8476-40bc-a0bc-69b95687b9f3 |
> | [Administrateur Key Vault](#key-vault-administrator) | Permet d’effectuer toutes les opération du plan de données sur un coffre de clés et tous les objets qu’il contient, notamment les certificats, les clés et les secrets. Ne peut pas gérer les ressources du coffre de clés ni gérer les attributions de rôles. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
> | [Agent des certificats Key Vault](#key-vault-certificates-officer) | Permet d’effectuer une action sur les certificats d’un coffre de clés, à l’exception des autorisations de gestion. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | a4417e6f-fecd-4de8-b567-7b0420556985 |
> | [Contributeur Key Vault](#key-vault-contributor) | Permet de gérer les coffres de clés, mais ne vous permet pas d’attribuer des rôles dans Azure RBAC ni d’accéder à des secrets, des clés ou des certificats. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Agent de chiffrement Key Vault](#key-vault-crypto-officer) | Permet d’effectuer une action sur les clés d’un coffre de clés, à l’exception des autorisations de gestion. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
> | [Utilisateur du service de chiffrement de Key Vault](#key-vault-crypto-service-encryption-user) | Permet de lire les métadonnées des clés et d’effectuer des opérations visant à envelopper/désenvelopper. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
> | [Utilisateur de chiffrement Key Vault](#key-vault-crypto-user) | Permet d’effectuer des opérations de chiffrement à l’aide de clés. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | 12338af0-0e69-4776-bea7-57ae8d297424 |
> | [Lecteur Key Vault](#key-vault-reader) | Permet de lire les métadonnées de coffres de clés et de leurs certificats, clés et secrets. Ne peut pas lire les valeurs sensibles, telles que les contenus secrets ou les documents clés. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | 21090545-7ca7-4776-b22c-e363652d74d2 |
> | [Agent des secrets Key Vault](#key-vault-secrets-officer) | Permet d’effectuer une action sur les secrets d’un coffre de clés, à l’exception des autorisations de gestion. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
> | [Utilisateur des secrets Key Vault](#key-vault-secrets-user) | Permet de lire le contenu du secret. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». | 4633458b-17de-408a-b874-0445c86b69e6 |
> | [Contributeur HSM managé](#managed-hsm-contributor) | Vous permet de gérer des pools HSM managés, mais pas d’y accéder. | 18500a29-7fe2-46b2-a342-b16a415e101d |
> | [Contributeur Microsoft Sentinel Automation](#microsoft-sentinel-automation-contributor) | Contributeur Microsoft Sentinel Automation | f4c81013-99ee-4d62-a7ee-b3f1f648599a |
> | [Contributeur Microsoft Sentinel](#microsoft-sentinel-contributor) | Contributeur Microsoft Sentinel | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Lecteur Microsoft Sentinel](#microsoft-sentinel-reader) | Lecteur Microsoft Sentinel | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Répondeur Microsoft Sentinel](#microsoft-sentinel-responder) | Répondeur Microsoft Sentinel | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Administrateur de la sécurité](#security-admin) | Autorisations d’affichage et de mise à jour pour Security Center. Dispose des mêmes autorisations que le rôle Lecteur de sécurité et peut également modifier la stratégie de sécurité et ignorer les alertes et les recommandations. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Contributeur d'évaluation de la sécurité](#security-assessment-contributor) | Vous permet d’envoyer (push) les évaluations à Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Gestionnaire de sécurité (hérité)](#security-manager-legacy) | Il s’agit d’un rôle hérité. Utilisez plutôt l’administrateur de sécurité. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Lecteur de sécurité](#security-reader) | Autorisations d’affichage pour Security Center. Peut afficher les recommandations, les alertes, une stratégie de sécurité et les états de sécurité, mais ne peut pas apporter de modifications. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [Utilisateur de DevTest Labs](#devtest-labs-user) | Permet de connecter, de démarrer, de redémarrer et d’arrêter vos machines virtuelles dans votre Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Créateur Lab](#lab-creator) | Créez des labs sous vos comptes Azure Lab. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Surveiller** |  |  |
> | [Contributeur de composants Application Insights](#application-insights-component-contributor) | Gérer les composants Application Insights | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Débogueur de capture instantanée d’Application Insights](#application-insights-snapshot-debugger) | Autorise l’utilisateur à consulter et à télécharger les instantanés de débogage collectés à l’aide du débogueur de capture instantanée Application Insights. Ces autorisations ne sont pas incluses dans les rôles [Propriétaire](#owner) et [Contributeur](#contributor). Lorsque vous donnez aux utilisateurs le rôle Débogueur de capture instantanée Application Insights, vous devez leur accorder directement le rôle. Le rôle n’est pas reconnu lorsqu’il est ajouté à un rôle personnalisé. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Contributeur de surveillance](#monitoring-contributor) | Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Publication des métriques de surveillance](#monitoring-metrics-publisher) | Permet de publier les métriques relatives aux ressources Azure | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Lecteur de surveillance](#monitoring-reader) | Peut lire toutes les données de supervision (métriques, journaux d’activité, etc.) Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Contributeur de classeur](#workbook-contributor) | Peut enregistrer les classeurs partagés. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Lecteur de classeur](#workbook-reader) | Peut lire les classeurs. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Gestion + gouvernance** |  |  |
> | [Contributeur Automation](#automation-contributor) | Gérez les ressources Azure Automation et d’autres ressources à l’aide d’Azure Automation. | f353d9bd-d4a6-484e-a77a-8050b599b867 |
> | [Opérateur de travaux Automation](#automation-job-operator) | Permet de créer et de gérer des travaux avec des runbooks Automation. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Opérateur Automation](#automation-operator) | Les opérateurs d’Automation sont en mesure de démarrer, d’arrêter, de suspendre et de reprendre des travaux | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Opérateur de runbook Automation](#automation-runbook-operator) | Propriétés de lecture du runbook : pour pouvoir créer des travaux depuis le runbook. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Rôle d'utilisateur de cluster Kubernetes avec Azure Arc](#azure-arc-enabled-kubernetes-cluster-user-role) | Répertorie les actions relatives aux informations d'identification de l'utilisateur du cluster. | 00493d72-78f6-4148-b6c5-d3ce8e4799dd |
> | [Azure Arc Kubernetes Admin](#azure-arc-kubernetes-admin) | Gérez toutes les ressources sous cluster/espace de noms, à l’exception de la mise à jour ou de la suppression de quotas de ressources et d’espaces de noms. | dffb1e0c-446f-4dde-a09f-99eb5cc68b96 |
> | [Azure Arc Kubernetes Cluster Admin](#azure-arc-kubernetes-cluster-admin) | Gérez toutes les ressources du cluster. | 8393591c-06b9-48a2-a542-1bd6b377f6a2 |
> | [Azure Arc Kubernetes Viewer](#azure-arc-kubernetes-viewer) | Affichez toutes les ressources dans le cluster/l’espace de noms, à l’exception des secrets. | 63f0a09d-1495-4db4-a681-037d84835eb4 |
> | [Azure Arc Kubernetes Writer](#azure-arc-kubernetes-writer) | Vous permet de mettre à jour tout ce qui se trouve dans le cluster/espace de noms, à l'exception des rôles (de cluster) et des liaisons de rôles (de cluster). | 5b999177-9696-4545-85c7-50de3797e5a1 |
> | [Intégration de machine connectée à Azure](#azure-connected-machine-onboarding) | Peut intégrer des machines connectées à Azure. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Administrateur des ressources de la machine connectée à Azure](#azure-connected-machine-resource-administrator) | Peut lire, écrire, supprimer et réintégrer des machines connectées à Azure. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Lecteur de facturation](#billing-reader) | Autorise l’accès en lecture aux données de facturation | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Contributeur blueprint](#blueprint-contributor) | Peut gérer les définitions blueprint, mais ne peut pas les affecter. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Opérateur blueprint](#blueprint-operator) | Peut affecter des blueprints publiés existants, mais ne peut pas en créer de nouveaux. Notez que cela fonctionne uniquement si l’affectation est effectuée avec une identité managée affectée par l’utilisateur. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Contributeur Cost Management](#cost-management-contributor) | Peut afficher les coûts et gérer la configuration des coûts (par exemple, budgets, exportations) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Lecteur Cost Management](#cost-management-reader) | Peut afficher les données et la configuration des coûts (par exemple, budgets, exportations) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Administration des paramètres de hiérarchie](#hierarchy-settings-administrator) | Permet aux utilisateurs de modifier et de supprimer des paramètres de hiérarchie | 350f8d15-c687-4448-8ae1-157740a3936d |
> | [Cluster Kubernetes – Intégration Azure Arc](#kubernetes-cluster---azure-arc-onboarding) | Définition de rôle pour autoriser tout utilisateur/service à créer une ressource connectedClusters | 34e09817-6cbe-4d01-b1a2-e0eac5743d41 |
> | [Contributeur d’extension Kubernetes](#kubernetes-extension-contributor) | Peut créer, mettre à jour, obtenir, répertorier et supprimer des extensions Kubernetes, et obtenir des opérations asynchrones d’extension | 85cb6faf-e071-4c9b-8136-154b5a04f717 |
> | [Rôle Contributeur d'application managée](#managed-application-contributor-role) | Permet de créer des ressources d’application managées. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Rôle opérateur d’application managée](#managed-application-operator-role) | Permet de lire les ressources d’application managée et d’effectuer des actions sur ces ressources. | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Lecteur Applications managées](#managed-applications-reader) | Vous permet de lire les ressources dans une application managée et de demander un accès JIT. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Suppression du rôle d’attribution d’inscription de services managé](#managed-services-registration-assignment-delete-role) | La suppression du rôle d’attribution d’inscription de services managés permet aux utilisateurs du client gérant de supprimer l’attribution d’inscription assignée à leur locataire. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Contributeur du groupe d’administration](#management-group-contributor) | Rôle de collaborateur du groupe d’administration | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Lecteur du groupe d’administration](#management-group-reader) | Rôle de lecteur du groupe d’administration | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Contributeur de compte NewRelic APM](#new-relic-apm-account-contributor) | Vous permet de gérer des comptes et applications New Relic Application Performance Management, mais pas d’y accéder. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Policy Insights Data Writer (préversion)](#policy-insights-data-writer-preview) | Permet de lire les stratégies de ressources et d’écrire les événements de stratégie de composant de ressource. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Opérateur de requête de quota](#quota-request-operator) | Lisez et créez des requêtes de quota, obtenez l’état de la requête de quota et créez des tickets de support. | 0e5f05e5-9ab9-446b-b98d-1e2157c94125 |
> | [Acheteur de réservation](#reservation-purchaser) | Vous permet d’acheter des réservations | f7b75c60-3036-4b75-91c3-6b41c27c1689 |
> | [Contributeur de stratégie de ressource](#resource-policy-contributor) | Utilisateurs dotés de droits pour créer ou modifier une stratégie de ressource, créer un ticket de support et lire des ressources ou la hiérarchie. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Contributeur Site Recovery](#site-recovery-contributor) | Permet de gérer le service Site Recovery sauf la création de coffre et l’attribution de rôle | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Opérateur Site Recovery](#site-recovery-operator) | Permet de basculer et de restaurer mais pas d’effectuer d’autres opérations de gestion de Site Recovery | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Lecteur Site Recovery](#site-recovery-reader) | Permet d’afficher l’état de Site Recovery mais pas d’effectuer d’autres opérations de gestion | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Contributeur de demande de support](#support-request-contributor) | Permet de créer et de gérer des demandes de support | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Contributeur d’étiquette](#tag-contributor) | Vous permet de gérer les étiquettes sur les entités, sans fournir l’accès aux entités elles-mêmes. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Autres** |  |  |
> | [Propriétaire des données Azure Digital Twins](#azure-digital-twins-data-owner) | Rôle d’accès complet pour le plan de données Digital Twins | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
> | [Lecteur de données Azure Digital Twins](#azure-digital-twins-data-reader) | Rôle en lecture seule pour les propriétés du plan de données Digital Twins | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |
> | [Contributeur BizTalk](#biztalk-contributor) | Permet de gérer des services BizTalk, mais pas d’y accéder. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Contributeur du groupe d’applications de virtualisation de poste de travail](#desktop-virtualization-application-group-contributor) | Contributeur du groupe d’applications de virtualisation de poste de travail. | 86240b0e-9422-4c43-887b-b61143f32ba8 |
> | [Lecteur du groupe d’applications de virtualisation de poste de travail](#desktop-virtualization-application-group-reader) | Lecteur du groupe d’applications de virtualisation de poste de travail. | aebf23d0-b568-4e86-b8f9-fe83a2c6ab55 |
> | [Contributeur de virtualisation des services Bureau](#desktop-virtualization-contributor) | Contributeur de virtualisation de poste de travail | 082f0a83-3be5-4ba1-904c-961cca79b387 |
> | [Contributeur de pool d’hôtes de virtualisation de poste de travail](#desktop-virtualization-host-pool-contributor) | Contributeur de pool d’hôtes de virtualisation de poste de travail. | e307426c-f9b6-4e81-87de-d99efb3c32bc |
> | [Lecteur de pool d’hôtes de virtualisation de poste de travail](#desktop-virtualization-host-pool-reader) | Lecteur de pool d’hôtes de virtualisation de poste de travail. | ceadfde2-b300-400a-ab7b-6143895aa822 |
> | [Lecteur de virtualisation des services Bureau](#desktop-virtualization-reader) | Lecteur de virtualisation de poste de travail | 49a72310-ab8d-41df-bbb0-79b649203868 |
> | [Opérateur d’hôte de session de virtualisation de virtualisation de poste de travail](#desktop-virtualization-session-host-operator) | Opérateur d’hôte de session de virtualisation de virtualisation de poste de travail. | 2ad6aaab-ead9-4eaa-8ac5-da422f562408 |
> | [Utilisateur de virtualisation de bureau](#desktop-virtualization-user) | Permet à l’utilisateur d’utiliser les applications dans un groupe d’applications. | 1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63 |
> | [Opérateur de session utilisateur de virtualisation de poste de travail](#desktop-virtualization-user-session-operator) | Opérateur de la session utilisateur de virtualisation de poste de travail. | ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6 |
> | [Contributeur d’espace de travail de virtualisation de poste de travail](#desktop-virtualization-workspace-contributor) | Contributeur d’espace de travail de virtualisation de poste de travail. | 21efdde3-836f-432b-bf3d-3e8e734d4b2b |
> | [Lecteur d’espace de travail de virtualisation de poste de travail](#desktop-virtualization-workspace-reader) | Lecteur d’espace de travail de virtualisation de poste de travail. | 0fa44ee9-7a7d-466b-9bb2-2bf446b1204d |
> | [Lecteur de sauvegarde de disque](#disk-backup-reader) | Fournit une autorisation sur le coffre de sauvegarde pour effectuer une sauvegarde de disque. | 3e5e47e6-65f7-47ef-90b5-e5dd4d455f24 |
> | [Opérateur de pool de disques](#disk-pool-operator) | Accordez l’autorisation au fournisseur de ressources StoragePool pour gérer les disques ajoutés à un pool de disques. | 60fc6e62-5479-42d4-8bf4-67625fcc2840 |
> | [Opérateur de restauration de disque](#disk-restore-operator) | Fournit une autorisation sur le coffre de sauvegarde pour effectuer une restauration de disque. | b50d9833-a0cb-478e-945f-707fcc997c13 |
> | [Contributeur d’instantané de disque](#disk-snapshot-contributor) | Fournit une autorisation sur le coffre de sauvegarde pour gérer les instantanés de disque. | 7efff54f-a5b4-42b5-a1c5-5411624893ce |
> | [Contributeur des collections de travaux du planificateur](#scheduler-job-collections-contributor) | Permet de gérer des collections de tâches du planificateur, mais pas d’y accéder. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Opérateur de hub de services](#services-hub-operator) | L’opérateur de hub de services vous permet d’effectuer toutes les opérations de lecture, d’écriture et de suppression liées aux connecteurs de hub de services. | 82200a5b-e217-47a5-b665-6d8765ee745b |


## <a name="general"></a>Général


### <a name="contributor"></a>Contributeur

Accorde un accès total pour gérer toutes les ressources, mais ne vous permet pas d’affecter des rôles dans Azure RBAC, de gérer des affectations dans Azure Blueprints ou de partager des galeries d’images. [En savoir plus](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | * | Créer et gérer les ressources de tous les types |
> | **NotActions** |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Delete | Supprimer des rôles, des affectations de stratégie, des définitions de stratégie et des définitions d’ensemble de stratégies |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/Write | Créer des rôles, des attributions de rôle, des affectations de stratégie, des définitions de stratégie et des définitions d’ensemble de stratégies |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/elevateAccess/Action | Accorde à l’appelant un accès Administrateur de l’accès utilisateur au niveau de la portée du client |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/write | Créer ou mettre à jour toutes les affectations de blueprint |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/delete | Supprimer toutes les affectations de blueprint |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/galleries/share/action | Partage une galerie sur des différentes étendues |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, but does not allow you to assign roles in Azure RBAC, manage assignments in Azure Blueprints, or share image galleries.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete",
        "Microsoft.Compute/galleries/share/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Propriétaire

Octroie un accès total pour gérer toutes les ressources, notamment la possibilité d’attribuer des rôles dans Azure RBAC. [En savoir plus](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | * | Créer et gérer les ressources de tous les types |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to manage all resources, including the ability to assign roles in Azure RBAC.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Lecteur

Affiche toutes les ressources, mais ne vous autorise pas à apporter des modifications. [En savoir plus](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View all resources, but does not allow you to make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Administrateur de l'accès utilisateur

Vous permet de gérer l'accès utilisateur aux ressources Azure. [En savoir plus](rbac-and-directory-admin-roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/* | Gérer les autorisations |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Calcul


### <a name="classic-virtual-machine-contributor"></a>Contributeur de machine virtuelle classique

Permet de gérer des machines virtuelles classiques, mais pas d’y accéder, ni au réseau virtuel ou au compte de stockage auquel elles sont connectées.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/domainNames/* | Créer et gérer des noms de domaine de calcul classique |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/* | Créer et gérer les machines virtuelles |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/networkSecurityGroups/join/action |  |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/link/action | Lier une adresse IP réservée |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/reservedIps/read | Obtient les adresses IP réservées |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/join/action | Joint le réseau virtuel. |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/virtualNetworks/read | Obtenez le réseau virtuel. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/disks/read | Retourne le disque du compte de stockage. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/images/read | Retourne l’image du compte de stockage. (Déconseillé. Utilisez « Microsoft.ClassicStorage/storageAccounts/vmImages ») |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Répertorie les clés d’accès des comptes de stockage. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Retourne le compte de stockage avec le compte spécifique. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Connexion de l’administrateur aux machines virtuelles

Afficher les machines virtuelles dans le portail et se connecter en tant qu’administrateur [En savoir plus](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Obtient une définition de l’adresse IP publique. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Obtient une définition d’équilibrage de charge. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Obtient une définition d’interface réseau.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/*/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Se connecter à la machine virtuelle comme utilisateur normal |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/loginAsAdmin/action | Se connecter à une machine virtuelle avec des privilèges d’administrateur Windows ou d’utilisateur racine Linux |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/login/action | Se connecte à la machine Azure Arc comme utilisateur normal |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/loginAsAdmin/action | Se connecte à une machine Azure Arc avec des privilèges d’administrateur Windows ou d’utilisateur root Linux |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.HybridCompute/machines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action",
        "Microsoft.HybridCompute/machines/login/action",
        "Microsoft.HybridCompute/machines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Contributeur de machine virtuelle

Créer et gérer des machines virtuelles, gérer des disques et des instantanés de disques, installer et exécuter des logiciels, réinitialiser le mot de passe de l’utilisateur racine de la machine virtuelle à l’aide d’extensions de machine virtuelle et gérer les comptes d’utilisateurs locaux à l’aide d’extensions de machine virtuelle. Ce rôle ne vous accorde pas l’accès de gestion au réseau virtuel ou au compte de stockage auquel les machines virtuelles sont connectées. Ce rôle ne vous permet pas d’attribuer des rôles dans Azure RBAC. [En savoir plus](/azure/architecture/reference-architectures/n-tier/linux-vm)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* | Créer et gérer des groupes à haute disponibilité de calcul |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/locations/* | Créer et gérer des emplacements de calcul |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* | Effectuer toutes les actions de machine virtuelle, notamment créer, mettre à jour, supprimer, démarrer, redémarrer et mettre hors tension des machines virtuelles. Exécuter des scripts sur des machines virtuelles |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachineScaleSets/* | Créez et gérez des jeux de mise à l’échelle des machines virtuelles |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/cloudServices/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Créer ou mettre à jour un disque |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Obtenir les propriétés d’un disque |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/delete | Supprimer le disque |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/schedules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/applicationGateways/backendAddressPools/join/action | Joint un pool d’adresses principales de passerelle d’application. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Joint un pool d’adresses principales d’équilibrage de charge. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatPools/join/action | Joint un pool NAT entrant d’équilibrage de charge. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Joint une règle nat de trafic entrant d’équilibrage de charge. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/probes/join/action | Autorise l’utilisation des sondes d’un équilibreur de charge. Par exemple, avec cette autorisation, la propriété healthProbe du groupe de machines virtuelles identiques peut faire référence à la sonde. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Obtient une définition d’équilibrage de charge. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/locations/* | Créer et gérer des emplacements réseau |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* | Créer et gérer des interfaces réseau |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Joint un groupe de sécurité réseau. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/read | Obtient une définition de groupe de sécurité réseau. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Joint une adresse IP publique. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Obtient une définition de l’adresse IP publique. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Joint un réseau virtuel. Impossible à alerter. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Crée une intention de protection de sauvegarde. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Renvoie des détails d’objet de l’élément protégé. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Créer un élément protégé de sauvegarde. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Renvoie toutes les stratégies de protection. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/write | Crée une stratégie de protection. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | L’opération de création de coffre entraîne la création d’une ressource Azure de type « coffre ». |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | Microsoft.SerialConsole/serialPorts/connect/action | Se connecter à un port série |
> | [Microsoft.SqlVirtualMachine](resource-provider-operations.md#microsoftsqlvirtualmachine)/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/cloudServices/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SerialConsole/serialPorts/connect/action",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Connexion de l’utilisateur aux machines virtuelles

Affichez les machines virtuelles dans le portail et connectez-vous en tant qu’utilisateur normal. [En savoir plus](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Obtient une définition de l’adresse IP publique. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/read | Obtient une définition d’équilibrage de charge. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Obtient une définition d’interface réseau.  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/*/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/login/action | Se connecter à la machine virtuelle comme utilisateur normal |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/login/action | Se connecte à la machine Azure Arc comme utilisateur normal |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.HybridCompute/machines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.HybridCompute/machines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Mise en réseau


### <a name="cdn-endpoint-contributor"></a>Contributeur de point de terminaison CDN

Peut gérer les points de terminaison CDN, mais ne peut pas accorder l’accès à d’autres utilisateurs.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>Lecteur de point de terminaison CDN

Peut afficher des points de terminaison CDN, mais ne peut pas effectuer de modifications.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/endpoints/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>Contributeur de profil CDN

Peut gérer des profils CDN et leurs points de terminaison, mais ne peut pas accorder l’accès à d’autres utilisateurs. [En savoir plus](../cdn/cdn-app-dev-net.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>Lecteur de profil CDN

Peut afficher des profils CDN et leurs points de terminaison, mais ne peut pas y apporter des modifications.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/edgenodes/read |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/operationresults/* |  |
> | [Microsoft.Cdn](resource-provider-operations.md#microsoftcdn)/profiles/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Contributeur de réseau classique

Permet de gérer des réseaux classiques, mais pas d’y accéder. [En savoir plus](../virtual-network/virtual-network-manage-peering.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/* | Créer et gérer des réseaux classiques |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>Contributeur de Zone DNS

Permet de gérer des zones DNS et des jeux d’enregistrements dans Azure DNS, mais pas de contrôler qui y a accès. [En savoir plus](../dns/dns-protect-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/dnsZones/* | Créer et gérer des enregistrements et zones DNS |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Contributeur de réseau

Permet de gérer des réseaux, mais pas d’y accéder.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/* | Créer et gérer des réseaux |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="private-dns-zone-contributor"></a>Private DNS Zone Contributor

Permet de gérer les ressources de zone DNS privée, mais pas les réseaux virtuels auxquels elles sont liées. [En savoir plus](../dns/dns-protect-private-zones-recordsets.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsZones/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationResults/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/privateDnsOperationStatuses/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/join/action | Joint un réseau virtuel. Impossible à alerter. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage private DNS zone resources, but not the virtual networks they are linked to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "name": "b12aa53e-6015-4669-85d0-8515ebb3ae7f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/privateDnsZones/*",
        "Microsoft.Network/privateDnsOperationResults/*",
        "Microsoft.Network/privateDnsOperationStatuses/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/join/action",
        "Microsoft.Authorization/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Private DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Contributeur Traffic Manager

Permet de gérer des profils Traffic Manager, mais pas de contrôler qui y a accès.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/trafficManagerProfiles/* |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Stockage


### <a name="avere-contributor"></a>Contributeur Avere

Peut créer et gérer un cluster Avere vFXT. [En savoir plus](../avere-vfxt/avere-vfxt-deploy-plan.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/*/read |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/proximityPlacementGroups/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/* |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/*/read |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | Obtient une définition de sous-réseau de réseau virtuel. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Joint un réseau virtuel. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Joint un groupe de sécurité réseau. Impossible à alerter. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/*/read |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Créer et gérer les comptes de stockage |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Obtient les ressources du groupe de ressources. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Retourner le résultat de la suppression d’un objet blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Retourne un objet blob ou une liste d'objets blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Retourner le résultat de l’écriture d’un objet blob |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/proximityPlacementGroups/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Opérateur Avere

Utilisé par le cluster Avere vFXT pour gérer le cluster [En savoir plus](../avere-vfxt/avere-vfxt-manage-cluster.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Obtenir les propriétés d’une machine virtuelle |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Obtient une définition d’interface réseau.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Crée une interface réseau ou met à jour une interface réseau existante.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/read | Obtient une définition de sous-réseau de réseau virtuel. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Joint un réseau virtuel. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/join/action | Joint un groupe de sécurité réseau. Impossible à alerter. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Retourne le résultat de la suppression d’un conteneur |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Retourne la liste des conteneurs |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Retourne le résultat du conteneur put blob |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Retourner le résultat de la suppression d’un objet blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Retourne un objet blob ou une liste d'objets blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Retourner le résultat de l’écriture d’un objet blob |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Contributeur de sauvegarde

Permet de gérer le service de sauvegarde, mais pas de créer des coffres, ni d’accorder l’accès à d’autres personnes [En savoir plus](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/* | Gérer les résultats des opérations de gestion des sauvegardes |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/* | Créer et gérer des conteneurs de sauvegarde dans les structures de sauvegarde du coffre Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Actualise la liste de conteneurs. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Créer et gérer des travaux de sauvegarde |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Travaux d’exportation |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Créer et gérer les résultats des opérations de gestion des sauvegardes |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/* | Créer et gérer des stratégies de sauvegarde |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Créer et gérer les éléments qui peuvent être sauvegardés |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/* | Créer et gérer les éléments sauvegardés |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/* | Créer et gérer les conteneurs contenant les éléments de sauvegarde |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupSecurityPIN/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/* | Créer et gérer des certificats associés à la sauvegarde dans le coffre Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Créer et gérer des informations étendues associées au coffre |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Créer et gérer les identités inscrites |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/* | Créer et gérer l’utilisation du coffre Recovery Services |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Valider l’opération sur l’élément protégé. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/write | L’opération de création de coffre entraîne la création d’une ressource Azure de type « coffre ». |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Renvoie l’état de l’opération de sauvegarde pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Retourne tous les serveurs d’administration de sauvegarde inscrits auprès du coffre. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Obtient tous les conteneurs protégeables |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Vérifie l’état de la sauvegarde pour les coffres Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Valide des fonctionnalités |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Résout l’alerte. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Retourne la liste d’opérations pour un fournisseur de ressources |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Obtient l’état de l’opération pour une opération donnée. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Répertorier tous les intentions de protection de sauvegarde |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/getBackupStatus/action | Vérifie l’état de la sauvegarde pour les coffres Recovery Services |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/write | Crée une instance de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/delete | Supprime l’instance de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/read | Retourne toutes les instances de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/read | Retourne toutes les instances de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/backup/action | Effectue une sauvegarde sur l’instance de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/validateRestore/action | Valide la restauration de l’instance de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/restore/action | Déclenche la restauration sur l’instance de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/write | Crée une stratégie de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/delete | Supprime la stratégie de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/read | Retourne toutes les stratégies de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/read | Retourne toutes les stratégies de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/recoveryPoints/read | Retourne tous les points de récupération |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/recoveryPoints/read | Retourne tous les points de récupération |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/findRestorableTimeRanges/action | Recherche des intervalles de temps pouvant être restaurés |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/write | L’opération Create BackupVault crée une ressource Azure de type « coffre de sauvegarde » |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | Obtient la liste des coffres de sauvegarde dans un abonnement |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/operationResults/read | Obtient le résultat d’une opération de patch pour un coffre de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/checkNameAvailability/action | Vérifie si le nom de coffre de sauvegarde demandé est disponible |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | Obtient la liste des coffres de sauvegarde dans un abonnement |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | Obtient la liste des coffres de sauvegarde dans un abonnement |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/operationStatus/read | Retourne l’état de l’opération de sauvegarde pour le coffre de sauvegarde. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/operationResults/read | Retourne le résultat de l’opération de sauvegarde pour le coffre de sauvegarde. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/validateForBackup/action | Valide la sauvegarde de l’instance de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/providers/operations/read | Retourne la liste d’opérations pour un fournisseur de ressources |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*",
        "Microsoft.DataProtection/locations/getBackupStatus/action",
        "Microsoft.DataProtection/backupVaults/backupInstances/write",
        "Microsoft.DataProtection/backupVaults/backupInstances/delete",
        "Microsoft.DataProtection/backupVaults/backupInstances/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/backup/action",
        "Microsoft.DataProtection/backupVaults/backupInstances/validateRestore/action",
        "Microsoft.DataProtection/backupVaults/backupInstances/restore/action",
        "Microsoft.DataProtection/backupVaults/backupPolicies/write",
        "Microsoft.DataProtection/backupVaults/backupPolicies/delete",
        "Microsoft.DataProtection/backupVaults/backupPolicies/read",
        "Microsoft.DataProtection/backupVaults/backupPolicies/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges/action",
        "Microsoft.DataProtection/backupVaults/write",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/backupVaults/operationResults/read",
        "Microsoft.DataProtection/locations/checkNameAvailability/action",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/locations/operationStatus/read",
        "Microsoft.DataProtection/locations/operationResults/read",
        "Microsoft.DataProtection/backupVaults/validateForBackup/action",
        "Microsoft.DataProtection/providers/operations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Opérateur de sauvegarde

Permet de gérer des services de sauvegarde, à l’exception de la suppression de la sauvegarde, de la création de coffres et de l’octroi d’autorisations d’accès à d’autres personnes [En savoir plus](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Renvoie l’état de l’opération. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtient les résultats de l’opération effectuée sur le conteneur de protection. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Effectue la sauvegarde d’un élément protégé. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtient les résultats de l’opération effectuée sur les éléments protégés. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Renvoie l’état de l’opération effectuée sur les éléments protégés. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Renvoie des détails d’objet de l’élément protégé. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Approvisionner la récupération d’éléments instantanée pour l’élément protégé. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action | Obtenir AccessToken pour la restauration interrégionale. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtenir les points de récupération des éléments protégés. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restaurer les points de récupération des éléments protégés. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Révoquer la récupération d’éléments instantanée pour l’élément protégé. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/write | Créer un élément protégé de sauvegarde. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Renvoie tous les conteneurs inscrits. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/refreshContainers/action | Actualise la liste de conteneurs. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/* | Créer et gérer des travaux de sauvegarde |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Travaux d’exportation |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/* | Créer et gérer les résultats des opérations de gestion des sauvegardes |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Obtenir les résultats de l’opération de stratégie. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Renvoie toutes les stratégies de protection. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectableItems/* | Créer et gérer les éléments qui peuvent être sauvegardés |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Renvoie la liste de tous les éléments protégés. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Renvoie tous les conteneurs appartenant à l’abonnement. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | L’opération de mise à jour de certificat de ressource met à jour le certificat d’identification du coffre/de la ressource. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/write | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/write | L’opération d’inscription d’un conteneur de service peut être utilisée pour inscrire un conteneur avec Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupValidateOperation/action | Valider l’opération sur l’élément protégé. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Renvoie l’état de l’opération de sauvegarde pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | Obtenir l’état de l’opération de stratégie. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/write | Crée un conteneur inscrit |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/inquire/action | Recherche les charges de travail dans un conteneur |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Retourne tous les serveurs d’administration de sauvegarde inscrits auprès du coffre. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/write | Crée une intention de protection de sauvegarde. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Créer une intention de protection de sauvegarde |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectableContainers/read | Obtient tous les conteneurs protégeables |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Obtient tous les éléments figurant dans un conteneur |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Vérifie l’état de la sauvegarde pour les coffres Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupPreValidateProtection/action |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Valide des fonctionnalités |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupAadProperties/read | Obtenir les propriétés AAD d’authentification dans la troisième région pour la restauration interrégionale. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJobs/action | Répertorier les travaux de restauration interrégionale dans la région secondaire pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJob/action | Obtenir les détails du travail de restauration interrégionale dans la région secondaire pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrossRegionRestore/action | Déclencher la restauration interrégion. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationResults/read | Retourne le résultat de l’opération de restauration interrégionale du coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationsStatus/read | Retourne l’état de l’opération de restauration interrégionale du coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Résout l’alerte. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Retourne la liste d’opérations pour un fournisseur de ressources |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Obtient l’état de l’opération pour une opération donnée. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Répertorier tous les intentions de protection de sauvegarde |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/read | Retourne toutes les instances de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/read | Retourne toutes les instances de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/read | Retourne toutes les stratégies de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/read | Retourne toutes les stratégies de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/recoveryPoints/read | Retourne tous les points de récupération |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/recoveryPoints/read | Retourne tous les points de récupération |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/findRestorableTimeRanges/action | Recherche des intervalles de temps pouvant être restaurés |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | Obtient la liste des coffres de sauvegarde dans un abonnement |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/operationResults/read | Obtient le résultat d’une opération de patch pour un coffre de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | Obtient la liste des coffres de sauvegarde dans un abonnement |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | Obtient la liste des coffres de sauvegarde dans un abonnement |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/operationStatus/read | Retourne l’état de l’opération de sauvegarde pour le coffre de sauvegarde. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/operationResults/read | Retourne le résultat de l’opération de sauvegarde pour le coffre de sauvegarde. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/providers/operations/read | Retourne la liste d’opérations pour un fournisseur de ressources |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/accessToken/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/locations/backupAadProperties/read",
        "Microsoft.RecoveryServices/locations/backupCrrJobs/action",
        "Microsoft.RecoveryServices/locations/backupCrrJob/action",
        "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action",
        "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read",
        "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*",
        "Microsoft.DataProtection/backupVaults/backupInstances/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/read",
        "Microsoft.DataProtection/backupVaults/backupPolicies/read",
        "Microsoft.DataProtection/backupVaults/backupPolicies/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges/action",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/backupVaults/operationResults/read",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/locations/operationStatus/read",
        "Microsoft.DataProtection/locations/operationResults/read",
        "Microsoft.DataProtection/providers/operations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Lecteur de sauvegarde

Peut afficher des services de sauvegarde, mais pas apporter des modifications [En savoir plus](../backup/backup-rbac-rs-vault.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/operationResults/read | Renvoie l’état de l’opération. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/operationResults/read | Obtient les résultats de l’opération effectuée sur le conteneur de protection. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Obtient les résultats de l’opération effectuée sur les éléments protégés. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Renvoie l’état de l’opération effectuée sur les éléments protégés. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/read | Renvoie des détails d’objet de l’élément protégé. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Obtenir les points de récupération des éléments protégés. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/read | Renvoie tous les conteneurs inscrits. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/operationResults/read | Renvoie le résultat de l’opération de travail. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobs/read | Renvoie tous les objets de travail. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupJobsExport/action | Travaux d’exportation |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperationResults/read | Renvoie le résultat de l’opération de sauvegarde pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operationResults/read | Obtenir les résultats de l’opération de stratégie. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/read | Renvoie toutes les stratégies de protection. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectedItems/read | Renvoie la liste de tous les éléments protégés. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionContainers/read | Renvoie tous les conteneurs appartenant à l’abonnement. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupUsageSummaries/read | Renvoie des résumés pour les éléments protégés et les serveurs protégés d’un coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupstorageconfig/read | Renvoie la configuration de stockage pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupconfig/read | Renvoie la configuration pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupOperations/read | Renvoie l’état de l’opération de sauvegarde pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupPolicies/operations/read | Obtenir l’état de l’opération de stratégie. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupEngines/read | Retourne tous les serveurs d’administration de sauvegarde inscrits auprès du coffre. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/backupProtectionIntent/read | Créer une intention de protection de sauvegarde |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupFabrics/protectionContainers/items/read | Obtient tous les éléments figurant dans un conteneur |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupStatus/action | Vérifie l’état de la sauvegarde pour les coffres Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/write | Résout l’alerte. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/operations/read | Retourne la liste d’opérations pour un fournisseur de ressources |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/operationStatus/read | Obtient l’état de l’opération pour une opération donnée. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/backupProtectionIntents/read | Répertorier tous les intentions de protection de sauvegarde |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupValidateFeatures/action | Valide des fonctionnalités |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJobs/action | Répertorier les travaux de restauration interrégionale dans la région secondaire pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrJob/action | Obtenir les détails du travail de restauration interrégionale dans la région secondaire pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationResults/read | Retourne le résultat de l’opération de restauration interrégionale du coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/backupCrrOperationsStatus/read | Retourne l’état de l’opération de restauration interrégionale du coffre Recovery Services. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/getBackupStatus/action | Vérifie l’état de la sauvegarde pour les coffres Recovery Services |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/write | Crée une instance de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/read | Retourne toutes les instances de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/read | Retourne toutes les instances de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/backup/action | Effectue une sauvegarde sur l’instance de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/validateRestore/action | Valide la restauration de l’instance de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/restore/action | Déclenche la restauration sur l’instance de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/read | Retourne toutes les stratégies de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupPolicies/read | Retourne toutes les stratégies de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/recoveryPoints/read | Retourne tous les points de récupération |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/recoveryPoints/read | Retourne tous les points de récupération |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/backupInstances/findRestorableTimeRanges/action | Recherche des intervalles de temps pouvant être restaurés |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | Obtient la liste des coffres de sauvegarde dans un abonnement |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/operationResults/read | Obtient le résultat d’une opération de patch pour un coffre de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | Obtient la liste des coffres de sauvegarde dans un abonnement |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/read | Obtient la liste des coffres de sauvegarde dans un abonnement |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/operationStatus/read | Retourne l’état de l’opération de sauvegarde pour le coffre de sauvegarde. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/locations/operationResults/read | Retourne le résultat de l’opération de sauvegarde pour le coffre de sauvegarde. |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/backupVaults/validateForBackup/action | Valide la sauvegarde de l’instance de sauvegarde |
> | [Microsoft.DataProtection](resource-provider-operations.md#microsoftdataprotection)/providers/operations/read | Retourne la liste d’opérations pour un fournisseur de ressources |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/locations/backupCrrJobs/action",
        "Microsoft.RecoveryServices/locations/backupCrrJob/action",
        "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read",
        "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read",
        "Microsoft.DataProtection/locations/getBackupStatus/action",
        "Microsoft.DataProtection/backupVaults/backupInstances/write",
        "Microsoft.DataProtection/backupVaults/backupInstances/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/backup/action",
        "Microsoft.DataProtection/backupVaults/backupInstances/validateRestore/action",
        "Microsoft.DataProtection/backupVaults/backupInstances/restore/action",
        "Microsoft.DataProtection/backupVaults/backupPolicies/read",
        "Microsoft.DataProtection/backupVaults/backupPolicies/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints/read",
        "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges/action",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/backupVaults/operationResults/read",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/backupVaults/read",
        "Microsoft.DataProtection/locations/operationStatus/read",
        "Microsoft.DataProtection/locations/operationResults/read",
        "Microsoft.DataProtection/backupVaults/validateForBackup/action",
        "Microsoft.DataProtection/providers/operations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Contributeur de compte de stockage classique

Permet de gérer des comptes de stockage classiques, mais pas d’y accéder.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/* | Créer et gérer les comptes de stockage |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Rôle de service d’opérateur de clé de compte de stockage classique

Les opérateurs de clés de comptes de stockage classiques sont autorisés à lister et à regénérer des clés sur des comptes de stockage classiques [En savoir plus](../key-vault/secrets/overview-storage-keys.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listkeys/action | Répertorie les clés d’accès des comptes de stockage. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/regeneratekey/action | Régénère les clés d’accès existantes du compte de stockage. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Contributeur Data Box

Permet de gérer toutes les opérations sous le service Data Box à l’exception de l’octroi d’accès à d’autres personnes. [En savoir plus](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Lecteur Data Box

Permet de gérer le service Data Box, mais ne permet pas de créer une commande, de modifier les détails d’une commande ou d’octroyer l’accès à d’autres personnes. [En savoir plus](../databox/data-box-logs.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/*/read |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listsecrets/action |  |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/jobs/listcredentials/action | Répertorie les informations d’identification non chiffrées liées à la commande |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/availableSkus/action | Retourner la liste des références (SKU) disponibles |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateInputs/action | Cette méthode effectue tous les types de validations. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/regionConfiguration/action | Cette méthode retourne les configurations pour la région. |
> | [Microsoft.Databox](resource-provider-operations.md#microsoftdatabox)/locations/validateAddress/action | Valider l'adresse de livraison et fournir d'autres adresses s’il en est |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Développeur Data Lake Analytics

Permet d’envoyer, de surveiller et de gérer vos propres travaux, mais pas de créer ni de supprimer des comptes Data Lake Analytics. [En savoir plus](../data-lake-analytics/data-lake-analytics-manage-use-portal.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.BigAnalytics/accounts/* |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Delete | Supprime un compte Data Lake Analytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/TakeOwnership/action | Accorde des autorisations pour annuler des travaux soumis par d’autres utilisateurs. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/Write | Crée ou met à jour un compte Data Lake Analytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Write | Crée ou met à jour un compte Data Lake Store lié d’un compte Data Lake Analytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/dataLakeStoreAccounts/Delete | Dissocie un compte Data Lake Store d’un compte Data Lake Analytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Write | Crée ou met à jour un compte de stockage lié d’un compte Data Lake Analytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/storageAccounts/Delete | Dissocie un compte de stockage d’un compte Data Lake Analytics. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Write | Créer ou mettre à jour une règle de pare-feu. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/firewallRules/Delete | Supprimer une règle de pare-feu. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Write | Crée ou met à jour une stratégie de calcul. |
> | [Microsoft.DataLakeAnalytics](resource-provider-operations.md#microsoftdatalakeanalytics)/accounts/computePolicies/Delete | Supprime une stratégie de calcul. |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Lecteur et accès aux données

Permet d’afficher tous les éléments, mais pas de supprimer ou de créer un compte de stockage ou une ressource contenue. En outre, autorise l’accès en lecture/écriture à toutes les données contenues dans un compte de stockage via l’accès aux clés de compte de stockage.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/ListAccountSas/action | Retourne le jeton SAS du compte de stockage spécifié. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Contributeur de compte de stockage

Permet la gestion des comptes de stockage. Fournit l’accès à la clé de compte, qui peut être utilisée pour accéder aux données par le biais de l’autorisation de clé partagée. [En savoir plus](../storage/common/storage-auth-aad.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Crée, met à jour ou lit le paramètre de diagnostic pour Analysis Server. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. Impossible à alerter. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/* | Créer et gérer les comptes de stockage |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Rôle de service d’opérateur de clé de compte de stockage

Permet de répertorier et de régénérer les clés d’accès au compte de stockage. [En savoir plus](../storage/common/storage-account-keys-manage.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/regeneratekey/action | Régénère les clés d’accès au compte de stockage spécifié. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Contributeur aux données Blob du stockage

Lire, écrire et supprimer des conteneurs et objets blob du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [En savoir plus](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/delete | Supprimer un conteneur. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Retourner un conteneur ou une liste de conteneurs. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/write | Modifier les métadonnées ou les propriétés d’un conteneur. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Retourne une clé de délégation d’utilisateur pour le service Blob. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/delete | Supprimer un objet blob. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Retourner un objet blob ou une liste d'objets blob. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/write | Écrire dans un objet blob. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/move/action | Déplace l'objet blob d'un chemin à un autre |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/add/action | Retourner le résultat de l’ajout de contenu d’objet blob |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Propriétaire des données Blob du stockage

Fournit un accès total aux conteneurs d’objets blob et aux données du Stockage Azure, notamment l’attribution du contrôle d’accès POSIX. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [En savoir plus](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/* | Toutes les autorisations sur les conteneurs. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Retourne une clé de délégation d’utilisateur pour le service Blob. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/* | Toutes les autorisations sur les objets blob. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Lecteur des données blob du stockage

Lire et répertorier des conteneurs et objets blob du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [En savoir plus](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/read | Retourner un conteneur ou une liste de conteneurs. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Retourne une clé de délégation d’utilisateur pour le service Blob. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/containers/blobs/read | Retourner un objet blob ou une liste d'objets blob. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Délégation du Stockage Blob

Obtenez une clé de délégation d’utilisateur qui peut être utilisée pour créer une signature d’accès partagé pour un conteneur ou un objet blob signé avec les informations d’identification Azure AD. Pour en savoir plus, consultez [Créer une SAP de délégation d’utilisateur](/rest/api/storageservices/create-user-delegation-sas). [En savoir plus](/rest/api/storageservices/get-user-delegation-key)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/blobServices/generateUserDelegationKey/action | Retourne une clé de délégation d’utilisateur pour le service Blob. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>Contributeur de partage SMB de données de fichier de stockage

Permet l'accès en lecture, en écriture et en suppression aux fichiers/répertoires des partages de fichiers Azure. Ce rôle n'a pas d'équivalent intégré sur les serveurs de fichiers Windows. [En savoir plus](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Retourne un fichier/dossier ou une liste de fichiers/dossiers. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Retourne le résultat de l’écriture d’un fichier ou de la création d’un dossier. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Retourne le résultat de la suppression d’un fichier/dossier. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Contributeur élevé de partage SMB de données de fichier de stockage

Permet la lecture, l'écriture, la suppression et la modification des listes de contrôle d'accès sur les fichiers/répertoires des partages de fichiers Azure. Ce rôle équivaut à une liste de contrôle d'accès de partage de fichiers en modification sur les serveurs de fichiers Windows. [En savoir plus](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Retourne un fichier/dossier ou une liste de fichiers/dossiers. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/write | Retourne le résultat de l’écriture d’un fichier ou de la création d’un dossier. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/delete | Retourne le résultat de la suppression d’un fichier/dossier. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Retourne le résultat de la modification de l’autorisation sur un fichier/dossier. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>Lecteur de partage SMB de données de fichier de stockage

Permet l'accès en lecture aux fichiers/répertoires des partages de fichiers Azure. Ce rôle équivaut à une liste de contrôle d'accès de partage de fichiers en lecture sur les serveurs de fichiers Windows. [En savoir plus](../storage/files/storage-files-identity-auth-active-directory-enable.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/fileServices/fileshares/files/read | Retourne un fichier/dossier ou une liste de fichiers/dossiers. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Contributeur aux données en file d’attente du stockage

Lire, écrire et supprimer des files d'attente et messages en file d'attente du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [En savoir plus](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/delete | Supprimer une file d’attente. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Retourner une file d’attente ou une liste de files d’attente. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/write | Modifier les métadonnées ou propriétés en file d’attente. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/delete | Supprimer un ou plusieurs messages à partir d’une file d’attente. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Récupérer un ou plusieurs messages à partir d’une file d’attente, ou en afficher un aperçu. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/write | Ajouter un message à une file d'attente. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | Retourner le résultat du traitement d’un message |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Processeur de messages de données en file d’attente du stockage

Récupérer et supprimer un message, ou en afficher un aperçu à partir d’une file d’attente Stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [En savoir plus](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Afficher l’aperçu d’un message. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/process/action | Récupérer et supprimer un message. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Expéditeur de messages de données en file d’attente du stockage

Ajoutez des messages à une file d’attente de stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [En savoir plus](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/add/action | Ajouter un message à une file d'attente. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Lecteur des données en file d’attente du stockage

Lire et répertorier des files d’attente et messages en file d’attente du stockage Azure. Pour savoir quelles actions sont requises pour une opération de données spécifique, consultez [Autorisations pour appeler les opérations de données d’objet blob et de file d’attente](/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). [En savoir plus](../storage/common/storage-auth-aad-rbac-portal.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/read | Retourne une file d’attente ou une liste de files d’attente. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/queueServices/queues/messages/read | Récupérer un ou plusieurs messages à partir d’une file d’attente, ou en afficher un aperçu. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-table-data-contributor"></a>Contributeur aux données de table du stockage

Permet l’accès en lecture, en écriture et en suppression aux tables et entités du stockage Azure

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/read | Tables de requête |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/write | Créer des tables |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/delete | Supprime des tables |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/entities/read | Interroge les entités de table |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/entities/write | Insère, fusionne ou remplace des entités de table |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/entities/delete | Suppression d’entités de table |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/entities/add/action | Insère des entités de table |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/entities/update/action | Fusionne ou met à jour des entités de table |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage tables and entities",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0a9a7e1f-b9d0-4cc4-a60d-0319b160aaa3",
  "name": "0a9a7e1f-b9d0-4cc4-a60d-0319b160aaa3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/tableServices/tables/read",
        "Microsoft.Storage/storageAccounts/tableServices/tables/write",
        "Microsoft.Storage/storageAccounts/tableServices/tables/delete"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/tableServices/tables/entities/read",
        "Microsoft.Storage/storageAccounts/tableServices/tables/entities/write",
        "Microsoft.Storage/storageAccounts/tableServices/tables/entities/delete",
        "Microsoft.Storage/storageAccounts/tableServices/tables/entities/add/action",
        "Microsoft.Storage/storageAccounts/tableServices/tables/entities/update/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Table Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-table-data-reader"></a>Lecteur de données de table du stockage

Permet l’accès en lecture aux tables et entités du stockage Azure

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/read | Tables de requête |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/tableServices/tables/entities/read | Interroge les entités de table |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage tables and entities",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76199698-9eea-4c19-bc75-cec21354c6b6",
  "name": "76199698-9eea-4c19-bc75-cec21354c6b6",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/tableServices/tables/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/tableServices/tables/entities/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Table Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Web


### <a name="azure-maps-data-contributor"></a>Contributeur aux données Azure Maps

Accorde l’accès en lecture, en écriture et en suppression aux données liées aux cartes depuis un compte Azure Maps. [En savoir plus](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/write |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/delete |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/action |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read, write, and delete access to map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "name": "8f5e0ce6-4f7b-4dcf-bddf-e6f48634a204",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read",
        "Microsoft.Maps/accounts/*/write",
        "Microsoft.Maps/accounts/*/delete",
        "Microsoft.Maps/accounts/*/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-maps-data-reader"></a>Lecteur de données Azure Maps

Octroie un accès pour lire les données liées au mappage à partir d’un compte Azure Maps. [En savoir plus](../azure-maps/azure-maps-authentication.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Maps](resource-provider-operations.md#microsoftmaps)/accounts/*/read |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-spring-cloud-config-server-contributor"></a>Contributeur au serveur Azure Spring Cloud Config

Autoriser l'accès en lecture, écriture et suppression au serveur Azure Spring Cloud Config [En savoir plus](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/configService/read | Lit le contenu de configuration (par exemple, application.yaml) d'une instance de service Azure Spring Cloud spécifique |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/configService/write | Écrit le contenu du serveur de configuration d'une instance de service Azure Spring Cloud spécifique |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/configService/delete | Supprime le contenu du serveur de configuration d'une instance de service Azure Spring Cloud spécifique |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read, write and delete access to Azure Spring Cloud Config Server",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a06f5c24-21a7-4e1a-aa2b-f19eb6684f5b",
  "name": "a06f5c24-21a7-4e1a-aa2b-f19eb6684f5b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/configService/read",
        "Microsoft.AppPlatform/Spring/configService/write",
        "Microsoft.AppPlatform/Spring/configService/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Config Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-spring-cloud-config-server-reader"></a>Lecteur de serveur Azure Spring Cloud Config

Autoriser l'accès en lecture au serveur Azure Spring Cloud Config [En savoir plus](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/configService/read | Lit le contenu de configuration (par exemple, application.yaml) d'une instance de service Azure Spring Cloud spécifique |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read access to Azure Spring Cloud Config Server",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d04c6db6-4947-4782-9e91-30a88feb7be7",
  "name": "d04c6db6-4947-4782-9e91-30a88feb7be7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/configService/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Config Server Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-spring-cloud-data-reader"></a>Lecteur de données Azure Spring Cloud

Autoriser l'accès en lecture aux données Azure Spring Cloud

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/*/read |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read access to Azure Spring Cloud Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b5537268-8956-4941-a8f0-646150406f0c",
  "name": "b5537268-8956-4941-a8f0-646150406f0c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-spring-cloud-service-registry-contributor"></a>Contributeur au registre du service Azure Spring Cloud

Autoriser l'accès en lecture, écriture et suppression au registre du service Azure Spring Cloud [En savoir plus](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/eurekaService/read | Lit les informations d'inscription des applications utilisateur d'une instance de service Azure Spring Cloud spécifique |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/eurekaService/write | Écrit les informations d'inscription des applications utilisateur d'une instance de service Azure Spring Cloud spécifique |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/eurekaService/delete | Supprime les informations d'inscription des applications utilisateur d'une instance de service Azure Spring Cloud spécifique |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read, write and delete access to Azure Spring Cloud Service Registry",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f5880b48-c26d-48be-b172-7927bfa1c8f1",
  "name": "f5880b48-c26d-48be-b172-7927bfa1c8f1",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/eurekaService/read",
        "Microsoft.AppPlatform/Spring/eurekaService/write",
        "Microsoft.AppPlatform/Spring/eurekaService/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Service Registry Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-spring-cloud-service-registry-reader"></a>Lecteur de registre du service Azure Spring Cloud

Autoriser l'accès en lecture au registre du service Azure Spring Cloud [En savoir pus](../spring-cloud/how-to-access-data-plane-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.AppPlatform](resource-provider-operations.md#microsoftappplatform)/Spring/eurekaService/read | Lit les informations d'inscription des applications utilisateur d'une instance de service Azure Spring Cloud spécifique |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allow read access to Azure Spring Cloud Service Registry",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cff1b556-2399-4e7e-856d-a8f754be7b65",
  "name": "cff1b556-2399-4e7e-856d-a8f754be7b65",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppPlatform/Spring/eurekaService/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Spring Cloud Service Registry Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="media-services-account-administrator"></a>Administrateur de compte Media Services

Créer, lire, modifier et supprimer des comptes Media Services ; accès en lecture seule à d’autres ressources Media Services.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Lire des mesures |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Lire les définitions des mesures |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/*/read |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/listStreamingLocators/action | Lister les localisateurs de streaming pour l’actif multimédia |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/listPaths/action | Répertorie les chemins d’accès |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/write | Crée ou met à jour un compte Media Services |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/delete | Supprime un compte Media Services |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/privateEndpointConnectionsApproval/action | Approuve les connexions de point de terminaison privé |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/privateEndpointConnections/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, read, modify, and delete Media Services accounts; read-only access to other Media Services resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/054126f8-9a2b-4f1c-a9ad-eca461f08466",
  "name": "054126f8-9a2b-4f1c-a9ad-eca461f08466",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Media/mediaservices/*/read",
        "Microsoft.Media/mediaservices/assets/listStreamingLocators/action",
        "Microsoft.Media/mediaservices/streamingLocators/listPaths/action",
        "Microsoft.Media/mediaservices/write",
        "Microsoft.Media/mediaservices/delete",
        "Microsoft.Media/mediaservices/privateEndpointConnectionsApproval/action",
        "Microsoft.Media/mediaservices/privateEndpointConnections/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Media Services Account Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="media-services-live-events-administrator"></a>Administrateur d’événement en direct Media Services

Créer, lire, modifier et supprimer des événements en direct, des ressources, des filtres de ressources et des localisateurs de streaming ; accès en lecture seule à d’autres ressources Media Services.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Lire des mesures |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Lire les définitions des mesures |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/*/read |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/assetfilters/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/liveEvents/* |  |
> | **NotActions** |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/getEncryptionKey/action | Obtient la clé de chiffrement de la ressource |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/listContentKeys/action | Répertorie les clés de contenu |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, read, modify, and delete Live Events, Assets, Asset Filters, and Streaming Locators; read-only access to other Media Services resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/532bc159-b25e-42c0-969e-a1d439f60d77",
  "name": "532bc159-b25e-42c0-969e-a1d439f60d77",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Media/mediaservices/*/read",
        "Microsoft.Media/mediaservices/assets/*",
        "Microsoft.Media/mediaservices/assets/assetfilters/*",
        "Microsoft.Media/mediaservices/streamingLocators/*",
        "Microsoft.Media/mediaservices/liveEvents/*"
      ],
      "notActions": [
        "Microsoft.Media/mediaservices/assets/getEncryptionKey/action",
        "Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Media Services Live Events Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="media-services-media-operator"></a>Opérateur de média Media Services

Créer, lire, modifier et supprimer des ressources, des filtres de ressources, des localisateurs de streaming et des tâches ; accès en lecture seule à d’autres ressources Media Services.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Lire des mesures |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Lire les définitions des mesures |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/*/read |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/assetfilters/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/transforms/jobs/* |  |
> | **NotActions** |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/getEncryptionKey/action | Obtient la clé de chiffrement de la ressource |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/listContentKeys/action | Répertorie les clés de contenu |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, read, modify, and delete Assets, Asset Filters, Streaming Locators, and Jobs; read-only access to other Media Services resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e4395492-1534-4db2-bedf-88c14621589c",
  "name": "e4395492-1534-4db2-bedf-88c14621589c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Media/mediaservices/*/read",
        "Microsoft.Media/mediaservices/assets/*",
        "Microsoft.Media/mediaservices/assets/assetfilters/*",
        "Microsoft.Media/mediaservices/streamingLocators/*",
        "Microsoft.Media/mediaservices/transforms/jobs/*"
      ],
      "notActions": [
        "Microsoft.Media/mediaservices/assets/getEncryptionKey/action",
        "Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Media Services Media Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="media-services-policy-administrator"></a>Administrateur de stratégie Media Services

Créer, lire, modifier et supprimer des filtres de compte, des stratégies de streaming, des stratégies de clés de contenu et des transformations ; accès en lecture seule à d’autres ressources Media Services. Impossible de créer des tâches, des ressources ou des ressources de streaming.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Lire des mesures |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Lire les définitions des mesures |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/*/read |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/listStreamingLocators/action | Lister les localisateurs de streaming pour l’actif multimédia |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/listPaths/action | Répertorie les chemins d’accès |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/accountFilters/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingPolicies/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/contentKeyPolicies/* |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/transforms/* |  |
> | **NotActions** |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Obtient les propriétés de stratégie avec les secrets |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, read, modify, and delete Account Filters, Streaming Policies, Content Key Policies, and Transforms; read-only access to other Media Services resources. Cannot create Jobs, Assets or Streaming resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c4bba371-dacd-4a26-b320-7250bca963ae",
  "name": "c4bba371-dacd-4a26-b320-7250bca963ae",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Media/mediaservices/*/read",
        "Microsoft.Media/mediaservices/assets/listStreamingLocators/action",
        "Microsoft.Media/mediaservices/streamingLocators/listPaths/action",
        "Microsoft.Media/mediaservices/accountFilters/*",
        "Microsoft.Media/mediaservices/streamingPolicies/*",
        "Microsoft.Media/mediaservices/contentKeyPolicies/*",
        "Microsoft.Media/mediaservices/transforms/*"
      ],
      "notActions": [
        "Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Media Services Policy Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="media-services-streaming-endpoints-administrator"></a>Administrateur de points de terminaison de streaming Media Services

Créer, lire, modifier et supprimer des points de terminaison de streaming ; accès en lecture seule à d’autres ressources Media Services.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Lire des mesures |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Lire les définitions des mesures |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/*/read |  |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/assets/listStreamingLocators/action | Lister les localisateurs de streaming pour l’actif multimédia |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingLocators/listPaths/action | Répertorie les chemins d’accès |
> | [Microsoft.Media](resource-provider-operations.md#microsoftmedia)/mediaservices/streamingEndpoints/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, read, modify, and delete Streaming Endpoints; read-only access to other Media Services resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/99dba123-b5fe-44d5-874c-ced7199a5804",
  "name": "99dba123-b5fe-44d5-874c-ced7199a5804",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Media/mediaservices/*/read",
        "Microsoft.Media/mediaservices/assets/listStreamingLocators/action",
        "Microsoft.Media/mediaservices/streamingLocators/listPaths/action",
        "Microsoft.Media/mediaservices/streamingEndpoints/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Media Services Streaming Endpoints Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-index-data-contributor"></a>Contributeur de données d’index de recherche

Octroie un accès complet aux données d’index de la Recherche cognitive Azure.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/indexes/documents/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants full access to Azure Cognitive Search index data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8ebe5a00-799e-43f5-93ac-243d3dce84a7",
  "name": "8ebe5a00-799e-43f5-93ac-243d3dce84a7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Search/searchServices/indexes/documents/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Search Index Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-index-data-reader"></a>Lecteur de données d’index de recherche

Octroie un accès en lecture aux données d’index de la Recherche cognitive Azure.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/indexes/documents/read | Lire des documents ou des termes de requête suggérés à partir d’un index. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants read access to Azure Cognitive Search index data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1407120a-92aa-4202-b7e9-c0e197c71c8f",
  "name": "1407120a-92aa-4202-b7e9-c0e197c71c8f",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Search/searchServices/indexes/documents/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Search Index Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Contributeur du service de recherche

Permet de gérer des services de recherche, mais pas d’y accéder. [En savoir plus](../search/search-security-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Search](resource-provider-operations.md#microsoftsearch)/searchServices/* | Créer et gérer les services de recherche |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-accesskey-reader"></a>Lecteur AccessKey SignalR

Lire les clés d’accès du service SignalR

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/*/read |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/listkeys/action | Afficher la valeur des clés d’accès SignalR dans le portail de gestion ou par le biais d’une API |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read SignalR Service Access Keys",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/04165923-9d83-45d5-8227-78b77b0a687e",
  "name": "04165923-9d83-45d5-8227-78b77b0a687e",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*/read",
        "Microsoft.SignalRService/SignalR/listkeys/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR AccessKey Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-app-server-preview"></a>Serveur d’applications SignalR (préversion)

Permet à votre serveur d’applications d’accéder au service SignalR avec les options d’authentification AAD.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | Générer une clé d’accès pour signer les jetons d’accès. Par défaut, cette clé expire sous 90 minutes. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/write | Démarrer une connexion au serveur. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets your app server access SignalR Service with AAD auth options.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/420fcaa2-552c-430f-98ca-3264be4806c7",
  "name": "420fcaa2-552c-430f-98ca-3264be4806c7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/serverConnection/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR App Server (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-rest-api-owner"></a>Propriétaire de l’API REST SignalR

Accès complet aux API REST du service Azure SignalR

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | Générer un jeton d’accès pour que le client se connecte à ASRS. Par défaut, ce jeton expire sous 5 minutes. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/send/action | Diffusez des messages à toutes les connexions clientes dans le hub. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/send/action | Diffusez le message au groupe. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Vérifiez l’existence du groupe ou l’existence de l’utilisateur dans le groupe. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/write | Rejoignez/Quittez le groupe. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/send/action | Envoyer des messages directement à une connexion cliente. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Vérifier l’existence de la connexion cliente. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/write | Fermez la connexion cliente. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/send/action | Envoyer des messages à l’utilisateur, qui peut se composer de plusieurs connexions clientes. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Vérifiez l’existence d’un utilisateur. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/write | Modifie un utilisateur. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd53cd77-2268-407a-8f46-7e7863d0f521",
  "name": "fd53cd77-2268-407a-8f46-7e7863d0f521",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/clientToken/action",
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR REST API Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-rest-api-reader"></a>Lecteur de l’API REST SignalR

Accès en lecture seule aux API REST du service Azure SignalR

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Vérifiez l’existence du groupe ou l’existence de l’utilisateur dans le groupe. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Vérifier l’existence de la connexion cliente. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Vérifiez l’existence d’un utilisateur. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ddde6b66-c0df-4114-a159-3618637b3035",
  "name": "ddde6b66-c0df-4114-a159-3618637b3035",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/user/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR REST API Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalr-service-owner"></a>Propriétaire SignalR Service

Accès complet aux API REST du service Azure SignalR

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/accessKey/action | Générer une clé d’accès pour signer les jetons d’accès. Par défaut, cette clé expire sous 90 minutes. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/auth/clientToken/action | Générer un jeton d’accès pour que le client se connecte à ASRS. Par défaut, ce jeton expire sous 5 minutes. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/hub/send/action | Diffusez des messages à toutes les connexions clientes dans le hub. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/send/action | Diffusez le message au groupe. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/read | Vérifiez l’existence du groupe ou l’existence de l’utilisateur dans le groupe. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/group/write | Rejoignez/Quittez le groupe. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/send/action | Envoyer des messages directement à une connexion cliente. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/read | Vérifier l’existence de la connexion cliente. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/clientConnection/write | Fermez la connexion cliente. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/serverConnection/write | Démarrer une connexion au serveur. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/send/action | Envoyer des messages à l’utilisateur, qui peut se composer de plusieurs connexions clientes. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/read | Vérifiez l’existence d’un utilisateur. |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/SignalR/user/write | Modifie un utilisateur. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to Azure SignalR Service REST APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "name": "7e4f1700-ea5a-4f59-8f37-079cfe29dce3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.SignalRService/SignalR/auth/accessKey/action",
        "Microsoft.SignalRService/SignalR/auth/clientToken/action",
        "Microsoft.SignalRService/SignalR/hub/send/action",
        "Microsoft.SignalRService/SignalR/group/send/action",
        "Microsoft.SignalRService/SignalR/group/read",
        "Microsoft.SignalRService/SignalR/group/write",
        "Microsoft.SignalRService/SignalR/clientConnection/send/action",
        "Microsoft.SignalRService/SignalR/clientConnection/read",
        "Microsoft.SignalRService/SignalR/clientConnection/write",
        "Microsoft.SignalRService/SignalR/serverConnection/write",
        "Microsoft.SignalRService/SignalR/user/send/action",
        "Microsoft.SignalRService/SignalR/user/read",
        "Microsoft.SignalRService/SignalR/user/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR Service Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="signalrweb-pubsub-contributor"></a>Contributeur SignalR/Web PubSub

Créer, lire, mettre à jour et supprimer des ressources de service SignalR

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.SignalRService](resource-provider-operations.md#microsoftsignalrservice)/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete SignalR service resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "name": "8cf5e20a-e4b2-4e9d-b3a1-5ceb692c2761",
  "permissions": [
    {
      "actions": [
        "Microsoft.SignalRService/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SignalR/Web PubSub Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Contributeur de plan web

Gérez les plans web pour les sites web. Ne vous permet pas d’attribuer des rôles dans Azure RBAC.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/* | Créer et gérer des batteries de serveurs |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/hostingEnvironments/Join/Action | Joint un environnement App Service Environment |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Contributeur de site web

Gérez les sites web, mais pas les plans web. Ne vous permet pas d’attribuer des rôles dans Azure RBAC.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Créer et gérer les composants Insights |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/certificates/* | Créer et gérer les certificats de site web |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/listSitesAssignedToHostName/read | Récupère les noms de sites affectés à un nom d’hôte. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Joint un plan App Service |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Récupère les propriétés d’un plan App Service. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/* | Créer et gérer des sites web (la création de sites nécessite également des autorisations d’écriture pour le plan App Service associé) |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Containers


### <a name="acrdelete"></a>AcrDelete

Supprimer des référentiels, des balises ou des manifestes d’un registre de conteneurs. [En savoir plus](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/artifacts/delete | Supprimer l’artefact dans un registre de conteneurs. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

Envoyer (push) des images approuvées vers un registre de conteneurs activé pour l’approbation de contenu ou tirer (pull) des images approuvées d’un tel registre. [En savoir plus](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/sign/write | Envoie ou tire des métadonnées d’approbation du contenu pour un registre de conteneurs. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/trustedCollections/write | Autorise l’envoi (push) ou la publication de collections approuvées du contenu de registre de conteneurs. Similaire à l’action Microsoft.ContainerRegistry/registries/sign/write, sauf qu’il s’agit d’une action de données |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerRegistry/registries/trustedCollections/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull

Tirer (pull) des artefacts à partir d’un registre de conteneurs. [En savoir plus](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Tire (pull) ou obtient des images à partir d’un registre de conteneurs. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush

Envoyer (push) des artefacts vers un registre de conteneurs ou tirer (pull) des artefacts d’un tel registre. [En savoir plus](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/pull/read | Tire (pull) ou obtient des images à partir d’un registre de conteneurs. |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/push/write | Envoie (push) ou écrit des images dans un registre de conteneurs. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineReader

Tirer (pull) des images en quarantaine à partir d’un registre de conteneurs. [En savoir plus](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Tire (pull) ou obtient des images en quarantaine à partir du registre de conteneurs |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantinedArtifacts/read | Autorise le tirage (pull) ou l’obtention d’artefacts mis en quarantaine à partir du registre de conteneurs. Similaire à Microsoft.ContainerRegistry/registries/quarantine/read, sauf qu’il s’agit d’une action de données |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerRegistry/registries/quarantinedArtifacts/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

Envoyer (push) des images en quarantaine vers un registre de conteneurs ou extraire des images en quarantaine d’un tel registre. [En savoir plus](../container-registry/container-registry-roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/read | Tire (pull) ou obtient des images en quarantaine à partir du registre de conteneurs |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantine/write | Écrit ou modifie l’état des images en quarantaine |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantinedArtifacts/read | Autorise le tirage (pull) ou l’obtention d’artefacts mis en quarantaine à partir du registre de conteneurs. Similaire à Microsoft.ContainerRegistry/registries/quarantine/read, sauf qu’il s’agit d’une action de données |
> | [Microsoft.ContainerRegistry](resource-provider-operations.md#microsoftcontainerregistry)/registries/quarantinedArtifacts/write | Autorise l’écriture ou la mise à jour de l’état de quarantaine d’artefacts mis en quarantaine. Similaire à l’action Microsoft.ContainerRegistry/registries/quarantine/write, sauf qu’il s’agit d’une action de données |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerRegistry/registries/quarantinedArtifacts/read",
        "Microsoft.ContainerRegistry/registries/quarantinedArtifacts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Rôle d’administrateur de cluster Azure Kubernetes Service

Répertorie les actions relatives aux informations d’identification de l’administrateur du cluster. [En savoir plus](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterAdminCredential/action | Répertorier les informations d’identification clusterAdmin d’un cluster géré |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/accessProfiles/listCredential/action | Obtient un profil d’accès au cluster géré en fonction du nom de rôle à l’aide des informations d’identification de la liste |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Obtient un cluster géré |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Rôle d’utilisateur de cluster Azure Kubernetes Service

Répertorie les actions relatives aux informations d’identification de l’utilisateur du cluster. [En savoir plus](../aks/control-kubeconfig-access.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Répertorier les informations d’identification clusterAdmin d’un cluster géré |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Obtient un cluster géré |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action",
        "Microsoft.ContainerService/managedClusters/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-contributor-role"></a>Rôle Contributeur Azure Kubernetes Service

Octroie l’accès en lecture et en écriture aux clusters Azure Kubernetes Service. [En savoir plus](../aks/concepts-identity.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/read | Obtient un cluster géré |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/write | Crée ou met à jour un cluster géré |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read and write Azure Kubernetes Service clusters",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "name": "ed7f3fbd-7b88-4dd4-9017-9adb7ce333f8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/read",
        "Microsoft.ContainerService/managedClusters/write",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-admin"></a>Azure Kubernetes Service RBAC Admin

Gérez toutes les ressources sous cluster/espace de noms, à l’exception de la mise à jour ou de la suppression de quotas de ressources et d’espaces de noms. [En savoir plus](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crée ou met à jour un déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Répertorier les informations d’identification clusterAdmin d’un cluster géré |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/write | Écrit resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/delete | Supprime resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/write | Écrit namespaces |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/delete | Supprime namespaces |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3498e952-d568-435e-9b2c-8d77e338d7f7",
  "name": "3498e952-d568-435e-9b2c-8d77e338d7f7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": [
        "Microsoft.ContainerService/managedClusters/resourcequotas/write",
        "Microsoft.ContainerService/managedClusters/resourcequotas/delete",
        "Microsoft.ContainerService/managedClusters/namespaces/write",
        "Microsoft.ContainerService/managedClusters/namespaces/delete"
      ]
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-cluster-admin"></a>Azure Kubernetes Service RBAC Cluster Admin

Gérez toutes les ressources du cluster. [En savoir plus](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crée ou met à jour un déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/listClusterUserCredential/action | Répertorier les informations d’identification clusterAdmin d’un cluster géré |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "name": "b1ff04bb-8a4e-4dc4-8eb5-8693973ce19b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-reader"></a>Azure Kubernetes Service RBAC Reader

Autorise l’accès en lecture seule pour voir la plupart des objets dans un espace de noms. Ce rôle n’autorise pas l’affichage des rôles ni des liaisons de rôles. Il n’autorise pas l’affichage des secrets, car la lecture du contenu de Secrets donne accès aux informations d’identification ServiceAccount dans l’espace de noms, ce qui permet l’accès aux API comme n’importe quel ServiceAccount dans l’espace de noms (une forme d’élévation de privilèges). L’application de ce rôle à l’étendue du cluster fournit un accès à tous les espaces de noms. [En savoir plus](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crée ou met à jour un déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Lit controllerrevisions |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/read | Lit daemonsets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/read | Lit deployments |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/read | Lit replicasets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/read | Lit statefulsets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/read | Lit horizontalpodautoscalers |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/read | Lit cronjobs |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/read | Lit jobs |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/read | Lit configmaps |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/read | Lit endpoints |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Lit events |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Lit events |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/read | Lit daemonsets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/read | Lit deployments |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/read | Lit ingresses |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/read | Lit networkpolicies |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/read | Lit replicasets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Lit limitranges |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Lit namespaces |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/read | Lit ingresses |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/read | Lit networkpolicies |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/read | Lit persistentvolumeclaims |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/read | Lit pods |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/read | Lit poddisruptionbudgets |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Lit replicationcontrollers |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/read | Lit replicationcontrollers |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Lit resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/read | Lit serviceaccounts |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/read | Lit services |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read-only access to see most objects in a namespace. It does not allow viewing roles or role bindings. This role does not allow viewing Secrets, since reading the contents of Secrets enables access to ServiceAccount credentials in the namespace, which would allow API access as any ServiceAccount in the namespace (a form of privilege escalation). Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "name": "7f6c6a51-bcf8-42ba-9220-52d62157d7db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/apps/deployments/read",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/read",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/read",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/read",
        "Microsoft.ContainerService/managedClusters/batch/jobs/read",
        "Microsoft.ContainerService/managedClusters/configmaps/read",
        "Microsoft.ContainerService/managedClusters/endpoints/read",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/read",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/read",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/read",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/read",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/read",
        "Microsoft.ContainerService/managedClusters/pods/read",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/read",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/read",
        "Microsoft.ContainerService/managedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-rbac-writer"></a>Azure Kubernetes Service RBAC Writer

Autorise l’accès en lecture/écriture à la plupart des objets d’un espace de noms. Ce rôle n’autorise pas l’affichage ni la modification des rôles ou des liaisons de rôles. Toutefois, ce rôle permet d’accéder aux secrets et aux pods en cours d’exécution comme n’importe quel ServiceAccount de l’espace de noms. Il peut donc être utilisé pour obtenir les niveaux d’accès API de n’importe quel ServiceAccount dans l’espace de noms. L’application de ce rôle à l’étendue du cluster fournit un accès à tous les espaces de noms. [En savoir plus](../aks/manage-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crée ou met à jour un déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/controllerrevisions/read | Lit controllerrevisions |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/apps/statefulsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/cronjobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/batch/jobs/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/configmaps/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/endpoints/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events.k8s.io/events/read | Lit events |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/events/read | Lit events |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/daemonsets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/deployments/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/extensions/replicasets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/limitranges/read | Lit limitranges |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/namespaces/read | Lit namespaces |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/pods/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/replicationcontrollers/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/resourcequotas/read | Lit resourcequotas |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/secrets/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/serviceaccounts/* |  |
> | [Microsoft.ContainerService](resource-provider-operations.md#microsoftcontainerservice)/managedClusters/services/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read/write access to most objects in a namespace.This role does not allow viewing or modifying roles or role bindings. However, this role allows accessing Secrets and running Pods as any ServiceAccount in the namespace, so it can be used to gain the API access levels of any ServiceAccount in the namespace. Applying this role at cluster scope will give access across all namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "name": "a7ffa36f-339b-4b5c-8bdf-e2c188b2c0eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ContainerService/managedClusters/apps/controllerrevisions/read",
        "Microsoft.ContainerService/managedClusters/apps/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/apps/deployments/*",
        "Microsoft.ContainerService/managedClusters/apps/replicasets/*",
        "Microsoft.ContainerService/managedClusters/apps/statefulsets/*",
        "Microsoft.ContainerService/managedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.ContainerService/managedClusters/batch/cronjobs/*",
        "Microsoft.ContainerService/managedClusters/batch/jobs/*",
        "Microsoft.ContainerService/managedClusters/configmaps/*",
        "Microsoft.ContainerService/managedClusters/endpoints/*",
        "Microsoft.ContainerService/managedClusters/events.k8s.io/events/read",
        "Microsoft.ContainerService/managedClusters/events/read",
        "Microsoft.ContainerService/managedClusters/extensions/daemonsets/*",
        "Microsoft.ContainerService/managedClusters/extensions/deployments/*",
        "Microsoft.ContainerService/managedClusters/extensions/ingresses/*",
        "Microsoft.ContainerService/managedClusters/extensions/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/extensions/replicasets/*",
        "Microsoft.ContainerService/managedClusters/limitranges/read",
        "Microsoft.ContainerService/managedClusters/namespaces/read",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.ContainerService/managedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.ContainerService/managedClusters/persistentvolumeclaims/*",
        "Microsoft.ContainerService/managedClusters/pods/*",
        "Microsoft.ContainerService/managedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/replicationcontrollers/*",
        "Microsoft.ContainerService/managedClusters/resourcequotas/read",
        "Microsoft.ContainerService/managedClusters/secrets/*",
        "Microsoft.ContainerService/managedClusters/serviceaccounts/*",
        "Microsoft.ContainerService/managedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service RBAC Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Bases de données


### <a name="azure-connected-sql-server-onboarding"></a>Intégration de SQL Server connecté à Azure

Autorise l’accès en lecture et en écriture aux ressources Azure pour SQL Server sur les serveurs avec Arc. [En savoir plus](/sql/sql-server/azure-arc/connect)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | Microsoft.AzureArcData/sqlServerInstances/read | Récupère une ressource d’instance SQL Server |
> | Microsoft.AzureArcData/sqlServerInstances/write | Met à jour une ressource d’instance SQL Server |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Microsoft.AzureArcData service role to access the resources of Microsoft.AzureArcData stored with RPSAAS.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8113dce-c529-4d33-91fa-e9b972617508",
  "name": "e8113dce-c529-4d33-91fa-e9b972617508",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureArcData/sqlServerInstances/read",
        "Microsoft.AzureArcData/sqlServerInstances/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected SQL Server Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-account-reader-role"></a>Rôle de lecteur de compte Cosmos DB

Lire les données de comptes Azure Cosmos DB. Consultez [Contributeur de compte DocumentDB](#documentdb-account-contributor) pour en savoir plus sur la gestion des comptes Azure Cosmos DB. [En savoir plus](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/*/read | Lire n’importe quelle collection |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlykeys/action | Lire les clés en lecture seule du compte de base de données. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/read | Lire les définitions des mesures |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/read | Lire des mesures |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Opérateur Cosmos DB

Permet de gérer des comptes Azure Cosmos DB, mais pas d’accéder aux données qu’ils contiennent. Empêche d’accéder aux clés de compte et aux chaînes de connexion. [En savoir plus](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. Impossible à alerter. |
> | **NotActions** |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/readonlyKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/regenerateKey/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listKeys/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/listConnectionStrings/* |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/write | Créer ou mettre à jour une définition de rôle SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleDefinitions/delete | Supprimer une définition de rôle SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/write | Créer ou mettre à jour une attribution de rôle SQL |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/sqlRoleAssignments/delete | Supprimer une attribution de rôle SQL |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions/delete",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/write",
        "Microsoft.DocumentDB/databaseAccounts/sqlRoleAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Peut envoyer une requête de restauration d’une base de données Cosmos DB ou d’un conteneur pour un compte [En savoir plus](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/backup/action | Soumettre une demande pour configurer la sauvegarde |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/restore/action | Soumettre une demande de restauration |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosrestoreoperator"></a>CosmosRestoreOperator

Peut effectuer une action de restauration pour un compte de base de données Cosmos DB avec le mode de sauvegarde continu

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/restore/action | Soumettre une demande de restauration |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/*/read |  |
> | [Microsoft.DocumentDB](resource-provider-operations.md#microsoftdocumentdb)/locations/restorableDatabaseAccounts/read | Lit un compte de base de données pouvant être restauré ou liste tous les comptes de base de données pouvant être restaurés |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform restore action for Cosmos DB database account with continuous backup mode",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "name": "5432c526-bc82-444a-b7ba-57c5b0b5b34f",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosRestoreOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Contributeur de compte DocumentDB

Gérer des comptes Azure Cosmos DB. Azure Cosmos DB était auparavant appelé DocumentDB. [En savoir plus](../cosmos-db/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.DocumentDb](resource-provider-operations.md#microsoftdocumentdb)/databaseAccounts/* | Créer et gérer des comptes Azure Cosmos DB |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. Impossible à alerter. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Contributeur Cache Redis

Permet de gérer des caches Redis, mais pas d’y accéder.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/register/action | Inscrit le fournisseur de ressources « Microsoft.Cache » à un abonnement |
> | [Microsoft.Cache](resource-provider-operations.md#microsoftcache)/redis/* | Créer et gérer les caches Redis |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/register/action",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>Contributeur de base de données SQL

Permet de gérer des bases de données SQL, mais pas d’y accéder. Vous ne pouvez pas non plus gérer leurs stratégies de sécurité ni leurs serveurs SQL parents. [En savoir plus](../data-share/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/* | Créer et gérer les bases de données SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Lire des mesures |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Lire les définitions des mesures |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/ledgerDigestUploads/write | Active le chargement des synthèses de registre  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/ledgerDigestUploads/disable/action | Désactive le chargement des synthèses de registre |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Modifier les paramètres d'audit |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Récupère les enregistrements d’audit d’objet blob de base de données. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Modifier les stratégies de masquage des données |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Modifier les stratégies d'alerte de sécurité |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Modifier les mesures de sécurité |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/servers/databases/ledgerDigestUploads/write",
        "Microsoft.Sql/servers/databases/ledgerDigestUploads/disable/action",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>Contributeur d’Instance managée SQL

Permet de gérer des instances SQL Managed Instance et la configuration réseau requise, mais pas d’accorder l’accès à d’autres personnes.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkSecurityGroups/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/routeTables/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/instanceFailoverGroups/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/* |  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Lire des mesures |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Lire les définitions des mesures |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/delete | Supprime un objet d’authentification Azure Active Directory d’un serveur géré spécifique |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/write | Ajoute ou met à jour un objet d’authentification Azure Active Directory d’un serveur géré spécifique |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/locations/instanceFailoverGroups/*",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>Gestionnaire de sécurité SQL

Permet de gérer les stratégies de sécurité des serveurs et bases de données SQL, mais pas d’y accéder. [En savoir plus](../azure-sql/database/azure-defender-for-sql.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joint des ressources telles qu’un compte de stockage ou une base de données SQL à un sous-réseau. Impossible à alerter. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/administratorAzureAsyncOperation/read | Obtient le résultat des opérations de l’administrateur Azure Async de l’instance gérée. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | Créer et gérer les paramètres d’audit de serveur SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/read | Récupère les détails de la stratégie étendue d’audit des objets blob de serveur configurée sur un serveur spécifié |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Créer et gérer les paramètres d’audit de base de données de serveur SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Récupère les enregistrements d’audit d’objet blob de base de données. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Créer et gérer les stratégies de masquage de données de base de données de serveur SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/read | Récupère les détails de la stratégie étendue d’audit d’objets blob configurée dans une base de données spécifique |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/read | Retourner la liste des bases de données ou obtenir les propriétés pour la base de données spécifiée. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/read | Obtenir un schéma de base de données. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/read | Obtenir une colonne de base de données. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/read | Obtenir un tableau de base de données. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Créer et gérer les stratégies d’alerte de sécurité de base de données de serveur SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Créer et gérer les mesures de sécurité de base de données de serveur SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/transparentDataEncryption/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/firewallRules/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/read | Retourner la liste des serveurs ou obtenir les propriétés pour le serveur spécifié. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Créer et gérer les stratégies d’alerte de sécurité de serveur SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/read | Retourne la liste des instances gérées ou obtient les propriétés de l’instance gérée spécifiée. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/azureADOnlyAuthentications/* |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/sqlVulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/administrators/read | Obtient la liste des administrateurs de l’instance gérée. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/administrators/read | Obtient un objet d’administrateur Azure Active Directory spécifique |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/administratorAzureAsyncOperation/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/*",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/azureADOnlyAuthentications/*",
        "Microsoft.Security/sqlVulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/administrators/read",
        "Microsoft.Sql/servers/administrators/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>Contributeur SQL Server

Permet de gérer des serveurs et bases de données SQL, mais pas d’y accéder, ni de gérer leurs stratégies de sécurité. [En savoir plus](../azure-sql/database/authentication-aad-configure.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/locations/*/read |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/* | Créer et gérer les serveurs SQL |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Lire des mesures |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/read | Lire les définitions des mesures |
> | **NotActions** |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/securityAlertPolicies/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/managedInstances/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/auditingSettings/* | Modifier les paramètres d'audit d'un serveur SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditingSettings/* | Modifier les paramètres d'audit d'une base de données de serveur SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/auditRecords/read | Récupère les enregistrements d’audit d’objet blob de base de données. |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/currentSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/dataMaskingPolicies/* | Modifier les stratégies de masquage de données d'une base de données de serveur SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/recommendedSensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityAlertPolicies/* | Modifier les stratégies d'alerte de sécurité d'une base de données de serveur SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/securityMetrics/* | Modifier les mesures de sécurité d'une base de données de serveur SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/sensitivityLabels/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentScans/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/devOpsAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/extendedAuditingSettings/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/securityAlertPolicies/* | Modifier les stratégies d'alerte de sécurité du serveur SQL |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/vulnerabilityAssessments/* |  |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/delete | Supprime l’objet d’authentification Azure Active Directory d’un serveur spécifique |
> | [Microsoft.Sql](resource-provider-operations.md#microsoftsql)/servers/azureADOnlyAuthentications/write | Lit ou met à jour l’objet d’authentification Azure Active Directory d’un serveur spécifique |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/devOpsAuditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/delete",
        "Microsoft.Sql/servers/azureADOnlyAuthentications/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analytics


### <a name="azure-event-hubs-data-owner"></a>Propriétaire de données Azure Event Hubs

Permet un accès complet aux ressources Azure Event Hubs. [En savoir plus](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Récepteur de données Azure Event Hubs

Permet d’obtenir un accès en réception aux ressources Azure Event Hubs. [En savoir plus](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/receive/action |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Expéditeur de données Azure Event Hubs

Permet d’obtenir un accès en envoi aux ressources Azure Event Hubs. [En savoir plus](../event-hubs/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/eventhubs/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/*/send/action |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Contributeurs de fabrique de données

Créer et gérer des fabriques de données, ainsi que les ressources enfants qu’elles contiennent. [En savoir plus](../data-factory/concepts-roles-permissions.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/dataFactories/* | Créer et gérer des fabriques de données ainsi que leurs ressources enfants |
> | [Microsoft.DataFactory](resource-provider-operations.md#microsoftdatafactory)/factories/* | Créer et gérer des fabriques de données ainsi que leurs ressources enfants |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Créer ou mettre à jour un abonnement à un événement |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Videur de données

Supprimez des données privées à partir d’un espace de travail Log Analytics. [En savoir plus](../azure-monitor/logs/personal-data-mgmt.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/purge/action | Vider des données d’Application Insights |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Afficher les données Log Analytics |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/purge/action | Supprime les données spécifiées de l’espace de travail |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>Opérateur de cluster HDInsight

Permet de lire et de modifier des configurations de cluster HDInsight. [En savoir plus](../hdinsight/hdinsight-migrate-granular-access-cluster-configurations.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/*/read |  |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/getGatewaySettings/action | Obtenir les paramètres de passerelle pour un HDInsight Cluster |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/updateGatewaySettings/action | Mettre à jour les paramètres de passerelle pour un HDInsight Cluster |
> | [Microsoft.HDInsight](resource-provider-operations.md#microsofthdinsight)/clusters/configurations/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>Contributeur HDInsight Domain Services

Peut lire, créer, modifier et supprimer les opérations Domain Services nécessaires pour le pack Sécurité Entreprise HDInsight [En savoir plus](../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/*/read |  |
> | [Microsoft.AAD](resource-provider-operations.md#microsoftaad)/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Contributeur Log Analytics

Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. La modification des paramètres de supervision inclut l’ajout de l’extension de machine virtuelle aux machines virtuelles, la lecture des clés de comptes de stockage permettant de configurer la collection de journaux d’activité du stockage Azure, l’ajout de solutions et la configuration de diagnostics Azure sur toutes les ressources Azure. [En savoir plus](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/extensions/* |  |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Répertorie les clés d’accès des comptes de stockage. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/extensions/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Installe ou met à jour toutes les extensions Azure Arc |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Crée, met à jour ou lit le paramètre de diagnostic pour Analysis Server. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Lecteur Log Analytics

Peut afficher et rechercher toutes les données de surveillance, ainsi qu’afficher les paramètres de surveillance, notamment la configuration des diagnostics Azure sur toutes les ressources Azure. [En savoir plus](../azure-monitor/logs/manage-access.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Effectue les recherches à l’aide d’un nouveau moteur. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Exécute une requête de recherche. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/read | Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail. |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-curator-legacy"></a>Conservateur de données Purview (hérité)

Le rôle hérité Conservateur de données Microsoft.Purview peut créer, lire, modifier et supprimer des objets de données de catalogue et établir des relations entre les objets. Nous avons récemment déconseillé ce rôle pour l’accès en fonction du rôle Azure et introduit un nouveau conservateur de données dans le plan de données Azure Purview. Consultez [Contrôle d’accès dans Azure Purview - Rôles](../purview/catalog-permissions.md#roles)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Lire une ressource de compte pour le fournisseur Microsoft Purview. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Lire des objets de données. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/write | Créer, mettre à jour et supprimer des objets de données. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data curator is a legacy role that can create, read, modify and delete catalog data objects and establish relationships between objects. We have recently deprecated this role from Azure role-based access and introduced a new data curator inside Azure Purview data plane. See https://docs.microsoft.com/azure/purview/catalog-permissions#roles",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "name": "8a3c2885-9b38-4fd2-9d99-91af537c1347",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read",
        "Microsoft.Purview/accounts/data/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Curator (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-reader-legacy"></a>Lecteur de données Purview (hérité)

Le lecteur de données Microsoft. Purview est un rôle hérité qui peut lire les objets de données de catalogue. Nous avons récemment déconseillé ce rôle pour l’accès en fonction du rôle Azure et introduit un nouveau lecteur de données dans le plan de données Azure Purview. Consultez [Contrôle d’accès dans Azure Purview - Rôles](../purview/catalog-permissions.md#roles)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Lire une ressource de compte pour le fournisseur Microsoft Purview. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/data/read | Lire des objets de données. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data reader is a legacy role that can read catalog data objects. We have recently deprecated this role from Azure role-based access and introduced a new data reader inside Azure Purview data plane. See https://docs.microsoft.com/azure/purview/catalog-permissions#roles",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ff100721-1b9d-43d8-af52-42b69c1272db",
  "name": "ff100721-1b9d-43d8-af52-42b69c1272db",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Reader (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="purview-data-source-administrator-legacy"></a>Administrateur de source de données Purview (hérité)

Le rôle hérité Administrateur de source de données Microsoft.Purview peut gérer les sources de données et les analyses de données. Nous avons récemment déconseillé ce rôle pour l’accès en fonction du rôle Azure et introduit un nouvel administrateur de source de données dans le plan de données Azure Purview. Consultez [Contrôle d’accès dans Azure Purview - Rôles](../purview/catalog-permissions.md#roles)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/read | Lire une ressource de compte pour le fournisseur Microsoft Purview. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/read | Lire les sources de données et les analyses. |
> | [Microsoft.Purview](resource-provider-operations.md#microsoftpurview)/accounts/scan/write | Créer, mettre à jour et supprimer des sources de données et gérer les analyses. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "The Microsoft.Purview data source administrator is a legacy role that can manage data sources and data scans. We have recently deprecated this role from Azure role-based access and introduced a new data source admin inside Azure Purview data plane. See https://docs.microsoft.com/azure/purview/catalog-permissions#roles",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/200bba9e-f0c8-430f-892b-6f0794863803",
  "name": "200bba9e-f0c8-430f-892b-6f0794863803",
  "permissions": [
    {
      "actions": [
        "Microsoft.Purview/accounts/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Purview/accounts/scan/read",
        "Microsoft.Purview/accounts/scan/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Purview Data Source Administrator (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-contributor-preview"></a>Contributeur du registre de schémas (préversion)

Lire, écrire et supprimer des groupes de registres de schémas et des schémas.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read, write, and delete Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5dffeca3-4936-4216-b2bc-10343a5abb25",
  "name": "5dffeca3-4936-4216-b2bc-10343a5abb25",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Contributor (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="schema-registry-reader-preview"></a>Lecteur du registre de schémas (préversion)

Lire et répertorier les groupes de registres de schémas et les schémas.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemagroups/read | Obtenir la liste des descriptions de ressources du groupe de schémas |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.EventHub](resource-provider-operations.md#microsofteventhub)/namespaces/schemas/read | Récupérer des schémas |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and list Schema Registry groups and schemas.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "name": "2c56ea50-c6b3-40a6-83c0-9d98858bc7d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/namespaces/schemagroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/namespaces/schemas/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Schema Registry Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockchain


### <a name="blockchain-member-node-access-preview"></a>Accès au nœud du membre blockchain (préversion)

Permet d’accéder aux nœuds du membre blockchain [En savoir plus](../blockchain/service/configure-aad.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/read | Crée ou répertorie un ou plusieurs nœuds de transaction existants du membre blockchain. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Blockchain](resource-provider-operations.md#microsoftblockchain)/blockchainMembers/transactionNodes/connect/action | Connecte à un nœud de transaction d’un membre blockchain. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>IA + Machine Learning


### <a name="azureml-data-scientist"></a>Scientifique des données AzureML

Peut effectuer toutes les actions au sein d’un espace de travail Azure Machine Learning, à l’exception de la création ou de la suppression des ressources de calcul et de la modification de l’espace de travail lui-même.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/*/read |  |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/*/action |  |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/*/delete |  |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/*/write |  |
> | **NotActions** |  |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/delete | Supprime le ou les espaces de travail Machine Learning Services |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/write | Crée ou met à jour le ou les espaces de travail Machine Learning Services |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/computes/*/write |  |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/computes/*/delete |  |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/computes/listKeys/action | Répertorie les secrets des ressources de calcul dans l’espace de travail Machine Learning Services |
> | [Microsoft.MachineLearningServices](resource-provider-operations.md#microsoftmachinelearningservices)/workspaces/listKeys/action | Répertorie les secrets d’un espace de travail Machine Learning Services |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can perform all actions within an Azure Machine Learning workspace, except for creating or deleting compute resources and modifying the workspace itself.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f6c7c914-8db3-469d-8ca1-694a8f32e121",
  "name": "f6c7c914-8db3-469d-8ca1-694a8f32e121",
  "permissions": [
    {
      "actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/*/write"
      ],
      "notActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AzureML Data Scientist",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-contributor"></a>Contributeur Cognitive Services

Vous permet de créer, lire, mettre à jour, supprimer et gérer les clés de Cognitive Services. [En savoir plus](../cognitive-services/cognitive-services-virtual-networks.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/features/read | Afficher les fonctionnalités d’un abonnement |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/providers/features/read | Afficher les fonctionnalités d’un abonnement pour un fournisseur de ressources donné |
> | [Microsoft.Features](resource-provider-operations.md#microsoftfeatures)/providers/features/register/action | Enregistrer les fonctionnalités d’un abonnement pour un fournisseur de ressources donné |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Crée, met à jour ou lit le paramètre de diagnostic pour Analysis Server. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Lire les définitions de journal |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Lire les définitions des mesures |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Lire des mesures |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourcegroups/deployments/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Features/providers/features/register/action",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-contributor"></a>Contributeur Cognitive Services Custom Vision

Accès complet au projet, y compris la possibilité de visualiser, créer, modifier et supprimer des projets. [En savoir plus](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the ability to view, create, edit, or delete projects.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "name": "c1ff6cc2-c111-46fe-8896-e0ef812ad9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Custom Vision Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-deployment"></a>Déploiement de Cognitive Services Custom Vision

Publier, dépublier ou exporter des modèles. Le déploiement peut visualiser le projet, mais ne peut pas le mettre à jour. [En savoir plus](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/publish/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/iterations/export/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/quicktest/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/classify/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/detect/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Exporte un projet. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Publish, unpublish or export models. Deployment can view the project but can't update.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "name": "5c4089e1-6d96-4d2f-b296-c1bc7137275f",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/publish/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/iterations/export/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/quicktest/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/classify/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/detect/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Deployment",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-labeler"></a>Étiqueteur Cognitive Services Custom Vision

Visualiser, modifier des images d’entraînement, et créer, ajouter, supprimer ou effacer les étiquettes des images. Les étiqueteurs peuvent visualiser le projet, mais ne peuvent pas mettre à jour autre chose que des images d’entraînement et des étiquettes. [En savoir plus](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | Obtient les images qui ont été envoyées à votre point de terminaison de prédiction. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tags/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/images/suggested/* |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/tagsandregions/suggestions/action | Cette API recevra des suggestions d’étiquette et de région pour un tableau/lot d’images non étiquetées, ainsi que des confiances pour les étiquettes. Elle retourne un tableau vide si aucune étiquette n’est trouvée. |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Exporte un projet. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit training images and create, add, remove, or delete the image tags. Labelers can view the project but can't update anything other than training images and tags.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/88424f51-ebe7-446f-bc41-7fa16989e96c",
  "name": "88424f51-ebe7-446f-bc41-7fa16989e96c",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tags/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/images/suggested/*",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/tagsandregions/suggestions/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Labeler",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-reader"></a>Lecteur Cognitive Services Custom Vision

Actions en lecture seule dans le projet. Les lecteurs ne peuvent pas créer ni mettre à jour le projet. [En savoir plus](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/predictions/query/action | Obtient les images qui ont été envoyées à votre point de terminaison de prédiction. |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Exporte un projet. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only actions in the project. Readers can't create or update the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "name": "93586559-c37d-4a6b-ba08-b9f0940c2d73",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*/read",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/predictions/query/action"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-custom-vision-trainer"></a>Entraîneur Cognitive Services Custom Vision

Afficher, modifier les projets et entraîner les modèles, avec la possibilité de publier, de dépublier, d’exporter les modèles. Les entraîneurs ne peuvent pas créer ni supprimer le projet. [En savoir plus](../cognitive-services/custom-vision-service/role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/* |  |
> | **NotDataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/action | Crée un projet. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/delete | Supprime un projet spécifique. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/import/action | Importe un projet. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/CustomVision/projects/export/read | Exporte un projet. |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View, edit projects and train the models, including the ability to publish, unpublish, export the models. Trainers can't create or delete the project.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "name": "0a5ae4ab-0d65-4eeb-be61-29fc9b54394b",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/*"
      ],
      "notDataActions": [
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/delete",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/import/action",
        "Microsoft.CognitiveServices/accounts/CustomVision/projects/export/read"
      ]
    }
  ],
  "roleName": "Cognitive Services Custom Vision Trainer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Lecteur de données Cognitive Services (préversion)

Permet de lire des données Cognitive Services.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-face-recognizer"></a>Cognitive Services : Face Recognizer

Vous permet de détecter, de vérifier, d’identifier, de regrouper et de rechercher des opérations similaires sur l’API Visage. Ce rôle n’autorise pas les opérations de création ou de suppression, ce qui en fait un bon choix pour les points de terminaison nécessitant uniquement des fonctionnalités d’inférence, en suivant les meilleures pratiques en matière de « privilèges minimum ».

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/Face/detect/action | Détectez les visages humains dans une image, retournez des rectangles de visage et éventuellement des faceId, des points de repère et des attributs. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/Face/verify/action | Vérifiez si deux visages appartiennent à une même personne, ou si un visage appartient à une personne. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/Face/identify/action | L’identification de 1 à plusieurs pour rechercher les correspondances les plus proches pour le visage de la personne spécifiée dans la requête à partir d’un groupe de personnes ou d’un grand groupe de personnes. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/Face/group/action | Divise les visages des candidats en groupes en fonction de la similarité du visage. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/Face/findsimilars/action | En fonction du faceId de la requête, recherche les visages similaires à partir d’un tableau de faceId, d’une liste de visages ou d’une grande liste de visages. faceId |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you perform detect, verify, identify, group, and find similar operations on Face API. This role does not allow create or delete operations, which makes it well suited for endpoints that only need inferencing capabilities, following 'least privilege' best practices.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9894cab4-e18a-44aa-828b-cb588cd6f2d7",
  "name": "9894cab4-e18a-44aa-828b-cb588cd6f2d7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/Face/detect/action",
        "Microsoft.CognitiveServices/accounts/Face/verify/action",
        "Microsoft.CognitiveServices/accounts/Face/identify/action",
        "Microsoft.CognitiveServices/accounts/Face/group/action",
        "Microsoft.CognitiveServices/accounts/Face/findsimilars/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Face Recognizer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-metrics-advisor-administrator"></a>Administrateur Cognitive Services Metrics Advisor

Accès complet au projet, y compris la configuration au niveau du système. [En savoir plus](../applied-ai-services/metrics-advisor/how-tos/alerts.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/MetricsAdvisor/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access to the project, including the system level configuration.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cb43c632-a144-4ec5-977c-e80c4affc34a",
  "name": "cb43c632-a144-4ec5-977c-e80c4affc34a",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/MetricsAdvisor/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Metrics Advisor Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-editor"></a>Éditeur QnA Maker Cognitive Services

Vous permet de créer, modifier, importer et exporter une base de connaissances. Vous ne pouvez pas publier ni supprimer une base de connaissances. [En savoir plus](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Obtenez des informations sur une affectation de rôle. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | Obtenez des informations sur une définition de rôle. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | Permet d'obtenir la liste des bases de connaissances ou les détails d'une base de connaissances spécifique. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | Télécharge la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/create/write | Opération asynchrone permettant de créer une base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/write | Opération asynchrone pour modifier une base de connaissances ou remplacer le contenu de la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | Appelle GenerateAnswer pour interroger la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/train/action | Former un appel pour ajouter des suggestions à la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | Télécharge les modifications à partir du runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/write | Remplace les données de modification. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | Permet d'obtenir les clés d'un point de terminaison |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/refreshkeys/action | Recrée une clé de point de terminaison. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | Permet d'obtenir les paramètres d'un point de terminaison |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/write | Met à jour les paramètres d'un point de terminaison. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/operations/read | Obtient les détails d’une opération durable spécifique. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | Permet d'obtenir la liste des bases de connaissances ou les détails d'une base de connaissances spécifique. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | Télécharge la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/create/write | Opération asynchrone permettant de créer une base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/write | Opération asynchrone pour modifier une base de connaissances ou remplacer le contenu de la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | Appelle GenerateAnswer pour interroger la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/train/action | Former un appel pour ajouter des suggestions à la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | Télécharge les modifications à partir du runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/write | Remplace les données de modification. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | Permet d'obtenir les clés d'un point de terminaison |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action | Recrée une clé de point de terminaison. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | Permet d'obtenir les paramètres d'un point de terminaison |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/write | Met à jour les paramètres d'un point de terminaison. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/operations/read | Obtient les détails d’une opération durable spécifique. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | Permet d'obtenir la liste des bases de connaissances ou les détails d'une base de connaissances spécifique. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | Télécharge la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write | Opération asynchrone permettant de créer une base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/write | Opération asynchrone pour modifier une base de connaissances ou remplacer le contenu de la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | Appelle GenerateAnswer pour interroger la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action | Former un appel pour ajouter des suggestions à la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/read | Télécharge les modifications à partir du runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/write | Remplace les données de modification. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | Permet d'obtenir les clés d'un point de terminaison |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action | Recrée une clé de point de terminaison. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | Permet d'obtenir les paramètres d'un point de terminaison |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/write | Met à jour les paramètres d'un point de terminaison. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/operations/read | Obtient les détails d’une opération durable spécifique. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you create, edit, import and export a KB. You cannot publish or delete a KB.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "name": "f4cc2bf9-21be-47a1-bdf1-5c5804381025",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker/operations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/operations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/create/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/train/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/refreshkeys/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/write",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/operations/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Editor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-qna-maker-reader"></a>Lecteur QnA Maker Cognitive Services

Vous permet de seulement lire et tester une base de connaissances. [En savoir plus](../cognitive-services/qnamaker/reference-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Obtenez des informations sur une affectation de rôle. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleDefinitions/read | Obtenez des informations sur une définition de rôle. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/read | Permet d'obtenir la liste des bases de connaissances ou les détails d'une base de connaissances spécifique. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/download/read | Télécharge la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/knowledgebases/generateanswer/action | Appelle GenerateAnswer pour interroger la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/alterations/read | Télécharge les modifications à partir du runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointkeys/read | Permet d'obtenir les clés d'un point de terminaison |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker/endpointsettings/read | Permet d'obtenir les paramètres d'un point de terminaison |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/read | Permet d'obtenir la liste des bases de connaissances ou les détails d'une base de connaissances spécifique. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/download/read | Télécharge la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/knowledgebases/generateanswer/action | Appelle GenerateAnswer pour interroger la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/alterations/read | Télécharge les modifications à partir du runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointkeys/read | Permet d'obtenir les clés d'un point de terminaison |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/QnAMaker.v2/endpointsettings/read | Permet d'obtenir les paramètres d'un point de terminaison |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/read | Permet d'obtenir la liste des bases de connaissances ou les détails d'une base de connaissances spécifique. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read | Télécharge la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action | Appelle GenerateAnswer pour interroger la base de connaissances. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/alterations/read | Télécharge les modifications à partir du runtime. |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointkeys/read | Permet d'obtenir les clés d'un point de terminaison |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/TextAnalytics/QnAMaker/endpointsettings/read | Permet d'obtenir les paramètres d'un point de terminaison |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Let's you read and test a KB only.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/466ccd10-b268-4a11-b098-b4849f024126",
  "name": "466ccd10-b268-4a11-b098-b4849f024126",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/alterations/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/QnAMaker.v2/endpointsettings/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/download/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/knowledgebases/generateanswer/action",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/alterations/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointkeys/read",
        "Microsoft.CognitiveServices/accounts/TextAnalytics/QnAMaker/endpointsettings/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services QnA Maker Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Utilisateur Cognitive Services

Vous permet de lire et de répertorier les clés de Cognitive Services. [En savoir plus](../cognitive-services/authentication.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/*/read |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/accounts/listkeys/action | List keys (Afficher la liste des clés) |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Lire une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/read | Lire un paramètre de diagnostic de ressource |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logDefinitions/read | Lire les définitions de journal |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricdefinitions/read | Lire les définitions des mesures |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metrics/read | Lire des mesures |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.CognitiveServices](resource-provider-operations.md#microsoftcognitiveservices)/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="internet-of-things"></a>Internet des objets


### <a name="device-update-administrator"></a>Administrateur Device Update

Vous accorde l’accès complet aux opérations de gestion et de contenu [En savoir plus](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Effectue une opération de lecture liée aux mises à jour |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | Effectue une opération d’écriture liée aux mises à jour |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | Effectue une opération de suppression liée aux mises à jour |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Effectue une opération de lecture liée à la gestion |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | Effectue une opération d’écriture liée à la gestion |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | Effectue une opération de suppression liée à la gestion |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management and content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "name": "02ca0879-e8e4-47a5-a61e-5c618b76e64a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete",
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-administrator"></a>Administrateur de contenu Device Update

Vous accorde l’accès complet aux opérations de contenu [En savoir plus](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Effectue une opération de lecture liée aux mises à jour |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/write | Effectue une opération d’écriture liée aux mises à jour |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/delete | Effectue une opération de suppression liée aux mises à jour |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to content operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "name": "0378884a-3af5-44ab-8323-f5b22f9f3c98",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/updates/write",
        "Microsoft.DeviceUpdate/accounts/instances/updates/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-content-reader"></a>Lecteur du contenu Device Update

Vous accorde l’accès en lecture aux opérations de contenu, mais ne vous permet pas d’effectuer des modifications [En savoir plus](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Effectue une opération de lecture liée aux mises à jour |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "name": "d1ee9a80-8b14-47f0-bdc2-f4a351625a7b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Content Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-administrator"></a>Administrateur des déploiements Device Update

Vous accorde l’accès complet aux opérations de gestion [En savoir plus](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Effectue une opération de lecture liée à la gestion |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/write | Effectue une opération d’écriture liée à la gestion |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/delete | Effectue une opération de suppression liée à la gestion |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you full access to management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e4237640-0e3d-4a46-8fda-70bc94856432",
  "name": "e4237640-0e3d-4a46-8fda-70bc94856432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/write",
        "Microsoft.DeviceUpdate/accounts/instances/management/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-deployments-reader"></a>Lecteur des déploiements Device Update

Vous accorde l’accès en lecture aux opérations de gestion, mais ne vous permet pas d’effectuer des modifications [En savoir plus](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Effectue une opération de lecture liée à la gestion |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "name": "49e2f5d2-7741-4835-8efa-19e1fe35e47f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Deployments Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="device-update-reader"></a>Lecteur Device Update

Vous accorde l’accès en lecture aux opérations de gestion et de contenu, mais ne vous permet pas d’effectuer des modifications [En savoir plus](../iot-hub-device-update/device-update-control-access.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/updates/read | Effectue une opération de lecture liée aux mises à jour |
> | [Microsoft.DeviceUpdate](resource-provider-operations.md#microsoftdeviceupdate)/accounts/instances/management/read | Effectue une opération de lecture liée à la gestion |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives you read access to management and content operations, but does not allow making changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "name": "e9dba6fb-3d52-4cf0-bce3-f06ce71b9e0f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/alertRules/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.DeviceUpdate/accounts/instances/updates/read",
        "Microsoft.DeviceUpdate/accounts/instances/management/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Device Update Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="iot-hub-data-contributor"></a>Contributeur de données IoT Hub

Permet un accès complet aux opérations du plan de données IoT Hub. [En savoir plus](../iot-hub/iot-hub-dev-guide-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Devices](resource-provider-operations.md#microsoftdevices)/IotHubs/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to IoT Hub data plane operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fc6c259-987e-4a07-842e-c321cc9d413f",
  "name": "4fc6c259-987e-4a07-842e-c321cc9d413f",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Devices/IotHubs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "IoT Hub Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="iot-hub-data-reader"></a>Lecteur de données IoT Hub

Permet un accès en lecture complet aux propriétés du plan de données IoT Hub [En savoir plus](../iot-hub/iot-hub-dev-guide-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Devices](resource-provider-operations.md#microsoftdevices)/IotHubs/*/read |  |
> | [Microsoft.Devices](resource-provider-operations.md#microsoftdevices)/IotHubs/fileUpload/notifications/action | Reçoit, termine ou abandonne les notifications de téléchargement de fichier |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full read access to IoT Hub data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b447c946-2db7-41ec-983d-d8bf3b1c77e3",
  "name": "b447c946-2db7-41ec-983d-d8bf3b1c77e3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Devices/IotHubs/*/read",
        "Microsoft.Devices/IotHubs/fileUpload/notifications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "IoT Hub Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="iot-hub-registry-contributor"></a>Contributeur du Registre IoT Hub

Permet un accès complet au Registre de l’appareil IoT Hub. [En savoir plus](../iot-hub/iot-hub-dev-guide-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Devices](resource-provider-operations.md#microsoftdevices)/IotHubs/devices/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to IoT Hub device registry.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4ea46cd5-c1b2-4a8e-910b-273211f9ce47",
  "name": "4ea46cd5-c1b2-4a8e-910b-273211f9ce47",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Devices/IotHubs/devices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "IoT Hub Registry Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="iot-hub-twin-contributor"></a>Contributeur de jumeau IoT Hub

Autorise l’accès en lecture et en écriture à tous les appareils et jumeaux de module IoT Hub. [En savoir plus](../iot-hub/iot-hub-dev-guide-azure-ad-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Devices](resource-provider-operations.md#microsoftdevices)/IotHubs/twins/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read and write access to all IoT Hub device and module twins.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494bdba2-168f-4f31-a0a1-191d2f7c028c",
  "name": "494bdba2-168f-4f31-a0a1-191d2f7c028c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Devices/IotHubs/twins/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "IoT Hub Twin Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Réalité mixte


### <a name="remote-rendering-administrator"></a>Administrateur Remote Rendering

Fournit à l’utilisateur des fonctionnalités de conversion, de gestion de session, de rendu et de diagnostic pour Azure Remote Rendering [En savoir plus](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/action | Lance une conversion de ressources |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/read | Permet d'obtenir les propriétés de la conversion de ressources |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/convert/delete | Met fin à une conversion de ressources |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Définit les propriétés de session |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Lance des sessions |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Met fin à des sessions |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Permet de se connecter à une session |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Permet de se connecter à l'inspecteur Remote Rendering |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with conversion, manage session, rendering and diagnostics capabilities for Azure Remote Rendering",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "name": "3df8b902-2a6f-47c7-8cc5-360e9b272a7e",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/convert/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="remote-rendering-client"></a>Client Remote Rendering

Fournit à l’utilisateur des fonctionnalités de gestion de session, de rendu et de diagnostic pour Azure Remote Rendering [En savoir plus](../remote-rendering/how-tos/authentication.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/read | Définit les propriétés de session |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/action | Lance des sessions |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/managesessions/delete | Met fin à des sessions |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/render/read | Permet de se connecter à une session |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/RemoteRenderingAccounts/diagnostic/read | Permet de se connecter à l'inspecteur Remote Rendering |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides user with manage session, rendering and diagnostics capabilities for Azure Remote Rendering.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "name": "d39065c4-c120-43c9-ab0a-63eed9795f0a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/action",
        "Microsoft.MixedReality/RemoteRenderingAccounts/managesessions/delete",
        "Microsoft.MixedReality/RemoteRenderingAccounts/render/read",
        "Microsoft.MixedReality/RemoteRenderingAccounts/diagnostic/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Remote Rendering Client",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-contributor"></a>Contributeur de compte Spatial Anchors

Permet de gérer des ancres spatiales dans votre compte, mais pas de les supprimer [En savoir plus](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Créer des ancres spatiales |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Détecter des ancres spatiales proches |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Obtenir les propriétés d’ancres spatiales |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Localiser des ancres spatiales |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Envoyer des données de diagnostic pour aider à améliorer la qualité du service Azure Spatial Anchors |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Mettre à jour les propriétés d’ancres spatiales |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Propriétaire de compte Spatial Anchors

Permet de gérer des ancres spatiales dans votre compte, y compris de les supprimer [En savoir plus](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/create/action | Créer des ancres spatiales |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/delete | Supprimer des ancres spatiales |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Détecter des ancres spatiales proches |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Obtenir les propriétés d’ancres spatiales |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Localiser des ancres spatiales |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Envoyer des données de diagnostic pour aider à améliorer la qualité du service Azure Spatial Anchors |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/write | Mettre à jour les propriétés d’ancres spatiales |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Lecteur de compte Spatial Anchors

Permet de localiser et de lire les propriétés d’ancres spatiales dans votre compte [En savoir plus](../spatial-anchors/concepts/authentication.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/discovery/read | Détecter des ancres spatiales proches |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/properties/read | Obtenir les propriétés d’ancres spatiales |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/query/read | Localiser des ancres spatiales |
> | [Microsoft.MixedReality](resource-provider-operations.md#microsoftmixedreality)/SpatialAnchorsAccounts/submitdiag/read | Envoyer des données de diagnostic pour aider à améliorer la qualité du service Azure Spatial Anchors |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Intégration


### <a name="api-management-service-contributor"></a>Contributeur du service de gestion des API

Peut gérer le service et les API [En savoir plus](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/* | Créer et gérer le service Gestion des API |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Rôle d’opérateur du service Gestion des API

Peut gérer le service, mais pas les API [En savoir plus](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Lire les instances du service Gestion des API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/backup/action | Sauvegarder le service Gestion des API dans le conteneur spécifié dans un compte de stockage fourni par l’utilisateur |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/delete | Supprimer l’instance du service Gestion des API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/managedeployments/action | Modifier une référence/unité, ajouter/supprimer des déploiements régionaux du service Gestion des API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Lire les métadonnées d’une instance du service Gestion des API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/restore/action | Restaurer le service Gestion des API à partir du conteneur spécifié dans un compte de stockage fourni par l’utilisateur |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatecertificate/action | Télécharger le certificat TLS/SSL pour un service Gestion des API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/updatehostname/action | Configurer, mettre à jour ou supprimer des noms de domaine personnalisés pour un service Gestion des API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/write | Crée ou met à jour une instance du service Gestion des API |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Obtenir les clés associées à un utilisateur |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Rôle de lecteur du service Gestion des API

Accès en lecture seule au service et aux API [En savoir plus](../api-management/api-management-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/*/read | Lire les instances du service Gestion des API |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/read | Lire les métadonnées d’une instance du service Gestion des API |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | [Microsoft.ApiManagement](resource-provider-operations.md#microsoftapimanagement)/service/users/keys/read | Obtenir les clés associées à un utilisateur |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>Propriétaire des données App Configuration

Permet l’accès total aux données App Configuration. [En savoir plus](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/write |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Lecteur des données App Configuration

Permet de lire les données App Configuration. [En savoir plus](../azure-app-configuration/concept-enable-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.AppConfiguration](resource-provider-operations.md#microsoftappconfiguration)/configurationStores/*/read |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-relay-listener"></a>Écouteur Azure Relay

Autorise l’accès en écoute aux ressources Azure Relay.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/*/wcfRelays/read |  |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/*/hybridConnections/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/*/listen/action |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for listen access to Azure Relay resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/26e0b698-aa6d-4085-9386-aadae190014d",
  "name": "26e0b698-aa6d-4085-9386-aadae190014d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Relay/*/wcfRelays/read",
        "Microsoft.Relay/*/hybridConnections/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Relay/*/listen/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Relay Listener",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-relay-owner"></a>Propriétaire Azure Relay

Autorise l’accès total aux ressources Azure Relay.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Relay resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2787bf04-f1f5-4bfe-8383-c8a24483ee38",
  "name": "2787bf04-f1f5-4bfe-8383-c8a24483ee38",
  "permissions": [
    {
      "actions": [
        "Microsoft.Relay/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Relay/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Relay Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-relay-sender"></a>Expéditeur Azure Relay

Autorise l’accès en envoi aux ressources Azure Relay.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/*/wcfRelays/read |  |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/*/hybridConnections/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Relay](resource-provider-operations.md#microsoftrelay)/*/send/action |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Relay resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/26baccc8-eea7-41f1-98f4-1762cc7f685d",
  "name": "26baccc8-eea7-41f1-98f4-1762cc7f685d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Relay/*/wcfRelays/read",
        "Microsoft.Relay/*/hybridConnections/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Relay/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Relay Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Propriétaire de données Azure Service Bus

Permet un accès total aux ressources Azure Service Bus. [En savoir plus](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Récepteur de données Azure Service Bus

Permet d’obtenir un accès en réception aux ressources Azure Service Bus. [En savoir plus](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/receive/action |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Expéditeur de données Azure Service Bus

Permet d’obtenir un accès en envoi aux ressources Azure Service Bus. [En savoir plus](../service-bus-messaging/authenticate-application.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/queues/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/read |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/topics/subscriptions/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.ServiceBus](resource-provider-operations.md#microsoftservicebus)/*/send/action |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Propriétaire de l’inscription Azure Stack

Permet de gérer les inscriptions Azure Stack.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/edgeSubscriptions/read |  |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/*/action |  |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/products/read | Obtient les propriétés d’un produit de la place de marché Azure Stack. |
> | [Microsoft.AzureStack](resource-provider-operations.md#microsoftazurestack)/registrations/read | Obtient les propriétés d’une inscription Azure Stack. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/edgeSubscriptions/read",
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-contributor"></a>Contributeur Eventgrid

Vous permet de gérer les opérations EventGrid.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/* | Créer et gérer des ressources Event Grid |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1e241071-0855-49ea-94dc-649edcd759de",
  "name": "1e241071-0855-49ea-94dc-649edcd759de",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-data-sender"></a>Expéditeur de données EventGrid

Autorise l’accès en envoi aux événements Event Grid.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topics/read | Lit une rubrique |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/domains/read | Lit un domaine |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/partnerNamespaces/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/events/send/action | Envoyer des événements aux rubriques |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to event grid events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d5a91429-5739-47e2-a06b-3470a27159e7",
  "name": "d5a91429-5739-47e2-a06b-3470a27159e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/topics/read",
        "Microsoft.EventGrid/domains/read",
        "Microsoft.EventGrid/partnerNamespaces/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventGrid/events/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>Contributeur EventGrid EventSubscription

Vous permet de gérer les opérations d’abonnement aux événements EventGrid. [En savoir plus](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/* | Créer et gérer des abonnements aux événements régionaux |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Lister les abonnements à des événements globaux par type de rubrique |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Lister les abonnements à des événements régionaux |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Lister des abonnements à des événements régionaux par type de rubrique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>Lecteur EventGrid EventSubscription

Vous permet de lire les abonnements aux événements EventGrid. [En savoir plus](../event-grid/security-authorization.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | Lit un abonnement à un événement |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/topicTypes/eventSubscriptions/read | Lister les abonnements à des événements globaux par type de rubrique |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/eventSubscriptions/read | Lister les abonnements à des événements régionaux |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/locations/topicTypes/eventSubscriptions/read | Lister des abonnements à des événements régionaux par type de rubrique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-contributor"></a>Contributeur aux données FHIR

Ce rôle accorde à l’utilisateur ou au principal un accès complet aux données FHIR [En savoir plus](../healthcare-apis/azure-api-for-fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | Microsoft.HealthcareApis/workspaces/fhirservices/resources/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal full access to FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "name": "5a1fc7df-4bf1-4951-a576-89034ee01acd",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*",
        "Microsoft.HealthcareApis/workspaces/fhirservices/resources/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-exporter"></a>Exportateur de données FHIR

Ce rôle permet à l’utilisateur ou au principal de lire et d’exporter des données FHIR [En savoir plus](../healthcare-apis/azure-api-for-fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Lire les ressources FHIR (y compris l’historique des recherches et des versions).  |
> | Microsoft.HealthcareApis/services/fhir/resources/export/action | Opération d’exportation ($export). |
> | Microsoft.HealthcareApis/workspaces/fhirservices/resources/read | Lire les ressources FHIR (y compris l’historique des recherches et des versions).  |
> | Microsoft.HealthcareApis/workspaces/fhirservices/resources/export/action | Opération d’exportation ($export). |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and export FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3db33094-8700-4567-8da5-1501d4e7e843",
  "name": "3db33094-8700-4567-8da5-1501d4e7e843",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/services/fhir/resources/export/action",
        "Microsoft.HealthcareApis/workspaces/fhirservices/resources/read",
        "Microsoft.HealthcareApis/workspaces/fhirservices/resources/export/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Exporter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-reader"></a>Lecteur de données FHIR

Ce rôle permet à l’utilisateur ou au principal de lire des données FHIR [En savoir plus](../healthcare-apis/azure-api-for-fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/read | Lire les ressources FHIR (y compris l’historique des recherches et des versions).  |
> | Microsoft.HealthcareApis/workspaces/fhirservices/resources/read | Lire les ressources FHIR (y compris l’historique des recherches et des versions).  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "name": "4c8d0bbc-75d3-4935-991f-5f3c56d81508",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/read",
        "Microsoft.HealthcareApis/workspaces/fhirservices/resources/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "FHIR Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="fhir-data-writer"></a>Enregistreur de données FHIR

Ce rôle permet à l’utilisateur ou au principal de lire et d’écrire des données FHIR [En savoir plus](../healthcare-apis/azure-api-for-fhir/configure-azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/* |  |
> | Microsoft.HealthcareApis/workspaces/fhirservices/resources/* |  |
> | **NotDataActions** |  |
> | Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action | Suppression définitive (y compris l’historique des versions). |
> | Microsoft.HealthcareApis/workspaces/fhirservices/resources/hardDelete/action | Suppression définitive (y compris l’historique des versions). |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role allows user or principal to read and write FHIR Data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3f88fce4-5892-4214-ae73-ba5294559913",
  "name": "3f88fce4-5892-4214-ae73-ba5294559913",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/*",
        "Microsoft.HealthcareApis/workspaces/fhirservices/resources/*"
      ],
      "notDataActions": [
        "Microsoft.HealthcareApis/services/fhir/resources/hardDelete/action",
        "Microsoft.HealthcareApis/workspaces/fhirservices/resources/hardDelete/action"
      ]
    }
  ],
  "roleName": "FHIR Data Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-contributor"></a>Contributeur de l’environnement de service d’intégration

Permet de gérer les environnements de service d’intégration, mais pas d’y accéder. [En savoir plus](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage integration service environments, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "name": "a41e2c5b-bd99-4a07-88f4-9bf657a760b8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="integration-service-environment-developer"></a>Développeur d’environnement de service d’intégration

Permet aux développeurs de créer et de mettre à jour des workflows, des comptes d’intégration et des connexions d’API dans les environnements de service d’intégration. [En savoir plus](../logic-apps/add-artifacts-integration-service-environment-ise.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/read | Lire l'environnement de service d'intégration |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/integrationServiceEnvironments/*/join/action |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows developers to create and update workflows, integration accounts and API connections in integration service environments.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "name": "c7aa55d3-1abb-444a-a5ca-5e51e485d6ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*",
        "Microsoft.Logic/integrationServiceEnvironments/read",
        "Microsoft.Logic/integrationServiceEnvironments/*/join/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Integration Service Environment Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Contributeur de compte Intelligent Systems

Permet de gérer des comptes Intelligent Systems, mais pas d’y accéder.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | Microsoft.IntelligentSystems/accounts/* | Créer et gérer les comptes Intelligent Systems |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Contributeur d’application logique

Permet de gérer des applications logiques, mais pas d’en modifier l’accès. [En savoir plus](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/listKeys/action | Répertorie les clés d’accès des comptes de stockage. |
> | [Microsoft.ClassicStorage](resource-provider-operations.md#microsoftclassicstorage)/storageAccounts/read | Retourne le compte de stockage avec le compte spécifique. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Crée, met à jour ou lit le paramètre de diagnostic pour Analysis Server. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/logdefinitions/* | Cette autorisation est nécessaire pour les utilisateurs qui doivent accéder aux journaux d’activité via le portail. Répertorier les catégories de journaux dans le journal d’activité. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/* | Lire des définitions de mesure (liste de types de mesure disponibles pour une ressource). |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/* | Gère les ressources Logic Apps. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/* | Crée et gère une passerelle de connexion. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/* | Crée et gère une connexion. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/* | Crée et gère une API personnalisée. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/join/action | Joint un plan App Service |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Récupère les propriétés d’un plan App Service. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/sites/functions/listSecrets/action | Liste les secrets de fonction. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Opérateur d’application logique

Permet de lire, d’activer et de désactiver des applications logiques, mais pas de les modifier ou de les mettre à jour. [En savoir plus](../logic-apps/logic-apps-securing-a-logic-app.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/*/read | Lit les règles d’alerte d’Insights. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/*/read | Obtient les paramètres de diagnostic de Logic Apps. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricDefinitions/*/read | Obtient les mesures disponibles pour Logic Apps. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/*/read | Lit les ressources Logic Apps. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/disable/action | Désactive le workflow. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/enable/action | Active le workflow. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/validate/action | Valide le workflow. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connectionGateways/*/read | Lit les passerelles de connexion. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/connections/*/read | Lit les connexions. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/customApis/*/read | Lit l’API personnalisée. |
> | [Microsoft.Web](resource-provider-operations.md#microsoftweb)/serverFarms/read | Récupère les propriétés d’un plan App Service. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identité


### <a name="managed-identity-contributor"></a>Contributeur d’identités gérées

Peut créer, lire, mettre à jour et supprimer une identité affectée par l’utilisateur [En savoir plus](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/read | Obtient l’identité assignée d’un utilisateur existant |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/write | Crée une identité assignée d’utilisateur ou met à jour les balises associées à l’identité assignée d’un utilisateur existant |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/delete | Supprime l’identité assignée d’un utilisateur existant |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Opérateur d’identités gérées

Peut lire et assigner une identité affectée par l’utilisateur [En savoir plus](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/read |  |
> | [Microsoft.ManagedIdentity](resource-provider-operations.md#microsoftmanagedidentity)/userAssignedIdentities/*/assign/action |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Sécurité


### <a name="attestation-contributor"></a>Contributeur d’attestation

Peut lire, écrire ou supprimer l’instance du fournisseur d’attestations [En savoir plus](../attestation/quickstart-powershell.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | Microsoft.Attestation/attestationProviders/attestation/write |  |
> | Microsoft.Attestation/attestationProviders/attestation/delete |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read write or delete the attestation provider instance",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "name": "bbf86eb8-f7b4-4cce-96e4-18cddf81d86e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read",
        "Microsoft.Attestation/attestationProviders/attestation/write",
        "Microsoft.Attestation/attestationProviders/attestation/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="attestation-reader"></a>Lecteur d’attestation

Peut lire les propriétés du fournisseur d’attestations [En savoir plus](../attestation/troubleshoot-guide.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | Microsoft.Attestation/attestationProviders/attestation/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read the attestation provider properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "name": "fd1bd22b-8476-40bc-a0bc-69b95687b9f3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Attestation/attestationProviders/attestation/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Attestation Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-administrator"></a>Administrateur Key Vault

Permet d’effectuer toutes les opération du plan de données sur un coffre de clés et tous les objets qu’il contient, notamment les certificats, les clés et les secrets. Ne peut pas gérer les ressources du coffre de clés ni gérer les attributions de rôles. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». [En savoir plus](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Vérifier que le nom du coffre Key Vault est valide et qu’il n’est pas déjà utilisé |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Afficher les propriétés de coffres Key Vault supprimés de manière réversible |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Répertorie les opérations disponibles sur le fournisseur de ressources Microsoft.KeyVault |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform all data plane operations on a key vault and all objects in it, including certificates, keys, and secrets. Cannot manage key vault resources or manage role assignments. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "name": "00482a5a-887f-4fb3-b363-3b7fe8e74483",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-certificates-officer"></a>Agent des certificats Key Vault

Permet d’effectuer une action sur les certificats d’un coffre de clés, à l’exception des autorisations de gestion. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». [En savoir plus](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Vérifier que le nom du coffre Key Vault est valide et qu’il n’est pas déjà utilisé |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Afficher les propriétés de coffres Key Vault supprimés de manière réversible |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Répertorie les opérations disponibles sur le fournisseur de ressources Microsoft.KeyVault |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificatecas/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/certificates/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the certificates of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4417e6f-fecd-4de8-b567-7b0420556985",
  "name": "a4417e6f-fecd-4de8-b567-7b0420556985",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/certificatecas/*",
        "Microsoft.KeyVault/vaults/certificates/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Certificates Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Contributeur Key Vault

Permet de gérer les coffres de clés, mais ne vous permet pas d’attribuer des rôles dans Azure RBAC ni d’accéder à des secrets, des clés ou des certificats. [En savoir plus](../key-vault/general/security-features.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/deletedVaults/purge/action | Vider un coffre Key Vault supprimé de manière réversible |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/hsmPools/* |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHsms/* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*",
        "Microsoft.KeyVault/managedHsms/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-officer"></a>Agent de chiffrement Key Vault

Permet d’effectuer une action sur les clés d’un coffre de clés, à l’exception des autorisations de gestion. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». [En savoir plus](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Vérifier que le nom du coffre Key Vault est valide et qu’il n’est pas déjà utilisé |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Afficher les propriétés de coffres Key Vault supprimés de manière réversible |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Répertorie les opérations disponibles sur le fournisseur de ressources Microsoft.KeyVault |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the keys of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "name": "14b46e9e-c2b7-41b4-b07b-48a6ebf60603",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-service-encryption-user"></a>Utilisateur du service de chiffrement de Key Vault

Permet de lire les métadonnées des clés et d’effectuer des opérations visant à envelopper/désenvelopper. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». [En savoir plus](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/write | Créer ou mettre à jour un abonnement à un événement |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/read | Lit un abonnement à un événement |
> | [Microsoft.EventGrid](resource-provider-operations.md#microsofteventgrid)/eventSubscriptions/delete | Supprimer un abonnement à un événement |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | Répertorie les clés dans le coffre spécifié, ou lit les propriétés et le matériel public d'une clé. Pour les clés asymétriques, cette opération expose la clé publique et permet d'exécuter des algorithmes de clé publique, comme le chiffrement et la vérification de la signature. Les clés privées et les clés symétriques ne sont jamais exposées. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Enveloppe une clé symétrique avec une clé Key Vault. Notez que si la clé Key Vault est asymétrique, cette opération peut être effectuée par des principaux avec accès en lecture. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Désenveloppe une clé symétrique avec une clé Key Vault. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of keys and perform wrap/unwrap operations. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "name": "e147488a-f6f5-4113-8e2d-b22465e65bf6",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventGrid/eventSubscriptions/write",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/eventSubscriptions/delete"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto Service Encryption User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-crypto-user"></a>Utilisateur de chiffrement Key Vault

Permet d’effectuer des opérations de chiffrement à l’aide de clés. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». [En savoir plus](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/read | Répertorie les clés dans le coffre spécifié, ou lit les propriétés et le matériel public d'une clé. Pour les clés asymétriques, cette opération expose la clé publique et permet d'exécuter des algorithmes de clé publique, comme le chiffrement et la vérification de la signature. Les clés privées et les clés symétriques ne sont jamais exposées. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/update/action | Met à jour les attributs spécifiés associés à la clé donnée. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/backup/action | Crée le fichier de sauvegarde d’une clé. Le fichier peut être utilisé pour restaurer la clé dans un coffre de clés Key Vault du même abonnement. Des restrictions peuvent s'appliquer. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/encrypt/action | Chiffre le texte en clair avec une clé. Notez que si la clé est asymétrique, cette opération peut être effectuée par des principaux avec accès en lecture. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/decrypt/action | Déchiffre le texte chiffré avec une clé. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/wrap/action | Enveloppe une clé symétrique avec une clé Key Vault. Notez que si la clé Key Vault est asymétrique, cette opération peut être effectuée par des principaux avec accès en lecture. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/unwrap/action | Désenveloppe une clé symétrique avec une clé Key Vault. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/sign/action | Signe un condensé de message (hachage) avec une clé. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/keys/verify/action | Vérifie la signature d’un condensé de message (hachage) à l’aide d’une clé. Notez que si la clé est asymétrique, cette opération peut être effectuée par des principaux avec accès en lecture. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform cryptographic operations using keys. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/12338af0-0e69-4776-bea7-57ae8d297424",
  "name": "12338af0-0e69-4776-bea7-57ae8d297424",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/keys/read",
        "Microsoft.KeyVault/vaults/keys/update/action",
        "Microsoft.KeyVault/vaults/keys/backup/action",
        "Microsoft.KeyVault/vaults/keys/encrypt/action",
        "Microsoft.KeyVault/vaults/keys/decrypt/action",
        "Microsoft.KeyVault/vaults/keys/wrap/action",
        "Microsoft.KeyVault/vaults/keys/unwrap/action",
        "Microsoft.KeyVault/vaults/keys/sign/action",
        "Microsoft.KeyVault/vaults/keys/verify/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Crypto User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-reader"></a>Lecteur Key Vault

Permet de lire les métadonnées de coffres de clés et de leurs certificats, clés et secrets. Ne peut pas lire les valeurs sensibles, telles que les contenus secrets ou les documents clés. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». [En savoir plus](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Vérifier que le nom du coffre Key Vault est valide et qu’il n’est pas déjà utilisé |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Afficher les propriétés de coffres Key Vault supprimés de manière réversible |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Répertorie les opérations disponibles sur le fournisseur de ressources Microsoft.KeyVault |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | Permet de répertorier ou d'affiche les propriétés d'un secret, mais pas sa valeur. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read metadata of key vaults and its certificates, keys, and secrets. Cannot read sensitive values such as secret contents or key material. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
  "name": "21090545-7ca7-4776-b22c-e363652d74d2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-officer"></a>Agent des secrets Key Vault

Permet d’effectuer une action sur les secrets d’un coffre de clés, à l’exception des autorisations de gestion. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». [En savoir plus](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/checkNameAvailability/read | Vérifier que le nom du coffre Key Vault est valide et qu’il n’est pas déjà utilisé |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/deletedVaults/read | Afficher les propriétés de coffres Key Vault supprimés de manière réversible |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/locations/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/*/read |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/operations/read | Répertorie les opérations disponibles sur le fournisseur de ressources Microsoft.KeyVault |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Perform any action on the secrets of a key vault, except manage permissions. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "name": "b86a8fe4-44ce-4948-aee5-eccb2c155cd7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.KeyVault/checkNameAvailability/read",
        "Microsoft.KeyVault/deletedVaults/read",
        "Microsoft.KeyVault/locations/*/read",
        "Microsoft.KeyVault/vaults/*/read",
        "Microsoft.KeyVault/operations/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets Officer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-secrets-user"></a>Utilisateur des secrets Key Vault

Permet de lire le contenu du secret. Fonctionne uniquement pour les coffres de clés qui utilisent le modèle d’autorisation « Contrôle d’accès en fonction du rôle Azure ». [En savoir plus](../key-vault/general/rbac-guide.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/getSecret/action | Obtient la valeur d’un secret. |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/vaults/secrets/readMetadata/action | Permet de répertorier ou d'affiche les propriétés d'un secret, mais pas sa valeur. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read secret contents. Only works for key vaults that use the 'Azure role-based access control' permission model.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
  "name": "4633458b-17de-408a-b874-0445c86b69e6",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.KeyVault/vaults/secrets/getSecret/action",
        "Microsoft.KeyVault/vaults/secrets/readMetadata/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Secrets User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-hsm-contributor"></a>Contributeur HSM managé

Vous permet de gérer des pools HSM managés, mais pas d’y accéder. [En savoir plus](../key-vault/managed-hsm/secure-your-managed-hsm.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.KeyVault](resource-provider-operations.md#microsoftkeyvault)/managedHSMs/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage managed HSM pools, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18500a29-7fe2-46b2-a342-b16a415e101d",
  "name": "18500a29-7fe2-46b2-a342-b16a415e101d",
  "permissions": [
    {
      "actions": [
        "Microsoft.KeyVault/managedHSMs/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed HSM contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="microsoft-sentinel-automation-contributor"></a>Contributeur Microsoft Sentinel Automation

Contributeur Microsoft Sentinel Automation [En savoir plus](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/read | Lit le déclencheur. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/triggers/listCallbackUrl/action | Obtient l’URL de rappel pour le déclencheur. |
> | [Microsoft.Logic](resource-provider-operations.md#microsoftlogic)/workflows/runs/read | Lit l’exécution d’un workflow. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Microsoft Sentinel Automation Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "name": "f4c81013-99ee-4d62-a7ee-b3f1f648599a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Logic/workflows/triggers/read",
        "Microsoft.Logic/workflows/triggers/listCallbackUrl/action",
        "Microsoft.Logic/workflows/runs/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Microsoft Sentinel Automation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="microsoft-sentinel-contributor"></a>Contributeur Microsoft Sentinel

Contributeur Microsoft Sentinel [En savoir plus](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Effectue les recherches à l’aide d’un nouveau moteur. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Afficher les données Log Analytics |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* |  |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Obtenir la solution OMS existante. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Exécuter des requêtes sur les données dans l’espace de travail |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Obtenir des sources de données sous un espace de travail. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/querypacks/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Lit un classeur privé |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Microsoft Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/querypacks/*/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Microsoft Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="microsoft-sentinel-reader"></a>Lecteur Microsoft Sentinel

Lecteur Microsoft Sentinel [En savoir plus](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Vérifier l’autorisation et la licence de l’utilisateur |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Interroger les indicateurs Threat Intelligence |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Interroger les indicateurs Threat Intelligence |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Effectue les recherches à l’aide d’un nouveau moteur. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Afficher les données Log Analytics |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/LinkedServices/read | Obtient les services liés sous un espace de travail spécifié. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Obtient une requête de recherche enregistrée. |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Obtenir la solution OMS existante. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Exécuter des requêtes sur les données dans l’espace de travail |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/querypacks/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Obtenir des sources de données sous un espace de travail. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Lire un classeur |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Lit un classeur privé |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Microsoft Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/querypacks/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Microsoft Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="microsoft-sentinel-responder"></a>Répondeur Microsoft Sentinel

Répondeur Microsoft Sentinel [En savoir plus](../sentinel/roles.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/*/read |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/dataConnectorsCheckRequirements/action | Vérifier l’autorisation et la licence de l’utilisateur |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/automationRules/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/* |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Ajouter des étiquettes à un indicateur Threat Intelligence |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/query/action | Interroger les indicateurs Threat Intelligence |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/bulkTag/action | Balises en masse du renseignement sur les menaces |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/appendTags/action | Ajouter des étiquettes à un indicateur Threat Intelligence |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/indicators/replaceTags/action | Remplacer les étiquettes d’un indicateur Threat Intelligence |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/threatIntelligence/queryIndicators/action | Interroger les indicateurs Threat Intelligence |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/analytics/query/action | Effectue les recherches à l’aide d’un nouveau moteur. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Afficher les données Log Analytics |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Obtenir des sources de données sous un espace de travail. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/read | Obtient une requête de recherche enregistrée. |
> | [Microsoft.OperationsManagement](resource-provider-operations.md#microsoftoperationsmanagement)/solutions/read | Obtenir la solution OMS existante. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/read | Exécuter des requêtes sur les données dans l’espace de travail |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/query/*/read |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/dataSources/read | Obtenir des sources de données sous un espace de travail. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/querypacks/*/read |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Lire un classeur |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/myworkbooks/read | Lit un classeur privé |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/cases/*/Delete |  |
> | [Microsoft.SecurityInsights](resource-provider-operations.md#microsoftsecurityinsights)/incidents/*/Delete |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Microsoft Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/dataConnectorsCheckRequirements/action",
        "Microsoft.SecurityInsights/automationRules/*",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/query/action",
        "Microsoft.SecurityInsights/threatIntelligence/bulkTag/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/appendTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/indicators/replaceTags/action",
        "Microsoft.SecurityInsights/threatIntelligence/queryIndicators/action",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/querypacks/*/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Insights/myworkbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.SecurityInsights/cases/*/Delete",
        "Microsoft.SecurityInsights/incidents/*/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Microsoft Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Administrateur de la sécurité

Autorisations d’affichage et de mise à jour pour Security Center. Dispose des mêmes autorisations que le rôle Lecteur de sécurité et peut également modifier la stratégie de sécurité et ignorer les alertes et les recommandations. [En savoir plus](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyAssignments/* | Créer et gérer des attributions de stratégies |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyDefinitions/* | Créer et gérer des définitions de stratégies |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyExemptions/* | Créer et gérer des exemptions de stratégie |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policySetDefinitions/* | Créer et gérer des ensembles de stratégies |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Afficher les données Log Analytics |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Créer et gérer des stratégies et des composants de sécurité |
> | [Microsoft.IoTSecurity](resource-provider-operations.md#microsoftiotsecurity)/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | [Microsoft.IoTSecurity](resource-provider-operations.md#microsoftiotsecurity)/defenderSettings/write | Crée ou met à jour les paramètres de Defender pour IoT |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policyExemptions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.IoTSecurity/*",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.IoTSecurity/defenderSettings/write"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Contributeur d'évaluation de la sécurité

Vous permet d’envoyer (push) les évaluations à Security Center

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/assessments/write | Créer ou mettre à jour des évaluations de la sécurité de votre abonnement |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Gestionnaire de sécurité (hérité)

Il s’agit d’un rôle hérité. Utilisez plutôt l’administrateur de sécurité.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/*/read | Lire les informations de configuration relatives aux machines virtuelles classiques |
> | [Microsoft.ClassicCompute](resource-provider-operations.md#microsoftclassiccompute)/virtualMachines/*/write | Écrire la configuration des machines virtuelles classiques |
> | [Microsoft.ClassicNetwork](resource-provider-operations.md#microsoftclassicnetwork)/*/read | Lire les informations de configuration relatives au réseau classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/* | Créer et gérer des stratégies et des composants de sécurité |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Lecteur de sécurité

Autorisations d’affichage pour Security Center. Peut afficher les recommandations, les alertes, une stratégie de sécurité et les états de sécurité, mais ne peut pas apporter de modifications. [En savoir plus](../security-center/security-center-permissions.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Lire une alerte de métrique classique |
> | [Microsoft.operationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/*/read | Afficher les données Log Analytics |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/*/read | Lire des stratégies et des composants de sécurité |
> | [Microsoft.IoTSecurity](resource-provider-operations.md#microsoftiotsecurity)/*/read |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/*/read |  |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/packageDownloads/action | Obtient des informations sur les packages de Defender pour IoT téléchargeables |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotDefenderSettings/downloadManagerActivation/action | Télécharger le fichier d’activation du gestionnaire avec les données de quota d’abonnement |
> | [Microsoft.Security](resource-provider-operations.md#microsoftsecurity)/iotSensors/downloadResetPassword/action | Les téléchargements réinitialisent le fichier de mot de passe pour les capteurs IoT |
> | [Microsoft.IoTSecurity](resource-provider-operations.md#microsoftiotsecurity)/defenderSettings/packageDownloads/action | Obtient des informations sur les packages de Defender pour IoT téléchargeables |
> | [Microsoft.IoTSecurity](resource-provider-operations.md#microsoftiotsecurity)/defenderSettings/downloadManagerActivation/action | Télécharger un fichier d’activation de gestionnaire |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.IoTSecurity/*/read",
        "Microsoft.Support/*/read",
        "Microsoft.Security/iotDefenderSettings/packageDownloads/action",
        "Microsoft.Security/iotDefenderSettings/downloadManagerActivation/action",
        "Microsoft.Security/iotSensors/downloadResetPassword/action",
        "Microsoft.IoTSecurity/defenderSettings/packageDownloads/action",
        "Microsoft.IoTSecurity/defenderSettings/downloadManagerActivation/action",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>Utilisateur de DevTest Labs

Permet de connecter, de démarrer, de redémarrer et d’arrêter vos machines virtuelles dans votre Azure DevTest Labs. [En savoir plus](../devtest-labs/devtest-lab-add-devtest-user.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/availabilitySets/read | Obtenir les propriétés d’un groupe à haute disponibilité |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/*/read | Lire les propriétés d’une machine virtuelle (tailles de machine virtuelle, état de l’exécution, extensions de machine virtuelle, etc.) |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/deallocate/action | Mettre hors tension la machine virtuelle et libérer les ressources de calcul |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/read | Obtenir les propriétés d’une machine virtuelle |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/restart/action | Redémarrer la machine virtuelle |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/start/action | Démarrer la machine virtuelle |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/*/read | Lire les propriétés d’un laboratoire |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/claimAnyVm/action | Revendiquer une machine virtuelle exigible aléatoire dans le laboratoire. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/createEnvironment/action | Créer des machines virtuelles dans un laboratoire. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/ensureCurrentUserProfile/action | Vérifiez que l’utilisateur actuel dispose d’un profil valide dans le labo. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/delete | Supprimer des formules. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/read | Lire des formules. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/formulas/write | Ajouter ou modifier des formules. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/policySets/evaluatePolicies/action | Évaluer la stratégie de laboratoire. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/claim/action | Prendre possession d’une machine virtuelle existante. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualmachines/listApplicableSchedules/action | Répertorie les planifications de démarrage/arrêt applicables, le cas échéant. |
> | [Microsoft.DevTestLab](resource-provider-operations.md#microsoftdevtestlab)/labs/virtualMachines/getRdpFileContents/action | Obtenir une chaîne qui représente le contenu du fichier RDP pour la machine virtuelle |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/backendAddressPools/join/action | Joint un pool d’adresses principales d’équilibrage de charge. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/loadBalancers/inboundNatRules/join/action | Joint une règle nat de trafic entrant d’équilibrage de charge. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/*/read | Lire les propriétés d’une interface réseau (par exemple, tous les équilibreurs de charge dont l’interface réseau fait partie) |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/join/action | Joint une machine virtuelle à une interface réseau. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/read | Obtient une définition d’interface réseau.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/networkInterfaces/write | Crée une interface réseau ou met à jour une interface réseau existante.  |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/*/read | Lire les propriétés d’une adresse IP publique |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/join/action | Joint une adresse IP publique. Impossible à alerter. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/publicIPAddresses/read | Obtient une définition de l’adresse IP publique. |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/subnets/join/action | Joint un réseau virtuel. Impossible à alerter. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/operations/read | Obtient ou répertorie les opérations de déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Obtient ou répertorie les déploiements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listKeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | **NotActions** |  |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/virtualMachines/vmSizes/read | Répertorier les tailles disponibles pour la mise à jour de la machine virtuelle |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Créateur Lab

Créez des labs sous vos comptes Azure Lab. [En savoir plus](../lab-services/add-lab-creator.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/*/read |  |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/createLab/action | Crée un lab dans un compte lab. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getPricingAndAvailability/action | Permet d'obtenir le prix et la disponibilité de combinaisons de tailles, de zones géographiques et de systèmes d'exploitation pour le compte Lab. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labAccounts/getRestrictionsAndUsage/action | Obtient les informations principales sur les restrictions et l’utilisation de cet abonnement |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labPlans/images/read | Obtient les propriétés d’une image. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labPlans/read | Obtient les propriétés d’un plan de laboratoire. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labPlans/saveImage/action | Crée une image à partir d’une machine virtuelle dans la galerie associée au plan de laboratoire. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labs/read | Obtient les propriétés d’un laboratoire. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labs/schedules/read | Obtient les propriétés d’une planification. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labs/users/read | Obtient les propriétés d’un utilisateur. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labs/virtualMachines/read | Obtient les propriétés d’une machine virtuelle. |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/locations/usages/read | Obtenir l’utilisation dans un emplacement |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/skus/read | Obtient les propriétés d’une SKU de Lab Services. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.LabServices](resource-provider-operations.md#microsoftlabservices)/labPlans/createLab/action | Crée un nouveau laboratoire à partir d’un plan de laboratoire. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create new labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.LabServices/labPlans/images/read",
        "Microsoft.LabServices/labPlans/read",
        "Microsoft.LabServices/labPlans/saveImage/action",
        "Microsoft.LabServices/labs/read",
        "Microsoft.LabServices/labs/schedules/read",
        "Microsoft.LabServices/labs/users/read",
        "Microsoft.LabServices/labs/virtualMachines/read",
        "Microsoft.LabServices/locations/usages/read",
        "Microsoft.LabServices/skus/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.LabServices/labPlans/createLab/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>Superviser


### <a name="application-insights-component-contributor"></a>Contributeur de composants Application Insights

Peut gérer les composants Application Insights [En savoir plus](../azure-monitor/app/resources-roles-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer des règles d’alerte classiques |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/generateLiveToken/read | Métriques en temps réel - Obtenir un jeton |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricAlerts/* | Créer et gérer de nouvelles règles d’alerte |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Créer et gérer les composants Insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/topology/read | Lire la topologie |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/transactions/read | Lire des transactions |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Créer et gérer les tests web Insights |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/generateLiveToken/read",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/topology/read",
        "Microsoft.Insights/transactions/read",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Débogueur de capture instantanée d’Application Insights

Autorise l’utilisateur à consulter et à télécharger les instantanés de débogage collectés à l’aide du débogueur de capture instantanée Application Insights. Ces autorisations ne sont pas incluses dans les rôles [Propriétaire](#owner) et [Contributeur](#contributor). Lorsque vous donnez aux utilisateurs le rôle Débogueur de capture instantanée Application Insights, vous devez leur accorder directement le rôle. Le rôle n’est pas reconnu lorsqu’il est ajouté à un rôle personnalisé. [En savoir plus](../azure-monitor/app/snapshot-debugger.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Contributeur d’analyse

Peut lire toutes les données de surveillance et modifier les paramètres de surveillance. Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). [En savoir plus](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alerts/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/alertsSummary/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/actiongroups/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/activityLogAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/AlertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/components/* | Créer et gérer les composants Insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionEndpoints/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/dataCollectionRuleAssociations/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/DiagnosticSettings/* | Crée, met à jour ou lit le paramètre de diagnostic pour Analysis Server. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/eventtypes/* | Événements du journal d’activité, (événements de gestion) dans un abonnement. Cette autorisation est applicable pour l’accès par programme et portail dans le journal d’activité. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/LogDefinitions/* | Cette autorisation est nécessaire pour les utilisateurs qui doivent accéder aux journaux d’activité via le portail. Répertorier les catégories de journaux dans le journal d’activité. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/metricalerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricDefinitions/* | Lire des définitions de mesure (liste de types de mesure disponibles pour une ressource). |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/* | Lire des mesures pour une ressource. |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Inscrire le fournisseur Microsoft Insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/scheduledqueryrules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/webtests/* | Créer et gérer les tests web Insights |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopes/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/privateLinkScopeOperationStatuses/* |  |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/write | Crée un espace de travail ou lie un espace de travail existant en fournissant l’ID de client à partir de l’espace de travail existant. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/intelligencepacks/* | Lire/écrire/supprimer des packs de solution Log Analytics. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/savedSearches/* | Lire/écrire/supprimer des recherches enregistrées Log Analytics. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Exécute une requête de recherche. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/storageinsightconfigs/* | Lire/écrire/supprimer les configurations des insights de stockage Log Analytics. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.WorkloadMonitor](resource-provider-operations.md#microsoftworkloadmonitor)/monitors/* | Obtenir des informations sur les moniteurs d’intégrité de machine virtuelles invitée. |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartDetectorAlertRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/actionRules/* |  |
> | [Microsoft.AlertsManagement](resource-provider-operations.md#microsoftalertsmanagement)/smartGroups/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/dataCollectionEndpoints/*",
        "Microsoft.Insights/dataCollectionRules/*",
        "Microsoft.Insights/dataCollectionRuleAssociations/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Insights/privateLinkScopes/*",
        "Microsoft.Insights/privateLinkScopeOperationStatuses/*",
        "Microsoft.OperationalInsights/workspaces/write",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Publication des métriques de surveillance

Permet de publier les métriques relatives aux ressources Azure [En savoir plus](../azure-monitor/insights/container-insights-update-metrics.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Register/Action | Inscrire le fournisseur Microsoft Insights |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/Metrics/Write | Écrit des métriques |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Lecteur d’analyse

Peut lire toutes les données de supervision (métriques, journaux d’activité, etc.) Consultez aussi [Bien démarrer avec les rôles, les autorisations et la sécurité dans Azure Monitor](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles). [En savoir plus](../azure-monitor/roles-permissions-security.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/search/action | Exécute une requête de recherche. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Contributeur de classeur

Peut enregistrer les classeurs partagés. [En savoir plus](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/write | Créer ou mettre à jour un classeur |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/delete | Supprimer un classeur |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Lire un classeur |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Lecteur de classeur

Peut lire les classeurs. [En savoir plus](../sentinel/tutorial-monitor-your-data.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [microsoft.insights](resource-provider-operations.md#microsoftinsights)/workbooks/read | Lire un classeur |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Gestion + gouvernance


### <a name="automation-contributor"></a>Contributeur Automation

Gérez les ressources Azure Automation et d’autres ressources à l’aide d’Azure Automation. [En savoir plus](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/* |  |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/ActionGroups/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/ActivityLogAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/MetricAlerts/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/ScheduledQueryRules/* |  |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/diagnosticSettings/* | Crée, met à jour ou lit le paramètre de diagnostic pour Analysis Server. |
> | [Microsoft.OperationalInsights](resource-provider-operations.md#microsoftoperationalinsights)/workspaces/sharedKeys/action | Récupère les clés partagées de l’espace de travail. Ces clés sont utilisées pour connecter les agents Microsoft Operational Insights à l’espace de travail. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Manage azure automation resources and other resources using azure automation.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f353d9bd-d4a6-484e-a77a-8050b599b867",
  "name": "f353d9bd-d4a6-484e-a77a-8050b599b867",
  "permissions": [
    {
      "actions": [
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/ActionGroups/*",
        "Microsoft.Insights/ActivityLogAlerts/*",
        "Microsoft.Insights/MetricAlerts/*",
        "Microsoft.Insights/ScheduledQueryRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-job-operator"></a>Opérateur de travaux Automation

Permet de créer et de gérer des travaux avec des runbooks Automation. [En savoir plus](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Lit des ressources Runbook Worker hybrides |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Obtient un travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Reprend un travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Arrête un travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Obtient un flux de travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Suspend un travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Crée un travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Obtient le résultat d’un travail |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Opérateur Automation

Les opérateurs d’Automation peuvent démarrer, arrêter, suspendre et reprendre des travaux [En savoir plus](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/hybridRunbookWorkerGroups/read | Lit des ressources Runbook Worker hybrides |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/read | Obtient un travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/resume/action | Reprend un travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/stop/action | Arrête un travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/streams/read | Obtient un flux de travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/suspend/action | Suspend un travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/write | Crée un travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/read | Obtient une planification du travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobSchedules/write | Crée une planification du travail Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/linkedWorkspace/read | Obtient l’espace de travail lié au compte Automation. |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/read | Obtient un compte Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Obtient un runbook Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/read | Obtient une ressource de planification Azure Automation |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/schedules/write | Crée ou met à jour une ressource de planification Azure Automation |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/jobs/output/read | Obtient le résultat d’un travail |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Opérateur de runbook Automation

Propriétés de lecture du runbook : pour pouvoir créer des travaux depuis le runbook. [En savoir plus](../automation/automation-role-based-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Automation](resource-provider-operations.md#microsoftautomation)/automationAccounts/runbooks/read | Obtient un runbook Azure Automation |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-enabled-kubernetes-cluster-user-role"></a>Rôle d'utilisateur de cluster Kubernetes avec Azure Arc

Répertorie les actions relatives aux informations d'identification de l'utilisateur du cluster.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crée ou met à jour un déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/listClusterUserCredentials/action | Liste les informations d'identification clusterUser |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credentials action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "name": "00493d72-78f6-4148-b6c5-d3ce8e4799dd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/listClusterUserCredentials/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Enabled Kubernetes Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-admin"></a>Azure Arc Kubernetes Admin

Gérez toutes les ressources sous cluster/espace de noms, à l’exception de la mise à jour ou de la suppression de quotas de ressources et d’espaces de noms. [En savoir plus](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crée ou met à jour un déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Lit controllerrevisions |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write | Écrit localsubjectaccessreviews |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Lit events |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Lit events |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Lit limitranges |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Lit namespaces |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/rolebindings/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/rbac.authorization.k8s.io/roles/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Lit resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources under cluster/namespace, except update or delete resource quotas and namespaces.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "name": "dffb1e0c-446f-4dde-a09f-99eb5cc68b96",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/authorization.k8s.io/localsubjectaccessreviews/write",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/rolebindings/*",
        "Microsoft.Kubernetes/connectedClusters/rbac.authorization.k8s.io/roles/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-cluster-admin"></a>Azure Arc Kubernetes Cluster Admin

Gérez toutes les ressources du cluster. [En savoir plus](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crée ou met à jour un déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage all resources in the cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "name": "8393591c-06b9-48a2-a542-1bd6b377f6a2",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Cluster Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-viewer"></a>Azure Arc Kubernetes Viewer

Affichez toutes les ressources dans le cluster/l’espace de noms, à l’exception des secrets. [En savoir plus](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crée ou met à jour un déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Lit controllerrevisions |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/read | Lit daemonsets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/read | Lit deployments |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/read | Lit replicasets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/read | Lit statefulsets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/read | Lit horizontalpodautoscalers |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/read | Lit cronjobs |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/read | Lit jobs |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/read | Lit configmaps |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/read | Lit endpoints |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Lit events |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Lit events |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/read | Lit daemonsets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/read | Lit deployments |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/read | Lit ingresses |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/read | Lit networkpolicies |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/read | Lit replicasets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Lit limitranges |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Lit namespaces |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/read | Lit ingresses |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/read | Lit networkpolicies |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/read | Lit persistentvolumeclaims |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/read | Lit pods |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/read | Lit poddisruptionbudgets |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | Lit replicationcontrollers |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/read | Lit replicationcontrollers |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Lit resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/read | Lit serviceaccounts |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/read | Lit services |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view all resources in cluster/namespace, except secrets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/63f0a09d-1495-4db4-a681-037d84835eb4",
  "name": "63f0a09d-1495-4db4-a681-037d84835eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/read",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/read",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/read",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/read",
        "Microsoft.Kubernetes/connectedClusters/configmaps/read",
        "Microsoft.Kubernetes/connectedClusters/endpoints/read",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/read",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/read",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/read",
        "Microsoft.Kubernetes/connectedClusters/pods/read",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/read",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/read",
        "Microsoft.Kubernetes/connectedClusters/services/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Viewer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-arc-kubernetes-writer"></a>Azure Arc Kubernetes Writer

Vous permet de mettre à jour tout ce qui se trouve dans le cluster/espace de noms, à l'exception des rôles (de cluster) et des liaisons de rôles (de cluster). [En savoir plus](../azure-arc/kubernetes/azure-rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crée ou met à jour un déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/controllerrevisions/read | Lit controllerrevisions |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/apps/statefulsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/autoscaling/horizontalpodautoscalers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/cronjobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/batch/jobs/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/configmaps/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/endpoints/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events.k8s.io/events/read | Lit events |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/events/read | Lit events |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/daemonsets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/deployments/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/extensions/replicasets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/limitranges/read | Lit limitranges |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/namespaces/read | Lit namespaces |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/ingresses/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/networking.k8s.io/networkpolicies/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/persistentvolumeclaims/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/pods/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/policy/poddisruptionbudgets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/replicationcontrollers/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/resourcequotas/read | Lit resourcequotas |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/secrets/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/serviceaccounts/* |  |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/services/* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you update everything in cluster/namespace, except (cluster)roles and (cluster)role bindings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5b999177-9696-4545-85c7-50de3797e5a1",
  "name": "5b999177-9696-4545-85c7-50de3797e5a1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/controllerrevisions/read",
        "Microsoft.Kubernetes/connectedClusters/apps/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/apps/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/apps/statefulsets/*",
        "Microsoft.Kubernetes/connectedClusters/autoscaling/horizontalpodautoscalers/*",
        "Microsoft.Kubernetes/connectedClusters/batch/cronjobs/*",
        "Microsoft.Kubernetes/connectedClusters/batch/jobs/*",
        "Microsoft.Kubernetes/connectedClusters/configmaps/*",
        "Microsoft.Kubernetes/connectedClusters/endpoints/*",
        "Microsoft.Kubernetes/connectedClusters/events.k8s.io/events/read",
        "Microsoft.Kubernetes/connectedClusters/events/read",
        "Microsoft.Kubernetes/connectedClusters/extensions/daemonsets/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/deployments/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/extensions/replicasets/*",
        "Microsoft.Kubernetes/connectedClusters/limitranges/read",
        "Microsoft.Kubernetes/connectedClusters/namespaces/read",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/ingresses/*",
        "Microsoft.Kubernetes/connectedClusters/networking.k8s.io/networkpolicies/*",
        "Microsoft.Kubernetes/connectedClusters/persistentvolumeclaims/*",
        "Microsoft.Kubernetes/connectedClusters/pods/*",
        "Microsoft.Kubernetes/connectedClusters/policy/poddisruptionbudgets/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/replicationcontrollers/*",
        "Microsoft.Kubernetes/connectedClusters/resourcequotas/read",
        "Microsoft.Kubernetes/connectedClusters/secrets/*",
        "Microsoft.Kubernetes/connectedClusters/serviceaccounts/*",
        "Microsoft.Kubernetes/connectedClusters/services/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Arc Kubernetes Writer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Intégration de machine connectée à Azure

Peut intégrer des machines connectées à Azure. [En savoir plus](../azure-arc/servers/onboard-service-principal.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Lit les données des machines Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Écrit toutes les machines Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/read | Lit tous les privateLinkScopes Azure Arc |
> | [Microsoft.GuestConfiguration](resource-provider-operations.md#microsoftguestconfiguration)/guestConfigurationAssignments/read | Obtient l’attribution de la configuration des invités. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/privateLinkScopes/read",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Administrateur des ressources de la machine connectée à Azure

Peut lire, écrire, supprimer et réintégrer des machines connectées à Azure.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/read | Lit les données des machines Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/write | Écrit toutes les machines Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/delete | Supprime toutes les machines Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/UpgradeExtensions/action | Met à niveau les extensions sur les machines Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/read | Lit toutes les extensions Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/write | Installe ou met à jour toutes les extensions Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/machines/extensions/delete | Supprime toutes les extensions Azure Arc |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/privateLinkScopes/* |  |
> | [Microsoft.HybridCompute](resource-provider-operations.md#microsofthybridcompute)/*/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/UpgradeExtensions/action",
        "Microsoft.HybridCompute/machines/extensions/read",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/machines/extensions/delete",
        "Microsoft.HybridCompute/privateLinkScopes/*",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Lecteur de facturation

Autorise l’accès en lecture aux données de facturation [En savoir plus](../cost-management-billing/manage/manage-billing-access.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/*/read | Lire les informations de facturation |
> | [Microsoft.Commerce](resource-provider-operations.md#microsoftcommerce)/*/read |  |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Contributeur blueprint

Peut gérer les définitions blueprint, mais ne peut pas les affecter. [En savoir plus](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprints/* | Créer et gérer des définitions de blueprint ou des artefacts de blueprint. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Opérateur blueprint

Peut affecter des blueprints publiés existants, mais ne peut pas en créer de nouveaux. Notez que cela fonctionne uniquement si l’affectation est effectuée avec une identité managée affectée par l’utilisateur. [En savoir plus](../governance/blueprints/overview.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Blueprint](resource-provider-operations.md#microsoftblueprint)/blueprintAssignments/* | Créer et gérer des affectations de blueprint |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Contributeur Cost Management

Peut afficher les coûts et gérer la configuration des coûts (par exemple budgets, exportations) [En savoir plus](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/* |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/* |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Obtenir des configurations |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Lit les recommandations |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Lecteur Cost Management

Peut afficher les données et la configuration des coûts (par exemple budgets, exportations) [En savoir plus](../cost-management-billing/costs/understand-work-scopes.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/*/read |  |
> | [Microsoft.CostManagement](resource-provider-operations.md#microsoftcostmanagement)/*/read |  |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingPeriods/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/configurations/read | Obtenir des configurations |
> | [Microsoft.Advisor](resource-provider-operations.md#microsoftadvisor)/recommendations/read | Lit les recommandations |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | [Microsoft.Billing](resource-provider-operations.md#microsoftbilling)/billingProperty/read |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Billing/billingProperty/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hierarchy-settings-administrator"></a>Administrateur de paramètres de hiérarchie

Permet aux utilisateurs de modifier et de supprimer des paramètres de hiérarchie

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/write | Crée ou met à jour les paramètres de hiérarchie de groupe d’administration. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/settings/delete | Supprime les paramètres de hiérarchie de groupe d’administration. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows users to edit and delete Hierarchy Settings",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/350f8d15-c687-4448-8ae1-157740a3936d",
  "name": "350f8d15-c687-4448-8ae1-157740a3936d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/settings/write",
        "Microsoft.Management/managementGroups/settings/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Hierarchy Settings Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-cluster---azure-arc-onboarding"></a>Cluster Kubernetes – Intégration Azure Arc

Définition de rôle pour autoriser tout utilisateur/service à créer une ressource connectedClusters. [En savoir plus](../azure-arc/kubernetes/connect-cluster.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/write | Crée ou met à jour un déploiement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/operationresults/read | Obtenir les résultats de l’opération de l’abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/Write | Écrit la ressource connectedClusters |
> | [Microsoft.Kubernetes](resource-provider-operations.md#microsoftkubernetes)/connectedClusters/read | Lit la ressource connectedClusters |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Role definition to authorize any user/service to create connectedClusters resource",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "name": "34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/write",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Kubernetes/connectedClusters/Write",
        "Microsoft.Kubernetes/connectedClusters/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Cluster - Azure Arc Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="kubernetes-extension-contributor"></a>Contributeur d’extension Kubernetes

Peut créer, mettre à jour, obtenir, répertorier et supprimer des extensions Kubernetes, et obtenir des opérations asynchrones d’extension

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.KubernetesConfiguration](resource-provider-operations.md#microsoftkubernetesconfiguration)/extensions/write | Crée ou met à jour une ressource d’extension. |
> | [Microsoft.KubernetesConfiguration](resource-provider-operations.md#microsoftkubernetesconfiguration)/extensions/read | Obtient la ressource d’instance d’extension. |
> | [Microsoft.KubernetesConfiguration](resource-provider-operations.md#microsoftkubernetesconfiguration)/extensions/delete | Supprime la ressource d’instance d’extension. |
> | [Microsoft.KubernetesConfiguration](resource-provider-operations.md#microsoftkubernetesconfiguration)/extensions/operations/read | Obtient l’état de l’opération asynchrone. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create, update, get, list and delete Kubernetes Extensions, and get extension async operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/85cb6faf-e071-4c9b-8136-154b5a04f717",
  "name": "85cb6faf-e071-4c9b-8136-154b5a04f717",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.KubernetesConfiguration/extensions/write",
        "Microsoft.KubernetesConfiguration/extensions/read",
        "Microsoft.KubernetesConfiguration/extensions/delete",
        "Microsoft.KubernetesConfiguration/extensions/operations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Kubernetes Extension Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Rôle Contributeur d'application managée

Permet de créer des ressources d’application managées.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/* |  |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/register/action | Inscrit à Solutions. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Rôle opérateur d’application managée

Permet de lire les ressources d’application managée et d’effectuer des actions sur ces ressources.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/applications/read | Récupère une liste d’applications. |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/*/action |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Lecteur Applications managées

Vous permet de lire les ressources dans une application managée et de demander un accès JIT.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Solutions](resource-provider-operations.md#microsoftsolutions)/jitRequests/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Suppression du rôle d’attribution d’inscription de services managé

La suppression du rôle d’attribution d’inscription de services managés permet aux utilisateurs du client gérant de supprimer l’attribution d’inscription assignée à leur locataire. [En savoir plus](../lighthouse/how-to/remove-delegation.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/read | Récupère la liste des affectations d'inscription des services managés. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/registrationAssignments/delete | Supprime l'affectation d'inscription des services managés. |
> | [Microsoft.ManagedServices](resource-provider-operations.md#microsoftmanagedservices)/operationStatuses/read | Lit l’état de l’opération pour la ressource. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Collaborateur du groupe d’administration

Rôle Contributeur du groupe d’administration [En savoir plus](../governance/management-groups/overview.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/delete | Supprime un groupe d’administration. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/delete | Dissocie un abonnement du groupe d’administration. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/write | Associe un abonnement existant au groupe d’administration. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/write | Crée ou met à jour un groupe d’administration. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Répertorie les abonnements associés au groupe d'administration donné |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Lecteur du groupe d’administration

Rôle de lecteur du groupe d’administration

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/read | Répertorie les groupes d’administration de l’utilisateur authentifié. |
> | [Microsoft.Management](resource-provider-operations.md#microsoftmanagement)/managementGroups/subscriptions/read | Répertorie les abonnements associés au groupe d'administration donné |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Contributeur de compte NewRelic APM

Vous permet de gérer des comptes et applications New Relic Application Performance Management, mais pas d’y accéder.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | NewRelic.APM/accounts/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Policy Insights Data Writer (préversion)

Permet de lire les stratégies de ressources et d’écrire les événements de stratégie de composant de ressource. [En savoir plus](../governance/policy/concepts/policy-for-kubernetes.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/read | Obtenez des informations sur une affectation de stratégie. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/read | Obtenez des informations sur une définition de stratégie. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/read | Obtenir des informations sur une exemption de stratégie. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/read | Obtient des informations sur une définition d’ensemble de stratégies |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/checkDataPolicyCompliance/action | Vérifie l’état de conformité d’un composant donné par rapport aux stratégies de données. |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/policyEvents/logDataEvents/action | Journalise les événements de stratégie de composant de ressource. |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policyexemptions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="quota-request-operator"></a>Opérateur de requête de quota

Lisez et créez des requêtes de quota, obtenez l’état de la requête de quota et créez des tickets de support. [En savoir plus](/rest/api/reserved-vm-instances/quotaapi)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/read | Obtenir la limite du service actuel, ou le quota de la ressource et de l’emplacement spécifiés |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimits/write | Créer une limite du service actuel, ou un quota pour la ressource et l’emplacement spécifiés |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/resourceProviders/locations/serviceLimitsRequests/read | Obtenir les demandes de limite du service pour la ressource et l’emplacement spécifiés |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | Inscrit le fournisseur de ressources de capacité et active la création de ressources de capacité. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and create quota requests, get quota request status, and create support tickets.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "name": "0e5f05e5-9ab9-446b-b98d-1e2157c94125",
  "permissions": [
    {
      "actions": [
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/read",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimits/write",
        "Microsoft.Capacity/resourceProviders/locations/serviceLimitsRequests/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Quota Request Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reservation-purchaser"></a>Acheteur de réservation

Vous permet d’acheter des réservations [En savoir plus](../cost-management-billing/reservations/prepare-buy-reservation.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/read | Obtient la liste des abonnements. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/register/action | Inscrit le fournisseur de ressources de capacité et active la création de ressources de capacité. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/register/action | Inscrit l’abonnement auprès du fournisseur de ressources Microsoft.Compute |
> | [Microsoft.SQL](resource-provider-operations.md#microsoftsql)/register/action | Inscrit l’abonnement pour le fournisseur de ressources Microsoft SQL Database et permet la création de bases de données Microsoft SQL. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/register/action | S’inscrire auprès d’un fournisseur de ressources de consommation |
> | [Microsoft.Capacity](resource-provider-operations.md#microsoftcapacity)/catalogs/read | Lire le catalogue de réservation |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/roleAssignments/read | Obtenez des informations sur une affectation de rôle. |
> | [Microsoft.Consumption](resource-provider-operations.md#microsoftconsumption)/reservationRecommendations/read | Répertorie les recommandations uniques ou partagées pour les instances réservées concernant un abonnement. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/supporttickets/write | Permet de créer et de mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you purchase reservations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "name": "f7b75c60-3036-4b75-91c3-6b41c27c1689",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Capacity/register/action",
        "Microsoft.Compute/register/action",
        "Microsoft.SQL/register/action",
        "Microsoft.Consumption/register/action",
        "Microsoft.Capacity/catalogs/read",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Consumption/reservationRecommendations/read",
        "Microsoft.Support/supporttickets/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reservation Purchaser",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Contributeur de la stratégie de ressource

Utilisateurs dotés de droits pour créer ou modifier une stratégie de ressource, créer un ticket de support et lire des ressources ou la hiérarchie. [En savoir plus](../governance/policy/overview.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | */read | Lire les ressources de tous les types, à l’exception des secrets. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyassignments/* | Créer et gérer des attributions de stratégies |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policydefinitions/* | Créer et gérer des définitions de stratégies |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policyexemptions/* | Créer et gérer des exemptions de stratégie |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/policysetdefinitions/* | Créer et gérer des ensembles de stratégies |
> | [Microsoft.PolicyInsights](resource-provider-operations.md#microsoftpolicyinsights)/* |  |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policyexemptions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Contributeur Site Recovery

Permet de gérer le service Site Recovery sauf la création de coffre et l’attribution de rôle [En savoir plus](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp est une opération interne utilisée par le service. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/certificates/write | L’opération de mise à jour de certificat de ressource met à jour le certificat d’identification du coffre/de la ressource. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/* | Créer et gérer des informations étendues associées au coffre |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/* | Créer et gérer les identités inscrites |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/* | Créer ou mettre à jour les paramètres d’alerte de réplication |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Lire des événements. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/* | Créer et gérer des structures de réplication |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Créer et gérer des travaux de réplication |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/* | Créer et gérer des stratégies de réplication |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/* | Créer et gérer des plans de récupération |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationVaultSettings/* |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/* | Créer et gérer la configuration de stockage du coffre Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Lire les alertes pour le coffre Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationOperationStatus/read | Lit tout état de l’opération de réplication du coffre |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/vaults/replicationVaultSettings/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Opérateur Site Recovery

Permet de basculer et de restaurer mais pas d’effectuer d’autres opérations de gestion de Site Recovery [En savoir plus](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Network](resource-provider-operations.md#microsoftnetwork)/virtualNetworks/read | Obtenir la définition de réseau virtuel. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocateStamp/action | AllocateStamp est une opération interne utilisée par le service. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Lire des paramètres d’alertes. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Lire des événements. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/checkConsistency/action | Vérifie la cohérence de la structure. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Lire des structures. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/reassociateGateway/action | Réassocier une passerelle. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/renewcertificate/action | Renouveler le certificat pour Fabric |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Lire des réseaux. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Lire des mappages réseau. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Lire des conteneurs de protection. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Lire des éléments pouvant être protégés. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Appliquer un point de récupération. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Validation du basculement. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Basculement planifié. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Lire des éléments protégés. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Lire des points de récupération de réplication. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Réparer la réplication. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Reprotéger l’élément protégé. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Basculer un conteneur de protection. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test Failover |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Nettoyage de basculement test. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Basculement |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Mettre à jour le service Mobilité. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Lire des mappages de conteneurs de protection. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Lire des fournisseurs Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Actualiser un fournisseur. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Lire des classifications de stockage. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Lire des mappages de classifications de stockage. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Lire des serveurs vCenter. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/* | Créer et gérer des travaux de réplication |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Lire des stratégies. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/failoverCommit/action | Plan de récupération de validation de basculement. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/plannedFailover/action | Plan de récupération de basculement planifié. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Lire des plans de récupération. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/reProtect/action | Reprotéger le plan de récupération. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailover/action | Plan de récupération de basculement test. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plan de récupération de nettoyage de basculement test. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan de récupération de basculement. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationVaultSettings/read | Lire tout  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/* | Lire les alertes pour le coffre Recovery Services |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre. |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationVaultSettings/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Lecteur Site Recovery

Permet d’afficher l’état de Site Recovery mais pas d’effectuer d’autres opérations de gestion [En savoir plus](../site-recovery/site-recovery-role-based-linked-access-control.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/locations/allocatedStamp/read | GetAllocatedStamp est une opération interne utilisée par le service. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/extendedInformation/read | L’opération d’obtention d’informations étendues obtient les informations étendues d’un objet représentant la ressource Azure de type « coffre ». |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringAlerts/read | Obtient les alertes pour le coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/read | L’opération d’obtention de coffre obtient un objet représentant la ressource Azure de type « coffre ». |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/refreshContainers/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/operationResults/read | L’opération d’obtention des résultats d’une opération peut être utilisée pour obtenir l’état de l’opération et le résultat de l’opération envoyée de manière asynchrone. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/registeredIdentities/read | L’opération d’obtention de conteneurs peut être utilisée pour obtenir les conteneurs inscrits pour une ressource. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationAlertSettings/read | Lire des paramètres d’alertes. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationEvents/read | Lire des événements. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/read | Lire des structures. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/read | Lire des réseaux. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Lire des mappages réseau. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/read | Lire des conteneurs de protection. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Lire des éléments pouvant être protégés. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Lire des éléments protégés. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Lire des points de récupération de réplication. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Lire des mappages de conteneurs de protection. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Lire des fournisseurs Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/read | Lire des classifications de stockage. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Lire des mappages de classifications de stockage. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationFabrics/replicationvCenters/read | Lire des serveurs vCenter. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationJobs/read | Lire des travaux. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationPolicies/read | Lire des stratégies. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationRecoveryPlans/read | Lire des plans de récupération. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/vaults/replicationVaultSettings/read | Lire tout  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/storageConfig/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/tokenInfo/read |  |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/usages/read | Renvoie des détails d’utilisation d’un coffre Recovery Services. |
> | [Microsoft.RecoveryServices](resource-provider-operations.md#microsoftrecoveryservices)/Vaults/vaultTokens/read | L’opération de jeton de coffre peut être utilisée pour obtenir un jeton de coffre pour les opérations de serveur principal au niveau du coffre. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationVaultSettings/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Contributeur de demande de support

Permet de créer et de gérer des demandes de support [En savoir plus](../azure-portal/supportability/how-to-create-azure-support-request.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>Contributeur d’étiquette

Vous permet de gérer les étiquettes sur les entités, sans fournir l’accès aux entités elles-mêmes. [En savoir plus](../azure-resource-manager/management/tag-resources.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/resources/read | Obtient les ressources du groupe de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resources/read | Obtient les ressources d’un abonnement. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/tags/* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Autres


### <a name="azure-digital-twins-data-owner"></a>Propriétaire des données Azure Digital Twins

Rôle d’accès complet pour le plan de données Digital Twins [En savoir plus](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/* | Lire, supprimer, créer ou mettre à jour une route d’événement |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/* | Lire, créer, mettre à jour ou supprimer un jumeau numérique |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/commands/* | Appeler une commande sur un jumeau numérique |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/* | Lire, créer, mettre à jour ou supprimer une relation de jumeau numérique |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/* | Lire, créer, mettre à jour ou supprimer un modèle |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/* | Interroger un graphe Digital Twins |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Full access role for Digital Twins data-plane",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "name": "bcd981a7-7f74-457b-83e1-cceb9e632ffe",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/eventroutes/*",
        "Microsoft.DigitalTwins/digitaltwins/*",
        "Microsoft.DigitalTwins/digitaltwins/commands/*",
        "Microsoft.DigitalTwins/digitaltwins/relationships/*",
        "Microsoft.DigitalTwins/models/*",
        "Microsoft.DigitalTwins/query/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-digital-twins-data-reader"></a>Lecteur des données Azure Digital Twins

Rôle en lecture seule pour les propriétés du plan de données Digital Twins [En savoir plus](../digital-twins/concepts-security.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/read | Lire un jumeau numérique |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/digitaltwins/relationships/read | Lire une relation du jumeau numérique |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/eventroutes/read | Lire une route d’événement |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/models/read | Lire un modèle |
> | [Microsoft.DigitalTwins](resource-provider-operations.md#microsoftdigitaltwins)/query/action | Interroger un graphe Digital Twins |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only role for Digital Twins data-plane properties",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "name": "d57506d4-4c8d-48b1-8587-93c323f6a5a3",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DigitalTwins/digitaltwins/read",
        "Microsoft.DigitalTwins/digitaltwins/relationships/read",
        "Microsoft.DigitalTwins/eventroutes/read",
        "Microsoft.DigitalTwins/models/read",
        "Microsoft.DigitalTwins/query/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Digital Twins Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="biztalk-contributor"></a>Contributeur BizTalk

Permet de gérer des services BizTalk, mais pas d’y accéder.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | Microsoft.BizTalkServices/BizTalk/* | Créer et gérer BizTalk Services |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-contributor"></a>Contributeur du groupe d’applications de virtualisation de poste de travail

Contributeur du groupe d’applications de virtualisation de poste de travail. [En savoir plus](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Lire des pools d’hôtes |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Lire des pools d’hôtes/hôtes de sessions |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86240b0e-9422-4c43-887b-b61143f32ba8",
  "name": "86240b0e-9422-4c43-887b-b61143f32ba8",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-application-group-reader"></a>Lecteur du groupe d’applications de virtualisation de poste de travail

Lecteur du groupe d’applications de virtualisation de poste de travail. [En savoir plus](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Lire des groupes d’applications |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Lire des pools d’hôtes |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Lire des pools d’hôtes/hôtes de sessions |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Obtient ou répertorie les déploiements. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Lire une alerte de métrique classique |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Application Group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "name": "aebf23d0-b568-4e86-b8f9-fe83a2c6ab55",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/applicationgroups/*/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Application Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-contributor"></a>Contributeur de virtualisation des services Bureau

Contributeur de virtualisation de poste de travail [En savoir plus](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/082f0a83-3be5-4ba1-904c-961cca79b387",
  "name": "082f0a83-3be5-4ba1-904c-961cca79b387",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-contributor"></a>Contributeur de pool d’hôtes de virtualisation de poste de travail

Contributeur de pool d’hôtes de virtualisation de poste de travail. [En savoir plus](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "name": "e307426c-f9b6-4e81-87de-d99efb3c32bc",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-host-pool-reader"></a>Lecteur de pool d’hôtes de virtualisation de poste de travail

Lecteur de pool d’hôtes de virtualisation de poste de travail. [En savoir plus](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/*/read |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Lire des pools d’hôtes |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Obtient ou répertorie les déploiements. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Lire une alerte de métrique classique |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Host Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ceadfde2-b300-400a-ab7b-6143895aa822",
  "name": "ceadfde2-b300-400a-ab7b-6143895aa822",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/*/read",
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Host Pool Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-reader"></a>Lecteur de virtualisation des services Bureau

Lecteur de virtualisation de poste de travail [En savoir plus](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/*/read |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Obtient ou répertorie les déploiements. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Lire une alerte de métrique classique |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of Desktop Virtualization.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/49a72310-ab8d-41df-bbb0-79b649203868",
  "name": "49a72310-ab8d-41df-bbb0-79b649203868",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-session-host-operator"></a>Opérateur d’hôte de session de virtualisation de virtualisation de poste de travail

Opérateur d’hôte de session de virtualisation de virtualisation de poste de travail. [En savoir plus](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Lire des pools d’hôtes |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Session Host.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "name": "2ad6aaab-ead9-4eaa-8ac5-da422f562408",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Session Host Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user"></a>Utilisateur de virtualisation de bureau

Permet à l’utilisateur d’utiliser les applications dans un groupe d’applications. [En savoir plus](../virtual-desktop/delegated-access-virtual-desktop.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | *Aucune* |  |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationGroups/useApplications/action | Utiliser ApplicationGroup |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows user to use the applications in an application group.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "name": "1d18fff3-a72a-46b5-b4a9-0b38a3cd7e63",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.DesktopVirtualization/applicationGroups/useApplications/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-user-session-operator"></a>Opérateur de session utilisateur de virtualisation de poste de travail

Opérateur de la session utilisateur de virtualisation de poste de travail. [En savoir plus](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/read | Lire des pools d’hôtes |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/read | Lire des pools d’hôtes/hôtes de sessions |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/hostpools/sessionhosts/usersessions/* |  |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Operator of the Desktop Virtualization Uesr Session.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "name": "ea4bfff8-7fb4-485a-aadd-d4129a0ffaa6",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/hostpools/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
        "Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization User Session Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-contributor"></a>Contributeur d’espace de travail de virtualisation de poste de travail

Contributeur d’espace de travail de virtualisation de poste de travail. [En savoir plus](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/* |  |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Lire des groupes d’applications |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Contributor of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "name": "21efdde3-836f-432b-bf3d-3e8e734d4b2b",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/*",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="desktop-virtualization-workspace-reader"></a>Lecteur d’espace de travail de virtualisation de poste de travail

Lecteur d’espace de travail de virtualisation de poste de travail. [En savoir plus](../virtual-desktop/rbac.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/workspaces/read | Lire des espaces de travail |
> | [Microsoft.DesktopVirtualization](resource-provider-operations.md#microsoftdesktopvirtualization)/applicationgroups/read | Lire des groupes d’applications |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/read | Obtient ou répertorie les déploiements. |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/read | Lire une alerte de métrique classique |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Reader of the Desktop Virtualization Workspace.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "name": "0fa44ee9-7a7d-466b-9bb2-2bf446b1204d",
  "permissions": [
    {
      "actions": [
        "Microsoft.DesktopVirtualization/workspaces/read",
        "Microsoft.DesktopVirtualization/applicationgroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Desktop Virtualization Workspace Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-backup-reader"></a>Lecteur de sauvegarde de disque

Fournit une autorisation sur le coffre de sauvegarde pour effectuer une sauvegarde de disque. [En savoir plus](../backup/disk-backup-faq.yml)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Obtenir les propriétés d’un disque |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | Obtenir l’URI SAP du disque pour l’accès aux objets blob |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk backup.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "name": "3e5e47e6-65f7-47ef-90b5-e5dd4d455f24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/beginGetAccess/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-pool-operator"></a>Opérateur de pool de disques

Accordez l’autorisation au fournisseur de ressources StoragePool pour gérer les disques ajoutés à un pool de disques.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Créer ou mettre à jour un disque |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Obtenir les propriétés d’un disque |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the StoragePool Resource Provider to manage Disks added to a Disk Pool.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/60fc6e62-5479-42d4-8bf4-67625fcc2840",
  "name": "60fc6e62-5479-42d4-8bf4-67625fcc2840",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Pool Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-restore-operator"></a>Opérateur de restauration de disque

Fournit une autorisation sur le coffre de sauvegarde pour effectuer une restauration de disque. [En savoir plus](../backup/restore-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/write | Créer ou mettre à jour un disque |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/read | Obtenir les propriétés d’un disque |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to perform disk restore.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b50d9833-a0cb-478e-945f-707fcc997c13",
  "name": "b50d9833-a0cb-478e-945f-707fcc997c13",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Restore Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="disk-snapshot-contributor"></a>Contributeur d’instantané de disque

Fournit une autorisation sur le coffre de sauvegarde pour gérer les instantanés de disque. [En savoir plus](../backup/backup-managed-disks.md)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/delete | Supprimer une capture instantanée |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/write | Créer ou mettre à jour une capture instantanée |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/read | Obtenir les propriétés d’une capture instantanée |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/beginGetAccess/action | Obtient l’URI SAP de la capture instantanée pour l’accès aux objets blob |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/snapshots/endGetAccess/action | Révoque l’URI SAP de la capture instantanée |
> | [Microsoft.Compute](resource-provider-operations.md#microsoftcompute)/disks/beginGetAccess/action | Obtenir l’URI SAP du disque pour l’accès aux objets blob |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/listkeys/action | Retourne les clés d’accès au compte de stockage spécifié. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/write | Crée un compte de stockage avec les paramètres spécifiés ou met à jour les propriétés ou les balises, ou ajoute un domaine personnalisé pour le compte de stockage spécifié. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/read | Retourne la liste des comptes de stockage ou récupère les propriétés du compte de stockage spécifié. |
> | [Microsoft.Storage](resource-provider-operations.md#microsoftstorage)/storageAccounts/delete | Supprime un compte de stockage existant. |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Provides permission to backup vault to manage disk snapshots.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "name": "7efff54f-a5b4-42b5-a1c5-5411624893ce",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Compute/snapshots/delete",
        "Microsoft.Compute/snapshots/write",
        "Microsoft.Compute/snapshots/read",
        "Microsoft.Compute/snapshots/beginGetAccess/action",
        "Microsoft.Compute/snapshots/endGetAccess/action",
        "Microsoft.Compute/disks/beginGetAccess/action",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/write",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Disk Snapshot Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Contributeur des collections de travaux du planificateur

Permet de gérer des collections de tâches du planificateur, mais pas d’y accéder.

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Insights](resource-provider-operations.md#microsoftinsights)/alertRules/* | Créer et gérer une alerte de métrique classique |
> | [Microsoft.ResourceHealth](resource-provider-operations.md#microsoftresourcehealth)/availabilityStatuses/read | Obtient les états de disponibilité de toutes les ressources dans l’étendue spécifiée. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Scheduler](resource-provider-operations.md#microsoftscheduler)/jobcollections/* | Créer et gérer des collections de travaux |
> | [Microsoft.Support](resource-provider-operations.md#microsoftsupport)/* | Créer et mettre à jour un ticket de support |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="services-hub-operator"></a>Opérateur de hub de services

L’opérateur de hub de services vous permet d’effectuer toutes les opérations de lecture, d’écriture et de suppression liées aux connecteurs de hub de services. [En savoir plus](/services-hub/health/sh-connector-roles)

> [!div class="mx-tableFixed"]
> | Actions | Description |
> | --- | --- |
> | [Microsoft.Authorization](resource-provider-operations.md#microsoftauthorization)/*/read | Lire les rôles et les affectations de rôles |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/subscriptions/resourceGroups/read | Obtient ou répertorie les groupes de ressources. |
> | [Microsoft.Resources](resource-provider-operations.md#microsoftresources)/deployments/* | Créer et gérer un déploiement |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/write | Créer ou mettre à jour un connecteur de hub de services |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/read | Visualiser ou lister des connecteurs de hub de services |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/delete | Supprimer des connecteurs de hub de services |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/connectors/checkAssessmentEntitlement/action | Liste les droits d’évaluation pour un espace de travail de hub de services donné |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/supportOfferingEntitlement/read | Visualiser les droits des offres de support pour un espace de travail de hub de services donné |
> | [Microsoft.ServicesHub](resource-provider-operations.md#microsoftserviceshub)/workspaces/read | Lister les espaces de travail de hub de services pour un utilisateur donné |
> | **NotActions** |  |
> | *Aucune* |  |
> | **DataActions** |  |
> | *Aucune* |  |
> | **NotDataActions** |  |
> | *Aucune* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Services Hub Operator allows you to perform all read, write, and deletion operations related to Services Hub Connectors.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/82200a5b-e217-47a5-b665-6d8765ee745b",
  "name": "82200a5b-e217-47a5-b665-6d8765ee745b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.ServicesHub/connectors/write",
        "Microsoft.ServicesHub/connectors/read",
        "Microsoft.ServicesHub/connectors/delete",
        "Microsoft.ServicesHub/connectors/checkAssessmentEntitlement/action",
        "Microsoft.ServicesHub/supportOfferingEntitlement/read",
        "Microsoft.ServicesHub/workspaces/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Services Hub Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Étapes suivantes

- [Attribuer des rôles Azure à l’aide du portail Azure](role-assignments-portal.md)
- [Rôle personnalisés Azure](custom-roles.md)
- [Autorisations dans Azure Security Center](../security-center/security-center-permissions.md)
