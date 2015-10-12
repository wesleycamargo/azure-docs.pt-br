<properties
   pageTitle="Use o Azure Data Factory com o SQL Data Warehouse | Microsoft Azure"
   description="Dicas de utilização do Azure Data Factory (ADF) com o Azure SQL Data Warehouse para o desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/23/2015"
   ms.author="lodipalm"/>

# Use o Azure Data Factory com o SQL Data Warehouse

O Azure Data Factory fornece um método totalmente gerenciado para orquestrar a transferência de dados e a execução de procedimentos armazenados no SQL Data Warehouse. Isso permitirá que você configure e programe mais facilmente procedimentos complexos de Extração, Transformação e Carga (ELT) com o SQL Data Warehouse. Para obter uma visão mais completa do Azure Data Factory, consulte a [documentação do Azure Data Factory][].

## Movimentação de dados 

O Azure Data Factory permite a movimentação de dados entre fontes locais e diferentes serviços do Azure. De forma geral, a integração atual com o Azure Data Factory oferece suporte à movimentação de dados de e para os seguintes locais:

+ Armazenamento do blob do Azure
+ Banco de Dados SQL do Azure
+ SQL Server local
+ SQL Server na IaaS

Para obter informações sobre como configurar uma atividade de cópia de dados, veja [Copiar dados com o Azure Data Factory](../data-factory/data-factory-data-movement-activities.md).

## Procedimentos Armazenados
 Da mesma forma que ele pode ser usado para agendar a transferência de dados, o Azure Data Factory também pode ser usado para coordenar a execução de procedimentos armazenados. Isso permite que pipelines mais complexos sejam criados e estende a capacidade do Azure Data Factory de aproveitar o poder computacional do SQL Data Warehouse.

## Próximas etapas
Para obter uma visão geral da integração, consulte [Visão geral de integração do SQL Data Warehouse](sql-data-warehouse-overview-integrate.md). Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL Data Warehouse](sql-data-warehouse-overview-develop.md).

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: https://azure.microsoft.com/pt-BR/documentation/articles/data-factory-azure-sql-connector/
[SQL Data Warehouse development overview]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]: https://azure.microsoft.com/pt-BR/documentation/articles/sql-data-warehouse-overview-integrate/

<!--MSDN references-->

<!--Other Web references-->
[documentação do Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Copy data with Azure Data Factory]: https://azure.microsoft.com/pt-BR/documentation/articles/data-factory-data-movement-activities/

<!---HONumber=Oct15_HO1-->