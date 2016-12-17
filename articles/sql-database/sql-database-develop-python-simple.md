---
title: Conectar-se ao Banco de Dados SQL usando Python | Microsoft Docs
description: "Apresenta um exemplo de código Phyton que pode ser usado para se conectar ao Banco de Dados SQL do Azure."
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
ms.date: 10/05/2016
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5f3a4e49646063b41af5a9941f27291762f5336e


---
# <a name="connect-to-sql-database-by-using-python"></a>Conectar-se ao Banco de Dados SQL usando Phyton
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tópico mostra como conectar e consultar um Banco de Dados SQL do Azure usando o Python. Você pode executar essa amostra de plataformas Windows, Ubuntu Linux ou Mac.

## <a name="step-1-create-a-sql-database"></a>Etapa 1: Criar um banco de dados SQL
Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo.  É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**. Depois de criar seu banco de dados, certifique-se de habilitar o acesso ao seu endereço IP habilitando as regras do firewall, conforme descrito na [página de introdução](sql-database-get-started.md)

## <a name="step-2-configure-development-environment"></a>Etapa 2: configurar o Ambiente de Desenvolvimento
### <a name="mac-os"></a>**Mac OS**
### <a name="install-the-required-modules"></a>Instalar os módulos necessários
Abra o seu terminal e instale

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql==2.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra seu terminal e navegue até um diretório no qual você planeja criar o script python. Digite os seguintes comandos para instalar **FreeTDS** e **pymssql**. pymssql usa o FreeTDS para se conectar a bancos de dados SQL.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql==2.1.1

### <a name="windows"></a>**Windows**
Instale o pymssql [**aqui**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql). 

Certifique-se de que escolheu o arquivo whl correto. Por exemplo: se você estiver usando o Python 2.7 em um computador de 64 bits, escolha: pymssql‑2.1.1‑cp27‑none‑win_amd64.whl. Depois de baixar o arquivo .whl, coloque-o na pasta C:/Python27.

Agora, instale o driver pymssql usando pip na linha de comando. cd em C:/Python27 e execute o que segue

    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

É possível encontrar instruções para habilitar o uso de pip [aqui](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)

## <a name="step-3-run-sample-code"></a>Etapa 3: Executar o código de exemplo
Crie um arquivo chamado **sql_sample.py** e cole o código a seguir dentro dele. Você pode executar isso a partir da linha de comando usando:

    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>Conectar-se ao seu Banco de Dados SQL
A função [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) é usada para se conectar ao Banco de Dados SQL.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>Executar uma instrução SQL SELECT
A função [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) pode ser usada para recuperar um conjunto de resultados de uma consulta no Banco de Dados SQL. Essencialmente, essa função aceita qualquer consulta e retorna um conjunto de resultados que pode ser iterado com o uso de [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])     
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Inserir uma linha, passar parâmetros e recuperar a chave primária gerada
No Banco de Dados SQL, a propriedade [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) e o objeto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) podem ser usados para gerar automaticamente valores de [chave primária](https://msdn.microsoft.com/library/ms179610.aspx). 

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>Transações
Este exemplo de código demonstra o uso de transações nas quais você:

* Inicia uma transação
* Insere uma linha de dados
* Reverte a transação para desfazer a inserção 

Cole o código a seguir no sql_sample.py.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>Próximas etapas
* Leia a [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md)
* Mais informações sobre o [Microsoft Python Driver para SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* Visite o [Centro de Desenvolvimento do Python](/develop/python/).

## <a name="additional-resources"></a>Recursos adicionais
* [Padrões de design para aplicativos SaaS multilocatários com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore todos os [recursos do Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/)




<!--HONumber=Nov16_HO3-->


