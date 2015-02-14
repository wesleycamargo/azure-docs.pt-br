<properties 
	pageTitle="Desenvolver aplicativos de processamento de dados de streaming com SCP.NET no Storm | Azure" 
	description="Saiba como desenvolver aplicativos de processamento de dados de streaming com SCP.NET e C# no Storm no HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="weedqian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2014" 
	ms.author="qixia"/>

#Desenvolver aplicativos de processamento de dados de streaming em C# com Stream Computing Platform e Storm no HDInsight

O Stream Computing Platform (SCP) é uma plataforma para a criação em tempo real de aplicativos de processamento de dados confiáveis, distribuídos, consistentes e de alto desempenho usando .NET. Ela é criada na parte superior do [Apache Storm](http://storm.incubator.apache.org/) -- um sistema de processamento de fluxo em tempo real com código-fonte aberto, que está disponível com o HDInsight.

Neste artigo, você aprenderá:

* O que é o SCP e como ele funciona com o Storm

* Como criar uma solução SCP

* Como testar uma solução SCP

* Como implantar uma solução de SCP em um cluster Storm no HDInsight

##Pré-requisitos

* Uma assinatura do Azure

* Um cluster Storm no HDInsight

* Visual Studio 2010 ou 2013

##Sumário

* [SCP e Storm](#scpandstorm)

##<a id="scpandstorm"></a>SCP e Storm

O Apache Storm é um sistema de computação distribuído executado em clusters Hadoop que permite a realização de processamento de dados em tempo real. Embora o Storm execute em Máquina Virtual Java (JVM), ele foi desenvolvido para que as soluções (conhecidas como **topologias**) possam ser implementadas em uma série de linguagens de programação. É possível criar uma topologia que seja uma combinação de componentes gravados em diversas linguagens.

O SCP fornece as bibliotecas que facilitam a criação das soluções Storm usando .NET. Os clusters Storm no HDInsight incluem os componentes necessários do lado do servidor para executar as soluções SCP que você cria.

Para obter mais informações sobre Storm no HDInsight, consulte [Visão geral do Storm no HDInsight](http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-storm-overview/).

###Criar uma solução SCP

O SCP fornece uma interface que permite a criação dos seguintes componentes do Storm:

* Spout - consome dados de uma fonte (arquivo, banco de dados, API, etc.) e emite um ou mais fluxos de tuplas (uma lista ordenada de elementos)
* Bolt - consome um ou mais fluxos e, como opção, emite um ou mais fluxos
* Topologia - define como deve ser o fluxo de dados entre os spouts e bolts, o paralelismo entre eles e as informações de configuração

> [AZURE.NOTE] Os spouts e bolts também implementam processamento genérico. Por exemplo, um spout pode segmentar uma mensagem de entrada em diversas tupla ou apenas emitir uma tupla e deixar que um bolt extraia os valores dos quais necessita. De forma semelhante, se precisar gravar dados em um armazenamento de dados, você deverá implementar isso em um bolt.

Criar uma solução envolve o seguinte:

* Qual fonte de dados você consumirá? É preciso ter um spout para implementá-la.
* Qual processamento deve ocorrer? É preciso implementar nos spouts e/ou bolts.
* Se o processamento estiver segmentado por diversos bolts, qual é o fluxo de dados entre eles? É preciso descrever isso por meio da topologia.
* Como o processamento será distribuído pelos nós no cluster HDInsight? É preciso descrever isso por meio da topologia.

##Instalar o SDK do SCP

O SDK do SCP é fornecido no cluster Storm no HDInsight. Depois de [criar um cluster Storm no HDInsight](http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-storm-getting-started/), siga as seguintes etapas para baixar o SDK para seu ambiente de desenvolvimento local.

1. Entre no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).

2. Clique em **HDINSIGHT** no painel esquerdo. Você verá uma lista de clusters HDInsight implantados.

3. Clique no cluster HDInsight ao qual você deseja se conectar.

4. Na parte superior da página, clique em **CONFIGURAÇÃO**.

5. Na parte inferior da página, clique em **HABILITAR REMOTO**.

6. No assistente Configurar Área de Trabalho Remota, digite um **nome do usuário** e uma **senha** para a área de trabalho remota. Insira uma data de validade na caixa de diálogo **EXPIRA EM** e clique no ícone de verificação.

7. Depois de habilitar a Área de Trabalho Remota, selecione **CONECTAR** na parte inferior da página e siga as instruções.

8. Após conectar-se ao cluster pela Área de Trabalho Remota, abra o **Gerenciador de Arquivos** e digite **%storm_home%\examples** na barra de endereços.

9. Clique com o botão direito do mouse na pasta **SDK** e selecione **Copiar**.

10. Em seu ambiente de desenvolvimento local, abra o **Gerenciador de Arquivos** e navegue até o local no qual você deseja armazenar o SDK. Clique com o botão direito do mouse e selecione **Colar**.

##Criar uma solução SCP

1. No Visual Studio, crie um novo projeto para um **Aplicativo de Console**. Nomeie essa solução como **WordCount**.

2. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **Referências** e selecione **Adicionar Referência**.

3. Selecione o botão **Procurar** na parte inferior do **Gerenciador de Referências** e navegue até a pasta do SDK que você baixou antes. Selecione **Microsoft.SCPNet.dll** e **Microsoft.SCPLogger.dll** e clique em **Adicionar**. Clique em **OK** para fechar a janela do Gerenciador de Referências.

###Criar o spout

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WordCount** e selecione **Adicionar | Novo item**. Selecione **Classe** e digite **WordSpout.cs** como o nome. Por fim, clique em **Adicionar**.

2. Abra o arquivo **WordSpout.cs** e substitua o código existente pelo seguinte. Não se esqueça de ler os comentários para entender como esse código funciona.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class WordSpout : ISCPSpout
            {
                //Context
                private Context ctx;

                private Random rand = new Random();
                //The sentences to emit
                string[] sentences = new string[] {
                                                  "the cow jumped over the moon",
                                                  "an apple a day keeps the doctor away",
                                                  "four score and seven years ago",
                                                  "snow white and the seven dwarfs",
                                                  "i am at two with nature"};

                //Constructor
                public WordSpout(Context ctx)
                {
                    //Log that we are starting
                    Context.Logger.Info("WordSpout constructor called");
                    //Store the context that was passed
                    this.ctx = ctx;

                    //Define the schema for the emitted tuples
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    outputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Declare the schema for the stream
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
                }

                //Return a new instance of the spout
                public static WordSpout Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new WordSpout(ctx);
                }

                //Emit the next tuple
                //NOTE: When using data from an external data source
                //such as Service Bus, Event Hub, Twitter, etc.,
                //you would read and emit it in NextTuple
                public void NextTuple(Dictionary<string, object> parms)
                {
                    Context.Logger.Info("NextTuple enter");

                    //Get a random sentence
                    string sentence = sentences[rand.Next(0, sentences.Length - 1)];
                    Context.Logger.Info("Emit: {0}", sentence);
                    //Emit the sentence
                    this.ctx.Emit(new Values(sentence));

                    Context.Logger.Info("NextTuple exit");
                }

                //Ack's are not implemented
                public void Ack(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }

                //Ack's are not implemented, so
                //fail should never be called
                public void Fail(long seqId, Dictionary<string, object> parms)
                {
                    throw new NotImplementedException();
                }
            }
        }

