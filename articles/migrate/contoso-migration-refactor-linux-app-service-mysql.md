---
title: Refactoriser une application Service Desk Linux Contoso sur Azure App Service et Azure MySQL | Microsoft Docs
description: Découvrez comment Contoso refactorise une application Linux locale en la migrant vers Azure App Service à l’aide de GitHub pour la couche Web et Azure SQL Database.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: raynew
ms.openlocfilehash: 7deb22a0fd552ee697f6b39988d5f3ac01a29702
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782190"
---
# <a name="contoso-migration-refactor-a-contoso-linux-service-desk-app-to-multiple-regions-with-azure-app-service-traffic-manager-and-azure-mysql"></a>Migration de Contoso : Refactoriser une application Service Desk Linux Contoso dans plusieurs régions avec Azure App Service, Traffic Manager et Azure MySQL

Cet article explique comment Contoso refactorise son application Service Desk Linux locale à deux niveaux (osTicket) en la migrant vers Azure App Service avec intégration GitHub et Azure MySQL.

Ce document fait partie d’une série d’articles qui montrent comment la société fictive Contoso migre ses ressources locales vers le cloud Microsoft Azure. La série comprend des informations d’arrière-plan et des scénarios qui montrent comment configurer une infrastructure de migration et exécuter différents types de migrations. Les scénarios augmentent en complexité. Nous ajouterons des articles au fil du temps.

