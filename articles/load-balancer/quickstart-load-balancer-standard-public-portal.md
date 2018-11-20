---
title: 'Início Rápido: Criar um balanceador de carga padrão – Portal do Azure | Microsoft Docs'
description: Este início rápido mostra como criar um balanceador de carga padrão usando o Portal do Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to create a Standard Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/21/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: df79581f4ad6af3dc420e7c7895acf46b5e3d997
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615191"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Início Rápido: Criar um balanceador de carga padrão para VMs usando o Portal do Azure

O balanceamento de carga fornece um nível mais alto de disponibilidade e escala distribuindo as solicitações de entrada entre várias máquinas virtuais. Você pode usar o Portal do Azure para criar um balanceador de carga para balancear a carga de máquinas virtuais (VMs). Este início rápido mostra como carregar a carga de VMs usando um balanceador de carga padrão.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-load-balancer"></a>Criar um balanceador de carga público

Nesta seção, você criará um balanceador de carga público que ajuda a balancear a carga de máquinas virtuais. Um balanceador de carga padrão só oferece suporte a um endereço IP público padrão. Ao criar um balanceador de carga padrão, você também deve criar um novo endereço IP público padrão configurado como o front-end (nomeado como *LoadBalancerFrontend* por padrão) para o balanceador de carga padrão. 

1. No canto superior esquerdo da tela, clique em **Criar um recurso** > **Rede** > **Load Balancer**.
2. Na página **Criar balanceador de carga**, insira ou selecione as informações a seguir, aceite os padrões para as configurações restantes e selecione **Criar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | NOME                   | *myLoadBalancer*                                   |
    | Tipo          | Público                                        |
    | SKU           | Standard                          |
    | Endereço IP público | Selecione **Criar novo** e digite *myPublicIP* na caixa de texto. O SKU Standard para o endereço IP público é selecionado por padrão. Para **Zona de disponibilidade**, selecione **Com redundância de zona**. |
    | Assinatura               | Selecione sua assinatura.    |
    |Grupo de recursos | Selecione **Criar novo** e digite *myResourceGroupSLB*.    |
    | Local padrão           | Selecione **Europa Ocidental**.                          |
    

![Criar um balanceador de carga](./media/load-balancer-standard-public-portal/create-load-balancer.png)


## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, crie uma rede virtual, crie duas máquinas virtuais para o pool de back-end do balanceador de carga e, em seguida, instalar o IIS nas máquinas virtuais para ajudar a testar o balanceador de carga.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
1. No canto superior esquerdo da tela, clique em **Novo** > **Rede** > **Rede virtual** e insira estes valores para a rede virtual:
    - *myVnet* – para o nome da rede virtual.
    - *myResourceGroupSLB* – para o nome do grupo de recursos existente
    - *myBackendSubnet* – para o nome da sub-rede.
