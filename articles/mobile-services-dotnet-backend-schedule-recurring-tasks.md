<properties  pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Windows Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga"  solutions="mobile" writer="" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Agendar trabalhos recorrentes nos Serviços Móveis

<div class="dev-center-tutorial-subselector">
    <a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title="Back-end do .NET" class="current">Back-end do .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="Back-end do JavaScript" >Back-end do JavaScript</a>
</div>

Este tópico mostra como usar a funcionalidade de agendador de trabalhos do Portal de Gerenciamento para definir o código de script que é executado com base em uma agenda definida por você. Neste caso, o script verifica periodicamente com um serviço remoto, neste caso, o Twitter, e armazena os resultados em uma nova tabela. Algumas outras tarefas periódicas que podem ser agendadas incluem:

-   Arquivamento de registros de dados duplicados ou antigos.
-   Solicitação e armazenamento de dados externos, como tweets, entradas RSS e informações sobre local.
-   Processamento ou redimensionamento de imagens armazenadas.

Este tutorial explica as etapas de como usar o agendador de trabalhos para criar um trabalho agendado que solicita dados de tweet do Twitter e armazena os tweets em uma nova tabela de atualizações:

-   [Registrar-se para acesso ao Twitter e armazenar as credenciais][Registrar-se para acesso ao Twitter e armazenar as credenciais]
-   [Baixar e instalar o LINQ para a biblioteca do Twitter][Baixar e instalar o LINQ para a biblioteca do Twitter]
-   [Criar a nova tabela Updates][Criar a nova tabela Updates]
-   [Criar um novo trabalho agendado][Criar um novo trabalho agendado]
-   [Testar o trabalho agendado localmente][Testar o trabalho agendado localmente]
-   [Publicar o serviço e registrar o trabalho][Publicar o serviço e registrar o trabalho]

> [WACOM.NOTE]Este tutorial usa o LINQ para a biblioteca do Twitter de terceiros para simplificar o acesso OAuth 2.0 para APIs do Twitter v1.1. Você deve baixar e instalar o Pacote NuGet do LINQ para Twitter para concluir este tutorial. Para obter mais informações, consulte [LINQ para projeto CodePlex do Twitter][LINQ para projeto CodePlex do Twitter].

## <a name="get-oauth-credentials"></a>Registrar-se para acesso às APIs do Twitter v1.1 e armazenar as credenciais

[WACOM.INCLUDE [mobile-services-register-twitter-access][mobile-services-register-twitter-access]]

1.  No Gerenciador de Soluções do Visual Studio, abra o arquivo web config do projeto do serviço móvel, localize as configurações dos aplicativos **MS\_TwitterConsumerKey** e **MS\_TwitterConsumerSecret** e substitua os valores dessas chaves pelos valores da chave de consumidor do segredo do consumidor do Twitter que você definiu no portal.

2.  Na mesma seção, adicione as seguintes novas configurações do aplicativo, substituindo os espaços reservados pelos valores do token de acesso e do segredo do token de acesso do Twitter que você definiu como configurações do aplicativo no portal:

        <add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" />
        <add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" />

    O serviço móvel usa essas configurações armazenadas quando é executado no computador local, o que permite testar o trabalho agendado antes de publicá-lo. Ao executar no Azure, o serviço móvel usa o conjunto de valores no portal e ignora essas configurações do projeto.

## <a name="install-linq2twitter"></a>Baixar e instalar o LINQ para a biblioteca do Twitter

1.  No **Gerenciador de Soluções** do Visual Studio, clique com o botão direito do mouse no nome do projeto e selecione **Gerenciar Pacotes NuGet**.

2.  No painel esquerdo, selecione a categoria **Online**, pesquise `linq2twitter`, clique em **Instalar** no pacote **linqtotwitter** e leia e aceite os contratos de licença.

    ![][]

    Isso adiciona o Linq para a biblioteca do Twitter a seu projeto de serviço móvel.

Em seguida, você precisa criar uma nova tabela na qual armazenar tweets.

## <a name="create-table"></a>Criar a nova tabela Updates

1.  No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse na pasta DataObjects, expanda **Adicionar**, clique em **Classe**, digite `Updates` para **Nome** e, em seguida, clique em **Adicionar**.

    Isso cria um novo arquivo de projeto para a classe Updates.

2.  Clique com o botão direito do mouse em **Referências**, clique em **Adicionar Referência...**, selecione **Framework** em **Assemblies**, marque **System.ComponentModel.DataAnnotations** e clique em **OK**.

    ![][1]

    Isto adiciona uma nova referência de assembly.

3.  Nessa nova classe, adicione as seguintes instruções **using**:

        using Microsoft.WindowsAzure.Mobile.Service;
        using System.ComponentModel.DataAnnotations;

4.  Substitua a definição da classe **Atualizações** pelo seguinte código:

        public class Updates 
        {
            [Key]
            public int UpdateId { get; set; }
            public long TweetId { get; set; }
            public string Text { get; set; }
            public string Author { get; set; }
            public DateTime Date { get; set; }
        }

5.  Expanda a pasta Models, abra o arquivo de contexto do modelo de dados (denominado *service\_name*Context.cs) e adicione a seguinte propriedade que retorna um **DbSet** tipado:

        public DbSet<Updates> Updates { get; set; }

    A tabela Updates, que é criada no banco de dados quando o DbSet é acessado pela primeira vez, é usada pelo serviço para armazenar dados de tweet.

    > [WACOM.NOTE] Ao usar o inicializador de banco de dados padrão, o Entity Framework eliminará e recriará o banco de dados sempre que detectar uma mudança do modelo de dados na definição de modelo Code First. Para fazer com que esse modelo de dados altere e mantenha os dados existentes no banco de dados, você deve usar as Migrações Code First. O inicializador padrão não pode ser usado em um Banco de Dados SQL do Azure. Para obter mais informações, consulte [Como usar as Migrações Code First para atualizar o modelo de dados][Como usar as Migrações Code First para atualizar o modelo de dados].

