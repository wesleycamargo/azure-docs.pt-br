<properties
	pageTitle="Gerenciamento de conta com o .NET do Gerenciamento do Lote | Microsoft Azure"
	description="Criar, excluir e modificar contas do Lote do Azure em seus aplicativos com a biblioteca .NET de Gerenciamento do Lote."
	services="batch"
	documentationCenter=".net"
	authors="mmacy"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="batch"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="big-compute"
	ms.date="01/28/2016"
	ms.author="marsma"/>

# Gerenciar contas e cotas do Lote do Azure e com o .NET do Gerenciamento do Lote

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

Você pode diminuir a sobrecarga da manutenção em seus aplicativos de lote do Azure usando a biblioteca [.NET de Gerenciamento de Lotes][api_mgmt_net] para automatizar a criação da conta do Lote, exclusão, gerenciamento de chaves e descoberta de cotas.

- **Criar e excluir contas do Lote** em qualquer região. Se como um ISV (fornecedor independente de software), por exemplo, você fornece um serviço para os clientes no qual cada um é atribuído a uma conta do Lote separada para fins de cobrança, pode adicionar recursos de criação e exclusão de conta no portal do cliente.
- **Recuperar e regenerar chaves de conta** programaticamente para qualquer uma de suas contas do Lote. Isso é particularmente útil para manter a conformidade com políticas de segurança que podem impor a substituição periódica ou a expiração das chaves de conta. Quando você tiver um número de contas do Lote em várias regiões do Azure, a automação desse processo de substituição aumentará a eficiência da solução.
- **Verificar cotas de conta** e eliminar as suposições de tentativa e erro na determinação dos limites de cada conta do Lote. Ao verificar suas cotas de conta antes de iniciar trabalhos, de criar pools ou de adicionar nós de computação, você poderá ajustar proativamente quando ou onde esses recursos de computação serão criados. Você pode determinar quais contas exigem aumento de cota antes da alocação de recursos adicionais a elas.
- **Combine os recursos de outros serviços do Azure** para ter uma experiência de gerenciamento completa, aproveitando o .NET de Gerenciamento do Lote, [Active Directory do Azure][aad_about] e [Gerenciador de Recursos do Azure][resman_overview] juntos no mesmo aplicativo. Usando esses recursos e suas APIs, você pode fornecer uma experiência de autenticação, a capacidade de criação e exclusão de grupos de recursos ininterrupta, além dos recursos descritos acima para uma solução de gerenciamento de ponta a ponta.

> [AZURE.NOTE] Embora este artigo se concentre no gerenciamento programático de suas contas, chaves e cotas do Lote, você poderá executar muitas dessas atividades usando o [Portal do Azure][azure_portal]. Confira [Criar e gerenciar uma conta do Lote do Azure no Portal do Azure](batch-account-create-portal.md) e [Cotas e limites do serviço do Lote do Azure](batch-quota-limit.md) para saber mais.

## Criar e excluir contas do Lote

Como mencionado acima, um dos principais recursos da API de Gerenciamento do Lote é criar e excluir contas do Lote dentro de uma região do Azure. Para fazer isso, você deverá usar [BatchManagementClient.Accounts.CreateAsync][net_create] e [DeleteAsync][net_delete] ou suas correspondentes síncronas.

O trecho de código a seguir cria uma conta, obtém a conta recém-criada do serviço de Lote, então, a exclui. Neste e em outros trechos neste artigo, `batchManagementClient` é uma instância completamente inicializada de [BatchManagementClient][net_mgmt_client].

