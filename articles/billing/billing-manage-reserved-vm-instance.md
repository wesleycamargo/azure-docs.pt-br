---
title: Gerenciar reservas do Azure | Microsoft Docs
description: Saiba como você pode alterar o escopo de assinatura e gerenciar o acesso para reservas do Azure.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: yashesvi
ms.openlocfilehash: d47c85d4197f45db50f1974b6faea270e6761237
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628565"
---
# <a name="manage-reservations-for-resources-in-azure"></a>Gerenciar reservas de recursos no Azure

Depois de comprar uma reserva do Azure, convém aplicar a reserva a uma assinatura diferente daquela especificada durante a compra. Como alternativa, se suas máquinas virtuais, bancos de dados SQL ou outros recursos correspondentes estiverem sendo executados em várias assinaturas, convém alterar o escopo de reserva para compartilhado. Para maximizar o desconto da reserva, verifique se o número de instâncias que você comprou corresponde aos atributos e ao número de recursos que você está executando. Para obter mais informações, consulte [reservas Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>Alterar o escopo de uma reserva

 Seu desconto de reserva se aplica a máquinas virtuais, bancos de dados SQL ou outros recursos que correspondem à sua reserva e são executados no escopo da reserva. O escopo de uma reserva pode ser a única assinatura ou todas as assinaturas de seu contexto de cobrança. Se você definir o escopo como assinatura única, a reserva será correspondida à execução de recursos na assinatura selecionada. Se você definir o escopo compartilhado, o Azure corresponderá a reserva de recursos que são executados em todas as assinaturas no contexto de cobrança. O contexto de cobrança depende da assinatura usada para comprar a reserva.

Para atualizar o escopo de uma reserva:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione a reserva.
4. Selecione **Configurações** > **Configuração**.
5. Altere o escopo. Se você alterar de compartilhado para escopo único, poderá selecionar apenas as assinaturas em que é o proprietário. Somente as assinaturas no mesmo contexto de cobrança que a reserva podem ser selecionadas. O contexto de cobrança é determinado pela assinatura que você selecionou quando da compra da reserva. O escopo aplica-se somente a assinaturas da oferta Paga Conforme o Uso MS-AZR-0003P e da oferta Enterprise MS-AZR-0017. No que concerne aos contratos Enterprise, as assinaturas de desenvolvimento/de teste não se qualificam para o desconto de reserva.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Adicionar ou alterar os usuários que podem gerenciar uma reserva

Você pode delegar o gerenciamento de uma reserva adicionando pessoas às funções na reserva. Por padrão, a pessoa que comprou a reserva e o administrador da conta têm a função de Proprietário de reserva.

Você pode gerenciar o acesso às reservas independentemente das assinaturas que obtêm o desconto de reserva. Ao conceder a alguém permissões para gerenciar uma reserva, isso não lhes dá direitos de gerenciar a assinatura. E se você conceder a alguém permissões para gerenciar uma assinatura no escopo da reserva, isso não lhe concederá direitos de gerenciamento da reserva.

Para delegar o gerenciamento de acesso de uma reserva:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reserva** para listar as reservas às quais você tem acesso.
3. Selecione a reserva que deseja delegar acesso a outros usuários.
4. Selecione **Controle de acesso (IAM)** no menu.
5. Selecione **Adicionar** > **Função** > **Proprietário** (ou uma função diferente se você quiser dar acesso limitado).
6. Digite o endereço de email do usuário que você deseja adicionar como proprietário. 
7. Selecione o usuário e, em seguida, selecione **Salvar**.

## <a name="optimize-reserved-vm-instance-for-vm-size-flexibility-or-capacity-priority"></a>Otimize a instância de VMs reservadas para flexibilidade de tamanho de VM ou prioridade de capacidade

 A flexibilidade da instância de VM aplica o desconto de reserva a outras VMs no mesmo [grupo de tamanho de VM](https://aka.ms/RIVMGroups). Por padrão, quando o escopo da reserva é compartilhado, a flexibilidade do tamanho da instância é ativada e a capacidade do datacenter não é priorizada para implantações de VM. Para reservas em que o escopo é único, você pode otimizar a reserva para prioridade de capacidade em vez de flexibilidade de tamanho de instância de VM. A prioridade de capacidade reserva a capacidade do data center para suas implantações, oferecendo confiança adicional em sua capacidade de iniciar as instâncias de VM quando você precisar delas.

Para atualizar o escopo de uma reserva:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione a reserva.
4. Selecione **Configurações** > **Configuração**.
5. Altere a otimizar para configuração.

## <a name="split-a-single-reservation-into-two-reservations"></a>Dividir uma única reserva em duas reservas

 Depois de comprar mais de uma instância, pode ser que você queira atribuir instâncias, em uma reserva, a assinaturas diferentes. Por padrão, todas as instâncias (quantidade especificada durante a aquisição) têm um escopo - uma assinatura única ou compartilhada. Por exemplo, você adquiriu 10 VMs D2 padrão e especificou o escopo para ser a assinatura A. Agora você pode alterar o escopo de sete reservas para a assinatura A e as 3 restantes para a assinatura B. A divisão de uma reserva permite distribuir instâncias para a granularidade gerenciamento do escopo. Você pode simplificar a alocação para as assinaturas escolhendo o escopo compartilhado. Contudo, para fins de gerenciamento ou de orçamento, você pode alocar quantidades a assinaturas específicas.

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
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Você pode atualizar o escopo executando o seguinte comando:
    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:

- [Quais são as reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Pagar antecipadamente por recursos de computação de banco de dados SQL com capacidade reservada do Azure SQL Database](../sql-database/sql-database-reserved-capacity.md)
- [Entender como o desconto de reserva é aplicado](billing-understand-vm-reservation-charges.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não estão incluídos nas reservas](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
