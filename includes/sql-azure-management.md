
# Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio 

Você pode usar o Portal de Gerenciamento de Banco de Dados do Azure SQL ou o aplicativo cliente do SQL Server Management Studio (SSMS) para administrar suas assinaturas de banco de dados SQL e criar e gerenciar servidores lógicos e bancos de dados associados. O guia a seguir descreve como usar o Management Studio para gerenciar bancos de dados e servidores lógicos do banco de dados SQL.

>[AZURE.NOTE] Você deve usar o SQL Server 2014 Management Studio e instalar as atualizações mais recentes (CU5 ou posterior) para gerenciar o banco de dados SQL, incluindo Atualização de banco de dados SQL V12 mais recente. Você também pode usar o SQL Server 2012 ou a versão do SQL Server 2008 R2 do SSMS. Não há suporte para versões anteriores. 

Este tópico inclui as seguintes etapas:

-   [Etapa 1: Obter o SQL Server 2014 Management Studio][]
-   [Etapa 2: conectar-se ao Banco de Dados SQL][]
-   [Etapa 3: Criar e gerenciar bancos de dados][]
-   [Etapa 4: Criar e gerenciar logons][]
-   [Etapa 5: monitorar o Banco de Dados SQL usando Modos de Exibição de Gerenciamento Dinâmico][]

<h2><a id="Step1" name="Step1"> </a>Etapa 1: Obter o SQL Server 2014 Management Studio</h2>

O Management Studio é um ambiente integrado para
gerenciando de bancos de dados SQL. Ao gerenciar 
bancos de dados no Azure, você pode usar o aplicativo Management Studio instalado com
o SQL Server ou baixar a versão gratuita do SQL Server 2014 Management Studio (SSMS). As etapas a seguir descrevem
como instalar o SSMS.

1.  Na página do [Microsoft SQL Server 2014 Express][], role para baixo e selecione **MgmtStudio 32BIT\SQLManagementStudio_x86_ENU.exe** se você estiver executando um sistema operacional de 32 bits, ou **MgmtStudio 64BIT\SQLManagementStudio_x64_ENU.exe** se você estiver executando um sistema operacional de 64 bits. Clique em **Próximo** e, quando solicitado, execute a instalação.

2.  Clique **Instalação independente do novo SQL Server ou adicionar recursos a uma
    instalação existente** e clique em **OK**.

3.  Aceite os termos de licença e clique em **Próximo**.

4. Clique em **Instalar** para instalar os arquivos necessários para a instalação do SQL Server.

5.  Na tela **Seleção de Recursos**, **Ferramentas de Gerenciamento - Básicas** e **Ferramentas de Gerenciamento - Completas** são pré-selecionadas. Clique em **Avançar**.

6.  Na tela de **Relatório de erros**, você pode optar por enviar
    relatórios de erros à Microsoft.

7.  Quando a instalação for concluída, você verá a página **Concluído**
    . Clique em **Fechar**. 

8. Instale as atualizações mais recentes na página [Pacote de atualização cumulativa 5 do SQL Server 2014][].

<h2><a id="Step2" name="Step2"> </a>Etapa 2: conectar-se ao Banco de Dados SQL</h2>

Conectar ao banco de dados SQL requer que você saiba o nome do servidor no Azure. Talvez seja necessário entrar no portal para obter essa informação.

1.  Entre no [Portal de Gerenciamento do Azure][].

2.  No painel esquerdo de navegação, clique em **Bancos de Dados SQL**.

3.  Na página inicial Bancos de Dados SQL, clique em **SERVIDORES** na parte superior da página para listar todos os servidores associados a sua assinatura. Localize o nome do servidor ao qual você deseja se conectar e o copie para a área de transferência.

	Em seguida, configure o firewall do banco de dados SQL para
permitir conexões do computador local. Você pode fazer isso adicionando o seu endereço IP de computadores locais à lista de exceção do firewall.

1.  Na página inicial Bancos de dados SQL, clique em **SERVIDORES** e, em seguida, clique no servidor ao qual você deseja se conectar.

2.  Clique em **Configurar** na parte superior da página.

3.  Copie o endereço IP em ENDEREÇO DE IP DE CLIENTE ATUAL.

4.  Na página Configurar, **Endereços IP permitidos** inclui três caixas onde você pode especificar um nome de regra e um intervalo de endereços IP como valores inicial e final. Para o nome de uma regra, você pode inserir o nome do seu computador. Para o intervalo de início e de término, cole no endereço IP do seu computador em ambas as caixas e clique na caixa de seleção que aparece.

	O nome da regra deve ser exclusivo. Se esse for seu computador de desenvolvimento, você pode inserir o endereço IP na caixa de início do intervalo IP e caixa de término do intervalo IP. Caso contrário, talvez seja necessário inserir um amplo intervalo de endereços IP para acomodar conexões de outros computadores em sua organização.
 
