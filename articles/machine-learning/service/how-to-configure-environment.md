---
title: Configurer un environnement de développement pour Azure Machine Learning | Microsoft Docs
description: Découvrez comment configurer un environnement de développement quand vous travaillez avec le service Azure Machine Learning. Dans ce document, vous allez découvrir comment utiliser des environnements Conda, créer des fichiers de configuration et configurer des blocs-notes Jupyter, des blocs-notes Azure, des IDE, des éditeurs de code et Data Science Virtual Machine.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 8/6/2018
ms.openlocfilehash: 7796accffb7041e567c5e18857d09e105b5268ce
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961567"
---
# <a name="how-to-configure-a-development-environment-for-the-azure-machine-learning-service"></a>Guide pratique pour configurer un environnement de développement pour le service Azure Machine Learning

Découvrez comment configurer votre environnement de développement pour fonctionner avec le service Azure Machine Learning. Vous allez découvrir comment créer un fichier de configuration qui associe votre environnement à un espace de travail Azure Machine Learning. Vous apprendrez également à configurer les environnements de développement suivants :

* Blocs-notes Jupyter sur votre ordinateur
* Visual Studio Code
* Éditeur de code de votre choix

L’approche recommandée consiste à utiliser Continuum Anaconda [environnements virtuels conda](https://conda.io/docs/user-guide/tasks/manage-environments.html) pour isoler votre environnement de travail afin d’éviter les conflits de dépendance entre les packages. Cet article décrit les étapes de configuration d’un environnement conda et son utilisation pour Azure Machine Learning.


## <a name="prerequisites"></a>Prérequis

* Un espace de travail de service Microsoft Azure Machine Learning. Pour en créer un, effectuez les étapes décrites dans le document [Bien démarrer avec le service Azure Machine Learning](quickstart-get-started.md).

* Gestionnaire de package [Continuum Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://conda.io/miniconda.html).

 * Pour l’environnement Visual Studio Code, l’[Extension Python](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="create-workspace-configuration-file"></a>Créer un fichier de configuration d’espace de travail

Le fichier de configuration d’espace de travail est utilisé par le SDK pour communiquer avec votre espace de travail du service Azure Machine Learning.  Vous pouvez obtenir ce fichier de deux manières :

* Quand vous effectuez le [guide de démarrage rapide](quickstart-get-started.md), le fichier `config.json` est créé pour vous dans Azure Notebooks.  Ce fichier contient les informations de configuration pour votre espace de travail.  Téléchargez-le dans le même répertoire que les scripts ou les blocs-notes qui le référencent.

* Créez le fichier de configuration vous-même en effectuant les étapes suivantes :

    1. Ouvrez votre espace de travail dans le [portail Azure](https://portal.azure.com). Copie le __Nom de l’espace de travail__, le __Groupe de ressources__ et l’__ID de l’abonnement__. Ces valeurs servent à créer le fichier de configuration.

       Le tableau de bord de l’espace de travail du portail est pris en charge uniquement sur les navigateurs Edge, Chrome et Firefox.
    
        ![Portail Azure](./media/how-to-configure-environment/configure.png) 
    
    3. Dans un éditeur de texte, créez un fichier nommé **config.json**.  Ajoutez le contenu suivant à ce fichier, en insérant vos valeurs obtenues à partir du portail :
    
        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
    
        >[!NOTE] 
        >Plus loin dans votre code, vous lisez ce fichier avec :  `ws = Workspace.from_config()`
    
    4. Veillez à enregistrer **config.json** dans le même répertoire que les scripts ou les blocs-notes qui le référencent.
    
## <a name="azure-notebooks-and-data-science-virtual-machine"></a>Azure Notebooks et Data Science Virtual Machine

Azure Notebooks et Azure Data Science Virtual Machines (DSVM) sont préconfigurés pour fonctionner avec le service Azure Machine Learning. Les composants requis, tels que le SDK Azure Machine Learning, sont préinstallés sur ces environnements.

Azure Notebooks est un service de blocs-notes Jupyter dans le cloud Azure. Data Science Virtual Machine est une image de machine virtuelle qui est préconfigurée pour le travail de science des données. La machine virtuelle inclut des outils, des IDE et des packages populaires tels que Jupyter Notebook, PyCharm et Tensorflow.

Vous aurez quand même besoin d’un fichier de configuration d’espace de travail pour utiliser ces environnements.

Pour plus d’informations sur Data Science Virtual Machines, consultez la documentation [Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

Pour obtenir un exemple d’utilisation d’Azure Notebooks avec le service Azure Machine Learning, consultez le document [Bien démarrer avec le service Azure Machine Learning](quickstart-get-started.md).

## <a name="configure-jupyter-notebooks-on-your-own-computer"></a>Configurer des blocs-notes Jupyter sur votre ordinateur

1. Ouvrez une invite de commandes ou un interpréteur de commandes.

2. Pour créer un environnement conda, utilisez les commandes suivantes :

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

    La création de l’environnement peut prendre plusieurs minutes, car il peut être nécessaire de télécharger Python 3.6 et d’autres composants.

3. Pour installer le SDK Azure Machine Learning avec des fonctionnalités de blocs-notes supplémentaires, utilisez la commande suivante :

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl,contrib]
    ```

    L’installation du SDK peut prendre plusieurs minutes.

4. Pour installer les packages pour votre expérimentation de machine learning, utilisez la commande suivante et remplacez `<new package>` par le package que vous souhaitez installer :

    ```shell
    conda install <new package>
    ```

5. Pour installer un serveur Jupyter Notebook prenant en charge conda et activer des widgets d’expérimentation (afin de visualiser les informations sur les exécutions), utilisez les commandes suivantes :

    ```shell
    # install Jupyter 
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

6. Pour lancer Jupyter Notebook, utilisez la commande suivante :

    ```shell
    jupyter notebook
    ```

7. Ouvrez un nouveau bloc-notes, puis sélectionnez « myenv » comme noyau. Ensuite, vérifiez que le SDK Azure Machine Learning est installé en exécutant la commande suivante dans une cellule de bloc-notes :

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Configurer Visual Studio Code

1. Ouvrez une invite de commandes ou un interpréteur de commandes.

2. Pour créer un environnement conda, utilisez les commandes suivantes :

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

2. Pour installer le SDK Azure Machine Learning, utilisez la commande suivante :
 
    ```shell
    pip install --upgrade azureml-sdk[automl,contrib]
    ```

4. Pour installer Visual Studio code Tools for AI, consultez l’entrée [Tools for IA](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) sur la Place de marché Visual Studio. 

5. Pour installer les packages pour votre expérimentation de machine learning, utilisez la commande suivante et remplacez `<new package>` par le package que vous souhaitez installer :

    ```shell
    conda install <new package>
    ```

6. Lancez Visual Studio Code et utilisez __Ctrl-Maj-P__ pour accéder à la __Palette de commandes__. Entrez *Python: Select Interpreter*, puis sélectionnez l’environnement conda que vous avez créé.

    > [!NOTE]
    > Visual Studio Code prend automatiquement en charge les environnements conda sur votre ordinateur. Pour plus d’informations, consultez la [documentation de Visual Studio Code](https://code.visualstudio.com/docs/python/environments#_conda-environments).

7. Pour valider la configuration, utilisez Visual Studio Code afin de créer un fichier de script Python avec le code suivant, et exécutez-le :

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-code-editor-of-your-choice"></a>Configurer l’éditeur de code de votre choix

Pour utiliser un éditeur de code personnalisé avec le SDK Azure Machine Learning, créez d’abord un environnement conda comme décrit ci-dessus. Ensuite, suivez les instructions pour chaque éditeur afin d’utiliser l’environnement conda. Par exemple, les instructions relatives à PyCharm se trouvent à l’adresse [https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).
 
## <a name="next-steps"></a>Étapes suivantes

* [Entraîner un modèle sur Azure Machine Learning avec le jeu de données MNIST](tutorial-train-models-with-aml.md)
