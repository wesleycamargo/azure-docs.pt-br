---
title: "Compilando as configurações no DSC de Automação do Azure | Microsoft Docs"
description: "Este artigo descreve como compilar as configurações da DSC (Configuração de Estado Desejado) para Automação do Azure."
services: automation
documentationcenter: na
author: georgewallace
manager: carmonm
ms.assetid: 49f20b31-4fa5-4712-b1c7-8f4409f1aecc
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 02/07/2017
ms.author: magoedte; gwallace
ms.openlocfilehash: 96702fb1b377861c3692358a5754e73475cee84d
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="compiling-configurations-in-azure-automation-dsc"></a>Compilando configurações no DSC de Automação do Azure

Você pode compilar as configurações DSC (Configuração de Estado Desejado) de duas maneiras com a Automação do Azure: no Portal do Azure e com o Windows PowerShell. A seguinte tabela ajuda você a determinar quando usar qual método com base nas características de cada um:

### <a name="azure-portal"></a>Portal do Azure

* Método mais simples com interface do usuário interativo
* Formulário para fornecer valores de parâmetro simples
* Acompanhar facilmente o estado do trabalho
* Acesso autenticado com o logon do Azure

### <a name="windows-powershell"></a>Windows PowerShell

* Chame da linha de comando com os cmdlets do Windows PowerShell
* Pode ser incluído em uma solução automatizada com várias etapas
* Fornece valores de parâmetro simples e complexos.
* Acompanha o estado do trabalho
* É necessário um cliente para dar suporte a cmdlets do PowerShell
* Transmitir ConfigurationData
* Compilar configurações que usam credenciais

Depois de decidir sobre um método de compilação, é possível seguir os respectivos procedimentos abaixo para iniciar a compilação.

## <a name="compiling-a-dsc-configuration-with-the-azure-portal"></a>Compilando uma Configuração DSC com o portal do Azure