###Criar testes

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WordCount** e selecione **Adicionar | Novo item**. Selecione **Classe** e digite **LocalTest.cs** como o nome. Por fim, clique em **Adicionar**.

2. Abra o arquivo **LocalTest.cs** e substitua o código existente pelo seguinte. O código usado para testar o spout é muito semelhante ao código que será usado para testar os bolts.

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class LocalTest
            {
                //Run tests
                public void RunTestCase()
                {
                    Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();
                    //Spout tests
                    {
                        //Get local context
                        LocalContext spoutCtx = LocalContext.Get();
                        //Get an instance of the spout
                        WordSpout spout = WordSpout.Get(spoutCtx, emptyDictionary);
                        //Call NextTuple to emit data
                        for (int i = 0; i < 10; i++)
                        {
                            spout.NextTuple(emptyDictionary);
                        }
                        //Store the stream for the next component
                        spoutCtx.WriteMsgQueueToFile("spout.txt");
                    }
                }
            }
        }

3. Abra **Program.cs** e substitua o código existente pelo seguinte.

        using Microsoft.SCP;
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;

        namespace WordCount
        {
            class Program
            {
                static void Main(string[] args)
                {
                    if (args.Count() > 0)
                    {
                        //Code to run on HDInsight cluster will go here
                    }
                    else
                    {
                        //Set log prefix information for the component being tested
                        System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
                        //Initialize the runtime
                        SCPRuntime.Initialize();

                        //If we are not running under the local context, throw an error
                        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
                        {
                            throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
                        }
                        //Create an instance of LocalTest
                        LocalTest localTest = new LocalTest();
                        //Run the tests
                        localTest.RunTestCase();
                    }
                }
            }
        }

3. Execute o aplicativo. Após a conclusão, examine o diretório **WordCount\bin\debug** em seu projeto do Visual Studio. Deve existir um arquivo chamado **spout.txt**, o qual conterá os dados emitidos pelo spout durante o teste. O conteúdo textual deve ser semelhante ao seguinte.

        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]}
        {"__isset":{"streamId":true,"tupleId":true,"evt":true,"data":true},"StreamId":"default","TupleId":"","Evt":1000,"Data":[[116,104,101,32,99,111,119,32,106,117,109,112,101,100,32,111,118,101,114,32,116,104,101,32,109,111,111,110]]}

    > [AZURE.NOTE] Nas linhas acima, "Data" é a cadeia de caracteres emitida pelo spout, mas que está armazenada como uma matriz de bytes. Por exemplo, `[[97,110,32,97,112,112,108,101,32,97,32,100,97,121,32,107,101,101,112,115,32,116,104,101,32,100,111,99,116,111,114,32,97,119,97,121]]` é "uma maçã por dia é sinônimo de saúde".

###Criar os bolts

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse em **WordCount** e selecione **Adicionar | Novo item**. Selecione **Classe** e digite **SplitterBolt.cs** como o nome. Por fim, clique em **Adicionar**. Repita essa etapa para adicionar a classe chamada **CounterBolt.cs**.

