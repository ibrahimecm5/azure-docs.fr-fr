---
title: 'Tutoriel : Créer une application Angular qui utilise la plateforme d’identités Microsoft pour l’authentification à l’aide du flux de code d’authentification | Azure'
titleSuffix: Microsoft identity platform
description: Dans ce tutoriel, vous allez générer une application monopage (SPA) Angular à l’aide du flux de code d’authentification, qui utilise la plateforme d’identités Microsoft pour connecter les utilisateurs et obtenir un jeton d’accès permettant d’appeler l’API Microsoft Graph en leur nom.
services: active-directory
author: jo-arroyo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 04/14/2021
ms.author: joarroyo
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 398117120e560db00be97a344feed09351a26258
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050334"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application-spa-using-auth-code-flow"></a>Tutoriel : Connecter les utilisateurs et appeler l’API Microsoft Graph à partir d’une application monopage (SPA) Angular à l’aide du flux de code d’authentification

Dans ce tutoriel, vous générez une application monopage (SPA) Angular qui connecte les utilisateurs et appelle l’API Microsoft Graph en utilisant le flux de code d’autorisation avec PKCE. L’application SPA que vous générez utilise la bibliothèque d’authentification MSAL (Microsoft Authentication Library) pour Angular v2.

Dans ce tutoriel, vous allez :

> [!div class="checklist"]
> * Créer un projet angulaire avec `npm`.
> * Inscrire l’application dans le Portail Azure
> * Ajouter du code pour prendre en charge la connexion et la déconnexion des utilisateurs
> * Ajouter du code pour appeler l’API Microsoft Graph
> * Test de l'application

MSAL Angular v2 améliore MSAL Angular v1 en prenant en charge le flux de code d’autorisation dans le navigateur à la place du flux d’octroi implicite. MSAL Angular v2 ne prend **PAS** en charge le flux implicite.

## <a name="prerequisites"></a>Prérequis

