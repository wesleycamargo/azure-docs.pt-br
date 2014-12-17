
# Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio 

Você pode usar o Portal de Gerenciamento de Banco de Dados do Azure SQL ou o aplicativo cliente do SQL Server Management Studio (SSMS) para administrar suas assinaturas de banco de dados SQL e criar e gerenciar servidores lógicos e bancos de dados associados. O guia a seguir descreve como usar o Management Studio para gerenciar bancos de dados e servidores lógicos do banco de dados SQL.

<div class="dev-callout-new-collapsed">
<strong>Observação <span>Click to collapse</span></strong>
<div class="dev-callout-content">
<p>Você pode usar o SQL Server 2014, SQL Server 2012 ou o SQL Server 2008 R2 versão do SSMS. Não há suporte para versões anteriores.</p>
</div>
</div>

Este tópico inclui as seguintes etapas:

-   [Etapa 1: Obter o SQL Server Management Studio][]
-   [Etapa 2: Conectar-se ao Banco de Dados SQL][]
-   [Etapa 3: criar e gerenciar bancos de dados][]
-   [Etapa 4: criar e gerenciar logons][]
-   [Etapa 5: Monitorar o Banco de Dados SQL usando Modos de Exibição de Gerenciamento Dinâmico][]

<h2><a id="Step1" name="Step1"> </a>Etapa 1: Obter o Management Studio</h2>

O Management Studio é um ambiente integrado para
gerenciando de bancos de dados SQL. Ao gerenciar 
bancos de dados no Azure, você pode usar o aplicativo Management Studio instalado com
o SQL Server ou baixar a versão gratuita do SQL Server 2012 Management Studio Express (SSMSE). As etapas a seguir descrevem
como instalar o SSMSE.

1.  Na página [Microsoft SQL Server 2012 Express][], selecione a versão x86 do Management Studio se você estiver executando um sistema operacional de 32 bits, ou x64 se você estiver executando um sistema operacional de 64 bits. Clique em **Baixar**, e quando solicitado, execute a instalação.

2.  Clique **Instalação independente do novo SQL Server ou adicionar recursos a uma
    instalação existente** e clique em **OK**.

3.  Aceite os termos de licença e clique em **Avançar**.

4. Clique em **instalar** para instalar os arquivos necessários para a instalação do SQL Server.

N5.  Na tela **Seleção de recursos**, **Ferramentas de gerenciamento-
    Básico** é pré-selecionado. Isso ocorre porque você está executando o instalador do Management Studio. Se você estiver executando a instalação para todos os do SQL Server Express, escolha a opção ** Ferramentas de gerenciamento - Básico** e clique em **próximo**.

6.  Na tela de **Relatório de erros**, você pode optar por enviar
    relatórios de erros à Microsoft. Isso não é necessário usar SSMSE. Clique em
    **próximo** para iniciar a instalação.

7.  Quando a instalação for concluída, você verá a página **Concluído**
. Clique em **fechar** 


<h2><a id="Step2" name="Step2"> </a>Etapa 2: Conectar-se ao Banco de Dados SQL</h2>

Conectar ao banco de dados SQL requer que você saiba o nome do servidor no Azure. Talvez seja necessário entrar no portal para obter essa informação.

1.  Entre no [Portal de Gerenciamento do Azure][].

2.  No painel esquerdo de navegação, clique em **Bancos de Dados SQL**.

3.  Na página inicial do Bancos de Dados SQL, clique em **SERVIDORES** na parte superior da página para listar todos os servidores associados a sua assinatura. Localize o nome do servidor ao qual você deseja se conectar e copie-o na área de transferência.

	Em seguida, configure o firewall do banco de dados SQL para
permitir conexões do computador local. Você pode fazer isso adicionando o seu endereço IP de computadores locais à lista de exceção do firewall.

1.  Na página inicial do bancos de dados SQL, clique em **SERVIDORES** e, em seguida, clique no servidor ao qual você deseja se conectar.

2.  Clique em **Configurar** na parte superior da página.

