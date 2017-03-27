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
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5ec4b964066687b506686709c3dc5ed5b402fbaf
ms.openlocfilehash: a846d5a70451ed3082b90d87b90bef0eb6da5993
ms.lasthandoff: 02/09/2017


---
# <a name="use-the-hive-view-with-hadoop-in-hdinsight"></a>Use o Modo de Exibição do Hive com o Hadoop no HDInsight.

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

O Ambari é um utilitário de monitoramento e de gerenciamento fornecido com clusters HDInsight baseados em Linux. Um dos recursos fornecidos pelo Ambari é uma interface de usuário da Web que pode ser usada para executar consultas do Hive. É o **Modo de Exibição do Hive**, parte dos Modos de exibição do Ambari fornecidos com o cluster HDInsight.

> [!NOTE]
> O Ambari possui muitos recursos que não são abordados neste documento. Para saber mais, confira [Gerenciar clusters HDInsight usando a interface do usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="prerequisites"></a>Pré-requisitos

* Criar um cluster HDInsight baseado em Linux. Para saber mais sobre como criar um cluster, confira [Introdução ao HDInsight baseado no Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

> [!IMPORTANT]
> As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="open-the-hive-view"></a>Abrir o modo de exibição Hive

Você pode abrir os Modos de exibição do Ambari do portal do Azure; selecione seu cluster HDInsight e escolha **Modos de exibição do Ambari** na seção **Links Rápidos**.

![seção links rápidos](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

Você também pode navegar diretamente para o Ambari indo para https://NOMEDOCLUSTER.azurehdinsight.net em um navegador da web. Substitua **CLUSTERNAME** pelo nome do seu cluster HDInsight. Selecione o conjunto de quadrados no menu da página ao lado do link **Administrador** para listar os modos de exibição disponíveis. Escolha o **modo de exibição do Hive**.

![Escolhendo modos de exibição do Ambari](./media/hdinsight-hadoop-use-hive-ambari-view/selecthiveview.png).

> [!NOTE]
> Ao acessar o Ambari, você receberá uma solicitação para se autenticar no site. Insira o nome da conta e senha de administrador (o padrão é `admin`) que você usou ao criar o cluster.

Você verá uma página semelhante à seguinte:

![Imagem da página de modo de exibição do Hive, que contém uma seção de editor de consultas](./media/hdinsight-hadoop-use-hive-ambari-view/hiveview.png)

## <a name="view-tables"></a>Exibir tabelas
Na seção **Gerenciador de Banco de Dados** da página, escolha a entrada **padrão** na guia **Bancos de Dados**. Isso exibe uma lista de tabelas no banco de dados padrão. Deve haver apenas uma tabela em um novo cluster HDInsight: **hivesampletable**.

![gerenciador de banco de dados com o banco de dados padrão expandido](./media/hdinsight-hadoop-use-hive-ambari-view/databaseexplorer.png)

À medida que as tabelas forem adicionadas durante as etapas deste documento, você poderá usar o ícone de atualização no canto superior direito do Gerenciador de Banco de Dados para atualizar a lista.

## <a name="hivequery"></a>Editor de consultas

Use as seguintes etapas da exibição do Hive para executar uma consulta do Hive.

1. Na seção **Editor de Consultas** da página, cole as seguintes instruções HiveQL na planilha:
   
    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```
   
    As instruções executam as seguintes ações:
   
   * **DROP TABLE** - exclui a tabela e o arquivo de dados caso a tabela já exista.

   * **CREATE EXTERNAL TABLE** : cria uma nova tabela "externa" no Hive. 
   As tabelas externas armazenam apenas a definição da tabela no Hive. Os dados são mantidos no local original.

   * **ROW FORMAT** - informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

   * **STORED AS TEXTFILE LOCATION** - informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto.

   * **SELECT** - Seleciona uma contagem de todas as linhas em que a coluna t4 contém o valor [ERROR].
     
     > [!NOTE]
     > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo de carregamento de dados automatizados ou por outra operação MapReduce. Remover uma tabela externa *não* exclui os dados, somente a definição de tabela.

2. Para iniciar a consulta, use o botão **Executar** na parte inferior do Editor de Consultas. Ele fica laranja e o texto é alterado para **Parar execução**. Uma seção **Resultados do Processo de Consulta** deve aparecer abaixo do Editor de Consultas e exibir informações sobre o trabalho.
   
   > [!IMPORTANT]
   > Alguns navegadores podem não atualizar corretamente o log ou as informações dos resultados. Se você executar um trabalho e parecer que ele não cessa a execução sem atualizar o log ou retornar resultados, em vez disso, experimente usar o Mozilla FireFox ou o Google Chrome.
 
3. Depois que a consulta for concluída, a seção **Resultados do Processo de Consulta** exibirá os resultados da operação. O botão **Parar execução** também muda para um botão **Executar** verde quando a consulta é concluída. A guia **Resultados** deve conter as seguintes informações:
   
        sev       cnt
        [ERROR]   3
   
    A guia **Logs** pode ser usada para exibir as informações de log criadas pelo trabalho.
   
   > [!TIP]
   > O diálogo suspenso **Salvar resultados** no canto superior esquerdo da seção **Resultados do Processo de Consulta** permite que você baixe ou salve os resultados.

4. Selecione as quatro primeiras linhas dessa consulta e escolha **Executar**. Observe que não há resultados quando o trabalho é concluído. O uso do botão **Executar** quando parte da consulta está selecionada executa apenas as instruções escolhidas. Nesse caso, a seleção não incluiu a instrução final que recupera linhas da tabela. Se escolher apenas essa linha e usar **Executar**, você verá os resultados esperados.

5.Para adicionar uma nova planilha, use o botão **Nova Planilha** na parte inferior do **Editor de Consultas**. Na nova planilha, digite as seguintes instruções HiveQL:
   
    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```
   
    These statements perform the following actions:
   
   * **CREATE TABLE IF NOT EXISTS** - cria uma tabela, se ela ainda não existir. Como a palavra-chave **EXTERNAL** não é usada, uma tabela interna é criada. Uma tabela interna é armazenada no data warehouse do Hive e é totalmente gerenciada pelo Hive. Diferentemente de tabelas externas, o descarte de uma tabela interna excluirá também os dados subjacentes.

   * **STORES AS ORC** : armazena os dados no formato ORC (Optimized Row Columnar). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.

   * **INSERT OVERWRITE ... SELECT** - seleciona linhas da tabela **log4jLogs** que contêm [ERROR] e insere os dados na tabela **errorLogs**.
     
     Use o botão **Executar** para executar essa consulta. A guia **Resultados** não contém nenhuma informação quando a consulta retorna zero linhas. O status deve mostrar **SUCCEEDED** após a conclusão da consulta.

### <a name="hive-settings"></a>Configurações do Hive

Escolha o ícone **Configurações** à direita do editor.

![Ícone Configurações da exibição do hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-settings-icon.png)

As configurações podem ser usadas para alterar várias configurações do Hive, por exemplo, alterar o mecanismo de execução do Hive de Tez (padrão) para MapReduce.

### <a name="visualization"></a>Visualização

Escolha o ícone __Visualização__ à direita do editor.

![Ícone Visualização da exibição do hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization-icon.png)

Isso abre a interface de visualização, onde é possível criar visualizações dos dados retornados da consulta. A seguir, uma visualização de exemplo usando dados do `hivesampletable` incluído com o HDInsight.

![Visualização de exemplo](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization.png)

### <a name="visual-explain"></a>Explicação visual

Escolha o ícone **Explicação Visual** à direita do editor.

![Ícone de explicação visual para a visualização do hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visual-explain-icon.png)

Esse é o modo de exibição **Explicação Visual** da consulta, que pode ser útil na compreensão do fluxo de consultas complexas. Você pode exibir um equivalente textual desse modo de exibição usando o botão **Explicar** no Editor de Consultas.

![imagem de explicação visual](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

### <a name="tez"></a>Tez

Escolha o ícone de **Tez** à direita do editor.

![Ícone do Tez para exibição do hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-tez-icon.png)

Isso exibe o DAG (Gráfico Acíclico Dirigido) usado pelo Tez para essa consulta, se houver uma disponível. Se você quiser exibir o DAG de consultas executadas anteriormente ou depurar o processo do Tez, use [Modo de Exibição do Tez exibição](hdinsight-debug-ambari-tez-view.md) .

### <a name="notifications"></a>Notificações

Escolha o ícone de **Notificações** à direita do editor.

![Ícone Notificações para exibição do hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-notifications-icon.png)

Notificações são mensagens geradas durante a execução de consultas. Por exemplo, você receberá uma notificação quando uma consulta for enviada ou quando ocorrer um erro.

## <a name="saved-queries"></a>Consultas salvas

1. No Editor de Consultas, crie uma planilha e insira a seguinte consulta:
   
    ```hiveql
    SELECT * from errorLogs;
    ```
   
    Execute a consulta para verificar se funciona. Os resultados são semelhantes ao exemplo a seguir:
   
        errorlogs.t1     errorlogs.t2     errorlogs.t3     errorlogs.t4     errorlogs.t5     errorlogs.t6     errorlogs.t7
        2012-02-03     18:35:34     SampleClass0     [ERROR]     incorrect     id     
        2012-02-03     18:55:54     SampleClass1     [ERROR]     incorrect     id     
        2012-02-03     19:25:27     SampleClass4     [ERROR]     incorrect     id

2. Clique no botão **Salvar como** na parte inferior do editor. Nomeie essa consulta como **Errorlogs** e marque **OK**. O nome da planilha é alterado para **Errorlogs**.

3. Selecione a guia **Consultas Salvas** na parte superior da página Modo de Exibição do Hive. Agora, **Errorlogs** está listado como uma consulta salva. Ela permanecerá na lista até que você a remova. A seleção do nome abre a consulta no Editor de Consultas.

## <a name="query-history"></a>Histórico de consultas

O botão **Histórico** na parte superior do Modo de Exibição do Hive permite que você exiba consultas executadas anteriormente. Use-o agora e selecione algumas consultas executadas anteriormente. Quando você seleciona uma consulta, ela é aberta no Editor de Consultas.

## <a name="user-defined-functions-udf"></a>UDF (Funções definidas pelo usuário)

O Hive também pode ser estendido por meio de **UDF (funções definidas pelo usuário)**. As UDF permitem que você implemente funcionalidade ou lógica que não é facilmente modelada em HiveQL.

Na guia UDF na parte superior da exibição do Hive permite que você declare e salve um conjunto de UDFs que pode ser usado com o **Editor de Consultas**.

Depois de adicionar uma UDF ao Modo de Exibição do Hive, um botão **Inserir udfs** será exibido na parte inferior do **Editor de Consultas**. Ao selecionar esse botão, uma lista suspensa de UDFs definidas no Modo de Exibição do Hive será exibida. A seleção de uma UDF adiciona instruções HiveQL à sua consulta para habilitar a UDF.

Por exemplo, se você tiver definido uma UDF com as seguintes propriedades:

* Nome de recurso: myudfs

* Caminho do recurso: /myudfs.jar

* Nome da UDF: myawesomeudf

* Nome de classe da  UDF: com.myudfs.Awesome

O botão **Inserir udfs** exibe uma entrada denominada **myudfs**, com outra lista suspensa para cada UDF definida para esse recurso. Nesse caso, **myawesomeudf**. A seleção dessa entrada adiciona o seguinte ao início da consulta:

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Você pode usar a UDF em sua consulta. Por exemplo: `SELECT myawesomeudf(name) FROM people;`.

Para saber mais sobre como usar UDFs com Hive no HDInsight, consulte o seguinte:

* [Usando o Python com o Hive e com o Pig no HDInsight](hdinsight-python.md)
* [Como adicionar UDF personalizadas do Hive no HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a id="nextsteps"></a>Próximas etapas
Para informações gerais sobre o Hive no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)


