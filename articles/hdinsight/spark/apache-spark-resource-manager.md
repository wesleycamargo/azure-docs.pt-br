---
title: Gerenciar recursos para cluster do Apache Spark no Azure HDInsight | Microsoft Docs
description: Saiba como gerenciar recursos para clusters do Spark no Azure HDInsight para melhorar o desempenho.
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 9da7d4e3-458e-4296-a628-77b14643f7e4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: jgao
ms.openlocfilehash: 914811812b7e01f7b58f92c85cb5a16580c45796
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Gerenciar os recursos para o cluster do Apache Spark no Azure HDInsight 

Saiba como acessar interfaces como a interface do usuário do Ambari, a interface do usuário do YARN e o servidor de histórico do Spark associados ao cluster do Spark e como ajustar a configuração do cluster para o desempenho ideal.

**Pré-requisitos:**

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="open-the-ambari-web-ui"></a>Abrir a interface do usuário da Web do Ambari

O Apache Ambari é usado para monitorar o cluster e fazer alterações de configuração. Para obter mais informações, consulte [Gerenciar clusters Hadoop no HDInsight usando o portal do Azure](../hdinsight-administer-use-portal-linux.md#open-the-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>Abrir o servidor de histórico do Spark

O Servidor de Histórico do Spark é a interface do usuário da Web para aplicativos Spark concluídos e em execução. Trata-se de uma extensão de IU da Web do Spark.

**Para abrir a interface do usuário da Web do Servidor de Histórico do Spark**

1. No [portal do Azure](https://portal.azure.com/), abra o cluster Spark. Para obter mais informações, consulte [Listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. Em **Links Rápidos**, clique em **Painel do Cluster** e, em seguida, em **Servidor de Histórico do Spark**

    ![Spark History Server](./media/apache-spark-resource-manager/launch-history-server.png "Spark History Server")

    Quando solicitado, insira as credenciais de administrador para o cluster Spark. Você também pode abrir o Servidor de Histórico do Spark navegando para a seguinte URL:

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    Substitua <ClusterName> pelo nome do cluster Spark.

A interface do usuário da Web do Servidor de Histórico do Spark tem esta aparência:

![Servidor de Histórico do HDInsight Spark](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>Abrir a interface do usuário do Yarn
É possível usar a interface do usuário do YARN para monitorar aplicativos que estão em execução no momento no cluster Spark.

1. No [portal do Azure](https://portal.azure.com/), abra o cluster Spark. Para obter mais informações, consulte [Listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).
2. Em **Links Rápidos**, clique em **Painel do Cluster** e, em seguida, em **YARN**.

    ![Iniciar Interface do usuário do YARN](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > Alternativamente, também é possível iniciar a interface do usuário do YARN na interface do usuário do Ambari. Para iniciar a interface do usuário do Ambari, clique em **Painel do Cluster** e, em seguida, em **Painel do Cluster HDInsight**. Na interface do usuário do Ambari, clique em **YARN**, em **Links Rápidos**, no Resource Manager ativo e, por fim, em **Interface do Usuário do Resource Manager**.
   >
   >

## <a name="optimize-clusters-for-spark-applications"></a>Otimizar clusters para aplicativos do Spark
Os três principais parâmetros que podem ser usados para a configuração do Spark dependendo dos requisitos de aplicativo são `spark.executor.instances`, `spark.executor.cores` e `spark.executor.memory`. Um Executor é um processo iniciado por um aplicativo Spark. Ele é executado no nó de trabalho e é responsável por realizar as tarefas do aplicativo. O número padrão de executores e os tamanhos do executor para cada cluster são calculados com base no número de nós de trabalho e no tamanho do nó de trabalho. Essas informações são armazenadas em `spark-defaults.conf` nos nós do cabeçalho do cluster.

Os três parâmetros de configuração podem ser definidos no nível de cluster (para todos os aplicativos que são executados no cluster) ou também podem ser especificados para cada aplicativo individualmente.

### <a name="change-the-parameters-using-ambari-ui"></a>Alterar os parâmetros usando a interface de usuário do Ambari
1. Na interface de usuário do Ambari, clique em **Spark**, em **Configurações** e expanda **spark-defaults personalizados**.

    ![Definir parâmetros usando o Ambari](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. Ter quatro aplicativos Spark em execução simultaneamente no cluster é o número de valores padrão ideal. Você pode alterar esses valores na interface do usuário, conforme mostrado na seguinte captura de tela:

    ![Definir parâmetros usando o Ambari](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. Clique em **Salvar** para salvar as alterações na configuração. Na parte superior da página, será solicitado que você reinicie todos os serviços afetados. Clique em **Reiniciar**.

    ![Reiniciar serviços](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Alterar os parâmetros para um aplicativo em execução no bloco de notas do Jupyter
Para aplicativos em execução no bloco de notas Jupyter, você pode usar a mágica `%%configure` para fazer as alterações na configuração. De modo ideal, você deve fazer tais alterações no início do aplicativo, antes de executar a primeira célula de código. Isso garante que a configuração seja aplicada à sessão Livy quando for criada. Se quiser alterar a configuração em uma fase posterior no aplicativo, você deverá usar o parâmetro `-f` . No entanto, ao fazer isso, todo o progresso do aplicativo é perdido.

O trecho a seguir mostra como alterar a configuração para um aplicativo em execução no Jupyter.

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

Os parâmetros devem ser passados como uma cadeia de caracteres JSON e devem estar na linha seguinte, logo após a mágica, conforme mostrado na coluna de exemplo.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Alterar os parâmetros de um aplicativo enviado usando spark-submit
O comando a seguir é um exemplo de como alterar os parâmetros de configuração para um aplicativo em lote que é enviado usando `spark-submit`.

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Alterar os parâmetros de um aplicativo enviado usando cURL
O comando a seguir é um exemplo de como alterar os parâmetros de configuração para um aplicativo em lote que é enviado usando o cURL.

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Alterar esses parâmetros em um Servidor Spark Thrift
O Servidor Thrift Spark fornece acesso JDBC/ODBC a um cluster Spark e é usado para atender às consultas SQL do Spark. Ferramentas como Power BI, Tableau, etc. usam o protocolo ODBC para se comunicar com o Servidor Thrift Spark e executar consultas SQL do Spark como um Aplicativo Spark. Quando um cluster Spark é criado, as duas instâncias do Servidor Thrift Spark são iniciadas, uma em cada nó de cabeçalho. Cada Servidor Thrift Spark é visto como um aplicativo Spark na interface de usuário do YARN.

O Servidor Thrift Spark usa a alocação dinâmica de executor e, portanto, `spark.executor.instances` não é usado. Em vez disso, o Servidor Thrift Spark usa `spark.dynamicAllocation.minExecutors` e `spark.dynamicAllocation.maxExecutors` para especificar a contagem do executor. Os parâmetros de configuração `spark.executor.cores` e `spark.executor.memory` são usados para modificar o tamanho do executor. Altere esses parâmetros, conforme mostrado nas seguintes etapas:

* Expanda a categoria **spark-thrift-sparkconf avançada** para atualizar os parâmetros `spark.dynamicAllocation.minExecutors`, `spark.dynamicAllocation.maxExecutors` e `spark.executor.memory`.

    ![Configurar o servidor Thrift Spark](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* Expanda a categoria **spark-thrift-sparkconf personalizada** para atualizar o parâmetro `spark.executor.cores`.

    ![Configurar o servidor Thrift Spark](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Alterar a memória do driver do servidor Spark Thrift
A memória do driver do Servidor Spark Thrift é configurada para 25% do tamanho da RAM do nó de cabeçalho, desde que o tamanho total da RAM do nó de cabeçalho seja superior a 14 GB. Use a interface do usuário do Ambari para alterar a configuração da memória do driver, conforme mostrado na seguinte captura de tela:

* Na interface de usuário do Ambari, clique em **Spark**, em **Configurações**, expanda **spark-env avançado** e forneça o valor para **spark_thrift_cmd_opts**.

    ![Configurar a RAM do servidor Thrift Spark](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>Recuperar recursos do cluster Spark
Devido à alocação dinâmica do Spark, os únicos recursos que são consumidos pelo servidor Thrift são os recursos para os dois mestres de aplicativo. Para recuperar esses recursos, é necessário interromper os serviços do Servidor Thrift em execução no cluster.

1. Na interface de usuário do Ambari, no painel esquerdo, clique em **Spark**.
2. Na página seguinte, clique em **Servidor Thrift Spark**.

    ![Reiniciar o servidor Thrift](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. Você deve ver os dois nós de cabeçalho nos quais o Servidor Thrift Spark está em execução. Clique em um dos nós de cabeçalho.

    ![Reiniciar o servidor Thrift](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. A próxima página lista todos os serviços em execução nesse nó de cabeçalho. Na lista, clique no botão suspenso próximo ao servidor Thrift Spark e clique em **Parar**.

    ![Reiniciar o servidor Thrift](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. Repita essas etapas no outro nó de cabeçalho.

## <a name="restart-the-jupyter-service"></a>Reiniciar o serviço Jupyter
Inicie a interface do usuário da Web do Ambari, conforme mostrado no início do artigo. No painel de navegação esquerdo, clique em **Jupyter**, em **Ações de Serviço** e em **Reiniciar Tudo**. Isso iniciará o serviço Jupyter em todos os nós de cabeçalho.

![Reinicie o Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "reinicie Jupyter")

## <a name="monitor-resources"></a>Monitorar recursos
Inicie a interface do usuário do Yarn, conforme mostrado no início do artigo. Na tabela de métricas de Cluster na parte superior da tela, verifique os valores de **memória usada** e **memória Total** colunas. Se os dois valores estiverem próximos, talvez não haja recursos suficientes para iniciar o próximo aplicativo. O mesmo se aplica para o **VCores usado** e **VCores Total** colunas. Além disso, no modo de exibição principal, se houver um aplicativo continuaram em **aceito** estado e não a transição para **executando** nem **falha** estado, isso também pode ser uma indicação de que ele não estiver obtendo recursos suficientes para iniciar.

![Limite de recursos](./media/apache-spark-resource-manager/resource-limit.png "limite de recurso")

## <a name="kill-running-applications"></a>Encerrar aplicativos em execução
1. Na interface do usuário Yarn, no painel esquerdo, clique em **executando**. Na lista de aplicativos em execução, determine o aplicativo a ser interrompida e clique no **ID**.

    ![Eliminar App1](./media/apache-spark-resource-manager/kill-app1.png "Eliminar App1")

2. Clique em **Eliminar Aplicativo** no canto superior direito e clique em **OK**.

    ![Eliminar App2](./media/apache-spark-resource-manager/kill-app2.png "Eliminar App2")

## <a name="see-also"></a>Consulte também
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Para analistas de dados

* [Spark com Machine Learning: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Análise da telemetria de dados do Application Insight usando o Spark no HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Usar o Caffe no Azure HDInsight Spark para aprendizado aprofundado distribuído](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Para desenvolvedores do Spark

* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](apache-spark-livy-rest-interface.md)
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
