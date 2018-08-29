---
title: Visão geral do processo de design para acústica - Serviços Cognitivos
description: Este documento descreve como expressar sua intenção de projeto em todas as três fases do fluxo de trabalho Acústica do Projeto.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 3b35f78d66a6ae66b0a56818f5d4be455ce00de5
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40181094"
---
# <a name="design-process-overview"></a>Visão geral do processo de design
Você pode expressar sua intenção de design em todas as três fases do fluxo de trabalho do Project Acoustics: design pré-cozedura, posicionamento da fonte de som e design pós-cozedura. O processo requer menos marcação associada à colocação de volumes de reverberação, ao mesmo tempo em que retém o controle do designer sobre como uma cena é reproduzida.

## <a name="pre-bake-design"></a>Design de pré-tortas
O processo de projeto pré-cozedura produz a cena e os metadados que são usados para a simulação da onda sonora, que inclui a seleção de quais elementos da cena participarão da simulação para fornecer oclusões, reflexões e reverberação. Os metadados para a cena são a seleção de materiais acústicos para cada elemento de cena. Os materiais acústicos controlam a quantidade de energia sonora refletida de volta de cada superfície.

O coeficiente de absorção padrão para todas as superfícies é 0,04, que é altamente refletiva. Você pode obter efeitos estéticos e de jogabilidade ajustando os coeficientes de absorção de diferentes materiais ao longo da cena, que são especialmente proeminentes para os ouvintes quando eles ouvem as transições de uma área da cena para outra. Por exemplo, a transição de uma sala reverberant escura para brilhante, cena ao ar livre não reverberant aprimora o impacto da transição. Para conseguir este efeito, ajuste os coeficientes de absorção nos materiais da cena externa mais acima.

O tempo de reverberação de um determinado material em uma sala é inversamente relacionado ao seu coeficiente de absorção, com a maioria dos materiais tendo valores de absorção na faixa de 0,01 a 0,20. Materiais com coeficientes de absorção fora desta faixa são muito absorventes.

![Gráfico de tempo de reverberação](media/ReverbTimeGraph.png)

A [bake UI walk através de](bake-ui-walkthrough.md) descreve detalhadamente os controles de pré-cozedura.

## <a name="sound-source-placement"></a>Posicionamento de fonte de som
A visualização de voxels e pontos de sondagem em tempo de execução pode ajudar a depurar problemas com fontes de som presas dentro da geometria voxelizada. Para alternar a exibição da grade do voxel e dos pontos da sonda, clique na caixa de seleção correspondente no menu Gizmos, no canto superior direito da visualização da cena. Se a fonte de som está dentro de uma parede voxel, mova-o para um voxel ar.

![Menu gizmos](media/GizmosMenu.png)  

A exibição do voxel pode ajudar a determinar se os componentes visuais do jogo têm uma transformação aplicada a eles. Em caso afirmativo, aplique a mesma transformação ao GameObject que hospeda o **Acoustics Manager**.

## <a name="post-bake-design"></a>Design de pós-tortas
Os resultados do Bake são armazenados no arquivo ACE como parâmetros de oclusão e reverberação para todos os pares de localização do ouvinte de origem em toda a cena. Esse resultado fisicamente preciso pode ser usado para seu projeto como está e é um ótimo ponto de partida para o design. O processo de design pós-cozimento especifica regras para transformar os parâmetros de resultado do cozimento no tempo de execução.

### <a name="distance-based-attenuation"></a>Atenuação de distância
O DSP de áudio fornecido pelo **plug-in de spatializer da Microsoft Acoustics** respeita a atenuação baseada em distância por origem incorporada no Unity Editor. Controles de atenuação de distância estão na **Audio Source** componente encontrado na **Inspetor** fontes de painel de som, em **configurações de som 3D**:

![Atenuação de distância](media/distanceattenuation.png)

### <a name="tuning-scene-parameters"></a>Ajustar parâmetros de cena
Para ajustar os parâmetros de todas as fontes, clique na faixa do canal no **Audio Mixer** da Unity e ajuste os parâmetros no efeito **Acoustics Mixer**.

![Personalização do Mixer](media/MixerParameters.png)

### <a name="tuning-source-parameters"></a>Ajustar parâmetros de origem
Anexar o script **AcousticsSourceCustomization** a uma fonte ativa os parâmetros de ajuste para essa origem. Para anexar o script, clique em **Add Component** na parte inferior do painel **Inspector** e navegue até **Scripts> Acoustics Source Customization**. O script tem três parâmetros:

![Personalização de fonte](media/SourceCustomization.png)

* **Ajuste de potência de reverberação** - Ajusta a potência de reverberação em dB. Valores positivos emita um som mais reverberant, enquanto valores negativos emita um som mais dry.
* **Escala de tempo de decay** -ajusta um multiplicador para a hora de declínio. Por exemplo, se o resultado do coeficiente especificar um tempo de decaimento de 750 milissegundos, mas esse valor estiver definido como 1,5, o tempo de decaimento aplicado à fonte será de 1.125 milissegundos.
* **Ativar Acústica** - Controla se a acústica é aplicada a essa fonte. Quando desmarcada, a fonte será espacializada com HRTFs, mas sem acústica, ou seja, sem obstrução, oclusão e parâmetros de reverberação dinâmica, como nível e tempo de decaimento. A reverberação ainda é aplicada com um nível fixo e tempo de decaimento.

Fontes diferentes podem exigir configurações diferentes para alcançar certos efeitos estéticos ou de jogabilidade. Caixa de diálogo é um exemplo possível. O ouvido humano está mais sintonizado com a reverberação na fala, enquanto o diálogo muitas vezes precisa ser inteligível para o jogo. Você pode explicar isso sem tornar a caixa de diálogo não-diegética ajustando a energia de reverberação para baixo.
