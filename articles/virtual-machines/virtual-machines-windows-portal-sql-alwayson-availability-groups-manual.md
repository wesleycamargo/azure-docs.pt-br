---
title: "Configurar manualmente o grupo de disponibilidade AlwaysOn na VM do Azure – Microsoft Azure"
description: "Crie um grupo de disponibilidade AlwaysOn em máquinas virtuais do Azure. Este tutorial usa principalmente a interface do usuário e ferramentas em vez de scripts."
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: timlt
editor: monicar
tags: azure-service-management
ms.assetid: 986cbc2e-553d-4eba-8acb-c34ad7fd1d8b
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 10/21/2016
ms.author: MikeRayMSFT
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 6b102884c7d5aafc85acdeb52a92b630e6e99b6a


---
# <a name="configure-always-on-availability-group-in-azure-vm-manually---resource-manager"></a>Configurar manualmente o grupo de disponibilidade Always On na VM do Azure - Resource Manager
> [!div class="op_single_selector"]
> * [Resource Manager: Modelo](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [Resource Manager: Manual](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [Clássico: Interface de usuário](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Clássico: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

<br/>

Este tutorial de ponta a ponta mostra como implementar grupos de disponibilidade do SQL Server em máquinas virtuais do Azure Resource Manager. 

Neste tutorial, você cria os elementos a seguir:

* Uma rede virtual que contém duas sub-redes, incluindo uma de front-end e uma de back-end
* Dois controladores de domínio em um conjunto de disponibilidade com um domínio do AD (Active Directory) 
* Duas VMs do SQL Server em um conjunto de disponibilidade implantado na sub-rede de back-end e ingressado no domínio do AD
* Um cluster WSFC de três nós com o modelo de quórum de Nó Principal
* Um balanceador de carga interno com um ou mais endereços IP para dar suporte a um ou mais ouvintes do grupo de disponibilidade
* Um grupo de disponibilidade com duas réplicas de confirmação síncrona de um banco de dados de disponibilidade

A figura abaixo é uma representação gráfica da solução.

![Arquitetura para o grupo de disponibilidade no Azure Resource Manager](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/00-EndstateSampleNoELB.png)

Esta é uma configuração possível. Você pode modificá-la de acordo com suas necessidades. Por exemplo, você pode reduzir o número de máquinas virtuais usando um controlador de domínio como a testemunha de compartilhamento de arquivos de quorum. Isso reduziria o número de VMs para um grupo de disponibilidade de duas réplicas. Esse método reduz a contagem de VMs para uma a menos do que o número encontrado na solução.

Este tutorial cria um único grupo de disponibilidade com um endereço IP do ouvinte. Você também pode criar vários grupos de disponibilidade, cada um com um endereço IP e um ouvinte. Todos os endereços IP usam o mesmo balanceador de carga. Para configurar vários endereços IP em um balanceador de carga, use o PowerShell. Para obter mais informações, veja [Configurar um ou mais ouvintes do grupo de disponibilidade AlwaysOn – Resource Manager](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

A conclusão deste tutorial leva algumas horas porque você deve compilar e configurar várias máquinas virtuais do Azure. Você também pode compilar essa solução inteira automaticamente. No Portal do Azure, há uma configuração de galeria para grupos de disponibilidade Always On com um ouvinte. A configuração da galeria configura automaticamente tudo o que você precisa para grupos de disponibilidade. Para saber mais, confira [Portal - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

[!INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

Este tutorial pressupõe o seguinte:

* Você já tem uma conta do Azure.
* Você já sabe como provisionar uma VM do SQL Server da galeria de máquinas virtuais usando a GUI. Para obter mais informações, consulte [Provisionando uma máquina virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* Você já tem uma compreensão sólida dos grupos de disponibilidade. Para obter mais informações, confira [Grupos de Disponibilidade AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Se você estiver interessado em usar os grupos de disponibilidade com o SharePoint, confira também [Configure SQL Server 2012 Always On Availability Groups for SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx)(Configurar grupos de disponibilidade AlwaysOn do SQL Server 2012 para o SharePoint 2013).
> 
> 

## <a name="create-resource-group"></a>Criar grupo de recursos
1. Entre no [Portal do Azure](http://portal.azure.com). 
2. Clique em **+Novo** e, em seguida, digite **Grupo de recursos** na janela de pesquisa do **Marketplace**.
   
    ![Grupo de recursos](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/01-resourcegroupsymbol.png)
3. Clique em **Grupo de recursos** 
   
    ![Novo grupo de recursos](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/01-newresourcegroup.png)
4. Clique em **Criar**. 
5. Na folha **Grupo de recursos**, em **Nome do grupo de recursos**, digite **SQL-HA-RG**
6. Se você tiver várias assinaturas do Azure, verifique se a assinatura é aquela na qual você deseja criar o grupo de disponibilidade. 
7. Selecione um local. A localização é a região do Azure onde você deseja criar o grupo de disponibilidade. Neste tutorial, vamos compilar todos os recursos em uma localização do Azure. 
8. Verifique se a opção **Fixar no painel** está marcada. Essa configuração opcional coloca um atalho para o grupo de recursos no painel do Portal do Azure. 
9. Clique em **Criar** para criar o grupo de recursos.

O Azure cria o grupo de recursos e fixa um atalho para esse grupo de recursos no portal.

## <a name="create-network-and-subnets"></a>Criar rede e sub-redes
A próxima etapa é criar as redes e sub-redes no grupo de recursos do Azure.

A solução usa uma rede virtual com duas sub-redes. A [Visão geral da Rede Virtual](../virtual-network/virtual-networks-overview.md) fornece mais informações sobre as redes no Azure.

Para criar a rede virtual:

1. No Portal do Azure, clique no novo grupo de recursos e depois em **+** para adicionar um novo item ao grupo de recursos. O Azure abre a folha **Tudo** . 
   
   ![Novo Item](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/02-newiteminrg.png)
2. Pesquise pela **rede virtual**.
   
     ![Pesquisar pela Rede Virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/04-findvirtualnetwork.png)
3. Clique em **Rede virtual**.
4. Na folha **Rede virtual**, clique no modelo de implantação **Resource Manager** e depois em **Criar**.
   
     ![Criar rede virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/05-createvirtualnetwork.png)
5. Configure a rede virtual na folha **Criar rede virtual** . 
   
   A tabela a seguir mostra as configurações para a rede virtual.
   
   | **Campo** | Valor |
   | --- | --- |
   | **Nome** |autoHAVNET |
   | **Espaço de endereço** |10.0.0.0/16 |
   | **Nome da sub-rede** |Subnet-1 |
   | **Intervalo de endereços da sub-rede** |10.0.0.0/24 |
   | **Assinatura** |Especifique a assinatura que você pretende usar. Se você tiver apenas uma assinatura, esta opção poderá ficar em branco. |
   | **Localidade** |Especifique a localização do Azure. |
   
    Observe que o espaço de endereço e o intervalo de endereços da sub-rede podem ser diferentes daqueles na tabela. Dependendo da sua assinatura, o Azure especificará automaticamente um espaço de endereço disponível e o intervalo de endereços de sub-rede correspondente. Se não houver espaço de endereço disponível suficiente, use uma assinatura diferente. 
6. Clique em **Criar**
   
   ![Configurar Rede Virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/06-configurevirtualnetwork.png)

O Azure faz com que você retorne ao painel do portal e lhe notifica quando a nova rede é criada.

### <a name="create-the-second-subnet"></a>Crie a segunda sub-rede
Neste ponto, sua rede virtual conterá uma sub-rede chamada Subnet-1. Os controladores de domínio usam essa sub-rede. Os servidores SQL usam uma segunda sub-rede chamada **Subnet-2**. Para configurar a Subnet-2:

1. No seu painel, clique no grupo de recursos que você criou, **SQL-HA-RG**. Localize a rede no grupo de recursos em **Recursos**.
   
    Se **SQL-HA-RG** não estiver visível, encontre-o clicando em **Grupos de Recursos** e filtrando pelo nome do grupo de recursos.
2. Clique em **autoHAVNET** na lista de recursos. O Azure abre a folha de configuração de rede.
3. Na rede virtual **autoHAVNET**, clique em**Todas as configurações.**
4. **Na folha **Configurações**, clique em **Sub-redes**.
   
    Observe a sub-rede que você já criou. 
   
    ![Configurar Rede Virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/07-addsubnet.png)
5. Crie uma segunda sub-rede. Clique em **+ Subnet (+ Sub-rede)**. 
6. Na folha **Adicionar Sub-Rede**, configure a sub-rede digitando **subnet-2** em **Nome**. O Azure especifica automaticamente um **Intervalo de endereços**válido. Verifique se este intervalo de endereços tem pelo menos 10 endereços nele. Em um ambiente de produção, você poderá exigir mais endereços. 
7. Clique em **OK**.
   
    ![Configurar Rede Virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/08-configuresubnet.png)

Aqui está um resumo das definições de configuração para a rede virtual e ambas as sub-redes.

| **Campo** | Valor |
| --- | --- |
| **Nome** |**autoHAVNET** |
| **Espaço de endereço** |Depende dos espaços de endereço disponíveis em sua assinatura. Um valor típico é 10.0.0.0/16 |
| **Nome da sub-rede** |**Subnet-1** |
| **Intervalo de endereços da sub-rede** |Depende dos intervalos de endereço disponíveis em sua assinatura. Um valor típico é 10.0.0.0/24. |
| **Nome da sub-rede** |**Subnet-2** |
| **Intervalo de endereços da sub-rede** |Depende dos intervalos de endereço disponíveis em sua assinatura. Um valor típico é 10.0.1.0/24. |
| **Assinatura** |Especifique a assinatura que você pretende usar. |
| **Grupo de recursos** |**SQL-HA-RG** |
| **Localidade** |Especifique o mesmo local que você escolheu para o grupo de recursos. |

## <a name="create-availability-sets"></a>Criar conjuntos de disponibilidade
Antes de criar máquinas virtuais, você precisa criar conjuntos de disponibilidade. Os conjuntos de disponibilidade reduzem o tempo de inatividade para eventos de manutenção planejados ou não. Um conjunto de disponibilidade do Azure é um grupo lógico de recursos que o Azure coloca em domínios de falha física e em domínios de atualização. Um domínio de falha garante que os membros do conjunto de disponibilidade terão recursos de energia e rede separados. Um domínio de atualização garante que os membros do conjunto de disponibilidade não serão desativados para manutenção ao mesmo tempo. [Gerenciar a disponibilidade de máquinas virtuais](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Você precisará de dois conjuntos de disponibilidade. Um é para os controladores de domínio. A segunda é para os SQL Servers.

Para criar um conjunto de disponibilidade, acesse o grupo de recursos e clique em **Adicionar**. Filtre os resultados digitando **Conjunto de disponibilidade**. Clique em **Conjunto de Disponibilidade** nos resultados. Clique em **Criar**.

Configure dois conjuntos de disponibilidade de acordo com os parâmetros na tabela a seguir.

| **Campo** | Conjunto de Disponibilidade do Controlador de Domínio | Conjunto de Disponibilidade do SQL Server |
| --- | --- | --- |
| **Nome** |adavailabilitySet |sqlAvailabilitySet |
| **Grupo de recursos** |SQL-HA-RG |SQL-HA-RG |
| **Domínios de falha** |3 |3 |
| **Domínios de atualização** |5 |3 |

Depois de criar os conjuntos de disponibilidade, retorne ao grupo de recursos no Portal do Azure.

## <a name="create-domain-controllers"></a>Criar controladores de domínio
Após você criar a rede, as sub-redes, os conjuntos de disponibilidade e um balanceador de carga para a Internet. Você está pronto para criar as máquinas virtuais para os controladores de domínio.

### <a name="create-the-virtual-machines-for-the-domain-controllers"></a>Criar as máquinas virtuais para os controladores de domínio
Para criar e configurar os controladores de domínio, retorne para o grupo de recursos **SQL-HA-RG** .

1. Clique em Adicionar. A folha **Tudo** é aberta.
2. Digite **Windows Server 2012 R2 Datacenter**. 
3. Clique em **Windows Server 2012 R2 Datacenter**. Na folha **Windows Server 2012 R2 Datacenter**, verifique se o modelo de implantação está definido como **Resource Manager** e clique em **Criar**. O Azure abre a folha **Criar máquina virtual** . 

Conclua as etapas anteriores duas vezes para criar duas máquinas virtuais. Nomeie as duas máquinas virtuais:

* ad-primary-dc
* ad-secondary-dc
  
  > [!NOTE]
  > **ad-secondary-dc** é um componente opcional para fornecer alta disponibilidade para os Serviços de Domínio do Active Directory. 
  > 
  > 

A tabela a seguir mostra as configurações para esses dois computadores.

| **Campo** | Valor |
| --- | --- |
| **Nome de usuário** |DomainAdmin |
| **Senha** |Contoso!0000 |
| **Assinatura** |*sua assinatura* |
| **Grupo de recursos** |SQL-HA-RG |
| **Localidade** |*seu local* |
| **Tamanho** |D1_V2 (Padrão) |
| **Tipo de armazenamento** |padrão |
| **Conta de armazenamento** |*Criada automaticamente* |
| **Rede virtual** |autoHAVNET |
| **Sub-rede** |Subnet-1 |
| **Endereço IP público** |*Mesmo nome que a VM* |
| **Grupo de Segurança de Rede** |*Mesmo nome que a VM* |
| **Diagnostics** |Habilitado |
| **Conta de armazenamento de diagnóstico** |*Criada automaticamente* |
| **Conjunto de disponibilidade** |adavailabilitySet |

> [!NOTE]
> Você não pode alterar o conjunto de disponibilidade em uma VM após sua criação.
> 
> 

O Azure cria as máquinas virtuais.

Depois que as máquinas virtuais forem criadas, configure o controlador de domínio.

### <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio
Nas etapas a seguir, configure o computador **ad-primary-dc** como um controlador de domínio para corp.contoso.com.

1. No portal, abra o grupo de recursos **SQL-HA-RG** e selecione o computador **ad-primary-dc**. Na folha **ad-primary-dc**, clique em **Conectar** para abrir um arquivo RDP para acesso à área de trabalho remota.
   
    ![Conectar à Máquina Virtual](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/20-connectrdp.png)
2. Faça logon com sua conta de administrador (**\DomainAdmin**) e senha (**Contoso!0000**) configuradas.
3. Por padrão, o painel **Gerenciador do Servidor** deve ser exibido.
4. Clique no link **Adicionar funções e recursos** no painel.
   
    ![Adicionar Funções ao Gerenciador de Servidores](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784623.png)
5. Selecione **Avançar** até alcançar a seção **Funções de Servidor**.
6. Selecione **Active Directory Domain Services** e as funções do **Servidor DNS**. Quando solicitado, acrescente os recursos adicionais necessários para essas funções.
   
   > [!NOTE]
   > Windows avisa que não há nenhum endereço IP estático. Se você estiver testando a configuração, clique em continuar. Nos cenários de produção, defina o endereço IP como estático no Portal do Azure ou [use o PowerShell para definir o endereço IP estático do computador do controlador de domínio](../virtual-network/virtual-networks-reserved-private-ip.md).
   > 
   > 
   
    ![Adicionar Caixa de Diálogo de Funções](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784624.png)
7. Clique em **Avançar** até alcançar a seção **Confirmação**. Marque a caixa de seleção **Reiniciar cada servidor de destino automaticamente, se necessário** .
8. Clique em **Instalar**.
9. Depois que os recursos forem instalados, retorne para o painel **Gerenciador do Servidor** .
10. Selecione a nova opção **AD DS** no painel esquerdo.
11. Clique no link **Mais** na barra de aviso amarela.
    
    ![Caixa de diálogo do AD DS na VM do servidor DNS](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784625.png)
12. Na coluna **Ação** da caixa de diálogo **Todos os Detalhes de Tarefa do Servidor**, clique em **Promover este servidor a um controlador de domínio**.
13. No **Assistente de Configuração dos Serviços de Domínio do Active Directory**, use os seguintes valores:
    
    | **Página** | Configuração |
    | --- | --- |
    | **Configuração de Implantação** |**Adicionar uma nova floresta** = selecionado<br/>**Nome do domínio raiz** = corp.contoso.com |
    | **Opções de Controlador de Domínio** |**Senha DSRM** = Contoso!0000<br/>**Confirmar Senha** = Contoso!0000 |
14. Clique em **Avançar** para percorrer as outras páginas do assistente. Na página **Verificação de pré-requisitos**, verifique se a seguinte mensagem é exibida: **Todas as verificações de pré-requisitos passaram com êxito**. Observe que você deve examinar todas as mensagens de aviso aplicáveis, porém é possível continuar com a instalação.
15. Clique em **Instalar**. A máquina virtual **ad-primary-dc** é reinicializada automaticamente.

### <a name="configure-the-second-domain-controller"></a>Configurar o segundo controlador de domínio
Após a reinicialização do controlador de domínio primário, você pode configurar o segundo controlador de domínio. Esta etapa opcional é para alta disponibilidade. Para concluir esta etapa, você precisa saber o endereço IP privado do controlador de domínio. Você pode obtê-lo no Portal do Azure. Siga estas etapas para configurar o segundo controlador de domínio.

1. Faça logon no computador **ad-primary-dc** . 
2. Abra a área de trabalho remota e conecte-se ao controlador de domínio secundário pelo endereço IP. Se você não souber o endereço IP do controlador de domínio secundário, acesse o Portal do Azure e verifique o endereço atribuído à interface de rede para o controlador de domínio secundário. 
3. Altere o endereço do servidor DNS preferencial para o endereço do controlador de domínio. 
4. Inicie o arquivo RDP para o controlador de domínio primário (**ad-primary-dc**) e faça logon na VM usando sua conta de administrador (**BUILTIN\DomainAdmin**) e senha (**Contoso!0000**) configuradas.
5. Do controlador de domínio primário, inicie uma Área de Trabalho Remota para **ad-secondary-dc** usando o endereço IP. Use a mesma conta e senha.
6. Quando estiver conectado, você deverá ver o painel **Gerenciador do Servidor** . No painel esquerdo, clique em **Servidor Local** .
7. Selecione o link **Endereço IPv4 atribuído pelo DHCP, habilitado para IPv6** .
8. Na janela **Conexões de Rede** , selecione o ícone de rede.
   
    ![Alterar o servidor DNS preferencial da VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784629.png)
9. Na barra de comandos, clique em **Alterar as configurações desta conexão** (dependendo do tamanho da janela, talvez você precise clicar na seta dupla à direita para ver esse comando).
10. Clique em **Protocolo IP versão 4 (TCP/IPv4)** e clique em Propriedades.
11. Selecione a opção Usar os seguintes endereços de servidor DNS e especifique o endereço do controlador de domínio primário no **Servidor DNS preferencial**.
12. O endereço é aquele atribuído a uma VM na sub-rede subnet-1 na rede virtual do Azure e a VM é a **ad-primary-dc**. Para verificar o endereço IP de **ad-primary-dc**, use **nslookup ad-primary-dc** no prompt de comando, conforme mostrado abaixo.
    
    ![Use NSLOOKUP para localizar o endereço IP para DC](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC664954.png)
    
    > [!NOTE]
    > Depois de configurar o DNS, você poderá perder a sessão RDP para o servidor membro. Nesse caso, reinicie a VM no Portal do Azure.
    > 
    > 
13. Clique em **OK** e em **Fechar** para confirmar as alterações. Agora é possível ingressar a VM em **corp.contoso.com**.
14. Repita as etapas que você seguiu para criar o primeiro controlador de domínio, exceto no **Assistente de Configuração dos Serviços de Domínio do Active Directory**, use os seguintes valores:

| Página | Configuração |
| --- | --- |
| **Configuração de Implantação** |**Adicionar um controlador de domínio a um domínio existente** = selecionado<br/>**Raiz** = corp.contoso.com |
| **Opções de Controlador de Domínio** |**Senha DSRM** = Contoso!0000<br/>**Confirmar Senha** = Contoso!0000 |

### <a name="configure-domain-accounts"></a>Configurar Contas de Domínio
As próximas etapas configuram as contas do AD (Active Directory) para uso posterior.

1. Faça logon novamente no computador **ad-primary-dc** .
2. Em **Gerenciador do Servidor**, selecione **Ferramentas** e, em seguida, clique em **Centro Administrativo do Active Directory**.
   
    ![Centro Administrativo do Active Directory](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784626.png)
3. Na folha **Centro Administrativo do Active Directory** select **corp (local)** no painel esquerdo.
4. No painel **Tarefas** à direita, selecione **Novo** e clique em **Usuário**. Use as configurações a seguir:
   
   | Configuração | Valor |
   | --- | --- |
   |  **Nome** |Instalar |
   |  **SamAccountName do usuário** |Instalar |
   |  **Senha** |Contoso!0000 |
   |  **Confirmar senha** |Contoso!0000 |
   |  **Outras opções de senha** |Selecionado |
   |  **A senha nunca expira** |Verificado |
5. Clique em **OK** para criar o usuário de **Instalação**. Use essa conta para configurar o cluster de failover e o grupo de disponibilidade.
6. Crie dois usuários adicionais com as mesmas etapas: **CORP\SQLSvc1** e **CORP\SQLSvc2**. Os serviços do SQL Server usam essas contas. Em seguida, conceda as permissões necessárias ao **CORP\Install** para configurar o WSFC (Clustering de Failover do Serviço Windows).
7. No **Centro Administrativo do Active Directory**, selecione **corp (local)** no painel esquerdo. Em seguida, no painel **Tarefas** à direita, clique em **Propriedades**.
   
    ![Propriedades do usuário CORP](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784627.png)
8. Selecione **Extensões** e clique no botão **Avançado** na guia **Segurança**.
9. Na caixa de diálogo **Configurações de Segurança Avançadas para corp** . Clique em **Adicionar**.
10. Clique em **Selecione uma entidade**. Em seguida, procure **CORP\Install**. Clique em **OK**.
11. Selecione as permissões **Ler todas as propriedades** e **Criar objetos de computador**.
    
     ![Permissões de usuário corp](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784628.png)
12. Clique em **OK** e em **OK** novamente. Feche a janela de propriedades corporativas.

Agora que você concluiu a configuração do Active Directory e dos objetos de usuário, crie duas VMs do SQL Server, uma VM de servidor testemunha. Em seguida, faça com que todas as três ingressem no domínio.

## <a name="create-sql-servers"></a>Criar Servidores SQL
### <a name="create-and-configure-the-sql-server-vms"></a>Criar e configurar as VMs do SQL Server
Em seguida, crie três VMs, incluindo duas VMs do SQL Server e um nó de cluster do WSFC. Para criar cada uma das VMs, volte para o grupo de recursos **SQL-HA-RG**, clique em **Adicionar**, pesquise pelo item da galeria apropriado, **Máquina Virtual** e, em seguida, **Da Galeria**. Em seguida, use os modelos na tabela a seguir para ajudá-lo a criar as VMs.

| Página | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selecione o item da galeria apropriado |**Windows Server 2012 R2 Datacenter** |**SQL Server 2014 SP1 Enterprise no Windows Server 2012 R2** |**SQL Server 2014 SP1 Enterprise no Windows Server 2012 R2** |
|  **Noções básicas** |**Nome** = cluster-fsw<br/>**Nome de usuário** = DomainAdmin<br/>**Senha** = Contoso!0000<br/>**Assinatura** = Sua assinatura<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Local** = Seu local do Azure |**Nome** = sqlserver-0<br/>**Nome de usuário** = DomainAdmin<br/>**Senha** = Contoso!0000<br/>**Assinatura** = Sua assinatura<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Local** = Seu local do Azure |**Nome** = sqlserver-1<br/>**Nome de usuário** = DomainAdmin<br/>**Senha** = Contoso!0000<br/>**Assinatura** = Sua assinatura<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Local** = Seu local do Azure |
|  **Tamanho** |DS1 (Um núcleo, 3,5 GB de memória) |**TAMANHO** = DS 2 (dois núcleos, 7 GB de memória) |**TAMANHO** = DS 2 (dois núcleos, 7 GB de memória) |
|  **Configurações** |**Armazenamento** = Premium (SSD)<br/>**SUB-REDES** = autoHAVNET<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**Sub-rede** = subnet-2(10.1.1.0/24)<br/>**Endereço IP público** = Nenhum<br/>**Grupo de segurança de rede** = Nenhum<br/>**Monitorando Diagnóstico** = Habilitado<br/>**Conta de armazenamento de diagnóstico** = Use uma conta de armazenamento gerada automaticamente<br/>**CONJUNTO DE DISPONIBILIDADE** = sqlAvailabilitySet<br/> |**Armazenamento** = Premium (SSD)<br/>**SUB-REDES** = autoHAVNET<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**Sub-rede** = subnet-2(10.1.1.0/24)<br/>**Endereço IP público** = Nenhum<br/>**Grupo de segurança de rede** = Nenhum<br/>**Monitorando Diagnóstico** = Habilitado<br/>**Conta de armazenamento de diagnóstico** = Use uma conta de armazenamento gerada automaticamente<br/>**CONJUNTO DE DISPONIBILIDADE** = sqlAvailabilitySet<br/> |**Armazenamento** = Premium (SSD)<br/>**SUB-REDES** = autoHAVNET<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**Sub-rede** = subnet-2(10.1.1.0/24)<br/>**Endereço IP público** = Nenhum<br/>**Grupo de segurança de rede** = Nenhum<br/>**Monitorando Diagnóstico** = Habilitado<br/>**Conta de armazenamento de diagnóstico** = Use uma conta de armazenamento gerada automaticamente<br/>**CONJUNTO DE DISPONIBILIDADE** = sqlAvailabilitySet<br/> |
|  **Definições do SQL Server** |Não aplicável |**Conectividade SQL** = Particular (em rede virtual)<br/>**Porta** = 1433<br/>**Autenticação SQL** = Desabilitar<br/>**Configuração de armazenamento** = Geral<br/>**Aplicação de patch automatizada** = Domingo às 2:00<br/>**Backup automatizado** = Desabilitado</br>**Integração do Cofre de Chaves do Azure** = Desabilitado |**Conectividade SQL** = Particular (em rede virtual)<br/>**Porta** = 1433<br/>**Autenticação SQL** = Desabilitar<br/>**Configuração de armazenamento** = Geral<br/>**Aplicação de patch automatizada** = Domingo às 2:00<br/>**Backup automatizado** = Desabilitado</br>**Integração do Cofre de Chaves do Azure** = Desabilitado |

<br/>

> [!NOTE]
> A configuração anterior sugere máquinas virtuais de camada STANDARD, porque máquinas de camada BÁSICA não dão suporte aos pontos de extremidade com balanceamento de carga exigidos pelo ouvinte do grupo de disponibilidade. Além disso, os tamanhos de computador sugeridos aqui servem para testar grupos de disponibilidade em VMs do Azure. Para obter o melhor desempenho em cargas de trabalho de produção, consulte as recomendações de configuração e tamanhos de máquina do SQL Server em [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> 
> 

Depois que as três VMs forem totalmente provisionadas, você precisará ingressá-las no domínio **corp.contoso.com** e conceder direitos administrativos CORP\Install às máquinas.

Para ajudar você a prosseguir, anote o endereço IP virtual do Azure para cada VM. Obtenha o endereço IP para cada servidor. No grupo de recursos do Azure SQL-HA-RG clique no recurso **autohaVNET** . A folha **autohaVNET** mostra os endereços IP para cada computador na sua rede.

Registre os endereços IP para os seguintes dispositivos: 

| Função VM | Dispositivo | Endereço IP |
| --- | --- | --- |
| Controlador de domínio primário |ad-primary-dc | |
| Controlador de domínio secundário |ad-secondary-dc | |
| Testemunha de compartilhamento de arquivos de cluster |cluster-fsw | |
| SQL Server |sqlserver-0 | |
| SQL Server |sqlserver-1 | |

Use esses endereços para configurar o serviço DNS para cada VM. Realize as etapas a seguir para cada uma das três VMs.

1. Primeiro, altere o endereço do servidor DNS preferencial para cada servidor membro. 
2. Inicie o arquivo RDP para o controlador de domínio primário (**ad-primary-dc**) e faça logon na VM usando sua conta de administrador (**BUILTIN\DomainAdmin**) e senha (**Contoso!0000**) configuradas.
3. Do controlador de domínio primário, inicie uma Área de Trabalho Remota para o **sqlserver-0** usando o endereço IP. Use a mesma conta e senha.
4. Quando estiver conectado, você deverá ver o painel **Gerenciador do Servidor** . No painel esquerdo, clique em **Servidor Local** .
5. Selecione o link **Endereço IPv4 atribuído pelo DHCP, habilitado para IPv6** .
6. Na janela **Conexões de Rede** , selecione o ícone de rede.
   
    ![Alterar o servidor DNS preferencial da VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784629.png)
7. Na barra de comandos, clique em **Alterar as configurações desta conexão** (dependendo do tamanho da janela, talvez você precise clicar na seta dupla à direita para ver esse comando).
8. Clique em **Protocolo IP versão 4 (TCP/IPv4)** e clique em Propriedades.
9. Selecione a opção Usar os seguintes endereços de servidor DNS e especifique o endereço do controlador de domínio primário no **Servidor DNS preferencial**.
10. O endereço é aquele atribuído a uma VM na sub-rede subnet-1 na rede virtual do Azure e a VM é a **ad-primary-dc**. Para verificar o endereço IP de **ad-primary-dc**, use **nslookup ad-primary-dc** no prompt de comando, conforme mostrado abaixo.
    
    ![Use NSLOOKUP para localizar o endereço IP para DC](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC664954.png)
    
    > [!NOTE]
    > Depois de configurar o DNS, você poderá perder a sessão RDP para o servidor membro. Nesse caso, reinicie a VM no Portal do Azure.
    > 
    > 
11. Clique em **OK** e em **Fechar** para confirmar as alterações. Agora é possível ingressar a VM em **corp.contoso.com**.
12. Volte para a janela **Servidor Local** e clique no link **GRUPO DE TRABALHO**.
13. Na seção **Nome do Computador**, clique em **Alterar**.
14. Marque a caixa de seleção **Domínio** e digite **corp.contoso.com** na caixa de texto. Clique em **OK**.
15. Na caixa de diálogo pop-up **Segurança do Windows**, especifique as credenciais para a conta de administrador de domínio padrão (**CORP\DomainAdmin**) e a senha (**Contoso!0000**).
16. Quando a mensagem "Bem-vindo ao domínio corp.contoso.com" for exibida, clique em **OK**.
17. Clique em **Fechar** e em **Reiniciar Agora** na caixa de diálogo pop-up.
18. Repita essas etapas no servidor de testemunha de compartilhamento de arquivo e em cada SQL Server. 

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Adicione o usuário Corp\Install como um administrador em cada VM de cluster:
1. Aguarde até que a VM seja reiniciada e, em seguida, inicie o arquivo RDP novamente do controlador de domínio primário para fazer logon no **sqlserver-0** usando a conta **BUILTIN\DomainAdmin**.
2. Em **Gerenciador do Servidor**, selecione **Ferramentas** e clique em **Gerenciamento do Computador**.
   
    ![Gerenciamento do Computador](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784630.png)
3. Na janela **Gerenciamento do Computador**, expanda **Usuários e Grupos Locais** e selecione **Grupos**.
4. Clique duas vezes no grupo **Administradores** .
5. Na caixa de diálogo **Propriedades de Administradores**, clique no botão **Adicionar**.
6. Insira o usuário **CORP\Install** e clique em **OK**. Quando solicitado a fornecer as credenciais, use a conta **DomainAdmin** com a senha **Contoso!0000**.
7. Clique em **OK** para fechar a caixa do diálogo **Propriedades do Administrador**.
8. Repita as etapas acima em **sqlserver-1**, e **cluster-fsw**.

## <a name="create-the-cluster"></a>Criar o cluster
### <a name="add-the-failover-clustering-feature-to-each-cluster-vm"></a>Adicione o recurso de **Clustering de Failover** a cada VM de cluster.
1. RDP para **sqlserver-0**.
2. No painel **Gerenciador do Servidor**, clique em **Adicionar funções e recursos**.
3. No **Assistente para Adicionar Funções e Recursos**, clique em **Avançar** até chegar à página **Recursos**.
4. Selecione **Clustering de Failover**. Quando solicitado, adicione todos os outros recursos dependentes.
   
    ![Adicionar o Recurso de Cluster de Failover à VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784631.png)
5. Clique em **Avançar** e em **Instalar** na página **Confirmação**.
6. Quando a instalação do recurso **Clustering de Failover** for concluída, clique em **Fechar**.
7. Faça logoff da VM.
8. Repita as etapas nesta seção em **sqlserver-1**, e **cluster-fsw**.

As VMs do SQL Server agora estão provisionadas e em execução, mas estão instaladas com o SQL Server com as opções padrão.

### <a name="create-the-wsfc-cluster"></a>Criar o Cluster WSFC
Nesta seção, você cria o cluster WSFC para hospedar o grupo de disponibilidade. Agora, você deve ter feito o seguinte para cada uma das três VMs no cluster WSFC:

* Totalmente provisionado no Azure
* Ingressou a VM no domínio
* **CORP\Install** adicionado ao grupo de administradores local
* Recurso de Clustering de Failover adicionado

Todos são pré-requisitos em cada VM para poder ingressá-la no cluster WSFC.

Além disso, observe que a rede virtual do Azure não se comporta da mesma forma que uma rede local. Você precisa criar o cluster na seguinte ordem:

1. Crie um cluster de nó único em um dos nós (**sqlserver-0**).
2. Modifique o endereço IP do cluster para um endereço IP não usado em **sqlsubnet**.
3. Deixe o nome do cluster online.
4. Adicione os outros nós (**sqlserver-1** e **cluster-fsw**).

Siga as etapas abaixo para configurar o cluster.

1. Inicie o arquivo RDP para **sqlserver-0** e faça logon usando a conta de domínio **CORP\Install**.
2. No painel **Gerenciador do Servidor** selecione **Ferramentas**, e clique em **Gerenciador de Cluster de Failover**.
3. No painel esquerdo, clique com o botão direito do mouse em **Gerenciador de Cluster de Failover** e clique em **Criar um Cluster**, conforme mostrado abaixo.
   
    ![Criar Cluster](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784632.png)
4. No Assistente para Criação de Cluster, crie um cluster de um nó percorrendo as páginas com as configurações a seguir:
   
   | Página | Configurações |
   | --- | --- |
   | Antes de começar |Usar padrões |
   | Selecionar Servidores |Digite **sqlserver-0** em **Insira o nome do servidor** e clique em **Adicionar** |
   | Aviso de Validação |Selecione **Não. Eu não preciso de suporte da Microsoft para este cluster e, portanto, não desejo executar testes de validação. Continuar a criar o cluster quando eu clicar em Avançar**. |
   | Ponto de Acesso para Administrar o Cluster |Digite **Cluster1** em **Nome do Cluster** |
   | Confirmação |Use os padrões, a menos que você esteja usando Espaços de Armazenamento. Consulte a observação após esta tabela. |

    >[AZURE.NOTE] Se você estiver usando [Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739), que agrupam vários discos em pools de armazenamento, desmarque a caixa de seleção **Adicione todo o armazenamento qualificado ao cluster** na página **Confirmação**. Se você não desmarcar essa opção, o Windows desanexará os discos virtuais durante o processo de clustering. Como resultado, eles não aparecerão no Gerenciador ou Explorador de Discos até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell.

    Agora que você criou o cluster, verifique a configuração e adicione os nós restantes. 

1. No painel central, role para baixo até a seção **Recursos Principais do Cluster** e expanda os detalhes **Nome: Clutser1**. Você verá os recursos de **Nome** e de **Endereço IP** no estado **Com Falha**. O recurso de endereço IP não pode ficar online porque o cluster recebeu o mesmo endereço IP que a própria máquina, que é um endereço duplicado.
2. Clique com o botão direito do mouse no recurso **Endereço IP** com falha e clique em **Propriedades**.
   
    ![Propriedades do Cluster](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784633.png)
3. Selecione **Endereço IP estático** e especifique um endereço disponível da subnet-2 na caixa de texto Endereço. Em seguida, clique em **OK**.
4. Na seção **Recursos Principais do Cluster**, clique com o botão direito do mouse em **Nome: Cluster1** e clique em **Colocar Online**. Em seguida, aguarde até que ambos os recursos estejam online. Quando o recurso de nome de cluster fica online, ele atualiza o servidor DC com uma nova conta de computador do AD. Use essa conta do AD para executar o serviço clusterizado do grupo de disponibilidade posteriormente.
5. Por fim, você deve adicionar os nós restantes ao cluster. Na árvore do navegador, clique com o botão direito do mouse em **Cluster1.corp.contoso.com** e clique em **Adicionar Nó**, conforme mostrado abaixo.
   
    ![Adicionar Nó ao Cluster](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784634.png)
6. No **Assistente para Adicionar Nó**, clique em **Avançar**. Na página **Selecionar Servidores**, adicione **sqlserver-1** e **cluster-fsw** na lista digitando o nome do servidor em **Insira o nome do servidor** e clicando em **Adicionar**. Quando terminar, clique em **Avançar**.
7. Na página **Aviso de Validação**, clique em **Não** (em um cenário de produção, você deve executar os testes de validação). Em seguida, clique em **Avançar**.
8. Na página **Confirmação**, clique em **Avançar** para adicionar os nós.
   
   > [!WARNING]
   > Se você estiver usando [Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739), os quais agrupam vários discos em pools de armazenamento, desmarque a caixa de seleção **Adicionar todo o armazenamento qualificado ao cluster** . Se você não desmarcar essa opção, os discos virtuais serão desanexados durante o processo de clustering. Como resultado, eles também não aparecerão no Gerenciador ou Explorador de Discos até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell.
   > 
   > 
9. Depois que os nós forem adicionados ao cluster, clique em **Concluir**. O Gerenciador de Cluster de Failover agora deve mostrar que o cluster tem três nós e listá-los no contêiner **Nós** .
10. Faça logoff da sessão de área de trabalho remota.

## <a name="configure-availability-groups"></a>Configurar grupos de disponibilidade
Nesta seção, você fará o seguinte no **sqlserver-0** e **sqlserver-1**:

* Adicionar **CORP\Install** como uma função de sysadmin à instância padrão do SQL Server
* Abra o firewall para acesso remoto ao SQL Server para o ponto de extremidade de espelhamento de banco de dados, o SQL Server e a porta de investigação do Azure Load Balancer 
* Habilitar o recurso dos grupos de disponibilidade
* Alterar a conta de serviço do SQL Server para **CORP\SQLSvc1** e **CORP\SQLSvc2**, respectivamente

Essas ações podem ser executadas em qualquer ordem. No entanto, as etapas a seguir as percorrerá em ordem. Siga as etapas para **sqlserver-0** e **sqlserver-1**:

### <a name="add-installation-account-as-sysadmin-fixed-server-role-on-each-sql-server"></a>Adicione a conta de instalação como função de servidor fixa do sysadmin em cada SQL Server
1. Se você não tiver feito logoff da sessão de área de trabalho remota para a VM, faça isso agora.
2. Inicie os arquivos RDP para **sqlserver-0** e **sqlserver-1** e faça logon como **BUILTIN\DomainAdmin**.
3. Inicie o **SQL Server Management Studio**, adicione **CORP\Install** como uma função **sysadmin** na instância padrão do SQL Server. Em **Pesquisador de Objetos**, clique com o botão direito do mouse em **Logons** e clique em **Novo Logon**.
4. Digite **CORP\Install** em **Nome de logon**.
5. Na página **Funções de Servidor**, selecione **sysadmin**. Em seguida, clique em **OK**. Depois que o logon for criado, você pode vê-lo expandindo **Logons** in **Pesquisador de Objetos**.

### <a name="open-the-firewall-for-remote-access-to-sql-server-and-the-probe-port-on-each-sql-server"></a>Abra o firewall para ter acesso remoto ao SQL Server e à porta de investigação em cada SQL Server
Essa solução exige duas regras de firewall em cada SQL Server. A primeira regra fornece acesso de entrada para o SQL Server, a segunda fornece acesso de entrada para o balanceador de carga e para o ouvinte. 

1. Em seguida, crie uma regra de firewall para o SQL Server. Na tela **Iniciar**, inicie o **Firewall do Windows com Segurança Avançada**.
2. No painel esquerdo, selecione **Regras de Entrada**. No painel direito, clique em **Nova Regra**.
3. Na página **Tipo de Regra**, selecione **Programa**, e clique em **Avançar**.
4. Na página **Programa** selecione **Este caminho de programa** e digite **%ProgramFiles%\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\Binn\sqlservr.exe** na caixa de texto (se você estiver seguindo essas instruções, mas usando o SQL Server 2012, o diretório do SQL Server será **MSSQL11.MSSQLSERVER**). Em seguida, clique em **Próximo**.
5. Na página **Ação**, mantenha selecionado **Permitir a conexão** e clique em **Avançar**.
6. Na página **Perfil**, aceite as configurações padrão e clique em **Avançar**.
7. Na página **Nome**, especifique um nome de regra, como **SQL Server (Regra de Programa)**, na caixa de texto **Nome** e clique em **Concluir**.
8. Crie uma regra adicional de firewall de entrada para a porta de investigação. Essa é uma regra de entrada para a porta TCP 59999, para os fins deste tutorial. Nomeie a regra **Ouvinte do SQL Server**.

Conclua todas as etapas em ambos os servidores SQL.

### <a name="enable-availability-groups-feature-on-each-sql-server"></a>Habilite o recurso grupos de disponibilidade em cada SQL Server
Siga estas etapas em ambos os servidores SQL. 

1. Em seguida, habilite o recurso de **Grupos de Disponibilidade AlwaysOn** . Na tela **Iniciar**, inicie o **SQL Server Configuration Manager**.
2. Na árvore do navegador, clique em **Serviços do SQL Server**, clique com o botão direito do mouse no serviço **SQL Server (MSSQLSERVER)** e clique em **Propriedades**.
3. Clique na guia **Alta Disponibilidade AlwaysOn** e selecione **Habilitar Grupos de Disponibilidade AlwaysOn**, conforme mostrado abaixo, e clique em **Aplicar**. Clique em **OK** na caixa de diálogo pop-up e não feche a janela Propriedades por enquanto. Você reiniciará o serviço SQL Server depois de alterar a conta de serviço.
   
    ![Habilitar Grupos de Disponibilidade AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665520.gif)

### <a name="set-the-sql-server-service-account-on-each-sql-server"></a>Defina a conta de serviço do SQL Server em cada SQL Server
Siga estas etapas em ambos os servidores SQL.

1. Em seguida, você pode alterar a conta de serviço do SQL Server. Clique na guia **Logon**, digite **CORP\SQLSvc1** (para **sqlserver-0**) ou **CORP\SQLSvc2** (para **sqlserver-1**) em **Nome da Conta**, em seguida, preencha e confirme a senha e clique em **OK**.
2. Na janela pop-up, clique em **Sim** para reiniciar o serviço do SQL Server. Depois que o serviço do SQL Server for reiniciado, as alterações feitas na janela de propriedades entrarão em vigor.
3. Faça logoff das VMs.

### <a name="create-the-availability-group"></a>Criar o Grupo de Disponibilidade
Agora você está pronto para configurar um grupo de disponibilidade. Abaixo está uma descrição do que você deve fazer:

* Crie um novo banco de dados (**MyDB1**) no **sqlserver-0**
* Faça um backup completo e um backup de log de transações do banco de dados
* Restaure os backups completos e de log para o **sqlserver-1** com a opção **NORECOVERY**
* Crie o grupo de disponibilidade (**AG1**) com confirmação síncrona, failover automático e réplicas secundárias legíveis

### <a name="create-the-mydb1-database-on-sqlserver-0"></a>Crie o banco de dados MyDB1 no sqlserver-0:
1. Se você ainda não tiver saído das sessões da área de trabalho remota para **sqlserver-0** e **sqlserver-1**, faça isso agora.
2. Inicie o arquivo RDP para o **sqlserver-0** e faça logon como **CORP\Install**.
3. No **Explorador de Arquivos**, em **C:\**, crie um diretório chamado **backup**. Você usará esse uso de diretório para fazer backup e restaurar o banco de dados.
4. Clique com o botão direito do mouse no novo diretório, aponte o mouse para **Compartilhar com**, e clique em **Pessoas específicas**, como mostrado abaixo.
   
    ![Criar uma pasta de Backup](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665521.gif)
5. Adicione **CORP\SQLSvc1** e conceda a ele a permissão de **Leitura/Gravação** e, em seguida, adicione **CORP\SQLSvc2** e conceda a ele a permissão de **Leitura/Gravação**, conforme mostrado abaixo, e clique em **Compartilhar**. Quando o processo de compartilhamento de arquivos for concluído, clique em **Concluído**.
   
    ![Conceder permissões para a pasta de Backup](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665522.gif)
6. Em seguida, crie o banco de dados. No menu **Iniciar**, abra o **SQL Server Management Studio** e clique em **Conectar** para se conectar à instância padrão do SQL Server.
7. No **Pesquisador de Objetos**, clique com o botão direito do mouse em **Bancos de Dados** e em **Novo Banco de Dados**.
8. Em **Nome do banco de dados**, digite **MyDB1** e clique em **OK**.

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-sqlserver-1"></a>Faça um backup completo do MyDB1 e restaure-o no sqlserver-1:
1. Em seguida, faça um backup completo do banco de dados. No **Pesquisador de Objetos**c, expanda **Bancos de Dados** e clique com o botão direito do mouse em **MyDB1**; em seguida aponte o mouse para **Tarefas** e clique em **Fazer Backup**.
2. Na seção **Origem**, mantenha o **Tipo de backup** definido como **Completo**. Na seção **Destino**, clique em **Remover** para remover o caminho de arquivo padrão para o arquivo de backup.
3. Na seção **Destino**, clique em **Adicionar**.
4. Na caixa de texto **Nome do arquivo**, digite **\\\\sqlserver-0\backup\MyDB1.bak**. Em seguida, clique em **OK** e em **OK** novamente para fazer backup do banco de dados. Quando a operação de backup for concluída, clique em **OK** novamente para fechar a caixa de diálogo.
5. Em seguida, faça um backup do log de transações do banco de dados. No **Pesquisador de Objetos**c, expanda **Bancos de Dados** e clique com o botão direito do mouse em **MyDB1**; em seguida aponte o mouse para **Tarefas** e clique em **Fazer Backup**.
6. No tipo **Backup**, selecione **Log de Transações**. Mantenha o caminho do arquivo de **Destino** definido para aquele especificado anteriormente e clique em **OK**. Quando a operação de backup for concluída, clique em **OK** novamente.
7. Em seguida, restaure os backups completo e de log de transações no **sqlserver-1**. Inicie o arquivo RDP para o **sqlserver-1** e faça logon como **CORP\Install**. Deixe a sessão de área de trabalho remota para abrir o **sqlserver-0** .
8. No menu **Iniciar**, abra o **SQL Server Management Studio** e clique em **Conectar** para se conectar à instância padrão do SQL Server.
9. No **Pesquisador de Objetos**, clique com o botão direito do mouse em **Bancos de Dados** e em **Restaurar Banco de Dados**.
10. Na seção **Origem**, selecione **Dispositivo** e clique no botão **…** .
11. Em **Selecionar dispositivos de backup**, clique em **Adicionar**.
12. No local do arquivo de Backup, digite **\\\\sqlserver-0\backup**, clique em Atualizar, selecione MyDB1.bak, clique em OK e, em seguida, clique em OK novamente. Agora você deverá ver o backup completo e o backup de log no painel Conjuntos de backup a serem restaurados.
13. Vá para a página de Opções, selecione RESTORE WITH NORECOVERY em Estado de recuperação e, em seguida, clique em OK para restaurar o banco de dados. Quando a operação de restauração for concluída, clique em OK.

### <a name="create-the-availability-group"></a>Crie o Grupo de Disponibilidade:
1. Volte para a sessão de área de trabalho remota para o **sqlserver-0**. No **Pesquisador de Objetos** no SSMS, clique com o botão direito do mouse em **Alta Disponibilidade AlwaysOn** e clique em **Assistente de Novo Grupo de Disponibilidade**, conforme mostrado abaixo.
   
    ![Inicie o Assistente de Novo Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665523.gif)
2. Na página **Introdução**, clique em **Avançar**. Na página **Especificar Nome do Grupo de Disponibilidade**, digite **AG1** em **Nome do grupo de disponibilidade** e clique em **Avançar** novamente.
   
    ![Novo assistente de AG, especifique o nome do AG](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665524.gif)
3. Na página **Selecionar Bancos de Dados**, selecione **MyDB1** e clique em **Avançar**. Esse banco de dados atende aos pré-requisitos para um grupo de disponibilidade, pois você fez pelo menos um backup completo na réplica primária pretendida.
   
    ![Novo assistente AG, selecione os bancos de dados](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665525.gif)
4. Na página **Especificar Réplicas**, clique em **Adicionar Réplica**.
   
    ![Novo assistente AG, especifique as réplicas](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665526.png)
5. A caixa de diálogo **Conectar ao Servidor** é aberta. Digite **sqlserver-1** em **Nome do servidor** e clique em **Conectar**.
   
    ![Novo assistente AG, conecte ao servidor](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665527.png)
6. De volta à página **Especificar Réplicas**, você verá agora o **sqlserver-1** relacionado em **Réplicas de Disponibilidade**. Configure as réplicas conforme mostrado abaixo. 
   
    ![Novo assistente de AG, Especificar Réplicas (Completo)](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665528.png)
7. Clique em **Pontos de extremidade** para ver o ponto de extremidade de espelhamento de banco de dados que será usado por esse grupo de disponibilidade. Cada instância do SQL Server deve ter um ponto de extremidade de espelhamento de banco de dados. Memorize a porta TCP que o assistente especifica para esse ponto de extremidade. Crie uma regra de firewall de entrada em cada servidor para essa porta TCP.
   
    Quando tiver terminado, clique em **Avançar**.
8. Na página **Selecionar Sincronização de Dados Inicial**, selecione **Somente ingressar** e clique em **Avançar**. Você já executou a sincronização de dados manualmente quando obteve os backups completos e de transações no **sqlserver-0** e os restaurou no **sqlserver-1**. Você pode preferir não realizar as operações de backup e restauração no seu banco de dados e selecionar **Completo** para permitir que o Assistente de Novo Grupo de Disponibilidade execute a sincronização de dados para você. No entanto, isso não é recomendado para grandes bancos de dados que se encontram em algumas empresas.
   
    ![Novo assistente de AG, selecionar sincronização de dados inicial](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665529.png)
9. Na página **Validação**, clique em **Avançar**. Esta página deve ser semelhante ao que é mostrado abaixo. Há um aviso para a configuração de ouvinte porque você não configurou um ouvinte de grupo de disponibilidade. Você pode ignorar esse aviso, pois este tutorial não configura um ouvinte. Este tutorial fará com que você crie o ouvinte posteriormente.
   
    ![Novo assistente AG, Validação](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665530.gif)
10. Na página **Resumo**, clique em **Concluir** e aguarde enquanto o assistente configura o novo grupo de disponibilidade. Na página **Progresso**, você pode clicar em **Mais detalhes** para exibir o progresso detalhado. Quando o assistente for concluído, inspecione a página **Resultados** para verificar se o grupo de disponibilidade foi criado com êxito, conforme mostrado abaixo, e clique em **Fechar** para sair do assistente.
    
     ![Novo assistente de AG, Resultados](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665531.png)
11. No **Pesquisador de Objetos**, expanda **Alta Disponibilidade AlwaysOn** e expanda **Grupos de Disponibilidade**. Agora você poderá ver o novo grupo de disponibilidade neste contêiner. Clique com o botão direito do mouse em **AG1 (Primário)** e clique em **Mostrar Painel**.
    
     ![Mostrar Painel de AG](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665532.png)
12. O **Painel AlwaysOn** deve ser semelhante ao mostrado abaixo. Você pode ver as réplicas, o modo de failover de cada réplica e o estado de sincronização.
    
     ![Painel de AG](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665533.png)
13. Retorne ao **Gerenciador do Servidor**, selecione **Ferramentas**, e inicie o **Gerenciador de Cluster de Failover**.
14. Expanda **Cluster1.corp.contoso.com** e depois expanda **Serviços e Aplicativos**. Selecione **Funções** e observe se a função do grupo de disponibilidade **AG1** foi criada. Observe que o AG1 não tem nenhum endereço IP pelo qual os clientes do banco de dados podem se conectar ao grupo de disponibilidade porque você não configurou um ouvinte. Você pode se conectar diretamente ao nó principal para operações de leitura/gravação e o nó secundário para consultas somente leitura.
    
    ![AG no Gerenciador de Cluster de Failover](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665534.png)
    
    > [!WARNING]
    > Não tente fazer failover do grupo de disponibilidade no Gerenciador de Cluster de Failover. Todas as operações de failover devem ser executadas no **Painel AlwaysOn** no SSMS. Para obter mais informações, consulte [Restrições do uso do Gerenciador de Cluster de Failover WSFC com Grupos de Disponibilidade](https://msdn.microsoft.com/library/ff929171.aspx).
    > 
    > 

## <a name="configure-internal-load-balancer"></a>Configurar o balanceador de carga interno
Para conectar-se ao grupo de disponibilidade diretamente, você precisa configurar um balanceador de carga. O balanceador de carga direciona o tráfego de cliente para a VM associada ao endereço IP do ouvinte e na porta de investigação. Este tutorial usa um ILB (balanceador de carga interno). O ILB permite que o tráfego de dentro da mesma rede virtual se conecte ao SQL Server. Aplicativos que precisam se conectar ao SQL Server pela Internet exigem um balanceador de carga para a Internet ou externo. Para obter mais informações, veja [Visão geral do Azure Load Balancer](../load-balancer/load-balancer-overview.md).

> [!NOTE]
> Este tutorial mostra como criar um ouvinte – com um endereço IP de ILB. Para criar um ou mais ouvintes usando um ou mais endereços IP, veja [Criar o ouvinte do grupo de disponibilidade e balanceador de carga | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

### <a name="create-the-load-balancer-in-azure"></a>Criar o balanceador de carga no Azure
1. No Portal do Azure, acesse **SQL-HA-RG** e clique em **+ Adicionar**.
2. Pesquise pelo **Balanceador de Carga**. Escolha o balanceador de carga publicado pela Microsoft e clique em **Criar**.
3. Configure os seguintes parâmetros para o balanceador de carga.

| Configuração | Campo |
| --- | --- |
| **Nome** |sqlLB |
| **Esquema** |Interna |
| **Rede virtual** |autoHAVNET |
| **Sub-rede** |subnet-2. Esse é o endereço IP que você configura para o ouvinte no recurso de cluster. |
| **Atribuição de endereço IP** |Estático |
| **Endereço IP** |Use um endereço disponível da subnet-2. |
| **Assinatura** |Use a mesma assinatura que todos os outros recursos nesta solução. |
| **Localidade** |Use o mesmo local que todos os outros recursos nesta solução. |

Clique em **Criar**.

Verifique as configurações a seguir no balanceador de carga:

| Configuração | Campo |
| --- | --- |
| **Pool de back-end** Nome |sqlLBBE |
| **Conjunto de disponibilidade SQLLBBE** |sqlAvailabilitySet |
| **Máquinas virtuais SQLLBBE** |sqlserver-0, sqlserver-1 |
| **SQLLBBE usado por** |SQLAlwaysOnEndPointListener |
| **Investigação** Nome |SQLAlwaysOnEndPointProbe |
| **Protocolo de investigação** |TCP |
| **Porta de investigação** |59999 - Observe que você pode usar qualquer porta não utilizada. |
| **Intervalo de investigação** |5 |
| **Limite de teste não íntegro** |2 |
| **Investigação usada por** |SQLAlwaysOnEndPointListener |
| **Regras de balanceamento de carga** Nome |SQLAlwaysOnEndPointListener |
| **Protocolo de regras de balanceamento de carga** |TCP |
| **Porta de regras de balanceamento de carga** |1433 * |
| **Porta de back-end das regras de balanceamento de carga** |1433 |
| **Investigação de regras de balanceamento de carga** |SQLAlwaysOnEndPointProbe |
| **Persistência de sessão das regras de balanceamento de carga** |Nenhum |
| **Tempo limite ocioso das regras de balanceamento de carga** |4 |
| **IP flutuante das regras de balanceamento de carga (retorno de servidor direto)** |Habilitado |

> * 1433 é a porta padrão do SQL Server. Use para a porta de front-end para a instância padrão. Se você precisar de vários grupos de disponibilidade, precisará criar endereços IP adicionais para cada grupo de disponibilidade. Cada grupo de disponibilidade terá sua própria porta de front-end. Veja [Criar o balanceador de carga e o ouvinte do grupo de disponibilidade | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> 
> [!NOTE]
> Você deve habilitar o retorno de servidor direto nas regras de balanceamento de carga no momento da criação.
> 
> 

Depois de configurar o balanceador de carga, configure o ouvinte no cluster de failover. 

## <a name="configure-the-listener"></a>Configurar o ouvinte
A próxima etapa é configurar um ouvinte do grupo de disponibilidade no cluster de failover. 

1. RDP para o SQL Server do ad-primary-dc para o sqlserver-0.
2. No Gerenciador de Cluster de Failover, observe o nome da rede de cluster. Para determinar o nome da rede de cluster no **Gerenciador de Cluster de Failover**, clique em **Redes** no painel esquerdo. Você usará esse nome na variável `$ClusterNetworkName` no script do PowerShell.
3. No Gerenciador de Cluster de Failover, expanda o nome do cluster e clique em **Funções**.
4. Em **Funções**, clique com o botão direito do mouse no nome do grupo de disponibilidade e, em seguida, selecione **Adicionar Recurso** > **Ponto de Acesso para Cliente**. 
5. Em **Nome**, digite **aglistener**. Clique em **Avançar** duas vezes e depois em **Concluir**. Não coloque o ouvinte ou o recurso online neste momento.
6. Clique a guia **recursos** , em seguida, expanda o ponto de acesso do cliente que você acabou de criar. Clique com o botão direito do mouse no recurso de IP e clique em Propriedades. Observe o nome do endereço IP. Use esse nome na variável `$IPResourceName` no script do PowerShell.
7. Em **Endereço IP**, clique em **Endereço IP estático** e defina o endereço IP estático para o mesmo endereço que você usou no Portal do Azure para o balanceador de carga **sqlLB**. Use esse mesmo endereço IP na variável `$ILBIP` no script do PowerShell. 
8. Desabilite o NetBIOS para este endereço e clique em **OK**. 
9. No nó de cluster que hospeda a réplica primária, abra um ISE do PowerShell ISE elevado e cole os comandos a seguir em um novo script.
   
    ```
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = "<X.X.X.X>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # In this sample we've using 59999 for the probe port. 
   
    Import-Module FailoverClusters
   
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```
10. Atualize as variáveis e execute o script do PowerShell para configurar o endereço IP e a porta para o novo ouvinte.
11. Em **Gerenciador de Cluster de Failover**, clique com o botão direito do mouse no recurso do grupo de disponibilidade e, então, clique em **Propriedades**. Na guia **Dependências** , defina o grupo de recursos como dependente do nome de rede do ouvinte. 
12. Defina a propriedade de porta do ouvinte para 1433. Para fazer isso, abra o SQL Server Management Studio, clique com o botão direito do mouse no ouvinte do grupo de disponibilidade e selecione propriedades. Definir a **Porta** para 1433. Você deve usar o mesmo número da porta que você configurou para o SQL Server. 
13. Neste ponto, você pode colocar o ouvinte online. Clique com o botão direito do mouse no nome do ouvinte e clique em **Trazer online**.

### <a name="test-the-connection-to-the-listener"></a>Testar a conexão com o ouvinte
Para testar a conexão:

1. RDP para o SQL Server que não tem a réplica.
2. Use o utilitário sqlcmd para testar a conexão. Por exemplo, o script a seguir estabelece uma conexão de sqlcmd com a réplica primária por meio do ouvinte com autenticação do Windows:
   
    ```
    sqlcmd -S "<listenerName>" -E
    ```
   
   Se o ouvinte está usando uma porta além da 1433, você precisa especificar o número da porta no teste. Por exemplo, a consulta a seguir testa a conectividade para o nome do ouvinte usando a porta 1435:
   
    ```
    sqlcmd -S "<listenerName>",1435 -E
    ```

## <a name="next-steps"></a>Próximas etapas
Para obter outras informações sobre como usar o SQL Server no Azure, veja [SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


