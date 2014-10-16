<properties linkid="manage-services-hdinsight-hbase-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Get started using HBase with Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using HBase with Hadoop in HDInsight. learn how to created HBase tables and query them with Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using HBase with Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev"></tags>

# Introdução ao uso do HBase com Hadoop no HDInsight

O HBase é um banco de dados NoSQL de baixa latência que permite o processamento transacional online de Big data. Ele é oferecido como um cluster gerenciado integrado no ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no armazenamento do Blob do Azure, que fornece baixa latência e aumento de elasticidade no desempenho e escolhas de custo. Isso permite aos clientes criarem websites interativos que trabalham com grandes conjuntos de dados, criar serviços que armazenam o dados de sensor e telemetria de milhões de terminas e analisar esses dados com tarefas Hadoop.

Neste tutorial, você aprenderá como criar e consultar tabelas Hbase com o HDInsight. Os procedimentos a seguir são descritos:

-   Como provisionar um cluster HBase usando o portal do Azure.
-   Como habilitar e usar o RDP para acessar o shell Hbase e usar o shell Hbase para criar uma tabela de amostra Hbase, incluir linhas e listar as linhas na tabela.
-   Como criar uma tabela Hive que mapeia para uma tabela Hbase existente e usar o HiveQL para consultar os dados na tabela HBase.
-   Como usar o SDK .NET para criar uma nova tabela Hbase, listar as tabelas Hbase em sua conta e como incluir e recuperar as linhas de suas tabelas.

> [WACOM.NOTE] O HBase está atualmente disponível na visualização para uso com clusters HDInsight 3.0 no HDInsight (baseado no Hadoop 2.2.0). Para obter informações sobre a versão, consulte [O que há de novo nas versões de cluster do Hadoop fornecidas pelo HDInsight?][]

Durante o período de visualização, é recomendado que uma cópia de backup da versão original de quaisquer dados usados no cluster Hbase seja armazenada fora desse cluster. Isso porque o formato dos arquivos do banco de dados podem ser alterados em versões futuras e os formatos de arquivo de dados atuais usados na versão de visualização podem não ter suporte ou não serem atualizáveis para versões subsequentes.

**Pré-requisitos:**

Antes de começar este tutorial, você deve ter o seguinte:

-   Uma assinatura do Azure. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][], [Ofertas para membros][] ou [Avaliação gratuita][].
-   Uma conta de armazenamento do Azure. Para obter instruções, consulte [Como criar uma conta de armazenamento][].
-   Uma cópia do Visual Studio.

**Tempo estimado para conclusão:** 30 minutos

## Neste tutorial

