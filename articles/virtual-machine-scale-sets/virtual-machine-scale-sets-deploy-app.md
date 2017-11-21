---
title: "Implantar um aplicativo em um conjunto de dimensionamento de máquinas virtuais do Azure | Microsoft Docs"
description: "Saiba como implantar aplicativos em instâncias de máquina virtual Linux e Windows em um conjunto de dimensionamento"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 7e03d5e2bbdb1b3b206fa7fa455f7dce7951f02b
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2017
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Implantar o aplicativo em conjuntos de dimensionamento de máquinas virtuais
Para executar aplicativos em instâncias de VM (máquina virtual) em um conjunto de dimensionamento, primeiro é necessário instalar os componentes de aplicativo e os arquivos necessários. Este artigo apresenta maneiras de criar uma imagem de VM personalizada para instâncias em um conjunto de dimensionamento ou executar scripts de instalação automaticamente em instâncias de VM existentes. Você também aprenderá a gerenciar aplicativos ou atualizações do SO em um conjunto de dimensionamento.


## <a name="build-a-custom-vm-image"></a>Compilar uma imagem de VM personalizada
Quando você usa uma das imagens de plataforma do Azure para criar as instâncias no seu conjunto de dimensionamento, nenhum software adicional é instalado ou configurado. Você pode automatizar a instalação desses componentes, no entanto, isso aumenta o tempo necessário para provisionar as instâncias de VM para seus conjuntos de dimensionamento. Se você aplicar várias alterações de configuração às instâncias de VM, haverá sobrecarga de gerenciamento com esses scripts de configuração e tarefas.

Para reduzir o gerenciamento de configuração e a hora para provisionar uma VM, você pode criar uma imagem de VM personalizada que está pronta para executar o aplicativo assim que uma instância é provisionada no conjunto de dimensionamento. O processo geral para criar uma imagem de VM personalizada para instâncias de conjunto de dimensionamento são os seguintes:

