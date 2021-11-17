---
title: Bien démarrer avec la gestion des appareils Azure IoT Hub (Python) | Microsoft Docs
description: Découvrez comment utiliser la gestion des appareils IoT Hub pour lancer un redémarrage d’appareil à distance. Vous utilisez le SDK Azure IoT pour Python afin d’implémenter une application d’appareil simulé qui inclut une méthode directe et une application de service qui appelle la méthode directe.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: lizross
ms.custom: mqtt, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 47aac8108bf1c5cb6bf5c0d36bafc781695ec9af
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551631"
---
# <a name="get-started-with-device-management-python"></a>Prise en main de la gestion d’appareils (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ce didacticiel vous explique les procédures suivantes :

* Utiliser le portail Azure pour créer un IoT Hub et une identité d’appareil dans celui-ci.

* Créer une application d’appareil simulé disposant d’une méthode directe permettant le redémarrage de cet appareil. Les méthodes directes sont appelées à partir du cloud.

* Créer une application console Python qui appelle une méthode directe de redémarrage sur l’application d’appareil simulé via votre IoT Hub.

À la fin de ce didacticiel, vous disposerez de deux applications console Python :

* **dmpatterns_getstarted_device.py**, qui se connecte à votre IoT Hub avec l’identité d’appareil créée précédemment, reçoit une méthode directe de redémarrage, simule un redémarrage physique et indique l’heure du dernier redémarrage.

* **dmpatterns_getstarted_service.py**, qui appelle une méthode directe sur l’application de l’appareil simulé, affiche la réponse et affiche les propriétés signalées pour la mise à jour.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Vérifiez que le port 8883 est ouvert dans votre pare-feu. L’exemple d’appareil décrit dans cet article utilise le protocole MQTT, qui communique via le port 8883. Ce port peut être bloqué dans certains environnements réseau professionnels et scolaires. Pour plus d’informations sur les différentes façons de contourner ce problème, consultez [Connexion à IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Inscrire un nouvel appareil dans le hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Création d’une application de périphérique simulé

Dans cette section, vous allez :

* Créer une application console Python qui répond à une méthode directe appelée par le cloud

* Simuler un redémarrage d’appareil

* Utiliser les propriétés signalées pour activer les requêtes sur le jumeau d’appareil afin d’identifier les appareils et l’heure de leur dernier redémarrage

1. À partir de votre invite de commandes, exécutez la commande suivante pour installer le package **azure-iot-device** :

    ```cmd/sh
    pip install azure-iot-device
    ```

2. À l’aide d’un éditeur de texte, dans votre répertoire de travail, créez un fichier nommé **dmpatterns_getstarted_device.py**.

3. Ajoutez les instructions `import` suivantes au début du fichier **dmpatterns_getstarted_device.py**.

    ```python
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Ajoutez la variable **CONNECTION_STRING**. Remplacez la valeur de l’espace réservé `{deviceConnectionString}` par la chaîne de connexion de votre appareil. Vous avez copié cette chaîne de connexion précédemment à l’étape [Inscrire un nouvel appareil dans le hub IoT](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Ajoutez la fonction suivante pour instancier un client configuré pour des méthodes directes sur l’appareil.

    ```python
    def create_client():
        # Instantiate the client
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

        # Define the handler for method requests
        def method_request_handler(method_request):
            if method_request.name == "rebootDevice":
                # Act on the method by rebooting the device
                print("Rebooting device")
                time.sleep(20)
                print("Device rebooted")

                # ...and patching the reported properties
                current_time = str(datetime.datetime.now())
                reported_props = {"rebootTime": current_time}
                client.patch_twin_reported_properties(reported_props)
                print( "Device twins updated with latest rebootTime")

                # Create a method response indicating the method request was resolved
                resp_status = 200
                resp_payload = {"Response": "This is the response from the device"}
                method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            
            else:
                # Create a method response indicating the method request was for an unknown method
                resp_status = 404
                resp_payload = {"Response": "Unknown method"}
                method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)

            # Send the method response
            client.send_method_response(method_response)

        try:
            # Attach the handler to the client
            client.on_method_request_received = method_request_handler
        except:
            # In the event of failure, clean up
            client.shutdown()

        return client
    ```

6. Démarrez l’exemple de méthode directe et attendez.

    ```python
    def main():
        print ("Starting the IoT Hub Python sample...")
        client = create_client()

        print ("Waiting for commands, press Ctrl-C to exit")
        try:
            # Wait for program exit
            while True:
                time.sleep(1000)
        except KeyboardInterrupt:
            print("IoTHubDeviceClient sample stopped")
        finally:
            # Graceful exit
            print("Shutting down IoT Hub Client")
            client.shutdown()

    if __name__ == '__main__':
        main()
    ```

7. Enregistrez et fermez le fichier **dmpatterns_getstarted_device.py**.

> [!NOTE]
> Pour simplifier les choses, ce didacticiel n’implémente aucune stratégie de nouvelle tentative. Dans le code de production, vous devez implémenter des stratégies de nouvelle tentative (par exemple, une interruption exponentielle), comme indiqué dans l’article [Gestion des erreurs temporaires](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Obtenir la chaîne de connexion du hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Déclencher un redémarrage à distance sur l’appareil à l’aide d’une méthode directe

Dans cette section, vous créez une application console Python qui lance un redémarrage à distance sur un appareil avec une méthode directe. L’application utilise des requêtes du jumeau d’appareil pour déterminer l’heure du dernier redémarrage de cet appareil.

1. À partir de votre invite de commandes, exécutez la commande suivante pour installer le package **azure-iot-hub** :

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. À l’aide d’un éditeur de texte, dans votre répertoire de travail, créez un fichier nommé **dmpatterns_getstarted_service.py**.

3. Ajoutez les instructions `import` suivantes au début du fichier **dmpatterns_getstarted_service.py**.

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Ajoutez les déclarations de variable suivantes. Remplacez la valeur de l’espace réservé `{IoTHubConnectionString}` par la chaîne de connexion du hub IoT que vous avez copiée dans [Obtenir la chaîne de connexion du hub IoT](#get-the-iot-hub-connection-string). Remplacez la valeur de l’espace réservé `{deviceId}` par l’ID d’appareil que vous avez inscrit à l’étape [Inscrire un nouvel appareil dans le hub IoT](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Ajoutez la fonction suivante pour appeler la méthode d’appareil afin de redémarrer l’appareil cible, puis interrogez les jumeaux de l’appareil et obtenez l’heure du dernier redémarrage.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. Enregistrez et fermez le fichier **dmpatterns_getstarted_service.py**.

## <a name="run-the-apps"></a>Exécuter les applications

Vous êtes maintenant prêt à exécuter les applications.

1. À l’invite de commandes, exécutez la commande suivante pour commencer à écouter la méthode directe de redémarrage.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. À l’autre invite de commandes, exécutez la commande suivante pour déclencher le redémarrage à distance et interroger la représentation d’appareil pour déterminer le moment du dernier redémarrage.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. La réponse de l’appareil à la méthode directe s’affiche dans la console.

   La réponse de l’appareil à la méthode directe de redémarrage est affichée ci-dessous :

   ![Sortie de l’application d’appareil simulé](./media/iot-hub-python-python-device-management-get-started/device.png)

   Le code suivant montre le service appelant la méthode directe de redémarrage et interrogeant le jumeau d’appareil pour connaître son état :

   ![Déclencher la sortie du service de redémarrage](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
