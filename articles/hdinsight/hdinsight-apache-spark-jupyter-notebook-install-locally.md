---
title: Instalar o bloco de notas Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight | Microsoft Docs
description: Saiba mais sobre como instalar o bloco de notas Jupyter localmente em seu computador e se conectar a um cluster Apache Spark no Azure HDInsight.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: nitinme

---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-cluster-on-hdinsight-linux"></a>Instalar o notebook Jupyter em seu computador e conectar-se ao cluster do Apache Spark no HDInsight Linux
Neste artigo, você aprenderá como instalar blocos de notas Jupyter, com o PySpark personalizado (para o Python) e kernels Spark (para Scala) com a mágica de Spark, e conectar o bloco de notas a um cluster HDInsight. Pode haver inúmeros motivos para instalar o Jupyter no computador local e alguns desafios também. Para obter uma lista de motivos e desafios, confira a seção [Por que devo instalar o Jupyter no meu computador](#why-should-i-install-jupyter-on-my-computer) no final deste artigo.

Há três etapas principais envolvidas na instalação do Jupyter e da mágica do Spark em seu computador.

* Instalar bloco de notas Jupyter
* Instalar os kernels PySpark e Spark com a mágica do Spark
* Configure a mágica do Spark para acessar o cluster Spark no HDInsight

Para saber mais sobre os kernels personalizados e a sobre a mágica Spark disponível para blocos de notas Jupyter com o cluster HDInsight, confira [Kernels disponíveis para blocos de notas Jupyter com clusters do Apache Spark Linux no HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Pré-requisitos
Os pré-requisitos listados aqui não são para a instalação do Jupyter. Eles são para conectar o bloco de notas Jupyter a um cluster HDInsight depois que o bloco de notas está instalado.

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, confira [Create Apache Spark clusters in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Instalar bloco de notas Jupyter em seu computador
Você deve instalar o Python antes de instalar notebooks do Jupyter. Python e Jupyter estão disponíveis como parte da [distribuição do Ananconda](https://www.continuum.io/downloads). Quando você instala o Anaconda, na verdade instala uma distribuição do Python. Quando o Anaconda é instalado, você adiciona a instalação do Jupyter executando um comando. Esta seção fornece as instruções que devem ser seguidas.

1. Baixe o [instalador do Anaconda](https://www.continuum.io/downloads) para sua plataforma e execute a instalação. Ao executar o assistente de instalação, não deixe de selecionar a opção de adicionar o Anaconda à variável PATH.
2. Execute o comando a seguir para instalar o Jupyter.
   
        conda install jupyter
   
    Para saber mais sobre a instalação do Jupyter, confira [Installing Jupyter using Anaconda](http://jupyter.readthedocs.io/en/latest/install.html)(Instalando o Jupyter usando Anaconda).

## <a name="install-the-kernels-and-spark-magic"></a>Instalar os kernels e a mágica do Spark
Para obter instruções sobre como instalar a mágica do Spark, os kernels PySpark e Spark, confira a [documentação do sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) no GitHub.

## <a name="configure-spark-magic-to-access-the-hdinsight-spark-cluster"></a>Configurar a mágica do Spark para acessar o cluster HDInsight Spark
Nesta seção, você configura a mágica do Spark instalada anteriormente para se conectar a um cluster Apache Spark que já deve ter criado no Azure HDInsight.

1. As informações de configuração do Jupyter normalmente são armazenadas no diretório base dos usuários. Para localizar seu diretório base em qualquer plataforma de sistema operacional, digite os comandos a seguir.
   
    Inicie o shell do Python. Em uma janela de comando, digite o seguinte:
   
        python
   
    No shell do Python, digite o comando a seguir para localizar o diretório base.
   
        import os
        print(os.path.expanduser('~'))
2. Navegue até o diretório base e crie uma pasta chamada **.sparkmagic** , caso ela ainda não exista.
3. Dentro da pasta, crie um arquivo chamado **config.json** e adicione o trecho de código JSON a seguir dentro dele.
   
        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }
4. Substitua **{USERNAME}**, **{CLUSTERDNSNAME}** e **{BASE64ENCODEDPASSWORD}** pelos valores apropriados. Você pode usar vários utilitários em sua linguagem de programação favorita ou online para gerar uma senha codificada em base64 para sua senha real. Um trecho de código Python simples para executar do prompt de comando seria:
   
        python -c "import base64; print(base64.b64encode('{YOURPASSWORD}'))"
5. Inicie o Jupyter. Use o comando do prompt de comando a seguir.
   
        jupyter notebook
6. Verifique se você pode se conectar ao cluster usando o bloco de notas Jupyter e se você pode usar a mágica Spark disponível com os kernels. Execute as seguintes etapas:
   
   1. Crie um novo bloco de anotações. No canto direito, clique em **Novo**. Você deve ver o kernel padrão **Python2** e os dois kernels novos instalados, **PySpark** e **Spark**.
      
       ![Criar um novo bloco de anotações do Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Create a new Jupyter notebook")

        Clique em **PySpark**.


    2. Execute o trecho de código a seguir.

            %%sql
            SELECT * FROM hivesampletable LIMIT 5

        Se você puder recuperar a saída com êxito, a conexão com o cluster HDInsight será testada.

    >[AZURE.TIP] Se você quiser atualizar a configuração do bloco de notas para se conectar a um cluster diferente, atualize o config.json com o novo conjunto de valores, conforme mostrado na Etapa 3 acima. 

## <a name="why-should-i-install-jupyter-on-my-computer?"></a>Por que devo instalar o Jupyter no meu computador?
Pode haver vários motivos pelos quais você possa querer instalar o Jupyter no computador e conectá-lo a um cluster Spark no HDInsight.

* Mesmo que os blocos de notas Jupyter já estejam disponíveis no cluster Spark no Azure HDInsight, a instalação do Jupyter no seu computador fornece a opção de criar os blocos de notas localmente, de testar o aplicativo em um cluster em execução e de carregar os blocos de notas no cluster. Para carregar os blocos de notas no cluster, você pode carregá-los usando o bloco de notas Jupyter que está em execução ou o cluster, ou salvá-los na pasta /HdiNotebooks na conta de armazenamento associada ao cluster. Para saber mais sobre como os blocos de notas são armazenados no cluster, confira [Onde os blocos de notas Jupyter são armazenados](hdinsight-apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Com os blocos de notas disponíveis localmente, você pode se conectar a clusters Spark diferentes com base nos requisitos do aplicativo.
* É possível usar o GitHub para implementar um sistema de controle de origem e ter o controle de versão para os blocos de notas. Você também pode ter um ambiente colaborativo, onde vários usuários podem trabalhar com o mesmo bloco de notas.
* Você pode trabalhar com blocos de notas localmente sem sequer ter um cluster em operação. É preciso apenas um cluster no qual testar seus blocos de notas, e não para gerenciar manualmente os blocos de notas ou um ambiente de desenvolvimento.
* Pode ser mais fácil configurar seu próprio ambiente de desenvolvimento local que configurar a instalação do Jupyter no cluster.  É possível aproveitar todo o software que você tem instalado localmente sem configurar um ou mais clusters remotos.

> [!WARNING]
> Com o Jupyter instalado no computador local, vários usuários podem executar o mesmo bloco de notas no mesmo cluster Spark ao mesmo tempo. Nessa situação, várias sessões Livy são criadas. Se você tiver um problema e desejar depurá-lo, será uma tarefa complexa rastrear qual sessão Livy pertence a qual usuário.
> 
> 

## <a name="<a-name="seealso"></a>see-also"></a><a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)

<!--HONumber=Oct16_HO2-->


