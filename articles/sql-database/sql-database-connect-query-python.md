---
title: Usar o Python para consultar banco de dados SQL do Azure | Microsoft Docs
description: "Este tópico mostra como usar Python para criar um programa que se conecta a um banco de dados SQL do Azure e consultá-lo usando instruções Transact-SQL."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: On Demand
ms.tgt_pltfrm: n
ms.devlang: python
ms.topic: quickstart
ms.date: 08/09/2017
ms.author: carlrab
ms.openlocfilehash: bb3396c95d884f0ad90e2d1c07209fecd87ece15
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="use-python-to-query-an-azure-sql-database"></a>Usar Python para consultar um banco de dados SQL do Azure

 Este início rápido demonstra como usar o [Python](https://python.org) para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados. Para obter mais detalhes do sdk, confira a nossa documentação de [referência](https://docs.microsoft.com/python/api/overview/azure/sql), um pyodbc de [exemplo](https://github.com/mkleehammer/pyodbc/wiki/Getting-started) e o repositório GitHub [pyodbc](https://github.com/mkleehammer/pyodbc/wiki/).

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial de início rápido, certifique-se de que você tenha o seguinte:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Uma [regra de firewall no nível do servidor](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) para o endereço IP público do computador que usou para este tutorial de início rápido.

- O Python e o software relacionado para seu sistema operacional instalados:

    - **MacOS**: instale o Homebrew e o Python, instale o driver ODBC e o SQLCMD e, em seguida, instale o Driver Python para SQL Server. Consulte [Etapas 1.2, 1.3 e 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).
    - **Ubuntu**: instale o Python e outros pacotes necessários e, em seguida, instale o Driver Python para SQL Server. Consulte [Etapas 1.2, 1.3 e 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu/).
    - **Windows**: instale a versão mais recente do Python (a variável de ambiente agora está configurada para você), instale o driver ODBC e o SQLCMD e então instale o Driver Python para o SQL Server. Veja a [Etapa 1.2, 1.3 e 2.1](https://www.microsoft.com/sql-server/developer-get-started/python/windows/). 

## <a name="sql-server-connection-information"></a>Informações de conexão do servidor SQL

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>Inserir código para consultar o banco de dados SQL 

1. Em seu editor de texto favorito, crie um novo arquivo, **sqltest.py**.  

2. Substitua o conteúdo pelo código a seguir e adicione os valores apropriados para seu servidor, banco de dados, usuário e senha.

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
    print (str(row[0]) + " " + str(row[1]))
    row = cursor.fetchone()
```

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute estes comandos:

   ```Python
   python sqltest.py
   ```

2. Verifique se as 20 linhas superiores são retornadas e, em seguida, feche a janela do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Projetar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)
- [Drivers Python da Microsoft para SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centro de desenvolvedores do Python](https://azure.microsoft.com/develop/python/?v=17.23h)

