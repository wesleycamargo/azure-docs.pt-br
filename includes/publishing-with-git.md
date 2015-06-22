# Implantação contínua usando GIT no Serviço de Aplicativo do Azure

[O Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) dá suporte a implantação contínua para aplicativos Web por meio de ferramentas de repositório e de controle do código-fonte como BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial e TFS. Você pode usar essas ferramentas para manter o conteúdo e o código de seu aplicativo, e enviar as alterações de modo rápido e fácil ao seu aplicativo Web quando desejar.

Neste artigo, você aprenderá a usar o Git para publicar diretamente do seu computador local em Aplicativos Web (no Azure, esse método de publicação é chamado de **Git local**). Você também saberá como habilitar a implantação contínua de sites de repositório, como BitBucket, CodePlex, DropBox, GitHub ou Mercurial. Para obter mais informações sobre como usar o TFS para implantação contínua, consulte [Envio contínuo ao Azure por meio do Visual Studio Online].

> [AZURE.NOTE] Muitos dos comandos Git descritos neste artigo são executados automaticamente ao criar um aplicativo Web usando as [Ferramentas de Linha de Comando do Azure para Mac e Linux](/develop/nodejs/how-to-guides/command-line-tools/).

<h2><a id="Step2"></a>Instalando o Git</h2>

As etapas necessárias para instalar o Git variam de acordo com o sistema operacional. Consulte [instalando o Git] para distribuições específicas do sistema operacional e orientações de instalação.

> [AZURE.NOTE] Em alguns sistemas operacionais, uma versão de linha de comando e outra com GUI do Git serão disponibilizados. As instruções fornecidas neste artigo usam a versão de linha de comando.

## <a id="Step2"></a>Etapa 2: Criar um repositório local

Execute as seguintes tarefas para criar um novo repositório do Git.

1. Crie um diretório chamado MyGitRepository para os arquivos de aplicativo Web e repositório git.

2. Abra uma linha de comando como **GitBash** (Windows) ou **Bash** (shell do Unix). Nos sistemas OS X, você pode acessar a linha de comando por meio do aplicativo **Terminal**.

3. Da linha de comando, mude para o diretório MyGitRepository.

		cd MyGitRepository

4. Use o seguinte comando para inicializar um novo repositório do Git:

		git init

	Isso deve retornar uma mensagem como **Inicializou vazio no repositório do Git em [path]**.

## <a id="Step3"></a>Etapa 3: Adicionar uma página da web

Aplicativos Web dão suporte a aplicativos criados em uma variedade de linguagens de programação. Para este exemplo, você usará um arquivo .html estático. Para obter informações sobre a publicação de aplicativos Web em outras linguagens de programação no Azure, consulte o [Azure Developer Center].

1. Usando um editor de texto, crie um novo arquivo chamado **index.html** na raiz do repositório do Git.

2. Adicione o seguinte como o conteúdo do arquivo index.html e salve-o.

		Hello Git!

3. Na linha de comando, verifique se você está na raiz do seu repositório Git. Depois, use o seguinte comando para adicionar o arquivo **index.html** para o repositório:

		git add index.html 

	> [AZURE.NOTE] Você pode encontrar ajuda sobre qualquer comando git digitando -ajuda ou --ajuda após o comando. Por exemplo, para obter opções de parâmetro para o comando add, digite 'git add -help' para obter ajuda de linha de comando ou 'git add --help' para obter ajuda mais detalhada.

4. Em seguida, confirme as alterações no repositório usando o comando a seguir:

		git commit -m "Adding index.html to the repository"

	You should see output similar to the following:

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

<h2><a id="Step4"></a>Habilitar o repositório de aplicativo Web</h2>

Execute as seguintes etapas para habilitar um repositório do Git em seu aplicativo Web usando o [Portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715):

1. Faça logon no [Portal do Azure].

2. Na lâmina de seu aplicativo Web, role para baixo até a seção **Implantação** e clique em **Configurar implantação contínua**. Clique em **Escolher fonte**, depois em **Repositório Git local** e em **OK**.  

2. Aguarde até que o processo de criação do aplicativo Web seja concluído, então selecione o aplicativo Web na lâmina Aplicativos Web.

	![An image displaying a selected web app](./media/publishing-with-git/azure1-local-git.png)

4. Se for a primeira vez que você configura um repositório no Azure, você precisa criar as credenciais de logon para ele. Você as usará para fazer logon no repositório do Azure e enviar por push as alterações do seu repositório Git Local. Na lâmina de seu aplicativo Web, clique em **Definir credenciais de implantação** e, em seguida, configure o nome de usuário de implantação e a senha. Quando terminar, clique em **OK**.

	![](./media/publishing-with-git/azure2-credentials.png)

