---
title: Solucionar problemas de eventos de entrada malformados no Azure Stream Analytics | Microsoft Docs
description: Como saber qual evento nos meus dados de entrada está causando o problema em um trabalho do Stream Analytics
keywords: ''
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: Kfile
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/05/2018
ms.author: sngun
ms.openlocfilehash: 6b6c154568fe97b7495ae70dc162dc475169afea
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="common-issues-in-stream-analytics-and-steps-to-troubleshoot"></a>Problemas comuns no Stream Analytics e etapas para solucionar problemas

## <a name="troubleshoot-for-malformed-input-events"></a>Solucionar problemas de eventos de entrada malformados

Quando o fluxo de entrada de seu trabalho do Stream Analytics contém mensagens malformadas, ele causa problemas de serialização. As mensagens malformadas incluem serialização incorreta, como parênteses ausentes em um objeto JSON ou formato de carimbo de data/hora incorreto. Quando um trabalho do Stream Analytics recebe uma mensagem malformada, ele descarta a mensagem e notifica o usuário com um aviso. Um símbolo de aviso é mostrado no bloco **Entradas** do trabalho do Stream Analytics (esse sinal de aviso existe enquanto o trabalho estiver no estado em execução):

![Bloco Entradas](media/stream-analytics-malformed-events/inputs_tile.png)

É possível habilitar os logs de diagnóstico para visualizar os detalhes do aviso. Para eventos de entrada mal formatados, os logs de execução contêm uma entrada com a mensagem que semelhante a: "Mensagem: Não foi possível desserializar os eventos de entrada do recurso <blob URI> como json)". 

### <a name="troubleshooting-steps"></a>Etapas para solucionar problemas

1. Navegue até o bloco de entrada e clique para exibir avisos.
2. O bloco de detalhes de entrada exibe um conjunto de avisos com detalhes sobre o problema. A seguir está uma mensagem de aviso de exemplo, a mensagem de aviso mostra a Partição, o Deslocamento e números de sequência em que há dados JSON malformados. 

   ![Mensagem de aviso com deslocamento](media/stream-analytics-malformed-events/warning_message_with_offset.png)

3. Para obter os dados JSON com formato incorreto, execute o código CheckMalformedEvents.cs, e é possível obtê-lo do [repositório de amostras GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Este código lê a partição Id, deslocamento, e imprime os dados que estão localizados nesse deslocamento. 

4. Depois de ler os dados, você pode analisar e corrigir o formato de serialização. 

## <a name="handling-duplicate-records-when-using-azure-sql-database-as-output-for-a-stream-analytics-job"></a>Manipulação de registros duplicados ao usar o Banco de Dados SQL do Azure como saída para um trabalho do Stream Analytics

Ao configurar o Banco de Dados SQL do Azure como saída para um trabalho do Stream Analytics, o volume insere registros na tabela de destino. Em geral, o Azure Stream Analytics garante [pelo menos uma vez a entrega]( https://msdn.microsoft.com/azure/stream-analytics/reference/event-delivery-guarantees-azure-stream-analytics) para o coletor de saída, ainda pode-se obter [EOD (exactly-once-delivery)]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) para saída do SQL quando a tabela do SQL tiver uma restrição exclusiva definida. 

Uma vez que as restrições de chave exclusivas são configuradas na tabela do SQL e há registros duplicados sendo inseridos na tabela do SQL, o Azure Stream Analytics remove a gravação duplicada, dividindo os dados em lotes e inserindo recursivamente os lotes até localizar uma única gravação duplicada. Se houver um número considerável de linhas duplicadas no pipeline, a divisão e a inserção recursiva de dados ignorando duplicados um a um será um processo lento. Se visualizar várias mensagens de advertência de violação da chave no log de atividades na última hora, é provável que a saída do SQL esteja retardando o trabalho inteiro. Para resolver esse problema, é necessário [configurar o índice]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) que está causando a violação da chave, habilitando a opção IGNORE_DUP_KEY. Habilitar essa opção permite que os valores duplicados sejam ignorados pelo SQL durante as inserções em massa e o SQL do Azure simplesmente produz uma mensagem de aviso em vez de um erro. O Azure Stream Analytics não produz mais erros de violação de chave primária.

Observe as observações a seguir ao configurar IGNORE_DUP_KEY para vários tipos de índices:

* Não é possível definir IGNORE_DUP_KEY em uma chave primária ou uma restrição exclusiva que utiliza ALTER INDEX, sendo necessário remover e recriar o índice.  
* É possível definir a opção IGNORE_DUP_KEY utilizando ALTER INDEX para um índice exclusivo, que é diferente da restrição PRIMARY KEY/UNIQUE e criada utilizando a definição CREATE INDEX ou INDEX.  
* IGNORE_DUP_KEY não é aplicável para índices de repositório de coluna porque não é possível impor a exclusividade desses índices.  

## <a name="next-steps"></a>Próximas etapas

* [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

