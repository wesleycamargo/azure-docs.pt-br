---
title: 'Tutorial: Consulta Elástica com o SQL Data Warehouse do Azure | Microsoft Docs'
description: Este tutorial usa o recurso de Consulta Elástica para consulta SQL Data Warehouse do Azure a partir do Banco de Dados SQL do Azure.
services: sql-data-warehouse
author: hirokib
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/14/2018
ms.author: elbutter
ms.reviewer: igorstan
ms.openlocfilehash: 355ae1c27d0af8f77c2c9bda61c3581562050fc4
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307085"
---
# <a name="tutorial-use-elastic-query-to-access-data-in-azure-sql-data-warehouse-from-azure-sql-database"></a>Tutorial: Usar a Consulta elástica para acessar dados no SQL Data Warehouse do Azure a partir do Banco de Dados SQL do Microsoft Azure

Este tutorial usa o recurso de Consulta Elástica para consulta SQL Data Warehouse do Azure a partir do Banco de Dados SQL do Azure. 

## <a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial

Antes de iniciar este tutorial, você deve ter os seguintes pré-requisitos:

1. SSMS (SQL Server Management Studio) instalado.
2. Um servidor do SQL do Azure criado com um banco de dados e data warehouse dentro do servidor.
3. Regras de firewall configuradas para acessar o SQL Server do Azure.

## <a name="set-up-connection-between-sql-data-warehouse-and-sql-database-instances"></a>Conexão configurada entre as instâncias do SQL Data Warehouse e do Banco de Dados SQL 

1. Usando o SSMS ou outro cliente de consulta, abra uma nova consulta para o banco de dados **mestre** em seu servidor lógico.

2. Crie um logon e usuário que representem a conexão entre o Banco de Dados SQL e o data warehouse.

   ```sql
   CREATE LOGIN SalesDBLogin WITH PASSWORD = 'aReallyStrongPassword!@#';
   ```

3. Usando o SSMS ou outro cliente de consulta, abra uma nova consulta para a **instância do data warehouse do SQL** em seu servidor lógico.

4. Criar um usuário na instância do data warehouse com o logon criado na Etapa 2

   ```sql
   CREATE USER SalesDBUser FOR LOGIN SalesDBLogin;
   ```

5. Conceda permissões para o usuário da Etapa 4 para execução do Banco de Dados SQL. Neste exemplo, a permissão só está sendo concedida para SELECT em um esquema específico, ilustrando como podemos restringir as consultas no Banco de Dados SQL para um domínio específico. 

   ```sql
   GRANT SELECT ON SCHEMA :: [dbo] TO SalesDBUser;
   ```

6. Usando o SSMS ou outro cliente de consulta, abra uma nova consulta para a **instância do Banco de Dados SQL** em seu servidor lógico.

7. Crie uma chave mestra, se você ainda não tiver uma. 

   ```sql
   CREATE MASTER KEY; 
   ```

8. Crie uma credencial com escopo no banco de dados usando as credenciais criadas na Etapa 2.

   ```sql
   CREATE DATABASE SCOPED CREDENTIAL SalesDBElasticCredential
   WITH IDENTITY = 'SalesDBLogin',
   SECRET = 'aReallyStrongPassword@#!';
   ```

9. Crie uma fonte de dados externa que aponte para a instância do data warehouse.

   ```sql
   CREATE EXTERNAL DATA SOURCE EnterpriseDwSrc WITH 
       (TYPE = RDBMS, 
       LOCATION = '<SERVER NAME>.database.windows.net', 
       DATABASE_NAME = '<SQL DATA WAREHOUSE NAME>', 
       CREDENTIAL = SalesDBElasticCredential, 
   ) ;
   ```

10. Agora você pode criar tabelas externas que fazem referência a essa fonte de dados externa. As consultas que usam essas tabelas são enviadas para a instância do data warehouse onde serão processadas e enviadas de volta para a instância do banco de dados.


## <a name="elastic-query-from-sql-database-to-sql-data-warehouse"></a>Consulta Elástica do Banco de Dados SQL para o SQL Data Warehouse

Nas próximas etapas, criaremos uma tabela em nossa instância do data warehouse com vários valores. Depois, demonstraremos como configurar uma tabela externa para consultar a instância do data warehouse a partir da instância do banco de dados.

1. Usando o SSMS ou outro cliente de consulta, abra uma nova consulta para o **SQL Data Warehouse** em seu servidor lógico.

2. Envie a consulta a seguir para criar uma tabela **OrdersInformation** em sua instância do data warehouse.

   ```sql
   CREATE TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL 
   ,   [CustomerID] [int] NOT NULL 
   ) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
   INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
   ```

3. Usando o SSMS ou outro cliente de consulta, abra uma nova consulta para o **Banco de Dados SQL** em seu servidor lógico.

4. Envie a consulta a seguir para criar uma definição de tabela externa que aponta para a tabela **OrdersInformation** na instância do data warehouse.

   ```sql
   CREATE EXTERNAL TABLE [dbo].[OrderInformation]
   ( 
       [OrderID] [int] NOT NULL
   ,   [CustomerID] [int] NOT NULL 
   ) 
   WITH 
   (
        DATA_SOURCE = EnterpriseDwSrc
   ,    SCHEMA_NAME = N'dbo'
   ,    OBJECT_NAME = N'OrderInformation'
   )
   ```

5. Observe que agora você tem uma definição de tabela externa em sua **instância do Banco de Dados SQL**.

   ![definição de tabela externa da consulta elástica](media/sql-data-warehouse-elastic-query-with-sql-database/elastic-query-external-table.png)


6. Envie a consulta a seguir, que consulta a instância do data warehouse. Você deve receber os cinco valores que você inseriu na Etapa 2. 

```sql
SELECT * FROM [dbo].[OrderInformation];
```

> [!NOTE]
>
> Observe que, apesar de ter poucos valores, essa consulta demora um tempo considerável para retornar. Ao usar a consulta elástica com o data warehouse, é necessário considerar os custos de sobrecarga do processamento de consulta e movimentação eletrônica. Utilize a execução remota da consulta elástica quando a capacidade de computação, não a latência, for a prioridade.

Parabéns, você configurou o básico da Consulta Elástica. 

## <a name="next-steps"></a>Próximas etapas
Para recomendações, consulte [Práticas recomendadas para usar a Consulta Elástica com o SQL Data Warehouse do Azure](how-to-use-elastic-query-with-sql-data-warehouse.md).