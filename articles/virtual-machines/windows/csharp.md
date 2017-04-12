---
title: "Implantar uma Máquina Virtual do Azure usando o C# | Microsoft Docs"
description: "Use o C# e o Azure Resource Manager para implantar uma máquina virtual e todos os seus recursos de suporte."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 87524373-5f52-4f4b-94af-50bf7b65c277
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f4c63af2d873fb11c8503a30b104b9b7db7f74f0
ms.lasthandoff: 04/03/2017


---
# <a name="deploy-an-azure-virtual-machine-using-c"></a>Implantar uma Máquina Virtual do Azure usando o C# #

Este artigo mostra como usar o C# para criar uma máquina virtual do Azure e seus recursos de suporte.

São necessários cerca de 20 minutos para a conclusão destas etapas.

## <a name="step-1-create-a-visual-studio-project"></a>Etapa 1: Criar um projeto do Visual Studio

Nesta etapa, você verifica se o Visual Studio está instalado e cria um aplicativo de console usado para criar os recursos.

1. Se você ainda não fez isso, instale o [Visual Studio](https://www.visualstudio.com/).
2. No Visual Studio, clique em **Arquivo** > **Novo** > **Projeto**.
3. Em **Modelos** > **Visual C#**, selecione **Aplicativo de Console**, digite o nome e o local do projeto e, em seguida, clique em **OK**.

## <a name="step-2-install-libraries"></a>Etapa 2: Instalar bibliotecas

Os pacotes NuGet são a maneira mais fácil de instalar as bibliotecas de que você precisa para concluir estas etapas. Para obter as bibliotecas que você precisa no Visual Studio, siga estas etapas:


1. Clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções, clique em **Gerenciar Pacotes NuGet** e, em seguida, em **Procurar**.
2. Digite *Microsoft.IdentityModel.Clients.ActiveDirectory* na caixa de pesquisa, clique em **Instalar** e siga as instruções para instalar o pacote.
3. Na parte superior da página, selecione **Incluir Pré-lançamento**. Digite *Microsoft.Azure.Management.Compute* na caixa de pesquisa, clique em **Instalar** e siga as instruções para instalar o pacote.
4. Digite *Microsoft.Azure.Management.Network* na caixa de pesquisa, clique em **Instalar** e siga as instruções para instalar o pacote.
5. Digite *Microsoft.Azure.Management.Storage* na caixa de pesquisa, clique em **Instalar** e siga as instruções para instalar o pacote.
6. Digite *Microsoft.Azure.Management.ResourceManager* na caixa de pesquisa, clique em **Instalar** e siga as instruções para instalar o pacote.

Agora você está pronto para começar a usar as bibliotecas para criar seu aplicativo.

## <a name="step-3-create-the-credentials-used-to-authenticate-requests"></a>Etapa 3: Criar as credenciais usadas para autenticar as solicitações

Antes de começar essa etapa, verifique se você tem acesso a uma [entidade de serviço do Active Directory](../../resource-group-authenticate-service-principal.md). Na entidade de serviço, você adquire um token para autenticar as solicitações para o Azure Resource Manager.

1. Abra o arquivo Program.cs para o projeto que você criou e, em seguida, adicione o seguinte usando instruções na parte superior do arquivo:
   
    ```
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
    ```

2. Para obter o token necessário para criar as credenciais, adicione este método à classe Program:
   
    ```    
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("{client-id}", "{client-secret}");
      var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token");
      }
      return token;
    }
    ```

    Substitua esses valores:
    
    - *{client-id}* com o identificador do aplicativo do Azure Active Directory. É possível encontrar esse identificador na folha Propriedades do aplicativo do AD. Para encontrar o aplicativo do AD no portal do Azure, clique em **Azure Active Directory** no menu de recursos e, em seguida, em **Registros do aplicativo**.
    - *{client-secret}* com a tecla de acesso do aplicativo do AD. É possível encontrar esse identificador na folha Propriedades do aplicativo do AD.
    - *{tenant-id}* com o identificador de locatário da assinatura. É possível encontrar o identificador de locatário na folha Propriedades do Azure Active Directory no portal do Azure. Ele é rotulado *ID de Diretório*.

3. Para chamar o método que você adicionou anteriormente, adicione este código ao método Main no arquivo Program.cs:
   
    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. Salve o arquivo Program.cs.

## <a name="step-3-create-the-resources"></a>Etapa 3: Criar os recursos

### <a name="register-the-providers-and-create-a-resource-group"></a>Registrar os provedores e criar um grupo de recursos

Todos os recursos devem estar contidos em um grupo de recursos. Antes que você possa adicionar recursos a um grupo, sua assinatura deve ser registrada com os provedores de recursos.

1. Adicione variáveis ao método Main da classe Programa para especificar os nomes que você deseja usar para os recursos:

    ```   
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var storageName = "myStorageAccount";
    var ipName = "myPublicIP";
    var subnetName = "mySubnet";
    var vnetName = "myVnet";
    var nicName = "myNIC";
    var avSetName = "myAVSet";
    var vmName = "myVM";  
    var location = "location";
    var adminName = "adminName";
    var adminPassword = "adminPassword";
    ```

    Substitua esses valores:

    - Todos os nomes de recursos que começam com *my*, com nomes que fazem sentido para o ambiente.
    - *subscriptionId* com o identificador de assinatura. É possível encontrar o identificador de assinatura na folha Assinaturas do portal do Azure.
    - *location* com a [região do Azure](https://azure.microsoft.com/regions/) na qual você deseja criar os recursos.
    - *adminName* com o nome da conta de administrador na máquina virtual.
    - *adminPassword* com a senha da conta de administrador.

2. Para criar o grupo de recursos e registrar os provedores, adicione este método à classe Programa:
   
    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location)
    {
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
   
      Console.WriteLine("Registering the providers...");
      var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
      Console.WriteLine(rpResult.RegistrationState);
      rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
      Console.WriteLine(rpResult.RegistrationState);
      rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
      Console.WriteLine(rpResult.RegistrationState);
   
      Console.WriteLine("Creating the resource group...");
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. Para chamar o método que você adicionou anteriormente, adicione este código ao método Main:
   
    ```   
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Ao usar um disco não gerenciado, uma [conta de armazenamento](../../storage/storage-create-storage-account.md) é necessária para armazenar o arquivo de disco rígido virtual criado para a máquina virtual.

1. Para criar a conta de armazenamento, adicione este método à classe Programa:

    ```   
    public static async Task<StorageAccount> CreateStorageAccountAsync(
      TokenCredentials credential,       
      string groupName,
      string subscriptionId,
      string location,
      string storageName)
    {
      var storageManagementClient = new StorageManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the storage account...");

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
    ```

2. Para chamar o método que você adicionou anteriormente, adicione este código ao método Main:
   
    ```
    var stResult = CreateStorageAccountAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      storageName);
    Console.WriteLine(stResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público

Um endereço IP público é necessário para se comunicar com a máquina virtual.

1. Para criar o endereço IP público para a máquina virtual, adicione este método à classe Program:
   
    ```
    public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
      TokenCredentials credential,  
      string groupName,
      string subscriptionId,
      string location,
      string ipName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      
      Console.WriteLine("Creating the public ip...");

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
    ```

2. Para chamar o método que você adicionou anteriormente, adicione este código ao método Main:
   
    ```   
    var ipResult = CreatePublicIPAddressAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      ipName);
    Console.WriteLine(ipResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-virtual-network"></a>Criar uma rede virtual

Uma máquina virtual criada com o modelo de implantação do Gerenciador de Recursos devem ser incluídas em uma rede virtual.

1. Para criar uma sub-rede e uma rede virtual, adicione este método à classe Programa:

    ```   
    public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string vnetName,
      string subnetName)
    {
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
   
      var subnet = new Subnet
        {
          Name = subnetName,
          AddressPrefix = "10.0.0.0/24"
        };
   
      var address = new AddressSpace 
        {
          AddressPrefixes = new List<string> { "10.0.0.0/16" }
        };
         
      Console.WriteLine("Creating the virtual network...");
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
    ```

2. Para chamar o método que você adicionou anteriormente, adicione este código ao método Main:

    ```   
    var vnResult = CreateVirtualNetworkAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      vnetName,
      subnetName);
    Console.WriteLine(vnResult.Result.ProvisioningState);  
    Console.ReadLine();
    ```

### <a name="create-a-network-interface"></a>Criar um adaptador de rede

Uma máquina virtual precisa de uma interface de rede para se comunicar na rede virtual.

1. Para criar um adaptador de rede adicione este método à classe Programa:

    ```   
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
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var subnet = await networkManagementClient.Subnets.GetAsync(
        groupName,
        vnetName,
        subnetName
      );
      var publicIP = await networkManagementClient.PublicIPAddresses.GetAsync(
        groupName, 
        ipName
      );
   
      Console.WriteLine("Creating the network interface...");
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
    ```

2. Para chamar o método que você adicionou anteriormente, adicione este código ao método Main:
   
    ```
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
    ```

### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Conjuntos de disponibilidade facilitam o gerenciamento e a manutenção das máquinas virtuais usadas por seu aplicativo.

1. Para criar o conjunto de disponibilidade, Adicione este método à classe Programa:

    ```   
    public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location,
      string avsetName)
    {
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };
        
      Console.WriteLine("Creating the availability set...");
      return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
        groupName,
        avsetName,
        new AvailabilitySet()
          { Location = location }
      );
    }
    ```

2. Para chamar o método que você adicionou anteriormente, adicione este código ao método Main:
   
    ```
    var avResult = CreateAvailabilitySetAsync(
      credential,  
      groupName,
      subscriptionId,
      location,
      avSetName);
    Console.ReadLine();
    ```

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Agora que você criou todos os recursos de suporte, você pode criar uma máquina virtual.

1. Para criar a máquina virtual, adicione este método à classe Programa:

    ```   
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
      var networkManagementClient = new NetworkManagementClient(credential)
        { SubscriptionId = subscriptionId };
      var computeManagementClient = new ComputeManagementClient(credential)
        { SubscriptionId = subscriptionId };  
      var nic = await networkManagementClient.NetworkInterfaces.GetAsync(
        groupName, 
        nicName);
      var avSet = await computeManagementClient.AvailabilitySets.GetAsync(
        groupName, 
        avsetName);
   
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
    }
    ```

    > [!NOTE]
    > Este tutorial cria uma máquina virtual executando uma versão do sistema operacional do Windows Server. Para saber mais sobre a seleção de outras imagens, veja [Navegar e selecionar imagens de máquina virtual do Azure com o Windows PowerShell e a CLI do Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    >

2. Para chamar o método que você adicionou anteriormente, adicione este código ao método Main:
   
    ```
    var vmResult = CreateVirtualMachineAsync(
      credential,
      groupName,
      subscriptionId,
      location,
      nicName,
      avSetName,
      storageName,
      adminName,
      adminPassword,
      vmName);
    Console.WriteLine(vmResult.Result.ProvisioningState);
    Console.ReadLine();
    ```

## <a name="step-4-delete-the-resources"></a>Etapa 4: Excluir os recursos

Como você é cobrado pelos recursos utilizados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Se você quiser excluir as máquinas virtuais e todos os recursos de suporte, tudo o que precisa fazer é excluir o grupo de recursos.

1. Para excluir o grupo de recursos, adicione este método à classe Programa:
   
    ```
    public static async void DeleteResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId)
    {
      Console.WriteLine("Deleting resource group...");
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
      await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
    }
    ```

2. Para chamar o método que você adicionou anteriormente, adicione este código ao método Main:
   
    ```   
    DeleteResourceGroupAsync(
      credential,
      groupName,
      subscriptionId);
    Console.ReadLine();
    ```

## <a name="step-5-run-the-console-application"></a>Etapa 5: Executar o aplicativo de console

1. Para executar o aplicativo de console, clique em **Iniciar** no Visual Studio e, em seguida, entre no AD do Azure usando o mesmo nome de usuário e senha que você usa com sua assinatura.

2. Pressione **Enter** depois que o status *Êxito* for exibido. 
   
3. Depois que a máquina virtual for criada e antes de pressionar **Enter** para iniciar a exclusão de recursos, reserve alguns minutos para inspecionar os recursos no portal do Azure.

## <a name="next-steps"></a>Próximas etapas
* Aproveite o uso de um modelo para criar uma máquina virtual usando as informações em [Implantar uma Máquina Virtual do Azure usando C# e um modelo do Resource Manager](csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Saiba como gerenciar a máquina virtual que você criou examinando [Gerenciar Máquinas Virtuais do Azure usando o Azure Resource Manager e o C#](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


