---
title: Processar eventos de Hubs de Eventos com o Storm no HDInsight | Microsoft Docs
description: Saiba como processar dados de Hubs de Eventos com uma topologia Storm C# criada no Visual Studio usando as Ferramentas do HDInsight para Visual Studio.
services: hdinsight,notification hubs
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2016
ms.author: larryfr

---
# Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight
Os Hubs de Eventos do Azure permitem processar grandes quantidades de dados de sites, aplicativos e dispositivos. O spout dos Hubs de Eventos facilita o uso do Apache Storm no HDInsight para analisar esses dados em tempo real. Você pode também gravar dados no Hub de Eventos usando o bolt dos Hubs de Eventos.

Neste tutorial, você aprenderá como usar os modelos do Visual Studio instalados com ferramentas do HDInsight para o Visual Studio criar duas topologias que funcionam com os Hubs de Eventos do Azure.

* **EventHubWriter**: gera dados aleatoriamente e grava nos Hubs de Eventos
* **EventHubReader**: lê dados dos Hubs de Eventos e os armazena no armazenamento de Tabela do Azure

> [!NOTE]
> As etapas neste documento só funcionam com um cluster HDInsight baseado em Windows. Para obter uma versão Java deste projeto que funcionará com um cluster baseado em Windows ou Linux, consulte [Processar eventos dos Hubs de eventos do Azure com Storm no HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).
> 
> 

