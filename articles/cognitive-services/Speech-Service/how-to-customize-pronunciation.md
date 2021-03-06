---
title: Service Speech d’Azure Cognitive Services
description: Découvrez comment personnaliser la prononciation avec le service Speech de Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/02/2018
ms.author: panosper
ms.openlocfilehash: 93fec1ea78263798588a43b2314ffdea736cdbbc
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745349"
---
# <a name="enable-custom-pronunciation"></a>Activer la prononciation personnalisée
La prononciation personnalisée vous permet de définir l’écriture et la forme phonétiques d’un mot ou d’un terme. Cela peut être utile pour traiter les termes personnalisés tels que les noms ou les acronymes. Vous avez uniquement besoin d’un fichier de prononciation (un simple fichier .txt).

Voici comment cela fonctionne. Dans un simple fichier .txt, vous pouvez saisir plusieurs entrées de prononciation personnalisées. La structure est comme suit :

```
Display form <Tab> Spoken form <Newline>
```

Plusieurs exemples sont présentés dans le tableau suivant :

| Forme écrite (en anglais) | Forme orale (en anglais) |
|----------|-------|
| C3PO | see three pea o |
| L8R | late are |
| CNTK (Computational Network Toolkit de Microsoft Research) | see n tea k|

## <a name="requirements-for-the-spoken-form"></a>Exigences pour la forme orale
La forme orale doit être en minuscules, ce que vous pouvez appliquer pendant l’importation. En outre, vous devez fournir des vérifications dans l’importateur de données. Aucune tabulation n’est autorisée dans la forme orale ou la forme écrite. Néanmoins, d’autres caractères interdits peuvent être présents dans la forme écrite (par exemple, ~ et ^).

Chaque fichier .txt peut avoir plusieurs entrées, comme le montre l’image suivante :

![Exemples de prononciation d’acronyme](media/stt/custom-speech-pronunciation-file.png)

La forme orale est la séquence phonétique de la forme écrite. Elle est composée de lettres, de mots ou de syllabes. Actuellement, il n’existe aucun guide ou ensemble de normes pour vous aider à formuler la forme orale. 

## <a name="supported-pronunciation-characters"></a>Caractères de prononciation pris en charge
Pour l’heure, la prononciation personnalisée prend uniquement en charge l’anglais (en-US) et l’allemand (de-DE). Les jeux de caractères dont vous pouvez vous servir pour exprimer la forme orale d’un terme (dans le fichier de prononciation personnalisée) sont présentés dans le tableau suivant : 

| Langue | Caractères |
|---------- |----------|
| Anglais (en-US) | a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |
| Allemand (de-de) | ä, ö, ü, ẞ, a, b, c, d, e, f, g, h, i, j, k, l, o, p, q, r, s, t, u, v, w, x, y, z |

> [!NOTE]
> La forme écrite d’un terme (dans un fichier de prononciation) doit être identique à celle utilisée dans un jeu de données d’adaptation de langue.

## <a name="requirements-for-the-display-form"></a>Exigences pour la forme écrite
Une forme écrite peut être constituée d’un mot, d’un terme, d’un acronyme personnalisé unique ou de mots composés combinant des mots existants. Vous pouvez également entrer des prononciations différentes pour les mots courants. 

>[!NOTE]
>Nous vous déconseillons d’utiliser cette fonctionnalité pour reformuler des mots courants ou modifier la forme orale. Il est préférable d’exécuter le décodeur pour voir si certains mots inhabituels (tels que des abréviations, des mots techniques ou des mots étrangers) ne sont pas décodés de manière incorrecte. S’ils ne sont pas décodés correctement, ajoutez-les dans le fichier de prononciation personnalisée. Dans le modèle de langage, vous devez toujours utiliser uniquement la forme écrite d’un mot. 

## <a name="requirements-for-the-file-size"></a>Exigences pour la taille de fichier
La taille du fichier .txt qui contient les entrées de prononciation est limitée à 1 mégaoctet (Mo). En principe, vous n’avez pas besoin de charger de grandes quantités de données via ce fichier. La taille de la plupart des fichiers de prononciation personnalisée est normalement de quelques kilo-octets (Ko). L’encodage du fichier .txt pour tous les paramètres régionaux doit être la marque d'ordre d'octet UTF-8. Pour les paramètres régionaux anglais, ANSI est également acceptable.

## <a name="next-steps"></a>Étapes suivantes
* Améliorez la précision de la reconnaissance en créant un [modèle acoustique personnalisé](how-to-customize-acoustic-models.md).
* Améliorez la précision de la reconnaissance en créant un [modèle de langage personnalisé](how-to-customize-language-model.md).
 