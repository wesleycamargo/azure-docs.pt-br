<properties
	pageTitle="Executar um aplicativo do .NET de computação intensiva em uma VM | Microsoft Azure"
	description="Saiba como implantar e executar um aplicativo .NET que exige computação intensa em uma máquina virtual do Azure e como usar filas de Barramento de Serviço do Azure para monitorar o progresso remotamente."
	services="virtual-machines"
	documentationCenter=".net"
	authors="wadepickett"
	manager="wpickett"
	editor="mollybos"
	tags=“azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/25/2015"
	ms.author="wpickett"/>

# Como executar uma tarefa que exija muita computação no .NET em uma máquina virtual Azure

Com o Azure, você pode usar uma máquina virtual para lidar com tarefas de computação intensiva. Por exemplo, uma máquina virtual pode lidar com tarefas e fornecer resultados às máquinas dos clientes ou aos aplicativos móveis. Depois de concluir o tutorial, você saberá como criar uma máquina virtual que executa um aplicativo .NET de uso intensivo de computação que pode ser monitorado por outro aplicativo .NET.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


Este tutorial pressupõe que você saiba como criar aplicativos de console do .NET. Nenhum conhecimento do Azure é assumido.

Você aprenderá a:

* Como criar uma máquina virtual.
* Fazer logon remotamente na máquina virtual.
* Como criar um namespace de Barramento de Serviço do Azure.
* Como criar um aplicativo do .NET que execute uma tarefa que exija muita computação.
* Como criar um aplicativo do .NET que monitore o progresso da tarefa que exige muita computação.
* Como executar os aplicativos do .NET.
* Como parar os aplicativos do .NET.

Este tutorial usará o problema do Caixeiro Viajante para a tarefa de computação intensiva. Este é um exemplo do aplicativo do .NET que executa a tarefa que exige computação intensa:

![Solucionador de problemas do Caixeiro Viajante][solver_output]

Este é um exemplo do aplicativo do .NET que monitora a tarefa que exige computação intensa:

![Cliente de problemas do Caixeiro Viajante][client_output]

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## Para criar uma máquina virtual

