---
title: Руководство по Kubernetes в Azure. Масштабирование приложения
description: Руководство по AKS. Масштабирование приложения
services: container-service
author: dlepow
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 22f7f9aee791d315300ffdc4dc9f708a80a5baf7
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39127416"
---
# <a name="tutorial-scale-application-in-azure-kubernetes-service-aks"></a>Руководство. Масштабирование приложения в службе Azure Kubernetes

Если вы выполнили инструкции в руководствах, то у вас имеется работающий кластер Kubernetes в AKS и вы развернули в нем приложение Vote Azure.

В этом руководстве (часть 5 из 7) описывается масштабирование pod, содержащихся в приложении, и их автомасштабирование. Вы также узнаете, как масштабировать количество узлов виртуальной машины Azure, чтобы менять емкость кластера для размещения рабочих нагрузок. Вам предстоят следующие задачи:

> [!div class="checklist"]
> * Масштабирование узлов Kubernetes Azure
> * масштабирование pod Kubernetes вручную;
> * настройка автомасштабирования pod, в которых выполняется интерфейсная часть приложения;

В последующих руководствах описано, как обновить приложение Azure для голосования до новой версии.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образ контейнера, этот образ был передан в реестр контейнеров Azure и был создан кластер Kubernetes. Затем приложение было запущено в кластере Kubernetes.

Если вы не выполнили эти действия и хотите продолжить работу, вернитесь к руководству по [созданию образов контейнеров (часть 1)][aks-tutorial-prepare-app].

## <a name="scale-aks-nodes"></a>Масштабирование узлов AKS

Если вы создали кластер Kubernetes с помощью команд в предыдущем руководстве, то у него один узел. Если вы планируете увеличение или уменьшение рабочих нагрузок контейнеров в кластере, то можете соответствующим образом изменить число узлов вручную.

В следующем примере в кластере Kubernetes *myAKSCluster* число узлов увеличивается до трех. Для выполнения этой команды требуется несколько минут.

```azurecli
az aks scale --resource-group=myResourceGroup --name=myAKSCluster --node-count 3
```

Выходные данные должны быть следующего вида.

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="manually-scale-pods"></a>Масштабирование pod вручную

К настоящему моменту было развернуто по отдельной реплике внешнего приложения Vote Azure и экземпляра Redis. Чтобы проверить это, выполните команду [kubectl get][kubectl-get].

```azurecli
kubectl get pods
```

Выходные данные:

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Вручную измените число групп pod в развертывании `azure-vote-front`, выполнив команду [kubectl scale][kubectl-scale]. В этом примере их число увеличивается до 5.

```azurecli
kubectl scale --replicas=5 deployment/azure-vote-front
```

Выполните команду [kubectl get pods][kubectl-get] и проверьте, что Kubernetes создает новые группы pod. Дополнительные pod будут запущены примерно через минуту.

```azurecli
kubectl get pods
```

Выходные данные:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Автомасштабирование pod

Kubernetes поддерживает [горизонтальное автомасштабирование pod][kubernetes-hpa] для изменения числа групп pod в развертывании в зависимости от использования ЦП или других выбранных метрик. [Сервер метрик][metrics-server] используется для предоставления сведений об использовании ресурсов в Kubernetes. Чтобы установить сервер метрик, клонируйте репозиторий GitHub `metrics-server` и установите примеры определений ресурсов. Чтобы просмотреть содержимое этих определений YAML, см. руководство по [использованию сервера метрик для Kuberenetes 1.8+][metrics-server-github].

```console
git clone https://github.com/kubernetes-incubator/metrics-server.git
kubectl create -f metrics-server/deploy/1.8+/
```

Чтобы использовать инструмент автомасштабирования, для ваших pod необходимо определить запросы и лимиты ресурсов ЦП. В развертывании `azure-vote-front` каждый контейнер внешнего приложения запрашивает 0,25 ресурсов ЦП с лимитом в 0,5 ресурсов ЦП. Параметры имеют следующий вид.

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

В следующем примере используется команда [kubectl autoscale][kubectl-autoscale] для автомасштабирования числа групп pod в развертывании `azure-vote-front`. Если использование ЦП превышает 50 %, то инструмент автомасштабирования увеличивает число pod максимум до 10.

```azurecli
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Выполните следующую команду, чтобы просмотреть состояние инструмента автомасштабирования.

```azurecli
kubectl get hpa
```

Выходные данные:

```
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Через несколько минут минимальной нагрузки на приложение Vote Azure число реплик pod автоматически уменьшится до 3.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы использовали различные возможности масштабирования кластера Kubernetes. Были рассмотрены такие задачи:

> [!div class="checklist"]
> * масштабирование pod Kubernetes вручную;
> * настройка автомасштабирования pod, в которых выполняется интерфейсная часть приложения;
> * Масштабирование узлов Kubernetes Azure

Перейдите к следующему руководству, чтобы узнать об обновлении приложения в Kubernetes.

> [!div class="nextstepaction"]
> [Обновление приложения в Kubernetes][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-incubator/metrics-server/tree/master/deploy/1.8%2B
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/core-metrics-pipeline/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
