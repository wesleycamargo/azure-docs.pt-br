<properties linkid="manage-services-how-to-deploy-a-sqldb" urlDisplayName="How to deploy" pageTitle="How to deploy a SQL Database - Azure" metaKeywords="" description="Learn how to deploy a SQL Server database to Azure. You will use the Deploy Database to SQL Database wizard to upload a sample database." metaCanonical="" services="sql-database" documentationCenter="" title="How to Deploy a Database to Azure" authors="Lori Clark" solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Lori="" Clark" />

# <span id="howtodeploySQLdb"></span></a>Como implantar um banco de dados no Windows Azure

Há várias maneiras diferentes de mover um banco de dados local do SQL server para o Azure. Nesta tarefa, você usará o assistente para Implantar Banco de Dados no Banco de dados SQL para carregar um banco de dados de exemplo.

O banco de dados de exemplo School é convenientemente simples. Todos os seus objetos são compatíveis com o Banco de Dados SQL eliminando a necessidade de modificar ou preparar um banco de dados para migração. Como um novo administrador, primeiro tente implantar um banco de dados simples para aprender as etapas antes de usar seus próprios bancos de dados.

**Observação:** examine o Guia de Migração do Banco de Dados SQL para obter instruções detalhadas sobre como preparar um banco de dados local para a migração para o Azure. Além disso, considere a possibilidade de baixar o Kit de Treinamento do Azure. Ele inclui um laboratório que mostra uma abordagem alternativa para migrar um banco de dados local.

## Sumário

-   [Como: Criar o banco de dados school em um servidor local][Como: Criar o banco de dados school em um servidor local]
-   [Como: Implantar no Banco de Dados SQL][Como: Implantar no Banco de Dados SQL]
-   [Como: Verificar a implantação do banco de dados][Como: Verificar a implantação do banco de dados]

## <span id="schooldb"></span></a>Como: Criar o banco de dados school em um servidor local

Os scripts para criar esse banco de dados podem ser encontrados em [Introdução à administração de Banco de Dados SQL][Introdução à administração de Banco de Dados SQL]. Nesse guia, você executará esses scripts no Management Studio para criar uma versão local do banco de dados School.

1.  No Management Studio, conecte-se a um servidor local. Clique com o botão direito do mouse em **Bancos de Dados**, clique em **Novo Banco de Dados** e digite *school*.

2.  Clique com o botão direito do mouse em *school* e clique em **Nova Consulta**.

3.  Copie e, em seguida, execute o script Create Schema do tutorial.

