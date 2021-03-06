---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 8afaeaea3c8ceb0de33147e70e00f3f57c857ca7
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883151"
---
Quando você cria uma VM (máquina virtual), reinicia VMs paradas (desalocadas) ou redimensiona uma VM, o Microsoft Azure aloca recursos de computação para sua assinatura. Estamos continuamente investindo em infraestrutura e recursos adicionais para garantir que sempre tenhamos todos os tipos de VM disponíveis para dar suporte à demanda do cliente. No entanto, você pode enfrentar ocasionalmente falhas de alocação de recursos devido ao crescimento sem precedentes da demanda por serviços do Azure em regiões específicas. Esse problema pode ocorrer quando você tenta criar ou iniciar VMs em uma região, enquanto as VMs exibem o seguinte código de erro e a seguinte mensagem:

**Erro de código**: AllocationFailed ou ZonalAllocationFailed

**Mensagem de erro**: “Falha na alocação. Não temos capacidade suficiente para o tamanho de VM solicitado nesta região. Leia mais sobre como melhorar a probabilidade de sucesso de alocação em https://aka.ms/allocation-guidance"

Este artigo explica as causas de algumas das falhas de alocação mais comuns e sugere possíveis correções.

Se o problema do Azure não for resolvido neste artigo, visite os [Fóruns do Azure no MSDN e Excedente de Pilha](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou usando @AzureSupport no Twitter. Além disso, você pode registrar uma solicitação de suporte do Azure selecionando Obter suporte no site de [suporte do Azure](https://azure.microsoft.com/support/options/).

Até o tipo de VM preferencial estar disponível em sua região preferida, aconselhamos que os clientes que encontrarem problemas de implantação considerem a diretriz na tabela a seguir como uma solução alternativa. 

Identifique o cenário que melhor descreva seu caso e, em seguida, tente novamente a solicitação de alocação usando a solução alternativa sugerida correspondente para aumentar a probabilidade de sucesso de alocação. Como alternativa, você pode sempre tentar novamente mais tarde. Isso ocorre porque recursos suficientes podem ter sido liberados no cluster, região ou zona para acomodar a solicitação. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>Redimensionar uma VM ou adicionar VMs a um conjunto de disponibilidade existente

### <a name="cause"></a>Causa

A solicitação de redimensionar ou adicionar uma VM a um conjunto de disponibilidade existente deve ser tentada no cluster original que hospeda esse conjunto. O tamanho da VM solicitada é suportado pelo cluster, mas o cluster pode não ter capacidade suficiente no momento. 

### <a name="workaround"></a>Solução alternativa

Quando a VM puder fazer parte de um conjunto de disponibilidade diferente, crie uma VM em um conjunto de disponibilidade diferente (na mesma região). Essa nova VM pode ser adicionada à mesma rede virtual.

Pare (desaloque) todas as VMs no mesmo conjunto de disponibilidade e reinicie cada uma delas.
Para parar: Clique em Grupos de recursos > [seu grupo de recursos] > Recursos > [seu conjunto de disponibilidade] > Máquinas Virtuais > [sua máquina virtual] > Parar.
Depois de parar todas as VMs, selecione a primeira VM e clique em Iniciar.
Essa etapa garante que uma nova tentativa de alocação seja executada e que um novo cluster com capacidade suficiente possa ser selecionado.

## <a name="restart-partially-stopped-deallocated-vms"></a>Reiniciar VMs parcialmente paradas (desalocadas)

### <a name="cause"></a>Causa

A desalocação parcial significa que você parou (desalocou) uma ou mais, mas não todas, VMs em um conjunto de disponibilidade. Quando você desaloca uma VM, os recursos associados são liberados. Reiniciar as VMs em um conjunto de disponibilidade parcialmente desalocado é o mesmo que adicionar VMs a um conjunto de disponibilidade existente. Portanto, a solicitação de alocação deve ser tentada no cluster original que hospeda o grupo de disponibilidade existente que pode não ter capacidade suficiente.

### <a name="workaround"></a>Solução alternativa

Pare (desaloque) todas as VMs no mesmo conjunto de disponibilidade e reinicie cada uma delas.
Para parar: Clique em Grupos de recursos > [seu grupo de recursos] > Recursos > [seu conjunto de disponibilidade] > Máquinas Virtuais > [sua máquina virtual] > Parar.
Depois de parar todas as VMs, selecione a primeira VM e clique em Iniciar.
Isso garantirá que uma nova tentativa de alocação seja executada e que um novo cluster com capacidade suficiente possa ser selecionado.

## <a name="restart-fully-stopped-deallocated-vms"></a>Reiniciar VMs totalmente paradas (desalocadas)

### <a name="cause"></a>Causa

A desalocação total significa que você parou (desalocou) todas as VMs em um conjunto de disponibilidade. A solicitação de alocação para reiniciar essas VMs se destinará a todos os clusters compatíveis com o tamanho desejado dentro da região ou da zona. Altere sua solicitação de alocação de acordo com as sugestões deste artigo e repita a solicitação para aumentar a chance de sucesso de alocação. 

### <a name="workaround"></a>Solução alternativa

Se você usar séries ou tamanhos de VM mais antigos, como Dv1, DSv1, Av1, D15v2 ou DS15v2, considere a migração para versões mais recentes. Consulte essas recomendações para tamanhos específicos de VM.
Se você não tiver a opção de usar um tamanho de VM diferente, tente implantar em uma região diferente dentro a mesma área geográfica. Para obter mais informações sobre tamanhos de VM disponíveis em cada região em https://aka.ms/azure-regions

Se você estiver usando zonas de disponibilidade, tente outra zona dentro da região que pode ter a capacidade disponível para o tamanho solicitado de VM.

Se sua solicitação de alocação for grande (mais de 500 núcleos), consulte o guia nas seções a seguir para dividir a solicitação em implantações menores.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Falhas de alocação para tamanhos de VM mais antigos (Av1, Dv1, DSv1, D15v2, DS15v2 etc.)

À medida que expandimos a infraestrutura do Azure, nós implantamos hardware de geração mais recente projetado para dar suporte aos tipos de máquina virtual mais recentes. Algumas das VMs de séries mais antigas não serão executadas em nossa infraestrutura de geração mais recente. Por esse motivo, os clientes podem ocasionalmente enfrentar falhas de alocação para essas SKUs herdadas. Para evitar esse problema, recomendamos que os clientes que estejam usando as máquinas virtuais de séries herdadas considerem a migração para as VMs equivalentes mais recentes, conforme recomendações a seguir: Essas VMs são otimizadas para o hardware mais recente, gerando preço e desempenho melhores. 

|Série/tamanho de VM herdada|Série/tamanho recomendado de VM mais nova|Mais informações|
|----------------------|----------------------------|--------------------|
|Série Av1|[Série Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Série Dv1 ou DSv1 (D1 a D5)|[Dv3 ou DSV3](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Série Dv1 ou DSv1 (D11 a D14)|[Ev3 ou Esv3-series](../articles/virtual-machines/windows/sizes-memory.md#esv3-series)|
|D15v2 ou DS15v2|Se você estiver usando o modelo de implantação do Gerenciador de Recursos para aproveitar os tamanhos maiores de VM, considere mudar para E16v3 / E16sv3 ou E32v3 / E32sv3. Eles são projetados para execução em hardware de geração mais recente. Se você estiver usando o modelo de implantação do Gerenciador de Recursos para se certificar de que sua instância de VM esteja isolada para o hardware dedicado a um único cliente, considere a migração para os novos tamanhos de VM isolados, E64i_v3 ou E64is_v3, que são projetados para execução em hardware de geração mais recente. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/


## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Falhas de alocação para implantações grandes (mais de 500 núcleos)

Reduza o número de instâncias do tamanho de VM solicitado e repita a operação de implantação. Além disso, para implantações maiores, convém avaliar os [conjunto de dimensionamento de máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). O número de instâncias VM pode automaticamente aumentar ou diminuir em resposta à demanda ou a um agendamento definido, e você tem uma maior probabilidade de êxito de alocação porque as implantações podem ser distribuídas em vários clusters. 

## <a name="background-information"></a>Informações básicas
### <a name="how-allocation-works"></a>Como funciona a alocação
Os servidores são particionados em clusters nos datacenters do Microsoft Azure. Normalmente, uma tentativa de solicitação de alocação é feita em vários clusters, mas é possível que determinadas restrições da solicitação de alocação forcem a plataforma do Azure a repeti-la em um único cluster. Neste artigo, chamaremos isso de "fixada a um cluster". O diagrama 1 a seguir ilustra o caso de uma tentativa de alocação normal feita em vários clusters. O diagrama 2 ilustra o caso de uma alocação que foi fixada ao Cluster 2 por se tratar do local de hospedagem do Serviço de Nuvem CS_1 ou do conjunto de disponibilidade existente.
![Diagrama de alocação](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Motivos das falhas de alocação
Quando a solicitação de alocação é fixada a um cluster, é mais provável que haja falha na localização de recursos gratuitos, pois o pool de recursos disponíveis é menor. Além disso, quando a solicitação de alocação é fixada a um cluster, mas não há suporte para o tipo de recurso solicitado ao cluster, a solicitação falha mesmo que o cluster tenha recursos livres. O Diagrama 3 a seguir demonstra o caso em que uma alocação fixada falha porque o único cluster candidato não dispõe de recursos gratuitos. O diagrama 4 demonstra o caso em que uma alocação fixada falha porque o único cluster candidato não é compatível com o tamanho da VM solicitado, mesmo que o cluster disponha de recursos livres.

![Falha na alocação fixada](./media/virtual-machines-common-allocation-failure/Allocation2.png)


