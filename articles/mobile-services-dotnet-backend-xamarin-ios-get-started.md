<properties 
	pageTitle="Introdução aos serviços móveis para aplicativos Xamarin iOS - Serviços Móveis do Azure" 
	description="Siga este tutorial para começar a usar os serviços móveis do Azure para desenvolvimento do iOS Xamarin" 
	services="" 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="donnam"/>

# <a name="getting-started"> </a>Introdução aos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Xamarin iOS usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples _To do list_ que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa as linguagens do .NET com suporte usando o Visual Studio para a lógica de negócios do lado do servidor e para gerenciar o serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript] neste tópico.

>[AZURE.NOTE]Este tópico mostra como criar um novo projeto de serviço móvel usando o Portal de Gerenciamento do Azure. Usando o Visual Studio 2013 Update 2, você também pode adicionar um novo projeto de serviço móvel em uma solução existente do Visual Studio. Para saber mais, confira [Início rápido: Adicionar um serviço móvel (back-end do .NET)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]


A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Serviços Móveis para aplicativos Xamarin iOS. 

>[AZURE.NOTE]Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-ios-get-started" target="_blank">Avaliação Gratuita do Azure</a>.<br />Este tutorial requer o <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Uma versão de avaliação gratuita está disponível.

## Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Criar um novo aplicativo Xamarin iOS

Depois de criar seu serviço móvel, você poderá seguir um guia de início rápido fácil no Portal de Gerenciamento para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel. 

Nesta seção você baixará um novo aplicativo Xamarin iOS e um projeto de serviço para seu serviço móvel.

1. No Portal de Gerenciamento, clique em **Serviços Móveis**e, em seguida, clique no serviço móvel que você acabou de criar.
   
2. Na guia de início rápido, clique em **Xamarin** em **Escolher plataforma** e expanda **Criar um novo aplicativo Xamarin**.

   	![][6]

   	Isso exibe as três etapas fáceis de criar um aplicativo Xamarin iOS conectado ao seu serviço móvel.

  	![][7]

3. Se você ainda não o fez, baixe e instale o <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a> no computador local ou na máquina virtual.  

4. Baixe e instale o [Xcode] v4.4 ou uma versão posterior e o [Xamarin Studio]. Você também pode usar Xamarin para Visual Studio.

5. Em **baixar e publicar seu serviço de nuvem**, selecione **iOS** e clique em **Baixar**. 

  	Isso baixa uma solução que contém projetos para o serviço móvel e para o aplicativo de exemplo _To do list_ que está conectado ao seu serviço móvel. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

6. Baixe seu perfil de publicação, salve o arquivo baixado no computador local e anote o local onde o salvou.

## Testar o serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicar o serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Executar o aplicativo Xamarin iOS

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o projeto cliente dentro da solução de serviço móvel, no Visual Studio ou Xamarin Studio.

	![][8]

	![][9]

2. Pressione o botão **Executar** para compilar o projeto cliente e iniciar o aplicativo no emulador do iPhone.

3. No aplicativo, digite um texto significativo, como _Complete the tutorial_ e, em seguida, clique no ícone de adição(**+**).

	![][10]

	Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

>[AZURE.NOTE]Você pode examinar o código que acessa seu serviço móvel para consultar e inserir dados no arquivo QSTodoService.cs C#.

    
## Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* [Introdução à sincronização de dados offline]
  <br/>Saiba como o quickstart utiliza a sincronização de dados offline para tornar o aplicativo robusto e responsivo.

* [Introdução à autenticação]
  <br/>Aprenda a autenticar usuários do aplicativo com um provedor de identidade.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

* [Solucionar um problema de back-end do .NET de Serviços Móveis]
  <br/> Saiba como diagnosticar e corrigir problemas que podem surgir com um back-end do .NET dos serviços móveis. 

<!-- Anchors. -->
[Introdução aos Serviços Móveis]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Próximas etapas]:#next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Introdução à sincronização de dados offline]: /pt-br/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[SDK dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript e HTML]: serviços móveis-javascript win8 /
[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Versão de back-end do JavaScript]: /pt-br/documentation/articles/partner-xamarin-mobile-services-ios-get-started
[Introdução aos dados nos Serviços Móveis usando o Visual Studio 2012]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-data-vs2012
[Solucionar um problema de back-end do .NET dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/


[Xamarin Studio]: http://xamarin.com/download
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532&clcid=0x409
[Xamarin para Windows]: https://go.microsoft.com/fwLink/?LinkID=330242&clcid=0x409


<!--HONumber=42-->