## Pré-requisitos
* Um [Apache Storm no cluster HDInsight](hdinsight-apache-storm-tutorial-get-started.md)
* Um [Hub de Eventos do Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* O [SDK do .NET do Azure](http://azure.microsoft.com/downloads/)
* As [Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

## Projeto concluído
Você pode baixar uma versão concluída do projeto criado neste tutorial do GitHub: [eventhub-storm-hybrid](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub). No entanto, você ainda precisa fornecer definições de configuração seguindo as etapas neste tutorial.

> [!NOTE]
> Ao usar o projeto concluído, você deverá usar o **Gerenciador de Pacotes NuGet** para restaurar pacotes exigidos pela solução.
> 
> 

## Bolt e spout dos Hubs de Eventos
O bolt e o spout dos Hubs de Eventos são componentes de Java que permitem trabalhar facilmente com o Hub de Eventos do Apache Storm. Embora esses componentes sejam escritos em Java, as Ferramentas do HDInsight para Visual Studio permitem que você crie topologias híbridas que combinem componentes C# e Java.

O bolt e o spout são distribuídos como um único arquivo Java (.jar) denominado **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

### Baixar o arquivo .jar
A versão mais recente do arquivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** foi incluída no projeto [exemplos do Storm, no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples), na pasta **lib**. Para baixar o arquivo, use um dos métodos a seguir.

> [!NOTE]
> O bolt e spout foram enviados para inclusão no projeto Apache Storm. Para obter mais informações, confira [STORM-583: check-in inicial para Hubs de Eventos do storm](https://github.com/apache/storm/pull/336/files) no GitHub.
> 
> 

* **Baixar um arquivo ZIP**: do site [Exemplos do Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples), selecione **Baixar ZIP** no painel à direita para baixar um arquivo .zip que contém o projeto.
  
    ![botão baixar zip](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)
  
    Depois de baixar o arquivo, você poderá extrair o arquivo para o diretório **lib**.
* **Clonar o projeto**: se você tiver o [Git](http://git-scm.com/) instalado, use o comando a seguir para clonar o repositório localmente e, em seguida, localize o arquivo no diretório **lib**.
  
        git clone https://github.com/hdinsight/hdinsight-storm-examples

## Configurar os Hubs de Eventos
Hubs de Eventos é a fonte de dados para este exemplo. Use as seguintes etapas para criar um novo Hub de Eventos.

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), selecione **NOVO** > **SERVIÇOS DE APLICATIVOS** > **BARRAMENTO DE SERVIÇO** > **HUB DE EVENTOS** > **CRIAÇÃO PERSONALIZADA**.
2. Na tela **Adicionar um novo Hub de Eventos**, insira um **Nome do Hub de Eventos**, selecione a **Região** na qual criar o hub e crie um novo namespace ou selecione um existente. Clique na **Seta** para continuar.
   
    ![página 1 do assistente](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)
   
   > [!NOTE]
   > Você deve selecionar o mesmo **Local** como seu Storm no servidor HDInsight para reduzir a latência e os custos.
   > 
   > 
3. Na tela **Configurar o Hub de Eventos**, insira os valores de **Contagem de partições** e **Retenção de Mensagem**. Para este exemplo, use uma contagem de partições de 8 e uma retenção de mensagens de 1. Observe a contagem de partições, pois você precisará desse valor posteriormente.
4. Depois que o hub de eventos tiver sido criado, selecione o namespace, selecione **Hubs de Eventos** e, em seguida, selecione o hub de eventos criado anteriormente.
5. Selecione **Configurar** e crie duas novas políticas de acesso usando as informações a seguir.
   
   | Nome | Permissões |
   | --- | --- |
   | Gravador |Enviar |
   | Leitor |Escutar |
   
    Depois de criar permissões, selecione o ícone **Salvar** na parte inferior da página. Isso cria políticas de acesso compartilhado que serão usadas para enviar (gravador) e escutar (leitor) esse Hub de Eventos.
   
    ![políticas](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)
6. Depois de salvar as políticas, use o **Gerador de chave de acesso compartilhado** na parte inferior da página para recuperar a chave para ambas as políticas **gravador** e **leitor**. Salve-as, pois serão usadas mais tarde.

## Configurar o armazenamento de Tabela
O armazenamento de Tabela será usado para armazenar valores lidos de Hubs de Eventos, pois você pode exibir facilmente o armazenamento de Tabela no Visual Studio por meio do **Gerenciador de Servidores**. Use as seguintes etapas para criar um novo armazenamento de Tabela.

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), selecione **NOVO** > **Serviços de Dados** > **Armazenamento** > **Criação Rápida**.
   
    ![armazenamento de criação rápida](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)
2. Insira um **Nome** para a conta de armazenamento, selecione um **Local** e, em seguida, clique na **marca de seleção** para criar a conta de armazenamento.
   
   > [!NOTE]
   > Você deve selecionar o mesmo **Local** dos seus Hubs de Eventos e Storm no servidor HDInsight para reduzir a latência e os custos.
   > 
   > 
3. Depois que a nova conta de armazenamento for criada, selecione a conta e, em seguida, use o link **Gerenciar Chaves de Acesso** na parte inferior da página para recuperar o **Nome da conta de armazenamento** e a **Tecla de acesso primária**. Salve essas informações porque elas serão usadas posteriormente.
   
    ![teclas de acesso](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)
4. Abra o Visual Studio. No menu **Exibição**, selecione **Gerenciador de Nuvem**. No **Gerenciador de Nuvem**, expanda as **Contas de Armazenamento** e, em seguida, a conta de armazenamento criada anteriormente.
   
    ![Gerenciador de nuvem](./media/hdinsight-storm-develop-csharp-event-hub-topology/createtablestorage.png)
5. Clique com o botão direito do mouse em **Tabelas** para sua conta de armazenamento e, em seguida, selecione **Criar Tabela**. Quando solicitado, insira **eventos** como o nome da sua tabela. Salve o nome, pois você precisará dele em etapas posteriores.

## Criar EventHubWriter
Nesta seção, você criará uma topologia que grava dados nos Hubs de Eventos usando o bolt dos Hubs de Eventos.

1. Se você ainda não instalou a versão mais recente das Ferramentas do HDInsight para Visual Studio, consulte [Introdução ao uso das Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).
2. Abra o Visual Studio, selecione **Arquivo** > **Novo** e **Projeto**.
3. Na caixa de diálogo **Novo Projeto**, expanda **Instalado** > **Modelos** e selecione **HDInsight**. Na lista de modelos, selecione **Aplicativo Storm**. Na parte inferior da caixa de diálogo, digite **EventHubWriter** como o nome do aplicativo.
   
    ![imagem](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)
4. Quando o projeto tiver sido criado, você deverá ter os seguintes arquivos:
   
   * **Program.cs**: define a topologia para seu projeto. Observe que uma topologia padrão que consiste em um spout e um bolt é criada por padrão.
   * **Spout.cs**: um spout de exemplo.
   * **Bolt.cs**: um bolt de exemplo. Isso será excluído porque você usará o bolt dos Hubs de Eventos para gravar no Hub de Eventos

### Configuração
1. No **Gerenciador de Soluções**, clique com botão direito do mouse em **EventHubWriter** e, em seguida, selecione **Propriedades**.
2. Nas propriedades do projeto, selecione **Configurações** e, em seguida, selecione **Este projeto não contém um arquivo de configurações padrão. Clique aqui para criar um.**
3. Insira as configurações a seguir. Use as informações para o Hub de Eventos criado anteriormente na coluna **Valor**.
   
   | Nome | Tipo | Escopo |
   | --- | --- | --- |
   | EventHubPolicyName |string |Aplicativo |
   | EventHubPolicyKey |string |Aplicativo |
   | EventHubNamespace |string |Aplicativo |
   | EventHubName |string |Aplicativo |
   | EventHubPartitionCount |int |Aplicativo |
4. Salve e feche a página **Propriedades**.

### Definir a topologia
1. No **Gerenciador de Soluções**, clique com botão direito do mouse em **Bolt.cs** e selecione **Excluir**. Como você está usando o bolt dos Hubs de Eventos do Java, esse arquivo não será necessário.
2. Abra o arquivo** Program.cs** e adicione o seguinte código imediatamente após a linha `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter" + DateTime.Now.ToString("yyyyMMddHHmmss"));`.
   
        int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
        List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };
   
    A primeira linha lê a contagem de partições de propriedades definidas anteriormente. A segunda linha define um desserializador usado para desserializar dados JSON produzidos pelo spout, em um`java.lang.String` para que os componentes Java possam consumir os dados.
3. Localize o código a seguir:
   
        topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);
   
    Substitua-o pelo seguinte:
   
        topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);
   
    Isso cria um spout e usa a contagem de partições dos Hubs de Eventos como a dica de paralelismo para este componente. Isso deve criar uma instância do spout para cada partição.
   
    Isso também associa o desserializador criado anteriormente ao fluxo de saída deste componente. Isso permite que o componente downstream EventHubSpout consuma os dados produzidos a partir do spout C#.