2. Abra o arquivo **SplitterBolt.cs** e substitua o código existente pelo seguinte.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;
        using System.Threading;

        namespace WordCount
        {
            class SplitterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Constructor
                public SplitterBolt(Context ctx)
                {
                    Context.Logger.Info("Splitter constructor called");
                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //In this case, just a string tuple
                    inputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //We are also only emitting a string tuple
                    outputSchema.Add("default", new List<Type>() {typeof (string)});
                    //Declare both incoming and outbound schemas
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get a new instance
                public static SplitterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new SplitterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the incomin tuple value
                    string sentence = tuple.GetString(0);
                    //Split it
                    foreach (string word in sentence.Split(' '))
                    {
                        //Emit each word to the outbound stream
                        Context.Logger.Info("Emit: {0}", word);
                        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));
                    }
                    Context.Logger.Info("Execute exit");
                }
            }
        }

4. Abra o arquivo **CounterBolt.cs** e substitua o conteúdo pelo seguinte.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.SCP;

        namespace WordCount
        {
            class CounterBolt : ISCPBolt
            {
                //Context
                private Context ctx;

                //Store each word and a count of how many times it has
                //been emitted by the splitter
                private Dictionary<string, int> counts = new Dictionary<string, int>();

                //Constructor
                public CounterBolt(Context ctx)
                {
                    Context.Logger.Info("Counter constructor called");

                    //Set context
                    this.ctx = ctx;
                    //Define the schema for the incoming tuples from spout
                    Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
                    //Just a string, which will contain a word from the splitter
                    inputSchema.Add("default", new List<Type>() { typeof(string) });
                    //Define the schema for tuples to be emitted from this bolt
                    Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
                    //In this case, a string (containing a word) and an int (containing the count)
                    outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
                    this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
                }

                //Get an instance
                public static CounterBolt Get(Context ctx, Dictionary<string, Object> parms)
                {
                    return new CounterBolt(ctx);
                }

                //Process a tuple from the stream
                public void Execute(SCPTuple tuple)
                {
                    Context.Logger.Info("Execute enter");
                    //Get the word that was emitted from the splitter
                    string word = tuple.GetString(0);
                    //Have we seen this word before?
                    int count = counts.ContainsKey(word) ? counts[word] : 0;
                    //Increment and store the count for this word
                    count++;
                    counts[word] = count;

                    Context.Logger.Info("Emit: {0}, count: {1}", word, count);
                    //Emit the word and the count
                    this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));

                    Context.Logger.Info("Execute exit");
                }
            }
        }

    > [AZURE.NOTE] Esse bolt emite um fluxo que é útil para testes. Em uma solução no mundo real, você armazenaria os dados em uma fila de banco de dados ou em outro armazenamento persistente ao final do processamento.

3. Abra **LocalTest.cs** e adicione os seguintes testes para SplitterBolt e CounterBolt após o bloco de teste do spout anterior.

        //SplitterBolt tests
        {
            LocalContext splitterCtx = LocalContext.Get();
            SplitterBolt splitter = SplitterBolt.Get(splitterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the spout
            splitterCtx.ReadFromFileToMsgQueue("spout.txt");
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            //Store the stream for the next component
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
        }
        //CounterBolt tests
        {
            LocalContext counterCtx = LocalContext.Get();
            CounterBolt counter = CounterBolt.Get(counterCtx, emptyDictionary);
            //Read from the 'stream' emitted by the splitter
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            //Store the stream for the next component
            counterCtx.WriteMsgQueueToFile("counter.txt");
        }

4. Execute a solução. Após a conclusão, você deve ter os seguintes arquivos no diretório **WordCount\bin\debug**.

    * **spout.txt** - os dados emitidos pelo WordSpout
    * **splitter.txt** - os dados emitidos pelo SplitterBolt
    * **counter.txt** - os dados emitidos pelo CounterBolt

###Adicionar código para executar em um cluster

1. Abra **Program.cs** e substitua a linha `//Code to run on HDInsight cluster will go here` pelo seguinte e, em seguida, recompile o projeto

        //The component to run
        string compName = args[0];
        //Run the component
        if ("wordspout".Equals(compName))
        {
            //Set the prefix for logging
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Spout");
            //Initialize the runtime
            SCPRuntime.Initialize();
            //Run the plugin (WordSpout)
            SCPRuntime.LaunchPlugin(new newSCPPlugin(WordSpout.Get));
        }
        else if ("splitterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Splitter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(SplitterBolt.Get));
        }
        else if ("counterbolt".Equals(compName))
        {
            System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-Counter");
            SCPRuntime.Initialize();
            SCPRuntime.LaunchPlugin(new newSCPPlugin(CounterBolt.Get));
        }
        else
        {
            throw new Exception(string.Format("unexpected compName: {0}", compName));
        }

4. Crie um novo arquivo chamado **WordCount.spec** e use o seguinte para o conteúdo. Isso define a topologia - os componentes, quantas instâncias devem ser criadas nos nós do cluster e como é o fluxo de dados entre eles. Isso é gravado usando a [linguagem de especificação da topologia](#spec).

        {
          :name "WordCount"
          :topology
            (nontx-topolopy
              "WordCount"

              {
                "spout"

                (spout-spec
                  (scp-spout
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["wordspout"]
                      "output.schema" {"default" ["sentence"]}
                    })

                  :p 1)
              }

              {
                "splitter"

                (bolt-spec
                  {
                    "spout" :shuffle
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["splitterbolt"]
                      "output.schema" {"default" ["word"]}
                    })

                  :p 1)

                "counter"

                (bolt-spec
                  {
                    "splitter" :global
                  }

                  (scp-bolt
                    {
                      "plugin.name" "WordCount.exe"
                      "plugin.args" ["counterbolt"]
                      "output.schema" {"default" ["word" "count"]}
                    })

                  :p 1)
              })

          :config
            {
              "topology.kryo.register" ["[B"]
            }
        }

