---
title: Provisionner des appareils Windows pour la surveillance à distance en C - Azure| Microsoft Docs
description: Explique comment connecter un appareil à l’accélérateur de solution de surveillance à distance avec une application écrite en C exécutée sur Windows.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: dobett
ms.openlocfilehash: 139daea3e885636b352d4c9a1ba2651a24195b21
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38309868"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>Connecter votre appareil à l’accélérateur de solution de surveillance à distance (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Ce tutoriel vous montre comment connecter un appareil physique à l’accélérateur de solution Surveillance à distance.

## <a name="create-a-c-client-solution-on-windows"></a>Créer une solution de client C sur Windows

Comme avec la plupart des applications embarquées qui s’exécutent sur des appareils limités, le code client pour l’application d’appareil est écrit en C. Dans ce didacticiel, vous générez l’application sur une machine exécutant Windows.

### <a name="create-the-starter-project"></a>Créer le projet de démarrage

Créez un projet de démarrage dans Visual Studio 2017, puis ajoutez les packages NuGet clients de l’appareil IoT Hub :

1. Dans Visual Studio, créez une application console C à l’aide du modèle **Application console Windows** de Visual C++. Nommez le projet **RMDevice**.

    ![Créer une application de console Windows Visual C++](./media/iot-accelerators-connecting-devices/visualstudio01.png)

1. Dans **l’Explorateur de solutions**, supprimez les fichiers `stdafx.h`, `targetver.h` et `stdafx.cpp`.

1. Dans **l’Explorateur de solutions**, renommer le fichier `RMDevice.cpp` en `RMDevice.c`.

    ![Explorateur de solutions affichant le fichier RMDevice.c renommé](./media/iot-accelerators-connecting-devices/visualstudio02.png)

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **RMDevice**, puis cliquez sur **Gérer les packages NuGet**. Choisissez **Parcourir**, puis recherchez et installez les packages NuGet suivants :

    * Microsoft.Azure.IoTHub.Serializer
    * Microsoft.Azure.IoTHub.IoTHubClient
    * Microsoft.Azure.IoTHub.MqttTransport

    ![Gestionnaire de package NuGet montrant les packages Microsoft.Azure.IoTHub installés](./media/iot-accelerators-connecting-devices/visualstudio03.png)

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **RMDevice**, puis choisissez **Propriétés** pour ouvrir la boîte de dialogue **Pages de propriétés** du projet. Pour plus d’informations, consultez [Setting Visual C++ Project Properties](https://docs.microsoft.com/cpp/ide/working-with-project-properties) (Définition des propriétés de projet Visual C++).

1. Choisissez le dossier **C/C++**, puis choisissez la page de propriétés **En-têtes précompilés**.

1. Définissez **En-tête précompilé** sur **Sans utiliser les en-têtes précompilés**. Choisissez ensuite **Appliquer**.

    ![Propriétés du projet indiquant que le projet n’utilise pas les en-têtes précompilés](./media/iot-accelerators-connecting-devices/visualstudio04.png)

1. Choisissez le dossier **Linker**, puis la page de propriétés **d’entrée**.

1. Ajoutez `crypt32.lib` à la propriété **Dépendances supplémentaires**. Pour enregistrer les valeurs des propriétés du projet, cliquez sur **OK**, puis de nouveau sur **OK**.

    ![Propriétés du projet montrant l’éditeur de liens avec crypt32.lib](./media/iot-accelerators-connecting-devices/visualstudio05.png)

### <a name="add-the-parson-json-library"></a>Ajouter la bibliothèque JSON Parson

Ajoutez la bibliothèque JSON Parson au projet **RMDevice** ainsi que les instructions `#include` requises :

1. Dans un dossier approprié sur votre ordinateur, clonez le référentiel GitHub Parson à l’aide de la commande suivante :

    ```cmd
    git clone https://github.com/kgabis/parson.git
    ```

1. Copiez les fichiers `parson.h` et `parson.c` de la copie locale du dépôt Parson dans le dossier de votre projet **RMDevice**.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet **RMDevice**, choisissez **Ajouter**, puis **Élément existant**.

1. Dans la boîte de dialogue **Ajouter un élément existant**, sélectionnez les fichiers `parson.h` et `parson.c` dans le dossier du projet **RMDevice**. Pour ajouter ces deux fichiers à votre projet, choisissez **Ajouter**.

    ![Explorateur de solutions affichant les fichiers parson.h et parson.c](./media/iot-accelerators-connecting-devices/visualstudio06.png)

1. Dans Visual Studio, ouvrez le fichier `RMDevice.c`. Remplacez les instructions existantes `#include` par ce qui suit :

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include <string.h>
    ```

    > [!NOTE]
    > À présent, vous pouvez vérifier que votre projet contient les dépendances appropriées définies en générant la solution.

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>Créer et exécuter l’exemple.

Ajoutez du code pour appeler la fonction **remote\_monitoring\_run**, puis générez et exécutez l’application de l’appareil :

1. Pour appeler la fonction **remote\_monitoring\_run**, remplacez la fonction **main** par le code suivant :

    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. Choisissez **Générer**, puis **Générer la solution** pour générer l’application de l’appareil.

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **RMDevice**, choisissez **Déboguer**, puis choisissez **Démarrer une nouvelle instance** pour exécuter l’exemple. La console affiche des messages quand l’application :

    * L’application envoie un exemple de données de télémétrie à l’accélérateur de solution.
    * Reçoit les valeurs de propriété souhaitées définies dans le tableau de bord des solutions.
    * Répond aux méthodes appelées à partir du tableau de bord des solutions.

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
