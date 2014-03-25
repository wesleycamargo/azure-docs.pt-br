
# Gerenciando o Banco de Dados SQL do Windows Azure usando o SQL Server Management Studio 

Você pode usar o Portal de gerenciamento de banco de dados do Windows Azure SQL ou o aplicativo de cliente do SQL Server Management Studio (SSMS) para administrar suas assinaturas de banco de dados SQL e criar e gerenciar servidores lógicos e bancos de dados está associada. O guia a seguir descreve como usar o Management Studio para gerenciar bancos de dados e servidores lógicos do banco de dados SQL. Para obter informações sobre como usar conexões de banco de dados SQL no código do aplicativo, consulte [como usar o banco de dados do Windows Azure SQL][]

<div class="dev-callout-new-collapsed">
<strong>Nota <span>clique para recolher</span></strong>
<div class="dev-callout-content">
<p>Você pode usar o SQL Server 2012 ou a versão do SQL Server 2008 R2 do Management Studio. Não há suporte para versões anteriores.</p>
</div>
</div>

Esta tarefa inclui as seguintes etapas:

-   [Etapa 1: Obter o SQL Server Management Studio][]
-   [Etapa 2: Conectar-se ao banco de dados SQL][]
-   [Etapa 3: Criar e gerenciar os bancos de dados][]
-   [Etapa 4: Criar e gerenciar os logons][]
-   [Etapa 5: Monitorar o Banco de Dados SQL usando Dynamic Management Views][]

<h2><a id="Step1" name="Step1"> </a>Etapa 1: Obter o Management Studio</h2>

O Management Studio é um ambiente integrado para gerenciar bancos de dados SQL. Ao gerenciar bancos de dados no Windows Azure, você pode usar o aplicativo do Management Studio instalado com o SQL Server ou baixar a versão gratuita do SQL Server 2012 Management Studio Express (SSMSE). As etapas a seguir descrevem como instalar SSMSE.

1.  Sobre o [Microsoft SQL Server 2012 Express][] página, selecione o x86 versão do Management Studio se você estiver executando um sistema operacional de 32 bits ou x64, se você estiver executando um sistema operacional de 64 bits. Clique em **Download**, e quando solicitado, execute a instalação.

2.  Clique em **instalação autônoma novo SQL Server ou adicionar recursos a uma instalação existente do** e clique em **OK**

3.  Aceite os termos de licença e clique em **OK**

4. Clique em **instalar** para instalar os arquivos necessários para a instalação do SQL Server.

5.  Sobre o **seleção de recursos** tela, **as ferramentas de gerenciamento - Basic** é pré-selecionado. Isso ocorre porque você está executando o instalador do Management Studio. Se você estiver executando a instalação para todos os do SQL Server Express, escolher o **as ferramentas de gerenciamento - Basic** opção e clique em **próximo**

6.  Sobre o **relatório de erros** tela, você pode optar por enviar relatórios de erros para a Microsoft. Isso não é necessário usar SSMSE. Clique em
    **próximo** para iniciar a instalação.

7.  Quando a instalação for concluída, você verá o **completa**
    página. Clique em **fechar** 


<h2><a id="Step2" name="Step2"> </a>Etapa 2: Conectar-se ao banco de dados SQL</h2>

Conectando ao banco de dados SQL requer que você saiba o nome do servidor no Windows Azure. Talvez seja necessário entrar no portal para obter essa informação.

1.  Entre no [Portal de Gerenciamento do Windows Azure][].

2.  No painel esquerdo de navegação, clique em **Bancos de Dados SQL**.

3.  Na home page do Bancos de Dados SQL, clique em **SERVIDORES** na parte superior da página para listar todos os servidores associados a sua assinatura. Localize o nome do servidor ao qual você deseja se conectar e copiá-lo para a área de transferência.

	Em seguida, configure o firewall de banco de dados SQL para permitir conexões do computador local. Você pode fazer isso adicionando o seu endereço IP de computadores locais à lista de exceção do firewall.

1.  Na home page do bancos de dados SQL, clique em **SERVIDORES** e, em seguida, clique no servidor ao qual você deseja se conectar.

2.  Clique em **Configurar** na parte superior da página.

3.  Copie o endereço IP em endereço de IP de cliente atual.

4.  Na página Configure, **permitido endereços IP** inclui três caixas onde você pode especificar um nome de regra e um intervalo de endereços IP como valores inicial e final. Para o nome de uma regra, você pode inserir o nome do seu computador. Para o intervalo de início e de término, colar no endereço IP do seu computador em ambas as caixas e clique na caixa de seleção que aparece.

	O nome da regra deve ser exclusivo. Se esse for seu computador de desenvolvimento, você pode inserir o endereço IP na caixa de início do intervalo IP e caixa de término do intervalo IP. Caso contrário, talvez seja necessário inserir um amplo intervalo de endereços IP para acomodar conexões de outros computadores em sua organização.
 
