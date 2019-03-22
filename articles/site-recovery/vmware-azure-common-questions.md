---
title: Perguntas comuns - recuperação de desastre do VMware para Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo resume as perguntas comuns ao configurar a recuperação de desastre de VMs do VMware locais no Azure usando o Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.date: 03/21/2019
ms.topic: conceptual
ms.author: raynew
ms.openlocfilehash: 82ae36eaaf4616dbd85760a0962f301a2b1a20f5
ms.sourcegitcommit: 5e4ca656baf3c7d370ab3c0fbad0278aa2c9f1e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58319373"
---
# <a name="common-questions---vmware-to-azure-replication"></a>Perguntas comuns - replicação do VMware para Azure

Este artigo fornece respostas para as perguntas comuns que surgem ao implantar a recuperação de desastre de VMs do VMware locais no Azure. Se você tiver dúvidas após a leitura deste artigo, publique-as no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Geral
### <a name="how-is-site-recovery-priced"></a>Como é o Site Recovery é precificado?
Analise os detalhes em [Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

### <a name="how-do-i-pay-for-azure-vms"></a>Como fazer para pagar as VMs do Azure?
Durante a replicação, os dados são replicados para o armazenamento do Azure e não é necessário pagar nenhuma alteração de VM. Ao executar um failover no Azure, o Site Recovery cria automaticamente as máquinas virtuais da IaaS do Azure. Depois disso, serão cobrados os recursos de computação consumidos no Azure.

### <a name="what-can-i-do-with-vmware-to-azure-replication"></a>O que é possível fazer com a replicação de VMware para Azure?
- **Recuperação de desastre**: É possível configurar a recuperação de desastre completa. Nesse cenário, você replica VMs VMware locais para o armazenamento do Azure. Em seguida, se a infraestrutura local estiver indisponível, será possível fazer failover para o Azure. Ao fazer failover, as VMs do Azure são criadas usando os dados replicados. Você poderá acessar os aplicativos e as cargas de trabalho nas VMs do Azure até que o datacenter local esteja disponível novamente. Em seguida, será possível fazer failback do Azure para o site local.
- **Migração**: É possível utilizar o Site Recovery para migrar VMs VMware locais para o Azure. Nesse cenário, você replica VMs VMware locais para o armazenamento do Azure. Em seguida, você faz failover do local para o Azure. Após o failover, os aplicativos e as cargas de trabalho estarão disponíveis e em execução nas VMs do Azure.

## <a name="azure"></a>Azure
### <a name="what-do-i-need-in-azure"></a>O que é necessário no Azure?
Você precisa de uma assinatura do Azure, um cofre dos serviços de recuperação, uma conta de armazenamento de cache, discos gerenciados e uma rede virtual. O cofre, a conta de armazenamento de cache gerenciado discos e rede deve estar na mesma região.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Minha conta do Azure precisa de permissões para criar VMs?
Se você é um administrador da assinatura, tem as permissões de replicação necessárias. Se você não tiver, você precisa de permissões para criar uma VM do Azure no grupo de recursos e especifique quando você configurar a recuperação de Site e permissões para gravar na conta de armazenamento selecionada ou gerenciado disco com base em sua configuração de rede virtual. [Saiba mais](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).

### <a name="can-i-use-guest-os-server-license-on-azure"></a>Posso usar a licença de servidor do SO convidado no Azure?
Sim, os clientes do Microsoft Software Assurance podem usar o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para economizar nos custos de licenciamento para **computadores do Windows Server** que são migrados para o Azure ou usar o Azure para a recuperação de desastre.

## <a name="pricing"></a>Preços

### <a name="how-are-licensing-charges-handled-during-replication-after-failover"></a>Como os preços de licenciamento são manipulados durante a replicação, após o failover?

Veja nossas perguntas frequentes sobre licenciamento [aqui](https://aka.ms/asr_pricing_FAQ) para obter mais informações.

### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Como posso calcular preços aproximados durante o uso do Site Recovery?

Você pode usar a [calculadora de preços](https://aka.ms/asr_pricing_calculator) para estimar os custos ao usar o Azure Site Recovery. Para a previsão detalhada nos custos, executar a ferramenta de Planejador de implantação (https://aka.ms/siterecovery_deployment_planner) e analise os [relatório de estimativa de custo](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-when-i-replicate-directly-to-managed-disk"></a>Há qualquer diferença de custo ao replicar diretamente para o disco gerenciado?

Discos gerenciados são cobrados um pouco diferente de contas de armazenamento. Consulte o exemplo a seguir para um disco de origem de 100 GiB de tamanho. O exemplo é específico para o diferencial custo de armazenamento. Esse custo não inclui o custo de instantâneos, armazenamento em cache e transações.

* Conta de armazenamento padrão Vs. Disco gerenciado de HDD padrão

    - **Disco de armazenamento provisionado pelo ASR**: S10
    - **Conta de armazenamento padrão cobrada em consumido volume**: US $5 por mês
    - **Disco gerenciado Standard cobrado por volume provisionado**: US $5.89 por mês

* Conta de armazenamento Premium Vs. Premium SSD Managed Disk 
    - **Disco de armazenamento provisionado pelo ASR**: P10
    - **Conta de armazenamento Premium é cobrado no volume provisionado**: US $17.92 por mês
    - **Disco gerenciado Premium, cobrado por volume provisionado**: US $17.92 por mês

Saiba mais sobre [detalhadas de preços de discos gerenciados](https://azure.microsoft.com/pricing/details/managed-disks/).

### <a name="do-i-incur-additional-charges-for-cache-storage-account-with-managed-disks"></a>Eu incorro em encargos adicionais para a conta de armazenamento em Cache com discos gerenciados?

Não, você não incorrem em encargos adicionais para o cache. Cache sempre faz parte do VMware para arquitetura do Azure. Quando você replica para conta de armazenamento padrão, esse armazenamento em cache é parte da mesma conta de armazenamento de destino.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Sou usuário do Azure Site Recovery há mais de um mês. Eu ainda terei os primeiros 31 dias gratuitos para todas as instâncias protegidas?

Sim, não importa por quanto tempo você está usando o Azure Site Recovery. Nenhuma instância protegida gera cobranças do Azure Site Recovery durante os primeiros 31 dias. Por exemplo, se você esteve protegendo 10 instâncias nos últimos seis meses e conectar uma 11ª instância ao Azure Site Recovery, não haverá cobranças do Azure Site Recovery para a 11ª instância durante os primeiros 31 dias. As primeiras 10 instâncias continuam gerando encargos do Azure Site Recovery, uma vez que elas foram protegidas por mais de 31 dias.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, serei cobrado por outras tarifas do Azure?

Sim. Entretanto, embora o Azure Site Recovery seja gratuito durante os primeiros 31 dias de uma instância protegida, você poderá ser cobrado pelo Armazenamento do Microsoft Azure, por transações de armazenamento e por transferência de dados. Uma máquina virtual recuperada também poderá gerar encargos de computação do Azure.

### <a name="what-charges-do-i-incur-while-using-azure-site-recovery"></a>Quais encargos serão cobrados quando eu usar o Azure Site Recovery?

Veja nossas [perguntas frequentes sobre custos incorridos](https://aka.ms/asr_pricing_FAQ) para obter informações detalhadas.

### <a name="is-there-a-cost-associated-to-perform-dr-drillstest-failover"></a>Há um custo associado para executar análises de recuperação de desastres/failover de teste?

Não há nenhum custo separado para análise de recuperação de desastres. Haverá encargos de computação após a máquina virtual ser criada depois do failover de teste.

## <a name="azure-site-recovery-components-upgrade"></a>Atualização de componentes do Azure Site Recovery

### <a name="my-mobility-agentconfiguration-serverprocess-server-version-is-very-old-and-my-upgrade-has-failed-how-should-i-upgrade-to-latest-version"></a>A versão do meu agente de mobilidade/servidor de configuração/servidor de processos é muito antiga e minha atualização falhou. Como devo atualizar para a versão mais recente?

O Azure Site Recovery segue o modelo de suporte N-4. Confira a nossa [declaração de suporte](https://aka.ms/asr_support_statement) para entender os detalhes sobre como atualizar de versões muito antigas.

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-azure-site-recovery"></a>Onde posso encontrar as notas sobre a versão/pacotes cumulativos de atualizações do Azure Site Recovery?

Confira o [documento](https://aka.ms/asr_update_rollups) para saber mais sobre as notas de versão. Você pode encontrar links de instalação dos respectivos componentes em cada pacote cumulativo de atualizações.

### <a name="how-should-i-upgrade-site-recovery-components-for-on-premises-vmware-or-physical-site-to-azure"></a>Como devo atualizar dos componentes do Azure Site Recovery para site físico ou VMware local no Azure?

Confira nossas diretrizes fornecidas [aqui](https://aka.ms/asr_vmware_upgrades) para atualizar seus componentes.

## <a name="is-reboot-of-source-machine-mandatory-for-each-upgrade"></a>A reinicialização do computador de origem é obrigatória a cada atualização?

Embora seja recomendável, a reinicialização não é obrigatória a cada atualização. Clique [aqui](https://aka.ms/asr_vmware_upgrades) para obter diretrizes detalhadas.

## <a name="on-premises"></a>Configuração local

### <a name="what-do-i-need-on-premises"></a>O que é necessário no local?

No local, você precisa:
- Componentes do Site Recovery, instalados em uma única VM VMware.
- Uma infraestrutura VMware, com pelo menos um host ESXi, e recomendamos um servidor vCenter.
- Um ou mais VMs do VMware para replicar.

[Saiba mais](vmware-azure-architecture.md) sobre a arquitetura do VMware para o Azure.

O servidor de configuração local pode ser implementado da seguinte forma:

- Recomendamos que você implante o servidor de configuração como uma VM VMware usando um modelo OVA com o servidor de configuração pré-instalado.
- Se por algum motivo você não puder usar um modelo, poderá configurar o servidor de configuração manualmente. [Saiba mais](physical-azure-disaster-recovery.md#set-up-the-source-environment).



### <a name="where-do-on-premises-vms-replicate-to"></a>Para onde as VMs locais são replicadas?
Os dados replicam para o armazenamento do Azure. Quando você executa um failover, recuperação de Site cria VMs do Azure da conta de armazenamento automaticamente ou com base em sua configuração de disco gerenciado.

## <a name="replication"></a>Replicação

### <a name="what-applications-can-i-replicate"></a>Quais aplicativos posso replicar?
É possível replicar qualquer aplicativo ou carga de trabalho em execução em uma VM VMware que esteja em conformidade com os [requisitos de replicação](vmware-physical-azure-support-matrix.md##replicated-machines). O Site Recovery fornece suporte para replicação com reconhecimento de aplicativo, de modo que os aplicativos possam fazer failover e failback para um estado inteligente. O Site Recovery integra-se a aplicativos da Microsoft como SharePoint, Exchange, Dynamics, SQL Server e Active Directory e trabalha em conjunto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre a proteção de carga de trabalho.

### <a name="can-i-protect-a-virtual-machine-that-has-docker-disk-configuration"></a>Posso proteger uma máquina virtual que tem a configuração de disco do Docker?

Não. Esse é um cenário sem suporte.

### <a name="can-i-replicate-to-azure-with-a-site-to-site-vpn"></a>É possível replicar para o Azure com uma VPN site a site?
O Site Recovery replica dados do local para o armazenamento do Azure em um ponto de extremidade público ou utilizando o emparelhamento público do ExpressRoute. A replicação de uma rede VPN site a site não tem suportada.

### <a name="can-i-replicate-to-azure-with-expressroute"></a>É possível replicar para o Azure com o ExpressRoute?
Sim, o ExpressRoute pode ser utilizado para replicar VMs para o Azure. Site Recovery replica os dados no armazenamento do Azure em um ponto de extremidade público. Você precisa configurar o [emparelhamento público](../expressroute/expressroute-circuit-peerings.md#publicpeering) ou [emparelhamento da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) para usar o ExpressRoute para replicação de Site Recovery. Emparelhamento da Microsoft é o domínio de roteamento recomendado para replicação. Assegure que os [Requisitos de Rede](vmware-azure-configuration-server-requirements.md#network-requirements) também sejam atendidos para replicação. Após fazer failover das VMs para uma rede virtual do Azure, será possível acessá-las, utilizando o [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering).

### <a name="how-can-i-change-storage-account-after-machine-is-protected"></a>Como alterar a conta de armazenamento depois que o computador for protegido?

Você precisará desabilitar e habilitar a replicação para atualizar ou fazer downgrade do tipo de conta de armazenamento.

### <a name="can-i-replicate-to-storage-accounts-for-new-machine"></a>É possível replicar para contas de armazenamento para a nova máquina?

Não, a partir de 19 de março ', você pode replicar para o managed disks no Azure no portal. A replicação para contas de armazenamento para uma nova máquina só está disponível por meio da API REST e Powershell. Use a API versão 2016-08-10 ou 2018-01-10 para a replicação para contas de armazenamento.

### <a name="what-are-the-benefits-in-replicating-to-managed-disks"></a>Quais são os benefícios do replicando para discos gerenciados?

Leia o artigo sobre como [Azure Site Recovery simplifica recuperação de desastre com discos gerenciados](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/).

### <a name="how-can-i-change-managed-disk-type-after-machine-is-protected"></a>Como posso alterar o tipo de disco gerenciado depois que o computador é protegido?

Sim, você pode alterar facilmente o tipo de disco gerenciado. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage). No entanto, depois que você alterar o tipo de disco gerenciado, certifique-se de que você espere atualizados para pontos de recuperação a ser gerado se você precisa para testar o failover ou failover lançar essa atividade.

### <a name="can-i-switch-the-replication-from-managed-disks-to-unmanaged-disks"></a>Posso mudar a replicação de discos gerenciados para discos não gerenciados?

Não, alternando de gerenciado para não gerenciado não tem suporte.

### <a name="why-cant-i-replicate-over-vpn"></a>Por que não é possível replicar em VPN?

Ao replicar para o Azure, o tráfego de replicação alcança os pontos de extremidade públicos de um armazenamento do Azure, assim só é possível replicar pela internet pública com o ExpressRoute (emparelhamento público) e VPN não funciona.

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Pode usar SteelHeads Riverbed para replicação?

Nosso parceiro, Riverbed, fornece uma orientação detalhada sobre como trabalhar com o Azure Site Recovery. Consulte seus [guia de solução](https://community.riverbed.com/s/article/DOC-4627).

### <a name="what-are-the-replicated-vm-requirements"></a>Quais são os requisitos de VM replicadas?

Para replicação, uma VM VMware deve estar em execução em um sistema operacional com suporte. Além disso, a VM deve atender aos requisitos das VMs do Azure. [Saiba mais](vmware-physical-azure-support-matrix.md##replicated-machines) na matriz de suporte.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência é possível replicar para o Azure?
A replicação é contínua ao replicar VMs VMware para o Azure.

### <a name="can-i-retain-the-ip-address-on-failover"></a>Posso manter o endereço IP durante o failover?
Sim, você pode manter o endereço IP durante o failover. Certifique-se de mencionar o endereço IP de destino na folha “Computação e rede” antes do failover. Além disso, certifique-se de desligar os computadores no momento do failover para evitar conflitos de IP no momento de failback.

### <a name="can-i-extend-replication"></a>É possível estender replicação?
Esse tipo de replicação estendida ou encadeada não tem suporte. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>É possível fazer uma replicação inicial offline?
Não há suporte para isso. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-disks"></a>É possível excluir discos?
Sim, é possível excluir discos da replicação.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Posso alterar o tamanho da VM de destino ou o tipo VM antes do failover?
Sim, você pode alterar o tipo ou tamanho da VM a qualquer momento antes do failover indo para a computação e rede configurações do item de replicação a partir do portal.

### <a name="can-i-replicate-vms-with-dynamic-disks"></a>É possível replicar VMs com discos dinâmicos?
Discos dinâmicos podem ser replicados. O disco do sistema operacional deve ser um disco básico.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Se eu usar grupos de replicação para consistência de várias VMs, posso adicionar uma nova VM a um grupo de replicação existente?
Sim, você pode adicionar novas VMs a um grupo de replicação existente quando ativar a replicação para elas. Você não pode adicionar uma VM a um grupo de replicação existente após a replicação ser iniciada e não é possível criar um grupo de replicação para VMs existentes.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Posso modificar as VMs que estão replicando adicionando ou redimensionando discos?

Para replicação do VMware no Azure, você pode modificar o tamanho do disco. Se você quiser adicionar novos discos, precisará adicionar o disco e reativar a proteção para a VM.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-without-impacting-ongoing-replication"></a>Pode migrar em máquinas de local para um novo Vcenter sem afetar a replicação em andamento?
Não, migrar ou alterar o vCenter terá impacto sobre a replicação em andamento. Você precisa configurar o ASR com o novo vCenter e habilitar a replicação de máquinas.

### <a name="can-i-replicate-to-cachetarget-storage-account-which-has-a-vnet-with-azure-storage-firewalls-configured-on-it"></a>É possível replicar no cache/conta de armazenamento de destino que tem uma rede virtual (com firewalls da Armazenamento do Azure) configurada?
Não, o Azure Site Recovery não é compatível com a replicação no Armazenamento em rede virtual.

## <a name="configuration-server"></a>Servidor de configuração

### <a name="what-does-the-configuration-server-do"></a>O que o servidor de configuração faz?
O servidor de configuração executa os componentes locais do Site Recovery, incluindo:
- O servidor de configuração que coordena as comunicações entre local e Azure e gerencia a replicação de dados.
- Servidor em processo que age como um gateway de replicação. Esse servidor recebe os dados de replicação, otimiza isso com cache, compactação e criptografia, e envia-os para o armazenamento do Azure. O servidor em processo também instala o Serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs VMware locais.
- O servidor de destino mestre que manipula os dados de replicação durante failback do Azure.

[Saiba mais](vmware-azure-architecture.md) sobre os componentes e processos do servidor de configuração.

### <a name="where-do-i-set-up-the-configuration-server"></a>Onde o servidor de configuração deve ser configurado?
É necessária uma única VM VMware local altamente disponível para o servidor de configuração.

### <a name="what-are-the-requirements-for-the-configuration-server"></a>Quais são os requisitos para o servidor de configuração?

Analise os [pré-requisitos](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>É possível configurar manualmente o servidor de configuração em vez de utilizar um modelo?
É recomendável utilizar a última versão do modelo de OVF para [criar a VM do servidor de configuração](vmware-azure-deploy-configuration-server.md). Se por algum motivo isso não for possível, por exemplo, não tiver acesso ao servidor VMware, você poderá [baixar o arquivo de Instalação Unificada](physical-azure-set-up-source.md) do portal e executá-lo em uma VM.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Um servidor de configuração pode replicar para mais de uma região?
 Não. Para fazer isso, é necessário configurar um servidor de configuração em cada região.

### <a name="can-i-host-a-configuration-server-in-azure"></a>É possível hospedar um servidor de configuração no Azure?
Embora seja possível, a VM do Azure em execução no servidor de configuração precisa comunicar-se com a infraestrutura VMware e VMs locais. Isso pode adicionar latências e impactar a replicação em andamento.

### <a name="how-do-i-update-the-configuration-server"></a>Como fazer para atualizar o servidor de configuração?
[Aprenda sobre](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) atualizar o servidor de configuração. Você pode encontrar as informações de atualização mais recentes na [página de atualizações do Azure](https://azure.microsoft.com/updates/?product=site-recovery). Você também pode fazer o download diretamente a versão mais recente do servidor de configuração do [Centro de Download da Microsoft](https://aka.ms/asrconfigurationserver). Se a sua versão for quatro versões anterior à versão atual, confira nossa [declaração de suporte](https://aka.ms/asr_support_statement) para obter orientações para atualizar.

### <a name="should-i-backup-the-deployed-configuration-server"></a>Eu devo fazer backup do servidor de configuração implantado?
É recomendável fazer backups agendados regulares do servidor de configuração. Para obter failback com êxito, a máquina virtual que está executando failback deve existir no banco de dados do servidor de configuração e o servidor de configuração deve estar em execução e em um estado conectado. Você pode aprender mais sobre as tarefas comuns de gerenciamento do servidor de configuração [aqui](vmware-azure-manage-configuration-server.md).

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Quando estou configurando o servidor de configuração, posso fazer o download e instalar o MySQL manualmente?

Sim. Faça o download o MySQL e colocá-lo na pasta **C:\Temp\ASRSetup**. Em seguida, instale-o manualmente. Quando você configura a VM do servidor de configuração e aceita os termos, o MySQL será listado como **Já instalado** em **Faça o download e instale**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Posso evitar de fazer o download de MySQL mas permitir que o Site Recovery instale-o?

Sim. Faça o download do instalador do MySQL e coloque-o na pasta **C:\Temp\ASRSetup**.  Quando você configurar a VM do servidor de configuração, aceite os termos e clique em **Faça o download e instale**, o portal usará o instalador que você adicionou para instalar o MySQL.
 
### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Posso usar a VM do servidor de configuração para mais alguma coisa?
Não, você só deve usar a VM para o servidor de configuração. 

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Posso clonar um servidor de configuração e usá-lo para orquestração?
Não, você deve configurar um servidor de configuração atualizado para evitar problemas de registro.

### <a name="can-i-change-the-vault-registered-in-the-configuration-server"></a>Posso alterar o cofre registrado no servidor de configuração?
 Não. Depois que um cofre é registrado no servidor de configuração, ele não pode ser alterado. Revisão [deste artigo](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) para obter as etapas do novo registro.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Posso usar o mesmo servidor de configuração para recuperação de desastres de VMs VMware e servidores físicos
Sim, mas observe que a máquina física só pode ser reprovada em uma VM do VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Onde posso fazer o download da frase secreta para o servidor de configuração?
[Examine este artigo](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) para saber mais sobre como fazer o download da frase secreta.

### <a name="where-can-i-download-vault-registration-keys"></a>Em que local posso baixar as chaves de registro do cofre?

No **Cofre dos Serviços de Recuperação**, **Gerenciar** > **Infraestrutura do Site Recovery** > **Servidores de Configuração**. Em **Servidores**, selecione **Baixar a chave de registro** para baixar o arquivo de credenciais do cofre.



## <a name="mobility-service"></a>Serviço de mobilidade

### <a name="where-can-i-find-the-mobility-service-installers"></a>Onde é possível localizar os instaladores do Serviço de Mobilidade?
Os instaladores são mantidos na pasta **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** no servidor de configuração.

## <a name="how-do-i-install-the-mobility-service"></a>Como fazer para instalar o Serviço Mobility?
Você instala em cada VM que deseja replicar, usando uma [instalação push ](vmware-azure-install-mobility-service.md) ou [instalação manual](vmware-physical-mobility-service-install-manual.md) da interface do usuário ou do Powershell. Como alternativa, você pode implantar usando uma ferramenta de implantação, como [System Center Configuration Manager](vmware-azure-mobility-install-configuration-mgr.md).



## <a name="security"></a>Segurança

### <a name="what-access-does-site-recovery-need-to-vmware-servers"></a>Que acesso o Site Recovery precisa para os servidores VMware?
O Site Recovery precisa de acesso aos servidores VMware para:

- Configure uma VM VMware executando o servidor de configuração e outros componentes locais do Site Recovery. [Saiba mais](vmware-azure-deploy-configuration-server.md)
- Descobrir automaticamente as VMs para replicação. Saiba como preparar uma conta para descoberta automática. [Saiba mais](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)


### <a name="what-access-does-site-recovery-need-to-vmware-vms"></a>Que acesso o Site Recovery precisa para VMs VMware?

- Para replicar, uma VM VMware deve ter o serviço de Mobilidade do Site Recovery instalado e em execução. É possível implantar a ferramenta manualmente ou especificar que o Site Recovery faça uma instalação por push do serviço, quando você habilitar a replicação para uma VM. Para a instalação por push, o Site Recovery precisa de uma conta que possa ser utilizada para instalar o componente de serviço. [Saiba mais](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation). O servidor em processo (em execução, por padrão, no servidor de configuração) executa essa instalação. [Saiba mais](vmware-azure-install-mobility-service.md) sobre a instalação do serviço de Mobilidade.
- Durante a replicação, as VMs comunicam-se com o Site Recovery da seguinte maneira:
    - As VMs comunicam-se com o servidor de configuração na porta HTTPS 443 para gerenciamento de replicação.
    - As VMs enviam os dados de replicação para o servidor em processo na porta HTTPS 9443 (pode ser modificada).
    - Se você habilitar a consistência de várias VMs, as VMs irão comunicar-se pela porta 20004.


### <a name="is-replication-data-sent-to-site-recovery"></a>Os dados de replicação são enviados para o Site Recovery?
Não, o Site Recovery não intercepta dados replicados e não tem informações sobre o que está sendo executado nas VMs. Os dados de replicação são trocados entre os hipervisores VMware e o armazenamento do Azure. O Site Recovery não tem a capacidade de interceptar os dados. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.  

O Site Recovery é certificado pela ISO 27001:2013, 27018, HIPAA, DPA e está em processo de conclusão de avaliação dos padrões SOC2 e FedRAMP JAB.

### <a name="can-we-keep-on-premises-metadata-within-a-geographic-regions"></a>É possível manter os metadados locais em uma região geográfica?
Sim. Quando você cria um cofre em uma região, garantimos que todos os metadados utilizados pelo Site Recovery permanecem dentro do limite geográfico dessa região.

### <a name="does-site-recovery-encrypt-replication"></a>O Site Recovery criptografa a replicação?
Sim, tanto criptografia em trânsito como [criptografia no Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) têm suporte.


## <a name="failover-and-failback"></a>Failover e failback
### <a name="can-i-use-the-process-server-at-on-premises-for-failback"></a>Posso usar o servidor de processo no local para failback?
É altamente recomendável criar um servidor de processo no Azure para fins de failback para evitar as latências de transferência de dados. Além disso, no caso de você separado da rede de VMs de origem com a rede voltado para o Azure no servidor de configuração, é essencial para usar o servidor de processo criado no Azure para failback.

### <a name="how-far-back-can-i-recover"></a>A que tempo é possível fazer failback de recuperação?
De VMware para o Azure, o ponto de recuperação mais antigo que você pode utilizar é de 72 horas.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Como fazer para acessar VMs do Azure após o failover?
Após o failover, você poderá acessar as VMs do Azure por meio de uma conexão segura com a Internet, por meio de uma VPN site a site ou por meio do Microsoft Azure ExpressRoute. Você precisará preparar uma série de coisas para se conectar. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="is-failed-over-data-resilient"></a>Failover de dados é resiliente?
O Azure foi desenvolvido para resiliência. O Site Recovery foi projetado para failover em um datacenter do Azure secundário, de acordo com o SLA do Azure. Quando ocorre um failover, garantimos que seus metadados e cofres permaneçam na mesma região geográfica escolhida para o cofre.

### <a name="is-failover-automatic"></a>O failover é automático?
[Failover](site-recovery-failover.md) não é automático. Iniciar failovers com um único clique no portal, ou você pode usar [PowerShell](/powershell/module/azurerm.siterecovery) para disparar um failover.

### <a name="can-i-fail-back-to-a-different-location"></a>É possível fazer failback para um local diferente?
Sim, se você fez failover no Azure poderá fazer failback para um local diferente, se o original não estiver disponível. [Saiba mais](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-to-fail-back"></a>Por que preciso de uma VPN ou ExpressRoute para fazer failback?
Ao fazer failback do Azure, os dados do Azure são copiados de volta para a VM local e o acesso privado é necessário.

### <a name="can-i-resize-the-azure-vm-after-failover"></a>Eu posso redimensionar a VM do Azure após failover?
Não, você não pode alterar o tamanho nem o tipo da VM de destino após o failover.


## <a name="automation-and-scripting"></a>Automação e scripts

### <a name="can-i-set-up-replication-with-scripting"></a>É possível configurar a replicação com scripts?
Sim. Você pode automatizar fluxos de trabalho do Site Recovery, utilizando a API Rest, o PowerShell ou o SDK do Azure.[Saiba mais](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Desempenho e capacidade
### <a name="can-i-throttle-replication-bandwidth"></a>É possível limitar a largura de banda de replicação?
Sim. [Saiba mais](site-recovery-plan-capacity-vmware.md).


## <a name="next-steps"></a>Próximas etapas
* [Analisar](vmware-physical-azure-support-matrix.md) os requisitos de suporte.
* [Configurar](vmware-azure-tutorial.md) replicação de VMware para o Azure.
