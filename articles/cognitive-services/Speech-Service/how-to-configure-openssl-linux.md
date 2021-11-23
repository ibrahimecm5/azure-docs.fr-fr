---
title: Guide pratique pour configurer OpenSSL pour Linux
titleSuffix: Azure Cognitive Services
description: Découvrez comment configurer OpenSSL pour Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
zone_pivot_groups: programming-languages-set-two
ROBOTS: NOINDEX
ms.openlocfilehash: ec2bd2cb46ff96602ed39cf3c9c4e41ddcf5ab33
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132155986"
---
# <a name="configure-openssl-for-linux"></a>Configurer OpenSSL pour Linux

Avec le kit SDK Speech dans la version 1.19.0 ou ultérieure, [OpenSSL](https://www.openssl.org) est configuré de façon dynamique en fonction de la version du système hôte. Dans les versions précédentes, OpenSSL était lié de manière statique à la bibliothèque principale du kit de développement logiciel (SDK).

Pour garantir la connectivité, vérifiez que les certificats OpenSSL ont été installés sur votre système. Exécutez une commande :
```bash
openssl version -d
```

Sur les systèmes basés sur Ubuntu/Debian, la sortie doit être la suivante :
```
OPENSSLDIR: "/usr/lib/ssl"
```

Vérifiez s'il existe un sous-répertoire `certs` sous OPENSSLDIR. Dans l'exemple ci-dessus, il s'agirait de `/usr/lib/ssl/certs`.

* S'il existe un sous-répertoire `/usr/lib/ssl/certs` et qu'il contient de nombreux fichiers de certificats individuels (avec l'extension `.crt` ou `.pem`), aucune action supplémentaire n'est nécessaire.

* Si OPENSSLDIR est différent de `/usr/lib/ssl` et/ou en présence d'un seul fichier de groupement de certificats plutôt que de plusieurs fichiers individuels, vous devez définir une variable d'environnement SSL appropriée pour indiquer l'emplacement des certificats.

## <a name="examples"></a>Exemples

- OPENSSLDIR est `/opt/ssl`. Il y a un sous-répertoire `certs` contenant de nombreux fichiers `.crt` ou `.pem`.
Définissez la variable d'environnement `SSL_CERT_DIR` pour qu'elle pointe sur `/opt/ssl/certs` avant d'exécuter un programme qui utilise le kit de développement logiciel (SDK) Speech. Par exemple :
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR a la valeur `/etc/pki/tls` (par exemple sur les systèmes RHEL/CentOS). Il y a un sous-répertoire `certs` contenant un fichier de bundle de certificats, par exemple `ca-bundle.crt`.
Définissez la variable d’environnement `SSL_CERT_FILE` pour qu’elle pointe vers ce fichier avant d’exécuter un programme qui utilise le SDK Speech. Par exemple :
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>Vérifications de la révocation de certificats
Lors de la connexion au service Speech, le kit de développement logiciel (SDK) Speech vérifie que le certificat TLS utilisé par le service Speech n’a pas été révoqué. Pour effectuer cette vérification, le kit de développement logiciel (SDK) Speech doit avoir accès aux points de distribution de la liste de révocation de certificats pour les autorités de certification utilisées par Azure. Vous trouverez une liste des éventuels emplacements de téléchargement de la liste de révocation de certificats dans [ce document](../../security/fundamentals/tls-certificate-changes.md). Si un certificat a été révoqué ou si la liste de révocation de certificats ne peut pas être téléchargée, le kit de développement logiciel (SDK) Speech interrompt la connexion et déclenche l’événement annulé.

Si le réseau à partir duquel est utilisé le kit de développement logiciel (SDK) Speech est configuré de façon à ne pas autoriser l’accès aux emplacements de téléchargement de la liste de révocation de certificats, la vérification de la liste de révocation des certificats peut être désactivée ou définie de manière à ne pas échouer si la liste de révocation des certificats ne peut pas être récupérée. Cette configuration s’effectue via l’objet de configuration utilisé pour créer un objet de module de reconnaissance.

Pour poursuivre la connexion lorsqu’une liste de révocation de certificats ne peut pas être récupérée, définissez la propriété OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
Quand la valeur est « true », une tentative de récupération de la liste de révocation des certificats est effectuée et, si la récupération réussit, le certificat est vérifié pour la révocation. Si la récupération échoue, la connexion est autorisée à se poursuivre.

Pour désactiver complètement les vérifications de révocation de certificats, affectez la valeur « true » à la propriété OPENSSL_DISABLE_CRL_CHECK.
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> Il est également à noter que certaines distributions de Linux n’ont pas de variable d’environnement TMP ou TMPDIR définie. Le Kit de développement logiciel (SDK) Speech télécharge la liste de révocation de certificat (CRL) à chaque fois, au lieu de la mettre en cache sur le disque pour la réutiliser jusqu’à son expiration. Pour améliorer les performances de la connexion initiale, vous pouvez [créer une variable d’environnement nommée TMPDIR et la définir sur le chemin d’accès du répertoire temporaire que vous avez choisi](https://help.ubuntu.com/community/EnvironmentVariables).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [À propos du kit SDK Speech](speech-sdk.md)
