<properties urlDisplayName="Analyze realt-time Twitter sentiment with Hbase in HDInsight" pageTitle="Analisar sentimento no Twitter em tempo real com HBase no HDInsight | Azure" metaKeywords="" description="Learn how to do real-time analysis of big data using HBase in an HDInsight (Hadoop) cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze real-time Twitter sentiment with HBase in HDInsight" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# Analisar sentimento no Twitter em tempo real com HBase no HDInsight.

Saiba como fazer [análise de sentimento](http://en.wikipedia.org/wiki/Sentiment_analysis) em tempo real de big data usando o HBase em um cluster HDInsight (Hadoop).


Sites sociais são uma das forças principais para adoção de Big Data. APIs públicas fornecidas por sites, como o Twitter, são uma fonte útil de dados para analisar e compreender as tendências populares. Neste tutorial, você desenvolverá um aplicativo de serviço de streaming do console e um aplicativo da web ASP.NET para realizar o seguinte:

![][img-app-arch]

- Obtenha os tweets marcados geograficamente em tempo real, usando o API de streaming do Twitter.
- Avalie o sentimento desses tweets.
- Armazene as informações do sentimento no HBase usando o Microsoft HBase SDK.
- Plote o resultados estatísticos em tempo real nos mapas Bing usando um aplicativo da web ASP.NET. Uma visualização dos tweets terá a seguinte aparência:

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
	
	Você será capaz de consultar tweets com certas palavras-chave para obter um sentido da opinião expressa em tweets em positivo, negativo ou neutro.

Um exemplo completo de solução do Visual Studio pode ser encontrado em [https://github.com/maxluk/tweet-sentiment](https://github.com/maxluk/tweet-sentiment).






























##Neste artigo

- [Pré-requisitos](#prerequisites)
- [Criar um aplicativo do Twitter](#twitter)
- [Criar um serviço de streaming do Twitter único](#streaming)
- [Criar um site do Azure para visualizar o sentimento do Twitter](#web)
- [Próximas etapas](#nextsteps)

##<a id="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

- **Um cluster HBase no HDInsight**. Para obter instruções sobre a provisão do cluster, consulte [Introdução ao uso do HBase com o Hadoop no HDInsight][hBase-get-started]. Você precisará dos seguintes dados para percorrer o tutorial:

	<table border="1">
	<tr><th>Propriedade do cluster</th><th>Descrição</th></tr>
	<tr><td>Nome do cluster do HBase</td><td>Este é o nome do cluster HBase HDInsight. Por exemplo: https://myhbase.azurehdinsight.net/</td></tr>
	<tr><td>Nome de usuário do cluster</td><td>O nome da conta do usuário Hadoop. O nome de usuário Hadoop padrão é <strong>admin</strong>.</td></tr>
	<tr><td>Senha de usuário do cluster</td><td>A senha de usuário do cluster Hadoop.</td></tr>
	</table>

- **Uma estação de trabalho** com Visual Studio 2013 instalado. Para obter instruções, consulte [Instalando o Visual Studio](http://msdn.microsoft.com/pt-br/library/e2h7fzkw.aspx).





##<a id="twitter"></a>Criar ID e segredos do aplicativo Twitter

O uso de APIs de Streaming do Twitter [OAuth](http://oauth.net/) para autorizar solicitações. 

A primeira etapa para usar OAuth é criar um novo aplicativo no site do desenvolvedor do Twitter.

**Para criar ID e segredos do aplicativo Twiiter:**

1. Entre no [https://apps.twitter.com/](https://apps.twitter.com/). Clique no link **Inscreva-se agora** se você não tiver uma conta do Twitter.
2. Clique em **Criar Novo Aplicativo**.
3. Digite o **Nome**, a **Descrição** e o **Site**. O campo site da Web na verdade não é usado. Ele não precisa ser uma URL válida. A tabela a seguir mostra alguns valores de exemplo para usar:

	<table border="1">
	<tr><th>Campo</th><th>Valor</th></tr>
	<tr><td>Nome</td><td>MyHDInsightHBaseApp</td></tr>
	<tr><td>Descrição</td><td>MyHDInsightHBaseApp</td></tr>
	<tr><td>Site</td><td>http://www.myhdinsighthbaseapp.com</td></tr>
	</table>

4. Marque **Sim, eu concordo** e, em seguida, clique em **Criar seu aplicativo do Twitter**.
5. Clique na guia **Permissões**. A permissão padrão é **Somente leitura**. Isso é suficiente para este tutorial. 
6. Clique na guia **Chaves de API**.
7. Clique em **Criar meu token de acesso**.
8. Clique em **OAuth de teste** no canto superior direito da página.
9. Anote a **chave API**, o **segredo do API**, o **Token de acesso** e **Segredo do token de acesso**. Você precisará dos valores mais tarde no tutorial.

	![hdi.hbase.twitter.sentiment.twitter.app][img-twitter-app]






























##<a id="streaming"></a> Criar um serviço de streaming do Twitter único

Crie um aplicativo de console para obter tweets, calcular pontuação de sentimento de tweet e enviar palavras de tweet processadas para o HBase.

**Para criar a  solução do Visual Studio:**

1. Abra o **Visual Studio**.
2. No menu **Arquivo**, aponte para **Novo** e clique em **Projeto**.
3. Digite ou selecione os valores a seguir:

	- Modelos: **Visual C#**
	- Modelo: **Aplicativo de console**
	- Nome: **TweetSentimentStreaming** 
	- Local: **C:\Tutorials**
	- Nome da solução: **TweetSentimentStreaming**

4. Clique em **OK** para continuar.
 


**Para instalar os pacotes Nuget e adicionar referências SDK:**

1. No menu **Ferramentas**, clique em **Gerenciador de Pacotes NuGet** e, em seguida, clique em **Console do Gerenciador de Pacotes**. O painel do console abrirá na parte interior da página.
2. Use os seguintes comandos para instalar o pacote [Tweetinvi](https://www.nuget.org/packages/TweetinviAPI/) , que é usado para acessar a API do Twitter, e o pacote [Protobuf-net](https://www.nuget.org/packages/protobuf-net/) , que é usado para serializar e desserializar objetos.

		Install-Package TweetinviAPI
		Install-Package protobuf-net 

	> [WACOM.NOTE] O pacote Nuget do Microsoft Hbase SDK não está disponível a partir de 26 de agosto de 2014. O repositório Github está [https://github.com/hdinsight/hbase-sdk-for-net](https://github.com/hdinsight/hbase-sdk-for-net). Até que o SDK esteja disponível, você deve criar o dll sozinho. Para obter instruções, consulte [Introdução ao uso do HBase com o Hadoop no HDInsight][hdinsight-hbase-get-started].

3. Em ** Gerenciador de Soluções**, clique com o botão direito do mouse em ** Referências** e, em seguida, clique em ** Adicionar Referência**.
4. No painel esquerdo, expanda **Assemblies** e, em seguida, clique em **Framework**.
5. No painel direito, selecione a caixa de diálogo na parte frontal do **System.Configuration** e, em seguida, clique em **OK**.



**Para definir a classe de serviço do streaming de tweeter:**

1. Do **Gerenciador de Soluções**, clique com o botão direito do mouse em **TweetSentimentStreaming**, aponte para **Adicionar** e, em seguida, clique em **Classe**.
2. Em **Nome**, digite **HBaseWriter** e, em seguida, clique em **Adicionar**.
3. Em **HBaseWriter.cs**, adicione o seguinte usando as instruções na parte superior do arquivo:

		using System.IO;		
		using System.Threading;
		using System.Globalization;
		using Microsoft.HBase.Client;
		using Tweetinvi.Core.Interfaces;
		using org.apache.hadoop.hbase.rest.protobuf.generated;

4. Dentro de **HbaseWriter.cs**, adicione uma nova chamada de classe **DictionaryItem**:

	    classe pública DictionaryItem
	    {
	        public string Type { get; set; }
	        public int Length { get; set; }
	        public string Word { get; set; }
	        public string Pos { get; set; }
	        public string Stemmed { get; set; }
	        public string Polarity { get; set; }
	    }

	Essa estrutura de classe é usada para analisar o arquivo do dicionário de sentimento. Os dados são usados para calcular a pontuação de sentimento para cada tweet.

5. Dentro da classe de **HBaseWriter**, defina as seguintes constantes e variáveis:

        // Informações da tabela de cluster do HDinsight HBase e HBase
        const string CLUSTERNAME = "https://<HBaseClusterName>.azurehdinsight.net/";
        const string HADOOPUSERNAME = "<HadoopUserName>"; //the default name is "admin"
        const string HADOOPUSERPASSWORD = "<HaddopUserPassword>";
        const string HBASETABLENAME = "tweets_by_words";

        // Arquivo de dicionário de sentimento e caracteres de pontuação
        const string DICTIONARYFILENAME = @"..\..\data\dictionary\dictionary.tsv";
        private static char[] _punctuationChars = new[] { 
            ' ', '!', '\"', '#', '$', '%', '&', '\'', '(', ')', '*', '+', ',', '-', '.', '/',   //ascii 23--47
            ':', ';', '<', '=', '>', '?', '@', '[', ']', '^', '_', '`', '{', '|', '}', '~' };   //ascii 58--64 + misc.

        // Para gravar no HBase
        HBaseClient client;

        // um dicionário de sentimento para estimar sentimento. Ele é carregado de um arquivo físico.
        Dictionary<string, DictionaryItem> dictionary;
        
        // usar gravação multithread
        Thread writerThread;
        Queue<ITweet> queue = new Queue<ITweet>();
        bool threadRunning = true;

6. Defina os valores de constantes, incluindo **&lt;HBaseClusterName>**, **&lt;HadoopUserName>** e **&lt;HaddopUserPassword>**. Se quiser mudar o nome da tabela HBase, você deve mudar o nome da tabela no aplicativo Web correspondente.

	Você baixará e moverá o arquivo dictionary.tsv para uma pasta específica posteriormente no tutorial.

7. Defina as seguintes funções dentro da classe **HBaseWriter**:

		// Esta função se conecta ao HBase, carrega o dicionário de sentimento e inicia o thread para gravação.
        public HBaseWriter()
        {
            ClusterCredentials credentials = new ClusterCredentials(new Uri(CLUSTERNAME), HADOOPUSERNAME, HADOOPUSERPASSWORD);
            client = new HBaseClient(credentials);

            // criar a tabela HBase se ela não existir
            if (!client.ListTables().name.Contains(HBASETABLENAME))
            {
                TableSchema tableSchema = new TableSchema();
                tableSchema.name = HBASETABLENAME;
                tableSchema.columns.Add(new ColumnSchema { name = "d" });
                client.CreateTable(tableSchema);
                Console.WriteLine("Table \"{0}\" is created.", HBASETABLENAME);
            }

            // Carregar o dicionário de sentimento de um arquivo
            LoadDictionary();

			// Iniciar um thread para gravar no HBase
            writerThread = new Thread(new ThreadStart(WriterThreadFunction));
            writerThread.Start();
        }

        ~HBaseWriter()
        {
            threadRunning = false;
        }

        // Enfileirar os Tweets recebidos
        public void WriteTweet(ITweet tweet)
        {
            lock (queue)
            {
                queue.Enqueue(tweet);
            }
        }

        // Carregar o dicionário de sentimento de um arquivo
        private void LoadDictionary()
        {
            List<string> lines = File.ReadAllLines(DICTIONARYFILENAME).ToList();
            var items = lines.Select(line =>
            {
                var fields = line.Split('\t');
                var pos = 0;
                return new DictionaryItem
                {
                    Type = fields[pos++],
                    Length = Convert.ToInt32(fields[pos++]),
                    Word = fields[pos++],
                    Pos = fields[pos++],
                    Stemmed = fields[pos++],
                    Polarity = fields[pos++]
                };
            });

            dictionary = new Dictionary<string, DictionaryItem>();
            foreach (var item in items)
            {
                if (!dictionary.Keys.Contains(item.Word))
                {
                    dictionary.Add(item.Word, item);
                }
            }
        }

        // Calcular a pontuação de sentimento
        private int CalcSentimentScore(string[] words)
        {
            Int32 total = 0;
            foreach (string word in words)
            {
                if (dictionary.Keys.Contains(word))
                {
                    switch (dictionary[word].Polarity)
                    {
                        case "negative": total -= 1; break;
                        case "positive": total += 1; break;
                    }
                }
            }
            if (total > 0)
            {
                return 1;
            }
            else if (total < 0)
            {
                return -1;
            }
            else
            {
                return 0;
            }
        }

		// Popular um objeto CellSet a ser gravado no HBase
        private void CreateTweetByWordsCells(CellSet set, ITweet tweet)
        {
            // Dividir o Tweet em palavras
            string[] words = tweet.Text.ToLower().Split(_punctuationChars);

            // Calcular a pontuação de sentimento com base nas palavras
            int sentimentScore = CalcSentimentScore(words);
            var word_pairs = words.Take(words.Length - 1)
                                  .Select((word, idx) => string.Format("{0} {1}", word, words[idx + 1]));
            var all_words = words.Concat(word_pairs).ToList();

            // Para cada palavra contida Tweet, adicione uma linha à tabela HBase
            foreach (string word in all_words)
            {
                string time_index = (ulong.MaxValue - (ulong)tweet.CreatedAt.ToBinary()).ToString().PadLeft(20) + tweet.IdStr;
                string key = word + "_" + time_index;

                // Criar uma linha
                var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };

                // Adicionar colunas à linha, incluindo o identificador, idioma, coordenador (se disponível) e sentimento do Tweet 
                var value = new Cell { column = Encoding.UTF8.GetBytes("d:id_str"), data = Encoding.UTF8.GetBytes(tweet.IdStr) };
                row.values.Add(value);
                
                value = new Cell { column = Encoding.UTF8.GetBytes("d:lang"), data = Encoding.UTF8.GetBytes(tweet.Language.ToString()) };
                row.values.Add(value);
                
                if (tweet.Coordinates != null)
                {
                    var str = tweet.Coordinates.Longitude.ToString() + "," + tweet.Coordinates.Latitude.ToString();
                    value = new Cell { column = Encoding.UTF8.GetBytes("d:coor"), data = Encoding.UTF8.GetBytes(str) };
                    row.values.Add(value);
                }

                value = new Cell { column = Encoding.UTF8.GetBytes("d:sentiment"), data = Encoding.UTF8.GetBytes(sentimentScore.ToString()) };
                row.values.Add(value);

                set.rows.Add(row);
            }
        }

        // Gravar um Tweet (CellSet) no HBase
        public void WriterThreadFunction()
        {
            try
            {
                while (threadRunning)
                {
                    if (queue.Count > 0)
                    {
                        CellSet set = new CellSet();
                        lock (queue)
                        {
                            do
                            {
                                ITweet tweet = queue.Dequeue();
                                CreateTweetByWordsCells(set, tweet);
                            } while (queue.Count > 0);
                        }

                        // Gravar o Tweet por célula de palavras definida na tabela HBase
                        client.StoreCells(HBASETABLENAME, set);
                        Console.WriteLine("\tRows written: {0}", set.rows.Count);
                    }
                    Thread.Sleep(100);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Exception: " + ex.Message);
            }
        }

	O código fornece a seguinte funcionalidade:

	- **Conectar-se ao Hbase [ HBaseWriter() ]**: Use o HBase SDK para criar um objeto *ClusterCredentials* com a URL do cluster e a credencial do usuário Hadoop e, em seguida, crie um objeto *HBaseClient* usando o objeto ClusterCredentials.
	- **Criar tabela HBase [ HBaseWriter() ]**: A chamada do método é *HBaseClient.CreateTable()*.
	- **Gravar para tabela HBase [ WriterThreadFunction() ]**: A chamada do método é *HBaseClient.StoreCells()*.

**Para concluir o Program.cs:**

1. No **Gerenciador de Soluções**, clique duas vezes em **Program.cs** para abri-lo.
2. No início do arquivo, adicione as seguintes instruções de uso:

		using System.Configuration;
		using System.Diagnostics;
		using Tweetinvi;

3. Dentro da classe de **Programa**, defina as seguintes constantes:

        const string TWITTERAPPACCESSTOKEN = "<TwitterApplicationAccessToken";
        const string TWITTERAPPACCESSTOKENSECRET = "TwitterApplicationAccessTokenSecret";
        const string TWITTERAPPAPIKEY = "TwitterApplicationAPIKey";
        const string TWITTERAPPAPISECRET = "TwitterApplicationAPISecret";

4. Defina os valores constantes para combinar seus valores de aplicativo do Twitter.

3. Modifique a função **Main()**, para que ela se assemelhe como:

		static void Main(string[] args)
		{
            TwitterCredentials.SetCredentials(TWITTERAPPACCESSTOKEN, TWITTERAPPACCESSTOKENSECRET, TWITTERAPPAPIKEY, TWITTERAPPAPISECRET);

            Stream_FilteredStreamExample();
		}

4. Adicione a seguinte função à classe:

        private static void Stream_FilteredStreamExample()
        {
            for (; ; )
            {
                try
                {
                    HBaseWriter hbase = new HBaseWriter();
                    var stream = Stream.CreateFilteredStream();
                    stream.AddLocation(Geo.GenerateLocation(-180, -90, 180, 90));

                    var tweetCount = 0;
                    var timer = Stopwatch.StartNew();

                    stream.MatchingTweetReceived += (sender, args) =>
                    {
                        tweetCount++;
                        var tweet = args.Tweet;

                        
// Gravar Tweets no HBase
                        hbase.WriteTweet(tweet);

                        if (timer.ElapsedMilliseconds > 1000)
                        {
                            if (tweet.Coordinates != null)
                            {
                                Console.ForegroundColor = ConsoleColor.Green;
                                Console.WriteLine("\n{0}: {1} {2}", tweet.Id, tweet.Language.ToString(), tweet.Text);
                                Console.ForegroundColor = ConsoleColor.White;
                                Console.WriteLine("\tLocation: {0}, {1}", tweet.Coordinates.Longitude, tweet.Coordinates.Latitude);
                            }

                            timer.Restart();
                            Console.WriteLine("\tTweets/sec: {0}", tweetCount);
                            tweetCount = 0;
                        }
                    };

                    stream.StartStreamMatchingAllConditions();
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Exception: {0}", ex.Message);
                }
            }
        }

**Para baixar o arquivo do dicionário de sentimento:**

1. Navegue até [https://github.com/maxluk/tweet-sentiment](https://github.com/maxluk/tweet-sentiment).
2. Clique em **Baixar ZIP**.
3. Extraia o arquivo localmente.
4. Copie o arquivo de **../tweet-sentiment/SimpleStreamingService/data/dictionary/dictionary.tsv**.
5. Cole o arquivo para sua solução em **TweetSentimentStreaming/TweetSentimentStreaming/data/dictionary/dictionary.tsv**.

**Para executar o serviço de streaming:**

1. A partir do Visual Studio, pressione **F5**. Esta é a captura de tela do aplicativo do console:

	![hdinsight.hbase.twitter.sentiment.streaming.service][img-streaming-service]
2. Mantenha o aplicativo de console de streaming em execução enquanto desenvolve o aplicativo de web, e então você tem mais dados para usar.



























##<a id="web"></a> Criar um site do Azure para visualizar o sentimento do Twitter

Nesta seção, você criará um aplicativo da web ASP.NET MVC para ler os dados de sentimento em tempo real do HBase e plotar os dados nos mapas Bing.

**Para criar um aplicativo da web ASP.NET MVC:**

1. Abra o Visual Studio.
2. Clique em **Arquivo**, em **Novo** e, em seguida, clique em **Projeto**.
3. Digite ou insira o seguinte:

	- Categoria do modelo: **Visual C#/Web**
	- Modelo: **Aplicativo Web ASP.NET**
	- Nome: **TweetSentimentWeb**
	- Local: **C:\Tutorials** 
4. Clique em **OK**.
5. Em **Selecionar um modelo**, clique em **MVC**. 
6. No **Microsoft Azure**, clique em **Gerenciar Assinaturas**.
7. De **Gerenciar Assinaturas do Microsoft Azure**, clique em **Entrar**.
8. Insira sua credencial Azure. Suas informações de assinatura do Azure serão mostradas na guia Contas.
9. Clique em **Fechar** para fechar a janela de Gerenciar Assinaturas do Microsoft Azure.
10. De **Novo Projeto ASP.NET - TweetSentimentWeb**, clique em **OK**.
11. De **Configurar Definições do Site do Microsoft Azure**, selecione a **Região** que está mais próxima de você. Não é necessário especificar um servidor do banco de dados. 
12. Clique em **OK**.

**Para instalar os pacotes Nuget:**

1. No menu **Ferramentas**, clique em **Gerenciador de Pacotes NuGet** e, em seguida, clique em **Console do Gerenciador de Pacotes**. O painel do console é aberto na parte interior da página.
2. Use o seguinte comando para instalar o pacote [Protobuf-net](https://www.nuget.org/packages/protobuf-net/) , que é usado para serializar e desserializar objetos.

		Install-Package protobuf-net 

	> [WACOM.NOTE] O pacote Nuget do Microsoft Hbase SDK não está disponível a partir de 20 de agosto de 2014. O repositório Github está [https://github.com/hdinsight/hbase-sdk-for-net](https://github.com/hdinsight/hbase-sdk-for-net). Até que o SDK esteja disponível, você deve criar o dll sozinho. Para obter instruções, consulte [Introdução ao uso do HBase com o Hadoop no HDInsight][hdinsight-hbase-get-started].

**Para adicionar a classe HBaseReader:**

1. A partir do **Gerenciador de Soluções**, expanda **TweetSentiment**.
2. Clique com o botão direito do mouse em **Modelos**, clique em **Adicionar** e, em seguida, clique em **Classe**.
3. No Nome, digite **HBaseReader.cs** e, em seguida, clique em **Adicionar**.
4. Substitua o código pelo seguinte código:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		
		using System.Configuration;
		using System.Threading.Tasks;
		using System.Text;
		using Microsoft.HBase.Client;
		using org.apache.hadoop.hbase.rest.protobuf.generated;
		
		namespace TweetSentimentWeb.Models
		{
		    public class HBaseReader
		    {
		        // Para ler dados de sentimento Tweet do HDInsight HBase
		        HBaseClient client;
		
		        // Informações da tabela de cluster do HDinsight HBase e HBase
		        const string CLUSTERNAME = "<HBaseClusterName>";
		        const string HADOOPUSERNAME = "<HBaseClusterHadoopUserName>"
		        const string HADOOPUSERPASSWORD = "<HBaseCluserUserPassword>";
		        const string HBASETABLENAME = "tweets_by_words";
		
		        // O construtor
		        public HBaseReader()
		        {
		            ClusterCredentials creds = new ClusterCredentials(
		                            new Uri(CLUSTERNAME),
		                            HADOOPUSERNAME,
		                            HADOOPUSERPASSWORD);
		            client = new HBaseClient(creds);
		        }
		
		        // Consultar dados de sentimento de Tweets da tabela HBase assincronamente 
		        public async Task<IEnumerable<Tweet>> QueryTweetsByKeywordAsync(string keyword)
		        {
		            List<Tweet> list = new List<Tweet>();
		
		            // Demonstrar a filtragem dos dados das últimas 6 horas da chave de linha
		            string timeIndex = (ulong.MaxValue -
		                (ulong)DateTime.UtcNow.Subtract(new TimeSpan(6, 0, 0)).ToBinary()).ToString().PadLeft(20);
		            string startRow = keyword + "_" + timeIndex;
		            string endRow = keyword + "|";
		            Scanner scanSettings = new Scanner
		            {
		                batch = 100000,
		                startRow = Encoding.UTF8.GetBytes(startRow),
		                endRow = Encoding.UTF8.GetBytes(endRow)
		            };
		
		            // Fazer chamada de verificação assíncrona
		            ScannerInformation scannerInfo =
		                await client.CreateScannerAsync(HBASETABLENAME, scanSettings);
		
		            CellSet next;
		
		            while ((next = await client.ScannerGetNextAsync(scannerInfo)) != null)
		            {
		                foreach (CellSet.Row row in next.rows)
		                {
		                    // localizar a célula com o padrão de cadeia de caracteres "d:coor" 
		                    var coordinates =
		                        row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:coor");
		
		                    if (coordinates != null)
		                    {
		                        string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
		
		                        var sentimentField =
		                            row.values.Find(c => Encoding.UTF8.GetString(c.column) == "d:sentiment");
		                        Int32 sentiment = 0;
		                        if (sentimentField != null)
		                        {
		                            sentiment = Convert.ToInt32(Encoding.UTF8.GetString(sentimentField.data));
		                        }
		
		                        list.Add(new Tweet
		                        {
		                            Longtitude = Convert.ToDouble(lonlat[0]),
		                            Latitude = Convert.ToDouble(lonlat[1]),
		                            Sentiment = sentiment
		                        });
		                    }
		
		                    if (coordinates != null)
		                    {
		                        string[] lonlat = Encoding.UTF8.GetString(coordinates.data).Split(',');
		                    }
		                }
		            }
		
		            return list;
		        }
		    }
		
		    public class Tweet
		    {
		        public string IdStr { get; set; }
		        public string Text { get; set; }
		        public string Lang { get; set; }
		        public double Longtitude { get; set; }
		        public double Latitude { get; set; }
		        public int Sentiment { get; set; }
		    }
		}

4. Dentro da classe **HBaseReader**, mude os valores constantes:

	- **NOME DO CLUSTER**: Nome do cluster HBase. Por exemplo, *https://<HBaseClusterName>.azurehdinsight.net/*. 
    - **NOME DE USUÁRIO HADOOP**: Nome do usuário do Hadoop do cluster HBase. O nome padrão é *admin*.
    - **SENHA DO USUÁRIO HADOOP**: Senha do usuário do Hadoop do cluster HBase.
    - **HBASETABLENAME** = "tweets_by_words";

	O nome da tabela HBase é "tweets_by_words". Os valores devem corresponder aos valores que você enviou no serviço de streaming, para que o aplicativo da Web leia os dados a partir da tabela HBase.




**Para adicionar o controlador TweetsController:**

1. A partir do **Gerenciador de Soluções**, expanda **TweetSentimentWeb**.
2. Clique com o botão direito do mouse em **Controladores**, clique em **Adicionar** e, em seguida, clique em **Controlador**.
3. Clique em **Controlador de API Web 2 - Vazio**, depois clique em **Adicionar**.
4. No nome do Controlador, digite **TweetsController** e, em seguida, clique em **Adicionar**.
5. A partir do **Gerenciador de Soluções**, clique duas vezes com o mouse em TweetsController.cs para abrir o arquivo.
5. Modifique o arquivo para que ele se assemelhe ao seguinte:

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		
		using System.Threading.Tasks;
		using TweetSentimentWeb.Models;
		
		namespace TweetSentimentWeb.Controllers
		{
		    public class TweetsController : ApiController
		    {
		        HBaseReader hbase = new HBaseReader();
		
		        public async Task<IEnumerable<Tweet>> GetTweetsByQuery(string query)
		        {
		            return await hbase.QueryTweetsByKeywordAsync(query);
		        }
		    }
		}

**Para adicionar heatmap.js**

1. A partir do **Gerenciador de Soluções**, expanda **TweetSentimentWeb**.
2. Clique com o botão direito do mouse em **Scripts**, clique em **Adicionar** e em **Arquivo JavaScript**.
3. No nome do Item, digite **heatmap.js**.
4. Copie e cole o seguinte código no arquivo. O código foi escrito por Alaistair Aitchison. Para obter mais informações, consulte [http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/](http://alastaira.wordpress.com/2011/04/15/bing-maps-ajax-v7-heatmap-library/).

		/*******************************************************************************
		* Autor: Alastair Aitchison
		* Site: http://alastaira.wordpress.com
		* Data: 15 de abril de 2011
		* 
		* Descrição: 
		* Este arquivo JavaScript fornece um algoritmo que pode ser usado para adicionar um heatmap
		* sobrepor em um controle do Bing Maps v7. A paleta de intensidade e temperatura
		* do heatmap foi projetada para ser facilmente personalizável.
		*
		* Requisitos:
		* A própria camada do heatmap é criada dinamicamente no lado do cliente usando
		* o elemento HTML5 <canvas> e, portanto, requer um navegador que ofereça suporte
		* a esse elemento. Ela foi testada nos navegadores IE9, Firefox 3.6/4 e 
		* Chrome 10. Se você puder confirmar se ele funciona em outros navegadores ou
		* não, eu adoraria saber!
		
		* Uso:
		* O construtor HeatMapLayer requer:
		* - Uma referência a um objeto de mapa
		* - Uma matriz ou itens Microsoft.Maps.Location
		* - Parâmetros opcionais para personalizar a aparência da camada
		*  (Radius, Unidade, Intensidade e ColourGradient) e uma função de retorno de chamada
		*
		*/
		
		var HeatMapLayer = function (map, locations, options) {
		
		    /* Private Properties */
		    var _map = map,
		      _canvas,
		      _temperaturemap,
		      _locations = [],
		      _viewchangestarthandler,
		      _viewchangeendhandler;
		
		    // Definir opções padrão
		    var _options = {
		        // Opacidade no Centro de cada ponto de calor
		        intensidade: 0.5,
		
		        // Radius afetado de cada ponto de calor
		        radius: 1000,
		
		        // Se o radius é um valor de pixel absoluto ou metros
		        unidade: 'metros'
		
		        // Gradiente de temperatura de cor do mapa
		        colourgradient: {
		            "0.00": 'rgba(255,0,255,20)'  // Magenta
		            "0.25": 'rgba(0,0,255,40)',    // Azul
		            "0.50": 'rgba(0,255,0,80)',    // Verde
		            "0.75": 'rgba(255,255,0,120)', // Yellow
		            "1.00": 'rgba(255,0,0,150)'    // Vermelho
		        },
		
		        // Função de retorno de chamada a ser acionada depois que a camada heatmap foi redesenhada 
		        callback nulo
		    };
		
		    /* Private Methods */
		    function _init() {
		        var _mapDiv = _map.getRootElement();
		
		        if (_mapDiv.childNodes.length >= 3 && _mapDiv.childNodes[2].childNodes.length >= 2) {
		            // Criar o elemento canvas
		            _canvas = document.createElement('canvas');
		            _canvas.style.position = 'relative';
		
		            var container = document.createElement('div');
		            container.style.position = 'absolute';
		            container.style.left = '0px';
		            container.style.top = '0px';
		            container.appendChild(_canvas);
		
		            _mapDiv.childNodes[2].childNodes[1].appendChild(container);
		
		            // Padrões de substituição com quaisquer opções passado no construtor
		            _setOptions(options);
		
		            // Matriz de carregamento de dados local
		            _setPoints(locations);
		
		            // Criar um gradiente de cores a partir de colourstops fornecidos
		            _temperaturemap = _createColourGradient(_options.colourgradient);
		
		            // Conectar o manipulador de eventos para redesenhar a tela heatmap
		            _viewchangestarthandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangestart', _clearHeatMap);
		            _viewchangeendhandler = Microsoft.Maps.Events.addHandler(_map, 'viewchangeend', _createHeatMap);
		
		            _createHeatMap();
		
		            delete _init;
		        } else {
		            setTimeout(_init, 100);
		        }
		    }
		
		    // Redefine o mapa de calor
		    function _clearHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        ctx.clearRect(0, 0, _canvas.width, _canvas.height);
		    }
		
		    // Cria um gradiente de cores de paradas de cor fornecidas na inicialização
		    function _createColourGradient(colourstops) {
		        var ctx = document.createElement('canvas').getContext('2d');
		        var grd = ctx.createLinearGradient(0, 0, 256, 0);
		        for (var c in colourstops) {
		            grd.addColorStop(c, colourstops[c]);
		        }
		        ctx.fillStyle = grd;
		        ctx.fillRect(0, 0, 256, 1);
		        return ctx.getImageData(0, 0, 256, 1).data;
		    }
		
		    // Aplica um gradiente de cores ao mapa de intensidade
		    function _colouriseHeatMap() {
		        var ctx = _canvas.getContext("2d");
		        var dat = ctx.getImageData(0, 0, _canvas.width, _canvas.height);
		        var pix = dat.data; // pix is a CanvasPixelArray containing height x width x 4 bytes of data (RGBA)
		        for (var p = 0, len = pix.length; p < len;) {
		            var a = pix[p + 3] * 4; // Obtém o alfa deste pixel
		            if (a != 0) { // Se houver dados para plotar
		                pix[p] = _temperaturemap[a]; // define o valor de vermelho do gradiente que corresponde a essa alfa
		                pix[p + 1] = _temperaturemap[a + 1]; // defina o valor de verde com base em alpha
		                pix[p + 2] = _temperaturemap[a + 2]; // define o valor de azul com base em alpha
		            }
		            p += 4; // Vá para o próximo pixel
		        }
		        ctx.putImageData(dat, 0, 0);
		    }
		
		    // Define as opções passadas em
		    function _setOptions(options) {
		        for (attrname in options) {
		            _options[attrname] = options[attrname];
		        }
		    }
		
		    // Define os pontos de heatmap de uma matriz de Microsoft.Maps.Locations  
		    function _setPoints(locations) {
		        _locations = locations;
		    }
		
		    // Método principal para desenhar o heatmap
		    function _createHeatMap() {
		        // Certifique-se de que a tela corresponde às dimensões atuais do mapa
		        // Isso também tem o efeito de redefinir a tela
		        _canvas.height = _map.getHeight();
		        _canvas.width = _map.getWidth();
		
		        _canvas.style.top = -_canvas.height / 2 + 'px';
		        _canvas.style.left = -_canvas.width / 2 + 'px';
		
		        // Calcule o raio do pixel de cada heatpoint no zoom do mapa atual
		        if (_options.unit == "pixels") {
		            radiusInPixel = _options.radius;
		        } else {
		            radiusInPixel = _options.radius / _map.getMetersPerPixel();
		        }
		
		        var ctx = _canvas.getContext("2d");
		
		        // Converter lat/longo para localização de pixel
		        var pixlocs = _map.tryLocationToPixel(_locations, Microsoft.Maps.PixelReference.control);
		        var shadow = 'rgba(0, 0, 0, ' + _options.intensity + ')';
		        var mapWidth = 256 * Math.pow(2, _map.getZoom());
		
		        // Criar o mapa de intensidade fazendo um loop através de cada local
		        for (var i = 0, len = pixlocs.length; i < len; i++) {
		            var x = pixlocs[i].x;
		            var y = pixlocs[i].y;
		
		            if (x < 0) {
		                x += mapWidth * Math.ceil(Math.abs(x / mapWidth));
		            }
		
		            // Criar um gradiente radial centralizado neste ponto
		            var grd = ctx.createRadialGradient(x, y, 0, x, y, radiusInPixel);
		            grd.addColorStop(0.0, shadow);
		            grd.addColorStop(1.0, 'transparent');
		
		            // Desenhar o heatpoint na tela
		            ctx.fillStyle = grd;
		            ctx.fillRect(x - radiusInPixel, y - radiusInPixel, 2 * radiusInPixel, 2 * radiusInPixel);
		        }
		
		        // Aplicar o gradiente de cores específico ao mapa de intensidade
		        _colouriseHeatMap();
		
		        // Chamar a função de retorno de chamada, se especificado
		        if (_options.callback) {
		            _options.callback();
		        }
		    }
		
		    /* Public Methods */
		
		    this.Show = function () {
		        if (_canvas) {
		            _canvas.style.display = '';
		        }
		    };
		
		    this.Hide = function () {
		        if (_canvas) {
		            _canvas.style.display = 'none';
		        }
		    };
		
		    // Define opções de intensidade, radius, colourgradient etc.
		    this.SetOptions = function (options) {
		        _setOptions(options);
		    }
		
		    // Define uma matriz de Microsoft.Maps.Locations da qual o heatmap é criado
		    this.SetPoints = function (locations) {
		        // Redefina a camada heatmap existente
		        _clearHeatMap();
		        // Passe o novo conjunto de locais
		        _setPoints(locations);
		        // Recrie a camada
		        _createHeatMap();
		    }
		
		    // Remove a camada heatmap do DOM
		    this.Remove = function () {
		        _canvas.parentNode.parentNode.removeChild(_canvas.parentNode);
		
		        if (_viewchangestarthandler) { Microsoft.Maps.Events.removeHandler(_viewchangestarthandler); }
		        if (_viewchangeendhandler) { Microsoft.Maps.Events.removeHandler(_viewchangeendhandler); }
		
		        _locations = null;
		        _temperaturemap = null;
		        _canvas = null;
		        _options = null;
		        _viewchangestarthandler = null;
		        _viewchangeendhandler = null;
		    }
		
		    // Chame a rotina de inicialização
		    _init();
		};
		
		// Chame o método do módulo carregado
		Microsoft.Maps.moduleLoaded('HeatMapModule');


**Para adicionar o tweetStream.js:**

1. A partir do **Gerenciador de Soluções**, expanda **TweetSentimentWeb**.
2. Clique com o botão direito do mouse em **Scripts**, clique em **Adicionar** e em **Arquivo JavaScript**.
3. No nome do Item, digite **twitterStream.js**.
4. Copie e cole o seguinte código no arquivo:

		var liveTweetsPos = [];
		var liveTweets = [];
		var liveTweetsNeg = [];
		var map;
		var heatmap;
		var heatmapNeg;
		var heatmapPos;
		
		function initialize() {
		    // Inicializar o map
		    var options = {
		        credenciais: "AvFJTZPZv8l3gF8VC3Y7BPBd0r7LKo8dqKG02EAlqg9WAi0M7la6zSIT-HwkMQbx",
		        center: new Microsoft.Maps.Location(23.0, 8.0),
		        mapTypeId: Microsoft.Maps.MapTypeId.ordnanceSurvey,
		        labelOverlay: Microsoft.Maps.LabelOverlay.hidden,
		        zoom: 2.5
		    };
		    var map = new Microsoft.Maps.Map(document.getElementById('map_canvas'), options);
		
		    // Opções de Heatmap para camadas negativas, neutras e positivas
		
		    var heatmapOptions = {
		        // Opacidade no Centro de cada ponto de calor
		        intensidade: 0.5,
		
		        // Radius afetado de cada ponto de calor
		        radius: 15,
		
		        // Se o radius é um valor de pixel absoluto ou metros
		        unidade: 'pixels'
		    };
		
		    var heatmapPosOptions = {
		        // Opacidade no Centro de cada ponto de calor
		        intensidade: 0.5,
		
		        // Radius afetado de cada ponto de calor
		        radius: 15,
		
		        // Se o radius é um valor de pixel absoluto ou metros
		        unidade: 'pixels',
		
		        colourgradient: {
		            0.0: 'rgba(0, 255, 255, 0)',
		            0.1: 'rgba(0, 255, 255, 1)',
		            0.2: 'rgba(0, 255, 191, 1)',
		            0.3: 'rgba(0, 255, 127, 1)',
		            0.4: 'rgba(0, 255, 63, 1)',
		            0.5: 'rgba(0, 127, 0, 1)',
		            0.7: 'rgba(0, 159, 0, 1)',
		            0.8: 'rgba(0, 191, 0, 1)',
		            0.9: 'rgba(0, 223, 0, 1)',
		            1.0: 'rgba(0, 255, 0, 1)'
		        }
		    };
		
		    var heatmapNegOptions = {
		        // Opacidade no Centro de cada ponto de calor
		        intensidade: 0.5,
		
		        // Radius afetado de cada ponto de calor
		        radius: 15,
		
		        // Se o radius é um valor de pixel absoluto ou metros
		        unidade: 'pixels',
		
		        colourgradient: {
		            0.0: 'rgba(0, 255, 255, 0)',
		            0.1: 'rgba(0, 255, 255, 1)',
		            0.2: 'rgba(0, 191, 255, 1)',
		            0.3: 'rgba(0, 127, 255, 1)',
		            0.4: 'rgba(0, 63, 255, 1)',
		            0.5: 'rgba(0, 0, 127, 1)',
		            0.7: 'rgba(0, 0, 159, 1)',
		            0.8: 'rgba(0, 0, 191, 1)',
		            0.9: 'rgba(0, 0, 223, 1)',
		            1.0: 'rgba(0, 0, 255, 1)'
		        }
		    };
		
		    // Registrar e carregar o módulo de HeatMap do lado do cliente
		    Microsoft.Maps.registerModule("HeatMapModule", "scripts/heatmap.js");
		    Microsoft.Maps.loadModule("HeatMapModule", {
		        callback function () {
		            // Criar camadas de heatmap para tweets negativos, neutros e positivos
		            heatmapPos = new HeatMapLayer(map, liveTweetsPos, heatmapPosOptions);
		            heatmap = new HeatMapLayer(map, liveTweets, heatmapOptions);
		            heatmapNeg = new HeatMapLayer(map, liveTweetsNeg, heatmapNegOptions);
		        }
		    });
		
		    $("#searchbox").val("xbox");
		    $("#searchBtn").click(onsearch);
		    $("#positiveBtn").click(onPositiveBtn);
		    $("#negativeBtn").click(onNegativeBtn);
		    $("#neutralBtn").click(onNeutralBtn);
		    $("#neutralBtn").button("toggle");
		}
		
		function onsearch() {
		    var uri = 'api/tweets?query=';
		    var query = $('#searchbox').val();
		    $.getJSON(uri + query)
		        .done(function (data) {
		            liveTweetsPos = [];
		            liveTweets = [];
		            liveTweetsNeg = [];
		
		            // Em caso de êxito, 'dados' contém uma lista de tweets.
		            $.each(data, function (key, item) {
		                addTweet(item);
		            });
		
		            if (!$("#neutralBtn").hasClass('active')) {
		                $("#neutralBtn").button("toggle");
		            }
		            onNeutralBtn();
		        })
		        .fail(function (jqXHR, textStatus, err) {
		            $('#statustext').text('Error: ' + err);
		        });
		}
		
		function addTweet(item) {
		    //Adicione tweet para as matrizes de mapa de calor.
		    var tweetLocation = new Microsoft.Maps.Location(item.Latitude, item.Longtitude);
		    if (item.Sentiment > 0) {
		        liveTweetsPos.push(tweetLocation);
		    } else if (item.Sentiment < 0) {
		        liveTweetsNeg.push(tweetLocation);
		    } else {
		        liveTweets.push(tweetLocation);
		    }
		}
		
		function onPositiveBtn() {
		    if ($("#neutralBtn").hasClass('active')) {
		        $("#neutralBtn").button("toggle");
		    }
		    if ($("#negativeBtn").hasClass('active')) {
		        $("#negativeBtn").button("toggle");
		    }
		
		    heatmapPos.SetPoints(liveTweetsPos);
		    heatmapPos.Show();
		    heatmapNeg.Hide();
		    heatmap.Hide();
		
		    $('#statustext').text('Tweets: ' + liveTweetsPos.length + "   " + getPosNegRatio());
		}
		
		function onNeutralBtn() {
		    if ($("#positiveBtn").hasClass('active')) {
		        $("#positiveBtn").button("toggle");
		    }
		    if ($("#negativeBtn").hasClass('active')) {
		        $("#negativeBtn").button("toggle");
		    }
		
		    heatmap.SetPoints(liveTweets);
		    heatmap.Show();
		    heatmapNeg.Hide();
		    heatmapPos.Hide();
		
		    $('#statustext').text('Tweets: ' + liveTweets.length + "   " + getPosNegRatio());
		}
		
		function onNegativeBtn() {
		    if ($("#positiveBtn").hasClass('active')) {
		        $("#positiveBtn").button("toggle");
		    }
		    if ($("#neutralBtn").hasClass('active')) {
		        $("#neutralBtn").button("toggle");
		    }
		
		    heatmapNeg.SetPoints(liveTweetsNeg);
		    heatmapNeg.Show();
		    heatmap.Hide();;
		    heatmapPos.Hide();;
		
		    $('#statustext').text('Tweets: ' + liveTweetsNeg.length + "\t" + getPosNegRatio());
		}
		
		function getPosNegRatio() {
		    if (liveTweetsNeg.length == 0) {
		        return "";
		    }
		    else {
		        var ratio = liveTweetsPos.length / liveTweetsNeg.length;
		        var str = parseFloat(Math.round(ratio * 10) / 10).toFixed(1);
		        return "Positive/Negative Ratio: " + str;
		    }
		}


**Para modificar o layout.cshtml:**

1. A partir do **Gerenciador de Soluções**, expanda **TweetSentimentWeb**, expanda **Exibições**, expanda**Compartilhado** e, em seguida, clique duas vezes com o mouse em **Layout.cshtml**.
2. Substitua o conteúdo com o seguinte:

		<!DOCTYPE html>
		<html>
		<head>
		    <meta charset="utf-8" />
		    <meta name="viewport" content="width=device-width, initial-scale=1.0">
		    <title>@ViewBag.Title</title>
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		    <!-- Bing Maps -->
		    <script type="text/javascript" src="http://ecn.dev.virtualearth.net/mapcontrol/mapcontrol.ashx?v=7.0&mkt=en-gb"></script>
		    <!-- Spatial Dashboard JavaScript -->
		    <script src="~/Scripts/twitterStream.js" type="text/javascript"></script>
		</head>
		<body onload="initialize()">
		    <div class="navbar navbar-inverse navbar-fixed-top">
		        <div class="container">
		            <div class="navbar-header">
		                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                    <span class="icon-bar"></span>
		                </button>
		            </div>
		            <div class="navbar-collapse collapse">
		                <div class="row">
		                    <ul class="nav navbar-nav col-lg-5">
		                        <li class="col-lg-12">
		                            <div class="navbar-form">
		                                <input id="searchbox" type="search" class="form-control">
		                                <button type="button" id="searchBtn" class="btn btn-primary">Go</button>
		                            </div>
		                        </li>
		                    </ul>
		                    <ul class="nav navbar-nav col-lg-7">
		                        <li>
		                            <div class="navbar-form">
		                                <div class="btn-group" data-toggle="buttons-radio">
		                                    <button type="button" id="positiveBtn" class="btn btn-primary">Positive</button>
		                                    <button type="button" id="neutralBtn" class="btn btn-primary">Neutral</button>
		                                    <button type="button" id="negativeBtn" class="btn btn-primary">Negative</button>
		                                </div>
		                            </div>
		                        </li>
		                        <li><span id="statustext" class="navbar-text"></span></li>
		                    </ul>
		                </div>
		            </div>
		        </div>
		    </div>
		    <div class="map_container">
		        @RenderBody()
		    </div>
		    @Scripts.Render("~/bundles/jquery")
		    @Scripts.Render("~/bundles/bootstrap")
		    @RenderSection("scripts", required: false)
		</body>
		</html>



**Para modificar o Index.cshtml**

1. A partir do **Gerenciador de Soluções**, expanda **TweetSentimentWeb**, expanda **Exibições**, expanda**Página Inicial** e, em seguida, clique duas vezes com o mouse em **Index.cshtml**.
2. Substitua o conteúdo com o seguinte:

		@{
		    ViewBag.Title = "Tweet Sentiment";
		}
		
		<div class="map_container">
		    <div id="map_canvas"/>
		</div>

**Para modificar o arquivo site.css:**

1. A partir do **Gerenciador de Soluções**, expanda **TweetSentimentWeb**, expanda **Conteúdo** e, em seguida, clique duas vezes com o mouse em **Site.css**.
2. Acrescente o seguinte código ao arquivo.
		
		/* make container, and thus map, 100% width */
		.map_container {
			width: 100%;
			height: 100%;
		}
		
		#map_canvas{
		  height:100%;
		}
		
		#tweets{
		  position: absolute;
		  top: 60px;
		  left: 75px;
		  z-index:1000;
		  font-size: 30px;
		}

**Para modificar o arquivo global.asax:**

1. A partir do **Gerenciador de Soluções**, expanda **TweetSentimentWeb** e, em seguida, clique duas vezes com o mouse em **Global.asax**.
2. Adicione a seguinte instrução usando:

		using System.Web.Http;

2. E as seguintes linhas dentro da função **Application_Start()**:

		// Registrar rotas API
		GlobalConfiguration.Configure(WebApiConfig.Register);
  
	Modifique o registro das rotas API para fazer o controlador API da Web funcionar dentro do aplicativo MVC.

**Para executar o aplicativo Web:**

1. Verifique que o aplicativo de console do serviço de streaming ainda está em execução. Então, você pode ver as alterações em tempo real.
2. Pressione **F5** para executar o aplicativo Web.

	![hdinsight.hbase.twitter.sentiment.bing.map][img-bing-map]
2. Na caixa de texto, insira uma palavra-chave e, em seguida, clique em **Ir**.  Dependendo dos dados coletados na tabela HBase, algumas palavras-chave podem não ser encontradas. Tente algumas palavras-chave em comum, como "amor", "xbox", "playstation" e assim por diante. 
3. Alterne entre **Positivo**, **Neutro** e **Negativo** para comparar o sentimento no objeto.
4. Deixe o serviço de streaming executando por mais uma hora, e então pesquise a mesma palavra-chave e compare os resultados.

 
Opcionalmente, é possível implantar o aplicativo em um site do Azure.  Para obter instruções, consulte [Introdução aos Sites do Azure e ASP.NET][website-get-started].
 
##<a id="nextsteps"></a>Próximas etapas

Neste tutorial, aprendemos como obter tweets, analisar o sentimento dos tweets, salvar os dados do sentimento para o HBase e apresentar os dados dos sentimentos do Twitter em tempo real para mapas Bing. Para obter mais informações, consulte:

- [Introdução ao HDInsight][hdinsight-get-started]
- [Analisar dados do Twitter com Hadoop no HDInsight][hdinsight-analyze-twitter-data]
- [Analisar dados de atraso de voo usando o HDInsight][hdinsight-analyze-flight-delay-data]
- [Desenvolver programas de streaming do Hadoop em C# para o HDInsight][hdinsight-develop-streaming]
- [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]


[hbase-get-started]: ../hdinsight-hbase-get-started/
[website-get-started]: ../web-sites-dotnet-get-started/



[img-app-arch]: ./media/hdinsight-hbase-analyze-twitter-sentiment/AppArchitecture.png
[img-twitter-app]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterApp.png
[img-streaming-service]: ./media/hdinsight-hbase-analyze-twitter-sentiment/StreamingService.png
[img-bing-map]: ./media/hdinsight-hbase-analyze-twitter-sentiment/TwitterSentimentBingMap.png



[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-analyze-twitter-data]: ../hdinsight-analyze-twitter-data/
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started/




[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[apache-hive-tutorial]: https://cwiki.apache.org/confluence/display/Hive/Tutorial

[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter

[powershell-start]: http://technet.microsoft.com/pt-br/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell
[powershell-script]: http://technet.microsoft.com/pt-br/library/ee176949.aspx

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/