4. Conecte-se ao cluster Storm usando a Área de Trabalho Remota e copie a pasta **bin\debug** de seu projeto WordCount local em seu cluster Storm. Por exemplo, copie-a na pasta **%storm_home%\examples** e a renomeie como **WordCount**.

3. Também copie **WordCount.spec** no servidor do HDInsight. Coloque o no diretório **%storm_home%\examples**.

4. No cluster HDInsight Storm, use o ícone da **Linha de comando do Storm** na área de trabalho para abrir uma linha de comando e use o seguinte comando para iniciar a topologia do WordCount.

        bin\runspec examples\WordCount.spec temp examples\WordCount

    Isso criará uma nova pasta chamada **temp** e usará a especificação e os arquivos da solução WordCount para criar um arquivo **WordCount.jar**, que será enviado ao Storm.

5. Após concluir o comando, abra a **interface do usuário do Storm** na área de trabalho. A topologia do **WordCount** deve ser listada. É possível selecionar a topologia na **interface do usuário do Storm** para exibir estatísticas.

6. Para parar a topologia, na **interface do usuário do Storm**, escolha **WordCount** e **Interromper** em **Ações da topologia**.

##Resumo

Nas etapas acima, você aprendeu como criar, testar e implantar uma topologia básica de contagem de palavras criada com o SCP. Para obter mais exemplos, consulte o diretório **%storm_home%\examples** em seu cluster HDInsight. Para obter informações mais detalhadas sobre o SCP, consulte as referências a seguir.

##Referência do SCP

###Interface do plug-in do SCP

Os aplicativos do SCP (conhecidos como plug-ins) são conectados ao pipeline do Storm. Um plug-in é um aplicativo de console .NET que implementa uma ou mais das seguintes interfaces.  

-	**ISCPSpout** - use ao implementar um spout não transacional
- 	**ISCPTxSpout** - use ao implementar um spout transacional
-	**ISCPBolt** - use ao implementar um bolt não transacional
-	**ISCPBatchBolt** - use ao implementar um bolt transacional

####ISCPPlugin

O ISCPPlugin é a interface básica da qual todas as outras interfaces do SCP são herdadas. Atualmente, trata-se de uma interface fictícia.  

    public interface ISCPPlugin
    {
    }  

####ISCPSpout

ISCPSpout é a interface para um spout não transacional.  

    public interface ISCPSpout : ISCPPlugin
    {
        void NextTuple(Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

Quando `NextTuple()` é chamado, o código pode emitir uma ou mais tuplas. Se não houver nada a ser emitido, esse método deverá retornar sem emitir nada.

`ACK()` e `Fail()` serão chamados somente quando ack está habilitado no arquivo de especificações. O `seqId` é usado para identificar a tupla reconhecida ou com falha. Se o reconhecimento for habilitado em uma topologia não transacional, a seguinte função de emissão deverá ser usada no spout:  

    public abstract void Emit(string streamId, List<object> values, long seqId);  

Se não há suporte para o ack na topologia não transacional, `Ack()` e `Fail()` podem ser deixadas como funções vazias.  

O parâmetro `parms` para essas funções é um objeto de dicionário vazio, o qual é reservado para uso futuro.  

> [AZURE.NOTE] `NextTuple()`, `Ack()`, e `Fail()` são todos chamados em um loop estreito em um único thread. Se não houver tuplas a emitir, considere usar `sleep` por um breve período de tempo (tal como 10 milissegundos) para conservar os ciclos da CPU.

####ISCPTxSpout

ISCPTxSpout é a interface para um spout transacional.  

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);
        void Ack(long seqId, Dictionary<string, Object> parms);
        void Fail(long seqId, Dictionary<string, Object> parms);
    }  

`NextTx()` é chamado para iniciar uma nova transação. O `seqId` é usado para identificar a transação, que também é usada em `Ack()` e `Fail()`. Os dados emitidos de `NextTx()` serão armazenados no ZooKeeper para dar suporte à reprodução. Como a capacidade do ZooKeeper é bastante limitada, você deve emitir somente metadados, e não dados em massa no spout transacional.  

O Storm reproduzirá uma transação automaticamente se falhar, por isso, Fail() não deve ser normalmente chamado. No entanto, se o SCP puder verificar os metadados emitidos pelo spout transacional, ele pode chamar Fail() se os metadados forem inválidos.

O parâmetro `parms` para essas funções é um objeto de dicionário vazio, o qual é reservado para uso futuro.

> [AZURE.NOTE] `NextTx()`, `Ack()`, e `Fail()` são todos chamados em um loop estreito em um único thread. Se não houver tuplas a emitir, considere usar `sleep` por um breve período de tempo (tal como 10 milissegundos) para conservar os ciclos da CPU.

####ISCPBolt

ISCPBolt é a interface para um bolt não transacional.  

    public interface ISCPBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
    }

Quando uma nova tupla está disponível, a função `Execute()` é chamada para processá-la.

####ISCPBatchBolt

