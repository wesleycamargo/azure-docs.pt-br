---
title: "Gerenciar Instâncias de Máquinas Virtuais Reservadas do Azure | Microsoft Docs"
description: "Saiba como você pode alterar o escopo de assinatura e gerenciar o acesso para Instâncias de VM Reservadas do Azure."
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2017
ms.author: vikdesai
ms.openlocfilehash: f3f5f974630c4bf1c68599e26612ed729b55bcfc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-reserved-virtual-machine-instances"></a>Gerenciar instâncias de máquina virtual reservadas

Depois de comprar uma instância de VM reservada do Azure, convém aplicar a reserva a uma assinatura diferente daquela especificada durante a compra. Como alternativa, as suas máquinas virtuais correspondentes estiverem em execução em várias assinaturas, convém alterar o escopo de reserva para compartilhado. Para maximizar o desconto da reserva, certifique-se de que o número de instâncias adquiridas coincide com os atributos e o número de máquinas virtuais que tem em execução. Para saber mais sobre as instâncias de máquina virtual reservadas, consulte [Economizar dinheiro fazendo o pré-pagamento de máquinas virtuais do Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>Alterar o escopo de uma reserva
 O desconto de reserva se aplica às máquinas virtuais que correspondem à reserva e executam no escopo da reserva. O escopo de uma reserva pode ser a única assinatura ou todas as assinaturas de seu contexto de cobrança. Se você definir o escopo como uma assinatura única, a correspondência da reserva será feita com máquinas virtuais em execução na assinatura selecionada. Se você definir o escopo como compartilhado, o Azure fará a correspondência da reserva com as máquinas virtuais sendo executadas em todas as assinaturas no contexto de cobrança. O contexto de cobrança depende da assinatura usada para comprar a reserva. Para obter mais informações, consulte [Pré-pagamento de VMs com instâncias de VM reservadas](https://go.microsoft.com/fwlink/?linkid=861721).

Para atualizar o escopo de uma reserva: 
1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione a reserva.
4. Selecione **Configurações** > **Configuração**.
5. Altere o escopo. Se você alterar de compartilhado para escopo único, poderá selecionar apenas as assinaturas em que é o proprietário. Somente as assinaturas no mesmo contexto de cobrança que a reserva podem ser selecionadas. O contexto de cobrança é determinado pela assinatura que você selecionou quando da compra da reserva. O escopo aplica-se somente a assinaturas da oferta Paga Conforme o Uso MS-AZR-0003P e da oferta Enterprise MS-AZR-0017. No que concerne aos contratos Enterprise, as assinaturas de desenvolvimento/de teste não se qualificam para o desconto de reserva.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividir uma única reserva em duas reservas
 Depois de comprar mais de uma instância, pode ser que você queira atribuir instâncias, em uma reserva, a assinaturas diferentes. Por padrão, todas as instâncias (quantidade especificada durante a aquisição) têm um escopo - uma assinatura única ou compartilhada. Por exemplo, você adquiriu 10 VMs Padrão D2 e especificou o escopo como assinatura A. Agora pode ser que você queira alterar o escopo das 7 instâncias de VM reservada para assinatura A e as três restantes para assinatura B. Dividir uma reserva permite que você distribua instâncias para o gerenciamento de escopo granular. Você pode simplificar a alocação para as assinaturas escolhendo o escopo compartilhado. Contudo, para fins de gerenciamento ou de orçamento, você pode alocar quantidades a assinaturas específicas.

 Você pode dividir uma reserva em duas reservas por meio do PowerShell, CLI ou da API.

### <a name="split-a-reservation-by-using-powershell"></a>Dividir uma reserva usando o PowerShell
1. Obtenha a ID do pedido de reserva executando o seguinte comando:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Obtenha os detalhes de uma reserva:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Divida a reserva em duas e distribua as instâncias:

    ```powershell
    # Split the reservation. The sum of the Reserved VM instances, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Você pode atualizar o escopo executando o seguinte comando:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Adicionar ou alterar os usuários que podem gerenciar uma reserva
Você pode delegar o gerenciamento de uma reserva adicionando pessoas às funções na reserva. Por padrão, a pessoa que comprou a reserva e o administrador da conta têm a função de Proprietário de reserva. 

Você pode gerenciar o acesso às reservas independentemente das assinaturas que obtêm o desconto de reserva. Ao conceder a alguém permissões para gerenciar uma reserva, isso não lhes dá direitos de gerenciar a assinatura. E se você conceder a alguém permissões para gerenciar uma assinatura no escopo da reserva, isso não lhe concederá direitos de gerenciamento da reserva.
 
Para delegar o gerenciamento de acesso de uma reserva: 
1.  Faça logon no [Portal do Azure](https://portal.azure.com).
2.  Selecione **Todos os serviços** > **Reserva** para listar as reservas às quais você tem acesso.
3.  Selecione a reserva que deseja delegar acesso a outros usuários.
4.  Selecione **Controle de acesso (IAM)** no menu.
5.  Selecione **Adicionar** > **Função** > **Proprietário** (ou uma função diferente se você quiser dar acesso limitado). 
6. Digite o endereço de email do usuário que você deseja adicionar como proprietário. 
7. Selecione o usuário e, em seguida, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as Instâncias de Máquina Virtual Reservadas, confira os artigos a seguir.

- [Pagar antecipadamente pelas Máquinas Virtuais com Instâncias de VM Reservadas](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Economizar dinheiro de máquinas virtuais com Instâncias de Máquinas Virtuais Reservadas](billing-save-compute-costs-reservations.md)
- [Entender como é aplicado o desconto em Instâncias de Máquina Virtual Reservada](billing-understand-vm-reservation-charges.md)
- [Entender o uso da Instância Reservada na sua assinatura Paga pelo Uso](billing-understand-reserved-instance-usage.md)
- [Entender o uso da Instância Reservada no seu registro de Empresa](billing-understand-reserved-instance-usage-ea.md)
- [Os custos de software do Windows não estão incluídos nas Instâncias Reservadas](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
