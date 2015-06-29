<properties
	pageTitle="Tutorial - Introdução à Biblioteca do Lote do Azure para .NET"
	description="Aprenda os conceitos básicos sobre o Azure Batch e a desenvolver para o serviço Batch com um cenário simples"
	services="batch"
	documentationCenter=".net"
	authors="yidingzhou"
	manager="timlt"
	editor=""/>

<tags
	ms.service="batch"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.workload="big-compute"
	ms.date="05/04/2015"
	ms.author="yidingz"/>

#Introdução à Biblioteca do Azure Batch para .NET  

Este artigo contém os dois tutoriais a seguir para ajudá-lo a começar o desenvolvimento com a biblioteca do .NET para o serviço Azure Batch.

-	[Tutorial 1: biblioteca do Azure Batch para .NET](#tutorial1)
-	[Tutorial 2: biblioteca do Azure Batch Apps para .NET](#tutorial2)  


Para obter informações gerais e cenários para uso do Azure Batch, consulte [Visão técnica geral do Azure Batch](batch-technical-overview.md).

##<a name="tutorial1"></a>Tutorial 1: Biblioteca do Azure Batch para .NET

Este tutorial mostra a você como criar um aplicativo de console que configura a computação distribuída entre um pool de máquinas virtuais usando o serviço Azure Batch. As tarefas que são criadas neste tutorial avaliam o texto dos arquivos contidos no armazenamento do Azure e retornam as palavras que são mais comumente usadas. Os exemplos são escritos em código C# e usam a Biblioteca do Azure Batch para .NET.


>[AZURE.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).
>
>Você precisa usar o NuGet para obter o assembly **Microsoft.Azure.Batch.dll**. Depois de criar seu projeto no Visual Studio, clique com botão direito do mouse no projeto no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**. Pesquise online por **Azure.Batch** e, em seguida, clique em Instalar para instalar o pacote Azure Batch e as respectivas dependências.
>
>Certifique-se de que sua versão do Gerenciador de Pacotes do Nuget é a 2.8 ou posterior. Você pode encontrar o número de versão em Visual Studio -> "Ajuda" -> caixa de diálogo "Sobre o Microsoft Visual Studio". Se você tiver uma versão mais antiga do Gerenciador de Pacotes do Nuget, você precisa atualizar o Visual Studio ou talvez tenha problemas para baixar a versão correta das dependências do Nuget.
>
>Além disso, você pode consultar o [exemplo Hello World do Azure Batch](https://code.msdn.microsoft.com/Azure-Batch-Sample-Hello-6573967c) no MSDN para obter um exemplo similar ao código discutido aqui.



###Conceitos
O serviço Batch é usado para o agendamento de computação distribuída e dimensionável. Ele permite executar cargas de trabalho em grande escala, que são distribuídas entre várias máquinas virtuais. Essas cargas de trabalho fornecem suporte eficiente para computação intensiva. Quando usa o serviço Batch, você tira proveito dos seguintes recursos:

-	**Conta** - uma entidade com identificação exclusiva dentro do serviço. Todo o processamento é feito através de uma conta do Batch.
-	**Pool** – uma coleção de máquinas virtuais de tarefa nas quais aplicativos de tarefa são executados.
-	**Máquina virtual de tarefa** - uma máquina que é atribuída a um pool, e é usada pelas tarefas atribuídas a trabalhos executados nesse pool.
-	**Usuário da máquina virtual de tarefa** – uma conta de usuário em uma máquina virtual de tarefa.
-	**Item de trabalho** - especifica como a computação é realizada em máquinas virtuais de tarefa em um pool.
-	**Trabalho** - uma instância em execução de um item de trabalho e que consiste em um conjunto de tarefas.
-	**Tarefa** - um aplicativo que está associado a um trabalho e é executado em uma máquina virtual de tarefa.
-	**Arquivo** – contém as informações que são processadas por uma tarefa.  

Vamos começar com o uso mais básico.

###Criar uma conta do Azure Batch
Você pode usar o Portal de Gerenciamento para criar uma conta do Batch. Uma chave é fornecida a você depois que a conta é criada. Para obter mais informações, consulte [Visão técnica geral do Azure Batch](batch-technical-overview.md).

###Como adicionar um pool a uma conta
Um pool de máquinas virtuais de tarefa é o primeiro conjunto de recursos que você deve criar para executar tarefas.

1.	Abra o Microsoft Visual Studio 2013 e, no menu **Arquivo**, clique em **Novo**, depois clique em **Projeto**.

2.	No **Windows**, em **Visual C#**, clique em **Aplicativo de Console**, nomeie o projeto como **GettingStarted**, nomeie a solução como **AzureBatch** e, em seguida, clique em **OK**.

3.	Adicione as seguintes declarações de namespace ao topo de Program.cs:

		using Microsoft.Azure.Batch;
		using Microsoft.Azure.Batch.Auth;
		using Microsoft.Azure.Batch.Common;

	Certifique-se de fazer referência ao assembly Microsoft.Azure.Batch.dll.

4.	Adicione as seguintes variáveis à classe Program:

		private const string PoolName = "[name-of-pool]";
		private const int NumOfMachines = 3;
		private const string AccountName = "[name-of-batch-account]";
		private const string AccountKey = "[key-of-batch-account]";
		private const string Uri = "https://batch.core.windows.net";
	Substitua os seguintes valores:- **[nome-do-pool]** - o nome que você deseja usar para o pool. - **[nome-de-conta-do-batch]** - o nome da conta do Batch. - **[chave-de-conta-do-batch]** - a chave fornecida a você para a conta do Batch.
5.	Adicione a Main o seguinte código, que define as credenciais a usar:

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
6.	Adicione a Main o seguinte código, para criar o cliente que é usado para executar operações:

		IBatchClient client = BatchClient.Connect(Uri, cred);
7.	Adicione a Main o seguinte código, que cria o pool se ele não existir:

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   IEnumerable<ICloudPool> pools = pm.ListPools();
		   if (!pools.Select(pool => pool.Name).Contains(PoolName))
		   {
		      Console.WriteLine("The pool does not exist, creating now...");
		      ICloudPool newPool = pm.CreatePool(
		         PoolName,
		         osFamily: "3",
		         vmSize: "small",
		         targetDedicated: NumOfMachines);
		       newPool.Commit();
		    }
		}
		Console.WriteLine("Created pool {0}", PoolName);
		Console.ReadLine();
8.	Salve e execute o programa. O status é apresentado como **Ativo** para um pool que foi adicionado com êxito.  

###Como listar os pools em uma conta
Se você não souber o nome de um pool existente, você pode obter uma lista dos pools em uma conta.

1.	Adicione a Main o seguinte código, que define as credenciais a usar:  

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
2.	Adicione a Main o seguinte código, para criar o cliente que é usado para executar operações:

		IBatchClient client = BatchClient.Connect(Uri, cred);

3.	Atualize o procedimento Main para o seguinte código, que grava os nomes e os estados de todos os pools presentes na conta e cria o pool se ele não existir:

		BatchCredentials cred = new BatchCredentials(AccountName, AccountKey);
		IBatchClient client = BatchClient.Connect(Uri, cred);

		using (IPoolManager pm = client.OpenPoolManager())
		{
		    IEnumerable<ICloudPool> pools = pm.ListPools();

		    Console.WriteLine("Listing Pools\n=================");
		    foreach (var p in pools)
		    {
		        Console.WriteLine("Pool: " + p.Name + " State:" + p.State);
		    }  

		    if (!pools.Select(pool => pool.Name).Contains(PoolName))
		    {
		        Console.WriteLine("The pool does not exist, creating now...");
		        ICloudPool newPool = pm.CreatePool(
		           PoolName,
		           osFamily: "3",
		           vmSize: "small",
		           targetDedicated: NumOfMachines);
		        newPool.Commit();
		    }
		}
		Console.WriteLine("Created pool {0}. Press <Enter> to continue.", PoolName);
		Console.ReadLine();

4.	Salve e execute o programa. Você verá a saída a seguir.

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

###Como listar os itens de trabalho em uma conta
Se você não souber o nome de um item de trabalho existente, poderá obter uma lista dos itens de trabalho existentes em uma conta.

1.	Adicione ao final do procedimento Main o código a seguir, que grava os nomes e estados de todos os itens de trabalho na conta:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   Console.WriteLine("Listing Workitems\n=================");
		   IEnumerable<ICloudWorkItem> workitems = wm.ListWorkItems();
		   foreach (var w in workitems)
		   {
		      Console.WriteLine("Workitem: " + w.Name + " State:" + w.State);
		   }
		}
		Console.WriteLine("Press <Enter> to continue.");
		Console.ReadLine();
7.	Salve e execute o programa. Provavelmente você não verá nada, já que nós ainda não enviamos nenhum item de trabalho. Falaremos sobre como adicionar um item de trabalho na próxima seção.  

##Como adicionar um item de trabalho a uma conta
Você deve criar um item de trabalho para definir como as tarefas serão executadas no pool.

1.	Adicione as seguintes variáveis à classe Program:

		private static readonly string WorkItemName = Environment.GetEnvironmentVariable("USERNAME") + DateTime.Now.ToString("yyyyMMdd-HHmmss");

2.	Quando um item de trabalho é criado, um trabalho também é criado. Você pode atribuir um nome ao item de trabalho, mas o trabalho sempre receberá o nome **trabalho-0000000001**. Adicione ao procedimento Main (antes do código para listar itens de trabalho) o código a seguir, que adiciona o item de trabalho:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   ICloudWorkItem cloudWorkItem = wm.CreateWorkItem(WorkItemName);
           cloudWorkItem.JobExecutionEnvironment = new JobExecutionEnvironment() {PoolName = PoolName};
		   cloudWorkItem.Commit();
		}
		Console.WriteLine("Workitem successfully added. Press <Enter> to continue.");
		Console.ReadLine();
