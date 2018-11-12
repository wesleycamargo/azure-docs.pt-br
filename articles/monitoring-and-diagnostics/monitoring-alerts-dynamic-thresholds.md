---
title: Criação de alertas com limites dinâmicos no Azure Monitor
description: Criar alertas com limites dinâmicos baseados em aprendizado de máquina
author: antonfrMSFT
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: mbullwin
ms.reviewer: antonfr
ms.component: alerts
ms.openlocfilehash: 1b47e3804d8be36e3c6c8c570fec06f542e8dbf2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233959"
---
# <a name="alerts-with-dynamic-thresholds-in-azure-monitor-limited-public-preview"></a>Alertas com limites dinâmicos no Azure Monitor (visualização pública limitada)

Alertas com limites dinâmicos são um aprimoramento dos alertas de métrica do Azure no Azure Monitor, que aproveitam recursos avançados de aprendizado de máquina (ML) para aprender o comportamento do histórico de métricas para calcular automaticamente as linhas de base e usá-las como limites de alerta.

Os benefícios de usar limites dinâmicos são:

- Salve as complicações associadas com a configuração de um limite rígido predefinido conforme o monitor aprende o desempenho histórico da métrica e aplica os algoritmos de ML para determinar limites de alerta.
- Eles só podem identificar comportamentos e alertas sazonais em desvios do comportamento sazonal esperado. Alertas de métricas com limites dinâmicos não vão ser disparados se o seu serviço estiver regularmente ocioso nos finais de semana e com picos todas as segundas-feiras. Com suporte no momento: sazonalidade por hora, diariamente e semanalmente.
- Aprendizado contínuo do desempenho de métrica e é adaptável às alterações de métrica.

Os alertas baseados em limites dinâmicos estão disponíveis em todas as fontes de métricas baseadas no Azure Monitor listadas neste [artigo](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts#what-resources-can-i-create-near-real-time-metric-alerts-for).

## <a name="sign-up-to-access-the-preview"></a>Inscreva-se para acessar a versão prévia

Para testar esse recurso, [inscreva-se para a versão prévia](https://aka.ms/DynamicThresholdMetricAlerts). Como sempre, adoraríamos receber seus comentários; envie-os para [azurealertsfeedback@microsoft.com](mailto:azurealertsfeedback@microsoft.com)

## <a name="how-to-configure-alerts-with-dynamic-thresholds"></a>Como configurar alertas com limites dinâmicos

Alertas com limites dinâmicos podem ser configurados por meio de alertas no Azure Monitor

![Versão prévia dos alertas](./media/monitoring-alerts-dynamic-thresholds/0001.png)

## <a name="creating-an-alert-rule-with-dynamic-thresholds"></a>Criar uma regra de alerta com limites dinâmicos

1. No painel de Alertas, em Monitor, selecione o botão **Nova Regra de Alerta** para criar um novo alerta no Azure.

   ![Nova regra de alerta](./media/monitoring-alerts-dynamic-thresholds/002.png)

2. A seção Criar regra é mostrada com as três partes que consistem em: _Definir condição de alerta_, _Definir detalhes do alerta_ e _Definir grupo de ação_. Comece com a seção _Definir condição de alerta_ e use o link **Selecionar destino** para especificar o destino selecionando um recurso. Depois que um recurso apropriado for escolhido, clique no botão Concluído.

   ![Selecionar o destino](./media/monitoring-alerts-dynamic-thresholds/0003.png)

3. Em seguida, use o botão **Adicionar critérios** para exibir uma lista de opções de sinais disponíveis para o recurso e, na lista de sinais, escolha uma opção de **métrica** apropriada. (Por exemplo, porcentagem de CPU.)

   ![Adicionar critérios](./media/monitoring-alerts-dynamic-thresholds/004.png)

4. Na seção Lógica de alerta da tela Configurar lógica de sinal, você tem a opção de alternar a condição a um tipo de Dinâmico, que gerará automaticamente os Limites dinâmicos (linhas vermelhas) junto com a métrica (linha azul).

   ![Dinâmico](./media/monitoring-alerts-dynamic-thresholds/005.png)

5. Os limites que aparecem no gráfico são calculados com base nos últimos sete dias de dados históricos; quando um alerta é criado, os Limites dinâmicos vão adquirir dados históricos adicionais disponíveis e continuarão aprendendo com base nos novos dados para tornar os limites mais precisos.

6. Configurações adicionais de Lógica de alerta:
   - Condição – você pode escolher o alerta a ser disparado em uma das três condições a seguir:
       - Maior que o limite superior ou menor que o limite inferior (padrão)
       - Maior que o limite superior
       - Menor do que o limite inferior.
   - Agregação de tempo: média (padrão), soma, mín., máx.
   - Sensibilidade do alerta:
       - Alto – mais alertas, uma vez que o alerta será disparado com o menor desvio.
       - Médio – menos sensível que o alto, menos alertas do que com alta sensibilidade (padrão)
       - Baixo – o limite menos sensível.

    ![Configurações da lógica de alerta](./media/monitoring-alerts-dynamic-thresholds/00007.png)

7. Avaliado com base em:
    -  Qual tempo de duração; o Alerta deve procurar a condição especificada escolhendo o **Período**.

    ![Avaliado com base em](./media/monitoring-alerts-dynamic-thresholds/007.png)

   > [!NOTE]
   > Valores de período com suporte: 5 minutos, 10 minutos, 30 minutos e 1 hora.

   Para reduzir o ruído de alerta gerado por picos transitórios, é recomendável usar as configurações de “Violações de número para disparar o alerta”. Essa funcionalidade permite que você só receba um alerta se o limite for violado X vezes consecutivas ou se Y atingir tempo limite dos último períodos de Z. Por exemplo: 

    Para disparar um alerta quando o problema for contínuo por 15 minutos, 3 vezes consecutivas em um determinado período de 5 minutos, use as seguintes configurações:

   ![Avaliado com base em](./media/monitoring-alerts-dynamic-thresholds/0008.png)

    Para disparar um alerta quando houver uma violação de um Limite dinâmico em 15 minutos dos últimos 30 minutos com período de 5 minutos, use as seguintes configurações:

   ![Avaliado com base em](./media/monitoring-alerts-dynamic-thresholds/0009.png)

8. No momento, os usuários podem ter alertas com critérios de limite dinâmico como um único critério.

   ![Criar regra](./media/monitoring-alerts-dynamic-thresholds/010.png)

## <a name="q--a"></a>Perguntas e respostas

- P: Se a métrica mudar lentamente ao longo do tempo, vai ser disparado um alerta com limites dinâmicos?

- R: Provavelmente não. Limites dinâmicos são bons para detectar desvios significativos, e não problemas que se desenvolvem lentamente.

- P: Posso configurar limites dinâmicos por meio de uma API?

- R: Estamos trabalhando nisso.
