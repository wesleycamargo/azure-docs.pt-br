<properties
	pageTitle="Implantar dos recursos usando bibliotecas .NET | Microsoft Azure"
	description="Saiba como usar as bibliotecas de Computação, Armazenamento e Rede do .NET para criar e excluir recursos no Microsoft Azure usando o Gerenciador de Recursos."
	services="virtual-machines,virtual-network,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="davidmu"/>

# Implantar recursos do Azure usando bibliotecas de computação, rede e armazenamento do .NET

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

Este tutorial mostra como usar as bibliotecas de computação, armazenamento e rede do .NET para criar e excluir recursos no Microsoft Azure. Ele também mostra como autenticar as solicitações para o Gerenciador de Recursos do Azure usando o Active Directory do Azure.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Para concluir este tutorial, você também precisa do seguinte:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Conta de armazenamento do Azure](../storage/storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

São necessários cerca de 30 minutos para a conclusão destas etapas.

## Etapa 1: adicionar um aplicativo ao AD do Azure e definir permissões

Para usar o AD do Azure para autenticar solicitações ao Gerenciador de Recursos do Azure, um aplicativo deve ser adicionado ao diretório padrão. Para obter mais informações, consulte [Autenticar uma entidade de serviço com o Gerenciador de Recursos do Azure](../resource-group-authenticate-service-principal.md)

1. Abra um prompt do Azure PowerShell, execute este comando e digite as credenciais de sua assinatura quando solicitado:

			Login-AzureRmAccount

2. Substitua {senha} no comando a seguir pelo que você deseja usar e, em seguida, execute-o para criar o aplicativo:

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE] Anote o identificador do aplicativo retornado depois que o aplicativo é criado, pois você precisará dele para a próxima etapa. Você também pode encontrar o identificador do aplicativo no campo de ID de cliente do aplicativo na seção Active Directory do portal do Azure.

3. Substitua {id-aplicativo} pelo identificador que acabou de registrar e, em seguida, crie a entidade de serviço para o aplicativo:

        New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Defina a permissão para usar o aplicativo:

	    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Etapa 2: criar um projeto do Visual Studio e instalar as bibliotecas

Os pacotes NuGet são a maneira mais fácil de instalar as bibliotecas de que você precisa para concluir este tutorial. Você deve instalar a Biblioteca de Gerenciamento de Recursos do Azure, a Biblioteca de Autenticação do Active Directory do Azure e a Biblioteca do Provedor de Recursos do Computador. Para obter essas bibliotecas no Visual Studio, faça o seguinte:

1. Clique em **Arquivo** > **Novo** > **Projeto**.

2. Em **Modelos** > **Visual C#**, selecione **Aplicativo de Console**, digite o nome e o local do projeto e, em seguida, clique em **OK**.

3. Clique com o botão direito do mouse no nome no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.

4. Digite *Active Directory* na caixa de pesquisa, clique em **Instalar** para o pacote da Biblioteca de Autenticação do Active Directory e siga as instruções para instalar o pacote.

5. Na parte superior da página, selecione **Incluir Pré-lançamento**. Digite *Microsoft.Azure.Management.Compute* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas do .NET de Computação e siga as instruções para instalar o pacote.

6. Digite *Microsoft.Azure.Management.Network* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas do .NET de Rede e siga as instruções para instalar o pacote.

7. Digite *Microsoft.Azure.Management.Storage* na caixa de pesquisa, clique em **Instalar** para as bibliotecas de armazenamento do .NET e siga as instruções para instalar o pacote.

8. Digite *Microsoft.Azure.Management.Resources* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas de Gerenciamento de Recursos.

Agora você está pronto para começar a usar as bibliotecas para criar seu aplicativo.

## Etapa 3: criar as credenciais que são usadas para autenticar solicitações

Agora que o aplicativo Active Directory do Azure foi criado e a biblioteca de autenticação está instalada, você pode formatar as informações do aplicativo em credenciais que são usadas para autenticar solicitações para o Gerenciador de Recursos do Azure.

1. Abra o arquivo Program.cs para o projeto que você criou e, em seguida, adicione o seguinte usando instruções na parte superior do arquivo:

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Azure.Management.Storage;
	using Microsoft.Azure.Management.Storage.Models;
	using Microsoft.Azure.Management.Network;
	using Microsoft.Azure.Management.Network.Models;
	using Microsoft.Azure.Management.Compute;
	using Microsoft.Azure.Management.Compute.Models;
	using Microsoft.Rest;
	```

2. Adicione este método à classe Program para obter o token necessário para criar as credenciais:

	```
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);

		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	Substitua {id-aplicativo} pelo identificador do aplicativo que você registrou anteriormente, {senha} pela senha que você escolheu para o aplicativo AD e {id-locatário} pelo identificador do locatário para sua assinatura. Você pode encontrar a id do locatário executando Get-AzureSubscription.

