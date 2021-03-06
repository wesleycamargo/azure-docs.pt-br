---
title: Instalar o Jupyter localmente e conectar-se a um cluster do Spark do Azure HDInsight
description: Saiba mais sobre como instalar o bloco de anotações do Jupyter localmente em seu computador e se conectar a um cluster Apache Spark.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: hrasheed
ms.openlocfilehash: 7b20f0ec4669b485f87d050fcf597244fb898c85
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091249"
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Instalar o bloco de anotações do Jupyter em seu computador e conectar-se ao Apache Spark no HDInsight

Neste artigo, você aprenderá a instalar o notebook Jupyter, com os kernels PySpark (para Python) e Apache Spark (para Scala) personalizados com o Spark Magic, e conectar o notebook a um cluster do HDInsight. Pode haver inúmeros motivos para instalar o Jupyter no computador local e alguns desafios também. Para saber mais sobre isso, confira a seção [Por que devo instalar o Jupyter no meu computador](#why-should-i-install-jupyter-on-my-computer) no final deste artigo.

Há quatro etapas principais envolvidas na instalação do Jupyter e conectar-se ao Apache Spark no HDInsight.

* Configure o cluster Spark.
* Instalar Jupyter notebook.
* Instale os kernels PySpark e Spark com a mágica do Spark.
* Configure a mágica do Spark para acessar o cluster Spark no HDInsight.

Para saber mais sobre os kernels personalizados e a sobre a mágica Spark disponível para blocos de notas Jupyter com o cluster HDInsight, confira [Kernels disponíveis para blocos de notas Jupyter com clusters do Apache Spark Linux no HDInsight](apache-spark-jupyter-notebook-kernels.md).

> [!IMPORTANT]  
> As etapas neste artigo só funcionam para o Spark versão 2.1.0.

## <a name="prerequisites"></a>Pré-requisitos
Os pré-requisitos listados aqui não são para a instalação do Jupyter. Eles são para conectar o bloco de notas Jupyter a um cluster HDInsight depois que o bloco de notas está instalado.

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark (versão 2.1.0 ou inferior) no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).



## <a name="install-jupyter-notebook-on-your-computer"></a>Instalar bloco de notas Jupyter em seu computador

