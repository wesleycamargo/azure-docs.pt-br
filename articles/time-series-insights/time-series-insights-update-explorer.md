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
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: 862465a7611f1a2bc65dbb0c49c4de512bd239de
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442094"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Visualizar os dados na atualização do gerenciador

Este documento descreve os recursos de interface do usuário/experiência do usuário e a interface da visualização dos Insights de série de tempo do Azure [aplicativo web de demonstração](https://insights.timeseries.azure.com/preview/demo). Especificamente, ele aborda o layout do exemplo hospedado, opções de personalização da interface e navegação por meio de demonstração fornecida.

## <a name="prerequisites"></a>Pré-requisitos

Para começar com o Gerenciador de visualização de Insights de série de tempo do Azure, faça o seguinte:

* Criar um ambiente do Time Series Insights. Para saber mais sobre o provisionamento de uma instância Experimente nossos [versão prévia do Azure tempo série Insights](./time-series-insights-update-create-environment.md) tutorial.
* [Fornecer acesso a dados](./time-series-insights-data-access.md) no ambiente do Time Series Insights que você criou para a conta. Você pode fornecer acesso a outras pessoas, bem como para si mesmo.
* Adicione uma origem do evento no ambiente do Time Series Insights para enviar dados por push para o ambiente:
  * Saiba mais [como se conectar a um hub de eventos](./time-series-insights-how-to-add-an-event-source-eventhub.md)
  * Saiba mais [como se conectar a um hub IoT](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="learn-about-the-preview-explorer"></a>Saiba mais sobre o Gerenciador de visualização

O Gerenciador da Versão prévia do Azure Time Series Insights consiste dos seguintes elementos:

[![O modo de exibição do Explorer](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

1. <a href="#environment-dropdown">**Painel de ambiente**</a>: Exibe seus ambientes Azure TSI.
1. <a href="#navigation-menu">**Menu de navegação**</a>: Permite que você alterne entre o **Analyze** e **modelo** páginas.
1. <a href="#hierarchy-tree">**Árvore hierárquica**</a>: Permite selecionar o modelo específico e elementos de dados devem ser plotados.
1. <a href="#preview-well">**Série temporal bem**</a>: Exibe os elementos de dados selecionado no momento em formato de tabela com codificação de cores.
1. <a href="#preview-chart">**Painel de gráfico**</a>:  Exibe o gráfico de trabalho atual.
1. <a href="#time-editor-panel">**Linha do tempo**</a>:  Permite que você modifique seu período de tempo de trabalho.
1. <a href="#navigation-panel">**Barra de aplicativo**</a>:  Contém opções de gerenciamento de usuário, como o locatário atual e permite que você altere as configurações de tema e idioma.

## <a name="environment-dropdown"></a>Menu suspenso do ambiente

O menu suspenso ambiente exibe todos os ambientes de Time Series Insights, que você tem acesso. A lista inclui ambientes pago conforme o uso (visualização) e S1/S2 (disponibilidade geral ou GA). 

1. Basta clicar na seta suspensa ao lado de seu ambiente exibido:

   [![O painel de controle](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Em seguida, selecione o ambiente desejado.

## <a name="navigation-menu"></a>Menu de navegação

  [![O menu de navegação](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

O menu de navegação permite que você selecione entre duas exibições:

* **Analisar**: Permite executar análises avançadas em seus dados de série de tempo modelados ou não modelados e gráfico.
* **Modelo**: Permite que você envie por push novos tipos de visualização de Insights de série de tempo, hierarquias e instâncias ao seu modelo Time Series Insights.

## <a name="hierarchy-tree"></a>árvore de hierarquia

A árvore de hierarquia exibe elementos de dados selecionados, incluindo modelos, dispositivos específicos e sensores em seus dispositivos.

### <a name="model-search-panel"></a>Painel de pesquisa de modelo

O painel de pesquisa de modelo permite que você facilmente pesquise e navegue em sua hierarquia de modelo de série temporal para localizar as instâncias da série de tempo específica que você deseja exibir no gráfico. Ao selecionar as instâncias, elas são adicionadas ao gráfico atual e os dados também.

  [![O painel de pesquisa de modelo](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Criação de modelos

A visualização dos Insights de série de tempo do Azure dá suporte a todas as operações criar, ler, atualizar e excluir (CRUD) em seu modelo de série temporal.  

* **Tipo de Modelo do Time Series**: Os tipos de modelo do Azure Time Series Insights ajudam a definir variáveis ou fórmulas para fazer cálculos. Os tipos são associados a uma instância específica do Time Series Insights. Um tipo pode ter uma ou mais variáveis.
* **Hierarquias do modelo do Time Series**: Hierarquias são organizações sistemáticas de seus dados. Hierarquias descrevem as relações entre entidades diferentes em seus dados de Azure Time Series Insights.
* **Instâncias do modelo do Time Series**: As instâncias são a série temporal em si. Na maioria dos casos, eles são os **DeviceID** ou **AssetID**, que é o identificador exclusivo do ativo no ambiente.

Para saber mais sobre o Modelo do Time Series, consulte [Times Series Models](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Visualizar bem

O também exibe campos de instância e outros metadados associados com instâncias selecionadas do TSI. As caixas de seleção à direita permitem ocultar ou exibir instâncias específicas do gráfico atual. Você também pode remover elementos de dados específicos de seus dados atuais bem clicando em vermelha **excluir** (Lixeira) controle no lado esquerdo do elemento.

  [![A visualização bem](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Você também pode reconfigurar o layout de seu **analisar** , selecionando o ícone de reticências no canto superior direito da página do gráfico:

  [![Opções de layout de telemetria](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Se você vir a mensagem a seguir, a instância não tem todos os dados durante o período de tempo selecionado. Para resolver o problema, você pode aumentar o período de tempo ou confirmar se a instância está enviando dados.
>
> ![Nenhuma notificação de dados](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Visualização do gráfico

Com o gráfico, você pode exibir instâncias TSI como linhas. Você pode recolher o painel de ambiente, o modelo de dados e o painel de controle de intervalo de tempo, clicando em controles da web para tornar o gráfico maior.

  [![Visão geral do gráfico de visualização](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

1. **Intervalo de Data Selecionada**: Controla quais elementos de dados estão disponíveis para visualização.

1. **Ferramenta de controle deslizante de intervalo de data interna**: Use os dois controles de ponto de extremidade, arrastando-os no período de tempo desejado.

1. **Controle de recolhimento do período de tempo**: Recolhe e expande o editor de alcance do painel de tempo.

1. **Controle de formato do eixo y**: Alterna entre as opções disponíveis de modo de exibição do eixo y:

    * `Default`: Cada linha tem um eixo de y individual.
    * `Stacked`: Permite empilhar várias linhas no mesmo eixo y, com a alteração de dados do eixo y com base na linha selecionada.
    * `Shared`: Todos os dados do eixo y exibidos juntos.

1. **Elemento de dados atual**: O elemento de dados selecionado no momento e seus detalhes associados.

Você pode detalhar ainda mais em uma fatia de dados específicos clicando com o botão um ponto de dados no gráfico atual e, em seguida, arrastando a área selecionada para o ponto de extremidade de sua escolha. A área cinza, selecionada com o botão direito e, em seguida, clique em **Zoom** conforme mostrado nesta imagem a seguir:

  [![Zoom de gráfico de visualização](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Depois de executar o **Zoom** ação, você verá seu conjunto de dados selecionado. Clique no controle de formato do eixo y para alternar entre as representações de eixo y três dos seus dados do Time Series Insights.

  [![Visualização do gráfico eixo y](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Aqui você pode ver um exemplo de eixos y compartilhados:

  [![Visualização compartilhado eixo y](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Painel do editor de tempo

Quando você trabalha com a versão prévia do Time Series Insights, você primeiro selecione um período de tempo. O período de tempo selecionado controla o conjunto de dados que está disponível para manipulação com os widgets de versão prévia do Time Series Insights. Os controles da web a seguir estão disponíveis na versão prévia do Time Series Insights para selecionar o seu tempo de trabalho.

  [![Painel de seleção de tempo](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Ferramenta de controle deslizante de intervalo de data interna**: Use os dois controles de ponto de extremidade, arrastando-os no período de tempo desejado. Este intervalo de datas interna é restrito pelo controle deslizante de intervalo de data externa.

1. **Aumentar e diminuir os botões de intervalo de data**: Aumente ou diminua o período de tempo, selecionando um dos botões para o intervalo desejado.

1. **Controle de recolhimento do período de tempo**: Esse controle da web permite ocultar todos os controles, exceto para a ferramenta de controle deslizante de intervalo de datas interna.

1. **Ferramenta de controle deslizante de intervalo de data interna**: Use os controles de ponto de extremidade para selecionar o intervalo de data externa, que estará disponível para o seu controle de intervalo de datas interna.

1. **Tempos rápidos de intervalo de datas suspensos**: Permite que você alternar rapidamente entre seleções span horário predefinido, como a última **30 minutos**, o **últimas 12 horas**, ou uma **intervalo personalizado**. A alteração desse valor também muda os intervalos de intervalo disponíveis discutidos na ferramenta de controle deslizante de tamanho do intervalo.

1. **Ferramenta de controle deslizante de tamanho do intervalo**: Permite que você amplie e reduza intervalos no mesmo período de tempo. Essa ação fornece controle mais preciso dos movimentos entre fatias de tempo grandes. Exibe tendências suaves até fatias tão pequenas quanto um milissegundo, permitindo que você veja os recortes granulares e de alta resolução dos seus dados. O ponto de partida padrão do controle deslizante é definido como a exibição mais ideal dos dados na seleção, que balanceia a resolução, velocidade de consulta e granularidade.

1. **Intervalo de controle da web e de datas**: Com esse controle da web, você pode facilmente clique e selecione seu data desejada e intervalos de tempo. Você também pode usar o controle para alternar entre fusos horários diferentes. Depois de fazer alterações, para aplicar ao seu espaço de trabalho atual, selecione **Salvar**.

   [![Para e do painel de seleção](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Painel de navegação

O painel de navegação de visualização de Insights de série de tempo é exibida na parte superior do seu aplicativo TSI. Ele fornece as seguintes funcionalidades.

### <a name="current-session-share-link-control"></a>Controle de link de compartilhamento de sessão atual

  [![Ícone de compartilhamento](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Selecione a nova **compartilhar** ícone para compartilhar um link de URL com sua equipe.

  [![Compartilhe sua URL de instância](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Seção de Locatário

  [![Seleção de locatário](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Exibe suas informações de conta de logon atuais do Time Series Insights.
* Permite que você alterne entre os temas disponíveis do Time Series Insights.
* Permite que você exiba a versão prévia [aplicativo web de demonstração](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Seleção de tema

Para selecionar um novo tema, clique em seu ícone perfil localizado no canto superior direito. Em seguida, selecione **alterar o tema**.

  [![Seleção de tema](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Seleção de idioma também está disponível clicando no ícone de seu perfil.

O Azure Time Series Insights Preview suporta dois temas:

* **Tema claro**: O tema padrão mostrado ao longo deste documento.
* **Tema escuro**:  Renderiza o explorer conforme mostrado aqui:

  [![Tema escuro selecionado](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>Controles de ambiente S1/S2

### <a name="preview-terms-panel"></a>Painel de termos de visualização

Esta seção se aplica somente a ambientes existentes do S1/S2 que tentam usar o gerenciador na interface do usuário atualizada. Você talvez queira usar o produto de GA e a visualização em combinação. Adicionamos algumas funcionalidades de interface do usuário existente para ao gerenciador atualizado, mas você pode obter a experiência de interface do usuário completa para o ambiente S1/S2 no gerenciador do Time Series Insights existente.  

Em vez de hierarquia, você verá o painel de termos do Time Series Insights, onde você define consultas em seu ambiente. Isso permite filtrar seus dados com base em um predicado.

  [![Em que o painel de consulta](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

O painel de editor de termos de visualização do Azure Time Series Insights leva em consideração os seguintes parâmetros:

**Onde**: A cláusula Onde permite que você rapidamente filtre seus eventos usando o conjunto de operandos listados na tabela a seguir. Caso você realize uma pesquisa selecionando um operando, o predicado é atualizado automaticamente com base nessa pesquisa. Os tipos de operando com suporte incluem:

| Operação | Tipos com suporte   | Observações |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, TimeSpan, NULL |
| `IN` | String, Bool, Double, DateTime, TimeSpan, NULL | Todos os operandos devem ser do mesmo tipo ou ser uma constante NULL. |
| `HAS` | String | Apenas os literais de cadeia de caracteres de constante são permitidos no lado direito. Uma cadeia de caracteres vazia e NULL não são permitidas. |

Saiba mais sobre os tipos de dados e operações de consulta com suporte, lendo [Time Series Expression (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Exemplos de cláusulas Onde

  [![Onde obter exemplos da cláusula](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Medida**: Uma lista suspensa que exibe todas as colunas numéricas (**duplos**) você pode usar como elementos do gráfico atual.

**Dividir por**: Essa lista suspensa exibe todas as colunas categóricas disponíveis (cadeias de caracteres) em seu modelo que você pode agrupar os dados. Adicione até cinco termos a serem exibidos no mesmo eixo X. Insira os parâmetros desejados e, em seguida, selecione **Adicionar** para adicionar um novo termo.

  [![Exibição filtrada e consultada um](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Você pode mostrar e ocultar elementos no painel de gráfico, selecionando o ícone visível, conforme mostrado na imagem a seguir. Você pode remover completamente a consultas, clicando em vermelha **X**.

  [![Exibição filtrada e consultada dois](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [armazenamento e a entrada](./time-series-insights-update-storage-ingress.md) na visualização de Insights de série de tempo do Azure.

- Leia o documento de visualização de Insights de série de tempo sobre [modelagem de dados](./time-series-insights-update-tsm.md).

- Saiba mais [como diagnosticar e solucionar problemas de](./time-series-insights-update-how-to-troubleshoot.md) sua instância do Time Series Insights.
