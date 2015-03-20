<properties
   pageTitle="Processar eventos do Hub de Eventos com o Storm no HDInsight | Azure"
   description="Saiba como processar dados do Hub de Eventos com uma topologia C# Storm criada no Visual Studio usando as Ferramentas do HDInsight para Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/03/2015"
   ms.author="larryfr"/>

#Processar eventos do Hub de Eventos do Azure com Storm no HDInsight (C#)

O Hub de Eventos do Azure permite processar grandes quantidades de dados de sites, aplicativos e dispositivos. O Spout Hub de Eventos facilita o uso do Apache Storm no HDInsight para analisar esses dados em tempo real. Você pode também gravar dados no Hub de Eventos usando o Bolt do Hub de Eventos. 

Neste documento, você aprenderá a usar as Ferramentas do HDInsight para Visual Studio, além do Spout e do Bolt do Hub de Eventos para criar duas tolopogias C#/Java híbridas:

* **EventHubWriter** - gera dados aleatoriamente e grava no Hub de Eventos

* **EventHubReader** - lê dados do Hub de Eventos e os armazenam no Armazenamento de tabela do Azure

##Pré-requisitos

* Um <a href="../hdinsight-storm-getting-started/" target="_blank">Apache Storm no cluster HDInsight</a>

* Um <a href="../service-bus-event-hubs-csharp-ephcs-getstarted/" target="_blank">Hub de Eventos do Azure</a>

* O <a href="http://azure.microsoft.com/downloads/" target="_blank">SDK .NET do Azure</a>

* As <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Ferramentas do HDInsight para Visual Studio</a> - a versão 17/02/2015 ou mais recente

##Projeto concluído

