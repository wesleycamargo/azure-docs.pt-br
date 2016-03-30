Nesta etapa, você cria manualmente o ouvinte do grupo de disponibilidade no Gerenciador de Cluster de Failover e no Server Management Studio (SSMS).

1. Abra o Gerenciador de Cluster de Failover a partir do nó que hospeda a réplica primária.

1. Selecione o nó **Redes** e observe o nome da rede do cluster. Esse nome será usado na variável $ClusterNetworkName no script do PowerShell.

1. Expanda o nome do cluster e, em seguida, clique em **Funções**.

1. No painel **Funções**, clique com o botão direito do mouse no nome do grupo de disponibilidade e, em seguida, selecione**Adicionar recurso**>**Ponto de acesso para o cliente**.

	![Adicionar ponto de acesso para cliente para o grupo de disponibilidade](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

1. Na caixa **Nome**, crie um nome para este novo ouvinte e clique em **Próximo** duas vezes e, em seguida, clique em**Concluir**. Não coloque o ouvinte ou o recurso online neste momento.

1. Clique a guia **recursos**, em seguida, expanda o ponto de acesso do cliente que você acabou de criar. Você verá o recurso **Endereço IP** para cada uma das redes de cluster no seu cluster. Se essa é uma solução somente no Azure, você verá somente um recurso de endereço IP.

1. Se você estiver configurando uma solução híbrida, continue com esta etapa. Se você estiver configurando uma única solução do Azure, pule para a próxima etapa.
	 - Clique no botão direito do mouse no recurso de endereço IP que corresponde à sua sub-rede local e selecione **Propriedades**. Observe o nome do endereço IP e nome da rede.
	 - Selecione ao **Endereço IP estático**, atribua um endereço IP não utiliado e, em seguida, clique em **OK**.

1. Clique no botão direito do mouse no recurso de endereço IP que corresponde à sua sub-rede local e selecione Propriedades.
	>[AZURE.NOTE] Se o ouvinte posteriormente não ficar online, devido a um endereço IP problemático selecionado pelo DHCP, você pode configurar um endereço IP estático válido nessa janela de propriedades.

1. Na mesma janela de propriedades **endereço IP**, alterar o**Nome do endereço IP**. Esse nome de endereço IP será usado no **$IPResourceName**variável do script do PowerShell. Repita essa etapa para cada recurso IP se sua solução abrange diversas VNets do Azure.
<!----HONumber=Oct15_HO3-->