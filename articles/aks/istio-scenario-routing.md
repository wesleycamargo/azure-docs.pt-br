---
title: Roteamento inteligente e versões canário com Istio no AKS (Serviço de Kubernetes do Azure)
description: Saiba como usar o Istio para fornecer roteamento inteligente e implantar versões canário em um cluster do AKS (Serviço de Kubernetes do Azure)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: 0a4e5e7e310a9949ee59291c2032eafda46955a9
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892761"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Usar roteamento inteligente e versões canário com Istio no AKS (Serviço de Kubernetes do Azure)

[Istio][istio-github] é uma malha de serviço de software livre que fornece um conjunto de chaves de funcionalidade nos microsserviços em um cluster do Kubernetes. Esses recursos incluem gerenciamento de tráfego, segurança e identidade do serviço, imposição de política e observabilidade. Para obter mais informações sobre o Istio, consulte a documentação oficial [O que é Istio?][istio-docs-concepts].

Este artigo mostra como usar a funcionalidade de gerenciamento de tráfego do Istio. Um aplicativo de votação de exemplo do AKS é usado para explorar o roteamento inteligente e as versões canário.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Implantar o aplicativo
> * Atualizar o aplicativo
> * Distribuir uma versão canário do aplicativo
> * Finalizar a distribuição

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste artigo presumem que você criou um cluster do AKS (Kubernetes 1.10 e superior, com RBAC habilitado) e estabeleceu uma conexão `kubectl` com o cluster. Também é necessário ter o Istio instalado no seu cluster.

Se você precisar de ajuda com qualquer um desses itens, consulte o [Início rápido do AKS][aks-quickstart] e [Instalar o Istio no AKS][istio-install].

## <a name="about-this-application-scenario"></a>Sobre esse cenário de aplicativo

O aplicativo de votação de exemplo do AKS fornece duas opções de votação (Gatos ou Cachorros) para os usuários. Há um componente de armazenamento que persiste o número de votos para cada opção. Além disso, há um componente de análise que fornece detalhes sobre os votos convertidos para cada opção.

Neste artigo, você começa implantando a versão *1.0* do aplicativo de votação e a versão *1.0* do componente de análise. O componente de análise fornece contagens simples para o número de votos. O aplicativo de votação e o componente de análise interagem com a versão *1.0* do componente de armazenamento, que tem suporte do Redis.

Atualize o componente de análise para a versão *1.1*, que fornece contagens e, agora, os totais e porcentagens.

Um subconjunto de usuários testa a versão *2.0* do aplicativo por meio de uma versão canário. Essa nova versão usa um componente de armazenamento que tem suporte de um banco de dados MySQL.

Quando você tiver certeza de que a versão *2.0* funciona conforme esperado no seu subconjunto de usuários, implante a versão *2.0* para todos os usuários.

## <a name="deploy-the-application"></a>Implantar o aplicativo

Vamos começar pela implantação do aplicativo no cluster do AKS (Serviço de Kubernetes do Azure). O diagrama a seguir mostra o que é executado no final desta seção - versão *1.0* de todos os componentes com solicitações de entrada atendidas por meio do gateway de entrada do Istio:

![Os componentes e roteamento do aplicativo de votação do AKS.](media/istio/components-and-routing-01.png)

Os artefatos necessários para acompanhar este artigo estão disponíveis no repositório do GitHub [Azure-Samples/aks-voting-app][github-azure-sample]. É possível baixar os artefatos ou clonar o repositório da seguinte maneira:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Altere para a seguinte pasta no repositório clonado / baixado e execute todas as etapas subsequentes nesta pasta:

```console
cd scenarios/intelligent-routing-with-istio
```

Primeiro, crie um namespace no cluster do AKS para o aplicativo de votação de exemplo do AKS nomeado *voting* como a seguir:

```console
kubectl create namespace voting
```

Rotule o namespace com `istio-injection=enabled`. Esse rótulo instrui o Istio para injetar automaticamente os istio-proxies como sidecars em todos os pods nesse namespace.

```console
kubectl label namespace voting istio-injection=enabled
```

Agora vamos criar os componentes para o aplicativo de votação do AKS. Crie esses componentes no namespace *voting* criado em uma etapa anterior.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