**Article** | **Détails** | **État**
--- | --- | ---
[Article 1 : vue d’ensemble](contoso-migration-overview.md) | Vue d’ensemble de la série d’articles, de la stratégie de migration de Contoso et des exemples d’applications utilisés dans la série. | Disponible
[Article 2 : Déployer une infrastructure Azure](contoso-migration-infrastructure.md) | Contoso prépare son infrastructure locale et son infrastructure Azure pour la migration. La même infrastructure est utilisée pour tous les articles de migration de la série. | Disponible
[Article 3 : Évaluer les ressources locales à migrer vers Azure](contoso-migration-assessment.md)  | Contoso évalue son application locale SmartHotel360 qui s’exécute sur VMware. Contoso évalue les machines virtuelles de l’application à l’aide du service Azure Migrate et la base de données SQL Server de l’application à l’aide de l’Assistant Migration de données. | Disponible
[Article 4 : Réhéberger une application sur une machine virtuelle Azure et SQL Database Managed Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso exécute une migration lift-and-shift vers Azure pour son application SmartHotel360 locale. Contoso migre la machine virtuelle frontend de l’application à l’aide d’[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migre la base de données de l’application vers Azure SQL Database Managed Instance à l’aide [d’Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | Disponible   
[Article 5 : Réhéberger une application sur des machines virtuelles Azure](contoso-migration-rehost-vm.md) | Contoso migre les machines virtuelles de son application SmartHotel360 vers des machines virtuelles Azure avec le service Site Recovery. | Disponible
[Article 6 : Réhéberger une application sur des machines virtuelles Azure et dans un groupe de disponibilité SQL Server AlwaysOn](contoso-migration-rehost-vm-sql-ag.md) | Contoso migre l’application SmartHotel360. Contoso utilise Site Recovery pour migrer les machines virtuelles d’application. La société utilise Database Migration Service pour migrer la base de données d’application vers un cluster SQL Server protégé par un groupe de disponibilité AlwaysOn. | Disponible 
[Article 7 : ré-héberger une application Linux sur des machines virtuelles Azure](contoso-migration-rehost-linux-vm.md) | Contoso effectue une migration lift-and-shift de l’application osTicket Linux vers des machines virtuelles Azure à l’aide d’Azure Site Recovery. | Disponible
[Article 8 : Réhéberger une application Linux sur des machines virtuelles Azure et Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migre l’application osTicket Linux vers des machines virtuelles Azure à l’aide d’Azure Site Recovery et la base de données de l’application vers une instance Azure MySQL Server à l’aide de MySQL Workbench. | Disponible
[Article 9 : Refactoriser une application sur Azure Web Apps et Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migre l’application SmartHotel360 vers Azure Web App et la base de données de l’application vers une instance Azure SQL Server avec l’Assistant Migration de données. | Disponible
Article 10 : Refactoriser une application Linux sur Azure Web Apps et Azure MySQL | Contoso migre son application osTicket Linux vers Azure Web App dans plusieurs régions Azure à l’aide d’Azure Traffic Manager, intégré à GitHub de façon à assurer une livraison continue. Contoso migre la base de données d’application vers une instance d’Azure Database pour MySQL. | Cet article
[Article 11 : Refactoriser TFS sur VSTS](contoso-migration-tfs-vsts.md) | Contoso migre son déploiement local de Team Foundation Server vers Visual Studio Team Services dans Azure. | Disponible
[Article 12 : Réarchitecturer une application sur des conteneurs Azure et Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migre son application SmartHotel vers Azure. Ensuite, l’entreprise restructure la couche web de l’application comme un conteneur Windows s’exécutant dans Azure Service Fabric, et la base de données avec Azure SQL Database. | Disponible
[Article 13 : Regénérer une application dans Azure](contoso-migration-rebuild.md) | Contoso regénère son application SmartHotel360 en utilisant une série de fonctionnalités et de services Azure, notamment Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services et Azure Cosmos DB. | Disponible

Dans cet article, Contoso migre une application de Service Desk Linux Apache MySQL PHP (LAMP) (osTicket) à deux niveaux vers Azure. Si vous souhaitez utiliser cette application open source, vous pouvez la télécharger à partir de [GitHub](https://github.com/osTicket/osTicket).


## <a name="business-drivers"></a>Axes stratégiques

L’équipe informatique a travaillé en étroite collaboration avec des partenaires commerciaux pour comprendre le résultat souhaité :

- **Répondre à la croissance de l’entreprise** : Contoso croît et accède à de nouveaux marchés. L’entreprise a besoin d’agents de service client supplémentaires. 
- **Mise à l’échelle** : La solution doit être conçue afin que Contoso puisse ajouter des agents de service client parallèlement à la croissance de l’entreprise.
- **Augmenter la résilience** : Dans le passé, les problèmes liés au système affectaient uniquement les utilisateurs internes. Avec le nouveau modèle d’entreprise, les utilisateurs externes sont affectés, et Contoso a besoin que l’application soit opérationnelle à tout moment.

## <a name="migration-goals"></a>Objectifs de la migration

L’équipe cloud de Contoso a défini les objectifs de cette migration, afin de déterminer la meilleure méthode de migration :

- L’application doit être mise à l’échelle au-delà des performances et de la capacité locales actuelles.  Contoso déplace l’application afin de tirer parti de la mise à l’échelle à la demande offerte par Azure.
- Ils souhaitent déplacer la base de code de l’application vers un pipeline de livraison continue.  À mesure que les changements de l’application sont poussés sur GitHub, Contoso veut les déployer sans que le personnel chargé des opérations n’ait à effectuer aucune tâche.
- L’application doit être résiliente avec des capacités de croissance et de basculement. Contoso veut déployer l’application dans deux régions Azure différentes et la configurer pour une mise à l’échelle automatique.
- Contoso souhaite réduire au minimum les tâches d’administration de base de données après la migration de l’application vers le cloud.

## <a name="solution-design"></a>Conception de la solution
Après avoir défini précisément les objectifs et exigences, Contoso conçoit et examine une solution de déploiement, et identifie le processus de migration, notamment les services Azure à utiliser pour la migration.


## <a name="current-architecture"></a>Architecture actuelle

- L’application est hiérarchisée sur deux machines virtuelles (OSTICKETWEB et OSTICKETMYSQL).
- Les machines virtuelles sont situées sur un hôte VMware ESXi **contosohost1.contoso.com** (version 6.5).
- L’environnement VMware est géré par le serveur vCenter Server 6.5 (**vcenter.contoso.com**) s’exécutant sur une machine virtuelle.
- Contoso dispose d’un centre de données local (contoso-datacenter), avec un contrôleur de domaine local (**contosodc1**).

![Architecture actuelle](./media/contoso-migration-refactor-linux-app-service-mysql/current-architecture.png) 


## <a name="proposed-architecture"></a>Architecture proposée

Voici l’architecture proposée :

- L’application de la couche web sur OSTICKETWEB sera migrée en créant un Azure App Service dans deux régions Azure. Azure App Service pour Linux sera implémenté à l’aide du conteneur Docker PHP 7.0.
- Le code d’application sera déplacé vers GitHub, et Azure Web App sera configuré pour une livraison continue avec GitHub.
- Les serveurs Azure App seront déployés dans les régions primaire (USA Est 2) et secondaire (USA Centre).
- Traffic Manager sera configuré devant les deux applications web Azure dans les deux régions.
- Traffic Manager sera configuré en mode prioritaire pour forcer le trafic à passer par USA Est 2.
- Si le serveur Azure App dans USA Est 2 bascule hors connexion, les utilisateurs peuvent accéder à l’application ayant basculé dans USA Centre.
- La base de données de l’application sera migrée vers le service PaaS Azure MySQL à l’aide d’outils MySQL Workbench. La base de données locale sera sauvegardée localement, et restaurée directement dans Azure MySQL.
- La base de données résidera dans la région primaire USA Est 2, sur le sous-réseau de la base de données (PROD-DB-EUS2) sur le réseau de production (VNET-PROD-EUS2) :
- Dans la mesure où la migration concerne une charge de travail de production, les ressources Azure résideront dans le groupe de ressources de production **ContosoRG**.
- La ressource Traffic Manager sera déployée dans le groupe de ressources d’infrastructure de Contoso **ContosoInfraRG**.
- Les machines virtuelles locales dans le centre de données Contoso seront désaffectées une fois la migration terminée.


![Architecture du scénario](./media/contoso-migration-refactor-linux-app-service-mysql/proposed-architecture.png) 


## <a name="migration-process"></a>Processus de migration

Contoso va effectuer le processus de migration comme suit :

1. Dans un premier temps, les administrateurs de Contoso configurent l’infrastructure Azure, notamment le provisionnement d’Azure App Services, la configuration de Traffic Manager et le provisionnement d’une instance Azure MySQL.
2. Après avoir préparé Azure, ils migrent la base de données à l’aide de MySQL Workbench. 
3. Une fois la base de données en cours d’exécution dans Azure, ils configurent un dépôt privé GitHub pour Azure App Service avec livraison continue et le chargent avec l’application osTicket.
4. Dans le portail Azure, ils chargent l’application de GitHub vers le conteneur Docker exécutant Azure App Service. 
5. Ils ajustent les paramètres DNS et configure la mise à l’échelle automatique de l’application.

![Processus de migration](./media/contoso-migration-refactor-linux-app-service-mysql/migration-process.png) 


### <a name="azure-services"></a>Services Azure

**Service** | **Description** | **Coût**
--- | --- | ---
[Azure App Service](https://azure.microsoft.com/services/app-service/) | Le service exécute et met à l’échelle des applications à l’aide du service PaaS Azure pour les sites web.  | La tarification est basée sur la taille des instances et sur les fonctionnalités requises. [Plus d’informations](https://azure.microsoft.com/pricing/details/app-service/windows/)
[Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) | Équilibreur de charge qui utilise DNS pour diriger les utilisateurs vers Azure, ou vers des sites web et des services externes. | La tarification est basée sur le nombre de requêtes DNS reçues et sur le nombre de points de terminaison surveillés. | [Plus d’informations](https://azure.microsoft.com/pricing/details/traffic-manager/)
[Azure Database pour MySQL](https://docs.microsoft.com/azure/mysql/) | La base de données est basée sur le moteur du serveur MySQL open source. Ils fournissent une base de données MySQL entièrement managée et de classe Entreprise, appuyée par une communauté active, en tant que service pour le développement et le déploiement d’applications. | La tarification est basée sur les exigences de sauvegarde, de stockage et de calcul. [Plus d’informations](https://azure.microsoft.com/pricing/details/mysql/)

 
## <a name="prerequisites"></a>Prérequis

Voici ce dont Contoso a besoin pour exécuter ce scénario.

**Configuration requise** | **Détails**
--- | ---
**Abonnement Azure** | Contoso a créé des abonnements plus tôt dans cette série d’articles. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Si vous créez un compte gratuit, vous êtes l’administrateur de votre abonnement et pouvez effectuer toutes les actions.<br/><br/> Si vous utilisez un abonnement existant et que vous n’êtes pas l’administrateur, vous devez collaborer avec l’administrateur pour qu’il vous donne les autorisations Propriétaire ou Contributeur. 
**Infrastructure Azure** | Contoso configure son infrastructure Azure comme décrit dans [Infrastructure Azure pour la migration](contoso-migration-infrastructure.md).



## <a name="scenario-steps"></a>Étapes du scénario

Voici comment Contoso effectue la migration :

> [!div class="checklist"]
> * **Étape 1 : Provisionner Azure App Services** : les administrateurs de Contoso provisionnent les applications web dans les régions primaire et secondaire.
> * **Étape 2 : Configurer Traffic Manager** : Contoso configure Traffic Manager devant les applications web, pour l’équilibrage de charge et le routage du trafic.
> * **Étape 3 : Provisionner MySQL** : dans Azure, ils provisionnent une instance de base de données Azure MySQL.
> * **Étape 4 : Migrer la base de données** : Contoso migre la base de données à l’aide de MySQL Workbench. 
> * **Étape 5 : Configurer GitHub** : ils configurent un dépôt GitHub local pour le code et les sites web de l’application.
> * **Étape 6 : Déployer les applications web** : ils déploient les applications web à partir de GitHub.




## <a name="step-1-provision-azure-app-services"></a>Étape 1 : Provisionner Azure App Services

Les administrateurs de Contoso provisionnent deux applications web (une dans chaque région) à l’aide d’Azure App Services.

1. Ils créent une ressource Web App dans la région primaire USA Est 2 (**osticket-eus2**) à partir de la Place de Marché Azure.
2. Ils placent la ressource dans le groupe de ressources de production **ContosoRG**.

    ![Azure App](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app1.png) 

3. Ils créent un nouveau plan App Service dans la région principale (**APP-SVP-EUS2**), de taille standard.

     ![Azure App](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app2.png) 
    
4. Ils sélectionnent un système d’exploitation Linux avec la pile d’exécution PHP 7.0, qui est un conteneur Docker.

    ![Azure App](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app3.png) 

5. Ils créent une deuxième application web (**osticket-cus**) et un plan App Service pour la région USA Centre.

    ![Azure App](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app4.png) 


**Besoin de plus d’aide ?**

- Découvrez-en plus sur [Azure App Service Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview).
- Découvrez-en plus sur [Azure App Service sur Linux](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro).


## <a name="step-2-set-up-traffic-manager"></a>Étape 2 : Configurer Traffic Manager

Les administrateurs de Contoso configurent Traffic Manager pour diriger les requêtes web entrantes vers les applications web qui s’exécutent sur la couche web d’osTicket.

1. Ils créent une ressource Traffic Manager (**osticket.trafficmanager.net**) à partir de la Place de Marché Azure. Ils utilisent le routage par priorité afin qu’USA Est 2 soit le site principal. Ils placent la ressource dans leur groupe de ressources d’infrastructure (**ContosoInfraRG**). Notez que Traffic Manager est global et non lié à un emplacement spécifique.

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager1.png) 

2. Maintenant, ils configurent Traffic Manager avec des points de terminaison. Ils ajoutent l’application web USA Est 2 en tant que site principal (**osticket-eus2**) et l’application web USA Centre en tant que site secondaire (**osticket-cus**).

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager2.png) 

3. Après avoir ajouté les points de terminaison, ils peuvent les surveiller.

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager3.png)

**Besoin de plus d’aide ?**

- En savoir plus sur [Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview).
- En savoir plus sur le [routage du trafic vers un point de terminaison prioritaire](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-priority-routing-method).
 
## <a name="step-3-provision-azure-database-for-mysql"></a>Étape 3 : approvisionner Azure Database pour MySQL

Les administrateurs de Contoso provisionnent une instance de base de données MySQL dans la région USA Est 2 principale.

1. Dans le portail Azure, Contoso crée une base de données Azure Database pour MySQL. 

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-1.png)

2. Contoso ajoute le nom **contosoosticket** pour la base de données Azure. Contoso ajoute la base de données au groupe de ressources de production **ContosoRG**, puis spécifient les informations d’identification pour celle-ci.
3. La base de données MySQL locale étant la version 5.7, Contoso sélectionne cette version à des fins de compatibilité. Contoso utilise les tailles par défaut, qui correspondent à leurs exigences en matière de base de données.

     ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-2.png)

4. Pour les **Options de redondance de sauvegarde**, ils sélectionnent **Géoredondant**. Cette option leur permet de restaurer la base de données dans leur région USA Centre secondaire en cas de panne. Contoso ne peut configurer cette option que lors de l’approvisionnement de la base de données.

    ![Redondance](./media/contoso-migration-refactor-linux-app-service-mysql/db-redundancy.png)

4. Ils configurent la sécurité de la connexion. Dans la base de données > **Sécurité de la connexion**, ils configurent des règles de pare-feu pour autoriser la base de données à accéder aux services Azure.
5. Ils ajoutent l’adresse IP cliente de la station de travail locale aux adresses IP de début et de fin. Ainsi, les applications web peuvent accéder à la base de données MySQL, en plus du client de base de données qui effectue la migration.

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-3.png)



## <a name="step-4-migrate-the-database"></a>Étape 4 : Migrer la base de données

Les administrateurs de Contoso migrent la base de données en utilisant la sauvegarde et la restauration, avec des outils MySQL. Contoso installe MySQL Workbench, sauvegardent la base de données à partir de OSTICKETMYSQL, puis la restaurent sur Azure Database pour MySQL.

### <a name="install-mysql-workbench"></a>Installer MySQL Workbench

1. Ils vérifient les [prérequis et téléchargent MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Contoso installe MySQL Workbench pour Windows conformément aux [instructions d’installation](https://dev.mysql.com/doc/workbench/en/wb-installing.html). L’ordinateur sur lequel ils installent doit être accessible à la machine virtuelle OSTICKETMYSQL et à Azure par le biais d’Internet.
3. Dans MySQL Workbench, Contoso crée une connexion MySQL à OSTICKETMYSQL. 

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench1.png)

4. Contoso exporte la base de données en tant que **osticket** dans un fichier autonome local.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench2.png)

5. Une fois la base de données sauvegardée localement, Contoso crée une connexion à l’instance Azure Database pour MySQL.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench3.png)

6. À présent, ils peuvent importer (restaurer) la base de données dans l’instance MySQL Azure à partir du fichier autonome. Un nouveau schéma (osticket) est créé pour l’instance.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench4.png)

7. Une fois les données restaurées, elles peuvent être interrogées à l’aide de Workbench et elles apparaissent dans le portail Azure.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench5.png)

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench6.png)

