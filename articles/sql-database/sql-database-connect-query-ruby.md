---
title: Como usar o Ruby para consultar o Banco de Dados SQL do Azure| Microsoft Docs
description: Este tópico mostra como usar o Ruby para criar um programa que se conecta a um Banco de Dados SQL do Azure e consultá-los usando instruções do Transact-SQL.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop apps
ms.devlang: ruby
ms.topic: quickstart
ms.date: 07/15/2017
ms.author: carlrab
ms.openlocfilehash: 19ff40c5e4a42195dcfe0b68c8d0b941e53f25a2
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="use-ruby-to-query-an-azure-sql-database"></a>Como usar o Ruby para consultar um banco de dados SQL do Azure

Este tutorial de início rápido demonstra como usar o [Ruby](https://www.ruby-lang.org) para criar um programa que permite se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial de início rápido, você precisa ter o seguinte:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Uma [regra de firewall no nível do servidor](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) para o endereço IP público do computador que usou para este tutorial de início rápido.

- O Ruby e o software relacionado para seu sistema operacional instalados:
    - **MacOS**: instale o Homebrew, instale o rbenv e ruby-build, instale o Ruby e, em seguida, instale o FreeTDS. Confira a [Etapa 1.2, 1.3, 1.4 e 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
    - **Ubuntu**: instale os pré-requisitos do Ruby, instale o rbenv e ruby-build, instale o Ruby e, em seguida, instale o FreeTDS. Confira a [Etapa 1.2, 1.3, 1.4 e 1.5](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).

## <a name="sql-server-connection-information"></a>Informações de conexão do servidor SQL

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> Você deve ter uma regra de firewall em vigor para o endereço IP público do computador em que você executa este tutorial. Se você estiver em um computador diferente ou se tiver um endereço IP público diferente, crie uma [regra de firewall no nível de servidor usando o portal do Azure](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 

## <a name="insert-code-to-query-sql-database"></a>Inserir código para consultar o banco de dados SQL

1. Em seu editor de texto favorito, crie um novo arquivo, **sqltest.rb**

2. Substitua o conteúdo pelo código a seguir e adicione os valores apropriados para seu servidor, banco de dados, usuário e senha.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
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

1. No prompt de comando, execute estes comandos:

   ```bash
   ruby sqltest.rb
   ```

2. Verifique se as 20 linhas superiores são retornadas e, em seguida, feche a janela do aplicativo.


## <a name="next-steps"></a>Próximas etapas
- [Projetar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)
- [Repositório GitHub do TinyTDS](https://github.com/rails-sqlserver/tiny_tds)
- [Relatar problemas ou fazer perguntas sobre o TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Drivers Ruby para SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/)
