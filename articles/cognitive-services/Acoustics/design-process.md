---
title: Visão geral do processo de design para Project Acoustics
titlesuffix: Azure Cognitive Services
description: Este documento descreve como expressar sua intenção de projeto em todas as três fases do fluxo de trabalho Acústica do Projeto.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: bb5f309a96feac2caea85fbe81b7216eecfc4b79
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873930"
---
# <a name="design-process-overview"></a>Visão geral do processo de design
Você pode expressar sua intenção de design em todas as três fases do fluxo de trabalho do Project Acoustics: configuração de cena pré-bake, posicionamento da fonte de som e design pós-bake. O processo requer menos marcação associada à colocação de volumes de reverberação, ao mesmo tempo em que retém o controle do designer sobre como uma cena é reproduzida.

## <a name="pre-bake-design"></a>Design de pré-tortas
O processo de configuração de cena pré-bake produz a cena e os metadados que são usados para a simulação da onda sonora, que inclui a seleção de quais elementos da cena participarão da simulação para fornecer oclusões, reflexões e reverberação. Os metadados para a cena são a seleção de materiais acústicos para cada elemento de cena. Os materiais acústicos controlam a quantidade de energia sonora refletida de volta de cada superfície.

O coeficiente de absorção padrão para todas as superfícies é 0,04, que é altamente refletiva. Você pode obter efeitos estéticos e de jogabilidade ajustando os coeficientes de absorção de diferentes materiais ao longo da cena, que são especialmente proeminentes para os ouvintes quando eles ouvem as transições de uma área da cena para outra. Por exemplo, a transição de uma sala reverberant escura para brilhante, cena ao ar livre não reverberant aprimora o impacto da transição. Para conseguir este efeito, ajuste os coeficientes de absorção nos materiais da cena externa mais acima.

O tempo de reverberação de um determinado material em uma sala é inversamente relacionado ao seu coeficiente de absorção, com a maioria dos materiais tendo valores de absorção na faixa de 0,01 a 0,20. Materiais com coeficientes de absorção fora desta faixa são muito absorventes.

![Gráfico de tempo de reverberação](media/ReverbTimeGraph.png)

A [bake UI walk através de](bake-ui-walkthrough.md) descreve detalhadamente os controles de pré-cozedura.

## <a name="sound-source-placement"></a>Posicionamento de fonte de som
A visualização de voxels e pontos de sondagem em tempo de execução pode ajudar a depurar problemas com fontes de som presas dentro da geometria voxelizada. Para alternar a exibição da grade do voxel e dos pontos da sonda, clique na caixa de seleção correspondente no menu Gizmos, no canto superior direito da visualização da cena. Se a fonte de som está dentro de uma parede voxel, mova-o para um voxel ar.

![Menu gizmos](media/GizmosMenu.png)  

A exibição do voxel pode ajudar a determinar se os componentes visuais do jogo têm uma transformação aplicada a eles. Em caso afirmativo, aplique a mesma transformação ao GameObject que hospeda o **Acoustics Manager**.

### <a name="voxel-size-discrepancies"></a>Discrepâncias de tamanho de voxel
O tamanho dos voxels usados para ilustrar quais das malhas de cena participam do bake acústico é diferente nas visualizações de tempo de design e tempo de execução. Essa diferença não afeta a qualidade/granularidade da frequência de simulação selecionada, mas é um subproduto do uso de tempo de execução da cena com voxels. No tempo de execução, os voxels de simulação são “refinados” para dar suporte à interpolação entre os locais de origem. Isso também permite o posicionamento do tempo de design de fontes de som mais próximas das malhas da cena do que o tamanho do voxel de simulação permite – já que as fontes dentro de um voxel que contêm uma malha tratada acusticamente não produzem nenhum som.

Veja aqui duas imagens mostrando a diferença entre voxels de design (pré-bake) e voxels de tempo de execução (pós-bake), conforme visualizado pelo plug-in Unity:

Voxels de tempo de design:

![VoxelsDesignTime](media/VoxelsDesignTime.png)

Voxels de tempo de execução:

![VoxelsRuntime](media/VoxelsRuntime.png)

A decisão sobre se a malha de voxel representa ou não com precisão as malhas de arquitetura / cena estrutural deve ser feita usando os voxels de modo de design, não a visualização de tempo de execução dos voxels refinados.

## <a name="post-bake-design"></a>Design de pós-tortas
Os resultados do Bake são armazenados no arquivo ACE como parâmetros de oclusão e reverberação para todos os pares de localização do ouvinte de origem em toda a cena. Esse resultado fisicamente preciso pode ser usado para seu projeto como está e é um ótimo ponto de partida para o design. O processo de design pós-cozimento especifica regras para transformar os parâmetros de resultado do cozimento no tempo de execução.

### <a name="distance-based-attenuation"></a>Atenuação de distância
O DSP de áudio fornecido pelo **plug-in de spatializer da Microsoft Acoustics** respeita a atenuação baseada em distância por origem incorporada no Unity Editor. Controles de atenuação de distância estão na **Audio Source** componente encontrado na **Inspetor** fontes de painel de som, em **configurações de som 3D**:

![Atenuação de distância](media/distanceattenuation.png)

A acústica executa o cálculo em uma caixa de “região da simulação” centralizada em torno do local do player. Se uma fonte de som estiver distante do player, localizada fora dessa região de simulação, somente a geometria dentro da caixa afetará a propagação do som (como causar oclusão), o que funciona razoavelmente bem quando os oclusores estão nas proximidades do player. No entanto, nos casos em que o player está em espaço aberto, mas os oclusores estão perto da fonte de som distante, o som pode passar por uma desoclusão irreal. A solução sugerida é garantir, em tais casos, que a atenuação do som caia para 0 a cerca de 45 m, a distância horizontal padrão do player em relação à borda da caixa.

