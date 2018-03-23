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
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 4cc814e1fd3ee8979662695f9dec3dcce335dc2a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Rotear tráfego com uma tabela de rotas utilizando o Portal do Azure

Por padrão, o Azure roteia automaticamente o tráfego entre todas as sub-redes dentro de uma rede virtual. É possível criar as próprias rotas para substituir o roteamento padrão do Azure. A capacidade de criar rotas personalizadas será útil, por exemplo, se você quiser rotear o tráfego entre sub-redes por meio de um firewall. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar uma tabela de rotas
> * Criar uma rota
> * Associar uma tabela de rotas a uma sub-rede de rede virtual
> * Testar o roteamento
> * Solucionar problemas de roteamento

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon no Portal do Azure em http://portal.azure.com.

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Por padrão, o Azure roteia o tráfego entre todas as sub-redes em uma rede virtual. Para saber mais sobre as rotas padrão do Azure, consulte [Rotas do sistema](virtual-networks-udr-overview.md). Para substituir o roteamento padrão do Azure, você cria uma tabela de rotas que contém rotas e associa a tabela de rotas a uma sub-rede de rede virtual.

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede**e, em seguida, selecione**Tabela de rotas**.
3. Selecione a **Assinatura**, selecione ou insira as informações a seguir e selecione **Criar**:
 
    ![Criar tabela de rotas](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Criar uma rota

Uma tabela de rotas contém zero ou mais rotas. 

1. Na caixa *Pesquisar recursos, serviços e documentos* na parte superior do portal, comece digitando *myRouteTablePublic*. Quando **myRouteTablePublic** aparecer nos resultados da pesquisa, selecione-o.
2. Em **CONFIGURAÇÕES**, selecione **Rotas** e, em seguida, selecione **+ Add**, como mostrado na imagem a seguir:

    ![Adicionar rota](./media/tutorial-create-route-table-portal/add-route.png) 
 
4. Em **Adicionar rota**, selecione ou insira as informações a seguir e selecione **OK**:
    - **Nome da rota**: *ToPrivateSubnet*
    - **Prefixo de endereço**: 10.0.1.0/24
    - **Tipo do próximo salto**: selecione **Solução de virtualização** .
    - **Endereço do próximo salto**: 10.0.2.4

    A rota direcionará todo o tráfego destinado ao prefixo de endereço 10.0.1.0/24 por meio de uma solução de virtualização de rede com o endereço IP 10.0.2.4. A solução de virtualização de rede e sub-rede com o prefixo de endereço especificado serão criadas em etapas posteriores. A rota substitui o roteamento padrão do Azure, que roteia o tráfego entre sub-redes diretamente. Cada rota especifica um tipo do próximo salto. O tipo do próximo salto instrui o Azure como rotear o tráfego. Nesse exemplo, o tipo do próximo salto é *VirtualAppliance*. Para saber mais sobre todos os tipos disponíveis do próximo salto no Azure, e quando utilizá-los, consulte [tipos do próximo salto](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Antes de poder associar uma tabela de rotas a uma sub-rede, será necessário criar uma rede virtual e uma sub-rede:

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede** e, sem seguida, selecione **Rede Virtual**.
3. Em **Criar rede virtual**, selecione, ou insira as informações a seguir, e selecione **Criar**:
    
    - **Nome**: *myVirtualNetwork*
    - **Espaço de endereço**: *10.0.0.0/16*
    - **Assinatura**: selecione sua assinatura.
    - **Grupo de recursos**: selecione **Usa existente** e selecione **myResourceGroup**.
    - **Localização**: selecione *Leste dos EUA*
    - **Nome da sub-rede**: *Público*
    - **Intervalo de endereços:** *10.0.0.0/24*

4. Na caixa **Pesquisar recursos, serviços e documentos** na parte superior do portal, comece digitando *myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-o.
5. Adicione duas sub-redes adicionais à rede virtual. Em **CONFIGURAÇÕES**, selecione **Sub-redes** e, em seguida, selecione **+ Sub-rede**, conforme mostrado na imagem a seguir:

    ![Adicionar sub-rede](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Selecione ou insira as informações a seguir e selecione **OK**:
    - **Nome**: Privado
    - **Espaço de endereço**: *10.0.1.0/24*

7. Complete as etapas 5 e 6 novamente, fornecendo as informações a seguir:
    - **Nome**: DMZ
    - **Espaço de endereço**: *10.0.2.0/24*

É possível associar uma tabela de rotas para zero ou mais sub-redes. Uma sub-rede pode ter zero ou uma tabela de rotas associada a ela. O tráfego de saída de uma sub-rede é roteado com base nas rotas padrão do Azure e quaisquer rotas personalizadas adicionadas a uma tabela de rotas associadas a uma sub-rede. Associe a tabela de rotas *myRouteTablePublic* à sub-rede *Pública*:

1. A caixa **myVirtualNetwork - Subnets** é exibida após completar a etapa anterior. Em **CONFIGURAÇÕES**, selecione **Sub-redes** e, em seguida, selecione **Público**.
2. Conforme mostrado na imagem a seguir, selecione **Tabela de rotas** e, em seguida, selecione **MyRouteTablePublic**.

    ![Associar tabela de rotas](./media/tutorial-create-route-table-portal/associate-route-table.png) 

3. Selecione **Salvar**.

## <a name="test-routing"></a>Testar o roteamento

Para testar o roteamento, será necessário criar uma máquina virtual que serve como a solução de virtualização de rede em que a rota criada em uma etapa anterior passa. Após criar a solução de virtualização de rede, será necessário implantar uma máquina virtual nas sub-redes *Pública* e *Privada*. Em seguida, você roteará o tráfego da sub-rede *Pública* para a sub-rede *Privada* por meio da solução de virtualização de rede.

### <a name="create-a-network-virtual-appliance"></a>Criar uma solução de virtualização de rede

Em uma etapa anterior, você criou uma rota que especificou uma solução de virtualização como o tipo do próximo salto. Uma máquina virtual executando um aplicativo de rede é frequentemente referida como uma solução de virtualização de rede. Em ambientes de produção, a solução de virtualização de rede que você implanta é frequentemente uma máquina virtual pré-configurada. Várias soluções de virtualização de rede estão disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). Neste artigo, é criada uma máquina virtual básica.

1. Selecione **Criar um recurso** no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**. É possível selecionar um sistema operacional diferente, mas as etapas restantes assumirão que está selecionado o **Windows Server 2016 Datacenter**. 
3. Selecione ou insira as informações a seguir para **Básico** e, em seguida, selecione **OK**:
    - **Nome**: *myVmNva*
    - **Grupo de recursos**: selecione **Usar existente** e, em seguida, selecione *myResourceGroup*.
    - **Localização**: selecione *Leste dos EUA*.

    O **Nome de usuário** e **Senha** inseridos serão utilizados em uma etapa posterior. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). A **Localização** e **Assinatura** selecionadas deverão ser iguais à localização e assinatura da rede virtual onde estão. Não é necessário selecionar o mesmo grupo de recursos em que a rede virtual foi criada, porém, o mesmo grupo de recursos foi selecionado para este tutorial.
4. Selecione um tamanho da VM em **Escolher um tamanho**.
5. Selecione ou insira as informações a seguir para **Configurações** e, em seguida, selecione **OK**:
    - **Rede virtual**: certifique-se de que **myVirtualNetwork** está selecionada. Se não, selecione **Rede Virtual** e, em seguida, selecione **myVirtualNetwork** em **Escolher rede virtual**.
    - **Sub-rede**: selecione **Sub-rede** e, em seguida, selecione **DMZ** em **Escolher sub-rede**.
    - **Endereço IP público**: selecione **Endereço IP públicos** e selecione **Nenhum** em **Escolher endereço IP público**. Nenhum endereço IP público é atribuído a esta máquina virtual, uma vez que não será conectado da Internet.
6. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implantação da máquina virtual.

A criação da máquina virtual demora alguns minutos. Não prossiga para a próxima etapa até que o Azure termine de criar a máquina virtual e uma caixa com informações sobre a máquina virtual seja aberta.

Quando a Azure criou a máquina virtual, também criou uma [interface de rede](virtual-network-network-interface.md) na sub-rede *DMZ* e anexou a interface de rede à máquina virtual. O encaminhamento de IP deve ser habilitado para cada interface de rede do Azure que encaminha o tráfego destinado a qualquer endereço IP que não esteja atribuído à interface de rede.

1. Na caixa que abriu para a máquina virtual após ser criada, em **CONFIGURAÇÕES** , selecione **Rede** e, em seguida, selecione **myvmnva158** (a interface de rede do Azure criada para a máquina virtual tem um número diferente após **myvmnva**), conforme mostrado na imagem a seguir:

    ![Rede da máquina virtual](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

    Quando você criou a solução de virtualização de rede na sub-rede *DMZ*, o Azure criou automaticamente a interface de rede, anexou a interface de rede à máquina virtual e atribuiu o endereço de IP privado *10.0.2.4* à interface de rede. 

2. Em **CONFIGURAÇÕES**, selecione **Configurações de IP**, selecione **Habilitado** para **Encaminhamento de IP** e, em seguida, selecione **Salvar**, conforme mostrado na imagem a seguir:

    ![Habilitar o encaminhamento de IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas máquinas virtuais na rede virtual para que seja possível validar esse tráfego da sub-rede *Pública* roteada para a sub-rede *Privada* por meio da solução de virtualização de rede em uma etapa posterior.

Complete as etapas de 1 a 6 de [Criar uma solução de virtualização de rede](#create-a-network-virtual-appliance). Utilize as mesmas configurações nas etapas 3 e 5, exceto para as alterações a seguir:

|Máquina virtual   |NOME      |Sub-rede      | Endereço IP público     |
|---------         |--------- | -----------|---------              |
|Máquina virtual 1 | myVmWeb  | Público     | Aceitar o padrão do portal |
|máquina virtual 2 | myVmMgmt | Privado    | Aceitar o padrão do portal |

Você pode criar a máquina virtual *myVmMgmt* enquanto o Azure cria a máquina virtual *myVmWeb*. Não continue com as etapas seguintes até que o Azure termine de criar ambas as máquinas virtuais.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Rotear o tráfego por meio de uma solução de virtualização de rede

1. Nas caixa *Pesquisar* na parte superior do portal, comece digitando *myVmMgmt*. Quando **myVmMgmt** aparecer na caixa de resultados, selecione-o.
2. Crie uma conexão de área de trabalho remota para a máquina virtual *myVmMgmt*, selecionando **Conectar**, conforme mostrado na imagem a seguir:

    ![Conectar-se à máquina virtual](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Para conectar-se à VM, abra o arquivo RDP baixado. Se solicitado, selecione **Conectar**.
4. Insira o nome de usuário e senha especificados ao criar a máquina virtual (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a máquina virtual) e selecione **OK**.
5. Você pode receber um aviso do certificado durante o processo de logon. Selecione **Sim** para prosseguir com a conexão.
6. Em uma etapa posterior, o comando tracert.exe será utilizado para testar o roteamento. O Tracert usa ICMP, que é negado por meio do Firewall do Windows. Habilite o ICMP através do firewall do Windows, inserindo o seguinte comando a partir do prompt de comando:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

    Embora o Tracert seja utilizado para testar o roteamento neste artigo, não é recomendável o ICMP através do Firewall do Windows para implantações de produção.
7. Você habilitou o encaminhamento de IP no Azure para a interface de rede da máquina virtual em [Habilitar encaminhamento de IP](#enable-ip-forwarding). Dentro da máquina virtual, o sistema operacional, ou um aplicativo em execução na máquina virtual, também pode ser capaz de encaminhar o tráfego. Quando você implanta uma solução de virtualização de rede em um ambiente de produção, a solução geralmente filtra, registra ou executa alguma outra função antes de encaminhar o tráfego. No entanto, neste artigo, o sistema operacional simplesmente encaminha todo o tráfego que recebe. Habilite o encaminhamento de IP no sistema operacional do *myVmNva*, completando as etapas seguintes da máquina virtual *myVmMgmt*:

    Área de trabalho remota para a máquina virtual *myVmNva* com o seguinte comando a partir de um prompt de comando:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Para habilitar o encaminhamento de IP dentro do sistema operacional, insira o comando a seguir no PowerShell:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Reinicie a máquina virtual, que também irá desconectar a sessão da área de trabalho remota.
8. Enquanto ainda estiver conectado à máquina virtual *myVmMgmt* após reiniciar a máquina virtual *myVmNva*, crie uma sessão de área de trabalho remota para a máquina virtual *myVmWeb* com o comando a seguir:

    ``` 
    mstsc /v:myVmWeb
    ```
    
    Habilite o ICMP através do firewall do Windows, inserindo o seguinte comando a partir do prompt de comando:

    ```
    netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
    ```

9. Para testar o roteamento do tráfego para a máquina virtual *myVmMgmt* da máquina virtual *myVmWeb*, insira o seguinte comando a partir de um prompt de comando:

    ```
    tracert myvmmgmt
    ```

    A resposta é semelhante ao seguinte exemplo:
    
    ```
    Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Você pode ver que o primeiro salto é 10.0.2.4, que é o endereço IP privado da solução de virtualização de rede. O segundo salto é 10.0.1.4, o endereço IP privado da máquina virtual *myVmMgmt*. A rota adicionada à tabela de rotas *myRouteTablePublic* e associada à sub-rede *Pública* fez o Azure encaminhar o tráfego através de NVA, em vez de diretamente para a sub-rede *Privada*.
10.  Feche a sessão da área de trabalho remota para a máquina virtual *myVmWeb*, que ainda mantém conectado à máquina virtual *myVmMgmt*.
11. Para testar o roteamento do tráfego para a máquina virtual *myVmWeb* da máquina virtual *myVmMgmt*, insira o seguinte comando a partir de um prompt de comando:

    ```
    tracert myvmweb
    ```

    A resposta é semelhante ao seguinte exemplo:

    ```
    Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Você pode ver que o tráfego é roteado diretamente da máquina virtual *myVmMgmt* para a máquina virtual *myVmWeb*. Por padrão, o Azure roteia o tráfego diretamente entre sub-redes.
12. Feche a sessão da área de trabalho remota para a máquina virtual *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Solucionar problemas de roteamento

Como você aprendeu nas etapas anteriores, o Azure aplica rotas padrão que opcionalmente podem ser substituídas pelas suas próprias rotas. Às vezes, o tráfego pode não ser roteado como você espera que seja. É possível usar a capacidade de [	Próximo salto](../network-watcher/network-watcher-check-next-hop-portal.md) do Observador de Rede do Azure para determinar como o Azure está roteando o tráfego entre duas máquinas virtuais. 

1. Na caixa *Pesquisar* na parte superior do portal, comece digitando *Observador de Rede*. Quando os resultados da pesquisa exibirem **Observador de Rede**, selecione essa opção.
2. Em **FERRAMENTAS DE DIAGNÓSTICO DE REDE**, selecione **Próximo salto**.
3. Para testar o roteamento de tráfego da máquina virtual *myVmWeb* (10.0.0.4) para a máquina virtual *myVmMgmt* (10.0.1.4), selecione sua assinatura, insira as informações mostradas na imagem a seguir (o nome da **interface de rede** é ligeiramente diferente) e, em seguida, selecione **Próximo salto**:

    ![Próximo salto](./media/tutorial-create-route-table-portal/next-hop.png)  

    A saída do **Resultado** informa que o endereço IP do próximo salto para o tráfego de *myVmWeb* para *myVmMgmt* é 10.0.2.4 (a máquina virtual *myVmNva*), que o tipo do próximo salto é *VirtualAppliance* e que a tabela de rotas que causa o roteamento é *myRouteTablePublic*.

As rotas efetivas para cada interface de rede são uma combinação das rotas padrão do Azure e quaisquer rotas que você definir. Para ver todas as rotas efetivas para uma interface de rede em uma máquina virtual, conclua as etapas a seguir:

1. Na caixa *Pesquisar* na parte superior do portal, comece digitando *myVmWeb*. Quando **myVmWeb** apareces nos resultados da pesquisa, selecione-o.
2. Em **CONFIGURAÇÕES**, selecione **rede** e, em seguida, selecione **myvmweb369** (a interface de rede que o Azure criou para a máquina virtual tem um número diferente após **myvmweb**).
3. Em **SUPORTE + SOLUÇÃO DE PROBLEMAS**, selecione **Rotas efetivas**, conforme mostrado na imagem a seguir:

    ![Rotas efetivas](./media/tutorial-create-route-table-portal/effective-routes.png) 

    Você visualiza as rotas padrão do Azure e a rota que você adicionou na tabela de rotas *myRouteTablePublic*. Para saber mais sobre como o Azure seleciona uma rota na lista de rotas, consulte [Como o Azure seleciona uma rota](virtual-networks-udr-overview.md#how-azure-selects-a-route).

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos e todos os recursos que ele contém: 

1. Insira *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando aparecer **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Insira *myResourceGroup* para **DIGITAR O NOME DO GRUPO DE RECURSOS:** e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou uma tabela de rotas e associou-a a uma sub-rede. Você criou uma solução de virtualização de rede que roteou o tráfego de uma sub-rede pública para uma sub-rede privada. Embora seja possível implantar muitos recursos do Azure dentro de uma rede virtual, os recursos para alguns serviços PaaS do Azure não podem ser implantados em uma rede virtual. Ainda assim, é possível restringir acesso aos recursos de alguns serviços PaaS do Azure somente para tráfego de uma sub-rede de rede virtual. Avance para o próximo tutorial para aprender como restringir o acesso à rede para recursos PaaS do Azure.

> [!div class="nextstepaction"]
> [Restringir o acesso à rede para recursos PaaS](virtual-network-service-endpoints-configure.md#azure-portal)
