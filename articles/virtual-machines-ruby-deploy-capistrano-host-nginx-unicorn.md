<properties linkid="dev-ruby-web-app-with-linux-vm-capistrano" urlDisplayName="Ruby on Rails Azure VM Capistrano" pageTitle="Deploying a Ruby on Rails Web application to an Azure Virtual Machine using Capistrano - tutorial" metaKeywords="ruby on rails, ruby on rails azure, rails azure, rails vm, capistrano azure vm, capistrano azure rails, unicorn azure vm, unicorn azure rails, unicorn nginx capistrano, unicorn nginx capistrano azure, nginx azure" description="Learn how to deploy a Ruby on Rails application to an Azure Virtual Machine using Capistrano, Unicorn and Nginx." metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Deploy a Ruby on Rails Web application to an Azure VM using Capistrano" authors="larryfr" />

<tags ms.service="virtual-machines" ms.workload="web" ms.tgt_pltfrm="vm-linux" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

# implantar um aplicativo Web Ruby on Rails em uma VM do Azure usando o Capistrano

Este tutorial descreve como implantar um site baseado no Ruby on Rails em uma máquina virtual do Azure usando o [Capistrano 3][]. Uma vez implantado, serão usados o [Nginx][] e o [Unicorn][] para hospedar o site. O [PostgreSQL][] armazenará os dados de aplicativo para o aplicativo implantado.

Este tutorial pressupõe que você não tem nenhuma experiência anterior com o Azure, mas pressupõe que está familiarizado com o Ruby, Rails, Git e Linux. Depois de concluir este tutorial, você terá um aplicativo baseado no Ruby on Rails em funcionamento na nuvem.

Você saberá como:

-   Configurar seus ambientes de desenvolvimento e de produção

-   Instalar o Ruby e o Ruby on Rails

-   Instalar o Unicorn, Nginx, e PostgreSQL

-   Criar um novo aplicativo Rails

-   Criar uma implantação Capistrano e usá-la para implantar o aplicativo

Esta é uma captura de tela do aplicativo concluído:

![um navegador que exibe postagens de listagem][]

> [WACOM.NOTE] O aplicativo usado neste tutorial inclui componentes binários nativos. É possível encontrar erros ao implantar à VM se o seu ambiente de desenvolvimento não for baseado em Linux. O arquivo Gemfile.lock usado durante a implantação conterá as gemas de plataforma específica, que pode não incluir as entradas para as versões Linux nativas das gemas necessárias na VM.
>
> As etapas específicas são chamadas para usar o ambiente de desenvolvimento do Windows. No entanto, se forem encontrados erros durante ou após a implantação que não sejam mencionados neste artigo, recomenda-se repetir as etapas neste artigo em um ambiente de desenvolvimento baseado em Linux.

## Neste artigo

-   [Configurar seu ambiente de desenvolvimento][]

-   [Criar um aplicativo Rails][]

-   [Testar o aplicativo][]

-   [Criar um repositório de origem][]

-   [Criar uma máquina virtual do Azure][]

-   [Testar Nginx][]

-   [Preparar para a implantação][]

-   [Implantar][]

-   [Próximas etapas][]

## <span id="setup"></span></a>Configurar seu ambiente de desenvolvimento

1.  Instale o Ruby em seu ambiente de desenvolvimento. Dependendo do sistema operacional, as etapas podem ser diferentes.

    -   **Apple OS X** - há várias distribuições do Ruby para o OS X. Este tutorial foi validado no OS X usando o [Homebrew][] para instalar **rbenv**, **ruby-build** e **Ruby 2.0.0-p451**. As informações da instalação podem ser encontradas em [][]<https://github.com/sstephenson/rbenv/></a>.

    -   **Linux** - use o sistema de gerenciamento de pacotes de distribuições. Este tutorial foi validado no Ubuntu 12.10 usando **rbenv**, **ruby-build** e **Ruby 2.0.0-p451**.

    -   **Windows** - existem várias distribuições do Ruby para o Windows. Este tutorial foi validado usando o [RubyInstaller][] para instalar o **Ruby 2.0.0-p451**. Os comandos foram emitidos usando a linha de comando **GitBash** disponível com [Git para Windows][].

