---
title: Configurar o Cofre de Chaves do Azure com a rotação de chaves e auditoria de ponta a ponta | Microsoft Docs
description: Use este guia de instruções para ajudá-lo a configurar a rotação de chaves e monitore os logs do Cofre de chaves.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: ''
ms.assetid: 9cd7e15e-23b8-41c0-a10a-06e6207ed157
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: fb3300a45f905eb57fcc4880269e4a9bed9dac0c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789882"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Configurar o Azure Key Vault com a rotação de chaves e auditoria

## <a name="introduction"></a>Introdução

Depois de ter um cofre de chaves, você pode começar a usá-lo para armazenar chaves e segredos. Seus aplicativos não precisam manter suas chaves ou segredos, mas podem solicitá-los do cofre conforme necessário. Um cofre de chaves permite que você atualize chaves e segredos sem afetar o comportamento do seu aplicativo, que abre uma gama de possibilidades para a sua chave e o gerenciamento de segredos.

>[!IMPORTANT]
> Os exemplos neste artigo são fornecidos apenas para fins ilustrativos. Eles não estiverem se destina para uso em produção. 

Este artigo guia você por:

- Um exemplo de como usar o Azure Key Vault para armazenar um segredo. Neste artigo, o segredo armazenado é a chave de conta de armazenamento do Azure acessada por um aplicativo. 
- Como implementar uma rotação agendada dessa chave de conta de armazenamento.
- Como a chave de monitorar os logs de auditoria do cofre e gerar alertas quando são feitas solicitações inesperadas.

