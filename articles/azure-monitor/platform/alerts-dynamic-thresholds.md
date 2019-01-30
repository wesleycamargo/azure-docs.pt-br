---
title: Criação de alertas com limites dinâmicos no Azure Monitor
description: Criar alertas com limites dinâmicos baseados em aprendizado de máquina
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 4024ecddde4b0d020e2c657214a4a258ea0b2ea5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449003"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor-public-preview"></a>Alertas de Métrica com Limites Dinâmicos no Azure Monitor (Versão Prévia Pública)

O Alerta de Métrica com detecção de Limites Dinâmicos aproveita ML (aprendizado de máquina) avançado para aprender o comportamento histórico das métricas e identificar padrões e anomalias que indicam problemas de serviço possíveis. Ele fornece suporte tanto de uma interface do usuário simples quanto de operações em escala permitindo aos usuários configurar regras de alerta por meio da API do Azure Resource Manager de maneira totalmente automatizada.

Depois que uma regra de alerta tiver sido acionada, ela será acionada somente quando a métrica monitorada não se comportar conforme o esperado com base em seus limites personalizados.

Adoraríamos receber seus comentários; envie-os para azurealertsfeedback@microsoft.com.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>Por que e quando é recomendado usar o tipo de condição dinâmica?

1. **Alertas Dimensionáveis** – regras de alerta de Limites Dinâmicos podem criar limites personalizados para centenas de séries de métricas por vez. Ainda assim, oferecendo a mesma facilidade da definição de uma regra de alerta em uma única métrica. Usando a interface do usuário ou os resultados da API do Azure Resource Manager em menos regras de alerta para gerenciar. A abordagem dimensionável é especialmente útil ao lidar com dimensões de métrica ou ao aplicar a vários recursos, como todos os recursos de assinatura. Isso se converte em uma economia de tempo significativa em gerenciamento e criação de regras de alertas. [Saiba mais sobre como configurar Alertas de Métrica com Limites Dinâmicos usando modelos](alerts-metric-create-templates.md).

1. **Reconhecimento Inteligente de Padrão de Métrica** – usando nossa exclusiva tecnologia de ML, podemos detectar padrões de métrica automaticamente e nos adaptar às mudanças de métricas ao longo do tempo, o que geralmente pode incluir a sazonalidade (horária/diária/semanal). Adaptar o comportamento das métricas ao longo do tempo e emitir alertas com base em desvios do padrão alivia a carga de conhecer o limite "certo" para cada métrica. O algoritmo de ML usado em limites dinâmicos foi projetado para evitar ruído (baixa precisão) ou limites amplos (baixo recall) que não têm um padrão esperado.

1. **Configuração intuitiva** – Limites Dinâmicos permitem a configuração de alertas de métrica usando conceitos de alto nível, diminuindo a necessidade de ter conhecimento de domínio abrangente sobre a métrica.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Como configurar alertas com Limites Dinâmicos?

Alertas com Limites Dinâmicos podem ser configurados por meio de Alertas de Métrica no Azure Monitor. [Saiba mais sobre como configurar Alertas de Métrica](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Como os limites são calculados?

O Limite Dinâmico aprende continuamente os dados da série de métrica e tenta modelá-los usando um conjunto de algoritmos e métodos. Ele detecta padrões nos dados, como periodicidade (por horária/diária/semanal) e é capaz de lidar com métricas ruidosas (como memória ou CPU do computador), bem como as métricas com baixa dispersão (como taxa de erro e disponibilidade).

Os limites são selecionados de modo que um desvio deles indique uma anomalia no comportamento da métrica.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>O que a configuração de 'Sensibilidade' em Limites Dinâmicos significa?

A sensibilidade do limite de alerta é um conceito de alto nível que controla a quantidade de desvio do comportamento da métrica necessária para disparar um alerta.
Essa opção não exige o conhecimento de domínio sobre a métrica, como limite estático. As opções disponíveis são:

- Alto – os limites serão rígidos e próximos do padrão da série da métrica. A regra de alerta será disparada ao menor desvio, resultando em mais alertas.
- Médio – limites menos rígidos e mais equilibrados, menos alertas que com alta sensibilidade (padrão).
- Baixo – os limites serão flexíveis, mais afastados do padrão da série de métrica. A regra de alerta só será disparada quando houver um desvio grande, resultando em menos alertas.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Quais são as opções de configuração de 'Operator' em Limites Dinâmicos?

A regra de alerta de Limites Dinâmicos pode criar limites personalizados com base no comportamento da métrica para os limites superior e inferior usando a mesma regra de alerta.
Você pode escolher o alerta a ser disparado em uma das três condições a seguir:

- Maior que o limite superior ou menor que o limite inferior (padrão)
- Maior que o limite superior
- Menor do que o limite inferior.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>O que as configurações avançadas em Limites Dinâmicos significam?

**Períodos de Falha** – Limites Dinâmicos também permitem que você configure o "Número de violações para disparar o alerta", um número mínimo de desvios necessários em uma determinada janela de tempo para o sistema gerar um alerta (a janela de tempo padrão é de quatro desvios em 20 minutos). O usuário pode configurar períodos de falha e escolher sobre o que ser alertado alterando os períodos de falha e a janela de tempo. Esse recurso reduz o ruído de alerta gerado por picos transitórios. Por exemplo: 

Para disparar um alerta quando o problema for contínuo por 20 minutos, 4 vezes consecutivas em um determinado período de agrupamento de 5 minutos, use as seguintes configurações:

![Configurações de períodos de falha para problema contínuo de 20 minutos, 4 vezes consecutivas em um determinado agrupamento de período de 5 minutos](media/alerts-dynamic-thresholds/0008.png)

Para disparar um alerta quando houver uma violação de um Limite Dinâmico em 20 minutos dos últimos 30 minutos com período de 5 minutos, use as seguintes configurações:

![Configurações de períodos de falha para problema para 20 minutos dos últimos 30 minutos com o agrupamento de período de 5 minutos](media/alerts-dynamic-thresholds/0009.png)

**Ignorar dados antes de** – os usuários também podem definir uma data de início a partida da qual o sistema deve começar a calcular os limites. Um caso de uso típico pode ocorrer quando um recurso estava em execução em um modo de teste e agora será promovido para atender a uma carga de trabalho de produção, portanto, o comportamento de qualquer métrica durante a fase de teste deve ser desconsiderado.

## <a name="will-slow-behavior-change-in-the-metric-trigger-an-alert"></a>Alteração de comportamento lenta na métrica disparará um alerta?

Provavelmente, não. Limites Dinâmicos são bons para detectar desvios significativos, não problemas que se desenvolvem lentamente.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Qual é a quantidade de dados usada para visualizar e então calcular os limites?

Os limites que aparecem no gráfico, antes de uma regra de alerta ser criada na métrica, são calculados com base nos últimos 10 dias de dados históricos; quando uma regra de alerta é criada, os Limites Dinâmicos vão adquirir dados históricos adicionais disponíveis e continuarão aprendendo com base nos novos dados para tornar os limites mais precisos.
