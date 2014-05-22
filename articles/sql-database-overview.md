

# Como gerenciar Bancos de Dados SQL no Azure

Este guia mostra como realizar tarefas administrativas para servidores lógicos e instâncias de banco de dados no Banco de Dados SQL do Azure. 

##O que é o Banco de Dados SQL?

O Banco de Dados SQL fornece serviços de gerenciamento de banco de dados relacional no Azure e baseia-se na tecnologia do SQL Server. Com o Banco de Dados SQL, você poderá provisionar e implantar facilmente instâncias de banco de dados, aproveitar um data center distribuído que oferece disponibilidade, escalabilidade e segurança empresariais com os benefícios de autorrecuperação e proteção de dados interna. 

##Sumário

* [Entrar no Azure][] 
* [Configurar o Banco de Dados SQL][]
* [Conectar-se usando o Management Studio][]
* [Implantar um banco de dados no Azure][]
* [Adicionar logons e usuários][]
* [Dimensionar uma solução de Banco de Dados SQL][]
* [Monitorar servidores lógicos e instâncias de bancos de dados][]
* [Próximas etapas][]


<h2><a id="PreReq1"></a>Entrar no Azure</h2>

O Banco de Dados SQL fornece serviços de armazenamento, acesso e gerenciamento de dados relacionais no Azure. Para usá-lo, você precisará de uma assinatura do Azure.

