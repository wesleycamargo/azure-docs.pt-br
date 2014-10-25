<properties linkid="manage-services-getting-started-with-sqldbs" urlDisplayName="How to create and provision" pageTitle="Getting started with SQL Database - Azure" metaKeywords="" description="Get started creating and managing SQL Databases in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Database" authors="loclar"  solutions="" writer="" manager="jeffreyg" editor="tysonn"  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="loclar"></tags>

# Introdução ao banco de dados SQL do Microsoft Azure

Neste tutorial você aprenderá os fundamentos da administração do Microsoft Azure usando o Portal de Gerenciamento do Azure. Se você for novo na administração de banco de dados, poderá seguir estas lições para aprender habilidades essenciais em cerca de 30 minutos.

Este tutorial não pressupõe nenhuma experiência anterior com o SQL Server ou o Banco de Dados SQL do Azure. Após a conclusão deste tutorial, você terá um exemplo de banco de dados no Azure e um entendimento de como executar tarefas básicas de administração usando o Portal de Gerenciamento.

Você irá criar e provisionar um banco de dados de exemplo na plataforma do Azure e um sistema de consulta e dados de usuário usando o Excel.

## Sumário

-   [Etapa 1: Criar uma conta do Microsoft Azure][Etapa 1: Criar uma conta do Microsoft Azure]
-   [Etapa 2: Conectar-se ao Azure e criar um banco de dados][Etapa 1: Criar uma conta do Microsoft Azure]
-   [Etapa 3: Configurar o firewall][Etapa 3: Configurar o firewall]
-   [Etapa 4: Adicionar dados e um esquema usando um script Transact-SQL][Etapa 4: Adicionar dados e um esquema usando um script Transact-SQL]
-   [Etapa 5: Criar o esquema][Etapa 5: Criar o esquema]
-   [Etapa 6: Inserir dados][Etapa 6: Inserir dados]
-   [Etapa 7: Exemplo de consulta e dados do sistema no Portal de Gerenciamento do Banco de Dados SQL][Etapa 7: Exemplo de consulta e dados do sistema no Portal de Gerenciamento do Banco de Dados SQL]
-   [Etapa 8: Criar um logon de banco de dados e atribuir permissões][Etapa 8: Criar um logon de banco de dados e atribuir permissões]
-   [Etapa 9: Conectar-se de outros aplicativos][Etapa 9: Conectar-se de outros aplicativos]

## Etapa 1: Criar uma conta do Microsoft Azure

1.  Abra um navegador da web e navegue para [][]<http://azure.microsoft.com></a>.
    Para começar com uma conta gratuita, clique em avaliação gratuita no canto superior direito e siga as etapas.

2.  Sua conta agora está criada. Você está pronto para começar.

## Etapa 2: Conectar-se ao Azure e criar um banco de dados

1.  Entre no [Portal de Gerenciamento][Portal de Gerenciamento]. Você deve ver um painel de navegação semelhante a este.

    ![Painel de navegação][Painel de navegação]

2.  Clique em **Novo** na parte inferior da página. Quando você clica em **Novo**, uma lista rola a tela para cima mostrando coisas que você pode criar.

3.  Clique em **Banco de Dados SQL** e, em seguida, clique em **Criação Personalizada**.

    ![Painel de navegação][1]

A escolha dessa opção permite que você crie um novo servidor e banco de dados SQL ao mesmo tempo, com você como o administrador. Como o administrador do sistema, você pode executar mais tarefas, incluindo a conexão ao Portal de Gerenciamento do Banco de Dados SQL, o que você irá fazer mais tarde neste tutorial.

1.  A página Configurações do Banco de Dados é exibida quando você clica em **Criação Personalizada**. Esta página fornece informações básicas que criam um banco de dados SQL vazio no servidor. A adição de tabelas e dados será apresentada em uma etapa posterior.

    Preencha a página Configurações do Banco de Dados da seguinte maneira:

    ![Painel de navegação][2]

