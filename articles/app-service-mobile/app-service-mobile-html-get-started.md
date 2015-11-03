<properties
	pageTitle="Introdução aos back-ends do aplicativo móvel para aplicativos HTML/JavaScript | Aplicativos móveis do serviço de aplicativo do Azure"
	description="Siga este tutorial para começar a usar os back-ends de aplicativo móvel do Azure para desenvolvimento de aplicativos web em HTML5 e JavaScript."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html5"
	ms.devlang="javascript"
	ms.topic="get-started-article"
	ms.date="10/05/2015"
	ms.author="glenga"/>


#Criar um aplicativo HTML

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Visão geral

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo web HTML5/JavaScript usando um back-end de aplicativo móvel do Azure. Você criará um novo back-end do aplicativo móvel e um aplicativo web simples com *Lista de tarefas pendentes* que armazena dados de aplicativo no Azure.

Uma captura de tela do aplicativo completo está disponível abaixo:

![Captura de tela do aplicativo concluído](./media/app-service-mobile-html-get-started/mobile-quickstart-completed-html.png)

A conclusão desse tutorial é um pré-requisito para todos os outros tutoriais de aplicativos móveis para os aplicativos HTML.

##Pré-requisitos

Para concluir este tutorial, você precisará do seguinte:

* Uma conta ativa do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 aplicativos móveis gratuitos que você pode continuar a usar mesmo após o fim do seu período de avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).

* [Visual Studio Community 2013] ou versão posterior.

>[AZURE.NOTE]Se você deseja começar com o serviço de aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá até [Experimentar o serviço de aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751&appServiceName=mobile), onde você pode criar imediatamente um aplicativo móvel inicial de curta duração no serviço de aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

##Criar um novo back-end de Aplicativo móvel

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## Baixar o projeto de servidor

1. No [Portal do Azure], clique em**Procurar Tudo** > **Aplicativos Web** e, em seguida, clique no back-end de Aplicativo Móvel que você acabou de criar. 

2. No back-end de Aplicativo Móvel, clique em **Todas as Configurações** e, em **Aplicativo Móvel**, clique **Início rápido** > **HTML/JavaScript**.

3. Em **Baixe e execute seu projeto de servidor** na **Criar um novo aplicativo**, clique em **Baixar**, extraia os arquivos compactados do projeto em seu computador local e abra a solução no Visual Studio.

4. Compile o projeto para restaurar os pacotes do NuGet.

##Habilitar CORS no projeto do servidor

O compartilhamento de recursos entre origens (CORS) é uma maneira para seu aplicativo baseado na web indicar a partir de quais domínios as solicitações são seguras e devem ser permitidas pelo navegador. Você deve adicionar uma entrada CORS para cada site que acessará o back-end do aplicativo móvel. Você pode controlar as configurações de CORS usando os comportamentos padrão de API Web do ASP.NET. Para obter mais informações, consulte [Permitindo solicitações entre origens na API Web ASP.NET](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api#enable-cors).

Por padrão, o projeto de início rápido do cliente que você baixará do portal é executado no localhost na porta 8000. Por isso, você posteriormente habilitará CORS para `http://localhost:8000` no projeto do servidor.

1. No menu Ferramentas do Visual Studio, clique em **Gerenciador de pacotes NuGet** > **Console do gerenciador de pacotes**, selecione Nuget.org como a **Fonte do pacote** e execute o seguinte comando na janela do console:
 
		Install-Package Microsoft.AspNet.WebApi.Cors  

2. Abra o arquivo de projeto App\_Start/Startup.MobileApp.cs e adicione a instrução de uso a seguir:

		using System.Web.Http.Cors;

3. Em seguida, adicione o seguinte código ao método **Startup.ConfigureMobileApp** após **HttpConfiguration** (*config*) ser criado:

        // Enable CORS support for localhost port 8000, all headers and methods.
        var cors = new EnableCorsAttribute("http://localhost:8000", "*", "*");
        config.EnableCors(cors);

4. Salve as atualizações.

Em seguida, você implantará o projeto habilitado por CORS no Azure.

##Publicar o projeto de servidor no Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##Baixe e execute o projeto do cliente

1. De volta à folha para o back-end do Aplicativo Móvel, clique em **Todas as Configurações** e, em **Aplicativo Móvel**, clique em **Início rápido** > **HTML/JavaScript**. 

2.  Em **Baixe e execute seu projeto do HTML/JavaScript** em **Criar um novo aplicativo**, clique em **Baixar** e salve os arquivos compactados do projeto em seu computador local.

3. Navegue até o local onde salvou os arquivos de projeto compactados, expanda os arquivos em seu computador e inicie um dos arquivos de comando a seguir da subpasta **server**.

	+ ** launch-windows** (computadores com Windows)
	+ ** launch-mac.command** (computadores com Mac OS X)
	+ ** launch-linux.sh** (computadores com Linux)

	> [AZURE.NOTE]Em um computador Windows, digite `R` quando o PowerShell solicitar que você confirme se deseja executar o script. Seu navegador da web poderá avisá-lo para não executar o script por ele ter sido baixado da internet. Quando isso acontecer, você deverá solicitar que o navegador continue para carregar o script.

	Isso iniciará um servidor web no computador local para hospedar o novo aplicativo.

4. Abra a URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> em um navegador da Web para iniciar o aplicativo. O aplicativo cliente está configurado para se conectar ao back-end do aplicativo móvel no Azure.

5. No aplicativo, digite um texto significativo, como _Concluir o tutorial_, em **Inserir nova tarefa** e clique em **Adicionar**.

   	![Executando o aplicativo](./media/app-service-mobile-html-get-started/mobile-quickstart-startup-html.png)

   	Isso envia uma solicitação de POST para o novo back-end de Aplicativo móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem no esquema Aplicativo móvel. Os itens armazenados na tabela são retornados pelo serviço, e os dados são exibidos na segunda coluna do aplicativo.

	> [AZURE.TIP]Você poderá examinar o código que acessa o seu serviço móvel para consultar e inserir dados, que estão localizados no arquivo app.js.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Get started with authentication]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Mobile App SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portal do Azure]: https://portal.azure.com/

[Visual Studio Community 2013]: https://www.visualstudio.com/downloads
 

<!---HONumber=Nov15_HO1-->