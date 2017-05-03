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
ms.date: 04/24/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 56e8f5579da2b5bed7975f25f0779c54d70cb886
ms.lasthandoff: 04/26/2017


---
# <a name="batch-service-quotas-and-limits"></a>Cotas e limites de serviço do Lote

Como com outros serviços do Azure, há limites em determinados recursos associados ao serviço de Lote. Muitos desses limites são cotas padrão aplicadas pelo Azure no nível da assinatura ou da conta. Este artigo discute esses padrões e como você pode solicitar aumentos de cotas.

Lembre-se dessas cotas quando estiver projetando e dimensionando suas cargas de trabalho do Lote. Por exemplo, se o pool não alcançar o número de destino de nós de computação que você especificou, você talvez atingiu o limite de cota de núcleos para sua conta de lote ou uma cota de núcleos de VMs regional para sua assinatura.

Você pode executar várias cargas de trabalho do Lote em uma única conta do Lote ou distribuir suas cargas de trabalho entre contas do Lote que estão na mesma assinatura mas em diferentes regiões do Azure.

Se você planeja executar cargas de trabalho de produção em Lote, talvez seja necessário aumentar uma ou mais cotas para acima do padrão. Se desejar aumentar a cota, você poderá abrir uma [solicitação de atendimento ao cliente](#increase-a-quota) online gratuitamente.

> [!NOTE]
> Uma cota é um limite de crédito, não uma garantia de capacidade. Se você precisar de capacidade em larga escala, entre em contato com o suporte do Azure.
> 
> 

## <a name="resource-quotas"></a>Cotas de recursos
[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="quotas-in-user-subscription-mode"></a>Cotas no modo de assinatura de usuário

Para uma conta de lote com modo de alocação do pool definido como **assinatura de usuário**, lote VMs e outros recursos, como contas de armazenamento, são criados diretamente na sua assinatura quando um pool é criado. A cota de núcleos de lote do Azure não se aplica a uma conta criada nesse modo. Em vez disso, as cotas em sua assinatura para regionais núcleos de computação e outros recursos são aplicados. Saiba mais sobre a [assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md).

Ao planejar o uso de recursos para uma conta criada no modo de usuário de assinatura, observe que os seguintes recursos de lote (além de núcleos de computação) são necessários para cada 40 VMs do Linux ou 20 VMs do Windows:

| Recurso | Cota | Provedor |
| --- | ---| --- |
| Uma conta de armazenamento | Contas de armazenamento | Microsoft.Storage |
| Um endereço IP público | Endereços IP públicos | Microsoft.Network | 
| Uma rede virtual | Redes Virtuais | Microsoft.Network | 
| Um grupo de segurança de rede | Grupos de segurança de rede | Microsoft.Network | 
| Conjunto de escala de máquina virtual | Conjuntos de Escala de Máquina Virtual | Microsoft.Compute | 
| Um balanceador de carga | Balanceadores de Carga | Microsoft.Network | 

A cota de núcleos em nível regional ou por família VM deve ser definida de acordo com o tamanho da VM necessário para o pool do lote ou pools:

| Cota | Provedor |
| --- | ---- |
| Total de núcleos regionais | Microsoft.Compute |
| … Núcleos de família | Microsoft.Compute |



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
2. Selecione **Propriedades** na folha de menu da conta de Lote.
3. A folha Propriedades exibe as **cotas** atualmente aplicadas à conta do Lote
   
    ![Cotas para conta do Lote][account_quotas]

Para uma conta de lote criada no modo de usuário de assinatura, exiba as cotas de assinatura relacionadas no Portal do Azure.

1. Selecione **Assinaturas**e selecione a assinatura que você está usando para a conta do lote.

2. Na folha **Assinatura**, selecione **Uso + cotas**.



## <a name="increase-a-quota"></a>Aumentar uma cota
Siga estas etapas para solicitar uma cota aumentam para sua conta de lote ou sua assinatura usando o [portal do Azure][portal]. O tipo de aumento de cota depende do modo de alocação de pool de sua conta do lote.

### <a name="increase-a-batch-cores-quota"></a>Aumentar a cota de núcleos de lote 

Se sua conta do lote foi criada em modo de **serviço de lote**, siga estas etapas para solicitar uma cota de núcleos de lote aumentam:

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

### <a name="increase-a-subscription-cores-quota"></a>Aumentar a cota de núcleos de assinatura

Se sua conta do Lote foi criada no modo **assinatura do usuário** e você precisa de núcleos regionais adicionais ou de família VM, solicite um aumento de cota em sua assinatura. Para as etapas, consulte [Solicitações de aumento da cota de núcleo do Gerenciador de recursos](../azure-supportability/resource-manager-core-quotas-request.md).



## <a name="related-topics"></a>Tópicos relacionados
* [Criar uma conta do Lote do Azure usando o portal do Azure](batch-account-create-portal.md)
* [Visão geral dos recursos do Lote do Azure](batch-api-basics.md)
* [Assinatura do Azure e limite de serviços, cotas e restrições](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: http://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.PNG

