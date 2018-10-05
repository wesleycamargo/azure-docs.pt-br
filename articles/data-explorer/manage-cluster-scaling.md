---
title: Escala do cluster do Azure Data Explorer para acomodar as mudanças de demanda
description: Este artigo descreve as etapas de expansão e redução de um cluster do Azure Data Explorer conforme as mudanças de demanda.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 59a6a94e2906413423a4ae03a7c1c115b2ec0cc0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47045532"
---
# <a name="manage-cluster-scaling-to-accommodate-changing-demand"></a>Gerenciar a escala do cluster para acomodar as mudanças de demanda

Dimensionar um cluster apropriadamente é essencial para o desempenho do Azure Data Explorer. Contudo, não é possível prever a demanda em um cluster com 100% de precisão. Um tamanho de cluster estático pode levar a subutilização ou superutilização, e nenhuma dessas condições é a ideal. Uma abordagem melhor é a *escala* de um cluster, adicionando e removendo a capacidade confirme as mudanças na demanda. Este artigo mostra como gerenciar a escala de um cluster.

Navegue para seu cluster e, em **Configurações**, selecione **Escalar horizontalmente**. Em **Configurar**, selecione **Habilitar dimensionamento automático**.

![Habilitar dimensionamento automático](media/manage-cluster-scaling/enable-autoscale.png)

O gráfico a seguir mostra o fluxo das próximas etapas. Fornecemos mais detalhes abaixo do gráfico.

![Regra de escala](media/manage-cluster-scaling/scale-rule.png)

1. Em **Nome da configuração de dimensionamento automático**, informe um nome, como *Expansão: utilização de cache*.

1. Em **Modo de escala**, selecione **Escala baseada em uma métrica**. Esse modo oferece escala automática. Também é possível selecionar **Escalar para uma contagem específica de instâncias**.

1. Selecione **Adicionar uma regra**.

1. Na seção **Regra de escala** à direita, informe os valores para cada configuração.

    | Configuração | Descrição e valor |
    | --- | --- | --- |
    | **Agregação de tempo** | Selecione um critério de agregação, como **Média**. |
    | **Nome da métrica** | Selecione a métrica na qual a operação de escala se baseará, como **Utilização de cache**. |
    | **Estatística de intervalo de agregação** | Escolha entre **Média**, **Mínima**, **Máxima** e **Soma**. |
    | **Operador** | Escolha a opção apropriada, como **Maior que ou igual a**. |
    | **Limite** | Escolha um valor apropriado. Por exemplo, para a utilização de cache, 80% é um bom ponto de partida. |
    | **Duração** | Escolha um período apropriado para o sistema retroceder ao calcular métricas. Comece com o padrão de dez minutos. |
    | **Operação** | Escolha a opção apropriada para reduzir ou escalar horizontalmente. |
    | **Contagem de instâncias** | Escolha o número de nós ou instâncias que você deseja adicionar ou remover quando uma condição de métrica for atendida. |
    | **Tempo de resfriamento (minutos)** | Escolha um intervalo de tempo apropriado de espera entre as operações de escala. Comece com o padrão de cinco minutos. |
    |  |  |

1. Selecione **Adicionar**.

1. Na seção **Limites de instância** à esquerda, informe os valores para cada configuração.

    | Configuração | Descrição e valor |
    | --- | --- | --- |
    | *Mínimo* | Este é o número mínimo de instâncias abaixo do qual o cluster não será escalado, independentemente da utilização. |
    | *Máximo* | Este é o número máximo de instâncias acima do qual o cluster não será escalado, independentemente da utilização. |
    | *Padrão* | O número padrão de instâncias, usado em caso de problema ao ler as métricas de recursos. |
    |  |  |

1. Clique em **Salvar**.

Agora, você configurou uma operação de expansão para o cluster do Azure Data Explorer. Adicione outra regra para uma operação de redução. Isso permite que seu cluster seja escalado dinamicamente conforme as métricas de utilização que você especificar.

Se você precisar de auxílio com problemas de escala, abra uma solicitação de suporte no [Portal do Azure](https://portal.azure.com).