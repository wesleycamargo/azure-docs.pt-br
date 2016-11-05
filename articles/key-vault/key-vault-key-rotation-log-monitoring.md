---
title: Como configurar o Cofre de Chaves com a rotação de chaves e auditoria de ponta a ponta | Microsoft Docs
description: Use estas instruções para ajudá-lo a concluir a configuração com a rotação de chaves e o monitoramento de logs do Cofre de Chaves
services: key-vault
documentationcenter: ''
author: swgriffith
manager: ''
tags: ''

ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: jodehavi;stgriffi

---
# Como configurar o Cofre de Chaves com a rotação de chaves e auditoria de ponta a ponta
## Introdução
Depois de criar seu Cofre de Chaves do Azure, você poderá começar a aproveitar esse cofre para armazenar suas chaves e segredos. Seus aplicativos não precisam manter suas chaves ou segredos, mas solicitá-los do Cofre de Chaves conforme necessário. Isso permite que você atualize as chaves e segredos sem afetar o comportamento do seu aplicativo, que abre uma gama de possibilidades em relação ao seu comportamento de gerenciamento de chaves e segredos.

Este artigo apresenta um exemplo de aproveitamento do Cofre de Chaves do Azure para armazenar um segredo, neste caso uma chave de Conta de Armazenamento do Azure que é acessada por um aplicativo. Ele também demonstra a implementação de uma rotação agendada dessa chave de conta de armazenamento. Por fim, ele percorre uma demonstração de como monitorar os logs de auditoria do Cofre de Chaves e gerar alertas quando são feitas solicitações inesperadas.

> [!NOTE]
> Este tutorial não pretende explicar detalhadamente a configuração inicial do Cofre de Chaves do Azure. Para obter essas informações, consulte [Introdução ao Cofre de Chaves do Azure](key-vault-get-started.md). Ou, para obter instruções de Interface de linha de comando entre diferentes plataformas, consulte [este tutorial equivalente](key-vault-manage-with-cli.md).
> 
> 

## Configurando o KeyVault
Para permitir que um aplicativo recupere um segredo do Cofre de Chaves do Azure, primeiro crie o segredo e carregue-o em seu cofre. Isso pode ser feito facilmente por meio do PowerShell como mostrado abaixo.

Inicie uma sessão do PowerShell do Azure e entre em sua conta do Azure com o seguinte comando:

```powershell
Login-AzureRmAccount
```

Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. O Azure PowerShell obtém todas as assinaturas que estão associadas a essa conta e, por padrão, usa a primeira.

Se você tiver várias assinaturas, talvez tenha que indicar uma assinatura específica que tenha sido usada para criar o Cofre de Chaves do Azure. Digite o seguinte para ver as assinaturas da sua conta:

```powershell
Get-AzureRmSubscription
```

Em seguida, para especificar a assinatura associada ao cofre de chaves do qual os logs serão registrados, digite:

```powershell
Set-AzureRmContext -SubscriptionId <subscriptionID> 
```

Como este artigo demonstra o armazenamento de uma chave de conta de armazenamento como um segredo, você precisará obter essa chave de conta de armazenamento.

```powershell
Get-AzureRmStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Depois de recuperar o segredo, nesse caso sua chave de conta de armazenamento, você precisará convertê-lo em uma cadeia de caracteres segura e, em seguida, criar um segredo com esse valor no Cofre de Chaves.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzureKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```
Em seguida, você desejará obter o URI para o segredo que acabou de criar. Isso será usado em uma etapa posterior, quando você chama o Cofre de Chaves para recuperar o segredo. Execute o seguinte comando do PowerShell e anote o valor de 'Id', que é o URI do segredo.

```powershell
Get-AzureKeyVaultSecret –VaultName <vaultName>
```

## Configurando o aplicativo
Agora que você tem um segredo armazenado, desejará recuperar esse segredo e usá-lo do código. Há algumas etapas necessárias para fazer isso, a primeira e mais importante delas é registrar seu aplicativo com o Azure Active Directory e, em seguida, informar ao Cofre de Chaves do Azure suas informações de aplicativo para que ela possa permitir solicitações de seu aplicativo.

> [!NOTE]
> Seu aplicativo deve ser criado no mesmo locatário do Azure Active Directory que seu Cofre de Chaves.
> 
> 

Primeiro abra a guia de aplicativos do Azure Active Directory

![Abrir aplicativos no Azure AD](./media/keyvault-keyrotation/AzureAD_Header.png)

Escolha 'Adicionar' para adicionar um novo aplicativo ao Azure AD

![Escolher Adicionar aplicativos](./media/keyvault-keyrotation/Azure_AD_AddApp.png)

