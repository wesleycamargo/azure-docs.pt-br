---
title: Ativos de certificado na Automação do Azure
description: Os certificados podem ser armazenados com segurança na Automação do Azure para que possam ser acessados pelos runbooks ou pelas configurações DSC para serem autenticados em recursos do Azure e de terceiros.  Este artigo explica os detalhes de certificados e como trabalhar com elas na criação textual e gráfica.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d91a7ba8d2c05e2d24738764438ce91325743699
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330850"
---
# <a name="certificate-assets-in-azure-automation"></a>Ativos de certificado na Automação do Azure

Os certificados podem ser armazenados com segurança na Automação do Azure para que possam ser acessados pelos runbooks ou pelas configurações DSC usando a atividade **Get-AzureRmAutomationCertificate** para recursos do Azure Resource Manager. Esse recurso permite criar runbooks e configurações DSC que usam certificados para autenticação ou adicioná-los a recursos do Azure ou de terceiros.

>[!NOTE]
>Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é armazenada em um Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a chave é carregada do Key Vault e usada para criptografar o ativo. Esse processo é gerenciado pela Automação do Azure.

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets do AzureRM PowerShell
Para o AzureRM, os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell. Eles são fornecidos como parte do [módulo de AzureRM.Automation](/powershell/azure/overview) que está disponível para uso em runbooks de automação e na configuração de DSC.

|Cmdlets|DESCRIÇÃO|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Obtém informações sobre um certificado a ser usado em um runbook ou configuração DSC. Você só pode recuperar o certificado propriamente dito da atividade Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Cria um novo certificado para a Automação do Azure.|
[Remove-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Remove um certificado da Automação do Azure.|Cria um novo certificado para a Automação do Azure.
|[Set-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Define as propriedades para um certificado existente, incluindo o carregamento do arquivo de certificado e a definição da senha para um. pfx.|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|Carrega um certificado de serviço para o serviço de nuvem especificado.|

## <a name="activities"></a>Atividades
As atividades na tabela a seguir são usadas para acessar certificados em um runbook e em configurações de DSC.

| Atividades | DESCRIÇÃO |
|:---|:---|
|Get-AutomationCertificate|Obtém um certificado a ser usado em um runbook ou configuração DSC. Retorna um objeto [System.Security.Cryptography.X509Certificates.X509Certificate2](https://msdn.microsoft.com/library/system.security.cryptography.x509certificates.x509certificate2.aspx).|

> [!NOTE] 
> Evite usar variáveis no parâmetro –Name de **Get-AutomationCertificate** em um runbook ou em uma configuração de DSC, pois isso complica a descoberta de dependências entre runbooks ou configurações de DSC e variáveis da Automação no tempo de design.

## <a name="python2-functions"></a>Funções Python2

A função na tabela a seguir é usada para acessar certificados em um runbook Python2.

| Função | DESCRIÇÃO |
|:---|:---|
| automationassets.get_automation_certificate | Recupera informações sobre um ativo de certificado. |

> [!NOTE]
> É necessário importar o módulo **automationassets** no início do runbook Python para acessar as funções do ativo.

## <a name="creating-a-new-certificate"></a>Criando um novo certificado

Ao criar um novo certificado, você carrega um arquivo .cer ou .pfx na Automação do Azure. Se marcar certificado como exportável, você poderá transferi-lo do repositório de certificados da Automação do Azure. Se ele não for exportável, ele só poderá ser usado para a assinatura no runbook ou na configuração DSC. A Automação do Azure exige que o certificado tenha o provedor: **Microsoft Enhanced RSA e AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Para criar um novo certificado com o portal do Azure

1. Na sua conta de automação, clique no bloco **Ativos** para abrir a folha **Ativos**.
1. Clique no bloco **Certificados** para abrir a folha **Certificados**.
1. Clique em **Adicionar um certificado** na parte superior da folha.
1. Digite um nome para o certificado na caixa **Nome** .
1. Para procurar um arquivo .cer ou .pfx., clique em **Selecionar um arquivo** em **Carregar um arquivo de certificado**. Se você selecionar um arquivo .pfx, especifique uma senha e a possibilidade de exportação.
1. Clique em **Criar** para salvar o novo ativo de certificado.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Para criar um novo certificado com o Windows PowerShell

O exemplo a seguir mostra como criar um novo Certificado de automação e marcá-lo como exportável. Isso importa um arquivo .pfx existente.

```powershell-interactive
$certName = 'MyCertificate'
$certPath = '.\MyCert.pfx'
$certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup
```

## <a name="using-a-certificate"></a>Usando um certificado

Para usar um certificado, use a atividade **Get-AutomationCertificate**. Não é possível usar o cmdlet [Get-AzureRmAutomationCertificate](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationcertificate?view=azurermps-6.6.0), pois ele retorna informações sobre o ativo de certificado, mas não sobre o próprio certificado.

### <a name="textual-runbook-sample"></a>Exemplo de runbook textual

O código de exemplo a seguir mostra como adicionar um certificado a um serviço de nuvem em um runbook. Nesse exemplo, a senha é recuperada de uma variável de automação criptografada.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Exemplo de runbook gráfico

Você adiciona **Get-AutomationCertificate** a um runbook gráfico clicando com o botão direito no certificado no painel Biblioteca do editor gráfico e selecionando **Adicionar à tela**.

![Adicionar certificado à tela](media/automation-certificates/automation-certificate-add-to-canvas.png)

A imagem a seguir mostra um exemplo do uso de um certificado em um runbook gráfico. Isso é o mesmo que foi mostrado no exemplo acima para adicionar um certificado a um serviço de nuvem de um runbook textual.

![Exemplo de criação gráfica ](media/automation-certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Exemplo de Python2
O exemplo a seguir mostra como acessar certificados em runbooks do Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert 
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como trabalhar com links para controlar o fluxo lógico de atividades que o seu runbook é projetado para realizar, consulte [Links na criação gráfica](automation-graphical-authoring-intro.md#links-and-workflow). 