3. Adicione este código ao método Main no arquivo Program.cs para criar as credenciais:

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

5. Salve o arquivo Program.cs.

## Etapa 4: adicionar o código para registrar os provedores e criar os recursos

### Registrar os provedores e criar um grupo de recursos

Todos os recursos devem estar contidos em um grupo de recursos. Antes que você possa adicionar recursos a um grupo, sua assinatura deve ser registrada com os provedores de recursos.

1. Adicione variáveis ao método Main da classe Program para especificar os nomes que você deseja usar para os recursos, o local dos recursos, como "Oeste dos EUA", informações da conta de administrador e o identificador da assinatura:

	```
	var groupName = "{resource-group-name}";
	var ipName = "{public-ip-name}";
	var avSetName = "{availability-set-name}";
	var nicName = "{network-interface-name}";
	var storageName = "{storage-account-name}";
	var vmName = "{virtual-machine-name};  
	var vnetName = "{vnet-name}";
	var subnetName = "{subnet-name}";
	var adminName = "{administrator-account-name}";
	var adminPassword = "{administrator-account-password};
	var location = "{location}";
	var subscriptionId = "{subsciption-id}";
	```

   Substitua todos os espaços reservados entre colchetes pelos nomes que você deseja usar. Você pode encontrar o identificador da assinatura executando Get-AzureSubscription.

2. Adicione este método à classe Program para criar o grupo de recursos:

	```
	public static void CreateResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var resourceGroup = new ResourceGroup {
			Location = location
		};
		var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
		Console.WriteLine(rgResult.Properties.ProvisioningState);

		var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
		Console.WriteLine(rpResult.RegistrationState);
	}
	```

