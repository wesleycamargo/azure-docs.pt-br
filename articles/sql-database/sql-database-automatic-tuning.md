---
title: "Banco de Dados SQL do Microsoft Azure - ajuste automático | Microsoft Docs"
description: "O Banco de Dados SQL do Microsoft Azure analisa a consulta SQL e automaticamente se adapta à carga de trabalho do usuário."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: drasumic
editor: danimir
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 11/08/2017
ms.author: jovanpop
ms.openlocfilehash: 50d1548637ecc6862f680f73e333e8d7f8d20e39
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Ajuste automático no Banco de Dados SQL do Microsoft Azure

Ajuste automático do Banco de Dados SQL do Azure oferece um ótimo desempenho e cargas de trabalho estáveis por meio do ajuste contínuo de desempenho utilizando Inteligência Artificial.

Ajuste automático é um serviço totalmente gerenciado que usa inteligência interna para monitorar continuamente consultas executadas em um banco de dados e melhora automaticamente o desempenho. Isso é obtido adaptando dinamicamente o banco de dados à mudança das cargas de trabalho e aplicando recomendações de ajuste. O ajuste automático aprende horizontalmente com todos os bancos de dados no Azure por meio de Inteligência Artificial e melhora de modo dinâmico suas ações de ajustes. Quanto mais longa é a execução do Banco de Dados SQL do Azure com o ajuste automático ligado, melhor se torna seu desempenho.

O ajuste Automático de Banco de Dados SQL do Azure pode ser um dos recursos mais importantes que você pode habilitar para fornecer cargas de trabalho de desempenho de pico e estáveis.

## <a name="what-can-automatic-tuning-do-for-you"></a>O que o Ajuste Automático pode fazer por você?

- Ajuste de desempenho automatizado de Bancos de Dados SQL do Azure
- Verificação automatizada de ganhos de desempenho
- Autocorreção e reversão automática
- Log de histórico de ajuste
- Ajuste dos scripts do T-SQL de ação para implantações manuais
- Monitoramento do desempenho de carga de trabalho proativa
- Capacidade de expansão em centenas de milhares de bancos de dados
- Impacto positivo nos recursos de DevOps e no custo total de propriedade

## <a name="safe-reliable-and-proven"></a>Seguro, confiável e comprovado

Operações de ajuste aplicadas a Bancos de Dados SQL do Azure são totalmente seguras para o desempenho das cargas de trabalho mais intensas. O sistema foi projetado com cuidado para não interferir nas cargas de trabalho do usuário. Recomendações de ajuste automatizadas são aplicadas somente nos horários de pouca utilização. O sistema também pode desabilitar temporariamente as operações de ajuste automático para proteger o desempenho da carga de trabalho. Nesse caso, a mensagem "Desabilitado pelo sistema" será mostrada no portal do Azure. Ajuste automático considera cargas de trabalho com a prioridade mais alta de recurso.

Mecanismos de ajuste automático são desenvolvidos e foram aperfeiçoados em centenas de milhares de bancos de dados em execução no Azure. As operações de ajuste automatizado aplicadas são verificadas automaticamente para garantir que exista uma melhoria no desempenho da carga de trabalho. Recomendações de desempenho retornadas são detectadas dinamicamente e revertidas no mesmo momento. Por meio do log de histórico de ajuste, há um rastreamento claro das melhorias de ajuste feitas em cada Banco de Dados SQL do Azure. 

![Como funciona o trabalho de ajuste automático](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

O ajuste Automático do Banco de Dados SQL do Azure está compartilhando sua lógica principal com o mecanismo de ajuste automático do SQL Server. Para obter informações técnicas adicionais sobre o mecanismo interno de inteligência, consulte [Ajuste automático do SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).

## <a name="use-automatic-tuning"></a>Usar ajuste automático

O ajuste automático deve ser habilitado manualmente em sua assinatura. Para habilitar o ajuste automático usando o portal do Azure, consulte [Habilitar ajuste automático](sql-database-automatic-tuning-enable.md).

O ajuste automático pode funcionar de maneira autônoma aplicando automaticamente as recomendações de ajuste, incluindo a verificação automatizada de ganhos de desempenho. 

Para obter mais controle, a aplicação automática de recomendações de ajuste pode ser desativada e recomendações de ajuste podem ser aplicadas manualmente por meio do portal do Azure. Também é possível usar a solução para exibir somente recomendações de ajuste automatizadas e aplicá-las manualmente por meio de scripts e ferramentas de sua escolha. 

Para uma visão geral de como o ajuste automático funciona em cenários de uso típicos, assista ao vídeo inserido:


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>Opções de ajuste automático

As opções de ajuste automático disponíveis no Banco de Dados SQL do Azure são:
 1. **CREATE INDEX** que identifica os índices que podem melhorar o desempenho da carga de trabalho, cria os índices e verifica que eles melhoram o desempenho das consultas.
 2. **DROP INDEX** que identifica os índices duplicados e redundantes e índices que não foram usados em um longo período de tempo.
 3. **FORCE LAST GOOD PLAN** que identifica as consultas SQL que estão usando o plano de execução que são mais lentas do que o plano bom anterior e usa o último plano bom conhecido, em vez do plano retornado.

O Banco de Dados SQL do Microsoft Azure identifica recomendações **CREATE INDEX**, **DROP INDEX** e **FORCE LAST GOOD PLAN** que podem otimizar seu banco de dados e as mostra no Portal do Azure. Encontre mais informações sobre a identificação de índices que devem ser alterados em [Encontrar recomendações de índice no portal do Azure](sql-database-advisor-portal.md). Você pode aplicar manualmente as recomendações usando o portal ou permitir que o Banco de Dados SQL do Microsoft Azure aplique automaticamente as recomendações, monitore a carga de trabalho após a alteração e verifique se a recomendação melhorou o desempenho da carga de trabalho.

As opções de ajuste automático podem ser ativadas ou desativadas de forma independente por banco de dados, ou podem ser configuradas no servidor lógico e aplicadas em cada banco de dados que herda as configurações do servidor. Configurar as opções de Ajuste automático no servidor e herdar as configurações nos bancos de dados no servidor é um método recomendável para configurar o ajuste automático, pois simplifica o gerenciamento das opções de ajuste automático em um grande número de bancos de dados.

## <a name="next-steps"></a>Próximas etapas

- Para habilitar o ajuste automático no Banco de Dados SQL do Azure para gerenciar a carga de trabalho, consulte [Habilitar ajuste automático](sql-database-automatic-tuning-enable.md).
- Para examinar e aplicar recomendações de Ajuste automático manualmente, consulte [Localizar e aplicar recomendações de desempenho](sql-database-advisor-portal.md).
- Para saber mais sobre inteligência interna usada no Ajuste automático, consulte [Inteligência artificial ajusta os Bancos de Dados SQL do Azure](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Para saber mais sobre como o Ajuste automático funciona no Banco de Dados SQL do Azure e no SQL Server de 2017, consulte [Ajuste automático do SQL Server](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning).
