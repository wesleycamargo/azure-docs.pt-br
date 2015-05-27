<properties 
	pageTitle="Agendar tarefas de back-end com o Agendador - Serviços Móveis" 
	description="Use o Agendador dos serviços móveis do Azure para agendar trabalhos para seu aplicativo móvel." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	writer="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="2/26/2015" 
	ms.author="glenga"/>

# Agendar trabalhos recorrentes nos Serviços Móveis 

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [(Any | Javascript)](mobile-services-schedule-recurring-tasks.md)
 
Este tópico mostra como usar a funcionalidade de agendador de trabalhos do Portal de Gerenciamento para definir o código de script que é executado com base em uma agenda definida por você. Neste caso, o script verifica periodicamente com um serviço remoto, neste caso, o Twitter, e armazena os resultados em uma nova tabela. Algumas outras tarefas periódicas que podem ser agendadas incluem:

+ Arquivamento de registros de dados duplicados ou antigos.
+ Solicitação e armazenamento de dados externos, como tweets, entradas RSS e informações sobre local.
+ Processamento ou redimensionamento de imagens armazenadas.

Este tutorial explica as etapas de como usar o agendador de trabalhos para criar um trabalho agendado que solicita dados de tweet do Twitter e armazena os tweets em uma nova tabela de atualizações:

1. [Registrar-se para acesso ao Twitter e armazenar as credenciais]
2. [Baixar e instalar a biblioteca LINQ to Twitter]
3. [Criar a nova tabela Updates]
4. [Criar um novo trabalho agendado]
5. [Testar o trabalho agendado localmente]
6. [Publicar o serviço e registrar o trabalho]

>[AZURE.NOTE]Este tutorial usa o LINQ para a biblioteca do Twitter de terceiros para simplificar o acesso de OAuth 2.0 às APIs do Twitter v1.1. Você deve baixar e instalar o Pacote NuGet do LINQ para Twitter para concluir este tutorial. Para obter mais informações, consulte [LINQ para projeto CodePlex do Twitter].

##<a name="get-oauth-credentials"></a>Registrar-se para acesso às APIs do Twitter v1.1 e armazenar as credenciais

