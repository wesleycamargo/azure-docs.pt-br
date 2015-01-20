<properties linkid="access yarn application logs" urlDisplayName="access yarn application logs" pageTitle="Acessar os Logs de aplicativo do HDInsight do Azure de forma pragmaticamente|" metaKeywords ="" description="Acesse os Logs do aplicativo HDInsight de forma programática." metaCanonical="" services="hdinsight" documentationCenter="" title="Access HDInsight Application Logs Programmatically" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/21/2014" ms.author="bradsev" />

# Acessar os Logs de aplicativo do HDInsight programaticamente

Este tópico explica como enumerar os aplicativos de YARN de forma programática que foram concluídos em um cluster Hadoop no HDInsight e como acessar os logs do aplicativo de forma programática sem a necessidade de RDP em seus clusters. Especificamente, foram adicionados um novo componente e uma nova API:

  1. O servidor de histórico de aplicativo genérico em clusters HDInsight foi habilitado. Ele é um componente dentro do Servidor de Linha do Tempo de YARN que manipula o armazenamento e recuperação de informações genéricas de aplicativos concluídos.
  2. Novas APIs no HDInsight do Azure foram adicionadas para permitir que você enumere de forma programatica os aplicativos que foram executados em seus clusters e baixe o aplicativo relevante ou logs específicos do contêiner (em texto sem formatação) para ajudar na depuração de problemas de aplicativo que ocorrem.


## Pré-requisitos

O SDK do Azure HDInsight é exigido para usar o código apresentado neste tópico em um aplicativo .NET. A compilação publicada mais recentemente do SDK está disponível em [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). 

Para instalar o SDK do HDInsight de um aplicativo do Visual Studio, acesse o menu **Ferramentas**, clique em **NuGet Package Manager**e, em seguida, clique em **Package Manager Console**. Execute os seguintes comandos no console para instalar os pacotes.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

Este comando adiciona bibliotecas .NET no HDInsight e se refere a elas no projeto existente do Visual Studio.

## Neste artigo

