---
title: Analisar dados do dispositivo em seu aplicativo do Azure IoT Central | Microsoft Docs
description: Analise os dados do dispositivo no aplicativo Central do IoT do Azure.
author: lmasieri
ms.author: lmasieri
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 851a509421924a2ea6607020e7749c10dcbec01d
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771720"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Como usar análise para analisar os dados do dispositivo


*Este artigo aplica-se a operadores, construtores e administradores.*


A Central de IoT do Azure fornece recursos avançados de análise para compreender grandes quantidades de dados de seus dispositivos. Para começar, visite **Análise** no menu de navegação à esquerda. 

## <a name="querying-your-data"></a>Consultando seus dados

Você precisará escolher um **conjunto de dispositivos**, adicionar um **filtro** (opcional) e selecionar um **período de tempo** para começar. Ao terminar, clique em *Mostrar resultados* para começar a visualizar seus dados.


* **Conjuntos de dispositivos:** Um [conjunto de dispositivos](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) é um grupo de dispositivos definido pelo usuário. Por exemplo, todos os refrigeradores em Oakland ou todas as turbinas eólicas rev 2.0.

<!---
to-do: confirm if 10 is the max number of filters
to-do: do we need to explain how fiters work?
--->

* **Filtros:** Opcionalmente, é possível adicionar filtros à pesquisa para aprimorar os dados. Você pode adicionar até 10 filtros por vez. Por exemplo, em todos os refrigeradores em Oakland, encontre aqueles que apresentaram temperatura acima de 60 graus. 
* **Período de tempo:** Por padrão, recuperaremos os dados dos últimos 10 minutos. Você pode alterar esse valor para um dos intervalos de tempo predefinidos ou selecionar um período de tempo personalizado. 

 ![Consulta de análise](media/howto-create-analytics-experimental/analytics-query.png)

## <a name="visualizing-your-data"></a>Visualizando seus dados

Depois de consultar seus dados, você poderá começar a visualizá-los. Você pode mostrar/ocultar medidas, alterar a maneira como os dados são agregados e dividir os dados ainda mais pelas diferentes propriedades do dispositivo.  

* **Dividir por:** Dividir os dados por propriedades de dispositivo permite fazer busca mais detalhada nos seus dados. Por exemplo, você pode dividir os resultados por ID ou local do dispositivo.
<!---
to-do: confirm if 10 is the max number of measurements
--->
* **Medidas:** Você pode optar por mostrar/ocultar até 10 itens de telemetria diferentes sendo informados por seus dispositivos por vez. As medidas são itens como temperatura e umidade. 
* **Agregação:** Por padrão, agregamos dados pela média, mas é possível optar por alterar a agregação de dados para algo que atenda às suas necessidades. 

   ![Visualização de análise - dividir por](media/howto-create-analytics-experimental/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Interagindo com seus dados

Há várias maneiras em que você pode alterar ainda mais os resultados da consulta para atender às suas necessidades de visualização. É possível alternar entre um modo de exibição de gráficos e uma exibição em grade, ampliar/reduzir, atualizar o conjunto de dados e alterar como as linhas são mostradas.

* **Mostrar grade:** Os resultados estarão disponíveis em um formato de tabela, permitindo que você visualize o valor específico para cada ponto de dados. Essa exibição também atende aos padrões de acessibilidade. 
* **Mostrar gráfico:** Os resultados serão exibidos em um formato de linha para exibir facilmente as tendências de alta e de baixa e anomalias. 

 ![Mostrando a exibição em grade da sua análise](media/howto-create-analytics-experimental/analytics-showgrid.png)

O zoom permite que você apure ainda mais os dados. Se você encontrar um período de tempo em que gostaria de se concentrar no conjunto de resultados, use o cursor para capturar a área que deseja ampliar e use os controles disponíveis para executar uma das seguintes ações:
* **Ampliar:** Após selecionar um período de tempo, a ampliação será habilitada e permitirá ampliar os dados.
* **Reduzir:** Esse controle permite que você reduza um nível em relação à última ampliação. Por exemplo, se você ampliar os dados três vezes, a redução retrocederá uma etapa por vez.
* **Redefinir Zoom:** Após realizar vários níveis de zoom, você poderá usar o controle redefinir zoom para retornar ao conjunto de resultados original. 

 ![Executando zoom em seus dados](media/howto-create-analytics-experimental/analytics-zoom.png)


Você pode alterar o estilo da linha para atender às suas necessidades. Há quatro opções à sua escolha:
* **Linha:** Uma linha plana entre cada um dos pontos de dados será formada. 
* **Suave:** Uma linha curva entre cada ponto será formada
* **Etapa:** A linha entre cada ponto no gráfico criará um gráfico de etapas
* **Dispersão:** Todos os pontos serão plotados no gráfico sem linhas conectando-os. 

 ![Diferentes tipos de linha disponíveis na análise](media/howto-create-analytics-experimental/analytics-linetypes.png)

Por fim, você pode organizar seus dados ao longo do eixo y, escolhendo um dos três modos:

* **Empilhado:** Um gráfico para cada medida é empilhado e cada um dos gráficos tem seu próprio eixo Y. Os gráficos empilhados são úteis quando você tem várias medidas selecionadas e deseja ter uma exibição distinta dessas medidas.
* **Fora da pilha:** Um gráfico para cada medida é plotado em relação a um eixo Y, mas os valores para o eixo Y são alterados com base na medida realçada. Gráficos não empilhados são úteis quando você deseja sobrepor várias medidas e deseja ver padrões nessas medidas para o mesmo intervalo de tempo.
* **Eixo compartilhado Y:** Todos os gráficos compartilham o mesmo eixo Y e os valores para o eixo não são alterados. Os gráficos compartilhados do eixo Y são úteis quando você deseja examinar uma única medida ao fatiar os dados com a opção dividir por.

 ![Organizar dados ao longo do eixo y com diferentes modos de visualização](media/howto-create-analytics-experimental/analytics-yaxis.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a criar análises personalizadas para o aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Preparar e conectar um aplicativo Node.js](howto-connect-nodejs-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)