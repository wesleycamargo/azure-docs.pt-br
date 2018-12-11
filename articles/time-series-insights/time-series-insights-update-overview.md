---
title: Visão geral do Azure Time Series Insights | Microsoft Docs
description: Visão geral do Azure Time Series Insights
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 12/03/2018
ms.openlocfilehash: 83513ab9f7bee5c51fc909e5f4246105b6f145ca
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876124"
---
# <a name="azure-time-series-insights-overview"></a>Visão geral do Azure Time Series Insights

O Azure Time Series Insights (TSI) é uma plataforma como serviço de ponta a ponta para ingerir, processar, armazenar e consultar dados em escala de IoT altamente contextualizados e otimizados para série temporal. Como tal, o Azure TSI é ideal para exploração de dados ad hoc e para análise operacional. O TSI é uma oferta de serviço exclusivamente extensível e personalizado que atende às amplas necessidades de implantações de IoT industriais.

## <a name="what-is-iot-data"></a>O que são dados de IoT?

Dados de IoT são qualquer dado “industrial” disponível em organizações com uso intensivo de ativos. Dados de IoT geralmente são altamente desestruturados, já que são enviados de uma ampla gama de ativos que registram medidas com uma quantidade razoável de ruído, como temperatura, movimento e umidade. Além disso, esses fluxos de dados são caracterizados frequentemente por grandes lacunas, mensagens corrompidas e leituras falsas. Os dados desses fluxos precisam ser limpos antes da análise. Os dados de IoT geralmente são relevantes apenas no contexto de entradas de dados adicionais provenientes de fontes próprias (como CRM ou ERP) ou terceirizadas (por exemplo, clima ou local).

Por conta disso, apenas uma fração insignificante desses dados é usada para fins operacionais e comerciais. Esses dados fornecem informações consistentes, abrangentes, atuais e corretas para análises e relatórios de negócios. Transformar dados de IoT coletados em informações acionáveis, portanto, requer alguns recursos importantes:

* Processamento de dados para limpar, filtrar, interpolar, transformar e preparar dados para análise
* Estrutura para navegar e compreender os dados (para normalizar e contextualizar os dados)
* Armazenamento econômico para retenção de longo prazo/indefinida de décadas de dados processados (derivados) e dados brutos

