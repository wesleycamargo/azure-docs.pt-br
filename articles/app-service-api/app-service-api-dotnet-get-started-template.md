<properties
	pageTitle="Introdução ao Serviço de Aplicativo do Azure e da API Web ASP.NET 2 | Microsoft Azure"
	description="Saiba como criar um projeto de API Web ASP.NET 2 no Visual Studio e implantá-lo em um novo aplicativo de API no Serviço de Aplicativo do Azure."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="04/13/2016"
	ms.author="tdykstra"/>

# Introdução ao Serviço de Aplicativo do Azure e da API Web ASP.NET 2 usando o Visual Studio

## Visão geral

Este tutorial mostra como implantar um aplicativo API Web ASP.NET 2 em um [aplicativo de API](app-service-api-apps-why-best-platform.md) no Serviço de Aplicativo do Azure usando o Visual Studio 2015. Você criará um projeto do Visual Studio e o implantará em um aplicativo de API, como mostrado no diagrama.

![Criação e implantação do diagrama no Visual Studio](./media/app-service-api-dotnet-get-started-template/Create_App.png)

O tutorial presume que você seja desenvolvedor ASP.NET sem experiência anterior no uso do Azure. Ao concluir o tutorial, você terá uma API Web simples em funcionamento na nuvem.

O que você aprenderá:

* Como criar um novo aplicativo de API do Serviço de Aplicativo enquanto cria um projeto API Web 2 no Visual Studio.
* Como implantar um projeto de API Web 2 em um aplicativo de API do Serviço de Aplicativo usando o Visual Studio.
* Como usar o [Portal do Azure](/features/azure-portal/) para monitorar e gerenciar seu aplicativo de API.

Ao final do tutorial, uma seção [Solução de problemas](#troubleshooting) dá ideias sobre o que fazer se algo não funcionar, e a seção [Próximas etapas](#next-steps) fornece links para outros tutoriais com mais detalhes sobre como usar o Serviço de Aplicativo do Azure.

## Pré-requisitos

### Conta do Azure

Você precisa de uma conta do Azure para concluir este tutorial. Você pode:

* [Abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F). Obtenha créditos que possam ser usados para experimentar os serviços pagos do Azure. Mesmo depois que os créditos são usados, você pode manter a conta e usar os serviços e recursos gratuitos do Azure.
* [Ativar benefícios de assinante do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). - todos os meses, sua assinatura do MSDN lhe oferece créditos que podem ser usados para serviços pagos do Azure.

Para obter uma introdução ao Serviço de Aplicativo do Azure antes de se inscrever para obter uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751). Lá, você poderá criar imediatamente um aplicativo de curta duração inicial no Serviço de Aplicativo — sem exigência de cartão de crédito e sem compromissos.

### <a name="setupdevenv"></a>Visual Studio 2015 com o SDK do Azure para .NET

