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
ms.custom: tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 03/23/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 313bcf4fbc0ab7f251dd62b1e2151afef8392a55
ms.lasthandoff: 03/28/2017


---

# <a name="design-your-first-azure-sql-database"></a>Criar seu primeiro banco de dados SQL do Azure

Neste tutorial, você usa o Portal do Azure para criar um banco de dados em um novo servidor com um firewall de nível de servidor. Em seguida, você usará o SQL Server Management Studio para criar uma tabela, carregar nessa tabela, consultá-la e adicionar um índice a ela. Por fim, você usará backups automatizados do serviço de Banco de Dados SQL para restaurar o banco de dados para um ponto no tempo anterior à adição dessa nova tabela.

Para concluir este tutorial, certifique-se de ter instalado a versão mais recente do SSMS ([SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx)) 

## <a name="step-1---log-in-to-the-azure-portal"></a>Etapa 1 – Fazer logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="step-2---create-a-sql-database"></a>Etapa 2 – Criar um banco de dados SQL

Um banco de dados SQL do Azure é criado com um conjunto definido de [recursos de computação e armazenamento](sql-database-service-tiers.md). O banco de dados é criado dentro de um [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) e em um [servidor lógico de banco de dados SQL do Azure](sql-database-features.md). 

Execute estas etapas para criar um Banco de Dados SQL que contém os dados de exemplo do Adventure Works LT. 

1. Clique no botão **Novo** no canto superior esquerdo do Portal do Azure.

2. Selecione **Bancos de Dados** na página **Novo** e **Banco de Dados SQL** na página **Bancos de Dados**.

3. Preencha o formulário do Banco de Dados SQL com as informações necessárias: 
   - Nome do Banco de Dados: fornece um nome de banco de dados
   - Assinatura: selecione sua assinatura
   - Grupo de recursos: selecione novo ou existente
   - Fonte: Selecione **Exemplo (AdventureWorksLT)**
   - Servidor: crie um novo servidor (o nome do **Servidor** deve ser globalmente exclusivo)
   - Pool elástico: selecione **Agora não** para esse início rápido
   - Tipo de preço: selecione **20 DTUs** e **250** GB de armazenamento
   - Agrupamento: não é possível alterar esse valor ao importar o banco de dados de exemplo 
   - Fixar no painel: marque esta caixa de seleção

      ![criar banco de dados](./media/sql-database-get-started/create-database-s1.png)

4. Clique em **Criar** quando terminar. O provisionamento demora alguns minutos.
5. Após a conclusão da implantação do Banco de Dados SQL, selecione o **Bancos de Dados SQL** no painel ou selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu novo banco de dados na página **Bancos de Dados SQL**. Uma página de visão geral de seu banco de dados é aberta, mostrando o nome totalmente qualificado do servidor (como **mynewserver20170313.database.windows.net**) e fornece opções para configurações adicionais.

      ![new-sql database](./media/sql-database-get-started/new-database-s1-overview.png) 

## <a name="step-3---create-a-server-level-firewall-rule"></a>Etapa 3 – Criar uma regra de firewall no nível de servidor

O serviço Banco de Dados SQL cria um firewall, impedindo que aplicativos e ferramentas externas se conectem aos seus novos servidor e banco de dados. Execute estas etapas para criar uma [regra de firewall no nível do servidor do Banco de Dados SQL](sql-database-firewall-configure.md) para o endereço IP, a fim de habilitar a conectividade externa por meio do firewall do Banco de Dados SQL. 

1. Clique em **Definir firewall do servidor** na barra de ferramentas do banco de dados. A página **Configurações do firewall** do servidor de Banco de Dados SQL é aberta. 

      ![regra de firewall do servidor](./media/sql-database-get-started/server-firewall-rule.png) 

2. Clique em **Adicionar IP do cliente** na barra de ferramentas e clique em **Salvar**. Uma regra de firewall no nível do servidor é criada para seu endereço IP atual.

3. Clique em **OK** e clique no **X** para fechar a página de configurações do Firewall.

Agora você pode se conectar ao banco de dados e ao seu servidor usando o SQL Server Management Studio ou outra ferramenta de sua escolha.

## <a name="step-4---get-connection-information"></a>Etapa 4 – Obter informações de conexão

Obtenha o nome de servidor totalmente qualificado para o servidor de Banco de Dados SQL do Azure no Portal do Azure. Use o nome do servidor totalmente qualificado para se conectar ao servidor usando o SQL Server Management Studio.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de Dados SQL**. 
3. No painel **Essentials**, na página do Portal do Azure de seu banco de dados, localize e copie o **Nome do servidor**.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="step-5---connect-to-the-server-using-ssms"></a>Etapa 5 – Conectar-se ao servidor usando o SSMS

Use o SQL Server Management Studio para estabelecer uma conexão com seu servidor de Banco de Dados SQL do Azure.

1. Digite **SSMS** na caixa de pesquisa do Windows e clique em **Enter** para abrir o SSMS.

