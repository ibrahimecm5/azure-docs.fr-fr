---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 04/28/2021
ms.openlocfilehash: 4957bd3a4b43413a7377e0c9eb019a3bfe4ae105
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132529814"
---
Si vous développez pour des *appareils avec contraintes*, vous pouvez utiliser IoT Plug-and-Play avec :

- Le [SDK Azure pour les bibliothèques clientes IoT Embedded C](https://aka.ms/embeddedcsdk).
- [Azure RTOS](/azure/rtos/overview-rtos).

Cet article contient des liens et des ressources pour ces scénarios limités.

## <a name="prerequisites"></a>Prérequis

La plupart des exemples ci-dessous nécessitent un appareil matériel spécifique et les prérequis sont différents pour chacun de ces exemples. Suivez le lien vers l’exemple approprié pour obtenir des informations détaillées sur les prérequis, la configuration et les instructions de génération.

## <a name="use-the-sdk-for-embedded-c"></a>Utiliser le kit de développement logiciel (SDK) pour Embedded C

Le kit de développement logiciel (SDK) pour Embedded C offre une solution légère pour connecter des appareils limités aux services Azure IoT, y compris à l’aide des [conventions d’IoT Plug-and-Play](../articles/iot-develop/concepts-convention.md). Les liens suivants incluent des exemples d’appareil basé sur une MCU, à titre éducatif et à des fins de débogage.

### <a name="use-an-mcu-based-device"></a>Utiliser un appareil basé sur une MCU

Pour obtenir un tutoriel complet de bout en bout sur l’utilisation du SDK pour Embedded C, le service Device Provisioning et IoT Plug-and-Play sur une MCU, consultez [Reprogrammer PIC-IoT WX Development Board pour se connecter à Azure via le service IoT Hub Device Provisioning](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx).

### <a name="introductory-samples"></a>Exemple d’introduction

Le référentiel du kit de développement logiciel (SDK) pour Embedded C contient [plusieurs exemples](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) qui vous montrent comment utiliser IoT Plug-and-Play :

> [!NOTE]
> L’exécution de ces exemples est présentée sur Windows et Linux à des fins éducatives et de débogage. Dans un scénario de production, les exemples sont destinés aux appareils limités uniquement.

- [Exemple de thermostat avec le kit de développement logiciel (SDK) pour Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/main/sdk/samples/iot/paho_iot_pnp_sample.c)

- [Exemple de contrôleur de température avec le kit de développement logiciel (SDK) pour Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/main/sdk/samples/iot/paho_iot_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Utilisation d’Azure RTOS

Azure RTOS inclut une couche légère qui ajoute une connectivité native aux services cloud Azure IoT. Cette couche offre un mécanisme simple pour connecter des appareils limités à Azure IoT tout en utilisant les fonctionnalités avancées d’Azure RTOS. Pour en savoir plus, consultez [Qu’est-ce que Microsoft Azure RTOS](/azure/rtos/overview-rtos).

### <a name="toolchains"></a>Chaînes d’outils

Les exemples Azure RTOS sont proposés avec différents types de combinaisons d’IDE et de chaîne d’outils, comme notamment :

- IAR : IDE [Embedded Workbench](https://www.iar.com/iar-embedded-workbench/) d’IAR
- GCC/CMake : Générer sur le système de build [CMake](https://cmake.org/) open source et la [chaîne d’outils GNU pour processeurs Arm embarqués](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- MCUExpresso : [IDE MCUXpresso](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE) de NXP
- STM32Cube : [IDE STM32Cube](https://www.st.com/en/development-tools/stm32cubeide.html) de STMicroelectronics
- MPLAB : [IDE MPLAB X](https://www.microchip.com/mplab/mplab-x-ide) de Microchip

### <a name="samples"></a>Exemples

Le tableau suivant liste des exemples qui vous montrent comment démarrer sur différents appareils avec Azure RTOS et IoT Plug-and-Play :

Fabricant | Appareil | Exemples |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_ATSAME54-XPRO_IAR_Samples_2021_11_03.zip) • [MPLAB](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_ATSAME54-XPRO_MPLab_Samples_2021_11_03.zip)
| MXCHIP | [AZ3166](../articles/iot-develop/quickstart-devkit-mxchip-az3166.md) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_MIMXRT1060_IAR_Samples_2021_11_03.zip) • [MCUXpresso](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_MIMXRT1060_MCUXpresso_Samples_2021_11_03.zip)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_STM32F746G-DISCO_IAR_Samples_2021_11_03.zip) • [STM32Cube](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_STM32F746G-DISCO_STM32CubeIDE_Samples_2021_11_03.zip)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_STM32L4+-DISCO_IAR_Samples_2021_11_03.zip) • [STM32Cube](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_STM32L4+-DISCO_STM32CubeIDE_Samples_2021_11_03.zip)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_STM32L4+-DISCO_IAR_Samples_2021_11_03.zip) • [STM32Cube](https://github.com/azure-rtos/samples/releases/download/v6.1_rel/Azure_RTOS_6.1_STM32L4+-DISCO_STM32CubeIDE_Samples_2021_11_03.zip)
