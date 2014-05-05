<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Introdução ao Push (JS)" pageTitle="Introdução às notificações por push (JavaScript da Windows Store) | Centro de Desenvolvimento Móvel" metaKeywords="" description="Saiba como usar os Serviços Móveis do Azure para enviar notificações por push ao aplicativo JavaScript da Windows Store" metaCanonical="http://www.windowsazure.com/pt-br/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Introdução às notificações por push nos Serviços Móveis" authors="glenga" solutions="" manager="" editor="" />


# Introdução às notificações por push nos Serviços Móveis
<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="JavaScript da Windows Store" class="current">JavaScript da Windows Store</a><a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a><a href="/pt-br/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Back-end do .NET">Back-end do .NET</a> |  <a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push/"  title="Back-end do JavaScript" class="current">Back-end do JavaScript</a></div>		


Este tópico mostra como o Visual Studio 2013 permite que você use os Serviços Móveis do Azure para enviar notificações por push para o seu aplicativo da Windows Store. Neste tutorial, você adicionará notificações por push utilizando o serviço WNS (Notificação por Push do Windows) ao projeto de início rápido, diretamente do Visual Studio. Ao concluir, seu serviço móvel enviará uma notificação de push sempre que um registro é inserido.

>[WACOM.NOTE]Os Serviços Móveis agora integram-se aos Hubs de Notificação do Azure para oferecer suporte à funcionalidade de notificação por push, como modelos, várias plataformas e escala. Essa funcionalidade integrada está no modo de visualização. Para obter mais informações, consulte esta versão da [Introdução às notificações por push](/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/).

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo para notificações por push e configurar os Serviços Móveis]
2. [Atualizar o código de notificação por push gerado]
3. [Inserir dados para receber notificações]

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis] ou a [Introdução aos dados] para conectar seu projeto ao serviço móvel. Quando um serviço móvel não é conectado, o assistente Adicionar Notificação por Push cria essa conexão para você. 

<h2><a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Adicionar e configurar as notificações por push no aplicativo</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Abra o arquivo de código gerado push.register.js e inspecione o código que obtém a ID de instalação e o canal para o dispositivo e insere esses dados na nova tabela de <strong>canais</strong>.</p> 

	<p>Esta tabela foi criada no serviço móvel pelo assistente Adicionar Notificação por Push. Este código garante a tentativa do registro do dispositivo sempre que o aplicativo for ativado.</p></li>
<li><p>No Gerenciador de Servidores, expanda <strong>Azure</strong>, <strong>Serviços Móveis</strong>, o nome do seu serviço e <strong>canais</strong> e abra o arquivo insert.js.</p> 

<p>Esse arquivo, que está armazenado no seu serviço móvel, contém o código JavaScript que é executado quando um cliente envia uma solicitação para registrar um dispositivo, inserindo dados na tabela de canais.</p> 

<div class="dev-callout"><b>Observação</b>
	<p>A versão inicial desse arquivo contém o código que verifica a existência de um registro existente para o dispositivo. Ela também contém o código que envia uma notificação por push quando um novo registro é adicionado à tabela de canais. O código que envia uma notificação por push pode ser incluído em qualquer arquivo de script registrado. O local desse script depende de como a notificação é disparada. Os scripts podem ser registrados em relação a uma operação de inserção, atualização, exclusão ou leitura em uma tabela; como um trabalho agendado; ou como uma API personalizada. Para obter mais informações, consulte <a href="http://go.microsoft.com/fwlink/p/?LinkID=287178">Trabalhar com scripts de servidor em Serviços Móveis</a>.</p>
</div>
</li> 
<li><p>Pressione a tecla F5 para executar o aplicativo e verificar se uma notificação é recebida imediatamente do serviço móvel.</p>
<p>Essa notificação foi gerada inserindo uma linha na nova tabela de canais, que é o registro do dispositivo.</p>
</li>
</ol>

Enquanto o código gerado facilita a demonstração de uma notificação quando o aplicativo é executado, geralmente, isso não é um cenário significativo. Em seguida, você removerá o código de notificação da tabela de canais e substituirá esse código, com algumas alterações, na tabela TodoItem. 

<h2><a name="update-scripts"></a><span class="short-header">Atualizar o código</span>Atualizar o código de notificação por push gerado</h2>

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a><span class="short-header">Testar o aplicativo</span>Testar notificações por push em seu aplicativo</h2>

1. No Visual Studio, pressione F5 para executar o aplicativo.

2. No aplicativo, digite um texto em **Inserir um TodoItem** e clique em **Salvar**.

   	![][13]

   	Observe que, após a inserção, o aplicativo receberá uma notificação por push do WNS.

   	![][14]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstra recurso básico de notificação por push fornecido pelos Serviços Móveis. Se seu aplicativo requer recursos mais avançados, como o envio de notificações em várias plataformas, roteamento baseado em assinatura ou volumes muito grandes, convém usar os Hubs de Notificação do Azure com seu serviço móvel. Para obter mais informações, consulte um dos seguintes tópicos de Hubs de Notificação:

+ [Introdução aos Hubs de Notificação]
  <br/>Saiba como aproveitar os Hubs de Notificação no seu aplicativo da Windows Store.

+ [Enviar notificações para assinantes]
	<br/>Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

+ [Enviar notificações para usuários]
	<br/>Saiba como enviar as notificações por push de um Serviço Móvel para usuários específicos em qualquer dispositivo.

+ [Enviar notificações de várias plataformas para usuários]
	<br/>Saiba usar modelos para enviar notificações por push de um Serviço Móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.

Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.

* [Referência conceitual de tutorial do HTML/JavaScript de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com HTML e JavaScript.  

<!-- Anchors. -->
[Registrar seu aplicativo para notificações por push e configurar os Serviços Móveis]: #register
[Atualizar o código de notificação por push gerado]: #update-scripts
[Inserir dados para receber notificações]: #test
[Próximas etapas]:#next-steps

<!-- Images. -->







[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[Enviar uma página de aplicativo:]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-js/
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-js
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-js
[Notificações por push para usuários de aplicativos]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-js
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-push-js

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual de tutorial do HTML/JavaScript de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client/
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/getting-started-windows-dotnet/
[O que são Hubs de Notificação?]: /pt-br/develop/net/how-to-guides/service-bus-notification-hubs/
[Enviar notificações para assinantes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet/
[Enviar notificações para usuários]: /pt-br/manage/services/notification-hubs/notify-users/
[Enviar notificações de várias plataformas para usuários]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/

