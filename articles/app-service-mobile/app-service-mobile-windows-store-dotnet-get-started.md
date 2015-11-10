<properties
	pageTitle="Criar um aplicativo universal do Tempo de Execução do Windows 8.1 nos aplicativos móveis do Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Siga este tutorial para começar a usar os back-ends de aplicativo móvel do Azure para desenvolvimento da Windows Store em C#, no Visual Basic ou em JavaScript."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="08/14/2015"
	ms.author="glenga"/>

#Criar um aplicativo do Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Visão geral

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo universal do Windows Runtime 8.1 usando um back-end de aplicativo móvel do Azure. As soluções de aplicativos universais do Windows incluem projetos para a Windows Store 8.1 e a Loja do Windows Phone 8.1, além de um projeto compartilhado em comum.

[AZURE.INCLUDE [app-service-mobile-windows-universal-get-started](../../includes/app-service-mobile-windows-universal-get-started.md)]

##Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] ou uma versão posterior.

>[AZURE.NOTE]Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile). Lá, você pode criar imediatamente um aplicativo móvel de curta duração inicial no Serviço de Aplicativo – sem cartão de crédito e sem compromissos.

##Criar um novo back-end de aplicativo móvel do Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Baixar o projeto de servidor

1. No [portal do Azure], clique em**Procurar Tudo** > **Aplicativos Web** e clique no back-end de aplicativo móvel que você acabou de criar.

2. No back-end de aplicativo móvel, clique em **Todas as Configurações** e, em **Aplicativo Móvel**, clique em **Início rápido** > **Windows (C#)**.

3. Em **Baixe e execute seu projeto de servidor** na **Criar um novo aplicativo**, clique em **Baixar**, extraia os arquivos compactados do projeto em seu computador local e abra a solução no Visual Studio.

4. Compile o projeto para restaurar os pacotes do NuGet.

##Publicar o projeto de servidor no Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##Baixe e execute o projeto do cliente

Depois de criar seu back-end de aplicativo móvel, você poderá seguir um Início Rápido fácil no portal do Azure para criar um novo aplicativo ou modificar um aplicativo existente para conectar-se ao back-end de aplicativo móvel.

Nesta seção, você baixa um projeto de modelo de aplicativo Windows universal personalizado para se conectar ao back-end do aplicativo móvel do Azure.

1. De volta à folha do back-end do Aplicativo Móvel, clique em **Todas as configurações** e, em **Aplicativo Móvel**, clique em **Início rápido** > **Windows (C#)**.

2.  Em **Baixar e executar seu projeto do Windows** na **Criar um novo aplicativo**, clique em **Baixar** e extraia os arquivos compactados do projeto para seu computador local.

3. (Opcional) Adicione o projeto de aplicativo Windows universal à solução com o projeto do servidor. Isso torna mais fácil depurar e testar o aplicativo e o back-end na mesma solução do Visual Studio, se você optar por fazer isso.

4. Com o aplicativo da Windows Store como projeto de inicialização, pressione a tecla F5 para recompilar o projeto e para iniciar o aplicativo da Windows Store.

5. No aplicativo, digite um texto significativo, como *Concluir o tutorial*, na caixa de texto **Inserir um TodoItem** e clique em **Salvar**.

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

	Isso envia uma solicitação POST para o novo back-end de aplicativo móvel hospedado no Azure.

6. Pare a depuração, clique com o botão direito no projeto `<your app name>.WindowsPhone`, clique em **Definir como Projeto de Inicialização** e pressione F5 novamente.

	![](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-wp8.png)

	Observe que os dados salvos da etapa anterior são carregados por meio do aplicativo móvel após o aplicativo do Windows ser iniciado.

##Próximas etapas

* [Adicionar autenticação ao seu aplicativo](app-service-mobile-windows-store-dotnet-get-started-users.md) <br/>Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.

* [Adicionar notificações por push ao seu aplicativo](app-service-mobile-windows-store-dotnet-get-started-push.md) <br/>Saiba como enviar uma notificação por push bem básica ao seu aplicativo.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[portal do Azure]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=Nov15_HO2-->