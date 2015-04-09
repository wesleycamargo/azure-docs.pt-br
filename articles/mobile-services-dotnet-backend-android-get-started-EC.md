<properties 
	pageTitle="Introdução aos Serviços Móveis do Azure para aplicativos Android" 
	description="Siga este tutorial para começar a usar os serviços móveis do Azure para desenvolvimento Android." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="02/13/2015" 
	ms.author="ricksal"/>

# <a name="getting-started"></a>Introdução aos Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started-EC.md)]

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Android usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples _To do list_ que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa as linguagens do .NET com suporte usando o Visual Studio para a lógica de negócios do lado do servidor e para gerenciar o serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript](/documentation/articles/mobile-services-android-get-started-EC/) neste tópico.

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial requer as [Ferramentas para desenvolvedores do Android][SDK para Android], que inclui o IDE (Ambiente de Desenvolvimento Eclipse)integrado, o plug-in do ADT (Android Developer Tools) e a mais recente plataforma Android. Android 4.2 ou uma versão posterior é necessária. 

O projeto de início rápido baixado contém o SDK de Serviços Móveis para Android. Enquanto este projeto requer Android 4.2 ou uma versão posterior, o SDK dos Serviços Móveis exige apenas Android 2.2 ou uma versão posterior.

> [AZURE.IMPORTANT] Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28"%20target="_blank).

## <a name="create-new-service"></a>Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Baixar o serviço móvel para o computador local

Agora que você criou o serviço móvel, baixe o projeto de serviço móvel personalizado que pode ser executado no computador local ou na máquina virtual.

1. Clique no serviço móvel que você acabou de criar, em seguida, na guia quickstart, clique em **Android** em **Escolher plataforma** e expanda **Criar um novo aplicativo Android**.

	![][1]  

2. Se ainda não tiver feito isso, baixe e instale o [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934), ou uma versão posterior.

3. Clique em **Baixar** em **Baixar e publicar o serviço na nuvem**.

	Isso baixa o projeto do Visual Studio que implementa o serviço móvel. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

4. Além disso, baixe seu perfil de publicação, salve o arquivo baixado no computador local e anote o local onde o salvou.

## Testar o serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicar o serviço móvel

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## Criar novo aplicativo Android

Nesta seção você criará um novo aplicativo Android que está conectado ao seu serviço móvel.

1. No [Portal de Gerenciamento], clique em **Serviços Móveis**, e clique no serviço móvel que você acabou de criar.

2. Na guia de início rápido, clique em **Android** em **Escolher a plataforma** e expanda **Criar um novo aplicativo Android**. 
 
	![][2]  

3. Se ainda não fez isso, baixe e instale o [Android Developer Tools][SDK para Android] em seu computador local ou máquina virtual.

4. Em **Baixar e executar o aplicativo**, clique em **Baixar**. 

  	Isso baixa o projeto para o aplicativo de exemplo _To do list_ que está conectado ao seu serviço móvel. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu aplicativo Android

O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o local onde você salvou os arquivos do projeto compactados e expanda os arquivos em seu computador.

2. No Eclipse, clique em **Arquivo** depois em **Importar**, expanda **Android**, clique em **Código Android existente no espaço de trabalho**, e clique em **Avançar.** 

 	![][14]

3. Clique em **Procurar**, navegue até o local dos arquivos do projeto expandidos, clique em **OK**, verifique se o projeto TodoActivity está marcado e clique em **Concluir**. 

 	![][15]

	Isso importa os arquivos de projeto no espaço de trabalho atual.

   	![][8]

4. No menu **Executar**, clique em **Executar** para iniciar o projeto no emulador do Android.

	> [AZURE.IMPORTANT] Para que seja possível executar o projeto no emulador do Android, você deve definir no mínimo um AVD (Dispositivo Virtual Android). Use o Gerenciador do AVD para criar e gerenciar esses dispositivos.

5. No aplicativo, digite um texto significativo, como _Complete the tutorial_ e clique em **Adicionar**.

   	![][10]

   	Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.

	> [AZURE.NOTE] Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados, que estão localizados no arquivo ToDoActivity.java.

<!--This shows how to run your new client app against the mobile service running in Azure. Before you can test the Android app with the mobile service running on a local computer, you must configure the Web server and firewall to allow access from your Android development computer. For more information, see [Configure the local web server to allow connections to a local mobile service](/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).-->

## <a name="next-steps"> </a>Próximas Etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis: 

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação]
  <br/>Aprenda a autenticar usuários do aplicativo com um provedor de identidade.

* [Introdução às notificações por push] 
  <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

* [Solucionar um problema de back-end do .NET de Serviços Móveis]
  <br/> Saiba como diagnosticar e corrigir problemas que podem surgir com um back-end do .NET dos serviços móveis. 

<!-- Anchors. -->
[Introdução aos Serviços Móveis]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-completed-android.png
[1]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-vs-EC.png
[2]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android-EC.png


[6]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-portal-quickstart-android.png
[7]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-steps-android.png
[8]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-eclipse-quickstart.png

[10]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-quickstart-startup-android.png
[11]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-data-browse.png

[14]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-dotnet-backend-android-get-started/mobile-services-import-android-project.png

<!-- URLs. -->
[Introdução aos dados]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-data
[Introdução à autenticação]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-users
[Introdução às notificações por push]: /documentation/articles/mobile-services-dotnet-backend-android-get-started-push
[SDK para Android]: https://go.microsoft.com/fwLink/p/?LinkID=280125
[SDK para Android de Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Solucionar um problema de back-end do .NET de Serviços Móveis]: /documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/

[Portal de Gerenciamento]: https://manage.windowsazure.com/

<!--HONumber=47-->
