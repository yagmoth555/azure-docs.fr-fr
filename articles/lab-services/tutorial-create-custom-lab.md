---
title: Créer un laboratoire à l’aide de Azure DevTest Labs | Microsoft Docs
description: Dans ce guide de démarrage rapide, vous créez un laboratoire à l’aide de Azure DevTest Labs.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 5a93feec7996fc0ebf742b8d62b159dca5f1c1ed
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636985"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs"></a>Didacticiel : configurer un laboratoire à l’aide de Azure DevTest Labs
Dans ce didacticiel, vous créez un laboratoire en utilisant le portail Azure. Un administrateur de laboratoire définit un laboratoire dans une organisation, il crée des machines virtuelles dans le laboratoire et configure des stratégies. Les utilisateurs du laboratoire (les développeurs et les testeurs par exemple) revendiquent des machines virtuelles dans le laboratoire, se connectent à eux et les utilisent. 

Dans ce didacticiel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Création d’un laboratoire
> * Ajouter des machines virtuelles (VM) dans le laboratoire
> * Ajouter un utilisateur au rôle Utilisateur de laboratoire

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-a-lab"></a>Création d’un laboratoire
Les étapes suivantes montrent comment utiliser le portail Azure pour créer un laboratoire dans Azure DevTest Labs. 

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu principal sur le côté gauche, sélectionnez **Créer une ressource** (en haut de la liste), pointez sur **Outils de développement**, puis cliquez sur **Lab Services (préversion)**. 

    ![Menu Nouveau DevTest Lab](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. Dans la fenêtre **Créer un DevTest Lab**, effectuez les actions suivantes : 
    1. Pour **Nom du laboratoire**, saisissez un nom pour le laboratoire. 
    2. Pour **Abonnement**, sélectionnez l’abonnement dans lequel vous souhaitez créer le laboratoire. 
    3. Pour **Groupe de ressources**, sélectionnez **Créer un nouveau** et entrez un nom pour le groupe de ressources. 
    4. Pour **Emplacement**, sélectionnez la région ou l’emplacement où créer le laboratoire. 
    5. Sélectionnez **Créer**. 
    6. Sélectionnez **Épingler au tableau de bord**. Après avoir créé le laboratoire, il s’affiche dans le tableau de bord. 

        ![Création d’un laboratoire de DevTest Labs](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)

## <a name="add-a-vm-to-the-lab"></a>Ajouter une machine virtuelle au laboratoire

1. Sur la page **DevTest Lab**, sélectionnez **+ Ajouter** sur la barre d’outils. 

    ![Bouton Ajouter](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. Sur la page **Choisir une base**, faites une recherche avec le mot clé **Ubuntu** et sélectionnez l’une des images de base dans la liste. 
1. Sur la page **Machine virtuelle**, effectuez les actions suivantes : 
    1. Pour **Nom de la machine virtuelle**, saisissez le nom de la machine virtuelle. 
    2. Pour **Nom d’utilisateur**, entrez un nom pour l’utilisateur qui a accès à la machine virtuelle. 
    3. Dans **Entrer une valeur**, entrez le mot de passe de l’utilisateur. 
    4. Sélectionnez **Paramètres avancés**.
    5. Pour **Rendre cette machine exigible**, sélectionnez **Oui**.
    6. Vérifiez que le **nombre d’instances** est défini sur **1**. Si vous le définissez sur **2**, 2 machines virtuelles sont créées avec les noms : `<base image name>00' and <base image name>01`. Par exemple : `win10vm00` et `win10vm01`. 
    7. Pour fermer la page **Avancé**, cliquez sur **OK**. 
    8. Sélectionnez **Créer**. 

        ![Choisir une base](./media/tutorial-create-custom-lab/new-virtual-machine.png)
    9. Vous voyez l’état de la machine virtuelle dans la liste des **machines virtuelles exigibles**. La création de la machine virtuelle peut prendre environ 25 minutes. La machine virtuelle est créée dans un groupe de ressources Azure distinct, dont le nom commence par le nom du groupe de ressources actuel qui possède le laboratoire. Par exemple, si le laboratoire est dans `labrg`, la machine virtuelle peut être créée dans le groupe de ressources `labrg3988722144002`. 

        ![État de la création de la machine virtuelle](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. Une fois la machine virtuelle créée, elle apparaît dans la liste des **machines virtuelles exigibles** dans la liste. 

## <a name="add-a-user-to-the-lab-user-role"></a>Ajouter un utilisateur au rôle Utilisateur de laboratoire

1. Sélectionnez **Configuration et stratégies** sur le panneau de gauche. 

    ![Configuration et stratégies](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. Sélectionnez **Contrôle d’accès (IAM)** dans le menu, et sélectionnez **+ Ajouter** dans la barre d’outils. 

    ![Contrôle d’accès - Bouton Ajouter un utilisateur](./media/tutorial-create-custom-lab/access-control-add.png)
1. Dans la page **Ajouter des autorisations**, effectuez les actions suivantes :
    1. Pour **Rôle**, sélectionnez **Utilisateurs de DevTest Labs**. 
    2. Sélectionnez l’**utilisateur** que vous souhaitez ajouter. 
    3. Sélectionnez **Enregistrer**.

        ![Ajout d’autorisations](./media/tutorial-create-custom-lab/add-lab-user.png)
4. Pour fermer **Configuration et Stratégies - Contrôle d’accès (IAM)**, sélectionnez **X** dans le coin droit. 

## <a name="cleanup-resources"></a>Nettoyer les ressources
Le didacticiel suivant montre comment un utilisateur de laboratoire peut revendiquer et se connecter à une machine virtuelle dans le laboratoire. Si vous ne souhaitez pas suivre ce didacticiel, et pour nettoyer les ressources créées dans le cadre de ce didacticiel, procédez comme suit : 

1. Dans le portail Azure, sélectionnez **Groupes de ressources** dans le menu. 
2. Sélectionnez le groupe de ressources dans lequel vous avez créé le laboratoire. 
3. Sélectionnez **Supprimer le groupe de ressources** dans la barre d’outils. Supprimer un groupe de ressources supprime toutes les ressources dans le groupe, notamment le laboratoire. 
4. Répétez ces étapes pour supprimer le groupe de ressources supplémentaire créé pour vous, avec le nom `<your resource group name><random numbers>`. Par exemple : `splab3988722144001`. Les machines virtuelles sont créées dans ce groupe de ressources plutôt que dans le groupe de ressources au sein duquel se trouve le laboratoire. 

## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez créé un laboratoire avec une machine virtuelle et vous avez donné un accès utilisateur au laboratoire. Pour savoir comment accéder au laboratoire en tant qu’un utilisateur du laboratoire, passer au didacticiel suivant :

> [!div class="nextstepaction"]
> [Didacticiel : Accéder au laboratoire](tutorial-use-custom-lab.md)

