---
title: Appeler une API à partir d’un navigateur - Azure Cognitive Services | Microsoft Docs
description: Bien démarrer avec le service Décision personnalisée Azure pour optimiser une page web en effectuant des appels d’API directement à partir d’un navigateur.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/09/2018
ms.author: slivkins,marcozo,alekh
ms.openlocfilehash: 10236c9d8f70d9b90a896464b4f86a847ee904c2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35370085"
---
# <a name="call-api-from-a-browser"></a>Appeler des API à partir d’un navigateur

Cet article vous aide à appeler les API du service Décision personnalisée Azure directement depuis un navigateur.

Commencez par [inscrire votre application](custom-decision-service-get-started-register.md).

Allons-y. Votre application est modélisée comme ayant une première page, qui mène à plusieurs pages d’article. La première page utilise le service Décision personnalisée pour spécifier l’ordre de ses pages d’article. Dans l’en-tête HTML de la première page, insérez le code suivant :

```html
// Define the "callback function" to render UI
<script> function callback(data) { … } </script>

// call Ranking API, after callback() is defined
<script src="https://ds.microsoft.com/api/v2/<appId>/rank/<actionSetId>" async></script>
```

L’argument `data` contient le classement des URL à afficher. Pour plus d’informations, voir la documentation de référence sur les [API](custom-decision-service-api-reference.md).

Pour gérer un utilisateur, cliquez sur le premier article, puis appelez le code suivant sur la première page :

```javascript
// call Reward API to report a click
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/' + data.eventId,
    contentType: "application/json" })
```

Ici, `data` est l’argument de la fonction `callback()`. Vous trouverez un exemple d’implémentation de ce [didacticiel](custom-decision-service-tutorial-news.md#use-the-apis).

Enfin, vous devez fournir l’API Ensemble d’actions qui renvoie la liste des articles (actions) que le service Décision personnalisée doit prendre en compte. Implémentez cette API en tant que flux RSS, comme illustré ici :

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Ici, chaque élément `<item>` de niveau supérieur décrit un article. Le `<link>` est obligatoire. Il est utilisé en tant qu’ID d’action par le service Décision personnalisée. Spécifiez `<date>` (au format RSS standard) si vous avez plus de 15 articles. Les 15 articles les plus récents sont utilisés. Le `<title>` est facultatif. Il est utilisé pour créer des fonctionnalités de texte pour l’article.

## <a name="next-steps"></a>Étapes suivantes

* Suivez un [didacticiel](custom-decision-service-tutorial-news.md) pour obtenir un exemple plus détaillé.
* Pour en savoir plus sur les fonctionnalités fournies, voir la documentation de référence sur les [API](custom-decision-service-api-reference.md).