5. Na parte inferior da página, clique em **SALVAR**.

    **Observação:** pode haver um atraso de até cinco minutos para as alterações nas configurações de firewall sejam efetivadas.

	Agora você está pronto para se conectar ao banco de dados SQL usando o Management Studio.

1.  Na barra de tarefas, clique em **iniciar**, aponte para **todos os programas**, aponte para
    **Microsoft SQL Server 2012**e, em seguida, clique em **SQL Server Management Studio**

2.  Em **Conectar-se ao servidor**, especifique o nome do servidor totalmente qualificado como *serverName*. database.windows.net. No Windows Azure, o nome do servidor é uma sequência de gerado automaticamente composta por caracteres alfanuméricos.

3.  Selecione **Autenticação do SQL Server**.

4.  Em **Login**, digite o logon de administrador do SQL Server que você especificou no portal durante a criação de seu servidor no formato
    *login*@*nomedoservidor*

5.  Em **Senha**, digite a senha que você especificou no portal durante a criação de seu servidor.

8.  Clique em **Conectar** para estabelecer a conexão.

No Windows Azure, cada servidor lógico do banco de dados SQL é uma abstração que define um agrupamento de bancos de dados. O local físico de cada banco de dados pode estar em qualquer computador no data center. 

Nas versões anteriores, você precisava se conectar diretamente ao **mestre** ao configurar a conexão no Management Studio. Essa etapa não é necessária. Conexões agora funcionará com base no nome do servidor, tipo de autenticação e as credenciais de administrador.

Muitos dos assistentes SSMS que você pode usar para tarefas como criar e modificar logons e bancos de dados em um banco de dados do SQL Server não estão disponíveis para bancos de dados SQL no Windows Azure, portanto você precisará utilizar instruções Transact-SQL para executar essas tarefas. As etapas a seguir fornecem exemplos dessas instruções. Para obter mais informações sobre como usar o Transact-SQL com o banco de dados SQL, incluindo detalhes sobre quais comandos são suportados, consulte [referência Transact-SQL (SQL Database)][]

<h2><a id="Step3" name="Step3"> </a>Etapa 3: Criar e gerenciar os bancos de dados</h2>

Enquanto conectado a **mestre** banco de dados, você pode criar novos bancos de dados no servidor e modificar ou soltar existentes bancos de dados. As etapas a seguir descrevem como realizar várias tarefas de gerenciamento de banco de dados comum através do Management Studio. Para executar essas tarefas, verifique se você está conectado a
**mestre** banco de dados com o logon de nível de servidor principal que você criou quando configurou o servidor.

Para abrir uma janela de consulta no Management Studio, abra a pasta de bancos de dados, clique com botão direito em **mestre**e, em seguida, clique em **nova consulta**

Clique em **Execute** para executar a consulta.

-Use o **criar banco de dados** instrução para criar um novo banco de dados. Para obter mais informações, consulte [CREATE DATABASE (Banco de Dados SQL)][]. A instrução a seguir cria um novo banco de dados chamado
    **myTestDB** e especifica que se trata de um banco de dados da Web Edition com um tamanho máximo de 1 GB.

        CREATE DATABASE myTestDB
        (MAXSIZE=1GB,
         EDITION='web');

- Use o **ALTER DATABASE** a instrução para modificar um banco de dados existente, por exemplo, se você desejar alterar o nome, o tamanho máximo ou a edição (comercial ou web) do banco de dados. Para obter mais informações, consulte [ALTER DATABASE (Banco de Dados SQL)][]. A instrução a seguir modifica o banco de dados criado na etapa anterior para alterar o tamanho máximo de 5 GB.

        ALTER DATABASE myTestDB
        MODIFY
        (MAXSIZE=5GB,
         EDITION='web');

- Use a instrução **DROP DATABASE** para excluir um banco de dados existente.
    Para obter mais informações, consulte [DROP DATABASE (Banco de Dados SQL)][]. A instrução a seguir exclui o **myTestDB**
    de banco de dados, mas não solte-o agora porque você usará-lo criar logons na próxima etapa.

        DROP DATABASE myTestBase;

- O banco de dados mestre tem o modo de exibição **sys.databases** que você pode usar para exibir detalhes sobre todos os bancos de dados. Para exibir todos os bancos de dados existentes, execute a seguinte instrução:

        SELECT * FROM sys.databases;

- No banco de dados SQL, a declaração **USE** não oferece suporte para alternar entre bancos de dados. Em vez disso, você precisa estabelecer uma conexão diretamente com o banco de dados de destino.