```
// Create a new Batch account
await batchManagementClient.Accounts.CreateAsync("MyResourceGroup",
	"mynewaccount",
	new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mynewaccount");
AccountResource account = getResponse.Resource;

// Delete the account
await batchManagementClient.Accounts.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] Os aplicativos que usam a biblioteca .NET de Gerenciamento do Lote e sua classe BatchManagementClient requerem o acesso de **administrador de serviço** ou **coadministrador** à assinatura que possui a conta do Lote a ser gerenciada. Confira a seção “[Active Directory do Azure](#aad)” abaixo e o código de exemplo [AccountManagement][acct_mgmt_sample] para saber mais.

## Recuperar e regenerar chaves de conta

Obtenha as chaves da conta principal e secundária de qualquer conta do Lote em sua assinatura usando [ListKeysAsync][net_list_keys]. Você pode regenerar essas chaves usando [RegenerateKeyAsync][net_regenerate_keys].

```
// Get and print the primary and secondary keys
BatchAccountListKeyResponse accountKeys = await batchManagementClient.Accounts.ListKeysAsync("MyResourceGroup", "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.PrimaryKey);
Console.WriteLine("Secondary key: {0}", accountKeys.SecondaryKey);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys = await batchManagementClient.Accounts.RegenerateKeyAsync(
	"MyResourceGroup",
	"mybatchaccount",
	new BatchAccountRegenerateKeyParameters() { KeyName = AccountKeyType.Primary });
```

> [AZURE.TIP] Você pode criar um fluxo de trabalho simplificado de conexão para os aplicativos de gerenciamento. Primeiro, obtenha uma chave de conta para a conta do Lote que você deseja gerenciar com [ListKeysAsync][net_list_keys]. Em seguida, use essa chave ao inicializar a classe [BatchSharedKeyCredentials][net_sharedkeycred] da biblioteca .NET do Lote, que é usada ao inicializar o [BatchClient][net_batch_client].

## Verificar a assinatura e cotas da conta do Lote do Azure

As assinaturas do Azure e os serviços Azure individuais, como o Lote, têm cotas padrão limitando o número de determinadas entidades neles. Para ver as cotas padrão para as assinaturas do Azure, consulte [Assinatura do Azure e limites de serviço, cotas e restrições](./../azure-subscription-service-limits.md). Para ver as cotas padrão do serviço de Lote, confira [Cotas e limites para o serviço de Lote do Azure](batch-quota-limit.md). Usando a biblioteca .NET de Gerenciamento de Lotes, você pode verificar essas cotas em seus aplicativos. Isso permite que você tome decisões de alocação antes de adicionar contas ou recursos de computação, como pools e nós de computação.

### Verificar uma assinatura do Azure para cotas de conta do Lote

Antes de criar uma conta do Lote em uma região, verifique a sua assinatura do Azure para ver se é possível adicionar uma conta nessa região.

No trecho de código abaixo, podemos usar primeiro [BatchManagementClient.Accounts.ListAsync][net_mgmt_listaccounts] para obter uma coleção de todas as contas do Lote em uma assinatura. Depois que obtivemos essa coleção, podemos determinar quantas contas estão na região de destino. Em seguida, usamos [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] para obter a cota da conta do Lote e determinar quantas contas (se houver) podem ser criadas nessa região.

```
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse = await batchManagementClient.Accounts.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}", creds.SubscriptionId, accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

No trecho acima, `creds` é uma instância de [TokenCloudCredentials][azure_tokencreds]. Para ver um exemplo de como criar esse objeto, confira o código de exemplo [AccountManagement][acct_mgmt_sample] no GitHub.

### Verificar as cotas de recursos de computação na conta do Lote

Antes de aumentar os recursos de computação em sua solução do Lote, você pode verificar se os recursos que você pretende alocar não excederão as cotas de conta que estão atualmente em vigor. No trecho de código abaixo, podemos simplesmente imprimir as informações da cota para a conta do Lote denominada `mybatchaccount`. Mas, em seu próprio aplicativo, você pode usar essas informações para determinar se a conta pode lidar com os recursos adicionais que deseja criar.

