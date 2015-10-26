
# Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio 

Você pode usar o SQL Server Management Studio (SSMS) para administrar servidores lógicos e bancos de dados do Banco de Dados SQL do Azure. Este tópico o orienta em tarefas comuns com o SSMS. Você já deve ter um servidor lógico e o banco de dados criados no Banco de Dados SQL do Azure antes de começar. Para começar, leia [Criar o seu Banco de Dados SQL do Azure primeiro](sql-database-get-started.md) e, em seguida, volte.

É recomendável que você use a versão mais recente do SSMS sempre que trabalhar com o Banco de Dados SQL do Azure. Visite [Baixar o SQL Server Management Studio](https://msdn.microsoft.com/pt-BR/library/mt238290.aspx) para obtê-lo.


## Conectar a um servidor lógico do Banco de Dados SQL

Conectar ao banco de dados SQL requer que você saiba o nome do servidor no Azure. Talvez seja necessário entrar no portal para obter essa informação.

1.  Entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2.  No painel à esquerda, clique em **Banco de Dados SQL**.

3.  Na home page do Bancos de Dados SQL, clique em **SERVIDORES** na parte superior da página para listar todos os servidores associados a sua assinatura. Localize o nome do servidor ao qual você deseja se conectar e o copie para a área de transferência.

	Em seguida, configure o firewall de banco de dados SQL para permitir conexões do computador local. Você pode fazer isso adicionando o seu endereço IP de computadores locais à lista de exceção do firewall.

1.  Na home page do bancos de dados SQL, clique em **SERVIDORES** e, em seguida, clique no servidor ao qual você deseja se conectar.

2.  Clique em **Configurar** na parte superior da página.

3.  Copie o endereço IP em endereço de IP de cliente atual.

4.  Na página Configure, **permitido endereços IP** inclui três caixas onde você pode especificar um nome de regra e um intervalo de endereços IP como valores inicial e final. Para o nome de uma regra, você pode inserir o nome do seu computador. Para o intervalo de início e de término, colar no endereço IP do seu computador em ambas as caixas e clique na caixa de seleção que aparece.

	O nome da regra deve ser exclusivo. Se esse for seu computador de desenvolvimento, você pode inserir o endereço IP na caixa de início do intervalo IP e caixa de término do intervalo IP. Caso contrário, talvez seja necessário inserir um amplo intervalo de endereços IP para acomodar conexões de outros computadores em sua organização.
 
5. Na parte inferior da página, clique em **SALVAR**.

    **Observação:** pode haver um atraso de até cinco minutos para as alterações nas configurações de firewall sejam efetivadas.

	Agora você está pronto para se conectar ao banco de dados SQL usando o Management Studio.

1.  Na barra de tarefas, clique em **Iniciar**, aponte para **Todos os programas**, aponte para **Microsoft SQL Server 2014** e, em seguida, clique em **SQL Server Management Studio**.

2.  Em **Conectar-se ao servidor**, especifique o nome do servidor totalmente qualificado como *nomeDoServidor*.database.windows.net. No Azure, o nome do servidor é uma sequência gerada automaticamente composta por caracteres alfanuméricos.

3.  Selecione **Autenticação do SQL Server**.

4.  Na caixa **Login**, digite o logon de administrador do SQL Server que você especificou no portal durante a criação de seu servidor.

5.  Na caixa **Senha**, digite a senha que você especificou no portal durante a criação de seu servidor.

8.  Clique em **Conectar** para estabelecer a conexão.

O SQL Server 2014 SSMS com as atualizações mais recentes oferece suporte expandido para tarefas como criar e modificar bancos de dados SQL Azure. Além disso, você também pode usar instruções Transact-SQL para realizar essas tarefas. As etapas a seguir fornecem exemplos dessas instruções. Para saber mais sobre como usar o Transact-SQL com o banco de dados SQL, incluindo detalhes sobre quais comandos são têm suporte, consulte [Referência de Transact-SQL (banco de dados SQL)](http://msdn.microsoft.com/library/bb510741.aspx).

## Criar e gerenciar Banco de Dados SQL do Azure

Enquanto estiver conectado ao banco de dados **mestre**, você pode criar novos bancos de dados no servidor e modificar ou soltar bancos de dados existentes. As etapas a seguir descrevem como realizar várias tarefas de gerenciamento de banco de dados comum por meio do Management Studio. Para executar essas tarefas, verifique se você está conectado ao banco de dados **mestre** com o logon de nível de servidor principal que criou quando configurou o servidor.

Para abrir uma janela de consulta no Management Studio, abra a pasta de bancos de dados, expanda a pasta **Banco de dados do sistema**, clique com botão direito do mouse em **mestre** e clique em **Nova consulta**.

-   Use o **CRIAR BANCO DE DADOS** instrução para criar um novo banco de dados. Para saber mais, confira [CREATE DATABASE (Banco de dados SQL)](https://msdn.microsoft.com/library/dn268335.aspx). A instrução a seguir cria um novo banco de dados chamado **myTestDB** e especifica que se trata de um banco de dados da Standard S0 Edition com um tamanho padrão máximo de 250 GB.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Clique em **Execute** para executar a consulta.

-   Use a instrução **ALTER DATABASE** para modificar um banco de dados existente, por exemplo, se você quiser alterar o nome e a edição do banco de dados. Para obter mais informações, consulte [ALTER DATABASE (Banco de Dados SQL)](https://msdn.microsoft.com/library/ms174269.aspx). A instrução a seguir modifica o banco de dados criado na etapa anterior para alterar a edição para Standard S1.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   Use a instrução **DROP DATABASE** para excluir um banco de dados existente. Para saber mais, confira [DROP DATABASE (Banco de dados SQL)](https://msdn.microsoft.com/library/ms178613.aspx). A instrução abaixo exclui o banco de dados **myTestDB**, mas não elimine-o agora porque você o utilizará para criar logons na próxima etapa.

        DROP DATABASE myTestBase;

-   O banco de dados mestre tem o modo de exibição **sys.databases**, que você pode usar para exibir detalhes sobre todos os bancos de dados. Para exibir todos os bancos de dados existentes, execute a seguinte instrução:

        SELECT * FROM sys.databases;

-   No banco de dados SQL, a instrução **USE** não é suportada para alternar entre bancos de dados. Em vez disso, você precisa estabelecer uma conexão diretamente com o banco de dados de destino.

>[AZURE.NOTE]Muitas das instruções Transact-SQL que cria ou modifica um banco de dados devem ser executadas em seu próprio lote e não podem ser agrupadas com outras instruções Transact-SQL. Para obter mais informações, consulte as informações específicas de instrução disponíveis dos links listados acima.

## Criar e gerenciar logons

O banco de dados **mestre** controla os logons e quais logons têm permissão para criar bancos de dados ou outros logons. Gerencie logons conectando-se ao banco de dados **mestre** com o logon de nível de servidor principal que você criou quando configurou o servidor. Você pode usar as instruções **CREATE LOGIN**, **ALTER LOGIN** ou **DROP LOGIN** para executar consultas no banco de dados mestre que gerenciarão logons por todo o servidor. Para obter mais informações, consulte [gerenciamento de bancos de dados e logons no banco de dados SQL](http://msdn.microsoft.com/library/azure/ee336235.aspx)


-   Use a instrução **CREATE LOGIN** para criar um novo logon em nível de servidor. Para obter mais informações, consulte [CREATE LOGIN (Banco de Dados SQL)](https://msdn.microsoft.com/library/ms189751.aspx). A instrução a seguir cria um novo logon chamado **login1**. Substitua **password1** pela senha de sua escolha.

        CREATE LOGIN login1 WITH password='password1';

-   Use a instrução **CREATE USER** para conceder permissões em nível de banco de dados. Todos os logons devem ser criados no banco de dados **mestre**, mas para que um logon se conecte a um banco de dados diferente, você deve conceder a esse logon permissões de nível de banco de dados usando a instrução **CREATE USER** nesse banco de dados. Para obter mais informações, consulte [CREATE USER (Banco de Dados SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

-   Para dar permissões de login1 para um banco de dados chamado **myTestDB**, execute as seguintes etapas:

 1.  Para atualizar o Gerenciador de Objetos e visualizar o banco de dados **myTestDB** que você acabou de criar, clique com o botão direito do mouse no nome do servidor no Gerenciador de Objetos e, em seguida, clique em **Atualizar**.  

     Se você fechou a conexão, você pode se reconectar, selecionando **conectar Object Explorer** no menu arquivo.

 2. Clique com o botão direito do mouse em banco de dados **myTestDB** e selecione **nova consulta**.

    3.  Execute a seguinte instrução no banco de dados myTestDB para criar um usuário de banco de dados chamado **login1User** que corresponde ao logon em nível de servidor **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Use o procedimento **sp\_addrolemember** armazenado para oferecer à conta do usuário o nível apropriado de permissões no banco de dados. Para saber mais, consulte [sp\_addrolemember (Transact-SQL)](http://msdn.microsoft.com/library/ms187750.aspx). A declaração a seguir dá ao **login1User** permissões de apenas leitura ao banco de dados adicionando **login1User** à regra **db\_datareader**.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Use a instrução **ALTER LOGIN** para modificar um logon existente, por exemplo, se você desejar alterar a senha para o logon. Para saber mais, consulte [ALTER LOGIN (banco de dados SQL)](https://msdn.microsoft.com/library/ms189828.aspx). A instrução **ALTER LOGIN** deve ser executada no banco de dados **mestre**. Alterne para a janela de consulta que está conectada ao banco de dados.

    A instrução a seguir modifica o logon **logon1** para redefinir a senha. Substitua **novaSenha** pelaa senha de sua escolha, e **senhaAntiga** pela senha atual para o logon.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Use a instrução **DROP LOGIN** para excluir um logon existente. Excluir um logon no nível do servidor também exclui quaisquer contas de usuário do banco de dados associado. Para saber mais, confira [DROP DATABASE (Banco de dados SQL)](https://msdn.microsoft.com/library/ms178613.aspx). A instrução **DROP LOGIN** deve ser executada no banco de dados **mestre**. A instrução a seguir exclui o logon **login1**.

        DROP LOGIN login1;

-   O banco de dados mestre tem a visualização de **sys.sql\_logins** que você pode usar para visualizar logons. Para exibir todos os logons existentes, execute a seguinte instrução:

        SELECT * FROM sys.sql_logins;

## Monitorar o Banco de Dados SQL usando Modos de Exibição de Gerenciamento Dinâmico</h2>

O Banco de dados SQL oferece suporte a vários modos de exibição de gerenciamento dinâmico que você pode usar para monitorar um banco de dados individual. Abaixo estão alguns exemplos do tipo de dados de monitor que podem ser recuperados por meio desses modos de exibição. Para obter detalhes completos e mais exemplos de uso, confira [Monitorando o Banco de Dados SQL Usando Exibições de Gerenciamento Dinâmico](https://msdn.microsoft.com/library/azure/ff394114.aspx).

-   Consultar uma exibição de gerenciamento dinâmico requer permissões **VIEW DATABASE STATE**. Para conceder a permissão **VIEW DATABASE STATE** a um usuário de banco de dados específico, conecte-se ao banco de dados que você deseja gerenciar com o logon principal de nível de servidor e execute a seguinte instrução no banco de dados:

        GRANT VIEW DATABASE STATE TO login1User;

-   Calcular banco de dados usando a exibição **sys.dm\_db\_partition\_stats**. A exibição **sys.dm\_db\_partition\_stats** retorna informações da página e sobre a contagem de linhas para cada partição do banco de dados, que você pode usar para calcular o tamanho do banco de dados. A consulta a seguir retorna o tamanho do seu banco de dados em megabytes:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Use as exibições **sys.dm\_exec\_connections** e **sys.dm\_exec\_sessions** para recuperar informações sobre as conexões do usuário atual e tarefas internas associadas ao banco de dados. A consulta a seguir retorna informações sobre a conexão atual:

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   Use a exibição **sys.dm\_exec\_query\_stats** para recuperar estatísticas de desempenho agregado para planos de consulta em cache. A consulta a seguir retorna informações sobre as consultas de cinco principais classificados por tempo médio de CPU.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;

<!---HONumber=Oct15_HO3-->