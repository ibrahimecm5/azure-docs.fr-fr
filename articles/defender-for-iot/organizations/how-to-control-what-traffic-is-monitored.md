---
title: Contrôler quel trafic est surveillé
description: Les capteurs effectuent automatiquement la détection profonde de paquets pour le trafic informatique et le trafic OT et déterminent des informations sur les périphériques réseau, telles que les attributs d’appareil et le comportement réseau. Plusieurs outils sont disponibles pour contrôler le type de trafic détecté par chaque capteur.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 76923afc40469ba30e4a2ce8ead6696f056a7580
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305937"
---
# <a name="control-what-traffic-is-monitored"></a>Contrôler quel trafic est surveillé

Les capteurs effectuent automatiquement la détection profonde de paquets pour le trafic informatique et le trafic OT et déterminent des informations sur les périphériques réseau, telles que le comportement et les attributs d’appareil. Plusieurs outils sont disponibles pour contrôler le type de trafic détecté par chaque capteur.

## <a name="learning-and-smart-it-learning-modes"></a>Apprentissage et modes d’apprentissage informatique intelligent

Le mode d’apprentissage donne l’ordre à votre capteur d’apprendre l’activité habituelle de votre réseau. Cela inclut par exemple, les périphériques découverts dans votre réseau, les protocoles détectés sur le réseau, les transferts de fichiers entre des appareils spécifiques, etc. Cette activité devient la ligne de base de votre réseau.

Le mode d’apprentissage est activé automatiquement après l’installation et reste actif tant que vous ne le désactivez pas. La durée du mode d’apprentissage approximative est comprise entre deux et six semaines, en fonction de la taille et de la complexité du réseau. Après cette période, lorsque le mode d’apprentissage est désactivé, toute nouvelle activité détectée déclenche des alertes. Les alertes sont déclenchées lorsque le moteur de stratégie découvre des écarts par rapport à la ligne de base apprise.

Une fois la période d’apprentissage terminée et le mode d’apprentissage désactivé, le capteur peut détecter un niveau anormalement élevé de modifications de ligne de base qui sont le résultat d’une activité informatique normale, comme des requêtes DNS et HTTP. Cette activité est appelée comportement informatique non déterministe. Le comportement peut également déclencher des alertes de violation de stratégie et des notifications système inutiles. Pour réduire la quantité de ces alertes et notifications, vous pouvez activer la fonction **Apprentissage informatique intelligent**.

Lorsque l’apprentissage informatique intelligent est activé, le capteur effectue le suivi du trafic qui génère un comportement informatique non déterministe basé sur des scénarios d’alerte spécifiques.

Le capteur surveille ce trafic pendant sept jours. S’il détecte le même trafic informatique non déterministe pendant les sept jours, il continue à surveiller le trafic sept jours de plus. Lorsque le trafic n’est pas détecté pendant sept jours, l’apprentissage informatique intelligent est désactivé pour ce scénario. Le nouveau trafic détecté pour ce scénario ne génère ensuite que des alertes et des notifications.

L’utilisation de l’apprentissage informatique intelligent vous permet de réduire le nombre d’alertes et de notifications inutiles provoquées par les scénarios informatiques bruyants.

Si votre capteur est contrôlé par la console de gestion locale, vous ne pouvez pas désactiver les modes d’apprentissage. Dans ce type de situation, le mode d’apprentissage peut uniquement être désactivé à partir de la console de gestion.

Les fonctionnalités d’apprentissage (apprentissage et apprentissage informatique intelligent) sont activées par défaut.

Pour activer ou désactiver l’apprentissage :

- Sélectionnez **Paramètres système** et activez/désactivez les options **Apprentissage** et **Apprentissage informatique intelligent**.

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="Écran d’activation/désactivation des paramètres système.":::

## <a name="configure-subnets"></a>Configuration des sous-réseaux

Les configurations de sous-réseaux affectent la manière dont les périphériques s’affichent dans la carte des appareils.

Par défaut, le capteur détecte votre configuration de sous-réseau et remplit la boîte de dialogue **Configuration de sous-réseau** avec ces informations.

Pour permettre la focalisation sur les appareils OT, les appareils informatiques sont automatiquement regroupés par sous-réseau dans la carte des appareils. Chaque sous-réseau est présenté sous la forme d’une entité unique sur la carte, avec une capacité de réduction/d’extension interactive pour procéder à un « zoom avant » sur un sous-réseau informatique.

