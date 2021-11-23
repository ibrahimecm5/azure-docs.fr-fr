---
title: Configurer RHEL/CentOS 7 - Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez comment configurer RHEL/CentOS 7 de manière à utiliser le kit de développement logiciel (SDK) Speech.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: 8c7b9a6f8bb74bd8c66eac976bf9d17a3fd798d5
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132155966"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Configurer RHEL/CentOS 7 pour le kit de développement logiciel (SDK) Speech

Pour utiliser le kit de développement logiciel (SDK) Speech pour le développement C++ sur Red Hat Enterprise Linux (RHEL) 8 x64 et CentOS 8 x64, mettez à jour le compilateur C++ et la bibliothèque runtime C++ partagée sur votre système.

### <a name="1-general-setup"></a>1. Configuration générale

Commencez par installer toutes les dépendances générales :

```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

### <a name="2-cc-compiler-and-runtime-libraries"></a>2. Compilateur C/C++ et bibliothèques du runtime

Installez les packages requis à l’aide de la commande suivante :

```bash
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
```

Ensuite, mettez à jour le compilateur et les bibliothèques du runtime :

```bash
# Build GCC 7.5.0 and runtimes and install them under /usr/local
curl https://ftp.gnu.org/gnu/gcc/gcc-7.5.0/gcc-7.5.0.tar.bz2 -O
tar jxf gcc-7.5.0.tar.bz2
mkdir gcc-7.5.0-build && cd gcc-7.5.0-build
../gcc-7.5.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip
```

Si le compilateur et les bibliothèques mis à jour doivent être déployés sur plusieurs machines, vous pouvez simplement les copier à partir de `/usr/local` vers d’autres machines. Si seules les bibliothèques du runtime sont nécessaires, les fichiers figurant dans `/usr/local/lib64` seront suffisants.

### <a name="3-environment-settings"></a>3. Paramètres d'environnement

Exécutez les commandes suivantes pour achever la configuration :

```bash
# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-<version>/lib/x64:$LD_LIBRARY_PATH

```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [À propos du kit SDK Speech](speech-sdk.md)
