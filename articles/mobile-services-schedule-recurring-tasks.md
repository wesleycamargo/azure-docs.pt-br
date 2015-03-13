<properties 
	pageTitle="Agendar tarefas de back-end com o Agendador - Serviços Móveis" 
	description="Use o Agendador dos serviços móveis do Azure para agendar trabalhos para seu aplicativo móvel." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="glenga"/>

# Agendar trabalhos recorrentes nos Serviços Móveis 

<div class="dev-center-tutorial-subselector">
	<a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title=".NET backend">Back-end .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="JavaScript backend" class="current">Back-end JavaScript</a>
</div>
 
Este tópico mostra como usar a funcionalidade de agendador de trabalhos do Portal de Gerenciamento para definir o código de script que é executado com base em uma agenda definida por você. Neste caso, o script verifica periodicamente com um serviço remoto, neste caso, o Twitter, e armazena os resultados em uma nova tabela. Algumas outras tarefas periódicas que podem ser agendadas incluem:

+ Arquivamento de registros de dados duplicados ou antigos.
+ Solicitação e armazenamento de dados externos, como tweets, entradas RSS e informações sobre local.
+ Processamento ou redimensionamento de imagens armazenadas.

Este tutorial explica as etapas de como usar o agendador de trabalhos para criar um trabalho agendado que solicita dados de tweet do Twitter e armazena os tweets em uma nova tabela de atualizações:

+ [Registrar-se para acesso ao Twitter e armazenar as credenciais]
+ [Criar a nova tabela de Atualizações]
+ [Criar um novo trabalho agendado]

##<a name="get-oauth-credentials"></a>Registrar-se para acesso às APIs do Twitter v1.1 e armazenar as credenciais

