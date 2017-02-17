---
title: Proteger o Active Directory e o DNS com o Azure Site Recovery | Microsoft Docs
description: "Este artigo descreve como implementar uma solução de recuperação de desastre para o Active Directory usando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: af1d9b26-1956-46ef-bd05-c545980b72dc
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/9/2017
ms.author: pratshar
translationtype: Human Translation
ms.sourcegitcommit: feb0200fc27227f546da8c98f21d54f45d677c98
ms.openlocfilehash: a583225b4f3acd747a10c1c1fd337bc1b7ac599c


---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Proteger o Active Directory e o DNS com o Azure Site Recovery
Aplicativos empresariais como o SharePoint, o Dynamics AX e o SAP dependem do Active Directory e de uma infraestrutura de DNS para funcionar corretamente. Quando você cria uma solução de recuperação de desastre para aplicativos, é importante se lembrar de que você precisa proteger e recuperar o Active Directory e o DNS antes dos outros componentes de aplicativo, para garantir que tudo funcione corretamente quando ocorrer um desastre.

O Site Recovery é um serviço do Azure que fornece recuperação de desastre por meio da orquestração de replicação, failover e recuperação de máquinas virtuais. O Site Recovery dá suporte a vários cenários de replicação, com o intuito de proteger e executar failover de forma consistente de máquinas virtuais e aplicativos em nuvens privadas, públicas ou de hoster.

Com a Recuperação de Site, você pode criar um plano totalmente automatizado de recuperação de desastre para o Active Directory. Quando ocorrem interrupções, você pode iniciar um failover em poucos segundos de qualquer lugar para que o Active Directory esteja em funcionamento em alguns minutos. Se você implantou o Active Directory para vários aplicativos, como o SharePoint e o SAP, em seu site primário e deseja executar failover de todo o site, é possível executar o failover do Active Directory primeiro usando a Recuperação de Site e depois executar o failover dos outros aplicativos que usam planos de recuperação específicos do aplicativo.

Este artigo explica como criar uma solução de recuperação de desastre para o Active Directory, como executar failovers de teste planejados e não planejados usando um plano de recuperação de um clique, as configurações com suporte e os pré-requisitos.  Antes de começar, você deve estar familiarizado com o Active Directory e o Azure Site Recovery.

## <a name="replicating-domain-controller"></a>Replicando o controlador de domínio

