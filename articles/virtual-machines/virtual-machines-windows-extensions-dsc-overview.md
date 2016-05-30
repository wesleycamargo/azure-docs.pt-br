<properties
   pageTitle="Visão geral de configuração de estado desejado para o Azure | Microsoft Azure"
   description="Visão geral para usar o manipulador de extensão do Microsoft Azure para configuração de estado desejado do PowerShell. Incluindo pré-requisitos, arquitetura e cmdlets."
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

# Introdução ao manipulador de extensão de configuração do estado desejado do Azure #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

O agente de VM do Azure e as extensões associadas são parte dos serviços de infraestrutura do Microsoft Azure. Extensões de VM são componentes de software que estendem a funcionalidade de máquina virtual e simplificam várias operações de gerenciamento de VM; por exemplo, a extensão VMAccess pode ser usada para redefinir a senha do administrador ou a extensão Custom Script pode ser usada para executar um script na VM.

Este artigo apresenta a extensão de configuração de estado desejado (DSC) do PowerShell para VMs do Azure como parte do SDK do Azure PowerShell. Você pode usar os novos cmdlets para carregar e aplicar uma DSC do PowerShell em uma VM do Azure habilitada com a extensão de DSC do PowerShell. A extensão de DSC do PowerShell chamará a DSC do PowerShell para aplicar a configuração DSC recebida na VM. Essa funcionalidade também está disponível por meio do portal do Azure.

## Pré-requisitos ##
**Máquina local** Para interagir com a extensão de VM do Azure, você precisará usar o portal do Azure ou o SDK do Azure PowerShell.

**Agente convidado** A VM do Azure que será configurada para a configuração do DSC precisará ser um sistema operacional que suporta o Windows Management Framework (WMF) 4.0 ou 5.0. A lista completa de versões suportadas do sistema operacional pode ser encontrada no histórico de versão da extensão de DSC.

## Termos e conceitos ##
Este guia presume familiaridade com os seguintes conceitos:

Configuração - um documento de configuração DSC.

Nó - um destino para uma configuração de DSC. Neste documento, "nó" sempre fará referência a uma VM do Azure.

Dados de configuração - um arquivo .psd1 contendo dados ambientais para uma configuração

## Visão geral da arquitetura ##

A extensão de DSC do Azure usa a estrutura do Agente de VM do Azure para entregar, aplicar e gerar relatórios sobre configurações da DSC executadas em VMs do Azure. A extensão de DSC espera um arquivo .zip contendo pelo menos um documento de configuração e um conjunto de parâmetros fornecidos por meio do SDK do Azure PowerShell ou do portal do Azure.

Quando a extensão é chamada pela primeira vez, executa um processo de instalação. Esse processo instala uma versão do Windows Management Framework (WMF) conforme definido abaixo:

1. Se o sistema operacional da VM do Azure for o Windows Server 2016, nenhuma ação é executada. O WS 2016 já possui a versão mais recente do PowerShell instalada.
2. Se a propriedade `wmfVersion` for especificada, essa versão do WMF é instalada, a menos que ele não seja compatível com o sistema operacional da VM.
3. Se nenhuma propriedade `wmfVersion` for especificada, a versão mais recente do WMF aplicável é instalada.

A instalação do WMF requer uma reinicialização. Após a reinicialização, a extensão baixa o arquivo. zip especificado na propriedade `modulesUrl`. Se esse local estiver no armazenamento de blobs do Azure, um token SAS pode ser especificado na propriedade `sasToken` para acessar o arquivo. Depois que o arquivo .zip for baixado e descompactado, a função de configuração definida em `configurationFunction` é executada para gerar o arquivo .MOF. Em seguida, a extensão executa `Start-DscConfiguration -Force` no arquivo MOF gerado. A extensão captura a saída e grava de volta para no canal de status do Azure. Desse ponto em diante, o LCM de DSC lida com o monitoramento e correção da maneira normal.

## Cmdlets do PowerShell ##

Os cmdlets do PowerShell podem ser usados com ARM ou ASM para empacotar, publicar e monitorar implantações de extensão de DSC. Os cmdlets listados a seguir são os módulos do ASM, mas "Azure" pode ser substituído por "AzureRm" para usar o modelo ARM. Por exemplo, `Publish-AzureVMDscConfiguration` usará ASM e `Publish-AzureRmVMDscConfiguration` usará ARM.

`Publish-AzureVMDscConfiguration` executará em um arquivo de configuração, verificará os recursos dependentes da DSC e criará um arquivo .zip contendo a configuração e recursos da DSC necessários para aplicar a configuração. Ele pode criar o pacote localmente usando o parâmetro `-ConfigurationArchivePath`. Caso contrário, ele irá publicar o arquivo .zip para o armazenamento de blobs do Azure e irá protegê-lo com um token SAS.