4. Logo após o código anterior, adicione o seguinte:
   
        JavaComponentConstructor constructor =
            JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.bolt.EventHubBolt. (com.microsoft.eventhubs.bolt.EventHubBoltConfig. " +
            @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
            Properties.Settings.Default.EventHubPolicyName,
            Properties.Settings.Default.EventHubPolicyKey,
            Properties.Settings.Default.EventHubNamespace,
            "servicebus.windows.net", //suffix for servicebus fqdn
            Properties.Settings.Default.EventHubName,
            "true"));
   
    Isso cria um novo construtor para o bolt Java, que é usado em tempo de execução para configurar uma nova instância do bolt. Nesse caso, você está usando o <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> para configurar o spout com as informações de configuração dos Hubs de Eventos adicionadas anteriormente. Mais especificamente, esse código é usado pelo HDInsight em tempo de execução para fazer o seguinte:
   
   * Criar uma nova instância de **com.microsoft.eventhubs.bolt.EventHubBoltConfig** usando as informações dos Hubs de Eventos que você fornecer.
   * Criar uma nova instância de **com.microsoft.eventhubs.bolt.EventHubBolt**, passando na instância **EventHubBoltConfig**.
5. Localize o código a seguir:
   
        topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");
   
    Substitua-o pelo seguinte:
   
        topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");
   
    Isso instrui a topologia a usar o **JavaComponentConstructor** da etapa anterior como o bolt. O componente pode ser chamado nessa topologia pelo nome amigável de "EventHubBolt". A dica de paralelismo é definida como o número de partições para o Hub de Eventos, e ele assina dados produzidos pelo spout ("Spout").

