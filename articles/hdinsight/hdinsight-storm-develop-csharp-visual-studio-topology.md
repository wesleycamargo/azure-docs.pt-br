---
title: Topologias Apache Storm com Visual Studio e C# | Microsoft Docs
description: Aprenda a criar topologias Storm em C# criando uma topologia de contagem de palavras simples no Visual Studio usando as ferramentas do HDInsight para Visual Studio.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 380d804f-a8c5-4b20-9762-593ec4da5a0d
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 1336f95809712779ca5c4c68237a3a8c6d630af4
ms.openlocfilehash: 337e1c185ef8989345ec3abb60f6b255dbc9ef4f
ms.lasthandoff: 02/14/2017


---
# <a name="develop-c-topologies-for-apache-storm-on-hdinsight-using-hadoop-tools-for-visual-studio"></a>Desenvolver topologias C# para Apache Storm no HDInsight usando ferramentas do Hadoop para Visual Studio

Saiba como criar uma topologia Storm C# usando as ferramentas do HDInsight para Visual Studio. Este documento percorre o processo de criação de um novo projeto do Storm no Visual Studio, os testes locais e a implantação em um Apache Storm no cluster HDInsight.

Você também aprende a criar topologias híbridas que usam componentes C# e Java.

> [!IMPORTANT]
> Embora as etapas neste documento dependam de um ambiente de desenvolvimento do Windows com Visual Studio, o projeto compilado pode ser enviado a um cluster HDInsight baseado em Windows ou Linux. __Somente os clusters baseados em Linux criados depois de 28/10/2016 são compatíveis com as topologias do SCP.NET__.
> 
> Para usar uma topologia do C# com um cluster baseado em Linux, você deverá atualizar o pacote NuGet Microsoft.SCP.Net.SDK usado pelo seu projeto para a versão 0.10.0.6 ou superior. A versão do pacote também deve coincidir com a versão principal do Storm instalada no HDInsight. Por exemplo, o HDInsight versões 3.3 e 3.4 usam o Storm versão 0.10.x, enquanto o HDInsight 3.5 usa o Storm 1.0. x.
> 
> As topologias C# em clusters baseados em Linux devem usar o .NET 4.5 e o Mono para execução no cluster HDInsight. A maioria das coisas funciona, mas você deve verificar o documento [Compatibilidade do Mono](http://www.mono-project.com/docs/about-mono/compatibility/) em busca de possíveis incompatibilidades.


## <a name="prerequisites"></a>Pré-requisitos

