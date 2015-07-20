<properties
   pageTitle="Usar o Pig do Hadoop no HDInsight | Microsoft Azure"
   description="Aprenda a usar o Pig com Hadoop no HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

# Usar o Pig com Hadoop no HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

O [Apache Pig](http://pig.apache.org/) é uma plataforma para criar programas para o Hadoop usando uma linguagem de procedimento conhecida como *Pig Latin*. O Pig é uma alternativa ao Java para a criação de soluções de *MapReduce* e está incluído no Azure HDInsight.

Neste artigo, você aprenderá a usar o Pig com o HDInsight.

##<a id="why"></a>Por que usar o Pig?

Um dos desafios do processamento de dados usando o MapReduce no Hadoop é implementar a lógica de processamento usando somente um mapa e uma função de redução. Em processamentos complexos, é comum você precisar dividir o processamento em várias operações de MapReduce e depois encadeá-las para atingir o resultado desejado.

Em vez de forçá-lo a usar apenas as funções mapear e reduzir, o Pig permite que você defina o processamento como uma série de transformações por onde os dados fluem para produzir a saída desejada.

A linguagem do Pig Latin permite descrever o fluxo de dados a partir de dados brutos, por meio de uma ou mais transformações, para produzir a saída desejada. Os programas em Pig Latin seguem este padrão geral:

- **Carga**: ler dados a serem manipulados no sistema de arquivos
- **Transformação**: manipular os dados
- **Despejo ou armazenamento**: a saída dos dados na tela ou armazenamento para processamento

O Pig Latin também oferece suporte a UDF (funções definidas pelo usuário), que permite que você invoque componentes externos que implementam a lógica que é difícil de modelar no Pig Latin.

Para saber mais sobre o Pig Latin, confira o [Manual de referência do Pig Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) e o [Manual de referência do Pig Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Para obter um exemplo de uso de UDF com o Hive, confira os seguinte documentos:

* [Usando o Python com o Pig e o Hive no HDInsight](hdinsight-python.md)

* [Use o C# com o Hive e o Pig no HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>Sobre o aplicativo de amostra

Este exemplo usa um arquivo de exemplo *log4j*, que fica armazenado em **/example/data/sample.log** no seu contêiner de armazenamento de blob. Cada log dentro do arquivo consiste em uma linha de campos que contém um campo `[LOG LEVEL]` para mostrar o tipo e a gravidade, por exemplo:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

No exemplo anterior, o nível de log é ERRO.

> [AZURE.NOTE]Você também pode gerar um arquivo log4j usando a ferramenta de log [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) e carregá-lo para o blob. Consulte [Carregar dados para o HDInsight](hdinsight-upload-data.md) para obter instruções. Para saber mais sobre como o armazenamento de blob do Azure é usado com o HDInsight, confira [Usar armazenamento de blob do Azure com o HDInsight](../hdinsight-use-blob-storage.md).

Os dados de exemplo ficam no armazenamento de Blob do Azure, que é usado pelo HDInsight como o sistema de arquivos padrão para clusters Hadoop. O HDInsight pode acessar arquivos armazenados no armazenamento blob usando o prefixo **wasb**. Por exenplo, para acessar o arquivo sample.log, você devel usar a seguinte sintaxe:

	wasb:///example/data/sample.log

Como o WASB é o armazenamento padrão para HDInsight, você também pode acessar o arquivo usando **/example/data/sample.log** do Pig Latin.

> [AZURE.NOTE]A sintaxe acima, **wasb:///**, é usada para acessar arquivos armazenados no recipiente de armazenamento padrão do cluster HDInsight. Se você tiver especificado contas de armazenamento adicionais ao provisionar o cluster e quiser acessar arquivos armazenados nessas contas, você pode acessar os dados especificando o nome do contêiner e endereço da conta de armazenamento, por exemplo: **wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.


##<a id="job"></a>Sobre o trabalho de exemplo

O trabalho do Pig Latin a seguir carrega o arquivo **sample.log** do armazenamento padrão para o cluster HDInsight. Em seguida, ele executa uma série de transformações que resultam na contagem de quantas vezes cada nível de log ocorreu nos dados de entrada. Os resultados são despejados no STDOUT.

	LOGS = LOAD 'wasb:///example/data/sample.log';
	LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
	FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
	GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
	FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
	RESULT = order FREQUENCIES by COUNT desc;
	DUMP RESULT;

A imagem a seguir mostra uma análise do que cada transformação faz aos dados.

![Representação gráfica das transformações][image-hdi-pig-data-transformation]

##<a id="run"></a>Executar o trabalho do Pig Latin

O HDInsight pode executar trabalhos de Pig Latin usando vários métodos. Use a tabela a seguir para decidir qual método é o melhor para você e siga o link para obter o passo-a-passo.

| **Use** se quiser... | ...um shell **interativo** | ...processamento em **lotes** | ... com esse **sistema operacional de cluster** | ... desse **sistema operacional cliente** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X ou Windows |
| [Curl](hdinsight-hadoop-use-pig-curl.md) | &nbsp; | ✔ | Linux ou Windows | Linux, Unix, Mac OS X ou Windows |
| [SDK .NET para Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) | &nbsp; | ✔ | Linux ou Windows | Windows (por enquanto) |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md) | &nbsp; | ✔ | Linux ou Windows | Windows |
| [Área de Trabalho Remota](hdinsight-hadoop-use-pig-remote-desktop.md) | ✔ | ✔ | Windows | Windows |

##<a id="nextsteps"></a>Próximas etapas

Agora que você aprendeu a usar o Pig com o HDInsight, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar trabalhos do MapReduce com o HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../install-configure-powershell.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png

<!---HONumber=July15_HO2-->