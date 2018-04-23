---
title: Balancear carga de VMs entre zonas de disponibilidade - Portal do Azure | Microsoft Docs
description: Criar um Load Balancer Standard com front-end com redundância de zona para balancear carga de VMs entre zonas de disponibilidade utilizando o Portal do Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/18
ms.author: kumud
ms.openlocfilehash: ad476922342844a908961960407eb344711932f5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Balancear carga de VMs entre zonas de disponibilidade com um Load Balancer Standard utilizando o Portal do Azure

Este artigo orienta a criação de um Load Balancer Standard público com um front-end com redundância de zona para obter redundância de zona sem depender de vários registros DNS. Um único endereço IP de front-end em um Load Balancer Standard é automaticamente com redundância de zona. Usando um front-end com redundância de zona para o balanceador de carga, com um único endereço IP, será possível acessar qualquer VM em uma rede virtual em uma região que esteja em todas as Zonas de Disponibilidade. Use zonas de disponibilidade para proteger seus aplicativos e dados de uma improvável falha ou perda de um datacenter inteiro. Com redundância de zona, uma ou mais Zonas de Disponibilidade podem falhar e o caminho de dados sobreviver enquanto uma zona na região permanecer íntegra. 

Para obter mais informações sobre o uso de Zonas de Disponibilidade com o Load Balancer Standard, consulte [Zonas de disponibilidade e Load Balancer Standard](load-balancer-standard-availability-zones.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Criar um Load Balancer Standard público

Um balanceador de carga padrão só oferece suporte a um endereço IP público padrão. Ao criar um novo IP público durante a criação do balanceador de carga, ele é configurado automaticamente como uma versão de SKU Standard e, além disso, é automaticamente com redundância de zona.

1. No canto superior esquerdo da tela, clique em **Criar um recurso** > **Rede** > **Load Balancer**.
2. Na página **Criar um balanceador de carga**, insira estes valores para o balanceador de carga:
    - *myLoadBalancer* – para o nome do balanceador de carga.
    - **Public** – para o tipo do balanceador de carga.
     - *myPublicIP* - para o novo endereço IP público que você cria. Para fazer isso, clique em **Escolher um endereço IP público** e, em seguida, clique em **Criar novo**. Para o tipo de nome *myPublicIP*, a SKU é Standard por padrão e selecione **Com redundância de zona** para **Zona de disponibilidade**.
    - *myResourceGroupLBAZ* -  para o nome do novo grupo de recursos que você cria.
    - **westeurope** – para o local.
3. Clique em **Criar** para criar o balanceador de carga.
   
    ![Criar um balanceador de carga](./media/load-balancer-standard-public-availability-zones-portal/1a-load-balancer.png)


## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você cria uma rede virtual, cria máquinas virtuais em zonas diferentes (zona 1, zona 2 e zona 3) para a região adicionar ao pool de back-end do balanceador de carga e instala IIS nas máquinas virtuais para ajudar a testar o balanceador de carga com redundância de zona. Portanto, se uma região falhar, a investigação de integridade da VM na mesma zona falhará e o tráfego continuará sendo atendido pelas VMs nas outras regiões.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
1. Na parte superior esquerda da tela, clique em **Criar um recurso** > **Rede** > **Rede virtual** e insira esses valores para a rede virtual:
    - *myVNet* – para o nome da rede virtual.
    - *myResourceGroupLBAZ* - para o nome do grupo de recursos existente
    - *myBackendSubnet* – para o nome da sub-rede.
2. Clique em **Criar** para criar a rede virtual.

    ![Criar uma rede virtual](./media/load-balancer-standard-public-availability-zones-portal/2-load-balancer-virtual-network.png)

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. No canto superior esquerdo da tela, clique em **Criar um recurso**, na caixa de pesquisa digite *Grupo de Segurança de Rede* e, na página do grupo de segurança de rede, clique em **Criar**.
2. Na página Criar grupo de segurança de rede, insira estes valores:
    - *myNetworkSecurityGroup*  - para o nome do grupo de segurança de rede.
    - *myResourceGroupLBAZ* - para o nome do grupo de recursos existente.
   
![Criar uma rede virtual](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-nsg-rules"></a>Criar regras NSG

Nesta seção, você cria regras do NSG para permitir conexões de entrada usando HTTP e RDP, utilizando o Portal do Azure.

1. No Portal do Azure, clique em **Todos os recursos** no menu esquerdo e, em seguida, pesquise e clique em **myNetworkSecurityGroup** localizado no grupo de recursos **myResourceGroupLBAZ**.
2. Em **Configurações**, clique em **Regras de segurança de entrada** e clique em **Adicionar**.
3. Insira esses valores para a regra de segurança de entrada denominada *myHTTPRule* para permitir conexões de entrada HTTP usando a porta 80:
    - *Service Tag* – para **Fonte**.
    - *Internet* – para **Marca de serviço de fonte**
    - *80* - para os **Intervalos de porta de destino**
    - *TCP* – para **Protocolo**
    - *Allow* – para **Ação**
    - *100* - para **Prioridade**
    - *myHTTPRule* - para nome
    - *Allow HTTP* - para descrição
4. Clique em **OK**.
 
 ![Criar uma rede virtual](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Repita as etapas 2 a 4 para criar outra regra denominada *myRDPRule* para permitir uma conexão de RDP de entrada usando a porta 3389 com os seguintes valores:
    - *Service Tag* – para **Fonte**.
    - *Internet* – para **Marca de serviço de fonte**
    - *3389* - para os **Intervalos de porta de destino**
    - *TCP* – para **Protocolo**
    - *Allow* – para **Ação**
    - *200* - para **Prioridade**
    - *myRDPRule* - para nome
    - *Allow RDP* - para descrição


### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No canto superior esquerdo da tela, clique em **Criar um recurso** > **Computação** > **Windows Server 2016 Datacenter** e insira esses valores para a máquina virtual:
    - *myVM1* - para o nome da máquina virtual.        
    - *azureuser* – para o nome de usuário do administrador.    
    - *myResourceGroupLBAZ* - para **Grupo de recursos**, selecione **Usar existente** e, em seguida, selecione *myResourceGroupLBAZ*.
2. Clique em **OK**.
3. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
4. Insira estes valores para as configurações da VM:
    - *zona 1* - para a zona onde você coloca a VM.
    -  *myVNet* – verifique se esse valor está selecionado como a rede virtual.
    - *myBackendSubnet* – verifique se esse valor está selecionado como a sub-rede.
    - *myNetworkSecurityGroup* - para o nome do grupo de segurança de rede (firewall).
5. Clique em **Desabilitado** para desabilitar o diagnóstico de inicialização.
6. Clique em **OK**, examine as configurações na página de resumo e, em seguida, clique em **Criar**.
  
 ![Criar uma máquina virtual](./media/load-balancer-standard-public-availability-zones-portal/create-vm-standard-ip.png)

7. Crie uma segunda VM, nomeada *VM2* na Zona 2 e a terceira VM na Zona 3 e, com *myVnet* como a rede virtual, *myBackendSubnet* como a sub-rede e **myNetworkSecurityGroup* como o grupo de segurança de rede, utilizando as etapas de 1 a 6.

### <a name="install-iis-on-vms"></a>Instalar IIS em VMs

1. Clique em **Todos os recursos** no menu esquerdo e, em seguida, na lista de recursos, clique em **myVM1** localizado no grupo de recursos *myResourceGroupLBAZ*.
2. Na página **Visão geral**, clique em **Conectar** para o RDP na VM.
3. Faça logon na VM com nome de usuário *azureuser*.
4. Na área de trabalho do servidor, navegue até **Ferramentas Administrativas do Windows**>**Gerenciador do Servidor**.
5. Na página de início rápido do Gerenciador do Servidor, clique em **Adicionar funções e recursos**.

   ![Adicionando ao pool de endereços de back-end – ](./media/load-balancer-standard-public-availability-zones-portal/servermanager.png)    

1. No **Assistente Adicionar Funções e Recursos**, use os seguintes valores:
    - Na página **Selecionar tipo de instalação**, clique em **Instalação baseada em função ou em recurso**.
    - Na página **Selecionar servidor de destino**, clique em **myVM1**.
    - Na página **Selecionar função de servidor**, clique em **Servidor Web (IIS)**.
    - Siga as instruções para concluir o restante do assistente.
2. Feche a sessão RDP com a máquina virtual - *myVM1*.
3. Repita as etapas de 1 a 7 para instalar o IIS nas VMs *myVM2* e *myVM3*.

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta seção, você definirá as configurações do balanceador de carga para um pool de endereços de back-end e uma investigação de integridade, também especificará as regras do balanceador de carga e NAT.


### <a name="create-a-backend-address-pool"></a>Criar um pool de endereços de back-end

Para distribuir o tráfego para as máquinas virtuais, um pool de endereços de back-end contém os endereços IP das NICs virtuais conectadas ao balanceador de carga. Crie o pool de endereços de back-end *myBackendPool* para incluir *VM1* e *VM2*.

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** da lista de recursos.
2. Em **Configurações**, clique em **Pools de back-end** e clique em **Adicionar**.
3. Na página **Adicionar um pool de back-end**, faça o seguinte:
    - Em nome, digite *myBackEndPool como o nome de seu pool de back-end.
    - Para **Rede virtual**, no menu suspenso, clique em **myVNet**
    - Para **Máquina virtual**, no menu suspenso, clique em **myVM1**.
    - Para **Endereço IP**, no menu suspenso, clique no endereço IP de myVM1.
4. Clique em **Adicionar novo recurso de back-end** para adicionar cada máquina virtual (*myVM2* e *myVM3*) para adicionar no pool de back-end do balanceador de carga.
5. Clique em **Adicionar**.

    ![Adicionando ao pool de endereços de back-end – ](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Verifique se a configuração do pool de back-end do balanceador de carga exibe todas as três VMs - **myVM1**, **myVM2** e **myVM3**.

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Para permitir que o balanceador de carga monitore o status de seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. Crie uma investigação de integridade *myHealthProbe* para monitorar a integridade das VMs.

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, clique em **Investigação de integridade** e clique em **Adicionar**.
3. Use estes valores para criar a investigação de integridade:
    - *myHealthProbe* – para o nome da investigação de integridade.
    - **HTTP** – para o tipo de protocolo.
    - *80* – para o número da porta.
    - *15* – para o número de **Intervalo** em segundos entre tentativas de investigação.
    - *2* – para o número de **Limite não íntegro** ou falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra.
4. Clique em **OK**.

   ![Adicionando uma investigação](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. Definir a configuração de IP de front-end para o tráfego de entrada e o pool de IP de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Crie uma regra do balanceador de carga *myLoadBalancerRuleWeb* para escutar a porta 80 no front-end *FrontendLoadBalancer* e enviar o tráfego de rede com carga balanceada ao pool de endereços de back-end *myBackEndPool* também usando a porta 80. 

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, clique em **Regras de balanceamento de carga** e em **Adicionar**.
3. Use estes valores para configurar a regra do balanceamento de carga:
    - *myHTTPRule* – para o nome da regra de balanceamento de carga.
    - **TCP** – para o tipo de protocolo.
    - *80* – para o número da porta.
    - *80* – para a porta de back-end.
    - *myBackendPool* – para o nome do pool de back-end.
    - *myHealthProbe* – para o nome da investigação de integridade.
4. Clique em **OK**.
    
    ![Adicionando uma regra de balanceamento de carga](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga
1. Localize o endereço IP público para o Load Balancer na tela **Visão Geral**. Clique em **Todos os recursos**, depois clique em **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. A página padrão do servidor Web do IIS é exibida no navegador.

      ![Servidor Web do IIS](./media/load-balancer-standard-public-availability-zones-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Para isso, selecione o grupo de recursos que contém o balanceador de carga e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Load Balancer Standard](load-balancer-standard-overview.md).
