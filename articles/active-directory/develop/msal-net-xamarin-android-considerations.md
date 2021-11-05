---
title: Configuration et résolution des problèmes du code Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Découvrez les considérations à prendre en compte lors de l’utilisation de Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 04194dc73b7c91bd1e59140a032232246b72420e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052699"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Configuration requise et conseils de dépannage pour Xamarin Android avec MSAL.NET

Vous devez effectuer plusieurs modifications de configuration dans votre code lorsque vous utilisez Xamarin Android avec la bibliothèque d’authentification Microsoft pour .NET (MSAL.NET). Les sections suivantes décrivent les modifications requises, suivies d’une section [Résolution des problèmes](#troubleshooting) pour vous aider à éviter certains des problèmes les plus courants.

## <a name="set-the-parent-activity"></a>Définir l’activité parente

Sur Xamarin Android, définissez l’activité parente de façon à ce que le jeton soit retourné après l’interaction :

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

Dans MSAL.NET 4.2 et versions ultérieures, vous pouvez également définir cette fonctionnalité au niveau de [PublicClientApplication][PublicClientApplication]. Pour ce faire, utilisez un rappel :

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Si vous utilisez [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin), votre code générateur [`PublicClientApplication`][PublicClientApplication] doit ressembler à cet extrait de code :

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>S’assurer que le contrôle retourne à MSAL

Lorsque la partie interactive du flux d’authentification se termine, rendez le contrôle à MSAL en remplaçant la méthode [`Activity`][Activity].[`OnActivityResult()`][OnActivityResult] .

Dans votre remplacement, appelez la méthode `AuthenticationContinuationHelper`.`SetAuthenticationContinuationEventArgs()` de MSAL.NET pour rendre le contrôle à la bibliothèque MSAL à la fin de la partie interactive du flux d’authentification.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest-for-system-webview-support"></a>Mettre à jour le manifeste Android pour la prise en charge de System WebView 

Pour prendre en charge System WebView, le fichier *AndroidManifest.xml* doit contenir les valeurs suivantes :

```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="msal{Client Id}" android:host="auth" />
  </intent-filter>
</activity>
```

La valeur `android:scheme` est créée à partir de l’URI de redirection configuré dans le portail d’application. Par exemple, si votre URI de redirection est `msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842://auth`, l’entrée `android:scheme` du manifeste ressemble à l’exemple suivant :

```xml
<data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
```

Vous pouvez également [créer l’activité dans le code](/xamarin/android/platform/android-manifest#the-basics) plutôt que de modifier manuellement le fichier *AndroidManifest.xml*. Pour créer l’activité dans le code, commencez par créer une classe qui comprend les attributs `Activity` et `IntentFilter`.

Voici un exemple de classe qui représente les valeurs du fichier XML :

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="use-system-webview-in-brokered-authentication"></a>Utiliser System WebView dans l’authentification répartie

Pour utiliser System WebView comme solution de secours pour l’authentification interactive lorsque vous avez configuré votre application en vue d’utiliser l’authentification répartie et lorsque l’appareil ne dispose pas de répartiteur, activez MSAL pour capturer la réponse d’authentification à l’aide de l’URI de redirection du répartiteur. MSAL essaiera de s’authentifier à l’aide de System WebView par défaut sur l’appareil lorsqu’il détecte que le répartiteur n’est pas disponible. Avec cette valeur par défaut, MSAL échoue car l’URI de redirection est configuré pour utiliser un répartiteur, et System WebView ne sait pas comment l’utiliser pour revenir à MSAL. Pour y remédier, créez un _filtre d’intention_ à l’aide de l’URI de redirection du répartiteur que vous avez configuré précédemment. Ajoutez le filtre d’intention en modifiant le manifeste de votre application comme dans l’exemple suivant :

```xml
<!--Intent filter to capture System WebView or Authenticator calling back to our app after sign-in-->
<activity
      android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash" />
    </intent-filter>
</activity>
```

Remplacez le nom de package que vous avez inscrit dans le Portail Azure par la valeur `android:host=`. Remplacez le hachage de clé que vous avez inscrit dans le Portail Azure par la valeur `android:path=`. Le hachage de signature **ne doit pas** être encodé dans l’URL. Assurez-vous qu’une barre oblique (`/`) précède votre hachage de signature.

### <a name="xamarinforms-43x-manifest"></a>Manifeste Xamarin.Forms 4.3.x

Xamarin.Forms 4.3.x génère du code qui définit l’attribut `package` sur `com.companyname.{appName}` dans le fichier *AndroidManifest.xml*. Si vous utilisez `DataScheme` comme `msal{client_id}`, vous souhaiterez peut-être modifier la valeur pour qu’elle corresponde à la valeur de l’espace de noms `MainActivity.cs`.

## <a name="android-11-support"></a>Prise en charge d’Android 11

Pour utiliser le navigateur système et l’authentification répartie dans Android 11, vous devez d’abord déclarer ces packages afin qu’ils soient visibles par l’application. Les applications qui ciblent Android jusqu’à la version 10 (API 29) peuvent interroger le système d’exploitation pour obtenir la liste des packages disponibles sur l’appareil à un moment donné. Pour prendre en charge la confidentialité et la sécurité, Android 11 réduit la visibilité des packages à une liste par défaut de packages de système d’exploitation et les packages spécifiés dans le fichier *AndroidManifest.xml* de l’application. 

Pour permettre à l’application de s’authentifier à l’aide du navigateur système et du répartiteur, ajoutez la section suivante à *AndroidManifest.xml* :

```xml
<!-- Required for API Level 30 to make sure the app can detect browsers and other apps where communication is needed.-->
<!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
<queries>
  <package android:name="com.azure.authenticator" />
  <package android:name="{Package Name}" />
  <package android:name="com.microsoft.windowsintune.companyportal" />
  <!-- Required for API Level 30 to make sure the app detect browsers
      (that don't support custom tabs) -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" />
  </intent>
  <!-- Required for API Level 30 to make sure the app can detect browsers that support custom tabs -->
  <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
  <intent>
    <action android:name="android.support.customtabs.action.CustomTabsService" />
  </intent>
</queries>
``` 

Remplacez `{Package Name}` par le nom du package d’application. 

Votre manifeste mis à jour, qui prend désormais en charge le navigateur système et l’authentification répartie, doit ressembler à l’exemple suivant :

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.XamarinDev">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <application android:theme="@android:style/Theme.NoTitleBar">
        <activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msauth" android:host="com.companyname.XamarinDev" android:path="/Fc4l/5I4mMvLnF+l+XopDuQ2gEM=" />
            </intent-filter>
        </activity>
    </application>
    <!-- Required for API Level 30 to make sure we can detect browsers and other apps we want to
     be able to talk to.-->
    <!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
    <queries>
        <package android:name="com.azure.authenticator" />
        <package android:name="com.companyname.xamarindev" />
        <package android:name="com.microsoft.windowsintune.companyportal" />
        <!-- Required for API Level 30 to make sure we can detect browsers
        (that don't support custom tabs) -->
        <intent>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="https" />
        </intent>
        <!-- Required for API Level 30 to make sure we can detect browsers that support custom tabs -->
        <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
        <intent>
            <action android:name="android.support.customtabs.action.CustomTabsService" />
        </intent>
    </queries>
</manifest>
```

## <a name="use-the-embedded-web-view-optional"></a>Utilisez la vue web incorporée (facultatif)

Par défaut, MSAL.NET utilise le navigateur web du système. Ce navigateur vous permet d’accéder à l’authentification unique (SSO) à l’aide d’applications web et d’autres applications. Dans certains cas rares, vous souhaiterez peut-être que votre système utilise une vue web incorporée.

Cet exemple de code montre comment configurer une vue web incorporée :

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Pour plus d’informations, consultez [Utiliser des navigateurs web pour MSAL.NET](msal-net-web-browsers.md) et [Considérations relatives au navigateur système Xamarin Android](msal-net-system-browser-android-considerations.md).

## <a name="troubleshooting"></a>Dépannage

### <a name="general-tips"></a>Conseils généraux

- Mettez à jour le package NuGet existant de MSAL.NET vers la [version la plus récente de MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Vérifiez que Xamarin.Forms est sur la version la plus récente.
- Vérifiez que Xamarin.Android.Support.v4 est sur la version la plus récente.
- Assurez-vous que tous les packages Xamarin.Android.Support ciblent la version la plus récente.
- Nettoyez ou régénérez l’application.
- Dans Visual Studio, essayez de définir le nombre maximal de builds de projets en parallèle sur **1**. Pour ce faire, sélectionnez **Options** > **Projets et solutions** > **Générer et exécuter** > **Nombre maximal de builds de projets parallèles**.
- Si vous générez à partir de la ligne de commande et que votre commande utilise `/m`, essayez de supprimer cet élément de la commande.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Erreur : Le nom AuthenticationContinuationHelper n’existe pas dans le contexte actuel

Si une erreur indique que `AuthenticationContinuationHelper` n’existe pas dans le contexte actuel, Visual Studio a peut-être mis à jour le fichier *Android.csproj\** de manière incorrecte. Parfois, le chemin d’accès au fichier dans l’élément `<HintPath>` contient de façon erronée `netstandard13` au lieu de `monoandroid90`.

Cet exemple contient un chemin d’accès au fichier correct :

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez l’exemple d’une [application mobile Xamarin utilisant la Plateforme d’identités Microsoft](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). Le tableau suivant récapitule les informations pertinentes dans le fichier LISEZMOI.

| Exemple | Plateforme | Description |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Application Xamarin.Forms simple qui montre comment utiliser MSAL pour authentifier les comptes personnels Microsoft et Azure AD via le point de terminaison Azure AD 2.0. L’application montre également comment accéder à Microsoft Graph et affiche le jeton résultant. <br>![Diagramme du flux d’authentification](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
