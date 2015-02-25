<properties 
	pageTitle="Criar um site do Node. js no Mac - tutoriais do Azure" 
	description="Aprenda a criar e implantar um site do Node.js no Azure. Código de exemplo é escrito em Java." 
	services="web-sites" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>






# Compilar e implantar um site do node.js no Azure

Este tutorial mostra como criar um aplicativo [Nó][nodejs.org] e implantá-lo em um Website do Azure usando [Git]. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional que seja capaz de executar o nó.

Se você preferir assistir a este tutorial como um vídeo, o clipe a seguir mostra etapas semelhantes:
[AZURE.VIDEO create-a-nodejs-site-deploy-from-github]
 
A seguinte é uma captura de tela do aplicativo concluído:

![A browser displaying the 'Hello World' message.][helloworld-completed]

##Criar um Site do Azure e habilitar a publicação Git

Siga estas etapas para criar um Site do Azure e habilitar a publicação Git para o site.

> [AZURE.NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Avaliação gratuita do Azure</a>.
> 
> Se você quiser iniciar com Websites do Azure antes de assinar uma conta, acesse <a href="https://trywebsites.azurewebsites.net/?language=nodejs">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um site de iniciante ASP.NET de vida curta nos Websites do Azure gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.

1. Faça logon no [Portal de Gerenciamento do Azure].

2. Clique no ícone **+ NOVO** na parte inferior esquerda do portal

    ![The Azure Portal with the +NEW link highlighted.][portal-new-website]

3. Clique em **SITE**, em seguida clique em **CRIAÇÃO RÁPIDA**. Insira um valor para **URL** e selecione o datacenter para seu site no menu suspenso **REGIÃO**. Clique na marca de seleção na parte inferior da caixa de diálogo.

    ![The Quick Create dialog][portal-quick-create]

4. Assim que o status do site for alterado para **Executando**, clique no nome do site para acessar o **Painel**

	![Open web site dashboard][go-to-dashboard]

6. Na parte inferior direita da página Quickstart, selecione **Configurar a implantação a partir do controle de origem**.

	![Set up Git publishing][setup-git-publishing]

6. Quando receber a pergunta "Onde está seu código-fonte?" selecionar **Repositório Git Local** e, em seguida, clique na seta.

	![where is your source code][where-is-code]

7. Para habilitar a publicação do Git, você deve fornecer um nome de usuário e uma senha. Se você habilitou a publicação de um Site do Azure anteriormente, o nome do usuário ou senha não será solicitado. Em vez disso, um repositório Git será criado usando o nome do usuário e a senha especificados anteriormente. Anote o nome do usuário e a senha, pois serão usados para a publicação Git em todos os Sites do Azure que você criar.

	![The dialog prompting for user name and password.][portal-git-username-password]

8. Quando o repositório Git estiver pronto, você receberá instruções sobre os comandos Git a serem usados para configurar um repositório local e enviar por push os arquivos ao Azure.

	![Git deployment instructions returned after creating a repository for the website.][git-instructions]

##Criar e testar o aplicativo localmente

Nesta seção, você criará um arquivo **Server. js** contendo o exemplo 'hello world' do [nodejs.org]. Este exemplo foi modificado a partir do exemplo original adicionando process.env.PORT como a porta de escuta ao executar em um Site do Azure.

1. Usando um editor de texto, crie um novo arquivo chamado **server.js** no diretório **helloworld**. Se o diretório **helloworld** não existir, crie-o.
2. Adicione o seguinte como o conteúdo do arquivo **server.js** e salve-o:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Abra a linha de comando e use o seguinte comando para iniciar a página da web localmente:

        node server.js

4. Abra o navegador da Web e navegue até http://localhost:1337. Uma página da web exibindo "Hello World" aparecerá conforme mostrado na captura de tela abaixo:

    ![A browser displaying the 'Hello World' message.][helloworld-localhost]

##Publicar seu aplicativo

1. Na linha de comando, altere para o diretório **helloworld** e digite os seguintes comandos para inicializar um repositório Git local. 

		git init

	> [AZURE.NOTE] **Comando Git indisponível?**
	[Git](http://git-scm.com/%20target="_blank) é um sistema de controle de versão distribuído que você pode usar para implantar seu Site do Azure. Para obter instruções de instalação para sua plataforma, consulte [a página de download do Git](http://git-scm.com/download%20target="_blank").

2. Use os comandos a seguir para adicionar arquivos ao repositório:

		git add .
		git commit -m "initial commit"

3. Adicione um Git remoto para enviar atualizações por push ao Site do Azure criado anteriormente usando o seguinte comando:

		git remote add azure [URL for remote repository]

    ![Git deployment instructions returned after creating a repository for the web site.][git-instructions]
 
4. Envie as alterações por push ao Azure usando o seguinte comando:

		git push azure master

	Você será solicitado a fornecer a senha que você criou anteriormente e verá a seguinte saída:

		Password for 'testsite.scm.azurewebsites.net':
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
    
	Se você navegar até a guia de implantações de seu Site do Azure dentro do portal de gerenciamento, você verá a primeira implantação no histórico de implantações:

	![Git deployment status on the portal][git-deployments-first] 

5. Navegue até o seu site usando o botão **Procurar** no seu Site do Azure dentro do portal de gerenciamento.

##Publicar alterações em seu aplicativo

1. Abra o arquivo **server.js** em um editor de texto e altere 'Hello World\n' para 'Hello Azure\n'. Salve o arquivo.
2. Na linha de comando, altere para o diretório **helloworld** e execute os seguintes comandos:

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Será solicitada a senha que você criou anteriormente. Se navegar até a guia de implantações de seu Site do Azure dentro do portal de gerenciamento, você verá o histórico de implantação atualizado:
	
	![Git deployment status updated on the portal][git-deployments-second]

3. Navegue até o site usando o botão **Procurar** e observe se as atualizações foram aplicadas.

	![A web page displaying 'Hello Azure'][helloworld-completed]

4. Você pode reverter para a implantação anterior selecionando-a na guia "Implantações" do Site do Azure dentro do portal de gerenciamento e usando o botão **Reimplantar**.

##Próximas etapas

Embora as etapas deste artigo usem o Portal do Azure para criar um site, você também pode usar as [Ferramenta de Linha de Comando do Azure para Mac e Linux] para executar as mesmas operações.

O Node.js fornece um rico ecossistema de módulos que podem ser usadas por seus aplicativos. Para saber como os Sites do Azure funcionam com módulos, consulte [Usando módulos do Node.js com aplicativos do Azure](/pt-br/documentation/articles/nodejs-use-node-modules-azure-apps/).

Para saber mais sobre as versões do Node.js que são fornecidas com o Azure e como especificar a versão a ser usada com seu aplicativo, consulte [Especificando uma versão do Node.js em um aplicativo do Azure](/pt-br/documentation/articles/nodejs-specify-node-version-azure-apps/).

Se você tiver problemas com seu aplicativo após ele ter sido implantado no Azure, consulte [Como depurar um aplicativo Node.js nos Sites do Azure](/pt-br/documentation/articles/web-sites-nodejs-debug/) for information on diagnosing the problem.


##Recursos adicionais

* [PowerShell do Azure]
* [Ferramenta de Linha de Comando do Azure para Mac e Linux] 

[PowerShell do Azure]: /pt-br/documentation/articles/install-configure-powershell/

[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com

[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
[Ferramenta de Linha de Comando do Azure para Mac e Linux]: /pt-br/documentation/articles/xplat-cli/

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-new-website]: ./media/web-sites-nodejs-develop-deploy-mac/plus-new.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-git-username-password]: ./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png
[git-instructions]: ./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png

[git-deployments-first]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png
[git-deployments-second]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png

[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[where-is-code]: ./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png


<!--HONumber=42-->
