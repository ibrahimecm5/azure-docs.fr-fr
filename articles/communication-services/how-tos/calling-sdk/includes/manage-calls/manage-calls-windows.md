---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 9737c4e756e034c88800264e5cc56a4ca1f81741
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131254075"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-windows.md)]

### <a name="request-access-to-the-microphone"></a>Demander l’accès au microphone

L’application nécessite l’accès au micro pour s’exécuter correctement. Dans les applications UWP, la capacité du micro doit être déclarée dans le fichier manifeste de l’application. Les étapes suivantes illustrent comment y parvenir.

1. Dans le panneau `Solution Explorer`, double-cliquez sur le fichier avec l’extension `.appxmanifest`.
2. Cliquez sur l’onglet `Capabilities`.
3. Cochez la case `Microphone` dans la liste des capacités.

### <a name="create-ui-buttons-to-place-and-hang-up-the-call"></a>Créer des boutons d’interface utilisateur pour passer et raccrocher l’appel

Cet exemple d’application simple contiendra deux boutons. Un pour passer l’appel et une autre pour raccrocher un appel passé.
Les étapes suivantes illustrent comment ajouter ces boutons à l’application.

1. Dans le panneau `Solution Explorer`, double-cliquez sur le fichier nommé `MainPage.xaml`.
2. Dans le panneau central, recherchez le code XAML sous l’aperçu de l’interface utilisateur.
3. Remplacez le code XAML `<Grid>` à `</Grid>` par l’extrait suivant :
```xml
<StackPanel Orientation="Horizontal" VerticalAlignment="Center" HorizontalAlignment="Center">
    <Button x:Name="callButton" Click="CallHandler" Margin="10,10,10,10" HorizontalAlignment="Stretch" VerticalAlignment="Stretch">Call</Button>
    <Button x:Name="hangupButton" Click="HangupHandler" Margin="10,10,10,10" HorizontalAlignment="Stretch" VerticalAlignment="Stretch">Hang up</Button>
</StackPanel>
```

### <a name="setting-up-the-app-with-calling-sdk-apis"></a>Configuration de l’application avec les API du Kit de développement logiciel (SDK) Appel

Les API du Kit de développement logiciel (SDK) Appel se trouvent dans deux espaces de noms différents.
Les étapes suivantes informent le compilateur C# de ces espaces de noms, ce qui permet à IntelliSense de Visual Studio de faciliter le développement du code.

1. Dans le panneau `Solution Explorer`, cliquez sur la flèche située à gauche du fichier nommé `MainPage.xaml`.
2. Double-cliquez sur le fichier nommé `MainPage.xaml.cs` qui est apparu.
3. Ajoutez les commandes suivantes au bas des instructions `using` actuelles.

```csharp
using Azure.Communication;
using Azure.Communication.Calling;
```

Veillez à garder le fichier `MainPage.xaml.cs` ouvert. Les étapes suivantes y ajouteront du code.

## <a name="allow-app-interactions"></a>Autoriser les interactions avec l’application

Les boutons d’interface utilisateur précédemment ajoutés doivent fonctionner en même temps qu’un `Call` passé. Cela signifie qu’un membre de données `Call` doit être ajouté à la classe `MainPage`.
En outre, pour permettre à l’opération asynchrone qui crée `CallAgent` de réussir, un membre de données `CallAgent` doit également être ajouté à la même classe.

Ajoutez les membres de données suivants à la classe `MainPage` :
```csharp
CallAgent agent_;
Call call_;
```

## <a name="create-button-handlers"></a>Créer des gestionnaires de bouton

Précédemment, deux boutons d’interface utilisateur ont été ajoutés au code XAML. Le code suivant ajoute les gestionnaires à exécuter lorsqu’un utilisateur sélectionne le bouton.
Le code suivant doit être ajouté après les membres de données de la section précédente.

```csharp
private void CallHandler(object sender, RoutedEventArgs e)
{
}

private void HangupHandler(object sender, RoutedEventArgs e)
{
}
```

## <a name="object-model"></a>Modèle objet