- [Servidor de linha do tempo do YARN](#YARNTimelineServer)
- [Logs e aplicativos do YARN](#YARNAppsAndLogs)
- [Enumerando aplicativos e baixando os logs programaticamente](#enumerate-and-download)


## <a name="YARNTimelineServer"></a>Servidor de linha do tempo do YARN

O <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Servidor de linha do tempo do YARN</a> fornece informações genéricas sobre aplicativos concluídos, bem como informações específicas do framework aplicativo por meio de duas interfaces diferentes. Especificamente:

* O armazenamento e recuperação de informações de aplicativo genérico em clusters HDInsight foram habilitados com as versões 3.1.1.374 ou superior. 
* O componente de informações específicas do framework de aplicativo do servidor do cronograma não está atualmente disponível em clusters HDInsight.


As informações genéricas sobre aplicativos incluem os seguintes tipos de dados: 

* **ApplicationId** (um identificador exclusivo de um aplicativo), 
* o **usuário** que iniciou o aplicativo, 
* informações sobre **tentativas** feitas para concluir o aplicativo, e 
* os **contêineres** usados por qualquer tentativa de determinado aplicativo. 

Nos seus clusters HDInsight, essas informações serão armazenadas pelo Gerenciador de recursos para um repositório de histórico no contêiner padrão da sua conta de armazenamento padrão. Esses dados genéricos em aplicativos concluídos podem ser recuperados por meio de uma API REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps

Adicionamos novas APIs no SDK do HDInsight para .net para tornar mais fácil para recuperar esses dados programaticamente. Observe que, os dados genéricos também podem ser recuperados executando comandos YARN CLI diretamente em nós do cluster (depois de se conectar ao cluster usando o RDP).

## <a name="YARNAppsAndLogs"></a>Logs e aplicativos do YARN

YARN (um novo recurso negociador) oferece suporte a vários modelos de programação (sendo o MapReduce uma delas), separando o gerenciamento de recursos de agendamento/do monitoramento de aplicativos. Isso é feito por meio de um *Gerenciador de Recurso* (RM) global, por nó do operador *Gerenciadores de Nós* (NMs) e por aplicativo *Mestres de Aplicativo* (AMs). O aplicativo AM negocia recursos (CPU, memória, disco e rede) para executar o aplicativo com o Gerenciador de recurso. O RM funciona com NMs para conceder a esses recursos, que são concedidos como *Contêineres*. O AM é responsável por controlar o andamento dos contêineres atribuídos pelo RM. Um aplicativo pode exigir um número de contêineres dependendo da natureza do aplicativo. 

Além disso, cada aplicativo pode consistir em vários *tentativas de aplicativo* para concluir o aplicativo na presença de falhas ou devido à perda de comunicação entre um AM e um RM. Portanto, contêineres são concedidos a uma tentativa específica de um aplicativo. De certa forma, um contêiner fornece o contexto para a unidade básica de trabalho executado por um aplicativo de YARN e todo o trabalho é feito no contexto de que um contêiner é executado no nó único de trabalho no qual ele foi alocado. Consulte [Conceitos de YARN][YARN-concepts] para mais referências.

Os logs de aplicativos (e os logs de contêiner associado) são essenciais na depuração de aplicativos problemáticos do Hadoop. O YARN fornece uma estrutura legal para coleta, agregação e armazenamento dos logs de aplicativo com o recurso [Agregação de Log][log-aggregation]. O recurso de agregação de Log permite acessar logs de aplicativos mais determinista como ele agrega logs em todos os contêineres em um nó de trabalho e as armazena como um agregado arquivo de log por nó de trabalho no sistema de arquivos padrão após a conclusão de um aplicativo. O aplicativo deve usar centenas ou milhares de contêineres, mas logs para todos os contêineres executados em um nó único de trabalhado sempre serão agregados em um único arquivo, resultando em um arquivo de log por nó de trabalho usado pelo seu aplicativo. A agregação de log é habilitada como padrão em clusters HDInsight (versão 3.0 e versões posteriores) e logs agregados podem ser encontrados no contêiner padrão do cluster no seguinte local:

	wasb:///app-logs/<user>/logs/<applicationId>

onde "* user*" é o nome do usuário que iniciou o aplicativo, e "* applicationId *" é o identificador exclusivo de um aplicativo como atribuído pelo Resource Manager do YARN.

Os logs agregados não são diretamente legíveis por serem gravados em um [TFile][T-file], [formato binário][binary-format] indexado pelo contêiner. O YARN fornece ferramentas CLI para despejar os logs como texto sem formatação para aplicativos ou contêineres de interesse. Você pode exibir esses logs como texto sem formatação, executando um dos seguintes comandos de YARN diretamente em nós de cluster (depois de se conectar a ele via RDP):

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address> 

A próxima seção explica como logs específicos de aplicativo ou contêiner podem ser acessados programaticamente, sem precisar usar o RDP para os clusters HDInsight.

## <a name="enumerate-and-download"></a>Enumerando aplicativos e baixando os logs programaticamente

Para usar os exemplos de código a seguir, você deve atender aos pré-requisitos descritos acima, baixando a versão mais recente do SDK. do .Net do HDInsight. Consulte as instruções fornecidas.

O código a seguir ilustra como usar as novas APIs para enumerar os aplicativos e baixar os logs de aplicativos concluídos.

> [WACOM.NOTE] As APIs abaixo só funcionam em clusters do Hadoop "Executando" com a versão 3.1.1.374 ou superior. Adicione as seguintes diretivas.

	using Microsoft.Hadoop.Client;
	using Microsoft.WindowsAzure.Management.HDInsight;

Essas referenciam as APIs recentemente definidas no código a seguir. O seguinte trecho do código acima cria um Cliente de Histórico do Aplicativo em um cluster "Em execução" em sua assinatura.

	string subscriptionId = "<your-subscription-id>";
	string clusterName = "<your-cluster-name>";
	string certName = "<your-subscription-management-cert-name>";
	
	// Create an HDInsight Client
	X509Store store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
	store.Open(OpenFlags.ReadOnly);
	X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>()
	                            .Single(x => x.FriendlyName == certName);
	
	HDInsightCertificateCredential creds = 
				new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
	
	IHDInsightClient client = HDInsightClient.Connect(creds);
	
	// Get the cluster on which your applications were run
	// The cluster needs to be in "Running" state
	ClusterDetails cluster = client.GetCluster(clusterName);
	
	// Create an Application History client against your cluster
	IHDInsightApplicationHistoryClient appHistoryClient = 
				cluster.CreateHDInsightApplicationHistoryClient(TimeSpan.FromMinutes(5));


 Agora você pode usar o cliente de histórico do aplicativo para listar os aplicativos concluídos, filtrar os aplicativos com base em seus critérios e logs do aplicativo relevante de download. O trecho de código a seguir mostra como isso é feito programaticamente.

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

O código acima lista/localiza aplicativos interessantes usando o Cliente de Histórico do Aplicativo e, em seguida, baixa os logs para esses aplicativos em uma pasta local. 

Como alternativa, o trecho de código abaixo baixa os logs para um aplicativo cujo "ApplicationId" (seu identificador exclusivo) é conhecido. O ApplicationId é um identificador global exclusivo de um aplicativo atribuído pelo Gerenciador de Recursos. Ele é construído usando a hora de início do Gerenciador de Recursos com um contador aumenta de forma monotônica para solicitações enviadas a ele. ApplicationId é da forma "application\_<RM-start-time>\_<Counter&gt". Observe que o ApplicationId e JobId são diferentes. A JobId é um conceito específico de estrutura MapReduce enquanto o ApplicationId é um conceito YARN independente do framework. No YARN, um JobId identifica um determinado MapReduce Job como manipulado pelo aplicativo mestre de um aplicativo MapReduce enviado para o Gerenciador de Recursos.

	// Download application logs for an application whose application Id is known
	string applicationId = "application_1416017767088_0028";
	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	appHistoryClient.DownloadApplicationLogs(someApplication, downloadLocation);

If needed, you can also download logs for each container (or any specific container) used by an application as shown below.

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



[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/browse/HADOOP-3315
[binary-format]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf 
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

<!--HONumber=35.2-->
