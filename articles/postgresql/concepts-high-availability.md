---
title: Conceitos de alta disponibilidade no Banco de Dados do Azure para PostgreSQL | Microsoft Docs
description: "Este tópico fornece informações de alta disponibilidade ao usar o Banco de Dados do Azure para PostgreSQL"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/19/2017
ms.openlocfilehash: 600896cf064770a5b294f874dc29081f0ce7d942
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2017
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql"></a>Conceitos de alta disponibilidade no Banco de Dados do Azure para PostgreSQL
O serviço do Banco de Dados do Azure para PostgreSQL fornece garantia de alto nível de disponibilidade. O SLA (Contrato de Nível de Serviço) com suporte financeiro é 99,99% em disponibilidade geral. Não há praticamente nenhum tempo de inatividade de aplicativo ao usar este serviço.

## <a name="high-availability"></a>Alta disponibilidade
O modelo de alta disponibilidade (HA) se baseia em mecanismos internos de failover quando ocorre uma interrupção no nível do nó. Uma interrupção no nível do nó pode ocorrer devido a uma falha de hardware ou como reação a uma implantação de serviço.

Todas as vezes, as alterações feitas em um servidor de banco de dados do Banco de Dados do Azure para PostgreSQL ocorrem no contexto de uma transação. As alterações são registradas de forma síncrona no armazenamento do Azure quando a transação é confirmada. Em caso de uma interrupção no nível do nó, o servidor de banco de dados cria automaticamente um novo nó e anexa o armazenamento de dados ao novo nó. As conexões ativas são descartadas e nenhuma transação em andamento é confirmada.

## <a name="application-retry-logic-is-essential"></a>A lógica de repetição do aplicativo é essencial
É importante que aplicativos de banco de dados do PostgreSQL sejam compilados para detectar e repetir conexões descartadas e transações com falha. Quando o aplicativo realiza uma nova tentativa, a conexão do aplicativo é redirecionada de forma transparente para a instância recém-criada, o que assume o controle para a instância com falha.

Internamente no Azure, um gateway é usado para redirecionar as conexões para a nova instância. Após uma interrupção, normalmente o processo inteiro de failover leva dezenas de segundos. Como o redirecionamento é tratado internamente pelo gateway, a cadeia de conexão externa permanece a mesma para os aplicativos cliente.

## <a name="scaling-up-or-down"></a>Expandir ou reduzir
Semelhante ao modelo de alta disponibilidade, quando um Banco de Dados do Azure para PostgreSQL é expandido ou reduzido, uma nova instância do servidor é criada com o tamanho especificado. O armazenamento de dados existente é desanexado da instância original e anexado à nova instância.

Durante a operação de escala, ocorre uma interrupção nas conexões de banco de dados. Os aplicativos cliente são desconectados e as transações abertas não confirmadas são canceladas. Depois que o aplicativo cliente repetir a tentativa ou realizar uma nova conexão, o gateway direcionará a conexão para a instância recém-dimensionada. 

## <a name="next-steps"></a>Próximas etapas
- Para obter uma visão geral do serviço, confira [Visão geral do Banco de Dados para PostgreSQL](overview.md)