Les classes et les interfaces suivantes gèrent certaines des principales fonctionnalités de la bibliothèque de client Appel Azure Communication Services pour UWP.

| Nom                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient est le point d’entrée principal de la bibliothèque de client Appel. |
| CallAgent | CallAgent sert à lancer et à joindre des appels. |
| Appeler | Call est utilisé pour gérer les appels passés ou rejoints. |
| CommunicationTokenCredential | CommunicationTokenCredential est utilisé comme informations d’identification du jeton pour instancier CallAgent.|
| CallAgentOptions | CallAgentOptions contient des informations permettant d’identifier l’appelant. |
| HangupOptions | HangupOptions indique à tous les participants si un appel doit être interrompu. |

## <a name="initialize-the-callagent"></a>Initialiser CallAgent

Pour créer une instance `CallAgent` à partir de `CallClient`, vous devez utiliser la méthode `CallClient.CreateCallAgent` qui renvoie de manière asynchrone un objet `CallAgent` une fois qu’elle est initialisée.

Pour créer `CallAgent`, vous devez transmettre un objet `CommunicationTokenCredential` et un objet `CallAgentOptions`. Gardez à l’esprit que `CommunicationTokenCredential` lève une exception si un jeton malformé est transmis.

Le code suivant doit être ajouté dans `CallHandler`.

```csharp
CallClient client = new CallClient();
CommunicationTokenCredential credentials;

CallAgentOptions callAgentOptions = new CallAgentOptions
{
  DisplayName = "<CALLER NAME>"
};

try
{
    credentials = new CommunicationTokenCredential("<CREDENTIAL TOKEN>");
}
catch (Exception)
{
    throw new Exception("Invalid credential token");
}
```

`<USER ACCESS TOKEN>` doit être remplacé par un jeton d’informations d’identification valide pour votre ressource. Référez-vous à la documentation relative aux [jetons d’accès utilisateur](../../../../quickstarts/access-tokens.md) si un jeton d’informations d’identification doit être sourcé.

## <a name="create-callagent-and-place-a-call"></a>Créer CallAgent et passer un appel

Les objets nécessaires à la création d’un objet `CallAgent` sont maintenant prêts. Il est temps de créer `CallAgent` de manière asynchrone et de passer un appel.

Le code suivant doit être ajouté après avoir traité l’exception de l’étape précédente.

```csharp
client.CreateCallAgent(creds, callAgentOptions).Completed +=
async (IAsyncOperation<CallAgent> asyncInfo, AsyncStatus asyncStatus) =>
{
    agent_ = asyncInfo.GetResults();

    CommunicationUserIdentifier target = new CommunicationUserIdentifier("<CALLEE>");

    StartCallOptions startCallOptions = new StartCallOptions();
    call_ = await agent_.StartCallAsync(new List<ICommunicationIdentifier>() { target }, startCallOptions);
};
```

Remplacez `<CALLEE>` par une autre identité de votre locataire. Vous pouvez également utiliser `8:echo123` pour communiquer avec le bot d’écho d’ACS.

## <a name="end-a-call"></a>Terminer un appel

Une fois qu’un appel est passé, la méthode `Hangup` de l’objet `Call` doit être utilisée pour raccrocher l’appel.

Une instance `HangupOptions` doit également être utilisée pour indiquer à tous les participants si l’appel doit être interrompu.

Le code suivant doit être ajouté dans `HangupHandler`.

```csharp
HangUpOptions hangupOptions = new HangUpOptions();
call_.HangUpAsync(hangupOptions).Completed += (IAsyncAction asyncInfo, AsyncStatus asyncStatus) =>
{

};
```

## <a name="run-the-code"></a>Exécuter le code

Assurez-vous que Visual Studio génère l’application pour `x64`, `x86` ou `ARM64`, puis appuyez sur `F5` pour démarrer l’exécution de l’application. Après cela, cliquez sur le bouton `Call` pour passer un appel à l’appelé défini.

N’oubliez pas que lors de la première exécution de l’application, le système demande à l’utilisateur d’autoriser l’accès au micro.