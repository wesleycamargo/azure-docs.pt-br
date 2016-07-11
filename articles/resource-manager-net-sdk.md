<properties
   pageTitle="SDK do Gerenciador de Recursos para .NET| Microsoft Azure"
   description="Uma visão geral dos exemplos de autenticação e uso do SDK do Gerenciador de Recursos para .NET"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="navale;tomfitz;"/>

# SDK do Azure Resource Manager para .NET  
Os SDKs do Azure Resource Manager estão disponíveis em várias linguagens e plataformas. Cada uma dessas implementações da linguagem está disponível por meio do gerenciador de pacotes do ecossistema e do GitHub.

O código em cada um desses SDKs é gerado a partir de [especificações da API RESTful do Azure](https://github.com/azure/azure-rest-api-specs). Essas especificações têm o código-fonte aberto e baseiam-se na especificação Swagger 2.0. O código do SDK é gerado por um projeto de fonte-aberta denominado [AutoRest](https://github.com/azure/autorest). O AutoRest transforma essas especificações da API RESTful em bibliotecas de cliente em várias linguagens. Se você quiser melhorar algum aspecto do código gerado nos SDKs, todo o conjunto de ferramentas para criar os SDKs é aberto, está disponível gratuitamente e baseado em um formato de especificação API amplamente adotado.

O [SDK do Azure para .NET](https://azure.microsoft.com/downloads/) é um conjunto de pacotes NuGet que ajuda a chamar a maioria das APIs do Azure Resource Manager. Se o SDK não fornecer a funcionalidade necessária, você poderá combinar facilmente o SDK com as chamadas regulares para a API REST do Gerenciador de Recursos nos bastidores.

Este artigo não se destina a descrever todos os aspectos do SDK do Azure para .NET, APIs do Azure Resource Manager ou Visual Studio. É fornecido como uma maneira rápida para você começar.

Todos os trechos de código a seguir vêm de um [projeto de exemplo para download](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net).

## Instalar os pacotes NuGet

Os exemplos neste artigo exigem dois pacotes NuGet (além do SDK do Azure para .NET). Para instalar:

1. No Visual Studio, clique com o botão direito em seu projeto e selecione **Gerenciar pacotes NuGet**.
2. Procure **Microsoft.IdentityModel.Clients.ActiveDirectory** e instale a versão estável mais recente do pacote.
3. Procure **Microsoft.Azure.Management.ResourceManager** e selecione **Incluir pré-lançamento**. Instale a versão mais recente de visualização (por exemplo, 1.1.2-preview).

## Autenticar
O Azure Active Directory (AD do Azure) lida com a autenticação do Gerenciador de Recursos. Para conectar qualquer API, primeiro você precisa autenticar-se no AD do Azure para receber um token de acesso que poderá passar em cada solicitação. Para obter esse token, você precisa criar um aplicativo AD do Azure e uma entidade de serviço que será usada para entrar. Para obter instruções passo a passo, siga um destes artigos:

- [Usar o Azure PowerShell para criar um aplicativo do Active Directory para acessar recursos](resource-group-authenticate-service-principal.md)
- [Usar a CLI do Azure para criar um aplicativo do Active Directory para acessar recursos](resource-group-authenticate-service-principal-cli.md)
- [Usar o portal do Azure para criar um aplicativo do Active Directory que pode acessar os recursos](resource-group-create-service-principal-portal.md)

Após criar a entidade de serviço, você deverá ter:

- ID do cliente ou do aplicativo (GUID)
- Segredo do cliente ou senha (string)
- ID do locatário (GUID) ou nome de domínio (cadeia de caracteres)

### Receber o token de acesso do código
Você pode adquirir o token de acesso usando as seguintes linhas de código, passando apenas sua ID de locatário do AD do Azure, ID de cliente do aplicativo do AD do Azure e segredo de cliente do aplicativo do AD do Azure. Salve o token para várias solicitações, porque ele é válido por uma hora por padrão.

```csharp
private static async Task<AuthenticationResult> GetAccessTokenAsync(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
            + $"{tenantId}" /* Tenant ID */);

    var credential = new ClientCredential(clientId, clientSecret);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

Em vez de usar a ID do locatário para se conectar, você pode usar o domínio do AD do Azure, como mostrado no código abaixo. O uso dessa abordagem requer a alteração da assinatura do método para incluir o nome de domínio, em vez da ID do locatário.

```csharp
AuthenticationContext authContext = new AuthenticationContext
    ("https://login.windows.net/" /* Azure AD URI */
    + $"{domain}.onmicrosoft.com");
```

Você pode obter o token de acesso para um aplicativo AD do Azure que usa um certificado para autenticação usando este código:

```csharp
private static async Task<AuthenticationResult> GetAccessTokenFromCertAsync(string tenantId, string clientId, string certName)
{
    Console.WriteLine("Acquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* Azure AD URI */
        + $"{tenantId}" /* Tenant ID or Azure AD domain */);

    X509Certificate2 cert = null;
    X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);

    try
    {
        store.Open(OpenFlags.ReadOnly);
        var certCollection = store.Certificates;
        var certs = certCollection.Find(X509FindType.FindBySubjectName, certName, false);
        cert = certs[0];
    }
    finally
    {
        store.Close();
    }

    var certCredential = new ClientAssertionCertificate(clientId, cert);

    var token = await authContext.AcquireTokenAsync("https://management.azure.com/", certCredential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### Consultar as assinaturas do Azure anexadas ao aplicativo autenticado
Talvez você queira consultar as assinaturas do Azure que estão associadas ao aplicativo recém-autenticado. A ID de assinatura para sua assinatura de destino terá que passar para cada chamada da API feita de agora em diante.

O código de exemplo a seguir consulta as APIs do Azure diretamente usando a API REST. Ou seja, não usa os recursos no SDK do Azure para .NET.

```csharp
async private static Task<List<string>> GetSubscriptionsAsync(string token)
{
    Console.WriteLine("Querying for subscriptions");
    const string apiVersion = "2015-01-01";

    var client = new HttpClient();
    client.BaseAddress = new Uri("https://management.azure.com/");
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await client.GetAsync($"subscriptions?api-version={apiVersion}");

    var jsonResponse = response.Content.AsString();

    var subscriptionIds = new List<string>();
    dynamic json = JsonConvert.DeserializeObject(jsonResponse);

    for (int i = 0; i < json.value.Count; i++)
    {
        subscriptionIds.Add(json.value[i].subscriptionId.Value);
    }

    Console.WriteLine($"Found {subscriptionIds.Count} subscription(s)");
    return subscriptionIds;
}
```

Observe que você obtém uma resposta JSON do Azure. Então, extrai as IDs de assinatura dessa resposta para retornar uma lista de IDs. Todas as chamadas subsequentes para as APIs do Azure Resource Manager neste artigo usam uma única ID de assinatura do Azure. Portanto, se seu aplicativo estiver associado a várias assinaturas, bastará escolher a correta e passá-la como um parâmetro no futuro.

Aqui, toda chamada feita nas APIs do Azure usará o SDK do Azure para .NET. Portanto, o código parecerá um pouco diferente.

### Encapsular o token como um objeto TokenCredentials
Todas as chamadas da API a seguir precisarão do token recebido do AD do Azure no formato de um objeto TokenCredentials. Você pode criar um objeto passando o token bruto como um parâmetro para o construtor da classe.

```csharp
var credentials = new TokenCredentials(token);
```

Se você tiver uma versão anterior do pacote NuGet do Resource Manager (denominado Microsoft.Azure.Management.Resources), precisará usar o código a seguir:

```csharp
var credentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);
```

## Criar um grupo de recursos
Tudo no Azure gira em torno dos grupos de recursos, portanto, iremos criar um. O *ResourceManagementClient* lida com os recursos gerais e os grupos de recursos. Para qualquer um dos clientes de gerenciamento mais especializados a seguir usados, você precisará fornecer suas credenciais e uma ID de assinatura para identificar com qual assinatura deseja trabalhar.

```csharp
private static async Task<ResourceGroup> CreateResourceGroupAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location)
{
    Console.WriteLine($"Creating Resource Group {resourceGroup}");
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await resourceClient.ResourceGroups.CreateOrUpdateAsync(resourceGroup,
        new ResourceGroup
        {
            Location = location
        });
}
```

## Criar recursos manualmente ou usando modelos
Há diversas maneiras de interagir com as APIs do Azure Resource Manager, mas as duas principais são:

* Manualmente, chamando provedores de recursos específicos
* Usando um modelo do Azure Resource Manager

Usar um modelo do Resource Manager tem os seguintes benefícios:

* Você especifica declarativamente como deseja o resultado final, em vez de como ele deveria ser obtido.
* Você não precisa lidar manualmente com a execução paralela de suas implantações. O Resource Manager fará isso para você.
* Você não precisa aprender o C# ou outra linguagem para implantar um modelo do Resource Manager, mesmo que possa usar qualquer linguagem para iniciar uma implantação de modelo.
* A linguagem específica do domínio (DSL) usada nos modelos é criada por meio do JSON. Qualquer pessoa que trabalhou com o JSON deverá achá-lo bem fácil de entender.

Mesmo com todos os benefícios dos modelos, começaremos mostrando como chamar as APIs manualmente.

### Criar uma máquina virtual, peça por peça
Você tem sua assinatura e seu grupo de recursos. Se você quiser implantar uma máquina virtual (VM), precisará saber do que é formada:

* Uma ou muitas contas de armazenamento para armazenar os discos persistentes
* Um ou muitos endereços IP públicos para tornar acessíveis seus recursos no Azure pela Internet (inclui um nome DNS)
* Uma ou muitas redes virtuais para a comunicação interna entre seus recursos
* Uma ou muitas placas de interface de rede (NICs) para permitir que a VMs se comuniquem
* Uma ou muitas máquinas virtuais para executar o software

Alguns desses recursos podem ser criados em paralelo, mas outros não. Por exemplo:

* As NICs dependem dos endereços IP públicos e das redes virtuais.
* As VMs dependem das NICs e das contas de armazenamento.

Não tente instanciar nenhum recurso antes das dependências necessárias terem sido criadas. O [exemplo](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) completo mostra como você pode criar seus recursos em paralelo com eficiência mantendo o controle do que foi criado.

#### Criar a conta de armazenamento
Você precisa de uma conta de armazenamento para armazenar os discos rígidos virtuais para sua máquina virtual. Se você tiver uma conta de armazenamento existente, poderá usá-la para várias VMs. Mas lembre-se de distribuir a carga entre as várias contas de armazenamento para não ficar em limites. Também lembre que o tipo da conta de armazenamento e sua localização podem limitar o tamanho da VM que você pode escolher, já que nem todos os tamanhos de VM estão disponíveis em todas as regiões ou para todos os tipos de conta de armazenamento.

```csharp
private static async Task<StorageAccount> CreateStorageAccountAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, AccountType accountType = AccountType.StandardLRS)
{
    Console.WriteLine("Creating Storage Account");
    var storageClient = new StorageManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await storageClient.StorageAccounts.CreateAsync(resourceGroup, storageAccountName,
        new StorageAccountCreateParameters
        {
            Location = location,
            AccountType = accountType,
        });
}
```

#### Criar um endereço IP público
O endereço IP público é o que torna acessíveis os recursos no Azure pela Internet. Junto com o endereço IP, você receberá um nome de domínio totalmente qualificado (FQDN) que poderá usar para facilitar o acesso.

```csharp
private static Task<PublicIPAddress> CreatePublicIPAddressAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string pipAddressName, string pipDnsName)
{
    Console.WriteLine("Creating Public IP");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createPipTask = networkClient.PublicIPAddresses.CreateOrUpdateAsync(resourceGroup, pipAddressName,
        new PublicIPAddress
        {
            Location = location,
            DnsSettings = new PublicIPAddressDnsSettings { DomainNameLabel = pipDnsName },
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support static IP addresses but can be extended to do so
        });

    return createPipTask;
}
```

#### Criar a rede virtual
Toda VM criada com as APIs do Resource Manager precisa fazer parte de uma rede virtual, mesmo se a VM estiver sozinha nela. A rede virtual deve conter, pelo menos, uma sub-rede, mas você pode usar várias sub-redes para dividir e ajudar a proteger seus recursos.

```csharp
private static Task<VirtualNetwork> CreateVirtualNetworkAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string vNetName, string vNetAddressPrefix, Subnet[] subnets)
{
    Console.WriteLine("Creating Virtual Network");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createVNetTask = networkClient.VirtualNetworks.CreateOrUpdateAsync(resourceGroup, vNetName,
        new VirtualNetwork
        {
            Location = location,
            AddressSpace = new AddressSpace(new[] { vNetAddressPrefix }),
            Subnets = subnets
        });

    return createVNetTask;
}
```

#### Criar a placa da interface de rede
A NIC é o que conecta a VM à rede virtual na qual ela reside. Uma VM pode ter várias NICs e, portanto, estar associada a várias redes virtuais. Este exemplo pressupõe que você está anexando suas VMs a apenas uma rede virtual.

```csharp
private static Task<NetworkInterface> CreateNetworkInterfaceAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string nicName, string nicIPConfigName, PublicIPAddress pip, Subnet subnet)
{
    Console.WriteLine("Creating Network Interface");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createNicTask = networkClient.NetworkInterfaces.CreateOrUpdateAsync(resourceGroup, nicName,
        new NetworkInterface()
        {
            Location = location,
            IpConfigurations = new[] {
                new NetworkInterfaceIPConfiguration
                {
                    Name = nicIPConfigName,
                    PrivateIPAllocationMethod = "Dynamic",
                    PublicIPAddress = pip,
                    Subnet = subnet
                }
            }
        });

    return createNicTask;
}
```

#### Criar a máquina virtual
Por fim, é hora de criar a máquina virtual. A VM depende (direta ou indiretamente) de todos os recursos criados anteriormente, portanto, você precisa esperar que todos os recursos estejam prontos antes de tentar provisionar uma VM. Provisionar uma VM leva mais tempo do que criar os outros recursos, assim, espere que seu aplicativo esteja aguardando que isso aconteça.

```csharp
private static async Task<VirtualMachine> CreateVirtualMachineAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, string vmName, string vmSize, string vmAdminUsername, string vmAdminPassword, string vmImagePublisher, string vmImageOffer, string vmImageSku, string vmImageVersion, string vmOSDiskName, string nicId)
{
    Console.WriteLine("Creating Virtual Machine (this may take a while)");
    var computeClient = new ComputeManagementClient(credentials) { SubscriptionId = subscriptionId };
    var vm = await computeClient.VirtualMachines.CreateOrUpdateAsync(resourceGroup, vmName,
        new VirtualMachine
        {
            Location = location,
            HardwareProfile = new HardwareProfile(vmSize),
            OsProfile = new OSProfile(vmName, vmAdminUsername, vmAdminPassword),
            StorageProfile = new StorageProfile(
                new ImageReference
                {
                    Publisher = vmImagePublisher,
                    Offer = vmImageOffer,
                    Sku = vmImageSku,
                    Version = vmImageVersion
                },
                new OSDisk
                {
                    Name = vmOSDiskName,
                    Vhd = new VirtualHardDisk($"http://{storageAccountName}.blob.core.windows.net/vhds/{vmOSDiskName}.vhd"),
                    Caching = "ReadWrite",
                    CreateOption = "FromImage"
                }),
            NetworkProfile = new NetworkProfile(
                new[] { new NetworkInterfaceReference { Id = nicId } }),
            DiagnosticsProfile = new DiagnosticsProfile(
                new BootDiagnostics
                {
                    Enabled = true,
                    StorageUri = $"http://{storageAccountName}.blob.core.windows.net"
                })
        });

    return vm;
}
```

### Implantar um modelo
Leia o artigo [Implantar recursos do Azure usando as bibliotecas .NET e um modelo](./virtual-machines/virtual-machines-windows-csharp-template.md) para obter instruções detalhadas sobre como implantar um modelo.

Em resumo, implantar um modelo é muito mais fácil do que provisionar os recursos manualmente. O código a seguir mostra como fazer isso apontando para os URIs onde você tem o modelo e um arquivo de parâmetros.

```csharp
private static async Task<DeploymentExtended> CreateTemplatedDeployment(TokenCredentials credentials, string subscriptionId, string resourceGroup, string templateUri, string parametersUri)
{
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };

    return await resourceClient.Deployments.BeginCreateOrUpdateAsync(resourceGroup, "mytemplateddeployment", new Deployment(
        new DeploymentProperties()
        {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink(templateUri),
            ParametersLink = new ParametersLink(parametersUri)
        }));

}
```

<!---HONumber=AcomDC_0629_2016-->