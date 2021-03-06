---
title: Modèles d’achat Azure SQL Database | Microsoft Docs
description: Découvrez le modèle d’achat pour Azure SQL Database.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 08/17/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 5fcdf02fe75905fb3e492671ba44adb65dfd0da7
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42144038"
---
# <a name="azure-sql-database-purchasing-models-and-resources"></a>Ressources et modèles d’achat Azure SQL Database 

Azure SQL Database vous permet d’acheter facilement un moteur de base de données PaaS adapté à vos exigences en terme de performances et de coûts. Selon le modèle de déploiement Azure SQL Database, vous pouvez sélectionner le modèle d’achat adapté à vos besoins : 
 - Les [Serveurs logiques](sql-database-logical-servers.md) dans [Azure SQL Database](sql-database-technical-overview.md) proposent deux modèles d’achat pour les ressources de calcul, de stockage et d’E/S : un modèle d’achat DTU et un [modèle d’achat vCore](sql-database-service-tiers-vcore.md). 
 - Les [Instances managées](sql-database-managed-instance.md) dans Azure SQL Database offrent uniquement le [modèle d’achat vCore](sql-database-service-tiers-vcore.md).

Le tableau et le graphique suivants comparent ces deux modèles d’achat.

|**Modèle d’achat**|**Description**|**Idéal pour**|
|---|---|---|
|Modèle DTU|Ce modèle est basé sur une mesure regroupant des ressources de calcul, de stockage et d’E/S. Les niveaux de performance en termes d’unités de transaction de base de données (DTU) pour des bases de données uniques et d’unités de transaction de base de données élastique (eDTU) pour les pools élastiques. Pour en savoir plus sur les DTU et les eDTU, consultez [Définition des DTU et des eDTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus).|Idéal pour les clients qui souhaitent des options de ressources préconfigurées et simples.| 
|Modèle vCore|Ce modèle vous permet de sélectionner indépendamment les ressources de calcul et de stockage. Il permet également d’utiliser Azure Hybrid Benefit pour SQL Server afin de réduire les coûts.|Idéal pour les clients qui donnent la priorité à la flexibilité, au contrôle et à la transparence.|
||||  

