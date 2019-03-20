---
title: Visualizar dados no gerenciador da versão prévia do Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve os vários recursos e opções disponíveis no aplicativo Web do Time Series Insights Explorer.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: ed8a829bd73a53dc94bf8b08648b3d6684f718f6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58109756"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualizar os dados na atualização do gerenciador

Este artigo descreve os vários recursos e opções disponíveis no aplicativo Web do gerenciador da versão prévia do [Azure Time Series Insights](https://insights.timeseries.azure.com/preview/samples).

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar o gerenciador da Versão prévia do Azure Time Series Insights, você deve:

* Criar um ambiente do Time Series Insights. Para obter mais informações, confira [Tutorial: Versão prévia do Azure Time Series Insights](./time-series-insights-update-create-environment.md).
* Fornece acesso a dados para o ambiente do Time Series Insights que você criou para a conta. Você pode fornecer acesso a outras pessoas, bem como para si mesmo.
* Adicione uma origem do evento no ambiente do Time Series Insights para enviar dados por push para o ambiente.

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>Saiba mais sobre o gerenciador Versão Prévia do Azure Time Series Insights

  ![explorer-one][1]

O Gerenciador da Versão prévia do Azure Time Series Insights consiste dos seguintes elementos:

* **Barra de navegação**: Permite que você alterne entre as páginas de análise e o modelo.
* **Árvore hierárquica**: Permite selecionar os elementos de dados específicos que devem ser plotados.
* **Série temporal**: Exibe os elementos de dados selecionado no momento.
* **Painel gráfico**: Exibe o gráfico de trabalho atual.
* **Linha do tempo**: Permite que você modifique seu período de tempo de trabalho.
* **Barra de aplicativo**: Contém opções de gerenciamento de usuário, como o locatário atual e permite que você altere as configurações de tema e idioma.

## <a name="time-series-insights-preview-environment-panel"></a>Ambiente de versão prévia do Time Series Insights

O painel de ambiente exibe todos os ambientes do Time Series Insights, que você tem acesso. A lista inclui versão prévia de ambientes pago conforme o uso de ambientes e ambientes S1/S2 (GA). Basta clicar no ambiente do Time Series Insights que você deseja usar.

  ![explorer-two][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Menu de navegação da versão do Azure Time Series Insights

  ![explorer-three][3]

Com o menu de navegação, você pode alternar entre os aplicativos do Azure Time Series Insights:

* **Analisar**: Permite executar análises avançadas em seus dados de série de tempo modelados ou não modelados e gráfico.

* **Modelo**: Permite que você envie por push novos tipos de visualização de Insights de série de tempo, hierarquias e instâncias ao seu modelo Time Series Insights.

## <a name="time-series-insights-preview-model-authoring"></a>Autoria do modelo de Versão prévia do Time Series Insights

Com este aplicativo, você pode executar operações de criar, ler, atualizar e excluir (CRUD) em seu Modelo do Time Series.  

* **Tipo de Modelo do Time Series**: Os tipos de modelo do Azure Time Series Insights ajudam a definir variáveis ou fórmulas para fazer cálculos. Os tipos são associados a uma instância específica do Time Series Insights. Um tipo pode ter uma ou mais variáveis.
* **Hierarquias do modelo do Time Series**: Hierarquias são organizações sistemáticas de seus dados. Hierarquias descrevem as relações entre entidades diferentes em seus dados de Azure Time Series Insights.
* **Instâncias do modelo do Time Series**: As instâncias são a série temporal em si. Na maioria dos casos, são o DeviceID ou AssetID, que é o identificador exclusivo do ativo no ambiente.

Para saber mais sobre o Modelo do Time Series, consulte [Times Series Models](./time-series-insights-update-tsm.md).

## <a name="time-series-insights-preview-model-search-panel"></a>Painel de pesquisa do modelo de Versão prévia do Time Series Insights

O painel de pesquisa de modelo permite que você facilmente pesquise e navegue em sua hierarquia de modelo de série temporal para localizar as instâncias da série de tempo específica que você deseja exibir no gráfico. Ao selecionar as instâncias, elas são adicionadas ao gráfico atual e os dados também.

  ![explorer-four][4]

## <a name="time-series-insights-preview-well"></a>Fonte da Versão prévia do Time Series Insights

O fonte exibe os campos de instância e outros metadados associados com instâncias da série de tempo selecionado. As caixas de seleção à direita permitem ocultar ou exibir instâncias específicas do gráfico atual. Você também pode remover elementos de dados específicos de seus dados atuais clicando o controle de x vermelho à direita do elemento.

  ![explorer-five][5]

Você também pode retirar o painel de telemetria para obter uma melhor exibição vertical dos elementos em sua fonte de dados.

  ![explorer-six][6]

> [!NOTE]
> Se você vir a mensagem a seguir, a instância não tem todos os dados durante o período de tempo selecionado. Para resolver o problema, você pode aumentar o período de tempo ou confirmar se a instância está enviando dados.
>
> ![explorer-seven][7]

## <a name="time-series-insights-preview-chart"></a>Gráfico da Versão prévia do Time Series Insights

Com o gráfico, você pode exibir as instâncias da série de tempo como linhas. Você pode recolher o painel de ambiente, o modelo de dados e o painel de controle de intervalo de tempo, clicando em controles da web para tornar o gráfico maior.

  ![explorer-eight][8]

1. **Intervalo de Data Selecionada**: Controla quais elementos de dados estão disponíveis para visualização.

1. **Ferramenta de controle deslizante de intervalo de data interna**: Use os dois controles de ponto de extremidade, arrastando-os no período de tempo desejado.

1. **Controle de recolhimento do período de tempo**: Recolhe e expande o editor de alcance do painel de tempo.

1. **Controle de formato do eixo y**: Alterna entre as opções disponíveis de modo de exibição do eixo y:

    * `Default`: Cada linha tem um eixo de y individual.
    * `Stacked`: Permite empilhar várias linhas no mesmo eixo y, com a alteração de dados do eixo y com base na linha selecionada.
    * `Shared`: Todos os dados do eixo y exibidos juntos.

1. **Elemento de dados atual**: O elemento de dados selecionado no momento e seus detalhes associados.

Você pode detalhar ainda mais em uma fatia de dados específicos clicando com o botão um ponto de dados no gráfico atual e, em seguida, arrastando a área selecionada para o ponto de extremidade de sua escolha. Clique na área cinza selecionada e clique no zoom conforme exibido na imagem a seguir:

  ![explorer-nine][9]

Depois de executar a ação de zoom, você verá seu conjunto de dados selecionado. Clique no controle de formato do eixo y para alternar entre as representações de eixo y três dos seus dados do Time Series Insights.

  ![explorer-ten][10]

Aqui você pode ver um exemplo de eixos y compartilhados:

  ![explorer-eleven][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Painel do editor de tempo de Versão prévia do Time Series Insights

Quando você trabalha com a versão prévia do Time Series Insights, você primeiro selecione um período de tempo. O período de tempo selecionado controla o conjunto de dados que está disponível para manipulação com os widgets de versão prévia do Time Series Insights. Os controles da web a seguir estão disponíveis na versão prévia do Time Series Insights para selecionar o seu tempo de trabalho.

  ![explorer-twelve][12]

1. **Ferramenta de controle deslizante de intervalo de data interna**: Use os dois controles de ponto de extremidade, arrastando-os no período de tempo desejado. Este intervalo de datas interna é restrito pelo controle deslizante de intervalo de data externa.

1. **Aumentar e diminuir os botões de intervalo de data**: Aumente ou diminua o período de tempo, selecionando um dos botões para o intervalo desejado.

1. **Controle de recolhimento do período de tempo**: Esse controle da web permite ocultar todos os controles, exceto para a ferramenta de controle deslizante de intervalo de datas interna.

1. **Ferramenta de controle deslizante de intervalo de data interna**: Use os controles de ponto de extremidade para selecionar o intervalo de data externa, que estará disponível para o seu controle de intervalo de datas interna.

1. **Menu suspenso de períodos de tempo rápido**: Permite que você alterne rapidamente entre seleções span horário predefinido, como os últimos 30 minutos, nas últimas 12 horas ou um intervalo personalizado. A alteração desse valor também muda os intervalos de intervalo disponíveis discutidos na ferramenta de controle deslizante de tamanho do intervalo.

1. **Ferramenta de controle deslizante de tamanho do intervalo**: Permite que você amplie e reduza intervalos no mesmo período de tempo. Essa ação fornece controle mais preciso dos movimentos entre fatias de tempo grandes. Exibe tendências suaves até fatias tão pequenas quanto um milissegundo, permitindo que você veja os recortes granulares e de alta resolução dos seus dados. O ponto de partida padrão do controle deslizante é definido como a exibição mais ideal dos dados na seleção, que balanceia a resolução, velocidade de consulta e granularidade.

1. **Intervalo de controle da web e de datas**: Com esse controle da web você pode facilmente clicar e selecionar seu data desejada e intervalos de tempo. Você também pode usar o controle para alternar entre fusos horários diferentes. Depois de fazer alterações, para aplicar ao seu espaço de trabalho atual, selecione **Salvar**.

   ![explorer-thirteen][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Painel de navegação da versão do Azure Time Series Insights

O painel de navegação de visualização do Time Series Insights fornece a seguinte funcionalidade:

  ![explorer-fourteen][14]

### <a name="current-session-share-link-control"></a>Controle de link de compartilhamento de sessão atual

  ![explorer-thirteen][15]

Selecione o link de controle da web (realçado) para gerar uma URL para salvar ou compartilhar sua sessão de trabalho atual do Azure Time Series Insights, que inclui:

* Intervalo de tempo selecionado personalizado
* Tamanho do intervalo selecionado no momento
* Elemento atualmente selecionado

### <a name="tenant-section"></a>Seção de Locatário

  ![explorer-sixteen][16]

* Exibe suas informações de conta de logon atuais do Time Series Insights.
* Permite que você alterne entre os temas disponíveis do Time Series Insights.

### <a name="theme-selection"></a>Seleção de tema

O Azure Time Series Insights Preview suporta dois temas:

* **Tema claro**: O tema padrão mostrado ao longo deste documento.
* **Tema escuro**:  Renderiza o explorer conforme mostrado aqui:

  ![explorer-seventeen][17]

Aqui você também pode alterar entre as linguagens com suporte.

## <a name="s1s2-environment-controls"></a>Controles de ambiente S1/S2

### <a name="time-series-insights-preview-terms-panel"></a>Painel dos termos da versão prévia do Time Series Insights

Esta seção se aplica somente a ambientes existentes do S1/S2 que tentam usar o gerenciador na interface do usuário atualizada. Você talvez queira usar o produto de GA e a visualização em combinação. Adicionamos algumas funcionalidades de interface do usuário existente para ao gerenciador atualizado, mas você pode obter a experiência de interface do usuário completa para o ambiente S1/S2 no gerenciador do Time Series Insights existente.  

Em vez de hierarquia, você verá o painel de termos do Time Series Insights, onde você define consultas em seu ambiente. Isso permite filtrar seus dados com base em um predicado.

  ![explorer-eighteen][18]

O painel de editor de termos de visualização do Azure Time Series Insights leva em consideração os seguintes parâmetros:

**Onde**: A cláusula Onde permite que você rapidamente filtre seus eventos usando o conjunto de operandos listados na tabela a seguir. Caso você realize uma pesquisa selecionando um operando, o predicado é atualizado automaticamente com base nessa pesquisa. Os tipos de operando com suporte incluem:

| Operação | Tipos com suporte   | Observações |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Todos os operandos devem ser do mesmo tipo ou ser uma constante NULL. |
| `HAS` | Cadeia de caracteres | Apenas os literais de cadeia de caracteres de constante são permitidos no lado direito. Uma cadeia de caracteres vazia e NULL não são permitidas. |

Saiba mais sobre os tipos de dados e operações de consulta com suporte, lendo [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Exemplos de cláusulas Onde

  ![explorer-nineteen][19]

**Medida**: Essa lista suspensa exibe todas as colunas numéricas (**Duplas**) que você pode usar como elementos do gráfico atual.

**Dividir por**: Essa lista suspensa exibe todas as colunas categóricas disponíveis (cadeias de caracteres) em seu modelo que você pode agrupar os dados. Adicione até cinco termos a serem exibidos no mesmo eixo X. Insira os parâmetros desejados e, em seguida, selecione **Adicionar** para adicionar um novo termo.

  ![explorer-twenty][20]

Você pode mostrar e ocultar elementos no painel de gráfico, selecionando o ícone visível, conforme mostrado na imagem a seguir. Você pode remover completamente a consultas, clicando em vermelha **x**.

  ![explorer-twenty-one][21]

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos:
* [Armazenamento e entrada da Versão Prévia do Azure Time Series Insights](./time-series-insights-update-storage-ingress.md)
* [Modelagem de dados](./time-series-insights-update-tsm.md)
* [Solução de problemas e diagnósticos](./time-series-insights-update-how-to-troubleshoot.md)

<!-- Images -->
[1]: media/v2-update-explorer/explorer-one.png
[2]: media/v2-update-explorer/explorer-two.png
[3]: media/v2-update-explorer/explorer-three.png
[4]: media/v2-update-explorer/explorer-four.png
[5]: media/v2-update-explorer/explorer-five.png
[6]: media/v2-update-explorer/explorer-six.png
[7]: media/v2-update-explorer/explorer-seven.png
[8]: media/v2-update-explorer/explorer-eight.png
[9]: media/v2-update-explorer/explorer-nine.png
[10]: media/v2-update-explorer/explorer-ten.png
[11]: media/v2-update-explorer/explorer-eleven.png
[12]: media/v2-update-explorer/explorer-twelve.png
[13]: media/v2-update-explorer/explorer-thirteen.png
[14]: media/v2-update-explorer/explorer-fourteen.png
[15]: media/v2-update-explorer/explorer-fifteen.png
[16]: media/v2-update-explorer/explorer-sixteen.png
[17]: media/v2-update-explorer/explorer-seventeen.png
[18]: media/v2-update-explorer/explorer-eighteen.png
[19]: media/v2-update-explorer/explorer-nineteen.png
[20]: media/v2-update-explorer/explorer-twenty.png
[21]: media/v2-update-explorer/explorer-twenty-one.png
