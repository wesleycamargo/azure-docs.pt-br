---
title: Obter propriedade de assinaturas do Azure de cobrança | Microsoft Docs
description: Saiba como solicitar a propriedade da cobrança de assinaturas do Azure de outros usuários.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2019
ms.author: banders
ms.openlocfilehash: be8c7fcebca224196d9eac7d22387989b1bdfd46
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57890926"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-users"></a>Obter propriedade de assinaturas do Azure de outros usuários de cobrança

Talvez você queira assumir a propriedade de assinaturas do Azure se o proprietário de cobrança existente é deixar sua organização ou deseja pagar para as assinaturas por meio de sua conta de cobrança.

Você pode enviar uma solicitação para assumir a propriedade de assinaturas do Azure de proprietários existentes em outras contas de cobrança. Apropriação transfere as responsabilidades de cobrança de assinaturas para a seção de nota fiscal.

Para solicitar a propriedade de cobrança, você deve ser um **proprietário da seção de nota fiscal** ou **colaborador da seção de nota fiscal**. Para obter mais informações, consulte [funções de tarefas da seção de nota fiscal](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

Este artigo se aplicam a uma conta de cobrança para um contrato de cliente da Microsoft. [Verifique se você tem acesso a um contrato de cliente do Microsoft](#check-access-to-a-microsoft-customer-agreement).

## <a name="request-billing-ownership-in-the-azure-portal"></a>Solicitar a propriedade de cobrança no portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Vá para a seção de nota fiscal. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança ou o perfil de cobrança. Na conta ou perfil de cobrança, selecione **seções de nota fiscal** e, em seguida, uma seção de nota fiscal.
   <!-- Todo - Add a screenshot -->

4. Selecione **solicitações de transferência** do lado inferior esquerdo.

5. Na parte superior da página, selecione **adicionar**.

6. Insira o endereço de email do usuário que está solicitando a propriedade da cobrança do. O usuário deve ser um administrador da conta em uma conta de cobrança do programa de serviço Online da Microsoft ou um proprietário de conta em um Enterprise Agreement. Para obter mais informações, consulte [exibir suas contas de cobrança no portal do Azure](billing-view-all-accounts.md).

   ![Captura de tela que mostra a adição de uma nova solicitação de transferência](./media/billing-mca-request-billing-ownership/mca-new-transfer-request.png)

7. Selecione **enviar solicitação de transferência**.

8. O usuário recebe um email com instruções para revisar a solicitação de transferência.

   ![Captura de tela desse email de solicitação de transferência de revisão mostra](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

9. Para aprovar a solicitação de transferência, o usuário seleciona o link no email e segue as instruções.

    ![Captura de tela desse email de solicitação de transferência de revisão mostra](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-status-of-your-transfer-request-in-the-azure-portal"></a>Verificar o status da sua solicitação de transferência no portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure para gerenciamento de custos + cobrança](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Vá para a seção de nota fiscal. Dependendo do seu acesso, você precisa selecionar uma conta de cobrança ou o perfil de cobrança. Na conta ou perfil de cobrança, selecione **seções de nota fiscal** e, em seguida, uma seção de nota fiscal.
   <!-- Todo - Add a screenshot -->

4. Selecione **solicitações de transferência** do lado inferior esquerdo.

5. A página de solicitações de transferência exibe as seguintes informações:

    ![Captura de tela que mostra a lista de solicitações de transferência](./media/billing-mca-request-billing-ownership/mca-view-transfer-requests.png)

   |Coluna|Definição|
   |---------|---------|
   |Data da solicitação|A data em que a solicitação de transferência foi enviada|
   |Destinatário|O endereço de email do usuário que você enviou a solicitação para transferir a propriedade da cobrança|
   |Data de validade|A data de vencimento da solicitação|
   |Status|O status da solicitação de transferência|

    A solicitação de transferência pode ter um dos seguintes status:

   |Status|Definição|
   |---------|---------|
   |Em andamento|O usuário não tiver aceitado a solicitação de transferência|
   |Processando|O usuário aprovar a solicitação de transferência. A cobrança para assinaturas que o usuário selecionou está sendo transferida para a seção de nota fiscal|
   |Concluído| A cobrança para assinaturas que o usuário selecionado é transferida para a seção de nota fiscal|
   |Concluído com erros|A solicitação foi concluída, mas a cobrança por algumas assinaturas que o usuário selecionado não pôde ser transferido|
   |Expirado|O usuário não aceitou a solicitação no tempo e expirado|
   |Cancelado|Alguém com acesso à solicitação de transferência cancelada a solicitação|
   |Recusado|O usuário recusou a solicitação de transferência|

6. Selecione uma solicitação de transferência para exibir detalhes. A página de detalhes da transferência exibe as seguintes informações:
   <!-- Todo - Add a screenshot -->

   |Coluna  |Definição|
   |---------|---------|
   |ID da solicitação de transferência|A ID exclusiva para a sua solicitação de transferência. Se você enviar uma solicitação de suporte, compartilhe a ID com suporte do Azure para acelerar a sua solicitação de suporte|
   |Transferência solicitada em|A data em que a solicitação de transferência foi enviada|
   |Transferência solicitada por|O endereço de email do usuário que enviou a solicitação de transferência|
   |Solicitação de transferência expira em| A data de expiração a solicitação de transferência|
   |Endereço de email do destinatário|O endereço de email do usuário que você enviou a solicitação para transferir a propriedade da cobrança|
   |Link de transferência enviada ao destinatário|A url que foi enviado para o usuário para revisar a solicitação de transferência|

## <a name="additional-information"></a>Informações adicionais

A seção a seguir fornece informações adicionais sobre como transferir as assinaturas.

### <a name="no-service-downtime"></a>Não há tempo de inatividade do serviço

Os serviços do Azure na assinatura a ser executados sem nenhuma interrupção. Vamos apenas fazer a transição a relação de cobrança para as assinaturas do Azure que o usuário seleciona para transferir.

### <a name="disabled-subscriptions"></a>Assinaturas desabilitadas

Não possível transferir assinaturas desabilitadas. Assinaturas devem estar no estado ativo para transferir a sua propriedade de cobrança.

### <a name="azure-resources-transfer"></a>Transferência de recursos do Azure

Todos os recursos das assinaturas, como VMs, discos e sites são transferidos.

### <a name="azure-marketplace-products-transfer"></a>Transferência de produtos do Azure Marketplace

Transferência de produtos do Marketplace do Azure, juntamente com suas respectivas assinaturas.

### <a name="azure-reservations-transfer"></a>Transferência de reservas do Azure

Reservas do Azure não movem automaticamente com as assinaturas. [Entre em contato com o suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para mover as reservas.

### <a name="access-to-azure-services"></a>Acesso aos serviços do Azure

Acesso aos recursos do Azure que foi definido usando o Azure RBAC (controle de acesso baseado em função) não é afetado durante a transição.

### <a name="azure-support-plan"></a>Plano de suporte do Azure

Suporte do Azure não transfere com as assinaturas. Se o usuário transferir todas as assinaturas do Azure, peça-lhe para cancelar seu plano de suporte.

### <a name="charges-for-transferred-subscription"></a>Encargos de assinatura transferido

O proprietário de cobrança original das assinaturas é responsável por quaisquer encargos relatados até o ponto que a transferência for concluída. A seção de nota fiscal é responsável por encargos relatados a partir do momento da transferência em diante. Pode haver alguns encargos que ocorreram antes da transferência, mas foi relatados posteriormente. Esses encargos aparecem na seção de nota fiscal.

### <a name="supported-offers"></a>Ofertas com suporte

Assinatura de todos os tipos ou ofertas, exceto o CSP oferece pode ser transferida.

### <a name="cancel-a-transfer-request"></a>Cancelar uma solicitação de transferência

Você pode cancelar a solicitação de transferência até que a solicitação é aprovada ou recusada. Para cancelar a solicitação de transferência, vá para a página de detalhes de transferência e selecionar Cancelar da parte inferior da página.

### <a name="software-as-a-service-saas-transfer"></a>Software como uma transferência de serviço (SaaS)

Produtos de SaaS não são transferidas com as assinaturas. Solicitar que o usuário [suporte do Azure de contato](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para transferir a propriedade de cobrança de produtos de SaaS. Junto com a propriedade de cobrança, o usuário também pode transferir a propriedade de recurso. Propriedade de recursos permite que você execute operações de gerenciamento, como a exclusão e exibindo os detalhes do produto. Usuário deve ser o proprietário do recurso sobre o produto de SaaS para transferir a propriedade de recurso.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Verificar o acesso a um contrato de cliente da Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

- A propriedade de cobrança das assinaturas do Azure é transferida para a seção de nota fiscal. Manter o controle dos encargos para essas assinaturas na [portal do Azure](https://portal.azure.com).
- Conceda permissões para exibir e gerenciar a cobrança por essas assinaturas a outros. Para obter mais informações, consulte [seção funções e tarefas de nota fiscal](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
