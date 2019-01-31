---
title: O que é Projeto Acústico?
titlesuffix: Azure Cognitive Services
description: O plug-in Project Acoustics do Unity fornece oclusão, reverberação e espacialização para projetos de VR e telas tradicionais.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 175203269f524f5c680345602e6382f5c26f1bd1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178895"
---
# <a name="what-is-project-acoustics"></a>O que é Projeto Acústico?
O plug-in Project Acoustics do Unity fornece oclusão, reverberação e espacialização para projetos de VR e telas tradicionais. Ele fornece uma maneira para criar acústica nos jogos que sobrepõe-se às intenções do designer em relação a uma simulação física de onda.

## <a name="why-use-acoustics-in-virtual-environments"></a>Por que usar acústica em ambientes virtuais?
Os seres humanos usam indicações audiovisuais para entender o mundo ao redor deles. Em mundos virtuais, combinar áudio espacial com acústica aumenta a imersão do usuário. A ferramenta acústica descrita aqui analisa mundos virtuais para criar uma simulação realista de acústica e dá suporte a um processo de design pós-simulação. A análise inclui a geometria e os materiais para cada superfície no mundo. A simulação inclui parâmetros, como a direção de chegada (portaling), intensidade da reverberação, tempos de declínio e efeitos de oclusão e obstrução.

## <a name="how-does-this-approach-to-acoustics-work"></a>Como essa abordagem para acústica funciona?
O sistema se baseia em uma computação offline do mundo virtual, que permite uma simulação mais complexa que se a análise fosse feita em tempo de execução. A computação offline produz uma tabela de pesquisa de acústicos parâmetros. Um designer especifica as regras aplicadas aos parâmetros em tempo de execução. Ajustar essas regras pode resultar em efeitos hiper-realistas para alta intensidade emocional ou cenas hipo-realista para obter mais sons de áudio em segundo plano.

## <a name="design-process-comparison"></a>Comparação do processo de design
O plug-in Project Acoustics dá suporte a um novo processo de design para acústica em cenas de Unity. Para explicar esse novo processo de design, vamos compará-lo a uma das abordagens atuais populares para acústica.

### <a name="typical-approach-to-acoustics-today"></a>Abordagem típica para acústica hoje
Em uma abordagem típica para acústica de hoje em dia, você desenha volumes de reverberação:

![Modo de exibição de Design](media/reverbZonesAltSPace2.png)

Em seguida, ajusta os parâmetros para cada zona:

![Modo de exibição de Design](media/TooManyReverbParameters.png)

Por fim, adiciona lógica de ray tracing para obter a filtragem correta de oclusão e obstrução ao longo da cena e lógica de pesquisa do caminho para portaling. Esse código pode resultar em um custo adicional de tempo de execução. Ele também apresenta problemas com suavidade em cantos e possui casos de bordas em cenas com formas irregulares.

### <a name="an-alternative-approach-with-physics-based-design"></a>Uma abordagem alternativa com design baseado em física
Com a abordagem fornecida pelo plug-in Project Acoustics do Unity, você pode fornecer materiais e forma para uma cena estática. Como a cena possui voxels e o processo não usa o ray tracing, você não precisa fornecer uma malha acústica simplificada ou precisa. Também não é necessário marcar a cena com volumes de reverberação. O plug-in carrega a cena para a nuvem, onde é usada a simulação física de onda. O resultado é integrado ao seu projeto como uma tabela de pesquisa e pode ser modificado para efeitos de estética ou jogabilidade.

![Modo de exibição de Design](media/GearsWithVoxels.jpg)

## <a name="requirements"></a>Requisitos
* Unity 2018.2+ para preparação acústica e Unity 5.2+ para implantação e design de som
* Editor de Unity de 64 bits do Windows
* Assinatura do Lote do Azure para preparação acústica
* O tempo de execução de script do Unity deve ser definido como “equivalente ao .NET 4.x”

## <a name="platform-support"></a>Suporte à plataforma
* Área de trabalho do Windows (x86 e AMD64)
* UWP do Windows (x86, AMD64 e ARM)
* Android (x86 e ARM64)

## <a name="download"></a>Baixar
Se você estiver interessado em avaliar o plug-in de acústica, registre-se [aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) para participar da Visualização do Designer.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [processo de design](design-process.md)
* Introdução ao [integrando acústica em seu projeto do Unity](getting-started.md)

