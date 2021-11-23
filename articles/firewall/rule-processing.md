---
title: Logique de traitement des règles du service Pare-feu Azure
description: Le service Pare-feu Azure comporte des règles de traduction d’adresses réseau (NAT), des règles de réseau et des règles d’application. Les règles sont traitées selon leur type.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/09/2021
ms.author: victorh
ms.openlocfilehash: ec914df62c98ea7948c198949b34e37aa3c485d3
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137347"
---
# <a name="configure-azure-firewall-rules"></a>Configurer des règles de pare-feu Azure
Vous pouvez configurer des règles NAT, des règles de réseau et des règles d’application sur le Pare-feu Azure à l’aide de règles classiques ou d’une stratégie de pare-feu. Le Pare-feu Azure refuse tout le trafic par défaut, jusqu’à ce que les règles soient configurées manuellement pour autoriser le trafic.

## <a name="rule-processing-using-classic-rules"></a>Traitement des règles à l’aide des règles classiques

Les collections de règles sont traitées en fonction du type de règle par ordre de priorité, des nombres inférieurs aux nombres supérieurs, compris entre 100 et 65 000. Un nom de règle de collection peut contenir uniquement des lettres, des chiffres, des traits de soulignement, des points ou des traits d’union. Il doit commencer par une lettre ou un chiffre et se terminer par une lettre, un chiffre ou un trait de soulignement. La longueur maximale du nom est limitée à 80 caractères.

Le mieux est de commencer par espacer les numéros de priorité de la collection de règles par incréments de 100 (100, 200, 300, etc.). Vous avez ainsi de l’espace pour ajouter d’autres collections de règles si nécessaire.

## <a name="rule-processing-using-firewall-policy"></a>Traitement des règles à l’aide d’une stratégie

Dans une stratégie de pare-feu, les règles sont organisées dans des collections de règles et des groupes de collections de règles. Les groupes de collections de règles contiennent de zéro à plusieurs collections de règles. Les collections de règles sont organisées en types : NAT, Réseau ou Applications. Un groupe de règles peut contenir plusieurs types de collections de règles. Une collection de règles peut contenir entre zéro et plusieurs règles. Les règles d’une collection de règles doivent être du même type (NAT, Réseau ou Application).    

Les règles sont traitées en fonction de la priorité du groupe de collections de règles et de la priorité de la collection des règles. La priorité est un nombre compris entre 100 (priorité la plus élevée) et 65 000 (priorité la plus basse). Les groupes de collections de règles dont la priorité est la plus élevée sont traités en premier. Au sein d’un groupe de collections de règles, les collections de règles avec la priorité la plus élevée (le plus petit nombre) sont traitées en premier.  

Si une stratégie de pare-feu est héritée d’une stratégie parent, les groupes de collections de règles dans la stratégie parente sont toujours prioritaires, quelle que soit la priorité d’une stratégie enfant.  

> [!NOTE]
> Les règles de type Application sont toujours traitées après les règles de type Réseau, qui sont traitées après les règles de type DNAT, quel que soient l’héritage de stratégie et la priorité du groupe de collections de règles par rapport aux collections de règles.

Voici un exemple de stratégie :


|Name  |Type  |Priority  |Règles  |Hérité de
|---------|---------|---------|---------|-------|
|BaseRCG1      |Groupe de collections de règles           |200         |8         |Stratégie parente|
|DNATRc1     |Collection de règles DNAT         |  600       |   7      |Stratégie parente|
|NetworkRc1     |Regroupement de règles de réseau  | 800        |    1     |Stratégie parente|
|BaseRCG2  |Groupe de collections de règles         |300         | 3        |Stratégie parente|
|AppRCG2     |Regroupement de règles d’application | 1200        |2         |Stratégie parente
|NetworkRC2     |Regroupement de règles de réseau         |1 300         |    1     |Stratégie parente|
|ChildRCG1  | Groupe de collections de règles        | 300        |5         |-|
|ChAppRC1     |Regroupement de règles d’application         |  700       | 3        |-|
|ChNetRC1       |   Regroupement de règles de réseau      |    900     |    2     |-|
|ChildRCG2      |Groupe de collections de règles         | 650        |    9     |-|
|ChNetRC2      |Regroupement de règles de réseau         |    1100     |  2       |-|
|ChAppRC2      |     Regroupement de règles d’application    |2000         |7         |-|
|ChDNATRC3     | Collection de règles DNAT        | 3000        |  2       |-|

Voici l’ordre de traitement de la règle : DNATRC1, DNATRC3, ChDNATRC3, NetworkRC1, NetworkRC2, ChNetRC1, ChNetRC2, AppRC2, ChAppRC1, ChAppRC2.

