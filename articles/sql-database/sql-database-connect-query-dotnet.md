---
title: Conectar-se ao Banco de Dados SQL do Azure usando .NET (C#) | Microsoft Docs
description: "Apresenta um exemplo de código .NET que pode ser usado para conectar e consultar o Banco de Dados SQL do Azure"
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/23/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 4faa029062fef6495debd5d787a1d86f4747453b
ms.contentlocale: pt-br
ms.lasthandoff: 05/26/2017


---
# <a name="azure-sql-database-use-net-c-to-connect-and-query-data"></a>Banco de Dados SQL do Azure: Usar .NET (C#) para conectar e consultar dados

Este guia rápido demonstra como usar o [C# e ADO.NET](https://msdn.microsoft.com/library/kb9s9ks0.aspx) para conectar um banco de dados SQL do Azure, em seguida, usar as instruções Transact-SQL para consultar, inserir, atualizar e excluir dados no banco de dados nas plataformas Windows, Mac OS e Ubuntu Linux.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido usa como ponto de partida os recursos criados em um destes inícios rápidos:

- [Criar Banco de dados - Portal](sql-database-get-started-portal.md)
- [Criar Banco de dados - CLI](sql-database-get-started-cli.md)
- [Criar Banco de dados - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-net"></a>Instalar o .NET

As etapas nesta seção pressupõem que você esteja familiarizado com o desenvolvimento usando o .NET e começou recentemente a trabalhar com o Banco de Dados SQL. Se você for novo no desenvolvimento com o .NET, acesse o [Criar um aplicativo usando o SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) e selecione **C#** e, em seguida, selecione o sistema operacional.

### <a name="windows-net-framework-and-net-core"></a>**Windows .NET Framework e .NET Core**

A Comunidade do Visual Studio 2017 é um IDE completo, extensível, gratuito para a criação de aplicativos modernos para Android, iOS, Windows, bem como aplicativos da Web e de banco de dados, e serviços de nuvem. Você pode instalar o .NET Framework completo ou apenas o .NET Core. Os trechos de código no início rápido funcionam com qualquer um. Se você já tiver o Visual Studio instalado em seu computador, ignore as próximas etapas.

1. Baixe o [instalador do Visual Studio 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15). 
2. Execute o instalador e siga os prompts de instalação para concluir a instalação.

### <a name="mac-os"></a>**Mac OS**
Abra seu terminal e navegue até um diretório no qual você planeja criar o projeto .NET Core. Digite os comandos a seguir para instalar **brew** e **OpenSSL** e **.NET Core**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

Instale o .NET Core no macOS. Baixe o [instalador oficial](https://go.microsoft.com/fwlink/?linkid=843444). Esse instalador instalará as ferramentas e as colocará em seu PATH para que você possa executar o dotnet a partir do Console

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra seu terminal e navegue até um diretório no qual você planeja criar o projeto .NET Core. Digite os comandos a seguir para instalar o **.NET Core**.

```bash
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-dev-1.0.1
```

## <a name="get-connection-information"></a>Obter informações de conexão

Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor, nome do banco de dados e informações de logon nos próximos procedimentos.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de Dados SQL**. 
3. Na página **Visão geral** do banco de dados, analise o nome totalmente qualificado do servidor, como mostrado na imagem abaixo. Passe o mouse sobre o nome do servidor para abrir a opção **Clique para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se você esquecer as informações de logon para o servidor do Banco de Dados SQL do Azure, navegue até a página do servidor do Banco de Dados SQL para exibir o nome de administrador do servidor e, se necessário, redefinir a senha.

5. Clique em **Mostrar cadeias de conexão de banco de dados**.

6. Examine a cadeia de conexão completa do **ADO.NET**.

    ![Cadeia de conexão do ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)
  
## <a name="add-systemdatasqlclient"></a>Adicionar System.Data.SqlClient
Ao usar o .NET core, adicione System.Data.SqlClient ao seu arquivo de projeto ***csproj*** como uma dependência.

```xml
<ItemGroup>
    <PackageReference Include="System.Data.SqlClient" Version="4.3.0" />
</ItemGroup>
```

## <a name="select-data"></a>Selecionar dados

1. No ambiente de desenvolvimento, abra um arquivo de código em branco.
2. Adicione ```using System.Data.SqlClient``` ao seu arquivo de código ([System.Data.SqlClient namespace](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)). 

3. Use o seguinte código para consultar os 20 principais produtos por categoria usando o comando [SqlCommand.ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) com a instrução [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) do Transact-SQL. Adicione os valores apropriados para o seu servidor, banco de dados, usuário e senha.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                    sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                    sb.Append("JOIN [SalesLT].[Product] p ");
                    sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                    String sql = sb.ToString();

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="insert-data"></a>Inserir dados

Use o código a seguir para inserir um novo produto na tabela SalesLT.Product usando [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) com uma instrução [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) do Transact-SQL. Adicione os valores apropriados para o seu servidor, banco de dados, usuário e senha.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nInsert data example:");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("INSERT INTO [SalesLT].[Product] ([Name], [ProductNumber], [Color], [StandardCost], [ListPrice], [SellStartDate]) ");
                    sb.Append("VALUES (@Name, @ProductNumber, @Color, @StandardCost, @ListPrice, @SellStartDate);");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        command.Parameters.AddWithValue("@ProductNumber", "200989");
                        command.Parameters.AddWithValue("@Color", "Blue");
                        command.Parameters.AddWithValue("@StandardCost", 75);
                        command.Parameters.AddWithValue("@ListPrice", 80);
                        command.Parameters.AddWithValue("@SellStartDate", "7/1/2016");
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) inserted");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="update-data"></a>Atualizar dados

Use o código a seguir para atualizar o novo produto que você adicionou anteriormente usando [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) com uma instrução [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) do Transact-SQL. Adicione os valores apropriados para o seu servidor, banco de dados, usuário e senha.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nUpdate data example");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("UPDATE [SalesLT].[Product] SET ListPrice = @ListPrice WHERE Name = @Name;");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        command.Parameters.AddWithValue("@ListPrice", 500);
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) updated");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="delete-data"></a>Excluir dados

Use o código a seguir para excluir o novo produto que você adicionou anteriormente usando [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) com uma instrução [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) do Transact-SQL. Adicione os valores apropriados para o seu servidor, banco de dados, usuário e senha.

```csharp
using System;
using System.Data;
using System.Data.SqlClient;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nDelete data example");
                    Console.WriteLine("=========================================\n");

                    connection.Open();       
                    StringBuilder sb = new StringBuilder();
                    sb.Append("DELETE FROM SalesLT.Product WHERE Name = @Name;");
                    String sql = sb.ToString();
                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        command.Parameters.AddWithValue("@Name", "BrandNewProduct");
                        int rowsAffected = command.ExecuteNonQuery();
                        Console.WriteLine(rowsAffected + " row(s) deleted");
                    }         
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas
- [Criar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)
- [Documentação do .NET](https://docs.microsoft.com/dotnet/).
- [Conectar e consultar com SSMS](sql-database-connect-query-ssms.md)
- [Conecte e consulte com o Visual Studio Code](sql-database-connect-query-vscode.md).


