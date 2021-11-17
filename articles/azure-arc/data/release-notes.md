---
title: Services de données avec Azure Arc – Notes de publication
description: Dernières notes de publication
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: ac5b33de1a95b413c7eba92d2bdeaa8cc05b3a05
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059561"
---
# <a name="release-notes---azure-arc-enabled-data-services"></a>Notes de publication – Services de données activés par Azure Arc

Cet article met en évidence les capacités, fonctionnalités et améliorations récemment mises en production ou améliorées pour les services de données avec Azure Arc.

## <a name="november-2021"></a>Novembre 2021

Cette version a été publiée le 3 novembre 2021

#### <a name="tools"></a>Outils

##### <a name="azure-data-studio"></a>Azure Data Studio

Installez ou mettez à jour vers la version la plus récente de [l’Extension Azure Arc pour Azure Data Studio](/sql/azure-data-studio/extensions/azure-arc-extension).

##### <a name="azure-az-cli"></a>Interface Azure (`az`) CLI

Installez ou mettez à jour l’extension `arcdata` pour l’interface CLI `az` afin de prendre en charge le déploiement en mode de connexion directe.

Les commandes `sql` suivantes prennent désormais en charge le mode de connexion directe :

   ```console
   az arcdata dc create
   az arcdata dc delete
   az arcdata sql mi-arc create
   az arcdata sql mi-arc delete
   ```
 
#### <a name="data-controller"></a>Contrôleur de données

- Le mode de connexion directe est en disponibilité générale
- Les extensions de contrôleur de données Azure Arc en mode de connexion directe sur des clusters Kubernetes avec Azure Arc utilisent désormais des identités managées générées par le système au lieu du nom de principal du service. L’identité managée est automatiquement créée lors de la création d’une nouvelle extension de contrôleur de données Azure Arc. Vous devez toujours accorder les autorisations appropriées pour charger l’utilisation et les métriques.
- Le chargement des métriques tire profit de l’identité managée générée par le système avec un contrôleur de données Azure Arc en mode de connexion directe. 
- Créez un contrôleur de données Azure Arc en mode de connexion directe à partir de l’interface Azure CLI (`az`).
- Charger automatiquement les métriques sur Azure Monitor
- Charger automatiquement les journaux dans Azure Log Analytics
- Activez ou désactivez le chargement automatique des métriques et/ou des journaux dans Azure après le déploiement du contrôleur de données Azure Arc.
- Mise à niveau à partir de la version de juillet 2021 en place (uniquement pour les services en disponibilité générale, comme le contrôleur de données Azure Arc et SQL Managed Instance à usage général) à l’aide de l’interface Azure CLI.
- Définissez les noms d’utilisateur et les mots de passe des tableaux de bord des métriques et des journaux au moment du déploiement du contrôleur de domaine à l’aide des variables du nouvel environnement :

   ```console
   AZDATA_LOGSUI_USERNAME
   AZDATA_LOGSUI_PASSWORD
   AZDATA_METRICSUI_USERNAME
   AZDATA_METRICSUI_PASSWORD
   ```
- Nouvelle commande : `az arcdata dc list-upgrades` affiche la liste des mises à niveau disponibles à partir du contrôleur de données actuellement déployé.


Vous pouvez continuer à utiliser les variables d’environnement `AZDATA_USERNAME` et `AZDATA_PASSWORD` comme précédemment. Si vous spécifiez uniquement les variables `AZDATA_USERNAME` et `AZDATA_PASSWORD`, le déploiement les utilise pour les tableaux de bord des métriques et des journaux.

##### <a name="region-availability"></a>Disponibilité des régions

Cette version introduit la disponibilité en mode connecté directement dans les régions Azure suivantes :

- Centre-Nord des États-Unis
- USA Ouest
- USA Ouest 3

