---
title: Créer une offre dans Azure Stack | Microsoft Docs
description: En tant qu’administrateur cloud, apprenez à créer une offre pour vos utilisateurs dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/20/2018
ms.author: brenduns
ms.reviewer: efemmano
ms.openlocfilehash: 66a89c3cb14dd642ae993cbf3c45885635f59759
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "43050428"
---
# <a name="create-an-offer-in-azure-stack"></a>Créer une offre dans Azure Stack

Les [offres](azure-stack-key-features.md) sont des groupes d’un ou plusieurs plans que les fournisseurs proposent à l’achat ou à l’abonnement aux utilisateurs. Ce document montre comment créer une offre comprenant le [plan que vous avez créé](azure-stack-create-plan.md). Cette offre donne aux abonnés la possibilité de configurer des machines virtuelles.

1. Connectez-vous au portail d’administration Azure Stack (https://adminportal.local.azurestack.external) et sélectionnez **Nouveau** > **Offres + plans de locataire** > **Offre**.

   ![Créer une offre](media/azure-stack-create-offer/image01.png)
  
2. Sous **Nouvelle offre**, renseignez le **Nom d’affichage** et le **Nom de la ressource**, puis sélectionnez un **Groupe de ressources**, sélectionnez **Créer nouveau** ou **Utiliser existant**. Le nom d’affichage correspond au nom convivial de l’offre. Ce nom convivial est la seule information sur l’offre que les utilisateurs verront au moment de l’abonnement à l’offre. Utilisez un nom intuitif qui aide les utilisateurs à comprendre en quoi elle consiste. Seul l’administrateur peut voir le nom de la ressource. Il s’agit du nom que les administrateurs utilisent pour gérer l’offre en tant que ressource Azure Resource Manager.

   ![Nouvelle offre](media/azure-stack-create-offer/image01a.png)
  
3. Sélectionnez **Plans de base** pour ouvrir le **Plan**. Sélectionnez les plans que vous souhaitez inclure dans l’offre, puis choisissez **Sélectionner**. Sélectionnez **Créer** pour créer l’offre.

   ![Sélectionner un plan](media/azure-stack-create-offer/image02.png)
  
4. Après avoir créé l’offre, vous pouvez modifier son état. Les offres doivent être rendues *publiques* pour permettre aux utilisateurs d’avoir une vue d’ensemble lorsqu’ils s’abonnent. Les offres peuvent être :

   - **Public** : ils sont visibles pour les utilisateurs.
   - **Privé** : visibles uniquement par les administrateurs cloud. Ce paramètre est utile lors de l’élaboration du plan ou de l’offre, ou si l’administrateur cloud souhaite [créer chaque abonnement pour les utilisateurs](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Retiré**: ils sont fermés aux nouveaux abonnés. L’administrateur cloud peut utiliser cet état pour empêcher tout abonnement futur, sans que cela affecte les abonnés actuels.

   > [!TIP]  
   > Les modifications apportées à l’offre ne sont pas immédiatement visibles par l’utilisateur. Pour voir les modifications et afficher la nouvelle offre, les utilisateurs pourraient devoir se déconnecter et se reconnecter au portail de l’utilisateur.

   Dans la vue d’ensemble de l’offre, sélectionnez **État de l’accessibilité**. Choisissez l’état que vous souhaitez utiliser (par exemple, **Public**), puis sélectionnez **Enregistrer**.
 
     ![Choix de l’état](media/azure-stack-create-offer/change-stage-1807.png)

     Une autre possibilité consiste à sélectionner l’option**Changer l’état**, puis à choisir un état.

    ![Sélectionner État de l’accessibilité](media/azure-stack-create-offer/change-stage-select-1807.png)

   > [!NOTE]
   > Vous pouvez également utiliser PowerShell pour créer des offres, des plans et des quotas par défaut. Pour plus d’informations, consultez l’article [Module Azure Stack PowerShell 1.4.0](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).

## <a name="next-steps"></a>Étapes suivantes

- [Création d’abonnements](azure-stack-subscribe-plan-provision-vm.md)
- [Approvisionner une machine virtuelle](azure-stack-provision-vm.md)
