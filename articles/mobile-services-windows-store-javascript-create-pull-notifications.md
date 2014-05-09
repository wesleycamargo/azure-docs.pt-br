<properties linkid="develop-mobile-tutorials-create-pull-notifications-js" urlDisplayName="Definir uma API personalizada que ofereça suporte a notificações por pull" pageTitle="Definir uma API personalizada que ofereça suporte a notificações por pull - Serviços Móveis do Azure" metaKeywords="" description="Saiba como definir uma API personalizada que ofereça suporte a notificações periódicas em aplicativos da Windows Store que usam os Serviços Móveis do Azure" metaCanonical="" services="" documentationCenter="" title="Definir uma API personalizada que ofereça suporte a notificações periódicas" authors="glenga" solutions="" manager="" editor="" />




# Definir uma API personalizada que ofereça suporte a notificações periódicas

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/pt-br/develop/mobile/tutorials/create-pull-notifications-dotnet" title="C# da Windows Store">C# da Windows Store</a><a href="/pt-br/develop/mobile/tutorials/create-pull-notifications-js" title="JavaScript da Windows Store" class="current">JavaScript da Windows Store</a>
</div>

Este tópico mostra como usar uma API personalizada para oferecer suporte a notificações periódicas em um aplicativo da Windows Store. Com as notificações periódicas habilitadas, o Windows acessará periodicamente o ponto de extremidade da API personalizada e usará o XML retornado em um formato específico de bloco para atualizar o bloco do aplicativo no menu Iniciar. Para obter mais informações, consulte [Notificações periódicas]. 

Você adicionará essa funcionalidade ao aplicativo que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis] ou [Introdução a dados]. Para fazer isso, você executará as seguintes etapas:

1. [Definir a API personalizada]
2. [Atualizar o aplicativo para ativar notificações periódicas]
3. [Testar o aplicativo] 

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, primeiro você deve concluir a [Introdução aos Serviços Móveis] ou a [Introdução aos dados].  

## <a name="define-custom-api"></a>Definir a API personalizada

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

   	![][0]

2. Clique na guia **API** e clique em **Criar uma API personalizada**.

   	![][1]

   	Isso exibe a caixa de diálogo **Criar uma nova API personalizada**.

3. Altere a **Permissão de Get** para **Todos**, digite _tiles_ em **Nome da API** e clique no botão de seleção.

   	![][2]

	Isso cria a nova API com acesso a GET público.

4. Clique na nova entrada tiles na tabela da API.

	![][3]

5. Clique na guia **Script** e substitua o código existente pelo código a seguir:

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

   	<div class="dev-callout"><b>Observação</b>
   		<p>Este script da API personalizada usa o <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">módulo wns</a> do Node.js, que é referenciado usando a função <strong>require</strong>. Esse módulo é diferente do <a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">objeto wns</a> retornado pelo <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/jj554217.aspx">objeto push</a> que é usado para enviar notificações por push de scripts de servidor.</p>
   	</div>

Em seguida, você modificará o aplicativo quickstart para iniciar notificações periódicas que atualizam o bloco ativo solicitando a nova API personalizada.

<h2><a name="update-app"></a><span class="short-header">Atualizar o aplicativo </span>Atualizar o aplicativo para ativar notificações periódicas</h2>

1. No Visual Studio, pressione a tecla F5 para executar o aplicativo quickstart do tutorial anterior.

2. Verifique se ao menos um item é exibido. Se não houver itens, digite texto em **Inserir um TodoItem** e clique em **Salvar**.

3. No Visual Studio, expanda a pasta `\js` no Gerenciador de Soluções, abra o projeto default.js e adicione as seguintes linhas de código depois do código que define a variável **client**:

        var notifications = Windows.UI.Notifications;
        var recurrence = notifications.PeriodicUpdateRecurrence.hour;
        var url = new Windows.Foundation.Uri(client.applicationUrl, "/api/tiles");

        notifications.TileUpdateManager.createTileUpdaterForApplication().startPeriodicUpdate(url, recurrence);

	Esse código ativa as notificações periódicas para solicitar dados do modelo tile a partir da nova API personalizada **tile**. Selecione um valor de [PeriodicUpdateRecurrance] que coincida com a frequência de atualização de seus dados.

## <a name="test-app"></a>Testar o aplicativo

1. No Visual Studio, pressione a tecla F5 para executar o aplicativo novamente.

	Isto ativará as notificações periódicas.

2. Navegue para a tela Iniciar, localize o bloco ativo do aplicativo e observe se os dados do item agora estão exibidos no bloco.

 	![][4]

## Próximas etapas

Agora que você criou uma notificação periódica, considere a possibilidade de obter mais informações sobre os seguintes tópicos dos Serviços Móveis:

* [Introdução às notificações por push]
	<br/>As notificações periódicas são gerenciadas pelo Windows e ocorrem apenas em uma programação predefinida. As notificações por push podem ser enviadas pelo serviço móvel sob demanda e podem ser notificações do sistema, de bloco e brutas.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como criar APIs personalizadas.

<!-- Anchors. -->
[Definir a API personalizada]: #define-custom-api
[Atualizar o aplicativo para ativar notificações periódicas]: #update-app
[Testar o aplicativo]: #test-app
[Próximas etapas]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-javascript-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Notificações por push do Windows e o Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Referência a scripts de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Painel Meus Aplicativos]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/started-with-data-js
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-js
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-js
[JavaScript e HTML]: mobile-services-win8-javascript/

[Portal de Gerenciamento Azure]: https://manage.windowsazure.com/
[Notificações periódicas]: http://msdn.microsoft.com/pt-br/library/windows/apps/jj150587.aspx