<div style="width:auto; height:300px; overflow:auto"><pre>
    -- Criar a tabela Department.
    SE N&Atilde;O EXISTIR (SELECIONAR * DE sys.objects 
        ONDE object_id = OBJECT_ID(N'[dbo].[Department]') 
        E DIGITAR EM (N'U'))
    COME&Ccedil;AR
    CRIAR TABELA [dbo].[Department](
        [DepartmentID] [int] N&Atilde;O NULO,
        [Name] [nvarchar](50) N&Atilde;O NULO,
        [Budget] [money] N&Atilde;O NULO,
        [StartDate] [datetime] N&Atilde;O NULO,
        [Administrator] [int] NULO,
     RESTRI&Ccedil;&Atilde;O [PK_Department] CHAVE PRIM&Aacute;RIA CLUSTERIZADA 
    (
    [DepartmentID] ASC
    )COM (IGNORE_DUP_KEY = OFF)
    )
    FINALIZAR;
    IR

    -- Criar a tabela Person.
    SE N&Atilde;O EXISTIR (SELECIONAR * DE sys.objects 
        ONDE object_id = OBJECT_ID(N'[dbo].[Person]') 
        E DIGITAR EM (N'U'))
    COME&Ccedil;AR
    CRIAR TABELA [dbo].[Person](
        [PersonID] [int] IDENTIDADE (1,1) N&Atilde;O NULO,
        [LastName] [nvarchar](50) N&Atilde;O NULO,
        [FirstName] [nvarchar](50) N&Atilde;O NULO,
        [HireDate] [datetime] NULO,
        [EnrollmentDate] [datetime] NULO,
     RESTRI&Ccedil;&Atilde;O [PK_School.Student] CHAVE PRIM&Aacute;RIA CLUSTERIZADA   
    (
    [PersonID] ASC
    )COM (IGNORE_DUP_KEY = OFF)
    ) 
    FINALIZAR;
    IR

    -- Criar a tabela OnsiteCourse.
    SE N&Atilde;O EXISTIR (SELECIONAR * DE sys.objects 
        ONDE object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]') 
        E DIGITAR EM (N'U'))
    COME&Ccedil;AR
    CRIAR TABELA [dbo].[OnsiteCourse](
        [CourseID] [int] N&Atilde;O NULO,
        [Location] [nvarchar](50) N&Atilde;O NULO,
        [Days] [nvarchar](50) N&Atilde;O NULO,
        [Time] [smalldatetime] N&Atilde;O NULO,
     RESTRI&Ccedil;&Atilde;O [PK_OnsiteCourse] CHAVE PRIM&Aacute;RIA CLUSTERIZADA 
    (
        [CourseID] ASC
    )COM (IGNORE_DUP_KEY = OFF)
    ) 
    FINALIZAR;
    IR

    -- Criar a tabela OnlineCourse.
    SE N&Atilde;O EXISTIR (SELECIONAR * DE sys.objects 
        ONDE object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]') 
        E DIGITAR EM (N'U'))
    COME&Ccedil;AR
    CRIAR TABELA [dbo].[OnlineCourse](
        [CourseID] [int] N&Atilde;O NULO,
        [URL] [nvarchar](100) N&Atilde;O NULO,
     RESTRI&Ccedil;&Atilde;O [PK_OnlineCourse] CHAVE PRIM&Aacute;RIA CLUSTERIZADA 
    (
        [CourseID] ASC
    )COM (IGNORE_DUP_KEY = OFF)
    ) 
    FINALIZAR;
    IR

    --Criar tabela StudentGrade.
    SE N&Atilde;O EXISTIR (SELECIONAR * DE sys.objects 
        ONDE object_id = OBJECT_ID(N'[dbo].[StudentGrade]') 
        E DIGITAR EM (N'U'))
    COME&Ccedil;AR
    CRIAR TABELA [dbo].[StudentGrade](
        [EnrollmentID] [int] IDENTIDADE(1,1) N&Atilde;O NULA,
        [CourseID] [int] N&Atilde;O NULO,
        [StudentID] [int] N&Atilde;O NULO,
        [Grade] [decimal](3, 2) NULO,
     RESTRI&Ccedil;&Atilde;O [PK_StudentGrade] CHAVE PRIM&Aacute;RIA CLUSTERIZADA 
    (
        [EnrollmentID] ASC
    )COM (IGNORE_DUP_KEY = OFF)
    ) 
    FINALIZAR;
    IR

    -- Criar a tabela CourseInstructor.
    SE N&Atilde;O EXISTIR (SELECIONAR * DE sys.objects 
        ONDE object_id = OBJECT_ID(N'[dbo].[CourseInstructor]') 
        E DIGITAR EM (N'U'))
    COME&Ccedil;AR
    CRIAR TABELA [dbo].[CourseInstructor](
        [CourseID] [int] N&Atilde;O NULO,
        [PersonID] [int] N&Atilde;O NULO,
     RESTRI&Ccedil;&Atilde;O [PK_CourseInstructor] CHAVE PRIM&Aacute;RIA CLUSTERIZADA 
    (
        [CourseID] ASC,
        [PersonID] ASC
    )COM (IGNORE_DUP_KEY = OFF)
    ) 
    FINALIZAR;
    IR

    -- Criar a tabela Course.
    SE N&Atilde;O EXISTIR (SELECIONAR * DE sys.objects 
        ONDE object_id = OBJECT_ID(N'[dbo].[Course]') 
        E DIGITAR EM (N'U'))
    COME&Ccedil;AR
    CRIAR TABELA [dbo].[Course](
        [CourseID] [int] N&Atilde;O NULO,
        [Title] [nvarchar](100) N&Atilde;O NULO,
        [Credits] [int] N&Atilde;O NULO,
        [DepartmentID] [int] N&Atilde;O NULO,
     RESTRI&Ccedil;&Atilde;O [PK_School.Course] CHAVE PRIM&Aacute;RIA CLUSTERIZADA 
    (
        [CourseID] ASC
    )COM (IGNORE_DUP_KEY = OFF)
    )
    FINALIZAR;
    IR

    -- Criar a tabela OfficeAssignment.
    SE N&Atilde;O EXISTIR (SELECIONAR * DE sys.objects 
        ONDE object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]')
        E DIGITAR EM (N'U'))
    COME&Ccedil;AR
    CRIAR TABELA [dbo].[OfficeAssignment](
        [InstructorID] [int] N&Atilde;O NULO,
        [Location] [nvarchar](50) N&Atilde;O NULO,
        [Timestamp] [timestamp] N&Atilde;O NULO,
     RESTRI&Ccedil;&Atilde;O [PK_OfficeAssignment] CHAVE PRIM&Aacute;RIA CLUSTERIZADA 
    (
        [InstructorID] ASC
    )COM (IGNORE_DUP_KEY = OFF)
    )
    FINALIZAR;
    IR

    -- Definir a rela&ccedil;&atilde;o entre OnsiteCourse e Course.
    SE N&Atilde;O EXISTIR (SELECIONE * DE sys.foreign_keys 
       ONDE object_id = OBJECT_ID(N'[dbo].[FK_OnsiteCourse_Course]')
       E parent_object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]'))
    ALTERAR TABELA [dbo].[OnsiteCourse]  COM VERIFICAR E ADICIONAR  
       RESTRI&Ccedil;&Atilde;O [FK_OnsiteCourse_Course] CHAVE ESTRANGEIRA([CourseID])
    REFERENCIAS [dbo].[Course] ([CourseID]);
    IR
    ALTERAR TABELA [dbo].[OnsiteCourse] VERIFICAR 
       RESTRI&Ccedil;&Atilde;O [FK_OnsiteCourse_Course];
    IR

    -- Definir a rela&ccedil;&atilde;o entre OnlineCourse e Course.
    SE N&Atilde;O EXISTIR (SELECIONE * DE sys.foreign_keys 
       ONDE object_id = OBJECT_ID(N'[dbo].[FK_OnlineCourse_Course]')
       E parent_object_id = OBJECT_ID(N'[dbo].[OnlineCourse]'))
    ALTERAR TABELA [dbo].[OnlineCourse]  COM VERIFICAR E ADICIONAR  
       RESTRI&Ccedil;&Atilde;O [FK_OnlineCourse_Course] CHAVE ESTRANGEIRA([CourseID])
    REFERENCIAS [dbo].[Course] ([CourseID]);
    IR
    ALTERAR TABELA [dbo].[OnlineCourse] VERIFICAR 
       RESTRI&Ccedil;&Atilde;O [FK_OnlineCourse_Course];
    IR
    -- Definir a rela&ccedil;&atilde;o entre StudentGrade e Course.
    SE N&Atilde;O EXISTIR (SELECIONE * DE sys.foreign_keys 
       ONDE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Course]')
       E parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))
    ALTERAR TABELA [dbo].[StudentGrade]  COM VERIFICAR E ADICIONAR  
       RESTRI&Ccedil;&Atilde;O [FK_StudentGrade_Course] CHAVE ESTRANGEIRA([CourseID])
    REFER&Ecirc;NCIAS [dbo].[Course] ([CourseID]);
    IR
    ALTERAR TABELA [dbo].[StudentGrade] VERIFICAR 
       RESTRI&Ccedil;&Atilde;O [FK_StudentGrade_Course];
    IR

    -- Definir a rela&ccedil;&atilde;o entre StudentGrade e Student.
    SE N&Atilde;O EXISTIR (SELECIONE * DE sys.foreign_keys 
       ONDE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Student]')
       E parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))   
    ALTERAR TABELA [dbo].[StudentGrade]  COM VERIFICAR E ADICIONAR  
       RESTRI&Ccedil;&Atilde;O [FK_StudentGrade_Student] CHAVE ESTRANGEIRA([StudentID])
    REFER&Ecirc;NCIAS [dbo].[Person] ([PersonID]);
    IR
    ALTERAR TABELA [dbo].[StudentGrade] VERIFICAR 
       RESTRI&Ccedil;&Atilde;O [FK_StudentGrade_Student];
    IR

    -- Definir a rela&ccedil;&atilde;o entre CourseInstructor e Course.
    SE N&Atilde;O EXISTIR (SELECIONE * DE sys.foreign_keys 
     ONDE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Course]')
     E parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
    ALTERAR TABELA [dbo].[CourseInstructor]  COM VERIFICAR E ADICIONAR  
     RESTRI&Ccedil;&Atilde;O [FK_CourseInstructor_Course] CHAVE ESTRANGEIRA([CourseID])
    REFERENCIAS [dbo].[Course] ([CourseID]);
    IR
    ALTERAR TABELA [dbo].[CourseInstructor] VERIFICAR 
      RESTRI&Ccedil;&Atilde;O [FK_CourseInstructor_Course];
    IR

    -- Definir a rela&ccedil;&atilde;o entre CourseInstructor e Person.
    SE N&Atilde;O EXISTIR (SELECIONE * DE sys.foreign_keys 
      ONDE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Person]')
       E parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
    ALTERAR TABELA [dbo].[CourseInstructor]  COM VERIFICAR E ADICIONAR  
      RESTRI&Ccedil;&Atilde;O [FK_CourseInstructor_Person] CHAVE ESTRANGEIRA([PersonId])
    REFER&Ecirc;NCIAS [dbo].[Person] ([PersonID]);
    IR
    ALTERAR TABELA [dbo].[CourseInstructor] VERIFICAR 
     RESTRI&Ccedil;&Atilde;O [FK_CourseInstructor_Person];
    IR

    -- Definir a rela&ccedil;&atilde;o entre Course e Department.
    SE N&Atilde;O EXISTIR (SELECIONE * DE sys.foreign_keys 
       ONDE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       E parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
    ALTERAR TABELA [dbo].[Course]  COM VERIFICAR E ADICIONAR  
       RESTRI&Ccedil;&Atilde;O [FK_OnsiteCourse_Course] CHAVE ESTRANGEIRA([DepartmentID])
    REFER&Ecirc;NCIAS [dbo].[Department] ([DepartmentID]);
    IR
    ALTERAR TABELA [dbo].[Course] VERIFICAR RESTRI&Ccedil;&Atilde;O [FK_Course_Department];
    IR

    -- Definir a rela&ccedil;&atilde;o entre OfficeAssignment e Person.
    SE N&Atilde;O EXISTIR (SELECIONE * DE sys.foreign_keys 
      ONDE object_id = OBJECT_ID(N'[dbo].[FK_OfficeAssignment_Person]')
      E parent_object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]'))
    ALTERAR TABELA [dbo].[OfficeAssignment]  COM VERIFICAR E ADICIONAR  
      RESTRI&Ccedil;&Atilde;O [FK_OfficeAssignment_Person] CHAVE ESTRANGEIRA([InstructorID])
    REFER&Ecirc;NCIAS [dbo].[Person] ([PersonID]);
    IR
    ALTERAR TABELA [dbo].[OfficeAssignment] VERIFICAR 
     RESTRI&Ccedil;&Atilde;O [FK_OfficeAssignment_Person];
    IR
</pre></div>

Em seguida, copie e execute o script Insert Data.

<div style="width:auto; height:300px; overflow:auto"><pre>
    -- Insertar os dados na tabela Person.
    ATIVAR O IDENTITY_INSERT dbo.Person;
    IR
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (1, 'Abercrombie', 'Kim', '1995-03-11', nulo);
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (2, 'Barzdukas', 'Gytis', nulo, '2005-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (3, 'Justice', 'Peggy', nulo, '2001-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (4, 'Fakhouri', 'Fadi', '2002-08-06', nulo);
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (5, 'Harui', 'Roger', '1998-07-01', nulo);
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (6, 'Li', 'Yan', nulo, '2002-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (7, 'Norman', 'Laura', nulo, '2003-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (8, 'Olivotto', 'Nino', nulo, '2005-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (9, 'Tang', 'Wayne', nulO, '2005-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (10, 'Alonso', 'Meredith', nulo, '2002-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (11, 'Lopez', 'Sophia', nulo, '2004-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (12, 'Browning', 'Meredith', nulo, '2000-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (13, 'Anand', 'Arturo', nulo, '2003-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (14, 'Walker', 'Alexandra', nulo, '2000-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (15, 'Powell', 'Carson', nulo, '2004-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (16, 'Jai', 'Damien', nulo, '2001-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (17, 'Carlson', 'Robyn', nulo, '2005-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (18, 'Zheng', 'Roger', '2004-02-12', nulo);
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (19, 'Bryant', 'Carson', nulo, '2001-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (20, 'OfficeAssignment', 'Robyn', nulo, '01.09.04');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (21, 'Holt', 'Roger', nulo, '2004-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (22, 'Alexander', 'Carson', nulo, '01.09.05');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (23, 'Morgan', 'Isaiah', nulo, '2001-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (24, 'Martin', 'Randall', nulo, '2005-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (25, 'Kapoor', 'Candace', '2001-01-15', nulo);
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (26, 'Rogers', 'Cody', nulo, '2002-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (27, 'Serrano', 'Stacy', '1999-06-01', nulo);
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (28, 'White', 'Anthony', nulo, '2001-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (29, 'Griffin', 'Rachel', nulo, '2004-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (30, 'Shan', 'Alicia', nulo, '2003-09-01');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (31, 'Stewart', 'Jasmine', '12.10.97', nulO);
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (32, 'Xu', 'Kristen', '23.07.01', nulO);
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (33, 'Gao', 'Erica', nulo, '30.01.03');
    INSERTAR EM dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
    VALORES (34, 'Van Houten', 'Roger', '2000-12-07', nulo);
    IR
    DESATIVAR O IDENTITY_INSERT dbo.Person;
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

## <span id="deploydb"></span></a>Como: Implantar no Banco de Dados SQL

1.  No Management Studio, conecte-se a uma instância do SQL Server local que tenha um banco de dados que você deseja migrar.

2.  Clique com o botão direito do mouse no banco de dados School que você acabou de criar, aponte para **Tarefas**e clique em **Implantar o Banco de Dados no SQL Azure**.

3.  Em Configurações da Implantação, digite um nome para o banco de dados, por exemplo, *school*.

4.  Clique em **Conectar**.

5.  Em Nome do Servidor, digite um nome de servidor de 10 caracteres, seguido por .database.windows.net.

6.  Em Autenticação, escolha **Autenticação do SQL Server**.

7.  Digite o nome de logon e a senha do administrador que você provisionou ao criar o servidor lógico do Banco de Dados SQL.

8.  Clique em **Opções**.

9.  Em Propriedades da Conexão, em Conectar-se ao banco de dados, digite **mestre**.

10. Clique em **Conectar**. Essa etapa conclui a especificação da conexão e leva você de volta ao assistente.

11. Clique em **Avançar** e clique em **Concluir** para executar o assistente.

## <span id="verify"></span></a>Como: Verificar a implantação do banco de dados

1.  No Management Studio, no Pesquisador de Objetos, atualize os bancos de dados para exibir o novo que você acabou de criar.

2.  Expanda a pasta Databases. Você deve ver o banco de dados **school** na lista.

3.  Clique com o botão direito do mouse no banco de dados school e clique em **Nova Consulta**.

4.  Execute a consulta a seguir para verificar se os dados estão acessíveis.

<div style="width:auto; height:auto; overflow:auto"><pre>
    SELECIONAR
        Course.Title como &quot;T&iacute;tulo do curso&quot;
        ,Department.Name como &quot;Departmento&quot;
        ,Person.LastName como &quot;Instrutor&quot;
        ,OnsiteCourse.Location como &quot;Local&quot;
        ,OnsiteCourse.Days como &quot;Dias&quot;
        ,OnsiteCourse.Time como &quot;Hora&quot;
    DE
     Curso
     JUN&Ccedil;&Atilde;O INTERNA Department
      EM Course.DepartmentID = Department.DepartmentID
     JUN&Ccedil;&Atilde;O INTERNA CourseInstructor
       EM Course.CourseID = CourseInstructor.CourseID
     JUN&Ccedil;&Atilde;O INTERNA Person
       EM CourseInstructor.PersonID = Person.PersonID
     JUN&Ccedil;&Atilde;O INTERNA OnsiteCourse
        EM OnsiteCourse.CourseID = CourseInstructor.CourseID;
</pre></div>

  [Como: Criar o banco de dados school em um servidor local]: #schooldb
  [Como: Implantar no Banco de Dados SQL]: #deploydb
  [Como: Verificar a implantação do banco de dados]: #verify
  [Introdução à administração de Banco de Dados SQL]: /pt-br/manage/services/sql-databases/getting-started-w-sql-databases/
