---
title: Gérer les protocoles propriétaires (Horizon)
description: Defender pour IoT Horizon fournit un environnement de développement ouvert (ODE) utilisé pour sécuriser les appareils IoT et ICS exécutant des protocoles propriétaires.
ms.date: 11/09/2021
ms.topic: reference
ms.openlocfilehash: 6a9995ec70f061381f1315e03c3781537733d97f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347282"
---
# <a name="defender-for-iot-horizon"></a>Defender pour IoT Horizon

Defender pour IoT Horizon inclut un environnement de développement ouvert (ODE) utilisé pour sécuriser les appareils IoT et ICS exécutant des protocoles propriétaires.

Horizon propose les éléments suivants :

  - Une prise en charge complète et illimitée des protocoles courants, propriétaires et personnalisés ou des protocoles qui s’écartent de toute norme. 
  - Un nouveau niveau de flexibilité et de possibilité pour le développement de plug-ins de dissecteur.
  - Un outil qui étend de façon exponentielle la visibilité et le contrôle de l’OT, sans avoir à effectuer une mise à niveau vers les nouvelles versions.
  - La sécurité de permettre un développement propriétaire sans divulguer d’informations sensibles.

Utilisez le Kit de développement logiciel (SDK) Horizon pour concevoir des plug-ins de dissecteur qui décodent le trafic afin qu’il puisse être traité par l’analyseur automatisé pour les programmes d’analyse de réseau IoT.

Les dissecteurs de protocole sont développés sous forme de plug-ins externes et sont intégrés à une vaste gamme de services Defender pour IoT, par exemple des services qui fournissent des capacités de surveillance, d’alerte et de création de rapports.

Envoyez un e-mail à l’adresse <ms-horizon-support@microsoft.com> pour obtenir plus d’informations sur l’utilisation du kit SDK Open Development Environment (ODE) et sur la création de plug-ins de protocole.

Une fois le plug-in développé, vous pouvez utiliser la console web Horizon pour :

  - Charger votre plug-in
  - Activer et désactiver les plug-ins  
  - Surveiller et déboguer le plug-in pour évaluer les performances
  - Créer des alertes personnalisées basées sur des protocoles propriétaires. Les afficher dans la console et les transférer aux fournisseurs partenaires. 

    :::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-plugin.png" alt-text="Chargez par le biais de votre plug-in Horizon."::: 

Cette fonctionnalité est disponible pour les utilisateurs Administrateur, CyberX ou Support.

Pour vous connecter à la console Horizon :

1. Connectez-vous à votre capteur via l’interface CLI.
2. Dans le fichier : `/var/cyberx/properties/horizon.properties` remplace la valeur de la propriété `ui.enabled` par `true` (`horizon.properties:ui.enabled=true`).
3. Connectez-vous à la console du capteur. 
4. Sélectionnez **Horizon** dans le menu principal. 

   :::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-from-the-menu.png" alt-text="Sélectionnez Horizon dans le menu principal.":::  

La console Horizon affiche les plug-ins de l’infrastructure fournis par Defender pour IoT et tout autre plug-in que vous avez créé et chargé. 

   :::image type="content" source="media/how-to-manage-proprietary-protocols/infrastructure.png" alt-text="Capture d’écran de l’infrastructure d’Horizon.":::

## <a name="upload-plugins"></a>Charger des plug-ins

Après avoir créé et testé votre plug-in de dissecteur propriétaire, vous pouvez le charger et le surveiller à partir de la console Horizon.

Pour charger :

1. Sélectionnez **CHARGER** à partir de la console.

   :::image type="content" source="media/how-to-manage-proprietary-protocols/upload-a-plugin.png" alt-text="Sélectionnez Charger pour votre plug-in.":::

2. Faites glisser votre plug-in ou accédez-y. Si le chargement échoue, un message d’erreur s’affiche.

Envoyez un e-mail à l’adresse <ms-horizon-support@microsoft.com> pour obtenir plus d’informations sur l’utilisation du kit SDK Open Development Environment (ODE) et sur la création de plug-ins de protocole.

## <a name="enable-and-disable-plugins"></a>Activer et désactiver les plug-ins

Utilisez le bouton bascule pour activer et désactiver les plug-ins. Lorsqu’ils sont désactivés, le trafic n’est plus analysé.

Les plug-ins de l’infrastructure ne peuvent pas être désactivés.

## <a name="monitor-plugin-performance"></a>Surveiller les performances des plug-ins 

La fenêtre Vue d’ensemble de la console Horizon fournit des informations de base sur les plug-ins que vous avez chargés et vous permet de les activer et de les désactiver.

:::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-overview.png" alt-text="Capture d’écran de la page de présentation d’Horizon."::: 

