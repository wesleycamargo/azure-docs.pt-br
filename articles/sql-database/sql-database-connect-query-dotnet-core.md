---
title: Usar o .NET Core para consultar o Banco de Dados SQL do Azure | Microsoft Docs
description: Este tópico mostra como usar o .NET Core para criar um programa que se conecta a um Banco de Dados SQL do Azure e o consulta usando instruções Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/21/2018
ms.openlocfilehash: 0b6e5116a90c66852ac39f67f9f32c94470e5332
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565319"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Início Rápido: Usar o .NET Core (C#) para consultar um banco de dados SQL do Azure

Neste início rápido, você usará o [.NET Core](https://www.microsoft.com/net/) e o código C# para conectar-se a um Banco de Dados SQL do Azure. Em seguida, você executará uma instrução Transact-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para este tutorial, é necessário:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [.NET Core para seu sistema operacional](https://www.microsoft.com/net/core) instalado. 

> [!NOTE]
> Este início rápido usa o banco de dados *mySampleDatabase*. Se você quiser usar um banco de dados diferente, precisará alterar as referências de banco de dados e modificar a consultar `SELECT` no código C#.


## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

#### <a name="get-adonet-connection-information-optional"></a>Obter informações de conexão do ADO.NET (opcional)

1. Navegue até a página **mySampleDatabase** e, em **Configurações**, selecione **Cadeias de conexão**.

2. Examine a cadeia de conexão completa do **ADO.NET**.

    ![Cadeia de conexão do ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. Copie a cadeia de conexão **ADO.NET** se pretender usá-la.
  
## <a name="create-a-new-net-core-project"></a>Criar um novo projeto do .NET Core

1. Abra um prompt de comando e crie uma pasta chamada **sqltest**. Navegue até essa pasta e execute este comando.

    ```cmd
    dotnet new console
    ```
    Esse comando cria os arquivos de projeto do aplicativo, incluindo um arquivo de código C# inicial (**Program.cs**), um arquivo de configuração XML (**sqltest.csproj**) e os binários necessários.

2. Em um editor de texto, abra **sqltest.csproj** e cole o seguinte XML entre as marcas `<Project>`. Esse XML adiciona `System.Data.SqlClient` como uma dependência.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Inserir código para consultar o banco de dados SQL

1. Em um editor de texto, abra **Program.cs**.

2. Substitua o conteúdo pelo código a seguir e adicione os valores apropriados para seu servidor, banco de dados, nome de usuário e senha.

> [!NOTE]
> Para usar uma cadeia de conexão do ADO.NET, substitua as 4 linhas no código de configuração de servidor, banco de dados, nome de usuário e senha pela linha abaixo. Na cadeia de caracteres, defina seu nome de usuário e senha.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
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
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>Executar o código

1. No prompt, execute os comandos a seguir.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Verifique se as primeiras 20 linhas são retornadas.

   ```text
   Query data example:
   =========================================

   Road Frames HL Road Frame - Black, 58
   Road Frames HL Road Frame - Red, 58
   Helmets Sport-100 Helmet, Red
   Helmets Sport-100 Helmet, Black
   Socks Mountain Bike Socks, M
   Socks Mountain Bike Socks, L
   Helmets Sport-100 Helmet, Blue
   Caps AWC Logo Cap
   Jerseys Long-Sleeve Logo Jersey, S
   Jerseys Long-Sleeve Logo Jersey, M
   Jerseys Long-Sleeve Logo Jersey, L
   Jerseys Long-Sleeve Logo Jersey, XL
   Road Frames HL Road Frame - Red, 62
   Road Frames HL Road Frame - Red, 44
   Road Frames HL Road Frame - Red, 48
   Road Frames HL Road Frame - Red, 52
   Road Frames HL Road Frame - Red, 56
   Road Frames LL Road Frame - Black, 58
   Road Frames LL Road Frame - Black, 60
   Road Frames LL Road Frame - Black, 62

   Done. Press enter.
   ```
3. Pressione **Enter** para fechar a janela do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao .NET Core no Windows/Linux/macOS usando a linha de comando](/dotnet/core/tutorials/using-with-xplat-cli).
- Saiba como [conectar-se e consultar um Banco de Dados SQL do Azure usando o .NET Framework e o Visual Studio](sql-database-connect-query-dotnet-visual-studio.md).  
- Saiba como [Projetar seu primeiro Banco de Dados SQL do Azure usando o SSMS](sql-database-design-first-database.md) ou [Projetar um Banco de Dados SQL do Azure e conectar-se com o C# e o ADO.NET](sql-database-design-first-database-csharp.md).
- Para saber mais sobre o .NET, veja a [documentação do .NET](https://docs.microsoft.com/dotnet/).
