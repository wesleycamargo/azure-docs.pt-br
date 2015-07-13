
# Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio 

Você pode usar o Portal de gerenciamento de banco de dados do Azure SQL ou o aplicativo de cliente do SQL Server Management Studio (SSMS) para administrar suas assinaturas de banco de dados SQL e criar e gerenciar servidores lógicos e bancos de dados está associada. O guia a seguir descreve como usar o Management Studio para gerenciar bancos de dados e servidores lógicos do banco de dados SQL.

>[AZURE.NOTE]Você deve usar o SQL Server 2014 Management Studio e instalar as atualizações mais recentes (CU5 ou posterior) para gerenciar o banco de dados SQL, incluindo Atualização de banco de dados SQL V12 mais recente. Você também pode usar o SQL Server 2012 ou a versão do SQL Server 2008 R2 do SSMS. Não há suporte para versões anteriores.

Este tópico inclui as seguintes etapas:

-   [Etapa 1: obter o SQL Server 2014 Management Studio][]
-   [Etapa 2: conectar-se ao Banco de Dados SQL][]
-   [Etapa 3: criar e gerenciar os bancos de dados][]
-   [Etapa 4: criar e gerenciar os logons][]
-   [Etapa 5: monitorar o Banco de Dados SQL usando Exibições de Gerenciamento Dinâmico][]

<h2><a id="Step1" name="Step1"> </a>Etapa 1: obter o SQL Server 2014 Management Studio</h2>

O Management Studio é um ambiente integrado para gerenciar bancos de dados SQL. Ao gerenciar bancos de dados no Azure, você pode usar o aplicativo do Management Studio instalado com o SQL Server ou baixar a versão gratuita do SQL Server 2014 Management Studio (SSMS). As etapas a seguir descrevem como instalar o SSMS.

1.  Na página do [Microsoft SQL Server 2014 Express][], clique em **Baixar** para abrir a janela de seleção de arquivo.

2.  Selecione **MgmtStudio 32BIT\SQLManagementStudio_x86_ENU.exe** se você estiver executando um sistema operacional de 32 bits, ou **MgmtStudio 64BIT\SQLManagementStudio_x64_ENU.exe** se estiver executando um sistema operacional de 64 bits.

3.  Clique em **Avançar** e, quando solicitado, execute a instalação.

2.  Clique em **Instalação autônoma do novo SQL Server ou adicionar recursos a uma instalação existente** e clique em **OK**.

3.  Aceite os termos de licença e clique em **Avançar**.

4. Clique em **instalar** para instalar os arquivos necessários para a instalação do SQL Server.

5.  Na tela **Seleção de Recursos**, **Ferramentas de Gerenciamento - Básicas** e **Ferramentas de Gerenciamento - Completas** são pré-selecionadas. Clique em **Próximo**.

6.  Na tela de **Relatório de erros**, você pode optar por enviar relatórios de erros para a Microsoft.

7.  Quando a instalação estiver concluída, você verá a página **Concluído**. Clique em **fechar**

8. Instale as atualizações mais recentes na página [Pacote de atualização cumulativa 5 do SQL Server 2014][].

<h2><a id="Step2" name="Step2"> </a>Etapa 2: conectar-se ao Banco de Dados SQL</h2>

Conectar ao banco de dados SQL requer que você saiba o nome do servidor no Azure. Talvez seja necessário entrar no portal para obter essa informação.

1.  Entre no [Portal de Gerenciamento do Azure][].

2.  No painel esquerdo de navegação, clique em **Bancos de Dados SQL**.

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

O SQL Server 2014 SSMS com as atualizações mais recentes oferece suporte expandido para tarefas como criar e modificar bancos de dados SQL Azure. Além disso, você também pode usar instruções Transact-SQL para realizar essas tarefas. As etapas a seguir fornecem exemplos dessas instruções. Para saber mais sobre como usar o Transact-SQL com o banco de dados SQL, incluindo detalhes sobre quais comandos são têm suporte, consulte [Referência de Transact-SQL (banco de dados SQL)][].

