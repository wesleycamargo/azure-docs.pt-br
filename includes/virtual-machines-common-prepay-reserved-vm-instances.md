---
author: yashesvi
ms.author: yashar
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 08-07-2018
ms.openlocfilehash: 19a153a5cdc9d5f878494984313baebd12dbcbb5
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39631156"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Pagar antecipadamente pelas Máquinas Virtuais com Instâncias de VM Reservadas do Microsoft Azure

Pagar antecipadamente por máquinas virtuais e economizar dinheiro com Instâncias de Máquina Virtual Reservada do Microsoft Azure. Para maiores informações, veja [Azure Reserved VM Instances offering](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Você pode comprar uma Instância de VM Reservada no [portal do Azure](https://portal.azure.com). Para comprara uma instância:

- Você deve estar em uma função de Proprietário em pelo menos uma assinatura Enterprise ou Pagamento Conforme o Uso.
- Para as assinaturas Enterprise, as compras de reserva devem estar habilitadas no [portal EA](https://ea.azure.com).
- Para o programa do CSP (Provedor de Solução na Nuvem) somente os agentes administradores ou agentes de vendas podem comprar as reservas.

## <a name="determine-the-right-vm-size-before-purchase"></a>Determinar o tamanho correto da VM antes da compra

Os campos Produto e Subcategoria do Medidor nos dados de uso não distinguem entre tamanhos de VM que usam o armazenamento Premium e tamanhos de VM que não usam o armazenamento Premium, usar esses campos para determinar o tamanho da VM para a compra de reserva pode levar a uma compra de reserva incorreta e não fornecer descontos de reserva a você. Use um dos métodos a seguir para determinar o tamanho de VM correto para a compra de reserva.

- Consulte o campo AdditionalInfo em seu arquivo de uso ou API de uso para determinar o tamanho correto da VM para uma compra de reserva. Não use os valores dos campos Subcategoria do Medidor ou Produto, pois esses campos não diferenciam as versões S e Não-S de uma VM.
- Você também pode obter informações precisas sobre o tamanho da VM usando o Powershell, o Azure Resource Manager ou os detalhes da VM no portal do Azure.

As Instâncias de VM Reservadas estão disponíveis para a maioria dos tamanhos de VM, com algumas exceções:

- VMs em versão prévia: qualquer série ou tamanho de VM que esteja em versão prévia não estará disponível para compra de reserva.
- Nuvens: as reservas não estão disponíveis para compra nas regiões Azure US Government, Alemanha ou China.
- Cota insuficiente: uma reserva que tenha um escopo de uma assinatura única precisa ter uma cota de vCPU disponível na assinatura para a nova RI. Por exemplo, se a assinatura de destino tiver um limite de cota de 10 vCPUs para a Série D, você não poderá comprar uma reserva para 11 instâncias Standard_D1. A verificação de cota para reservas inclui as VMs já implantadas na assinatura. Por exemplo, se a assinatura tiver uma cota de 10 vCPUs para o D-Series e tiver duas instâncias standard_D1 implantadas, você poderá comprar uma reserva para 10 instâncias standard_D1 nessa assinatura. 
- Restrições de capacidade: em circunstâncias raras, o Azure limita a compra de novas reservas para o subconjunto de tamanhos de VM, devido à baixa capacidade em uma região.

## <a name="buy-a-reserved-vm-instance"></a>Comprar uma Instância de VM Reservada

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** para adquirir uma nova reserva.
4. Preencha os campos obrigatórios. As instâncias de VM em execução que correspondem com os atributos que você selecionar, ficam qualificados para obter o desconto de reserva. O número real de suas instâncias VM que obtêm o desconto depende do escopo e da quantidade selecionada.

    | Campo      | DESCRIÇÃO|
    |:------------|:--------------|
    |NOME        |O nome dessa reserva.| 
    |Assinatura|A assinatura usada para pagar pela reserva. São cobrados os custos iniciais para a reserva à forma de pagamento na assinatura. O tipo de assinatura deve ser um contrato enterprise (número de oferta: MS-AZR-0017P) ou de Pagamento Conforme o Uso (número da oferta: MS-AZR-0003P). Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média. Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.|    
    |Escopo       |O escopo de assinatura pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar: <ul><li>Assinatura única - o desconto da reserva é aplicado às VMs nesta assinatura. </li><li>Compartilhado - o desconto da reserva é aplicado às VMs em execução em qualquer assinatura, dentro de seu contexto de cobrança. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas (exceto as assinaturas de desenvolvimento/teste) no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.</li></ul>|
    |Local padrão    |A região do Azure que é coberta pela reserva.|    
    |Tamanho da VM     |O tamanho das instâncias de VM.|
    |Otimizar para     |A flexibilidade de tamanho da instância de VM aplica o desconto de reserva a outras VMs no mesmo [grupo de tamanhos de VM](https://aka.ms/RIVMGroups). A prioridade da capacidade reserva a capacidade de datacenter para suas implantações. Isso oferece confiança adicional na sua capacidade de iniciar as instâncias de VM quando precisar delas. A prioridade de capacidade está disponível apenas quando o escopo de reserva é uma assinatura única. |
    |Termo        |Um ano ou três anos.|
    |Quantidade    |O número de instâncias sendo compradas na reserva. A quantidade é o número de instâncias de VM que podem obter o desconto de cobrança. Por exemplo, se estiver executando 10 VMs Standard_D2 no Leste dos EUA, pode especificar a quantidade como 10 para maximizar o benefício para todas as máquinas em execução. |
5. Você pode exibir o custo da reserva quando você seleciona **Calcular custo**.

    ![Captura de tela antes de enviar a compra de reserva](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Selecione **Comprar**.
7. Selecione **Exibir essa Reserva** para ver o status de sua compra.

    ![Captura de tela após enviar a compra de reserva](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Próximas etapas

O desconto da reserva é aplicado automaticamente ao número de máquinas virtuais em execução que correspondem ao escopo e atributos da reserva. Você pode atualizar o escopo da reserva por meio do [portal do Azure](https://portal.azure.com), PowerShell, CLI ou por meio da API.

Para aprender a gerenciar uma reserva, confira [Gerenciar reservas do Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

Para saber mais sobre as reservas do Azure, consulte os seguintes artigos:

- [O que são as reservas do Azure?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Gerenciar reservas no Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Entender como o desconto de reserva é aplicado](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Entender o uso de instâncias reservadas para sua assinatura de Pagamento Conforme o Uso](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para o seu registro Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Custos de software do Windows não incluídos com instâncias reservadas](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Reservas do Azure no programa do CSP (Provedor de Solução do Partner Center)](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
