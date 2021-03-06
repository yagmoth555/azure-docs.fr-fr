---
title: Transfert de Syslog Azure Stack
description: Découvrez comment intégrer Azure Stack aux solutions de contrôle à l’aide du transfert de Syslog
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/14/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 8e59f2e7e2fceda7f30e12571cd9e2a552f76231
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2018
ms.locfileid: "41946428"
---
# <a name="azure-stack-datacenter-integration---syslog-forwarding"></a>Intégration au centre de données Azure Stack - transfert de Syslog

Cet article vous montre comment utiliser Syslog pour intégrer l’infrastructure Azure Stack avec une ou plusieurs solutions de sécurité externes déjà déployées dans votre centre de données. Par exemple, un système SIEM (Security Information and Event Management). Le canal syslog présente des audits, des alertes et des journaux de sécurité provenant de tous les composants de l’infrastructure Azure Stack. Utilisez le transfert de Syslog pour intégrer des solutions de surveillance de la sécurité et/ou pour récupérer la totalité des audits, alertes et journaux de sécurité afin de les stocker pour la rétention. 

À compter de la mise à jour 1805, Azure Stack dispose d’un client syslog intégré qui, une fois configuré, émet des messages syslog avec la charge utile au format d’événement courant (CEF). 

> [!IMPORTANT]
> Le transfert de Syslog est en version préliminaire. Vous ne devez pas l’utiliser dans votre environnement de production. 

Le diagramme suivant montre les principaux composants qui font partie de l’intégration de syslog.

