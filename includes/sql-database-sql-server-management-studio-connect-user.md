## <a name="connect-to-azure-sql-database-as-a-user"></a>Conectar-se ao Banco de Dados SQL do Azure como um usuário
Use as etapas a seguir para se conectar ao Banco de Dados SQL do Azure com SSMS como um usuário.

1. Digite "Microsoft SQL Server Management Studio" na caixa de pesquisa do Windows e clique no aplicativo de área de trabalho para iniciar o SSMS.
2. Na janela Conectar ao Servidor, insira as informações a seguir:

* **Tipo de servidor**: o padrão é o mecanismo de banco de dados; não altere esse valor.
  
  * **Nome do servidor**: digite o nome do servidor que hospeda seu banco de dados SQL no seguinte formato: *&lt;servername>*.**database.windows.net**
  * **Tipo de autenticação**: se você acabou de começar, selecione a Autenticação SQL. Se você tiver habilitado o Active Directory para seu servidor lógico do Banco de Dados SQL, selecione a Autenticação de Senha do Active Directory ou a Autenticação Integrada do Active Directory.
  * **Nome de usuário**: se você selecionou a Autenticação do SQL ou a Autenticação de Senha do Active Directory, digite o nome de um usuário com acesso ao banco de dados no servidor.
  * **Senha**: se você selecionou a Autenticação do SQL ou a Autenticação de Senha do Active Directory, digite a senha para o usuário especificado.
    
       ![SQL Server Manager Studio: conectar-se a um servidor de Banco de Dados SQL](./media/sql-database-sql-server-management-studio-connect-user/connect-user-1.png)

1. Clique em **Opções** para especificar o banco de dados ao qual você deseja se conectar.
   
      ![SQL Server Manager Studio: conectar-se a um servidor de Banco de Dados SQL](./media/sql-database-sql-server-management-studio-connect-user/connect-user-2.png)
2. Em **Conectar ao Banco de Dados**, selecione o banco de dados ao qual você deseja se conectar.
   
     ![SQL Server Manager Studio: conectar-se a um servidor de Banco de Dados SQL](./media/sql-database-sql-server-management-studio-connect-user/connect-user-3.png)
3. Clique em **Conectar**.
4. Se o endereço IP de seu cliente não tiver acesso ao servidor lógico do Banco de Dados SQL, você receberá uma solicitação para entrar em uma conta do Azure e criar uma regra de firewall no nível do servidor. Se você for um administrador de assinatura do Azure, clique em **Entrar** para criar uma regra de firewall no nível do servidor. Caso contrário, peça para um administrador criar uma regra de firewall no nível do servidor ou uma regra de firewall no nível do banco de dados no banco de dados ao qual você está tentando se conectar.
   
      ![SQL Server Manager Studio: conectar-se a um servidor de Banco de Dados SQL](./media/sql-database-sql-server-management-studio-connect-user/connect-user-4.png)
5. Se suas credenciais concederem a você acesso ao banco de dados especificado, o Pesquisador de Objetos será aberto e você poderá executar tarefas administrativas ou consultar dados, dependendo das permissões do usuário.
   
      ![SQL Server Manager Studio: conectar-se a um servidor de Banco de Dados SQL](./media/sql-database-sql-server-management-studio-connect-user/connect-user-5.png)

## <a name="troubleshoot-connection-failures"></a>Solucionar falhas de conexão
O motivo mais comum das falhas de conexão são os erros no nome do servidor (lembre-se, <*nomeservidor*> é o nome do servidor lógico, não do banco de dados), nome de usuário ou senha, bem como o fato de o servidor não permitir conexões por motivos de segurança. 

