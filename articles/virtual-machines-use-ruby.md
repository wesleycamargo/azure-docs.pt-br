<properties linkid="manage-services-identity-multi-factor-authentication" urlDisplayName="O que é autenticação multifator do Azure?" pageTitle="O que é a autenticação multifator do Azure?" metaKeywords="" description="Saiba mais sobre a autenticação multifator do Azure, um método de autenticação que exige o uso de mais de um método de verificação e adiciona uma segunda camada crítica de segurança a entradas e transações dos usuários." metaCanonical="" services="active-directory,multi-factor-authentication" documentationCenter="" title="Como gerenciar as máquinas virtuais do Azure usando o Ruby" authors="larryfr" solutions="" manager="" editor="" />



#Como gerenciar as máquinas virtuais do Azure usando o Ruby

Este guia mostrará como executar tarefas de gerenciamento comuns de forma programática para máquinas virtuais do Azure, por exemplo, criar e configurar VMs e adicionar discos de dados. O SDK do Azure para Ruby fornece acesso à funcionalidade de gerenciamento de serviços para vários serviços do Azure, incluindo as máquinas virtuais do Azure.

##Sumário

* [O que é gerenciamento de serviços?](#what-is)
* [Conceitos](#concepts)
* [Criar um certificado de gerenciamento](#setup-certificate)
* [Criar um aplicativo Ruby](#create-app)
* [Configurar seu aplicativo para usar o SDK](#configure-access)
* [Configurar uma conexão de gerenciamento do Azure](#setup-connection)
* [Como: trabalhar com máquinas virtuais](#virtual-machine)
* [Como: trabalhar com imagens e discos](#vm-images)
* [Como trabalhar com serviços de nuvem](#cloud-services)
* [Como: trabalhar com serviços de armazenamento](#storage-services)
* [Próximas etapas](#next-steps)

## <a name="what-is"> </a>O que é gerenciamento de serviços?

O Azure fornece [APIs de REST para as operações de gerenciamento de serviços](http://msdn.microsoft.com/pt-br/library/windowsazure/ee460799.aspx), incluindo o gerenciamento de máquinas virtuais do Azure. O SDK do Azure para o Ruby expõe as operações de gerenciamento de máquinas virtuais por meio da classe **Azure::VirtualMachineSerivce**. A maior parte da funcionalidade de gerenciamento de máquina virtual disponível por meio do [Portal de Gerenciamento do Azure](https://manage.windowsazure.com) é acessível por meio dessa classe.

Embora a API de gerenciamento de serviços possa ser usada para gerenciar uma variedade de serviços hospedados no Azure, este documento fornece apenas detalhes para o gerenciamento de máquinas virtuais do Azure.

## <a name="concepts"> </a>Conceitos

As máquinas virtuais do Azure são implementadas como 'funções' dentro de um serviço de nuvem. Cada serviço de nuvem pode conter uma ou mais funções, que são logicamente agrupadas em implantações. A função define as características físicas gerais da VM, como a quantidade de memória disponível, a quantidade de núcleos de CPU etc.

Cada VM tem também um disco de SO, que contém o sistema operacional inicializável. Uma VM pode ter um ou mais discos de dados, que são discos adicionais que devem ser usados para armazenar dados de aplicativo. Os discos são implementados como discos rígidos virtuais (VHD) armazenados no armazenamento do blob do Azure. Os VHDs também podem ser expostos como 'imagens', que são modelos usados para criar discos usados por uma VM durante a criação da VM. Por exemplo, a criação de uma nova VM que usa uma imagem do Ubuntu resultará em um novo disco de SO criado a partir da imagem do Ubuntu.

A maioria das imagens são fornecidas pela Microsoft ou por parceiros, entretanto você pode criar suas próprias imagens ou uma imagem de uma máquina virtual hospedada no Azure.

## <a name="setup-certificate"> </a>Criar um certificado de gerenciamento do Azure

Quando executar operações de gerenciamento de serviços, como aqueles expostos por meio da classe **Azure::VirtualMachineService**, você deverá fornecer sua ID de assinatura do Azure e um arquivo contendo um certificado de gerenciamento para a sua assinatura. Ambos serão usados pelo SDK na autenticação na API do REST do Azure.

Você pode obter a ID de assinatura e um certificado de gerenciamento usando a interface de linha de comando entre plataformas do Azure (xplat-cli). Consulte [Instalar e configurar a interface de linha de comando entre plataformas do Azure](http://www.windowsazure.com/pt-br/manage/install-and-configure-cli/) para obter informações sobre como instalar e configurar a xplat-cli.

Depois que a xplat-cli estiver configurada, você poderá executar as etapas a seguir para recuperar sua ID de assinatura do Azure e exportar um certificado de gerenciamento:

1. Para recuperar a ID de assinatura, use:

		azure account list

2. Para exportar o certificado de gerenciamento, use o comando a seguir:

		azure account cert export

	Depois que o comando estiver concluído, o certificado será exportado para um arquivo denominado &lt;nome-de-assinatura-do-azure&gt;.pem. Por exemplo, se a sua assinatura tiver o nome **mygreatsubscription**, o arquivo criado será denominado **mygreatsubscription.pem**.

Anote a ID da assinatura e o local do arquivo PEM contendo o certificado exportado, pois eles serão usados posteriormente contida neste documento.

## <a name="create-app"></a>Criar um aplicativo Ruby

Criar um novo aplicativo Ruby. Os exemplos usados neste documento podem ser implementados em um único arquivo **.rb**.

## <a name="configure-access"></a>Configurar seu aplicativo

Para gerenciar os serviços do Azure, você precisa baixar e usar Azure gem, que contém o SDK do Azure para Ruby.

### Use o comando do gem para instalar o pacote

1.  Use uma interface de linha de comando, como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix), e navegue até a pasta em que criou o aplicativo de amostra.

2. Use o comando a seguir para instalar o azure gem:

		gem install azure

	Você deverá ver uma saída semelhante ao seguinte:

		Fetching: mini_portile-0.5.1.gem (100%)
		Fetching: nokogiri-1.6.0-x86-mingw32.gem (100%)
		Fetching: mime-types-1.25.gem (100%)
		Fetching: systemu-2.5.2.gem (100%)
		Fetching: macaddr-1.6.1.gem (100%)
		Fetching: uuid-2.3.7.gem (100%)
		Fetching: azure-0.5.0.gem (100%)
		Successfully installed mini_portile-0.5.1
		Successfully installed nokogiri-1.6.0-x86-mingw32
		Successfully installed mime-types-1.25
		Successfully installed systemu-2.5.2
		Successfully installed macaddr-1.6.1
		Successfully installed uuid-2.3.7
		Successfully installed azure-0.5.0
		7 gems installed

	<div class="dev-callout">
	<b>Observação</b>
	<p>Se você receber um erro relacionado a permissões, use <code>sudo gem install azure</code>.</p>
	</div>

### Exigir o gem

Usando um editor de texto, adicione o seguinte à parte superior do arquivo do aplicativo Ruby. Isso carregará o azure gem e disponibilizará o SDK do Azure para Ruby para o seu aplicativo:

	require 'azure'

## <a name="setup-connection"> </a>Como: conectar-se ao gerenciamento de serviços

Para executar as operações de gerenciamento de serviços com êxito com o Azure, você deve especificar a ID da assinatura e o certificado obtido na seção [Criar um certificado de gerenciamento do Azure](#setup-certificate). A maneira mais fácil de fazer isso é especificando a ID e o caminho do arquivo de certificado usando as variáveis de ambiente a seguir:

* AZURE\_MANAGEMENT\_CERTIFICATE - O caminho do arquivo .PEM contendo o certificado de gerenciamento.

* AZURE\_SUBSCRIPTION\_ID - A ID da sua assinatura do Azure.

Você também pode definir esses valores de forma programática no seu aplicativo usando o seguinte:

	Azure.configure do |config|
	  config.management_certificate = 'path/to/certificate'
	  config.subscription_id = 'subscription ID'
	end

##<a name="virtual-machine"> </a>Como: trabalhar com máquinas virtuais

As operações de gerenciamento de máquinas virtuais do Azure são executadas usando a classe **Azure::VirtualMachineService**.

###Como: criar uma nova máquina virtual

Para criar uma nova máquina virtual, use o método **create\_virtual\_machine**. Este método aceita um hash contendo os parâmetros a seguir e retorna uma instância **Azure::VirtualMachineManagement::VirtualMachine** que descreve a VM criada:

**Parâmetros**

* **:vm\_name** - O nome da máquina virtual

* **:vm\_user** - O nome do usuário raiz ou o nome de usuário do administrador

* **:password** - A senha a ser usada para o usuário raiz ou administrador

* **:image** - A imagem do SO que será usada para criar o disco de SO para esta VM. O disco de SO será armazenado em um VHD criado no armazenamento do blob.

* **:location** - A região em que a VM será criada. Essa deve ser a mesma região da conta de armazenamento que contém os discos usados por essa VM.

Este é um exemplo da criação de uma nova máquina virtual usando esses parâmetros:

	vm_params = {
	  :vm_name => 'mygreatvm',
	  :vm_user => 'myuser',
	  :password => 'mypassword',
	  :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-en-us-30GB',
	  :location = 'East US'
	}

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.create_virtual_machine(vm_params)

	puts "A VM named #{vm.vm_name} was created in a cloud service named #{vm.cloud_service_name}."
	puts "It uses a disk named #{vm.disk_name}, which was created from a #{vm.os_type}-based image."
	puts "The virtual IP address of the machine is #{vm.ipaddress}."
    puts "The fully qualified domain name is #{vm.cloud_service_name}.cloudapp.net."

**Opções**

Você pode fornecer um hash de parâmetros opcionais que lhe permita substituir o comportamento padrão da criação de VM, como especificar uma conta de armazenamento existente em vez de criar uma nova.

As opções a seguir estarão disponíveis quando você usar o método **create\_virtual\_machine**:

* **:storage\_account\_name** - O nome da conta de armazenamento a ser usada para armazenar imagens de disco. Se a conta de armazenamento ainda não existir, uma nova será criada. Se ela estiver omitida, uma nova conta de armazenamento será criada com um nome baseado no parâmetro :vm\_name.

* **:cloud\_service\_name** - O nome do serviço de nuvem a ser usado para hospedar a máquina virtual. Se o serviço de nuvem ainda não existir, um novo será criado. Se ele estiver omitido, uma nova conta de serviço de nuvem será criada com um nome baseado no parâmetro :vm\_name.

* **:deployment\_name** - O nome da implantação a ser usada ao implantar a configuração da máquina virtual

* **:tcp\_endpoints** - As portas TCP a serem expostas publicamente para esta VM. O ponto de extremidade do SSH (para VMs baseadas no Linux) e o ponto de extremidade do WinRM (para VMs baseadas no Windows) não precisam ser especificados e serão criados automaticamente. Várias portas podem ser especificadas, separadas por vírgula. Para associar uma porta interna a uma porta pública usando um número de porta diferente, use o formato **porta pública:porta interna**. Por exemplo, 80:8080 expõe a porta interna 8080 como porta pública 80.

* **:service\_location** - O local de destino de armazenamento do certificado na VM. Aplica-se somente às VMs baseadas no Windows.

* **:ssh\_private\_key\_file** - O arquivo contendo a chave privada, que será usada para proteger o acesso do SSH à VM baseada no Linux. Ele também será usado para especificar o certificado usado para proteger o WinRM se o transporte HTTPS estiver selecionado. Se **:ssh\_private\_key\_file** e **:ssh\_certificate\_file** estiverem omitidos, o SSH usará apenas a autenticação de senha

* **:ssh\_certificate\_file** - O arquivo contendo o arquivo de certificado, que será usado para proteger o acesso do SSH à VM baseada no Linux. Ele também será usado para especificar o certificado usado para proteger o WinRM se o transporte HTTPS estiver selecionado. Se **:ssh\_private\_key\_file** e **:ssh\_certificate\_file** estiverem omitidos, o SSH usará apenas a autenticação de senha

* **:ssh\_port** - A porta pública que será usada para a comunicação SSH. Se omitida, o padrão de porta do SSH será 22.

* **:vm\_size** - O tamanho da VM. Isso determina o tamanho de memória, o número de núcleos, a largura de banda e outras características físicas da VM. Consulte [Tamanhos de máquinas virtuais e serviços de nuvem para o Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/dn197896.aspx) para obter os tamanhos disponíveis e as características físicas.

* **:winrm_transport** - Uma matriz dos transportes disponíveis para uso com o WinRM. Os transportes válidos são 'http' e 'https'. Se 'https' for especificado como transporte, você também deverá usar **:ssh\_private\_key\_file** e **:ssh\_certificate\_file** para especificar o certificado usado para proteger as comunicações HTTPS.

Este é um exemplo de criação de uma nova máquina virtual que usa uma instância de computação pequena, expõe as portas publicamente para os tráfegos HTTP (porta local 8080, pública porta 80) e HTTPS (443) e permite a autenticação de certificado para as sessões SSH usando os arquivos de certificado especificados:

	vm_params = {
	  :vm_name => 'myvm',
	  :vm_user => 'myuser',
	  :password => 'mypassword',
	  :image => 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20130824-en-us-30GB',
	  :location = 'East US'
	}

	vm_opts = {
      :tcp_endpoints => '80:8080,443',
      :vm_size => 'Small',
      :ssh_private_key_file => '../sshkey/mykey.key',
      :ssh_certificate_file => '../sshkey/mykey.pem'
	}

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.create_virtual_machine(vm_params, vm_opts)

###Como: listar máquinas Virtuais

Para listar as máquinas virtuais existentes para a sua assinatura do Azure, use o método **list\_virtual\_machines**. Esse método retorna uma matriz de objetos **Azure::VirtualMachineManagement::VirtualMachine**:

	vm_mgr = Azure::VirtualMachineService.new
	virtual_machines = vm_mgr.list_virtual_machines

###Como: obter informações em uma máquina virtual

Para obter uma instância de **Azure::VirtualMachineManagement::VirtualMachine** para uma máquina virtual específica, use o método **get\_virtual\_machine** e atribua nomes à máquina virtual e ao serviço de nuvem:

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.get_virtual_machine('myvm', 'mycloudservice')

###Como: excluir uma máquina virtual

Para excluir uma máquina virtual, use o método **delete\_virtual\_machine** e atribua nomes à máquina virtual e ao serviço de nuvem:

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.delete_virtual_machine('myvm', 'mycloudservice')

<div class="dev-callout">
<b>Aviso</b>
<p>O método <b>delete_virtual_machine</b> exclui o serviço de nuvem e todos os discos associados à máquina virtual.</p>
</div>

###Como: desligar uma máquina virtual

Para desligar uma máquina virtual, use o método **shutdown\_virtual\_machine** e atribua nomes à máquina virtual e ao serviço de nuvem:

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.shutdown_virtual_machine('myvm', 'mycloudservice')

###Como: iniciar uma máquina virtual

Para iniciar uma máquina virtual, use o método **start\_virtual\_machine** e atribua nomes à máquina virtual e ao serviço de nuvem:

	vm_mgr = Azure::VirtualMachineService.new
	vm = vm_mgr.start_virtual_machine('myvm', 'mycloudservice')

##<a name="vm-images"> </a>Como: trabalhar com imagens e discos de máquinas virtuais

As operações nas imagens de máquinas virtuais são executadas usando a classe **Azure::VirtualMachineImageService**. As operações nos discos são executadas usando a classe **Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService**.

###Como: listar imagens de máquinas virtuais

Para listar as imagens de máquinas virtuais disponíveis, use o método **list\_virtual\_machine\_images**. Isso retornará uma matriz de objetos **Azure::VirtualMachineImageService**.

	image_mgr = Azure::VirtualMachineImageService.new
	images = image_mgr.list_virtual_machine_images

###Como: listar discos

Para listar discos para a sua assinatura do Azure, use o método **list\_virtual\_machine\_disks**. Isso retornará uma matriz de objetos **Azure::VirtualMachineImageManagement::VirtualMachineDisk**.

	disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
	disks = disk_mgr.list_virtual_machine_disks

###Como: excluir um disco

Para excluir um disco, use o método **delete\_virtual\_machine\_disk** e especifique o nome do disco a ser excluído:

	disk_mgr = Azure::VirtualMachineImageManagement::VirtualMachineDiskManagementService.new
	disk_mgr.delete_virtual_machine_disk

##<a name="cloud-services"> </a>Como: trabalhar com serviços de nuvem

As operações de gerenciamento dos serviços de nuvem do Azure são executadas usando a classe **Azure::CloudService**.

###Como: criar um serviço de nuvem

Para criar um novo serviço de nuvem, use o método **create\_cloud\_service** a atribua um nome e um hash de opções. As opções válidas são:

* **:location** - *Obrigatório*. A região em que o serviço de nuvem será criado.

* **:description** - uma descrição do serviço de nuvem.

O exemplo a seguir cria um novo serviço de nuvem na região Leste dos EUA:

	cs_mgr = Azure::CloudService.new
	cs_mgr.create_cloud_service('mycloudservice', { :location => 'East US' })

###Como: listar serviços de nuvem

Para listar os serviços de nuvem para a sua assinatura do Azure, use o método **list\_cloud\_services**. Esse método retorna uma matriz de objetos **Azure::CloudServiceManagement::CloudService**:

	cs_mgr = Azure::CloudService.new
	cloud_services = cs_mgr.list_cloud_services

###Como: verificar se já existe um serviço de nuvem

Para verificar se já existe um serviço de nuvem específico, use o método **get\_cloud\_service** e atribua o nome do serviço de nuvem. Retornará **true** se existir um serviço de nuvem do nome especificado; caso contrário, retornará **false**.

	cs_mgr = Azure::CloudService.new
	cs_exists = cs_mgr.get_cloud_service('mycloudservice')

###Como: excluir um serviço de nuvem

Para excluir um serviço de nuvem, use o método **delete\_cloud\_service** e atribua o nome do serviço de nuvem:

	cs_mgr = Azure::CloudService.new
	cs_mgr.delete_cloud_service('mycloudservice')

###Como: excluir uma implantação

Para excluir uma implantação para um serviço de nuvem, use o método **delete\_cloud\_service\_deployment** e atribua o nome do serviço de nuvem:

	cs_mgr = Azure::CloudService.new
	cs_mgr.delete_cloud_service_deployment('mycloudservice')

##<a name="storage-services"> </a>Como: trabalhar com serviços de armazenamento

As operações de gerenciamento dos serviços de nuvem do Azure são executadas usando a classe **Azure::StorageService**.

###Como: criar uma conta de armazenamento

Para criar uma nova conta de armazenamento, use o método **create\_storage\_account** a atribua um nome e um hash de opções. As opções válidas são:

* **:location** - *Obrigatório*. A região em que a conta de armazenamento será criada.

* **:description** - uma descrição da conta de armazenamento.

O exemplo a seguir cria uma nova conta de armazenamento na região 'Leste dos EUA':

	storage_mgr = Azure::StorageService.new
	storage_mgr.create_storage_account('mystorage', { :location => 'East US' })

###Como: listar contas de armazenamento

Para obter uma lista de contas de armazenamento para a sua assinatura do Azure, use o método **list\_storage\_accounts**. Esse método retorna uma matriz de objetos **Azure::StorageManagement::StorageAccount**.

	storage_mgr = Azure::StorageService.new
	accounts = storage_mgr.list_storage_accounts

###Como: verificar se existe uma conta de armazenamento

Para verificar se existe uma conta de armazenamento específica, use o método **get\_storage\_account** e defina o nome da conta de armazenamento. Retornará **true** se a conta de armazenamento existir; caso contrário, retornará **false**.

	storage_mgr = Azure::StorageService.new
	store_exists = storage_mgr.get_storage_account('mystorage')

###Como: excluir uma conta de armazenamento

Para excluir uma conta de armazenamento, use o método **delete\_storage\_account** e especifique o nome da conta de armazenamento:

	storage_mgr = Azure::StorageService.new
	storage_mgr.delete_storage_account('mystorage')

##<a name="next-steps"> </a>Próximas etapas

Agora que você aprendeu as noções básicas de criação de máquinas virtuais do Azure de forma programática, siga estes links para saber como executar mais tarefas ao trabalhar com VMs.

* Visite a página de recursos [Máquinas virtuais](http://www.windowsazure.com/pt-br/documentation/services/virtual-machines/)
*  Consulte a referência de MSDN: [Máquinas virtuais](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156003.aspx)
* Saiba como hospedar um [Ruby no aplicativo Rails em uma máquina virtual](http://www.windowsazure.com/pt-br/develop/ruby/tutorials/web-app-with-linux-vm/)

