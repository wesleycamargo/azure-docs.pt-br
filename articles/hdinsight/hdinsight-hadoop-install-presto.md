---
title: Instalar Presto em clusters Linux do Azure HDInsight
description: Saiba como instalar o Presto e o Airpal em clusters Hadoop do HDInsight baseados em Linux usando as ações de script.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: hrasheed
ms.openlocfilehash: 2bd5e1ae02ffbb62b9a5a95846aabeeab2b448b5
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704805"
---
# <a name="install-and-use-presto-on-hadoop-based-hdinsight-clusters"></a>Instalar e usar o Presto em clusters do HDInsight baseados em Hadoop

Este artigo explica como instalar o Presto em clusters de HDInsight do Azure com base em Hadoop por meio de ações de script. Você também aprenderá como instalar o Airpal em um cluster Presto do HDInsight existente.

O HDInsight também oferece o aplicativo Starburst Presto para clusters do Apache Hadoop. Para saber mais, confira [Instalar aplicativos de terceiros do Apache Hadoop no Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-install-applications).

> [!IMPORTANT]  
> As etapas deste artigo exigem um cluster Hadoop do HDInsight 3.5 que usa o Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou posterior. Para obter mais informações, consulte [Versões do HDInsight](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>O que é o Presto?
[Presto](https://prestosql.io) é um mecanismo de consulta SQL de distribuição rápida para big data. O Presto é ideal para consultas interativas de petabytes de dados. Para saber mais sobre os componentes do Presto e como eles funcionam juntos, confira [Conceitos do Presto](https://prestosql.io/docs/current/overview/concepts.html).

> [!WARNING]  
> Componentes fornecidos com o cluster HDInsight contam com suporte total. O Suporte da Microsoft ajuda a isolar e resolver problemas relacionados a esses componentes.
> 
> Componentes personalizados, como o Presto, recebem suporte comercial razoável para ajudá-lo a solucionar o problema. Esse suporte pode resolver o problema. Ou você pode ser solicitado a buscar nos canais disponíveis as tecnologias de software livre, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Há muitos sites de comunidades que podem ser usados. Por exemplo, o [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) e o [Stack Overflow](https://stackoverflow.com). 
>
> Os projetos do Apache têm sites de projeto no [site do Apache](https://apache.org). Um exemplo é o [Hadoop](https://hadoop.apache.org/).


## <a name="install-presto-by-using-script-actions"></a>Instalar o Presto usando ações de script

Esta seção explica como usar o script de exemplo durante a criação de um novo cluster usando o portal do Azure: 

1. Para começar a provisionar um cluster, siga as etapas em [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md). Crie o cluster usando o fluxo de criação de cluster **Personalizado**. O cluster deve atender aos seguintes requisitos:

   * Ele deve ser um cluster Hadoop com o HDInsight versão 3.6.

   * Ele deve usar o Armazenamento do Azure como armazenamento de dados. O uso do Presto em um cluster que utiliza o Azure Data Lake Storage como a opção de armazenamento ainda não é possível.

     ![HDInsight, Personalizado (tamanho, configurações, aplicativos)](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. Na área **Configurações avançadas**, selecione **Ações de script**. Forneça as seguintes informações. Você também pode escolher a opção **Instalar o Presto** para o tipo de script:
   
   * **NAME**. Insira um nome amigável para a ação de script.
   * **URI do script de bash**. `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`.
   * **CABEÇALHO**. Selecione essa opção.
   * **TRABALHO**. Selecione essa opção.
   * **ZOOKEEPER**. Deixe essa caixa de seleção em branco.
   * **PARÂMETROS**. Deixe este campo em branco.


3. Na parte inferior da área **Ações de script**, escolha o botão **Selecionar** para salvar a configuração. Por fim, escolha o botão **Selecionar** na parte inferior da área **Configurações avançadas** para salvar as informações de configuração.

4. Continue a provisionar o cluster conforme descrito em [Criar clusters baseados em Linux no HDInsight usando o portal do Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]  
    > O Azure PowerShell, CLI clássico do Azure, o SDK do .NET do HDInsight ou modelos do Azure Resource Manager também podem ser usados para aplicar ações de script. Também é possível aplicar ações de script a clusters que já estão em execução. Para saber mais, confira [Personalizar clusters HDInsight baseados em Linux usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Usar o Presto com o HDInsight

Para trabalhar com o Presto em um cluster do HDInsight, siga estas etapas:

1. Conecte-se ao cluster HDInsight usando SSH:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Para saber mais, confira [Conectar ao HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Inicie o shell do Presto executando o seguinte comando:
   
    `presto --schema default`

3. Execute uma consulta em uma tabela de exemplo, **hivesampletable**, que está disponível em todos os clusters do HDInsight por padrão:
   
    `select count (*) from hivesampletable;`
   
    Por padrão, os conectores [Apache Hive](https://prestosql.io/docs/current/connector/hive.html) e [TPCH](https://prestosql.io/docs/current/connector/tpch.html) já vêm configurados. O conector Hive é configurado para usar a instalação padrão do Hive. Portanto, todas as tabelas do Hive são automaticamente visíveis no Presto.

    Para obter mais informações, consulte a [documentação do Presto](https://prestosql.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Usar o Airpal com o Presto

O [Airpal](https://github.com/airbnb/airpal#airpal) é uma interface de consulta baseada na Web de código-fonte aberto para o Presto. Para saber mais sobre o Airpal, confira a [documentação do Airpal](https://github.com/airbnb/airpal#airpal).

Siga estas etapas para instalar o Airpal no nó de borda:

1. Usando o SSH, conecte-se ao nó principal do cluster do HDInsight que tenha o Presto instalado:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Para saber mais, confira [Conectar ao HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Quando você estiver conectado, execute o seguinte comando:

    `sudo slider registry  --name presto1 --getexp presto` 
   
    Você vê uma saída semelhante ao JSON a seguir:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. Na saída, observe o valor para a propriedade **value**. Você precisa desse valor ao instalar o Airpal no nó de borda do cluster. Na saída anterior, você precisa do valor **10.0.0.12:9090**.

4. Use [este modelo](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json) para criar um nó de borda do cluster do HDInsight. Forneça os valores mostrados na captura de tela a seguir.

    ![Implantação personalizada](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Selecione **Comprar**.

6. Depois de aplicar as alterações à configuração do cluster, acesse a interface Web do Airpal usando as etapas a seguir no [portal do Azure](https://portal.azure.com):

    1. No menu esquerdo, selecione **Todos os serviços**.

    1. Em **ANÁLISES**, selecione **Clusters do HDInsight**.

    1. Selecione seu cluster na lista para abrir a exibição padrão.

    1. Na exibição padrão, sob **Configurações**, selecione **Aplicativos**.

        ![HDInsight, Aplicativos](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    1. Na página **Aplicativos Instalados**, localize a entrada da tabela para **airpal**. Selecione **Portal**.

        ![Aplicativos instalados](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    1. Quando solicitado, insira as credenciais de administrador que você especificou quando criou o cluster do HDInsight baseado no Hadoop.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Personalizar uma instalação do Presto no cluster HDInsight

Para personalizar a instalação, siga estas etapas:

1. Usando o SSH, conecte-se ao nó principal do cluster do HDInsight que tenha o Presto instalado:
   
    `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Para saber mais, confira [Conectar ao HDInsight (Apache Hadoop) usando SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Faça as alterações de configuração no arquivo `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Para saber mais sobre a configuração do Presto, confira [Opções de configuração do Presto para clusters baseados em YARN](https://prestosql.github.io/presto-yarn/installation-yarn-configuration-options.html).

3. Pare e encerre a instância do Presto em execução no momento:

    `sudo slider stop presto1 --force` `sudo slider destroy presto1 --force`

4. Inicie uma nova instância do Presto com a personalização:

    `sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json`

5. Aguarde até que a nova instância esteja pronta. Anote o endereço do coordenador do Presto:

    `sudo slider registry --name presto1 --getexp presto`

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Gerar dados de avaliação de desempenho para clusters do HDInsight que executam o Presto

TPC-DS é o padrão da indústria para medir o desempenho de vários sistemas de suporte a decisões, incluindo sistemas de big data. É possível usar o Presto para gerar dados e avaliar como são comparados aos dados de referência do HDInsight. Para saber mais, confira [tpcds-hdinsight](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="next-steps"></a>Próximas etapas
* [Instalar e usar o Hue em clusters de Hadoop do HDInsight](hdinsight-hadoop-hue-linux.md). O Hue é uma interface do usuário da Web que facilita criar, executar e salvar trabalhos do Apache Pig e Hive.

* [Instalar o Apache Giraph nos clusters Hadoop do HDInsight e usar o Giraph para processar gráficos em grande escala](hdinsight-hadoop-giraph-install-linux.md). Use a personalização do cluster para instalar o Giraph em clusters do HDInsight baseados no Hadoop. Com o Giraph, é possível executar o processamento de gráficos usando Hadoop. Ele também pode ser usado com o Azure HDInsight.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