2.  Abra uma nova linha de comando ou uma sessão de terminal e insira o seguinte comando para instalar o Ruby on Rails:

        gem install rails --no-rdoc --no-ri

    > [WACOM.NOTE] Este comando pode exigir privilégios de administrador ou raiz em alguns sistemas operacionais. Se for exibido um erro durante a execução do comando, use 'sudo' da seguinte maneira.
    >
    > `sudo gem install rails`

    > [WACOM.NOTE] A versão 4.0.4 de gema do Rails foi usada neste tutorial.

3.  Também deve-se instalar um intérprete do JavaScript, que será usado pelo Rails para compilar os ativos CoffeeScript usados pelo aplicativo Rails. Uma lista de intérpretes com suporte está disponível em [][1]<https://github.com/sstephenson/execjs#readme></a>.

    > [WACOM.NOTE] [Node.js](<http://nodejs.org/>) foi usado para este tutorial, pois ele está disponível para os sistemas operacionais OS X, Linux e Windows.

## <span id="create"></span></a>Criar um aplicativo Rails

1.  Na linha de comando ou na sessão de terminal, crie um novo aplicativo Rails chamado "blog\_app" com o seguinte comando:

        rails new blog_app

    Esse comando cria um novo diretório chamado **blog\_app** e ele o preenche com os arquivos e os subdiretórios exigidos por um aplicativo Rails.

    > [WACOM.NOTE] Este comando pode demorar um minuto ou mais para ser concluído. Ele executa uma instalação silenciosa das gemas necessárias para um aplicativo padrão e, durante esse período, ele não ficará responsivo.

2.  Altere para o diretório **blog\_app** e, em seguida, use o comando a seguir para criar um scaffolding básico do blog:

        rails generate scaffold Post name:string title:string content:text

    Isso criará as migrações de controlador, de exibição, de modelo e de banco de dados usadas para hospedar postagens no blog. Cada postagem terá o nome do autor, o título da postagem e o conteúdo de texto.

3.  Para criar o banco de dados que armazenará as postagens do blog, use o seguinte comando:

        rake db:migrate

    Este comando criará o esquema de banco de dados para armazenar as postagens usando o provedor do banco de dados padrão para Rails, que é o [Banco de dados do SQLite3][].

4.  Para exibir um índice de postagens como a home page, modifique o arquivo **config/routes.rb** e adicione o seguinte após a linha `resources :posts`.

        root 'posts#index'

    Este comando exibirá uma lista de postagens após a visita dos usuários ao site.

## <span id="test"></span></a>Testar o aplicativo

1.  Altere para o diretório **blog\_app**, se isso ainda não tiver sido feito, e inicie o servidor Rails usando o comando a seguir.

        rails s

    Você deve ver saídas semelhantes às seguintes. Observe a porta na qual o servidor web está escutando. No exemplo abaixo, ele está escutando na porta 3000.

        => Booting WEBrick
        => Rails 4.0.4 application starting in development on http://0.0.0.0:3000
        => Call with -d to detach
        => Ctrl-C to shutdown server
        [2013-03-12 19:11:31] INFO  WEBrick 1.3.1
        [2013-03-12 19:11:31] INFO  ruby 2.0.0 (2014-02-24) [x86_64-linux]
        [2013-03-12 19:11:31] INFO  WEBrick::HTTPServer#start: pid=9789 port=3000

2.  Abra o navegador e acesse <http://localhost:3000/>. Você deverá ver uma página semelhante a esta:

    ![uma página que lista postagens][]

    Para interromper o processo do servidor, digite CTRL+C na linha de comando

## <span id="repository"></span></a>Criar um repositório de origem

Ao implantar um aplicativo usando o Capistrano, os Arquivos para serão retirados de um reposítório. Para este tutorial, o [Git][] será usado para o controle de versão e o [GitHub][], para o repositório.

1.  Crie um novo repositório no [GitHub][]. Se você não tiver uma conta do GitHub, será possível se inscrever em uma conta gratuita. As etapas a seguir pressupõem que o nome do repositório é **blog\_app**.

    > [WACOM.NOTE] Para dar suporte a implantações automatizadas do aplicativo, deve-se usar as chaves de SSH para autenticar para GitHub. Para obter mais informações, consulte a documentação do GitHub em [Gerando as chaves de SSH][].

2.  No prompt de comando, altere para o diretório **blog\_app** e execute os comandos a seguir para carregar o aplicativo em seu repositório GitHub. Substitua o **YourGitHubName** com o nome da sua conta do GitHub.

        git init
        git add .
        git commit -m "initial commit on azure"
        git remote add origin git@github.com:YourGitHubName/blog-azure.git
        git push -u origin master

Na seção a seguir, será criada a Máquina Virtual a qual este aplicativo será implantado.

## <span id="createvm"></span></a>Criar uma máquina virtual do Azure

Siga as instruções fornecidas [aqui][] para criar uma máquina virtual do Azure que hospede o Linux.

1.  Entre no [Portal de Gerenciamento][] do Azure. Na barra de comandos, selecione **Novo**.

2.  Selecione **Máquina Virtual** e, em seguida, selecione **Da galeria**.

3.  Em **Escolher uma imagem**, selecione **Ubuntu** e, em seguida, selecione a versão **12.04 LTS**. Clique na seta para continuar.

    > [WACOM.NOTE] As etapas neste tutorial foram executadas em uma Máquina virtual do Azure que hospeda o Ubuntu 12.04 LTS. Se você estiver usando uma distribuição diferente do Linux, etapas diferentes podem ser necessárias para realizar as mesmas tarefas.

4.  Em **Nome de Máquina Virtual**, digite o nome que você deseja usar para a máquina virtual. Este nome será usado para criar o nome do domínio desta máquina virtual.

5.  Em **Novo nome de usuário**, digite o nome da conta do administrador para este computador.

    > [WACOM.NOTE] Para este tutorial, a conta do administrador também será usada para implantar o aplicativo. Para obter as informações da criação de uma conta separada para a implantação, consulte a documentação do [Capistrano][].

6.  Em **Autenticação**, verifique **Carregar a chave SSH compatível para a autenticação** e, em seguida, procure e selecione o arquivo **.pem** contendo o certificado. Finalmente, clique na seta para continuar.

    > [WACOM.NOTE] Se você não estiver familiarizado com a geração ou o uso de uma chave SSH, consulte [Como usar o SSH com o Linux no Azure][] para obter as informações em como criar as chaves SSH.
    >
    > Também é possível habilitar a autenticação da senha, no entanto, a Chave SSH também deve ser fornecida, já que é usada para automatizar a implantação.

7.  Em **Terminais**, use a lista suspensa **Inserir ou selecionar um valor** para selecionar **HTTP**. Os outros campos nesta página podem ser deixados nos valores padrão. Faça uma observação do **Nome DNS do serviço de nuvem**, já que este valor será usado pelas etapas posteriores. Finalmente, clique na seta para continuar.

8.  Na página final, selecione a marca de seleção para criar a máquina virtual.

### Instalar o Git, Ruby e Nginx

Depois da criação da máquina virtual, conecte-se a ela usando o SSH e use os comandos a seguir para preparar o ambiente de hospedagem para seu aplicativo Ruby.

    sudo apt-get update
    sudo apt-get -y upgrade
    sudo apt-get -y install git build-essential libssl-dev curl nodejs nginx
    git clone git://github.com/sstephenson/rbenv.git ~/.rbenv
    echo 'export RBENV_ROOT=~/.rbenv' >> ~/.bash_profile
    echo 'export PATH="$RBENV_ROOT/bin:$PATH"' >> ~/.bash_profile
    echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
    source ~/.bash_profile
    eval "$(rbenv init -)"
    git clone git://github.com/sstephenson/ruby-build.git
    cd ruby-build
    sudo ./install.sh
    ~/.rbenv/bin/rbenv install 2.0.0-p451
    ~/.rbenv/bin/rbenv global 2.0.0-p451
    gem install bundler
    ~/.rbenv/bin/rbenv rehash

> [WACOM.NOTE] Pode ser que você deseje salvar os comandos acima em um script (arquivo .sh), para evitar erros de digitação ao executá-los.

> [WACOM.NOTE] O comando **~/.rbenv/bin/rbenv install 2.0.0-p451** pode demorar vários minutos para concluir.

O script **rbenv-install.sh** executa as ações a seguir:

-   Atualiza os pacotes atualmente instalados
-   Instala as ferramentas de compilação
-   Instala o Git
-   Instala o Node.js
-   Instala o Nginx
-   Instala as ferramentas de compilação e outros utilitários necessários pelo Ruby e pelo Rails
-   Configura uma implantação local (usuário) de [rbenv]
-   Instala o Ruby 2.0.0-p451
-   Instala a gema do [Bundler]

Depois que a instalação for concluída, use o seguinte comando para verificar se o Ruby foi instalado com êxito:

    ruby -v

Essa ação deverá retornar `ruby 2.0.0p451` como a versão.

### Instalar o PostgreSQL

O banco de dados padrão usado pelo Rails para desenvolvimento é o SQLite. Geralmente, será usado algo diferente na produção. As etapas a seguir instalam o PostgreSQL na máquina virtual e, em seguida, cria um usuário e um banco de dados. As etapas posteriores irão configurar o aplicativo do Rails para usar o PostgreSQL durante a implantação.

1.  Instale o PostgreSQL e desenvolva os bits usando o comando a seguir.

        sudo apt-get -y install postgresql postgresql-contrib libpq-dev

2.  Use os comandos a seguir para criar um usuário e um banco de dados para o seu logon. Substitua o **meu\_nome\_de\_usuário** e o **meu\_banco\_de\_dados** com o nome do seu logon. Por exemplo, se o logon para a sua VM for **implantar**, substitua o **meu\_nome\_de\_usuário** e o **meu\_banco\_de\_dados** por **implantar**.

        sudo -u postgres createuser -D -A -P my_username
        sudo -u postgres createdb -O my_username my_database

    > [WACOM.NOTE] Use o nome do usuário para o nome do banco de dados também. Isso é necessário para a gema capistrano-postgresql usada por este aplicativo.

    Quando for solicitado, digite uma senha para o usuário. Quando for solicitado para permitir que o usuário crie novas funções, selecione **y**, para que este usuário seja usado durante a implantação para criar o banco de dados e o logon que serão usados pelo aplicativo do Rails.

3.  Para verificar se é possível conectar-se ao banco de dados usando a conta do usuário, use o comando a seguir. Substitua o **meu\_nome\_de\_usuário** e o **meu\_banco\_de\_dados** com os valores usados anteriormente.

        psql -U my_username -W my_database

    Deve-se chegar em um prompt `database=>`. Para sair do utilitário psql, digite `\q` no prompt.

### <span id="nginx"></span></a>Testar Nginx

O ponto de extremidade HTTP adicionado durante a criação da máquina virtual permitirá aceitar as solicitações do HTTP da porta 80. Para verificar isso, use as etapas a seguir para ver se é possível acessar o site padrão criado pelo Nginx.

1.  Na sessão de SSH com a VM, inicie o serviço de Nginx:

        sudo service nginx start

    Isso iniciará o serviço de Nginx, que escutará o tráfego de entrada na porta 80.

2.  Teste o seu aplicativo navegando pelo nome DNS do seu computador virtual. O site deve aparecer ao seguinte:

    ![página de boas-vindas do nginx][]

    > [WACOM.NOTE] Os scripts de implantação usados posteriormente neste tutorial tornarão o blog\_app o site padrão servido pelo Nginx.

Neste ponto, você terá a Máquina Virtual do Azure com Ruby, Nginx, e PostgreSQL prontos para a implantação. Na próxima seção, o seu aplicativo de Rails será modificado para adicionar os scripts e as informações para executar a implantação.

## <span id="capify"></span></a>Preparar para a implantação

Em seu ambiente de desenvolvimento, modifique o aplicativo para usar o servidor Web Unicorn, o PostgreSQL, e permita a implantação do Capistrano e crie os scripts usados para a implantação e o início do aplicativo.

1.  Em seu computador de desenvolvimento, modifique o **Gemfile** e inclua as linhas a seguir para adicionar gemas ao Unicorn, PostgreSQL, Capistrano e as gemas relacionadas ao aplicativo de Rails.

        # Use Unicorn
        gem 'unicorn'
        # Use PostgreSQL
        gem 'pg', group: :production

        group :development do
          # Use Capistrano for deployment
          gem 'capistrano', '~> 3.1'
          gem 'capistrano-rails', '~> 1.1.1'
          gem 'capistrano-bundler'
          gem 'capistrano-rbenv', '~> 2.0'
          gem 'capistrano-unicorn-nginx', '~> 2.0'
          gem 'capistrano-postgresql', '~> 3.0'
        end

    > [WACOM.NOTE] O Unicorn não está disponível no Windows. Se você estiver usando o Windows AS como seu ambiente de desenvolvimento, modifique o **Gemfile** para garantir que ele somente tente instalar o Unicorn quando estiver implantado na VM usando o seguinte ao especificar a gema Unicorn.
    >
    > `platforms :ruby do`
    > `gem 'unicorn'`
    > `end`

    A maioria das gemas capistraon-\* sã ajudantes que trabalham com as coisas específicas usadas no servidor de produção(rbenv,) ou a estrutura (rails).

    A gema capistrano-unicorn-nginx cria os scripts usados com o Unicorn e Nginx automaticamente durante a implantação, para que não seja necessária a sua criação manual. A capistrano-postgresql gera automaticamente um banco de dados, um usuário e uma senha no PostgreSQL para o aplicativo. Mesmo que não tenha que usá-los, ambos tornam o processo de implantação muito mais simples.

2.  Use os comandos a seguir para instalar novas gemas.

        bundle

3.  Use o comando a seguir para criar os arquivos de implantação padrão usados pelo Capistrano.

        cap install

    Depois do comando `cap install` ser concluído, os arquivos e os diretórios a seguir serão sido adicionados ao aplicativo.

        ├── Capfile
        ├── config
        │   ├── deploy
        │   │   ├── production.rb
        │   │   └── staging.rb
        │   └── deploy.rb
        └── lib
            └── capistrano
                    └── tasks

    O arquivo **deploy.rb** fornece a configuração genérica e as ações para todos os tipos de implantações, enquanto o **production.rb** e o **staging.rb** contêm a configuração adicional para a produção e preparação das implantações.

    A pasta **capistrano** contém as tarefas e outros arquivos usados como parte do processo de implantação.

4.  Edite o **Capfile** na raiz do seu aplicativo e remova o comentário das linhas a seguir ao remover o caractere **\#** do início da linha.

        require 'capistrano/rbenv'
        require 'capistrano/bundler'
        require 'capistrano/rails/assets'
        require 'capistrano/rails/migrations'

    Após remover os comentários das linhas anteriores, adicione as linhas a seguir.

        require 'capistrano/unicorn_nginx'
        require 'capistrano/postgresql'

    Estas linhas instruem o Capistrano a usar as gemas que foram adicionadas anteriormente ao Gemfile.

    Após concluir as modificações acima, salve o arquivo.

5.  Edite o arquivo **config/deploy.rb** e substitua o conteúdo do arquivo por este: Substitua o **YourApplicationName** com o nome do seu aplicativo e substitua **<https://github.com/YourGitHubName/YourRepoName.git>** com a URL do repositório GitHub para este projeto.

        lock '3.1.0'
        # application name and the github repository
        set :application, 'YourApplicationName'
        set :repo_url, 'git@github.com:YourGitHubName/YourRepoName.git'

        # describe the rbenv environment we are deploying into
        set :rbenv_type, :user
        set :rbenv_ruby, '2.0.0-p451'
        set :rbenv_prefix, "RBENV_ROOT=#{fetch(:rbenv_path)} RBENV_VERSION=#{fetch(:rbenv_ruby)} #{fetch(:rbenv_path)}/bin/rbenv exec"
        set :rbenv_map_bins, %w{rake gem bundle ruby rails}

        # dirs we want symlinked to the shared folder
        # during deployment
        set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system}

        namespace :deploy do

          desc 'Restart application'
          task :restart do
            on roles(:app), in: :sequence, wait: 5 do
              # Your restart mechanism here, for example:
              # execute :touch, release_path.join('tmp/restart.txt')
              #
              # The capistrano-unicorn-nginx gem handles all this
              # for this example
            end
          end

          after :publishing, :restart

          after :restart, :clear_cache do
            on roles(:web), in: :groups, limit: 3, wait: 10 do
              # Here we can do anything such as:
              # within release_path do
              #   execute :rake, 'cache:clear'
              # end
            end
          end

        end

    Este arquivo fornece as informações e as tarefas genéricas comuns a todos os tipos de implantação.

