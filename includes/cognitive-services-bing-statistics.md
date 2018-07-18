---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: cognitive-services
author: scottwhi-msft
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2018
ms.author: scottwhi
ms.custom: include file
ms.openlocfilehash: 4e19c1afefdc5bcacebcb0d495193b48c7a6d724
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313703"
---
As Estatísticas do Bing fornecem análise para APIs de Pesquisa do Bing. A análise inclui o volume de chamadas, as principais cadeias de caracteres de consulta, a distribuição geográfica, e muito mais. Para habilitar as Estatísticas do Bing na assinatura paga da Pesquisa do Bing, navegue até o [painel do Azure](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7), selecione a assinatura paga e clique em Habilitar Estatísticas do Bing. Habilitar as Estatísticas do Bing aumenta ligeiramente a taxa de assinatura (consulte o [preço](https://aka.ms/bingstatisticspricing)).


> [!NOTE]
> As Estatísticas do Bing estão disponíveis apenas com assinaturas pagas - não estão disponíveis com assinaturas de avaliação gratuita. 

> [!NOTE]
> Não é possível usar dados disponíveis por meio do painel das Estatísticas do Bing para criar aplicativos para distribuição a terceiros.

O Bing atualiza dados analíticos a cada 24 horas e mantém até 13 meses de histórico.

## <a name="accessing-your-analytics"></a>Acessar as análises

Para acessar o painel de análise, vá para https://bingapistatistics.com. Certifique-se de que está conectado usando a mesma conta da Microsoft (MSA) utilizada para obter a assinatura paga.


## <a name="filtering-the-data"></a>Filtragem de dados

Por padrão, os gráficos e grafos refletem todos os dados de métricas que você tem acesso. É possível filtrar os dados mostrados nos gráficos e grafos, selecionando os recursos, mercados, pontos de extremidade e período de relatório nos quais está interessado. Os gráficos e grafos são alterados para refletir os filtros aplicados. A seguir, são descritos os filtros que podem ser alterados.

- **ID do recurso**: a ID exclusiva do recurso que identifica a assinatura do Azure. Se você assinar mais de uma camada de API de Pesquisa do Bing, a lista conterá várias IDs. Por padrão, todos os recursos são selecionados.  
  
- **Mercados**: os mercados de onde vêm os resultados. Por exemplo, en-us (inglês, Estados Unidos). Por padrão, todos os mercados são selecionados. Observe que o mercado en-WW é o mercado que o Bing usará, se a chamada não especificar um mercado e o Bing não puder determinar o mercado do usuário.  
  
- **Pontos de extremidade**: os pontos de extremidade de API de Pesquisa do Bing. A lista contém todos os pontos de extremidade para os quais você tem uma assinatura paga. Por padrão, todos os pontos de extremidade são selecionados.  

- **Período**: o período do relatório. É possível especificar:  
  
  - Todos &mdash;Inclui até 13 meses de dados  
  - Últimas 24 horas&mdash;Inclui análises das últimas 24 horas  
  - Última semana&mdash;Inclui análises dos últimos sete dias  
  - Último mês&mdash;Inclui análises dos últimos 30 dias  
  - Um intervalo de datas personalizado&mdash;Inclui análises do intervalo de datas especificado, se disponível  
  
  > [!NOTE]  
  > Pode demorar até 24 horas para que as métricas apareçam no painel. O painel mostra a data e hora em que os dados foram atualizados pela última vez.  
  
  > [!NOTE]  
  > As métricas estarão disponíveis a partir do momento que você habilitar o suplemento de Estatísticas do Bing. 


## <a name="charts-and-graphs"></a>Gráficos e grafos

O painel mostra gráficos e grafos das métricas disponíveis para o ponto de extremidade selecionado. Nem todas as métricas estão disponíveis para todos os pontos de extremidade. Os gráficos e grafos de cada ponto de extremidade são estáticos (não é possível selecionar os gráficos e grafos a serem exibidos). O painel mostra apenas gráficos e grafos para os quais há dados. 

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

A seguir, são apresentadas as métricas possíveis. Cada métrica registra restrições de ponto de extremidade. 

- **Volume da chamada**: mostra o número de chamadas feitas durante o período do relatório. Se o período do relatório for de um dia, o gráfico mostrará o número de chamadas feitas por hora. Caso contrário, o gráfico mostra o número de chamadas feitas por dia do período do relatório.  
  
  > [!NOTE]
  > O volume de chamadas pode diferir dos relatórios de cobrança, que geralmente incluem apenas chamadas com êxito. 
  
-  **Principais consultas**: mostra as principais consultas e o número de ocorrências de cada consulta durante o período do relatório. É possível configurar o número de consultas mostradas. Por exemplo, é possível mostrar as 25, 50 ou 75 principais consultas. As principais consultas não estão disponíveis para os pontos de extremidade a seguir:  
  
  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > Alguns termos de consulta podem ser suprimidos para remover informações confidenciais, como emails, números de telefone, SSN etc.  

- **Distribuição geográfica**: os mercados de onde vêm os resultados. Por exemplo, en-us (inglês, Estados Unidos). O Bing usa o parâmetro de consulta `mkt` para determinar o mercado, se especificado. Caso contrário, o Bing usa sinais como o endereço IP do chamador para determinar o mercado.  
  
- **Distribuição de código de resposta**: os códigos de status HTTP de todas as chamadas durante o período do relatório.  
  
- **Distribuição de origem de chamada**: os tipos de navegadores usados pelos usuários. Por exemplo, Edge, Chrome, Safari e FireFox. Chamadas feitas de fora de um navegador como bots, Postman ou usando ondulação de um aplicativo de console são agrupadas em Bibliotecas. A origem é determinada usando o valor do cabeçalho do Agente do Usuário da solicitação. Se a solicitação não incluir o cabeçalho do Agente do Usuário, o Bing tentará derivar a origem de outros sinais.  
  
- **Distribuição de pesquisa segura**: a distribuição de valores de pesquisa segura. Por exemplo, desativado, moderado ou estrito. O parâmetro de consulta `safeSearch` contém o valor, se especificado. Caso contrário, o Bing padroniza o valor para moderado.  
  
- **Distribuição de respostas solicitadas**: a API de pesquisa na Web responde às consultas solicitadas no parâmetro de consulta `responseFilter`.  
  
- **Distribuição de respostas retornadas**: as respostas que a API de Pesquisa da Web retornou na resposta.  
  
- **Distribuição do servidor de resposta**: o servidor de aplicativos que atendeu as solicitações de API. Os valores possíveis são Bing.com (para tráfego veiculado em dispositivos de desktop e laptop) e Bing.com-mobile (para tráfego veiculado em dispositivos móveis). O servidor é determinado usando o valor do cabeçalho do Agente do Usuário da solicitação. Se a solicitação não incluir o cabeçalho do Agente do Usuário, o Bing tentará derivar o servidor de outros sinais.  
  


A seguir, as análises disponíveis para cada ponto de extremidade.

![Distribuição por matriz de suporte de ponto de extremidade](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)


