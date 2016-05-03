
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md
articles/sql-database/sql-database-configure-firewall-settings

-->
## Criar um novo firewall de nível de servidor do Banco de Dados SQL do Azure

Use as seguintes etapas no portal do Azure para criar uma regra de firewall de nível de servidor que permite conexões de um endereço IP individual (seu computador cliente) ou um intervalo inteiro de endereços IP para um servidor lógico do Banco de Dados SQL.

1. Se não estiver conectado, conecte-se ao [Portal do Azure](http://portal.azure.com).
2. Na folha padrão, clique em **SQL Server**.

  	![firewall do novo servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-1.png)

2. Na folha SQL Server, clique no servidor de Banco de Dados SQL no qual a regra de firewall deve ser criada.

 	![firewall do novo servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-2.png)
           
3. Analise as propriedades do servidor.

 	![firewall do novo servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-3.png)
      
4. Na folha Configurações, clique em **Firewall**.

 	![firewall do novo servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-4.png)
    
5. Clique em **Adicionar IP do Cliente** para que o Azure crie uma regra para o endereço IP do cliente.

      ![firewall do novo servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-5.png)

6. Opcionalmente, clique no endereço IP que foi adicionado para editar o endereço de firewall para permitir o acesso a um intervalo de endereços IP.

      ![firewall do novo servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-6.png)
    
7. Clique em **Salvar** para criar a regra de firewall de nível de servidor.

     ![firewall do novo servidor](./media/sql-database-create-new-server-firewall-portal/sql-database-create-new-server-firewall-portal-7.png)

	>[AZURE.IMPORTANT] Seu endereço IP de cliente pode mudar periodicamente, e talvez você não possa acessar seu servidor até que crie uma nova regra de firewall. Você pode verificar seu endereço IP usando o [Bing](http://www.bing.com/search?q=my%20ip%20address) e depois adicionar um único endereço IP ou um intervalo de endereços IP. Confira [Gerenciar configurações de firewall](sql-database-configure-firewall-settings.md#manage-existing-server-level-firewall-rules-through-the-azure-portal) para obter detalhes.

<!---HONumber=AcomDC_0427_2016-->