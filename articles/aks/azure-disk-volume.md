---
title: Utiliser des disques Azure avec AKS
description: Utiliser des disques Azure avec AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: aa9b92df84a48ef4cb706e9e89e0f6c0a25cd42a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420490"
---
# <a name="volumes-with-azure-disks"></a>Volumes avec disques Azure

Les applications basées sur des conteneurs doivent souvent consulter et conserver des données dans un volume de données externe. Les disques Azure peuvent être utilisés comme banque de données externe. Cet article explique comment utiliser un disque Azure comme volume Kubernetes dans un cluster Azure Kubernetes Service (AKS).

Pour plus d’informations sur les volumes Kubernetes, consultez [Volumes Kubernetes][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Créer un disque Azure

Avant de monter un disque managé Azure comme volume Kubernetes, le disque doit se trouver dans le groupe de ressources du **nœud** AKS. Obtenez le nom du groupe de ressources avec la commande [az resource show][az-resource-show].

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Utilisez la commande [az disk create][az-disk-create] pour créer le disque Azure.

Mettez à jour `--resource-group` avec le nom du groupe de ressources collecté à la dernière étape et `--name` avec le nom de votre choix.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Une fois que le disque a été créé, vous devriez voir une sortie similaire à celle qui suit. Cette valeur correspond à l’ID du disque, qui est utilisé lors du montage du disque.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```
> [!NOTE]
> Les disques managés Azure sont facturés par référence (SKU) pour une taille donnée. Ces références SKU vont de 32 Gio pour les disques S4 ou P4 à 4 Tio pour les disques S50 ou P50. En outre, le débit et les performances d’E/S d’un disque managé Premium dépendent à la fois de la référence SKU et de la taille d’instance des nœuds du cluster AKS. Consultez [Tarification et performances des disques managés][managed-disk-pricing-performance].

> [!NOTE]
> Si vous souhaitez créer le disque dans un groupe de ressources distinct, ajoutez également le principal de service Azure Kubernetes Service (AKS) de votre cluster au groupe de ressources contenant le disque qui possède le rôle `Contributor`. 
>

## <a name="mount-disk-as-volume"></a>Monter le disque en tant que volume

Montez le disque Azure dans votre pod en configurant le volume dans les spécifications du conteneur.

Créez un fichier nommé `azure-disk-pod.yaml` avec le contenu suivant. Mettez à jour `diskName` avec le nom du disque nouvellement créé, et `diskURI` avec l’ID du disque. Notez également la valeur de `mountPath`. Il s’agit du chemin du disque Azure qui a été monté dans le pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Utilisez kubectl pour créer le pod.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Vous disposez maintenant d’un pod en cours d’exécution sur lequel est monté un disque Azure à l’emplacement suivant : `/mnt/azure`.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment les volumes Kubernetes utilisent les disques Azure.

> [!div class="nextstepaction"]
> [Plug-in Kubernetes pour les disques Azure][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