Neste ponto, você concluiu o **Program.cs**. A topologia foi definida, mas agora modifique **Spout.cs** para que ele produza dados em um formato que o bolt dos Hubs de Eventos possa usar.

> [!NOTE]
> Essa topologia terá como padrão a criação de um processo de trabalho, que é suficiente para fins de exemplo. Se estiver adaptando isso para um cluster de produção, você poderá adicionar o seguinte para alterar o número de trabalhos:
> 
> 

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### Modificar o spout
O bolt dos Hubs de Evento espera um valor único de cadeia de caracteres, que ele roteará para o Hub de Eventos. No exemplo a seguir, você modificará o arquivo padrão **Spout.cs** para produzir uma cadeia de caracteres JSON.

1. No **Gerenciador de Soluções**, abra **Spout.cs** e adicione o seguinte na parte superior do arquivo:
   
        using Newtonsoft.Json;
        using Newtonsoft.Json.Linq;
   
    Isso permite que você trabalhe com mais facilidade com dados JSON.
   
   > [!NOTE]
   > O pacote JSON.NET já deve estar instalado, conforme exigido pela estrutura SCP.NET usada para topologias C# Storm.
   > 
   > 
2. Localize o código a seguir:
   
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
   
    Substitua-o pelo seguinte:
   
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());
   
    Isso altera a definição de dados criados pelo spout para usar dados de **cadeia de caracteres** e o **CustomizedInteropJSONSerializer** declarado anteriormente na topologia (em program.cs).
3. Substitua o método **NextTuple** pelo seguinte:
   
        public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }
   
    Isso vai gerar aleatoriamente uma identificação de dispositivo, um valor e então usará Json.NET para emitir um objeto JSON usando esses valores.
4. Salve o arquivo **Spout.cs**.

Neste ponto, você tem uma topologia básica que vai gerar dados aleatórios e armazená-los em Hubs de Eventos usando o bolt dos Hubs de Eventos. Em seguida, você criará o leitor.

## Criar EventHubReader
Nesta seção, você criará uma topologia que lê dados de Hubs de Eventos usando o bolt dos Hubs de Eventos.

1. Abra o Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.
2. Na caixa de diálogo **Novo Projeto**, expanda **Instalado** > **Modelos** e selecione **HDInsight**. Na lista de modelos, selecione **Aplicativo Storm**. Na parte inferior da caixa de diálogo, insira **EventHubReader** como o nome do aplicativo.

### Configuração
1. No **Gerenciador de Soluções**, clique com botão direito do mouse em **EventHubReader** e, em seguida, selecione **Propriedades**.
2. Selecione **Ferramentas**, **Gerenciador de Pacotes NuGet** e, em seguida, **Console do Gerenciador de Pacotes**. Quando o console for exibido, use o seguinte comando para instalar os pacotes de armazenamento do Azure.
   
        NuGet install WindowsAzure.Storage
3. Nas propriedades do projeto, selecione **Configurações** e, em seguida, selecione **Este projeto não contém um arquivo de configurações padrão. Clique aqui para criar um.**
4. Insira as configurações a seguir. Use as informações para o Hub de Eventos e a Conta de armazenamento que você criou anteriormente na coluna **Valor**.
   
   | Nome | Tipo | Escopo |
   | --- | --- | --- |
   | EventHubPolicyName |string |Aplicativo |
   | EventHubPolicyKey |string |Aplicativo |
   | EventHubNamespace |string |Aplicativo |
   | EventHubName |string |Aplicativo |
   | EventHubPartitionCount |int |Aplicativo |
   | StorageConnection |(Cadeia de Conexão) |Aplicativo |
   | TableName |string |Aplicativo |
   
    Para **TableName**, insira o nome da tabela em que você deseja que eventos sejam armazenados.
   
    Para **StorageConnection**, insira um valor `DefaultEndpointsProtocol=https;AccountName=myAccount;AccountKey=myKey;`. Substitua **myAccount** e **myKey** pelo nome da conta de armazenamento e pela chave obtida anteriormente.
   
    Esses valores serão usados pela topologia para se comunicar com Hubs de Eventos e o Armazenamento de Tabela.
