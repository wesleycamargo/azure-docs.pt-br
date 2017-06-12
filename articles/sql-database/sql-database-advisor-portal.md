---
title: Assistente do Banco de Dados SQL do Azure usando o Portal do Azure | Microsoft Docs
description: "Você pode usar o Advisor do Banco de Dados SQL do Azure no Portal do Azure para analisar e implementar recomendações para seus Bancos de Dados SQL existentes que podem melhorar o desempenho de consulta atual."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: cda8a646-0584-4368-b28a-85cdd9b54fcd
ms.service: sql-database
ms.custom: monitor & manage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: f63bd47c15439e6fedc0f6edf579681697155592
ms.contentlocale: pt-br
ms.lasthandoff: 02/16/2017


---
# <a name="sql-database-advisor-using-the-azure-portal"></a>Assistente do Banco de Dados SQL usando o portal do Azure

Você pode usar o Advisor do Banco de Dados SQL do Azure no Portal do Azure para analisar e implementar recomendações para seus Bancos de Dados SQL existentes que podem melhorar o desempenho de consulta atual.

## <a name="viewing-recommendations"></a>Exibindo recomendações
A página de recomendações é onde você pode exibir as principais recomendações com base no seu impacto potencial para melhorar o desempenho. Você também pode exibir o status das operações do histórico. Selecione um status ou recomendação para ver seus detalhes.

Para exibir e aplicar as recomendações, você precisa das permissões corretas ao [controle de acesso baseado em função](../active-directory/role-based-access-control-configure.md) no Azure. As permissões de **Leitor**, **Contribuidor do DB SQL** são necessárias para exibir as recomendações, enquanto as permissões de **Proprietário**, **Contribuidor do DB SQL** são necessárias para executar todas as ações: criar ou remover índices e cancelar a criação de índices.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Mais serviços** > **Bancos de Dados SQL** e selecione seu banco de dados.
3. Clique em **Recomendação de desempenho** para exibir as recomendações disponíveis para o banco de dados selecionado.

> [!NOTE]
> Para obter recomendações, um banco de dados precisa ter aproximadamente um dia de uso e deve haver alguma atividade. Também é necessário haver certa atividade consistente. O Advisor do Banco de Dados SQL pode otimizar com maior facilidade padrões de consulta consistentes do que intermitências aleatórias e irregulares de atividade. Se não houver recomendações, a página **Recomendação de desempenho** deverá fornecer uma mensagem explicando o motivo.
> 
> 

![Recomendações](./media/sql-database-advisor-portal/recommendations.png)

Aqui está um exemplo de recomendação "Corrigir o problema do esquema" no Portal do Azure.

![Corrigir Problemas de Esquema](./media/sql-database-advisor-portal/sql-database-advisor-schema-issue.png)

As recomendações são classificadas de acordo com seu impacto em potencial no desempenho nas seguintes quatro categorias:

| Impacto | Descrição |
|:--- |:--- |
| Alto |Recomendações de alto impacto devem fornecer o impacto mais significativo no desempenho. |
| Média |Recomendações de médio impacto devem melhorar o desempenho, mas não substancialmente. |
| Baixo |Recomendações de baixo impacto devem fornecer um desempenho melhor do que seria obtido sem elas, mas as melhorias podem não ser significativas. |

### <a name="removing-recommendations-from-the-list"></a>Removendo recomendações da lista
Se sua lista de recomendações contiver itens que você deseja remover da lista, você poderá descartar a recomendação:

1. Selecione uma recomendação na lista de **Recomendações**.
2. Clique em **Descartar** na folha **Detalhes**.

Se desejar, você poderá adicionar itens descartados de volta para a lista de **Recomendações** :

1. Na folha **Recomendações**, clique em **Exibir descartados**.
2. Selecione um item descartado na lista para exibir seus detalhes.
3. Outra alternativa é clicar em **Desfazer Descarte** para adicionar o índice de volta à lista principal de **Recomendações**.

## <a name="applying-recommendations"></a>Aplicando recomendações
O Assistente do Banco de Dados SQL concede a você controle total sobre como as recomendações são habilitadas usando qualquer uma das três opções a seguir: 

* Aplicar uma recomendação individual de cada vez.
* Habilitar o Advisor a aplicar as recomendações automaticamente (atualmente, aplica-se somente às recomendações de índice).
* Para implementar uma recomendação manualmente, execute o script T-SQL recomendado no banco de dados.

Selecione qualquer recomendação para exibir seus detalhes e clique em **Exibir script** para examinar os detalhes exatos de como a recomendação é criada.

O banco de dados permanece online enquanto o assistente aplica a recomendação – o uso do Assistente do Banco de Dados SQL nunca colocará um banco de dados offline.

### <a name="apply-an-individual-recommendation"></a>Aplicar uma recomendação individual
Você pode examinar e aceitar uma recomendação de cada vez.