```
// First obtain the Batch account
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] Embora existam cotas padrão para assinaturas e serviços do Azure, muitos desses limites podem ser aumentados emitindo uma solicitação no [Portal do Azure][azure_portal]. Por exemplo, confira [Cotas e limites do serviço de Lote do Azure](batch-quota-limit.md) para obter instruções sobre como aumentar suas cotas da conta do Lote.

## .NET de Gerenciamento do Lote, AD do Azure e Gerenciador de Recursos

Ao trabalhar com a biblioteca .NET de Gerenciamento do Lote, normalmente você aproveitará os recursos do [Active Directory do Azure][aad_about] (AD do Azure) e do [Gerenciador de Recursos do Azure][resman_overview]. O projeto de exemplo analisado abaixo usa o Active Directory do Azure e o Gerenciador de Recursos ao demonstrar a API do .NET de Gerenciamento do Lote.

### <a name="aad"></a>Active Directory do Azure

O Azure em si usa o AD do Azure (Active Directory do Azure) para a autenticação de seus clientes, administradores de serviços e usuários organizacionais. No contexto do .NET de Gerenciamento de Lotes, você irá usá-lo para autenticar um administrador de assinatura ou coadministrador. Isso permitirá que a biblioteca de gerenciamento consulte o serviço de Lote e execute as operações descritas neste artigo.

No projeto de exemplo analisado abaixo, a [ADAL][aad_adal] (Biblioteca de Autenticação do Active Directory) do Azure é usada para solicitar ao usuário as credenciais da Microsoft. Quando as credenciais do administrador ou coadministrador do serviço são fornecidas, o aplicativo pode consultar o Azure para obter uma lista de assinaturas, criar e excluir os grupos de recursos e contas do Lote.

### Gerenciador de Recursos

Ao criar contas do Lote com a biblioteca .NET de Gerenciamento do Lote, geralmente você irá criá-las em um [grupo de recursos][resman_overview]. Você pode criar o grupo de recursos por meio de programação usando a classe [ResourceManagementClient][resman_client] na biblioteca [.NET do Gerenciador de Recursos][resman_api]. Ou pode adicionar uma conta a um grupo de recursos existente que você criou anteriormente usando o [portal do Azure][azure_portal].

## <a name="sample"></a>Projeto de exemplo no GitHub

Confira o projeto de exemplo [AccountManagment][acct_mgmt_sample] no GitHub para ver a biblioteca .NET de Gerenciamento do Lote em ação. Esse aplicativo de console mostra a criação e uso de [BatchManagementClient][net_mgmt_client] e [ResourceManagementClient][resman_client]. Também demonstra o uso da [ADAL][aad_adal] (Biblioteca de Autenticação do Active Directory) do Azure, que é necessária para os clientes.

Para executar o aplicativo de exemplo com êxito, você deve registrá-lo no AD do Azure usando o portal do Azure. Confira "Como adicionar um Aplicativo" em [Como integrar aplicativos ao Active Directory do Azure][aad_integrate]. Depois, siga as etapas no artigo para registrar o aplicativo de exemplo no Diretório Padrão de sua própria conta. Selecione "Aplicativo de Cliente Nativo" para o tipo de aplicativo e você poderá especificar qualquer URI válido (como `http://myaccountmanagementsample`) para o "URI de redirecionamento". Ele não precisa ser um ponto de extremidade real.

Depois de adicionar o aplicativo, delegue a permissão "Acessar Gerenciamento de Serviços do Azure como organização" para o aplicativo de *API de Gerenciamento de Serviços do Windows Azure* nas configurações do aplicativo no portal:

![Permissões de aplicativo no portal do Azure][2]

Após adicionar o aplicativo conforme descrito acima, atualize `Program.cs` no projeto de exemplo [AccountManagment][acct_mgmt_sample] com o URI de Redirecionamento do aplicativo e a ID do Cliente. Você pode encontrar esses valores na guia "Configurar" do aplicativo:

![Configuração de aplicativo no portal do Azure][3]

O aplicativo de exemplo [AccountManagment][acct_mgmt_sample] demonstra as seguintes operações:

1. Adquira um token de segurança do AD do Azure usando a [ADAL][aad_adal]. Se o usuário ainda não estiver conectado, será solicitado que ele forneça suas credenciais do Azure.
2. Usando o token de segurança obtido no Azure AD, crie um [SubscriptionClient][resman_subclient] para consultar o Azure e obter uma lista de assinaturas associadas à conta. Isso permite que o usuário selecione uma assinatura, se várias forem encontradas.
3. Criar um novo objeto de credenciais associado à assinatura selecionada.
4. Crie um [ResourceManagementClient][resman_client] usando as novas credenciais.
5. Use [ResourceManagementClient][resman_client] para criar um novo grupo de recursos.
6. Use [BatchManagementClient][net_mgmt_client] para executar várias operações de conta do Lote:
  - Crie uma nova conta do Lote no grupo de recursos recém-criado.
  - Obtenha a conta recém-criada no serviço do Lote.
  - Imprima as chaves de conta da nova conta.
  - Regenere uma nova chave primária para a conta.
  - Imprima as informações de cota para a conta.
  - Imprima as informações de cota para a assinatura.
  - Imprima todas as contas na assinatura.
  - Exclua a conta recém-criada.
7. Exclua o grupo de recursos.

Antes de excluir o grupo de recursos e a conta do Lote, você pode inspecionar ambos no [Portal do Azure][azure_portal]\:

![Portal do Azure exibindo o grupo de recursos e a conta do Lote][1] <br /> *Portal do Azure exibindo o novo grupo de recursos e a conta do Lote*

[aad_about]: ../active-directory/active-directory-whatis.md "O que é o Active Directory do Azure?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Cenários de autenticação do AD do Azure"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrando aplicativos ao Active Directory do Azure"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png

<!---HONumber=AcomDC_0204_2016-->