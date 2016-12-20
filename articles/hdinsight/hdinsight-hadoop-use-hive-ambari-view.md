---
title: "Usar os Modos de Exibição do Ambari para trabalhar com o Hive no HDInsight (Hadoop) | Microsoft Docs"
description: "Saiba como usar o Modo de Exibição do Hive em seu navegador da Web para enviar consultas do Hive. O Modo de exibição do Hive faz parte da Interface de Usuário da Web do Ambari fornecida com o cluster HDInsight baseado em Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4cdc1f0a8958edd23f8df02c4d16d3f60fe648bd


---
# <a name="use-the-hive-view-with-hadoop-in-hdinsight"></a>Use o Modo de Exibição do Hive com o Hadoop no HDInsight.
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

O Ambari é um utilitário de monitoramento e de gerenciamento fornecido com clusters HDInsight baseados em Linux. Um dos recursos fornecidos pelo Ambari é uma interface de usuário da Web que pode ser usada para executar consultas do Hive. É o **Modo de Exibição do Hive**, parte dos Modos de exibição do Ambari fornecidos com o cluster HDInsight.

> [!NOTE]
> O Ambari tem vários recursos que não serão discutidos neste documento. Para saber mais, confira [Gerenciar clusters HDInsight usando a interface do usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* Criar um cluster HDInsight baseado em Linux. Para saber mais sobre como criar um novo cluster, confira [Introdução ao HDInsight baseado no Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

## <a name="open-the-hive-view"></a>Abrir o modo de exibição Hive
Você pode abrir os Modos de exibição do Ambari do Portal do Azure; selecione seu cluster HDInsight e escolha **Modos de exibição do Ambari** na seção **Links Rápidos**.

![seção links rápidos](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

Você também pode navegar diretamente para o Ambari indo para https://NOMEDOCLUSTER.azurehdinsight.net em um navegador da Web (em que **NOMEDOCLUSTER** é o nome do cluster HDInsight) e escolhendo o conjunto de quadrados no menu da página (ao lado do link **Admin** e do botão no lado esquerdo da página) para listar os modos de exibição disponíveis. Escolha o **modo de exibição do Hive**.

![Escolhendo modos de exibição do Ambari](./media/hdinsight-hadoop-use-hive-ambari-view/selecthiveview.png).

> [!NOTE]
> Ao acessar o Ambari, você receberá uma solicitação para se autenticar no site. Insira o nome da conta e senha de administrador (o padrão é `admin`) que você usou ao criar o cluster.
> 
> 

Você verá uma página semelhante à seguinte:

![Imagem da página de modo de exibição do Hive, que contém uma seção de editor de consultas](./media/hdinsight-hadoop-use-hive-ambari-view/hiveview.png)

## <a name="view-tables"></a>Exibir tabelas
Na seção **Gerenciador de Banco de Dados** da página, escolha a entrada **padrão** na guia **Bancos de Dados**. Isso exibirá uma lista de tabelas no banco de dados padrão. Deve haver apenas uma tabela em um novo cluster HDInsight: **hivesampletable**.

![gerenciador de banco de dados com o banco de dados padrão expandido](./media/hdinsight-hadoop-use-hive-ambari-view/databaseexplorer.png)

À medida que novas tabelas forem adicionadas nas etapas deste documento, você poderá usar o ícone de atualização no canto superior direito do Gerenciador de Banco de Dados para atualizar a lista de tabelas disponíveis.

## <a name="a-namehivequeryaquery-editor"></a><a name="hivequery"></a>Editor de consultas
Use as etapas a seguir no modo de exibição do Hive para executar uma consulta do Hive em relação aos dados incluídos no cluster.

1. Na seção **Editor de Consultas** da página, cole as seguintes instruções HiveQL na planilha:
   
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
   
    As instruções executam as seguintes ações:
   
   * **DROP TABLE** - exclui a tabela e o arquivo de dados caso a tabela já exista.
   * **CREATE EXTERNAL TABLE** : cria uma nova tabela "externa" no Hive. As tabelas externas armazenam apenas a definição da tabela no Hive; os dados são deixados no local original.
   * **ROW FORMAT** - informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
   * **STORED AS TEXTFILE LOCATION** - informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto.
   * **SELECT** - Seleciona uma contagem de todas as linhas em que a coluna t4 contém o valor [ERROR].
     
     > [!NOTE]
     > Use tabelas externas se os dados subjacentes tiverem que ser atualizados por uma origem externa, como um processo automatizado de carregamento de dados, ou por outra operação MapReduce, mas você quiser que as consultas Hive sempre usem os dados mais recentes. Remover uma tabela externa *não* exclui os dados, somente a definição de tabela.
     > 
     > 
2. Use o botão **Executar** na parte inferior do Editor de Consultas para iniciar a consulta. Ele deverá ficar laranja e o texto será alterado para **Parar a execução**. Uma seção **Resultados do Processo de Consulta** deve aparecer abaixo do Editor de Consultas e exibir informações sobre o trabalho.
   
   > [!IMPORTANT]
   > Alguns navegadores podem não atualizar corretamente o log ou as informações dos resultados. Se você executar um trabalho e parecer que ele não cessa a execução sem atualizar o log ou retornar resultados, em vez disso, experimente usar o Mozilla FireFox ou o Google Chrome.
   > 
   > 
3. Depois que a consulta for concluída, a seção **Resultados do Processo de Consulta** exibirá os resultados da operação. O botão **Parar a execução** também se transformará em um botão verde **Executar**. A guia **Resultados** deve conter as seguintes informações:
   
        sev       cnt
        [ERROR]   3
   
    A guia **Logs** pode ser usada para exibir as informações de log criadas pelo trabalho. Você pode usar isso na solução de problemas, caso haja problemas com uma consulta.
   
   > [!TIP]
   > Observe a caixa de diálogo suspensa **Salvar resultados** no canto superior esquerdo da seção **Resultados do Processo de Consulta**. Você pode usá-la para baixar os resultados ou salvá-los no armazenamento do HDInsight como um arquivo CSV.
   > 
   > 
4. Selecione as quatro primeiras linhas dessa consulta e escolha **Executar**. Observe que não há resultados quando o trabalho é concluído. Isso ocorre porque, com o uso do botão **Executar** quando parte da consulta é selecionada, apenas as instruções escolhidas são executadas. Nesse caso, a seleção não incluiu a instrução final que recupera linhas da tabela. Se escolher apenas essa linha e usar **Executar**, você verá os resultados esperados.
5. Use o botão **Nova Planilha** na parte inferior do **Editor de Consultas** para criar uma nova planilha. Na nova planilha, digite as seguintes instruções HiveQL:
   
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
   
    As instruções executam as seguintes ações:
   
   * **CREATE TABLE IF NOT EXISTS** - cria uma tabela, se ela ainda não existir. Como a palavra-chave **EXTERNAL** não é usada, esta é uma tabela interna, que é armazenada no data warehouse do Hive e totalmente gerenciada pelo Hive. Ao contrário das tabelas externas, remover uma tabela interna excluirá também os dados subjacentes.
   * **STORES AS ORC** : armazena os dados no formato ORC (Optimized Row Columnar). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.
   * **INSERT OVERWRITE ... SELECT** - seleciona linhas da tabela **log4jLogs** que contêm [ERROR] e insere os dados na tabela **errorLogs**.
     
     Use o botão **Executar** para executar essa consulta. A guia **Resultados** não conterá informações, pois nenhuma linha é retornada por essa consulta, mas o status deverá ser mostrado como **ÊXITO**.

### <a name="hive-settings"></a>Configurações do Hive
Escolha o ícone **Configurações** à direita do editor.

![ícones](./media/hdinsight-hadoop-use-hive-ambari-view/settings.png)

As configurações podem ser usadas para alterar várias configurações do Hive, por  exemplo, alterar o mecanismo de execução do Hive de Tez (padrão) para MapReduce.

### <a name="visual-explain"></a>Explicação visual
Escolha o ícone **Explicação Visual** à direita do editor.

![ícones](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplainicon.png)

Esse é o modo de exibição **Explicação Visual** da consulta, que pode ser útil na compreensão do fluxo de consultas complexas. Você pode exibir um equivalente textual desse modo de exibição usando o botão **Explicar** no Editor de Consultas.

![imagem de explicação visual](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

### <a name="tez"></a>Tez
Escolha o ícone de **Tez** à direita do editor.

![ícones](./media/hdinsight-hadoop-use-hive-ambari-view/tez.png)

Isso exibirá o DAG (Gráfico acíclico dirigido) usado pelo Tez para essa consulta, se houver uma disponível. Se você quiser exibir o DAG de consultas executadas anteriormente ou depurar o processo do Tez, use [Modo de Exibição do Tez exibição](hdinsight-debug-ambari-tez-view.md) .

### <a name="notifications"></a>Notificações
Escolha o ícone de **Notificações** à direita do editor.

![ícones](./media/hdinsight-hadoop-use-hive-ambari-view/notifications.png)

Notificações são mensagens geradas durante a execução de consultas. Por exemplo, você receberá uma notificação quando uma consulta for enviada ou quando ocorrer um erro.

## <a name="saved-queries"></a>Consultas salvas
1. No Editor de Consultas, crie uma nova planilha e insira a seguinte consulta:
   
        SELECT * from errorLogs;
   
    Execute a consulta para verificar se funciona. Os resultados serão os seguintes:
   
        errorlogs.t1     errorlogs.t2     errorlogs.t3     errorlogs.t4     errorlogs.t5     errorlogs.t6     errorlogs.t7
        2012-02-03     18:35:34     SampleClass0     [ERROR]     incorrect     id     
        2012-02-03     18:55:54     SampleClass1     [ERROR]     incorrect     id     
        2012-02-03     19:25:27     SampleClass4     [ERROR]     incorrect     id
2. Clique no botão **Salvar como** na parte inferior do editor. Nomeie essa consulta como **Errorlogs** e marque **OK**. Observe que o nome da planilha é alterado para **Errorlogs**.
3. Selecione a guia **Consultas Salvas** na parte superior da página Modo de Exibição do Hive. Observe agora que **Errorlogs** está listado como uma consulta salva. Ele permanecerá na lista até que você o remova. A seleção do nome abrirá a consulta no Editor de Consultas.

## <a name="query-history"></a>Histórico de consultas
O botão **Histórico** na parte superior do Modo de Exibição do Hive permite que você exiba consultas executadas anteriormente. Use-o agora e selecione algumas consultas executadas anteriormente. Quando você seleciona uma consulta, ela é aberta no Editor de Consultas.

## <a name="user-defined-functions-udf"></a>UDF (Funções definidas pelo usuário)
O Hive também pode ser estendido por meio de **UDF (funções definidas pelo usuário)**. As UDF permitem que você implemente funcionalidade ou lógica que não é facilmente modelada em HiveQL.

Embora você possa adicionar uma UDF como parte das instruções HiveQL em sua consulta, a guia UDF na parte superior do Modo de Exibição do Hive permite que você declare e salve um conjunto de UDFs que podem ser usadas com o **Editor de Consultas**.

Depois de adicionar uma UDF ao Modo de Exibição do Hive, um botão **Inserir udfs** será exibido na parte inferior do **Editor de Consultas**. Ao selecionar esse botão, uma lista suspensa de UDFs definidas no Modo de Exibição do Hive será exibida. A seleção de uma UDF adicionará instruções HiveQL à sua consulta para habilitar a UDF.

Por exemplo, se você tiver definido uma UDF com as seguintes propriedades:

* Nome de recurso: myudfs
* Caminho do recurso: wasbs:///myudfs.jar
* Nome da UDF: myawesomeudf
* Nome de classe da  UDF: com.myudfs.Awesome

O botão **Inserir udfs** exibirá uma entrada denominada **myudfs**, com outra lista suspensa para cada UDF definida para esse recurso. Nesse caso, **myawesomeudf**. A seleção dessa entrada adicionará o seguinte ao início da consulta:

    add jar wasbs:///myudfs.jar;

    create temporary function myawesomeudf as 'com.myudfs.Awesome';

Você pode usar a UDF em sua consulta. Por exemplo: `SELECT myawesomeudf(name) FROM people;`.

Para saber mais sobre como usar UDFs com Hive no HDInsight, consulte o seguinte:

* [Usando o Python com o Hive e com o Pig no HDInsight](hdinsight-python.md)
* [Como adicionar UDF personalizadas do Hive no HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Próximas etapas
Para informações gerais sobre o Hive no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)




<!--HONumber=Nov16_HO3-->


