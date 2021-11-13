---
description: Dans ce tutoriel, vous allez apprendre à utiliser le composite d’appel sur iOS
author: palatter
ms.author: palatter
ms.date: 10/10/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: bc61f84c8ad131fec503ac81b84d70af881dfb02
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130288433"
---
## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un Mac exécutant [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), ainsi qu’un certificat de développeur valide installé dans votre trousseau [CocoaPods](https://cocoapods.org/) doit également être installé pour permettre la récupération des dépendances.
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../../create-communication-resource.md).
- Jeton Azure Communication Services. [Consultez l’exemple.](../../../identity/quick-create-identity.md)

## <a name="setting-up"></a>Configuration

### <a name="creating-the-xcode-project"></a>Création du projet Xcode

Dans Xcode, créez un projet iOS et sélectionnez le modèle **App**. Nous utiliserons les plans conceptuels UIKit. Vous n’allez pas créer de tests au cours de ce guide démarrage rapide. N’hésitez pas à décocher **Inclure des tests**.

![Capture d’écran montrant la sélection du modèle Nouveau projet dans Xcode.](../../media/xcode-new-project-template-select.png)

Nommez le projet `UILibraryQuickStart`, puis sélectionnez `Storyboard` dans la liste déroulante `Interface`.

![Capture d’écran montrant les détails du nouveau projet dans Xcode.](../../media/xcode-new-project-details.png)

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installer le package et les dépendances avec CocoaPods

1. Créez un Podfile dans le répertoire racine de votre projet en exécutant `pod init`.
1. 
1. Ajoutez le code suivant à votre Podfile :

```
source 'https://github.com/CocoaPods/Specs.git'
source 'https://github.com/Azure/AzurePrivatePodspecs.git'

platform :ios, '13.0'

target 'UILibraryQuickStart' do
    use_frameworks!
    pod 'AzureCommunicationUI', '1.0.0-alpha.1'
end
```

3. Exécutez `pod install --repo-update`. (Ce processus peut prendre 10 à 15 minutes.)
4. Ouvrez le fichier `.xcworkspace` généré avec Xcode.

### <a name="request-access-to-the-microphone-camera-etc"></a>Demander l’accès au microphone, l’appareil photo, etc.

Pour accéder au matériel de l’appareil, mettez à jour la liste des propriétés d’informations de votre application. Définissez une valeur `string` comme valeur associée qui sera incluse dans la boîte de dialogue affichée par le système pour demander l’accès à l’utilisateur.

Cliquez avec le bouton droit sur l’entrée `Info.plist` de l’arborescence du projet, puis sélectionnez **Open As** > **Source Code**. Ajoutez les lignes suivantes dans la section `<dict>` tout en haut, puis enregistrez le fichier.

```xml
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

### <a name="turn-off-bitcode"></a>Désactiver `Bitcode`
Définissez l’option `Enable Bitcode` sur `No` dans les paramètres `Build Settings` du projet. Pour trouver ce paramètre, vous devez modifier le filtre en remplaçant `Basic` par `All`. Vous pouvez également utiliser la barre de recherche située à droite.

![Capture d’écran montrant l’option BitCode dans Xcode.](../../media/xcode-bitcode-option.png)

## <a name="initialize-composite"></a>Initialiser le composite

Accédez à « ViewController ». Ici, nous allons placer le code suivant afin d’initialiser nos composants composites pour l’appel. Remplacez `<GROUP_CALL_ID>` par l’ID de votre groupe d’appels ou `UUID()` pour en générer un. Remplacez également `<DISPLAY_NAME>` par votre nom, et `<USER_ACCESS_TOKEN>` par votre jeton.

```swift
import UIKit
import AzureCommunicationCalling
import CallingComposite

class ViewController: UIViewController {

    private var callComposite: CallComposite?