* [Node.js](https://nodejs.org/en/download/) pour l’exécution d’un serveur web local
* [Visual Studio Code](https://code.visualstudio.com/download) ou un autre éditeur pour la modification des fichiers projet

## <a name="how-the-sample-app-works"></a>Fonctionnement de l’exemple d’application

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Diagramme montrant le flux de code d’autorisation dans une application monopage":::

L’exemple d’application créé dans ce tutoriel permet à une application monopage Angular d’interroger l’API Microsoft Graph ou une API web qui accepte des jetons émis par la plateforme d’identités Microsoft. Elle utilise la bibliothèque d’authentification MSAL (Microsoft Authentication Library) pour Angular v2, un wrapper de la bibliothèque MSAL.js v2. MSAL Angular permet aux applications Angular 9+ d’authentifier les utilisateurs en entreprise à l’aide d’Azure Active Directory (Azure AD) ainsi que les utilisateurs qui ont des comptes Microsoft et des identités de réseaux sociaux tels que Facebook, Google et LinkedIn. La bibliothèque permet aussi aux applications d’accéder aux services cloud Microsoft et à Microsoft Graph.

Dans ce scénario, une fois qu’un utilisateur s’est connecté, un jeton d’accès est demandé et ajouté aux requêtes HTTP par le biais de l’en-tête d’autorisation. Les opérations d’acquisition et de renouvellement de jetons sont gérées par MSAL.

### <a name="libraries"></a>Bibliothèques

Ce tutoriel utilise les bibliothèques suivantes :

|Bibliothèque|Description|
|---|---|
|[MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular)|Wrapper de la bibliothèque d’authentification Microsoft pour JavaScript Angular|
|[Navigateur MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)|Package de navigateur de la bibliothèque d’authentification Microsoft pour JavaScript v2 |

Vous trouverez le code source de toutes les bibliothèques MSAL.js dans le dépôt [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) sur GitHub.

## <a name="create-your-project"></a>Créer votre projet

Une fois [Node.js](https://nodejs.org/en/download/) installé, ouvrez une fenêtre de terminal, puis exécutez les commandes suivantes pour générer une nouvelle application Angular :

```bash
npm install -g @angular/cli                         # Install the Angular CLI
ng new msal-angular-tutorial --routing=true --style=css --strict=false    # Generate a new Angular app
cd msal-angular-tutorial                            # Change to the app directory
npm install @angular/material @angular/cdk          # Install the Angular Material component library (optional, for UI)
npm install @azure/msal-browser @azure/msal-angular # Install MSAL Browser and MSAL Angular in your application
ng generate component home                          # To add a home page
ng generate component profile                       # To add a profile page
```

## <a name="register-your-application"></a>Inscrivez votre application

Suivez les [instructions pour inscrire une application monopage](./scenario-spa-app-registration.md) sur le portail Azure.

Dans la page **Vue d’ensemble** de votre inscription, notez la valeur de **ID d’application (client)** pour une utilisation ultérieure.

Inscrivez la valeur de l’**URI de redirection** en tant que **http://localhost:4200/** et tapez « SPA ».

## <a name="configure-the-application"></a>Configurer l’application

1. Dans le dossier *src/app*, modifiez *app.module.ts*, puis ajoutez `MsalModule` et `MsalInterceptor` à `imports` ainsi que la constante `isIE`. Votre code doit ressembler à ceci :

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule } from '@azure/msal-angular';
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
    ```

    Remplacez les valeurs suivantes :

    |Nom de la valeur|À propos|
    |---------|---------|
    |Enter_the_Application_Id_Here|Dans la page **Vue d’ensemble** de l’inscription de votre application, il s’agit de la valeur de votre **ID d’application (client)** . |
    |Enter_the_Cloud_Instance_Id_Here|Il s’agit de l’instance du cloud Azure. Pour le cloud Azure principal ou mondial, entrez **https://login.microsoftonline.com** . Pour les clouds nationaux (par exemple Chine), consultez [Clouds nationaux](./authentication-national-cloud.md).|
    |Enter_the_Tenant_Info_Here| Définissez cette valeur sur une des options suivantes : Si votre application prend en charge les *comptes dans cet annuaire organisationnel*, remplacez cette valeur par l’ID de l’annuaire (locataire) ou le nom du locataire (par exemple, **contoso.microsoft.com**). Si votre application prend en charge les *Comptes dans un annuaire organisationnel*, remplacez cette valeur par **organizations**. Si votre application prend en charge les *Comptes dans un annuaire organisationnel et comptes personnels Microsoft*, remplacez cette valeur par **common**. Pour limiter la prise en charge aux *Comptes Microsoft personnels uniquement*, remplacez cette valeur par **consumers**. |
    |Enter_the_Redirect_Uri_Here|À remplacer par **http://localhost:4200** .|

    Pour plus d’informations sur les options configurables disponibles, consultez [Initialiser les applications clientes](msal-js-initializing-client-applications.md).

2. Ajoutez des routes aux composants d’accueil et de profil dans *src/app/app-routing.module.ts*. Votre code doit ressembler à ce qui suit :

    ```javascript
    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    const routes: Routes = [
      {
        path: 'profile',
        component: ProfileComponent,
      },
      {
        path: '',
        component: HomeComponent
      },
    ];

    const isIframe = window !== window.parent && !window.opener;

    @NgModule({
      imports: [RouterModule.forRoot(routes, {
        initialNavigation: !isIframe ? 'enabled' : 'disabled' // Don't perform initial navigation in iframes
      })],
      exports: [RouterModule]
    })
    export class AppRoutingModule { }
    ```

## <a name="replace-base-ui"></a>Remplacer l’IU de base

1. Remplacez le code d’espace réservé dans *src/app/app.component.html* par ce qui suit :

    ```HTML
    <mat-toolbar color="primary">
      <a class="title" href="/">{{ title }}</a>

      <div class="toolbar-spacer"></div>

      <a mat-button [routerLink]="['profile']">Profile</a>

      <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>

    </mat-toolbar>
    <div class="container">
      <!--This is to avoid reload during acquireTokenSilent() because of hidden iframe -->
      <router-outlet *ngIf="!isIframe"></router-outlet>
    </div>
    ```

2. Ajoutez des modules de matériaux à *src/app/app.module.ts*. Votre `AppModule` doit ressembler à ceci :

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule } from '@azure/msal-angular';
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here',
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, 
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
    ```

3. (FACULTATIF) Ajoutez du code CSS à *src/style.css* :

    ```css
    @import '~@angular/material/prebuilt-themes/deeppurple-amber.css';

    html, body { height: 100%; }
    body { margin: 0; font-family: Roboto, "Helvetica Neue", sans-serif; }
    .container { margin: 1%; }
    ```

4. (FACULTATIF) Ajoutez du code CSS à *src/app/app.component.css* :

    ```css
    .toolbar-spacer {
        flex: 1 1 auto;
      }
      
      a.title {
        color: white;
      }
    ```

## <a name="sign-in-a-user"></a>Connecter un utilisateur

Ajoutez le code des sections suivantes pour appeler la connexion à l’aide d’une fenêtre indépendante ou d’une redirection plein cadre : 

### <a name="sign-in-using-pop-ups"></a>Se connecter à l’aide de fenêtres contextuelles

1. Changez le code dans *src/app/app.component.ts* par ce qui suit pour connecter un utilisateur à l’aide d’une fenêtre indépendante :

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;

      constructor(private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;
      }

      login() {
        this.authService.loginPopup()
          .subscribe({
            next: (result) => {
              console.log(result);
              this.setLoginDisplay();
            },
            error: (error) => console.log(error)
          });
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }
    }
    ```

> [!NOTE]
> Le reste de ce tutoriel utilise la méthode `loginRedirect` avec Microsoft Internet Explorer en raison d’un [problème connu](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/internet-explorer.md) lié à la gestion des fenêtres indépendantes par Internet Explorer.

### <a name="sign-in-using-redirects"></a>Se connecter à l’aide de redirections

1. Mettez à jour *src/app/app.module.ts* pour démarrer `MsalRedirectComponent`. Il s’agit d’un composant de redirection dédié qui va gérer les redirections. Votre code doit désormais ressembler à ceci :

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent } from '@azure/msal-angular'; // Updated import
    import { PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', 
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), null, null)
      ],
      providers: [],
      bootstrap: [AppComponent, MsalRedirectComponent] // MsalRedirectComponent bootstrapped here
    })
    export class AppModule { }
    ```

2. Ajoutez le sélecteur `<app-redirect>` à *src/index.html*. Ce sélecteur est utilisé par `MsalRedirectComponent`. Le fichier *src/index.html* doit ressembler à ceci :

    ```HTML
    <!doctype html>
    <html lang="en">
    <head>
      <meta charset="utf-8">
      <title>msal-angular-tutorial</title>
      <base href="/">
      <meta name="viewport" content="width=device-width, initial-scale=1">
      <link rel="icon" type="image/x-icon" href="favicon.ico">
    </head>
    <body>
      <app-root></app-root>
      <app-redirect></app-redirect>
    </body>
    </html>
    ```

3. Remplacez le code dans *src/app/app.component.ts* par ce qui suit pour connecter un utilisateur à l’aide d’une redirection plein cadre :

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;

      constructor(private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;
      }

      login() {
        this.authService.loginRedirect();
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }
    }
    ```

4. Remplacez le code existant dans *src/app/home/home.component.ts* pour vous abonner à l’événement `LOGIN_SUCCESS`. Cela vous permet d’accéder au résultat de la connexion réussie par redirection. Votre code doit ressembler à ceci :

    ```javascript
    import { Component, OnInit } from '@angular/core';
    import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
    import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
    import { filter } from 'rxjs/operators';

    @Component({
      selector: 'app-home',
      templateUrl: './home.component.html',
      styleUrls: ['./home.component.css']
    })
    export class HomeComponent implements OnInit {
      constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

      ngOnInit(): void {
        this.msalBroadcastService.msalSubject$
          .pipe(
            filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
          )
          .subscribe((result: EventMessage) => {
            console.log(result);
          });
      }
    }
    ```

## <a name="conditional-rendering"></a>Affichage conditionnel

Pour permettre l’affichage de certaines IU uniquement pour les utilisateurs authentifiés, les composants doivent s’abonner à `MsalBroadcastService` afin de déterminer si les utilisateurs ont été connectés, et si l’interaction a eu lieu.

1. Ajoutez `MsalBroadcastService` à *src/app/app.component.ts*, puis abonnez-vous au `inProgress$` observable pour vérifier si l’interaction a eu lieu, et si un compte est connecté avant l’affichage de l’IU. Votre code doit désormais ressembler à ceci :

    ```javascript
    import { Component, OnInit, OnDestroy } from '@angular/core';
    import { MsalService, MsalBroadcastService } from '@azure/msal-angular';
    import { InteractionStatus } from '@azure/msal-browser';
    import { Subject } from 'rxjs';
    import { filter, takeUntil } from 'rxjs/operators';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit, OnDestroy {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;
      private readonly _destroying$ = new Subject<void>();

      constructor(private broadcastService: MsalBroadcastService, private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;

        this.broadcastService.inProgress$
        .pipe(
          filter((status: InteractionStatus) => status === InteractionStatus.None),
          takeUntil(this._destroying$)
        )
        .subscribe(() => {
          this.setLoginDisplay();
        })
      }

      login() {
        this.authService.loginRedirect();
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }

      ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
      }
    }
    ```

2. Mettez à jour le code dans *src/app/home/home.component.ts* pour vérifier également si l’interaction a eu lieu avant la mise à jour de l’IU. Votre code doit désormais ressembler à ceci :

    ```javascript
    import { Component, OnInit } from '@angular/core';
    import { MsalBroadcastService, MsalService } from '@azure/msal-angular';
    import { EventMessage, EventType, InteractionStatus } from '@azure/msal-browser';
    import { filter } from 'rxjs/operators';

    @Component({
      selector: 'app-home',
      templateUrl: './home.component.html',
      styleUrls: ['./home.component.css']
    })
    export class HomeComponent implements OnInit {
      loginDisplay = false;

      constructor(private authService: MsalService, private msalBroadcastService: MsalBroadcastService) { }

      ngOnInit(): void {
        this.msalBroadcastService.msalSubject$
          .pipe(
            filter((msg: EventMessage) => msg.eventType === EventType.LOGIN_SUCCESS),
          )
          .subscribe((result: EventMessage) => {
            console.log(result);
          });
        
        this.msalBroadcastService.inProgress$
          .pipe(
            filter((status: InteractionStatus) => status === InteractionStatus.None)
          )
          .subscribe(() => {
            this.setLoginDisplay();
          })
      }
      
      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }
    }
    ```

3. Remplacez le code dans *src/app/home/home.component.html* par les affichages conditionnels suivants :

    ```HTML
    <div *ngIf="!loginDisplay">
        <p>Please sign-in to see your profile information.</p>
    </div>

    <div *ngIf="loginDisplay">
        <p>Login successful!</p>
        <p>Request your profile information by clicking Profile above.</p>
    </div>
    ```

## <a name="guarding-routes"></a>Protection des routes

### <a name="angular-guard"></a>Protection Angular

MSAL Angular fournit `MsalGuard`, une classe qui vous permet de protéger les routes et d’imposer une authentification avant tout accès à la route protégée. Les étapes ci-dessous permettent d’ajouter `MsalGuard` à la route `Profile`. Protéger l’itinéraire `Profile` signifie que même si un utilisateur ne se connecte pas à l’aide du bouton `Login`, s’il tente d’accéder à l’itinéraire `Profile` ou s’il clique sur le bouton `Profile`, `MsalGuard` l’invite à s’authentifier via une fenêtre contextuelle ou le redirige avant d’afficher la page `Profile`.

`MsalGuard` est une classe pratique qui vous permet d’améliorer l’expérience utilisateur. Toutefois, son utilisation est déconseillée pour des raisons de sécurité. Les attaquants peuvent éventuellement contourner les protections côté client. Vous devez vérifier que le serveur ne retourne aucune donnée à laquelle l’utilisateur ne doit pas accéder.

1. Ajoutez la classe `MsalGuard` en tant que fournisseur dans votre application au sein de *src/app/app.module.ts*, puis ajoutez les configurations pour `MsalGuard`. Vous pouvez fournir dans `authRequest` les étendues nécessaires pour obtenir des jetons plus tard, et affecter `Redirect` ou `Popup` au type d’interaction de la protection. Votre code doit ressembler à ce qui suit :

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent, MsalGuard } from '@azure/msal-angular'; // MsalGuard added to imports
    import { PublicClientApplication, InteractionType } from '@azure/msal-browser'; // InteractionType added to imports

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_here',
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', 
            redirectUri: 'Enter_the_Redirect_Uri_Here'
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), {
            interactionType: InteractionType.Redirect, // MSAL Guard Configuration
            authRequest: {
              scopes: ['user.read']
            }
        }, null)
      ],
      providers: [
        MsalGuard // MsalGuard added as provider here
      ],
      bootstrap: [AppComponent, MsalRedirectComponent]
    })
    export class AppModule { }
    ```

