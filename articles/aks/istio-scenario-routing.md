---
title: Roteamento inteligente e versões canário com Istio no AKS (Serviço de Kubernetes do Azure)
description: Saiba como usar o Istio para fornecer roteamento inteligente e implantar versões canário em um cluster do AKS (Serviço de Kubernetes do Azure)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: bd660a2b6ffb96478c3170cc7013ff22518b758f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702217"
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

> [!NOTE]
> Esse cenário foi testado em relação à versão Istio `1.1.3`.

As etapas detalhadas neste artigo pressupõem que você criou um cluster do AKS (Kubernetes `1.11` e acima, com o RBAC habilitado) e ter estabelecido um `kubectl` conexão com o cluster. Você também precisará Istio instalado no seu cluster.

Se você precisar de ajuda com qualquer um desses itens, consulte o [início rápido do AKS] [ aks-quickstart] e [Istio instalar no AKS] [ istio-install] diretrizes.

## <a name="about-this-application-scenario"></a>Sobre esse cenário de aplicativo

O aplicativo de votação de AKS de exemplo fornece duas opções de votação (**gatos** ou **cachorros**) para os usuários. Há um componente de armazenamento que persiste o número de votos para cada opção. Além disso, há um componente de análise que fornece detalhes sobre os votos convertidos para cada opção.

Nesse cenário de aplicativo, você começar a implantação da versão `1.0` do aplicativo de votação e a versão `1.0` do componente de análise. O componente de análise fornece contagens simples para o número de votos. O aplicativo de votação e o componente de análise de interagir com a versão `1.0` do componente de armazenamento, que é apoiada por Redis.

Atualizar o componente de análise para a versão `1.1`, que fornece as contagens e agora totais e porcentagens.

Um subconjunto de usuários de teste versão `2.0` do aplicativo por meio de uma versão canário. Essa nova versão usa um componente de armazenamento que tem suporte de um banco de dados MySQL.

Quando tiver certeza de que a versão `2.0` funciona conforme esperado no seu subconjunto de usuários, você distribuir versão `2.0` para todos os seus usuários.

## <a name="deploy-the-application"></a>Implantar o aplicativo

Vamos começar pela implantação do aplicativo no cluster do AKS (Serviço de Kubernetes do Azure). O diagrama a seguir mostra o que é executado no final desta seção - versão `1.0` de todos os componentes com solicitações de entrada atendidas por meio do gateway de entrada Istio:

![Os componentes e roteamento do aplicativo de votação do AKS.](media/istio/components-and-routing-01.png)

Os artefatos necessários para acompanhar este artigo estão disponíveis no repositório do GitHub [Azure-Samples/aks-voting-app][github-azure-sample]. É possível baixar os artefatos ou clonar o repositório da seguinte maneira:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Altere para a seguinte pasta no repositório clonado / baixado e execute todas as etapas subsequentes nesta pasta:

```console
cd scenarios/intelligent-routing-with-istio
```

Primeiro, crie um namespace em seu cluster do AKS para o aplicativo de votação AKS exemplo chamado `voting` da seguinte maneira:

```azurecli
kubectl create namespace voting
```

Rotule o namespace com `istio-injection=enabled`. Esse rótulo instrui o Istio para injetar automaticamente os istio-proxies como sidecars em todos os pods nesse namespace.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Agora vamos criar os componentes para o aplicativo de votação do AKS. Criar esses componentes no `voting` namespace criado na etapa anterior.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

A saída de exemplo a seguir mostra os recursos que está sendo criados:

```console
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

```azurecli
kubectl get pods -n voting
```

A saída de exemplo a seguir mostra que há três instâncias do `voting-app` pod e uma única instância de ambos os `voting-analytics` e `voting-storage` pods. Cada um dos pods tem dois contêineres. Um desses contêineres é o componente, e o outro é o `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

Para obter informações sobre o pod, use o [kubectl describe pod][kubectl-describe]. Substitua o nome do pod pelo nome de um pod em seu próprio cluster do AKS da saída anterior:

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

O `istio-proxy` contêiner automaticamente foi injetado por Istio para gerenciar o tráfego de rede para e de seus componentes, como mostrado na saída de exemplo a seguir:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

Não é possível conectar ao aplicativo de votação até que você crie o [Gateway][istio-reference-gateway] e o [Serviço Virtual][istio-reference-virtualservice] do Istio. Esses recursos do Istio roteiam o tráfego do gateway de entrada padrão do Istio para nosso aplicativo.

> [!NOTE]
> Um **Gateway** é um componente na borda da malha de serviço que recebe o tráfego HTTP e TCP de entrada ou saída.
> 
> Um **Serviço Virtual** define um conjunto de regras de roteamento para um ou mais serviços de destino.

Use o `kubectl apply` comando para implantar o Gateway e serviço Virtual yaml. Lembre-se de especificar o namespace que esses recursos são implantados em.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