    override func viewDidLoad() {
        super.viewDidLoad()

        let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
        button.contentEdgeInsets = UIEdgeInsets(top: 10.0, left: 20.0, bottom: 10.0, right: 20.0)
        button.layer.cornerRadius = 10
        button.backgroundColor = .systemBlue
        button.setTitle("Start Experience", for: .normal)
        button.addTarget(self, action: #selector(startCallComposite), for: .touchUpInside)

        button.translatesAutoresizingMaskIntoConstraints = false
        self.view.addSubview(button)
        button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
    }

    @objc private func startCallComposite() {
        let callCompositeOptions = CallCompositeOptions()

        callComposite = CallComposite(withOptions: callCompositeOptions)

        let communicationTokenCredential = try! CommunicationTokenCredential(token: "<USER_ACCESS_TOKEN>")

        let options = GroupCallOptions(communicationTokenCredential: communicationTokenCredential,
                                       displayName: "<DISPLAY_NAME>",
                                       groupId: "<GROUP_CALL_ID>")
        callComposite?.launch(with: options)
    }
}
```

## <a name="run-the-code"></a>Exécuter le code

Vous pouvez générer et exécuter votre application sur un simulateur iOS en sélectionnant **Product** > **Run** ou en utilisant le raccourci clavier (&#8984;-R).

1) Appuyez sur `Start Experience`.
2) Acceptez les autorisations audio, puis sélectionnez les paramètres de l’appareil, du micro et de la vidéo.
3) Appuyez sur `Start Call`.

![Apparence finale de l’application du guide de démarrage rapide](../../media/quick-start-calling-composite-running-ios.gif)

## <a name="sample-application-code-can-be-found-here"></a>Un exemple de code d’application est disponible [ici](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/ui-library-quickstart)

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités de la bibliothèque cliente d’interface utilisateur Azure Communication Services :

| Name                                                                        | Description                                                                                  |
| --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| [CallComposite](#create-call-composite) | Le composite affiche une expérience d’appel avec une galerie de participants et des contrôles. |
| [CallCompositeOptions](#create-call-composite) | Inclut des options telles que la configuration du thème et le gestionnaire d’événements. |
| [CallCompositeEventsHandler](#subscribe-to-events-from-callcomposite) | Vous permet de recevoir des événements en provenance du composite. |
| [GroupCallOptions](#group-call) | Options permettant de rejoindre un appel de groupe, par exemple groupId. |
| [TeamsMeetingOptions](#teams-meeting) | Options permettant de rejoindre une réunion Teams, par exemple le lien de réunion. |
| [ThemeConfiguration](#apply-theme-configuration) | Vous permet de personnaliser le thème. |

## <a name="ui-library-functionality"></a>Fonctionnalités de la bibliothèque d’IU

### <a name="create-call-composite"></a>Créer un composite d’appel

Initialisez une instance de `CallCompositeOptions` et une instance de `CallComposite` dans la fonction `startCallComposite`.

```swift
@objc private func startCallComposite() {
    let callCompositeOptions = CallCompositeOptions()

    callComposite = CallComposite(withOptions: callCompositeOptions)
}
```

### <a name="setup-authentication"></a>Configurer l’authentification

Initialisez une instance de `CommunicationTokenCredential` dans la fonction `startCallComposite`. Remplacez `<USER_ACCESS_TOKEN>` par votre jeton.

```swift
let communicationTokenCredential = try! CommunicationTokenCredential(token: "<USER_ACCESS_TOKEN>")
```

Consultez la documentation sur les [jetons d’accès utilisateur](../../../identity/quick-create-identity.md) si vous n’avez pas encore de jeton disponible.

### <a name="setup-group-call-or-teams-meeting-options"></a>Configurer les options d’appel de groupe ou de réunion Teams

Selon le type d’appel/de réunion que vous souhaitez configurer, utilisez l’objet d’options approprié.

#### <a name="group-call"></a>Appel de groupe

Initialisez une instance de `GroupCallOptions` dans la fonction `startCallComposite`. Remplacez `<GROUP_CALL_ID>` par votre ID de groupe de l’appel et `<DISPLAY_NAME>` par votre nom.

```swift
let options = GroupCallOptions(communicationTokenCredential: communicationTokenCredential,
                               displayName: displayName,
                               groupId: uuid)
```

#### <a name="teams-meeting"></a>Réunion Teams

Initialisez une instance de `TeamsMeetingOptions` dans la fonction `startCallComposite`. Remplacez `<TEAMS_MEETING_LINK>` par votre ID de groupe de l’appel et `<DISPLAY_NAME>` par votre nom.

```swift
let options = TeamsMeetingOptions(communicationTokenCredential: communicationTokenCredential,
                                  displayName: displayName,
                                  meetingLink: link)
```

#### <a name="get-a-microsoft-teams-meeting-link"></a>Obtenir un lien de réunion Microsoft Teams

Un lien de réunion Microsoft Teams peut être récupéré par le biais des API Graph. Ce processus est détaillé dans la [documentation de Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
Le kit SDK Appel Communication Services accepte un lien de réunion Microsoft Teams complet. Ce lien est retourné comme faisant partie de la ressource `onlineMeeting`, accessible sous la [propriété `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). Vous pouvez également récupérer les informations de réunion nécessaires à partir de l’URL **Rejoindre la réunion** dans l’invite de réunion Teams elle-même.

### <a name="launch"></a>Lancer

Appelez `launch` sur l’instance de `CallComposite` dans la fonction `startCallComposite`

```swift
callComposite?.launch(with: options)
```

### <a name="subscribe-to-events-from-callcomposite"></a>S’abonner aux événements à partir de `CallComposite`

Vous pouvez implémenter les fermetures à partir de `CallCompositeEventsHandler` pour agir sur les événements et passer l’implémentation à `CallCompositeOptions`. Un événement indiquant le moment où le composite a fini son exécution par une erreur en est un bon exemple.

```swift
let handler = CallCompositeEventsHandler(didFail: { error in
            print("didFail with error:\(error)")
        })
```

```swift
let callCompositeOptions = CallCompositeOptions(callCompositeEventsHandler: handler)
```

### <a name="apply-theme-configuration"></a>Appliquer la configuration du thème

Vous pouvez personnaliser le thème en créant une configuration de thème personnalisée qui implémente le protocole ThemeConfiguration. Vous incluez ensuite une instance de cette nouvelle classe dans CallCompositeOptions.

```swift
class CustomThemeConfiguration: ThemeConfiguration {
   var primaryColor: UIColor {
       return UIColor.red
   }
}
```

```swift
let callCompositeOptions = CallCompositeOptions(themeConfiguration: CustomThemeConfiguration())
```
