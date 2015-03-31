<properties 
	pageTitle="Introdução ao Banco de Dados SQL - Azure" 
	description="Introdução à criação e ao gerenciamento de Bancos de Dados SQL no Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="jeffgoll" 
	writer="" 
	manager="jeffreyg" 
	editor="tysonn"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/04/2014" 
	ms.author="jeffreyg"/>


## Introdução ao banco de dados SQL do Microsoft Azure

Neste tutorial você aprenderá os fundamentos da administração do Banco de Dados SQL do Microsoft Azure usando o Portal de Gerenciamento do Azure. Se você for novo na administração de banco de dados, poderá seguir estas lições para aprender habilidades essenciais em cerca de 30 minutos. 

Este tutorial não pressupõe nenhuma experiência anterior com o SQL Server ou o Banco de Dados SQL do Azure. Após a conclusão deste tutorial, você terá um exemplo de banco de dados no Azure e um entendimento de como executar tarefas básicas de administração usando o Portal de Gerenciamento.

Você criará e provisionará um banco de dados de exemplo na plataforma do Azure e um sistema de consulta e dados de usuário usando o Excel.




## Etapa 1: Criar uma conta do Microsoft Azure

1. Abra um navegador da web e navegue até [http://azure.microsoft.com](http://azure.microsoft.com).
Para começar com uma conta gratuita, clique em avaliação gratuita no canto superior direito e siga as etapas.

2. Sua conta agora está criada. Você está pronto para começar.


## Etapa 2: Conectar-se ao Azure e criar um banco de dados


1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com). Você deve ver um painel de navegação semelhante a este.

	![Navigation pane][Image1]

2. Clique em **Novo** na parte inferior da página. Quando você clica em **Novo**, uma lista rola a tela para cima mostrando coisas que você pode criar.

3. Clique em **Banco de Dados SQL** e, em seguida, clique em **Criação Personalizada**. 

	![Navigation pane][Image2]

A escolha dessa opção permite que você crie um novo servidor e banco de dados SQL ao mesmo tempo, com você como o administrador. Como o administrador do sistema, você pode executar mais tarefas, incluindo a conexão ao Portal de Gerenciamento do Banco de Dados SQL, o que você vai fazer mais tarde neste tutorial.  

4.  A página Configurações do Banco de Dados é exibida quando você clica em **Criação Personalizada**. Esta página fornece informações básicas que criam um banco de dados SQL vazio no servidor. A adição de tabelas e dados será apresentada em uma etapa posterior. 

    Preencha a página Configurações do Banco de Dados da seguinte maneira:

	![Navigation pane][Image3]

* Insira **Escola** como o nome do banco de dados. 

* Use as configurações padrão de edição, tamanho máximo e agrupamento. 

* Escolha **Novo Servidor de Banco de Dados SQL**. Selecionar um novo servidor adiciona uma segunda página que iremos usar para definir a conta do administrador e a região. 

* Ao concluir, clique na seta para ir para a próxima página.


7. Preencha as Configurações do Servidor da seguinte maneira: 

	![Navigation pane][Image4]

* Digite um nome de administrador como uma palavra sem espaços. O Banco de Dados SQL usa a Autenticação do SQL através de uma conexão criptografada para validar a identidade do usuário. Será criado um novo logon de autenticação do SQL Server que tem permissões de administrador usando o nome que você fornecer. O nome do administrador não pode ser um usuário do Windows e também não deve ser uma ID do nome de usuário do Windows Live. A Autenticação do Windows não tem suporte no Banco de Dados SQL.

* Forneça uma senha forte com mais de oito caracteres, usando uma combinação de letras maiúsculas e minúsculas e um número ou um símbolo. Use a bolha de Ajuda para obter detalhes sobre a complexidade da senha.

* Escolha uma região. A Região determina a localização geográfica do servidor. As Regiões não podem ser facilmente alternadas, portanto, escolha uma que faça sentido para este servidor. Escolha um local mais próximo de você. Ao manter seu aplicativo do Azure e o banco de dados na mesma região, você economiza em custo de largura de banda de entrada e em latência de dados.

* Certifique-se de manter a caixa de seleção **Permitir que os serviços do Azure acessem este servidor**  marcada para que você pode se conectar ao banco de dados usando o Portal de Gerenciamento do Banco de Dados SQL, o Excel no Office 365 ou relatórios SQL do Azure.

* Clique na marca de seleção localizada na parte inferior da página quando tiver concluído.