Pour obtenir la liste complète, consultez [Régions prises en charge](overview.md#supported-regions).

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance avec Azure Arc

- Mettre à niveau des instances SQL Managed Instance avec Azure Arc à usage général en place
- Les fichiers binaires de SQL sont mis à jour vers une nouvelle version
- Déploiement en mode connecté direct de SQL Managed Instance avec Azure Arc à l’aide de l’interface Azure CLI
- La limite de restauration dans le temps pour SQL Managed Instance avec Azure Arc est mise à la disposition générale avec cette version. La limite de restauration dans le temps est uniquement prise en charge pour SQL Managed Instance à usage général. La limite de restauration dans le temps pour SQL Managed Instance vital pour l’entreprise est toujours disponible en préversion.
- Nouvelle option `--dry-run` fournie pour la limite de restauration dans le temps
- L’objectif de point de récupération est défini sur 5 minutes par défaut et n’est pas configurable
- La période de rétention de sauvegarde est définie sur 7 jours par défaut. Une nouvelle option permettant de définir la période de rétention sur zéro désactive les sauvegardes automatiques pour les instances de test et de développement qui ne nécessitent pas de sauvegardes
- Résolution du problème concernant l’opération de limite de restauration dans le temps qui ne respecte pas le fuseau horaire configuré 
- Limite de restauration dans le temps à partir de l’interface Azure CLI ou Azure Data Studio
 

### <a name="known-issues"></a>Problèmes connus

#### <a name="data-controller-upgrade"></a>Mise à niveau du contrôleur de données

- La mise à niveau d’un contrôleur de données en mode de connexion directe via l’interface CLI ou le portail n’est pas prise en charge actuellement.
- À l’heure actuelle, vous pouvez uniquement mettre à niveau les services mis à la disposition générale, comme le contrôleur de données Azure Arc et SQL Managed Instance à usage général. Si vous disposez également de PostgreSQL Hyperscale avec Azure Arc et/ou Azure SQL Managed Instance critiques pour l’entreprise, commencez par les supprimer avant de procéder à la mise à niveau.

#### <a name="commands"></a>Commandes

Les commandes suivantes ne prennent pas en charge le mode de connexion directe pour l’instant :

```console
az arcdata dc update
az arcdata sql mi-arc update
```

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale avec Azure Arc

- La sauvegarde et la restauration d’un serveur PostgreSQL Hyperscale avec Azure Arc ne sont pas prises en charge dans la préversion actuelle.

- Il n’est pas possible d’activer et de configurer l’extension `pg_cron` en même temps. Vous devez utiliser deux commandes pour cela. Une commande pour l’activer et une autre pour la configurer. Par exemple :

   1. Activez l’extension :

      ```console
      az postgres arc-server edit -n myservergroup --extensions pg_cron
      ```

   1. Redémarrez le groupe de serveurs.

   1. Configurez l’extension :

      ```console
      az postgres arc-server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   Si vous exécutez la deuxième commande avant la fin du redémarrage, elle échouera. Si c’est le cas, attendez simplement quelques instants de plus et exécutez à nouveau la deuxième commande.

- Le passage d’une valeur non valide au paramètre `--extensions` lors de la modification de la configuration d’un groupe de serveurs pour activer des extensions supplémentaires réinitialise de manière incorrecte la liste des extensions activées à ce qu’elle était au moment de la création du groupe de serveurs et empêche l’utilisateur de créer d’autres extensions. La seule solution de contournement disponible dans ce cas est de supprimer le groupe de serveurs et de le redéployer.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance avec Azure Arc

- Quand un pod est reprovisionné, SQL Managed Instance démarre un nouveau jeu de sauvegardes complètes pour toutes les bases de données.
- Si votre contrôleur de données est en mode de connexion directe, avant de pouvoir provisionner une instance SQL Managed Instance, vous devez d’abord mettre à niveau votre contrôleur de données vers la version la plus récente. La tentative de provisionnement d’une instance SQL Managed Instance avec une image du contrôleur de données version `v1.0.0_2021-07-30` ne fonctionne pas.


##### <a name="other-limitations"></a>Autres limitations

- La réplication des transactions n’est actuellement pas prise en charge.
- La copie des journaux de transaction est actuellement bloquée.
- Seule l’authentification SQL Server est prise en charge.

## <a name="july-2021"></a>Juillet 2021

Cette version a été publiée le 30 juillet 2021.

Cette version annonce la disponibilité générale du [niveau de service Usage général](service-tiers.md) de SQL Managed Instance avec Azure Arc en mode de connexion indirecte.

   > [!NOTE]
   > En outre, cette version fournit les services avec Azure Arc suivants en préversion : 
   > - SQL Managed Instance en mode de connexion directe
   > - [Niveau de service Vital pour l’entreprise](service-tiers.md) de SQL Managed Instance
   > - PostgreSQL Hyperscale

### <a name="breaking-changes"></a>Changements cassants

#### <a name="tools"></a>Outils

Utilisez les outils suivants :
- [Build Insiders d’Azure Data Studio](https://github.com/microsoft/azuredatastudio#try-out-the-latest-insiders-build-from-main)
- [Extension `arcdata` pour Azure (`az`) CLI](install-arcdata-extension.md) 


#### <a name="data-controller"></a>Contrôleur de données

- Le paramètre `az arcdata dc create` nommé `--azure-subscription` a été modifié pour utiliser le paramètre `--subscription` standard.
- Le déploiement sur AKS HCI nécessite une configuration spéciale de la classe de stockage. Voir les détails sous [Configurer le stockage (Azure Stack HCI avec AKS-HCI)](create-data-controller-indirect-cli.md#configure-storage-azure-stack-hci-with-aks-hci).
- Une nouvelle exigence permet d’autoriser les connexions non SSL lors de l’exportation de données. Définissez une variable d’environnement pour supprimer l’invite interactive.

### <a name="whats-new"></a>Nouveautés

#### <a name="data-controller"></a>Contrôleur de données

- Le mode de connexion directe est en préversion. 

- Le mode de connexion directe (préversion) est disponible uniquement dans les régions Azure suivantes pour cette version :

   - USA Centre Nord *
   - USA Centre
   - USA Est
   - USA Est 2
   - USA Ouest *
   - USA Ouest 2
   - USA Ouest 3 *
   - Sud du Royaume-Uni
   - Europe Ouest
   - Europe Nord
   - Australie Est
   - Asie Sud-Est
   - Centre de la Corée
   - France Centre \* récemment ajouté pour novembre 2021.

- Actuellement, d’autres utilisateurs d’authentification de base peuvent être ajoutés à Grafana à l’aide de l’expérience d’administration Grafana. La personnalisation de Grafana par la modification des fichiers Grafana .ini n’est pas prise en charge.

- Actuellement, la modification de la configuration d’ElasticSearch et de Kibana n’est pas prise en charge au-delà de ce qui est disponible par le biais de l’expérience d’administration Kibana. Seule l’authentification de base avec un seul utilisateur est prise en charge.
    
- Les métriques personnalisées dans Portail Azure sont en préversion.

- L’exportation des informations d’utilisation/facturation, des métriques et des journaux à l’aide de la commande `az arcdata dc export` nécessite de contourner la vérification SSL pour l’instant.  Vous êtes invité à contourner la vérification SSL ou vous pouvez définir la variable d’environnement `AZDATA_VERIFY_SSL=no` pour éviter les invites.  Il n’existe actuellement aucun moyen de configurer un certificat SSL pour l’API d’exportation du contrôleur de données.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance avec Azure Arc

- La sauvegarde automatisée et la limite de restauration dans le temps sont en préversion.
- Prend en charge la limite de restauration dans le temps d’une base de données existante dans une instance SQL Managed Instance avec Azure Arc sur une nouvelle base de données au sein de la même instance.
- Si la valeur DateHeure actuelle est indiquée comme limite dans le temps au format UTC, elle se résout en l’heure de restauration valide la plus récente et restaure la base de données donnée jusqu’à la dernière transaction valide.
- Une base de données peut être restaurée à n’importe quelle limite dans le temps où les transactions ont eu lieu.
- Pour définir un objectif de point de récupération spécifique pour SQL Managed Instance avec Azure Arc, modifiez la définition de ressource personnalisée (CRD) de SQL Managed Instance pour définir la propriété `recoveryPointObjectiveInSeconds`. Les valeurs prises en charge sont comprises entre 300 et 600.
- Pour désactiver les sauvegardes automatiques, modifiez la CRD de l’instance SQL et définissez la propriété `recoveryPointObjectiveInSeconds` sur 0.

### <a name="known-issues"></a>Problèmes connus

#### <a name="platform"></a>Plateforme

- Vous pouvez créer un contrôleur de données, une instance SQL Managed Instance ou un groupe de serveurs PostgreSQL Hyperscale sur un cluster en mode de connexion directe à l’aide du portail Azure. Le déploiement en mode de connexion directe n’est pas pris en charge avec d’autres outils des services de données avec Azure Arc. Plus précisément, vous ne pouvez pas déployer un contrôleur de données en mode de connexion directe avec l’un des outils suivants pendant cette version.
   - Azure Data Studio
   - Outils natifs Kubernetes (`kubectl`)
   - Extension `arcdata` pour Azure CLI (`az`)

   [Créer un contrôleur de données Azure Arc en mode de connectivité directe à partir de Portail Azure](create-data-controller-direct-azure-portal.md) explique comment créer le contrôleur de données dans le portail.

- Vous pouvez toujours utiliser `kubectl` pour créer des ressources directement sur un cluster Kubernetes, mais celles-ci n’apparaîtront pas dans le portail Azure si vous utilisez le mode de connexion directe.

- En mode de connexion directe, le chargement de l’utilisation, des métriques et des journaux à l’aide de `az arcdata dc upload` est bloqué en raison de sa conception. Les données d'utilisation sont automatiquement chargées. Le chargement du contrôleur de données créé en mode de connexion indirecte doit continuer à fonctionner.
- Le chargement automatique des données d'utilisation en mode de connexion directe échoue si vous utilisez un proxy via `–proxy-cert <path-t-cert-file>`.
- SQL Managed Instance avec Azure Arc et PostgreSQL Hyperscale avec Azure Arc ne sont pas certifiés GB18030.
- Actuellement, un seul contrôleur de données Azure Arc est pris en charge par cluster Kubernetes.

#### <a name="data-controller"></a>Contrôleur de données

- Quand le contrôleur de données Azure Arc est supprimé du portail Azure, une validation est effectuée pour bloquer la suppression s’il existe des instances SQL Managed Instance avec Azure Arc déployées sur ce contrôleur de données Arc. Actuellement, cette validation est appliquée uniquement lorsque la suppression est effectuée à partir de la page Vue d’ensemble du contrôleur de données Azure Arc. 

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale avec Azure Arc

- À ce stade, PosgreSQL Hyperscale ne peut pas être utilisé sur Kubernetes 1.22 et les versions ultérieures. 
- Les opérations de sauvegarde et de restauration ne fonctionnent plus dans la version du 30 juillet. Il s’agit d’une limite temporaire. Utilisez la version de juin 2021 pour le moment si vous devez effectuer des sauvegardes ou des restaurations. Ceci fera l’objet d’un correctif dans une version future.

- Il n’est pas possible d’activer et de configurer l’extension `pg_cron` en même temps. Vous devez utiliser deux commandes pour cela. Une commande pour l’activer et une autre pour la configurer. Par exemple :

   1. Activez l’extension :

      ```console
      azdata postgres arc-server edit -n myservergroup --extensions pg_cron
      ```

   1. Redémarrez le groupe de serveurs.

   1. Configurez l’extension :

      ```console
      azdata postgres arc-server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   Si vous exécutez la deuxième commande avant la fin du redémarrage, elle échouera. Si c’est le cas, attendez simplement quelques instants de plus et exécutez à nouveau la deuxième commande.

- Le passage d’une valeur non valide au paramètre `--extensions` lors de la modification de la configuration d’un groupe de serveurs pour activer des extensions supplémentaires réinitialise de manière incorrecte la liste des extensions activées à ce qu’elle était au moment de la création du groupe de serveurs et empêche l’utilisateur de créer d’autres extensions. La seule solution de contournement disponible dans ce cas est de supprimer le groupe de serveurs et de le redéployer.

- La limite de restauration dans le temps n’est pas prise en charge pour l’instant sur le stockage NFS.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance avec Azure Arc

##### <a name="cant-see-resources-in-portal"></a>Impossible de voir les ressources dans le portail

- Le portail n’affiche pas les ressources SQL Managed Instance avec Azure Arc créées dans la version de juin. Supprimez les ressources SQL Managed Instance de l’affichage de la liste des groupes de ressources. Vous devrez peut-être d’abord supprimer la ressource d’emplacement personnalisé.

##### <a name="point-in-time-restorepitr-supportability-and-limitations"></a>Prise en charge de la restauration à un instant dans le passé et limites :
    
- Ne prend pas en charge la restauration d’une SQL Managed Instance avec Azure Arc vers une autre SQL Managed Instance avec Azure Arc.  La base de données peut uniquement être restaurée sur la même instance gérée SQL avec Azure Arc où les sauvegardes ont été créées.
- Le changement de nom d’une base de données n’est actuellement pas pris en charge, à des fins de limite de restauration dans le temps.
- Actuellement, il n’existe pas de commande CLI ni d’API pour fournir des informations sur la fenêtre de temps autorisée pour une limite de restauration dans le temps. Vous pouvez fournir une heure dans une fenêtre raisonnable, depuis la création de la base de données, et, si le timestamp est valide, la restauration fonctionne. Si le timestamp n’est pas valide, la fenêtre de temps autorisée sera fournie par l’intermédiaire d’un message d’erreur.
- Aucune prise en charge de la restauration d’une base de données avec TDE.
- Une base de données supprimée ne peut pas être restaurée actuellement.

#####   <a name="automated-backups"></a>Sauvegardes automatisées

- Si vous renommez la base de données, les sauvegardes automatisées de cette base de données s’arrêtent.
- Aucune conservation des données n’est appliquée. Toutes les sauvegardes seront conservées tant qu’il y aura de l’espace disponible. 
- Les bases de données utilisateur avec le mode de récupération SIMPLE ne sont pas sauvegardées.
- Le `model` de base de données système n’est pas sauvegardé afin d’éviter toute interférence avec la création/suppression de la base de données. La base de données est verrouillée lorsque des opérations d’administration sont effectuées. 
- Actuellement, seules les bases de données système `master` et `msdb` sont sauvegardées. Seules les sauvegardes complètes sont effectuées toutes les 12 heures.
- Seules les bases de données utilisateur `ONLINE` sont sauvegardées.
- Objectif de point de récupération (RPO) par défaut : 5 minutes. Ne peut pas être modifié dans la version actuelle.
- Les sauvegardes sont conservées indéfiniment. Pour récupérer de l’espace, supprimez manuellement les sauvegardes.

##### <a name="other-limitations"></a>Autres limitations
- La réplication des transactions n’est actuellement pas prise en charge.
- La copie des journaux de transaction est actuellement bloquée.
- Seule l’authentification SQL Server est prise en charge.

## <a name="june-2021"></a>Juin 2021

Cette préversion a été publiée le 13 juillet 2021.

### <a name="breaking-changes"></a>Changements cassants

#### <a name="new-deployment-templates"></a>Nouveaux modèles de déploiement

- Les modèles de déploiement natifs Kubernetes ont été modifiés pour le contrôleur de données, le programme de démarrage et SQL Managed Instance. Mettez à jour vos modèles .yaml. [Exemples de fichiers yaml](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml)

#### <a name="new-azure-cli-extension-for-data-controller-and-azure-arc-enabled-sql-managed-instance"></a>Nouvelle extension Azure CLI pour le contrôleur de données et SQL Managed Instance avec Azure Arc

Cette version introduit l’extension `arcdata` à Azure CLI. Pour ajouter l’extension, exécutez la commande suivante :

```azurecli
az extension add --name arcdata
```

L’extension prend en charge l’interaction en ligne de commande avec le contrôleur de données, SQL Managed Instance et les ressources PostgreSQL Hyperscale.

Afin de mettre à jour vos scripts pour le contrôleur de données, remplacez `azdata arc dc...` par `az arcdata dc...`.

Afin de mettre à jour vos scripts pour l’instance gérée, remplacez `azdata arc sql mi...` par `az sql mi-arc...`.

Pour PostgreSQL Hyperscale avec Azure Arc, remplacez `azdata arc sql postgres...` par `az postgres arc-server...`.

En plus des paramètres qui ont historiquement existé sur les commandes `azdata`, les mêmes commandes dans l’extension Azure CLI `arcdata` présentent quelques nouveaux paramètres tels que`--k8s-namespace` et `--use-k8s` qui sont maintenant obligatoires. Le paramètre `--use-k8s` sera utilisé pour décider si la commande doit être envoyée à l’API Kubernetes ou à l’API ARM. Pour l’instant, toutes les commandes Azure CLI pour les services de données avec Azure Arc ciblent uniquement l’API Kubernetes.

Certaines formes abrégées des noms de paramètres (par exemple, `-cl` pour `--core-limit`) ont été supprimées ou modifiées. Utilisez les nouveaux noms courts ou le nom long des paramètres.

La commande `azdata arc dc export` n’est plus fonctionnelle. Utilisez `az arcdata dc export` à la place.

#### <a name="required-property-infrastructure"></a>Propriété obligatoire : `infrastructure`

La propriété `infrastructure` est une nouvelle propriété obligatoire lors du déploiement d’un contrôleur de données. Adaptez vos fichiers yaml, vos scripts azdata/az et vos modèles ARM pour tenir compte de la spécification de cette valeur de propriété. Les valeurs autorisées sont `alibaba`, `aws`, `azure`, `gpc`, `onpremises` et `other`.

#### <a name="kibana-login"></a>Connexion à Kibana

Le pack de sécurité OpenDistro a été supprimé. La connexion à Kibana se fait désormais par le biais d’une invite générique avec nom d’utilisateur/mot de passe dans un navigateur. De plus amples informations seront fournies ultérieurement sur la configuration d’options d’authentification/autorisation supplémentaires.

#### <a name="crd-version-bump-to-v1beta1"></a>Passage de la version de la CRD à `v1beta1`

La version de toutes les CRD est passée de `v1alpha1` à `v1beta1` pour cette mise en production. Veillez à supprimer toutes les CRD dans le cadre du processus de désinstallation si vous avez déployé une version des services de données avec Azure Arc antérieure à la version de juin 2021. Les nouvelles CRD déployées avec la version de juin 2021 auront pour version v1beta1.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance avec Azure Arc

Le service de sauvegarde automatique est disponible et activé par défaut. Surveillez de près la disponibilité de l’espace sur le volume de sauvegarde.

### <a name="whats-new"></a>Nouveautés

Cette version introduit les extensions CLI `az` pour les services de données avec Azure Arc. Voir les informations dans [Changement cassant](#breaking-change) ci-dessus.

#### <a name="platform"></a>Plateforme

#### <a name="data-controller"></a>Contrôleur de données

- Expérience utilisateur rationalisée pour le déploiement d’un contrôleur de données en mode de connexion directe à partir du portail Azure. Une fois qu’Azure Arc est activé sur un cluster Kubernetes, vous pouvez déployer le contrôleur de données entièrement à partir du portail grâce à l’Assistant Création d’un contrôleur de données Arc d’un seul mouvement. Ce déploiement crée également l’emplacement personnalisé et l’extension des services de données avec Azure Arc (programme de démarrage). Vous pouvez également précréer l’emplacement personnalisé et/ou l’extension et configurer le déploiement du contrôleur de données pour les utiliser.
- La nouvelle propriété `Infrastructure` est une propriété obligatoire lorsque vous déployez un contrôleur de données Arc. Cette propriété sera requise à des fins de facturation. De plus amples informations seront fournies lors de la disponibilité générale.
- Diverses améliorations de la convivialité de l’expérience utilisateur du contrôleur de données dans le portail Azure, notamment la possibilité de mieux voir l’état de déploiement des ressources qui sont en cours de déploiement sur le cluster Kubernetes.
- Le contrôleur de données charge automatiquement les journaux (facultatif) et maintenant les métriques également vers Azure en mode de connexion directe.
- La pile d’analyse (bases de données et tableaux de bord des métriques et des journaux) a été intégrée dans sa propre définition de ressource personnalisée (CRD), `monitors.arcdata.microsoft.com`. Lorsque cette ressource personnalisée est créée, les pods de la pile d’analyse sont créés. Lorsqu’elle est supprimée, les pods de la pile d’analyse sont supprimés. Lorsque le contrôleur de données est créé, la ressource personnalisée d’analyse est automatiquement créée.
- Nouvelles régions prises en charge pour le mode de connexion directe (préversion) : USA Est 2, USA Ouest 2, USA Centre Sud, Royaume-Uni Sud, France Centre, Asie Sud-Est, Australie Est.
- Le graphique des ressources de l’emplacement personnalisé sur le panneau Vue d’ensemble affiche désormais les ressources des services de données avec Azure Arc qui y sont déployées.
- Des diagnostics et des solutions ont été ajoutés au portail Azure pour le contrôleur de données.
- La nouvelle propriété `Observed Generation` a été ajoutée à toutes les ressources personnalisées relatives à Azure Arc.
- Le service gestionnaire des informations d’identification est maintenant inclus et gère la distribution automatique des certificats à tous les services gérés par le contrôleur de données.

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale avec Azure Arc

- Azure Arc PostgreSQL Hyperscale prend désormais en charge le stockage NFS.
- Les déploiements d’Azure Arc PostgreSQL Hyperscale prennent désormais en charge les stratégies d’attribution des pods Kubernetes aux nœuds avec nodeSelector, nodeAffinity et anti-affinity.
- Vous pouvez désormais configurer les paramètres de calcul (vCore et mémoire) par rôle (Coordinateur ou Worker) lorsque vous déployez un groupe de serveurs PostgreSQL Hyperscale ou après le déploiement à partir d’Azure Data Studio et du portail Azure.
- À partir du portail Azure, vous pouvez désormais afficher la liste des extensions PostgreSQL créées sur votre groupe de serveurs PostgreSQL Hyperscale.
- À partir du portail Azure, vous pouvez supprimer des groupes PostgreSQL Hyperscale avec Azure Arc sur un contrôleur de données qui est directement connecté à Azure.


#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance avec Azure Arc

- Les sauvegardes automatiques sont désormais activées.
- Vous pouvez maintenant restaurer une sauvegarde de base de données en tant que nouvelle base de données sur la même instance SQL en créant une nouvelle ressource personnalisée basée sur la définition de ressource personnalisée (CRD) `sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com`. Pour plus d'informations, reportez-vous à la documentation. Il n’existe pas encore d’expérience d’interface de ligne de commande (`azdata` ou `az`), de portail Azure ou Azure Data Studio pour restaurer une base de données.
- La version des binaires du moteur SQL incluse dans cette mise en production s’aligne sur les binaires les plus récents qui sont déployés à l’échelle mondiale dans Azure SQL Managed Instance (PaaS dans Azure). Cet alignement permet la sauvegarde/restauration dans les deux sens entre un PaaS Azure SQL Managed Instance et SQL Managed Instance avec Azure Arc. Plus de détails sur la compatibilité seront fournis ultérieurement.
- Vous pouvez désormais supprimer des instances gérées SQL avec Azure Arc à partir du portail Azure en mode de connexion directe.
- Vous pouvez maintenant configurer une instance gérée SQL pour qu’elle ait un niveau tarifaire (`GeneralPurpose`, `BusinessCritical`), un type de licence (`LicenseIncluded`, `BasePrice` [utilisé pour la tarification AHB]) et un `developer`. Aucun frais ne sera encouru pour l’utilisation de SQL Managed Instance avec Azure Arc jusqu’à la date de disponibilité générale (annoncée publiquement pour le 30 juillet 2021) et jusqu’à ce que vous passiez à la version Disponibilité générale du service.
- L’extension `arcdata` pour Azure Data Studio dispose désormais de paramètres supplémentaires qui peuvent être configurés pour le déploiement et la modification des instances gérées SQL : activation/désactivation de l’agent, secret de connexion de l’administrateur, annotations, étiquettes, annotations de service, étiquettes de service, paramètres de configuration SSL/TLS, classement, langage et indicateurs de trace.
- Nouvelles commandes dans les tâches `azdata`/de ressource personnalisée pour la configuration des groupes de disponibilité distribués. Ces commandes sont aux premiers stades de la préversion, la documentation sera bientôt disponible.

   > [!NOTE]
   > Ces commandes vont migrer vers l’extension `az arcdata`.

- `azdata arc dc export` est déconseillé. Il est remplacé par `az arcdata dc export` dans l’extension `arcdata` pour Azure CLI (`az`). Elle utilise une approche différente pour exporter les données. Elle ne se connecte plus directement à l’API du contrôleur de données. Elle crée plutôt une tâche d’exportation basée sur la définition de ressource personnalisée (CRD) `exporttasks.tasks.arcdata.microsoft.com`. La ressource personnalisée de la tâche d’exportation qui est créée pilote un flux de travail pour générer un package téléchargeable. Azure CLI attend la fin de l’exécution de cette tâche, puis récupère l’URL sécurisée à partir de l’état de la ressource personnalisée de la tâche pour télécharger le package.
- Prise en charge de l’utilisation des classes de stockage NFS.
- Des diagnostics et des solutions ont été ajoutés au portail Azure pour SQL Managed Instance avec Azure Arc.

## <a name="may-2021"></a>Mai 2021

Cette préversion est publiée le 2 juin 2021.

En tant que fonctionnalité en préversion, la technologie présentée dans cet article est soumise aux [conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="breaking-change"></a>Modification avec rupture

- Les modèles de déploiement natif Kubernetes ont été modifiés. Mettez à jour vos modèles .yml.
    - Modèles mis à jour pour le contrôleur de données, le démarrage et SQL Managed Instance : [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)
    - Modèles mis à jour pour PostgreSQL Hyperscale : [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)

### <a name="whats-new"></a>Nouveautés

#### <a name="platform"></a>Plateforme

- Création et suppression à partir du portail Azure des groupes de serveurs de contrôleur de données, de SQL Managed Instance et de PostgreSQL Hyperscale.
- Validation des actions du portail lors de la suppression de services de données Azure Arc. Par exemple, le portail vous avertit si vous tentez de supprimer le contrôleur de données alors que des instances SQL Managed sont déployées et utilisent le contrôleur de données.
- Création de profils de configuration personnalisés pour prendre en charge des paramètres personnalisés lorsque vous déployez un contrôleur de données avec Azure Arc à l’aide du portail Azure.
- Le cas échéant, chargement automatique de vos journaux dans l’espace de travail Azure log Analytics en mode connecté directement.

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale avec Azure Arc

Cette version présente les fonctionnalités ou capacités suivantes :

- Suppression à partir du portail Azure d’une instance PostgreSQL Hyperscale avec Azure Arc alors que son contrôleur de données était configuré pour le mode de connectivité directe.
- Déploiement de PostgreSQL Hyperscale avec Azure Arc à partir de la page de déploiement d’Azure Database pour PostgreSQL dans le portail Azure. Consultez l’[option de déploiement Sélectionner Azure Database pour PostgreSQL - Microsoft Azure](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer).
- Spécification des classes de stockage et des extensions Postgres lors du déploiement de PostgreSQL Hyperscale avec Azure Arc depuis le portail Azure.
- Réduction du nombre de nœuds Worker dans votre instance PostgreSQL Hyperscale avec Azure Arc. Vous pouvez effectuer cette opération (appelée scale-in par opposition au scale-out que vous effectuez lorsque vous augmentez le nombre de nœuds Worker) depuis la ligne de commande `azdata`.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance avec Azure Arc

- La nouvelle [extension d’Azure CLI](/cli/azure/azure-cli-extensions-overview) pour SQL Managed Instance avec Azure Arc présente les mêmes commandes que `az sql mi-arc <command>`. Toutes les commandes de SQL Managed Instance avec Azure Arc se trouvent à l’emplacement `az sql mi-arc`. Toutes les commandes `azdata` liées à Arc seront dépréciées et déplacées vers Azure CLI dans une prochaine version.

   Pour ajouter l’extension :

   ```azurecli
   az extension add --source https://azurearcdatacli.blob.core.windows.net/cli-extensions/arcdata-0.0.1-py2.py3-none-any.whl -y
   az sql mi-arc --help
   ```

- Déclenchez manuellement un basculement à l’aide de Transact-SQL. Exécutez les deux commandes suivantes dans cet ordre :

   1. Sur la connexion de point de terminaison du réplica principal :

      ```sql
       ALTER AVAILABILITY GROUP current SET (ROLE = SECONDARY);
      ```

   1. Sur la connexion de point de terminaison du réplica secondaire :

      ```sql
      ALTER AVAILABILITY GROUP current SET (ROLE = PRIMARY);
      ```

- La commande Transact-SQL `BACKUP` est bloquée à moins d’utiliser le paramètre `COPY_ONLY`. La fonctionnalité de limite de restauration dans le temps est prise en charge.

## <a name="april-2021"></a>Avril 2021

Cette préversion est publiée le 29 avril 2021.

### <a name="whats-new"></a>Nouveautés

Cette section décrit les nouvelles fonctionnalités introduites ou activées pour cette version.

#### <a name="platform"></a>Plateforme

- Les clusters directement connectés chargent automatiquement les informations de télémétrie sur Azure.

####    <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale avec Azure Arc

- PostgreSQL Hyperscale avec Azure Arc est désormais pris en charge en mode de connexion directe. Vous pouvez maintenant déployer PostgreSQL Hyperscale avec Azure Arc depuis Place de marché Azure dans le portail Azure.
- PostgreSQL Hyperscale avec Azure Arc est fourni avec l’extension Citus 10.0 qui propose le stockage de table en colonnes.
- PostgreSQL Hyperscale avec Azure Arc prend désormais en charge la gestion complète des utilisateurs et des rôles.
- PostgreSQL Hyperscale avec Azure Arc prend désormais en charge des extensions supplémentaires avec `Tdigest` et `pg_partman`.
- PostgreSQL Hyperscale avec Azure Arc prend désormais en charge la configuration des paramètres de mémoire et de vCore par rôle de l’instance PostgreSQL dans le groupe de serveurs.
- PostgreSQL Hyperscale avec Azure Arc prend désormais en charge la configuration du moteur de base de données/des paramètres de serveur par rôle de l’instance PostgreSQL dans le groupe de serveurs.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance avec Azure Arc

- Restauration d’une base de données sur SQL Managed Instance avec trois réplicas, celle-ci sera automatiquement ajoutée au groupe de disponibilité.
- Connexion à un point de terminaison en lecture seule secondaire sur des instances SQL Managed Instance déployées avec trois réplicas. Utilisez `azdata arc sql endpoint list` pour voir le point de terminaison de connexion en lecture seule secondaire.

## <a name="march-2021"></a>Mars 2021

La version initiale de mars 2021 a été publiée le 5 avril 2021, et les dernières étapes de sa publication ont été achevées le 9 avril 2021.

Numéro de version d'Azure Data CLI (`azdata`) : 20.3.2. Vous pouvez installer `azdata` à partir de la section [Installer Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

### <a name="data-controller"></a>Contrôleur de données

- Déployez le contrôleur de données des services de données avec Azure Arc en mode de connexion directe à partir du portail. Commencez par [Déployer le contrôleur de données - Mode de connexion directe - Prérequis](create-data-controller-direct-prerequisites.md).

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale avec Azure Arc

Les deux définitions de ressources personnalisées (CRD) relatives à PostgreSQL ont été regroupées en une. Consultez le tableau suivant.

|Libérer |CRD |
|-----|-----|
|Février 2021 et versions antérieures| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|À compter de mars 2021 | postgresqls.arcdata.microsoft.com

Vous supprimerez les CRD précédentes au fur et à mesure que vous nettoierez les installations antérieures. Consultez [Nettoyage des installations précédentes](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations).

### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance avec Azure Arc

- Vous pouvez désormais créer une instance SQL Managed Instance à partir du portail Azure en mode de connexion directe.

- Vous pouvez maintenant restaurer une base de données sur SQL Managed Instance avec trois réplicas, et celle-ci sera automatiquement ajoutée au groupe de disponibilité.

- Vous pouvez maintenant vous connecter à un point de terminaison en lecture seule secondaire sur des instances SQL Managed Instance déployées avec trois réplicas. Utilisez `azdata arc sql endpoint list` pour voir le point de terminaison de connexion en lecture seule secondaire.

## <a name="february-2021"></a>Février 2021

### <a name="new-capabilities-and-features"></a>Nouvelles capacités et fonctionnalités

Numéro de version d’Azure Data CLI (`azdata`) : 20.3.1. Vous pouvez installer `azdata` à partir de la section [Installer Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

Les mises à jour supplémentaires incluent :

- SQL Managed Instance avec Azure Arc
   - Haute disponibilité avec des groupes de disponibilité Always On

- PostgreSQL Hyperscale Azure Data Studio avec Azure Arc :
   - La page Vue d’ensemble affiche l’état du groupe de serveurs détaillé par nœud
   - Une nouvelle page de propriétés affiche des détails supplémentaires sur le groupe de serveurs
   - Configurer les paramètres du moteur Postgres à partir de la page **Paramètres du nœud**

## <a name="january-2021"></a>Janvier 2021

### <a name="new-capabilities-and-features"></a>Nouvelles capacités et fonctionnalités

Numéro de version d’Azure Data CLI (`azdata`) : 20.3.0. Vous pouvez installer `azdata` à partir de la section [Installer Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

Les mises à jour supplémentaires incluent :
- Portail localisé disponible dans 17 nouvelles langues
- Modifications mineures apportées aux fichiers .yaml Kube natifs
- Nouvelles versions de Grafana et Kibana
- Les problèmes affectant les environnements Python lors de l’utilisation d’azdata dans des notebooks dans Azure Data Studio ont été résolus
- L’extension pg_audit est désormais disponible pour PostgreSQL Hyperscale
- Aucun ID de sauvegarde n’est nécessaire pour effectuer une restauration complète d’une base de données PostgreSQL Hyperscale
- L’état (état d’intégrité) est indiqué pour chacune des instances PostgreSQL qui constituent un groupe de serveurs

   Dans les versions antérieures, l’état était agrégé au niveau du groupe de serveurs et n’était pas répertorié au niveau du nœud PostgreSQL.

- Les déploiements PostgreSQL respectent les paramètres de taille de volume indiqués dans les commandes create
- Les paramètres de version du moteur sont désormais respectés lors de la modification d’un groupe de serveurs
- La convention d’affectation de noms des pods pour PostgreSQL Hyperscale avec Azure Arc a changé

    Il se présente désormais sous la forme : `ServergroupName{c, w}-n`. Par exemple, un groupe de serveurs avec trois nœuds, un nœud coordinateur et deux nœuds Worker sont représentés comme suit :
   - `Postgres01c-0` (nœud coordinateur)
   - `Postgres01w-0` (nœud Worker)
   - `Postgres01w-1` (nœud Worker)

## <a name="december-2020"></a>Décembre 2020

### <a name="new-capabilities--features"></a>Nouvelles fonctionnalités

Numéro de version d’Azure Data CLI (`azdata`) : 20.2.5. Vous pouvez installer `azdata` à partir de la section [Installer Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

Affichez les points de terminaison pour SQL Managed Instance et l’hyperscale PostgreSQL à l’aide d’Azure Data CLI (`azdata`) avec des commandes `azdata arc sql endpoint list` et `azdata arc postgres endpoint list` .

Modifiez les demandes et les limites de ressources SQL Managed Instance (processeur et mémoire) à l’aide d’Azure Data Studio.

PostgreSQL Hyperscale avec Azure Arc prend désormais en charge la limite de restauration dans le temps en plus de la restauration de sauvegarde complète pour les versions 11 et 12 de PostgreSQL. La fonction de restauration dans le temps vous permet d’indiquer une date et une heure spécifiques de restauration.

La convention d’affectation de noms des pods pour PostgreSQL Hyperscale avec Azure Arc a changé. Il se présente désormais sous la forme : ServergroupName{r, s}-_n_. Par exemple, un groupe de serveurs avec trois nœuds, un nœud coordinateur et deux nœuds Worker sont représentés comme suit :
- `postgres02r-0` (nœud coordinateur)
- `postgres02s-0` (nœud Worker)
- `postgres02s-1` (nœud Worker)

### <a name="breaking-change"></a>Modification avec rupture

#### <a name="new-resource-provider"></a>Nouveau fournisseur de ressources

Cette version inaugure un [fournisseur de ressources](../../azure-resource-manager/management/azure-services-resource-providers.md) mis à jour appelé `Microsoft.AzureArcData`. Avant de pouvoir utiliser cette fonctionnalité, vous devez inscrire ce fournisseur de ressources.

Pour inscrire le fournisseur de ressources :

1. Dans le portail Azure, sélectionnez **Abonnements**
2. Choisir votre abonnement
3. Sous **Paramètres**, sélectionnez **Fournisseurs de ressources**
4. Recherchez `Microsoft.AzureArcData` et sélectionnez **Inscrire**

Vous pouvez consulter les étapes détaillées dans [Types de ressources et fournisseurs Azure](../../azure-resource-manager/management/resource-providers-and-types.md). Cette modification supprime également toutes les ressources Azure existantes que vous avez téléchargées sur le Portail Azure. Pour pouvoir utiliser le fournisseur de ressources, vous devez mettre à jour le contrôleur de données et utiliser la dernière `azdata` CLI.

### <a name="platform-release-notes"></a>Notes de publication de la plateforme

#### <a name="direct-connectivity-mode"></a>Mode de connectivité directe

Cette version introduit le mode de connectivité directe. Le mode de connectivité directe permet au contrôleur de données de télécharger automatiquement les informations d’utilisation dans Azure. Dans le cadre du chargement de l’utilisation, la ressource de contrôleur de données Arc est créée automatiquement dans le portail, si elle n’est pas déjà créée via le chargement de `azdata` .

Vous pouvez spécifier une connectivité directe quand vous créez le contrôleur de données. L’exemple suivant crée un contrôleur de données avec `az arcdata dc create` nommé `arc` à l’aide du mode de connectivité directe (`connectivity-mode direct`). Avant d’exécuter l’exemple, remplacez `<subscription id>` par votre ID d’abonnement.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

## <a name="october-2020"></a>Octobre 2020

Numéro de version d’Azure Data CLI (`azdata`) : 20.2.3. Vous pouvez installer `azdata` à partir de la section [Installer Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata).

### <a name="breaking-changes"></a>Changements cassants

Cette version introduit les changements cassants suivants :

* Dans la définition de ressource personnalisée PostgreSQL, le terme `shards` est renommé en `workers`. Ce terme (`workers`) correspond au nom du paramètre de ligne de commande.

* `azdata arc postgres server delete` demande une confirmation avant de supprimer une instance postgres. Utilisez `--force` pour ignorer l’invite.

### <a name="additional-changes"></a>Modifications supplémentaires

* Un nouveau paramètre facultatif a été ajouté à `azdata arc postgres server create` appelé `--volume-claim mounts`. La valeur est une liste de montages de revendications de volume séparés par des virgules. Un montage de revendication de volume est une paire de type de volume et de nom PVC. Le seul type de volume actuellement pris en charge est `backup`. Dans PostgreSQL, lorsque le type de volume est `backup`, le PVC est monté sur `/mnt/db-backups`. Cela permet de partager des sauvegardes entre instances PostgresSQL afin que la sauvegarde d’une instance PostgresSQL puisse être restaurée dans une autre instance.

* Nouveaux noms courts pour les définitions de ressources personnalisées PostgresSQL :
  * `pg11`
  * `pg12`
* Le chargement de la télémétrie fournit à l’utilisateur l’un des éléments suivants :
   * Nombre de points chargés sur Azure ou
   * Si aucune donnée n’a été chargée sur Azure, une invite pour essayer à nouveau.
* `az arcdata dc debug copy-logs` lit maintenant aussi dans le dossier `/var/opt/controller/log` et collecte les journaux du moteur PostgreSQL sur Linux.
*   Affichage d’un indicateur de travail lors de la création et de la restauration de la sauvegarde avec PostgreSQL Hyperscale.
* `azdata arc postrgres backup list` comprend désormais des informations sur la taille de la sauvegarde.
* La propriété Nom de l’administrateur SQL Managed Instance a été ajoutée à la colonne de droite du panneau Vue d’ensemble dans le portail Azure.
* Azure Data Studio prend en charge la configuration du nombre de nœuds Worker, de vCore et des paramètres de mémoire pour PostgreSQL Hyperscale.
* La préversion prend en charge la sauvegarde/restauration pour les versions 11 et 12 de Postgres.

## <a name="september-2020"></a>Septembre 2020

Les services de données avec Azure Arc sont mis en production pour la préversion publique. Les services de données avec Azure Arc permettent de gérer les services de données en tout lieu.

- Instance managée SQL
- PostgreSQL Hyperscale

Pour obtenir des instructions, consultez [Présentation des services de données avec Azure Arc](overview.md).

## <a name="next-steps"></a>Étapes suivantes

> **Vous voulez juste essayer ?**
> Démarrez rapidement avec [Démarrage rapide d’Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) sur AKS, AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou sur une machine virtuelle Azure.

- [Installer les outils clients](install-client-tools.md)
- [Créer le contrôleur de données Azure Arc](create-data-controller.md) (nécessite l'installation préalable des outils clients)
- [Créer une instance Azure SQL Managed Instance sur Azure Arc](create-sql-managed-instance.md) (nécessite la création préalable d’un contrôleur de données Azure Arc)
- [Créer un groupe de serveurs Azure Database pour PostgreSQL Hyperscale sur Azure Arc](create-postgresql-hyperscale-server-group.md) (nécessite la création préalable d’un contrôleur de données Azure Arc)
- [Fournisseurs de ressources pour les services Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
