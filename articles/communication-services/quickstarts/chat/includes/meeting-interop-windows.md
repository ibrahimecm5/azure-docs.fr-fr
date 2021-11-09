---
title: Démarrage rapide - Participer à une réunion Teams
author: juramir
ms.author: juramir
ms.date: 10/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: fd559b8f393f7dca88bfb64fa24e9ba17e6b3832
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253089"
---
Dans ce démarrage rapide, vous allez découvrir comment rejoindre une conversation dans une réunion Teams à l’aide du Kit de développement logiciel (SDK) Conversation d’Azure Communication Services pour C#.

## <a name="sample-code"></a>Exemple de code
Retrouvez le code ce démarrage rapide sur [GitHub](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/ChatTeamsInteropQuickStart).

## <a name="prerequisites"></a>Prérequis 

*  [Déploiement Teams](/deployoffice/teams-install). 
* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).  
* Installez [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) avec la charge de travail de développement pour la plateforme Windows universelle.  
* Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../create-communication-resource.md). 
* Un lien de réunion Teams.

## <a name="joining-the-meeting-chat"></a>Participation à la conversation d’une réunion 

Un utilisateur de Communication Services peut participer à une réunion Teams en tant qu’utilisateur anonyme à l’aide du kit de développement logiciel (SDK) Appel. En rejoignant la réunion, il est également ajouté comme participant à la conversation de la réunion, où il peut échanger des messages avec d’autres utilisateurs lors de la réunion. L’utilisateur n’aura pas accès aux messages de la conversation envoyés avant qu’il rejoigne la réunion. Il ne pourra pas non plus envoyer ni recevoir des messages une fois la réunion terminée. Pour participer à la réunion et commencer à converser, vous pouvez suivre les étapes suivantes.

## <a name="run-the-code"></a>Exécuter le code
Vous pouvez générer et exécuter le code sur Visual Studio. Notez que les plateformes de solution que nous prenons en charge sont `x64`, `x86` et `ARM64`. 

1. Ouvrez une instance de PowerShell, de Terminal Windows, une invite de commandes ou équivalent, puis accédez au répertoire dans lequel vous souhaitez cloner l’exemple.
2. `git clone https://github.com/Azure-Samples/Communication-Services-dotnet-quickstarts.git`
3. Ouvrez le projet ChatTeamsInteropQuickStart/ChatTeamsInteropQuickStart.csproj dans Visual Studio.
4. Installez les versions de packages NuGet suivantes (ou ultérieures) :
``` csharp
Install-Package Azure.Communication.Calling -Version 1.0.0-beta.29
Install-Package Azure.Communication.Chat -Version 1.1.0
Install-Package Azure.Communication.Common -Version 1.0.1
Install-Package Azure.Communication.Identity -Version 1.0.1

```

5. À l’aide de la ressource Communication Services obtenue dans les prérequis, ajoutez la chaîne de connexion au fichier **ChatTeamsInteropQuickStart/MainPage.xaml.cs**. 

``` csharp
//ACS resource connection string i.e = "endpoint=https://your-resource.communication.azure.net/;accesskey=your-access-key";
private const string connectionString_ = "";
```

> [!IMPORTANT]
> * Sélectionnez la plateforme appropriée dans la liste déroulante « Plateformes de solution » dans Visual Studio <b>avant</b> d’exécuter le code. Par exemple, `x64`
> * assurez-vous que le « mode développeur » est activé dans Windows 10 [(Paramètres du développeur)](/windows/apps/get-started/enable-your-device-for-development)
>  
>  *Les étapes suivantes ne fonctionnent pas si cette configuration n’est pas correcte.*


6. Appuyez sur la touche F5 pour démarrer le projet en mode débogage.
7. Collez un lien de réunion Teams valide dans la zone « Lien de réunion Teams » (voir la section suivante).
8. Appuyez sur « Rejoindre la réunion Teams » pour commencer la conversation instantanée.

