---
title: "Implantar contêineres com Helm no Kubernetes no Azure"
description: "Use a ferramenta de empacotamento Helm para implantar contêineres em um cluster Kubernetes no AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f0fa4c667074a1acda70371251172acf9ff119e5
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="use-helm-with-azure-container-service-aks"></a>Usar o Helm com o Serviço de Contêiner do Azure (AKS)

[Helm][helm] é uma ferramenta de empacotamento de software livre que ajuda a instalar e gerenciar o ciclo de vida de aplicativos Kubernetes. Semelhante a gerenciadores de pacotes do Linux, como *APT* e *Yum*, o Helm é usado para gerenciar gráficos Kubernetes, que são pacotes de recursos de Kubernetes pré-configurados.

Este documento explica como configurar e usar o Helm em um cluster Kubernetes no AKS.

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste documento pressupõem que você tenha criado um cluster AKS e estabelecido uma conexão kubectl com o cluster. Se você precisar desses itens, consulte o [Início rápido do AKS][aks-quickstart].

## <a name="install-helm-cli"></a>Instalar a CLI do Helm

A CLI do Helm é um cliente executado em seu sistema de desenvolvimento e permite que você inicie, pare e gerencie aplicativos com gráficos Helm.

Se você estiver usando o Azure CloudShell, a CLI do Helm já estará instalada. Para instalar a CLI do Helm em um Mac, use `brew`. Para obter opções adicionais de instalação, veja [Instalação do Helm][helm-install-options].

```console
brew install kubernetes-helm
```

Saída:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="configure-helm"></a>Configurar o Helm

O comando [helm init][helm-init] é usado para instalar componentes do Helm em um cluster Kubernetes e fazer as configurações no cliente. Execute o seguinte comando para instalar o Helm no cluster do AKS e configurar o cliente do Helm.

```azurecli-interactive
helm init
```

Saída:

```
$HELM_HOME has been configured at /home/user/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.
Happy Helming!
```

## <a name="find-helm-charts"></a>Localizar gráficos Helm

Os gráficos do Helm são usados para implantar aplicativos em um cluster Kubernetes. Para procurar gráficos do Helm pré-criados, use o comando [helm search][helm-search].

```azurecli-interactive
helm search
```

A saída será semelhante à seguinte, mas com muitos mais gráficos.

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

Para atualizar a lista de gráficos, use o comando [helm repo update][helm-repo-update].

```azurecli-interactive
helm repo update
```

Saída:

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Executar gráficos Helm

Para implantar um controlador de ingresso NGINX, use o comando [helm install][helm-install].

```azurecli-interactive
helm install stable/nginx-ingress
```

A saída é semelhante à seguinte, mas inclui informações adicionais, como instruções sobre como usar a implantação do Kubernetes.

```
NAME:   tufted-ocelot
LAST DEPLOYED: Thu Oct  5 00:48:04 2017
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                    DATA  AGE
tufted-ocelot-nginx-ingress-controller  1     5s

==> v1/Service
NAME                                         CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
tufted-ocelot-nginx-ingress-controller       10.0.140.10  <pending>    80:30486/TCP,443:31358/TCP  5s
tufted-ocelot-nginx-ingress-default-backend  10.0.34.132  <none>       80/TCP                      5s

==> v1beta1/Deployment
NAME                                         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
tufted-ocelot-nginx-ingress-controller       1        1        1           0          5s
tufted-ocelot-nginx-ingress-default-backend  1        1        1           1          5s
...
```

Para saber mais sobre como usar um controlador de ingresso NGINX com Kubernetes, veja [Controlador de ingresso NGINX][nginx-ingress].

## <a name="list-helm-charts"></a>Listar gráficos Helm

Para ver uma lista de gráficos instalado no seu cluster, use o comando [helm list][helm-list].

```azurecli-interactive
helm list
```

Saída:

```
NAME            REVISION    UPDATED                     STATUS      CHART               NAMESPACE
bilging-ant     1           Thu Oct  5 00:11:11 2017    DEPLOYED    nginx-ingress-0.8.7 default
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como gerenciar gráficos de Kubernetes, veja a documentação do Helm.

> [!div class="nextstepaction"]
> [Documentação do Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://github.com/kubernetes/helm/blob/master/docs/index.md
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md