Deixe o tipo de aplicativo como 'Aplicativo Web E/OU API DA WEB' e forneça um nome de seu aplicativo.

![Nomear o aplicativo](./media/keyvault-keyrotation/AzureAD_NewApp1.png)

Forneça ao aplicativo uma ‘URL de Entrada’ e um ‘URI da ID do Aplicativo’. Eles podem ser qualquer coisa que você desejar para esta demonstração e podem ser alterados posteriormente, se necessário.

![Fornecer os URIs necessários](./media/keyvault-keyrotation/AzureAD_NewApp2.png)

Depois que o aplicativo for adicionado ao Azure AD, você será levado para a página do aplicativo. Desse ponto, clique na guia 'Configurar' e, em seguida, localize e copie o valor de ‘ID do Cliente’. Anote a ID do cliente para as etapas posteriores.

Em seguida, você precisará gerar uma chave para que o aplicativo possa interagir com o Azure AD. Você pode criá-la na seção 'Chaves' na guia 'Configuração'. Anote a chave recém-gerada do aplicativo do Azure AD para usá-la em uma etapa posterior.

![Chaves de aplicativo do Azure AD](./media/keyvault-keyrotation/Azure_AD_AppKeys.png)

Antes de estabelecer as chamadas do seu aplicativo para o Cofre de Chaves, será necessário informar o Cofre de Chaves sobre seu aplicativo e suas permissões. O comando a seguir obtém o nome do cofre e a ID do cliente do seu aplicativo do Azure AD e concede acesso 'Get' para o Cofre de Chaves para o aplicativo.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Agora você está pronto para começar a criar as chamadas do aplicativo. Em seu aplicativo você, precisará primeiro instalar os pacotes NuGet necessários para interagir com o Cofre de Chaves do Azure e o Azure Active Directory. No Visual Studio Package Manager Console, insira os comandos a seguir. Observe que na elaboração deste artigo, a versão atual do pacote do Active Directory é 3.10.305231913, portanto você pode desejar confirmar a versão mais recente e atualizar adequadamente.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

No código do aplicativo, crie uma classe para manter o método de autenticação do seu Active Directory. Neste exemplo a classe é chamada 'Utils'. Depois, você precisará adicionar o seguinte usando.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Em seguida, adicione o seguinte método para recuperar o token JWT do Azure AD. Para a facilidade de manutenção, talvez você queira mover os valores de cadeia de caracteres embutidos no código para a configuração do aplicativo ou Web.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Por fim, você pode adicionar o código necessário para chamar o Cofre de Chaves e recuperar o valor do segredo. Primeiro é necessário adicionar o seguinte usando a instrução.

```csharp
using Microsoft.Azure.KeyVault;
```

Em seguida, você adiciona as chamadas de método para invocar o Cofre de Chaves e recuperar o segredo. Nesse método, você fornecerá o URI do segredo salvo na etapa anterior. Observe o uso do método GetToken da classe Utils criada acima.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Ao executar o aplicativo, agora você deverá se autenticar no Azure Active Directory e, em seguida, recuperar o valor do segredo do Cofre de Chaves do Azure.

## Rotação de chaves usando a Automação do Azure
Há várias opções para implementar uma estratégia de rotação para valores armazenados como segredos do Cofre de Chaves do Azure. Os segredos podem ser rotacionados como parte de um processo manual, programaticamente aproveitando as chamadas à API ou por meio de um script de automação. Para os fins deste artigo, utilizaremos o Azure PowerShell combinado com a Automação do Azure para alterar uma chave de acesso da Conta de Armazenamento do Azure e atualizaremos um segredo do Cofre de Chaves com essa chave.

Para permitir que a Automação do Azure defina valores de segredo no Cofre de Chaves, você precisará obter a ID do cliente para a conexão chamada 'AzureRunAsConnection', que foi criada ao estabelecer sua instância de Automação do Azure. Você pode obter essa ID selecionando 'Ativos' da instância da Automação do Azure. Lá, você seleciona 'Conexões' e, em seguida, seleciona o princípio de serviço 'AzureRunAsConnection'. Você desejará anotar a 'ID do Aplicativo'.