-   Insira **Escola** como o nome do banco de dados.

-   Use as configurações padrão de edição, tamanho máximo e agrupamento.

-   Escolha **Novo Servidor de Banco de Dados SQL**. Selecionar um novo servidor adiciona uma segunda página que iremos usar para definir a conta do administrador e a região.

-   Ao concluir, clique na seta para ir para a próxima página.

1.  Preencha as Configurações do Servidor da seguinte maneira:

    ![Painel de navegação][3]

-   Digite um nome de administrador como uma palavra sem espaços. O Banco de Dados SQL usa a Autenticação do SQL através de uma conexão criptografada para validar a identidade do usuário. Será criado um novo logon de autenticação do SQL Server que tem permissões de administrador usando o nome que você fornecer. O nome do administrador não pode ser um usuário do Windows e também não deve ser uma ID do nome do usuário do Windows Live. A Autenticação do Windows não é suportada no Banco de Dados SQL.

-   Forneça uma senha forte com mais de oito caracteres, usando uma combinação de letras maiúsculas e minúsculas e um número ou um símbolo. Use a bolha de Ajuda para obter detalhes sobre a complexidade da senha.

-   Escolha uma região. A Região determina a localização geográfica do servidor. As Regiões não podem ser facilmente alternadas, portanto, escolha uma que faça sentido para este servidor. Escolha um local mais próximo de você. Ao manter seu aplicativo do Azure e o banco de dados na mesma região, você economiza em custo de largura de banda de entrada e em latência de dados.

-   Mantenha a caixa de seleção **Permitir que os serviços do Azure Services** acessem este servidor selecionada, para que você possa conectar-se ao banco de dados usando o Portal de Gerenciamento do Banco de Dados SQL, o Excel no Office 365 ou os Relatórios SQL do Azure.

-   Clique na marca de seleção localizada na parte inferior da página quando tiver concluído.

Observe que você não especificou um nome de servidor. Como o servidor de Banco de Dados SQL deve ser acessível em todo o mundo, o Banco de Dados SQL configura as entradas DNS apropriadas quando o servidor é criado. O nome gerado garante que não há colisões de nome com outras entradas DNS. Você não pode alterar o nome do seu servidor de Banco de Dados SQL.

Para ver o nome do servidor que hospeda o banco de dados **School** que você acabou de criar, clique em **Bancos de Dados SQL** no painel de navegação à esquerda e, em seguida clique no banco de dados **School** na exibição da lista dos **Bancos de Dados SQL**. Na página **Início Rápido**, role para baixo para ver o nome do servidor.

Na próxima etapa, você irá configurar o firewall para que as conexões de aplicativos em execução no seu computador tenham permissão para acessar os bancos de dados em seu servidor de Banco de Dados SQL.

## Etapa 3: Configurar o firewall

Para configurar o firewall para que as conexões sejam permitidas através dele, você digitará informações na página do servidor.

<strong>Observação:</strong> O serviço de Banco de Dados SQL só está disponível com a porta TCP 1433 usada pelo protocolo TDS, portanto certifique-se de que o firewall no seu computador local e na sua rede permita a comunicação de saída TCP na porta 1433. Para obter mais informações, consulte [Firewall do Banco de Dados SQL (a página pode estar em inglês)][Firewall do Banco de Dados SQL (a página pode estar em inglês)].

1.  No painel de navegação à esquerda, clique em **Bancos de Dados SQL**.

2.  Clique em **Servidores** na parte superior da página. Em seguida, clique no servidor que você acabou de criar para abrir a página do servidor.

3.  Na página do servidor, clique em **Configurar** para abrir as configurações de **Endereços de IP Permitidos** e, em seguida, clique no link de **Adicionar os Endereços de IP permitidos**. Isso criará uma nova regra de firewall para permitir solicitações de conexão a partir do roteador ou servidor de proxy que o seu dispositivo está escutando.

4.  Você pode criar regras de firewall adicional ao especificar um nome de regra e os valores de intervalo de IP inicial e final.

