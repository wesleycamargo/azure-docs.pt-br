<properties 
   pageTitle="Ativos de conexão na Automação do Azure"
   description="Ativos de conexão na Automação do Azure contêm as informações necessárias para se conectar a um aplicativo ou serviço externo de um runbook. Este artigo explica os detalhes de conexões e como trabalhar com elas na criação textual e gráfica."
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
   ms.date="05/21/2015"
   ms.author="bwren" />

# Ativos de conexão na Automação do Azure

Um ativo de conexão da Automação contém as informações necessárias para se conectar a um aplicativo ou serviço externo de um runbook. Isso pode incluir as informações necessárias para autenticação, como um nome de usuário e uma senha, além das informações de conexão, como uma URL ou uma porta. O valor de uma conexão é manter todas as propriedades para se conectar a um aplicativo específico em um ativo, em vez de criar diversas variáveis. O usuário pode editar os valores para uma conexão em um único local, e você pode passar o nome de uma conexão a um runbook em um único parâmetro. As propriedades de uma conexão podem ser acessadas no runbook com a atividade **Get-AutomationConnection**.

Ao criar uma conexão, você deve especificar um *tipo de conexão*. O tipo de conexão é um modelo que define um conjunto de propriedades. A conexão define valores para cada propriedade definida em seu tipo de conexão. Os tipos de conexão são adicionados à Automação do Azure nos módulos de integração ou criados com a [API da Automação do Azure](http://msdn.microsoft.com/library/azure/mt163818.aspx). Os únicos tipos de conexão que estão disponíveis quando você cria uma conexão são aqueles instalados em sua conta de automação.

>[AZURE.NOTE]Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é criptografada por um certificado mestre e armazenada na Automação do Azure. Antes de armazenar um ativo seguro, a chave para a conta de automação é descriptografada usando o certificado mestre e usada para criptografar o ativo.

## Cmdlets do Windows PowerShell

Os cmdlets na tabela a seguir são usados para criar e gerenciar conexões da Automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo do PowerShell do Azure](powershell-install-configure.md) que está disponível para uso em runbooks da Automação.

