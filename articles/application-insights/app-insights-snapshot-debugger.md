---
title: Débogueur de captures instantanées Azure Application Insights pour les applications .NET | Microsoft Docs
description: Des captures instantanées de débogage sont collectées automatiquement lorsque des exceptions sont levées dans des applications .NET de production
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2018
ms.reviewer: pharring
ms.author: mbullwin
ms.openlocfilehash: d4c27c8297fb5a2ad13a245279a206d00fc4f8b1
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144123"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Captures instantanées de débogage sur exceptions levées dans des applications .NET

Quand une exception se produit, vous pouvez collecter automatiquement une capture instantanée de débogage à partir de votre application web dynamique. La capture instantanée indique l’état du code source et des variables au moment où l’exception a été levée. Le Débogueur de capture instantanée (préversion) dans [Azure Application Insights](app-insights-overview.md) analyse la télémétrie des exceptions à partir de votre application web. Il collecte des captures instantanées sur les principales exceptions levées afin que vous disposiez des informations dont vous avez besoin pour diagnostiquer des problèmes de production. Incluez le [package NuGet de collecte des captures instantanées](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application, et configurez éventuellement les paramètres de collecte dans [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Les captures instantanées apparaissent sur les [exceptions](app-insights-asp-net-exceptions.md) dans le portail Application Insights.

Vous pouvez afficher les captures instantanées de débogage dans le portail pour consulter la pile des appels et inspecter les variables à chaque frame de pile des appels. Pour améliorer la puissance de débogage du code source, ouvrez les captures instantanées à l’aide de Visual Studio 2017 Enterprise en [téléchargeant l’extension du Débogueur de capture instantanée pour Visual Studio](https://aka.ms/snapshotdebugger). Dans Visual Studio, vous pouvez également [définir des points de capture instantanée pour prendre des captures instantanées de manière interactive](https://aka.ms/snappoint) sans attendre la levée d’une exception.

La collecte de captures instantanées est disponible pour :
* les applications .NET framework et ASP.NET exécutant .NET Framework 4.5 ou version ultérieure ;
* les applications .NET core 2.0 et ASP.NET Core 2.0 s’exécutant sous Windows.

Les environnements suivants sont pris en charge :
* Azure App Service
* Service Cloud Azure exécutant la famille de systèmes d’exploitation 4 ou ultérieur
* Services Azure Service Fabric exécutant Windows Server 2012 R2 ou ultérieur
* Machines virtuelles Azure exécutant Windows Server 2012 R2 ou ultérieur
* Machines locales physiques ou virtuelles exécutant Windows Server 2012 R2 ou version ultérieure.

> [!NOTE]
> Les applications clientes (par exemple, WPF, Windows Forms ou UWP) ne sont pas prises en charge.

### <a name="configure-snapshot-collection-for-aspnet-applications"></a>Configurer la collecte de captures instantanées pour les applications ASP.NET

1. Si vous ne l’avez pas encore fait, [Activez Application Insights dans votre application web](app-insights-asp-net.md).

2. Incluez le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application.

3. Examinez les options par défaut que le package a ajoutées à [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
        <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
        <IsEnabled>true</IsEnabled>
        <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
        <!-- DeveloperMode is a property on the active TelemetryChannel. -->
        <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
        <!-- How many times we need to see an exception before we ask for snapshots. -->
        <ThresholdForSnapshotting>1</ThresholdForSnapshotting>
        <!-- The maximum number of examples we create for a single problem. -->
        <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
        <!-- The maximum number of problems that we can be tracking at any time. -->
        <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
        <!-- How often we reconnect to the stamp. The default value is 15 minutes.-->
        <ReconnectInterval>00:15:00</ReconnectInterval>
        <!-- How often to reset problem counters. -->
        <ProblemCounterResetInterval>1.00:00:00</ProblemCounterResetInterval>
        <!-- The maximum number of snapshots allowed in ten minutes.The default value is 1. -->
        <SnapshotsPerTenMinutesLimit>1</SnapshotsPerTenMinutesLimit>
        <!-- The maximum number of snapshots allowed per day. -->
        <SnapshotsPerDayLimit>30</SnapshotsPerDayLimit>
        <!-- Whether or not to collect snapshot in low IO priority thread. The default value is true. -->
        <SnapshotInLowPriorityThread>true</SnapshotInLowPriorityThread>
        <!-- Agree to send anonymous data to Microsoft to make this product better. -->
        <ProvideAnonymousTelemetry>true</ProvideAnonymousTelemetry>
        <!-- The limit on the number of failed requests to request snapshots before the telemetry processor is disabled. -->
        <FailedRequestLimit>3</FailedRequestLimit>
        </Add>
    </TelemetryProcessors>
    ```

4. Des captures instantanées sont collectées uniquement sur des exceptions signalées à Application Insights. Dans certains cas (par exemple, des versions plus anciennes de la plateforme .NET), il se peut que vous deviez [configurer la collecte des exceptions](app-insights-asp-net-exceptions.md#exceptions) afin de voir celles-ci avec des captures instantanées dans le portail.


### <a name="configure-snapshot-collection-for-aspnet-core-20-applications"></a>Configurer la collecte de captures instantanées pour les applications ASP.NET Core 2.0

1. Si vous ne l’avez pas encore fait, [Activez Application Insights dans votre application web ASP.NET Core](app-insights-asp-net-core.md).

    > [!NOTE]
    > Être sûr que votre application fait référence à la version 2.1.1 ou plus récente, du package Microsoft.ApplicationInsights.AspNetCore.

2. Incluez le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application.

3. Modifiez la classe `Startup` de votre application pour ajouter et configurer le processeur de télémétrie du collecteur de captures instantanées.

    Ajoutez les paramètres suivants à l’aide des instructions pour `Startup.cs`

   ```csharp
   using Microsoft.ApplicationInsights.SnapshotCollector;
   using Microsoft.Extensions.Options;
   using Microsoft.ApplicationInsights.AspNetCore;
   using Microsoft.ApplicationInsights.Extensibility;
   ```

   Ajoutez les paramètres suivants de la classe `SnapshotCollectorTelemetryProcessorFactory` à la classe `Startup`.

   ```csharp
   class Startup
   {
       private class SnapshotCollectorTelemetryProcessorFactory : ITelemetryProcessorFactory
       {
           private readonly IServiceProvider _serviceProvider;

           public SnapshotCollectorTelemetryProcessorFactory(IServiceProvider serviceProvider) =>
               _serviceProvider = serviceProvider;

           public ITelemetryProcessor Create(ITelemetryProcessor next)
           {
               var snapshotConfigurationOptions = _serviceProvider.GetService<IOptions<SnapshotCollectorConfiguration>>();
               return new SnapshotCollectorTelemetryProcessor(next, configuration: snapshotConfigurationOptions.Value);
           }
       }
       ...
    ```
    Ajoutez les services `SnapshotCollectorConfiguration` et `SnapshotCollectorTelemetryProcessorFactory` au pipeline de start-up :

    ```csharp
       // This method gets called by the runtime. Use this method to add services to the container.
       public void ConfigureServices(IServiceCollection services)
       {
           // Configure SnapshotCollector from application settings
           services.Configure<SnapshotCollectorConfiguration>(Configuration.GetSection(nameof(SnapshotCollectorConfiguration)));

           // Add SnapshotCollector telemetry processor.
           services.AddSingleton<ITelemetryProcessorFactory>(sp => new SnapshotCollectorTelemetryProcessorFactory(sp));

           // TODO: Add other services your application needs here.
       }
   }
   ```

4. Configurez le collecteur de captures instantanées en ajoutant une section SnapshotCollectorConfiguration au fichier appsettings.json. Par exemple : 

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "IsEnabledInDeveloperMode": false,
       "ThresholdForSnapshotting": 1,
       "MaximumSnapshotsRequired": 3,
       "MaximumCollectionPlanSize": 50,
       "ReconnectInterval": "00:15:00",
       "ProblemCounterResetInterval":"1.00:00:00",
       "SnapshotsPerTenMinutesLimit": 1,
       "SnapshotsPerDayLimit": 30,
       "SnapshotInLowPriorityThread": true,
       "ProvideAnonymousTelemetry": true,
       "FailedRequestLimit": 3
     }
   }
   ```

### <a name="configure-snapshot-collection-for-other-net-applications"></a>Configurer la collecte de captures instantanées pour d’autres applications .NET

1. Si votre application n’est pas instrumentée avec Application Insights, commencez par [activer Application Insights et définir la clé d’instrumentation](app-insights-windows-desktop.md).

2. Incluez le package NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) dans votre application.

3. Des captures instantanées sont collectées uniquement sur des exceptions signalées à Application Insights. Il se peut que vous deviez modifier votre code pour les signaler. Le code de gestion des exceptions dépend de la structure de votre application, mais en voici un exemple :
    ```csharp
   TelemetryClient _telemetryClient = new TelemetryClient();

   void ExampleRequest()
   {
        try
        {
            // TODO: Handle the request.
        }
        catch (Exception ex)
        {
            // Report the exception to Application Insights.
            _telemetryClient.TrackException(ex);

            // TODO: Rethrow the exception if desired.
        }
   }
    ```

## <a name="grant-permissions"></a>Accorder des autorisations

Les propriétaires de l’abonnement Azure peuvent inspecter des captures instantanées. Les autres utilisateurs doivent y être autorisés par un propriétaire.

Pour accorder cette autorisation, vous devez attribuer le rôle `Application Insights Snapshot Debugger` aux utilisateurs chargés d’inspecter les captures instantanées. Ce rôle peut être attribué à des utilisateurs ou à des groupes par les propriétaires d’abonnements pour la ressource Application Insights cible, ou pour son groupe de ressources ou son abonnement.

1. Accédez à la ressource Application Insights dans le portail Azure.
1. Cliquez sur **Contrôle d’accès (IAM)**.
1. Cliquez sur le bouton **+Ajouter**.
1. Sélectionnez **Débogueur de capture instantanée d’Application Insights** dans la liste déroulante **Rôles**.
1. Entrez le nom de l’utilisateur à ajouter.
1. Cliquez sur le bouton **Enregistrer** pour ajouter l’utilisateur au rôle.


> [!IMPORTANT]
> Les captures instantanées des valeurs de variables et de paramètres peuvent contenir des informations personnelles ou sensibles.

## <a name="debug-snapshots-in-the-application-insights-portal"></a>Déboguer des captures instantanées dans le portail Application Insights

Si une capture instantanée est disponible pour une exception ou un ID de problème donné, le bouton **Ouvrir la capture instantanée de débogage** s’affiche dans [l’exception](app-insights-asp-net-exceptions.md) dans le portail Application Insights.

![Bouton Ouvrir la capture instantanée de débogage sur l’exception](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

Une pile d’appel et un volet de variables s’affichent dans la vue Capture instantanée de débogage. Quand vous sélectionnez des images de la pile d’appel dans le volet correspondant, vous voyez les variables et les paramètres locaux de cet appel de fonction dans le volet des variables.

![Afficher la capture instantanée de débogage dans le portail](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Les captures instantanées peuvent contenir des informations sensibles. Par défaut, elles ne sont pas visibles. Pour afficher les captures instantanées, le rôle `Application Insights Snapshot Debugger` doit vous être attribué.

## <a name="debug-snapshots-with-visual-studio-2017-enterprise"></a>Déboguer des captures instantanées avec Visual Studio 2017 Enterprise
1. Cliquez sur le bouton **Télécharger la capture instantanée** pour télécharger un fichier `.diagsession`, qui peut être ouvert par Visual Studio 2017 Enterprise.

2. Pour ouvrir le fichier `.diagsession`, vous devez d’abord [télécharger et installer l’extension du débogueur de captures instantanées pour Visual Studio](https://aka.ms/snapshotdebugger).

3. Une fois le fichier de capture instantanée ouvert, la page de débogage de minidump s’affiche dans Visual Studio. Cliquez sur **Debug Managed Code** (Déboguer le code managé) pour démarrer le débogage de la capture instantanée. La capture instantanée s’ouvre sur la ligne de code où l’exception a été levée et vous permet de déboguer l’état actuel du processus.

    ![Afficher la capture instantanée de débogage dans Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

La capture instantanée téléchargée contient tous les fichiers de symboles détectés sur votre serveur d’applications web. Ces fichiers de symboles sont nécessaires pour associer les données de capture instantanée au code source. Pour les applications App Service, veillez à activer le déploiement des symboles lorsque vous publiez vos applications web.

## <a name="how-snapshots-work"></a>Fonctionnement des captures instantanées

Le collecteur de captures instantanées est implémenté en tant que [Processeur d’Application Insights Telemetry](app-insights-configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Lorsque votre application s’exécute, le processeur de télémétrie du collecteur de captures instantanées est ajouté au pipeline de télémétrie de votre application.
Chaque fois que votre application appelle [TrackException](app-insights-asp-net-exceptions.md#exceptions), le collecteur de captures instantanées calcule un ID de problème à partir du type de l’exception levée et de la méthode de levée de l’exception.
Chaque fois que votre application appelle TrackException, un compteur est incrémenté pour l’ID de problème approprié. Lorsque le compteur atteint la valeur `ThresholdForSnapshotting`, l’ID de problème est ajouté à un Plan de collecte.

Le collecteur de captures instantanées analyse également les exceptions à mesure qu’elles sont levées en s’abonnant à l’événement [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception). Lorsque cet événement se déclenche, l’ID de problème de l’exception est calculé et comparé aux ID de problème figurant dans le Plan de collecte.
S’il existe une correspondance, un instantané du processus en cours d’exécution est créé. Un identificateur unique est attribué à l’instantané et l’exception est marquée avec cet identificateur. Après fois que le gestionnaire FirstChanceException a retourné l’exception levée, celle-ci est traitée normalement. Finalement, l’exception atteint à nouveau la méthode TrackException où elle est signalée à Application Insights avec l’identificateur de capture instantanée.

Le processus principal continue à s’exécuter et à assurer le trafic pour les utilisateurs avec une courte interruption. Pendant ce temps, l’instantané est remis au processus du chargeur de capture instantanée. Le chargeur de capture instantanée crée un minidump et le charge sur Application Insights, ainsi que tous les fichiers de symboles (.pdb) pertinents.

> [!TIP]
> - Une capture instantanée de processus est un clone suspendu du processus en cours d’exécution.
> - La création de l’instantané prend environ de 10 à 20 millisecondes.
> - La valeur par défaut de `ThresholdForSnapshotting` est 1. Il s’agit également la valeur minimale. Par conséquent, votre application doit déclencher la même exception **à deux reprises** avant qu’un instantané soit créé.
> - Définissez `IsEnabledInDeveloperMode` sur true si vous voulez générer des captures instantanées lors d’un débogage dans Visual Studio.
> - Le taux de création de capture instantanée est limité par le paramètre `SnapshotsPerTenMinutesLimit`. Par défaut, la limite est d’une capture instantanée toutes les dix minutes.
> - Au maximum 50 captures instantanées peuvent être chargées par jour.

## <a name="current-limitations"></a>Limitations actuelles

### <a name="publish-symbols"></a>Publier des symboles
Le débogueur de captures instantanées nécessite que des fichiers de symboles se trouvent sur le serveur de production pour le décodage des variables et pour fournir une fonctionnalité de débogage dans Visual Studio.
La version 15.2 de Visual Studio 2017 (ou ultérieure) publie les symboles des builds de mise en production par défaut lors de sa publication dans App Service. Dans les versions antérieures, vous devez ajouter la ligne suivante à votre fichier de profil de publication `.pubxml` afin que les symboles soient publiés en mode Mise en production :

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Pour Calcul Azure et d’autres types, vérifiez que les fichiers de symboles se trouvent dans le même dossier que le fichier .dll de l’application principale (généralement `wwwroot/bin`), ou sont disponibles dans le chemin d’accès actuel.

### <a name="optimized-builds"></a>Optimisation des versions
Dans certains cas, des variables locales sont invisibles dans les builds de mise en production en raison d’optimisations appliquées par le compilateur JIT.
Toutefois, dans Azure App Services, le collecteur de captures instantanées peut « désoptimiser » des méthodes de levée d’exception faisant partie de son Plan de collecte.

> [!TIP]
> Installez l’extension de site Application Insights dans votre App Service pour obtenir un support de « désoptimisation ».

## <a name="troubleshooting"></a>Résolution de problèmes

Les conseils suivants peuvent vous aider à résoudre des problèmes rencontrés avec le Débogueur de capture instantanée.

### <a name="use-the-snapshot-health-check"></a>Utiliser le contrôle d’intégrité de capture instantanée
Plusieurs problèmes courants empêchent l’affichage du message Ouvrir l’instantané de débogage. L’utilisation d’un collecteur de captures instantanées obsolète, par exemple lié à l’atteinte de la limite de chargement quotidienne, entraîne un temps de téléchargement important. Utilisez le contrôle d’intégrité de capture instantanée pour résoudre des problèmes courants.

Il existe un lien dans le volet d’exception de l’affichage de suivi de bout en bout, qui permet d’accéder au contrôle d’intégrité de capture instantanée.

![Entrer le contrôle d’intégrité de capture instantanée](./media/app-insights-snapshot-debugger/enter-snapshot-health-check.png)

L’interface interactive, de type conversation, recherche des problèmes courants et vous guide pour les résoudre.

![Contrôle d’intégrité](./media/app-insights-snapshot-debugger/healthcheck.png)

Si cela ne résout pas le problème, consultez les étapes de dépannage manuel suivantes.

### <a name="verify-the-instrumentation-key"></a>Vérifier la clé d’instrumentation

Assurez-vous que vous utilisez la clé d’instrumentation correcte dans votre application publiée. En règle générale, la clé d’instrumentation est lue à partir du fichier ApplicationInsights.config. Vérifiez que la valeur est identique à la clé d’instrumentation de la ressource Application Insights que vous voyez dans le portail.

### <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Mettre à niveau vers la dernière version du package NuGet

Utilisez le Gestionnaire de package NuGet de Visual Studio pour vous assurer que vous utilisez la dernière version de Microsoft.ApplicationInsights.SnapshotCollector. Les notes de publication sont disponibles dans la page https://github.com/Microsoft/ApplicationInsights-Home/issues/167.

### <a name="check-the-uploader-logs"></a>Vérifier les journaux du chargeur

Une fois une capture instantanée créée, un fichier minidump (.dmp) est créé sur le disque. Un processus distinct du chargeur crée ce fichier minidump et le charge, ainsi que tous les fichiers PDB associés, vers le stockage du Débogueur de capture instantanée d’Application Insights. Une fois le fichier minidump correctement chargé, il est supprimé du disque. Les fichiers journaux du processus de chargement sont conservés sur disque. Dans un environnement App Service, vous pouvez trouver ces fichiers journaux dans `D:\Home\LogFiles`. Le site de gestion Kudu pour App Service permet de rechercher ces fichiers journaux.

1. Ouvrez votre application App Service dans le portail Azure.
2. Cliquez sur **Outils avancés**, ou recherchez **Kudu**.
3. Cliquez sur **Atteindre**.
4. Dans le zone de liste déroulante de la **Console de débogage**, sélectionnez **CMD**.
5. Cliquez sur **LogFiles**.

Il devrait y avoir au moins un fichier dont le nom commence par `Uploader_` ou `SnapshotUploader_` et qui comporte l’extension `.log`. Cliquez sur l’icône appropriée pour télécharger tous les fichiers journaux ou les ouvrir dans un navigateur.
Le nom de fichier comporte un suffixe unique qui identifie l’instance App Service. Si votre instance App Service est hébergée sur plusieurs machines, il existe des fichiers journaux distincts pour chacune d’elles. Lorsque le chargeur détecte la présence d’un nouveau fichier minidump, celui-ci est enregistré dans le fichier journal. Voici un exemple de capture instantanée et de chargement réussis :

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> L’exemple ci-dessus concerne la version 1.2.0 du package NuGet Microsoft.ApplicationInsights.SnapshotCollector. Dans les versions antérieures, le processus de chargement est appelé `MinidumpUploader.exe` et le journal est moins détaillé.

Dans l’exemple précédent, la clé d’instrumentation est `c12a605e73c44346a984e00000000000`. Cette valeur doit correspondre à la clé d’instrumentation de votre application.
Le fichier minidump est associé à une capture instantanée dont l’ID est `139e411a23934dc0b9ea08a626db16c5`. Vous pouvez utiliser cet ID ultérieurement pour rechercher la télémétrie d’exception associée dans Application Insights - Analyses.

Le chargeur analyse les nouveaux fichiers PDB environ toutes les 15 minutes. Voici un exemple :

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Pour les applications qui ne sont _pas_ hébergées dans App Service, les fichiers journaux du chargeur figurent dans le même dossier que les fichiers minidump : `%TEMP%\Dumps\<ikey>` (où `<ikey>` est votre clé d’instrumentation).

### <a name="troubleshooting-cloud-services"></a>Dépannage de Cloud Services
Pour les rôles dans Cloud Services, le dossier temporaire par défaut peut être trop petit pour contenir les fichiers minidump, conduisant à des pertes d’instantanés.
L’espace nécessaire varie selon le jeu de travail total de votre application et le nombre d’instantanés simultanés.
Le jeu de travail du rôle web ASP.NET 32 bits contient en général de 200 à 500 Mo.
Autorisez au moins deux instantanés simultanés.
Par exemple, si votre application utilise un jeu de travail de 1 Go au total, vous devez vous assurer qu’il y a au moins 2 Go d’espace disque pour stocker les instantanés.
Suivez ces étapes pour configurer votre rôle service cloud avec une ressource locale dédiée pour les instantanés.

1. Ajoutez une nouvelle ressource locale à votre service cloud en modifiant le fichier de définition (.csdef) du service cloud. L’exemple suivant définit une ressource appelée `SnapshotStore` avec une taille de 5 Go.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Modifiez le code de démarrage de votre rôle pour ajouter une variable d’environnement qui pointe vers la ressource locale `SnapshotStore`. Dans le cas des rôles de travail, le code doit être ajouté à la méthode `OnStart` du rôle :
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   Dans le cas des rôles Web (ASP.NET), le code doit être ajouté à la méthode `Application_Start` de l’application web :
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Mettez à jour le fichier ApplicationInsights.config de votre rôle pour remplacer l’emplacement de dossier temporaire utilisé par `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

### <a name="overriding-the-shadow-copy-folder"></a>Remplacement du dossier des clichés instantanés

Lorsque le collecteur de captures instantanées démarre, il essaie de rechercher un dossier sur le disque convenant pour l’exécution du processus du chargeur des captures instantanées. Le dossier choisi est appelé dossier des clichés instantanés.

Le collecteur de captures instantanées vérifie quelques emplacements connus, en s’assurant qu’il dispose des autorisations pour copier les fichiers binaires du chargeur des captures instantanées. Les variables d'environnement suivantes sont utilisées :
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Si aucun dossier approprié n’est trouvé, le collecteur de captures instantanées signale un message d’erreur indiquant _« Impossible de trouver un dossier des clichés instantanées »._

Si la copie échoue, le collecteur de captures instantanées indique une erreur `ShadowCopyFailed`.

Si le chargeur ne peut pas être lancé, le collecteur de captures instantanées indique une erreur `UploaderCannotStartFromShadowCopy`. Le corps du message comprend souvent `System.UnauthorizedAccessException`. Cette erreur se produit généralement lorsque l’application est exécutée avec un compte disposant d’autorisations réduites. Le compte a l’autorisation d’écrire dans le dossier des clichés instantanés, mais il n’est pas autorisé à exécuter du code.

Étant donné que ces erreurs se produisent généralement lors du démarrage, elles seront généralement suivies d’une erreur `ExceptionDuringConnect` indiquant _« Échec du démarrage du chargeur »._

Pour contourner ces erreurs, vous pouvez spécifier manuellement le dossier des clichés instantanés avec l’option de configuration `ShadowCopyFolder`. Par exemple, en utilisant ApplicationInsights.config :

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

Ou bien, si vous utilisez appsettings.json avec une application .NET Core :

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

### <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Utilisez une recherche Application Insights pour trouver des exceptions avec des captures instantanées

Quand un instantané est créé, l’exception levée est marquée avec un ID d’instantané. Cet ID d’instantané est inclus en tant que propriété personnalisée lorsque la télémétrie d’exception est signalée à Application Insights. La fonction **Rechercher** dans Application Insights vous permet de trouver toute télémétrie avec la propriété personnalisée `ai.snapshot.id`.

1. Parcourez votre ressource Application Insights dans le portail Azure.
2. Cliquez sur **Rechercher**.
3. Tapez `ai.snapshot.id` dans la zone de texte Rechercher, puis appuyez sur Entrée.

![Rechercher une télémétrie avec un ID d’instantané dans le portail](./media/app-insights-snapshot-debugger/search-snapshot-portal.png)

Si cette recherche ne retourne aucun résultat, cela signifie qu’aucun instantané n’a été signalé à Application Insights pour votre application dans l’intervalle de temps sélectionné.

Pour rechercher un ID d’instantané spécifique dans les fichiers journaux du chargeur, tapez cet ID dans la zone de recherche. Si vous ne trouvez la télémétrie d’une capture instantanée dont vous savez qu’elle a été chargée, procédez comme suit :

1. Vérifiez soigneusement que vous examinez la ressource Application Insights appropriée en vérifiant la clé d’instrumentation.

2. À l’aide de l’horodateur du fichier journal du chargeur, ajustez le filtre Intervalle de temps de la recherche pour couvrir cet intervalle.

Si vous ne voyez toujours pas d’exception avec cet ID d’instantané, cela signifie que la télémétrie de l’exception n’a pas été signalée à Application Insights. Cette situation peut se produire si votre application s’est arrêtée anormalement après avoir pris la capture instantanée, mais avant d’avoir signalé la télémétrie de l’exception. Dans ce cas, vérifiez les journaux d’App Service sous `Diagnose and solve problems` pour voir si des redémarrages intempestifs ou des exceptions non prises en charge se sont produits.

### <a name="edit-network-proxy-or-firewall-rules"></a>Modifier les règles de pare-feu ou de proxy réseau

Si votre application se connecte à Internet via un proxy ou un pare-feu, il se peut que vous deviez modifier les règles pour autoriser votre application à communiquer avec le service Débogueur de capture instantanée. Voici la [liste des adresses IP et des ports utilisés par le Débogueur de capture instantanée](app-insights-ip-addresses.md#snapshot-debugger).

## <a name="next-steps"></a>Étapes suivantes

* [Définir des points de capture instantanée dans votre code](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) afin obtenir des captures instantanées sans attendre la levée d’une exception.
* [Diagnostiquer des exceptions dans vos applications web](app-insights-asp-net-exceptions.md) explique comment rendre visible à Application Insights un plus grand nombre d’exceptions.
* [Détection intelligente](app-insights-proactive-diagnostics.md) permet de détecter automatiquement les anomalies relatives aux performances.
