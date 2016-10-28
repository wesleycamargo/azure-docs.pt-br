<properties 
   pageTitle="Compilando configurações no DSC de Automação do Azure | Microsoft Azure" 
   description="Visão geral de duas maneiras para compilar as configurações de DSC (Configuração de Estado Desejado): no portal do Azure e com o Windows PowerShell. " 
   services="automation" 
   documentationCenter="na" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na" 
   ms.date="01/25/2016"
   ms.author="coreyp"/>
   
#Compilando configurações no DSC de Automação do Azure#

Você pode compilar as configurações DSC (Configuração de Estado Desejado) de duas maneiras com a Automação do Azure: no portal do Azure e com o Windows PowerShell. A seguinte tabela ajudará você a determinar quando usar qual método com base nas características de cada um:

###Portal de Visualização do Azure###
- Método mais simples com interface do usuário interativo
- Formulário para fornecer valores de parâmetro simples
- Acompanhar facilmente o estado do trabalho
- Acesso autenticado com o logon do Azure

###Windows PowerShell###
- Chame da linha de comando com os cmdlets do Windows PowerShell
- Pode ser incluído em uma solução automatizada com várias etapas
- Fornece valores de parâmetro simples e complexos.
- Acompanha o estado do trabalho
- É necessário um cliente para dar suporte a cmdlets do PowerShell
- Transmitir ConfigurationData
- Compilar configurações que usam credenciais

Depois de decidir sobre um método de compilação, é possível seguir os respectivos procedimentos abaixo para iniciar a compilação.

##Compilando uma Configuração DSC com o portal do Azure##

1.  Na sua conta de automação, clique em **Configurações**.
2.  Clique em uma configuração para abrir sua folha.
3.  Clique em **Compilar**.
4.  Se a configuração não tiver parâmetros, você será solicitado a confirmar se deseja compilá-la. Se a configuração tiver parâmetros, a folha **Compilar Configuração** será aberta para que você possa fornecer os valores de parâmetro. Veja a seção <a href="#basic-parameters">**Parâmetros Básicos**</a> abaixo para obter mais detalhes sobre os parâmetros.
5.  A folha **Trabalho de Compilação** é aberta para que você possa acompanhar o status do trabalho de compilação e as configurações de nó (documentos de configuração do MOF) que ele colocou no Servidor de Recepção de DSC de Automação do Azure.

##Compilando uma Configuração DSC com o Windows PowerShell##