6.  Edite o arquivo **config/deploy/production.rb** e substitua o conteúdo existente pelo seguinte. Substitua o **YourVm.cloudapp.net** com o nome do domínio da sua VM. Substitua o **YourAzureVMUserName** com a conta de logon criada anteriormente pela VM do Azure.

        # production deployment
        set :stage, :production
        # use the master branch of the repository
        set :branch, "master"
        # the user login on the remote server
        # used to connect and deploy
        set :deploy_user, "YourAzureVMUserName"
        # the 'full name' of the application
        set :full_app_name, "#{fetch(:application)}_#{fetch(:stage)}"
        # the server(s) to deploy to
        server 'YourVM.cloudapp.net', user: 'YourAzureVMUserName', roles: %w{web app db}, primary: true
        # the path to deploy to
        set :deploy_to, "/home/#{fetch(:deploy_user)}/apps/#{fetch(:full_app_name)}"
        # set to production for Rails
        set :rails_env, :production

    Este arquivo fornece as informações específicas para as implantações de produção.

7.  Execute os comandos a seguir para confirmar as alterações nos arquivos modificados nas etapas anteriores e, em seguida, carregue as alterações no GitHub.

        git add .
        git commit -m "adding config files"
        git push

O aplicativo deve, agora, estar pronto para a implantação.

