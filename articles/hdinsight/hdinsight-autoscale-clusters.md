---
title: Dimensionar automaticamente os clusters do Azure HDInsight
description: Use o recurso de dimensionamento automático do HDInsight para dimensionar automaticamente os clusters
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: hrasheed
ms.openlocfilehash: 043c83e2039d87b1650ba17f770ce16a2ad2c13d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811155"
---
# <a name="automatically-scale-azure-hdinsight-clusters"></a>Dimensionar automaticamente os clusters do Azure HDInsight

O recurso de dimensionamento automático do Azure HDInsight dimensiona automaticamente o número de nós de trabalho em um cluster, para mais ou para menos, com base na carga dentro de um intervalo predefinido. Durante a criação de um novo cluster HDInsight, um número mínimo e um número máximo de nós de trabalho podem ser definidos. O dimensionamento automático então monitora os requisitos de recursos da carga da análise e dimensiona o número de nós de trabalho para mais ou para menos, conforme necessário. Não há nenhum custo adicional para esse recurso.

## <a name="getting-started"></a>Introdução

### <a name="create-cluster-with-azure-portal"></a>Criar cluster com o portal do Azure

> [!Note]
> Atualmente, o dimensionamento automático só tem suporte para clusters do Azure HDInsight, Hive, MapReduce e Spark versão 3.6.

Siga as etapas em [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md) e quando chegar na etapa 5, **Tamanho do Cluster**, selecione **Dimensionamento automático do nó de trabalho (versão prévia)** conforme mostrado abaixo. 

![Habilitar a opção de dimensionamento automático do nó de trabalho](./media/hdinsight-autoscale-clusters/worker-node-autoscale-option.png)

Ao marcar essa opção, você pode especificar:

* O número inicial de nós de trabalho
* O número mínimo de nós de trabalho
* O número máximo de nós de trabalho

O número inicial de nós de trabalho deve estar entre o mínimo e máximo, inclusive. Esse valor define o tamanho inicial do cluster quando ele é criado. O número mínimo de nós de trabalho deve ser maior que zero.

Após escolher o tipo de VM para cada tipo de nó, você poderá ver o intervalo de custo estimado para o cluster inteiro. Em seguida, você pode ajustar essas configurações de acordo com seu orçamento.

Sua assinatura tem uma cota de capacidade para cada região. O número total de núcleos de seus nós de cabeçalho combinado com o número máximo de nós de trabalho não pode exceder a cota de capacidade. No entanto, essa cota é um limite flexível; você sempre pode criar um tíquete de suporte para aumentá-lo facilmente.

> [!Note]
> Se você exceder o limite de cota de núcleos total, você receberá uma mensagem de erro dizendo “o nó máximo excedeu os núcleos disponíveis nessa região, escolha outra região ou entre em contato com o suporte para aumentar a cota”.

### <a name="create-cluster-with-an-resource-manager-template"></a>Criar cluster com um modelo do Resource Manager

Ao criar um cluster HDInsight com um modelo do Resource Manager, você precisará adicionar as seguintes configurações na seção “nó de trabalho” de “computeProfile”:

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "minInstanceCount": 2,
        "maxInstanceCount": 10
    },
    "hardwareProfile": {
        "vmSize": "Standard_D13_V2"
    },
    "osProfile": {
        "linuxOperatingSystemProfile": {
            "username": "[parameters('sshUserName')]",
            "password": "[parameters('sshPassword')]"
        }
    },
    "virtualNetworkProfile": null,
    "scriptActions": []
}
```

### <a name="enable-and-disabling-autoscale-for-a-running-cluster"></a>Habilitar e desabilitar o dimensionamento automático para um cluster em execução

Não há suporte para habilitar o dimensionamento automático para um cluster em execução durante a versão prévia privada. Ele deve ser habilitado durante a criação do cluster.

Não há suporte para desabilitar o dimensionamento automático ou modificar as configurações de dimensionamento automático para um cluster em execução na versão prévia privada. Você deve excluir o cluster e criar um novo para excluir ou modificar as configurações.

## <a name="monitoring"></a>Monitoramento

Você pode exibir o histórico de aumento ou redução de clusters como parte das métricas do cluster. Você pode listar todas as ações de dimensionamento do último dia, semana ou de um período de tempo maior.

## <a name="how-it-works"></a>Como ele funciona

### <a name="metrics-monitoring"></a>Monitoramento de métricas

O dimensionamento automático monitora o cluster continuamente e coleta as seguintes métricas:

1. **Total de CPU pendente**: o número total de núcleos necessários para iniciar a execução de todos os contêineres pendentes.
2. **Total de memória pendente**: o total de memória (em MB) necessária para iniciar a execução de todos os contêineres pendentes.
3. **Total de CPU livre**: a soma de todos os núcleos não utilizados em nós de trabalho ativos.
4. **Total de memória livre**: A soma de memória não usada (em MB) em nós de trabalho ativos.
5. **Memória usada por nó**: A carga em um nó de trabalho. Um nó de trabalho no qual 10 GB de memória são usados é considerado como estando sob uma carga maior que um nó de trabalho com 2 GB de memória usada.
6. **Número de aplicativos mestres por nó**: O número de contêineres de aplicativo mestre (AM) em execução em um nó de trabalho. Um nó de trabalho hospeda 2 contêineres de AM é considerado mais importante do que um nó de trabalho que hospeda 0 contêiner de AM.

As métricas acima são verificadas a cada 60 segundos. O dimensionamento automático vai verticalmente escalar ou reduzir as decisões com base nessas métricas.

### <a name="cluster-scale-up"></a>Escalar verticalmente o cluster

Quando as seguintes condições são detectadas, o dimensionamento automático emite uma solicitação para escalar verticalmente:

* O total de CPU pendente é maior que o total de CPU livre por mais de 1 minuto.
* O total de memória pendente é maior que o total de memória livre por mais de 1 minuto.

Calcularemos que os N novos nós de trabalho são necessários para atender aos requisitos de CPU e memória atuais e, em seguida, emitiremos uma solicitação para escalar verticalmente ao solicitar N novos nós de trabalho.

### <a name="cluster-scale-down"></a>Reduzir verticalmente o cluster

Quando as seguintes condições forem detectadas, o dimensionamento automático emite uma solicitação para reduzir verticalmente:

* O total de CPU pendente é menor que o total de CPU livre por mais de 10 minutos.
* O total de memória pendente é menor que o total de memória livre por mais de 10 minutos.

Com base no número de contêineres de AM por nó, bem como nos requisitos de CPU e memória atuais, o dimensionamento automático emitirá uma solicitação para remover N nós, especificando quais nós são potenciais candidatos para remoção. Por padrão, dois nós serão removidos em um ciclo.

## <a name="next-steps"></a>Próximas etapas

* Leia sobre as práticas recomendadas para dimensionar os clusters manualmente em [Práticas recomendadas de dimensionamento](hdinsight-scaling-best-practices.md)