### <a name="tuning-scene-parameters"></a>Ajustar parâmetros de cena
Para ajustar os parâmetros de todas as fontes, clique na faixa do canal no **Mixer de Áudio** do Unity e ajuste os parâmetros no efeito **Mixer do Projeto Acústico**.

![Personalização do Mixer](media/MixerParameters.png)

* **Ajuste de Umidade** – ajusta o poder de reverberação, em dB, em todas as origens na cena com base na distância entre o ouvinte e a origem. Valores positivos emita um som mais reverberant, enquanto valores negativos emita um som mais dry.
* **Escala de RT60** – escalar multiplicativo para tempo de reverberação.
* **Usar o Balanço** – controla se o áudio será gerado como binaural (0) ou balanço multicanal (1). Qualquer valor além de 1 indica binaural. A saída binaural é espacializada com HRTFs para uso com fones de ouvido, enquanto a saída multicanal é espacializada com VBAP para uso com sistemas de alto-falantes surround multicanais. Se estiver usando o panner multicanal, não deixe de selecionar o modo de alto-falante que corresponde às configurações do dispositivo, encontradas em **Configurações do Projeto** > **Áudio**.

![SpeakerMode](media/SpeakerMode.png)

### <a name="tuning-source-parameters"></a>Ajustar parâmetros de origem
Anexar o script **AcousticsAdjust** a uma fonte ativa os parâmetros de ajuste para essa origem. Para anexar o script, clique em **Add Component** na parte inferior do painel **Inspector** e navegue até **Scripts > Acoustics Adjust**. O script tem seis controles:

![AcousticsAdjust](media/AcousticsAdjust.png)

* **Ativar Acústica** - Controla se a acústica é aplicada a essa fonte. Quando desmarcada, a origem será espacializada com HRTFs ou movimento panorâmico, mas não haverá nenhuma acústica. Isso significa que não haverá nenhum parâmetro de obstrução, oclusão e reverberação dinâmica, tais como nível e tempo de decaimento. A reverberação ainda é aplicada com um nível fixo e tempo de decaimento.
* **Occlusion** – aplique um multiplicador ao nível de dB de oclusão calculado pelo sistema acústico. Se esse multiplicador for maior que 1, a oclusão será exagerada, enquanto valores menores que 1 tornam o efeito de oclusão mais sutil e um valor 0 desabilita a oclusão.
* **Transmission (dB)** – defina a atenuação (em dB) causada pela transmissão por meio de geometria. Defina esse controle deslizante para o nível mais baixo para desabilitar a transmissão. A acústica espacializa o áudio seco inicial ao chegar em torno da geometria da cena (portaling). A transmissão fornece uma chegada seca adicional que é espacializada na direção da linha de visão. A curva de atenuação de distância da fonte também é aplicada.
* **Wetness (dB)** – ajusta a potência de reverberação, em dB, de acordo com a distância da fonte. Valores positivos emita um som mais reverberant, enquanto valores negativos emita um som mais dry. Clique no controle de curva (linha verde) para abrir o editor de curvas. Para modificar a curva, clique com o botão esquerdo do mouse para adicionar pontos e arraste-os para formar a função desejada. O eixo x é a distância da fonte e o eixo y é o ajuste de reverberação em dB. Para obter mais informações sobre como editar curvas, confira este [Manual do Unity](https://docs.unity3d.com/Manual/EditingCurves.html). Para redefinir a curva ao padrão, clique com o botão direito do mouse em **Wetness** e selecione **Reset**.
* **Escala de tempo de decay** -ajusta um multiplicador para a hora de declínio. Por exemplo, se o resultado do coeficiente especificar um tempo de decaimento de 750 milissegundos, mas esse valor estiver definido como 1,5, o tempo de decaimento aplicado à fonte será de 1.125 milissegundos.
* **Outdoorness** – um ajuste complementar à estimativa do sistema acústico sobre o modo como a reverberação em uma fonte soa “ao ar livre”. Definir esse valor para 1 fará com que uma fonte sempre soe como ao ar livre, enquanto a definição para -1 fará com que a fonte soe como interna.

Fontes diferentes podem exigir configurações diferentes para alcançar certos efeitos estéticos ou de jogabilidade. Caixa de diálogo é um exemplo possível. O ouvido humano está mais sintonizado com a reverberação na fala, enquanto o diálogo muitas vezes precisa ser inteligível para o jogo. Você pode conseguir isso sem tornar a caixa de diálogo não diegética, movendo **Wetness** para baixo, ajustando o parâmetro **Perceptual Distance Warp** descrito abaixo, adicionando **Transmission** para algum aumento de áudio seco propagado através das paredes e/ou reduzindo a **Occlusion** definida como 1 para que chegue mais som pelos portais.

Anexe o script **AcousticsAdjustExperimental** a uma fonte ativa parâmetros adicionais de ajuste experimental para essa fonte. Para anexar o script, clique em **Add Component** na parte inferior do painel **Inspector** e navegue até **Scripts > Acoustics Adjust Experimental**. Atualmente, há um controle experimental:

![AcousticsAdjustExperimental](media/AcousticsAdjustExperimental.png)

* **Perceptual Distance Warp** – aplique uma distorção exponencial à distância usada para calcular a relação seco-úmido. O sistema acústico calcula os níveis de umidade em todo o espaço, que variam levemente com a distância e fornecem pistas de distância perceptiva. Valores de distorção maiores que 1 exageram esse efeito, aumentando os níveis de reverberação relacionada à distância e tornando o som "distante". Valores de distorção inferiores a 1 tornam a alteração à reverberação com base em distância mais sutil, tornando o som mais "presente".

