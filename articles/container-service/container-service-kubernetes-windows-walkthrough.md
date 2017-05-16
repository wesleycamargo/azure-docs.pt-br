---
title: Cluster Kubernetes do Azure para Windows | Microsoft Docs
description: "Implantar e começar a usar um cluster Kubernetes para contêineres do Windows no Serviço de Contêiner do Azure"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 4e730b65a98af05ea00c5f8ebd9914e3367b66a7
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Introdução ao Kubernetes e aos contêineres do Windows em Serviço de Contêiner


Este artigo mostra como criar um cluster do Kubernetes no Serviço de contêiner do Azure que contenha nós do Windows para executar contêineres do Windows. Introdução aos comandos da CLI 2.0 do Azure `az acs` para criar o cluster do Kubernetes no serviço de contêiner do Azure. Em seguida, use a ferramenta da linha de comando `kubectl` do Kubernetes para começar a trabalhar com contêineres do Windows criados a partir de imagens do Docker. 

> [!NOTE]
> O suporte para contêineres do Windows com Kubernetes no Serviço de Contêiner do Azure está em versão prévia. 
>



A imagem a seguir mostra a arquitetura de um cluster Kubernetes no Serviço de Contêiner do Azure com um nó mestre do Linux e dois nós de agente do Windows. 

