<properties
	pageTitle="Criar um aplicativo UWP (Plataforma Universal do Windows) em Aplicativos Móveis do Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Siga este tutorial para começar a usar os back-ends de aplicativo móvel do Azure para desenvolvimento de aplicativos UWP (Plataforma Universal do Windows) em C#, Visual Basic ou JavaScript."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="05/03/2016"
	ms.author="glenga"/>

#Criar um aplicativo do Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##Visão geral

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a um aplicativo UWP (Plataforma Universal do Windows). Para saber mais, confira [O que são Aplicativos Móveis](app-service-mobile-value-prop.md). A seguir, há capturas de tela do aplicativo concluído:

![Aplicativo de área de trabalho concluído](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png) em execução em uma área de trabalho.

![Aplicativo de telefone concluído](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png) em execução em um telefone

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais de Aplicativos Móveis para aplicativos UWP.

##Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2015] ou uma versão posterior.

>[AZURE.NOTE] Se você quiser ter uma introdução ao Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o Serviço de Aplicativo](https://tryappservice.azure.com/?appServiceName=mobile). Lá, você pode criar imediatamente um aplicativo móvel de curta duração inicial no Serviço de Aplicativo – sem cartão de crédito e sem compromissos.

##Criar um novo back-end de aplicativo móvel do Azure

Siga estas etapas para criar um novo back-end de aplicativo móvel.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Você acabou de provisionar um back-end do aplicativo móvel do Azure que pode ser usado pelos aplicativos móveis clientes. Em seguida, você baixará um projeto do servidor para um back-end simples da "lista de tarefas" e o publicará no Azure.

## Configurar o projeto de servidor

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

##Baixe e execute o projeto do cliente

Quando você tiver configurado o back-end do Aplicativo Móvel, poderá criar um novo aplicativo cliente ou modificar um aplicativo existente para se conectar ao Azure. Nesta seção, você baixa um projeto de modelo de aplicativo UWP personalizado para se conectar ao back-end do Aplicativo Móvel.

1. De volta à folha **Início rápido** do back-end do Aplicativo Móvel, clique em **Criar um novo aplicativo** > **Baixar** e extraia os arquivos de projeto compactados para o computador local.

	![Baixar o projeto de início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)

3. (Opcional) Adicione o projeto de aplicativo UWP à mesma solução que o projeto de servidor. Isso torna mais fácil depurar e testar o aplicativo e o back-end na mesma solução do Visual Studio, se você optar por fazer isso. Para adicionar um projeto de aplicativo UWP à solução, você deve estar usando o Visual Studio 2015 ou posterior.

4. Com o aplicativo UWP como o projeto de inicialização, pressione a tecla F5 para implantar e executar o aplicativo.

5. No aplicativo, digite um texto significativo, como *Concluir o tutorial*, na caixa de texto **Inserir um TodoItem** e clique em **Salvar**.

	![Área de trabalho completa do início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

	Isso envia uma solicitação POST para o novo back-end de aplicativo móvel hospedado no Azure.

6. (Opcional) Interrompa o aplicativo e reinicie-o em outro dispositivo ou emulador móvel.

	![Telefone completo do início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed.png)

	Observe que os dados salvos da etapa anterior são carregados do Azure depois que o aplicativo UWP é iniciado.

##Próximas etapas

* [Adicionar autenticação ao seu aplicativo](app-service-mobile-windows-store-dotnet-get-started-users.md) Saiba como autenticar os usuários de seu aplicativo com um provedor de identidade.

* [Adicionar notificações por push ao aplicativo](app-service-mobile-windows-store-dotnet-get-started-push.md) Saiba como adicionar suporte a notificações por push ao aplicativo e configurar o back-end do Aplicativo Móvel para usar os Hubs de Notificação do Azure para enviar notificações por push.

* [Habilitar sincronização offline para seu aplicativo](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Saiba como adicionar suporte offline em seu aplicativo usando um back-end do aplicativo móvel. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2015]: https://go.microsoft.com/fwLink/p/?LinkID=534203

<!---HONumber=AcomDC_0629_2016-->