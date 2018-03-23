---
title: Disponibilidade do SAP HANA em regiões do Azure | Microsoft Docs
description: Descreve a visão geral das considerações de disponibilidade ao executar o SAP HANA em VMs do Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 052394884f85d527caa88ff6c9464af669f47bb5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-across-azure-regions"></a>Disponibilidade do SAP HANA em regiões do Azure
Neste artigo, são descritos e discutidos os cenários relacionados à disponibilidade do SAP HANA em diferentes regiões do Azure. Devido ao fato de que regiões do Azure separadas têm maior distância entre elas, há considerações especiais que são listadas neste artigo.

## <a name="motivation-to-deploy-across-multiple-azure-regions"></a>Motivação para implantar em várias regiões do Azure
Diferentes regiões do Azure são separadas por uma distância maior. Dependendo da região geopolítica, isso pode significar centenas de milhas ou mesmo milhares de milhas, como nos Estados Unidos. Devido à distância entre as diferentes Regiões do Azure, o tráfego entre os recursos implantados em duas regiões do Azure diferentes experimenta uma latência na viagem de ida e volta de rede significativa. Significativa o suficiente para excluir troca de dados síncrona entre duas instâncias do SAP HANA sob a carga de trabalho típica do SAP. Por outro lado, frequentemente nos deparamos com o fato de que há um requisito definido na distância entre o datacenter primário e um datacenter secundário para fornecer disponibilidade em caso de desastre natural atingindo uma área mais ampla. Como, por exemplo, os furacões que atingiram o Caribe e a área da Flórida em setembro e outubro de 2017. Ou, pelo menos um requisito de distância mínima. Na maioria dos casos de cliente, essa definição de distância mínima requer a criação de disponibilidade em [Regiões do Azure](https://azure.microsoft.com/regions/). Quando a distância for muito grande entre duas regiões do Azure para usar o modo de replicação síncrona do HANA, os requisitos de RTO e RPO poderão forçá-lo a implantar configurações de disponibilidade dentro de uma região e, em seguida, complementar com implantações adicionais em uma segunda região.

Outro aspecto a ser considerado nessas configurações é o failover e o redirecionamento do cliente. A suposição é que um failover entre instâncias do SAP HANA em duas regiões do Azure diferentes é sempre um failover manual. Uma vez que o modo de Replicação de Sistema do SAP HANA é configurado como assíncrono, há um potencial de que os dados confirmados na instância do HANA primário ainda não tenham feito a instância do HANA secundário. Portanto, o failover automático não poderá ser aceito para configurações onde a replicação for assíncrona. Mesmo com failover controlado manualmente, como em um exercício de failover, será necessário tomar medidas para certificar-se de que todos os dados confirmados no lado primário chegaram à instância secundária antes de movimentar manualmente para a outra região do Azure.
 
Uma vez que operam com um intervalo de endereços IP diferente nas VNets do Azure, os quais são implantados na segunda região do Azure, os clientes do SAP HANA precisam ser alterados na configuração ou considerar implantar as etapas no local para alterar a resolução do nome. Portanto, que os clientes estejam sendo redirecionados para o novo endereço IP do servidor secundário. O artigo do SAP sobre a [Recuperação da conexão do cliente após tomada de controle](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html) envolve mais detalhes.   

## <a name="simple-availability-between-two-azure-regions"></a>Disponibilidade simples entre duas regiões do Azure
Nesse cenário, você decidiu não implantar configurações de disponibilidade no local dentro de uma única região. Mas, há a demanda para ter a carga de trabalho atendida em caso de desastre. Casos típicos para sistemas como esse são sistemas que não são de produção. Embora seja possível sustentar para ter o sistema inoperante por meio dia ou mesmo um dia, não é possível permitir que o sistema fique indisponível por 48 horas ou mais. Para tornar a instalação menos dispendiosa, é possível executar outro sistema que seja ainda menos importante na VM que funciona como um destino, ou dimensionar a VM na região secundária menor e optar por não pré-carregar os dados. Uma vez que o failover é manual e implica muitas outras etapas para failover na pilha de aplicativo completa, o tempo adicional para encerrar a VM, redimensioná-la e iniciar a VM novamente, é aceitável.

> [!NOTE]
> Mesmo sem pré-carregamento de dados no destino de Replicação de Sistema do HANA, será necessário pelo menos 64 GB de memória, além de memória suficiente para manter os dados rowstore em memória da instância de destino.

![Duas VMs em duas regiões](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> Nessa configuração, não é possível fornecer um RPO=0, pois o modo de replicação de sistema do HANA é assíncrono. Se for necessário fornecer um RPO=0, essa configuração não será a configuração preferencial.

Uma pequena alteração na configuração pode configurar o pré-carregamento de dados. No entanto, dada a natureza manual do failover e o fato de que as camadas de aplicativo também precisam se mover para a segunda região, não é adequado pré-carregar dados. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Combinar disponibilidade dentro de uma região e entre regiões 
Uma combinação de disponibilidade dentro e entre regiões pode ser conduzida por:

- Requisito para RPO=0 dentro de uma Região do Azure.
- Não querer ou ser capaz de ter operações globais da empresa sendo afetadas por uma grande catástrofe natural que afeta uma região maior. Como foi o caso de alguns furacões que atingiram o Caribe nos últimos anos.
- Regulamentos que exigem distâncias entre sites primários e secundários que estão claramente além do que as áreas de Disponibilidade do Azure podem fornecer.

 
Nesses casos, você irá configurar que o SAP chame uma [configuração de Replicação de Sistema Multicamadas do SAP HANA ](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) com Replicação do Sistema do HANA. A arquitetura seria semelhante a:

![três VMs em duas regiões](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Essa configuração fornece um RPO=0 com pequenos tempos do RTO dentro da região primária e, adicionalmente, fornece RPO decrescente no caso de uma movimentação para a segunda região. Os tempos do RTO na segunda região dependem se configurar ou não pré-carregamento de dados. Em muitos casos de cliente, a VM na região secundária é utilizada para executar um sistema de teste. Como resultado dessa utilização, os dados não podem ser pré-carregados.

> [!NOTE]
> Como você está utilizando o modo de operação logreplay para Replicação de Sistema do SAP HANA indo do Nível 1 ao Nível 2 (replicação síncrona na região primária), a replicação entre o Nível 2 e Nível 3 (replicação para site secundário) não pode estar no modo de operação delta_datashipping. Os detalhes dos modos de operação e algumas restrições são documentados pela SAP em [Modos de operação para Replicação de Sistema do SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Próximas etapas
Se você precisar de diretrizes passo a passo sobre como definir essa configuração no Azure, leia os artigos:

- [Configurar a replicação de sistema do SAP HANA em VMs do Azure](sap-hana-high-availability.md)
- [SAP no Azure – Parte 4 – Alta disponibilidade para SAP HANA usando Replicação de Sistema](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