ISCPBatchBolt é a interface para bolt transacional.  

    public interface ISCPBatchBolt : ISCPPlugin
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);
    }  

Quando uma nova tupla estiver disponível, a função `Execute()` será chamada para processá-lo. `FinishBatch()` será chamada quando a transação foi encerrada.

O parâmetro `parms` para essas funções é um objeto de dicionário vazio, o qual é reservado para uso futuro.

> [AZURE.NOTE] Bolts que implementam `ISCPBatchBolt` podem obter `StormTxAttempt` de `parms`. `StormTxAttempt` pode ser usado para determinar se uma tupla é a original ou uma tentativa de repetição. Isso geralmente é feito no bolt de confirmação, e é demonstrado no exemplo **HelloWorldTx**.  

###Modelo do objeto

O SCP.NET também oferece um conjunto simples de objetos chave para que os desenvolvedores realizem a programação.

* `Context` - fornece informações sobre o ambiente em execução ao aplicativo

* `StateStore` - oferece serviços de metadados, geração de sequência monotônica e coordenação sem espera. Abstrações de simultaneidade distribuídas de níveis mais altos podem ser baseadas no `StateStore`, incluindo bloqueios distribuídos, filas distribuídas, barreiras e serviços transacionais

* `SCPRuntime` - inicializa o ambiente de tempo de execução e inicia os plug-ins ao executar uma solução no Storm

* `LocalContext` - fornece métodos para serializar e desserializar as tuplas emitidas em arquivos locais ao testar um aplicativo localmente no Visual Studio

####Contexto

Cada instância ISCPPlugin (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) possui uma instância de Contexto correspondente. A funcionalidade fornecida pelo Contexto pode ser dividida em duas partes

* **Estática** -  disponível no processo inteiro do C#
* **Dinâmica** - disponível para a instância específica  

**Contexto estático**

* `public static ILogger Logger = null;` - fornecido para fins de registro  

* `public static SCPPluginType pluginType;` - Obtém o tipo de plug-in do processo de c#. Se o processo do C# for executado no modo de teste local (sem Java), o tipo de plug-in é "SCP_NET_LOCAL"

        public enum SCPPluginType
        {
            SCP_NET_LOCAL = 0,
            SCP_NET_SPOUT = 1,
            SCP_NET_BOLT = 2,
            SCP_NET_TX_SPOUT = 3,
            SCP_NET_BATCH_BOLT = 4
        }  

* `public static Config Config { get; set; }` - Obtém os parâmetros de configuração da JVM. Os parâmetros são passados por meio da JVM quando o plug-in é inicializado. `Config` contém dois dicionários

    * `public Dictionary<string, Object> stormConf { get; set; }` - contém parâmetros definidos pelo Storm

    * `public Dictionary<string, Object> pluginConf { get; set; }` - contém parâmetros definidos pelo SCP

* `public static TopologyContext TopologyContext { get; set; } `  - Obtém o contexto de topologia. É mais útil para os componentes com vários paralelismos. O exemplo a seguir demonstra como acessar o contexto da topologia

        if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
        {
            Context.Logger.Info("TopologyContext info:");
            TopologyContext topologyContext = Context.TopologyContext;
            Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
            taskIndex = topologyContext.GetThisTaskIndex();
            Context.Logger.Info("taskIndex: {0}", taskIndex);
            string componentId = topologyContext.GetThisComponentId();
            Context.Logger.Info("componentId: {0}", componentId);
            List<int> componentTasks = topologyContext.GetComponentTasks(componentId);
            Context.Logger.Info("taskNum: {0}", componentTasks.Count);
        }

**Contexto dinâmico**

* `public void DeclareComponentSchema(ComponentStreamSchema schema);` - declara o esquema de entrada e saída para fluxos. O exemplo a seguir declara um esquema de entrada que consiste em uma tupla de cadeia única e um esquema de saída que consiste em uma tupla de cadeia e uma tupla de números inteiros

        this.ctx = context;
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));

* `public abstract void Emit(List<object> values);` - emite uma ou mais tuplas para o fluxo padrão. O exemplo a seguir emite duas tuplas para o fluxo padrão

        this.ctx.Emit(new Values(word, count));

* `public abstract void Emit(string streamId, List<object> values);` - emite uma ou mais tuplas para o fluxo especificado. O exemplo a seguir emite duas tuplas para o fluxo chamado 'mystream'

        this.ctx.Emit("mystream", new Values(word, count));

Ao usar spouts e bolts não transacionais que têm o reconhecimento habilitado, use o seguinte.

* `public abstract void Emit(string streamId, List<object> values, long seqId);` - emite uma ou mais tuplas e um identificador de sequência **por meio de um spout** para o fluxo especificado. O exemplo a seguir emite uma tupla e um identificador de sequência para o fluxo padrão

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(word), lastSeqId);

* `public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);` - emite uma ou mais tuplas e um identificador de sequência **por meio de um bolt** para o fluxo especificado. As tuplas emitidas são ancoradas às tuplas de entrada especificadas como `anchors`. Isso habilita os reconhecimentos a voltar o fluxo para o pipeline ao seguir a cadeia de tuplas de entrada/saída. O exemplo a seguir emite uma tupla e um identificador de sequência para o fluxo padrão e ancora as tuplas emitidas nas tuplas de entrada contidas em `tuple`

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word));

