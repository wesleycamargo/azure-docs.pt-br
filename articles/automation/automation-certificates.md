<properties 
   pageTitle="Ativos de certificados na Automação do Azure | Microsoft Azure"
   description="Os certificados podem ser armazenados com segurança na Automação do Azure para que possam ser acessados pelos runbooks ou pelas configurações DSC para serem autenticados em recursos do Azure e de terceiros. Este artigo explica os detalhes de certificados e como trabalhar com elas na criação textual e gráfica."
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
   ms.date="01/27/2016"
   ms.author="bwren" />

# Ativos de certificado na Automação do Azure

Os certificados podem ser armazenados com segurança na Automação do Azure para que possam ser acessados pelos runbooks ou pelas configurações DSC usando a atividade **Get-AutomationCertificate**. Isso permite criar runbooks e configurações DSC que usam certificados para autenticação ou adicioná-los a recursos do Azure ou de terceiros.

>[AZURE.NOTE] Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é criptografada por um certificado mestre e armazenada na Automação do Azure. Antes de armazenar um ativo seguro, a chave para a conta de automação é descriptografada usando o certificado mestre e usada para criptografar o ativo.

## Cmdlets do Windows PowerShell

Os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de certificados de automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo do Azure PowerShell](../powershell-install-configure.md) que está disponível para uso em runbooks e na configuração DSC da Automação.

|Cmdlets|Descrição|
|:---|:---|
|[Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx)|Recupera informações sobre um certificado. Você só pode recuperar o certificado propriamente dito da atividade Get-AutomationCertificate.|
|[New- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913764.aspx)|Importa um novo certificado para a Automação do Azure.|
|[Remove- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913773.aspx)|Remove um certificado da Automação do Azure.|
|[Set- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913763.aspx)|Define as propriedades para um certificado existente, incluindo o carregamento do arquivo de certificado e a definição da senha para um. pfx.|

## Atividades para acessar certificados

As atividades na tabela a seguir são usadas para acessar certificados em um runbook ou em uma configuração DSC.

|Atividades|Descrição|
|:---|:---|
|Get-AutomationCertificate|Obtém um certificado a ser usado em um runbook ou configuração DSC.|

>[AZURE.NOTE] Evite usar variáveis no parâmetro –Name de Get-AutomationCertificate, pois isso pode complicar a descoberta de dependências entre runbooks ou configurações DSC e ativos de certificados no momento do design.

## Criando um novo certificado

Ao criar um novo certificado, você carrega um arquivo .cer ou .pfx na Automação do Azure. Se marcar certificado como exportável, você poderá transferi-lo do repositório de certificados da Automação do Azure. Se ele não for exportável, ele só poderá ser usado para a assinatura no runbook ou na configuração DSC.

### Para criar um novo certificado com o portal clássico do Azure

1. Em sua conta de automação, clique em **Ativos** na parte superior da janela.
1. Clique em **Adicionar Configuração** na parte inferior da janela.
1. Clique em **Adicionar Credencial**.
2. No menu suspenso **Tipo de Credencial**, selecione **Certificado**.
3. Digite um nome para o certificado na caixa **Nome** e clique na seta à direita.
4. Procure um arquivo .cer ou .pfx. Se você selecionar um arquivo .pfx, especifique uma senha e se ele deve ter permissão para ser exportado.
1. Clique na marca de seleção para carregar o arquivo de certificado e salve o novo ativo de certificado.


### Para criar um novo certificado com o portal do Azure

1. Em sua conta de automação, clique na parte **Ativos** para abrir a folha **Ativos**.
1. Clique na parte **Certificados** para abrir a folha **Certificados**.
1. Clique em **Adicionar um certificado** na parte superior da folha.
2. Digite um nome para o certificado na caixa **Nome**.
2. Clique em **Selecionar um arquivo** em **Carregar um arquivo de certificado** para procurar um arquivo .cer ou .pfx. Se você selecionar um arquivo .pfx, especifique uma senha e se ele deve ter permissão para ser exportado.
1. Clique em **Criar** para salvar o novo ativo de certificado.


### Para criar um novo certificado com o Windows PowerShell

Os comandos de exemplo a seguir mostram como criar um novo certificado de automação e marcá-lo como exportável. Isso importa um arquivo .pfx existente.

	$certName = 'MyCertificate'
	$certPath = '.\MyCert.pfx'
	$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
	
	New-AzureAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable

## Usando um certificado

Você deve usar a atividade **Get-AutomationCertificate** para usar um certificado. Não é possível usar o cmdlet [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx), pois ele retorna informações sobre o ativo de certificado, mas não sobre o próprio certificado.

### Exemplo de runbook textual

O código de exemplo a seguir mostra como adicionar um certificado a um serviço de nuvem em um runbook. Nesse exemplo, a senha é recuperada de uma variável de automação criptografada.

	$serviceName = 'MyCloudService'
	$cert = Get-AutomationCertificate -Name 'MyCertificate'
	$certPwd = Get-AutomationVariable –Name 'MyCertPassword'
	Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### Exemplo de runbook gráfico

Você adiciona um **Get-AutomationCertificate** a um runbook gráfico clicando com o botão direito do mouse no certificado no painel Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![](media/automation-certificates/certificate-add-canvas.png)

A imagem a seguir mostra um exemplo do uso de um certificado em um runbook gráfico. Esse é o mesmo exemplo mostrado acima para adicionar um certificado a um serviço de nuvem de um runbook textual.

Esse exemplo usa o parâmetro **UseConnectionObject** definido para a atividade **Send-TwilioSMS** que usa um objeto de conexão para autenticação no serviço. Um [link de pipeline](automation-graphical-authoring-intro.md#links-and-workflow) deve ser usado aqui, pois um link de sequência retornaria uma coleção contendo um único objeto, que o parâmetro Connection não está esperando.

![](media/automation-certificates/add-certificate.png)


## Consulte também

- [Links na criação gráfica](automation-graphical-authoring-intro.md#links-and-workflow) 

<!---HONumber=AcomDC_0204_2016-->