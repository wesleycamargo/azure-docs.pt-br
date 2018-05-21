---
title: 'Tutorial: VMs do balanceador de carga em uma zona – portal do Azure | Microsoft Docs'
description: Este tutorial demonstra como criar um Balanceador de Carga Standard com front-end zonal para balancear a carga de VMs em uma zona de disponibilidade usando o portal do Azure
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 9067ea350997ed0c4fc5c65dccb72f403adfa774
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="tutorialload-balance-vms-within-an-availability-zone-with-a-standard-load-balancer-using-the-azure-portal"></a>Tutorial: Balancear a carga de VMs em uma zona de disponibilidade com um Balanceador de Carga Standard usando o portal do Azure

Este tutorial descreve em etapas como criar um [Balanceador de Carga Standard](https://aka.ms/azureloadbalancerstandard) público com um front-end zonal usando um endereço IP Público Standard usando o portal do Azure. Nesse cenário, é possível especificar uma zona particular para suas instâncias de front-end e de back-end, a fim de alinhar o caminho de dados e os recursos com uma zona específica. Você aprenderá como:

> [!div class="checklist"]
> * Criar um balanceador de carga Standard do Azure com um front-end zonal
> * Criar grupos de segurança de rede para definir as regras do tráfego de entrada
> * Criar VMs zonais e anexá-las a um balanceador de carga
> * Criar uma investigação de integridade do balanceador de carga
> * Criar regras de tráfego para o balanceador de carga
> * Criar um site básico do IIS
> * Exibir um balanceador de carga em ação

Para obter mais informações sobre o uso de Zonas de Disponibilidade com o Load Balancer Standard, consulte [Zonas de disponibilidade e Load Balancer Standard](load-balancer-standard-availability-zones.md).

Se preferir, você pode concluir este tutorial usando a [CLI do Azure](load-balancer-standard-public-zonal-cli.md).

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).

## <a name="create-a-public-standard-load-balancer"></a>Criar um Load Balancer Standard público

Um balanceador de carga padrão só oferece suporte a um endereço IP público padrão. Ao criar um novo IP público durante a criação do balanceador de carga, ele é configurado automaticamente como uma versão de SKU Standard e, além disso, é automaticamente com redundância de zona.

1. No canto superior esquerdo da tela, clique em **Criar um recurso** > **Rede** > **Load Balancer**.
2. Na página **Criar um balanceador de carga**, insira estes valores para o balanceador de carga:
    - *myLoadBalancer* – para o nome do balanceador de carga.
    - **Public** – para o tipo do balanceador de carga.
     - *myPublicIPZonal* - para o novo endereço IP público que você cria. Para fazer isso, clique em **Escolher um endereço IP público** e, em seguida, clique em **Criar novo**. Para o tipo de nome *myPublicIP*, a SKU é Standard por padrão e seleciona a **Zona 1** para **Zona de disponibilidade**.
    - *myResourceGroupZLB* - para o nome do novo grupo de recursos que você cria.
    - **westeurope** – para o local.
3. Clique em **Criar** para criar o balanceador de carga.
   
    ![criar balanceador de carga padrão zonal com o Portal do Azure](./media/tutorial-load-balancer-standard-zonal-portal/create-load-balancer-zonal-frontend.png)


## <a name="create-backend-servers"></a>Criar servidores de back-end

Nesta seção, você cria uma rede virtual, cria duas máquinas virtuais na mesma zona (ou seja, zona 1) para a região adicionar ao pool de back-end do balanceador de carga e instala o IIS nas máquinas virtuais para ajudar a testar o balanceador de carga com redundância de zona. Portanto, se uma VM falhar, a investigação de integridade da VM na mesma zona falhará e o tráfego continuará a ser atendido por outra VM na mesma zona.

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
1. Na parte superior esquerda da tela, clique em **Criar um recurso** > **Rede** > **Rede virtual** e insira esses valores para a rede virtual:
    - *myVNet* – para o nome da rede virtual.
    - *myResourceGroupZLB* - para o nome do grupo de recursos existente
    - *myBackendSubnet* – para o nome da sub-rede.