5.  Para habilitar as interações entre este servidor e os outros serviços da Azure, clique em **Sim** para a opção de **Serviços do Microsoft Azure**.

6.  Para salvar suas alterações, clique em **Salvar** na parte inferior da página.

7.  Depois que você salvar a regra, sua página terá aparência semelhante à seguinte captura de tela.

    ![Painel de navegação][4]

Agora você tem um servidor de Banco de Dados SQL no Azure, uma regra de firewall que permite o acesso ao servidor, um objeto de banco de dados e um logon de administrador. Mas você ainda não tem um banco de dados de trabalho que você possa consultar. Para fazer isso, seu banco de dados deve ter um esquema e dados reais.

Por causa deste tutorial, use apenas as ferramentas em mãos, você usará a janela de consulta no Portal de Gerenciamento de Banco de Dados SQL para executar um script Transact-SQL para compilar um banco de dados predefinido.

Conforme suas habilidades aumentarem, você desejará explorar maneiras adicionais de criar um banco de dados, incluindo abordagens programáticas ou a superfície do designer nas Ferramentas de Dados do SQL Server. Se você já tiver um banco de dados SQL Server existente que executa em um servidor local, poderá migrar facilmente esse banco de dados para o servidor do Azure que acabou de configurar. Use os links no final deste tutorial para descobrir como.

## Etapa 4: Adicionar dados e um esquema usando um script Transact-SQL

Nesta etapa, você executa dois scripts. O primeiro cria um esquema que define tabelas, colunas e relações. O segundo script adiciona os dados. Cada etapa é executada independentemente em uma conexão separada. Se você tiver criado bancos de dados no SQL Server anteriormente, uma das diferenças que você observará no Banco de Dados SQL é que os comandos CREATE e INSERT devem ser executados em lotes separados. O Banco de Dados SQL impõe esse requisito para minimizar ataques contra os dados enquanto estiverem em trânsito.

<strong>Observação:</strong>Os valores de esquema e de dados são obtidos deste [MSDN][MSDN] e foram modificados para funcionar com o Banco de Dados SQL.

1.  Acesse a página inicial. No [Portal de Gerenciamento][Portal de Gerenciamento], o Banco de Dados **Escola** aparece na lista de itens na página inicial.

    ![Painel de navegação][5]

2.  Clique em **Escola** para selecioná-la e, em seguida, clique em**Gerenciar** na parte inferior da página. Isso abrirá o Portal de Gerenciamento do Banco de Dados SQL. Esse Portal é separado do Portal de Gerenciamento do Azure. Você usará esse portal para executar comandos e consultas Transact-SQL.

3.  Insira um nome de logon e senha de administrador para efetuar logon no banco de dados **Escola**. Esse é o logon de administrador que você especificou quando criou o servidor.

4.  Clique em **Nova Consulta** no Portal de Gerenciamento do Banco de Dados SQL, na faixa de opções. Uma janela de consulta vazia abrirá na área de trabalho. Na próxima etapa, você usará essa janela para copiar uma série de scripts predefinidos que adicionarão a estrutura e os dados a seu banco de dados vazio.

## Etapa 5: Criar o esquema

Nesta etapa, você criará o esquema usando o seguinte script. O script primeiro verifica se há uma tabela existente de mesmo nome para garantir que não haverá uma colisão de nomes e cria a tabela usando a instrução [CREATE TABLE][CREATE TABLE]. Além disso, esse script usa a instrução [ALTER TABLE][ALTER TABLE] para especificar as relações de chave primária e de tabela.

Copie o script e cole-o na janela de consulta. Clique em **Executar** na parte superior da janela para executar o script.

