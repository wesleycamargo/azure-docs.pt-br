---
title: "Grupos de Disponibilidade do SQL Server ‑ Máquinas Virtuais do Azure ‑ Pré-requisitos | Microsoft Docs"
description: "Este tutorial mostra como configurar os pré-requisitos para a criação de um Grupo de Disponibilidade Always On do SQL Server nas VMs do Azure."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/10/2017
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 4326cc342088ff16a72b8c460245bda1f2cd17c9
ms.openlocfilehash: 3e0c58af3566ea443efaa012495e5b736fafb46d


---

# <a name="complete-prerequisites-for-creating-always-on-availability-groups-in-azure-virtual-machines"></a>Conclua os pré-requisitos para a criação de Grupos de Disponibilidade AlwaysOn em Máquinas Virtuais do Azure

Este tutorial mostra como concluir os pré-requisitos para criar um [Grupo de Disponibilidade Always On do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Quando os pré-requisitos forem concluídos, você terá um controlador de domínio, dois SQL Servers e um servidor testemunha em um único grupo de recursos.

**Tempo estimado**: pode levar algumas horas para concluir os pré-requisitos. Grande parte desse tempo é gasto na criação de máquinas virtuais.

O diagrama ilustra o que você cria no tutorial.

![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Leia a documentação do Grupo de Disponibilidade

O tutorial supõe que você tem uma compreensão básica dos Grupos de Disponibilidade Always On do SQL Server. Se você não estiver familiarizado com essa tecnologia, consulte [Visão geral dos Grupos de Disponibilidade AlwaysOn (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Criar uma conta do Azure
* Você precisa de uma conta do Azure. Você pode [abrir uma conta gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar os benefícios de assinante do Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

## <a name="create-resource-group"></a>Criar grupo de recursos
1. Entre no [Portal do Azure](http://portal.azure.com). 
2. Clique em **+** para criar um novo objeto no portal.

   ![Novo](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

1. Digite **grupo de recursos** na janela de pesquisa do **Marketplace**.
   
   ![Grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
1. Clique em **Grupo de recursos**.
4. Clique em **Criar**. 
5. Na folha **Grupo de recursos**, em **Nome do grupo de recursos**, digite um nome para o grupo de recursos. Por exemplo, digite **sql-ha-rg**.
6. Se você tiver várias assinaturas do Azure, verifique se a assinatura é aquela na qual você deseja criar o grupo de disponibilidade. 
7. Selecione um local. A localização é a região do Azure onde você deseja criar o grupo de disponibilidade. Neste tutorial, vamos compilar todos os recursos em uma localização do Azure. 
8. Verifique se a opção **Fixar no painel** está marcada. Essa configuração opcional coloca um atalho para o grupo de recursos no painel do Portal do Azure.

   ![Grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

9. Clique em **Criar** para criar o grupo de recursos.

O Azure cria o grupo de recursos e fixa um atalho para esse grupo de recursos no portal.

## <a name="create-network-and-subnets"></a>Criar rede e sub-redes
A próxima etapa é criar as redes e sub-redes no grupo de recursos do Azure.

A solução usa uma rede virtual com duas sub-redes. A [Visão geral da Rede Virtual](../../../virtual-network/virtual-networks-overview.md) fornece mais informações sobre as redes no Azure.

Para criar a rede virtual:

1. No Portal do Azure no seu grupo de recursos, clique em **+ Adicionar**. O Azure abre a folha **Tudo** . 
   
   ![Novo Item](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Pesquise pela **rede virtual**.
   
     ![Pesquisar pela Rede Virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Clique em **Rede virtual**.
4. Na folha **Rede virtual**, clique no modelo de implantação **Resource Manager** e depois em **Criar**.
   
   A tabela a seguir mostra as configurações para a rede virtual:
   
   | **Campo** | Valor |
   | --- | --- |
   | **Nome** |autoHAVNET |
   | **Espaço de endereço** |10.33.0.0/24 |
   | **Nome da sub-rede** |Administrador |
   | **Intervalo de endereços da sub-rede** |10.33.0.0/29 |
   | **Assinatura** |Especifique a assinatura que você pretende usar. **Assinatura** estará em branco se você tiver apenas uma assinatura. |
   | **Localidade** |Especifique a localização do Azure. |
   
   Observe que o espaço de endereço e o intervalo de endereços da sub-rede podem ser diferentes daqueles na tabela. Dependendo da sua assinatura, o portal sugere um espaço de endereço disponível e o intervalo de endereços de sub-rede correspondente. Se não houver espaço de endereço disponível suficiente, use uma assinatura diferente. 

   O exemplo usa o nome de sub-rede **Admin**. Esta sub-rede destina-se aos controladores de domínio. 

6. Clique em **Criar**.
   
   ![Configurar Rede Virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

O Azure faz com que você retorne ao painel do portal e lhe notifica quando a nova rede é criada.

### <a name="create-a-second-subnet"></a>Criar uma segunda sub-rede
A nova rede virtual tem uma sub-rede, chamada **Admin**. Os controladores de domínio usam essa sub-rede. Os SQL Servers usam uma segunda sub-rede chamada **SQL**. Para configurar essa sub-rede:

1. No seu painel, clique no grupo de recursos que você criou, **SQL-HA-RG**. Localize a rede no grupo de recursos em **Recursos**.
   
    Se **SQL-HA-RG** não estiver visível, encontre-o clicando em **Grupos de Recursos** e filtrando pelo nome do grupo de recursos.
2. Clique em **autoHAVNET** na lista de recursos. O Azure abre a folha de configuração de rede.
3. Na rede virtual **autoHAVNET**, clique em**Todas as configurações.**
4. Na folha **Configurações**, clique em **Sub-redes**.
   
    Observe a sub-rede que você já criou. 
   
   ![Configurar Rede Virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Crie uma segunda sub-rede. Clique em **+ Subnet (+ Sub-rede)**. 
6. Na folha **Adicionar Sub-Rede**, configure a sub-rede digitando **sqlsubnet** em **Nome**. O Azure especifica automaticamente um **Intervalo de endereços**válido. Verifique se este intervalo de endereços tem pelo menos 10 endereços nele. Em um ambiente de produção, você poderá exigir mais endereços. 
7. Clique em **OK**.
   
    ![Configurar Rede Virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

A tabela a seguir resume as definições de configuração de rede:

| **Campo** | Valor |
| --- | --- |
| **Nome** |**autoHAVNET** |
| **Espaço de endereço** |Depende dos espaços de endereço disponíveis em sua assinatura. Um valor típico é 10.0.0.0/16 |
| **Nome da sub-rede** |**admin** |
| **Intervalo de endereços da sub-rede** |Depende dos intervalos de endereço disponíveis em sua assinatura. Um valor típico é 10.0.0.0/24. |
| **Nome da sub-rede** |**sqlsubnet** |
| **Intervalo de endereços da sub-rede** |Depende dos intervalos de endereço disponíveis em sua assinatura. Um valor típico é 10.0.1.0/24. |
| **Assinatura** |Especifique a assinatura que você pretende usar. |
| **Grupo de recursos** |**SQL-HA-RG** |
| **Localidade** |Especifique o mesmo local que você escolheu para o grupo de recursos. |

## <a name="create-availability-sets"></a>Criar conjuntos de disponibilidade

Antes de criar máquinas virtuais, você precisa criar conjuntos de disponibilidade. Os conjuntos de disponibilidade reduzem o tempo de inatividade para eventos de manutenção planejados ou não. Um conjunto de disponibilidade do Azure é um grupo lógico de recursos que o Azure coloca em domínios de falha física e em domínios de atualização. Um domínio de falha garante que os membros do conjunto de disponibilidade terão recursos de energia e rede separados. Um domínio de atualização garante que os membros do conjunto de disponibilidade não serão desativados para manutenção ao mesmo tempo. [Gerenciar a disponibilidade de máquinas virtuais](../../virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Você precisará de dois conjuntos de disponibilidade. Um é para os controladores de domínio. A segunda é para os SQL Servers.

Para criar um conjunto de disponibilidade, acesse o grupo de recursos e clique em **Adicionar**. Filtre os resultados digitando **Conjunto de disponibilidade**. Clique em **Conjunto de Disponibilidade** nos resultados. Clique em **Criar**.

Configure dois conjuntos de disponibilidade de acordo com os parâmetros na tabela a seguir:

| **Campo** | Conjunto de Disponibilidade do Controlador de Domínio | Conjunto de Disponibilidade do SQL Server |
| --- | --- | --- |
| **Nome** |adavailabilityset |sqlavailabilityset |
| **Grupo de recursos** |SQL-HA-RG |SQL-HA-RG |
| **Domínios de falha** |3 |3 |
| **Domínios de atualização** |5 |3 |

Depois de criar os conjuntos de disponibilidade, retorne ao grupo de recursos no Portal do Azure.

## <a name="create-domain-controllers"></a>Criar controladores de domínio
Após você criar a rede, as sub-redes, os conjuntos de disponibilidade e um balanceador de carga para a Internet. Você está pronto para criar as máquinas virtuais para os controladores de domínio.

### <a name="create-the-virtual-machines-for-the-domain-controllers"></a>Criar as máquinas virtuais para os controladores de domínio
Para criar e configurar os controladores de domínio, retorne para o grupo de recursos **SQL-HA-RG** .

1. Clique em Adicionar. A folha **Tudo** é aberta.
2. Digite **Windows Server 2016 Datacenter**. 
3. Clique em **Windows Server 2016 Datacenter**. Na folha **Windows Server 2016 Datacenter**, verifique se o modelo de implantação está definido como **Resource Manager** e clique em **Criar**. O Azure abre a folha **Criar máquina virtual** . 

Repita as etapas anteriores para criar duas máquinas virtuais. Nomeie as duas máquinas virtuais:

* ad-primary-dc
* ad-secondary-dc
  
  > [!NOTE]
  > **ad-secondary-dc** é um componente opcional para fornecer alta disponibilidade para os Serviços de Domínio do Active Directory. 
  > 
  > 

A tabela a seguir mostra as configurações para esses dois computadores:

| **Campo** | Valor |
| --- | --- |
| **Tipo de disco da VM** |SSD |
| **Nome de usuário** |DomainAdmin |
| **Senha** |Contoso!0000 |
| **Assinatura** |*sua assinatura* |
| **Grupo de recursos** |SQL-HA-RG |
| **Localidade** |*seu local* |
| **Tamanho** |DS1_V2 |
| **Conta de armazenamento** |*Criada automaticamente* |
| **Rede virtual** |autoHAVNET |
| **Sub-rede** |admin |
| **Endereço IP público** |*Mesmo nome que a VM* |
| **Grupo de Segurança de Rede** |*Mesmo nome que a VM* |
| **Conjunto de disponibilidade** |adavailabilityset |
| **Diagnostics** |Habilitado |
| **Conta de armazenamento de diagnóstico** |*Criada automaticamente* |


   >[!IMPORTANT]
   >Você só pode colocar uma VM em um conjunto de disponibilidade ao criá-la. Não é possível alterar o conjunto de disponibilidade após a criação da VM. Consulte [Gerenciar a disponibilidade das máquinas virtuais](../../virtual-machines-windows-manage-availability.md).

O Azure cria as máquinas virtuais.

Depois que as máquinas virtuais forem criadas, configure o controlador de domínio.

### <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio
Nas etapas a seguir, configure o computador **ad-primary-dc** como um controlador de domínio para corp.contoso.com.

1. No portal, abra o grupo de recursos **SQL-HA-RG** e selecione o computador **ad-primary-dc**. Na folha **ad-primary-dc**, clique em **Conectar** para abrir um arquivo RDP para acesso à área de trabalho remota.
   
    ![Conectar à Máquina Virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Faça logon com sua conta de administrador (**\DomainAdmin**) e senha (**Contoso!0000**) configuradas.
3. Por padrão, o painel **Gerenciador do Servidor** deve ser exibido.
4. Clique no link **Adicionar funções e recursos** no painel.
   
    ![Adicionar Funções ao Gerenciador de Servidores](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Selecione **Avançar** até alcançar a seção **Funções de Servidor**.
6. Selecione **Active Directory Domain Services** e as funções do **Servidor DNS**. Quando solicitado, acrescente os recursos adicionais necessários para essas funções.
   
   > [!NOTE]
   > Windows avisa que não há nenhum endereço IP estático. Se você estiver testando a configuração, clique em continuar. Nos cenários de produção, defina o endereço IP como estático no Portal do Azure ou [use o PowerShell para definir o endereço IP estático do computador do controlador de domínio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   > 
   > 
   
    ![Adicionar Caixa de Diálogo de Funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Clique em **Avançar** até alcançar a seção **Confirmação**. Marque a caixa de seleção **Reiniciar cada servidor de destino automaticamente, se necessário** .
8. Clique em **Instalar**.
9. Depois que os recursos forem instalados, retorne para o painel **Gerenciador do Servidor** .
10. Selecione a nova opção **AD DS** no painel esquerdo.
11. Clique no link **Mais** na barra de aviso amarela.
    
    ![Caixa de diálogo do AD DS na VM do servidor DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. Na coluna **Ação** da caixa de diálogo **Todos os Detalhes de Tarefa do Servidor**, clique em **Promover este servidor a um controlador de domínio**.
13. No **Assistente de Configuração dos Serviços de Domínio do Active Directory**, use os seguintes valores:
    
    | **Página** | Configuração |
    | --- | --- |
    | **Configuração de Implantação** |**Adicionar uma nova floresta**<br/> **Nome do domínio raiz** = corp.contoso.com |
    | **Opções de Controlador de Domínio** |**Senha DSRM** = Contoso!0000<br/>**Confirmar Senha** = Contoso!0000 |
14. Clique em **Avançar** para percorrer as outras páginas do assistente. Na página **Verificação de pré-requisitos**, verifique se a seguinte mensagem é exibida: **Todas as verificações de pré-requisitos passaram com êxito**. Examine todas as mensagens de aviso aplicáveis, porém é possível continuar com a instalação.
15. Clique em **Instalar**. A máquina virtual **ad-primary-dc** é reinicializada automaticamente.

### <a name="note-ip-address-of-primary-domain-controller"></a>Anote o endereço IP do controlador de domínio primário

Use o controlador de domínio primário para DNS. Anote endereço IP do controlador de domínio primário.

Uma maneira de obter o endereço IP do controlador de domínio primário é por meio do Portal do Azure. 

1. No Portal do Azure, abra o grupo de recursos. 

1. Clique no controlador de domínio primário.

1. Na folha do controlador de domínio primário, clique em **Interfaces de rede**.

![Novo Item](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Anote o endereço IP privado para este servidor. 

### <a name="configure-the-second-domain-controller"></a>Configurar o segundo controlador de domínio
Após a reinicialização do controlador de domínio primário, você pode configurar o segundo controlador de domínio. Esta etapa opcional é para alta disponibilidade. Siga estas etapas para configurar o segundo controlador de domínio:

1. No portal, abra o grupo de recursos **SQL-HA-RG** e selecione o computador **ad-secondary-dc**. Na folha **ad-secondary-dc**, clique em **Conectar** para abrir um arquivo RDP para acesso à Área de Trabalho Remota.
4. Faça logon na VM usando sua conta de administrador (**BUILTIN\DomainAdmin**) e senha (**Contoso!0000**) configuradas.
3. Altere o endereço do servidor DNS preferencial para o endereço do controlador de domínio. 
1. Em **Central de Rede e Compartilhamento**, clique no adaptador de rede. 
   ![NetworkInterface](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Clique em **Propriedades**.
10. Clique em **Protocolo IP versão 4 (TCP/IPv4)** e clique em Propriedades.
11. Selecione **Usar os seguintes endereços de servidor DNS** e especifique o endereço do controlador de domínio primário no **Servidor DNS preferencial**.
1. Clique em **OK** e em **Fechar** para confirmar as alterações. Agora é possível ingressar a VM em **corp.contoso.com**.

   >[!IMPORTANT]
   >Se você perder a conexão com a Área de Trabalho Remota após alterar a configuração do DNS, acesse o Portal do Azure e reinicie a máquina virtual.

1. Na Área de Trabalho Remota para o controlador de domínio secundário, abra **Painel do Gerenciador do Servidor**.
4. Clique no link **Adicionar funções e recursos** no painel.
   
    ![Adicionar Funções ao Gerenciador de Servidores](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Selecione **Avançar** até alcançar a seção **Funções de Servidor**.
6. Selecione **Active Directory Domain Services** e as funções do **Servidor DNS**. Quando solicitado, acrescente os recursos adicionais necessários para essas funções.

9. Depois que os recursos forem instalados, retorne para o painel **Gerenciador do Servidor** .
10. Selecione a nova opção **AD DS** no painel esquerdo.
11. Clique no link **Mais** na barra de aviso amarela.
12. Na coluna **Ação** da caixa de diálogo **Todos os Detalhes de Tarefa do Servidor**, clique em **Promover este servidor a um controlador de domínio**.
1. Em **Configuração de Implantação**, selecione **Adicionar um controlador de domínio a um domínio existente**. 
   ![NetworkInterface](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)

1. Clique em **Selecionar...**.
1. Conecte-se usando a conta de administrador (**CORP.CONTOSO.COM\domainadmin**) e senha (**Contoso!0000**).
1. Em **Selecione um domínio na floresta**, clique em seu domínio e em **OK**. 

1. Em **Opções do Controlador de Domínio**, use os valores padrão e defina uma senha de DSRM.

   >[!NOTE]
   >A página **Opções de DNS** poderá avisar que não foi possível criar uma delegação para este servidor DNS. Você pode ignorar esse aviso em ambientes que não são de produção. 
1. Clique em **Avançar** até atingir a caixa de diálogo de **Pré-requisitos**. Em seguida, clique em **Instalar**.

Depois que o servidor concluir as alterações de configuração, reinicie-o. 

### <a name="a-namedomainaccountsa-configure-domain-accounts"></a><a name=DomainAccounts></a> Configurar Contas de Domínio

As próximas etapas configuram as contas do AD (Active Directory). A tabela abaixo mostra as contas:

| |Conta de Instalação<br/> |sqlserver-0 <br/>Conta do SQL Server e do serviço SQL Agent |sqlserver-1<br/>Conta do SQL Server e do serviço SQL Agent
| --- | --- | --- | --- 
|**Nome** |Instalar |SQLSvc1 | SQLSvc2
|**SamAccountName do usuário** |Instalar |SQLSvc1 | SQLSvc2

Use as etapas a seguir para criar cada conta. 

1. Faça logon novamente no computador **ad-primary-dc** .
2. No **Gerenciador do Servidor**, selecione **Ferramentas** e clique em **Centro Administrativo do Active Directory**.   
3. Selecione **corp (local)** no painel à esquerda.
4. No painel **Tarefas** à direita, selecione **Novo** e clique em **Usuário**. 
   ![Centro Administrativo do Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Defina uma senha complexa para cada conta.<br/> Para ambientes de não produção, defina a conta de usuário para nunca expirar.

5. Clique em **OK** para criar o usuário. 
6. Repita as etapas anteriores para cada uma das três contas. 

### <a name="grant-required-permissions-to-the-installation-account"></a>Conceda as permissões necessárias para a conta de instalação
1. No **Centro Administrativo do Active Directory**, selecione **corp (local)** no painel esquerdo. Em seguida, no painel **Tarefas** à direita, clique em **Propriedades**.
   
    ![Propriedades do usuário CORP](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
8. Selecione **Extensões** e clique no botão **Avançado** na guia **Segurança**.
9. Na caixa de diálogo **Configurações de Segurança Avançadas para corp** . Clique em **Adicionar**.
10. Clique em **Selecione uma entidade**. Em seguida, procure **CORP\Install**. Clique em **OK**.
11. Marque **Ler todas as propriedades**.

1. Marque **Criar objetos de Computador**.
    
     ![Permissões de usuário corp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
12. Clique em **OK** e em **OK** novamente. Feche a janela de propriedades corporativas.

Agora que você concluiu a configuração do Active Directory e dos objetos de usuário, crie duas VMs do SQL Server, uma VM de servidor testemunha. Em seguida, faça com que todas as três ingressem no domínio.

## <a name="create-sql-servers"></a>Criar Servidores SQL
### <a name="create-and-configure-the-sql-server-vms"></a>Criar e configurar as VMs do SQL Server
Em seguida, crie três VMs, incluindo duas VMs do SQL Server e um nó de cluster do WSFC. Para criar cada uma das VMs, volte para o grupo de recursos **SQL-HA-RG**, clique em **Adicionar**, pesquise pelo item da galeria apropriado, **Máquina Virtual** e, em seguida, **Da Galeria**. Use as informações na tabela a seguir para ajudar a criar as VMs:

| Página | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selecione o item da galeria apropriado |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise no Windows Server 2016** |**SQL Server 2016 SP1 Enterprise no Windows Server 2016** |
| **Noções básicas** |**Nome** = cluster-fsw<br/>**Nome de usuário** = DomainAdmin<br/>**Senha** = Contoso!0000<br/>**Assinatura** = Sua assinatura<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Local** = Seu local do Azure |**Nome** = sqlserver-0<br/>**Nome de usuário** = DomainAdmin<br/>**Senha** = Contoso!0000<br/>**Assinatura** = Sua assinatura<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Local** = Seu local do Azure |**Nome** = sqlserver-1<br/>**Nome de usuário** = DomainAdmin<br/>**Senha** = Contoso!0000<br/>**Assinatura** = Sua assinatura<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Local** = Seu local do Azure |
| **Tamanho** |DS1\_V2 (1 núcleo, 3,5 GB) |**SIZE** = DS2\_V2 (2 núcleos, 7 GB) |**SIZE** = DS2\_V2 (2 núcleos, 7 GB) |
| **Configurações** |**Armazenamento** = Premium (SSD)<br/>**SUB-REDES** = autoHAVNET<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**Sub-rede** = sqlsubnet(10.1.1.0/24)<br/>**Endereço IP público** = Nenhum<br/>**Grupo de segurança de rede** = Nenhum<br/>**Monitorando Diagnóstico** = Habilitado<br/>**Conta de armazenamento de diagnóstico** = Use uma conta de armazenamento gerada automaticamente<br/>**CONJUNTO DE DISPONIBILIDADE** = sqlAvailabilitySet<br/> |**Armazenamento** = Premium (SSD)<br/>**SUB-REDES** = autoHAVNET<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**Sub-rede** = sqlsubnet(10.1.1.0/24)<br/>**Endereço IP público** = Nenhum<br/>**Grupo de segurança de rede** = Nenhum<br/>**Monitorando Diagnóstico** = Habilitado<br/>**Conta de armazenamento de diagnóstico** = Use uma conta de armazenamento gerada automaticamente<br/>**CONJUNTO DE DISPONIBILIDADE** = sqlAvailabilitySet<br/> |**Armazenamento** = Premium (SSD)<br/>**SUB-REDES** = autoHAVNET<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**Sub-rede** = sqlsubnet(10.1.1.0/24)<br/>**Endereço IP público** = Nenhum<br/>**Grupo de segurança de rede** = Nenhum<br/>**Monitorando Diagnóstico** = Habilitado<br/>**Conta de armazenamento de diagnóstico** = Use uma conta de armazenamento gerada automaticamente<br/>**CONJUNTO DE DISPONIBILIDADE** = sqlAvailabilitySet<br/> |
| **Definições do SQL Server** |Não aplicável |**Conectividade SQL** = Particular (em rede virtual)<br/>**Porta** = 1433<br/>**Autenticação SQL** = Desabilitar<br/>**Configuração de armazenamento** = Geral<br/>**Aplicação de patch automatizada** = Domingo às 2:00<br/>**Backup automatizado** = Desabilitado</br>**Integração do Cofre de Chaves do Azure** = Desabilitado |**Conectividade SQL** = Particular (em rede virtual)<br/>**Porta** = 1433<br/>**Autenticação SQL** = Desabilitar<br/>**Configuração de armazenamento** = Geral<br/>**Aplicação de patch automatizada** = Domingo às 2:00<br/>**Backup automatizado** = Desabilitado</br>**Integração do Cofre de Chaves do Azure** = Desabilitado |

<br/>

> [!NOTE]
> Os tamanhos de computador sugeridos aqui servem para testar grupos de disponibilidade em VMs do Azure. Para obter o melhor desempenho em cargas de trabalho de produção, consulte as recomendações de configuração e tamanhos de máquina do SQL Server em [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> 
> 

Depois que as três VMs forem totalmente provisionadas, você precisará ingressá-las no domínio **corp.contoso.com** e conceder direitos administrativos CORP\Install às máquinas.

### <a name="set-dns-on-each-server"></a>Definir DNS em cada servidor
Primeiro, altere o endereço do servidor DNS preferencial para cada servidor membro. Siga estas etapas:

1. No portal, abra o grupo de recursos **SQL-HA-RG** e selecione o computador **sqlserver-0**. Na folha **sqlserver-0**, clique em **Conectar** para abrir um arquivo RDP para acesso à Área de Trabalho Remota.
2. Faça logon com sua conta de administrador (**\DomainAdmin**) e senha (**Contoso!0000**) configuradas.
3. Por padrão, o painel **Gerenciador do Servidor** deve ser exibido. No painel esquerdo, clique em **Servidor Local** .
5. Selecione o link **Endereço IPv4 atribuído pelo DHCP, habilitado para IPv6** .
6. Na janela **Conexões de Rede** , selecione o ícone de rede.
7. Na barra de comandos, clique em **Alterar as configurações dessa conexão**. Se você não vir essa opção, clique na seta dupla direita.
8. Clique em **Protocolo IP versão 4 (TCP/IPv4)** e clique em Propriedades.
9. Selecione **Usar os seguintes endereços de servidor DNS** e especifique o endereço do controlador de domínio primário no **Servidor DNS preferencial**.
   >[!TIP]
   >Para obter o endereço IP do servidor, use `nslookup`.<br/>
   >No prompt de comando, digite `nslookup ad-primary-dc`. 
11. Clique em **OK** e em **Fechar** para confirmar as alterações. 

   >[!IMPORTANT]
   >Se você perder a conexão com a Área de Trabalho Remota após alterar a configuração do DNS, acesse o Portal do Azure e reinicie a máquina virtual.

Repita essas etapas para todos os servidores.

### <a name="a-namejoindomainajoin-the-servers-to-the-domain"></a><a name="joinDomain"></a>Ingressar os servidores no domínio.

Agora é possível ingressar a VM em **corp.contoso.com**. Execute o seguinte para os SQL Servers e para o servidor testemunha do compartilhamento de arquivos: 

1. Conecte-se remotamente à máquina virtual com **BUILTIN\DomainAdmin**. 
1. Em **Gerenciador do Servidor**, clique em **Servidor Local**
1. Clique no link **WORKGROUP**.
1. Na seção **Nome do Computador**, clique em **Alterar**.
1. Marque a caixa de seleção **Domínio** e digite **corp.contoso.com** na caixa de texto. Clique em **OK**.
1. Na caixa de diálogo pop-up **Segurança do Windows**, especifique as credenciais para a conta de administrador de domínio padrão (**CORP\DomainAdmin**) e a senha (**Contoso!0000**).
1. Quando a mensagem "Bem-vindo ao domínio corp.contoso.com" for exibida, clique em **OK**.
1. Clique em **Fechar** e em **Reiniciar Agora** na caixa de diálogo pop-up.



### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Adicione o usuário Corp\Install como um administrador em cada VM de cluster:

Depois de reiniciar cada máquina virtual como um membro do domínio, adicione **CORP\Install** como um membro do grupo administradores local. 

1. Aguarde até que a VM seja reiniciada e inicialize o arquivo RDP novamente do controlador de domínio primário para fazer logon no **sqlserver-0** usando a conta **CORP\DomainAdmin**.
   >[!TIP]
   >Verifique se você fez logon com a conta de administrador de domínio. Nas etapas anteriores, você estava usando a conta de administrador BUILT IN. Agora que o servidor está no domínio, use a conta de domínio. Na sua sessão de RDP, especifique *DOMÍNIO*\\*nome de usuário*.

2. Em **Gerenciador do Servidor**, selecione **Ferramentas** e clique em **Gerenciamento do Computador**.
3. Na janela **Gerenciamento do Computador**, expanda **Usuários e Grupos Locais** e selecione **Grupos**.
4. Clique duas vezes no grupo **Administradores** .
5. Na caixa de diálogo **Propriedades de Administradores**, clique no botão **Adicionar**.
6. Insira o usuário **CORP\Install** e clique em **OK**. 
7. Clique em **OK** para fechar a caixa do diálogo **Propriedades do Administrador**.
8. Repita as etapas acima em **sqlserver-1**, e **cluster-fsw**.

### <a name="a-namesetserviceaccountaset-the-sql-server-service-accounts"></a><a name="setServiceAccount"></a>Defina as contas de serviço do SQL Server

Em cada SQL Server, defina a conta de serviço do SQL Server. Use as contas que você criou quando [configurou as contas de domínio](#DomainAccounts).

1. Abra o **SQL Server Configuration Manager**.

1. Clique com o botão direito do mouse no serviço do SQL Server e em **Propriedades**.

1. Defina a conta e a senha. 

1. Repita essas etapas no outro SQL Server.  

Para Grupos de Disponibilidade do SQL Server, cada SQL Server precisa ser executado como uma conta de domínio. 

### <a name="create-login-on-each-sql-server-for-installation-account"></a>Criar logon em cada SQL Server para a conta de instalação

Use a conta de instalação para configurar o grupo de disponibilidade. Essa conta precisa ser membro da função de servidor fixa **sysadmin** de cada SQL Server. As seguintes etapas criam um logon para a conta de instalação:

1. RDP no servidor usando a conta *\<MachineName\>\DomainAdmin*.

1. Abra o SQL Server Management Studio e conecte-se ao SQL Server da instância local. 

1. Em **Pesquisador de Objetos**, clique em **Segurança**.

1. Clique com botão direito do mouse em **Logons**. Clique em **Novo Logon...**.

1. Em **Logon – Novo**, clique em **Pesquisar...**.

1. Clique em **Locais...**.

1. Insira as credenciais da rede do administrador de domínio. 

1. Use a conta de instalação.

1. Defina o logon como um membro da função de servidor fixa **sysadmin**. 

1. Clique em OK. 

Repita as etapas anteriores no outro SQL Server. 

## <a name="add-failover-cluster-features-to-both-sql-servers"></a>Adicione os Recursos de Cluster de Failover em ambos os SQL Servers

Para adicionar os recursos de cluster de failover, execute as etapas a seguir em ambos os SQL Servers:

1. Na Área de Trabalho Remota para o controlador de domínio secundário, abra **Painel do Gerenciador do Servidor**.
4. Clique no link **Adicionar funções e recursos** no painel.
   
    ![Adicionar Funções ao Gerenciador de Servidores](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Selecione **Avançar** até alcançar a seção **Recursos do Servidor**.
1. Em **Recursos**, selecione **Clustering de Failover**. 
1. Adicione os recursos adicionais necessários. 
1. Clique em instalar para adicionar todos os recursos.

Repita essas etapas no outro SQL Server. 


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server"></a><a name="endpoint-firewall"> Configurar o firewall em cada SQL Server

A solução requer que as seguintes portas TCP estejam abertas no firewall:

- **SQL Server**<br/>
   Porta 1433 para uma instância padrão do SQL Server. 
- **Investigação do Azure Load Balancer**<br/>
   Qualquer porta disponível. Os exemplos geralmente usam 59999.
- **Ponto de extremidade de espelhamento de banco de dados** <br/>
   Qualquer porta disponível. Os exemplos geralmente usam 5022. 

As portas do firewall precisam ser abertas em ambos os SQL Servers.

A maneira de abrir as portas depende da solução de firewall usada. A próxima seção explica como abrir portas no Firewall do Windows. Abra as portas necessárias em cada SQL Server. 

### <a name="open-a-tcp-port-on-a-firewall"></a>Abrir uma porta TCP em um firewall 

1. Na tela **Iniciar** do primeiro SQL Server, inicialize **Firewall do Windows com Segurança Avançada**.

2. No painel esquerdo, selecione **Regras de Entrada**. No painel direito, clique em **Nova Regra**.

3. Para **Tipo de Regra**, escolha **Porta**.

1. Para a porta, especifique TCP e digite os números de porta apropriados. Veja os exemplos a seguir:

   ![SQLFirewall](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

1. Clique em **Próximo**. 

5. Na página **Ação**, mantenha selecionado **Permitir a conexão** e clique em **Avançar**.

6. Na página **Perfil**, aceite as configurações padrão e clique em **Avançar**.

7. Na página **Nome**, especifique um nome de regra, como **Investigação do Azure LB** na caixa de texto **Nome** e clique em **Concluir**.

Repita essas etapas no segundo SQL Server da mesma maneira.



## <a name="next-steps"></a>Próximas etapas

* [Criar um Grupo de Disponibilidade AlwaysOn do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)



<!--HONumber=Jan17_HO2-->


