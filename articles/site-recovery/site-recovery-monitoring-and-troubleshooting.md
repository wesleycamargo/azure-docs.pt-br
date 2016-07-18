<properties
	pageTitle="Monitorar e solucionar problemas de proteção para máquinas virtuais e sites físicos | Microsoft Azure" 
	description="O Azure Site Recovery coordena a replicação, o failover e a recuperação de máquinas virtuais localizadas em servidores locais no Azure ou em um armazenamento de dados secundário. Use este artigo para monitorar e solucionar problemas de proteção de sites do VMM ou do Hyper-V." 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="07/06/2016" 
	ms.author="anbacker"/>
	
# Monitorar e solucionar problemas de proteção para máquinas virtuais e sites físicos

Este guia de solução de problemas e monitoramento permite que você aprenda sobre o acompanhamento da integridade da replicação e sobre técnicas de solução de problemas do Azure Site Recovery.

## Noções básicas sobre os componentes

### Implantação de site físico/VMware para replicação entre locais e o Azure.
Para configurar a recuperação de desastres entre a máquina física/VMware, o Servidor de configuração, Destino Mestre e o Servidor em Processo precisam configurados. Ao habilitar a proteção para o servidor de origem, o Azure Site Recovery instala o Serviço de Mobilidade. Após a interrupção local, quando o servidor de origem faz failover para o Azure, o cliente precisa configurar um Servidor de Processo no Azure e um servidor de Destino Mestre local para proteger o servidor de origem de volta para o local recriado.

