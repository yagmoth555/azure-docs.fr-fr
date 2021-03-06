---
title: 'Didacticiel : Intégration d’Azure Active Directory dans &frankly | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et &frankly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1d702060-1b89-4e9d-9f01-ede4f1171c73
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 388203903f33d969a7796cf466078159e9b73ad0
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218098"
---
# <a name="tutorial-azure-active-directory-integration-with-frankly"></a>Didacticiel : Intégration d’Azure Active Directory dans &frankly

Dans ce didacticiel, vous allez apprendre à intégrer &frankly à Azure Active Directory (Azure AD).

L’intégration d’Azure AD dans &frankly offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à &frankly.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à &frankly (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD dans &frankly, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement &frankly pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de &frankly à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-frankly-from-the-gallery"></a>Ajout de &frankly à partir de la galerie
Pour configurer l’intégration de &frankly dans Azure AD, vous devez ajouter &frankly depuis la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter &amp;frankly à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

4. Dans la zone de recherche, tapez **&frankly**.

    ![Création d’un utilisateur de test Azure AD](./media/andfrankly-tutorial/tutorial_andfrankly_search.png)

5. Dans le volet de résultats, sélectionnez **&frankly**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/andfrankly-tutorial/tutorial_andfrankly_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec &frankly, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur &frankly correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur &frankly correspondant doit être établie.

Dans &frankly, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec &frankly, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test &amp;frankly](#creating-a-frankly-test-user)** pour avoir un équivalent de Britta Simon dans &amp;frankly lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application &frankly.

**Pour configurer l’authentification unique Azure AD avec &amp;frankly, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **&frankly**, cliquez sur **Authentification unique**.

    ![Configure Single Sign-On][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configure Single Sign-On](./media/andfrankly-tutorial/tutorial_andfrankly_samlbase.png)

3. Dans la section **Domaine et URL &frankly**, si vous souhaitez configurer l’application en mode démarré par le **fournisseur d’identité**, suivez les étapes ci-dessous :

    ![Configure Single Sign-On](./media/andfrankly-tutorial/tutorial_andfrankly_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/metadata.php/<tenant id>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://andfrankly.com/saml/simplesaml/www/module.php/saml/sp/saml2-acs.php/<tenant id>`

4. Cliquez sur **Afficher les paramètres d’URL avancés**. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service** :

    ![Configure Single Sign-On](./media/andfrankly-tutorial/tutorial_andfrankly_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://andfrankly.com/saml/okta/?saml_sso=<tenant id>`
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de connexion et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe du support &frankly](mailto:help@andfrankly.com).

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configure Single Sign-On](./media/andfrankly-tutorial/tutorial_andfrankly_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Configure Single Sign-On](./media/andfrankly-tutorial/tutorial_general_400.png)

7. Pour configurer l’authentification unique du côté **&frankly**, vous devez envoyer le fichier **XML de métadonnées** téléchargé à [l’équipe du support &frankly](mailto:help@andfrankly.com). 

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/andfrankly-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/andfrankly-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/andfrankly-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/andfrankly-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-frankly-test-user"></a>Création d’un utilisateur de test dans &frankly

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans &frankly. Pour ajouter des utilisateurs dans la plateforme &frankly, contactez [l’équipe du support &frankly](mailto:help@andfrankly.com).

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à &frankly.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à &amp;frankly, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **&frankly**.

    ![Configure Single Sign-On](./media/andfrankly-tutorial/tutorial_andfrankly_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette &frankly dans le volet d’accès vous connecte automatiquement à votre application &frankly.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/andfrankly-tutorial/tutorial_general_01.png
[2]: ./media/andfrankly-tutorial/tutorial_general_02.png
[3]: ./media/andfrankly-tutorial/tutorial_general_03.png
[4]: ./media/andfrankly-tutorial/tutorial_general_04.png

[100]: ./media/andfrankly-tutorial/tutorial_general_100.png

[200]: ./media/andfrankly-tutorial/tutorial_general_200.png
[201]: ./media/andfrankly-tutorial/tutorial_general_201.png
[202]: ./media/andfrankly-tutorial/tutorial_general_202.png
[203]: ./media/andfrankly-tutorial/tutorial_general_203.png

