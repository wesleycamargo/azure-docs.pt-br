<properties linkid="develop-node-create-a-website-mac" urlDisplayName="Site" pageTitle="Criar um site do Node.js no Mac - Tutoriais do Azure" metaKeywords="Azure criar site nó, Azure implantar site nó, site do Node.js, nó site" description="Saiba como criar e implantar um site do Node.js no Azure. O código de exemplo é escrito em Java." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="Criar e implantar um site do Node.js no Azure" authors=""  solutions="" writer="" manager="" editor=""  />





# Criar e implantar um site do Node.js no Azure

Este tutorial mostra como criar um aplicativo [Nó] e implantá-lo em um Site do Azure usando [Git]. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional que seja capaz de executar o nó.

Se você preferir assistir a um vídeo, o clipe à direita segue as mesmas etapas deste tutorial.
 
A seguinte é uma captura de tela do aplicativo concluído:

![Navegador exibindo a mensagem "Hello World".][helloworld-completed]

##Criar um Site do Azure e habilitar a publicação Git

Siga estas etapas para criar um Site do Azure e habilitar a publicação Git para o site.

<div class="dev-callout"><strong>Observação</strong>
<p>Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Avaliação gratuita do Azure</a>.</p>
</div>

1. Faça logon no [Portal de Gerenciamento do Azure].

2. Clique no ícone **+ NOVO** na parte inferior esquerda do portal.

    ![Portal do Azure com o link +NOVO realçado.][portal-new-website]

3. Clique em **SITE** e em **CRIAÇÃO RÁPIDA**. Insira um valor para **URL** e selecione o datacenter para seu site no menu suspenso **REGIÃO**. Clique na marca de seleção na parte inferior da caixa de diálogo.

    ![Caixa de diálogo Criação Rápida][portal-quick-create]

4. Assim que o status do site for alterado para **Executando**, clique no nome do site para acessar o **Painel**

	![Abrir o painel do site][go-to-dashboard]

6. Na parte inferior direita da página Quickstart, selecione **Configurar a implantação a partir do controle de origem**.

	![Configurar a publicação do Git][setup-git-publishing]

6. Quando receber a pergunta "Onde está seu código-fonte?" selecionar **Repositório Git Local** e, em seguida, clique na seta.

	![onde está o código-fonte][where-is-code]

7. Para habilitar a publicação do Git, você deve fornecer um nome de usuário e uma senha. Se você habilitou a publicação de um Site do Azure anteriormente, o nome do usuário ou senha não será solicitado. Em vez disso, um repositório Git será criado usando o nome do usuário e a senha especificados anteriormente. Anote o nome do usuário e a senha, pois serão usados para a publicação Git em todos os Sites do Azure que você criar.

	![Caixa de diálogo solicitando o nome do usuário e a senha.][portal-git-username-password]

8. Quando o repositório Git estiver pronto, você receberá instruções sobre os comandos Git a serem usados para configurar um repositório local e enviar os arquivos por push ao Azure.

	![Instruções da implantação do Git retornadas após a criação de um repositório para o site.][git-instructions]

##Criar e testar o aplicativo localmente

Nesta seção, você criará um arquivo **server.js** contendo o exemplo 'hello world' do [nodejs.org]. Este exemplo foi modificado a partir do exemplo original adicionando process.env.PORT como a porta de escuta ao executar em um Site do Azure.

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
    [WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

4. Abra o navegador da Web e navegue até http://localhost:1337. Uma página da web exibindo "Hello World" aparecerá conforme mostrado na captura de tela abaixo:

    ![Navegador exibindo a mensagem "Hello World".][helloworld-localhost]

##Publicar o aplicativo

1. Na linha de comando, altere para o diretório **helloworld** e digite os seguintes comandos para inicializar um repositório Git local. 

		git init

	<div class="dev-callout"><strong>Comando Git indisponível?</strong>
	<p>O <a href="http://git-scm.com/" target="_blank">Git</a> é um sistema de controle de versão distribuído que você pode usar para implantar seu Site do Azure. Para obter instruções de instalação para sua plataforma, consulte <a href="http://git-scm.com/download" target="_blank">a página de download do Git</a>.</p>
	</div>

2. Use os comandos a seguir para adicionar arquivos ao repositório:

		git add .
		git commit -m "initial commit"

3. Adicione um Git remoto para enviar atualizações por push ao Site do Azure criado anteriormente usando o seguinte comando:

		git remote add azure [URL for remote repository]

    ![Instruções da implantação do Git retornadas após a criação de um repositório para o site.][git-instructions]
 
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

	![Status da implantação do Git no portal][git-deployments-first] 

5. Navegue até o seu site usando o botão **Procurar** no Site do Azure no portal de gerenciamento.

##Publicar alterações em seu aplicativo

1. Abra o arquivo **Server.js** em um editor de texto e altere 'Hello World\n' para 'Hello Azure\n'. Salve o arquivo.
2. Na linha de comando, altere para o diretório **helloworld** e execute os seguintes comandos:

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Será solicitada a senha que você criou anteriormente. Se navegar até a guia de implantações de seu Site do Azure dentro do portal de gerenciamento, você verá o histórico de implantação atualizado:
	
	![Status da implantação do Git atualizado no portal][git-deployments-second]

3. Navegue até o site usando o botão **Procurar** e observe se as atualizações foram aplicadas.

	![Página da web exibindo 'Hello Azure'][helloworld-completed]

4. Você pode reverter para a implantação anterior selecionando-a na guia "Implantações" do Site do Azure dentro do portal de gerenciamento e usando o botão **Reimplantar**.

##Próximas etapas

Embora as etapas deste artigo usem o Portal do Azure para criar um site, você também pode usar as [Ferramentas de Linha de Comando do Azure para Mac e Linux] para executar as mesmas operações.

O Node.js fornece um rico ecossistema de módulos que podem ser usadas por seus aplicativos. Para saber como os Sites do Azure funcionam com módulos, consulte [Usando módulos do Node.js com aplicativos do Azure (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/develop/nodejs/common-tasks/working-with-node-modules/).

Para saber mais sobre as versões do Node.js que são fornecidas com o Azure e como especificar a versão a ser usada com seu aplicativo, consulte [Especificando uma versão do Node.js em um aplicativo do Azure (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/develop/nodejs/common-tasks/specifying-a-node-version/).

Se você tiver problemas com seu aplicativo após ele ter sido implantado no Azure, consulte [Como depurar um aplicativo Node.js em Sites do Azure (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/Debug-Website/) para obter informações sobre como diagnosticar o problema.


##Recursos adicionais

* [Azure PowerShell]
* [Ferramentas de Linha de Comando do Azure para Mac e Linux]

[PowerShell do Azure]: /pt-br/develop/nodejs/how-to-guides/powershell-cmdlets/



[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
[Ferramentas de Linha de Comando do Azure para Mac e Linux]: /pt-br/develop/nodejs/how-to-guides/command-line-tools/

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

