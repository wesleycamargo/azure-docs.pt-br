---
title: Como usar o Java para consultar o banco de dados SQL do Azure| Microsoft Docs
description: Mostra como usar o Java para criar um programa que se conecta a um banco de dados SQL do Azure e consultá-los usando instruções do T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/20/2018
ms.openlocfilehash: 5c12bd54c0ea96ac915fedab94f03cf044330dcf
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52723288"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Início Rápido: Como usar o Java para consultar um banco de dados SQL do Azure

Este artigo demonstra como usar o [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) para se conectar a um Banco de dados SQL do Azure. Depois você pode usar as instruções T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este modelo, é necessário atender aos seguintes pré-requisitos:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Uma [regra de firewall no nível do servidor](sql-database-get-started-portal-firewall.md) para o endereço IP público do computador sendo usado

- O software relacionado ao Java foi instalado no seu sistema operacional:

  - **MacOS**, instale o Homebrew e o Java, depois instale o Maven. Confira as [etapas 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  - **Ubuntu**, instale o Java, o Kit de desenvolvimento Java e depois o Maven. Confira as [etapas 1.2, 1.3 e 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  - **Windows**, instale o Java e depois o Maven. Confira as [etapas 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

## <a name="get-database-connection"></a>Obter a conexão de banco de dados

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-the-project"></a>Criar o projeto

1. A partir do prompt de comando, crie um novo projeto Maven chamado *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Altere o diretório para *sqltest* e abra *pom.xml* com seu editor de texto preferido. Adicione o **Microsoft JDBC Driver para SQL Server** às suas dependências do projeto usando o código a seguir.

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. Também no *pom.xml*, adicione as seguintes propriedades ao seu projeto. Se não tiver uma seção de propriedades, você pode adicioná-lo depois das dependências.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Salve e feche o *pom.xml*.

## <a name="add-code-to-query-database"></a>Adicionar código ao banco de dados de consulta

1. Você já deve ter um arquivo chamado *App.java* no seu projeto Maven localizado em:

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. Abra o arquivo e substitua o conteúdo pelo código a seguir. Depois adicione os valores apropriados para seu servidor, banco de dados, usuário e senha.

    ```java
    package com.sqldbsamples;

    import java.sql.Connection;
    import java.sql.Statement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.DriverManager;

    public class App {

        public static void main(String[] args) {

            // Connect to database
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
            Connection connection = null;

            try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName "
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > O exemplo de código usa o banco de dados de modelo **AdventureWorksLT** para o SQL Azure.

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute o aplicativo.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Verifique se as 20 linhas superiores são retornadas, depois feche a janela do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Projetar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)  

- [Microsoft JDBC Driver para SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Relatar problemas/fazer perguntas](https://github.com/microsoft/mssql-jdbc/issues)  