A saída de exemplo a seguir mostra que os recursos foram criados com êxito:

```
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> O Istio tem alguns requisitos específicos acerca de pods e serviços. Para obter mais informações, consulte o [Requisitos dos Istio para a documentação de Pods e Serviços][istio-requirements-pods-and-services].

Para ver os pods que foram criados, use o comando [kubectl get pods][kubectl-get] como a seguir:

```console
kubectl get pods -n voting
```

A saída de exemplo a seguir mostra que há três instâncias do pod *voting-app* e uma única instância de ambos os pods *voting-analytics* e *voting-storage*. Cada um dos pods tem dois contêineres. Um desses contêineres é o componente, sendo que o outro é o *istio-proxy*:

```
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-669f99dcc8-lzh7k   2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-bdmld         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-gcrng         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-strzc         2/2       Running   0          1m
voting-storage-1-0-7954799d96-5fv9r     2/2       Running   0          1m
```

Para obter informações sobre o pod, use o [kubectl describe pod][kubectl-describe]. Substitua o nome do pod pelo nome de um pod em seu próprio cluster do AKS da saída anterior:

```console
kubectl describe pod voting-app-1-0-6c65c4bdd4-bdmld --namespace voting
```

O contêiner *istio-proxy* foi injetado automaticamente pelo Istio para gerenciar o tráfego de rede de e para os componentes, como mostrado na saída de exemplo a seguir:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.0.4
[...]
```

Não é possível conectar ao aplicativo de votação até que você crie o [Gateway][istio-reference-gateway] e o [Serviço Virtual][istio-reference-virtualservice] do Istio. Esses recursos do Istio roteiam o tráfego do gateway de entrada padrão do Istio para nosso aplicativo.

> [!NOTE]
> Um *Gateway* é um componente na borda da malha de serviço que recebe o tráfego HTTP e TCP de entrada ou saída.
>
> Um *Serviço Virtual* define um conjunto de regras de roteamento para um ou mais serviços de destino.

Use o binário de cliente `istioctl` para implantar o gateway e o serviço virtual yaml. Assim como o comando `kubectl apply`, lembre-se de especificar o namespace no qual esses recursos são implantados.

```console
istioctl create -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Obtenha o endereço IP do gateway de entrada do Istio usando o seguinte comando:

```console
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

A saída de exemplo a seguir mostra os endereços IP do gateway de entrada:

```
52.187.250.239
```

Abra um navegador e cole o endereço IP. O aplicativo de votação de exemplo do AKS é exibido.

![O aplicativo de votação do AKS em execução no Istio habilitou o cluster do AKS.](media/istio/deploy-app-01.png)

As informações na parte inferior da tela mostram que o aplicativo usa a versão *1.0* do *voting-app* e a versão *1.0* (Redis) como a opção de armazenamento.

## <a name="update-the-application"></a>Atualizar o aplicativo

Nós vamos implantar uma nova versão do componente de análise. Essa nova versão *1.1* exibe totais e porcentagens, além da contagem para cada categoria.

O diagrama a seguir mostra o que é executado no final desta seção - somente a versão *1.1* do nosso componente *voting-analytics* tem um tráfego roteado do componente *voting-app*. Mesmo que versão *1.0* do nosso componente *voting-analytics* continue a ser executada e seja referenciada pelo serviço *voting-analytics*, os proxies do Istio não permitem o tráfego de e para ele.

![Os componentes e roteamento do aplicativo de votação do AKS.](media/istio/components-and-routing-02.png)

Vamos implantar a versão *1.1* do componente *voting-analytics*. Crie esse componente no namespace *voting*:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Abra novamente o aplicativo de votação de exemplo do AKS em um navegador, usando o endereço IP do gateway de entrada do Istio obtido na etapa anterior.

Seu navegador alterna entre os dois modos de exibição mostrados abaixo. Como você está usando um Serviço de [Kubernetes][kubernetes-service] para o componente *voting-analytics* com apenas um único seletor de rótulo (`app: voting-analytics`), o Kubernetes usa o comportamento padrão de round robin entre os pods que correspondem ao seletor. Nesse caso, isso é ambas as versões *1.0* e *1.1* dos pods *voting-analytics*.

![A versão 1.0 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/istio/deploy-app-01.png)

![A versão 1.1 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/istio/update-app-01.png)

