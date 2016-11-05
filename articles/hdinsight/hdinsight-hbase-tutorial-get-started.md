---
title: 'Tutorial do HBase: Introdução ao HBase no Hadoop | Microsoft Docs'
description: Siga este tutorial do HBase para começar a usar o Apache HBase com o Hadoop no HDInsight. Criar tabelas a partir do shell do HBase e consultá-las usando o Hive.
keywords: apache hbase, hbase, shell do hbase,tutorial do hbase
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: jgao

---
# Tutorial do HBase: Introdução ao uso do Apache HBase com o Hadoop baseado no Windows no HDInsight
[!INCLUDE [hbase-selector](../../includes/hdinsight-hbase-selector.md)]

Saiba como criar um cluster HBase no HDInsight, criar tabelas HBase e consultar as tabelas usando o Apache Hive. Para obter informações gerais do HBase, consulte [Visão geral do HBase do HDInsight][hdinsight-hbase-overview].

As informações neste documento são específicas de clusters HDInsight baseados em Windows. Para saber mais sobre clusters baseados no Windows, use o seletor de guias na parte superior da página para alternar.

> [!NOTE]
> O HBase (versão 0.98.0) no HDInsight baseado em Windows está disponível somente para uso com os clusters do HDInsight 3.1 (baseado no Apache Hadoop e YARN 2.4.0). Para obter informações sobre a versão, consulte [O que há de novo nas versões de cluster do Hadoop fornecidas pelo HDInsight?][hdinsight-versions]
> 
> 

### Antes de começar
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de começar este tutorial do HBase, você deve ter o seguinte:

