---
title: Déployer et configurer un pare-feu Azure dans un réseau hybride à l’aide d’Azure PowerShell
description: Ce didacticiel vous apprend à déployer et configurer un pare-feu Azure à l’aide du portail Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 919051a945d423a104b286e9c5703c5b749cf026
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946457"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Didacticiel : Déployer et configurer un pare-feu Azure dans un réseau hybride à l’aide d’Azure PowerShell


Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Configurer l’environnement réseau
> * Configurer et déployer le pare-feu
> * Créer les itinéraires
> * Créer les machines virtuelles
> * Tester le pare-feu

Pour ce didacticiel, vous créez trois réseaux virtuels :
- **VNet-Hub** : le pare-feu est dans ce réseau virtuel.
- **VNet-Spoke** : le réseau virtuel spoke correspond à la charge de travail sur Azure.
- **VNet-Onprem** : le réseau virtuel OnPrem représente un réseau local. Dans un déploiement réel, il peut être connecté via un VPN ou une connexion Express Route. Par souci de simplicité, ce didacticiel utilise une connexion de passerelle VPN et un réseau virtuel situé sur Azure sert à représenter un réseau local.

![Pare-feu dans un réseau hybride](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

## <a name="key-requirements"></a>Conditions clés

Il existe trois conditions clés pour que ce scénario fonctionne correctement :

- Un itinéraire défini par l’utilisateur sur le sous-réseau spoke qui pointe vers l’adresse IP du pare-feu Azure en tant que passerelle par défaut. La propagation des itinéraires BGP doit être **désactivée** sur cette table de routage.
- Un itinéraire défini par l’utilisateur sur le sous-réseau de passerelle hub doit pointer vers l’adresse IP du pare-feu comme prochain tronçon pour les réseaux spoke.
- Aucun itinéraire défini par l’utilisateur n’est requis sur le sous-réseau du pare-feu Azure, puisqu’il apprend les itinéraires à partir de BGP.
- Assurez-vous de définir **AllowGatewayTransit** lors de l’appairage de VNet-Hub avec VNet-Spoke et **UseRemoteGateways** lors de l’appairage de VNet-Spoke avec VNet-Hub.

Consultez la section [Créer des itinéraires](#create-routes) de ce didacticiel pour voir comment ces itinéraires sont créés.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="declare-the-variables"></a>Déclarer les variables
L’exemple suivant déclare les variables avec les valeurs de ce didacticiel. Dans la plupart des cas, vous devez remplacer les valeurs par les vôtres. Cependant, vous pouvez utiliser ces variables si vous exécutez la procédure pour vous familiariser avec ce type de configuration. Si besoin, modifiez les variables, puis copiez et collez-les dans la console PowerShell.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke VNet

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the OnPrem VNet

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un groupe de ressources qui contiendra toutes les ressources requises pour ce didacticiel :

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

## <a name="create-and-configure-the-firewall-hub-vnet"></a>Créer et configurer le réseau virtuel du hub de pare-feu

Définissez les sous-réseaux qui doivent être inclus dans le réseau virtuel :

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Maintenant, créez le réseau virtuel du hub de pare-feu :

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```
Demandez l’allocation d’une adresse IP publique à la passerelle VPN que vous allez créer pour votre réseau virtuel. Notez que la valeur *AllocationMethod* est **dynamique**. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. Elle est allouée à votre passerelle VPN de façon dynamique. 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```
## <a name="create-and-configure-the-spoke-vnet"></a>Créer et configurer le réseau virtuel spoke

Définissez les sous-réseaux qui doivent être inclus dans le réseau virtuel spoke :

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Créez le réseau virtuel spoke :

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="configure-and-deploy-the-firewall"></a>Configurer et déployer le pare-feu

Déployez maintenant le pare-feu dans le réseau virtuel hub.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzureRmPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzureRmFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Configurer des règles de réseau

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80
$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *
$Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2,$Rule3 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```
### <a name="configure-an-application-rule"></a>Configurer une règle d’application

```azurepowershell
$Rule4 = New-AzureRmFirewallApplicationRule -Name "AllowBing" -Protocol "Http:80","Https:443" `
   -SourceAddress $SNOnpremPrefix -TargetFqdn "bing.com"

$AppRuleCollection = New-AzureRmFirewallApplicationRuleCollection -Name RCApp01 -Priority 100 `
   -Rule $Rule4 -ActionType "Allow"
$Azfw.ApplicationRuleCollections = $AppRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw

```

## <a name="create-and-connect-the-vpn-gateways"></a>Créer et connecter les passerelles VPN

Les réseaux virtuels hub et OnPrem sont connectés via une passerelle VPN.

### <a name="create-a-vpn-gateway-for-the-hub-vnet"></a>Créer une passerelle VPN pour le réseau virtuel hub

Créez la configuration de la passerelle VPN. La configuration de la passerelle VPN définit le sous-réseau et l’adresse IP publique à utiliser.

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Créez maintenant la passerelle VPN pour le réseau virtuel hub. Les configurations de réseau virtuel à réseau virtuel requièrent un VPN de type RouteBased. La création d’une passerelle VPN nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle VPN sélectionnée.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-onprem-vnet"></a>Créer une passerelle VPN pour le réseau virtuel OnPrem

Créez la configuration de la passerelle VPN. La configuration de la passerelle VPN définit le sous-réseau et l’adresse IP publique à utiliser.

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Créez maintenant la passerelle VPN pour le réseau virtuel OnPrem. Les configurations de réseau virtuel à réseau virtuel requièrent un VPN de type RouteBased. La création d’une passerelle VPN nécessite généralement au moins 45 minutes, selon la référence SKU de passerelle VPN sélectionnée.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```
### <a name="create-the-vpn-connections"></a>Créer les connexions VPN
Vous pouvez maintenant créer les connexions VPN entre les passerelles hub et OnPrem.

#### <a name="get-the-vpn-gateways"></a>Obtenir les passerelles VPN
```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Créer les connexions

Dans cette étape, vous créez la connexion du réseau virtuel hub vers le réseau virtuel OnPrem. Une clé partagée est référencée dans les exemples. Vous pouvez utiliser vos propres valeurs pour cette clé partagée. Il est important que la clé partagée corresponde aux deux connexions. La création d’une connexion peut prendre quelques instants.

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Créez la connexion entre les réseaux virtuels OnPrem et hub. Cette étape est similaire à la précédente, sauf que vous créez la connexion du réseau virtuel OnPrem vers le réseau virtuel hub. Vérifiez que les clés partagées correspondent. Après quelques minutes, la connexion est établie.

  ```azurepowershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
#### <a name="verify-the-connection"></a>Vérifier la connexion
 
Vous pouvez vérifier la réussite de la connexion via la cmdlet *Get-AzureRmVirtualNetworkGatewayConnection*, avec ou sans *-Debug*. Utilisez l’exemple d’applet de commande suivant, en configurant les valeurs sur les vôtres. Si vous y êtes invité, sélectionnez **A** pour exécuter **tout**. Dans l’exemple, *-Name* fait référence au nom de la connexion que vous voulez tester.

```azurepowershell
Get-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Une fois la cmdlet exécutée, affichez les valeurs. Dans l’exemple ci-dessous, l’état de la connexion indique *Connecté* et vous pouvez voir les octets d’entrée et de sortie.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="create-and-configure-the-onprem-vnet"></a>Créer et configurer le réseau virtuel OnPrem

Définissez les sous-réseaux qui doivent être inclus dans le réseau virtuel :

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Maintenant, créez le réseau virtuel OnPrem :

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```
Demandez l’allocation d’une adresse IP publique à la passerelle que vous allez créer pour le réseau virtuel. Notez que la valeur *AllocationMethod* est **dynamique**. Vous ne pouvez pas spécifier l’adresse IP que vous souhaitez utiliser. Elle est allouée à votre passerelle de façon dynamique. 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="peer-the-hub-and-spoke-vnets"></a>Appairer les réseaux virtuels hub et spoke

Appairez maintenant les réseaux virtuels spoke et hub.

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```
## <a name="create-routes"></a>Créer des itinéraires

Ensuite, créez deux itinéraires : 
- Un itinéraire à partir du sous-réseau de passerelle hub vers le sous-réseau spoke via l’adresse IP du pare-feu
- Un itinéraire par défaut à partir du sous-réseau spoke via l’adresse IP du pare-feu

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzureRmRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzureRmVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzureRmRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzureRmVirtualNetwork
```
## <a name="create-virtual-machines"></a>Créer des machines virtuelles

Maintenant, créez les machines virtuelles de charge de travail spoke et OnPrem, et placez-les dans les sous-réseaux appropriés.

### <a name="create-the-workload-virtual-machine"></a>Créer la machine virtuelle de charge de travail
Créez une machine virtuelle dans le réseau virtuel spoke, exécutant IIS, sans adresse IP publique, et autorisez les tests Ping entrants.
Lorsque vous y êtes invité, saisissez le nom d’utilisateur et le mot de passe pour la machine virtuelle.

```azurepowershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzureRmNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzureRmVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzureRmVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1

#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1
```

### <a name="create-the-onprem-virtual-machine"></a>Créer la machine virtuelle OnPrem
Il s’agit d’une machine virtuelle simple, à laquelle vous pouvez vous connecter à l’aide du Bureau à distance et de l’adresse IP publique. À partir de là, vous pouvez vous connecter au serveur OnPrem via le pare-feu. Lorsque vous y êtes invité, saisissez le nom d’utilisateur et le mot de passe pour la machine virtuelle.
```azurepowershell
New-AzureRmVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>Tester le pare-feu
Tout d’abord, obtenez et notez l’adresse IP privée pour la machine virtuelle **VM-spoke-01**.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

1. À partir du portail Azure, connectez-vous à la machine virtuelle **VM-Onprem**.
2. Ouvrez une invite de commandes Windows PowerShell sur **VM-Onprem** et effectuez un test ping sur l’adresse IP privée pour **VM-spoke-01**.

   Vous devez obtenir une réponse.
1. Ouvrez un navigateur web sur **VM-Onprem**et accédez à http://\<adresse IP privée de VM-spoke-01\>

   Vous devez voir la page Internet Information Services par défaut.

3. À partir de **VM-Onprem**, ouvrez une session de Bureau à distance sur **VM-spoke-01** à l’adresse IP privée.

   Votre connexion doit réussir et vous devriez pouvoir vous connecter à l’aide de votre nom d’utilisateur et de votre mot de passe.

Maintenant que vous avez vérifié que les règles de pare-feu fonctionnent :

- Vous pouvez effectuer un test ping du serveur sur le réseau virtuel spoke.
- Vous pouvez naviguer sur le serveur web sur le réseau virtuel spoke.
- Vous pouvez vous connecter au serveur sur le réseau virtuel spoke à l’aide de RDP.

Modifiez ensuite l’action de collecte des règles du réseau de pare-feu en **Refuser** pour vérifier que les règles de pare-feu fonctionnent comme prévu. Exécutez le script suivant pour modifier l’action de collections des règles en **Refuser**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

$rcApp = $azfw.GetApplicationRuleCollectionByName("RCApp01")
$rcApp.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```
Maintenant, réexécutez les tests. Cette fois, ils doivent échouer. Fermez les bureaux à distance existants avant de tester les règles modifiées.

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez garder vos ressources de pare-feu pour le prochain didacticiel, ou, si vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources **FW-Hybrid-Test** pour supprimer toutes les ressources associées au pare-feu.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Configurer l’environnement réseau
> * Configurer et déployer le pare-feu
> * Créer les itinéraires
> * Créer les machines virtuelles
> * Tester le pare-feu

Ensuite, vous pouvez surveiller les journaux de Pare-feu Azure.

> [!div class="nextstepaction"]
> [Tutoriel : Surveiller les journaux de Pare-feu Azure](./tutorial-diagnostics.md)
