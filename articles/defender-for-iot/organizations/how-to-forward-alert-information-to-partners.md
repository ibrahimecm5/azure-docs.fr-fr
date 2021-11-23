---
title: Transférer les informations d’alerte
description: Vous pouvez envoyer des informations d’alerte à des systèmes partenaires en utilisant des règles de transfert.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: d863ab9de5e030ddd54ba1b4b40efc01e844f419
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278851"
---
# <a name="forward-alert-information"></a>Transférer les informations d’alerte

Vous pouvez envoyer des informations d’alerte à des partenaires qui s’intègrent à Microsoft Defender pour IoT, à des serveurs Syslog, à des adresses e-mail et bien plus encore. L’utilisation de règles de transfert vous permet de transmettre rapidement des informations d’alerte aux parties prenantes de la sécurité.

Définissez les critères de déclenchement d’une règle de transfert. L’utilisation de critères de règle de transfert permet d’identifier et de gérer le volume d’informations envoyé du capteur aux systèmes externes.

Syslog et d’autres actions de transfert par défaut sont fournis avec votre système. D’autres actions de transfert peuvent être disponibles lorsque vous intégrez des fournisseurs partenaires, par exemple Microsoft Sentinel, ServiceNow ou Splunk.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alert-information-screen.png" alt-text="Informations d’alerte.":::

Les administrateurs Defender pour IoT sont autorisés à utiliser les règles de transfert.

## <a name="about-forwarded-alert-information"></a>À propos des informations d’alerte transférées

Les alertes fournissent des informations sur un large éventail d’événements opérationnels et de sécurité. Par exemple :

- Date et heure de l’alerte

- Moteur ayant détecté l’événement

- Titre de l’alerte et message descriptif

- Gravité de l’alerte

- Nom et adresse IP de la source et de la destination

- Trafic suspect détecté

:::image type="content" source="media/how-to-work-with-alerts-sensor/address-scan-detected-screen.png" alt-text="Analyse des adresses détectée.":::

Les informations pertinentes sont envoyées aux systèmes partenaires lors de la création des règles de transfert.

## <a name="about-forwarding-rules-and-certificates"></a>À propos des règles de transfert et des certificats

Certaines règles de transfert autorisent le chiffrement et la validation de certificat entre le capteur ou la console de gestion locale, et le serveur du fournisseur intégré.

Quand c’est le cas, le capteur ou la console de gestion locale est le client et l’initiateur de la session.  Les certificats sont généralement reçus du serveur ou utilisent le chiffrement asymétrique quand un certificat spécifique est fourni pour configurer l’intégration.

