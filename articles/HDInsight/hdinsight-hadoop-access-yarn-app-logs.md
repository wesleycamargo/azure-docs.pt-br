<properties 
	pageTitle="Acessar logs de aplicativos YARN no HDInsight programaticamente | Microsoft Azure" 
	description="Acessar os Logs de aplicativo do HDInsight programaticamente." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# Acessar logs de aplicativo YARN no HDInsight programaticamente

Este tópico explica como enumerar programaticamente os aplicativos YARN (Yet Another Resource Negotiator, ou “mais um negociador de recursos”) que acabaram em um cluster Hadoop no Azure HDInsight e como acessar programaticamente os logs dos aplicativos sem precisar se conectar aos clusters, usando o protocolo RDP. Especificamente, foram adicionados um novo componente e uma nova API:

  1. O servidor de histórico de aplicativo genérico nos clusters HDInsight está habilitado. Ele é um componente dentro do Servidor de Linha do Tempo de YARN que manipula o armazenamento e recuperação de informações genéricas de aplicativos concluídos.
  2. Há APIs disponíveis no Azure HDInsight .NET SDK para enumerar programaticamente os aplicativos que foram executados em seus clusters e baixar os logs relevantes específicos de aplicativo ou de contêiner (em texto sem formatação) para ajudar na depuração de problemas de aplicativo que ocorrerem.


## Pré-requisitos

É necessário ter o Azure HDInsight SDK para usar o código apresentado neste tópico em um aplicativo .NET Framework. A compilação mais recente publicada do SDK está disponível no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started).

Para instalar o HDInsight SDK por meio de um aplicativo do Visual Studio, acesse o menu **Ferramentas**, clique em **Gerenciador de Pacotes NuGet** e clique em **Console do Gerenciador de Pacotes**. Execute o seguinte comando no console para instalar os pacotes:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

Este comando adiciona bibliotecas .NET para HDInsight e adiciona referências a elas no projeto atual do Visual Studio.


## <a name="YARNTimelineServer"></a>YARN Timeline Server

O <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN Timeline Server</a> fornece informações genéricas sobre aplicativos concluídos, bem como informações de aplicativo específicas da estrutura, por meio de duas interfaces diferentes. Especificamente:

* O armazenamento e a recuperação de informações do aplicativo genérico em clusters HDInsight estão habilitados na versão 3.1.1.374 ou superior. 
* O componente de informações específicas do framework de aplicativo do servidor do cronograma não está atualmente disponível em clusters HDInsight.


As informações genéricas sobre aplicativos incluem os seguintes tipos de dados:

* A ID do aplicativo, um identificador exclusivo de um aplicativo 
* O usuário que iniciou o aplicativo 
* Informações sobre as tentativas feitas para concluir o aplicativo 
* Os contêineres usados por uma determinada tentativa de aplicativo 

Nos seus clusters HDInsight, essas informações serão armazenadas pelo Gerenciador de Recursos do Azure em um repositório de histórico no contêiner padrão de sua conta do Armazenamento do Azure padrão. Esses dados genéricos em aplicativos concluídos podem ser recuperados por meio de uma API REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps

Nós adicionamos novas APIs ao HDInsight .NET SDK para facilitar a recuperação desses dados programaticamente. Observe que os dados genéricos também podem ser recuperados pela execução de comandos CLI (interface de linha de comando) YARN diretamente nos nós de cluster (após conexão ao cluster usando RDP).

## <a name="YARNAppsAndLogs"></a>Logs e aplicativos YARN

O YARN dá suporte a vários modelos de programação (inclusive MapReduce), por separar o gerenciamento de recursos do agendamento/monitoramento de aplicativos. Isso é feito por meio de um RM (*Gerenciador de Recursos*) global, NMs (*Gerenciadores de Nós*) por nó de trabalho e AMs (*Mestres de Aplicativos*) por aplicativo. O aplicativo AM negocia recursos (CPU, memória, disco e rede) para executar o aplicativo com o Gerenciador de recurso. O RM atua junto com os NMs para conceder esses recursos na forma de *contêineres*. O AM é responsável por controlar o andamento dos contêineres atribuídos pelo RM. Um aplicativo pode exigir um número de contêineres dependendo da natureza do aplicativo.

Além disso, cada aplicativo pode consistir em várias *tentativas (de encerramento) de aplicativo* na presença de panes ou devido a perda de comunicação entre um AM e um RM. Portanto, contêineres são concedidos a uma tentativa específica de um aplicativo. De certa forma, um contêiner fornece o contexto da unidade básica de trabalho executada por um aplicativo YARN; todo o trabalho feito no contexto de um contêiner é executado no nó de trabalho único no qual o contêiner foi alocado. Consulte [Conceitos de YARN][YARN-concepts] para referência adicional.

Os logs de aplicativos (e os logs de contêiner associado) são essenciais na depuração de aplicativos problemáticos do Hadoop. O YARN fornece uma ótima estrutura para coletar, agregar e armazenar logs de aplicativos com o recurso [Agregação de Logs][log-aggregation]. O recurso Agregação de Logs torna o acesso aos logs de aplicativos mais determinista, uma vez que agrega os logs de todos os contêineres em um nó de trabalho e os armazena como um arquivo de log agregado por nó de trabalho no sistema de arquivos padrão após o encerramento de um aplicativo. O aplicativo deve usar centenas ou milhares de contêineres, mas logs para todos os contêineres executados em um nó único de trabalhado sempre serão agregados em um único arquivo, resultando em um arquivo de log por nó de trabalho usado pelo seu aplicativo. A Agregação de Logs está habilitada por padrão nos clusters HDInsight (versão 3.0 ou superior) e os logs agregados se encontram no contêiner padrão de seu cluster no seguinte local:

	wasb:///app-logs/<user>/logs/<applicationId>

