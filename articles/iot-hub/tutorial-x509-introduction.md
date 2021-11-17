---
title: Tutoriel - Comprendre le chiffrement et les certificats X.509 pour Azure IoT Hub | Microsoft Docs
description: Tutoriel - Comprendre le chiffrement et l’infrastructure à clé publique X.509 pour Azure IoT Hub
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/25/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: e79d367cf93354506198d2d317c759b714c55d7f
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555695"
---
# <a name="tutorial-understanding-public-key-cryptography-and-x509-public-key-infrastructure"></a>Tutoriel : Comprendre le chiffrement à clé publique et l’infrastructure à clé publique X.509

Vous pouvez utiliser des certificats X.509 pour authentifier des appareils auprès d’un hub Azure IoT. Un certificat est un document numérique qui contient la clé publique de l’appareil et qui peut être utilisé pour vérifier que l’appareil est ce qu’il prétend être. Les certificats X.509 et les listes de révocation de certificats sont documentés par la spécification [RFC 5280](https://tools.ietf.org/html/rfc5280). Les certificats ne sont qu’une partie d’une infrastructure à clé publique (PKI) X.509. Pour comprendre l’infrastructure à clé publique X.509, vous devez comprendre les algorithmes de chiffrement, les clés de chiffrement, les certificats et les autorités de certification :

* Les **algorithmes** définissent la façon dont les données de texte en clair d’origine sont transformées en texte chiffré, puis en texte en clair.
* Les **clés** sont des chaînes de données aléatoires ou pseudo-aléatoires utilisées comme entrée d’un algorithme.
* Les **certificats** sont des documents numériques qui contiennent la clé publique d’une entité et vous permettent de déterminer si le sujet du certificat est qui ou ce qu’il prétend être.
* Les **autorités de certification** attestent l’authenticité des sujets du certificat.

Vous pouvez acheter un certificat auprès d’une autorité de certification. Vous pouvez également, à des fins de test et de développement ou si vous travaillez dans un environnement autonome, créer une autorité de certification racine auto-signée. Si, par exemple, vous avez un ou plusieurs appareils et que vous testez l’authentification auprès d’un hub IoT, vous pouvez signer automatiquement votre autorité de certification racine et l’utiliser pour émettre des certificats d’appareil. Vous pouvez également émettre des certificats d’appareil auto-signés. Ce sujet est abordé dans les articles suivants.

Avant de nous pencher plus en détail sur les certificats X.509 et de les utiliser pour authentifier des appareils auprès d’un hub IoT, nous allons aborder le chiffrement sur lequel reposent les certificats.

## <a name="cryptography"></a>Chiffrement

Le chiffrement est utilisé pour protéger les informations et les communications. Cela consiste généralement à utiliser des techniques de chiffrement pour brouiller du texte en clair (texte ordinaire) en texte chiffré (texte encodé), puis faire l’opération inverse. Ce processus de brouillage est appelé chiffrement. Le processus inverse est appelé déchiffrement. Le chiffrement poursuit les objectifs suivants :

* **Confidentialité** : les informations peuvent être comprises uniquement par le public visé.
* **Intégrité** : les informations ne peuvent pas être modifiées dans le stockage ou en transit.
* **Non-répudiation** : le créateur d’informations ne peut pas refuser ultérieurement cette création.
* **Authentification** : l’expéditeur et le destinataire peuvent chacun confirmer l’identité de l’autre.

## <a name="encryption"></a>Chiffrement

Le processus de chiffrement nécessite un algorithme et une clé. L’algorithme définit la manière dont les données sont transformées de texte en clair en texte chiffré, puis de nouveau en texte en clair. Une clé est une chaîne aléatoire de données utilisée comme entrée de l’algorithme. Toute la sécurité du processus est contenue dans la clé. La clé doit donc être stockée de manière sécurisée. Les détails des algorithmes les plus connus, toutefois, sont disponibles publiquement.

Il existe deux types de chiffrement. Le chiffrement symétrique utilise la même clé pour le chiffrement et le déchiffrement. Le chiffrement asymétrique utilise des clés différentes mais mathématiquement associées pour effectuer le chiffrement et le déchiffrement.

### <a name="symmetric-encryption"></a>Chiffrement symétrique

Le chiffrement symétrique utilise la même clé pour chiffrer le texte en clair en texte chiffré et déchiffrer le texte chiffré en texte en clair. La longueur nécessaire de la clé, exprimée en nombre de bits, est déterminée par l’algorithme. Une fois que la clé a été utilisée pour chiffrer le texte en clair, le message chiffré est envoyé au destinataire qui déchiffre ensuite le texte chiffré. La clé symétrique doit être transmise de manière sécurisée au destinataire. L’envoi de la clé constitue le risque le plus élevé en matière de sécurité lors de l’utilisation d’un algorithme symétrique.

![Exemple de chiffrement symétrique](media/tutorial-x509-introduction/symmetric-keys.png)

### <a name="asymmetric-encryption"></a>Chiffrement asymétrique

Si seul le chiffrement symétrique est utilisé, le problème est que toutes les parties prenantes de la communication doivent posséder la clé privée. Toutefois, il est possible que des tiers non autorisés capturent la clé pendant la transmission aux utilisateurs autorisés. Pour résoudre ce problème, utilisez le chiffrement asymétrique ou à clé publique à la place.

Dans le chiffrement asymétrique, chaque utilisateur a deux clés mathématiquement associées, appelées « paire de clés ». L’une des clés est publique, l’autre privée. La paire de clés garantit que seul le destinataire a accès à la clé privée nécessaire pour déchiffrer les données. L’illustration suivante récapitule le processus de chiffrement asymétrique.

![Exemple de chiffrement asymétrique](media/tutorial-x509-introduction/asymmetric-keys.png)

1. Le destinataire crée une paire de clés publique/privée et envoie la clé publique à une autorité de certification. L’autorité de certification empaquette la clé publique dans un certificat X.509.

1. La partie expéditrice obtient la clé publique du destinataire auprès de l’autorité de certification.

1. L’expéditeur chiffre les données en texte en clair à l’aide d’un algorithme de chiffrement. La clé publique du destinataire est utilisée pour effectuer le chiffrement.

1. L’expéditeur transmet le texte chiffré au destinataire. Il n’est pas nécessaire d’envoyer la clé, car le destinataire a déjà la clé privée nécessaire pour déchiffrer le texte chiffré.

1. Le destinataire déchiffre le texte chiffré à l’aide de l’algorithme asymétrique spécifié et de la clé privée.

### <a name="combining-symmetric-and-asymmetric-encryption"></a>Combinaison du chiffrement symétrique et du chiffrement asymétrique

Vous pouvez combiner le chiffrement symétrique et le chiffrement asymétrique pour tirer parti de leurs forces respectives. Le chiffrement symétrique est beaucoup plus rapide que le chiffrement asymétrique, mais en raison de la nécessité d’envoyer des clés privées aux autres parties, il n’est pas aussi sécurisé. Afin de combiner les deux types, vous pouvez utiliser le chiffrement symétrique pour convertir du texte en clair en texte chiffré. Le chiffrement asymétrique est utilisé pour échanger la clé symétrique. C’est ce qu’illustre le diagramme suivant.

![Chiffrement symétrique et chiffrement asymétrique](media/tutorial-x509-introduction/symmetric-asymmetric-encryption.png)

1. L’expéditeur récupère la clé publique du destinataire.

1. L’expéditeur génère une clé symétrique et l’utilise pour chiffrer les données d’origine.

1. L’expéditeur utilise la clé publique du destinataire pour chiffrer la clé symétrique.

1. L’expéditeur transmet la clé symétrique chiffrée et le texte chiffré au destinataire prévu.

1. Le destinataire utilise la clé privée qui correspond à la clé publique du destinataire pour déchiffrer la clé symétrique de l’expéditeur.

1. Le destinataire utilise la clé symétrique pour déchiffrer le texte chiffré.

### <a name="asymmetric-signing"></a>Signature asymétrique

Les algorithmes asymétriques peuvent être utilisés pour protéger les données contre toute modification et pour prouver l’identité du créateur des données. L’illustration suivante montre comment la signature asymétrique aide à prouver l’identité de l’expéditeur.

![Exemple de signature asymétrique](media/tutorial-x509-introduction/asymmetric-signing.png)

1. L’expéditeur transmet des données en texte en clair via un algorithme de chiffrement asymétrique, en utilisant la clé privée pour le chiffrement. Notez que ce scénario inverse l’utilisation des clés privée et publique décrite dans la section précédente qui détaillait le chiffrement asymétrique.

1. Le texte chiffré qui en résulte est envoyé au destinataire.

1. Le destinataire obtient la clé publique de l’expéditeur auprès d’un annuaire.

1. Le destinataire déchiffre le texte chiffré en utilisant la clé publique de l’expéditeur. Le texte en clair résultant prouve l’identité de l’expéditeur, car seul ce dernier a accès à la clé privée qui a initialement chiffré le texte d’origine.

## <a name="signing"></a>Signature

La signature numérique peut être utilisée pour déterminer si les données ont été modifiées en transit ou au repos. Les données sont transmises via un algorithme de hachage, fonction unidirectionnelle qui produit un résultat mathématique à partir du message donné. Le résultat est appelé *valeur de hachage*, *code de hachage*, *synthèse du message*, *signature* ou *empreinte*. Une valeur de hachage ne peut pas être inversée pour obtenir le message d’origine. Une petite modification du message entraînant une modification significative de l’*empreinte*, la valeur de hachage peut être utilisée pour déterminer si un message a été modifié. L’illustration suivante montre comment les algorithmes de hachage et de chiffrement asymétrique peuvent être utilisés pour vérifier qu’un message n’a pas été modifié.

![Exemple de signature](media/tutorial-x509-introduction/signing.png)

1. L’expéditeur crée un message en texte en clair.

1. L’expéditeur hache le message en texte en clair pour créer un code de hachage.

1. L’expéditeur chiffre le code de hachage avec une clé privée.

1. L’expéditeur transmet le message en texte en clair et le code de hachage chiffré au destinataire prévu.

1. Le destinataire déchiffre le code de hachage avec la clé publique de l’expéditeur.

1. Le destinataire exécute le même algorithme de hachage que celui utilisé par l’expéditeur sur le message.

1. Le destinataire compare la signature obtenue à la signature déchiffrée. Si les codes de hachage sont identiques, le message n’a pas été modifié au cours de la transmission.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les champs qui composent un certificat, consultez [Présentation des certificats à clé publique X.509](tutorial-x509-certificates.md).

Si vous connaissez déjà de nombreux certificats X.509 et que vous souhaitez générer des versions de test en vue de les utiliser pour vous authentifier auprès de votre hub IoT, consultez les rubriques suivantes :

* [Utilisation de scripts fournis par Microsoft pour créer des certificats de test](tutorial-x509-scripts.md)
* [Utilisation d’OpenSSL pour créer des certificats de test](tutorial-x509-openssl.md)
* [Utilisation d’OpenSSL pour créer des certificats de test auto-signés](tutorial-x509-self-sign.md)

Si vous avez un certificat d’autorité de certification ou un certificat d’autorité de certification secondaire et que vous souhaitez le charger sur votre hub IoT et prouver que vous en êtes le propriétaire, consultez [Prouver la possession d’un certificat d’autorité de certification](tutorial-x509-prove-possession.md).