Lorsque vous utilisez des sous-réseaux, sélectionnez les sous-réseaux ICS pour identifier les sous-réseaux OT. Vous pouvez ensuite concentrer la vue cartographique sur les réseaux OT et ICS et réduire au minimum la présentation des éléments du réseau informatique. Cet effort réduit le nombre total d’appareils affichés sur la carte et fournit une image claire des éléments réseau OT et ICS.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="Capture d’écran montrant le filtrage sur un réseau.":::

Vous pouvez modifier la configuration ou changer manuellement les informations de sous-réseaux en exportant les données découvertes, en les modifiant manuellement, puis en réimportant la liste des sous-réseaux que vous avez définis manuellement. Pour plus d’informations sur l’exportation et l’importation, consultez [Importer des informations sur l’appareil](how-to-import-device-information.md).

Dans certains cas, par exemple pour les environnements qui utilisent des plages publiques comme plages internes, vous pouvez demander au capteur de résoudre tous les sous-réseaux en tant que sous-réseaux internes en sélectionnant l’option **Ne pas détecter l’activité Internet**. Lorsque vous sélectionnez cette option :

- Les adresses IP publiques sont traitées comme des adresses locales.

- Aucune alerte n’est envoyée à propos des activités Internet non autorisées, ce qui réduit les notifications et les alertes reçues sur les adresses externes.

Pour configurer des sous-réseaux :

1. Dans le menu latéral, sélectionnez **Paramètres système**.

2. Dans la fenêtre **Paramètre système**, sélectionnez **Sous-réseaux**.

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="Capture de l’écran de configuration des sous-réseaux."::: 

3. Pour ajouter automatiquement des sous-réseaux lorsque de nouveaux appareils sont découverts, ne désélectionnez pas **Apprentissage automatique des sous-réseaux**.

4. Pour résoudre tous les sous-réseaux en tant que sous-réseaux internes, sélectionnez **Ne pas détecter l’activité Internet**.

5. Sélectionnez **Ajouter un réseau** et définissez les paramètres suivants pour chaque sous-réseau :

    - adresse IP du sous-réseau,
    - adresse du masque du sous-réseau,
    - nom du sous-réseau. Nous vous recommandons de nommer chaque sous-réseau avec un nom explicite que vous pouvez facilement identifier, afin de pouvoir faire la différence entre les réseaux informatiques et les réseaux OT. Le nom peut comprendre jusqu’à 60 caractères.

6. Pour marquer ce sous-réseau en tant que sous-réseau OT, sélectionnez **Sous-réseau ICS**.

7. Pour présenter le sous-réseau séparément lorsque vous organisez la carte en fonction du niveau Purdue, sélectionnez **Séparé**.

8. Pour supprimer un sous-réseau, sélectionnez :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false":::.

9. Pour supprimer tous les sous-réseaux, sélectionnez **Tout supprimer**.

10. Pour exporter les sous-réseaux configurés, sélectionnez **Exporter**. Le tableau de sous-réseaux est téléchargé sur votre station de travail.

11. Sélectionnez **Enregistrer**.

### <a name="importing-information"></a>Importation d’informations 

Pour importer des informations de sous-réseau, sélectionnez **Importer** puis sélectionnez un fichier CSV à importer. Les informations de sous-réseau sont mises à jour avec les informations que vous avez importées. Si vous importez un champ vide, vous perdrez vos données.

## <a name="detection-engines"></a>Moteurs de détection

Des moteurs d’analyse d’apprentissage automatique éliminent la nécessité de mettre à jour les signatures ou de définir des règles. Les moteurs utilisent l’analyse comportementale et la science des données propres à ICS pour analyser en permanence le trafic OT à la recherche d’anomalies, de logiciels malveillants, de problèmes opérationnels, de violations de protocole et d’écarts par rapport à l’activité réseau de ligne de base.

> [!NOTE]
> Nous vous recommandons d’activer tous les moteurs de sécurité.

Lorsqu’un moteur détecte un écart, une alerte est déclenchée. Vous pouvez afficher et gérer les alertes depuis l’écran d’alerte ou à partir d’un système partenaire.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="Capture d’écran illustrant la détection d’un écart.":::

