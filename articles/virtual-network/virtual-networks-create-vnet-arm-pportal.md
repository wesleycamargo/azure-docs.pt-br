---
title: Criar uma Rede Virtual do Azure | Microsoft Docs
description: "Aprenda a criar uma rede virtual com várias sub-redes."
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
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 19857ad1e970ad32359708ded320c53a4778ef8c
ms.contentlocale: pt-br
ms.lasthandoff: 05/16/2017


---
# <a name="create-a-virtual-network-with-multiple-subnets"></a>Criar uma rede virtual com várias sub-redes

Neste tutorial, você aprenderá a criar uma VNet (Rede Virtual) do Azure básica com sub-redes públicas e privadas separadas. Você pode conectar recursos do Azure como VMs (máquinas virtuais), Ambientes de Serviço de Aplicativo, Conjuntos de Dimensionamento de Máquina Virtual, HDInsight e Serviços de Nuvem para sub-redes. Recursos conectados a VNets são capazes de se comunicar entre si pela rede privada do Azure.

As seções a seguir incluem etapas para implantar uma VNet usando o [portal](#portal) do Azure, a [CLI (interface de linha de comando)](#cli) do Azure, o Azure [PowerShell](#powershell) e o [modelo](#template) do Azure Resource Manager. O resultado é o mesmo, não importa a ferramenta com a qual você escolha implantar a VNet. Clicar no link para qualquer ferramenta leva você diretamente à respectiva seção do artigo. Para saber mais sobre todas as configurações de VNet e sub-rede, leia os artigos [Gerenciar VNets](virtual-network-manage-network.md) e [Gerenciar sub-redes](virtual-network-manage-subnet.md).

## <a name="portal"></a>Portal do Azure

1. Em um navegador da Internet, abra o [portal](https://portal.azure.com) do Azure e entre com a sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. No portal, clique em **+ Novo** > **Rede** > **Rede virtual**.
3. Na folha **Rede virtual** exibida, deixe *Resource Manager* selecionado em **Selecionar um modelo de implantação** e clique em **Criar**.
4. Na folha **Criar rede virtual** exibida, digite os seguintes valores e clique no botão **Criar**:

    |Configuração|Valor|
    |---|---|
    |Nome|*MyVnet*|
    |Espaço de endereço|*10.0.0.0/16*|
    |Nome da sub-rede|Público|
    |Intervalo de endereços da sub-rede|*10.0.0.0/24*|
    |Grupo de recursos|Deixe **Criar novo** selecionado e digite *MyResourceGroup*|
    |Assinatura e Localização|Selecione sua assinatura e localização.

    Se você for novo no Azure, saiba mais sobre [Grupos de recursos](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), [assinaturas](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [locais](https://azure.microsoft.com/regions) (que também são chamados de regiões).
6. O portal permite apenas que você crie uma sub-rede ao criar uma VNet. Para este tutorial, uma segunda sub-rede é criada depois que a VNet é criada. Posteriormente, você pode conectar recursos acessíveis pela Internet à sub-rede *Pública* e conectar-se a recursos que não estejam acessíveis pela Internet a uma sub-rede privada. Para criar a segunda sub-rede, digite *MyVnet* na caixa *Pesquisar recursos* na parte superior do portal. Quando **MyVnet** aparecer nos resultados da pesquisa, clique nela. Se você tiver várias VNets em sua assinatura com o mesmo nome, verá nomes de grupo de recursos listados em cada VNet com o mesmo nome. Clique no resultado de MyVnet com *MyResourceGroup* sob ele.
7. Na folha **MyVnet** que aparece, clique em **Sub-redes** em **CONFIGURAÇÕES**.
8. Na folha **MyVnet – sub-redes**, clique em **+Sub-rede**.
9. Insira *Privado* para **Nome**, *10.0.1.0/24* para **Intervalo de endereços** na folha **Adicionar sub-rede** e clique em **OK**.
10. Examine as sub-redes na folha **MyVnet – Sub-Redes**. Você verá as sub-redes **Pública** e **Privada** que você criou.
11. **Opcional:** para excluir os recursos criados neste tutorial, conclua as etapas na seção [Excluir recursos](#delete-portal) deste artigo.

## <a name="cli"></a>CLI
Embora os comandos da CLI sejam os mesmos, não importa se você os executa do Windows, do Linux ou do macOS, há diferenças de scripts entre shells do sistema operacional. As instruções a seguir são para executar um script de Busca que contém comandos da CLI:

1. Em um navegador da Internet, abra o [portal](https://portal.azure.com) do Azure e entre com a sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Na parte superior do portal, à direita da barra *Pesquisar recursos*, clique no ícone **>_** para iniciar um Azure Cloud Shell de Busca (Versão Prévia). O painel do cloud shell é exibido na parte inferior do portal e, depois de alguns segundos, apresenta um prompt **username@Azure:~$**. O cloud shell conecta-o automaticamente ao Azure usando as credenciais com as quais você fez a autenticação no portal.
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
    
    # Create an additional subnet within the VNet.
    az network vnet subnet create \
      --name Private \
      --address-prefix 10.0.1.0/24 \
      --vnet-name MyVnet \
      --resource-group MyResourceGroup
    ```
4. Crie um arquivo de script e salve-o. No prompt de cloud shell, digite `nano myscript.sh --nonewlines`. O comando inicia o editor nano GNU com um arquivo vazio myscript.sh. Coloque o mouse dentro da janela do editor, clique com o botão direito do mouse e, em seguida, clique em **Colar**. O armazenamento de cloud shell não persiste entre as sessões. Se você preferir manter o script entre as sessões do cloud shell, configure o [armazenamento persistente](../cloud-shell/persisting-shell-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para o cloud shell. 
5. No teclado, mantenha as teclas **Ctrl + X** pressionadas, digite **Y** e, em seguida, pressione a tecla **Enter** para salvar o arquivo como myscript.sh.
6. No prompt de cloud shell, marque o arquivo como executável com o comando `chmod +x myscript.sh`.
7. Execute o script digitando `./myscript.sh`.
8. Quando o script estiver concluído, examine as sub-redes para a VNet, copiando e colando o seguinte comando no cloud shell de Busca:
    ```azurecli
    az network vnet subnet list --resource-group MyResourceGroup --vnet-name MyVnet --output table
    ```
9. **Opcional:** para excluir os recursos criados neste tutorial, conclua as etapas na seção [Excluir recursos](#delete-cli) deste artigo.

## <a name="powershell"></a>PowerShell
1. Instale a versão mais recente do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do Azure PowerShell. Se você for novo no Azure PowerShell, leia o artigo [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell clicando no botão Iniciar do Windows, digitando **PowerShell** e, em seguida, clicando em **PowerShell** nos resultados da pesquisa.
3. Na janela do PowerShell, digite o comando `login-azurermaccount` para entrar na sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. No navegador, copie o script a seguir:
    ```powershell
    # Create a resource group
    New-AzureRmResourceGroup `
      -Name MyResourceGroup `
      -Location eastus
    
    # Create two subnets
    $Subnet1 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Public `
      -AddressPrefix 10.0.0.0/24
    $Subnet2 = New-AzureRmVirtualNetworkSubnetConfig `
      -Name Private `
      -AddressPrefix 10.0.1.0/24
    
    # Create a virtual network
    $Vnet=New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroup `
      -Location eastus `
      -Name MyVnet `
      -AddressPrefix 10.0.0.0/16 `
      -Subnet $Subnet1,$Subnet2
    #
    ```
5. Para executar o script, clique com o botão direito do mouse na janela do PowerShell.
6. Examine as sub-redes para a VNet copiando o comando a seguir e colando-o na janela do PowerShell:
    ```powershell
    $Vnet = $Vnet.subnets | Format-Table Name, AddressPrefix
    ```
7. **Opcional:** para excluir os recursos criados neste tutorial, conclua as etapas na seção [Excluir recursos](#delete-powershell) deste artigo.

## <a name="template"></a>Modelo

Você pode implantar uma VNet com um modelo do Azure Resource Manager. Para saber mais sobre modelos, leia o artigo [O que é o Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#template-deployment). Para acessar o modelo e aprender sobre seus parâmetros, exiba a página da Web [Criar uma VNet com o modelo de duas sub-redes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). Você pode implantar o modelo usando [portal](#template-portal), [CLI](#template-cli) ou [PowerShell](#template-powershell).

**Opcional:** para excluir os recursos criados neste tutorial, conclua as etapas em qualquer subseção da seção [Excluir recursos](#delete) deste artigo.

### <a name="template-portal"></a>Portal

1. No navegador, abra o modelo de [página da Web](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets).
2. Clique no botão **Implantar no Azure**, que abre a página de entrada no portal do Azure.
3. Entre no portal com sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Insira os seguintes valores para os parâmetros:

    |Parâmetro|Valor|
    |---|---|
    |Assinatura|Selecione sua assinatura.|
    |Grupo de recursos|MyResourceGroup|
    |Local|Selecione um local.|
    |Nome da VNet|MyVnet|
    |Prefixo de Endereço da VNet|10.0.0.0/16|
    |Subnet1Prefix|10.0.0.0/24|
    |Subnet1Name|Público|
    |Subnet2Prefix|10.0.1.0/24|
    |Subnet2Name|Privado|

5. Concorde com os termos e condições, clique em **Comprar** para implantar a VNet.

### <a name="template-cli"></a>CLI

1. Em um navegador da Internet, abra o [portal](https://portal.azure.com) do Azure e entre com a sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
2. Na parte superior do portal, à direita da barra *Pesquisar recursos*, clique no ícone **>_** para iniciar um Azure Cloud Shell de Busca (Versão Prévia). O painel do cloud shell é exibido na parte inferior do portal e, depois de alguns segundos, apresenta um prompt **username@Azure:~$**. O cloud shell conecta-o automaticamente ao Azure usando as credenciais com as quais você fez a autenticação no portal.
3. Crie um grupo de recursos para a VNet digitando o seguinte comando:  `az group create --name MyResourceGroup --location eastus`
4. Você pode implantar o modelo com:
    - **Valores de parâmetro padrão:** digite o seguinte comando:   `az group deployment create --resource-group MyResourceGroup --name VnetTutorial --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`
    - **Valores de parâmetro personalizados:** baixe e modifique o modelo antes de implantá-lo, implante o modelo com parâmetros da linha de comando ou implante o modelo usando um arquivo de parâmetros separado. Você pode baixar os arquivos de modelo e parâmetro clicando no botão **Procurar no GitHub** na página da Web [Criar uma VNet com o modelo de duas sub-redes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). No GitHub, clique no arquivo **azuredeploy.parameters.json** ou **azuredeploy.json** e, em seguida, clique no botão **Raw** para o arquivo. No navegador, copie o conteúdo e salve-o em um arquivo no computador. Modifique os valores de parâmetro no modelo ou implante o modelo com o arquivo de parâmetro separado.  

    Para saber mais sobre como implantar modelos usando esses métodos, digite `az group deployment create --help`.

### <a name="template-powershell"></a>PowerShell

1. Instale a versão mais recente do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do Azure PowerShell. Se você for novo no Azure PowerShell, leia o artigo [Visão geral do Azure PowerShell](/azure/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Inicie uma sessão do PowerShell clicando no botão Iniciar do Windows, digitando **powershell** e, em seguida, clicando em **PowerShell** nos resultados da pesquisa.
3. Na janela do PowerShell, digite o comando `login-azurermaccount` para entrar na sua [conta](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) do Azure. Caso ainda não tenha uma conta, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p).
4. Crie um grupo de recursos para a VNet digitando o seguinte comando:  `New-AzureRmResourceGroup -Name MyResourceGroup -Location eastus`
5. Você pode implantar o modelo com:
    - **Valores de parâmetro padrão:** para fazer isso, digite o seguinte comando:   `New-AzureRmResourceGroupDeployment -Name VnetTutorial -ResourceGroupName MyResourceGroup -TemplateUri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vnet-two-subnets/azuredeploy.json`        
    - **Valores de parâmetro personalizado:** para fazer isso, você pode baixar e modificar o modelo antes de implantá-lo, implantar o modelo com parâmetros da linha de comando ou implantar o modelo usando um arquivo de parâmetros separado. Você pode baixar os arquivos de modelo e parâmetro clicando no botão **Procurar no GitHub** na página da Web [Criar uma VNet com o modelo de duas sub-redes](https://azure.microsoft.com/resources/templates/101-vnet-two-subnets/). No GitHub, clique no arquivo **azuredeploy.parameters.json** ou **azuredeploy.json** e, em seguida, clique no botão **Raw** para o arquivo. No navegador, copie o conteúdo e salve-o em um arquivo no computador. Modifique os valores de parâmetro no modelo ou implante o modelo com o arquivo de parâmetro separado.  

    Para saber mais sobre como implantar modelos usando esses métodos, digite `Get-Help New-AzureRmResourceGroupDeployment`. 

## <a name="delete"></a>Excluir recursos
Depois de concluir este tutorial, você talvez queira excluir o recurso para que não incorra em encargos de uso. Excluir um grupo de recursos exclui também todos os recursos contidos nele.

### <a name="delete-portal"></a>Portal

1. No portal, comece a digitar *MyResourceGroup* na caixa *Pesquisar recursos* na parte superior do portal. Quando **MyResourceGroup** aparece nos resultados da pesquisa, clique nele.
2. Na folha MyResourceGroup que aparece, clique no ícone Excluir na parte superior da folha.
3. Para confirmar a exclusão, digite *MyResourceGroup* na caixa **DIGITAR NOME DO GRUPO DE RECURSOS:** e clique em **Excluir**.

### <a name="delete-cli"></a>CLI

No prompt do cloud shell, digite o seguinte comando: `az group delete --name MyResourceGroup --yes`

### <a name="delete-powershell"></a>PowerShell

No prompt do PowerShell, digite o seguinte comando: `Remove-AzureRmResourceGroup -Name MyResourceGroup`

## <a name="next-steps"></a>Próximas etapas

- Para entender as configurações da VNet e da sub-rede, leia os artigos [Gerenciar VNet](virtual-network-manage-network.md#view-vnet) e [Gerenciar sub-rede](virtual-network-manage-subnet.md#create-subnet). Existem várias opções que permitem criar VNets e sub-redes de produção para atender a diferentes requisitos.
- Filtre o tráfego de entrada e de saída da sub-rede criando e aplicando NSGs ([grupos de segurança de rede](virtual-networks-nsg.md)) a sub-redes.
- Crie uma VM [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e conecte-a à VNet.
- Conecte a VNet a outra VNet no mesmo local com [Emparelhamento de VNet](virtual-network-peering-overview.md).
- Conecte a VNet a uma rede local usando uma [VPN (rede privada virtual) site a site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou um circuito [ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

