<properties 
	pageTitle="Introdução aos Serviços Móveis para aplicativos iOS do Xamarin" 
	description="Siga este tutorial para começar a usar os serviços móveis do Azure para desenvolvimento do Xamarin iOS." 
	services="mobile-services" 
	documentationCenter="xamarin" 
	authors="conceptdev" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="11/22/2014" 
	ms.author="craig.dunn@xamarin.com"/>

# <a name="getting-started"></a>Introdução aos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Xamarin.iOS usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples <em>To do list</em> que armazena dados do aplicativo no novo serviço móvel.

Se você preferir assistir a um vídeo, o clipe abaixo segue as mesmas etapas deste tutorial.

Vídeo: "Introdução aos Serviços Móveis do Xamarin e do Azure" com Craig Dunn, desenvolvedor evangelista para Xamarin (duração: 10h:05m)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]



Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial requer o XCode e o [Xamarin Studio] para OS X ou o plug-in Xamarin Visual Studio para Visual Studio no Windows. A amostra será executada no iOS 5.0 e mais recentes.

> [AZURE.IMPORTANT] Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F"%20target="_blank).

## <a name="create-new-service"></a>Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>Criar um noco aplicativo Xamarin.iOS</h2>

Depois de criar seu serviço móvel, você poderá seguir um quickstart fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel. 

Nesta seção você criará um novo aplicativo Xamarin.iOS que está conectado ao seu serviço móvel.

1.  No Portal de Gerenciamento, clique em **Serviços Móveise**, em seguida, clique no serviço móvel que você acabou de criar.

2. Na guia de início rápido, clique em **Xamarin.iOS** em **Escolher Plataforma** e expanda **Criar um novo aplicativo Xamarin.iOS**.

	![][6]

	Isso exibe as três etapas fáceis de criar um aplicativo Xamarin.iOS conectado a seu serviço móvel.

  	![][7]

3. Se ainda não tiver feito isso, baixe e instale o Xcode (recomendamos a versão mais recente, Xcode 6.0, ou mais recente) e o[Xamarin Studio].

4. Clique em **Criar tabela TodoItems** para criar uma tabela para armazenar dados do aplicativo.

5. Em **Baixe e execute o aplicativo**, clique em **Baixar**. 

	Isso baixa o projeto para o aplicativo _To do list_ que está conectado a seu serviço móvel e faz referência ao componente dos Serviços Móveis do Azure para Xamarin.iOS. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

<h2>Executar seu novo aplicativo Xamarin.iOS</h2>

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o local onde você salvou os arquivos compactados do projeto, expanda os arquivos em seu computador e abra o arquivo da solução **XamarinTodoQuickStart.iOS.sln** usando o Xamarin Studio ou o Visual Studio.

	![][8]

	![][9]

2. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo no emulador do iPhone, que é o padrão para este projeto.

3. No aplicativo, digite um texto significativo, como Concluir o tutorial e clique no ícone de adição (**+**).

	![][10]

	Isso envia uma solicitação de POST ao novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

	> [AZURE.NOTE] Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados que estão localizados no arquivo TodoService.cs C#.

4. No Portal de Gerenciamento, clique na guia **Dados** e, em seguida na tabela **TodoItems**.

	![][11]

	Isso permite que você procure os dados inseridos pelo aplicativo na tabela.

	![][12]

## <a name="next-steps"></a>Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

## Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* [Introdução à sincronização de dados offline]
  <br/>Saiba como o quickstart utiliza a sincronização de dados offline para tornar o aplicativo robusto e responsivo.

* [Introdução à autenticação]
  <br/>Aprenda a autenticar usuários do aplicativo com um provedor de identidade.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

<!-- Anchors. -->
[Introdução aos Serviços Móveis]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Introdução à sincronização de dados offline]: /pt-br/develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Xamarin Studio]: http://xamarin.com/download
[SDK do iOS dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[Portal de Gerenciamento]: https://manage.windowsazure.com/


<!--HONumber=42-->
