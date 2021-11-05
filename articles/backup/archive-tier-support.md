---
title: Vue d’ensemble de la prise en charge du niveau Archive
description: En savoir plus sur la prise en charge du niveau Archive pour le service Sauvegarde Azure.
ms.topic: overview
ms.date: 10/23/2021
ms.custom: references_regions
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 3c28d99c066bf71ea3970ce8a01eb68989e11123
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080894"
---
# <a name="about-archive-tier-support"></a>À propos de la prise en charge du niveau Archive

Les clients s’appuient sur le service Sauvegarde Azure pour stocker leurs données de sauvegarde, notamment pour une conservation à long terme (LTR), avec des exigences de conservation définies par les règles de conformité de l’organisation. Dans la plupart des cas, les données de sauvegarde plus anciennes sont rarement utilisées, et stockées uniquement à des fins de conformité.

Le service Sauvegarde Azure prend en charge la sauvegarde des points de conservation à long terme dans le niveau Archive, en plus des captures instantanées et du niveau Standard.

## <a name="supported-workloads"></a>Charges de travail prises en charge

Le niveau Archive prend en charge les charges de travail suivantes :

| Charges de travail | Operations |
| --- | --- |
| Machines virtuelles Azure | <ul><li>Uniquement les points de récupération mensuels et annuels. Les points de récupération quotidiens et hebdomadaires ne sont pas pris en charge.  </li><li>Âge >= 3 mois au niveau Coffre-Standard. </li><li>Conservation restante >= 6 mois. </li><li>Aucune dépendance quotidienne ou hebdomadaire active. </li></ul> |
| SQL Server dans des machines virtuelles Azure/SAP HANA dans des machines virtuelles Azure | <ul><li>Uniquement les points de récupération complets. Les journaux et les différentiels ne sont pas pris en charge. </li><li>Âge >= 45 jours au niveau Coffre-Standard. </li><li>Conservation restante >= 6 mois. </li><li>Aucune dépendance </li></ul> |