3.	Salve e execute o programa. O status fica **Ativo** para qualquer item de trabalho adicionado com êxito. Você deve ver a saída a seguir.

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

		Workitem successfully added. Press <Enter> to continue.

		Listing Workitems
		=================
		Workitem: yidingz20141106-111211 State:Active
		Press <Enter> to continue.

###Como adicionar tarefas a um trabalho
Um item de trabalho sem tarefa não fará nada. Depois de você criar o item de trabalho e o trabalho ter sido criado, você pode adicionar tarefas a esse trabalho. Vamos adicionar uma tarefa simples ao trabalho.

1.	Adicione as seguintes variáveis à classe Program:

		private const int Count = 4; // number of tasks that will run
		private const string JobName = "job-0000000001";

2.	Adicione ao procedimento Main o seguinte código, que adiciona tarefas a um trabalho, aguarda a execução dessas tarefas e informa os resultados:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		    string taskName;
		    ICloudJob job = wm.GetJob(WorkItemName, JobName);
		    for (int i = 1; i <= Count; ++i)
		    {
		        taskName = "taskdata" + i;
		        string commandLine = String.Format("cmd /c echo I am {0}", taskName);
		        ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		        job.AddTask(taskToAdd);
		        job.Commit();
		        job.Refresh();
		    }

		    ICloudJob listjob = wm.GetJob(WorkItemName, JobName);
		    client.OpenToolbox().CreateTaskStateMonitor().WaitAll(listjob.ListTasks(),
		       TaskState.Completed, new TimeSpan(0, 30, 0));
		    Console.WriteLine("The tasks completed successfully. Terminating the workitem...");
		    wm.GetWorkItem(WorkItemName).Terminate();
		    foreach (ICloudTask task in listjob.ListTasks())
		    {
		        Console.WriteLine("Task " + task.Name + " says:\n" + task.GetTaskFile(Constants.StandardOutFileName).ReadAsString());
		    }
		    Console.ReadLine();
		}

