<properties
	pageTitle="Criar um aplicativo Web Node.js no Serviço de Aplicativo do Azure | Microsoft Azure"
	description="Aprenda a compilar e implantar um aplicativo Web do Node.js no Azure."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.date="08/18/2015"
	ms.author="mwasson"/>

# Compilar e implantar um aplicativo Web do Node.js no Serviço de Aplicativo do Azure

Este tutorial mostra como criar um aplicativo [Nó][nodejs.org] e implantá-lo no [recurso Aplicativos Web no Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) usando [Git]. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional que seja capaz de executar o nó.

A seguinte é uma captura de tela do aplicativo concluído:

![Navegador exibindo a mensagem "Hello World".][helloworld-completed]

##Criar um aplicativo Web e habilitar a publicação Git

Siga estas etapas para criar um aplicativo Web e habilitar a publicação Git.

> [AZURE.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [ativar os benefícios de assinante MSDN](/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [inscrever-se para uma avaliação gratuita](/pt-br/pricing/free-trial/?WT.mc_id=A261C142F).

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Clique no ícone **+ NOVO** no canto superior esquerdo do portal.

3. Clique em **Web + Celular** e em **Aplicativo Web**.

    ![][portal-quick-create]

4. Insira um valor para **URL**.

5. Selecione um plano de serviço de aplicativo ou crie um novo. Se você criar um novo plano, selecione a camada de preços, localização e outras opções.

    ![][portal-quick-create2]

6. Clique em **Criar**.

7. Depois que o status for alterado para **Em execução**, o portal abrirá automaticamente a folha de seu aplicativo Web. Você também poderá acessar a folha clicando em **Procurar**.

	![][go-to-dashboard]

8. Clique em **Implantação**. Talvez seja necessário rolar para ver essa parte da folha.

	![][deployment-part]

9. Clique em **Escolher Fonte**, em **Repositório Git Local** e em **OK**.

	![][setup-git-publishing]


10. Clique na parte de **credenciais de implantação** (contornada em vermelho abaixo). Digite um nome de usuário e senha. Clique em **Salvar**. Se você tiver habilitado a publicação em um aplicativo Web anteriormente, não precisará realizar esta etapa.

	![][deployment-credentials]


11. Para publicar, você enviará por push para um repositório remoto Git. Encontre a URL do repositório, clique em **Todas as Configurações** e em **Propriedades**. A URL está listada em **URL DO GIT**.

	![][git-url]

##Criar e testar o aplicativo localmente

Nesta seção, você criará um arquivo **server.js** com o exemplo 'Hello World' de [nodejs.org]. Este exemplo foi modificado no exemplo original adicionando process.env.PORT como a porta a escutar ao executar em um aplicativo Web do Azure.

1. Usando um editor de texto, crie um novo arquivo chamado **server.js** no diretório **helloworld**. Se o diretório **helloworld** não existir, crie-o.

2. Adicione o seguinte como o conteúdo do arquivo **server.js** e salve-o:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Abra a linha de comando e use o seguinte comando para iniciar o aplicativo Web localmente:

        node server.js

4. Abra o navegador e acesse http://localhost:1337. Aparecerá uma página da Web exibindo "Hello World", como mostrado na captura de tela abaixo:

    ![Navegador exibindo a mensagem "Hello World".][helloworld-localhost]

##Publicar seu aplicativo

1. Na linha de comando, altere para o diretório **helloworld** e insira o seguinte comando para inicializar um repositório Git local.

		git init

	> [AZURE.NOTE]**Comando Git indisponível?**
	[O Git](http://git-scm.com/%20target="_blank) é um sistema de controle de versão distribuído que você pode usar para implantar seu Site do Azure. Para obter instruções de instalação para sua plataforma, consulte [a página de download do Git](http://git-scm.com/download%20target="_blank").

2. Use os comandos a seguir para adicionar arquivos ao repositório:

		git add .
		git commit -m "initial commit"

3. Adicione um comando remoto Git para enviar atualizações por push ao aplicativo Web criado anteriormente usando este comando:

		git remote add azure [URL for remote repository]


4. Envie as alterações por push ao Azure usando o seguinte comando:

		git push azure master

	Será solicitada a senha que você criou anteriormente. A saída deverá ser semelhante a esta:

		Counting objects: 3, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (3/3), 374 bytes, done.
		Total 3 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '5ebbe250c9'.
		remote: Preparing files for deployment.
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		 * [new branch]      master -> master


5. Para exibir seu aplicativo, clique no botão **Procurar** na parte **Aplicativo Web** contida no portal do Azure.

##Publicar alterações em seu aplicativo

1. Abra o arquivo **Server.js** em um editor de texto e altere 'Hello World\\n' para 'Hello Azure\\n'. Salve o arquivo.
2. Na linha de comando, altere para o diretório **helloworld** e execute os seguintes comandos:

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Será solicitada a senha que você criou anteriormente.

3. Navegue até seu aplicativo clicando em **Procurar** e observe se as atualizações foram aplicadas.

	![Página da web exibindo 'Hello Azure'][helloworld-completed]

4. Você pode reverter para a implantação anterior selecionando-a em **Implantações**.

>[AZURE.NOTE]Se você quiser ter uma introdução ao Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751). Lá, você poderá criar imediatamente um aplicativo Web de curta duração inicial no Serviço de Aplicativo – sem exigência de cartão de crédito e sem compromissos.

##Próximas etapas

Embora as etapas deste artigo usem o portal do Azure para criar um aplicativo Web, você também poderá usar a [Interface de Linha de Comando do Azure](../xplat-cli.md) para executar as mesmas operações.

O Node.js fornece um rico ecossistema de módulos que podem ser usadas por seus aplicativos. Para saber como os Aplicativos Web funcionam com módulos, confira [Usando módulos do Node.js com aplicativos do Azure](../nodejs-use-node-modules-azure-apps.md).

Para saber mais sobre as versões do Node.js que são fornecidas com o Azure e como especificar a versão a ser usada com seu aplicativo, consulte [Especificando uma versão do Node.js em um aplicativo do Azure](../nodejs-specify-node-version-azure-apps.md).

Se você tiver problemas com seu aplicativo depois de implantá-lo no Azure, confira [Como depurar um aplicativo Web Node.js no Serviço de Aplicativo do Azure](web-sites-nodejs-debug.md) para obter informações sobre como diagnosticar o problema.


##Recursos adicionais

* [PowerShell do Azure](../install-configure-powershell.md)
* [Interface de linha de comando do Azure](../xplat-cli.md)

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, confira: [Serviço de Aplicativo do Azure e os serviços existentes do Azure](http://go.microsoft.com/fwlink/?LinkId=529714).
* Para obter um guia sobre a alteração do portal antigo para o novo portal, confira: [Referência para a navegação no portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715)


[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com


[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png

[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png


[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png

[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png

[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png

[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png


[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png

<!---HONumber=August15_HO9-->