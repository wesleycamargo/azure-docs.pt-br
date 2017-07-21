---
title: "Banco de Dados SQL - ajuste automático | Microsoft Docs"
description: "O Banco de Dados SQL analisa a consulta SQL e automaticamente se adapta à carga de trabalho do usuário."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/05/2017
ms.author: jovanpop
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: f5552793db2d89542782b7d9e8f996314f62e429
ms.contentlocale: pt-br
ms.lasthandoff: 06/17/2017


---
# <a name="automatic-tuning"></a>Ajuste automático

O Banco de Dados SQL do Azure é um serviço de dados totalmente gerenciado que monitora as consultas que são executadas no banco de dados e pode melhorar automaticamente o desempenho da carga de trabalho. O Banco de Dados SQL do Azure tem um mecanismo de inteligência interno que pode ajustar e melhorar o desempenho de suas consultas adaptando dinamicamente o banco de dados para sua carga de trabalho automaticamente. O ajuste automático no Banco de Dados SQL do Azure talvez seja um dos recursos mais importantes que você pode habilitar no Banco de Dados SQL do Azure para otimizar o desempenho das suas consultas.

Consulte este artigo para obter as etapas para [habilitar o ajuste automático](sql-database-automatic-tuning-enable.md) usando o portal do Azure.

## <a name="why-automatic-tuning"></a>Por que usar o ajuste automático?

Uma das principais tarefas de administração do banco de dados clássico é monitorar a carga de trabalho, identificando consultas SQL críticas, os índices que devem ser adicionados para melhorar o desempenho e os índices usados raramente. O Banco de Dados SQL do Azure fornece informações detalhadas sobre as consultas e índices que você precisa monitorar. No entanto, monitorar o banco de dados é uma tarefa difícil e entediante, especialmente ao lidar com muitos bancos de dados. Gerenciar um grande número de bancos de dados pode ser impossível de se fazer com eficiência, mesmo com todas as ferramentas e relatórios disponíveis fornecidos pelo Banco de Dados SQL do Azure e pelo portal do Azure. Em vez de monitorar e ajustar o banco de dados manualmente, você pode considerar delegar algumas das ações de monitoramento e ajustes usando o recurso de ajuste automático do Banco de Dados SQL do Azure. 


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="how-does-automatic-tuning-work"></a>Como funciona o trabalho de ajuste automático?

O Banco de Dados SQL do Azure tem um processo de análise e monitoramento de desempenho contínuo que constantemente aprende sobre as características da sua carga de trabalho e identifica possíveis problemas e aprimoramentos.

![Processo de ajuste automático](./media/sql-database-automatic-tuning/tuning-process.png)

Esse processo permite que o Banco de Dados SQL do Azure adapte-se dinamicamente à sua carga de trabalho, localizando quais planos e índices podem melhorar o desempenho das cargas de trabalho e quais índices afetam suas cargas de trabalho. Com base nessas conclusões, o ajuste automático aplica ações de ajuste que melhoram o desempenho da carga de trabalho. Além disso, o Banco de Dados SQL do Azure monitora continuamente o desempenho após qualquer alteração feita pelo ajuste automático para garantir que ele melhora o desempenho da carga de trabalho. Qualquer ação que não melhorar o desempenho é revertida automaticamente. Esse processo de verificação é um recurso importante que garante que qualquer alteração feita pelo ajuste automático não diminua o desempenho da carga de trabalho.

Há dois aspectos de ajuste automático que estão disponíveis no Banco de Dados SQL do Azure:

 -  **Gerenciamento de índice automático** que identifica os índices que devem ser adicionados ao seu banco de dados e os índices que devem ser removidos.
 -  **Correção automática de plano** (em breve; já está disponível no SQL Server 2017) que identifica planos problemáticos e corrige problemas de desempenho do plano SQL .

## <a name="automatic-index-management"></a>Gerenciamento de índice automático

No Banco de Dados SQL do Azure, o gerenciamento de índice é fácil porque o Banco de Dados SQL do Azure aprende sobre sua carga de trabalho e garante que seus dados são indexados sempre da melhor forma. Um design de índice apropriado é crucial para otimizar o desempenho da carga de trabalho e o gerenciamento de índice automático pode ajudar a otimizar seus índices. O gerenciamento de índice automático pode corrigir problemas de desempenho em bancos de dados indexados incorretamente ou manter e aprimorar os índices no esquema de banco de dados existente. 

### <a name="why-do-you-need-index-management"></a>Por que você precisa de gerenciamento de índice?

