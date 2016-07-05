<properties
	pageTitle="Introdução aos Aplicativos Móveis usando o Xamarin.Forms"
	description="Siga este tutorial para começar a usar os Aplicativos Móveis do Azure para desenvolvimento do Xamarin.Forms"
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="05/06/2016"
	ms.author="glenga"/>

#Criar um aplicativo Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Visão geral

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo móvel Xamarin.Forms usando um back-end de Aplicativo Móvel do Azure. Você criará um novo back-end do Aplicativo Móvel e um aplicativo Xamarin.Forms simples _Todo list_ que armazena dados do aplicativo no Azure.

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais de Aplicativos Móveis para o Xamarin.Forms.

##Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio com Xamarin. Confira [Configuração e instalação para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) para obter instruções.

* Um Mac com Xcode v7.0 ou posterior e o Xamarin Studio Community instalados. Confira [Configuração e instalação para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [Configuração, instalação e verificações para usuários do Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).
 
>[AZURE.NOTE] Se você desejar uma introdução ao Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](https://tryappservice.azure.com/?appServiceName=mobile), onde poderá criar imediatamente um Aplicativo Móvel inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Criar um novo back-end de aplicativo móvel do Azure

Siga estas etapas para criar um novo back-end de aplicativo móvel.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


Você acabou de provisionar um back-end do aplicativo móvel do Azure que pode ser usado pelos aplicativos móveis clientes. Em seguida, você baixará um projeto do servidor para um back-end simples da "lista de tarefas" e o publicará no Azure.

## Configurar o projeto de servidor

Siga as etapas abaixo para configurar o projeto de servidor para usar o back-end Node.js ou .NET.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]


## (Opcional) Testar seu projeto de back-end localmente

Se você tiver escolhido uma configuração de back-end .NET acima, também poderá testar o back-end localmente.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-test-local-service](../../includes/app-service-mobile-dotnet-backend-test-local-service.md)]


##Baixar e executar a solução de Xamarin.Forms

Aqui, você tem algumas opções. Você pode baixar a solução em um Mac e abri-la no Xamarin Studio, ou pode baixar a solução para um computador com Windows e abri-la no Visual Studio usando um Mac em rede para compilar o aplicativo para iOS. Confira [Configuração e instalação do Visual Studio e do Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) se você precisa de instruções mais detalhadas sobre os cenários de instalação do Xamarin.

Vamos continuar:

 1. Em seu Mac ou em seu computador com o Windows, abra o [Portal do Azure] em uma janela do navegador.
 2. Na folha configurações do seu Aplicativo Móvel, clique em **Introdução** (em Celular) > **Xamarin.Forms**. Na etapa 3, clique em **Criar um novo aplicativo** se essa opção ainda não tiver sido selecionada. Em seguida, clique no botão **Baixar**.

    Isso baixa um projeto que contém uma aplicação de cliente que está conectada ao seu aplicativo móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.

 3. Extraia o projeto que você baixou e abra-o no Xamarin Studio ou no Visual Studio.

	![][9]

	![][8]


##(Opcional) Executar o projeto do iOS

Esta seção trata da execução do projeto do iOS Xamarin para dispositivos iOS. Você poderá ignorá-la se não estiver trabalhando com dispositivos iOS.

####No Xamarin Studio

1. Com o botão direito do projeto do iOS e, em seguida, clique em **Definir Como Projeto de Inicialização**.
2. No menu **Executar**, clique em **Iniciar Depuração** para compilar o projeto e iniciar o aplicativo no emulador do iPhone.

####No Visual Studio
1. Clique com o botão direito do mouse no projeto do iOS e clique em **Definir como Projeto de Inicialização**.
2. No menu **Compilar**, clique em **Gerenciador de Configurações**.
3. Na caixa de diálogo **Gerenciador de Configurações**, marque as caixas de seleção **Criar** e **Implantar** do projeto do iOS.
4. Pressione a tecla **F5** para compilar o projeto e iniciar o aplicativo no emulador do iPhone.

	>[AZURE.NOTE] Se você está com dificuldades na criação, execute o gerenciador de pacote NuGet e atualize para a versão mais recente dos pacotes de suporte a Xamarin. Às vezes, os projetos de Início Rápido podem atrasar sua atualização para a versão mais recente.

No aplicativo, digite um texto significativo, como _Saiba mais sobre o Xamarin_ e clique no botão **+**.

![][10]

Isso envia uma solicitação de POST para o novo back-end de aplicativo móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo back-end de aplicativo móvel, e os dados são exibidos na lista.

