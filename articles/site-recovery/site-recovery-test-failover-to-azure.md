---
title: Failover de teste para o Azure no Site Recovery | Microsoft Docs
description: "Aprenda sobre a execução de um failover de teste do local para o Azure"
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
ms.sourcegitcommit: 753d70b9b9a4c2805033f2f2fb293dbdef7b4db4
ms.openlocfilehash: 27ee6bbadd2b03ccf60739a276f49783ea68f9d1
ms.lasthandoff: 02/15/2017


---
# <a name="test-----failover-to-azure-in-site-recovery"></a>Failover de teste para o Azure no Site Recovery

Este artigo fornece informações e instruções sobre como fazer um failover de teste ou uma simulação de recuperação de desastre de máquinas virtuais e servidores físicos protegidos com o Site Recovery usando o Azure como o local de recuperação.

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

O failover de teste é executado para validar sua estratégia de replicação ou executar uma simulação de recuperação de desastre sem nenhuma perda de dados ou tempo de inatividade. Fazer o failover de teste não afeta a replicação em andamento ou em seu ambiente de produção. O failover de teste pode ser feito em uma máquina virtual ou a [plano de recuperação](site-recovery-create-recovery-plans.md). Quando disparar um failover de teste, que você precisa especificar a rede à qual as máquinas virtuais de teste devem se conectar. Depois que um failover de teste for disparado, você poderá acompanhar o progresso na página **trabalhos**.  


## <a name="supported-scenarios"></a>Cenários com suporte
Failover de teste é suportado em todos os cenários de implantação, além de [site herdado do VMware no Azure](site-recovery-vmware-to-azure-classic-legacy.md). O failover de teste também não terá suporte quando a máquina virtual tiver feito failover no Azure.  


## <a name="run-a-test-failover"></a>Execute um teste de failover
Este procedimento descreve como executar um failover de teste para um plano de recuperação. Como alternativa, você também pode executar o failover de teste para um único computador usando a opção apropriada nele.

1. Selecione **Planos de Recuperação** > *recoveryplan_name*. Clique em **Failover de Teste**.

    ![Failover de Teste](./media/site-recovery-test-failover-to-azure/test-failover1.png)
1. Selecione um **ponto de recuperação** para failover. Você pode usar uma das seguintes opções:
    1.  **Mais recentes processado**: essa opção faz failover em todas as máquinas virtuais do plano de recuperação para o último ponto de recuperação que já foi processado pelo serviço de recuperação de site. Quando você estiver realizando o failover de teste de uma máquina virtual, o carimbo de data/hora do último ponto de recuperação processado também é mostrado. Se você estiver fazendo o failover de um plano de recuperação, poderá ir para a máquina virtual individual e examinar o bloco **Últimos Pontos de Recuperação** para obter essas informações. Como nenhum tempo é gasto para processar os dados não processados, esta opção fornece uma opção de failover de baixo RTO (objetivo de tempo de recuperação).
    1.    **Consistente de aplicativo mais recente**: essa opção falhar em todas as máquinas virtuais do plano de recuperação para o último ponto de recuperação consistente de aplicativo que já foi processado pelo serviço de recuperação de site. Quando você estiver realizando failover de teste de uma máquina virtual, o carimbo de data/hora do ponto mais recente recuperação consistentes com o aplicativo também é mostrado. Se você estiver fazendo o failover de um plano de recuperação, poderá ir para a máquina virtual individual e examinar o bloco **Últimos Pontos de Recuperação** para obter essas informações.
    1.    **Mais recente**: essa opção primeiro processa todos os dados que foram enviados ao serviço de recuperação de site para criar um ponto de recuperação para cada máquina virtual antes de failover para ele. Essa opção oferece o menor RPO (objetivo de ponto de recuperação) da máquina virtual criada após failover terá todos os dados que tenha sido replicada para o serviço de recuperação de site quando o failover foi disparado.
    1.    **Personalizado**: se você estiver realizando teste de failover de uma máquina virtual, você pode usar essa opção de failover para um determinado ponto de recuperação.
