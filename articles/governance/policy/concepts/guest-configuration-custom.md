---
title: Changements du comportement dans PowerShell Desired State Configuration pour la configuration d’invité
description: Cet article fournit une présentation de la plateforme utilisée pour modifier la configuration de machines via Azure Policy.
ms.date: 05/31/2021
ms.topic: how-to
ms.openlocfilehash: 6118ec0ce0bb8b0296153d32dbad559a6b53ebb8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261859"
---
# <a name="changes-to-behavior-in-powershell-desired-state-configuration-for-guest-configuration"></a>Changements du comportement dans PowerShell Desired State Configuration pour la configuration d’invité

Avant de commencer, nous vous conseillons de lire la présentation de la [configuration d’invité](./guest-configuration.md).

[Un guide vidéo de ce document est disponible](https://youtu.be/nYd55FiKpgs).

La configuration d’invité utilise [Desired State Configuration (DSC)](/powershell/scripting/dsc/overview/overview) version 3 pour auditer et configurer des machines. La configuration DSC définit l’état dans lequel la machine doit se trouver. Il y a de nombreuses différences notables dans la façon dont DSC est implémenté dans la configuration d’invité.

## <a name="guest-configuration-uses-powershell-7-cross-platform"></a>La configuration d’invité utilise PowerShell 7 multiplateforme

La configuration d’invité est conçue de façon à ce que l’expérience de gestion de Windows et Linux puisse être cohérente. Dans les deux environnements de système d’exploitation, une personne disposant d’une connaissance de PowerShell DSC peut créer et publier des configurations en mobilisant ses compétences en matière d’écriture de script.

La configuration d’invité utilise uniquement PowerShell DSC version 3 et ne s’appuie pas sur l’implémentation précédente de[DSC pour Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) ou des fournisseurs « nx » inclus dans ce référentiel.

La configuration invité fonctionne dans PowerShell 7.1.3 pour Windows et PowerShell 7.2 préversion 6 pour Linux. Depuis la version 7.2, le module `PSDesiredStateConfiguration` ne fait plus partie de l’installation de PowerShell, mais est installé en tant que [module de PowerShell Gallery](https://www.powershellgallery.com/packages/PSDesiredStateConfiguration).

## <a name="multiple-configurations"></a>Configurations multiples

La configuration d’invité prend en charge l’attribution de plusieurs configurations à la même machine. Aucune action particulière n’est requise dans le système d’exploitation de l’extension de configuration d’invité. Il n’est pas nécessaire de configurer des [configurations partielles](/powershell/scripting/dsc/pull-server/partialConfigs).

## <a name="dependencies-are-managed-per-configuration"></a>Les dépendances sont gérées par configuration

Quand une configuration est [ajoutée à un package au moyen des outils disponibles](../how-to/guest-configuration-create.md), les dépendances requises pour la configuration sont incluses dans un fichier .zip.
Les machines extraient le contenu dans un dossier unique pour chaque configuration.
L’agent fourni par l’extension de configuration d’invité crée une session PowerShell dédiée pour chaque configuration, en utilisant `$Env:PSModulePath` pour autoriser le chargement automatique des modules uniquement dans le chemin où le package a été extrait.

Ce changement présente de multiples avantages.

- Il est possible d’utiliser des versions de modules différentes pour chaque configuration sur une même machine.
- Quand une configuration est supprimée sur une machine, le dossier entier dans lequel elle avait été extraite est supprimé en toute sécurité par l’agent sans qu’il soit nécessaire de gérer les dépendances partagées entre les configurations.
- Il n’est pas nécessaire de gérer plusieurs versions d’un module dans un service central.
  
## <a name="artifacts-are-managed-as-packages"></a>Les artefacts sont gérés sous forme de packages

La fonctionnalité Azure Automation State Configuration comprend la gestion des artefacts pour les modules et les scripts de configuration. Une fois que les deux sont publiés dans le service, le script peut être compilé au format MOF. De la même façon, le serveur Pull Windows nécessitait également une gestion des configurations et des modules au niveau de l’instance du service web. Au contraire, l’extension DSC offre un modèle simplifié où tous les artefacts sont regroupés dans un package qui est stocké à un emplacement accessible à partir de la machine cible via une requête HTTPS (Stockage Blob Azure est l’option la plus courante).

La configuration d’invité utilise uniquement le modèle simplifié où tous les artefacts sont regroupés dans un package accessible à partir de la machine cible via HTTPS.
Il n’est pas nécessaire de publier des modules ou des scripts, ni de les compiler dans le service. L’un des changements est que le package doit toujours inclure un MOF compilé. Il n’est pas possible d’inclure un fichier de script dans le package et de le compiler sur la machine cible.

## <a name="maximum-size-of-custom-configuration-package"></a>Taille maximale du package de configuration personnalisée

Dans la configuration d’état d’Azure Automation, les configurations DSC étaient [limitées en taille](../../../automation/automation-dsc-compile.md#compile-your-dsc-configuration-in-windows-powershell).
La configuration d’invité prend en charge une taille de package totale de 100 Mo (avant compression). Il n’existe aucune limite spécifique quant à la taille du fichier MOF au sein du package.

## <a name="configuration-mode-is-set-in-the-package-artifact"></a>Le mode de configuration est défini dans l’artefact de package

Lors de la création du package de configuration, le mode est défini à l’aide des options suivantes :

- _Audit_ : vérifie la conformité d’une machine. Aucune modification n’est apportée.
- _AuditandSet_ : vérifie et corrige l’état de conformité de la machine.
  Des modifications sont apportées si la machine n’est pas conforme.

Le mode est défini dans le package plutôt que dans le service [Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings), car il peut différer selon la configuration quand plusieurs configurations sont attribuées.

## <a name="parameter-support-through-azure-resource-manager"></a>Prise en charge de paramétrage via Azure Resource Manager

Les paramètres définis par le tableau de propriétés `configurationParameter` dans les [attributions de configuration d’invité](guest-configuration-assignments.md) remplacent le texte statique dans un fichier MOF de configuration lorsque celui-ci est stocké sur une machine. Les paramètres permettent à un opérateur de contrôler la personnalisation et les modifications à partir de l’API de service sans avoir besoin d’exécuter des commandes à l’intérieur de la machine.

Les paramètres d’Azure Policy qui transmettent des valeurs à des attributions de configuration d’invité doivent être de type _chaîne_. Il n’est pas possible de passer des tableaux en paramètre, même si la ressource DSC les prend en charge.

## <a name="trigger-set-from-outside-machine"></a>Déclencheur défini à partir d’une machine extérieure

Un problème rencontré dans les versions précédentes de DSC était de corriger la dérive à grande échelle en utilisant peu de code personnalisé, et la dépendance de connexions à distance WinRM. Une configuration d’invité résout ce problème. Les utilisateurs d’une configuration d’invité contrôlent la correction de la dérive via une [correction à la demande](./guest-configuration-policy-effects.md#remediation-on-demand-applyandmonitor).

## <a name="sequence-includes-get-method"></a>La séquence contient une méthode Get

Quand une configuration d’invité audite ou configure une machine, la même séquence d’événements est utilisée pour Windows et Linux. Le changement de comportement notable est que le service appelle la méthode `Get` pour retourner des détails sur l’état de la machine.

1. L’agent exécute d’abord `Test` pour déterminer si la configuration est dans l’état approprié.
1. Si le package est défini sur `Audit`, la valeur booléenne retournée par la fonction détermine si l’état Azure Resource Manager de l’attribution d’invité doit être conforme ou non conforme.
1. Si le package est défini sur `AuditandSet`, la valeur booléenne détermine s’il faut corriger la machine en appliquant la configuration à l’aide de la méthode `Set`.
   Si la méthode `Test` retourne False, la commande `Set` est exécutée. Si la méthode `Test` retourne True, la commande `Set` n’est pas exécutée.
1. Enfin, le fournisseur exécute `Get` pour retourner l’état actuel de chaque paramètre, afin que des détails soient disponibles à la fois sur la raison pour laquelle un machine n’est pas conforme et pour confirmer que l’état actuel est conforme.

## <a name="special-requirements-for-get"></a>Exigences particulières pour la méthode Get

La méthode `Get` de la fonction a des exigences particulières pour la configuration d’invité Azure Policy qui n’existaient pas pour Windows PowerShell Desired State Configuration.

- La table de hachage retournée doit inclure une propriété nommée **Reasons**.
- La propriété Reasons doit être un tableau.
- Chaque élément du tableau doit être une table de hachage avec les clés **Code** et **Phrase**.
- Aucune autre valeur que la table de hachage ne doit être renvoyée.

La propriété Reasons est utilisée par le service pour normaliser la manière dont les informations sont présentées. Vous pouvez considérer chaque élément dans la propriété Reasons comme une « raison » pour laquelle la ressource est conforme ou non. La propriété est un tableau, car une ressource peut ne pas être conforme pour plusieurs raisons.

Les propriétés **Code** et **Phrase** sont attendues par le service. Lorsque vous créez une ressource personnalisée, définissez le texte (en général stdout) que vous souhaitez afficher pour expliquer pourquoi la ressource n’est pas conforme à la valeur de **Phrase**.
**Code** a des exigences de mise en forme spécifiques, visant à afficher clairement les informations sur la ressource utilisée pour effectuer l’audit. Cette solution rend la configuration d’invité extensible. Vous pouvez exécuter n’importe quelle commande tant que la sortie peut être retournée sous forme de valeur de chaîne pour la propriété **Phrase**.

- **Code** (chaîne) : nom de la ressource, répété, puis un nom abrégé sans espaces comme identificateur de la raison. Ces trois valeurs doivent être séparées par des points-virgules, sans espaces.
  - Par exemple, `registry:registry:keynotpresent`.
- **Phrase** (chaîne) : texte lisible par l’utilisateur expliquant pourquoi le paramètre n’est pas conforme.
  - Par exemple, `The registry key $key isn't present on the machine.`.

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

Lorsque vous utilisez des outils de ligne de commande pour obtenir des informations qui seront renvoyées dans Get, vous pouvez constater que l’outil renvoie une sortie à laquelle vous ne vous attendiez pas. Même si vous capturez la sortie dans PowerShell, la sortie a également pu être écrite dans une erreur standard. Pour éviter ce problème, pensez à rediriger la sortie vers la valeur Null.

### <a name="the-reasons-property-embedded-class"></a>Classe incorporée de la propriété Reasons

Dans les ressources basées sur un script (Windows uniquement), la classe Reasons est incluse dans le fichier MOF de schéma comme suit.

```mof
[ClassVersion("1.0.0.0")]
class Reason
{
  [Read] String Phrase;
  [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
  [Key, Description("Example description")] String Example;
  [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

Dans les ressources basées sur une classe (Windows et Linux), la classe `Reason` est incluse dans le module PowerShell comme suit. Linux respectant la casse, le « C » de code et le « P » de Phrase doivent être en majuscules.

```powershell
enum ensure {
  Absent
  Present
}

class Reason {
  [DscProperty()]
  [string] $Code

  [DscProperty()]
  [string] $Phrase
}

[DscResource()]
class Example {

  [DscProperty(Key)]
  [ensure] $ensure

  [DscProperty()]
  [Reason[]] $Reasons

  [Example] Get() {
    # return current current state
  }

  [void] Set() {
    # set the state
  }

  [bool] Test() {
    # check whether state is correct
  }
}

```

Si la ressource requiert certaines propriétés, celles-ci doivent également être retournées par `Get` en parallèle avec la classe `Reason`. Si `Reason` n’est pas inclus, le service inclut un comportement « catch-all » qui compare les valeurs entrées dans `Get` et les valeurs retournées par `Get`, et fournit une comparaison détaillée comme `Reason`.

## <a name="configuration-names"></a>Noms de configuration

Le nom de la configuration personnalisée doit être cohérent partout. Le nom du fichier `.zip` du package de contenu, le nom de configuration dans le fichier MOF et le nom d’attribution d’invité dans le modèle Azure Resource Manager doivent être identiques.

## <a name="common-dsc-features-not-available-during-guest-configuration-public-preview"></a>Fonctionnalités DSC courantes non disponibles pendant la préversion publique de la configuration d’invité

Dans la préversion publique, la configuration d’invité ne prend pas en charge la [spécification de dépendances entre machines](/powershell/scripting/dsc/configurations/crossnodedependencies) avec les ressources « WaitFor* ». Il n’est pas possible pour une machine de surveiller une autre machine en attendant qu’elle atteigne un état déterminé avant de progresser.

La [gestion du redémarrage](/powershell/scripting/dsc/configurations/reboot-a-node) n’est pas disponible dans la préversion publique de la configuration d’invité. Notamment, l’état `$global:DSCMachineStatus` n’est pas disponible. Les configurations ne peuvent pas redémarrer un nœud au cours ou à la fin d’une configuration.

## <a name="known-compatibility-issues-with-supported-modules"></a>Problèmes de compatibilité connus avec les modules pris en charge

Le module `PsDscResources` dans PowerShell Gallery et le module `PSDesiredStateConfiguration` fourni avec Windows sont pris en charge par Microsoft, et constituent un ensemble de ressources couramment utilisé pour DSC. En attendant que le module `PSDscResources` soit mis à jour pour DSCv3, vous devez être conscient des problèmes de compatibilité connus suivants.

- N’utilisez pas les ressources du module `PSDesiredStateConfiguration` fourni avec Windows. Au lieu de cela, passez aux `PSDscResources`.
- N’utilisez pas les ressources `WindowsFeature` et `WindowsFeatureSet` dans `PsDscResources`. Au lieu de cela, passez aux ressources `WindowsOptionalFeature` et `WindowsOptionalFeatureSet`.
  
Les ressources « nx » pour Linux qui étaient incluses dans le référentiel [DSC for Linux](https://github.com/microsoft/PowerShell-DSC-for-Linux/tree/master/Providers) ont été écrites dans une combinaison des langages C et Python. Comme la voie à suivre pour DSC for Linux consiste à utiliser PowerShell, les ressources « nx » existantes ne sont pas compatibles avec DSCv3. Jusqu’à ce qu’un nouveau module contenant les ressources prises en charge pour Linux soit disponible, il est nécessaire de créer des ressources personnalisées.

## <a name="coexistence-with-dsc-version-3-and-previous-versions"></a>Coexistence avec DSC version 3 et versions antérieures

DSC version 3 dans la configuration d’invité peut coexister avec des versions plus anciennes installées dans [Windows](/powershell/scripting/dsc/getting-started/wingettingstarted) et [Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).
Les implémentations sont séparées. Toutefois, étant donné qu’il n’y a pas de détection des conflits entre les versions de DSC, ne tentez pas de gérer les mêmes paramètres.

## <a name="next-steps"></a>Étapes suivantes

- Lisez la [Présentation de la configuration d’invité](./guest-configuration.md).
- Configurez un [environnement de développement](../how-to/guest-configuration-create-setup.md) de package de configuration d’invité personnalisé.
- [Créez un artefact de package](../how-to/guest-configuration-create.md) pour la configuration d’invité.
- [Testez l’artefact de package](../how-to/guest-configuration-create-test.md) à partir de votre environnement de développement.
- Utilisez le module `GuestConfiguration` afin de [créer une définition Azure Policy](../how-to/guest-configuration-create-definition.md) pour une gestion à grande échelle de votre environnement.
- [Attribuez votre définition de stratégie personnalisée](../assign-policy-portal.md) à l’aide du portail Azure.
- Découvrez comment visualiser les [informations relatives à la conformité pour les attributions de stratégie de configuration d’invité](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration).