* `public abstract void Ack(SCPTuple tuple);` - reconhece uma tupla. Invoca a função `ISCPSpout.Ack` do spout que produziu originalmente a tupla

* `public abstract void Fail(SCPTuple tuple);` - falha uma tupla. Invoca a função `ISCPSpout.Fail` do spout que produziu originalmente a tupla

####StateStore

Os aplicativos do SCP podem usar o objeto `State` para persistir algumas informações no ZooKeeper, especialmente para uma topologia transacional. Isso permite que os spouts transacionais recuperem o estado do ZooKeeper e reinicie o pipeline em caso de falha.  

O objeto `StateStore` fornece os seguintes métodos.

* `public static StateStore Get(string storePath, string connStr);` - obtém uma `StateStore` para a cadeia de conexão e o caminho fornecidos

* `public State Create();`-cria um novo objeto `State` nesta instância do armazenamento de estado

* `public IEnumerable<State> GetUnCommitted();` - obtém todos os objetos `State` não confirmados, exceto estados anulados

* `public IEnumerable<State> States();`-obtém todos os objetos 'State' na `StateStore`


* `public T Get<T>(string info = null);` - obtém um objeto `State` ou `Registry`

    * `info` - o nome `Registry` a obter. Usado apenas ao recuperar um objeto `Registry`

    * `T` - tipo de `State` ou `Registry`

* `public IEnumerable<Registry> Commited();` - obtém objetos `Registry` que contêm `State` confirmado

* `public IEnumerable<Registry> Aborted();` - obtém objetos `Registry` que contêm `State` anulado

* `public State GetState(long stateId)` - obtém o objeto `State` para a ID de estado especificada

O **Estado** oferece os métodos a seguir.

* `public void Commit(bool simpleMode = true);`-Define o status do objeto `State` a confirmar

    > [AZURE.NOTE] Quando `simpleMode` é configurado como true, ele simplesmente exclui o ZNode correspondente no ZooKeeper. Caso contrário, ele exclui o ZNode atual e adiciona um novo nó no `COMMITTED_PATH`.

* `public void Abort();` - define o status do objeto `State` a anular

* `public void PutAttribute<T>(string key, T attribute);` - define um valor de atributo para a chave fornecida

    * `key` - a chave define o atributo para

    * `attribute` - o valor do atributo

* `public T GetAttribute<T>(string key);` - obtém o valor de atributo para a chave fornecida

####SCPRuntime

SCPRuntime oferece os seguintes métodos.

* `public static void Initialize();`-inicializa o ambiente de tempo de execução do SCP. Quando chamado, o processo do C# se conecta à JVM e obtém os parâmetros de configuração e o contexto da topologia

* `public static void LaunchPlugin(newSCPPlugin pluginDelegate);` - inicia o circuito de processamento de mensagens. Nesse loop, o plug-in do C# receberá mensagens da JVM (incluindo tuplas e sinais de controle) e as processará

    * `pluginDelegate` - um representante que pode retornar um objeto que implementa ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt

####LocalContext


* `List<SCPTuple> RecvFromMsgQueue();` - recebe tuplas da fila

* `void WriteMsgQueueToFile(string filepath, bool append = false);` - persiste tuplas para o arquivo

* `void ReadFromFileToMsgQueue(string filepath);` - faz a leitura de tuplas do arquivo


###<a id="spec"></a>Linguagem de especificação da topologia