5. Salve e feche a página **Propriedades**.

### Definir a topologia
1. No **Gerenciador de Soluções**, clique com botão direito do mouse em **Spout.cs** e selecione **Excluir**. Como você está usando o spout dos Hubs de Eventos do Java, esse arquivo não será necessário.
2. Abra o arquivo **Program.cs** e adicione o seguinte código imediatamente após a linha `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader" + DateTime.Now.ToString("yyyyMMddHHmmss"));`.
   
        int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
        EventHubSpoutConfig ehConfig = new EventHubSpoutConfig(
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount);
   
    A contagem de partições é lida e atribuída a uma variável local. Ela será usada várias vezes.
   
    O `EventHubSpoutConfig` define a configuração do spout do Hub de Eventos. Nesse caso, as informações de configuração dos Hubs de Eventos que você adicionou anteriormente. Nos bastidores, isso usa o spout Java do Hub de Eventos e criará uma nova instância de **com.microsoft.eventhubs.spout.EventHubSpoutConfig** usando as informações dos Hubs de Eventos.
3. Localize o código a seguir:
   
        topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);
   
    Substitua-o pelo seguinte:
   
        topologyBuilder.SetEventHubSpout(
            "EventHubSpout", 
            ehConfig, 
            partitionCount); 
   
    Isso instrui a topologia a criar um novo spout do Hub de Eventos e a usar o `EventHubSpoutConfig` da etapa anterior como configuração. "EventHubSpout" define o nome amigável do spout e `partitionCount` é usado para definir a dica de paralelismo. Nos bastidores, isso cria uma nova instância do componente Java **com.microsoft.eventhubs.spout.EventHubSpout** usando as informações de configuração fornecidas.
4. Adicione o seguinte logo após o código anterior:
   
         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };
   
    Isso cria um serializador personalizado, que será usado para serializar informações geradas pelos componentes Java (como EventHubSpout) em um formato JSON que possa ser usado por componentes C# downstream.
5. Localize o código a seguir:
   
        topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");
   
    Substitua-o pelo seguinte:
   
        topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount,
            true).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");
   
    Este código instrui a topologia a usar um bolt (definido em Bolt.cs). O serializador personalizado definido anteriormente é usado aqui para que este bolt possa consumir dados produzidos pelos componentes Java upstream. Nesse caso, o EventHubSpout.
   
   > [!IMPORTANT]
   > O último parâmetro para SetBolt, (com um valor `true`), habilita a funcionalidade ACK para este bolt. Isso é necessário, já que o componente EventHubSpout espera uma ACK para os dados que ele emite. Se ACKs não forem retornados por componentes downstream, o spout terá a recepção interrompida após o processamento de cerca de 1000 mensagens.
   > 
   > 

Neste ponto, você concluiu **Program.cs**. A topologia foi definida, mas agora você deve criar uma classe auxiliar para gravar dados no armazenamento de Tabela. Você deve modificar **Bolt.cs** para que ele possa entender os dados produzidos pelo spout.

> [!NOTE]
> Essa topologia terá como padrão a criação de um processo de trabalho, que é suficiente para fins de exemplo. Se você estiver adaptando isso para um cluster de produção, deverá adicionar o seguinte para alterar o número de trabalhos:
> 
> 

    StormConfig config = new StormConfig();
    config.setNumWorkers(1);
    topologyBuilder.SetTopologyConfig(config);


