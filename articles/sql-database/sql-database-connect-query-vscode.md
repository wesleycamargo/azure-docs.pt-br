---
title: 'VS Code: Conectar e consultar dados no Banco de Dados SQL do Azure | Microsoft Docs'
description: "Saiba como se conectar a um Banco de Dados SQL no Azure usando o Visual Studio Code. Em seguida, execute instruções T-SQL (Transact-SQL) para consultar e editar dados."
metacanonical: 
keywords: conectar-se ao banco de dados sql
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 676bd799-a571-4bb8-848b-fb1720007866
ms.service: sql-database
ms.custom: quick start manage
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 45405c7bb9993d1fd529b25b599c3cd7f459843c
ms.lasthandoff: 04/20/2017


---
# <a name="azure-sql-database-use-visual-studio-code-to-connect-and-query-data"></a>Banco de Dados SQL do Azure: Use Visual Studio Code para se conectar e consultar dados

O [Visual Studio Code](https://code.visualstudio.com/docs) é um editor de código gráfico para o Linux, macOS e Windows que oferece suporte às extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consultar o Microsoft SQL Server, Banco de Dados SQL do Azure e SQL Data Warehouse. Este início rápido demonstra como usar o Visual Studio Code para conectar um banco de dados SQL do Azure, então, usar as instruções Transact-SQL para consultar, inserir, atualizar e excluir os dados no banco de dados.

Este início rápido usa como ponto de partida os recursos criados em um destes inícios rápidos:

- [Criar Banco de dados - Portal](sql-database-get-started-portal.md)
- [Criar Banco de dados - CLI](sql-database-get-started-cli.md)

Antes de começar, verifique se você instalou a versão mais recente do [Visual Studio Code](https://code.visualstudio.com/Download) e carregou a [extensão mssql](https://aka.ms/mssql-marketplace). Para obter orientações de instalação para a extensão mssql, consulte [Instalar o VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-vs-code) e consulte [mssql para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql). 

## <a name="configure-vs-code"></a>Configurar o VS Code 

### <a name="mac-os"></a>**Mac OS**
Para o macOS, você precisa instalar o OpenSSL, que é um pré-requisito do DotNet Core a extensão mssql usa. Abra seu terminal e digite os seguintes comandos para instalar o **brew** e o **OpenSSL**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Nenhuma configuração especial é necessária.

### <a name="windows"></a>**Windows**

Nenhuma configuração especial é necessária.

## <a name="get-connection-information"></a>Obter informações de conexão

Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor, nome do banco de dados e informações de logon nos próximos procedimentos.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de Dados SQL**. 
3. Na página **Visão geral** do banco de dados, examine o nome totalmente qualificado do servidor, como mostrado na imagem a seguir. Você pode passar o mouse sobre o nome do servidor para abrir a opção **Clique para copiar**.

   ![informações da conexão](./media/sql-database-connect-query-ssms/connection-information.png) 

4. Se você esqueceu as informações de logon para o servidor do Banco de Dados SQL, navegue até a página do servidor para exibir o nome de administrador do servidor e, se necessário, redefinir a senha. 

## <a name="set-language-mode-to-sql"></a>Definir o modo de linguagem para SQL

Defina o modo de linguagem como **SQL** no Visual Studio Code para permitir comandos mssql e T-SQL IntelliSense.

1. Abra uma nova janela do Visual Studio Code. 

2. Clique em **Texto sem Formatação** no canto inferior direito da barra de status.
3. No menu suspenso **Selecionar modo de linguagem** aberto, digite **SQL**e pressione **ENTER** para definir o modo de linguagem para SQL. 

   ![Modo de linguagem SQL](./media/sql-database-connect-query-vscode/vscode-language-mode.png)

## <a name="connect-to-your-database-in-the-sql-database-logical-server"></a>Conectar o banco de dados no servidor lógico do Banco de Dados SQL

Use o Visual Studio Code para estabelecer uma conexão com seu servidor de Banco de Dados SQL do Azure.

> [!IMPORTANT]
> Antes de continuar, verifique se você tem seu servidor, banco de dados e informações de logon prontos. Depois que você começar a inserir as informações de perfil da conexão, se você mudar o foco do Visual Studio Code, terá que reiniciar a criação do perfil de conexão.
>

1. No VS Code, pressione **CTRL + SHIFT + P** (ou **F1**) para abrir a Paleta de Comandos.

2. Digite **sqlcon** e pressione **ENTER**.

3. Pressione **ENTER** para selecionar **Criar Perfil de Conexão**. Isso cria um perfil de conexão para a instância do SQL Server.

4. Siga os prompts para especificar as propriedades de conexão para o novo perfil de conexão. Depois de especificar cada valor, pressione **ENTER** para continuar. 

   A tabela a seguir descreve as propriedades do Perfil de Conexão.

   | Configuração | Descrição |
   |-----|-----|
   | **Nome do servidor** | Insira seu nome do servidor totalmente qualificado, como **mynewserver20170313.database.windows.net** |
   | **Nome do banco de dados** | Insira o nome do banco de dados, como **mySampleDatabase** |
   | **Autenticação** | Selecione Logon do SQL |
   | **Nome de usuário** | Insira a conta do administrador do servidor |
   | **Senha (Logon do SQL)** | Insira a senha para sua conta do administrador do servidor | 
   | **Salvar a Senha?** | Selecione **Sim** ou **Não** |
   | **[Opcional] Insira um nome para este perfil** | Insira um nome de perfil de conexão, como **mySampleDatabase**. 

5. Pressione a tecla **ESC** para fechar a mensagem de informações que informa que o perfil foi criado e está conectado.

6. Verifique se sua conexão na barra de status.

   ![Status da conexão](./media/sql-database-connect-query-vscode/vscode-connection-status.png)

## <a name="query-data"></a>Consultar dados

Use o seguinte código para consultar os 20 principais produtos por categoria usando a instrução [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) do Transact-SQL.

1. Na janela **Editor**, insira a seguinte consulta na janela de consulta vazia:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Pressione **CTRL + SHIFT + E** para recuperar dados das tabelas Product e ProductCategory.

    ![Consultar](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Inserir dados

Use o código a seguir para inserir um novo produto na tabela SalesLT.Product usando a instrução [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) do Transact-SQL.

1. Na janela **Editor**, exclua a consulta anterior e insira a seguinte consulta:

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate]
           )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Pressione **CTRL + SHIFT + E** para inserir uma nova linha na tabela Product.

## <a name="update-data"></a>Atualizar dados

Use o código a seguir para atualizar o novo produto que você adicionou anteriormente usando a instrução [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) do Transact-SQL.

1.  Na janela **Editor**, exclua a consulta anterior e insira a seguinte consulta:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Pressione **CTRL + SHIFT + E** para atualizar a linha especificada na tabela Product.

## <a name="delete-data"></a>Excluir dados

Use o código a seguir para excluir o novo produto que você adicionou anteriormente usando a instrução [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) do Transact-SQL.

1. Na janela **Editor**, exclua a consulta anterior e insira a seguinte consulta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Pressione **CTRL + SHIFT + E** para excluir a linha especificada na tabela Product.

## <a name="next-steps"></a>Próximas etapas

- Para conectar e consultar usando SQL Server Management Studio, veja [Conectar e consultar com o SSMS](sql-database-connect-query-ssms.md)
- Para conectar e consultar usando o .NET, veja [Conectar e consultar com o .NET](sql-database-connect-query-dotnet.md).
- Para conectar e consultar usando o PHP, veja [Conectar e consultar com o PHP](sql-database-connect-query-php.md).
- Para conectar e consultar usando o Node.js, veja [Conectar e consultar com o Node.js](sql-database-connect-query-nodejs.md).
- Para conectar e consultar usando o Java, veja [Conectar e consultar com o Java](sql-database-connect-query-java.md).
- Para conectar e consultar usando o Python, veja [Conectar e consultar com o Python](sql-database-connect-query-python.md).
- Para conectar e consultar usando o Ruby, veja [Conectar e consultar com o Ruby](sql-database-connect-query-ruby.md).