Você deve instalar o Python antes de instalar notebooks do Jupyter. Ambos o Python e o Jupyter estão disponíveis como parte da [distribuição do Anaconda](https://www.anaconda.com/download/). Quando instala o Anaconda, você instala uma distribuição do Python. Quando o Anaconda é instalado, você adiciona a instalação do Jupyter executando comandos apropriados.

1. Baixe o [instalador do Anaconda](https://www.anaconda.com/download/) para sua plataforma e execute a instalação. Ao executar o assistente de instalação, não deixe de selecionar a opção de adicionar o Anaconda à variável PATH.

2. Execute o comando a seguir para instalar o Jupyter.

        conda install jupyter

    Para obter mais informações sobre a instalação do Jupyter, confira [Installing Jupyter using Anaconda](https://jupyter.readthedocs.io/en/latest/install.html)(Instalando o Jupyter usando o Anaconda).

## <a name="install-the-kernels-and-spark-magic"></a>Instalar os kernels e a mágica do Spark

Para obter instruções sobre como instalar a mágica do Spark, os kernels PySpark e Spark, siga as instruções da instalação na [documentação do sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) no GitHub. A primeira etapa na documentação da mágica do Spark solicita a instalação da mágica do Spark. Substitua essa primeira etapa no link com os seguintes comandos, dependendo da versão do cluster do HDInsight de conexão. Depois disso, siga as etapas restantes na documentação da mágica do Spark. Se quiser instalar os kernels diferentes, você deverá executar a Etapa 3 na seção de instruções da instalação da mágica do Spark.

* Para clusters v3.5 e v3.6, instale sparkmagic 0.11.2 executando `pip install sparkmagic==0.11.2`

* Para clusters v3.4, instale sparkmagic 0.2.3 executando `pip install sparkmagic==0.2.3`

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configurar a mágica do Spark para se conectar ao cluster do HDInsight Spark

Nesta seção, você configura a mágica do Spark instalada anteriormente para se conectar a um cluster do Apache Spark que você já deve ter criado no Azure HDInsight.

1. Inicie o shell do Python com o seguinte comando:

    ```
    python
    ```

2. As informações de configuração do Jupyter normalmente são armazenadas no diretório base dos usuários. Insira o comando a seguir para identificar o diretório base e crie uma pasta chamada **sparkmagic**.  O caminho completo será podem ser exportado.

    ```python
    import os
    path = os.path.expanduser('~') + "\\.sparkmagic"
    os.makedirs(path)
    print(path)
    exit()
    ```

3. Dentro da pasta `.sparkmagic`, crie um arquivo chamado **config** e adicione o trecho JSON a seguir dentro dele.  

    ```json
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
      },

      "heartbeat_refresh_seconds": 5,
      "livy_server_heartbeat_timeout_seconds": 60,
      "heartbeat_retry_seconds": 1
    }
    ```
4. Faça as seguintes edições no arquivo:

    |Valor do modelo | Novo valor |
    |---|---|
    |{USERNAME}|Logon do cluster, o padrão é Admin.|
    |{CLUSTERDNSNAME}|Nome do cluster|
    |{BASE64ENCODEDPASSWORD}|Base64 codificado a senha para sua senha real.  Você pode gerar uma senha de base64 no [ https://www.url-encode-decode.com/base64-encode-decode/ ](https://www.url-encode-decode.com/base64-encode-decode/).|
    |`"livy_server_heartbeat_timeout_seconds": 60`|Lembre-se usando `sparkmagic 0.11.23` (clusters v3.5 e v3.6).  Se usando `sparkmagic 0.2.3` (clusters v3.4), substitua `"should_heartbeat": true`.|

    Você pode ver um arquivo de exemplo completo em [config. JSON de exemplo](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json).

   > [!TIP]  
   > As pulsações são enviadas para garantir que as sessões não sejam perdidas. Quando um computador entra em suspensão ou está desligado, a pulsação não é enviada e, como resultado, a sessão é limpa. Para clusters v3.4, se desejar desabilitar esse comportamento, você poderá definir a configuração Livy `livy.server.interactive.heartbeat.timeout` para `0` da interface do usuário do Ambari. Para clusters v3.5, se você não definir a configuração de 3.5 ou acima, a sessão não será excluída.

5. Inicie o Jupyter. Use o comando do prompt de comando a seguir.

        jupyter notebook

6. Verifique se que você pode usar a mágica Spark disponível com os kernels. Execute as seguintes etapas:

     a. Crie um novo bloco de anotações. No canto superior direito, selecione **New**. Você deve ver o kernel padrão **Python 2** ou **Python 3** e os kernels que você instalou. Os valores reais podem variar, dependendo de suas opções de instalação.  Selecione **PySpark**.

    ![Kernels no bloco de anotações do Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Kernels no bloco de anotações do Jupyter")

    > [!IMPORTANT]  
    > Depois de selecionar **New** examine seu shell para todos os erros.  Se você vir o erro `TypeError: __init__() got an unexpected keyword argument 'io_loop'` você esteja enfrentando um problema conhecido com determinadas versões do Tornado.  Nesse caso, pare o kernel e, em seguida, fazer downgrade de sua instalação Tornado com o seguinte comando: `pip install tornado==4.5.3`.

    b. Execute o snippet de código a seguir.

    ```sql
    %%sql
    SELECT * FROM hivesampletable LIMIT 5
    ```  

    Se você puder recuperar a saída com êxito, a conexão com o cluster HDInsight será testada.

    Se você quiser atualizar a configuração do bloco de anotações para se conectar a um cluster diferente, atualize o config. JSON com o novo conjunto de valores, conforme mostrado na etapa 3 acima.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Por que devo instalar o Jupyter no meu computador?

Pode haver vários motivos pelos quais você pode querer instalar o Jupyter em seu computador e, em seguida, conectá-lo a um cluster do Apache Spark no HDInsight.

* Mesmo que os blocos de notas Jupyter já estejam disponíveis no cluster Spark no Azure HDInsight, a instalação do Jupyter no seu computador fornece a opção de criar os blocos de notas localmente, de testar o aplicativo em um cluster em execução e de carregar os blocos de notas no cluster. Para carregar os blocos de notas no cluster, você pode carregá-los usando o bloco de notas Jupyter que está em execução ou o cluster, ou salvá-los na pasta /HdiNotebooks na conta de armazenamento associada ao cluster. Para saber mais sobre como os blocos de notas são armazenados no cluster, confira [Onde os blocos de notas Jupyter são armazenados](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)?
* Com os blocos de notas disponíveis localmente, você pode se conectar a clusters Spark diferentes com base nos requisitos do aplicativo.
* É possível usar o GitHub para implementar um sistema de controle de origem e ter o controle de versão para os blocos de notas. Você também pode ter um ambiente colaborativo, onde vários usuários podem trabalhar com o mesmo bloco de notas.
* Você pode trabalhar com blocos de notas localmente sem sequer ter um cluster em operação. É preciso apenas um cluster no qual testar seus blocos de notas, e não para gerenciar manualmente os blocos de notas ou um ambiente de desenvolvimento.
* Pode ser mais fácil configurar seu próprio ambiente de desenvolvimento local que configurar a instalação do Jupyter no cluster.  É possível aproveitar todo o software que você tem instalado localmente sem configurar um ou mais clusters remotos.

> [!WARNING]  
> Com o Jupyter instalado no computador local, vários usuários podem executar o mesmo bloco de notas no mesmo cluster Spark ao mesmo tempo. Nessa situação, várias sessões Livy são criadas. Se você tiver um problema e desejar depurá-lo, será uma tarefa complexa rastrear qual sessão Livy pertence a qual usuário.  

## <a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Apache Spark com BI: Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema de HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark com Machine Learning: Usar o Spark no HDInsight para prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análise de log do site usando o Apache Spark no HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](apache-spark-create-standalone-application.md)
* [Execute trabalhos remotamente em um cluster do Apache Spark usando o Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Use o Plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use o Plugin do HDInsight Tools para o IntelliJ IDEA para depurar os aplicativos do Apache Spark remotamente](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Use os blocos de anotações do Apache Zeppelin com um cluster do Apache Spark no HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels disponíveis para o notebook Jupyter no cluster do Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Usar pacotes externos com blocos de notas Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](apache-spark-job-debugging.md)