A Especificação da Topologia do SCP é uma linguagem específica do domínio para descrever e configurar as topologias do SCP. Ela é baseada na [DSL Clojure](http://storm.incubator.apache.org/documentation/Clojure-DSL.html) do Storm.  

Use o seguinte para definir uma topologia:

|Novas funções|	Parâmetros|	Descrição
|-------------|-----------|-----------
|**tx-topolopy**|	topology-name<br> spout-map<br> bolt-map|	 Defina uma topologia transacional com o nome da topologia, o mapa de definição de spouts e o mapa de definição de bolts
|**scp-tx-spout**|	exec-name<br> args<br> fields|	Defina um spout transacional. Executará um aplicativo com o ***exec-name*** usando ***args***.<br><br>Os ***fields*** são os campos de saída do spout
|**scp-tx-batch-bolt**|	exec-name<br> args<br> fields| 	Defina um bolt em lote transacional. Executará um aplicativo com o ***exec-name*** usando ***args***.<br><br>Os Fields são os campos de saída do bolt.
|**scp-tx-commit-bolt**|	exec-name<br>args<br>fields|	Defina um bolt confirmador transacional. Executará um aplicativo com o ***exec-name*** usando args.<br><br>Os ***fields*** são os campos de saída do bolt
|**nontx-topolopy**|	topology-name<br> spout-map<br>bolt-map|	Define uma topologia não transacional com o nome de topologia,  o mapa de definição dos spouts e o mapa de definição dos bolts
|**scp-spout**|	exec-name<br>args<br>fields<br>parameters|	Defina um spout não transacional. Executará um aplicativo com o ***exec-name*** usando ***args***.<br><br>Os ***fields*** são os campos de saída do spout<br><br>O ***parameters*** é opcional, utilizando-o para especificar alguns parâmetros como "nontransactional.ack.enabled".
|**scp-bolt**|	exec-name<br>args<br>fields<br>parameters|	Defina um bolt não transacional. Executará um aplicativo com o ***exec-name*** usando ***args***.<br><br>Os ***fields*** são os campos de saída do bolt<br><br>O ***parameters*** é opcional, utilizando-o para especificar alguns parâmetros como "nontransactional.ack.enabled".

As seguintes são palavras-chave que podem ser usadas ao definir uma topologia

|Palavras-chave|	Descrição
|---------|------------
|**:name**|	Defina o nome da topologia
|**:topology**|	Defina a Topologia usando as funções acima e as integradas.
|**:p**|	Defina a dica de paralelismo para cada spout ou bolt.
|**:config**|	Defina o parâmetro de configuração ou atualize os existentes
|**:schema**|	Defina o esquema do fluxo.

Parâmetros usados com frequência

|Parâmetro|	Descrição
|---------|------------
|**"plugin.name"**|	nome do arquivo exe do plugin do C#
|**"plugin.args"**|	args do plugin
|**"output.schema"**|	Esquema de saída
|**"nontransactional.ack.enabled"**|	Se o reconhecimento está ativado para a topologia não transacional

As especificações da topologia podem ser enviadas diretamente ao cluster Storm para execução por meio do comando ***runspec***, que está localizado no diretório **%storm_home%\bin** nos clusters do Storm.  

    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
     ex: runSpec examples\HelloWorld\HelloWorld.spec target examples\HelloWorld\Target

> [AZURE.NOTE] O parâmetro ***resource-dir*** é opcional, você precisa especificá-lo quando quiser executar um aplicativo do C#, e esse diretório conterá o aplicativo, as dependências e as configurações.  

O parâmetro ***classpath*** também é opcional. Ele é usado para especificar o caminho de classe do Java se o arquivo de especificações contiver spout ou bolt do Java.  

###Recursos diversos

####Declaração de esquema de entrada e saída

Ao chamar `Emit()`, a plataforma serializa a tupla em uma matriz de bytes e a transfere para a JVM. Em seguida, o Storm transfere essa tupla aos destinos. Os bolts recebem a tupla. Para os bolts C#, a tupla é recebida da JVM e convertida de volta ao tipo original.

Para dar suporte à serialização e desserialização, você deve declarar o esquema das entradas e saídas. Eles são definidos como objetos `Dictionary<string, List<Type>`, onde a chave é a ID do fluxo e o valor é o `Types` de tuplas que serão emitidas. O componente pode declarar vários fluxos.

    public class ComponentStreamSchema
    {
        public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
        public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
        public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
        {
            InputStreamSchema = input;
            OutputStreamSchema = output;
        }
    }

O objeto `Context` fornece o `DeclareComponentSchema`, o qual pode ser usado para declarar o esquema para serialização/desserialização.

     public void DeclareComponentSchema(ComponentStreamSchema schema)

> [AZURE.NOTE] Você deve assegurar que as tuplas emitidas obedeçam ao esquema definido para esse fluxo ou o sistema receberá uma exceção de tempo de execução.  

####Suportes a múltiplos fluxos

Você pode emitir vários fluxos ao fazer várias chamadas ao `Emit()`, especificando o parâmetro `streamId` de cada fluxo a ser gravado.

> [AZURE.NOTE] A emissão para um fluxo não existente causará exceções de tempo de execução.

####Agrupamento de campos

A agrupamento de campos integrado no Storm não funciona adequadamente no SCP.NET. Ao usar um proxy de dados na JVM, todos os tipos de dados de campo são na verdade `byte[]` e o agrupamento de campos usa o código hash do objeto  `byte[]` para realizar o agrupamento. O código hash do objeto `byte[]` é o endereço desse objeto na memória, de modo que o agrupamento será errado para dois objetos `byte[]` que compartilham o mesmo conteúdo, mas não o mesmo endereço.

Para solucionar esse problema, use `scp-field-group` em sua especificação. Isso utilizará o conteúdo do `byte[]` para realizar o agrupamento. A seguir, veja um exemplo de como usar isso em uma especificação.  

    (bolt-spec
        {
            "spout_test""" (scp-field-group :non-tx [0,1])
        }
        ...
    )  

Esse exemplo faz o seguinte.

* `scp-field-group` - usar agrupamento personalizado  
* `:tx` ou `:non-tx` - indica se isto é transacional ou não transacional
* `[0,1]` - use um hashset de IDs de campo, começando por 0.  

####Topologia híbrida

A maioria dos spouts, bolts e topologias nativas do Storm são implementadas em Java. Para dar suporte à reutilização desses componentes nas soluções que usam os componentes do C#, o SCP permite a criação de topologias híbridas definidas na especialização.

* **Spout ou bolt do Java** - no arquivo de especificações, `scp-spout` e `scp-bolt` também podem ser usados para especificar os spouts e bolts do Java. O exemplo a seguir demonstra a especificação de um spout do Java com um nome de classe `microsoft.scp.example.HybridTopology.Generator`

        (spout-spec
          (microsoft.scp.example.HybridTopology.Generator.)
          :p 1)

* **Caminho de classe do Java** - ao usar os spouts ou bolts do Java, você deve compilar primeiro o spout ou bolt em um arquivo JAR. Em seguida, adicione o caminho de classe do Java com o parâmetro `-cp` ao usar o comando **runSpec**. O exemplo a seguir inclui arquivos JAR localizados no diretório **examples\HybridTopology\java\target**.  

        bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*  


* **Serialização e desserialização entre Java e C#** - Para serializar objetos entre Java e C#, use o `CustomizedInteropJSONSerializer`

    > [AZURE.NOTE] No momento, o `CustomizedInteropJSONSerializer` dá suporte apenas aos **spouts do Java ** e aos **bolts do C#**.

    * Especifique o serializador dos componentes Java no arquivo de especificações

            (scp-bolt
              {
                "plugin.name" "HybridTopology.exe"
                "plugin.args" ["displayer"]
                "output.schema" {}
                "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
              })  

    * Use o serializador em seus componentes C#

            Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
            inputSchema.Add("default", new List<Type>() { typeof(Person) });
            this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
            this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());  

    A implementação padrão deve lidar com a maioria dos casos se o tipo de dados não for muito complexo.  Se o tipo de dados for muito complexo ou se o desempenho da implementação padrão não atender aos requisitos, você pode criar uma implementação personalizada para adequar às suas necessidades usando as seguintes interfaces

    **Interface de serialização em Java**  

            public interface ICustomizedInteropJavaSerializer {
                public void prepare(String[] args);
                public List<ByteBuffer> serialize(List<Object> objectList);
            }  

    **Interface de desserialização em C#**

            public interface ICustomizedInteropCSharpDeserializer
            {
                List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
            }  