Votre système Defender pour IoT a été configuré pour valider les certificats ou ignorer la validation de certificat.  Pour plus d’informations sur l’activation et la désactivation de la validation, consultez [À propos de la validation des certificats](how-to-deploy-certificates.md#about-certificate-validation).

Si la validation est activée et que le certificat ne peut pas être vérifié, la communication entre Defender pour IoT et le serveur est interrompue.  Le capteur affiche un message d’erreur indiquant l’échec de la validation.  Si la validation est désactivée et que le certificat n’est pas valide, la communication continue.

Les règles de transfert suivantes autorisent le chiffrement et la validation des certificats :
- Syslog CEF
- Microsoft Sentinel
- QRadar

## <a name="create-forwarding-rules"></a>Créer des règles de transfert

**Pour créer une nouvelle règle de transfert sur un capteur** :

1. Connectez-vous au capteur.

1. Sélectionnez **Transfert** dans le menu latéral.

1. Sélectionnez **Créer une règle de transfert**.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/create-forwarding-rule-screen.png" alt-text="Créer une icône Règle de transfert.":::

1. Entrez un nom pour la règle de transfert.

1. Sélectionnez le niveau de gravité.

   Il s’agit de l’incident minimal à transférer, en termes de niveau de gravité. Par exemple, si vous sélectionnez **Mineur**, les alertes mineures et toute alerte supérieure à ce niveau de gravité seront transférées. Les niveaux sont prédéfinis.

1. Sélectionnez les protocoles à appliquer.

   Déclenche uniquement la règle de transfert si le trafic détecté s’exécutait sur des protocoles spécifiques. Sélectionnez les protocoles requis dans la liste déroulante ou sélectionnez-les tous.

1. Sélectionnez les moteurs auxquels la règle doit s’appliquer.

   Sélectionnez les moteurs requis ou choisissez-les tous. Les alertes des moteurs sélectionnés seront envoyées. 

1. Sélectionnez une action à appliquer et entrez les paramètres requis pour celle-ci.

   Les actions de règle de transfert demandent au capteur de transférer les informations d’alerte aux fournisseurs ou serveurs partenaires. Vous pouvez créer plusieurs actions pour chaque règle de transfert.

1. Ajoutez toute autre action souhaitée.

1. Sélectionnez **Envoyer**.

### <a name="email-address-action"></a>Action d’adresse e-mail

Envoyez un e-mail qui contient les informations d’alerte. Vous pouvez entrer une adresse e-mail par règle.

Pour définir l’e-mail pour la règle de transfert :

1. Entrez une seule adresse e-mail. Si vous devez ajouter plusieurs e-mails, vous devez créer une autre action pour chaque adresse e-mail.

    :::image type="content" source="media/how-to-forward-alert-information-to-partners/forward-email.png" alt-text="Capture d’écran de l’alerte de transfert pour transférer les alertes à une adresse de e-mail.":::

1. Entrez le fuseau horaire de l’horodatage pour la détection d’alerte sur les SIEM.

1. Sélectionnez **Envoyer**.

### <a name="syslog-server-actions"></a>Actions du serveur Syslog

Les formats suivants sont pris en charge :

- SMS

- Messages CEF

- Messages LEEF

- Messages d’objet

:::image type="content" source="media/how-to-work-with-alerts-sensor/create-actions-rule.png" alt-text="Créer des actions de règle de transfert.":::

Entrez les paramètres suivants :

- Nom d’hôte et port Syslog.

- Protocole TCP et UDP.

- Fuseau horaire de l’horodatage pour la détection d’alerte sur les SIEM.

- Fichier de clé et fichier de certificat de chiffrement TLS pour les serveurs CEF (facultatif).

:::image type="content" source="media/how-to-work-with-alerts-sensor/configure-encryption.png" alt-text="Configurez votre chiffrement pour votre règle de transfert.":::

| Champs de sortie des SMS à Syslog | Description |
|--|--|
| Date et heure | Date et heure auxquelles le serveur Syslog a reçu les informations. |
| Priority | User.Alert |
| HostName | Adresse IP du capteur |
| Protocol | TCP ou UDP |
| Message | Capteur : Nom du capteur.<br /> Alerte : Titre de l’alerte.<br /> Tapez : Type de l’alerte. Peut être **Violation de protocole**, **Violation de stratégie**, **Programme malveillant**, **Anomalie** ou **Opérationnelle**.<br /> Gravité : Gravité de l’alerte. Peut être **Avertissement**, **Mineur**, **Majeur** ou **Critique**.<br /> Source : Nom de l’appareil source.<br /> IP source : Adresse IP de l’appareil source.<br /> Destination : Nom de l’appareil de destination.<br /> Adresse IP de destination : Adresse IP de l’appareil de destination.<br /> Message : Message de l’alerte.<br /> Groupe d’alertes : Groupe d’alertes associé à l’alerte. |

| Sortie de l’objet Syslog | Description |
|--|--|
| Date et heure | Date et heure auxquelles le serveur Syslog a reçu les informations. |  
| Priority | User.Alert |
| HostName | IP du capteur |
| Message | Nom du capteur : Nom de l’appliance. <br /> Heure de l’alerte : Heure à laquelle l’alerte a été détectée : Peut varier de l’heure du serveur Syslog et dépend de la configuration du fuseau horaire de la règle de transfert. <br /> Titre de l’alerte : Titre de l’alerte. <br /> Message d’alerte : Message de l’alerte. <br /> Gravité d’alerte : Gravité de l’alerte : **Avertissement**, **Mineur**, **Majeur** ou **Critique**. <br /> Type d’alerte : **Violation de protocole**, **Violation de stratégie**, **Programme malveillant**, **Anomalie** ou **Opérationnelle**. <br /> Protocole : Protocole de l’alerte.  <br /> **Source_MAC** : Adresse IP, nom, fournisseur ou système d’exploitation de l’appareil source. <br /> Destination_MAC : Adresse IP, nom, fournisseur ou système d’exploitation de l’appareil de destination. Si des données sont manquantes, la valeur sera **s.o.** . <br /> alert_group : Groupe d’alertes associé à l’alerte. |

| Format de sortie CEF Syslog | Description |
|--|--|
| Date et heure | Date et heure auxquelles le serveur Syslog a reçu les informations. |
| Priority | User.Alert |
| HostName | Adresse IP du capteur |
| Message | CEF:0 <br />Microsoft Defender pour IoT <br />Nom du capteur : Nom de l’appliance de détection. <br />Version du capteur <br />Titre de l’alerte : Titre de l’alerte. <br />msg : Message de l’alerte. <br />protocole : Protocole de l’alerte. <br />gravité :  **Avertissement**, **Mineur**, **Majeur** ou **Critique**. <br />type :  **Violation de protocole**, **Violation de stratégie**, **Programme malveillant**, **Anomalie** ou **Opérationnelle**. <br /> start : Heure à laquelle l’alerte a été détectée. <br />Peut varier de l’heure du serveur Syslog et dépend de la configuration du fuseau horaire de la règle de transfert. <br />src_ip : Adresse IP de l’appareil source.  <br />dst_ip : Adresse IP de l’appareil de destination.<br />cat : Groupe d’alertes associé à l’alerte.  |

| Format de sortie LEEF Syslog | Description |
|--|--|
| Date et heure | Date et heure auxquelles le serveur Syslog a reçu les informations. |  
| Priority | User.Alert |
| HostName | IP du capteur |
| Message | Nom du capteur : nom de l’appliance Microsoft Defender pour IoT. <br />LEEF:1.0 <br />Microsoft Defender pour IoT <br />Capteur  <br />Version du capteur <br />Alerte Microsoft Defender pour IoT <br />titre : Titre de l’alerte. <br />msg : Message de l’alerte. <br />protocole : Protocole de l’alerte.<br />gravité :  **Avertissement**, **Mineur**, **Majeur** ou **Critique**. <br />type : Type de l’alerte : **Violation de protocole**, **Violation de stratégie**, **Programme malveillant**, **Anomalie** ou **Opérationnelle**. <br />start : Heure de l’alerte.Elle peut être différente de l’heure de la machine du serveur Syslog. (Cela dépend de la configuration du fuseau horaire.) <br />src_ip : Adresse IP de l’appareil source.<br />dst_ip : Adresse IP de l’appareil de destination. <br />cat : Groupe d’alertes associé à l’alerte. |

Après avoir entré toutes les informations, sélectionnez **Envoyer**.

### <a name="webhook-server-action"></a>Action du serveur Webhook

Envoyez des informations d’alerte à un serveur Webhook. L’utilisation de serveurs Webhook vous permet de configurer des intégrations qui s’abonnent à des événements d’alerte avec Defender pour IoT. Lors du déclenchement d’un événement d’alerte, la console de gestion envoie une charge utile HTTP POST à l’URL configurée du Webhook. Les Webhooks peuvent être utilisés pour mettre à jour un système SIEM externe, des systèmes SOAR, des systèmes de gestion des incidents, etc.

**Pour définir une action de Webhook :**

1. Sélectionnez l’action de Webhook.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/webhook.png" alt-text="Définissez une règle de transfert de Webhook.":::

1. Entrez l’adresse du serveur dans le champ **URL**.

1. Dans les champs **Clé** et **Valeur**, personnalisez l’en-tête HTTP avec une définition de clé et de valeur. Les clés peuvent uniquement contenir des lettres, des chiffres, des tirets et des traits de soulignement. Les valeurs peuvent uniquement contenir une espace de début et/ou une espace de fin.

1. Sélectionnez **Enregistrer**.

### <a name="webhook-extended"></a>Webhook étendu

Un webhook étendu peut être utilisé pour envoyer des données supplémentaires au point de terminaison. La fonctionnalité étendue inclut toutes les informations de l’alerte de webhook et ajoute les informations suivantes au rapport :

- sensorID
- sensorName
- zoneID
- zoneName
- siteID
- siteName
- sourceDeviceAddress
- destinationDeviceAddress
- remediationSteps
- handled
- additionalInformation

**Pour définir une action étendue de webhook** :

1. Dans la console de gestion, dans le volet gauche, sélectionnez **Transfert**.

1. Ajoutez une règle de transfert en sélectionnant le bouton :::image type="icon" source="media/how-to-forward-alert-information-to-partners/add-icon.png" border="false":::.

1. Ajoutez un nom explicite pour l’alerte de transfert.

1. Sélectionner un niveau de gravité.

1. Sélectionnez **Ajouter**.

1. Dans la fenêtre déroulante Sélectionner un type, sélectionnez **Webhook étendu**.

   :::image type="content" source="media/how-to-forward-alert-information-to-partners/webhook-extended.png" alt-text="Sélectionnez l’option Webhook étendu dans le menu déroulant Sélectionner un type.":::

1. Dans le champ URL, ajoutez l’URL des données de point de terminaison.

1. (Facultatif) Personnalisez l’en-tête HTTP avec une définition de clé et de valeur. Ajoutez un des en-têtes en sélectionnant le bouton :::image type="icon" source="media/how-to-forward-alert-information-to-partners/add-header.png" border="false":::.

1. Sélectionnez **Enregistrer**.

Une fois la règle de transfert Webhook étendu configurée, vous pouvez tester l’alerte à partir de l’écran Transfert de la console de gestion.

**Pour tester la règle de transfert Webhook étendu** :

1. Dans la console de gestion, dans le volet gauche, sélectionnez **Transfert**.

1. Sélectionnez le bouton **Exécuter** pour tester votre alerte.

    :::image type="content" source="media/how-to-forward-alert-information-to-partners/run-button.png" alt-text="Sélectionnez le bouton Exécuter pour tester votre règle de transfert.":::

Vous saurez que la règle de transfert fonctionne si vous voyez s’afficher la notification Réussite.

:::image type="content" source="media/how-to-forward-alert-information-to-partners/success.png" alt-text="L’alerte a fonctionné correctement si la notification Réussite s’affiche.":::

### <a name="netwitness-action"></a>Action NetWitness

Envoyez des informations d’alerte à un serveur NetWitness.

Pour définir les paramètres de transfert de NetWitness :

1. Entrez les informations **Nom d’hôte** et **Port** de NetWitness.

1. Entrez le fuseau horaire de l’horodatage pour la détection d’alerte sur les SIEM.

   :::image type="content" source="media/how-to-work-with-alerts-sensor/add-timezone.png" alt-text="Ajoutez un fuseau horaire à votre règle de transfert.":::

1. Sélectionnez **Envoyer**.

### <a name="integrated-vendor-actions"></a>Actions des fournisseurs intégrés

Vous avez peut-être intégré un fournisseur de sécurité, de gestion de périphériques ou d’autres secteurs d’activité à votre système. Ces intégrations vous permettent d’effectuer les actions suivantes :

  - Envoyer des informations d’alerte.

  - Envoyer des informations de l’inventaire des appareils.

  - Communiquer avec les pare-feu côté fournisseur.

Les intégrations permettent de faire le pont entre des solutions de sécurité auparavant cloisonnées, d’améliorer la visibilité des appareils et d’accélérer la réponse à l’échelle du système afin d’atténuer plus rapidement les risques.

Utilisez la section Actions pour entrer les informations d’identification et d’autres informations nécessaires pour communiquer avec les fournisseurs intégrés.

Pour plus d’informations sur la configuration des règles de transfert pour les intégrations, reportez-vous aux articles pertinents sur l’intégration des partenaires.

## <a name="test-forwarding-rules"></a>Tester les règles de transfert

Testez la connexion entre le capteur et le serveur partenaire défini dans vos règles de transfert :

1. Sélectionnez la règle dans la boîte de dialogue **Règle de transfert**.

1. Cochez la case **Plus**.

1. Sélectionnez **Envoyer un message de test**.

1. Accédez au système de votre partenaire pour vérifier que les informations envoyées par le capteur ont été reçues.

## <a name="edit-and-delete-forwarding-rules"></a>Modifier et supprimer des règles de transfert 

**Pour modifier une règle de transfert** :

- Dans l’écran **Règle de transfert**, sélectionnez **Modifier** sous le menu déroulant **Plus**. Apportez les modifications souhaitées, puis sélectionnez **Envoyer**.

**Pour supprimer une règle de transfert** :

- Dans l’écran **Règle de transfert**, sélectionnez **Supprimer** sous le menu déroulant **Plus**. Dans la boîte de dialogue **Avertissement**, sélectionnez **OK**.

## <a name="forwarding-rules-and-alert-exclusion-rules"></a>Règles de transfert et règles d’exclusion d’alerte

L’administrateur peut avoir défini des règles d’exclusion d’alerte. Ces règles aident les administrateurs à obtenir un contrôle plus granulaire sur le déclenchement des alertes en demandant au capteur d’ignorer les événements d’alerte en fonction de différents paramètres. Ces paramètres peuvent inclure des adresses d’appareil, des noms d’alerte ou des capteurs spécifiques.

Cela signifie que les règles de transfert que vous définissez peuvent être ignorées en fonction des règles d’exclusion créées par votre administrateur. Les règles d’exclusion sont définies dans la console de gestion locale.

## <a name="see-also"></a>Voir aussi

[Accélérer les workflows d’alerte](how-to-accelerate-alert-incident-response.md)
