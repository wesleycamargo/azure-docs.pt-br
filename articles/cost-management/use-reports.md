---
title: "Usar relatórios do Gerenciamento de Custos no Gerenciamento de Custos do Azure | Microsoft Docs"
description: "Este artigo descreve como usar vários relatórios do Gerenciamento de Custos no portal do Cloudyn."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/29/2017
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 92bb4f2a6458057613bdbcb749026781111a778d
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="use-cost-management-reports"></a>Usar relatórios do Gerenciamento de Custos

Este artigo descreve como usar vários relatórios do Gerenciamento de Custos no portal do Cloudyn. A maioria dos relatórios do Cloudyn é intuitiva e tem uma aparência uniforme. Para obter uma visão geral sobre os relatórios do Cloudyn, confira [Noções básicas sobre relatórios de custo](understading-cost-reports.md). O artigo também descreve várias opções e campos usados na maioria dos relatórios.

## <a name="cost-analysis-reports"></a>Relatórios de análise de custos

Os relatórios de análise de custo exibem dados de cobrança de seus provedores de nuvem. Usando os relatórios, você pode agrupar e analisar vários segmentos de dados especificados no arquivo de cobrança. Os relatórios permitem a navegação detalhada nos custos por todos os dados brutos de cobrança dos provedores de nuvem.

Os relatórios de análise de custos não agrupam os custos por marcas. Os relatórios baseados em marca só estão disponíveis no conjunto de relatórios de alocação de custos depois que você cria um modelo de custo usando o Alocador de Custos 360.

### <a name="actual-cost-analysis"></a>Análise de Custo Real

O relatório de análise de custo real mostra suas principais causas de custo, incluindo custos contínuos e tarifas avulsas.

 Use o relatório de análise de custo real para:

- Analisar e monitorar os custos reais gastos durante um período especificado
- Agendar um limite de alerta
- Analisar os custos de recuperação e estorno

#### <a name="to-use-the-actual-cost-analysis-report"></a>Para usar o relatório de análise de custo real

Execute, no mínimo, as etapas a seguir. Você também pode usar outras opções e campos.

1. Selecione um intervalo de datas.
2. Selecione um filtro.

Clique com botão direito do mouse nos resultados do relatório para analisá-los e exibir informações mais detalhadas.

![Exemplo de relatório de análise de custo real](./media/use-reports/actual-cost-analysis.png)

### <a name="actual-cost-over-time"></a>Custo real ao longo do tempo

O relatório de custo real ao longo do tempo é um relatório de análise de custo padrão que distribui os custos por um tempo definido. O relatório exibe os gastos ao longo do tempo para que você possa observar as tendências e detectar problemas nos gastos. Esse relatório mostra suas principais causas de custo, incluindo custos contínuos e tarifas avulsas reservadas gastos durante o período de tempo definido.

Use o relatório de custo real ao longo do tempo para:

- Consultar as tendências de custo ao longo do tempo.
- Localizar irregularidades nos custos.
- Localizar todas as questões relacionadas ao custo do Amazon Web Services.

#### <a name="to-use-the-actual-cost-over-time-report"></a>Para usar o relatório de custo real ao longo do tempo:

Execute, no mínimo, as etapas a seguir. Você também pode usar outras opções e campos.

- Selecione um intervalo de datas.

Por exemplo, você pode selecionar grupos para exibir os custos ao longo do tempo. Em seguida, adicione filtros para restringir os resultados.

![Exemplo de relatório de custo real ao longo do tempo](./media/use-reports/actual-cost-over-time.png)



### <a name="amortized-cost-reports"></a>Relatórios de custos amortizados

Esse conjunto de relatórios de custos amortizados mostram tarifas de serviço sem base no uso ou custos de pagamento único e distribui uniformemente os custos ao longo de sua vida útil.

Por exemplo, as tarifas avulsas podem incluir:

- Taxas de suporte anual
- Taxas anuais de componente de segurança
- Taxas de compra de instâncias reservadas
- Alguns itens do Azure Marketplace

No arquivo de cobrança, as tarifas avulsas são caracterizadas quando as datas de início e término do consumo do serviço ou carimbo de data/hora têm valores iguais. O Cloudyn reconhece as tarifas como episódios únicos que podem ser amortizados. Não é possível amortizar outros serviços baseados em consumo com custos de uso sob demanda.

Para ilustrar os custos amortizados, reveja a imagem de exemplo a seguir de um relatório de custo real ao longo do tempo. No exemplo, ele mostra um aumento de custo em 23 de agosto. Pode parecer uma anomalia em comparação com a tendência de custo diário normal. A análise da causa raiz e a navegação pelos dados identificaram esse custo como uma taxa de APN do serviço AWS, que é uma tarifa avulsa gerada e cobrada no mesmo dia. Você pode ver como esse custo é amortizado na próxima seção.

