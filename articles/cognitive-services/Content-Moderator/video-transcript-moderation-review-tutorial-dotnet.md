---
title: Azure Content Moderator - Modérer des vidéos et des transcriptions dans .NET | Microsoft Docs
description: Comment utiliser Content Moderator pour modérer des vidéos et des transcriptions dans .NET.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: 0f851c030a05880d79a998ed4b4a941082c057b9
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865469"
---
# <a name="video-and-transcript-moderation-tutorial"></a>Tutoriel de modération de vidéos et de transcriptions

Les API Vidéo de Content Moderator vous permettent de modérer des vidéos et de créer des révisions des vidéos dans l’outil de révision par un opérateur humain. 

Ce tutoriel détaillé permet de comprendre comment concevoir une solution de modération de vidéos et de transcriptions complète avec la modération assistée par ordinateur et la création de révisions pour une intervention humaine.

Téléchargez l’[application console C#](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) pour ce tutoriel. L’application console utilise le kit SDK et les packages associés pour effectuer les tâches suivantes :

- Compresser la ou les vidéos d’entrée pour un traitement plus rapide
- Modérer la vidéo pour obtenir des captures et des trames avec des insights
- Utiliser des horodatages de trame pour créer des miniatures (images)
- Soumettre des horodatages et des miniatures pour créer des révisions de vidéos
- Convertir la reconnaissance vocale (transcription) de vidéos avec l’API d’indexeur multimédia
- Modérer la transcription avec le service de modération de texte
- Ajouter la transcription modérée à la révision de la vidéo

## <a name="sample-program-outputs"></a>Exemple de sorties du programme

Avant de continuer, examinons les exemples de sorties du programme suivantes :

- [Sortie de la console](#program-output)
- [Révision de vidéo](#video-review-default-view)
- [Vue Transcription](#video-review-transcript-view)

## <a name="prerequisites"></a>Prérequis

1. Inscrivez-vous au site web de l’[outil de révision Content Moderator](https://contentmoderator.cognitive.microsoft.com/) et [créez des balises personnalisées](Review-Tool-User-Guide/tags.md) que l’application console C# attribue à partir du code. L’écran suivant présente les balises personnalisées.

  ![Balises personnalisées de modération de vidéos](images/video-tutorial-custom-tags.png)

1. Pour exécuter l’exemple d’application, vous devez disposer d’un compte Azure et d’un compte Azure Media Services. De plus, vous devez avoir accès à la préversion privée de Content Moderator. Enfin, vous avez besoin d’informations d’identification d’authentification Azure Active Directory. Pour plus d’informations sur l’obtention de ces informations, consultez [Démarrage rapide de l’API Modération des vidéos](video-moderation-api.md).

1. Modifiez le fichier `App.config` et ajoutez le nom du locataire Active Directory, les points de terminaison de service et les clés d’abonnement indiquées par `#####`. Vous avez besoin des informations suivantes :

|Clé|Description|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Point de terminaison pour l’API Azure Media Services (AMS)|
|`ClientSecret`|Clé d’abonnement pour Azure Media Services|
|`ClientId`|ID client pour Azure Media Services|
|`AzureAdTenantName`|Nom du locataire Active Directory représentant votre organisation|
|`ContentModeratorReviewApiSubscriptionKey`|Clé d’abonnement pour l’API de révision Content Moderator|
|`ContentModeratorApiEndpoint`|Point de terminaison pour l’API Content Moderator|
|`ContentModeratorTeamId`|ID d’équipe Content Moderator|

## <a name="getting-started"></a>Prise en main

La classe `Program` de `Program.cs` est le point d’entrée principal de l’application de modération des vidéos.

### <a name="methods-of-class-program"></a>Méthodes de la classe Program

|Méthode|Description|
|-|-|
|`Main`|Analyse la ligne de commande, collecte l’entrée utilisateur et commence le traitement.|
|`ProcessVideo`|Compresse, charge, modère et crée des révisions de vidéos.|
|`CreateVideoStreamingRequest`|Crée un flux pour charger une vidéo.|
|`GetUserInputs`|Collecte l’entrée utilisateur ; utilisée quand aucune option de ligne de commande n’est présente.|
|`Initialize`|Initialise les objets nécessaires pour le processus de modération.|

### <a name="the-main-method"></a>La méthode Main

`Main()` est l’emplacement où commence l’exécution. C’est donc par ce point qu’il faut commencer pour comprendre le processus de modération des vidéos.

    static void Main(string[] args)
    {
        if (args.Length == 0)
        {
            string videoPath = string.Empty;
            GetUserInputs(out videoPath);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(Path.GetDirectoryName(videoPath), "log.txt");
            try
            {
                ProcessVideo(videoPath).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
        else
        {
            DirectoryInfo directoryInfo = new DirectoryInfo(args[0]);
            if (args.Length == 2)
                bool.TryParse(args[1], out generateVtt);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(args[0], "log.txt");
            var files = directoryInfo.GetFiles("*.mp4", SearchOption.AllDirectories);
            foreach (var file in files)
            {
                try
                {
                    ProcessVideo(file.FullName).Wait();
                }
                catch (Exception ex)
                {
                    Console.WriteLine(ex.Message);
                }
            }
        }
    }

`Main()` gère les arguments de ligne de commande suivants :

- Le chemin d’un répertoire contenant des fichiers vidéo MPEG-4 à soumettre à la modération. Tous les fichiers `*.mp4` présents dans ce répertoire et ses sous-répertoires sont soumis à la modération.
- Éventuellement, un indicateur booléen (true/false) spécifiant si les transcriptions de texte doivent être générées pour les besoins d’une modération de données audio.

Si aucun argument de ligne de commande n’est présent, `Main()` appelle `GetUserInputs()`. Cette méthode invite l’utilisateur à entrer le chemin d’un seul fichier vidéo et à spécifier si une transcription de texte doit être générée.

> [!NOTE]
> L’application console utilise l’[API Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) pour générer des transcriptions à partir de la piste audio de la vidéo chargée. Les résultats sont fournis au format WebVTT. Pour plus d’informations sur ce format, consultez la rubrique relative au [format WebVTT (Web Video Text Tracks)](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).

### <a name="initialize-and-processvideo-methods"></a>Méthodes Initialize et ProcessVideo

Que les options du programme proviennent de la ligne de commande ou d’une entrée utilisateur interactive, `Main()` appelle ensuite `Initialize()` pour créer les instances suivantes :

|Classe|Description|
|-|-|
|`AMSComponent`|Compresse les fichiers vidéo avant de les soumettre à la modération.|
|`AMSconfigurations`|Interface pour les données de configuration de l’application, se trouvant dans `App.config`.|
|`VideoModerator`| Chargement, encodage, chiffrement et modération à l’aide du kit SDK AMS|
|`VideoReviewApi`|Gère les révisions des vidéos dans le service Content Moderator|

Ces classes (hormis `AMSConfigurations`, qui est simple) sont traitées plus en détail dans les prochaines sections de ce tutoriel.

Enfin, les fichiers vidéo sont traités un par un en appelant `ProcessVideo()` pour chacun.

    private static async Task ProcessVideo(string videoPath)
    {
        Stopwatch sw = new Stopwatch();
        sw.Start();
        Console.ForegroundColor = ConsoleColor.White;
        Console.WriteLine("\nVideo compression process started...");

        var compressedVideoPath = amsComponent.CompressVideo(videoPath);
        if (string.IsNullOrWhiteSpace(compressedVideoPath))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Compression failed.");
        }

        Console.WriteLine("\nVideo compression process completed...");

        UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
        UploadAssetResult uploadResult = new UploadAssetResult();

        if (generateVtt)
        {
            uploadResult.GenerateVTT = generateVtt;
        }
        Console.WriteLine("\nVideo moderation process started...");

        if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Review process failed.");
        }

        Console.WriteLine("\nVideo moderation process completed...");
        Console.WriteLine("\nVideo review process started...");
        string reviewId = await videoReviewApi.CreateVideoReviewInContentModerator(uploadResult);
        Console.WriteLine("\nVideo review successfully completed...");
        sw.Stop();
        Console.WriteLine("\nTotal Elapsed Time: {0}", sw.Elapsed);
        using (var stw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            stw.WriteLine("Video File Name: " + Path.GetFileName(videoPath));
            stw.WriteLine($"ReviewId: {reviewId}");
            stw.WriteLine("Total Elapsed Time: {0}", sw.Elapsed);
        }
    }


La méthode `ProcessVideo()` est assez simple. Elle effectue les opérations suivantes, dans l’ordre :

- Compresse la vidéo
- Charge la vidéo dans une ressource Azure Media Services
- Crée une tâche AMS pour modérer la vidéo
- Crée une révision de vidéo dans Content Moderator

Les sections suivantes examinent plus en détail certains des différents processus appelés par `ProcessVideo()`. 

## <a name="compressing-the-video"></a>Compression de la vidéo

Pour réduire le trafic réseau, l’application convertit les fichiers vidéo au format H.264 (MPEG-4 AVC) et les met à l’échelle à une largeur maximale de 640 pixels. Le codec H.264 est recommandé en raison de sa haute efficacité (taux de compression). La compression est effectuée à l’aide de l’outil en ligne de commande gratuit `ffmpeg`, qui est inclus dans le dossier `Lib` de la solution Visual Studio. Les fichiers d’entrée peuvent être de n’importe quel format pris en charge par `ffmpeg`, ce qui inclut les codecs et formats de fichiers vidéo es plus fréquemment utilisés.

> [!NOTE]
> Quand vous démarrez le programme à l’aide d’options de ligne de commande, vous spécifiez un répertoire contenant les fichiers vidéo à soumettre à la modération. Tous les fichiers de ce répertoire ayant l’extension de nom de fichier `.mp4` sont traités. Pour traiter d’autres extensions de nom de fichier, mettez à jour la méthode `Main()` dans `Program.cs` pour inclure les extensions souhaitées.

Le code qui compresse un seul fichier vidéo est la classe `AmsComponent` dans `AMSComponent.cs`. La méthode chargée de cette fonctionnalité est `CompressVideo()`, illustrée ici.

    public string CompressVideo(string videoPath)
    {
        string ffmpegBlobUrl;
        if (!ValidatePreRequisites())
        {
            Console.WriteLine("Configurations check failed. Please cross check the configurations!");
            throw new Exception();
        }

        if (File.Exists(_configObj.FfmpegExecutablePath))
        {
            ffmpegBlobUrl = this._configObj.FfmpegExecutablePath;
        }
        else
        {
            Console.WriteLine("ffmpeg.exe is missing. Please check the Lib folder");
            throw new Exception();
        }

        string videoFilePathCom = videoPath.Split('.')[0] + "_c.mp4";
        ProcessStartInfo processStartInfo = new ProcessStartInfo();
        processStartInfo.WindowStyle = ProcessWindowStyle.Hidden;
        processStartInfo.FileName = ffmpegBlobUrl;
        processStartInfo.Arguments = "-i \"" + videoPath + "\" -vcodec libx264 -n -crf 32 -preset veryfast -vf scale=640:-1 -c:a aac -aq 1 -ac 2 -threads 0 \"" + videoFilePathCom + "\"";
        var process = Process.Start(processStartInfo);
        process.WaitForExit();
        process.Close();
        return videoFilePathCom;
    }

Le code effectue les étapes suivantes :

- Vérifie que la configuration dans `App.config` contient toutes les données nécessaires
- Vérifie que le binaire `ffmpeg` est présent
- Génère le nom de fichier de sortie en ajoutant `_c.mp4` au nom de base du fichier (comme `Example.mp4` -> `E>xample_c.mp4`)
- Génère une chaîne de ligne de commande pour effectuer la conversion
- Démarre un processus `ffmpeg` à l’aide de la ligne de commande
- Attend que la vidéo soit traitée

> [!NOTE]
> Si vous savez que vos vidéos sont déjà compressées avec H.264 et que vous avez les dimensions appropriées, vous pouvez réécrire `CompressVideo()` pour ignorer la compression.

La méthode retourne le nom de fichier du fichier de sortie compressé.

## <a name="uploading-and-moderating-the-video"></a>Chargement et modération de la vidéo

La vidéo doit être stockée dans Azure Media Services pour pouvoir être traitée par le service Content Moderation. La classe `Program` dans `Program.cs` a une courte méthode `CreateVideoStreamingRequest()` qui retourne un objet représentant la requête de streaming utilisée pour charger la vidéo.

    private static UploadVideoStreamRequest CreateVideoStreamingRequest(string compressedVideoFilePath)
    {
        return
            new UploadVideoStreamRequest
            {
                VideoStream = File.ReadAllBytes(compressedVideoFilePath),
                VideoName = Path.GetFileName(compressedVideoFilePath),
                EncodingRequest = new EncodingRequest()
                {
                    EncodingBitrate = AmsEncoding.AdaptiveStreaming
                },
                VideoFilePath = compressedVideoFilePath
            };
    }

L’objet `UploadVideoStreamRequest` obtenu est défini dans `UploadVideoStreamRequest.cs` (et son parent, `UploadVideoRequest`, dans `UploadVideoRequest.cs`). Ces classes ne sont pas affichées ici ; elles sont brèves et servent uniquement à contenir les données vidéo compressées et des informations les concernant. Une autre classe de données uniquement, `UploadAssetResult` (`UploadAssetResult.cs`) est utilisée pour contenir les résultats du processus de chargement. Il est désormais possible de comprendre les lignes suivantes dans `ProcessVideo()` :

    UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
    UploadAssetResult uploadResult = new UploadAssetResult();

    if (generateVtt)
    {
        uploadResult.GenerateVTT = generateVtt;
    }
    Console.WriteLine("\nVideo moderation process started...");

    if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine("Video Review process failed.");
    }

Ces lignes effectuent les tâches suivantes :

- Créent un `UploadVideoStreamRequest` pour charger la vidéo compressée
- Définissent l’indicateur `GenerateVTT` de la requête si l’utilisateur a demandé une transcription de texte
- Appellent `CreateAzureMediaServicesJobToModerateVideo()` pour effectuer le chargement et recevoir le résultat

## <a name="deep-dive-into-video-moderation"></a>Présentation approfondie de la modération des vidéos

La méthode `CreateAzureMediaServicesJobToModerateVideo()` se trouve dans `VideoModerator.cs`, qui contient la majeure partie du code qui interagit avec Azure Media Services. Le code source de la méthode est présenté dans l’extrait suivant.

    public bool CreateAzureMediaServicesJobToModerateVideo(UploadVideoStreamRequest uploadVideoRequest, UploadAssetResult uploadResult)
    {
        asset = CreateAsset(uploadVideoRequest);
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        // Encoding the asset , Moderating the asset, Generating transcript in parallel
        IAsset encodedAsset = null;
        //Creates the job for the tasks.
        IJob job = this._mediaContext.Jobs.Create("AMS Review Job");

        //Adding encoding task to job.
        ConfigureEncodeAssetTask(uploadVideoRequest.EncodingRequest, job);

        ConfigureContentModerationTask(job);

        //adding transcript task to job.
        if (uploadResult.GenerateVTT)
        {
            ConfigureTranscriptTask(job);
        }

        Stopwatch timer = new Stopwatch();
        timer.Start();
        //submit and execute job.
        job.Submit();
        job.GetExecutionProgressTask(new CancellationTokenSource().Token).Wait();
        timer.Stop();
        using (var sw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            sw.WriteLine("AMS Job Elapsed Time: {0}", timer.Elapsed);
        }

        if (job.State == JobState.Error)
        {
            throw new Exception("Video moderation has failed due to AMS Job error.");
        }

        UploadAssetResult result = uploadResult;
        encodedAsset = job.OutputMediaAssets[0];
        result.ModeratedJson = GetCmDetail(job.OutputMediaAssets[1]);
        // Check for valid Moderated JSON
        var jsonModerateObject = JsonConvert.DeserializeObject<VideoModerationResult>(result.ModeratedJson);

        if (jsonModerateObject == null)
        {
            return false;
        }
        if (uploadResult.GenerateVTT)
        {
            GenerateTranscript(job.OutputMediaAssets.Last());
        }

        uploadResult.StreamingUrlDetails = PublishAsset(encodedAsset);
        string downloadUrl = GenerateDownloadUrl(asset, uploadVideoRequest.VideoName);
        uploadResult.StreamingUrlDetails.DownloadUri = downloadUrl;
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        uploadResult.VideoFilePath = uploadVideoRequest.VideoFilePath;
        return true;
    }

Ce code effectue les tâches suivantes :

- Crée une tâche AMS pour le traitement à effectuer
- Ajoute des tâches pour l’encodage du fichier vidéo, sa modération et la génération d’une transcription de texte
- Soumet la tâche, en chargeant le fichier et en commençant le traitement
- Récupère les résultats de la modération, la transcription de texte (si nécessaire) et d’autres informations

## <a name="sample-video-moderation-response"></a>Exemple de réponse de modération de vidéo

Le résultat de la tâche de modération des vidéos (consultez le guide de [démarrage rapide de la modération des vidéos](video-moderation-api.md) est une structure de données JSON contenant les résultats de la modération. Ces résultats incluent une répartition des fragments (captures) dans la vidéo, chacun contenant des événements (clips) avec des trames clés qui ont été marquées pour révision. Chaque trame clé est évaluée selon la probabilité qu’elle contienne du contenu pour adultes ou osé. L’exemple suivant présent une réponse JSON :

    {
        "version": 2,
        "timescale": 90000,
        "offset": 0,
        "framerate": 50,
        "width": 1280,
        "height": 720,
        "totalDuration": 18696321,
        "fragments": [
        {
            "start": 0,
            "duration": 18000
        },
        {
            "start": 18000,
            "duration": 3600,
            "interval": 3600,
            "events": [
            [
            {
                "reviewRecommended": false,
                "adultScore": 0.00001,
                "racyScore": 0.03077,
                "index": 5,
                "timestamp": 18000,
                "shotIndex": 0
            }
            ]
        ]
        },
        {
            "start": 18386372,
            "duration": 119149,
            "interval": 119149,
            "events": [
            [
            {
                "reviewRecommended": true,
                "adultScore": 0.00000,
                "racyScore": 0.91902,
                "index": 5085,
                "timestamp": 18386372,
                "shotIndex": 62
            }
        ]
        ]
        }
    ]
    }

Une transcription des données audio de la vidéo est également générée quand l’indicateur `GenerateVTT` est défini.

> [!NOTE]
> L’application console utilise l’[API Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) pour générer des transcriptions à partir de la piste audio de la vidéo chargée. Les résultats sont fournis au format WebVTT. Pour plus d’informations sur ce format, consultez la rubrique relative au [format WebVTT (Web Video Text Tracks)](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API).


## <a name="creating-the-human-in-the-loop-review"></a>Création de la révision pour une intervention humaine

Le processus de modération retourne une liste de trames clés à partir de la vidéo, ainsi qu’une transcription de ses pistes audio. L’étape suivante consiste à créer une révision dans l’outil de révision Content Moderator pour les modérateurs humains. Concernant la méthode `ProcessVideo()` dans `Program.cs`, vous voyez l’appel à la méthode `CreateVideoReviewInContentModerator()`. Cette méthode, illustrée ici, se trouve dans la classe `videoReviewApi`, qui est dans `VideoReviewAPI.cs`.

    public async Task<string> CreateVideoReviewInContentModerator(UploadAssetResult uploadAssetResult)
    {
    
        string reviewId = string.Empty;
        List<ProcessedFrameDetails> frameEntityList = framegenerator.CreateVideoFrames(uploadAssetResult);
        string path = uploadAssetResult.GenerateVTT == true ? this._amsConfig.FfmpegFramesOutputPath + Path.GetFileNameWithoutExtension (uploadAssetResult.VideoName) + "_aud_SpReco.vtt" : "";
        TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult();
        
    if (File.Exists(path))
        {
            screenTextResult = await GenerateTextScreenProfanity(reviewId, path, frameEntityList);
            uploadAssetResult.Category1TextScore = screenTextResult.Category1Score;
            uploadAssetResult.Category2TextScore = screenTextResult.Category2Score;
            uploadAssetResult.Category3TextScore = screenTextResult.Category3Score;
            uploadAssetResult.Category1TextTag = screenTextResult.Category1Tag;
            uploadAssetResult.Category2TextTag = screenTextResult.Category2Tag;
            uploadAssetResult.Category3TextTag = screenTextResult.Category3Tag;
        }
        
        var reviewVideoRequestJson = CreateReviewRequestObject(uploadAssetResult, frameEntityList);
        if (string.IsNullOrWhiteSpace(reviewVideoRequestJson))
        {
            throw new Exception("Video review process failed in CreateVideoReviewInContentModerator");
        }
        
        reviewId = JsonConvert.DeserializeObject<List<string>>(ExecuteCreateReviewApi(reviewVideoRequestJson).Result).FirstOrDefault();
        frameEntityList = framegenerator.GenerateFrameImages(frameEntityList, uploadAssetResult, reviewId);
        await CreateAndPublishReviewInContentModerator(uploadAssetResult, frameEntityList, reviewId, path, screenTextResult);
        return reviewId;
    
    }

`CreateVideoReviewInContentModerator()` appelle plusieurs autres méthodes pour effectuer les tâches suivantes :

> [!NOTE]
> L’application console utilise la bibliothèque [FFmpeg](https://ffmpeg.org/) pour générer des miniatures. Ces miniatures (images) correspondent aux horodatages de trame dans la [sortie de la modération des vidéos](#sample-video-moderation-response).

|Tâche|Méthodes|Fichier|
|-|-|-|
|Extraire les trames clés à partir de la vidéo et créer des images miniatures de celles-ci|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Analyser la transcription de texte, le cas échéant, pour localiser du contenu audio pour adultes ou osé|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Préparer et soumettre une requête de révision de vidéo pour l’inspection par un opérateur humain|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Vue par défaut de la révision de vidéo

L’écran suivant montre les résultats des étapes précédentes.

![Vue par défaut de la révision de vidéo](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Génération de la transcription

Jusqu’à présent, le code présenté dans ce tutoriel se concentre sur le contenu visuel. La révision de contenu vocal est un processus distinct et facultatif qui, comme indiqué, utilise une transcription générée à partir des données audio. Il est maintenant temps d’examiner la façon dont les transcriptions de texte sont créées et utilisées dans le processus de révision. La tâche de génération de la transcription incombe au service [Azure Media Indexer](https://docs.microsoft.com/azure/media-services/media-services-index-content).

L’application effectue les tâches suivantes :

|Tâche|Méthodes|Fichier|
|-|-|-|
|Déterminer si des transcriptions de texte doivent être générées|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Si c’est le cas, soumettre une tâche de transcription dans le cadre de la modération|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Obtenir une copie locale de la transcription|`GenerateTranscript()`|`VideoModerator.cs`|
|Marquer les trames de la vidéo qui contiennent des données audio inappropriées|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Ajouter les résultats à la révision|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Configuration de la tâche

Passons directement à la soumission de la tâche de transcription. `CreateAzureMediaServicesJobToModerateVideo()` (déjà décrite) appelle `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

La configuration de la tâche de transcription est lue dans le fichier `MediaIndexerConfig.json` situé dans le dossier `Lib` de la solution. Les ressources AMS sont créées pour le fichier de configuration et pour la sortie du processus de transcription. Quand la tâche AMS s’exécute, elle crée une transcription de texte à partir de la piste audio du fichier vidéo.

> [!NOTE]
> L’exemple d’application procède à la reconnaissance vocale en anglais américain uniquement.

### <a name="transcript-generation"></a>Génération de la transcription

La transcription est publiée sous la forme d’une ressource AMS. Pour analyser la transcription de contenu répréhensible, l’application télécharge la ressource à partir d’Azure Media Services. `CreateAzureMediaServicesJobToModerateVideo()` appelle `GenerateTranscript()`, illustrée ici, pour récupérer le fichier.

    public bool GenerateTranscript(IAsset asset)
    {
        try
        {
            var outputFolder = this._amsConfigurations.FfmpegFramesOutputPath;
            IAsset outputAsset = asset;
            IAccessPolicy policy = null;
            ILocator locator = null;
            policy = _mediaContext.AccessPolicies.Create("My 30 days readonly policy", TimeSpan.FromDays(360), AccessPermissions.Read);
            locator = _mediaContext.Locators.CreateLocator(LocatorType.Sas, outputAsset, policy, DateTime.UtcNow.AddMinutes(-5));
            DownloadAssetToLocal(outputAsset, outputFolder);
            locator.Delete();
            return true;
        }
        catch
        {   //TODO:  Logging
            Console.WriteLine("Exception occured while generating index for video.");
            throw;
        }
    }

Après une configuration nécessaire d’AMS, le téléchargement réel s’effectue en appelant `DownloadAssetToLocal()`, une fonction générique qui copie une ressource AMS dans un fichier local.

## <a name="transcript-moderation"></a>Modération de la transcription

Avec la transcription à proximité, elle est analysée et utilisée dans la révision. La création de la révision est du ressort de `CreateVideoReviewInContentModerator()`, qui appelle `GenerateTextScreenProfanity()` pour effectuer cette opération. À son tour, cette méthode appelle `TextScreen()`, qui contient la plupart des fonctionnalités. 

`TextScreen()` effectue les tâches suivantes :

- Analyser la transcription pour rechercher les horodatages et les sous-titres
- Soumettre chaque sous-titre à la modération de texte
- Indiquer toutes les trames qui peuvent avoir du contenu vocal répréhensible

Examinons plus en détail chacune de ces tâches :

### <a name="initialize-the-code"></a>Initialiser le code

Tout d’abord, initialisez toutes les variables et collections.

    private async Task<TranscriptScreenTextResult> TextScreen(string filepath, List<ProcessedFrameDetails> frameEntityList)
    {
        List<TranscriptProfanity> profanityList = new List<TranscriptProfanity>();
        string responseContent = string.Empty;
        HttpResponseMessage response;
        bool category1Tag = false;
        bool category2Tag = false;
        bool category3Tag = false;
        double category1Score = 0;
        double category2Score = 0;
        double category3Score = 0;
        List<string> vttLines = File.ReadAllLines(filepath).Where(line => !line.Contains("NOTE Confidence:") && line.Length > 0).ToList();
        StringBuilder sb = new StringBuilder();
        List<CaptionScreentextResult> csrList = new List<CaptionScreentextResult>();
        CaptionScreentextResult captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };

        // Code from the next sections in the tutorial
    

### <a name="parse-the-transcript-for-captions"></a>Analyser la transcription pour rechercher les sous-titres

Ensuite, analysez la transcription au format VTT pour rechercher les sous-titres et les horodatages. L’outil de révision affiche ces sous-titres sous l’onglet Transcription sur l’écran de révision des vidéos. Les horodatages sont utilisés pour synchroniser les sous-titres avec les trames vidéo correspondantes.

        // Code from the previous section(s) in the tutorial

        //
        // Parse the transcript
        //
        foreach (var line in vttLines.Skip(1))
        {
                if (line.Contains("-->"))
                {
                    if (sb.Length > 0)
                    {
                        captionScreentextResult.Captions.Add(sb.ToString());
                        sb.Clear();
                    }
                    if (captionScreentextResult.Captions.Count > 0)
                    {
                        csrList.Add(captionScreentextResult);
                        captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };
                    }
                    string[] times = line.Split(new string[] { "-->" }, StringSplitOptions.RemoveEmptyEntries);
                    string startTimeString = times[0].Trim();
                    string endTimeString = times[1].Trim();
                    int startTime = (int)TimeSpan.ParseExact(startTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    int endTime = (int)TimeSpan.ParseExact(endTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    captionScreentextResult.StartTime = startTime;
                    captionScreentextResult.EndTime = endTime;
                }
                else
                {
                    sb.Append(line);
                }
                if (sb.Length + line.Length > 1024)
                {
                    captionScreentextResult.Captions.Add(sb.ToString());
                    sb.Clear();
                }
            }
            if (sb.Length > 0)
            {
                captionScreentextResult.Captions.Add(sb.ToString());
            }
            if (captionScreentextResult.Captions.Count > 0)
            {
                csrList.Add(captionScreentextResult);
            }

            // Code from the following section in the quickstart

### <a name="moderate-captions-with-the-text-moderation-service"></a>Modérer les sous-titres avec le service de modération de texte

Ensuite, nous analysons les sous-titres de texte décryptés avec l’API de texte de Content Moderator.

> [!NOTE]
> Votre clé de service Content Moderator a une limite de fréquence des demandes par seconde (RPS). Si vous dépassez la limite, le kit SDK lève une exception avec le code d’erreur 429. 
>
> Une clé de niveau gratuit a une limite de fréquence d’une demande par seconde (RSP).

    //
    // Moderate the captions or cues
    //
    int waitTime = 1000;
    foreach (var csr in csrList)
    {
                bool captionAdultTextTag = false;
                bool captionRacyTextTag = false;
                bool captionOffensiveTextTag = false;
                bool retry = true;

                foreach (var caption in csr.Captions)
                {
                    while (retry)
                    {
                        try
                        {
                            System.Threading.Thread.Sleep(waitTime);
                            var lang = await CMClient.TextModeration.DetectLanguageAsync("text/plain", caption);
                            var oRes = await CMClient.TextModeration.ScreenTextWithHttpMessagesAsync(lang.DetectedLanguageProperty, "text/plain", caption, null, null, null, true);
                            response = oRes.Response;
                            responseContent = await response.Content.ReadAsStringAsync();
                            retry = false;
                        }
                        catch (Exception e)
                        {
                            if (e.Message.Contains("429"))
                            {
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                                waitTime = (int)(waitTime * 1.5);
                                Console.WriteLine($"wait time: {waitTime}");
                            }
                            else
                            {
                                retry = false;
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                            }
                        }
                    }
                    var jsonTextScreen = JsonConvert.DeserializeObject<TextScreen>(responseContent);
                    if (jsonTextScreen != null)
                    {
                        TranscriptProfanity transcriptProfanity = new TranscriptProfanity();
                        transcriptProfanity.TimeStamp = "";
                        List<Terms> transcriptTerm = new List<Terms>();
                        if (jsonTextScreen.Terms != null)
                        {
                            foreach (var term in jsonTextScreen.Terms)
                            {
                                var profanityobject = new Terms
                                {
                                    Term = term.Term,
                                    Index = term.Index
                                };
                                transcriptTerm.Add(profanityobject);
                            }
                            transcriptProfanity.Terms = transcriptTerm;
                            profanityList.Add(transcriptProfanity);
                        }
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) captionAdultTextTag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) captionRacyTextTag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) captionOffensiveTextTag = true;
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) category1Tag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) category2Tag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) category3Tag = true;
                        category1Score = jsonTextScreen.Classification.Category1.Score > category1Score ? jsonTextScreen.Classification.Category1.Score : category1Score;
                        category2Score = jsonTextScreen.Classification.Category2.Score > category2Score ? jsonTextScreen.Classification.Category2.Score : category2Score;
                        category3Score = jsonTextScreen.Classification.Category3.Score > category3Score ? jsonTextScreen.Classification.Category3.Score : category3Score;
                    }
                    foreach (var frame in frameEntityList.Where(x => x.TimeStamp >= csr.StartTime && x.TimeStamp <= csr.EndTime))
                    {
                        frame.IsAdultTextContent = captionAdultTextTag;
                        frame.IsRacyTextContent = captionRacyTextTag;
                        frame.IsOffensiveTextContent = captionOffensiveTextTag;
                    }
                }
            }
            TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult()
            {
                TranscriptProfanity = profanityList,
                Category1Tag = category1Tag,
                Category2Tag = category2Tag,
                Category3Tag = category3Tag,
                Category1Score = category1Score,
                Category2Score = category2Score,
                Category3Score = category3Score
            };
            return screenTextResult;
    }

### <a name="breaking-down-the-text-moderation-step"></a>Décomposition de l’étape de modération du texte

`TextScreen()` est une méthode importante. Nous allons donc la décomposer.

1. Tout d’abord, la méthode lit le fichier de transcription ligne par ligne. Elle ignore les lignes vides et les lignes contenant une valeur `NOTE` avec un score de confiance. Elle extrait les horodatages et les éléments de texte à partir des *répliques* contenues dans le fichier. Une réplique représente le texte de la piste audio et inclut les heures de début et de fin. Une réplique commence à la ligne d’horodatage comprenant la chaîne `-->`. Elle est suivie d’une ou de plusieurs lignes de texte.

1. Les instances de `CaptionScreentextResult` (définies dans `TranscriptProfanity.cs`) sont utilisées pour contenir les informations analysées à partir de chaque réplique.  Quand une nouvelle ligne d’horodatage est détectée, ou qu’une longueur de texte maximale de 1 024 caractères est atteinte, un nouveau `CaptionScreentextResult` est ajouté au `csrList`. 

1. La méthode soumet ensuite chaque réplique à l’API Modération de texte. Elle appelle `ContentModeratorClient.TextModeration.DetectLanguageAsync()` et `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, qui sont définies dans l’assembly `Microsoft.Azure.CognitiveServices.ContentModerator`. Pour éviter d’avoir une fréquence limitée, la méthode fait une pause d’une seconde avant de soumettre chaque réplique.

1. Après avoir reçu les résultats du service de modération de texte, la méthode les analyse pour voir s’ils atteignent les seuils de confiance. Ces valeurs sont établies dans `App.config` en tant que `OffensiveTextThreshold`, `RacyTextThreshold` et `AdultTextThreshold`. Enfin, les termes choquants sont eux-mêmes également stockés. Toutes les trames dans l’intervalle de temps de la réplique sont marquées comme contenant du texte offensant, osé et/ou pour adultes.

1. `TextScreen()` retourne une instance de `TranscriptScreenTextResult` qui contient le résultat de la modération du texte provenant de la vidéo dans son ensemble. Cet objet contient des indicateurs et des scores pour les différents types de contenus répréhensibles, ainsi qu’une liste de tous les termes choquants. L’appelant, `CreateVideoReviewInContentModerator()`, appelle `UploadScreenTextResult()` pour lier ces informations à la révision afin qu’elles soient disponible pour les réviseurs humains.
 
## <a name="video-review-transcript-view"></a>Vue de transcription de la révision de vidéo

L’écran suivant montre le résultat de la génération de la transcription et les étapes de la modération.

![Vue de transcription de la modération de vidéo](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Sortie du programme

La sortie de la ligne de commande suivante du programme montre les différentes tâches à mesure qu’elles sont terminées. De plus, le résultat de la modération (au format JSON) et la transcription vocale sont disponibles dans le même répertoire que les fichiers vidéo d’origine.

    Microsoft.ContentModerator.AMSComponentClient
    Enter the fully qualified local path for Uploading the video :
    "Your File Name.MP4"
    Generate Video Transcript? [y/n] : y
    
    Video compression process started...
    Video compression process completed...
    
    Video moderation process started...
    Video moderation process completed...
    
    Video review process started...
    Video Frames Creation inprogress...
    Frames(83) created successfully.
    Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
    Video review successfully completed...
    
    Total Elapsed Time: 00:05:56.8420355


## <a name="next-steps"></a>Étapes suivantes

[Téléchargez la solution Visual Studio](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp), ainsi que des exemples de fichiers et les bibliothèques nécessaires pour ce tutoriel, puis commencez votre intégration.
