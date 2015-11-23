<properties 
	pageTitle="Usar SSH no Linux e Mac | Microsoft Azure" 
	description="Gerar e usar chaves SSH no Linux e no Mac para os modelos de implantação clássico e do Gerenciador de Recursos no Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/28/2015" 
	ms.author="rasquill"/>

#Como usar SSH com Linux e Mac no Azure

> [AZURE.SELECTOR]
- [Windows](../articles/virtual-machines/virtual-machines-windows-use-ssh-key.md)
- [Linux/Mac](../articles/virtual-machines/virtual-machines-linux-use-ssh-key.md)

Este tópico descreve como usar **ssh-keygen** e **openssl** em Linux e Mac para criar e usar arquivos no formato **ssh-rsa** e no formato **.pem** a fim de proteger a comunicação com VMs do Azure baseadas em Linux. A criação e Máquinas virtuais do Azure baseadas em Linux usando o modelo de implantação do Gerenciador de Recursos é recomendada para novas implantações e utiliza um arquivo ou cadeia de caracteres de chave pública do tipo *ssh-rsa* (dependendo do cliente de implantação). O [portal de visualização](https://portal.azure.com) aceita no momento apenas as cadeias de caracteres no formato **ssh-rsa** para implantações do Gerenciador de Recursos ou clássica.

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]Para criar esses tipos de arquivos e usá-los em um computador com Windows para comunicar-se com segurança com VMs Linux no Azure, consulte [Usar chaves SSH no Windows](virtual-machines-windows-use-ssh-key.md).

## De quais arquivos você precisa?

Uma configuração ssh básica para o Azure inclui um par de chaves **ssh-rsa** pública e privada de 2048 bits (por padrão, **ssh-keygen** armazena esses arquivos como **~/.ssh/id\_rsa** e **~/.ssh/id-rsa.pub**, a menos que você mude os padrões) e também um arquivo `.pem` gerado com a chave privada **id\_rsa** para uso com o modelo de implantação clássico do portal clássico.

Estes são os cenários de implantação e os tipos de arquivo que você usa em cada um deles:

1. As chaves **ssh-rsa** são exigidas para qualquer implantação que use o [portal de visualização](https://portal.azure.com), independentemente do modelo de implantação.
2. O arquivo .pem é necessário para criar VMs usando o [portal clássico](https://manage.windowsazure.com). Arquivos .pem também recebem suporte em implantações clássicas que usam a [CLI do Azure](xplat-cli-install.md). 

## Criar chaves para uso com SSH

O Azure exige arquivos de chave no formato **ssh-rsa** de 2048 bits, ou arquivos .pem equivalentes, dependendo do cenário. Se você já tiver esses arquivos, passe o arquivo de chave pública ao criar sua VM do Azure.

Se você precisar criar os arquivos:

1. Verifique se sua implementação de **ssh-keygen** e **openssl** está atualizada. Isso varia de acordo com a plataforma. 

	- Para Mac, visite o [site de Segurança de produtos da Apple](https://support.apple.com/HT201222) e escolha as atualizações adequadas, se for necessário.
	- Para distribuições Linux com base em Debian, como Ubuntu, Debian, Mint etc:

			sudo apt-get update ssh-keygen
			sudo apt-get update openssl

	- Para distribuições Linux com base em RPM, como CentOS e Oracle Linux:

			sudo yum update ssh-keygen
			sudo yum update openssl

	- Para SLES e OpenSUSE

			sudo zypper update ssh-keygen
			sudo zypper update openssl

2. Use **ssh-keygen** para criar arquivos de chave pública e privada de RSA de 2048 bits. Além disso, a menos que você tenha um local específico ou nomes específicos para os arquivos, aceite o local e o nome padrão de `~/.ssh/id_rsa`. O comando básico é:

		ssh-keygen -t rsa -b 2048 

	Normalmente, sua implementação de **ssh-keygen** adiciona um comentário, geralmente o nome de usuário e o nome do host do computador. Você pode especificar um comentário usando a opção `-C`.

3. Criar um arquivo .pem de seu arquivo `~/.ssh/id_rsa` para que você possa trabalhar com o portal clássico. Use **openssl** da seguinte maneira:

		openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

	Se você quiser criar um arquivo .pem de um arquivo de chave privada diferente, modifique o argumento `-key`.

> [AZURE.NOTE]Se você planeja gerenciar os serviços implantados com o modelo de implantação clássico, convém criar também um arquivo no formato **.cer** para carregar no portal, embora isso não envolva **ssh** ou a conexão com VMS do Linux, que é o assunto deste artigo. Para criar esses arquivos no Linux ou no Mac, digite:<br /> openssl.exe x509 -outform der -in myCert.pem -out myCert.cer

Para converter o arquivo .pem em um arquivo de certificado X509 codificado em DER.

## Usar as chaves SSH que você já possui

Você pode usar as chaves ssh-rsa (`.pub`) para todo trabalho novo, especialmente com o modelo de implantação do Gerenciador de Recursos e o portal de visualização; talvez seja necessário criar um arquivo `.pem` de suas chaves, se você precisar usar o portal clássico.

## Criar uma VM com o arquivo de chave pública

Depois de criar os arquivos necessários, há muitas maneiras de criar uma VM com a qual você pode se conectar com segurança usando uma troca de chaves pública-privada. Em quase todas as situações, especialmente ao usar implantações do Gerenciador de Recursos, passe o arquivo .pub quando receber uma solicitação por um caminho de arquivo de chave ssh ou o conteúdo de um arquivo como uma cadeia de caracteres.

### Exemplo: criando uma VM com o arquivo id\_rsa.pub

O uso mais comum é durante a criação obrigatória de uma VM, ou durante o carregamento de um modelo para criar uma VM. O exemplo de código a seguir mostra como criar uma nova VM Linux segura no Azure passando o nome de arquivo público (neste caso, o arquivo padrão `~/.ssh/id_rsa`) para o comando `azure vm create`. (Os outros argumentos foram criados anteriormente.)

	azure vm create \
	--nic-name testnic \
	--public-ip-name testpip \
	--vnet-name testvnet \
	--vnet-subnet-name testsubnet \
	--storage-account-name computeteststore 
	--image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
	--username ops \
	-ssh-publickey-file ~/.ssh/id_rsa \
	testrg testvm westeurope linux

O próximo exemplo mostra o uso do formato **ssh-rsa** com um modelo do Gerenciador de Recursos e a CLI do Azure para criar uma VM Ubuntu protegida por um nome de usuário e o conteúdo de `~/.ssh/id_rsa.pub` como uma cadeia de caracteres. (Nesse caso, a cadeia de caracteres de chave pública é reduzida para ser mais legível.)

	azure group deployment create \
	--resource-group test-sshtemplate \
	--template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
	--name mysshdeployment
	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	testnewStorageAccountName: testsshvmtemplate3
	adminUserName: ops
	sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQA+/L+rHIjz+nXTzxApgnP+iKDZco9 user@macbookpro
	dnsNameForPublicIP: testsshvmtemplate
	location: West Europe
	vmName: sshvm
	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "mysshdeployment"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mysshdeployment
	data:    ResourceGroupName  : test-sshtemplate
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-08T00:12:12.2529678Z
	data:    Mode               : Incremental
	data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
	data:    ContentVersion     : 1.0.0.0
	data:    Name                   Type    Value

	data:    newStorageAccountName  String  testtestsshvmtemplate3
	data:    adminUserName          String  ops
	data:    sshKeyData             String  ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDAkek3P6V3EhmD+xP+iKDZco9 user@macbookpro
	data:    dnsNameForPublicIP     String  testsshvmtemplate
	data:    location               String  West Europe
	data:    vmSize                 String  Standard_A2
	data:    vmName                 String  sshvm
	data:    ubuntuOSVersion        String  14.04.2-LTS
	info:    group deployment create command OK


### Exemplo: criando uma VM com um arquivo .pem

Em seguida, você pode usar o arquivo .pem com o portal clássico ou com o modo de implantação clássico e `azure vm create`, conforme o exemplo a seguir:

	azure vm create \
	-l "West US" -n testpemasm \
	-P -t myCert.pem -e 22 \
	testpemasm \
	b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-pt-BR-30GB \
	ops
	info:    Executing command vm create
	warn:    --vm-size has not been specified. Defaulting to "Small".
	+ Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-pt-BR-30GB
	+ Looking up cloud service
	info:    cloud service testpemasm not found.
	+ Creating cloud service
	+ Retrieving storage accounts
	+ Configuring certificate
	+ Creating VM
	info:    vm create command OK


## Conectar-se à sua VM

O comando **ssh** usa um nome de usuário para fazer logon, endereço de rede do computador e a porta com a qual se conectar ao endereço – além de muitas outras variações especiais. (Para saber mais sobre **ssh**, comece [aqui](https://en.wikipedia.org/wiki/Secure_Shell))

Um uso típico com a implantação do Gerenciador de Recursos pode parecer com o seguinte, se você tiver especificado apenas um subdomínio e um local de implantação:

	ssh user@subdomain.westus.cloudapp.azure.com -p 22

ou, se você estiver se conectando a um serviço de nuvem de implantação clássica, o endereço usado teria esta aparência:

	ssh user@subdomain.cloudapp.net -p 22

Como a forma do endereço pode mudar (você sempre pode usar o endereço IP ou talvez você tenha um nome de domínio personalizado atribuído), você precisará descobrir o endereço de sua VM do Azure.

### Descobrindo o endereço SSH de sua VM do Azure com implantações clássicas

Você pode descobrir o endereço a ser usado com uma VM e o modelo de implantação clássico usando o comando `azure vm show` com o nome da VM:

	azure vm show testpemasm
	info:    Executing command vm show
	+ Getting virtual machines
	data:    DNSName "testpemasm.cloudapp.net"
	data:    Location "West US"
	data:    VMName "testpemasm"
	data:    IPAddress "100.116.160.154"
	data:    InstanceStatus "ReadyRole"
	data:    InstanceSize "Small"
	data:    Image "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-pt-BR-30GB"
	data:    OSDisk hostCaching "ReadWrite"
	data:    OSDisk name "testpemasm-testpemasm-0-201510102050230517"
	data:    OSDisk mediaLink "https://portalvhds4blttsxgjj1rf.blob.core.windows.net/vhd-store/testpemasm-2747c9c432b043ff.vhd"
	data:    OSDisk sourceImageName "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_3-LTS-amd64-server-20150908-pt-BR-30GB"
	data:    OSDisk operatingSystem "Linux"
	data:    OSDisk iOType "Standard"
	data:    ReservedIPName ""
	data:    VirtualIPAddresses 0 address "40.83.178.221"
	data:    VirtualIPAddresses 0 name "testpemasmContractContract"
	data:    VirtualIPAddresses 0 isDnsProgrammed true
	data:    Network Endpoints 0 localPort 22
	data:    Network Endpoints 0 name "ssh"
	data:    Network Endpoints 0 port 22
	data:    Network Endpoints 0 protocol "tcp"
	data:    Network Endpoints 0 virtualIPAddress "40.83.178.221"
	data:    Network Endpoints 0 enableDirectServerReturn false
	info:    vm show command OK

### Descobrindo o endereço SSH de sua VM do Azure com implantações do Gerenciador de Recursos

	azure vm show testrg testvm
	info:    Executing command vm show
	+ Looking up the VM "testvm"
	+ Looking up the NIC "testnic"
	+ Looking up the public ip "testpip"

Examine a seção de perfil de rede:

	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-21-8E-AE
	data:          Provisioning State        :Succeeded
	data:          Name                      :testnic
	data:          Location                  :westeurope
	data:            Private IP alloc-method :Static
	data:            Private IP address      :192.168.1.101
	data:            Public IP address       :40.115.48.189
	data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
	data:
	data:    Diagnostics Instance View:
	info:    vm show command OK

Se você não usou a porta SSH padrão 22 ao criar a máquina virtual, será possível descobrir quais portas têm regras de entrada com o comando `azure network nsg show`, como no exemplo a seguir:

	azure network nsg show testrg testnsg
	info:    Executing command network nsg show
	+ Looking up the network security group "testnsg"
	data:    Id                              : /subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testnsg
	data:    Name                            : testnsg
	data:    Type                            : Microsoft.Network/networkSecurityGroups
	data:    Location                        : westeurope
	data:    Provisioning state              : Succeeded
	data:    Security group rules:
	data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
	data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
	data:    testnsgrule                    *                  *            *               22                Tcp       Inbound    Allow   1000
	data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
	data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
	data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
	data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
	data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
	data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
	info:    network nsg show command OK

### Exemplo: saída da sessão SSH usando chaves .pem e a implantação clássica

Se você criou uma VM usando um arquivo .pem criado a partir de seu arquivo `~/.ssh/id_rsa`, poderá direcionar o ssh para essa VM. Observe que quando você faz isso, o handshake de certificado usará sua chave privada em `~/.ssh/id_rsa`. Isso deverá parecer com o exemplo a seguir:

	ssh ops@testpemasm.cloudapp.net -p 22
	The authenticity of host 'testpemasm.cloudapp.net (40.83.178.221)' can't be established.
	RSA key fingerprint is dc:bb:e4:cc:59:db:b9:49:dc:71:a3:c8:37:36:fd:62.
	Are you sure you want to continue connecting (yes/no)? yes
	Warning: Permanently added 'testpemasm.cloudapp.net,40.83.178.221' (RSA) to the list of known hosts.
	Saving password to keychain failed
	Identity added: /Users/rasquill/.ssh/id_rsa (/Users/rasquill/.ssh/id_rsa)
	Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)

	* Documentation:  https://help.ubuntu.com/

	System information as of Sat Oct 10 20:53:08 UTC 2015

	System load: 0.52              Memory usage: 5%   Processes:       80
	Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0

	Graph this data and manage this system at:
		https://landscape.canonical.com/

	Get cloud support with Ubuntu Advantage Cloud Guest:
		http://www.ubuntu.com/business/services/cloud

	0 packages can be updated.
	0 updates are security updates.

	The programs included with the Ubuntu system are free software;
	the exact distribution terms for each program are described in the
	individual files in /usr/share/doc/*/copyright.

	Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
	applicable law.

## Se você tiver problemas para se conectar

Leia as sugestões em [Solucionando problemas de conexões SSH](virtual-machines-troubleshoot-ssh-connections.md) para ver se elas podem ajudar a resolver a situação.

## Próximas etapas
 
Agora que você conectou-se à sua VM, atualize sua distribuição escolhida antes de continuar a usá-la.

<!---HONumber=Nov15_HO3-->