A saída de exemplo a seguir mostra o novo Gateway e o serviço Virtual que está sendo criado:

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Obtenha o endereço IP do gateway de entrada do Istio usando o seguinte comando:

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

A saída de exemplo a seguir mostra os endereços IP do gateway de entrada:

```
20.188.211.19
```

Abra um navegador e cole o endereço IP. O aplicativo de votação de exemplo do AKS é exibido.

![O aplicativo de votação do AKS em execução no Istio habilitou o cluster do AKS.](media/istio/deploy-app-01.png)

As informações na parte inferior da tela mostram que o aplicativo usa a versão `1.0` dos `voting-app` e a versão `1.0` de `voting-storage` (Redis).

## <a name="update-the-application"></a>Atualizar o aplicativo

Nós vamos implantar uma nova versão do componente de análise. Essa nova versão `1.1` exibe totais e porcentagens, além de contagem para cada categoria.

O diagrama a seguir mostra o que será executado no final desta seção, somente a versão `1.1` de nosso `voting-analytics` tráfego roteado a partir do componente tem o `voting-app` componente. Mesmo que versão `1.0` de nosso `voting-analytics` componente continuará a ser executado e é referenciado pelo `voting-analytics` service, os proxies Istio não permitir o tráfego de e para ele.

![Os componentes e roteamento do aplicativo de votação do AKS.](media/istio/components-and-routing-02.png)

Vamos implantar a versão `1.1` do `voting-analytics` componente. Criar esse componente no `voting` namespace:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

A saída de exemplo a seguir mostra os recursos que está sendo criados:

```console
deployment.apps/voting-analytics-1-1 created
```

Abra novamente o aplicativo de votação de exemplo do AKS em um navegador, usando o endereço IP do gateway de entrada do Istio obtido na etapa anterior.

Seu navegador alterna entre os dois modos de exibição mostrados abaixo. Como você está usando um Kubernetes [Service] [ kubernetes-service] para o `voting-analytics` componente com apenas um seletor de rótulo único (`app: voting-analytics`), Kubernetes usa o comportamento padrão de round robin entre o pods que correspondem ao seletor. Nesse caso, ele é que ambas versões `1.0` e `1.1` de seu `voting-analytics` pods.

![A versão 1.0 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/istio/deploy-app-01.png)

![A versão 1.1 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/istio/update-app-01.png)

Você pode visualizar a alternância entre as duas versões do `voting-analytics` componente da seguinte maneira. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do Istio.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Powershell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
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

Agora vamos bloquear o tráfego para a versão só `1.1` do `voting-analytics` componente e a versão `1.0` da `voting-storage` componente. Em seguida, você define regras de roteamento para todos os outros componentes.

> * Um **Serviço Virtual** define um conjunto de regras de roteamento para um ou mais serviços de destino.
> * Uma **Regra de Destino** define políticas de tráfego e políticas específicas de versão.
> * Uma **Política** define quais métodos de autenticação podem ser aceitos em cargas de trabalho.

Use o `kubectl apply` comando para substituir a definição de serviço Virtual em seu `voting-app` e adicione [regras de destino] [ istio-reference-destinationrule] e [serviços virtuais] [ istio-reference-virtualservice] para os outros componentes. Você adicionará uma [diretiva] [ istio-reference-policy] para o `voting` namespace para garantir que todos se comunicar entre serviços é protegido usando certificados de cliente e de TLS mútua.

* A política tem `peers.mtls.mode` definido como `STRICT` para garantir que TLS mútua é imposta entre os serviços dentro do `voting` namespace.
* Também vamos definir a `trafficPolicy.tls.mode` para `ISTIO_MUTUAL` em todas as nossas regras de destino. O Istio fornece serviços com identidades fortes e protege as comunicações entre serviços usando a TLS mútua e certificados do cliente que o Istio gerencia de forma transparente.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

A saída de exemplo a seguir mostra a nova política, as regras de destino e serviços virtuais que estão sendo atualizadas/criadas:

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Se você abrir o aplicativo Voting AKS em um navegador mais uma vez, apenas a nova versão `1.1` do `voting-analytics` componente é usado pela `voting-app` componente.

![A versão 1.1 do componente de análise em execução no nosso aplicativo de votação do AKS.](media/istio/update-app-01.png)

Você pode visualizar o que você está agora apenas roteado para versão `1.1` de seu `voting-analytics` componente da seguinte maneira. Lembre-se de usar o endereço IP do seu próprio gateway de entrada do Istio:

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Powershell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

A saída de exemplo a seguir mostra a parte relevante do site retornada:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Agora vamos confirmar Istio é usando o TLS mútua para proteger as comunicações entre cada um dos nossos serviços. Para isso, usaremos o [verificação do tls authn] [ istioctl-authn-tls-check] comando o `istioctl` cliente binário, que utiliza o formato a seguir.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Este conjunto de comandos fornecem informações sobre o acesso a serviços especificados, de todos os compartimentos que estão em um namespace e corresponderem a um conjunto de rótulos:

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