3.  Copie o endereço IP em endereço de IP de cliente atual.

4.  Na página Configuração, **Endereços IP permitidos** inclui três caixas nas quais você pode especificar um nome de regra e um intervalo de endereços IP como valores inicial e final. Para o nome de uma regra, você pode inserir o nome do seu computador. Para o intervalo de início e de término, cole no endereço IP do seu computador em ambas as caixas e clique na caixa de seleção que aparece.

	O nome da regra deve ser exclusivo. Se esse for seu computador de desenvolvimento, você pode inserir o endereço IP na caixa de início do intervalo IP e caixa de término do intervalo IP. Caso contrário, talvez seja necessário inserir um amplo intervalo de endereços IP para acomodar conexões de outros computadores em sua organização.
 
5. Clique em **SALVAR** na parte inferior da página.

    **Observação:** Pode haver até cinco minutos de atraso para que as alterações
    das configurações de firewall sejam efetivadas.

	Agora você está pronto para se conectar ao banco de dados SQL usando o Management Studio.

1.  Na barra de tarefas, clique em **Iniciar**, aponte para **Todos os programas**, aponte para
    **Microsoft SQL Server 2012**e, então, clique em **SQL Server
    Management Studio**.

2.  Em **Conectar-se ao servidor**, especifique o nome de servidor totalmente qualificado
    como *serverName*.database.windows.net. No Azure, o nome do servidor é uma cadeia de caracteres gerada automaticamente e composta por caracteres alfanuméricos.

3.  Selecione **Autenticação do SQL Server**.

4.  Na caixa **Logon**, insira o logon do administrador do SQL Server que você
    especificou no portal durante a criação de seu servidor no formato
    *login*@*yourServerName*.

5.  Na caixa **Senha**, digite a senha que você especificou no
    portal durante a criação de seu servidor.

8.  Clique em **Conectar** para estabelecer a conexão.

No Azure, cada servidor lógico do banco de dados SQL é uma abstração que define um agrupamento de bancos de dados. O local físico de cada banco de dados pode estar em qualquer computador no data center. 

Nas versões anteriores, você precisava se conectar diretamente ao **mestre** ao configurar a conexão no Management Studio. Essa etapa não é necessária. As conexões agora funcionarão com base no nome do servidor, tipo de autenticação e as credenciais de administrador.

Muitos dos assistentes SSMS que você pode usar para tarefas, como
criar e modificar logons e bancos de dados em um banco de dados do SQL Server, não
estão disponíveis para bancos de dados do SQL no Azure, portanto, você precisará utilizar
instruções Transact-SQL para realizar essas tarefas. As etapas a seguir
fornecem exemplos dessas instruções. Para obter mais informações sobre como usar
Transact-SQL com o banco de dados SQL, incluindo detalhes sobre quais comandos são
com suporte, consulte [Referências do Transact-SQL (banco de dados SQL)][].

<h2><a id="Step3" name="Step3"> </a>Etapa 3: Criar e gerenciar bancos de dados</h2>

Enquanto estiver conectado ao banco de dados **mestre**, você pode criar novos
bancos de dados no servidor e modificar ou remover bancos de dados existentes. As etapas
a seguir descrevem como realizar diversas tarefas comuns de gerenciamento de banco de dados
com o Management Studio. Para executar essas tarefas, verifique se você está conectado ao
banco de dados **mestre** com o logon da entidade de segurança no nível do servidor que você
criou quando configurou o servidor.

Para abrir uma janela de consulta no Management Studio, abra a pasta de bancos de dados, expanda a pasta **Banco de dados do sistema**, clique com botão direito do mouse em **mestre**e, em seguida, clique em **Nova consulta**.

-   Use a instrução **CRIAR BANCO DE DADOS** para criar um novo banco de dados. Para
    obter mais informações, consulte [CRIAR BANCO DE DADOS (Banco de Dados SQL)][]. A instrução a seguir cria um novo banco de dados chamado
    **myTestDB** e especifica que se trata de um banco de dados Web Edition
    com um tamanho máximo de 1 GB.

        CREATE DATABASE myTestDB
        (MAXSIZE=1GB,
         EDITION='web');

