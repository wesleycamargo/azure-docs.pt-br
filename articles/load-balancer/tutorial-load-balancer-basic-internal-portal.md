---
title: Tutorial - Criar um Balanceador de Carga Básico público – portal do Azure | Microsoft Docs
description: Este tutorial mostra como criar um Balanceador de Carga Básico interno usando o portal do Azure.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/28/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: a5b6ae833fcd340a639c068156940e6b9ad469ca
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711983"
---
# <a name="tutorial-load-balance-internal-traffic-with-basic-load-balancer-to-vms-using-the-azure-portal"></a>Tutorial: Balancear a carga do tráfego interno com o Balanceador de Carga Básico para VMs usando o portal do Azure

O balanceamento de carga fornece um nível mais alto de disponibilidade e escala distribuindo as solicitações de entrada entre várias máquinas virtuais. Use o portal do Azure para balancear a carga do tráfego interno para máquinas virtuais com um Balanceador de Carga Básico. Este tutorial mostra como criar recursos de rede, servidores back-end e um Balanceador de Carga Básico interno.

Se preferir, você pode concluir este tutorial usando a [CLI do Azure](load-balancer-get-started-ilb-arm-cli.md) ou o [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
1. No canto superior esquerdo da tela, clique em **Novo** > **Rede** > **Rede virtual** e insira estes valores para a rede virtual:
    - *myVNet* – para o nome da rede virtual.
    - *myResourceGroupILB* – para o nome do grupo de recursos existente
    - *myBackendSubnet* – para o nome da sub-rede.
2. Clique em **Criar** para criar a rede virtual.

![Criar um balanceador de carga](./media/tutorial-load-balancer-basic-internal-portal/1-load-balancer.png)

## <a name="create-a-basic-load-balancer"></a>Criar o Load Balancer Básico
Crie um Balanceador de Carga Básico interno usando o portal.

1. No canto superior esquerdo da tela, clique em **Criar um recurso** > **Rede** > **Load Balancer**.
2. Na página **Criar um balanceador de carga**, insira estes valores para o balanceador de carga:
    - *myLoadBalancer* – para o nome do balanceador de carga.
    - **Interno** – para o tipo do balanceador de carga.
    - **Básico** – para a versão de SKU.
    - **10.1.0.7** – para o endereço IP privado estático.
    - *myVNet* – para a rede virtual que você escolher na lista de redes existentes.
    - *mySubnet* – para a sub-rede que você escolher na lista de sub-redes existentes.
    - *myResourceGroupILB* – para o nome do novo grupo de recursos criados.
3. Clique em **Criar** para criar o balanceador de carga.
   
    ## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você cria duas máquinas virtuais para o pool de back-end do Balanceador de Carga Básico e, em seguida, instala o IIS nas máquinas virtuais para ajudar a testar o balanceador de carga.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No canto superior esquerdo da tela, clique em **Criar um recurso** > **Computação** > **Windows Server 2016 Datacenter** e insira esses valores para a máquina virtual:
    - *myVM1* - para o nome da máquina virtual.        
    - *azureuser* – para o nome de usuário do administrador.   
    - *myResourceGroupILB* – para **Grupo de recursos**, selecione **Usar existente** e, em seguida, *myResourceGroupILB*.
2. Clique em **OK**.
3. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
4. Insira estes valores para as configurações da VM:
    - *myAvailabilitySet* – para o nome do novo Conjunto de disponibilidade que você criar.
    -  *myVNet* – verifique se esse valor está selecionado como a rede virtual.
    - *myBackendSubnet* – verifique se esse valor está selecionado como a sub-rede.
5. Em **Grupo de segurança de rede**, selecione **Avançado**. Em seguida, para **Grupo de segurança de rede (firewall)**, selecione **Nenhum**.
5. Clique em **Desabilitado** para desabilitar o diagnóstico de inicialização.
6. Clique em **OK**, examine as configurações na página de resumo e, em seguida, clique em **Criar**.
7. Usando as etapas 1 a 6, crie uma segunda VM chamada *VM2* com *myAvailabilityset* como o Conjunto de disponibilidade, *myVnet* como a rede virtual, *myBackendSubnet* como a sub-rede e selecione **Nenhum** para o **Grupo de segurança de rede (firewall)**. 

### <a name="install-iis-and-customize-the-default-web-page"></a>Instalar o IIS e personalizar a página da Web padrão

1. Clique em **Todos os recursos** no menu esquerdo e, em seguida, na lista de recursos, clique em **myVM1**, localizada no grupo de recursos *myResourceGroupILB*.
2. Na página **Visão geral**, clique em **Conectar** para o RDP na VM.
3. Faça logon na VM.
4. Na área de trabalho do servidor, navegue até **Ferramentas Administrativas do Windows**>**Gerenciador do Servidor**.
5. Inicie o Windows PowerShell na VM1 e usando os comandos a seguir para instalar o servidor do IIS e atualizar o arquivo htm padrão.
    ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
5. Feche a conexão RDP com *myVM1*.
6. Repita as etapas de 1 a 5 com *myVM2* para instalar o IIS e personalizar a página da Web padrão.

## <a name="create-basic-load-balancer-resources"></a>Criar recursos do Load Balancer Básico

Nesta seção, você define as configurações do balanceador de carga para um pool de endereços de back-end e uma investigação de integridade, além de especificar as regras do balanceador de carga.


### <a name="create-a-backend-address-pool"></a>Criar um pool de endereços de back-end

Para distribuir o tráfego para as máquinas virtuais, um pool de endereços de back-end contém os endereços IP das NICs virtuais conectadas ao balanceador de carga. Crie o pool de endereços de back-end *myBackendPool* para incluir *VM1* e *VM2*.

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** da lista de recursos.
2. Em **Configurações**, clique em **Pools de back-end** e clique em **Adicionar**.
3. Na página **Adicionar um pool de back-end**, faça o seguinte:
    - Para nome, digite *myBackEndPool*, como o nome do pool de back-end.
    - Em **Associado ao**, no menu suspenso, clique em **Conjunto de disponibilidade**
    - Em **Conjunto de disponibilidade**, clique em **myAvailabilitySet**.
    - Clique em **Adicionar uma configuração de IP de rede de destino** para adicionar cada máquina virtual (*myVM1* & *myVM2*) que você criou ao pool de back-end.
    - Clique em **OK**.

        ![Adicionando ao pool de endereços de back-end – ](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)

3. Verifique se a configuração do seu pool de back-end do balanceador de carga exibe as máquinas virtuais **VM1** e **VM2**.

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade

Para permitir que o Load Balancer Básico monitore o status de seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. Crie uma investigação de integridade *myHealthProbe* para monitorar a integridade das VMs.

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** da lista de recursos.
2. Em **Configurações**, clique em **Investigação de integridade** e clique em **Adicionar**.
3. Use estes valores para criar a investigação de integridade:
    - *myHealthProbe* – para o nome da investigação de integridade.
    - **HTTP** – para o tipo de protocolo.
    - *80* – para o número da porta.
    - *15* – para o número de **Intervalo** em segundos entre tentativas de investigação.
    - *2* – para o número de **Limite não íntegro** ou falhas de investigação consecutivas que devem ocorrer antes que uma VM seja considerada não íntegra.
4. Clique em **OK**.

   ![Adicionando uma investigação](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Criar uma regra do Load Balancer

Uma regra do Load Balancer é usada para definir como o tráfego é distribuído para as VMs. Definir a configuração de IP de front-end para o tráfego de entrada e o pool de IP de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Crie uma regra do Load Balancer *myLoadBalancerRuleWeb* para escutar a porta 80 no front-end *LoadBalancerFrontEnd* e enviar o tráfego de rede com carga balanceada ao pool de endereços de back-end *myBackEndPool* também usando a porta 80. 

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** da lista de recursos.
2. Em **Configurações**, clique em **Regras de balanceamento de carga** e em **Adicionar**.
3. Use estes valores para configurar a regra do balanceamento de carga:
    - *myHTTPRule* – para o nome da regra de balanceamento de carga.
    - **TCP** – para o tipo de protocolo.
    - *80* – para o número da porta.
    - *80* – para a porta de back-end.
    - *myBackendPool* – para o nome do pool de back-end.
    - *myHealthProbe* – para o nome da investigação de integridade.
4. Clique em **OK**.
    
    ![Adicionando uma regra de balanceamento de carga](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="create-a-virtual-machine-to-test-the-load-balancer"></a>Criar uma máquina virtual para testar o balanceador de carga
Para testar o balanceador de carga interno, é necessário criar uma máquina virtual localizada na mesma rede virtual das VMs de servidor back-end.
1. No canto superior esquerdo da tela, clique em **Criar um recurso** > **Computação** > **Windows Server 2016 Datacenter** e insira esses valores para a máquina virtual:
    - *myVMTest* – para o nome da máquina virtual.        
    - *myResourceGroupILB* – para **Grupo de recursos**, selecione **Usar existente** e, em seguida, *myResourceGroupILB*.
2. Clique em **OK**.
3. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
4. Insira estes valores para as configurações da VM:
    -  *myVNet* – verifique se esse valor está selecionado como a rede virtual.
    - *myBackendSubnet* – verifique se esse valor está selecionado como a sub-rede.
5. Clique em **Desabilitado** para desabilitar o diagnóstico de inicialização.
6. Clique em **OK**, examine as configurações na página de resumo e, em seguida, clique em **Criar**.

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga
1. No portal do Azure, obtenha o endereço IP Privado do Balanceador de Carga na tela **Visão Geral**. Para fazer isso: a. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer** da lista de recursos.
    b. Na página de detalhes **Visão Geral**, copie o endereço IP Privado (neste exemplo, 10.1.0.7).

2. Crie uma conexão remota com *myVMTest* da seguinte maneira: a. Clique em **Todos os recursos** no menu esquerdo e, em seguida, na lista de recursos, clique em **myVMTest**, localizada no grupo de recursos *myResourceGroupILB*.
2. Na página **Visão Geral**, clique em **Conectar** para iniciar uma sessão remota com a VM.
3. Faça logon na *myVMTest*.
3. Cole o endereço IP Privado na barra de endereços do navegador em *myVMTest*. A página padrão do servidor Web do IIS é exibida no navegador.

      ![Servidor Web do IIS](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png)

Para ver o balanceador de carga distribuir o tráfego entre ambas as VMs que executam o aplicativo, você poderá forçar a atualização do navegador da Web.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Para isso, selecione o grupo de recursos que contém o balanceador de carga e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um grupo de recursos, recursos de rede e servidores back-end. Em seguida, você usou esses recursos para criar um balanceador de carga interno para balancear a carga do tráfego interno para VMs. Em seguida, saiba como [balancear a carga de VMs entre zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