Le nom du moteur qui a déclenché l’alerte s’affiche sous le titre de l’alerte.

### <a name="protocol-violation-engine"></a>Moteur de violation de protocole 

Une violation de protocole se produit lorsque la structure de paquets ou les valeurs de champ ne sont pas conformes à la spécification de protocole.

Exemple de scénario : Alerte *« Opération MODBUS illégale (code de fonction zéro) »* . Cette alerte indique qu’un appareil principal a envoyé une requête avec le code de fonction 0 à un appareil secondaire. Cette action n’est pas autorisée conformément aux spécifications du protocole, et l’appareil secondaire peut ne pas gérer correctement l’entrée.

### <a name="policy-violation-engine"></a>Moteur de violation de stratégie

Une violation de stratégie se produit lorsqu’il y a un écart par rapport au comportement de la ligne de base défini dans les paramètres appris ou configurés.

Exemple de scénario : Alerte *« Agent utilisateur HTTP non autorisé »* . Cette alerte indique qu’une application qui n’a pas été apprise ou approuvée par la stratégie est utilisée comme client HTTP sur un appareil. Il peut s’agir d’un nouveau navigateur web ou d’une nouvelle application sur cet appareil.

### <a name="malware-engine"></a>Moteur de programme malveillant

Le moteur de programme malveillant détecte l’activité de réseau malveillante.

Exemple de scénario : Alerte *« Suspicion d’activité malveillante (Stuxnet) »* . Cette alerte indique que le capteur a détecté une activité réseau suspecte connue pour être liée au programme malveillant Stuxnet. Ce programme malveillant est une menace avancée permanente qui vise le contrôle industriel et les réseaux SCADA.

### <a name="anomaly-engine"></a>Moteur d’anomalies

Le moteur d’anomalies détecte les anomalies dans le comportement du réseau.

Exemple de scénario : Alerte *« Comportement périodique dans le canal de communication »* . Le composant inspecte les connexions réseau et détecte le comportement périodique et cyclique de la transmission de données. Ce comportement est courant dans les réseaux industriels.

### <a name="operational-engine"></a>Moteur opérationnel

Le moteur opérationnel détecte les incidents opérationnels ou les entités défectueuses.

Exemple de scénario : Alerte *« Suspicion de déconnexion de l’appareil (ne répond pas) »* . Cette alerte est déclenchée lorsqu’un appareil ne répond à aucun type de requête pour une période prédéfinie. Elle peut indiquer un arrêt, une déconnexion ou un dysfonctionnement de l’appareil.

### <a name="enable-and-disable-engines"></a>Activer et désactiver les moteurs

Lorsque vous désactivez un moteur de stratégie, le capteur n’a plus accès aux informations générées par le moteur. Par exemple, si vous désactivez le moteur d’anomalies, vous ne recevrez pas d’alertes sur les anomalies de réseau. Si vous avez créé une règle de transfert, les anomalies apprises par le moteur ne seront pas envoyées. Pour activer ou désactiver un moteur de stratégie, sélectionnez **Activé** ou **Désactivé** pour le moteur spécifique.

Le score global est affiché dans le coin inférieur droit de l’écran **Paramètres système**. Ce score indique le pourcentage de protection disponible activé par les moteurs de protection contre les menaces. Chaque moteur représente 20 % de la protection disponible.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="Capture d’écran d’un score.":::

## <a name="configure-dhcp-address-ranges"></a>Configurer des plages d’adresses DHCP

Votre réseau peut être constitué d’adresses IP statiques et dynamiques. En règle générale, les adresses statiques se trouvent sur les réseaux OT à l’aide d’historiens, de contrôleurs et d’appareils d’infrastructure réseau, tels que les commutateurs et les routeurs. L’allocation d’adresses IP dynamiques est généralement implémentée sur des réseaux invités pour des ordinateurs portables, des PC, des smartphones et d’autres équipements portables (utilisant des connexions physiques Wi-Fi ou LAN à différents emplacements).

Si vous travaillez avec des réseaux dynamiques, vous gérez les modifications d’adresse IP ayant lieu lorsque de nouvelles adresses IP sont affectées. Pour ce faire, vous devez définir des plages d’adresses DHCP.