| Application | Nom du plug-in que vous avez chargé. |
|--|--|
| :::image type="icon" source="media/how-to-manage-proprietary-protocols/toggle-icon.png" border="false"::: | Activez ou désactivez le plug-in. Le capteur ne traite pas le trafic du protocole défini dans le plug-in lorsque vous désactivez le plug-in. |
| Heure | Heure à laquelle les données ont été analysées pour la dernière fois. Mise à jour toutes les cinq secondes. |
| PPS | Nombre de paquets par seconde. |
| Bande passante | Bande passante moyenne détectée au cours des cinq dernières secondes. |
| Malformations | Validations malformées utilisées après que le protocole a été validé positivement. En cas d’échec du traitement des paquets basé sur le protocole, une réponse d’échec est retournée.<br/> <br />Cette colonne indique le nombre d’erreurs de malformation au cours des cinq dernières secondes. |
| Avertissements | Les paquets correspondent à la structure et à la spécification, mais le comportement est inattendu et basé sur la configuration d’avertissement du plug-in. |
| Erreurs | Nombre de paquets ayant échoué aux validations de base du protocole et qui correspondent aux définitions du protocole.  Le nombre affiché ici indique le nombre d’erreurs détectées au cours des cinq dernières secondes. |
| :::image type="icon" source="media/how-to-manage-proprietary-protocols/monitor-icon.png" border="false"::: | Passez en revue les détails sur les malformations et les avertissements détectés pour votre plug-in. |

### <a name="plugin-performance-details"></a>Détails des performances du plug-in

Vous pouvez surveiller les performances du plug-in en temps réel en analysant le nombre de malformations et d’avertissements détectés pour votre plug-in. Une option est disponible pour geler l’écran et l’exporter pour une enquête plus approfondie.

:::image type="content" source="media/how-to-manage-proprietary-protocols/snmp-monitor.png" alt-text="Capture d’écran de la vue d’ensemble du moniteur SNMP.":::

### <a name="horizon-logs"></a>Journaux Horizon

Les informations sur la dissection d’Horizon sont disponibles pour l’exportation dans les détails de la dissection, les journaux de dissection et les journaux d’exportation.

:::image type="content" source="media/how-to-manage-proprietary-protocols/export-logs-details.png" alt-text="Détails de la dissection des journaux exportés.":::

## <a name="trigger-horizon-alerts"></a>Déclencher des alertes Horizon 

Améliorez la gestion des alertes au sein de votre entreprise en déclenchant des alertes personnalisées pour n’importe quel protocole basé sur les dissecteurs de trafic de l’infrastructure Horizon. 

Ces alertes peuvent être utilisées pour communiquer des informations :  

  - Sur les détections de trafic basées sur les protocoles et les protocoles sous-jacents dans un plug-in Horizon propriétaire.

  - Sur une combinaison de champs de protocole à partir de toutes les couches de protocole. Par exemple, dans un environnement exécutant MODBUS, vous souhaiterez peut-être générer une alerte quand le capteur détecte une commande d’écriture dans un registre de mémoire sur une adresse IP et une destination Ethernet spécifiques, ou une alerte quand un accès à une adresse IP spécifique est effectué.

Les alertes sont déclenchées lorsque les conditions des règles d’alerte Horizon qui ont été définies sont remplies.

  :::image type="content" source="media/how-to-manage-proprietary-protocols/custom-alert-rules.png" alt-text="Exemples de règles personnalisées pour Horizon.":::

En outre, l’utilisation des alertes personnalisées Horizon vous permet d’écrire vos propres titres et messages d’alerte. Les valeurs et les champs de protocole résolus peuvent également être incorporés dans le texte du message d’alerte.

L’utilisation de la messagerie et du déclenchement d’alertes basés sur des conditions personnalisées permet d’identifier l’activité réseau et de mettre à jour efficacement vos équipes de sécurité, informatiques et opérationnelles.

### <a name="working-with-horizon-alerts"></a>Utilisation des alertes Horizon

Les alertes générées par les règles d’alerte personnalisées Horizon s’affichent dans la fenêtre Alertes de la console de gestion et du capteur et dans les systèmes partenaires intégrés lors de l’utilisation de règles de transfert. 

Les alertes générées par Horizon peuvent être acceptées ou désactivées. L’option d’apprentissage n’est pas disponible pour les alertes personnalisées, car les événements d’alerte ne peuvent pas être appris à la ligne de base de stratégie.

Les informations d’alerte sont transférées aux fournisseurs partenaires lorsque des règles de transfert sont utilisées.

La gravité des alertes personnalisées Horizon est critique.

Les alertes personnalisées Horizon incluent du texte statique sous la section **Gérer cet événement** qui indique que l’alerte a été générée par l’équipe de sécurité de votre organisation.