-   [Provisionar um cluster HBase no portal do Azure][]
-   [Criar uma tabela de amostra HBase a partir do shell HBase][]
-   [Usar o Hive para consultar uma tabela HBase][]
-   [Usar APIs C# do HBase para criar uma tabela HBase e recuperar dados da tabela][]
-   [Resumo][]

## <a name="create-hbase-cluster"></a>Provisionar um cluster HBase no portal do Azure

Esta seção descreve como provisionar um cluster HBase usando o portal do Azure.

**Para provisionar um cluster do HDInsight no portal do Azure**

1.  Entre no [Portal de Gerenciamento do Azure][].

2.  Clique em **HDInsight** à esquerda para listar o status dos clusters em sua conta e depois no ícone **+NOVO** na parte inferior esquerda.

    ![][]

3.  Clique no ícone HDInsight na segunda coluna da esquerda e na opção HBase na próxima coluna. Especifique os valores para o CLUSTERNAME e CLUSTER SIZE, o nome da Conta de Armazenamento e uma senha para o novo cluster HBase.

    ![][1]

4.  Clique no ícone de conferido na parte inferior esquerda para criar o cluster HBase.

## <a name="create-sample-table"></a>Criar uma tabela de amostra HBase a partir do shell HBase

Esta seção descreve como habilitar e usar o Remote Desktop Protocol (RDP) para acessar o shell HBase e usá-lo para criar uma tabela de amostra HBase, incluir linhas e listar as linhas na tabela.

Presume-se que você concluiu o procedimento descrito na primeira seção e, portanto, já criou com sucesso um cluster HBase.

**Habilitar a conexão do RDP com o cluster HBase**

1.  Para habilitar uma Conexão de Área de Trabalho Remota com o cluster HDInsight, selecione o cluster HBase que criou e clique na guia **CONFIGURAÇÃO**. Clique no botão **HABILITAR REMOTO** na parte inferior da página para habilitar a conexão do RDP com o cluster.
2.  Forneça as credenciais e a data de expiração no assistente **CONFIGURAR ÁREA DE TRABALHO REMOTA** e clique no círculo marcado na parte inferior direita. (Pode levar alguns minutos até a operação ser concluída.)
3.  Para conectar-se ao cluster HDInsight, clique no botão **CONECTAR** na parte inferior da guia **CONFIGURAÇÃO**.

**Abrir o Shell HBase**

1.  Em sua sessão do RDP, clique no atalho **Prompt de Comando do Hadoop** localizado na Área de Trabalho.

2.  Altere a pasta para o diretório inicial HBase:

        cd %HBASE_HOME%\bin

3.  Abra o shell HBase:

        hbase shell

**Crie uma tabela de amostra, inclua dados e recupere os dados**

1.  Crie uma tabela de amostra:

        create 'sampletable', 'cf1'

2.  Inclua uma linha na tabela de amostra:

        put 'sampletable', 'row1', 'cf1:col1', 'value1'

3.  Liste as linhas na tabela de amostra:

        scan 'sampletable'

## <a name="hive-query"></a>Usar o Hive para consultar uma tabela HBase

Agora que você tem um cluster HBase fornecido e criou uma tabela, é possível consultá-la usando o Hive. Esta seção cria uma tabela Hive que mapeia para a tabela HBase e usa-a para consultas nos dados em sua tabela HBase.

**Para abrir o painel do cluster**

1.  Entre no [Portal de Gerenciamento do Azure][].
2.  Clique em **HDINSIGHT** a partir do painel esquerdo. Você verá uma lista dos clusters criados, incluindo aquele que criou na última seção.
3.  Clique no nome do cluster em que deseja executar a tarefa do Hive.
4.  Clique em **GERENCIAR CLUSTER** na parte inferior da página para abrir o painel do cluster. Ele abre uma página da Web em uma guia diferente do navegador.
5.  Insira o nome de usuário e senha da conta do Usuário Hadoop. O nome de usuário padrão é **admin**, a senha é o que você digitou durante o processo de provisionamento. O painel fica assim:

    ![][2]

**Para executar uma consulta do Hive**

1.  Para criar uma Tabela Hive com um mapeamento para a tabela HBase, insira o script HiveQL abaixo na janela do console Hive e clique no botão **ENVIAR**. Assegure-se de ter criado a tabela de amostra referenciada aqui no HBase usando o Shell HBase antes de executar esta declaração.

        SET hbase.zookeeper.quorum=zookeepernode0,zookeepernode1,zookeepernode2;  
        CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
        TBLPROPERTIES ('hbase.table.name' = 'sampletable');

2.  Para executar uma Consulta do Hive nos Dados no HBase, insira o script HiveQL abaixo na janela do console Hive e clique no botão **ENVIAR**.

        SET hbase.zookeeper.quorum=zookeepernode0,zookeepernode1,zookeepernode2;
        SET hive.aux.jars.path=file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hive-hbase-handler-0.12.0.2.0.9.0-1677.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-server-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-protocol-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/htrace-core-2.01.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-client-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/guava-12.0.1.jar;
        SELECT count(*) FROM hbasesampletable;

3.  Para recuperar os resultados da consulta do Hive, clique no link **Visualizar Detalhes** na janela **Sessão da Tarefa** quando a execução da tarefa for concluída.

Observação: O link do shell HBase alterna a guia para o **Shell HBase**.

**Para pesquisar o arquivo de saída**

1.  No painel do cluster, clique em **Arquivos** na parte superior.
2.  Clique emk **Templeton-Job-Status**.
3.  Clique no número da GUID que possui o último Horário modificado um pouco após o Hora de início da tarefa que você anotou anteriormente. Anote este GUID. Você precisará dele na próxima seção.
4.  O arquivo **stdout** possui os dados necessários para a próxima seção. É possível clicar em **stdout** para baixar uma cópia do arquivo de dados se desejar.

## <a name="hbase-powershell"></a>Usar APIs C# do HBase para criar uma tabela HBase e recuperar dados da tabela

Marlin é uma camada fina sobre a API REST, que cuida da interação com o HBase usando o ProtoBuf no C\#. O projeto Marlin deve ser transferido por download do github e o projeto criado para usar o SDK .NET do HBase.

1.  Siga as etapas de criação descritas em Baixar o projeto Marlin na [página do projeto Marlin][]. Descompacte-o em um diretório local.

2.  Abra o projeto no Visual Studio. Abra o assistente de gerenciador Gerenciar Pacote NuGet acessando os menus **FERRAMENTAS** -\> **Gerenciador de Pacote de Biblioteca** e selecione **Gerenciar pacotes NuGet para Solução...**

    ![][3]

3.  Na caixa Online, na parte superior direita, pesquise protobuf-net e instale a v2.0.0.68. Crie o projeto Marlin clicando com o botão direito no projeto **Marlin** no **Gerenciador de Soluções** e selecionando **Criar**.

4.  Recupere o marlin.dll resultante criado e inclua-o no seu projeto C\#.

5.  Crie uma nova instância do Marlin usando as credenciais de cluster e recupere a versão do cluster:

        var credentials = ClusterCredentials.Create("https://yourclustername.azurehdinsight.net/", "user", "password");
            var marlin = new Marlin(credentials);
        // retrieve the version as a test
        var version = marlin.GetVersion();
        Console.WriteLine(version);

6.  Para listar as tabelas no cluster, é possível usar o código a seguir:

        var tables = marlin.ListTables();
        foreach(var tableName in tables.name) 
                Console.WriteLine(tableName);

7.  Para criar uma nova tabela HBase, use este código:

        var schema = new TableSchema();
        schema.name = "sampletable";
        schema.columns.Add(new ColumnSchema() { name = "cf1" });
        schema.columns.Add(new ColumnSchema() { name = "cf2" });
        marlin.CreateTable(schema);

8.  Para recuperar uma linha pela chave, especifique o nome da tabela e uma chave de linha para recuperar um valor de linha.

        var cells = marlin.GetCells("sampletable", "row1");
        var row = cells.rows[0];
            foreach(var val in row.values) 
            {
               Console.WriteLine(Encoding.UTF8.GetString(val.data));
            }

9.  Para armazenar uma nova linha de dados, é possível usar o código a seguir:

        CellSet set = new CellSet();
        CellSet.Row row = new CellSet.Row() { key = BitConverter.GetBytes(1337) };
            var value = new Cell()
                    {
                        column = Encoding.UTF8.GetBytes("cf1:d"),
                        data = Encoding.UTF8.GetBytes("Hello World!")
                    };
            row.values.Add(value);
            set.rows.Add(row);
        marlin.StoreCells("sampletable", set);

## <a name="summary"></a>Resumo

Neste tutorial, você aprendeu a provisionar um cluster HBase, criar tabelas e exibir os dados nessas tabelas a partir do shell HBase. Você também aprendeu a usar o Hive para consultar os dados em tabelas HBase e como usar as APIs C# do HBase para criar uma tabela HBase e recuperar dados da tabela.

  [O que há de novo nas versões de cluster do Hadoop fornecidas pelo HDInsight?]: ../hdinsight-component-versioning/
  [Opções de compra]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Ofertas para membros]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Avaliação gratuita]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Como criar uma conta de armazenamento]: http://azure.microsoft.com/pt-br/documentation/articles/storage-create-storage-account/
  [Provisionar um cluster HBase no portal do Azure]: #create-hbase-cluster
  [Criar uma tabela de amostra HBase a partir do shell HBase]: #create-sample-table
  [Usar o Hive para consultar uma tabela HBase]: #hive-query
  [Usar APIs C# do HBase para criar uma tabela HBase e recuperar dados da tabela]: #hbase-powershell
  [Resumo]: #summary
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  []: http://i.imgur.com/PmGynKZ.jpg
  [1]: http://i.imgur.com/ecxbB9K.jpg
  [2]: http://i.imgur.com/tMwXlj9.jpg
  [página do projeto Marlin]: https://github.com/thomasjungblut/marlin
  [3]: http://i.imgur.com/hUNoJDJ.jpg