Des modifications peuvent se produire, par exemple, lorsqu’un serveur DHCP attribue des adresses IP.

Définir des adresses IP dynamiques sur chaque capteur permet une prise en charge complète et transparente dans les instances de modifications d’adresse IP. Cela garantit la génération de rapports complets pour chaque appareil unique.

La console de capteur présente l’adresse IP la plus récente associée à l’appareil et indique quels appareils sont dynamiques. Par exemple :

- Le rapport d’exploration de données et le rapport d’inventaire des appareils regroupent l’ensemble des activités apprises par l’appareil en une seule entité, quelle que soit l’adresse IP modifiée. Ces rapports indiquent quelles adresses ont été définies comme adresses DHCP.

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="Capture d’écran de l’inventaire des appareils.":::

- La fenêtre **Propriétés de l’appareil** indique si l’appareil a été défini en tant qu’appareil DHCP.

Pour définir une plage d’adresses DHCP :

1.  Dans le menu latéral, sélectionnez **Plages DHCP** dans la fenêtre **Paramètres système**.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="Capture d’écran présentant la sélection de plages DHCP.":::

2.  Définissez une nouvelle plage en paramétrant les valeurs **À partir de** et **Vers**.

3.  Facultatif : Définissez le nom de la plage, jusqu’à 256 caractères.

4.  Pour exporter les plages vers un fichier CSV, sélectionnez **Exporter**.

5. Pour ajouter manuellement plusieurs plages à partir d’un fichier CSV, sélectionnez **Importer** puis sélectionnez le fichier.

    > [!NOTE]
    > Les plages que vous importez à partir d’un fichier CSV remplacent les paramètres de plage existants.

6. Sélectionnez **Enregistrer**.

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>Configurer des serveurs DNS pour la résolution de recherche inversée

Pour améliorer l’enrichissement des appareils, vous pouvez configurer plusieurs serveurs DNS pour effectuer des recherches inversées. Vous pouvez résoudre les noms d’hôte ou les noms de domaine complets associés aux adresses IP détectées dans les sous-réseaux du réseau. Par exemple, si un capteur découvre une adresse IP, il peut interroger plusieurs serveurs DNS pour résoudre le nom d’hôte.

Tous les formats CIDR sont pris en charge.

Le nom d’hôte s’affiche dans l’inventaire des appareils, ainsi que dans la carte des appareils et dans les rapports.

Vous pouvez planifier la résolution de recherche inversée à des intervalles spécifiques, toutes les 12 heures par exemple. Ou vous pouvez prévoir une heure spécifique.

Pour définir des serveurs DNS :

1. Sélectionnez **Paramètres système** puis **Paramètres DNS**.

2. Sélectionnez **Ajouter un serveur DNS**.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="Capture d’écran présentant la sélection d’un serveur DNS.":::

3. Dans le champ **Planifier la recherche DNS inversée**, choisissez :

    - Intervalles (par heure).
  
    - Moment spécifique. Utilisez la mise en forme européenne. Par exemple, utilisez **14:30** et non **2:30 PM**.

4. Dans le champ **Adresse du serveur DNS**, entrez l’adresse IP DNS.

5. Dans le champ **Port du serveur DNS**, entrez le port DNS.

6. Résolvez les adresses IP du réseau en noms de domaine complet d’appareil. Dans le champ **Nombre d’étiquettes**, ajoutez le nombre d’étiquettes de domaine à afficher. Jusqu’à 30 caractères s’affichent de gauche à droite.

7. Dans le champ **Sous-réseaux**, entrez les sous-réseaux que le serveur DNS doit interroger.

8. Sélectionnez le commutateur **Activer/Désactiver** si vous souhaitez lancer la recherche inversée.

### <a name="test-the-dns-configuration"></a>Vérifier la configuration DNS 

À l’aide d’un appareil de test, vérifiez que les paramètres que vous avez définis fonctionnent correctement :

1. Activez le commutateur **Recherche DNS**.

2. Sélectionnez **Test**.

3. Entrez une adresse dans **Adresse de recherche** pour la boîte de dialogue **Test de recherche DNS inversée pour le serveur**.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="Capture d’écran de la zone Adresse de recherche.":::

4. Sélectionnez **Test**.

## <a name="configure-windows-endpoint-monitoring"></a>Configuration de la surveillance des points de terminaison Windows