> [!IMPORTANT]
> Une fois que le Kit de développement logiciel (SDK) appelant a établi la connexion avec la réunion Teams ([voir Communication Services appelant une application Windows ](../../voice-video-calling/getting-started-with-calling.md)), les fonctions clés pour gérer les opérations de conversation sont : StartPollingForChatMessages et SendMessageButton_Click. Les deux extraits de code se trouvent sous ChatTeamsInteropQuickStart\MainPage.xaml.cs. 

```csharp
        /// <summary>
        /// Backgroung task that keeps polling for chat messages while the call connection is stablished
        /// </summary>
        private async Task StartPollingForChatMessages()
        {
            CommunicationTokenCredential communicationTokenCredential = new(user_token_);
            chatClient_ = new ChatClient(EndPointFromConnectionString(), communicationTokenCredential);
            await Task.Run(async () =>
            {
                keepPolling_ = true;

                ChatThreadClient chatThreadClient = chatClient_.GetChatThreadClient(thread_Id_);
                int previousTextMessages = 0;
                while (keepPolling_)
                {
                    try
                    {
                        CommunicationUserIdentifier currentUser = new(user_Id_);
                        AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
                        SortedDictionary<long, string> messageList = new();
                        int textMessages = 0;
                        string userPrefix;
                        await foreach (ChatMessage message in allMessages)
                        {
                            if (message.Type == ChatMessageType.Html || message.Type == ChatMessageType.Text)
                            {
                                textMessages++;
                                userPrefix = message.Sender.Equals(currentUser) ? "[you]:" : "";
                                messageList.Add(long.Parse(message.SequenceId), $"{userPrefix}{StripHtml(message.Content.Message)}");
                            }
                        }

                        //Update UI just when there are new messages
                        if (textMessages > previousTextMessages)
                        {
                            previousTextMessages = textMessages;
                            await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                            {
                                TxtChat.Text = string.Join(Environment.NewLine, messageList.Values.ToList());
                            });

                        }
                        if (!keepPolling_)
                        {
                            return;
                        }

                        await SetInCallState(true);
                        Thread.Sleep(3000);
                    }
                    catch (Exception e)
                    {
                        await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
                        {
                            _ = new MessageDialog($"An error ocurred while fetching messages in PollingChatMessagesAsync(). The application will shutdown. Details : {e.Message}").ShowAsync();
                            throw e;
                        });
                        await SetInCallState(false);
                    }
                }
            });
        }
        private async void SendMessageButton_Click(object sender, RoutedEventArgs e)
        {
            SendMessageButton.IsEnabled = false;
            ChatThreadClient chatThreadClient = chatClient_.GetChatThreadClient(thread_Id_);
            _ = await chatThreadClient.SendMessageAsync(TxtMessage.Text);
            
            TxtMessage.Text = "";
            SendMessageButton.IsEnabled = true;
        }
```



## <a name="get-a-teams-meeting-link"></a>Obtenir un lien de réunion Teams

Il est possible de récupérer le lien de réunion Teams à l’aide d’API Graph décrites dans la [documentation de Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true). Ce lien est retourné dans le cadre de la ressource `onlineMeeting`, accessible sous la [propriété `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). 

Vous pouvez également récupérer le lien de réunion requis à partir de l’URL **Rejoindre la réunion** disponible dans l’invitation à la réunion Teams elle-même.
Un lien de réunion Teams ressemble à ceci : `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. 

:::image type="content" source="../join-teams-meeting-chat-quickstart-windows.png" alt-text="Capture d’écran de l’application csharp terminée.":::

> [!NOTE] 
> Actuellement, seuls l’envoi, la réception et la modification de messages et l’envoi de notifications de saisie sont pris en charge pour les scénarios d’interopérabilité avec Teams. D’autres fonctionnalités, telles que les accusés de réception et les utilisateurs Communication Services qui ajoutent ou suppriment d’autres utilisateurs dans la réunion Teams, ne sont pas prises en charge pour l’instant.
