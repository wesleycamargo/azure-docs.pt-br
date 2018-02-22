# <a name="prepay-for-virtual-machines-with-reserved-vm-instances"></a>Pagar antecipadamente pelas Máquinas Virtuais com Instâncias de VM Reservadas

Pagar antecipadamente por máquinas virtuais e economizar dinheiro com Instâncias de Máquina Virtual Reservada. Para obter mais informações, confira [Oferta de instâncias de Máquina Virtual Reservada](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Você pode comprar Instâncias de Máquina Virtual Reservadas no [portal do Azure](https://portal.azure.com). Para comprar uma Instância de Máquinas Virtuais Reservadas do Azure:
-   Você deve estar em uma função de Proprietário em pelo menos uma assinatura Enterprise ou Pagamento Conforme o Uso.
-   Para as assinaturas Enterprise, as compras de reserva devem estar habilitadas no [portal EA](https://ea.azure.com).

## <a name="buy-a-reserved-virtual-machine-instance"></a>Comprar uma Instância de Máquinas Virtuais Reservadas do Azure
1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Selecione **Mais Serviços** > **Reservas**.
3. Selecione **Adicionar** para adquirir uma nova reserva.
4. Preencha os campos obrigatórios. As instâncias de VM em execução que correspondem com os atributos que você selecionar, ficam qualificados para obter o desconto de reserva. O número real de suas instâncias VM que obtêm o desconto depende do escopo e da quantidade selecionada.

    | Campo      | DESCRIÇÃO|
    |:------------|:--------------|
    |NOME        |O nome dessa reserva.| 
    |Assinatura|A assinatura usada para pagar pela reserva. São cobrados os custos iniciais para a reserva à forma de pagamento na assinatura. O tipo de assinatura deve ser um contrato enterprise (número de oferta: MS-AZR-0017P) ou de Pagamento Conforme o Uso (número da oferta: MS-AZR-0003P). Para uma assinatura de empresa, os encargos são deduzidos do saldo do compromisso monetário do registro ou cobrados como média. Para a assinatura de Pagamento Conforme o Uso, as cobranças são feitas ao cartão de crédito ou à forma de pagamento de faturas na assinatura.|    
    |Escopo       |O escopo de assinatura pode abranger uma ou várias assinaturas (escopo compartilhado). Se você selecionar: <ul><li>Assinatura única - o desconto da reserva é aplicado às VMs nesta assinatura. </li><li>Compartilhado - o desconto da reserva é aplicado às VMs em execução em qualquer assinatura, dentro de seu contexto de cobrança. Para clientes empresariais, o escopo compartilhado é o registro e inclui todas as assinaturas (exceto as assinaturas de desenvolvimento/teste) no registro. Para clientes de Pagamento Conforme o Uso, o escopo compartilhado consiste em todas as assinaturas de Pagamento Conforme o Uso criadas pelo administrador da conta.</li></ul>|
    |Local padrão    |A região do Azure que é coberta pela reserva.|    
    |Tamanho da VM     |O tamanho das instâncias de VM.|
    |Termo        |Um ano ou três anos.|
    |Quantidade    |O número de instâncias sendo compradas na reserva. A quantidade é o número de instâncias de VM que podem obter o desconto de cobrança. Por exemplo, se estiver executando 10 VMs Standard_D2 no Leste dos EUA, pode especificar a quantidade como 10 para maximizar o benefício para todas as máquinas em execução. |
5. Você pode exibir o custo da reserva quando você seleciona **Calcular custo**.

    ![Captura de tela antes de enviar a compra de reserva](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvminstance-purchase.png)

6. Selecione **Comprar**.
7. Selecione **Exibir essa Reserva** para ver o status de sua compra.

    ![Captura de tela antes de enviar a compra de reserva](./media/virtual-machines-buy-compute-reservations/virtualmachines-reservedvmInstance-submit.png)

## <a name="next-steps"></a>Próximas etapas 
O desconto da reserva é aplicado automaticamente ao número de máquinas virtuais em execução que correspondem ao escopo e atributos da reserva. Você pode atualizar o escopo de reserva por meio do [portal do Azure](https://portal.azure.com), PowerShell, CLI ou por meio da API. 

Para aprender a gerenciar uma reserva, confira [Gerenciar Instâncias de Máquinas Virtuais Reservadas do Azure](../articles/billing/billing-manage-reserved-vm-instance.md).

Para saber mais sobre as Instâncias de Máquina Virtual Reservadas, consulte os artigos a seguir.

- [Economizar dinheiro de máquinas virtuais com Instâncias de Máquinas Virtuais Reservadas](../articles/billing/billing-save-compute-costs-reservations.md)
- [Entender como é aplicado o desconto em Instâncias de Máquina Virtual Reservada](../articles/billing/billing-understand-vm-reservation-charges.md)
- [Entender o uso da Instância Reservada na sua assinatura Paga pelo Uso](../articles/billing/billing-understand-reserved-instance-usage.md)
- [Entender o uso da Instância Reservada no seu registro de Empresa](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
- [Os custos de software do Windows não estão incluídos nas Instâncias Reservadas](../articles/billing/billing-reserved-instance-windows-software-costs.md)