8. Pour finir, ils doivent mettre à jour les informations de base de données sur les applications web. Sur l’instance MySQL, ils ouvrent **Chaînes de connexion**. 

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench7.png)

9. Dans la liste de chaînes, ils localisent les paramètres d’applications web, et ils cliquent pour les copier.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench8.png)

10. Ils ouvrent une fenêtre du Bloc-notes et collent la chaîne dans un nouveau fichier, et ils le mettent à jour afin qu’il corresponde aux paramètres d’informations d’identification, à la base de données osticket et à l’instance MySQL.

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench9.png)

11. Ils peuvent vérifier le nom du serveur et la connexion à partir de **Vue d’ensemble** dans l’instance MySQL dans le portail Azure.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench10.png)


## <a name="step-5-set-up-github"></a>Étape 5 : Configurer GitHub

Les administrateurs de Contoso créent un dépôt GitHub privé et configurent une connexion à la base de données osTicket dans Azure MySQL. Ensuite, ils chargent l’application web Azure avec l’application.  

1.  Ils accèdent au dépôt GitHub public d’OsTicket et le dupliquent dans le compte GitHub de Contoso.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github1.png)

2. Une fois cette opération effectuée, ils accèdent au dossier **include** et recherchent le fichier **ost-config.php**.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github2.png)


