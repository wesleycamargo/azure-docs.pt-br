---
title: Migração do ACS (Serviço de Contêiner do Azure) para o AKS (Serviço de Kubernetes do Azure)
description: Migração do ACS (Serviço de Contêiner do Azure) para o AKS (Serviço de Kubernetes do Azure)
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: 910c96988ec0a8b8aa7b6ac8ce287c4fdc59e177
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467554"
---
# <a name="migrating-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migração do ACS (Serviço de Contêiner do Azure) para o AKS (Serviço de Kubernetes do Azure)

O objetivo deste documento é ajudar a planejar e executar uma migração bem-sucedida entre o ACS (Serviço de Contêiner do Azure com Kubernetes) e o AKS (Serviço de Kubernetes do Azure). Este guia detalha as diferenças entre o ACS e o AKS, fornece uma visão geral do processo de migração e pode ajudar a tomar decisões importantes.

## <a name="differences-between-acs-and-aks"></a>Diferenças entre o ACS e o AKS

O ACS e o AKS diferem em algumas áreas importantes que afetam a migração. Você precisa examinar e planejar como resolver as diferenças a seguir antes de qualquer migração.

* Nós do AKS usam [Discos Gerenciados](../virtual-machines/windows/managed-disks-overview.md)
    * Discos não gerenciados precisarão ser convertidos para poderem ser anexados a nós do AKS
    * Objetos `StorageClass` personalizados para os discos do Azure precisarão ser alterados de `unmanaged` para `managed`
    * Qualquer `PersistentVolumes` precisará usar `kind: Managed`