2. Définissez `MsalGuard` pour les routes à protéger dans *src/app/app-routing.module.ts* :

    ```javascript
    import { NgModule } from '@angular/core';
    import { Routes, RouterModule } from '@angular/router';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';
    import { MsalGuard } from '@azure/msal-angular';

    const routes: Routes = [
      {
        path: 'profile',
        component: ProfileComponent,
        canActivate: [MsalGuard]
      },
      {
        path: '',
        component: HomeComponent
      },
    ];

    const isIframe = window !== window.parent && !window.opener;

    @NgModule({
      imports: [RouterModule.forRoot(routes, {
        initialNavigation: !isIframe ? 'enabled' : 'disabled' // Don't perform initial navigation in iframes
      })],
      exports: [RouterModule]
    })
    export class AppRoutingModule { }
    ```

3. Modifiez les appels de connexion dans *src/app/app.component.ts* pour prendre en compte le `authRequest` défini dans les configurations de protection. À présent, votre code doit ressembler à ce qui suit :

    ```javascript
    import { Component, OnInit, OnDestroy, Inject } from '@angular/core';
    import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
    import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
    import { Subject } from 'rxjs';
    import { filter, takeUntil } from 'rxjs/operators';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent implements OnInit, OnDestroy {
      title = 'msal-angular-tutorial';
      isIframe = false;
      loginDisplay = false;
      private readonly _destroying$ = new Subject<void>();

      constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

      ngOnInit() {
        this.isIframe = window !== window.parent && !window.opener;

        this.broadcastService.inProgress$
        .pipe(
          filter((status: InteractionStatus) => status === InteractionStatus.None),
          takeUntil(this._destroying$)
        )
        .subscribe(() => {
          this.setLoginDisplay();
        })
      }

      login() {
        if (this.msalGuardConfig.authRequest){
          this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
        } else {
          this.authService.loginRedirect();
        }
      }

      setLoginDisplay() {
        this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
      }

      ngOnDestroy(): void {
        this._destroying$.next(undefined);
        this._destroying$.complete();
      }
    }
    ```

