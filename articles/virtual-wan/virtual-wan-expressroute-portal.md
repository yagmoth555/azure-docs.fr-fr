---
title: Utiliser Azure Virtual WAN pour établir des connexions ExpressRoute avec des environnements Azure et locaux | Microsoft Docs
description: Dans ce didacticiel, vous allez découvrir comment utiliser Azure Virtual WAN pour établir des connexions ExpressRoute avec des environnements Azure et locaux.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 09/12/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporoate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 46a48c6e06f37968ab3f41b30d983f2664785811
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990550"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan-preview"></a>Didacticiel : Créer une association ExpressRoute avec Azure Virtual WAN (préversion)

Ce didacticiel vous montre comment utiliser Azure Virtual WAN pour vous connecter à vos ressources dans Azure via un circuit et une association ExpressRoute. Pour plus d’informations sur le WAN virtuel, consultez [Vue d'ensemble de WAN virtuel](virtual-wan-about.md)

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un vWAN
> * Créer un hub
> * Rechercher et associer un circuit au hub
> * Associer le circuit à un ou plusieurs hubs
> * Connecter un réseau virtuel à un hub
> * Afficher votre WAN virtuel
> * Afficher l’intégrité des ressources
> * Surveiller une connexion

> [!IMPORTANT]
> Cette préversion publique est fournie sans contrat de niveau de service et ne doit pas être utilisée pour les charges de travail de production. Certaines fonctionnalités peuvent ne pas être prises en charge, disposer de capacités limitées ou ne pas être disponibles dans tous les emplacements Azure. Consultez les [Conditions d’utilisation supplémentaires des préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]


## <a name="vnet"></a>1. Créez un réseau virtuel

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. Créer un WAN virtuel

Dans un navigateur, accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

### <a name="getting-started-page"></a>Page Mise en route

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-gettingstarted-include.md)]

## <a name="hub"></a>3. Créer un hub

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="hub"></a>4. Rechercher et associer un circuit au hub

1. Sélectionnez votre vWAN et, sous **Architecture de Virtual WAN**, sélectionnez **Circuits ExpressRoute**.
2. Si le circuit ExpressRoute se trouve dans le même abonnement que votre vWAN, cliquez sur **Sélectionner circuit ExpressRoute** depuis votre ou vos abonnements. 
3. Avec la liste déroulante, sélectionnez le circuit ExpressRoute que vous souhaitez associer au hub.
4. Si le circuit ExpressRoute ne se trouve pas dans le même abonnement ou que vous disposez d’[une clé d’autorisation et d’un ID d’homologue](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md), sélectionnez **Trouver un circuit en échange d’une clé d’autorisation**.
5. Entrez les informations suivantes :
* **Clé d’autorisation** : générée par le propriétaire du circuit comme décrit ci-dessus.
* **URI du circuit pair** : URI qui est fourni par le propriétaire du circuit et identifie le circuit de manière unique.
* **Poids du routage** - [Poids du routage](../expressroute/expressroute-optimize-routing.md) vous permet de privilégier certains chemins d’accès lorsque plusieurs circuits provenant de différents sites d’homologation sont connectés au même hub.
6. Cliquez sur **Trouver le circuit** et sélectionnez le circuit qui s’affiche.
7. Sélectionnez un ou plusieurs hubs dans la liste déroulante et cliquez sur **Enregistrer**.

## <a name="vnet"></a>5. Connecter votre réseau virtuel à un hub

Dans cette étape, vous créez la connexion de peering entre votre hub et un réseau virtuel. Répétez ces étapes pour chaque réseau virtuel que vous souhaitez connecter.

1. Dans la page de votre réseau étendu, cliquez sur **Connexion réseau de réseau virtuel**.
2. Dans la page de connexion de réseau virtuel, cliquez sur **+ Ajouter une connexion**.
3. Dans la page **Ajouter une connexion**, renseignez les champs suivants :

    * **Nom de connexion** : nommez votre connexion.
    * **Hubs** : sélectionnez le hub que vous souhaitez associer à cette connexion.
    * **Abonnement** : vérifiez l’abonnement.
    * **Réseau virtuel** : sélectionnez le réseau virtuel que vous souhaitez connecter à ce hub. Le réseau virtuel ne peut pas avoir une passerelle de réseau virtuel déjà existante.


## <a name="viewwan"></a>6. Afficher votre WAN virtuel

1. Accédez au WAN virtuel.
2. Dans la page Vue d’ensemble, chaque point sur la carte représente un hub. Placez le curseur sur n’importe quel point pour afficher le résumé de l’intégrité du hub.
3. Dans la section des hubs et des connexions, vous pouvez afficher l’état du hub, le site, la région, l’état de la connexion VPN et les octets entrés et sortis.

## <a name="viewhealth"></a>7. Afficher l’intégrité des ressources

1. Accédez à votre réseau étendu.
2. Sur la page de votre WAN, dans la section **SUPPORT + Dépannage**, cliquez sur **Intégrité** pour afficher votre ressource.

## <a name="connectmon"></a>8. Surveiller une connexion

Créez une connexion pour surveiller les communications entre une machine virtuelle Azure et un site distant. Pour plus d’informations sur la façon de configurer un contrôleur de connexion, consultez [Surveiller une communication réseau](~/articles/network-watcher/connection-monitor.md). Le champ source est l’adresse IP de la machine virtuelle dans Azure, et l’adresse IP de destination est l’adresse IP du site.

## <a name="cleanup"></a>9. Supprimer les ressources

Quand vous n’avez plus besoin de ces ressources, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Remplacez « myResourceGroup » par le nom de votre groupe de ressources et exécutez la commande PowerShell suivante :

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer un vWAN
> * Créer un hub
> * Rechercher et associer un circuit au hub
> * Associer le circuit à un ou plusieurs hubs
> * Connecter un réseau virtuel à un hub
> * Afficher votre WAN virtuel
> * Afficher l’intégrité des ressources
> * Surveiller une connexion

Pour plus d’informations sur le WAN virtuel, consultez la page [Vue d'ensemble de WAN virtuel](virtual-wan-about.md).