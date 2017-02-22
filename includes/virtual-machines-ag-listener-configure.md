O ouvinte do Grupo de Disponibilidade é um nome de rede e endereço IP que o Grupo de Disponibilidade do SQL Server escuta. Para criar o ouvinte do Grupo de Disponibilidade, execute as etapas a seguir:

1. [Obter o nome do recurso da rede de cluster](#getnet).

1. [Adicionar o ponto de acesso de cliente](#addcap).

1. [Configure o recurso de IP do Grupo de Disponibilidade](#congroup).

1. [Torne o recurso de grupo de disponibilidade do SQL Server dependente do ponto de acesso para cliente](#dependencyGroup)

1. [Torne o recurso de ponto de acesso de cliente dependente do endereço IP](#listname).

1. [Defina os parâmetros de cluster no PowerShell](#setparam).

As seções a seguir fornecem instruções detalhadas para cada uma dessas etapas. 

#### <a name="a-namegetnetaget-the-name-of-the-cluster-network-resource"></a><a name="getnet"></a>Obter o nome do recurso da rede de clusters

1. Use o RDP para se conectar à máquina virtual do Azure que hospeda a réplica primária. 

1. Abra o Gerenciador de Cluster de Failover.

1. Selecione o nó **Redes** e observe o nome da rede do cluster. Use esse nome na variável `$ClusterNetworkName` no script do PowerShell.

   Na figura a seguir, o nome da rede de clusters é **Rede de Cluster 1**:

   ![Nome da rede de clusters](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

#### <a name="a-nameaddcapaadd-the-client-access-point"></a><a name="addcap"></a>Adicionar o ponto de acesso de cliente

O ponto de acesso do cliente é o nome da rede que os aplicativos usam para se conectar aos bancos de dados em um grupo de disponibilidade. Crie o ponto de acesso de cliente no Gerenciador de Cluster de Failover. 

1. Expanda o nome do cluster e, em seguida, clique em **Funções**.

1. No painel **Funções**, clique com o botão direito do mouse no nome do Grupo de Disponibilidade e, em seguida, selecione **Adicionar recurso** > **Ponto de acesso para o cliente**.

   ![Ponto de acesso para cliente](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

1. Na caixa **Nome**, crie um nome para este novo ouvinte. 

   O nome para o novo ouvinte é o nome da rede que o aplicativo usará para se conectar aos bancos de dados no Grupo de Disponibilidade do SQL Server.
   
   Para concluir a criação do ouvinte, clique em **Próximo** duas vezes e, em seguida, clique em **Concluir**. Não coloque o ouvinte ou o recurso online neste momento.
   
#### <a name="a-namecongroupaconfigure-the-ip-resource-for-the-availability-group"></a><a name="congroup"></a>Configure o recurso de IP do Grupo de Disponibilidade

1. Clique na guia **Recursos**e , em seguida, expanda o ponto de acesso para cliente que você acabou de criar. O ponto de acesso para cliente está offline.

   ![Ponto de acesso para cliente](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

1. Clique com o botão direito do mouse no recurso de IP e clique em Propriedades. Observe o nome do endereço IP. Use esse nome na variável `$IPResourceName` no script do PowerShell.

1. Em **Endereço IP**, clique em **Endereço IP Estático**. Defina o endereço IP para o mesmo endereço que você usou quando definiu o endereço do balanceador de carga no portal do Azure.

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

<!-----------------------I don't see this option on server 2016
1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
------------------------->

#### <a name="a-name--dependencygroupamake-the-sql-server-availability-group-resource-dependent-on-the-client-access-point"></a><a name = "dependencyGroup"></a>Torne o recurso do grupo de disponibilidade do SQL Server dependente do ponto de acesso para cliente

1. No Gerenciador de Cluster de Failover, clique em **Funções** e em seu Grupo de Disponibilidade.

1. Na guia **Recursos**, clique com o botão direito do mouse no grupo de recursos em **Nome do Servidor** e clique em **Propriedades**. 

1. Na guia dependências, adicione o recurso de nome. Este recurso é o ponto de acesso para cliente. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

1. Clique em **OK**.

#### <a name="a-namelistnameamake-the-client-access-point-resource-dependent-on-the-ip-address"></a><a name="listname"></a>Torne o recurso de ponto de acesso para cliente dependente do endereço IP

1. No Gerenciador de Cluster de Failover, clique em **Funções** e em seu Grupo de Disponibilidade. 

1. Na guia **Recursos**, clique com o botão direito do mouse no recurso do ponto de acesso no **Nome do Servidor** e clique em **Propriedades**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

1. Selecione a guia **Dependências** . Defina uma dependência no nome do recurso de ouvinte. Se houver vários recursos listados, verifique se os endereços IP têm dependências OR, e não AND. Clique em **OK**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

1. Clique com o botão direito do mouse no nome do ouvinte e clique em **Trazer online**. 

#### <a name="a-namesetparamaset-the-cluster-parameters-in-powershell"></a><a name="setparam"></a>Defina os parâmetros de cluster no PowerShell

1. Copie o seguinte script do PowerShell para um de seus Servidores SQL. Atualize as variáveis para o seu ambiente.     
   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

2. Defina os parâmetros de cluster executando o script do PowerShell em um dos nós do cluster.  

> [!NOTE]
> Se seus servidores SQL estiverem em regiões separadas, você precisará executar o script do PowerShell duas vezes. Na primeira vez, use o `$ILBIP` e `$ProbePort` da primeira região. Na primeira vez, use o `$ILBIP` e `$ProbePort` da primeira região. O nome de rede do cluster e o nome de recurso de IP de cluster são os mesmos. 




<!--HONumber=Jan17_HO2-->