### <a name="required-permissions"></a>Autorisations requises

Les utilisateurs définis comme utilisateurs Defender pour IoT sont autorisés à créer des règles d’alerte personnalisées Horizon.

### <a name="about-creating-rule-conditions"></a>À propos de la création de conditions de règle

Les conditions de règle décrivent le trafic qui doit être détecté pour déclencher l’alerte. Les conditions de règle peuvent comporter un ou plusieurs ensembles de champs, d’opérateurs et de valeurs. Créez des ensembles de conditions à l’aide de la condition **AND**.

Lorsque la condition de règle ou l’ensemble de conditions défini est rempli, l’alerte est envoyée. Vous serez averti si la logique de condition n’est pas valide.

  :::image type="content" source="media/how-to-manage-proprietary-protocols/and-condition.png" alt-text="Utilisez la condition AND pour votre règle personnalisée.":::

Vous pouvez également créer plusieurs règles pour un protocole. Cela signifie qu’une alerte est déclenchée pour chaque règle que vous avez créée lorsque les conditions de la règle sont remplies.

### <a name="about-titles-and-messages"></a>À propos des titres et des messages

Les messages d’alerte peuvent contenir des caractères alphanumériques que vous entrez, ainsi que des variables de trafic détectées. Par exemple, incluez les adresses source et de destination détectées dans les messages d’alerte. Plusieurs langues sont prises en charge.

### <a name="about-alert-recommendations"></a>À propos des recommandations d’alerte 

Les alertes personnalisées Horizon incluent du texte statique sous la section **Gérer cet événement** qui indique que l’alerte a été générée par l’équipe de sécurité de votre organisation. Vous pouvez également utiliser des commentaires d’alerte pour améliorer la communication entre les individus et les équipes qui lisent votre alerte. 

## <a name="create-horizon-alert-rules"></a>Créer des règles d’alerte Horizon

Cet article explique comment créer la règle d’alerte.

Pour créer des alertes personnalisées Horizon :

1. Cliquez avec le bouton droit sur un plug-in dans le menu des plug-ins de la console Horizon.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/plugins-menu.png" alt-text="Cliquez avec le bouton droit sur un plug-in dans le menu.":::

2. Sélectionnez **Alertes personnalisées Horizon**. La fenêtre **Règle** s’ouvre pour le plug-in que vous avez sélectionné.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/sample-rule-window.png" alt-text="La fenêtre de l’exemple de règle s’ouvre pour votre plug-in.":::

3. Entrez un titre dans le champ Titre.

4. Entrez un message d’alerte dans le champ Message. Utilisez des accolades `{}` pour inclure les paramètres de champ détectés dans le message. Lorsque vous entrez la première accolade, les champs pertinents s’affichent.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/rule-window.png" alt-text="Utilisez {} dans la fenêtre de règle pour inclure les champs détectés.":::

5. Définissez des conditions d’alerte.

   :::image type="content" source="media/how-to-manage-proprietary-protocols/define-conditions.png" alt-text="Définissez les conditions de l’alerte.":::

6. Sélectionnez une **variable**. Les variables représentent les champs configurés dans le plug-in.

7. Sélectionnez un **opérateur** :

    - Égal à
    
    - Différent de
    
    - Inférieur à
    
    - Inférieur ou égal à
    
    - Supérieur à
    
    - Supérieur ou égal à

8. Entrez une **valeur** sous la forme d’un nombre. Si la variable que vous avez sélectionnée est une adresse MAC ou une adresse IP, la valeur doit être convertie d’une adresse décimale séparée par des points au format décimal. Utilisez un outil de conversion d’adresses IP, par exemple <https://www.ipaddressguide.com/ip>.

    :::image type="content" source="media/how-to-manage-proprietary-protocols/ip-address-value.png" alt-text="Valeur de l’adresse IP traduite.":::

9. Sélectionnez **AND** pour créer un ensemble de conditions.

10. Sélectionnez **SAVE** (Enregistrer). La règle est ajoutée à la section Règles.

### <a name="edit-and-delete-horizon-custom-alert-rules"></a>Modifier et supprimer des règles d’alerte personnalisées Horizon

Utilisez les options Modifier et Supprimer le cas échéant. Certaines règles sont incorporées et ne peuvent être ni modifiées ni supprimées.

### <a name="create-multiple-rules"></a>Créer plusieurs règles

Lorsque vous créez plusieurs règles, des alertes sont déclenchées lorsque toute condition de règle ou tout ensemble de conditions défini est valide.

## <a name="see-also"></a>Voir aussi

[Afficher les informations fournies dans les alertes](how-to-view-information-provided-in-alerts.md)
