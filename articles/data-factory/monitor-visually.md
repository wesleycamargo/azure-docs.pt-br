---
title: Monitorar visualmente as data factories do Azure | Microsoft Docs
description: Saiba como monitorar visualmente data factories do Azure
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: shlo
ms.openlocfilehash: 76070b5a9944b4cbb47ad337ba9a4e6171bf12a3
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="visually-monitor-azure-data-factories"></a>Monitorar visualmente data factories do Azure
O Azure Data Factory é um serviço de integração de dados baseado em nuvem que permite que você crie fluxos de trabalho controlados por dados na nuvem para orquestrar e automatizar a movimentação e a transformação de dados. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que podem ingerir dados de repositórios de dados diferentes, processar/transformr os dados usando serviços de computação como o Hadoop do Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e publicar os dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de business intelligence (BI).
Neste início rápido, você aprenderá a monitorar visualmente pipelines do data factory v2 sem escrever uma única linha de código.
Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você usar a versão 1 do serviço do Data Factory, que está em GA (disponibilidade geral), consulte [Monitorar e gerenciar pipelines no Data Factory versão 1](v1/data-factory-monitor-manage-app.md).

## <a name="monitor-data-factory-v2-pipelines"></a>Monitorar pipelines de data factory v2

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Navegue até a folha do data factory criada no portal do Azure e clique no bloco 'Monitorar e Gerenciar'. Isso inicializará a experiência de monitoramento visual do ADF v2.

## <a name="list-view-monitoring"></a>Monitoramento de exibição de lista

Monitore execuções de atividade e pipeline com uma simples interface de exibição de lista. Todas as execuções são exibidas no fuso horário local do navegador. Você pode alterar o fuso horário e todos os campos de data e hora serão encaixados no fuso horário selecionado.  

#### <a name="monitoring-pipeline-runs"></a>Monitorando execuções de pipeline
Exibição de lista mostrando cada execução de pipeline para seus pipelines do data factory v2. Colunas incluídas:

| **Nome da Coluna** | **Descrição** |
| --- | --- |
| Nome do Pipeline | Nome do pipeline. |
| Ações | Ação única disponível para exibir execuções de atividade. |
| Início da Execução | Data e hora de início da execução do pipeline (MM/DD/AAAA HH:MM:SS AM/PM) |
| Duration | Duração da execução (HH:MM:SS) |
| Disparado por | Gatilho manual, Gatilho de agenda |
| Status | Falha, Sucesso, Em Andamento |
| parâmetros | Parâmetros de execução de pipeline (pares de nome, valor) |
| Erro | Erro de execução de pipeline (if/any) |
| ID da execução | ID da execução de pipeline |

![Monitorar execuções de pipeline](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>Monitorando execuções de atividade
Exibição de lista mostrando execuções de atividade correspondentes a cada execução de pipeline. Clique no ícone **'Execuções de Atividade'** na coluna **'Ações'** para exibir execuções de atividade para cada execução de pipeline. Colunas incluídas:

| **Nome da Coluna** | **Descrição** |
| --- | --- |
| Nome da atividade | Nome da atividade no pipeline. |
| Tipo de atividade | Tipo de atividade, ou seja, o Copy, HDInsightSpark, HDInsightHive etc. |
| Início da Execução | Data hora de início de execução da atividade (MM/DD/AAAA HH: MM: SS AM/PM) |
| Duration | Duração da execução (HH:MM:SS) |
| Status | Falha, Sucesso, Em Andamento |
| Entrada | Matriz JSON que descreve as entradas de atividade |
| Saída | Matriz JSON que descreve as saídas de atividade |
| Erro | Erro de execução da atividade (if/any) |

![Monitorar execuções de atividade](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> Você precisará clicar no ícone **'Atualizar'** na parte superior para atualizar a lista de execuções do pipeline e atividades. No momento, não há suporte para atualização automática.
>

![Atualizar](media/monitor-visually/refresh.png)

## <a name="features"></a>Recursos

#### <a name="rich-ordering-and-filtering"></a>Classificação e filtragem avançadas

Ordene as execuções do pipeline em ordem decrescente/crescente com Início da Execução e filtre as execuções de pipeline pelas seguintes colunas:

| **Nome da Coluna** | **Descrição** |
| --- | --- |
| Nome do Pipeline | Nome do pipeline. As opções incluem filtros rápidos para 'últimas 24 horas', 'Última semana', 'Últimos 30 dias' ou selecione uma data e hora personalizadas. |
| Início da Execução | Data hora de início da execução do pipeline |
| Status da execução | O filtro é executado por status, ou seja, Sucesso, Falha, Em andamento |

![Filter](media/monitor-visually/filter.png)

#### <a name="addremove-columns-to-list-view"></a>Adicionar/remover colunas para exibição de lista
Clique com o botão direito do mouse no cabeçalho da exibição de lista e escolha as colunas que você deseja que apareçam na exibição de lista

![Colunas](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>Reordene as larguras de colunas na exibição de lista
Aumente e diminua as larguras das colunas na exibição de lista simplesmente passando o cursor do mouse sobre o cabeçalho da coluna

#### <a name="select-data-factory"></a>Selecionar o data factory
Passe o cursor do mouse sobre o ícone 'Data Factory' no canto superior esquerdo. Clique no ícone de 'Seta' para ver uma lista de assinaturas e data factories do Azure que você pode monitorar.

![Selecionar o data factory](media/monitor-visually/select-datafactory.png)

#### <a name="guided-tours"></a>Passeios Guiados
Clique em 'Ícone Informações' no canto inferior esquerdo e clique em 'Passeios Guiados' para obter instruções passo a passo sobre como monitorar as execuções de atividade e o pipeline.

![Passeios guiados](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>Comentários
Clique no ícone 'Comentários' para fazer comentários em vários recursos ou problemas que você possa estar enfrentando.

![Comentários](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>Próximas etapas

Consulte o artigo [Monitorar e gerenciar os pipelines programaticamente](https://docs.microsoft.com/azure/data-factory/monitor-programmatically) para saber mais sobre o monitoramento e o gerenciamento de pipelines
