---
title: Implantar contêineres com Helm no Kubernetes no Azure
description: Use a ferramenta de empacotamento Helm para implantar contêineres em um cluster Kubernetes no AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 531e6d9368b2bf91c48fd41b1e9330879b0df49a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101215"
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>Usar Helm com o AKS (Serviço de Kubernetes do Azure)

[Helm][helm] é uma ferramenta de empacotamento de software livre que ajuda a instalar e gerenciar o ciclo de vida de aplicativos Kubernetes. Semelhante a gerenciadores de pacotes do Linux, como *APT* e *Yum*, o Helm é usado para gerenciar gráficos Kubernetes, que são pacotes de recursos de Kubernetes pré-configurados.

Este documento explica como configurar e usar o Helm em um cluster Kubernetes no AKS.

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste documento pressupõem que você tenha criado um cluster AKS e estabelecido uma conexão kubectl com o cluster. Se você precisar desses itens, consulte o [Início rápido do AKS][aks-quickstart].

## <a name="install-helm-cli"></a>Instalar a CLI do Helm

A CLI do Helm é um cliente executado em seu sistema de desenvolvimento e permite que você inicie, pare e gerencie aplicativos com Helm.

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

## <a name="create-service-account"></a>Crie conta de serviço

Antes de configurar o Helm em um RBAC cluster habilitado, você precisa de uma conta de serviço e a função de associação para o serviço Tiller. Para obter mais informações sobre como proteger o Helm / cluster habilitado para o Tiller em um RBAC, consulte [Tiller, Namespaces e RBAC][tiller-rbac]. Note, se o cluster não for habilitado em RBAC, pule esta etapa.

Crie um arquivo chamado `helm-rbac.yaml` e copie no YAML a seguir.

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Criar a conta de serviço e a associação de função com o comando `kubectl create`.

```
kubectl create -f helm-rbac.yaml
```

Ao usar um cluster com o RBAC habilitado, você tem opções no nível de acesso de Tiller no cluster. Ver [Helm: controles de acesso baseado em função][helm-rbac] para obter mais informações sobre opções de configuração.

## <a name="configure-helm"></a>Configurar o Helm

Agora, instale tiller usando o comando [helm init][helm-init]. Se o cluster não está habilitado o RBAC, remova o `--service-account` argumento e o valor.

```
helm init --service-account tiller
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

Para implantar o Wordpress usando um gráfico do Helm, use o comando [helm install][helm-install].

```azurecli-interactive
helm install stable/wordpress
```

A saída é semelhante à seguinte, mas inclui informações adicionais, como instruções sobre como usar a implantação do Kubernetes.

```
NAME:   bilging-ibex
LAST DEPLOYED: Tue Jun  5 14:31:49 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                     READY  STATUS   RESTARTS  AGE
bilging-ibex-mariadb-7557b5474-dmdxn     0/1    Pending  0         1s
bilging-ibex-wordpress-7494c545fb-tskhz  0/1    Pending  0         1s

==> v1/Secret
NAME                    TYPE    DATA  AGE
bilging-ibex-mariadb    Opaque  2     1s
bilging-ibex-wordpress  Opaque  2     1s

==> v1/ConfigMap
NAME                        DATA  AGE
bilging-ibex-mariadb        1     1s
bilging-ibex-mariadb-tests  1     1s

==> v1/PersistentVolumeClaim
NAME                    STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
bilging-ibex-mariadb    Pending  default  1s
bilging-ibex-wordpress  Pending  default  1s

==> v1/Service
NAME                    TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)                     AGE
bilging-ibex-mariadb    ClusterIP     10.0.76.164   <none>       3306/TCP                    1s
bilging-ibex-wordpress  LoadBalancer  10.0.215.250  <pending>    80:30934/TCP,443:31134/TCP  1s

==> v1beta1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
bilging-ibex-mariadb    1        1        1           0          1s
bilging-ibex-wordpress  1        1        1           0          1s
...
```

## <a name="list-helm-releases"></a>Listar versões do Helm

Para ver uma lista de versões instaladas no seu cluster, use o comando [helm list][helm-list].

```azurecli-interactive
helm list
```

Saída:

```
NAME            REVISION    UPDATED                     STATUS      CHART           NAMESPACE
bilging-ibex    1           Tue Jun  5 14:31:49 2018    DEPLOYED    wordpress-1.0.9 default
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como gerenciar gráficos de Kubernetes, veja a documentação do Helm.

> [!div class="nextstepaction"]
> [Documentação do Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
