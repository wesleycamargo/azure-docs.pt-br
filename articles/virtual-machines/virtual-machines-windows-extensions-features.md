<properties
 pageTitle="Recursos e extensões de máquina virtual | Microsoft Azure"
 description="Saiba quais extensões estão disponíveis para as máquinas virtuais do Azure, agrupadas pelas funcionalidades fornecidas ou aperfeiçoadas."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="neilpeterson"
 manager="timlt"
 editor=""
 tags="azure-service-management,azure-resource-manager"/>

<tags
 ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/30/2016"
 ms.author="nepeters"/>


# <a name="about-virtual-machine-extensions-and-features"></a>Sobre os recursos e extensões de máquina virtual

## <a name="azure-vm-extensions"></a>Extensões de VM do Azure

Extensões da máquina virtual do Azure são pequenos aplicativos que fornecem tarefa de configuração e automação de pós-implantação nas máquinas virtuais do Azure. Por exemplo, se uma máquina virtual requer a instalação do software, proteção antivírus ou a configuração do Docker, uma extensão da VM pode ser usada para concluir essas tarefas. As extensões da VM do Azure podem ser executadas usando a CLI do Azure, o PowerShell, modelos do Resource Manager e o portal do Azure. Extensões podem ser agrupadas com uma nova implantação de máquina virtual ou executar qualquer sistema existente.

Este documento fornece os pré-requisitos para a extensão da máquina virtual do Azure e orientações sobre como detectar extensões da VM disponíveis. 

## <a name="azure-vm-agent"></a>Agente de VM do Azure

O Agente de VM do Azure gerencia a interação entre uma máquina virtual do Azure e o Controlador de Malha do Azure. O agente de VM é responsável por muitos aspectos funcionais de implantação e gerenciamento de máquinas virtuais do Azure, incluindo a execução de extensões da VM. O agente de VM do Azure é pré-instalado nas imagens da galeria do Azure e pode ser instalado em sistemas operacionais com suporte. 

Para saber mais sobre os sistemas operacionais com suporte e as instruções de instalação, confira [Agente de máquina virtual do Azure](./virtual-machines-windows-classic-agents-and-extensions.md).

## <a name="discover-vm-extensions"></a>Descobrir extensões de VM

Muitas extensões de VM diferentes estão disponíveis para uso com as máquinas virtuais do Azure. Para ver uma lista completa, execute o seguinte comando com a CLI do Azure, substituindo a localização pela localização de escolha.

```none
Get-AzureVMAvailableExtension | Select ExtensionName, Version
```

<br />

## <a name="common-vm-extensions"></a>Extensões comuns de VM

|Nome da extensão   |Descrição   |Mais informações   |
|---|---|---|
|Extensão de script personalizado para o Windows  | Executar scripts em uma máquina virtual do Azure  |[Extensão de script personalizado para o Windows](./virtual-machines-windows-extensions-customscript.md)   |
|Extensão de DSC para o Windows | Extensão do PowerShell DSC (configuração de estado desejado).  | [Extensão da VM do Docker](./virtual-machines-windows-extensions-dsc-overview.md)  |
|Extensão de Diagnóstico do Azure | Gerenciar Diagnóstico do Azure |[Extensão de Diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |



<!--HONumber=Oct16_HO2-->