O arquivo .zip criado por esse cmdlet possui o script de configuração .ps1 na raiz da pasta de arquivamento. Os recursos possuem a pasta de módulo colocada na pasta de arquivo morto.

`Set-AzureVMDscExtension` aplicará as configurações necessárias pela extensão de DSC do PowerShell em um objeto de configuração da VM, que pode ser aplicado a uma VM do Azure com `Update-AzureVM`.

`Get-AzureVMDscExtension` recupera o status da extensão de DSC de uma VM específica.

`Get-AzureVMDscExtensionStatus` recupera o status da configuração DSC imposta pelo manipulador de extensão de DSC em uma VM ou grupo de VMs.

`Remove-AzureVMDscExtension` remove o manipulador de extensão de uma determinada máquina virtual. Isso **não** remove a configuração, desinstale o WMF ou altera as configurações aplicadas na máquina virtual. Apenas remove o manipulador de extensão.

**Principais diferenças nos cmdlets do ASM e do ARM**

- Os cmdlets do ARM são síncronos. Os cmdlets do ASM são assíncronos.
- ResourceGroupName, VMName, ArchiveStorageAccountName, Version e Location são os novos parâmetros necessários.
- ArchiveResourceGroupName é um novo parâmetro opcional para ARM. Você pode especificá-lo quando sua conta de armazenamento pertence a um grupo de recursos de diferente daquele onde a máquina virtual é criada.
- ConfigurationArchive é chamado ArchiveBlobName no ARM
- ContainerName é chamado ArchiveContainerName no ARM
- StorageEndpointSuffix é chamado ArchiveStorageEndpointSuffix no ARM
- A opção AutoUpdate foi adicionada para ARM para habilitar a atualização automática do manipulador de extensão para a versão mais recente quando estiver disponível. Observe que isso pode causar potenciais reinicializações na VM quando uma nova versão do WMF é lançada. 


## Funcionalidade do portal do Azure ##
Navegue até uma VM clássica. Em Configurações -> Geral, clique em "Extensões". Um novo painel é criado. Clique em "Adicionar" e selecione DSC do PowerShell.

O portal precisará de uma entrada. **Script ou módulos de configuração**: Este é um campo obrigatório. Requer um arquivo. ps1 contendo um script de configuração ou um arquivo .zip com um script de configuração .ps1 na raiz e todos os recursos dependentes em pastas de módulo dentro do .zip. Ele pode ser criado com o cmdlet `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` incluído no SDK do Azure PowerShell. O arquivo .zip será carregado em seu armazenamento de blobs de usuário protegido por um token SAS.

**Arquivo de configuração PSD1 de dados**: Este é um campo opcional. Se sua configuração requer um arquivo de dados de configuração em .psd1, use este campo para selecioná-lo e carregá-lo para o armazenamento de blobs de usuário, onde eles serão protegidos por um token SAS.
 
**Nome de configuração qualificado por módulo**: os arquivos .ps1 podem ter várias funções de configuração. Insira o nome do script .ps1 de configuração seguido por um '' e o nome da função de configuração. Por exemplo, se o seu script .ps1 tiver o nome "configuration.ps1" e a configuração for "IisInstall", insira: `configuration.ps1\IisInstall`

**Argumentos de configuração**: se a função de configuração leva argumentos, insira-os aqui no formato `argumentName1=value1,argumentName2=value2`. Observe que esso é um formato diferente daquele como argumentos de configuração são aceitos por meio de cmdlets do PowerShell ou modelos de ARM.

## Introdução ##

A extensão de DSC do Azure usa documentos de configuração DSC e impõe os mesmos em VMs do Azure. A seguir está um exemplo simples de uma configuração. Salve localmente como "IisInstall.ps1":

```powershell
configuration IISInstall 
{ 
    node "localhost"
    { 
        WindowsFeature IIS 
        { 
            Ensure = "Present" 
            Name = "Web-Server"                       
        } 
    } 
}
```

As etapas a seguir colocam o script IisInstall.ps1 na VM especificada, executam a configuração e relatam o status.
 
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "demo.ps1.zip" -StorageContext $storageContext -ConfigurationName "runScript" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```

## Registro em log ##

Os logs são colocados em:

C:\\WindowsAzure\\Logs\\Plugins\\Microsoft.Powershell.DSC[Version Number]

## Próximas etapas ##

Para saber mais sobre a DSC do PowerShell, [visite o centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview).

Para encontrar a funcionalidade adicional que você pode gerenciar com a DSC do PowerShell, [navegue na galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) para conhecer mais recursos da DSC.

Para obter detalhes sobre como passar parâmetros confidenciais em configurações, consulte [Gerenciar credenciais com segurança com o manipulador de extensão de DSC](virtual-machines-windows-extensions-dsc-credentials.md).

<!---HONumber=AcomDC_0518_2016-->