![Implantação do VMware/Site Físico para replicação entre o local e o Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### Implantação de Site do VMM para replicação no site local.

Como parte da configuração de recuperação de desastres entre dois espaços locais, o Provedor do Azure Site Recovery precisa ser baixado e instalado no servidor do VMM. O provedor precisa de conectividade com a internet para garantir que todas as operações acionadas no Portal do Azure sejam traduzidas por operações locais como habilitar a proteção, desligamento das máquinas virtuais do lado primário como parte de failovers etc.

![Implantação de Site do VMM para replicação no site local](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### Implantação de Site do VMM para replicação entre o local e o Azure.

Como parte da configuração de recuperação de desastres entre o local e o Azure, o Provedor do Azure Site Recovery precisa ser baixado e instalado no servidor do VMM junto com o Agente de Serviços de Recuperação do Azure que precisa ser instalado em cada host Hyper-V. Consulte [Noções básicas sobre a proteção do site para o Azure](./site-recovery-understanding-site-to-azure-protection.md) para saber mais.

![Implantação de Site do VMM para replicação entre o local e o Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### Implantação de Site do Hyper-V para replicação entre o local e o Azure.

É igual à Implantação do VMM – a única diferença é que o Provedor e o Agente são instalados no próprio host Hyper-V. Consulte [Noções básicas sobre a proteção do site para o Azure](./site-recovery-understanding-site-to-azure-protection.md) para saber mais.

## Monitorar as operações de configuração, proteção e recuperação

Todas as operações do Azure Site Recovery (ASR) são auditadas e podem ser controladas na guia "TRABALHOS". No caso de qualquer erro de configuração, proteção ou recuperação, acesse a guia TRABALHOS e veja se há falhas.

![Monitorar as operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Caso você encontre falhas na exibição dos TRABALHOS, selecione o TRABALHO e clique em DETALHES DO ERRO desse trabalho.

![Monitorar as operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Os detalhes do erro ajudarão você a identificar a possível causa e a recomendação para o problema.

![Monitorar as operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image5.png)

No caso acima, parece haver outra operação em andamento que está causando a falha na configuração da Proteção. Resolva o problema de acordo com a recomendação e, em seguida, clique em REINICIAR para iniciar novamente a operação.

![Monitorar as operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image6.png)

A opção de REINICIAR não está disponível para todas as operações. Para aquelas que não tenham a opção de reinicialização, retorne ao objeto e repita a operação. Todos os TRABALHOS podem ser cancelados a qualquer momento durante o processamento por meio do botão CANCELAR.

![Monitorar as operações de configuração, proteção e recuperação](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## Monitorar a integridade de replicação da máquina virtual

Monitoramento central e remoto do provedor ASR por meio do portal do Azure para cada uma das entidades protegidas. Navegue até os ITENS PROTEGIDOS e selecione NUVENS DO VMM ou GRUPOS DE PROTEÇÃO. A guia NUVENS DO VMM é usada somente para implantações do VMM e todos os outros cenários têm as entidades protegidas na guia GRUPOS DE PROTEÇÃO.

![Monitorar a integridade de replicação da máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Em seguida, selecione a entidade protegida na nuvem correspondente ou no grupo de proteção. Depois de selecionar a entidade protegida, todas as operações permitidas são mostradas no painel inferior.

![Monitorar a integridade de replicação da máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Como mostrado acima, caso a INTEGRIDADE da máquina virtual seja crítica, você poderá clicar no botão DETALHES DO ERRO na parte inferior para ver o erro. Com base nas "Possíveis causas" e na "Recomendação" mencionadas, resolva o problema.

![Monitorar a integridade de replicação da máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Monitorar a integridade de replicação da máquina virtual](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Observação: se houver quaisquer operações ativas que estejam em andamento ou tenham falhado, navegue até o modo de exibição de TRABALHOS como mencionado anteriormente para exibir o erro específico do TRABALHO.

## Solucionar problemas do Hyper-V no local

Conecte-se ao console do gerenciador local do Hyper-V, selecione a máquina virtual e verifique a integridade da replicação.

![Solucionar problemas do Hyper-V no local](media/site-recovery-monitoring-and-troubleshooting/image12.png)

Nesse caso, a *Integridade da Replicação* está indicada como crítica; clique em *Exibir Integridade da Replicação* para ver os detalhes.

![Solucionar problemas do Hyper-V no local](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Para os casos nos quais a replicação está pausada para a máquina virtual, clique com o botão direito do mouse em Selecionar *Replicação*->*Retomar replicação* ![Solucionar problemas do Hyper-V no local](media/site-recovery-monitoring-and-troubleshooting/image19.png)

No caso em que a máquina virtual migra um novo host Hyper-V (dentro do cluster ou de um computador autônomo), que foi configurado por meio do ASR, a replicação para a máquina virtual não é afetada. Verifique se o novo host Hyper-V atende a todos os pré-requisitos e foi configurado usando o ASR.

### Log de Eventos

| Origens de eventos | Detalhes |
|-------------------------	|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------	|
| **Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin** (Servidor VMM) | Isso fornece registros úteis para a solução de muitos problemas diferentes de VMM. |
| **Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** (Host Hyper-V) | Isso fornece registros úteis para solução de muitos problemas com o Agente de Serviços de Recuperação do Microsoft Azure. <br/> ![Origem do evento para o host Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational** (Host Hyper-V) | Isso fornece registros úteis para solução de muitos problemas com o Microsoft Azure Site Recovery Service. <br/> ![Origem do evento para o host Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** (Host Hyper-V) | Isso fornece registros úteis para solução de muitos problemas de gerenciamento de máquina virtual do Hyper-V. <br/> ![Origem do evento para o host Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |


### Opções de log de replicação do Hyper-V

Todos os eventos relativos ao Hyper-V Replica são registrados no log do Hyper-V-VMMS\\Admin localizado em **Applications and Service Logs\\Microsoft\\Windows**. Além disso, um log analítico pode ser habilitado para o VMMS do Hyper-V. Para habilitar esse log, primeiro exiba os logs analíticos e de depuração no Visualizador de Eventos. Abra o Visualizador de Eventos e, em seguida, no **menu Exibir**, clique em **Mostrar Logs Analíticos e de Depuração**.

![Solucionar problemas do Hyper-V no local](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Um log Analítico pode ser visto no VMMS do Hyper-V

![Solucionar problemas do Hyper-V no local](media/site-recovery-monitoring-and-troubleshooting/image15.png)

Em seguida, no painel **Ações**, clique em **Habilitar Log**. Uma vez habilitado, ele é exibido no **Monitor de Desempenho** como uma Sessão de Rastreamento de Evento localizado em **Conjuntos de Coletores de Dados.**

![Solucionar problemas do Hyper-V no local](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Para exibir as informações coletadas, primeiro interrompa a sessão de rastreamento desabilitando o log; em seguida, salve o log e abra-o novamente no Visualizador de Eventos ou use outras ferramentas para convertê-lo conforme desejado.



## Contatando o Suporte da Microsoft

### Coleta de logs

Para a proteção de do Site do VMM, consulte [Coleta de Logs do ASR usando a Ferramenta de Plataforma de Diagnóstico de Suporte (SDP)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) para coletar os logs necessários.

Para proteção do Site do Hyper-V, baixe a [ferramenta](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) e execute-a no host Hyper-V para coletar os logs.

Para cenários do VMware/físicos, consulte [Coleta de Logs do Azure Site Recovery para proteção de sites do VMware e físicos](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) para coletar os logs necessários.

A ferramenta coleta os logs localmente em uma subpasta nomeada aleatoriamente, em **%LocalAppData%\\ElevatedDiagnostics**

![Etapas de exemplo mostradas da proteção de site do Hyper-V.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### Abrindo um tíquete de suporte

Para gerar um tíquete de suporte para o ASR, acesse o Suporte do Azure usando a URL em <http://aka.ms/getazuresupport>

## Artigos da Base de Dados de Conhecimento

-   [Como preservar a letra da unidade das máquinas virtuais protegidas com failover ou migradas para o Azure](http://support.microsoft.com/kb/3031135)
-   [Como gerenciar localmente o uso de largura de banda de rede para proteção do Azure](https://support.microsoft.com/kb/3056159)
-   [ASR: erro "O recurso de cluster não pôde ser encontrado" quando você tenta habilitar a proteção para uma máquina virtual](http://support.microsoft.com/kb/3010979)
-   [Guia para compreender e solucionar problemas da Réplica do Hyper-V](http://www.microsoft.com/en-in/download/details.aspx?id=29016)

## Erros de ASR comuns e suas resoluções

Veja abaixo os erros comuns que você pode encontrar e suas resoluções. Cada erro é documentado em uma página WIKI separada.

### Geral
-   <span style="color:green;">NOVO</span> [Trabalhos que falham com um erro "Operação em andamento". Erro 505, 514, 532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">NOVO</span> [Trabalhos que falham com o erro "O servidor não está conectado à Internet". Erro 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### Configuração
-   [Não é possível registrar o servidor VMM devido a um erro interno. Consulte o modo de exibição de trabalhos no Portal de Recuperação de Site para obter mais detalhes sobre o erro. Execute a Instalação novamente para registrar o servidor.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [Não é possível estabelecer uma conexão com o cofre do Gerenciador de Recuperação do Hyper-V. Verifique as configurações de proxy ou tente novamente mais tarde.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### Configuração
-   [Não é possível criar o Grupo de Proteção: ocorreu um erro ao recuperar a lista de servidores.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [O cluster do host Hyper-V contém pelo menos um adaptador de rede estático ou nenhum adaptador conectado está configurado para usar DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [O VMM não tem permissões para completar uma ação](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [Não é possível selecionar a conta de armazenamento na assinatura durante a configuração da proteção](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### Proteção
- <span style="color:green;">NOVO</span> [Falha em Habilitar Proteção com o erro "Não foi possível configurar a proteção para a máquina virtual". Erro 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">NOVO</span> [Falha em Habilitar Proteção com o erro "Não foi possível habilitar a proteção para a máquina virtual". Erro 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">NOVO</span> [Erro 23848 de migração dinâmica — a máquina virtual será movida usando o tipo Live. Isso pode interromper o status da proteção de recuperação da máquina virtual.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx)
- [Falha na habilitação da proteção, pois o Agente não está instalado no computador host](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [Não é possível encontrar um host adequado para a máquina virtual de réplica, pois há poucos recursos de computação](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [Não é possível encontrar um host adequado para a máquina virtual de réplica, pois não há uma rede lógica anexada](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [Não é possível conectar-se ao computador host de réplica, pois não foi possível estabelecer conexão](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### Recuperação
- A VMM não conseguiu concluir a operação do host -
    -   [Failover para o ponto de recuperação selecionado para a máquina virtual: erro geral de acesso negado.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [O Hyper-V não conseguiu fazer failover para o ponto de recuperação selecionado para a máquina virtual: operação anulada. Tente um ponto de recuperação mais recente. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   Não foi possível estabelecer uma conexão com o servidor (0x00002EFD)
        -   [O Hyper-V não conseguiu habilitar a replicação inversa para a máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [O Hyper-V não conseguiu habilitar a replicação para a máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [Não foi possível confirmar o failover para a máquina virtual](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [O plano de recuperação contém máquinas virtuais que não estão prontas para o failover planejado](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [A máquina virtual não está pronta para o failover planejado](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [A máquina virtual não está em execução e não está desligada](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [Ocorreu uma operação fora de banda em uma máquina virtual e não foi possível confirmar o failover](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   Failover de Teste
    -   [Não foi possível iniciar o failover, pois o failover de teste está em andamento](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
-   <span style="color:green;">NOVO</span> O failover atinge o tempo limite com 'WaitForScriptExecutionTask da tarefa PreFailoverWorkflow atingiu o tempo limite' devido às definições de configuração do Grupo de Segurança de Rede associado à Máquina Virtual ou a sub-rede à qual o computador pertence. Veja ['WaitForScriptExecutionTask da tarefa PreFailoverWorkflow atingiu o tempo limite'](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) para obter detalhes.


### Servidor de Configuração, Servidor de Processo, Destino Mestre
Servidor de Configuração (CS), Servidor de Processo (PS), Destino Mestre (MT)
-   [O host ESXi no qual o PS/CS está hospedado como uma VM falha com uma tela roxa.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### Solução de problemas da área de trabalho remota após failover
-   Muitos clientes têm enfrentado problemas para conexão com a VM com failover no Azure. [Use o documento de solução de problemas para fazer o RDP na VM](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

<!---HONumber=AcomDC_0706_2016-->