> [!NOTE]
> Este artigo não explica em detalhes a configuração inicial do seu Cofre de chaves. Para obter essa informação, confira [O que é o Azure Key Vault?](key-vault-overview.md). Para obter instruções de interface de linha de comando de plataforma cruzada, consulte [Gerenciar cofre de chaves usando a CLI do Azure](key-vault-manage-with-cli2.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-up-key-vault"></a>Configurar o Cofre de Chaves

Para permitir que um aplicativo recupere um segredo do Cofre de Chaves, primeiro crie o segredo e carregue-o no cofre.

Inicie uma sessão do PowerShell do Azure e entre em sua conta do Azure com o seguinte comando:

```powershell
Connect-AzAccount
```

Na janela pop-up do navegador, insira o nome de usuário e a senha para sua conta do Azure. O PowerShell obterá todas as assinaturas que estão associadas a essa conta. O PowerShell usa a primeira por padrão.

Se tiver várias assinaturas, você terá que especificar a que foi usada para criar o cofre de chaves. Digite o seguinte para ver as assinaturas de sua conta:

```powershell
Get-AzSubscription
```

Para especificar a assinatura que está associada com o Cofre de chaves que você vai estar fazendo o logon, digite:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Como este artigo demonstra como armazenar uma chave de conta de armazenamento como um segredo, você deve obter essa chave de conta de armazenamento.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Depois de recuperar o segredo (no caso, sua chave de conta de armazenamento), você deve converter essa chave em uma cadeia de caracteres segura e, em seguida, criar um segredo com esse valor no cofre de chaves.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Em seguida, obtenha o URI do segredo que você criou. Você precisará esse URI em uma etapa posterior ao chamar o Cofre de chaves e recuperar o segredo. Execute o seguinte comando do PowerShell e anote o valor de ID, que é o URI do segredo:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Configurar o aplicativo

Agora que você tem um segredo armazenado, você pode usar o código para recuperar e usá-lo depois de executar mais algumas etapas.

Primeiro, você deve registrar seu aplicativo com o Azure Active Directory. Em seguida, informar ao Cofre de chaves suas informações de aplicativo para que ele possa permitir solicitações do aplicativo.

> [!NOTE]
> O aplicativo deve ser criado no mesmo locatário do Azure Active Directory que o cofre de chaves.

1. Abra **do Azure Active Directory**.
2. Selecione **Registros do Aplicativo**. 
3. Selecione **novo registro de aplicativo** para adicionar um aplicativo ao Azure Active Directory.

    ![Abrir os aplicativos no Azure Active Directory](./media/keyvault-keyrotation/azure-ad-application.png)

4. Sob **Create**, deixe o tipo de aplicativo como **aplicativo Web / API** e dê um nome de seu aplicativo. Dê ao aplicativo uma **URL de logon**. Essa URL pode ser qualquer coisa que você deseja para esta demonstração.

    ![Criar registro de aplicativo](./media/keyvault-keyrotation/create-app.png)

5. Depois que o aplicativo é adicionado ao Azure Active Directory, abre a página do aplicativo. Selecione **as configurações**e, em seguida, selecione **propriedades**. Copie o valor de **ID do Aplicativo**. Você precisará dela em etapas posteriores.

Em seguida, gere uma chave para seu aplicativo para que ele possa interagir com o Azure Active Directory. Para criar uma chave, selecione **teclas** sob **configurações**. Anote a chave recém-gerada do aplicativo do Azure Active Directory. Ela será necessária em uma etapa posterior. A chave não estarão disponível depois que você sair desta seção. 

![Chaves de aplicativo do Active Directory do Azure](./media/keyvault-keyrotation/create-key.png)

Antes de estabelecer as chamadas do seu aplicativo para o Cofre de chaves, você deve informar o Cofre de chaves sobre seu aplicativo e suas permissões. O comando a seguir usa o nome do cofre e a ID do aplicativo do seu aplicativo do Azure Active Directory para conceder ao aplicativo **obter** acesso ao Cofre de chaves.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Agora você está pronto para começar a criar as chamadas do aplicativo. Em seu aplicativo, você deve instalar os pacotes do NuGet que são necessários para interagir com o Azure Key Vault e o Azure Active Directory. No Visual Studio Package Manager Console, insira os comandos a seguir. Na elaboração deste artigo, a versão atual do pacote do Azure Active Directory é 3.10.305231913, portanto, confirme se a versão mais recente e atualize conforme necessário.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

No código do aplicativo, crie uma classe para manter o método de autenticação do Azure Active Directory. Neste exemplo a classe é chamada **Utils**. Adicione a instrução `using` a seguir:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Em seguida, adicione o seguinte método para recuperar o token JWT do Azure Active Directory. Para facilidade de manutenção, você talvez queira mover os valores de cadeia de caracteres embutida para a configuração da web ou aplicativo.

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

Adicione o código necessário para chamar o Cofre de Chaves e recuperar o valor do segredo. Primeiro, você deve adicionar o seguinte `using` instrução:

```csharp
using Microsoft.Azure.KeyVault;
```

Adicione as chamadas de método para invocar o Cofre de Chaves e recuperar o segredo. Nesse método, você fornece o URI de segredo que salvou na etapa anterior. Observe o uso do **GetToken** método a partir de **Utils** classe criada anteriormente.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Ao executar o aplicativo, agora você deverá se autenticar no Azure Active Directory e recuperar o valor do segredo do Cofre de Chaves do Azure.

## <a name="key-rotation-using-azure-automation"></a>Rotação de chaves usando a Automação do Azure

> [!IMPORTANT]
> Runbooks de automação do Azure ainda exigem o uso do `AzureRM` módulo.

Agora você está pronto para configurar uma estratégia de rotação para os valores armazenados como segredos do Cofre de chaves. Os segredos podem ser girados de várias maneiras:

- Como parte de um processo manual
- Programaticamente, usando chamadas à API
- Por meio de um script de automação do Azure

Para os fins deste artigo, você usará o PowerShell combinado à automação do Azure para alterar a chave de acesso da conta de armazenamento do Azure. Em seguida, você atualizará um segredo do Cofre de chaves com essa nova chave.

Para permitir que a automação do Azure definir os valores do segredo no cofre de chaves, você deve obter a ID do cliente para a conexão nomeada **AzureRunAsConnection**. Essa conexão foi criada ao estabelecer sua instância de automação do Azure. Para localizar essa ID, selecione **ativos** da sua instância de automação do Azure. A partir daí, selecione **conexões**e, em seguida, selecione o **AzureRunAsConnection** entidade de serviço. Anote o **ApplicationId** valor.

![ID do cliente da Automação do Azure](./media/keyvault-keyrotation/Azure_Automation_ClientID.png)

Na **ativos**, selecione **módulos**. Selecione **galeria**e, em seguida, procure e importe as versões atualizadas de cada um dos seguintes módulos:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Na elaboração deste artigo, somente os módulos mencionados anteriormente precisaram ser atualizados para o script a seguir. Se o trabalho de automação falhar, confirme que você importou todos os módulos necessários e suas dependências.

Depois que você recuperou a ID do aplicativo para sua conexão de automação do Azure, você deve informar o seu Cofre de chaves que esse aplicativo tem permissão para atualizar os segredos em seu cofre. Use o seguinte comando do PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Em seguida, selecione **Runbooks** em sua instância de automação do Azure e selecione **adicionar Runbook**. Selecione **Criação Rápida**. Nomeie seu runbook e selecione **PowerShell** como o tipo de runbook. Você pode adicionar uma descrição. Por fim, selecione **Criar**.

![Criar runbook](./media/keyvault-keyrotation/Create_Runbook.png)

Cole o seguinte script do PowerShell no painel do editor para o novo runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
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

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureRmKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

No painel do editor, selecione **painel de teste** para testar o script. Depois que o script é executado sem erros, você pode selecionar **publicar**, e, em seguida, você pode aplicar um cronograma ao runbook no painel de configuração.

## <a name="key-vault-auditing-pipeline"></a>Pipeline de auditoria do Cofre de Chaves

Ao configurar um cofre de chaves, você pode ativar a auditoria para coletar logs de solicitações de acesso feitas para o cofre de chaves. Esses logs são armazenados em uma conta de armazenamento do Azure designada e podem ser extraídos, monitorados e analisados. O cenário a seguir usa o Azure functions, aplicativos lógicos do Azure e logs de auditoria do Cofre de chaves para criar um pipeline que envia um email quando um aplicativo que não corresponde à ID do aplicativo do aplicativo web recupera segredos do cofre.

Primeiro, você deve habilitar o log no cofre de chaves. Use os seguintes comandos do PowerShell. (Você pode ver os detalhes completos na [deste artigo sobre key-vault-logging](key-vault-logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Depois que o log estiver habilitado, os logs de auditoria Iniciar que estão sendo armazenados na conta de armazenamento designada. Esses logs contêm eventos sobre como e quando os cofres de chaves são acessados e por quem.

> [!NOTE]
> Você pode acessar as informações de log 10 minutos após a operação do cofre de chaves. Ele geralmente estará disponível mais cedo do que isso.

A próxima etapa é [criar uma fila do Barramento de Serviço do Azure](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Essa fila é onde os logs de auditoria do Cofre de chaves são enviados por push. Quando as mensagens de log de auditoria estão na fila, o aplicativo lógico elas serão recolhidas e age sobre eles. Crie uma instância do barramento de serviço com as seguintes etapas:

1. Crie um namespace do barramento de serviço (se você já tiver um que você deseja usar, pule para a etapa 2).
2. Navegue até a instância do barramento de serviço no portal do Azure e selecione o namespace que você deseja criar a fila.
3. Selecione **criar um recurso** > **Enterprise Integration** > **do barramento de serviço**e, em seguida, insira os detalhes necessários.
4. Localizar as informações de conexão do barramento de serviço selecionando o namespace e, em seguida, selecionando **informações de Conexão**. Você precisará dessas informações para a próxima seção.

Em seguida, [criar uma função do Azure](../azure-functions/functions-create-first-azure-function.md) para sondar os logs do Cofre de chaves na conta de armazenamento e obter novos eventos. Essa função será disparada em uma agenda.

Para criar um aplicativo de funções do Azure, selecione **criar um recurso**, pesquise no marketplace **aplicativo de funções**e, em seguida, selecione **criar**. Durante a criação, você pode usar um plano de hospedagem existente ou criar um novo. Você também pode optar pela hospedagem dinâmica. Para obter mais informações sobre as opções de hospedagem para o Azure Functions, consulte [como dimensionar o Azure Functions](../azure-functions/functions-scale.md).

Depois que o aplicativo de funções do Azure é criado, vá até ele e selecione o **temporizador** cenário e **C\#**  para o idioma. Em seguida, selecione **criar esta função**.

![Folha de Início das Funções do Azure](./media/keyvault-keyrotation/Azure_Functions_Start.png)

Na guia **Desenvolver** , substitua o código de run.csx pelo seguinte:

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

            //Required to order by time as they might not be in the file
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
> Altere as variáveis no código anterior para apontar para sua conta de armazenamento onde os logs do Cofre de chaves são gravados, a instância do barramento de serviço que você criou anteriormente e o caminho específico para os logs de armazenamento do Cofre de chaves.

A função obtém o arquivo de log mais recente da conta de armazenamento em que os logs do cofre de chaves são gravados, capta os eventos mais recentes do arquivo e os envia por push para uma fila do Barramento de Serviço. 

Como um único arquivo pode ter vários eventos, você deve criar um arquivo Sync txt que a função também analisa para determinar o carimbo de data / hora do último evento que foi obtido. Usando esse arquivo garante que você não envie por push o mesmo evento várias vezes. 

O arquivo Sync txt contém um carimbo de hora para o evento encontrado por último. Quando os logs são carregados, eles devem ser classificados com base em seus carimbos de data / hora para garantir que eles são ordenados corretamente.

Para essa função, referenciamos algumas bibliotecas adicionais que não estão disponíveis pré-configuradas no Azure Functions. Para incluir essas bibliotecas, precisamos de Azure Functions para recebê-los usando o NuGet. Sob o **código** caixa, selecione **exibir arquivos**.

![Opção "Exibir arquivos"](./media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Adicione um arquivo chamado Project. JSON com o seguinte conteúdo:

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

Depois de selecionar **salvar**, funções do Azure baixarão os binários necessários.

Mude para a guia **Integrar** e atribua ao parâmetro do temporizador um nome significativo para ser usado na função. No código anterior, a função espera que o temporizador seja chamado *myTimer*. Especifique um [expressão CRON](../app-service/webjobs-create.md#CreateScheduledCRON) do temporizador da seguinte maneira: `0 * * * * *`. Essa expressão fará com que a função seja executada uma vez por minuto.

No mesmo **integrar** guia, adicione uma entrada do tipo **armazenamento de BLOBs do Azure**. Essa entrada irá apontar para o arquivo Sync txt que contém o carimbo de data / hora do último evento analisado pela função. Essa entrada será acessada dentro da função usando o nome do parâmetro. No código anterior, a entrada de armazenamento de BLOBs do Azure espera que o nome do parâmetro seja *inputBlob*. Selecione a conta de armazenamento onde o arquivo Sync estará localizado (ele pode ser o mesmo ou outra conta de armazenamento). No campo caminho, forneça o caminho para o arquivo no formato `{container-name}/path/to/sync.txt`.

Adicionar uma saída do tipo **armazenamento de BLOBs do Azure**. Essa saída irá apontar para o arquivo Sync txt que você definiu na entrada. Esta saída é usada pela função para gravar o carimbo de hora do último evento analisado. O código anterior espera que esse parâmetro seja chamado de *outputBlob*.

A função agora está pronta. Volte para a guia **Desenvolver** e salve o código. Verifique a janela Saída para os erros de compilação e corrigi-los conforme necessário. Se o código é compilado, em seguida, o código agora deve ser verificando os logs do Cofre de chaves a cada minuto e enviar os novos eventos na fila do barramento de serviço definida. Você deve ver as informações de log gravadas na janela de log sempre que a função for disparada.

### <a name="azure-logic-app"></a>Aplicativo lógico do Azure

Em seguida, você deve criar um aplicativo lógico do Azure que obtém os eventos que a função está enviando para a fila do barramento de serviço, analisa o conteúdo e envia um email com base em uma condição que está sendo correspondida.

[Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md) , selecionando **criar um recurso** > **integração** > **aplicativo lógico**.

Depois que o aplicativo lógico é criado, vá até ele e selecione **editar**. No editor do aplicativo lógico, selecione **fila do barramento de serviço** e insira suas credenciais do barramento de serviço para conectar-se à fila.

![Barramento de Serviço de aplicativo lógico do Azure](./media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Selecione **Adicionar uma condição**. Na condição, alterne para o editor avançado e insira o código a seguir. Substitua *APP_ID* com a ID do aplicativo real do seu aplicativo web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

Essa expressão retornará essencialmente **falsos** se o *appid* do evento recebido (que é o corpo da mensagem do barramento de serviço) não é o *appid* do aplicativo.

Agora, crie uma ação sob **se não, não faça NADA**.

![Ação de escolha de aplicativos lógicos do Azure](./media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Para a ação, selecione **Office 365 - enviar email**. Preencha os campos para criar um email para enviar quando a condição definida retornar **false**. Se você não tiver o Office 365, procure alternativas para atingir os mesmos resultados.

Agora você tem um pipeline de ponta a ponta que procura novos logs de auditoria do Cofre de chaves a cada minuto. Ele envia novos logs que encontra para uma fila do barramento de serviço. O aplicativo lógico é disparado quando uma nova mensagem chega na fila. Se o *appid* dentro do evento não corresponde à ID de aplicativo do aplicativo de chamada, ele envia um email.