>[AZURE.NOTE]
Você encontrará o código que acessa o back-end do aplicativo móvel no arquivo C# TodoItemManager.cs do projeto de biblioteca de classes móvel da sua solução.

##(Opcional) Executar o projeto do Android

Esta seção trata da execução do projeto droid Xamarin para Android. Você poderá ignorá-la se não estiver trabalhando com dispositivos Android.

####No Xamarin Studio

1. Clique com o botão direito do mouse no projeto do Android e clique em **Definir como Projeto de Inicialização**.
2. No menu **Executar**, clique em **Iniciar Depuração** para compilar o projeto e iniciar o aplicativo no emulador do Android.

####No Visual Studio
1. Clique com o botão direito do mouse no projeto do Android (Droid) e clique em **Definir como Projeto de Inicialização**.
4. No menu **Compilar**, clique em **Gerenciador de Configurações**.
5. Na caixa de diálogo **Gerenciador de Configurações**, marque as caixas de seleção **Criar** e **Implantar** do projeto do Android.
6. Pressione a tecla **F5** para compilar o projeto e iniciar o aplicativo no emulador do Android.

	>[AZURE.NOTE] Se você está com dificuldades na criação, execute o gerenciador de pacote NuGet e atualize para a versão mais recente dos pacotes de suporte a Xamarin. Às vezes, os projetos de Início Rápido podem atrasar sua atualização para a versão mais recente.


No aplicativo, digite um texto significativo, como _Saiba mais sobre o Xamarin_ e clique no botão **+**.

![][11]

Isso envia uma solicitação de POST para o novo back-end de aplicativo móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo back-end de aplicativo móvel, e os dados são exibidos na lista.

> [AZURE.NOTE]
Você encontrará o código que acessa o back-end do aplicativo móvel no arquivo C# TodoItemManager.cs do projeto de biblioteca de classes móvel da sua solução.


##(Opcional) Executar o projeto do Windows


Esta seção trata da execução do projeto WinApp Xamarin para dispositivos Windows. Você poderá ignorá-la se não estiver trabalhando com dispositivos Windows.


####No Visual Studio
1. Clique com o botão direito do mouse em qualquer um dos projetos do Windows e clique em **Definir como Projeto de Inicialização**.
4. No menu **Compilar**, clique em **Gerenciador de Configurações**.
5. Na caixa de diálogo **Gerenciador de Configurações**, marque as caixas de seleção **Criar** e **Implantar** do projeto do Windows que você escolheu.
6. Pressione a tecla **F5** para compilar o projeto e iniciar o aplicativo no emulador do Windows.

	>[AZURE.NOTE] Se você está com dificuldades na criação, execute o gerenciador de pacote NuGet e atualize para a versão mais recente dos pacotes de suporte a Xamarin. Às vezes, os projetos de Início Rápido podem atrasar sua atualização para a versão mais recente.


No aplicativo, digite um texto significativo, como _Saiba mais sobre o Xamarin_ e clique no botão **+**.

Isso envia uma solicitação de POST para o novo back-end de aplicativo móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Itens armazenados na tabela são retornados pelo back-end do aplicativo móvel e os dados são exibidos na lista.

![][12]

> [AZURE.NOTE]
Você encontrará o código que acessa o back-end do aplicativo móvel no arquivo C# TodoItemManager.cs do projeto de biblioteca de classes móvel da sua solução.

##Próximas etapas

* [Adicionar autenticação ao seu aplicativo](app-service-mobile-xamarin-forms-get-started-users.md) Saiba como autenticar os usuários de seu aplicativo com um provedor de identidade.

* [Adicionar notificações por push ao aplicativo](app-service-mobile-xamarin-forms-get-started-push.md) Saiba como adicionar suporte a notificações por push ao aplicativo e configurar o back-end do Aplicativo Móvel para usar os Hubs de Notificação do Azure para enviar notificações por push.

* [Habilitar sincronização offline para seu aplicativo](app-service-mobile-xamarin-forms-get-started-offline-data.md) Saiba como adicionar suporte offline em seu aplicativo usando um back-end do aplicativo móvel. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede.

* [Como usar o cliente gerenciado para os Aplicativos Móveis do Azure](app-service-mobile-dotnet-how-to-use-client-library.md) Saiba como trabalhar com o SDK do cliente gerenciado em seu aplicativo Xamarin.


<!-- Anchors. -->
[Getting started with mobile app backends]: #getting-started
[Create a new mobile app backend]: #create-new-service
[Next Steps]: #next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal do Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_0629_2016-->