![Diagramme de transfert de Syslog](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>Configuration du transfert de Syslog

Le client syslog dans Azure Stack prend en charge les configurations suivantes :

1. **Syslog via TCP, avec authentification mutuelle (client et serveur) et chiffrement TLS 1.2 :** dans cette configuration, le serveur syslog et le client syslog peuvent vérifier leur identité mutuelle par le biais de certificats. Les messages sont envoyés via un canal chiffré TLS 1.2.

2. **Syslog via TCP avec authentification serveur et chiffrement TLS 1.2 :** dans cette configuration, le client syslog peut vérifier l’identité du serveur syslog via un certificat. Les messages sont envoyés via un canal chiffré TLS 1.2.

3. **Syslog via TCP sans chiffrement :** dans cette configuration, ni le serveur syslog ni le client syslog ne vérifie leur identité mutuelle. Les messages sont envoyés en texte clair via TCP.

4. **Syslog via UDP sans chiffrement :** dans cette configuration, ni le serveur syslog ni le client syslog ne vérifie leur identité mutuelle. Les messages sont envoyés en texte clair via UDP.

> [!IMPORTANT]
> Microsoft recommande d’utiliser le protocole TCP avec authentification et chiffrement (configuration #1 ou, au minimum, la configuration #2) pour les environnements de production afin de vous protéger contre les attaques de type interception (man-in-the-middle) et d’écoute des messages.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Applets de commande pour configurer le transfert de Syslog
La configuration du transfert de Syslog requiert l’accès au point de terminaison privilégié (PEP). Deux applets de commande PowerShell ont été ajoutées au PEP pour configurer le transfert de Syslog :


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] 
```
#### <a name="cmdlets-parameters"></a>Paramètres des applets de commande

Paramètres pour l’applet de commande *Set-SyslogServer* :

| Paramètre | Description | type |
|---------|---------| ---------|
| *ServerName* | Nom de domaine complet ou adresse IP du serveur syslog | Chaîne |
|*NoEncryption*| Forcer le client à envoyer des messages syslog en texte clair | indicateur | 
|*SkipCertificateCheck*| Ignorer la validation du certificat fourni par le serveur syslog pendant la négociation TLS initiale | indicateur |
|*SkipCNCheck*| Ignorer la validation de la valeur Nom courant du certificat fourni par le serveur syslog pendant la négociation TLS initiale | indicateur |
|*UseUDP*| Utiliser syslog avec le protocole de transport UDP |indicateur |
|*Remove*| Supprimer la configuration du serveur à partir du client et arrêter le transfert de Syslog| indicateur |

Paramètres pour l’applet de commande *Set-SyslogClient* :
| Paramètre | Description | type |
|---------|---------| ---------|
| *pfxBinary* | Fichier PFX contenant le certificat à utiliser par le client en tant qu’identité pour s’authentifier auprès du serveur syslog  | Byte[] |
| *CertPassword* |  Mot de passe pour importer la clé privée associée au fichier pfx | SecureString |
|*RemoveCertificate* | Supprimer le certificat du client | indicateur|

### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>Configuration du transfert de Syslog avec TCP, authentification mutuelle et chiffrement TLS 1.2

Dans cette configuration, le client syslog dans Azure Stack transfère les messages au serveur syslog via TCP, avec chiffrement TLS 1.2. Pendant la négociation initiale, le client vérifie que le serveur fournit un certificat valide et approuvé ; de même, le client fournit également un certificat au serveur comme preuve de son identité. Cette configuration est la plus sécurisée car elle fournit une validation complète de l’identité du client et du serveur, et envoie des messages via un canal chiffré. 

> [!IMPORTANT]
> Microsoft recommande fortement d’utiliser cette configuration pour les environnements de production. 

Pour configurer le transfert de Syslog avec TCP, authentification mutuelle et chiffrement TLS 1.2, exécutez ces deux applets de commande :
```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```
Le certificat client doit avoir la même racine que celui fourni lors du déploiement d’Azure Stack. Il doit également contenir une clé privée.

```powershell
##Example on how to set your syslog client with the ceritificate for mutual authentication. 
##Run these cmdlets from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword 
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>Configuration du transfert de Syslog avec TCP, authentification du serveur et chiffrement TLS 1.2

Dans cette configuration, le client syslog dans Azure Stack transfère les messages au serveur syslog via TCP, avec chiffrement TLS 1.2. Pendant la négociation initiale, le client vérifie également que le serveur fournit un certificat valide et approuvé. Cela empêche le client d’envoyer des messages vers des destinations non approuvées.
TCP avec authentification et chiffrement est la configuration par défaut et représente le niveau minimal de sécurité que Microsoft recommande pour un environnement de production. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server>
```

Si vous souhaitez tester l’intégration de votre serveur syslog avec le client Azure Stack à l’aide d’un certificat auto-signé et/ou non approuvé, vous pouvez utiliser ces indicateurs pour ignorer la validation du serveur effectuée par le client pendant la négociation initiale.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCNCheck
 
 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -SkipCertificateCheck
```
> [!IMPORTANT]
> Microsoft déconseille l’utilisation de l’indicateur -SkipCertificateCheck pour les environnements de production. 


### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>Configuration du transfert de Syslog avec TCP et aucun chiffrement

Dans cette configuration, le client syslog dans Azure Stack transfère les messages au serveur syslog via TCP, sans chiffrement. Le client ne vérifie pas l’identité du serveur et lui fournit sa propre identité pour vérification. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -NoEncryption
```
> [!IMPORTANT]
> Microsoft déconseille d’utiliser cette configuration pour les environnements de production. 


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>Configuration du transfert de Syslog avec UDP et aucun chiffrement

Dans cette configuration, le client syslog dans Azure Stack transfère les messages au serveur syslog via UDP, sans chiffrement. Le client ne vérifie pas l’identité du serveur et lui fournit sa propre identité pour vérification. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -UseUDP
```
La configuration UDP sans chiffrement est la plus facile, mais elle ne fournit aucune protection contre les attaques de type interception (man-in-the-middle) et d’écoute des messages. 

> [!IMPORTANT]
> Microsoft déconseille d’utiliser cette configuration pour les environnements de production. 


## <a name="removing-syslog-forwarding-configuration"></a>Suppression de la configuration du transfert de Syslog

Pour supprimer complètement de la configuration du serveur syslog et arrêter le transfert de Syslog :

**Supprimez la configuration du serveur syslog du client**

```PowerShell  
Set-SyslogServer -Remove
```

**Supprimez le certificat client du client**

```PowerShell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>Vérification de la configuration syslog

Si vous avez connecté avec succès le client syslog à votre serveur syslog, vous devriez commencer à recevoir des événements. Si vous ne recevez aucun événement, vérifiez la configuration de votre client syslog en exécutant les applets de commande suivantes :

**Vérifiez la configuration du serveur dans le client syslog**

```PowerShell  
Get-SyslogServer
```

**Vérifiez la configuration du certificat dans le client syslog**

```PowerShell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Schéma du message syslog

Le transfert Syslog de l’infrastructure Azure Stack envoie des messages au format CEF (Common Event Format).
Chaque message syslog est structuré selon ce schéma : 

```Syslog
<Time> <Host> <CEF payload>
```

La charge utile CEF est basée sur la structure ci-dessous, mais le mappage pour chaque champ varie selon le type de message (événements Windows, alerte créée, alerte fermée).

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0 
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack
* Device Version: 1.0
```

### <a name="cef-mapping-for-windows-events"></a>Mappage CEF pour les événements Windows
```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tableau de sévérité pour les événements Windows : 
| Valeur de gravité CEF | Niveau d’événements Windows | Valeur numérique |
|--------------------|---------------------| ----------------|
|0|Undefined|Valeur : 0. Indique les journaux à tous les niveaux|
|10|Critique|Valeur : 1. Indique les journaux pour une alerte critique|
|8|Error| Valeur : 2. Indique les journaux pour une erreur|
|5.|Avertissement|Valeur : 3. Indique les journaux pour un avertissement|
|2|Information|Valeur : 4. Indique les journaux pour un message d’information|
|0|Détaillé|Valeur : 5. Indique les journaux à tous les niveaux|

Table d’extension personnalisée pour les événements Windows dans Azure Stack :
| Nom de l’extension personnalisée | Exemple d’événement Windows | 
|-----------------------|---------|
|MasChannel | System|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| svchost!!4132,G,0!!!!EseDiskFlushConsistency!!ESENT!!0x800000|
|MasEventDescription| Les paramètres de stratégie de groupe pour l’utilisateur ont été traités avec succès. Aucune modification n’a été détectée depuis le dernier traitement réussi de la stratégie de groupe.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |Succès de l’audit|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |info|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| Création de processus|
|MasUserData|KB4093112!!5112!!Installed!!0x0!!WindowsUpdateAgent Xpath: /Event/UserData/*|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>Mappage CEF pour les alertes créées
```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```
Tableau de gravité des alertes :
| Severity | Level |
|----------|-------|
|0|Undefined|
|10|Critique|
|5.|Avertissement|

Table d’extension personnalisée pour les alertes créées dans Azure Stack :
| Nom de l’extension personnalisée | Exemples | 
|-----------------------|---------|
|MasEventDescription|DESCRIPTION : compte d’utilisateur \<TestUser\> créé pour \<DomaineTest\>. Il existe un risque potentiel de sécurité. --CORRECTION : contactez le support technique. L’Assistance client est nécessaire pour résoudre ce problème. N’essayez pas de résoudre ce problème sans leur assistance. Avant d’ouvrir une demande de support, démarrez le processus de collecte du fichier journal à l’aide des instructions disponibles à l’adresse https://aka.ms/azurestacklogfiles |

### <a name="cef-mapping-for-alerts-closed"></a>Mappage CEF pour les alertes fermées
```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

L’exemple ci-dessous montre un message syslog avec une charge utile CEF :
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```
## <a name="next-steps"></a>Étapes suivantes

[Stratégie de maintenance](azure-stack-servicing-policy.md)