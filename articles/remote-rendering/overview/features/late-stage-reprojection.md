---
title: Reprojection en phase tardive
description: Informations sur la reprojection en phase tardive et sur son utilisation.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 13a950f67053d9eaaea39e2df34df3dd12ed00c1
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028685"
---
# <a name="late-stage-reprojection"></a>Reprojection en phase tardive

La *reprojection en phase tardive* (LSR) est une fonctionnalité matérielle qui permet de stabiliser les hologrammes lorsque l’utilisateur est en mouvement.

Les modèles statiques sont censés conserver visuellement leur position lorsque vous les contournez. S’ils semblent instables, ce comportement peut indiquer des problèmes LSR. Gardez à l’esprit que des transformations dynamiques supplémentaires, comme des animations ou des vues d’explosion, risquent de masquer ce comportement.

Vous pouvez choisir entre deux modes LSR différents : **Planar LSR** ou **Depth LSR**. Les deux modes LSR améliorent la stabilité des hologrammes, bien qu’ils aient chacun leurs propres limitations. Commencez par essayer Depth LSR, car il permet assurément d’obtenir de meilleurs résultats dans la plupart des cas.

## <a name="how-to-set-the-lsr-mode"></a>Définition du mode LSR

Le mode LSR à utiliser n’est pas le même selon que l’application cliente envoie ou non un tampon de profondeur. Elle opte pour le mode **Depth LSR** si elle envoie le tampon de profondeur, et pour le mode **Planar LSR** dans le cas contraire.

Les paragraphes suivants expliquent comment l’envoi du tampon de profondeur s’effectue respectivement dans les applications Unity et natives.

### <a name="unity"></a>Unity

Dans l’éditeur Unity, accédez à *:::no-loc text="File > Build Settings":::* . Sélectionnez *:::no-loc text="Player Settings":::* en bas à gauche, puis vérifiez sous *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* si la case **:::no-loc text="Enable Depth Buffer Sharing":::** est cochée :

![Indicateur d’activation du partage du tampon de profondeur](./media/unity-depth-buffer-sharing-enabled.png)

Si c’est le cas, votre application utilisera Depth LSR, sinon, elle utilisera Planar LSR.

Quand vous utilisez OpenXR, le tampon de profondeur doit toujours être envoyé. Le paramètre se trouve dans *:::no-loc text="XR Plug-in Management > OpenXR":::* . Le mode de reprojection peut ensuite être modifié via une extension dans le plug-in OpenXR :

```cs
using Microsoft.MixedReality.OpenXR;

public class OverrideReprojection : MonoBehaviour
{
    void OnEnable()
    {
        RenderPipelineManager.endCameraRendering += RenderPipelineManager_endCameraRendering;
    }
    void OnDisable()
    {
        RenderPipelineManager.endCameraRendering -= RenderPipelineManager_endCameraRendering;
    }

    // When using the Universal Render Pipeline, OnPostRender has to be called manually.
    private void RenderPipelineManager_endCameraRendering(ScriptableRenderContext context, Camera camera)
    {
        OnPostRender();
    }

    // Called directly when using Unity's legacy renderer.
    private void OnPostRender()
    {
        ReprojectionSettings reprojectionSettings = default;
        reprojectionSettings.ReprojectionMode = ReprojectionMode.PlanarManual; // Or your favorite reprojection mode.
        
        // In case of PlanarManual you also need to provide a focus point here.
        reprojectionSettings.ReprojectionPlaneOverridePosition = ...;
        reprojectionSettings.ReprojectionPlaneOverrideNormal = ...;
        reprojectionSettings.ReprojectionPlaneOverrideVelocity = ...;

        foreach (ViewConfiguration viewConfiguration in ViewConfiguration.EnabledViewConfigurations)
        {
            if (viewConfiguration.IsActive && viewConfiguration.SupportedReprojectionModes.Contains(reprojectionSettings.ReprojectionMode))
            {
                viewConfiguration.SetReprojectionSettings(reprojectionSettings);
            }
        }
    }
}
```

### <a name="native-c-applications"></a>Applications C++ natives

