---
title: Apporter votre propre clé pour Apache Kafka sur Azure HDInsight (préversion)
description: Cet article explique comment utiliser votre propre clé Azure Key Vault pour chiffrer des données stockées dans Apache Kafka sur Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 46105ee92a5c98cb8180b2499d0ad295702aac43
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953362"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>Apporter votre propre clé pour Apache Kafka sur Azure HDInsight (préversion)

Azure HDInsight assure la prise en charge du modèle BYOK (Bring Your Own Key) pour Apache Kafka. Cette fonctionnalité vous permet de posséder et de gérer les clés servant à chiffrer les données au repos. 

Tous les disques managés dans HDInsight sont protégés par Azure Storage Service Encryption (SSE). Par défaut, les données stockées sur ces disques sont chiffrées à l’aide de clés gérées par Microsoft. Si vous activez BYOK, vous devez fournir la clé de chiffrement pour permettre à HDInsight de l’utiliser et de la gérer à l’aide d’Azure Key Vault. 

Le chiffrement BYOK est un processus en une étape géré pendant la création d’un cluster sans coût supplémentaire. Il vous suffit d’inscrire HDInsight comme identité managée auprès d’Azure Key Vault et d’ajouter la clé de chiffrement pendant la création du cluster.

Tous les messages adressés au cluster Kafka (y compris les réplicas gérés par Kafka) sont chiffrés avec une clé de chiffrement de données (DEK) symétrique. La clé DEK est protégée avec la clé de chiffrement principale (KEK) de votre coffre de clés. Les processus de chiffrement et de déchiffrement sont entièrement gérés par Azure HDInsight. 

Vous pouvez utiliser le portail Azure ou Azure CLI pour faire alterner les clés du coffre de clés en toute sécurité. Quand une clé alterne, le cluster HDInsight Kafka démarre en quelques minutes en utilisant la nouvelle clé. Activez les fonctionnalités de protection de clés « Ne pas vider » et « Suppression réversible » pour vous protéger contre les scénarios de ransomware et de suppression accidentelle. Les clés dépourvues de ces fonctionnalités de protection ne sont pas prises en charge.

## <a name="get-started-with-byok"></a>Commencer avec BYOK

1. Créez des identités managées pour les ressources Azure.

   Pour vous authentifier auprès de Key Vault, créez une identité managée affectée par l’utilisateur via le [portail Azure](../../active-directory/managed-service-identity/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-service-identity/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-service-identity/how-to-manage-ua-identity-arm.md) ou [Azure CLI](../../active-directory/managed-service-identity/how-to-manage-ua-identity-cli.md). Si Azure Active Directory est obligatoire pour les identités managées et BYOK pour Kafka, le pack Sécurité Entreprise (ESP) n’est en revanche pas exigé. Veillez à enregistrer l’ID de ressource d’identité managée, vous en aurez besoin pour l’ajouter à la stratégie d’accès Key Vault.

   ![Créer une identité managée affectée par l’utilisateur dans le portail Azure](./media/apache-kafka-byok/user-managed-identity-portal.png)

2. Créez ou importez Azure Key Vault.

   HDInsight prend uniquement en charge Azure Key Vault. Si vous disposez de votre propre coffre de clés, vous pouvez importer vos clés dans Azure Key Vault. N’oubliez pas que les fonctionnalités « Suppression réversible » et « Ne pas vider » doivent être activées pour les clés. Les fonctionnalités « Suppression réversible » et « Ne pas vider » sont disponible via les interfaces REST, .NET/C#, PowerShell et Azure CLI.

   Pour créer un coffre de clés, suivez le guide de démarrage rapide [Azure Key Vault](../../key-vault/key-vault-get-started.md). Pour plus d’informations sur l’importation de clés existantes, consultez [Présentation des clés, des secrets et des certificats](../../key-vault/about-keys-secrets-and-certificates.md).

3. Ajoutez l’identité managée à la stratégie d’accès au coffre de clés.

   Créez une stratégie d’accès Azure Key Vault.

   ![Créer une stratégie d’accès Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

   Définissez **Autorisations de clé** sur **Obtenir**, **Ne pas inclure la clé** et **Inclure la clé**.

   ![Définir les autorisations de clé pour la stratégie d’accès Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

   Définissez **Autorisations du secret** sur **Obtenir**, **Définir** et **Supprimer**.

   ![Définir les autorisations de clé pour la stratégie d’accès Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

   Sous **Sélectionner le principal**, choisissez l’identité managée affectée par l’utilisateur que vous avez créé.

   ![Définir Sélectionner le principal pour la stratégie d’accès Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

4. Créer un cluster HDInsight

   Vous êtes maintenant prêt à créer un cluster HDInsight. BYOK ne peut être appliqué qu’aux nouveaux clusters, pendant leur création. Le chiffrement ne peut pas être retiré des clusters BYOK, et BYOK ne peut pas être ajouté aux clusters existants.

   ![Chiffrement de disque Kafka dans le portail Azure](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   Pendant la création du cluster, indiquez l’URL complète de la clé en incluant la version de la clé. Par exemple : `myakv.azure.com/KEK1/v1`. Vous devez aussi affecter l’identité managée au cluster et indiquer l’URI de la clé.

## <a name="faq-for-byok-to-kafka"></a>Questions fréquentes (FAQ) sur BYOK pour Kafka

**Comment le cluster Kafka accède-t-il à mon coffre de clés ?**

   Associez une identité managée au cluster HDInsight Kafka pendant la création de ce dernier. Cette identité managée peut être créée avant ou pendant la création du cluster. Vous devez aussi accorder à l’identité managée un accès au coffre de clés dans lequel la clé est stockée.

**Cette fonctionnalité est-elle accessible à tous les clusters Kafka sur HDInsight ?**

   Le chiffrement BYOK est possible uniquement pour les clusters Kafka version 1.1 et ultérieures.

**Est-il possible de disposer de clés différentes pour différentes rubriques/partitions ?**

   Non, tous les disques managés du cluster sont chiffrés par la même clé.

**Comment puis-je récupérer le cluster si les clés sont supprimées ?**

   Sachant que seules sont prises en charge les clés ayant la « Suppression réversible » activée, si les clés sont restaurés dans le coffre de clés, le cluster doit retrouver l’accès aux clés. Pour restaurer une clé Azure Key Vault, consultez [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey).

**Est-il possible de faire fonctionner des applications producteur/consommateur simultanément avec un cluster BYOK et un cluster non BYOK ?**

   Oui. L’utilisation de BYOK est transparente pour les applications producteur/consommateur. Le chiffrement se produit au niveau de la couche du système d’exploitation. Aucune modification n’a besoin d’être apportée aux applications Kafka producteur/consommateur existantes.

**Les disques de système d’exploitation/ressources sont-ils également chiffrés ?**

   Non. Les disques de système d’exploitation et de ressources ne sont pas chiffrés.

**En cas de montée en puissance d’un cluster, les nouveaux répartiteurs prennent-ils en charge BYOK sans interruption ?**

   Oui. Le cluster doit accéder à la clé dans le coffre de clés pendant la montée en puissance. La même clé sert à chiffrer tous les disques managés du cluster.

**La fonctionnalité BYOK est-elle disponible à mon emplacement ?**

   La fonctionnalité BYOK de Kafka est disponible dans tous les clouds publics.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault](../../key-vault/key-vault-whatis.md) ?
* Pour démarrer avec Azure Key Vault, consultez [Bien démarrer avec Azure Key Vault](../../key-vault/key-vault-get-started.md).