1. Selecione uma **rede virtual do Azure**: forneça uma rede virtual do Azure, onde as máquinas virtuais de teste seriam criadas. O Site Recovery tenta criar máquinas virtuais de teste em uma sub-rede de mesmo nome e usando o mesmo IP fornecido nas configurações de **Computação e Rede** da máquina virtual. Se a sub-rede de mesmo nome não estiver disponível na rede virtual do Azure fornecida para failover de teste, então a máquina virtual de teste será criada na primeira sub-rede em ordem alfabética. Se o mesmo IP não estiver disponível na sub-rede, a máquina virtual receberá outro endereço IP disponível na sub-rede. Leia esta seção para obter [mais detalhes](#creating-a-network-for-test-failover)
1. Se estiver fazendo failover no Azure e a criptografia de dados estiver habilitada, em **Chave de Criptografia**, selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a instalação do provedor. Você pode ignorar esta etapa se você não ativou a criptografia na máquina virtual.
1. Acompanhe o progresso do failover na guia **Trabalhos** . Você deve poder ver o computador de réplica de teste no portal do Azure.
1. Para iniciar uma conexão de RDP na máquina virtual será necessário [adicionar um ip público](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) na interface de rede de máquina virtual. Se o failover para uma máquina virtual do clássico, você precisará [adicionar um ponto de extremidade](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md) na porta 3389
1. Quando terminar, clique em **Failover de teste de limpeza** no plano de recuperação. Em **Observações** , registre e salve todas as observações associadas ao failover de teste. Isso excluirá as máquinas virtuais que foram criadas durante o failover de teste.


> [!TIP]
> O Site Recovery tenta criar máquinas virtuais de teste em uma sub-rede de mesmo nome e usando o mesmo IP fornecido nas configurações de **Computação e Rede** da máquina virtual. Se a sub-rede de mesmo nome não estiver disponível na rede virtual do Azure fornecida para failover de teste, então a máquina virtual de teste será criada na primeira sub-rede em ordem alfabética. Se o mesmo IP não estiver disponível na sub-rede, a máquina virtual receberá outro endereço IP disponível na sub-rede. 
>
>


## <a name="creating-a-network-for-test-failover"></a>Criar uma rede para failover de teste
É recomendável que, quando você estiver realizando um failover de teste você escolher uma rede isolada da rede de site de recuperação de produção que você forneceu na **de computação e rede** configurações para a máquina virtual. Por padrão quando você cria uma rede virtual do Azure, é isolado de outras redes. Essa rede deve imitar a sua rede de produção:

1. Rede de teste deve ter o mesmo número de sub-redes que em sua rede de produção e com o mesmo nome que aquelas de subents em sua rede de produção.
1. Rede de teste deve usar o mesmo intervalo IP da rede de produção.
1. Atualizar o DNS da rede de teste como o IP que você atribuiu como IP de destino para a máquina virtual DNS em **de computação e rede** configurações. Leia a seção [considerações sobre failover de teste para o Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obter mais detalhes.


## <a name="test-failover-to-a-production-network-on-recovery-site"></a>Failover de teste para uma rede de produção no site de recuperação
É recomendável que, quando você estiver realizando um failover de teste você escolher uma rede diferente da sua rede de site de recuperação de produção que você forneceu no **de computação e rede** configurações para a máquina virtual. Mas se você realmente deseja validar a conectividade de rede ponta a ponta em uma falha na máquina virtual, observe os seguintes pontos:

1. Verifique se máquina virtual primária está desligada quando você estiver realizando o failover de teste. Se você não fizer isso, existirão duas máquinas virtuais com a mesma identidade em execução na mesma rede ao mesmo tempo, e isso pode levar a consequências indesejadas.
1. As alterações feitas nas máquinas virtuais de failover de teste são perdidas quando elas são limpas. Essas alterações não serão replicadas para a máquina virtual primária.
1. Essa forma de teste resulta em um tempo de inatividade do seu aplicativo de produção. Os usuários do aplicativo devem ser solicitados a para não usar o aplicativo quando a análise de DR está em andamento.  



## <a name="prepare-active-directory-and-dns"></a>Preparar o Active Directory e o DNS
Para executar um failover de teste em um teste de aplicativo, você precisará de uma cópia do ambiente de produção do Active Directory no ambiente de teste. Leia a seção [considerações sobre failover de teste para o Active Directory](site-recovery-active-directory.md#test-failover-considerations) para obter mais detalhes.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Se você quiser se conectar às VMs do Azure usando o RDP após o failover, realize as ações resumidas na tabela.

**Failover** | **Localidade** | **Ações**
--- | --- | ---
**VM do Azure executando o Windows** | No computador local antes do failover | Para acessar a VM do Azure pela Internet, habilite o RDP, certifique-se de que as regras de TCP e UDP sejam adicionadas para o **Público** e verifique se o RDP foi permitido no **Firewall do Windows** > **Aplicativos permitidos** para todos os perfis.<br/><br/> Para acesso por meio de uma conexão site a site, habilite o RDP no computador e verifique se o RDP foi permitido no **Firewall do Windows** -> **Aplicativos e recursos permitidos** para as redes **Domínio e Particular**.<br/><br/> Instalar o Agente de VM do Azure<br/><br/> Certifique-se de que a política de SAN do sistema operacional esteja definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Desligue o serviço IPsec antes de executar o failover.
**VM do Azure executando o Windows** | Na VM do Azure após o failover | Adicione um ponto de extremidade público para o protocolo RDP (porta 3389) e especifique as credenciais de logon.<br/><br/> Verifique se não há nenhuma política de domínio que o impeça de se conectar a uma máquina virtual usando um endereço público.<br/><br/> Tente se conectar. Se você não conseguir se conectar, verifique se a VM está em execução. Obtenha [dicas de solução de problemas](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).<br/><br/> Certifique-se de que a política de SAN do sistema operacional esteja definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Desligue o serviço IPsec antes de executar o failover.<br/><br/> Se você tiver um grupo de segurança de rede associado ao computador ou à sub-rede, verifique se o grupo tem uma regra de saída para permitir HTTP/HTTPS. Certifique-se de que o DNS da rede para o qual a VM faz failover está configurado corretamente. Caso contrário, o failover poderá atingir o tempo limite com o erro -“PreFailoverWorkflow task WaitForScriptExecutionTask atingiu o tempo limite”. [Saiba mais](https://github.com/rayne-wiselman/azure-docs-pr/blob/75653b84d6ccbefe7d5230449bea81f498e10a98/articles/site-recovery/site-recovery-monitoring-and-troubleshooting.md#recovery).
**VM do Azure executando Linux** | No computador local antes do failover | Verifique se o serviço Secure Shell na VM do Azure está definido para iniciar automaticamente na inicialização do sistema.<br/><br/> Verifique se as regras de firewall permitem uma conexão SSH com ele.
**VM do Azure executando Linux** | VM do Azure após o failover | As regras de grupo de segurança de rede na VM com failover e a sub-rede do Azure à qual ela está conectada precisam permitir conexões de entrada para a porta SSH.<br/><br/> Um ponto de extremidade público deve ser criado, para permitir conexões de entrada na porta SSH (porta TCP 22, por padrão).<br/><br/> Se a VM for acessada por meio de uma conexão VPN (ExpressRoute ou VPN site a site), então o cliente poderá ser usado para se conectar diretamente à VM via SSH.<br/><br/> Se você tiver um grupo de segurança de rede associado ao computador ou à sub-rede, verifique se o grupo tem uma regra de saída para permitir HTTP/HTTPS. Certifique-se de que o DNS da rede para o qual a VM faz failover está configurado corretamente. Caso contrário, o failover poderá atingir o tempo limite com o erro -“PreFailoverWorkflow task WaitForScriptExecutionTask atingiu o tempo limite”. [Saiba mais](https://github.com/rayne-wiselman/azure-docs-pr/blob/75653b84d6ccbefe7d5230449bea81f498e10a98/articles/site-recovery/site-recovery-monitoring-and-troubleshooting.md#recovery).



## <a name="next-steps"></a>Próximas etapas
Depois de tentar executar um failover de teste com êxito, você poderá tentar executar um [failover](site-recovery-failover.md).

