---
title: 'Didacticiel : Intégration d’Attendance Management Services avec Azure Active Directory | Microsoft Azure'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Attendance Management Services.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.openlocfilehash: c5422c9894c66348d571b757e50073d2a5501c7b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440096"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Didacticiel : Intégration d’Attendance Management Services avec Azure Active Directory

Dans ce tutoriel, vous allez apprendre à intégrer Attendance Management Services avec Azure Active Directory (Azure AD).

L’intégration d’Attendance Management Services avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Attendance Management Services.
- Vous pouvez permettre à vos utilisateurs de se connecter automatiquement à Attendance Management Services (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Attendance Management Services avec Azure AD, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Attendance Management Services pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Attendance Management Services à partir de la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-attendance-management-services-from-the-gallery"></a>Ajout d’Attendance Management Services à partir de la galerie
Pour configurer l’intégration d’Attendance Management Services avec Azure AD, vous devez ajouter Attendance Management Services à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Attendance Management Services à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

1. Dans la zone de recherche, tapez **Attendance Management Services**, sélectionnez **Attendance Management Services** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Attendance Management Services dans la liste des résultats](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Attendance Management Services avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Attendance Management Services équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Attendance Management Services associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Attendance Management Services, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
1. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
1. **[Créer un utilisateur de test Attendance Management Services](#create-an-attendance-management-service-test-user)** pour avoir un homologue de Britta Simon dans Attendance Management Services lié à sa représentation dans Azure AD.
1. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
1. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Attendance Management Services.

**Pour configurer l’authentification unique Azure AD avec Attendance Management Services, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Attendance Management Services**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_samlbase.png)

1. Dans la section **Domaine et URL Attendance Management Services**, effectuez les étapes suivantes :

    ![Informations du domaine et des URL Attendance Management Services pour l’authentification unique](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://id.obc.jp/<tenant information >/`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Contactez l’[équipe de support technique d’Attendance Management Services](http://www.obcnet.jp/) pour obtenir ces valeurs.

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/attendancemanagementservices-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration d’Attendance Management Services**, cliquez sur **Configurer Attendance Management Services** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**

    ![Configuration d’Attendance Management Services](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_configure.png) 

1. Dans une autre fenêtre de navigateur, connectez-vous au site d’entreprise Attendance Management Services en tant qu’administrateur.

1. Cliquez sur **SAML authentication** sous la **section Security management**.

    ![Configuration d’Attendance Management Services](./media/attendancemanagementservices-tutorial/user1.png)

1. Procédez comme suit :

    ![Configuration d’Attendance Management Services](./media/attendancemanagementservices-tutorial/user2.png)

    a. Sélectionnez **Use SAML authentication**.

    b. Dans la zone de texte **Identifier**, collez la valeur d’**ID d’entité SAML** que vous avez copiée sur le portail Azure. 

    c. Dans la zone de texte **Authentication endpoint URL**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure.

    d. Cliquez sur **Select a file** pour charger le certificat que vous avez téléchargé à partir d’Azure AD.

    e. Sélectionnez **Disable password authentication**.

    f. Cliquez sur **Registration**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application. Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/attendancemanagementservices-tutorial/create_aaduser_01.png)

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/attendancemanagementservices-tutorial/create_aaduser_02.png)

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/attendancemanagementservices-tutorial/create_aaduser_03.png)

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/attendancemanagementservices-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-attendance-management-services-test-user"></a>Créer un utilisateur de test Attendance Management Services

Pour permettre aux utilisateurs d’Azure AD de se connecter à Attendance Management Services, ils doivent être approvisionnés dans Attendance Management Services. Dans le cas d’Attendance Management Services, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise Attendance Management Services en tant qu’administrateur.

1. Cliquez sur **User management** sous la **section Security management**.

    ![Ajouter un employé](./media/attendancemanagementservices-tutorial/user5.png)

1. Cliquez sur **New rules login**.

    ![Ajouter un employé](./media/attendancemanagementservices-tutorial/user3.png)

1. Dans la section **OBCiD information**, procédez comme suit :

    ![Ajouter un employé](./media/attendancemanagementservices-tutorial/user4.png)

    a. Dans la zone de texte **OBCiD**, tapez l’adresse e-mail de l’utilisateur, par exemple, **BrittaSimon@contoso.com**.

    b. Dans la zone de texte **Password** (Mot de passe), tapez le mot de passe de l’utilisateur.

    c. Cliquez sur **Registration**.


### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Attendance Management Services.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Attendance Management Services, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Attendance Management Services**.

    ![Lien Attendance Management Services dans la liste des applications](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_app.png)  

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Attendance Management Services dans le volet d’accès, vous devez être connecté automatiquement à votre application Attendance Management Services.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/attendancemanagementservices-tutorial/tutorial_general_01.png
[2]: ./media/attendancemanagementservices-tutorial/tutorial_general_02.png
[3]: ./media/attendancemanagementservices-tutorial/tutorial_general_03.png
[4]: ./media/attendancemanagementservices-tutorial/tutorial_general_04.png

[100]: ./media/attendancemanagementservices-tutorial/tutorial_general_100.png

[200]: ./media/attendancemanagementservices-tutorial/tutorial_general_200.png
[201]: ./media/attendancemanagementservices-tutorial/tutorial_general_201.png
[202]: ./media/attendancemanagementservices-tutorial/tutorial_general_202.png
[203]: ./media/attendancemanagementservices-tutorial/tutorial_general_203.png

