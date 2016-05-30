<properties
   pageTitle="SDK do Gerenciador de Recursos para. NET| Microsoft Azure"
   description="Uma visão geral dos exemplos de autenticação e de uso do SDK do Gerenciador de Recursos para .NET"
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
   ms.date="05/17/2016"
   ms.author="navale;tomfitz;"/>

# SDK do Gerenciador de Recursos do Azure para .NET  
Os SDKs de Visualização do ARM (Azure Resource Manager - Gerenciador de Recursos do Azure) estão disponíveis em várias linguagens e plataformas. Cada uma dessas implementações de linguagem estão disponíveis por meio dos gerenciadores de pacotes de seu ecossistema e do GitHub.

O código em cada um desses SDKs é gerado a partir de [especificações da API RESTful do Azure](https://github.com/azure/azure-rest-api-specs). Essas especificações são software livre e se baseiam na especificação do Swagger v2. O código do SDK é um código gerado por um projeto de software livre chamado [AutoRest](https://github.com/azure/autorest). O AutoRest transforma essas especificações da API RESTful em bibliotecas de cliente em várias linguagens. Se houver aspectos do código gerado nos SDKs que você deseja melhorar, todo o conjunto de ferramentas para criar SDKs está liberado, disponível gratuitamente e baseado no formato da especificação da API amplamente adotado.

O SDK do Azure para .NET é fornecido como um conjunto de Pacotes NuGet que ajudam você a chamar a maioria das APIs expostas pelo Gerenciador de Recursos do Azure. Se o SDK não expor a funcionalidade desejada, você poderá facilmente combinar o SDK com chamadas regulares à API REST do ARM nos bastidores.

Esta documentação não se destina a descrever todos os aspectos do SDK do Azure para .NET, APIs do ARM do Azure ou o Visual Studio. Ela serve apenas como uma maneira rápida de começar.

Um projeto de exemplo completo para baixar, a partir do qual todos os trechos do código abaixo foram tirados, pode ser encontrado [aqui](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net).

## Autenticação
A autenticação para ARM é tratada pelo Azure AD (Active Directory). Para se conectar a uma API, você primeiro precisa se autenticar com o Azure AD para receber um token de autenticação que poderá ser passado em cada solicitação. Para obter esse token, primeiro você precisa criar o que chamamos de Aplicativo do Azure AD e uma Entidade de Serviço que será usada para fazer logon. Siga [Criar aplicativo Azure AD e entidade de serviço](resource-group-create-service-principal-portal.md) para obter instruções passo a passo.

Após criar a entidade de serviço, você deverá ter:
* ID do Cliente (GUID)
* Segredo do cliente (cadeia de caracteres)
* Id do locatário (GUID) ou nome de domínio (cadeia de caracteres)

### Recebendo o Token de Acesso do código
O token de autenticação pode ser adquirido facilmente com as linhas de código, passando apenas a ID do Locatário do Azure AD, a ID do Cliente de Aplicativo do Azure AD e o Segredo do Cliente do aplicativo do Azure AD abaixo. Salve o token para várias solicitações, já que, por padrão, ele é válido por uma hora.

```csharp
private static AuthenticationResult GetAccessToken(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Aquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* AAD URI */
            + $"{tenantId}.onmicrosoft.com" /* Tenant ID or AAD domain */);

    var credential = new ClientCredential(clientId, clientSecret);

    AuthenticationResult token = authContext.AcquireToken("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### Consultando as assinaturas do Azure conectadas ao aplicativo autenticado
Uma das primeiras coisas que você deveria fazer é consultar quais Assinaturas do Azure estão associadas ao aplicativo recém-autenticado. A ID de Assinatura para sua assinatura de destino tem que passar para cada chamada à API que você fizer de agora em diante.

O código de exemplo abaixo consulta diretamente as APIs do Azure usando a API REST, ou seja, sem usar recursos no SDK do Azure para .NET.

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

Observe que podemos obter uma resposta JSON do Azure de onde extraímos as IDs de assinaturas para retornar uma lista de IDs. Todas as chamadas subsequentes às APIs do ARM nesta documentação usam apenas uma única ID de Assinatura do Azure. Portanto, se seu aplicativo estiver associado a várias assinaturas, basta escolher a assinatura correta e passar como um parâmetro no futuro.

A partir daqui, todas as chamadas que fazemos em relação às APIs do Azure usarão o SDK do Azure para .NET. Portanto, o código parecerá um pouco diferente.

### Encapsulando o token como um Objeto de TokenCredentials
Todas as chamadas à API a seguir precisarão do token que você recebeu do Azure AD no formato de um objeto de "TokenCredentials". Esse objeto é criado facilmente passando apenas o token bruto como um parâmetro para o construtor da classe.

```csharp
var credentials = new TokenCredentials(token);
```

## Criando grupo de recursos
Tudo no Azure gira em torno dos Grupos de Recursos, portanto, vamos começar criando um. Os recursos gerais e os grupos de recursos são tratados pelo *ResourceManagementClient* e, como qualquer um dos Clientes de Gerenciamento mais especializados que vamos usar, você precisa fornecer suas credenciais, além de uma ID de Assinatura, para identificar com qual assinatura deseja trabalhar.

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
Há diversas maneiras de interagir com as APIs do Gerenciador de Recursos do Azure, mas as duas maneiras principais de fazer isso são:

* Manualmente, chamando Provedores de Recursos específicos manualmente ou
* Usando o Modelo de Gerenciador de Recursos do Azure (conhecido como Modelo de ARM)

O uso dos modelos do ARM oferece os seguintes benefícios:

* Especifique declarativamente como você deseja que seja o resultado final, em vez de como ele deveria ser obtido
* Você não precisa lidar manualmente com a execução paralela de suas implantações. O ARM fará isso para você
* Você não precisa aprender C# ou outra linguagem para implantar um modelo de ARM, embora você possa usar qualquer linguagem para iniciar a implantação de um modelo
* A linguagem específica do domínio, DSL, que é usada nos modelos é compilada usando JSON e é fácil de entender para qualquer pessoa que trabalha com JSON

Mesmo com todos os benefícios dos modelos, vamos começar mostrando como chamar a API manualmente.

### Criando uma Máquina Virtual, peça por peça
Agora temos nosso grupo de recursos e nossa assinatura. Se quisermos implantar uma Máquina Virtual, precisaremos descobrir quais partes realmente constituem uma Máquina Virtual, na verdade, são várias peças:

* Uma ou mais Contas de Armazenamento para armazenar discos persistentes
* Um ou mais Endereços IP Públicos, PIP, por serem acessíveis pela Internet (inclui um nome DNS)
* Uma ou mais Redes Virtuais, VNET, para comunicação interna entre seus recursos
* Uma ou mais Placas de Interface de Rede, NIC, para permitir que a Máquina Virtual se comunique
* Uma ou mais Máquinas Virtuais, VM, para executar nosso software

Outro ponto interessante é também como alguns desses recursos podem ser criados em paralelo, enquanto outros, não. Por exemplo:

* NICs, dependem de PIP e de VNet
* VMs, dependem de NICs e Contas de Armazenamento

Você precisa não tentar instanciar todos os recursos antes das dependências necessárias serem criadas. O [exemplo](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) completo fornecido com esta documentação mostra como você pode criar seus recursos em paralelo com eficiência mantendo o controle do que foi criado.

#### Criando a Conta de Armazenamento
Você precisa de uma conta de armazenamento para armazenar os VHDs Virtuais para a Máquina Virtual. Se você já tiver uma conta de armazenamento, poderá usá-la para várias VMs. No entanto, lembre-se de distribuir a carga entre várias contas de armazenamento para não ter problemas com limites. Lembre-se de que o tipo de Conta de Armazenamento e sua localização podem limitar o Tamanho da VM que você pode escolher, já que nem todos os Tamanhos de VM estão disponíveis em todas as regiões e/ou para todos os tipos de conta de armazenamento.

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

#### Criando o Endereço IP Público, o PIP
O Endereço IP público é o que torna os recursos no Azure acessíveis pela Internet. Junto com o Endereço IP, você também receberá um nome de domínio totalmente qualificado, FQDN, que poderá usar para facilitar o acesso.

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
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support Static IP Addresses but could be extended to do so
        });

    return createPipTask;
}
```

#### Criando a Rede Virtual, a VNET
Cada VM criada com as APIs do ARM precisa fazer parte de uma Rede Virtual, mesmo se a VM estiver sozinha nela. A rede virtual deve conter pelo menos uma sub-rede, mas você pode ter muitas para dividir e proteger seus recursos em várias sub-redes.

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

#### Criando a Placa de Interface de Rede, a NIC
A Placa de Interface de Rede, NIC, é o que conecta a VM com a Rede Virtual na qual ela reside. Uma VM pode ter várias NICs e, portanto, estar associada a várias Redes Virtuais. Esse exemplo pressupõe que você só está anexando VMs a uma VNET.

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

#### Criando a Máquina Virtual
Por fim, é hora de criar a Máquina Virtual real. A VM depende diretamente ou indiretamente de todos os recursos criados acima. Portanto, você precisa esperar que todos os itens acima estejam prontos antes de tentar provisionar uma VM. O provisionamento de uma VM é o recurso que leva mais tempo. Sendo assim, saiba que seu aplicativo aguardará o provisionamento por um bom tempo.

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

### Usando uma implantação de modelo
Leia e siga o tutorial [Implantar recursos do Azure usando bibliotecas do .NET e um modelo](./virtual-machines/virtual-machines-windows-csharp-template.md) para obter instruções detalhadas sobre como implantar um modelo.

Resumindo: a implantação de um modelo é muito mais fácil do que provisionar os recursos manualmente e o código abaixo mostra como fazer isso apontando para os URIs onde o modelo e um arquivo de parâmetros estão localizados.

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


 
   

<!---HONumber=AcomDC_0518_2016-->