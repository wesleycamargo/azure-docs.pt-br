---
title: Usar o .NET Core para consultar o Banco de Dados SQL do Azure | Microsoft Docs
description: Este tópico mostra como usar o .NET Core para criar um programa que se conecta a um banco de dados SQL do Azure e consultá-lo usando instruções Transact-SQL.
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
ms.date: 04/01/2018
ms.openlocfilehash: 0b7c04f21b75125324e33b9152e2b1be142c5043
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47062867"
---
# <a name="use-net-core-c-to-query-an-azure-sql-database"></a>Usar o .NET Core (C#) para consultar um banco de dados SQL do Azure

Este início rápido demonstra como usar o [.NET Core](https://www.microsoft.com/net/) no Windows/Linux/macOS a fim de criar um programa C# para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, tenha o seguinte:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Uma [regra de firewall no nível do servidor](sql-database-get-started-portal-firewall.md) para o endereço IP público do computador que usou para este início rápido.

- Você instalou [.NET Core para o sistema operacional](https://www.microsoft.com/net/core). 

## <a name="sql-server-connection-information"></a>Informações de conexão do servidor SQL

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

#### <a name="for-adonet"></a>Para ADO.NET

1. Para continuar, clique em **Mostrar cadeias de conexão do banco de dados**.

2. Examine a cadeia de conexão completa do **ADO.NET**.

    ![Cadeia de conexão do ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> Você deve ter uma regra de firewall em vigor para o endereço IP público do computador em que você executa este tutorial. Se você estiver em um computador diferente ou se tiver um endereço IP público diferente, crie uma [regra de firewall no nível de servidor usando o portal do Azure](sql-database-get-started-portal-firewall.md). 
>
  
## <a name="create-a-new-net-project"></a>Criar um novo projeto .NET

1. Abra um prompt de comando e crie uma pasta chamada *sqltest*. Navegue até a pasta que você criou e execute o seguinte comando:

    ```
    dotnet new console
    ```

2. Abra ***sqltest.csproj*** com seu editor de texto favorito e adicione System.Data.SqlClient como uma dependência usando o seguinte código:

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.4.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Inserir código para consultar o banco de dados SQL

1. Em seu ambiente de desenvolvimento ou editor de texto favorito, abra **Program.cs**

2. Substitua o conteúdo pelo código a seguir e adicione os valores apropriados para seu servidor, banco de dados, usuário e senha.

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
            Console.ReadLine();
        }
    }
}
```

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute estes comandos:

   ```csharp
   dotnet restore
   dotnet run
   ```

2. Verifique se as 20 linhas superiores são retornadas e, em seguida, feche a janela do aplicativo.


## <a name="next-steps"></a>Próximas etapas

- [Introdução ao .NET Core no Windows/Linux/macOS usando a linha de comando](/dotnet/core/tutorials/using-with-xplat-cli).
- Saiba como [conectar-se e consultar um banco de dados SQL do Azure usando o .NET framework e o Visual Studio](sql-database-connect-query-dotnet-visual-studio.md).  
- Saiba como [Projetar seu primeiro banco de dados SQL do Azure usando o SSMS](sql-database-design-first-database.md) ou [Projetar seu primeiro banco de dados SQL do Azure usando o .NET](sql-database-design-first-database-csharp.md).
- Para saber mais sobre o .NET, veja a [documentação do .NET](https://docs.microsoft.com/dotnet/).
