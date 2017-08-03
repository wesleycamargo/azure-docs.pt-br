---
title: Criar seu primeiro banco de dados SQL do Azure | Microsoft Docs
description: Aprenda a criar seu primeiro banco de dados SQL do Azure.
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop databases
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 06/20/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 8af9ea0a76b9a0606284505195ee3f52b1964604
ms.contentlocale: pt-br
ms.lasthandoff: 06/28/2017


---

# <a name="design-your-first-azure-sql-database"></a>Criar seu primeiro banco de dados SQL do Azure

O Banco de Dados SQL do Azure é um DBaaS (banco de dados como serviço) no Microsoft Cloud (“Azure”). Neste tutorial, você aprenderá a usar o Portal do Azure e o SSMS ([SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx)) para: 

> [!div class="checklist"]
> * Criar um banco de dados no Portal do Azure
> * Configurar uma regra de firewall de nível de servidor no Portal do Azure
> * Conectar-se ao banco de dados com o SSMS
> * Criar tabelas com SSMS
> * Carregar dados em massa com o BCP
> * Consultar dados com o SSMS
> * Restaurar um banco de dados para uma [restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore) no Portal do Azure

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, certifique-se de ter instalado a versão mais recente do SSMS [(SQL Server Management Studio)](https://msdn.microsoft.com/library/ms174173.aspx). 

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-blank-sql-database-in-the-azure-portal"></a>Criar um Banco de Dados SQL em branco no Portal do Azure

Um banco de dados SQL do Azure é criado com um conjunto definido de [recursos de computação e armazenamento](sql-database-service-tiers.md). O banco de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e em um [servidor lógico de banco de dados SQL do Azure](sql-database-features.md). 

Siga estas etapas para criar um banco de dados SQL em branco. 

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

2. Selecione **Bancos de Dados** na página **Novo** e **Banco de Dados SQL** na página **Bancos de Dados**. 

   ![criar banco de dados vazio](./media/sql-database-design-first-database/create-empty-database.png)

3. Preencha o formulário do Banco de Dados SQL com as informações abaixo, conforme mostrado na imagem anterior:   

   | Configuração       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome do banco de dados** | mySampleDatabase | Para ver os nomes do banco de dados válidos, consulte [Identificadores do Banco de Dados](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers). | 
   | **Assinatura** | Sua assinatura  | Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   | **Grupo de recursos** | myResourceGroup | Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   | **Selecionar fonte** | Banco de dados em branco | Especifica que um banco de dados em branco deve ser criado. |

4. Clique em **Servidor** para criar e configurar um novo servidor para o novo banco de dados. Preencha o **formulário Novo servidor** com as seguintes informações: 

   | Configuração       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome do servidor** | Qualquer nome exclusivo globalmente | Para ver os nomes do servidor válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). | 
   | **Logon de administrador do servidor** | Qualquer nome válido | Para ver os nomes de logon válidos, consulte [Identificadores do Banco de Dados](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers).|
   | **Senha** | Qualquer senha válida | Sua senha deve ter, pelo menos, oito caracteres e deve conter caracteres de três das seguintes categorias: caracteres com letras maiúsculas, letras minúsculas, números e caracteres não alfanuméricos. |
   | **Localidade** | Qualquer local válido | Para obter mais informações sobre as regiões, consulte [Regiões do Azure](https://azure.microsoft.com/regions/). |

   ![criar database-server](./media//sql-database-design-first-database/create-database-server.png)

5. Clique em **Selecionar**.

6. Clique em **Tipo de preço** para especificar o nível de desempenho e o tipo de serviço para o novo banco de dados. Para este tutorial, selecione **20 DTUs** e **250** GB de armazenamento.

   ![Criar database-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. Clique em **Aplicar**.  

8. Selecione um **agrupamento** para o banco de dados em branco (para este tutorial, use o valor padrão). Para obter mais informações sobre agrupamentos, consulte [Agrupamentos](https://docs.microsoft.com/sql/t-sql/statements/collations)

9. Clique em **Criar** para provisionar o banco de dados. O provisionamento leva cerca de um minuto e meio para concluir. 

10. Na barra de ferramentas, clique em **Notificações** para monitorar o processo de implantação.

   ![notificação](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Criar uma regra de firewall de nível de servidor no portal do Azure

O serviço do Banco de Dados SQL cria um firewall no nível do servidor impedindo que os aplicativos e ferramentas externos conectem o servidor ou os bancos de dados no servidor, a menos que uma regra de firewall seja criada para abrir o firewall para endereços IP específicos. Execute estas etapas a fim de criar uma [regra de firewall no nível do servidor do Banco de Dados SQL](sql-database-firewall-configure.md) para o endereço IP do seu cliente e habilitar a conectividade externa por meio do firewall do Banco de Dados SQL somente para seu endereço IP. 

> [!NOTE]
> O Banco de Dados SQL se comunica pela porta 1433. Se você estiver tentando conectar-se a partir de uma rede corporativa, o tráfego de saída pela porta 1433 poderá não ser permitido pelo firewall de sua rede. Se isto acontecer, você não poderá se conectar ao servidor de Banco de Dados SQL do Azure, a menos que o departamento de TI abra a porta 1433.
>

1. Depois da implantação ser concluída, clique em **Bancos de dados SQL** no menu à esquerda, depois, clique em **mySampleDatabase** na página **Bancos de dados SQL**. Uma página de visão geral de seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver20170313.database.windows.net**) e fornece opções para configurações adicionais. Copie esse nome totalmente qualificado do servidor para um uso posterior.

   > [!IMPORTANT]
   > Você precisará desse nome totalmente qualificado do servidor para conectar o servidor e seus bancos de dados nos inícios rápidos subsequentes.
   > 

   ![nome do servidor](./media/sql-database-get-started-portal/server-name.png) 

2. Clique em **Definir o firewall do servidor** na barra de ferramentas, conforme mostrado na imagem anterior. A página **Configurações do firewall** do servidor de Banco de Dados SQL é aberta. 

   ![regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule.png) 


3. Clique em **Adicionar IP do cliente** na barra de ferramentas para adicionar seu endereço IP atual a uma nova regra de firewall. Uma regra de firewall pode abrir a porta 1433 para um único endereço IP ou um intervalo de endereços IP.

4. Clique em **Salvar**. Uma regra de firewall no nível do servidor é criada para a porta de abertura 1433 de seu endereço IP atual no servidor lógico.

   ![definir regra de firewall do servidor](./media/sql-database-get-started-portal/server-firewall-rule-set.png) 

4. Clique em **OK**, em seguida, feche a página **Configurações do Firewall**.

Agora, você pode conectar o servidor do Banco de Dados SQL e seus bancos de dados usando o SQL Server Management Studio ou outra ferramenta de sua escolha neste endereço IP usando a conta do administrador do servidor criada anteriormente.

> [!IMPORTANT]
> Por padrão, o acesso através do firewall do Banco de Dados SQL está habilitado para todos os serviços do Azure. Clique em **DESATIVAR** nesta página para desabilitar todos os serviços do Azure.

## <a name="get-connection-information-in-the-azure-portal"></a>Obter informações de conexão no Portal do Azure

Obtenha o nome de servidor totalmente qualificado para o servidor de Banco de Dados SQL do Azure no Portal do Azure. Use o nome do servidor totalmente qualificado para se conectar ao servidor usando o SQL Server Management Studio.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de Dados SQL**. 
3. No painel **Essentials**, na página do Portal do Azure de seu banco de dados, localize e copie o **Nome do servidor**.

   ![informações da conexão](./media/sql-database-get-started-portal/server-name.png)

## <a name="connect-to-the-database-with-ssms"></a>Conectar-se ao banco de dados com o SSMS

Use o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) para estabelecer uma conexão com seu servidor do Banco de Dados SQL do Azure.

1. Abra o SQL Server Management Studio.

2. Na caixa de diálogo **Conectar ao Servidor**, insira as informações a seguir:

   | Configuração       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Tipo de servidor | Mecanismo de banco de dados | Esse valor é obrigatório |
   | Nome do servidor | O nome do servidor totalmente qualificado | O nome deve ser semelhante como: **mynewserver20170313.database.windows.net**. |
   | Autenticação | Autenticação do SQL Server | A Autenticação do SQL é o único tipo de autenticação que configuramos neste tutorial. |
   | Logon | A conta do administrador do servidor | Esta é a conta que você especificou quando criou o servidor. |
   | Senha | A senha para sua conta do administrador do servidor | Esta é a senha que você especificou quando criou o servidor. |

   ![conectar-se ao servidor](./media/sql-database-connect-query-ssms/connect.png)

3. Clique em **Opções** na caixa de diálogo **Conectar servidor**. Na seção **Conectar ao banco de dados**, digite **mySampleDatabase** para conectar-se a este banco de dados.

   ![conectar o banco de dados no servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Clique em **Conectar**. A janela Pesquisador de Objetos abre no SSMS. 

5. No Pesquisador de Objetos, expanda **Bancos de Dados** e expanda **mySampleDatabase** para exibir os objetos no banco de dados de exemplo.

   ![objetos de banco de dados](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database-with-ssms"></a>Criar tabelas no banco de dados com SSMS 

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
   PersonId   INT IDENTITY PRIMARY KEY,
   FirstName   NVARCHAR(128) NOT NULL,
   MiddelInitial NVARCHAR(10),
   LastName   NVARCHAR(128) NOT NULL,
   DateOfBirth   DATE NOT NULL
   )
   
   -- Create Student table
 
   CREATE TABLE Student
   (
   StudentId INT IDENTITY PRIMARY KEY,
   PersonId  INT REFERENCES Person (PersonId),
   Email   NVARCHAR(256)
   )
   
   -- Create Course table
 
   CREATE TABLE Course
   (
   CourseId  INT IDENTITY PRIMARY KEY,
   Name   NVARCHAR(50) NOT NULL,
   Teacher   NVARCHAR(256) NOT NULL
   ) 

   -- Create Credit table
 
   CREATE TABLE Credit
   (
   StudentId   INT REFERENCES Student (StudentId),
   CourseId   INT REFERENCES Course (CourseId),
   Grade   DECIMAL(5,2) CHECK (Grade <= 100.00),
   Attempt   TINYINT,
   CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
   (
   StudentId, CourseId, Grade, Attempt
   )
   )
   ```

   ![Criar tabelas](./media/sql-database-design-first-database/create-tables.png)

3. Expanda o nó 'tabelas' no Pesquisador de Objetos do SQL Server Management Studio para ver as tabelas que você criou.

   ![ssms tabelas criadas](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables-with-ssms"></a>Carregar dados nas tabelas com SSMS

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

## <a name="query-the-tables-with-ssms"></a>Consultar as tabelas com SSMS

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

## <a name="restore-a-database-to-a-previous-point-in-time-using-the-azure-portal"></a>Restaurar um banco de dados para um ponto anterior no tempo usando o Portal do Azure

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

3. Clique em **OK** para restaurar o banco de dados [a um ponto no tempo](sql-database-recovery-using-backups.md#point-in-time-restore) anterior à adição das tabelas. A restauração de um banco de dados para um ponto diferente no tempo cria um banco de dados duplicado no mesmo servidor do banco de dados original do ponto no tempo que você especificar, contanto que esteja dentro do período de retenção da sua [camada de serviço](sql-database-service-tiers.md).

## <a name="next-steps"></a>Próximas etapas 
Neste tutorial, você aprendeu as tarefas básicas de banco de dados, como criar um banco de dados e tabelas, carregar e consultar dados e restaurar o banco de dados para um ponto anterior no tempo. Você aprendeu como:
> [!div class="checklist"]
> * Criar um banco de dados
> * Configurar uma regra de firewall
> * Conecte-se ao banco de dados com o [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS.exe)
> * Criar tabelas
> * Carregar dados em massa
> * Consultar os dados
> * Restaure o banco de dados para um ponto anterior no tempo usando os recursos de [recuperação pontual](sql-database-recovery-using-backups.md#point-in-time-restore) do Banco de Dados SQL. Avance para o próximo tutorial para aprender como migrar seus dados.

> [!div class="nextstepaction"]
>[Migrar seu banco de dados do SQL Server para o banco de dados SQL do Azure](sql-database-migrate-your-sql-server-database.md)

