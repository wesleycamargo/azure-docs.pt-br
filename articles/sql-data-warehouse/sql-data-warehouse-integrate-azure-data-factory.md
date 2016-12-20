---
title: Usar o Azure Data Factory com o SQL Data Warehouse | Microsoft Docs
description: "Dicas de utilização do  Azure Data Factory (ADF) com o Azure SQL Data Warehouse para o desenvolvimento de soluções."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 427a1e8c6bfb11ace38fcf84b7cda89d2d01d091


---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>Use o Azure Data Factory com o SQL Data Warehouse
O Azure Data Factory fornece um método totalmente gerenciado para orquestrar a transferência de dados e a execução de procedimentos armazenados no SQL Data Warehouse.  Isso permitirá que você configure e programe mais facilmente procedimentos complexos de Extração, Transformação e Carga (ELT) com o SQL Data Warehouse. Para obter uma visão geral mais completa do Azure Data Factory, consulte a [Documentação do Azure Data Factory][Documentação do Azure Data Factory].

## <a name="data-movement"></a>Movimentação de dados
O Azure Data Factory permite a movimentação de dados entre fontes locais e diferentes serviços do Azure.  De forma geral, a integração atual com o Azure Data Factory oferece suporte à movimentação de dados de e para os seguintes locais:

* Armazenamento do blob do Azure
* Banco de Dados SQL Azure
* SQL Server local
* SQL Server na IaaS

Para saber mais sobre como configurar uma atividade de cópia de dados, consulte [Copiar dados com o Azure Data Factory][Copiar dados com o Azure Data Factory]

## <a name="stored-procedures"></a>Procedimentos Armazenados
 Da mesma forma que ele pode ser usado para agendar a transferência de dados, o Azure Data Factory também pode ser usado para coordenar a execução de procedimentos armazenados.  Isso permite que pipelines mais complexos sejam criados e estende a capacidade do Azure Data Factory de aproveitar o poder computacional do SQL Data Warehouse.

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral de integração, consulte [Visão geral de integração do SQL Data Warehouse][Visão geral de integração do SQL Data Warehouse].
Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL Data Warehouse][Visão geral de desenvolvimento do SQL Data Warehouse].

<!--Image references-->

<!--Article references-->

[Copiar dados com o Azure Data Factory]: ../data-factory/data-factory-data-movement-activities.md
[Visão geral de desenvolvimento do SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Visão geral de integração do SQL Data Warehouse]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Documentação do Azure Data Factory]:https://azure.microsoft.com/documentation/services/data-factory/




<!--HONumber=Nov16_HO3-->