Você pode visualizar a alternância entre as duas versões do componente *voting-analytics* como a seguir. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do Istio.

```console
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

A saída de exemplo a seguir mostra a parte relevante do site retornada como as alternâncias de site entre as versões:

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Bloquear o tráfego para a versão 1.1 do aplicativo

Agora vamos bloquear o tráfego somente para a versão *1.1* do componente *voting-analytics* e para a versão *1.0* do componente *voting-storage*. Em seguida, você define regras de roteamento para todos os outros componentes.

> * Um *Serviço Virtual* define um conjunto de regras de roteamento para um ou mais serviços de destino.
> * Uma *Regra de Destino* define políticas de tráfego e políticas específicas de versão.
> * Uma *Política* define quais métodos de autenticação podem ser aceitos em cargas de trabalho.

Você usa o binário de cliente `istioctl` para substituir a definição do serviço virtual no *voting-app* e adiciona [Regras de Destino][istio-reference-destinationrule] e [ serviços virtuais][istio-reference-virtualservice] para os outros componentes.

Você também adiciona uma [Política][istio-reference-policy] para o namespace *voting* para garantir que toda a comunicação entre serviços seja protegida usando TLS mútua e certificados do cliente.

Como há uma definição de serviço virtual existente para *voting-app* que você substitui, use o comando `istioctl replace` como a seguir:

```console
istioctl replace -f istio/step-2a-update-voting-app-virtualservice.yaml --namespace voting
```

A saída de exemplo a seguir mostra que o serviço virtual do Istio foi atualizado com êxito:

```
Updated config virtual-service/voting/voting-app to revision 141902
```

Em seguida, use o comando `istioctl create` para adicionar a nova política e também as novas regras de destino e serviços virtuais para todos os outros componentes.

* A política tem `peers.mtls.mode` definido como `STRICT` para garantir que a TLS mútua seja imposta entre os serviços dentro do namespace *voting*.
* Você também define `trafficPolicy.tls.mode` como `ISTIO_MUTUAL` em todas as nossas regras de destino. O Istio fornece serviços com identidades fortes e protege as comunicações entre serviços usando a TLS mútua e certificados do cliente que o Istio gerencia de forma transparente.

```console
istioctl create -f istio/step-2b-add-routing-for-all-components.yaml --namespace voting
```

A saída de exemplo a seguir mostra que a nova política, as regras de destino e os serviços virtuais foram criados com êxito:

```
Created config policy/voting/default to revision 142118
Created config destination-rule/voting/voting-app at revision 142119
Created config destination-rule/voting/voting-analytics at revision 142120
Created config virtual-service/voting/voting-analytics at revision 142121
Created config destination-rule/voting/voting-storage at revision 142122
Created config virtual-service/voting/voting-storage at revision 142123
```

Se você abrir o aplicativo de votação do AKS em um navegador outra vez, apenas a nova versão *1.1* do componente *voting-analytics* é usada pelo componente *voting-app*.

![A versão 1.1 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/istio/update-app-01.png)

É possível visualizar mais facilmente que agora nós estamos roteados apenas para a versão *1.1* do componente *voting-analytics* como a seguir. Lembre-se de usar o endereço IP do seu gateway de entrada do Istio.

É possível visualizar que agora você está roteado apenas para a versão *1.1* do componente *voting-analytics* como a seguir. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do Istio:

```azurecli-interactive
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

A saída de exemplo a seguir mostra a parte relevante do site retornada:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Confirme se Istio usa TLS mútua para proteger as comunicações entre cada um dos nossos serviços. Os comandos a seguir verificam as configurações de TLS para cada um dos serviços *voting-app*:

```console
istioctl authn tls-check voting-app.voting.svc.cluster.local
istioctl authn tls-check voting-analytics.voting.svc.cluster.local
istioctl authn tls-check voting-storage.voting.svc.cluster.local
```

Este exemplo de saída a seguir mostra que a TLS mútua é imposta para cada um dos serviços por meio da política e das regras de destino:

