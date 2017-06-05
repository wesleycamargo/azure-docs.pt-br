---
title: "Balancear a carga de contêineres Kubernetes no Azure | Microsoft Docs"
description: "Conecte-se externamente e balanceie a carga em vários contêineres em um cluster Kubernetes no Serviço de Contêiner do Azure."
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Contêineres, Microsserviços, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: danlep
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 9046879158a4617d478bcf1157d5ead3c1054fd8
ms.contentlocale: pt-br
ms.lasthandoff: 05/18/2017


---
# <a name="load-balance-containers-in-a-kubernetes-cluster-in-azure-container-service"></a>Balancear a carga de contêineres em um cluster Kubernetes no Serviço de Contêiner do Azure 
Este artigo apresenta o balanceamento de carga em um cluster Kubernetes no Serviço de Contêiner do Azure. O balanceamento de carga fornece um endereço IP acessível externamente para o serviço e distribui o tráfego de rede entre os pods em execução em VMs do agente.

Você pode configurar um serviço Kubernetes para usar o [Azure Load Balancer](../load-balancer/load-balancer-overview.md) para gerenciar o tráfego de rede externo (TCP). Com uma configuração adicional, o balanceamento de carga e roteamento de tráfego HTTP ou HTTPS ou cenários mais avançados são possíveis.

## <a name="prerequisites"></a>Pré-requisitos
* [Implantar um cluster Kubernetes](container-service-kubernetes-walkthrough.md) no Serviço de Contêiner do Azure
* [Conectar o cliente](container-service-connect.md) ao cluster

## <a name="azure-load-balancer"></a>Azure Load Balancer

Por padrão, um cluster Kubernetes implantado no Serviço de Contêiner do Azure inclui um Azure Load Balancer voltado para a Internet para VMs do agente. (Um recurso de balanceador de carga separado é configurado para as VMs mestres.) O Azure Load Balancer é um balanceador de carga de Camada 4. No momento, o balanceador de carga dá suporte apenas a tráfego TCP em Kubernetes.

Ao criar um serviço Kubernetes, você pode configurar automaticamente o Azure Load Balancer para permitir o acesso ao serviço. Para configurar o balanceador de carga, defina o serviço `type` como `LoadBalancer`. O balanceador de carga cria uma regra para mapear um endereço IP público e o número da porta do tráfego do serviço de entrada para os endereços IP privados e números de porta dos pods nas VMs do agente (e vice-versa para o tráfego de resposta). 

 A seguir estão dois exemplos que mostram como configurar o serviço Kubernetes `type` como `LoadBalancer`. (Depois de experimentar os exemplos, exclua as implantações se não precisar mais delas.)

### <a name="example-use-the-kubectl-expose-command"></a>Exemplo: use o comando `kubectl expose` 
O [passo a passo do Kubernetes](container-service-kubernetes-walkthrough.md) inclui um exemplo de como expor um serviço com o comando `kubectl expose` e seu sinalizador `--type=LoadBalancer`. Siga estas etapas:

1. Inicie uma nova implantação do contêiner. Por exemplo, o comando a seguir inicia uma nova implantação chamada `mynginx`. A implantação consiste em três contêineres com base na imagem do Docker para o servidor Web Nginx.

    ```console
    kubectl run mynginx --replicas=3 --image nginx
    ```
2. Verifique se os contêineres estão em execução. Por exemplo, se você consultar os contêineres com `kubectl get pods`, verá uma saída semelhante à seguinte:

    ![Obter contêineres Nginx](./media/container-service-kubernetes-load-balancing/nginx-get-pods.png)

3. Para configurar o balanceador de carga para aceitar tráfego externo para a implantação, execute `kubectl expose` com `--type=LoadBalancer`. O comando a seguir expõe o servidor Nginx na porta 80:

    ```console
    kubectl expose deployments mynginx --port=80 --type=LoadBalancer
    ```

4. Digite `kubectl get svc` para ver o estado dos serviços no cluster. Enquanto o balanceador de carga configura a regra, o `EXTERNAL-IP` do serviço aparece como `<pending>`. Depois de alguns minutos, o endereço IP externo está configurado: 

    ![Configurar o Azure Load Balancer](./media/container-service-kubernetes-load-balancing/nginx-external-ip.png)

5. Verifique se você pode acessar o serviço no endereço IP externo. Por exemplo, abra um navegador da Web para o endereço IP exibido. O navegador mostra servidor Web Nginx em execução em um dos contêineres. Ou execute o comando `curl` ou `wget`. Por exemplo:

    ```
    curl 13.82.93.130
    ```

    Você deve ver uma saída semelhante a:

    ![Acessar o Nginx com curl](./media/container-service-kubernetes-load-balancing/curl-output.png)