2. Na caixa de diálogo **Conectar ao Servidor**, insira as informações a seguir:
   - **Tipo de servidor**: especifique mecanismo de banco de dados
   - **Nome do servidor**: insira seu nome do servidor totalmente qualificado, como **mynewserver20170313.database.windows.net**
   - **Autenticação**: especifique a Autenticação do SQL Server
   - **Logon**: insira a conta do administrador do servidor
   - **Senha**: insira a senha para sua conta do administrador do servidor
 
    <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. Clique em **Conectar**. A janela Pesquisador de Objetos abre no SSMS. 

    <img src="./media/sql-database-connect-query-ssms/connected.png" alt="connected to server" style="width: 780px;" />

4. No Pesquisador de Objetos, expanda **Bancos de Dados** e expanda **mySampleDatabase** para exibir os objetos no banco de dados de exemplo.

## <a name="step-6---create-and-query-a-table"></a>Etapa 6 – Criar e consultar uma tabela 
1. No Pesquisador de Objetos, clique com o botão direito em **mySampleDatabase** e clique em **Nova Consulta**. Uma janela de consulta em branco conectada ao seu banco de dados é aberta.
2. Na janela de consulta, execute a consulta a seguir:

   ```sql 
   CREATE TABLE [dbo].[Students]
   (
     [student_id] int, 
     [name] varchar(100),
     [age] int,
     [email] varchar(100),
     [AddressID] int REFERENCES [SalesLT].[Address] (AddressID)
   );
   ```

   Quando a consulta for concluída, você terá criado uma tabela vazia no banco de dados chamado Alunos.

3. Em uma janela de consulta do SSMS, execute a consulta a seguir: 

   ```sql
   SELECT name, age, email 
   FROM [dbo].[Students]
   ```

   A tabela Alunos não retorna nenhum dado.

## <a name="step-7---load-data-into-the-table"></a>Etapa 7 – Carregar dados na tabela 
1. Abra una janela de prompt de comando.

2. Execute o comando do PowerShell a seguir para baixar um arquivo de texto de exemplo para o diretório atual.

   ```powershell
   powershell -command "& { (New-Object Net.WebClient).DownloadFile('https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData.txt', 'SampleStudentData.txt'); echo 'Download complete' }" 
   ``` 

3. Quando isso for concluído, execute o comando a seguir para inserir 1.000 linhas na tabela Alunos, substituindo os valores de **ServerName**, **DatabaseName**, **UserName** e **Password** pelos valores do seu ambiente.

   ```bcp
   bcp Students in SampleStudentData.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

Agora, você carregou dados de exemplo para a tabela que você criou anteriormente.

## <a name="step-8---add-an-index-to-a-table"></a>Etapa 8 – Adicionar um índice a uma tabela
Para tornar a busca por valores específicos na tabela mais eficiente, crie um índice na tabela Alunos. Um índice organiza os dados de tal forma que agora todos os dados precisam ser verificados para que seja possível localizar um valor específico.

1. Em uma janela de consulta do SSMS, execute a consulta a seguir:

   ```sql 
   CREATE NONCLUSTERED INDEX IX_Age ON Students (age);
   ```

2. Em uma janela de consulta do SSMS, execute a consulta a seguir:

   ```sql
   SELECT name, age, email 
   FROM [dbo].[Students]
   WHERE age > 20
   ```

   Esta consulta retorna o nome, a idade e o email de alunos com mais de 20 anos de idade.

## <a name="step-9---restore-a-database-to-a-point-in-time"></a>Etapa 9 – Restaurar um banco de dados para um momento específico 
Bancos de dados no Azure têm [backups contínuos](sql-database-automated-backups.md) que são realizados automaticamente a cada intervalo de 5 a 10 minutos. Esses backups permitem restaurar o banco de dados para um ponto anterior no tempo. Restaurar um banco de dados para um ponto diferente no tempo cria um banco de dados duplicado no mesmo servidor do banco de dados original do ponto no tempo que você especificar (dentro do período de retenção para a camada de serviço). As etapas a seguir restauram o banco de dados de exemplo para um ponto anterior à adição da tabela **Alunos**. 

1. Na página do Banco de Dados SQL do seu banco de dados, clique em **Restaurar** na barra de ferramentas. A página **Restaurar** será aberta.

    <img src="./media/sql-database-design-first-database/restore.png" alt="restore" style="width: 780px;" />

2. Preencha o formulário **Restaurar** com as informações necessárias:
    * Nome do Banco de Dados: fornece um nome de banco de dados 
    * Ponto no tempo: selecione a guia **Ponto no tempo** no formulário Restaurar 
    * Ponto de restauração: selecione uma hora anterior à alteração do banco de dados
    * Servidor de destino: não é possível alterar esse valor ao restaurar um banco de dados 
    * Pool de banco de dados elástico: selecione **Nenhum**  
    * Tipo de preço: selecione **20 DTUs** e **250 GB** de armazenamento.

    <img src="./media/sql-database-design-first-database/restore-point.png" alt="restore-point" style="width: 780px;" />

3. Clique em **OK** para restaurar o banco de dados de exemplo para um ponto no tempo anterior à adição da tabela *Alunos*.

## <a name="next-steps"></a>Próximas etapas 
Para obter amostras do PowerShell para tarefas comuns, consulte [Amostras do PowerShell de Banco de Dados SQL](sql-database-powershell-samples.md)

