---
title: Solucionar problemas usando o Azure Blueprint
description: Saiba como solucionar problemas de criação e atribuição de blueprints
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dd1163ece225c2e9a9b082f5e8364f34b06a10ae
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46982283"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Solucionar problemas usando o Azure Blueprint

Você pode encontrar erros ao criar ou atribuir blueprints. Este artigo descreve os vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Encontrando os detalhes do erro

Muitos erros serão o resultado da atribuição de um blueprint para um escopo. Quando uma atribuição falha, o blueprint fornece detalhes sobre a implantação com falha. Essas informações indicarão o problema para que ele pode ser corrigido e a implantação subsequente tenha êxito.

1. Inicie o serviço Azure Blueprints no portal do Azure clicando no **todos os serviços** e procurando e selecionando **Política** no painel esquerdo. Sobre a **política** página, clique em **plantas**.

1. Selecione **Blueprints Atribuídos** na página à esquerda e use a caixa de pesquisa para filtrar as atribuições de blueprint para encontrar as atribuições com falha. Você também pode classificar a tabela de atribuições de acordo com a coluna **Estado de Provisionamento** para ver todas as atribuições com falha agrupadas.

1. Clique com o botão esquerdo do mouse no blueprint com o status _Com falha_ ou clique com o botão direito do mouse e selecione **Exibir Detalhes da Atribuição**.

1. Na parte superior da página de atribuição do blueprint há uma faixa vermelha avisando que a atribuição falhou. Clique em qualquer lugar na faixa para obter mais detalhes.

É comum o erro ser causado por um artefato incluído no blueprint e não pelo blueprint como um todo. Por exemplo, se o blueprint contiver um artefato para criar um Key Vault, mas a criação do Key Vault for impedida pelo Azure Policy, toda a atribuição falhará.

## <a name="general-errors"></a>Erros gerais

### <a name="policy-violation"></a>Cenário: violação de política

#### <a name="issue"></a>Problema

A implantação de modelo falhou por causa da violação de política.

#### <a name="cause"></a>Causa

Uma política pode entrar em conflito com a implantação por vários motivos:

- O recurso que está sendo criado é restrito pela política (normalmente restrições SKU ou local)
- A implantação está definindo campos que são configurados pela política (comum com marcas)

#### <a name="resolution"></a>Resolução

Ajuste o blueprint para não estar em conflito com as políticas listadas nas informações de erro. Se isso não for possível, uma opção alternativa será alterar o escopo da atribuição de política para que o blueprint não esteja mais em conflito com a política.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/)
- Conecte-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente conectando-se à comunidade do Azure para os recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.