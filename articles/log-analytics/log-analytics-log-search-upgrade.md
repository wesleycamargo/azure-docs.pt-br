---
title: Atualizar o Azure Log Analytics para nova pesquisa de logs | Microsoft Docs
description: "A nova linguagem de consulta do Log Analytics está quase chegando e você pode participar da visualização pública.  Este artigo descreve as vantagens da nova linguagem e diz como converter seu espaço de trabalho."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 36a08cab2b1d409d2de382a07cfd7259a1c94e13
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="azure-log-analytics-upgrade-to-new-log-search"></a>Atualização do Azure Log Analytics para a nova pesquisa de logs

A nova linguagem de consulta do Log Analytics chegou e seu espaço de trabalho precisa receber o upgrade para você aproveitá-la.  Você pode atualizar o seu espaço de trabalho por conta própria ou aguardar até que seja feito o upgrade automático durante o período de distribuição que começa no final de outubro e vai até o final do ano.  Este artigo descreve as vantagens da nova linguagem e diz como converter seu espaço de trabalho.  

## <a name="why-the-new-language"></a>Por que a nova linguagem?
Compreendemos que há problemas em qualquer transição e não estamos apenas alterando linguagem de consulta por diversão.  Há várias razões pelas quais essa alteração fornecerá valor significativo para os clientes do Log Analytics.

