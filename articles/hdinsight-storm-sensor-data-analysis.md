<properties 
	pageTitle="Analisando dados do sensor com o Apache Storm e o HDInsight do Microsoft Azure (Hadoop)" 
	description="Aprenda a usar  o Apache Storm para processar dados de sensor em tempo real com o HDInsight (Hadoop)" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# Analisando dados do sensor com o Storm e o HBase no HDInsight (Hadoop)

Saiba como criar uma solução que use o cluster do Storm no HDInsight para processar dados do sensor do hub de eventos do Azure. Durante o processamento, a topologia do Storm armazenará dados recebidos em um cluster HBase. A topologia também usará o SignalR para fornecer informações quase em tempo real por meio de um painel baseado na web hospedado em sites do Azure.

> [AZURE.NOTE] Uma versão completa desse projeto está disponível em [https://github.com/Blackmist/hdinsight-eventhub-example](https://github.com/Blackmist/hdinsight-eventhub-example).

## Pré-requisitos

* Uma assinatura do Azure

* Visual Studio com o [SDK do Microsoft Azure para .NET](http://azure.microsoft.com/downloads/archive-net-downloads/)

* [Java e JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* [Maven](http://maven.apache.org/what-is-maven.html)

* [Git](http://git-scm.com/)

> [AZURE.NOTE] Java, o JDK, Maven e Git também estão disponíveis por meio do gerenciador de pacotes [Chocolatey NuGet](http://chocolatey.org/).

## Criar o painel

O painel é usado para exibir informações do sensor em tempo quase real. Nesse caso, o painel está em um aplicativo ASP.NET hospedado no site do Azure. O principal objetivo do aplicativo é servir como um hub [SignalR](http://www.asp.net/signalr/overview/getting-started/introduction-to-signalr) que recebe informações da topologia do Storm conforme processa mensagens.

O site também contém um arquivo index.html estático, que também se conecta ao SignalR e usa D3.js para representar em gráfico os dados transmitidos pela topologia do Storm.

> [AZURE.NOTE] Você também pode usar WebSockets brutos em vez do SignalR, mas WebSockets não fornecem um mecanismo interno de escala, se você precisar escalar horizontalmente o site. O SignalR pode ser colocado em escala usando o Barramento de Serviço do Azure ([http://www.asp.net/signalr/overview/performance/scaleout-with-windows-azure-service-bus](http://www.asp.net/signalr/overview/performance/scaleout-with-windows-azure-service-bus)).
>
> Para um exemplo de uso da topologia do Storm para comunicar-se com um site da Web Python usando WebSockets brutos, consulte o projeto [Visualização do Storm Tweet Sentiment D3](https://github.com/P7h/StormTweetsSentimentD3Viz).

1. No Visual Studio, crie um novo aplicativo C# usando o modelo de projeto de **Aplicativo Web ASP.NET**. Atribua ao aplicativo o nome de **Painel**.

2. Na janela **Novo Projeto ASP.NET**, selecione o modelo de aplicativo **Vazio**. Na seção**Windows Azure**, selecione **Host na nuvem** e **Site**. Por fim, clique em **Ok**.

	> [AZURE.NOTE] Se solicitado, entre com a sua assinatura do Azure.

3. Na caixa de diálogo**Configurar o Site do Windows Azure**, insira um **Nome de site** e **Região** para o seu site, então clique em **OK**. Isso criará o site do Azure que hospedará o painel.

4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Adicionar | Classe do Hub SignalR (v2)**. Atribua à classe o nome **DashHub.cs** e adicione-a ao projeto. Isso conterá o hub do SignalR que é usado para comunicar dados entre o HDInsight e a página da Web do painel.

	> [AZURE.NOTE] Se você estiver usando o Visual Studio 2012, o modelo da **Classe de Hub do SignalR (v2)** não estará disponível. É possível adicionar uma **Classe** simples chamada DashHub em vez disso. Você também precisará instalar manualmente o pacote SignalR abrindo **Ferramentas | Gerenciador de Pacote de Biblioteca | Console do Gerenciador de Pacotes** e executando o seguinte comando:
	>
	> `install-package Microsoft.AspNet.SignalR`

5. Substitua o código em **DashHub.cs** pelo seguinte.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Web;
		using Microsoft.AspNet.SignalR;

		namespace dashboard
		{
		    public class DashHub : Hub
		    {
		        public void Send(string message)
		        {
		            // Call the broadcastMessage method to update clients.
		            Clients.All.broadcastMessage(message);
		        }
		    }
		}

6. No **Gerenciador de Soluções**, clique com o botão direito no projeto e selecione **Adicionar | Classe de Inicialização do OWIN**. Atribua à nova classe o nome de **Startup.cs**.

	> [AZURE.NOTE] Se você estiver usando o Visual Studio 2012, o modelo da **Classe de Inicialização OWIN** não estará disponível. É possível criar uma **Classe** chamada Startup, em vez disso.

7. Substitua o conteúdo de **Startup.cs** pelo seguinte.

		using System;
		using System.Threading.Tasks;
		using Microsoft.Owin;
		using Owin;

		[assembly: OwinStartup(typeof(dashboard.Startup))]

		namespace dashboard
		{
		    public class Startup
		    {
		        public void Configuration(IAppBuilder app)
		        {
		            // For more information on how to configure your application, visit http://go.microsoft.com/fwlink/?LinkID=316888
		            app.MapSignalR();
		        }
		    }
		}

8. No **Gerenciador de Soluções**, clique com o botão direito no projeto e depois clique em **Adicionar | Página HTML**. Atribua à nova página o nome de **index.html**. Essa página conterá o painel em tempo real para esse projeto. Ela receberá informações do DashHub e exibirá um gráfico usando D3.js.

9. Em **Gerenciador de Soluções**, clique com o botão direito em **index.html** e selecione **Definir como Página Inicial**.

10. Substitua o código no arquivo **index.html** pelo seguinte.

		<!DOCTYPE html>
		<html xmlns="http://www.w3.org/1999/xhtml">
		<head>
		    <title>Dashboard</title>
		    <style>

		        .x.axis line {
		            shape-rendering: auto;
		        }

		        .line {
		            fill: none;
		            stroke-width: 1.5px;
		        }

		    </style>
		    <!--Script references. -->
		    <!--Reference the jQuery library. -->
		    <script src="Scripts/jquery-1.10.2.min.js"></script>
		    <!--Reference the SignalR library. -->
		    <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
		    <!--Reference the autogenerated SignalR hub script. -->
		    <script src="signalr/hubs"></script>
		    <!--Reference d3.js.-->
		    <script src="http://d3js.org/d3.v3.min.js"></script>
		</head>
		<body>
		    <script>
		        $(function () {
		            //Huge thanks to Mike Bostok for his Path Transitions article - http://bost.ocks.org/mike/path/
		            var n = 243,                                 //number of x coordinates in the graph
		            duration = 750,                          //duration for transitions
		            deviceValue=[0,0,0,0,0,0,0,0,0,0],       //temp holding for each device value
		            now = new Date(Date.now() - duration),   //Now
		            //fill an array of arrays with dummy data to start the chart
		            //each item in the top-level array is a line
		            //each item in the line arrays represents the X coordinate across a graph
		            //The 'value' within each line array represents the Y coordinate for that point
		            data = [
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; }),
		                d3.range(n).map(function () { return { value: 0 }; })
		            ];

		            //Color scale for 10 items
		            var color = d3.scale.category10();
		            //The domain for color (the device IDs)
		            var devices = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9"];
		            //This will auto-generate colors for this range of IDs
		            color.domain(devices);

		            //set margins and figure out width/height
		            var margin = {top: 6, right: 0, bottom: 20, left: 40},
		                width = 960 - margin.right,
		                height = 240 - margin.top - margin.bottom;

		            //the time scale for the X axis
		            var x = d3.time.scale()
		                .domain([now - (n - 2) * duration, now - duration])
		                .range([0, width]);

		            //the numerical scale for the Y axis
		            var y = d3.scale.linear()
		                .domain([100, 0])
		                .range([0, height]);

		            //The line, which is really just a
		            //couple functions that we can pass data to
		            //in order to get back x/y coords.
		            var line = d3.svg.line()
		                .interpolate("basis")
		                .x(function (d, i) { return x(now - (n - 1 - i) * duration); })
		                .y(function (d, i) { return y(d.value); });

		            //Find the HTML body element and add a child SVG element
		            var svg = d3.select("body").append("svg")
		                .attr("width", width + margin.left + margin.right)
		                .attr("height", height + margin.top + margin.bottom)
		              .append("g")
		                .attr("transform", "translate(" + margin.left + "," + margin.top + ")");

		            //Define a clipping path, because we need to clip
		            //the graph to render only the bits we want to see
		            //as it moves
		            svg.append("defs").append("clipPath")
		                .attr("id", "clip")
		              .append("rect")
		                .attr("width", width)
		                .attr("height", height);

		            //Append the x axis
		            var axis = svg.append("g")
		                .attr("class", "x axis")
		                .attr("transform", "translate(0," + height + ")")
		                .call(x.axis = d3.svg.axis().scale(x).orient("bottom"));

		            //append the y axis
		            var yaxis = svg.append("g")
		                .attr("class", "y axis")
		                .call(y.axis = d3.svg.axis().scale(y).orient("left").ticks(5));

		            //append the clipping path
		            var linegroup = svg.append("g")
		              .attr("clip-path", "url(#clip)");

		            //magic. Select all paths with a class of .line
		            //if they don't exist, make them.
		            //use the points in the line object to define
		            //the paths
		            //set the color to the cooresponding auto-generated coclor
		            var path = linegroup.selectAll(".line")
		              .data(data)
		              .enter().append("path")
		              .attr("class", "line")
		              .attr("d", line)
		              .style("stroke", function (d, i) { return color(i); });

		            //We need to transition the graph after all
		            //lines have been updated. There's no
		            //built-in for this, so this function
		            //does reference counting on end events
		            //for each line, then applies whatever
		            //callback when all are finished.
		            function endall(transition, callback) {
		                var n = 0;
		                transition
		                    .each(function () { ++n; })
		                    .each("end", function () { if (!--n) callback.apply(this, arguments); });
		            }

		            //wire up the SignalR client and listen for messages
		            var chat = $.connection.dashHub;
		            chat.client.broadcastMessage = function (message) {
		                //parse the JSON data
		                var incomingData = JSON.parse(message);
		                //stuff it in the global array slot for the device ID
		                deviceValue[incomingData.device] = incomingData.temperature;

		            };
		            //start listening
		            $.connection.hub.start();
		            //tick for D3 graphics
		            tick();

		            function tick() {
		                // update the domains
		                now = new Date();
		                x.domain([now - (n - 2) * duration, now - duration]);

		                //push the (presumably) fresh data deviceValue array onto
		                //the arrays that define the lines.
		                for (i = 0; i < 10; i++) {
		                    data[i].push({ value: deviceValue[i] });
		                    //data[1].push({ value: maxValue });
		                }
		                //slide the x-axis left
		                axis.transition()
		                    .duration(duration)
		                    .ease("linear")
		                    .call(x.axis);

		                //Update the paths based on the updated line data
		                //and slide left
		                path
		                    .attr("d", line)
		                    .attr("transform", null)
		                .transition()
		                    .duration(duration)
		                    .ease("linear")
		                    .attr("transform", "translate(" + x(now - (n - 1) * duration) + ",0)")
		                    .call(endall, tick);

		                // pop the old data point off the front
		                // of the arrays
		                for (var i = 0; i < data.length; i++) {
		                    data[i].shift();
		                };
		            };
		         })()
		        </script>
		    </body>
		</html>

	> [AZURE.NOTE] Uma versão posterior dos scripts SignalR pode ser instalada pelo gerenciador de pacote. Verifique se as referências de script abaixo correspondem às versões dos arquivos de script no projeto (elas serão diferentes se você tiver adicionado SignalR usando NuGet, em vez de adicionar um hub.)

11. No **Gerenciador de Soluções**, clique com o botão direito no projeto e depois clique em **Adicionar | Página HTML**. Atribua à nova página o nome de **test.html**. Essa página pode ser usada para testar o DashHub e o painel, enviando e recebendo mensagens.

12. Substitua o código no arquivo **test.html** pelo seguinte.

		<!DOCTYPE html>
		<html>
		<head>
		    <title>Test</title>
		    <style type="text/css">
		        .container {
		            background-color: #99CCFF;
		            border: thick solid #808080;
		            padding: 20px;
		            margin: 20px;
		        }
		    </style>
		</head>
		<body>
		    <div class="container">
		        <input type="text" id="message" />
		        <input type="button" id="sendmessage" value="Send" />
		        <input type="hidden" id="displayname" />
		        <ul id="discussion"></ul>
		    </div>
		    <!--Script references. -->
		    <!--Reference the jQuery library. -->
		    <script src="Scripts/jquery-1.10.2.min.js"></script>
		    <!--Reference the SignalR library. -->
		    <script src="Scripts/jquery.signalR-2.0.2.min.js"></script>
		    <!--Reference the autogenerated SignalR hub script. -->
		    <script src="signalr/hubs"></script>
		    <!--Add script to update the page and send messages.-->
		    <script type="text/javascript">
		        $(function () {
		            // Declare a proxy to reference the hub.
		            var chat = $.connection.dashHub;
		            // Create a function that the hub can call to broadcast messages.
		            chat.client.broadcastMessage = function (message) {
		                // Html encode display the message.
		                var encodedMsg = $('<div />').text(message).html();
		                // Add the message to the page.
		                $('#discussion').append('<li>' + encodedMsg + '</li>');
		            };
		            // Set initial focus to message input box.
		            $('#message').focus();
		            // Start the connection.
		            $.connection.hub.start().done(function () {
		                $('#sendmessage').click(function () {
		                    // Call the Send method on the hub.
		                    chat.server.send($('#message').val());
		                    // Clear text box and reset focus for next comment.
		                    $('#message').val('').focus();
		                });
		            });
		        });
		    </script>
		</body>
		</html>

13. **Salvar Tudo** para o projeto.

14. No **Gerenciador de Soluções**, clique com o botão direito no projeto **Painel** e selecione **Publicar**. Selecione o site da Web criado para esse projeto e clique em **Publicar**.

15. Quando o site tiver sido publicado, uma página da Web se abrirá exibindo uma linha do tempo móvel.

### Testar o painel

1. Para verificar se o SignalR está funcionando e se o painel exibirá linhas de gráfico para dados enviados ao SignalR, abra uma nova janela do navegador para a página **test.html** nesse site da Web. Por exemplo, **http://mydashboard.azurewebsites.net/test.html**.

2. O painel espera dados formatados JSON, com uma **ID de dispositivo** e um valor de **temperatura**. Por exemplo **{"device":0, "temperature":80}**. Insira alguns valores de teste na página **test.html** usando os IDs de dispositivo 0 a 9, enquanto o painel está aberto em outra página. Observe que as linhas para cada ID de dispositivo são traçadas usando uma cor diferente.

## Configurar o Hub de Eventos

O Hub de Eventos é usado para receber mensagens (eventos) dos sensores. Use as seguintes etapas para criar um novo Hub de Eventos.

1. No [Portal do Azure](https://manage.windowsazure.com), selecione **NOVO | Barramento de Serviço | Hub de Eventos | Criação Personalizada**.

2. No diálogo **Adicionar um novo Hub de Eventos**, insira um **Nome do Hub de Eventos**, selecione a **Região** na qual criar o hub e crie um novo namespace ou selecione um existente. Por fim, clique na **Seta**.

3. No diálogo **Configurar o Hub de Eventos**, insira os valores de **Contagem de partições** e **Retenção de Mensagem**. Para este exemplo, use uma contagem de partições de 10 e uma retenção de mensagens de 1.

4. Depois que o hub de eventos tiver sido criado, selecione o namespace e selecione **Hubs de Eventos**. Por fim, selecione o hub de eventos criado anteriormente.

5. Selecione **Configurar**, e crie duas novas políticas de acesso usando as seguintes informações.

	<table>
	<tr><th>Nome</th><th>Permissões</th></tr>
	<tr><td>dispositivos</td><td>Enviar</td></tr>
	<tr><td>storm</td><td>Escutar</td></tr>
	</table>

	Depois de criar permissões, selecione o ícone **Salvar** na parte inferior da página. Isso cria as políticas de acesso compartilhado que serão usadas para enviar mensagens ao hub e ler mensagens dele.

6. Depois de salvar as políticas, use o **Gerador de chave de acesso compartilhado** na parte inferior da página para recuperar a chave para as políticas de **dispositivos** e **storm**. Salve-as, pois serão usadas mais tarde.

### Enviar mensagens ao Hub de Eventos

Uma vez que não há um conjunto padrão e fácil de sensores disponível a todos, um aplicativo .NET é usado para gerar números aleatórios. O aplicativo .NET criado usando as etapas abaixo gerará eventos para 10 dispositivos a cada segundo, até que você interrompa o aplicativo pressionando uma tecla.

1. No Visual Studio, crie um novo projeto do **Windows Desktop** e selecione o modelo de projeto **Aplicativo de Console**. Atribua ao projeto o nome **SendEvents** e clique em **OK**.

2. No **Gerenciador de Soluções**, clique com o botão direito em **SendEvents** e depois selecione **Gerenciar Pacotes NuGet**.

3. Em **Gerenciar Pacotes NuGet**, pesquise e instale os seguintes pacotes.

	* **Barramento de Serviço do Microsoft Azure**
	* **JSON.Net**

	Depois da instalação dos pacotes, você deve **Fechar** o gerenciador de pacotes.

4. Substitua os conteúdos de **Program.cs** pelo seguinte.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using Microsoft.ServiceBus.Messaging;
		using Newtonsoft.Json;
		using Microsoft.ServiceBus;
		using System.Threading;

		namespace SendEvents
		{
		    class Program
		    {

		        static int numberOfDevices = 10;
		        static string eventHubName = "temperature";
		        static string eventHubNamespace = "namespace";
		        static string sharedAccessPolicyName = "devices";
		        static string sharedAccessPolicyKey = "key for devices policy";

		        static void Main(string[] args)
		        {
		            var settings = new MessagingFactorySettings()
		            {
		                TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(sharedAccessPolicyName, sharedAccessPolicyKey),
		                TransportType = TransportType.Amqp
		            };
		            var factory = MessagingFactory.Create(
		                 ServiceBusEnvironment.CreateServiceUri("sb", eventHubNamespace, ""), settings);

		            EventHubClient client = factory.CreateEventHubClient(eventHubName);

		            try
		            {

		                List<Task> tasks = new List<Task>();
		                // Send messages to Event Hub
		                Console.WriteLine("Sending messages to Event Hub {0}", client.Path);
		                Random random = new Random();
		                //for (int i = 0; i < numberOfMessages; ++i)
		                while(!Console.KeyAvailable)
		                {
		                    // One event per device
		                    for(int devices = 0; devices < numberOfDevices; devices++)
		                    {
		                        // Create the device/temperature metric
		                        Event info = new Event() {
		                            TimeStamp = DateTime.UtcNow,
		                            DeviceId = random.Next(numberOfDevices),
		                            Temperature = random.Next(100)
		                        };
		                        // Serialize to JSON
		                        var serializedString = JsonConvert.SerializeObject(info);
		                        Console.WriteLine(serializedString);
		                        EventData data = new EventData(Encoding.UTF8.GetBytes(serializedString))
		                        {
		                            PartitionKey = info.DeviceId.ToString()
		                        };

		                        // Send the metric to Event Hub
		                        tasks.Add(client.SendAsync(data));
		                    }
		                    // Sleep a second
		                    Thread.Sleep(1000);
		                };

		                Task.WaitAll(tasks.ToArray());
		            }
		            catch (Exception exp)
		            {
		                Console.WriteLine("Error on send: " + exp.Message);
		            }

		        }
		    }
		}

	Por enquanto, você receberá um aviso sobre linhas que fazem referência à classe do Evento. Ignore-o por enquanto.

5. No arquivo **Program.cs**, defina o valor das seguintes variáveis no início do arquivo para os valores correspondentes recuperados do seu Hub de Eventos no Portal de Gerenciamento do Azure.

	<table>
	<tr><th>Defina isto...</th><th>Para fazer isto...</th></tr>
	<tr><td>eventHubName</td><td>Nome do seu hub de eventos. Por exemplo, <strong>temperatura</strong>.</td></tr>
	<tr><td>eventHubNamespace</td><td>O namespace do seu hub de eventos. Por exemplo, <strong>sensors-ns</strong>.</td></tr>
	<tr><td>sharedAccessPolicyName</td><td>A política criada com acesso de envio. Por exemplo, <strong>dispositivos</strong>.</td></tr>
	<tr><td>sharedAccessPolicyKey</td><td>A chave para a política com acesso de envio.</td></tr>
	</table>

6. No **Gerenciador de Soluções**, clique com o botão direito em **SendEvents** e**Adicionar | Classe**. Atribua à nova classe o nome **Event.cs**. Isso descreverá a mensagem enviada ao Hub de Eventos.

7. Substitua o conteúdos de **Event.cs** pelo seguinte.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Runtime.Serialization;
		using System.Text;
		using System.Threading.Tasks;

		namespace SendEvents
		{
		    [DataContract]
		    public class Event
		    {
		    	[DataMember]
		    	public DateTime TimeStamp { get; set; }
		        [DataMember]
		        public DateTime TimeStamp { get; set; }
		        [DataMember]
		        public int DeviceId { get; set; }
		        [DataMember]
		        public int Temperature { get; set; }
		    }
		}

	Essa classe descreve os dados que estamos enviando - o TimeStamp, um DeviceID e um valor de Temperatura.

8. Selecione **Salvar Tudo** e execute o aplicativo para preencher o Hub de Eventos com mensagens.

## Criar uma rede virtual do Azure

Para que a topologia em execução no Storm se comunique diretamente com o HBase, você deve provisionar ambos os servidores na Rede Virtual do Azure.

1. Entre no [Portal de Gerenciamento do Azure][azure-portal].

2. No final da página, clique em **+NOVO**, clique em **Serviços de Rede**, clique em **Rede Virtual** e, por fim, clique em **Criação Rápida**.

3. Digite ou selecione os valores a seguir:

	- **Nome**: O nome da sua rede virtual.
	- **Espaço de endereço**: Escolha um nome de endereço para a rede virtual que seja grande o suficiente para fornecer endereços para todos os nós do cluster. Caso contrário, a provisão falhará.
	- **Contagem máxima de VMs**: Escolha uma das contagens máximas de VMs.
	- **Local**: O local deve ser o mesmo que o do cluster do HBase que você criará.
	- **Servidor DNS**: Este artigo usa o servidor DNS interno fornecido pelo Azure, então, você pode selecionar **Nenhum**. Configurações de rede mais avançadas com servidores DNS personalizados também têm suporte. Para obter orientações detalhadas, consulte [http://msdn.microsoft.com/library/azure/jj156088.aspx](http://msdn.microsoft.com/library/azure/jj156088.aspx).

4. Clique em **Criar uma Rede Virtual**. O nome da nova rede virtual aparecerá na lista. Aguarde até que a coluna de Status exiba **Criado**.

5. No painel principal, clique na rede virtual que você acabou de criar.

6. No topo da página, clique em **PAINEL**.

7. Em **visão rápida**, anote a **ID DA REDE VIRTUAL**. Ele será necessário ao provisionar os clusters Storm e HBase.

8. No topo da página, clique em **CONFIGURAR**.

9. No final da página, o nome da sub-rede padrão é **Subnet-1**. Use o botão **adicionar sub-rede** para adicionar a **Sub-rede-2**. Essas sub-redes alojarão os clusters do Storm e do HBase.

	> [AZURE.NOTE] Neste artigo, usaremos clusters com apenas um nó. Se estiver criando clusters de vários nós, verifique o **CIDR (CONTAGEM DE ENDEREÇOS)** da sub-rede que será usada para o cluster. A contagem de endereços deve ser maior que o número de nós de trabalho mais sete (Gateway: 2, Headnode: 2, Zookeeper: 3). Por exemplo, se você precisa de um cluster do HBase de 10 nós, a contagem de endereços da sub-rede deve ser maior que 17 (10+7). Caso contrário, a implantação falhará.
	>
	> É altamente recomendado designar uma única sub-rede para um cluster.

11. Clique em **Salvar** na parte inferior da página.

## Criar o cluster Storm no HDInsight

1. Entre no [Portal de Gerenciamento do Azure][azureportal]

2. Clique em **HDInsight** à esquerda e depois em **+NOVO** no canto inferior esquerdo da página.

3. Clique no ícone HDInsight na segunda coluna e selecione **Personalizar**.

4. Na página **Detalhes do Cluster**, insira o nome do novo cluster e selecione **Storm** como o **Tipo de Cluster**. Clique na seta para continuar.

5. Insira 1 para o número de **Nós de Dados** a usar para esse cluster. Em **Região/Rede Virtual**, selecione a Rede Virtual do Azure criada anteriormente. Em **Sub-redes da Rede Virtual**, selecione **Sub-rede-2**.

	> [AZURE.NOTE] Para minimizar o custo do cluster usado neste artigo, reduza o **Tamanho do Cluster** para 1 e exclua o cluster após ter terminado de usá-lo.

6. Insira o **Nome de Usuário** e a **Senha** do administrador e selecione a seta para continuar.

7. Em **Conta de Armazenamento**, selecione **Criar Novo Armazenamento** ou selecione uma conta de armazenamento existente. Selecione ou insira o **Nome da Conta** e o **Contêiner padrão** a utilizar. Clique no ícone de seleção na parte inferior esquerda para criar o cluster do Storm.

## Criar o cluster do HDInsight HBase

1. Entre no [Portal de Gerenciamento do Azure][azureportal]

2. Clique em **HDInsight** à esquerda e depois em **+NOVO** no canto inferior esquerdo da página.

3. Clique no ícone HDInsight na segunda coluna e selecione **Personalizar**.

4. Na página **Detalhes do Cluster**, insira o nome do novo cluster e selecione **HBase** como o **Tipo de Cluster**. Clique na seta para continuar.

5. Insira 1 para o número de **Nós de Dados** a usar para esse cluster. Em **Região/Rede Virtual**, selecione a Rede Virtual do Azure criada anteriormente. Em **Sub-redes da Rede Virtual**, selecione **Sub-rede-1**.

	> [AZURE.NOTE] Para minimizar o custo do cluster usado neste artigo, reduza o **Tamanho do Cluster** para 1 e exclua o cluster após ter terminado de usá-lo.

6. Insira o **Nome de Usuário** e a **Senha** do administrador e selecione a seta para continuar.

7. Em **Conta de Armazenamento**, selecione **Criar Novo Armazenamento** ou selecione uma conta de armazenamento existente. Selecione ou insira o **Nome da Conta** e o **Contêiner padrão** a utilizar. Clique no ícone de seleção na parte inferior esquerda para criar o cluster do Storm.

	> [AZURE.NOTE] Você deve usar um contêiner diferente do usado para o cluster do Storm.

### Habilitar área de trabalho remota

Para este tutorial, devemos usar a área de trabalho remota para acessar os clusters Storm e HBase. Use essas etapas para habilitar a área de trabalho remota em ambos.

1. Entre no [Portal de Gerenciamento do Azure][azureportal].

2. À esquerda, selecione **HDInsight** e depois selecione seu cluster Storm na lista. Por fim, selecione **Configurar** na parte superior da página.

3. Na parte inferior da página, selecione **Habilitar Remoto**. Quando solicitado, informe um nome de usuário, senha e data em que o acesso à área de trabalho remota expirará. Clique na marca de seleção para habilitar a área de trabalho remota.

Depois de habilitar a área de trabalho remota, selecione **Conectar** na parte inferior da página. Siga os avisos pra conectar-se ao cluster.

### Descobrir o sufixo DNS do HBase

Para gravar no HBase usando o cluster Storm, é preciso usar o nome de domínio totalmente qualificado (FQDN) para o cluster HBase. Use as seguintes etapas para descobrir essas informações.

1. Conecte-se ao cluster HBase usando a área de trabalho remota.

2. Depois de se conectar ao cluster, abra a linha de comando do Hadoop e execute o comando **ipconfig** para obter o sufixo DNS. O **Sufixo DNS específico da conexão** conterá o valor do sufixo. Por exemplo, **mycluster.b4.internal.cloudapp.net**. Salve essas informações.

## Desenvolver a topologia do Storm

> [AZURE.NOTE] As etapas nesta seção devem ser realizadas no ambiente de desenvolvimento local.

### Baixar e criar dependências externas

Várias dependências usadas neste projeto devem ser baixadas e criadas individualmente, então instaladas no repositório Maven local no seu ambiente de desenvolvimento. Nesta seção, você vai baixar e instalar.

* O spout do Hub de Eventos que lê mensagens do Hub de Eventos.

* O SDK do cliente Java do SignalR

#### Baixar e criar o spout do Hub de Eventos

Para receber dados do Hub de Eventos, usaremos o **eventhubs-storm-spout**.

1. Use a Área de Trabalho Remota para se conectar ao cluster do Storm e copie o arquivo **%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar** para seu ambiente de desenvolvimento local. Ele contém o **events-storm-spout**.

2. Use o seguinte comando para instalar o pacote no armazenamento Maven local. Isso nos permitirá adicioná-lo facilmente como referência no projeto Storm em uma etapa posterior.

		mvn install:install-file -Dfile=target/eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

#### Baixar e instalar o cliente SignalR

Para enviar mensagens ao painel ASP.NET, use o [SDK do cliente SignalR para Java](https://github.com/SignalR/java-client).

1. Abra um prompt de comando.

2. Altere os diretórios para onde deseja baixar e armazenar o projeto do SDK do cliente SignalR.

3. Use o seguinte comando para efetuar o download do projeto do GitHub.

	git clone https://github.com/SignalR/java-client

4. Altere os diretórios para o diretório **java-client\signalr-client-sdk** e compile o projeto para um arquivo JAR usando os seguintes comandos.

		cd java-client\signalr-client-sdk
		mvn package

	> [AZURE.NOTE] Se você receber um erro dizendo que a dependência **gson** não pode ser baixada, remova as seguintes linhas do arquivo **java-client\signalr-client-sdk\pom.xml**.
	> 
<repository>
<id>central</id>
<name>Central</name>
<url>http://maven.eclipse.org/build</url>
</repository>
</repositories>

	> Remover essas linhas fará o Maven obter o arquivo do repositório central (o comportamento padrão). Para forçar o Maven a tentar acessar o repositório novamente, use o comando `-U`. Por exemplo, `mvn package -U`

5. Use o seguinte comando para instalar o pacote no armazenamento Maven local. Isso nos permitirá adicioná-lo facilmente como referência no projeto Storm em uma etapa posterior.

		mvn install:install-file -Dfile=target/signalr-client-sdk-1.0.jar -DgroupId=microsoft.aspnet.signalr -DartifactId=signalr-client-sdk -Dversion=1.0 -Dpackaging=jar

### Fazer scaffold do projeto de topologia do Storm

Agora que instalamos o spout do Hub de Eventos e o cliente SignalR no repositório local, use o Maven para criar o scaffolding para o projeto de topologia do Storm.

1. Abra um prompt de comando, sessão Bash, sessão Terminal ou o que você usar para digitar comandos no seu sistema.

2. Altere os diretórios para o local em que deseja criar este projeto. Por exemplo, se tiver um diretório em que armazene todos os seus projetos de código.

3. Use o seguinte comando Maven para criar o scaffolding básico para seu aplicativo.

		mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.examples -DartifactId=TemperatureMonitor -DinteractiveMode=false

	Esse comando vai...

	* Crie um novo diretório usando o *artifactId* especificado. Neste caso, é **Temperatura**.
	* Crie um arquivo **pom.xml**, que contém informações do Maven para esse projeto.
	* Crie uma estrutura de diretório **src**, que contém alguns códigos e testes básicos.

### Adicionar dependências e plug-ins

A seguir, modifique o **pom.xml** para fazer referência às dependências desse projeto, bem como os plug-ins do Maven a usar ao criar e empacotar.

1. Usando um editor de texto, abra o arquivo **pom.xml** e adicione o seguinte à seção **&lt;dependências>**. É possível adicioná-los ao fim da seção, depois da dependência para junit.

		<dependency>
	      <groupId>org.apache.storm</groupId>
	      <artifactId>storm-core</artifactId>
	      <version>0.9.2-incubating</version>
	      <!-- keep storm out of the jar-with-dependencies -->
	      <scope>provided</scope>
	    </dependency>
	    <dependency>
	      <groupId>microsoft.aspnet.signalr</groupId>
	      <artifactId>signalr-client-sdk</artifactId>
	      <version>1.0</version>
	    </dependency>
	    <dependency>
	      <groupId>com.microsoft.eventhubs</groupId>
	      <artifactId>eventhubs-storm-spout</artifactId>
	      <version>0.9</version>
	    </dependency>
	    <dependency>
	      <groupId>com.google.code.gson</groupId>
	      <artifactId>gson</artifactId>
	      <version>2.2.2</version>
	    </dependency>
		<dependency>
      	  <groupId>com.github.ptgoetz</groupId>
      	  <artifactId>storm-hbase</artifactId>
      	  <version>0.1.2</version>
    	</dependency>
	    <dependency>
	      <groupId>com.netflix.curator</groupId>
	      <artifactId>curator-framework</artifactId>
	      <version>1.3.3</version>
	      <exclusions>
	        <exclusion>
	          <groupId>log4j</groupId>
	            <artifactId>log4j</artifactId>
	          </exclusion>
	        <exclusion>
	          <groupId>org.slf4j</groupId>
	            <artifactId>slf4j-log4j12</artifactId>
	        </exclusion>
	      </exclusions>
	      <scope>provided</scope>
	    </dependency>

	Isso adiciona dependências para...

	* eventhubs-storm-spout - o spout do Hub de Eventos
	* signalr-client-sdk - o cliente SignalR
	* gson - é uma dependência do cliente SignalR e também será usado para criar JSON ao gravar no SignalR
	* storm-core - fornece funcionalidade central para aplicativos Storm
	* slf4j - fornece recursos de registro em log e é usado por eventhubs-storm-spout
	* curator-framework - usado pelo eventhubs-storm-spout
	* storm-core - classes centrais para o Storm
	* storm-hbase - classes que permitem gravar no HBase

	> [AZURE.NOTE] Observe que algumas dependências estão marcadas com um escopo de **fornecido** para indicar que devem ser baixadas do repositório Maven e usadas para criar e testar o aplicativo localmente, mas também estão disponíveis no ambiente de tempo de execução e não precisam ser compiladas e incluídas no JAR criado por esse projeto.

2. Ao fim do arquivo **pom.xml**, logo antes da entrada **&lt;/project>**, adicione o seguinte.

		  <build>
		    <plugins>
		      <plugin>
		        <groupId>org.apache.maven.plugins</groupId>
		        <artifactId>maven-compiler-plugin</artifactId>
		        <version>2.3.2</version>
		        <configuration>
		          <source>1.7</source>
		          <target>1.7</target>
		        </configuration>
		      </plugin>
		      <plugin>
		        <groupId>org.apache.maven.plugins</groupId>
		        <artifactId>maven-shade-plugin</artifactId>
		        <version>2.3</version>
		        <configuration>
		          <transformers>
		            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		            </transformer>
		          </transformers>
		        </configuration>
		        <executions>
		          <execution>
		            <phase>package</phase>
		            <goals>
		              <goal>shade</goal>
		            </goals>
		          </execution>
		        </executions>
		      </plugin>
		      <plugin>
		        <groupId>org.codehaus.mojo</groupId>
		        <artifactId>exec-maven-plugin</artifactId>
		        <version>1.2.1</version>
		        <executions>
		          <execution>
		          <goals>
		            <goal>exec</goal>
		          </goals>
		          </execution>
		        </executions>
		        <configuration>
		          <executable>java</executable>
		          <includeProjectDependencies>true</includeProjectDependencies>
		          <includePluginDependencies>false</includePluginDependencies>
		          <classpathScope>compile</classpathScope>
		          <mainClass>${storm.topology}</mainClass>
		        </configuration>
		      </plugin>
		    </plugins>
		    <resources>
		      <resource>
		        <directory>${basedir}/conf</directory>
		        <filtering>false</filtering>
		        <includes>
		          <include>Config.properties</include>
				  <include>hbase-site.xml</include>
		        </includes>
		      </resource>
		    </resources>
		  </build>

	Isso diz ao Maven para fazer o seguinte ao criar o projeto:

	* Inclua o arquivo de recursos **/conf/Config.properties**. Esse arquivo será criado mais tarde, mas conterá informações de configuração para conectar-se ao Hub de Eventos do Azure.
	* Inclua o arquivo de recursos **/conf/hbase-site.xml**. Esse arquivo será criado mais tarde, mas conterá informações sobre como conectar-se ao HBase
	* Use o **maven-compiler-plugin** para compilar o aplicativo.
	* Use o **maven-shade-plugin** para criar um uberjar ou fat jar, que contém esse projeto e quaisquer eventuais dependências requeridas.
	* Use o **exec-maven-plugin**, que lhe permite executar o aplicativo localmente sem um cluster Hadoop.

### Adicionar arquivos de configuração

O **eventhubs-storm-spout** lê informações de configuração de um arquivo **Config.properties**. Isso diz a que hub de eventos se conectar. Embora você possa especificar um arquivo de configuração ao iniciar a topologia em um cluster, incluir um no projeto fornece uma configuração padrão conhecida.

1. No diretório **TemperatureMonitor** crie um novo diretório chamado **conf**.

2. No diretório **conf**, crie dois novos arquivos:

	* **Config.properties** - contém as configurações para o hub de eventos
	* **hbase-site.xml** - contém as configurações para conexão ao hbase

3. Use o seguinte como conteúdo para o arquivo **Config.properties**.

		eventhubspout.username = storm

		eventhubspout.password = <the key of the 'storm' policy>

		eventhubspout.namespace = <the event hub namespace>

		eventhubspout.entitypath = <the event hub name>

		eventhubspout.partitions.count = <the number of partitions for the event hub>

		# if not provided, will use storm's zookeeper settings
		# zookeeper.connectionstring=localhost:2181

		eventhubspout.checkpoint.interval = 10

		eventhub.receiver.credits = 1024

	Substitua a **senha** pela chave da política do **storm** criada anteriormente no Hub de Eventos.
	
	Substitua **namespace** pelo namespace do seu Hub de Eventos.
	
	Substitua o **entitpath** pelo nome de seu Hub de eventos.

4. Use o seguinte como conteúdo para o arquivo **hbase-site.xml**.

		<?xml version="1.0"?>
		<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
		<!--
		/**
		 * Copyright 2010 The Apache Software Foundation
		 *
		 * Licensed to the Apache Software Foundation (ASF) under one
		 * or more contributor license agreements.  See the NOTICE file
		 * distributed with this work for additional information
		 * regarding copyright ownership.  The ASF licenses this file
		 * to you under the Apache License, Version 2.0 (the
		 * "License"); you may not use this file except in compliance
		 * with the License.  You may obtain a copy of the License at
		 *
		 *     http://www.apache.org/licenses/LICENSE-2.0
		 *
		 * Unless required by applicable law or agreed to in writing, software
		 * distributed under the License is distributed on an "AS IS" BASIS,
		 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		 * See the License for the specific language governing permissions and
		 * limitations under the License.
		 */
		-->
		<configuration>
		  <property>
		    <name>hbase.cluster.distributed</name>
		    <value>true</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.quorum</name>
		    <value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.property.clientPort</name>
		    <value>2181</value>
		  </property>
		</configuration>

5. No arquivo **hbase-site.xml**, substitua o valor do **suffix** do sufixo das entradas do zookeeper pelo sufixo DNS recuperado anteriormente para o HBase. Por exemplo, **zookeeper0.mycluster.b4.internal.cloudapp.net, zookeeper1.mycluster.b4.internal.cloudapp.net, zookeeper2.mycluster.b4.internal.cloudapp.net**.

6. Salve os arquivos.

### Adicionar auxiliares

Para dar suporte à serialização para e de JSON, precisamos de algumas classes auxiliares que definam a estrutura do objeto.

1. No diretório **\temperaturemonitor\src\main\java\com\microsoft\examples**, crie um novo diretório chamado **auxiliares**.

2. No diretório **auxiliares**, crie dois novos arquivos:

	* **EventHubMessage.java** - define o formato de mensagem do hub de eventos

	* **SignalRMessage.java** - define o formato da mensagem enviada ao SignalR

3. Use o seguinte como conteúdo do arquivo **EventHubMessage.java**.

		package com.microsoft.examples;

		public class EventHubMessage {
		  String TimeStamp;
		  int DeviceId;
		  int Temperature;
		}

4. Use o seguinte como conteúdo do arquivo **SignalRMessage.java**.

		package com.microsoft.examples;

		public class SignalRMessage {
		  int device;
		  int temperature;
		}

5. Salve e feche esses arquivos.

### Adicionar bolts

Bolts fazem o processamento principal em uma topologia. Para esta topologia, há três bolts, embora um seja o hbase-bolt, que serão baixados automaticamente quando o projeto for criado.

1. No diretório **\temperaturemonitor\src\main\java\com\microsoft\examples**, crie um novo diretório chamado **bolts**.

2. No diretório **bolts**, crie dois novos arquivos:

	* **ParserBolt.java** - analisa a mensagem recebida do Hub de Eventos em campos individuais, então emite dois fluxos
	* **DashboardBolt.java** - registra em log informações para o painel da Web via SignalR

3. Use o seguinte como conteúdo do arquivo **ParserBolt.java**.

		package com.microsoft.examples;

		import backtype.storm.topology.base.BaseBasicBolt;
		import backtype.storm.topology.BasicOutputCollector;
		import backtype.storm.topology.OutputFieldsDeclarer;
		import backtype.storm.tuple.Tuple;
		import backtype.storm.tuple.Fields;
		import backtype.storm.tuple.Values;

		import com.google.gson.Gson;
		import com.google.gson.GsonBuilder;

		public class ParserBolt extends BaseBasicBolt {

		  //Declare output fields & streams
		  //hbasestream is all fields, and goes to hbase
		  //dashstream is just the device and temperature, and goes to the dashboard
		  @Override
		  public void declareOutputFields(OutputFieldsDeclarer declarer) {
		    declarer.declareStream("hbasestream", new Fields("timestamp", "deviceid", "temperature"));
		    declarer.declareStream("dashstream", new Fields("deviceid", "temperature"));
		  }

		  //Process tuples
		  @Override
		  public void execute(Tuple tuple, BasicOutputCollector collector) {
		    Gson gson = new Gson();
		    //Should only be one tuple, which is the JSON message from the spout
		    String value = tuple.getString(0);

			//Deal with cases where we get multiple
			//EventHub messages in one tuple
			String[] arr = value.split("}");
			for (String ehm : arr)
			{

			    //Convert it from JSON to an object
				EventHubMessage msg = new Gson().fromJson(ehm.concat("}"),EventHubMessage.class);

			    //Pull out the values and emit as a stream
			    String timestamp = msg.TimeStamp;
			    int deviceid = msg.DeviceId;
			    int temperature = msg.Temperature;
			    collector.emit("hbasestream", new Values(timestamp, deviceid, temperature));
			    collector.emit("dashstream", new Values(deviceid, temperature));
			}
		  }
		}

4. Use o seguinte como conteúdo do arquivo **DashboardBolt.java**.

		package com.microsoft.examples;

		import backtype.storm.topology.BasicOutputCollector;
		import backtype.storm.topology.OutputFieldsDeclarer;
		import backtype.storm.topology.base.BaseBasicBolt;
		import backtype.storm.tuple.Tuple;
		import backtype.storm.task.TopologyContext;
		import backtype.storm.Config;
		import backtype.storm.Constants;

		import microsoft.aspnet.signalr.client.Action;
		import microsoft.aspnet.signalr.client.ErrorCallback;
		import microsoft.aspnet.signalr.client.LogLevel;
		import microsoft.aspnet.signalr.client.Logger;
		import microsoft.aspnet.signalr.client.MessageReceivedHandler;
		import microsoft.aspnet.signalr.client.hubs.HubConnection;
		import microsoft.aspnet.signalr.client.hubs.HubProxy;

		import com.google.gson.Gson;
		import com.google.gson.GsonBuilder;

		import java.util.Map;

		public class DashboardBolt extends BaseBasicBolt {
		  //Connection and proxy for SignalR hub
		  private HubConnection conn;
		  private HubProxy proxy;

		  //Declare output fields
		  @Override
		  public void declareOutputFields(OutputFieldsDeclarer declarer) {
		    //no stream output - we talk directly to SignalR
		  }

		  @Override
		  public void prepare(Map config, TopologyContext context) {

		    // Connect to the DashHub SignalR server
		    conn = new HubConnection("http://dashboard.azurewebsites.net/");
		    // Create the hub proxy
		    proxy = conn.createHubProxy("DashHub");
		    // Subscribe to the error event
		    conn.error(new ErrorCallback() {
		      @Override
		      public void onError(Throwable error) {
		        error.printStackTrace();
		      }
		    });
		    // Subscribe to the connected event
		    conn.connected(new Runnable() {
		      @Override
		      public void run() {
		        System.out.println("CONNECTED");
		      }
		    });
		    // Subscribe to the closed event
		    conn.closed(new Runnable() {
		      @Override
		      public void run() {
		        System.out.println("DISCONNECTED");
		      }
		    });
		    // Start the connection
		    conn.start()
		      .done(new Action<Void>() {
		        @Override
		        public void run(Void obj) throws Exception {
		          System.out.println("Done Connecting!");
		        }
		    });
		  }

		  //Process tuples
		  @Override
		  public void execute(Tuple tuple, BasicOutputCollector collector) {
		    Gson gson = new Gson();
		    try {
		      //Get the deviceid and temperature by field name
		      int deviceid = tuple.getIntegerByField("deviceid");
		      int temperature = tuple.getIntegerByField("temperature");
		      //Construct the SignalR message
		      SignalRMessage srMessage = new SignalRMessage();
		      srMessage.device = deviceid;
		      srMessage.temperature = temperature;
		      // send it as JSON
		      proxy.invoke("send", gson.toJson(srMessage));
		    } catch (Exception e) {
		       // LOG.error("Bolt execute error: {}", e);
		       collector.reportError(e);
		    }
		  }
		}

	Substitua`http://dashboard.azurewebsites.net/` pelo endereço do site do Azure em que você publicou o painel anteriormente. Por exemplo, http://mydashboard.azurewebsites.net.

5. Salve e feche os arquivos.

### Definir a topologia

A topologia descreve como os dados fluem entre spouts e bolts em uma topologia, bem como o grau de paralelismo para a topologia e os componentes dentro dela.

1. No diretório **\temperaturemonitor\src\main\java\com\microsoft\examples**, crie um novo arquivo chamado **Temperature.java**.

2. Abra o arquivo **Temperature.java** e use o seguinte como conteúdo.

		package com.microsoft.examples;

		import backtype.storm.Config;
		import backtype.storm.LocalCluster;
		import backtype.storm.StormSubmitter;
		import backtype.storm.generated.StormTopology;
		import backtype.storm.topology.TopologyBuilder;
		import backtype.storm.tuple.Fields;
		import com.microsoft.eventhubs.spout.EventHubSpout;
		import com.microsoft.eventhubs.spout.EventHubSpoutConfig;

		import java.io.FileReader;
		import java.util.Properties;

		//hbase
		import org.apache.storm.hbase.bolt.mapper.SimpleHBaseMapper;
		import org.apache.storm.hbase.bolt.HBaseBolt;
		import java.util.Map;
		import java.util.HashMap;
		import backtype.storm.tuple.Fields;

		public class Temperature
		{
		  protected EventHubSpoutConfig spoutConfig;
		  protected int numWorkers;

		  // Reads the configuration information for the Event Hub spout
		  protected void readEHConfig(String[] args) throws Exception {
		    Properties properties = new Properties();
		    if(args.length > 1) {
		      properties.load(new FileReader(args[1]));
		    }
		    else {
		      properties.load(Temperature.class.getClassLoader().getResourceAsStream(
		        "Config.properties"));
		    }

		    String username = properties.getProperty("eventhubspout.username");
		    String password = properties.getProperty("eventhubspout.password");
		    String namespaceName = properties.getProperty("eventhubspout.namespace");
		    String entityPath = properties.getProperty("eventhubspout.entitypath");
		    String zkEndpointAddress = properties.getProperty("zookeeper.connectionstring");
		    int partitionCount = Integer.parseInt(properties.getProperty("eventhubspout.partitions.count"));
		    int checkpointIntervalInSeconds = Integer.parseInt(properties.getProperty("eventhubspout.checkpoint.interval"));
		    int receiverCredits = Integer.parseInt(properties.getProperty("eventhub.receiver.credits"));
		    System.out.println("Eventhub spout config: ");
		    System.out.println("  partition count: " + partitionCount);
		    System.out.println("  checkpoint interval: " + checkpointIntervalInSeconds);
		    System.out.println("  receiver credits: " + receiverCredits);
		    spoutConfig = new EventHubSpoutConfig(username, password,
		      namespaceName, entityPath, partitionCount, zkEndpointAddress,
		      checkpointIntervalInSeconds, receiverCredits);

		    //set the number of workers to be the same as partition number.
		    //the idea is to have a spout and a partial count bolt co-exist in one
		    //worker to avoid shuffling messages across workers in storm cluster.
		    numWorkers = spoutConfig.getPartitionCount();

		    if(args.length > 0) {
		      //set topology name so that sample Trident topology can use it as stream name.
		      spoutConfig.setTopologyName(args[0]);
		    }
		  }

		  // Create the spout using the configuration
		  protected EventHubSpout createEventHubSpout() {
		    EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
		    return eventHubSpout;
		  }

		  // Build the topology
		  protected StormTopology buildTopology(EventHubSpout eventHubSpout, SimpleHBaseMapper mapper) {
		    TopologyBuilder topologyBuilder = new TopologyBuilder();
		    // Name the spout 'EventHubsSpout', and set it to create
		    // as many as we have partition counts in the config file
		    topologyBuilder.setSpout("EventHub", eventHubSpout, spoutConfig.getPartitionCount())
		      .setNumTasks(spoutConfig.getPartitionCount());
		    // Create the parser bolt, which subscribes to the stream from EventHub
		    topologyBuilder.setBolt("Parser", new ParserBolt(), spoutConfig.getPartitionCount())
		      .localOrShuffleGrouping("EventHub").setNumTasks(spoutConfig.getPartitionCount());
		    // Create the dashboard bolt, which subscribes to the stream from Parser
		    topologyBuilder.setBolt("Dashboard", new DashboardBolt(), spoutConfig.getPartitionCount())
		      .fieldsGrouping("Parser", "dashstream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
		    // Create the HBase bolt, which subscribes to the stream from Parser
		    // WARNING - uncomment the following two lines when deploying
			// leave commented when testing locally
			// topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
		    //  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());
		    return topologyBuilder.createTopology();
		  }

		  protected void submitTopology(String[] args, StormTopology topology, Config config) throws Exception {
		    // Config config = new Config();
		    config.setDebug(false);

		    //Enable metrics
		    config.registerMetricsConsumer(backtype.storm.metric.LoggingMetricsConsumer.class, 1);

		    // Is this running locally, or on an HDInsight cluster?
		    if (args != null && args.length > 0) {
		      config.setNumWorkers(numWorkers);
		      StormSubmitter.submitTopology(args[0], config, topology);
		    } else {
		      config.setMaxTaskParallelism(2);

		      LocalCluster localCluster = new LocalCluster();
		      localCluster.submitTopology("test", config, topology);

		      Thread.sleep(5000000);

		      localCluster.shutdown();
		    }
		  }

		  // Loads the configuration, creates the spout, builds the topology,
		  // and then submits it
		  protected void runScenario(String[] args) throws Exception{
		    readEHConfig(args);
		    Config config = new Config();

		    //hbase configuration
		    Map<String, Object> hbConf = new HashMap<String, Object>();
		    if(args.length > 0) {
		      hbConf.put("hbase.rootdir", args[0]);
		    }
		    config.put("hbase.conf", hbConf);
		    SimpleHBaseMapper mapper = new SimpleHBaseMapper()
		          .withRowKeyField("deviceid")
		          .withColumnFields(new Fields("timestamp", "temperature"))
		          .withColumnFamily("cf");

		    EventHubSpout eventHubSpout = createEventHubSpout();
		    StormTopology topology = buildTopology(eventHubSpout, mapper);
		    submitTopology(args, topology, config);
		  }

		  public static void main(String[] args) throws Exception {
		    Temperature scenario = new Temperature();
		    scenario.runScenario(args);
		  }
		}

	> [AZURE.NOTE] Observe que as linhas para **HBaseBolt** têm comentários. Isso ocorre porque a próxima etapa é executar a topologia localmente. Uma vez que o HBaseBolt conversa diretamente com o HBase, isso retornará erros se estiver habilitado. A menos que você tenha configurado uma rede virtual com um servidor DNS e unido seu computador local à rede virtual também.

### Testar a topologia localmente

Para compilar e testar o arquivo no computador de desenvolvimento, use as seguintes etapas.

1. Inicie o aplicativo **SendEvent** .NET para começar a enviar eventos, de modo que tenhamos algo para ler do Hub de Eventos.

2. Abra um navegador da Web para o painel da web implantado anteriormente em um site da Web do Azure. Isso lhe permitirá ver o gráfico plotar os valores conforme eles fluem pela topologia

3. Inicie a topologia localmente usando o seguinte comando

	mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

	Isso iniciará a topologia, lerá arquivos do Hub de Eventos e os enviará ao painel em execução nos sites da Websites do Azure. Você deve ver linhas aparecerem no painel da web.

4. Depois de verificar se isso funciona, pare a topologia inserindo Ctrl-C. Para interromper o aplicativo SendEvent, selecione a janela e pressione qualquer tecla.

### Habilitar o HBaseBolt e preparar o HBase

1. Abra o arquivo **Temperature.java** e remova o comentário (//) das seguintes linhas:

		//topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    	//  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

	Isso habilita o bolt do HBase.

2. Salve **Temperature.java**.

3. Usando a área de trabalho remota, conecte-se ao cluster HBase.

4. Na área de trabalho, inicie a linha de comando do HDInsight e insira os seguintes comandos.

		cd %hbase_home%
		bin\hbase shell

5. No shell do HBase, insira o seguinte comando para criar uma tabela na qual os dados do sensor serão armazenados.

		create 'SensorData', 'cf'

6. Verifique se a tabela não contém dados inserindo o seguinte comando.

		scan 'SensorData'

Deixe esse aviso aberto no shell do HBase por enquanto.

## Empacotar e implantar a topologia no HDInsight

No seu ambiente de desenvolvimento, use as seguintes etapas para executar a topologia Temperature no  cluster Storm.

1. Use o seguinte comando para criar um pacote JAR do seu projeto.

		mvn package

	Isso criará um arquivo denominado **TemperatureMonitor-1.0-SNAPSHOT.jar** no diretório de **destino** do seu projeto.

2. No computador de desenvolvimento local, inicie o aplicativo .NET **SendEvents** .NET de modo que haja alguns eventos a ler.

3. Conecte-se ao cluster Storm usando a Área de Trabalho Remota e copie o arquivo **TemperatureMonitor-1.0-SNAPSHOT.jar** no diretório **c:\apps\dist\storm&lt;número_da_versão>**.

4. Use o ícone **Linha de Comando do HDInsight** na área de trabalho do cluster para abrir um novo prompt de comando e use os seguintes comandos para executar a topologia.

		cd %storm_home%
		bin\storm jar TemperatureMonitor-1.0-SNAPSHOT.jar com.microsoft.examples.Temperature Temperature

5. Quando a topologia tiver sido iniciada, pode levar alguns segundos antes de os itens começarem a aparecer no painel da Web.

6. Depois de os itens aparecerem no painel, troque para a sessão da área de trabalho remota no cluster HBase.

7. No shell HBase, insira o seguinte comando.

		scan 'SensorData'

	Observe que ele agora retorna várias linhas de dados que foram gravadas pela topologia do Storm.

8. Para interromper a topologia, acesse a sessão da área de trabalho remota com o cluster do Storm e insira o seguinte na Linha de Comando HDInsight.

		bin\storm kill Temperature

	Depois de alguns segundos, a topologia parará.

## Resumo

Você agora aprendeu a usar o Storm para ler dados do Hub de Eventos, armazenar dados no HBase e exibir informações do Storm em um painel externo usando SignalR e D3.js.

* Para obter mais informações sobre o Apache Storm, consulte [https://storm.incubator.apache.org/](https://storm.incubator.apache.org/)

* Para obter mais informações sobre HBase com HDInsight, consulte a [Visão geral do HBase com HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-hbase-overview/)

* Para obter mais informações sobre SignalR, consulte [ASP.NET SignalR](http://signalr.net/)

* Para obter mais informações sobre D3.js, consulte [D3.js - Documentos Conduzidos por Dados](http://d3js.org/)

* Para obter mais informações sobre o desenvolvimento com SCP, consulte [Desenvolver aplicativos de processamento de dados de streaming com SCP.NET e C# no Storm no HDInsight](hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application.md)

[azure-portal]: https://manage.windowsazure.com/
<!--HONumber=42-->
