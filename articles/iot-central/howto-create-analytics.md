---
title: Criar análises personalizadas para o aplicativo Azure IoT Central | Microsoft Docs
description: Como um operador, saiba como criar análises personalizadas para o aplicativo Azure IoT Central.
author: lmasieri
ms.author: lmasieri
ms.date: 08/26/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0a78c534605b6eab08d5b12674689f0459e80b26
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247089"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Como usar análise para analisar os dados do dispositivo

O Microsoft Azure IoT Central fornece recursos avançados de análise para analisar grandes quantidades de dados de dispositivos. Para começar, visite **Análise** no menu de navegação à esquerda. 

  ![Navegação do IoT Central para análise](media\howto-create-analytics\analytics-navigation.png)

## <a name="querying-your-data"></a>Consultando seus dados

Você precisará escolher um **conjunto de dispositivos**, adicionar um **filtro** (opcional) e selecionar um **período de tempo** para começar. Ao terminar, clique em *Mostrar resultados* para começar a visualizar seus dados.


* **Conjuntos de dispositivos:** um [conjunto de dispositivo](howto-use-device-sets.md) é um grupo de dispositivos definido pelo usuário. Por exemplo, todos os refrigeradores em Oakland ou todas as turbinas eólicas rev 2.0.

<!---
to-do: confirm if 10 is the max number of filters
to-do: do we need to explain how fiters work?
--->

* **Filtros:** opcionalmente, você pode adicionar filtros à pesquisa para apurar ainda mais os dados. Você pode adicionar até 10 filtros por vez. Por exemplo, em todos os refrigeradores em Oakland, encontre aqueles que apresentaram temperatura acima de 60 graus. 
* **Período de tempo:** por padrão, recuperaremos dados dos últimos 10 minutos. Você pode alterar esse valor para um dos intervalos de tempo predefinidos ou selecionar um período de tempo personalizado. 

 ![Consulta de análise](media\howto-create-analytics\analytics-query.png)

## <a name="visualizing-your-data"></a>Visualizando seus dados

Depois de consultar seus dados, você poderá começar a visualizá-los. Você pode mostrar/ocultar medidas, alterar a maneira como os dados são agregados e dividir os dados ainda mais pelas diferentes propriedades do dispositivo.  

* **Dividir por:** dividir os dados pelas propriedades do dispositivo permite que você faça uma busca mais detalhada nos dados. Por exemplo, você pode dividir os resultados por ID ou local do dispositivo.
<!---
to-do: confirm if 10 is the max number of measurements
--->
* **Medições:** você pode optar por mostrar/ocultar até 10 itens de telemetria diferentes que estão sendo relatados por seus dispositivos por vez. As medidas são itens como temperatura e umidade. 
* **Agregação:** por padrão, podemos agregar dados pela média, mas você pode alterar a agregação de dados para outra coisa para atender às suas necessidades. 

   ![Visualização de análise](media\howto-create-analytics\analytics-visualize.png) <br/><br/>
   ![Visualização de análise – dividir por](media\howto-create-analytics\analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interagindo com seus dados

Há várias maneiras em que você pode alterar ainda mais os resultados da consulta para atender às suas necessidades de visualização. É possível alternar entre um modo de exibição de gráficos e uma exibição em grade, ampliar/reduzir, atualizar o conjunto de dados e alterar como as linhas são mostradas.

* **Mostrar grade:** seus resultados ficarão disponíveis no formato de tabela permitindo que você exiba o valor específico de cada ponto de dados. Essa exibição também atende aos padrões de acessibilidade. 
* **Mostrar gráfico:** seus resultados serão exibidos em um formato de linha para exibir facilmente tendências em alta e em baixa e anomalias. 

 ![Mostrando a exibição em grade da sua análise](media\howto-create-analytics\analytics-showgrid.png)

O zoom permite que você apure ainda mais os dados. Se você encontrar um período de tempo no qual deseja se concentrar em seu conjunto de resultados, usando o cursor, capture a área que deseja ampliar e use os controles disponíveis para executar uma das seguintes ações:
* **Ampliar:** depois de selecionar um período de tempo, a ampliação será habilitada e permitirá que você amplie seus dados.
* **Reduzir:** esse controle permite que você reduza um nível em relação ao último zoom. Por exemplo, se você ampliar os dados três vezes, a redução retrocederá uma etapa por vez.
* **Redefinir zoom:** depois de experimentar vários níveis de zoom, você pode usar o controle de redefinição de zoom para retornar ao conjunto de resultados original. 

 ![Executando zoom em seus dados](media\howto-create-analytics\analytics-zoom.png)


Você pode alterar o estilo da linha para atender às suas necessidades. Há quatro opções à sua escolha:
* **Linha:** uma linha reta entre cada um dos pontos de dados será formada. 
* **Suave:** uma linha curva entre cada ponto será formada
* **Etapa:** uma linha entre cada ponto no gráfico criará um gráfico de etapas
* **Dispersão:** todos os pontos serão plotados no gráfico sem linhas conectando-os. 

 ![Diferentes tipos de linha disponíveis na análise](media\howto-create-analytics\analytics-linetypes.png)

Por fim, você pode organizar seus dados ao longo do eixo y, escolhendo um dos três modos:

* **Empilhado:** um gráfico para cada medida é empilhado e cada um dos gráficos tem seu próprio eixo Y. Os gráficos empilhados são úteis quando você tem várias medidas selecionadas e deseja ter uma exibição distinta dessas medidas.
* **Não empilhado:** um gráfico para cada medida é plotado em relação a um eixo Y, mas os valores para o eixo Y são alterados com base na medida realçada. Gráficos não empilhados são úteis quando você deseja sobrepor várias medidas e deseja ver padrões nessas medidas para o mesmo intervalo de tempo.
* **Eixo Y compartilhado:** todos os gráficos compartilham o mesmo eixo Y e os valores do eixo não são alterados. Os gráficos compartilhados do eixo Y são úteis quando você deseja examinar uma única medida ao fatiar os dados com a opção dividir por.

 ![Organizar dados ao longo do eixo y com diferentes modos de visualização](media\howto-create-analytics\analytics-yaxis.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a criar análises personalizadas para o aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Preparar e conectar um aplicativo Node.js](howto-connect-nodejs.md)