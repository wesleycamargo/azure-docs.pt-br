---
title: Conectar-se ao Banco de Dados SQL do Azure usando o Python | Microsoft Docs
description: "Apresenta um exemplo de código do Python que pode ser usado para conectar e consultar o Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/17/2017
ms.author: meetb;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: e75058c8b387bc090bf924b9099a64e5d154afa4
ms.lasthandoff: 04/18/2017


---
# <a name="azure-sql-database-use-python-to-connect-and-query-data"></a>Banco de Dados SQL do Azure: Usar o Python para conectar e consultar dados

 Este guia de início rápido demonstra como usar o [Python](https://python.org) para se conectar a um banco de dados SQL do Azure e usar as instruções do Transact-SQL para consultar, inserir, atualizar e excluir dados no banco de dados em plataformas Mac OS, Linux Ubuntu e Windows.

Este início rápido usa como ponto de partida os recursos criados em um destes inícios rápidos:

- [Criar Banco de dados - Portal](sql-database-get-started-portal.md)
- [Criar Banco de dados - CLI](sql-database-get-started-cli.md)

## <a name="install-the-python-and-database-communication-libraries"></a>Instalar o Python e as bibliotecas de comunicação do banco de dados

### <a name="mac-os"></a>**Mac OS**
Abra seu terminal e navegue até um diretório no qual você planeja criar o script python. Insira os comandos a seguir para instalar **brew**, **Driver ODBC da Microsoft para Mac** e **pyodbc**. pyodbc usa o Microsoft ODBC Driver no Linux para se conectar a Bancos de Dados SQL.

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra seu terminal e navegue até um diretório no qual você planeja criar o script python. Insira os comandos a seguir para instalar o **Microsoft ODBC Driver for Linux** e **pyodbc**. pyodbc usa o Microsoft ODBC Driver no Linux para se conectar a Bancos de Dados SQL.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
Instale o [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339) (atualize o driver, se solicitado). O pyodbc usa o Microsoft ODBC Driver no Linux para se conectar aos Bancos de Dados SQL. 

Em seguida, instale o **pyodbc** usando pip.

```cmd
pip install pyodbc==3.1.1
```

É possível encontrar instruções para habilitar o uso de pip [aqui](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)

## <a name="get-connection-information"></a>Obter informações de conexão

Use as informações nas etapas a seguir, se necessário, para obter as informações de conexão para o banco de dados e o servidor do Banco de Dados SQL do Azure. Você precisará dessas informações para se conectar e consultar seu banco de dados SQL do Azure usando o Python. 

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de Dados SQL**. 
3. Na página **Visão geral** do banco de dados, analise o nome totalmente qualificado do servidor, como mostrado na imagem abaixo. Passe o mouse sobre o nome do servidor para abrir a opção **Clique para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se você esqueceu as informações de logon para o servidor do Banco de Dados SQL do Azure, navegue até a página do servidor do Banco de Dados SQL para exibir o nome de administrador do servidor e, se necessário, redefinir a senha.     
   
## <a name="select-data"></a>Selecionar dados

Use o seguinte código para consultar o banco de dados SQL do Azure usando a função [pyodbc.connect]((https://github.com/mkleehammer/pyodbc/wiki)) com uma instrução [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) do Transact-SQL. A função [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) é usada para recuperar um conjunto de resultados de uma consulta no Banco de Dados SQL. Essa função aceita uma consulta e retorna um conjunto de resultados que pode ser iterado com o uso de [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html). Substitua os parâmetros de servidor, banco de dados, nome de usuário e senha pelos valores que você especificou ao criar o banco de dados com os dados de exemplo do AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```

## <a name="insert-data"></a>Inserir dados
Use o seguinte código para inserir um novo produto na tabela SalesLT.Product no banco de dados especificado, usando a função [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) e a instrução [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) do Transact-SQL. Substitua os parâmetros de servidor, banco de dados, nome de usuário e senha pelos valores que você especificou ao criar o banco de dados com os dados de exemplo do AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## <a name="update-data"></a>Atualizar dados
Use o seguinte código para atualizar dados no banco de dados SQL do Azure, usando a função [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) com uma instrução [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) do Transact-SQL. Substitua os parâmetros de servidor, banco de dados, nome de usuário e senha pelos valores que você especificou ao criar o banco de dados com os dados de exemplo do AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```

## <a name="delete-data"></a>Excluir dados
Use o seguinte código para excluir dados no banco de dados SQL do Azure, usando a função [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) com uma instrução [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) do Transact-SQL. Substitua os parâmetros de servidor, banco de dados, nome de usuário e senha pelos valores que você especificou ao criar o banco de dados com os dados de exemplo do AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## <a name="next-steps"></a>Próximas etapas

- Mais informações sobre o [Microsoft Python Driver para SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/).
- Visite o [Centro de Desenvolvimento do Python](/develop/python/).
- Para conectar e consultar usando SQL Server Management Studio, veja [Conectar e consultar com o SSMS](sql-database-connect-query-ssms.md)
- Para conectar e consultar usando o Visual Studio, veja [Conectar e consultar com o Visual Studio Code](sql-database-connect-query-vscode.md).
- Para conectar e consultar usando o .NET, veja [Conectar e consultar com o .NET](sql-database-connect-query-dotnet.md).
- Para conectar e consultar usando o PHP, veja [Conectar e consultar com o PHP](sql-database-connect-query-php.md).
- Para conectar e consultar usando o Node.js, veja [Conectar e consultar com o Node.js](sql-database-connect-query-nodejs.md).
- Para conectar e consultar usando o Java, veja [Conectar e consultar com o Java](sql-database-connect-query-java.md).
- Para conectar e consultar usando o Ruby, veja [Conectar e consultar com o Ruby](sql-database-connect-query-ruby.md).

