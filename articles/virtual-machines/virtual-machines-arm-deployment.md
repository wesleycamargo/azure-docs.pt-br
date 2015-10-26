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
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# Implantar recursos do Azure usando bibliotecas de computação, rede e armazenamento do .NET

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.


Este tutorial mostra como usar alguns dos clientes disponíveis nas bibliotecas de computação, armazenamento e rede do .NET para criar e excluir recursos no Microsoft Azure. Ele também mostra como autenticar as solicitações para o Gerenciador de Recursos do Azure usando o Active Directory do Azure.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Para concluir este tutorial, você também precisa do seguinte:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Conta de armazenamento do Azure](../storage-create-storage-account.md)
- [Windows Management Framework 3.0](http://www.microsoft.com/pt-BR/download/details.aspx?id=34595) ou [Windows Management Framework 4.0](http://www.microsoft.com/pt-BR/download/details.aspx?id=40855)
- [PowerShell do Azure](../install-configure-powershell.md)

São necessários cerca de 30 minutos para a conclusão destas etapas.

## Etapa 1: adicionar um aplicativo ao AD do Azure e definir permissões

Para usar o AD do Azure para autenticar solicitações ao Gerenciador de Recursos do Azure, um aplicativo deve ser adicionado ao diretório padrão. Faça o seguinte para adicionar um aplicativo:

1. Abra um prompt do Azure PowerShell, execute este comando e digite as credenciais de sua assinatura quando solicitado:

	    Login-AzureRmAccount

2. Substitua {senha} no comando a seguir pelo que você deseja usar e, em seguida, execute-o para criar o aplicativo:

	    New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE]Anote o identificador do aplicativo retornado depois que o aplicativo é criado, pois você precisará dele para a próxima etapa. Você também pode encontrar o identificador do aplicativo no campo de ID de cliente do aplicativo na seção Active Directory do portal.

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

5. Na parte superior da página, selecione **Incluir Pré-lançamento**. Digite *Computação do Azure* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas do .NET de Computação e siga as instruções para instalar o pacote.

6. Digite *Rede do Azure* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas do .NET de Rede e siga as instruções para instalar o pacote.

7. Digite *Armazenamento do Azure* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas do .NET de Rede e siga as instruções para instalar o pacote.

8. Digite *Gerenciamento de Recursos do Azure* na caixa de pesquisa, clique em **Instalar** para as Bibliotecas de Gerenciamento de Recursos.

Agora você está pronto para começar a usar as bibliotecas para criar seu aplicativo.

## Etapa 3: criar as credenciais que são usadas para autenticar solicitações

Agora que o aplicativo Active Directory do Azure foi criado e a biblioteca de autenticação está instalada, você pode formatar as informações do aplicativo em credenciais que são usadas para autenticar solicitações para o Gerenciador de Recursos do Azure. Faça o seguinte:

1.	Abra o arquivo Program.cs para o projeto que você criou e, em seguida, adicione o seguinte usando instruções na parte superior do arquivo:

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


2. Adicione o seguinte método à classe Program para obter o token necessário para criar as credenciais:

		private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);

          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	Substitua {id-aplicativo} pelo identificador do aplicativo que você registrou anteriormente, {senha} pela senha que você escolheu para o aplicativo AD e {id-locatário} pelo identificador do locatário para sua assinatura. Você pode encontrar a id do locatário executando Get-AzureSubscription.

3.	Adicione o seguinte código ao método Main no arquivo Program.cs para criar as credenciais:

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

	Substitua {id-assinatura} pelo seu identificador de assinatura.

4.	Salve o arquivo Program.cs.

## Etapa 4: adicionar o código para registrar os provedores e criar os recursos

### Registrar os provedores e criar um grupo de recursos

Os recursos sempre são implantados em um grupo de recursos. Você usa o [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) e classes [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx) para criar o grupo de recursos em que os recursos são implantados.

1.	Adicione o seguinte método à classe Program para criar o grupo de recursos:

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");

          using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", new ResourceGroup { Location = "West US" });
            Console.WriteLine(rgResult.StatusCode);
            var rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Storage");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Network");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Compute");
            Console.WriteLine(rpResult.StatusCode);
		  }
		}