![ID do cliente da Automação do Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Enquanto você ainda estiver na janela Ativos, também desejará selecionar 'Módulos'. Nos módulos, você selecionará 'Galeria' e pesquisará e utilizará a opção 'Importar' para as versões atualizadas de cada um dos módulos a seguir.

    Azure
    Azure.Storage    
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Na elaboração desse artigo, apenas os módulos indicados acima precisavam ser atualizados para o script compartilhado abaixo. Se achar que seu trabalho de automação está falhando, você deve confirmar que tem todos os módulos necessários e suas dependências importados.
> 
> 

Depois de recuperar a ID do aplicativo para a conexão da Automação do Azure, você precisará informar ao Cofre de Chaves do Azure que esse aplicativo tem acesso aos segredos atualizados no seu cofre. Isso pode ser feito com o seguinte comando do PowerShell.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Depois, você selecionará o recurso 'Runbooks' na instância da Automação do Azure e selecionará 'Add a Runbook' (Adicionar um Runbook). Selecione 'Criação Rápida'. Atribua um nome ao runbook e selecione 'PowerShell' como o tipo do runbook. Opcionalmente, adicione uma descrição. Por fim, clique em 'Criar'.

![Criar Runbook](./media/keyvault-keyrotation/Create_Runbook.png)

No painel do editor do novo runbook, você colará o seguinte script do PowerShell.

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

#Optionally you may set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -StorageAccountName $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

No painel do editor, você pode escolher 'Painel de teste' para testar seu script. Depois que o script for executado sem erros, você poderá selecionar a opção 'Publicar' e, em seguida, poderá aplicar um cronograma ao runbook no painel de configuração.

## Pipeline de auditoria do Cofre de Chaves
Ao configurar um Cofre de Chaves do Azure, você pode ativar a auditoria para coletar logs de solicitações de acesso feitas para o Cofre de Chaves. Esses logs são armazenados em uma conta de armazenamento do Azure designada e podem ser extraídos, monitorados e analisados. Abaixo explica um cenário que utiliza os logs de auditoria do Azure Functions, os Aplicativo Lógico do Azure e Cofre de Chaves para criar um pipeline para enviar um email quando os segredos do cofre forem recuperados por um aplicativo que corresponde à ID do aplicativo Web.

Primeiro, você precisará habilitar o registro no Cofre de Chaves. Isso pode ser feito por meio dos seguintes comandos do PowerShell (os detalhes completos podem ser vistos [aqui](key-vault-logging.md)):

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzureRmKeyVault -VaultName '<vaultName>' 
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Depois que isso for habilitado, os logs de auditoria começarão a realizar a coleta na conta de armazenamento designada. Esses logs conterão eventos sobre como e quando seus Cofres de Chaves são acessados e por quem.

> [!NOTE]
> Você pode acessar suas informações de log em até 10 minutos após a operação do Cofre de Chaves. Na maioria dos casos, será mais rápido do que isso.
> 
> 

A próxima etapa é [criar uma fila do Barramento de Serviço do Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Isso será onde os logs de auditoria do Cofre de Chaves são enviados por push. Uma vez na fila, o aplicativo lógico vai coletá-los e agir neles. Criar um Barramento de Serviço é relativamente simples e a seguir estão as etapas de alto nível:

1. Crie um namespace do Barramento de Serviço (se você já tiver um que deseja usar para isso, vá para a etapa 2).
2. Navegue até o Barramento de Serviço no portal e selecione o namespace no qual deseja criar a fila.
3. Selecione Novo e selecione Barramento de Serviço -> Fila e insira os detalhes necessários.
4. Obtenha as informações de conexão do Barramento de Serviço escolhendo o namespace e clicando em *Informações de Conexão*. Você precisará dessas informações para a próxima parte.

Em seguida, você [criará uma função do Azure](../azure-functions/functions-create-first-azure-function.md) para sondar os logs do Cofre de Chaves na conta de armazenamento e obter novos eventos. Essa será uma função disparada em um cronograma.

Crie uma função do Azure (selecione Novo -> Aplicativo de Funções no portal). Durante a criação, você pode usar um plano de hospedagem existente ou criar um novo. Você também pode optar pela hospedagem dinâmica. É possível obter mais detalhes sobre opções de hospedagem do Function [aqui](../azure-functions/functions-scale.md).

Quando a função do Azure for criada, navegue até ela e escolha uma função de temporizador e C# e clique em **Criar** na tela inicial.

![Folha de início do Azure Functions](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Na guia *Desenvolver*, substitua o código de run.csx pelo seguinte:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging; 
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log) 
{ 
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didnt have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //required to order by time as they may not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob) 
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```
> [!NOTE]
> Certifique-se de substituir as variáveis no código acima para apontar para sua conta de armazenamento em que os logs do Cofre de Chaves são gravados, para o Barramento de Serviço criado anteriormente e para o caminho específico para os logs de armazenamento do Cofre de Chaves.
> 
> 

A função obtém o arquivo de log mais recente da conta de armazenamento em que os logs do Cofre de Chaves são gravados, capta os eventos mais recentes do arquivo e os envia por push para uma fila do Barramento de Serviço. Como um único arquivo pode ter vários eventos, por exemplo, em uma hora completa, criamos um arquivo *sync.txt* que a função também analisa para determinar o carimbo de data/hora do último evento que foi obtido. Isso garantirá que não enviaremos por push o mesmo evento várias vezes. Esse arquivo *sync.txt* simplesmente contém um carimbo de data/hora para o último evento encontrado. Os logs, quando carregados, precisam ser classificados com base no carimbo de data/hora para garantir que estão classificados corretamente.

Para essa função, referenciamos algumas bibliotecas adicionais que não estão disponíveis pré-configuradas no Azure Functions. Para incluí-las, precisamos que o Azure Functions as realize o pull usando o nuget. Escolha a opção *Exibir Arquivos*

![Opção Exibir Arquivos](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

e adicione um novo arquivo chamado *project.json* com o seguinte conteúdo:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```
Ao *Salvar*, isso vai disparar o Azure Functions para baixar os binários necessários.

Mude para a guia **Integrar** e atribua ao parâmetro do temporizador um nome significativo para ser usado na função. No código acima, ele espera que o temporizador seja chamado *myTimer*. Especifique uma [expressão CRON](../app-service-web/web-sites-create-web-jobs.md#CreateScheduledCRON) da seguinte maneira: 0 ***** para o temporizador que fará com que a função seja executada uma vez por minuto.

Na mesma guia **Integrar**, adicione uma entrada que será do tipo *Armazenamento de Blobs do Azure*. Ela apontará para o arquivo *sync.txt* que contém o carimbo de data/hora do último evento analisado pela função. Isso será disponibilizado na função pelo nome do parâmetro. No código acima, a entrada do Armazenamento de Blobs do Azure espera que o nome do parâmetro seja *inputBlob*. Escolha a conta de armazenamento em que o arquivo *sync.txt* residirá (pode ser a mesma conta de armazenamento ou uma diferente) e, no campo do caminho, forneça o caminho em que o arquivo fica no formato {container-name}/path/to/sync.txt.

Adicione uma saída que será do tipo de saída do *Armazenamento de Blobs do Azure*. Ela também apontará para o arquivo *sync.txt* que você acabou de definir na entrada. Ele será usado pela função para gravar o carimbo de data/hora do último evento analisado. O código acima espera que esse parâmetro seja chamado *outputBlob*.

Neste ponto, a função está pronta. Certifique-se de voltar para a guia **Desenvolver** e *salve* o código. Verifique a janela de saída quanto a quaisquer erros de compilação e corrija-os adequadamente. Se ele for compilado, o código agora deverá estar em execução e a cada minuto verificará os logs do Cofre de Chaves e enviará por push todos os novos eventos para a fila do Barramento de Serviço definida. Você deve ver as informações de log gravadas na janela de log sempre que a função for disparada.

### Aplicativo lógico do Azure
Em seguida, precisaremos criar um aplicativo lógico do Azure que obterá os eventos que a função está enviando por push para a fila do Barramento de Serviço, analisará o conteúdo e enviará um email com base em uma condição sendo correspondida.

[Crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md) indo até Novo -> Aplicativo Lógico.

Após o aplicativo lógico ser criado, navegue até ele e selecione *editar*. No editor do aplicativo lógico, escolha a API gerenciada *Fila do Barramento de Serviço* e insira as credenciais do Barramento de Serviço para conectá-lo à fila.

![Barramento de Serviço de aplicativo lógico do Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Em seguida, escolha *Adicionar uma condição*. Na condição, mude para o *editor avançado* e insira o seguinte, substituindo o APP\_ID pelo APP\_ID real do seu aplicativo Web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Essa expressão essencialmente retornará **false** se a propriedade **appid** do evento recebido (que é o corpo da mensagem do Barramento de Serviço) não for a **appid** do aplicativo.

Agora, crie uma ação na opção *If no, do nothing... (Se não, não faça nada...)*.

![Escolher ação do aplicativo lógico do Azure](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Para a ação, escolha *Office 365 - send email (Office 365 – enviar email)*. Preencha os campos para criar um email para enviar quando a condição definida retornar false. Se você não tiver o Office 365, poderá consultar alternativas para fazer o mesmo.

Neste ponto, você tem um pipeline de ponta a ponta que, uma vez por minuto, procurará novos logs de auditoria do Cofre de Chaves. Todos os novos logs que encontrar, ele enviará por push para uma Fila de Barramento de Serviço. O aplicativo lógico será disparado assim que uma nova mensagem chegar à fila e, se o appid dentro do evento não corresponder à ID do aplicativo da chamada, enviará um email.

<!---HONumber=AcomDC_0928_2016-->