5. Na parte inferior da página, clique em **SALVAR**.

    **Observação:** Pode haver até cinco minutos de atraso para que as alterações
    das configurações de firewall sejam efetivadas.

	Agora você está pronto para se conectar ao banco de dados SQL usando o Management Studio.

1.  Na barra de tarefas, clique em **Iniciar**, aponte para **Todos os programas**, aponte para
    **Microsoft SQL Server 2014** e, então, clique em **SQL Server
    Management Studio**.

2.  Em **Conectar-se ao servidor**, especifique o nome de servidor totalmente qualificado
    como *serverName*.database.windows.net. No Azure, o nome do servidor é uma sequência gerada automaticamente composta por caracteres alfanuméricos.

3.  Selecione **Autenticação do SQL Server**.

4.  Na caixa **Logon**, insira o logon do administrador do SQL Server que você
    especificou no portal quando criou seu servidor.

5.  Na caixa **Senha**, digite a senha que você especificou no
    portal quando criou seu servidor.

8.  Clique em **Conectar** para estabelecer a conexão.

SQL Server 2014 SSMS com as atualizações mais recentes oferece suporte expandido para tarefas como
criação e modificação dos bancos de dados do SQL do Azure. Além disso, você também pode usar
instruções Transact-SQL para realizar essas tarefas. As etapas a seguir
fornecem exemplos dessas instruções. Para obter mais informações sobre como usar
Transact-SQL com o banco de dados SQL, incluindo detalhes sobre quais comandos são
com suporte, consulte [Referência Transact-SQL (Banco de Dados SQL)][].

<h2><a id="Step3" name="Step3"> </a>Etapa 3: criar e gerenciar bancos de dados</h2>

Enquanto estiver conectado ao banco de dados **mestre**, você pode criar novos
bancos de dados no servidor e modificar ou remover bancos de dados existentes. As etapas
a seguir descrevem como realizar diversas tarefas comuns de gerenciamento de banco de dados
com o Management Studio. Para executar essas tarefas, verifique se você está conectado ao banco de dados
banco de dados **mestre** com o logon da entidade de segurança no nível do servidor que você
criou quando configurou o servidor.

Para abrir uma janela de consulta no Management Studio, abra a pasta de bancos de dados, expanda a pasta **Banco de dados do sistema**, clique com botão direito em **mestre** e, em seguida, clique em **Nova consulta**.

-   Use a instrução **CREATE DATABASE** para criar um novo banco de dados. Por
    obter mais informações, consulte [CRIAR BANCO DE DADOS (Banco de Dados SQL)][]. A instrução a seguir cria um novo banco de dados chamado
    **myTestDB** e especifica que se trata de um banco de dados Standard S0 Edition com um tamanho máximo padrão de 250GB.

        CRIAR BANCO DE DADOS myTestDB
        (EDITION='Standard',
         SERVICE_OBJECTIVE='S0');

Clique em **Executar** para executar a consulta.

-   Use a instrução **ALTERAR BANCO DE DADOS** para modificar um banco de dados existente,
    por exemplo, se você quiser alterar o nome e a edição
    do banco de dados. Para obter mais informações, consulte [ALTER DATABASE (Banco de Dados SQL)][]. A
    instrução a seguir modifica o banco de dados que você criou anteriormente na
    etapa para alterar a edição para Standard S1.

        ALTERAR BANCO DE DADOS myTestDB
        MODIFICAR
        (SERVICE_OBJECTIVE='S1');

-   Use a instrução **DROP DATABASE** para excluir um banco de dados existente.
    Para obter mais informações, consulte [DROP DATABASE (Banco de Dados SQL)][]. A instrução a seguir exclui o banco de dados **myTestDB**
    , mas não o remova agora porque você vai usá-lo para criar logons na próxima etapa.

        DROP DATABASE myTestBase;

-   O banco de dados mestre tem o modo de exibição **sys.databases** que você pode usar
    para exibir detalhes sobre todos os bancos de dados. Para exibir todos os bancos de dados existentes,
    execute a seguinte instrução:

        SELECT * FROM sys.databases;

-   No banco de dados SQL, a instrução **USAR** não dá suporte para alternar
    entre bancos de dados. Em vez disso, você precisa estabelecer uma conexão
    diretamente com o banco de dados de destino.

>[AZURE.NOTE] Muitas das instruções Transact-SQL que cria ou modifica um banco de dados devem ser executadas em seu próprio lote e não podem ser agrupadas com outras instruções Transact-SQL. Para obter mais informações, consulte as informações específicas de instrução disponíveis dos links listados acima.

