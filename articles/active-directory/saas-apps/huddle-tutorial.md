---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Huddle | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Huddle.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8389ba4c-f5f8-4ede-b2f4-32eae844ceb0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.openlocfilehash: d67dbcef1b287ed9552d96338a2591b5f8319532
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434779"
---
# <a name="tutorial-azure-active-directory-integration-with-huddle"></a>Didacticiel : Intégration d’Azure Active Directory avec Huddle

Dans ce didacticiel, vous allez apprendre à intégrer Huddle à Azure Active Directory (Azure AD).

L’intégration de Huddle dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Huddle.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Huddle (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Huddle, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Huddle pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario

Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Huddle depuis la galerie
1. Configuration et test de l’authentification unique Azure AD

## <a name="adding-huddle-from-the-gallery"></a>Ajout de Huddle depuis la galerie
Pour configurer l’intégration de Huddle avec Azure AD, vous devez ajouter Huddle à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter Huddle à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

1. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
1. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

1. Dans la zone de recherche, entrez **Huddle**.

    ![Création d’un utilisateur de test Azure AD](./media/huddle-tutorial/tutorial_huddle_search.png)

1. Dans le panneau des résultats, sélectionnez **Huddle**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/huddle-tutorial/tutorial_huddle_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Huddle avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Huddle équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Huddle associé doit être établie.

Dans Huddle, attribuez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Huddle, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.

1. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.

1. **[Création d’un utilisateur de test Huddle](#creating-a-huddle-test-user)** pour obtenir un équivalent de Britta Simon dans Huddle lié à la représentation Azure AD de l’utilisateur.

1. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.

1. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Huddle.

**Pour configurer l’authentification unique Azure AD avec Huddle, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Huddle**, cliquez sur **Authentification unique**.

    ![Configurer l'authentification unique][4]

1. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l'authentification unique](./media/huddle-tutorial/tutorial_huddle_samlbase.png)

1. Dans la section **Domaine et URL Huddle**, procédez comme suit :

    ![Configurer l'authentification unique](./media/huddle-tutorial/tutorial_huddle_url.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `http://<company name>.huddle.com`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL d’authentification réelle. Contactez [l’équipe de support technique Huddle](https://huddle.zendesk.com) pour obtenir cette valeur. 

1. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/huddle-tutorial/tutorial_huddle_certificate.png) 

1. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l'authentification unique](./media/huddle-tutorial/tutorial_general_400.png)

1. Dans la section **Configuration de Huddle**, cliquez sur **Configurer Huddle** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide** 

    ![Configurer l'authentification unique](./media/huddle-tutorial/tutorial_huddle_configure.png) 
    
1. Pour configurer l’authentification unique côté Huddle, vous devez envoyer le **Certificat** téléchargé, **l’ID d’entité SAML** et **l’URL du service d’authentification unique SAML** à [l’équipe de support technique Huddle](https://huddle.zendesk.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.  
   
    >[!NOTE]
    > L’authentification unique doit être activée par l’équipe du support technique Huddle. Vous recevrez une notification une fois la configuration terminée. 
    > 

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 
   
### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/huddle-tutorial/create_aaduser_01.png) 

1. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/huddle-tutorial/create_aaduser_02.png) 

1. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/huddle-tutorial/create_aaduser_03.png) 

1. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/huddle-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-huddle-test-user"></a>Création d’un utilisateur de test Huddle

Pour se connecter à Huddle, les utilisateurs d’Azure AD doivent être configurés dans Huddle. Dans le cas de Huddle, l’approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **Huddle** en tant qu’administrateur.
1. Cliquez sur **Workspace**.
1. Cliquez sur **People \> Invite People**.
   
   ![Personnes](./media/huddle-tutorial/IC787838.png "Personnes")

1. Dans la section **Create a new invitation** , procédez comme suit :
   
   ![New Invitation](./media/huddle-tutorial/IC787839.png "New Invitation")
   
   a. Dans la liste **Choose a team to invite people to join**, sélectionnez **team**.

   b. Entrez l’**adresse e-mail** associée au compte Azure AD valide que vous souhaitez configurer dans la zone de texte **Entrer l’adresse e-mail des personnes que vous souhaitez inviter**.

   c. Cliquez sur **Invite**.   
   
    >[!NOTE]
    > Le titulaire du compte Azure AD reçoit alors un message électronique contenant un lien pour confirmer le compte avant qu’il ne soit activé. 
    > 

>[!NOTE]
>Vous pouvez utiliser n’importe quels autres outils ou API de création de compte d’utilisateur Huddle fourni par ce site pour configurer des comptes d’utilisateurs Azure AD. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Huddle.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Huddle, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

1. Dans la liste des applications, sélectionnez **Huddle**.

    ![Configurer l'authentification unique](./media/huddle-tutorial/tutorial_huddle_app.png) 

1. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

1. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

1. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

1. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

1. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Huddle dans le panneau d’accès, vous accédez automatiquement à la page de connexion Huddle.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/huddle-tutorial/tutorial_general_01.png
[2]: ./media/huddle-tutorial/tutorial_general_02.png
[3]: ./media/huddle-tutorial/tutorial_general_03.png
[4]: ./media/huddle-tutorial/tutorial_general_04.png
[100]: ./media/huddle-tutorial/tutorial_general_100.png
[200]: ./media/huddle-tutorial/tutorial_general_200.png
[201]: ./media/huddle-tutorial/tutorial_general_201.png
[202]: ./media/huddle-tutorial/tutorial_general_202.png
[203]: ./media/huddle-tutorial/tutorial_general_203.png
