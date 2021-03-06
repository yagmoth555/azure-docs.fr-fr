---
title: 'Haute disponibilité : service Azure SQL Database | Microsoft Docs'
description: En savoir plus sur les fonctionnalités de haute disponibilité du service Azure SQL Database
services: sql-database
author: jovanpop-msft
manager: craigg
ms.service: sql-database
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: jovanpop
ms.reviewer: carlrab, sashan
ms.openlocfilehash: 7a60d800ce76f8ff9a903cc068fa7bc87cd33f3f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700633"
---
# <a name="high-availability-and-azure-sql-database"></a>Haute disponibilité et Azure SQL Database

Azure SQL Database un service de base de données de type PaaS (Platform as a Service) hautement disponible qui garantit une disponibilité dans le temps de votre base de données de 99,99 %, éliminant tout souci lié à la maintenance et aux temps d’arrêt. Ce processus du moteur de base de données SQL Server, entièrement géré et hébergé dans le cloud Azure, garantit que votre base de données SQL Server est toujours mise à niveau/corrigée sans que votre charge de travail ne soit affectée. Quand une instance est corrigée ou bascule, le temps d’arrêt n’est généralement pas perceptible si vous [utilisez une logique de nouvelle tentative](sql-database-develop-overview.md#resiliency) dans votre application. Si le temps de basculement est supérieur à 60 secondes, vous devriez ouvrir un cas de support. Pour assurer la disponibilité de vos données, Azure SQL Database bénéficie de fonctionnalités de récupération rapide, même dans les situations les plus critiques.

La plateforme Azure gère entièrement chaque base de données SQL Azure et garantit un pourcentage élevé de disponibilité des données, sans aucune perte. Azure gère automatiquement les tâches suivantes : mises à jour correctives, sauvegardes, réplication, détection des éventuelles défaillances matérielles, logicielles ou réseau sous-jacentes, déploiement de correctifs de bogues, basculements, mises à niveau de base de données et autres tâches de maintenance. Les ingénieurs de SQL Server ont implémenté les bonnes pratiques connues pour que la durée d’exécution des opérations de maintenance représente moins de 0,01 % de la durée de vie de votre base de données. Cette architecture est conçue de manière à éliminer tout risque de perte des données validées et à mener à bien les opérations de maintenance sans affecter la charge de travail. Rien, pas même une fenêtre de maintenance ou un temps d’arrêt, ne doit vous obliger à arrêter la charge de travail pendant la mise à niveau ou la maintenance de la base de données. La haute disponibilité intégrée à Azure SQL Database garantit que cette base de données ne constitue jamais un point de défaillance unique dans votre architecture logicielle.

Azure SQL Database est basé sur une architecture de moteur de base de données SQL Server. Celle-ci est ajustée pour l’environnement cloud afin de garantir une disponibilité de 99,99 % même en cas de panne d’infrastructure. Azure SQL Database utilise deux modèles architecturaux haute disponibilité (les deux garantissant une disponibilité de 99,99 %) :
- Le modèle Standard/Usage général est basé sur la séparation du calcul et du stockage. Ce modèle architectural s’appuie sur la haute disponibilité et la haute fiabilité du niveau de stockage. Cependant, il peut faire l’objet d’une détérioration des performances durant les activités de maintenance.
- Le modèle Premium/Critique pour l’entreprise est basé sur un cluster de processus de moteur de base de données. Ce modèle architectural s’appuie sur le fait qu’un quorum de nœuds de moteur de base de données est toujours disponible. Il n’a qu’un impact minime sur les performances de votre charge de travail, même durant les activités de maintenance.

Azure met à niveau et corrige le système d’exploitation sous-jacent, les pilotes et le moteur de base de données SQL Server en toute transparence avec un temps d’arrêt minimal pour les utilisateurs finaux. Azure SQL Database s’exécutant sur la dernière version stable du moteur de base de données SQL Server et du système d’exploitation Windows, la plupart des utilisateurs ne se rendent pas compte que les mises à niveau sont effectuées en continu.

## <a name="standardgeneral-purpose-availability"></a>Disponibilité Standard/Usage général

La disponibilité Standard fait référence au contrat SLA de 99,99 % appliqué dans les niveaux Standard/De base/Usage général. Dans le cadre de ce modèle architectural, la haute disponibilité est obtenue par la séparation des couches de calcul et de stockage et la réplication des données dans le niveau de stockage.

L’illustration suivante montre quatre nœuds dans un modèle architectural Standard avec les couches de calcul et de stockage séparées.

![Séparation du calcul et du stockage](media/sql-database-managed-instance/general-purpose-service-tier.png)

Le modèle de disponibilité Standard comprend deux couches :

- Une couche de calcul sans état qui exécute le processus sqlserver.exe et contient uniquement des données transitoires et mises en cache (par exemple : cache du plan, pool de mémoires tampons, pool de stockage de colonnes). Ce nœud SQL Server sans état est géré par Azure Service Fabric qui initialise le processus, contrôle l’intégrité du nœud et effectue le basculement vers un autre emplacement si nécessaire.
- Une couche de données avec état comprenant les fichiers de base de données (.mdf/.ldf) stockés dans le Stockage Premium Azure. Stockage Azure garantit que tout enregistrement placé dans un fichier de base de données ne subit aucune perte de données. Stockage Azure est doté de fonctionnalités intégrées de redondance et de disponibilité des données qui garantissent la préservation des enregistrements d’un fichier journal ou des pages d’un fichier de données, même en cas de plantage du processus SQL Server.

Dès que le moteur de base de données ou le système d’exploitation est mis à niveau, qu’une partie de l’infrastructure sous-jacente est défaillante ou qu’un problème critique est détecté dans le processus SQL Server, Azure Service Fabric déplace le processus SQL Server sans état vers un autre nœud de calcul sans état. Afin de réduire le temps de basculement, un ensemble de nœuds de réserve se tient prêt à exécuter le nouveau service de calcul en cas de basculement. Les données dans la couche Stockage Azure ne sont pas affectées, et les fichiers de données/journaux sont attachés à des processus SQL Server nouvellement initialisés. Ce processus garantit une disponibilité de 99,99 %, mais il peut impacter les performances des lourdes charges de travail en cours d’exécution, et ce, en raison des délais de transition et du fait que le nouveau nœud SQL Server démarre avec un cache à froid.

## <a name="premiumbusiness-critical-availability"></a>Disponibilité Premium/Critique pour l’entreprise

La disponibilité Premium, activée dans le niveau Premium de Azure SQL Database, est conçue pour les charges de travail intenses qui ne tolèrent aucun impact sur les performances résultant des opérations de maintenance en cours.

Dans le modèle Azure, Azure SQL Database intègre les opérations de calcul et stockage sur le nœud unique. Dans le cadre de ce modèle architectural, la haute disponibilité est obtenue par la réplication du calcul (processus de moteur de base de données SQL Server) et du stockage (disque SSD attaché localement). Elle est déployée dans un cluster de [groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) à 4 nœuds.

![Cluster de nœuds de moteur de base de données](media/sql-database-managed-instance/business-critical-service-tier.png)

Le processus du moteur de base de données SQL Server et les fichiers mdf/ldf sous-jacents sont placés sur le même nœud, et le stockage SSD attaché localement permet à votre charge de travail de bénéficier d’une latence faible. La haute disponibilité est implémentée à l’aide de [groupes de disponibilité Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) standard. Chaque base de données est un cluster de nœuds de base de données, avec une base de données primaire accessible à la charge de travail cliente et trois processus secondaires contenant les copies des données. Le nœud principal envoie (push) constamment les changements apportés aux nœuds secondaires afin de garantir la disponibilité des données sur les réplicas secondaires en cas de plantage du nœud principal, pour quelque raison que ce soit. Le basculement est géré par le moteur de base de données SQL Server : un réplica secondaire devient le nœud principal, et un réplica secondaire est créé pour garantir un nombre suffisant de nœuds dans le cluster. La charge de travail est automatiquement redirigée vers le nouveau nœud principal.

En outre, le cluster Critique pour l’entreprise fournit un nœud intégré en lecture seule qui peut être utilisé pour exécuter des requêtes en lecture seule (des rapports, par exemple) et qui ne devrait pas affecter les performances de votre charge de travail principale. 

## <a name="zone-redundant-configuration-preview"></a>Configuration de zone redondante (préversion)

Par défaut, les réplicas du jeu de quorum pour les configurations de stockage local sont créés dans le même centre de données. Avec l’introduction des [Zones de disponibilité Azure](../availability-zones/az-overview.md), vous avez la possibilité de placer les différents réplicas dans les jeux de quorum sur des zones de haute disponibilité distinctes dans la même région. Pour éliminer un point de défaillance unique, l’anneau de contrôle est également dupliqué sur plusieurs fuseaux horaires sous forme de trois anneaux de passerelle (GW). Le routage vers un anneau de passerelle spécifique est contrôlé par [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Étant donné que la configuration de la redondance dans une zone ne crée pas de redondance de base de données supplémentaire, l’utilisation des zones de disponibilité dans les niveaux de service Premium ou Critique pour l’entreprise (préversion) est disponible sans coût supplémentaire. En sélectionnant une base de données redondante dans une zone, vous pouvez rendre vos bases de données Premium ou Critique pour l’entreprise (préversion) résistantes à un plus grand éventail d’échecs, notamment les pannes graves de centre de données, sans aucune modification de la logique d’application. Vous pouvez également convertir vos bases de données ou pools Premium ou Critique pour l’entreprise (préversion) en configuration avec redondance dans une zone.

L’ensemble du jeu de quorums de redondance de zone ayant des réplicas dans différents centres de données avec une certaine distance entre eux, la latence accrue du réseau peut augmenter le temps de validation et ainsi avoir un impact sur les performances de certaines charges de travail OLTP. Vous pouvez toujours revenir à la configuration de zone unique en désactivant le paramètre de redondance de zone. Ce processus dépend de la taille des données et est semblable à la mise à jour des objectifs de niveau de service (SLO) ordinaires. À la fin du processus, la base de données ou le pool est migré à partir d’un anneau de redondance de zone vers un anneau de zone unique, ou vice versa.

> [!IMPORTANT]
> Pour le moment, les bases de données avec redondance de zone et les pools élastiques sont uniquement pris en charge dans le niveau de service Premium. Pour la version préliminaire publique, les sauvegardes et enregistrements d’audit sont stockés dans le stockage de RA-GRS, et ne sont donc pas automatiquement disponibles en cas d’une panne à l’échelle de la zone. 

La version avec redondance de zone de l’architecture de haute disponibilité est illustrée dans le diagramme suivant :
 
![architecture haute disponibilité avec redondance de zone](./media/sql-database-high-availability/high-availability-architecture-zone-redundant.png)

## <a name="read-scale-out"></a>Lecture du Scale-out
Comme décrit, les niveaux de service Premium et Critique pour l’entreprise (préversion) tirent parti du quorum et de la technologie Always On pour une haute disponibilité à la fois dans des configurations pour zone unique et redondantes dans une zone. Un des avantages de la technologie Always On est que les réplicas sont toujours dans un état cohérent au niveau transactionnel. Étant donné que les réplicas ont le même niveau de performances que le principal, l’application peut tirer parti de cette capacité supplémentaire pour la maintenance des charges de travail en lecture seule sans coût supplémentaire (lecture du Scale-out). De cette façon, les requêtes en lecture seule seront isolées à partir de la charge de travail principale en lecture-écriture et n’affecteront pas ses performances. La fonctionnalité de lecture du Scale-out est conçue pour les applications incluant des charges de travail en lecture seule séparées logiquement, comme des analyses, et peut par conséquent tirer parti de cette capacité supplémentaire sans connexion au principal. 

Pour utiliser la fonctionnalité d’échelle horizontale en lecture avec une base de données en particulier, vous devez l’activer explicitement lors de la création de la base de données, ou ultérieurement en modifiant sa configuration avec PowerShell par un appel aux cmdlets [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [ New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) ou avec l’API REST Azure Resource Manager suivant la méthode [Bases de données – Créer ou mettre à jour](/rest/api/sql/databases/createorupdate).

Une fois la lecture du Scale Out activée pour une base de données, les applications se connectant à cette base de données seront dirigées vers le réplica en lecture-écriture ou un réplica en lecture seule de cette base de données en fonction de la propriété `ApplicationIntent` configurée dans la chaîne de connexion de l’application. Pour plus d’informations sur la propriété `ApplicationIntent`, consultez [Spécification de l’intention de l’application](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent). 

Si l’échelle horizontale en lecture est désactivée ou si la propriété d’échelle lecture est définie dans un niveau de service non pris en charge, toutes les connexions sont dirigées vers le réplica en lecture-écriture, indépendamment de la propriété `ApplicationIntent`.  

## <a name="conclusion"></a>Conclusion
Azure SQL Database est étroitement intégré à la plateforme Azure et dépend très largement de Service Fabric pour la détection des défaillances et la récupération, mais aussi des objets blob de stockage Azure pour la protection des données et des zones de disponibilité. Parallèlement à cela, Azure SQL Database utilise pleinement la technologie des groupes de disponibilité Always On intégrée à SQL Server pour la réplication et le basculement. La combinaison de ces technologies permet aux applications de profiter pleinement des avantages d’un modèle de stockage mixte et de prendre en charge les contrats de niveau de service les plus exigeants. 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Zones de disponibilité Azure](../availability-zones/az-overview.md)
- En savoir plus sur [Service Fabric](../service-fabric/service-fabric-overview.md)
- En savoir plus sur [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). 
