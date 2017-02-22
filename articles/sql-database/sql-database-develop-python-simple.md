---
title: Conectar-se ao Banco de Dados SQL usando Python | Microsoft Docs
description: "Apresenta um exemplo de código Python que pode ser usado para se conectar ao Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/03/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 2793ddb1c903f6732a193276a2d804192b7ab53b
ms.openlocfilehash: 86524dd1a73df3b60245cb664c0e17a63df00763


---
# <a name="connect-to-sql-database-by-using-python"></a>Conectar-se ao Banco de Dados SQL usando Python
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tópico mostra como conectar e consultar um Banco de Dados SQL do Azure usando o Python. Você pode executar essa amostra de plataformas Windows, Ubuntu Linux ou Mac.

## <a name="step-1-create-a-sql-database"></a>Etapa 1: Criar um banco de dados SQL
Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo.  É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**. Depois de criar seu banco de dados, certifique-se de habilitar o acesso ao seu endereço IP habilitando as regras do firewall, conforme descrito na [página de introdução](sql-database-get-started.md)

## <a name="step-2-configure-development-environment"></a>Etapa 2: configurar o Ambiente de Desenvolvimento
### <a name="mac-os"></a>**Mac OS**
Abra seu terminal e navegue até um diretório no qual você planeja criar o script python. Insira os comandos a seguir para instalar **brew**, **Driver ODBC da Microsoft para Mac** e **pyodbc**. pyodbc usa o Microsoft ODBC Driver no Linux para se conectar a Bancos de Dados SQL.

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra seu terminal e navegue até um diretório no qual você planeja criar o script python. Insira os comandos a seguir para instalar o **Microsoft ODBC Driver for Linux** e **pyodbc**. pyodbc usa o Microsoft ODBC Driver no Linux para se conectar a Bancos de Dados SQL.

```
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev-utf16
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
Instale o [Microsoft ODBC Driver 13.1](https://www.microsoft.com/en-us/download/details.aspx?id=53339). pyodbc usa o Microsoft ODBC Driver no Linux para se conectar a Bancos de Dados SQL. 

Em seguida, instale o pyodbc usando pip

```
pip install pyodbc==3.1.1
```

É possível encontrar instruções para habilitar o uso de pip [aqui](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)

## <a name="step-3-run-sample-code"></a>Etapa 3: Executar o código de exemplo
Crie um arquivo chamado **sql_sample.py** e cole o código a seguir dentro dele. Você pode executar isso a partir da linha de comando usando:

```
python sql_sample.py
```

### <a name="connect-to-your-sql-database"></a>Conectar-se ao seu Banco de Dados SQL
A função [pyodbc.connect](https://mkleehammer.github.io/pyodbc/api-connection.html) é usada para se conectar ao Banco de Dados SQL.

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
if row:
    print row
```

### <a name="execute-an-sql-select-statement"></a>Executar uma instrução SQL SELECT
A função [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) pode ser usada para recuperar um conjunto de resultados de uma consulta no Banco de Dados SQL. Essencialmente, essa função aceita qualquer consulta e retorna um conjunto de resultados que pode ser iterado com o uso de [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html).

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1]) + " " + str(row[2])
    row = cursor.fetchone()
```

### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Inserir uma linha, passar parâmetros e recuperar a chave primária gerada
No Banco de Dados SQL, a propriedade [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) e o objeto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) podem ser usados para gerar automaticamente valores de [chave primária](https://msdn.microsoft.com/library/ms179610.aspx). 

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("select @@VERSION")
row = cursor.fetchone()
while row:
    print "Inserted Product ID : " +str(row[0])
    row = cursor.fetchone()
```

### <a name="transactions"></a>Transações
Este exemplo de código demonstra o uso de transações nas quais você:

* Inicia uma transação
* Insere uma linha de dados
* Reverte a transação para desfazer a inserção 

Cole o código a seguir no sql_sample.py.

```
import pyodbc
server = 'yourserver.database.windows.net'
database = 'yourdatabase'
username = 'yourusername'
password = 'yourpassword'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("BEGIN TRANSACTION")
cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
cnxn.rollback()
```

## <a name="next-steps"></a>Próximas etapas
* Leia a [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md)
* Mais informações sobre o [Microsoft Python Driver para SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
* Visite o [Centro de Desenvolvimento do Python](/develop/python/).

## <a name="additional-resources"></a>Recursos adicionais
* [Padrões de design para aplicativos SaaS multilocatários com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore todos os [recursos do Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/)



<!--HONumber=Feb17_HO2-->