Você pode usar [`Start-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244118.aspx) para iniciar a compilação com o Windows PowerShell. O código de exemplo a seguir inicia a compilação de uma configuração DSC chamada **SampleConfig**.

    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig" 
 
`Start-AzureRmAutomationDscCompilationJob` retorna um objeto de trabalho de compilação que você pode usar para acompanhar seu status. Você pode usar esse objeto de trabalho de compilação com o [`Get-AzureRmAutomationDscCompilationJob`](https://msdn.microsoft.com/library/mt244120.aspx) para determinar o status do trabalho de compilação, e o [`Get-AzureRmAutomationDscCompilationJobOutput`](https://msdn.microsoft.com/library/mt244103.aspx) para exibir suas transmissões (saída). O código de exemplo a seguir inicia a compilação da configuração **SampleConfig**, aguarda até a sua conclusão e exibe suas transmissões.
    
    $CompilationJob = Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "SampleConfig"
    
    while($CompilationJob.EndTime –eq $null -and $CompilationJob.Exception –eq $null)       	
    {
    	$CompilationJob = $CompilationJob | Get-AzureRmAutomationDscCompilationJob
    	Start-Sleep -Seconds 3
    }
    
    $CompilationJob | Get-AzureRmAutomationDscCompilationJobOutput –Stream Any 


##Parâmetros básicos##

A declaração de parâmetro nas configurações DSC, incluindo tipos de parâmetros e propriedades, funciona da mesma maneira que os runbooks da Automação do Azure. Veja [Iniciando um runbook na Automação do Azure](automation-starting-a-runbook.md) para saber mais sobre os parâmetros de runbook.

O exemplo a seguir usa dois parâmetros chamados **FeatureName** e **IsPresent** para determinar os valores das propriedades na configuração de nó **ParametersExample.sample**, gerada durante a compilação.

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

É possível compilar as Configurações DSC que usam parâmetros básicos no portal de DSC de Automação do Azure ou com o Azure PowerShell:

###Portal###

No portal, é possível inserir valores de parâmetro depois de clicar em **Compilar**.

![texto alternativo](./media/automation-dsc-compile/DSC_compiling_1.png)

###PowerShell###

O PowerShell requer parâmetros em uma [tabela de hash](http://technet.microsoft.com/library/hh847780.aspx) em que a chave corresponda ao nome do parâmetro e o valor seja igual ao valor do parâmetro.

    $Parameters = @{
    		"FeatureName" = "Web-Server"
    		"IsPresent" = $False
    }
    
    
    Start-AzureRmAutomationDscCompilationJob -ResourceGroupName "MyResourceGroup" -AutomationAccountName "MyAutomationAccount" -ConfigurationName "ParametersExample" -Parameters $Parameters 
    

Para obter informações sobre como transmitir PSCredentials como parâmetros, confira <a href="#credential-assets">**Ativos de credencial**</a> abaixo.

##ConfigurationData##

**ConfigurationData** permite que você separe a configuração estrutural de qualquer configuração específica do ambiente durante o uso da DSC do PowerShell. Veja [Separar “O que” de “Onde” na DSC do PowerShell](http://blogs.msdn.com/b/powershell/archive/2014/01/09/continuous-deployment-using-dsc-with-minimal-change.aspx) para saber mais sobre **ConfigurationData**.

>[AZURE.NOTE] É possível usar **ConfigurationData** durante a compilação na DSC de Automação do Azure usando o Azure PowerShell, mas não no portal do Azure.

A configuração DSC de exemplo a seguir usa **ConfigurationData** por meio das palavras-chave **$ConfigurationData** e **$AllNodes**. Você também precisará do [módulo **xWebAdministration**](https://www.powershellgallery.com/packages/xWebAdministration/) para este exemplo:

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

Você pode compilar a configuração da DSC acima com o PowerShell. O PowerShell abaixo adiciona duas configurações de nó ao Servidor de Recepção de DSC de Automação do Azure: **ConfigurationDataSample.MyVM1** e **ConfigurationDataSample.MyVM3**:

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


##Ativos##

As referências de ativos são as mesmas em runbooks e configurações de DSC de Automação do Azure. Veja o seguinte para obter mais informações:

- [Certificados](automation-certificates.md)
- [Conexões](automation-connections.md)
- [Credenciais](automation-credentials.md)
- [Variáveis](automation-variables.md)

###Ativos de credencial###
Embora as Configurações DSC na Automação do Azure possam fazer referência aos ativos de credencial usando **Get-AzureRmAutomationCredential**, os ativos de credencial também podem ser transmitidos por meio de parâmetros, se desejado. Se uma configuração aceitar um parâmetro do tipo **PSCredential**, você precisará transmitir o nome da cadeia de caracteres de um ativo de credencial da Automação do Azure como o valor desse parâmetro, em vez de um objeto do PSCredential. Em segundo plano, o ativo de credencial da Automação do Azure com esse nome será recuperado e transmitido para a configuração.

Manter as credenciais seguras em configurações de nó (documentos de configuração do MOF) requer a criptografia das credenciais no arquivo MOF da configuração de nó. A Automação do Azure vai além e criptografa todo o arquivo MOF. No entanto, atualmente, você deve informar a DSC do PowerShell que as credenciais podem ser exportadas em texto sem formatação durante a geração do MOF da configuração de nó, pois a DSC do PowerShell não está ciente de que a Automação do Azure criptografará todo o arquivo MOF após sua geração por meio de um trabalho de compilação.

Você pode informar à DSC do PowerShell que não há problema nas credenciais serem exportadas em texto sem formatação nos MOFs gerados da configuração de nó usando <a href="#configurationdata">**ConfigurationData**</a>. Você deve transmitir `PSDscAllowPlainTextPassword = $true` por meio do **ConfigurationData** para o nome do bloco de cada nó que aparece na configuração DSC e que usa credenciais.

O exemplo a seguir mostra uma configuração DSC que usa um ativo de credencial da Automação.

    Configuration CredentialSample
    {
       $Cred = Get-AzureRmAutomationCredential -Name "SomeCredentialAsset"
    
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

Você pode compilar a configuração da DSC acima com o PowerShell. O PowerShell abaixo adiciona duas configurações de nó ao Servidor de Recepção de DSC de Automação do Azure: **CredentialSample.MyVM1** e **CredentialSample.MyVM2**.


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

<!---HONumber=AcomDC_0921_2016-->