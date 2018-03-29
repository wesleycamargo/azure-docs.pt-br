---
title: Rotear tráfego - Portal do Azure | Microsoft Docs
description: Saiba como rotear tráfego com uma tabela de rotas utilizando o Portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 980cf7b59ed16778bbb6cd1b657e3522407c79c9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Rotear tráfego com uma tabela de rotas utilizando o Portal do Azure

Por padrão, o Azure roteia automaticamente o tráfego entre todas as sub-redes dentro de uma rede virtual. É possível criar as próprias rotas para substituir o roteamento padrão do Azure. A capacidade de criar rotas personalizadas será útil, por exemplo, se você quiser rotear o tráfego entre sub-redes por meio de uma NVA (solução de virtualização de rede). Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar uma tabela de rotas
> * Criar uma rota
> * Criar uma rede virtual com várias sub-redes
> * Associar uma tabela de rotas a uma sub-rede
> * Criar uma NVA que roteia o tráfego
> * Implantar VMs (máquinas virtuais) em diferentes sub-redes
> * Rotear o tráfego de uma sub-rede para outra por meio de uma NVA

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede**e, em seguida, selecione**Tabela de rotas**.
3. Insira, ou selecione, as informações a seguir, aceite o padrão para a configuração restante e, em seguida, selecione **Criar**:

    |Configuração|Valor|
    |---|---|
    |NOME|myRouteTablePublic|
    |Assinatura| Selecione sua assinatura.|
    |Grupo de recursos | Selecione **Criar novo** e insira *myResourceGroup*.|
    |Local padrão|Leste dos EUA|
 
    ![Criar tabela de rotas](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Criar uma rota

1. Na caixa *Pesquisar recursos, serviços e documentos* na parte superior do portal, comece digitando *myRouteTablePublic*. Quando **myRouteTablePublic** aparecer nos resultados da pesquisa, selecione-o.
2. Em **CONFIGURAÇÕES**, selecione **Rotas** e, em seguida, selecione **+ Add**, como mostrado na imagem a seguir:

    ![Adicionar rota](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. Em **Adicionar rota**, insira, ou selecione, as informações a seguir, aceite o padrão para as configurações restantes e, em seguida, selecione **Criar**:

    |Configuração|Valor|
    |---|---|
    |Nome da rota|ToPrivateSubnet|
    |Prefixo de endereço| 10.0.1.0/24|
    |Tipo do próximo salto | Selecione **Solução de virtualização**.|
    |Endereço do próximo salto| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Antes de poder associar uma tabela de rotas a uma sub-rede, será necessário criar uma rede virtual e uma sub-rede. Em seguida, você poderá associar a tabela de rotas a uma sub-rede:

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.
3. Em **Criar rede virtual**, insira, ou selecione, as informações a seguir, aceite o padrão para as configurações restantes e, em seguida, selecione **Criar**:

    |Configuração|Valor|
    |---|---|
    |NOME|myVirtualNetwork|
    |Espaço de endereço| 10.0.0.0/16|
    |Assinatura | Selecione sua assinatura.|
    |Grupo de recursos|Clique em **Usar existente** e selecione **myResourceGroup**.|
    |Local padrão|Selecione *Leste dos EUA*|
    |Nome da sub-rede|Público|
    |Intervalo de endereços|10.0.0.0/24|
    
4. Na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal, comece digitando *myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o.
5. Em **CONFIGURAÇÕES**, selecione **Sub-redes** e, em seguida, selecione **+ Sub-rede**, conforme mostrado na imagem a seguir:

    ![Adicionar sub-rede](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Selecione ou insira as informações a seguir e selecione **OK**:

    |Configuração|Valor|
    |---|---|
    |NOME|Privado|
    |Espaço de endereço| 10.0.1.0/24|

7. Complete as etapas 5 e 6 novamente, fornecendo as informações a seguir:

    |Configuração|Valor|
    |---|---|
    |NOME|Rede de Perímetro|
    |Espaço de endereço| 10.0.2.0/24|

8. A caixa **myVirtualNetwork - Subnets** é exibida após completar a etapa anterior. Em **CONFIGURAÇÕES**, selecione **Sub-redes** e, em seguida, selecione **Público**.
9. Conforme mostrado na imagem a seguir, selecione **Tabela de rotas**, selecione **MyRouteTablePublic** e, em seguida, selecione **Salvar**:

    ![Associar tabela de rotas](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>Criar uma NVA

Uma NVA é uma VM que executa uma função de rede, como roteamento, firewall ou otimização de WAN.

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. É possível selecionar um sistema operacional diferente, mas as etapas restantes assumirão que está selecionado o **Windows Server 2016 Datacenter**. 
3. Selecione ou insira as informações a seguir para **Básico** e, em seguida, selecione **OK**:

    |Configuração|Valor|
    |---|---|
    |NOME|myVmNva|
    |Nome de usuário|Insira um nome de usuário de sua escolha.|
    |Senha|Insira uma senha de sua escolha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupo de recursos| Clique em **Usar existente** e selecione *myResourceGroup*.|
    |Local padrão|Selecione **Leste dos EUA**.|
4. Selecione um tamanho da VM em **Escolher um tamanho**.
5. Selecione ou insira as informações a seguir para **Configurações** e, em seguida, selecione **OK**:

    |Configuração|Valor|
    |---|---|
    |Rede virtual|myVirtualNetwork - Se não estiver selecionada, selecione **Rede Virtual** e, em seguida, selecione **myVirtualNetwork** em **Escolher rede virtual**.|
    |Sub-rede|Selecione **Sub-rede** e, em seguida, selecione **DMZ** em **Escolher sub-rede**. |
    |Endereço IP público| Selecione **Endereço IP públicos** e selecione **Nenhum** em **Escolher endereço IP público**. Nenhum endereço IP público é atribuído a esta VM, uma vez que não será conectado da Internet.
6. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implantação da VM.

    A VM demora alguns minutos para criar. Não prossiga para a próxima etapa até que o Azure termine de criar a VM e uma caixa com informações sobre a VM seja aberta.

7. Na caixa que abriu para a VM após ser criada, em **CONFIGURAÇÕES**, selecione **Rede** e, em seguida, selecione **myvmnva158** (a interface de rede do Azure criada para a VM tem um número diferente após **myvmnva**), conforme mostrado na imagem a seguir:

    ![Rede de VM](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. Para uma interface de rede poder encaminhar o tráfego de rede enviado a ela, que não seja destinado a seu próprio endereço IP, o encaminhamento de IP deve ser habilitado para a interface de rede. Em **CONFIGURAÇÕES**, selecione **Configurações de IP**, selecione **Habilitado** para **Encaminhamento de IP** e, em seguida, selecione **Salvar**, conforme mostrado na imagem a seguir:

    ![Habilitar o encaminhamento de IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual para que seja possível validar esse tráfego da sub-rede *Pública* roteada para a sub-rede *Privada* por meio da NVA em uma etapa posterior. Conclua as etapas 1 a 6 de [Criar uma NVA](#create-a-network-virtual-appliance). Utilize as mesmas configurações nas etapas 3 e 5, exceto para as alterações a seguir:

|Nome da máquina virtual      |Sub-rede      | Endereço IP público     |
|--------- | -----------|---------              |
| myVmPublic  | Público     | Aceitar o padrão do portal |
| myVmPrivate | Privado    | Aceitar o padrão do portal |

Você pode criar a VM *myVmPrivate* enquanto o Azure cria a VM *myVmPublic*. Não continue com as etapas seguintes até que o Azure termine de criar ambas as VMs.

## <a name="route-traffic-through-an-nva"></a>Rotear o tráfego por meio de uma NVA

1. Na caixa *Pesquisar* na parte superior do portal, comece digitando *myVmPrivate*. Quando **myVmPrivate** aparecer nos resultados da pesquisa, selecione-o.
2. Crie uma conexão de área de trabalho remota para a VM *myVmPrivate*, selecionando **Conectar**, conforme mostrado na imagem a seguir:

    ![Conectar-se a uma VM ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Para conectar-se à VM, abra o arquivo RDP baixado. Se solicitado, selecione **Conectar**.
4. Insira o nome de usuário e senha especificados ao criar a VM (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a VM) e selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Selecione **Sim** para prosseguir com a conexão.
6. Em uma etapa posterior, o comando tracert.exe será utilizado para testar o roteamento. O Tracert usa o protocolo ICMP, que é negado por meio do Firewall do Windows. Habilite o ICMP através do firewall do Windows, inserindo o comando do PowerShell a seguir:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Embora o Tracert seja utilizado para testar o roteamento neste artigo, não é recomendável o ICMP através do Firewall do Windows para implantações de produção.
7. Você habilitou o encaminhamento de IP no Azure para a interface de rede da VM em [Habilitar encaminhamento de IP](#enable-ip-forwarding). Dentro da VM, o sistema operacional, ou um aplicativo em execução na VM, também pode ser capaz de encaminhar o tráfego. Habilite o encaminhamento de IP no sistema operacional da VM *myVmNva*, completando as etapas seguintes da VM *myVmPrivate*:

    Área de trabalho remota para a *myVmNva* com o seguinte comando a partir de um prompt de comando:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Para habilitar o encaminhamento de IP dentro do sistema operacional, insira o comando a seguir no PowerShell:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Reinicie a VM, que também desconecta a sessão da área de trabalho remota.
8. Ainda conectado à VM *myVmPrivate*, crie uma sessão da área de trabalho remota para a VM *myVmPublic* com o comando a seguir, após a VM *myVmNva* reiniciar:

    ``` 
    mstsc /v:myVmPublic
    ```
    
    Habilite o ICMP através do firewall do Windows, inserindo o comando do PowerShell a seguir:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. Para testar o roteamento do tráfego para a VM *myVmPrivate* da VM *myVmPublic*, insira o comando do PowerShell a seguir:

    ```
    tracert myVmPrivate
    ```

    A resposta é semelhante ao seguinte exemplo:
    
    ```
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Você pode ver que o primeiro salto é 10.0.2.4, que é o endereço IP privado da NVA. O segundo salto é 10.0.1.4, o endereço IP privado da VM *myVmPrivate*. A rota adicionada à tabela de rotas *myRouteTablePublic* e associada à sub-rede *Pública* fez o Azure encaminhar o tráfego através de NVA, em vez de diretamente para a sub-rede *Privada*.
10.  Feche a sessão da área de trabalho remota para a VM *myVmPublic*, que ainda mantém você ainda conectado à VM *myVmPrivate*.
11. Para testar o roteamento do tráfego para a VM *myVmPublic* da VM *myVmPrivate*, insira o comando a seguir em um prompt de comando:

    ```
    tracert myVmPublic
    ```

    A resposta é semelhante ao seguinte exemplo:

    ```
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Você pode ver que o tráfego é roteado diretamente da VM *myVmPrivate* para a VM *myVmPublic*. Por padrão, o Azure roteia o tráfego diretamente entre sub-redes.
12. Feche a sessão da área de trabalho remota para a VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém: 

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou uma tabela de rotas e associou-a a uma sub-rede. Você criou uma NVA simples que roteou o tráfego de uma sub-rede pública para uma sub-rede privada. Implante uma variedade de NVAs pré-configuradas que executem funções de rede, como firewall e otimização de WAN pelo [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Antes de implantar as tabelas de rotas para uso em produção, é recomendável que você cuidadosamente se familiarizar com o [Roteamento no Azure](virtual-networks-udr-overview.md), [Gerenciar tabelas de rotas](manage-route-table.md) e [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).


Embora seja possível implantar muitos recursos do Azure dentro de uma rede virtual, os recursos para alguns serviços PaaS do Azure não podem ser implantados em uma rede virtual. Ainda assim, é possível restringir acesso aos recursos de alguns serviços PaaS do Azure somente para tráfego de uma sub-rede de rede virtual. Avance para o próximo tutorial para aprender como restringir o acesso à rede para recursos PaaS do Azure.

> [!div class="nextstepaction"]
> [Restringir o acesso à rede para recursos PaaS](tutorial-restrict-network-access-to-resources.md)