[AZURE.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

##<a name="create-table"></a>Criar a nova tabela de Atualizações

Em seguida, você precisa criar uma nova tabela na qual armazenar tweets.

2. No Portal de Gerenciamento, clique na guia **Dados** de seu serviço móvel e clique em **+Criar**.

   	![][2]

   	Isso exibe a caixa de diálogo **Criar uma nova tabela**.

3. Em **Nome da tabela**, digite _Atualizações_ e então clique no botão de seleção.

   	![][3]

  	Isso cria uma nova tabela de **Atualizações** de armazenamento. 

##<a name="add-job"></a>Criar um novo trabalho agendado  

Agora, você pode criar o trabalho agendado que acessa o Twitter e armazena dados de tweet na nova tabela Atualizações.

2. Clique na guia **Agendador** e clique em **+Criar**. 

   	![][4]

    >[AZURE.NOTE]Quando você executa seu serviço móvel na camada <em>Gratuita</em>, pode executar apenas um trabalho agendado de cada vez. Em camadas pagas, você pode executar até dez trabalhos agendados simultaneamente.

3. Na caixa de diálogo Agendador, digite _getUpdates_ para o **Nome do Trabalho**, defina o intervalo e as unidades do agendamento e clique no botão de seleção. 
   
   	![][5]

   	Isso cria um novo trabalho chamado **getUpdates**. 

4. Clique no novo trabalho que você acabou de criar e clique na guia **Script**.

   	![][6] 

5. Substitua a função de espaço reservado **getUpdates** pelo seguinte código:

		var updatesTable = tables.getTable('Updates');
		var request = require('request');
		var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json?q=%23mobileservices&result_type=recent";

		// Get the service configuration module.
		var config = require('mobileservice-config');
		
		// Get the stored Twitter consumer key and secret. 
		var consumerKey = config.twitterConsumerKey,
		    consumerSecret = config.twitterConsumerSecret
		// Get the Twitter access token from app settings.    
		var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
		    accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;
		
		function getUpdates() {   
		    // Check what is the last tweet we stored when the job last ran
		    // and ask Twitter to only give us more recent tweets
		    appendLastTweetId(
		        twitterUrl, 
		        function twitterUrlReady(url){            
		            // Create a new request with OAuth credentials.
		            request.get({
		                url: url,                
		                oauth: {
		                    consumer_key: consumerKey,
		                    consumer_secret: consumerSecret,
		                    token: accessToken,
		                    token_secret: accessTokenSecret
		                }},
		                function (error, response, body) {
		                if (!error && response.statusCode == 200) {
		                    var results = JSON.parse(body).statuses;
		                    if(results){
		                        console.log('Fetched ' + results.length + ' new results from Twitter');                       
		                        results.forEach(function (tweet){
		                            if(!filterOutTweet(tweet)){
		                                var update = {
		                                    twitterId: tweet.id,
		                                    text: tweet.text,
		                                    author: tweet.user.screen_name,
		                                    date: tweet.created_at
		                                };
		                                updatesTable.insert(update);
		                            }
		                        });
		                    }            
		                } else { 
		                    console.error('Could not contact Twitter');
		                }
		            });
		
		        });
		 }
		// Find the largest (most recent) tweet ID we have already stored
		// (if we have stored any) and ask Twitter to only return more
		// recent ones
		function appendLastTweetId(url, callback){
		    updatesTable
		    .orderByDescending('twitterId')
		    .read({success: function readUpdates(updates){
		        if(updates.length){
		            callback(url + '&since_id=' + (updates[0].twitterId + 1));           
		        } else {
		            callback(url);
		        }
		    }});
		}
		
		function filterOutTweet(tweet){
		    // Remove retweets and replies
		    return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
		}


   	Esse script chama a API de consulta do Twitter usando as credenciais armazenadas para solicitar tweets recentes que contêm o hashtag "#mobileservices". Respostas e tweets duplicados são removidos dos resultados antes de serem armazenados na tabela.

    >[AZURE.NOTE]Esse exemplo pressupõe que apenas algumas linhas são inseridas na tabela durante cada execução agendada. Em casos onde várias linhas são inseridas em um loop, você pode esgotar as conexões ao executar na camada Gratuita. Nesse caso, você deve executar inserções em lotes. Para obter mais informações, consulte <a href="/pt-br/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts">Como: Executar inserções em massa</a>.

6. Clique em **Executar uma vez** para testar o script. 

  	![][7]

   	Isso salva e executa o trabalho enquanto ele permanece desabilitado no Agendador.

7. Clique no botão Voltar, clique em **Dados**, clique na tabela **Atualizações**, clique em **Procurar** e verifique se os dados do Twitter foram inseridos na tabela.

   	![][8]

8. Clique no botão Voltar, clique em **Agendador**, selecione **getUpdates** e clique em **Habilitar**.

   	![][9]

   	Isso permite que o trabalho seja executado no agendamento especificado, nesse caso, a cada hora.

Parabéns, você criou com êxito um novo trabalho agendado em seu serviço móvel. Esse trabalho será executado conforme programado até que você o desabilite ou modifique.

## <a name="nextsteps"> </a>Próximas Etapas

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.

<!-- Anchors. -->
[Registrar-se para acesso ao Twitter e armazenar as credenciais]: #get-oauth-credentials
[Criar a nova tabela de Atualizações]: #create-table
[Criar um novo trabalho agendado]: #add-job
[Próximas etapas]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-schedule-recurring-tasks/mobile-twitter-my-apps.png
[1]: ./media/mobile-services-schedule-recurring-tasks/mobile-twitter-app-secrets.png
[2]: ./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png
[3]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png
[4]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[5]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png
[6]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png
[7]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png
[8]: ./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png
[9]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png
[10]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-app-settings.png
[11]: ./media/mobile-services-schedule-recurring-tasks/mobile-identity-tab-twitter-only.png

<!-- URLs. -->
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://azure.microsoft.com/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Registrar seus aplicativos para logon do Twitter com os Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/register-for-twitter-authentication
[Desenvolvedores do Twitter]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[Configurações do aplicativo]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

\<!--HONumber=42-->