Powershell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

Este exemplo de saída a seguir mostra que o TLS mútua é imposta para cada uma das nossas consultas acima. A saída também mostra a política e as regras de destino que impõe os TLS mútuos:

```console
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Distribuir uma versão canário do aplicativo

Agora vamos implantar uma nova versão `2.0` do `voting-app`, `voting-analytics`, e `voting-storage` componentes. O novo `voting-storage` componente usam o MySQL em vez de Redis e o `voting-app` e `voting-analytics` componentes são atualizados para permitir que eles usam esse novo `voting-storage` componente.

O `voting-app` componente agora dá suporte à funcionalidade de sinalizador de recurso. Esse sinalizador de recursos permite que você teste a capacidade da versão canário do Istio para um subconjunto de usuários.

O diagrama a seguir mostra o que você tem em execução no final desta seção.

* Versão `1.0` do `voting-app` componente, a versão `1.1` da `voting-analytics` componente e a versão `1.0` do `voting-storage` componente são capazes de se comunicar entre si.
* Versão `2.0` do `voting-app` componente, a versão `2.0` da `voting-analytics` componente e a versão `2.0` do `voting-storage` componente são capazes de se comunicar entre si.
* Versão `2.0` do `voting-app` componente só são acessíveis aos usuários que possuem um conjunto de sinalizadores de recurso específico. Essa alteração é gerenciada usando um sinalizador de recurso por meio de um cookie.

![Os componentes e roteamento do aplicativo de votação do AKS.](media/istio/components-and-routing-03.png)

Primeiro, atualize as regras de destino do Istio e os serviços virtuais para atender a esses novos componentes. Essas atualizações garantem que você não roteie tráfego incorretamente para os novos componentes e os usuários não obtenham acesso inesperado:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

A saída de exemplo a seguir mostra as regras de destino e os serviços virtuais que está sendo atualizado:

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Em seguida, vamos adicionar os objetos de Kubernetes para a nova versão `2.0` componentes. Você também atualizar o `voting-storage` serviço para incluir o `3306` porta para MySQL:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

A saída de exemplo a seguir mostra que os objetos do Kubernetes foram criados ou atualizados com êxito:

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Aguarde até que todos os a versão `2.0` pods estiverem em execução. Use o [kubectl get pods] [ kubectl-get] comando para exibir todos os pods no `voting` namespace:

```azurecli
kubectl get pods --namespace voting
```

Agora você deve ser capaz de alternar entre a versão `1.0` e a versão `2.0` (canário) do aplicativo de votação. A alternância de sinalizador de recurso na parte inferior da tela define um cookie. Esse cookie é usado pelas `voting-app` serviço Virtual para rotear usuários para a nova versão `2.0`.

![A versão 1.0 do aplicativo de votação do AKS - sinalizador de recurso NÃO ESTÁ definida.](media/istio/canary-release-01.png)

![A versão 2.0 do aplicativo de votação do AKS - sinalizador de recurso ESTÁ definida.](media/istio/canary-release-02.png)

As contagens de voto são diferentes entre as versões do aplicativo. Essa diferença destaca que você está usando dois back-ends de armazenamento diferentes.

## <a name="finalize-the-rollout"></a>Finalizar a distribuição

Depois que você testou com êxito a versão canário, atualize o `voting-app` serviço Virtual para rotear todo o tráfego para a versão `2.0` da `voting-app` componente. Todos os usuários, em seguida, consultem a versão `2.0` do aplicativo, independentemente se o sinalizador de recurso está definido ou não:

![Os componentes e roteamento do aplicativo de votação do AKS.](media/istio/components-and-routing-04.png)

Atualize todas as regras de destino para remover as versões dos componentes que não quer mais ativas. Em seguida, atualize todos os serviços virtuais para parar de fazer referência a essas versões.

Como não há mais nenhum tráfego para qualquer uma das versões mais antigas dos componentes, agora é possível excluir com segurança todas as implantações para esses componentes.

![Os componentes e roteamento do aplicativo de votação do AKS.](media/istio/components-and-routing-05.png)

Você agora distribuiu com êxito uma nova versão do aplicativo de votação do AKS.

## <a name="clean-up"></a>Limpar 

Você pode remover o aplicativo de votação do AKS são usados neste cenário de cluster do AKS, excluindo o `voting` namespace da seguinte maneira:

```azurecli
kubectl delete namespace voting
```

A saída de exemplo a seguir mostra que todos os componentes do aplicativo de votação AKS foram removidos do cluster do AKS.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Próximas etapas

Você pode explorar cenários adicionais usando o [Exemplo de Aplicação do Bookinfo do Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
