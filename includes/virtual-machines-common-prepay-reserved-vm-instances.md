---
ms.topic: include
ms.openlocfilehash: 509d2bc3f58c57a3e2e15eed2ea2c70ed17856f3
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063690"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Pagar antecipadamente pelas Máquinas Virtuais com Instâncias de VM Reservadas do Microsoft Azure

Pagar antecipadamente por máquinas virtuais e economizar dinheiro com Instâncias de Máquina Virtual Reservada do Microsoft Azure. Para maiores informações, veja [Azure Reserved VM Instances offering](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Você pode comprar instâncias reservadas do Azure no [portal do Azure](https://portal.azure.com). Comprar uma Instância Reservada:
-   Você deve estar em uma função de Proprietário em pelo menos uma assinatura Enterprise ou Pagamento Conforme o Uso.
-   Para as assinaturas Enterprise, as compras de Instância Reservada devem estar habilitadas no [Portal EA](https://ea.azure.com).
-   Para o programa do CSP (Provedor de Solução na Nuvem) somente os agentes administradores ou agentes de vendas podem comprar as Instâncias Reservadas.

[!IMPORTANT]
Você deve usar um dos métodos descritos abaixo para identificar o tamanho correto da VM para uma compra de reserva.

## <a name="determine-the-right-vm-size-before-purchase"></a>Determinar o tamanho correto da VM antes da compra
1. Consulte o campo AdditionalInfo em seu arquivo de uso ou API de uso para determinar o tamanho correto da VM para uma compra de reserva. Não use os valores dos campos Subcategoria do Medidor ou Produto, pois esses campos não diferenciam as versões S e Não-S de uma VM.
2. Você também pode obter informações precisas sobre o tamanho da VM usando o Powershell, o Azure Resource Manager ou os detalhes da VM no portal do Azure.

## <a name="buy-a-reserved-virtual-machine-instance"></a>Comprar uma Instância de Máquinas Virtuais Reservadas do Azure
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os serviços** > **Reservas**.
3. Selecione **Adicionar** para adquirir uma nova Instância Reservada.
4. Preencha os campos obrigatórios. As instâncias de VM em execução que correspondem aos atributos que você selecionar, ficam qualificadas para obter o desconto de Instância Reservada. O número real de suas instâncias VM que obtêm o desconto depende do escopo e da quantidade selecionada.

    | Campo      | DESCRIÇÃO|
    |:------------|:--------------|
    |NOME        |O nome desta Instância Reservada.| 
    |Assinatura|A assinatura usada para pagar pela Instância Reservada. O método de pagamento da assinatura é cobrado nos custos iniciais para a Instância Reservada. O tipo de assinatura deve ser um contrato enterprise (número de oferta: MS-AZR-0017P) ou de Pagamento Conforme o Uso (número da oferta: MS-AZR-0003P). Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média. Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.|    
    |Escopo       |O escopo de assinatura de Instância Reservada pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar: <ul><li>Assinatura única - o desconto da Instância Reservada é aplicado às VMs nesta assinatura. </li><li>Compartilhado - o desconto da Instância Reservada é aplicado às VMs em execução em qualquer assinatura, dentro de seu contexto de cobrança. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas (exceto as assinaturas de desenvolvimento/teste) no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.</li></ul>|
    |Local padrão    |A região do Azure que é coberta pela Instância Reservada.|    
    |Tamanho da VM     |O tamanho das instâncias de VM.|
    |Termo        |Um ano ou três anos.|
    |Quantidade    |O número de instâncias sendo compradas na Instância Reservada. A quantidade é o número de instâncias de VM que podem obter o desconto de cobrança. Por exemplo, se estiver executando 10 VMs Standard_D2 no Leste dos EUA, pode especificar a quantidade como 10 para maximizar o benefício para todas as máquinas em execução. |
5. Você pode exibir o custo da Instância Reservada ao selecionar I **Calcular custo**.

    ![Captura de tela antes de enviar a compra de Instância Reservada](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Selecione **Comprar**.
7. Selecione **Exibir essa Reserva** para ver o status de sua compra.

    ![Captura de tela depois de enviar a compra de Instância Reservada](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Próximas etapas 
O desconto da Instância Reservada é aplicado automaticamente ao número de máquinas virtuais em execução que correspondem ao escopo e atributos da Instância Reservada. Você pode atualizar o escopo de Instância Reservada por meio do [portal do Azure](https://portal.azure.com), PowerShell, CLI ou por meio da API. 

Para saber como gerenciar uma instância reservada, consulte [Gerenciar instâncias reservadas no Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

Para saber mais sobre as instâncias reservadas do Azure, consulte os seguintes artigos:

- [O que são instâncias de VM reservadas do Azure?](../articles/billing/billing-save-compute-costs-reservations.md)
- [Gerenciar instâncias reservadas no Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
- [Entender como é aplicado o desconto na Instância Reservada](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Entender o uso da Instância Reservada na sua assinatura Paga pelo Uso](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Entender o uso da Instância Reservada no seu registro de Empresa](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Os custos de software do Windows não estão incluídos nas Instâncias Reservadas](../articles/billing/billing-reserved-instance-windows-software-costs.md)
- [Instâncias reservadas no programa do CSP (Provedor de Soluções na Nuvem CSP) do Partner Center](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.