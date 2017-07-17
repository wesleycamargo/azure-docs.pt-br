---
title: Conectar-se ao Banco de Dados do Azure para PostgreSQL usando a linguagem Go | Microsoft Docs
description: "Este guia de início rápido fornece um exemplo em linguagem de programação Go que você pode usar para se conectar e consultar dados do Banco de Dados do Azure para PostgreSQL."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.devlang: go
ms.topic: hero-article
ms.date: 06/29/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: f3e7e57993b5253a895640854672da431c00854b
ms.contentlocale: pt-br
ms.lasthandoff: 07/04/2017

---

<a id="azure-database-for-postgresql-use-go-language-to-connect-and-query-data" class="xliff"></a>

# Banco de dados do Azure para PostgreSQL: usar a linguagem Go para se conectar e consultar dados
Este guia de início rápido demonstra como se conectar a um banco de dados do Azure para PostgreSQL usando código escrito na linguagem [Go](https://golang.org/). Ele mostra como usar instruções SQL para consultar, inserir, atualizar e excluir dados no banco de dados. Este artigo pressupõem que você está familiarizado com o desenvolvimento usando Go, mas que começou recentemente a trabalhar com o Banco de Dados do Azure para PostgreSQL.

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos
Este guia de início rápido usa os recursos criados em um destes guias como ponto de partida:
- [Criar Banco de dados - Portal](quickstart-create-server-database-portal.md)
- [Criar Banco de dados - CLI do Azure](quickstart-create-server-database-azure-cli.md)

<a id="install-go-and-pq" class="xliff"></a>

## Instalar Go e pq
- Baixe e instale o Go de acordo com as [instruções de instalação](https://golang.org/doc/install) correspondentes à plataforma.
- Crie uma pasta para o seu projeto, como C:\Postgresql\. Usando a linha de comando, altere o diretório na pasta do projeto, como `cd C:\Postgres\`.
- Baixe o [Driver Pure Go Postgres (pq)](https://github.com/lib/pq) em sua pasta de projeto, digitando o comando `go get github.com/lib/pq` enquanto está no mesmo diretório.

<a id="build-and-run-go-code" class="xliff"></a>

## Compilar e executar o código Go 
- Salve o código para um arquivo de texto com extensão *.go e salve-o na pasta do projeto, como `C:\postgres\read.go`.
- Para executar o código, altere o diretório para a pasta de projeto `cd postgres`, digite o comando `go run read.go` para compilar o aplicativo e executá-lo.
- Para compilar o código em um aplicativo nativo, `go build read.go`, inicie read.exe para executar o aplicativo.

<a id="get-connection-information" class="xliff"></a>

## Obter informações de conexão
Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados do Azure para PostgreSQL. Você precisa das credenciais de logon e do nome do servidor totalmente qualificado.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e pesquise pelo servidor que você criou, como **mypgserver-20170401**.
3. Clique no nome do servidor **mypgserver-20170401**.
4. Selecione a página **Visão geral** do servidor. Anote o **Nome do servidor** e o **Nome de logon de administrador do servidor**.
 ![Banco de Dados do Azure para PostgreSQL – Logon de administrador do servidor](./media/connect-go/1-connection-string.png)
5. Se você se esquecer das informações de logon do servidor, navegue até a página **Visão Geral** para exibir o nome de logon do Administrador do servidor. Se necessário, redefina a senha.

<a id="connect-and-create-a-table" class="xliff"></a>

## Conectar-se e criar uma tabela
Use o código a seguir para se conectar e criar uma tabela usando a instrução SQL **CREATE TABLE**, seguida por instruções SQL **INSERT INTO** para adicionar linhas à tabela.

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [pacote pq](http://godoc.org/github.com/lib/pq) como driver para se comunicar com o servidor Postgres e o [fmt pacote](https://golang.org/pkg/fmt/) para impressão de entrada e saída na linha de comando.

O código chama o método [sql. Open ()](http://godoc.org/github.com/lib/pq#Open) para se conectar ao Banco de Dados do Azure para PostgreSQL e verifica a conexão usando o método [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Um [identificador de banco de dados](https://golang.org/pkg/database/sql/#DB) é usado em todo o processo, mantendo o pool de conexão para o servidor de banco de dados. O código chama o método [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) várias vezes para executar vários comandos SQL. A cada ocorrência, um método personalizado checkError() para verificar se ocorreu um erro e pane para sair em caso de erro.


Substitua os parâmetros `HOST`, `DATABASE`, `USER`, e `PASSWORD` pelos seus próprios valores. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Drop previous table of same name if one exists.
    _, err = db.Exec("DROP TABLE IF EXISTS inventory;")
    checkError(err)
    fmt.Println("Finished dropping table (if existed)")

    // Create table.
    _, err = db.Exec("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
    checkError(err)
    fmt.Println("Finished creating table")

    // Insert some data into table.
    sql_statement := "INSERT INTO inventory (name, quantity) VALUES ($1, $2);"
    _, err = db.Exec(sql_statement, "banana", 150)
    checkError(err)
    _, err = db.Exec(sql_statement, "orange", 154)
    checkError(err)
    _, err = db.Exec(sql_statement, "apple", 100)
    checkError(err)
    fmt.Println("Inserted 3 rows of data")
}
```

<a id="read-data" class="xliff"></a>

## Ler dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **SELECT**. 

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [pacote pq](http://godoc.org/github.com/lib/pq) como driver para se comunicar com o servidor Postgres e o [fmt pacote](https://golang.org/pkg/fmt/) para impressão de entrada e saída na linha de comando.

O código chama o método [sql. Open ()](http://godoc.org/github.com/lib/pq#Open) para se conectar ao Banco de Dados do Azure para PostgreSQL e verifica a conexão usando o método [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Um [identificador de banco de dados](https://golang.org/pkg/database/sql/#DB) é usado em todo o processo, mantendo o pool de conexão para o servidor de banco de dados. A consulta select é executada chamando o método [db.Query()](https://golang.org/pkg/database/sql/#DB.Query), e as linhas resultantes são mantidas em uma variável do tipo [linhas](https://golang.org/pkg/database/sql/#Rows). O código lê a coluna de valores de dados na linha atual usando o método [rows.Scan()](https://golang.org/pkg/database/sql/#Rows.Scan) e loops em relação às linhas usando o iterador [rows.Next ()](https://golang.org/pkg/database/sql/#Rows.Next) até que não haja mais linhas. Os valores de coluna de cada linha são impressos no console de saída. A cada ocorrência, um método personalizado checkError() para verificar se ocorreu um erro e pane para sair em caso de erro.

Substitua os parâmetros `HOST`, `DATABASE`, `USER`, e `PASSWORD` pelos seus próprios valores. 

```go
package main

import (
    "database/sql"
    "fmt"
    _ "github.com/lib/pq"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {

    // Initialize connection string.
    var connectionString string = fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Read rows from table.
    var id int
    var name string
    var quantity int

    sql_statement := "SELECT * from inventory;"
    rows, err := db.Query(sql_statement)
    checkError(err)

    for rows.Next() {
        switch err := rows.Scan(&id, &name, &quantity); err {
        case sql.ErrNoRows:
            fmt.Println("No rows were returned")
        case nil:
            fmt.Printf("Data row = (%d, %s, %d)\n", id, name, quantity)
        default:
            checkError(err)
        }
    }
}
```

<a id="update-data" class="xliff"></a>

## Atualizar dados
Use o código a seguir para conectar-se e atualizar os dados usando uma instrução SQL **UPDATE**.

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [pacote pq](http://godoc.org/github.com/lib/pq) como driver para se comunicar com o servidor Postgres e o [fmt pacote](https://golang.org/pkg/fmt/) para impressão de entrada e saída na linha de comando.

O código chama o método [sql. Open ()](http://godoc.org/github.com/lib/pq#Open) para se conectar ao Banco de Dados do Azure para PostgreSQL e verifica a conexão usando o método [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Um [identificador de banco de dados](https://golang.org/pkg/database/sql/#DB) é usado em todo o processo, mantendo o pool de conexão para o servidor de banco de dados. O código chama o método [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) para executar a instrução SQL que atualiza a tabela. Um método personalizado checkError() para verificar se ocorreu um erro e pane para sair em caso de erro.

Substitua os parâmetros `HOST`, `DATABASE`, `USER`, e `PASSWORD` pelos seus próprios valores. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Modify some data in table.
    sql_statement := "UPDATE inventory SET quantity = $2 WHERE name = $1;"
    _, err = db.Exec(sql_statement, "banana", 200)
    checkError(err)
    fmt.Println("Updated 1 row of data")
}
```

<a id="delete-data" class="xliff"></a>

## Excluir dados
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **DELETE**. 

O código importa três pacotes: o [pacote sql](https://golang.org/pkg/database/sql/), o [pacote pq](http://godoc.org/github.com/lib/pq) como driver para se comunicar com o servidor Postgres e o [fmt pacote](https://golang.org/pkg/fmt/) para impressão de entrada e saída na linha de comando.

O código chama o método [sql. Open ()](http://godoc.org/github.com/lib/pq#Open) para se conectar ao Banco de Dados do Azure para PostgreSQL e verifica a conexão usando o método [db. Ping()](https://golang.org/pkg/database/sql/#DB.Ping). Um [identificador de banco de dados](https://golang.org/pkg/database/sql/#DB) é usado em todo o processo, mantendo o pool de conexão para o servidor de banco de dados. O código chama o método [Exec()](https://golang.org/pkg/database/sql/#DB.Exec) para executar a instrução SQL que atualiza a tabela. Um método personalizado checkError() para verificar se ocorreu um erro e pane para sair em caso de erro.

Substitua os parâmetros `HOST`, `DATABASE`, `USER`, e `PASSWORD` pelos seus próprios valores. 
```go
package main

import (
  "database/sql"
  _ "github.com/lib/pq"
  "fmt"
)

const (
    // Initialize connection constants.
    HOST     = "mypgserver-20170401.postgres.database.azure.com"
    DATABASE = "mypgsqldb"
    USER     = "mylogin@mypgserver-20170401"
    PASSWORD = "<server_admin_password>"
)

func checkError(err error) {
    if err != nil {
        panic(err)
    }
}

func main() {
    
    // Initialize connection string.
    var connectionString string = 
        fmt.Sprintf("host=%s user=%s password=%s dbname=%s sslmode=require", HOST, USER, PASSWORD, DATABASE)

    // Initialize connection object.
    db, err := sql.Open("postgres", connectionString)
    checkError(err)

    err = db.Ping()
    checkError(err)
    fmt.Println("Successfully created connection to database")

    // Delete some data from table.
    sql_statement := "DELETE FROM inventory WHERE name = $1;"
    _, err = db.Exec(sql_statement, "orange")
    checkError(err)
    fmt.Println("Deleted 1 row of data")
}
```

<a id="next-steps" class="xliff"></a>

## Próximas etapas
> [!div class="nextstepaction"]
> [Migre seu banco de dados usando Exportar e Importar](./howto-migrate-using-export-and-import.md)

