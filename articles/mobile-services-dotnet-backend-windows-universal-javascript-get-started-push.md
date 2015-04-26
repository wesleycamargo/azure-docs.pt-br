<properties 
	pageTitle="Introdução à notificação por push usando um serviço móvel de back-end do .NET" 
	description="Saiba como usar os serviços móveis do Azure e Hubs de notificação para enviar notificações por push para seu aplicativo Windows universal." 
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
	ms.date="09/27/2014" 
	ms.author="glenga"/>


# Adicionar notificações por push ao seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do .NET para enviar notificações por push a um aplicativo do Windows universal. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure em um projeto do aplicativo Windows universal. Ao concluir, seu serviço móvel enviará uma notificação por push do back-end do .NET para todos os aplicativos do Windows Store e Windows Phone Store sempre que um registro é inserido na tabela TodoList. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

>[AZURE.NOTE]Esse tópico lhe mostra como usar as ferramentas no Visual Studio Professional 2013 com atualização 3 para adicionar suporte para notificações por push dos Serviços Móveis para um aplicativo Windows universal. As mesmas etapas podem ser usadas para adicionar notificações por push dos Serviços Móveis para um aplicativo da Windows Store ou Windows Phone Store 8.1. Se você não pode atualizar para o Visual Studio Professional 2013 Update 3 ou prefere adicionar manualmente seu projeto de serviço móvel a uma solução de aplicativo da Windows Store, veja [esta versão](/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push) do tópico.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo para notificações por push](#register)
2. [Atualizar o serviço para enviar notificações por push](#update-service)
3. [Habilitar notificações por push para teste local](#local-testing)
4. [Testar notificações de push no seu aplicativo](#test)

Para concluir este tutorial, você precisará do seguinte:

* Uma [conta da Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) ativa.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Atualização 3 ou versão posterior). <br/>Uma versão de avaliação gratuita está disponível. 

##<a id="register"></a>Registrar seu aplicativo para notificações por push

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Navegue até a <code>\services\mobileServices\scripts</code> pasta do projeto, copie o arquivo de script &lt;<em>your_service_name</em>&gt;.push.register.js gerado na pasta compartilhada <code>\js</code> e, em seguida, exclua esse arquivo de ambos os projetos de aplicativo individuais do Windows e WindowsPhone.</p></li> 
<li><p>Abra o arquivo de script na pasta de projeto compartilhada <code>\js</code> localize o código no ouvinte de eventos <em>ativado</em> que registra a URL de canal do dispositivo com o hub de notificação e exclua a função de promessa <strong>concluída</strong>.</p>
<p>Esse tutorial envia notificações quando um novo item é inserido, não quando um API personalizado é chamado.</p></li>
<li><p>No projeto de aplicativo do Windows, abra o arquivo default.html e altere o caminho de referência do arquivo de script para a pasta compartilhada <code>\js</code> do projeto, para que se pareça com o seguinte:</p><pre><code>&lt;script src="/js/your_service_name.push.register.js"&gt;&lt;/script&gt;</code></pre></li>
<li><p>Repita essa etapa para o projeto do aplicativo WindowsPhone.</p>
<p>Agora, ambos os projetos estão usando uma versão compartilhada do script de registro em push.</p></li>
</ol>

Agora que as notificações por push estão habilitadas no aplicativo, você deve atualizar o serviço móvel para enviar notificações por push. 

##<a id="update-service"></a>Atualizar o serviço para enviar notificações por push

As seguintes etapas atualizam a classe TodoItemController existente para enviar uma notificação por push para todos os dispositivos registrados quando um novo item é inserido. Você pode implementar um código semelhante em qualquer [ApiController], [TableController] personalizado ou em qualquer outro lugar em seus serviços de back-end. 

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Habilitar notificações por push para testes locais

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

As etapas restantes nesta seção são opcionais. Elas permitem que você teste seu aplicativo em relação ao seu serviço móvel em execução em um computador local. Se você planeja testar as notificações por push usando o serviço móvel em execução no Azure, você pode apenas pular para a última seção. Isso ocorre porque o assistente Adicionar Notificação por Push já configurou seu aplicativo para se conectar ao seu serviço em execução no Azure.  

>[AZURE.NOTE]Nunca use um serviço móvel de produção para testar e desenvolver o trabalho. Sempre publique seu projeto de serviço móvel para um serviço de preparação para testes.

<ol start="5">
<li><p>Navegue até a <code>\services\mobileServices\settings</code> pasta do projeto, copie o arquivo de script &lt;<em>your_service_name</em>&gt;.js gerado na pasta compartilhada <code>\js</code> do projeto e, em seguida, exclua esse arquivo de ambos os projetos individuais de aplicativo do Windows e WindowsPhone. Exclua também este arquivo da <code>\services\mobileServices\scripts</code> pasta em cada projeto de aplicativo se ela existir lá também.</p></li> 
<li><p>Abra o arquivo de script na pasta de projeto compartilhada <code>\js</code> e comente o código existente que define o <a href="http://msdn.microsoft.com/library/azure/jj554219.aspx">MobileServiceClient object</a> usado para acessar o serviço móvel em execução no Azure.</p></li>
<li><p>Adicione uma nova definição de objeto <strong>MobileServiceClient</strong> com o mesmo nome, mas usando a URL do host local no construtor, semelhante à seguinte:</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
var todolistClient = new WindowsAzure.MobileServiceClient(
	"http://localhost:4584");
</code></pre><p>Usando este objeto <strong>MobileServiceClient</strong>, o aplicativo conectará ao serviço local ao invés da versão hospedada no Azure. Quando quiser voltar e executar o aplicativo no serviço móvel hospedado no Azure, mude de volta para as definições de objeto <strong>MobileServiceClient</strong> originais.</p></li>
</ol>

##<a id="test"></a> Testar notificações por push no seu aplicativo

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação dos aplicativos da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o tutorial seguinte [Enviar notificações por push para usuários autenticados], , que permite mostrar como usar marcas para enviar notificações por push de um Serviço Móvel para um único usuário autenticado.

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os serviços móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários de seu aplicativo com diferentes tipos de conta usando serviços móveis.

* [O que são Hubs de Notificação?]
  <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depurar aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obter orientação ao solucionar problemas ou ao depurar soluções de hubs de notificação. 

* [Como usar um cliente HTML/JavaScript para Serviços Móveis do Azure]
  <br/>Saiba mais sobre como usar os Serviços Móveis em aplicativos HTML e JavaScript.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-users

[Enviar notificações por push para usuários autenticados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[O que são Hubs de Notificação?]: /pt-br/documentation/articles/notification-hubs-overview/

[Como usar um cliente HTML/JavaScript para os Serviços Móveis do Azure]: /pt-br/documentation/articles/mobile-services-html-how-to-use-client-library


<!--HONumber=42-->