Um fluxo de dados de IoT típico pode ser descrito da seguinte maneira:

  ![Fluxo de dados de IoT][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights para IoT industrial

O panorama atual de IoT é variado. Ele inclui clientes que vão de fábricas à indústria automotiva, petróleo, energia e serviços essenciais, prédios inteligentes e consultoria. Os cenários incluem uma exploração de dados ad hoc em que a forma dos dados é desconhecida, bem como análise operacional sobre os dados esquematizados (explicitamente modelados) para aumentar a eficiência operacional. Esses cenários normalmente existem lado a lado e dão suporte a diferentes casos de uso. Recursos da plataforma, como armazenamento em várias camadas (warm e cold), capacidade de armazenar amostras de décadas de dados de série temporal e capacidade de modelar explicitamente e otimizar consultas para inteligência operacional baseada em ativos estão se tornando chave para o sucesso das empresas de IoT industriais e sua revolução digital.

O Azure TSI é uma oferta de plataforma como um serviço de ponta a ponta abrangente para exploração de dados de IoT e insights operacionais. O Time Series Insights oferece um serviço de nuvem totalmente gerenciado para analisar dados de séries temporais de IoT em escala.

Os clientes podem armazenar dados brutos em um repositório sem esquema na memória e executar consultas ad hoc interativas por meio de um mecanismo e uma API de consulta distribuída, e também aproveitar nossa experiência de usuário avançada para visualizar bilhões de eventos em segundos. Saiba mais sobre nossos [recursos de exploração de dados](./time-series-insights-overview.md).

O TSI também oferece recursos de insights operacionais atualmente em versão prévia pública. Junto com a exploração interativa de dados e a inteligência operacional, o Time Series Insights permite que os clientes obtenham mais valor dos dados coletados de ativos de IoT. Mais especificamente, a oferta de versão prévia pública dá suporte aos seguintes recursos principais:

* Um armazenamento de dados de série temporal escalonável e econômico para desempenho que permite que uma solução de IoT baseada em nuvem analise em segundos a tendência de dados de série temporal de vários anos.
* Suporte a modelo semântico para descrever o domínio e os metadados associados aos sinais derivados e não derivados de ativos e dispositivos.
* Uma experiência do usuário que combina insights de dados com base em ativos com análises de dados ad hoc avançadas para gerar inteligência operacional e de negócios
* Integração com ferramentas de aprendizado de máquina e análise, como o Azure Databricks, o Apache Spark, o Azure Machine Learning, os Jupyter Notebooks, o Power BI e outros para ajudar os clientes a lidar com os desafios dos dados de série temporal e aumentar a eficiência operacional.

Juntos, os insights operacionais e a exploração de dados são oferecidos com um modelo de preços simples de pagamento conforme o uso para processamento de dados, armazenamento e consulta, fornecendo aos clientes um modelo muito mais escalonável para atender às suas necessidades de negócios.

Abaixo está um fluxograma geral que descreve os recursos atualizados:

  ![Principais recursos][2]

Com a introdução desses recursos de IoT industrial essenciais, o Azure TSI fornece os principais benefícios a seguir.

* Armazenamento em várias camadas para dados de série temporal em escala de IoT

  * Com um pipeline de processamento de dados para ingerir dados, os clientes podem armazenar dados em armazenamento warm para consultas interativas e/ou em armazenamento cold para armazenar grandes volumes de dados e usufruir de consultas de alto desempenho baseadas em ativos.

  * O roteamento dinâmico entre camadas de armazenamento estará disponível em breve.

* O modelo de série temporal para contextualizar dados telemétricos brutos e derivar insights baseados em ativos

  * Os clientes podem contextualizar os dados brutos de telemetria com o modelo de série temporal descritivo e derivar inteligência operacional avançada com consultas de alto desempenho e otimização de custo baseadas em dispositivo.

* Integração perfeita com outras soluções de dados
  
  * Como os dados no Azure Time Series Insights são armazenados em arquivos do Apache Parquet de software livre, os clientes podem facilmente integrar com outras soluções de dados (próprias ou terceirizadas) para cenários de ponta a ponta, incluindo business intelligence, aprendizado de máquina avançado, análise preditiva e outros.

* Exploração de dados quase em tempo real

  * A experiência do usuário do explorer do Azure Time Series Insights fornece uma visualização de todos os dados de streaming por meio do pipeline de ingestão. Logo após conectarem-se a uma origem de evento, os clientes podem exibir, explorar e consultar dados de evento para validar se um dispositivo está emitindo dados conforme esperado e monitorar a integridade, a produtividade e a eficiência geral de um ativo de IoT.

* Análise de causa raiz e detecção de anomalias

  * O explorer do Azure Time Series Insights dá suporte a padrões e exibições de perspectiva para conduzir e salvar a análise de causa raiz em várias etapas. Em combinação com o Azure Stream Analytics, os clientes podem usar o Time Series Insights para detectar alertas e anomalias em tempo quase real.

* Criar aplicativos personalizados na plataforma do Time Series Insights

  * O Azure Time Series Insights dá suporte a SDK do JavaScript com controles avançados e acesso simplificado a consultas para permitir que os clientes criem aplicativos de IoT personalizados com base na plataforma do Time Series Insights a fim de atender a necessidades de negócios individuais.
  * Os clientes também podem usar as APIs de consulta do Time Series Insights diretamente para levar os dados a aplicativos de IoT personalizados.

## <a name="next-steps"></a>Próximas etapas

Você está pronto para começar a usar o Azure TSI (versão prévia):

> [!div class="nextstepaction"]
> [Leia o guia de Início Rápido](./time-series-insights-update-quickstart.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png