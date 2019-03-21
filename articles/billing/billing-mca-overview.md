---
title: Introdução à sua conta de cobrança para um contrato de cliente da Microsoft - Azure | Microsoft Docs
description: Entender a conta de cobrança para um contrato de cliente da Microsoft
services: billing
documentationcenter: ''
author: amberbhargava
manager: amberbhargava
editor: banders
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: ea625a61ed600dbaa22fef85987e9570a6fb7dbc
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337406"
---
# <a name="get-started-with-your-billing-account-for-a-microsoft-customer-agreement"></a>Introdução à sua conta de cobrança para um contrato de cliente da Microsoft

Uma conta de cobrança é criada para cada contrato que você entrar com a Microsoft para usar o Azure. Você pode usar sua conta de cobrança para gerenciar a cobrança e controlar os custos. Você pode ter acesso a várias contas de cobrança. Por exemplo, você pode ter entrado do Azure para seus projetos pessoais. Você também pode ter acesso ao Azure por meio do Enterprise Agreement ou contrato de cliente da Microsoft da sua organização. Para cada um desses cenários, você teria uma conta de cobrança separada.

Este artigo se aplicam a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se você tem acesso a um contrato de cliente do Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="understand-billing-account"></a>Entender a conta de cobrança

Sua conta de cobrança para o contrato de cliente da Microsoft contém um ou mais perfis de cobrança que permitem que você gerencie seus métodos de pagamento e notas fiscais. Cada perfil de cobrança contém um ou mais seções de nota fiscal que permitem que você organize os custos de fatura de cobrança do perfil.

O diagrama a seguir mostra a relação entre uma conta de cobrança, a cobrança de perfis e seções de nota fiscal.

![Diagrama que mostra a hierarquia de cobrança para o contrato de cliente da Microsoft](./media/billing-mca-overview/mca-billing-hierarchy.png)