<div class="dev-callout-new">
 <strong>Nota <span>clique para recolher</span></strong>
 <div class="dev-callout-content">
   <p>Muitas das instruções Transact-SQL que cria ou modifica um banco de dados devem ser executadas em seu próprio lote e não podem ser agrupadas com outras instruções Transact-SQL. Para obter mais informações, consulte as informações específicas de instrução disponíveis dos links listados acima.</p>
</div>
</div>

<h2><a id="Step4" name="Step4"> </a>Etapa 4: Criar e gerenciar os logons</h2>

O banco de dados mestre controla logons e quais logons tem permissão para criar bancos de dados ou outros logons. Gerencie logons conectando-se ao banco de dados **mestre** com o login de nível de servidor principal que você criou quando configurou o servidor. Você pode usar o
**CREATE LOGIN**, **ALTER LOGIN**, ou **DROP LOGIN** instruções para executar consultas no banco de dados mestre que gerenciarão logons em todo o servidor. Para obter mais informações, consulte [gerenciamento de bancos de dados e logons no banco de dados SQL][] 


- Use a declaração **CREATE LOGIN** para criar um novo login em nível de servidor. Para obter mais informações, consulte [CREATE LOGIN (Banco de Dados SQL)][]. A instrução a seguir cria um novo logon chamado **login1** Substituir **password1** com a senha de sua escolha.

        CREATE LOGIN login1 WITH password='password1';

-Use o **CREATE USER** banco de dados-nível de instrução para conceder permissões. Todos os logons devem ser criados no **mestre** de banco de dados, mas para um logon para se conectar a um banco de dados diferente, você deve conceder a ele nível banco de dados permissões usando o **CREATE USER**
    instrução nesse banco de dados. Para obter mais informações, consulte [CREATE USER (Banco de Dados SQL)][]. 

-Para dar login1
    permissões para um banco de dados chamado **myTestDB**, execute as seguintes etapas:

    1.  Atualizar o Gerenciador de objeto para exibir o **myTestDB** banco de dados que você acabou de criar. Ela deve aparecer abaixo do **bancos de dados do sistema** pasta contém **mestre**  

     Se você fechou a conexão, você pode se reconectar, selecionando **conectar Object Explorer** no menu arquivo. Repita as instruções [etapa 2: conectar-se ao banco de dados SQL][] para se conectar ao banco de dados.

    2. Clique com o botão **myTestDB** banco de dados e selecione **nova consulta**

    3.  Execute a seguinte instrução contra o myTestDB banco de dados para criar um usuário de banco de dados chamado **login1User** que corresponde do login em nível de servidor **login1**

            CREATE USER login1User FROM LOGIN login1;

-   Use o procedimento **sp\_addrolemember** armazenado para oferecer à conta do usuário o nível apropriado de permissões no banco de dados. Para obter mais informações, consulte [sp_addrolemember (Transact-SQL)][]. A declaração a seguir dá ao **login1User** permissões de apenas leitura ao banco de dados adicionando **login1User** à regra **db\_datareader**.

        exec sp_addrolemember 'db_datareader', 'login1User';    

- Use o **ALTER LOGIN** a instrução para modificar um logon existente, por exemplo, se você deseja alterar a senha para o logon. Para obter mais informações, consulte [ALTER LOGIN (Banco de Dados SQL)][]. A instrução **ALTER LOGIN** deve ser executada no banco de dados **mestre**. Alterne para a janela de consulta que está conectada ao banco de dados. 

    A instrução a seguir modifica o **login1** login para redefinir a senha.
    Substituir **newPassword** com a senha de sua escolha, e
    **oldPassword** com a senha atual para o logon.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

- Use a instrução **DROP LOGIN** para excluir um login existente.
    Excluir um logon no nível do servidor também exclui quaisquer contas de usuário do banco de dados associado. Para obter mais informações, consulte [DROP DATABASE (Banco de Dados SQL)][]. A instrução **DROP LOGIN** deve ser executada no banco de dados **mestre**. A instrução a seguir exclui o **login1** login.

        DROP LOGIN login1;

-   O banco de dados mestre possui o modo de exibição **sys.sql\_logins** que pode ser usado para visualizar os logons. Para exibir todos os logons existentes, execute a seguinte instrução:

        SELECT * FROM sys.sql_logins;

<h2><a id="Step5" name="Step5"> </a>Etapa 5: Monitorar o Banco de Dados SQL usando Dynamic Management Views</h2>

O Banco de dados SQL oferece suporte a vários modos de exibição de gerenciamento dinâmico que você pode usar para monitorar um banco de dados individual. Abaixo estão alguns exemplos do tipo de dados de monitor que podem ser recuperados por meio desses modos de exibição. Para obter detalhes completos e mais exemplos de uso, consulte [monitoramento de banco de dados SQL usando exibições de gerenciamento dinâmico][]