>[!Note]
>- La prise en charge du niveau de stockage archive pour les serveurs SQL dans les machines virtuelles Azure et SAP HANA dans les machines virtuelles Azure est à présent généralement disponible dans plusieurs régions. Pour obtenir la liste détaillée des régions prises en charge, consultez la [matrice de prise en charge](#support-matrix).
>- La prise en charge du niveau archive pour les machines virtuelles Azure est également en préversion publique limitée. Pour vous inscrire à la préversion publique limitée, utilisez [ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR463S33c54tEiJLEM6Enqb9UNU5CVTlLVFlGUkNXWVlMNlRPM1lJWUxLRy4u).

## <a name="supported-clients"></a>Clients pris en charge

Le niveau Archive prend en charge les clients suivants :

- [PowerShell](/azure/backup/use-archive-tier-support?pivots=client-powershelltier)
- [INTERFACE DE LIGNE DE COMMANDE](/azure/backup/use-archive-tier-support?pivots=client-clitier)
- [Azure portal](/azure/backup/use-archive-tier-support?pivots=client-portaltier)

## <a name="how-azure-backup-moves-recovery-points-to-the-vault-archive-tier"></a>Comment Sauvegarde Azure déplace les points de récupération vers le niveau Coffre-Archive ?

> [!VIDEO https://www.youtube.com/embed/nQnH5mpiz60?start=416]

## <a name="archive-recommendations-only-for-azure-virtual-machines"></a>Recommandations sur les archives (uniquement pour les machines virtuelles Azure)

Les points de récupération pour les machines virtuelles Azure sont incrémentiels. Lorsque vous déplacez des points de récupération vers le niveau Archive, ils sont convertis en points de récupération complets (pour s’assurer que tous les points de récupération du niveau Archive sont indépendants et isolés les uns des autres). Ainsi, le stockage de sauvegarde global (coffre-standard + coffre-archive) peut augmenter.

La quantité d’augmentation du stockage dépend du modèle d’attrition des machines virtuelles.

- Plus l’attrition des machines virtuelles est élevée, plus le stockage de sauvegarde global est petit lorsqu’un point de récupération est déplacé vers le niveau archive.
- Si l’attrition de la machine virtuelle est faible, le passage au niveau archive peut entraîner une augmentation du stockage de sauvegarde, ce qui peut compenser la différence de prix entre le niveau Coffre-Standard et le niveau Coffre-Archive. Par conséquent, cela peut augmenter le coût global.

Pour résoudre cela, Sauvegarde Azure fournit un ensemble de recommandations. L’ensemble de recommandations renvoie une liste de points de récupération qui, s’ils sont déplacés ensemble vers le niveau Archive, garantissent des économies.

>[!Note]
>Les économies dépendent de divers facteurs et peuvent différer selon l’instance.

## <a name="modify-protection"></a>Modifier la protection

Sauvegarde Azure offre deux moyens de modifier la protection d’une source de données :

- Modifier une stratégie existante
- Protection de la source de source avec une nouvelle stratégie

Dans les deux scénarios, la nouvelle stratégie est appliquée à tous les points de récupération plus anciens du niveau Standard, qui sont dans le niveau Standard ou le niveau Archive. Ainsi, des points de récupération plus anciens peuvent être supprimés en cas de changement de stratégie.

Lorsque vous déplacez les points de récupération vers l’archive, ils sont soumis à une période de suppression précoce de 180 jours. Les frais sont calculés au prorata. Si un point de récupération qui n’est pas resté au niveau Archive pendant 180 jours est supprimé, cela occasionne un coût équivalent à 180 moins le nombre de jours qu’il est resté au niveau Standard.

Si vous supprimez les points de récupération qui ne sont pas restés au niveau Archive pendant au minimum 180 jours, cela occasionne un coût de suppression précoce.

## <a name="stop-protection-and-delete-data"></a>Arrêter la protection et supprimer les données

L’option Arrêter la protection et supprimer les données supprime tous les points de récupération. Pour les points de récupération qui ne sont pas restés pendant une durée de 180 jours au niveau Archive, leur suppression occasionne un coût de suppression précoce.

## <a name="archive-tier-pricing"></a>Tarification du niveau Archive

Vous pouvez consulter la tarification du niveau Archive dans notre [page de tarification](azure-backup-pricing.md).

## <a name="support-matrix"></a>Matrice de prise en charge

| Charges de travail | Préversion | Mise à la disposition générale |
| --- | --- | --- |
| SQL Server dans des machines virtuelles Azure/SAP HANA dans des machines virtuelles Azure | None | Australie Est, Inde Centre, Europe Nord, Asie Sud-Est, Asie Est, Australie Sud-Est, Canada Centre, Brésil Sud, Canada Est, France Centre, France Sud, Japon Est, Japon Ouest, Corée Centre, Corée Sud, Inde Sud, Royaume-Uni Ouest, Royaume-Uni Sud, USA Centre, USA Est 2, USA Ouest, USA Ouest 2, USA Centre-Ouest, USA Est, USA Centre Sud, USA Centre Nord, Europe Ouest, US Gov Virginie, US Gov Texas, US Gov Arizona. |
| Machines virtuelles Azure | USA Est, USA Est 2, USA Centre, USA Centre Sud, USA Ouest, USA Ouest 2, USA Centre-Ouest, USA Centre Nord, Brésil Sud, Canada Est, Canada Centre, Europe Ouest, Royaume-Uni Sud, Royaume-Uni Ouest, Asie Est, Japon Est, Inde Sud, Asie Sud-Est, Australie Est, Inde Centre, Europe Nord, Australie Sud-Est, France Centre, France Sud, Japon Ouest, Corée Centre, Corée Sud. | Aucun |


## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>Que se passe-t-il pour les points de récupération d’archive si j’arrête la protection et conserve les données ?

Le point de récupération reste dans l’archive définitivement. Pour plus d’informations, consultez [Impact de l’arrêt de la protection sur les points de récupération](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points).

### <a name="is-cross-region-restore-supported-from-archive-tier"></a>La restauration entre régions est-elle prise en charge à partir du niveau archive ?

Lorsque vous déplacez vos données dans des coffres GRS du niveau standard au niveau archive, les données sont déplacées dans l’archive GRS. Cela est vrai même lorsque la restauration entre régions est activée. Une fois les données de sauvegarde déplacées dans le niveau archive, vous ne pouvez pas restaurer les données dans la région jumelée. Toutefois, en cas de défaillance régionale, les données de sauvegarde dans la région secondaire deviennent disponibles pour restauration. 

Lors de la restauration d’un point de récupération de niveau archive dans la région primaire, le point de récupération est copié vers le niveau standard et conservé conformément à la durée de réhydratation, tant dans la région primaire que dans la région secondaire. Vous pouvez effectuer une restauration entre régions à partir de ces points de récupération réhydratés.

### <a name="i-can-see-eligible-recovery-points-for-my-virtual-machine-but-i-cant-seeing-any-recommendation-what-can-be-the-reason"></a>Je peux voir les points de récupération éligibles pour ma machine virtuelle, mais je ne vois aucune recommandation. Quelle peut être la raison ?

Les points de récupération des machines virtuelles répondent aux critères d’éligibilité. Par conséquent, il existe des points de récupération archivables. Toutefois, l’attrition de la machine virtuelle peut être faible, de sorte qu’il n’y a aucune recommandation. Dans ce scénario, bien que vous puissiez déplacer les points de récupération archivables vers le niveau Archive, cela peut augmenter les coûts de stockage de sauvegarde globaux.

### <a name="i-have-stopped-protection-and-retained-data-for-my-workload-can-i-move-the-recovery-points-to-archive-tier"></a>J’ai arrêté la protection et conservé les données pour ma charge de travail. Puis-je déplacer les points de récupération vers le niveau Archive ?

Non. Une fois la protection arrêtée pour une charge de travail particulière, les points de récupération correspondants ne peuvent pas être déplacés vers le niveau Archive. Pour déplacer des points de récupération vers le niveau Archive, vous devez reprendre la protection de la source de données.

## <a name="next-steps"></a>Étapes suivantes

- [Utilisation du niveau Archive](use-archive-tier-support.md)
- [Tarifs Sauvegarde Azure](azure-backup-pricing.md)