```
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Distribuir uma versão canário do aplicativo

Agora vamos implantar uma nova versão *2.0* dos componentes *voting-app*, *voting-analytics* e *voting-storage*. O novo componente *voting-storage* usa o MySQL em vez do Redis e os componentes *voting-app* e *voting-analytics* são atualizados para permitir que eles usem esse novo componente *voting-storage*.

O componente *voting-app* agora dá suporte à funcionalidade de sinalizador de recurso. Esse sinalizador de recursos permite que você teste a capacidade da versão canário do Istio para um subconjunto de usuários.

O diagrama a seguir mostra o que é executado no final desta seção.

* A versão *1.0* do componente *voting-app*, a versão *1.1* do componente *voting-analytics* e a versão  *1.0* do componente *voting-storage* são capazes de comunicar-se entre si.
* A versão *2.0* do componente *voting-app*, a versão *2.0* do componente *voting-analytics* e a versão  *2.0* do componente *voting-storage* são capazes de comunicar-se entre si.
* Versão *2.0* do componente *voting-app* só é acessível aos usuários que tenham um conjunto de sinalizadores de recurso específico. Essa alteração é gerenciada usando um sinalizador de recurso por meio de um cookie.

![Os componentes e roteamento do aplicativo de votação do AKS.](media/istio/components-and-routing-03.png)

Primeiro, atualize as regras de destino do Istio e os serviços virtuais para atender a esses novos componentes. Essas atualizações garantem que você não roteie tráfego incorretamente para os novos componentes e os usuários não obtenham acesso inesperado:

```console
istioctl replace -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

A saída de exemplo a seguir mostra que as regras de destino e os serviços virtuais foram atualizados com êxito:

```
Updated config destination-rule/voting/voting-app to revision 150930
Updated config virtual-service/voting/voting-app to revision 150931
Updated config destination-rule/voting/voting-analytics to revision 150937
Updated config virtual-service/voting/voting-analytics to revision 150939
Updated config destination-rule/voting/voting-storage to revision 150940
Updated config virtual-service/voting/voting-storage to revision 150941
```

Em seguida, vamos adicionar os objetos do Kubernetes para os componentes da nova versão *2.0*. Você também atualiza o serviço *voting-storage* para incluir a porta *3306* para MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

A saída de exemplo a seguir mostra que os objetos do Kubernetes foram criados ou atualizados com êxito:

```
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Aguarde até que todos os pods da versão *2.0* estejam em execução. Use o comando [kubectl get pods][kubectl-get] para exibir todos os pods no namespace *voting*:

```azurecli-interactive
kubectl get pods --namespace voting
```

Agora você deve ser capaz de alternar entre a versão *1.0* e a versão *2.0* (canário) do aplicativo de votação. A alternância de sinalizador de recurso na parte inferior da tela define um cookie. Esse cookie é usado pelo serviço virtual *voting-app* para rotear usuários para a nova versão *2.0*.

![A versão 1.0 do aplicativo de votação do AKS - sinalizador de recurso NÃO ESTÁ definida.](media/istio/canary-release-01.png)

![A versão 2.0 do aplicativo de votação do AKS - sinalizador de recurso ESTÁ definida.](media/istio/canary-release-02.png)

As contagens de voto são diferentes entre as versões do aplicativo. Essa diferença destaca que você está usando dois back-ends de armazenamento diferentes.

## <a name="finalize-the-rollout"></a>Finalizar a distribuição

Após testar com êxito a versão canário, atualize o serviço virtual *voting-app* para rotear todo o tráfego para a versão *2.0* do componente *voting-app*. Em seguida, todos os usuários podem ver a versão *2.0* do aplicativo, independentemente se o sinalizador de recurso está definido ou não:

![Os componentes e roteamento do aplicativo de votação do AKS.](media/istio/components-and-routing-04.png)

Atualize todas as regras de destino para remover as versões dos componentes que não quer mais ativas. Em seguida, atualize todos os serviços virtuais para parar de fazer referência a essas versões.

Como não há mais nenhum tráfego para qualquer uma das versões mais antigas dos componentes, agora é possível excluir com segurança todas as implantações para esses componentes.

![Os componentes e roteamento do aplicativo de votação do AKS.](media/istio/components-and-routing-05.png)

Você agora distribuiu com êxito uma nova versão do aplicativo de votação do AKS.

## <a name="next-steps"></a>Próximas etapas

Você pode explorar cenários adicionais usando o [Exemplo de Aplicação do Bookinfo do Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/spec-requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
