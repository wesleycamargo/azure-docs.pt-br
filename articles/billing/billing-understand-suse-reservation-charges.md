---
title: Entenda o desconto e uso do plano SUSE - Reservas do Azure | Microsoft Docs
description: Saiba como os descontos de planos do SUSE são aplicados ao software SUSE em máquinas virtuais.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2018
ms.author: cwatson
ms.openlocfilehash: 62f75be44ed92528b48fc0f093f5966284662312
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393682"
---
# <a name="understand-how-the-suse-linux-enterprise-software-plan-discount-is-applied"></a>Entenda como o desconto do plano de software do SUSE Linux Enterprise é aplicado

Depois de adquirir um plano do SUSE Linux, o desconto é aplicado automaticamente às máquinas virtuais (VMs) do SUSE implantadas que correspondem à reserva. Um plano do SUSE Linux cobre o custo de execução do software SUSE em uma VM do Azure.

Para comprar o plano correto do SUSE Linux, você precisa entender quais VMs do SUSE são executadas e o número de vCPUs nessas VMs. Use as seções a seguir para ajudar a identificar, a partir do arquivo CSV de uso, qual plano comprar.

## <a name="discount-applies-to-different-vm-sizes-with-instance-size-flexibility"></a>O desconto se aplica a diferentes tamanhos de VM com flexibilidade de tamanho de instância

Como as instâncias de VMs reservadas, as compras do plano do SUSE oferecem flexibilidade no tamanho da instância. Isso significa que seu desconto se aplica mesmo quando você implanta uma VM com uma contagem diferente de vCPU. O desconto se aplica a diferentes tamanhos de VM dentro do plano de software.

O desconto se aplica a diferentes tamanhos de VM dentro do plano de software. A proporção compara a área de cobertura relativa de cada medidor nesse grupo. A proporção depende das vCPUs da VM. Use o valor da razão para calcular quantas instâncias de VM obtêm o desconto do plano do SUSE Linux.

Por exemplo, se você comprar um plano para o SUSE Linux Enterprise Server para HPC Priority para uma VM com 3 ou 4 vCPUs, a proporção dessa reserva será 2. O desconto cobre o custo do software SUSE para:

- 2 VMs implantadas com 1 ou 2 vCPUs,
- 1 VM implantada com 3 ou 4 vCPUs,
- ou 0,77 ou cerca de 77% de uma VM com 5 ou mais vCPUs.

A taxa de 5 ou mais vCPUs é 2.6. Portanto, uma reserva para o SUSE com uma VM com 5 ou mais vCPUs cobre apenas uma parte do custo do software, que é de cerca de 77%.

## <a name="understand-your-suse-vm-usage-before-buying-a-suse-linux-plan"></a>Entenda o uso da VM do SUSE antes de comprar um plano do SUSE Linux

As tabelas a seguir mostram os planos de software para os quais você pode comprar uma reserva, os medidores de uso associados e as taxas de cada uma.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server para HPC Priority

Nome do marketplace do portal do Azure:

- SLES 12 SP3 para HPC (prioridade)

|VM SUSE | MeterId| Proporção| Tamanho de VM de exemplo|
| -------| ------------------------| --- |--- |
|SLES para HPC 1 ou 2 vCPUs|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES para HPC 3-4 vCPUs|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES para HPC 5+ vCPUs|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

Nome do marketplace do portal do Azure:

- SLES 12 SP3 para HPC

|VM SUSE | MeterId | Proporção|Tamanho de VM de exemplo|
| ------- | --- | ------------------------| --- | --- |
|SLES para HPC 1 ou 2 vCPUs |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES para HPC 3-4 vCPUs|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1.92308|D4s_v3|
|SLES para HPC 5+ vCPUs |907a85de-024f-4dd6-969c-347d47a1bdff|2.92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Nomes do marketplace no portal do Azure:

- SLES para SAP 15 (prioridade)
- SLES para SAP 12 SP3 (prioridade)
- SLES para SAP SP2 12 (prioridade)

|VM SUSE | MeterId | Proporção|Tamanho de VM de exemplo|
| ------- |------------------------| --- | --- |
|SLES para SAP Priority 1 ou 2 vCPUs|497fe0b6-Fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES para SAP prioridade 3 e 4 vCPUs |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES para SAP de prioridade 5 + vCPUs |18ae79cd-dfce-48c9-897b-ebd3053c6058|2.41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>Prioridade do SUSE Linux Enterprise Server

Nomes do marketplace no portal do Azure:

- SLES 15 (PRIORIDADE)
- SLES 12 SP3 (prioridade)
- SLES 11 SP4 (prioridade)

|VM SUSE | MeterId | Proporção|Tamanho de VM de exemplo|
| ------- |------------------------| --- |--- |
|SLES 1 vCPU|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES 2 a 4 vCPUs |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES 2 a 4 vCPUs |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|SLES 6 vCPUs |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES 8 vCPUs |e11331a8-fd32-4e71-b60e-4de2a818c67a|3.2|D8s_v3|
|SLES 12 vCPUs de núcleo |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3.2||
|SLES 16 vCPUs |bb21066f-fe46-46D3-8006-b326b1663e52|3.2| D16s_v3|
|SLES 20 vCPUs |c5228804-1de6-4bd4-a61c-501d9003acc8|3.2| |
|SLES 24 núcleos vCPUs |-005 d-4075 ac11 822ccde9e8f6|3.2| ND24s|
|SLES 32 vCPUs |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3.2| D32s_v3|
|SLES 40 núcleos vCPUs |a161d3d3-0592-4956-9b64-6829678b6506|3.2||
|SLES 64 vCPUs |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3.2| D64s_v3|
|SLES 72 núcleos vCPUs |93329a72-24d7-4faa-93d9-203f367ed334|3.2|F72s_v2|
|SLES 96 núcleos vCPUs |2018c3a8-ff13-41f8-b64d-9558c5206547|3.2||
|SLES 128 núcleos vCPUss |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3.2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Nomes do marketplace no portal do Azure:

- SLES 15
- SLES 15 (padrão)
- SLES 12 SP3 (padrão)

|VM SUSE | MeterId | Proporção|Tamanho de VM de exemplo|
| ------- |------------------------| --- |--- |
|SLES 1-2 núcleos vCPUs |4b2fecfc-B110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES 3 a 4 núcleos vCPUs |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|D4s_v3|
|SLES 5 + vCPUs |7b349b65-d906-42e5-833f-b2af38513468|2.30769| D8s_v3|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as reservas, consulte os seguintes artigos:

- [O que são Reservas do Azure?](billing-save-compute-costs-reservations.md)
- [Pagar antecipadamente por planos de software do SUSE com reservas do Azure](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Pré-pagamento para máquinas virtuais com instâncias de VMs reservadas do Azure](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Gerenciar Reservas do Azure](billing-manage-reserved-vm-instance.md)
- [Entender o uso de reserva para a sua assinatura paga conforme o uso](billing-understand-reserved-instance-usage.md)
- [Entender o uso de reserva para seu registro de empresa](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.