1. Faça logon no [portal clássico do Azure](https://manage.windowsazure.com).
2. Clique em **Novo**.
3. Clique em **Máquina virtual**.
4. Clique em **Criação rápida**.
5. Na tela **Criar uma máquina virtual**, insira um valor para **Nome DNS**.
6. Na lista suspensa **Imagem**, selecione uma imagem, como **Windows Server 2012 R2**.
7. Digite um nome para o administrador no campo **Nome do Usuário**. Lembre-se do nome e da senha que você digitar a seguir, pois vai usá-los ao fazer logon remotamente na máquina virtual.
8. Digite uma senha no campo **Nova senha** e insira-a novamente no campo **Confirmar**.
9. Na lista suspensa **Local**, selecione o local do data center da máquina virtual.
10. Clique em **Criar máquina virtual**. Você pode monitorar o status na seção **Máquinas virtuais** do portal clássico do Azure. Quando o status for exibido como **Ativo**, você poderá fazer logon na máquina virtual.

## Para fazer logon remotamente na máquina virtual

1. Faça logon no [portal clássico do Azure](https://manage.windowsazure.com).
2. Clique em **Máquinas Virtuais**.
3. Clique no nome da máquina virtual na qual você deseja fazer logon.
4. Clique em **Conectar**.
5. Responda às solicitações conforme necessário para se conectar à máquina virtual. Quando for solicitado o nome do administrador e a senha, use os valores que você forneceu quando criou a máquina virtual.

## Como criar um namespace do Barramento de Serviço

Para começar a usar filas do Barramento de Serviço no Azure, primeiro crie um namespace de serviço. Um namespace de serviço fornece um contêiner de controle para endereçamento dos recursos do Barramento de Serviço em seu aplicativo.

Para criar um namespace de serviço:

1.  Faça logon no [portal clássico do Azure](https://manage.windowsazure.com).
2.  No painel de navegação esquerdo do portal clássico do Azure, clique em **Barramento de Serviço**.
3.  No painel inferior do portal clássico do Azure, clique em **Criar**.

    ![Criar novo barramento de serviço][create_service_bus]
4.  Na caixa de diálogo **Criar um namespace**, digite um nome de namespace. O sistema verifica imediatamente se o nome está disponível, porque ele precisa ser um nome exclusivo.

    ![Criar uma caixa de diálogo de namespace][create_namespace_dialog]
5.  Depois de verificar se o nome do namespace está disponível, escolha a região em que o namespace deve ser hospedado (não se esqueça de usar a mesma região em que a máquina virtual está hospedada).

    > [AZURE.IMPORTANT]Escolha a **mesma região** que você usa ou pretende usar na máquina virtual. Isso lhe dará o melhor desempenho.

6. Se você tiver mais de uma assinatura do Azure para a conta com a qual você fez logon, selecione a assinatura a ser usada no namespace. (Se tiver somente uma assinatura para a conta com a qual você fez logon, uma lista suspensa contendo suas assinaturas não será mostrada.)
7. Clique na marca de seleção. Agora, o sistema cria o namespace de serviço e o habilita. Talvez você precise aguardar vários minutos, conforme o sistema fornece recursos para sua conta.

	![Clique em criar captura de tela][click_create]

O namespace que você criou aparecerá no portal clássico do Azure e demorará algum tempo para ser ativado. Aguarde até que o status esteja **Ativo** para passar à próxima etapa.

## Obter as credenciais de gerenciamento padrão do namespace

A fim de executar operações de gerenciamento, como criar uma fila no novo namespace, você precisar obter as credenciais de gerenciamento para o namespace.

1.  No painel de navegação esquerdo, clique em **Barramento de Serviço** para exibir a lista de namespaces disponíveis.![Captura de tela de namespaces disponíveis][available_namespaces]
2.  Selecione o namespace que você acabou de criar na lista.![Captura de tela da lista de namespaces][namespace_list]
3. Clique em **Informações de Conexão**. ![Botão da chave de acesso][access_key_button]
4.  Na caixa de diálogo, encontre a entrada **Cadeia de Conexão**. Anote esse valor, pois você usará essas informações posteriormente no tutorial para executar operações com o namespace.

## Como criar um aplicativo do .NET que execute uma tarefa que exija muita computação

1. Na máquina de desenvolvimento (que não precisa ser a máquina virtual que você criou), baixe o [SDK do Azure para .NET](http://azure.microsoft.com/develop/net/).
2. Crie um aplicativo de console do .NET com o projeto chamado TSPSolver. Verifique se a estrutura de destino está definida como .**NET Framework 4** ou posterior (e não **.NET Framework 4 Client Profile**). A estrutura de destino poderá ser definida depois que você criar um projeto fazendo o seguinte: no menu do Visual Studio, clique em **Projetos**, em **Propriedades**, na guia **Aplicativo** e defina o valor para **Estrutura de destino**.
3. Adicione a biblioteca Microsoft ServiceBus. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse em **TSPSolver**, clique em **Adicionar Referência**, clique na guia **Procurar**, procure o SDK do .NET do Azure (por exemplo, em C:\\Arquivos de Programas\\Microsoft SDKs\\Azure.NET SDK\\v2.5\\ToolsRef) e selecione **Microsoft.ServiceBus.dll** como referência.
4. Adicione a biblioteca de Serialização de Tempo de Execução do Sistema. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse em **TSPSolver**, clique em **Adicionar Referência**, clique na guia **.NET** e selecione **System.Runtime.Serialization** como referência.
5. Use o código de exemplo ao final desta seção para o conteúdo do arquivo Program.cs.
6. Modifique o espaço reservado **your\_connection\_string** para usar a sua **cadeia de conexão** do Barramento de Serviço.
7. Compile o aplicativo. Isso criará TSPSolver.exe na pasta bin do projeto (bin\\release ou bin\\debug, dependendo do destino, uma versão ou uma compilação de depuração). Você copiará esse executável e Microsoft.ServiceBus.dll para a máquina virtual depois.

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;

	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;

	namespace TSPSolver
	{
	    class Program
	    {
	        // Value specifying how often to provide an update to the console.
	        private static long loopCheck = 100000000;
	        private static long nTimes = 0, nLoops = 0;

	        private static double[,] distances;
	        private static String[] cityNames;
	        private static int[] bestOrder;
	        private static double minDistance;

	        private static NamespaceManager namespaceManager;
	        private static QueueClient queueClient;
	        private static String queueName = "TSPQueue";

	        private static void BuildDistances(String fileLocation, int numCities)
	        {

	            try
	            {
	                StreamReader sr = new StreamReader(fileLocation);
	                String[] sep1 = { ", " };

	                double[,] cityLocs = new double[numCities, 2];

	                for (int i = 0; i < numCities; i++)
	                {
	                    String[] line = sr.ReadLine().Split(sep1, StringSplitOptions.None);
	                    cityNames[i] = line[0];
	                    cityLocs[i, 0] = Convert.ToDouble(line[1]);
	                    cityLocs[i, 1] = Convert.ToDouble(line[2]);
	                }
	                sr.Close();

	                for (int i = 0; i < numCities; i++)
	                {
	                    for (int j = i; j < numCities; j++)
	                    {
	                        distances[i, j] = hypot(Math.Abs(cityLocs[i, 0] - cityLocs[j, 0]), Math.Abs(cityLocs[i, 1] - cityLocs[j, 1]));
	                        distances[j, i] = distances[i, j];
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        private static double hypot(double x, double y)
	        {
	            return Math.Sqrt(x * x + y * y);
	        }

	        private static void permutation(List<int> startCities, double distSoFar, List<int> restCities)
	        {
	            try
	            {

	                nTimes++;
	                if (nTimes == loopCheck)
	                {
	                    nLoops++;
	                    nTimes = 0;
	                    DateTime dateTime = DateTime.Now;
	                    Console.Write("Current time is {0}.", dateTime);
	                    Console.WriteLine(" Completed {0} iterations of size of {1}.", nLoops, loopCheck);
	                }

	                if ((restCities.Count == 1) && ((minDistance == -1) || (distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 1]] < minDistance)))
	                {
	                    startCities.Add(restCities[0]);
	                    newBestDistance(startCities, distSoFar + distances[restCities[0], startCities[0]] + distances[restCities[0], startCities[startCities.Count - 2]]);
	                    startCities.Remove(startCities[startCities.Count - 1]);
	                }
	                else
	                {
	                    for (int i = 0; i < restCities.Count; i++)
	                    {
	                        startCities.Add(restCities[0]);
	                        restCities.Remove(restCities[0]);
	                        permutation(startCities, distSoFar + distances[startCities[startCities.Count - 1], startCities[startCities.Count - 2]], restCities);
	                        restCities.Add(startCities[startCities.Count - 1]);
	                        startCities.Remove(startCities[startCities.Count - 1]);
	                    }
	                }
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        private static void newBestDistance(List<int> cities, double distance)
	        {
	            try
	            {
	                minDistance = distance;
	                String cityList = "Shortest distance is " + minDistance + ", with route: ";

	                for (int i = 0; i < bestOrder.Length; i++)
	                {
	                    bestOrder[i] = cities[i];
	                    cityList += cityNames[bestOrder[i]];
	                    if (i != bestOrder.Length - 1)
	                        cityList += ", ";
	                }
	                Console.WriteLine(cityList);
	                queueClient.Send(new BrokeredMessage(cityList));
	            }
	            catch (Exception e)
	            {
	                throw e;
	            }
	        }

	        static void Main(string[] args)
	        {
	            try
	            {

                  String connectionString = @"your_connection_string";

	                int numCities = 10; // Use as the default, if no value is specified
	                // at the command line.
	                if (args.Count() != 0)
	                {

	                    if (args[0].ToLower().CompareTo("createqueue") == 0)
	                    {
	                        // No processing to occur other than creating the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.CreateQueue(queueName);
	                        Console.WriteLine("Queue named {0} was created.", queueName);
	                        Environment.Exit(0);
	                    }

	                    if (args[0].ToLower().CompareTo("deletequeue") == 0)
	                    {
	                        // No processing to occur other than deleting the queue.
	                        namespaceManager = NamespaceManager.CreateFromConnectionString(connectionString);
	                        namespaceManager.DeleteQueue("TSPQueue");
	                        Console.WriteLine("Queue named {0} was deleted.", queueName);
	                        Environment.Exit(0);
	                    }

	                    // Neither creating or deleting a queue.
	                    // Assume the value passed in is the number of cities to solve.
	                    numCities = Convert.ToInt32(args[0]);
	                }

	                Console.WriteLine("Running for {0} cities.", numCities);

	                queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

	                List<int> startCities = new List<int>();
	                List<int> restCities = new List<int>();

	                startCities.Add(0);
	                for (int i = 1; i < numCities; i++)
	                {
	                    restCities.Add(i);
	                }
	                distances = new double[numCities, numCities];
	                cityNames = new String[numCities];
	                BuildDistances(@"c:\tsp\cities.txt", numCities);
	                minDistance = -1;
	                bestOrder = new int[numCities];
	                permutation(startCities, 0, restCities);
	                Console.WriteLine("Final solution found!");
	                queueClient.Send(new BrokeredMessage("Complete"));

	                queueClient.Close();
	                Environment.Exit(0);

	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}



## Como criar um aplicativo do .NET que monitore o progresso da tarefa que exige muita computação

1. No computador de desenvolvimento, crie um aplicativo de console do .NET usando TSPClient como o nome do projeto. Verifique se a estrutura de destino está definida como .**NET Framework 4** ou posterior (e não **.NET Framework 4 Client Profile**). A estrutura de destino poderá ser definida depois que você criar um projeto fazendo o seguinte: no menu do Visual Studio, clique em **Projetos**, em **Propriedades**, clique na guia **Aplicativo** e defina o valor para **Estrutura de destino**.
2. Adicione a biblioteca Microsoft ServiceBus. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse em **TSPClient**, clique em **Adicionar Referência**, clique na guia **Procurar**, procure o SDK do .NET do Azure (por exemplo, em C:\\Arquivos de Programas\\Microsoft SDKs\\Azure.NET SDK\\v2.5\\ToolsRef) e selecione **Microsoft.ServiceBus.dll** como referência.
3. Adicione a biblioteca de Serialização de Tempo de Execução do Sistema. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse em **TSPClient**, clique em **Adicionar Referência**, clique na guia **.NET** e selecione **System.Runtime.Serialization** como referência.
4. Use o código de exemplo ao final desta seção para o conteúdo do arquivo Program.cs.
5. Modifique o espaço reservado **your\_connection\_string** para usar a sua **cadeia de conexão** do Barramento de Serviço.
6. Compile o aplicativo. Isso criará TSPClient.exe na pasta bin do projeto (bin\\release ou bin\\debug, dependendo do destino, uma versão ou uma compilação de depuração). Você pode executar esse código na máquina de desenvolvimento ou copiar esse executável e Microsoft.ServiceBus.dll para uma máquina que executará o aplicativo cliente (ele não precisa estar na máquina virtual).

<p/>

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.IO;

	using Microsoft.ServiceBus;
	using Microsoft.ServiceBus.Messaging;
	using System.Threading; // For Thread.Sleep

	namespace TSPClient
	{
	    class Program
	    {

	        static void Main(string[] args)
	        {

	            try
	            {

	                Console.WriteLine("Starting at {0}", DateTime.Now);

									String connectionString = @"your_connection_string";

	                QueueClient queueClient = QueueClient.CreateFromConnectionString(connectionString, "TSPQueue");

	                BrokeredMessage message;

	                int waitMinutes = 3;  // Use as the default, if no value
	                // is specified at command line.

	                if (0 != args.Length)
	                {
	                    waitMinutes = Convert.ToInt16(args[0]);
	                }

	                String waitString;
	                waitString = (waitMinutes == 1) ? "minute" : waitMinutes.ToString() + " minutes";

	                while (true)
	                {
	                    message = queueClient.Receive();

	                    if (message != null)
	                    {
	                        try
	                        {
	                            string str = message.GetBody<string>();
	                            Console.WriteLine(str);

	                            // Remove message from queue.
	                            message.Complete();

	                            if ("Complete" == str)
	                            {
	                                Console.WriteLine("Finished at {0}.", DateTime.Now);
	                                break;
	                            }
	                        }
	                        catch (Exception e)
	                        {
	                            // Indicates a problem. Unlock the message in the queue.
	                            message.Abandon();
	                            throw e;
	                        }
	                    }
	                    else
	                    {
	                        // The queue is empty.
	                        Console.WriteLine("Queue is empty. Sleeping for another {0}.", waitString);
	                        System.Threading.Thread.Sleep(60000 * waitMinutes);
	                    }
	                }
	                queueClient.Close();
	                Environment.Exit(0);
	            }
	            catch (ServerBusyException serverBusyException)
	            {
	                Console.WriteLine("ServerBusyException encountered");
	                Console.WriteLine(serverBusyException.Message);
	                Console.WriteLine(serverBusyException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (ServerErrorException serverErrorException)
	            {
	                Console.WriteLine("ServerErrorException encountered");
	                Console.WriteLine(serverErrorException.Message);
	                Console.WriteLine(serverErrorException.StackTrace);
	                Environment.Exit(-1);
	            }
	            catch (Exception exception)
	            {
	                Console.WriteLine("Exception encountered");
	                Console.WriteLine(exception.Message);
	                Console.WriteLine(exception.StackTrace);
	                Environment.Exit(-1);
	            }
	        }
	    }
	}

## Como executar os aplicativos do .NET.

Execute o aplicativo que exige computação intensa, primeiro para criar a fila, depois para solucionar o Problema do Caixeiro Viajante, que adicionará a melhor rota atual para a fila do Barramento de Serviço. Enquanto o aplicativo que exige computação intensa estiver em execução (ou depois disso), execute o cliente para exibir os resultados da fila do Barramento de Serviço.

### Como executar o aplicativo que exige muita computação

1. Faça logon na máquina virtual.
2. Crie uma pasta chamada c:\\TSP. É onde você executará o aplicativo.
3. Copie TSPSolver.exe e Microsoft.ServiceBus.dll, ambos disponíveis na pasta bin do projeto TSPSolver, para c:\\TSP.
4. Crie um arquivo chamado c:\\TSP\\cities.txt com o seguinte conteúdo.

		City_1, 1002.81, -1841.35
		City_2, -953.55, -229.6
		City_3, -1363.11, -1027.72
		City_4, -1884.47, -1616.16
		City_5, 1603.08, -1030.03
		City_6, -1555.58, 218.58
		City_7, 578.8, -12.87
		City_8, 1350.76, 77.79
		City_9, 293.36, -1820.01
		City_10, 1883.14, 1637.28
		City_11, -1271.41, -1670.5
		City_12, 1475.99, 225.35
		City_13, 1250.78, 379.98
		City_14, 1305.77, 569.75
		City_15, 230.77, 231.58
		City_16, -822.63, -544.68
		City_17, -817.54, -81.92
		City_18, 303.99, -1823.43
		City_19, 239.95, 1007.91
		City_20, -1302.92, 150.39
		City_21, -116.11, 1933.01
		City_22, 382.64, 835.09
		City_23, -580.28, 1040.04
		City_24, 205.55, -264.23
		City_25, -238.81, -576.48
		City_26, -1722.9, -909.65
		City_27, 445.22, 1427.28
		City_28, 513.17, 1828.72
		City_29, 1750.68, -1668.1
		City_30, 1705.09, -309.35
		City_31, -167.34, 1003.76
		City_32, -1162.85, -1674.33
		City_33, 1490.32, 821.04
		City_34, 1208.32, 1523.3
		City_35, 18.04, 1857.11
		City_36, 1852.46, 1647.75
		City_37, -167.44, -336.39
		City_38, 115.4, 0.2
		City_39, -66.96, 917.73
		City_40, 915.96, 474.1
		City_41, 140.03, 725.22
		City_42, -1582.68, 1608.88
		City_43, -567.51, 1253.83
		City_44, 1956.36, 830.92
		City_45, -233.38, 909.93
		City_46, -1750.45, 1940.76
		City_47, 405.81, 421.84
		City_48, 363.68, 768.21
		City_49, -120.3, -463.13
		City_50, 588.51, 679.33

5. Em um prompt de comando, altere os diretórios para c:\\TSP.
6. Você precisará criar a fila do Barramento de Serviço antes de executar as permutas do TSP solver. Execute o seguinte comando para criar a fila do Barramento de Serviço.

        TSPSolver createqueue

7. Agora que a fila está criada, você pode executar as permutas de solver TSP. Por exemplo, execute o seguinte comando para executar o solver para 8 cidades.

        TSPSolver 8

 Se você não especificar um número, o solver será executado para 10 cidades. Como localiza rotas atuais mais curtas, o solver as adicionará à fila.

O solver será executado até terminar de examinar todas as rotas.

> [AZURE.NOTE]Quanto maior o número que você especificar, por mais tempo o solver será executado. Por exemplo, a execução de 14 cidades pode levar vários minutos, e a execução de 15 cidades pode levar várias horas. Aumentar para 16 ou mais cidades pode resultar em dias de tempo de execução (e eventualmente em semanas, meses e anos). Isso ocorre porque o rápido aumento do número de permutas avaliadas pelo solver, como o número de cidades, aumenta.

### Como executar o aplicativo cliente de monitoramento
1. Faça logon no computador onde você executará o aplicativo cliente. Ele não precisa ser o mesmo computador executando o aplicativo TSPSolver, embora possa ser.
2. Crie uma pasta onde você executará seu aplicativo. Por exemplo, c:\\TSP.
3. Copie TSPClient.exe e Microsoft.ServiceBus.dll, ambos disponíveis na pasta bin do projeto TSPClient, para a pasta c:\\TSP.
4. Em um prompt de comando, altere os diretórios para c:\\TSP.
5. Execute o comando a seguir.

        TSPClient

    Como opção, especifique o número de minutos de suspensão entre a verificação da fila passando um argumento de linha de comando. O período de suspensão padrão para a verificação da fila é de três minutos, que será usado se nenhum argumento de linha de comando for passado para TSPClient. Se você quiser usar um valor diferente para o intervalo de suspensão, como um minuto, por exemplo, execute o comando a seguir.

	    TSPClient 1

    O cliente será executado até ver a mensagem de uma fila "Concluído". Observe que, se executar várias ocorrências do solver sem executar o cliente, você precisará executar o cliente várias vezes para esvaziar completamente a fila. Também é possível excluir a fila e depois criá-la novamente. Para excluir a fila, execute o comando do TSPSolver (não TSPClient) a seguir.

        TSPSolver deletequeue

## Como parar os aplicativos do .NET

Para os aplicativos solver e de cliente, é possível pressionar Ctrl+C para sair se você quiser encerrar antes da conclusão normal.

## Alternativa à criação e à exclusão da fila com TSPSolver
Em vez de usar TSPSolver para criar ou excluir a fila, é possível criá-la ou exclui-la usando o [portal clássico do Azure](https://manage.windowsazure.com). Visite a seção de Barramento de Serviço do portal clássico do Azure a fim de acessar as interfaces de usuário para criar ou excluir uma fila, bem como para recuperar a cadeia de conexão, o emissor e a chave de acesso. Também é possível exibir um painel das suas filas do Barramento de Serviço, permitindo exibir métricas para as mensagens de entrada e de saída.

[solver_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPSolver.png
[client_output]: ./media/virtual-machines-dotnet-run-compute-intensive-task/WA_dotNetTSPClient.png
[create_service_bus]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ServiceBusCreateNew.png
[create_namespace_dialog]: ./media/virtual-machines-dotnet-run-compute-intensive-task/CreateNameSpaceDialog.png
[available_namespaces]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AvailableNamespaces.png
[click_create]: ./media/virtual-machines-dotnet-run-compute-intensive-task/ClickCreate.png
[namespace_list]: ./media/virtual-machines-dotnet-run-compute-intensive-task/NamespaceList.png
[access_key_button]: ./media/virtual-machines-dotnet-run-compute-intensive-task/AccessKey.png

<!---HONumber=AcomDC_1203_2015-->