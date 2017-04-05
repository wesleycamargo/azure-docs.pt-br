---
title: Acesso aos logs de aplicativo YARN do Hadoop no HDInsight baseado em Linux | Microsoft Docs
description: Saiba como acessar os logs de aplicativo de YARN em um cluster HDInsight (Hadoop) baseado em Linux usando a linha de comando e um navegador da Web.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 3ec08d20-4f19-4a8e-ac86-639c04d2f12e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 1d568bc6ab8f2801d575d726352f4c68e1f9277a
ms.lasthandoff: 03/25/2017


---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Acesso aos logs de aplicativo YARN no HDInsight baseado em Linux
Este documento explica como acessar os logs de aplicativos YARN (um novo recurso negociador) que termina em um cluster de Hadoop no HDInsight do Azure.

> [!IMPORTANT]
> As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Pré-requisitos
* Criar um cluster HDInsight baseado em Linux.
* Você deve [criar um túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md) antes de poder acessar a IU Web dos Logs do ResourceManager.

## <a name="YARNTimelineServer"></a>YARN Timeline Server
O [YARN Timeline Server](http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fornece informações genéricas sobre aplicativos concluídos e informações de aplicativo específicas da estrutura, por meio de duas interfaces diferentes. Especificamente:

* O armazenamento e a recuperação de informações do aplicativo genérico em clusters HDInsight estão habilitados na versão 3.1.1.374 ou superior.
* O componente de informações específicas do framework de aplicativo do servidor do cronograma não está atualmente disponível em clusters HDInsight.

As informações genéricas sobre aplicativos incluem os seguintes tipos de dados:

* A ID do aplicativo, um identificador exclusivo de um aplicativo
* O usuário que iniciou o aplicativo
* Informações sobre as tentativas feitas para concluir o aplicativo
* Os contêineres usados por uma determinada tentativa de aplicativo

## <a name="YARNAppsAndLogs"></a>Logs e aplicativos YARN

O YARN dá suporte a vários modelos de programação (inclusive MapReduce), por separar o gerenciamento de recursos do agendamento/monitoramento de aplicativos. Isso é feito por meio de um RM (*Gerenciador de Recursos*) global, NMs (*Gerenciadores de Nós*) por nó de trabalho e AMs (*Mestres de Aplicativos*) por aplicativo. O aplicativo AM negocia recursos (CPU, memória, disco e rede) para executar o aplicativo com o Gerenciador de recurso. O RM atua junto com os NMs para conceder esses recursos na forma de *contêineres*. O AM é responsável por controlar o andamento dos contêineres atribuídos pelo RM. Um aplicativo pode exigir um número de contêineres dependendo da natureza do aplicativo.

Cada aplicativo pode consistir em várias *tentativas do aplicativo*. Isso permite que um aplicativo repetir uma operação quando ocorre uma falha ou se houver uma perda de comunicação entre um AM e um RM. Cada tentativa é executado em um contêiner. De certa forma, um contêiner fornece o contexto para a unidade básica de trabalho executado por um aplicativo YARN. Todo trabalho é feito no contexto de um contêiner é executado no nó de trabalho único no qual o contêiner foi alocado. Confira [Conceitos de YARN][YARN-concepts] para referência adicional.

Os logs de aplicativos (e os logs de contêiner associado) são essenciais na depuração de aplicativos problemáticos do Hadoop. O YARN fornece uma ótima estrutura para coletar, agregar e armazenar logs de aplicativos com o recurso [Agregação de Logs][log-aggregation]. O recurso de agregação de logs permite acessar logs de aplicativos mais determinista. Ele agrega logs em todos os contêineres em um nó de trabalho e as armazena como um arquivo de log agregado por nó de trabalho. O log é armazenado no sistema de arquivos padrão após a conclusão de um aplicativo. O aplicativo deve usar centenas ou milhares de contêineres, mas logs para todos os contêineres executados em um nó único de trabalhado sempre serão agregados em um único arquivo. Isso resulta em um arquivo de log por nó de trabalho usado pelo seu aplicativo. A Agregação de Logs está habilitada por padrão nos clusters HDInsight (versão 3.0 ou superior) e os logs agregados se encontram no contêiner padrão de seu cluster no seguinte local:

    wasbs:///app-logs/<user>/logs/<applicationId>

Nesse local, *user* é o nome do usuário que iniciou o aplicativo e *applicationId* é o identificador exclusivo de um aplicativo, conforme atribuído pelo RM do YARN.

Os logs agregados não são diretamente legíveis, já que são gravados em um [TFile][T-file], [formato binário][binary-format] indexado pelo contêiner. Você deve usar as ferramentas dos logs ResourceManager do YARN ou CLI para ver esses logs como texto sem formatação para os aplicativos ou contêineres de interesse. 

## <a name="yarn-cli-tools"></a>Ferramentas CLI do YARN

Para usar as ferramentas CLI do YARN, você deve primeiro se conectar ao cluster HDInsight usando o SSH. Para saber mais, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

É possível exibir esses logs como texto sem formatação, executando um dos seguintes comandos:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Você deve especificar o &lt;applicationId>, &lt;usuário que-iniciou-o-aplicativo >, &lt;containerId> e informações do &lt;worker-node-address> ao executar esses comandos.

## <a name="yarn-resourcemanager-ui"></a>IU do ResourceManager YARN
A IU do ResourceManager YARN é executado no headnode do cluster e podem ser acessado por meio da IU da Web Ambari; No entanto, você deve primeiro [criar um túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md) antes de poder acessar a IU do ResourceManager.

Depois de criar um túnel SSH, use as etapas a seguir para exibir os logs YARN:

1. No seu navegador, navegue até https://CLUSTERNAME.azurehdinsight.net. Substitua CLUSTERNAME com o nome do cluster HDInsight.
2. Na lista de serviços à esquerda da página, selecione **YARN**.
   
    ![Serviço de yarn selecionado](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Na lista suspensa **Links rápidos**, selecione um dos nós principais do cluster e selecione **Log do ResourceManager**.
   
    ![Links rápidos do yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)
   
    Você verá uma lista de links para os logs do YARN.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