1. Na folha **Recomendações**, clique em uma recomendação.
2. Na folha **Detalhes**, clique em **Aplicar**.
   
    ![Aplicar recomendação](./media/sql-database-advisor-portal/apply.png)

### <a name="enable-automatic-index-management"></a>Habilitar o gerenciamento de índice automático
Você pode definir o Advisor do Banco de Dados SQL para implementar as recomendações automaticamente. Conforme as recomendações são disponibilizadas, elas serão aplicadas automaticamente. Como com todas as operações de índice gerenciadas pelo serviço, se o impacto de desempenho for negativo, a recomendação será revertida.

1. Na folha **Recomendações**, clique em **Automatizar**:
   
    ![Configurações do supervisor](./media/sql-database-advisor-portal/settings.png)
2. Configure o assistente para **Criar** ou **Remover** índices automaticamente:
   
    ![Índices recomendados](./media/sql-database-advisor-portal/automation.png)

### <a name="manually-run-the-recommended-t-sql-script"></a>Executar manualmente o script T-SQL recomendado
Selecione qualquer recomendação e clique em **Exibir script**. Execute este script em seu banco de dados para aplicar manualmente a recomendação.

*Os índices que são executados manualmente não são monitorados e validados quanto ao impacto no desempenho pelo serviço* , portanto, é recomendável que você monitore esses índices após a criação para verificar se eles fornecem ganhos de desempenho e ajustá-los ou excluí-los, se necessário. Para obter detalhes sobre a criação de índices, consulte [CRIAR ÍNDICE (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).

### <a name="canceling-recommendations"></a>Cancelando recomendações
Recomendações que estão com status **Pendente**, **Verificando** ou **Sucesso** podem ser canceladas. Recomendações com status **Executando** não podem ser canceladas.

1. Selecione uma recomendação na área **Histórico de Ajustes** para abrir a folha **detalhes da recomendação**.
2. Clique em **Cancelar** para anular o processo de aplicação da recomendação.

## <a name="monitoring-operations"></a>Monitoramento de operações
A aplicação de uma recomendação pode não acontecer instantaneamente. O portal fornece detalhes sobre o status das operações de recomendação. Um índice pode estar em um dos estados a seguir:

| Status | Descrição |
|:--- |:--- |
| Pendente |O comando Aplicar recomendação foi recebido e está programado para execução. |
| Executando |A recomendação está sendo aplicada. |
| Sucesso |A recomendação foi aplicada com êxito. |
| Erro |Ocorreu um erro durante o processo de aplicação da recomendação. Este pode ser um problema temporário ou, possivelmente, uma alteração de esquema na tabela, tornando o script inválido. |
| Revertendo |A recomendação foi aplicada, mas foi considerada não funcional e está sendo revertida automaticamente. |
| Revertida |A recomendação foi revertida. |

Clique em uma recomendação no processo da lista para ver mais detalhes:

![Índices recomendados](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Revertendo uma recomendação
Se você usou o supervisor para aplicar a recomendação (ou seja, você não executou o script T-SQL manualmente), ele a reverterá automaticamente se o impacto de desempenho for negativo. Se, por algum motivo, você simplesmente desejar reverter uma recomendação, poderá fazer o seguinte:

1. Selecione uma recomendação aplicada com êxito na área **Histórico de ajustes** .
2. Clique em **Reverter** na folha **detalhes da recomendação**.

![Índices recomendados](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitorando o impacto do desempenho de recomendações de índice
Depois que as recomendações forem implementadas com êxito (atualmente, apenas recomendações de operações de índice e de parametrização de consultas), clique em **Análise de Consultas** na folha detalhes da recomendação para abrir [Análise de Desempenho de Consultas](sql-database-query-performance.md) e ver o impacto no desempenho das principais consultas.

![Monitorar o impacto do desempenho](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Resumo
O Advisor do Banco de Dados SQL fornece recomendações para aprimorar o desempenho de bancos de dados SQL. Ao fornecer scripts T-SQL, além de individuais e totalmente automáticos (atualmente, somente de índice), o Advisor fornece assistência útil ao otimizar seus bancos de dados e, como resultado final, melhorar o desempenho de consulta.

## <a name="next-steps"></a>Próximas etapas
Monitore suas recomendações e continue a aplicá-las para refinar o desempenho. Cargas de trabalho de banco de dados são dinâmicas e mudam continuamente. O Advisor do Banco de Dados SQL continuará a monitorar e fornecer recomendações que podem potencialmente melhorar o desempenho do seu banco de dados. 

* Consulte [Advisor do Banco de Dados SQL](sql-database-advisor.md) para uma visão geral do Advisor do Banco de Dados SQL.
* Consulte [Análise de desempenho de consultas](sql-database-query-performance.md) para saber mais sobre como visualizar o impacto no desempenho de suas principais consultas.

## <a name="additional-resources"></a>Recursos adicionais
* [Repositório de Consultas](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Controle de acesso baseado em função](../active-directory/role-based-access-control-configure.md)


