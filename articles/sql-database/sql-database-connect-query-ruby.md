---
title: Como usar o Ruby para consultar o Banco de Dados SQL do Azure| Microsoft Docs
description: Este tópico mostra como usar o Ruby para criar um programa que se conecta a um Banco de Dados SQL do Azure e consultá-los usando instruções do Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/20/2018
ms.openlocfilehash: 8fa161f82cf73e61127ab67aba6ca3408c8e7c10
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197816"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Início Rápido: Como usar o Ruby para consultar um banco de dados SQL do Azure

Este início rápido demonstra como usar o [Ruby](https://www.ruby-lang.org) para se conectar a um Banco de Dados SQL do Azure e consultar dados com instruções Transact-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisará dos seguintes pré-requisitos:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- O Ruby e o software relacionado para seu sistema operacional:
  
  - **MacOS**: Instale o Homebrew, o rbenv e o ruby-build, o Ruby, o FreeTDS e o TinyTDS. Confira as Etapas 1.2, 1.3, 1.4, 1.5 e 2.1 em [Criar aplicativos Ruby usando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Instale os pré-requisitos do Ruby, do rbenv e do ruby-build, do Ruby, do FreeTDS e do TinyTDS. Confira as Etapas 1.2, 1.3, 1.4, 1.5 e 2.1 em [Criar aplicativos Ruby usando o SQL Server no Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: Instale o Ruby, o Ruby Devkit e o TinyTDS. Confira [Configurar um ambiente de desenvolvimento para desenvolvimento com o Ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-your-sql-database"></a>Criar código para consultar seu Banco de Dados SQL

1. Em um editor de texto ou de códigos, crie um arquivo chamado *sqltest.rb*.
   
1. Adicione os códigos a seguir. Substitua os valores do Banco de Dados SQL do Azure de `<server>`, `<database>`, `<username>` e `<password>`.
   
   >[!IMPORTANT]
   >O código neste exemplo usa os dados de exemplo AdventureWorksLT, que você pode escolher como fonte durante a criação de seu banco de dados. Se o banco de dados tiver dados diferentes, use tabelas de seu próprio banco de dados na consulta SELECT. 
   
   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute o comando a seguir:

   ```bash
   ruby sqltest.rb
   ```
   
1. Verifique se as 20 primeiras linhas de Categoria/Produto do banco de dados são retornadas. 

## <a name="next-steps"></a>Próximas etapas
- [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-design-first-database.md).
- [Repositório GitHub do TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Relatar problemas ou fazer perguntas sobre o TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Ruby Driver para SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
