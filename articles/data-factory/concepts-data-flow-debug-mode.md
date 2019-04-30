---
title: Modo de Depuração do Fluxo de Dados do Mapeamento do Azure Data Factory
description: Iniciar uma sessão de depuração interativa ao construir fluxos de dados
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 1a332dd46cac196c8185ddb12c0d900f5c36e1b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270727"
---
# <a name="mapping-data-flow-debug-mode"></a>Modo de Depuração do Fluxo de Dados de Mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Azure Data Factory de mapeamento de fluxo de dados tem um modo de depuração, que pode ser ativado com o botão de fluxo de dados de depuração na parte superior da superfície de design. Ao projetar fluxos de dados, a configuração do modo de depuração permite que você visualize de forma interativa a transformação da forma de dados enquanto constrói e depura seus fluxos de dados. A sessão de depuração pode ser usada tanto em sessões de design de fluxo de dados, bem como durante a execução do pipeline depuração de fluxos de dados.

![Botão de depuração](media/data-flow/debugbutton.png "no botão depurar")

## <a name="overview"></a>Visão geral
Quando o modo de depuração está ativado, você irá criar interativamente o fluxo de dados com um cluster Spark do Active Directory. A sessão fecha quando você desativa a depuração no Azure Data Factory. Você deve estar ciente das cobranças por hora incorridas pelo Azure Databricks durante o tempo em que a sessão de depuração está ativa.

Na maioria dos casos, recomenda-se criar seus Fluxos de Dados no modo de depuração para que você possa validar a lógica de negócios e exibir as transformações de dados antes de publicar seu trabalho no Azure Data Factory. Você também deve usar o botão de "Debug" no painel de pipeline para testar seu fluxo de dados dentro de um pipeline.

## <a name="debug-mode-on"></a>Modo de depuração
Ao ativar o modo de depuração, você recebe um formulário de painel lateral que solicita que você aponte para o seu cluster interativo do Azure Databricks e selecione opções para a amostragem de fonte. Você deve usar um cluster interativo do Azure Databricks e selecionar um tamanho de amostragem de cada uma das suas transformações de Fonte, ou escolher um arquivo de texto a ser usado para os dados de teste.

<img src="media/data-flow/upload.png" width="400">

> [!NOTE]
>Ao executar no Modo de Depuração no Fluxo de Dados, seus dados não são gravados na transformação de Coletor. Uma sessão de depuração destina-se a servir como um agente de teste para suas transformações. Os coletores não são necessários durante a depuração e são ignorados no fluxo de dados. Se você deseja testar a gravação dos dados em seu Coletor, execute o Fluxo de Dados de um Pipeline do Azure Data Factory e use a execução da Depuração de um pipeline.

## <a name="debug-settings"></a>Configurações de depuração
Configurações de depuração pode ser aparecerá no painel lateral de cada fonte de fluxo de dados e também podem ser editada selecionando "configurações da fonte" na barra de designer de fluxo de dados. Você pode selecionar os limites e/ou a fonte do arquivo a usar para cada transformação de Fonte aqui. Os limites de linha nesta configuração são apenas para a sessão de depuração atual. Você também pode usar a configuração de amostragem na fonte para limitar as linhas em transforamtion o código-fonte.

## <a name="cluster-status"></a>Status do cluster
Há um indicador de status de cluster na parte superior da superfície de design que fica verde quando o cluster está pronto para depuração. Se o seu cluster já está em estado passivo, o indicador verde aparece quase que instantaneamente. Se o cluster ainda não estava em execução quando você entrou no modo de depuração, é preciso aguardar de 5 a 7 minutos para que o cluster fique ativo. A luz indicadora fica amarela quando ele está pronto. Quando o cluster está pronto para a depuração do fluxo de dados, a luz indicadora fica verde.

Após a conclusão da depuração, desative a Depuração para que o cluster do Azure Databricks possa ser encerrado.

<img src="media/data-flow/datapreview.png" width="400">

## <a name="data-preview"></a>Visualização de dados
Com a depuração ativa, a guia Visualização dos Dados fica destacada no painel inferior. Sem o modo de depuração ativo, o Fluxo de Dados mostra apenas os metadados atuais dentro e fora de cada uma de suas transformações na guia Inspecionar. A visualização de dados só consultará o número de linhas que você definiu como seu limite em suas configurações de depuração. Talvez seja necessário clicar em "Buscar dados" para atualizar a visualização dos dados.

<img src="media/data-flow/stats.png" width="400">

## <a name="data-profiles"></a>Perfis de dados
A seleção de colunas individuais na guia de visualização de dados exibe um gráfico na extrema direita de sua grade de dados com estatísticas detalhadas sobre cada campo. O Azure Data Factory faz uma determinação com base na amostragem de dados do tipo de gráfico a ser exibido. Os campos de cardinalidade alta são padronizados para os gráficos NULO/NÃO NULO, enquanto os dados categóricos e numéricos com baixa cardinalidade exibem gráficos de barras mostrando a frequência do valor dos dados. Você também vê o comprimento mín./máx. dos campos de cadeias de caracteres, valores mín./máx. dos campos numéricos, desvio padrão, percentis, contagens e média. 

<img src="media/data-flow/chart.png" width="400">

## <a name="next-steps"></a>Próximas etapas

Quando você estiver compilando concluído e depurando o fluxo de dados, [executá-lo em um pipeline.](control-flow-execute-data-flow-activity.md)

Ao testar seu pipeline com um fluxo de dados, usar o pipeline [depuração executa a opção de execução.](iterative-development-debugging.md)
