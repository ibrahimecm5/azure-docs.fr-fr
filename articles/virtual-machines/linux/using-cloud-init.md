---
title: Vue d’ensemble de la prise en charge de cloud-init pour les machines virtuelles Linux dans Azure
description: Vue d’ensemble des fonctionnalités cloud-init permettant de configurer une machine virtuelle lors de l’approvisionnement dans Azure.
author: srijang
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/29/2021
ms.author: srijangupta
ms.openlocfilehash: 7ebabe554121e2ad07ef776ed78046a09db9ca9c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054587"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Prise en charge cloud-init pour les machines virtuelles dans Azure

**S’applique à :** :heavy_check_mark: Machines virtuelles Linux :heavy_check_mark: Groupes identiques flexibles 

Cet article décrit la prise en charge existante pour [cloud-init](https://cloudinit.readthedocs.io) destinée à la configuration d’une machine virtuelle ou de groupes de machines virtuelles identiques au moment du provisionnement dans Azure. Ces configurations cloud-init sont exécutées au premier démarrage une fois que les ressources ont été approvisionnées par Azure.  

L’approvisionnement de la machine virtuelle est le processus par lequel Azure transmet à votre machine virtuelle les valeurs des paramètres de création, tels que le nom d’hôte, le nom d’utilisateur, le mot de passe, etc. et les met à la disposition de la machine virtuelle au moment de son démarrage. Un « agent d’approvisionnement » consomme ces valeurs, configure la machine virtuelle et renvoie un rapport une fois terminé. 

Azure prend en charge deux agents d’approvisionnement : [cloud-init](https://cloudinit.readthedocs.io) et l’[agent Linux Azure (WALA)](../extensions/agent-linux.md).

## <a name="cloud-init-overview"></a>Présentation de cloud-init
[cloud-init](https://cloudinit.readthedocs.io) est une approche courante permettant de personnaliser une machine virtuelle Linux lors de son premier démarrage. Vous pouvez utiliser cloud-init pour installer des packages et écrire des fichiers, ou encore pour configurer des utilisateurs ou des paramètres de sécurité. cloud-init étant appelé pendant le processus de démarrage initial, aucune autre étape ni aucun agent ne sont nécessaires pour appliquer votre configuration.  Pour plus d’informations sur la façon de mettre correctement en forme vos fichiers `#cloud-config` ou d’autres entrées, consultez le [site de documentation cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  Les fichiers `#cloud-config` sont des fichiers texte encodés en base64.

cloud-init fonctionne aussi sur les différentes distributions. Par exemple, vous n’utilisez pas **apt-get install** ou **yum install** pour installer un package. Au lieu de cela, vous pouvez définir une liste des packages à installer, Après quoi cloud-init se charge d’utiliser automatiquement l’outil de gestion de package natif correspondant à la distribution que vous sélectionnez.

Nous travaillons activement avec nos partenaires de distribution Linux afin de mettre des images compatibles cloud-init à disposition sur Place de marché Azure. Ces images permettent à vos déploiements et configurations cloud-init de fonctionner de manière fluide avec des machines virtuelles et des groupes de machines virtuelles identiques. Dans un premier temps, nous collaborons avec les partenaires de distribution Linux agréés et vérifions en amont pour garantir le fonctionnement de cloud-init avec le système d’exploitation sur Azure, puis les packages sont mis à jour et rendus accessibles publiquement dans les référentiels de packages de la distribution. 

Il existe deux étapes pour rendre cloud-init disponible pour les systèmes d’exploitation de distribution Linux approuvés sur Azure, la prise en charge des packages, puis la prise en charge des images :
* « prise en charge des packages cloud-init sur Azure » documente quels packages cloud-init sont pris en charge ou en préversion. Vous pouvez donc utiliser ces packages avec le système d’exploitation dans une image personnalisée.
* « image compatible avec cloud-init » documente si l’image est déjà configurée pour utiliser cloud-init.


### <a name="canonical"></a>Canonical
| Éditeur/version| Offre | SKU | Version | image compatible avec cloud-init | prise en charge des packages cloud-init sur Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 20.04 |UbuntuServer |20.04-LTS |latest |Oui | Oui |
|Canonical 18.04 |UbuntuServer |18.04-LTS |latest |Oui | Oui |


### <a name="rhel"></a>RHEL
| Éditeur/version| Offre | SKU | Version | image compatible avec cloud-init | prise en charge des packages cloud-init sur Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7 |RHEL |7.7, 7.8, 7_9 |latest |Oui | Oui |
|RedHat 8 |RHEL |8.1, 8.2, 8_3, 8_4 |latest |Oui | Oui |

* Toutes les autres références SKU RedHat à partir de RHEL 7 (version 7.7) et RHEL 8 (version 8.1), notamment les images Gen1 et Gen2, sont provisionnées à l’aide de cloud-init. Les images RHEL 6 ne prennent pas en charge cloud-init. 


### <a name="centos"></a>CentOS
 Éditeur/version| Offre | SKU | Version | image compatible avec cloud-init | prise en charge des packages cloud-init sur Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7 |CentOS |7.7, 7.8, 7.9 |latest |Oui | Oui |
|OpenLogic 8 |CentOS |8.1, 8.2, 8.3 |latest |Oui | Oui |

* Toutes les autres références SKU CentOS à partir de CentOS 7 (version 7.7) et CentOS 8 (version 8.1), notamment les images Gen1 et Gen2, sont provisionnées à l’aide de cloud-init. Les images CentOS 6.10, 7.4, 7.5 et 7.6 ne prennent pas en charge cloud-init. 

> [!NOTE]
> OpenLogic s’appelle désormais Rogue Wave Software 



### <a name="oracle"></a>Oracle

 Éditeur/version| Offre | SKU | Version | image compatible avec cloud-init | prise en charge des packages cloud-init sur Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7 |Oracle Linux |77, 78, ol79 |latest |Oui | Oui |
|Oracle 8 |Oracle Linux |81, ol82, ol83-lvm, ol84-lvm |latest |Oui | Oui |

* Toutes les autres références SKU Oracle à partir d’Oracle 7 (version 7.7) et Oracle 8 (version 8.1), notamment les images Gen1 et Gen2, sont provisionnées à l’aide de cloud-init.


### <a name="suse-sles"></a>SUSE SLES

 Éditeur/version| Offre | SKU | Version | image compatible avec cloud-init | prise en charge des packages cloud-init sur Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
| SUSE 15 |SLES (SUSE Linux Enterprise Server) |sp1, sp2, sp3 |latest |Oui | Oui |
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sp5 |latest |Oui | Oui |

* Toutes les autres références SKU SUSE à partir de SLES 15 (sp1) et SLES 12 (sp5), notamment les images Gen1 et Gen2, sont provisionnées à l’aide de cloud-init.
* De plus, ces images sont également provisionnées avec cloud-init -


 Éditeur/version| Offre | Référence SKU / Version
|:--- |:--- |:---
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sles-{byos/sap/sap-byos}:12-sp4:2020.06.10
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sles-{byos/sap/sap-byos}:12-sp3:2020.06.10
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sles-{byos/sap/sap-byos}:12-sp2:2020.06.10
| SUSE 15 |SLES (SUSE Linux Enterprise Server) |manager-proxy-4-byosgen1:2020.06.10
| SUSE 15 |SLES (SUSE Linux Enterprise Server) |manager-server-4-byos:gen1:2020.06.10


### <a name="debian"></a>Debian
| Éditeur/version | Offre | SKU | Version | image compatible avec cloud-init | prise en charge des packages cloud-init sur Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
| debian (Gen1) |debian-10 | 10-cloudinit |10:0.20201013.422| Oui | Oui, prise en charge de la version du package : `20.2-2~deb10u1` |
| debian (Gen2) |debian-10 | 10-cloudinit-gen2 |0.20201013.422| Oui | Oui, prise en charge de la version du package : `20.2-2~deb10u1` |


Actuellement Azure Stack prend en charge l’approvisionnement d’images compatibles cloud-init.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Quelle est la différence entre cloud-init et l’agent Linux (WALA) ?
WALA est un agent spécifique à la plateforme Azure, qui est utilisé pour approvisionner et configurer des machines virtuelles et gérer des [extensions Azure](../extensions/features-linux.md). 

Nous améliorons la tâche de configuration des machines virtuelles afin d’utiliser cloud-init au lieu de l’agent Linux, ceci pour permettre aux clients cloud-init existants de s’appuyer sur leurs scripts cloud-init actuels ou aux nouveaux clients de tirer parti de la riche fonctionnalité de configuration de cloud-init. Si vous possédez des investissements existants dans des scripts cloud-init pour la configuration de systèmes Linux, **aucun autre paramètre n’est requis** pour permettre aux processus cloud-init de les traiter. 

cloud-init ne peut pas traiter les extensions Azure. Par conséquent, WALA est toujours requis dans l’image pour traiter les extensions, mais son code d’approvisionnement devra être désactivé. Pour les images de distribution Linux approuvées qui sont converties en approvisionnement par cloud-init, elles seront dotées de WALA et configurées correctement.

Lorsque vous créez une machine virtuelle, si vous n’incluez pas le commutateur `--custom-data` de l’interface de ligne de commande Azure au moment de l’approvisionnement, cloud-init ou WALA prend les paramètres minimaux d’approvisionnement de machines virtuelles requis pour configurer la machine virtuelle et exécuter le déploiement avec les valeurs par défaut.  Si vous référencez la configuration cloud-init avec le commutateur `--custom-data`, tout ce qui est contenu dans vos données personnalisées sera disponible pour cloud-init au démarrage de la machine virtuelle.

Les configurations cloud-init appliquées aux machines virtuelles n’étant soumises à aucune contrainte de temps, aucune mise en échec de déploiement par expiration de ces dernières n’est possible. Cela n’est pas le cas pour WALA : si vous modifiez les valeurs par défaut de WALA pour traiter les données personnalisées, cela ne doit pas dépasser le temps total d’approvisionnement de machines virtuelles de 40 minutes. Si tel est le cas, la création de la machine virtuelle échouera.

## <a name="cloud-init-vm-provisioning-without-a-udf-driver"></a>Provisionnement de machines virtuelles cloud-init sans pilote UDF  
À partir de cloud-init 21.2, vous pouvez utiliser cloud-init pour provisionner une machine virtuelle dans Azure sans pilote UDF. Si aucun pilote UDF n’est disponible dans l’image, cloud-init utilise les métadonnées disponibles dans Azure Instance Metadata Service pour provisionner la machine virtuelle. Notez que cette option fonctionne uniquement pour la clé SSH et les [données utilisateur](../user-data.md). Pour passer un mot de passe ou des données personnalisées à une machine virtuelle durant son provisionnement, vous devez utiliser un pilote UDF.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Déploiement d’une machine virtuelle compatible cloud-init
Il est aussi simple de déployer une machine virtuelle cloud-init que de référencer une distribution compatible cloud-init durant le déploiement.  Les gestionnaires de la distribution Linux doivent décider d’activer cloud-init et de l’intégrer dans leurs images publiées Azure. Une fois que vous avez confirmé que l’image que vous souhaitez déployer est compatible cloud-init, vous pouvez utiliser l’interface de ligne de commande Azure pour le déploiement. 

La première étape du déploiement de cette image est la création d’un groupe de ressources avec la commande [az group create](/cli/azure/group). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Il est ensuite question de créer un fichier dans l’interpréteur de commandes actuel, nommé *cloud-init.txt*, et de le coller dans la configuration suivante. Pour cet exemple, créez le fichier dans Cloud Shell et non sur votre ordinateur local. Vous pouvez utiliser l’éditeur de votre choix. Entrez `sensible-editor cloud-init.txt` pour créer le fichier et afficher la liste des éditeurs disponibles. Choisissez le n°1 pour utiliser l’éditeur **nano**. Vérifiez que l’intégralité du fichier cloud-init est copiée, en particulier la première ligne :

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
> [!NOTE]
> cloud-init a plusieurs [types d’entrée](https://cloudinit.readthedocs.io/en/latest/topics/format.html). Il utilise la première ligne de customData/userData pour indiquer le traitement de l’entrée. Par exemple, `#cloud-config` indique que le contenu doit être traité comme une configuration cloud-init.

Appuyez sur <kbd>Ctrl + X</kbd> pour quitter le fichier, saisissez <kbd>y</kbd> pour l’enregistrer, puis appuyez sur <kbd>Entrée</kbd> pour confirmer le nom lors de la sortie.

Enfin, vous devez créer une machine virtuelle avec la commande [az vm create](/cli/azure/vm). 

L’exemple suivant crée une machine virtuelle nommée `centos74` et crée des clés SSH si elles n’existent pas déjà dans un emplacement de clé par défaut. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`.  Utilisez le paramètre `--custom-data` à transmettre dans votre fichier de configuration cloud-init. Indiquez le chemin complet vers la configuration *cloud-init.txt* si vous avez enregistré le fichier en dehors de votre répertoire de travail actuel. 

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Lorsque la machine virtuelle est créée, l’interface de ligne de commande Azure affiche des informations spécifiques à votre déploiement. Notez la valeur de `publicIpAddress`. Cette adresse est utilisée pour accéder à la machine virtuelle.  Vous devez patienter un certain temps que la machine virtuelle soit créée, que les packages soient installés et que l’application démarre. Certaines tâches en arrière-plan continuent à s’exécuter une fois que l’interface CLI Azure vous renvoie à l’invite de commandes. Vous pouvez exécuter une commande SSH dans la machine virtuelle et suivre la procédure décrite dans la section de résolution des problèmes afin d’afficher les journaux d’activité cloud-init. 

Vous pouvez également déployer une machine virtuelle compatible avec cloud-init en transférant les [paramètres dans le modèle ARM](../../azure-resource-manager/templates/deploy-cli.md#inline-parameters).

## <a name="troubleshooting-cloud-init"></a>Résolution des problèmes cloud-init
Une fois la machine virtuelle configurée, cloud-init est exécuté dans l’ensemble des modules et des scripts définis dans `--custom-data` pour la configuration de la machine virtuelle.  Si vous devez corriger des erreurs ou des omissions dans la configuration, vous devez chercher le nom du module (`disk_setup` ou `runcmd` par exemple) dans le journal cloud-init, situé dans **/var/log/cloud-init.log**.

> [!NOTE]
> Toutes les défaillances de module n’entraînent pas d’échec irrécupérable de configuration cloud-init. Par exemple, si vous utilisez le module `runcmd`, si le script est mis en échec, cloud-init fera tout de même état d’une réussite de configuration permise par l’exécution du module runcmd.

Pour plus d’informations sur la journalisation de cloud-init, consultez la [documentation cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html). 

## <a name="next-steps"></a>Étapes suivantes

[Résoudre les problèmes liés à cloud-init](cloud-init-troubleshooting.md).


Pour obtenir des exemples cloud-init de modifications de configuration, consultez les documents suivants :
 
- [Ajouter un utilisateur Linux supplémentaire à une machine virtuelle](cloudinit-add-user.md)
- [Exécuter un gestionnaire de package pour mettre à jour les packages existants au premier démarrage](cloudinit-update-vm.md)
- [Use cloud-init to set hostname for a Linux VM in Azure](cloudinit-update-vm-hostname.md) (Utiliser cloud-init pour définir un nom d’hôte pour une machine virtuelle Linux dans Azure) 
- [Installer un package d’application, mettre à jour des fichiers de configuration et injecter des clés](tutorial-automate-vm-deployment.md)