<h2><a id="Step5"></a>Implantar o seu projeto</h2>

* [Copiar arquivos locais para o Azure (Git Local)](#Step6)
* [Implantar arquivos de um site de repositório como BitBucket, CodePlex, Dropbox, GitHub ou Mercurial](#Step7)
* [Implantar uma solução do Visual Studio a partir do BitBucket, CodePlex, Dropbox, GitHub ou Mercurial](#Step75)

Use as etapas a seguir para publicar seu aplicativo Web no Azure usando o Git local:

1. Na lâmina de seu aplicativo Web, na seção Implantação, clique em **Nenhuma implantação encontrada**.

	![](./media/publishing-with-git/azure3-repo-details.png)

	A **URL do Git** será a referência remota à qual implantar posteriormente, por meio do seu repositório local.

1. Usando a linha de comando, verifique se você está na raiz do seu repositório git local que contém o arquivo index.html criado anteriormente.

2. Use o comando remoto do Git para adicionar a referência remota listada na **URL do Git** a partir da etapa 1. O comando será semelhante ao seguinte:

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [AZURE.NOTE] O comando **remoto** adiciona uma referência com nome a um repositório remoto. Neste exemplo, ele cria uma referência denominada 'azure' para o repositório do aplicativo Web.

1. Use a seguinte da linha de comando para transferir o conteúdo do repositório atual do repositório local para o comando remoto do 'azure':

		git push azure master

	Será solicitada senha que você criou anteriormente ao redefinir suas credenciais de implantação no portal. Digite a senha (o Gitbash não ecoa asteriscos no console à medida que você digita a senha). Você deverá ver uma saída semelhante ao seguinte:

		Counting objects: 6, done.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (6/6), 486 bytes, done.
		Total 6 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '369a79c929'.
		remote: Preparing files for deployment.
		remote: Deployment successful.
		To https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
		* [new branch]		master -> master

	> [AZURE.NOTE] O repositório criado para o seu aplicativo Web espera solicitações por push para definir como alvo a ramificação <strong>mestre</strong> do seu repositório, que será usada como o conteúdo do aplicativo Web.

2. Volte para a lâmina de seu aplicativo Web no Portal do Azure. **Nenhuma implantação encontrada** deve ser alterada para **Implantação Ativa** com uma entrada de log de seu envio por push mais recente. 

	![](./media/publishing-with-git/azure4-deployed.png)

2. Clique na URL na parte superior do portal para verificar se o arquivo **index.html** foi implantado. Será exibida uma página contendo 'Hello Git!'.

	![A webpage containing 'Hello Git!'][hello-git]

3. Usando um editor de texto, altere o arquivo **index.html** de modo que ele contenha 'Sim!' e, em seguida, salve o arquivo.

4. Use os seguintes comandos na linha de comando para **add** (adicionar) e **commit** (confirmar) as alterações e, em seguida, **push** (envie) as alterações para o repositório remoto:

		git add index.html
		git commit -m "Celebration"
		git push azure master

	Uma vez que o **push** foi concluído, atualize o navegador (você terá que pressionar Ctrl + F5 para o navegador para atualizar corretamente) e observe que o conteúdo da página agora reflete as últimas mudanças de confirmação.

	![A webpage containing 'Yay!'][yay]

<h3><a id="Step7"></a>Implante arquivos por meio de um site de repositório como BitBucket, CodePlex, Dropbox, GitHub ou Mercurial</h3>

Enviar arquivos locais do Azure por push usando Git Local permite que você envie por push, manualmente, atualizações de um projeto local para o aplicativo Web do Azure, enquanto implantar por meio do BitBucket, CodePlex, Dropbox, GitHub ou Mercurial resulta em um processo de implantação contínua, no qual o Azure receberá as atualizações mais recentes do seu projeto.

Enquanto ambos os métodos resultam na implantação do seu projeto em Aplicativos Web, a implantação contínua é útil quando você tem várias pessoas trabalhando em um projeto e deseja ter certeza de que a versão mais recente será sempre publicada, independentemente de quem tiver feito a atualização mais recente. A implantação contínua também é útil se você estiver usando uma das ferramentas mencionadas acima como o repositório central do seu aplicativo.

A implantação de arquivos do GitHub, CodePlex ou BitBucket requer que você tenha publicado seu projeto local em um desses serviços. Para obter mais informações sobre a publicação de seu projeto nesses serviços, consulte [Criar um Repo (GitHub)], [Usando Git com CodePlex], [Criar um Repo (BitBucket)], [Usando o Dropbox para compartilhar repositórios do Git], ou [Início rápido: Mercurial].

1. Primeiro coloque seus arquivos do aplicativo Web no repositório selecionado que será usado para implantação contínua.

2. Na lâmina de seu aplicativo Web presente no portal, role para baixo até a seção **Implantação** e clique em **Configurar implantação contínua**. Clique em **Escolher Fonte** e depois em **GitHub**, por exemplo.  

	![](./media/publishing-with-git/azure6-setup-github.png)
	
2. Na lâmina **Implantação contínua**, clique em **Autorização** e depois em **Autorizar**. O portal do Azure redirecionará você ao site do repositório para concluir o processo de autorização. 

4. Quando terminar, volte para o portal do Azure e clique em **OK** na lâmina **Autorização**.

5. Na lâmina **Implantação contínua**, escolha a organização, o projeto e a ramificação por meio dos quais você deseja implantar. Quando terminar, clique em **OK**.
  
	![](./media/publishing-with-git/azure7-setup-github-configure.png)

	> [AZURE.NOTE] Ao habilitar a implantação contínua com GitHub ou BitBucket, os projetos públicos e privados serão exibidos.

O Azure cria uma associação com o repositório selecionado e extrai os arquivos da ramificação especificada. Depois que o processo for concluído, a seção **Implantação** da lâmina de seu aplicativo Web mostrará uma mensagem **Implantação Ativa**, que indica que a implantação foi bem-sucedida.

7. Nesse ponto, seu projeto foi implantado do seu repositório de escolha para seu aplicativo Web. Para verificar se o aplicativo Web está ativo, clique na **URL** na parte superior do portal. O navegador deve acessar o aplicativo Web.

8. Para verificar se a implantação contínua está ocorrendo no repositório de sua escolha, envie uma alteração por push para o repositório. Seu aplicativo Web deve ser atualizado para refletir as alterações logo após a conclusão do envio por push para o repositório. Você pode verificar se ele recebeu a atualização na página **Implantações** do seu aplicativo Web.

### <a id="Step75"></a>Implantar uma solução do Visual Studio a partir do BitBucket, CodePlex, Dropbox, GitHub ou Mercurial

Enviar uma solução do Visual Studio por push para Aplicativos Web no Serviço de Aplicativo do Azure é tão simples quanto enviar por push um arquivo index.html simples. O processo de implantação dos aplicativos Web simplifica todos os detalhes, incluindo as dependências de restauração NuGet e a criação dos binários do aplicativo. Você pode seguir as práticas recomendadas de controle do código-fonte, de manter apenas código no seu repositório Git e deixar que a implantação de aplicativos Web do Azure cuide do resto.

As etapas para enviar sua solução do Visual Studio para aplicativos Web é a mesma da [seção anterior](#Step7), desde que você configure sua solução e o repositório da seguinte maneira:

-	Na raiz do seu repositório, adicione um arquivo `.gitignore`, que especifique todos os arquivos e pastas que você quer excluir do seu repositório, como as pastas `Obj`, `Bin` e `packages` (consulte a [documentação gitignore](http://git-scm.com/docs/gitignore) para formatar informações). Por exemplo:

		[Oo]bj/
		[Bb]in/
		*.user
		/TestResults
		*.vspscc
		*.vssscc
		*.suo
		*.cache
		*.csproj.user
		packages/*
		App_Data/
		/apps
		msbuild.log
		_app/
		nuget.exe

	>[AZURE.NOTE] Se usar o GitHub, você pode gerar opcionalmente um arquivo .gitignore específico do Visual Studio ao criar seu repositório, que inclui todos os arquivos temporários comuns, resultados da compilação, etc. Você pode personalizá-lo para atender suas necessidades específicas.

-	Adicionar toda a árvore do diretório da solução para seu repositório, com o arquivo .sln na raiz do repositório.

-	Na sua solução do Visual Studio, [habilite a Restauração de Pacote NuGet](http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages) para que o Visual Studio restaure automaticamente os pacotes faltantes.

Assim que você tiver configurado seu repositório conforme descrito e tiver configurado seu aplicativo Web do Azure para publicação contínua por meio de um dos repositórios Git online, você poderá desenvolver seu aplicativo ASP.NET localmente no Visual Studio e implantar continuamente seu código, simplesmente enviando por push suas alterações a seu repositório Git online.

<h2>Desabilitar a implantação contínua</h2>

A implantação contínua pode ser desabilitada na lâmina **Implantações**. Na lâmina de seu aplicativo Web, na seção **Implantação**, clique em Implantação Ativa. Em seguida, clique em **Desconectar**.

![git-DisconnectFromGitHub](./media/publishing-with-git/azure5-disconnect.png)	

Depois de responder **Sim** à mensagem de confirmação, você pode retornar à lâmina de seu aplicativo Web e clicar em **Configurar implantação contínua**, caso deseje configurar a publicação por meio de outra origem.

## <a id="Step8"></a>Solucionar problemas

Estes são erros ou problemas comumente encontrados ao usar o Git para publicar em um aplicativo Web do Azure:

****

**Sintoma**: Não é possível acessar '[siteURL]': Falha ao conectar ao [scmAddress]

**Causa**: Este erro pode ocorrer se o aplicativo Web não estiver carregado e em execução.

**Resolução**: Inicie o aplicativo Web no portal do Azure. A implantação Git não funcionará a menos que o aplicativo Web esteja em execução. 


****

**Sintoma**: Não foi possível resolver o host 'hostname'

**Causa**: Este erro pode ocorrer se as informações de endereço inseridas ao criar o remoto do  'azure' estiverem incorretas.

**Resolução**: Use o comando `git remote -v` para listar todos os remotos, junto com a URL associada. Verifique se a URL do remoto do 'azure' está correta. Se necessário, remova e recrie esse remoto usando a URL correta.

****

**Sintoma**: não há referências em comum e nenhum especificado; fazendo nada. Talvez você deva especificar uma ramificação como 'master'.

**Causa**: Este erro pode ocorrer se você não especificar uma ramificação ao executar uma operação de envio de git e não tiver definido o valor de push.default usado pelo Git.

**Resolução**: execute a operação de envio novamente, especificando a ramificação mestre. Por exemplo:

	git push azure master

****

**Sintoma**: src refspec [branchname] não corresponde a nada.

**Causa**: Este erro pode ocorrer se você tentar enviar para uma ramificação que não seja a mestre no remoto do 'azure'.

**Resolução**: execute a operação de envio novamente, especificando a ramificação mestre. Por exemplo:

	git push azure master

****

**Sintoma**: Erro - Alterações confirmadas para o repositório remoto, mas o aplicativo Web não foi atualizado.

**Causa**: Este erro pode ocorrer se você estiver implantando um aplicativo do Node.js que contém um arquivo package.json que especifica módulos adicionais necessários.

**Resolução**: mensagens adicionais contendo 'npm ERR!' devem ser registradas antes deste erro e podem fornecer contexto adicional sobre a falha. A seguir estão as causas conhecidas desse erro e a mensagem 'npm ERR!' correspondente:

* **Arquivo malformado package.json**: npm ERR! Não foi possível ler as dependências.

* **Um módulo nativo que não tenha uma distribuição binária para o Windows**:

	* npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1

		OR

	* npm ERR! [modulename@version] preinstall: \`make || gmake\`


## Recursos adicionais

* [Como usar o PowerShell do Azure]
* [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]
* [Documentação do Git]
* [Kudu do Projeto](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE] Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de inscrever-se em uma conta do Azure, vá para [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), onde você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia para a mudança de sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia para a mudança do portal antigo para o novo portal, consulte: [Referência para navegação no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

[Centro de desenvolvedores do Azure]: http://azure.microsoft.com/develop/overview/
[Portal do Azure]: https://portal.azure.com
[Site do Git]: http://git-scm.com
[Instalando o Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Como usar o PowerShell do Azure]: ../articles/install-configure-powershell.md
[Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]: ../articles/xplat-cli.md
[Documentação do Git]: http://git-scm.com/documentation

[portal-select-website]: ./media/publishing-with-git/git-select-website.png
[git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
[git-instructions]: ./media/publishing-with-git/git-instructions.png
[portal-deployment-credentials]: ./media/publishing-with-git/git-deployment-credentials.png

[git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
[hello-git]: ./media/publishing-with-git/git-hello-git.png
[yay]: ./media/publishing-with-git/git-yay.png
[git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
[git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
[git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
[Criar um Repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Usando Git com CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Criar um Repo (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Início rápido - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[Usando Dropbox para compartilhar repositórios Git]: https://gist.github.com/trey/2722927
[Entrega contínua para o Azure usando o Visual Studio Online]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md

<!--HONumber=49-->