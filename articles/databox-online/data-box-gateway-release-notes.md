---
title: Notes de publication de préversion d’Azure Data Box Gateway | Microsoft Docs
description: Décrit les problèmes majeurs existants et les résolutions pour Azure Data Box Gateway exécutant la préversion.
services: databox-edge-gateway
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 7619056ace5d9b3cf083a40a6cfa06a0cac0561e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949687"
---
# <a name="azure-data-box-gateway-preview-release-notes"></a>Notes de publication de préversion d’Azure Data Box Gateway

## <a name="overview"></a>Vue d’ensemble

Les notes de publication suivantes identifient les problèmes majeurs existants et les problèmes résolus dans la préversion de Microsoft Azure Data Box Gateway.

Les notes de publication sont régulièrement mises à jour ; les problèmes critiques nécessitant une solution de contournement sont ajoutés au fur et à mesure de leur découverte. Lisez attentivement les informations contenues dans les notes de publication avant de déployer votre Data Box Gateway.

La préversion correspond à la version du logiciel **Data Box Gateway préversion 2.0**.

## <a name="issues-fixed-in-preview-release"></a>Problèmes résolus dans la préversion

Le tableau suivant récapitule les problèmes corrigés dans cette version.

| Non. | Problème |
| --- | --- |
| 1 | Dans cette version, lorsqu’un fichier chargé par un autre outil (AzCopy) est actualisé puis mis à jour d’une manière qui augmente/étend la taille du fichier, l’erreur suivante est observée : *Erreur 400 : InvalidBlobOrBlock (L’objet blob spécifié ou le contenu du bloc n’est pas valide.)*|


## <a name="known-issues-in-preview-release"></a>Problèmes connus dans la préversion

Le tableau suivant résume les problèmes connus pour Data Box Gateway exécutant la préversion.

| Non. | Fonctionnalité | Problème | Solution de contournement/commentaires |
| --- | --- | --- | --- |
| **1.** |Mises à jour |Les appareils Data Box Gateway créés dans les préversions antérieures ne peuvent pas être mis à jour vers cette version. |Téléchargez les images de disque virtuel de la nouvelle version, puis configurez et déployez de nouveaux appareils. Pour plus d’informations, consultez [Préparer le déploiement d’Azure Data Box Gateway](data-box-gateway-deploy-prep.md). |
| **2.** |Disque de données configuré |Une fois que vous avez provisionné un disque de données d’une certaine taille spécifiée et créé le Data Box Gateway correspondant, vous ne devez pas réduire ce disque de données. Toute tentative de réduction du disque entraîne la perte de toutes les données locales sur l’appareil. | |
| **3.** |Actualiser |Dans cette version, vous ne pouvez actualiser qu’un seul partage à la fois. | |
| **4.** |Renommer |Le changement de nom d’objets n’est pas pris en charge. |Contactez le Support Microsoft si cette fonctionnalité est essentielle pour votre flux de travail. |
| **5.** |Copier| Si un fichier en lecture seule est copié sur l’appareil, la propriété de lecture seule n’est pas conservée. | |
| **6.** |Journaux| En raison d’un bogue dans cette version, vous pouvez voir des instances du code d’erreur 110 dans *error.xml* avec des noms d’éléments non reconnaissables. | |
| **7.** |Télécharger | En raison d’un bogue dans cette version, vous pouvez voir des instances du code d’erreur 2003 lors du téléchargement de fichiers spécifiques. | |
| **8.** |Suppression | En raison d’un bogue dans cette version, si un partage NFS est supprimé, le partage peut ne pas être supprimé. L’état du partage indique *Suppression*.  |Cela ne se produit que lorsque le partage utilise un nom de fichier non pris en charge. |
| **9.** |Aide en ligne |Les liens d’aide dans le portail Azure peuvent ne pas diriger vers la documentation.|Les liens d’aide fonctionneront dans la version en disponibilité générale. |



## <a name="next-steps"></a>Étapes suivantes

- [Préparer le déploiement d’Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


