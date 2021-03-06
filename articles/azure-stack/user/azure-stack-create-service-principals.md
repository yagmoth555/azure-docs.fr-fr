---
title: Créer un principal de service pour Azure Stack | Microsoft Docs
description: Cet article explique comment créer un principal de service utilisable avec le contrôle d’accès en fonction du rôle dans Azure Resource Manager pour gérer l’accès aux ressources.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: 891032e9f0a40b2a9df19ed25ffc19dd81507cab
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2018
ms.locfileid: "45628946"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Permettre à des applications d’accéder à des ressources Azure Stack en créant des principaux de service

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez autoriser une application à accéder à des ressources Azure Stack en créant un principal de service qui utilise Azure Resource Manager. Un principal de service vous permet de déléguer des autorisations spécifiques à l’aide d’un [contrôle d’accès en fonction du rôle](azure-stack-manage-permissions.md).

En guise de meilleure pratique, nous vous recommandons d’utiliser des principaux de service pour vos applications. L’utilisation de principaux de service est préférable à l’exécution d’une application utilisant vos propres informations d’identification pour les raisons suivantes :

* Vous pouvez attribuer au principal de service des autorisations différentes des vôtres. En règle générale, les autorisations d’un principal de service sont strictement limitées à ce que l’application doit faire.
* Il est inutile de modifier les informations d’identification de l’application si votre rôle ou vos responsabilités évoluent.
* Vous pouvez utiliser un certificat pour automatiser l’authentification lors de l’exécution d’un script sans assistance.

## <a name="example-scenario"></a>Exemple de scénario

Vous disposez d’une application de gestion de la configuration qui doit inventorier les ressources Azure à l’aide d’Azure Resource Manager. Vous pouvez créer un principal de service et l’attribuer au rôle Lecteur. Ce rôle donne à l’application un accès en lecture seule aux ressources Azure.

## <a name="getting-started"></a>Prise en main

Les étapes décrites dans cet article sont destinées à vous guider pour effectuer les opérations suivantes :

* Créer un principal de service pour votre application.
* Inscrire votre application et créer une clé d’authentification.
* Assigner votre application à un rôle.

La façon que vous avez configuré Active Directory pour Azure Stack détermine la manière dont vous créez un principal de service. Choisissez l’une des options suivantes :

* Créez un principal de service pour [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Créez un service principal pour les [Services de fédération Active Directory (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

Les étapes d’assignation d’un principal de service à un rôle sont identiques pour Azure AD et AD FS. Après avoir créé le principal de service, vous pouvez [déléguer des autorisations](azure-stack-create-service-principals.md#assign-role-to-service-principal) en l’assignant à un rôle.

## <a name="create-a-service-principal-for-azure-ad"></a>Créer un principal de service pour Azure AD

Si votre Azure Stack utilise Azure AD en tant que le magasin d’identités, vous pouvez créer un service principal en procédant de la même façon que dans Azure, en utilisant le portail Azure.

>[!NOTE]
Avant de commencer à créer un principal de service, vérifiez que vous disposez des [autorisations Azure AD requises](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions).

### <a name="create-service-principal"></a>Créer un principal du service

Pour créer un principal de service pour votre application :

1. Connectez-vous à votre compte Azure via le [portail Azure](https://portal.azure.com).
2. Sélectionnez **Azure Active Directory** > **Enregistrement d’applications** > **Ajouter**.
3. Fournissez un nom et une URL pour l’application. Sélectionnez **Application Web / API** ou **Native** pour le type d’application que vous souhaitez créer. Après avoir défini les valeurs, sélectionnez **Créer**.

### <a name="get-credentials"></a>Récupérer les informations d’identification

Si vous vous connectez par programmation, utilisez l’ID de votre application et une clé d’authentification. Pour obtenir ces valeurs :

1. Dans **Inscriptions d’applications** dans Active Directory, sélectionnez votre application.

2. Copiez l’**ID d’application** et stockez-le dans votre code d’application. Les [exemples d’applications](#sample-applications) utilisent un **ID de client** pour faire référence à l’**ID d’application**.

     ![ID d’application pour l’application](./media/azure-stack-create-service-principal/image12.png)
3. Pour générer une clé d’authentification, sélectionnez **Clés**.

4. Fournissez une description de la clé et la durée de la clé. Lorsque vous avez terminé, sélectionnez **Enregistrer**.

>[!IMPORTANT]
Une fois la clé enregistrée, la clé **VALUE** s’affiche. Notez cette valeur, car vous ne pourrez pas récupérer la clé ultérieurement. Stockez la valeur de la clé à un emplacement où votre application peut la récupérer.

![Avertissement de valeur de clé pour la clé enregistrée.](./media/azure-stack-create-service-principal/image15.png)

L’étape finale consiste à [assigner votre application à un rôle](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Créer un principal de service pour AD FS

Si vous avez déployé Azure Stack en utilisant AD FS en tant que magasin d’identités, vous pouvez utiliser PowerShell pour effectuer les tâches suivantes :

* Créer un principal de service.
* Assigner un principal de service à un rôle.
* Vous connecter en utilisant l’identité du principal de service.

Pour plus d’informations sur la façon de créer le principal du service, consultez [Créer un principal du service pour AD FS](../azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

## <a name="assign-the-service-principal-to-a-role"></a>Assigner le principal de service à un rôle

Pour accéder aux ressources de votre abonnement, vous devez affecter un rôle à l’application. Vous devez décider du rôle qui doit représenter les autorisations appropriées pour l’application. Pour en savoir plus sur les rôles disponibles, consultez [RBAC : rôles intégrés](../../role-based-access-control/built-in-roles.md).

>[!NOTE]
Vous pouvez définir l’étendue d’un rôle au niveau d’un abonnement, d’un groupe de ressources ou d’une ressource. Les autorisations sont héritées des niveaux inférieurs de l’étendue Par exemple, une application avec le rôle Lecteur pour un groupe de ressources peut lire toutes les ressources de ce groupe.

Pour attribuer un rôle à un principal de service, procédez comme suit.

1. Sur le portail Azure Stack, accédez au niveau d’étendue auquel vous voulez assigner l’application. Par exemple, pour affecter un rôle sur l’étendue de l’abonnement, sélectionnez **Abonnements**.

2. Sélectionnez l’abonnement auquel assigner l’application. Dans cet exemple, l’abonnement est Visual Studio Enterprise.

     ![Sélectionner l’abonnement Visual Studio Enterprise pour l’attribution](./media/azure-stack-create-service-principal/image16.png)

3. Sélectionnez **Contrôle d’accès (IAM)** pour l’abonnement.

     ![Sélectionner Contrôle d’accès](./media/azure-stack-create-service-principal/image17.png)

4. Sélectionnez **Ajouter**.

5. Sélectionnez le rôle que vous souhaitez affecter à l’application.

6. Recherchez votre application et sélectionnez-la.

7. Sélectionnez **OK** pour finaliser l’affectation du rôle. Votre application apparaît dans la liste des utilisateurs assignés à un rôle pour cette étendue.

À présent que vous avez créé un principal de service et que vous lui avez attribué un rôle, votre application peut accéder aux ressources Azure Stack.

## <a name="next-steps"></a>Étapes suivantes

[Gérer les autorisations utilisateur](azure-stack-manage-permissions.md)
