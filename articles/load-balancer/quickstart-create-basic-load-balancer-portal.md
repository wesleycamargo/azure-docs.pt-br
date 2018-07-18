---
title: 'Início Rápido: Criar um balanceador de carga Básico público usando o portal do Azure | Microsoft Docs'
description: Este início rápido mostra como criar um balanceador de carga Básico público usando o portal do Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 49fa4cf9b24c432b0956f930a1429e1cdf827f1b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34304871"
---
# <a name="quickstart-create-a-public-basic-load-balancer-by-using-the-azure-portal"></a>Início Rápido: Criar um balanceador de carga Básico público usando o portal do Azure

O balanceamento de carga fornece um nível mais alto de disponibilidade e escala com a distribuição das solicitações recebidas entre várias VMs (máquinas virtuais). Use o portal do Azure para criar um balanceador de carga que balanceará a carga de máquinas virtuais. Este início rápido mostra como criar recursos de rede, servidores back-end e um balanceador de carga no tipo de preço Básico.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-the-azure-portal"></a>Entrar no Portal do Azure

Para todas as tarefas deste início rápido, entre no [portal do Azure](http://portal.azure.com).

## <a name="create-a-basic-load-balancer"></a>Criar o balanceador de carga Básico

Nesta seção, você cria um balanceador de carga Básico público usando o portal. O endereço IP público é configurado automaticamente como o front-end do balanceador de carga quando você cria o IP público e o recurso de balanceador de carga usando o portal. O nome do front-end é **LoadBalancerFrontend**.

1. No canto superior esquerdo do portal, selecione **Criar um recurso** > **Rede** > **Balanceador de Carga**.
2. No painel **Criar balanceador de carga**, insira estes valores:
   - **myLoadBalancer** para o nome do balanceador de carga
   - **Público** para o tipo do balanceador de carga 
   - **myPublicIP** para o IP público que você precisa criar, com **SKU** definido como **Básico** e **Atribuição** definido como **Dinâmica**
   - **myResourceGroupLB** para o nome do novo grupo de recursos
3. Clique em **Criar**.
   
![Criar um balanceador de carga](./media/load-balancer-get-started-internet-portal/1-load-balancer.png)


## <a name="create-back-end-servers"></a>Criar servidores back-end

Nesta seção, você cria uma rede virtual e duas máquinas virtuais para o pool de back-end do balanceador de carga Básico. Em seguida, você instala o IIS (Serviços de Informações da Internet) nas máquinas virtuais para ajudar a testar o balanceador de carga.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
1. No canto superior esquerdo do portal, selecione **Nova** > **Rede** > **Rede Virtual**.
2. No painel **Criar rede virtual**, insira estes valores e, em seguida, selecione **Criar**:
   - **myVnet** para o nome da rede virtual
   - **myResourceGroupLB** para o nome do grupo de recursos existente
   - **myBackendSubnet** para o nome da sub-rede

   ![Criar uma rede virtual](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No canto superior esquerdo do portal, selecione **Novo** > **Computação** > **Windows Server 2016 Datacenter**. 
2. Insira esses valores para a máquina virtual e, em seguida, selecione **OK**:
   - **myVM1** para o nome da máquina virtual.        
   - **azureuser** para o nome de usuário do administrador.    
   - **myResourceGroupLB** para o grupo de recursos. (Em **Grupo de recursos**, selecione **Usar existente** e, em seguida, **myResourceGroupLB**.)   
3. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
4. Insira estes valores para as configurações da VM:
   - **myAvailabilitySet** para o nome do novo conjunto de disponibilidade criado.
   - **myVNet** para o nome da rede virtual. (Verifique se ela está selecionada.)
   - **myBackendSubnet** para o nome da sub-rede. (Verifique se ela está selecionada.)
   - **myVM1-ip** para o endereço IP público.
   - **myNetworkSecurityGroup** para o nome do novo grupo de segurança de rede (NSG, um tipo de firewall) que você precisa criar.
5. Selecione **Desabilitado** para desabilitar o diagnóstico de inicialização.
6. Selecione **OK**, examine as configurações na página de resumo e, em seguida, selecione **Criar**.
7. Usando as etapas 1 a 6, crie uma segunda VM chamada **VM2**, com:
   - **myAvailabilityset** como a o conjunto de disponibilidade.
   - **myVnet** como a rede virtual.
   - **myBackendSubnet** como a sub-rede.
   - **myNetworkSecurityGroup** como o grupo de segurança de rede. 

### <a name="create-nsg-rules"></a>Criar regras NSG

Nesta seção, você cria regras do NSG para permitir conexões de entrada que usam HTTP e RDP.

1. Selecione **Todos os recursos** no menu à esquerda. Na lista de recursos, selecione **myNetworkSecurityGroup** no grupo de recursos **myResourceGroupLB**.
2. Em **Configurações**, selecione **Regras de segurança de entrada** e, em seguida, **Adicionar**.
3. Insira os valores a seguir para a regra de segurança de entrada chamada **myHTTPRule** para permitir conexões HTTP de entrada que usam a porta 80. Depois, selecione **OK**.
   - **Marca de Serviço** para **Origem**
   - **Internet** para **Marca de serviço de origem**
   - **80** para **Intervalos de porta de destino**
   - **TCP** para **Protocolo**
   - **Permitir** para **Ação**
   - **100** - para **Prioridade**
   - **myHTTPRule** para **Nome**
   - **Permitir HTTP** para **Descrição**
 
   ![Criar uma regra do NSG](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
4. Repita as etapas 2 e 3 para criar outra regra chamada **myRDPRule** para permitir uma conexão RDP de entrada pela porta 3389. Use os seguintes valores:
   - **Marca de Serviço** para **Origem**
   - **Internet** para **Marca de serviço de origem**
   - **3389** para **Intervalos de porta de destino**
   - **TCP** para **Protocolo**
   - **Permitir** para **Ação**
   - **200** - para **Prioridade**
   - **myRDPRule** para **Nome**
   - **Permitir RDP** para **Descrição**

   

### <a name="install-iis"></a>Instalar o IIS

1. Selecione **Todos os recursos** no menu à esquerda. Na lista de recursos, selecione **myVM1** no grupo de recursos **myResourceGroupLB**.
2. Na página **Visão Geral**, selecione **Conectar** para habilitar o RDP na VM.
3. Entre na VM com o nome de usuário **azureuser** e a senha **Azure123456!**.
4. Na área de trabalho do servidor, procure **Ferramentas Administrativas do Windows** > **Gerenciador do Servidor**.
5. No Gerenciador do Servidor, selecione **Gerenciar** e, em seguida, **Adicionar Funções e recursos**.
   ![Adicionar função de gerenciador do servidor](./media/load-balancer-get-started-internet-portal/servermanager.png)
6. No Assistente Adicionar Funções e Recursos, use os seguintes valores:
   - Na página **Selecionar tipo de instalação**, selecione **Instalação baseada em função ou em recurso**.
   - Na página **Selecionar servidor de destino**, selecione **myVM1**.
   - Na página **Selecionar função de servidor**, selecione **Servidor Web (IIS)**.
   - Siga as instruções para concluir o restante do assistente. 
7. Repita as etapas 1 a 6 para a máquina virtual **myVM2**.

## <a name="create-resources-for-the-basic-load-balancer"></a>Criar recursos para o balanceador de carga Básico

Nesta seção, você define as configurações de balanceador de carga para um pool de endereços de back-end e uma investigação de integridade. Especifique também o balanceador de carga e as regras NAT.


### <a name="create-a-back-end-address-pool"></a>Criar um pool de endereços de back-end

Para distribuir o tráfego para as VMs, um pool de endereços de back-end contém os endereços IP das NICs virtuais conectadas ao balanceador de carga. Crie o pool de endereços de back-end **myBackendPool** para incluir **VM1** e **VM2**.

1. Selecione **Todos os recursos** no menu à esquerda e, em seguida, **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, selecione **Pools de back-end** e, em seguida, **Adicionar**.
3. Na página **Adicionar um pool de back-end**, faça o seguinte e, em seguida, selecione **OK**:
   - Para **Nome**, insira **myBackEndPool**.
   - Em **Associado ao**, no menu suspenso, selecione **Conjunto de disponibilidade**.
   - Em **Conjunto de disponibilidade**, selecione **myAvailabilitySet**.
   - Selecione **Adicionar uma configuração de IP de rede de destino** para adicionar ao pool de back-end cada máquina virtual (**myVM1** e **myVM2**) criada.

   ![Adicionando ao pool de endereços de back-end](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)

3. Verifique se a configuração do pool de back-end do balanceador de carga exibe as VMs **VM1** e **VM2**.

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Para permitir que o balanceador de carga Básico monitore o status de seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. Crie uma investigação de integridade chamada **myHealthProbe** para monitorar a integridade das VMs.

1. Selecione **Todos os recursos** no menu à esquerda e, em seguida, **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, selecione **Investigações de integridade** e, em seguida, **Adicionar**.
3. Use esses valores e, em seguida, selecione **OK**:
   - **myHealthProbe** para o nome da investigação de integridade
   - **HTTP** para o tipo de protocolo
   - **80** para o número da porta
   - **15** para **Intervalo**, o número de segundos entre as tentativas de investigação
   - **2** para **Limite não íntegro**, o número de falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra

   ![Adicionando uma investigação](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Use uma regra do balanceador de carga para definir como o tráfego é distribuído para as VMs. Definir a configuração de IP de front-end para o tráfego de entrada e o pool de IP de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. 

Crie uma regra de balanceador de carga chamada **myLoadBalancerRuleWeb** para escuta na porta 80 no **LoadBalancerFrontEnd** de front-end. A regra também se aplica ao envio do tráfego de rede com balanceamento de carga para o pool de endereços de back-end **myBackEndPool**, também por meio da porta 80. 

1. Selecione **Todos os recursos** no menu à esquerda e, em seguida, **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, selecione **Regras de balanceamento de carga** e, em seguida, **Adicionar**.
3. Use esses valores e, em seguida, selecione **OK**:
   - **myHTTPRule** para o nome da regra do balanceador de carga
   - **TCP** para o tipo de protocolo
   - **80** para o número da porta
   - **80** para a porta de back-end
   - **myBackendPool** para o nome do pool de back-end
   - **myHealthProbe** para o nome da investigação de integridade
    
   ![Adicionando uma regra do balanceador de carga](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga
1. Encontre o endereço IP público para o balanceador de carga na tela **Visão Geral**. Selecione **Todos os recursos** e, em seguida, **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. A página padrão do servidor Web do IIS é exibida no navegador.

   ![Servidor Web do IIS](./media/load-balancer-get-started-internet-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Limpar recursos

Exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados quando não forem mais necessários. Selecione o grupo de recursos que contém o balanceador de carga e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um grupo de recursos, recursos de rede e servidores back-end. Em seguida, você usou esses recursos para criar um Load Balancer Básico. Para saber mais sobre o Azure Load Balancer, continue nos tutoriais do Azure Load Balancer.

> [!div class="nextstepaction"]
> [Tutoriais do Azure Load Balancer](tutorial-load-balancer-basic-internal-portal.md)