3. Adicione este código ao método Main para chamar o método que você acabou de adicionar:

	```
	CreateResourceGroup(
		credential,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Criar uma conta de armazenamento

Uma conta de armazenamento é necessária para armazenar o arquivo de disco rígido virtual criado para a máquina virtual.

1. Adicione este método à classe Program para criar a conta de armazenamento:

	```
	public static void CreateStorageAccount(
		TokenCredentials credential,         
		string storageName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the storage account...");
		var storageManagementClient = new StorageManagementClient(credential);
		storageManagementClient.SubscriptionId = subscriptionId;
		var saResult = storageManagementClient.StorageAccounts.Create(
			groupName,
			storageName,
			new StorageAccountCreateParameters()
			{
				AccountType = AccountType.StandardLRS,
				Location = location
			}
		);
		Console.WriteLine(saResult.ProvisioningState);
	}
	```

2. Adicione este código ao método Main da classe Program para chamar o método que você acabou de adicionar:

	```
	CreateStorageAccount(
		credential,
		storageName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Criar um endereço IP público

Um endereço IP público é necessário para se comunicar com a máquina virtual.

1. Adicione este método à classe Program para criar o endereço IP público:

	```
	public static void CreatePublicIPAddress(
		TokenCredentials credential,
		string ipName,  
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the public ip...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var ipResult = networkManagementClient.PublicIPAddresses.CreateOrUpdate(
			groupName,
			ipName,
			new PublicIPAddress
			{
				Location = location,
				PublicIPAllocationMethod = "Dynamic"
			}
		);
		Console.WriteLine(ipResult.ProvisioningState);
	}
	```

2. Adicione este código ao método Main da classe Program para chamar o método que você acabou de adicionar:

	```
	CreatePublicIPAddress(
		credential,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Criar a rede virtual

Uma máquina virtual criada com o modelo de implantação do Gerenciador de Recursos devem ser incluídas em uma rede virtual.

1. Adicione este método à classe Program para criar uma sub-rede e uma rede virtual:

	```
	public static void CreateNetwork(
		TokenCredentials credential,
		string vnetName,
		string subnetName,
		string nicName,
		string ipName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the virtual network...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;

		var subnet = new Subnet
		{
			Name = subnetName,
			AddressPrefix = "10.0.0.0/24"
		};

		var address = new AddressSpace {
			AddressPrefixes = new List<string> { "10.0.0.0/16" }
		};

		var vnResult = networkManagementClient.VirtualNetworks.CreateOrUpdate(
			groupName,
			vnetName,
			new VirtualNetwork
			{
				Location = location,
				AddressSpace = address,
				Subnets = new List<Subnet> { subnet }
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);

		var subnetResponse = networkManagementClient.Subnets.Get(
			groupName,
			vnetName,
			subnetName
		);

		var pubipResponse = networkManagementClient.PublicIPAddresses.Get(groupName, ipName);

		Console.WriteLine("Updating the network with the nic...");
		var nicResult = networkManagementClient.NetworkInterfaces.CreateOrUpdate(
			groupName,
			nicName,
			new NetworkInterface
			{
				Location = location,
				IpConfigurations = new List<NetworkInterfaceIPConfiguration>
				{
					new NetworkInterfaceIPConfiguration
					{
						Name = "nicConfig1",
						PublicIPAddress = pubipResponse,
						Subnet = subnetResponse
					}
				}
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);
	}
	```

2. Adicione este código ao método Main da classe Program para chamar o método que você acabou de adicionar:

	```
	CreateNetwork(
		credential,
		vnetName,
		subnetName,
		nicName,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Criar um conjunto de disponibilidade

Conjuntos de disponibilidade facilitam o gerenciamento e a manutenção das máquinas virtuais usadas por seu aplicativo.

1. Adicione este método à classe Program para criar o conjunto de disponibilidade:

	```
	public static void CreateAvailabilitySet(
		TokenCredentials credential,
		string avsetName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the availability set...");
		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avResult = computeManagementClient.AvailabilitySets.CreateOrUpdate(
			groupName,
			avsetName,
			new AvailabilitySet()
			{
				Location = location
			}
		);
	}
	```

2. Adicione este código ao método Main da classe Program para chamar o método que você acabou de adicionar:

	```
	CreateAvailabilitySet(
		credential,
		avsetName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Criar uma máquina virtual

Agora que você criou todos os recursos de suporte, você pode criar uma máquina virtual.

1. Adicione este método à classe Program para criar a máquina virtual:

	```
	public static void CreateVirtualMachine(
		TokenCredentials credential,
		string vmName,
		string groupName,
		string nicName,
		string avsetName,
		string storageName,
		string adminName,
		string adminPassword,
		string subscriptionId,
		string location)
	{
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

		Console.WriteLine("Creating the virtual machine...");
		var vm = computeManagementClient.VirtualMachines.CreateOrUpdate(
			groupName,
			vmName,
			new VirtualMachine
			{
				Location = location,
				AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
				{
					Id = avSet.Id
				},
				HardwareProfile = new HardwareProfile
				{
					VmSize = "Standard_A0"
				},
				OsProfile = new OSProfile
				{
					AdminUsername = adminName,
					AdminPassword = adminPassword,
					ComputerName = vmName,
					WindowsConfiguration = new WindowsConfiguration
					{
						ProvisionVMAgent = true
					}
				},
				NetworkProfile = new NetworkProfile
				{
					NetworkInterfaces = new List<NetworkInterfaceReference>
					{
						new NetworkInterfaceReference { Id = nic.Id }
					}
				},
				StorageProfile = new StorageProfile
				{
					ImageReference = new ImageReference
					{
						Publisher = "MicrosoftWindowsServer",
						Offer = "WindowsServer",
						Sku = "2012-R2-Datacenter",
						Version = "latest"
					},
					OsDisk = new OSDisk
					{
						Name = "mytestod1",
						CreateOption = "FromImage",
						Vhd = new VirtualHardDisk
						{
							Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
						}
					}
				}
			}
		);
		Console.WriteLine(vm.ProvisioningState);
	}
	```

	>[AZURE.NOTE] Este tutorial cria uma máquina virtual executando uma versão do sistema operacional do Windows Server. Para saber mais sobre a seleção de outras imagens, confira [Navegar e selecionar imagens da máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](resource-groups-vm-searching.md).

2. Adicione o seguinte código ao método Main para chamar o método que você acabou de adicionar:

	```
	CreateVirtualMachine(
		credential,
		vmName,
		groupName,
		nicName,
		avsetName,
		storageName,
		adminName,
		adminPassword,
		subscriptionId,
		location);
	Console.ReadLine();
	```

##Etapa 5: adicionar código para excluir os recursos

Como você é cobrado pelos recursos usados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Se você quiser excluir as máquinas virtuais e todos os recursos de suporte, tudo o que precisa fazer é excluir o grupo de recursos.

1. Adicione o seguinte método à classe Program para excluir o grupo de recursos:

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2. Adicione o seguinte código ao método Main para chamar o método que você acabou de adicionar:

	```
	DeleteResourceGroup(
		credential,
		groupName,
		subscriptionId);
	Console.ReadLine();
	```

## Etapa 6: executar o aplicativo de console

1. Para executar o aplicativo de console, clique em **Iniciar** no Visual Studio e, em seguida, entre no AD do Azure usando o mesmo nome de usuário e senha que você usa com sua assinatura.

2. Pressione **Enter** após cada código de status ser retornado para criar cada recurso. Depois que a máquina virtual for criada, execute a próxima etapa antes de pressionar Enter para excluir todos os recursos.

	Devem ser necessários cerca de cinco minutos para o aplicativo de console executar completamente do início ao fim. Antes de pressionar Enter para iniciar a exclusão de recursos, você pode levar alguns minutos para verificar a criação de recursos no portal do Azure antes de excluí-los.

3. Procure os Logs de Auditoria no portal do Azure para ver o status dos recursos:

	![Criar um aplicativo do AD](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=AcomDC_0128_2016-->