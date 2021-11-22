---
title: Résolution des problèmes du micro-agent Defender pour le cloud pour IoT (préversion)
description: Découvrez comment gérer les erreurs inattendues ou inexpliquées.
ms.date: 11/09/2021
ms.topic: reference
ms.openlocfilehash: 94fd4c75a24b37bbc50ca582ca7bb64de87a042c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283958"
---
# <a name="defender-for-cloud-iot-micro-agent-troubleshooting-preview"></a>Résolution des problèmes du micro-agent Defender pour le cloud pour IoT (préversion)

Si une erreur inattendue se produit, vous pouvez utiliser ces méthodes de résolution de problèmes pour tenter de résoudre le problème. Vous pouvez également contacter l’équipe produit d’Azure Defender pour le cloud pour IoT afin d’obtenir de l’aide si nécessaire.   

## <a name="service-status"></a>État du service 

Pour voir l’état du service : 

1. Exécutez la commande suivante :

    ```bash
    systemctl status defender-iot-micro-agent.service 
    ```

1. Vérifiez que le service est stable en contrôlant qu’il est `active` et que la durée de bon fonctionnement dans le processus est appropriée.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="Vérifiez que le service est stable en contrôlant qu’il est actif et que la durée de bon fonctionnement est appropriée.":::

Si le service apparaît comme étant `inactive`, exécutez la commande suivante pour le démarrer :

```bash
systemctl start defender-iot-micro-agent.service 
```

Vous savez que le service plante si la durée de bon fonctionnement du processus est de moins de 2 minutes. Pour résoudre ce problème, vous devez [vérifier les journaux](#review-the-logs).

## <a name="validate-micro-agent-root-privileges"></a>Valider les privilèges racines d’un micro-agent

Utilisez la commande suivante pour vérifier que le service du micro-agent IoT Defender pour le cloud s’exécute avec des privilèges racines.

```bash
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Vérifiez que le service de l’agent Defender pour IoT s’exécute avec des privilèges racines.":::
## <a name="review-the-logs"></a>Vérifier les journaux 

Pour vérifier les journaux, utilisez la commande suivante :  

```bash
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### <a name="quick-log-review"></a>Revue rapide des journaux

Si un problème se produit lors de l’exécution du micro-agent, vous pouvez exécuter le micro-agent dans un état temporaire, ce qui permet d’afficher les journaux à l’aide de la commande suivante :

```bash
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## <a name="restart-the-service"></a>Redémarrez le service.

Pour redémarrer le service, utilisez la commande suivante : 

```bash
sudo systemctl restart defender-iot-micro-agent 
```

## <a name="next-steps"></a>Étapes suivantes

Consultez [Prise en charge et mise hors service des fonctionnalités](edge-security-module-deprecation.md).
