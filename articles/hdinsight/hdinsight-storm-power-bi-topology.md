<properties
 pageTitle="Usar o Apache Storm com o Power BI | Microsoft Azure"
 description="Crie um relatório do Power BI usando dados de uma topologia C# em execução em um cluster do Apache Storm no HDInsight."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="05/27/2016"
 ms.author="larryfr"/>

# Usar o Power BI para visualizar dados de uma topologia do Apache Storm

O Power BI permite a exibição visual dos dados como relatórios. Usando modelos do Visual Studio para Storm no HDInsight, você pode usar facilmente o dados do repositório de uma topologia em execução em um Apache Storm no cluster HDInsight no SQL Azure e visualizar os dados usando o Power BI.

Neste documento, você aprenderá como usar o Power BI para criar um relatório de dados gerados por uma topologia Apache Storm e armazenado no Banco de Dados SQL do Azure.

## Pré-requisitos

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um usuário do Active Directory do Azure com acesso ao [Power BI](https://powerbi.com)

* Visual Studio (uma das seguintes versões a seguir)

    * Visual Studio 2012 com [atualização 4](http://www.microsoft.com/download/details.aspx?id=39305)

    * Visual Studio 2013 com [atualização 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)

    * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)

* Ferramentas do HDInsight para o Visual Studio: consulte [Comece a usar as Ferramentas do HDInsight para o Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md) para obter informações sobre a instalação.

## Como ele funciona

Este exemplo contém uma topologia do Storm em C# que gera aleatoriamente dados de log do IIS (Serviços de Informações da Internet). Esses dados são gravados em um Banco de Dados SQL e, por meio dele, são usados para gerar relatórios no Power BI.

A seguir está uma lista dos arquivos que implementam a funcionalidade principal desse exemplo.

* **SqlAzureBolt.cs**: grava informações produzidas na topologia do Storm no Banco de Dados SQL.

* **IISLogsTable.sql**: as instruções Transact-SQL usadas para gerar o banco de dados no qual os dados são armazenados.

> [AZURE.WARNING] Você deve criar a tabela no Banco de Dados SQL antes de iniciar a topologia em seu cluster HDInsight.

## Baixar o exemplo

Baixe o [exemplo do Power BI do Storm em C# do HDInsight](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi). Para baixá-lo, use uma bifurcação/clone-o usando [git](http://git-scm.com/) ou use o link de **Download** link para baixar um .zip do arquivo.

## Criar um banco de dados

1. Use as etapas no documento [Tutorial do Banco de Dados SQL](../sql-database/sql-database-get-started.md) para criar um novo Banco de Dados SQL.

2. Conecte-se ao banco de dados seguindo as etapas no documento [Conectar-se ao Banco de Dados SQL com o Visual Studio](../sql-database/sql-database-connect-query.md) para se conectar ao banco de dados.

4. Clique com o botão direito do mouse no banco de dados no Pesquisador de Objetos e criar uma __Nova Consulta__. Cole o conteúdo do arquivo __IISLogsTable.sql__ incluído no projeto baixado na janela de consulta e use Ctrl + Shift + E para executar a consulta. Você deve receber uma mensagem de que os comandos foram concluídos com êxito.

    Quando isso for concluído, haverá uma nova tabela chamada __IISLOGS__ no banco de dados.

## Configurar o exemplo

1. No [Portal do Azure](https://portal.azure.com), selecione seu Banco de Dados SQL. Na seção __Fundamentos__ da folha Banco de Dados SQL, selecione __Mostrar cadeias de conexão de banco de dados__. Na lista exibida, copie as informações de __ADO.NET (autenticação do SQL)__.

1. Abra o exemplo no Visual Studio. No **Gerenciador de Soluções**, abra o arquivo **App.config** e localize a seguinte entrada:

        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />
    
    Substitua o valor __##TOBEFILLED##__ valor pela cadeia de conexão de banco de dados copiada na etapa anterior. Substitua __{your\_username}__ e __{your\_password}__ por nome de usuário e a senha para o banco de dados.

2. Salve e feche os arquivos.

## Implantar o exemplo

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **StormToSQL** e selecione **Enviar para o Storm no HDInsight**. Selecione o cluster HDInsight no menu suspenso **Cluster Storm**.

    > [AZURE.NOTE] Pode levar alguns segundos para a lista suspensa **Cluster Storm** ser populada com os nomes de servidor.
    >
    > Se solicitado, insira as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, faça o logon naquela que contém seu Storm no cluster HDInsight.

2. Depois que a topologia tiver sido enviada com êxito, as Topologias Storm para o cluster deverão ser exibidas. Selecione a entrada SqlAzureWriterTopology na lista para exibir informações sobre a topologia em execução.

    ![As topologias, com a topologia selecionada](./media/hdinsight-storm-power-bi-topology/topologyview.png)

    Você pode usar esta exibição para ver informações sobre a topologia ou clicar duas vezes nas entradas (como SqlAzureBolt) para ver as informações específicas de um componente na topologia.

3. Depois que a topologia estiver executado por alguns minutos, volte para a janela de consulta do SQL usada para criar o banco de dados. Substitua as instruções existentes pelo seguinte.

        select * from iislogs;
    
    Use Ctrl + Shift + E para executar a consulta e você deverá receber resultados semelhantes aos a seguir.
    
        1	2016-05-27 17:57:14.797	255.255.255.255	/bar	GET	200
        2	2016-05-27 17:57:14.843	127.0.0.1	/spam/eggs	POST	500
        3	2016-05-27 17:57:14.850	123.123.123.123	/eggs	DELETE	200
        4	2016-05-27 17:57:14.853	127.0.0.1	/foo	POST	404
        5	2016-05-27 17:57:14.853	10.9.8.7	/bar	GET	200
        6	2016-05-27 17:57:14.857	192.168.1.1	/spam	DELETE	200

    Esses são dados que foram gravados da topologia do Storm.

## Criar um relatório

1. Conecte-se ao [conector do Banco de Dados SQL do Azure](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect) para o Power BI.

2. Dentro de __Bancos de Dados__, selecione __Obter__.

3. Selecione __Banco de Dados SQL do Azure__, e, em seguida, selecione __Conectar__.

4. Insira as informações para se conectar ao seu Banco de Dados SQL do Azure. Você pode encontrá-lo visitando o [Portal do Azure](https://portal.azure.com) e selecionando o Banco de Dados SQL.

    > [AZURE.NOTE] Você também pode definir o intervalo de atualização e filtros personalizados usando __Habilitar Opções Avançadas__ na caixa de diálogo de conexão.

5. Depois de ter se conectado, você verá um novo conjunto de dados com o mesmo nome que o banco de dados ao qual está conectado. Selecione o conjunto de dados para começar a criar um relatório.

3. De __Campos__, expanda a entrada __IISLOGS__. Selecione a caixa de seleção para __URISTEM__. Isso criará um novo relatório que lista os troncos URI (/foo, /bar, etc.) registrados no banco de dados.

    ![Criando um relatório](./media/hdinsight-storm-power-bi-topology/createreport.png)

5. Em seguida, arraste __METHOD__ para o relatório. O relatório será atualizado para listar os troncos e o método HTTP correspondente usado para a solicitação HTTP.

    ![adicionando os dados de método](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

4. Na coluna __Visualizações__, selecione o ícone __Campos__ e selecione a seta para baixo ao lado de __METHOD__ na seção __Valores__. Na lista exibida, selecione __Contagem__. Isso alterará o relatório para listar uma contagem de quantas vezes um URI específico foi acessado.

    ![Mudando para uma contagem de métodos](./media/hdinsight-storm-power-bi-topology/count.png)

6. Em seguida, selecione o __Gráfico de colunas empilhadas__ para alterar como as informações são exibidas.

    ![Mudando para um gráfico empilhado](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

7. Uma vez que o relatório estiver como você deseja, use a entrada __Salvar__ no menu para inserir um nome e salvar o relatório.

## Parar a topologia

A topologia continuará sendo executado até você pará-la ou excluir o Storm no cluster HDInsight. Execute as seguintes etapas para interromper a topologia.

1. No Visual Studio, volte ao visualizador de topologia e selecione a topologia.

2. Selecione o botão **Eliminar** para interromper a topologia.

    ![Botão Eliminar no resumo de topologia](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## Excluir o cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Próximas etapas

Neste documento, você aprendeu como enviar dados de uma topologia do Storm para o Banco de Dados SQL e exibir os dados usando o Power BI. Para obter informações sobre como trabalhar com outras tecnologias do Azure usando o Storm no HDInsight, consulte o seguinte:

* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0601_2016-->