3. Le fichier s’ouvre dans le navigateur et ils le modifient.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github3.png)

4. Dans l’éditeur, ils mettent à jour les détails de la base de données, en particulier **DBHOST** et **DBUSER**. 

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github4.png)

5. Ensuite, ils valident les modifications.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github5.png)

6. Pour chaque application web (**osticket-eus2** et **osticket-cus**), ils modifient les **Paramètres de l’application** dans le portail Azure.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github6.png)

7. Ils entrent la chaîne de connexion avec le nom **osticket**, puis copient la chaîne à partir du bloc-notes dans la **zone de valeur**. Ils sélectionnent **MySQL** dans la liste déroulante en regard de la chaîne, puis enregistrent les paramètres.

    ![GitHub](./media/contoso-migration-refactor-linux-app-service-mysql/github7.png)

## <a name="step-6-configure-the-web-apps"></a>Étape 6 : Configurer les applications web

Dans la dernière étape du processus de migration, les administrateurs de Contoso configurent les applications web avec les sites web osTicket.



1. Dans l’application web principale (**osticket-eus2**), ils ouvrent **Option de déploiement** et définissent **GitHub** comme source.

    ![Configurer l’application](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app1.png)

2. Ils sélectionnent les options de déploiement.

    ![Configurer l’application](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app2.png)

