# Publicando do Controle do código-fonte para sites do Azure.

Os Sites do Azure oferecem suporte à implantação contínua a partir do controle do código-fonte e ferramentas de repositório, como BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial e TFS. Você pode usar essas ferramentas para manter o conteúdo e o código de seu site e enviar as alterações de forma rápida e fácil ao seu site quando desejar.

Neste artigo, você aprenderá a usar o Git para publicar diretamente do seu computador local em um Site do Azure (no Azure, esse método de publicação é chamado de **Git local**). Você também saberá como habilitar a implantação contínua de sites de repositório, como BitBucket, CodePlex, DropBox, GitHub ou Mercurial. Para obter mais informações sobre como usar o TFS para implantação contínua, consulte [Envio contínuo ao Azure por meio do Visual Studio Online].

> [WACOM.NOTE] Muitos dos comandos Git descritos neste artigo são executados automaticamente ao criar um Site usando as <a href="/pt-br/develop/nodejs/how-to-guides/command-line-tools/">ferramentas de linha de comando do Azure para Mac e Linux</a>.

A tarefa inclui as seguintes etapas:

* [Instalar o Git.](#Step1)
* [Criar um repositório local](#Step2)
* [Adicionar uma página da web](#Step3)
* [Habilitar o repositório de site](#Step4)
* [Implantar o seu projeto](#Step5)
	* [Copiar arquivos locais para o Azure (Git Local)](#Step6)
	* [Implantar arquivos de um site de repositório como BitBucket, CodePlex, recados, GitHub ou Mercurial](#Step7)
* [Solucionar problemas](#Step8)

<h2><a id="Step2"></a>Instalando o Git</h2>

As etapas necessárias para instalar o Git variam de acordo com o sistema operacional. Consulte [Instalando o Git] para distribuições específicas de sistemas operacionais e orientações de instalação.

> [WACOM.NOTE] Em alguns sistemas operacionais, uma versão de linha de comando e outra com GUI do Git serão disponibilizados. As instruções fornecidas neste artigo usam a versão de linha de comando.

<h2><a id="Step2"></a>Criar um repositório local</h2>

Execute as seguintes tarefas para criar um novo repositório do Git.

1. Crie um diretório chamado MyGitRepository para os arquivos de site e repositório git.

2. Abra uma linha de comando, como **GitBash** (Windows) or **Bash** (shell do Unix). Nos sistemas OS X, você pode acessar a linha de comando por meio do aplicativo **Terminal**.

3. Da linha de comando, mude para o diretório MyGitRepository.

		cd MyGitRepository

4. Use o seguinte comando para inicializar um novo repositório do Git:

		git init

	Isso deve retornar uma mensagem como **Inicializou vazio no repositório do Git em [path]**.

<h2><a id="Step3"></a>Adicionar uma página da web</h2>

Os Sites do Azure oferecem suporte a aplicativos criados em uma variedade de linguagens de programação. Para este exemplo, você usará um arquivo .html estático. Para obter informações sobre a publicação de sites em outras linguagens de programação no Azure, consulte o [Azure Developer Center]

1. Usando um editor de texto, crie um novo arquivo chamado **index.html** na raiz do repositório do Git.

2. Adicione o seguinte como o conteúdo do arquivo index.html e salve-o.

		Hello Git!

3. Na linha de comando, verifique se você está na raiz do seu repositório Git. Depois, use o seguinte comando para adicionar o arquivo **index.html** para o repositório:

		git add index.html 

	> [WACOM.NOTE] Você pode encontrar ajuda sobre qualquer comando git digitando -ajuda ou --ajuda após o comando. Por exemplo, para obter opções de parâmetro para o comando adicionar, digite â €˜git add - helpâ€ ™ para ajuda de linha de comando ou adicione â€˜git add --help' para obter mais ajuda.

4. Em seguida, confirme as alterações no repositório usando o comando a seguir:

		git commit -m "Adding index.html to the repository"

	Você deverá ver uma saída semelhante ao seguinte:

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

<h2><a id="Step4"></a>Habilitar o repositório de site</h2>

Execute as seguintes etapas para ativar um repositório do Git em seu site usando o portal do Azure:

1. Faça logon no [portal do Azure].

2. No lado esquerdo da página, selecione **Sites da Web** e, em seguida, selecione o site no qual você deseja ativar um repositório.

	![Uma imagem exibindo um site selecionado][portal-select-website]

3. Selecione a guia **PAINEL**.

4. Na seção **visão rápida**, selecione **Configurar a implantação a partir do controle de código-fonte**.  A caixa de diálogo **CONFIGURAR IMPLANTAÇÃO** é exibida.

	![git-WhereIsYourSourceCode][git-WhereIsYourSourceCode]

4. Escolha **Git local** e, em seguida, clique na seta **Avançar**.

	
5. Após um pequeno intervalo, deverá aparecer uma mensagem informando que seu repositório está pronto. 

	![git-instructions][git-instructions]

<h2><a id="Step5"></a>Implantar o seu projeto</h2>

<h3><a id="Step6"></a>Copiar arquivos locais para o Azure (Git Local)</h3>

Nesse ponto, o portal exibe instruções para inicializar o repositório local e adicionar arquivos. Você já fez isso nas etapas anteriores deste tópico. No entanto, se você não tiver configurado suas credenciais de implantação, você deve seguir as instruções da etapa 3. Isso instrui você a seguir o link intitulado **Redefinir suas credenciais de implantação**.

Use as seguintes etapas para publicar seu site no Azure usando o git local:

1. Usando a linha de comando, verifique se você está na raiz do seu repositório git local que contém o arquivo index.html criado anteriormente.

2. Copie o comando add git remoto da etapa 3 das instruções enviadas pelo portal. Ele é semelhante ao seguinte comando:

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [WACOM.NOTE] O comando **remoto** adiciona uma referência com nome ao repositório remoto. Neste exemplo, ele cria uma referência denominada 'azure' para o repositório do site do Azure.

1. Use a seguinte da linha de comando para transferir o conteúdo do repositório atual do repositório local para o "azure" remoto:

		git push azure master

	Será solicitada senha que você criou anteriormente ao reinicializar suas credenciais de implantação no portal. Digite a senha (o Gitbash não ecoa asteriscos no console à medida que você digita a senha). Você deverá ver uma saída semelhante ao seguinte:

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

	> [WACOM.NOTE] O repositório criado para o seu site do Azure espera solicitações de envio para o destino da ramificação <strong>mestre</strong> do repositório, que será usada como o conteúdo do site da web.

2. No portal, clique no link **NAVEGAR** na parte inferior do portal para verificar se o **index. html** foi implantado. Será exibida uma página contendo 'Hello Git!'.

	![A webpage containing 'Hello Git!'][hello-git]

3. Usando um editor de texto, altere o arquivo **index.html** de modo que ele contenha 'Sim!' e, em seguida, salve o arquivo.

4. Use os seguintes comandos na linha de comando para **add** (adicionar) e **commit** (confirmar) as alterações e, em seguida, **push** (envie) as alterações para o repositório remoto:

		git add index.html
		git commit -m "Celebration"
		git push azure master

	Uma vez que o **push** foi concluído, atualize o navegador (você terá que pressionar Ctrl + F5 para o navegador para atualizar corretamente) e observe que o conteúdo da página agora reflete as últimas mudanças de confirmação.

	![A webpage containing 'Yay!'][yay]

<h3><a id="Step7"></a>Implantar arquivos de um site de repositório como BitBucket, CodePlex, recados, GitHub ou Mercurial</h3>

Copiar arquivos locais do Azure usando Git local permite que você envie atualizações manualmente de um projeto local para o Site do Azure ao implantar do BitBucket, CodePlex, recados, GitHub ou Mercurial. Isso resulta em um processo de implantação contínua no qual o Azure irá receber as atualizações mais recentes do seu projeto.

Enquanto ambos os métodos resultam na implantação do seu projeto em um Site do Azure, a implantação contínua é útil quando você tem várias pessoas trabalhando em um projeto e deseja ter certeza de que a versão mais recente é sempre publicada, independentemente de quem fez a atualização mais recente. A implantação contínua também é útil se você estiver usando uma das ferramentas mencionadas acima como o repositório central do seu aplicativo.

A implantação de arquivos do GitHub, CodePlex ou BitBucket requer que você tenha publicado seu projeto local em um desses serviços. Para obter mais informações sobre a publicação de seu projeto nesses serviços, consulte [Criar um Repo (GitHub)], [Usando Git com CodePlex], [Criar um Repo (BitBucket)], [Usando o Dropbox para compartilhar repositórios do Git], ou [Início rápido - Mercurial].

1. Primeiro coloque seus arquivos do site no repositório selecionado que será usado para implantação contínua.

2. No portal Azure de seu site, vá para a guia **PAINEL**. Na seção **Visão Rápida**, selecione **Configurar a implantação a partir do controle de origem**.  A caixa de diálogo **Configurar implantação** é exibida com a seguinte mensagem: **Onde está o código-fonte?**. 

2. Escolha o método de controle do código-fonte que você deseja usar para implantação contínua.
	
3. Quando solicitado, insira suas credenciais para o serviço selecionado.

4. Após autorizar o Azure a acessar sua conta, será apresentada uma lista de repositórios. 

	![git-ChooseARepositoryToDeploy][git-ChooseARepositoryToDeploy]
  
5. Selecione o repositório com o qual você deseja associar seu site do Azure. Clique na marca de seleção para continuar.

	> [WACOM.NOTE] Ao habilitar a implantação contínua com GitHub ou BitBucket, os projetos públicos e privados serão exibidos.

6. O Azure cria uma associação com o repositório selecionado e extrai os arquivos a partir da ramificação mestre. Depois que esse processo for concluído, o **histórico de implantação** da página **Implantações** exibirá uma mensagem da **Implantação Ativa** da seguinte maneira:

	![git-githubdeployed][git-githubdeployed]

7. Nesse ponto, seu projeto foi implantado do seu repositório de escolha no site do Azure. Para verificar se o site está ativo, clique no link **Navegar** na parte inferior do portal. O navegador deve acessar o site.

8. Para verificar se a implantação contínua está ocorrendo, faça uma alteração ao seu projeto e envie a atualização para o repositório que você associou com este site. Seu seu site deve ser atualizado para refletir as alterações logo após a conclusão do envio para o repositório. Você pode verificar se ele recebeu a atualização na página **Implantações** do seu Site.

	![git-GitHubDeployed-Updated][git-GitHubDeployed-Updated]


<h4>Como funciona a implantação contínua</h4>
A implantação contínua funciona fornecendo a **URL DO GATILHO DA IMPLANTAÇÃO** encontrada na seção **implantações** da guia **Configurar** do seu site.

![git-DeploymentTrigger][git-DeploymentTrigger]

Quando as atualizações são feitas ao seu repositório, é enviada uma solicitação POST para esta URL, que notifica o Site do Azure de que o repositório foi atualizado. Nesse ponto, ele recuperará a atualização e a implantará ao seu site.

<h4>Especificando a ramificação a ser usada</h4>

Quando você habilitar a implantação contínua, o padrão será a ramificação **mestre** do repositório. Se quiser usar uma ramificação diferente, execute as seguintes etapas:

1. No portal, selecione seu site e, em seguida, selecione **CONFIGURAR**.

2. Na seção **implantações** da página, insira a ramificação que você deseja usar no campo **RAMIFICAÇÃO PARA IMPLANTAR** e pressione enter. Por fim, clique em **SALVAR**.

	O Azure deve começar imediatamente a atualização com base nas alterações para a nova ramificação.

<h4>Desabilitar a implantação contínua</h4>

A implantação contínua pode ser desabilitada do **Painel** do Azure. Na seção **visão rápida**, escolha a opção de desconectar do repositório que você está usando:

![git-DisconnectFromGitHub][git-DisconnectFromGitHub]	

Depois de responder a **Sim** na mensagem de confirmação, você poderá retornar à **visão rápida** e clicar em **Configurar a implantação a partir do controle de origem** se você deseja configurar a publicação de outra fonte.

<h2><a id="Step8"></a>Solucionar problemas</h2>

Estes são erros ou problemas comumente encontrados ao usar o Git para publicar em um site do Azure:

****

**Sintoma**: não foi possível resolver o nome de host 'host'

**Causa**: este erro pode ocorrer se as informações de endereço inseridas ao criar o azure remoto estiverem incorretas.

**Resolução**: usar o comando `git remote -v` para listar todos os comandos remotos, juntamente com a URL associada. Verifique se a URL do remote do 'azure' está correta. Se necessário, remova e recrie esse remote usando a URL correta.

****

**Sintoma**: não há referências em comum e nenhum especificado; fazendo nada. Talvez você deva especificar uma ramificação como 'mestre'.

**Causa**: este erro pode ocorrer se você não especificar uma ramificação ao executar uma operação de envio de git e não tiver definido o valor de push.default usado pelo Git.

**Solução**: execute a operação de envio novamente, especificando a ramificação mestre. Por exemplo:

	git push azure master

****

**Sintoma**: src refspec [branchname] não corresponde a nada.

**Causa**: este erro pode ocorrer se você tentar enviar para uma ramificação que não seja a mestre no remote 'azure'.

**Solução**: execute a operação de envio novamente, especificando a ramificação mestre. Por exemplo:

	git push azure master

****

**Sintoma**: erro - alterações confirmadas para o repositório remoto, mas site não atualizado.

**Causa**: este erro pode ocorrer se você estiver implantando um aplicativo do Node. js que contém um arquivo package.json que especifica módulos adicionais necessários.

**Resolução**: mensagens adicionais contendo 'npm ERR!' devem ser registradas antes deste erro e podem fornecer contexto adicional sobre a falha. A seguir estão as causas conhecidas desse erro e a mensagem 'npm ERR!' correspondente:

* **Arquivo malformado package.json**: npm ERR! Não foi possível ler as dependências.

* **Um módulo nativo que não tenha uma distribuição binária para o Windows**:

	* npm ERR! \`cmd "/c" "node-gyp rebuild"\` failed with 1

		OU

	* npm ERR! [modulename@version] preinstall: \`make || gmake\`


## Recursos adicionais

* [Como usar o PowerShell para o Azure]
* [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]
* [Documentação de Git]

[Centro de Desenvolvedores do Azure]: http://www.windowsazure.com/pt-br/develop/overview/
[Portal do Azure]: http://manage.windowsazure.com
[Site do Git]: http://git-scm.com
[Instalando o Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Como usar o PowerShell para o Azure]: http://www.windowsazure.com/pt-br/develop/nodejs/how-to-guides/powershell-cmdlets/
[Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]: /pt-br/develop/nodejs/how-to-guides/command-line-tools/
[Documentação de Git]: http://git-scm.com/documentation

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
[Início Rápido - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[Usando o Dropbox para compartilhar repositórios do Git]: https://gist.github.com/trey/2722927
[Envio contínuo ao Azure por meio do Visual Studio Online]: http://www.windowsazure.com/pt-br/develop/net/common-tasks/publishing-with-tfs/