### Criar uma classe auxiliar
Ao gravar dados no armazenamento de Tabela, você deve criar uma classe que descreve os dados que serão gravados.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EventHubReader** e escolha **Adicionar** e **Classe**. Nomeie a nova classe como **Device.cs**.
2. Abra **Device.cs** e substitua o código padrão pelo seguinte:
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.Storage.Table;
   
        namespace EventHubReader
        {
            class Device : TableEntity
            {
                public int value { get; set; }
   
                public Device() { }
                public Device(int id)
                {
                    this.PartitionKey = id.ToString();
                    this.RowKey = System.Guid.NewGuid().ToString();
                }
            }
        }
   
    Isso criará entidades no armazenamento de Tabela que consistem em uma chave de partição (que será definida como a identificação do dispositivo para ler do Hub de Eventos), uma chave exclusiva da linha e um valor que é lido do Hub de Eventos. Cada entidade também terá um carimbo de data/hora, que será criado automaticamente quando a entidade for inserida na tabela.

### Modificar o bolt
1. No **Gerenciador de Soluções**, expanda o projeto **EventHubReader** e abra o arquivo **Bolt.cs**. Adicione o seguinte na parte superior do arquivo:
   
        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
   
    Isso nos permitirá trabalhar mais facilmente com dados JSON do bolt e gravar dados no armazenamento de Tabela.
2. Localize a instrução `private int count;` e substitua-a pelo seguinte:
   
        private CloudTable table;
   
    Isso será usado ao conectar-se à tabela.
3. Localize o código a seguir:
   
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
   
    Substitua-o pelo seguinte:
   
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
   
    Isso instrui o bolt que estará recebendo um valor **cadeia de caracteres** em vez de um **int**, e que os dados devem ser desserializados usando o **CustomizedInteropJSONDeserialzer** declarado anteriormente na topologia (no arquivo program.cs).
4. Logo após o código anterior, adicione o seguinte:
   
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();
   
    Isso se conecta à tabela de armazenamento do Azure que você criou anteriormente usando a cadeia de conexão armazenada em `TableName`.
5. Localize o método **Execute** e substitua-o pelo seguinte:
   
        public void Execute(SCPTuple tuple)
        {
            Context.Logger.Info("Processing events");
            string eventValue = (string)tuple.GetValue(0);
            if (eventValue != null)
            {
                JObject eventData = JObject.Parse(eventValue);
   
                Device device = new Device((int)eventData["deviceId"]);
                device.value = (int)eventData["deviceValue"];
   
                TableOperation insertOperation = TableOperation.Insert(device);
   
                table.Execute(insertOperation);
                this.ctx.Ack(tuple);
            }
        }
   
    Isso usa Json.NET para analisar os dados JSON do spout e, em seguida, seleciona os campos **deviceId** e **deviceValue**. Um novo objeto **Dispositivo** é criado, usando a **deviceId** durante a inicialização para definir a chave de partição da tabela. O valor é definido como **deviceValue** e, por fim, a entidade é inserida na tabela.
   
    Depois que a entidade for inserida na tabela, `Ack()` será chamado para a tupla, para informar o spout de que os dados foram processados com êxito.
   
   > [!IMPORTANT]
   > O componente EventHubSpout espera um ACK para cada tupla de componentes downstream, como este bolt. Se ACKS não forem recebidas, o EventHubSpout suporá que o processamento da tupla falhou.
   > 
   > 

Neste ponto, você tem uma topologia completa que lê dados de Hub de Eventos e os armazena no Armazenamento de Tabelas, em uma tabela que você criou anteriormente.

## Implantar as topologias
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EventHubReader** e escolha **Enviar para o Storm no HDInsight**.
   
    ![enviar para o storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)
2. Na caixa de diálogo **Enviar Topologia**, selecione seu **Cluster Storm**. Expanda **Configurações Adicionais**, selecione **Caminhos de Arquivo Java**, selecione **...** e o diretório que contém o arquivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** baixado anteriormente. Por fim, clique em **Enviar**.
   
    ![Imagem da caixa de diálogo de envio](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)