Nesse local, *user* é o nome do usuário que iniciou o aplicativo e *applicationId* é o identificador exclusivo de um aplicativo, conforme atribuído pelo RM do YARN.

Os logs agregados não são diretamente legíveis, já que são gravados em um [TFile][T-file], [formato binário][binary-format] indexado pelo contêiner. O YARN fornece ferramentas CLI para despejar os logs como texto sem formatação para aplicativos ou contêineres de interesse. Você pode exibir esses logs como texto sem formatação, executando um dos seguintes comandos de YARN diretamente em nós de cluster (depois de se conectar a ele via RDP):

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address> 

A próxima seção fala sobre como acessar logs específicos de aplicativo ou de contêiner programaticamente, sem precisar usar RDP para se conectar a seus clusters HDInsight.

## <a name="enumerate-and-download"></a>Enumerando aplicativos e baixando os logs programaticamente

Para usar os exemplos de código a seguir, você deve atender aos pré-requisitos descritos acima, baixando a versão mais recente do HDInsight .NET SDK. Consulte as instruções fornecidas.

O código a seguir ilustra como usar as novas APIs para enumerar os aplicativos e baixar os logs de aplicativos concluídos.

> [AZURE.NOTE]As APIs a seguir funcionarão somente com clusters Hadoop versão 3.1.1.374 ou superior "Em execução". Adicione as seguintes diretivas:

	using Microsoft.Hadoop.Client;
	using Microsoft.WindowsAzure.Management.HDInsight;

Essas referenciam as APIs recentemente definidas no código a seguir. O trecho de código a seguir cria um cliente de Histórico de Aplicativos em um cluster "Em execução" em sua assinatura:

	string subscriptionId = "<your-subscription-id>";
	string clusterName = "<your-cluster-name>";
	string certName = "<your-subscription-management-cert-name>";
	
	// Create an HDInsight client
	X509Store store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
	store.Open(OpenFlags.ReadOnly);
	X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>()
	                            .Single(x => x.FriendlyName == certName);
	
	HDInsightCertificateCredential creds = 
				new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
	
	IHDInsightClient client = HDInsightClient.Connect(creds);
	
	// Get the cluster on which your applications were run
	// The cluster needs to be in the "Running" state
	ClusterDetails cluster = client.GetCluster(clusterName);
	
	// Create an Application History client against your cluster
	IHDInsightApplicationHistoryClient appHistoryClient = 
				cluster.CreateHDInsightApplicationHistoryClient(TimeSpan.FromMinutes(5));


Agora você pode usar o cliente de Histórico de Aplicativos para listar os aplicativos concluídos, filtrar os aplicativos por seus critérios e baixar os logs de aplicativo relevante. O trecho de código a seguir mostra como isso é feito programaticamente:

	// Local download folder location where the logs will be placed
	string downloadLocation = "E:\\YarnApplicationLogs";
	
	// List completed applications on your cluster that were submitted in the last 24 hours but failed
	// Search for applications based on application name
	string appNamePrefix = "your-app-name-prefix";
	DateTime endTime = DateTime.UtcNow;
	DateTime startTime = endTime.AddHours(-24);
	IEnumerable<ApplicationDetails> applications = appHistoryClient
	                .ListCompletedApplications(startTime, endTime)
	                .Where(app => 
	                    app.GetApplicationFinalStatusAsEnum() == ApplicationFinalStatus.Failed 
	                    && app.Name.StartsWith(appNamePrefix));
	
	// Download logs for failed or killed applications
	// This will generate one log file for each application
	foreach (ApplicationDetails application in applications)
	{
	    appHistoryClient.DownloadApplicationLogs(application, downloadLocation);
	}

O código acima lista/localiza aplicativos de interesse usando o cliente de Histórico de Aplicativos e baixa os logs desses aplicativos para uma pasta local.

Como alternativa, o trecho de código a seguir baixa os logs de um aplicativo cuja ID é conhecida. A ID do aplicativo é um identificador global exclusivo de um aplicativo atribuído pelo RM. Ela é formada pela hora de início do RM, acrescida de uma contagem crescente monotônica dos aplicativos que foram enviados para ele. A ID do aplicativo está no formato "aplicativo_ <Hora-de-início-do-RM>_<Contador>". Observe que a ID do aplicativo e a ID do trabalho são diferentes. A ID do trabalho é um conceito específico da estrutura do MapReduce, enquanto que a ID do aplicativo é um conceito YARN independente de estrutura. No YARN, uma ID de trabalho identifica um trabalho de MapReduce específico, conforme manipulado pelo AM de um aplicativo MapReduce enviado ao RM.

	// Download application logs for an application whose application ID is known
	string applicationId = "application_1416017767088_0028";
	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	appHistoryClient.DownloadApplicationLogs(someApplication, downloadLocation);

Se necessário, você também pode baixar os logs de cada contêiner (ou de um contêiner específico) usados por um aplicativo, como mostrado abaixo.

	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	
	// Download logs separately for each container of application(s) of interest
	// This will generate one log file per container
	IEnumerable<ApplicationAttemptDetails> applicationAttempts =
				appHistoryClient.ListApplicationAttempts(someApplication);
	
	ApplicationAttemptDetails finalAttempt = applicationAttempts
	    		.Single(x => x.ApplicationAttemptId == someApplication.LatestApplicationAttemptId);
	
	IEnumerable<ApplicationContainerDetails> containers =
				appHistoryClient.ListApplicationContainers(finalAttempt);
	
	foreach (ApplicationContainerDetails container in containers)
	{
	    appHistoryClient.DownloadApplicationLogs(container, downloadLocation);
	}
[YARN-timeline-server]: http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]: http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]: https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]: https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]: http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

<!--HONumber=54--> 