- **Simples mas poderosa.** A nova linguagem é mais fácil de entender e é semelhante ao SQL, com construções mais semelhantes a linguagem natural do que a linguagem herdada.
- **Linguagem totalmente por piping.**  A nova linguagem tem recursos de piping mais amplos do que a linguagem herdada.  Virtualmente qualquer saída pode ser canalizada por pipe para outro comando para criar consultas mais complexas do que era possível anteriormente.
- **Extrações de campo de tempo de pesquisa.**  A nova linguagem dá suporte a campos calculados de tempo de execução mais avançados do que a linguagem herdada.  Você pode usar cálculos complexos para os campos estendidos e, em seguida, usar os campos calculados para comandos adicionais, incluindo junções e agregações.
- **Junções avançadas.**  A nova linguagem fornece junções mais avançadas do que a linguagem herdada, incluindo a capacidade de unir tabelas em vários campos, usar junções internas e externas fazer junções em campos estendidos.
- **Funções de data/hora.**  A nova linguagem tem funções de data/hora mais avançadas do que a linguagem herdada.
- **Análise inteligente.**  A nova linguagem tem algoritmos avançados para avaliar padrões em conjuntos de dados e comparar diferentes conjuntos de dados.
- **Portal Análise Avançada.**  O portal Análise Avançada oferece recursos de análise não disponíveis no portal do Log Analytics, incluindo edição de consultas multilinha, visualizações adicionais e diagnósticos avançados.
- **Consistência com outros aplicativos.**  A nova linguagem e o Portal Análise Avançada já são usados para análise no Application Insights.  Implementá-la para o Log Analytics proporciona consistência entre os serviços do Azure.
- **Melhor integração com o Power BI.** Consultas na nova linguagem podem ser exportadas para o Power BI Desktop, para que você possa utilizar seus recursos de transformação de dados avançados.
- **E muito mais.** Consulte o site [Linguagem de Consulta do Azure Log Analytics](https://docs.loganalytics.io) para obter detalhes completos e tutoriais sobre a nova linguagem.


## <a name="when-can-i-upgrade"></a>Quando posso fazer a atualização?
A atualização será lançada em todas as regiões do Azure, então ela pode estar disponível em algumas regiões antes de outras.  Você saberá quando seu espaço de trabalho está disponível para upgrade quando vir uma faixa na parte superior do espaço de trabalho convidando-o para atualizar.

![Atualização 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

Se o seu espaço de trabalho for atualizado automaticamente, você verá uma faixa indicando que ele recebeu o upgrade com um resumo indicando se algum problema foi encontrado.

 ![Atualização automática](media/log-analytics-log-search-upgrade/auto-upgrade.png)


## <a name="what-happens-after-the-upgrade"></a>O que acontece após a atualização?
As seguintes alterações são feitas ao seu espaço de trabalho quando ele é convertido:

- Quaisquer pesquisas salvas, regras de alerta e exibições que você tenha criado com o Designer de Exibição são convertidas automaticamente na nova linguagem.  Pesquisas incluídas em soluções não são convertidas automaticamente, mas em vez disso são convertidas em tempo real quando abertas por você.  
- [Meu painel](log-analytics-dashboards.md) está sendo preterido a favor do [Designer de Exibição](log-analytics-view-designer.md) e dos [Painéis do Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards.md).  Os blocos que você adicionou ao Meu Painel ainda estão disponíveis, mas são somente leitura.
- A [Integração do Power BI](log-analytics-powerbi.md) é substituída por um novo processo.  Qualquer agenda existente do Power BI que você tenha criado será desabilitada e você precisa substituí-la pelo novo processo.
- As respostas de [ações de alerta](log-analytics-alerts-actions.md) usando webhooks e runbooks têm um novo formato e talvez seja necessário atualizar suas regras de alerta adequadamente.
- Examine as [Perguntas Frequentes de Pesquisa de Logs](log-analytics-log-search-faq.md) sobre perguntas comuns referentes à atualização.

## <a name="how-do-i-know-if-there-were-any-issues-from-the-upgrade"></a>Como faço para saber se houve problemas com a atualização?
Após a conclusão da atualização, haverá uma seção de **Resumo da Atualização** nas configurações para o espaço de trabalho.  Consulte esta seção para obter informações sobre a atualização e exibir quaisquer problemas que foram encontrados.

 ![Resumo de atualização](media/log-analytics-log-search-upgrade/upgrade-summary.png)

## <a name="how-do-i-manually-perform-the-upgrade"></a>Como faço para executar a atualização manualmente?
Você pode fazer atualização do seu espaço de trabalho ao ver a faixa na parte superior do portal.  

1.  Inicie o processo de atualização clicando na faixa que diz **Saber mais e atualizar**.

    ![Atualização 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>

2.  Leia as informações adicionais sobre a atualização na página de informações de atualização.

    ![Atualização 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>

3.  Clique em **Atualizar Agora** para iniciar a atualização.

    ![Atualização 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>Uma caixa de notificação no canto direito superior mostra o status.
    
    ![Atualização 5](media/log-analytics-log-search-upgrade/upgrade-05.png)

4.  É isso!  Examine a página Pesquisa de Logs para dar uma olhada em seu espaço de trabalho atualizado.

    ![Atualização 6](media/log-analytics-log-search-upgrade/upgrade-06.png)

Se você encontrar um problema que cause falha na atualização, você poderá ir para o [fórum de discussão](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e postar sua pergunta ou [criar uma solicitação de suporte](../azure-supportability/how-to-create-azure-support-request.md) do Portal do Azure.

## <a name="how-do-i-learn-the-new-language"></a>Como fazer para aprender a nova linguagem?
Já que ela é usada por vários serviços, criamos um [site externo para hospedar a documentação](https://docs.loganalytics.io/) da nova linguagem.  Isso inclui uma referência completa para ajudá-lo a aprender, além de exemplos e tutoriais. Você pode percorrer um tutorial da nova linguagem em [Introdução a consultas](https://go.microsoft.com/fwlink/?linkid=856078) e acessar a referência da linguagem em [Linguagem de consulta do Log Analytics](https://go.microsoft.com/fwlink/?linkid=856079).  

Se você quiser experimentar o novo idioma em um ambiente de demonstração, incluindo um conjunto de dados de exemplo, dê uma olhada no [ambiente de playground](https://portal.loganalytics.io/demo#/discover/home).

No entanto, se você já está familiarizado com a linguagem de consulta do Log Analytics herdada, você pode usar o conversor de idioma que é adicionado ao seu espaço de trabalho como parte da atualização.  Basta digitar sua consulta herdada e, em seguida, clique em **Converter** para ver a versão traduzida.  Você pode, em seguida, clicar no botão Pesquisar para executar a pesquisa ou copiar e colar a consulta convertida para usar em outro lugar, por exemplo, uma regra de alerta.  Você também pode dar uma olhada em nosso [roteiro](log-analytics-log-search-transition.md) que compara diretamente consultas comuns da linguagem herdada.

![Conversor de linguagem](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>Próximas etapas
- Confira um [tutorial sobre a nova linguagem](https://go.microsoft.com/fwlink/?linkid=856078).
- Percorra um [tutorial sobre como usar o portal de Pesquisa de Logs](log-analytics-log-search-log-search-portal.md) com a nova linguagem de consulta.
- Obtenha uma introdução ao novo [portal Análise Avançada](https://go.microsoft.com/fwlink/?linkid=856587).
