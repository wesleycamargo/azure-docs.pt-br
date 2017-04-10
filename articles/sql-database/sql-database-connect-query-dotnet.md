---
title: Conectar-se ao Banco de Dados SQL do Azure usando .NET (C#) | Microsoft Docs
description: "Use o código de exemplo neste início rápido para criar um aplicativo moderno com C# e sustentado por um poderoso banco de dados relacional na nuvem com o Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 7faca033-24b4-4f64-9301-b4de41e73dfd
ms.service: sql-database
ms.custom: quick start
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/28/2017
ms.author: andrela;sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: c6c0c218b8d0456d37a4514238675fd8e75faf9d
ms.lasthandoff: 03/30/2017


---
# <a name="azure-sql-database-use-net-c-to-connect-and-query-data"></a>Banco de Dados SQL do Azure: Usar .NET (C#) para conectar e consultar dados

Use [C# e ADO.NET](https://msdn.microsoft.com/library/kb9s9ks0.aspx) para conectar e consultar um Banco de Dados SQL do Azure. Este guia detalha o uso do C# para se conectar a um Banco de Dados SQL do Azure e, depois, executar instruções de consulta, inserção, atualização e exclusão.

Este início rápido usa como ponto de partida os recursos criados em um destes inícios rápidos:

- [Criar Banco de dados - Portal](sql-database-get-started-portal.md)
- [Criar Banco de dados - CLI](sql-database-get-started-cli.md)

## <a name="configure-development-environment"></a>Configurar o ambiente de desenvolvimento

As seções a seguir detalham como configurar os ambientes de desenvolvimento existentes do Mac OS, Linux (Ubuntu) e Windows para trabalhar com o Banco de Dados SQL do Azure.

### <a name="mac-os"></a>**Mac OS**
Abra seu terminal e navegue até um diretório no qual você planeja criar o projeto .NET Core. Digite os comandos a seguir para instalar **brew** e **OpenSSL** e **.NET Core**. 

```C#
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

Instale o .NET Core no macOS. Baixe o [instalador oficial](https://go.microsoft.com/fwlink/?linkid=843444). Esse instalador instalará as ferramentas e as colocará em seu PATH para que você possa executar dotnet do Console

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra seu terminal e navegue até um diretório no qual você planeja criar o projeto .NET Core. Digite os comandos a seguir para instalar o **.NET Core**.

```C#
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
sudo apt-get update
sudo apt-get install dotnet-dev-1.0.1
```

### <a name="windows"></a>**Windows**
Instale o Visual Studio 2015 Community Edition e o .NET Framework. Se você já tiver o Visual Studio instalado em seu computador, ignore as próximas etapas.

A Comunidade do Visual Studio 2015 é um IDE completo, extensível, gratuito para a criação de aplicativos modernos para Android, iOS, Windows, bem como aplicativos de banco de dados e serviços de nuvem.

1. Baixe o [instalador](https://go.microsoft.com/fwlink/?LinkId=691978). 
2. Execute o instalador e siga os prompts de instalação para concluir a instalação.

## <a name="get-connection-information"></a>Obter informações de conexão

Obtenha a cadeia de conexão no Portal do Azure. Use a cadeia de conexão para se conectar ao Banco de Dados SQL do Azure.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de Dados SQL**. 
3. No painel **Essentials** de seu banco de dados, examine o nome do servidor totalmente qualificado. 

    <img src="./media/sql-database-connect-query-dotnet/connection-strings.png" alt="connection strings" style="width: 780px;" />

4. Clique em **Mostrar cadeias de conexão de banco de dados**.

5. Examine a cadeia de conexão completa do **ADO.NET**.

    <img src="./media/sql-database-connect-query-dotnet/adonet-connection-string.png" alt="ADO.NET connection string" style="width: 780px;" />
    
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

3. Use [SqlCommand.ExecuteReader](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executereader.aspx) com uma instrução [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) de Transact-SQL para consultar dados no Banco de Dados SQL do Azure. Adicionar os valores apropriados para seu servidor

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

Use [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) com uma instrução [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) do Transact-SQL para inserir dados no Banco de Dados SQL do Azure.

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

Use [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) com uma instrução [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) de Transact-SQL para atualizar dados no Banco de Dados SQL do Azure.

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

Use [SqlCommand.ExecuteNonQuery](https://msdn.microsoft.com/library/system.data.sqlclient.sqlcommand.executenonquery.aspx) com uma instrução [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) de Transact-SQL para excluir dados no Banco de Dados SQL do Azure.

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

- Para obter a documentação do .NET, consulte [Documentação do .NET](https://docs.microsoft.com/dotnet/).
- Para saber mais sobre como consultar e editar dados usando o Visual Studio Code, confira [Visual Studio Code](https://code.visualstudio.com/docs).