## <a name="acquire-a-token"></a>Acquérir un jeton

### <a name="angular-interceptor"></a>Classe Interceptor d’Angular

MSAL Angular fournit une classe `Interceptor` qui acquiert automatiquement des jetons pour les demandes sortantes qui utilisent le client `http` Angular pour les ressources connues protégées.

1. Ajoutez la classe `Interceptor` en tant que fournisseur à votre application dans *src/app/app.module.ts* avec ses configurations. Votre code doit désormais ressembler à ceci :

    ```javascript
    import { BrowserModule } from '@angular/platform-browser';
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { NgModule } from '@angular/core';
    import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http"; // Import 

    import { MatButtonModule } from '@angular/material/button';
    import { MatToolbarModule } from '@angular/material/toolbar';
    import { MatListModule } from '@angular/material/list';

    import { AppRoutingModule } from './app-routing.module';
    import { AppComponent } from './app.component';
    import { HomeComponent } from './home/home.component';
    import { ProfileComponent } from './profile/profile.component';

    import { MsalModule, MsalRedirectComponent, MsalGuard, MsalInterceptor } from '@azure/msal-angular'; // Import MsalInterceptor
    import { InteractionType, PublicClientApplication } from '@azure/msal-browser';

    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

    @NgModule({
      declarations: [
        AppComponent,
        HomeComponent,
        ProfileComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        AppRoutingModule,
        MatButtonModule,
        MatToolbarModule,
        MatListModule,
        HttpClientModule,
        MsalModule.forRoot( new PublicClientApplication({
          auth: {
            clientId: 'Enter_the_Application_Id_Here',
            authority: 'Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here',
            redirectUri: 'Enter_the_Redirect_Uri_Here',
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE,
          }
        }), {
          interactionType: InteractionType.Redirect,
          authRequest: {
            scopes: ['user.read']
            }
        }, {
          interactionType: InteractionType.Redirect, // MSAL Interceptor Configuration
          protectedResourceMap: new Map([ 
              ['Enter_the_Graph_Endpoint_Here/v1.0/me', ['user.read']]
          ])
        })
      ],
      providers: [
        {
          provide: HTTP_INTERCEPTORS,
          useClass: MsalInterceptor,
          multi: true
        },
        MsalGuard
      ],
      bootstrap: [AppComponent, MsalRedirectComponent]
    })
    export class AppModule { }

    ```

    Les ressources protégées sont fournies en tant que `protectedResourceMap`. Les URL que vous fournissez dans la collection `protectedResourceMap` sont sensibles à la casse. Pour chaque ressource, ajoutez les étendues à retourner dans le jeton d’accès.

    Par exemple :

    * `["user.read"]` pour Microsoft Graph
    * `["<Application ID URL>/scope"]` pour les API web personnalisées (c’est-à-dire `api://<Application ID>/access_as_user`)
    
    Modifiez les valeurs de `protectedResourceMap` comme indiqué ici :

    |Nom de la valeur| À propos|
    |----------|------|
    |`Enter_the_Graph_Endpoint_Here`| Instance de l’API Microsoft Graph avec laquelle l’application doit communiquer. Pour le point de terminaison de l’API Microsoft Graph **mondial**, remplacez les deux instances de cette chaîne par `https://graph.microsoft.com`. Pour les points de terminaison dans les déploiements dans des clouds **nationaux**, consultez [Déploiements dans des clouds nationaux](/graph/deployments) dans la documentation Microsoft Graph.|

