---
title: "Cotas do serviço e limites do Lote do Azure | Microsoft Docs"
description: "Saiba mais sobre as restrições, limites e cotas padrão do Lote do Azure e como aumentar a cota da solicitação"
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 6b6c548ca1001587e2b40bbe9ee2fcb298f40d72
ms.openlocfilehash: a0f47a19f7ef1832e64e9a0bdc4bda3434f77aa2
ms.lasthandoff: 02/28/2017


---
# <a name="batch-service-quotas-and-limits"></a>Cotas e limites de serviço do Lote

Como com outros serviços do Azure, há limites em determinados recursos associados ao serviço de Lote. Muitos desses limites são cotas padrão aplicadas pelo Azure no nível da assinatura ou da conta. Este artigo discute esses padrões e como você pode solicitar aumentos de cotas.

Se você planeja executar cargas de trabalho de produção em Lote, talvez seja necessário aumentar uma ou mais cotas para acima do padrão. Se desejar aumentar a cota, você poderá abrir uma [solicitação de atendimento ao cliente](#increase-a-quota) online gratuitamente.

> [!NOTE]
> Uma cota é um limite de crédito, não uma garantia de capacidade. Se você precisar de capacidade em larga escala, entre em contato com o suporte do Azure.
> 
> 

## <a name="resource-quotas"></a>Cotas de recursos
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="other-limits"></a>Outros limites
| **Recurso** | **Limite máximo** |
| --- | --- |
| [Tarefas simultâneas](batch-parallel-node-tasks.md) por nó de computação |4 vezes o número de núcleos de nó |
| [Aplicativos](batch-application-packages.md) por conta do Lote |20 |
| Pacotes de aplicativos por aplicativo |40 |
| Tamanho do pacote de aplicativos (cada) |Aproximadamente 195 GB<sup>1</sup> |

<sup>1</sup> O limite do Armazenamento do Azure para o tamanho máximo do blob de blocos

## <a name="view-batch-quotas"></a>Exibir cotas do Lote
Exibir suas cotas de conta do Lote no [portal do Azure][portal].

1. Selecione **Contas do Lote** no portal e selecione a conta do Lote na qual você está interessado.
2. Selecione **Propriedades** na folha de menu da conta do Lote
3. A folha Propriedades exibe as **cotas** atualmente aplicadas à conta do Lote
   
    ![Cotas para conta do Lote][account_quotas]

## <a name="increase-a-quota"></a>Aumentar uma cota
Siga as etapas abaixo para solicitar um aumento de cota usando o [portal do Azure][portal].

1. Selecione o bloco **Ajuda + suporte** no painel do portal ou o ponto de interrogação (**?**) no canto superior direito do portal.
2. Selecione **Nova solicitação de suporte** > **Fundamentos**.
3. Na folha **Fundamentos** :
   
    a. **Tipo de Problema** > **Cota**
   
    b. Selecione sua assinatura.
   
    c. **Tipo de cota** > **Lote**
   
    d. **Plano de suporte** > **Suporte da cota - Incluído**
   
    Clique em **Próximo**.
4. Na folha **Problema** :
   
    a. Selecione uma **Gravidade** de acordo com o [impacto nos negócios][support_sev].
   
    b. Em **Detalhes**, especifique cada cota que você deseja alterar, o nome da conta do Lote e o novo limite.
   
    Clique em **Próximo**.
5. Na folha **Informações de contato** :
   
    a. Selecione um **método de contato preferencial**.
   
    b. Verifique e insira os detalhes de contato necessários.
   
    Clique em **Criar** para enviar a solicitação de suporte.

Depois que a solicitação de suporte foi enviada, o suporte do Azure entrará em contato com você. Observe que a conclusão do pedido pode levar até dois dias úteis.

## <a name="related-topics"></a>Tópicos relacionados
* [Criar uma conta do Lote do Azure usando o portal do Azure](batch-account-create-portal.md)
* [Visão geral dos recursos do Lote do Azure](batch-api-basics.md)
* [Assinatura do Azure e limite de serviços, cotas e restrições](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

