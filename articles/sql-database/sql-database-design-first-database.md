---
title: Criar seu primeiro banco de dados SQL do Azure | Microsoft Docs
description: Aprenda a criar seu primeiro banco de dados SQL do Azure.
services: sql-database
documentationcenter: 
author: janeng
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 03/30/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 0d02954829ebac9275c014f7dac7e1ec423b0fc1
ms.lasthandoff: 04/21/2017


---

# <a name="design-your-first-azure-sql-database"></a>Criar seu primeiro banco de dados SQL do Azure

Neste tutorial, você criará um banco de dados para uma universidade acompanhar as notas de alunos e os registros em cursos. Este tutorial mostra como usar o [Portal do Azure](https://portal.azure.com/) e o [SSMS](https://msdn.microsoft.com/library/ms174173.aspx) (SQL Server Management Studio) para criar um banco de dados SQL do Azure em um servidor lógico do Banco de Dados SQL, adicionar tabelas ao banco de dados, carregar dados nas tabelas e consultar o banco de dados. Ele também demonstra como usar os recursos de [Recuperação Pontual](sql-database-recovery-using-backups.md#point-in-time-restore) do Banco de Dados SQL para restaurar o banco de dados em um ponto anterior no tempo.

Para concluir este tutorial, certifique-se de ter instalado a versão mais recente do SSMS [(SQL Server Management Studio)](https://msdn.microsoft.com/library/ms174173.aspx). 

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-blank-sql-database-in-azure"></a>Criar um banco de dados SQL em branco no Azure

Um banco de dados SQL do Azure é criado com um conjunto definido de [recursos de computação e armazenamento](sql-database-service-tiers.md). O banco de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e em um [servidor lógico de banco de dados SQL do Azure](sql-database-features.md). 

Siga estas etapas para criar um banco de dados SQL em branco. 

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

2. Selecione **Bancos de Dados** na página **Novo** e **Banco de Dados SQL** na página **Bancos de Dados**. 

    ![criar banco de dados vazio](./media/sql-database-design-first-database/create-empty-database.png)

3. Preencha o formulário do Banco de Dados SQL com as informações abaixo, conforme mostrado na imagem anterior:     

   - Nome do banco de dados: **mySampleDatabase**
   - Grupo de recursos: **myResourceGroup**
   - Origem: **banco de dados em branco**

4. Clique em **Servidor** para criar e configurar um novo servidor para o novo banco de dados. Preencha o **formulário Novo servidor** especificando um nome de servidor exclusivo, forneça um nome para o logon de administrador do servidor e especifique a senha escolhida. 

    ![criar database-server](./media//sql-database-design-first-database/create-database-server.png)
5. Clique em **Selecionar**.

6. Clique em **Tipo de preço** para especificar o nível de desempenho e o tipo de serviço para o novo banco de dados. Para este tutorial, selecione **20 DTUs** e **250** GB de armazenamento.

    ![Criar database-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. Clique em **Aplicar**.  

8. Clique em **Criar** para provisionar o banco de dados. O provisionamento leva cerca de um minuto e meio para concluir. 

9. Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.

    ![notificação](./media/sql-database-get-started-portal/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>Criar uma regra de firewall no nível de servidor

Os Banco de Dados SQL do Azure são protegidos por um firewall. Por padrão, todas as conexões com o servidor e com os bancos de dados dentro do servidor são rejeitadas. Siga estas etapas para criar uma [regra de firewall de nível de servidor do Banco de Dados SQL](sql-database-firewall-configure.md) para que o servidor permita conexões de seu endereço IP de cliente. 

1. Após a conclusão da implantação, clique em **Bancos de Dados SQL** no menu à esquerda e clique no novo banco de dados, **mySampleDatabase**na página **Bancos de Dados SQL**. A página de visão geral de seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver-20170313.database.windows.net**) e fornece opções para configurações adicionais.

      ![regra de firewall do servidor](./media/sql-database-design-first-database/server-firewall-rule.png) 

2. Clique em **Definir o firewall do servidor** na barra de ferramentas, conforme mostrado na imagem anterior. A página **Configurações do firewall** do servidor de Banco de Dados SQL é aberta. 

3. Clique em **Adicionar IP do cliente** na barra de ferramentas e clique em **Salvar**. Uma regra de firewall no nível do servidor é criada para seu endereço IP atual.

      ![definir regra de firewall do servidor](./media/sql-database-design-first-database/server-firewall-rule-set.png) 

4. Clique em **OK** e clique no **X** para fechar a página **Configurações do firewall**.

Agora você pode se conectar ao banco de dados e ao seu servidor usando o SQL Server Management Studio ou outra ferramenta de sua escolha.

> [!NOTE]
> O Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isto acontecer, você não conseguirá conectar seu servidor do Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

## <a name="get-connection-information"></a>Obter informações de conexão

Obtenha o nome de servidor totalmente qualificado para o servidor de Banco de Dados SQL do Azure no Portal do Azure. Use o nome do servidor totalmente qualificado para se conectar ao servidor usando o SQL Server Management Studio.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de Dados SQL**. 
3. No painel **Essentials**, na página do Portal do Azure de seu banco de dados, localize e copie o **Nome do servidor**.

    ![informações da conexão](./media/sql-database-connect-query-ssms/connection-information.png) 

## <a name="connect-to-your-database-using-sql-server-management-studio"></a>Conectar ao banco de dados usando o SQL Server Management Studio

Use o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) para estabelecer uma conexão com seu servidor do Banco de Dados SQL do Azure.

1. Abra o SQL Server Management Studio.

2. Na caixa de diálogo **Conectar ao Servidor**, insira as informações a seguir:
   - **Tipo de servidor**: especifique mecanismo de banco de dados
   - **Nome do servidor**: insira seu nome do servidor totalmente qualificado, como **mynewserver20170313.database.windows.net**
   - **Autenticação**: especifique a Autenticação do SQL Server
   - **Logon**: insira a conta do administrador do servidor
   - **Senha**: insira a senha para sua conta do administrador do servidor


   <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. Clique em **Opções** na caixa de diálogo **Conectar ao servidor**. Na seção **Conectar ao banco de dados**, digite **mySampleDatabase** para conectar-se a este banco de dados.

   ![conectar o banco de dados no servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Clique em **Conectar**. A janela Pesquisador de Objetos abre no SSMS. 

5. No Pesquisador de Objetos, expanda **Bancos de Dados** e expanda **mySampleDatabase** para exibir os objetos no banco de dados de exemplo.

   ![objetos de banco de dados](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database"></a>Criar tabelas no banco de dados 

Criar um esquema de banco de dados com quatro tabelas que modelam um sistema de gerenciamento de aluno para universidades, usando o [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference):

- Pessoa
- Curso
- Aluno
- Crédito que modela um sistema de gerenciamento de aluno para universidades

O diagrama a seguir mostra como essas tabelas estão relacionadas. Algumas dessas tabelas fazem referência a colunas em outras tabelas. Por exemplo, a tabela Student faz referência à coluna **PersonId** da tabela **Person**. Estude o diagrama para entender como as tabelas neste tutorial estão relacionadas umas com as outras. Para obter uma visão detalhada de como criar tabelas de banco de dados eficientes, consulte [Criar tabelas de banco de dados eficientes](https://msdn.microsoft.com/library/cc505842.aspx). Para obter informações sobre como escolher tipos de dados, consulte [Tipos de dados](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Você também pode usar o [designer de tabela no SQL Server Management Studio](https://msdn.microsoft.com/library/hh272695.aspx) para criar e projetar suas tabelas. 

![Relações de tabela](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. No Pesquisador de Objetos, clique com o botão direito em **mySampleDatabase** e clique em **Nova Consulta**. Uma janela de consulta em branco conectada ao seu banco de dados é aberta.

2. Na janela de consulta, execute a consulta a seguir para criar quatro tabelas em seu banco de dados: 

   ```sql 
   -- Create Person table

    CREATE TABLE Person
    (
      PersonId      INT IDENTITY PRIMARY KEY,
      FirstName     NVARCHAR(128) NOT NULL,
      MiddelInitial NVARCHAR(10),
      LastName      NVARCHAR(128) NOT NULL,
      DateOfBirth   DATE NOT NULL
    )
   
   -- Create Student table
 
    CREATE TABLE Student
    (
      StudentId INT IDENTITY PRIMARY KEY,
      PersonId  INT REFERENCES Person (PersonId),
      Email     NVARCHAR(256)
    )
    
   -- Create Course table
 
    CREATE TABLE Course
    (
      CourseId  INT IDENTITY PRIMARY KEY,
      Name      NVARCHAR(50) NOT NULL,
      Teacher   NVARCHAR(256) NOT NULL
    ) 

   -- Create Credit table
 
    CREATE TABLE Credit
    (
      StudentId   INT REFERENCES Student (StudentId),
      CourseId    INT REFERENCES Course (CourseId),
      Grade       DECIMAL(5,2) CHECK (Grade <= 100.00),
      Attempt     TINYINT,
      CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
      (
        StudentId, CourseId, Grade, Attempt
      )
    )
   ```

![Criar tabelas](./media/sql-database-design-first-database/create-tables.png)

3. Expanda o nó 'tabelas' no Pesquisador de Objetos do SQL Server Management Studio para ver as tabelas que você criou.

   ![ssms tabelas criadas](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Carregar dados nas tabelas

1. Crie uma pasta chamada **SampleTableData** na pasta Downloads para armazenar os dados de exemplo de seu banco de dados. 

2. Clique com o botão direito do mouse nos seguintes links e salve-os na pasta **SampleTableData**. 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Abra uma janela de prompt de comando e navegue até a pasta SampleTableData.

4. Execute o comando a seguir para inserir dados de exemplo nas tabelas, substituindo os valores de **ServerName**, **DatabaseName**, **UserName** e **Password** pelos valores do seu ambiente.
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

Agora, você carregou dados de exemplo nas tabelas que criou anteriormente.

## <a name="query-the-tables"></a>Consultar as tabelas

Execute as seguintes consultas para recuperar as informações das tabelas do banco de dados. Veja [Escrevendo consultas SQL](https://technet.microsoft.com/library/bb264565.aspx) para saber mais sobre como escrever consultas SQL. A primeira consulta une todas as quatro tabelas para localizar todos os alunos ensinados por 'Dominick Pope' e que têm uma nota superior a 75% em sua classe. A segunda consulta une todas as quatro tabelas e localiza todos os cursos em que 'Noe Coleman' já se registrou.

1. Em uma janela de consulta do SQL Server Management Studio, execute a seguinte consulta:

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

    SELECT  person.FirstName,
        person.LastName,
        course.Name,
        credit.Grade
    FROM  Person AS person
        INNER JOIN Student AS student ON person.PersonId = student.PersonId
        INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
        INNER JOIN Course AS course ON credit.CourseId = course.courseId
    WHERE course.Teacher = 'Dominick Pope' 
        AND Grade > 75
   ```

2. Em uma janela de consulta do SQL Server Management Studio, execute a seguinte consulta:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

    SELECT  course.Name,
        course.Teacher,
        credit.Grade
    FROM  Course AS course
        INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
        INNER JOIN Student AS student ON student.StudentId = credit.StudentId
        INNER JOIN Person AS person ON person.PersonId = student.PersonId
    WHERE person.FirstName = 'Noe'
        AND person.LastName = 'Coleman'
   ```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Restaurar um banco de dados em um ponto anterior no tempo 

Imagine que você excluiu acidentalmente uma tabela. Isso é algo que você não pode se recuperar facilmente. O Banco de Dados SQL do Azure lhe permite voltar para qualquer ponto no tempo, até os últimos 35 dias, e restaurar esse ponto no tempo em um novo banco de dados. Você pode usar esse banco de dados para recuperar os dados excluídos. As etapas a seguir restauram o banco de dados de exemplo para um ponto anterior à adição das tabelas.

1. Na página do Banco de Dados SQL do seu banco de dados, clique em **Restaurar** na barra de ferramentas. A página **Restaurar** será aberta.

   ![restaurar](./media/sql-database-design-first-database/restore.png)

2. Preencha o formulário **Restaurar** com as informações necessárias:
    * Nome do Banco de Dados: fornece um nome de banco de dados 
    * Ponto no tempo: selecione a guia **Ponto no tempo** no formulário Restaurar 
    * Ponto de restauração: selecione uma hora anterior à alteração do banco de dados
    * Servidor de destino: não é possível alterar esse valor ao restaurar um banco de dados 
    * Pool de banco de dados elástico: selecione **Nenhum**  
    * Tipo de preço: selecione **20 DTUs** e **250 GB** de armazenamento.

   ![ponto de restauração](./media/sql-database-design-first-database/restore-point.png)

3. Clique em **OK** para restaurar o banco de dados [a um ponto no tempo](sql-database-recovery-using-backups.md#point-in-time-restore) anterior à adição das tabelas. A restauração de um banco de dados para um ponto diferente no tempo cria um banco de dados duplicado no mesmo servidor do banco de dados original do ponto no tempo que você especificar, desde que esteja dentro do período de retenção da sua [camada de serviço](sql-database-service-tiers.md).

## <a name="next-steps"></a>Próximas etapas 

Para obter amostras do PowerShell para tarefas comuns, consulte [Amostras do PowerShell de Banco de Dados SQL](sql-database-powershell-samples.md)