![Exemplo de relatório de custo real ao longo do tempo mostrando custos avulsos](./media/use-reports/actual-amort-example.png)

#### <a name="to-use-the-amortized-cost-over-time-report"></a>Para usar o relatório de custo amortizado ao longo do tempo:

Execute, no mínimo, as etapas a seguir. Você também pode usar outras opções e campos.

1. Selecione um intervalo de datas.
2. Selecione um serviço e um provedor.

Seguindo com o exemplo anterior, você pode ver que o custo único agora é amortizado na seguinte imagem:

![Exemplo de relatório de custo amortizado ao longo do tempo](./media/use-reports/amort-cost-over-time.png)

A imagem anterior mostra o custo amortizado da despesa com reserva de APN ao longo do tempo. Esse relatório mostra a amortização da tarifa avulsa e o custo de APN como compra de reserva anual. O custo de APN é distribuído uniformemente em uma base diária como 1/365º do custo inicial de reserva.

## <a name="cost-allocation-analysis-reports"></a>Relatórios de análise de alocação de custos

Os relatórios de análise de alocação de custos estão disponíveis depois que você cria um modelo de custo usando o Alocador de Custos 360. O Cloudyn processa os dados de custo/cobrança e corresponde os dados aos dados de uso e marca de suas contas na nuvem. Para corresponder aos dados, o Cloudyn requer acesso aos seus dados de uso. As contas sem credenciais são rotuladas como recursos não categorizados.

### <a name="cost-analysis-report"></a>Relatório de análise de custo

O relatório de análise de custo fornece informações sobre o consumo e gastos na nuvem durante um período de tempo selecionado. As políticas definidas no Gerenciador de Alocação de Custos são usadas no relatório de análise de custo.

Como o Cloudyn calcula esse relatório?

O Cloudyn faz com que a alocação mantenha a integridade de cada conta associada aplicando a afinidade de conta. A afinidade faz com que uma conta que não usa um serviço específico não tenha custos desse serviço alocados a ela. Os custos acumulados na conta permanecem nessa conta e não são calculados pelas políticas de alocação. Por exemplo, você pode ter cinco contas associadas. Se apenas três delas usam serviços de armazenamento, o custo dos serviços de armazenamento só é alocado entre marcas nas três contas.

 Use o relatório de análise de custo para:

- Ver uma exibição agregada da sua implantação completa em um período de tempo específico.
- Exibir os custos por categorias de marca com base em políticas criadas no modelo de custo.

#### <a name="to-use-the-cost-analysis-report"></a>Para usar o relatório de análise de custo:

1. Selecione um intervalo de datas.
2. Adicione marcas conforme a necessidade.
3. Adicione grupos.
4. Escolha um modelo de custo criado anteriormente.

A imagem a seguir mostra um exemplo de relatório de análise de custo em formato de explosão solar. Os anéis mostram grupos. O anel externo mostra o serviço e o círculo interno, a unidade.

![Exemplo de relatório de análise de custo](./media/use-reports/cost-analysis01.png)



Veja um exemplo das mesmas informações em uma exibição de tabela.

![Exemplo de relatório de análise de custo](./media/use-reports/cost-analysis02.png)



### <a name="cost-over-time-report"></a>Relatório de Custo ao Longo do Tempo

O relatório de custo ao longo do tempo exibe gastos ao longo do tempo para que você possa observar as tendências e detectar irregularidades em sua implantação. Essencialmente, ele mostra os custos distribuídos em um período definido. O relatório inclui suas principais causas de custo, incluindo custos contínuos e tarifas avulsas reservadas gastos durante o período de tempo definido. As políticas definidas no Gerenciador de Custos 360° podem ser usadas neste relatório.

Use o relatório ao longo do tempo para:

- Ver as alterações ao longo do tempo e quais influencias mudam de um dia (ou intervalo de datas) para outro.
- Analisar os custos ao longo do tempo de uma instância específica.
- Entender por que houve um aumento de custo em uma instância específica.

#### <a name="to-use-the-cost-over-time-report"></a>Para usar o relatório de custo ao longo do tempo:

1. Selecione um intervalo de datas.
2. Adicione marcas conforme a necessidade.
3. Adicione grupos.
4. Escolha um modelo de custo criado anteriormente.
5. Selecione os custos reais ou custos amortizados.
6. Escolha se deseja aplicar regras de alocação para exibir dados brutos de cobrança ou o custo recalculado na exibição do Cloudyn.

Veja um exemplo de relatório.

![Exemplo de custo ao longo do tempo](./media/use-reports/cost-over-time.png)



## <a name="next-steps"></a>Próximas etapas

- Se você ainda não concluiu o primeiro tutorial de Gerenciamento de Custos, leia-o em [Examinar o uso e os custos](tutorial-review-usage.md).