[AZURE.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

<ol start="7">
<li><p>No Gerenciador de Soluções do Visual Studio, abra o arquivo web config do projeto do serviço móvel, localize as configurações dos aplicativos <strong>MS_TwitterConsumerKey</strong> e <strong>MS_TwitterConsumerSecret</strong> e substitua os valores dessas chaves pelos valores da chave de consumidor do segredo do consumidor do Twitter que você definiu no portal.</p></li>

<li><p>Na mesma seção, adicione as seguintes novas configurações do aplicativo, substituindo os espaços reservados pelos valores do token de acesso e do segredo do token de acesso do Twitter que você definiu como configurações do aplicativo no portal:</p>

<pre><code>&lt;add key="TWITTER_ACCESS_TOKEN" value="**seu_token_de_acesso**" />
&lt;add key="TWITTER_ACCESS_TOKEN" value="**chave_secreta_do_seu_token_de_acesso**" /></code></pre>

<p>O serviço móvel usa essas configurações armazenadas quando é executado no computador local, o que permite testar o trabalho agendado antes de publicá-lo. Ao executar no Azure, o serviço móvel usa o conjunto de valores no portal e ignora essas configurações do projeto.  </p></li>
</ol>

##<a name="install-linq2twitter"></a>Baixar e instalar a biblioteca LINQ to Twitter

1. No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no nome do projeto e selecione **Gerenciar Pacotes NuGet**.

2. No painel esquerdo, selecione a categoria **Online**, pesquise por `linq2twitter`, clique em **Instalar** no pacote **linqtotwitter** e leia e aceite os contratos de licença.

  	![][1]

  	Isso adiciona o Linq para a biblioteca do Twitter a seu projeto de serviço móvel.

Em seguida, você precisa criar uma nova tabela na qual armazenar tweets.

##<a name="create-table"></a>Criar a nova tabela de Atualizações

1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse na pasta DataObjects, expanda **Adicionar**, clique em **Classe**, digite `Updates` para **Nome** e, em seguida, clique em **Adicionar**.

	Isso cria um novo arquivo de projeto para a classe Updates.

2. Clique com o botão direito do mouse em **Referências**, clique em **Adicionar Referência...**, selecione **Framework** em **Assemblies**, marque **System.ComponentModel.DataAnnotations** e clique em **OK**.

	![][7]

	Isto adiciona uma nova referência de assembly.

2. Nessa nova classe, adicione as seguintes instruções **using**:
 
		using Microsoft.WindowsAzure.Mobile.Service;
		using System.ComponentModel.DataAnnotations;

3. Substitua a definição da classe **Atualizações** pelo seguinte código:

		public class Updates 
	    {
	        [Key]
	        public int UpdateId { get; set; }
	        public long TweetId { get; set; }
	        public string Text { get; set; }
	        public string Author { get; set; }
	        public DateTime Date { get; set; }
    	}

4. Expanda a pasta Models, abra o arquivo de contexto do modelo de dados (denominado <em>service_name</em>Context.cs) e adicione a seguinte propriedade que retorna um **DbSet** tipado:

		public DbSet<Updates> Updates { get; set; }

	A tabela Updates, que é criada no banco de dados quando o DbSet é acessado pela primeira vez, é usada pelo serviço para armazenar dados de tweet.

	>[AZURE.NOTE]Ao usar o inicializador de banco de dados padrão, o Entity Framework vai remover e recriar o banco de dados sempre que detectar uma alteração no modelo de dados na definição do modelo Code First. Para fazer com que esse modelo de dados altere e mantenha os dados existentes no banco de dados, você deve usar as Migrações Code First. O inicializador padrão não pode ser usado em um Banco de Dados SQL do Azure. Para obter mais informações, consulte [Como usar as Migrações Code First para atualizar o modelo de dados](mobile-services-dotnet-backend-use-code-first-migrations.md).

Em seguida, você pode criar o trabalho agendado que acessa o Twitter e armazena dados de tweet na nova tabela Updates.

##<a name="add-job"></a>Criar um novo trabalho agendado  

1. Expanda a pasta ScheduledJobs e abra o arquivo do projeto SampleJob.cs.

	Essa classe, que herda do **ScheduledJob**, representa um trabalho que pode ser agendado, no Portal de Gerenciamento do Azure, para executar em uma agenda fixa ou sob demanda.

2. Substitua o conteúdo do arquivo SampleJob.cs pelo código a seguir:
 
		using System;
		using System.Linq;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Web.Http;
		using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.WindowsAzure.Mobile.Service.ScheduledJobs;
		using LinqToTwitter;
		using todolistService.Models;
		using todolistService.DataObjects;
		
		namespace todolistService
		{
		    // A simple scheduled job which can be invoked manually by submitting an HTTP
		    // POST request to the path "/jobs/sample".
		    public class SampleJob : ScheduledJob
		    {
		        private todolistContext context;
		        private string accessToken;
		        private string accessTokenSecret;
		
		        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, CancellationToken cancellationToken)
		        {
		            base.Initialize(scheduledJobDescriptor, cancellationToken);
		
		            // Create a new context with the supplied schema name.
		            context = new todolistContext();
		        }
		
		        public async override Task ExecuteAsync()
		        {            
		            // Try to get the stored Twitter access token from app settings.  
		            if (!(Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN", out accessToken) |
		            Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN_SECRET", out accessTokenSecret)))
		            {
		                Services.Log.Error("Could not retrieve Twitter access credentials.");
		            }
		
		            // Create a new authorizer to access Twitter v1.1 APIs
		            // using single-user OAUth 2.0 credentials.
		            MvcAuthorizer auth = new MvcAuthorizer();
		            SingleUserInMemoryCredentialStore store = 
		                new SingleUserInMemoryCredentialStore()
		            {
		                ConsumerKey = Services.Settings.TwitterConsumerKey,
		                ConsumerSecret = Services.Settings.TwitterConsumerSecret,
		                OAuthToken = accessToken,
		                OAuthTokenSecret = accessTokenSecret
		            };
		
		            // Set the credentials for the authorizer.
		            auth.CredentialStore = store;
		
		            // Create a new LINQ to Twitter context.
		            TwitterContext twitter = new TwitterContext(auth);
		
		            // Get the ID of the most recent stored tweet.
		            long lastTweetId = 0;
		            if (context.Updates.Count() > 0)
		            {
		                lastTweetId = (from u in context.Updates
		                               orderby u.TweetId descending
		                               select u).Take(1).SingleOrDefault()
		                                            .TweetId;
		            }
		
		            // Execute a search that returns a filtered result.
		            var response = await (from s in twitter.Search
		                                  where s.Type == SearchType.Search
		                                  && s.Query == "%23mobileservices"
		                                  && s.SinceID == Convert.ToUInt64(lastTweetId + 1)
		                                  && s.ResultType == ResultType.Recent
		                                  select s).SingleOrDefaultAsync();
		
		            // Remove retweets and replies and log the number of tweets.
		            var filteredTweets = response.Statuses
		                .Where(t => !t.Text.StartsWith("RT") && t.InReplyToUserID == 0);
		            Services.Log.Info("Fetched " + filteredTweets.Count()
		                + " new tweets from Twitter.");
		
		            // Store new tweets in the Updates table.
		            foreach (Status tweet in filteredTweets)
		            {
		                Updates newTweet =
		                    new Updates
		                    {
		                        TweetId = Convert.ToInt64(tweet.StatusID),
		                        Text = tweet.Text,
		                        Author = tweet.User.Name,
		                        Date = tweet.CreatedAt
		                    };
		
		                context.Updates.Add(newTweet);
		            }
		
		            await context.SaveChangesAsync();
		        }
		        protected override void Dispose(bool disposing)
		        {
		            base.Dispose(disposing);
		            if (disposing)
		            {
		                context.Dispose();
		            }
		        }
		    }
		}

	No código acima, você deve substituir as cadeias de caracteres _todolistService_ e _todolistContext_ pelo namespace e DbContext do projeto baixado, que são <em>mobile&#95;service&#95;name</em>Service e <em>mobile&#95;service&#95;name</em>Context, respectivamente.
   	
	No código acima, o método de substituição **ExecuteAsync** chama a API de consulta do Twitter usando as credenciais armazenadas para solicitar tweets recentes que contenham a hashtag `#mobileservices`. Respostas e tweets duplicados são removidos dos resultados antes de serem armazenados na tabela.

##<a name="run-job-locally"></a>Testar o trabalho agendado localmente

Trabalhos agendados podem ser testados localmente antes de sua publicação no Azure e registrados no portal.

1. No Visual Studio, com o projeto do serviço móvel definido como o projeto de inicialização, pressione F5.

	Isso inicia o projeto do serviço móvel e exibe uma nova janela do navegador com a página de boas-vindas.

2. Clique em **experimentar** e clique em **POST jobs/{jobName}**.

	![][8]
 
4. Clique em **experimentar**, digite `Sample` como o valor do parâmetro **{jobName}** e clique em **Enviar**.

	![][9]

	Isso envia uma nova solicitação POST ao ponto de extremidade de trabalho de exemplo. No serviço local, o método **ExecuteAsync** é iniciado. É possível definir um ponto de divisão neste método para depurar o código.

3. No Gerenciador de Servidores, expanda **Conexões de Dados**, **MSTableConnectionString** e **tabelas**; clique com o botão direito do mouse em **Updates** e clique em **Mostrar Dados da Tabela**.

	Os novos tweets são inseridos como linhas na tabela de dados.

##<a name="register-job"></a>Publicar o serviço e registrar o novo trabalho 

O trabalho deve ser registrado na guia **Agendador** para que os Serviços Móveis possam executá-lo na agenda que você definir.

3. Republique o projeto de serviço móvel no Azure. 

4. No [Portal de Gerenciamento do Azure], clique em Serviços Móveis e clique em seu aplicativo.

2. Clique na guia **Agendador** e clique em **+Criar**.

    >[AZURE.NOTE]Ao executar o seu serviço móvel na camada <em>Gratuita</em>, você pode executar apenas um trabalho agendado de cada vez. Em camadas pagas, você pode executar até dez trabalhos agendados ao mesmo tempo.

3. Na caixa de diálogo Agendador, digite _Exemplo_ no **Nome do Trabalho**, defina o intervalo e as unidades do agendamento e clique no botão de seleção.
   
   	![][4]

   	Isso cria um novo trabalho chamado **Exemplo**.

4. Clique no novo trabalho que você acabou de criar e clique em **Executar uma vez** para testar o script.

   	Isso executa o trabalho enquanto ele permanece desabilitado no Agendador. Nessa página, você pode habilitar o trabalho e alterar sua agenda a qualquer momento.

	>[AZURE.NOTE]Uma solicitação POST ainda pode ser usada para iniciar o trabalho agendado. No entanto, a autorização padroniza para o usuário, o que significa que a solicitação deve incluir a chave do aplicativo no cabeçalho.

4. (Opcional) No [Portal de Gerenciamento do Azure], clique em gerenciar para o banco de dados associado ao seu serviço móvel.

    ![][6]

5. No Portal de Gerenciamento, execute uma consulta para exibir as alterações feitas pelo aplicativo. A sua consulta será semelhante à consulta a seguir, mas use o nome do seu serviço móvel como o nome do esquema em vez de `todolist`.

        SELECT * FROM [todolist].[Updates]

Parabéns, você criou com êxito um novo trabalho agendado em seu serviço móvel. Esse trabalho será executado conforme programado até que você o desabilite ou modifique.

<!-- Anchors. -->
[Registrar-se para acesso ao Twitter e armazenar as credenciais]: #get-oauth-credentials
[Baixar e instalar a biblioteca LINQ to Twitter]: #install-linq2twitter
[Criar a nova tabela Updates]: #create-table
[Criar um novo trabalho agendado]: #add-job
[Testar o trabalho agendado localmente]: #run-job-locally
[Publicar o serviço e registrar o trabalho]: #register-job
[Next steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
[2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
[3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
[5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
[6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png
[7]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-component-model-reference.png
[8]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-start-page.png
[9]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-try-this-out.png

<!-- URLs. -->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: mobile-services-how-to-register-twitter-authentication.md
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[LINQ para projeto CodePlex do Twitter]: http://linqtotwitter.codeplex.com/
<!--HONumber=54-->