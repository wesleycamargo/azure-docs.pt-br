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
   ms.date="05/13/2015"
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
| Habilitado | Um runbook é habilitado por padrão quando ele é criado. Se você defini-lo como Desabilitado, nenhum cliente poderá usá-lo. Você pode definir a propriedade **Habilitado** quando você cria o webhook ou a qualquer momento quando ele é criado. |


### Parâmetros
Um webhook pode definir valores para parâmetros de runbook que são usados quando o runbook é iniciado por esse webhook. O webhook deve incluir valores de quaisquer parâmetros obrigatórios do runbook e pode incluir valores de parâmetros opcionais. Vários webhooks vinculados a um único runbook podem usar valores de parâmetros diferentes.

>[AZURE.NOTE]Os valores de parâmetro definidos por um webhook ativo não podem ser alterados depois que o webhook é criado. Você deve criar outro webhook que usa valores de parâmetros diferentes.

Quando um cliente inicia um runbook usando um webhook, ele não pode substituir os valores de parâmetro definidos no webhook. Para receber dados do cliente, o runbook pode aceitar um parâmetro único chamado **$WebhookData** do tipo [objeto] que conterá os dados que o cliente inclui na solicitação POST.

![Webhookdata](media/automation-webhooks/webhookdata.png)

O objeto **$WebhookData** terá as seguintes propriedades:

| Propriedade | Descrição |
|:--- |:---|
| WebhookName | O nome do webhook. |
| RequestHeader | Os cabeçalhos da solicitação de POSTA de entrada. |
| RequestBody | O corpo da solicitação de POST de entrada. |

Nenhuma configuração do webhook é necessária para suporte ao parâmetro **$WebhookData** e o runbook não é necessário para aceitá-lo. Se o runbook não definir o parâmetro, os detalhes da solicitação enviada pelo cliente são ignorados.

Se você especificar um valor para $WebhookData quando você cria o webhook, esse valor será substituído quando o webhook inicia o runbook com os dados da solicitação POST do cliente, mesmo que o cliente não inclua todos os dados no corpo da solicitação. Se você iniciar um runbook que tenha $WebhookData usando um método que não seja um webhook, você pode fornecer um valor para $Webhookdata que será reconhecidos pelo runbook. Esse valor deve ser um objeto com as mesmas propriedades de $Webhookdata para que o runbook possa trabalhar corretamente com ele.

>[AZURE.NOTE]Os valores de todos os parâmetros de entrada são registrados com o trabalho de runbook. Isso significa que qualquer entrada fornecida pelo cliente será registrada com $WebhookData e estará disponível para qualquer pessoa com acesso ao trabalho de automação. Por esse motivo, você deve ter cuidado ao incluir informações confidenciais em chamadas webhook.

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
1. Clique em **Criar** para criar o webhook.
3. Clique em **Parâmetros** para fornecer valores para os parâmetros do runbook. <br>
1. Clique em **OK** quando tiver concluído a configuração de webhook.


## Usando um webhook

Para usar um webhook, o aplicativo cliente deve emitir um HTTP POST com a URL para o webhook. A sintaxe do webhook será no formato a seguir.

	http://<Webhook Server>/token?=<Token Value>


O cliente receberá um dos seguintes códigos em resposta à solicitação de POST.

| Código | Texto | Descrição |
|:---|:----|:---|
| 202 | Aceita | A solicitação foi aceita e o runbook foi iniciado com êxito. |
| 400 | Solicitação incorreta | A solicitação não foi aceita por um dos motivos a seguir. <ul> <li>O webhook expirou.</li> <li>O webhook está desabilitado.</li> <li>O token na URL é inválido.</li> </ul>|
| 500 | Erro interno do servidor | A URL era válida, mas ocorreu um erro. Envie a solicitação novamente. |

O cliente não pode saber quando o trabalho de runbook é concluído ou o status da conclusão de um webhook porque o webhook não retorna um identificador para o trabalho de runbook. Você só pode validar que a solicitação foi enviada com êxito.

### Exemplo

O exemplo a seguir inicia um runbook usando um webhook com o Windows PowerShell. Este exemplo inclui dados no cabeçalho e no corpo que podem ser usados pelo runbook. Observe que qualquer linguagem que possa fazer uma solicitação HTTP pode usar um webhook.

	$uri = "https://oaaswebhookcurrent.cloudapp.net/webhooks?token=8ud0dSrSo%2fvHWpYbklW%3c8s0GrOKJZ9Nr7zqcS%2bIQr4c%3d"
	$headers = @{"header1"="headerval1";"header2"="headerval2"}
	$body = "some request body"

	Invoke-RestMethod -Method Post -Uri $uri -Headers $headers -Body $body

O seguinte exemplo de runbook aceita a solicitação anterior e recupera os dados da webhook.

	workflow Sample-Webhook
	{
		param (	
				[object]$WebhookData
		)
	
		$WebhookName 	= 	$WebhookData.WebhookName
		$WebhookHeaders = 	$WebhookData.RequestHeader
		$WebhookBody 	= 	$WebhookData.RequestBody
	} 

	

## Artigos relacionados

- [Iniciando um runbook](automation-starting-a-runbook.md)
- [Exibindo o status de um trabalho de runbook](automation-viewing-the-status-of-a-runbook-job.md)

<!---HONumber=58-->