L’envoi du tampon de profondeur est entièrement sous le contrôle du code de liaison C++ natif, indépendamment de la version de WMR et d’OpenXR. La seule condition à remplir est qu’un tampon de profondeur doit être lié à l’API de graphismes au moment de l’appel de `GraphicsBinding::BlitRemoteFrame`.

## <a name="depth-lsr"></a>Depth LSR

Pour que Depth LSR fonctionne, l’application cliente doit fournir un tampon de profondeur valide qui contient toutes les géométries appropriées à prendre en compte pendant la reprojection en phase tardive.

Depth LSR tente de stabiliser la trame vidéo en fonction du contenu du tampon de profondeur fourni. Ainsi, le contenu qui n’y a pas été rendu, tel que les objets transparents, ne peut pas être ajusté par LSR mais présenter une instabilité et des artefacts de reprojection.

Pour atténuer l’instabilité de la reprojection pour les objets transparents, vous pouvez forcer l’écriture dans le tampon de profondeur. Consultez l’indicateur de matériau *TransparencyWritesDepth* pour les matériaux [Couleur](color-materials.md) et [PBR](pbr-materials.md). Notez toutefois que la qualité visuelle de l’interaction entre l’objet transparent et l’objet opaque peut souffrir de cet indicateur.

## <a name="planar-lsr"></a>Planar LSR

Planar LSR ne dispose pas d’informations de profondeur par pixel, à l’instar de Depth LSR. Au lieu de cela, il reprojette tout le contenu en fonction d’un plan que vous devez fournir à chaque trame.

Planar LSR reprojette de façon optimale les objets qui se trouvent le plus près du plan fourni. Plus un objet est éloigné, plus son apparence est instable. Si Depth LSR convient mieux pour la reprojection d’objets à différentes profondeurs, Planar LSR obtient de meilleurs résultats dans l’alignement de contenu par rapport à un plan.

### <a name="configure-planar-lsr-in-unity"></a>Configurer Planar LSR dans Unity

Les paramètres de plan sont dérivés de ce qu’on appelle un *point de focus*. Lors de l’utilisation de WMR, le point de focus doit être défini pour chaque image via `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`. Pour plus d’informations, consultez [API Unity Focus Point](/windows/mixed-reality/focus-point-in-unity). Pour OpenXR, le point de focus doit être défini via les `ReprojectionSettings` indiqués dans la section précédente.
Si vous ne définissez pas de zone de focus, une solution de secours sera choisie pour vous. Pour autant, ce point de secours automatique aboutit souvent à des résultats inférieurs.

Vous pouvez calculer vous-même la zone de focus, bien qu’il puisse paraître judicieux de la baser sur celle qui est calculée par l’hôte Remote Rendering. Appelez `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` pour l’obtenir.

En général, le client et l’hôte affichent chacun le contenu qui n’est pas pris en charge par l’autre, par exemple des éléments d’interface utilisateur sur le client. Ainsi, il peut s’avérer opportun de combiner la zone de focus à distance avec un point calculé localement.

Les zones de focus calculées dans deux trames successives peuvent varier considérablement. Leur utilisation telle quelle peut donner aux hologrammes une apparence sautillante. Pour éviter ce comportement, il est conseillé d’interpoler entre la zone de focus précédente et la zone actuelle.

## <a name="reprojection-pose-modes"></a>Modes de pose de reprojection

L’étendue du problème général avec un rendu hybride peut être formulée comme suit : le contenu local et le contenu distant se trouvent dans des poses distinctes (c’est-à-dire des espaces de coordonnées), car la pose distante est prédite par le serveur, tandis que la pose locale correspond à la pose actuelle réelle. Toutefois, à la fin d’une trame de rendu, le contenu distant et le contenu local doivent être alignés et mis à l’écran. L’illustration suivante montre un exemple dans lequel la pose locale et la pose distante sont traduites par rapport à la fenêtre d’affichage :

![Diagramme illustrant la pose locale et la pose distante par rapport à la fenêtre d’affichage cible.](./media/reprojection-remote-local.png)

