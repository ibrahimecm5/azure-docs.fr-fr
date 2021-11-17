---
title: Journalisation du SDK Speech – Service Speech
titleSuffix: Azure Cognitive Services
description: Découvrez-en plus sur l’activation de la journalisation dans le SDK Speech (C++, C#, Python, Objective-C, Java).
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: ba3f5234c3f11cdd5c2e302679bbb6a79ec0c4f6
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550208"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Activer la journalisation dans le SDK Speech

La journalisation dans un fichier est une fonctionnalité facultative pour le SDK Speech. Pendant le développement, la journalisation fournit des informations et des diagnostics supplémentaires en provenance des composants de base du SDK Speech. Elle peut être activée en attribuant à la propriété `Speech_LogFilename` d’un objet de configuration Speech l’emplacement et le nom du fichier journal. La journalisation est gérée par une classe statique dans la bibliothèque native du kit de développement logiciel (SDK) Speech. Vous pouvez activer la journalisation pour n’importe quelle instance de module de reconnaissance ou de synthétiseur du SDK Speech. Toutes les instances dans le même processus écrivent leurs entrées de journal dans le même fichier journal.

> [!NOTE]
> La journalisation est disponible depuis la version 1.4.0 du SDK Speech dans tous les langages de programmation pris en charge par le SDK Speech, à l’exception de JavaScript.

## <a name="sample"></a>Exemple

Le nom du fichier journal est spécifié dans un objet de configuration. En prenant `SpeechConfig` comme exemple et en supposant que vous avez créé une instance appelée `config` :

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

```go
import ("github.com/Microsoft/cognitive-services-speech-sdk-go/common")

config.SetProperty(common.SpeechLogFilename, "LogfilePathAndName")
```

Vous pouvez créer un module de reconnaissance à partir de l’objet de configuration. La journalisation est alors activée pour tous les modules de reconnaissance.

> [!NOTE]
> Si vous créez un `SpeechSynthesizer` à partir de l’objet de configuration, la journalisation n’est pas activée. S’il s’avère qu’elle est quand même activée, vous recevez aussi les diagnostics de `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Créer un fichier journal sur différentes plateformes

Pour Windows ou Linux, le fichier journal peut figurer dans n’importe quel chemin pour lequel l’utilisateur dispose d’une autorisation d’accès en écriture. Les autorisations d’accès en écriture aux emplacements du système de fichiers des autres systèmes d’exploitation peuvent être limitées ou restreintes par défaut.

### <a name="universal-windows-platform-uwp"></a>Plateforme Windows universelle (UWP)

Les applications UWP doivent placer les fichiers journaux à l’un des emplacements de données d’application (locaux, itinérants ou temporaires). Un fichier journal peut être créé dans le dossier d’application local :

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Au sein d’une application UWP Unity, un fichier journal peut être créé en utilisant le dossier du chemin des données persistantes de l’application, comme suit :

```csharp
#if ENABLE_WINMD_SUPPORT
    string logFile = Application.persistentDataPath + "/logFile.txt";
    config.SetProperty(PropertyId.Speech_LogFilename, logFile);
#endif
```
Pour plus d’informations sur les autorisations d’accès aux fichiers dans les applications UWP, consultez [Autorisations d’accès aux fichiers](/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Vous pouvez enregistrer un fichier journal dans un espace de stockage interne, externe ou dans le répertoire du cache. Les fichiers créés dans l’espace de stockage interne ou dans le répertoire du cache sont privés pour l’application. Il est préférable de créer un fichier journal dans un espace de stockage externe.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

Le code ci-dessus enregistre un fichier journal dans l’espace de stockage externe à la racine d’un répertoire propre à l’application. Un utilisateur peut accéder au fichier avec le gestionnaire de fichiers (généralement dans `Android/data/ApplicationName/logfile.txt`). Le fichier est supprimé quand l’application est désinstallée.

Vous devez aussi demander une autorisation `WRITE_EXTERNAL_STORAGE` dans le fichier manifeste :

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Au sein d’une application Android Unity, le fichier journal peut être créé en utilisant le dossier du chemin des données persistantes de l’application, comme suit :

```csharp
string logFile = Application.persistentDataPath + "/logFile.txt";
config.SetProperty(PropertyId.Speech_LogFilename, logFile);
```
De plus, vous avez aussi besoin de définir l’autorisation d’écriture dans les paramètres de votre lecteur Unity pour Android sur « External (SDCard) ». Le journal est écrit dans un répertoire que vous pouvez obtenir à l’aide d’un outil comme AndroidStudio Device File Explorer. Le chemin exact du répertoire peut varier d’un appareil Android à l’autre. Son emplacement est généralement le répertoire `sdcard/Android/data/your-app-packagename/files`.

Vous trouverez des informations supplémentaires sur le stockage de données et de fichiers pour les applications Android [ici](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Seuls les répertoires à l’intérieur du bac à sable d’application sont accessibles. Des fichiers peuvent être créés dans les répertoires documents, library et temp. Les fichiers contenus dans le répertoire documents peuvent être mis à la disposition d’un utilisateur. 

Si vous utilisez Objective-C sur iOS, utilisez l’extrait de code suivant pour créer un fichier journal dans le répertoire document de l’application :

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Pour accéder à un fichier créé, ajoutez les propriétés ci-dessous à la liste de propriétés `Info.plist` de l’application :

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Si vous utilisez Swift sur iOS, utilisez l’extrait de code suivant pour activer les journaux :
```swift
let documentsDirectoryPathString = NSSearchPathForDirectoriesInDomains(.documentDirectory, .userDomainMask, true).first!
let documentsDirectoryPath = NSURL(string: documentsDirectoryPathString)!
let logFilePath = documentsDirectoryPath.appendingPathComponent("swift.log")
self.speechConfig!.setPropertyTo(logFilePath!.absoluteString, by: SPXPropertyId.speechLogFilename)
```

Vous trouverez des informations complémentaires sur le système de fichiers iOS [ici](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="logging-with-multiple-recognizers"></a>Journalisation avec plusieurs modules de reconnaissance

Même si un chemin de sortie de fichier journal est spécifié comme propriété de configuration dans un objet `SpeechRecognizer` ou un autre objet de SDK, la journalisation de SDK est une fonctionnalité singleton au *niveau processus* sans concept d’instances individuelles. Vous pouvez la considérer comme le constructeur `SpeechRecognizer` (ou un constructeur similaire) appelant implicitement une routine « Configurer la journalisation globale » statique et interne avec les données de propriété disponibles dans le `SpeechConfig` correspondant.

Ainsi, par exemple, vous ne pouvez pas configurer six modules de reconnaissance parallèles pour générer des sorties simultanément dans six fichiers distincts. Au lieu de cela, le dernier module de reconnaissance créé configure l’instance de journalisation globale pour qu’elle génère une sortie dans le fichier spécifié dans ses propriétés de configuration et l’ensemble de la journalisation du SDK sera émis dans ce fichier.

Ceci signifie également que la durée de vie de l’objet qui a configuré la journalisation n’est pas liée à la durée de la journalisation. La journalisation ne s’arrêtera pas en réponse à la publication d’un objet de SDK et se poursuivra tant qu’aucune nouvelle configuration de journalisation ne sera fournie. Quand une journalisation au niveau processus a démarré, vous pouvez l’arrêter en définissant le chemin du fichier journal sur une chaîne vide à la création d’un objet.

Pour réduire la confusion potentielle durant la configuration de la journalisation pour plusieurs instances, il peut être utile de considérer le contrôle de la journalisation indépendamment des objets effectuant effectivement le travail. Voici un exemple de paire de routines d’assistance :

```cpp
void EnableSpeechSdkLogging(const char* relativePath)
{
    auto configForLogging = SpeechConfig::FromSubscription("unused_key", "unused_region");
    configForLogging->SetProperty(PropertyId::Speech_LogFilename, relativePath);
    auto emptyAudioConfig = AudioConfig::FromStreamInput(AudioInputStream::CreatePushStream());
    auto temporaryRecognizer = SpeechRecognizer::FromConfig(configForLogging, emptyAudioConfig);
}

void DisableSpeechSdkLogging()
{
    EnableSpeechSdkLogging("");
}
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Explorer nos exemples sur GitHub](https://aka.ms/csspeech/samples)
