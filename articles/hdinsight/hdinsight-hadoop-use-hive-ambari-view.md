<properties
   pageTitle="Usar os Modos de Exibição do Ambari para trabalhar com o Hive no HDInsight (Hadoop) | Microsoft Azure"
   description="Saiba como usar o Modo de Exibição do Hive em seu navegador da Web para enviar consultas do Hive. O Modo de exibição do Hive faz parte da Interface de Usuário da Web do Ambari fornecida com o cluster HDInsight baseado em Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/20/2016"
   ms.author="larryfr"/>

#Use o Modo de Exibição do Hive com o Hadoop no HDInsight.

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

O Ambari é um utilitário de monitoramento e de gerenciamento fornecido com clusters HDInsight baseados em Linux. Um dos recursos fornecidos pelo Ambari é uma interface de usuário da Web que pode ser usada para executar consultas do Hive. É o __Modo de Exibição do Hive__, parte dos Modos de exibição do Ambari fornecidos com o cluster HDInsight.

> [AZURE.NOTE] O Ambari tem vários recursos que não serão discutidos neste documento. Para saber mais, confira [Gerenciar clusters HDInsight usando a interface do usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md).

##Pré-requisitos

- Criar um cluster HDInsight baseado em Linux. Para saber mais sobre como criar um novo cluster, confira [Introdução ao HDInsight baseado no Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

##Abrir o modo de exibição Hive

Você pode abrir os Modos de exibição do Ambari do Portal do Azure; selecione seu cluster HDInsight e escolha __Modos de exibição do Ambari__ na seção __Links Rápidos__.

![seção links rápidos](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

Você também pode navegar diretamente para o Ambari indo para https://CLUSTERNAME.azurehdinsight.net em um navegador da Web (em que __NOMEDOCLUSTER__ é o nome do cluster HDInsight) e escolhendo o conjunto de quadrados no menu da página (ao lado do link __Admin__ e do botão no lado esquerdo da página) para listar os modos de exibição disponíveis. Escolha o __modo de exibição do Hive__.

![Escolhendo modos de exibição do Ambari](./media/hdinsight-hadoop-use-hive-ambari-view/selecthiveview.png).

> [AZURE.NOTE] Ao acessar o Ambari, você receberá uma solicitação para se autenticar no site. Insira o nome da conta e senha de administrador (o padrão é `admin`) que você usou ao criar o cluster.

Você verá uma página semelhante à seguinte:

![Imagem da página de modo de exibição do Hive, que contém uma seção de editor de consultas](./media/hdinsight-hadoop-use-hive-ambari-view/hiveview.png)

##Exibir tabelas

Na seção __Gerenciador de Banco de Dados__ da página, escolha a entrada __Padrão__ na guia __Bancos de Dados__. Isso exibirá uma lista de tabelas no banco de dados padrão. Deve haver apenas uma tabela em um novo cluster HDInsight: __hivesampletable__.

![gerenciador de banco de dados com o banco de dados padrão expandido](./media/hdinsight-hadoop-use-hive-ambari-view/databaseexplorer.png)

À medida que novas tabelas forem adicionadas nas etapas deste documento, você poderá usar o ícone de atualização no canto superior direito do Gerenciador de Banco de Dados para atualizar a lista de tabelas disponíveis.

##<a name="hivequery"></a>Editor de consultas

Use as etapas a seguir no modo de exibição do Hive para executar uma consulta do Hive em relação aos dados incluídos no cluster.

1. Na seção __Editor de Consultas__ da página, cole as seguintes instruções HiveQL na planilha:

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	Essas instruções executam as seguintes ações:

	- **DROP TABLE** - exclui a tabela e o arquivo de dados caso a tabela já exista.
	- **CREATE EXTERNAL TABLE**: cria uma nova tabela "externa" no Hive. As tabelas externas armazenam apenas a definição da tabela no Hive; os dados são deixados no local original.
	- **ROW FORMAT** - informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
	- **STORED AS TEXTFILE LOCATION** - informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto.
	- **SELECT** - Seleciona uma contagem de todas as linhas em que a coluna t4 contém o valor [ERROR].

	>[AZURE.NOTE] Use tabelas externas se os dados subjacentes tiverem que ser atualizados por uma origem externa, como um processo automatizado de carregamento de dados, ou por outra operação MapReduce, mas você quiser que as consultas Hive sempre usem os dados mais recentes. Remover uma tabela externa *não* exclui os dados, somente a definição de tabela.

2. Use o botão __Executar__ na parte inferior do Editor de Consultas para iniciar a consulta. Ele deverá ficar laranja e o texto será alterado para __Parar a execução__. Uma seção __Resultados do Processo de Consulta__ deve aparecer abaixo do Editor de Consultas e exibir informações sobre o trabalho.

    > [AZURE.IMPORTANT] Alguns navegadores podem não atualizar corretamente o log ou as informações dos resultados. Se você executar um trabalho e parecer que ele não cessa a execução sem atualizar o log ou retornar resultados, em vez disso, experimente usar o Mozilla FireFox ou o Google Chrome.

3. Depois que a consulta for concluída, a seção __Resultados do Processo de Consulta__ exibirá os resultados da operação. O botão __Parar a execução__ também se transformará em um botão verde __Executar__. A guia __Resultados__ deve conter as seguintes informações:

        sev       cnt
        [ERROR]   3

    A guia __Logs__ pode ser usada para exibir as informações de log criadas pelo trabalho. Você pode usar isso na solução de problemas, caso haja problemas com uma consulta.

    > [AZURE.TIP] Observe a caixa de diálogo suspensa __Salvar resultados__ no canto superior esquerdo da seção __Resultados do Processo de Consulta__. Você pode usá-la para baixar os resultados ou salvá-los no armazenamento do HDInsight como um arquivo CSV.

3. Selecione as quatro primeiras linhas dessa consulta e escolha __Executar__. Observe que não há resultados quando o trabalho é concluído. Isso ocorre porque, com o uso do botão __Executar__ quando parte da consulta é selecionada, apenas as instruções escolhidas são executadas. Nesse caso, a seleção não incluiu a instrução final que recupera linhas da tabela. Se escolher apenas essa linha e usar __Executar__, você verá os resultados esperados.

3. Use o botão __Nova Planilha__ na parte inferior do __Editor de Consultas__ para criar uma nova planilha. Na nova planilha, digite as seguintes instruções HiveQL:

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

	Essas instruções executam as seguintes ações:

	- **CREATE TABLE IF NOT EXISTS** - cria uma tabela, se ela ainda não existir. Como a palavra-chave **EXTERNAL** não é usada, esta é uma tabela interna, que é armazenada no data warehouse do Hive e totalmente gerenciada pelo Hive. Ao contrário das tabelas externas, remover uma tabela interna excluirá também os dados subjacentes.
	- **STORES AS ORC**: armazena os dados no formato ORC (Optimized Row Columnar). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.
	- **INSERT OVERWRITE ... SELECT** - Seleciona linhas da tabela **log4jLogs** que contêm [ERROR] e insere os dados na tabela **errorLogs**.

    Use o botão __Executar__ para executar essa consulta. A guia __Resultados__ não conterá informações, pois nenhuma linha é retornada por essa consulta, mas o status deverá ser mostrado como __ÊXITO__.

###Configurações do Hive

Escolha o ícone __Configurações__ à direita do editor.

![ícones](./media/hdinsight-hadoop-use-hive-ambari-view/settings.png)

As configurações podem ser usadas para alterar várias configurações do Hive, por exemplo, alterar o mecanismo de execução do Hive de Tez (padrão) para MapReduce.

###Explicação visual

Escolha o ícone __Explicação Visual__ à direita do editor.

![ícones](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplainicon.png)

Esse é o modo de exibição __Explicação Visual__ da consulta, que pode ser útil na compreensão do fluxo de consultas complexas. Você pode exibir um equivalente textual desse modo de exibição usando o botão __Explicar__ no Editor de Consultas.

![imagem de explicação visual](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

###Tez

Escolha o ícone de __Tez__ à direita do editor.

![ícones](./media/hdinsight-hadoop-use-hive-ambari-view/tez.png)

Isso exibirá o DAG (Gráfico acíclico dirigido) usado pelo Tez para essa consulta, se houver uma disponível. Se você quiser exibir o DAG de consultas executadas anteriormente ou depurar o processo do Tez, use [Modo de Exibição do Tez exibição](hdinsight-debug-ambari-tez-view.md).

###Notificações

Escolha o ícone de __Notificações__ à direita do editor.

![ícones](./media/hdinsight-hadoop-use-hive-ambari-view/notifications.png)

Notificações são mensagens geradas durante a execução de consultas. Por exemplo, você receberá uma notificação quando uma consulta for enviada ou quando ocorrer um erro.

##Consultas salvas

1. No Editor de Consultas, crie uma nova planilha e insira a seguinte consulta:

        SELECT * from errorLogs;

    Execute a consulta para verificar se funciona. Os resultados serão os seguintes:

        errorlogs.t1 	errorlogs.t2 	errorlogs.t3 	errorlogs.t4 	errorlogs.t5 	errorlogs.t6 	errorlogs.t7
        2012-02-03 	18:35:34 	SampleClass0 	[ERROR] 	incorrect 	id 	
        2012-02-03 	18:55:54 	SampleClass1 	[ERROR] 	incorrect 	id 	
        2012-02-03 	19:25:27 	SampleClass4 	[ERROR] 	incorrect 	id

2. Clique no botão __Salvar como__ na parte inferior do editor. Nomeie essa consulta como __Errorlogs__ e marque __OK__. Observe que o nome da planilha é alterado para __Errorlogs__.

3. Selecione a guia __Consultas Salvas__ na parte superior da página Modo de Exibição do Hive. Observe agora que __Errorlogs__ está listado como uma consulta salva. Ele permanecerá na lista até que você o remova. A seleção do nome abrirá a consulta no Editor de Consultas.

##Histórico de consultas

O botão __Histórico__ na parte superior do Modo de Exibição do Hive permite que você exiba consultas executadas anteriormente. Use-o agora e selecione algumas consultas executadas anteriormente. Quando você seleciona uma consulta, ela é aberta no Editor de Consultas.

##UDF (Funções definidas pelo usuário)

O Hive também pode ser estendido por meio de **UDF (funções definidas pelo usuário)**. As UDF permitem que você implemente funcionalidade ou lógica que não é facilmente modelada em HiveQL.

Embora você possa adicionar uma UDF como parte das instruções HiveQL em sua consulta, a guia UDF na parte superior do Modo de Exibição do Hive permite que você declare e salve um conjunto de UDFs que podem ser usadas com o __Editor de Consultas__.

Depois de adicionar uma UDF ao Modo de Exibição do Hive, um botão __Inserir udfs__ será exibido na parte inferior do __Editor de Consultas__. Ao selecionar esse botão, uma lista suspensa de UDFs definidas no Modo de Exibição do Hive será exibida. A seleção de uma UDF adicionará instruções HiveQL à sua consulta para habilitar a UDF.

Por exemplo, se você tiver definido uma UDF com as seguintes propriedades:

* Nome de recurso: myudfs
* Caminho do recurso: wasbs:///myudfs.jar
* Nome da UDF: myawesomeudf
* Nome de classe da UDF: com.myudfs.Awesome

O botão __Inserir udfs__ exibirá uma entrada denominada __myudfs__, com outra lista suspensa para cada UDF definida para esse recurso. Nesse caso, __myawesomeudf__. A seleção dessa entrada adicionará o seguinte ao início da consulta:

    add jar wasbs:///myudfs.jar;

    create temporary function myawesomeudf as 'com.myudfs.Awesome';

Você pode usar a UDF em sua consulta. Por exemplo: `SELECT myawesomeudf(name) FROM people;`.

Para saber mais sobre como usar UDFs com Hive no HDInsight, consulte o seguinte:

* [Usando o Python com o Hive e com o Pig no HDInsight](hdinsight-python.md)

* [Como adicionar UDF personalizadas do Hive no HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

##<a id="nextsteps"></a>Próximas etapas

Para informações gerais sobre o Hive no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0727_2016-->