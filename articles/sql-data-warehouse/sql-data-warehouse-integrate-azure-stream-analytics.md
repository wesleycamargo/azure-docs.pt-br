---
title: Usar o Stream Analytics do Azure com o SQL Data Warehouse | Microsoft Docs
description: "Dicas para usar o Stream Analytics do Azure com o Azure SQL Data Warehouse para desenvolver as soluções."
services: sql-data-warehouse
documentationcenter: NA
author: kevinvngo
manager: barbkess
editor: 
ms.assetid: 8aeb2247-20c5-4a29-b327-30a8ce09dfdc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: kevin;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f0d7faede5df839c699f562b3aa21b8bb9836a3b


---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Usar o Stream Analytics do Azure com o SQL Data Warehouse
A Stream Analytics do Azure é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos ao longo do fluxo de dados na nuvem. Você pode aprender as noções básicas lendo [Introdução ao Stream Analytics do Azure][Introdução ao Stream Analytics do Azure]. Depois, você pode saber como criar uma solução de ponta a ponta com o Stream Analytics seguindo o tutorial [Introdução ao uso do Stream Analytics do Azure][Introdução ao uso do Stream Analytics do Azure].

Neste artigo, você aprenderá como usar o banco de dados do SQL Data Warehouse do Azure como um coletor de saída seus trabalhos do  Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos
Primeiro, execute as seguintes etapas no tutorial [Introdução ao uso do Azure Stream Analytics][Introdução ao uso do Stream Analytics do Azure].  

1. Criar uma entrada de Hub de eventos
2. Configurar e iniciar o aplicativo gerador de evento
3. Provisionar um trabalho de análise de fluxo
4. Especifique a entrada e a consulta do trabalho

Em seguida, crie um banco de dados do SQL Data Warehouse do Azure

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Especifique a saída do trabalho: banco de dados do SQL Data Warehouse do Azure
### <a name="step-1"></a>Etapa 1
No trabalho Stream Analytics, clique em **SAÍDA** na parte superior da página e depois em **ADICIONAR SAÍDA**.

### <a name="step-2"></a>Etapa 2
Selecione o Banco de Dados SQL e clique em Avançar.

![][add-output]

### <a name="step-3"></a>Etapa 3
Insira os seguintes valores na próxima página:

* *Alias de saída*: insira um nome amigável para essa saída de trabalho.
* *Assinatura*:
  * se o seu banco de dados do SQL Data Warehouse estiver na mesma assinatura que o trabalho do Stream Analytics, selecione Usar Banco de Dados SQL da Assinatura Atual.
  * Se o seu banco de dados estiver em uma assinatura diferente, selecione Usar Banco de Dados SQL de Outra Assinatura.
* *Banco de dados*: especifique o nome de um banco de dados de destino.
* *Nome do servidor*: especifique o nome do servidor do banco de dados que você acabou de especificar. Você pode usar o Portal clássico do Azure para encontrá-lo.

![][server-name]

* *Nome de usuário*: especifique o nome de usuário de uma conta que tenha permissões de gravação para o banco de dados.
* *Senha*: forneça a senha da conta de usuário especificada.
* *Tabela*: especifique o nome da tabela de destino no banco de dados.

![][add-database]

### <a name="step-4"></a>Etapa 4
Clique no botão de verificação para adicionar essa saída e para verificar se o Stream Analytics pode se conectar com êxito ao banco de dados.

![][test-connection]

Quando a conexão com o banco de dados tiver êxito, você verá uma notificação na parte inferior do portal. Você pode clicar em Testar Conexão na parte inferior para testar a conexão com o banco de dados.

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral de integração, consulte [Visão geral de integração do SQL Data Warehouse][Visão geral de integração do SQL Data Warehouse].

Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL Data Warehouse][Visão geral de desenvolvimento do SQL Data Warehouse].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introdução ao Stream Analytics do Azure]: ../stream-analytics/stream-analytics-introduction.md
[Introdução ao uso do Stream Analytics do Azure]: ../stream-analytics/stream-analytics-get-started.md
[Visão geral de desenvolvimento do SQL Data Warehouse]:  ./sql-data-warehouse-overview-develop.md
[Visão geral de integração do SQL Data Warehouse]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Documentação do Stream Analytics do Azure]: http://azure.microsoft.com/documentation/services/stream-analytics/



<!--HONumber=Nov16_HO3-->