![Imagem do cluster Kubernetes no Azure](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

* O mestre do Linux serve à API REST do Kubernetes e está acessível por meio de SSH na porta 22 ou por `kubectl` na porta 443. 
* Os nós de agente do Windows estão agrupados em um conjunto de disponibilidade do Azure e executam os seus contêineres. Os nós do Windows podem ser acessados por meio de um túnel RDP SSH através do nó mestre. As regras do Azure Load Balancer são adicionadas dinamicamente ao cluster dependendo dos serviços expostos.



Todas as VMs estão na mesma rede virtual privada e são totalmente acessíveis entre si. Todas as VMs executam um kubelet, um Docker e um proxy.

Para obter mais informações, confira a [Introdução ao serviço de contêiner do Azure](container-service-intro.md) e a [documentação Kubernetes](https://kubernetes.io/docs/home/).

## <a name="prerequisites"></a>Pré-requisitos
Para criar um cluster do Serviço de Contêiner do Azure usando a CLI do Azure 2.0, você deve:
* ter uma conta do Azure ([obtenha uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/))
* ter instalado e estar registrado na [CLI 2.0 do Azure](/cli/azure/install-az-cli2)

Para o cluster do Kubernetes, também é necessário seguir os seguintes passos. Você pode prepará-los antecipadamente ou usar as opções do comando `az acs create` para gerá-los automaticamente durante a implantação de cluster. 

* **Chave pública RSA SSH**: se você quiser criar chaves RSA Secure Shell (SSH), confira a orientação para [macOS e Linux](../virtual-machines/linux/mac-create-ssh-keys.md) ou [Windows](../virtual-machines/linux/ssh-from-windows.md). 

* **Segredo e ID do cliente de entidade de cliente**: para obter informações adicionais sobre as etapas para criar uma entidade de serviço do Azure Active Directory, confira [O que é a entidade de serviço para um cluster Kubernetes](container-service-kubernetes-service-principal.md).

O exemplo de comando neste artigo gera automaticamente a entidade de serviço e as chaves SSH.
  
## <a name="create-your-kubernetes-cluster"></a>Criar o cluster Kubernetes

Estes são alguns comandos da CLI 2.0 do Azure para criar o cluster. 

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos em um local onde o serviço de contêiner do Azure está [disponível](https://azure.microsoft.com/regions/services/). O comando a seguir cria um grupo de recursos denominado *myKubernetesResourceGroup* no local do *westus*:

```azurecli
az group create --name=myKubernetesResourceGroup --location=westus
```

### <a name="create-a-kubernetes-cluster-with-windows-agent-nodes"></a>Crie um cluster do Kubernetes com nós do agente do Windows

Crie um cluster do Kubernetes no seu grupo de recursos usando o comando `az acs create` com `--orchestrator-type=kubernetes` e com a opção do agente `--windows`. Para obter a sintaxe de comando, confira a `az acs create` [ajuda](/cli/azure/acs#create).

O comando a seguir cria um cluster do serviço de contêiner chamado *myKubernetesClusterName*, com um prefixo de DNS *myPrefix* para o nó de gerenciamento e as credenciais especificadas para acessar os nós do Windows. Esta versão do comando gera automaticamente as chaves RSA SSH e a entidade de serviço para o cluster do Kubernetes.


```azurecli
az acs create --orchestrator-type=kubernetes \
    --resource-group myKubernetesResourceGroup \
    --name=myKubernetesClusterName \
    --dns-prefix=myPrefix \
    --agent-count=2 \
    --generate-ssh-keys \
    --windows --admin-username myWindowsAdminName \
    --admin-password myWindowsAdminPassword
```

Após alguns minutos, o comando é concluído e você deve ter um cluster Kubernetes em funcionamento.

> [!IMPORTANT]
> Se sua conta não tiver permissões para criar a entidade de serviço do Azure AD, o comando gerará um erro semelhante ao `Insufficient privileges to complete the operation.` Para obter mais informações, confira [O que é a entidade de serviço para um cluster Kubernetes](container-service-kubernetes-service-principal.md). 
> 

## <a name="connect-to-the-cluster-with-kubectl"></a>Conecte-se ao cluster com kubectl

Para se conectar ao cluster do Kubernetes do computador cliente, use [`kubectl`](https://kubernetes.io/docs/user-guide/kubectl/), o cliente de linha de comando do Kubernetes. 

Se você não tiver `kubectl` instalado localmente, você pode instalá-lo com `az acs kubernetes install-cli`. (Você também pode baixá-lo do [site do Kubernetes](https://kubernetes.io/docs/tasks/kubectl/install/).)

**Linux ou macOS**

```azurecli
sudo az acs kubernetes install-cli
```

**Windows**
```azurecli
az acs kubernetes install-cli
```

> [!TIP]
> Por padrão, esse comando instala o binário `kubectl` em `/usr/local/bin/kubectl` em um sistema Linux ou macOS, ou em `C:\Program Files (x86)\kubectl.exe` no Windows. Para especificar um caminho de instalação diferente, use o parâmetro `--install-location`.
>
> Depois que `kubectl` for instalado, verifique se seu diretório está no caminho do sistema ou adicione-o ao caminho. 


Em seguida, execute o comando a seguir para baixar a configuração do cluster do Kubernetes mestre para o arquivo `~/.kube/config`:

```azurecli
az acs kubernetes get-credentials --resource-group=myKubernetesResourceGroup --name=myKubernetesClusterName
```

Agora, você deve estar pronto para acessar o cluster em seu computador. Experimente executar:

```bash
kubectl get nodes
```

Verifique se é possível ver uma lista dos computadores no cluster.

![Nós em execução em um cluster do Kubernetes](media/container-service-kubernetes-windows-walkthrough/kubectl-get-nodes.png)

## <a name="create-your-first-kubernetes-service"></a>Criar seu primeiro serviço Kubernetes

Depois de criar o cluster e conectar-se com `kubectl`, tente iniciar um aplicativo do Windows de um contêiner do Docker e exponha-o à Internet. Este exemplo básico usa um arquivo JSON para especificar um contêiner do Servidor de informações de internet (IIS) da Microsoft e, depois, usa `kubctl apply` para criá-lo. 

1. Crie um arquivo local chamado `iis.json` e copie o seguinte. Esse arquivo informa ao Kubernetes para executar o IIS no Server Core do Windows Server 2016, usando uma imagem pública do [Docker Hub](https://hub.docker.com/r/microsoft/iis/). O contêiner usa a porta 80, mas inicialmente é acessível somente dentro da rede de cluster.

  ```JSON
  {
    "apiVersion": "v1",
    "kind": "Pod",
    "metadata": {
      "name": "iis",
      "labels": {
        "name": "iis"
      }
    },
    "spec": {
      "containers": [
        {
          "name": "iis",
          "image": "microsoft/iis",
          "ports": [
            {
            "containerPort": 80
            }
          ]
        }
      ],
      "nodeSelector": {
        "beta.kubernetes.io/os": "windows"
      }
    }
  }
  ```
2. Para iniciar o aplicativo, digite:  
  
  ```bash
  kubectl apply -f iis.json
  ```  
3. Para acompanhar a implantação do contêiner, digite:  
  ```bash
  kubectl get pods
  ```
  Enquanto o contêiner está implantando, o status é `ContainerCreating`. 

  ![Contêiner IIS no estado ContainerCreating](media/container-service-kubernetes-windows-walkthrough/iis-pod-creating.png)   

  Devido ao tamanho da imagem do IIS, pode levar vários minutos para que o contêiner entre no estado `Running`.

  ![Contêiner IIS no estado de execução](media/container-service-kubernetes-windows-walkthrough/iis-pod-running.png)

4. Para expor o contêiner para o mundo, digite o seguinte comando:

  ```bash
  kubectl expose pods iis --port=80 --type=LoadBalancer
  ```

  Com este comando, o Kubernetes cria uma regra de balanceador de carga do Azure com um endereço IP público. A alteração demora alguns minutos para se propagar ao balanceador de carga. Para obter mais informações, confira [Como balancear a carga de contêineres em um cluster do Kubernetes no Serviço de contêiner do Azure](container-service-kubernetes-load-balancing.md).

5. Execute o seguinte comando para ver o status do serviço.

  ```bash
  kubectl get svc
  ```

  Inicialmente o endereço IP é exibido como `pending`:

  ![Endereço IP externo pendente](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose.png)

  Depois de alguns minutos, o endereço IP é definido:
  
  ![Endereço IP externo do IIS](media/container-service-kubernetes-windows-walkthrough/iis-svc-expose-public.png)


6. Depois que o endereço IP externo estiver disponível, você poderá usar o seu navegador para acessá-lo:

  ![Imagem da navegação até o IIS](media/container-service-kubernetes-windows-walkthrough/kubernetes-iis.png)  

7. Para excluir o pod do IIS, digite:

  ```bash
  kubectl delete pods iis
  ```

## <a name="next-steps"></a>Próximas etapas

* Para usar a IU do Kubernetes, execute o comando `kubectl proxy`. Em seguida, navegue até http://localhost:8001/ui.

* Para aprender a compilar um site IIS personalizado e executá-lo em um contêiner do Windows, confira o guia em [Docker Hub](https://hub.docker.com/r/microsoft/iis/).

* Para acessar os nós do Windows por meio de um túnel RDP SSH para o mestre com PuTTy, confira a [documentação do mecanismo do ACS](https://github.com/Azure/acs-engine/blob/master/docs/ssh.md#create-port-80-tunnel-to-the-master). 

