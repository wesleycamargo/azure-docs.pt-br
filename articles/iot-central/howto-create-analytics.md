---
title: Criar análises personalizadas para o aplicativo Azure IoT Central | Microsoft Docs
description: Como um operador, saiba como criar análises personalizadas para o aplicativo Azure IoT Central.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5facdf3f02b71e154a23d8f26c7bcc40b5c71e35
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629296"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Como usar análise para analisar os dados do dispositivo

O Microsoft Azure IoT Central fornece recursos avançados de análise para analisar grandes quantidades de dados de dispositivos. É possível usar a análise para exibir e analisar dados de um [conjunto de dispositivos](howto-use-device-sets.md) no aplicativo. Um conjunto de dispositivos é um grupo dos dispositivos definido pelo usuário. É possível restringir a análise a um pequeno conjunto de dispositivos ou a um único dispositivo.

Como operador, escolha **Análise** no menu de navegação esquerdo, selecione um conjunto de dispositivos e escolha as medidas a serem exibidas no gráfico. A seguir, são apresentadas algumas ferramentas que podem ser utilizadas para fatiar ainda mais os dados:

* **Medidas:** escolha as medidas a serem exibidas, como temperatura e umidade.
* **Agregação:** escolha a função de agregação para as medidas. Por exemplo, **Mínimo** ou **Médio**.
* **Dividir por:** detalhe dividindo os dados por propriedades do dispositivo ou nome do dispositivo. Por exemplo, dividir por local do dispositivo:

     ![Página principal de análise](media\howto-create-analytics\analytics-main.png)

* **Intervalo de tempo:** é possível escolher o período de um dos intervalos de tempo predefinidos ou criar um período personalizado:   ![Intervalo de tempo de análise](media\howto-create-analytics\analytics-time-range.png)

## <a name="change-the-visualizations"></a>Alterar as visualizações

É possível alterar os gráficos para atender aos requisitos de visualização, escolhendo um dos três modos:

* **Empilhado:** um gráfico para cada medida é empilhado e cada um dos gráficos tem seu próprio eixo Y. Os gráficos empilhados são úteis quando você tem várias medidas selecionadas e deseja ter uma exibição distinta dessas medidas.
* **Não empilhado:** um gráfico para cada medida é plotado em relação a um eixo Y, mas os valores para o eixo Y são alterados com base na medida realçada. Gráficos não empilhados são úteis quando você deseja sobrepor várias medidas e deseja ver padrões nessas medidas para o mesmo intervalo de tempo.
* **Eixo Y compartilhado:** todos os gráficos compartilham o mesmo eixo Y e os valores do eixo não são alterados. Os gráficos compartilhados do eixo Y são úteis quando você deseja examinar uma única medida ao fatiar os dados com a opção dividir por.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a criar análises personalizadas para o aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Preparar e conectar um aplicativo Node.js](howto-connect-nodejs.md)