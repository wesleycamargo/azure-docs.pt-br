---
title: Instalar Presto em clusters Linux do Azure HDInsight
description: Saiba como instalar o Presto e o Airpal em clusters Hadoop do HDInsight baseados em Linux usando as ações de script.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: b9ac9c49e633906e47244eedcb18a4cda4a6228d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978940"
---
# <a name="install-and-use-presto-on-hdinsight-hadoop-clusters"></a>Instalar e usar Presto em clusters Hadoop do HDInsight

Neste documento, você aprende como instalar o Presto em clusters Hadoop do HDInsight usando a Ação de Script. Você também aprenderá como instalar o Airpal em um cluster Presto do HDInsight existente.

> [!IMPORTANT]
> As etapas deste documento exigem um **cluster Hadoop do HDInsight 3.5** que usa Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, consulte [Versões do HDInsight](hdinsight-component-versioning.md).

## <a name="what-is-presto"></a>O que é o Presto?
[Presto](https://prestodb.io/overview.html) é um mecanismo de consulta SQL distribuído rápido para big data. O Presto é ideal para consultas interativas de petabytes de dados. Para saber mais sobre os componentes do Presto e como eles funcionam juntos, confira [Conceitos do Presto](https://github.com/prestodb/presto/blob/master/presto-docs/src/main/sphinx/overview/concepts.rst).

> [!WARNING]
> Há suporte total a componentes fornecidos com o cluster HDInsight e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.
> 
> Componentes personalizados, como o Presto, recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de você buscar nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites de comunidades que podem ser usados, como o [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Além disso, os projetos do Apache têm sites do projeto em [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).
> 
> 


## <a name="install-presto-using-script-action"></a>Instalar o Presto usando a ação de script

Esta seção fornece instruções sobre como usar o script de exemplo durante a criação de um novo cluster usando o portal do Azure. 

1. Inicie o provisionamento de um cluster usando as etapas em [Provisionar clusters HDInsight baseados em Linux](hdinsight-hadoop-create-linux-clusters-portal.md). Crie o cluster usando o fluxo de criação de cluster **Personalizado**. O cluster deve atender aos requisitos a seguir.

    * Ele deve ser um cluster Hadoop com o HDInsight versão 3.6.

    * Ele deve usar o Armazenamento do Azure como armazenamento de dados. Não há suporte para o uso do Presto em um cluster que usa o Azure Data Lake Store como opção de armazenamento no momento. 

    ![Criação do cluster HDInsight usando opções personalizadas](./media/hdinsight-hadoop-install-presto/hdinsight-install-custom.png)

2. Na área **Configurações avançadas**, selecione **Ações de Script** e forneça as informações abaixo:
   
   * **NOME**: insira um nome amigável para a ação de script.
   * **URI do script Bash**: `https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh`
   * **CABEÇALHO**: marque esta opção
   * **TRABALHO**: marque esta opção
   * **ZOOKEEPER**: desmarque essa caixa de seleção
   * **PARÂMETROS**: deixe este campo em branco


3. Na parte inferior da área **Ações de Script**, clique no botão **Selecionar** para salvar a configuração. Finalmente, clique no botão **Selecionar** na parte inferior da área **Configurações Avançadas** para salvar as informações de configuração.

4. Continue o provisionamento do cluster conforme descrito em [Provisionar clusters HDInsight baseados em Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

    > [!NOTE]
    > O Azure PowerShell, CLI clássico do Azure, o SDK do .NET do HDInsight ou modelos do Azure Resource Manager também podem ser usados para aplicar ações de script. Também é possível aplicar ações de script a clusters que já estão em execução. Para saber mais, veja [Personalizar clusters HDInsight com as Ações de Script](hdinsight-hadoop-customize-cluster-linux.md).
    > 
    > 

## <a name="use-presto-with-hdinsight"></a>Usar o Presto com o HDInsight

Para trabalhar com o Presto em um cluster HDInsight, use as etapas a seguir:

1. Conecte-se ao cluster HDInsight usando SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
     

2. Inicie o shell Presto usando o comando a seguir.
   
        presto --schema default

3. Execute uma consulta em uma tabela de exemplo, **hivesampletable**, que está disponível em todos os clusters do HDInsight por padrão.
   
        select count (*) from hivesampletable;
   
    Por padrão, os conectores [Hive](https://prestodb.io/docs/current/connector/hive.html) e [TPCH](https://prestodb.io/docs/current/connector/tpch.html) já vêm configurados. O conector Hive é configurado para usar a instalação do Hive padrão existente, para que todas as tabelas do Hive fiquem automaticamente visíveis no Presto.

    Para obter mais informações, consulte a [documentação do Presto](https://prestodb.io/docs/current/index.html).

## <a name="use-airpal-with-presto"></a>Usar o Airpal com o Presto

O [Airpal](https://github.com/airbnb/airpal#airpal) é uma interface de consulta baseada na Web de código-fonte aberto para o Presto. Para saber mais sobre o Airpal, confira a [documentação do Airpal](https://github.com/airbnb/airpal#airpal).

Use as etapas a seguir para instalar o Airpal no nó de borda:

1. Usando o SSH, conecte-se ao nó principal do cluster do HDInsight que tenha o Presto instalado:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Quando você estiver conectado, execute o comando a seguir.

        sudo slider registry  --name presto1 --getexp presto 
   
    Você vê uma saída semelhante ao JSON a seguir:

        {
            "coordinator_address" : [ {
                "value" : "10.0.0.12:9090",
                "level" : "application",
                "updatedTime" : "Mon Apr 03 20:13:41 UTC 2017"
        } ]

3. Na saída, observe o valor para a propriedade **value**. Você precisará desse valor ao instalar o Airpal no nó de borda de cluster. Na saída acima, você precisará do valor **10.0.0.12:9090**.

4. Use o modelo **[aqui](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2Fpresto-hdinsight%2Fmaster%2Fairpal-deploy.json)** para criar um edgenode de cluster HDInsight e forneça os valores conforme mostrado na captura de tela a seguir.

    ![Instalação do Airpal no cluster Presto pelo HDInsight](./media/hdinsight-hadoop-install-presto/hdinsight-install-airpal.png)

5. Clique em **Comprar**.

6. Depois que as alterações são aplicadas à configuração do cluster, você pode acessar a interface Web do Airpal usando as etapas a seguir.

    1. Na caixa de diálogo do cluster, clique em **Aplicativos**.

        ![O HDInsight inicia o Airpal no cluster Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal.png)

    2. Na área **Aplicativos Instalados**, clique em **Portal** no Airpal.

        ![O HDInsight inicia o Airpal no cluster Presto](./media/hdinsight-hadoop-install-presto/hdinsight-presto-launch-airpal-1.png)

    3. Quando solicitado, insira as credenciais de administrador que você especificou ao criar o cluster Hadoop do HDInsight.

## <a name="customize-a-presto-installation-on-hdinsight-cluster"></a>Personalizar uma instalação do Presto no cluster HDInsight

Para personalizar a instalação, use as seguintes etapas:

1. Usando o SSH, conecte-se ao nó principal do cluster do HDInsight que tenha o Presto instalado:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Faça as alterações de configuração no arquivo `/var/lib/presto/presto-hdinsight-master/appConfig-default.json`. Para saber mais sobre a configuração do Presto, confira [Configuração do Presto para clusters baseados em YARN](https://prestodb.io/presto-yarn/installation-yarn-configuration-options.html).

3. Pare e elimine a instância do Presto em execução no momento.

        sudo slider stop presto1 --force
        sudo slider destroy presto1 --force

4. Inicie uma nova instância do Presto com a personalização.

       sudo slider create presto1 --template /var/lib/presto/presto-hdinsight-master/appConfig-default.json --resources /var/lib/presto/presto-hdinsight-master/resources-default.json

5. Aguarde até que a nova instância esteja pronta e anote o endereço do coordenador do Presto.


       sudo slider registry --name presto1 --getexp presto

## <a name="generate-benchmark-data-for-hdinsight-clusters-that-run-presto"></a>Gerar dados de avaliação de desempenho para clusters do HDInsight que executam o Presto

TPC-DS é o padrão da indústria para medir o desempenho de vários sistemas de suporte de decisão, incluindo sistemas de big data. É possível usar o Presto para gerar dados e avaliar como são comparados aos dados de referência do HDInsight. Para saber mais, clique [aqui](https://github.com/hdinsight/tpcds-datagen-as-hive-query/blob/master/README.md).



## <a name="see-also"></a>Consulte também
* [Instalar e usar matiz em clusters HDInsight](hdinsight-hadoop-hue-linux.md). O Hue é uma interface do usuário da Web que facilita criar, executar e salvar trabalhos do Hive e Pig.

* [Instalar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use a personalização do cluster para instalar o Giraph em clusters de Hadoop do HDInsight. O Giraph permite que você realize processamento de grafos usando o Hadoop, além de poder ser usado com o HDInsight do Azure.

* [Instalar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). Use a personalização do cluster para instalar o Solr em clusters de Hadoop do HDInsight. O Solr permite que você execute operações de pesquisa poderosas nos dados armazenados.

[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