2. Remplacez le code dans *src/app/profile/profile.component.ts* pour récupérer le profil d’un utilisateur avec une requête HTTP :

    ```JavaScript
    import { Component, OnInit } from '@angular/core';
    import { HttpClient } from '@angular/common/http';

    const GRAPH_ENDPOINT = 'Enter_the_Graph_Endpoint_Here/v1.0/me';

    type ProfileType = {
      givenName?: string,
      surname?: string,
      userPrincipalName?: string,
      id?: string
    };

    @Component({
      selector: 'app-profile',
      templateUrl: './profile.component.html',
      styleUrls: ['./profile.component.css']
    })
    export class ProfileComponent implements OnInit {
      profile!: ProfileType;

      constructor(
        private http: HttpClient
      ) { }

      ngOnInit() {
        this.getProfile();
      }

      getProfile() {
        this.http.get(GRAPH_ENDPOINT)
          .subscribe(profile => {
            this.profile = profile;
          });
      }
    }
    ```

3. Remplacez l’IU dans *src/app/profile/profile.component.html* pour afficher les informations de profil : 

    ```HTML
    <div>
        <p><strong>First Name: </strong> {{profile?.givenName}}</p>
        <p><strong>Last Name: </strong> {{profile?.surname}}</p>
        <p><strong>Email: </strong> {{profile?.userPrincipalName}}</p>
        <p><strong>Id: </strong> {{profile?.id}}</p>
    </div>
    ```

