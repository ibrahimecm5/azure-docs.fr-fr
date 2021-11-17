---
title: Tutoriel - Tester la capacité des certificats X.509 à authentifier les appareils auprès d’un hub Azure IoT | Microsoft Docs
description: 'Tutoriel : Tester la capacité d’authentification de vos certificats X.509 auprès d’un hub Azure IoT'
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: lizross
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: f3f59cb5346f6a2526ee4e8b0d433b577c9cfa57
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553797"
---
# <a name="tutorial-testing-certificate-authentication"></a>Tutoriel : Test de l’authentification par certificat

Vous pouvez utiliser l’exemple de code C# suivant pour vérifier que votre certificat peut authentifier votre appareil auprès de votre hub IoT. Notez que vous devez effectuer les opérations suivantes avant d’exécuter le code de test :

* Créer un certificat d’autorité de certification racine ou secondaire
* Charger votre certificat d’autorité de certification sur votre hub IoT
* Prouver que vous êtes en possession du certificat d’autorité de certification
* Ajouter un appareil à votre hub IoT
* Créer un certificat d’appareil avec le même ID d’appareil que celui de votre appareil

>[!IMPORTANT]
>Le processus d’authentification vérifie que votre appareil est associé au nom de l’IoT Hub correct. 

## <a name="code-example"></a>Exemple de code

L’exemple de code suivant montre comment créer une application C# afin de simuler l’appareil X.509 inscrit pour votre hub IoT. L’exemple envoie les valeurs de température et d’humidité de l’appareil simulé sur votre hub. Dans ce didacticiel, nous créerons uniquement l’application de l’appareil. En guide d’exercice, les lecteurs seront chargés de créer l’application de service IoT Hub qui enverra les réponses aux événements envoyés par cet appareil simulé.

1. Ouvrez Visual Studio, sélectionnez **Créer un nouveau projet**, puis choisissez le modèle de projet **Application console (.NET Framework)** . Sélectionnez **Suivant**.

1. Dans **Configurer votre nouveau projet**, nommez le projet *SimulateX509Device*, puis sélectionnez **Créer**.

   ![Créer un projet d’appareil X.509 dans Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **SimulateX509Device**, puis sélectionnez **Gérer les packages NuGet**.

1. Dans **Gestionnaire de package NuGet**, sélectionnez **Parcourir**, puis recherchez et choisissez **Microsoft.Azure.Devices.Client**. Sélectionnez **Installer**.

   ![Ajouter le package NuGet device SDK dans Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Cette opération a pour effet de télécharger, d’installer et d’ajouter une référence au package NuGet Azure IoT device SDK et ses dépendances.

    Entrez et exécutez le code suivant :

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

                if (deviceClient == null)
                {
                    Console.WriteLine("Failed to create DeviceClient!");
                }
                else
                {
                    Console.WriteLine("Successfully created DeviceClient!");
                    SendEvent(deviceClient).Wait();
                }

                Console.WriteLine("Exiting...\n");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
         }
    }
}
```