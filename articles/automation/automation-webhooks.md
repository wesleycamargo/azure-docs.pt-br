<properties 
   pageTitle="Webhooks da Automação do Azure"
   description="Um webhook que permite que um cliente inicie um runbook na Automação do Azure a partir de uma chamada HTTP. Este artigo descreve como criar um webhook e como chamar um para iniciar um runbook."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/03/2015"
   ms.author="bwren" />

# Webhooks da Automação do Azure

O *webhook* permite que você inicie um runbook específico na Automação do Azure por meio de uma única solicitação HTTP. Isso permite que os serviços externos, como Visual Studio Online, GitHub ou aplicativos personalizados iniciem runbooks sem implementar uma solução completa usando a API da Automação do Azure.

![Webhooks](media/automation-webhooks/webhooks-overview.png)

Você pode comparar os webhooks a outros métodos para iniciar um runbook em [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md)

## Detalhes de um webhook

A tabela a seguir descreve as propriedades que devem ser configuradas para um webhook.

| Propriedade | Descrição |
|:---|:---|
|Nome | Você pode fornecer qualquer nome que desejar para um webhook já que o mesmo não aparece para o cliente. Ele é usado apenas por você para identificar o runbook na Automação do Azure. <br> Como prática recomendada, você deve atribuir ao webhook um nome relacionado ao cliente que irá usá-lo. |
|URL |A URL do webhook é o endereço exclusivo que um cliente chama um HTTP POST para iniciar o runbook vinculado ao webhook. Ele é gerado automaticamente quando você cria o webhook. Você não pode especificar uma URL personalizada. <br> <br> A URL contém um token de segurança que permite que o runbook seja invocado por um sistema de terceiros sem autenticação adicional. Por esse motivo, ele deve ser tratado como uma senha. Por motivos de segurança, você pode exibir apenas a URL no portal de visualização do Azure no momento em que o webhook é criado. Você deve anotar a URL em um local seguro para uso futuro. |
|Data de validade | Como um certificado, cada webhook tem uma data de validade após a qual ele não pode mais ser usado. A data de validade não pode ser alterada depois que o webhook é criado e o webhook também não pode ser habilitado novamente depois que a data de validade é atingida. Nesse caso, você deve criar outro webhook para substituir o atual e informar ao cliente que ele deve usar o novo webhook. |
| Habilitado | Um webhook é habilitado por padrão quando ele é criado. Se você defini-lo como Desabilitado, nenhum cliente poderá usá-lo. Você pode definir a propriedade **Habilitado** quando você cria o webhook ou a qualquer momento quando ele é criado. |


### Parâmetros
Um webhook pode definir valores para parâmetros de runbook que são usados quando o runbook é iniciado por esse webhook. O webhook deve incluir valores de quaisquer parâmetros obrigatórios do runbook e pode incluir valores de parâmetros opcionais. Vários webhooks vinculados a um único runbook podem usar valores de parâmetros diferentes.

>[AZURE.NOTE]Os valores de parâmetro definidos por um webhook ativo não podem ser alterados depois que o webhook é criado. Você deve criar outro webhook que usa valores de parâmetros diferentes.

Quando um cliente inicia um runbook usando um webhook, ele não pode substituir os valores de parâmetro definidos no webhook. Para receber dados do cliente, o runbook pode aceitar um parâmetro único chamado **$WebhookData** do tipo [objeto] que conterá os dados que o cliente inclui na solicitação POST.

![Webhookdata](media/automation-webhooks/webhookdata.png)

O objeto **$WebhookData** terá as seguintes propriedades:

| Propriedade | Descrição |
|:--- |:---|
| WebhookName | O nome do webhook. |
| RequestHeader | Tabela de hash contendo os cabeçalhos da solicitação de POST de entrada. |
| RequestBody | O corpo da solicitação de POST de entrada. Isso manterá qualquer formatação, como a cadeia de caracteres, JSON, XML ou os dados codificados de formulário. O runbook deve ser escrito para trabalhar com o formato de dados esperado.|


Nenhuma configuração do webhook é necessária para suporte ao parâmetro **$WebhookData** e o runbook não é necessário para aceitá-lo. Se o runbook não definir o parâmetro, os detalhes da solicitação enviada pelo cliente são ignorados.