<h2><a id="Step3" name="Step3"> </a>Etapa 3: criar e gerenciar os bancos de dados</h2>

Enquanto estiver conectado ao banco de dados **mestre**, você pode criar novos bancos de dados no servidor e modificar ou soltar bancos de dados existentes. As etapas a seguir descrevem como realizar várias tarefas de gerenciamento de banco de dados comum por meio do Management Studio. Para executar essas tarefas, verifique se você está conectado ao banco de dados **mestre** com o logon de nível de servidor principal que criou quando configurou o servidor.

Para abrir uma janela de consulta no Management Studio, abra a pasta de bancos de dados, expanda a pasta **Banco de dados do sistema**, clique com botão direito do mouse em **mestre** e clique em **Nova consulta**.

-   Use o **CRIAR BANCO DE DADOS** instrução para criar um novo banco de dados. Para saber mais, confira [CREATE DATABASE (Banco de dados SQL)][]. A instrução a seguir cria um novo banco de dados chamado **myTestDB** e especifica que se trata de um banco de dados da Standard S0 Edition com um tamanho padrão máximo de 250 GB.

        CREATE DATABASE myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Clique em **Execute** para executar a consulta.

-   Use a instrução **ALTER DATABASE** para modificar um banco de dados existente, por exemplo, se você quiser alterar o nome e a edição do banco de dados. Para obter mais informações, consulte [ALTER DATABASE (Banco de Dados SQL)][]. A instrução a seguir modifica o banco de dados criado na etapa anterior para alterar a edição para Standard S1.

        ALTER DATABASE myTestDB
        MODIFY
        (SERVICE_OBJECTIVE='S1');

-   Use a instrução **DROP DATABASE** para excluir um banco de dados existente. Para saber mais, confira [DROP DATABASE (Banco de dados SQL)][]. A instrução abaixo exclui o banco de dados **myTestDB**, mas não elimine-o agora porque você o utilizará para criar logons na próxima etapa.

        DROP DATABASE myTestBase;

-   O banco de dados mestre tem o modo de exibição **sys.databases**, que você pode usar para exibir detalhes sobre todos os bancos de dados. Para exibir todos os bancos de dados existentes, execute a seguinte instrução:

        SELECT * FROM sys.databases;

-   No banco de dados SQL, a instrução **USE** não é suportada para alternar entre bancos de dados. Em vez disso, você precisa estabelecer uma conexão diretamente com o banco de dados de destino.

>[AZURE.NOTE]Muitas das instruções Transact-SQL que cria ou modifica um banco de dados devem ser executadas em seu próprio lote e não podem ser agrupadas com outras instruções Transact-SQL. Para obter mais informações, consulte as informações específicas de instrução disponíveis dos links listados acima.

<h2><a id="Step4" name="Step4"> </a>Etapa 4: criar e gerenciar os logons</h2>

O banco de dados **mestre** controla os logons e quais logons têm permissão para criar bancos de dados ou outros logons. Gerencie logons conectando-se ao banco de dados **mestre** com o logon de nível de servidor principal que você criou quando configurou o servidor. Você pode usar as instruções **CREATE LOGIN**, **ALTER LOGIN** ou **DROP LOGIN** para executar consultas no banco de dados mestre que gerenciarão logons por todo o servidor. Para obter mais informações, consulte [gerenciamento de bancos de dados e logons no banco de dados SQL][]


-   Use a instrução **CREATE LOGIN** para criar um novo logon em nível de servidor. Para obter mais informações, consulte [CREATE LOGIN (Banco de Dados SQL)][]. A instrução a seguir cria um novo logon chamado **login1**. Substitua **password1** pela senha de sua escolha.

        CREATE LOGIN login1 WITH password='password1';