|Cmdlet|Descrição|
|:---|:---|
|[Get-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921828.aspx)|Recupera uma conexão. Inclui uma tabela de hash com os valores dos campos da conexão.|
|[New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx)|Cria uma nova conexão.|
|[Remove-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921827.aspx)|Remove uma conexão existente.|
|[Set-AzureAutomationConnectionFieldValue](http://msdn.microsoft.com/library/dn921826.aspx)|Define o valor de determinado campo para uma conexão existente.|

## Atividades de runbook

As atividades na tabela a seguir são usadas para acessar conexões em um runbook.

|Atividades|Descrição|
|---|---|
|Get-AutomationConnection|Obtém uma conexão para usar em um runbook. Retorna uma tabela de hash com as propriedades da conexão.|

>[AZURE.NOTE]Evite usar variáveis no parâmetro –Name de **Get-AutomationConnection**, pois isso pode complicar a descoberta de dependências entre runbooks e ativos de conexão no momento do design.

## Criando uma nova conexão

### Para criar uma nova conexão com o portal do Azure

1. Em sua conta de automação, clique em **Ativos** na parte superior da janela.
1. Clique em **Adicionar Configuração** na parte inferior da janela.
1. Clique em **Adicionar Conexão**.
2. No menu suspenso **Tipo de Conexão**, selecione o tipo de conexão que você deseja criar. O assistente apresentará as propriedades para esse tipo específico.
1. Conclua o assistente e clique na caixa de seleção para salvar a nova conexão.


### Para criar uma nova conexão com o portal de visualização do Azure

1. Em sua conta de automação, clique na parte **Ativos** para abrir a folha de **Ativos**.
1. Clique na parte **Conexões** para abrir a folha **Conexões**.
1. Clique em **Adicionar uma conexão** na parte superior da folha.
2. No menu suspenso **Tipo**, selecione o tipo de conexão que você deseja criar. O formulário apresentará as propriedades para esse tipo específico.
1. Preencha o formulário e clique em **Criar** para salvar a nova conexão.



### Para criar uma nova conexão com o Windows PowerShell

Criar uma nova conexão com o Windows PowerShell usando o cmdlet [New-AzureAutomationConnection](http://msdn.microsoft.com/library/dn921825.aspx). Esse cmdlet tem um parâmetro chamado **ConnectionFieldValues** que espera uma [tabela de hash](http://technet.microsoft.com/library/hh847780.aspx) definindo valores para cada uma das propriedades definidas pelo tipo de conexão.


Os comandos de exemplo a seguir criam uma nova conexão para o [Twilio](http://www.twilio.com), um serviço de telefonia que permite enviar e receber mensagens de texto. Um módulo de integração de exemplo que inclui um tipo de conexão do Twilio está disponível no [Script Center](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8). Esse tipo de conexão define propriedades para o SID de Conta e o Token de Autorização, que são necessários para validar sua conta durante a conexão com o Twilio. Você deve [baixar este módulo](http://gallery.technet.microsoft.com/scriptcenter/Twilio-PowerShell-Module-8a8bfef8) e instalá-lo em sua conta de automação para que esse código de exemplo funcione.

	$AccountSid = "DAf5fed830c6f8fac3235c5b9d58ed7ac5"
	$AuthToken  = "17d4dadfce74153d5853725143c52fd1"
	$FieldValues = @{"AccountSid" = $AccountSid;"AuthToken"=$AuthToken}

	New-AzureAutomationConnection -AutomationAccountName "MyAutomationAccount" -Name "TwilioConnection" -ConnectionTypeName "Twilio" -ConnectionFieldValues $FieldValues


## Usando uma conexão em um runbook

Você recupera uma conexão em um runbook com o cmdlet **Get-AutomationConnection**. Essa atividade recupera os valores dos diferentes campos na conexão e retorna-os como um [tabela de hash](http://go.microsoft.com/fwlink/?LinkID=324844) que pode ser usada com os comandos apropriados no runbook.

### Exemplo de runbook textual
Os comandos de exemplo a seguir mostram como usar a conexão do Twilio do exemplo anterior para enviar uma mensagem de texto de um runbook. A atividade Send-TwilioSMS usada aqui tem dois conjuntos de parâmetros, e cada um deles usa um método diferente para autenticação no serviço Twilio. Um usa um objeto de conexão e o outro usa parâmetros individuais para o SID de Conta e o Token de Autorização. Ambos os métodos são mostrados neste exemplo.

	$Con = Get-AutomationConnection -Name "TwilioConnection"
	$NumTo = "14255551212"
	$NumFrom = "15625551212"
	$Body = "Text from Azure Automation."

	#Send text with connection object.
	Send-TwilioSMS -Connection $Con -From $NumFrom -To $NumTo -Body $Body

	#Send text with connection properties.
	Send-TwilioSMS -AccountSid $Con.AccountSid -AuthToken $Con.AuthToken $Con -From $NumFrom -To $NumTo -Body $Body

### Exemplos de runbook gráfico

Você adiciona uma atividade **Get-AutomationConnection** a um runbook gráfico clicando com o botão direito do mouse na conexão no painel Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![](media/automation-connections/connection-add-canvas.png)

A imagem a seguir mostra um exemplo do uso de uma conexão em um runbook gráfico. Esse é o mesmo exemplo mostrado acima para enviar uma mensagem de texto usando o Twilio em um runbook textual. Esse exemplo usa o parâmetro **UseConnectionObject** definido para a atividade **Send-TwilioSMS** que usa um objeto de conexão para autenticação no serviço. Um [link de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) é usado aqui, pois o parâmetro Connection está esperando um único objeto.

O motivo pelo qual uma expressão do PowerShell é usada para o valor o parâmetro **To** em vez de um valor Constante é que esse parâmetro espera um tipo de valor de matriz de cadeia de caracteres para que você possa enviar a vários números. Uma expressão de PowerShell permite que você forneça um único valor ou uma matriz.

![](media/automation-connections/get-connection-object.png)

A imagem abaixo mostra o mesmo exemplo mostrado acima, mas usa o conjunto de parâmetros **SpecifyConnectionFields** que espera que os parâmetros AccountSid e AuthToken sejam especificados individualmente, em vez de usar um objeto de conexão para autenticação. Nesse caso, os campos da conexão são especificados em vez do objeto propriamente dito.

![](media/automation-connections/get-connection-properties.png)



## Artigos relacionados

- [Links na criação gráfica](automation-graphical-authoring-intro.md#links-and-workflow)

<!---HONumber=58-->