<properties
	pageTitle="Criar e implantar um aplicativo de API do Node.js no Serviço de Aplicativo do Azure"
	description="Saiba como criar um pacote do aplicativo de API do Node.js e implantá-lo no Serviço de Aplicativo do Azure."
	services="app-service\api"
	documentationCenter="nodejs"
	authors="pkefal"
  manager="" 
  editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/21/2015"
	ms.author="pakefali"/>

# Criar e implantar um aplicativo de API do Node.js no serviço de aplicativo do Azure

Este tutorial mostra como criar um aplicativo [Node.js](http://nodejs.org) e implantá-lo em aplicativos de API do serviço de aplicativo do Azure usando [Git](http://git-scm.com). As instruções deste tutorial podem ser seguidas em qualquer sistema operacional que seja capaz de executar o nó.

Eis aqui uma captura de tela do aplicativo concluído:

![][sample-api-app-page]

> [AZURE.NOTE]Você também pode usar o Visual Studio Code para compilar e implantar um aplicativo de API do Node.js no Serviço de Aplicativo do Azure. Para obter mais informações sobre o Visual Studio Code e Node.js, consulte [Visual Studio Code](http://code.visualstudio.com/Docs/) e [Desenvolvimento de Aplicativos de Nó](http://code.visualstudio.com//Docs/nodejs).

## Criar um aplicativo de API no Portal de Visualização do Azure

> [AZURE.NOTE]Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [ativar os benefícios de assinante MSDN](/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se para uma avaliação gratuita](/pricing/free-trial/). Você também pode experimentar gratuitamente [Exemplos de aplicativos do serviço de aplicativo](http://tryappservice.azure.com).

1. Faça logon no [Portal de Visualização do Azure](https://portal.azure.com).

2. Clique em **NOVO** na parte inferior esquerda do portal.

3. Clique em **Web + Móvel > Aplicativo de API**.

	![][portal-quick-create]

4. Insira um valor para **Nome**, como NodejsAPIApp.

5. Selecione um plano de serviço de aplicativo ou crie um novo. Se você criar um novo plano, selecione a camada de preços, localização e outras opções.

	![][portal-create-api]

6. Clique em **Criar**.

	![][api-app-blade]

	Se você deixou a caixa de seleção **Adicionar ao quadro inicial** marcada, o portal abre automaticamente a folha de seu aplicativo de API após sua criação. Se você desmarcou a caixa de seleção, clique em **Notificações** na home page do portal para ver o status de criação do aplicativo de API e clique na notificação para ir até a folha do novo aplicativo de API.

7. Clique em **Configurações > Configurações do aplicativo**.

9. Defina o nível de acesso como **Público (anônimo)**.

11. Clique em **Salvar**.

	![][set-api-app-access-level]

## Habilitar a publicação de Git para o novo aplicativo de API

[O Git](http://git-scm.com/%20target="_blank) é um sistema de controle de versão distribuído que você pode usar para implantar seu Site do Azure. Você armazenará o código que você escreve para seu aplicativo de API em um repositório Git local e você implantará seu código no Azure enviando-o por push para um repositório remoto. Esse método de implantação é um recurso dos aplicativos Web do serviço de aplicativo que pode ser usado em um aplicativo de API, já que aplicativos de API se baseiam em aplicativos Web: um aplicativo de API no serviço de aplicativo do Azure é um aplicativo Web com recursos adicionais para a hospedagem de serviços da Web.

No portal, você gerencia os recursos específicos dos aplicativos de API na folha **Aplicativo de API** e gerencia os recursos que são compartilhados com os aplicativos Web na folha **Host de aplicativo de API**. Portanto, nesta seção, você vai até a folha **Host de aplicativo de API** para configurar o recurso de implantação do Git.

1. Na folha Aplicativo de API, clique em **Host de aplicativo de API**.

	![][api-app-host]

2. Encontre a seção **Implantação** da folha **Aplicativo de API** e clique em **Configurar implantação contínua**. Talvez seja necessário rolar para baixo para ver essa parte da folha.

	![][deployment-part]

3. Clique em **Escolher fonte > Repositório Git local**.

5. Clique em **OK**.

	![][setup-git-publishing]

6. Se você não configurou previamente as credenciais de implantação para publicação de um Aplicativo de API ou outro aplicativo de Serviço de Aplicativo, configure-as agora:

	* Clique em **Configurar as credenciais de implantação**.

	* Digite um nome de usuário e senha.

	* Clique em **Salvar**.

	![][deployment-credentials]

1. Na folha **Host de aplicativo de API**, clique em **Configurações > Propriedades**. A URL do repositório Git remoto na qual você vai implantar é mostrada em "URL GIT".

2. Copie a URL para uso posterior no tutorial.

	![][git-url]

## Baixe e inspecione o código para um aplicativo de API do Node.js

Nesta seção, você vai baixar e examinar o código fornecido como parte do exemplo NodeAPIApp.

1. Baixe o código [neste repositório GitHub](http://go.microsoft.com/fwlink/?LinkID=534023&clcid=0x409). Você pode clonar o repositório ou clicar em **Baixar Zip** para baixá-lo como um arquivo .zip. Se você baixar o arquivo .zip, descompacte-o em seu disco local.

2. Navegue até a pasta em que você descompactou o exemplo.

	![][api-app-folder-browse]

3. Abra o arquivo **apiapp.json** em um editor de texto e inspecione o conteúdo.

	![][apiapp-json]

	O Serviço de aplicativo do Azure possui dois pré-requisitos para reconhecer um aplicativo Node.js como um Aplicativo de API:

	+ Um arquivo chamado *apiapp.json* deve estar presente no diretório raiz do aplicativo.
	+ Um ponto de extremidade de metadados Swagger 2.0 precisa ser exposto pelo aplicativo. A URL do ponto de extremidade é especificada no arquivo *apiapp.json*.

	Observe a propriedade **apiDefinition**. O caminho para essa URL é relativo à URL da sua API e aponta para o ponto de extremidade Swagger 2.0. O Serviço de Aplicativo do Azure usa essa propriedade para descobrir a definição de sua API e habilitar muitos dos recursos de aplicativos de API do serviço de aplicativo.

	> [AZURE.NOTE]O ponto de extremidade deve ter especificação Swagger 2.0, já que as versões anteriores (por exemplo, 1.2) sem suporte pela plataforma. O aplicativo de exemplo está usando swaggerize-express para criar um ponto de extremidade de especificação Swagger 2.0.

4. Abra o arquivo **server.js** e inspecione o código.

	![][server-js]

	O código usa o módulo swaggerize-express para criar o ponto de extremidade Swagger 2.0.

		app.use(swaggerize({
		    api: require('./api.json'),
		    docspath: '/swagger',
		    handlers: './handlers/'
		}));

	A propriedade `api` aponta para o arquivo de api.json, que contém a definição da especificação Swagger 2.0 de sua API. Você pode criar manualmente o arquivo em um editor de texto ou usar o [editor do Swagger](http://editor.swagger.io) online e baixar o arquivo JSON a partir de lá. (O arquivo *api.json* especifica uma propriedade `host`, mas o valor dessa propriedade é determinado e substituído dinamicamente em tempo de execução.)

	A propriedade `docspath` aponta para o ponto de extremidade Swagger 2.0. Essa URL é relativa ao caminho base de sua API. O caminho base é declarado no arquivo api.json. Em nosso exemplo é o caminho base é *api/data*, portanto, o caminho relativo para o ponto de extremidade swagger é */api/data/swagger*.

	> [AZURE.NOTE]Como o caminho base é declarado no arquivo *api.json*, tentar acessar o ponto de extremidade */swagger* como um caminho relativo para a URL do seu aplicativo API retornará 404. Esse é um erro comum ao tentar acessar o ponto de extremidade swagger.

	A propriedade `handlers` aponta para a pasta local que contém os manipuladores de rotas para o módulo Express.js.

## Execute o código de aplicativo de API localmente

Nesta seção você executa o aplicativo localmente para verificar se que ele funciona antes da implantação.

1. Navegue até a pasta de que você baixou o exemplo.

2. Abra um prompt de linha de comando e digite o seguinte comando:

		npm install

3. Quando o comando *instalar* terminar, digite o seguinte comando:

		node server.js

	A saída da janela de linha de comando exibe "Servidor iniciado .."

5. Aponte seu navegador para http://localhost:1337/

	Você verá a página a seguir

	![][sample-api-app-page]

6. Para exibir o arquivo Swagger.json, navegue até http://localhost:1337/api/data/swagger.

## Publicar seu código de aplicativo de API no serviço de aplicativo do Azure

Nesta seção você criará um repositório Git local e enviará por push a partir desse repositório para o Azure, para implantar seu aplicativo de exemplo no Aplicativo de API em execução no Serviço de Aplicativo do Azure.

1. Se o Git não estiver instalado, instale-o a partir da [página de download do Git](http://git-scm.com/download%20target="_blank").

1. Na linha de comando, alterne para o diretório do aplicativo de exemplo e digite os seguintes comandos para inicializar um repositório Git local.

		git init


2. Insira os comandos a seguir para adicionar arquivos ao repositório:

		git add .
		git commit -m "Initial commit of the API App"

3. Crie uma referência remota para enviar atualizações por push para o aplicativo Web (host de aplicativo de API) criado anteriormente, usando a URL de Git que você copiou anteriormente:

		git remote add azure [URL for remote repository]

4. Envie as alterações por push ao Azure usando o seguinte comando:

		git push azure master

	Será solicitada a senha que você criou anteriormente.

	A saída deste comando termina com uma mensagem de que a implantação foi bem-sucedida:

		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
	 	* [new branch]      master -> master

## Exibir a definição de API no portal de visualização do Azure

Agora que você implantou uma API para seu aplicativo de API, você pode ver a definição de API no portal de visualização do Azure. Você começará reiniciando o *gateway*, que permite que o Azure reconheça que a definição de API de um aplicativo de API foi alterada. O gateway é um aplicativo Web que trata da administração da API e da autorização para os Aplicativos de API em um grupo de recursos.

6. No portal de visualização do Azure, vá até a folha **Aplicativo de API** para o aplicativo de API que você criou anteriormente e clique no link **Gateway**.

	![](./media/app-service-api-nodejs-api-app/clickgateway.png)

7. Na folha **Gateway**, clique em **Reiniciar**. Agora você pode fechar esta folha.

	![](./media/app-service-api-nodejs-api-app/gatewayrestart.png)

8. Na folha **Aplicativo de API**, clique em **Definição de API**.

	![](./media/app-service-api-nodejs-api-app/apidef.png)

	A folha **Definição de API** mostra dois métodos Get.

	![](./media/app-service-api-nodejs-api-app/apidefblade.png)

## Executar o aplicativo de exemplo no Azure

No portal de visualização do Azure, vá até a folha **Host de aplicativo de API** para seu aplicativo de API e clique em **Procurar** .

![][browse-api-app-page]

O navegador exibe a home page que vimos anteriormente, quando você executou o aplicativo de exemplo localmente.

[AZURE.INCLUDE [app-service-api-direct-deploy-metadata](../../includes/app-service-api-direct-deploy-metadata.md)]

## Próximas etapas

Você implantou um aplicativo Web Node.js que usa um back-end do aplicativo de API para o Azure. Para obter mais informações sobre como usar o Node.js no Azure, consulte o [Centro de desenvolvedores do Node.js](/develop/nodejs/).

* Você pode experimentar esse aplicativo de API de exemplo no [Serviço TryApp](http://tryappservice.azure.com)

[portal-quick-create]: ./media/app-service-api-nodejs-api-app/portal-quick-create.png
[portal-create-api]: ./media/app-service-api-nodejs-api-app/portal-create-api.png
[api-app-blade]: ./media/app-service-api-nodejs-api-app/api-app-blade.png
[api-app-folder-browse]: ./media/app-service-api-nodejs-api-app/api-app-folder-browse.png
[api-app-host]: ./media/app-service-api-nodejs-api-app/api-app-host.png
[deployment-part]: ./media/app-service-api-nodejs-api-app/continuous-deployment.png
[set-api-app-access-level]: ./media/app-service-api-nodejs-api-app/set-api-app-access.png
[setup-git-publishing]: ./media/app-service-api-nodejs-api-app/local-git-repo.png
[deployment-credentials]: ./media/app-service-api-nodejs-api-app/deployment-credentials.png
[git-url]: ./media/app-service-api-nodejs-api-app/git-url.png
[apiapp-json]: ./media/app-service-api-nodejs-api-app/apiapp-json.png
[server-js]: ./media/app-service-api-nodejs-api-app/server-js.png
[sample-api-app-page]: ./media/app-service-api-nodejs-api-app/sample-api-app-page.png
[browse-api-app-page]: ./media/app-service-api-nodejs-api-app/browse-api-app-page.png
 

<!---HONumber=62-->