<h2><a id="Step4" name="Step4"> </a>Etapa 4: criar e gerenciar logons</h2>

O banco de dados **mestre** mantém controle de logons e quais logons têm
permissão para criar bancos de dados ou outros logons. Gerencie logons ao
conectar-se ao banco de dados **mestre** com o logon da entidade de segurança no nível do servidor
que você criou quando configurou o servidor. Você pode usar as instruções
**CRIAR LOGON**, **ALTERAR LOGON** ou **REMOVER LOGON** para
executar consultas no banco de dados mestre que gerenciarão logons
em todo o servidor. Para obter mais informações, consulte [Gerenciando bancos de dados e logons no banco de dados SQL][] 


-   Use a instrução **CRIAR LOGON** para criar um novo logon no nível do servidor
    . Para obter mais informações, consulte [CREATE LOGIN (Banco de Dados SQL)][]. A instrução a seguir cria um novo logon chamado
    **login1**. Substitua **password1** pela senha de sua
    escolha.

        CREATE LOGIN login1 WITH password='password1';

-   Use a instrução **CRIAR USUÁRIO** para conceder permissões no nível do banco de dados
    . Todos os logons devem ser criados no banco de dados **mestre**,
    mas para um logon para se conectar a um banco de dados diferente, você
    deve conceder a ele permissões de nível de banco de dados usando a instrução **CRIAR USUÁRIO**
    nesse banco de dados. Para obter mais informações, consulte [CREATE USER (Banco de Dados SQL)][]. 

-   Para fornecer permissões login1
    para um banco de dados chamado **myTestDB**, execute as seguintes
    etapas:

 1.  Para atualizar o Gerenciador de Objetos e visualizar o banco de dados **myTestDB** que você acabou de criar, clique com o botão direito do mouse no nome do servidor no Gerenciador de Objetos e, em seguida, clique em **Atualizar**.  

     Se você fechou a conexão, você pode se reconectar, selecionando **Conectar ao Gerenciador de objetos** no menu arquivo. Repita as instruções na [Etapa 2: Conectar-se ao banco de dados SQL][] para se conectar ao banco de dados.

 2. Clique com o botão direito em **myTestDB** e selecione **Nova consulta**.

    3.  Execute a seguinte instrução em relação ao banco de dados myTestDB para
        criar um usuário de banco de dados denominado **login1User** que corresponda ao
        logon de nível de servidor **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Use o procedimento armazenado **sp\_addrolemember** para fornecer à conta do usuário
    o nível apropriado de permissões no banco de dados. Por
    obter mais informações, consulte [sp_addrolemember (Transact-SQL)][]. A instrução a seguir fornece ao **login1User**
    permissões somente leitura no banco de dados adicionando **login1User** à
    função **db\_datareader**.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Use a instrução **ALTERAR LOGON** para modificar um logon existente, por
    exemplo, se você quiser alterar a senha para o logon. Por
    obter mais informações, consulte [ALTERAR LOGON (Banco de Dados SQL)][]. A instrução **ALTERAR LOGON** deve ser executada em relação ao
    banco de dados **mestre**. Alterne para a janela de consulta que está conectada ao banco de dados. 

    A instrução a seguir modifica o logon **login1** para redefinir a senha.
    Substitua **newPassword** pela senha de sua escolha, e
    **oldPassword** pela senha atual para o logon.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Use a instrução **DROP LOGIN** para excluir um logon existente.
    Excluir um logon no nível do servidor também exclui contas de usuário
    de banco de dados associadas. Para mais informações,
    consulte [REMOVER BANCO DE DADOS (Banco de Dados SQL)][]. A instrução **REMOVER LOGON**
    deve ser executada em relação ao banco de dados **mestre**. A
    instrução a seguir exclui o logon **login1**.

        DROP LOGIN login1;

-   O banco de dados mestre tem o modo de exibição **sys.sql\_logins** que você pode usar
    para exibir logons. Para exibir todos os logons existentes, execute
    a instrução a seguir:

        SELECT * FROM sys.sql_logins;

<h2><a id="Step5" name="Step5"> </a>Etapa 5: Monitorar o Banco de Dados SQL usando Modos de Exibição de Gerenciamento Dinâmico</h2>

O banco de dados SQL dá suporte a várias exibições de gerenciamento dinâmico que você
pode usar para monitorar um banco de dados individual. A seguir estão alguns exemplos do
tipo de dados de monitor que podem ser recuperados por meio desses modos de exibição. Por
obter detalhes completos e mais exemplos de uso, consulte [Monitoramento de banco de dados SQL usando exibições de gerenciamento dinâmico][].