Pour plus d’informations sur les ensembles de règles de stratégie de pare-feu, consultez [Ensembles de règles de stratégie de pare-feu Azure](policy-rule-sets.md).

### <a name="threat-intelligence"></a>Informations sur les menaces

Si vous activez le filtrage basé sur le renseignement sur les menaces, ces règles ont la priorité la plus élevée et sont donc toujours traitées en premier. Le filtrage basé sur le renseignement sur les menaces peut refuser le trafic avant que les règles configurées ne soient traitées. Pour plus d’informations, voir [Fonctionnalité de filtrage basé sur la Threat Intelligence du Pare-feu Azure](threat-intel.md).

### <a name="idps"></a>IDPS

Lorsque IDPS est configuré en mode *Alerte*, le moteur IDPS fonctionne en parallèle à la logique de traitement de la règle et génère des alertes sur les signatures correspondantes pour les flux entrants et sortants.Lors d’une correspondance de signature IDPS, une alerte est enregistrée dans les journaux d’activité du pare-feu. Toutefois, étant donné que le moteur IDPS fonctionne en parallèle avec le moteur de traitement des règles, le trafic qui est refusé/autorisé par les règles d’application/réseau peut toujours générer une autre entrée de journal. 

Lorsque IDPS est configuré en mode *Alert and Deny*  (« Alerte et refus »), le moteur IDPS est inclus et activé après le moteur de traitement des règles. Les deux moteurs génèrent donc des alertes et peuvent bloquer les flux correspondants.  

Les annulations de session effectuées par IDPS bloquent le flux en mode silencieux. Par conséquent, aucun RST n’est envoyé au niveau TCP.Étant donné que IDPS inspecte le trafic toujours après que la règle Réseau/Application a été mise en correspondance (Autorisation/Refus) et marquée dans les journaux d’activité, un autre message *Annuler* peut être journalisé, où IDPS décide de refuser la session en raison d’une correspondance de signature. 

Lorsque l’inspection TLS est activée, les trafics chiffrés et non chiffrés sont inspectés.  

## <a name="outbound-connectivity"></a>Connectivité sortante

### <a name="network-rules-and-applications-rules"></a>Règles de réseau et règles d’application

Si vous configurez des règles de réseau et des règles d'application, les règles de réseau sont appliquées par ordre de priorité avant les règles d'application. Ce processus prend fin dès qu’une règle est exécutée. Ainsi, si une correspondance est trouvée dans une règle de réseau, aucune autre règle n'est traitée. S’il est configuré, IDPS est effectué sur tout le trafic parcouru. En cas de correspondance de signature, IDPS peut alerter ou/et bloquer le trafic suspect.  

En l’absence de correspondance avec les règles de réseau, si le protocole est HTTP, HTTPS ou MSSQL, le paquet est évalué par les règles de type Application, par ordre de priorité.  

Pour HTTP, le Pare-feu Azure recherche une correspondance de règle de type Application en fonction de l’en-tête de l’hôte. Pour HTTPs, le Pare-feu Azure recherche une correspondance de règle de type Application uniquement en fonction de l’indication du nom du serveur.  

Dans les cas de HTTP et de HTTPS inspecté par TLS, le pare-feu ignore l’adresse IP de destination du paquet et utilise l’adresse IP résolue par DNS à partir de l’en-tête de l’hôte. Le pare-feu s’attend à recevoir le numéro de port dans l’en-tête de l’hôte. Dans le cas contraire, il part du principe que le port a la valeur standard 80. En cas d’incompatibilité de port entre le port TCP réel et le port dans l’en-tête de l’hôte, le trafic est abandonné.La résolution DNS est effectuée par Azure DNS ou par un DNS personnalisé s’il est configuré sur le pare-feu.  

> [!NOTE]
> Les protocoles HTTP et HTTPs (avec inspection TLS) sont toujours remplis par le Pare-feu Azure avec l’en-tête XFF (X-Forwarded-For) égal à l’adresse IP source d’origine.  

Lorsqu’une règle de type Application contient l’inspection TLS, le moteur de règles du pare-feu traite l’indication du nom du serveur, l’en-tête de l’hôte et également l’URL correspondant à la règle. 

Si aucune correspondance n’est trouvée dans les règles de type Application, le paquet est évalué par rapport à la collection de règles d’infrastructure. S’il n’existe toujours pas de correspondance, le paquet est refusé par défaut. 

