<properties
   pageTitle="Usar o Hadoop Pig com o SSH em um cluster HDInsight | Microsoft Azure"
   description="Saiba como conectar a um cluster Hadoop baseado em Linux com o SSH e use o comando Pig para executar instruções Pig Latin interativamente ou como um trabalho em lotes."
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
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#Executar trabalhos do Pig em um cluster baseado em Linux com o comando Pig (SSH)

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Neste documento, você verá o processo de conexão para um cluster Azure HDInsight baseado em Linux usando SSH (Secure Shell), usando o comando Pig para executar instruções Pig Latin interativamente ou como um trabalho em lotes.

A linguagem de programação Pig Latin permite descrever as transformações que são aplicadas aos dados de entrada para produzir a saída desejada.

> [AZURE.NOTE]Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas é novo no HDInsight, consulte [Dicas do HDInsight baseado em Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster HDInsight baseado em Linux (Hadoop no HDInsight).

* Um cliente SSH. Sistemas operacionais Linux, Unix e Mac devem ser acompanhados de um cliente SSH. Os usuários do Windows devem baixar um cliente, como [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Conexão com o SSH

Conecte-se com o FQDN (nome de domínio totalmente qualificado) do cluster HDInsight usando o comando SSH. O FQDN será o nome que você atribuiu ao cluster, depois será **.azurehdinsight.net**. Por exemplo, o exibido a seguir se conectaria a um cluster chamado **myhdinsight**.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se você tiver fornecido uma chave de certificado para autenticação de SSH** ao criar o cluster HDInsight, talvez seja necessário especificar o local da chave privada no sistema cliente.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se você forneceu uma senha para autenticação SSH** ao criar o cluster HDInsight, você precisará fornecer a senha quando solicitado.

Para obter mais informações sobre como usar o SSH com o HDInsight, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###PuTTY (clientes baseados no Windows)

O Windows não fornece um cliente SSH integrado. É recomendável usar o **PuTTY**, que pode ser baixado de [http://www.chiark.greenend.org.uk/\~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre o uso de PuTTY, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="pig"></a>Usar o comando Pig

2. Uma vez conectado, inicie a CLI (interface de linha de comando) do Pig usando o comando a seguir.

        pig

	Após um momento, você deverá ver um prompt `grunt>`.

3. Insira a instrução a seguir.

		LOGS = LOAD 'wasb:///example/data/sample.log';

	Esse comando carrega o conteúdo do arquivo sample.log em LOGS. Você pode exibir o conteúdo do arquivo usando o seguinte.

		DUMP LOGS;

4. Em seguida, transforme os dados aplicando uma expressão regular para extrair apenas o nível de registro em log de cada registro usando o seguinte.

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

	Você pode usar **DUMP** para exibir os dados após a transformação. Nesse caso, use `DUMP LEVELS;`.

5. Continue a aplicar transformações usando as instruções a seguir. Use `DUMP` para exibir o resultado da transformação após cada etapa.

	<table>
<tr>
<th>Instrução</th><th>O que faz</th>
</tr>
<tr>
<td>FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;</td><td>Remove as linhas que contêm um valor nulo para o nível de log e armazena os resultados em FILTEREDLEVELS.</td>
</tr>
<tr>
<td>GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;</td><td>Agrupa as linhas pelo nível de log e armazena os resultados em GROUPEDLEVELS.</td>
</tr>
<tr>
<td>FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;</td><td>Cria um novo conjunto de dados que contém cada valor de nível de log exclusivo e quantas vezes ele ocorre. Isso é armazenado em FREQUENCIES.</td>
</tr>
<tr>
<td>RESULT = order FREQUENCIES by COUNT desc;</td><td>Ordena os níveis de log por contagem (decrescente) e armazena em RESULT.</td>
</tr>
</table>

6. Você também pode salvar os resultados de uma transformação usando a instrução `STORE`. Por exemplo, o descrito a seguir salva `RESULT` no diretório **/example/data/pigout** no contêiner de armazenamento padrão para seu cluster.

		STORE RESULT into 'wasb:///example/data/pigout'

	> [AZURE.NOTE]Os dados são armazenados no diretório especificado nos arquivos chamados **part-nnnnn**. Se o diretório já existir, você receberá um erro.

7. Para sair do prompt do assistente, insira a instrução a seguir.

		QUIT;

###Arquivos de lote do Pig Latin

Você também pode usar o comando Pig para executar o Pig Latin contido em um arquivo.

3. Depois de sair do prompt do assistente, use o seguinte comando para canalizar STDIN em um arquivo chamado **pigbatch.pig**. Esse arquivo será criado no diretório inicial para a conta que você está conectado na sessão SSH.

		cat > ~/pigbatch.pig

4. Digite ou cole as seguintes linhas e use Ctrl+D quando terminar.

		LOGS = LOAD 'wasb:///example/data/sample.log';
		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;

5. Use o demonstrado a seguir para executar o arquivo **pigbatch.pig** utilizando o comando Pig.

		pig ~/pigbatch.pig

	Quando o trabalho em lotes for concluído, você verá a seguinte saída, que deverá ser igual àquela de quando você usou `DUMP RESULT;` nas etapas anteriores.

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
		(ERROR,6)
		(FATAL,2)

##<a id="summary"></a>Resumo

Como você pode ver, o comando Pig permite executar operações do MapReduce interativamente usando o Pig Latin, bem como executar instruções armazenadas em um arquivo em lotes.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Pig no HDInsight.

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight.

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO8-->