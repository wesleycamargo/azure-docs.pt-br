---
title: Usar o Python para consultar banco de dados SQL do Azure | Microsoft Docs
description: Este tópico mostra como usar o Python para criar um programa que se conecta a um Banco de Dados SQL do Azure e consultá-lo usando instruções Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: b9c33da4f002504a55802e4253d648ff87847d92
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271820"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Início Rápido: Usar Python para consultar um banco de dados SQL do Azure

 Este início rápido demonstra como usar o [Python](https://python.org) para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados. Para obter mais detalhes do SDK, confira a nossa documentação de [referência](https://docs.microsoft.com/python/api/overview/azure/sql), o [repositório GitHub pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) e um [pyodbc de exemplo](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, tenha o seguinte:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- Uma [regra de firewall no nível do servidor](sql-database-get-started-portal-firewall.md) para o endereço IP público do computador que usou para este início rápido.
  
- Python e software relacionado para seu sistema operacional:
  
  - **MacOS**: instale o Homebrew e o Python, instale o driver ODBC e o SQLCMD e, em seguida, instale o driver Python para SQL Server. Confira as etapas 1.2, 1.3 e 2.1 em [Criar aplicativos Python usando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Para obter mais informações, confira [Instalar o Microsoft ODBC Driver no Linux e macOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).
    
  - **Ubuntu**: instale o Python e outros pacotes necessários com `sudo apt-get install python python-pip gcc g++ build-essential`. Baixe e instale o driver ODBC, o SQLCMD e o driver Python para o SQL Server. Para obter instruções, confira [Configurar um ambiente de desenvolvimento para o desenvolvimento Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).
    
  - **Windows**: instale o Python, o driver ODBC e SQLCMD e o driver Python para o SQL Server. Para obter instruções, confira [Configurar um ambiente de desenvolvimento para o desenvolvimento Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="create-code-to-query-your-sql-database"></a>Criar código para consultar seu Banco de Dados SQL 

1. Em um editor de texto, crie um novo arquivo chamado *sqltest.py*.  
   
1. Adicione os códigos a seguir. Substitua seus próprios valores para \<server>, \<database>, \<username> e \<password>.
   
   >[!IMPORTANT]
   >O código neste exemplo usa os dados de exemplo AdventureWorksLT, que você pode escolher como fonte durante a criação de seu banco de dados. Se o banco de dados tiver dados diferentes, use tabelas de seu próprio banco de dados na consulta SELECT. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute o comando a seguir:

   ```cmd
   python sqltest.py
   ```

1. Verifique se as primeiras 20 linhas de Categoria/Produto são retornadas e feche a janela Comando.

## <a name="next-steps"></a>Próximas etapas

- [Projetar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)
- [Drivers Python da Microsoft para SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Central de desenvolvedores do Python](https://azure.microsoft.com/develop/python/?v=17.23h)

