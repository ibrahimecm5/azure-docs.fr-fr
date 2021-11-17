---
title: Création d’un certificat client à l’aide de Registre confidentiel Microsoft Azure
description: Création d’un certificat client à l’aide de Registre confidentiel Microsoft Azure
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 216a626bfaaf86a8c2237b5d871ab7895d921127
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476392"
---
# <a name="creating-a-client-certificate"></a>Création d’un certificat client

Les API de Registre confidentiel Azure requièrent l’authentification par certificat client. Seuls les certificats ajoutés à une liste d’autorisation lors de la création ou de la mise à jour du registre peuvent être utilisés pour appeler les API fonctionnelles de Registre confidentiel.

Vous aurez besoin d’un certificat au format PEM. Vous pouvez créer plusieurs certificats et les ajouter ou les supprimer à l’aide de l’API de mise à jour du registre.

## <a name="openssl"></a>OpenSSL

Nous vous recommandons d’utiliser OpenSSL pour générer les certificats. Si git est installé, vous pouvez exécuter OpenSSL dans le shell git. Dans le cas contraire, vous pouvez installer OpenSSL pour votre système d’exploitation.

- **Windows** : Installez [chocolatey pour Windows](https://chocolatey.org/install), ouvrez une fenêtre de terminal PowerShell en mode administrateur, puis exécutez `choco install openssl`. Vous pouvez également installer OpenSSL pour Windows directement à partir d’[ici](http://gnuwin32.sourceforge.net/packages/openssl.htm).
- **Linux** : Exécutez `sudo apt-get install openssl`

Vous pouvez ensuite générer un certificat en exécutant `openssl` dans une fenêtre de terminal Bash ou PowerShell :

```bash
openssl ecparam -out "privkey_name.pem" -name "secp384r1" -genkey
openssl req -new -key "privkey_name.pem" -x509 -nodes -days 365 -out "cert.pem" -"sha384" -subj=/CN="ACL Client Cert"
```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de Registre confidentiel Microsoft Azure](overview.md)