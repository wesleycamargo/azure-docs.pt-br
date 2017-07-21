---
title: Criar uma rede virtual do Azure com sub-redes | Microsoft Docs
description: "Saiba como criar uma rede virtual com várias sub-redes no Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: jdial
ms.custom: 
ms.translationtype: Human Translation
ms.sourcegitcommit: 07584294e4ae592a026c0d5890686eaf0b99431f
ms.openlocfilehash: 1ecfba0470c31d1119b5e6c0f9d8538abe40f834
ms.contentlocale: pt-br
ms.lasthandoff: 06/02/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Criar uma rede virtual com várias sub-redes

Neste tutorial, aprenda como criar uma rede virtual do Azure básica com sub-redes públicas e privadas separadas. Você pode conectar recursos do Azure como máquinas virtuais, ambientes de Serviço de Aplicativo do Azure, conjuntos de dimensionamento de máquina virtual, Azure HDInsight e serviços de nuvem para sub-redes. Recursos que estão conectados a redes virtuais podem se comunicar entre si pela rede privada do Azure.

As seções a seguir incluem etapas que você pode executar para implantar uma rede virtual usando o [portal do Azure](#portal), a interface de linha de comando do Azure ([CLI do Azure](#azure-cli)), [Azure PowerShell](#powershell)e um [modelo do Azure Resource Manager](#resource-manager-template). O resultado é o mesmo, não importa a ferramenta que você usa para implantar a rede virtual. Clique no link da ferramenta para ir para essa seção do tutorial. Para saber mais sobre todas as configurações de sub-rede e rede virtual, consulte [Gerenciar redes virtuais](virtual-network-manage-network.md) e [Gerenciar sub-redes virtuais](virtual-network-manage-subnet.md).

## <a name="portal"></a>Portal do Azure

1. Em um navegador da Internet, vá para o [portal do Azure](https://portal.azure.com). Entre usando sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. No portal, clique em **+ Novo** > **Rede** > **Rede virtual**.
3. Na folha **Rede virtual**, em **Selecionar um modelo de implantação**, deixe **Resource Manager** selecionado e, depois, clique em **Criar**.
4. Na folha **Criar rede virtual**, insira os valores a seguir e clique em **Criar**:

    |Configuração|Valor|
    |---|---|
    |Nome|MyVnet|
    |Espaço de endereço|10.0.0.0/16|
    |Nome da sub-rede|Público|
    |Intervalo de endereços da sub-rede|10.0.0.0/24|
    |Grupo de recursos|Deixe **Criar novo** selecionado e digite **MyResourceGroup**.|
    |Assinatura e localização|Selecione sua assinatura e localização.

    Se você for novo no Azure, saiba mais sobre [grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [assinaturas](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [locais](https://azure.microsoft.com/regions) (também conhecido como *regiões*).
6. No portal, você pode criar apenas uma sub-rede quando você criar uma rede virtual. Neste tutorial, você cria uma segunda sub-rede após criar a rede virtual. É possível conectar mais tarde recursos acessíveis pela Internet para a sub-rede **pública**. Você também pode conectar recursos que não são acessíveis pela Internet para uma sub-rede **privada**. Para criar a segunda sub-rede, na caixa **Pesquisar recursos** na parte superior da página, digite **MyVnet**. Nos resultados da pesquisa, clique em **MyVnet**. Se você tiver várias redes virtuais com o mesmo nome em sua assinatura, verifique os grupos de recursos que estão listados em cada rede virtual. Certifique-se de que você clique o resultado de pesquisa **MyVnet** que tem o grupo de recursos **MyResourceGroup**.
7. Na folha **MyVnet**, em **CONFIGURAÇÕES**, clique em **Sub-redes**.
8. Na folha **MyVnet – Sub-redes**, clique em **+Sub-rede**.
9. Na folha **Adicionar sub-rede**, para **Nome**, insira **Privada**. Para **Intervalo de endereços**, digite **10.0.1.0/24**.  Clique em **OK**.
10. Na folha **MyVnet – Sub-Redes**, examine as sub-redes. Você pode ver as sub-redes **Pública** e **Privada** que você criou.
11. **Opcional**: Para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-portal) deste artigo.

## <a name="azure-cli"></a>CLI do Azure

Comandos da CLI do Azure são os mesmos, se você executar os comandos do Windows, Linux ou macOS. No entanto, há diferenças de script entre shells de sistema operacional. As instruções a seguir são para executar um script de Busca que contém comandos da CLI do Azure:

1. Em um navegador da Internet, abra o [portal do Azure](https://portal.azure.com). Entre usando sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Na parte superior da página do portal, à direita da caixa **Pesquisar recursos**, clique no ícone **>_** para iniciar um Azure Cloud Shell de Busca (Versão Prévia). O painel de Cloud Shell aparece na parte inferior do portal. Depois de alguns segundos, um prompt  **username@Azure:~ $** será exibido. O Cloud Shell entrará automaticamente no Azure usando as credenciais usadas para entrar no portal.
3. No navegador, copie o script a seguir:
    ```azurecli
    #!/bin/bash
    
    # Create a resource group.
    az group create \
      --name MyResourceGroup \
      --location eastus
    
    # Create a virtual network with one subnet.
    az network vnet create \
      --name MyVnet \
      --resource-group MyResourceGroup \
      --subnet-name Public
    
    # Create an additional subnet within the virtual network.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. Crie um arquivo de script e salve-o. No prompt de comando do Cloud Shell, digite `nano myscript.sh --nonewlines`. O comando inicia o editor nano GNU com um arquivo myscript.sh vazio. Coloque o cursor dentro da janela do editor, clique com o botão direito e, em seguida, clique em **Colar**.  
5. Para salvar o arquivo como myscript.sh, pressione Ctrl + X, digite **Y**e, em seguida, pressione a tecla Enter. O armazenamento de Cloud Shell não mantém arquivos salvos entre as sessões. Se você deseja manter o script entre as sessões do Cloud Shell, configure o [armazenamento persistente](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para o Cloud Shell e salve o arquivo para armazenamento persistente.
6. No prompt de comando do Cloud Shell, para marcar o arquivo como executável, execute o comando `chmod +x myscript.sh`.
7. Para executar o script, digite `./myscript.sh`.
8. Quando o script é concluído em execução, para examinar as sub-redes da rede virtual, copie o seguinte comando e, em seguida, cole-o no painel de Busca do Cloud Shell:
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **Opcional**: Para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-cli) deste artigo.

## <a name="powershell"></a>PowerShell

1. Instale a última versão do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se você for novo no Azure PowerShell, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Para iniciar uma sessão do PowerShell, acesse **Iniciar**, insira **powershell** e, em seguida, clique em **PowerShell**.
3. Na janela do PowerShell, para entrar com sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account), digite `login-azurermaccount`.
4. No navegador, copie o script a seguir:
    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets.
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network.
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. Para executar o script, clique com o botão direito na janela do PowerShell.
6. Para examinar as sub-redes para a rede virtual, copie o seguinte comando e, em seguida, cole-o na janela do PowerShell:
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **Opcional**: Para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-powershell) deste artigo.

## <a name="resource-manager-template"></a>Modelo do Resource Manager

Você pode implantar uma rede virtual usando um modelo do Azure Resource Manager. Para saber mais sobre modelos, consulte [O que é o Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Para acessar o modelo e saber mais sobre seus parâmetros, consulte o modelo [Criar uma rede virtual com duas sub-redes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Você pode implantar o modelo usando [portal](#template-portal), [CLI do Azure](#template-cli) ou [PowerShell](#template-powershell).

**Opcional**: Para excluir os recursos criados neste tutorial, conclua as etapas em quaisquer subseções de [Excluir recursos](#delete) deste artigo.

### <a name="template-portal"></a>Portal do Azure

1. No seu navegador, abra a [página modelo](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Clique no botão **Implantar no Azure**. Isso abre a página de logon do portal do Azure.
3. Entre no portal usando sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Insira os seguintes valores para os parâmetros:

    |Parâmetro|Valor|
    |---|---|
    |Assinatura|Selecione sua assinatura|
    |Grupo de recursos|MyResourceGroup|
    |Local|Selecione um local|
    |Nome da VNet|MyVnet|
    |Prefixo de Endereço da VNet|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Público|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privado|

5. Concorde com os termos e condições, clique em **Comprar** para implantar uma rede virtual.

### <a name="template-cli"></a>Azure CLI

1. No [portal](https://portal.azure.com), entre com sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. À direita da caixa **Pesquisar recursos**, clique no ícone **>_** para iniciar um Azure Cloud Shell de Busca (Versão Prévia). O painel de Cloud Shell aparece na parte inferior do portal. Depois de alguns segundos, um prompt  **username@Azure:~ $** será exibido. O Cloud Shell entrará automaticamente no Azure usando as credenciais usadas para entrar no portal do Azure.
3. Para criar um grupo de recursos para a rede virtual, digite o seguinte comando:  `az group create --name MyResourceGroup --location eastus`
4. Você pode implantar o modelo, usando uma das seguintes opções de parâmetros:
    - **Valores do parâmetro padrão**. Digite o seguinte comando:   `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - **Valores do parâmetro personalizado**. Baixe e modifique o modelo antes de implantar o modelo. Você também pode implantar o modelo usando os parâmetros na linha de comando, ou implantar o modelo com um arquivo de parâmetros separados. Para baixar os arquivos de modelo e parâmetro, clique no botão **Procurar no GitHub** na página de modelo [Criar uma rede virtual com duas sub-redes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). No GitHub, clique no arquivo **azuredeploy.parameters.json** ou **azuredeploy.json**. Em seguida, clique no botão **Raw** para exibir o arquivo. No seu navegador, copie os conteúdos do arquivo. Salve os conteúdos em um arquivo no seu computador. Você pode modificar os valores de parâmetro no modelo, ou implantar o modelo com um arquivo de parâmetros separado.  

    Para saber mais sobre como implantar modelos usando esses métodos, digite `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Instale a última versão do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se você for novo no Azure PowerShell, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Para iniciar uma sessão do PowerShell, acesse **Iniciar**, insira **powershell** e, em seguida, clique em **PowerShell**.
3. Na janela do PowerShell, para entrar com sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account), digite `login-azurermaccount`.
4. Para criar um grupo de recursos para a rede virtual, digite o seguinte comando:  `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. Você pode implantar o modelo, usando uma das seguintes opções de parâmetros:
    - **Valores do parâmetro padrão**. Digite o seguinte comando:   `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - **Valores do parâmetro personalizado**. Baixe e modifique o modelo antes de implantá-lo. Você também pode implantar o modelo usando os parâmetros na linha de comando, ou implantar o modelo com um arquivo de parâmetros separados. Para baixar os arquivos de modelo e parâmetro, clique no botão **Procurar no GitHub** na página de modelo [Criar uma rede virtual com duas sub-redes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). No GitHub, clique no arquivo **azuredeploy.parameters.json** ou **azuredeploy.json**. Em seguida, clique no botão **Raw** para exibir o arquivo. No seu navegador, copie os conteúdos do arquivo. Salve os conteúdos em um arquivo no seu computador. Você pode modificar os valores de parâmetro no modelo, ou implantar o modelo com um arquivo de parâmetros separado.  

    Para saber mais sobre como implantar modelos usando esses métodos, digite `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Excluir recursos
Ao concluir este tutorial, talvez você deseje excluir os recursos criados que você usou para o tutorial, para não incorrer em encargos de uso. Excluir um grupo de recursos também exclui todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa do portal, insira **MyResourceGroup**. Nos resultados da pesquisa, clique em **MyResourceGroup**.
2. Na folha **MyResourceGroup**, clique no ícone **Excluir**.
3. Para confirmar a exclusão, na caixa **DIGITAR O NOME DO GRUPO DE RECURSOS**, insira **MyResourceGroup** e, depois, clique em **Excluir**.

### <a name="delete-cli"></a>Azure CLI

No prompt de comando do Cloud Shell, insira o seguinte comando: `az group delete --name MyResourceGroup --yes`

### <a name="delete-powershell"></a>PowerShell

No prompt de comando do PowerShell, insira o seguinte comando: `Remove-AzureRmResourceGroup -Name MyResourceGroup`

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre todas as configurações de sub-rede e rede virtual, consulte [Gerenciar redes virtuais](virtual-network-manage-network.md#view-vnet) e [Gerenciar sub-redes virtuais](virtual-network-manage-subnet.md#create-subnet). Você tem várias opções para o uso de redes virtuais e sub-redes em um ambiente de produção para atender aos requisitos diferentes.
- Crie e aplique [grupos de segurança de rede](virtual-networks-nsg.md) (NSGs) a sub-redes para filtrar o tráfego de entrada e saída da sub-rede.
- Crie uma [máquina virtual do Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou uma [máquina virtual do Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), e conecte-a à rede virtual.
- Conecte a rede virtual a outra rede virtual no mesmo local usando [emparelhamento de rede virtual](virtual-network-peering-overview.md).
- Conecte a rede virtual para uma rede local usando uma [rede privada virtual site a site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou um circuito [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

