---
title: Habilitar a criação automática de tópico no Apache Kafka - Azure HDInsight
description: Saiba como configurar o Apache Kafka no HDInsight para criar tópicos automaticamente. Você pode configurar o Kafka definindo auto.create.topics.enable como true por meio de Ambari ou durante a criação do cluster por meio de modelos do PowerShell ou do Gerenciador de Recursos.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/18/2018
ms.openlocfilehash: 65dd4b172c0913e11bb7f25909eb062e93a4a03a
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2018
ms.locfileid: "51015223"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Como configurar o Apache Kafka no HDInsight para criar tópicos automaticamente

Por padrão, o Kafka no HDInsight não habilita a criação automática de tópico. Você pode habilitar a criação automática de tópico para clusters existentes usando o Ambari. Você também pode habilitar a criação automática de tópico ao criar um novo cluster do Kafka usando um modelo do Azure Resource Manager.

## <a name="ambari-web-ui"></a>Interface do usuário da Ambari Web

Para habilitar a criação automática de tópico em um cluster existente por meio da interface do usuário da Web do Ambari, use as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com), selecione o cluster do Kafka.

2. Na __Visão geral do cluster__, selecione __Painel do Cluster__. 

    ![Imagem do portal com o painel do cluster selecionado](./media/apache-kafka-auto-create-topics/kafka-cluster-overview.png)

3. Em seguida, selecione __Painel do Cluster HDInsight__. Quando solicitado, autentique-se usando as credenciais de logon (administrador) do cluster.

    ![Imagem da entrada do painel do cluster HDInsight](./media/apache-kafka-auto-create-topics/hdinsight-cluster-dashboard.png)

3. Selecione o serviço Kafka na lista à esquerda da página.

    ![Lista de serviços](./media/apache-kafka-auto-create-topics/service-list.png)

4. Selecione Configurações no meio da página.

    ![Guia Configuração de serviço](./media/apache-kafka-auto-create-topics/service-config.png)

5. No campo Filtrar, digite um valor de `auto.create`. 

    ![Imagem do campo de filtro](./media/apache-kafka-auto-create-topics/filter.png)

    Isso filtrará a lista de propriedades e exibirá a configuração `auto.create.topics.enable`.

6. Altere o valor de `auto.create.topics.enable` para `true` e selecione Salvar. Adicionar uma observação e selecione Salvar novamente.

    ![Imagem da entrada auto.create.topics.enable](./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png)

7. Selecione o serviço Kafka, __Reiniciar__ e, em seguida, selecione __Reiniciar todos os afetados__. Quando solicitado, selecione __Confirmar reiniciar tudo__.

    ![Imagem da seleção de reiniciar](./media/apache-kafka-auto-create-topics/restart-all-affected.png)

> [!NOTE]
> Você também pode definir valores do Ambari por meio da API REST do Ambari. Isso é geralmente mais difícil, já que você precisa fazer várias chamadas REST para recuperar a configuração atual, modificá-la etc. Para obter mais informações, consulte o documento [Gerenciar clusters HDInsight usando a API REST do Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

Ao criar um cluster do Kafka usando um modelo do Azure Resource Manager, você pode definir diretamente `auto.create.topics.enable` adicionando-o em um `kafka-broker`. O snippet JSON a seguir demonstra como definir esse valor como `true`:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como habilitar a criação automática de tópico para Kafka no HDInsight. Para obter mais informações sobre como trabalhar com o Kafka, consulte os seguintes links:

* [Analisar logs do Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicar dados entre clusters de Kafka](apache-kafka-mirroring.md)