![Modèle de prix](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model"></a>Modèle d’achat vCore 

Un vCore représente l’UC logique offerte avec une option permettant de choisir entre plusieurs générations de matériel et de caractéristiques physiques du matériel (par exemple, le nombre de cœurs, la mémoire, la taille de stockage). Le modèle d’achat vCore apporte flexibilité, contrôle et transparence pour la consommation des ressources. En outre, il permet de traduire facilement les exigences des charges de travail locales pour le cloud. Ce modèle permet de sélectionner le calcul, la mémoire et le stockage en fonction des besoins des charges de travail. Dans le modèle d’achat vCore, les clients peuvent choisir entre les niveaux de service [Usage général](sql-database-high-availability.md#standardgeneral-purpose-availability) et [Critique pour l’entreprise](sql-database-high-availability.md#premiumbusiness-critical-availability) pour les [bases de données uniques](sql-database-single-database-scale.md), les [instances gérées](sql-database-managed-instance.md) et les [pools élastiques](sql-database-elastic-pool.md). 

Le modèle d’achat vCore vous permet de sélectionner les ressources de calcul et de stockage indépendamment les unes des autres, d’égaler les performances d’une exécution locale et d’optimiser les coûts. Dans le modèle d’achat vCore, les clients paient pour :
- Le calcul (niveau de service + nombre de vCore + quantité de mémoire + génération du matériel)*
- Le type et la quantité de stockage des journaux et des données 
- Nombre d’E/S** - applicable aux [Serveurs logiques](sql-database-logical-servers.md) uniquement
- Le stockage de sauvegarde (RA-GRS)** 

\* Dans la préversion publique initiale, les processeurs logiques Gen 4 sont basés sur des processeurs Intel E5-2673 v3 (Haswell) 2.4 GHz.

\*\* Dans la préversion, les sauvegardes et les E/S sont gratuites pendant sept jours.

> [!IMPORTANT]
> Le calcul, les E/S, ainsi que le stockage des données et des journaux, sont facturés au niveau de chaque base de données ou au niveau du pool élastique. Le stockage des sauvegardes est facturé au niveau de chaque base de données. Pour plus d’informations sur les frais liés à Managed Instance, consultez [Azure SQL Database Managed Instance](sql-database-managed-instance.md).
> **Limitations liées aux régions :** le modèle d’achat vCore n’est pas encore disponible dans les régions suivantes : Europe de l’Ouest, France Centre, Royaume-Uni Sud, Royaume-Uni Ouest et Sud-Est de l’Australie.

Si votre base de données ou pool élastique consomme plus de 300 DTU, le passage au modèle vCore peut vous permettre de réaliser des économies. Vous pouvez passer au modèle vCore à l’aide de l’API de votre choix ou du portail Azure, sans aucun temps d’arrêt. Le passage au modèle vCore n’est toutefois pas obligatoire. Si le modèle d’achat DTU répond à vos besoins métier et à vos besoins en performance, vous devez continuer de l’utiliser. Si vous décidez de passer du modèle DTU au modèle vCore, vous devez sélectionner le niveau de performance en suivant la règle de base suivante : chaque groupe de 100 DTU du niveau Standard nécessite au moins 1 vCore dans le niveau Usage général, et chaque groupe de 125 DTU du niveau Premium nécessite au moins 1 vCore Critique pour l'entreprise.

## <a name="dtu-based-purchasing-model"></a>Modèle d’achat DTU

L’unité DTU (Database Transaction Unit) correspond à un mélange de mesures d’UC, de mémoire, de lectures et d’écritures. Le modèle d’achat DTU offre un ensemble préconfiguré de ressources de calcul et de stockage inclus pour obtenir différents niveaux de performance d’application. Les clients qui préfèrent la simplicité d’un ensemble préconfiguré et de versements mensuels peuvent trouver le modèle DTU plus adapté à leurs besoins. Dans le modèle d’achat DTU, les clients peuvent choisir parmi les niveaux de service **De base**, **Standard** et **Premium** pour [chaque base de données](sql-database-single-database-scale.md) et pour les [pools élastiques](sql-database-elastic-pool.md). Ce modèle d’achat n’est pas disponible dans les [instances gérées](sql-database-managed-instance.md).

### <a name="what-are-database-transaction-units-dtus"></a>Définition des unités de transaction de base de données (DTU)
Microsoft garantit un certain niveau de ressources pour une seule base de données Azure SQL Database à un niveau de performances spécifique au sein d’un [niveau de service](sql-database-single-database-scale.md) (indépendamment de toute autre base de données dans le cloud Azure), fournissant un niveau de performances prévisible. Le volume de ressources est calculé sous forme d’unités de transactions de base de données ou DTU. Il s’agit d’une mesure groupée de calcul, de stockage et de ressources d’E/S. Le ratio entre ces ressources a été déterminé à l’origine par une [charge de travail d’évaluation OLTP](sql-database-benchmark-overview.md), conforme aux charges de travail OLTP réelles standard. Quand votre charge de travail dépasse la quantité de ces ressources, le débit est limité, ce qui entraîne un ralentissement des performances et des délais d’attente. Les ressources utilisées par votre charge de travail n’impactent pas les ressources disponibles pour les autres bases de données SQL dans le cloud Azure, et les ressources utilisées par d’autres charges de travail n’impactent pas les ressources disponibles pour votre base de données SQL.

![cadre englobant](./media/sql-database-what-is-a-dtu/bounding-box.png)

Les DTU sont particulièrement utiles pour comprendre la quantité relative de ressources entre les bases de données Azure SQL Database à différents niveaux de performances et de service. Par exemple, le fait de doubler les DTU en augmentant le niveau de performances d’une base de données revient à doubler l’ensemble des ressources disponibles pour cette base de données. Par exemple, une base de données Premium P11 comprenant 1 750 DTU fournit une puissance de calcul DTU 350 fois plus importante qu’une base de données de base comprenant 5 DTU.  

Pour avoir une idée plus précise de la consommation de ressources (DTU) de votre charge de travail, utilisez [Query Performance Insight pour Azure SQL Database ](sql-database-query-performance.md) pour :

- Identifier les principales requêtes par UC/durée/nombre d’exécutions qui peuvent être réglées pour améliorer les performances. Par exemple, une requête utilisant beaucoup d’E/S peut tirer profit de l’utilisation de [techniques d’optimisation en mémoire](sql-database-in-memory.md) pour mieux utiliser la mémoire disponible à un certain niveau de service et de performances.
- Connaître les détails d’une requête, afficher son texte et l’historique d’utilisation des ressources.
- Accéder aux recommandations de réglage des performances qui indiquent les actions effectuées par [SQL Database Advisor](sql-database-advisor.md).

### <a name="what-are-elastic-database-transaction-units-edtus"></a>Définition des unités de transaction de base de données élastique (DTU)
Plutôt que de fournir un ensemble de ressources (DTU) dédié qui peut ne pas s’avérer toujours nécessaire pour une base de données SQL Database qui est toujours disponible, vous pouvez placer les bases de données dans un [pool élastique](sql-database-elastic-pool.md) sur un serveur SQL Database qui partage un pool de ressources entre ces bases de données. Les ressources partagées dans un pool élastique sont mesurées en eDTU (elastic Database Transaction Unit). Les pools élastiques offrent une solution simple et économique pour gérer les objectifs de performance de plusieurs bases de données ayant des modèles d’utilisation variables et non prévisibles. Un pool élastique garantit que les ressources ne peuvent pas être consommées par une base de données dans le pool, mais également que chaque base de données dans le pool dispose toujours d’une quantité de ressources minimale. 

![Introduction à la base de données SQL : eDTU par couche et niveau](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Un pool bénéficie d’un nombre défini d’eDTU pour un prix donné. Au sein du pool élastique, les différentes bases de données peuvent s’adapter facilement et automatiquement aux limites définies. Une base de données soumise à une charge plus élevée consomme plus d’eDTU pour répondre à la demande. Les bases de données soumises à des charges plus légères consomment moins d’eDTU. Les bases de données qui ne subissent aucune charge ne consomment aucune eDTU. Le provisionnement des ressources pour l’ensemble du pool, plutôt que pour chaque base de données, simplifie les tâches de gestion, fournissant un aperçu du budget nécessaire pour le pool.

Vous pouvez ajouter des eDTU à un pool existant sans que les bases de données du pool ne connaissent de temps d’arrêt et ne soient affectées. De même, si les eDTU supplémentaires ne sont plus nécessaires, elles peuvent être supprimées à partir d’un pool existant à tout moment. Vous pouvez ajouter des bases de données dans le pool ou en supprimer. Vous pouvez également limiter la quantité d’eDTU qu’une base de données peut utiliser en cas de charge importante pour réserver des eDTU pour les autres bases de données. Si vous prévoyez qu’une base de données sous-utilise des ressources, vous pouvez la déplacer hors du pool et la configurer en tant que base de données unique avec une quantité prévisible des ressources nécessaires.

### <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Comment puis-je déterminer le nombre de DTU requises par ma charge de travail ?
Si vous cherchez à migrer une charge de travail de machine virtuelle SQL Server ou locale existante vers une base de données SQL Azure, vous pouvez utiliser l’outil [DTU Calculator](http://dtucalculator.azurewebsites.net/) pour évaluer approximativement le nombre de DTU requises. Dans le cas d’une charge de travail de base de données SQL Azure existante, vous pouvez utiliser [Query Performance Insight pour base de données SQL](sql-database-query-performance.md) pour comprendre votre consommation des ressources de la base de données (DTU) et obtenir un insight plus approfondi pour l’optimisation de votre charge de travail. Vous pouvez également utiliser la DMV [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) pour voir la consommation des ressources au cours de la dernière heure. Sinon, la vue de catalogue [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) affiche la consommation des ressources pour les 14 derniers jours, mais avec une précision inférieure (moyennes de cinq minutes).

### <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Comment savoir si je peux tirer parti d’un pool élastique de ressources ?
Les pools sont idéaux dans le cas de nombreuses bases de données avec des modèles d’utilisation spécifiques. Pour une base de données indiquée, ce modèle se caractérise par une moyenne d’utilisation faible avec des pics d’utilisation relativement rares. La base de données SQL évalue automatiquement l’historique d’utilisation en ressources des bases de données dans un serveur de base de données SQL existant et recommande la configuration de pool appropriée dans le portail Azure. Pour plus d’informations, consultez l’article [Quand utiliser un pool élastique ?](sql-database-elastic-pool.md).


## <a name="next-steps"></a>Étapes suivantes

- Pour le modèle d’achat vCore, voir [Modèle d’achat vCore](sql-database-service-tiers-vcore.md).
- Pour le modèle d’achat basé sur les DTU, consultez [Modèle d’achat DTU](sql-database-service-tiers-dtu.md).
