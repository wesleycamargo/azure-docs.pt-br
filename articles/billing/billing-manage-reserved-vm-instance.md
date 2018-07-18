---
title: Gerenciar instâncias de VMs reservadas do Azure | Microsoft Docs
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
ms.openlocfilehash: ddb9d46dc2689b0dbcd8734e276916f7cd9d2728
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064232"
---
# <a name="manage-reserved-instances-in-azure"></a>Gerenciar instâncias reservadas no Azure

Depois de adquirir uma Instância de VM Reservada do Azure, você poderá aplicar a instância reservada a uma assinatura diferente daquela especificada durante a compra. Como alternativa, se as máquinas virtuais correspondentes estiverem sendo executadas em várias assinaturas, você poderá alterar o escopo da instância reservada para compartilhado. Para maximizar o desconto de instância reservada, verifique se o número de instâncias que você comprou corresponde aos atributos e ao número de máquinas virtuais que você está executando. Para saber mais sobre as instâncias reservadas do Azure, consulte [Economize dinheiro pagando antecipadamente as máquinas virtuais do Azure](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reserved-instance"></a>Alterar o escopo de uma instância reservada
 Seu desconto de instância reservado se aplica a máquinas virtuais que correspondem à sua instância reservada e são executadas no escopo da instância reservada. O escopo de uma Instância Reservada pode ser a única assinatura ou todas as assinaturas de seu contexto de cobrança. Se você definir o escopo para assinatura única, a instância reservada será correspondida para a execução de máquinas virtuais na assinatura selecionada. Se você definir o escopo como compartilhado, o Azure fará a correspondência da instância reservada com as máquinas virtuais executadas em todas as inscrições no contexto de cobrança. O contexto de faturamento depende da assinatura usada para comprar a instância reservada. Para obter mais informações, consulte [Pré-pagamento de VMs com Instâncias Reservadas](https://go.microsoft.com/fwlink/?linkid=861721).

Para atualizar o escopo de uma instância reservada: 
1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. To update the scope of a reserved instance:Selecione a Instância Reservada.
4. Selecione **Configurações** > **Configuração**.
5. Altere o escopo. Se você alterar de compartilhado para escopo único, poderá selecionar apenas as assinaturas em que é o proprietário. Somente assinaturas no mesmo contexto de faturamento da instância reservada podem ser selecionadas. O contexto de cobrança é determinado pela assinatura que você selecionou quando a instância reservada foi comprada. O escopo aplica-se somente a assinaturas da oferta Paga Conforme o Uso MS-AZR-0003P e da oferta Enterprise MS-AZR-0017. Para contratos empresariais, as assinaturas de desenvolvimento/teste não são elegíveis para obter o desconto de instância reservada.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>Dividir uma única instância reservada em duas instâncias reservadas
 Depois de comprar mais de uma instância, convém atribuir instâncias dentro de uma instância reservada a assinaturas diferentes. Por padrão, todas as instâncias (quantidade especificada durante a aquisição) têm um escopo - uma assinatura única ou compartilhada. Por exemplo, você adquiriu 10 VMs D2 padrão e especificou o escopo para ser a assinatura A. Agora você pode alterar o escopo de sete instâncias reservadas para a assinatura A e as 3 restantes para a assinatura B. A divisão de uma instância reservada permite distribuir instâncias para gerenciamento de escopo granular. Você pode simplificar a alocação para as assinaturas escolhendo o escopo compartilhado. Contudo, para fins de gerenciamento ou de orçamento, você pode alocar quantidades a assinaturas específicas.

 Você pode dividir uma instância reservada em duas instâncias reservadas pelo PowerShell, CLI ou por meio da API.

### <a name="split-a-reserved-instance-by-using-powershell"></a>Dividir uma instância reservada usando o PowerShell
1. Obtenha o ID da ordem de instância reservada executando o seguinte comando:

    ```powershell
    # Get the reserved instance orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Veja os detalhes de uma instância reservada:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Divida a instância reservada em dois e distribua as instâncias:

    ```powershell
    # Split the reserved instance. The sum of the reserved instances, the quantity, must equal the total number of instances in the reserved instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. Você pode atualizar o escopo executando o seguinte comando:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>Adicionar ou alterar usuários que podem gerenciar uma instância reservada
Você pode delegar o gerenciamento de uma instância reservada adicionando pessoas a funções na instância reservada. Por padrão, a pessoa que comprou a instância reservada e o administrador da conta têm a função de proprietário na instância reservada. 

Você pode gerenciar o acesso a instâncias reservadas independentemente das assinaturas que recebem o desconto da instância reservada. Quando você concede permissões a alguém para gerenciar uma instância reservada, isso não lhes dá direitos para gerenciar a assinatura. E se você conceder permissões a alguém para gerenciar uma assinatura no escopo da instância reservada, isso não lhes dará direitos para gerenciar a instância reservada.
 
Para delegar o gerenciamento de acesso para uma instância reservada: 
1.  Faça logon no [Portal do Azure](https://portal.azure.com).
2.  Selecione **Todos os serviços** >  **Reserva** para listar as instâncias reservadas às quais você tem acesso.
3.  Selecione a instância reservada para a qual você deseja delegar acesso a outros usuários.
4.  Selecione **Controle de acesso (IAM)** no menu.
5.  Selecione **Adicionar** > **Função** > **Proprietário** (ou uma função diferente se você quiser dar acesso limitado). 
6. Digite o endereço de email do usuário que você deseja adicionar como proprietário. 
7. Selecione o usuário e, em seguida, selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre as instâncias reservadas do Azure, consulte os seguintes artigos:

- [O que são instâncias de VM reservadas do Azure?](billing-save-compute-costs-reservations.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Entenda como o desconto de instância reservada é aplicado](billing-understand-vm-reservation-charges.md)
- [Entenda o uso de instâncias reservadas para sua assinatura do Pay-As-You-Go](billing-understand-reserved-instance-usage.md)
- [Entenda o uso reservado de instâncias para o seu registro Enterprise ](billing-understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não incluídos com instâncias reservadas](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
