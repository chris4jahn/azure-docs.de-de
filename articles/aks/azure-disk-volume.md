---
title: Verwenden von Azure-Datenträgern mit AKS
description: Verwenden von Azure-Datenträgern mit AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 03/08/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e6b2d6e31a843259001a36ffb8c588c879a3f2b9
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/09/2018
---
# <a name="volumes-with-azure-disks"></a>Volumes mit Azure-Datenträgern

Containerbasierte Anwendungen müssen häufig auf Daten in einem externen Datenvolume zugreifen und diese dauerhaft speichern. Azure-Datenträger können als ein solcher externer Datenspeicher verwendet werden. In diesem Artikel wird erläutert, wie ein Azure-Datenträger als Kubernetes-Volume in einem Azure Container Service-Cluster (AKS) verwendet wird.

Weitere Informationen zu Kubernetes-Volumes finden Sie unter [Kubernetes-Volumes][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Erstellen eines Azure-Datenträgers

Vor dem Einbinden eines verwalteten Azure-Datenträgers als Kubernetes-Volume muss der Datenträger in derselben Ressourcengruppe wie die AKS-Clusterressourcen vorhanden sein. Um diese Ressourcengruppe zu finden, verwenden Sie den Befehl [az group list][az-group-list].

```azurecli-interactive
az group list --output table
```

Sie suchen eine Ressourcengruppe mit einem ähnlichen Namen wie `MC_clustername_clustername_locaton`, wobei „clustername“ der Name Ihres AKS-Clusters ist und „location“ die Azure-Region, in der der Cluster bereitgestellt wurde.

```console
Name                                 Location    Status
-----------------------------------  ----------  ---------
MC_myAKSCluster_myAKSCluster_eastus  eastus      Succeeded
myAKSCluster                         eastus      Succeeded
```

Verwenden Sie den Befehl [az disk create][az-disk-create], um den Azure-Datenträger zu erstellen. 

Ersetzen Sie in diesem Beispiel den Wert von `--resource-group` durch den Namen der Ressourcengruppe und den Wert von `--name` durch einen Namen Ihrer Wahl.

```azurecli-interactive
az disk create \
  --resource-group MC_myAKSCluster_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Nachdem der Datenträger erstellt wurde, sollte eine Ausgabe ähnlich der folgenden angezeigt werden. Dieser Wert ist die Datenträger-ID, die beim Einbinden des Datenträgers in einen Kubernetes-Pod verwendet wird.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-file-share-as-volume"></a>Einbinden einer Dateifreigabe als Volume

Sie binden den Azure-Datenträger in Ihren Pod ein, indem Sie das Volume in den Containerspezifikationen konfigurieren. 

Erstellen Sie eine neue Datei namens „`azure-disk-pod.yaml`“ mit folgendem Inhalt. Ersetzen Sie den Wert von `diskName` durch den Namen des neu erstellten Datenträgers und den Wert von `diskURI` durch die Datenträger-ID. Notieren Sie auch den Wert `mountPath`. Dies ist der Pfad, unter dem der Azure-Datenträger im Pod eingebunden wird.

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

Verwenden Sie kubectl, um den Pod zu erstellen.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Sie verfügen nun über einen ausgeführten Pod mit einem Azure-Datenträger, der unter `/mnt/azure` eingebunden wurde.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Kubernetes-Volumes mit Azure-Datenträgern.

> [!div class="nextstepaction"]
> [Kubernetes-Plug-In für Azure-Datenträger][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