-   Use a instrução **CREATE USER** para conceder permissões em nível de banco de dados. Todos os logons devem ser criados no banco de dados **mestre**, mas para que um logon se conecte a um banco de dados diferente, você deve conceder a esse logon permissões de nível de banco de dados usando a instrução **CREATE USER** nesse banco de dados. Para obter mais informações, consulte [CREATE USER (Banco de Dados SQL)][].

-   Para dar permissões de login1 para um banco de dados chamado **myTestDB**, execute as seguintes etapas:

 1.  Para atualizar o Gerenciador de Objetos e visualizar o banco de dados **myTestDB** que você acabou de criar, clique com o botão direito do mouse no nome do servidor no Gerenciador de Objetos e, em seguida, clique em **Atualizar**.  

     Se você fechou a conexão, você pode se reconectar, selecionando **conectar Object Explorer** no menu arquivo. Repita as instruções [etapa 2: conectar-se ao banco de dados SQL][] para se conectar ao banco de dados.

 2. Clique com o botão direito do mouse em banco de dados **myTestDB** e selecione **nova consulta**.

    3.  Execute a seguinte instrução no banco de dados myTestDB para criar um usuário de banco de dados chamado **login1User** que corresponde ao logon em nível de servidor **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Use o procedimento **sp_addrolemember** armazenado para oferecer à conta do usuário o nível apropriado de permissões no banco de dados. Para saber mais, consulte [sp_addrolemember (Transact-SQL)][]. A declaração a seguir dá ao **login1User** permissões de apenas leitura ao banco de dados adicionando **login1User** à regra **db_datareader**.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Use a instrução **ALTER LOGIN** para modificar um logon existente, por exemplo, se você desejar alterar a senha para o logon. Para saber mais, consulte [ALTER LOGIN (banco de dados SQL)][]. A instrução **ALTER LOGIN** deve ser executada no banco de dados **mestre**. Alterne para a janela de consulta que está conectada ao banco de dados.

    A instrução a seguir modifica o logon **logon1** para redefinir a senha. Substitua **novaSenha** pelaa senha de sua escolha, e **senhaAntiga** pela senha atual para o logon.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Use a instrução **DROP LOGIN** para excluir um logon existente. Excluir um logon no nível do servidor também exclui quaisquer contas de usuário do banco de dados associado. Para saber mais, confira [DROP DATABASE (Banco de dados SQL)][]. A instrução **DROP LOGIN** deve ser executada no banco de dados **mestre**. A instrução a seguir exclui o logon **login1**.

        DROP LOGIN login1;

-   O banco de dados mestre tem a visualização de **sys.sql_logins** que você pode usar para visualizar logons. Para exibir todos os logons existentes, execute a seguinte instrução:

        SELECT * FROM sys.sql_logins;

<h2><a id="Step5" name="Step5"> </a>Etapa 5: monitorar o Banco de Dados SQL usando Exibições de Gerenciamento Dinâmico</h2>

O Banco de dados SQL oferece suporte a vários modos de exibição de gerenciamento dinâmico que você pode usar para monitorar um banco de dados individual. Abaixo estão alguns exemplos do tipo de dados de monitor que podem ser recuperados por meio desses modos de exibição. Para obter detalhes completos e mais exemplos de uso, confira [Monitorando o Banco de Dados SQL Usando Exibições de Gerenciamento Dinâmico][].

-   Consultar uma exibição de gerenciamento dinâmico requer permissões **VIEW DATABASE STATE**. Para conceder a permissão **VIEW DATABASE STATE** a um usuário de banco de dados específico, conecte-se ao banco de dados que você deseja gerenciar com o logon principal de nível de servidor e execute a seguinte instrução no banco de dados:

        GRANT VIEW DATABASE STATE TO login1User;

