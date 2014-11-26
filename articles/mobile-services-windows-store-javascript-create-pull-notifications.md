<properties linkid="develop-mobile-tutorials-create-pull-notifications-js" urlDisplayName="Define a custom API that supports pull notifications" pageTitle="Define a custom API that supports pull notifications - Azure Mobile Services" metaKeywords="" description="Learn how to Define a custom API that supports periodic notifications in Windows Store apps that use Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Define a custom API that supports periodic notifications" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Definir uma API personalizada que dá suporte a notificações periódicas

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/pt-br/develop/mobile/tutorials/create-pull-notifications-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/create-pull-notifications-js" title="JavaScript da Windows Store" class="current">JavaScript da Windows Store</a>
</div>

Este tópico mostra como usar uma API personalizada para oferecer suporte a notificações periódicas em um aplicativo da Windows Store. Com as notificações periódicas habilitadas, o Windows acessará periodicamente o ponto de extremidade da API personalizada e usará o XML retornado em um formato específico de bloco para atualizar o bloco do aplicativo no menu Iniciar. Para obter mais informações, consulte [Notificações periódicas][Notificações periódicas].

Você adicionará essa funcionalidade ao aplicativo que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou [Introdução a dados][Introdução a dados]. Para fazer isso, você executará as seguintes etapas:

1.  [Definir a API personalizada][Definir a API personalizada]
2.  [Atualizar o aplicativo para ativar notificações periódicas][Atualizar o aplicativo para ativar notificações periódicas]
3.  [Testar o aplicativo][Testar o aplicativo]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, primeiro você deve concluir a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou a [Introdução aos dados][Introdução a dados].

## <a name="define-custom-api"></a>Definir a API personalizada

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo.

    ![][0]

2.  Clique na guia **API** e, em seguida, clique em **Criar uma API personalizada**.

    ![][1]

    Isso exibe a caixa de diálogo **Criar uma nova API personalizada**.

3.  Altere **Obter permissão** para **Todos**, digite *organizar lado a lado* no **nome da API** e, em seguida, clique no botão de verificação.

    ![][2]

    Isso cria a nova API com acesso a GET público.

4.  Clique na nova entrada de organização lado a lado na tabela da API.

    ![][3]

5.  Clique na guia **Script** e substitua o código existente pelo seguinte:

        exports.get = function(request, response) {
            var wns = require('wns');
            var todoItems = request.service.tables.getTable('TodoItem');
            todoItems.where({
                complete: false
            }).read({
                success: sendResponse
            });

            function sendResponse(results) {
                var tileText = {
                    text1: "My todo list"
                };
                var i = 0;
                console.log(results)
                results.forEach(function(item) {
                    tileText["text" + (i + 2)] = item.text;
                    i++;
                });
                var xml = wns.createTileSquareText01(tileText);
                response.set('content-type', 'application/xml');
                response.send(200, xml);
            }
        };

    Esse código retorna os três primeiros itens não concluídos da tabela TodoItem e os carrega em um objeto JSON passado para a função **wns**.**createTileSquareText01**. Essa função retorna o seguinte XML do modelo tile:

        <tile>
            <visual>
                <binding template="TileSquareText01">
                    <text id="1">My todo list</text>
                    <text id="2">Task 1</text>
                    <text id="3">Task 2</text>
                    <text id="4">Task 3</text>
                </binding>
            </visual>
        </tile>

    A função **exports.get** é usada porque o cliente envia uma solicitação de GET para acessar o modelo tile.

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Este script da API personalizada usa o <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">m&oacute;dulo wns</a> do Node.js, que &eacute; referenciado usando a fun&ccedil;&atilde;o <strong>require</strong>. Esse m&oacute;dulo &eacute; diferente do <a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">objeto wns</a> retornado pelo <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/jj554217.aspx">objeto push</a>, que &eacute; usado para enviar notifica&ccedil;&otilde;es por push dos scripts de servidor.</p>
</div>

Em seguida, você modificará o aplicativo quickstart para iniciar notificações periódicas que atualizam o bloco ativo solicitando a nova API personalizada.

## <a name="update-app"></a><span class="short-header">Atualizar o aplicativo </span>Atualize o aplicativo para ativar as notificações periódicas

1.  No Visual Studio, pressione a tecla F5 para executar o aplicativo quickstar do tutorial anterior.

2.  Certifique-se de que ao menos um item seja exibido. Se não houver itens, digite texto em **Inserir um TodoItem** e clique em **Salvar**.

3.  No Visual Studio, expanda a pasta `\js` no Gerenciador de Soluções, abra o projeto default.js e adicione as seguintes linhas de código depois do código que define a variável **client**:

        var notifications = Windows.UI.Notifications;
        var recurrence = notifications.PeriodicUpdateRecurrence.hour;
        var url = new Windows.Foundation.Uri(client.applicationUrl, "/api/tiles");

        notifications.TileUpdateManager.createTileUpdaterForApplication().startPeriodicUpdate(url, recurrence);

    Esse código ativa as notificações periódicas para solicitar dados do modelo tile a partir da nova API personalizada **tile**. Selecione um valor [PeriodicUpdateRecurrance] que corresponda com a frequência de atualização de seus dados.

## <a name="test-app"></a>Testar o aplicativo

1.  No Visual Studio, pressione a tecla F5 para executar o aplicativo novamente.

    Isto ativará as notificações periódicas.

2.  Navegue para a tela Iniciar, localize o bloco dinâmico do aplicativo e observe se os dados do item agora são exibidos no bloco.

    ![][4]

## Próximas etapas

Agora que você criou uma notificação periódica, considere a possibilidade de obter mais informações sobre os seguintes tópicos dos Serviços Móveis:

-   [Introdução às notificações por push][Introdução às notificações por push]
    As notificações periódicas são gerenciadas pelo Windows e ocorrem apenas em uma programação predefinida. As notificações por push podem ser enviadas pelo serviço móvel sob demanda e podem ser notificações do sistema, de bloco e brutas.

-   [Referência de script de servidor de Serviços Móveis][Referência de script de servidor de Serviços Móveis]
    Saiba mais sobre como criar APIs personalizadas.

 
 


  [Notificações periódicas]: http://msdn.microsoft.com/pt-br/library/windows/apps/jj150587.aspx
  [Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
  [Introdução a dados]: /pt-br/develop/mobile/tutorials/started-with-data-js
  [Definir a API personalizada]: #define-custom-api
  [Atualizar o aplicativo para ativar notificações periódicas]: #update-app
  [Testar o aplicativo]: #test-app
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-services-selection.png
  [1]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create.png
  [2]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create-dialog.png
  [3]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-select.png
  [4]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-live-tile.png
  [Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-js
  [Referência de script de servidor de Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
