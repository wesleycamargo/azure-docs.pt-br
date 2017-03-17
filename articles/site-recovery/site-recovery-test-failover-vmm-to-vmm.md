---
title: Testar o failover (VMM para VMM) no Site Recovery (Azure) | Microsoft Docs
description: "O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre o failover no Azure ou em um datacenter secundário."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 2/15/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: 6b1a5b2879a7b98ec4ad3e8ebbc9e95c0740d89f
ms.openlocfilehash: 3aaa005319b1ce2a10cd913c63b31860d31b797e
ms.lasthandoff: 02/23/2017


---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>Testar o Failover (VMM para VMM) no Site Recovery
> [!div class="op_single_selector"]
> * [Failover de teste para o Azure](./site-recovery-test-failover-to-azure.md)
> * [Failover de teste (VMM para VMM)](./site-recovery-test-failover-vmm-to-vmm.md)


Este artigo fornece informações e instruções sobre como fazer um failover de teste ou uma simulação de recuperação de desastre de máquinas virtuais e servidores físicos protegidos com o Site Recovery usando um site local gerenciado por VMM como o local de recuperação. 

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

O failover de teste é executado para validar sua estratégia de replicação ou executar uma simulação de recuperação de desastre sem nenhuma perda de dados ou tempo de inatividade. Fazer o failover de teste não afeta a replicação em andamento ou em seu ambiente de produção. O failover de teste pode ser feito em uma máquina virtual ou a [plano de recuperação](site-recovery-create-recovery-plans.md). Quando disparar um failover de teste, que você precisa especificar a rede à qual as máquinas virtuais de teste devem se conectar. Depois que um failover de teste for disparado, você poderá acompanhar o progresso na página **trabalhos**.  


## <a name="preparing-infrastructure-for-test-failover"></a>Preparando a infraestrutura para o failover de teste
* Se deseja executar um failover de teste usando uma rede existente, prepare o Active Directory, DHCP e DNS nessa rede.
* Se deseja executar um failover de teste usando a opção para criar redes VM automaticamente, adicione uma etapa manual antes do Grupo-1 no plano de recuperação que usará para o failover de teste e, em seguida, adicione os recursos de infraestrutura à rede criada automaticamente antes de executar o failover de teste.

### <a name="things-to-note"></a>Elementos a serem observados
* Ao replicar em um site secundário, o tipo de rede usado pelo computador de réplica não precisa corresponder ao tipo da rede lógica usado para o failover de teste, mas algumas combinações podem não funcionar. Quando a réplica usa isolamento baseado em VLAN e DHCP, a rede VM da réplica não precisa de um pool de endereços IP estáticos. Desse modo, usar a Virtualização de Rede do Windows para o failover de teste não funcionará porque nenhum pool de endereços está disponível. Além disso, o failover de teste não funcionará se a rede de réplica for Sem Isolamento e a rede de teste for Virtualização de Rede do Windows. Isso porque a rede Sem Isolamento não tem as sub-redes necessárias para criar uma rede da Virtualização de Rede do Windows.
* O modo como as máquinas virtuais de réplica são conectadas às redes VM mapeadas após o failover depende de como a rede VM é configurada no console do VMM:
  * **Rede VM configurada sem isolamento ou com isolamento VLAN**: se DHCP for definido para a rede VM, a máquina virtual de réplica será conectada à ID da VLAN usando as configurações que são especificadas para o site da rede na rede lógica associada. A máquina virtual receberá seu endereço IP do servidor DHCP disponível. Não é necessário um pool de endereços IP estáticos definido para a rede VM de destino. Se um pool de endereços IP estáticos for usado para a rede VM, a máquina virtual de réplica será conectada à ID da VLAN usando as configurações que são especificadas para o site da rede na rede lógica associada. A máquina virtual receberá seu endereço IP do pool definido para a rede VM. Se um pool de endereços IP estáticos não for definido na rede VM de destino, a alocação do endereço IP falhará. O pool de endereços IP deve ser criado em servidores VMM de origem e de destino que você pretende usar para proteção e recuperação.
  * **Rede VM com Virtualização de Rede do Windows**: quando uma rede VM é configurada com essa definição, um pool estático deve ser definido para a rede VM de destino, independentemente de a rede VM de origem estar configurada para usar DHCP ou um pool de endereços IP estáticos. Quando você define DHCP, o servidor VMM de destino atua como um servidor DHCP e fornece um endereço IP do pool que está definido para a rede VM de destino. Quando é definido o uso de um pool de endereços IP estáticos para o servidor de origem, o servidor VMM de destino aloca um endereço IP do pool. Em ambos os casos, a alocação de endereço IP falhará se um pool de endereços IP estáticos não for definido.


### <a name="prepare-dhcp"></a>Preparar o DHCP
Se as máquinas virtuais envolvidas no failover de teste usarem DHCP, um servidor DHCP de teste deverá ser criado na rede isolada que é criada para fins de failover de teste.