1. Em sua conta de Automação, clique em **Configurações DSC**.
2. Clique em uma configuração para abrir sua folha.
3. Clique em **Compilar**.
4. Se a configuração não tiver parâmetros, você será solicitado a confirmar se deseja compilá-la. Se a configuração tiver parâmetros, a folha **Compilar Configuração** será aberta para que você possa fornecer os valores de parâmetro. Veja a seção [**Parâmetros básicos**](#basic-parameters) abaixo para obter mais detalhes sobre os parâmetros.
5. A folha **Trabalho de Compilação** é aberta para que você possa acompanhar o status do trabalho de compilação e as configurações de nó (documentos de configuração do MOF) que ele colocou no Servidor de Recepção de DSC de Automação do Azure.

## <a name="compiling-a-dsc-configuration-with-windows-powershell"></a>Compilando uma Configuração DSC com o Windows PowerShell

Você pode usar [`Start-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/start-azurermautomationdsccompilationjob) para iniciar a compilação com o Windows PowerShell. O código de exemplo a seguir inicia a compilação de uma configuração DSC chamada **SampleConfig**.

```powershell
Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
```

`Start-AzureRmAutomationDscCompilationJob` retorna um objeto de trabalho de compilação que você pode usar para acompanhar seu status. Você pode usar esse objeto do trabalho de compilação com [`Get-AzureRmAutomationDscCompilationJob`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjob) para determinar o status do trabalho de compilação e [`Get-AzureRmAutomationDscCompilationJobOutput`](/powershell/module/azurerm.automation/get-azurermautomationdsccompilationjoboutput) para exibir seus fluxos (saída). O código de exemplo a seguir inicia a compilação da configuração **SampleConfig** , aguarda até a sua conclusão e exibe suas transmissões.

```powershell
$CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"

while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)
{
    $CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any
```

## <a name="basic-parameters"></a>Parâmetros básicos
A declaração de parâmetro nas configurações DSC, incluindo tipos de parâmetros e propriedades, funciona da mesma maneira que os runbooks da Automação do Azure. Veja [Iniciando um runbook na Automação do Azure](automation-starting-a-runbook.md) para saber mais sobre os parâmetros de runbook.

O exemplo a seguir usa dois parâmetros denominados **FeatureName** e **IsPresent** para determinar os valores das propriedades na configuração de nó **ParametersExample.sample** gerada durante a compilação.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]

        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = "Present"
    if($IsPresent -eq $false)
    {
        $EnsureString = "Absent"
    }

    Node "sample"
    {
        WindowsFeature ($FeatureName + "Feature")
        {
            Ensure = $EnsureString
            Name = $FeatureName
        }
    }
}
```

É possível compilar as Configurações DSC que usam parâmetros básicos no portal de DSC de Automação do Azure ou com o Azure PowerShell:

### <a name="portal"></a>Portal

No portal, é possível inserir valores de parâmetro depois de clicar em **Compilar**.

![texto alternativo](./media/automation-dsc-compile/DSC_compiling_1.png)

### <a name="powershell"></a>PowerShell

O PowerShell requer parâmetros em uma [tabela de hash](http://technet.microsoft.com/library/hh847780.aspx) em que a chave corresponda ao nome do parâmetro e o valor seja igual ao valor do parâmetro.

```powershell
$Parameters = @{
    "FeatureName" = "Web-Server"
    "IsPresent" = $False
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters
```

Para obter informações sobre como transmitir PSCredentials como parâmetros, confira <a href="#credential-assets">**Ativos de credencial**</a> abaixo.

## <a name="composite-resources"></a>Recursos de composição

**Recursos de Composição** permitem que você use as configurações DSC como recursos aninhados dentro de uma configuração. Isso permite que você aplique várias configurações para um único recurso.  Consulte [recursos de composição: utilizando uma configuração DSC como um recurso](https://docs.microsoft.com/powershell/dsc/authoringresourcecomposite) para saber mais sobre **Recursos de Composição**

> [!NOTE]
> Para que os **Recursos de Composição** sejam compilados corretamente, primeiro você deverá garantir que todos os Recursos DSC que a composição confie sejam instalados primeiro no repositório dos Módulos de Conta de Automação do Azure ou não serão importados corretamente.

Para adicionar um **Recursos de Composição** DSC, você deverá adicionar o módulo de recurso a um arquivo (*.zip). Acesse o repositório de Módulos na sua Conta de Automação do Azure. Em seguida, clique no botão 'Adicionar um Módulo'.

![Adicionar módulo](./media/automation-dsc-compile/add_module.png)

Navegue até o diretório onde seu arquivo está localizado. Selecione o arquivo e clique em OK.

![Selecione Módulo](./media/automation-dsc-compile/select_dscresource.png)

Em seguida, você será levado de volta ao diretório de módulos, onde é possível monitorar o status do seu **Recursos de Composição** enquanto ele desempacota e se registra com Automação do Azure.

![Importar Recursos de Composição](./media/automation-dsc-compile/register_composite_resource.png)

Quando o módulo estiver registrado, você poderá clicar nele para validar que os **Recursos de Composição** estão disponíveis para serem utilizados em uma configuração.

![Validar Recursos de Composição](./media/automation-dsc-compile/validate_composite_resource.png)

Então, será possível chamar os **Recursos de Composição** na sua configuração, desse modo:

```powershell

    Node ($AllNodes.Where{$_.Role -eq "WebServer"}).NodeName
    {
            
            JoinDomain DomainJoin
            {
                DomainName = $DomainName
                Admincreds = $Admincreds
            }

            PSWAWebServer InstallPSWAWebServer
            {
                DependsOn = "[JoinDomain]DomainJoin"
            }        
    }

```

## <a name="configurationdata"></a>ConfigurationData
**ConfigurationData** permite que você separe a configuração estrutural de qualquer configuração específica do ambiente durante o uso da DSC do PowerShell. Veja [Separar “O que” de “Onde” na DSC do PowerShell](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) para saber mais sobre **ConfigurationData**.

> [!NOTE]
> É possível usar **ConfigurationData** durante a compilação na DSC de Automação do Azure usando o Azure PowerShell, mas não no portal do Azure.

A seguinte configuração de exemplo DSC usa **ConfigurationData** por meio das palavras-chave **$ConfigurationData** e **$AllNodes**. Você também precisará do módulo [**xWebAdministration** para](https://www.powershellgallery.com/packages/xWebAdministration/) este exemplo:

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq "WebServer"}.NodeName
    {
        xWebsite Site
        {
            Name = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure   = "Present"
        }
    }
}
```

Você pode compilar a configuração da DSC acima com o PowerShell. O PowerShell abaixo adiciona duas configurações de nó ao Servidor de Pull da DSC de Automação do Azure: **ConfigurationDataSample.MyVM1** e **ConfigurationDataSample.MyVM3**:

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "MyVM1"
            Role = "WebServer"
        },
        @{
            NodeName = "MyVM2"
            Role = "SQLServer"
        },
        @{
            NodeName = "MyVM3"
            Role = "WebServer"
        }
    )

    NonNodeData = @{
        SomeMessage = "I love Azure Automation DSC!"
    }
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ConfigurationDataSample" -ConfigurationData $ConfigData
```

## <a name="assets"></a>Ativos

As referências de ativos são as mesmas em runbooks e configurações de DSC de Automação do Azure. Veja o seguinte para obter mais informações:

* [Certificados](automation-certificates.md)
* [Conexões](automation-connections.md)
* [Credenciais](automation-credentials.md)
* [Variáveis](automation-variables.md)

### <a name="credential-assets"></a>Ativos de credencial

Embora as Configurações DSC na Automação do Azure possam fazer referência aos ativos de credencial usando **Get-AzureRmAutomationCredential**, os ativos de credencial também podem ser transmitidos por meio de parâmetros, se desejado. Se uma configuração aceitar um parâmetro do tipo **PSCredential** , você precisará transmitir o nome da cadeia de caracteres de um ativo de credencial da Automação do Azure como o valor desse parâmetro, em vez de um objeto do PSCredential. Em segundo plano, o ativo de credencial da Automação do Azure com esse nome será recuperado e transmitido para a configuração.

Manter as credenciais seguras em configurações de nó (documentos de configuração do MOF) requer a criptografia das credenciais no arquivo MOF da configuração de nó. A Automação do Azure vai além e criptografa todo o arquivo MOF. No entanto, atualmente, você deve informar a DSC do PowerShell que as credenciais podem ser exportadas em texto sem formatação durante a geração do MOF da configuração de nó, pois a DSC do PowerShell não está ciente de que a Automação do Azure criptografará todo o arquivo MOF após sua geração por meio de um trabalho de compilação.

Você pode informar à DSC do PowerShell que não há problema nas credenciais serem exportadas em texto sem formatação nos MOFs gerados da configuração de nó usando [**ConfigurationData**](#configurationdata). Você deve transmitir `PSDscAllowPlainTextPassword = $true` por meio do **ConfigurationData** para o nome do bloco de cada nó que aparece na configuração DSC e que usa credenciais.

O exemplo a seguir mostra uma configuração DSC que usa um ativo de credencial da Automação.

```powershell
Configuration CredentialSample
{
    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -AutomationAccountName "AutomationAcct" -Name "SomeCredentialAsset"

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath = "\\Server\share\path\file.ext"
            DestinationPath = "C:\destinationPath"
            Credential = $Cred
        }
    }
}
```

Você pode compilar a configuração da DSC acima com o PowerShell. O PowerShell abaixo adiciona duas configurações de nó ao servidor de Pull da DSC de Automação do Azure: **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = "*"
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = "MyVM1"
        },
        @{
            NodeName = "MyVM2"
        }
    )
}

Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "CredentialSample" -ConfigurationData $ConfigData
```

## <a name="importing-node-configurations"></a>Como importar configurações de nó

Você também pode importar configurações de nó (MOFs) que você compilou fora do Azure. Uma vantagem disso é que as configurações dos nós podem ser assinadas.
Uma configuração de nó assinada é verificada localmente em um nó gerenciado pelo agente de DSC, garantindo que a configuração aplicada ao nó seja proveniente de uma origem autorizada.

> [!NOTE]
> Você pode importar configurações assinadas em sua conta de Automação do Azure, mas a Automação do Azure não oferece suporte à compilação de configurações assinadas.

> [!NOTE]
> Um arquivo de configuração de nó não deve ultrapassar 1 MB, para poder ser importado na Automação do Azure.

Saiba mais sobre como assinar as configurações de nó em https://msdn.microsoft.com/pt-br/powershell/wmf/5.1/dsc-improvements#how-to-sign-configuration-and-module.

### <a name="importing-a-node-configuration-in-the-azure-portal"></a>Importar uma configuração de nó no Portal do Azure

1. Em sua conta de Automação, clique em **Configurações do nó DSC**.

    ![Configurações de nó DSC](./media/automation-dsc-compile/node-config.png)
2. Na folha **Configurações de nó DSC**, clique em **Adicionar uma Configuração de Nó**.
3. Na folha **Importar**, clique no ícone de pasta próximo à caixa de texto **Arquivo de Configuração de Nó** para procurar um arquivo de configuração de nó (MOF) no computador local.

    ![Procurar arquivo local](./media/automation-dsc-compile/import-browse.png)
4. Insira um nome na caixa de texto **Nome da Configuração**. Esse nome deve corresponder ao nome da configuração a partir da qual a configuração de nó foi compilada.
5. Clique em **OK**.

### <a name="importing-a-node-configuration-with-powershell"></a>Importar uma configuração de nó com o PowerShell

Use o cmdlet [Import-AzureRmAutomationDscNodeConfiguration](/powershell/module/azurerm.automation/import-azurermautomationdscnodeconfiguration) para importar uma configuração de nó para a conta de automação.

```powershell
Import-AzureRmAutomationDscNodeConfiguration -AutomationAccountName "MyAutomationAccount" -ResourceGroupName "MyResourceGroup" -ConfigurationName "MyNodeConfiguration" -Path "C:\MyConfigurations\TestVM1.mof"
```



