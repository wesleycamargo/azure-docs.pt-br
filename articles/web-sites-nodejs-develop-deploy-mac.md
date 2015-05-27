<properties 
	pageTitle="Criar um aplicativo Web do Node.js no Serviço de Aplicativo do Azure" 
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
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mwasson"/>

# Compilar e implantar um aplicativo Web do Node.js no Serviço de Aplicativo do Azure

Este tutorial mostra como criar um aplicativo de [Nó][nodejs.org] e implantá-lo em [Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) usando [Git]. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional que seja capaz de executar o nó.

A seguinte é uma captura de tela do aplicativo concluído:

![Navegador exibindo a mensagem "Hello World".][helloworld-completed]

##Criar um aplicativo Web e habilitar a publicação Git

Siga estas etapas para criar um aplicativo Web e habilitar a publicação Git.

> [AZURE.NOTE]Para concluir este tutorial, você precisa de uma conta do Microsoft Azure. Se não tiver uma conta, você poderá [ativar os benefícios de assinante MSDN](/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) ou [inscrever-se para uma avaliação gratuita](/pt-br/pricing/free-trial/?WT.mc_id=A261C142F).

1. Faça logon no [Portal do Azure](https://portal.azure.com).
 
2. Clique no ícone **+ NOVO** na parte inferior esquerda do portal.

3. Clique em **Web + Móvel** e, em seguida, em **Aplicativo Web**.

    ![][portal-quick-create]

4. Insira um valor para **URL**.

5. Selecione um plano de serviço de aplicativo ou crie um novo. Se você criar um novo plano, selecione a camada de preços, localização e outras opções.

    ![][portal-quick-create2]

6. Clique em **Criar**.

7. Depois que o status for alterado para **Em execução**, o portal abrirá automaticamente a folha de seu aplicativo Web. Você também poderá acessar a folha clicando em **Procurar**.

	![][go-to-dashboard]

8. Clique em **Implantação**. Talvez seja necessário rolar para ver essa parte da folha.

	![][deployment-part]

9. Clique em **Escolher fonte** e, em seguida, em **Repositório Git Local**. Clique em **OK**.

	![][setup-git-publishing]


10. Clique na parte de **credenciais de implantação** (contornada em vermelho abaixo). Digite um nome de usuário e senha. Clique em **Salvar**. Se habilitou a publicação para um aplicativo Web anteriormente, você não precisa realizar esta etapa.

	![][deployment-credentials]


11. Para publicar, você enviará por push para um repositório remoto Git. Encontre a URL do repositório, clique em **Todas as Configurações** e, em seguida, clique em **Propriedades**. A URL está listada em "URL DO GIT".

	![][git-url]

##Criar e testar o aplicativo localmente

Nesta seção, você criará um arquivo **server.js** contendo o exemplo 'hello world' do [nodejs.org]. Este exemplo foi modificado no exemplo original adicionando process.env.PORT como a porta a escutar ao executar em um aplicativo Web do Azure.

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

4. Abra o navegador e acesse http://localhost:1337. Uma página da web exibindo "Hello World" aparecerá conforme mostrado na captura de tela abaixo:

    ![Navegador exibindo a mensagem "Hello World".][helloworld-localhost]

##Publicar seu aplicativo

1. Na linha de comando, altere para o diretório **helloworld** e digite os seguintes comandos para inicializar um repositório Git local. 

		git init

	> [AZURE.NOTE]**Comando Git indisponível?** [O Git](http://git-scm.com/%20target="_blank) é um sistema de controle de versão distribuído que você pode usar para implantar seu Site do Azure. Para obter instruções de instalação para sua plataforma, consulte [a página de download do Git](http://git-scm.com/download%20target="_blank").

2. Use os comandos a seguir para adicionar arquivos ao repositório:

		git add .
		git commit -m "initial commit"

3. Usando o comando a seguir, adicione um Git remoto para enviar atualizações por push ao aplicativo Web criado anteriormente:

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
    

5. Para exibir seu aplicativo, clique no botão **Procurar** na parte **Aplicativo Web** contida no portal de gerenciamento.

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

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

##Próximas etapas

Embora as etapas deste artigo usem o Portal do Azure para criar um site, você também pode usar as [Ferramentas de Linha de Comando do Azure para Mac e Linux](xplat-cli.md) para executar as mesmas operações.

O Node.js fornece um rico ecossistema de módulos que podem ser usadas por seus aplicativos. Para saber como os Aplicativos Web funcionam com módulos, consulte [Usando módulos do Node.js com aplicativos do Azure](nodejs-use-node-modules-azure-apps.md).

Para saber mais sobre as versões do Node.js que são fornecidas com o Azure e como especificar a versão a ser usada com seu aplicativo, consulte [Especificando uma versão do Node.js em um aplicativo do Azure](nodejs-specify-node-version-azure-apps.md).

Se você tiver problemas com seu aplicativo após ele ter sido implantado no Azure, consulte [Como depurar um aplicativo Node.js em Sites do Azure](web-sites-nodejs-debug.md) para obter informações sobre como diagnosticar o problema.


##Recursos adicionais

* [PowerShell do Azure](install-configure-powershell.md)
* [Ferramenta de linha de comando do Azure para Mac e Linux](xplat-cli.md)

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, consulte: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)


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

<!--HONumber=54-->