## <a name="sign-out"></a>Se déconnecter

Mettez à jour le code dans *src/app/app.component.html* pour afficher un bouton `Logout` de manière conditionnelle :

```HTML
<mat-toolbar color="primary">
  <a class="title" href="/">{{ title }}</a>

  <div class="toolbar-spacer"></div>

  <a mat-button [routerLink]="['profile']">Profile</a>

  <button mat-raised-button *ngIf="!loginDisplay" (click)="login()">Login</button>
  <button mat-raised-button *ngIf="loginDisplay" (click)="logout()">Logout</button>

</mat-toolbar>
<div class="container">
  <!--This is to avoid reload during acquireTokenSilent() because of hidden iframe -->
  <router-outlet *ngIf="!isIframe"></router-outlet>
</div>
```

### <a name="sign-out-using-redirects"></a>Se déconnecter à l’aide de redirections

Mettez à jour le code dans *src/app/app.component.ts* pour déconnecter un utilisateur à l’aide de redirections :

```javascript
import { Component, OnInit, OnDestroy, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, RedirectRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit, OnDestroy {
  title = 'msal-angular-tutorial';
  isIframe = false;
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {
    this.isIframe = window !== window.parent && !window.opener;

    this.broadcastService.inProgress$
    .pipe(
      filter((status: InteractionStatus) => status === InteractionStatus.None),
      takeUntil(this._destroying$)
    )
    .subscribe(() => {
      this.setLoginDisplay();
    })
  }

  login() {
    if (this.msalGuardConfig.authRequest){
      this.authService.loginRedirect({...this.msalGuardConfig.authRequest} as RedirectRequest);
    } else {
      this.authService.loginRedirect();
    }
  }

  logout() { // Add log out function here
    this.authService.logoutRedirect({
      postLogoutRedirectUri: 'http://localhost:4200'
    });
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}
```

