---
title: Agendar tarefas de back-end em um serviço móvel de back-end JavaScript | Microsoft Docs
description: Use o agendador nos Serviços Móveis do Azure para definir os trabalhos de back-end JavaScript executados em uma agenda.
services: mobile-services
documentationcenter: ''
author: ggailey777
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Agendar trabalhos recorrentes nos Serviços Móveis
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

> [!div class="op_single_selector"]
> * [Back-end do .NET](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
> * [Back-end do Javascript](mobile-services-schedule-recurring-tasks.md)
> 
> 

Este tópico mostra como usar a funcionalidade de agendador de trabalho no Portal clássico do Azure para definir o código de script de servidor que é executado com base em uma agenda definida por você. Neste caso, o script verifica periodicamente com um serviço remoto, neste caso, o Twitter, e armazena os resultados em uma nova tabela. Algumas outras tarefas periódicas que podem ser agendadas incluem:

* Arquivamento de registros de dados duplicados ou antigos.
* Solicitação e armazenamento de dados externos, como tweets, entradas RSS e informações sobre local.
* Processamento ou redimensionamento de imagens armazenadas.

Este tutorial mostra como usar o agendador de trabalhos para criar um trabalho agendado que solicita dados de tweet do Twitter e armazena os tweets em uma nova tabela de atualizações:

## <a name="get-oauth-credentials"></a>Registrar-se para acesso às APIs do Twitter v1.1 e armazenar as credenciais
[!INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

## <a name="create-table"></a>Criar a nova tabela de atualizações
Em seguida, você precisa criar uma nova tabela na qual armazenar tweets.

1. No [Portal clássico do Azure], clique na guia **Dados** de seu serviço móvel e clique em **+Criar**.
2. Em **Nome da tabela** digite *Atualizações* e clique no botão de seleção.

## <a name="add-job"></a>Criar um novo trabalho agendado
Agora, você pode criar o trabalho agendado que acessa o Twitter e armazena dados de tweet na nova tabela Atualizações.

1. Clique na guia **Agendador** e clique em **+Criar**.
   
   > [!NOTE]
   > Quando você executa o serviço móvel na camada <em>Gratuita</em>, pode executar apenas um trabalho agendado de cada vez. Em camadas pagas, você pode executar até dez trabalhos agendados ao mesmo tempo.
   > 
   > 
2. Na caixa de diálogo Agendador, digite *getUpdates* para o **Nome do Trabalho**, defina o intervalo e as unidades do agendamento e clique no botão de seleção.
   
       Isso cria um novo trabalho chamado **getUpdates**.
3. Clique no novo trabalho que você acabou de criar, clique na guia **Script** e substitua a função de espaço reservado **getUpdates** pelo seguinte código:
   
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

       Esse script chama a API de consulta do Twitter usando as credenciais armazenadas para solicitar tweets recentes que contêm a hashtag `#mobileservices`. Respostas e tweets duplicados são removidos dos resultados antes de serem armazenados na tabela.

    >[AZURE.NOTE]Esse exemplo pressupõe que apenas algumas linhas são inseridas na tabela durante cada execução agendada. Em casos onde várias linhas são inseridas em um loop, você pode esgotar as conexões ao executar na camada Gratuita. Nesse caso, você deve executar inserções em lotes. Para obter mais informações, consulte [Como executar inserções em lotes](mobile-services-how-to-use-server-scripts.md#bulk-inserts).

1. Clique em **Executar uma vez** para testar o script.
   
       Isso salva e executa o trabalho enquanto ele permanece desabilitado no Agendador.
2. Clique no botão Voltar, clique em **Dados**, clique na tabela **Atualizações**, clique em **Procurar** e verifique se os dados do Twitter foram inseridos na tabela.
3. Clique no botão Voltar, clique em **Agendador**, selecione **getUpdates** e clique em **Habilitar**.
   
       Isso permite que o trabalho seja executado no agendamento especificado, nesse caso, a cada hora.

Parabéns, você criou com êxito um novo trabalho agendado em seu serviço móvel. Esse trabalho será executado conforme programado até que você o desabilite ou modifique.

## <a name="nextsteps"></a>Consulte também
* [Referência de script de servidor dos Serviços Móveis] <br/>Saiba mais sobre como registrar e usar scripts de servidor.

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Next steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Portal clássico do Azure]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: /develop/mobile/how-to-guides/register-for-twitter-authentication
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

<!---HONumber=AcomDC_0727_2016-->