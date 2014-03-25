Primeiro, use o assistente Adicionar Notificação por Push no Visual Studio 2013 para registrar seu aplicativo com a Windows Store, configurar o serviço móvel para ativar as notificações por push e adicionar o código ao seu aplicativo, a fim de registrar um canal do dispositivo.

0. Se ainda não o fez, siga as etapas em [Importar o arquivo publishsettings no Visual Studio 2013] para importar seu arquivo publisher.settings para o Visual Studio. 

	Não é necessário fazer isso se você já tiver usado o Visual Studio para criar ou gerenciar serviços móveis na sua assinatura do Windows Azure.

1. No Visual Studio 2013, abra o Gerenciador de Soluções, clique com o botão direito do projeto, clique em **Adicionar** e, em seguida, em **Notificação por push**. 

	![mobile-add-push-notifications-vs2013](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)

	Isso inicia o Assistente Adicionar Notificação por Push.

2. Clique em **Próximo**, entre na sua conta da Windows Store e forneça um nome em **Reservar um novo nome** e clique em **Reservar**.

	![mobile-add-push-notifications-vs2013-2](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-2.png) 

	Isso cria um novo registro de aplicativo.

3. Clique no novo registro na lista **Nome do Aplicativo** e clique em **Próximo**.

	![mobile-add-push-notifications-vs2013-3](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-3.png)

4. Na caixa de diálogo **Selecione um serviço**, clique no nome do serviço móvel que você criou quando concluiu [Começar com os Serviços Móveis] ou [Começar com os dados] e, em seguida, clique em **Próximo** e **Concluir**. 

	
	O serviço móvel é atualizado para registrar o pacote de aplicativo SID e segredo de cliente e a nova tabela **canais** é criada. Os Serviços Móveis agora estão configurados para trabalhar com o WNS (Windows Push Notification Services, Serviços de Notificação por Push do Windows) para poder enviar notificações para o seu aplicativo.   

	<div class="dev-callout"><b>Observação</b>
		<p>Quando seu aplicativo ainda não estiver configurado para se conectar ao serviço móvel, o assistente também concluirá as mesmas tarefas de configuração demonstradas em <strong>Começar com dados</strong>.</p>
	</div>

<!-- URLs. -->
[Começar com os Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/
[Começar com os dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet/
[Importe o arquivo publishsettings no Visual Studio 2013]: /pt-br/documentation/articles/mobile-services-windows-how-to-import-publishsettings/

