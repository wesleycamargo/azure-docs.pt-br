<properties 
	pageTitle="Introdução às notificações por push (push herdado) | Centro de desenvolvimento dos Serviços Móveis" 
	description="Saiba como usar serviços móveis do Azure para enviar notificações por push para seu aplicativo JavaScript da Windows Store (push herdado)." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="glenga"/>

# Adicionar notificações por push ao seu aplicativo de Serviços Móveis (push herdado)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">C# da Windows Store</a>
    <a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript" class="current">JavaScript da Windows Store</a>
    <a href="/pt-br/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/pt-br/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/pt-br/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/pt-br/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/pt-br/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title=".NET backend">Back-end .NET</a> | <a href="/pt-br/documentation/articles/mobile-services-windows-store-javascript-get-started-push/"  title="JavaScript backend" class="current">Back-end JavaScript</a></div>		

Este tópico mostra como o Visual Studio 2013 permite que você use os Serviços Móveis do Azure para enviar notificações por push para o seu aplicativo da Windows Store. Neste tutorial, você adicionará notificações por push utilizando o serviço WNS (Notificação por Push do Windows) ao projeto de início rápido, diretamente do Visual Studio. Ao concluir, seu serviço móvel enviará uma notificação por push sempre que um registro for inserido.

>[AZURE.NOTE]Este tópico oferece suporte a serviços móveis <em>existentes</em> que ainda <em>não foram atualizados</em> para usar a integração de Hubs de Notificação. Quando você cria um <em>novo</em> serviço móvel, essa funcionalidade integrada é habilitada automaticamente. Para novos serviços móveis, consulte [Introdução às notificações por push](/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/).
>
>Os Serviços Móveis integram-se aos Hubs de Notificação do Azure para dar suporte à funcionalidade de notificação por push, como modelos, várias plataformas e escala aprimorada. <em>Você deve atualizar seus serviços móveis existentes para usar os Hubs de Notificação quando possível</em>. Depois de atualizar, consulte esta versão do [Introdução às notificações por push](/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push/).

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo para notificações por push e configurar os serviços móveis]
2. [Atualizar o código de notificação por push gerado]
3. [Inserir dados para receber notificações]

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis] ou a [Introdução aos dados] para conectar seu projeto ao serviço móvel. Quando um serviço móvel não é conectado, o assistente Adicionar Notificação por Push cria essa conexão para você. 

<h2><a name="register"></a>Adicionar e configurar notificações por push no aplicativo</h2>

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Abra o arquivo de código gerado push.register.js e inspecione o código que obtém a ID de instalação e o canal para o dispositivo e insere esses dados na nova tabela <strong>canais</strong>.</p> 

	<p>Esta tabela foi criada no serviço móvel pelo assistente Adicionar Notificação por Push. Este código garante a tentativa do registro do dispositivo sempre que o aplicativo for ativado.</p></li>
<li><p>No Gerenciador de Servidores, expanda <strong>Azure</strong>, <strong>Serviços Móveis</strong>, o nome do seu serviço e <strong>canais</strong>, então abra o arquivo insert.js.</p> 

<p>Esse arquivo, que está armazenado no seu serviço móvel, contém o código JavaScript que é executado quando um cliente envia uma solicitação para registrar um dispositivo, inserindo dados na tabela de canais.</p> 

> [AZURE.NOTE] A versão inicial desse arquivo contém o código que verifica a existência de um registro existente para o dispositivo. Ela também contém o código que envia uma notificação por push quando um novo registro é adicionado à tabela de canais. O código que envia uma notificação por push pode ser incluído em qualquer arquivo de script registrado. O local desse script depende de como a notificação é disparada. Os scripts podem ser registrados em relação a uma operação de inserção, atualização, exclusão ou leitura em uma tabela; como um trabalho agendado; ou como uma API personalizada. Para obter mais informações, consulte [Trabalhar com scripts de servidor em Serviços Móveis](http://go.microsoft.com/fwlink/p/?LinkID=287178).
</li> 
<li><p>Pressione a tecla F5 para executar o aplicativo e verificar se uma notificação é recebida imediatamente do serviço móvel.</p>
<p>Essa notificação foi gerada inserindo uma linha na nova tabela de canais, que é o registro do dispositivo.</p>
</li>
</ol>

Enquanto o código gerado facilita a demonstração de uma notificação quando o aplicativo é executado, geralmente, isso não é um cenário significativo. Em seguida, você removerá o código de notificação da tabela de canais e substituirá esse código, com algumas alterações, na tabela TodoItem. 

<h2><a name="update-scripts"></a>Atualizar o código de notificação por push gerado</h2>

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013-2](../includes/mobile-services-create-new-push-vs2013-2.md)]

<h2><a name="test"></a>Testar notificações por push em seu aplicativo</h2>

1. No Visual Studio, pressione F5 para executar o aplicativo.

2. No aplicativo, digite um texto em **Inserir um TodoItem** e, em seguida, clique em **Salvar**.

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

+ [Enviar notificações entre plataformas para usuários]
	<br/>Saiba usar modelos para enviar notificações por push de um Serviço Móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.

Considere a possibilidade de obter mais informações sobre os seguintes tópicos de Serviços Móveis:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários do seu aplicativo com uma conta do Windows.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como registrar e usar scripts de servidor.

* [Referência conceitual do tutorial HTML/JavaScript de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com HTML e JavaScript.  

<!-- Anchors. -->
[Registrar seu aplicativo para notificações por push e configurar os serviços móveis]: #register
[Atualizar o código de notificação por push gerado]: #update-scripts
[Inserir dados para receber notificações]: #test
[Próximas etapas]:#next-steps

<!-- Images. -->







[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png




<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-js/
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-js
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-js
[Notificações por push para usuários do aplicativo]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-js
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-push-js

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial HTML/JavaScript de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-html-js-client/
[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Introdução aos Hubs de Notificação]: /pt-br/manage/services/notification-hubs/getting-started-windows-dotnet/
[O que são Hubs de Notificação?]: /pt-br/develop/net/how-to-guides/service-bus-notification-hubs/
[Enviar notificações para assinantes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet/
[Enviar notificações para usuários]: /pt-br/manage/services/notification-hubs/notify-users/
[Enviar notificações entre plataformas para usuários]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/

\<!--HONumber=42-->
