---
title: "Criar uma rota definida pelo usuário para rotear tráfego de rede por meio de um dispositivo de rede virtual – portal do Azure | Microsoft Docs"
description: "Saiba como criar uma rota definida pelo usuário para substituir o roteamento padrão do Azure pelo roteamento de tráfego de rede por meio de um dispositivo de rede virtual."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2017
ms.author: jdial
ms.openlocfilehash: 736e48f9651d89a1f4e8e0ae72cdffebb8e9c6e0
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="create-a-user-defined-route---azure-portal"></a>Criar uma rota definida pelo usuário - portal do Azure

Neste tutorial, saiba como criar rotas definidas pelo usuário para rotear o tráfego entre duas sub-redes de [rede virtual](virtual-networks-overview.md) por meio de um dispositivo de rede virtual. Um dispositivo de rede virtual é uma máquina virtual que executa um aplicativo de rede, tal como um firewall. Para saber mais sobre soluções de virtualização de rede pré-configuradas que você pode implantar em uma rede virtual do Azure, consulte o [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

Quando você cria sub-redes em uma rede virtual, o Azure cria [rotas de sistema](virtual-networks-udr-overview.md#system-routes) padrão que habilitam recursos em todas as sub-redes para se comunicarem entre si, conforme mostrado na figura a seguir:

![Rotas padrão](./media/create-user-defined-route/default-routes.png)

Neste tutorial, você criará uma rede virtual com sub-redes públicas, privadas e DMZ, conforme mostrado na figura a seguir. Normalmente, os servidores Web podem ser implantados em uma sub-rede pública, enquanto um aplicativo ou servidor de banco de dados pode ser implantado em uma sub-rede privada. Você cria uma máquina virtual para atuar como uma solução de virtualização de rede na sub-rede DMZ e, opcionalmente, cria uma máquina virtual em cada sub-rede que se comunica por meio do dispositivo de rede virtual. Todo o tráfego entre as sub-redes públicas e privadas é roteado por dispositivo, conforme mostrado na figura a seguir:

![Rotas definidas pelo usuário](./media/create-user-defined-route/user-defined-routes.png)

Este artigo fornece etapas para criar uma rota definida pelo usuário usando o modelo de implantação do Resource Manager, que é o modelo de implantação recomendado ao criar rotas definidas pelo usuário. Se você precisar criar uma rota definida pelo usuário (clássica), consulte [Criar uma rota definida pelo usuário (clássica)](virtual-network-create-udr-classic-ps.md). Se você não estiver familiarizado com os modelos de implantação do Azure, confira [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Para saber mais sobre as rotas definidas pelo usuário, confira [Visão geral de rotas definidas pelo usuário](virtual-networks-udr-overview.md#user-defined).

## <a name="create-routes-and-network-virtual-appliance"></a>Criar rotas e solução de virtualização de rede

1. **Pré-requisitos**: criar uma rede virtual com duas sub-redes completando as etapas em [Criar uma rede virtual](#create-a-virtual-network).
2. Depois de criar a rede virtual, em um navegador da Internet, vá para o [portal do Azure](https://portal.azure.com). Faça logon usando sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account).
3. Na caixa **Pesquisar recursos** na parte superior do portal, insira *myResourceGroup*. Clique em **myResourceGroup** quando ele for exibido nos resultados da pesquisa. O grupo de recursos foi criado como parte do pré-requisito.
4. Clique em **myVnet**, conforme mostrado na figura abaixo:

    ![Configurações de adaptador de rede](./media/create-user-defined-route/virtual-network.png)

5. Crie uma sub-rede para o dispositivo de rede virtual:
 
    - Em **myVnet**, clique em **sub-redes** em **CONFIGURAÇÕES** no lado esquerdo.
    - Clique em **+ Sub-rede**, conforme mostrado na figura abaixo:

        ![Sub-redes](./media/create-user-defined-route/subnets.png) 
    - Insira os seguintes valores em **Adicionar sub-rede** e clique em **OK**:

        |Configuração|Valor|
        |-----|-----|
        |Nome|Rede de Perímetro|
        |Intervalo de endereços (bloco CIDR)|10.0.2.0/24|

6. Crie uma máquina virtual de solução de virtualização de rede:

    - No lado esquerdo do portal, clique em **+ Novo**, clique em **Computação**e clique em **Datacenter do Windows Server 2016** ou em **Ubuntu Server 16.04 LTS**.
    - Insira os seguintes valores na folha **Informações Básicas** exibida e clique em **OK**:

        |Configuração|Valor|
        |---|---|
        |Nome|myVm-Nva|
        |Nome de usuário|azureuser|
        |Senha e Confirmar senha|Uma senha de sua escolha|
        |Assinatura|Selecione sua assinatura|
        |Grupo de recursos|Clique em **Usar existente** e selecione **myResourceGroup**|
        |Local|Leste dos EUA|
    - Na folha **Escolha um tamanho** exibida, clique em **DS1_V2 Standard** e em **Selecionar**.
    - Na folha **Configurações** exibida, clique em **Rede virtual**. Clique em **myVnet** na folha **Escolher rede virtual** exibida.
    - Na folha **Configurações**, clique em **Sub-redes**. Clique em **DMZ** na folha **Escolher sub-rede** exibida. 
    - Mantenha os padrões para as configurações restantes e clique em **OK**.
    - Clique em **Criar** na folha **Criar** exibida. A implantação da máquina virtual leva alguns minutos.

    > [!NOTE]
    > Além de criar a máquina virtual, o portal do Azure cria um endereço IP público e o atribui à máquina virtual por padrão. Se você está implantando a máquina virtual em um ambiente de produção, pode escolher não atribuir um endereço IP público à máquina virtual. Em vez disso, você pode acessar a solução de virtualização de rede de outras máquinas virtuais na rede virtual. Para saber mais sobre endereços IP públicos, confira [Gerenciar um endereço IP público](virtual-network-public-ip-address.md).

7. Atribua um endereço IP privado estático e habilite o encaminhamento IP para o adaptador de rede do portal criado na etapa anterior. 
    - Na caixa **Pesquisar recursos** na parte superior da página, digite *myVm Nva*.
    - Clique em **myVm-Nva** quando ele for exibido nos resultados da pesquisa.
    - Clique em **Rede** em **CONFIGURAÇÕES** no lado esquerdo.
    - Clique no nome do adaptador de rede em **myVm-Nva - Adaptadores de rede**. O nome é **myvm nva***X*, em que *X* é um número atribuído pelo portal.
    - Clique em **Configurações de IP** em **CONFIGURAÇÕES** do adaptador de rede, conforme mostrado na figura abaixo:

        ![Configurações de adaptador de rede](./media/create-user-defined-route/network-interface-settings.png)
        
    - Clique em **Habilitado** na configuração de **Encaminhamento IP**e clique em **Salvar**. O encaminhamento de IP deve ser habilitado para cada adaptador de rede que recebe o tráfego não endereçado a um endereço IP atribuído a ele. A habilitação do encaminhamento IP desabilita a verificação de origem/destino do Azure para o adaptador de rede.
    - Clique em **ipconfig1** na lista de configurações de IP.
    - Clique em **Estático** para **Atribuição** do endereço IP privado em **ipconfig1**, conforme mostrado na figura abaixo:

        ![Configuração de IP](./media/create-user-defined-route/ip-configuration.png)
    - Conforme mostrado na figura anterior, digite *10.0.2.4* em **Endereço IP** nas **configurações de endereço IP privado**. A atribuição de um endereço IP estático ao adaptador de rede faz com que o endereço não mude durante a vida útil da máquina virtual à qual o adaptador está anexado. O endereço inserido não está atualmente atribuído a outro recurso na sub-rede DMZ em que o adaptador de rede está. 
    - Para salvar a configuração, clique em **Salvar** em **ipconfig1**. Não feche a caixa de ipconfig1 até que você receba uma notificação no portal do que o adaptador de rede foi salvo.
 
8. Crie duas tabelas de rotas. Tabelas de rotas contêm zero ou mais rotas:

    - No lado esquerdo do portal, clique em **+Novo** > **Rede** > **Tabela de rotas**.
    - Insira os seguintes valores em **Criar uma tabela de rotes**e clique em **Criar**:

        |Configuração|Valor|
        |---|---|
        |Nome|myRouteTable-Public|
        |Assinatura|Selecione sua assinatura|
        |Grupo de recursos|Clique em **Usar existente** e selecione **myResourceGroup**|
        |Local|Leste dos EUA|
    
    - Conclua as subetapas anteriores da etapa 8 novamente, mas nomeie a tabela de rotas *myRouteTable-Private*.
9. Adicione rotas à tabela de rotas *myRouteTable-Public* e associe a tabela de rotas à sub-rede *Pública*:

    - Na caixa **Pesquisar recursos** na parte superior do portal, insira *myRouteTable-Public*. Clique em **myRouteTable-Public** quando ele for exibido nos resultados da pesquisa.
    - Em **myRouteTable-Public**, clique em **Rotas** na lista de **CONFIGURAÇÕES**.
    - Clique em **+ Adicionar** em **myRouteTable-Public - Rotas**.
    -  Por padrão, o Azure roteia tráfego entre todas as sub-redes contidas em uma rede virtual. Crie uma rota para alterar o roteamento padrão do Azure para que o tráfego da sub-rede *pública* seja roteado por meio da NVA, em vez de diretamente para a sub-rede *privada*. Insira os seguintes valores na folha **Adicionar rota** exibida e clique em **OK**:

        |Configuração|Valor|Explicação|
        |---|---|---|
        |Nome da rota|ToPrivateSubnet|Essa rota direciona o tráfego para a sub-rede privada por meio da solução de virtualização de rede.|
        |Prefixo de endereço|10.0.1.0/24| O tráfego enviado para algum endereço nesse prefixo de endereço (10.0.1.0 - 10.0.1.254) é enviado para a solução de virtualização de rede.|
        |Tipo do próximo salto| Selecione **Dispositivo virtual**||
        |Endereço do próximo salto|10.0.2.4| O endereço IP privado estático do adaptador de rede anexado à solução de virtualização de rede. O único tipo de salto para o qual você pode especificar um endereço é **Solução de virtualização**.|

    - Em **myRouteTable-Public**, clique em **Sub-redes** em **Configurações**. 
    - Clique em **+ Associar** em **myRouteTable-Public - Sub-redes**.
    - Clique em **Rede virtual** em **Associar sub-rede** e clique em **myVnet**.
    - Clique em **Sub-rede** em **Associar sub-rede** e clique em **Pública** em **Escolher sub-rede**. 
    - Para salvar a configuração, clique em **OK** em **Associar sub-rede**. Uma sub-rede pode ter zero ou uma tabela de rotas associada a ela.
10. Conclua a etapa 9 novamente procurando **myRouteTable-Private**, criando uma rota com as seguintes configurações e associando a tabela de rotas à sub-rede **Privada** da rede virtual **myVnet**:

    |Configuração|Valor|Explicação|
    |---|---|---|
    |Nome da rota|ToPublicSubnet|Essa rota direciona o tráfego para a sub-rede púbica por meio da solução de virtualização de rede.|
    |Prefixo de endereço|10.0.0.0/24| O tráfego enviado para algum endereço nesse prefixo de endereço (10.0.0.0 - 10.0.1.254) é enviado para a solução de virtualização de rede.|
    |Tipo do próximo salto| Selecione **Dispositivo virtual**||
    |Endereço do próximo salto|10.0.2.4||

    O tráfego de rede entre todos os recursos nas sub-redes privadas e públicas flui pela solução de virtualização de rede. 
11. **Opcional:** crie uma máquina virtual nas sub-redes pública e privada e valide que a comunicação entre as máquinas virtuais é roteada através da solução de virtualização de rede completando as etapas em [Validar roteamento](#validate-routing). 
12. **Opcional**: exclua os recursos criados neste tutorial concluindo as etapas em [Excluir recursos](#delete-resources).

## <a name="validate-routing"></a>Validar roteamento

1. Se você ainda não fez isso, conclua as etapas em [Criar rotas e solução de virtualização de rede](#create-routes-and-network-virtual-appliance).
2. Clique no botão **Experimente** na caixa a seguir, que abre o Azure Cloud Shell. Se solicitado, faça logon no Azure usando a [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). O Azure Cloud Shell é um shell de busca gratuito com a interface de linha de comando do Azure pré-instalada. 

    Os scripts a seguir criam duas máquinas virtuais, uma na sub-rede *Pública* e outra na *Privada*. Os scripts também habilitam o encaminhamento IP para o adaptador de rede dentro do sistema operacional da NVA para habilitar o sistema operacional a rotear o tráfego por meio do adaptador de rede. Uma NVA de produção normalmente inspeciona o tráfego antes de roteá-lo, mas neste tutorial, a NVA simples apenas roteia o tráfego, sem inspecioná-lo. 

    Clique no botão **Copiar** dos scripts **Linux** ou **Windows** a seguir e cole o conteúdo do script em um editor de texto. Altere a senha para a variável *adminPassword* e, em seguida, cole o script no Azure Cloud Shell. Execute o script para o sistema operacional que você selecionou ao criar o dispositivo de rede virtual na etapa 6 de [Criar rotas e solução de virtualização de rede](#create-routes-and-network-virtual-appliance). 

    **Linux**

    ```azurecli-interactive
    #!/bin/bash

    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image UbuntuLTS \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.    
    az vm extension set \
      --resource-group $rgName \
      --vm-name myVm-Nva \
      --name customScript \
      --publisher Microsoft.Azure.Extensions \
      --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
    ```

    **Windows**

    ```azurecli-interactive

    #!/bin/bash
    #Set variables used in the script.
    rgName="myResourceGroup"
    location="eastus"
    adminPassword=ChangeToYourPassword
    
    # Create a virtual machine in the Public subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Public \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Public \
      --public-ip-address myPublicIp-Public \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Public \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Create a virtual machine in the Private subnet.
    az vm create \
      --resource-group $rgName \
      --name myVm-Private \
      --image win2016datacenter \
      --vnet-name myVnet \
      --subnet Private \
      --public-ip-address myPublicIp-Private \
      --admin-username azureuser \
      --admin-password $adminPassword

    # Allow pings through the Windows Firewall.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Private \
      --resource-group $rgName \
      --settings '{"commandToExecute":"netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}'

    # Enable IP forwarding for the network interface in the NVA virtual machine's operating system.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1"}'

    # Restart the NVA virtual machine.
    az vm extension set \
      --publisher Microsoft.Compute \
      --version 1.9 \
      --name CustomScriptExtension \
      --vm-name myVm-Nva \
      --resource-group $rgName \
      --settings '{"commandToExecute":"powershell.exe Restart-Computer -ComputerName myVm-Nva -Force"}'
    ```

3. Valide a comunicação entre as máquinas virtuais nas sub-redes pública e privada. 

    - Abra uma conexão [SSH](../virtual-machines/linux/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Linux) ou de [Área de Trabalho Remota](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json#connect-to-vm) (Windows) para o endereço IP público da máquina virtual *myVm-Public*.
    - Em um prompt de comando na máquina virtual *myVm-Public*, digite `ping myVm-Private`. Você recebe respostas porque a NVA roteia o tráfego da sub-rede pública para a privada.
    - Da máquina virtual *myVm-Public*, execute uma rota de rastreamento entre as máquinas virtuais nas sub-redes pública e privada. Digite o comando apropriado a seguir, dependendo de qual sistema operacional você instalou nas máquinas virtuais nas sub-redes Pública e Privada:
        - **Windows**: Em um prompt de comando, execute o comando `tracert myvm-private`.
        - **Ubuntu**: Execute o comando `tracepath myvm-private`.
      O tráfego passa por 10.0.2.4 (a NVA) antes de alcançar 10.0.1.4 (a máquina virtual na sub-rede Privada). 
    - Conclua as etapas anteriores conectando-se à máquina virtual *myVm-Private* e executando ping na máquina virtual *myVm-Public*. A rota de rastreamento mostra a comunicação viajando através de 10.0.2.4 antes de alcançar 10.0.0.4 (a máquina virtual na sub-rede pública).
    - **Opcionalmente**: para validar o próximo salto entre duas máquinas virtuais no Azure, use a funcionalidade de próximo salto do Observador de Rede do Azure. Antes de usar o Observador de Rede, você deve primeiro [Criar uma instância do Observador de Rede do Azure](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para a região na qual você deseja usá-lo. Neste tutorial, a região Leste dos EUA é usada. Depois de habilitar uma instância do Observador de Rede para a região, digite o comando a seguir para ver as informações do próximo salto entre as máquinas virtuais nas sub-redes Pública e Privada:
     
        ```azurecli-interactive
        az network watcher show-next-hop --resource-group myResourceGroup --vm myVm-Public --source-ip 10.0.0.4 --dest-ip 10.0.1.4
        ```

       A saída retorna *10.0.2.4* como o **nextHopIpAddress** e *VirtualAppliance* como o **nextHopType**. 

> [!NOTE]
> Para ilustrar os conceitos neste tutorial, os endereços IP públicos são atribuídos a máquinas virtuais nas sub-redes Pública e Privada, enquanto todo o acesso de porta de rede é habilitado dentro do Azure para ambas as máquinas virtuais. Ao criar máquinas virtuais para uso em produção, você não pode atribuir endereços IP públicos a elas e pode filtrar o tráfego de rede para a sub-rede Privada implantando uma solução de virtualização de rede diante dela ou atribuindo um grupo de segurança de rede às sub-redes, ao adaptador de rede ou a ambos. Para saber mais sobre grupos de segurança de rede, confira [Grupos de segurança de rede](virtual-networks-nsg.md).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Este tutorial requer uma rede virtual existente com duas sub-redes. Clique no botão **Experimente** da caixa a seguir para criar uma rede virtual rapidamente. Clicar no botão **Experimente** abre o [Azure Cloud Shell](../cloud-shell/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Embora o Cloud Shell execute o PowerShell ou um shell Bash, nesta seção o shell Bash é usado para criar a rede virtual. O shell Bash tem a interface de linha de comando do Azure instalada. Se solicitado pelo Cloud Shell, faça logon no Azure usando a [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account). Se não tiver uma conta do Azure, você poderá assinar uma versão de [avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p). Para criar a rede virtual usada neste tutorial, clique no botão **Copiar** na caixa a seguir e, em seguida, cole o script no Azure Cloud Shell:

```azurecli-interactive
#!/bin/bash

#Set variables used in the script.
rgName="myResourceGroup"
location="eastus"

# Create a resource group.
az group create \
  --name $rgName \
  --location $location

# Create a virtual network with one subnet named Public.
az network vnet create \
  --name myVnet \
  --resource-group $rgName \
  --address-prefixes 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24

# Create an additional subnet named Private in the virtual network.
az network vnet subnet create \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --vnet-name myVnet \
  --resource-group $rgName
```

Para saber mais sobre como usar o portal, o PowerShell ou um modelo do Azure Resource Manager para criar uma rede virtual, consulte [Criar uma rede virtual](virtual-networks-create-vnet-arm-pportal.md).

## <a name="delete-resources"></a>Excluir recursos

Após a conclusão este tutorial, convém excluir os recursos criados, para não incorrer em encargos de uso. A exclusão de um grupo de recursos também exclui todos os recursos que estão no grupo de recursos. Com o portal aberto, conclua as seguintes etapas:

1. Na caixa de pesquisa do portal, insira **myResourceGroup**. Nos resultados da pesquisa, clique em **myResourceGroup**.
2. Na folha **myResourceGroup**, clique no ícone **Excluir**.
3. Para confirmar a exclusão, na caixa **DIGITAR O NOME DO GRUPO DE RECURSOS**, insira **myResourceGroup** e, depois, clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Crie uma [solução de virtualização de rede altamente disponível](/azure/architecture/reference-architectures/dmz/nva-ha?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Soluções de virtualização de rede geralmente possuem vários adaptadores de rede e endereços IP atribuídos a eles. Saiba como [adicionar adaptadores de rede a uma máquina virtual existente](virtual-network-network-interface-vm.md#vm-add-nic) e [adicionar endereços IP a um adaptador de rede](virtual-network-network-interface-addresses.md#add-ip-addresses). Embora todos os tamanhos de máquina virtual possam ter pelo menos dois adaptadores de rede anexados a eles, o tamanho de cada máquina virtual dá suporte a um número máximo de adaptadores de rede. Para saber a quantos adaptadores de rede cada tamanho de máquina virtual dá suporte, consulte os tamanhos de máquina virtual do [Windows](../virtual-machines/windows/sizes.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) e [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- Crie uma rota definida pelo usuário para realizar túnel forçado de tráfego local por meio de uma [conexão de VPN site a site](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