ARR fournit deux modes de reprojection qui fonctionnent orthogonalement par rapport au mode LSR décrit ci-dessus. Ces modes sont appelés **:::no-loc text="Remote pose mode":::** et **:::no-loc text="Local pose mode":::** . Contrairement au mode LSR, les modes de pose définissent la façon dont le contenu distant et le contenu local sont combinés. Le choix du mode demande un compromis entre la qualité visuelle du contenu local et le niveau de performance d’exécution. Il faut donc réfléchir soigneusement à l’option appropriée pour les applications. Considérez les points ci-dessous.

### :::no-loc text="Remote pose mode":::

Le mode :::no-loc text="Remote pose mode"::: constitue le mode par défaut dans ARR. Dans ce mode, le contenu local est affiché en haut du flux d’image distante entrant à l’aide de la pose distante de la trame distante. Le résultat combiné est ensuite transféré au système d’exploitation pour la reprojection finale. Bien que cette approche n’utilise qu’une seule reprojection, la correction finale se base sur l’intervalle aller-retour afin que l’erreur de reprojection complète soit également appliquée au contenu local. Par conséquent, un delta de correction important peut entraîner des distorsions significatives de la géométrie locale, et notamment des éléments d’interface utilisateur.

Si l’on reprend l’illustration ci-dessus, la transformation suivante est appliquée dans le mode :::no-loc text="Remote pose mode"::: :

![Étapes de reprojection dans le mode de pose distante.](./media/reprojection-pose-mode-remote.png)

### :::no-loc text="Local pose mode":::

Dans ce mode, la reprojection est divisée en deux étapes distinctes : lors de la première étape, le contenu distant est reprojeté dans l’espace de pose local, c’est-à-dire l’espace avec lequel le contenu local est restitué par défaut sur les appareils de réalité virtuelle ou augmentée. Ensuite, le contenu local est affiché en haut de cette image prétransformée à l’aide de la pose locale habituelle. Au cours de la deuxième étape, le résultat combiné est transféré au système d’exploitation pour la reprojection finale. Dans la mesure où cette deuxième reprojection n’implique qu’un petit delta (le même que si ARR n’était pas présent), les artefacts de distorsion sont sensiblement atténués sur le contenu local.

En conséquence, l’illustration se présente ainsi :

![Étapes de reprojection dans le mode de pose locale.](./media/reprojection-pose-mode-local.png)

### <a name="performance-and-quality-considerations"></a>Considérations relatives au niveau de performance et à la qualité

Le choix du mode de pose présente des implications en matière de qualité visuelle et de niveau de performance. Le coût d’exécution supplémentaire côté client pour effectuer la reprojection supplémentaire dans le mode :::no-loc text="Local pose mode"::: sur un appareil HoloLens 2 s’élève à environ 1 milliseconde par trame de temps GPU. Il doit être pris en considération si l’application cliente est déjà proche du budget de trame de 16 millisecondes. En revanche, il existe des types d’applications sans contenu local et d’autres dont le contenu local n’est pas sujet à des artefacts de distorsion. Dans ce cas, le mode :::no-loc text="Local pose mode"::: n’offre aucun avantage visuel, car la qualité de la reprojection du contenu distant n’est pas affectée.

Le conseil général serait donc de tester les modes selon le cas d’usage et de déterminer si le gain en termes de qualité visuelle justifie la surcharge de performances supplémentaire. Il est également possible de basculer dynamiquement entre les modes, par exemple en n’activant le mode local que lorsque des interfaces utilisateur importantes s’affichent.

### <a name="how-to-change-the-no-loc-textpose-mode-at-runtime"></a>Modification du mode :::no-loc text="Pose mode"::: à l’exécution

L’API cliente suivante permet de changer de mode à l’exécution :

```cs
RenderingSession session = ...;
session.GraphicsBinding.SetPoseMode(PoseMode.Local); // set local pose mode
```
 
```cpp
ApiHandle<RenderingSession> session = ...;
session->GetGraphicsBinding()->SetPoseMode(PoseMode::Local); // set local pose mode
```

Le mode peut être modifié chaque fois que l’objet de liaison de graphismes est disponible.

## <a name="next-steps"></a>Étapes suivantes

* [Requêtes de performances côté serveur](performance-queries.md)