Clique em **Executar** para executar a consulta.

-   Use a instrução **ALTERAR BANCO DE DADOS** para modificar um banco de dados existente,
    por exemplo, se você quiser alterar o nome, o tamanho máximo ou a edição
    (comercial ou web) do banco de dados. Para obter mais informações, consulte [ALTER DATABASE (Banco de Dados SQL)][]. A
    instrução a seguir modifica o banco de dados que você criou anteriormente na
    etapa para alterar o tamanho máximo para 5 GB.

        ALTER DATABASE myTestDB
        MODIFY
        (MAXSIZE=5GB,
         EDITION='web');

-   Use a instrução **REMOVER BANCO DE DADOS** para excluir um banco de dados existente.
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

<div class="dev-callout-new">
 <strong>Observação <span>Click to collapse</span></strong>
 <div class="dev-callout-content">
   <p>Muitas das instruções Transact-SQL que criam ou modificam um
banco de dados devem ser executadas em seu próprio lote e não podem ser agrupadas com
outras instruções Transact-SQL. Para obter mais informações, consulte as informações específicas de instrução disponíveis dos links listados acima.</p>
</div>
</div>

<h2><a id="Step4" name="Step4"> </a>Etapa 4: Criar e gerenciar logons</h2>

O banco de dados **mestre** mantém controle de logons e quais logons têm
permissão para criar bancos de dados ou outros logons. Gerencie logons ao
conectar-se ao banco de dados **mestre** com o logon da entidade de segurança no nível do servidor
que você criou quando configurou o servidor. Você pode usar as instruções
**CRIAR LOGON**, **ALTERAR LOGON** ou **REMOVER LOGON** para
executar consultas no banco de dados mestre que gerenciarão logons
em todo o servidor. Para obter mais informações, consulte [gerenciamento de bancos de dados e logons no banco de dados SQL][] 


-   Use a instrução **CRIAR LOGON** para criar um novo logon no nível do servidor
. Para obter mais informações, consulte [CREATE LOGIN (Banco de Dados SQL)][]. A instrução a seguir cria um novo logon chamado
    **login1**. Substitua **password1** pela senha de sua
    escolha.

        CREATE LOGIN login1 WITH password='password1';

-   Use a instrução **CRIAR USUÁRIO** para conceder permissões no nível do
    banco de dados. Todos os logons devem ser criados no banco de dados **mestre**,
    mas para um logon para se conectar a um banco de dados diferente, você
    deve conceder a ele permissões de nível de banco de dados usando a instrução **CRIAR USUÁRIO**
    nesse banco de dados. Para obter mais informações, consulte [CREATE USER (Banco de Dados SQL)][]. 

-   Para fornecer permissões login1
    para um banco de dados chamado **myTestDB**, execute as seguintes
    etapas:

 1.  Para atualizar o Gerenciador de Objetos para exibir o banco de dados **myTestDB** que você acabou de criar, clique com o botão direito do mouse no nome do servidor no Gerenciador de Objetos e, em seguida, clique em **Atualizar**.  

     Se você fechou a conexão, você pode se reconectar selecionando **Conectar Pesquisador de Objetos** no menu Arquivo. Repita as instruções na [Etapa 2: Conectar-se ao banco de dados SQL][] para se conectar ao banco de dados.

 2. Clique com o botão direito do mouse em banco de dados **myTestDB** e selecione **Nova consulta**.

    3.  Execute a seguinte instrução em relação ao banco de dados myTestDB para
        criar um usuário de banco de dados denominado **login1User** que corresponda ao
        logon de nível de servidor **login1**.

            CREATE USER login1User FROM LOGIN login1;

