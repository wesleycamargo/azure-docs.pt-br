<properties linkid="dev-ruby-web-app-with-linux-vm" urlDisplayName="Aplicativo Web Ruby on Rails no Azure usando VM do Linux" pageTitle="Aplicativo Web Ruby on Rails no Azure usando VM do Linux" metaKeywords="aplicativo Web Ruby no Azure, aplicativo Ruby no Azure, app Ruby no Azure, Ruby azure vm, máquina virtual do ruby, ruby linux vm" description="Hospede um site baseado no Ruby on Rails no Azure usando uma máquina virtual do Linux. " metaCanonical="" services="virtual-machines" documentationCenter="Ruby" title="Aplicativo Web Ruby on Rails Web em uma VM do Azure" authors=""  solutions="" writer="larryfr" manager="" editor=""  />





#Aplicativo Web Ruby on Rails Web em uma VM do Azure

Este tutorial descreve como hospedar um site baseado no Ruby on Rails no Azure usando uma máquina virtual do Linux. Este tutorial pressupõe que você não tem experiência anterior com o Azure. Depois de concluir este tutorial, você terá um aplicativo baseado no Ruby on Rails em funcionamento na nuvem.

Você saberá como:

* Configurar seu ambiente de desenvolvimento

* Configure uma máquina virtual do Azure para hospedar o Ruby on Rails.

* Criar um novo aplicativo Rails

* Copiar arquivos para a máquina virtual usando o scp 

Esta é uma captura de tela do aplicativo concluído:

![um navegador que exibe postagens de listagem][blog-rails-cloud]

##Neste artigo

