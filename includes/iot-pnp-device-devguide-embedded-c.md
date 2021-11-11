---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 09/07/2021
ms.openlocfilehash: 3f580069fc44f676bc6fcffa40bd74256a818a50
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131521152"
---
## <a name="sdks"></a>SDK

Les extraits de code indiqués dans cet article reposent sur des exemples qui utilisent le [module complémentaire Azure IoT Middleware pour Azure RTOS](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot). Ce module complémentaire est une couche de liaison entre [Azure RTOS](/azure/rtos/overview-rtos) et le [SDK Azure pour Embedded C](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

Les extraits de code figurant dans cet article reposent sur les exemples suivants :

- [Exemple de thermostat IoT Plug-and-Play](https://github.com/azure-rtos/netxduo/blob/master/addons/azure_iot/samples/sample_azure_iot_embedded_sdk_pnp.c)
- [Exemple de contrôleur de température IoT Plug-and-Play](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot/samples/sample_pnp_temperature_controller)

## <a name="model-id-announcement"></a>Annonce de l’ID de modèle

Pour annoncer l’ID de modèle, l’appareil doit l’inclure dans les informations de connexion :

```c
#include "nx_azure_iot_hub_client.h"

// ...

#define SAMPLE_PNP_MODEL_ID "dtmi:com:example:Thermostat;1"

// ...

status = nx_azure_iot_hub_client_model_id_set(iothub_client_ptr, (UCHAR *)SAMPLE_PNP_MODEL_ID, sizeof(SAMPLE_PNP_MODEL_ID) - 1);
```

> [!TIP]
> C’est la seule fois où un appareil peut définir l’ID de modèle : celui-ci ne peut pas être mis à jour après la connexion de l’appareil.

## <a name="dps-payload"></a>Charge utile DPS

Les appareils qui utilisent [Device Provisioning Service (DPS)](../articles/iot-dps/about-iot-dps.md) peuvent inclure le `modelId` à utiliser pendant le processus de provisionnement à l’aide de la charge utile JSON suivante :

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

L’exemple utilise le code suivant pour envoyer cette charge utile :

```c
#include "nx_azure_iot_provisioning_client.h"

// ...

#define SAMPLE_PNP_MODEL_ID "dtmi:com:example:Thermostat;1"
#define SAMPLE_PNP_DPS_PAYLOAD "{\"modelId\":\"" SAMPLE_PNP_MODEL_ID "\"}"

// ...

status = nx_azure_iot_provisioning_client_registration_payload_set(prov_client_ptr, (UCHAR *)SAMPLE_PNP_DPS_PAYLOAD, sizeof(SAMPLE_PNP_DPS_PAYLOAD) - 1);
```

## <a name="use-components"></a>Utiliser des composants

Comme le décrit la section [Présentation des composants dans les modèles IoT Plug-and-Play](../articles/iot-develop/concepts-modeling-guide.md), les générateurs d’appareils doivent décider s’ils souhaitent utiliser des composants pour décrire leurs appareils, Lors de l’utilisation des composants, les appareils devront suivre les règles décrites dans les sections suivantes. Pour simplifier l’utilisation des conventions IoT Plug-and-Play pour les composants, les exemples utilisent les fonctions d’assistance dans [nx_azure_iot_hub_client.h](https://github.com/azure-rtos/netxduo/blob/master/addons/azure_iot/nx_azure_iot_hub_client.h).

## <a name="telemetry"></a>Télémétrie

Un composant par défaut ne nécessite aucune propriété spéciale ajoutée au message de télémétrie.

Lorsque vous utilisez des composants imbriqués, les appareils doivent définir une propriété de message avec le nom du composant. Dans l’extrait de code suivant, `component_name_ptr` est le nom d’un composant comme `thermostat1`. La fonction d’assistance `nx_azure_iot_pnp_helper_telemetry_message_create` définie dans *nx_azure_iot_pnp_helpers.h* ajoute la propriété de message avec le nom du composant :

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR telemetry_name[] = "temperature";

// ...

UINT sample_pnp_thermostat_telemetry_send(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle, NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr)
{
UINT status;
NX_PACKET *packet_ptr;
NX_AZURE_IOT_JSON_WRITER json_writer;
UINT buffer_length;

    // ...

    /* Create a telemetry message packet. */
    if ((status = nx_azure_iot_pnp_helper_telemetry_message_create(iothub_client_ptr, handle -> component_name_ptr,
        handle -> component_name_length,
        &packet_ptr, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    if ((status = nx_azure_iot_hub_client_telemetry_send(iothub_client_ptr, packet_ptr,
        (UCHAR *)scratch_buffer, buffer_length, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    return(status);
}
```

## <a name="read-only-properties"></a>Propriétés en lecture seule

Le signalement d’une propriété à partir du composant par défaut ne nécessite pas de construction spéciale :

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR reported_max_temp_since_last_reboot[] = "maxTempSinceLastReboot";

// ...

static UINT sample_build_reported_property(NX_AZURE_IOT_JSON_WRITER *json_builder_ptr, double temp)
{
UINT ret;

    if (nx_azure_iot_json_writer_append_begin_object(json_builder_ptr) ||
        nx_azure_iot_json_writer_append_property_with_double_value(json_builder_ptr,
            (UCHAR *)reported_max_temp_since_last_reboot,
            sizeof(reported_max_temp_since_last_reboot) - 1,
            temp, DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_end_object(json_builder_ptr))
    {
        ret = 1;
        printf("Failed to build reported property\r\n");
    }
    else
    {
        ret = 0;
    }

    return(ret);
}

// ...

if ((status = sample_build_reported_property(&json_builder, device_max_temp)))
{
    // ...
}

reported_properties_length = nx_azure_iot_json_writer_get_bytes_used(&json_builder);
if ((status = nx_azure_iot_hub_client_device_twin_reported_properties_send(&(context -> iothub_client),
    scratch_buffer,
    reported_properties_length,
    &request_id, &response_status,
    &reported_property_version,
    (5 * NX_IP_PERIODIC_RATE))))
{
    // ...
}
```

Le jumeau d’appareil est mis à jour avec la propriété rapportée suivante :

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

Quand vous utilisez des composants imbriqués, les propriétés doivent être créées au sein du nom du composant et inclure un marqueur. Dans l’extrait de code suivant, `component_name_ptr` est le nom d’un composant comme `thermostat1`. La fonction d’assistance `nx_azure_iot_pnp_helper_build_reported_property` définie dans *nx_azure_iot_pnp_helpers.h* crée la propriété rapportée dans le format correct :

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR reported_max_temp_since_last_reboot[] = "maxTempSinceLastReboot";

UINT sample_pnp_thermostat_report_max_temp_since_last_reboot_property(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle, NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr)
{
UINT reported_properties_length;
UINT status;
UINT response_status;
UINT request_id;
NX_AZURE_IOT_JSON_WRITER json_builder;
ULONG reported_property_version;

    // ...

    if ((status = nx_azure_iot_pnp_helper_build_reported_property(handle -> component_name_ptr,
        handle -> component_name_length,
        append_max_temp, (VOID *)handle,
        &json_builder)))
    {
        // ...
    }

    reported_properties_length = nx_azure_iot_json_writer_get_bytes_used(&json_builder);
    if ((status = nx_azure_iot_hub_client_device_twin_reported_properties_send(iothub_client_ptr,
        scratch_buffer,
        reported_properties_length,
        &request_id, &response_status,
        &reported_property_version,
        (5 * NX_IP_PERIODIC_RATE))))
    {
        // ...
    }

    // ...
}
```

Le jumeau d’appareil est mis à jour avec la propriété rapportée suivante :

```json
{
    "reported": {
        "thermostat1" : {  
            "__t" : "c",  
            "maxTemperature" : 38.7
        } 
    }
}
```

## <a name="writable-properties"></a>Propriétés accessibles en écriture

Ces propriétés peuvent être définies par l’appareil ou mises à jour par la solution. Pour respecter les conventions IoT Plug-and-Play, l’appareil doit informer le service que la propriété a bien été reçue.

### <a name="report-a-writable-property"></a>Signalement d’une propriété accessible en écriture

Quand un appareil rapporte une propriété accessible en écriture, il doit inclure les valeurs `ack` définies dans les conventions.

Pour signaler une propriété accessible en écriture à partir du composant par défaut :

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR reported_temp_property_name[] = "targetTemperature";
static const CHAR reported_value_property_name[] = "value";
static const CHAR reported_status_property_name[] = "ac";
static const CHAR reported_version_property_name[] = "av";
static const CHAR reported_description_property_name[] = "ad";

// ...

static VOID sample_send_target_temperature_report(SAMPLE_CONTEXT *context, double current_device_temp_value,
    UINT status, UINT version, UCHAR *description_ptr,
    UINT description_len)
{
NX_AZURE_IOT_JSON_WRITER json_builder;
UINT bytes_copied;
UINT response_status;
UINT request_id;
ULONG reported_property_version;

    // ...

    if (nx_azure_iot_json_writer_append_begin_object(&json_builder) ||
        nx_azure_iot_json_writer_append_property_name(&json_builder,
            (UCHAR *)reported_temp_property_name,
            sizeof(reported_temp_property_name) - 1) ||
        nx_azure_iot_json_writer_append_begin_object(&json_builder) ||
        nx_azure_iot_json_writer_append_property_with_double_value(&json_builder,
            (UCHAR *)reported_value_property_name,
            sizeof(reported_value_property_name) - 1,
            current_device_temp_value, DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_int32_value(&json_builder,
            (UCHAR *)reported_status_property_name,
            sizeof(reported_status_property_name) - 1,
            (int32_t)status) ||
        nx_azure_iot_json_writer_append_property_with_int32_value(&json_builder,
            (UCHAR *)reported_version_property_name,
            sizeof(reported_version_property_name) - 1,
            (int32_t)version) ||
        nx_azure_iot_json_writer_append_property_with_string_value(&json_builder,
            (UCHAR *)reported_description_property_name,
            sizeof(reported_description_property_name) - 1,
            description_ptr, description_len) ||
        nx_azure_iot_json_writer_append_end_object(&json_builder) ||
        nx_azure_iot_json_writer_append_end_object(&json_builder))
    {
        // ...
    }
    else
    // ...
}
```

Le jumeau d’appareil est mis à jour avec la propriété rapportée suivante :

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
  }
}
```

Pour rapporter une propriété accessible en écriture à partir d’un composant imbriqué, le jumeau doit inclure un marqueur et les propriétés doivent être créées au sein du nom du composant. Dans l’extrait de code suivant, `component_name_ptr` est le nom d’un composant comme `thermostat1`. La fonction d’assistance `nx_azure_iot_pnp_helper_build_reported_property_with_status` définie dans *nx_azure_iot_pnp_helpers.h* crée la charge utile de la propriété rapportée :

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static VOID sample_send_target_temperature_report(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr, double temp,
    INT status_code, UINT version, const CHAR *description)
{
UINT bytes_copied;
UINT response_status;
UINT request_id;
NX_AZURE_IOT_JSON_WRITER json_writer;
ULONG reported_property_version;

    // ...

    if (nx_azure_iot_pnp_helper_build_reported_property_with_status(handle -> component_name_ptr, handle -> component_name_length,
        (UCHAR *)target_temp_property_name,
        sizeof(target_temp_property_name) - 1,
        append_temp, (VOID *)&temp, status_code,
        (UCHAR *)description,
        strlen(description), version, &json_writer))
    {
        // ...
    }
    else
    {
        // ...
    }

    // ...
}
```

Le jumeau d’appareil est mis à jour avec la propriété rapportée suivante :

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
    }
  }
}
```

### <a name="subscribe-to-desired-property-updates"></a>Abonnement aux mises à jour de propriétés souhaitées

Les services peuvent mettre à jour les propriétés souhaitées qui déclenchent une notification sur les appareils connectés. Cette notification inclut les propriétés voulues mises à jour et le numéro de version identifiant la mise à jour. Les appareils doivent inclure ce numéro de version dans le message `ack` renvoyé au service.

Un composant par défaut considère la propriété unique et crée la propriété `ack` rapportée avec la version reçue :

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR temp_response_description[] = "success";

// ...

static UINT sample_parse_desired_temp_property(SAMPLE_CONTEXT *context,
    NX_AZURE_IOT_JSON_READER *json_reader_ptr,
    UINT is_partial)
{
double parsed_value;
UINT version;
NX_AZURE_IOT_JSON_READER copy_json_reader;
UINT status;

    // ...

    copy_json_reader = *json_reader_ptr;
    if (sample_json_child_token_move(&copy_json_reader,
            (UCHAR *)desired_version_property_name,
            sizeof(desired_version_property_name) - 1) ||
        nx_azure_iot_json_reader_token_int32_get(&copy_json_reader, (int32_t *)&version))
    {
        // ...
    }

    // ...

    sample_send_target_temperature_report(context, current_device_temp, 200,
        (UINT)version, (UCHAR *)temp_response_description,
        sizeof(temp_response_description) - 1);

    // ...
}
```

Un composant imbriqué reçoit les propriétés voulues wrappées avec le nom du composant et crée la propriété `ack` rapportée avec la version reçue :

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR target_temp_property_name[] = "targetTemperature";
static const CHAR temp_response_description_success[] = "success";
static const CHAR temp_response_description_failed[] = "failed";

// ...

UINT sample_pnp_thermostat_process_property_update(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr,
    UCHAR *component_name_ptr, UINT component_name_length,
    UCHAR *property_name_ptr, UINT property_name_length,
    NX_AZURE_IOT_JSON_READER *property_value_reader_ptr, UINT version)
{
double parsed_value = 0;
INT status_code;
const CHAR *description;

    // ...

    if (property_name_length != (sizeof(target_temp_property_name) - 1) ||
        strncmp((CHAR *)property_name_ptr, (CHAR *)target_temp_property_name, property_name_length) != 0)
    {
        // ...
    }
    else if (nx_azure_iot_json_reader_token_double_get(property_value_reader_ptr, &parsed_value))
    {
        status_code = 401;
        description = temp_response_description_failed;
    }
    else
    {
        status_code = 200;
        description = temp_response_description_success;

        // ...
    }

    sample_send_target_temperature_report(handle, iothub_client_ptr, parsed_value,
                                          status_code, version, description);

    // ...
}
```

Le jumeau d’appareil d’un composant imbriqué montre les sections desired et reported de la façon suivante :

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
    }
  }
}
```

## <a name="commands"></a>Commandes

Un composant par défaut reçoit le nom de la commande telle qu’elle a été appelée par le service.

Un composant imbriqué reçoit le nom de la commande avec, pour préfixe, le nom du composant et le séparateur `*`. Dans l’extrait de code suivant, la fonction d’assistance `nx_azure_iot_pnp_helper_command_name_parse` définie dans *nx_azure_iot_pnp_helpers.h* analyse le nom du composant et le nom de la commande à partir du message que l’appareil reçoit du service :

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_pnp_helpers.h"

// ...

static VOID sample_direct_method_action(SAMPLE_CONTEXT *sample_context_ptr)
{
NX_PACKET *packet_ptr;
UINT status;
USHORT method_name_length;
const UCHAR *method_name_ptr;
USHORT context_length;
VOID *context_ptr;
UINT component_name_length;
const UCHAR *component_name_ptr;
UINT pnp_command_name_length;
const UCHAR *pnp_command_name_ptr;
NX_AZURE_IOT_JSON_WRITER json_writer;
NX_AZURE_IOT_JSON_READER json_reader;
NX_AZURE_IOT_JSON_READER *json_reader_ptr;
UINT status_code;
UINT response_length;

    // ...

    if ((status = nx_azure_iot_hub_client_direct_method_message_receive(&(sample_context_ptr -> iothub_client),
        &method_name_ptr, &method_name_length,
        &context_ptr, &context_length,
        &packet_ptr, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    if ((status = nx_azure_iot_pnp_helper_command_name_parse(method_name_ptr, method_name_length,
        &component_name_ptr, &component_name_length,
        &pnp_command_name_ptr,
        &pnp_command_name_length)) != NX_AZURE_IOT_SUCCESS)
    {
        // ...
    }
    
    // ...

    else
    {
        // ...

        if ((status = sample_pnp_thermostat_process_command(&sample_thermostat_1, component_name_ptr,
            component_name_length, pnp_command_name_ptr,
            pnp_command_name_length, json_reader_ptr,
            &json_writer, &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else if ((status = sample_pnp_thermostat_process_command(&sample_thermostat_2, component_name_ptr,
            component_name_length, pnp_command_name_ptr,
            pnp_command_name_length, json_reader_ptr,
            &json_writer, &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else if((status = sample_pnp_temp_controller_process_command(component_name_ptr, component_name_length,
            pnp_command_name_ptr, pnp_command_name_length,
            json_reader_ptr, &json_writer,
            &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else
        {
            printf("Failed to find any handler for method %.*s\r\n", method_name_length, method_name_ptr);
            status_code = SAMPLE_COMMAND_NOT_FOUND_STATUS;
            response_length = 0;
        }

        // ...
    }
}
```

### <a name="request-and-response-payloads"></a>Charge utile de demande et de réponse

Les commandes utilisent des types pour définir leur charge utile de demande et de réponse. Un appareil doit désérialiser le paramètre d’entrée entrant et sérialiser la réponse.

L’exemple suivant montre comment implémenter une commande avec des types complexes définis dans les charges utiles :

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

Les extraits de code suivants montrent comment un appareil implémente cette définition de commande, notamment les types utilisés pour permettre la sérialisation et la désérialisation :

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR report_max_temp_name[] = "maxTemp";
static const CHAR report_min_temp_name[] = "minTemp";
static const CHAR report_avg_temp_name[] = "avgTemp";
static const CHAR report_start_time_name[] = "startTime";
static const CHAR report_end_time_name[] = "endTime";
static const CHAR fake_start_report_time[] = "2020-01-10T10:00:00Z";
static const CHAR fake_end_report_time[] = "2023-01-10T10:00:00Z";

// ...

static UINT sample_get_maxmin_report(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_JSON_READER *json_reader_ptr,
    NX_AZURE_IOT_JSON_WRITER *out_json_builder_ptr)
{
UINT status;
UCHAR *start_time = (UCHAR *)fake_start_report_time;
UINT start_time_len = sizeof(fake_start_report_time) - 1;
UCHAR time_buf[32];

    // ...

    /* Build the method response payload */
    if (nx_azure_iot_json_writer_append_begin_object(out_json_builder_ptr) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_max_temp_name,
            sizeof(report_max_temp_name) - 1,
            handle -> maxTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_min_temp_name,
            sizeof(report_min_temp_name) - 1,
            handle -> minTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_avg_temp_name,
            sizeof(report_avg_temp_name) - 1,
            handle -> avgTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_string_value(out_json_builder_ptr,
            (UCHAR *)report_start_time_name,
            sizeof(report_start_time_name) - 1,
            (UCHAR *)start_time, start_time_len) ||
        nx_azure_iot_json_writer_append_property_with_string_value(out_json_builder_ptr,
            (UCHAR *)report_end_time_name,
            sizeof(report_end_time_name) - 1,
            (UCHAR *)fake_end_report_time,
            sizeof(fake_end_report_time) - 1) ||
        nx_azure_iot_json_writer_append_end_object(out_json_builder_ptr))
    {
        status = NX_NOT_SUCCESSFUL;
    }
    else
    {
        status = NX_AZURE_IOT_SUCCESS;
    }

    return(status);
}
```

> [!Tip]
> Le nom des demandes et des réponses n’est pas présent dans les charges utiles sérialisées transmises sur le réseau.