3. Une fois les options définies, la configuration apparaît comme étant en attente dans le portail Azure.

    ![Configurer l’application](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app3.png)

4. Une fois la configuration mise à jour et l’application web osTicket chargée à partir de GitHub vers le conteneur Docker exécutant Azure App Service, le site apparaît comme étant actif.

    ![Configurer l’application](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app4.png)

5. Ils répètent les étapes ci-dessus pour l’application web secondaire (**osticket-cus**).
6. Une fois le site configuré, il est accessible par le biais du profil Traffic Manager. Le nom DNS est le nouvel emplacement de l’application osTicket. [Plus d’informations](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)

    ![Configurer l’application](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app5.png)
    
7. Contoso souhaite que le nom DNS soit facile à mémoriser. Ils créent un enregistrement d’alias (CNAME) **osticket.contoso.com** qui pointe vers le nom Traffic Manager, dans le système DNS sur leurs contrôleurs de domaine.

    ![Configurer l’application](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app6.png)

8. Ils configurent les applications web **osticket-eus2** et **osticket-cus** de façon à autoriser les noms d’hôtes personnalisés.

    ![Configurer l’application](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app7.png)

### <a name="set-up-autoscaling"></a>Configurer la mise à l’échelle automatique

Pour finir, ils configurent la mise à l’échelle automatique pour l’application. Cela garantit que, à mesure que les agents utilisent l’application, les instances d’application augmentent et diminuent en fonction des besoins de l’entreprise. 