Observe que você não especificou um nome do servidor. Como o servidor do Banco de Dados SQL deve ser acessível em todo o mundo, o Banco de Dados SQL configura as entradas DNS apropriadas quando o servidor é criado. O nome gerado garante que não há colisões de nome com outras entradas DNS. Você não pode alterar o nome do seu servidor de Banco de Dados SQL.

Para ver o nome do servidor que hospeda o banco de dados **Escola** que você acabou de criar, clique em **Bancos de Dados SQL** no painel de navegação à esquerda e, em seguida clique no banco de dados **Escola** na exibição da lista dos **Bancos de Dados SQL**. Na página **Início Rápido**, role para baixo para ver o nome do servidor.

Na próxima etapa, você vai configurar o firewall para que as conexões de aplicativos em execução no seu computador tenham permissão para acessar os bancos de dados em seu servidor do Banco de Dados SQL.



## Etapa 3: Configurar o firewall

Para configurar o firewall para que as conexões sejam permitidas através dele, você digitará informações na página do servidor.

**Observação:** o serviço de Banco de Dados SQL só está disponível com a porta TCP 1433 usada pelo protocolo TDS, portanto certifique-se de que o firewall no seu computador local e na sua rede permita a comunicação de saída TCP na porta 1433. Para obter mais informações, consulte [Firewall do Banco de Dados SQL (a página pode estar em inglês)](http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-pt-br.aspx)


1. No painel de navegação à esquerda, clique em **Bancos de Dados SQL**.

2. Clique em **Servidores** na parte superior da página. Em seguida, clique no servidor que você acabou de criar para abrir a página do servidor.

3. Na página do servidor, clique em **Configurar** para abrir as configurações de **Endereços de IP Permitidos** e, em seguida, clique no link de **Adicionar os Endereços de IP permitidos**. Isso criará uma nova regra de firewall para permitir solicitações de conexão por meio do roteador ou servidor proxy que o seu dispositivo está escutando.

4. Você pode criar regras de firewall adicional ao especificar um nome de regra e os valores de intervalo de IP inicial e final.

5. Para habilitar as interações entre este servidor e os outros serviços da Azure, clique em **Sim** para a opção de **Serviços do Microsoft Azure**. 

7. Para salvar suas alterações, clique em **Salvar** na parte inferior da página.

6. Depois que você salvar a regra, sua página terá aparência semelhante à seguinte captura de tela.

	![Navigation pane][Image7]

Agora você tem um servidor do Banco de Dados SQL no Azure, uma regra de firewall que permite o acesso ao servidor, um objeto de banco de dados e um logon de administrador. Mas você ainda não tem um banco de dados de trabalho que você possa consultar. Para fazer isso, seu banco de dados deve ter um esquema e dados reais.

Por causa deste tutorial usa apenas as ferramentas disponíveis, você usará a janela de consulta no Portal de Gerenciamento de Banco de Dados SQL para executar um script Transact-SQL para compilar um banco de dados predefinido.

Conforme suas habilidades aumentarem, você desejará explorar maneiras adicionais de criar um banco de dados, incluindo abordagens programáticas ou a superfície do designer nas Ferramentas de Dados do SQL Server. Se você já tiver um banco de dados SQL Server existente que é executado em um servidor local, poderá migrar facilmente esse banco de dados para o servidor do Azure que acabou de configurar. Use os links no final deste tutorial para descobrir como. 



## Etapa 4: Adicionar dados e um esquema usando um script Transact-SQL

Nesta etapa, você executa dois scripts. O primeiro cria um esquema que define tabelas, colunas e relações. O segundo script adiciona os dados. Cada etapa é executada independentemente em uma conexão separada. Se você tiver criado bancos de dados no SQL Server anteriormente, uma das diferenças que você observará no Banco de Dados SQL é que os comandos CREATE e INSERT devem ser executados em lotes separados. O Banco de Dados SQL impõe esse requisito para minimizar ataques contra os dados enquanto estiverem em trânsito. 