3. Depois que a topologia tiver sido enviada, o **Visualizador de Topologias Storm** será exibido. Selecione a topologia **EventHubReader** do lado esquerdo da caixa de diálogo para exibir estatísticas da topologia. Atualmente, nada deveria estar acontecendo, pois nenhum evento foi gravado no Hub de Eventos ainda.
   
    ![exibição de armazenamento de exemplo](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)
4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EventHubWriter** e escolha **Enviar para o Storm no HDInsight**.
5. Na caixa de diálogo **Enviar Topologia**, selecione seu **Cluster Storm**. Expanda **Configurações Adicionais**, selecione **Caminhos de Arquivo Java**, selecione **...** e o diretório que contém o arquivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** baixado anteriormente. Por fim, clique em **Enviar**.
6. Depois que a topologia tiver sido enviada, atualize a lista de topologia no **Visualizador de Topologias Storm** para verificar se ambas estão em execução no cluster.
7. Quando ambas as topologias estiverem em execução, selecione **Gerenciador de Servidores**, expanda **Azure** > **Armazenamento** e selecione a Conta de Armazenamento criada anteriormente. Na Conta de Armazenamento, expanda **Tabelas**. Por fim, clique duas vezes na tabela **eventos** para abrir a tabela. Você deve ver os dados que foram armazenados na tabela por meio da topologia **EventHubReader**.
   
   * Os eventos são gerados pela topologia **EventHubWriter**, que os grava no Hub de Eventos.
   * Em seguida, o **EventHubReader** lê os eventos de Hubs de Eventos e os armazena no armazenamento de Tabela na tabela **eventos**.

## Interromper as topologias
Para interromper as topologias, selecione cada topologia no **Visualizador de Topologia Storm** e selecione **Eliminar**.

![imagem de eliminação de uma topologia](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

## Excluir o cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Observações
### Ponto de verificação
O EventHubSpout cria periodicamente pontos de verificação para seu estado para o nó Zookeeper, que salva o deslocamento atual para mensagens lidas da fila. Isso permite que o componente comece a receber mensagens no deslocamento salvo nos seguintes cenários:

* A instância de componente falha e é reiniciada.
* Você aumenta ou reduz o cluster ao adicionar ou remover nós.
* A topologia é interrompida e reiniciada **com o mesmo nome**.

Você também pode exportar e importar os pontos de verificação persistentes para WASB (Armazenamento do Azure usado pelo seu cluster HDInsight). Os scripts para fazer isso estão localizados no Storm no cluster HDInsight, em **c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin**.

> [!NOTE]
> O número de versão no caminho pode ser diferente, já que a versão do Storm instalada no cluster pode ser alterada no futuro.
> 
> 

Os scripts que estão nesse diretório são:

* **stormmeta\_import.cmd**: importe todos os metadados do Storm do contêiner de armazenamento de cluster padrão para o Zookeeper.
* **stormmeta\_export.cmd**: exporte todos os metadados do Storm do Zookeeper para o contêiner de armazenamento do cluster padrão.
* **stormmeta\_delete.cmd**: exclua todos os metadados do Storm do Zookeeper.

Exportar uma importação permite que você mantenha os dados de ponto de verificação quando precisar excluir o cluster, mas quiser retomar o processamento do deslocamento atual no hub quando colocar um novo cluster novamente online.

> [!NOTE]
> Como os dados são mantidos para o contêiner de armazenamento padrão, o novo cluster **deverá** usar a mesma conta de armazenamento e o contêiner do cluster anterior.
> 
> 

## Próximas etapas
Neste documento, você aprendeu a usar o Bolt e o Spout dos Hub de Eventos Java de uma topologia C# para trabalhar com dados no Hub de Eventos do Azure. Para saber mais sobre a criação de topologias de C#, consulte o seguinte.

* [Desenvolver topologias C# para o Apache Storm no HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Guia de programação do SCP](hdinsight-storm-scp-programming-guide.md)
* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0914_2016-->