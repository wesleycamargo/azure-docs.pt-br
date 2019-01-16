---
title: Controle de transações e simultaneidade otimista do banco de dados no Azure Cosmos DB
description: Este artigo descreve o controle de transações e simultaneidade otimista do banco de dados no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: cbd09f141128f9103af88b695baf717eaa3c99d5
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038829"
---
# <a name="database-transactions-and-optimistic-concurrency-control"></a>Controle de transações e simultaneidade otimista do banco de dados

As transações do banco de dados oferecem um modelo de programação seguro e previsível para lidar com alterações simultâneas aos dados. Os bancos de dados relacionais tradicionais, como o SQL Server, permitem que você escreva a lógica de negócios usando procedimentos armazenados e/ou gatilhos, envie-a para o servidor para execução diretamente do mecanismo de banco de dados. Com bancos de dados relacionais tradicionais, é necessário lidar com duas linguagens de computador diferentes – a linguagem de programação de aplicativo (não transacional), como JavaScript, Python, C#, Java, etc., e a linguagem de programação transacional (T-SQL), nativamente executada pelo banco de dados.

O mecanismo de banco de dados no Azure Cosmos DB dá suporte completo a transações em conformidade ACID (atomicidade, consistência, isolamento, durabilidade) com isolamento de instantâneo. Todas as operações de banco de dados dentro do escopo da partição lógica de um contêiner são executadas de maneira transacional dentro do mecanismo de banco de dados hospedado pela réplica da partição. Essas operações incluem operações de gravação (atualização de um ou mais itens dentro da partição lógica) e de leitura. A tabela a seguir ilustra diferentes operações e tipos de transação:

| **Operação**  | **Tipo de operação** | **Transação de item único ou de vários itens** |
|---------|---------|---------|
| Inserir (sem um pré/pós-gatilho) | Gravar | Transação de item único |
| Inserir (com um pré/pós-gatilho) | Gravar e ler | Transação de vários itens |
| Substituir (sem um pré/pós-gatilho) | Gravar | Transação de item único |
| Substituir (com um pré/pós-gatilho) | Gravar e ler | Transação de vários itens |
| Upsert (sem um pré/pós-gatilho) | Gravar | Transação de item único |
| Upsert (com um pré/pós-gatilho) | Gravar e ler | Transação de vários itens |
| Excluir (sem um pré/pós-gatilho) | Gravar | Transação de item único |
| Excluir (com um pré/pós-gatilho) | Gravar e ler | Transação de vários itens |
| Executar procedimento armazenado | Gravar e ler | Transação de vários itens |
| O sistema iniciou a execução de um procedimento de mesclagem | Gravar | Transação de vários itens |
| O sistema iniciou a execução da exclusão de itens com base na expiração (vida útil) de um item | Gravar | Transação de vários itens |
| Ler | Ler | Transação de item único |
| Feed de alterações | Ler | Transação de vários itens |
| Leitura paginada | Ler | Transação de vários itens |
| Consulta paginada | Ler | Transação de vários itens |
| Executar UDF como parte da consulta paginada | Ler | Transação de vários itens |

## <a name="multi-item-transactions"></a>Transações de vários itens

O Azure Cosmos DB permite gravar procedimentos armazenados, pré/pós-gatilhos, UDFs (funções definidas pelo usuário) e procedimentos de mesclagem em JavaScript. O Azure Cosmos DB dá suporte nativo à execução de JavaScript dentro de seu mecanismo de banco de dados. É possível registrar procedimentos armazenados, pré/pós-gatilhos, UDFs (funções definidas pelo usuário) e procedimentos de mesclagem em um contêiner e executá-los posteriormente de maneira transacional dentro do mecanismo de banco de dados do Azure Cosmos. Gravar uma lógica de aplicativo em JavaScript permite a expressão natural do fluxo de controle, escopo de variáveis, atribuição e integração de primitivos de manuseio de exceções dentro de transações do banco de dados diretamente na linguagem JavaScript.

Os procedimentos armazenados baseados em JavaScript, gatilhos, UDFs e procedimentos de mesclagem são encapsulados dentro de uma transação ACID ambiente com isolamento de instantâneos em todos os itens dentro da partição lógica. No decorrer de sua execução, se o programa JavaScript gerar uma exceção, toda a transação será abortada e revertida. O modelo de programação resultante é simples, porém, poderoso. Os desenvolvedores de JavaScript obtêm um modelo de programação durável, ao mesmo tempo em que utilizam os construtores de linguagem e primitivas de biblioteca com os quais estão familiarizados.