### <a name="prepare-active-directory"></a>Preparar o Active Directory
Para executar um failover de teste em um teste de aplicativo, você precisará de uma cópia do ambiente de produção do Active Directory no ambiente de teste. Leia a seção [considerações sobre failover de teste para o Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obter mais detalhes. 

### <a name="prepare-dns"></a>Preparar o DNS
Prepare um servidor DNS para o failover de teste da seguinte forma:

* **DHCP**: se as máquinas virtuais usarem DHCP, o endereço IP do DNS de teste deverá ser atualizado no servidor DHCP de teste. Quando você usa um tipo de rede da Virtualização de Rede do Windows, o servidor VMM atua como o servidor DHCP. Portanto, o endereço IP do DNS deve ser atualizado na rede de failover de teste. Nesse caso, as máquinas virtuais se registrarão no servidor DNS relevante.
* **Endereço estático**– se as máquinas virtuais usarem um endereço IP estático, o endereço IP do servidor DNS de teste deverá ser atualizado na rede de failover de teste. Talvez você precise atualizar o DNS com o endereço IP das máquinas virtuais de teste. É possível usar o seguinte script de exemplo para este fim:

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Execute um teste de failover
Este procedimento descreve como executar um failover de teste para um plano de recuperação. Como alternativa, você pode executar o failover de uma única máquina virtual ou servidor físico na guia **Máquinas Virtuais** .

![Failover de Teste](./media/site-recovery-test-failover-vmm-to-vmm/TestFailover.png)

1. Selecione **Planos de Recuperação** > *recoveryplan_name*. Clique em **Failover** > **Test Failover**.
1. Na folha **Failover de Teste** , especifique como as máquinas virtuais devem ser conectadas às redes após o failover de teste. Examine as [opções de rede](#network-options-in-site-recovery) para obter mais detalhes.
1. Acompanhe o progresso do failover na guia **Trabalhos** . 
1. Depois de concluído, verifique se as máquinas virtuais iniciam com sucesso.
1. Quando terminar, clique em **Failover de teste de limpeza** no plano de recuperação. Em **Observações** , registre e salve todas as observações associadas ao failover de teste. Isso excluirá as máquinas e redes virtuais que foram criadas durante o failover de teste. 


## <a name="network-options-in-site-recovery"></a>Opções de rede na recuperação de site

Na execução de um failover de teste, é solicitado que você selecione as configurações de rede para testar os computadores de réplica. Você tem várias opções.  

| **Opção de failover de teste** | **Descrição** | **Verificação do failover** | **Detalhes** |
| --- | --- | --- | --- |
| **Failover em um site do VMM secundário — sem rede** |Não selecionar uma rede de VM |O failover verifica se os computadores de teste são criados.<br/><br/>A máquina virtual de teste será criada no mesmo host que o host no qual está a máquina virtual de réplica. Ele não será adicionado na nuvem em que a máquina virtual de réplica está localizada. |<p>O computador com failover não será conectado a nenhuma rede.<br/><br/>O computador pode ser conectado a uma rede de VM depois que ela for criada |
| **Failover em um site do VMM secundário — com rede** |Selecionar uma rede VM existente |O failover verifica se as máquinas virtuais são criadas |A máquina virtual de teste será criada no mesmo host que o host no qual está a máquina virtual de réplica. Ele não será adicionado na nuvem em que a máquina virtual de réplica está localizada.<br/><br/>Criar uma rede VM isolada da rede de produção<br/><br/>Se você estiver usando uma rede baseada em VLAN, é recomendável criar uma rede lógica separada (não usada em produção) no VMM para esse fim. Essa rede lógica é usada para criar redes de VM para fins de failover de teste.<br/><br/>A rede lógica deve ser associada a pelo menos um dos adaptadores de rede de todos os servidores do Hyper-V hospedando máquinas virtuais.<br/><br/>Para redes lógicas de VLAN, os sites de rede adicionados à rede lógica devem ser isolados.<br/><br/>Se você está estiver usando uma rede lógica baseada na Virtualização de Rede do Windows, o Azure Site Recovery criará automaticamente redes de VM isoladas. |
| **Failover em um site do VMM secundário — criar uma rede** |Uma rede de teste temporária será criada automaticamente com base na configuração especificada em **Rede Lógica** e seus sites de rede relacionados |O failover verifica se as máquinas virtuais são criadas |Use esta opção se o plano de recuperação usa mais de uma rede VM. Caso esteja usando redes da Virtualização de Rede do Windows, essa opção pode ser usada para criar automaticamente redes VM com as mesmas configurações (sub-redes e pools de endereços IP) na rede da máquina virtual de réplica. Essas redes VM são removidas automaticamente depois que o failover de teste é concluído.</p><p>A máquina virtual de teste será criada no mesmo host que o host no qual está a máquina virtual de réplica. Ele não será adicionado na nuvem em que a máquina virtual de réplica está localizada. |

> [!TIP]
> O endereço IP fornecido a uma máquina virtual durante o failover de teste é o mesmo endereço IP que ela recebia durante um failover planejado ou não planejado (considerando que o endereço IP esteja disponível na rede de failover de teste. Se o mesmo endereço IP não estiver disponível na rede de failover de teste, a máquina virtual receberá outro endereço IP disponível na rede de failover de teste.
>
>


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>Failover de teste para uma rede de produção no site de recuperação 
Recomendamos que, ao fazer um failover de teste, você escolha uma rede diferente da rede do site de recuperação de produção fornecida no **Mapeamento de rede**. Mas se você realmente deseja validar a conectividade de rede ponta a ponta em uma falha na máquina virtual, observe os seguintes pontos:

1. Verifique se máquina virtual primária está desligada quando você estiver realizando o failover de teste. Se você não fizer isso, existirão duas máquinas virtuais com a mesma identidade em execução na mesma rede ao mesmo tempo, e isso pode levar a consequências indesejadas. 
1. As alterações feitas nas máquinas virtuais de failover de teste são perdidas quando elas são limpas. Essas alterações não serão replicadas para a máquina virtual primária.
1. Essa forma de teste resulta em um tempo de inatividade do seu aplicativo de produção. Os usuários do aplicativo devem ser solicitados a não usar o aplicativo quando a análise de DR está em andamento.  


## <a name="next-steps"></a>Próximas etapas
Depois de tentar executar um failover de teste com êxito, você poderá tentar executar um [failover](site-recovery-failover.md).

