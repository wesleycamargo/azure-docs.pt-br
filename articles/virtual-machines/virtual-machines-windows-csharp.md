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
	ms.date="04/18/2016"
	ms.author="davidmu"/>

# Implantar recursos do Azure usando C# 

Este artigo mostra como configurar a autenticação e o armazenamento usando o Azure PowerShell e, em seguida, criar recursos do Azure usando C#.

Para concluir este tutorial, você também precisa do seguinte:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- [Um token de autenticação](../resource-group-authenticate-service-principal.md)

São necessários cerca de 30 minutos para a conclusão destas etapas.

## Etapa 1: instalar o PowerShell do Azure

Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura que você deseja usar e entrar na sua conta do Azure.

## Etapa 2: criar um projeto do Visual Studio e instalar as bibliotecas

Os pacotes NuGet são a maneira mais fácil de instalar as bibliotecas de que você precisa para concluir este tutorial. Você deve instalar a Biblioteca de Gerenciamento de Recursos do Azure, a Biblioteca de Autenticação do Active Directory do Azure e a Biblioteca do Provedor de Recursos do Computador. Para obter essas bibliotecas no Visual Studio, faça o seguinte:

1. Clique em **Arquivo** > **Novo** > **Projeto**.

2. Em **Modelos** > **Visual C#**, selecione **Aplicativo de Console**, digite o nome e o local do projeto e, em seguida, clique em **OK**.

3. Clique com o botão direito do mouse no nome no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.

4. Digite *Active Directory* na caixa de pesquisa, clique em **Instalar** para o pacote da Biblioteca de Autenticação do Active Directory e siga as instruções para instalar o pacote.

5. Na parte superior da página, selecione **Incluir Pré-lançamento**. Digite *Microsoft.Azure.Management.Compute* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas do .NET de Computação e siga as instruções para instalar o pacote.

6. Digite *Microsoft.Azure.Management.Network* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas do .NET de Rede e siga as instruções para instalar o pacote.

7. Digite *Microsoft.Azure.Management.Storage* na caixa de pesquisa, clique em **Instalar** para as bibliotecas de armazenamento do .NET e siga as instruções para instalar o pacote.

8. Digite *Microsoft.Azure.ResourceManager* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas de Gerenciamento de Recursos.

Agora você está pronto para começar a usar as bibliotecas para criar seu aplicativo.

## Etapa 3: criar as credenciais que são usadas para autenticar solicitações

O aplicativo do Azure Active Directory foi criado e a biblioteca de autenticação está instalada, agora você pode formatar as informações do aplicativo em credenciais que são usadas para autenticar solicitações para o Azure Resource Manager. Faça o seguinte:

1. Abra o arquivo Program.cs para o projeto que você criou e adicione o seguinte usando instruções na parte superior do arquivo:

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

2. Adicione este método à classe Programa para obter o token necessário para criar as credenciais:

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

	Substitua {id-aplicativo} pelo identificador do aplicativo que você registrou anteriormente, {senha} pela senha que você escolheu para o aplicativo AD e {id-locatário} pelo identificador do locatário para sua assinatura. Você pode encontrar a ID do locatário executando Get-AzureRmSubscription.

3. Adicione este código ao método Main no arquivo Program.cs para criar as credenciais:

        var token = GetAuthorizationHeader();
        var credential = new TokenCredentials(token);

4. Salve o arquivo Program.cs.

## Etapa 4: adicionar o código para registrar os provedores e criar os recursos

### Registrar os provedores e criar um grupo de recursos

Todos os recursos devem estar contidos em um grupo de recursos. Antes que você possa adicionar recursos a um grupo, sua assinatura deve ser registrada com os provedores de recursos.

1. Adicione variáveis ao método Principal da classe Programa para especificar os nomes que você deseja usar para os recursos, o local dos recursos, como "EUA Central", informações da conta de administrador e o identificador da assinatura:

        var groupName = "resource group name";
        var ipName = "public ip name";
        var avSetName = "availability set name";
        var nicName = "network interface name";
        var storageName = "storage account name";
        var vmName = "virtual machine name";  
        var vnetName = "virtual network name";
        var subnetName = "subnet name";
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        var location = "location name";
        var subscriptionId = "subsciption id";

    Substitua todos os valores de variáveis pelos nomes e identificador que você deseja usar. Você pode encontrar o identificador da assinatura executando Get-AzureRmSubscription.