2.	Adicione o seguinte código ao método Main para chamar o método que você acabou de adicionar:

		CreateResourceGroup(credential);
		Console.ReadLine();

###Criar uma conta de armazenamento

Uma conta de armazenamento é necessária para armazenar o arquivo de disco rígido virtual criado para a máquina virtual.

1.	Adicione o seguinte método à classe Program para criar a conta de armazenamento:

		public async static void CreateStorageAccount(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the storage account...");

          using (var storageManagementClient = new StorageManagementClient(credential))
          {
            var saResult = await storageManagementClient.StorageAccounts.CreateAsync(
              "mytestrg1",
              "mytestsa1",
              new StorageAccountCreateParameters()
              { AccountType = AccountType.StandardLRS, Location = "West US" } );
            Console.WriteLine(saResult.StatusCode);
          }
        }

3.	Adicione o seguinte código ao método Main para chamar o método que você acabou de adicionar:

		CreateStorageAccount(credential);
		Console.ReadLine();

###Criar a configuração de rede

Uma máquina virtual é mais produtiva quando é adicionada a uma rede virtual.

1.	Adicione o seguinte método à classe Program para criar uma sub-rede, um endereço IP público e uma rede virtual:

		public async static void CreateNetwork(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the virtual network...");
          using (var networkClient = new NetworkResourceProviderClient(credential))
          {
            var subnet = new Subnet
            {
              Name = "mytestsn1",
              AddressPrefix = "10.0.0.0/24"
            };

            var vnResult = await networkClient.VirtualNetworks.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestvn1",
              new VirtualNetwork
              {
                Location = "West US",
                AddressSpace = new AddressSpace { AddressPrefixes = new List<string> { "10.0.0.0/16" } },
                Subnets = new List<Subnet> { subnet }
              });
            Console.WriteLine(vnResult.StatusCode);

            var subnetResponse = await networkClient.Subnets.GetAsync(
              "mytestrg1",
              "mytestvn1",
              "mytestsn1"
            );

            Console.WriteLine("Creating the public ip...");
            vnResult = await networkClient.PublicIpAddresses.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestip1",
              new PublicIpAddress
              {
                Location = "West US",
                PublicIpAllocationMethod = "Dynamic"
              });
            Console.WriteLine(vnResult.StatusCode);

            var pubipResponse = await networkClient.PublicIpAddresses.GetAsync("mytestrg1", "mytestip1");

            Console.WriteLine("Updating the network with the nic...");
            vnResult = await networkClient.NetworkInterfaces.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestnic1",
              new NetworkInterface
              {
                Name = "mytestnic1",
                Location = "West US",
                IpConfigurations = new List<NetworkInterfaceIpConfiguration>
                {
                  new NetworkInterfaceIpConfiguration
                  {
                    Name = "nicconfig1",
                    PublicIpAddress = new ResourceId
                    {
                      Id = pubipResponse.PublicIpAddress.Id
                    },
                    Subnet = new ResourceId
                    {
                      Id = subnetResponse.Subnet.Id
                    }
                  }
                }
              });
            Console.WriteLine(vnResult.StatusCode);
          }
        }

2.	Adicione o seguinte código ao método Main para chamar o método que você acabou de adicionar:

		CreateNetwork(credential);
		Console.ReadLine();

###Criar uma máquina virtual

Agora que você criou todos os recursos de suporte, você pode criar uma máquina virtual.