-   Use o procedimento armazenado **sp\_addrolemember** para fornecer à conta do usuário
    o nível apropriado de permissões no banco de dados. Para
    obter mais informações, consulte [sp_addrolemember (Transact-SQL)][]. A instrução a seguir fornece ao **login1User**
    permissões somente leitura no banco de dados adicionando **login1User** à
    função **db\_datareader**.

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   Use a instrução **ALTERAR LOGON** para modificar um logon existente, por
    exemplo, se você quiser alterar a senha para o logon. Para
    obter mais informações, consulte [ALTERAR LOGON (Banco de Dados SQL)][]. A instrução **ALTERAR LOGON** deve ser executada em relação ao
    banco de dados **mestre**. Alterne para a janela de consulta que está conectada ao banco de dados. 

    A instrução a seguir modifica o logon **login1** para redefinir a senha.
    Substitua **newPassword** pela senha de sua escolha, e
    **oldPassword** pela senha atual para o logon.

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   Use a instrução **REMOVER LOGON** para excluir um logon existente.
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
tipo de dados de monitor que podem ser recuperados por meio desses modos de exibição. Para
obter detalhes completos e mais exemplos de uso, consulte [Monitoramento de banco de dados SQL usando exibições de gerenciamento dinâmico][].

-   Consultar um modo de exibição de gerenciamento dinâmico requer permissões de **EXIBIR ESTADO DO BANCO DE DADOS**
. Para conceder a permissão **EXIBIR ESTADO DE BANCO DE DADOS** a um
    usuário de banco de dados específico, conecte-se ao banco de dados que você deseja gerenciar
    com o logon do princípio de nível de servidor e execute a seguinte
    instrução no banco de dados:

        GRANT VIEW DATABASE STATE TO login1User;

-   Calcule o tamanho do banco de dados usando o modo de exibição **sys.dm\_db\_partition\_stats**
. A exibição **sys.dm\_db\_partition\_stats** retorna a página e
    informações de contagem de linhas para cada partição no banco de dados, que você
    pode usar para calcular o tamanho do banco de dados. A seguinte consulta retorna
    o tamanho do banco de dados em megabytes:

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   Use os modos de exibição **sys.dm\_exec\_connections** e **sys.dm\_exec\_sessions**
    para recuperar informações sobre conexões de usuário atuais e
    tarefas internas associadas ao banco de dados. A consulta a seguir
    retorna informações sobre a conexão atual:

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

-   Use o modo de exibição **sys.dm\_exec\_query\_stats** para recuperar as estatísticas de desempenho de agregação
    para planos de consulta em cache. A consulta a seguir
    retorna informações sobre as cinco principais consultas classificadas por tempo médio de
    CPU.

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
* [Adicionando usuários ao seu Banco de Dados SQL][]   
* [Referência Transact-SQL (Banco de Dados SQL)][]

  [Como usar o Banco de Dados SQL do Azure]: http://www.windowsazure.com/pt-br/develop/net/how-to-guides/sql-azure/
  [Etapa 1: Obter o SQL Server Management Studio]: #Step1
  [Etapa 2: Conectar-se ao Banco de Dados SQL]: #Step2
  [Etapa 3: criar e gerenciar bancos de dados]: #Step3
  [Etapa 4: criar e gerenciar logons]: #Step4
  [Etapa 5: Monitorar o Banco de Dados SQL usando Modos de Exibição de Gerenciamento Dinâmico]:
    #Step5
  [Microsoft SQL Server 2012 Express]: http://www.microsoft.com/pt-br/download/details.aspx?id=29062
  [Instalador do SSMS - selecione o tipo de instalação]: /media/installer_installation_type.png
  [Instalador do SSMS - Selecione os recursos]: /media/installer_feature_selection.png
  [Instalador do SSMS - Instalação completa]: /media/installer_completed.png
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com/
  [Obter o nome do servidor de Banco de Dados SQL do Portal de Gerenciamento]: /media/portal_get_database_name.png
  [Conectar-se ao SSMS]: /media/ssms_connect.png
  [Conectar-se ao SSMS ― propriedades]: /media/ssms_connect_properties.png
  [Referência Transact-SQL (Banco de Dados SQL)]: http://msdn.microsoft.com/pt-br/library/bb510741(v=sql.120).aspx
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
