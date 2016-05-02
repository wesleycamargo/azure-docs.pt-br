<properties
   pageTitle="Passando credenciais para o Azure usando a DSC | Microsoft Azure"
   description="Visão geral sobre como passar com segurança as credenciais para máquinas virtuais do Azure usando a Configuração de Estado Desejado do PowerShell"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="04/18/2016"
   ms.author="zachal"/>

# Passando credenciais para o manipulador de extensões DSC do Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Este artigo aborda a extensão Configuração de Estado Desejado do Azure. Uma visão geral do manipulador de extensões DSC pode ser encontrada em [Introduction to the Azure Desired State Configuration extension handler (Introdução ao manipulador de extensões Configuração de Estado Desejado do Azure)](virtual-machines-windows-extensions-dsc-overview.md).

Como parte do processo de configuração, talvez seja necessário configurar contas de usuário, acessar serviços ou instalar um programa em um contexto de usuário. Para tudo isso, você precisa fornecer credenciais.

A DSC permite configurações parametrizadas nas quais as credenciais são passadas na configuração e seguramente armazenadas em arquivos MOF. O Manipulador de Extensões do Azure simplifica o gerenciamento de credenciais fornecendo gerenciamento automático de certificados.

Considere o seguinte script de configuração da DSC que cria uma conta de usuário local com a senha especificada:

*user\_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

É importante incluir *node localhost* como parte da configuração. O manipulador de extensões procura especificamente a instrução node localhost e não funcionará sem ela. Também é importante incluir a conversão de tipo *[PsCredential]*, pois esse tipo específico dispara a extensão para criptografar a credencial, conforme descrito abaixo.

Publique esse script no armazenamento de blobs:

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Defina a extensão DSC do Azure e forneça a credencial:

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"
 
$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments
 
$vm | Update-AzureVM
```

Executar esse código solicitará uma credencial. Assim que ela for fornecida, ela será armazenada na memória por pouco tempo. Quando publicada com o cmdlet `Set-AzureVmDscExtension`, ela é transmitida por HTTP à VM, onde o Azure a armazena criptografada no disco, usando o certificado da VM local. Ela é descriptografada na memória por um curto período e criptografada novamente para que seja passada para a DSC.

Isso é diferente de usar configurações seguras sem o manipulador de extensões. O ambiente do Azure oferece uma maneira de transmitir dados de configuração seguramente por certificados, de modo que ao usar o manipulador de extensões DSC, não há necessidade de fornecer uma entrada $CertificatePath ou $CertificateID/$Thumbprint em ConfigurationData.


## Próximas etapas ##

Para saber mais sobre o manipulador de extensões DSC do Azure, confira [Introduction to the Azure Desired State Configuration extension handler (Introdução ao manipulador de extensões Configuração de Estado Desejado do Azure)](virtual-machines-windows-extensions-dsc-overview.md).

Para saber mais sobre a DSC do PowerShell, [visite o centro de documentos do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).

Para encontrar a funcionalidade adicional que você pode gerenciar com a DSC do PowerShell, [navegue na galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) para conhecer mais recursos da DSC.

<!---HONumber=AcomDC_0420_2016-->