A capacidade de executar JavaScript diretamente dentro do mecanismo de banco de dados oferece desempenho e execução transacional de operações do banco de dados com relação aos itens de um contêiner. Além disso, como o mecanismo de banco de dados do Azure Cosmos dá suporte nativo a JSON e JavaScript, não há incompatibilidade de impedância entre os sistemas do tipo de um aplicativo e o banco de dados.

## <a name="optimistic-concurrency-control"></a>Controle de simultaneidade otimista 

O controle de simultaneidade otimista permite que você evite a perda de atualizações e exclusões. Operações simultâneas e conflitantes estão sujeitas ao bloqueio pessimista regular do mecanismo de banco de dados hospedado pela partição lógica da qual o item faz parte. Quando duas operações simultâneas estão tentando atualizar a versão mais recente de um item dentro de uma partição lógica, uma delas vencerá e a outra falhará. No entanto, se uma ou duas operações tentando atualizar simultaneamente o mesmo item tivessem lido anteriormente um valor mais antigo do item, o banco de dados não saberia se o valor lido anteriormente por qualquer uma ou por ambas as operações conflitantes era de fato o valor mais recente do item. Felizmente, essa situação pode ser detectada com o OCC (Controle de Simultaneidade Otimista) antes de permitir que as duas operações entrem no limite de transação dentro do mecanismo de banco de dados. O OCC protege seus dados contra alterações de substituição acidental feitas por outras pessoas. Ele também impede outras pessoas de substituir acidentalmente suas próprias alterações.

As atualizações simultâneas de um item estão sujeitas ao OCC pela camada de protocolo de comunicação do Azure Cosmos DB. O banco de dados do Azure Cosmos garante que a versão do lado do cliente do item que você está atualizando (ou excluindo) é a mesma que a versão do item no contêiner do Azure Cosmos. Isso garante que suas gravações sejam protegidas contra substituições acidentais realizadas pelas gravações de outras pessoas e vice-versa. Em um ambiente multiusuário, o controle de simultaneidade otimista protege você contra a exclusão ou a atualização acidental da versão incorreta de um item. Como tal, os itens são protegidos contra problemas infames de "atualização perdida" ou "exclusão perdida".

Cada item armazenado em um contêiner do Azure Cosmos tem uma propriedade `_etag` definida por sistema. O valor do `_etag` é gerado e atualizado automaticamente pelo servidor toda vez que o item é atualizado. `_etag` pode ser usado com o cabeçalho de solicitação if-match fornecido pelo cliente para permitir que o servidor decida se um item pode ser atualizado condicionalmente. Se o valor do cabeçalho if-match corresponder ao valor do `_etag` no servidor, então o item será atualizado. Se o valor do cabeçalho de solicitação if-match não for mais atual, o servidor rejeitará a operação com uma mensagem de resposta "HTTP 412 Falha de pré-condição". O cliente, então, poderá buscar novamente o item para adquirir a versão atual dele no servidor ou substituir a versão de item no servidor por seu próprio valor `_etag` para o item. Além disso, `_etag` podem ser usadas com o cabeçalho if-none-match para determinar se uma nova busca de um recurso é necessária. 

O valor da __etag do item muda sempre que o item é atualizado. Para operações de item de substituição, if-match deve ser explicitamente expresso como parte das opções de solicitação. Para obter um exemplo, confira o código de exemplo no [GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs#L398-L446). Os valores `_etag` são implicitamente verificados para todos os itens gravados tocados por um procedimento armazenado. Se qualquer conflito for detectado, o procedimento armazenado reverterá a transação e gerará uma exceção. Com esse método, toda ou nenhuma gravação dentro do procedimento armazenado é aplicada de maneira atômica. Isso é um sinal para o aplicativo reaplicar atualizações e repetir a solicitação original do cliente.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre controle de transações e simultaneidade otimista do banco de dados nos seguintes artigos:

- [Trabalhando com bancos de dados, contêineres e itens do Azure Cosmos](databases-containers-items.md)
- [Níveis de consistência](consistency-levels.md)
- [Tipos e políticas de resolução de conflitos](conflict-resolution-policies.md)