* [Java](https://java.com) 1.7 ou posterior em seu ambiente de desenvolvimento. O Java é usado para o pacote da topologia quando ela é enviada ao cluster HDInsight.

  * A variável de ambiente **JAVA_HOME** deve apontar ao diretório que contém o Java.
  * O diretório **%JAVA_HOME%/bin** deve estar no caminho

* Uma das seguintes versões do Visual Studio:
  
  * Visual Studio 2012 com [Atualização 4](http://www.microsoft.com/download/details.aspx?id=39305)
  * Visual Studio 2013 com [Atualização 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)
  * Visual Studio 2015 ou [Visual Studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* SDK 2.9.5 do Azure ou posterior

* Ferramentas do HDInsight para o Visual Studio: consulte [Introdução ao uso das Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) para instalar e configurar as ferramentas do HDInsight para Visual Studio.
  
  > [!NOTE]
  > Não há suporte para ferramentas de HDInsight para o Visual Studio no Visual Studio Express

* Apache Storm no cluster HDInsight: consulte [Introdução ao Apache Storm no HDInsight](hdinsight-apache-storm-tutorial-get-started.md) para obter as etapas para criar um cluster.

  > [!IMPORTANT]
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="templates"></a>Modelos

As ferramentas de HDInsight para o Visual Studio fornecem os seguintes modelos:

| Tipo de projeto | Demonstra |
| --- | --- |
| Aplicativo Storm |Um projeto de topologia Storm vazio |
| Amostra de gravador do SQL Azure Storm |Como gravar em um Banco de Dados SQL do Azure |
| Amostra de leitor de Banco de Dados de Documentos do Storm |Como ler por meio do Banco de Dados de Documentos do Azure |
| Amostra de gravador DO Banco de Dados de Documentos do Storm |Como gravar no Banco de Dados de Documentos do Azure |
| Amostra de leitor de Hub de Eventos do Storm |Como ler por meio de Hubs de Eventos do Azure |
| Amostra do gravador de Hub de Eventos do Storm |Como gravar em Hubs de Eventos do Azure |
| Amostra de leitor HBase do Storm |Como ler por meio de HBase em clusters HDInsight |
| Amostra de gravador HBase do Storm |Como gravar no HBase em clusters HDInsight |
| Amostra híbrida do Storm |Como usar um componente Java |
| Amostra do Storm |Uma topologia básica de contagem de palavras |

Nas etapas neste documento, você usará o tipo de projeto de aplicativo Storm básico para criar uma nova topologia.

### <a name="hbase-templates-notes"></a>Notas de modelos de HBase

Os modelos de leitor e gravador do HBase usam a API REST do HBase para se comunicar com um HBase no cluster HDInsight, não a API Java do HBase.

### <a name="eventhub-templates-notes"></a>Notas de modelos do EventHub

> [!IMPORTANT]
> O componente spout do EventHub baseado em Java incluído com o modelo de Leitor do EventHub não funciona com o Storm no HDInsight versão 3.5. Em vez disso, use o componente spout do EventHub [https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar](https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar).

Para obter um exemplo de topologia que usa esse componente e funciona com o Storm no HDInsight 3.5, veja [https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Criar uma topologia C#

1. Se você ainda não instalou a versão mais recente das Ferramentas do HDInsight para Visual Studio, consulte [Introdução ao uso das Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

2. Abra o Visual Studio, selecione **Arquivo** > **Novo** e **Projeto**.

3. Na caixa de diálogo **Novo Projeto**, expanda **Instalados** > **Modelos** e escolha **HDInsight**. Na lista de modelos, selecione **Aplicativo Storm**. Na parte inferior da tela, insira **WordCount** como o nome do aplicativo.
   
    ![imagem](./media/hdinsight-storm-develop-csharp-visual-studio-topology/new-project.png)

4. Quando o projeto tiver sido criado, você deverá ter os seguintes arquivos:
   
   * **Program.cs**: define a topologia para seu projeto. Observe que uma topologia padrão consistindo em um spout e um bolt é criada por padrão.

   * **Spout.cs**: um spout de exemplo que emite números aleatórios.

   * **Bolt.cs**: um bolt de exemplo que mantém uma contagem de números emitidos pelo spout.
     
     Como parte da criação do projeto, os [Pacotes SCP.NET](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/) mais recentes são baixados do NuGet.
     
     [!INCLUDE [scp.net version important](../../includes/hdinsight-storm-scpdotnet-version.md)]

Nas próximas seções, você modificará esse projeto em um aplicativo WordCount básico.

### <a name="implement-the-spout"></a>Implementar o spout

1. Abra **Spout.cs**. Os spouts são usados para ler os dados em uma topologia de uma fonte externa. Os principais componentes de um spout são:
   
   * **NextTuple**: chamado pelo Storm quando o spout pode emitir novas tuplas.

   * **Ack** : (apenas topologia transacional): lida com confirmações iniciadas por outros componentes na topologia, para cadeias de identificação enviadas deste spout. Confirmar uma tupla informa ao spout que ele foi processado com êxito por componentes downstream.

   * **Falha** : (apenas topologia transacional) - lida com tuplas que falham ao processar outros componentes na topologia. Isso oferece a oportunidade para emitir novamente a tupla para que ela possa ser processada novamente.

2. Substitua o conteúdo da classe **Spout** com o seguinte. Isso cria um spout que emite aleatoriamente uma frase para a topologia.
    
    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```
   
    Dedique uns momentos para ler os comentários para entender o que esse código faz.

### <a name="implement-the-bolts"></a>Implementar os bolts

1. Exclua o arquivo **Bolt.cs** existente do projeto.

2. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Novo item**. Na lista, selecione **Storm Bolt** e digite **Splitter.cs** como o nome. Repita isso para criar um segundo bolt chamado **Counter.cs**.
   
   * **Splitter.cs**: implementará um bolt que divide as frases em palavras individuais e emite um novo fluxo de palavras.

   * **Counter.cs**: implementará um bolt que conta cada palavra e emite um novo fluxo de palavras e a contagem de cada palavra.
     
     > [!NOTE]
     > Esses bolts simplesmente leem e gravam em fluxos, mas você também pode usar um bolt para se comunicar com um banco de dados ou serviço.

3. Abra **Splitter.cs**. Observe que ele possui apenas um método por padrão: **Execute**. Isso é chamado quando o bolt recebe um cadeia de identificação para processamento. Aqui, você pode ler e processar tuplas de entradas e emitir tuplas de saída.

4. Substitua o conteúdo da classe **Splitter** com o seguinte código:
    
    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```
   
    Dedique uns momentos para ler os comentários para entender o que esse código faz.

5. Abra **Counter.cs** e substitua o conteúdo da classe com o seguinte:
    
    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```
   
    Dedique uns momentos para ler os comentários para entender o que esse código faz.

### <a name="define-the-topology"></a>Definir a topologia

Spouts e bolts são organizados em um gráfico, que define como os dados fluem entre componentes. Para essa topologia, o gráfico é o seguinte:

![imagem de como os componentes são organizados](./media/hdinsight-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

As frases são emitidas do spout, que são distribuídos para instâncias do bolt Splitter. O bolt Splitter divide as frases em palavras, que são distribuídas para o bolt Contador.

Como a contagem de palavras é mantida localmente na instância Contador, queremos nos certificar de que palavras específicas sejam transmitidas para a mesma instância do bolt Contador, portanto, temos apenas uma instância mantendo o controle de uma palavra específica. Mas para o bolt Splitter, não importa qual bolt recebe uma frase, portanto, desejamos apenas carregar frases de equilíbrio entre essas instâncias.

Abra **Program.cs**. O método importante é **GetTopologyBuilder**, que é usado para definir a topologia enviada ao Storm. Substitua o conteúdo de **GetTopologyBuilder** pelo seguinte código para implementar a topologia descrita anteriormente:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

Dedique uns momentos para ler os comentários para entender o que esse código faz.

## <a name="submit-the-topology"></a>Enviar a topologia

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Enviar para o Storm no HDInsight**.
   
   > [!NOTE]
   > Se solicitado, insira as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, faça o logon naquela que contém seu Storm no cluster HDInsight.

2. Selecione seu Storm no cluster HDInsight no menu suspenso **Cluster Storm** e selecione **Enviar**. Você pode monitorar se o envio obteve êxito ou não usando a janela **Saída** .

3. Depois que a topologia tiver sido enviada com êxito, as **Topologias Storm** para o cluster deverão aparecer. Selecione a topologia **WordCount** na lista para exibir informações sobre a topologia em execução.
   
   > [!NOTE]
   > Você também pode exibir **Topologias Storm** no **Gerenciador de Servidores**: expanda **Azure** > **HDInsight**, clique com o botão direito em um Storm no cluster HDInsight e selecione **Exibir Topologias Storm**.

    Use os links para os spouts ou bolts para exibir informações sobre esses componentes. Uma nova janela será aberta para cada item selecionado.

4. Na exibição **Resumo da Topologia**, selecione **Eliminar** para parar a topologia.
   
   > [!NOTE]
   > As topologias Storm continuarão em execução até serem paradas ou até que o cluster seja excluído.

## <a name="transactional-topology"></a>Topologia transacional

A topologia anterior não é transacional. Os componentes da topologia não implementarão qualquer funcionalidade para reproduzir novamente mensagens se o processamento falhar por causa de um componente na topologia. Para uma topologia transacional de exemplo, crie um novo projeto e selecione **Amostra do Storm** como o tipo de projeto.

As topologias transacionais implementam o seguinte para suportar a reprodução de dados:

* **Cache de metadados**: o spout deve armazenar metadados sobre os dados emitidos para que os dados possam ser recuperados e emitidos novamente caso ocorra uma falha. Como os dados emitidos pela amostra são pequenos, os dados brutos de cada tupla são armazenados em um dicionário para reprodução.

* **Ack**: cada bolt na topologia pode chamar `this.ctx.Ack(tuple)` para confirmar que processou com êxito uma tupla. Quando todos os bolts confirmarem a tupla, o método `Ack` do spout será chamado. Isso permite que o spout remova os dados armazenados em cache para reprodução, já que os dados foram totalmente processados.

* **Falha**: cada bolt pode chamar `this.ctx.Fail(tuple)` para indicar que o processamento falhou para uma tupla. A falha é propagada para o método `Fail` do spout, onde a tupla pode ser reproduzida usando os metadados armazenados em cache.

* **ID de Sequência**: ao emitir uma tupla, uma ID de sequência poderá ser especificada. Esse deve ser um valor que identifica a cadeia de identificação para o processamento da reprodução (Confirmação e Falha). Por exemplo, o spout do projeto **Amostra do Storm** usa o seguinte ao emitir dados:
  
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);
  
    Isso emite uma nova tupla que contém uma frase para o fluxo padrão com o valor da ID de sequência contido em **lastSeqId**. Neste exemplo, **lastSeqId** é simplesmente incrementado para todas as tuplas emitidas.

Conforme demonstrado no projeto **Amostra do Storm** , se um componente for transacional, não poderá ser definido no tempo de execução com base na configuração.

## <a name="hybrid-topology"></a>Topologia híbrida

As ferramentas do HDInsight para Visual Studio também podem ser usadas para criar topologias híbridas, onde alguns componentes são C# e outros são Java.

Para um exemplo de topologia híbrida, crie um novo projeto e selecione **Amostra de Híbrido do Storm**. Isso criará um exemplo totalmente comentado com várias topologias que demonstram o seguinte:

* **Spout Java** e **bolt C#**: definidos em **HybridTopology_javaSpout_csharpBolt**
  
    * Uma versão transacional é definida em **HybridTopologyTx_javaSpout_csharpBolt**

* **Spout C#** e **bolt Java**: definidos em **HybridTopology_csharpSpout_javaBolt**
  
    * Uma versão transacional é definida em **HybridTopologyTx_csharpSpout_javaBolt**
  
  > [!NOTE]
  > Essa versão também demonstra como usar o código Clojure de um arquivo de texto como um componente Java.


Para alterar entre a topologia que é usada quando o projeto é enviado, basta mover a instrução `[Active(true)]` para a topologia que você deseja usar antes de enviá-la para o cluster.

> [!NOTE]
> Todos os arquivos Java necessários são fornecidos como parte desse projeto na pasta **JavaDependency** .


O seguinte é usado ao criar e enviar uma topologia híbrida:

* **JavaComponentConstructor** deve ser usado para criar uma nova instância da classe Java para um spout ou bolt.

* **microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer** deve ser usado para serializar dados de entrada ou saída de componentes Java desde objetos Java até o JSON.

* Ao enviar a topologia para o servidor, você deve usar a opção **Configurações adicionais** para especificar **Caminhos de arquivo Java**. O caminho especificado deve ser o diretório que contém os arquivos JAR com suas classes Java.

### <a name="azure-event-hubs"></a>Hubs de eventos do Azure

A versão 0.9.4.203 do SCP.Net introduz uma classe e um método novos especificamente para trabalhar com o spout do Hub de Eventos (um spout Java que lê do Hub de Eventos.) Ao criar uma topologia que use esse spout, use os seguintes métodos:

* **EventHubSpoutConfig**: cria um objeto que contém a configuração do componente spout.

* **TopologyBuilder.SetEventHubSpout**: adiciona o componente Spout do Hub de Eventos à topologia.

> [!NOTE]
> Embora eles tornem mais fácil trabalhar com o Spout do Hub de Eventos do que outros componentes Java, você ainda deve usar o CustomizedInteropJSONSerializer para serializar os dados produzidos pelo spout.

## <a name="a-idconfigurationmanagerausing-configurationmanager"></a><a id="configurationmanager"></a>Usando o ConfigurationManager

Não use o ConfigurationManager para recuperar valores de configuração dos componentes bolt e spout; isso pode gerar uma exceção de ponteiro nulo. Em vez disso, a configuração do seu projeto é passada para a topologia Storm como um par de chave/valor no contexto da topologia. Cada componente que se baseia em valores de configuração deve recuperá-los no contexto durante a inicialização.

O seguinte código demonstra como recuperar esses valores:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Se você usar um método `Get` para retornar uma instância do seu componente, será preciso garantir que ele passe os parâmetros `Context` e `Dictionary<string, Object>` ao construtor. O seguinte exemplo é um método `Get` básico que passa corretamente esses valores:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Como atualizar o SCP.NET

As versões recentes do SCP.NET oferecem suporte à atualização de pacote por meio do NuGet. Quando uma nova atualização estiver disponível, você receberá uma notificação de atualização. Para verificar manualmente uma atualização, execute estas etapas:

1. No **Gerenciador de Soluções**, clique com o botão direito no projeto e escolha **Gerenciar Pacotes NuGet**.

2. No Gerenciador de pacotes, selecione **Atualizações**. Se uma atualização estiver disponível, ela será listada. Clique no botão **Atualização** referente ao pacote para instalá-lo.

> [!IMPORTANT]
> Se o seu projeto foi criado com uma das versões anteriores do SCP.NET que não usou o NuGet para atualizações de pacote, você deve executar as seguintes etapas para atualizar para a nova versão:
> 
> 1. No **Gerenciador de Soluções**, clique com o botão direito no projeto e escolha **Gerenciar Pacotes NuGet**.
> 2. Usando o campo **Pesquisa**, faça a pesquisa e adicione **Microsoft.SCP.Net.SDK** ao projeto.

## <a name="troubleshooting"></a>Solucionar problemas

### <a name="null-pointer-exceptions"></a>Exceções de ponteiro nulo

Ao usar uma topologia C# com um cluster HDInsight baseado em Linux, os componentes bolt e spout que usam o ConfigurationManager para ler configurações em tempo de execução podem retornar exceções de ponteiro nulo. Isso acontece porque a configuração do domínio carregado não é do assembly que contém o projeto.

A configuração do seu projeto é passada para a topologia Storm como um par de chave/valor no contexto da topologia e pode ser recuperada do objeto de dicionário que é passado para seus componentes quando eles são inicializados.

O exemplo a seguir demonstra o carregamento dos valores de configuração do contexto da topologia. Confira a seção [ConfigurationManager](#configurationmanager) deste documento.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Ao usar uma topologia C# com um cluster HDInsight baseado em Linux, você pode encontrar o seguinte erro:

    System.TypeLoadException: Failure has occurred while loading a type.

Isso geralmente ocorre quando você está usando um binário que não é compatível com a versão do .NET aceita pelo Mono.

Para clusters HDInsight baseados em Linux, você deve se certificar de que o projeto usa binários compilados para o .NET 4.5.

### <a name="test-a-topology-locally"></a>Testar uma topologia localmente

Embora seja fácil implantar uma topologia em um cluster, em alguns casos poderá ser necessário testar uma topologia localmente. Use as seguintes etapas para executar e testar a topologia de exemplo nesse tutorial localmente em seu ambiente de desenvolvimento.

> [!WARNING]
> Testes locais funcionam somente para topologias C# básicas. Você não deve usar o teste local para topologias híbridas ou topologias que usam vários fluxos.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Propriedades**. Nas propriedades do projeto, altere **Tipo de saída** para **Aplicativo de Console**.
   
    ![tipo de saída](./media/hdinsight-storm-develop-csharp-visual-studio-topology/outputtype.png)
   
   > [!NOTE]
   > Lembre-se de alterar o **Tipo de saída** de volta para **Biblioteca de Classes** antes de implantar a topologia em um cluster.

2. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar** > **Novo Item**. Selecione **Classe** e insira **LocalTest.cs** como o nome da classe. Por fim, clique em **Adicionar**.

3. Abra **LocalTest.cs** e adicione a seguinte instrução **using** na parte superior:
    
    ```csharp
    using Microsoft.SCP;
    ```

4. Use o seguinte como o conteúdo da classe **LocalTest** :
    
    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Tome um momento para ler os comentários do código. Esse código usa **LocalContext** para executar os componentes no ambiente de desenvolvimento, persistindo o fluxo de dados entre componentes para arquivos de texto na unidade local.

1. Abra **Program.cs** e adicione o seguinte ao método **Main**:
    
    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

2. Salve as alterações e use **F5** ou selecione **Depurar** > **Iniciar Depuração** para iniciar o projeto. Uma janela de console deve aparecer e o status do log colocado como progresso dos testes. Quando **Testes concluídos** aparecer, pressione qualquer tecla para fechar a janela.

3. Use o **Windows Explorer** para localizar o diretório que contém o projeto, por exemplo, **C:\Users\<seu_nome_de_usuário>\Documents\Visual Studio 2013\Projects\WordCount\WordCount**. Nesse diretório, abra **Bin** e clique em **Depurar**. Você deve ver os arquivos de texto produzidos após a execução dos testes: sentences.txt, counter.txt e splitter.txt. Abra cada arquivo de texto e inspecione os dados.
   
   > [!NOTE]
   > Os dados da cadeia de caracteres são persistidos como uma matriz de valores decimais nesses arquivos. Por exemplo, \[[97,103,111]] no arquivo **splitter.txt** é a palavra "and".


Embora o teste de um aplicativo de contagem de palavras básico local seja bastante simples, o valor real será obtido quando você tiver uma topologia complexa que se comunica com as fontes de dados externas ou realiza análise de dados complexos. Ao trabalhar nesse projeto, poderá ser necessário definir pontos de interrupção e analisar o código em seus componentes para isolar problemas.

> [!NOTE]
> Defina **Tipo de projeto** novamente como **Biblioteca de Classes** antes de implantar em um Storm no cluster HDInsight.

### <a name="log-information"></a>Registrando informações no log

É possível registrar em log com facilidade informações de seus componentes de topologia usando o `Context.Logger`. Por exemplo, o seguinte criará uma entrada do log de informações:

```csharp
Context.Logger.Info("Component started");
```

As informações registradas em log podem ser exibidas no **Log do Serviço do Hadoop**, que é encontrado no **Gerenciador de Servidores**. Expanda a entrada para o seu Storm no cluster HDInsight e expanda **Log de Serviço do Hadoop**. Por fim, selecione o arquivo de log para exibir.

> [!NOTE]
> Os logs são armazenados na conta de Armazenamento do Azure usada pelo seu cluster. Se essa for uma assinatura diferente daquela em que você está conectada no Visual Studio, você precisará fazer logon na assinatura que contém a conta de armazenamento para exibir essa informação.

### <a name="view-error-information"></a>Exibir informações de erro

Para exibir erros ocorridos em uma topologia em execução, use as etapas a seguir:

1. No **Gerenciador de Servidores**, clique com o botão direito do mouse no Storm do cluster HDInsight e selecione **Exibir topologias Storm**.

2. Para **Spout** e **Bolts**, a coluna **Último Erro** contém informações sobre o último erro ocorrido.

3. Selecione **ID do Spout** ou **ID do Bolt** para o componente com um erro listado. Na página de detalhes exibida, as informações adicionais do erro serão listadas na seção **Erros** na parte inferior da página.

4. Para obter mais informações, selecione uma **Porta** na seção **Executores** da página para ver o log de trabalho do Storm nos últimos minutos.

### <a name="errors-submitting-topologies"></a>Erros durante o envio de topologias

Se você encontrar erros ao enviar uma topologia ao HDInsight, poderá encontrar registros para os componentes do lado do servidor que lidam com o envio de topologia em seu cluster HDInsight. Para recuperar esses logs, use o seguinte comando em uma linha de comando:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Substitua __sshuser__ pela conta de usuário SSH para o cluster. Substitua __clustername__ pelo nome do cluster HDInsight. Se você tiver usado uma senha para a conta SSH, a inserção da senha poderá ser solicitada. O comando baixa o arquivo no diretório no qual o comando é executado.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a desenvolver e a implantar topologias Storm das ferramentas do HDInsight para Visual Studio, saiba como [Processar eventos do Hub de Eventos do Azure com Storm no HDInsight](hdinsight-storm-develop-csharp-event-hub-topology.md).

Para obter um exemplo de uma topologia de C# que divide os dados de fluxo em vários fluxos, consulte [Exemplo de Storm C#](https://github.com/Blackmist/csharp-storm-example).

Para descobrir mais informações sobre como criar topologias de C#, visite [SCP.NET GettingStarted.md](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Para obter outras maneiras de trabalhar com o HDInsight e mais amostras do Storm no HDInsight, consulte o seguinte:

**Microsoft SCP.NET**

* [Guia de programação do SCP](hdinsight-storm-scp-programming-guide.md)

**Apache Storm no HDInsight**

* [Implantar e monitorar topologias com o Apache Storm no HDInsight](hdinsight-storm-deploy-monitor-topology.md)
* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

**Apache Hadoop no HDInsight**

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

**Apache HBase no HDInsight**

* [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-started.md)


