---
title: Criar um emparelhamento de rede virtual do Azure | Microsoft Docs
description: Saiba como criar um emparelhamento de rede virtual entre duas redes virtuais.
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/06/2017
ms.author: jdial;narayan;annahar
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: 666165115e80a39d02386719b0d7e64d7ae17749
ms.contentlocale: pt-br
ms.lasthandoff: 06/09/2017


---
# <a name="create-a-virtual-network-peering"></a>Criar um emparelhamento de rede virtual

Neste tutorial, você aprende a criar um emparelhamento de rede virtual entre duas redes virtuais. O emparelhamento de duas redes virtuais permite que recursos em diferentes redes virtuais se comuniquem com a mesma largura de banda e latência, como se os recursos estivessem na mesma rede virtual. Um emparelhamento de rede virtual só pode ser criado entre duas redes virtuais que existem na mesma região do Azure. Para saber mais sobre o emparelhamento de rede virtual, consulte o artigo de visão geral [Emparelhamento de rede virtual](virtual-network-peering-overview.md). 

Se você precisar se conectar a redes virtuais que existem em diferentes regiões do Azure, poderá usar um [Gateway de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure para conectar as redes virtuais. 

Use o [portal do Azure](#portal), o Azure [PowerShell](#powershell), a [CLI](#cli) (interface de linha de comando) do Azure ou um [modelo do Azure Resource Manager](#template) para criar um emparelhamento de rede virtual. Clique em um dos links de ferramentas anteriores para ir diretamente para as etapas para a criação de um emparelhamento de rede virtual usando a ferramenta de sua escolha.

## <a name="portal"></a>Criar emparelhamento – portal do Azure

Conclua as etapas a seguir para criar um emparelhamento de rede virtual entre duas redes virtuais implantadas por meio do Resource Manager que existem na mesma assinatura. Também [emparelhe duas redes virtuais em diferentes assinaturas](#different-subscriptions) ou [emparelhe uma rede virtual (Resource Manager) com uma rede virtual (clássica)](#different-models). 

1. Entre no [Portal do Azure](https://portal.azure.com). A conta com a qual você entra deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
2. Clique em **+ Novo**, em **Rede** e, em seguida, em **Rede virtual**.
3. Na folha **Rede virtual**, na caixa **Selecionar um modelo de implantação**, deixe **Resource Manager** selecionado e, depois, clique em **Criar**.
4. Na folha **Criar rede virtual**, insira ou selecione valores para as seguintes configurações e, depois, clique em **Criar**:
    - **Nome**: *myVnet1*
    - **Espaço de endereço**: *10.0.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.0.0.0/24*
    - **Assinatura**: selecione sua assinatura
    - **Grupo de recursos**: selecione **Criar novo** e insira *myResourceGroup*
    - **Localização**: *Leste dos EUA*
5. Conclua as etapas 2 a 4 novamente especificando os seguintes valores na etapa 4:
    - **Nome**: *myVnet2*
    - **Espaço de endereço**: *10.1.0.0/16*
    - **Nome da sub-rede**: *padrão*
    - **Intervalo de endereços da sub-rede**: *10.1.0.0/24*
    - **Assinatura**: selecione sua assinatura
    - **Grupo de recursos**: selecione **Usar existente** e selecione *myResourceGroup*
    - **Localização**: *Leste dos EUA*
6. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myResourceGroup*. Clique em **myResourceGroup** quando ele for exibido nos resultados da pesquisa. Uma folha é exibida para o grupo de recursos **myresourcegroup**. O grupo de recursos contém as duas redes virtuais criadas nas etapas anteriores.
7. Clique em **myVNet1**.
8. Na folha **myVnet1** exibida, clique em **Emparelhamentos** na lista vertical de opções no lado esquerdo da folha.
9. Na folha **myVnet1 – Emparelhamentos** exibida, clique em **+ Adicionar**
10. Na folha **Adicionar emparelhamento** exibida, insira ou selecione as seguintes opções e clique em **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modelo de implantação de rede virtual**: selecione **Resource Manager**. 
     - **Assinatura**: selecione sua assinatura
     - **Rede virtual**: clique em **Escolher uma rede virtual** e, em seguida, clique em **myVnet2**.
     - **Permitir acesso à rede virtual:** verifique se a opção **Habilitado** está selecionada.
    Nenhuma outra configuração é usada neste tutorial. Para saber mais sobre todas as configurações de emparelhamento, leia [Gerenciar emparelhamentos de rede virtual](virtual-network-manage-peering.md#create-peering).
11. Depois de clicar em **OK** na etapa anterior, a folha **Adicionar emparelhamento** será fechada e você verá a folha **myVnet1 – Emparelhamentos** novamente. Depois de alguns segundos, o emparelhamento que você criou será exibido na folha. **Iniciado** é listado na coluna **STATUS DE EMPARELHAMENTO** do emparelhamento de **myVnet1ToMyVnet2** criado. Você emparelhou a Vnet1 com a Vnet2, mas agora você deve emparelhar a Vnet2 com a Vnet1. O emparelhamento deve ser criado em ambas as orientações para permitir que os recursos nas redes virtuais se comuniquem entre si.
12. Conclua as etapas 6 a 11 novamente para a myVnet2.  Nomeie o emparelhamento *myVnet2ToMyVnet1*.
13. Alguns segundos depois de clicar em **OK** para criar o emparelhamento para a MyVnet2, o emparelhamento de **myVnet2ToMyVnet1** que você acabou de criar será listado com **Conectado** na coluna **STATUS DE EMPARELHAMENTO**.
14. Conclua as etapas 6 a 8 novamente para a MyVnet1. O **STATUS DE EMPARELHAMENTO** do emparelhamento de **myVnet1ToVNet2** agora também é **Conectado**. O emparelhamento é estabelecido com êxito após a exibição de **Conectado** na coluna **STATUS DE EMPARELHAMENTO** de ambas as redes virtuais no emparelhamento.
15. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
16. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas da seção [Excluir recursos](#delete-portal) deste artigo.

Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="cli"></a>Criar emparelhamento – CLI do Azure

O seguinte script:

- Exige a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar atualizar, confira [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funciona em um shell do Bash. Para opções sobre como executar scripts da CLI do Azure no cliente Windows, veja [Execução da CLI do Azure no Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

1. Crie um grupo de recursos e duas redes virtuais.
    ```azurecli-interactive
    #!/bin/bash

    # Variables for common values used throughout the script.
    rgName="myResourceGroup"
    location="eastus"

    # Create a resource group.
    az group create \
      --name $rgName \
      --location $location

    # Create virtual network 1.
    az network vnet create \
      --name myVnet1 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.0.0.0/16

    # Create virtual network 2.
    az network vnet create \
      --name myVnet2 \
      --resource-group $rgName \
      --location $location \
      --address-prefix 10.1.0.0/16
    #
    ```
2. Crie um emparelhamento de rede virtual entre as duas redes virtuais.
    ```azurecli-interactive
    # Get the id for VNet1.
    vnet1Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet1 \
      --query id --out tsv)

    # Get the id for VNet2.
    vnet2Id=$(az network vnet show \
      --resource-group $rgName \
      --name myVnet2 \
      --query id \
      --out tsv)

    # Peer VNet1 to VNet2.
    az network vnet peering create \
      --name myVnet1ToMyVnet2 \
      --resource-group $rgName \
      --vnet-name myVnet1 \
      --remote-vnet-id $vnet2Id \
      --allow-vnet-access

    # Peer VNet2 to VNet1.
    az network vnet peering create \
      --name myVnet2ToMyVnet1 \
      --resource-group $rgName \
      --vnet-name myVnet2 \
      --remote-vnet-id $vnet1Id \
      --allow-vnet-access
    #
    ```
3. Depois que o script for executado, examine os emparelhamentos de cada rede virtual. Copie o seguinte comando e, em seguida, cole-o na janela Comando:

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroup \
      --vnet-name myVnet1 \
      --output table
    #
    ```
Execute o comando anterior novamente, substituindo *myVnet1* por *myVnet2*. A saída dos dois comandos mostra **Conectado** na coluna **PeeringState**.
4. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
5. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-cli) deste artigo.

Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
 
## <a name="powershell"></a>Criar emparelhamento – PowerShell

1. Instale a última versão do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se você estiver conhecendo o Azure PowerShell agora, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Para iniciar uma sessão do PowerShell, acesse **Iniciar**, insira **powershell** e, em seguida, clique em **PowerShell**.
3. Na janela do PowerShell, entre no Azure inserindo o comando `login-azurermaccount`. A conta com a qual você entra deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
4. No navegador, copie o seguinte script e, depois, clique com o botão direito do mouse na janela do PowerShell para executar o script que cria um grupo de recursos e duas redes virtuais:
    ```powershell
    # Variables for common values used throughout the script.
    $rgName='myResourceGroup'
    $location='eastus'

    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name $rgName `
      -Location $location

    # Create virtual network 1.
    $vnet1 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet1' `
      -AddressPrefix '10.0.0.0/16' `
      -Location $location

    # Create virtual network 2.
    $vnet2 = New-AzureRmVirtualNetwork `
      -ResourceGroupName $rgName `
      -Name 'myVnet2' `
      -AddressPrefix '10.1.0.0/16' `
      -Location $location
    #
    ```
5. No navegador, copie o seguinte script e, depois, clique com o botão direito do mouse na janela do PowerShell para executar o script que cria um emparelhamento de rede virtual entre as duas redes virtuais:
    ```powershell
    # Peer VNet1 to VNet2.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet1ToMyVnet2' `
      -VirtualNetwork $vnet1 `
      -RemoteVirtualNetworkId $vnet2.Id

    # Peer VNet2 to VNet1.
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnet2ToMyVnet1' `
      -VirtualNetwork $vnet2 `
      -RemoteVirtualNetworkId $vnet1.Id
    #
    ```
6. Para examinar as sub-redes da rede virtual, copie o seguinte comando e, em seguida, cole-o na janela do PowerShell:

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroup `
      -VirtualNetworkName myVnet1 `
      | Format-Table VirtualNetworkName, PeeringState
    #
    ```

Execute o comando anterior novamente, substituindo *myVnet1* por *myVnet2*. A saída dos dois comandos mostra **Conectado** na coluna **PeeringState**.
7. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
8. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas em [Excluir recursos](#delete-powershell) deste artigo.

Todos os recursos do Azure criados na rede virtual agora podem se comunicar entre si por meio de seus endereços IP. Se você estiver usando a resolução de nomes padrão do Azure para as redes virtuais, os recursos nas redes virtuais não poderão resolver nomes entre as redes virtuais. Se você desejar resolver nomes entre redes virtuais em um emparelhamento, deverá criar seu próprio servidor DNS. Saiba como configurar a [Resolução de nomes usando seu próprio servidor DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

## <a name="template"></a>Criar emparelhamento – modelo do Resource Manager

1. Consulte [Criar um emparelhamento de rede virtual](https://azure.microsoft.com/resources/templates/201-vnet-to-vnet-peering) usando um modelo do Resource Manager. As instruções são fornecidas com o modelo para implantação do modelo usando o portal do Azure, o PowerShell ou a CLI do Azure. Conecte-se à ferramenta de sua escolha para implantar o modelo usando uma conta que tem as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
2. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
3. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas da seção [Excluir recursos](#delete) deste artigo, usando o portal do Azure, o PowerShell ou a CLI do Azure.

## <a name="different-models"></a>Emparelhar redes virtuais criadas por meio de diferentes modelos de implantação

As etapas das seções anteriores explicaram como criar um emparelhamento de rede virtual entre duas redes virtuais criadas por meio do modelo de implantação do Resource Manager. Você também pode criar um emparelhamento entre uma rede virtual (Resource Manager) e uma rede virtual (clássica). Não é possível criar um emparelhamento de rede virtual entre duas redes virtuais criadas por meio do modelo de implantação clássico. Para saber mais sobre os modelos de implantação do Azure, leia o artigo [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Para criar o emparelhamento de rede virtual, conclua as etapas 1 a 11 da seção [Portal](#portal) deste artigo, mas escolha o modelo de implantação **Clássico** ao criar a myVnet2 na etapa 3.

Ao criar uma emparelhamento de rede virtual entre uma rede virtual (Resource Manager) e uma rede virtual (clássica), você configura apenas o emparelhamento com a rede virtual (clássica) na rede virtual (Resource Manager). Depois de criar o emparelhamento da rede virtual (Resource Manager), seu status de emparelhamento será **Atualizando**. O status é alterado automaticamente para **Conectado** após alguns segundos, porque você não precisa criar um emparelhamento para a rede virtual (clássica). 

> [!NOTE]
> Embora isso não seja abordado neste tutorial, você pode ajustar os scripts nas seções [CLI do Azure](#cli) e [PowerShell](#powershell) deste artigo para refletir as diferenças abordadas nesta seção.

## <a name="different-subscriptions"></a>Emparelhar redes virtuais em assinaturas diferentes

As etapas das seções anteriores explicaram como criar um emparelhamento de rede virtual entre duas redes virtuais na mesma assinatura. Você também pode criar um emparelhamento entre redes virtuais em assinaturas diferentes, desde que ambas as assinaturas estejam associadas ao mesmo locatário do Azure Active Directory. Se você ainda não tiver um locatário do AD, [crie um](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#start-from-scratch) rapidamente. Se as assinaturas estiverem associadas a locatários diferentes do Active Directory, não será possível criar um emparelhamento de rede virtual entre redes virtuais em assinaturas diferentes. As etapas para criação do emparelhamento são um pouco diferentes, dependendo do modelo de implantação pelo qual as redes virtuais foram criadas, da seguinte maneira:

### <a name="different-subscriptions-same-deployment-model"></a>Ambas as redes virtuais criadas por meio do Resource Manager

1. Conclua as etapas 1 a 7 da seção [Portal](#portal) deste artigo, mas escolha uma assinatura diferente (associada ao mesmo locatário do Azure Active Directory) para a myVnet2 na etapa 5 da que você escolher para a myVnet1 na etapa 4.
2. Na folha **myVnet1** exibida, clique em **Controle de acesso (IAM)** na lista vertical de opções no lado esquerdo da folha.
3. Na folha **myVnet1 – Controle de acesso (IAM)** exibida, clique em **+ Adicionar**.
4. Na folha **Adicionar permissões** exibida, selecione **Colaborador de rede** na caixa **Função**.
5. Na caixa **Selecionar**, selecione um nome de usuário ou digite um endereço de email para pesquisar um. A lista de usuários mostrada é do mesmo locatário do Azure Active Directory da rede virtual para a qual você está configurando o emparelhamento.
6. Clique em **Salvar**.
7. Na folha **myVnet1 – Controle de acesso (IAM)**, clique em **Propriedades** na lista vertical de opções no lado esquerdo da folha. Copie a **ID DO RECURSO**, que se parece com o seguinte exemplo: /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet1
8. Conclua as etapas 1 a 7 desta seção para a myVnet2.
9. Para garantir que a autorização seja habilitada com êxito, saia do Azure e entre novamente. Se você usou contas diferentes para as duas redes virtuais, saia de ambas as contas e faça logon novamente nelas.
10. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myResourceGroup*. Clique em **myResourceGroup** quando ele for exibido nos resultados da pesquisa. Uma folha é exibida para o grupo de recursos **myresourcegroup**. O grupo de recursos contém as duas redes virtuais criadas nas etapas anteriores.
11. Clique em **myVNet1**.
12. Na folha **myVnet1** exibida, clique em **Emparelhamentos** na lista vertical de opções no lado esquerdo da folha.
13. Na folha **myVnet1 – Emparelhamentos** exibida, clique em **+ Adicionar**
14. Na folha **Adicionar emparelhamento** exibida, insira ou selecione as seguintes opções e clique em **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modelo de implantação de rede virtual**: selecione **Clássico**. 
     - **Sei minha ID do recurso**: marque essa caixa.
     - **ID do recurso**: insira a ID do recurso de myVnet2. Essa caixa é exibida apenas quando a caixa de seleção **Sei minha ID do recurso** é marcada.
     - **Permitir acesso à rede virtual:** verifique se a opção **Habilitado** está selecionada.
    Nenhuma outra configuração é usada neste tutorial. Para saber mais sobre todas as configurações de emparelhamento, leia [Gerenciar emparelhamentos de rede virtual](virtual-network-manage-peering.md#create-peering).
15. Depois de clicar em **OK** na etapa anterior, a folha **Adicionar emparelhamento** será fechada e você verá a folha **myVnet1 – Emparelhamentos** novamente. Depois de alguns segundos, o emparelhamento que você criou será exibido na folha. **Iniciado** é listado na coluna **STATUS DE EMPARELHAMENTO** do emparelhamento de **myVnet1ToMyVnet2** criado. Você emparelhou a Vnet1 com a Vnet2, mas agora você deve emparelhar a Vnet2 com a Vnet1. O emparelhamento deve ser criado em ambas as orientações para permitir que os recursos nas redes virtuais se comuniquem entre si.
16. Conclua as etapas 10 a 14 desta seção novamente para a myVnet2.  Nomeie o emparelhamento *myVnet2ToMyVnet1* e insira a ID do recurso para a myVnet1 na **ID do Recurso**.
17. Alguns segundos depois de clicar em **OK** para criar o emparelhamento para a MyVnet2, o emparelhamento de **myVnet2ToMyVnet1** que você acabou de criar será listado com **Conectado** na coluna **STATUS DE EMPARELHAMENTO**.
18. Conclua as etapas 10 a 11 desta seção novamente para a MyVnet1. O **STATUS DE EMPARELHAMENTO** do emparelhamento de **myVnet1ToVNet2** agora também é **Conectado**. O emparelhamento é estabelecido com êxito após a exibição de **Conectado** na coluna **STATUS DE EMPARELHAMENTO** de ambas as redes virtuais no emparelhamento.
19. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
20. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas da seção [Excluir recursos](#delete-portal) deste artigo.

### <a name="different-subscriptions-different-deployment-models"></a>Uma rede virtual (Resource Manager), uma rede virtual (clássica)
 
[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-preview.md)]

1. Registre-se para a versão prévia. Só é possível emparelhar redes virtuais criadas por meio de diferentes modelos de implantação que existem em assinaturas diferentes quando você estiver registrado para a versão prévia. Não é possível se registrar para a versão prévia no portal ou usando a CLI do Azure. Só é possível se registrar para a versão prévia usando o PowerShell. Para se registrar para a versão prévia, conclua as seguintes tarefas:
    - Instale a última versão do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se você estiver conhecendo o Azure PowerShell agora, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Para iniciar uma sessão do PowerShell em um computador Windows, acesse **Iniciar**, insira **powershell** e, em seguida, clique em **PowerShell**.
    - Na janela do PowerShell, entre no Azure inserindo o comando `login-azurermaccount`. A conta com a qual você entra deve ter as permissões necessárias para criar um emparelhamento de rede virtual. Consulte a seção [Permissões](#permissions) deste artigo para obter detalhes.
    - Registre a funcionalidade de versão prévia para ambas as assinaturas do Azure e insira os seguintes comandos do PowerShell: 
    
    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    #
    ```
    Registre-se para a versão prévia enquanto estiver conectado a cada assinatura. Só continue com as etapas a seguir quando a saída de **RegistrationState** recebida depois de inserir o comando a seguir for **Registrado** em ambas as assinaturas.

    ```powershell
    Get-AzureRmProviderFeature `
      -FeatureName AllowClassicCrossSubscriptionPeering `
      -ProviderNamespace Microsoft.Network
    #
    ```

2. Conclua as etapas 1 a 7 da seção [Portal](#portal) deste artigo com as seguintes alterações:
    - Escolha uma assinatura diferente (associada ao mesmo locatário do Azure Active Directory) para a myVnet2 na etapa 5 da que você escolher para a myVnet1 na etapa 4.
    - Escolha **Clássica** na etapa 3 ao criar a myVnet2.
3. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myResourceGroup*. Clique em **myResourceGroup** quando ele for exibido nos resultados da pesquisa. Uma folha é exibida para o grupo de recursos **myresourcegroup**. O grupo de recursos contém as duas redes virtuais criadas nas etapas anteriores.
4. Clique em **myVNet1**.
5. Conclua as etapas 2 a 9 da seção [Ambas as redes virtuais criadas por meio do Resource Manager](#different-subscriptions-same-deployment-model) deste artigo.
6. Na caixa **Pesquisar recursos** na parte superior do portal, digite *myResourceGroup*. Clique em **myResourceGroup** quando ele for exibido nos resultados da pesquisa. Uma folha é exibida para o grupo de recursos **myresourcegroup**. O grupo de recursos contém as duas redes virtuais criadas nas etapas anteriores.
7. Clique em **myVNet1**.
8. Na folha **myVnet1** exibida, clique em **Emparelhamentos** na lista vertical de opções no lado esquerdo da folha.
9. Na folha **myVnet1 – Emparelhamentos** exibida, clique em **+ Adicionar**
10. Na folha **Adicionar emparelhamento** exibida, insira ou selecione as seguintes opções e clique em **OK**:
     - **Nome**: *myVnet1ToMyVnet2*
     - **Modelo de implantação de rede virtual**: selecione **Clássico**. 
     - **Sei minha ID do recurso**: marque essa caixa.
     - **ID do recurso**: insira a ID do recurso de myVnet2. Essa caixa é exibida apenas quando a caixa de seleção **Sei minha ID do recurso** é marcada.
     - **Permitir acesso à rede virtual:** verifique se a opção **Habilitado** está selecionada.
    Nenhuma outra configuração é usada neste tutorial. Para saber mais sobre todas as configurações de emparelhamento, leia [Gerenciar emparelhamentos de rede virtual](virtual-network-manage-peering.md#create-peering).

    Ao criar uma emparelhamento de rede virtual entre uma rede virtual (Resource Manager) e uma rede virtual (clássica), você configura apenas o emparelhamento com a rede virtual (clássica) na rede virtual (Resource Manager). Depois de criar o emparelhamento da rede virtual (Resource Manager), seu status de emparelhamento será **Atualizando**. O status é alterado automaticamente para **Conectado** após alguns segundos, porque você não precisa criar um emparelhamento para a rede virtual (clássica). 
11. **Opcional**: embora a criação de máquinas virtuais não seja abordada neste tutorial, você poderá criar uma máquina virtual em cada rede virtual e conectar-se de uma máquina virtual a outra, para validar a conectividade.
12. **Opcional**: para excluir os recursos criados neste tutorial, conclua as etapas da seção [Excluir recursos](#delete-portal) deste artigo.

> [!NOTE]
> Embora isso não seja abordado neste tutorial, você pode ajustar os scripts nas seções [CLI do Azure](#cli) e [PowerShell](#powershell) deste artigo para refletir as diferenças abordadas nesta seção.

## <a name="permissions"></a>Permissões

As contas usadas para criar um emparelhamento de rede virtual devem ter a função ou as permissões necessárias. Por exemplo, se você pretender emparelhar duas redes virtuais, chamadas VNet1 e VNet2, sua conta deverá ser atribuído a seguinte função ou permissões mínimas para cada rede virtual:
    
|Rede virtual|Modelo de implantação|Função|Permissões|
|---|---|---|---|
|VNet1|Gerenciador de Recursos|[Colaborador de rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Clássico|[Colaborador de rede clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|N/D|
|VNet2|Gerenciador de Recursos|[Colaborador de rede](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Clássico|[Colaborador de rede clássica](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Saiba mais sobre [funções internas](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e como atribuir permissões específicas a [funções personalizadas](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (somente para o Resource Manager).

## <a name="delete"></a>Excluir recursos
Ao concluir este tutorial, talvez você deseje excluir os recursos criados no tutorial para não incorrer em encargos de uso. A exclusão de um grupo de recursos também exclui todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa do portal, insira **myResourceGroup**. Nos resultados da pesquisa, clique em **myResourceGroup**.
2. Na folha **myResourceGroup**, clique no ícone **Excluir**.
3. Para confirmar a exclusão, na caixa **DIGITAR O NOME DO GRUPO DE RECURSOS**, insira **myResourceGroup** e, depois, clique em **Excluir**.

### <a name="delete-cli"></a>Azure CLI

Em um shell de comando do Linux, macOS ou Windows, insira o comando a seguir. Como alternativa, clique no botão Experimentar na parte superior direita do bloco de código a seguir para iniciar o Cloud Shell. Em seguida, use o botão Copiar para copiar e colar o código de exemplo no Cloud Shell.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

No prompt de comando do PowerShell, insira o seguinte comando:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

- Familiarize por completo com [comportamentos e restrições importantes do emparelhamento de rede virtual](virtual-network-manage-peering.md#about-peering) antes de criar um emparelhamento de rede virtual para uso em produção.
- Saiba mais sobre todas as [configurações de emparelhamento de rede virtual](virtual-network-manage-peering.md#create-peering).
- Saiba como [criar uma topologia de rede hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) com o emparelhamento de rede virtual.