-Consultar um modo de exibição de gerenciamento dinâmico requer permissões **VIEW DATABASE STATE**. Para conceder a permissão **VIEW DATABASE STATE** a um usuário de banco de dados específico, conecte-se ao banco de dados que você deseja gerenciar com o login do princípio de nível de servidor e execute a seguinte instrução no banco de dados:

        GRANT VIEW DATABASE STATE TO login1User;

-   Calcule o tamanho do banco de dados usando o modo de exibição **sys.dm\_db\_partition\_stats**. O modo de exibição **sys.dm\_db\_partition\_stats** retorna informações da página e sobre a contagem de linhas para cada partição do banco de dados, que você pode usar para calcular o tamanho do banco de dados. A consulta a seguir retorna o tamanho do seu banco de dados em megabytes:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Use os modos de visualização **sys.dm\_exec\_connections** e **sys.dm\_exec\_sessions** para recuperar informações sobre as conexões do usuário atual e tarefas internas associadas ao banco de dados. A consulta a seguir retorna informações sobre a conexão atual:

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

-   Use o modo de exibição **sys.dm\_exec\_query\_stats** para recuperar estatísticas de desempenho agregado para cada plano de consulta. A consulta a seguir retorna informações sobre as consultas de cinco principais classificados por tempo médio de CPU.

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

* [Introdução ao Banco de Dados SQL][]   
* [Gerenciamento de bancos de dados e logons no banco de dados SQL][]   
* [Monitorando o Banco de Dados SQL usando Modos de Exibição de Gerenciamento Dinâmico][]   
* [Modelo de provisionamento de Banco de Dados SQL][]   
* [Adicionar usuários ao seu Banco de Dados SQL][]   
* [Referência Transact-SQL (Banco de Dados SQL)][]

  [Como usar o Banco de Dados SQL do Windows Azure]: http://www.windowsazure.com/pt-br/develop/net/how-to-guides/sql-azure/
  [Etapa 1: Obter o SQL Server Management Studio]: #Step1
  [Etapa 2: Conectar-se ao Banco de Dados SQL]: #Step2
  [Etapa 3: Criar e gerenciar os bancos de dados]: #Step3
  [Etapa 4: Criar e gerenciar os logons]: #Step4
  [Etapa 5: Monitorar o Banco de Dados SQL usando Dynamic Management Views]:
    #Step5
  [Microsoft SQL Server 2012 Express]: http://www.microsoft.com/pt-br/download/details.aspx?id=29062
  [Instalador do SSMS - selecione o tipo de instalação]: /media/installer_installation_type.png
  [Instalador do SSMS - selecione os recursos]: /media/installer_feature_selection.png
  [Instalador do SSMS - instalação completa]: /media/installer_completed.png
  [Portal de Gerenciamento do Windows Azure]: http://manage.windowsazure.com/
  [Obter o nome do servidor de Banco de Dados SQL do Portal de gerenciamento]: /media/portal_get_database_name.png
  [Connect-se ao SSMS]: /media/ssms_connect.png
  [Conecte-se ao SSMS -- propriedades]: /media/ssms_connect_properties.png
  [Referência Transact-SQL (Banco de Dados SQL)]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336281.aspx
  [CREATE DATABASE (Banco de Dados SQL)]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336274.aspx
  [ALTER DATABASE (Banco de Dados SQL)]: http://msdn.microsoft.com/pt-br/library/windowsazure/ff394109.aspx
  [DROP DATABASE (Banco de Dados SQL)]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336259.aspx
  [Gerenciamento de bancos de dados e logons no banco de dados SQL]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336235.aspx
  [CREATE LOGIN (Banco de Dados SQL)]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336268.aspx
  [CREATE USER (Banco de Dados SQL)]: http://msdn.microsoft.com/pt-br/library/ee336277.aspx
  [sp_addrolemember (Transact-SQL)]: http://msdn.microsoft.com/pt-br/library/ms187750.aspx
  [ALTER LOGIN (Banco de Dados SQL)]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336254.aspx
  [Monitorando o Banco de Dados SQL usando Modos de Exibição de Gerenciamento Dinâmico]: http://msdn.microsoft.com/pt-br/library/windowsazure/ff394114.aspx
  [Introdução ao Banco de Dados SQL]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336230.aspx
  [Modelo de provisionamento de Banco de Dados SQL]: http://msdn.microsoft.com/pt-br/library/ee336227.aspx
  [Adicionar usuários ao seu Banco de Dados SQL]: http://blogs.msdn.com/b/sqlazure/archive/2010/06/21/10028038.aspx

