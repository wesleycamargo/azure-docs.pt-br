---
title: Cotas do serviço e limites da IA do Lote do Azure | Microsoft Docs
description: Saiba mais sobre as restrições, limites e cotas padrão da IA do Lote do Azure e como aumentar a cota da solicitação
services: batch-ai
documentationcenter: ''
author: johnwu10
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 1f9bd8503c2e8f2aa3c54d41b8585c6fbf81c92b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542075"
---
# <a name="batch-ai-service-quotas-and-limits"></a>Cotas e limites da IA do Lote

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

Como com outros serviços do Azure, há limites em determinados recursos associados ao serviço da IA do Lote. Na IA do Lote, esses limites são cotas padrão aplicadas no nível da assinatura para cada região em que o serviço está [disponível](https://azure.microsoft.com/global-infrastructure/services/). Este artigo discute esses padrões e como você pode solicitar aumentos de cotas.

Lembre-se dessas cotas quando estiver projetando e expandindo seus recursos da IA do Lote. Por exemplo, se o cluster não atingir o número de destino de nós especificado, você poderá ter atingido um limite de núcleos da IA do Lote para sua assinatura.

Se você planeja executar cargas de trabalho de produção na IA do Lote, talvez seja necessário aumentar uma ou mais cotas para acima do padrão.

> [!NOTE]
> Uma cota é um limite de crédito, não uma garantia de capacidade. Se você precisar de capacidade em larga escala, entre em contato com o suporte do Azure.
> 
> 

## <a name="resource-quotas"></a>Cotas de recursos

[!INCLUDE [azure-batch-ai-limits](../../includes/azure-batch-ai-limits.md)]

## <a name="other-limits"></a>Outros limites

Os limites a seguir são rígidos, que não podem ser excedidos uma vez atingidos.

| **Recurso** | **Limite máximo** |
| --- | --- |
| Máximo de workspaces por grupo de recursos | 800 |
| Tamanho máximo do cluster | 100 nós |
| Máximo de processos de MPI de GPU nó | 1-4 |
| Máximo de trabalhos de GPU nó | 1-4 |
| Tempo de vida máximo do trabalho | 7 dias<sup>1</sup> |
| Máximo de servidores de parâmetro por nó | 1 |

<sup>1</sup> O tempo de vida máximo se refere à hora que um trabalho começa a ser executado e quando ele é concluído. Trabalhos concluídos persistem indefinidamente; os dados de trabalhos não concluídos dentro do tempo de vida máximo não ficam acessíveis.

## <a name="view-batch-ai-quotas"></a>Exibir cotas da IA do Lote

Exibir suas cotas de assinatura de IA do Lote atuais no [portal do Azure][portal].

1. No painel esquerdo, clique em **Todos os serviços**. Em seguida, pesquise por **IA do Lote** e clique para abrir o serviço.
2. Clique em **Uso + cotas** no menu da IA do Lote.
3. Selecione sua assinatura para exibir os limites de cota.

## <a name="increase-a-batch-ai-cores-quota"></a>Aumentar a cota de núcleos da IA do Lote

Siga estas etapas para solicitar uma um aumento de cota para sua assinatura da IA do Lote usando o [portal do Azure][portal]. 

1. No painel esquerdo, clique em **Todos os serviços**. Em seguida, pesquise por **IA do Lote** e clique para abrir o serviço.
2. Clique em **Nova solicitação de suporte** no menu da IA do Lote.
3. Em **Noções básicas**:
   
     a. **Tipo de Problema** > **Cota**
   
    b. **Assinatura** > selecione sua assinatura.
   
    c. **Tipo de cota** > **IA do Lote**
   
    d. **Plano de suporte** > Selecione seu plano de suporte.

    Clique em **Avançar**.
4. Em **Problema**:
   
     a. Selecione uma **Gravidade** de acordo com o [impacto nos negócios][support_sev].
   
    b. Em **Detalhes da cota**, especifique o local, o tipo de cota e o tipo de recurso. Especifique o novo limite que deseja solicitar. Clique em **Salvar e continuar**.

    c. Opcional – faça upload dos arquivos relevantes com mais informações sobre o motivo do aumento.
   
    Clique em **Avançar**.
5. Em **Informações de contato**:
   
     a. Selecione um **método de contato preferencial**.
   
    b. Verifique e insira os detalhes de contato necessários.
   
    Clique em **Criar** para enviar a solicitação de suporte.

Depois que a solicitação de suporte foi enviada, o suporte do Azure entrará em contato com você. A conclusão da solicitação pode levar até dois dias úteis.


## <a name="next-steps"></a>Próximas etapas

Depois de se familiarizar com os limites de cota, confira os seguintes artigos de introdução ao uso da IA do Lote.

> [!div class="nextstepaction"]
> [Tutorial de início rápido da IA do Lote](quickstart-tensorflow-training-cli.md)
> [Receitas da IA do Lote](https://github.com/Azure/BatchAI/tree/master/recipes)
> [Saiba mais sobre os recursos da IA do Lote](resource-concepts.md)

[portal]: https://portal.azure.com
[support_sev]: https://aka.ms/supportseverity