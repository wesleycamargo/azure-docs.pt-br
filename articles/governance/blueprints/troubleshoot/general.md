---
title: Solucionar problemas usando o Azure Blueprint
description: Saiba como solucionar problemas de criação e atribuição de blueprints
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b910f90e70af4ce6d4243c06bfe5bd03d25d74d6
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092927"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Solucionar problemas usando o Azure Blueprint

Você pode executar erros ao criar ou atribuir blueprints. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Encontrando os detalhes do erro

Muitos erros serão o resultado da atribuição de um blueprint para um escopo. Quando uma atribuição falha, o blueprint fornece detalhes sobre a implantação com falha. Essa informação indica o problema para que ele possa ser corrigido e a próxima implantação for bem-sucedida.

1. Clique em **Todos os serviços**; procure e selecione **Política** no painel esquerdo. Sobre a **política** página, clique em **plantas**.

1. Selecione **Blueprints Atribuídos** na página à esquerda e use a caixa de pesquisa para filtrar as atribuições de blueprint para encontrar as atribuições com falha. Você também pode classificar a tabela de atribuições de acordo com a coluna **Estado de Provisionamento** para ver todas as atribuições com falha agrupadas.

1. Clique com o botão esquerdo do mouse no blueprint com o status _Com falha_ ou clique com o botão direito do mouse e selecione **Exibir Detalhes da Atribuição**.

1. Uma faixa vermelha que falhou a atribuição de aviso está na parte superior da página de atribuição de plano gráfico. Clique em qualquer lugar na faixa para obter mais detalhes.

É comum o erro a ser causado por um artefato e não o plano gráfico como um todo. Se um artefato cria um cofre de chaves e a Azure Policy impede a criação de Cofre de chaves, a atribuição inteira falhará.

## <a name="general-errors"></a>Erros gerais

### <a name="policy-violation"></a>Cenário: violação de política

#### <a name="issue"></a>Problema

A implantação de modelo falhou por causa da violação de política.

#### <a name="cause"></a>Causa

Uma política pode entrar em conflito com a implantação por vários motivos:

- O recurso que está sendo criado é restrito pela política (normalmente restrições SKU ou local)
- A implantação está definindo campos que são configurados pela política (comum com marcas)

#### <a name="resolution"></a>Resolução

Altere o plano gráfico para que ele não entre em conflito com as políticas nos detalhes do erro. Se isso não for possível, uma opção alternativa será alterar o escopo da atribuição de política para que o blueprint não esteja mais em conflito com a política.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
- Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.