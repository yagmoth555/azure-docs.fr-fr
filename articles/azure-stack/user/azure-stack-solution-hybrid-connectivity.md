---
title: Configurer l’identité de cloud hybride avec Azure et Azure Stack | Microsoft Docs
description: Découvrez comment configurer l’identité de cloud hybride avec Azure et Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/25/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 652d39b4d15569b9365543e02f170664a88715fa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953257"
---
# <a name="tutorial-configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Tutoriel : Configurer l’identité de cloud hybride avec Azure et Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez accéder aux ressources en toute sécurité dans Azure global et Azure Stack à l’aide du modèle de connectivité hybride.

Dans ce tutoriel, vous créez un exemple d’environnement pour :

> [!div class="checklist"]
> - Conserver les données localement pour le respect de la confidentialité ou des obligations réglementaires, tout en gardant l’accès aux ressources Azure globales.
> - Gérer un système hérité tout en utilisant un déploiement d’application avec une mise à l’échelle dans le cloud et des ressources dans Azure global.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack est une extension d’Azure. Azure Stack apporte l’agilité et l’innovation du cloud computing à votre environnement local et active le seul cloud hybride qui vous permet de créer et de déployer des applications hybrides en tous lieux.  
> 
> Le livre blanc [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars) (Étude des conceptions pour les applications hybrides) se penche sur les fondements de la qualité logicielle (sélection élective, extensibilité, disponibilité, résilience, facilité de gestion et sécurité) en matière de conception, de déploiement et d’exploitation des applications hybrides. Les considérations de conception vous aident à optimiser la conception des applications hybrides, en réduisant les risques dans les environnements de production.


## <a name="prerequisites"></a>Prérequis

Certains composants sont nécessaires pour créer un déploiement de connectivité hybride. La préparation de certains de ces composants peut prendre du temps, vous devrez donc planifier en conséquence.

**Azure Stack**

Un partenaire OEM/matériel Azure peut déployer un service Azure Stack de production et tous les utilisateurs peuvent déployer un Kit de développement Azure Stack.

**Composants d’Azure Stack**

Un opérateur Azure Stack doit déployer le service App Service, créer des plans et des offres, créer un abonnement de locataire et ajouter l’image Windows Server 2016. Si vous disposez déjà de certains de ces composants, vérifiez qu’ils répondent aux conditions requises avant de commencer ce didacticiel.

