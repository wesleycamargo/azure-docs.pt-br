---
title: Solucionar problemas com o cluster Apache Spark no Azure HDInsight
description: Saiba mais sobre problemas relacionados aos clusters Apache Spark no Azure HDInsight e como solucioná-los.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: 92baa28393100abe3d7694920e5ee327966db927
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048301"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Problemas conhecidos do cluster do Apache Spark no HDInsight

Este documento mantém o registro de todos os problemas conhecidos para a visualização pública do Spark no HDInsight.  

## <a name="livy-leaks-interactive-session"></a>Sessão interativa de vazamentos do Livy
Quando o Livy é reiniciado com uma sessão interativa (do Ambari ou devido a reinicialização da máquina virtual de nó principal 0) ainda ativa, uma sessão de trabalho interativo é vazado. Como resultado, novos trabalhos podem estar paralisados no estado Aceito.

**Atenuação:**

Use o procedimento a seguir para contornar o problema:

1. SSH no nó principal. Para obter informações, consulte [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Execute o seguinte comando para encontrar as IDs de aplicativo dos trabalhos interativos iniciados por Livy. 
   
        yarn application –list
   
    Os nomes de trabalho padrão serão Livy se os trabalhos foram iniciados com uma sessão interativa Livy sem nenhum nome explícito especificado. Para a sessão Livy iniciada por Jupyter Notebook, o nome do trabalho inicia com remotesparkmagics_*. 
3. Execute o seguinte comando para eliminar esses trabalhos. 
   
        yarn application –kill <Application ID>

A execução de novos trabalhos tem início. 

## <a name="spark-history-server-not-started"></a>Servidor de histórico Spark não iniciado
O servidor de histórico Spark não é iniciado automaticamente depois de um cluster ser criado.  

**Atenuação:** 

Inicie manualmente o servidor de histórico do Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problema de permissão no diretório de log do Spark
O hdiuser obtém o seguinte erro ao enviar um trabalho usando o envio spark:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```
E nenhum driver log é gravado. 

**Atenuação:**

1. Adicione hdiuser ao grupo Hadoop. 
2. Forneça 777 permissões em /var/log/spark após a criação do cluster. 
3. Atualize o local do log spark usando o Ambari para ser um diretório com 777 permissões.  
4. Execute spark-submit como sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Não há suporte para o conector Spark Phoenix

Os clusters de HDInsight Spark não dão suporte para o conector Spark Phoenix.

**Atenuação:**

Use o conector Spark-HBase em vez disso. Para obter instruções, confira [Como usar o conector Spark-HBase](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problemas relacionados aos notebooks do Jupyter
A seguir estão alguns problemas conhecidos relacionados aos notebooks do Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Notebooks com caracteres não ASCII nos nomes de arquivos
Não use caracteres que não sejam ASCII em nomes de arquivos de notebook Jupyter. Se você tentar carregar um arquivo por meio da UI Jupyter, que tem um nome de arquivo não ASCII, ele falha sem qualquer mensagem de erro. Jupyter não permite que você carregue o arquivo, mas ele não gerará um erro visível.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Erro ao carregar notebooks de tamanhos maiores
Você pode encontrar um erro **`Error loading notebook`** ao carregar notebooks com um tamanho maior.  

**Atenuação:**

Se você visualizar esse erro, não significa que seus dados estão corrompidos ou foram perdidos.  Os notebooks ainda estão no disco, em `/var/lib/jupyter`, e você pode usar o SSH no cluster para acessá-los. Para obter informações, consulte [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Depois de se conectar ao cluster usando o SSH, é possível copiar os blocos de anotações do cluster para o computador local (usando SCP ou WinSCP) como um backup para impedir a perda de quaisquer dados importantes no notebook. Você pode aplicar SSH no túnel no nó de cabeçalho na porta 8001 para acessar o Jupyter sem passar pelo gateway.  A partir daí, você pode limpar a saída do bloco de anotações e salvá-lo novamente para minimizar o tamanho dele.

Para evitar que esse erro aconteça no futuro, você deve seguir algumas práticas recomendadas:

* É importante manter um tamanho pequeno de bloco de anotações. Qualquer saída de trabalho no Spark que é enviada de volta ao Jupyter é mantida no bloco de anotações.  De modo geral, uma melhor prática com o Jupyter é evitar a execução de `.collect()` em RDDs ou em quadros de dados grandes. Em vez disso, se você quiser inspecionar o conteúdo de um RDD, considere a execução de `.take()` ou `.sample()` para que sua saída não fique muito grande.
* Além disso, quando você salvar um bloco de anotações, limpe todas as células de saída para reduzir o tamanho.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>A inicialização inicial do notebook demora mais do que o esperado
A primeira instrução de código no notebook Jupyter usando a mágica do Spark pode demorar mais de um minuto.  

**Explicação:**

Isso acontece quando a primeira célula de código é executada. Em segundo plano, isso inicia a configuração de sessão e os contextos de Spark, SQL e Hive são definidos. Depois desses contextos são definidos, a primeira instrução é executada e isso dá a impressão de que a instrução levou muito tempo para ser concluída.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Tempo limite do notebook do Jupyter atingido na criação da sessão
Quando o cluster Spark está sem recursos, os kernels Spark e PySpark no notebook do Jupyter atingirão o tempo limite ao tentar criar a sessão. 

**Atenuações:** 

1. Libere alguns recursos do cluster Spark:
   
   * Pare outros notebooks do Spark no menu Fechar e Parar ou clicando em Desligar no gerenciador de notebook.
   * Interrompendo outros aplicativos Spark do YARN.
2. Reinicie o notebook que você está tentando iniciar. Recursos suficientes devem estar disponíveis para você criar uma sessão agora.

## <a name="see-also"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)