> [!NOTE]
> Les règles de type Réseau peuvent être configurées pour le protocole IP  *TCP*,  *UDP*,  *ICMP* ou  *N’importe lequel* . Par « N’importe lequel », on entend tous les protocoles IP tels que définis dans le document sur les numéros de protocole IANA (Internet Assigned Numbers Authority). Si un port de destination est configuré de manière explicite, la règle est traduite en règle TCP+UDP. Avant le 9 novembre 2020,  *N’importe lequel* signifiait TCP, UDP ou ICMP. Par conséquent, vous avez peut-être configuré une règle avant cette date avec **Protocole = N’importe lequel** et les **ports de destination = ' * '** . Si vous n’avez pas l’intention d’autoriser n’importe quel protocole IP comme actuellement défini, modifiez la règle pour configurer explicitement le ou les protocoles souhaités (TCP, UDP ou ICMP). 

## <a name="inbound-connectivity"></a>Connectivité entrante

### <a name="dnat-rules-and-network-rules"></a>Règles de type DNAT et règles de type Réseau

Vous pouvez activer la connectivité Internet entrante en configurant le mode DNAT (Destination Network Address Translation), comme décrit dans l’article [Tutoriel : Filtrer le trafic entrant avec pare-feu Azure DNAT via le portail Azure](tutorial-firewall-dnat.md). Les règles NAT sont appliquées en priorité par rapport aux règles de réseau. Si une correspondance est trouvée, une règle de réseau correspondante implicite autorisant le trafic traduit est ajoutée. Par souci de sécurité, l’approche recommandée consiste à ajouter une source Internet spécifique pour autoriser l’accès de DNAT au réseau et éviter d’utiliser des caractères génériques.

Aucune règle d’application n’est appliquée pour les connexions entrantes. Si vous souhaitez filtrer le trafic HTTP/S entrant, vous devez donc utiliser Web Application Firewall (WAF). Pour plus d’informations, consultez [Présentation du pare-feu d’applications web](../web-application-firewall/overview.md)

## <a name="examples"></a>Exemples

Les exemples suivants montrent les résultats de certaines de ces combinaisons de règles.

### <a name="example-1"></a>Exemple 1

La connexion à google.com est autorisée en raison d'une règle de réseau correspondante.

**Règle de réseau**

- Action : Allow


|name  |Protocol  |Type de source  |Source  |Type de destination  |Adresse de destination  |Ports de destination|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-web     |TCP|Adresse IP|*|Adresse IP|*|80,443

**Règle d’application**

- Action : Deny

|name  |Type de source  |Source  |Protocole:Port|Noms de domaine complets cibles|
|---------|---------|---------|---------|----------|----------|
|Deny-google     |Adresse IP|*|http:80,https:443|google.com

**Résultat**

La connexion à google.com est autorisée, car le paquet correspond à la règle de réseau *Allow-web*. Le traitement des règles s’arrête à ce stade.

### <a name="example-2"></a>Exemple 2

Le trafic SSH est refusé, car une collection de règles de réseau *Deny* de priorité supérieure le bloque.

**Collection de règles de réseau 1**

- Nom : Allow-collection
- Priorité : 200
- Action : Allow

|name  |Protocol  |Type de source  |Source  |Type de destination  |Adresse de destination  |Ports de destination|
|---------|---------|---------|---------|----------|----------|--------|
|Allow-SSH     |TCP|Adresse IP|*|Adresse IP|*|22

**Collection de règles de réseau 2**

- Nom : Deny-collection
- Priorité : 100
- Action : Deny

|name  |Protocol  |Type de source  |Source  |Type de destination  |Adresse de destination  |Ports de destination|
|---------|---------|---------|---------|----------|----------|--------|
|Deny-SSH     |TCP|Adresse IP|*|Adresse IP|*|22

**Résultat**

Les connexions SSH sont refusées, car une collection de règles de réseau de priorité plus élevée les bloque. Le traitement des règles s’arrête à ce stade.

## <a name="rule-changes"></a>Modification de la règle

Si vous modifiez une règle de façon à refuser du trafic jusqu’ici autorisé, toutes les sessions existantes concernées sont supprimées.

## <a name="3-way-handshake-behavior"></a>Comportement de la négociation à 3 voies

En tant que service avec état, le pare-feu Azure effectue une négociation TCP à 3 voies pour le trafic autorisé, d’une source vers la destination.Par exemple, VNet-A vers VNet-B.

La création d’une règle d’autorisation à partir d’un réseau virtuel-A vers un réseau virtuel-B ne signifie pas que les nouvelles connexions établies entre VNet-B et VNet-A sont autorisées.

Par conséquent, il n’est pas nécessaire de créer une règle de refus explicite de VNet-B vers VNet-A. Si vous créez cette règle de refus, vous interrompez la négociation à trois voies de la règle d’autorisation initiale de VNet-A vers VNet-B. 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [déployer et configurer un Pare-feu Azure](tutorial-firewall-deploy-portal.md).
