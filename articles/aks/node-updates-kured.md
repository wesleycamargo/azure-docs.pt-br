---
title: Atualizar e reinicializar nós com kured no AKS (Serviço de Kubernetes do Azure)
description: Saiba como atualizar nós e reiniciá-los automaticamente com kured no AKS (Serviço de Kubernetes do Azure)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 11/06/2018
ms.author: iainfou
ms.openlocfilehash: 0bcc49df6540b73b8feb5bb1ec4312e680572797
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617804"
---
# <a name="apply-security-and-kernel-updates-to-nodes-in-azure-kubernetes-service-aks"></a>Aplicar atualizações de kernel e segurança aos nós no AKS (Serviço de Kubernetes do Azure)

Para proteger os clusters, as atualizações de segurança são aplicadas automaticamente aos nós no AKS. Essas atualizações incluem correções de segurança do SO ou atualizações de kernel. Algumas dessas atualizações exigem uma reinicialização do nó para concluir o processo. O AKS não reinicializa automaticamente os nós para concluir o processo de atualização.

Este artigo mostra como usar o [kured (KUbernetes REboot Daemon)][kured] de software livre para inspecionar os nós que exigem uma reinicialização e, em seguida, manipular automaticamente o reagendamento de pods em execução e o processo de reinicialização do nó .

> [!NOTE]
> `Kured` é um projeto de software livre da Weaveworks. O suporte para esse projeto no AKS é fornecido com base no melhor esforço. Suporte adicional pode ser encontrado no canal do Slack #weave-community,

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar de um cluster do AKS, confira o guia de início rápido do AKS [Usando a CLI do Azure][aks-quickstart-cli] ou [Usando o portal do Azure][aks-quickstart-portal].

A CLI do Azure versão 2.0.49 ou posterior também precisa estar instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="understand-the-aks-node-update-experience"></a>Compreender a experiência de atualização do nó AKS

Em um cluster do AKS, os nós do Kubernetes executam como VMs (máquinas virtuais) do Azure. Essas VMs baseadas em Linux usam uma imagem do Ubuntu, com o SO configurado para verificar automaticamente as atualizações todas as noites. Se atualizações de kernel ou de segurança estiverem disponíveis, elas serão baixadas e instaladas automaticamente.

![Atualização de nó do AKS e processo de reinicialização com kured](media/node-updates-kured/node-reboot-process.png)

Algumas atualizações de segurança, como atualizações de kernel, exigem uma reinicialização do nó para finalizar o processo. Um nó que requer uma reinicialização cria um arquivo nomeado */var/run/reboot-required*. Esse processo de reinicialização não ocorre automaticamente.

Você pode usar seus próprios fluxos de trabalho e processos para manipular reinicializações de nós ou usar `kured` para orquestrar o processo. Com `kured`, é implantado um [DaemonSet][DaemonSet] que executa um pod em cada nó no cluster. Esses pods no DaemonSet inspecionam a existência do arquivo */var/run/reboot-required* e, em seguida, iniciam um processo para reinicializar os nós.

### <a name="node-upgrades"></a>Upgrades de nós

Há um processo adicional no AKS que permite fazer *upgrade* de um cluster. Um upgrade é basicamente passar para uma versão mais nova do Kubernetes, e não apenas aplicar atualizações de segurança do nó. Um upgrade de AKS executa as seguintes ações:

* Um novo nó é implantado com as atualizações de segurança mais recentes e a versão do Kubernetes aplicada.
* Um nó antigo é isolado e drenado.
* Os pods estão agendados no novo nó.
* O nó antigo é excluído.

Não será possível permanecer na mesma versão do Kubernetes durante um evento de upgrade. Você deverá especificar uma versão mais nova do Kubernetes. Para fazer upgrade para a última versão do Kubernetes, você pode [fazer upgrade do seu cluster do AKS][aks-upgrade].

## <a name="deploy-kured-in-an-aks-cluster"></a>Implantar em um cluster do AKS

Para implantar o DaemonSet `kured`, aplique o seguinte exemplo de manifesto YAML da sua página de projeto do GitHub. Esse manifesto cria uma função e uma função de cluster, associações e uma conta de serviço e, em seguida, implanta o DaemonSet usando a versão 1.1.0 de `kured` que dá suporte aos clusters do AKS 1.9 ou posterior.

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.1.0/kured-1.1.0.yaml
```

Também é possível configurar parâmetros adicionais para `kured`, como integração com Prometheus ou Slack. Para obter mais informações sobre parâmetros de configuração adicionais, consulte os [documentos de instalação do kured][kured-install].

## <a name="update-cluster-nodes"></a>Atualizar nós do cluster

Por padrão, os nós do AKS procuram atualizações todas as noites. Se não quiser esperar, poderá executar manualmente uma atualização para verificar se `kured` executa corretamente. Primeiro, siga as etapas para [SSH para um dos nós do AKS][aks-ssh]. Quando houver uma conexão SSH com o nó, verifique as atualizações e aplique-as conforme a seguir:

```console
sudo apt-get update && sudo apt-get upgrade -y
```

Se forem aplicadas atualizações que exigem uma reinicialização do nó, um arquivo será gravado em */var/run/reboot-required*. `Kured` verifica por nós que exigem uma reinicialização a cada 60 minutos por padrão.

## <a name="monitor-and-review-reboot-process"></a>Monitorar e revisar o processo de reinicialização

Quando uma das réplicas no DaemonSet detectou que uma reinicialização do nó é necessária, um bloqueio foi colocado no nó por meio da API do Kubernetes. Esse bloqueio impedirá que pods adicionais sejam agendados no nó. O bloqueio também indica que apenas um nó deverá ser reinicializado de cada vez. Com o nó isolado, os pods em execução serão drenados do nó e o nó será reinicializado.

É possível monitorar o status dos nós usando o comando [kubectl get nodes][kubectl-get-nodes]. A seguinte saída de exemplo mostra um nó com um status *SchedulingDisabled*, na medida em que o nó se prepara para o processo de reinicialização:

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-79590246-2   Ready,SchedulingDisabled   agent     1h        v1.9.11
```

Quando o processo de atualização estiver concluído, você poderá exibir o status dos nós usando o comando [kubectl get nodes][kubectl-get-nodes] com o parâmetro `--output wide`. Essa saída adicional permite ver uma diferença em *KERNEL-VERSION* dos nós subjacentes, conforme mostrado na seguinte saída de exemplo. O nó *aks-nodepool1-79590246-2* foi atualizado em uma etapa anterior e mostra a versão do kernel *4.15.0-1025-azure*. O nó *aks-nodepool1-79590246-1* que não foi atualizado mostra a versão do kernel *4.15.0-1023-azure*.

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.11   10.240.0.6    <none>        Ubuntu 16.04.5 LTS   4.15.0-1023-azure   docker://1.13.1
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.11   10.240.0.4    <none>        Ubuntu 16.04.5 LTS   4.15.0-1025-azure   docker://1.13.1
```

## <a name="next-steps"></a>Próximas etapas

Este artigo detalhou como usar `kured` para reinicializar os nós automaticamente como parte do processo de atualização de segurança. Para fazer upgrade para a última versão do Kubernetes, você pode [fazer upgrade do seu cluster do AKS][aks-upgrade].

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
