---
title: Usar GPUs no AKS (Serviço do Kubernetes do Azure)
description: Saiba como usar GPUs para computação de alto desempenho ou cargas de trabalho de uso intensivo de gráficos no AKS (Serviço de Kubernetes do Azure)
services: container-service
author: zr-msft
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 150eaa6a4df558ed0c737d99cbcc8010baf63e96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466385"
---
# <a name="use-gpus-for-compute-intensive-workloads-on-azure-kubernetes-service-aks"></a>Usar GPUs para cargas de trabalho de computação intensiva no AKS (Serviço de Kubernetes do Azure)

As GPUs (unidades de processamento gráfico) geralmente são usadas para cargas de trabalho de computação intensiva, como gráficos e cargas de trabalho de visualização. O AKS dá suporte à criação de pools de nós habilitados para GPU para executar essas cargas de trabalho de computação intensiva no Kubernetes. Para obter mais informações sobre as VMs habilitadas para GPU disponíveis, confira [Tamanhos de VM otimizadas para GPU no Azure][gpu-skus]. Para nós do AKS, é recomendável um tamanho mínimo de *Standard_NC6*.

> [!NOTE]
> As VMs habilitadas para GPU contêm hardware especializado sujeito a maiores preços e disponibilidade da região. Para obter mais informações, confira a ferramenta [preço][azure-pricing] e a [disponibilidade de região][azure-availability].

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS com nós compatíveis com GPUs. O cluster do AKS deve executar o Kubernetes 1.10 ou posterior. Se você precisar um cluster do AKS que atenda a esses requisitos, confira a primeira seção deste artigo para [criar um cluster do AKS](#create-an-aks-cluster).

Você também precisa da CLI do Azure versão 2.0.59 ou posterior instalado e configurado. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

Se você precisar de um cluster do AKS que atenda aos requisitos mínimos (nó habilitado para GPU e Kubernetes versão 1.10 ou posterior), conclua as etapas a seguir. Se você já tiver um cluster do AKS que atende a esses requisitos [pular para a próxima seção](#confirm-that-gpus-are-schedulable).

Primeiro, crie um grupo de recursos para o cluster usando o comando [az group create][az-group-create]. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na região *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Agora, crie um cluster do AKS usando o comando [az aks create][az-aks-create]. O exemplo a seguir cria um cluster com um único nó de tamanho `Standard_NC6`, e executa a versão 1.11.7 do Kubernetes:

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-vm-size Standard_NC6 \
    --node-count 1 \
    --kubernetes-version 1.11.8
```

Obtenha as credenciais do seu cluster do AKS usando o comando [az aks get-credentials][az-aks-get-credentials]:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="confirm-that-gpus-are-schedulable"></a>Confirmar se as GPUs são agendáveis

Com o cluster do AKS criado, confirme se as GPUs são agendáveis no Kubernetes. Primeiro, liste os nós no seu cluster usando o comando [kubectl get nodes][kubectl-get]:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-28993262-0   Ready    agent   6m    v1.11.7
```

Agora use o comando [kubectl describe node][kubectl-describe] para confirmar se as GPUs são agendáveis. Na seção *Capacidade*, a GPU deve ser listada como `nvidia.com/gpu:  1`. Se você não vir as GPUs, confira a seção [Solucionar problemas de disponibilidade de GPU](#troubleshoot-gpu-availability).

O exemplo condensado a seguir mostra que uma GPU está disponível no nó chamado *aks-nodepool1-18821093-0*:

```
$ kubectl describe node aks-nodepool1-28993262-0

Name:               aks-nodepool1-28993262-0
Roles:              agent
Labels:             accelerator=nvidia

[...]

Capacity:
 cpu:                6
 ephemeral-storage:  30428648Ki
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             57713780Ki
 nvidia.com/gpu:     1
 pods:               110
Allocatable:
 cpu:                5916m
 ephemeral-storage:  28043041951
 hugepages-1Gi:      0
 hugepages-2Mi:      0
 memory:             52368500Ki
 nvidia.com/gpu:     1
 pods:               110
System Info:
 Machine ID:                 9148b74152374d049a68436ac59ee7c7
 System UUID:                D599728C-96F3-B941-BC79-E0B70453609C
 Boot ID:                    a2a6dbc3-6090-4f54-a2b7-7b4a209dffaf
 Kernel Version:             4.15.0-1037-azure
 OS Image:                   Ubuntu 16.04.5 LTS
 Operating System:           linux
 Architecture:               amd64
 Container Runtime Version:  docker://1.13.1
 Kubelet Version:            v1.11.7
 Kube-Proxy Version:         v1.11.7
PodCIDR:                     10.244.0.0/24
ProviderID:                  azure:///subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Compute/virtualMachines/aks-nodepool1-28993262-0
Non-terminated Pods:         (9 in total)
  Namespace                  Name                                     CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                  ----                                     ------------  ----------  ---------------  -------------  ---
  gpu-resources              nvidia-device-plugin-97zfc               0 (0%)        0 (0%)      0 (0%)           0 (0%)         2m4s

[...]
```

## <a name="run-a-gpu-enabled-workload"></a>Executar uma carga de trabalho habilitada para GPU

Para ver a GPU em ação, agende uma carga de trabalho habilitada para GPU com a solicitação de recurso adequada. Neste exemplo, vamos executar um trabalho do [Tensorflow](https://www.tensorflow.org/versions/r1.1/get_started/mnist/beginners) no [conjunto de dados MNIST](http://yann.lecun.com/exdb/mnist/).

Crie um arquivo chamado *samples-tf-mnist-demo.yaml* e cole o manifesto YAML a seguir. O manifesto de trabalho a seguir inclui um limite de recurso de `nvidia.com/gpu: 1`:

> [!NOTE]
> Se você receber um erro de incompatibilidade de versão durante uma chamada a drivers, como: a versão do driver CUDA é insuficiente para a versão do tempo de execução CUDA, examine o gráfico da matriz de compatibilidade do driver nVidia – [https://docs.nvidia.com/deploy/cuda-compatibility/index.html](https://docs.nvidia.com/deploy/cuda-compatibility/index.html)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    app: samples-tf-mnist-demo
  name: samples-tf-mnist-demo
spec:
  template:
    metadata:
      labels:
        app: samples-tf-mnist-demo
    spec:
      containers:
      - name: samples-tf-mnist-demo
        image: microsoft/samples-tf-mnist-demo:gpu
        args: ["--max_steps", "500"]
        imagePullPolicy: IfNotPresent
        resources:
          limits:
           nvidia.com/gpu: 1
      restartPolicy: OnFailure
```

Use o comando [kubectl apply][kubectl-apply] para executar o aplicativo. Esse comando analisa o arquivo de manifesto e cria os objetos Kubernetes definidos:

```console
kubectl apply -f samples-tf-mnist-demo.yaml
```

## <a name="view-the-status-and-output-of-the-gpu-enabled-workload"></a>Exibir o status e a saída da carga de trabalho habilitada para GPU

Monitore o andamento do trabalho usando o comando [kubectl get jobs][kubectl-get] com o argumento `--watch`. Poderá levar alguns minutos para efetuar pull da imagem e processar o conjunto de dados pela primeira vez. Quando a coluna *CONCLUSÕES* mostrar *1/1*, o trabalho terá sido concluído com êxito:

```
$ kubectl get jobs samples-tf-mnist-demo --watch

NAME                    COMPLETIONS   DURATION   AGE

samples-tf-mnist-demo   0/1           3m29s      3m29s
samples-tf-mnist-demo   1/1   3m10s   3m36s
```

Para examinar a saída da carga de trabalho habilitada para GPU, primeiro obtenha o nome dos pods com o comando [kubectl get pods][kubectl-get]:

```
$ kubectl get pods --selector app=samples-tf-mnist-demo

NAME                          READY   STATUS      RESTARTS   AGE
samples-tf-mnist-demo-smnr6   0/1     Completed   0          3m
```

Agora use o comando [kubectl logs][kubectl-logs] para exibir os logs de pod. Os logs de pod de exemplo a seguir confirmam que o dispositivo GPU correto foi descoberto, `Tesla K80`. Forneça o nome para o seu pod:

```
$ kubectl logs samples-tf-mnist-demo-smnr6

2019-02-28 23:47:34.749013: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2019-02-28 23:47:34.879877: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: 3130:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2019-02-28 23:47:34.879915: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: 3130:00:00.0, compute capability: 3.7)
2019-02-28 23:47:39.492532: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
Accuracy at step 40: 0.8685
Accuracy at step 50: 0.8864
Accuracy at step 60: 0.901
Accuracy at step 70: 0.905
Accuracy at step 80: 0.9103
Accuracy at step 90: 0.9126
Adding run metadata for 99
Accuracy at step 100: 0.9176
Accuracy at step 110: 0.9149
Accuracy at step 120: 0.9187
Accuracy at step 130: 0.9253
Accuracy at step 140: 0.9252
Accuracy at step 150: 0.9266
Accuracy at step 160: 0.9255
Accuracy at step 170: 0.9267
Accuracy at step 180: 0.9257
Accuracy at step 190: 0.9309
Adding run metadata for 199
Accuracy at step 200: 0.9272
Accuracy at step 210: 0.9321
Accuracy at step 220: 0.9343
Accuracy at step 230: 0.9388
Accuracy at step 240: 0.9408
Accuracy at step 250: 0.9394
Accuracy at step 260: 0.9412
Accuracy at step 270: 0.9422
Accuracy at step 280: 0.9436
Accuracy at step 290: 0.9411
Adding run metadata for 299
Accuracy at step 300: 0.9426
Accuracy at step 310: 0.9466
Accuracy at step 320: 0.9458
Accuracy at step 330: 0.9407
Accuracy at step 340: 0.9445
Accuracy at step 350: 0.9486
Accuracy at step 360: 0.9475
Accuracy at step 370: 0.948
Accuracy at step 380: 0.9516
Accuracy at step 390: 0.9534
Adding run metadata for 399
Accuracy at step 400: 0.9501
Accuracy at step 410: 0.9552
Accuracy at step 420: 0.9535
Accuracy at step 430: 0.9545
Accuracy at step 440: 0.9533
Accuracy at step 450: 0.9526
Accuracy at step 460: 0.9566
Accuracy at step 470: 0.9547
Accuracy at step 480: 0.9548
Accuracy at step 490: 0.9545
Adding run metadata for 499
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover os objetos de Kubernetes associados criados neste artigo, use o comando [kubectl delete job][kubectl delete] da seguinte maneira:

```console
kubectl delete jobs samples-tf-mnist-demo
```

## <a name="troubleshoot-gpu-availability"></a>Solucionar problemas de disponibilidade de GPU

Se você não vir as GPUs como disponíveis em seus nós, será necessário implantar um DaemonSet para o plug-in do dispositivo nVidia. Este DaemonSet executa um pod em cada nó para fornecer os drivers necessários para as GPUs.

Primeiro, crie um namespace usando o comando [kubectl create namespace][kubectl-create], como *gpu-resources*:

```console
kubectl create namespace gpu-resources
```

Crie um arquivo chamado *nvidia-device-plugin-ds.yaml* e cole o manifesto YAML a seguir. Atualize o `image: nvidia/k8s-device-plugin:1.11` do manifesto para corresponder à sua versão do Kubernetes. Por exemplo, se o cluster do AKS executar Kubernetes versão 1.12, atualize a marca a `image: nvidia/k8s-device-plugin:1.12`.

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  labels:
    kubernetes.io/cluster-service: "true"
  name: nvidia-device-plugin
  namespace: gpu-resources
spec:
  template:
    metadata:
      # Mark this pod as a critical add-on; when enabled, the critical add-on scheduler
      # reserves resources for critical add-on pods so that they can be rescheduled after
      # a failure.  This annotation works in tandem with the toleration below.
      annotations:
        scheduler.alpha.kubernetes.io/critical-pod: ""
      labels:
        name: nvidia-device-plugin-ds
    spec:
      tolerations:
      # Allow this pod to be rescheduled while the node is in "critical add-ons only" mode.
      # This, along with the annotation above marks this pod as a critical add-on.
      - key: CriticalAddonsOnly
        operator: Exists
      containers:
      - image: nvidia/k8s-device-plugin:1.11 # Update this tag to match your Kubernetes version
        name: nvidia-device-plugin-ctr
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop: ["ALL"]
        volumeMounts:
          - name: device-plugin
            mountPath: /var/lib/kubelet/device-plugins
      volumes:
        - name: device-plugin
          hostPath:
            path: /var/lib/kubelet/device-plugins
      nodeSelector:
        beta.kubernetes.io/os: linux
        accelerator: nvidia
```

Agora, use o comando [kubectl apply][kubectl-apply] para criar o DaemonSet:

```
$ kubectl apply -f nvidia-device-plugin-ds.yaml

daemonset "nvidia-device-plugin" created
```

Execute o comando [kubectl describe node][kubectl-describe] novamente para verificar se a GPU já está disponível no nó.

## <a name="next-steps"></a>Próximos passos

Para executar trabalhos do Apache Spark, confira [Executar trabalhos do Apache Spark no AKS][aks-spark].

Para obter mais informações sobre execução de cargas de trabalho de ML (aprendizado de máquina) no Kubernetes, confira [laboratórios do Kubeflow][kubeflow-labs].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeflow-labs]: https://github.com/Azure/kubeflow-labs
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[azure-pricing]: https://azure.microsoft.com/pricing/
[azure-availability]: https://azure.microsoft.com/global-infrastructure/services/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[aks-spark]: spark-job.md
[gpu-skus]: ../virtual-machines/linux/sizes-gpu.md
[install-azure-cli]: /cli/azure/install-azure-cli
