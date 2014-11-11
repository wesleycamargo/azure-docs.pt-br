<properties linkid="develop-node-create-a-website-mac" urlDisplayName="Website" pageTitle="Create a Node.js website on Mac - Azure tutorials" metaKeywords="Azure create website Node, Azure deploy website Node, website Node.js, Node website" description="Learn how to build and deploy a Node.js website in Azure. Sample code is written in Java." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Compilar e implantar um site do node.js no Azure.

Este tutorial mostra como criar um aplicativo [Nó][Nó] e implantá-lo em um Site do Azure usando [Git][Git]. As instruções deste tutorial podem ser seguidas em qualquer sistema operacional que seja capaz de executar o nó.

Se você preferir assistir a um vídeo, o clipe à direita segue as mesmas etapas deste tutorial.

A seguinte é uma captura de tela do aplicativo concluído:

![Navegador exibindo a mensagem "Hello World".][Navegador exibindo a mensagem "Hello World".]

## Criar um Site do Azure e habilitar a publicação Git

Siga estas etapas para criar um Site do Azure e habilitar a publicação Git para o site.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong>
<p>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p>
</div>

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].

2.  Clique no ícone **+ NOVO** na parte inferior esquerda do portal.

    ![Portal do Azure com o link +NOVO realçado.][Portal do Azure com o link +NOVO realçado.]

3.  Clique em **SITE**, em seguida clique em **CRIAÇÃO RÁPIDA**. Insira um valor para **URL** e selecione o datacenter para seu site no menu suspenso **REGIÃO**. Clique na marca de seleção na parte inferior da caixa de diálogo.

    ![Caixa de diálogo Criação Rápida][Caixa de diálogo Criação Rápida]

4.  Assim que o status do site for alterado para **Executando**, clique no nome do site para acessar o **Painel**

    ![Abrir o painel do site][Abrir o painel do site]

5.  Na parte inferior direita da página Quickstart, selecione **Configurar a implantação a partir do controle de origem**.

    ![Configurar a publicação do Git][Configurar a publicação do Git]

6.  Quando receber a pergunta "Onde está seu código-fonte?" selecionar **Repositório Git Local** e, em seguida, clique na seta.

    ![onde está o código-fonte][onde está o código-fonte]

7.  Para habilitar a publicação do Git, você deve fornecer um nome de usuário e uma senha. Se você habilitou a publicação de um Site do Azure anteriormente, o nome do usuário ou senha não será solicitado. Em vez disso, um repositório Git será criado usando o nome do usuário e a senha especificados anteriormente. Anote o nome do usuário e a senha, pois serão usados para a publicação Git em todos os Sites do Azure que você criar.

    ![Caixa de diálogo solicitando o nome do usuário e a senha.][Caixa de diálogo solicitando o nome do usuário e a senha.]

8.  Quando o repositório Git estiver pronto, você receberá instruções sobre os comandos Git a serem usados para configurar um repositório local e enviar os arquivos ao Azure.

    ![Instruções da implantação do Git retornadas após a criação de um repositório para o site.][Instruções da implantação do Git retornadas após a criação de um repositório para o site.]

## Criar e testar o aplicativo localmente

Nesta seção, você criará um arquivo **server.js** contendo o exemplo 'hello world' do [nodejs.org][Nó]. Este exemplo foi modificado a partir do exemplo original adicionando process.env.PORT como a porta de escuta ao executar em um Site do Azure.

1.  Usando um editor de texto, crie um novo arquivo chamado **server.js** no diretório **helloworld**. Se o diretório **helloworld** não existir, crie-o.
2.  Adicione o seguinte como o conteúdo do arquivo **server.js** e salve-o:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3.  Abra a linha de comando e use o seguinte comando para iniciar a página da web localmente:

        node server.js

4.  Abra o navegador da Web e navegue até <http://localhost:1337>. Uma página da web exibindo "Hello World" aparecerá conforme mostrado na captura de tela abaixo:

    ![Navegador exibindo a mensagem "Hello World".][1]

## Publicar seu aplicativo

1.  Na linha de comando, altere para o diretório **helloworld** e digite os seguintes comandos para inicializar um repositório Git local.

        git init

    <div class="dev-callout"><strong>Comando Git indispon&iacute;vel?</strong>