Funções na conta de cobrança têm o nível mais alto de permissões. Por padrão, somente os administradores globais no Azure Active Directory da sua organização obtém acesso à conta de cobrança. Essas funções devem ser atribuídas a usuários que precisam exibir faturas e controlar os custos para toda a organização, como finanças ou gerentes de TI. Para obter mais informações, consulte [tarefas e funções da conta de cobrança](billing-understand-mca-roles.md#billing-account-roles-and-tasks).

## <a name="understand-billing-profiles"></a>Entender os perfis de cobrança

Use um perfil de cobrança para gerenciar seus métodos de pagamento da nota fiscal. Uma fatura mensal é gerada para as assinaturas do Azure e outros produtos adquiridos usando o perfil de cobrança. Você pode usar os métodos de pagamento para pagar a nota fiscal.

Um perfil de cobrança é automaticamente criado para sua conta de cobrança. Você pode criar novos perfis de cobrança para configurar as faturas adicionais. Por exemplo, convém faturas diferentes para cada departamento ou um projeto em sua organização.

Você também pode criar seções de nota fiscal para organizar os custos de fatura de cobrança um perfil. Encargos para assinaturas do Azure e os produtos adquiridos para uma seção de nota fiscal aparecem na seção. Fatura de cobrança do perfil inclui encargos para todas as seções de nota fiscal.

Funções nos perfis de cobrança têm permissões para exibir e gerenciar as notas fiscais e métodos de pagamento. Atribua essas funções para usuários que pagar notas fiscais, como membros da equipe de contabilidade em sua organização. Para obter mais informações, consulte [perfil de funções e tarefas de cobrança](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="monthly-invoice-generated-for-each-billing-profile"></a>Fatura mensal gerada para cada perfil de cobrança

Uma fatura mensal é gerada na data da fatura para cada perfil de cobrança. A nota fiscal contém todos os encargos para o mês anterior.

Você pode exibir a fatura, baixar documentos e alterar a configuração para obter o futuras faturas por email, no portal do Azure. Para obter mais informações, consulte [download de faturas para um contrato de cliente do Microsoft](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

### <a name="invoices-paid-through-payment-methods"></a>Faturas pagas por meio de métodos de pagamento

Cada perfil de cobrança tem seus próprios métodos de pagamento que são usados para pagar suas faturas. Há suporte para os seguintes métodos de pagamento:

| Type             | Definição  |
|------------------|-------------|
|Créditos do Azure    |  Os créditos são aplicados automaticamente para a quantidade total cobrada em sua fatura para calcular o valor que você precisa pagar. Para obter mais informações, consulte [acompanhar o saldo de crédito do Azure para o perfil de cobrança](billing-mca-check-azure-credits-balance.md). |
|Cheque ou transferência eletrônica | Você pode pagar o valor devido para obter a fatura por meio da seleção ou um cabo de transferência. As instruções de pagamento são fornecidas na nota fiscal |

### <a name="control-azure-marketplace-and-reservation-purchases-by-applying-policies"></a>Compras de reserva e controle do Azure Marketplace por meio da aplicação de políticas

Aplica políticas para controlar as compras feitas usando um perfil de cobrança. Você pode definir políticas para desabilitar a compra de reservas do Azure e produtos do Marketplace. Quando as políticas são aplicadas, as assinaturas criadas para as seções de nota fiscal no perfil de cobrança não podem ser usadas para comprar reservas do Azure e produtos do Marketplace.

### <a name="allow-users-to-create-azure-subscriptions-by-enabling-azure-plans"></a>Permitir que usuários criem assinaturas do Azure, permitindo que os planos do Azure

Os planos do Azure são habilitados automaticamente quando você cria um perfil de cobrança. Todas as seções de nota fiscal no perfil de cobrança obtém acesso a esses planos. Os usuários com acesso à seção de nota fiscal usar os planos de criar assinaturas do Azure. Eles não é possível criar assinaturas do Azure, a menos que um plano do Azure está habilitado para o perfil de cobrança. Os planos do Azure a seguir têm suporte em contas de cobrança para o contrato de cliente da Microsoft:

| Plano             | Definição  |
|------------------|-------------|
|Plano do Microsoft Azure   | Permitir que os usuários criem assinaturas que podem executar qualquer carga de trabalho. Para obter mais informações, consulte [plano do Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) |
|Plano do Microsoft Azure para Desenvolvimento/Teste | Permitir que os assinantes do Visual Studio criar assinaturas que são restritas para o desenvolvimento ou teste de cargas de trabalho. Essas assinaturas obtêm os benefícios como acesso a imagens de máquina de virtual exclusivo e taxas menores no portal do Azure. Para obter mais informações, consulte [plano do Microsoft Azure para desenvolvimento/teste](https://azure.microsoft.com/offers/ms-azr-0148g/)|

## <a name="understand-invoice-sections"></a>Entender as seções de nota fiscal

Crie seções de nota fiscal para organizar os custos em fatura de cobrança um perfil. Por exemplo, talvez seja necessário uma única fatura para a sua organização, mas deseja organizar os custos por departamento, equipe ou projeto. Para este cenário, você tem um único perfil de cobrança em que você criar uma seção de nota fiscal para cada departamento, equipe ou projeto.

Quando uma seção de nota fiscal é criada, você pode dar a outros usuários permissão para criar assinaturas do Azure para a seção. Todos os encargos de uso e compras para as assinaturas, em seguida, são refletidas na seção apropriada da nota fiscal.

Funções na seção de nota fiscal têm permissões para controlar quem cria assinaturas do Azure. Atribua essas funções para usuários que configurar o ambiente do Azure para as equipes em nossa organização, como líderes de engenharia e arquitetos técnicos. Para obter mais informações, consulte [seção funções e tarefas de nota fiscal](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para saber mais sobre sua conta de cobrança:

- [Entender as funções administrativas do contrato de cliente da Microsoft no Azure](billing-understand-mca-roles.md)
- [Criar uma assinatura do Azure adicional para o contrato de cliente da Microsoft](billing-mca-create-subscription.md)
- [Criar seções na sua fatura para organizar seus custos](billing-mca-section-invoice.md)