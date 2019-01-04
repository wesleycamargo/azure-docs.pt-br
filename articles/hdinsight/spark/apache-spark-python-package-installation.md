---
title: Ação de script – instalar pacotes Python com o Jupyter no Azure HDInsight
description: Instruções passo a passo sobre como usar ação de script para configurar os blocos de anotações do Jupyter disponíveis com clusters Spark no HDInsight para usar pacotes Python externos.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: bfb2df377030f14893b3e124e6112ef6c2994afd
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321163"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Usar ação de script para instalar pacotes Python externos em notebooks Jupyter em clusters do Apache Spark no HDInsight
> [!div class="op_single_selector"]
> * [Usando a mágica da célula](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Usando a ação de script](apache-spark-python-package-installation.md)
>
>

Saiba como usar as Ações de script para configurar um cluster do [Apache Spark](https://spark.apache.org/) no HDInsight (Linux) para usar pacotes Python **externos, contribuídos pela comunidade** que não estão incluídos imediatamente o cluster.

> [!NOTE]
> Você também pode configurar um notebook Jupyter usando a mágica `%%configure` para usar pacotes externos. Para obter instruções, confira [Usar pacotes externos com notebooks Jupyter em clusters do Apache Spark no HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).
> 
> 

Você pode pesquisar o [índice do pacote](https://pypi.python.org/pypi) para obter uma lista de pacotes que estão disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, você pode instalar pacotes disponibilizados por meio [conda-forge](https://conda-forge.org/feedstocks/).

Neste artigo, você aprende como instalar o pacote [TensorFlow](https://www.tensorflow.org/) usando a Ação de Script em seu cluster e usá-lo através do notebook Jupyter como exemplo.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter o seguinte:

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]
   > Se você ainda não tiver um cluster Spark no HDInsight Linux, poderá executar ações de script durante a criação do cluster. Acesse a documentação em [como usar ações de script personalizadas](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   > 
   > 
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para software livre utilizado em clusters do HDInsight

O serviço Microsoft Azure HDInsight usa um ecossistema de tecnologias de código aberto formado em torno do Apache Hadoop. O Microsoft Azure fornece um nível geral de suporte para tecnologias de software livre. Para obter mais informações, consulte a seção **Escopo do Suporte** do [site de perguntas frequentes sobre o Suporte do Azure](https://azure.microsoft.com/support/faq/). O serviço HDInsight fornece um nível adicional de suporte a componentes internos.

Há dois tipos de componentes de software livre disponíveis no serviço HDInsight:

* **Componentes internos** : estes componentes estão pré-instalado em clusters HDInsight e fornecem a funcionalidade básica do cluster. Por exemplo, o Apache Hadoop YARN ResourceManager, a linguagem de consulta do Apache Hive (HiveQL) e a biblioteca do Mahout pertencem a essa categoria. Uma lista completa de componentes de cluster está disponível em [O que há de novo nas versões de cluster do Apache Hadoop fornecidas pelo HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Componentes personalizados** : como usuário do cluster, você pode instalar ou usar em sua carga de trabalho qualquer componente disponível na comunidade ou criado por você.

> [!WARNING]
> Componentes fornecidos com o cluster HDInsight contam com suporte total. O Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. O suporte da Microsoft pode estar apto a resolver o problema OU pode solicitar que você contate canais disponíveis para as tecnologias de software livre em que é encontrado conhecimento profundo sobre essa tecnologia. Por exemplo, existem muitos sites da comunidade que podem ser usados, como: [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Além disso, os projetos Apache têm sites de projeto em [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Usar pacotes externos com blocos de notas Jupyter

1. No [Portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   

2. Na folha do cluster Spark, clique em **Ações de Script** no painel esquerdo. Use o tipo de script "Personalizado" e insira um nome amigável para a ação de script. Execute o script no **nó de cabeçalho e nos nós de trabalho** e deixe o campo de parâmetros em branco. O script de Bash pode ser referenciado de: https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh Acesse a documentação em [como usar ações de script personalizadas](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

   > [!NOTE]
   > Há duas instalações Python no cluster. O Spark usará a instalação do Anaconda Python localizada em `/usr/bin/anaconda/bin` e usará como padrão o ambiente Python 2.7. Para usar o Python 3.x e instalar pacotes no kernel PySpark3, use o caminho para o executável `conda` para esse ambiente e use o parâmetro `-n` para especificar o ambiente. Por exemplo, o comando `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35` instala o pacote `ggplot` para o ambiente Python 3.5 usando o canal `conda-forge`.

3. Abrir um PySpark Jupyter Notebook

    ![Criar um novo bloco de anotações do Jupyter](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Criar um novo bloco de anotações do Jupyter")

4. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.

    ![Fornecer um nome para o bloco de anotações](./media/apache-spark-python-package-installation/hdinsight-spark-name-notebook.png "Fornecer um nome para o bloco de anotações")

5. Agora, você fará `import tensorflow` e executará um exemplo de hello world. 

    Código a ser copiado:

        import tensorflow as tf
        hello = tf.constant('Hello, TensorFlow!')
        sess = tf.Session()
        print(sess.run(hello))

    O resultado é semelhante a:
    
    ![Execução de código TensorFlow](./media/apache-spark-python-package-installation/execution.png "Executar código TensorFlow")

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Apache Spark usando o Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar pacotes externos com notebooks Jupyter em clusters Apache Spark no HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA para depurar os aplicativos do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o notebook Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