<p><a href="http://git-scm.com/" target="_blank">O Git</a> &eacute; um sistema de controle de vers&atilde;o distribu&iacute;do que voc&ecirc; pode usar para implantar seu Site do Azure. Para obter instru&ccedil;&otilde;es de instala&ccedil;&atilde;o para sua plataforma, consulte <a href="http://git-scm.com/download" target="_blank">a p&aacute;gina de download do Git</a>.</p>
</div>

2.  Use os comandos a seguir para adicionar arquivos ao repositório:

        git add .
        git commit -m "initial commit"

3.  Adicione um Git remoto para enviar atualizações por push ao Site do Azure criado anteriormente usando o seguinte comando:

        git remote add azure [URL for remote repository]

    ![Instruções da implantação do Git retornadas após a criação de um repositório para o site.][Instruções da implantação do Git retornadas após a criação de um repositório para o site.]

4.  Envie as alterações por push ao Azure usando o seguinte comando:

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

    ![Status da implantação do Git no portal][Status da implantação do Git no portal]

5.  Navegue até o seu site usando o botão **Procurar** no Site do Azure no portal de gerenciamento.

## Publicar alterações em seu aplicativo

1.  Abra o arquivo **Server.js** em um editor de texto e altere 'Hello World\\n' para 'Hello Azure\\n'. Salve o arquivo.
2.  Na linha de comando, altere para o diretório **helloworld** e execute os seguintes comandos:

        git add .
        git commit -m "changing to hello azure"
        git push azure master

    Será solicitada a senha que você criou anteriormente. Se navegar até a guia de implantações de seu Site do Azure dentro do portal de gerenciamento, você verá o histórico de implantação atualizado:

    ![Status da implantação do Git atualizado no portal][Status da implantação do Git atualizado no portal]

3.  Navegue até o site usando o botão **Procurar** e observe se as atualizações foram aplicadas.

    ![Página da web exibindo 'Hello Azure'][Navegador exibindo a mensagem "Hello World".]

4.  Você pode reverter para a implantação anterior selecionando-a na guia "Implantações" do Site do Azure dentro do portal de gerenciamento e usando o botão **Reimplantar**.

## Próximas etapas

Embora as etapas deste artigo usem o Portal do Azure para criar um site, você também pode usar as [Ferramentas de Linha de Comando do Azure para Mac e Linux][Ferramentas de Linha de Comando do Azure para Mac e Linux] para executar as mesmas operações.

O Node.js fornece um rico ecossistema de módulos que podem ser usadas por seus aplicativos. Para saber como os Sites do Azure funcionam com módulos, consulte [Usando módulos do Node.js com aplicativos do Azure (a página pode estar em inglês)][Usando módulos do Node.js com aplicativos do Azure (a página pode estar em inglês)].

Para saber mais sobre as versões do Node.js que são fornecidas com o Azure e como especificar a versão a ser usada com seu aplicativo, consulte [Especificando uma versão do Node.js em um aplicativo do Azure][Especificando uma versão do Node.js em um aplicativo do Azure].

Se você tiver problemas com seu aplicativo após ele ter sido implantado no Azure, consulte [Como depurar um aplicativo Node.js em Sites do Azure][Como depurar um aplicativo Node.js em Sites do Azure] para obter informações sobre como diagnosticar o problema.

## Recursos adicionais

-   [PowerShell do Azure][PowerShell do Azure]
-   [Ferramentas de linha de comando do Azure para Mac e Linux][Ferramentas de Linha de Comando do Azure para Mac e Linux]

  [Nó]: http://nodejs.org
  [Git]: http://git-scm.com
  [Navegador exibindo a mensagem "Hello World".]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Caixa de diálogo Criação Rápida]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
  [Abrir o painel do site]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
  [Configurar a publicação do Git]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
  [onde está o código-fonte]: ./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png
  [Caixa de diálogo solicitando o nome do usuário e a senha.]: ./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png
  [Instruções da implantação do Git retornadas após a criação de um repositório para o site.]: ./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png
  [1]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
  [Status da implantação do Git no portal]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png
  [Status da implantação do Git atualizado no portal]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png
  [Ferramentas de Linha de Comando do Azure para Mac e Linux]: /pt-br/documentation/articles/xplat-cli/
  [Especificando uma versão do Node.js em um aplicativo do Azure]: /pt-br/documentation/articles/nodejs-specify-node-version-azure-apps/
  [Como depurar um aplicativo Node.js em Sites do Azure]: /pt-br/documentation/articles/web-sites-nodejs-debug/
  [PowerShell do Azure]: /pt-br/documentation/articles/install-configure-powershell/
