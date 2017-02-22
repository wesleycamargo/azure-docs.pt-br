### <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall de nível de servidor no portal do Azure

1. Na folha do SQL server, em Configurações, clique em **Firewall** para abrir a folha de Firewall para o SQL server.

    ![firewall do sql server](./media/sql-data-warehouse-server-firewall/sql-server-firewall.png)
    
2. Examine o endereço IP do cliente exibido e confirme se esse é o endereço IP na Internet usando um navegador de sua escolha (perguntar "o que é meu endereço IP). Ocasionalmente, eles não coincidem por vários motivos.

    ![seu endereço IP](../articles/sql-database/media/sql-database-get-started/your-ip-address.png)

3. Supondo que os endereços IP têm correspondência, clique em **Adicionar IP do cliente** na barra de ferramentas.

    ![adicionar IP do cliente](./media/sql-data-warehouse-server-firewall/add-client-ip.png)

    > [!NOTE]
    > Você pode abrir o firewall no SQL Server (servidor lógico) para um único endereço IP ou um intervalo inteiro de endereços. Abrir o firewall permite que os administradores do SQL e os usuários façam logon em qualquer banco de dados no servidor para o qual eles têm credenciais válidas.
    >

4. Clique em **Salvar** na barra de ferramentas para salvar essa regra de firewall de nível de servidor e, em seguida, clique em **OK**.

    ![adicionar IP do cliente](../articles/sql-database/media/sql-database-get-started/save-firewall-rule.png)

<!--HONumber=Jan17_HO3-->


