<properties 
	pageTitle="Como implantar um Banco de Dados SQL no SQL Azure" 
	description="Implante um banco de dados do SQL Server no Banco de Dados SQL do Azure usando o Assistente no SQL Server 2016 Management Studio." 
	services="sql-database" 
	documentationCenter="" 
	authors="CarlRabeler" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="carlrab"/>


# Como implantar um banco de dados do SQL Server no Banco de Dados SQL do Azure

Neste artigo, você usará o **assistente para Implantação de Banco de Dados no Banco de Dados SQL do Azure** para carregar um exemplo de banco de dados no Banco de Dados SQL do Azure. Você deve baixar o **SQL Server 2016 Management Studio (CTP 2.1)** para este tutorial.

Tempo estimado para conclusão: 15 minutos (inclui o tempo de download)

> [AZURE.NOTE]Este tutorial usa um banco de dados de exemplo “school”, que é muito simples; todos os seus objetos são compatíveis com o Banco de Dados SQL do Azure, o que elimina a necessidade de modificar ou preparar o banco de dados para migração. Se estiver migrando um banco de dados existente mais complexo, você também poderá considerar usar o [SQL Database Migration Wizard](http://sqlazuremw.codeplex.com/) e consultar esta [visão geral](sql-database-cloud-migrate.md).

## Pré-requisitos

Uma **conta do Microsoft Azure**. Para uma avaliação gratuita, consulte esta [oferta](http://azure.microsoft.com/pricing/free-trial/).

Baixe o [**SQL Server Management Studio**](https://msdn.microsoft.com/library/mt238290.aspx). (Para saber mais sobre a ferramenta, confira [SQL Server Management Studio - notas de versão de junho de 2015](https://msdn.microsoft.com/library/mt238486.aspx).)

Um **servidor de Banco de Dados SQL do Azure**. Para criar um servidor, você deve criar pelo menos um banco de dados primeiro. Quando cria um banco de dados, você tem a opção de criá-lo em um servidor novo ou existente. Para obter instruções sobre como criar um novo banco de dados (em um novo servidor), confira [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).

## Criar o banco de dados school em um servidor local

Execute estes scripts no SQL Server Management Studio (SSMS) para criar uma versão local do banco de dados “school”.

1. No SSMS, conecte-se a um servidor local. Clique com o botão direito do mouse em **Bancos de Dados**, clique em **Novo Banco de Dados** e digite *school*.

2. Clique com o botão direito do mouse em *school* e clique em **Nova Consulta**.

3. Copie e execute este script:

<div style="width:auto; height:300px; overflow:auto"><pre>
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

Em seguida, copie e execute o script Insert Data.

<div style="width:auto; height:300px; overflow:auto"><pre>
	-- Insertar os dados na tabela Person.
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
	-- Insertar os dados na tabela Department.
	INSERTAR EM dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALORES (1, 'Engineering', 350000.00, '2007-09-01', 2);
	INSERTAR EM dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALORES (2, 'English', 120000.00, '2007-09-01', 6);
	INSERTAR EM dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALORES (4, 'Economics', 200000,00, '2007-09-01', 4);
	INSERTAR EM dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALORES (7, 'Mathematics', 250000,00, '2007-09-01', 3);
	IR
	-- Insertar os dados na tabela Course.
	INSERTAR EM dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALORES (1050, 'Chemistry', 4, 1);
	INSERTAR EM dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALORES (1061, 'Physics', 4, 1);
	INSERTAR EM dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALORES (1045, 'Calculus', 4, 7);
	INSERTAR EM dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALORES (2030, 'Poetry', 2, 2);
	INSERTAR EM dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALORES (2021, 'Composition', 3, 2);
	INSERTAR EM dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALORES (2042, 'Literature', 4, 2);
	INSERTAR EM dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALORES (4022, 'Microeconomics', 3, 4);
	INSERTAR EM dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALORES (4041, 'Macroeconomics', 3, 4);
	INSERTAR EM dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALORES (4061, 'Quantitative', 2, 4);
	INSERTAR EM dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALORES (3141, 'Trigonometry', 4, 7);
	IR
	-- Insertar os dados na tabela OnlineCourse.
	INSERTAR EM dbo.OnlineCourse (CourseID, URL)
	VALORES (2030, 'http://www.fineartschool.net/Poetry');
	INSERTAR EM dbo.OnlineCourse (CourseID, URL)
	VALORES (2021, 'http://www.fineartschool.net/Composition');
	INSERTAR EM dbo.OnlineCourse (CourseID, URL)
	VALORES (4041, 'http://www.fineartschool.net/Macroeconomics');
	INSERTAR EM dbo.OnlineCourse (CourseID, URL)
	VALORES (3141, 'http://www.fineartschool.net/Trigonometry');
	-- Insertar os dados na tabela OnsiteCourse.
	INSERTAR EM dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALORES (1050, '123 Smith', 'MTWH', '11:30');
	INSERTAR EM dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALORES (1061, '234 Smith', 'TWHF', '13:15');
	INSERTAR EM dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALORES (1045, '121 Smith', 'MWHF', '15:30');
	INSERTAR EM dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALORES (4061, '22 Williams', 'TH', '11:15');
	INSERTAR EM dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALORES (2042, '225 Adams', 'MTWH', '11:00');
	INSERTAR EM dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALORES (4022, '23 Williams', 'MWF', '09:00');
	-- Insertar os dados na tabela CourseInstructor.
	INSERTAR EM dbo.CourseInstructor(CourseID, PersonID)
	VALORES (1050, 1);
	INSERTAR EM dbo.CourseInstructor(CourseID, PersonID)
	VALORES (1061, 31);
	INSERTAR EM dbo.CourseInstructor(CourseID, PersonID)
	VALORES (1045, 5);
	INSERTAR EM dbo.CourseInstructor(CourseID, PersonID)
	VALORES (2030, 4);
	INSERTAR EM dbo.CourseInstructor(CourseID, PersonID)
	VALORES (2021, 27);
	INSERTAR EM dbo.CourseInstructor(CourseID, PersonID)
	VALORES (2042, 25);
	INSERTAR EM dbo.CourseInstructor(CourseID, PersonID)
	VALORES (4022, 18);
	INSERTAR EM dbo.CourseInstructor(CourseID, PersonID)
	VALORES (4041, 32);
	INSERTAR EM dbo.CourseInstructor(CourseID, PersonID)
	VALORES (4061, 34);
	IR
	-- Insertar os dados na tabela OfficeAssignment.
	INSERTAR EM dbo.OfficeAssignment(InstructorID, Location)
	VALORES (1, '17 Smith');
	INSERTAR EM dbo.OfficeAssignment(InstructorID, Location)
	VALORES (4, '29 Adams');
	INSERTAR EM dbo.OfficeAssignment(InstructorID, Location)
	VALORES (5, '37 Williams');
	INSERTAR EM dbo.OfficeAssignment(InstructorID, Location)
	VALORES (18, '143 Smith');
	INSERTAR EM dbo.OfficeAssignment(InstructorID, Location)
	VALORES (25, '57 Adams');
	INSERTAR EM dbo.OfficeAssignment(InstructorID, Location)
	VALORES (27, '271 Williams');
	INSERTAR EM dbo.OfficeAssignment(InstructorID, Location)
	VALORES (31, '131 Smith');
	INSERTAR EM dbo.OfficeAssignment(InstructorID, Location)
	VALORES (32, '203 Williams');
	INSERTAR EM dbo.OfficeAssignment(InstructorID, Location)
	VALORES (34, '213 Smith');
	-- Insertar os dados na tabela StudentGrade.
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (2021, 2, 4);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (2030, 2, 3,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (2021, 3, 3);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (2030, 3, 4);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (2021, 6, 2,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (2042, 6, 3,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (2021, 7, 3,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (2042, 7, 4);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (2021, 8, 3);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (2042, 8, 3);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4041, 9, 3,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4041, 10, nulo);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4041, 11, 2,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4041, 12, nulo);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4061, 12, nulo);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4022, 14, 3);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4022, 13, 4);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4061, 13, 4);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4041, 14, 3);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4022, 15, 2,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4022, 16, 2);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4022, 17, nulo);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4022, 19, 3,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4061, 20, 4);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4061, 21, 2);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4022, 22, 3);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4041, 22, 3,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4061, 22, 2,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (4022, 23, 3);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (1045, 23, 1,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (1061, 24, 4);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (1061, 25, 3);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (1050, 26, 3,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (1061, 26, 3);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (1061, 27, 3);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (1045, 28, 2,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (1050, 28, 3,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (1061, 29, 4);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (1050, 30, 3,5);
	INSERTAR EM dbo.StudentGrade (CourseID, StudentID, Grade)
	VALORES (1061, 30, 4);
	IR
</pre></div>
Agora você tem um banco de dados local que pode ser exportado para o Azure. Em seguida, você executará um assistente que criará um arquivo .bacpac, carregará o assistente no Azure e o importará para o Banco de Dados SQL.

	
## Implantar o banco de dados no SQL do Azure 
	
1. No Management Studio, clique com o botão direito do mouse no banco de dados school que você acabou de criar, aponte para **Tarefas** e clique em **Implantar Banco de Dados no Banco de Dados SQL do Microsoft Azure**.
2. Em **Configurações da Implantação**, digite um nome para o banco de dados, por exemplo, *school*.
5. Clique em **Conectar**. Para solucionar problemas com conectividade, tente esta [solução de problemas](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1).
6. Em **Nome do servidor**, digite um nome de servidor de 10 caracteres, seguido por **.database.windows.net**
7. Em **Autenticação**, escolha **Autenticação do SQL Server**.
8. Digite o nome e a senha de logon do administrador que você criou no provisionamento do servidor lógico do Banco de Dados SQL.
9. Clique em **Opções**.
10. Em Propriedades da Conexão, em **Conectar-se ao banco de dados**, digite **mestre**.

	**Observação**: você deve se conectar ao banco de dados **mestre** sempre que quiser criar um banco de dados no servidor do Banco de Dados SQL do Azure. 
11. Clique em **Conectar**. Essa etapa conclui a especificação da conexão e leva você de volta ao assistente.
12. Clique em **Avançar** e clique em **Concluir** para executar o assistente.

	
## Como verificar a implantação do banco de dados
	
1. No Management Studio, em **Pesquisador de Objetos**, clique no ícone **Conectar**.
2. Na caixa de nome **Servidor**, digite o nome do SQL Server do Azure, seguido por **database.windows.net**
3. Em **Autenticação**, selecione **Autenticação do SQL Server**.
4. Digite o nome e a senha de logon do administrador que você criou no provisionamento do servidor. 
5. Clique no botão **Opções**.
6. Clique na lista suspensa **Conectar ao banco de dados** e clique em **Procurar servidor**. Na caixa de diálogo seguinte, clique em **Sim** para permitir a navegação do servidor.
7. Clique no banco de dados **school** para selecioná-lo e clique em **OK**. 
8. E clique em **Conectar**. Para solucionar problemas com conectividade, tente esta [solução de problemas](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1).
2. Expanda a pasta **Bancos de dados**. Você deve ver o banco de dados **school** na lista.

	**Observação**: você deve se conectar ao banco de dados que deseja consultar. 
3. Clique com o botão direito do mouse em **school** e clique em **Nova Consulta**.
4. Execute a consulta a seguir para verificar se os dados estão acessíveis.

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
		
## Próximas etapas

Para ver um tutorial sobre como criar um novo banco de dados SQL do Azure, confira [Introdução à administração do Banco de Dados SQL](sql-database-get-started.md). Para ver as noções básicas de como conectar um banco de dados SQL do Azure de um aplicativo C#, confira [Conectar e consultar seu Banco de Dados SQL com C#](sql-database-connect-query.md). Para obter mais tutoriais sobre conexão de várias plataformas (como PHP), confira [Desenvolvimento do Banco de Dados SQL do Azure: Tópicos de instruções](https://msdn.microsoft.com/library/azure/ee621787.aspx).

 

<!---HONumber=Oct15_HO3-->