###Modo de host do SCP

O modo de host permite compilar o seu projeto em uma DLL e hospedá-la usando o **SCPHost.exe**. Essa é a maneira recomendada de hospedar a sua solução em um ambiente de produção. Ao usar o modo de host, o arquivo de especificações listará `SCPHost.exe` como o plug-in.

A seguir, veja um exemplo de um arquivo de especificações que usou um modo de host para o exemplo HelloWorld.

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

* **HelloWorld.dll** - o assembly que contém os spouts e bolts
* **Scp.App.HelloWorld.Generator** - o nome de classe de um spout contido em **HelloWorld.dll**
* **Get** - O método para invocar a obtenção de uma instância do spout


##Exemplos de programação do SCP

Os aplicativos de exemplo a seguir gravados com o SCP podem ser encontrados em seu cluster Storm no HDInsight em **%storm_home%\examples**.

* **HelloWorld** - HelloWorld é um exemplo muito simples de SCP.Net e é semelhante ao exemplo de contagem de palavras usado anteriormente neste artigo. Ele usa uma topologia não transacional com um spout chamado **generator** e dois bolts chamados **splitter** e **counter**. O spout **generator** gerará aleatoriamente algumas sentenças e emitirá essas sentenças ao **splitter**. O bolt **splitter** dividirá as sentenças em palavras e as emitirá ao bolt **counter**. O bolt **counter** usa um dicionário para registrar o número de ocorrências de cada palavra.

    Há dois arquivos de especificações, **HelloWorld.spec** e **HelloWorld_EnableAck.spec** para este exemplo. Ao usar o **HelloWorld_EnableAck.spec**, o exemplo reconhecerá as tuplas que passarem pelos bolts, no entanto, o **splitter** é desenvolvido para provocar de modo aleatório a falha de alguns bolts para demonstrar falhas de manipulação em topologias não transacionais.

* **HelloWorldTx** - Um exemplo de como implementar uma topologia transacional. Possui um spout chamado **generator**, um bolt de lote chamado **partial-count** e um bolt de confirmação chamado **count-sum**. Existem também três arquivos txt pré-criados para serem usados com essa topologia: **DataSource0.txt**, **DataSource1.txt** e **DataSource2.txt**.

    Em cada transação, o spout escolhe aleatoriamente dois arquivos e emite os dois nomes dos arquivos ao bolt **partial-count**. O bolt obtém o nome do arquivo da tupla recebida, depois, abre o arquivo e conta o número de palavras nesse arquivo. Por fim, emite o número de palavras ao bolt **count-sum**. O bolt **count-sum** resume a contagem total.  

    Para alcançar a semântica "exatamente uma vez", o bolt **count-sum** precisa determinar se está processando uma transação reproduzida ou não. Neste exemplo, ele possui uma variável do membro estático:  

        public static long lastCommittedTxId = -1;  

    Quando uma instância do bolt é criada, a tentativa da transação (`txAttempt`,) dos parâmetros de entrada é obtida:  

        public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
        {
            /* for transactional topology, we can get txAttempt from the input parms */
            if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
            {
                StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
                return new CountSum(ctx, txAttempt);
            }
            else
            {
                throw new Exception("null txAttempt");
            }
        }  

    Quando `FinishBatch()` é chamado, `lastCommittedTxId` será atualizado se não for uma transação reproduzida.  

        public void FinishBatch(Dictionary<string, Object> parms)
        {
            /* judge whether it is a replayed transaction? */
            bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

            if (!replay)
            {
                /* If it is not replayed, update the toalCount and lastCommittedTxId vaule */
                totalCount = totalCount + this.count;
                lastCommittedTxId = this.txAttempt.TxId;
            }
            ... ...
        }  

* **HybridTopology** - Essa topologia contém um spout do Java e um bolt do C#. Ela usa a implementação de serialização e desserialização padrão fornecida pelo SCP. Consulte **%storm_home%examples\HybridTopology\HybridTopology.spec** para obter detalhes e **SubmitTopology.bat** para saber como especificar o caminho de classe do Java ao enviar a topologia.  


[1]: ./media/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application-01.png
<!--HONumber=42-->
