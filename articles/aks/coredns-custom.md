---
title: Personalizar CoreDNS para o serviço de Kubernetes do Azure (AKS)
description: Saiba como personalizar CoreDNS para adicionar subdomínios ou estender os pontos de extremidade DNS personalizados usando o serviço de Kubernetes do Azure (AKS)
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 03/15/2019
ms.author: jnoller
ms.openlocfilehash: 9186c5ff7c6fbc68487a1ccff0fc1d2d1478df79
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58405836"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Personalizar CoreDNS com serviço de Kubernetes do Azure

Usa o serviço de Kubernetes do Azure (AKS) a [CoreDNS] [ coredns] projeto para gerenciamento de cluster de DNS e resolução com todos os *1.12.x* e clusters mais alto. Anteriormente, o projeto do kube-dns foi usado. Este projeto kube-dns agora foi preterido. Para obter mais informações sobre a personalização de CoreDNS e Kubernetes, consulte o [documentação oficial do upstream][corednsk8s].

Como o AKS é um serviço gerenciado, você não pode modificar a configuração principal para CoreDNS (um *CoreFile*). Em vez disso, você pode usar um Kubernetes *ConfigMap* para substituir as configurações padrão. Para ver o AKS CoreDNS ConfigMaps padrão, use o `kubectl get configmaps coredns -o yaml` comando.

Este artigo mostra como usar ConfigMaps para opções de personalização básica de CoreDNS no AKS.

> [!NOTE]
> `kube-dns` oferecido em diferentes [opções de personalização] [ kubednsblog] por meio de um mapa de configuração do Kubernetes. É CoreDNS **não** compatíveis com versões anteriores com o dns do kube. Todas as personalizações que você usou anteriormente devem ser atualizadas para uso com CoreDNS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar um cluster do AKS, consulte o início rápido do AKS [usando a CLI do Azure] [aks-quickstart-cli] ou [usando o portal do Azure] [aks-quickstart-portal].

## <a name="change-the-dns-ttl"></a>Alterar a TTL do DNS

Um cenário, você talvez queira configurar no CoreDNS é reduzir ou aumentar o tempo de configuração de vida (TTL) para o cache de nome DNS. Neste exemplo, vamos alterar o valor de TTL. Por padrão, esse valor é de 30 segundos. Para obter mais informações sobre as opções de cache DNS, consulte o [documentos oficiais do CoreDNS][dnscache].

No exemplo a seguir ConfigMap, observe o `name` valor. Por padrão, CoreDNS não oferece suporte a esse tipo de personalização conforme você modifica o CoreFile em si. AKS usa o *coredns personalizado* ConfigMap para incorporar suas próprias configurações e é carregado, após o CoreFile principal.

O exemplo a seguir informa ao CoreDNS que para todos os domínios (mostrado pela `.` na `.:53`), na porta 53 (a porta padrão DNS), definir o TTL de cache para 15 (`cache 15`). Crie um arquivo chamado `coredns-custom.json` e cole a configuração de exemplo a seguir:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    .:53 {
        cache 15  # this is our new cache value
    }
```

Crie o ConfigMap usando o [kubectl aplicar configmap] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML:

```console
kubectl apply configmap coredns-custom.json
```

Para verificar as personalizações tiverem sido aplicadas, use o [kubectl get configmaps] [ kubectl-get] e especifique seu *coredns personalizado* ConfigMap:

```
kubectl get configmaps coredns-custom -o yaml
```

Força agora CoreDNS para recarregar o ConfigMap. O [kubectl excluir pod] [ kubectl delete] comando não é destrutivo e não causa tempo de inatividade. O `kube-dns` pods são excluídos, e o Agendador Kubernetes, em seguida, recria-os. Esses novos pods contêm a alteração no valor TTL.

```console
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="rewrite-dns"></a>Reescreva o DNS

É um cenário que é necessário executar o sistema em funcionamento regravações de nome DNS. No exemplo a seguir, substitua `<domain to be written>` com seu próprio nome de domínio totalmente qualificado. Crie um arquivo chamado `coredns-custom.json` e cole a configuração de exemplo a seguir:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        proxy .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Como no exemplo anterior, crie o ConfigMap usando o [kubectl aplicar configmap] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML. Em seguida, forçar CoreDNS para recarregar o ConfigMap usando o [kubectl excluir pod] [ kubectl delete] o Agendador Kubernetes para recriá-los:

```console
kubectl apply configmap coredns-custom.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="custom-proxy-server"></a>Servidor proxy personalizado

Se você precisar especificar um servidor proxy para o tráfego de rede, você pode criar um ConfigMap para personalizar o DNS. No exemplo a seguir, atualize o `proxy` nome e endereço com os valores para seu próprio ambiente. Crie um arquivo chamado `coredns-custom.json` e cole a configuração de exemplo a seguir:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    .:53 {
        proxy foo.com 1.1.1.1
    }
```

Nos exemplos anteriores, crie o ConfigMap usando o [kubectl aplicar configmap] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML. Em seguida, forçar CoreDNS para recarregar o ConfigMap usando o [kubectl excluir pod] [ kubectl delete] o Agendador Kubernetes para recriá-los:

```console
kubectl apply configmap coredns-custom.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Usar domínios personalizados

Você talvez queira configurar domínios personalizados só podem ser resolvidos internamente. Por exemplo, você talvez queira resolver o domínio personalizado *puglife.local*, que não é um domínio de nível superior válido. Sem um ConfigMap de domínio personalizado, o cluster do AKS não é possível resolver o endereço.

No exemplo a seguir, atualize o endereço IP e domínio personalizado para direcionar o tráfego para com os valores para seu próprio ambiente. Crie um arquivo chamado `coredns-custom.json` e cole a configuração de exemplo a seguir:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        proxy . 192.11.0.1  # this is my test/dev DNS server
    }
```

Nos exemplos anteriores, crie o ConfigMap usando o [kubectl aplicar configmap] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML. Em seguida, forçar CoreDNS para recarregar o ConfigMap usando o [kubectl excluir pod] [ kubectl delete] o Agendador Kubernetes para recriá-los:

```console
kubectl apply configmap coredns-custom.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domínios de stub

CoreDNS também pode ser usado para configurar os domínios de stub. No exemplo a seguir, atualize os endereços IP e os domínios personalizados com os valores para seu próprio ambiente. Crie um arquivo chamado `coredns-custom.json` e cole a configuração de exemplo a seguir:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
    abc.com:53 {
        errors
        cache 30
        proxy . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        proxy . 2.3.4.5
    }
```

Nos exemplos anteriores, crie o ConfigMap usando o [kubectl aplicar configmap] [ kubectl-apply] de comando e especifique o nome do seu manifesto YAML. Em seguida, forçar CoreDNS para recarregar o ConfigMap usando o [kubectl excluir pod] [ kubectl delete] o Agendador Kubernetes para recriá-los:

```console
kubectl apply configmap coredns-custom.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou alguns cenários de exemplo para personalização CoreDNS. Para obter informações sobre o projeto CoreDNS, consulte [a página do projeto upstream de CoreDNS][coredns].

Para saber mais sobre conceitos de rede, consulte [rede conceitos para aplicativos no AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete

<!-- LINKS - external -->
[concepts-network]: concepts-network.md