<div style="width:auto; height:600px; overflow:auto"><pre>
    -- Criar a tabela Department.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Department]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Department](
        [DepartmentID] [int] NOT NULL,
        [Name] [nvarchar](50) NOT NULL,
        [Budget] [money] NOT NULL,
        [StartDate] [datetime] NOT NULL,
        [Administrator] [int] NULL,
     CONSTRAINT [PK_Department] PRIMARY KEY CLUSTERED 
    (
    [DepartmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    IR

    -- Criar a tabela Person.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Person]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Person](
        [PersonID] [int] IDENTITY(1,1) NOT NULL,
        [LastName] [nvarchar](50) NOT NULL,
        [FirstName] [nvarchar](50) NOT NULL,
        [HireDate] [datetime] NULL,
        [EnrollmentDate] [datetime] NULL,
     CONSTRAINT [PK_School.Student] PRIMARY KEY CLUSTERED   
    (
    [PersonID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    IR

    -- Criar a tabela OnsiteCourse.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OnsiteCourse](
        [CourseID] [int] NOT NULL,
        [Location] [nvarchar](50) NOT NULL,
        [Days] [nvarchar](50) NOT NULL,
        [Time] [smalldatetime] NOT NULL,
     CONSTRAINT [PK_OnsiteCourse] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    IR

    -- Criar a tabela OnlineCourse.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OnlineCourse]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OnlineCourse](
        [CourseID] [int] NOT NULL,
        [URL] [nvarchar](100) NOT NULL,
     CONSTRAINT [PK_OnlineCourse] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    IR

    --Criar a tabela StudentGrade.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[StudentGrade]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[StudentGrade](
        [EnrollmentID] [int] IDENTITY(1,1) NOT NULL,
        [CourseID] [int] NOT NULL,
        [StudentID] [int] NOT NULL,
        [Grade] [decimal](3, 2) NULL,
     CONSTRAINT [PK_StudentGrade] PRIMARY KEY CLUSTERED 
    (
        [EnrollmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    IR

    -- Criar uma tabela CourseInstructor.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[CourseInstructor]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[CourseInstructor](
        [CourseID] [int] NOT NULL,
        [PersonID] [int] NOT NULL,
     CONSTRAINT [PK_CourseInstructor] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC,
        [PersonID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    ) 
    END;
    IR

    -- Criar uma tabela Course.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[Course]') 
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[Course](
        [CourseID] [int] NOT NULL,
        [Title] [nvarchar](100) NOT NULL,
        [Credits] [int] NOT NULL,
        [DepartmentID] [int] NOT NULL,
     CONSTRAINT [PK_School.Course] PRIMARY KEY CLUSTERED 
    (
        [CourseID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    IR

    -- Criar uma tabela OfficeAssignment.
    IF NOT EXISTS (SELECT * FROM sys.objects 
        WHERE object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]')
        AND type in (N'U'))
    BEGIN
    CREATE TABLE [dbo].[OfficeAssignment](
        [InstructorID] [int] NOT NULL,
        [Location] [nvarchar](50) NOT NULL,
        [Timestamp] [timestamp] NOT NULL,
     CONSTRAINT [PK_OfficeAssignment] PRIMARY KEY CLUSTERED 
    (
        [InstructorID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
    IR

    -- Definir o relacionamento entre OnsiteCourse e Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnsiteCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]'))
    ALTER TABLE [dbo].[OnsiteCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnsiteCourse_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    IR
    ALTER TABLE [dbo].[OnsiteCourse] CHECK 
       CONSTRAINT [FK_OnsiteCourse_Course];
    IR

    -- Definir o relacionamento entre OnlineCourse e Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnlineCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnlineCourse]'))
    ALTER TABLE [dbo].[OnlineCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnlineCourse_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    IR
    ALTER TABLE [dbo].[OnlineCourse] CHECK 
       CONSTRAINT [FK_OnlineCourse_Course];
    IR
    -- Definir o relacionamento entre StudentGrade e Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))
    ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    IR
    ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Course];
    IR

    -- Definir o relacionamento entre StudentGrade e Student.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Student]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))   
    ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Student] FOREIGN KEY([StudentID])
    REFERENCES [dbo].[Person] ([PersonID]);
    IR
    ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Student];
    IR

    -- Definir o relacionamento entre CourseInstructor e Course.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
     WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Course]')
     AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
    ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
     CONSTRAINT [FK_CourseInstructor_Course] FOREIGN KEY([CourseID])
    REFERENCES [dbo].[Course] ([CourseID]);
    IR
    ALTER TABLE [dbo].[CourseInstructor] CHECK 
      CONSTRAINT [FK_CourseInstructor_Course];
    IR

    -- Definir o relacionamento entre CourseInstructor e Person.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
      WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Person]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
    ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
      CONSTRAINT [FK_CourseInstructor_Person] FOREIGN KEY([PersonID])
    REFERENCES [dbo].[Person] ([PersonID]);
    IR
    ALTER TABLE [dbo].[CourseInstructor] CHECK 
     CONSTRAINT [FK_CourseInstructor_Person];
    IR

    -- Definir o relacionamento entre Course e Department.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
    ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
    REFERENCES [dbo].[Department] ([DepartmentID]);
    IR
    ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
    IR

    --Definir o relacionamento entre OfficeAssignment e Person.
    IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
      WHERE object_id = OBJECT_ID(N'[dbo].[FK_OfficeAssignment_Person]')
      AND parent_object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]'))
    ALTER TABLE [dbo].[OfficeAssignment]  WITH CHECK ADD  
      CONSTRAINT [FK_OfficeAssignment_Person] FOREIGN KEY([InstructorID])
    REFERENCES [dbo].[Person] ([PersonID]);
    IR
    ALTER TABLE [dbo].[OfficeAssignment] CHECK 
     CONSTRAINT [FK_OfficeAssignment_Person];
    IR
</pre></div>

## Etapa 6: Inserir dados

Abra uma nova janela de consulta e, em seguida, cole-a no script a seguir. Execute o script para inserir dados. Esse script usa a instrução [INSERT][INSERT] para adicionar valores a cada coluna.

<div style="width:auto; height:600px; overflow:auto"><pre>
    Inserir dados na tabela de Person.
    SET IDENTITY_INSERT dbo.Person ON;
    IR
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (1, 'Abercrombie', 'Kim', '1995-03-11', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (2, 'Barzdukas', 'Gytis', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (3, 'Justice', 'Peggy', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (4, 'Fakhouri', 'Fadi', '2002-08-06', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (5, 'Harui', 'Roger', '1998-07-01', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (6, 'Li', 'Yan', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (7, 'Norman', 'Laura', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (8, 'Olivotto', 'Nino', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (9, 'Tang', 'Wayne', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (10, 'Alonso', 'Meredith', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (11, 'Lopez', 'Sophia', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (12, 'Browning', 'Meredith', null, '2000-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (13, 'Anand', 'Arturo', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (14, 'Walker', 'Alexandra', null, '2000-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (15, 'Powell', 'Carson', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (16, 'Jai', 'Damien', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (17, 'Carlson', 'Robyn', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (18, 'Zheng', 'Roger', '2004-02-12', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (19, 'Bryant', 'Carson', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (20, 'Suarez', 'Robyn', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (21, 'Holt', 'Roger', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (22, 'Alexander', 'Carson', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (23, 'Morgan', 'Isaiah', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (24, 'Martin', 'Randall', null, '2005-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (25, 'Kapoor', 'Candace', '2001-01-15', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (26, 'Rogers', 'Cody', null, '2002-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (27, 'Serrano', 'Stacy', '1999-06-01', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (28, 'White', 'Anthony', null, '2001-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (29, 'Griffin', 'Rachel', null, '2004-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (30, 'Shan', 'Alicia', null, '2003-09-01');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (31, 'Stewart', 'Jasmine', '1997-10-12', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (32, 'Xu', 'Kristen', '2001-7-23', null);
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (33, 'Gao', 'Erica', null, '2003-01-30');
    INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALUES (34, 'Van Houten', 'Roger', '2000-12-07', null);
    IR
    SET IDENTITY_INSERT dbo.Person OFF;
    IR
    
</pre></div>

## Etapa 7: Exemplo de consulta e dados do sistema no Portal de Gerenciamento do Banco de Dados SQL

Para verificar seu trabalho, execute uma consulta que retorne os dados que você acabou de digitar. Você também pode executar procedimentos armazenados internos e modos de exibição de gerenciamento de dados que fornecem informações sobre os bancos de dados em execução no seu servidor de Banco de Dados SQL.

#### Consultar dados de exemplo

Em uma nova janela de consulta, copie e execute o seguinte script Transact-SQL para recuperar alguns dos dados que você acabou de adicionar.

<div style="width:auto; height:auto; overflow:auto"><pre>
    SELECT * From Person
</pre></div>

Você deve ver um conjunto de resultados que inclui 34 linhas da tabela pessoal, incluindo PersonID, LastName, FirstName, HireDate e EnrollmentDate.

#### Consultar dados do sistema

Você também pode usar exibições do sistema e procedimentos armazenados internos para obter informações do servidor. Para o objetivo deste tutorial, você tentará apenas alguns comandos.

Execute o seguinte comando para descobrir quais bancos de dados estão disponíveis no servidor.

    SELECT * FROM sys.databases  

Execute este comando para retornar uma lista de usuários atualmente conectados ao servidor.

    SELECT user_name(),suser_sname()

Execute este procedimento armazenado para retornar uma lista de todos os objetos no banco de dados **Escola**.

    EXEC SP_help

Não feche a conexão do portal ao banco de dados **Escola**. Você precisará dela novamente em alguns minutos.

## Etapa 8: Criar um logon de banco de dados e atribuir permissões

No Banco de Dados SQL, você pode criar logons e conceder permissões usando o Transact-SQL. Nesta lição, usando o Transact-SQL, você irá fazer três coisas:

1.  Criar um logon de autenticação do SQL Server
2.  Criar um usuário de banco de dados e
3.  Conceder permissões através da associação da função.

Um logon de autenticação do SQL Server é usado para conexões com o servidor. Todos os usuários que acessam um servidor de Banco de Dados fazem isso fornecendo um nome de logon e uma senha de autenticação do SQL Server.

Para criar um logon, você deve primeiro conectar-se ao banco de dados **mestre**.

#### Criar um logon de autenticação do SQL Server

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], selecione **Bancos de Dados SQL**, clique em **Servidores**, escolha o servidor e, em seguida, clique na seta branca para abrir a
    página do servidor.

2.  Na página Início Rápido, clique em **Gerenciar Servidor** para abrir uma nova conexão com o Portal de Gerenciamento do Banco de Dados SQL.

3.  Especifique **mestre** para o banco de dados se conectar, em seguida efetue logon com seu nome de usuário e senha. Esse é o logon de administrador que você especificou quando criou o servidor.

4.  O Portal de Gerenciamento do Banco de Dados SQL abre em uma nova janela do navegador e você será conectado ao **mestre**.

5.  Se você vir um erro na página semelhante ao seguinte, ignore-o. Clique em **Nova Consulta** para abrir uma janela de consulta que permite executar comandos Transact-SQL no banco de dados **mestre**.

    ![Painel de navegação][6]

6.  Copie e cole o seguinte comando na janela de consulta.

        CREATE LOGIN SQLDBLogin WITH password='Password1';

7.  Execute o comando para criar um novo logon do SQL Server chamado 'SQLDBLogin'.

#### Criar um usuário de banco de dados e atribuir permissões

Depois de criar um logon de autenticação do SQL Server, a próxima etapa é atribuir o banco de dados e os níveis de permissão associados ao logon. Você faz isso criando um **usuário de banco de dados** em cada banco de dados.

1.  Volte para a página Portal de Gerenciamento do Banco de Dados SQL que se conecta ao banco de dados **Escola**. Se você fechou a janela do navegador, inicie uma nova conexão ao banco de dados **Escola** usando as etapas da lição anterior, "Adicionar dados e um esquema usando um script Transact-SQL".

    Na página do Portal de Gerenciamento do Banco de Dados SQL, o nome do banco de dados **Escola** está visível no canto superior esquerdo.

    ![Painel de navegação][7]

2.  Clique em **Nova Consulta** para abrir uma nova janela de consulta e copiar na instrução a seguir.

        CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3.  Execute o script. Esse script cria um novo usuário de banco de dados baseado no logon.

	Em seguida, você poderá atribuir permissões usando a função db\_datareader. Os usuários do banco de dados atribuídos a essa função podem ler todos os dados de todas as tabelas de usuário no banco de dados.

1.  Abra uma nova janela de consulta e digite e execute a instrução a seguir. Essa instrução executa um procedimento armazenado interno que atribui a função db\_datareader ao novo usuário que você acabou de criar.

        EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

Agora você tem um novo logon de autenticação do SQL Server que tem permissão somente leitura para o banco de dados **Escola**. Usando essas etapas, você pode criar outros logons de autenticação do SQL Server para permitir diferentes níveis de acesso aos seus dados.

## Etapa 9: Conectar-se de outros aplicativos

Agora que você tem um banco de dados operacional, você pode conectá-lo em uma pasta de trabalho Excel.

#### Conectar-se a partir do Excel

Se o Microsoft Excel estiver instalado em seu computador, você poderá usar as etapas a seguir para se conectar a seu banco de dados de exemplo.

1.  No Excel, na guia Dados, clique em **De Outras Origens**e, em seguida, clique em **Do SQL Server**.

2.  No assistente para Conexão de Dados, digite o nome de domínio totalmente qualificado do seu Servidor de Banco de Dados SQL, seguido de um logon de Autenticação do SQL Server que tem permissão para acessar o banco de dados.

	O nome do servidor pode ser localizado no Portal de Gerenciamento do Azure, no Banco de Dados SQL, na página Servidor, no Painel, em **Gerenciar URL**. O nome do servidor consiste em uma série de letras e números, seguidos de '.database.windows.net'. Especifique esse nome no assistente de Conexão do Banco de Dados. Não inclua o prefixo <http://> ou <https://> ao especificar o nome.

	Insira um logon de autenticação do SQL Server. Para fins de teste, você pode usar o logon de administrador que criou quando configurou o servidor. Para acesso normal a dados, use um logon de usuário de banco de dados semelhante ao que você acabou de criar.

	![Painel de navegação][8]

1.  Na próxima página, escolha o banco de dados **Escola** e, em seguida, escolha **Pessoa**. Clique em **Concluir**. Se você solicitou por detalhes de logon, digite-os e, em seguida, clique em **OK**.

2.  A caixa de diálogo Importar Dados é exibida solicitando que você selecione como e onde importar seus dados. Com as opções padrão selecionadas, clique em **OK**.

    ![Painel de navegação][9]

3.  Na planilha, você deve ver uma tabela com um conjunto de resultados que inclui 34 linhas da tabela pessoal, incluindo PersonID, LastName, FirstName, HireDate e EnrollmentDate, assim como os resultados de consulta da Etapa 7.

Usando somente o Excel, você pode importar apenas uma tabela por vez. Uma abordagem melhor é usar o suplemento PowerPivot para Excel, que permite que você importe e trabalhe com várias tabelas como um único conjunto de dados. Trabalhar com o PowerPivot está além do escopo deste tutorial, mas você pode obter mais informações sobre isso em [PowerPivot para Excel][PowerPivot para Excel].

## Próximas etapas

Agora que você está familiarizado com o Banco de Dados SQL e os Portais de Gerenciamento, você pode experimentar outras ferramentas e técnicas usadas pelos administradores de Banco de Dados do SQL Server.

Para gerenciar ativamente o seu novo banco de dados, considere a possibilidade de instalar e usar o SQL Server Management Studio. O Management Studio é a ferramenta de administração de banco de dados primário para o gerenciamento de bancos de dados do SQL Server, inclusive aqueles em execução no Azure. Usando o Management Studio, você pode salvar consultas para uso futuro, adicionar novas tabelas e procedimentos armazenados e aprimorar suas habilidades no Transact-SQL em um ambiente de script sofisticado que inclui um verificador de sintaxe, intellisense e modelos. Para começar, siga as instruções em [Gerenciando o Banco de Dados do SQL Server usando o SQL Server Management Studio (a página pode estar em inglês)][Gerenciando o Banco de Dados do SQL Server usando o SQL Server Management Studio (a página pode estar em inglês)].

Ter fluência na consulta Transact SQL e na linguagem de definição de dados é essencial para os administradores de Banco de Dados. Se você for novo no Transact-SQL, comece com o [Tutorial: Escrevendo instruções Transact-SQL][Tutorial: Escrevendo instruções Transact-SQL] para aprender algumas técnicas básicas.

Existem outros métodos para mover um banco de dados local para o Banco de Dados SQL. Se você tiver bancos de dados existentes, ou fez o download de exemplos de bancos de dados para praticar, tente as seguintes abordagens alternativas:

-   [Migrando bancos de dados para o Banco de Dados SQL][Migrando bancos de dados para o Banco de Dados SQL]
-   [Copiando bancos de dados no Banco de Dados SQL][Copiando bancos de dados no Banco de Dados SQL]
-   [Implantar um Banco de dados do SQL Server em uma máquina virtual do Azure][Implantar um Banco de dados do SQL Server em uma máquina virtual do Azure]

  [Etapa 1: Criar uma conta do Microsoft Azure]: #Subscribe
  [Etapa 3: Configurar o firewall]: #ConfigFirewall
  [Etapa 4: Adicionar dados e um esquema usando um script Transact-SQL]: #AddData
  [Etapa 5: Criar o esquema]: #createschema
  [Etapa 6: Inserir dados]: #insertData
  [Etapa 7: Exemplo de consulta e dados do sistema no Portal de Gerenciamento do Banco de Dados SQL]: #QueryDBSysData
  [Etapa 8: Criar um logon de banco de dados e atribuir permissões]: #DBLogin
  [Etapa 9: Conectar-se de outros aplicativos]: #ClientConnection
  []: http://azure.microsoft.com
  [Portal de Gerenciamento]: http://manage.windowsazure.com
  [Painel de navegação]: ./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png
  [1]: ./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png
  [2]: ./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG
  [3]: ./media/sql-database-get-started/4ServerSettings_SQLTut.PNG
  [Firewall do Banco de Dados SQL (a página pode estar em inglês)]: http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-pt-br.aspx
  [4]: ./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png
  [MSDN]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee621790.aspx "artigo do "
  [5]: ./media/sql-database-get-started/20MainPageHome_SQLTut.PNG
  [CREATE TABLE]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336258.aspx
  [ALTER TABLE]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336286.aspx
  [INSERT]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336284.aspx
  [6]: ./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG
  [7]: ./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG
  [8]: ./media/sql-database-get-started/16ExcelConnect_SQLTut.png
  [9]: ./media/sql-database-get-started/19ExcelImport_SQLTut.png
  [PowerPivot para Excel]: http://go.microsoft.com/fwlink/?LinkId=396969
  [Gerenciando o Banco de Dados do SQL Server usando o SQL Server Management Studio (a página pode estar em inglês)]: http://www.azure.microsoft.com/pt-br/documentation/articles/sql-database-manage-azure-ssms/
  [Tutorial: Escrevendo instruções Transact-SQL]: http://msdn.microsoft.com/pt-br/library/ms365303.aspx
  [Migrando bancos de dados para o Banco de Dados SQL]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee730904.aspx
  [Copiando bancos de dados no Banco de Dados SQL]: http://msdn.microsoft.com/pt-br/library/windowsazure/ff951624.aspx
  [Implantar um Banco de dados do SQL Server em uma máquina virtual do Azure]: http://msdn.microsoft.com/pt-br/library/dn195938(v=sql.120).aspx
