---
title: "Recomendações de desempenho para o Banco de dados SQL do Azure | Microsoft Docs"
description: "O Advisor do Banco de Dados SQL do Azure fornece recomendações para seus Bancos de Dados SQL existentes que podem melhorar o desempenho de consulta atual."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 5df30f24a8e7370ac33c72641f05138776e24bb0
ms.openlocfilehash: 55502f346eaf5ccf76d6f1c03487e95f0b93e3f5


---
# <a name="sql-database-advisor"></a>Advisor do Banco de Dados SQL
> [!div class="op_single_selector"]
> * [Visão Geral do Advisor do Banco de Dados SQL](sql-database-advisor.md)
> * [Portal](sql-database-advisor-portal.md)
> 
> 

O Banco de Dados SQL do Azure aprende e adapta-se ao seu aplicativo e fornece recomendações personalizadas permitindo que você maximize o desempenho dos bancos de dados SQL. O Assistente do Banco de Dados SQL fornece recomendações para criar e descartar índices, parametrizar consultas e corrigir problemas de esquema. O assistente avalia o desempenho ao analisar seu o histórico de uso do banco de dados SQL. As recomendações mais adequadas para executar a carga de trabalho normal do seu banco de dados são recomendadas. 

As recomendações a seguir estão disponíveis para servidores V12 (as recomendações não estão disponíveis para servidores V11). Atualmente, você pode definir as recomendações de criação e remoção de índice para que elas sejam aplicadas automaticamente. Consulte [Gerenciamento automático de índices](sql-database-advisor-portal.md#enable-automatic-index-management) para ver mais detalhes.

## <a name="create-index-recommendations"></a>Criar recomendações de índice
**Criar Índice** aparecem quando o serviço de Banco de Dados SQL detecta um índice ausente que, se criado, pode beneficiar sua carga de trabalho de bancos de dados (somente índices não clusterizados).

## <a name="drop-index-recommendations"></a>Recomendações para Remover Índice
**Remover Índice** aparecem quando o serviço de Banco de Dados SQL detecta índices duplicados (atualmente em versão de visualização e aplica-se somente a índices duplicados).

## <a name="parameterize-queries-recommendations"></a>Recomendações para parametrizar consultas
Recomendações para **Parametrizar consultas** aparecem quando o serviço de Banco de Dados SQL detecta que você tem uma ou mais consultas que estão constantemente sendo recompiladas, mas terminam com o mesmo plano de execução de consulta. Essa condição abre uma oportunidade de aplicar parametrização forçada, o que permitirá que os planos de consulta sejam armazenados em cache e reutilizados no futuro, melhorando o desempenho e reduzindo o uso de recursos. 

Cada consulta emitida para o servidor SQL inicialmente precisa ser compilada para gerar um plano de execução. Cada plano gerado é adicionado ao cache do plano e as execuções subsequentes da mesma consulta poderão reutilizar esse plano pelo cache, eliminando a necessidade de compilações adicionais. 

Aplicativos que enviam consultas, que incluem valores sem parâmetros, podem causar uma sobrecarga de desempenho, no qual o plano de execução é compilado novamente para cada consulta com valores de parâmetros diferentes. Em muitos casos, as mesmas consultas com valores de parâmetros diferentes geram os mesmos planos de execução, mas esses planos são adicionados separadamente ao cache do plano. Os planos de execução de recompilação usam recursos do banco de dados, aumentam o tempo de duração da consulta e sobrecarregam o cache do plano, fazendo com que planos sejam removidos do cache. Esse comportamento do servidor SQL pode ser alterado ao definir a opção de parametrização forçada no banco de dados. 

Para ajudá-lo a estimar o impacto dessa recomendação, você receberá uma comparação entre o uso real da CPU e o uso projetado da CPU (como se a recomendação tivesse sido aplicada). Além poupar a CPU, a duração da consulta diminui pelo tempo gasto na compilação. A sobrecarga no cache do plano também será significativamente reduzida, permitindo que a maioria dos planos permaneça no cache e seja reutilizada. Você pode aplicar essa recomendação com rapidez e facilidade clicando no comando **Aplicar**. 

Depois de aplicar essa recomendação, ela habilitará a parametrização forçada em seu banco de dados em minutos e iniciará o processo de monitoramento, que dura aproximadamente 24 horas. Após esse período, você poderá ver o relatório de validação que mostra o uso da CPU do seu banco de dados 24 horas antes e depois da aplicação da recomendação. O Assistente do Banco de Dados SQL tem um mecanismo de segurança que reverte a recomendação aplicada automaticamente caso uma redução de desempenho seja detectada.

## <a name="fix-schema-issues-recommendations"></a>Recomendações para corrigir problemas do esquema
Recomendações para **Corrigir problemas do esquema** aparecem quando o serviço de Banco de Dados SQL observa uma anomalia no número erros de SQL relacionados ao esquema ocorrendo no Banco de Dados SQL do Azure. Essa recomendação normalmente aparece quando seu banco de dados encontra vários erros relacionados ao esquema (nome da coluna inválido, nome do objeto inválido etc.) no período de uma hora.

"Problemas de esquema" são uma classe de erros de sintaxe no servidor SQL que ocorrem quando a definição da consulta SQL e a definição do esquema do banco de dados não estão alinhadas. Por exemplo, uma das colunas esperadas pela consulta pode estar ausente na tabela de destino ou vice-versa. 

Recomendações para “Corrigir problemas do esquema” aparecem quando o serviço de Banco de Dados SQL do Azure observa uma anomalia no número erros de SQL relacionados ao esquema ocorrendo no Banco de Dados SQL do Azure. A tabela a seguir mostra os erros relacionados a problemas de esquema:

| Código do Erro SQL | Mensagem |
| --- | --- |
| 201 |Procedimento ou função '*' espera o parâmetro '*', que não foi fornecido. |
| 207 |Nome de coluna inválido '*'. |
| 208 |Nome de objeto inválido '*'. |
| 213 |O nome da coluna ou o número de valores fornecidos não corresponde à definição da tabela. |
| 2812 |Não foi possível encontrar o procedimento armazenado '*'. |
| 8144 |Função ou procedimento * tem muitos argumentos especificados. |

## <a name="next-steps"></a>Próximas etapas
Monitore suas recomendações e continue a aplicá-las para refinar o desempenho. Cargas de trabalho de banco de dados são dinâmicas e mudam continuamente. O assistente do Banco de Dados SQL continua a monitorar e fornecer recomendações que podem potencialmente melhorar o desempenho do seu banco de dados. 

* Consulte [Advisor do Banco de Dados SQL no portal do Azure](sql-database-advisor-portal.md) para obter etapas para usar esse recurso no portal do Azure.
* Consulte [Análise de desempenho de consultas](sql-database-query-performance.md) para saber mais e visualizar o impacto no desempenho de suas principais consultas.

## <a name="additional-resources"></a>Recursos adicionais
* [Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Controle de acesso baseado em função](../active-directory/role-based-access-control-configure.md)




<!--HONumber=Jan17_HO4-->


