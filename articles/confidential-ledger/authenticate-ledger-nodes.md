---
title: Authentification de nœuds Registre confidentiel Azure
description: Authentification de nœuds Registre confidentiel Azure
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 20e5c46ea174807efea21cf4812eef47ee8c0257
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450093"
---
# <a name="authenticating-azure-confidential-ledger-nodes"></a>Authentification de nœuds Registre confidentiel Azure

Les nœuds Registre confidentiel Azure peuvent être authentifiés par des exemples de code et par des utilisateurs.

## <a name="code-samples"></a>Exemples de code

Lors de l’initialisation, les exemples de code obtiennent le certificat de nœud en interrogeant le service d’identité. Une fois le certificat de nœud récupéré, un exemple de code interroge le réseau du Registre pour obtenir une citation qui est ensuite validée à l’aide des binaires de vérification de l’hôte. Si la vérification est réussie, l’exemple de code passe aux opérations du Registre.

## <a name="users"></a>Utilisateurs

Les utilisateurs peuvent valider l’authenticité des nœuds Registre confidentiel Azure pour vérifier qu’ils s’interfacent avec l’enclave de leur registre. La confiance dans les nœuds Registre confidentiel peut être renforcée de plusieurs façons, qui peuvent être superposées les unes aux autres pour augmenter le niveau de confiance total. En tant que telles, les étapes 1 et 2 permettent d’instaurer la confiance dans cette enclave de Registre confidentiel Azure dans le cadre de l’établissement d’une liaison TLS initiale et de l’authentification dans les flux de travail fonctionnels. Au-delà, une connexion client permanente est maintenue entre le client de l’utilisateur et le registre confidentiel.

- **Validation d’un nœud Registre confidentiel** : cette opération s’effectue en interrogeant le service d’identité hébergé par Microsoft, qui fournit un certificat réseau et permet ainsi de vérifier que le nœud de registre présente un certificat approuvé/signé par le certificat réseau pour cette instance spécifique. À l’instar du protocole HTTPS basé sur PKI, le certificat d’un serveur est signé par une autorité de certification ou une autorité de certification intermédiaire connue. Dans le cas d’un registre confidentiel Azure, le certificat de l’autorité de certification est renvoyé par un service d’identité sous la forme d’un certificat réseau. Il s’agit d’une mesure importante de création de confiance pour les utilisateurs de Registre confidentiel. Si ce certificat de nœud n’est pas signé par le certificat réseau retourné, la connexion client (telle qu’implémentée dans l’exemple de code) devrait échouer.
- **Validation d’une enclave de Registre confidentiel** : un registre confidentiel s’exécute dans une enclave Intel® SGX représentée par un Quote, un blob de données généré à l’intérieur de cette enclave. Elle peut être utilisée par n’importe quelle autre entité pour vérifier que la citation a été générée à partir d’une application s’exécutant avec des protections Intel® SGX. La citation est structurée de manière à permettre une vérification facile. Elle contient des revendications qui permettent d’identifier différentes propriétés de l’enclave et de l’application en cours d’exécution. Il s’agit d’un mécanisme important de création de confiance pour les utilisateurs du registre confidentiel. Il est possible de le mettre en œuvre en appelant une API de flux de travail fonctionnel pour obtenir une citation d’enclave. La connexion client devrait échouer si la citation n’est pas valide. La citation récupérée peut ensuite être validée avec l’outil open_enclaves Host_Verify. Pour plus d’informations à ce sujet, cliquez ici.

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de Registre confidentiel Microsoft Azure](overview.md)
- [Architecture de Registre confidentiel Azure](architecture.md)
