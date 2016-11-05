---
title: O que é o Log Analytics? | Microsoft Docs
description: O Log Analytics é um serviço no OMS (Operations Management Suite) que ajuda a coletar e analisar operacionais dados gerados pelos recursos nos seus ambientes local e de nuvem. Este artigo fornece uma visão geral dos diferentes componentes do Log Analytics e links para conteúdo detalhado.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn

ms.service: log-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/13/2016
ms.author: bwren

---
# O que é o Log Analytics?
O Log Analytics é um serviço no OMS (Operations Management Suite) que ajuda a coletar e analisar dados gerados pelos recursos nos seus ambientes local e de nuvem. Ele fornece informações em tempo real usando a pesquisa integrada e painéis personalizados para analisar prontamente milhões de registros em todas as suas cargas de trabalho e servidores, independentemente da sua localização física.

## Componentes do Log Analytics
No centro no Log Analytics está o repositório do OMS, que está hospedado na nuvem do Azure. Os dados são coletados para o repositório de fontes conectadas configurando fontes de dados e adicionando soluções à sua assinatura. Fontes de dados e soluções criarão diferentes tipos de registro que têm seus próprios conjuntos de propriedades, mas ainda podem ser analisados juntos em consultas no repositório. Isso permite usar as mesmas ferramentas e métodos para trabalhar com diferentes tipos de dados coletados por fontes diferentes.

![Repositório do OMS](media/log-analytics-overview/overview.png)

Fontes conectadas são os computadores e outros recursos que geram dados coletados pelo Log Analytics. Isso pode incluir os agentes instalados em computadores [Windows](log-analytics-windows-agents.md) e [Linux](log-analytics-linux-agents.md) que se conectam diretamente ou agentes em um [grupo de gerenciamento do System Center Operations Manager conectado](log-analytics-om-agents.md). O Log Analytics também pode coletar dados do [Armazenamento do Azure](log-analytics-azure-storage.md).

[Fontes de dados](log-analytics-data-sources.md) são os diferentes tipos de dados coletados de cada fonte conectada. Isso inclui eventos e [dados de desempenho](log-analytics-data-sources-performance-counters.md) de agentes [Windows](log-analytics-data-sources-windows-events.md) e Linux além de fontes como [logs do IIS](log-analytics-data-sources-iis-logs.md) e [logs de texto personalizado](log-analytics-data-sources-custom-logs.md). Você configura cada fonte de dados que deseja coletar e a configuração é fornecida automaticamente para cada fonte conectada.

## Analisando dados do Log Analytics
A maioria da sua interação com o Log Analytics será por meio do portal do OMS, que é executado em qualquer navegador e fornece acesso às definições de configuração e várias ferramentas para analisar e agir sobre os dados coletados. No portal, você pode aproveitar as [pesquisas de log](log-analytics-log-searches.md) nas quais é possível construir consultas para analisar os dados coletados, [painéis](log-analytics-dashboards.md) que podem ser usados para personalizar a exibição gráfica das suas pesquisas mais valiosas e [soluções](log-analytics-add-solutions.md) que fornecem ferramentas de análise e funcionalidade adicionais.

![Portal do OMS](media/log-analytics-overview/portal.png)

O Log Analytics fornece uma sintaxe de consulta para recuperar e consolidar dados no repositório rapidamente. Você poderá criar e salvar [Pesquisas de Log](log-analytics-log-searches.md) para analisar dados no portal do OMS ou fazer as pesquisas de log serem executadas automaticamente para criar um alerta se os resultados da consulta indicarem uma condição importante.

![Pesquisa de log](media/log-analytics-overview/log-search.png)

Para uma rápida exibição gráfica da integridade do seu ambiente geral, você pode adicionar visualizações de pesquisas de log salvas ao seu [painel](log-analytics-dashboards.md).

![Painel](media/log-analytics-overview/dashboard.png)

Para analisar os dados fora do Log Analytics, você pode exportá-los do repositório do OMS em ferramentas como [Power BI](log-analytics-powerbi.md) ou Excel. Você também pode aproveitar a [API de Pesquisa de Log](log-analytics-log-search-api.md) para criar soluções personalizadas que aproveitam os dados do Log Analytics ou para integração com outros sistemas.

## Soluções
As soluções acrescentam funcionalidades ao Log Analytics. A maioria delas é executada na nuvem e fornecem análise de dados coletados no repositório do OMS. Elas também podem definir novos tipos de registro a serem coletados que podem ser analisados com Pesquisas de Log ou pela interface do usuário adicional fornecida pela solução no painel do OMS.

![Solução de Controle de Alterações](media/log-analytics-overview/change-tracking.png)

Soluções estão disponíveis para uma variedade de funções e você pode procurar soluções disponíveis com facilidade e [adicioná-las ao seu espaço de trabalho do OMS](log-analytics-add-solutions.md) da Galeria de Soluções. Muitas delas serão implantados automaticamente e começarão a funcionar imediatamente, enquanto outras podem exigir certa configuração.

![Galeria de Soluções](media/log-analytics-overview/solution-gallery.png)

## Arquitetura do Log Analytics
Os requisitos de implantação do Log Analytics são mínimos, já que os componentes centrais são hospedados na nuvem do Azure. Isso inclui o repositório, além dos serviços que permitem correlacionar e analisar os dados coletados. O portal pode ser acessado de qualquer navegador, portanto não há nenhum requisito para o software cliente.

Você deve instalar agentes em computadores [Windows](log-analytics-windows-agents.md) e [Linux](log-analytics-linux-agents.md), mas nenhum agente adicional é necessário para computadores que já são membros de um [grupo de gerenciamento do SCOM conectado](log-analytics-om-agents.md). Agentes do SCOM continuarão a se comunicar com servidores de gerenciamento que encaminharão os dados para o Log Analytics. Contudo, algumas soluções exigirão que os agentes se comuniquem diretamente com o Log Analytics. A documentação para cada solução especificará a seus requisitos de comunicação.

Ao [inscrever-se para o Log Analytics](log-analytics-get-started.md), você criará um espaço de trabalho do OMS. Pense no espaço de trabalho como um ambiente exclusivo do OMS com seu próprio repositório de dados, fontes de dados e soluções. É possível criar vários espaços de trabalho na sua assinatura para dar suporte a vários ambientes como produção e teste.

![Arquitetura do Log Analytics](media/log-analytics-overview/architecture.png)

## Próximas etapas
* [Inscreva-se em uma conta gratuita do Log Analytics](log-analytics-get-started.md) para testá-lo em seu próprio ambiente.
* Veja as diferentes [Fontes de Dados](log-analytics-data-sources.md) disponíveis para coletar dados para o repositório do OMS.
* [Procure soluções disponíveis na Galeria de Soluções](log-analytics-add-solutions.md) para adicionar funcionalidades ao Log Analytics.

<!---HONumber=AcomDC_0525_2016-->