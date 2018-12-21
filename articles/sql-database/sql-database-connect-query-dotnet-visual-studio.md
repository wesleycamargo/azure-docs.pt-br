---
title: Usar o Visual Studio com o .NET e o C# para consultar o Banco de Dados SQL do Azure | Microsoft Docs
description: Use o Visual Studio para criar um aplicativo C# que se conecta a um Banco de Dados SQL do Azure e o consulta com instruções Transact-SQL.
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
ms.date: 12/11/2018
ms.openlocfilehash: 04a0abd0fba7ec53aebeb481ac80d36653d118b6
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384929"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Início Rápido: Use .NET e C# com o Visual Studio para se conectar e consultar um Banco de Dados SQL do Azure

Este início rápido mostra como usar o [.NET Framework](https://www.microsoft.com/net/) e o código C# no Visual Studio para consultar um Banco de Dados SQL do Azure com instruções Transact-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- Uma [regra de firewall no nível do servidor](sql-database-get-started-portal-firewall.md) para permitir o endereço IP público do computador que você usa.
  
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) Community, Professional ou Enterprise Edition.

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-the-sql-database"></a>Criar código para consultar o Banco de Dados SQL

1. No Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**. 
   
1. Na caixa de diálogo **Novo Projeto**, selecione **Visual C#** e, em seguida, selecione **Aplicativo de Console (.NET Framework)**.
   
1. Insira *sqltest* para o nome do projeto e selecione **OK**. Quando um novo projeto é criado. 
   
1. Selecione **Projeto** > **Gerenciar Pacotes do NuGet**. 
   
1. Na **Gerenciador de Pacotes do NuGet**, selecione a guia **Procurar** e, em seguida, pesquise e selecione **System.Data.SqlClient**.
   
1. Na página **System.Data.SqlClient**, selecione **Instalar**. 
   - Se solicitado, selecione **OK** para continuar com a instalação. 
   - Se uma janela **Aceitação da Licença** for exibida, selecione **Aceito**.
   
1. Quando a instalação for concluída, você poderá fechar **Gerenciador de Pacotes NuGet**. 
   
1. No editor de códigos, substitua o conteúdo de **Program.cs** pelo código a seguir. Substitua os valores para `<server>`, `<username>`, `<password>` e `<database>`.
   
   >[!IMPORTANT]
   >O código neste exemplo usa os dados de exemplo AdventureWorksLT, que você pode escolher como fonte durante a criação de seu banco de dados. Se o banco de dados tiver dados diferentes, use tabelas de seu próprio banco de dados na consulta SELECT. 
   
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
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
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

1. Para executar o aplicativo, selecione **Depurar** > **Iniciar Depuração** ou **Iniciar** na barra de ferramentas ou pressione **F5**.
1. Verifique se as primeiras 20 linhas de Categoria/Produto do banco de dados são retornadas e, em seguida, feche a janela do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [se conectar e consultar um Banco de Dados SQL do Azure usando o .NET Core](sql-database-connect-query-dotnet-core.md) no Windows/Linux/macOS.  
- Saiba mais sobre a [Introdução ao .NET Core no Windows/Linux/macOS usando a linha de comando](/dotnet/core/tutorials/using-with-xplat-cli).
- Saiba como [Projetar seu primeiro banco de dados SQL do Azure usando o SSMS](sql-database-design-first-database.md) ou [Projetar seu primeiro banco de dados SQL do Azure usando o .NET](sql-database-design-first-database-csharp.md).
- Para saber mais sobre o .NET, veja a [documentação do .NET](https://docs.microsoft.com/dotnet/).
- Exemplo de lógica de repetição: [Conecte-se de modo resiliente ao SQL com ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

