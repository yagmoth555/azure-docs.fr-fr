---
title: Effectuer une reconnaissance vocale à l’aide du kit de développement logiciel (SDK) Speech pour C++
titleSuffix: Microsoft Cognitive Services
description: >
  Découvrez comment effectuer une reconnaissance vocale (à partir d’un fichier, à partir d’un micro, avec un modèle personnalisé, en continu ou en une seule fois) à l’aide du kit SDK Speech pour C++.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: b3df385adbd93cd81637661f4df9a15707170346
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959442"
---
# <a name="recognize-speech-by-using-the-speech-sdk-for-c"></a>Effectuer une reconnaissance vocale à l’aide du kit de développement logiciel (SDK) Speech pour C++

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

[!INCLUDE [Introduction](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-intro.md)]

[!INCLUDE [Introduction for top-level declarations](../../../includes/cognitive-services-speech-service-how-to-toplevel-declarations.md)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#toplevel)]

[!INCLUDE [Introduction to using a microphone](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-microphone.md)]

[!code-cpp[Speech recognition by using a microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

[!INCLUDE [Introduction to using customized recognition](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-customized.md)]

[!code-cpp[Speech recognition by using a customized model](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

[!INCLUDE [Introduction to using a continuous file](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-continuous.md)]

[!code-cpp[Continuous speech recognition](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionWithFile)]

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Recherchez le code utilisé dans cet article au sein du dossier samples/cpp/windows/console.

## <a name="next-steps"></a>Étapes suivantes

- [Comment reconnaître les intentions vocales](how-to-recognize-intents-from-speech-cpp.md)
- [Comment traduire une entrée vocale](how-to-translate-speech-cpp.md)