Se você especificar um valor para $WebhookData quando você cria o webhook, esse valor será substituído quando o webhook inicia o runbook com os dados da solicitação POST do cliente, mesmo que o cliente não inclua todos os dados no corpo da solicitação. Se você iniciar um runbook que tenha $WebhookData usando um método que não seja um webhook, você pode fornecer um valor para $Webhookdata que será reconhecidos pelo runbook. Esse valor deve ser um objeto com as mesmas propriedades de $Webhookdata para que o runbook possa trabalhar corretamente com ele.

>[AZURE.NOTE]Os valores de todos os parâmetros de entrada são registrados com o trabalho de runbook. Isso significa que qualquer entrada fornecida pelo cliente na solicitação webhook será conectada e estará disponível para qualquer pessoa com acesso ao trabalho de automação. Por esse motivo, você deve ter cuidado ao incluir informações confidenciais em chamadas webhook.

## Segurança

A segurança de um webhook conta com a privacidade da sua URL que contém um token de segurança que permite que ele seja invocado. A Automação do Azure não executa nenhuma autenticação na solicitação desde que ela seja feita para a URL correta. Por esse motivo, os webhooks não devem ser usados para runbooks que executam funções altamente confidenciais sem usar um meio alternativo de validar a solicitação.

Você pode incluir a lógica no runbook para determinar o que foi chamado por um webhook verificando a propriedade **WebhookName** do parâmetro $WebhookData. O runbook pode executar uma validação adicional procurando por informações específicas nas propriedades **RequestHeader** ou **RequestBody**.

Outra estratégia é o runbook realizar alguma validação de uma condição externa quando ele receber uma solicitação webhook. Por exemplo, considere um runbook que é chamado pelo GitHub sempre que houver uma nova confirmação para um repositório GitHub. O runbook pode se conectar ao GitHub para validar que uma nova confirmação realmente ocorreu antes de continuar.

## Criando um webhook

Use o procedimento a seguir para criar um novo webhook vinculado a um runbook no portal de visualização do Azure.

