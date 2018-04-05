---
title: Criar um Azure Load Balancer público - Portal do Azure | Microsoft Docs
description: Saiba como criar um Azure Load Balancer Básico usando o Portal do Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 1b7901542a699e74f65527bf734133f73acb0bea
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-public-basic-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Criar um Load Balancer Básico público para balancear cargas de VMs usando o Portal do Azure

O balanceamento de carga fornece um nível mais alto de disponibilidade e escala distribuindo as solicitações de entrada entre várias máquinas virtuais. Você pode usar o Portal do Azure para criar um balanceador de carga a fim de balancear a carga de máquinas virtuais. Este guia de início rápido mostra como criar recursos de rede, servidores de back-end e um Load Balancer Básico.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-the-azure-portal"></a>Entrar no Portal do Azure

Entre no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Criar o Load Balancer Básico

Nesta seção, você cria um Load Balancer Básico público usando o portal. O endereço IP público é configurado automaticamente como front-end do Load Balancer, denominado *LoadBalancerFrontend* quando você cria o IP público na criação do recurso de balanceador de carga usando o portal.

1. No canto superior esquerdo da tela, clique em **Criar um recurso** > **Rede** > **Load Balancer**.
2. Na página **Criar um balanceador de carga**, insira estes valores para o balanceador de carga:
    - *myLoadBalancer* – para o nome do balanceador de carga.
    - **Public** – para o tipo da frente do balanceador de carga. 
     - *myPublicIP* – para o IP público que você deve criar com o SKU como **Básico** e **Atribuição** definida como **Dinâmico**.
    - *myResourceGroupLB* - para o nome do novo grupo de recursos que você criar.
