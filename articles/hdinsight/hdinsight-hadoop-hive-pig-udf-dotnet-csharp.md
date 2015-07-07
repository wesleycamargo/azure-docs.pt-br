<properties
	pageTitle="Usar o C# com o Hive e Pig no Hadoop no HDInsight | Microsoft Azure"
	description="Saiba como usar as UDFs (Funções Definidas pelo Usuário) do C# com streaming do Hive e Pig no Azure HDInsight."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/30/2015"
	ms.author="larryfr"/>


#Usar funções definidas pelo usuário do C# com streaming de Hive e Pig no Hadoop no HDInsight 

Hive e Pig são ótimos para trabalhar com dados no Azure HDInsight, mas algumas vezes você precisa de uma linguagem de propósito mais geral. Hive e Pig permitem que você chame código externo por meio de UDFs (funções definidas pelo usuário) ou streaming.

Neste documento, Aprenda a usar C# com Hive e Pig.

##Pré-requisitos

* Windows 7, Windows 8 ou Windows 8.1.

* O Visual Studio com as seguintes versões:

	* O Visual Studio 2012 Professional/Premium/Ultimate com <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">Atualização 4</a>

	* Visual Studio 2013 Community/Professional/Premium/Ultimate com <a href="https://www.microsoft.com/download/details.aspx?id=44921" target="_blank">Atualização 4</a>

	* Visualização do Visual Studio 2015

* Hadoop no cluster HDInsight - consulte [Provisionar um cluster HDInsight](hdinsight-provision-clusters.md) para conhecer as etapas para criar um cluster

* Ferramentas de Hadoop para Visual Studio. Consulte <a href="../hdinsight-hadoop-visual-studio-tools-get-started" target="_blank">Comece a usar as ferramentas do Hadoop do HDInsight para o Visual Studio</a> para obter as etapas para instalar e configurar as ferramentas.

##.NET no HDInsight

O CLR (common language runtime) e estruturas do .NET são instalados por padrão em clusters HDInsight baseados no Windows. Isso permite que você use aplicativos C# com streaming Hive e Pig (os dados são passados entre Hive/Pig e o aplicativo C# via stdout/stdin).

Atualmente não há suporte para executar aplicativos do .NET Framework em clusters HDInsight baseados em Linux.

##.NET e streaming

Streaming envolve a passagem de dados do Hive e do Pig para um aplicativo externo por stdout e o recebimento dos resultados por stdin. Para aplicativos em C#, isso é feito com mais facilidade via `Console.ReadLine()` e `Console.WriteLine()`.

Como Hive e Pig precisam invocar o aplicativo em tempo de execução, o modelo **Aplicativo de Console** deve ser usado para seus projetos em C#.

##Hive e C&#35;

###Criar o projeto em C#

1. Abra o Visual Studio e crie uma nova solução. Para o tipo de projeto, selecione **Aplicativo de Console** e nomeie o novo projeto como **HiveCSharp**.

2. Substitua os conteúdos de **Program.cs** pelo seguinte:

		using System;
		using System.Security.Cryptography;
		using System.Text;
		using System.Threading.Tasks;

		namespace HiveCSharp
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            string line;
		            // Read stdin in a loop
		            while ((line = Console.ReadLine()) != null)
		            {
		                // Parse the string, trimming line feeds
		                // and splitting fields at tabs
		                line = line.TrimEnd('\n');
		                string[] field = line.Split('\t');
		                string phoneLabel = field[1] + ' ' + field[2];
		                // Emit new data to stdout, delimited by tabs
		                Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
		            }
		        }
		        /// <summary>
		        /// Returns an MD5 hash for the given string
		        /// </summary>
		        /// <param name="input">string value</param>
		        /// <returns>an MD5 hash</returns>
		        static string GetMD5Hash(string input)
		        {
		            // Step 1, calculate MD5 hash from input
		            MD5 md5 = System.Security.Cryptography.MD5.Create();
		            byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
		            byte[] hash = md5.ComputeHash(inputBytes);

		            // Step 2, convert byte array to hex string
		            StringBuilder sb = new StringBuilder();
		            for (int i = 0; i < hash.Length; i++)
		            {
		                sb.Append(hash[i].ToString("x2"));
		            }
		            return sb.ToString();
		        }
		    }
		}

3. Compile o projeto.

###Carregar para o armazenamento

1. No Visual Studio, abra **Gerenciador de Servidores**.

3. Expanda **Azure**, então expanda **HDInsight**.

4. Se solicitado, insira suas credenciais de assinatura do Azure e, em seguida, clique em **Entrar**.

