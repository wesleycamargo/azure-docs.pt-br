<properties linkid="develop-mobile-tutorials-create-pull-notifications-dotnet" urlDisplayName="Definir uma API personalizada que dê suporte a notificações por pull" pageTitle="Definir uma API personalizada dê suporte a notificações por pull - Serviços Móveis do Azure" metaKeywords="" description="Saiba como definir uma API personalizada que dê suporte a notificações periódicas em aplicativos da Windows Store que usam os Serviços Móveis do Azure." metaCanonical="" services="" documentationCenter="" title="Definir uma API personalizada que dê suporte a notificações periódicas" authors="glenga" solutions="" manager="" editor="" />




# Definir uma API personalizada que dê suporte a notificações periódicas

<div class="dev-center-tutorial-selector"> 
	<a href="/pt-br/develop/mobile/tutorials/create-pull-notifications-dotnet" title="Windows Store C#" class="current">Windows Store C#</a><a href="/pt-br/develop/mobile/tutorials/create-pull-notifications-js" title="JavaScript da Windows Store">JavaScript da Windows Store</a>
</div>

Este tópico mostra como usar uma API personalizada para oferecer suporte a notificações periódicas em um aplicativo da Windows Store. Com as notificações periódicas habilitadas, o Windows acessará periodicamente o ponto de extremidade da API personalizada e usará o XML retornado em um formato específico de bloco para atualizar o bloco do aplicativo no menu Iniciar. Para obter mais informações, consulte [Notificações periódicas]. 

Você adicionará essa funcionalidade ao aplicativo que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis] ou [Introdução aos dados]. Para fazer isto, você concluirá as seguintes etapas:

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

3. Altere a **Permissão de GET** para **Todos**, digite _blocos_ em **Nome da API**e clique no botão de seleção.

   	![][2]

	Isso cria a nova API com acesso para GET público.

4. Clique na nova entrada de blocos na tabela de API.

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

	Esse código retorna os 3 primeiros itens não concluídos da tabela TodoItem e os carrega em um objeto JSON passado para a função **wns**.**createTileSquareText01**. Esta função retorna o seguinte XML de modelo de bloco:

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

	A função **exports.get** é usada porque o cliente envia uma solicitação de GET para acessar o modelo de bloco.

   	<div class="dev-callout"><b>Note</b>
   		<p>This custom API script uses the Node.js <a href="http://go.microsoft.com/fwlink/p/?LinkId=306750">wns module</a>, which is referenced by using the <strong>require</strong> function. This module is different from the <a href="http://go.microsoft.com/fwlink/p/?LinkId=260591">wns object</a> returned by the <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/jj554217.aspx">push object</a>, which is used to send push notifications from server scripts.</p>
   	</div>

Em seguida, você modificará o aplicativo Guia de início rápido para iniciar notificações periódicas que atualizem o bloco ativo, solicitando a nova API personalizada.

<h2><a name="update-app"></a><span class="short-header">Atualizar o aplicativo </span>Atualizar o aplicativo para ativar notificações periódicas</h2>

1. No Visual Studio, pressione a tecla F5 para executar o aplicativo Guia de início rápido a partir do tutorial anterior.

2. Verifique se ao menos um item é exibido. Se não houver itens, digite o texto em **Inserir um TodoItem** e clique em **Salvar**.

3. No Visual Studio, abra o arquivo de projeto App.xaml.cs e adicione a seguinte instrução de uso:

		using Windows.UI.Notifications;

4. Adicione o código a seguir no manipulador de eventos **OnLaunched**:

        TileUpdateManager.CreateTileUpdaterForApplication().StartPeriodicUpdate(
            new System.Uri(MobileService.ApplicationUri, "/api/tiles"),
            PeriodicUpdateRecurrence.Hour
        );

	Esse código ativa notificações periódicas para solicitar dados do modelo de blocos a partir da nova API personalizada **blocos**. Selecione um valor [PeriodicUpdateRecurrance] que coincida com a frequência de atualização de seus dados.

## <a name="test-app"></a>Testar o aplicativo

1. No Visual Studio, pressione a tecla F5 para executar o aplicativo novamente.

	Isto ativará as notificações periódicas.

2. Navegue até a tela Iniciar, localize o bloco ativo para o aplicativo e observe que os dados do item serão agora exibidos no bloco.

 	![][4]

## Próximas etapas

Agora que você criou uma notificação periódica, considere a possibilidade de saber mais informações sobre os seguintes tópicos dos Serviços Móveis:

* [Introdução às notificações por push]
	<br/>As notificações periódicas são gerenciadas pelo Windows e ocorrem apenas em uma programação predefinida. As notificações por push podem ser enviadas pelo serviço móvel sob demanda e podem ser notificações brutas, em bloco e do sistema.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como criar APIs personalizadas.

* [Referência conceitual de tutorial do .NET de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com .NET.

<!-- Anchors. -->
[Definir a API personalizada]: #define-custom-api
[Atualizar o aplicativo para ativar notificações periódicas]: #update-app
[Testar o aplicativo]: #test-app
[Próximas etapas]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-services-selection.png
[1]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create.png
[2]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-create-dialog.png
[3]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-select.png
[4]: ./media/mobile-services-windows-store-dotnet-create-pull-notifications/mobile-custom-api-live-tile.png

<!-- URLs. -->
[Notificações por push e Live Connect do Windows]: http://go.microsoft.com/fwlink/?LinkID=257677
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Painel Meus Aplicativos]: http://go.microsoft.com/fwlink/?LinkId=262039
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet
[JavaScript e HTML]: mobile-services-win8-javascript/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Notificações periódicas]: http://msdn.microsoft.com/pt-br/library/windows/apps/jj150587.aspx

[Referência conceitual de tutorial do .NET de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library