3.	Salve e execute o programa. O resultado deve ter essa aparência:

		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		I am taskdata1

		Task taskdata2 says:
		I am taskdata2

		Task taskdata3 says:
		I am taskdata3

###Criar um programa de processamento de tarefas
Agora que podemos executar "olá mundo" na VM, vamos fazer alguma coisa real. Criaremos um programa de processamento de tarefas nesta seção e o carregaremos na máquina virtual de tarefa, responsável pela execução de tarefas.

1.	No Gerenciador de Soluções, crie um novo projeto de aplicativo de console chamado **ProcessTaskData** na solução **AzureBatch**.

2.	Adicione as seguintes declarações de namespace ao topo de Program.cs:

		using System.Net;

3.	Adicione o seguinte código ao procedimento Main que será usado para processar dados:

		string blobName = args[0];
		int numTopN = int.Parse(args[1]);

		WebClient myWebClient = new WebClient();
		string content = myWebClient.DownloadString(blobName);

		string[] words = content.Split(' ');
		var topNWords =
		   words.
		   Where(word => word.Length > 0).
		   GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
		   OrderByDescending(x => x.Value).
		   Take(numTopN).
		   ToList();

		foreach (var pair in topNWords)
		{
		    Console.WriteLine("{0} {1}", pair.Key, pair.Value);
		}

4.	Salve e compile o projeto.

###Preparar recursos para execução de tarefa

Você usará o seguinte fluxo de trabalho básico ao criar um cenário computacional distribuído com o serviço Batch:

