---
title: "Azure DocumentDB como um repositório de valores de chave – Visão geral do custo | Microsoft Docs"
description: "Saiba mais sobre o baixo custo do uso do Azure DocumentDB como um repositório de valores de chave."
keywords: "repositório de valores de chave"
services: documentdb
author: ArnoMicrosoft
manager: jhubbard
editor: 
tags: 
documentationcenter: 
ms.assetid: 7f765c17-8549-4509-9475-46394fc3a218
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: acomet
translationtype: Human Translation
ms.sourcegitcommit: 6fc5dfd3fbdd2e0690b60f4b232e624c34bf53b6
ms.openlocfilehash: c1022a127266d28d3b59bfebd0543a840fe11e3a


---

# <a name="documentdb-as-a-key-value-store--cost-overview"></a>DocumentDB como um repositório de valores de chave – Visão geral do custo

O Azure DocumentDB é um serviço de banco de dados NoSQL distribuído globalmente e totalmente gerenciado para a compilação de aplicativos altamente disponíveis, de grande escala e [distribuídos globalmente](documentdb-distribute-data-globally.md). Por padrão, o DocumentDB indexa automaticamente e com eficiência todos os dados que ingere. Isso permite consultas [SQL](documentdb-sql-query.md) (e [JavaScript](documentdb-programming.md)) rápidas e consistentes em qualquer tipo de dados. 

Este artigo descreve o custo do DocumentDB para operações simples de gravação e leitura quando é usado como um repositório de chaves/valores. Operações de gravação incluem inserções, substituições, exclusões e upserts de documentos. Além de garantir 99,99% de a alta disponibilidade, o DocumentDB oferece <10 ms de latência para leituras e <15 ms de latência para as gravações (indexadas) respectivamente, na casa dos 99 por cento. 

## <a name="why-we-use-request-units-rus"></a>Por que usamos RUs (Unidades de Solicitação)

O desempenho do DocumentDB tem base na quantidade de RU [(Unidades de Solicitação)](documentdb-programming.md) provisionada para a partição. O provisionamento ocorre a uma granularidade de segundos e é adquirido em RUs/s ([não deve ser confundido com a cobrança por hora](https://azure.microsoft.com/pricing/details/documentdb/)). RUs devem ser considerados uma moeda que simplifica o provisionamento da taxa de transferência necessária para o aplicativo. Nossos clientes não precisam pensar em diferenciar unidades de capacidade de leitura e gravação. O modelo de moeda única de RUs cria eficiência no compartilhamento da capacidade provisionada entre leituras e gravações. Esse modelo de capacidade provisionada permite que o serviço forneça uma taxa de transferência previsível e consistente, além de garantia de baixa latência e alta disponibilidade. Finalmente, usamos o RU para modelar a taxa de transferência, mas cada RU provisionado também tem uma quantidade definida de recursos (Memória, Núcleo). RU/s não é apenas o IOPS.

Como um sistema de bancos de dados distribuídos globalmente, o DocumentDB é o único serviço do Azure que fornece um SLA sobre latência, taxa de transferência e consistência, além de alta disponibilidade. A taxa de transferência que você provisiona é aplicada a cada uma das regiões associadas à sua conta de banco de dados do DocumentDB. Para leituras, o DocumentDB oferece vários [níveis de consistência](documentdb-consistency-levels.md) bem definidos para sua escolha. 

A tabela a seguir mostra o número de RUs necessários para realizar transações de leitura e gravação com base em alguns tamanhos de documento. 

|Tamanho do documento|1 Leitura|1 Gravação|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|5 KB|5 RUs|25 RUs|
|10 KB|10 RUs|50 RUs|
|50 KB|50 RUs|250 RUs|
|100 KB|100 RUs|500 RUs|

## <a name="cost-of-running-documentdb-in-readwrite-mode-without-indexing"></a>Custo da execução do DocumentDB no modo de Leitura/Gravação sem a indexação

Se você provisionar 1.000 RU/s, o resultado será de 3,6 m RU/hora e custará $0,08 por hora (nos EUA e Europa). Para um documento com 1KB, isso significa que você pode consumir 3,6 m de leituras ou 0,72 m de gravações (3,6m RU/5) usando sua taxa de transferência provisionada. Ao normalizar para milhões de leituras e gravações, o custo seria de $0,022/m leituras ($0,08 / 3,6) e $0,111/m de gravações ($0,08 / 0,72). O custo por milhão se torna mínimo, conforme mostra a tabela a seguir.

|Tamanho do documento|1m de Leitura|1m de Gravação|
|-------------|-------|--------|
|1 KB|$&0;,022|$&0;,111|
|5 KB|$&0;,111|$&0;,556|
|10 KB|$&0;,222|$&1;,111|
|50 KB|$&1;,111|$&5;,556|
|100 KB|$&2;,222|$&11;,111|

A maioria dos repositórios de blob ou de objeto básicos, como os serviços AWS S3 ou o Armazenamento de Blobs do Azure, cobra $&0;,40 por um milhão de transações de leitura, e $&5; por um milhão de transações de gravação. Se usado corretamente, o DocumentDB pode ser até 98% mais barato do que essas outras soluções (para transações de 1KB).

## <a name="next-steps"></a>Próximas etapas

Fique atento a novos artigos sobre como otimizar o provisionamento de recursos do DocumentDB. Enquanto isso, fique à vontade para usar nossa [Calculadora de RU](https://www.documentdb.com/capacityplanner).



<!--HONumber=Feb17_HO1-->


