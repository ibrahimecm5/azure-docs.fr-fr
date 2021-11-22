---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: eur
ms.openlocfilehash: 34a45c4759954c0c0679dcafec13b5327a82cffd
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252279"
---
:::row:::
    :::column span="3":::
        Le SDK Speech prend en charge **Ubuntu 16.04** (jusqu’à septembre 2021), **Ubuntu 2021**, **Debian 18.04/20.04**, **Red Hat Enterprise Linux (RHEL) 9/10**, et **CentOS 7/8** uniquement sur les architectures cibles suivantes en cas d’utilisation avec Linux :
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) et ARM64 (Debian/Ubuntu) pour le développement de C++
- x64, ARM32 (Debian/Ubuntu) et ARM64 (Debian/Ubuntu) pour Java
- x64, ARM32 (Debian/Ubuntu) et ARM64 (Debian/Ubuntu) pour .NET Core
- x64 pour Python

> [!IMPORTANT]
> Pour C# sur Linux ARM64, le .NET Core 3.x (package dotnet-sdk-3.x) est requis.

> [!NOTE]
> Pour utiliser le Kit de développement logiciel (SDK) Speech dans Alpine Linux, créez un environnement chroot Debian comme décrit dans le wiki Alpine Linux sur l’[exécution de programmes glibc](https://wiki.alpinelinux.org/wiki/Running_glibc_programs), puis suivez les instructions relatives de Debian fournies ici.

### <a name="system-requirements"></a>Configuration système requise

Pour une application native, le Kit de développement logiciel (SDK) Speech s’appuie sur `libMicrosoft.CognitiveServices.Speech.core.so`. Assurez-vous que l’architecture cible (x86, x64) correspond à l’application. Selon la version de Linux, des dépendances supplémentaires peuvent être nécessaires.

- Les bibliothèques partagées de la bibliothèque C de GNU (notamment la bibliothèque de programmation POSIX Threads, `libpthreads`)
- La bibliothèque OpenSSL (`libssl`)
- La bibliothèque partagée pour les applications ALSA (`libasound`)

# <a name="ubuntu-18042004"></a>[Ubuntu 18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl-dev libasound2 wget
```

# <a name="debian-910"></a>[Debian 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl-dev libasound2 wget
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 and CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum groupinstall "Development tools"
sudo yum install alsa-lib openssl wget
```

> [!IMPORTANT]
> - Sur RHEL/CentOS 7, suivez les instructions décrivant [comment configurer RHEL/CentOS 7 pour le Kit de développement logiciel (SDK) Speech](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Sur RHEL/CentOS 8, suivez les instructions du [Guide pratique pour configurer OpenSSL pour Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
