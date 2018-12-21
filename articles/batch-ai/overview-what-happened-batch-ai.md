---
title: O que está acontecendo com IA do Lote do Azure? | Microsoft Docs
description: Saiba mais sobre o que está acontecendo com IA do Lote do Azure e a opção de computação do serviço do Azure Machine Learning.
services: batch-ai
author: garyericson
ms.service: batch-ai
ms.topic: overview
ms.date: 12/14/2018
ms.author: garye
ms.openlocfilehash: 6803a47ae77c072eff05df65e37156c90aabf3e6
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436867"
---
# <a name="whats-happening-to-azure-batch-ai"></a>O que está acontecendo com IA do Lote do Azure?

**O serviço de IA do Lote do Azure será desativado em março.** O treinamento em escala e as funcionalidades de pontuação do IA do Lote agora estão disponíveis no [serviço do Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md), que foi disponibilizado em 4 de dezembro de 2018.

Junto com muitas outras funcionalidades de aprendizado de máquina, o serviço do Azure Machine Learning inclui um destino de computação gerenciada baseado em nuvem para treinamento, implantação e modelos de aprendizado de máquina de pontuação. Esse destino de computação é chamado [Computação do Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute). [Comece a migrá-lo e usá-lo hoje](#migrate). Você pode interagir com o serviço do Azure Machine Learning por meio de seus [SDKs do Python](../machine-learning/service/quickstart-create-workspace-with-python.md), interface de linha de comando e [portal do Azure](../machine-learning/service/quickstart-get-started.md).

## <a name="support-timeline"></a>Linha do tempo de suporte

| Data | Detalhes de suporte do serviço de IA do Lote |
| ---- |-----------------|
| &nbsp;14&nbsp;de dezembro de 2018| Continue a usar as assinaturas existentes do IA do Lote do Azure como antes. No entanto, registrar **novas assinaturas** não é mais possível e nenhum novo investimento será feito a esse serviço.|
| &nbsp;31&nbsp;de março de 2019 | Após essa data, as assinaturas existentes de IA do Lote deixarão de funcionar. |

<a name="migrate"></a>
## <a name="how-do-i-migrate"></a>Como faço a migração?

Para evitar interrupções aos seus aplicativos e beneficie-se dos recursos mais recentes, execute as seguintes etapas antes de 31 de março de 2019:

1. Crie um workspace de serviço do Azure Machine Learning e comece:
    + [Início rápido baseado em Python](../machine-learning/service/quickstart-create-workspace-with-python.md)
    + [Início rápido baseado no portal do Azure](../machine-learning/service/quickstart-get-started.md)

1. Configurar uma [Computação do Azure Machine Learning](../machine-learning/service/how-to-set-up-training-targets.md#amlcompute) para treinamento de modelo.

1. Atualize seus scripts para usar a Computação do Azure Machine Learning.

## <a name="support"></a>Suporte

O suporte está disponível para os clientes existentes que desejam migrar para o [serviço do Azure Machine Learning](https://aka.ms/aml-docs) mais abrangente.

Se o serviço do Azure Machine Learning não atender às suas necessidades quando houver uma funcionalidade com suporte no serviço de IA do Lote, abra uma solicitação de suporte de IA do Lote com a equipe de suporte para incluir sua assinatura na lista de permissões para usar a IA do Lote até a desativação do serviço.

## <a name="next-steps"></a>Próximas etapas

+ Leia [Visão geral do serviço do Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md).

+ [Configurar um destino de computação para treinamento de modelo](../machine-learning/service/how-to-set-up-training-targets.md) com o serviço do Azure Machine Learning.

+ Examine o [Roteiro do Azure](https://azure.microsoft.com/updates/) para saber de outras atualizações de serviço do Azure.