1.	Adicione o seguinte método à classe Program para criar a máquina virtual:

		public async static void CreateVirtualMachine(TokenCloudCredentials credential)
        {
          using (var computeClient = new ComputeManagementClient(credential))
          {
            Console.WriteLine("Creating the availability set...");
            var avSetResponse = await computeClient.AvailabilitySets.CreateOrUpdateAsync(
              "mytestrg1",
              new AvailabilitySet
              {
                Name = "mytestav1",
                Location = "West US"
              } );
            Console.WriteLine(avSetResponse.StatusCode);

            var networkClient = new NetworkResourceProviderClient(credential);
            var nicResponse = await networkClient.NetworkInterfaces.GetAsync("mytestrg1", "mytestnic1");

            Console.WriteLine("Creating the virtual machine...");
            var putVMResponse = await computeClient.VirtualMachines.CreateOrUpdateAsync(
              "mytestrg1",
              new VirtualMachine
              {
                Name = "mytestvm1",
                Location = "West US",
                AvailabilitySetReference = new AvailabilitySetReference
                {
                  ReferenceUri = avSetResponse.AvailabilitySet.Id
                },
                HardwareProfile = new HardwareProfile
                {
                  VirtualMachineSize = "Standard_A0"
                },
                OSProfile = new OSProfile
                {
                  AdminUsername = "mytestuser1",
                  AdminPassword = "Mytestpass1",
                  ComputerName = "mytestsv1",
                  WindowsConfiguration = new WindowsConfiguration
                  {
                    ProvisionVMAgent = true
                  }
                },
                NetworkProfile = new NetworkProfile
                {
                  NetworkInterfaces = new List<NetworkInterfaceReference>
                  {
                    new NetworkInterfaceReference
                    {
                      ReferenceUri = nicResponse.NetworkInterface.Id
                    }
                  }
                },
                StorageProfile = new StorageProfile
                {
                  SourceImage = new SourceImageReference
                  {
                    ReferenceUri = "/{subscription-id}/services/images/a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201502.01-en.us-127GB.vhd"
                  },
                  OSDisk = new OSDisk
                  {
                    Name = "myosdisk1",
                    CreateOption = "FromImage",
                    VirtualHardDisk = new VirtualHardDisk
                    {
                      Uri = "http://mytestsa1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    }
                  }
                }
              } );
            Console.WriteLine(putVMResponse.StatusCode);
          }
        }

	>[AZURE.NOTE]Os nomes vhd de imagem são alterados regularmente na galeria de imagens, portanto, você precisa obter um nome de imagem atual para implantar a máquina virtual. Para fazer isso, confira [Gerenciar imagens do Windows usando o Windows PowerShell](https://msdn.microsoft.com/library/azure/dn790330.aspx), e substitua {nome-imagem-origem} pelo nome do arquivo vhd que você deseja usar. Por exemplo, "a699494373c04fc0bc8f2bb1389d6106\_\_Windows-Server-2012-R2-201411.01-en.us-127GB.vhd".

	Substitua {id-assinatura} pelo identificador da sua assinatura.

2.	Adicione o seguinte código ao método Main para chamar o método que você acabou de adicionar:

		CreateVirtualMachine(credential);
        Console.ReadLine();

##Etapa 5: adicionar código para excluir os recursos

Como você é cobrado pelos recursos usados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Se você quiser excluir as máquinas virtuais e todos os recursos de suporte, tudo o que precisa fazer é excluir o grupo de recursos.

1.	Adicione o seguinte método à classe Program para excluir o grupo de recursos:

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
        {
          Console.WriteLine("Deleting resource group...");
          using (var resourceGroupClient = new ResourceManagementClient(credential))
          {
            var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
            Console.WriteLine(rgResult.StatusCode);
          }
        }

2.	Adicione o seguinte código ao método Main para chamar o método que você acabou de adicionar:

		DeleteResourceGroup(credential);
        Console.ReadLine();

##Etapa 6: executar o aplicativo de console

1.	Para executar o aplicativo de console, clique em **Iniciar** no Visual Studio e, em seguida, entre no AD do Azure usando o mesmo nome de usuário e senha que você usa com sua assinatura.

2.	Pressione **Enter** após cada código de status ser retornado para criar cada recurso. Depois que a máquina virtual for criada, execute a próxima etapa antes de pressionar Enter para excluir todos os recursos.

	Devem ser necessários cerca de cinco minutos para o aplicativo de console executar completamente do início ao fim. Antes de pressionar Enter para iniciar a exclusão de recursos, você pode levar alguns minutos para verificar a criação de recursos no portal de visualização do Azure antes de excluí-los.

3. Procure os logs de auditoria no portal de visualização do Azure para ver o status dos recursos:

	![Criar um aplicativo do AD](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=Oct15_HO3-->