1. Na **folha de Runbooks** no portal de visualização do Azure, clique no runbook que o webhook começará a ver a sua folha de detalhes. 
3. Clique em **Webhook** na parte superior da folha para abrir a folha **Adicionar webhook**. <br> ![Botão Webhooks](media/automation-webhooks/webhooks-button.png)
4. Clique em **Criar novo webhook** para abrir a **folha Criar webhook**.
5. Especifique um **Nome**, **Data de validade** para o webhook e se ele deve ser habilitado. Consulte [Detalhes de um webhook](#details-of-a-webhook) para obter mais informações sobre essas propriedades.
6. Clique no ícone copiar e pressione Ctrl + C para copiar a URL do webhook. Em seguida, anote-o em um local seguro. **Quando você cria o webhook, não é possível recuperar a URL novamente.** <br> ![URL de Webhook](media/automation-webhooks/copy-webhook-url.png)
3. Clique em **Parâmetros** para fornecer valores para os parâmetros do runbook. Se o runbook tiver parâmetros obrigatórios, não será possível criar o webhook, a menos que os valores sejam fornecidos.
1. Clique em **Criar** para criar o webhook.


## Usando um webhook

Para usar um webhook após ele ter sido criado, o aplicativo cliente deve emitir um POST HTTP com a URL para o webhook. A sintaxe do webhook será no formato a seguir.

	http://<Webhook Server>/token?=<Token Value>

O cliente receberá um dos seguintes códigos de retorno da solicitação POST.

| Código | Texto | Descrição |
|:---|:----|:---|
| 202 | Aceita | A solicitação foi aceita e o runbook foi enfileirado com êxito. |
| 400 | Solicitação incorreta | A solicitação não foi aceita por um dos motivos a seguir. <ul> <li>O webhook expirou.</li> <li>O webhook está desabilitado.</li> <li>O token na URL é inválido.</li> </ul>|
| 500 | Erro interno do servidor | A URL era válida, mas ocorreu um erro. Envie a solicitação novamente. |

Supondo que a solicitação seja bem-sucedida, a resposta webhook contém a ID de trabalho no formato JSON da seguinte maneira. Ela conterá uma ID de trabalho única, mas o formato JSON permite possíveis aprimoramentos futuros.

	{"JobIds":["<JobId>"]}  

O cliente não pode determinar o status da conclusão do webhook ou quando o trabalho de runbook é concluído. Ele pode determinar essas informações usando a ID de trabalho com outro método como [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) ou [API de Automação do Azure](https://msdn.microsoft.com/library/azure/mt163826.aspx).

### Exemplo

O exemplo a seguir usa o Windows PowerShell para iniciar um runbook com um webhook. Observe que qualquer linguagem que possa fazer uma solicitação HTTP pode usar um webhook; o Windows PowerShell é usado aqui apenas como um exemplo.

O runbook está esperando uma lista de máquinas virtuais formatadas em JSON no corpo da solicitação. Também incluímos informações sobre quem está iniciando o runbook e a data e a hora que ele está sendo iniciado no cabeçalho da solicitação.

	$uri = "https://s1events.azure-automation.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
	$headers = @{"From"="user@contoso.com";"Date"="05/28/2015 15:47:00"}
    
    $vms  = @([pscustomobject]@{Name="vm01";ServiceName="vm01"})
    $vms += @([pscustomobject]@{Name="vm02";ServiceName="vm02"})
	$body = ConvertTo-Json -InputObject $vms 

	$response = Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body
	$jobid = ConvertFrom-Json $response 


A imagem a seguir mostra as informações de cabeçalho (usando um rastreamento [Fiddler](http://www.telerik.com/fiddler)) desta solicitação. Isso inclui os cabeçalhos padrão de uma solicitação HTTP, além dos cabeçalhos personalizados de Data e Origem que adicionamos. Cada um desses valores está disponível para o runbook na propriedade **RequestHeaders** de **WebhookData**.

![Botão Webhooks](media/automation-webhooks/webhook-request-headers.png)

A imagem a seguir mostra o corpo da solicitação (usando um rastreamento [Fiddler](http://www.telerik.com/fiddler)) que está disponível para o runbook na propriedade **RequestBody** do **WebhookData**. Ela é formatada como JSON, pois esse era o formato que foi incluído no corpo da solicitação.

![Botão Webhooks](media/automation-webhooks/webhook-request-body.png)

A imagem a seguir mostra a solicitação sendo enviada do Windows PowerShell e a resposta resultante. A ID do trabalho é extraída da resposta e convertida em uma cadeia de caracteres.

![Botão Webhooks](media/automation-webhooks/webhook-request-response.png)

O seguinte exemplo de runbook aceita a solicitação do exemplo anterior e inicia as máquinas virtuais especificadas no corpo da solicitação.

	workflow Test-StartVirtualMachinesFromWebhook
	{
		param (	
			[object]$WebhookData
		)

		# If runbook was called from Webhook, WebhookData will not be null.
		if ($WebhookData -ne $null) {	
			
			# Collect properties of WebhookData
			$WebhookName 	= 	$WebhookData.WebhookName
			$WebhookHeaders = 	$WebhookData.RequestHeader
			$WebhookBody 	= 	$WebhookData.RequestBody
			
			# Collect individual headers. VMList converted from JSON.
			$From = $WebhookHeaders.From
			$VMList = (ConvertFrom-Json -InputObject $WebhookBody).VirtualMachines
			Write-Output "Runbook started from webhook $WebhookName by $From."
			
			# Authenticate to Azure resources
			$Cred = Get-AutomationPSCredential -Name 'MyAzureCredential'
			Add-AzureAccount -Credential $Cred
			
            # Start each virtual machine
			foreach ($VM in $VMList)
			{
				Write-Output "Starting $VM.Name."
				Start-AzureVM -Name $VM.Name -ServiceName $VM.ServiceName
			}
		}
		else {
			Write-Error "Runbook mean to be started only from webhook." 
		} 
	}

	

## Artigos relacionados

- [Iniciando um runbook](automation-starting-a-runbook.md)
- [Exibindo o status de um trabalho de runbook](automation-viewing-the-status-of-a-runbook-job.md) 

<!---HONumber=58_postMigration-->