O tutorial é escrito para o Visual Studio 2015 com o [SDK do Azure para .NET](../dotnet-sdk.md) 2.8.2 ou posterior. [Baixe o SDK mais recente do Azure para Visual Studio 2015 aqui](http://go.microsoft.com/fwlink/?linkid=518003). O Visual Studio será instalado automaticamente com o SDK caso você ainda não o tenha.

Se você tiver o Visual Studio 2013, poderá [baixar o SDK do Azure mais recente para o Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Algumas telas podem parecer diferentes das ilustrações.

>[AZURE.NOTE] Dependendo de quantas dependências de SDK você já tiver no seu computador, a instalação do SDK pode demorar bastante, de vários minutos a meia hora ou mais.

### API Web ASP.NET 2

Este tutorial fala sobre o uso do API Web ASP.NET 2 com o Serviço de Aplicativo do Azure; ele não ensina a desenvolver uma API Web ASP.NET. Para obter uma introdução à API Web ASP.NET 2, confira [Introdução à API Web ASP.NET 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) no site do [ASP.NET](http://asp.net/).

## Criar um projeto e um aplicativo de API no Serviço de Aplicativo do Azure

A primeira etapa é criar um projeto API Web ASP.NET 2 no Visual Studio e um aplicativo de API no Serviço de Aplicativo do Azure. Quando isso for feito, você implantará o projeto no aplicativo de API para disponibilizar a API Web na Internet.

1. Abra o Visual Studio 2015.

2. Clique em **Arquivo > Novo > Projeto**.

3. Na caixa de diálogo **Novo Projeto**, clique em **Visual C# > Web > Aplicativo Web ASP .NET**. (Se preferir, você pode escolher **Visual Basic**).

3. Verifique se o **.NET Framework 4.5.2** está selecionado como a estrutura de destino.

4.  O [Application Insights do Azure](../application-insights/app-insights-overview.md) monitora seu aplicativo de API quanto à sua disponibilidade, desempenho e utilização. A caixa de seleção **Adicionar Application Insights ao Projeto** é selecionada por padrão na primeira vez que você criar um projeto Web depois de instalar o Visual Studio. Desmarque a caixa de seleção se ela tiver selecionada, mas não desejar tentar o Application Insights.

4. Dê ao aplicativo o nome **MyExample**.

5. Clique em **OK**.

	![Caixa de diálogo Novo Projeto](./media/app-service-api-dotnet-get-started-template/GS13newprojdb.png)

5. Na caixa de diálogo **Novo Projeto ASP.NET**, selecione o modelo **Aplicativo de API do Azure**.

5. Na seção **Microsoft Azure** da caixa de diálogo **Novo Projeto ASP.NET**, certifique-se de que **Host na nuvem** está selecionado e que **Serviço de Aplicativo** está selecionado na lista suspensa.

	![Caixa de diálogo Novo Projeto ASP .NET](./media/app-service-api-dotnet-get-started-template/GS13newaspnetprojdb.png)

	Essas configurações direcionam o Visual Studio para criar um aplicativo de API do Azure para seu projeto Web.

6. Clique em **OK**.

5. Se você ainda não tiver entrado no Azure, o Visual Studio solicitará a você que o faça. Entre com a ID e a senha da conta que você usa para gerenciar sua assinatura do Azure.

	Quando você estiver conectado, a caixa de diálogo **Criar Serviço de Aplicativo** perguntará quais recursos você deseja criar.

	![Conectado no Azure](./media/app-service-api-dotnet-get-started-template/configuresitesettings.png)

3. Na caixa de diálogo **Criar Serviço de Aplicativo**, insira um **Nome do Aplicativo de API** que seja exclusivo no domínio *azurewebsites.net*. Por exemplo, você pode denominá-lo MyExample com números à direita para torná-lo exclusivo, como MyExample810. Se um nome padrão for criado para você, ele será exclusivo e você poderá usá-lo.

	Se outra pessoa já tiver usado o nome que inseriu, você verá um ponto de exclamação vermelho à direita em vez de uma marca de verificação verde, e será necessário inserir outro nome de site.

	A URL da sua API Web é esse nome mais *.azurewebsites.net* (como mostrado ao lado da caixa de texto **Nome do Aplicativo Web**). Por exemplo, se o nome for `MyExample810`, a URL será `myexample810.azurewebsites.net`.

6. Ao lado da caixa **Grupo de Recursos**, clique em **Novo** e insira "MeuExemplo" ou outro nome, se preferir.

	Essa caixa de combinação permite que você selecione um grupo de recursos existente ou crie um novo digitando um nome diferente de qualquer grupo de recursos existente na assinatura.

	Um grupo de recursos é uma coleção de recursos do Azure, como aplicativos de API, bancos de dados e VMs. Para um tutorial, é melhor criar um novo grupo de recursos, pois isso facilita a exclusão em uma etapa de quaisquer recursos do Azure criados para o tutorial. Para saber mais, confira [Visão geral do Azure Resource Manager](../resource-group-overview.md).

4. Clique no botão **Novo** ao lado da lista suspensa **Plano do Serviço de Aplicativo**.

	![Criar caixa de diálogo do Serviço de Aplicativo](./media/app-service-api-dotnet-get-started-template/createas.png)

	A captura de tela mostra valores de exemplo para **Nome de Aplicativo Web**, **Assinatura** e **Grupo de Recursos** – os valores serão diferentes.

	Nas etapas a seguir, você criará um plano de Serviço de Aplicativo para o novo grupo de recursos. Um plano de Serviço de Aplicativo especifica os recursos de computação em que seu aplicativo de API é executado. Por exemplo, se você escolher a camada gratuita, seu aplicativo de API será executado em VMs compartilhadas, enquanto que para algumas camadas pagas, ele é executado em VMs dedicadas. Para saber mais, confira [Visão geral dos planos do Serviço de Aplicativo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. No diálogo **Configurar Plano de Serviço de Aplicativo**, insira "MyExamplePlan" ou outro nome, se preferir.

5. Na lista suspensa **Local**, escolha o local mais próximo de você.

	Essa configuração especifica em qual datacenter do Azure o aplicativo será executado. Para este tutorial, você pode selecionar qualquer região e isto não fará uma diferença notável. Porém, para um aplicativo de produção, o ideal é que seu servidor esteja o mais próximo possível dos clientes que o acessam, de modo a minimizar a [latência](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Na lista suspensa **Tamanho**, clique em **Gratuito**.

	Neste tutorial, o tipo de preço gratuito permitirá o desempenho suficiente.

6. Na caixa de diálogo **Configurar o Plano do Serviço de Aplicativo**, clique em **OK**.

	![Configurar a caixa de diálogo do Serviço de Aplicativo](./media/app-service-api-dotnet-get-started-template/configasp.png)

7. Na caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Criar**.

	![Criar a caixa de diálogo Serviço de Aplicativo](./media/app-service-api-dotnet-get-started-template/clickcreate.png)

	Em pouco tempo, geralmente menos de um minuto, o Visual Studio cria o projeto Web e o aplicativo de API.

	A janela **Gerenciador de Soluções** mostra os arquivos e as pastas no novo projeto.

	![Gerenciador de Soluções](./media/app-service-api-dotnet-get-started-template/solutionexplorer.png)

	A janela **Atividade do Serviço de Aplicativo do Azure** mostra que o aplicativo de API foi criado. (Embora a mensagem possa chamá-lo de aplicativo Web).

	![Aplicativo de API criado na janela Atividade do Serviço de Aplicativo do Azure](./media/app-service-api-dotnet-get-started-template/GS13sitecreated1.png)

	Você pode ver o aplicativo de API na janela **Cloud Explorer** do Visual Studio.

	![Aplicativo de API criado no Cloud Explorer](./media/app-service-api-dotnet-get-started-template/siteinse.png)
	
	Essa janela permite exibir e gerenciar uma vasta gama de recursos do Azure. Você pode ver os diferentes tipos de recursos em seu **Gerenciador de nuvem** janela que aqueles neste exemplo. Clique com o botão direito do mouse em um recurso como o aplicativo de API para ver as opções disponíveis para gerenciá-lo.

## Implantar o projeto do Visual Studio para o aplicativo de API do Azure

Nesta seção, você implanta o projeto Web no aplicativo de API, como ilustrado na etapa 2 do diagrama.

![Criação e implantação do diagrama no Visual Studio](./media/app-service-api-dotnet-get-started-template/Create_App.png)

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e escolha **Publicar**.

	![Escolha Publicar no menu Visual Studio](./media/app-service-api-dotnet-get-started-template/choosepublish.png)

	Em poucos segundos, o assistente de **Publicar Web** é exibido. O assistente abre um *perfil de publicação* com configurações para implantar um projeto para o novo aplicativo de API. Se desejar implantar um aplicativo de API diferente, você pode clicar na guia **Perfil** para criar um perfil diferente. Para este tutorial, aceite as configurações que implantam o aplicativo de API criado anteriormente.

	O perfil de publicação inclui um nome de usuário e senha para a implantação. Essas credenciais foram geradas para você, e não é preciso inseri-las nem alterá-las. A senha é criptografada em um arquivo oculto específico do usuário na pasta `Properties\PublishProfiles`.

8. Na guia **Conexão** do assistente de **Publicar Web**, clique em **Avançar**.

	![Clique em Avançar na guia Conexão do assistente Publicar Web](./media/app-service-api-dotnet-get-started-template/GS13ValidateConnection.png)

	A próxima guia é **Configurações** (mostrada abaixo). Aqui, você pode alterar a guia de configuração de compilação para implantar uma compilação de depuração para [depuração remota](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). A guia também oferece várias **Opções de Publicação do Arquivo**:

	* Remover os arquivos adicionais no destino
	* Pré-compilar durante a publicação
	* Excluir arquivos da pasta App\_Data

	Para este tutorial, você não precisará de qualquer uma delas. Para obter explicações detalhadas sobre o que elas fazem, confira [Como implantar um projeto Web usando a publicação de um clique no Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

10. Na guia **Configurações**, clique em **Avançar**.

	![Guia Configurações do assistente de Publicar Web](./media/app-service-api-dotnet-get-started-template/GS13SettingsTab.png)

	A guia **Visualização** (mostrada abaixo) é a próxima. Aqui, você tem a oportunidade de ver quais arquivos serão copiados do seu projeto para o aplicativo de API. Quando você estiver implantando um projeto para um aplicativo de API no qual já tenha implantado antes, somente os arquivos alterados serão copiados. Para ver uma lista do que será copiado, você pode clicar no botão **Iniciar Visualização**.

11. Na guia **Visualização**, clique em **Publicar**.

	![Guia Visualização do assistente Publicar Web](./media/app-service-api-dotnet-get-started-template/GS13previewoutput.png)

	Quando você clica em **Publicar**, o Visual Studio inicia o processo de cópia dos arquivos no servidor do Azure. Isso pode levar alguns minutos.

	As janelas **Saída** e **Atividade do Serviço de Aplicativo do Azure** mostram as ações de implantação que foram executadas e relatam a conclusão bem-sucedida da implantação.

	![Janela de saída do Visual Studio indicando o êxito na implantação](./media/app-service-api-dotnet-get-started-template/PublishOutput.png)

	Após a implantação bem-sucedida, o navegador padrão abre automaticamente a URL base do aplicativo de API implantado. O navegador exibe uma página com uma mensagem que indica "Este aplicativo Web foi criado com êxito".

	![Janela de saída do Visual Studio indicando o êxito na implantação](./media/app-service-api-dotnet-get-started-template/successfullycreated.png)

> [AZURE.TIP] Você pode habilitar a barra de ferramentas **Publicação Web com um Clique** para uma implantação rápida. Clique em **Exibir > Barras de Ferramentas** e, em seguida, selecione **Publicação Web com Um Clique**. Você pode usar a barra de ferramentas para selecionar um perfil, clicar em um botão para publicar ou clicar em um botão para abrir o assistente **Publicar Web**. ![Barra de ferramentas Publicação Web com Um Clique](./media/app-service-api-dotnet-get-started-template/weboneclickpublish.png)

## Testar a API Web implantada

1. Envie uma solicitação HTTP GET para a URL *{nome do seu aplicativo de API}.azurewebsites.net/api/values*, usando sua ferramenta de cliente HTTP preferida.

	O modelo de projeto de API Web define um controlador `Values` que retorna uma matriz de duas cadeias de caracteres no formato JSON para uma solicitação GET. A ilustração a seguir mostra uma solicitação enviada por [Postman](http://www.getpostman.com/), com o JSON retornado no corpo da resposta.

	![Janela de saída do Visual Studio indicando o êxito na implantação](./media/app-service-api-dotnet-get-started-template/postman.png)

2. Agora você pode fazer alterações no código, reimplantar o projeto da mesma forma que a implantação inicial e ver suas alterações funcionando no Azure em segundos.

## <a id="portal"></a> Opcional: Monitorar e gerenciar o aplicativo de API no portal do Azure

O [portal do Azure](/services/management-portal/) é uma interface da Web que você pode usar para gerenciar e monitorar seus serviços do Azure, como o aplicativo de API que acabou de criar. Nesta seção do tutorial, você descobre um pouco do que pode fazer no portal.

1. Em seu navegador, vá até [https://portal.azure.com](https://portal.azure.com) e entre com as credenciais usadas para gerenciar sua conta do Azure.
	
2. Clique em **Serviços de Aplicativos** e clique no nome do seu aplicativo de API.

	![Serviços de Aplicativos no Portal do Azure](./media/app-service-api-dotnet-get-started-template/selinportal.png)

	A folha **Aplicativo de API** exibe uma visão geral das configurações e estatísticas de uso de seu aplicativo de API. (As janelas abertas à direita do portal são chamadas de *folhas*).

	![Folha Aplicativo de API no portal do Azure](./media/app-service-api-dotnet-get-started-template/portaldashboard.png)

	Seu aplicativo de API não teve muito tráfego ainda e talvez não mostre nada no gráfico. Se você fizer mais algumas solicitações para sua API Web e então atualizar a página do portal, verá algumas estatísticas.

3. A folha **Configurações** mostra mais opções para configurar seu aplicativo de API.

	![Folha Configurações no Portal do Azure](./media/app-service-api-dotnet-get-started-template/portalconfigure1.png)

	A seção **API** vincula a folhas para a configuração de um ponto de extremidade de metadados de API e CORS. Veja a seção [Próximas etapas](#next-steps) para obter tutoriais que apresentem esses recursos.

	Observe o link **Credenciais de implantação** na seção **Publicação**. É nesse local que você pode criar um nome de usuário e uma senha personalizados para a implantação. Clique no botão **Salvar** na parte superior da folha para enviar a alteração. Se criar um novo nome de usuário e senha, você precisará inserir os mesmos valores na guia **Conexão** do assistente **Publicar Web** no projeto Web.
	
	A captura de tela é apenas uma visão parcial da folha **Configurações**. Há mais seções nessa folha do que é mostrado.

Esses são apenas alguns dos recursos do Portal. Você pode criar novos aplicativos de API, excluir aplicativos de API existentes, parar e reiniciar aplicativos de API e gerenciar outros tipos de serviços do Azure, como bancos de dados e máquinas virtuais.

## Opcional: Excluir o aplicativo de API do Azure

Quando não precisar mais do aplicativo de API que criou para este tutorial, você poderá excluí-lo.

Uma maneira fácil de excluir o aplicativo de API é clicar no botão **Excluir** na parte superior da folha **Aplicativo de API** no portal do Azure. Uma maneira mais adequada é excluir o grupo de recursos que você criou para conter o aplicativo de API. Para este tutorial, o grupo de recursos contém apenas o aplicativo de API, mas normalmente um grupo de recursos contém uma coleção de recursos relacionados. Por exemplo, o aplicativo de API pode usar um banco de dados ou uma conta de armazenamento do Azure que não são mais necessários quando você exclui o aplicativo de API. Quando você exclui um grupo de recursos, tudo o que ele contém é excluído. Para excluir um grupo de recursos usando o Portal do Azure, execute as etapas a seguir.

1. Acesse a home page do [portal do Azure](https://portal.azure.com).

2. Clique em **Grupos de Recursos**.

3. Na lista de grupos de recursos, clique no grupo de recursos que você deseja excluir.

	Quando a folha **Grupo de Recursos** for exibida, incluirá uma lista dos recursos que contém.

4. Na folha **Grupo de Recursos**, clique em **Excluir**.

	![Excluir grupo de recursos no Portal do Azure](./media/app-service-api-dotnet-get-started-template/delresgrp.png)

## Solucionar problemas

Se encontrar um problema ao percorrer este tutorial, verifique se você está usando a versão mais recente do SDK do Azure para .NET. A maneira mais fácil de fazer isso é [baixar o SDK do Azure para Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Se você tiver a versão atual instalada, o Web Platform Installer avisará que nenhuma instalação é necessária.

Se você estiver em uma rede corporativa e estiver tentando implantar no Serviço de Aplicativo do Azure por meio de um firewall, verifique se as portas 443 e 8172 estão abertas para implantação na Web. Se não for possível abrir essas portas, confira a seção Próximas etapas a seguir para obter outras opções de implantação.

Quando seu aplicativo de API ASP.NET estiver em execução no Serviço de Aplicativo do Azure, procure saber mais sobre os recursos do Visual Studio que simplificam a solução de problemas. Para saber mais sobre o registro em log, a depuração remota e muito mais, confira [Solução de problemas de aplicativos de API do Azure no Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## Próximas etapas

Neste tutorial, você viu como criar uma API Web simples e como implantá-la em um aplicativo de API no Serviço de Aplicativo do Azure. Para obter uma introdução aos recursos do Serviço de Aplicativo que facilitam o desenvolvimento e o consumo de APIs Web, veja a série de tutoriais que começa com [Introdução aos aplicativos de API e ao ASP.NET](app-service-api-dotnet-get-started.md).

<!---HONumber=AcomDC_0420_2016-->