La capacité de surveillance des points de terminaison Windows vous permet de configurer Microsoft Defender pour IoT afin que la solution sonde de manière sélective les systèmes Windows. Vous disposez ainsi d’informations plus ciblées et précises sur vos appareils, telles que les niveaux de Service Pack.

Vous pouvez configurer la détection avec des plages et des hôtes spécifiques et la configurer pour qu’elle soit exécutée aussi souvent que vous le souhaitez. Effectuez une détection sélective à l’aide de Windows Management Instrumentation (WMI), le langage standard de script de Microsoft pour la gestion des systèmes Windows.

> [!NOTE]
> - Vous ne pouvez exécuter qu’une seule analyse à la fois.
> - Le résultat est optimal avec les utilisateurs disposant de privilèges d’administrateur local ou de domaine.
> - Avant de commencer la configuration de WMI, configurez une règle de pare-feu qui ouvre le trafic sortant du capteur vers le sous-réseau analysé à l’aide du port UDP 135 et de tous les ports TCP au-dessus de 1024.

Lorsque la détection est terminée, un fichier journal contenant toutes les tentatives est disponible à partir de l’option permettant d’exporter un journal. Le journal contient toutes les adresses IP qui ont été sondées. Pour chaque adresse IP, le journal affiche les informations relatives aux réussites et aux échecs. Il y a également un code d’erreur, qui est une chaîne gratuite dérivée de l’exception. Le système conserve uniquement l’analyse du dernier journal.

Vous pouvez effectuer des analyses planifiées ou des analyses manuelles. Une fois une analyse terminée, vous pouvez afficher les résultats dans un fichier CSV.

**Composants requis**

Configurez une règle de pare-feu qui ouvre le trafic sortant du capteur vers le sous-réseau analysé à l’aide du port UDP 135 et de tous les ports TCP au-dessus de 1024.

Pour configurer une analyse automatique :

1. Dans le menu latéral, sélectionnez **Paramètres système**.

2. Sélectionnez **Surveillance des points de terminaison Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false":::.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Capture d’écran illustrant la sélection de Surveillance des points de terminaison Windows.":::

3. Dans le volet **Planifier l’analyse**, configurez les options comme suit :

      - **Par intervalles fixes (en heures)** : planifiez l’analyse en respectant des intervalles en heures.

      - **Par heures précises** : planifiez l’analyse en respectant des heures précises et sélectionnez **Enregistrer l’analyse**.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="Capture d’écran montrant le bouton Enregistrer l’analyse.":::

4. Pour définir la plage d’analyse, sélectionnez **Définir des plages d’analyse**.

5. Définissez la plage d’adresses IP et ajoutez un utilisateur et votre mot de passe.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="Capture d’écran qui illustre l’ajout d’un nouvel utilisateur et d’un mot de passe.":::

6. Pour exclure une plage d’adresses IP d’une analyse, sélectionnez **Désactiver** à côté de la plage.

7. Pour supprimer une plage, sélectionnez :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: à côté de la plage.

8. Sélectionnez **Enregistrer**. La boîte de dialogue **Modifier la configuration des plages d’analyse** se ferme et le nombre de plages s’affiche dans le volet **Plages d’analyse**.

Pour effectuer une analyse manuelle :

1. Dans le menu latéral, sélectionnez **Paramètres système**.

2. Sélectionnez **Surveillance des points de terminaison Windows** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false":::.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Capture de l’écran de configuration de la surveillance des points de terminaison Windows.":::

3. Dans le volet **Actions**, sélectionnez **Démarrer la sauvegarde**. Une barre d’état apparaît dans le volet **Actions** et affiche la progression du processus d’analyse.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="Capture d’écran du bouton Enregistrer l’analyse.":::

Pour afficher les résultats de l’analyse :

1. Une fois l’analyse terminée, dans le volet **Actions**, sélectionnez **Afficher les résultats de l’analyse**. Le fichier CSV contenant les résultats de l’analyse est téléchargé sur votre ordinateur.

## <a name="see-also"></a>Voir aussi

[Examiner les détections de capteur dans un inventaire des appareils](how-to-investigate-sensor-detections-in-a-device-inventory.md)
[Examiner les détections de capteur dans la carte des appareils](how-to-work-with-the-sensor-device-map.md)