1.	Carregue em uma conta de armazenamento do Azure os arquivos que você deseja usar no cenário computacional distribuído. Esses arquivos devem estar na conta de armazenamento para que o serviço Batch possa acessá-los. Os arquivos são carregados em uma máquina virtual de tarefa quando a tarefa é executada.
2.	Carregue na conta de armazenamento os arquivos binários dependentes. Os arquivos binários incluem o programa executado pela tarefa e os assemblies dependentes. Esses arquivos também devem ser acessados por meio do armazenamento e são carregados na máquina virtual de tarefa.
3.	Crie um pool de máquinas virtuais de tarefa. Você pode, quando o pool é criado, atribuir o tamanho da máquina virtual de tarefa a utilizar. Quando uma tarefa é executada, ela é atribuída a uma máquina virtual desse pool.
4.	Crie um item de trabalho. Um item de trabalho permite que você gerencie um trabalho de tarefas. Um trabalho é criado automaticamente quando você cria um item de trabalho.
5.	Adicione tarefas ao trabalho. Cada tarefa usa o programa que você carregou para processar informações de um arquivo que você carregou.
6.	Monitore os resultados da saída.  

Exibimos as etapas n.º 3 a 6. Vejamos como preparar o armazenamento do Azure para executar a tarefa.

####Obtendo a conta de armazenamento
Você precisará de uma conta de armazenamento para continuar a completar o restante deste tutorial. Se você não souber como fazer isso, consulte [Criar uma conta de armazenamento do Azure](#tutorial1_storage).

####Carregando dados

1. Crie um contêiner chamado "introdução" na conta de armazenamento do Azure. Isso pode ser feito no Portal do Azure. Certifique-se de definir o campo "ACESSO" como "Contêiner público".

2. Carregue o arquivo "ProcessTaskData.exe" no contêiner.

3. Crie três arquivos de texto (taskdata1.txt, taskdata2.txt, taskdata3.txt), com cada um deles contendo um dos parágrafos a seguir, então carregue-os no contêiner:

		You can use Azure Virtual Machines to provision on-demand, scalable compute infrastructure when you need flexible resources for your business needs. From the gallery, you can create virtual machines that run Windows, Linux, and enterprise applications such as SharePoint and SQL Server. Or, you can capture and use your own images to create customized virtual machines.

		Quickly deploy and manage powerful applications and services with Azure Cloud Services. Simply upload your application and Azure handles the deployment details - from provisioning and load balancing to health monitoring for continuous availability. Your application is backed by an industry leading 99.95% monthly SLA. You just focus on the application and not the infrastructure.

		Azure Web Sites provide a scalable, reliable, and easy-to-use environment for hosting web applications. Select from a range of frameworks and templates to create a web site in seconds. Use any tool or OS to develop your site with .NET, PHP, Node.js or Python. Choose from a variety of source control options including TFS, GitHub, and BitBucket to set up continuous integration and develop as a team. Expand your site functionality over time by leveraging additional Azure managed services like storage, CDN, and SQL Database.


>[AZURE.NOTE]Em um ambiente de produção, é recomendável que você use uma assinatura de acesso compartilhado.


>[AZURE.NOTE]A equipe de Armazenamento do Azure tem uma [postagem de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx) que lista os gerenciadores de Armazenamento do Azure que podem ajudar no carregamento de arquivos.



###Atualizar o código de envio de tarefa

1.	Adicione as seguintes variáveis à classe Program:

		private const string BlobPath = "[storage-path]";
	Substitua os seguintes valores: - **[caminho-de-armazenamento]** - o caminho para o blob no armazenamento. Por exemplo: http://yiding.blob.core.windows.net/gettingstarted/

2. Atualize o código de envio de tarefa como demonstrado a seguir.

		string taskName;
		ICloudJob job = wm.GetJob(WorkItemName, JobName);
		for (int i = 1; i <= Count; ++i)
		{
		    taskName = "taskdata" + i;
		    IResourceFile programFile = new ResourceFile(BlobPath + "ProcessTaskData.exe", "ProcessTaskData.exe");
		    IResourceFile supportFile = new ResourceFile(BlobPath + taskName + ".txt", taskName);
		    string commandLine = String.Format("ProcessTaskData.exe {0} {1}", BlobPath + taskName + ".txt", 3);
		    ICloudTask taskToAdd = new CloudTask(taskName, commandLine);
		    taskToAdd.ResourceFiles = new List<IResourceFile>();
		    taskToAdd.ResourceFiles.Add(programFile);
		    taskToAdd.ResourceFiles.Add(supportFile);
		    job.AddTask(taskToAdd);
		}
		job.Commit();

3. Salve e execute o programa. Você deverá ver:

		Listing Pools
		=================
		Pool: gettingstarted State:Active
		Created pool gettingstarted. Press <Enter> to continue.

		Workitem successfully added. Press <Enter> to continue.

		Listing Workitems
		=================
		Workitem: yidingz20141106-132140 State:Active
		Press <Enter> to continue.

		The tasks completed successfully. Terminating the workitem...
		Task taskdata1 says:
		can 3
		you 3
		and 3

		Task taskdata2 says:
		and 5
		application 3
		the 3

		Task taskdata3 says:
		a 5
		and 5
		to 3

###Como excluir o pool e o item de trabalho
Após o processamento do item de trabalho, você pode liberar recursos, excluindo o pool e o item de trabalho.

####Excluir o pool
1.	Adicione ao final do procedimento Main o seguinte código, que exclui o pool:

		using (IPoolManager pm = client.OpenPoolManager())
		{
		   pm.DeletePool(PoolName);
		}
		Console.WriteLine("Pool successfully deleted.");
		Console.ReadLine();
2.	Salve e execute o programa.


####Excluir o item de trabalho
1.	Adicione ao final do procedimento Main o seguinte código, que exclui o item de trabalho:

		using (IWorkItemManager wm = client.OpenWorkItemManager())
		{
		   wm.DeleteWorkItem(WorkItemName);
		}
		Console.WriteLine("Workitem successfully deleted.");
		Console.ReadLine();
2.	Salve e execute o programa.

###<a name="tutorial1_storage"></a>APÊNDICE: criar uma conta de Armazenamento do Azure
Antes de poder executar o código apresentado neste tutorial, você deve ter acesso a uma conta de armazenamento no Azure.

1.	Faça logon no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/).
2.	Na parte inferior do painel de navegação, clique em **NOVO**. ![][1]
3.	Clique em **SERVIÇOS DE DADOS**, **ARMAZENAMENTO** e em **CRIAÇÃO RÁPIDA**. ![][2]

