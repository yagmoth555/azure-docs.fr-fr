---
title: Modèles Azure Resource Manager pour la Sauvegarde Azure
description: Exemples Azure Backup PowerShell
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 04/18/2018
ms.author: markgal
ms.custom: mvc
ms.openlocfilehash: 0aac49be397f5e1c86fa834d341399775fd71cfa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607070"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Modèles Azure Resource Manager pour la Sauvegarde Azure

Le tableau suivant inclut des liens vers des modèles Azure Resource Manager à utiliser avec les coffres Recovery Services et les fonctionnalités de Sauvegarde Azure.

|   |   |
|---|---|
|**Coffre Recovery Services** | |
| [Créer un coffre Recovery Services](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Créez un coffre Recovery Services. Le coffre peut être utilisé pour la Sauvegarde Azure et Azure Site Recovery. |
|**Sauvegarde de machines virtuelles**| |
| [Sauvegarde des machines virtuelles Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Utilisez le coffre Recovery Services existant et la stratégie de sauvegarde pour sauvegarder les machines virtuelles Azure Resource Manager dans le même groupe de ressources.|
| [Sauvegarde des machines virtuelles IaaS dans le coffre Recovery Services](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Modèle pour sauvegarder les machines virtuelles classiques et Azure Resource Manager. |
| [Création d’une stratégie de sauvegarde hebdomadaire pour les machines virtuelles IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Le modèle crée un coffre Recovery Services et une stratégie de sauvegarde hebdomadaire utilisée pour sauvegarder des machines virtuelles classiques et Azure Resource Manager.|
| [Création d’une stratégie de sauvegarde quotidienne pour les machines virtuelles IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Le modèle crée un coffre Recovery Services et une stratégie de sauvegarde quotidienne utilisée pour sauvegarder des machines virtuelles classiques et Azure Resource Manager.|
| [Déploiement d’une machine virtuelle Windows Server avec la fonctionnalité de sauvegarde activée](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | Le modèle crée une machine virtuelle Windows Server et un coffre Recovery Services avec la stratégie de sauvegarde par défaut activée.|
|**Surveillance des travaux de sauvegarde** |  |
| [Utilisation d’OMS Log Analytics pour surveiller la Sauvegarde Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Le modèle déploie la surveillance OMS pour la Sauvegarde Azure qui vous permet de surveiller les travaux de sauvegarde et de restauration, les alertes de sauvegarde et le stockage cloud utilisés dans vos coffres Recovery Services.|  
|   |   |