Você pode baixar uma versão completa do projeto criado neste artigo em [https://github.com/Blackmist/eventhub-storm-hybrid](https://github.com/Blackmist/eventhub-storm-hybrid); no entanto, você ainda precisa fornecer definições de configuração seguindo as etapas neste documento.

> [AZURE.NOTE] Ao usar o projeto concluído, você deve usar o **Gerenciador de Pacotes NuGet** para restaurar pacotes exigidos pela solução.

##Bolt e spout do Hub de Eventos

O bolt e spout do Hub de Eventos são componentes de Java que permitem trabalhar facilmente com o Hub de Evento do Apache Storm. Embora esses componentes sejam escritos em Java, as Ferramentas do HDInsight para Visual Studio permitem que você crie topologias híbridas que combinem componentes C# e Java.

O bolt e spout são distribuídos como um único arquivo JAR (Java) arquivo denominado **eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

###Download do Jar

A versão mais recente do projeto **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** arquivo está incluído no <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a> na pasta **lib**. Para baixar o arquivo, use um dos seguintes métodos.

> [AZURE.NOTE] O bolt e spout foram enviados para inclusão no projeto Apache Storm. Para obter mais informações, consulte o <a href="https://github.com/apache/storm/pull/336/files">solicitação pull</a>.

* **Baixar um arquivo ZIP** - no site <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">https://github.com/hdinsight/hdinsight-storm-examples</a>, selecione o botão **Baixar ZIP** para baixar um arquivo .zip que contém o projeto.

	![download zip button](./media/hdinsight-storm-develop-csharp-event-hub-topology/download.png)

	Uma vez baixado, extraia o arquivo e ele estará na biblioteca **lib**.

* **Clonar o projeto** - se você tiver <a href="http://git-scm.com/" target="_blank">Git</a> instalado, use o seguinte comando para clonar o repositório localmente, em seguida, localize o arquivo no diretório **lib**.

		git clone https://github.com/hdinsight/hdinsight-storm-examples

##Configurar o Hub de Eventos

O Hub de Eventos é a fonte de dados para este exemplo. Use as seguintes etapas para criar um novo Hub de Eventos.

1. No [Portal do Azure](https://manage.windowsazure.com), selecione **NOVO | Barramento de Serviço | Hub de Eventos | Criação Personalizada**.

2. No diálogo **Adicionar um novo Hub de Eventos**, insira um **Nome do Hub de Eventos**, selecione a **Região** na qual criar o hub e crie um novo namespace ou selecione um existente. Por fim, clique na **Seta**.

	![wizard page 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE] Você deve selecionar o mesmo **local** como seu Storm no servidor HDInsight para reduzir a latência e os custos.

2. No diálogo **Configurar o Hub de Eventos**, insira os valores de **Contagem de partições** e **Retenção de Mensagem**. Para este exemplo, use uma contagem de partições de 10 e uma retenção de mensagens de 1. Observe a contagem de partições, pois você precisará desse valor posteriormente.

	![wizard page 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Depois que o hub de eventos tiver sido criado, selecione o namespace e selecione **Hubs de Eventos**. Por fim, selecione o hub de eventos criado anteriormente.

4. Selecione **Configurar**, e crie duas novas políticas de acesso usando as seguintes informações.

	<table>
	<tr><th>Nome</th><th>Permissões</th></tr>
	<tr><td>gravador</td><td>Enviar</td></tr>
	<tr><td>leitor</td><td>Escutar</td></tr>
	</table>

	Depois de criar permissões, selecione o ícone **Salvar** na parte inferior da página. Isso cria políticas de acesso compartilhado que serão usadas para enviar (gravador) e escutar (leitor) esse Hub de Eventos.

	![policies](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Depois de salvar as políticas, use o **Gerador de chave de acesso compartilhado** na parte inferior da página para recuperar a chave para ambas as políticas **gravador** e **leitor**. Salve-as, pois serão usadas mais tarde.

##Configurar o armazenamento de tabela

O armazenamento de tabela será usado para armazenar valores lidos do Hub de Eventos, pois você pode exibir facilmente o Armazenamento de Tabela no Visual Studio por meio do **Gerenciador de Servidores**. Use as seguintes etapas para criar um novo Armazenamento de tabela.

1. No [Portal do Azure](https://manage.windowsazure.com), selecione **NOVO | Serviços de Dados | Armazenamento | Criação Rápida**.

	![quick create storage](./media/hdinsight-storm-develop-csharp-event-hub-topology/storagecreate.png)

2. Insira um **Nome** para a Conta de armazenamento, selecione um **Local** e, em seguida, selecione a **marca de seleção** para criar a Conta de armazenamento.

	> [AZURE.NOTE] Você deve selecionar o mesmo **local** como seu Hub de Eventos e Storm no servidor HDInsight para reduzir a latência e os custos.

3. Depois que a nova Conta de armazenamento for provisionada, selecione a conta e, em seguida, use o link **Gerenciar Chaves de Acesso** na parte inferior da página para recuperar o **Nome da conta de armazenamento** e a **Chave de acesso primária**. Salve essas informações que serão usadas posteriormente.

	![access keys](./media/hdinsight-storm-develop-csharp-event-hub-topology/managekeys.png)

##Criar o EventHubWriter

Nesta seção, você criará uma topologia que grava dados no Hub de Eventos usando o bolt do Hub de Eventos.

1. Se você ainda não instalou a versão mais recente das Ferramentas do HDInsight para Visual Studio, consulte <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Introdução ao uso das Ferramentas do HDInsight para Visual Studio</a>.

2. Abra o Visual Studio, selecione **Arquivo**, **Novo** e **Projeto**.

3. Na caixa de diálogo **Novo Projeto**, expanda **Instalados**, **Modelos** e selecione **HDInsight**. Na lista de modelos, selecione **Aplicativo Storm**. Na parte inferior da caixa de diálogo, digite **EventHubWriter** como o nome do aplicativo.

	![image](./media/hdinsight-storm-develop-csharp-event-hub-topology/newproject.png)

4. Quando o projeto tiver sido criado, você deve ter os seguintes arquivos:

	* **Program.cs** - define a topologia para seu projeto. Observe que uma topologia padrão consistindo de um spout e um bolt é criada por padrão

	* **Spout.CS** - um spout de exemplo

	* **Bolt.CS** - um bolt de exemplo. Isso será excluído, pois você usará o bolt do Hub de Eventos para gravar no Hub de Eventos

###Configuração

1. No **Gerenciador de Soluções**, clique com botão direito do mouse em **EventHubWriter** e, em seguida, selecione **Propriedades**.

2. Nas propriedades do projeto, selecione **Configurações** e, em seguida, selecione **Este projeto não contém um arquivo de configurações padrão. Clique aqui para criar um.**

3. Insira as seguintes configurações. Use as informações para o Hub de Eventos que você criou anteriormente na coluna **Valor**.

	<table>
	<tr><th style="text-align:left">Nome</th><th style="text-align:left">Tipo</th><th style="text-align:left">Escopo</th></tr>
	<tr><td style="text-align:left">EventHubPolicyName</td><td style="text-align:left">cadeia de caracteres</td><td style="text-align:left">Aplicativo</td></tr>
	<tr><td style="text-align:left">EventHubPolicyKey</td><td style="text-align:left">cadeia de caracteres</td><td style="text-align:left">Aplicativo</td></tr>
	<tr><td style="text-align:left">EventHubNamespace</td><td style="text-align:left">cadeia de caracteres</td><td style="text-align:left">Aplicativo</td></tr>
	<tr><td style="text-align:left">EventHubName</td><td style="text-align:left">cadeia de caracteres</td><td style="text-align:left">Aplicativo</td></tr>
	<tr><td style="text-align:left">EventHubPartitionCount</td><td style="text-align:left">int</td><td style="text-align:left">Aplicativo</td></tr>
	</table>

4. Salve e feche a página de propriedades.

###Definir a topologia

1. No **Gerenciador de Soluções**, clique com botão direito do mouse em **Bolt.cs** e selecione **Excluir**. Como você está usando o bolt do Hub de Eventos do Java, esse arquivo não é necessário.

2. Abra o arquivo **Program.cs** e adicione o seguinte logo após a linha `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubWriter");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		List<string> javaDeserializerInfo =
            new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer", "java.lang.String" };

	A primeira linha lê a contagem de partições de propriedades definidas anteriormente. A segunda linha define um desserializador usado para desserializar dados JSON produzidos pelo spout, em um  `java.lang.String` para que o bolt do Hub de Eventos possa consumir os dados.

4. Localize o código a seguir.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Replace it with the following.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"Event"}}
            },
            partitionCount).
            DeclareCustomizedJavaDeserializer(javaDeserializerInfo);

	Isso cria um spout e usa a contagem de partições do Hub de Eventos como a dica de paralelismo para este componente. Isso deve criar uma instância do spout para cada partição.
	
	Isso também declara que o fluxo de saída deve usar o desserializador criado anteriormente.

5. Logo após o código anterior, adicione o seguinte.

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

	Isso cria um novo construtor para o bolt de Java, que é usado em tempo de execução para configurar uma nova instância do bolt. Nesse caso, você está usando o <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> para configurar o spout com as informações de configuração do Hub de Eventos que você adicionou anteriormente. Mais especificamente, esse código é usado pelo HDInsight em tempo de execução para fazer o seguinte:

	* Criar uma nova instância de **com.microsoft.eventhubs.bolt.EventHubBoltConfig** usando as informações do Hub de Eventos que você fornecer.
	* Criar uma nova instância de **com.microsoft.eventhubs.bolt.EventHubBolt**, passando na instância **EventHubBoltConfig**.

6. Localize o código a seguir.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Replace it with the following.

		topologyBuilder.SetJavaBolt(
            "EventHubBolt",
            constructor,
            partitionCount).
            shuffleGrouping("Spout");

	Isso instrui a topologia a usar o **JavaComponentConstructor** da etapa anterior como o bolt. O componente pode ser chamado nessa topologia pelo nome amigável do "EventHubBolt". A dica de paralelismo é definida como o número de partições para o Hub de Eventos, e ele assina dados produzidos pelo spout ("Spout").

Neste ponto, você concluiu o **Program.cs**. A topologia foi definida, mas agora modifique **Spout.cs** para que ele produza dados em um formato que o bolt do Hub de Eventos possa usar.

###Modificar o spout

O bolt do Hub de Evento espera um valor único de cadeia de caracteres, que ele encaminhará para o Hub de Eventos. No exemplo a seguir, você modificará o arquivo padrão **Spout.cs** para produzir uma cadeia de caracteres JSON.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EventHubWriter** e escolha **Gerenciar Pacotes Nuget**. Pesquise o pacote **Json.Net** e adicione-o à solução. Isso nos permitirá criar facilmente dados JSON que serão enviados ao Hub de Eventos usando o bolt.

1. Abra **Spout.cs** e adicione o seguinte à parte superior do arquivo.

		using Newtonsoft.Json;
		using Newtonsoft.Json.Linq;

	Isso nos permitirá trabalhar mais facilmente com dados JSON.

3. Localize o código a seguir.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));

	Substitua-o pelo código a seguir.

		Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
        this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer());

	Isso altera a definição de dados criados pelo spout para usar dados da **cadeia de caracteres** e um **Serializador JSON personalizado**.

2. Substitua o método **NextTuple** pelo seguinte.

		public void NextTuple(Dictionary<string, Object> parms)
        {
            JObject eventData = new JObject();
            eventData.Add("deviceId", r.Next(10));
            eventData.Add("deviceValue", r.Next());
            ctx.Emit(new Values(eventData.ToString(Formatting.None)));
        }

	Isso vai gerar aleatoriamente uma identificação de dispositivo, um valor e usar Json.net para emitir um objeto JSON usando esses valores.

3. Salve o arquivo **Spout.cs**.

Neste ponto, você tem uma topologia básica que vai gerar dados aleatórios e armazená-los no Hub de Eventos usando o bolt do Hub de Eventos. Em seguida, você criará o leitor.

##Criar o EventHubReader

Nesta seção, você criará uma topologia que lês dados do Hub de Eventos usando o bolt do Hub de Eventos.

2. Abra o Visual Studio, selecione **Arquivo**, **Novo** e **Projeto**.

3. Na caixa de diálogo **Novo Projeto**, expanda **Instalados**, **Modelos** e selecione **HDInsight**. Na lista de modelos, selecione **Aplicativo Storm**. Na parte inferior da caixa de diálogo, digite **EventHubReader** como o nome do aplicativo.

###Configuração

1. No **Gerenciador de Soluções**, clique com botão direito do mouse em **EventHubReader** e, em seguida, selecione **Propriedades**.

2. Nas propriedades do projeto, selecione **Configurações** e, em seguida, selecione **Este projeto não contém um arquivo de configurações padrão. Clique aqui para criar um.**

3. Insira as seguintes configurações. Use as informações para o Hub de Eventos e a Conta de armazenamento que você criou anteriormente na coluna **Valor**.

	<table>
	<tr><th style="text-align:left">Nome</th><th style="text-align:left">Tipo</th><th style="text-align:left">Escopo</th></tr>
	<tr><th style="text-align:left">EventHubPolicyName</th><th style="text-align:left">cadeia de caracteres</th><th style="text-align:left">Aplicativo</th></tr>
	<tr><th style="text-align:left">EventHubPolicyKey</th><th style="text-align:left">cadeia de caracteres</th><th style="text-align:left">Aplicativo</th></tr>
	<tr><th style="text-align:left">EventHubNamespace</th><th style="text-align:left">cadeia de caracteres</th><th style="text-align:left">Aplicativo</th></tr>
	<tr><th style="text-align:left">EventHubName</th><th style="text-align:left">cadeia de caracteres</th><th style="text-align:left">Aplicativo</th></tr>
	<tr><th style="text-align:left">EventHubPartitionCount</th><th style="text-align:left">int</th><th style="text-align:left">Aplicativo</th></tr>
	<tr><th style="text-align:left">StorageConnection</th><th style="text-align:left">(Cadeia de Conexão)</th><th style="text-align:left">Aplicativo</th></tr>
	<tr><th style="text-align:left">TableName</th><th style="text-align:left">cadeia de caracteres</th><th style="text-align:left">Aplicativo</th></tr>
	</table>

	Para **TableName**, digite o nome da tabela em que você deseja que eventos sejam armazenados.

	Esses valores serão usados pela topologia para se comunicar com o Hub de Eventos e o Armazenamento de tabela.

4. Salve e feche a página de propriedades.

###Definir a topologia

1. No **Gerenciador de Soluções**, clique com botão direito do mouse em **Spout.cs** e selecione **Excluir**. Como você está usando o spout do Hub de Eventos do Java, esse arquivo não é necessário.

2. Abra o arquivo **Program.cs** e adicione o seguinte logo após a linha `TopologyBuilder topologyBuilder = new TopologyBuilder("EventHubReader");`.

		int partitionCount = Properties.Settings.Default.EventHubPartitionCount;
		JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
            String.Format(@"(com.microsoft.eventhubs.spout.EventHubSpout. (com.microsoft.eventhubs.spout.EventHubSpoutConfig. " +
                @"""{0}"" ""{1}"" ""{2}"" ""{3}"" {4} ""{5}""))",
                Properties.Settings.Default.EventHubPolicyName,
                Properties.Settings.Default.EventHubPolicyKey,
                Properties.Settings.Default.EventHubNamespace,
                Properties.Settings.Default.EventHubName,
                partitionCount,
                "")); //Last value is the zookeeper connection string - leave empty

	A contagem de partições é lida e atribuída a uma variável local, pois ela será usada várias vezes.

	O `JavaComponentConstructor` define como o spout do Java será construído em tempo de execução. Nesse caso, você está usando o <a href="http://storm.apache.org/documentation/Clojure-DSL.html" target="_blank">Apache Storm Clojure DSL</a> para configurar o spout com as informações de configuração do Hub de Eventos que você adicionou anteriormente. Mais especificamente, esse código é usado pelo HDInsight em tempo de execução para fazer o seguinte:

	* Criar uma nova instância de **com.microsoft.eventhubs.spout.EventHubBoltConfig** usando as informações do Hub de Eventos que você fornecer.
	
	* Criar uma nova instância de **com.microsoft.eventhubs.spout.EventHubBolt**, passando na instância **EventHubBoltConfig**.

5. Localize o código a seguir.

		topologyBuilder.SetSpout(
            "Spout",
            Spout.Get,
            new Dictionary<string, List<string>>()
            {
                {Constants.DEFAULT_STREAM_ID, new List<string>(){"count"}}
            },
            1);

	Replace it with the following.

        topologyBuilder.SetJavaSpout(
            "EventHubSpout",
            constructor,
            partitionCount);

	Isso instrui a topologia a usar o **JavaComponentConstructor** da etapa anterior como o spout e a chamá-lo de "EventHubSpout". Isso também define a dica paralelismo deste componente para o número de partições no Hub de Eventos. 

2. Adicione o seguinte logo após o código anterior.

         List<string> javaSerializerInfo = new List<string>() { "microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer" };

	Isso cria um serializador personalizado, que será usado para serializar informações produzidas pelo spout do Java em formato JSON.

3. Localize o código a seguir.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            1).shuffleGrouping("Spout");

	Substitua-o pelo seguinte.

		topologyBuilder.SetBolt(
            "Bolt",
            Bolt.Get,
            new Dictionary<string, List<string>>(),
            partitionCount).
            DeclareCustomizedJavaSerializer(javaSerializerInfo).
            shuffleGrouping("EventHubSpout");

	Este código instrui a topologia a usar um bolt(definido em Bolt.cs). As adições instruem a topologia a usar o serializador personalizado para os dados consumidos por este componente, que vem do **EventHubSpout** - o spout do Java.

Neste ponto, você concluiu o **Program.cs**. A topologia foi definida, mas agora você deve criar uma classe auxiliar para gravar dados no Armazenamento de tabela. Você deve modificar **Bolt.cs** para que ele possa entender os dados produzidos pelo spout.

###Criar uma classe auxiliar

Ao gravar dados no Armazenamento de tabela, você deve criar uma classe que descreve os dados que serão gravados.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EventHubReader** e escolha **Adicionar**, **Nova Classe**. Nomeie a nova classe como **Devices.cs**.

2. Abra **Devices.cs** e substitua o código padrão pelo seguinte.

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

	Isso vai criar entidades no Armazenamento de tabela que consistem em uma chave de partição (que será definida como a identificação do dispositivo para ler do Hub de Eventos), uma chave exclusiva da linha e um valor que é lido do Hub de Eventos. Cada entidade também terá um carimbo de data/hora, que será criado automaticamente quando a entidade for inserida na tabela.

###Modificar o bolt

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EventHubReader** e escolha **Gerenciar Pacotes Nuget**. Pesquise o pacote **Json.Net** e adicione-o à solução. Isso nos permitirá processar com facilidade os dados JSON recebidos do spout. Adicione também o pacote **Armazenamento do Microsoft Azure**, que nos permitirá gravar para o armazenamento de tabela.

1. Abra **Bolt.cs** e adicione o seguinte à parte superior do arquivo.

		using Newtonsoft.Json.Linq;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Table;

	Isso nos permitirá trabalhar mais facilmente com dados JSON do bolt e gravar dados no Armazenamento de tabela.

2. Localize a instrução `private int count;` e substitua-a pelo seguinte.

        private CloudTable table;

	Isso será usado ao conectar-se à tabela.

4. Localize o código a seguir.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));

	Substitua-o pelo código a seguir.

		Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
        this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());

	Isso instrui o bolt que estará recebendo um valor **cadeia de caracteres** em vez de um **int**, e que os dados devem ser desserializados usando o **CustomizedInteropJSONDeserialzer**.

3. Logo após o código anterior, adicione o seguinte.

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(Properties.Settings.Default.StorageConnection);
        CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
        table = tableClient.GetTableReference(Properties.Settings.Default.TableName);
        table.CreateIfNotExists();

	Isso se conectará à tabela **eventos** usando a cadeia de conexão configurada anteriormente. Se não existir, ela será criada.

2. Localize o método **Execute** e substitua-o pelo seguinte.

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
            }
        }

	Isso usa Json.net para analisar os dados JSON do spout e, em seguida, seleciona os campos **deviceId** e **deviceValue**. Um novo objeto **Device** é criado, usando o **deviceId** durante a inicialização para definir a chave de partição da tabela. O valor é definido como **deviceValue** e, finalmente, a entidade é inserida na tabela.

Neste ponto, você tem uma topologia completa que lê dados de Hub de Eventos e os armazena no Armazenamento de tabela em uma tabela chamada **eventos**.

##Implantar as topologias

1. Abra a solução **EventHubReader**. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EventHubReader** e escolha **Enviar para o Storm no HDInsight**.

	![submit to storm](./media/hdinsight-storm-develop-csharp-event-hub-topology/submittostorm.png)

2. Na caixa de diálogo **Enviar Topologia**, selecione seu **Cluster do Storm**. Expanda **Configurações Adicionais**, selecione **Caminhos de Arquivo Java**, selecione **...** e o diretório que contém o **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** baixado anteriormente. Por fim, selecione **Enviar**.

	![Image of submission dialog](./media/hdinsight-storm-develop-csharp-event-hub-topology/submit.png)

3. Depois que a topologia tiver sido enviada, o **Visualizador de Topologias Storm** será exibido. Selecione a topologia **EventHubReader** do lado esquerdo da caixa de diálogo para exibir estatísticas da topologia. Atualmente, nada deveria estar acontecendo, pois nenhum evento foi gravado no Hub de Eventos ainda.

	![example storage view](./media/hdinsight-storm-develop-csharp-event-hub-topology/topologyviewer.png)

4. Abra a solução **EventHubWriter**. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **EventHubWriter** e escolha **Enviar para o Storm no HDInsight**.

2. Na caixa de diálogo **Enviar Topologia**, selecione seu **Cluster do Storm**. Expanda **Configurações Adicionais**, selecione **Caminhos de Arquivo Java**, selecione **...** e o diretório que contém o **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** baixado anteriormente. Por fim, selecione **Enviar**.

5. Depois que a topologia tiver sido enviada, atualize a lista de topologia no **Visualizador de Topologias Storm** para verificar se ambas estão em execução no cluster.

6. Quando ambas estiverem em execução, selecione **Gerenciador de Servidores**, expanda **Azure**, **Armazenamento** e, por fim, expandida a Conta de armazenamento que você criou anteriormente. Na Conta de armazenamento, expanda **Tabelas**. Por fim, clique duas vezes na tabela **Eventos** para abrir a tabela. Você deve ver os dados que foram armazenados na tabela por meio da topologia **EventHubReader**.

	* Os eventos são gerados pela topologia **EventHubWriter**, que os grava no Hub de Eventos.

	* Em seguida, o **EventHubReader** lê os eventos do Hub de Eventos e os armazena no Armazenamento de Tabela na tabela **eventos**.

##Interromper as topologias

Para interromper as topologias, selecione cada topologia no **Visualizador de Topologia Storm** e selecione **Eliminar**.

![image of killing a topology](./media/hdinsight-storm-develop-csharp-event-hub-topology/killtopology.png)

##Resumo

Neste documento, você aprendeu a usar o bolt e o spout do Java do Hub de Eventos de uma topologia de C# para trabalhar com dados no Hub de Eventos do Azure. Para saber mais sobre a criação de topologias de C#, consulte o seguinte.

* [Desenvolver topologias C# para Apache Storm no HDInsight usando o Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology/)

* [Exemplos do Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples)








<!--HONumber=47-->
