<properties 
   pageTitle="Ativos de credenciais na Automação do Azure"
   description="Ativos de credenciais na Automação do Azure contêm credenciais de segurança que podem ser usadas para autenticação para recursos acessados pelo runbook. Este artigo descreve como criar ativos de credenciais e usá-los em um runbook."
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

# Ativos de credenciais na Automação do Azure

Um ativo de credencial de Automação contém um objeto [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) que contém credenciais de segurança, como um nome de usuário e uma senha. Runbooks podem usar cmdlets que aceite um objeto PSCredential para autenticação ou o runbook pode extrair o nome de usuário e a senha do objeto PSCredential para fornecer a algum aplicativo ou serviço que exija autenticação. As propriedades de uma credencial são armazenadas com segurança na Automação do Azure e podem ser acessadas no runbook com a atividade [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx).

>[AZURE.NOTE]Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é criptografada por um certificado mestre e armazenada na Automação do Azure. Antes de armazenar um ativo seguro, a chave para a conta de automação é descriptografada usando o certificado mestre e usada para criptografar o ativo.

## Cmdlets do Windows PowerShell

Os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo PowerShell do Azure](../powershell-install-configure.md) que está disponível para uso em runbooks de Automação.

|Cmdlets|Descrição|
|:---|:---|
|[Get-AzureAutomationCredential](http://msdn.microsoft.com/library/dn913781.aspx)|Recupera informações sobre um ativo de credencial. Você só pode recuperar a credencial propriamente dita da atividade **Get-AutomationCredential**.|
|[New-AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|Cria uma nova credencial de Automação.|
|[Remove- AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|Remove uma credencial de Automação.|
|[Set- AzureAutomationCredential](http://msdn.microsoft.com/library/azure/jj554330.aspx)|Define as propriedades de uma credencial de Automação existente.|

## Atividades de runbook

As atividades na tabela a seguir são usadas para acessar credenciais em um runbook.

|Atividades|Descrição|
|:---|:---|
|Get-AutomationPSCredential|Obtém uma credencial a ser usada em um runbook. Retorna um objeto [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential).|

>[AZURE.NOTE]Evite usar variáveis no parâmetro –Name de Get-AutomationPSCredential, pois isso pode complicar a descoberta de dependências entre runbooks e ativos de credenciais no momento do design.

## Criando uma nova credencial


### Para criar uma nova variável com o portal do Azure

1. Em sua conta de automação, clique em **Ativos** na parte superior da janela.
1. Clique em **Adicionar Configuração** na parte inferior da janela.
1. Clique em **Adicionar Credencial**.
2. No menu suspenso **Tipo de Credencial**, selecione **Credencial do PowerShell**.
1. Conclua o assistente e clique na caixa de seleção para salvar a nova credencial.


### Para criar uma nova credencial com o portal de visualização do Azure

1. Em sua conta de automação, clique na parte **Ativos** para abrir a folha **Ativos**.
1. Clique na parte **Credenciais** para abrir a folha **Credenciais**.
1. Clique em **Adicionar uma credencial** na parte superior da folha.
1. Preencha o formulário e clique em **Criar** para salvar a nova credencial.


### Para criar uma nova credencial do PowerShell com o Windows PowerShell

Os comandos de exemplo a seguir mostram como criar uma nova credencial de automação. Um objeto PSCredential é criado pela primeira vez com o nome e a senha e, em seguida, é usado para criar o ativo de credencial. Como alternativa, você pode usar o cmdlet **Get-Credential** para ser solicitado a digitar um nome e uma senha.

	$user = "MyDomain\MyUser"
	$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
	$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
	New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred

## Usando uma credencial do PowerShell em um runbook

Você recupera um ativo de credencial em um runbook com a atividade **Get-AutomationPSCredential**. Isso retorna um [objeto PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) que você pode usar com uma atividade ou cmdlet que exija um parâmetro PSCredential. Você também pode recuperar as propriedades do objeto de credencial para usar individualmente. O objeto tem uma propriedade para o nome de usuário e a senha segura, ou você pode usar o método **GetNetworkCredential** para retornar um objeto [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) que fornecerá uma versão sem segurança da senha.

### Exemplo de runbook textual

Os comandos de exemplo a seguir mostram como usar uma credencial do PowerShell em um runbook. Neste exemplo, a credencial é recuperada e seu nome de usuário e senha são atribuídos a variáveis.

	$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
	$userName = $myCredential.UserName
	$securePassword = $myCredential.Password
	$password = $myCredential.GetNetworkCredential().Password


### Exemplo de runbook gráfico

Você adiciona uma atividade **Get-AutomationPSCredential** a um runbook gráfico clicando com o botão direito do mouse na credencial no painel Biblioteca do editor gráfico e selecionando **Adicionar à tela**.


![Adicionar a credencial à tela](media/automation-credentials/credential-add-canvas.png)

A imagem a seguir mostra um exemplo do uso de uma credencial em um runbook gráfico. Nesse caso, ela está sendo usada para fornecer autenticação para um runbook para os recursos do Azure, conforme descrito em [Configurando a autenticação para recursos do Azure](#automation-configuring.md). A primeira atividade recupera a credencial que tem acesso à assinatura do Azure. A atividade **Add-AzureAccount** usa essa credencial para fornecer autenticação para qualquer atividade que vier depois dela. Um [link de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) está disponível, já que **Get-AutomationPSCredential** está esperando um único objeto.

![Adicionar a credencial à tela](media/automation-credentials/get-credential.png)


## Artigos relacionados

- [Links na criação gráfica](automation-graphical-authoring-intro.md#links-and-workflow)

 

<!---HONumber=62-->