-   Consultar um modo de exibição de gerenciamento dinâmico requer permissões de **EXIBIR ESTADO DO BANCO DE DADOS**
    . Para conceder a permissão **EXIBIR ESTADO DE BANCO DE DADOS** a um
    usuário de banco de dados específico, conecte-se ao banco de dados que você deseja gerenciar
    com o logon do princípio de nível de servidor e execute a seguinte
    instrução no banco de dados:

        GRANT VIEW DATABASE STATE TO login1User;

-   Calcule o tamanho do banco de dados usando o modo de exibição **sys.dm\_db\_partition\_stats**
    padrão. A exibição **sys.dm\_db\_partition\_stats** retorna a página e
    informações de contagem de linhas para cada partição no banco de dados, que você
    pode usar para calcular o tamanho do banco de dados. A seguinte consulta retorna
    o tamanho do banco de dados em megabytes:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Use os modos de exibição **sys.dm\_exec\_connections** e **sys.dm\_exec\_sessions**
    para recuperar informações sobre conexões de usuário atuais e
    tarefas internas associadas ao banco de dados. A consulta a seguir
    retorna informações sobre a conexão atual:

        SELECIONAR
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        DE
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   Use o modo de exibição **sys.dm\_exec\_query\_stats** para recuperar as estatísticas de desempenho de agregação
    para planos de consulta em cache. A consulta a seguir
    retorna informações sobre as cinco principais consultas classificadas por tempo médio de
    CPU.

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        DE
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

* [Introdução ao Banco de Dados SQL][]   
* [Gerenciando bancos de dados e logons no banco de dados SQL][]   
* [Monitorando o Banco de Dados SQL Usando Exibições de Gerenciamento Dinâmico][]   
* [Referência Transact-SQL (Banco de Dados SQL)][]

  [Como usar o Banco de Dados SQL do Azure]: http://www.windowsazure.com/develop/net/how-to-guides/sql-azure/
  [Etapa 1: Obter o SQL Server 2014 Management Studio]: #Step1
  [Etapa 2: Conectar-se ao Banco de Dados SQL]: #Step2
  [Etapa 3: Criar e gerenciar bancos de dados]: #Step3
  [Etapa 4: Criar e gerenciar logons]: #Step4
  [Etapa 5: Monitorar o Banco de Dados SQL usando Modos de Exibição de Gerenciamento Dinâmico]:
    #Etapa5
  [Microsoft SQL Server 2014 Express]: http://www.microsoft.com/download/details.aspx?id=42299
  [Pacote de atualização cumulativa 5 do SQL Server 2014]: http://support2.microsoft.com/kb/3011055
  [Instalador do SSMS - selecione o tipo de instalação]: /media/installer_installation_type.png
  [Instalador do SSMS - selecione os recursos]: /media/installer_feature_selection.png
  [Instalador do SSMS - instalação completa]: /media/installer_completed.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
  [Obter o nome do servidor de Banco de Dados SQL por meio do Portal de Gerenciamento]: /media/portal_get_database_name.png
  [Conectar-se ao SSMS]: /media/ssms_connect.png
  [Conectar-se ao SSMS -- propriedades]: /media/ssms_connect_properties.png
  [Referência Transact-SQL (Banco de Dados SQL)]: http://msdn.microsoft.com/library/bb510741(v=sql.120).aspx
  [CREATE DATABASE (Banco de Dados SQL)]: https://msdn.microsoft.com/library/dn268335.aspx
  [ALTER DATABASE (Banco de Dados SQL)]: https://msdn.microsoft.com/library/ms174269.aspx
  [DROP DATABASE (Banco de Dados SQL)]: https://msdn.microsoft.com/library/ms178613.aspx
  [Gerenciando bancos de dados e logons no banco de dados SQL]: http://msdn.microsoft.com/library/windowsazure/ee336235.aspx
  [CREATE LOGIN (Banco de Dados SQL)]: https://msdn.microsoft.com/library/ms189751.aspx
  [CREATE USER (Banco de Dados SQL)]: https://msdn.microsoft.com/library/ms173463.aspx
  [sp_addrolemember (Transact-SQL)]: http://msdn.microsoft.com/library/ms187750.aspx
  [ALTER LOGIN (Banco de Dados SQL)]: https://msdn.microsoft.com/library/ms189828.aspx
  [Monitorando o Banco de Dados SQL Usando Exibições de Gerenciamento Dinâmico]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
  [Introdução ao Banco de Dados SQL]: http://azure.microsoft.com/services/sql-database/
 

<!--HONumber=47-->
