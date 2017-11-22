---
title: "O que é o Azure Time Series Insights? | Microsoft Docs"
description: "Introdução à Azure Time Series Insights, um novo serviço para soluções de IoT e de análises de dados de séries temporais."
services: time-series-insights
ms.service: time-series-insights
author: op-ravi
ms.author: omravi
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 95cb26ada6f8ea39bc1a437a755f80ee7ddb7698
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-azure-time-series-insights"></a>O que é o Azure Time Series Insights?

Time Series Insights é compilada para armazenar, visualizar e consultar grandes quantidades de dados de série temporal, como o que é gerado por dispositivos IoT.  Se você deseja armazenar, gerenciar, consultar ou visualizar os dados de série temporal na nuvem, Time Series Insights é provavelmente certa para você.  

Se você estiver compilando um aplicativo para consumo interno ou externos para uso dos clientes, Time Series Insights pode ser usada como um back-end para indexação, armazenamento e agregação de dados de série temporal.  Você pode compilar uma experiência de usuário e a visualização personalizada na parte superior.  Time Series Insights expõe as APIs REST de consulta para habilitar esse cenário.  

Se você não tiver certeza se seus dados são série temporal, aqui está o que você deve saber.  Dados de série temporal representam como um processo ou um ativo é alterado ao longo do tempo.  É exclusivo no sentido que ele tem um carimbo de data e hora e o time é mais significativa como um eixo.  Dados de série temporal normalmente chegam na ordem de tempo e geralmente são tratados como uma inserção em vez de uma atualização para o banco de dados.  Como Time Series Insights captura e armazena todos os novos eventos como uma linha, alteração é medida ao longo do tempo, permitindo que você pesquise para trás e para prever a alteração futura.  Em grandes volumes, armazenamento, indexação, consultas, análises e visualizações de dados de série temporal podem ser desafiadoras.  

## <a name="primary-scenarios"></a>Principais cenários

- Armazenando dados de série temporal de maneira escalonável.  
  - Essencialmente, Time Series Insights tem um banco de dados criado com dados de série temporal em mente.  Como ele é totalmente gerenciado e escalonável, Time Series Insights manipula o trabalho de armazenamento e gerenciamento de eventos.

- Próximo a exploração de dados em tempo real.  
  - Time Series Insights fornece um explorador que visualiza todos os dados em um ambiente de dados de streaming.  Logo após se conectar a uma fonte de evento, dados de evento podem ser exibidos, explorado e consultados em Time Series Insights.  Os dados são úteis para validar se um dispositivo estiver emitindo dados conforme esperado e monitorar um ativo de IoT para integridade, a produtividade e a eficiência geral.  

- Acelerar a detecção de anomalias e análise de causas raiz.
  - Time Series Insights tem ferramentas como padrões e as exibições de perspectiva para conduzir e salvar a análise da causa raiz de várias etapas.  Além disso, Time Series Insights funciona em conjunto com alertas de serviços, como o Azure Stream Analytics, então anomalias detectadas e alertas podem ser exibidos quase em tempo real no Gerenciador de Time Series Insights.  

- Uma exibição global dos dados de série temporal de streaming de dados de locais diferentes para comparação de vários asset/site.
  - Você pode se conectar a várias fontes de evento em um ambiente de Time Series Insights.  Isso significa que dados de streaming em vários locais diferentes podem ser exibidos juntos quase em tempo real.  Os usuários podem aproveitar essa visibilidade para compartilhar dados com líderes de negócios e para habilitar a colaboração melhor com os especialistas de domínio que podem aplicar seu conhecimento para ajudar a resolver problemas, aplicar as práticas recomendadas e compartilhar conhecimentos.

- Compilando um aplicativo cliente sobre Time Series Insights. 
  - Time Series Insights expõe as APIs REST de consulta, permitindo que você compile aplicativos que usam dados de série temporal.

## <a name="capabilities"></a>Funcionalidades

- **Fácil de iniciar**: o Azure Time Series Insights não exige nenhuma preparação de dados inicial e é inacreditavelmente rápido. Conecte-se a milhões de eventos em seu Hub IoT do Azure ou Hub de Eventos em minutos. Uma vez conectado, visualize e interaja com os dados do sensor para validar rapidamente suas soluções de IoT. Você pode interagir com os dados sem gravar código.
Não há nenhuma nova linguagem a aprender. O Time Series Insights fornece uma superfície de consulta de texto livre granular para usuários avançados, assim como a exploração do tipo apontar e clicar, para todos.
- **Análises em tempo quase real**: o Time Series Insights pode incluir milhões de eventos de sensor por dia, com latência de um minuto. O Time Series Insights ajuda você a realizar análises profundas em seus dados de sensor ajudando-o a identificar tendências e anomalias, realize facilmente análises de causas raiz e evitar um tempo de inatividade dispendioso. Ao habilitar a correlação cruzada entre dados de histórico e em tempo real, o Time Series Insights ajuda você a desbloquear tendências ocultas nos dados.
- **Crie soluções personalizadas**: Integre a Azure Time Series Insights aos seus aplicativos existentes ou crie novas soluções personalizadas com as APIs de consulta REST Time Series Insights. Criar e compartilhar exibições personalizadas que você pode compartilhar para que outras pessoas explorem suas descobertas.
- **Escalabilidade**: o Time Series Insights foi projetado para dar suporte à IoT em escala. É possível inserir de 1 milhão a 100 milhões de eventos por dia, com um período de tempo de retenção padrão de 31 dias. Você pode visualizar e analisar os fluxos de dados ativos quase em tempo real, juntamente com dados de histórico. Mais adiante, taxas de entrada e retenção aumentarão para acomodar uma escala empresarial em constante evolução.

## <a name="getting-started"></a>Introdução
Guia de Introdução leva menos de 5 minutos. 

1.  Para começar, forneça um novo ambiente de Time Series Insights no portal do Azure. 
2.  Conecte-se a uma fonte de evento como um Hub IoT do Azure ou Hub de eventos.  
3.  Carregar dados de referência (não é um serviço adicional).
4.  Ver seus dados em minutos com o Gerenciador de Time Series Insights.

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer
Este diagrama mostra um exemplo de série temporal de dados insights exibidos por meio do Pesquisador de objetos: ![Gerenciador de Time Series Insights] (media/time-series-insights-explorer/explorer4.png)


## <a name="next-steps"></a>Próximas etapas
 - [Explore usando o Time Series Insights Explorer em um ambiente de demonstração](./time-series-quickstart.md)
 - [Planeje o ambiente do Time Series Insights](time-series-insights-environment-planning.md)

