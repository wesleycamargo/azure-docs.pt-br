---
title: Implantar contêineres com Helm no Kubernetes no Azure
description: Use a ferramenta de empacotamento Helm para implantar contêineres em um cluster do AKS (Serviço de Kubernetes do Azure)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dd2deba25615373765dd3492d03c1ba547c8ba8c
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055127"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Instalar aplicativos com o Helm no AKS (Serviço de Kubernetes do Azure)

[Helm][helm] é uma ferramenta de empacotamento de software livre que ajuda a instalar e gerenciar o ciclo de vida de aplicativos Kubernetes. Semelhante a gerenciadores de pacotes do Linux, como *APT* e *Yum*, o Helm é usado para gerenciar gráficos Kubernetes, que são pacotes de recursos de Kubernetes pré-configurados.

Este artigo mostra como configurar e usar o Helm em um cluster Kubernetes no AKS.

## <a name="before-you-begin"></a>Antes de começar

As etapas detalhadas neste documento pressupõem que você tenha criado um cluster do AKS e estabelecido uma conexão `kubectl` com o cluster. Se você precisar desses itens, consulte o [Início rápido do AKS][aks-quickstart].

## <a name="install-helm-cli"></a>Instalar a CLI do Helm

A CLI do Helm é um cliente executado em seu sistema de desenvolvimento e permite que você inicie, pare e gerencie aplicativos com Helm.

Se você usa o Azure Cloud Shell, o a CLI do Helm já está instalada. Para instalar a CLI do Helm em um Mac, use `brew`. Para obter opções adicionais de instalação, veja [Instalação do Helm][helm-install-options].

```console
brew install kubernetes-helm
```

Saída:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.9.1.high_sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.9.1.high_sierra.bottle.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.9.1: 50 files, 66.2MB
```

## <a name="create-a-service-account"></a>Criar uma conta de serviço

Antes que possa implantar o Helm em um cluster habilitado para RBAC, você precisará de uma conta de serviço e da associação de funções para o serviço Tiller. Para obter mais informações sobre como proteger o Helm / cluster habilitado para o Tiller em um RBAC, consulte [Tiller, Namespaces e RBAC][tiller-rbac]. Se o cluster não for habilitado para RBAC, pule esta etapa.

Crie um arquivo chamado `helm-rbac.yaml` e o copie no YAML a seguir:

```yaml
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

Crie a conta de serviço e a associação de funções com o comando `kubectl create`:

```console
kubectl create -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Proteger o Tiller e o Helm

O cliente do Helm e o serviço do Tiller são autenticados e se comunicam entre si usando TLS/SSL. Esse método de autenticação ajuda a proteger o cluster Kubernetes e quais serviços podem ser implantados. Para aumentar a segurança, você pode gerar seus próprios certificados autoassinados. Cada usuário do Helm receberia seu próprio certificado do cliente e o Tiller seria inicializado no cluster Kubernetes com os certificados aplicados. Para obter mais informações, consulte [Usando TLS/SSL entre o Helm e o Tiller][helm-ssl].

Ao usar um cluster Kubernetes habilitado para RBAC, você pode controlar o nível de acesso que o Tiller tem ao cluster. Você pode definir o namespace do Kubernetes em que o Tiller é implantado e pode restringir em quais namespaces o Tiller pode implantar recursos. Essa abordagem permite criar instâncias do Tiller em namespaces diferentes, definir limites de implantação e definir o escopo dos usuários do cliente do Helm a determinados namespaces. Para obter mais informações, confira [Controles de acesso baseados em função do Helm][helm-rbac].

## <a name="configure-helm"></a>Configurar o Helm

Para implantar um Tiller básico em um cluster do AKS, use o comando [helm init][helm-init]. Se o cluster não está habilitado o RBAC, remova o `--service-account` argumento e o valor. Se você configurou TLS/SSL para o Tiller e o Helm, ignore esta etapa de inicialização básica e, em vez disso, forneça o `--tiller-tls-` necessário conforme mostrado no exemplo a seguir.

```console
helm init --service-account tiller
```

Se você configurou TLS/SSL entre o Helm e o Tiller, forneça os parâmetros de `--tiller-tls-` e os nomes de seus próprios certificados, conforme mostrado no exemplo a seguir:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller
```

