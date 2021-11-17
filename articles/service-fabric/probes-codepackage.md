---
title: Probes (diagnostics) Azure Service Fabric
description: Comment modéliser une probe liveness et readiness dans Azure Service Fabric en utilisant des fichiers manifeste d’application et de service.
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 1f78499d6be8ee68011540abfba00a404b8c6ec5
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059711"
---
# <a name="service-fabric-probes"></a>Probes Service Fabric
Avant de poursuivre la lecture de cet article, familiarisez-vous avec le [modèle d’application Service Fabric][application-model-link] et le [modèle d’hébergement Service Fabric][hosting-model-link]. Cet article fournit une vue d’ensemble de la manière de définir une probe liveness à l’aide de fichiers manifeste.

## <a name="liveness-probe"></a>Probe liveness
À partir de la version 7.1, Azure Service Fabric prend en charge un mécanisme de diagnostic de probe liveness pour applications conteneurisées et non conteneurisées. Une probe liveness permet de rendre compte de l’activité d’un package de code qui redémarre si elle ne répond pas rapidement.

## <a name="readiness-probe"></a>Probe readiness
Depuis la version 8.2, une probe readiness est également prise en charge. Une probe readiness permet de déterminer si un package de code est prêt à accepter du trafic. Par exemple, si votre conteneur prend beaucoup de temps pour traiter une demande ou si la file d’attente des demandes est pleine, votre package de code ne peut plus accepter de trafic. Par conséquent, les points de terminaison pour atteindre le package de code sont supprimés. 

Le comportement de la probe readiness est le suivant :
1.  L’instance de conteneur/package de code démarre
2.  Les points de terminaison sont publiés immédiatement
3.  La probe readiness comment à s’exécuter
4.  La probe readiness finit par atteindre un seuil d’échec et le point de terminaison est supprimé, ce qui la rend indisponible
5.  L’instance finit par devenir prête
6.  La probe readiness remarque que l’instance est prête et publie à nouveau un point de terminaison
7.  Les demandes sont à nouveau acheminées et aboutissent car l’instance est prête à les traiter

> [!NOTE] 
> Pour la probe readiness, le package de code n’est pas redémarré. Simplement, les points de terminaison n’étant pas publiés, l’ensemble de réplicas/partitions n’est pas affecté.
>

## <a name="semantics"></a>Sémantique
Vous ne pouvez spécifier qu’une probe liveness et une probe readiness par package de code, et vous pouvez contrôler leur comportement à l’aide des champs suivants :

* `type` : permet de spécifier si le type de probe est liveness ou readiness. Les valeurs prises en charge sont **Liveness** ou **Readiness**.

* `initialDelaySeconds` : délai initial, en secondes, avant le début de l’exécution du diagnostic probe après que le conteneur a démarré. La valeur prise en charge est **int**. La valeur par défaut est 0 et la valeur minimale est 0.

* `timeoutSeconds` : délai, en secondes, après lequel nous considérons le diagnostic probe comme ayant échoué s’il ne s’est pas terminé avec succès. La valeur prise en charge est **int**. La valeur par défaut est 1 et la valeur minimale est 1.

* `periodSeconds` : période, en secondes, pour spécifier la fréquence du diagnostic. La valeur prise en charge est **int**. La valeur par défaut est 10 et la valeur minimale est 1.

* `failureThreshold` : quand cette valeur est atteinte, le conteneur redémarre. La valeur prise en charge est **int**. La valeur par défaut est 3 et la valeur minimale est 1.

* `successThreshold` : en cas d’échec, pour que le diagnostic soit considéré comme réussi, il doit s’exécuter correctement pour cette valeur. La valeur prise en charge est **int**. La valeur par défaut est 1 et la valeur minimale est 1.

Il peut y avoir, au plus, un diagnostic sur un conteneur à tout moment. Si le diagnostic ne se termine pas avant l’expiration du délai défini dans **timeoutSeconds**, patientez et comptez le temps jusqu’au **FailureThreshold**. 

En outre, ServiceFabric déclenche les [rapports d’intégrité][health-introduction-link] du diagnostic probe suivants sur **DeployedServicePackage** :

* `OK` : le diagnostic réussit pour la valeur définie dans **successThreshold**.

* `Error` : **failureCount** ==  **failureThreshold** avant le redémarrage du conteneur.

* `Warning`: 
    * le diagnostic échoue et **failureCount** < **failureThreshold**. Ce rapport d’intégrité reste jusqu’à ce que **failureCount** atteigne la valeur définie dans **failureThreshold** ou **successThreshold**.
    * En cas de réussite après un échec, l’avertissement reste, mais avec les réussites consécutives mises à jour.

## <a name="specifying-a-probe"></a>Spécification d’une probe

Vous pouvez spécifier un diagnostic probe liveness dans le fichier ApplicationManifest.xml sous **ServiceManifestImport**.

Le diagnostic peut concerner l’un des éléments suivants :

* HTTP
* TCP
* Exec 

### <a name="http-probe"></a>Sonde HTTP

Pour un diagnostic HTTP, Service Fabric envoie une requête HTTP au port et au chemin que vous spécifiez. Un code de retour supérieur ou égal à 200, et inférieur à 400, indique une réussite.

Voici un exemple montrant comment spécifier une probe liveness HTTP :

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

Le diagnostic HTTP a des propriétés supplémentaires que vous pouvez définir :

* `path` : chemin à utiliser dans la requête HTTP.

* `port` : port à utiliser pour les diagnostics. Cette propriété est obligatoire. La plage est comprise entre 1 et 65 535.

* `scheme` : schéma à utiliser pour la connexion au package de code. Si cette propriété a la valeur HTTPS, la vérification du certificat est ignorée. Le paramètre par défaut est HTTP.

* `httpHeader` : en-têtes à définir dans la requête. Vous pouvez spécifier plusieurs en-têtes.

* `host` : adresse IP de l’hôte à laquelle se connecter.

### <a name="tcp-probe"></a>Sonde TCP

Pour un diagnostic TCP, Service Fabric essaie d’ouvrir un socket sur le conteneur à l’aide du port spécifié. S’il peut établir une connexion, le diagnostic est considéré comme ayant réussi. Voici un exemple montrant comment spécifier un diagnostic qui utilise un socket TCP :

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

### <a name="exec-probe"></a>Diagnostic Exec

Ce diagnostic émet une commande **exec** dans le conteneur et attend que la commande se termine.

> [!NOTE]
> La commande **exec** prend une chaîne séparée par des virgules. La commande de l’exemple suivant fonctionne pour un conteneur Linux.
> Si vous essayez de diagnostiquer un conteneur Windows, utilisez **cmd**.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Étapes suivantes
Pour accéder à des informations connexes, consultez l’article suivant :
* [Service Fabric et conteneurs][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

