<properties
    pageTitle="Criar uma Máquina virtual do SQL Server no Azure PowerShell (Gerenciador de Recursos) | Microsoft Azure"
    description="Fornece etapas e scripts do PowerShell para criar uma VM do Azure com imagens da galeria de máquinas virtuais do SQL Server."
	services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/20/2016"
	ms.author="jroth"/>

# Provisionar uma máquina virtual SQL Server usando o Azure PowerShell (Gerenciador de Recursos)

> [AZURE.SELECTOR]
- [Portal](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

## Visão geral

Este tutorial mostra como criar uma única máquina virtual do Azure usando o modelo de implantação do **Azure Resource Manager** usando os cmdlets do Azure PowerShell. Neste tutorial, criaremos uma única máquina virtual usando uma única unidade de disco de uma imagem na Galeria de SQL. Vamos criar novos provedores de armazenamento, rede e recursos de computação que serão usados pela máquina virtual. Se você tiver provedores existentes para qualquer um desses recursos, poderá usar tais provedores.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implantação clássico. Se você precisar da versão clássica deste tópico, consulte [Provisionar uma máquina virtual do SQL Server usando o Azure PowerShell Clássico](virtual-machines-windows-classic-ps-sql-create.md).

## Pré-requisitos

Para este tutorial, será necessário:

- Uma conta do Azure e uma assinatura antes de começar. Se não tiver uma, inscreva-se em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [Azure PowerShell](../powershell-install-configure.md), versão mínima do 1.0.0 ou posterior (este tutorial foi escrito usando a versão 1.0.4).
    - Para recuperar a versão, digite **Get-Module Azure -ListAvailable**.

## Configurar sua assinatura

Abra o Windows PowerShell e estabeleça o acesso à sua conta do Azure ao executar o cmdlet a seguir. Você encontrará uma tela de para se conectar e inserir suas credenciais. Use o mesmo email e senha usados para entrar no Portal do Azure.

	Add-AzureRmAccount

Depois de se conectar com êxito, você encontrará algumas informações na tela, incluindo a SubscriptionId usada para entrar. Esta é a assinatura em que os recursos para este tutorial serão criados, a menos que você altere para uma assinatura diferente. Se você tiver várias SubscriptionIds, execute o seguinte cmdlet para retornar uma lista de todas as suas SubscriptionIds:

	Get-AzureRmSubscription

Para alterar para outra SubscriptionID, execute o seguinte cmdlet com sua SubscriptionId desejada.

	Select-AzureRmSubscription -SubscriptionId xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

## Definir variáveis de imagem

Para simplificar a usabilidade e a compreensão do script concluído deste tutorial, vamos começar definindo um número de variáveis. Altere os valores de parâmetro como achar melhor, mas lembre-se de restrições de nomenclatura relacionadas a tamanhos de nome e caracteres especiais ao modificar os valores fornecidos.

### Local e Grupo de Recursos
Use duas variáveis para definir a região de dados e o grupo de recursos no qual você criará outros recursos para a máquina virtual.

Modifique conforme desejado e, em seguida, execute os seguintes cmdlets para inicializar essas variáveis.

	$Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"

### Propriedades de armazenamento

Use as seguintes variáveis para definir a conta de armazenamento e o tipo de armazenamento a ser usado pela máquina virtual.

Modifique conforme desejado e, em seguida, execute o seguinte cmdlet para inicializar essas variáveis. Observe que neste exemplo estamos usando [Armazenamento Premium](../storage/storage-premium-storage.md), que é recomendado para cargas de trabalho de produção. Para obter detalhes sobre essas diretrizes e outras recomendações, consulte [Melhores práticas para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md).

    $StorageName = $ResourceGroupName + "storage"
    $StorageType = "Premium_LRS"

### Propriedades da rede

Use as seguintes variáveis para definir a interface de rede, o método de alocação de TCP/IP, o nome da rede virtual, o nome da sub-rede virtual, o intervalo de endereços IP para a rede virtual, o intervalo de endereços IP para a sub-rede e o rótulo de nome de domínio público a ser usado pela rede na máquina virtual.

Modifique conforme desejado e, em seguida, execute o seguinte cmdlet para inicializar essas variáveis.

    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $TCPIPAllocationMethod = "Dynamic"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $DomainName = "sqlvm1"   

### Propriedades de máquina virtual

Use as seguintes variáveis para definir o nome da máquina virtual, o nome do computador, o tamanho da máquina virtual e o nome do disco do sistema operacional da máquina virtual.

Modifique conforme desejado e, em seguida, execute o seguinte cmdlet para inicializar essas variáveis.

    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

### Propriedades da imagem

Use as seguintes variáveis para definir a imagem a ser usada para a máquina virtual. Neste exemplo, a imagem de avaliação do SQL Server 2016 RC3 é usada.

Modifique conforme desejado e, em seguida, execute o seguinte cmdlet para inicializar essas variáveis.

    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016RC3-WS2012R2"
    $Sku = "Evaluation"
    $Version = "latest"

Observe que você pode obter uma lista completa das ofertas de imagem do SQL Server com o comando Get-AzureRmVMImageOffer:

    Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'

E você pode ver as SKUs disponíveis para uma oferta com o comando Get-AzureRmVMImageSku. O comando a seguir mostra todas as SKUs disponíveis para a oferta **SQL2014SP1-WS2012R2**.

    Get-AzureRmVMImageSku -Location 'East US' -Publisher 'MicrosoftSQLServer' -Offer 'SQL2014SP1-WS2012R2' | Select Skus

## Criar um grupo de recursos

Com o modelo de implantação do Gerenciador de Recursos, o primeiro objeto criado por você é o grupo de recursos. Usaremos o cmdlet [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt678985.aspx) para criar um grupo de recursos do Azure e seus recursos com o nome do grupo de recursos e local definido pelas variáveis que inicializada anteriormente.

Execute o seguinte cmdlet para criar novo grupo de recursos.

    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

## Criar uma conta de armazenamento

A máquina virtual exige recursos de armazenamento para o disco do sistema operacional para os arquivos de log de dados do SQL Server. Para simplificar, criaremos um único disco para ambos. Você pode anexar discos adicionais posteriormente usando o cmdlet [Adicionar Disco do Azure](https://msdn.microsoft.com/library/azure/dn495252.aspx) para colocar seus dados do SQL Server e o log de arquivos em discos dedicados. Usaremos o cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) para criar a conta de armazenamento em seu novo grupo de recursos e com o nome da conta de armazenamento, um nome de armazenamento e um local definido usando as variáveis inicializada anteriormente.

Execute o cmdlet a seguir para criar sua nova conta de armazenamento.

    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -Type $StorageType -Location $Location

## Criar recursos da rede

A máquina virtual requer um número de recursos de rede para conectividade de rede.

- Cada máquina virtual exige um rede virtual.
- Uma rede virtual deve ter pelo menos uma sub-rede definida.
- Uma interface de rede deve ser definida com um público ou um endereço IP privado.

### Criar uma configuração de sub-rede da rede virtual

Vamos começar criando uma configuração de sub-rede para nossa rede virtual. Para nosso tutorial, vamos criar uma sub-rede padrão usando o cmdlet [New-AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx). Criaremos nossa configuração de sub-rede da rede virtual com o prefixo de nome e endereço de sub-rede definido usando as variáveis inicializadas anteriormente.

>[AZURE.NOTE] Você pode definir propriedades adicionais da configuração de sub-rede da rede virtual usando esse cmdlet, mas isso está além do escopo deste tutorial.

Execute o seguinte cmdlet para criar a configuração da sub-rede virtual.

    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix

### Criar uma rede virtual

Em seguida, criaremos nossa rede virtual usando o cmdlet [New-AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx). Criaremos nossa rede virtual no novo grupo de recursos, com o nome, local e endereço de prefixo definido usando as variáveis inicializadas anteriormente e com a configuração de sub-rede que você definiu na etapa anterior.

Execute o seguinte cmdlet para criar a configuração da rede virtual.

    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig

### Criar um endereço IP público

Agora que temos nossa rede virtual definida, precisamos configurar um endereço IP para conectividade com a máquina virtual. Para este tutorial, criaremos um endereço IP público usando endereçamento IP dinâmico para dar suporte à conectividade com a Internet. Usaremos o cmdlet [New-AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) para criar o endereço IP público no grupo de recursos criado anteriormente criado e com o nome, o local, o método de alocação e o rótulo de nome de domínio DNS definido usando as variáveis inicializadas anteriormente.

>[AZURE.NOTE] Você pode definir propriedades adicionais do endereço IP público usando esse cmdlet, mas isso está além do escopo deste tutorial. Você também pode criar um endereço particular ou um endereço com um endereço estático, mas isso também está além do escopo deste tutorial.

Execute o seguinte cmdlet para criar o endereço IP público.

    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName

### Criar a interface de rede

Agora estamos prontos para criar a interface de rede que nossa máquina virtual usará. Usaremos o cmdlet [New-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) para criar nossa interface de rede no grupo de recursos criado anteriormente e com nome, local, sub-rede e endereço IP público definido anteriormente.

Execute o seguinte cmdlet para criar sua interface de rede.

    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

## Configurar um objeto da VM

Agora que temos recursos de armazenamento e rede definidos, estamos prontos para definir recursos de computação para a máquina virtual. Para nosso tutorial, vamos especificar varias propriedades do sistema operacional e tamanhos de máquina virtual, especificar a interface de rede criada anteriormente, definir o armazenamento de blobs e, em seguida, especificar o disco do sistema operacional.

### Criar o objeto da VM

Vamos começar especificando o tamanho da máquina virtual. Para este tutorial, estamos especificando um DS13. Usaremos o cmdlet [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) para criar um objeto de máquina virtual configurável com o nome e o tamanho definidos usando as variáveis inicializadas anteriormente.

Execute o seguinte cmdlet para criar o objeto da máquina virtual.

    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize

### Criar um objeto de credencial para armazenar o nome e a senha para as credenciais de administrador local

Antes que possamos definir as propriedades de sistema operacional da máquina virtual, precisamos fornecer as credenciais da conta de administrador local como uma cadeia de caracteres segura. Para fazer isso, usaremos o cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx).

Execute o cmdlet a seguir e, na janela de solicitação de credencial do Windows PowerShell, digite o nome e a senha a ser usada para a conta de administrador local na máquina virtual do Windows.

    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."

### Definir as propriedades de sistema operacional da máquina virtual

Agora estamos prontos para definir as propriedades de sistema operacional da máquina virtual. Usaremos o cmdlet [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) para definir o tipo de sistema operacional como Windows, para exigir que o [agente da máquina virtual](virtual-machines-windows-classic-agents-and-extensions.md) seja instalado, para especificar que o cmdlet habilite a atualização automática e para definir o nome da máquina virtual, o nome do computador e a credencial usando as variáveis que você inicializou anteriormente.

Execute o seguinte cmdlet para definir as propriedades de sistema operacional de sua máquina virtual.

    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate

### Adicionar a interface de rede à máquina virtual

Em seguida, adicionaremos a interface de rede que criamos anteriormente à máquina virtual. Usaremos o cmdlet [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) para adicionar a interface de rede usando a variável de interface de rede que você definiu anteriormente.

Execute o seguinte cmdlet para definir a interface de rede para sua máquina virtual.

    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id

### Definir o local de armazenamento de blobs para o disco a ser usado pela máquina virtual

Em seguida, definiremos o local de armazenamento de blobs para o disco a ser usado pela máquina virtual usando as variáveis que você definiu anteriormente.

Execute o seguinte cmdlet para definir o local de armazenamento de blobs.

    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"

### Definir as propriedades de disco do sistema operacional para a máquina virtual

Em seguida, definiremos as propriedades de disco do sistema operacional para a máquina virtual. Usaremos o cmdlet [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) para especificar que o sistema operacional da máquina virtual será proveniente de uma imagem, para definir o cache para somente leitura (pois o SQL Server é instalado no mesmo disco) e para definir o nome da máquina virtual e o disco do sistema operacional definido usando as variáveis que definimos anteriormente.

Execute o seguinte cmdlet para definir as propriedades de disco do sistema operacional de sua máquina virtual.

    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

### Especificar a imagem da plataforma para a máquina virtual

Nossa última etapa da configuração é especificar a imagem de plataforma para nossa máquina virtual. Para nosso tutorial, estamos usando a imagem mais recente do SQL Server 2016 CTP. Usaremos o cmdlet [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) para usar essa imagem, conforme definido pelas variáveis que você definiu anteriormente.

Execute o seguinte cmdlet para especificar a imagem da plataforma para sua máquina virtual.

    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## Criar a VM de SQL

Agora que você concluiu as etapas de configuração, você está pronto para criar a máquina virtual. Usaremos o cmdlet [New-AzureRmVM](https://msdn.microsoft.com/library/mt603754.aspx) para criar a máquina virtual usando as variáveis que definimos.

Execute o seguinte cmdlet para criar sua máquina virtual.

    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

A máquina virtual é criada. Observe que a conta de armazenamento padrão é criada para o diagnóstico de inicialização porque a conta de armazenamento especificada para o disco da máquina virtual é uma conta de armazenamento premium.

Agora você pode exibir esta máquina no Portal do Azure para ver [seu endereço IP público e o nome de domínio totalmente qualificado](virtual-machines-windows-portal-sql-server-provision.md#Connect).

## Script de exemplo

O script a seguir contém o script completo do PowerShell para este tutorial. Ele pressupõe que você já tenha instalado a assinatura do Azure para usar com os comandos **Add-AzureRmAccount** e **Select-AzureRmSubscription**.


    # Variables
    ## Global
    $Location = "SouthCentralUS"
    $ResourceGroupName = "sqlvm1"
    ## Storage
    $StorageName = $ResourceGroupName + "storage"
    $StorageType = "Premium_LRS"

    ## Network
    $InterfaceName = $ResourceGroupName + "ServerInterface"
    $VNetName = $ResourceGroupName + "VNet"
    $SubnetName = "Default"
    $VNetAddressPrefix = "10.0.0.0/16"
    $VNetSubnetAddressPrefix = "10.0.0.0/24"
    $TCPIPAllocationMethod = "Dynamic"
    $DomainName = "sqlvm1"

    ##Compute
    $VMName = $ResourceGroupName + "VM"
    $ComputerName = $ResourceGroupName + "Server"
    $VMSize = "Standard_DS13"
    $OSDiskName = $VMName + "OSDisk"

    ##Image
    $PublisherName = "MicrosoftSQLServer"
    $OfferName = "SQL2016RC3-WS2012R2"
    $Sku = "Evaluation"
    $Version = "latest"

    # Resource Group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

    # Storage
    $StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -Type $StorageType -Location $Location

    # Network
    $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
    $VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
    $PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
    $Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

    # Compute
    $VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    $Credential = Get-Credential -Message "Type the name and password of the local administrator account."
    $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
    $VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
    $OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
    $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

    # Image
    $VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

    ## Create the VM in Azure
    New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

## Próximas etapas
Depois que a máquina virtual for criada, você estará pronto para se conectar à máquina virtual usando a conectividade de instalação e RDP. Para obter mais informações, consulte [Conectar-se a uma Máquina Virtual do SQL Server (Gerenciador de Recursos)](virtual-machines-windows-sql-connect.md).

<!---HONumber=AcomDC_0427_2016-->