* **Uma assinatura do Microsoft Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Uma estação de trabalho** com o Visual Studio 2013 ou superior instalado: para obter instruções, veja [Instalar o Visual Studio](http://msdn.microsoft.com/library/e2h7fzkw.aspx).

## Nome do cluster HBase
[!INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Para criar um cluster HBase usando o portal do Azure**

1. Entre no [Portal do Azure][azure-management-portal].
2. Clique em **Novo** ou em **+** no canto superior esquerdo e clique em **Dados + Análise**, **HDInsight**.
3. Insira os valores a seguir:
   
   * **Nome do Cluster** - insira um nome para identificar este cluster.
   * **Tipo de Cluster**: selecione **HBase**
   * **Sistema Operacional do Cluster** - selecione **Windows**. Para a criação do cluster HBase baseado em Linux, veja [Tutorial do HBase: Introdução ao uso do Apache HBase com Hadoop no HDInsight (Linux)](hdinsight-hbase-tutorial-get-started-linux.md).
   * **Versão** - selecione uma versão do HBase.
   * **Assinatura** - selecione a sua assinatura do Azure usada para criar este cluster.
   * **Grupo de Recursos** - crie um novo grupo de recursos do Azure ou selecione um existente. Para saber mais, confira [Visão geral do Gerenciador de Recursos do Azure](../resource-group-overview.md).
   * **Credenciais** - para um cluster baseado no Windows, você pode criar um usuário de cluster (também conhecido como usuário HTTP, usuário do serviço Web HTTP) e um usuário de área de trabalho remota. Clique em **Habilitar Área de Trabalho Remota** para adicionar as credenciais do usuário da área de trabalho remota. A próxima seção requer RDP.
   * **Fonte de Dados** - crie uma nova conta de armazenamento do Azure ou selecione uma conta de armazenamento do Azure existente a ser usada como o sistema de arquivos padrão para o cluster. O local da conta de armazenamento padrão determina o local do cluster. A conta de armazenamento padrão e o cluster devem estar localizados no mesmo data center.
   * **Tipos de Preço do Nó** - selecione o número de servidores de região para o cluster HBase.
     
     > [!WARNING]
     > Para alta disponibilidade dos serviços HBase, você deverá criar um cluster que contenha pelo menos **três** nós. Isso garante que, se um nó falhar, as regiões de dados do HBase estão disponíveis em outros nós.
     > 
     > Se você estiver aprendendo a usar o HBase, sempre escolha 1 para o tamanho do cluster e exclua o cluster após cada uso para reduzir o custo.
     > 
     > 
   * **Configuração Opcional**: configure a rede virtual do Azure, configure as ações de Script e adicione mais contas de armazenamento.
4. Clique em **Criar**.

> [!NOTE]
> Depois que um cluster HBase for excluído, você pode criar outro cluster HBase usando a mesma conta de armazenamento padrão e o mesmo contêiner de blob padrão. O novo cluster selecionará as tabelas HBase criadas por você no cluster original. É recomendável desabilitar as tabelas HBase antes de excluir o cluster para evitar inconsistências.
> 
> 

## Criar tabelas e inserir dados
Atualmente, há duas maneiras de acessar o HBase. Esta seção aborda o uso do shell do HBase. A próxima seção aborda o uso do SDK do .NET.

Para a maioria das pessoas, os dados aparecem no formato de tabela:

![dados tabulares do hbase hdinsight][img-hbase-sample-data-tabular]

No HBase, que é uma implementação de BigTable, os mesmos dados são assim:

![dados bigtable do hbase hdinsight][img-hbase-sample-data-bigtable]

Depois de concluir o procedimento a seguir, ele fará mais sentido.

**Para usar o shell HBase**

1. Use o RDP para se conectar ao cluster HBase no HDInsight. Para obter instruções de RDP, consulte [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure][hdinsight-manage-portal].
2. Em sua sessão do RDP, clique no atalho **Linha de Comando do Hadoop** localizado na área de trabalho.
3. Abra o shell HBase:
   
        cd %HBASE_HOME%\bin
        hbase shell
4. Crie um HBase com duas famílias de coluna:
   
        create 'Contacts', 'Personal', 'Office'
        list
5. Insira alguns dados:
   
        put 'Contacts', '1000', 'Personal:Name', 'John Dole'
        put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
        put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
        put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
        scan 'Contacts'
   
    ![shell do hbase do hadoop hdinsight][img-hbase-shell]
6. Obter uma única linha
   
        get 'Contacts', '1000'
   
    Você verá os mesmos resultados do que quando usar o comando de verificação porque há apenas uma linha.
   
    Para obter mais informações sobre o esquema da tabela HBase, consulte [Introdução ao Design de esquema HBase][hbase-schema]. Para obter mais comandos HBase, consulte [Guia de referência do Apache HBase][hbase-quick-start].
7. Sair do shell
   
        exit

**Para carregar dados em massa na tabela de contatos HBase**

O HBase inclui vários métodos de carregamento de dados em tabelas. Para obter mais informações, consulte [Carregamento em massa](http://hbase.apache.org/book.html#arch.bulk.load).

Um arquivo de exemplo de dados foi carregado para um contêiner público de blobs, wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt. O conteúdo do arquivo de dados é:

    8396    Calvin Raji        230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600    Karen Wu        646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie        508-555-0163    230-555-0193    4912 La Vuelta
    16891    Jonn Jackson    674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile    592-555-0152    230-555-0196    1873 Lion Circle
    10272    Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes        599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander    670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443    Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Você pode criar um arquivo de texto e carregá-lo à sua própria conta de armazenamento se desejar. Para obter instruções, consulte [Carregar dados para trabalhos de Hadoop no HDInsight][hdinsight-upload-data].

> [!NOTE]
> Este procedimento usa a tabela Contatos HBase que você criou no último procedimento.
> 
> 

1. Em sua sessão do RDP, clique no atalho **Linha de Comando do Hadoop** localizado na área de trabalho.
2. Altere o diretório:
   
        cd %HBASE_HOME%\bin
3. Execute o seguinte comando para transformar o arquivo de dados para o StoreFiles e armazene em um caminho relativo especificado por Dimporttsv.bulk.output:
   
        hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:Phone, Office:Phone, Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasbs://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
4. Execute o seguinte comando para carregar os dados de /example/data/storeDataFileOutput para a tabela do HBase:
   
        hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
5. Você pode abrir o shell do HBase e usar o comando de verificação para listar o conteúdo da tabela.

## Usar o Hive para consultar tabelas HBase
Você pode consultar os dados armazenados no HBase usando o Hive. Esta seção cria uma tabela Hive que faz o mapeamento para a tabela do HBase e usa-a para consultar os dados em sua tabela do HBase.

**Para abrir o painel do cluster**

1. Navegue até **https://<Nome do Cluster HDInsight>.azurehdinsight.net/**.
2. Insira o nome de usuário e senha da conta do usuário do Hadoop. O nome de usuário padrão é **admin**, a senha é a que você inseriu durante o processo de criação. Uma nova guia do navegador é aberta.
3. Clique em **Editor Hive** na parte superior da página. O Editor Hive é semelhante a:
   
    ![Painel do cluster HDInsight.][img-hdinsight-hbase-hive-editor]

**Para executar consultas de Hive**

1. Insira o script HiveQL a seguir no Editor Hive e clique em **Enviar** para criar uma tabela Hive que é mapeada para a tabela HBase. Assegure-se de ter criado a tabela de amostra mencionada anteriormente neste tutorial, usando o shell HBase antes de executar esta instrução.
   
        CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
        TBLPROPERTIES ('hbase.table.name' = 'Contacts');
   
    Aguarde até o **Status** ser atualizado para **Concluído**.
2. Insira o script HiveQL a seguir no Editor de Hive e clique no botão **Enviar**. A consulta de Hive consulta os dados na tabela do HBase:
   
         SELECT count(*) FROM hbasecontacts;
3. Para recuperar os resultados da consulta do Hive, clique no link **Exibir Detalhes** na janela **Sessão da Tarefa** quando a execução da tarefa for concluída. Haverá apenas um arquivo de saída do trabalho, porque você coloca um registro na tabela HBase.

**Para pesquisar o arquivo de saída**

1. No Console de Consulta, clique em **Navegador de Arquivos**.
2. Clique na Conta de Armazenamento do Azure usada como o sistema de arquivos padrão para o cluster HBase.
3. Clique no nome do cluster HBase. O contêiner padrão de conta de armazenamento do Azure usa o nome do cluster.
4. Clique em **Usuário** e, em seguida, clique em **Admin**. (Esse é o nome de usuário do Hadoop.)
5. Clique no nome do trabalho com o horário da **Última modificação** correspondente ao horário de execução da consulta SELECIONAR do Hive.
6. Clique em **stdout**. Salve o arquivo e abra-o com o bloco de notas. Haverá um arquivo de saída.
   
    ![Navegador de arquivos do Editor Hive no HBase em HDInsight][img-hdinsight-hbase-file-browser]

## Usar a biblioteca de clienteda API REST .NET HBase
Você deve baixar a biblioteca de cliente API REST do HBase para .NET do GitHub e compilar o projeto para que você possa usar o SDK do .NET HBase. O procedimento a seguir inclui as instruções para essa etapa.

1. Crie um novo aplicativo C# do Visual Studio de Área de Trabalho do Windows.
2. Abra o console do Gerenciador de Pacotes NuGet clicando no menu **Ferramentas**, **Gerenciador de Pacotes NuGet**, **Console do Gerenciador de Pacotes**.
3. Execute o comando NuGet a seguir no console:
   
        Install-Package Microsoft.HBase.Client
4. Adicione as seguintes instruções **using** à parte superior do arquivo:
   
        using Microsoft.HBase.Client;
        using org.apache.hadoop.hbase.rest.protobuf.generated;
5. Substitua a função **Main** pelo seguinte:
   
        static void Main(string[] args)
        {
            string clusterURL = "https://<yourHBaseClusterName>.azurehdinsight.net";
            string hadoopUsername= "<yourHadoopUsername>";
            string hadoopUserPassword = "<yourHadoopUserPassword>";
   
            string hbaseTableName = "sampleHbaseTable";
   
            // Create a new instance of an HBase client.
            ClusterCredentials creds = new ClusterCredentials(new Uri(clusterURL), hadoopUsername, hadoopUserPassword);
            HBaseClient hbaseClient = new HBaseClient(creds);
   
            // Retrieve the cluster version.
            var version = hbaseClient.GetVersion();
            Console.WriteLine("The HBase cluster version is " + version);
   
            // Create a new HBase table.
            TableSchema testTableSchema = new TableSchema();
            testTableSchema.name = hbaseTableName;
            testTableSchema.columns.Add(new ColumnSchema() { name = "d" });
            testTableSchema.columns.Add(new ColumnSchema() { name = "f" });
            hbaseClient.CreateTable(testTableSchema);
   
            // Insert data into the HBase table.
            string testKey = "content";
            string testValue = "the force is strong in this column";
            CellSet cellSet = new CellSet();
            CellSet.Row cellSetRow = new CellSet.Row { key = Encoding.UTF8.GetBytes(testKey) };
            cellSet.rows.Add(cellSetRow);
   
            Cell value = new Cell { column = Encoding.UTF8.GetBytes("d:starwars"), data = Encoding.UTF8.GetBytes(testValue) };
            cellSetRow.values.Add(value);
            hbaseClient.StoreCells(hbaseTableName, cellSet);
   
            // Retrieve a cell by its key.
            cellSet = hbaseClient.GetCells(hbaseTableName, testKey);
            Console.WriteLine("The data with the key '" + testKey + "' is: " + Encoding.UTF8.GetString(cellSet.rows[0].values[0].data));
            // with the previous insert, it should yield: "the force is strong in this column"
   
            //Scan over rows in a table. Assume the table has integer keys and you want data between keys 25 and 35.
            Scanner scanSettings = new Scanner()
            {
                batch = 10,
                startRow = BitConverter.GetBytes(25),
                endRow = BitConverter.GetBytes(35)
            };
   
            ScannerInformation scannerInfo = hbaseClient.CreateScanner(hbaseTableName, scanSettings);
            CellSet next = null;
            Console.WriteLine("Scan results");
   
            while ((next = hbaseClient.ScannerGetNext(scannerInfo)) != null)
            {
                foreach (CellSet.Row row in next.rows)
                {
                    Console.WriteLine(row.key + " : " + Encoding.UTF8.GetString(row.values[0].data));
                }
            }
   
            Console.WriteLine("Press ENTER to continue ...");
            Console.ReadLine();
        }
6. Defina as primeiras três variáveis na função **Main**.
7. Pressione **F5** para executar o aplicativo.

## Verificar o status do cluster
O HBase em HDInsight é fornecido com uma interface do usuário da Web para monitorar clusters. Usando a interface do usuário da Web, você pode solicitar estatísticas ou informações sobre regiões.

Para abrir a interface do usuário da Web, você deve fazer RDP no cluster e, em seguida, clicar no atalho da interface do usuário da Web de informações do HMaster na área de trabalho ou usar a seguinte URL em um navegador da Web:

    http://zookeeper[0-2]:60010/master-status

Em um cluster de alta disponibilidade, você encontrará um link para o nó mestre HBase ativo atual que está hospedando a interface do usuário da Web.

## Excluir o cluster
É recomendável desabilitar as tabelas HBase antes de excluir o cluster para evitar inconsistências. [!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## O que vem a seguir?
Neste tutorial do HBase para o HDInsight, você aprendeu a criar um cluster HBase, criar tabelas e exibir os dados nessas tabelas por meio do shell HBase. Você também aprendeu a usar a consulta do Hive dos dados em tabelas HBase e como usar as APIs REST C# do HBase para criar uma tabela HBase e recuperar dados da tabela.

Para obter mais informações, consulte:

* [Visão geral do HDInsight HBase][hdinsight-hbase-overview]. O HBase é um banco de dados NoSQL de código aberto Apache baseado no Hadoop que fornece acesso aleatório e uma sólida consistência para grandes quantidades de dados não estruturados e semiestruturados.
* [Provisionar os clusters do HBase na Rede Virtual do Azure][hdinsight-hbase-provision-vnet] Com a integração da rede virtual, os clusters do HBase podem ser implantados na mesma rede virtual que seus aplicativos, de modo que os aplicativos possam se comunicar diretamente com o HBase.
* [Configurar a replicação do HBase no HDInsight](hdinsight-hbase-geo-replication.md) Saiba como configurar a replicação do HBase em dois datacenters Azure.
* [Analisar dados de sentimento no Twitter com o HBase no HDInsight][hbase-twitter-sentiment] Saiba como fazer a [análise de sentimento](http://en.wikipedia.org/wiki/Sentiment_analysis) em tempo real de Big Data, usando o HBase em um cluster Hadoop no HDInsight.

[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hbase-reference]: http://hbase.apache.org/book.html#importtsv
[hbase-schema]: http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf
[hbase-quick-start]: http://hbase.apache.org/book.html#quickstart





[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/

[img-hdinsight-hbase-cluster-quick-create]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-quick-create.png
[img-hdinsight-hbase-hive-editor]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-hive-editor.png
[img-hdinsight-hbase-file-browser]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-file-browser.png
[img-hbase-shell]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-shell.png
[img-hbase-sample-data-tabular]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-tabular.png
[img-hbase-sample-data-bigtable]: ./media/hdinsight-hbase-tutorial-get-started/hdinsight-hbase-contacts-bigtable.png

<!---HONumber=AcomDC_0914_2016-->