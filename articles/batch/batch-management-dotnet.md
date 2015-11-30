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
	ms.date="11/10/2015"
	ms.author="v-marsma"/>

# Gerenciar contas e cotas do Lote do Azure e com o .NET do Gerenciamento do Lote

> [AZURE.SELECTOR]
- [Azure preview portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)

Menor carga de manutenção em seus aplicativos Lote do Azure usando a biblioteca [.NET do Gerenciamento do Lote][api_mgmt_net] para automatizar a criação, a exclusão, o gerenciamento essencial e a descoberta de cotas da conta do Lote.

- **Criar e excluir contas do Lote** em qualquer região. Se como um ISV (fornecedor independente de software), por exemplo, você fornece um serviço para os clientes no qual cada um é atribuído a uma conta do Lote separada para fins de cobrança, pode adicionar recursos de criação e exclusão de conta no portal do cliente.
- **Recuperar e regenerar chaves de conta** programaticamente para qualquer uma de suas contas do Lote. Isso é particularmente útil para manter a conformidade com políticas de segurança que podem impor a substituição periódica ou a expiração das chaves de conta. Quando você tiver um número de contas do Lote em várias regiões do Azure, a automação desse processo de substituição aumentará a eficiência da solução.
- **Verificar cotas de conta** e eliminar as suposições de tentativa e erro na determinação dos limites de cada conta do Lote. Ao verificar suas cotas de conta antes de iniciar trabalhos, de criar pools ou de adicionar nós de computação, você poderá ajustar proativamente quando ou onde esses recursos de computação serão criados. Você pode determinar quais contas exigem aumento de cota antes da alocação de recursos adicionais a elas.
- **Combine os recursos de outros serviços do Azure** para uma experiência de gerenciamento completa, aproveitando o Gerenciamento do Lote do .NET, o [Active Directory do Azure][aad_about] e o [Gerenciador de Recursos do Azure][resman_overview] juntos no mesmo aplicativo. Usando esses recursos e suas APIs, você pode fornecer uma experiência de autenticação, criação e exclusão de grupos de recursos ininterrupta, além dos recursos descritos acima para uma solução de gerenciamento de ponta a ponta.

> [AZURE.NOTE]Embora este artigo se concentre no gerenciamento programático de suas contas, chaves e cotas do Lote, você poderá executar muitas dessas atividades usando o [Portal de visualização do Azure][azure_portal]. Confira [Criar e gerenciar uma conta do Lote do Azure no Portal de visualização do Azure](batch-account-create-portal.md) e [Cotas e limites do serviço do Lote do Azure](batch-quota-limit.md) para saber mais.

## Criar e excluir contas do Lote

Como mencionado acima, um dos principais recursos da API de Gerenciamento do Lote é criar e excluir contas do Lote dentro de uma região do Azure. Para fazer isso, você deverá usar [BatchManagementClient.Accounts.CreateAsync][net_create] e [DeleteAsync][net_delete] ou suas correspondentes síncronas.

O trecho de código a seguir cria uma conta, obtém a conta recém-criada do serviço do Lote e a exclui. Neste e em outros trechos neste artigo, `batchManagementClient` é uma instância completamente inicializada de [BatchManagementClient][net_mgmt_client].

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