2. Clique em **Criar** para criar a rede virtual.

    ![Criar uma rede virtual](./media/load-balancer-standard-public-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No canto superior esquerdo da tela, clique em **Novo** > **Computação** > **Windows Server 2016 Datacenter** e insira estes valores para a máquina virtual:
    - *myVM1* - para o nome da máquina virtual.        
    - *myResourceGroupSLB* – para **Grupo de recursos**, selecione **Usar existente** e depois *myResourceGroupSLB*.
2. Clique em **OK**.
3. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
4. Insira estes valores para as configurações da VM:
    1. Verifique se *myVNet* está selecionado para a rede virtual e se *myBackendSubnet* está selecionado como sub-rede.
    2. Para **Endereço IP público**, no painel **Criar endereço IP público**, selecione **Padrão** e, em seguida, selecione **OK**.
    3. Para **Grupo de Segurança de Rede**, selecione **Avançado**, e, em seguida, faça o seguinte:
        1. Selecione *Grupo de segurança de rede (firewall) e na página **Escolher grupo de segurança de rede**, selecione **Criar novo**. 
        2. Na página **Criar grupo de segurança de rede**, para **Nome**, insira *myNetworkSecurityGroup* e, em seguida, selecione **OK**.
5. Clique em **Desabilitado** para desabilitar o diagnóstico de inicialização.
6. Clique em **OK**, examine as configurações na página de resumo e, em seguida, clique em **Criar**.
7. Usando as etapas 1 a 6, crie uma segunda VM e a nomeie como *VM2* com *myAvailibilityset* como o Conjunto de disponibilidade, *myVnet* como a rede virtual, *myBackendSubnet* e sua sub-rede e **myNetworkSecurityGroup* como seu grupo de segurança de rede. 

### <a name="create-nsg-rule"></a>Como criar regra NSG

Nesta seção, você criará uma regra NSG para permitir conexões de entrada usando HTTP.

1. Clique em **Todos os recursos** no menu da esquerda e, depois, na lista de recursos, clique em **myNetworkSecurityGroup**, que está localizado no grupo de recursos **myResourceGroupSLB**.
2. Em **Configurações**, clique em **Regras de segurança de entrada**, depois clique em **Adicionar**.
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
 
### <a name="install-iis"></a>Instalar o IIS

1. Clique em **Todos os recursos** no menu da esquerda e, depois, na lista de recursos, clique em **myVM1**, que está localizado no grupo de recursos *myResourceGroupLB*.
2. Na página **Visão geral**, clique em **Conectar** para o RDP na VM.
3. Faça logon na VM com nome de usuário *azureuser*.
4. Na área de trabalho do servidor, navegue até **Ferramentas Administrativas do Windows**>**Gerenciador do Servidor**.
5. No Gerenciador do Servidor, clique em **Adicionar funções e recursos**.
6. No **Assistente Adicionar Funções e Recursos**, use os seguintes valores:
    - Na página **Selecionar tipo de instalação**, clique em **Instalação baseada em função ou em recurso**.
    - Na página **Selecionar servidor de destino**, clique em **myVM1**
    - Na página **Selecionar função de servidor**, clique em **Servidor Web (IIS)**
    - Siga as instruções para concluir o restante do assistente 
7. Repita as etapas 1 a 6 para a máquina virtual *myVM2*.

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta seção, você define as configurações do balanceador de carga para um pool de endereços de back-end e uma investigação de integridade, além de especificar uma regra do balanceador de carga.


### <a name="create-a-backend-address-pool"></a>Criar um pool de endereços de back-end

Para distribuir o tráfego entre as VMs, um pool de endereços de back-end contém os endereços IP dos NICs virtuais conectados ao balanceador de carga. Crie o pool de endereços de back-end *myBackendPool* para incluir *VM1* e *VM2*.

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** na lista de recursos.
2. Em **Configurações**, clique em **Pools de back-end** e clique em **Adicionar**.
3. Na página **Adicionar um pool de back-end**, faça o seguinte:
   - Para nome, digite *myBackendPool*, como o nome do pool de back-end.
   - Para **Rede virtual**, selecione *myVNet*.
   - Adicione *myVM1* e *myVM2* em **Máquina Virtual** junto com seus endereços IP correspondentes e, em seguida, selecione **Adicionar**.
    - Clique em **OK**.

3. Verifique se a configuração do seu pool de back-end do balanceador de carga exibe as máquinas virtuais **VM1** e **VM2**.

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Para permitir que o balanceador de carga monitore o status de seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. Crie uma investigação de integridade *myHealthProbe* para monitorar a integridade das VMs.

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** da lista de recursos.
2. Em **Configurações**, clique em **Investigação de integridade** e clique em **Adicionar**.
3. Use estes valores para criar a investigação de integridade:
    - *myHealthProbe* – para o nome da investigação de integridade.
    - **HTTP** – para o tipo de protocolo.
    - *80* – para o número da porta.
    - *Healthprobe.aspx* - para o caminho do URI. Você pode substituir esse valor com qualquer outro URI ou mantenha o valor do caminho padrão de **"\\"** para obter o URI padrão.
    - *15* – para o número de **Intervalo** em segundos entre tentativas de investigação.
    - *2* – para o número de **Limite não íntegro** ou falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra.
4. Clique em **OK**.

   ![Adicionando uma investigação](./media/load-balancer-standard-public-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga

Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. Defina a configuração de IP de front-end para o tráfego de entrada e o pool de IPs de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Crie uma regra do balanceador de carga *myLoadBalancerRuleWeb* para escutar a porta 80 no front-end *FrontendLoadBalancer* e enviar o tráfego de rede com carga balanceada ao pool de endereços de back-end *myBackEndPool* também usando a porta 80. 

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
    
    ![Adicionando uma regra de balanceamento de carga](./media/load-balancer-standard-public-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga
1. Localize o endereço IP público para o Load Balancer na tela **Visão Geral**. Clique em **Todos os recursos**, depois clique em **myPublicIP**.

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. A página padrão do servidor Web do IIS é exibida no navegador.

      ![Servidor Web do IIS](./media/load-balancer-standard-public-portal/9-load-balancer-test.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Para isso, selecione o grupo de recursos que contém o balanceador de carga e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Load Balancer Standard, anexou VMs a ele, configurou a regra de tráfego do balanceador de carga, a investigação de integridade e testou o balanceador de carga. Para saber mais sobre o Azure Load Balancer, continue nos tutoriais do Azure Load Balancer.

> [!div class="nextstepaction"]
> [Tutoriais do Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