3. Clique em **Criar** para criar o balanceador de carga.
   
    ![Criar um balanceador de carga](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você vai criar uma rede virtual, duas máquinas virtuais para o pool de back-end do Load Balancer Básico e instalar o IIS nas máquinas virtuais para ajudar a testar o balanceador de carga.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
1. No canto superior esquerdo da tela, clique em **Novo** > **Rede** > **Rede virtual** e insira estes valores para a rede virtual:
    - *myVNet* – para o nome da rede virtual.
    - *myResourceGroupLB* – para o nome do grupo de recursos existente
    - *myBackendSubnet* – para o nome da sub-rede.
2. Clique em **Criar** para criar a rede virtual.

    ![Criar uma rede virtual](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No canto superior esquerdo da tela, clique em **Novo** > **Computação** > **Windows Server 2016 Datacenter** e insira estes valores para a máquina virtual:
    - *myVM1* - para o nome da máquina virtual.        
    - *azureuser* – para o nome de usuário do administrador. -    
    - *myResourceGroupLB* – para **Grupo de recursos**, selecione **Usar existente** e *myResourceGroupLB*.
2. Clique em **OK**.
3. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
4. Insira estes valores para as configurações da VM:
    - *myAvailabilitySet* – para o nome do novo Conjunto de disponibilidade que você criar.
    -  *myVNet* – verifique se esse valor está selecionado como a rede virtual.
    - *myBackendSubnet* – verifique se esse valor está selecionado como a sub-rede.
    - *myVM1-ip* – para o endereço IP público.
    - *myNetworkSecurityGroup* – para o nome do novo grupo de segurança (firewall) de rede que você deve criar.
5. Clique em **Desabilitado** para desabilitar o diagnóstico de inicialização.
6. Clique em **OK**, examine as configurações na página de resumo e, em seguida, clique em **Criar**.
7. Usando as etapas 1 a 6, crie uma segunda VM chamada de *VM2* com *myAvailabilityset* como o Conjunto de disponibilidade, *myVnet* como a rede virtual, *myBackendSubnet* como a sub-rede e *myNetworkSecurityGroup* como seu grupo de segurança de rede. 

### <a name="create-nsg-rules"></a>Criar regras NSG

Nesta seção, você criará regras NSG para permitir conexões de entrada usando HTTP e RDP.

1. Clique em **Todos os recursos** no menu da esquerda e, depois, na lista de recursos, clique em **myNetworkSecurityGroup**, que está localizado no grupo de recursos **myResourceGroupLB**.
2. Em **Configurações**, clique em **Regras de segurança de entrada** e clique em **Adicionar**.
3. Insira esses valores para a regra de segurança de entrada denominada *myHTTPRule* para permitir conexões de entrada HTTP usando a porta 80:
    - *Marca de serviço* - para **Fonte**.
    - *Internet* - para **Marca de serviço de fonte**
    - *80* - para os **Intervalos de porta de destino**
    - *TCP* - para **Protocolo**
    - *Permitir* - para **Ação**
    - *100* - para **Prioridade**
    - *myHTTPRule* - para nome
    - *Allow HTTP* - para descrição
4. Clique em **OK**.
 
 ![Criar uma rede virtual](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
5. Repita as etapas 2 a 4 para criar outra regra denominada *myRDPRule* para permitir uma conexão de RDP de entrada usando a porta 3389 com os seguintes valores:
    - *Marca de serviço* - para **Fonte**.
    - *Internet* - para **Marca de serviço de fonte**
    - *3389* - para os **Intervalos de porta de destino**
    - *TCP* - para **Protocolo**
    - *Permitir* - para **Ação**
    - *200* - para **Prioridade**
    - *myRDPRule* - para nome
    - *Allow RDP* - para descrição

   

### <a name="install-iis"></a>Instalar o IIS

1. Clique em **Todos os recursos** no menu da esquerda e, depois, na lista de recursos, clique em **myVM1**, que está localizado no grupo de recursos *myResourceGroupLB*.
2. Na página **Visão geral**, clique em **Conectar** para o RDP na VM.
3. Faça logon na VM com o nome de usuário *azureuser* e a senha *Azure123456!*
4. Na área de trabalho do servidor, navegue até **Ferramentas Administrativas do Windows**>**Gerenciador do Servidor**.
5. Em Gerenciador do Servidor, clique em Gerenciar e clique em **Adicionar Funções e recursos**.
 ![Adicionar função de gerenciador do servidor](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. No **Assistente Adicionar Funções e Recursos**, use os seguintes valores:
    - Na página **Selecionar tipo de instalação**, clique em **Instalação baseada em função ou em recurso**.
    - Na página **Selecionar servidor de destino**, clique em **myVM1**
    - Na página **Selecionar função de servidor**, clique em **Servidor Web (IIS)**
    - Siga as instruções para concluir o restante do assistente 
7. Repita as etapas 1 a 6 para a máquina virtual *myVM2*.

## <a name="create-basic-load-balancer-resources"></a>Criar recursos do Load Balancer Básico

Nesta seção, você definirá as configurações do balanceador de carga para um pool de endereços de back-end e uma investigação de integridade, também especificará as regras do balanceador de carga e NAT.


### <a name="create-a-backend-address-pool"></a>Criar um pool de endereços de back-end

Para distribuir o tráfego para as máquinas virtuais, um pool de endereços de back-end contém os endereços IP das NICs virtuais conectadas ao balanceador de carga. Crie o pool de endereços de back-end *myBackendPool* para incluir *VM1* e *VM2*.

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** da lista de recursos.
2. Em **Configurações**, clique em **Pools de back-end** e clique em **Adicionar**.
3. Na página **Adicionar um pool de back-end**, faça o seguinte:
    - Em nome, digite *myBackEndPool como o nome de seu pool de back-end.
    - Em **Associado ao**, no menu suspenso, clique em **Conjunto de disponibilidade**
    - Em **Conjunto de disponibilidade**, clique em **myAvailabilitySet**.
    - Clique em **Adicionar uma configuração de IP de rede de destino** para adicionar cada máquina virtual (*myVM1* & *myVM2*) que você criou ao pool de back-end.
    - Clique em **OK**.

    ![Adicionando ao pool de endereços de back-end – ](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Verifique se a configuração do seu pool de back-end do balanceador de carga exibe as máquinas virtuais **VM1** e **VM2**.

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Para permitir que o Load Balancer Básico monitore o status de seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. Crie uma investigação de integridade *myHealthProbe* para monitorar a integridade das VMs.

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** da lista de recursos.
2. Em **Configurações**, clique em **Investigação de integridade** e clique em **Adicionar**.
3. Use estes valores para criar a investigação de integridade:
    - *myHealthProbe* - para o nome da investigação de integridade.
    - **HTTP** – para o tipo de protocolo.
    - *80* - para o número da porta.
    - *15* – para o número de **Intervalo** em segundos entre tentativas de investigação.
    - *2* – para o número de **Limite não íntegro** ou falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra.
4. Clique em **OK**.

   ![Adicionando uma investigação](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra do Load Balancer

Uma regra do Load Balancer é usada para definir como o tráfego é distribuído para as VMs. Definir a configuração de IP de front-end para o tráfego de entrada e o pool de IP de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Crie uma regra do Load Balancer *myLoadBalancerRuleWeb* para escutar a porta 80 no front-end *LoadBalancerFrontEnd* e enviar o tráfego de rede com carga balanceada ao pool de endereços de back-end *myBackEndPool* também usando a porta 80. 

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** da lista de recursos.
2. Em **Configurações**, clique em **Regras de balanceamento de carga** e em **Adicionar**.
3. Use estes valores para configurar a regra do balanceamento de carga:
    - *myHTTPRule* – para o nome da regra de balanceamento de carga.
    - **TCP** – para o tipo de protocolo.
    - *80* - para o número da porta.
    - *80* – para a porta de back-end.
    - *myBackendPool* – para o nome do pool de back-end.
    - *myHealthProbe* - para o nome da investigação de integridade.
4. Clique em **OK**.
    
    ![Adicionando uma regra de balanceamento de carga](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga
1. Localize o endereço IP público para o Load Balancer na tela **Visão Geral**. Clique em **Todos os recursos**, depois clique em **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. A página padrão do servidor Web do IIS é exibida no navegador.

  ![Servidor Web do IIS](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Para isso, selecione o grupo de recursos que contém o balanceador de carga e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um grupo de recursos, recursos de rede e servidores de back-end. Em seguida, você usou esses recursos para criar um balanceador de carga. Para saber mais sobre os balanceadores de carga e seus recursos associados, continue para os artigos de tutorial.
