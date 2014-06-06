<properties linkid="manage-services-getting-started-with-sqldbs" urlDisplayName="Como criar e provisionar" pageTitle="Introdução ao banco de dados SQL - Azure" metaKeywords="" description="Introdução à criação e ao gerenciamento de bancos de dados SQL no Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Introdução ao banco de dados SQL do Azure" authors=""  solutions="" writer="" manager="" editor=""  />





#Introdução ao banco de dados SQL do Azure

Neste tutorial você aprenderá os fundamentos da administração do Banco de Dados SQL do Azure usando o Portal de Gerenciamento do Azure. Se você for novo na administração de banco de dados, poderá seguir estas lições para aprender habilidades essenciais em cerca de 30 minutos.
 

Este tutorial não pressupõe nenhuma experiência anterior com o SQL Server ou o Banco de Dados SQL do Azure. Após a conclusão deste tutorial, você terá um exemplo de banco de dados no Azure e um entendimento de como executar tarefas básicas de administração usando o Portal de Gerenciamento.

Você irá criar e provisionar um banco de dados de exemplo no Azure e um sistema de consulta e dados de usuário usando o Excel e outros aplicativos.


##Sumário##

* [Etapa 1: criar uma conta do Azure](#Subscribe)
* [Etapa 2: conectar-se ao Azure e criar um banco de dados](#Connect)
* [Etapa 3: configurar o firewall](#ConfigFirewall)
* [Etapa 4: adicionar dados e um esquema usando um script Transact-SQL](#AddData)
* [Etapa 5: criar o esquema](#createschema)
* [Etapa 6: inserir dados](#insertData)
* [Etapa 7: exemplo de consulta e dados do sistema no Portal de Gerenciamento do Banco de Dados SQL](#QueryDBSysData)
* [Etapa 8: criar um logon de banco de dados e atribuir permissões](#DBLogin)
* [Etapa 9: conectar-se de outros aplicativos](#ClientConnection)
* [Etapa 10: configurar a Sincronização de Dados do SQL](#ConfigureDataSync)


<h2><a id="Subscribe"></a>Etapa 1: criar uma conta do Azure</h2>

1. Abra um navegador da Web e navegue para [http://www.windowsazure.com](http://www.windowsazure.com).
Para começar com uma conta gratuita, clique em avaliação gratuita no canto superior direito e siga as etapas.

2. Sua conta agora está criada. Você está pronto para começar.


<h2><a id="Connect"></a>Etapa 2: conectar-se ao Azure e criar um banco de dados</h2>


1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com). Você deve ver um painel de navegação semelhante a este. 

	![Image1](./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png)

2. Clique em **Novo** na parte inferior da página. Quando você clica em **Novo**, uma lista rola a tela para cima mostrando coisas que você pode criar.

3. Clique em **Banco de Dados SQL** e, em seguida, clique em **Criação Personalizada**. 

	![Image2](./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png)

	A escolha dessa opção permite que você crie um novo servidor ao mesmo tempo, com você como o administrador. Como o administrador do sistema, você pode executar mais tarefas, incluindo a conexão ao Portal de Gerenciamento do Banco de Dados SQL, o que você irá fazer mais tarde neste tutorial.  

4.  A página Configurações do Banco de Dados é exibida quando você clica em **Criação Personalizada**. Nesta página, você fornece informações básicas que criam um banco de dados vazio no servidor. A adição de tabelas e dados será apresentada em uma etapa posterior. 

	Preencha a página Configurações do Banco de Dados da seguinte maneira:

	![Image3](./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG)

	* Insira **Escola** como o nome do banco de dados. 

	* Use as configurações padrão de edição, tamanho máximo e agrupamento. 

	* Escolha **Novo Servidor de Banco de Dados SQL**. Selecionar um novo servidor adiciona uma segunda página que iremos usar para definir a conta do administrador e a região. 

	* Ao concluir, clique na seta para ir para a próxima página.


5. Preencha as Configurações do Servidor da seguinte maneira: 

	![Image4](./media/sql-database-get-started/4ServerSettings_SQLTut.PNG)

	* Digite um nome de administrador como uma palavra sem espaços. O Banco de Dados SQL usa a Autenticação do SQL através de uma conexão criptografada para validar a identidade do usuário. Será criado um novo logon de autenticação do SQL Server que tem permissões de administrador usando o nome que você fornecer. O nome do administrador não pode ser um usuário do Windows e também não deve ser uma ID do Windows Live. A Autenticação do Windows não é suportada no Banco de Dados SQL.

	* Forneça uma senha forte que tenha mais de oito caracteres, usando uma combinação de letras maiúsculas e minúsculas e um número ou um símbolo.

	* Escolha uma região. A Região determina a localização geográfica do servidor. As Regiões não podem ser facilmente alternadas, portanto, escolha uma que faça sentido para este servidor. Escolha um local mais próximo de você ou de seus usuários. Mantendo seu aplicativo do Azure e o banco de dados na mesma região, você economiza em custo de largura de banda de egresso e em latência de dados.

	* Mantenha a caixa de seleção **Permitir que os serviços do Azure Services acessem este servidor** selecionada, para que você possa conectar-se ao banco de dados usando o Portal de Gerenciamento do Banco de Dados SQL, o Excel no Office 365 ou os Relatórios SQL do Azure.

	* Clique na marca de seleção na parte inferior da página quando tiver concluído.

	Observe que você não especificou um nome de servidor. Como o servidor de Banco de Dados SQL deve ser acessível em todo o mundo, o Banco de Dados SQL configura as entradas DNS apropriadas quando o servidor é criado. O nome gerado garante que não há colisões de nome com outras entradas DNS. Você não pode alterar o nome do seu servidor de Banco de Dados SQL.

	Na próxima etapa, você irá configurar o firewall para que as conexões de aplicativos em execução no seu computador tenham permissão para acessar os bancos de dados em seu servidor de Banco de Dados SQL.



<h2><a id="ConfigFirewall"></a>Etapa 3: configurar o Firewall</h2>

Para configurar o firewall para que as conexões sejam permitidas através dele, você digitará informações na página do servidor.

**Observação:** o serviço de Banco de Dados SQL só está disponível com a porta TCP 1433 usada pelo protocolo TDS, portanto certifique-se de que o firewall no seu computador local e na sua rede permita a comunicação de saída TCP na porta 1433. Para obter mais informações, consulte [Firewall do Banco de Dados SQL (a página pode estar em inglês)](http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-en-us.aspx)


1. No painel de navegação à esquerda, clique em **Bancos de Dados SQL**.

2. Clique em **Servidores** na parte superior da página. Em seguida, clique no servidor que você acabou de criar para ver uma seta branca à direita. Clique na seta para abrir a página do servidor.

	![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

3. Na página do servidor, clique em **Configurar** para abrir os parâmetros da configuração do firewall e especificar a regra da seguinte maneira: 

	![Image6](./media/sql-database-get-started/6DBConfigFirewall_SQLTut.png)


	* Copie o endereço IP atual do cliente. Esse é o endereço IP no qual o roteador ou o servidor proxy está escutando. O Banco de Dados SQL detecta o endereço IP usado pela conexão atual para que você possa criar uma regra de firewall para aceitar solicitações de conexão nesse dispositivo. 
	
	* Cole o endereço IP no intervalo de início e fim. Mais tarde, se você encontrar erros de conexão indicando que o intervalo é muito estreito, você poderá editar essa regra para aumentar o intervalo.

	* Digite um nome para a regra de firewall, como o nome de seu computador ou empresa.

	* Clique na marca de seleção para salvar a regra.

	Depois que você salvar a regra, sua página terá aparência semelhante à seguinte captura de tela.

	![Image7](./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png)


4. Clique em **Salvar** na parte inferior da página para concluir a etapa. Se você não vir a opção **Salvar**, atualize a página do navegador.

Agora você tem um servidor de Banco de Dados SQL no Azure, uma regra de firewall que permite o acesso ao servidor, um objeto de banco de dados e um logon de administrador. Em seguida, você usará a janela de consulta no Portal de Gerenciamento de Banco de Dados SQL para executar um script Transact-SQL para criar um banco de dados predefinido.

Conforme suas habilidades aumentarem, você desejará explorar maneiras adicionais de criar um banco de dados, incluindo abordagens programáticas ou o designer nas Ferramentas de Dados do SQL Server. Se você já tiver um banco de dados SQL Server existente que executa em um servidor local, poderá migrar facilmente esse banco de dados para o servidor do Azure que acabou de configurar. Use os links no final deste tutorial para descobrir como. 



<h2><a id="AddData"></a>Etapa 4: adicionar dados e um esquema usando um script Transact-SQL</h2>

Nesta etapa, você executa dois scripts. O primeiro cria um esquema que define tabelas, colunas e relações. O segundo script adiciona os dados. Cada etapa é executada independentemente em uma conexão separada. Se você tiver criado bancos de dados no SQL Server anteriormente, uma das diferenças que você observará no Banco de Dados SQL é que os comandos CREATE e INSERT devem ser executados em lotes separados. O Banco de Dados SQL impõe esse requisito para minimizar ataques contra os dados enquanto estiverem em trânsito. 

**Observação:** os valores de esquema e de dados são obtidos deste [artigo do MSDN](http://msdn.microsoft.com/pt-br/library/windowsazure/ee621790.aspx "MSDN article") e foram modificados para funcionar com o Banco de Dados SQL.

1. Acesse a página inicial. No [Portal de Gerenciamento](http://manage.windowsazure.com), o Banco de Dados **Escola** aparece na lista de itens na página inicial. 

	![Image8](./media/sql-database-get-started/8MainPageHome_SQLTut.png)


2. Clique em **Escola** para ver uma seta branca à direita. Clique na seta para abrir a página do banco de dados.

	![Image9](./media/sql-database-get-started/9DBListSchool_SQLTut.png)

3. Clique em **Gerenciar** na parte inferior da página. Se não estiver visível, atualize a janela do navegador. Isso abrirá o Portal de Gerenciamento do Banco de Dados SQL. Esse Portal é separado do Portal de Gerenciamento do Azure. Você usará esse portal para executar comandos e consultas Transact-SQL. 
	
	![Image10](./media/sql-database-get-started/10DBPortalManageButton_SQLTut.png)


4. Insira um nome de logon e senha de administrador. Esse é o logon de administrador que você especificou quando criou o servidor.

	![Image11](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)


5. Clique em **Nova Consulta** no Portal de Gerenciamento do Banco de Dados SQL. Uma janela de consulta vazia é aberta na área de trabalho. Na próxima etapa, você usará essa janela para copiar uma série de scripts predefinidos que adicionarão a estrutura e os dados a seu banco de dados vazio.

	![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)
	 

<h2><a id="createschema"></a>Etapa 5: criar o esquema</h2>

Nesta etapa, você criará o esquema usando o seguinte script. O script primeiro verifica se há uma tabela existente de mesmo nome para garantir que não haverá uma colisão de nomes e cria a tabela usando a instrução [CREATE TABLE](http://msdn.microsoft.com/pt-br/library/windowsazure/ee336258.aspx). Além disso, esse script usa a instrução [ALTER TABLE](http://msdn.microsoft.com/pt-br/library/windowsazure/ee336286.aspx) para especificar as relações de chave primária e de tabela.

Copie o script e cole-o na janela de consulta. Clique em **Executar** na parte superior da janela para executar o script.

<div style="width:auto; height:600px; overflow:auto"><pre>
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



<h2><a id="insertData"></a>Etapa 6: inserir dados</h2>

Abra uma nova janela de consulta e, em seguida, cole-a no script a seguir. Execute o script para inserir dados. Esse script usa a instrução [INSERT](http://msdn.microsoft.com/pt-br/library/windowsazure/ee336284.aspx) para adicionar valores a cada coluna.

<div style="width:auto; height:600px; overflow:auto"><pre>
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


<h2><a id="QueryDBSysData"></a>Etapa 7: exemplo de consulta e dados do sistema no Portal de Gerenciamento do Banco de Dados SQL</h2>

Para verificar seu trabalho, execute uma consulta que retorne os dados que você acabou de digitar. Você também pode executar procedimentos armazenados internos e modos de exibição de gerenciamento de dados que fornecem informações sobre os bancos de dados em execução no seu servidor de Banco de Dados SQL.

<h4><a id="QueryDB"></a>Consultar dados de exemplo</h4>

Em uma nova janela de consulta, copie e execute o seguinte script Transact-SQL para recuperar alguns dos dados que você acabou de adicionar.


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

Você deve ver um conjunto de resultados semelhante à ilustração a seguir.

![Image13](./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG)


<h4><a id="QuerySys"></a>Consultar dados do sistema</h4>

Você também pode usar exibições do sistema e procedimentos armazenados internos para obter informações do servidor. Para o objetivo deste tutorial, você tentará apenas alguns comandos.

Execute o seguinte comando para descobrir quais bancos de dados estão disponíveis no servidor. 

	SELECT * FROM sys.databases  

Execute este comando para retornar uma lista de usuários atualmente conectados ao servidor.

	SELECT user_name(),suser_sname()

Execute este procedimento armazenado para retornar uma lista de todos os objetos no banco de dados **Escola**.

	EXEC SP_help

Não feche a conexão do portal ao banco de dados **Escola**. Você precisará dela novamente em alguns minutos.



<h2><a id="DBLogin"></a>Etapa 8: criar um logon de banco de dados e atribuir permissões</h2>

No Banco de Dados SQL, você pode criar logons e conceder permissões usando o Transact-SQL. Nesta lição, usando o Transact-SQL, você irá fazer três coisas: criar um logon de autenticação do SQL Server, criar um usuário de banco de dados e conceder permissões por meio da associação de função.

Um logon de autenticação do SQL Server é usado para conexões com o servidor. Todos os usuários que acessam um servidor de Banco de Dados fazem isso fornecendo um nome de logon e uma senha de autenticação do SQL Server. 

Para criar um logon, você deve primeiro conectar-se ao banco de dados **mestre**.

<h4><a id="CreateLogin"></a>Criar um logon de autenticação do SQL Server</h4>

1. No [Portal de Gerenciamento](http://manage.windowsazure.com), selecione **Bancos de Dados SQL**, clique em **Servidores**, escolha o servidor e, em seguida, clique na seta branca para abrir a página do servidor. 

	![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

2. Na página Início Rápido, clique em **Gerenciar Servidor** para abrir uma nova conexão com o Portal de Gerenciamento do Banco de Dados SQL. 

3. Insira um nome de administrador e senha. Esse é o logon de administrador que você especificou quando criou o servidor.

	![Image20](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)


4. O Portal de Gerenciamento do Banco de Dados SQL abre em uma nova janela do navegador. Clique em **Selecionar um Banco de Dados** na parte superior e clique em **mestre**.

	![Image14](./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG)

5. Se você vir um erro na página semelhante ao seguinte, ignore-o. Clique em **Nova Consulta** para abrir uma janela de consulta que permite executar comandos Transact-SQL no banco de dados **mestre**.

	![Image15](./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG)

6. Copie e cole o seguinte comando na janela de consulta.

        CREATE LOGIN SQLDBLogin WITH password='Password1';

7. Execute o comando para criar um novo logon do SQL Server chamado 'SQLDBLogin'.


<h4><a id="CreateDBuser"></a>Criar um usuário de banco de dados e atribuir permissões</h4>

Depois de criar um logon de autenticação do SQL Server, a próxima etapa é atribuir o banco de dados e os níveis de permissão associados ao logon. Você faz isso criando um **usuário de banco de dados** em cada banco de dados.

1. Volte para a página Portal de Gerenciamento do Banco de Dados SQL que se conecta ao banco de dados **Escola**. Se você fechou a janela do navegador, inicie uma nova conexão ao banco de dados **Escola** usando as etapas da lição anterior, "Adicionar dados e um esquema usando um script Transact-SQL". 

	Na página do Portal de Gerenciamento do Banco de Dados SQL, o nome do banco de dados **Escola** está visível no canto superior esquerdo.

	![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)

2. Clique em **Nova Consulta** para abrir uma nova janela de consulta e copiar na instrução a seguir. 

	    CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3. Execute o script. Esse script cria um novo usuário de banco de dados baseado no logon.

	Em seguida, você poderá atribuir permissões usando a função db_datareader. Os usuários do banco de dados atribuídos a essa função podem ler todos os dados de todas as tabelas de usuário no banco de dados. 

4. Abra uma nova janela de consulta e digite e execute a instrução a seguir. Essa instrução executa um procedimento armazenado interno que atribui a função db_datareader ao novo usuário que você acabou de criar. 

        EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

	Agora você tem um novo logon de autenticação do SQL Server que tem permissão somente leitura para o banco de dados **Escola**. Usando essas etapas, você pode criar outros logons de autenticação do SQL Server para permitir diferentes níveis de acesso aos seus dados.




<h2><a id="ClientConnection"></a>Etapa 9: conectar-se de outros aplicativos</h2>

Agora que você tem um banco de dados operacional, você pode conectá-lo em uma pasta de trabalho Excel.

<h4>Conectar-se a partir do Excel</h4>


Se o Excel 2010 estiver instalado em seu computador, você poderá usar as etapas a seguir para se conectar a seu banco de dados de exemplo.

1. No Excel, na guia Dados, clique em **De Outras Origens**e, em seguida, clique em **Do SQL Server**.

2. No assistente para Conexão de Dados, digite o nome de domínio totalmente qualificado do seu Servidor de Banco de Dados SQL, seguido de um logon de Autenticação do SQL Server que tem permissão para acessar o banco de dados. 

  Você pode localizar o nome do servidor na página **Banco de Dados** em **Links rápidos**. O nome do servidor também pode ser localizado no Portal de Gerenciamento do Azure, no Banco de Dados SQL, na página Servidor, no Painel, em **Gerenciar URL**.

  O nome do servidor consiste em uma série de letras e números, seguidos de '.database.windows.net'. Especifique esse nome no assistente de Conexão do Banco de Dados. Não inclua o prefixo http:// ou https:// ao especificar o nome.

  Insira um logon de autenticação do SQL Server. Para fins de teste, você pode usar o logon de administrador que criou quando configurou o servidor. Para acesso normal a dados, use um logon de usuário de banco de dados semelhante ao que você acabou de criar.

	![Image16](./media/sql-database-get-started/16ExcelConnect_SQLTut.png)


3.  Na próxima página, escolha o banco de dados **Escola** e, em seguida, escolha **Curso**. Clique em **Concluir**.

	![Image17](./media/sql-database-get-started/17ExcelSelect_SQLTut.png)

4. A caixa de diálogo Importar Dados é exibida solicitando que você selecione como e onde importar seus dados. Com as opções padrão selecionadas, clique em **OK**.

	![Image19](./media/sql-database-get-started/19ExcelImport_SQLTut.png)


5. Na planilha, você deverá ver uma tabela semelhante à seguinte. 
	
	![Image18](./media/sql-database-get-started/18ExcelTable_SQLTut.PNG)

Usando somente o Excel, você pode importar apenas uma tabela por vez. Uma abordagem melhor é usar o suplemento PowerPivot para Excel, que permite que você importe e trabalhe com várias tabelas como um único conjunto de dados. Trabalhar com o PowerPivot está além do escopo deste tutorial, mas você pode obter mais informações sobre isso em [Site da Microsoft (a página pode estar em inglês)](http://www.microsoft.com/pt-br/bi/powerpivot.aspx).

<h2><a id="ConfigureDataSync"></a>Etapa 10: configurar a Sincronização de Dados do SQL</h2>

<h4>Sincronização de Dados do SQL</h4>

Agora que você criou suas instâncias de Banco de Dados SQL, você pode utilizar a Sincronização de Dados do SQL para manter seus dados de alto valor sincronizados em vários locais.

A Sincronização de Dados do SQL é um recurso do Banco de Dados SQL que permite sincronizar dados selecionados tanto em uma agenda quanto sob demanda, sem escrever qualquer código ou scripts. A Sincronização de Dados do SQL oferece suporte à sincronização entre instâncias de Banco de Dados SQL ou topologias híbridas que incluem Bancos de Dados SQL e instâncias do SQL Server.

Para obter mais informações sobre Sincronização de Dados do SQL, consulte [Introdução à Sincronização de Dados do SQL (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=274959).

<h2><a id="NextSteps"></a>Próximas etapas</h2>

Agora que você está familiarizado com o Banco de Dados SQL e os Portais de Gerenciamento, você pode experimentar outras ferramentas e técnicas usadas pelos administradores de Banco de Dados do SQL Server.

Para gerenciar ativamente o seu novo banco de dados, considere a possibilidade de instalar e usar o SQL Server Management Studio. O Management Studio é a ferramenta de administração de banco de dados primário para o gerenciamento de bancos de dados do SQL Server, inclusive aqueles em execução no Azure. Usando o Management Studio, você pode salvar consultas para uso futuro, adicionar novas tabelas e procedimentos armazenados e aprimorar suas habilidades no Transact-SQL em um ambiente de script sofisticado que inclui um verificador de sintaxe, intellisense e modelos. Para começar, siga as instruções em [Gerenciando o Banco de Dados do SQL Server usando o SQL Server Management Studio (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/develop/net/common-tasks/sql-azure-management/).

Ter fluência na consulta Transact SQL e na linguagem de definição de dados é essencial para os administradores de Banco de Dados. Se você for novo no Transact-SQL, comece com o [Tutorial: escrevendo instruções Transact-SQL (a página pode estar em inglês)](http://msdn.microsoft.com/pt-br/library/ms365303.aspx) para aprender algumas técnicas básicas.

Existem outros métodos para mover um banco de dados local para o Banco de Dados SQL. Se você tiver bancos de dados existentes, ou fez o download de exemplos de bancos de dados para praticar, tente as seguintes abordagens alternativas:

* [Migrando bancos de dados para o Banco de Dados SQL](http://msdn.microsoft.com/pt-br/library/windowsazure/ee730904.aspx)
* [Copiando bancos de dados no Banco de Dados SQL](http://msdn.microsoft.com/pt-br/library/windowsazure/ff951624.aspx)


