1. Dans App Service **APP-SRV-EUS2**, ils ouvrent **Unité d’échelle**.
2. Ils configurent un nouveau paramètre de mise à l’échelle automatique avec une seule règle qui augmente le nombre d’instances d’une unité quand le pourcentage d’utilisation du processeur pour l’instance actuelle est supérieure à 70 % pendant 10 minutes.

    ![Autoscale](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale1.png)

3. Ils configurent le même paramètre sur **APP-SRV-CUS** afin de s’assurer que le même comportement s’applique si l’application bascule vers la région secondaire. La seule différence est qu’ils définissent la limite d’instance à 1, puisque cela concerne uniquement les basculements.

   ![Autoscale](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale2.png)

##  <a name="clean-up-after-migration"></a>Nettoyer après la migration

La migration étant terminée, l’application osTicket est refactorisée pour s’exécuter dans une application web Azure avec livraison continue à l’aide d’un dépôt GitHub privé. L’application s’exécute dans deux régions, afin d’optimiser la résilience. La base de données osTicket s’exécute dans Azure Database pour MySQL après la migration vers la plateforme PaaS.

Pour le nettoyage, Contoso doit effectuer les étapes suivantes : 
- Supprimer les machines virtuelles VMware du stock vCenter.
- Supprimer les machines virtuelles locales des travaux de sauvegarde locale.
- Mettre à jour la documentation interne afin qu’elle indique les nouveaux emplacements et adresses IP. 
- Passer en revue toutes les ressources qui interagissent avec les machines virtuelles locales, et mettre à jour les paramètres ou la documentation appropriés afin de refléter la nouvelle configuration.
- Reconfigurer la surveillance pour qu’elle pointe vers l’URL osticket-trafficmanager.net, afin de vérifier que l’application est opérationnelle.

## <a name="review-the-deployment"></a>Examiner le déploiement

L’application étant en cours d’exécution, Contoso doit à présent rendre son infrastructure entièrement opérationnelle et la sécuriser.

### <a name="security"></a>Sécurité

L’équipe de sécurité de Contoso a examiné l’application afin d’identifier d’éventuels problèmes de sécurité. Ils se sont rendus compte que la communication entre l’application osTicket et l’instance de base de données MySQL n’était pas configurée pour SSL. Contoso doit faire cela pour s’assurer que le trafic de base de données ne puisse pas être piraté. [Plus d’informations](https://docs.microsoft.com/azure/mysql/howto-configure-ssl)

### <a name="backups"></a>Sauvegardes

- Les applications web osTicket ne contiennent de données d’état, et ne doivent donc pas être sauvegardées.
- Contoso n’a pas besoin de configurer la sauvegarde de la base de données. Azure Database pour MySQL crée automatiquement les sauvegardes et les stocke. Comme Contoso a choisi d’utiliser la géo-redondance pour la base de données, celle-ci est résiliente et prête pour la production. Les sauvegardes peuvent être utilisées pour restaurer votre serveur à un point dans le temps. [Plus d’informations](https://docs.microsoft.com/azure/mysql/concepts-backup)


### <a name="licensing-and-cost-optimization"></a>Gestion des licences et optimisation des coûts

- Il n’y a aucun problème de licence pour le déploiement PaaS.
- Contoso va activer Azure Cost Management sous licence de Cloudyn, une filiale de Microsoft. Il s’agit d’une solution de gestion des coûts multicloud qui aide à utiliser et à gérer Azure ainsi que d’autres ressources cloud.  [En savoir plus](https://docs.microsoft.com/azure/cost-management/overview) sur Azure Cost Management.