1. Abra um navegador da web e navegue até [http://www.windowsazure.com](http://www.windowsazure.com). Para começar com uma conta gratuita, clique em avaliação gratuita no canto superior direito e siga as etapas.

2. Sua conta agora está criada. Você está pronto para começar.


<h2><a id="PreReq2"></a>Criar e configurar o Banco de Dados SQL</h2>

Em seguida, você aprenderá passo a passo a criar e configurar um servidor lógico. No novo Portal de Gerenciamento do Azure (Visualização), os fluxos de trabalho revisados permitem que você crie um banco de dados primeiro e, em seguida, um servidor. 

Neste guia, você criará o servidor primeiro. Talvez você prefira essa abordagem se tiver bancos de dados do SQL Server existentes que deseja carregar.

<h3><a id="createsrvr"></a>Criar um servidor lógico</h3>

1. Entre em [http://www.windowsazure.com](http://www.windowsazure.com).

2. Clique em **Banco de Dados SQL** e depois clique em **SERVIDORES** na página inicial do Banco de Dados SQL.

4. Clique em **Adicionar** na parte inferior da página. 

5. Nas Configurações do Servidor, digite um nome de administrador como uma palavra sem espaços. 

	O Banco de Dados SQL usa a Autenticação do SQL por meio de uma conexão criptografada. Será criado um novo login de autenticação do SQL Server atribuído à função do servidor fixo do sysadmin usando o nome fornecido. 

	O login não pode ser um endereço de e-mail, conta de usuário do Windows ou um ID do Windows Live. Declarações ou autenticação do Windows não são suportadas no Banco de Dados SQL.

6. Forneça uma senha forte que tenha mais de oito caracteres, usando uma combinação de letras maiúsculas e minúsculas e um número ou um símbolo.

7. Escolha uma região. A Região determina a localização geográfica do servidor. As Regiões não podem ser facilmente alternadas, portanto, escolha uma que faça sentido para este servidor. Escolha um local mais próximo de você. Ao manter seu aplicativo do Azure e o banco de dados na mesma região, você economiza em custo de largura de banda de entrada e em latência de dados.

8. Certifique-se de manter a opção **Permitir serviços** selecionada para que você possa se conectar ao banco de dados usando o Portal de Gerenciamento do Banco de Dados SQL, dos serviços de armazenamento e de outros serviços no Azure. 

9. Clique na marca de seleção localizada na parte inferior da página quando tiver concluído.

Observe que você não especificou um nome de servidor. O Banco de dados SQL gera automaticamente o nome do servidor para garantir que não existam entradas do DNS duplicadas. O nome do servidor é uma cadeia de 10 caracteres alfanuméricos. Você não pode alterar o nome do seu servidor de Banco de Dados SQL.

Na próxima etapa, você configurará o firewall para que as conexões de aplicativos em execução em sua rede tenham permissão de acesso.

<h3><a id="configFWLogical"></a>Configurar o firewall para o servidor lógico</h3>

1. Clique em **Bancos de Dados SQL**, clique em **Servidores** e, em seguida, clique no servidor que você acabou de criar.

2. Clique em **Configurar**. 

3. Copie o endereço IP atual do cliente. Se você estiver se conectando a partir de uma rede, este é o endereço IP que seu roteador ou o servidor proxy está escutando. O Banco de Dados SQL detecta o endereço IP usado pela conexão atual para que você possa criar uma regra de firewall para aceitar solicitações de conexão nesse dispositivo. 

4. Cole o endereço IP no intervalo de início e fim. Mais tarde, se você encontrar erros de conexão indicando que o intervalo é muito estreito, você poderá editar essa regra para aumentar o intervalo.

	Se os computadores cliente usam endereços IP atribuídos dinamicamente, você deve especificar um intervalo que seja amplo o suficiente para incluir os endereços IP atribuídos aos computadores em sua rede. Inicie com um intervalo estreito e depois expanda-o apenas se for necessário.

5. Digite um nome para a regra de firewall, como o nome de seu computador ou empresa.

6. Clique na marca de seleção para salvar a regra.

7. Clique em **Salvar** na parte inferior da página para concluir a etapa. Se você não vir a opção **Salvar**, atualize a página do navegador.

Agora você tem um servidor lógico e uma regra de firewall que permite conexões de entrada de seu endereço IP e um login de administrador. Na próxima etapa, você alternará para o computador local para concluir as etapas de configuração restantes.

**Nota:** O servidor lógico recém-criado é transitório e será hospedado dinamicamente em servidores físicos em um data center. Se você excluir o servidor, você deve saber antecipadamente que isto se trata de uma ação não recuperável. Certifique-se de fazer backup dos bancos de dados que você posteriormente carregar para o servidor. 



<h2><a id="PreReq3"></a>Conectar-se usando o Management Studio</h2>

O Management Studio é uma ferramenta administrativa que permite a você gerenciar várias instâncias do SQL Server e servidores em um único espaço de trabalho. Se já tiver uma instância do SQL Server local, você poderá abrir uma conexão com a instância local e um servidor lógico no Azure para executar as tarefas lado a lado.

O Management Studio inclui recursos que não estão disponíveis no momento no portal de gerenciamento, como um verificador de sintaxe e a capacidade de salvar scripts e consultas nomeadas para reutilização. O Banco de dados SQL é apenas um ponto de extremidade TDS. Qualquer ferramenta que funcione com o TDS, incluindo o Management Studio, é válida para as operações do Banco de Dados SQL. Os scripts que você desenvolver para o servidor local serão executado em um servidor lógico do Banco de Dados SQL. 

Na etapa seguinte, você usará o Management Studio para se conectar a um servidor lógico no Azure. Esta etapa requer que você tenha o SQL Server Management Studio versão 2008 R2 ou 2012. Se você precisar de ajuda para baixar ou conectar-se ao Management Studio, consulte [Gerenciando o Banco de Dados SQL usando o Management Studio][] neste site.

Antes de você se conectar, às vezes, é necessário criar uma exceção de firewall que permita as solicitações de saída na porta 1433 no seu sistema local. Os computadores que, por padrão, estejam protegidos normalmente não têm a porta 1433 aberta. 

<h3><a id="configFWOnPremise"></a>Configurar o firewall para um servidor local</h3>

1. No Firewall do Windows com Segurança Avançada, crie uma nova regra de saída.

2. Escolha **Porta**, especificar TCP 1433, especifique **Permitir a conexão** e certifique-se de que o perfil **Público** esteja selecionado.

3. Atribua um nome significativo, como *WindowsAzureSQLDatabase (tcp-out) port 1433*. 


<h3><a id="logical"></a>Conectar-se a um servidor lógico</h3>

1. No Management Studio, em Conectar-se ao Servidor, certifique-se de que o Mecanismo de Banco de Dados esteja selecionado e, em seguida, insira o nome do servidor lógico neste formato: *servername*. database.widnows.net

   Você também pode obter o nome do servidor totalmente qualificado no portal de gerenciamento, no painel do servidor, em GERENCIAR URL.

2. Na Autenticação, escolha **Autenticação do SQL Server** e insira o logon de administrador que você criou ao configurar o servidor lógico.

3. Clique em **Opções**. 

4. Em Conectar-se ao banco de dados, especifique **mestre**.


<h3><a id="premise"></a>Conectar-se a um servidor local</h3>

1. No Management Studio, em Conectar-se ao Servidor, certifique-se de que o Mecanismo de Banco de Dados esteja selecionado e, em seguida, insira o nome de uma instância local neste formato: *servername*\\*instancename*. Se o servidor for local e uma instância padrão, digite *localhost*.

2. Na Autenticação, escolha **Autenticação do Windows** e insira uma conta do Windows que seja membro da função sysadmin.


<h2><a id="HowTo1"></a>Implantar um banco de dados no Azure</h2>

Há inúmeras abordagens para mover um banco de dados SQL Server local para o Azure. Nesta tarefa, você usará o assistente para Implantar Banco de Dados no Banco de dados SQL para carregar um banco de dados de exemplo.

O banco de dados de exemplo School é convenientemente simples. Todos os seus objetos são compatíveis com o Banco de Dados SQL eliminando a necessidade de modificar ou preparar um banco de dados para migração. Como um novo administrador, primeiro tente implantar um banco de dados simples para aprender as etapas antes de usar seus próprios bancos de dados. 

**Observação:** examine o Guia de Migração do Banco de Dados SQL para obter instruções detalhadas sobre como preparar um banco de dados local para a migração para o Azure. Além disso, considere a possibilidade de baixar o Kit de Treinamento do Azure. Ele inclui um laboratório que mostra uma abordagem alternativa para migrar um banco de dados local.

<h3><a id="CreateDB"></a>Criar o banco de dados school em um servidor local</h3>

Os scripts para criar esse banco de dados podem ser encontrados em [Introdução à administração de Banco de Dados SQL][]. Nesse guia, você executará esses scripts no Management Studio para criar uma versão local do banco de dados School.

1. No Management Studio, conecte-se a um servidor local. Clique com o botão direito do mouse em **Bancos de Dados**, clique em **Novo Banco de Dados** e digite *school*.

2. Clique com o botão direito do mouse em *school* e clique em **Nova Consulta**. 

3. Copie e, em seguida, execute o script Create Schema do tutorial. 

<div style="width:auto; height:300px; overflow:auto"><pre>
	-- Create the Department table.
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
	GO

	-- Create the Person table.
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
	GO

	-- Create the OnsiteCourse table.
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
	GO

	-- Create the OnlineCourse table.
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
	GO

	--Create the StudentGrade table.
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
	GO

	-- Create the CourseInstructor table.
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
	GO

	-- Create the Course table.
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
	GO

	-- Create the OfficeAssignment table.
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
	GO

	-- Define the relationship between OnsiteCourse and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnsiteCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]'))
	ALTER TABLE [dbo].[OnsiteCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnsiteCourse_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[OnsiteCourse] CHECK 
       CONSTRAINT [FK_OnsiteCourse_Course];
	GO

	-- Define the relationship between OnlineCourse and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnlineCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnlineCourse]'))
	ALTER TABLE [dbo].[OnlineCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnlineCourse_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[OnlineCourse] CHECK 
       CONSTRAINT [FK_OnlineCourse_Course];
	GO
	-- Define the relationship between StudentGrade and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))
	ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Course];
	GO

	--Define the relationship between StudentGrade and Student.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Student]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))	
	ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Student] FOREIGN KEY([StudentID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Student];
	GO

	-- Define the relationship between CourseInstructor and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
  	 WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Course]')
  	 AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
	ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
  	 CONSTRAINT [FK_CourseInstructor_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[CourseInstructor] CHECK 
 	  CONSTRAINT [FK_CourseInstructor_Course];
	GO

	-- Define the relationship between CourseInstructor and Person.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
 	  WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Person]')
	   AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
	ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
 	  CONSTRAINT [FK_CourseInstructor_Person] FOREIGN KEY([PersonID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[CourseInstructor] CHECK 
  	 CONSTRAINT [FK_CourseInstructor_Person];
	GO

	-- Define the relationship between Course and Department.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
	ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
	REFERENCES [dbo].[Department] ([DepartmentID]);
	GO
	ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
	GO

	--Define the relationship between OfficeAssignment and Person.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
	  WHERE object_id = OBJECT_ID(N'[dbo].[FK_OfficeAssignment_Person]')
 	  AND parent_object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]'))
	ALTER TABLE [dbo].[OfficeAssignment]  WITH CHECK ADD  
 	  CONSTRAINT [FK_OfficeAssignment_Person] FOREIGN KEY([InstructorID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[OfficeAssignment] CHECK 
   	 CONSTRAINT [FK_OfficeAssignment_Person];
	GO
</pre></div>

Em seguida, copie e execute o script Insert Data.

<div style="width:auto; height:300px; overflow:auto"><pre>
	-- Insert data into the Person table.
	SET IDENTITY_INSERT dbo.Person ON;
	GO
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
	GO
	SET IDENTITY_INSERT dbo.Person OFF;
	GO
	-- Insert data into the Department table.
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (1, 'Engineering', 350000.00, '2007-09-01', 2);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (2, 'English', 120000.00, '2007-09-01', 6);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (4, 'Economics', 200000.00, '2007-09-01', 4);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (7, 'Mathematics', 250000.00, '2007-09-01', 3);
	GO
	-- Insert data into the Course table.
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1050, 'Chemistry', 4, 1);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1061, 'Physics', 4, 1);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1045, 'Calculus', 4, 7);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2030, 'Poetry', 2, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2021, 'Composition', 3, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2042, 'Literature', 4, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4022, 'Microeconomics', 3, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4041, 'Macroeconomics', 3, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4061, 'Quantitative', 2, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (3141, 'Trigonometry', 4, 7);
	GO
	-- Insert data into the OnlineCourse table.
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2030, 'http://www.fineartschool.net/Poetry');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2021, 'http://www.fineartschool.net/Composition');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (4041, 'http://www.fineartschool.net/Macroeconomics');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (3141, 'http://www.fineartschool.net/Trigonometry');
	--Insert data into OnsiteCourse table.
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1050, '123 Smith', 'MTWH', '11:30');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1061, '234 Smith', 'TWHF', '13:15');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1045, '121 Smith','MWHF', '15:30');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (4061, '22 Williams', 'TH', '11:15');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (2042, '225 Adams', 'MTWH', '11:00');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (4022, '23 Williams', 'MWF', '9:00');
	-- Insert data into the CourseInstructor table.
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1050, 1);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1061, 31);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1045, 5);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2030, 4);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2021, 27);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2042, 25);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4022, 18);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4041, 32);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4061, 34);
	GO
	--Insert data into the OfficeAssignment table.
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (1, '17 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (4, '29 Adams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (5, '37 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (18, '143 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (25, '57 Adams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (27, '271 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (31, '131 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (32, '203 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (34, '213 Smith');
	-- Insert data into the StudentGrade table.
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 2, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2030, 2, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 3, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2030, 3, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 6, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 6, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 7, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 7, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 8, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 8, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 9, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 10, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 11, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 12, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 12, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 14, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 13, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 13, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 14, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 15, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 16, 2);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 17, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 19, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 20, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 21, 2);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 22, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 22, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 22, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 23, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1045, 23, 1.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 24, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 25, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 26, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 26, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 27, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1045, 28, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 28, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 29, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 30, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 30, 4);
	GO
</pre></div>

   Agora você tem um banco de dados local que pode ser exportado para o Azure. Em seguida, você executará um assistente que criará um arquivo .bacpac, carregará o assistente no Azure e o importará para o Banco de Dados SQL.


<h3><a id="DeployDB"></a>Implantar no Banco de Dados SQL</h3>

1. No Management Studio, conecte-se a uma instância do SQL Server local que tenha um banco de dados que você deseja migrar.

2. Clique com o botão direito do mouse no banco de dados School que você acabou de criar, aponte para **Tarefas**e clique em **Implantar Banco de Dados no Banco de Dados SQL**.

3. Em Configurações da Implantação, digite um nome para o banco de dados, por exemplo, *school*. 

4. Clique em **Conectar**.

5. Em Nome do servidor, digite um nome de servidor de 10 caracteres, seguido por .databases.windows.net.

6. Em Autenticação, escolha **Autenticação do SQL Server**.

7. Digite o nome de logon e a senha do administrador que você provisionou ao criar o servidor lógico do Banco de Dados SQL.

8. Clique em **Opções**.

9.  Em Propriedades da Conexão, em Conectar-se ao banco de dados, digite **mestre**.

10. Clique em **Conectar**. Essa etapa conclui a especificação da conexão e leva você de volta ao assistente.


11. Clique em **Avançar** e clique em **Concluir** para executar o assistente.

<h3><a id="VerifyDBMigration"></a>Verificar a implantação do banco de dados</h3>

1. No Management Studio, conecte-se ao servidor local. Se já tiver uma conexão aberta, você poderá fechá-la e abrir uma nova. A conexão existente mostra apenas os bancos de dados que estavam sendo executados no momento em que a conexão foi feita.

   Para obter instruções sobre como se conectar a um servidor lógico, consulte [Conectar-se usando o Management Studio][] neste documento. 

2. Expanda a pasta Databases. Você deve ver o banco de dados school na lista.

3. Clique com o botão direito do mouse no banco de dados school e clique em **Nova Consulta**.

4. Execute a consulta a seguir para verificar se os dados estão acessíveis.

<div style="width:auto; height:auto; overflow:auto"><pre>
	SELECT
		Course.Title as "Course Title"
  		,Department.Name as "Department"
  		,Person.LastName as "Instructor"
  		,OnsiteCourse.Location as "Location"
  		,OnsiteCourse.Days as "Days"
  		,OnsiteCourse.Time as "Time"
	FROM
 	 Course
 	 INNER JOIN Department
  	  ON Course.DepartmentID = Department.DepartmentID
 	 INNER JOIN CourseInstructor
 	   ON Course.CourseID = CourseInstructor.CourseID
 	 INNER JOIN Person
 	   ON CourseInstructor.PersonID = Person.PersonID
 	 INNER JOIN OnsiteCourse
		ON OnsiteCourse.CourseID = CourseInstructor.CourseID;
</pre></div>


<h2><a id="HowTo2"></a>Adicionar logons e usuários</h2>

Depois de implantar um banco de dados, você precisará configurar logons e atribuir permissões. Na próxima etapa, você executará dois scripts.

Para o primeiro script, você se conectará ao mestre e executará um script que cria os logons. Os logons serão usados para oferecer suporte às operações de leitura e gravação e para delegar as tarefas operacionais OLE_LINK1OLE_LINK2, como a capacidade de executar consultas do sistema sem as permissões OLE_LINK1OLE_LINK2‘sa’.

Os logons que você criar devem ser de autenticação do SQL Server. Se você já tiver scripts prontos que usam as identidades de usuário do Windows ou as identidades baseadas em declarações, esse script não funcionará no Banco de Dados SQL.

O segundo script atribui permissões de usuário do banco de dados. Para esse script, você se conectará a um banco de dados já carregado no Azure.

<h3><a id="CreateLogins"></a>Criar logons</h3>

1. No Management Studio, conecte-se a um servidor lógico no Azure, expanda a pasta Databases, clique com o botão direito do mouse em **mestre**e selecione **Nova Consulta**.

2. Use as seguintes instruções Transact-SQL para criar logons. Substitua a senha por uma senha válida. Selecione cada um individualmente e, em seguida, clique em **Executar**. Repita para os logons restantes.

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on master, execute each line separately
    -- use this login to manage other logins on this server
    CREATE LOGIN sqladmin WITH password='&lt;ProvidePassword&gt;'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- use this login to create or copy a database
    CREATE LOGIN sqlops WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
</pre></div>


<h3><a id="CreateDBUsers"></a>Criar usuários de banco de dados</h3>

1. Expanda a pasta Databases, clique com o botão do mouse em **school** e selecione **Nova Consulta**.

2. Use as seguintes instruções Transact-SQL para adicionar os usuários do banco de dados. Substitua a senha por uma senha válida. 

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on a regular database, execute each line separately
    -- use this login for read operations
    CREATE LOGIN sqlreader WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- use this login for write operations
    CREATE LOGIN sqlwriter WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- grant DMV permissions on the school database to 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
</pre></div>

<h3><a id="ViewLogins"></a>Exibir e testar logons</h3>

1. Em uma nova janela de consulta, conecte-se a **mestre** e execute a seguinte instrução: 

        SELECT * from sys.sql_logins;

2. No Management Studio, clique com botão direito do mouse no banco de dados **school** e selecione **Nova Consulta**.

3. No menu Consulta, aponte para **Conexão**e, em seguida, clique em **Alterar Conexão**.

4. Faça logon como *sqlreader*.

5. Copie e tente executar a instrução a seguir. Uma mensagem de erro deverá ser exibida informando que o objeto não existe.

        INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6. Abra uma segunda janela de consulta e altere o contexto de conexão para *sqlwriter*. A mesma consulta agora deve ser executada com êxito.

Agora você criou e testou vários logons. Para obter mais informações, consulte [Gerenciando bancos de dados e logons no Banco de Dados SQL][] e [Monitorando o Banco de Dados SQL usando as exibições de gerenciamento dinâmico][].


<h2><a id="HowTo3"></a>Monitorar servidores lógicos e instâncias de bancos de dados</h2>

As ferramentas e as técnicas de monitoramento que você pode estar acostumado a usar em um servidor local, como a auditoria de logons, a execução de rastreamentos e o uso de contadores de desempenho, não estão disponíveis para o Banco de Dados SQL. No Azure, você deve usar DMVs (Data Management Views - Exibições de Gerenciamento de Dados) para monitorar a capacidade de dados, os problemas de consulta e as conexões atuais. 

Para obter mais informações, consulte [Monitorando o Banco de Dados SQL usando as exibições de gerenciamento dinâmico][].


<h2><a id="HowTo4"></a>Dimensionar uma solução de Banco de Dados SQL</h2>

No Azure, a escalabilidade do banco de dados é sinônimo de dimensionamento, na qual uma carga de trabalho é redistribuída entre vários servidores de commodity em um data center. O dimensionamento é uma estratégia para lidar com problemas de desempenho ou capacidade de dados. Um banco de dados muito grande que esteja em uma trajetória de alto crescimento acabará exigindo uma estratégia de dimensionamento, seja ele acessado por alguns ou vários usuários.

O dimensionamento no Azure é mais facilmente obtido por meio da federação. A federação do Banco de Dados SQL baseia-se na fragmentação horizontal, na qual uma ou mais tabelas são divididas por linha e repartidas entre vários membros da federação. 

A federação não é a única resposta para cada problema de escalabilidade. Às vezes, as características dos seus requisitos de dados ou de aplicativo apontam para abordagens mais simples. A lista a seguir apresenta possíveis soluções em ordem de complexidade.

**Aumentar o tamanho do banco de dados**

Os bancos de dados são criados com um tamanho fixo sujeito a um máximo imposto por cada edição. Para a Web edition, você pode aumentar um banco de dados até um máximo de 5 gigabytes. Para a Business Edition, o tamanho máximo do banco de dados é de 150 gigabytes. A forma mais óbvia para aumentar a capacidade de dados é alterar a edição e o tamanho máximo:

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

**Usar vários bancos de dados e alocar usuários**

Em cenários limitados, você pode criar cópias de um banco de dados e, em seguida, alocar logins e usuários em cada banco de dados. Antes que a federação fosse uma opção, essa era uma abordagem comum para redistribuir uma carga de trabalho. Essa abordagem é viável para bancos de dados usados a curto prazo e que depois são mesclados posteriormente em um banco de dados primário que é mantido no local e para soluções que oferecem dados somente leitura.

**Usar federações**

As federações no Banco de Dados SQL são usadas para atingir uma maior escalabilidade e desempenho. Uma ou mais tabelas em um banco de dados são divididas por linha e repartidas em vários bancos de dados (membros da Federação). Esse tipo de particionamento OLE_LINK3OLE_LINK4 horizontal é normalmente conhecido como 'fragmentação' OLE_LINK3OLE_LINK4as. Os principais cenários em que isso é útil são aqueles nos quais você precisa obter escala, desempenho ou para gerenciar a capacidade. 

As federações são suportadas no Business Edition. Para obter mais informações, consulte [Federações no Banco de Dados SQL][] e [Tutorial de Federações do Banco de Dados SQL - DBA][].

**Considerar outras formas de armazenamento**

Lembre-se de que o Azure oferece suporte a várias formas de armazenamento de dados, incluindo o armazenamento de tabelas e de blobs. Se você não precisar de recursos relacionais, o armazenamento de tabelas ou de blobs pode ser mais econômico. 

<h2><a id="NextSteps"></a>Próximas etapas</h2>

Agora que você aprendeu as noções básicas de administração do Banco de Dados SQL, siga estes links para saber como executar tarefas administrativas mais complexas.

* Consulte [Banco de Dados SQL][] no MSDN
* Visite o [TechNet WIKI do Banco de Dados SQL][]



[Entrar no Azure]: #PreReq1
[Configurar o Banco de Dados SQL]: #PreReq2
[Conectar-se usando o Management Studio]: #PreReq3
[Implantar um banco de dados no Azure]: #HowTo1
[Adicionar logons e usuários]: #HowTo2
[Monitorar servidores lógicos e instâncias de bancos de dados]: #HowTo3
[Dimensionar uma solução de Banco de Dados SQL]: #HowTo4
[Próximas etapas]: #NextSteps

[Banco de Dados SQL]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg619386

[TechNet WIKI do Banco de Dados SQL]: http://social.technet.microsoft.com/wiki/contents/articles/2267.sql-azure-technet-wiki-articles-index-en-us.aspx

[Como usar o Banco de Dados SQL]: http://www.windowsazure.com/pt-br/develop/net/how-to-guides/sql-azure/
[Federações no Banco de Dados SQL]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh597452.aspx
[Tutorial de Federações do Banco de Dados SQL - DBA]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh778416.aspx
[Gerenciando o Banco de Dados SQL usando o Management Studio]: http://www.windowsazure.com/pt-br/develop/net/common-tasks/sql-azure-management/
[Monitorando o Banco de Dados SQL usando as exibições de gerenciamento dinâmico]: http://msdn.microsoft.com/pt-br/library/windowsazure/ff394114.aspx
[Apresentando a replicação geográfica para o armazenamento do Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
[Como criar uma conta de armazenamento para uma assinatura do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg433066.aspx
[Baixar o SDK do Azure]: http://www.microsoft.com/pt-br/download/details.aspx?id=15658
[Ferramentas de gerenciamento do Azure]: http://wapmmc.codeplex.com/
[Introdução à administração de Banco de Dados SQL]: http://www.windowsazure.com/pt-br/manage/tutorials/sql-azure-management/  
[Gerenciando bancos de dados e logons no Banco de Dados SQL]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336235.aspx
[Como usar o serviço de armazenamento de blob]: https://www.windowsazure.com/pt-br/develop/net/how-to-guides/blob-storage/
[Cliente do serviço de importação/exportação do Banco de Dados SQL do DAC v 1.5]: http://sqldacexamples.codeplex.com/releases/view/85948


[Adventure Works para o Banco de Dados SQL]: http://msftdbprodsamples.codeplex.com/releases/view/37304