2. Adicione este método à classe Program para criar o grupo de recursos:

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

3. Adicione este código ao método Main para chamar o método que você acabou de adicionar:

        CreateResourceGroup(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

### Criar uma conta de armazenamento

Uma conta de armazenamento é necessária para armazenar o arquivo de disco rígido virtual criado para a máquina virtual.

1. Adicione este método à classe Program para criar a conta de armazenamento:

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

2. Adicione este código ao método Main da classe Program para chamar o método que você acabou de adicionar:

        CreateStorageAccount(
          credential,
          storageName,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

### Criar um endereço IP público

Um endereço IP público é necessário para se comunicar com a máquina virtual.

1. Adicione este método à classe Programa para criar o endereço IP público da máquina virtual:

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

2. Adicione este código ao método Main da classe Program para chamar o método que você acabou de adicionar:

        CreatePublicIPAddress(
          credential,
          ipName,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

### Criar a rede virtual

Uma máquina virtual criada com o modelo de implantação do Gerenciador de Recursos devem ser incluídas em uma rede virtual.

1. Adicione este método à classe Program para criar uma sub-rede e uma rede virtual:

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

2. Adicione este código ao método Main da classe Program para chamar o método que você acabou de adicionar:

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

### Criar um conjunto de disponibilidade

Conjuntos de disponibilidade facilitam o gerenciamento e a manutenção das máquinas virtuais usadas por seu aplicativo.

1. Adicione este método à classe Program para criar o conjunto de disponibilidade:

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

2. Adicione este código ao método Main da classe Program para chamar o método que você acabou de adicionar:

        CreateAvailabilitySet(
          credential,
          avSetName,
          groupName,
          subscriptionId,
          location);
        Console.ReadLine();

### Criar uma máquina virtual

Agora que você criou todos os recursos de suporte, você pode criar uma máquina virtual.

1. Adicione este método à classe Program para criar a máquina virtual:

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

	>[AZURE.NOTE] Este tutorial cria uma máquina virtual executando uma versão do sistema operacional do Windows Server. Para saber mais sobre a seleção de outras imagens, consulte [Navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](virtual-machines-linux-cli-ps-findimage.md).

2. Adicione este código ao método Main para chamar o método que você acabou de adicionar:

        CreateVirtualMachine(
          credential,
          vmName,
          groupName,
          nicName,
          avSetName,
          storageName,
          adminName,
          adminPassword,
          subscriptionId,
          location);
        Console.ReadLine();

##Etapa 5: adicionar código para excluir os recursos

Como você é cobrado pelos recursos usados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Se você quiser excluir as máquinas virtuais e todos os recursos de suporte, tudo o que precisa fazer é excluir o grupo de recursos.

1. Adicione este método à classe Programa para excluir o grupo de recursos:

        public static void DeleteResourceGroup(
          TokenCredentials credential,
          string groupName)
        {
            Console.WriteLine("Deleting resource group...");
            var resourceGroupClient = new ResourceManagementClient(credential);
            resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
        }

2. Adicione este código ao método Main para chamar o método que você acabou de adicionar:

        DeleteResourceGroup(
          credential,
          groupName);
        Console.ReadLine();

## Etapa 6: executar o aplicativo de console

1. Para executar o aplicativo de console, clique em **Iniciar** no Visual Studio e, em seguida, entre no AD do Azure usando o mesmo nome de usuário e senha que você usa com sua assinatura.

2. Pressione **Enter** após cada código de status ser retornado para criar cada recurso. Depois que a máquina virtual for criada, execute a próxima etapa antes de pressionar Enter para excluir todos os recursos.

	Devem ser necessários cerca de cinco minutos para o aplicativo de console executar completamente do início ao fim. Antes de pressionar Enter para iniciar a exclusão de recursos, você pode levar alguns minutos para verificar a criação de recursos no portal do Azure antes de excluí-los.

3. Procure os Logs de Auditoria no portal do Azure para ver o status dos recursos:

	![Procurar logs de auditoria no Portal do Azure](./media/virtual-machines-windows-csharp/crpportal.png)
    
## Próximas etapas

Aproveite o uso de um modelo para criar uma máquina virtual usando as informações em [Implantar uma Máquina Virtual do Azure usando C# e um modelo do Resource Manager](virtual-machines-windows-csharp-template.md)

<!---HONumber=AcomDC_0420_2016-->