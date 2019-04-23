---
title: O que são Reservas do Azure? | Microsoft Docs
description: Saiba mais sobre as Reservas do Azure e os preços para economizar nos custos de máquinas virtuais, de bancos de dados SQL, do Azure Cosmos DB e de outros recursos.
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: b20983c45cd62b9812cdb52de32a6e29da459efe
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149190"
---
# <a name="what-are-azure-reservations"></a>O que são Reservas do Azure?

As Reservas do Azure ajudam a economizar pagando previamente por um ano ou três anos de máquina virtual, de capacidade de computação do Banco de Dados SQL, de produtividade do Azure Cosmos DB ou de outros recursos do Azure. Pagar previamente permite que você obtenha um desconto nos recursos que você usar. As reservas podem reduzir significativamente os custos com máquinas virtuais, computação do Banco de Dados SQL, Azure Cosmos DB ou outros recursos em até 72% nos preços pagos conforme o uso. As reservas fornecem um desconto de cobrança e não afetam o estado de tempo de execução dos recursos.

Você pode comprar uma instância reservada no [portal do Azure](https://aka.ms/reservations).

## <a name="why-buy-a-reservation"></a>Por que comprar uma reserva?

Se você tiver máquinas virtuais, Azure Cosmos DB ou bancos de dados SQL executadas por longos períodos de tempo, comprar uma reserva dá a opção mais econômica. Por exemplo, quando você executa continuamente quatro instâncias de um serviço sem uma reserva, você será cobrado com taxas pré-pagas. Se você comprar uma reserva para esses recursos, você obtém imediatamente o desconto de reserva. Os recursos não serão mais cobrados com base nas taxas pagas conforme o uso.

## <a name="charges-covered-by-reservation"></a>Encargos cobertos por reserva

Planos de serviço:

- Instância de Máquina Virtual Reservada: Uma reserva cobre apenas os custos de computação da máquina virtual. Não cobre encargos adicionais de software, rede e armazenamento.
- Capacidade reservada do Azure Cosmos DB: Uma reserva abrange uma taxa de transferência provisionada para os seus recursos. Ela não cobre encargos de armazenamento e rede.
- vCore reservado do Banco de Dados SQL: Apenas os custos de computação são incluídos em uma reserva. A licença é cobrada separadamente.

Para as máquinas virtuais do Windows e banco de Dados SQL, você pode cobrir os custos de licenciamento com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).

## <a name="whos-eligible-to-purchase-a-reservation"></a>Quem é elegível para comprar uma reserva?

Para comprar um plano, você deve ter uma função de proprietário de assinatura em um Enterprise (MS-AZR - 0017p ou MS-AZR - 0148p) ou uma assinatura pré-paga (MS-AZR - 003p ou MS-AZR - 0023P). Provedores de soluções de nuvem podem usar o portal do Azure ou [Partner Center](/partner-center/azure-reservations) para comprar reservas do Azure.

Os clientes do EA podem limitar as compras para administradores EA desabilitando o **adicionar instâncias reservadas** opção no Portal EA. Os administradores EA devem ser um proprietário da assinatura pelo menos uma assinatura de EA para comprar uma reserva. A opção é útil para empresas que querem uma equipe centralizada para comprar reservas para centros de custo diferentes. Após a compra, equipes centralizadas podem adicionar os proprietários do Centro de custo para as reservas. Proprietários, em seguida, podem definir o escopo de reserva para suas assinaturas. A equipe central não precisa ter acesso de proprietário de assinatura em que a reserva é adquirida.

Um desconto de reserva aplica-se apenas aos recursos associados aos tipos de assinatura Enterprise, Pagamento Conforme o Uso ou CSP.

## <a name="reservation-scope"></a>Escopo de reserva

Um escopo de reserva determina os recursos aos quais o desconto de reserva se aplica. Um escopo de reserva pode ter valores a seguir:

**Escopo compartilhado** -o desconto de reserva é aplicado aos recursos correspondentes nas assinaturas qualificadas no contexto de cobrança.

- Para clientes do Enterprise Agreement, o contexto de cobrança é o registro.
 Para clientes com assinaturas pré-pagas, o escopo de cobrança é todas as assinaturas qualificadas criadas pelo administrador da conta.

**Assinatura única** -o desconto de reserva é aplicado aos recursos correspondentes na assinatura selecionada.

Você pode [atualizar o escopo depois que você comprar uma reserva](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

## <a name="discounted-subscription-and-offer-types"></a>Tipos de assinatura e a oferta com desconto

Descontos de reserva se aplica às seguintes assinaturas qualificadas e tipos de oferta.

- Contrato Enterprise (números de oferta: MS-AZR-0017P ou MS-AZR - 0148p)
- Pagamento Conforme o Uso (números da oferta): MS-AZR-0003P ou MS-AZR - 0023 P)
- Assinaturas de CSP

Os recursos executados em uma assinatura com outros tipos de oferta não recebem o desconto de reserva.

## <a name="how-is-a-reservation-billed"></a>Como uma reserva é cobrada?

