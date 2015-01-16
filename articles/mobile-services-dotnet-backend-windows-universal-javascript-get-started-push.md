<properties pageTitle="Introdução à notificação por push usando um serviço móvel de back-end do .NET" metaKeywords="" description="Saiba como usar os serviços móveis do Azure e Hubs de notificação para enviar notificações por push para seu aplicativo Windows universal." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />


# Adicionar notificações por push a seu aplicativo de Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do .NET para enviar notificações por push a um aplicativo do Windows universal. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure em um projeto do aplicativo Windows universal. Ao concluir, seu serviço móvel enviará uma notificação por push do back-end do .NET para todos os aplicativos do Windows Store e Windows Phone Store sempre que um registro é inserido na tabela TodoList. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

>[WACOM.NOTE]Esse tópico lhe mostra como usar as ferramentas no Visual Studio Professional 2013 com atualização 3 para adicionar suporte para notificações por push dos Serviços Móveis para um aplicativo Windows universal. As mesmas etapas podem ser usadas para adicionar notificações por push dos Serviços Móveis para um aplicativo da Windows Store ou Windows Phone Store 8.1. Se você não pode atualizar para o Visual Studio Professional 2013 Update 3 ou prefere adicionar manualmente seu projeto de serviço móvel a uma solução de aplicativo da Windows Store, veja [esta versão](/it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push) do tópico.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo para notificações por push](#register)
2. [Atualizar o serviço para enviar notificações por push](#update-service)
3. [Habilitar notificações por push para teste local](#local-testing)
4. [Testar notificações por push no seu aplicativo](#test)

Para concluir este tutorial, você precisará do seguinte:

* Uma [conta da Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) ativa.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Atualização 3 ou versão posterior). <br/>Uma versão de avaliação gratuita está disponível. 

##<a id="register"></a>Registrar seu aplicativo para notificações por push

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Navegue para a pasta <code>\services\mobileServices\scripts</code> do projeto, copie o arquivo de script &lt;<em>nome_do_seu_serviço</em>&gt;.push.register.js gerado na pasta compartilhada <code>\js</code> e, em seguida, exclua este arquivo tanto dos projetos de aplicativos individuais para Windows quanto dos projetos de aplicativos para Windows Phone.</p></li> 
<li><p>Abra esse arquivo de script na pasta compartilhada <code>\js</code> do projeto, localize o código no ouvinte de eventos <em>activated</em>, que registra o canal URL do dispositivo com o hub de notificação e exclua a função de comprometimento <strong>done</strong>.</p>
<p>Esse tutorial envia notificações quando um novo item é inserido, não quando um API personalizado é chamado.</p></li>
<li><p>No projeto do aplicativo Windows, abra o arquivo default.html e mude o caminho da referência do arquivo de script para a pasta compartilhada <code>\js</code> do projeto, para que se pareça com o seguinte:</p><pre><code>&lt;script src="/js/your_service_name.push.register.js"&gt;&lt;/script&gt;</code></pre></li>
<li><p>Repita essa etapa para o projeto do aplicativo WindowsPhone.</p>
<p>Agora, ambos os projetos estão usando uma versão compartilhada do script de registro em push.</p></li>
</ol>

Agora que as notificações por push estão ativadas no aplicativo, você deve atualizar o serviço móvel para enviar notificações por push. 

##<a id="update-service"></a>Atualizar o serviço para enviar notificações por push

As seguintes etapas atualizam a classe TodoItemController existente para enviar uma notificação por push para todos os dispositivos registrados quando um novo item é inserido. Você pode implementar código semelhante em qualquer [ApiController] ou [TableController] personalizado, ou em qualquer lugar nos seus serviços de back-end. 

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Habilitar notificações por push para teste local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

As etapas restantes nesta seção são opcionais. Elas permitem que você teste seu aplicativo em relação ao seu serviço móvel em execução em um computador local. Se você planeja testar as notificações por push usando o serviço móvel em execução no Azure, você pode apenas pular para a última seção. Isso ocorre porque o assistente Adicionar Notificação por Push já configurou seu aplicativo para se conectar ao seu serviço em execução no Azure.  

>[WACOM.NOTE]Nunca use um serviço móvel de produção para testar e desenvolver o trabalho. Sempre publique seu projeto de serviço móvel para um serviço de preparação para testes.

<ol start="5">
<li><p>Navegue para a pasta <code>\services\mobileServices\settings</code> do projeto, copie o arquivo de script &lt;<em>nome_do_seu_serviço</em>&gt;.js gerado na pasta compartilhada <code>\js</code> do projeto e, em seguida, exclua este arquivo tanto dos projetos de aplicativos individuais para Windows quanto dos projetos de aplicativos para Windows Phone. Também exclua este arquivo da pasta <code>\services\mobileServices\scripts</code> em cada projeto de aplicativo, se o arquivo existir lá também.</p></li> 
<li><p>Abra este arquivo de script na pasta de projeto compartilhada <code>\js</code> e comente o código existente que define o <a href="http://msdn.microsoft.com/it-it/library/azure/jj554219.aspx">objeto MobileServiceClient</a> usado para acessar o serviço móvel em execução no Azure.</p></li>
<li><p>Adicione uma nova definição de objeto <strong>MobileServiceClient</strong> com o mesmo nome, mas usando a URL do host local no construtor, semelhante à seguinte:</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
var todolistClient = new WindowsAzure.MobileServiceClient(
	"http://localhost:4584");
</code></pre><p>Usando este objeto <strong>MobileServiceClient</strong>, o aplicativo conectará ao serviço local ao invés da versão hospedada no Azure. Quando quiser voltar e executar o aplicativo no serviço móvel hospedado no Azure, mude de volta para as definições de objeto <strong>MobileServiceClient</strong> originais.</p></li>
</ol>

##<a id="test"></a> Testar notificações por push no seu aplicativo

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação dos aplicativos da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o tutorial seguinte, [Enviar notificações por push para usuários autenticados], que permite mostrar como usar marcas para enviar notificações por push de um Serviço Móvel para um único usuário autenticado.

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os serviços móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários de seu aplicativo com diferentes tipos de conta usando os serviços móveis.

* [O que são Hubs de Notificação?]
  <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depurar aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtenha diretrizes de solução de problemas e depuração de soluções de Hubs de Notificação. 

* [Como usar um cliente HTML/JavaScript para os Serviços Móveis do Azure]
  <br/>Saiba mais sobre como usar os Serviços Móveis em aplicativos HTML e JavaScript.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Enviar uma página de aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[SDK do Live para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[Introdução aos dados]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
[Introdução à autenticação]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-users

[Enviar notificações por push para usuários autenticados]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[Quais são os Hubs de notificação?]: /it-it/documentation/articles/notification-hubs-overview/

[Como usar um cliente HTML/JavaScript para Serviços Móveis do Azure]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library