Em seguida, você pode criar o trabalho agendado que acessa o Twitter e armazena dados de tweet na nova tabela Updates.

## <a name="add-job"></a>Criar um novo trabalho agendado

1.  Expanda a pasta ScheduledJobs e abra o arquivo do projeto SampleJob.cs.

    Essa classe, que herda do **ScheduledJob**, representa um trabalho que pode ser agendado, no Portal de Gerenciamento do Azure, para executar em uma agenda fixa ou sob demanda.

2.  Substitua o conteúdo do arquivo SampleJob.cs pelo código a seguir:

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
                    context = new todolistContext(Services.Settings.Name);
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

    No código acima, você deve substituir as cadeias de caracteres *todolistService* e *todolistContext* pelo namespace e DbContext do projeto baixado, que são *mobile\_service\_name*Service e *mobile\_service\_name*Context, respectivamente.

    No código acima, o método de substituição **ExecuteAsync** chama a API de consulta do Twitter usando as credenciais armazenadas para solicitar tweets recentes que contêm a hashtag `#mobileservices`. Respostas e tweets duplicados são removidos dos resultados antes de serem armazenados na tabela.

## <a name="run-job-locally"></a>Testar o trabalho agendado localmente

Trabalhos agendados podem ser testados localmente antes de sua publicação no Azure e registrados no portal.

1.  No Visual Studio, com o projeto do serviço móvel definido como o projeto de inicialização, pressione F5.

    Isso inicia o projeto do serviço móvel e exibe uma nova janela do navegador com a página de boas-vindas.

2.  Clique em **experimentar** e clique em **POST jobs/{jobName}**.

    ![][2]

3.  Clique em **experimentar**, digite `Sample` como o valor do parâmetro **{jobName}** e clique em **Enviar**.

    ![][3]

    Isso envia uma nova solicitação POST ao ponto de extremidade de trabalho de exemplo. No serviço local, o método **ExecuteAsync** é iniciado. É possível definir um ponto de divisão neste método para depurar o código.

4.  No Gerenciador de Servidores, expanda **Conexões de Dados**, **MSTableConnectionString** e **tabelas**; clique com o botão direito do mouse em **Updates** e clique em **Mostrar Dados da Tabela**.

    Os novos tweets são inseridos como linhas na tabela de dados.

## <a name="register-job"></a>Publicar o serviço e registrar o novo trabalho

O trabalho deve ser registrado na guia **Agendador** para que os Serviços Móveis possam executá-lo na agenda que você definir.

1.  Republique o projeto de serviço móvel no Azure.

2.  No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em Serviços Móveis e clique em seu aplicativo.

    ![][4]

3.  Clique na guia **Agendador** e clique em **+Criar**.

    ![][5]

    > [WACOM.NOTE]Quando executa seu serviço móvel na camada *Gratuita*, você pode executar apenas um trabalho agendado de cada vez. Em camadas pagas, você pode executar até dez trabalhos agendados ao mesmo tempo.

4.  Na caixa de diálogo Agendador, digite *SampleJob* para o **Nome do Trabalho**, defina o intervalo e as unidades do agendamento e clique no botão de seleção.

    ![][6]

    Isso cria um novo trabalho chamado **SampleJob**.

5.  Clique no novo trabalho que você acabou de criar e clique em **Executar uma vez** para testar o script.

    ![][7]

    Isso executa o trabalho enquanto ele permanece desabilitado no Agendador. Nessa página, você pode habilitar o trabalho e alterar sua agenda a qualquer momento.

    > [WACOM.NOTE]Uma solicitação POST ainda pode ser usada para iniciar o trabalho agendado. No entanto, a autorização padroniza para o usuário, o que significa que a solicitação deve incluir a chave do aplicativo no cabeçalho.

6.  (Opcional) No [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em gerenciar para o banco de dados associado ao seu serviço móvel.

    ![][8]

7.  No Portal de Gerenciamento, execute uma consulta para exibir as alterações feitas pelo aplicativo. Sua consulta será semelhante à consulta a seguir, mas use o nome do seu serviço móvel como o nome do esquema, em vez de `todolist`.

        SELECT * FROM [todolist].[Updates]

Parabéns, você criou com êxito um novo trabalho agendado em seu serviço móvel. Esse trabalho será executado conforme programado até que você o desabilite ou modifique.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-schedule-recurring-tasks/ "Back-end do JavaScript"
  [Registrar-se para acesso ao Twitter e armazenar as credenciais]: #get-oauth-credentials
  [Baixar e instalar o LINQ para a biblioteca do Twitter]: #install-linq2twitter
  [Criar a nova tabela Updates]: #create-table
  [Criar um novo trabalho agendado]: #add-job
  [Testar o trabalho agendado localmente]: #run-job-locally
  [Publicar o serviço e registrar o trabalho]: #register-job
  [LINQ para projeto CodePlex do Twitter]: http://linqtotwitter.codeplex.com/
  [mobile-services-register-twitter-access]: ../includes/mobile-services-register-twitter-access.md
  []: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
  [1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-component-model-reference.png
  [Como usar as Migrações Code First para atualizar o modelo de dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-use-code-first-migrations
  [2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-start-page.png
  [3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-try-this-out.png
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
  [5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
  [6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
  [7]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
  [8]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png
