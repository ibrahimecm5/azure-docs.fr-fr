---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 09/10/2021
ms.author: danlep
ms.openlocfilehash: 4153c44c60e8da560b5523e5263f0f7e0c81fcec
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096615"
---
### <a name="connected-registry-module-settings"></a>Paramètres du module de registre connecté

* Utilisez les informations d’identification du jeton et la chaîne de connexion des sections précédentes pour mettre à jour les valeurs JSON pertinentes dans le nœud `env`. 
* Les variables d’environnement suivantes sont facultatives dans le nœud `env` :

    |Variable  |Description  |
    |---------|---------|
    | `ACR_REGISTRY_LOGIN_SERVER` |  Spécifie un nom d’hôte unique ou un nom de domaine complet. S’il est utilisé, le registre connecté accepte uniquement les requêtes effectuées à cette valeur de serveur de connexion. <br/><br/>Si aucune valeur n’est fournie, le registre connecté est accessible avec n’importe quelle valeur de serveur de connexion.  |
    |`ACR_REGISTRY_CERTIFICATE_VOLUME`     |   Si votre registre connecté est accessible via HTTPS, pointe vers le volume sur lequel les certificats HTTPS sont stockés.<br/><br/>S’il n’est pas défini, l’emplacement par défaut est `/var/acr/certs`.      |
    |`ACR_REGISTRY_DATA_VOLUME`     |  Remplace l’emplacement par défaut `/var/acr/data` où les images sont stockées par le registre connecté.<br/><br/>Cet emplacement doit correspondre à la liaison de volume pour le conteneur.       |

    > [!IMPORTANT]
    > Si le registre connecté écoute sur un port différent entre 80 et 443, la valeur `ACR_REGISTRY_LOGIN_SERVER` (si spécifiée) doit inclure le port. Exemple : `192.168.0.100:8080`.
* Une liaison `HostPort` pour le registre connecté doit être définie si le module proxy d’API n’est pas utilisé. Exemple :

    ```azurecli
     "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"],\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"8080\"}]}}}"
    ```

### <a name="api-proxy-module-settings"></a>Paramètres du module de proxy d’API

* Le proxy d’API écoute sur le port 8000 configuré comme `NGINX_DEFAULT_PORT`. Pour plus d’informations sur les paramètres de proxy d’API, consultez le [Référentiel GitHub IoT Edge](https://github.com/Azure/iotedge/tree/master/edge-modules/api-proxy-module). 

```json
{
    "modulesContent": {
        "$edgeAgent": {
            "properties.desired": {
                "modules": {
                    "connected-registry": {
                        "settings": {
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/acr/connected-registry:0.5.0",
                            "createOptions": "{\"HostConfig\":{\"Binds\":[\"/home/azureuser/connected-registry:/var/acr/data\"]}}"
                        },
                        "type": "docker",
                        "env": {
                            "ACR_REGISTRY_CONNECTION_STRING": {
                                "value": "ConnectedRegistryName=<REPLACE_WITH_CONNECTED_REGISTRY_NAME>;SyncTokenName=<REPLACE_WITH_SYNC_TOKEN_NAME>;SyncTokenPassword=REPLACE_WITH_SYNC_TOKEN_PASSWORD;ParentGatewayEndpoint=<REPLACE_WITH_CLOUD_REGISTRY_NAME>.<REPLACE_WITH_CLOUD_REGISTRY_REGION>.data.azurecr.io;ParentEndpointProtocol=https"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    },
                    "IoTEdgeAPIProxy": {
                        "settings": {
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-api-proxy:1.1.2",
                            "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8000/tcp\":[{\"HostPort\":\"8000\"}]}}}"
                        },
                        "type": "docker",
                        "env": {
                            "NGINX_DEFAULT_PORT": {
                                "value": "8000"
                            },
                            "CONNECTED_ACR_ROUTE_ADDRESS": {
                                "value": "connected-registry:8080"
                            },
                            "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                "value": "AzureBlobStorageonIoTEdge:11002"
                            }
                        },
                        "status": "running",
                        "restartPolicy": "always",
                        "version": "1.0"
                    }
                },
                "runtime": {
                    "settings": {
                        "minDockerVersion": "v1.25",
                        "registryCredentials": {
                            "cloudregistry": {
                                "address": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io",
                                "password": "<REPLACE_WITH_SYNC_TOKEN_PASSWORD>",
                                "username": "<REPLACE_WITH_SYNC_TOKEN_NAME>"
                            }
                        }
                    },
                    "type": "docker"
                },
                "schemaVersion": "1.1",
                "systemModules": {
                    "edgeAgent": {
                        "settings": {
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-agent:1.2.4",
                            "createOptions": ""
                        },
                        "type": "docker",
                        "env": {
                            "SendRuntimeQualityTelemetry": {
                                "value": "false"
                            }
                        }
                    },
                    "edgeHub": {
                        "settings": {
                            "image": "<REPLACE_WITH_CLOUD_REGISTRY_NAME>.azurecr.io/azureiotedge-hub:1.2.4",
                            "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                        },
                        "type": "docker",
                        "status": "running",
                        "restartPolicy": "always"
                    }
                }
            }
        },
        "$edgeHub": {
            "properties.desired": {
                "routes": {
                    "route": "FROM /messages/* INTO $upstream"
                },
                "schemaVersion": "1.1",
                "storeAndForwardConfiguration": {
                    "timeToLiveSecs": 7200
                }
            }
        }
    }
}
```