5. Expanda o cluster HDInsight que você deseja implantar nesse aplicativo e, em seguida, expanda **Conta de Armazenamento Padrão**.

	![Gerenciador de Servidores mostrando a conta de armazenamento para o cluster](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

6. Clique duas vezes no **Contêiner Padrão** para o cluster. Isso abrirá uma nova janela que exibe o conteúdo do contêiner padrão.

7. Clique no ícone de carregamento e, em seguida, navegue até a pasta **bin\debug** para o projeto **HiveCSharp**. Por fim, selecione o arquivo **HiveCSharp.exe** e clique em **Ok**.

	![ícone de carregamento](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)

8. Quando o carregamento tiver terminado, você poderá usar o aplicativo por meio de uma consulta de Hive.

###Consulta de hive

1. No Visual Studio, abra **Gerenciador de Servidores**.

2. Expanda **Azure**, então expanda **HDInsight**.

5. Clique com o botão direito no cluster em que você implantou o aplicativo **HiveCSharp** e, em seguida, selecione **Escrever uma consulta de Hive**.

6. Use o seguinte para a consulta de Hive:

		add file wasb:///HiveCSharp.exe;

		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		USING 'HiveCSharp.exe' AS
		(clientid string, phoneLabel string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

    Isso seleciona os campos `clientid`, `devicemake` e `devicemodel` de `hivesampletable`, e passa os campos para o aplicativo HiveCSharp.exe. A consulta espera que o aplicativo retorne três campos, que são armazenados como `clientid`, `phoneLabel` e `phoneHash`. A consulta também espera encontrar HiveCSharp.exe na raiz do contêiner de armazenamento padrão (`add file wasb:///HiveCSharp.exe`).

5. Clique em **Enviar** para enviar o trabalho para o cluster HDInsight. A janela **Resumo do trabalho Hive** será aberta.

6. Clique em **Atualizar** para atualizar o resumo até que **Status do trabalho** mude para **Concluído**. Para exibir a saída do trabalho, clique em **Saída do trabalho**.

###Pig e C&#35;

###Criar o projeto em C#

1. Abra o Visual Studio e crie uma nova solução. Para o tipo de projeto, selecione **Aplicativo de Console** e nomeie o novo projeto como **PigUDF**.

2. Substitua o conteúdo do arquivo **Program.cs** pelo seguinte:

		using System;

		namespace PigUDF
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            string line;
				    // Read stdin in a loop
				    while ((line = Console.ReadLine()) != null)
		            {
		                // Fix formatting on lines that begin with an exception
		                if(line.StartsWith("java.lang.Exception"))
		                {
		                    // Trim the error info off the beginning and add a note to the end of the line
		                    line = line.Remove(0, 21) + " - java.lang.Exception";
		                }
		                // Split the fields apart at tab characters
		                string[] field = line.Split('\t');
		                // Put fields back together for writing
		                Console.WriteLine(String.Join("\t",field));
		            }
		        }
		    }
		}

	Esse aplicativo analisará as linhas enviadas do Pig e reformatará as linhas que começam com `java.lang.Exception`.

3. Salve **Program.cs**, e, em seguida, compile o projeto.

###Carregar o aplicativo

1. O streaming do Pig espera que o aplicativo seja local no sistema de arquivos de cluster. Habilite a área de trabalho remota para o cluster HDInsight, então conecte-o seguindo as instruções em <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conectar aos clusters HDInsight usando o RDP</a>.

2. Uma vez conectado, copie **PigUDF.exe** do diretório **bin/debug** para o projeto PigUDF em seu computador local e cole-o no diretório **%PIG_HOME%** no cluster.

###Usar o aplicativo por meio de Pig Latin

1. Na sessão de área de trabalho remota, use o ícone **Linha de comando do Hadoop** na área de trabalho para iniciar a linha de comando do Hadoop.

2. Use o seguinte para iniciar a linha de comando do Pig:

		cd %PIG_HOME%
		bin\pig

	Você verá um prompt `grunt>`.

3. Digite o seguinte para executar um trabalho de Pig simples usando o aplicativo do .NET Framework:

		DEFINE streamer `pigudf.exe` SHIP('pigudf.exe');
		LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
		LOG = FILTER LOGS by LINE is not null;
		DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
		DUMP DETAILS;

	A instrução `DEFINE` cria um alias de `streamer` para os aplicativos pigudf.exe, enquanto `SHIP` distribui esse alias por todos os nós no cluster. Posteriormente, `streamer` é usado com o operador `STREAM` para processar as linhas individuais contidas no LOG e retornar os dados como uma série de colunas.

> [AZURE.NOTE]O nome do aplicativo que é usado para streaming deve estar entre o caractere ` (acento grave) quando se tratar de um alias, e ' (aspa simples) quando usado com `SHIP`.

3. Depois de inserir a última linha, o trabalho deve ser iniciado. Eventualmente, ele retornará uma saída semelhante à seguinte:

		(2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
		(2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
		(2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
		(2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
		(2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

##Resumo

Neste documento, você aprendeu a usar um aplicativo do .NET Framework do Hive e do Pig no HDInsight. Se você quiser aprender como usar o Python com Hive e Pig, consulte [Usar o Python com o Hive e o Pig no HDInsight](hdinsight-python.md).

Para outras maneiras de usar o Pig e o Hive e para saber como usar o MapReduce, consulte o seguinte:

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)

* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)

* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
 

<!---HONumber=62-->