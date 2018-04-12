---
title: Visão geral de configuração de estado desejado para o Azure | Microsoft Docs
description: Saiba como usar o manipulador de extensão do Microsoft Azure para Configuração de Estado Desejado (DSC) do PowerShell. O artigo inclui cmdlets, a arquitetura e os pré-requisitos.
services: virtual-machines-windows
documentationcenter: ''
author: mgreenegit
manager: jeconnoc
editor: ''
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
ms.openlocfilehash: e23d0a70cdfcc1b37f02d86dd6418aa28c5bbf2c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introdução ao manipulador de extensão de configuração do estado desejado do Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

O agente de VM do Azure e as extensões associadas são parte dos serviços de infraestrutura do Microsoft Azure. Extensões de VM são componentes de software que estendem a funcionalidade da VM e simplificam várias operações de gerenciamento de VM.

Caso de uso primário para a extensão de Configuração de Estado de Desejado (DSC) do Azure inicializar uma VM para o [serviço de DSC de Automação do Azure](../../automation/automation-dsc-overview.md). Inicializar uma VM fornece [benefícios](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#pull-service) que incluem o gerenciamento contínuo da configuração da VM e a integração com outras ferramentas operacionais, como o Azure Monitor.

Você pode usar a extensão de DSC, independentemente do serviço de DSC de Automação. No entanto, isso envolve uma ação única que ocorre durante a implantação. Nenhum relatório contínuo ou gerenciamento de configuração está disponível, a não ser localmente na VM.

Este artigo fornece informações sobre cenários: usar a extensão DSC para integração da Automação e usar a extensão de DSC como uma ferramenta para atribuir configurações a VMs usando o SDK do Azure.

## <a name="prerequisites"></a>pré-requisitos

- **Computador local**: Para interagir com a extensão de VM do Azure, você deve usar o Portal do Azure ou o SDK do Azure PowerShell.
- **Agente convidado**: A VM do Azure que é configurada pela configuração do DSC deve ter um sistema operacional compatível com Windows Management Framework (WMF) 4.0 ou posterior. Para a lista completa de versões com suporte do sistema operacional, consulte o [Histórico de versões da extensão de DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termos e conceitos

Este guia presume familiaridade com os seguintes conceitos:

- **Configuração**: um documento de configuração DSC.
- **Nó**: um destino para uma configuração DSC. Neste documento, *nó* sempre faz referência a uma VM do Azure.
- **Dados de configuração**: Um arquivo .psd1 que tem dados ambientais de uma configuração.

## <a name="architecture"></a>Arquitetura

A extensão de DSC do Azure usa a estrutura do Agente de VM do Azure para entregar, aplicar e gerar relatórios sobre configurações da DSC executadas em VMs do Azure. A extensão de DSC aceita um documento de configuração e um conjunto de parâmetros. Se nenhum arquivo for fornecido, um [script de configuração padrão](#default-configuration-script) é inserido com a extensão. O script de configuração padrão é usado somente para definir metadados no [Local Configuration Manager](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).

Quando a extensão é chamada pela primeira vez, ela instala uma versão do WMF usando a lógica a seguir:

* Se o sistema operacional da VM do Azure for o Windows Server 2016, nenhuma ação é executada. O Windows Server 2016 já possui a versão mais recente do PowerShell instalada.
* Se a propriedade **wmfVersion** for especificada, essa versão do WMF é instalada, a menos que essa versão não seja compatível com o sistema operacional da VM.
* Se nenhuma propriedade **wmfVersion** for especificada, a versão mais recente de WMF aplicável é instalada.

Instalar o WMF requer uma reinicialização. Após a reinicialização, a extensão faz o download do arquivo .zip que é especificado na propriedade **modulesUrl**, se fornecido. Se esse local estiver no armazenamento de blobs do Azure, você pode especificar um token SAS na propriedade **sasToken** para acessar o arquivo. Depois que o arquivo .zip for baixado e descompactado, a função de configuração definida em **configurationFunction** é executada para gerar um arquivo .mof. Em seguida, a extensão executa **Start-DscConfiguration-Force** usando o arquivo .mof gerado. A extensão captura a saída e a grava no canal de status do Azure.

### <a name="default-configuration-script"></a>Script de configuração padrão

A extensão de DSC do Azure inclui um script de configuração padrão que é destinado a ser usado quando você carrega uma VM ao serviço de DSC de Automação do Azure. Os parâmetros do script estão alinhados com as propriedades configuráveis do [Gerenciador de Configurações Locais](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig). Para parâmetros de script, consulte [Script de configuração padrão](extensions-dsc-template.md#default-configuration-script) na [extensão de Configuração de Estado Desejado com modelos do Azure Resource Manager](extensions-dsc-template.md). Para o script completo, consulte o [modelo de início rápido do Azure no GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-resource-manager-templates"></a>Extensão de DSC nos modelos do Resource Manager

Na maioria dos cenários, os modelos de implantação do Azure Resource Manager (ARM) são a forma esperada de trabalhar com a extensão de DSC. Para obter mais informações e exemplos sobre como incluir a extensão de DSC em modelos de implantação do Resource Manager, consulte [Extensão de Configuração de Estado Desejado com modelos do Azure Resource Manager](extensions-dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Cmdlets do PowerShell de Extensão de DSC

Os cmdlets do PowerShell que são usados para gerenciar a extensão de DSC são melhor usados em cenários de solução de problemas interativa e coleta de informações. Você pode usar os cmdlets para empacotar, publicar e monitorar implantações de extensão de DSC. Observe que os cmdlets para a extensão de DSC ainda não foram atualizados para funcionar com o [Script de Configuração Padrão](#default-configuration-script).

O cdmlet **Publish-AzureRMVMDscConfiguration** entra em um arquivo de configuração, ele procura recursos de DSC dependentes e, em seguida, cria um arquivo. zip. O arquivo. zip contém a configuração e recursos de DSC que são necessários para aplicar a configuração. O cmdlet também pode criar o pacote localmente usando o parâmetro *-OutputArchivePath*. Caso contrário, o cmdlet publica o arquivo .zip no armazenamento de Blobs e o protege com um token SAS.

O script de configuração .ps1 criado por esse cmdlet é um arquivo .zip na raiz da pasta de arquivamento. A pasta de módulo é colocada na pasta de arquivamento em recursos.

O cmdlet **Set-AzureRMVMDscExtension** injeta as configurações que requer a extensão de DSC do PowerShell em um objeto de configuração de VM.

O cmdlet **Get-AzureRMVMDscExtension** recupera o status da extensão de DSC de uma VM específica.

O cmdlet **Get-AzureRMVMDscExtensionStatus** recupera o status da configuração de DSC que é imposta pelo manipulador de extensão de DSC. Essa ação pode ser executada em uma única VM ou em um grupo de VMs.

O cmdlet **Remove-AzureRMVMDscExtension** remove o manipulador de extensão de uma VM específica. Esse cmdlet *não* remove a configuração, desinstala o WMF ou altera as configurações aplicadas na máquina virtual. Apenas remove o manipulador de extensão. 

Informações importantes sobre os cmdlets de extensão de DSC do Resource Manager:

- Os cmdlets do Azure Resource Manager são síncronos.
- Os parâmetros *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version* e *Location* são obrigatórios.
- *ArchiveResourceGroupName* é um parâmetro opcional. Você pode especificar esse parâmetro quando sua conta de armazenamento pertencer a um grupo de recursos diferente daquele no qual a VM foi criada.
- Use a opção **AutoUpdate** para atualizar automaticamente do manipulador de extensão para a versão mais recente quando estiver disponível. Observe que esse parâmetro tem o potencial de causar reinicializações na VM quando uma nova versão de WMF for lançada.

### <a name="get-started-with-cmdlets"></a>Introdução aos cmdlets

A extensão de DSC do Azure pode usar documentos de configuração DSC para configurar diretamente VMs do Azure durante a implantação. Essa etapa não registra o nó para Automação. O nó *não* é gerenciado centralmente.

O exemplo a seguir mostra um exemplo simples de configuração. Salve a configuração localmente como IisInstall.ps1.

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

Os seguintes comandos colocam o script de IisInstall.ps1 na VM especificada. Os comandos também executar a configuração e, em seguida, reportam o status.

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script to user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Funcionalidade do portal do Azure

Para configurar o DSC no portal:

1. Ir para uma VM. 
2. Em **Configurações** > **Geral**, selecione **Extensões**. 
3. No painel novo que é criado, selecione **Adicionar**e, em seguida, selecione **PowerShell DSC**.

Este portal exige a seguinte entrada:

* **Módulos de Configuração ou Script**: este campo é obrigatório (o formulário não foi atualizado para o [script de configuração padrão](#default-configuration-script)). Módulos de configuração e scripts exigem um arquivo. ps1 que tem um script de configuração ou um arquivo. zip com um script de configuração. ps1 na raiz. Se você usar um arquivo. zip, todos os recursos dependentes devem ser incluídos em pastas de módulo no. zip. Você pode criar o arquivo. zip usando o cmdlet **Publish-AzureVMDscConfiguration - -OutputArchivePath** que está incluído no SDK do PowerShell do Azure. O arquivo .zip será carregado em seu armazenamento de blobs de usuário protegido por um token SAS.

* **Arquivo de configuração PSD1 de dados**: esse campo é opcional. Se sua configuração exigir um arquivo de dados de configuração em .psd1, use este campo para selecionar o campo de dados e carregá-lo no armazenamento de blobs de usuário. O arquivo de dados de configuração é protegido por um token SAS no armazenamento de blob.

* **Nome de configuração qualificado por módulo**: você pode incluir várias funções de configuração em um arquivo .ps1. Insira o nome do script .ps1 de configuração seguido por \\ e o nome da função de configuração. Por exemplo, se o seu script .ps1 tiver o nome configuration.ps1 e se a configuração for **IisInstall**, insira **configuration.ps1\IisInstall**.

* **Argumentos de configuração**: se a função de configuração leva argumentos, insira-os aqui no formato **argumentName1=value1,argumentName2=value2**. Observe que esse formato é diferente daquele como argumentos de configuração são aceitos nos cmdlets do PowerShell ou modelos do Resource Manager.

## <a name="logs"></a>Logs
Coloque os logs neste local:

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>
```

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a DSC do PowerShell, acesse o [centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 
* Examine o [modelo do Resource Manager para a extensão de DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* Para mais funcionalidade que você pode gerenciar com a DSC do PowerShell e para mais recursos de DSC, navegue na [galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
* Para obter detalhes sobre como passar parâmetros confidenciais em configurações, consulte [Gerenciar credenciais com segurança com o manipulador de extensão de DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