### <a name="sign-out-using-pop-ups"></a>Se déconnecter à l’aide de fenêtres contextuelles

Mettez à jour le code dans *src/app/app.component.ts* pour déconnecter un utilisateur à l’aide de fenêtres contextuelles :

```javascript
import { Component, OnInit, OnDestroy, Inject } from '@angular/core';
import { MsalService, MsalBroadcastService, MSAL_GUARD_CONFIG, MsalGuardConfiguration } from '@azure/msal-angular';
import { InteractionStatus, PopupRequest } from '@azure/msal-browser';
import { Subject } from 'rxjs';
import { filter, takeUntil } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit, OnDestroy {
  title = 'msal-angular-tutorial';
  isIframe = false;
  loginDisplay = false;
  private readonly _destroying$ = new Subject<void>();

  constructor(@Inject(MSAL_GUARD_CONFIG) private msalGuardConfig: MsalGuardConfiguration, private broadcastService: MsalBroadcastService, private authService: MsalService) { }

  ngOnInit() {
    this.isIframe = window !== window.parent && !window.opener;

    this.broadcastService.inProgress$
    .pipe(
      filter((status: InteractionStatus) => status === InteractionStatus.None),
      takeUntil(this._destroying$)
    )
    .subscribe(() => {
      this.setLoginDisplay();
    })
  }

  login() {
    if (this.msalGuardConfig.authRequest){
      this.authService.loginPopup({...this.msalGuardConfig.authRequest} as PopupRequest)
        .subscribe({
          next: (result) => {
            console.log(result);
            this.setLoginDisplay();
          },
          error: (error) => console.log(error)
        });
    } else {
      this.authService.loginPopup()
        .subscribe({
          next: (result) => {
            console.log(result);
            this.setLoginDisplay();
          },
          error: (error) => console.log(error)
        });
    }
  }

  logout() { // Add log out function here
    this.authService.logoutPopup({
      mainWindowRedirectUri: "/"
    });
  }

  setLoginDisplay() {
    this.loginDisplay = this.authService.instance.getAllAccounts().length > 0;
  }

  ngOnDestroy(): void {
    this._destroying$.next(undefined);
    this._destroying$.complete();
  }
}
```

