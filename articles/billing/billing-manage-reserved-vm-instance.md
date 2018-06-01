---
title: Gerenciar Instâncias Reservadas do Microsoft Azure - Azure Billing | Microsoft Docs
description: Saiba como você pode alterar o escopo de assinatura e gerenciar o acesso para Instâncias de VM Reservadas do Azure.
services: billing
documentationcenter: ''
author: vikramdesai01
manager: vikramdesai01
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: vikdesai
ms.openlocfilehash: fc473906be9c572e6d6549c85f9faa8fe7566b86
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303055"
---
# <a name="manage-reserved-instances"></a>Gerenciar Instâncias Reservadas

Depois de comprar uma instância de VM reservada do Microsoft Azure, convém aplicar a Instância Reservada a uma assinatura diferente daquela especificada durante a compra. Como alternativa, as suas máquinas virtuais correspondentes estiverem em execução em várias assinaturas, convém alterar o escopo de Instância Reservada para compartilhado. Para maximizar o desconto da Instância Reservada, certifique-se de que o número de instâncias adquiridas coincide com os atributos e o número de máquinas virtuais que tem em execução. Para saber mais sobre as Instância Reservada do Microsoft Azure, consulte [Economizar dinheiro fazendo o pré-pagamento de máquinas virtuais do Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reserved-instance"></a>Alterar o escopo de uma Instância Reservada
 O desconto de Instância Reservada se aplica às máquinas virtuais que correspondem à sua Instância Reservada e executam no escopo da Instância Reservada. O escopo de uma Instância Reservada pode ser a única assinatura ou todas as assinaturas de seu contexto de cobrança. Se você definir o escopo como uma assinatura única, a correspondência da Instância Reservada será feita com máquinas virtuais em execução na assinatura selecionada. Se você definir o escopo como compartilhado, o Azure fará a correspondência da Instância Reservada com as máquinas virtuais sendo executadas em todas as assinaturas no contexto de cobrança. O contexto de cobrança depende da assinatura usada para comprar a Instância Reservada. Para obter mais informações, consulte [Pré-pagamento de VMs com Instâncias Reservadas](https://go.microsoft.com/fwlink/?linkid=861721).

Para atualizar o escopo de uma Instância Reservada: 
1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione a Instância Reservada.
4. Selecione **Configurações** > **Configuração**.
5. Altere o escopo. Se você alterar de compartilhado para escopo único, poderá selecionar apenas as assinaturas em que é o proprietário. Somente as assinaturas no mesmo contexto de cobrança que a Instância Reservada podem ser selecionadas. O contexto de cobrança é determinado pela assinatura que você selecionou quando da compra da Instância Reservada. O escopo aplica-se somente a assinaturas da oferta Paga Conforme o Uso MS-AZR-0003P e da oferta Enterprise MS-AZR-0017. No que concerne aos contratos Enterprise, as assinaturas de desenvolvimento/de teste não se qualificam para o desconto de Instância Reservada.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>Dividir uma única Instância Reservada em duas Instâncias Reservadas
 Depois de comprar mais de uma instância, pode ser que você queira atribuir instâncias, em uma Instância Reservada a assinaturas diferentes. Por padrão, todas as instâncias (quantidade especificada durante a aquisição) têm um escopo - uma assinatura única ou compartilhada. Por exemplo, você adquiriu 10 VMs Padrão D2 e especificou o escopo como assinatura A. Agora pode ser que você queira alterar o escopo das sete Instâncias Reservadas para assinatura A e as 3 restantes para assinatura B. Dividir uma Instância Reservada permite que você distribua instâncias para o gerenciamento de escopo granular. Você pode simplificar a alocação para as assinaturas escolhendo o escopo compartilhado. Contudo, para fins de gerenciamento ou de orçamento, você pode alocar quantidades a assinaturas específicas.

 Você pode dividir uma Instância Reservada em duas reservas por meio do PowerShell, CLI ou da API.

### <a name="split-a-reserved-instance-by-using-powershell"></a>Dividir uma Instância Reservada usando o PowerShell
1. Obtenha a ID do pedido de Instância Reservada executando o seguinte comando:

    ```powershell
    # Get the Reserved Instance orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Obtenha os detalhes de uma Instância Reservada:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Divida a Instância Reservada em duas e distribua as instâncias:

    ```powershell
    # Split the Reserved Instance. The sum of the Reserved Instances, the quantity, must equal the total number of instances in the Reserved Instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Você pode atualizar o escopo executando o seguinte comando:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>Adicionar ou alterar os usuários que podem gerenciar uma Instância Reservada
Você pode delegar o gerenciamento de uma Instância Reservada adicionando pessoas às funções na Instância Reservada. Por padrão, a pessoa que comprou a Instância Reservada e o administrador da conta têm a função de Proprietário de Instância Reservada. 

Você pode gerenciar o acesso às Instâncias Reservadas, independentemente das assinaturas que obtêm o desconto de Instância Reservada. Ao conceder a alguém permissões para gerenciar uma Instância Reservada, isso não lhes dá direitos de gerenciar a assinatura. E se você conceder a alguém permissões para gerenciar uma assinatura no escopo da Instância Reservada, isso não lhe concederá direitos de gerenciamento da Instância Reservada.
 
Para delegar o gerenciamento de acesso de uma Instância Reservada: 
1.  Faça logon no [Portal do Azure](https://portal.azure.com).
2.  Selecione **Todos os serviços** > **Reserva** para listar as Instâncias Reservadas às quais você tem acesso.
3.  Selecione a Instância Reservada que deseja delegar acesso a outros usuários.
4.  Selecione **Controle de acesso (IAM)** no menu.
5.  Selecione **Adicionar** > **Função** > **Proprietário** (ou uma função diferente se você quiser dar acesso limitado). 
6. Digite o endereço de email do usuário que você deseja adicionar como proprietário. 
7. Selecione o usuário e, em seguida, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as Instâncias Reservadas do Azure, confira os seguintes artigos:

- [Economizar dinheiro de máquinas virtuais com Instâncias Reservadas do Microsoft Azure](billing-save-compute-costs-reservations.md)
- [Pagar antecipadamente por Máquinas Virtuais com Instâncias Reservadas](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Entender como é aplicado o desconto de Instância Reservada](billing-understand-vm-reservation-charges.md)
- [Entender o uso da Instância Reservada na sua assinatura Paga pelo Uso](billing-understand-reserved-instance-usage.md)
- [Entender o uso da Instância Reservada no seu registro de Empresa](billing-understand-reserved-instance-usage-ea.md)
- [Os custos de software do Windows não estão incluídos nas Instâncias Reservadas](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
