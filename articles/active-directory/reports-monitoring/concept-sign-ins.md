---
title: Rapports d’activité de connexion dans le portail Azure Active Directory | Microsoft Docs
description: Présentation des rapports d’activité de connexion dans le portail Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 06/21/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bc8d3525ab7cdbdf298ecbbc686ced16fa7bc77c
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2018
ms.locfileid: "42139833"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Rapports d’activité de connexion dans le portail Azure Active Directory

Grâce à la fonction de création de rapports Azure Active Directory (Azure AD) dans le [portail Azure](https://portal.azure.com), vous pouvez obtenir les informations dont vous avez besoin pour évaluer l’état de votre environnement.

L’architecture de création de rapports dans Azure Active Directory comprend les composants suivants :

- **Activité** 
    - **Activités de connexion** – Informations sur l’utilisation des applications gérées et les activités de connexion des utilisateurs
    - **Activités du système** – Informations sur les activités du système liées aux utilisateurs et à la gestion des groupes, à vos applications gérées et aux activités de répertoire.
- **Sécurité** 
    - **Connexions risquées** : une connexion risquée est une tentative de connexion susceptible de provenir d’un utilisateur autre que le propriétaire légitime d’un compte d’utilisateur. Pour en savoir plus, consultez Connexions risquées.
    - **Utilisateurs avec indicateur de risque** : il s’agit d’un compte d’utilisateur susceptible d’être compromis. Pour en savoir plus, consultez Utilisateurs avec indicateur de risque.

Cette rubrique vous donne une vue d’ensemble des activités de connexion.

## <a name="prerequisites"></a>Prérequis

### <a name="who-can-access-the-data"></a>Qui peut accéder aux données ?
* Les utilisateurs ayant le rôle Administrateur de sécurité, Lecteur Sécurité, Lecteur de rapports
* Administrateurs généraux
* Tous les utilisateurs (non administrateurs) peuvent accéder à leurs propres connexions 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>De quelle licence Azure AD avez-vous besoin pour accéder à l’activité de connexion ?
* Votre client doit avoir une licence Azure AD Premium associée pour afficher tous les rapports d’activités de connexion.


## <a name="sign-in-activities"></a>Activités de connexion

Avec les informations fournies par le rapport sur les connexions des utilisateurs, trouvez des réponses aux questions telles que :

* Quel est le modèle de connexion d’un utilisateur ?
* Combien d’utilisateurs se sont connectés au cours d’une semaine ?
* Quel est l’état de ces connexions ?

La zone **Connexions** de la section Activité de **Azure Active Directory** constitue votre premier point d’entrée pour toutes les activités de connexion.


![Activité de connexion](./media/concept-sign-ins/61.png "Activité de connexion")


Un journal de connexion inclut un mode Liste par défaut, qui indique :

- La date de connexion
- L’utilisateur associé
- L’application à laquelle l’utilisateur s’est connecté
- L’état de connexion
- L’état de la détection de risque
- L’état de l’exigence de l’authentification multifacteur (MFA)

![Activité de connexion](./media/concept-sign-ins/01.png "Activité de connexion")

Vous pouvez personnaliser le mode Liste en cliquant sur **Colonnes** dans la barre d’outils.

![Activité de connexion](./media/concept-sign-ins/19.png "Activité de connexion")

Cela vous permet d’afficher des champs supplémentaires, ou de supprimer des champs qui sont déjà affichés.

![Activité de connexion](./media/concept-sign-ins/02.png "Activité de connexion")

En cliquant sur un élément dans le mode Liste, vous pouvez obtenir toutes les informations disponibles le concernant dans un affichage horizontal.

![Activité de connexion](./media/concept-sign-ins/03.png "Activité de connexion")

> [!NOTE]
> Les clients peuvent maintenant résoudre les problèmes de stratégies d’accès conditionnel grâce à tous les rapports de connexion. Les clients peuvent examiner l’état de l’accès conditionnel et consulter en détail les stratégies applicables à la connexion et les résultats de chaque stratégie en cliquant sur l’onglet **Accès conditionnel** pour obtenir un rapport de connexion.
> Pour en savoir plus, consultez le [Forum aux questions sur les informations de l’accès conditionnel dans toutes les connexions](reports-faq.md#conditional-access).

![Activité de connexion](./media/concept-sign-ins/ConditionalAccess.png "Activité de connexion")


## <a name="filter-sign-in-activities"></a>Filtrer les activités de connexion

Pour limiter les données transmises à un niveau qui vous convient, vous pouvez filtrer les données de connexion à l’aide des champs par défaut suivants :

- Utilisateur
- Application
- État de la connexion
- Status of the risk detection (État de la détection de risque)
- Date

![Activité de connexion](./media/concept-sign-ins/04.png "Activité de connexion")

Le filtre **Utilisateur** vous permet de spécifier le nom ou le nom d’utilisateur principal de l’utilisateur qui vous intéresse.

Le filtre **Application** vous permet de spécifier le nom de l’application qui vous intéresse.

Le filtre **État de la connexion** vous permet de sélectionner :

- Tous
- Succès
- Échec

Le filtre **Risque détecté** vous permet de sélectionner :

- Tous
- Oui
- Non 

Le filtre **Date** vous permet de définir un intervalle de temps pour les données renvoyées.  
Les valeurs possibles sont les suivantes :

- 1 mois
- 7 jours
- 24 heures
- Intervalle de temps personnalisé

Lorsque vous sélectionnez une plage personnalisée, vous pouvez configurer une heure de début et une heure de fin.

Si vous ajoutez des champs à votre affichage de connexions, ils sont automatiquement ajoutés à la liste de filtres. Par exemple, en ajoutant un champ **Application cliente** à votre liste, vous obtenez également une autre option de filtre qui vous permet de définir les filtres suivants :

- Browser      
- Exchange ActiveSync (pris en charge)               
- Exchange ActiveSync (non pris en charge)
- Autres clients               
    - IMAP
    - MAPI
    - Clients Office plus anciens
    - POP
    - SMTP


![Activité de connexion](./media/concept-sign-ins/12.png "Activité de connexion")


## <a name="download-sign-in-activities"></a>Télécharger les activités de connexion

Vous pouvez télécharger les données des activités de connexion si vous souhaitez les utiliser en dehors du portail Azure. En cliquant sur **Télécharger**, un fichier CSV contenant les 5K enregistrements les plus récents est créé.  En plus d’un bouton de téléchargement, le portail Azure vous propose une option permettant de générer un script pour télécharger vos données.  

![Télécharger](./media/concept-sign-ins/71.png "Télécharger")

Si vous avez besoin de davantage de souplesse, vous pouvez utiliser la solution de script. En cliquant sur **Script**, un script PowerShell comprenant tous les filtres que vous avez définis est créé. Téléchargez et exécutez ce script en **mode administrateur** pour générer le fichier CSV. 

### <a name="running-the-script-on-a-windows-10-machine"></a>Exécuter le script sur une machine Windows 10

Si vous souhaitez exécuter le script sur une machine **Windows 10**, vous devez d’abord effectuer quelques étapes supplémentaires. 

1. Installez le [module AzureRM](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0l).
2. Importez le module en ouvrant une invite PowerShell et en exécutant la commande **Import-Module AzureRM**.
3. Exécutez **Set-ExecutionPolicy unrestricted (Définir la stratégie d’exécution sans restriction)** et choisissez **Yes to All (Oui à tous)**. 
4. Vous pouvez maintenant exécuter le script PowerShell téléchargé en mode administrateur pour générer le fichier CSV.

En plus de l’implémentation technique, le nombre d’enregistrements que vous pouvez télécharger est limité par les [stratégies de rétention de rapport Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-in-activities-shortcuts"></a>Raccourcis relatifs aux activités de connexion

En plus d’Azure Active Directory, le portail Azure vous offre d’autres points d’entrée pour les données sur les activités de connexion :

- Vue d’ensemble de la protection de la sécurité des identités
- Utilisateurs
- Groupes
- Applications d’entreprise


### <a name="users-sign-ins-activities"></a>Activités de connexion des utilisateurs

Avec les informations fournies par le rapport sur les connexions des utilisateurs, trouvez des réponses aux questions telles que :

- Quel est le modèle de connexion d’un utilisateur ?
- Combien d’utilisateurs se sont connectés au cours d’une semaine ?
- Quel est l’état de ces connexions ?

Votre point d’entrée pour ces données est le graphique des connexions des utilisateurs sur la page de vue d’ensemble de la **protection de la sécurité des identités**. Le graphique des connexions des utilisateurs affiche les agrégations hebdomadaires des connexions de tous les utilisateurs au cours d’une période donnée. La valeur par défaut de cette période est de 30 jours.

![Activité de connexion](./media/concept-sign-ins/06.png "Activité de connexion")

Lorsque vous cliquez sur un jour dans le graphique des connexions, vous obtenez une vue d’ensemble des activités de connexion correspondantes.

Chaque ligne de la liste des activités de connexion affiche :

* Qui s’est connecté ?
* Quelle application a été la cible de la connexion ?
* Quel est l’état de la connexion ?
* Quel est l’état MFA de la connexion ?

En cliquant sur un élément, vous obtenez plus d’informations sur l’opération de connexion :

- ID d'utilisateur
- Utilisateur
- Nom d’utilisateur
- ID de l'application
- Application
- Client
- Lieu
- Adresse IP
- Date
- MFA obligatoire
- État de la connexion

 
Sur la page **Utilisateurs**, vous obtenez une vue d’ensemble complète de toutes les connexions des utilisateurs en cliquant sur **Connexions** dans la section **Activité**.

![Activité de connexion](./media/concept-sign-ins/08.png "Activité de connexion")

## <a name="usage-of-managed-applications"></a>Utilisation des applications gérées

En disposant d’une vue centrée sur les applications de vos données de connexion, vous pouvez répondre aux questions telles que :

* Qui utilise mes applications ?
* Quelles sont les 3 principales applications dans votre organisation ?
* J’ai récemment déployé une application. Comment se comporte-t-elle ?

Les 3 principales applications de votre organisation dans le rapport sur les 30 derniers jours apparaissant dans la section **Vue d’ensemble** sous **Enterprise applications** (Applications d’entreprise) constituent votre point d’entrée.

![Activité de connexion](./media/concept-sign-ins/10.png "Activité de connexion")

Le graphique d’utilisation des applications affiche les agrégations hebdomadaires des connexions pour vos 3 principales applications au cours d’une période donnée. La valeur par défaut de cette période est de 30 jours.

![Activité de connexion](./media/concept-sign-ins/47.png "Activité de connexion")

Si vous le souhaitez, vous pouvez définir la focalisation sur une application spécifique.

![Reporting](./media/concept-sign-ins/single_spp_usage_graph.png "Reporting")

Lorsque vous cliquez sur un jour dans le graphique d’utilisation des applications, vous obtenez une liste détaillée des activités de connexion.

L’option **Connexions** vous fournit une vue d’ensemble complète de tous les événements de connexion à vos applications.

![Activité de connexion](./media/concept-sign-ins/11.png "Activité de connexion")



## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les codes d’erreur de l’activité des connexions, voir [Codes d’erreur des rapports d’activité des connexions dans le portail Azure Active Directory](reference-sign-ins-error-codes.md).

