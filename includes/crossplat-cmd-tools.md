#Como usar as ferramentas de linha de comando do Azure para Mac e Linux

Saiba como usar as ferramentas de linha de comando do Azure para Mac e Linux para criar e gerenciar serviços no Azure. Os cenários abordados incluem **instalar as ferramentas**, **importar as configurações de publicação**, **criação e gerenciamento dos sites do Azure** e **criação e gerenciamento das Máquinas Virtuais do Azure** Para obter documentação de referência abrangente, consulte [Ferramenta de linha de comando do Azure para Mac e a documentação do Linux][reference-docs] 

##Sumário
* [Quais são as ferramentas de linha de comando do Azure para Mac e Linux](#Overview)
* [Como instalar as ferramentas de linha de comando do Azure para Mac e Linux](#Download)
* [Criar uma conta do Azure](#CreateAccount)
* [Como baixar e importar as configurações de publicação](#Account)
* [Como criar e gerenciar um site do Azure](#WebSites)
* [Como criar e gerenciar uma Máquina Virtual do Azure](#VMs)


<h2><a id="Overview"></a>Quais são as ferramentas de linha de comando do Azure para Mac e Linux</h2>

As ferramentas de linha de comando do Azure para Mac e Linux são um conjunto de ferramentas de linha de comando para implantar e gerenciar os serviços do Azure.
 
As tarefas suportadas incluem o seguinte:

* Importar configurações de publicação.
* Criar e gerenciar sites do Azure.
* Criar e gerenciar Máquinas Virtuais do Azure.

Para obter uma lista completa dos comandos suportados, digite `azure - ajuda` na linha de comando após instalar as ferramentas, ou consulte a [documentação de referência][reference-docs]

<h2><a id="Download">Como instalar as ferramentas de linha de comando do Azure para Mac e Linux</a></h2>

A lista a seguir contém informações para instalar as ferramentas de linha de comando, dependendo do seu sistema operacional:

* **Mac**: baixe o [SDK do instalador do Azure][mac-installer]. Abra o arquivo .pkg baixado e conclua as etapas de instalação quando for solicitado.

* **Linux**: instalar a versão mais recente do [Node.js][nodejs-org] (consulte [Instalar o Node.js por meio do Gerenciador de Pacotes][install-node-linux]), em seguida, execute o seguinte comando:

		npm install azure-cli -g

	**Observação**: talvez seja necessário executar esse comando com privilégios elevados:

		sudo npm install azure-cli -g

* **Windows**: executar o instalador do Windows (arquivo .msi), que está disponível aqui: [Ferramentas de linha de comando do Azure][windows-installer].


Para testar a instalação, digite `azure` no prompt de comando. Se a instalação foi executada com êxito, você verá uma lista de todos os comandos `azure` disponíveis.

<h2><a id="CreateAccount"></a>Criar uma conta do Azure</h2>

Para usar as ferramentas de comando do Azure para Mac e Linux, você precisará de uma conta no Azure.

Abra um navegador da Web e navegue para [http://www.windowsazure.com][windowsazuredotcom] e clique em **Avaliação Gratuita** no canto superior direito.

![Site do Azure][Azure Web Site]

Siga as instruções para a criação de uma conta.

<h2><a id="Account"></a>Como baixar e importar as configurações de publicação</h2>

Para começar, você precisa primeiro baixar e importar as configurações de publicação. Isso permitirá que você use as ferramentas para criar e gerenciar os serviços do Azure. Para baixar as configurações de sua publicação, use o comando `download de conta`:

	azure account download

Isso abrirá o navegador padrão e será solicitado que você entre no Portal de Gerenciamento. Depois de entrar, seu arquivo `.publishsettings` será baixado. Anote de onde o arquivo foi salvo.

Em seguida, importe o arquivo `.publishsettings` ao executar o seguinte comando, substituindo `{path to .publishsettings file}` com o caminho para seu arquivo `.publishsettings`:

	azure account import {path to .publishsettings file}

Você pode remover todas as informações armazenadas pelo comando <code>import</code> usando o comando <code>account clear</code>:

	azure account clear

Para ver uma lista de opções para comandos `account`, use a opção `-help`:

	azure account -help

Depois de importar suas configurações de publicação, você deve excluir o arquivo `.publishsettings` por motivos de segurança.

<div class="dev-callout"> 
<b>Nota</b> 
<p>quando você importar as configurações de publicação, as credenciais para acessar sua assinatura do Azure são armazenadas dentro de sua <code>user</code> pasta. A pasta <code>user</code> está protegida pelo seu sistema operacional. No entanto, é recomendável que você execute etapas adicionais para criptografar sua pasta <code>user</code>. Você pode fazer isso das seguintes maneiras:</p>

<ul>
<li>No Windows, modifique as propriedades da pasta ou use o BitLocker.</li>
<li>No Mac, ative o FileVault para a pasta.</li>
<li>No Ubuntu, use o recurso de diretório home criptografado. Outras distribuições Linux oferecem recursos equivalentes.</li>
</ul>

</div>

Agora você está pronto para começar a criação e o gerenciamento dos sites do Azure e da Máquinas Virtuais do Azure.  

<h2><a id="WebSites"></a>Como criar e gerenciar um site do Azure</h2>

###Criar um site

Para criar um site do Azure, primeiro crie um diretório vazio chamado `MySite` e acesse esse diretório.

Em seguida, execute o seguinte comando:

	azure site create MySite --git

A saída desse comando conterá a URL padrão para o site recém-criado. A opção `--git` permite que você use o git para publicar seu site, criando repositórios git tanto no diretório do aplicativo local quanto no centro de dados do site. Observe se a sua pasta local já é um repositório git, o comando adicionará um novo controle remoto ao repositório existente, apontando para o repositório no centro de dados do site.

Observe se você pode executar o comando `azure site create` com qualquer uma das seguintes opções:

* `--location [nome do local]`. Esta opção permite que você especifique o local do centro de dados em que seu site é criado (por exemplo, "Oeste dos EUA"). Se você omitir esta opção, será solicitado que escolha um local.
* `--hostname [nome de host personalizado]`. Esta opção permite que você especifique um nome de host personalizado para seu site.

Em seguida, você pode adicionar conteúdo para o diretório do seu site. Usam o fluxo de git regular (`adicionar git`, `confirmação de git`) para confirmar seu conteúdo. Use o seguinte comando git para transferir o conteúdo do site para o Azure: 

	git push azure master

###Configurar a publicação do GitHub

Para configurar a publicação contínua de um repositório GitHub, use a opção ` – GitHub` durante a criação de um site:

	auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Se você tiver um clone local de um repositório GitHub ou se houver um repositório com uma única referência remota para um repositório GitHub, este comando publicará automaticamente o código no repositório GitHub ao seu site. A partir deste ponto, quaisquer alterações enviadas ao repositório GitHub serão automaticamente publicadas ao seu site.

Quando você configura a publicação do GitHub, a ramificação padrão usada é a ramificação principal. Para especificar uma ramificação diferente, execute o seguinte comando no seu repositório local:

	azure site repository <branch name>

###Definir configurações de aplicativo

As configurações de aplicativo são pares de valor chave que estão disponíveis para seu aplicativo em tempo de execução. Quando definido para um site do Azure, os valores de configuração de aplicativo substituirão as configurações com a mesma chave definida no arquivo Web.config do seu site. Para aplicativos Node.js e PHP, as configurações do aplicativo estão disponíveis como variáveis de ambiente. O exemplo a seguir mostra como definir um par de valor chave:

	azure site config add <key>=<value> 

Para ver uma lista de todos os pares de chave/valor, use o seguinte:

	azure site config list 

Ou se você souber a chave e desejar ver o valor, você pode usar:

	azure site config get <key> 

Se você desejar alterar o valor de uma chave existente deve primeiro limpar a chave existente e, em seguida, adicioná-lo novamente. O comando de limpeza é:

	azure site config clear <key> 

###Listar e mostrar sites

Para listar os sites, use o seguinte comando:

	azure site list

Para obter informações detalhadas sobre um site, use o comando `site show`. O exemplo a seguir mostra detalhes de `MySite`:

	azure site show MySite

###Parar, iniciar ou reiniciar um site

Parar, iniciar ou reiniciar um site com os comandos `site stop`,`site start`, ou `site restart`:

	azure site stop MySite
	azure site start MySite
	azure site restart MySite

###Excluir um site

Por fim, você pode excluir um site com o comando `site delete`:

	azure site delete MySite

Observe que se você estiver executando qualquer um dos comandos acima de dentro da pasta em que você executava `site create`, você não precisa especificar o nome do site `MySite` como o último parâmetro.

Para ver uma lista de opções completa para comandos `site`, use a opção `-help`:

	azure site -help 

<h2><a id="VMs"></a>Como criar e gerenciar uma Máquina Virtual do Azure</h2>

Uma Máquina Virtual do Azure é criada a partir de uma imagem de máquina virtual (um arquivo .vhd) fornecida por você ou que está disponível na Galeria de imagens. Para ver as imagens que estão disponíveis, use o comando `vm image list`:

	azure vm image list

Você pode configurar e iniciar uma máquina virtual a partir de uma das imagens disponíveis com o comando `vm create`. O exemplo a seguir mostra como criar uma máquina virtual do Linux (chamada `myVM`) a partir de uma imagem na Galeria de imagens (CentOS 6.2). O nome de usuário raiz e a senha para a máquina virtual são `myusername`e`Mypassw0rd` respectivamente. (Observe que o parâmetro `--location` especifica o centro de dados em que a máquina virtual é criada. Se você omitir o parâmetro `--location`, será solicitado que escolha um local.

	azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

Você pode considerar a passagem do sinalizador `--ssh` (Linux) ou do sinalizador `--rdp`(Windows) para `vm create` habilitar conexões remotas com a máquina virtual recém-criada.

Se você preferir provisionar uma máquina virtual de uma imagem personalizada, você pode criar uma imagem do arquivo .vhd com o comando `vm image create`, em seguida, usar o comando `vm create` para provisionar a máquina virtual. O exemplo a seguir mostra como criar uma imagem do Linux (chamada `myImage`) de um arquivo .vhd local. (O parâmetro `--location` especifica os dados nos quais a imagem está armazenada.)

	azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

Em vez de criar uma imagem de um .vhd local, você pode criar uma imagem de um .vhd armazenado no armazenamento de blob do Azure. Você pode fazer isso com o parâmetro `blob-url`:

	azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Depois de criar uma imagem, você pode provisionar uma máquina virtual a partir da imagem usando `vm create`. O comando abaixo cria uma máquina virtual chamada `myVM` a partir da imagem criada acima (`myImage`).

	azure vm create myVM myImage myusername --location "West US"

Depois que você tiver provisionado uma máquina virtual, você pode desejar criar pontos de extremidade para permitir acesso remoto à sua máquina virtual (por exemplo). O exemplo a seguir usa o comando `vm create endpoint` para abrir a porta 22 externa e porta 22 local em `myVM`:

	azure vm endpoint create myVM 22 22

Você pode obter informações detalhadas sobre uma máquina virtual (incluindo o endereço IP, nome DNS e informações de ponto de extremidade) com o comando `vm show`:

	azure vm show myVM

Para desligar, iniciar, ou reiniciar a máquina virtual, use um dos seguintes comandos:

	azure vm shutdown myVM
	azure vm start myVM
	azure vm restart myVM

E, finalmente, para excluir a máquina virtual, use comando o `vm delete`:

	azure vm delete myVM

Para obter uma lista completa dos comandos para criar e gerenciar máquinas virtuais, use a opção `-h`:

	azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com


