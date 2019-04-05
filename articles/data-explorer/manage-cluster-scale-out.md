---
title: Escalar horizontalmente um cluster do Data Explorer do Azure para acomodar a demanda de alteração
description: Este artigo descreve as etapas para escalar horizontalmente e escalar em um cluster do Gerenciador de dados do Azure com base na demanda de alteração.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: ab4ced6695e6066098dd5ff7348528deedfc0e1b
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044227"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Gerenciar a expansão do cluster para acomodar as mudanças de demanda

Dimensionar um cluster apropriadamente é essencial para o desempenho do Azure Data Explorer. Mas por demanda em um cluster não pode ser prevista com exatidão absoluta. Um tamanho de cluster estático pode levar a subutilização ou superutilização, nenhum deles é ideal.

Uma abordagem melhor é a *escala* de um cluster, adicionando e removendo a capacidade confirme as mudanças na demanda. Há dois fluxos de trabalho para o dimensionamento: ampliação e expansão. Este artigo explica o fluxo de trabalho de dimensionamento horizontal.

Este artigo mostra como gerenciar o cluster escala horizontal, também conhecido como o dimensionamento automático. Dimensionamento automático permite que você escale horizontalmente a contagem de instâncias automaticamente com base em regras e agendamentos predefinidos. Especifique as configurações de dimensionamento automático para seu cluster no portal do Azure, conforme descrito neste artigo.

Vá para seu cluster. Sob **as configurações**, selecione **expandir**. Em **Configurar**, selecione **Habilitar dimensionamento automático**.

![Habilitar dimensionamento automático](media/manage-cluster-scaling/enable-autoscale.png)

O gráfico a seguir mostra o fluxo das próximas etapas. Mais detalhes estão abaixo do gráfico.

![Regra de escala](media/manage-cluster-scaling/scale-rule.png)

1. No **nome da configuração de AutoEscala** caixa, forneça um nome, como *Scale-out: utilização de cache*.

1. Para **modo de escala**, selecione **dimensionar com base em uma métrica**. Esse modo oferece dimensionamento dinâmico. Você também pode selecionar **dimensionar para uma contagem de instância específica**.

1. Selecione **+ adicionar uma regra**.

1. Na seção **Regra de escala** à direita, informe os valores para cada configuração.

    **Critérios**

    | Configuração | Descrição e valor |
    | --- | --- |
    | **Agregação de tempo** | Selecione um critério de agregação, como **Média**. |
    | **Nome da métrica** | Selecione a métrica na qual a operação de escala se baseará, como **Utilização de cache**. |
    | **Estatística de intervalo de agregação** | Escolha entre **Média**, **Mínima**, **Máxima** e **Soma**. |
    | **Operador** | Escolha a opção apropriada, como **Maior que ou igual a**. |
    | **Limite** | Escolha um valor apropriado. Por exemplo, para a utilização do cache, 80 por cento é um bom ponto de partida. |
    | **Duração (em minutos)** | Escolha um período apropriado para o sistema retroceder ao calcular métricas. Comece com o padrão de dez minutos. |
    |  |  |

    **Ação**

    | Configuração | Descrição e valor |
    | --- | --- |
    | **Operação** | Escolha a opção apropriada para reduzir ou escalar horizontalmente. |
    | **Contagem de instâncias** | Escolha o número de nós ou instâncias que você deseja adicionar ou remover quando uma condição de métrica for atendida. |
    | **Tempo de resfriamento (minutos)** | Escolha um intervalo de tempo apropriado de espera entre as operações de escala. Comece com o padrão de cinco minutos. |
    |  |  |

1. Selecione **Adicionar**.

1. Na seção **Limites de instância** à esquerda, informe os valores para cada configuração.

    | Configuração | Descrição e valor |
    | --- | --- |
    | **Mínimo** | É o número de instâncias para baixo do qual o cluster não será reduzido, independentemente da utilização. |
    | **Máximo** | É o número de instâncias para acima do qual o cluster não será aumentado, independentemente da utilização. |
    | **Padrão** | O número padrão de instâncias. Essa configuração é usada se houver problemas com as métricas de recursos de leitura. |
    |  |  |

1. Clique em **Salvar**.

Agora, você configurou uma operação de expansão para o cluster do Azure Data Explorer. Adicione outra regra para uma operação de redução. Essa configuração permite que seu cluster para dimensionar dinamicamente com base nas métricas que você especificar.

Você também pode [gerenciar o cluster de escala vertical](manage-cluster-scale-up.md) para o dimensionamento apropriado de um cluster.

Se precisar de ajuda com problemas de dimensionamento de cluster [abrir uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) no portal do Azure.