> [AZURE.NOTE]Os aplicativos que usam a biblioteca do Gerenciamento do Lote do .NET e seu BatchManagementClient exigem acesso de **administrador de serviço** ou de **coadministrador** à assinatura que possui a conta do Lote a ser gerenciada. Confira a seção do [Active Directory do Azure](#aad) abaixo e o código de exemplo [AccountManagement][acct_mgmt_sample] para saber mais.

## Recuperar e regenerar chaves de conta

Obter chaves de conta principal e secundária de qualquer conta do Lote na sua assinatura usando [ListKeysAsync][net_list_keys] e regenerar essas chaves com [RegenerateKeyAsync][net_regenerate_keys].

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

> [AZURE.TIP]Você pode criar um fluxo de trabalho simplificado de conexão para os aplicativos de gerenciamento. Primeiro, obtenha uma chave de conta para a conta do Lote que você deseja gerenciar com [ListKeysAsync][net_list_keys] e use essa chave ao inicializar as [BatchSharedKeyCredentials][net_sharedkeycred] da biblioteca .NET do Lote usadas ao inicializar um [BatchClient][net_batch_client].

## Verificar a assinatura e cotas da conta do Lote do Azure

As assinaturas do Azure e os serviços Azure individuais, como o Lote, têm todos cotas padrão limitando o número de determinadas entidades neles. As cotas padrão de assinaturas do Azure podem ser encontradas no [Assinatura do Azure e limites do serviço, cotas e restrições](./../azure-subscription-service-limits.md) e as do serviço do Lote podem ser encontradas em [Cotas e limites do serviço do Lote do Azure](batch-quota-limit.md). A biblioteca .NET de Gerenciamento do Lote fornece a capacidade de verificar essas cotas em seus aplicativos, permitindo que você tome decisões de alocação antes de adicionar contas ou recursos, como pools de computação e nós de computação.

### Verificar uma assinatura do Azure para cotas de conta do Lote

Antes de criar uma conta do Lote em uma região, verifique a sua assinatura do Azure para ver se é possível adicionar uma conta nessa região.

No trecho de código abaixo, podemos usar primeiro [BatchManagementClient.Accounts.ListAsync][net_mgmt_listaccounts] para obter uma coleção de todas as contas do Lote em uma assinatura. Depois que obtivemos essa coleção, podemos determinar quantas contas há na região de destino, usar [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] para obter a cota da conta do Lote e determinar quantas contas (se houver) podem ser criadas nessa região.

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

Antes de aumentar os recursos de computação em sua solução do Lote, você pode verificar se os recursos que você pretende alocar não esconderão as cotas de conta que estão atualmente em vigor. No trecho de código a seguir, imprimimos as informações de cota para a conta do Lote denominada `mybatchaccount`, mas, em seu próprio aplicativo, você pode usar essas informações para determinar se a conta pode lidar com os recursos adicionais que deseja criar.

```
// First obtain the Batch account
BatchAccountGetResponse getResponse = await batchManagementClient.Accounts.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT]Embora existam cotas padrão para assinaturas e serviços do Azure, muitos desses limites podem ser aumentados emitindo uma solicitação no [Portal de visualização do Azure][azure_portal]. Por exemplo, confira [Cotas e limites do serviço do Lote do Azure](batch-quota-limit.md) para obter instruções sobre como aumentar suas cotas de conta do Lote.

## .NET do Gerenciamento do Lote, AAD e Gerenciador de Recursos

Ao trabalhar com a biblioteca .NET do Gerenciamento do Lote, normalmente você aproveitará os recursos tanto do [AAD][aad_about] (Active Directory do Azure) quanto do [Gerenciador de Recursos do Azure][resman_overview]. O projeto de exemplo discutido abaixo usa tanto o Active Directory do Azure quanto o Gerenciador de Recursos ao demonstrar a API do Gerenciamento do Lote do .NET.

### <a name="aad"></a>Active Directory do Azure

O Azure em si usa o AAD (Active Directory do Azure) para a autenticação de seus clientes, administradores de serviços e usuários organizacionais. No contexto do Gerenciamento do Lote do .NET, você o usa para autenticar um administrador ou coadministrador de assinatura que permitirá que a biblioteca de gerenciamento confira o serviço do Lote e execute as operações descritas neste artigo.

No projeto de exemplo discutido abaixo, a [ADAL][aad_adal] (Biblioteca de Autenticação do Active Directory) é usada para solicitar ao usuário as credenciais de ID da Microsoft. Quando as credenciais de administrador ou coadministrador de serviço são fornecidas, isso permite que o aplicativo confira uma lista de assinaturas do Azure e crie e exclua grupos de recursos e contas do Lote.

### Gerenciador de Recursos

Ao criar contas do Lote com a biblioteca .NET de Gerenciamento do Lote, você geralmente as criará dentro de um [Grupo de Recursos][resman_overview]. Você pode criar o grupo de recursos programaticamente usando o [ResourceManagementClient][resman_client] encontrado na biblioteca [.NET do Gerenciador de Recursos][resman_api] ou pode adicionar uma conta a um grupo de recursos existente criado anteriormente usando o [Portal de visualização do Azure][azure_portal].

## <a name="sample"></a>Projeto de exemplo no GitHub

Confira o projeto de exemplo [AccountManagment][acct_mgmt_sample] no GitHub para ver a biblioteca .NET de Gerenciamento do Lote em ação. Esse aplicativo de console mostra a criação e o uso do [BatchManagementClient][net_mgmt_client] e do [ResourceManagementClient][resman_client], bem como o uso da [ADAL][aad_adal] (Biblioteca de autenticação do Active Directory), que é necessária a ambos os clientes.

> [AZURE.IMPORTANT]Para executar o aplicativo de exemplo com êxito, você deve registrá-lo no Active Directory do Azure usando o Portal de Gerenciamento do Azure. Confira **Adicionando um aplicativo** em [Integrando aplicativos ao Active Directory do Azure][aad_integrate] e siga as etapas no artigo para registrar o aplicativo de exemplo em sua própria conta.

O aplicativo de exemplo demonstra as seguintes operações:

1. Adquirir um token de segurança do AAD (Active Directory do Azure) usando [ADAL][aad_adal]. Se o usuário já não estiver conectado, será solicitado que ele forneça suas credenciais do Azure.
2. Usando o token de segurança obtido do AAD, crie um [SubscriptionClient][resman_subclient] para consultar uma lista de assinaturas associadas à conta do Azure, permitindo que o usuário selecione um se vários forem encontrados.
3. Criar um novo objeto de credenciais associado à assinatura selecionada
4. Crie um [ResourceManagementClient][resman_client] usando as novas credenciais
5. Use o [ResourceManagementClient][resman_client] para criar um novo grupo de recursos
6. Use o [BatchManagementClient][net_mgmt_client] para executar várias operações de conta do Lote:
  - Criar uma nova conta do Lote no grupo de recursos recém-criado
  - Obter a conta recém-criada do serviço do Lote
  - Imprimir as chaves de conta da nova conta
  - Regenerar uma nova chave primária para a conta
  - Imprimir as informações de cota da conta
  - Imprimir as informações de cota da assinatura
  - Imprimir todas as contas da assinatura
  - Excluir conta recém-criada
7. Exclua o grupo de recursos

Antes de excluir o grupo de recursos e a conta do Lote, você pode inspecionar ambos no [Portal de visualização do Azure][azure_portal]\:

![Portal de visualização do Azure exibindo o grupo de recursos e a conta do Lote][1] <br /> *Portal de visualização do Azure exibindo o novo grupo de recursos e a conta do Lote*

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

<!---HONumber=Nov15_HO4-->