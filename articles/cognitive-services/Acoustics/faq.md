---
title: Perguntas frequentes sobre o Project Acoustics
titlesuffix: Azure Cognitive Services
description: Esta página fornece respostas a perguntas frequentes sobre o Project Acoustics, incluindo instruções de download e processo de cozedura.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: 12dda5d7af1236ec2d7eddbe025b8fdba47d7bca
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881067"
---
# <a name="frequently-asked-questions"></a>Perguntas frequentes

## <a name="what-is-project-acoustics"></a>O que é Projeto Acústico?

O plugin Project Acoustics Unity é um sistema acústico que calcula o comportamento das ondas sonoras antes do tempo de execução, semelhante à iluminação estática. A nuvem faz o trabalho pesado dos cálculos de física de onda, portanto, o custo da CPU de tempo de execução é baixo.  

## <a name="where-can-i-download-the-plugin"></a>Onde posso baixar o plug-in?

Se você estiver interessado em avaliar o plug-in de acústica, registre [aqui](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u) para participar da Visualização do Designer.

## <a name="is-azure-used-at-runtime"></a>O Azure é usado em tempo de execução?

Não, a integração na nuvem é usada apenas durante o estágio de pré-computação que faz parte da configuração da cena.
 
## <a name="what-is-simulation-input"></a>O que é a entrada de simulação? 

A entrada da simulação é sua cena 3D, ambiente virtual ou nível do jogo. O Project Acoustics realiza simulações de ondas volumétricas em 3D que modelam a física do som de perto, incluindo a oclusão e o espalhamento suaves.
 
## <a name="what-is-the-runtime-cost"></a>O que é o custo de tempo de execução?

Acústica leva aproximadamente 0,01% da CPU por origem por quadro. Uso de RAM depende do tamanho da cena e pode variar de 10 a 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Preciso simplificar a geometria do nível? Contagem do triângulo de controle? Verifique as malhas watertight?

 Não. O sistema ingerirá geometria de nível detalhada diretamente. Será voxelizado para processamento interno.
 
## <a name="whats-in-the-runtime-lookup-table"></a>O que há na tabela de consulta de tempo de execução?

O arquivo ACE é uma tabela de parâmetros acústicos entre vários pares de locais de origem e ouvinte.
 
## <a name="can-it-handle-moving-sources"></a>Ele pode lidar com movimentação fontes?

Sim, o plugin Unity spatializer da **Microsoft Acoustics** consulta a tabela de pesquisa em cada escala de processamento de áudio com as localizações atuais da fonte e do listener. DSP do spatializer suavemente atualiza os parâmetros de processamento acústico em todas as marcas.
 
## <a name="can-it-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Pode lidar com geometria dinâmica? Fechando portas? Paredes surpreso?

 Não. Os parâmetros acústicos são pré-computados com base no estado estático de um nível de jogo. Sugerimos deixar a geometria da porta fora da acústica e, em seguida, aplicar oclusão adicional com base no estado de objetos de jogo destrutíveis e móveis usando técnicas estabelecidas.
 
## <a name="does-it-handle-materials"></a>Ele manipula materiais?

Sim. Os materiais são escolhidos a partir dos nomes dos materiais físicos em seu nível, impulsionando a absorção.
 
## <a name="what-do-the-probes-represent"></a>O que representam os "testes"?

As sondas são uma amostra dos possíveis locais dos jogadores. Cada sonda representa uma simulação de onda separada da cena originada no local da sonda. Em tempo de execução, os parâmetros acústicos para o local do ouvinte são interpolados a partir dos locais de sonda próximos.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Por que gastar muito computação na nuvem? O que isso me compra?

O Project Acoustics fornece parâmetros acústicos precisos e confiáveis, mesmo para ambientes virtuais ultra-complexos, levando em consideração todos os aspectos arquitetônicos. Ele fornece oclusão / obstrução suave sem todo o trabalho manual e variação de reverberação dinâmica sem volumes de desenho. Tudo isso enquanto o restante de luz na CPU durante o tempo de execução.

## <a name="what-exactly-happens-during-baking"></a>Exatamente, o que acontece durante "trazendo"?

O sistema considera locais potenciais do jogador para gerar um conjunto de posições de amostra "sonda" uniformemente espaçadas. Um bake para um nível consiste em tarefas independentes para cada investigação: O sistema considera uma "Região de Simulação" cuboide centralizada na investigação e faz uma simulação de onda detalhada dentro dessa região com resolução de até 25 cm.

## <a name="next-steps"></a>Próximas etapas
* Explore o [cena de exemplo](sample-walkthrough.md)

