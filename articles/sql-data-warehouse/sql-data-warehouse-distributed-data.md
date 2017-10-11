---
title: "Como os dados distribuídos funcionam no SQL Data Warehouse do Azure | Microsoft Docs"
description: "Saiba como os dados são distribuídos para MPP (Processamento Paralelo Maciço) e as opções de distribuição tabelas no SQL Data Warehouse do Azure e Parallel Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: bae494a6-7ac5-4c38-8ca3-ab2696c63a9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 07/12/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 3c166acb17193caae32d7bad133ec510ff679353
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2017
---
# <a name="distributed-data-and-distributed-tables-for-massively-parallel-processing-mpp"></a>Dados distribuídos e tabelas distribuídas para MPP (Processamento Paralelo Maciço)
Saiba como os dados do usuário são distribuídos no SQL Data Warehouse do Azure e Parallel Data Warehouse, que são os sistemas de MPP (Processamento Paralelo Maciço) da Microsoft. Criar seu data warehouse para usar dados distribuídos efetivamente ajuda a obter a benefícios de processamento de consulta da arquitetura MPP. Algumas opções de design de banco de dados podem ter um impacto significativo sobre como melhorar o desempenho da consulta.  

> [!NOTE]
> O SQL Data Warehouse do Azure e o Parallel Data Warehouse usam o mesmo design MPP (Processamento Paralelo Maciço), mas eles têm algumas diferenças devido à plataforma subjacente. O SQL Data Warehouse é uma PaaS (plataforma como serviço) executada no Azure. Parallel Data Warehouse é executado no APS (sistema de Plataforma de Análise), que é um dispositivo local executado no Windows Server.
> 
> 

## <a name="what-is-distributed-data"></a>O que são dados distribuídos?
No SQL Data Warehouse e Parallel Data Warehouse, dados distribuídos refere-se a dados de usuário armazenados em vários locais em todo o sistema MPP. Cada um desses locais funciona como um armazenamento independente e a unidade de processamento que executa consultas em sua parte dos dados. Dados distribuídos são fundamentais para a execução de consultas em paralelo para alcançar o alto desempenho de consulta.

Para distribuir os dados, o data warehouse atribui cada linha de uma tabela de usuário para um local distribuído.  Você pode distribuir tabelas com um método de distribuição de hash ou um método round robin. O método de distribuição é especificado na instrução CREATE TABLE. 

## <a name="hash-distributed-tables"></a>Tabelas distribuídas em hash
O diagrama a seguir ilustra como uma (tabela não distribuída) completa é armazenada como uma tabela distribuída em hash. Uma função determinística atribui cada linha para pertencer a uma distribuição. Na definição de tabela, uma das colunas é designada como a coluna de distribuição. A função de hash usa o valor na coluna de distribuição para atribuir cada linha a uma distribuição.

Há considerações de desempenho para a seleção de uma coluna de distribuição, como distinção, distorção de dados e tipos de consultas executadas no sistema.

![Tabela distribuída](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Tabela distribuída")  

* Cada linha pertence a uma distribuição.  
* Um algoritmo de hash determinístico atribui cada linha a uma distribuição.  
* O número de linhas de tabela por distribuição varia conforme mostrado pelos diferentes tamanhos de tabelas.

## <a name="round-robin-distributed-tables"></a>Tabelas distribuídas round robin
Uma tabela distribuída round robin distribui as linhas atribuindo a cada linha uma distribuição de forma sequencial. É rápido carregar dados em uma tabela round robin, mas o desempenho de consulta pode ser mais lento.  Ingressar em uma tabela de round robin normalmente requer recombinar as linhas para habilitar a consulta a produzir um resultado preciso, o que leva tempo.

## <a name="distributed-storage-locations-are-called-distributions"></a>Locais de armazenamento distribuído são chamados de distribuições
Cada local distribuído é chamado de distribuição. Quando uma consulta é executada em paralelo, cada distribuição executa uma consulta SQL em sua parte dos dados. SQL Data Warehouse usa um Banco de Dados SQL para executar a consulta. Parallel Data Warehouse usa o SQL Server para executar a consulta. Esse design de arquitetura de nada compartilhado é fundamental para alcançar a computação paralela em escala horizontal.

### <a name="can-i-view-the-distributions"></a>Posso exibir as distribuições?
Cada distribuição tem uma ID de distribuição e está visível nas exibições do sistema que pertencem ao SQL Data Warehouse e ao Parallel Data Warehouse. Você pode usar a ID de distribuição para solucionar problemas de desempenho da consulta e outros problemas. Para obter uma lista das exibições de sistema, consulte [Exibição do sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="difference-between-a-distribution-and-a-compute-node"></a>Diferença entre uma distribuição e um nó de Computação
Uma distribuição é a unidade básica de armazenamento de dados distribuídos e processamento de consultas paralelas. Distribuições são agrupadas em nós de Computação. Cada nó de Computação rastreia uma ou mais distribuições.  

* O Analytics Platform System usa nós de Computação como um componente central dos recursos de escala horizontal arquitetura de hardware. Ele sempre usa oito distribuições por nó de computação, conforme mostrado no diagrama para tabelas distribuídas em hash. O número de nós de Computação e, portanto, o número de distribuições são determinados pelo número de nós de Computação que você compra para o dispositivo. Por exemplo, se você comprar oito nós de Computação, obterá 64 distribuições (oito nós de computação x oito distribuições/nó). 
* O SQL Data Warehouse tem um número fixo de 60 distribuições e um número flexível de nós de Computação. Os nós de Computação são implementados com recursos de computação e armazenamento do Azure. O número de nós de computação pode mudar de acordo com a carga de trabalho do serviço de back-end e a capacidade de computação (DWUs) especificadas para o data warehouse. Quando o número de nós de Computação muda, o número de distribuições por nó de computação também muda. 

### <a name="can-i-view-the-compute-nodes"></a>Posso exibir os nós de Computação?
Cada nó de computação tem uma ID de nó e está visível nas exibições do sistema referentes ao SQL Data Warehouse e ao Parallel Data Warehouse.  Você pode ver o nó de Computação olhando para a coluna node_id nas exibições do sistema cujos nomes começam com sys.pdw_nodes. Para obter uma lista das exibições de sistema, consulte [Exibição do sistema MPP](sql-data-warehouse-reference-tsql-statements.md).

## <a name="Replicated"></a>Tabelas Replicadas
Uma tabela replicada tem uma cópia completa da tabela armazenada em cada nó de Computação. Replicar uma tabela elimina a necessidade de transferir dados entre nós de Computação antes de uma junção ou agregação. Tabelas replicadas só são viáveis com tabelas pequenas devido ao armazenamento extra necessário para armazenar a tabela completa em cada nó de computação.  

O diagrama a seguir mostra uma tabela replicada armazenada em cada nó de Computação. Para SQL Data Warehouse, a tabela replicada é totalmente copiada para um banco de dados de distribuição em cada nó de computação. Para o Data Warehouse Paralelo, a tabela replicada é armazenada em todos os discos atribuídos ao nó de Computação.  Essa estratégia de disco é implementada usando grupos de arquivos do SQL Server.  

![Tabela replicada](media/sql-data-warehouse-distributed-data/replicated-table.png "Tabela replicada") 

## <a name="next-steps"></a>Próximas etapas
Para usar tabelas distribuídas com eficiência, consulte [Distribuir tabelas no SQL Data Warehouse](sql-data-warehouse-tables-distribute.md)  

