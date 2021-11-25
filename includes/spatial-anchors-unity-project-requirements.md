---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/12/2021
ms.author: parkerra
ms.openlocfilehash: 81dffe5fd5e5788538a0d263f305186933ee2574
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490488"
---
Lorsque vous démarrez un nouveau projet Unity, vous pouvez choisir entre l'[infrastructure de plug-in Unity XR](https://docs.unity3d.com/Manual/XRPluginArchitecture.html) et la XR intégrée existante.

### <a name="xr-plug-in-framework"></a>[Infrastructure de plug-in XR](#tab/xr-plugin-framework)

L’infrastructure de plug-in XR est uniquement prise en charge dans le kit de développement logiciel (SDK) ASA version 2.9.0 ou ultérieure. Pour cibler l’infrastructure de plug-in XR, utilisez Unity [2020.3 (LTS)](https://unity3d.com/unity/whats-new/2020.3.0)et AR Foundation [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@4.1/manual/index.html) avec les packages suivants, en fonction de votre plateforme :
> [!IMPORTANT]
> Le plug-in OpenXR de réalité mixte est uniquement pris en charge sur le SDK ASA version 2.10.0 ou ultérieure
- Plug-in OpenXR de la réalité mixte : [1.1.2](/windows/mixed-reality/develop/unity/xr-project-setup?tabs=openxr)
- Plug-in XR Windows : [4.5.0](https://docs.unity3d.com/Packages/com.unity.xr.windowsmr@4.5/manual/index.html)
- Plug-in XR ARCore : [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arcore@4.1/manual/index.html)
- Plug-in XR ARKit : [4.1.7](https://docs.unity3d.com/Packages/com.unity.xr.arkit@4.1/manual/index.html)

### <a name="legacy-built-in-xr"></a>[XR intégré hérité](#tab/legacy-built-in-xr)

Le XR intégré hérité est pris en charge uniquement sur le SDK ASA version 2.8.1 ou antérieure. Pour cibler le XR intégré hérité, utilisez Unity [2019.4 (LTS)](https://unity.com/releases/2019-lts)et AR Foundation [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arfoundation@3.1/manual/index.html) avec les packages suivants, en fonction de votre plateforme :
- Windows Mixed Reality : [4.2.1](https://docs.unity3d.com/Packages/com.unity.xr.windowsmr.metro@4.2/manual/index.html)
- Plug-in XR ARCore : [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arcore@3.1/manual/index.html)
- Plug-in XR ARKit : [3.1.3](https://docs.unity3d.com/Packages/com.unity.xr.arkit@3.1/manual/index.html)