## <a name="find-helm-charts"></a>Localizar gráficos Helm

Os gráficos do Helm são usados para implantar aplicativos em um cluster Kubernetes. Para procurar gráficos do Helm pré-criados, use o comando [helm search][helm-search]:

```console
helm search
```

A saída de exemplo condensada a seguir mostra alguns dos gráficos do Helm disponíveis para uso:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/acs-engine-autoscaler   2.2.0            2.1.1        Scales worker nodes within agent pools
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Para atualizar a lista de gráficos, use o comando [helm repo update][helm-repo-update]. O exemplo a seguir mostra uma atualização de repositório bem-sucedida:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Executar gráficos Helm

Para instalar gráficos com o Helm, use o comando [helm install][helm-install] e especifique o nome do gráfico a ser instalado. Para ver esse recurso em ação, vamos instalar uma implantação básica do Wordpress usando um gráfico do Helm. Se tiver configurado TLS/SSL, adicione o parâmetro `--tls` para usar seu certificado de cliente do Helm.

```console
helm install stable/wordpress
```

A saída do exemplo condensado a seguir mostra o status de implantação dos recursos do Kubernetes criados pelo gráfico do Helm:

```
$ helm install stable/wordpress

NAME:   wishful-mastiff
LAST DEPLOYED: Thu Jul 12 15:53:56 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/Deployment
NAME                       DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
wishful-mastiff-wordpress  1        1        1           0          1s

==> v1beta1/StatefulSet
NAME                     DESIRED  CURRENT  AGE
wishful-mastiff-mariadb  1        1        1s

==> v1/Pod(related)
NAME                                        READY  STATUS   RESTARTS  AGE
wishful-mastiff-wordpress-6f96f8fdf9-q84sz  0/1    Pending  0         1s
wishful-mastiff-mariadb-0                   0/1    Pending  0         1s

==> v1/Secret
NAME                       TYPE    DATA  AGE
wishful-mastiff-mariadb    Opaque  2     2s
wishful-mastiff-wordpress  Opaque  2     2s

==> v1/ConfigMap
NAME                           DATA  AGE
wishful-mastiff-mariadb        1     2s
wishful-mastiff-mariadb-tests  1     2s

==> v1/PersistentVolumeClaim
NAME                       STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
wishful-mastiff-wordpress  Pending  default  2s

==> v1/Service
NAME                       TYPE          CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
wishful-mastiff-mariadb    ClusterIP     10.1.116.54  <none>       3306/TCP                    2s
wishful-mastiff-wordpress  LoadBalancer  10.1.217.64  <pending>    80:31751/TCP,443:31264/TCP  2s
...
```

São necessários um ou dois minutos para que o endereço de *IP EXTERNO* do serviço do Wordpress seja populado e permita que você o acesse com um navegador da Web.

## <a name="list-helm-releases"></a>Listar versões do Helm

Para ver uma lista de versões instaladas no seu cluster, use o comando [helm list][helm-list]. O exemplo a seguir mostra a versão do Wordpress implantada na etapa anterior. Se tiver configurado TLS/SSL, adicione o parâmetro `--tls` para usar seu certificado de cliente do Helm.

```console
$ helm list

NAME             REVISION    UPDATED                     STATUS      CHART              NAMESPACE
wishful-mastiff  1           Thu Jul 12 15:53:56 2018    DEPLOYED    wordpress-2.1.3  default
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como gerenciar implantações de aplicativo do Kubernetes usando o Helm, consulte a documentação do Helm.

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
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