Você precisará configurar a [replicação do Site Recovery](#enable-protection-using-site-recovery) em pelo menos uma máquina virtual que hospede o controlador de domínio e o DNS. Se você tiver [vários controladores de domínio](#environment-with-multiple-domain-controllers) em seu ambiente, além de replicar a máquina virtual do controlador de domínio com o Site Recovery, também precisará configurar um [controlador de domínio adicional](#protect-active-directory-with-active-directory-replication) no site de destino (datacenter local secundário ou do Azure). 

### <a name="single-domain-controller-environment"></a>Ambiente de controlador de domínio único
Se você tiver uma pequena quantidade de aplicativos e um único controlador de domínio e desejar executar failover de todo o site, recomendamos usar o Site Recovery para replicar o controlador de domínio para o site secundário (esteja você executando failover para o Azure ou para site secundário). A mesma máquina virtual controladora/DNS replicada também pode ser usada para o [failover de teste](#test-failover-considerations).

### <a name="environment-with-multiple-domain-controllers"></a>Ambiente com vários controladores de domínio
Se você tiver um grande número de aplicativos e houver mais de um controlador de domínio no ambiente ou se você planejar executar failover de alguns aplicativos por vez, será recomendável que, além de replicar a máquina virtual do controlador de domínio com o Site Recovery, você também configure um [controlador de domínio adicional](#protect-active-directory-with-active-directory-replication) no site de destino (Azure ou um datacenter local secundário). Neste cenário, você usará o controlador de domínio replicado pela recuperação de site para [failover de teste](#test-failover-considerations) e o controlador de domínio adicional no site de destino durante um failover. 


As seções a seguir explicam como habilitar a proteção para um controlador de domínio na Recuperação de Site e como configurar um controlador de domínio no Azure.

## <a name="prerequisites"></a>Pré-requisitos
* Uma implantação local do servidor DNS e do Active Directory.
* Um cofre dos Serviços do Azure Site Recovery em uma assinatura do Microsoft Azure.
* Se estiver replicando para o Azure, execute a ferramenta de Avaliação de Prontidão de Máquina Virtual do Azure nas VMs para garantir que sejam compatíveis com as VMs do Azure e com os Serviços do Azure Site Recovery.

## <a name="enable-protection-using-site-recovery"></a>Habilitar a proteção com a Recuperação de Site
### <a name="protect-the-virtual-machine"></a>Proteger a máquina virtual
Habilite a proteção da máquina virtual do DNS/controlador de domínio na Site Recovery. Defina configurações da Recuperação de Site de acordo com o tipo de máquina virtual (Hyper-V ou VMware). É recomendável uma frequência de replicação consistente de falha de 15 minutos.

### <a name="configure-virtual-machine-network-settings"></a>Definir configurações de rede da máquina virtual
Para a máquina virtual do controlador de domínio/DNS, defina as configurações de rede da Recuperação de Site de modo que a VM seja anexada à rede correta após um failover. Por exemplo, se você estiver replicando VMs do Hyper-V no Azure, é possível selecionar a VM na nuvem do VMM ou no grupo de proteção para definir as configurações de rede, conforme mostrado abaixo

![Configurações de rede da VM](./media/site-recovery-active-directory/DNS-Target-IP.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Proteger o Active Directory com a replicação do Active Directory
### <a name="site-to-site-protection"></a>Proteção site a site
Crie um controlador de domínio no site secundário e especifique o nome do mesmo domínio que está sendo usado no site primário quando promover o servidor para uma função de controlador de domínio. Você pode usar o snap-in dos **Sites e Serviços do Active Directory** para definir as configurações no objeto de link de site ao qual os sites serão adicionados. Ao definir as configurações em um link de site, você pode controlar quando a replicação ocorre entre dois ou mais sites e com que frequência. Veja [Agendando a replicação entre sites](https://technet.microsoft.com/library/cc731862.aspx) para obter mais detalhes.

### <a name="site-to-azure-protection"></a>Proteção Site ao Azure
Siga estas instruções para [criar um controlador de domínio em uma rede virtual do Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Ao promover o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio usado no site primário.

Depois disso, [reconfigure o servidor DNS para a rede virtual](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)para usar o servidor DNS no Azure.

![Rede do Azure](./media/site-recovery-active-directory/azure-network.png)

**DNS na Rede de produção do Azure**

## <a name="test-failover-considerations"></a>considerações sobre failover de teste
O failover de teste ocorre em uma rede isolada da rede de produção para que não ocorra impactos nas cargas de trabalho de produção.

A maioria dos aplicativos também exige a presença de um controlador de domínio e um servidor DNS para funcionar; portanto, antes do failover do aplicativo, um controlador de domínio precisa ser criado na rede isolada a ser usada para failover de teste. A maneira mais fácil de fazer isso é habilitar a proteção na máquina virtual do DNS/controlador de domínio com o Site Recovery e executar um failover de teste dessa máquina virtual, antes de executar um failover de teste do plano de recuperação para o aplicativo. Veja como fazer isso:

1. Habilite a proteção na Recuperação de Site da máquina virtual do DNS/controlador de domínio.
1. Crie uma rede isolada. Qualquer rede virtual criada no Azure é isolada por padrão de outras redes. Recomendamos que o intervalo de endereços IP dessa rede seja o mesmo de sua rede de produção. Não habilite a conectividade site a site nessa rede.
1. Forneça um endereço IP de DNS na rede criada, como o endereço IP que você espera que a máquina virtual DNS obtenha. Se estiver replicando para o Azure, forneça o endereço IP da VM que será usada no failover na configuração **IP de Destino** em **Computação e Rede**. 

    ![IP de destino](./media/site-recovery-active-directory/DNS-Target-IP.png)
    **IP de destino**

    ![Rede de teste do Azure](./media/site-recovery-active-directory/azure-test-network.png)

    **DNS na Rede de teste do Azure**

1. Se você estiver replicando para outro site local e estiver usando o DHCP, siga as instruções para [configurar o DNS e o DHCP para failover de teste](site-recovery-test-failover-vmm-to-vmm.md#prepare-dhcp)
1. Execute um failover de teste na máquina virtual do controlador de domínio da rede isolada. Use o ponto de recuperação **consistente com o aplicativo** mais recente disponível da máquina virtual do controlador de domínio para fazer o failover de teste. 
1. Execute um failover de teste para o plano de recuperação do aplicativo.
1. Após a conclusão do teste, marque o trabalho de failover de teste da máquina virtual do controlador de domínio e do plano de recuperação como "Concluído" na guia **Trabalhos** do portal do Site Recovery.


> [!TIP]
> O endereço IP alocado a uma máquina virtual durante um failover de teste é o mesmo endereço IP que ela obteria durante um failover planejado ou não planejado se o endereço IP estivesse disponível na rede de failover de teste. Caso contrário, a máquina virtual receberá um endereço IP diferente disponível na rede de failover de teste.
> 
> 


### <a name="removing-reference-to-other-domain-controllers"></a>Remover a referência a outros controladores de domínio
Ao fazer um failover de teste, não coloque todos os controladores de domínio na rede de teste. Para remover a referência a outros controladores de domínio no ambiente de produção, será necessário [capturar as funções FSMO do Active Directory e fazer a limpeza dos metadados](http://aka.ms/ad_seize_fsmo) para controladores de domínio ausentes. 

### <a name="troubleshooting-domain-controller-issues-during-test-failover"></a>Solucionando problemas do controlador de domínio durante o failover de teste


Em um prompt de comando, execute o seguinte comando para verificar se as pastas SYSVOL e NETLOGON estão compartilhadas:

    NET SHARE

No prompt de comando, execute o seguinte comando para verificar se o controlador de domínio está funcionando corretamente.

    dcdiag /v > dcdiag.txt

No log de saída, procure o texto a seguir para confirmar se o controlador de domínio está funcionando bem. 

* "passou no teste Connectivity"
* "passou no teste Advertising"
* "passou no teste MachineAccount"

Se as condições acima forem atendidas, o controlador de domínio provavelmente funcionará bem. Se não for o caso, experimente as etapas a seguir.


* Faça uma restauração autoritativa do controlador de domínio.
    * Embora [não seja recomendável usar a replicação FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), se você a estiver usando, siga as etapas fornecidas [aqui](https://support.microsoft.com/en-in/kb/290762) para fazer uma restauração autoritativa. Você pode ler mais sobre os Burflags mencionados no link anterior [aqui](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Se você estiver usando replicação DFSR, siga as etapas disponíveis [aqui](https://support.microsoft.com/en-us/kb/2218556) para fazer uma restauração autoritativa. Você também pode usar funções do Powershell disponíveis neste [link](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/) para essa finalidade. 
    
* Ignore o requisito de sincronização inicial definindo a seguinte chave de registro como 0. Se esse DWORD não existir, você poderá criá-lo no nó 'Parameters'. Saiba mais sobre isso [aqui](https://support.microsoft.com/en-us/kb/2001093)

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations

* Desabilite o requisito de ter um servidor de catálogo global disponível para validar o logon do usuário definindo a chave de registro a seguir como 1. Se esse DWORD não existir, você poderá criá-lo no nó 'Lsa'. Saiba mais sobre isso [aqui](http://support.microsoft.com/kb/241789/EN-US)

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures



### <a name="dns-and-domain-controller-on-different-machines"></a>DNS e controlador de domínio em computadores diferentes
Caso o DNS não esteja na mesma máquina virtual que o controlador de domínio, será necessário criar uma VM do DNS para o failover de teste. Caso eles estejam na mesma VM, você pode ignorar esta seção.

Você pode usar um servidor DNS atualizado e criar todas as zonas necessárias. Por exemplo, se o domínio do Active Directory for contoso.com, é possível criar uma zona DNS com o nome contoso.com. As entradas que correspondem ao Active Directory devem ser atualizadas no DNS, da seguinte maneira:

1. Assegure-se de que essas configurações estão definidas antes que outra máquina virtual apareça no plano de recuperação:
   
   * A zona deve ser nomeada depois do nome raiz da floresta.
   * A zona deve ter backup em arquivo.
   * A zona deve ser habilitada para atualizações seguras e não seguras.
   * O resolvedor da máquina virtual do controlador de domínio deve apontar para o endereço IP da máquina virtual do DNS.
2. Execute o seguinte comando na máquina virtual do controlador de domínio:
   
    `nltest /dsregdns`
3. Adicionar uma zona no servidor DNS, permitir atualizações não seguras e adicionar uma entrada para ela no DNS:
   
        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1

## <a name="next-steps"></a>Próximas etapas
Leia [Quais cargas de trabalho posso proteger?](site-recovery-workload.md) para saber mais sobre como proteger as cargas de trabalho corporativas com o Azure Site Recovery.




<!--HONumber=Jan17_HO4-->