A reserva é cobrada no método de pagamento associado à assinatura. Se você tiver uma assinatura do Enterprise, o custo de reserva será deduzido do seu saldo de investimento. Se o saldo de investimento não cobrir o custo da reserva, você será cobrado pelo excedente. Se você tiver uma assinatura paga conforme o uso, será cobrado imediatamente do seu cartão de crédito. Se você for cobrado por fatura, verá os encargos na sua próxima fatura.

## <a name="how-reservation-discount-is-applied"></a>Como o desconto de reserva é aplicado

O desconto de reserva se aplica a correspondência de atributos que você selecionar quando você compra a reserva o uso de recursos. Os atributos incluem o escopo em que as VMs, os banco de dados SQL, o Azure Cosmos DB ou outros recursos correspondentes são executados. Por exemplo, se você quiser um desconto de reserva para quatro máquinas virtuais de D2 padrão na região Oeste dos EUA, em seguida, selecione a assinatura onde as VMs estão em execução.

Um desconto de reserva é "*uso-it-ou-perder-it*". Se você não tiver recursos correspondentes para qualquer hora, em seguida, você perderá uma quantidade de reserva para essa hora. Você não pode transportar encaminhar horas reservadas não utilizadas.

Quando você desligar um recurso, o desconto de reserva se aplica automaticamente a outro recurso correspondente no escopo especificado. Se nenhum recurso de correspondência é encontrado no escopo especificado, são as horas reservadas *perdido*.

Por exemplo, mais tarde você pode criar um recurso e tem uma reserva de correspondência é subutilizada. Neste exemplo, o desconto de reserva se aplica automaticamente para o novo recurso correspondente.

Se as máquinas virtuais são executadas em diferentes assinaturas em sua conta ou registro, então, selecione o escopo como compartilhado. O escopo compartilhado permite que o desconto de reserva seja aplicado entre assinaturas. Você pode alterar o escopo depois de comprar uma reserva. Para obter mais informações, consulte [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md).

Um desconto de reserva aplica-se apenas aos recursos associados aos tipos de assinatura Enterprise, Pagamento Conforme o Uso ou CSP. Os recursos executados em uma assinatura com outros tipos de oferta não recebem o desconto de reserva.

## <a name="when-the-reservation-term-expires"></a>Quando o prazo de reserva expira

No final do prazo de reserva, o desconto de cobrança expira e a máquina virtual, o Banco de Dados SQL, o Azure Cosmos DB ou outro recurso é cobrado pelo preço pago conforme o uso. As Reservas do Azure não são renovadas automaticamente. Para continuar recebendo o desconto de cobrança, você deve comprar uma nova reserva para serviços e softwares elegíveis.

## <a name="discount-applies-to-different-sizes"></a>Desconto se aplica às tamanhos diferentes

Quando você compra uma reserva, o desconto pode ser aplicado a outras instâncias com atributos que estão dentro do mesmo grupo de tamanho. Esse recurso é conhecido como a flexibilidade de tamanho de instância. A flexibilidade da cobertura de desconto depende do tipo de reserva e dos atributos que você escolhe quando compra a reserva.

Planos de serviço:

- Instâncias de VM reservadas: Quando você comprar a reserva e seleciona **otimizado para**: **flexibilidade de tamanho de instância**, a cobertura de desconto depende do tamanho da VM que você selecionar. A reserva pode ser aplicada aos tamanhos de VMs (máquinas virtuais) no mesmo grupo de série de tamanho. Para obter mais informações, confira [Flexibilidade de tamanho de máquina virtual com Instâncias de VM Reservadas](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Capacidade reservada do Banco de Dados SQL: A cobertura de desconto depende do nível de desempenho escolhido. Para obter mais informações, confira [Entender como um desconto de reserva do Azure é aplicado](billing-understand-reservation-charges.md).
- Capacidade reservada do Azure Cosmos DB: A cobertura de desconto depende da taxa de transferência provisionada. Para obter mais informações, confira [Entender como um desconto de reserva do Azure Cosmos DB é aplicado](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Se você tiver dúvidas ou precisar de Ajuda, [criar uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre as Reservas do Azure com os seguintes artigos:
    - [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md)
    - [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
    - [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)
    - [Custos de software do Windows não estão incluídos nas reservas](billing-reserved-instance-windows-software-costs.md)
    - [Reservas do Azure no programa de CSP (Provedor de Soluções na Nuvem) do Partner Center](/partner-center/azure-reservations)

- Saiba mais sobre as reservas de planos do serviço:
    - [Máquinas virtuais com instâncias de VM reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
    - [Capacidade reservada de recursos do BD Cosmos do Azure com o Azure Cosmos DB](../cosmos-db/cosmos-db-reserved-capacity.md)
    - [Capacidade reservada de recursos de computação de banco de dados SQL com o banco de dados SQL](../sql-database/sql-database-reserved-capacity.md) Saiba mais sobre reservas para planos de software:
    - [Planos de software Red Hat das reservas do Azure](../virtual-machines/linux/prepay-rhel-software-charges.md)
    - [Planos de software do SUSE das reservas do Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
