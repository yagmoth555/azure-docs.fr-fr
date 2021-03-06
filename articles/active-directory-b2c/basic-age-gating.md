---
title: Utilisation de la vérification de l’âge dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez comment identifier les mineurs à l’aide de votre application.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9a9a86d445deaea4872615f443ad53f76638a758
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056520"
---
#<a name="using-age-gating-in-azure-ad-b2c"></a>Utilisation de la vérification de l’âge dans Azure AD B2C

>[!IMPORTANT]
>Cette fonctionnalité est en préversion privée.  Consultez notre [blog consacré au service](https://blogs.msdn.microsoft.com/azureadb2c/) pour plus d’informations ou contactez AADB2CPreview@microsoft.com.  Tant que cette fonctionnalité n’est pas disponible généralement, ne l’utilisez PAS dans les répertoires de production. Sinon, cela risque d’entraîner une perte de données, ainsi que des modifications inattendues du comportement.  
>

##<a name="age-gating"></a>Vérification de l’âge
La vérification de l’âge vous permet d’utiliser Azure AD B2C pour identifier les mineurs dans votre application.  Vous pouvez choisir de bloquer la connexion de l’utilisateur à l’application ou d’autoriser l’utilisateur à revenir dans l’application avec des revendications supplémentaires qui identifient son âge et l’état du consentement parental.  

>[!NOTE]
>Le consentement parental est suivi dans un attribut utilisateur appelé `consentProvidedForMinor`.  Vous pouvez mettre à jour cette propriété via l’API Graph qui utilisera ce champ lors de la mise à jour de `legalAgeGroupClassification`.
>

##<a name="setting-up-your-directory-for-age-gating"></a>Configuration de votre répertoire pour la vérification de l’âge
Pour utiliser la vérification de l’âge dans un flux d’utilisateur, vous devez configurer votre répertoire avec des propriétés supplémentaires. Cette opération peut être effectuée via `Properties` dans le menu (disponible uniquement dans la préversion publique).  
1. Dans l’extension Azure AD B2C, cliquez sur les **propriétés** de votre locataire dans le menu de gauche.
2. Sous la section **Vérification de l’âge**, cliquez sur le bouton **Configurer**.
3. Attendez que l’opération soit terminée. Votre répertoire est alors configuré pour la vérification de l’âge.

##<a name="enabling-age-gating-in-your-user-flow"></a>Activation de la vérification de l’âge dans votre flux d’utilisateur
Une fois que votre répertoire est configuré pour utiliser la vérification de l’âge, vous pouvez ensuite utiliser cette fonctionnalité dans les flux d’utilisateurs en préversion.  Cette fonctionnalité nécessite des modifications qui la rendent incompatible avec les types de flux d’utilisateurs existants.  Pour activer la vérification de l’âge, procédez comme suit :
1. Créez un flux d’utilisateur en préversion.
2. Une fois qu’il a été créé, accédez à **Propriétés** dans le menu.
3. Dans la section **Vérification de l’âge**, appuyez sur le bouton bascule pour activer la fonctionnalité.
4. Vous pouvez ensuite choisir la façon dont vous souhaitez gérer les utilisateurs qui s’identifient comme étant mineurs.

##<a name="what-does-enabling-age-gating-do"></a>Fonctionnement de la vérification de l’âge
Une fois que la vérification de l’âge est activée dans votre flux d’utilisateur, l’expérience utilisateur change.  Lors de la création d’un compte, les utilisateurs sont maintenant invités à entrer leur date de naissance et leur pays de résidence, ainsi que les attributs d’utilisateur configurés pour le flux d’utilisateur.  Lors de la connexion, les utilisateurs qui n’ont pas encore entré de date de naissance ni de pays de résidence doivent indiquer ces informations à la prochaine connexion.  À partir de ces deux valeurs, Azure AD B2C peut déterminer si l’utilisateur est mineur et mettre à jour le champ `ageGroup`. Les valeurs possibles sont : `null`, `Undefined`, `Minor`, `Adult` et `NotAdult`.  Les champs `ageGroup` et `consentProvidedForMinor` sont ensuite utilisés pour calculer `legalAgeGroupClassification`. 

##<a name="age-gating-options"></a>Options de vérification de l’âge
Vous pouvez demander à Azure AD B2C de bloquer les mineurs sans consentement parental ou de les autoriser en laissant l’application décider ce qu’elle fera de ces utilisateurs.  

###<a name="allowing-minors-without-parental-consent"></a>Autoriser les mineurs sans consentement parental
Pour les flux d’utilisateurs impliquant des opérations de création de compte et/ou de connexion, vous pouvez choisir d’autoriser les mineurs sans consentement dans votre application.  Les mineurs sans consentement parental sont autorisés à se connecter ou à créer un compte normalement et Azure AD B2C émet un jeton d’ID avec la revendication `legalAgeGroupClassification`.  En utilisant cette revendication, vous pouvez choisir l’expérience de ces utilisateurs, par exemple la collecte du consentement parental (et la mise à jour du champ `consentProvidedForMinor`).

###<a name="blocking-minors-without-parental-consent"></a>Bloquer les mineurs sans consentement parental
Pour les flux d’utilisateurs impliquant des opérations de création de compte et/ou de connexion, vous pouvez choisir de bloquer les mineurs sans le consentement de votre application.  Il existe deux options pour la gestion des utilisateurs bloqués dans Azure AD B2C :
* Envoyez du code JSON à l’application : cette option envoie à l’application une réponse indiquant que le mineur a été bloqué.
* Affichez une page d’erreur : l’utilisateur voit une page l’informant qu’il ne peut pas accéder à l’application