> [WACOM.NOTE] Para um aplicativo mais complexo ou um banco de dados ou um servidor de aplicativos diferente, pode ser necessária configuração adicional ou scripts de implantação.

## <span id="deploy"></span></a>Implantar

1.  Em seu computador de desenvolvimento, use o comando a seguir para implantar os arquivos de configuração usados pelo aplicativo na VM.

        bundle exec cap production setup

    O Capistrano irá conectar-se à VM usando o SSH e, em seguida, criará o diretório (~/apps) no qual o aplicativo será implantado. Se esta for a primeira implantação, a gema capistrano-postgresql também criará uma função e um banco de dados no PostgreSQL no servidor. Também criará um arquivo de configuração database.yml, que o Rails usará para conectar-se ao banco de dados.

    > [WACOM.NOTE] Se aparecer um erro **Erro ao ler o tamanho da resposta do soquete de autenticação** na implantação, pode ser necessário iniciar o agente SSH em seu ambiente de implantação usando o comando `ssh-agent`. Por exemplo, adicionar `eval $(ssh-agent)` ao seu arquivo ~/.bash\_profile.
    >
    > Também pode ser necessário adicionar a chave SSH ao cache do agente usando o comando `ssh-add`.

2.  Executar uma implantação de produção usando o comando a seguir. Este comando implantará o aplicativo na máquina virtual, iniciará o serviço Unicorn e configurará o Nginx para rotear o tráfico no Unicorn.

        bundle exec cap production deploy

    Este comando implantará o aplicativo na VM, instalará as gemas necessárias e, em seguida, iniciará/reiniciará o Unicorn e o Nginx.

    > [WACOM.NOTE] O processo pode pausar por vários minutos durante o processamento.

    > [WACOM.NOTE] Algumas porções da implantação pode retornar 'exit status 1 (falha).' Geralmente, elas podem ser ignoradas se a implantação for concluída com sucesso.

    > [WACOM.NOTE] Em alguns sistemas, é possível encontrar uma situação em que o Agente SSH não pode encaminhar as credenciais à VM remota na autenticação do GitHub. Se isso ocorrer, é possível solucionar o erro modificando o arquivo **config/deploy.rb** e alterar a linha `set :repo_url` para usar HTTPS ao acessar o Github. Ao usar o HTTPS, deve-se especificar o nome do usuário e a senha do GitHub (ou o token de autenticação), como parte da URL. Por exemplo:
    >
    > \`set :repo\_url, '<https://you:yourpassword@github.com/You/yourrepository.git>'
    >
    > Mesmo que isso permite ignorar o erro e concluir este tutorial, esta não é uma solução recomendável para uma implantação de produção, já que ela armazena as suas credenciais de autenticação no texto sem formatação, como parte do aplicativo. Deve-se consultar a documentação para o seu sistema operacional, usando o encaminhamento com o Agente SSH.

Neste ponto, o Ruby no aplicativo Rails deve estar em execução em sua máquina virtual do Azure. Para verificar isso, insira o nome DNS da máquina virtual no navegador. Por exemplo, <http://railsvm.cloudapp.net>. O índice das postagens deve aparecer e deve ser possível criar, editar e excluir as postagens.

## <span id="next"></span></a>Próximas etapas

Neste artigo, você aprendeu a criar e publicar um aplicativo Rails básico baseado em formulários em Máquinas Virtuais do Azure usando o Capistrano. Trabalhar com um aplicativo básico, como o deste artigo, mostra superficialmente o que é possível fazer usando o Capistrano para a implantação. Para obter mais informações usando o Capistrano, consulte:

-   [Capistranorb.com][Capistrano] - O site do Capistrano.
-   [Azure, Ruby on Rails, Capistrano 3 e PostgreSQL][] - Uma abordagem alternativa para implantação no Azure envolvendo scripst de implantação personalizados.
-   [Tutorial do Capistrano 3][] - Um tutorial sobre como trabalhar com o Capistrano 3.

Para obter um exemplo mais básico de como criar e implantar um aplicativo Rails em uma VM do Azure VM usando somente o SSH, consulte [Hospede um aplicativo Web Ruby on Rails usando uma máquina virtual do Linux][].

Para saber mais sobre o Ruby on Rails, visite os [Guias do Ruby on Rails][].

Para saber como usar o Azure SDK do Ruby para acessar os serviços do Azure de seu aplicativo Ruby, consulte:

-   [Armazenar dados desestruturados usando blobs][]

-   [Armazenar pares de chave/valor usando tabelas][]

-   [Atender o conteúdo alto da largura de banda com a rede de fornecimento de conteúdo][]

  [Capistrano 3]: https://github.com/capistrano/capistrano/
  [Nginx]: http://nginx.org/
  [Unicorn]: https://github.com/blog/517-unicorn
  [PostgreSQL]: https://www.postgresql.org
  [um navegador que exibe postagens de listagem]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilscloud.png
  [Configurar seu ambiente de desenvolvimento]: #setup
  [Criar um aplicativo Rails]: #create
  [Testar o aplicativo]: #test
  [Criar um repositório de origem]: #repository
  [Criar uma máquina virtual do Azure]: #createvm
  [Testar Nginx]: #nginx
  [Preparar para a implantação]: #capify
  [Implantar]: #deploy
  [Próximas etapas]: #next
  [Homebrew]: http://brew.sh/
  []: https://github.com/sstephenson/rbenv/
  [RubyInstaller]: http://RubyInstaller.org/
  [Git para Windows]: http://git-scm.com/download/win
  [1]: https://github.com/sstephenson/execjs#readme
  [Banco de dados do SQLite3]: http://www.sqlite.org/
  [uma página que lista postagens]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/blograilslocal.png
  [Git]: http://git-scm.com/
  [GitHub]: https://github.com/
  [Gerando as chaves de SSH]: https://help.github.com/articles/generating-ssh-keys
  [aqui]: /en-us/manage/linux/tutorials/virtual-machine-from-gallery/
  [Portal de Gerenciamento]: https://manage.windowsazure.com/
  [Capistrano]: http://capistranorb.com
  [Como usar o SSH com o Linux no Azure]: http://azure.microsoft.com/en-us/documentation/articles/linux-use-ssh-key/
  [página de boas-vindas do nginx]: ./media/virtual-machines-ruby-deploy-capistrano-host-nginx-unicorn/welcomenginx.png
  [Azure, Ruby on Rails, Capistrano 3 e PostgreSQL]: http://wootstudio.ca/articles/tutorial-windows-azure-ruby-on-rails-capistrano-3-postgresql
  [Tutorial do Capistrano 3]: http://www.talkingquickly.co.uk/2014/01/deploying-rails-apps-to-a-vps-with-capistrano-v3/
  [Hospede um aplicativo Web Ruby on Rails usando uma máquina virtual do Linux]: /en-us/develop/ruby/tutorials/web-app-with-linux-vm/
  [Guias do Ruby on Rails]: http://guides.rubyonrails.org/
  [Armazenar dados desestruturados usando blobs]: /en-us/develop/ruby/how-to-guides/blob-storage/
  [Armazenar pares de chave/valor usando tabelas]: /en-us/develop/ruby/how-to-guides/table-service/
  [Atender o conteúdo alto da largura de banda com a rede de fornecimento de conteúdo]: /en-us/develop/ruby/app-services/