1. Para criar uma imagem VM personalizada para as instâncias de conjunto de dimensionamento, você cria uma VM e faz logon nela e, em seguida, instala e configura o aplicativo. Você pode usar o Packer para definir e compilar uma imagem de VM [Linux](../virtual-machines/linux/build-image-with-packer.md) ou [Windows](../virtual-machines/windows/build-image-with-packer.md). Ou, você pode criar e configurar a VM manualmente:

    - Crie uma VM Linux com a [CLI do Azure 2.0](../virtual-machines/linux/quick-create-cli.md), o [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md) ou o [portal](../virtual-machines/linux/quick-create-portal.md).
    - Crie uma VM do Windows com o [Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md), a [CLI do Azure 2.0](../virtual-machines/windows/quick-create-cli.md) ou o [portal](../virtual-machines/windows/quick-create-portal.md).
    - Faça logon em uma VM [Linux](../virtual-machines/linux/mac-create-ssh-keys.md#use-the-ssh-key-pair) ou [Windows](../virtual-machines/windows/connect-logon.md).
    - Instale e configure os aplicativos e as ferramentas necessárias. Se você precisar de versões específicas de uma biblioteca ou tempo de execução, uma imagem de VM personalizada permitirá que você defina uma versão e 

2. Capture a VM com a [CLI do Azure 2.0](../virtual-machines/linux/capture-image.md) ou o [Azure PowerShell](../virtual-machines/windows/capture-image.md). Esta etapa cria a imagem de VM personalizada que é usada para implantar instâncias em um conjunto de dimensionamento.

3. [Crie um conjunto de dimensionamento](virtual-machine-scale-sets-create.md) e especifique a imagem de VM personalizada criada nas etapas anteriores.


## <a name="already-provisioned"></a>Instalar um aplicativo com a Extensão de Script Personalizado
A extensão de script personalizado baixa e executa scripts em VMs do Azure. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Os scripts podem ser baixados do armazenamento do Azure ou do GitHub, ou fornecidos ao Portal do Azure no tempo de execução da extensão.

A extensão de script personalizado se integra com modelos do Azure Resource Manager e também pode ser executada usando a CLI do Azure, o PowerShell, o portal do Azure ou a API REST da máquina virtual do Azure. 

Para obter mais informações, consulte a [Visão geral da Extensão de Script Personalizado](../virtual-machines/windows/extensions-customscript.md).


### <a name="use-azure-powershell"></a>Usar PowerShell do Azure
O PowerShell usa uma tabela de hash para armazenar o arquivo a baixar e o comando a executar. O exemplo a seguir:

- Instrui as instâncias de VM a baixar um script do GitHub – *https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1*
- Define a extensão para executar um script de instalação – `powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1`
- Obtém informações sobre um conjunto de dimensionamento com [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Aplica a extensão para as instâncias de VM com [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

A Extensão de Script Personalizado é aplicada para instâncias de VM de *myScaleSet* no grupo de recursos denominado *myResourceGroup*. Insira seus próprios nomes da seguinte maneira:

```powershell
# Define the script for your Custom Script Extension to run
$customConfig = @{
    "fileUris" = (,"https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Se a política de atualização em seu conjunto de dimensionamento é *manual*, atualize as instâncias de VM com [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Este cmdlet aplica a configuração de conjunto de dimensionamento atualizado para as instâncias de VM e instala o aplicativo.


### <a name="use-azure-cli-20"></a>Usar a CLI 2.0 do Azure
Para usar a Extensão de Script Personalizado com a CLI do Azure, você deve criar um arquivo JSON que define quais arquivos para obter e os comandos a serem executados. Essas definições JSON podem ser reutilizadas em implantações de conjunto de dimensionamento para aplicar instalações de aplicativo consistentes.

No shell atual, crie um arquivo chamado *customConfig.json* e cole a configuração a seguir. Por exemplo, crie o arquivo no Cloud Shell e não em seu computador local. Você pode usar qualquer editor que queira. Insira `sensible-editor cloudConfig.json` para criar o arquivo e ver uma lista de editores disponíveis.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Aplique a Configuração de Extensão de Script personalizado às instâncias de VM no conjunto de dimensionamento com [az vmss extension set](/cli/azure/vmss/extension#set). O exemplo a seguir aplica a configuração *customConfig.json* às instâncias de VM de *myScaleSet* no grupo de recursos denominado *myResourceGroup*. Insira seus próprios nomes da seguinte maneira:

```azurecli
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfig.json
```

Se a política de atualização em seu conjunto de dimensionamento é *manual*, atualize as instâncias de VM com [az vmss update-instances](/cli/azure/vmss#update-instances). Este cmdlet aplica a configuração de conjunto de dimensionamento atualizado para as instâncias de VM e instala o aplicativo.


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Instalar um aplicativo em uma VM do Windows com o DSC do PowerShell
A [DSC (Configuração de Estado Desejado) do PowerShell](https://msdn.microsoft.com/en-us/powershell/dsc/overview) é uma plataforma de gerenciamento para definir a configuração dos computadores de destino. As configurações de DSC definem o que instalar em um computador e como configurar o host. Um mecanismo do LCM (Gerenciador de Configurações Local) é executado em cada nó de destino que processa as ações solicitadas com base em configurações enviadas por push.

A extensão de DSC do PowerShell permite que você personalize as instâncias de VM em um conjunto de dimensionamento com o PowerShell. O exemplo a seguir:

- Instrui as instâncias de VM para baixar um pacote de DSC do GitHub – *https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip*
- Define a extensão para executar um script de instalação – `configure-http.ps1`
- Obtém informações sobre um conjunto de dimensionamento com [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Aplica a extensão para as instâncias de VM com [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

A extensão de DSC é aplicada a instâncias de VM de *myScaleSet* no grupo de recursos denominado *myResourceGroup*. Insira seus próprios nomes da seguinte maneira:

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Se a política de atualização em seu conjunto de dimensionamento é *manual*, atualize as instâncias de VM com [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Este cmdlet aplica a configuração de conjunto de dimensionamento atualizado para as instâncias de VM e instala o aplicativo.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Instalar um aplicativo em uma VM Linux com o cloud-init
[Inicialização de nuvem](https://cloudinit.readthedocs.io/latest/) é uma abordagem amplamente utilizada para personalizar uma VM do Linux, quando ela é inicializada pela primeira vez. Você pode utilizar a inicialização de nuvem para instalar pacotes e gravar arquivos, ou para configurar usuários e segurança. Como a inicialização de nuvem é executada durante o processo de inicialização inicial, não há etapa adicional ou agentes necessários para aplicar a configuração.

A inicialização de nuvem também funciona em distribuições. Por exemplo, você não usa **apt-get install** nem **yum install** para instalar um pacote. Em vez disso, você pode definir uma lista de pacotes para instalar. Inicialização de nuvem usa automaticamente a ferramenta de gerenciamento de pacote nativo de distribuição que você selecionar.

Para obter mais informações, incluindo um arquivo de exemplo *cloud-init.txt*, consulte [Usar cloud-init para personalizar as VMs do Azure](../virtual-machines/linux/using-cloud-init.md).

Para criar um conjunto de dimensionamento e usar um arquivo cloud-init, adicione o parâmetro `--custom-data` para o comando [az vmss create](/cli/azure/vmss#create) e especifique o nome de um arquivo de cloud-init. O exemplo a seguir cria um conjunto de dimensionamento chamado *myScaleSet* em *myResourceGroup* e configura as instâncias de VM com um arquivo chamado *cloud-init.txt*. Insira seus próprios nomes da seguinte maneira:

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="install-applications-as-a-set-scales-out"></a>Instalar aplicativos como uma expansão de conjunto
Conjuntos de dimensionamento permitem que você aumente o número de instâncias de VM que executam seu aplicativo. Esse processo de expansão pode ser iniciado manualmente ou automaticamente com base em métricas como uso de CPU ou de memória.

Se você tiver aplicado uma Extensão de Script Personalizado ao conjunto de dimensionamento, o aplicativo será instalado em cada nova instância VM. Se o conjunto de dimensionamento é baseado em uma imagem personalizada com o aplicativo pré-instalado, cada nova instância VM é implantada em um estado utilizável. 

Se as instâncias de VM do conjunto de dimensionamento são hosts de contêiner, você pode usar a Extensão de Script Personalizado para efetuar pull das imagens de contêiner necessárias e executá-las. A extensão de Script personalizado também poderia registrar a nova instância VM com um orquestrador, como o Serviço de Contêiner do Azure.


## <a name="deploy-application-updates"></a>Implantar atualizações de aplicativo
Se você atualizar seu código do aplicativo, bibliotecas ou pacotes, você poderá enviar por push o estado mais recente do aplicativo para instâncias de VM em um conjunto de dimensionamento. Se você usar a Extensão de Script Personalizado, as atualizações para seu aplicativo não serão implantadas automaticamente. Altere a configuração de Script Personalizado, tal como para apontar para um script de instalação que tem um nome de versão atualizado. Em um exemplo anterior, a extensão de Script Personalizado usa um script chamado *automate_nginx.sh* da seguinte maneira:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Quaisquer atualizações feitas ao seu aplicativo não serão expostas à Extensão de Script Personalizado, a menos que esse script de instalação seja alterado. Uma abordagem é incluir um número de versão que é incrementado pelas versões do aplicativo. A extensão de Script personalizado agora poderia fazer referência a *automate_nginx_v2.sh* da seguinte maneira:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

A Extensão de Script Personalizado agora é executada em relação as instâncias de VM para aplicar as atualizações do aplicativo mais recentes.


### <a name="install-applications-with-os-updates"></a>Instalar aplicativos com atualizações de SO
Quando novas versões de sistema operacional estão disponíveis, você pode usar ou compilar uma nova imagem personalizada e [implantar atualizações do SO](virtual-machine-scale-sets-upgrade-scale-set.md) em um conjunto de dimensionamento. Cada instância VM é atualizada para a imagem mais recente que você especificar. Você pode usar uma imagem personalizada com o aplicativo já instalado, a Extensão de Script Personalizado ou DSC do PowerShell para que seu aplicativo fique disponível automaticamente conforme você realiza a atualização. Talvez seja necessário planejar a manutenção de aplicativos conforme você executa esse processo para garantir que não haja nenhum problema de compatibilidade de versão.

Se você usar uma imagem VM personalizada com o aplicativo já instalado, você poderá integrar as atualizações de aplicativo com um pipeline de implantação para compilar as novas imagens e implantar atualizações de SO em todo o conjunto de dimensionamento. Essa abordagem permite que o pipeline pegue os builds mais recentes do aplicativo, criar e validar uma imagem de VM e atualizar as instâncias de VM no conjunto de dimensionamento. Para executar um pipeline de implantação que compila e implanta atualizações de aplicativos em imagens de VM personalizadas, você pode usar o [Visual Studio Team Services](https://www.visualstudio.com/team-services/), o [Spinnaker](https://www.spinnaker.io/) ou o [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Próximas etapas
Conforme você compila e implanta aplicativos em seus conjuntos de dimensionamento, você pode examinar a [Visão geral de design do conjunto de dimensionamento](virtual-machine-scale-sets-design-overview.md). Para obter mais informações sobre como gerenciar o conjunto de dimensionamento, confira [Usar o PowerShell para gerenciar o conjunto de dimensionamento](virtual-machine-scale-sets-windows-manage.md).