4.	Em **URL**, digite um nome de subdomínio a ser usado no URI para a conta de armazenamento. A entrada pode conter de 3 a 24 letras minúsculas e números. Esse valor torna-se o nome de host no URI que é usado para lidar com os recursos de Blob, Fila ou Tabela da assinatura em questão.
5.	Escolha um **GRUPO DE AFINIDADE/REGIÃO** no qual deseja localizar o armazenamento.
6.	Opcionalmente, você pode habilitar a replicação geográfica.
7.	Clique em **CRIAR CONTA DE ARMAZENAMENTO**.  

Para obter mais informações sobre o armazenamento do Azure, consulte [Como usar o serviço de armazenamento de Blob do Azure no .NET](http://azure.microsoft.com/develop/net/how-to-guides/blob-storage/).


##<a name="tutorial2"></a>Tutorial 2: Biblioteca do Azure Batch Apps para .NET
Este tutorial mostra como executar cargas de trabalho de computação paralela no Azure Batch usando o serviço Batch Apps.

Batch Apps é um recurso do Azure Batch que oferece um modo, centrado em aplicativos, de gerenciar e executar cargas de trabalho em Lotes. Usando o SDK do Batch Apps, você pode criar pacotes para habilitar um aplicativo para uso em Lote, além de implantá-los em sua própria conta ou torná-los disponíveis para outros usuários do Batch. O Batch também fornece gerenciamento de dados, monitoramento de trabalhos, diagnóstico e registro em log internos e suporte para dependências entre as tarefas. Além disso, ele inclui um portal de gerenciamento no qual você pode gerenciar trabalhos, exibir logs e baixar saídas sem precisar escrever seu próprio cliente.

No cenário do Batch Apps, você escreve código usando o SDK de Nuvem do Batch Apps para dividir os trabalhos em tarefas paralelas, descrever quaisquer eventuais dependências entre essas tarefas e especificar como cada tarefa deve ser executada. Esse código é implantado na conta do Batch. Os clientes podem, em seguida, executar trabalhos especificando o tipo de trabalho e os arquivos de entrada para uma API REST.

>[AZURE.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/). Você pode usar o NuGet para obter ambos o assembly de <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps.Cloud/">Nuvem do Batch Apps</a> e o assembly de <a href="http://www.nuget.org/packages/Microsoft.Azure.Batch.Apps/">Cliente do Batch Apps</a>. Depois de criar seu projeto no Visual Studio, clique com botão direito do mouse no projeto no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**. Você também pode baixar a extensão do Visual Studio para Batch Apps, que inclui um modelo de projeto para aplicativos de habilitação de nuvem e a capacidade de implantar um aplicativo <a href="https://visualstudiogallery.msdn.microsoft.com/8b294850-a0a5-43b0-acde-57a07f17826a">aqui</a> ou pesquisando por **Batch Apps** no Visual Studio, pelo item de menu Extensões e Atualizações. Você também pode encontrar <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">exemplos de ponta a ponta no MSDN.</a>
>

###Princípios básicos do Azure Batch Apps
O Batch é projetado para funcionar com aplicativos de computação intensiva existentes. Ele utiliza o código do aplicativo existente e executa-o em um ambiente dinâmico, virtualizado e para uso geral. Para habilitar um aplicativo para trabalhar com o Batch Apps, há algumas coisas que precisam ser feitas:

1.	Prepare um arquivo zip com os executáveis do seu aplicativo existente – os mesmos que seriam executados em um cluster ou farm de servidores tradicional – e quaisquer arquivos de suporte requeridos por esse aplicativo. Este arquivo zip é então carregado à sua conta do Batch usando o portal de gerenciamento ou a API REST.
2.	Crie um arquivo zip com os "assemblies de nuvem" que enviam suas cargas de trabalho para o aplicativo e carregue esse zip por meio do portal de gerenciamento ou da API REST. Um assembly de nuvem contém dois componentes que são compilados com o SDK de nuvem:
	1.	Divisor de Trabalho – que divide o trabalho em tarefas que podem ser processadas independentemente. Por exemplo, em um cenário de animação, o divisor de trabalho abriria um trabalho de filme e dividiria-o em quadros individuais.
	2.	Processador de Tarefa – que chama o executável do aplicativo para uma determinada tarefa. Por exemplo, em um cenário de animação, o processador de tarefa invocaria um programa de renderização para renderizar o único quadro especificado pela tarefa em questão.
3.	Fornecem uma maneira para enviar trabalhos ao aplicativo habilitado no Azure. Isso pode ser um plug-in no aplicativo da interface do usuário ou um portal da Web, ou até mesmo um serviço autônomo como parte do pipeline de execução. Consulte os <a href="https://go.microsoft.com/fwLink/?LinkID=512183&clcid=0x409">exemplos</a> no MSDN para obter exemplos.



###Conceitos principais do Batch Apps
O modelo de uso e programação do Batch Apps envolve os conceitos principais a seguir:

####Trabalhos
Um **trabalho** é uma unidade de trabalho enviada pelo usuário. Quando um trabalho é enviado, o usuário especifica o tipo de trabalho, quaisquer eventuais configurações para esse trabalho e os dados necessários para ele. A implementação habilitada pode resolver esses detalhes no lugar do usuário em alguns casos ou então, em outros casos, o usuário pode fornecer essas informações explicitamente por meio do cliente. Um trabalho tem resultados, que são retornados. Cada trabalho tem uma saída primária e, opcionalmente, uma saída de visualização. Trabalhos também podem retornar saídas extras se desejado.

####Tarefas
Uma **tarefa** é uma unidade de trabalho a ser executada como parte de um trabalho. Quando um usuário envia um trabalho, ele é dividido em tarefas de menor porte. O serviço então processa essas tarefas individuais e compila os resultados das tarefas em uma saída geral do trabalho. A natureza das tarefas depende do tipo de trabalho. O Divisor de Trabalho define como um trabalho é dividido em tarefas, orientado pelo conhecimento de quais partes do trabalho o aplicativo foi projetado para processar. Saídas de tarefas também podem ser baixadas individualmente e pode ser úteis em alguns casos, por exemplo, quando um usuário deseja baixar tarefas individuais provenientes de um trabalho de animação.

####Tarefas de mesclagem
Uma **tarefa de mesclagem** é um tipo especial de tarefa, que reúne os resultados das tarefas individuais nos resultados do trabalho final. Para um trabalho de renderização de filme, a tarefa de mesclagem pode montar os quadros renderizados em um filme ou zipar todos os quadros renderizados em um único arquivo. Todo trabalho inclui uma tarefa de mesclagem, mesmo que nenhuma "mesclagem" propriamente dita seja necessária.

####Arquivos
Um **arquivo** é uma unidade de dados usados como entrada para um trabalho. Um trabalho pode não ter nenhum arquivo de entrada associado a ele; pode também ter um, ou muitos. O mesmo arquivo pode ser usado em vários trabalhos, por exemplo, para um trabalho de renderização de filme, os arquivos podem ser texturas, modelos etc. Para um trabalho de análise de dados, os arquivos podem ser um conjunto de observações ou de medidas.

###Habilitando o aplicativo em nuvem
Seu aplicativo deve conter uma propriedade ou campo estático, com todos os detalhes desse aplicativo. Essa propriedade ou campo especifica o nome do aplicativo e o tipo de trabalho ou tipos de trabalho realizados pelo aplicativo. Isso é fornecido ao usar o modelo no SDK, que pode ser baixado por meio da Galeria do Visual Studio.

Aqui está um exemplo de uma declaração de aplicativos de nuvem para uma carga de trabalho paralela:

	public static class ApplicationDefinition
	{
	    public static readonly CloudApplication App = new ParallelCloudApplication
	    {
	        ApplicationName = "ApplicationName",
	        JobType = "ApplicationJobType",
	        JobSplitterType = typeof(MyJobSplitter),
	        TaskProcessorType = typeof(MyTaskProcessor),
	    };
	}

####Implementando o Divisor de Trabalho e o Processador de Tarefa
Para cargas de trabalho excessivamente paralelas, você deve implementar um divisor de trabalho e um processador de tarefa.

####Implementando JobSplitter.SplitIntoTasks
Sua implementação do SplitIntoTasks deve retornar como resultado uma sequência de tarefas. Cada tarefa representa um trabalho separado, que será colocado em fila para processamento por um nó de computação. Cada tarefa deverá ser independente e deverá ser configurada com todas as informações de que o processador de tarefa precisará.

As tarefas especificadas com o divisor de trabalho são representadas como objetos TaskSpecifier. O TaskSpecifier tem várias propriedades que podem ser definidas antes do retorno da tarefa.


-	O TaskIndex é ignorado, mas está disponível para processadores de tarefa. Você pode usá-lo para passar um índice ao processador de tarefa. Se você não precisar passar um índice, você não precisará definir essa propriedade.
-	A Parameters é uma coleção vazia, por padrão. Você pode adicionar itens a ela substituí-la por uma nova coleção. Você pode copiar entradas da coleção de parâmetros do trabalho usando um dos métodos WithJobParameters ou WithAllJobParameters.  


A RequiredFiles é uma coleção vazia por padrão. Você pode adicionar itens a ela substituí-la por uma nova coleção. Você pode copiar entradas da coleção de arquivos de trabalho usando um dos métodos RequiringJobFiles ou RequiringAllJobFiles.

Você pode especificar uma tarefa que depende de uma outra tarefa específica. Para fazer isso, defina a propriedade TaskSpecifier.DependsOn, passando a ID da tarefa da qual ela depende:

	new TaskSpecifier {
	    DependsOn = TaskDependency.OnId(5)
	}

A tarefa não será executada até que a saída da tarefa da qual ela depende esteja disponível.

Você também pode especificar que todo um grupo de tarefas não inicie o processamento até que outro grupo tenha sido totalmente concluído. Nesse caso, você pode definir a propriedade TaskSpecifier.Stage. Tarefas com um determinado valor de Stage não começarão a ser processadas até que todas as tarefas com valores mais baixos de Stage tiverem sido concluídas; por exemplo, tarefas com Stage 3 não começarão seu processamento até que todas as tarefas com Stage 0, 1 ou 2 tenham terminado. As propriedades Stage precisam começar por 0 e a sequência de estágios não pode apresentar lacunas; a propriedade SplitIntoTasks deve retornar as tarefas segundo sua ordem de estágios: por exemplo, é um erro retornar uma tarefa com Stage 0 após uma tarefa com a Stage 1.

Todos os trabalhos paralelos terminam com uma tarefa especial chamada tarefa de mesclagem. A função da tarefa de mesclagem é combinar os resultados das tarefas de processamento paralelo em um resultado final. O Batch Apps cria automaticamente a tarefa de mesclagem para você.

Em casos raros, você talvez queira controlar explicitamente a tarefa de mesclagem, por exemplo, para personalizar os parâmetros dessa tarefa. Nesse caso, você pode especificar a tarefa de mesclagem, retornando um TaskSpecifier com sua propriedade IsMerge definida como true. Isso fará com que a tarefa de mesclagem automática seja substituída. Se você criar uma tarefa de mesclagem explícita:

-	Você pode criar apenas uma tarefa de mesclagem explícita
-	Ele deve ser a última tarefa na sequência
-	Ele deve ter IsMerge definida como true e todas as outras tarefas devem ter IsMerge definida como false  


No entanto, lembre-se de que normalmente você não precisa criar a tarefa de mesclagem explicitamente.

O código a seguir demonstra uma implementação simples de SplitIntoTasks.

	protected override IEnumerable<TaskSpecifier> SplitIntoTasks(
	    IJob job,
	    JobSplitSettings settings)
	{
	    int start = Int32.Parse(job.Parameters["startIndex"]);
	    int end = Int32.Parse(job.Parameters["endIndex"]);
	    int count = (end - start) + 1;

	    // Processing tasks
	    for (int i = 0; i < count; ++i)
	    {
	        yield return new TaskSpecifier
	        {
	            TaskIndex = start + i,
	        }.RequiringAllJobFiles();
	    }
	}
####Implementando ParallelTaskProcessor.RunExternalTaskProcess

A RunExternalTaskProcess é chamada para cada tarefa de mesclagem não retornada do divisor de trabalho. Ela deve invocar o aplicativo com os argumentos apropriados e retornar uma coleção de saídas, que precisam ser mantidas para uso posterior.

O fragmento a seguir mostra como chamar um programa nomeado como application.exe. Observe que você pode usar o método auxiliar ExecutablePath para criar caminhos de arquivo absolutos.

A classe ExternalProcess do SDK de nuvem fornece lógica auxiliar útil para a execução dos executáveis de seu aplicativo. A ExternalProcess pode se encarregar do cancelamento, traduzindo códigos de saída em exceções, capturando saídas/erros padrão e configurando variáveis de ambiente. Além disso, você pode usar a classe .NET Process diretamente para executar seus programas, se preferir.

O método RunExternalTaskProcess retorna um TaskProcessResult, que inclui uma lista de arquivos de saída. Isso deve incluir pelo menos todos os arquivos necessários para a mesclagem; você também pode, opcionalmente, retornar arquivos de log, arquivos de visualização e arquivos intermediários (por exemplo, para fins de diagnóstico em caso de falha na tarefa). Observe que seu método retorna os caminhos de arquivo, não o conteúdo do arquivo.

Cada arquivo deve ser identificado com o tipo de saída que contém: saída (ou seja, parte da eventual saída do trabalho), visualização, log ou intermediária. Esses valores são originados da enumeração TaskOutputFileKind. O fragmento a seguir retorna uma única saída de tarefa e nenhuma visualização ou log. O método TaskProcessResult.FromExternalProcessResult simplifica o cenário comum de capturar o código de saída, a saída do processador e arquivos de saída de um programa de linha de comando:

O código a seguir demonstra uma implementação simples de ParallelTaskProcessor.RunExternalTaskProcess.

	protected override TaskProcessResult RunExternalTaskProcess(
	    ITask task,
	    TaskExecutionSettings settings)
	{
	    var inputFile = LocalPath(task.RequiredFiles[0].Name);
	    var outputFile = LocalPath(task.TaskId.ToString() + ".jpg");

	    var exePath = ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("-in:{0} -out:{1}", inputFile, outputFile);

	    var result = new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath,
	        CancellationToken = settings.CancellationToken
	    }.Run();

	    return TaskProcessResult.FromExternalProcessResult(result, outputFile);
	}
