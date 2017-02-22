---
title: "Criar um ouvinte do grupo de disponibilidade do SQL Server — Máquinas Virtuais do Azure | Microsoft Docs"
description: "Instruções passo a passo de como criar um ouvinte para um grupo de disponibilidade Always On para SQL Server em Máquinas Virtuais do Azure"
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/28/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 407b189af12116d633ed505facf4bcfde9be5822
ms.openlocfilehash: 6a37e9e786a4e399c49cb77758a23793790888c9


---
# <a name="configure-an-internal-load-balancer-for-an-always-on-availability-group-in-azure"></a>Configurar um balanceador de carga interno para um grupo de disponibilidade Always On no Azure
Este tópico explica como criar um balanceador de carga interno para um grupo de disponibilidade Always On do SQL Server em máquinas virtuais do Azure em execução no modelo do Resource Manager. Um grupo de disponibilidade exige um balanceador de carga quando as instâncias do SQL Server estão em máquinas virtuais do Azure. O balanceador de carga armazena o endereço IP do ouvinte do grupo de disponibilidade. Se um grupo de disponibilidade abranger várias regiões, cada região precisará de um balanceador de carga.

Para concluir essa tarefa, você precisa ter um grupo de disponibilidade do SQL Server implantado em máquinas virtuais do Azure no modelo do Resource Manager. As máquinas virtuais do SQL Server devem pertencer ao mesmo conjunto de disponibilidade. Você pode usar o [modelo da Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para criar automaticamente o grupo de disponibilidade no Azure Resource Manager. Este modelo cria automaticamente o balanceador de carga interno para você. 

Se preferir, você poderá [configurar manualmente um grupo de disponibilidade](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Este tópico exige que os grupos de disponibilidade já estejam configurados.  

Os tópicos relacionados incluem:

* [Configurar os grupos de disponibilidade Always On na VM do Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Configurar uma conexão de rede virtual com rede virtual usando o PowerShell e o Azure Resource Manager](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Ao seguir este documento, você cria e configura um balanceador de carga no Portal do Azure. Depois de concluir esta tarefa, você configurará o cluster para usar o endereço IP do balanceador de carga no ouvinte do grupo de disponibilidade.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Criar e configurar o balanceador de carga no Portal do Azure
Nesta parte da tarefa, siga estas etapas no Portal do Azure:

1. Criar o balanceador de carga e configurar o endereço IP
2. Configurar o pool de back-end
3. Criar a investigação 
4. Definir as regras de balanceamento de carga

> [!NOTE]
> Se os SQL Servers estiverem em regiões e grupos de recursos diferentes, você executará todas essas etapas duas vezes, uma vez em cada grupo de recursos.
> 
> 

### <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. Criar o balanceador de carga e configurar o endereço IP
A primeira etapa é criar o balanceador de carga. No Portal do Azure, abra o grupo de recursos que contém as máquinas virtuais do SQL Server. No grupo de recursos, clique em **Adicionar**.

1. Pesquise pelo **balanceador de carga**. Nos resultados da pesquisa, selecione **Balanceador de Carga**, que é publicado pela **Microsoft**.
1. Na folha **Balanceador de Carga**, clique em **Criar**.
1. Em **Criar balanceador de carga**, configure o balanceador de carga da seguinte maneira:

   | Configuração | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa o balanceador de carga. Por exemplo, **sqlLB**. |
   | **Tipo** |**Interna** |
   | **Rede virtual** |Selecione a rede virtual na qual os servidores SQL estão. |
   | **Sub-rede** |Selecione a sub-rede na qual os servidores SQL estão. |
   | **Atribuição de endereço IP** |**Estático** |
   | **Endereço IP privado** |Especifique um endereço IP disponível na sub-rede. Você usa esse endereço IP ao criar um ouvinte no cluster. Em um script do PowerShell posterior neste artigo, use esse endereço para a variável `$ILBIP` . |
   | **Assinatura** |Se você tiver várias assinaturas, este campo poderá aparecer. Selecione a assinatura que você deseja que seja associada a esse recurso. Normalmente, trata-se da mesma assinatura de todos os recursos do grupo de disponibilidade. |
   | **Grupo de recursos** |Selecione o grupo de recursos no qual os servidores SQL estão. |
   | **Localidade** |Selecione a localização do Azure na qual os servidores SQL estão. |

1. Clique em **Criar**. 

O Azure cria o balanceador de carga. O balanceador de carga pertence a uma rede, sub-rede, grupo de recursos e local específicos. Após a conclusão do Azure, verifique as configurações do balanceador de carga no Azure. 

### <a name="2-configure-the-backend-pool"></a>2. Configurar o pool de back-end
A próxima etapa é criar um pool de endereços de back-end. O Azure chama o pool de endereços de back-end de *pool de back-end*. Nesse caso, o pool de back-end consiste dos endereços dos dois servidores SQL em seu grupo de disponibilidade. 

1. No grupo de recursos, clique no balanceador de carga que você criou. 
1. Em **Configurações**, clique em **Pools de back-end**.
1. Em **Pools de back-end**, clique em **Adicionar** para criar um pool de endereços de back-end. 
1. Em **Adicionar pool de back-end** under **Nome**, digite um nome para o pool de back-end.
1. Em **Máquinas virtuais**, clique em **+ Adicionar uma máquina virtual**. 
1. Em **Escolher máquinas virtuais**, clique em **Escolher um conjunto de disponibilidade** e especifique o conjunto de disponibilidade ao qual as máquinas virtuais do SQL Server pertencem.
1. Depois de escolher o conjunto de disponibilidade, clique em **Escolher as máquinas virtuais**. Clique em duas máquinas virtuais que hospedam as instâncias do SQL Server no grupo de disponibilidade. Clique em **Selecionar**. 
1. Clique em **OK** para fechar as folhas para **Escolher as máquinas virtuais** e, então, em **Adicionar pool de back-end**. 

O Azure atualiza as configurações para o pool de endereços de back-end. Agora seu conjunto de disponibilidade tem um pool de dois servidores SQL.

### <a name="3-create-a-probe"></a>3. Criar uma investigação
A próxima etapa é criar uma investigação. A investigação define como o Azure verifica qual dos SQL Servers atualmente têm o ouvinte do grupo de disponibilidade. O Azure investiga o serviço com base no endereço IP em uma porta que você define quando cria o teste.

1. Na folha **Configurações** do balanceador de carga, clique em **Investigações de integridade**. 
1. Na folha **Investigações de integridade**, clique em **Adicionar**.
1. Configure a investigação na folha **Adicionar investigação** . Use os valores a seguir para configurar a investigação:

   | Configuração | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa a investigação. Por exemplo, **SQLAlwaysOnEndPointProbe**. |
   | **Protocolo** |**TCP** |
   | **Porta** |Você pode usar qualquer porta disponível. Por exemplo, *59999*. |
   | **Intervalo** |*5* |
   | **Limite não íntegro** |*2* |

1.  Clique em **OK**. 

> [!NOTE]
> Verifique se a porta especificada está aberta no firewall dos servidores SQL. Ambos os servidores exigem uma regra de entrada para a porta TCP que você usa. Para saber mais, confira [Adicionar ou editar regra de firewall](http://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

O Azure cria a investigação. O Azure usará a investigação para testar qual SQL Server tem o ouvinte do grupo de disponibilidade.

### <a name="4-set-the-load-balancing-rules"></a>4. Definir as regras de balanceamento de carga
Definir as regras de balanceamento de carga. As regras de balanceamento de carga configuram como o balanceador de carga encaminha o tráfego para os servidores SQL. Para este balanceador de carga, habilite o retorno de servidor direto, pois somente um dos dois SQL Servers tem o recurso de ouvinte do grupo de disponibilidade por vez.

1. Na folha **Configurações** do balanceador de carga, clique em **Regras de balanceamento de carga**. 
1. Na folha **Regras de balanceamento de carga**, clique em **Adicionar**.
1. Use a folha **Adicionar regras de balanceamento de carga** para configurar a regra de balanceamento de carga. Use as configurações a seguir: 

   | Configuração | Valor |
   | --- | --- |
   | **Nome** |Um nome de texto que representa as regras de balanceamento de carga. Por exemplo, **SQLAlwaysOnEndPointListener**. |
   | **Protocolo** |**TCP** |
   | **Porta** |*1433* |
   | **Porta de back-end** |*1433*. Esse valor será ignorado porque essa regra usa **IP flutuante (retorno de servidor direto)**. |
   | **Investigação** |Use o nome da investigação que você criou para este balanceador de carga. |
   | **Persistência de sessão** |**Nenhum** |
   | **Tempo limite de ociosidade (minutos)** |*4* |
   | **IP flutuante (retorno de servidor direto)** |**Habilitado** |

   > [!NOTE]
   > Talvez você precise rolar para baixo na folha para ver todas as configurações.
   > 

1. Clique em **OK**. 
1. O Azure configura a regra de balanceamento de carga. Agora o balanceador de carga está configurado para rotear o tráfego para o SQL Server que hospeda o ouvinte para o grupo de disponibilidade. 

Neste ponto, o grupo de recursos tem um balanceador de carga que se conecta em ambas as máquinas do SQL Server. O balanceador de carga também contém um endereço IP para o ouvinte do grupo de disponibilidade AlwaysOn do SQL Server para que o computador possa responder às solicitações para os grupos de disponibilidade.

> [!NOTE]
> Se seus servidores SQL estiverem em duas regiões separadas, repita as etapas na outra região. Cada região exige um balanceador de carga. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configure o cluster para usar o endereço IP do balanceador de carga
A próxima etapa é configurar o ouvinte no cluster e colocar o ouvinte online. Execute as seguintes etapas: 

1. Crie o ouvinte do grupo de disponibilidade no cluster de failover 
2. Coloque o ouvinte online

### <a name="5-create-the-availability-group-listener-on-the-failover-cluster"></a>5. Crie o ouvinte do grupo de disponibilidade no cluster de failover
Nesta etapa, você cria manualmente o ouvinte do grupo de disponibilidade no Gerenciador de Cluster de Failover e no Server Management Studio (SSMS).

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

<!---------------------------
* Use RDP to connect to the Azure virtual machine that hosts the primary replica. 
* Open Failover Cluster Manager.
* Select the **Networks** node, and note the cluster network name. This name will be used in the `$ClusterNetworkName` variable in the PowerShell script.
* Expand the cluster name, and then click **Roles**.
* In the **Roles** pane, right-click the availability group name and then select **Add Resource** > **Client Access Point**.
* In the **Name** box, create a name for this new listener, then click **Next** twice, and then click **Finish**. Do not bring the listener or resource online at this point.
  
  > [!NOTE]
  > The name for the new listener is the network name that applications will use to connect to databases in the SQL Server availability group.
  > 
  > 
* Click the **Resources** tab, then expand the Client Access Point you just created. Right-click the IP resource and click properties. Note the name of the IP address. You will use this name in the `$IPResourceName` variable in the PowerShell script.
* Under **IP Address** click **Static IP Address** and set the static IP address to the same address that you used when you set the load balancer IP address on the Azure portal. Enable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
* On the cluster node that currently hosts the primary replica, open an elevated PowerShell ISE and paste the following commands into a new script.
  
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
  
        Import-Module FailoverClusters
  
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
* Update the variables and run the PowerShell script to configure the IP address and port for the new listener.
  
  > [!NOTE]
  > If your SQL Servers are in separate regions, you need to run the PowerShell script twice. The first time use the cluster network name, cluster IP resource name, and load balancer IP address from the first resource group. The second time use the cluster network name, cluster IP resource name, and load balancer IP address from the second resource group.
  > 
  > 

Now the cluster has an availability group listener resource.

### 2. Bring the listener online
With the availability group listener resource configured, you can bring the listener online so that applications can connect to databases in the availability group with the listener.

* Navigate back to Failover Cluster Manager. Expand **Roles** and then highlight your Availability Group. On the **Resources** tab, right-click the listener name and click **Properties**.
* Click the **Dependencies** tab. If there are multiple resources listed, verify that the IP addresses have OR, not AND, dependencies. Click **OK**.
* Right-click the listener name and click **Bring Online**.
* Once the listener is online, from the **Resources** tab, right-click the availability group and click **Properties**.
* Create a dependency on the listener name resource (not the IP address resources name). Click **OK**.
* Launch SQL Server Management Studio and connect to the primary replica.
* Navigate to **AlwaysOn High Availability** | **Availability Groups** | **Availability Group Listeners**. 
* You should now see the listener name that you created in Failover Cluster Manager. Right-click the listener name and click **Properties**.
* In the **Port** box, specify the port number for the availability group listener by using the $EndpointPort you used earlier (1433 was the default), then click **OK**.

------------------------------->

### <a name="verify-the-configuration-of-the-listener"></a>Verifique a configuração do ouvinte

Se os recursos e as dependências do cluster forem configurados corretamente, você poderá ver o ouvinte no SQL Server Management Studio. Execute as seguintes etapas para configurar a porta do ouvinte:

1. Inicie o SQL Server Management Studio e conecte-se à réplica principal.
2. Navegue até **Alta Disponibilidade do AlwaysOn** | **Grupos de Disponibilidade** | **Ouvintes do Grupo de Disponibilidade**. 
1. Agora você deve ver o nome do ouvinte que você criou no Gerenciador de Cluster de Failover. Clique com o botão direito do mouse no nome do ouvinte e clique em **Propriedades**.
1. Na caixa **Porta**, especifique o número da porta para o ouvinte do grupo de disponibilidade usando o $EndpointPort usado anteriormente (1433 era o padrão) e depois clique em **OK**.

Agora você tem um grupo de disponibilidade AlwaysOn do SQL Server em máquinas virtuais do Azure em execução no modo Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Testar a conexão com o ouvinte
Para testar a conexão:

1. RDP para um SQL Server que está na mesma rede virtual, mas não tem a réplica. Isso pode ser outro SQL Server no cluster.
2. Use o utilitário **sqlcmd** para testar a conexão. Por exemplo, o script a seguir estabelece uma conexão de **sqlcmd** com a réplica primária por meio do ouvinte com autenticação do Windows:
   
        sqlcmd -S <listenerName> -E

A conexão SQLCMD se conecta automaticamente a qualquer instância do SQL Server que hospede a réplica primária. 

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações
Observe as diretrizes a seguir no ouvinte do grupo de disponibilidade no Azure usando o balanceador de carga interno:

* Há suporte somente para um ouvinte de grupo de disponibilidade interno por serviço de nuvem, pois o ouvinte é configurado para o balanceador de carga e há apenas um balanceador de carga interno. No entanto, é possível criar vários ouvintes externos. 
* Com um balanceador de carga interno, você só pode acessar ao ouvinte de dentro da mesma rede virtual.


<!--HONumber=Jan17_HO2-->


