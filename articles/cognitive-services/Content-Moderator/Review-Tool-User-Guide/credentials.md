---
title: Informations d’identification dans Azure Content Moderator | Microsoft Docs
description: Gestion des informations d’identification Content Moderator utilisées avec les API.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 4531fa4c8bbb7bb9c1daeaaac6f9e7078dae250a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35367933"
---
# <a name="manage-credentials"></a>Gérer les informations d’identification

Vos informations d’identification Content Moderator se trouvent aux emplacements suivants :

- [Le portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)
- [L’outil de révision de Content Moderator](http://contentmoderator.cognitive.microsoft.com/)

Cet article détaille les différents emplacements des informations d’identification ainsi que leur interconnexion.

## <a name="the-azure-portal"></a>Le portail Azure

Dans le tableau de bord du portail Azure, sélectionnez votre compte Content Moderator. Sous **Gestion des ressources**, sélectionnez **Clés**. Pour copier la clé, sélectionnez l’icône située à droite de la clé.

![Clés Content Moderator du portail Azure](images/credentials-azure-portal-keys.PNG)

### <a name="how-to-use-your-azure-account-with-the-review-tool"></a>Utiliser votre compte Azure avec l’outil de révision
Pour utiliser votre clé Azure avec les API de révision, copiez l’ID de la ressource affiché dans l’écran **Propriétés** (voir capture d’écran ci-dessous). Lorsque vous êtes invité à saisir vos informations d’identification dans l’outil de révision, collez cet ID dans le champ **ID de ressource approuvée**, comme illustré ci-dessous dans la section **ID de ressource**. 

Pour utiliser votre clé Azure avec les flux de travail disponibles dans Content Moderator, saisissez-la dans le champ **Ocp-Apim-Subscription-Key** de la page **Paramètres du flux de travail**, comme illustré ci-dessous dans la section **Flux de travail**.

> [!NOTE]
> Une fois que vous avez indiqué la clé et l’ID de ressource de votre abonnement Azure à ces deux emplacements dans l’outil de révision, la clé d’évaluation **Trial Ocp-Apim-Subscription-Key** qui est affichée dans l’écran des informations d’identification devient obsolète, mais reste disponible.
> Avec la clé d’évaluation, vous ne pouvez pas dépasser plus de 5 000 transactions par mois, à raison d’une requête par seconde (RPS).

![ID de ressource Content Moderator dans le portail Azure](images/credentials-azure-portal-resourceid.PNG)


## <a name="the-review-tool"></a>L’outil de révision

Dans le tableau de bord de l’outil de révision, dans l’onglet **Paramètres**, sélectionnez **Informations d’identification**.

![Informations d’identification Content Moderator dans l’outil de révision](images/credentials-trial-resource-workflow.PNG)

La section suivante passe en revue les détails de l’image précédente :


### <a name="api"></a>API

Plusieurs éléments sont affichés dans la première partie : le **point de terminaison de l’API de révision**, l’**ID de l’équipe** et la clé d’évaluation de Content Moderator (**Ocp-Apim-Subscription-Key**) qui est générée lors du processus de création de votre équipe de révision. Utilisez-les pour appeler toutes les API Content Moderator, y compris l’API de révision.

Notez également l’identificateur de la région associée au point de terminaison de l’API. Par exemple, **westus** correspond à la région dans l’URL « https://westus.api.cognitive.microsoft.com/contentmoderator/review/v1.0 ».

![Clé Content Moderator dans l’outil de révision](images/credentials-trialkey.PNG)


### <a name="resource-id"></a>ID de ressource

La seconde partie est vide et ne comporte aucun ID de ressource. **Pour utiliser votre clé d’abonnement Azure avec l’API de révision, accédez à l’écran ID de ressource (voir la capture d’écran précédente) et copiez l’ID dans le champ indiqué**. Cliquez sur « **+** » pour enregistrer votre ID de ressource.

> [!NOTE]
> Votre abonnement Content Moderator et l’équipe de révision doivent être associés à la même région afin que l’outil reconnaisse votre équipe et accède aux données. Par exemple, dans les images de cette page, la région **West US** **(4)** contient l’abonnement Azure Content Moderator et votre équipe de révision.

![ID de ressource Content Moderator dans l’outil de révision](images/credentials-resourceids.PNG)


### <a name="workflows"></a>Flux de travail

La troisième partie fait état des informations utilisées pour l’exécution des flux de travail. Vous pouvez voir que la clé d’évaluation générée automatiquement est affichée par défaut. 

**Mettez-la à jour avec votre clé Azure dès que vous disposez d’un abonnement Azure**. Les deux autres champs permettent d’utiliser des listes de termes et d’images au cours des opérations de filtrage de texte et d’évaluation d’images, respectivement.

![Informations d’identification du flux de travail Content Moderator dans l’outil de révision](images/credentials-workflow.PNG)


## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment utiliser les informations d’identification Content Moderator dans vos [flux de travail](workflows.md).
