<properties
	pageTitle="Criar e implantar um aplicativo de API Java no Serviço de Aplicativo do Azure"
	description="Saiba como criar um pacote do aplicativo de API Java e implantá-lo no Serviço de Aplicativo do Azure."
	services="app-service\api"
	documentationCenter="java"
	authors="pkefal"
	manager="mohisri" 
	editor="jimbe"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="java"
	ms.topic="get-started-article"
	ms.date="08/11/2015"
	ms.author="pakefali"/>

# Criar e implantar um aplicativo de API Java no Serviço de Aplicativo do Azure

> [AZURE.SELECTOR]
- [.NET - Visual Studio 2015](app-service-dotnet-create-api-app.md)
- [.NET - Visual Studio Code](app-service-create-aspnet-api-app-using-vscode.md)
- [Node.js](app-service-api-nodejs-api-app.md)
- [Java](app-service-api-java-api-app.md)

Este tutorial mostra como criar um aplicativo Java e implantá-lo em aplicativos de API do Serviço de Aplicativo do Azure usando o [Git](http://git-scm.com). As instruções deste tutorial podem ser seguidas em qualquer sistema operacional que seja capaz de executar o Java. Este tutorial também usa o [Gradle](https://gradle.org) para habilitar a automação de compilação e a resolução de dependências de pacotes para o aplicativo Java. Por fim, [RESTEasy](http://resteasy.jboss.org/) é usado para criar o Serviço RESTful, implementando completamente a especificação [JaxRS](https://jax-rs-spec.java.net/).

Eis aqui uma captura de tela do aplicativo concluído:

![][sample-api-app-page]

## Criar um aplicativo de API no Portal do Azure

> [AZURE.NOTE]Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [ativar os benefícios de assinante MSDN](/pricing/member-offers/msdn-benefits-details/) ou [inscrever-se para uma avaliação gratuita](/pricing/free-trial/). Você também pode experimentar gratuitamente [Exemplos de aplicativos do serviço de aplicativo](http://tryappservice.azure.com).

1. Faça logon no [Portal de Visualização do Azure](https://portal.azure.com).

2. Clique em **NOVO** na parte inferior esquerda do portal.

3. Clique em **Web + Móvel > Aplicativo de API**.

	![][portal-quick-create]

4. Insira um valor para **Nome**, como JavaAPIApp.

5. Selecione um plano de Serviço de Aplicativo ou crie um plano novo. Se você criar um novo plano, selecione a camada de preços, localização e outras opções.

	![][portal-create-api]

6. Clique em **Criar**.

	![][api-app-blade]

	Se você deixou a caixa de seleção **Adicionar ao quadro inicial** marcada, o portal abre automaticamente a folha de seu aplicativo de API após sua criação. Se você desmarcou a caixa de seleção, clique em **Notificações** na home page do portal para ver o status de criação do aplicativo de API e clique na notificação para ir até a folha do novo aplicativo de API.

7. Clique em **Configurações > Configurações do aplicativo**.

9. Defina o nível de acesso como **Público (anônimo)**.

11. Clique em **Salvar**.

	![][set-api-app-access-level]

## Habilitar a publicação de Git para o novo aplicativo de API

[O Git](http://git-scm.com) é um sistema de controle de versão distribuído que você pode usar para implantar seu Site do Azure. Você armazenará o código que você escreve para seu aplicativo de API em um repositório Git local e você implantará seu código no Azure enviando-o por push para um repositório remoto. Esse método de implantação é um recurso dos aplicativos Web do serviço de aplicativo que pode ser usado em um aplicativo de API, já que aplicativos de API se baseiam em aplicativos Web: um aplicativo de API no serviço de aplicativo do Azure é um aplicativo Web com recursos adicionais para a hospedagem de serviços da Web.

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

## Habilitar o tempo de execução do Java no novo aplicativo de API

Para que o aplicativo de API hospede um aplicativo Java com êxito, temos que habilitar o tempo de execução do Java e escolher um servidor de aplicativos. O portal fornece uma maneira fácil de fazer isso. Vamos habilitar o Java 7 e o Jetty para hospedar nosso aplicativo.

1. Na folha Aplicativo de API, clique em **Host de aplicativo de API**.

	![][api-app-host]

2. Clique em **Configurações > Configurações do aplicativo**. Habilite o Java e selecione Jetty como o servidor de aplicativos. Clique em **Salvar**

	![][api-app-enable-java]

Isso vai **habilitar o tempo de execução do Java** em seu aplicativo de API e criar uma pasta **webapps/** na raiz do site. Essa pasta conterá todos os arquivos. .war de seus aplicativos.

## Baixar e inspecionar o código para um aplicativo de API do Java

Nesta seção, você vai baixar e examinar o código fornecido como parte do exemplo JavaAPIApp.

1. Baixe o código [neste repositório GitHub](http://go.microsoft.com/fwlink/?LinkId=571009). Você pode clonar o repositório ou clicar em **Baixar Zip** para baixá-lo como um arquivo .zip. Se você baixar o arquivo .zip, descompacte-o em seu disco local.

2. Navegue até a pasta em que você descompactou o exemplo e navegue até a pasta `build\libs`.

	![][api-app-folder-browse]

3. Abra o arquivo **apiapp.json** em um editor de texto e inspecione o conteúdo.

	![][apiapp-json]

	O Serviço de Aplicativo do Azure tem dois pré-requisitos para reconhecer um aplicativo do Java como um aplicativo de API:

	+ Um arquivo chamado *apiapp.json* deve estar presente no diretório raiz do aplicativo.
	+ Um ponto de extremidade de metadados Swagger 2.0 precisa ser exposto pelo aplicativo. A URL do ponto de extremidade é especificada no arquivo *apiapp.json*.

	Observe a propriedade **apiDefinition**. O caminho para essa URL é relativo à URL da sua API e aponta para o ponto de extremidade Swagger 2.0. O Serviço de Aplicativo do Azure usa essa propriedade para descobrir a definição de sua API e habilitar muitos dos recursos de aplicativos de API do serviço de aplicativo.

4. Navegue até `src\main\java\com\microsoft\trysamples\javaapiapp`, abra o arquivo **App.java** e inspecione o código.

	![][app-java]

	O código usa o pacote do Swagger para JaxRS para criar o ponto de extremidade do Swagger 2.0.

		beanConfig.setVersion("1.0.0");
		beanConfig.setBasePath("/JavaAPIApp/api");
		beanConfig.setHost(websitehostname);
		beanConfig.setResourcePackage("com.microsoft.trysamples.javaapiapp");
		beanConfig.setSchemes(new String[]{"http", "https"});
		beanConfig.setScan(true);

	O método `setVersion` define a versão de API nos metadados servidos pelo Swagger.

	O método `setBasePath` define o caminho base que o Swagger usa para gerar os metadados. Essa URL é relativa ao caminho base de seu aplicativo de API.

	O método `setHost` define o host em que a API está escutando. Nesse caso, estamos usando a variável `websitehostname` que atribuímos algumas linhas antes para definir dinamicamente para `localhost`, quando executado localmente, ou o nome de host do aplicativo de API, quando o aplicativo é executado no Serviço de Aplicativo do Azure.

	O método `setResourcePackage` define o pacote que o Swagger examinará e incluirá no arquivo Swagger.json, que contém os metadados de API.

	O método `setSchemes` define os esquemas com suporte.

	O método `setScan` faz o Swagger gerar a documentação do aplicativo.

	Há mais métodos disponíveis que personalizam a saída do Swagger ao usar RESTEasy. Eles podem ser encontrados na [página do Wiki](https://github.com/swagger-api/swagger-core/wiki/Swagger-Core-RESTEasy-2.X-Project-Setup-1.5#using-swaggers-beanconfig) do Swagger

	> [AZURE.NOTE]O arquivo de metadados do Swagger pode ser acessado em `/JavaAPIApp/api/swagger.json`.

## Execute o código de aplicativo de API localmente

Nesta seção você executa o aplicativo localmente para verificar se que ele funciona antes da implantação.

1. Navegue até a pasta de que você baixou o exemplo.

2. Abra um prompt de linha de comando e digite o seguinte comando:

		gradlew.bat

3. Quando o comando for concluído, digite o seguinte comando:

		gradlew.bat jettyRunWar

	A saída da janela de linha de comando exibe:

		17:25:49 INFO  JavaAPIApp runs at:
		17:25:49 INFO    http://localhost:8080/JavaAPIApp

5. Aponte seu navegador para `http://localhost:8080/JavaAPIApp/`

	Você verá a página a seguir

	![][sample-api-app-page]

6. Para exibir o arquivo Swagger.json, navegue até `http://localhost:8080/JavaAPIApp/api/Swagger.json`.

## Publicar seu código de aplicativo de API no serviço de aplicativo do Azure

Nesta seção você criará um repositório Git local e enviará por push a partir desse repositório para o Azure, para implantar seu aplicativo de exemplo no Aplicativo de API em execução no Serviço de Aplicativo do Azure.

1. Se o Git não estiver instalado, instale-o a partir da [página de download do Git](http://git-scm.com/download).

1. Na linha de comando, alterne para o diretório do aplicativo de exemplo e `build\libs` e, em seguida, digite os comandos a seguir para inicializar um repositório Git local.

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

## Exibir a definição de API no portal do Azure

Agora que você implantou uma API para seu aplicativo de API, você pode ver a definição de API no portal do Azure. Você começará reiniciando o *gateway*, que permite que o Azure reconheça que a definição de API de um aplicativo de API foi alterada. O gateway é um aplicativo Web que trata da administração da API e da autorização para os Aplicativos de API em um grupo de recursos.

6. No portal do Azure, vá até a folha **Aplicativo de API** para o aplicativo de API que você criou anteriormente e clique no link **Gateway**.

	![][click-gateway]

7. Na folha **Gateway**, clique em **Reiniciar**. Agora você pode fechar esta folha.

	![][restart-gateway]

8. Na folha **Aplicativo de API**, clique em **Definição de API**.

	![][api-definition-click]

	A folha **Definição de API** mostra um método Get.

	![][api-definition-blade]

## Executar o aplicativo de exemplo no Azure

No portal do Azure, vá até a folha **Host de aplicativo de API** para seu aplicativo de API e clique em **Procurar** .

![][browse-api-app-page]

O navegador exibe a home page que vimos anteriormente, quando você executou o aplicativo de exemplo localmente.

## Próximas etapas

Você implantou um aplicativo Web Java que usa um back-end do aplicativo de API para o Azure. Para obter mais informações sobre como usar o Java no Azure, consulte o [Centro de desenvolvedores do Java](/develop/java/).

Você pode experimentar esse aplicativo de API de exemplo no [Serviço TryApp](http://tryappservice.azure.com)

[portal-quick-create]: ./media/app-service-api-java-api-app/portal-quick-create.png
[portal-create-api]: ./media/app-service-api-java-api-app/portal-create-api.png
[api-app-blade]: ./media/app-service-api-java-api-app/api-app-blade.png
[api-app-folder-browse]: ./media/app-service-api-java-api-app/api-app-folder-browse.png
[api-app-host]: ./media/app-service-api-java-api-app/api-app-host.png
[deployment-part]: ./media/app-service-api-java-api-app/continuous-deployment.png
[set-api-app-access-level]: ./media/app-service-api-java-api-app/set-api-app-access.png
[setup-git-publishing]: ./media/app-service-api-java-api-app/local-git-repo.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[git-url]: ./media/app-service-api-java-api-app/git-url.png
[apiapp-json]: ./media/app-service-api-java-api-app/apiapp-json.png
[app-java]: ./media/app-service-api-java-api-app/app-java.png
[sample-api-app-page]: ./media/app-service-api-java-api-app/sample-api-app-page.png
[browse-api-app-page]: ./media/app-service-api-java-api-app/browse-api-app-page.png
[api-app-enable-java]: ./media/app-service-api-java-api-app/api-app-enable-java.png
[click-gateway]: ./media/app-service-api-java-api-app/clickgateway.png
[restart-gateway]: ./media/app-service-api-java-api-app/gatewayrestart.png
[api-definition-click]: ./media/app-service-api-java-api-app/apidef.png
[api-definition-blade]: ./media/app-service-api-java-api-app/apidefblade.png
 

<!---HONumber=Sept15_HO4-->