2. Clique em **Criar** para criar a rede virtual.

    ![Criar uma rede virtual](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. No canto superior esquerdo da tela, clique em **Criar um recurso**, na caixa de pesquisa digite *Grupo de Segurança de Rede* e, na página do grupo de segurança de rede, clique em **Criar**.
2. Na página Criar grupo de segurança de rede, insira estes valores:
    - *myNetworkSecurityGroup*  - para o nome do grupo de segurança de rede.
    - *myResourceGroupLBAZ* - para o nome do grupo de recursos existente.
   
    ![Criar uma rede virtual](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>Criar regras NSG

Nesta seção, você cria regras do NSG para permitir conexões de entrada usando HTTP e RDP, utilizando o Portal do Azure.

1. No Portal do Azure, clique em **Todos os recursos** no menu esquerdo e, em seguida, pesquise e clique em **myNetworkSecurityGroup** localizado no grupo de recursos **myResourceGroupZLB**.
2. Em **Configurações**, clique em **Regras de segurança de entrada** e clique em **Adicionar**.
3. Insira esses valores para a regra de segurança de entrada denominada *myHTTPRule* para permitir conexões de entrada HTTP usando a porta 80:
    - *Service Tag* – para **Fonte**.
    - *Internet* – para **Marca de serviço de fonte**
    - *80* - para os **Intervalos de porta de destino**
    - *TCP* – para **Protocolo**
    - *Allow* – para **Ação**
    - *100* - para **Prioridade**
    - *myHTTPRule* para **Nome**
    - *Allow HTTP* - para **Descrição**
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

    ![Criar regra RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

1. No canto superior esquerdo da tela, clique em **Criar um recurso** > **Computação** > **Windows Server 2016 Datacenter** e insira esses valores para a máquina virtual:
    - *myVM1* - para o nome da máquina virtual.        
    - *azureuser* – para o nome de usuário do administrador.    
    - *myResourceGroupZLB* - para **Grupo de recursos**, selecione **Usar existente** e, em seguida, selecione *myResourceGroupZLB*.
2. Clique em **OK**.
3. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
4. Insira estes valores para as configurações da VM:
    - *zona 1* - para a zona de disponibilidade onde você coloca a VM.
    -  *myVNet* – verifique se esse valor está selecionado como a rede virtual.
    - *myVM1PIP* - para o endereço IP público padrão que você cria. Clique em *Criar novo* e, em seguida para o tipo de nome digite, *myVM1PIP*, para Zona, selecione **1**. A SKU do endereço IP é Standard por padrão.
    - *myBackendSubnet* – verifique se esse valor está selecionado como a sub-rede.
    - *myNetworkSecurityGroup* - para o nome do grupo de segurança de rede (firewall) que já existe.
5. Clique em **Desabilitado** para desabilitar o diagnóstico de inicialização.
6. Clique em **OK**, examine as configurações na página de resumo e, em seguida, clique em **Criar**.
7. Crie uma segunda VM, nomeada, *myVM2* na Zona 1 com *myVnet* como a rede virtual, *myVM2PIP* como o endereço IP Público Padrão, *myBackendSubnet* como sub-rede,  e **myNetworkSecurityGroup* como o grupo de segurança de rede usando as etapas de 1 a 6.

    ![Criar regra RDP](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>Instalar IIS em VMs

1. Clique em **Todos os recursos** no menu esquerdo e, em seguida, na lista de recursos, clique em **myVM1**, localizado no grupo de recursos *myResourceGroupZLB*.
2. Na página **Visão geral**, clique em **Conectar** para o RDP na VM.
3. Faça logon na VM com o nome de usuário e senha que você especificou ao criar a VM (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a VM) e selecione **OK**. Você pode receber um aviso do certificado durante o processo de logon. Selecione **Sim** para prosseguir com a conexão.
4. Na área de trabalho do servidor, navegue até **Ferramentas Administrativas do Windows**>**Windows PowerShell**.
6. Na janela do PowerShell, execute os comandos a seguir para instalar o servidor IIS, remova o arquivo default.htm, adicione um novo com o arquivo default.htm que exibe o nome da VM:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello from" + $env:computername)
   ```
8. Feche a sessão RDP com *myVM1*
9. Repita as etapas de 1 a 8 para instalar o IIS no *myVM2*.

## <a name="create-load-balancer-resources"></a>Criar recursos do balanceador de carga

Nesta seção, você definirá as configurações do balanceador de carga para um pool de endereços de back-end e uma investigação de integridade, também especificará as regras do balanceador de carga e NAT.


### <a name="create-a-backend-address-pool"></a>Criar um pool de endereços de back-end

Para distribuir o tráfego para as máquinas virtuais, um pool de endereços de back-end contém os endereços IP das NICs virtuais conectadas ao balanceador de carga. Crie o pool de endereços de back-end *myBackendPool* para incluir *VM1* e *VM2*.

1. Clique em **Todos os recursos** no menu esquerdo e depois clique em **myLoadBalancer* na lista de recursos.
2. Em **Configurações**, clique em **Pools de back-end** e clique em **Adicionar**.
3. Na página **Adicionar um pool de back-end**, faça o seguinte:
    - Para nome, digite *myBackEndPool*, como o nome do pool de back-end.
    - Para **Rede virtual**, no menu suspenso, clique em *myVNet*
    - Para **Máquina virtual** e **Endereço IP**  adicione *myVM1* e *myVM2*  e os endereços IP públicos correspondentes.
4. Clique em **Adicionar**.
5. Verifique se a configuração do pool de back-end do balanceador de carga exibe ambas as VMs - **myVM1** e **myVM2**.
 
    ![Criar pool de back-end](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

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
    
    ![Adicionando uma regra de balanceamento de carga](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testar o balanceador de carga
1. Localize o endereço IP público para o Load Balancer na tela **Visão Geral**. Clique em **Todos os recursos**, depois clique em **myPublicIP**. 

2. Copie o endereço IP público e cole-o na barra de endereços do seu navegador. A página padrão que inclui o nome da página do servidor Web é exibida no navegador.

      ![Servidor Web do IIS](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. Para ver o balanceador de carga em ação, você pode forçar a parada da VM exibida e atualizar o navegador para ver o outro nome do servidor exibido no navegador.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua o grupo de recursos, o balanceador de carga e todos os recursos relacionados. Para isso, selecione o grupo de recursos que contém o balanceador de carga e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Load Balancer Standard](load-balancer-standard-overview.md).
- [VMs com balanceamento de carga entre zonas de disponibilidade](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
