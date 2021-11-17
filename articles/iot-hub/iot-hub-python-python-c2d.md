---
title: Messages cloud-à-appareil avec Azure IoT Hub (Python) | Microsoft Docs
description: Envoi de messages cloud-à-appareil vers un appareil depuis un Azure IoT Hub à l’aide des kits de développement logiciel Azure IoT pour Python. Vous modifiez une application d’appareil simulé pour recevoir des messages cloud-à-appareil et modifiez une application principale pour envoyer des messages cloud-à-appareil.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: lizross
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: de60d0c92ee44cf2e58d11b2e28d9c2dfccdc0b1
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551593"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Envoi de messages cloud-à-appareil avec IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub est un service entièrement géré qui permet d’autoriser des communications bidirectionnelles fiables et sécurisées entre des millions d’appareils et un serveur principal de solution. Le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) explique comment créer un hub IoT, y provisionner une identité d’appareil et coder une application d’appareil simulé qui envoie des messages appareil-à-cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ce tutoriel est basé sur [Envoyer des données de télémétrie d’un appareil à un hub IoT](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python). Cette rubrique vous explique les procédures suivantes :

* À partir du serveur principal de votre application, envoyez des messages cloud-à-appareil vers un appareil unique via IoT Hub.

* Recevez des messages cloud-à-appareil sur un appareil.

Vous trouverez des informations supplémentaires sur les messages cloud-à-appareil dans le [Guide du développeur IoT Hub](iot-hub-devguide-messaging.md).

À la fin de ce didacticiel, vous exécuterez deux applications console Python :

* **SimulatedDevice.py**, une version modifiée de l’application créée dans [Envoyer des données de télémétrie d’un appareil à un hub IoT](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python), qui se connecte à votre hub IoT et reçoit des messages cloud-à-appareil.

* **SendCloudToDeviceMessage.py**, qui envoie des messages cloud-à-appareil à l'application d'appareil simulé par le biais d'IoT Hub.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Vérifiez que le port 8883 est ouvert dans votre pare-feu. L’exemple d’appareil décrit dans cet article utilise le protocole MQTT, qui communique via le port 8883. Ce port peut être bloqué dans certains environnements réseau professionnels et scolaires. Pour plus d'informations sur les différentes façons de contourner ce problème, consultez [Se connecter à IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Recevoir des messages dans l’application d’appareil simulé

Dans cette section, vous créez une application de console Python pour simuler l’appareil et recevoir des messages cloud-à-appareil à partir de l’IoT Hub.

1. À partir d’une invite de commandes dans votre répertoire de travail, installez le **Kit SDK Azure IoT Hub Device pour Python** :

    ```cmd/sh
    pip install azure-iot-device
    ```

1. À l'aide d'un éditeur de texte, créez un fichier nommé **SimulatedDevice.py**.

1. Ajoutez les instructions et variables `import` ci-dessous au début du fichier **SimulatedDevice.py** :

    ```python
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Ajoutez le code suivant au fichier **SimulatedDevice.py**. Remplacez la valeur de l'espace réservé `{deviceConnectionString}` par la chaîne de connexion de l'appareil créée dans le guide de démarrage rapide [Envoyer des données de télémétrie d'un appareil à un hub IoT](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) :

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Définissez la fonction suivante qui sera utilisée pour imprimer les messages reçus sur la console :

    ```python
    def message_handler(message):
        global RECEIVED_MESSAGES
        RECEIVED_MESSAGES += 1
        print("")
        print("Message received:")

        # print data from both system and application (custom) properties
        for property in vars(message).items():
            print ("    {}".format(property))

        print("Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

1. Ajoutez le code suivant pour initialiser le client et attendre de recevoir le message cloud-à-appareil :

    ```python
    def main():
        print ("Starting the Python IoT Hub C2D Messaging device sample...")

        # Instantiate the client
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

        print ("Waiting for C2D messages, press Ctrl-C to exit")
        try:
            # Attach the handler to the client
            client.on_message_received = message_handler

            while True:
                time.sleep(1000)
        except KeyboardInterrupt:
            print("IoT Hub C2D Messaging device sample stopped")
        finally:
            # Graceful exit
            print("Shutting down IoT Hub Client")
            client.shutdown()
    ```

1. Ajoutez la fonction principale suivante :

    ```python
    if __name__ == '__main__':
        main()
    ```

1. Enregistrez et fermez le fichier **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Obtenir la chaîne de connexion du hub IoT

Dans cet article, vous créez un service back-end pour envoyer des messages cloud-à-appareil via le hub IoT que vous avez créé dans [Envoyer des données de télémétrie d'un appareil vers un hub IoT](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python). Pour envoyer des messages cloud-à-appareil, votre service a besoin de l'autorisation de **connexion de service**. Par défaut, chaque IoT Hub est créé avec une stratégie d’accès partagé nommée **service** qui accorde cette autorisation.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Envoi d’un message cloud vers appareil

Dans cette section, vous créez une application de console Python qui envoie des messages cloud-à-appareil à l’application de l’appareil simulé. Vous avez besoin de l’ID de l’appareil que vous avez ajouté dans le guide de démarrage rapide [Envoyer des données de télémétrie d’un appareil à un hub IoT](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python). Vous avez également besoin de la chaîne de connexion du hub IoT que vous avez précédemment copiée dans [Obtenir la chaîne de connexion du hub IoT](#get-the-iot-hub-connection-string).

1. Dans votre répertoire de travail, ouvrez une invite de commandes et installez le **Kit de développement logiciel (SDK) Azure IoT Hub Service pour Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. À l'aide d'un éditeur de texte, créez un fichier nommé **SendCloudToDeviceMessage.py**.

1. Ajoutez les instructions et variables `import` ci-dessous au début du fichier **SendCloudToDeviceMessage.py** :

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Ajoutez le code suivant au fichier **SendCloudToDeviceMessage.py**. Remplacez les valeurs d'espace réservé `{iot hub connection string}` et `{device id}` par la chaîne de connexion du hub IoT et l'identité d'appareil que vous avez notées précédemment :

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Ajoutez le code suivant pour envoyer des messages à votre appareil :

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Ajoutez la fonction principale suivante :

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Enregistrez et fermez le fichier **SendCloudToDeviceMessage.py**.

## <a name="run-the-applications"></a>Exécution des applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l'invite de commandes de votre répertoire de travail, exécutez la commande suivante pour écouter les messages cloud-à-appareil :

    ```shell
    python SimulatedDevice.py
    ```

    ![Exécution de l’application de périphérique simulé](./media/iot-hub-python-python-c2d/device-1.png)

1. Ouvrez une nouvelle invite de commandes dans votre répertoire de travail et exécutez la commande suivante pour envoyer des messages cloud-à-appareil :

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Exécutez l’application pour envoyer la commande cloud-à-appareil](./media/iot-hub-python-python-c2d/service.png)

1. Notez les messages reçus par l'appareil.

    ![Message reçu](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à envoyer et recevoir des messages cloud-à-appareil.

Pour en savoir plus sur le développement de solutions avec IoT Hub, consultez le [Guide du développeur d’IoT Hub](iot-hub-devguide.md).