Ce tutoriel suppose que vous disposez de connaissances de base sur Azure et Azure Stack. Pour en savoir plus avant de commencer le didacticiel, lisez les articles suivants :

 - [Présentation de Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Concepts clés d’Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
 - Créez une [application web](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts#create-an-azure-web-app-using-the-portal) dans Azure. Notez l’URL de l’application web, car vous en aurez besoin dans le didacticiel.

### <a name="azure-stack"></a>Azure Stack

 - Utilisez votre système Azure Stack de production ou déployez le Kit de développement Azure Stack à partir de https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Le déploiement de l’ASDK peut prendre jusqu’à 7 heures, planifiez donc en conséquence.

 - Déployez les services PaaS [App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) sur Azure Stack.
 - [Créez des plans et des offres](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) dans l’environnement Azure Stack.
 - [Créez un abonnement client](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) dans l’environnement Azure Stack.

### <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous remplissez les conditions suivantes avant de commencer la configuration de la connectivité de cloud hybride :

 - Vous avez besoin d’une adresse IPv4 publique exposée en externe pour votre périphérique VPN. Cette adresse IP ne peut pas se trouver derrière un NAT.
 - Toutes les ressources sont déployées dans la même région/le même emplacement.

#### <a name="tutorial-example-values"></a>Exemples de valeurs du didacticiel

Nous utilisons les valeurs suivantes dans les exemples de ce didacticiel. Vous pouvez utiliser ces valeurs pour créer un environnement de test ou vous y référer pour mieux comprendre les exemples. Pour plus d’informations sur les paramètres de la passerelle VPN, voir [À propos des paramètres de la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Spécifications de la connexion :

 - **Type de VPN** : Basé sur itinéraires
 - **Type de connexion :** Site-to-site (IPsec)
 - **Type de passerelle** : VPN
 - **Nom de la connexion Azure** : Azure-Gateway-AzureStack-S2SGateway (le portail fournira automatique cette valeur)
 - **Nom de la connexion Azure Stack** : AzureStack-Gateway-Azure-S2SGateway (le portail fournira automatique cette valeur)
 - **Clé partagée** : toute clé compatible avec le matériel VPN, avec des valeurs correspondantes des deux côtés de la connexion
 - **Abonnement** : un abonnement de votre choix
 - **Groupe de ressources** : Test-Infra

Adresses IP réseau et sous-réseau :

| Connexion Azure/Azure Stack | NOM | Sous-réseau | Adresse IP |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Réseau virtuel Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | Sous-réseau de passerelle<br>10.100.103.0/24 |  |
| Réseau virtuel Azure Stack | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | Sous-réseau de passerelle <br>10.100101.0/24 |  |
| Passerelle de réseau virtuel Azure | Azure-Gateway |  |  |
| Passerelle de réseau virtuel Azure Stack | AzureStack-Gateway |  |  |
| Adresse IP publique | Azure-GatewayPublicIP |  | Déterminée à la création |
| Adresse IP publique Azure Stack | AzureStack-GatewayPublicIP |  | Déterminée à la création |
| Passerelle du réseau local Azure | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Valeur d’adresse IP publique Azure Stack |
| Passerelle de réseau local Azure Stack | Azure-S2SGateway<br>10.100.102.0/23 |  | Adresse IP publique Azure |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Créer un réseau virtuel dans Azure global et Azure Stack

Procédez comme suit pour créer un réseau virtuel à l’aide du portail Azure. Vous pouvez utiliser ces [exemples de valeurs](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) si vous utilisez cet article comme un didacticiel. Toutefois, si vous utilisez cet article pour configurer un environnement de production, remplacez les exemples de paramètres par vos propres valeurs.

> [!IMPORTANT]
> Vous devez vous assurer que les adresses IP ne se chevauchent pas dans les espaces d’adressage du réseau virtuel Azure ou Azure Stack.

Pour créer un réseau virtuel dans Azure :

1. Utilisez votre navigateur pour vous connecter au [portail Azure](http://portal.azure.com/) et connectez-vous avec votre compte Azure.
2. Sélectionnez **Créer une ressource**. Dans le champ **Rechercher dans le marketplace**, entrez `virtual network`. Recherchez **Réseau virtuel** dans la liste des résultats, puis sélectionnez **Réseau virtuel**.
3. Dans la liste **Sélectionner un modèle de déploiement**, sélectionnez **Gestionnaire des ressources**, puis **Créer**.
4. Dans **Créer un réseau virtuel**, configurez les paramètres du réseau virtuel. Les noms des champs obligatoires sont précédés d’un astérisque rouge.  Lorsque vous entrez une valeur valide, l’astérisque devient une coche verte.

Pour créer un réseau virtuel dans Azure Stack :

* Répétez les étapes précédentes (1-4) à l’aide du **portail du locataire** Azure Stack.

## <a name="add-a-gateway-subnet"></a>Ajouter un sous-réseau de passerelle

Avant de connecter votre réseau virtuel à une passerelle, vous devez créer le sous-réseau de passerelle pour le réseau virtuel auquel vous souhaitez vous connecter. Les services de passerelle utilisent les adresses IP que vous spécifiez dans le sous-réseau de passerelle.

Dans le [portail Azure](http://portal.azure.com/), accédez au réseau virtuel Gestionnaire des ressources dans lequel vous souhaitez créer une passerelle de réseau virtuel.

1. Sélectionnez le réseau virtuel pour ouvrir la page **Réseau virtuel**.
2. Dans **PARAMÈTRES**, sélectionnez **Sous-réseaux**.
3. Sur la page **Sous-réseaux**, sélectionnez **+Sous-réseau de passerelle** pour ouvrir la page **Ajouter un sous-réseau**.

    ![Ajouter un sous-réseau de passerelle](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. Le **Nom** du sous-réseau est automatiquement rempli avec la valeur « GatewaySubnet ». Cette valeur est nécessaire pour qu’Azure puisse reconnaître le sous-réseau en tant que sous-réseau de passerelle.
5. Modifiez les valeurs **Plage d’adresses** qui sont fournies pour qu’elles correspondent à vos besoins de configuration, puis sélectionnez **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Créer une passerelle de réseau virtuel dans Azure et Azure Stack

Procédez comme suit pour créer une passerelle de réseau virtuel dans Azure.

1. Sur le côté gauche de la page du portail, sélectionnez **+**, puis entrez « Passerelle de réseau virtuel » dans le champ de recherche.
2. Dans **Résultats**, sélectionnez **Passerelle de réseau virtuel**.
3. Dans **Passerelle de réseau virtuel**, sélectionnez **Créer** pour ouvrir la page **Créer une passerelle de réseau virtuel**.
4. Dans **Créer une passerelle réseau virtuelle**, spécifiez les valeurs de votre passerelle de réseau, comme indiqué dans **Exemples de valeurs du didacticiel**, ainsi que les valeurs supplémentaires suivantes :

    - **Référence (SKU)** : De base
    - **Réseau virtuel** : sélectionnez le réseau virtuel que vous avez créé précédemment. Le sous-réseau de passerelle que vous avez créé est automatiquement sélectionné.
    - **Première configuration IP** : adresse IP publique de votre passerelle.
        - Sélectionnez **Créer une configuration d’IP de passerelle**, la page **Choisir une adresse IP publique** s’affiche.
        - Sélectionnez **+Créer nouveau** pour ouvrir la page **Créer une adresse IP publique**.
        - Donnez un **nom** à votre adresse IP publique. Laissez la référence SKU sur **De base**, puis sélectionnez **OK** pour enregistrer vos modifications.

       > [!Note]
       > Actuellement, la passerelle VPN prend uniquement en charge l’allocation d’adresses IP publiques dynamiques. Toutefois, cela ne signifie pas que l’adresse IP change après son affectation à votre passerelle VPN. L’adresse IP publique change uniquement lorsque la passerelle est supprimée, puis recréée. Un redimensionnement, une réinitialisation ou des autres opérations de maintenance/mise à niveau internes de votre passerelle VPN ne modifient pas l’adresse IP.

4. Vérifiez vos paramètres de passerelle.
5. Sélectionnez **Créer** pour créer la passerelle VPN. Les paramètres de passerelle sont validés et la vignette « Déploiement d’une passerelle de réseau virtuel » s’affiche sur votre tableau de bord.

   >[!Note]
   >La création d’une passerelle peut prendre jusqu’à 45 minutes Vous devrez peut-être actualiser la page du portail pour que l’état terminé apparaisse.

    Une fois la passerelle créée, observez le réseau virtuel dans le portail pour connaître l’adresse IP affectée à la passerelle. Cette dernière apparaît sous la forme d’un appareil connecté. Pour plus d’informations sur la passerelle, sélectionnez l’appareil.

6. Répétez les étapes précédentes (1-5) sur votre déploiement Azure Stack.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Créer une passerelle de réseau local dans Azure et Azure Stack

La passerelle de réseau local fait généralement référence à votre emplacement local. Vous donnez un nom auquel Azure ou Azure Stack peut faire référence, puis vous spécifiez :

- L’adresse IP du périphérique VPN local pour lequel vous créez une connexion.
- Les préfixes d’adresses IP qui seront acheminés via la passerelle VPN vers le périphérique VPN. Les préfixes d’adresses que vous spécifiez sont les préfixes situés sur votre réseau local.

  >[!Note]
  >En cas de modification du réseau local ou si vous devez modifier l’adresse IP publique du périphérique VPN, vous pouvez facilement mettre à jour les valeurs ultérieurement.

1. Dans le portail Azure, sélectionnez **+Créer une ressource**.
2. Dans la zone de recherche, entrez **Passerelle de réseau local**, puis sélectionnez **Entrée** pour lancer la recherche. Une liste de résultats apparaîtra.
3. Sélectionnez **Passerelle de réseau local**, puis **Créer** pour ouvrir la page **Créer une passerelle de réseau local**.
4. Dans **Créer une passerelle de réseau local**, spécifiez les valeurs de votre passerelle de réseau local à l’aide de nos **Exemples de valeurs du didacticiel**. Incluez les valeurs supplémentaires suivantes.

    - **Adresse IP :** il s’agit de l’adresse IP publique du périphérique VPN auquel vous souhaitez qu’Azure ou Azure Stack se connecte. Spécifiez une adresse IP publique valide qui ne se trouve pas derrière un NAT pour qu’Azure puisse atteindre l’adresse. Si vous ne disposez pas de l’adresse IP actuellement, vous pouvez utiliser une valeur de l’exemple comme un espace réservé. Toutefois, vous devrez revenir en arrière et remplacer l’espace réservé par l’adresse IP publique de votre périphérique VPN. Azure ne peut pas se connecter à l’appareil tant que vous ne fournissez pas une adresse valide.
    - **Espace d’adressage** : il s’agit de la plage d’adresses du réseau qui représente ce réseau local. Vous pouvez ajouter plusieurs plages d’espaces d’adressage. Assurez-vous que les plages que vous spécifiez ici ne chevauchent pas les plages d’autres réseaux auxquels vous souhaitez vous connecter. Azure achemine la plage d’adresses que vous spécifiez vers l’adresse IP du périphérique VPN local. Utilisez vos propres valeurs (et non un exemple de valeur) si vous voulez vous connecter à votre site local.
    - **Configurer les paramètres de BGP** : sélectionnez cette option uniquement lors de la configuration BGP. pour la configuration du protocole BGP.
    - **Abonnement** : vérifiez que l’abonnement approprié s’affiche.
    - **Groupe de ressources :** sélectionnez le groupe de ressources que vous souhaitez utiliser. Vous pouvez créer un groupe de ressources ou en sélectionner un déjà créé.
    - **Emplacement** : sélectionnez l’emplacement dans lequel cet objet sera créé. Vous pouvez sélectionner l’emplacement dans lequel se trouve votre réseau virtuel, mais vous n’êtes pas obligé de le faire.
5. Lorsque vous avez terminé de spécifier les valeurs requises, sélectionnez **Créer** pour créer la passerelle de réseau local.
6. Répétez ces étapes (1-5) sur votre déploiement Azure Stack.

## <a name="configure-your-connection"></a>Configurer votre connexion

Les connexions site à site vers un réseau local nécessitent un périphérique VPN. Le périphérique VPN que vous configurez est appelé une connexion. Pour configurer votre connexion, vous devez :

- Une clé partagée. Il s’agit de la clé partagée spécifiée lors de la création de la connexion VPN de site à site. Dans nos exemples, nous utilisons une clé partagée basique. Nous vous conseillons de générer une clé plus complexe.
- L’adresse IP publique de votre passerelle de réseau virtuel. Vous pouvez afficher l’adresse IP publique à l’aide du portail Azure, de PowerShell ou de l’interface de ligne de commande. Pour rechercher l’adresse IP publique de votre passerelle VPN à l’aide du portail Azure, accédez à Passerelles de réseau virtuel, puis sélectionnez le nom de votre passerelle.

Procédez comme suit pour créer une connexion VPN de site à site entre votre passerelle de réseau virtuel et votre périphérique VPN local.

1. Dans le portail Azure, sélectionnez **+ Créer une ressource**.
2. Recherchez des **connexions**.
3. Dans **Résultats**, sélectionnez **Connexions**.
4. Dans **Connexions**, sélectionnez **Créer**.
5. Dans **Créer une connexion**, configurez les paramètres suivants :

    - **Type de connexion** : sélectionnez Site à site (IPSec).
    - **Groupe de ressources** : sélectionnez votre groupe de ressources de test.
    - **Passerelle de réseau virtuel** : sélectionnez la passerelle de réseau virtuel que vous avez créée.
    - **Passerelle de réseau local** : sélectionnez la passerelle de réseau local que vous avez créée.
    - **Nom de la connexion** : rempli automatiquement avec les valeurs provenant des deux passerelles.
    - **Clé partagée** : cette valeur doit correspondre à celle que vous utilisez pour votre périphérique VPN local. Dans l’exemple du didacticiel, nous avons utilisé « abc123 », mais vous pouvez (et devriez) utiliser une valeur plus complexe. L’important, c’est que cette valeur DOIT être identique à celle spécifiée lors de la configuration de votre périphérique VPN.
    - Les valeurs pour **Abonnement**, **Groupe de ressources**, et **Emplacement** sont fixes.

6. Sélectionnez **OK** pour créer votre connexion.

Vous pouvez afficher la connexion dans la page **Connexions** de la passerelle de réseau virtuel. L’état passe de *Inconnu* à *Connexion*, puis à *Réussi*.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les modèles Azure Cloud, consultez [Modèles de conception cloud](https://docs.microsoft.com/azure/architecture/patterns).