* [Configurar seu ambiente de desenvolvimento](#setup)

* [Criar um aplicativo Rails](#create)

* [Testar o aplicativo](#test)

* [Criar uma máquina virtual do Azure](#createvm)

* [Copiar o aplicativo para a VM](#copy)

* [Instalar gemas e iniciar o aplicativo](#start)

* [Próximas etapas](#next)

##<a id="setup"></a>Configurar seu ambiente de desenvolvimento

1. Instale o Ruby em seu ambiente de desenvolvimento. Dependendo do sistema operacional, as etapas podem ser diferentes.

	* **Apple OS X** - há várias distribuições do Ruby para o OS X. Este tutorial foi validado no OS X com o [Homebrew](http://brew.sh/) para instalar **rbenv** e **ruby-build**. As informações de instalação podem ser encontradas em [https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/).

	* **Linux** - use o sistema de gerenciamento de pacotes de distribuições. Este tutorial foi validado no Ubuntu 12.10 usando os pacotes ruby1.9.1 e ruby1.9.1-dev.

	* **Windows** - existem várias distribuições do Ruby para o Windows. Este tutorial foi validado usando o [RailsInstaller](http://railsinstaller.org/) 1.9.3-p392.

2. Abra uma nova linha de comando ou uma sessão de terminal e insira o seguinte comando para instalar o Ruby on Rails:

		gem install rails --no-rdoc --no-ri

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Esse comando pode exigir privilégios de administrador ou de raiz em alguns sistemas operacionais. Se for exibido um erro durante a execução do comando, use 'sudo' da seguinte maneira:</p>
	<pre class="prettyprint">sudo gem install rails</pre>
	</div>

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>A versão 3.2.12 de gema do Rails foi usada neste tutorial.</p>

	</div>

3. Você também deve instalar um intérprete do JavaScript, que será usado pelo Rails para compilar os ativos CoffeeScript usados pelo aplicativo Rails. Uma lista de intérpretes com suporte está disponível em [https://github.com/sstephenson/execjs#readme](https://github.com/sstephenson/execjs#readme).
	
	[Node.js](http://nodejs.org/) foi usado durante a validação deste tutorial, pois ele está disponível para os sistemas operacionais OS X, Linux e Windows.

##<a id="create"></a>Criar um aplicativo Rails

1. Na linha de comando ou na sessão de terminal, crie um novo aplicativo Rails chamado "blog_app" com o seguinte comando:

		rails new blog_app

	Esse comando cria um novo diretório chamado **blog_app**, ele o preenche com os arquivos e os subdiretórios exigidos por um aplicativo Rails.

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Esse comando pode demorar um minuto ou mais para ser concluído. Ele executa uma instalação silenciosa das gemas necessárias para um aplicativo padrão e, durante esse período, ele pode parecer parado.</p>
	</div>

2. Mude os diretórios para o diretório **blog_app** e use o comando a seguir para criar um scaffolding básico do blog:

		rails generate scaffold Post name:string title:string content:text

	Isso criará as migrações de controlador, de exibição, de modelo e de banco de dados usadas para hospedar postagens no blog. Cada postagem terá o nome do autor, o título da postagem e o conteúdo de texto.

3. Para criar o banco de dados que armazenará as postagens do blog, use o seguinte comando:

		rake db:migrate

	Ele usa o provedor do banco de dados padrão do Rails, que é [banco de dados SQLite3][sqlite3]. Embora seja possível usar um banco de dados diferente para um aplicativo de produção, o SQLite é suficiente para a finalidade deste tutorial.

##<a id="test"></a>Testar o aplicativo

Execute as seguintes etapas para iniciar o servidor do Rails em seu ambiente de desenvolvimento

1. Na linha de comando ou na sessão terminal, inicie o servidor do rails usando o seguinte comando:

		rails s

	Você deve ver saídas semelhantes às seguintes. Observe a porta na qual o servidor web está escutando. No exemplo abaixo, ele está escutando na porta 3000.

		=> Booting WEBrick
		=> Rails 3.2.12 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. Abra o navegador e acesse http://localhost:3000/. Você verá uma página semelhante à seguinte:

	![página padrão de rails][default-rails]

	Esta é uma página de boas-vindas estática. Para consultar os formulários gerados pelo comando scaffolding, navegue até http://localhost:3000/posts. Você verá uma página semelhante à seguinte:

	![uma página que lista postagens][blog-rails]

	Para interromper o processo do servidor, digite CTRL+C na linha de comando

##<a id="createvm"></a>Criar uma Máquina Virtual do Azure

Siga as instruções fornecidas [aqui][vm-instructions] para criar uma Máquina Virtual do Azure que hospede o Linux.

<div class="dev-callout">
<strong>Observação</strong>
<p>As etapas neste tutorial foram executadas em Máquinas Virtuais do Azure que hospeda o Ubuntu 12.10. Se você estiver usando uma distribuição diferente do Linux, as etapas diferentes podem ser necessárias para realizar as mesmas tarefas.</p>
</div>

<div class="dev-callout">
<strong>Observação</strong>
<p>Você precisa <strong>somente</strong> criar a máquina virtual. Pare depois de aprender como se conectar à máquina virtual usando o SSH.</p>
</div> 

Depois de criar Máquinas Virtuais do Azure, execute as seguintes etapas para instalar o Ruby and Rails na máquina virtual:

1. Na linha de comando ou na sessão terminal, use o seguinte comando para se conectar com a máquina virtual usando SSH:

		ssh username@vmdns -p port

	Substitua o nome de usuário especificado durante a criação da VM, o endereço de DNS da VM e a porta do ponto de extremidade de SSH. Por exemplo:

		ssh railsdev@railsvm.cloudapp.net -p 61830

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Se você estiver usando o Windows como o ambiente de desenvolvimento, será possível usar um utilitário como <b>PuTTY</b> para a funcionalidade do SSH. O PuTTY pode ser obtido na página de download do <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html">PuTTY</a>.</p>
	</div>

2. Na sessão de SSH, use os seguintes comandos para instalar o Ruby na VM:

		sudo apt-get update -y
		sudo apt-get upgrade -y
		sudo apt-get install ruby1.9.1 ruby1.9.l-dev build-essential libsqlite3-dev nodejs -y

	Depois que a instalação for concluída, use o seguinte comando para verificar se o Ruby foi instalado com êxito:

		ruby -v

	Isso deve retornar à versão do Ruby que está instalada na máquina virtual, que pode ser diferente da 1.9.1. Por exemplo, **ruby 1.9.3p194 (2012-04-20 revision 35410) [x86_64-linux]**.

2. Use o seguinte comando para instalar o Bundler:

		sudo gem install bundler --no-rdoc --no-ri

	O Bundler será usado para instalar as gemas exigidas pelo aplicativo Rails quando ele for copiado para o servidor.

##<a id="copy"></a>Copiar o aplicativo para a VM

No ambiente do desenvolvimento, abra uma nova linha de comando ou uma sessão terminal e use o comando **scp** para copiar o diretório **blog_app** na máquina virtual. O formato desse comando é o seguinte:

	scp -r -P 54822 -C directory-to-copy user@vmdns:

Por exemplo:

	scp -r -P 54822 -C ~/blog_app railsdev@railsvm.cloudapp.net:

<div class="dev-callout">
<strong>Observação</strong>
<p>Se você estiver usando o Windows como o ambiente de desenvolvimento, será possível usar um utilitário como <b>pscp</b> para a funcionalidade scp. O Pscp pode ser obtido na página de download do <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html">PuTTY</a>.</p>
</div>

Os parâmetros usados nesse comando têm o seguinte efeito:

* **-r**: Copia recursivamente o conteúdo do diretório especificado e de todos os subdiretórios

* **-P**: Usa a porta especificada para a comunicação SSH

* **-C**: Habilita a compactação

* **directory-to-copy**: O diretório local a ser copiado

* **user@vmdns**: O endereço do computador para o qual copiar os arquivos, bem como a conta de usuário com a qual fazer logon

Após a operação de cópia, o diretório **blog_app** estará localizado no diretório de início dos usuários. Use os seguintes comandos na sessão de SSH com a máquina virtual para exibir os arquivos que foram copiados:

	cd ~/blog_app
	ls

A lista de arquivos retornados deve corresponder aos arquivos contidos no diretório **blog_app** no ambiente de desenvolvimento.

##<a id="start"></a>Instalar gemas e iniciar o Rails

1. Na máquina virtual, mude para o diretório **blog_app** e use o seguinte comando para instalar as gemas específicas no **Gemfile**:

		sudo bundle install

2. Para criar o banco de dados, use o seguinte comando:

		rake db:migrate

3. Use o seguinte comando para iniciar o servidor:
	
		rails s

	Você deve ver saídas semelhantes às seguintes. Observe a porta na qual o servidor web está escutando. No exemplo abaixo, ele está escutando na porta 3000.

		=> Booting WEBrick
		=> Rails 3.2.12 application starting in development on http://0.0.0.0:3000
		=> Call with -d to detach
		=> Ctrl-C to shutdown server
		[2013-03-12 19:11:31] INFO  WEBrick 1.3.1
		[2013-03-12 19:11:31] INFO  ruby 1.9.3 (2012-04-20) [x86_64-linux]
		[2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2. No navegador, acesse o [Portal de Gerenciamento do Azure][management-portal] e selecione sua Máquina Virtual.

	![lista da máquina virtual][vmlist]

3. Selecione **PONTOS DE EXTREMIDADE** na parte superior da página e clique em **+ ADICIONAR PONTO DE EXTREMIDADE** na parte inferior da página.

	![página de pontos de extremidade][endpoints]

4. Na caixa de diálogo **ADICIONAR PONTO DE EXTREMIDADE**, clique na seta na parte inferior esquerda para continuar na segunda página e digite as seguintes informações no formulário:

	* **NOME**: HTTP

	* **PROTOCOLO**: TCP

	* **PORTA PÚBLICA**: 80

	* **PORTA PRIVADA**: &lt;informações da porta na etapa 3 acima&gt;

	Isso criará uma porta pública de 80 que roteará o tráfego para a porta privada de 300, na qual o servidor Rails está escutando.

	![caixa de diálogo do novo ponde de extremidade][new-endpoint]

5. Clique na marca de seleção para salvar o ponto de extremidade.

6. Deve aparecer uma mensagem que indica **ATUALIZAÇÃO EM ANDAMENTO**. Depois que a mensagem desaparecer, o ponto de extremidade estará ativo. Você já pode testar seu aplicativo navegando até o nome DNS de sua máquina virtual. O site deve aparecer, semelhante ao seguinte:

	![página padrão de rails][default-rails-cloud]

	Acrescentar **/posts** à URL deve exibir as páginas geradas pelo comando de scaffolding.

	![página das postagens][blog-rails-cloud]

##<a id="next"></a>Próximas etapas

Neste artigo, você aprendeu a criar e publicar um aplicativo Rails básico baseado em formulários em Máquinas Virtuais do Azure. A maioria das ações que executamos foram manuais e, em um ambiente de produção, o desejável seria automatizar. Além disso, a maioria dos ambientes de produção hospeda o aplicativo Rails em conjunto com outro processo do servidor como Apache ou NginX, que trata o roteamento da solicitação para várias instâncias do aplicativo Rails e atende recursos estáticos.

Para obter informações sobre automatizar a implantação do aplicativo Rails, bem como usar o servidor Web Unicorn e o NginX, consulte [Unicorn+NginX+Capistrano com Máquina Virtuais do Azure(a página pode estar em inglês)][unicorn-nginx-capistrano].

Para saber mais sobre o Ruby on Rails, visite os [Guias do Ruby on Rails (a página pode estar em inglês)][rails-guides].

Para saber como usar o Azure SDK do Ruby para acessar os serviços do Azure de seu aplicativo Ruby, consulte:

* [Armazenar dados desestruturados usando blobs][blobs]

* [Armazenar pares de chave/valor usando tabelas][tables]

* [Atender o conteúdo alto da largura de banda com a rede de fornecimento de conteúdo][cdn-howto]



<!-- WA.com links -->
[blobs]: http://windowsazure.com/pt-br/documentation/articles/storage-ruby-how-to-use-blob-storage

[cdn-howto]: http://www.windowsazure.com/pt-br/develop/ruby/app-services/

[management-portal]: https://manage.windowsazure.com/

[tables]: http://www.windowsazure.com/pt-br/develop/ruby/how-to-guides/table-service/

[unicorn-nginx-capistrano]: http://windowsazure.com/pt-br/documentation/articles/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/

[vm-instructions]: http://windowsazure.com/pt-br/documentation/articles/virtual-machines-linux-tutorial


<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/

[sqlite3]: http://www.sqlite.org/

<!-- Images -->
[blog-rails]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilslocal.png

[blog-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/blograilscloud.png 

[default-rails]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailslocal.png

[default-rails-cloud]: ./media/virtual-machines-ruby-rails-web-app-linux/basicrailscloud.png

[vmlist]: ./media/virtual-machines-ruby-rails-web-app-linux/vmlist.png

[endpoints]: ./media/virtual-machines-ruby-rails-web-app-linux/endpoints.png

[new-endpoint]: ./media/virtual-machines-ruby-rails-web-app-linux/newendpoint.png


