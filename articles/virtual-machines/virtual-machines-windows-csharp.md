<properties
	pageTitle="Implantar recursos do Azure usando C# | Microsoft Azure"
	description="Aprenda a usar o C# e o Azure Resource Manager para criar recursos do Microsoft Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
	ms.author="davidmu"/>

# Implantar recursos do Azure usando C# 

Este artigo mostra como criar recursos do Azure usando C#.

Primeiro, você precisa realizar as seguintes tarefas:

- Instalar o [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Verificar a instalação do [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Obter um [token de autenticação](../resource-group-authenticate-service-principal.md)

São necessários cerca de 30 minutos para a conclusão destas etapas.

## Etapa 1: criar um projeto do Visual Studio e instalar as bibliotecas

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

## Etapa 2: Criar as credenciais que são usadas para autenticar solicitações

O aplicativo do Azure Active Directory foi criado e a biblioteca de autenticação está instalada, agora você pode formatar as informações do aplicativo em credenciais que são usadas para autenticar solicitações para o Azure Resource Manager. Faça o seguinte:

1. Abra o arquivo Program.cs para o projeto que você criou e adicione o seguinte usando instruções na parte superior do arquivo:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;

2. Adicione este método à classe Programa para obter o token necessário para criar as credenciais:

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }

	Substitua {client-id} pelo identificador do aplicativo do Azure Active Directory, {client-secret} pela chave de acesso do aplicativo do AD e {tenant-id} pelo identificador do locatário da sua assinatura. Você pode encontrar a ID do locatário executando Get-AzureRmSubscription. Você pode encontrar a chave de acesso usando o portal do Azure.

3. Adicione este código ao método Main no arquivo Program.cs para criar as credenciais:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Salve o arquivo Program.cs.

## Etapa 3: adicionar o código para registrar os provedores e criar os recursos

### Registrar os provedores e criar um grupo de recursos

Todos os recursos devem estar contidos em um grupo de recursos. Antes que você possa adicionar recursos a um grupo, sua assinatura deve ser registrada com os provedores de recursos.

1. Adicione variáveis ao método Principal da classe Programa para especificar os nomes que você deseja usar para os recursos, o local dos recursos, como "EUA Central", informações da conta de administrador e o identificador da assinatura:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        
    Substitua todos os valores de variáveis pelos nomes e identificador que você deseja usar. Você pode encontrar o identificador da assinatura executando Get-AzureRmSubscription.

2. Adicione este método à classe Program para criar o grupo de recursos e registrar os provedores:

        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
          
          Console.WriteLine("Creating the resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }

3. Adicione este código ao método Main para chamar o método que você acabou de adicionar:

        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### Criar uma conta de armazenamento

Uma [conta de armazenamento](../storage/storage-create-storage-account.md) é necessária para armazenar o arquivo de disco rígido virtual criado para a máquina virtual.

1. Adicione este método à classe Program para criar a conta de armazenamento:

        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential);
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }

2. Adicione este código ao método Main da classe Program para chamar o método que você acabou de adicionar:

        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### Criar um endereço IP público

Um endereço IP público é necessário para se comunicar com a máquina virtual.

1. Adicione este método à classe Programa para criar o endereço IP público da máquina virtual:

        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }

2. Adicione este código ao método Main da classe Program para chamar o método que você acabou de adicionar:

        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### Criar a rede virtual

Uma máquina virtual criada com o modelo de implantação do Gerenciador de Recursos devem ser incluídas em uma rede virtual.

1. Adicione este método à classe Program para criar uma sub-rede e uma rede virtual:

        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
        
2. Adicione este código ao método Main da classe Program para chamar o método que você acabou de adicionar:

        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();
        
### Criar a interface de rede

Uma máquina virtual precisa de um adaptador de rede para se comunicar na rede virtual que você acabou de criar.

1. Adicione este método à classe Program para criar um adaptador de rede:

        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);

          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }

2. Adicione este código ao método Main da classe Program para chamar o método que você acabou de adicionar:

        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### Criar um conjunto de disponibilidade

Conjuntos de disponibilidade facilitam o gerenciamento e a manutenção das máquinas virtuais usadas por seu aplicativo.

1. Adicione este método à classe Program para criar o conjunto de disponibilidade:

        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. Adicione este código ao método Main da classe Program para chamar o método que você acabou de adicionar:

        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### Criar uma máquina virtual

Agora que você criou todos os recursos de suporte, você pode criar uma máquina virtual.

1. Adicione este método à classe Program para criar a máquina virtual:

        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential);
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
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
                  CreateOption = DiskCreateOptionTypes.FromImage,
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

	>[AZURE.NOTE] Este tutorial cria uma máquina virtual executando uma versão do sistema operacional do Windows Server. Para saber mais sobre a seleção de outras imagens, veja [Navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md).

2. Adicione este código ao método Main para chamar o método que você acabou de adicionar:

        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avsetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

##Etapa 4: Adicionar código para excluir os recursos

Como você é cobrado pelos recursos usados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Se você quiser excluir as máquinas virtuais e todos os recursos de suporte, tudo o que precisa fazer é excluir o grupo de recursos.

1.	Adicione este método à classe Programa para excluir o grupo de recursos:

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.	Adicione este código ao método Main para chamar o método que você acabou de adicionar:

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

## Etapa 5: Executar o aplicativo de console

1. Para executar o aplicativo de console, clique em **Iniciar** no Visual Studio e, em seguida, entre no AD do Azure usando o mesmo nome de usuário e senha que você usa com sua assinatura.

2. Pressione **Enter** após cada código de status ser retornado para criar cada recurso. Depois que a máquina virtual for criada, execute a próxima etapa antes de pressionar Enter para excluir todos os recursos.

	Devem ser necessários cerca de cinco minutos para o aplicativo de console executar completamente do início ao fim. Antes de pressionar Enter para iniciar a exclusão de recursos, você pode levar alguns minutos para verificar a criação de recursos no portal do Azure antes de excluí-los.

3. Procure os Logs de Auditoria no portal do Azure para ver o status dos recursos:

	![Procurar logs de auditoria no Portal do Azure](./media/virtual-machines-windows-csharp/crpportal.png)
    
## Próximas etapas

- Aproveite o uso de um modelo para criar uma máquina virtual usando as informações em [Implantar uma Máquina Virtual do Azure usando C# e um modelo do Resource Manager](virtual-machines-windows-csharp-template.md).
- Saiba como gerenciar a máquina virtual que você acabou de criar examinando [Gerenciar as máquinas virtuais usando o Azure Resource Manager e o PowerShell](virtual-machines-windows-csharp-manage.md).

<!---HONumber=AcomDC_0720_2016-->