**Observação:** os valores de esquema e de dados são obtidos deste [artigo do MSDN](http://msdn.microsoft.com/library/windowsazure/ee621790.aspx "MSDN article") e foram modificados para funcionar com o Banco de Dados SQL.

1. Acesse a página inicial. No [Portal de Gerenciamento](http://manage.windowsazure.com), o Banco de Dados **Escola** aparece na lista de itens na página inicial.

	![Navigation pane][Image8]

2. Clique em **Escola** para selecioná-la e, em seguida, clique em **Gerenciar** na parte inferior da página. Isso abrirá o Portal de Gerenciamento do Banco de Dados SQL. Esse Portal é separado do Portal de Gerenciamento do Azure. Você usará esse portal para executar comandos e consultas Transact-SQL.

3. Insira um nome de logon e senha de administrador para efetuar logon no banco de dados **Escola**. Esse é o logon de administrador que você especificou quando criou o servidor.

4. Clique em **Nova Consulta** no Portal de Gerenciamento do Banco de Dados SQL, na faixa de opções. Uma janela de consulta vazia abrirá no espaço de trabalho. Na próxima etapa, você usará essa janela para copiar uma série de scripts predefinidos que adicionarão a estrutura e os dados a seu banco de dados vazio.



## Etapa 5: Criar o esquema

Nesta etapa, você criará o esquema usando o seguinte script. O script primeiro verifica se há uma tabela existente de mesmo nome para garantir que não haverá uma colisão de nomes e cria a tabela usando a instrução [CREATE TABLE](http://msdn.microsoft.com/library/windowsazure/ee336258.aspx). Além disso, esse script usa a instrução [ALTER TABLE](http://msdn.microsoft.com/library/windowsazure/ee336286.aspx) para especificar as relações de chave primária e de tabela.

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



## Etapa 6: Inserir dados

Abra uma nova janela de consulta e, em seguida, cole-a no script a seguir. Execute o script para inserir dados. Esse script usa a instrução [INSERT](http://msdn.microsoft.com/library/windowsazure/ee336284.aspx) para adicionar valores a cada coluna.

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
	
</pre></div>


## Etapa 7: Exemplo de consulta e dados do sistema no Portal de Gerenciamento do Banco de Dados SQL

Para verificar seu trabalho, execute uma consulta que retorne os dados que você acabou de digitar. Você também pode executar procedimentos armazenados internos e modos de exibição de gerenciamento de dados que fornecem informações sobre os bancos de dados em execução no seu servidor do Banco de Dados SQL.

<h4 id="QueryDB">Consultar dados de exemplo</h4>

Em uma nova janela de consulta, copie e execute o seguinte script Transact-SQL para recuperar alguns dos dados que você acabou de adicionar.


<div style="width:auto; height:auto; overflow:auto"><pre>
	SELECT * From Person
</pre></div>

Você deve ver um conjunto de resultados que inclui 34 linhas da tabela pessoal, incluindo PersonID, LastName, FirstName, HireDate e EnrollmentDate.


<h4 id="QuerySys">Consultar dados do sistema</h4>

Você também pode usar exibições do sistema e procedimentos armazenados internos para obter informações do servidor. Para o objetivo deste tutorial, você tentará apenas alguns comandos.

Execute o seguinte comando para descobrir quais bancos de dados estão disponíveis no servidor. 

	SELECT * FROM sys.databases  

Execute este comando para retornar uma lista de usuários atualmente conectados ao servidor.

	SELECT user_name(),suser_sname()

Execute este procedimento armazenado para retornar uma lista de todos os objetos no banco de dados **Escola**.

	EXEC SP_help

Não feche a conexão do portal ao banco de dados **Escola**. Você precisará dela novamente em alguns minutos.



## Etapa 8: Criar um logon de banco de dados e atribuir permissões

No Banco de Dados SQL, você pode criar logons e conceder permissões usando o Transact-SQL. Nesta lição, usando o Transact-SQL, você vai fazer três coisas:


1. Criar um logon de autenticação do SQL Server
2. Criar um usuário de banco de dados e
3. Conceder permissões através da associação da função.

Um logon de autenticação do SQL Server é usado para conexões com o servidor. Todos os usuários que acessam um servidor do Banco de Dados SQL fazem isso fornecendo um nome de logon e uma senha de autenticação do SQL Server. 

Para criar um logon, você deve primeiro conectar-se ao banco de dados **mestre**.

<h4 id="CreateLogin">Criar um logon de autenticação do SQL Server</h4>

1. No [Portal de Gerenciamento](http://manage.windowsazure.com), selecione **Bancos de Dados SQL**, clique em **Servidores**, escolha o servidor e, em seguida, clique na seta branca para abrir a
página do servidor.

2. Na página Início Rápido, clique em **Gerenciar Servidor** para abrir uma nova conexão com o Portal de Gerenciamento do Banco de Dados SQL. 

3. Especifique **mestre** para o banco de dados se conectar, em seguida efetue logon com seu nome de usuário e senha. Esse é o logon de administrador que você especificou quando criou o servidor.

4. O Portal de Gerenciamento do Banco de Dados SQL abre em uma nova janela do navegador e você será conectado ao **mestre**.

5. Se você vir um erro na página semelhante ao seguinte, ignore-o. Clique em **Nova Consulta** para abrir uma janela de consulta que permite executar comandos Transact-SQL no banco de dados **mestre**.

	![Navigation pane][Image15]

6. Copie e cole o seguinte comando na janela de consulta.

        CREATE LOGIN SQLDBLogin WITH password='Password1';

7. Execute o comando para criar um novo logon do SQL Server chamado  'SQLDBLogin'.


<h4 id="CreateDBuser">Criar um usuário de banco de dados e atribuir permissões</h4>

Depois de criar um logon de autenticação do SQL Server, a próxima etapa é atribuir o banco de dados e os níveis de permissão associados ao logon. Você faz isso criando um **usuário de banco de dados** em cada banco de dados.

1. Volte para a página Portal de Gerenciamento do Banco de Dados SQL que se conecta ao banco de dados **Escola**. Se você fechou a janela do navegador, inicie uma nova conexão ao banco de dados **Escola** usando as etapas da lição anterior, "Adicionar dados e um esquema usando um script Transact-SQL". 

	Na página do Portal de Gerenciamento do Banco de Dados SQL, o nome do banco de dados **Escola** está visível no canto superior esquerdo.

	![Navigation pane][Image12]

2. Clique em **Nova Consulta** para abrir uma nova janela de consulta e copiar na instrução a seguir. 

	    CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3. Execute o script. Esse script cria um novo usuário de banco de dados baseado no logon.

   Em seguida, você poderá atribuir permissões usando a função db_datareader. Os usuários do banco de dados atribuídos a essa função podem ler todos os dados de todas as tabelas de usuário no banco de dados. 

4. Abra uma nova janela de consulta e digite e execute a instrução a seguir. Essa instrução executa um procedimento armazenado interno que atribui a função db_datareader ao novo usuário que você acabou de criar. 

        EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

Agora você tem um novo logon de autenticação do SQL Server que tem permissão somente leitura para o banco de dados **Escola**. Usando essas etapas, você pode criar outros logons de autenticação do SQL Server para permitir diferentes níveis de acesso aos seus dados.


## Etapa 9: Conectar-se por meio de um aplicativo

É possível adicionar ADO.NET para conectar-se ao Banco de Dados SQL do Microsoft Azure. Diferente de uma conexão local, é preciso considerar a aceleração ou outras falhas de serviço que poderiam encerar uma conexão ou bloquear temporariamente novas conexões. Essa condição é chamada de falha temporária. Para gerenciar falhas temporárias, você implanta uma estratégia de nova tentativa. Ao se conectar ao banco de dados do SQL Azure, o [Bloco de aplicativos de manipulação de falhas transitórias](http://go.microsoft.com/fwlink/?LinkId=519356), parte da Biblioteca corporativa (Enterprise Library) de 6 de abril de 2013, tem estratégias de detecção que identificam uma condição de falha transitória.

<h4>Exemplo de aplicativo do console C#</h4>


	static void Main(string[] args)
    {
        //NOTE: Use appropriate exception handling in a production application.

        //Replace
        //  builder["Server"]: {servername} = Your Azure SQL Database server name
        //  builder["User ID"]: {username}@{servername} = Your Azure SQL Database user name and server name
        //  builder["Password"]: {password} = Your Azure SQL Database password

        System.Data.SqlClient.SqlConnectionStringBuilder builder = new System.Data.SqlClient.SqlConnectionStringBuilder();
        builder["Server"] = "{servername}";
        builder["User ID"] = "{username}@{servername}";
        builder["Password"] = "{password}";

        builder["Database"] = "AdventureWorks2012";
        builder["Trusted_Connection"] = false;
        builder["Integrated Security"] = false;
        builder["Encrypt"] = true;

        //1. Define an Exponential Backoff retry strategy for Azure SQL Database throttling (ExponentialBackoff Class). An exponential back-off strategy will gracefully back off the load on the service.
        int retryCount = 4;
        int minBackoffDelayMilliseconds = 2000;
        int maxBackoffDelayMilliseconds = 8000;
        int deltaBackoffMilliseconds = 2000;

        ExponentialBackoff exponentialBackoffStrategy = 
          new ExponentialBackoff("exponentialBackoffStrategy",
              retryCount,
              TimeSpan.FromMilliseconds(minBackoffDelayMilliseconds), 
              TimeSpan.FromMilliseconds(maxBackoffDelayMilliseconds),
              TimeSpan.FromMilliseconds(deltaBackoffMilliseconds));

        //2. Set a default strategy to Exponential Backoff.
        RetryManager manager = new RetryManager(new List<RetryStrategy>
        {  
            exponentialBackoffStrategy 
        }, "exponentialBackoffStrategy");

        //3. Set a default Retry Manager. A RetryManager provides retry functionality, or if you are using declarative configuration, you can invoke the RetryPolicyFactory.CreateDefault
            RetryManager.SetDefault(manager);

        //4. Define a default SQL Connection retry policy and SQL Command retry policy. A policy provides a retry mechanism for unreliable actions and transient conditions.
        RetryPolicy retryConnectionPolicy = manager.GetDefaultSqlConnectionRetryPolicy();
        RetryPolicy retryCommandPolicy = manager.GetDefaultSqlCommandRetryPolicy();

        //5. Create a function that will retry the connection using a ReliableSqlConnection.
        retryConnectionPolicy.ExecuteAction(() =>
        {
            using (ReliableSqlConnection connection = new ReliableSqlConnection(builder.ConnectionString))
            {
                connection.Open();

                IDbCommand command = connection.CreateCommand();
                command.CommandText = "SELECT Name FROM Production.Product";

                //6. Create a function that will retry the command calling ExecuteCommand() from the ReliableSqlConnection
                retryCommandPolicy.ExecuteAction(() =>
                {
                    using (IDataReader reader = connection.ExecuteCommand<IDataReader>(command))
                    {
                        while (reader.Read())
                        {
                            string name = reader.GetString(0);

                            Console.WriteLine(name);
                        }
                    }
                });                  
            }
        });

        Console.ReadLine();
    }



## Próximas etapas

Agora que você está familiarizado com o Banco de Dados SQL e os Portais de Gerenciamento, você pode experimentar outras ferramentas e técnicas usadas pelos administradores de Banco de Dados do SQL Server.

Para gerenciar ativamente o seu novo banco de dados, considere a possibilidade de instalar e usar o SQL Server Management Studio. O Management Studio é a ferramenta de administração de banco de dados primário para o gerenciamento de bancos de dados do SQL Server, inclusive aqueles em execução no Azure. Usando o Management Studio, você pode salvar consultas para uso futuro, adicionar novas tabelas e procedimentos armazenados e aprimorar suas habilidades no Transact-SQL em um ambiente de script sofisticado que inclui um verificador de sintaxe, intellisense e modelos. Para começar, siga as instruções em [Gerenciando o Banco de Dados do SQL Server usando o SQL Server Management Studio (a página pode estar em inglês)](http://www.azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/).

Ter fluência na consulta Transact-SQL e na linguagem de definição de dados é essencial para os administradores de Banco de Dados. Se você for novo no Transact-SQL, comece com o [Tutorial: Escrevendo instruções Transact-SQL](http://msdn.microsoft.com/library/ms365303.aspx) para aprender algumas técnicas básicas.

Existem outros métodos para mover um banco de dados local para o Banco de Dados SQL. Se você tiver bancos de dados existentes, ou fez o download de exemplos de bancos de dados para praticar, tente as seguintes abordagens alternativas:

* [Migrando bancos de dados para o Banco de Dados SQL](http://msdn.microsoft.com/library/windowsazure/ee730904.aspx)
* [Copiando bancos de dados no Banco de Dados SQL](http://msdn.microsoft.com/library/windowsazure/ff951624.aspx)
* [Implantar um Banco de dados do SQL Server em uma máquina virtual do Azure](http://msdn.microsoft.com/library/dn195938)



[Image1]: ./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png
[Image2]: ./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png
[Image3]: ./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG
[Image4]: ./media/sql-database-get-started/4ServerSettings_SQLTut.PNG
[Image5]: ./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG
[Image6]: ./media/sql-database-get-started/6DBConfigFirewall_SQLTut.PNG
[Image7]: ./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png
[Image8]: ./media/sql-database-get-started/20MainPageHome_SQLTut.PNG
[Image9]: ./media/sql-database-get-started/9dblistschool_SQLTut.PNG
[Image10]: ./media/sql-database-get-started/10dbportalmanagebutton_SQLTut.PNG
[Image11]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG
[Image12]: ./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG
[Image13]: ./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG
[Image14]: ./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG
[Image15]: ./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG
[Image16]: ./media/sql-database-get-started/16ExcelConnect_SQLTut.png
[Image17]: ./media/sql-database-get-started/17ExcelSelect_SQLTut.PNG
[Image18]: ./media/sql-database-get-started/18ExcelTable_SQLTut.PNG
[Image19]: ./media/sql-database-get-started/19ExcelImport_SQLTut.png
[Image20]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG


<!--HONumber=47-->