####Implementando ParallelTaskProcessor.RunExternalMergeProcess

Esse método é chamado para a tarefa de mesclagem. Ele deve invocar o aplicativo para combinar os resultados das tarefas anteriores de modo apropriado para seu aplicativo, além de retornar a saída combinada.

A implementação de RunExternalMergeProcess é muito semelhante à de RunExternalTaskProcess, exceto por:

-	RunExternalMergeProcess consumir as saídas de tarefas anteriores, em vez de arquivos de entrada do usuário. Você deve, portanto, descobrir os nomes dos arquivos que deseja processar com base na ID de tarefa, em vez de usar como referência a coleção Task.RequiredFiles.
-	RunExternalMergeProcess retorna um arquivo JobOutput e, opcionalmente, um arquivo JobPreview.


O código a seguir demonstra uma implementação simples de ParallelTaskProcessor.RunExternalMergeProcess.

	protected override JobResult RunExternalMergeProcess(
	    ITask mergeTask,
	    TaskExecutionSettings settings)
	{
	    var outputFile =  "output.zip";

	    var exePath =  ExecutablePath(@"application\application.exe");
	    var arguments = String.Format("a -application {0} *.jpg", outputFile);

	    new ExternalProcess {
	        CommandPath = exePath,
	        Arguments = arguments,
	        WorkingDirectory = LocalStoragePath
	    }.Run();

	    return new JobResult {
	        OutputFile = outputFile
	    };
	}

###Enviando trabalhos para o Batch Apps
Um trabalho descreve uma carga de trabalho a ser executada e precisa incluir todas as informações sobre a carga de trabalho, exceto o conteúdo do arquivo. Por exemplo, o trabalho contém definições de configuração que partem do cliente, passam pelo divisor de trabalho e chegam por fim às tarefas. Os exemplos fornecidos no MSDN são exemplos de como enviar trabalhos e fornecer vários clientes, inclusive um portal da Web e um cliente de linha de comando.


<!--Anchors-->


<!--Image references-->
[1]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-01.jpg
[2]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-02.jpg
[3]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-03.jpg
[4]: ./media/batch-dotnet-get-started/batch-dotnet-get-started-04.jpg

<!---HONumber=58_postMigration-->