-   Calcular banco de dados usando a exibição **sys.dm_db_partition_stats**. A exibição **sys.dm_db_partition_stats** retorna informações da página e sobre a contagem de linhas para cada partição do banco de dados, que você pode usar para calcular o tamanho do banco de dados. A consulta a seguir retorna o tamanho do seu banco de dados em megabytes:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Use as exibições **sys.dm_exec_connections** e **sys.dm_exec_sessions** para recuperar informações sobre as conexões do usuário atual e tarefas internas associadas ao banco de dados. A consulta a seguir retorna informações sobre a conexão atual:

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

-   Use a exibição **sys.dm_exec_query_stats** para recuperar estatísticas de desempenho agregado para planos de consulta em cache. A consulta a seguir retorna informações sobre as consultas de cinco principais classificados por tempo médio de CPU.

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

<h2>Recursos adicionais</h2>

* [Introdução ao banco de dados SQL][]   
* [Gerenciando bancos de dados e logons no banco de dados SQL][]   
* [Monitorando o Banco de Dados SQL Usando Exibições de Gerenciamento Dinâmico][]   
* [Referência Transact-SQL (Banco de Dados SQL)][]

  [How to use Azure SQL Database]: http://www.windowsazure.com/develop/net/how-to-guides/sql-azure/
  [Etapa 1: obter o SQL Server 2014 Management Studio]: #Step1
  [Etapa 2: conectar-se ao Banco de Dados SQL]: #Step2
  [Etapa 3: criar e gerenciar os bancos de dados]: #Step3
  [Etapa 4: criar e gerenciar os logons]: #Step4
  [Etapa 5: monitorar o Banco de Dados SQL usando Exibições de Gerenciamento Dinâmico]: #Step5
  [Microsoft SQL Server 2014 Express]: http://www.microsoft.com/download/details.aspx?id=42299
  [Pacote de atualização cumulativa 5 do SQL Server 2014]: http://support2.microsoft.com/kb/3011055
  [SSMS Installer - Select installation type]: /media/installer_installation_type.png
  [SSMS Installer - Select features]: /media/installer_feature_selection.png
  [SSMS Installer - Installation complete]: /media/installer_completed.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
  [Get SQL Database server name from Management Portal]: /media/portal_get_database_name.png
  [Connect to SSMS]: /media/ssms_connect.png
  [Connect to SSMS -- properties]: /media/ssms_connect_properties.png
  [Referência Transact-SQL (Banco de Dados SQL)]: http://msdn.microsoft.com/library/bb510741(v=sql.120).aspx
  [Referência de Transact-SQL (banco de dados SQL)]: http://msdn.microsoft.com/library/bb510741(v=sql.120).aspx
  [CREATE DATABASE (Banco de dados SQL)]: https://msdn.microsoft.com/library/dn268335.aspx
  [ALTER DATABASE (Banco de Dados SQL)]: https://msdn.microsoft.com/library/ms174269.aspx
  [DROP DATABASE (Banco de dados SQL)]: https://msdn.microsoft.com/library/ms178613.aspx
  [Gerenciando bancos de dados e logons no banco de dados SQL]: http://msdn.microsoft.com/library/windowsazure/ee336235.aspx
  [gerenciamento de bancos de dados e logons no banco de dados SQL]: http://msdn.microsoft.com/library/windowsazure/ee336235.aspx
  [CREATE LOGIN (Banco de Dados SQL)]: https://msdn.microsoft.com/library/ms189751.aspx
  [CREATE USER (Banco de Dados SQL)]: https://msdn.microsoft.com/library/ms173463.aspx
  [sp_addrolemember (Transact-SQL)]: http://msdn.microsoft.com/library/ms187750.aspx
  [ALTER LOGIN (banco de dados SQL)]: https://msdn.microsoft.com/library/ms189828.aspx
  [Monitorando o Banco de Dados SQL Usando Exibições de Gerenciamento Dinâmico]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
  [Introdução ao banco de dados SQL]: http://azure.microsoft.com/services/sql-database/
 

<!---HONumber=62-->