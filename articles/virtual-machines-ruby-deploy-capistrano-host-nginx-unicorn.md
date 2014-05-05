<properties linkid="dev-ruby-web-app-with-linux-vm-capistrano" urlDisplayName="Ruby em trilhos Azure VM Capistrano" pageTitle="Implantando um aplicativo Ruby on Rails Web para uma máquina da Azure Virtual usando Capistrano - tutorial" metaKeywords="ruby no azure rails, ruby nos trilhos, trilhos do azure, vm rails, capistrano vm azure, capistrano azure rails, unicórnio vm azure, unicórnio azure rails, capistrano de nginx de unicórnio, azure de capistrano de nginx do unicórnio, azure nginx" description="Saiba como implantar um aplicativo Ruby on Rails para uma máquina da Azure Virtual usando CapistranoUnicórnio e Nginx." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Implantar um aplicativo Ruby on Rails Web para uma VM do Azure usando Capistrano" authors="" />



#Implantar um aplicativo Web Ruby on Rails em uma VM do Azure usando o Capistrano

Este tutorial descreve como implantar um site baseado no Ruby on Rails em uma máquina virtual do Azure usando o [Capistrano](https://github.com/capistrano/capistrano/). Este tutorial também descreve como usar o [Nginx](http://nginx.org/) e o [Unicorn](https://github.com/blog/517-unicorn) para hospedar o aplicativo na máquina virtual.

Este tutorial pressupõe que você não tem experiência anterior com o Azure. Depois de concluir este tutorial, você terá um aplicativo baseado no Ruby on Rails em funcionamento na nuvem.

Você saberá como:

* Configurar seu ambiente de desenvolvimento

* Instalar o Ruby e o Ruby on Rails

* Instalar e configurar o Nginx e o Unicorn

* Criar um novo aplicativo Rails

* Implantar um aplicativo Rails em uma máquina virtual do Azure usando o Capistrano

Esta é uma captura de tela do aplicativo concluído:

![um navegador que exibe postagens de listagem][blog-rails-cloud]

<div class="dev-callout">
<strong>Observação</strong>
<p>O aplicativo usado neste tutorial inclui componentes binários nativos. Por esse motivo, você poderá ter problemas se o seu ambiente de desenvolvimento não for baseado em Linux já que o Gemfile.lock gerado no computador do desenvolvimento pode não incluir entradas para a versão compatível com o Linux das gemas exigidas.</p>
<p>Algumas etapas específicas são exigem o uso de um ambiente de desenvolvimento do Windows, já que isso representa o delta mais significativo do ambiente da implantação de destino. No entanto, se você encontrar erros durante ou após a implantação que não sejam tratados pelas etapas neste artigo, recomenda-se repetir as etapas em um ambiente de desenvolvimento baseado em Linux.</p>

</div>

##Neste artigo

* [Configurar seu ambiente de desenvolvimento](#setup)

* [Criar um aplicativo Rails](#create)

* [Testar o aplicativo](#test)

* [Criar um repositório de origem](#repository)

* [Criar uma máquina virtual do Azure](#createvm)

* [Testar o Nginx](#nginx)

* [Preparar para a implantação](#capify)

* [Implantar](#deploy)

* [Próximas etapas](#next)

##<a id="setup"></a>Configurar seu ambiente de desenvolvimento

1. Instale o Ruby em seu ambiente de desenvolvimento. Dependendo do sistema operacional, as etapas podem ser diferentes.

	* **Apple OS X** - Há várias distribuições do Ruby para o OS X. Este tutorial foi validado no OS X com o [Homebrew](http://brew.sh/) para instalar o **rbenv** e o **ruby-build**. As informações de instalação podem ser encontradas em [https://github.com/sstephenson/rbenv/](https://github.com/sstephenson/rbenv/).

	* **Linux** - Use o sistema de gerenciamento de pacotes de distribuições. Este tutorial foi validado no Ubuntu 12.10 usando os pacotes ruby1.9.1 e ruby1.9.1-dev.

	* **Windows** - Existem várias distribuições do Ruby para o Windows. Este tutorial foi validado usando o [RailsInstaller](http://railsinstaller.org/) 1.9.3-p392.

2. Abra uma nova linha de comando ou uma sessão de terminal e insira o seguinte comando para instalar o Ruby on Rails:

		gem install rails --no-rdoc --no-ri

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Esse comando pode exigir privilégios de administrador ou de raiz em alguns sistemas operacionais. Se for exibido um erro durante a execução do comando, use "sudo" da seguinte maneira:</p>
	<pre class="prettyprint">trilhos de instalação de gem sudo</pre>
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

	Esse comando cria um novo diretório chamado **blog_app**, e ele o preenche com os arquivos e os subdiretórios exigidos por um aplicativo Rails.

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Esse comando pode demorar um minuto ou mais para ser concluído. Ele executa uma instalação silenciosa das gemas necessárias para um aplicativo padrão e, durante esse período, ele pode parecer parado.</p>
	</div>

2. Mude para o diretórios de **blog_app** e use o comando a seguir para criar um scaffolding básico do blog:

		rails generate scaffold Post name:string title:string content:text

	Isso criará as migrações de controlador, de exibição, de modelo e de banco de dados usadas para hospedar postagens no blog. Cada postagem terá o nome do autor, o título da postagem e o conteúdo de texto.

3. Para criar o banco de dados que armazenará as postagens do blog, use o seguinte comando:

		rake db:migrate

	Ele usa o provedor do banco de dados padrão do Rails, que é o [Banco de Dados SQLite3][sqlite3]. Embora seja possível usar um banco de dados diferente para um aplicativo de produção, o SQLite é suficiente para a finalidade deste tutorial.

##<a id="test"></a>Testar o aplicativo

Execute as seguintes etapas para iniciar o servidor do Rails em seu ambiente de desenvolvimento

1. Mude o diretório para **blog_app**, se isso ainda não tiver sido feito, e inicie o servidor de rails com o seguinte comando:

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

##<a id="repository"></a>Criar um repositório de origem

Neste tutorial, usaremos [Git](http://git-scm.com/) e [GitHub](https://github.com/) no controle da versão e como um local central para o nosso código.

1. 	Crie um novo repositório no [GitHub](https://github.com/). Se você não tiver uma conta do GitHub, será possível se inscrever em uma conta gratuita. As etapas neste tutorial supõem que o nome do repositório é **blog_app**.

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Os scripts criados nas seções posteriores deste documento conterão o endereço da sua máquina virtual e o nome de usuário usado para implantar o aplicativo no SSH. Por esse motivo, recomendamos que você use um repositório GitHub particular, se possível.</p>
	</div>

2. 	No prompt de comando, mude para o diretório **blog_app** e execute os seguintes comandos para carregar a versão inicial do aplicativo em seu repositório GitHub:

		git init
		git add .
		git commit -m "initial commit on azure"
		git remote add origin https://github.com/YOUR_GITHUB_ACCOUNT/blog-azure.git
		git push -u origin master


##<a id="createvm"></a>Criar uma máquina virtual do Azure

Siga as instruções fornecidas [aqui][vm-instructions] para criar uma máquina virtual do Azure que hospede o Linux.

<div class="dev-callout">
<strong>Observação</strong>
<p>As etapas neste tutorial foram executadas em uma máquina da Azure Virtual hospedando 12 Ubuntu.10. Se você estiver usando uma distribuição Linux diferente, diferentes etapas podem ser necessárias para realizar as mesmas tarefas.</p>
</div>

<div class="dev-callout">
<strong>Observação</strong>
<p>Você precisa <strong>somente</strong> criar a máquina virtual. Pare depois de aprender como se conectar à máquina virtual usando o SSH.</p>
</div>

Depois de criar a máquina virtual do Azure usando o SSH e executar as seguintes etapas para instalar o Ruby e o Rails na máquina virtual:

1. Conecte-se à máquina virtual usando o SSH e use os seguintes comandos para atualizar pacotes existentes e instalar o ambiente do Ruby:

		sudo apt-get -y update
		sudo apt-get -y upgrade
		sudo apt-get -y install ruby1.9.1 ruby1.9.l-dev build-essential libsqlite3-dev nodejs curl git-core nginx

	Depois que a instalação for concluída, use o seguinte comando para verificar se o Ruby foi instalado com êxito:

		ruby -v

	Isso deve retornar à versão do Ruby que está instalada na máquina virtual, que pode ser diferente da 1.9.1. Por exemplo, **1.9.3p194 ruby (2012-04-20 revisão 35410) [x86_linux 64]**

2. Use o seguinte comando para instalar o Bundler:

		sudo gem install bundler

	O bundler será usado para instalar as gemas exigidas pelo aplicativo Rails quando ele for copiado para o servidor.

##<a id="nginx"></a>Abra a porta 80 e teste o Nginx

O Nginx fornece um site padrão que podemos usar para nos certificar que a nossa máquina virtual está aceitando o tráfego de Web. Executar as seguintes etapas habilita o tráfego sobre a porta 80 e testa a site padrão do Nginx.

2. 	Na sessão de SSH com a VM, inicie o serviço de Nginx:

		sudo service nginx start

	Isso iniciará o serviço de Nginx, que escutará o tráfego de entrada na porta 80.

2. No navegador, acesse o [Portal de Gerenciamento do Azure][management-portal] e selecione sua máquina virtual.

	![lista da máquina virtual][vmlist]

3. Selecione **PONTOS DE EXTREMIDADE** na parte superior da página e clique em **+ ADICIONAR PONTO DE EXTREMIDADE** na parte inferior da página.

	![página de pontos de extremidade][endpoints]

4. Na caixa de diálogo **ADICIONAR PONTO DE EXTREMIDADE**, clique na seta na parte inferior esquerda para continuar na segunda página e digite as seguintes informações no formulário:

	* **NAME**: HTTP

	* **PROTOCOL**: TCP

	* **PUBLIC PORT**: 80

	* **PRIVATE PORT**: 80

	Isso criará uma porta pública de 80 que roteará o tráfego para a porta privada de 80 - na qual o Nginx está escutando.

	![caixa de diálogo do novo ponde de extremidade][new-endpoint]

5. Clique na marca de seleção para salvar o ponto de extremidade.

6. Deve aparecer uma mensagem que indica **ATUALIZAÇÃO EM ANDAMENTO**. Depois que a mensagem desaparecer, o ponto de extremidade estará ativo. Você já pode testar seu aplicativo navegando até o nome DNS de sua máquina virtual. O site deve aparecer, semelhante ao seguinte:

	![página de boas-vindas do nginx][nginx-welcome]

2. 	Pare e remova o site padrão do Nginx com os seguintes comandos:

		sudo service nginx stop
		sudo rm /etc/nginx/sites-enabled/default

	Os scripts da implantação executados posteriormente neste tutorial tornarão o blog_app um site padrão atendido pelo Nginx.

##<a id="capify"></a>Preparar para a implantação

Nesta seção, você modifica o aplicativo para usar o servidor Web do Unicorn, de unicórnio, habilita Capistrano para a implantação, habilita o acesso do GitHub da máquina virtual e cria os scripts usados para implantar e iniciar o aplicativo.

1. Autorize sua máquina virtual a autenticar sua conta do GitHub com um certificado executando as etapas descritas na página [Geração das chaves de SSH](https://help.github.com/articles/generating-ssh-keys#platform-all). Isso será usado para acessar seu repositório GitHub dos scripts da implantação.

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Enquanto a chave SSH é gerada na máquina virtual, você pode adicionar a chave em sua conta do GitHub usando o navegador no ambiente de desenvolvimento.</p>
	<p>Para exibir o conteúdo dos certificados SSH de modo que você possa copiar e colar o GitHub, use o seguinte comando:</p>
	<pre>cat ~/.ssh/id_rsa.pub</pre>
	</div>


1. Em seu computador de desenvolvimento, modifique o **Gemfile** e remova o comentário das linhas do **Capistrano** e do **Unicorn** removendo o caractere '#' do início das seguintes linhas:

		# gem 'unicorn'
		# gem 'capistrano'

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>O Unicorn não está disponível no Windows. Se você estiver usando o Windows como seu ambiente de desenvolvimento, modifique o <strong>Gemfile</strong> para garantir que ele somente tente instalar o Unicorn quando estiver implantado na VM:</p>
	<pre class="prettyprint">plataformas: fazer ruby<br />  gem ' unicórnio'<br />final</pre>
	</div>
 
5. 	Execute os seguintes comandos para instalar as novas gemas e configurar o Capistrado para seu projeto:
		
		bundle
		capify .

6. 	Adicione os seguintes arquivos ao diretório **blog_app/config** e preencha cada arquivo com o conteúdo encontrado nos links abaixo:

	* [nginx.conf](https://gist.github.com/cff582f4f970a95991e9) - Configura o Nginx para que funcione com o Unicorn e sirva os arquivos estáticos incluídos no aplicativo Rails
	* [unicorn_init.sh](https://gist.github.com/3272994) - O script de shell usado para iniciar o processo do servidor do Unicorn
	* [unicorn.rb](https://gist.github.com/3273014) - O arquivo de configuração do Unicorn

	Em cada arquivo, substitua a palavra **blogger** pelo nome de usuário usado para fazer logon em sua máquina virtual. Esse é o usuário que será usado para implantar o aplicativo.

7. No diretório **blog_app/config**, edite o arquivo **deploy.rb** e substitua o conteúdo existente pelo seguinte:

		require "bundler/capistrano"

		set :application, "blog_app"
		set :user, "blogger"

		set :scm, :git
		set :repository, "git@github.com:YourGitHubAccount/blog_app.git"
		set :branch, "master"
		set :use_sudo, true

		server "VMDNSName", :web, :app, :db, primary: true

		set :deploy_to, "/home/#{user}/apps/#{application}"
		default_run_options[:pty] = true
		ssh_options[:forward_agent] = true
		ssh_options[:port] = SSHPort

		namespace :deploy do
		  desc "Fix permissions"
		  task :fix_permissions, :roles => [ :app, :db, :web ] do
		  	run "chmod +x #{release_path}/config/unicorn_init.sh"
		  end

		  %w[start stop restart].each do |command|
		    desc "#{command} unicorn server"
		    task command, roles: :app, except: {no_release: true} do
		      run "service unicorn_#{application} #{command}"
		    end
		  end

		  task :setup_config, roles: :app do
		    sudo "ln -nfs #{current_path}/config/nginx.conf /etc/nginx/sites-enabled/#{application}"
		    sudo "ln -nfs #{current_path}/config/unicorn_init.sh /etc/init.d/unicorn_#{application}"
		    sudo "mkdir -p #{shared_path}/config"
		  end
		  after "deploy:setup", "deploy:setup_config"

		  task :symlink_config, roles: :app do
		    # Add database config here
		  end
		  after "deploy:finalize_update", "deploy:fix_permissions"
		  after "deploy:finalize_update", "deploy:symlink_config"
		end

	No texto acima, substitua o seguinte:

	* **YourGitHubAccount** com o nome da conta do GitHub
	* **VMDNSName** com o DNS da máquina virtual Azure
	* **blogger** com o nome de usuário usado para fazer logon na máquina virtual
	* **SSHPort** com a porta externa de SSH para sua máquina virtual

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Se seu ambiente de desenvolvimento é um sistema do Windows, você deve adicionar a seguinte linha para o <b>deploy.rb</b> arquivo. Isso deve ser colocado junto com os outros <b>set</b> instruções no início do arquivo:</p>
	<pre>set :bundle_flags, "--no-deployment --quiet"</pre>
	<p>Essa não é a prática recomendada pois faz com que as gemas sejam carregadas do Gemfile durante a implantação em vez do Gemfile.lock, mas ela é necessária já que o sistema de destino (Linux) é diferente do sistema de desenvolvimento (Windows).</p>
	</div>

7.	Remova o comentário da linha de ativos no **Capfile** localizado no diretório **blog_app**.

		load 'deploy/assets'

8.	Execute os seguintes comandos para confirmar as alterações no projeto e carregá-las no GitHub.

		git add .
		git commit -m "adding config files"
		git push

##<a id="deploy"></a>Implantar

2.	A partir de seu computador local de desenvolvimento, use o seguinte comando para configurar a VM remota do Azure para a implantação.

		cap deploy:setup

	Quando for solicitado, digite a senha da conta de usuário da máquina virtual. O Capistrano se conectará com a VM e criará um diretório de **aplicativos** no diretório de início da conta de usuário.

3.	Em uma conexão SSH com a VM do Azure, modifique as permissões do diretório **aplicativo** usando o seguinte comando:
		
		sudo chmod -R 777 apps

	<div class="dev-callout">
	<strong>Observação</strong>
	<p>Isso não é sugerido em ambientes de produção e está sendo usado somente agora para fins de demonstração.</p>
	</div>

4.	Faça uma implantação fria usando o seguinte comando em seu ambiente de desenvolvimento. Ela implantará o aplicativo na máquina virtual e iniciará o serviço Unicorn.

		cap deploy:cold

3.	Inicie o serviço Nginx, que deve começar a rotear tráfego para o unicórnio e atender ao conteúdo estático:

		sudo service nginx start

Neste ponto, o Ruby no aplicativo Rails deve estar em execução em sua máquina virtual do Azure. Para verificar isso, insira o nome DNS da máquina virtual no navegador. Por exemplo, http://railsvm.cloudapp.net. A tela 'Bem-vindo a bordo' será exibida:

![página de boas-vindas][default-rails-cloud]

Se você acrescentar "/posts" ao URL, o índice das postagens deve aparecer e você conseguirá criar, editar e excluir postagens.

##<a id="next"></a>Próximas etapas

Neste artigo, você aprendeu a criar e publicar um aplicativo Rails básico baseado em formulários em Máquinas Virtuais do Azure usando o Capistrano. A máquina virtual usou o Unicorn e o Nginx para tratar solicitações da Web com o aplicativo.

Para obter um exemplo mais básico de como criar e implantar um aplicativo Rails em uma VM do Azure VM usando somente o SSH, consulte [Hospede um aplicativo Web Ruby on Rails usando uma máquina virtual do Linux][ruby-vm].

Para saber mais sobre o Ruby on Rails, visite os [Guias do Ruby on Rails (a página pode estar em inglês)][rails-guides].

Para saber como usar o Azure SDK do Ruby para acessar os serviços do Azure de seu aplicativo Ruby, consulte:

* [Armazenar dados desestruturados usando blobs][blobs]

* [Armazenar pares de chave/valor usando tabelas][tables]

* [Atender o conteúdo alto da largura de banda com a rede de fornecimento de conteúdo][cdn-howto]

[vm-instructions]: /pt-br/manage/linux/tutorials/virtual-machine-from-gallery/


[rails-guides]: http://guides.rubyonrails.org/
[blobs]: /pt-br/develop/ruby/how-to-guides/blob-storage/
[tables]: /pt-br/develop/ruby/how-to-guides/table-service/
[cdn-howto]: /pt-br/develop/ruby/app-services/
[ruby-vm]: /pt-br/develop/ruby/tutorials/web-app-with-linux-vm/
 
[blog-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png
[blog-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png 
[default-rails]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailslocal.png
[default-rails-cloud]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/basicrailscloud.png
[vmlist]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/vmlist.png
[endpoints]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/endpoints.png
[new-endpoint]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/newendpoint80.png
[nginx-welcome]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png

[management-portal]: https://manage.windowsazure.com/
[sqlite3]: http://www.sqlite.org/

