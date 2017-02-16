
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## <a name="create-a-new-azure-sql-database"></a>Criar um novo Banco de Dados SQL do Azure
Use as etapas a seguir no portal do Azure para criar um novo banco de dados SQL do Azure em um servidor lógico de Banco de Dados SQL novo ou existente.

1. Se você não estiver conectado, conecte-se ao [Portal do Azure](http://portal.azure.com).
2. Clique em **Novo**, digite **Banco de Dados SQL** e clique em **Banco de Dados SQL (novo banco de dados)**.
   
     ![Novo banco de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)
3. Clique em **Banco de Dados SQL (novo banco de dados)**.
   
     ![Novo banco de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)
4. Clique em **Criar** para criar um novo banco de dados no serviço Banco de Dados SQL.
   
     ![Novo banco de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)
5. Forneça os valores para as seguintes propriedades de servidor:
   
   * Nome do banco de dados
   * Assinatura: isso será aplicado somente se você tiver várias assinaturas.
   * Grupo de recursos: se você estiver começando, use o grupo de recursos do servidor lógico.
   * Selecione a origem: escolha um banco de dados em branco, dados de exemplo ou um backup de banco de dados do Azure. Para migrar um banco de dados local do SQL Server ou carregar dados usando a ferramenta de linha de comando BCP, consulte os links no final deste artigo.
   * Servidor: um servidor lógico novo ou existente.
   * Logon de administrador do servidor
   * Senha
   * Tipo de preços: se você estiver começando, use o valor padrão S0.
   * Agrupamento: isso será aplicado somente se um banco de dados vazio for escolhido.
     
        ![Novo banco de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)
6. Clique em **Criar**. Veja na área de notificação que a implantação foi iniciada.
   
    ![Novo banco de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)
7. Aguarde até que a implantação seja concluída antes de prosseguir para a próxima etapa.
   
     ![Novo banco de dados](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)



<!--HONumber=Jan17_HO3-->