* Atualmente, o AKS é compatível com apenas um pool de agentes
* Nós do Windows Server estão em [versão prévia privada](https://azure.microsoft.com/blog/kubernetes-on-azure/) no momento
* Verifique a lista de [regiões com suporte](https://docs.microsoft.com/azure/aks/container-service-quotas) para o AKS
* O AKS é um serviço gerenciado com um plano de controle de Kubernetes hospedado. Pode ser necessário modificar seus aplicativos se você tiver modificado a configuração dos ACS mestres anteriormente

### <a name="differences-between-kubernetes-versions"></a>Diferenças entre as versões do Kubernetes

Se você estiver migrando para uma versão mais recente do Kubernetes (ex: 1.7.x para 1.9.x), há algumas alterações para a API k8s que exigirão sua atenção.

* [Migrar um ThirdPartyResource para CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Alterações de API de cargas de trabalho nas versões 1.8 e 1.9](https://kubernetes.io/docs/reference/workloads-18-19/).

## <a name="migration-considerations"></a>Considerações sobre a migração

### <a name="agent-pools"></a>Pools de agentes

Embora o AKS gerencie o plano de controle do Kubernetes, ainda é preciso definir o tamanho e o número de nós que você deseja incluir no novo cluster. Supondo que você deseja ter um mapeamento de 1:1 de ACS para o AKS, convém capturar as informações de nó de ACS existentes. Você usará esses dados ao criar o novo cluster do AKS.

Exemplo:

| NOME | Contagem | Tamanho da VM | Sistema operacional |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 |  Windows |

Como as máquinas virtuais serão implantadas na sua assinatura durante a migração, você precisa verificar se suas cotas e limites são suficientes para esses recursos. Saiba mais examinando [Assinatura do Azure e limites de serviço](https://docs.microsoft.com/azure/azure-subscription-service-limits). Para verificar suas cotas atuais, acesse a [folha de assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) no Portal do Azure, selecione sua assinatura e, em seguida, selecione `Usage + quotas`.

### <a name="networking"></a>Rede

Para aplicativos complexos, geralmente a migração ocorre ao longo do tempo em vez de uma só vez. Isso significa que os ambientes antigos e novos podem precisar se comunicar pela rede. Aplicativos que antes podiam usar serviços `ClusterIP` para se comunicar podem precisar ser expostos como o tipo `LoadBalancer` e devidamente protegidos.

Para concluir a migração, convém apontar os clientes para os novos serviços em execução no AKS. A maneira recomendada para redirecionar o tráfego é atualizar o DNS para apontar para o Load Balancer que se encontra na frente do cluster do AKS.

### <a name="stateless-applications"></a>Aplicativos sem monitoração de estado

A migração de aplicativos sem monitoração de estado é o caso mais simples. Aplique as definições YAML ao novo cluster, valide se tudo está funcionando conforme o esperado e redirecione o tráfego para ativar o novo cluster.

### <a name="stateful-applications"></a>Aplicativos com estado

Aplicativos com estado requerem um planejamento cuidadoso evitar a perda de dados ou tempo de inatividade inesperado.

#### <a name="highly-available-applications"></a>Aplicativos altamente disponíveis

Alguns aplicativos com estado podem ser implantados em uma configuração de alta disponibilidade e podem copiar dados entre as réplicas. Se essa for a sua implantação atual, é possível criar um novo membro no novo cluster AKS e migrar com impacto mínimo para os chamadores downstream. As etapas de migração para este cenário geralmente são:

1. Criar uma nova réplica secundária no AKS
2. Aguardar a replicação dos dados
3. Fazer failover para transformar a réplica secundária em nova primária
4. Direcionar o tráfego para o cluster do AKS

#### <a name="migrating-persistent-volumes"></a>Migrar volumes persistentes

Há vários fatores a considerar se você estiver migrando Volumes Persistentes existentes para o AKS. Em geral, as etapas envolvidas são:

1. (Opcional) Fechar gravações para novas sessões para o aplicativo (requer tempo de inatividade)
2. Discos de instantâneo
3. Criar novos discos gerenciados de instantâneos
4. Criar volumes persistentes no AKS
5. Atualizar as especificações de Pod para [usar volumes existentes](https://docs.microsoft.com/azure/aks/azure-disk-volume) em vez de PersistentVolumeClaims (provisionamento estático)
6. Implantar o aplicativo no AKS
7. Validar
8. Direcionar o tráfego para o cluster do AKS

> **Importante**: Se você optar por não gravações de fechamento para novas sessões, você precisará replicar os dados para a nova implantação, como você perderá os dados que foram gravados desde o instantâneo do disco

Existem ferramentas de software livre que podem ajudar a criar Discos Gerenciados e migrar volumes entre clusters de Kubernetes.

* [noelbundick/azure-cli-disk-extension](https://github.com/noelbundick/azure-cli-disk-copy-extension) – copiar e converter os discos entre os grupos de recursos e regiões do Azure
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli) – enumerar volumes Kubernetes do ACS e migrá-los para um cluster do AKS

#### <a name="azure-files"></a>Arquivos do Azure

Ao contrário dos discos, os Arquivos do Azure podem ser montados em vários hosts simultaneamente. Nem Azure nem o Kubernetes impedem a criação de um Pod no cluster do AKS que ainda está sendo usado pelo seu cluster do ACS. Para evitar perda de dados e um comportamento inesperado, garanta que ambos os clusters não estão gravando nos mesmos arquivos ao mesmo tempo.

Se seu aplicativo pode hospedar várias réplicas que apontam para o mesmo compartilhamento de arquivos, siga as etapas de migração sem monitoração de estado e implante suas definições YAML no novo cluster.

Caso contrário, uma abordagem de migração possível envolve as seguintes etapas:

1. Implantar seu aplicativo no AKS com uma contagem de réplica de 0
2. Dimensionar o aplicativo no ACS para 0 (requer tempo de inatividade)
3. Dimensionar o aplicativo no AKS para 1
4. Validar
5. Direcionar o tráfego para o cluster do AKS

Caso você deseje começar com um compartilhamento vazio, faça uma cópia dos dados de origem e será possível usar os comandos [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) para migrar seus dados.

### <a name="deployment-strategy"></a>Estratégia de implantação

O método recomendado é usar seu pipeline de CI/CD existente para implantar uma configuração válida para o AKS. Clone suas tarefas de implantação existentes e verifique se o `kubeconfig` aponta para o novo cluster do AKS.

Caso isso não seja possível, será preciso exportar a definição de recurso do ACS e aplicá-la ao AKS. É possível usar `kubectl` para exportar objetos.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Também há várias ferramentas de software livre que podem ajudar, dependendo das suas necessidades:

* [heptio/ark](https://github.com/heptio/ark) – requer k8s 1.7
* [yaron2/azure-kube-cli](https://github.com/yaron2/azure-kube-cli)
* [mhausenblas/reshifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Etapas da migração

### <a name="1-create-an-aks-cluster"></a>1. Criar um cluster AKS

Siga a documentação para [criar um cluster do AKS](https://docs.microsoft.com/azure/aks/create-cluster) por meio do Portal do Azure, a CLI do Azure ou o modelo do Resource Manager.

> Você pode encontrar modelos do Azure Resource Manager de exemplo para AKS no repositório [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) no GitHub

### <a name="2-modify-applications"></a>2. Modificar aplicativos

Faça as modificações necessárias nas suas definições YAML. Exemplo: substituir `apps/v1beta1` com `apps/v1` por `Deployments`

### <a name="3-optional-migrate-volumes"></a>3. (Opcional) Migrar volumes

Migre volumes do seu cluster do ACS para o cluster do AKS. Mais detalhes podem ser encontrados na seção [Migrar volumes persistentes](#migrating-persistent-volumes).

### <a name="4-deploy-applications"></a>4. Implantar aplicativos

Use o sistema de CI/CD para implantar aplicativos no AKS ou use kubectl para aplicar as definições YAML.

### <a name="5-validate"></a>5. Validar

Valide se os seus aplicativos estão funcionando conforme o esperado e se todos os dados migrados foram copiados.

### <a name="6-redirect-traffic"></a>6. Tráfego de redirecionamento

Atualize o DNS para apontar os clientes para sua implantação do AKS.

### <a name="7-post-migration-tasks"></a>7. Tarefas pós-migração

Se você migrou os volumes e optou por não fechar as gravações para novas sessões, será preciso copiar esses dados para o novo cluster.