6. Para ver a configuração do Azure Load Balancer, acesse o [Portal do Azure](https://portal.azure.com).

7. Navegue até o grupo de recursos para o cluster do serviço de contêiner e selecione o balanceador de carga para VMs do agente. Seu nome deve ser igual ao do serviço de contêiner. (Não escolha o balanceador de carga para os nós mestres, aquele cujo nome inclui **master-lb**.) 

    ![Balanceador de carga no grupo de recursos](./media/container-service-kubernetes-load-balancing/container-resource-group-portal.png)

8. Para ver os detalhes de configuração do balanceador de carga, clique em **Regras de balanceamento de carga** e no nome da regra que foi configurada.

    ![Regras do balanceador de carga](./media/container-service-kubernetes-load-balancing/load-balancing-rules.png) 

### <a name="example-specify-type-loadbalancer-in-the-service-configuration-file"></a>Exemplo: especifique `type: LoadBalancer` no arquivo de configuração de serviço

Se você implantar um aplicativo de contêiner Kubernetes de [arquivo de configuração de serviço](https://kubernetes.io/docs/user-guide/services/operations/#service-configuration-file) JSON ou YAML, especifique um balanceador externo de carga adicionando a seguinte linha à especificação do serviço:

```YAML
 "type": "LoadBalancer"
``` 



As etapas a seguir usam o [exemplo Guestbook](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook) do Kubernetes. Este exemplo é um aplicativo Web de várias camadas com base em imagens do Docker PHP e Redis. Você pode especificar no arquivo de configuração de serviço que o servidor PHP front-end usa o Azure Load Balancer.

1. Baixe o arquivo `guestbook-all-in-one.yaml` do [GitHub](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook/all-in-one). 
2. Procure o `spec` para o serviço `frontend`.
3. Remova a marca de comentário da linha `type: LoadBalancer`.

    ![Balanceador de carga na configuração de serviço](./media/container-service-kubernetes-load-balancing/guestbook-frontend-loadbalance.png)

4. Salve o arquivo e implante o aplicativo executando o seguinte comando:

    ```
    kubectl create -f guestbook-all-in-one.yaml
    ```

5. Digite `kubectl get svc` para ver o estado dos serviços no cluster. Enquanto o balanceador de carga configura a regra, o `EXTERNAL-IP` do serviço `frontend` aparece como `<pending>`. Depois de alguns minutos, o endereço IP externo está configurado: 

    ![Configurar o Azure Load Balancer](./media/container-service-kubernetes-load-balancing/guestbook-external-ip.png)

6. Verifique se você pode acessar o serviço no endereço IP externo. Por exemplo, você pode abrir um navegador da Web para o endereço IP externo do serviço.

    ![Acessar a página de recados externamente](./media/container-service-kubernetes-load-balancing/guestbook-web.png)

    Você pode adicionar entradas da página de recados.

7. Para ver a configuração do Azure Load Balancer, navegue até o recurso de balanceador de carga para o cluster no [Portal do Azure](https://portal.azure.com). Consulte as etapas no exemplo anterior.

### <a name="considerations"></a>Considerações

* A criação da regra do balanceador de carga ocorre de maneira assíncrona e as informações sobre o balanceador provisionado são publicadas no campo `status.loadBalancer` do servidor.
* Cada serviço recebe automaticamente a atribuição de seu próprio endereço IP virtual no balanceador de carga.
* Se você deseja alcançar o balanceador de carga por um nome DNS, trabalhe com seu provedor de serviços de domínio para criar um nome DNS para o endereço IP da regra.

## <a name="http-or-https-traffic"></a>Tráfego HTTP ou HTTPS

Para balancear a carga do tráfego HTTP ou HTTPS para aplicativos Web de contêiner e gerenciar os certificados para o protocolo TLS, você pode usar o recurso de [Entrada](https://kubernetes.io/docs/user-guide/ingress/) Kubernetes. Uma Entrada é uma coleção de regras que permite que conexões de entrada alcançar os serviços de cluster. Para um recurso de Entrada funcionar, o cluster Kubernetes deve ter uma [Controlador de entrada](https://kubernetes.io/docs/user-guide/ingress/#ingress-controllers) em execução.

O Serviço de Contêiner do Azure não implementa um controlador de Entrada Kubernetes automaticamente. Existem várias implementações de controlador disponíveis. Atualmente, o [controlador de Entrada Nginx](https://github.com/kubernetes/ingress/tree/master/examples/deployment/nginx) é recomendado para configurar regras de Entrada e balancear a carga do tráfego HTTP e HTTPS. 

Para saber mais, consulte a [Documentação do controlador de entrada Nginx](https://github.com/kubernetes/ingress/tree/master/controllers/nginx/README.md).

> [!IMPORTANT]
> Ao usar o controlador de Entrada Nginx no Serviço de Contêiner do Azure, você deve expor a implantação do controlador como um serviço com `type: LoadBalancer`. Isso configura o Azure Load Balancer para rotear o tráfego para o controlador. Para obter mais informações, consulte a seção anterior.


## <a name="next-steps"></a>Próximas etapas

* Consulte a [documentação do balanceador de carga Kubernetes](https://kubernetes.io/docs/user-guide/load-balancer/)
* Saiba mais sobre [controladores de Entrada e Entrada Kubernetes](https://kubernetes.io/docs/user-guide/ingress/)
* Consulte [exemplos do Kubernetes](https://github.com/kubernetes/kubernetes/tree/master/examples)


