---
title: "Visão geral de configuração de estado desejado para o Azure | Microsoft Docs"
description: "Visão geral para usar o manipulador de extensão do Microsoft Azure para configuração de estado desejado do PowerShell. Incluindo pré-requisitos, arquitetura e cmdlets."
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: ed8b5bc54baa3a5abfb596b202f0af58e1b6c74f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introdução ao manipulador de extensão de configuração do estado desejado do Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

O agente de VM do Azure e as extensões associadas são parte dos serviços de infraestrutura do Microsoft Azure.
Extensões de VM são componentes de software que estendem a funcionalidade da VM e simplificam várias operações de gerenciamento de VM.

O caso de uso primário para a Extensão de Configuração de Estado Desejado é para inicializar uma VM para o [serviço DSC de Automação do Azure](../../automation/automation-dsc-overview.md), que fornece [benefícios](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig.md#pull-service), incluindo gerenciamento contínuo de configuração e integração da VM com outras ferramentas operacionais, como o Monitoramento do Azure.

Também é possível usar a Extensão de DSC independentemente do serviço de DSC de Automação do Azure, no entanto, esta é uma ação única que ocorre durante a implantação e não há nenhum relatório contínuo ou gerenciamento da configuração a não ser localmente na VM.

Este artigo contém informações sobre cenários, a Extensão de DSC para integração da Automação do Azure e como usar a Extensão do DSC como uma ferramenta para atribuir configurações a máquinas virtuais usando o SDK do Azure.

## <a name="prerequisites"></a>pré-requisitos

- **Computador local** – Para interagir com a extensão de VM do Azure, você precisa usar o Portal do Azure ou o SDK do Azure PowerShell.
- **Agente convidado** – A VM do Azure que é configurada pela configuração do DSC precisa ter um sistema operacional compatível com Windows Management Framework (WMF) 4.0 ou posterior. A lista completa de versões com suporte do sistema operacional pode ser encontrada na página [Histórico de versões da extensão de DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termos e Conceitos

Este guia presume familiaridade com os seguintes conceitos:

- **Configuração** – um documento de configuração DSC.
- **Nó** – um destino para uma configuração DSC. Neste documento, "nó" sempre faz referência a uma VM do Azure.
- **Dados de configuração** – Um arquivo .psd1 contendo dados ambientais de uma configuração.

## <a name="architectural-overview"></a>Visão geral da arquitetura

A extensão de DSC do Azure usa a estrutura do Agente de VM do Azure para entregar, aplicar e gerar relatórios sobre configurações da DSC executadas em VMs do Azure.
A extensão de DSC aceita um documento de configuração e um conjunto de parâmetros.
Se nenhum arquivo for fornecido, um [script de configuração padrão](###default-configuration-script) é inserido com a extensão que é usada apenas para definir metadados no [Gerenciador de Configurações Locais](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).

Quando a extensão é chamada pela primeira vez, ela instala uma versão do Windows Management Framework (WMF) usando a lógica a seguir:

1. Se o sistema operacional da VM do Azure for o Windows Server 2016, nenhuma ação é executada. O Windows Server 2016 já possui a versão mais recente do PowerShell instalada.
2. Se a propriedade `wmfVersion` for especificada, essa versão do WMF é instalada, a menos que ele não seja compatível com o sistema operacional da VM.
3. Se nenhuma propriedade `wmfVersion` for especificada, a versão mais recente do WMF aplicável é instalada.

A instalação do WMF requer uma reinicialização.
Após a reinicialização, a extensão faz o download do arquivo .zip especificado na propriedade `modulesUrl`, se fornecido.
Se esse local estiver no armazenamento de blobs do Azure, um token SAS pode ser especificado na propriedade `sasToken` para acessar o arquivo.
Depois que o arquivo .zip for baixado e descompactado, a função de configuração definida em `configurationFunction` é executada para gerar um arquivo MOF.
Em seguida, a extensão executa `Start-DscConfiguration -Force` usando o arquivo MOF gerado.
A extensão captura a saída e a grava no canal de status do Azure.

### <a name="default-configuration-script"></a>Script de Configuração Padrão

A Extensão de DSC do Azure inclui um script de configuração padrão para ser usado na integração de uma VM com o serviço de DSC de Automação do Azure.
Os parâmetros do script estão alinhados com as propriedades configuráveis do [Gerenciador de Configurações Locais](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).
Os parâmetros de script são [documentados](extensions-dsc-template.md##default-configuration-script) e o script completo está disponível em [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-arm-templates"></a>Extensão de DSC em Modelos ARM

Modelos de implantação do Azure Resource Manager (ARM) são a forma esperada de trabalhar com a Extensão de DSC na maioria dos cenários.
Para obter informações e exemplos de inclusão da Extensão de DSC em modelos de implantação do ARM, consulte a página de documentação dedicada [Extensão de Configuração de Estado Desejado com modelos do Azure Resource Manager](extensions-dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Cmdlets do PowerShell de Extensão de DSC

Os cmdlets do PowerShell para gerenciar a Extensão de DSC são melhor usados para cenários de solução de problemas interativa e coleta de informações.
Os cmdlets podem ser usados para empacotar, publicar e monitorar implantações de extensão de DSC.
Observe que os cmdlets para Extensão de DSC ainda não foram atualizados para funcionar com o [Script de Configuração Padrão](###default-configuration-script).

`Publish-AzureRMVMDscConfiguration` recebe um arquivo de configuração, verifica a existência de recursos dependentes de DSC e cria um arquivo .zip contendo a configuração e os recursos de DSC necessários para aplicar a configuração.
Também pode criar o pacote localmente usando o parâmetro `-ConfigurationArchivePath` .
Caso contrário, ele publicará o arquivo .zip no Armazenamento de Blobs do Azure e o protegerá com um token SAS.

O arquivo .zip criado por esse cmdlet possui o script de configuração .ps1 na raiz da pasta de arquivamento.
Os recursos possuem a pasta de módulo colocada na pasta de arquivo morto.

`Set-AzureRMVMDscExtension` injeta as configurações necessárias pela extensão de DSC do PowerShell em um objeto de configuração da VM.

O `Get-AzureRMVMDscExtension` recupera o status da extensão de DSC de uma VM específica.

`Get-AzureRMVMDscExtensionStatus` recupera o status da configuração DSC imposta pelo manipulador de extensão de DSC.
Essa ação pode ser executada em uma única VM ou em um grupo de VMs.

`Remove-AzureRMVMDscExtension` remove o manipulador de extensão de uma determinada máquina virtual.
Esse cmdlet **não** remove a configuração, desinstala o WMF ou altera as configurações aplicadas na máquina virtual.
Apenas remove o manipulador de extensão. 

Informações importantes com relação aos cmdlets de Extensão de DSC do AzureRM:

- Os cmdlets do Azure Resource Manager são síncronos.
- ResourceGroupName, VMName, ArchiveStorageAccountName, Version e Location são parâmetros obrigatórios.
- ArchiveResourceGroupName é um parâmetro opcional. Você pode especificar esse parâmetro quando sua conta de armazenamento pertencer a um grupo de recursos diferente daquele no qual a máquina virtual foi criada.
- A opção AutoUpdate habilita a atualização automática do manipulador de extensão para a versão mais recente quando estiver disponível. Observe que esse parâmetro tem o potencial de causar reinicializações na VM quando uma nova versão do WMF for lançada.

### <a name="getting-started-with-cmdlets"></a>Introdução aos Cmdlets

A extensão de DSC do Azure pode usar documentos de configuração de DSC diretamente para configurar VMS do Azure durante a implantação, apesar de isso não registrar o nó na Automação do Azure, portanto o nó **NÃO* será gerenciado centralmente.

A seguir está um exemplo simples de uma configuração.
Salve localmente como "IisInstall.ps1":

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
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Funcionalidade do Portal do Azure

Navegue até uma VM. Em Configurações -> Geral, clique em "Extensões".
Um novo painel é criado.
Clique em "Adicionar" e selecione DSC do PowerShell.

O portal precisa de entrada.
**Módulos de Configuração ou Script**: este campo é obrigatório (o formulário não foi atualizado para o [Script de Configuração Padrão](###default-configuration-script).
Requer um arquivo. ps1 contendo um script de configuração ou um arquivo .zip com um script de configuração .ps1 na raiz e todos os recursos dependentes em pastas de módulo dentro do .zip.
Ele pode ser criado com o cmdlet `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` incluído no SDK do Azure PowerShell.
O arquivo .zip será carregado em seu armazenamento de blobs de usuário protegido por um token SAS.

**Arquivo de configuração PSD1 de dados**: esse campo é opcional.
Se sua configuração exigir um arquivo de dados de configuração em .psd1, use este campo para selecioná-lo e carregá-lo no armazenamento de blobs de usuário, onde eles serão protegidos por um token SAS.

**Nome de configuração qualificado por módulo**: os arquivos .ps1 podem ter várias funções de configuração.
Insira o nome do script .ps1 de configuração seguido por um '\'' e o nome da função de configuração.
Por exemplo, se o seu script .ps1 tiver o nome "configuration.ps1" e se a configuração for "IisInstall", insira: `configuration.ps1\IisInstall`

**Argumentos de configuração**: se a função de configuração leva argumentos, insira-os aqui no formato `argumentName1=value1,argumentName2=value2`.
Observe que esse formato é diferente daquele como argumentos de configuração são aceitos por meio de cmdlets do PowerShell ou modelos do Resource Manager.

## <a name="logging"></a>Registro em log
Os logs são colocados em:

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Version Number]
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a DSC do PowerShell, [visite o centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

Examine o [modelo do Azure Resource Manager para a extensão de DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Para encontrar a funcionalidade adicional que você pode gerenciar com a DSC do PowerShell, [navegue na galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) para conhecer mais recursos da DSC.

Para obter detalhes sobre como passar parâmetros confidenciais em configurações, consulte [Gerenciar credenciais com segurança com o manipulador de extensão de DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