Os índices aceleram algumas de suas consultas de leitura de dados das tabelas; no entanto, eles podem retardar as consultas que atualizam dados. Você precisa analisar cuidadosamente quando criar um índice e quais colunas você precisa incluir no índice. Alguns índices podem não ser mais necessários após algum tempo. Portanto, você precisa identificar periodicamente e descartar os índices que não geram nenhum benefício. Se você ignorar os índices não utilizados, o desempenho das consultas que atualizam dados será reduzido sem nenhum benefício nas consultas de leitura de dados. Índices não utilizados também afetam o desempenho geral do sistema porque atualizações adicionais precisam do log desnecessário.

Descobrir o conjunto ideal de índices que melhoram o desempenho das consultas de leitura de dados das tabelas e que tenham um impacto mínimo sobre as atualizações pode exigir uma análise contínua e complexa.

O Banco de Dados SQL do Azure usa inteligência interna e regras avançadas para analisar suas consultas, identifique os índices que seriam ideais para suas cargas de trabalho atuais e os índices que podem ser removidos. O Banco de Dados SQL do Azure garante que você tenha um conjunto mínimo necessário de índices que otimizam as consultas de leitura de dados, com o menor impacto sobre as outras consultas.

### <a name="how-to-identify-indexes-that-need-to-be-changed-in-your-database"></a>Como identificar os índices que precisam ser alterados no seu banco de dados?

O Banco de Dados SQL do Azure facilita o processo de gerenciamento de índice. Em vez do processo tedioso de análise e monitoramento manuais da carga de trabalho de índice, o Banco de Dados SQL do Azure analisa sua carga de trabalho, identifica as consultas que podem ser executadas mais rapidamente com um novo índice e identifica os índices duplicados ou não utilizados. Encontre mais informações sobre a identificação de índices que devem ser alterados em [Encontrar recomendações de índice no portal do Azure](sql-database-advisor-portal.md).

### <a name="automatic-index-management-considerations"></a>Considerações sobre o gerenciamento de índice automático

Se você achar que as regras internas melhoram o desempenho do seu banco de dados, você pode permitir que Banco de Dados SQL do Azure gerenciar automaticamente seus índices.

As ações necessárias para criar índices necessários em Bancos de Dados SQL do Azure podem consumir recursos e afetar o desempenho da carga de trabalho temporariamente. Para minimizar o impacto da criação de índice no desempenho da carga de trabalho, o Banco de Dados SQL do Azure localiza a janela de tempo apropriada para qualquer operação de gerenciamento de índice. A ação de ajuste é adiada se o banco de dados precisa de recursos para executar sua carga de trabalho e iniciada quando o banco de dados tem uma quantidade suficiente de recursos não utilizados que podem ser usados para a tarefa de manutenção. Um recurso importante no gerenciamento de índice automático é a verificação das ações. Quando o Banco de Dados SQL do Azure cria ou descarta o índice, um processo de monitoramento analisa o desempenho da carga de trabalho para verificar se a ação melhorou o desempenho. Se essa ação não gerar uma melhoria significativa ela será revertida imediatamente. Dessa forma, o Banco de Dados SQL do Azure garante que as ações automáticas não afetam negativamente o desempenho da carga de trabalho. Os índices criados com o ajuste automático são transparentes para a operação de manutenção no esquema subjacente. Alterações de esquema como remover ou renomear colunas não são bloqueadas pela presença de índices criados automaticamente. Os índices criados automaticamente pelo Banco de Dados SQL do Azure são imediatamente descartados quando a tabela ou colunas relacionadas são descartadas.

## <a name="automatic-plan-choice-correction"></a>Correção automática de escolha do plano

A correção automática de plano é um novo recurso de ajuste automático adicionado no SQL Server 2017 CTP 2.0 que identifica os planos de SQL inadequados. Essa opção de ajuste automático estará disponível em breve no Banco de Dados SQL do Azure. Veja mais informações em [Ajuste automático no SQL Server 2017](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="next-steps"></a>Próximas etapas

- Para habilitar o ajuste automático no Banco de Dados SQL do Azure e permitir que o recurso de ajuste automático gerencie a carga de trabalho por completo, consulte [Habilitar o ajuste automático](sql-database-automatic-tuning-enable.md).
- Para usar o ajuste manual, examine as [Recomendações de ajuste no portal do Azure](sql-database-advisor-portal.md) e aplique manualmente aquelas que melhoram o desempenho de suas consultas.