## <a name="test-your-code"></a>Test de votre code

1.  Démarrez le serveur web pour écouter sur le port en exécutant les commandes suivantes sur une invite de ligne de commande à partir du dossier de l’application :

    ```bash
    npm install
    npm start
    ```
1. Dans votre navigateur, entrez **http://localhost:4200** ou **http://localhost:{port}** , où *port* est le port sur lequel votre serveur web est à l’écoute. Vous devez voir une page qui ressemble à celle présentée ci-dessous.

    :::image type="content" source="media/tutorial-v2-angular-auth-code/angular-01-not-signed-in.png" alt-text="Navigateur web affichant la boîte de dialogue de connexion":::


### <a name="provide-consent-for-application-access"></a>Accorder les droits d’accès à l’application

Quand vous vous connectez pour la première fois à votre application, vous êtes invité à lui accorder l’accès à votre profil et à l’autoriser à vous connecter :

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Boîte de dialogue de contenu affichée dans le navigateur web":::

Si vous donnez votre consentement pour les autorisations demandées, l’application web affiche une page de connexion réussie :

:::image type="content" source="media/tutorial-v2-angular-auth-code/angular-02-signed-in.png" alt-text="Résultats d’une connexion réussie dans le navigateur web":::

### <a name="call-the-graph-api"></a>Appeler l’API Graph

Une fois connecté, sélectionnez **Profile** pour voir les informations de profil utilisateur retournées dans la réponse de l’appel à l’API Microsoft Graph :

:::image type="content" source="media/tutorial-v2-angular-auth-code/angular-03-profile-data.png" alt-text="Informations de profil de Microsoft Graph affichées dans le navigateur":::

## <a name="add-scopes-and-delegated-permissions"></a>Ajouter des étendues et des autorisations déléguées

L’API Microsoft Graph requiert l’étendue _User.Read_ pour lire le profil d’un utilisateur. L’étendue _User.Read_ est ajoutée automatiquement à chaque inscription d’application que vous créez dans le portail Azure. D’autres API pour Microsoft Graph ainsi que des API personnalisées pour votre serveur principal peuvent nécessiter des étendues supplémentaires. Par exemple, l’API Microsoft Graph nécessite l’étendue _Mail.Read_ afin de lister l’e-mail de l’utilisateur.

À mesure que vous ajoutez des étendues, vos utilisateurs peuvent être invités à accorder un consentement supplémentaire pour les étendues ajoutées.

>[!NOTE]
>L’utilisateur peut être invité à donner des consentements supplémentaires à mesure que vous augmentez le nombre d’étendues.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Approfondissez le développement d’applications monopages sur la plateforme d’identités Microsoft grâce à notre série d’articles.

> [!div class="nextstepaction"]
> [Scénario : Application monopage](scenario-spa-overview.md)
