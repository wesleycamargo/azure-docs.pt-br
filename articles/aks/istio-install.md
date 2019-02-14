---
title: Instalar Istio no AKS (Serviço de Kubernetes do Azure)
description: Saiba como instalar e usar o Istio para criar uma malha de serviço em um cluster do AKS (Serviço de Kubernetes do Azure)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: f34d8c547738921374eaf5edcfcec4911423d9dc
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699204"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Instalar e usar o Istio no AKS (Serviço de Kubernetes do Azure)

[Istio][istio-github] é uma malha de serviço de software livre que fornece um conjunto de chaves de funcionalidade nos microsserviços em um cluster do Kubernetes. Esses recursos incluem gerenciamento de tráfego, segurança e identidade do serviço, imposição de política e observabilidade. Para obter mais informações sobre o Istio, consulte a documentação oficial [O que é Istio?][istio-docs-concepts].

Este artigo mostra como instalar o Istio. O Istio `istioctl` é instalado no seu computador cliente e, em seguida, os componentes do Istio são instalados em um cluster do Kubernetes no AKS. Estas instruções referenciam a versão do Istio *1.0.4*. É possível localizar versões adicionais do Istio em [GitHub - Versões do Istio][istio-github-releases].

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Baixar o Istio
> * Instalar o binário de cliente do Istio
> * Instalar os componentes de Kubernetes do Istio
> * validar a instalação

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste artigo presumem que você criou um cluster do AKS (Kubernetes 1.10 e superior, com RBAC habilitado) e estabeleceu uma conexão `kubectl` com o cluster. Se você precisar de ajuda com qualquer um desses itens, consulte o [Início rápido do AKS][aks-quickstart].

Para instalar o Istio, é necessário ter o [Helm][helm] versão *2.10.0* ou posterior instalado e configurado corretamente no cluster. Se precisar de ajuda para instalar o Helm, consulte [Diretrizes de instalação do Helm do AKS][helm-install]. Caso não tenha pelo menos a versão *2.10.0* do Helm instalada, faça upgrade ou consulte o guia [Istio - Instalação com Helm][istio-install-helm] para obter outras opções de instalação.

Este artigo separa as diretrizes de instalação do Istio em várias etapas discretas. Cada uma dessas etapas é descrita para você saber como instalar o Istio e compreender como o Istio funciona com o Kubernetes. O resultado final é o mesmo na estrutura das [diretrizes][istio-install-k8s-quickstart] de instalação do Istio oficial.

## <a name="download-istio"></a>Baixar o Istio

Primeiro, baixe e extraia a última versão do Istio. As etapas são um pouco diferentes para um shell bash no MacOS, Linux ou Subsistema do Windows para Linux e um shell do PowerShell. Escolha uma das seguintes etapas de instalação para o seu ambiente preferencial:

* [Bash ni MacOS, Linux ou Subsistema do Windows para Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

No MacOS, use `curl` para baixar a última versão do Istio e, em seguida, extrair com `tar`, conforme a seguir:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

No Linux ou no Subsistema do Windows para Linux, use `curl` para baixar a última versão do Istio e, em seguida, extrair com `tar`, conforme a seguir:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

### <a name="powershell"></a>PowerShell

No PowerShell, use [Invoke-WebRequest][Invoke-WebRequest] para baixar a última versão do Istio e, em seguida, extrair com [Expand-Archive][Expand-Archive], conforme a seguir:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.0.4"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

## <a name="install-the-istio-client-binary"></a>Instalar o binário de cliente do Istio

O binário de cliente `istioctl` executa no computador cliente e permite gerenciar políticas e regras de roteamento do Istio. Novamente, as etapas de instalação são um pouco diferentes entre os sistemas operacionais do cliente. Escolha uma das etapas de instalação a seguir para o seu ambiente preferencial.

> [!IMPORTANT]
> Execute todas as etapas restantes da pasta de nível superior da versão do Istio que você baixou e extraiu na seção anterior.

### <a name="macos"></a>MacOS

Para instalar o binário de cliente do Istio `istioctl` em um shell baseado em bash no MacOS, use os comandos a seguir. Esses comandos copiam o `istioctl` binário de cliente para o local do programa do usuário padrão no `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Se você quiser concluir a linha de comando do binário de cliente do Istio `istioctl`, configure-o conforme a seguir:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Agora, vá para a seção [Instalar os componentes de Kubernetes do Istio](#install-the-istio-kubernetes-components).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux ou Subsistema do Windows para Linux

Use os seguintes comandos para instalar o binário de cliente do Istio `istioctl` em um shell baseado em bash no Linux ou [Subsistema do Windows para Linux][install-wsl]. Esses comandos copiam o `istioctl` binário de cliente para o local do programa do usuário padrão no `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Se você quiser concluir a linha de comando do binário de cliente do Istio `istioctl`, configure-o conforme a seguir:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Agora, vá para a seção [Instalar os componentes de Kubernetes do Istio](#install-the-istio-kubernetes-components).

### <a name="windows"></a> Windows

Para instalar o binário de cliente do Istio `istioctl` em um shell baseado em Powershell no Windows, use os seguintes comandos. Esses comandos copiam o binário de cliente `istioctl` para um novo local do programa do usuário e o disponibiliza-o via `PATH`.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:/Program Files/Istio"
mv ./bin/istioctl.exe "C:/Program Files/Istio/"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Program Files\Istio\", "User")
```

## <a name="install-the-istio-kubernetes-components"></a>Instalar os componentes de Kubernetes do Istio

Para instalar os componentes do Istio no cluster do AKS, use o Helm. Instale os recursos do Istio no namespace `istio-system` e habilite as opções adicionais de segurança e monitoramento, conforme a seguir:

```azurecli
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set grafana.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

O pacote do Helm implanta um grande número de objetos. A implantação pode levar de 2 a 3 minutos para ser concluída, dependendo do ambiente de cluster.

## <a name="validate-the-installation"></a>validar a instalação

Para certificar-se de que você tenha uma implantação do Istio com êxito, vamos validar a instalação.

Primeiro, confirme se os serviços esperados foram criados. Use o comando [kubectl get svc][kubectl-get] para exibir os serviços em execução. Consulte o namespace *istio-system*, em que o Istio e os componentes de complemento foram instalados pelo pacote do Helm:

```console
kubectl get svc --namespace istio-system --output wide
```

A saída de exemplo a seguir mostra os serviços que devem estar em execução agora:

- serviços *istio-**
- serviços de rastreamento de complemento *jaeger-**, *tracing* e *zipkin*
- serviço de métricas de complemento *prometheus*
- serviço de painel de monitoramento e análise de complemento *grafana*
- serviço de painel de malha de serviço de complemento *kiali*

Se `istio-ingressgateway` mostrar um ip externo de `<pending>`, aguarde alguns minutos até que um endereço IP seja atribuído pela rede do Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                   AGE       SELECTOR
grafana                  ClusterIP      10.0.26.60     <none>           3000/TCP                                                                                                                  3m        app=grafana
istio-citadel            ClusterIP      10.0.88.87     <none>           8060/TCP,9093/TCP                                                                                                         3m        istio=citadel
istio-egressgateway      ClusterIP      10.0.115.115   <none>           80/TCP,443/TCP                                                                                                            3m        app=istio-egressgateway,istio=egressgateway
istio-galley             ClusterIP      10.0.104.183   <none>           443/TCP,9093/TCP                                                                                                          3m        istio=galley
istio-ingressgateway     LoadBalancer   10.0.12.216    52.187.250.239   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30469/TCP,8060:31999/TCP,853:31235/TCP,15030:32000/TCP,15031:30293/TCP   3m        app=istio-ingressgateway,istio=ingressgateway
istio-pilot              ClusterIP      10.0.38.134    <none>           15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     3m        istio=pilot
istio-policy             ClusterIP      10.0.253.81    <none>           9091/TCP,15004/TCP,9093/TCP                                                                                               3m        istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.181.186   <none>           443/TCP                                                                                                                   3m        istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.177.113   <none>           9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     3m        istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                3m        app=jaeger
jaeger-collector         ClusterIP      10.0.112.81    <none>           14267/TCP,14268/TCP                                                                                                       3m        app=jaeger
jaeger-query             ClusterIP      10.0.179.193   <none>           16686/TCP                                                                                                                 3m        app=jaeger
kiali                    ClusterIP      10.0.211.63    <none>           20001/TCP                                                                                                                 3m        app=kiali
prometheus               ClusterIP      10.0.70.113    <none>           9090/TCP                                                                                                                  3m        app=prometheus
tracing                  ClusterIP      10.0.139.121   <none>           80/TCP                                                                                                                    3m        app=jaeger
zipkin                   ClusterIP      10.0.60.155    <none>           9411/TCP                                                                                                                  3m        app=jaeger
```

Em seguida, confirme se os pods necessários foram criados. Use o comando [kubectl get pods][kubectl-get] e consulte novamente o namespace *istio-system*:

```console
kubectl get pods --namespace istio-system
```

A saída do exemplo a seguir mostra os pods em execução:

- pods *istio-**
- pod de métricas de complemento *prometheus-**
- pod de painel de monitoramento e análise de complemento *grafana*
- pode de painel de malha de serviço de complemento *kiali*

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-59b787b9b-cr6d7                  1/1       Running     0          6m
istio-citadel-78df8c67d9-9lfpf           1/1       Running     0          6m
istio-egressgateway-6b96cd7f5-k848h      1/1       Running     0          6m
istio-galley-58f566cb66-8mhbv            1/1       Running     0          6m
istio-ingressgateway-6cbbf596f6-6jz8g    1/1       Running     0          6m
istio-pilot-8449d555fc-sl6kp             2/2       Running     0          6m
istio-policy-6b99d88bc5-55s52            2/2       Running     0          6m
istio-sidecar-injector-b88dfb954-8m86s   1/1       Running     0          6m
istio-telemetry-675cb4cb9d-8s7wd         2/2       Running     0          6m
istio-tracing-7596597bd7-sbnt9           1/1       Running     0          6m
kiali-5fbd6ffb-4qcxw                     1/1       Running     0          6m
prometheus-76db5fddd5-2tkxs              1/1       Running     0          6m
```

Todos os pods mostram o status de `Running`. Se os pods não tiverem esses status, aguarde um minuto ou dois até que tenham. Se algum pods reportar um problema, use o comando [kubectl describe pod][kubectl-describe] para revisar a saída e o status.

## <a name="accessing-the-add-ons"></a>Acessar os complementos

Um número de complementos instalaram o Istio na configuração acima, que fornecem funcionalidade adicional. As interfaces de usuário para os complementos não são expostas publicamente por meio de um endereço IP externo. Para acessar as interfaces de usuário complementares, use o comando [kubectl port-forward][kubectl-port-forward]. Esse comando cria uma conexão segura entre uma porta local no computador cliente e o pod relevante no cluster do AKS.

### <a name="grafana"></a>Grafana

Os painéis de análise e monitoramento do Istio são fornecidos pelo [Grafana][grafana]. Encaminhe a porta local *3000* no computador cliente para a porta *3000* no pod que está executando o Grafana no cluster do AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

A seguinte saída de exemplo mostra o encaminhamento de porta sendo configurado para Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Agora, é possível acessar o Grafana na seguinte URL e no computador cliente - [http://localhost:3000](http://localhost:3000).

### <a name="prometheus"></a>Prometheus

O navegador de expressão para as métricas é fornecido por [Prometheus][prometheus]. Encaminhe a porta local *9090* no computador cliente para a porta *9090* no pod que está executando o Prometheus no cluster do AKS:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

A seguinte saída de exemplo mostra o encaminhamento de porta sendo configurado para Prometheus:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Agora, é possível acessar o navegador de expressão do Prometheus na seguinte URL no computador cliente - [http://localhost:9090](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

O rastreamento de interface do usuário é fornecido por [Jaeger][jaeger]. Encaminhe a porta local *16686* no computador cliente para a porta *16686* no pod que está executando o Jaeger no cluster do AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

A seguinte saída de exemplo mostra o encaminhamento de porta sendo configurado para Jaeger:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Agora, é possível acessar a interface do usuário de rastreamento Jaeger na seguinte URL no computador cliente - [http://localhost:16686](http://localhost:16686).

### <a name="kiali"></a>Kiali

Um painel de observabilidade de malha de serviço é fornecido por [Kiali][kiali]. Encaminhe a porta local *20001* no computador cliente para a porta *20001* no pod que está executando Kiali no cluster do AKS:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

A seguinte saída de exemplo mostra o encaminhamento de porta sendo configurado para Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Agora, é possível acessar o painel de observabilidade de malha do serviço do Kiali na seguinte URL no computador cliente - [http://localhost:20001](http://localhost:20001).

O nome de usuário e senha padrão para o painel do Kiali é *username:admin/password:admin*. Essas credenciais podem ser configuradas pelos valores do Helm *kiali.dashboard.username* e *kiali.dashboard.passphrase*.

## <a name="next-steps"></a>Próximas etapas

Para ver como é possível usar o Istio para fornecer roteamento inteligente entre várias versões do aplicativo e distribuir uma versão canário, consulte a documentação a seguir:

> [!div class="nextstepaction"]
> [Cenário de roteamento inteligente do Istio do AKS][istio-scenario-routing]

Para explorar mais opções de instalação e configuração para Istio, consulte os seguintes artigos oficiais do Istio:

- [Istio - Início rápido de instalação do Kubernetes][istio-install-k8s-quickstart]
- [Istio - Guia de instalação do Helm][istio-install-helm]
- [Istio - Opções de instalação do Helm][istio-install-helm-options]

Você também pode seguir cenários adicionais usando o [Bookinfo - Aplicação de exemplo do Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-k8s-quickstart]: https://istio.io/docs/setup/kubernetes/quick-start/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/helm-install/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
[kubernetes-crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
[Invoke-WebRequest]: /powershell/module/microsoft.powershell.utility/invoke-webrequest
[Expand-Archive]: /powershell/module/Microsoft.PowerShell.Archive/Expand-Archive
