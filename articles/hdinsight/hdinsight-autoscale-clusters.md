---
title: Dimensionar automaticamente os clusters de HDInsight do Azure (visualização)
description: Use o recurso de dimensionamento automático do HDInsight para dimensionar automaticamente os clusters
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: hrasheed
ms.openlocfilehash: 11828b3b056519d0ebe3233f078c6b3f6fc2ea1c
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59997488"
---
# <a name="automatically-scale-azure-hdinsight-clusters-preview"></a>Dimensionar automaticamente os clusters de HDInsight do Azure (visualização)

>[!Important]
>O recurso HDInsight Autoscale está atualmente em visualização. Envie um email para hdiautoscalepm@microsoft.com ter dimensionamento automático habilitado para sua assinatura.

O recurso de dimensionamento automático do Azure HDInsight dimensiona automaticamente o número de nós de trabalho em um cluster, para mais ou para menos, com base na carga dentro de um intervalo predefinido. Durante a criação de um novo cluster HDInsight, um número mínimo e um número máximo de nós de trabalho podem ser definidos. O dimensionamento automático então monitora os requisitos de recursos da carga da análise e dimensiona o número de nós de trabalho para mais ou para menos, conforme necessário. Não há nenhum custo adicional para esse recurso.

## <a name="getting-started"></a>Introdução

### <a name="create-a-cluster-with-the-azure-portal"></a>Criar um cluster com o portal do Azure

> [!Note]
> Atualmente, o dimensionamento automático só tem suporte para clusters do Azure HDInsight, Hive, MapReduce e Spark versão 3.6.

Para habilitar o recurso de dimensionamento automático, faça o seguinte como parte do processo de criação de cluster normal:

1. Selecione **Personalizado (tamanho, configurações, aplicativos)** em vez de **Criação rápida**.
2. Na etapa 5 (**Tamanho do cluster**) de **Personalizado**, marque a caixa de seleção **Dimensionamento automático de nó de trabalho**.
3. Insira os valores desejados para as seguintes propriedades:  

    * **Número inicial de nós de trabalho**.  
    * O número **mínimo** de nós de trabalho.  
    * O número **máximo** de nós de trabalho.  

![Habilitar a opção de dimensionamento automático do nó de trabalho](./media/hdinsight-autoscale-clusters/usingAutoscale.png)

O número inicial de nós de trabalho deve estar entre o mínimo e máximo, inclusive. Esse valor define o tamanho inicial do cluster quando ele é criado. O número mínimo de nós de trabalho deve ser maior que zero.

Após escolher o tipo de VM para cada tipo de nó, você poderá ver o intervalo de custo estimado para o cluster inteiro. Em seguida, você pode ajustar essas configurações de acordo com seu orçamento.

Sua assinatura tem uma cota de capacidade para cada região. O número total de núcleos de seus nós de cabeçalho combinado com o número máximo de nós de trabalho não pode exceder a cota de capacidade. No entanto, essa cota é um limite flexível; você sempre pode criar um tíquete de suporte para aumentá-lo facilmente.

> [!Note]  
> Se você exceder o limite de cota de núcleos total, você receberá uma mensagem de erro dizendo “o nó máximo excedeu os núcleos disponíveis nessa região, escolha outra região ou entre em contato com o suporte para aumentar a cota”.

Para obter mais informações sobre a criação de clusters HDInsight usando o portal do Azure, confira [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Criar um cluster com um modelo do Resource Manager

Para criar um cluster HDInsight com um modelo do Azure Resource Manager, adicione um nó `autoscale` à seção `computeProfile` > `workernode` com as propriedades `minInstanceCount` e `maxInstanceCount`, conforme mostrado no snippet de JSON abaixo.

```json
{                            
    "name": "workernode",
    "targetInstanceCount": 4,
    "autoscale": {
        "capacity": {
            "minInstanceCount": 2,
            "maxInstanceCount": 10
        }        
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

Para obter mais informações sobre como criar cluster com modelos do Resource Manager, confira [Criar clusters do Apache Hadoop no HDInsight usando modelos do Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).  

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Habilitar e desabilitar o dimensionamento automático para um cluster em execução

Você só pode habilitar ou desabilitar o dimensionamento automático para novos clusters HDInsight.

## <a name="monitoring"></a>Monitoramento

Você pode exibir o histórico de ampliação e redução de escala do cluster como parte das métricas de cluster. Você também pode listar todas as ações de dimensionamento do último dia, semana ou de um período de tempo maior.

## <a name="how-it-works"></a>Como ele funciona

### <a name="metrics-monitoring"></a>Monitoramento de métricas

O dimensionamento automático monitora o cluster continuamente e coleta as seguintes métricas:

1. **Total de CPU pendente**: o número total de núcleos necessários para iniciar a execução de todos os contêineres pendentes.
2. **Total de memória pendente**: o total de memória (em MB) necessária para iniciar a execução de todos os contêineres pendentes.
3. **Total de CPU livre**: a soma de todos os núcleos não utilizados em nós de trabalho ativos.
4. **Total de memória livre**: A soma de memória não usada (em MB) em nós de trabalho ativos.
5. **Memória usada por nó**: A carga em um nó de trabalho. Um nó de trabalho no qual 10 GB de memória são usados é considerado como estando sob uma carga maior que um nó de trabalho com 2 GB de memória usada.
6. **Número de aplicativos mestres por nó**: O número de contêineres de aplicativo mestre (AM) em execução em um nó de trabalho. Um nó de trabalho que está hospedando dois contêineres de AM, é considerado mais importante do que um nó de trabalho que está hospedando o zero contêineres AM.

As métricas acima são verificadas a cada 60 segundos. Dimensionamento automático tomará decisões de ampliação e redução com base nessas métricas.

### <a name="cluster-scale-up"></a>Expansão de cluster

Quando as condições a seguir são detectadas, o dimensionamento automático emitirá uma solicitação de aumento de escala:

* Total pendente da CPU é maior que o total de CPU livre para mais de 3 minutos.
* Total de memória pendentes é maior que o total de memória livre para mais de 3 minutos.

Podemos calcular a que um determinado número de novos nós de trabalho é necessários para atender aos requisitos de CPU e memória atuais e, em seguida, emitir uma solicitação de aumento de escala que adicionará esse número de novos nós de trabalho.

### <a name="cluster-scale-down"></a>Redução de escala do cluster

Quando as condições a seguir são detectadas, o dimensionamento automático emitirá uma solicitação de redução de escala:

* O total de CPU pendente é menor que o total de CPU livre por mais de 10 minutos.
* O total de memória pendente é menor que o total de memória livre por mais de 10 minutos.

Com base no número de contêineres de AM por nó e os requisitos de memória e CPU atual, a AutoEscala emitirá uma solicitação para remover um determinado número de nós, especifica quais nós são candidatos em potencial para remoção. A escala para baixo irá disparar o encerramento de nós e depois que os nós forem encerrados completamente, eles serão removidos.

## <a name="next-steps"></a>Próximas etapas

* Leia sobre as práticas recomendadas para dimensionar os clusters manualmente em [Práticas recomendadas de dimensionamento](hdinsight-scaling-best-practices.md)
