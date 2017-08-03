---
title: "Replicar uma implantação do Dynamics AX de várias camadas usando o Azure Site Recovery | Microsoft Docs"
description: Este artigo descreve como replicar e proteger o Dynamics AX usando o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: asgang
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: cf568d20f60709dbb64774bcbcc1b4aa6c43d8d3
ms.contentlocale: pt-br
ms.lasthandoff: 06/16/2017


---
# <a name="replicate-a-multi-tier-dynamics-ax-application-using-azure-site-recovery"></a>Replicar um aplicativo do Dynamics AX de várias camadas usando o Azure Site Recovery

## <a name="overview"></a>Visão geral


O Microsoft Dynamics AX é uma das mais populares soluções de ERP entre as empresas para padronizar o processo entre locais, gerenciar recursos e simplificar a conformidade. Considerando que o aplicativo é crítico para uma organização, é muito importante ter certeza de que, em caso de desastre, o aplicativo estará em execução no mínimo de tempo.

Hoje, o Microsoft Dynamics AX não fornece nenhuma funcionalidade integrada de recuperação de desastre. O Microsoft Dynamics AX consiste em vários componentes de servidor como o Servidor de Objetos de Aplicativo, o AD (Active Directory), o servidor de Banco de Dados SQL, o SharePoint Server, o Servidor de Relatórios, etc. Gerenciar a recuperação de desastre de cada um desses componentes manualmente não é apenas caro mas também propenso a erros.

Este artigo explica em detalhes sobre como você pode criar uma solução de recuperação de desastre para seu aplicativo Dynamics AX usando o [Azure Site Recovery](site-recovery-overview.md). Ele também discutirá failovers planejados/não planejados/de teste usando o plano de recuperação de um único clique, configurações com suporte e pré-requisitos.
A solução de recuperação de desastre com base no Azure Site Recovery é totalmente testada, certificada e recomendada pelo Microsoft Dynamics AX.



## <a name="prerequisites"></a>Pré-requisitos

A implementação de recuperação de desastre para aplicativos do Dynamics AX usando o Azure Site Recovery requer os pré-requisitos a seguir concluídos.

•   Uma implantação local do Dynamics AX foi configurada

•   Um cofre dos Serviços do Azure Site Recovery foi criado em uma assinatura do Microsoft Azure

•   Se o Azure for seu site de recuperação, execute a ferramenta de Avaliação de Prontidão de Máquina Virtual do Azure nas VMs, a fim de garantir que elas sejam compatíveis com as VMs do Azure e com os Serviços do Azure Site Recovery


## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para a criação deste artigo, usamos as máquinas virtuais VMware com o Dynamics AX 2012R3 no Windows Server 2012 R2 Enterprise. Já que a replicação do Site Recovery é independente do aplicativo, as recomendações fornecidas aqui devem servir também para os cenários a seguir.

### <a name="source-and-target"></a>Origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim

## <a name="enable-dr-of-dynamics-ax-application-using-asr"></a>Habilitar a DR do aplicativo Dynamics AX usando o ASR
### <a name="protect-your-dynamics-ax-application"></a>Proteger o aplicativo Dynamics AX
Cada componente do Dynamics AX precisa ser protegido para habilitar a replicação e a recuperação completas do aplicativo. Esta seção aborda:

**1. Proteção do Active Directory**

**2. Proteção da Camada SQL**

**3. Proteção de Aplicativo e as Camadas da Web**

**4. Configuração de rede**

**5. Plano de Recuperação**

### <a name="1-setup-ad-and-dns-replication"></a>1. Instalação do AD e replicação de DNS

O Active Directory é necessário no site de DR para o aplicativo Dynamics AX funcionar. Há duas opções recomendadas com base na complexidade do ambiente local do cliente.

**Opção 1**

Se o cliente tem um número pequeno de aplicativos e um único controlador de domínio para todo o site local e eles estão apresentando falhas em todo o site conjuntamente, é recomendável usar a replicação do ASR para replicar o computador do DC para um site secundário (isso é aplicável tanto de site a site quando de site para o Azure).

**Opção 2**

Se o cliente tem um grande número de aplicativos e está executando uma floresta do Active Directory e se fará failover de poucos aplicativos por vez, é recomendável configurar um controlador de domínio adicional no site de DR (em um site secundário ou no Azure).

Consulte [guia complementar para tornar um controlador de domínio disponível no site de DR](site-recovery-active-directory.md). Para o restante deste documento, presumiremos que um DC está disponível no site de DR.

### <a name="2-setup-sql-server-replication"></a>2. Configurar a Replicação do SQL Server
Consulte o guia complementar para obter diretrizes técnicas detalhadas sobre a opção recomendada para proteger a [camada SQL](site-recovery-sql.md).

### <a name="3-enable-protection-for-dynamics-ax-client-and-aos-vms"></a>3. Habilitar a proteção do cliente Dynamics AX e VMs AOS
Executar configuração relevante do Azure Site Recovery com base em as VMs estarem sendo implantadas em [Hyper-V](site-recovery-hyper-v-site-to-azure.md) ou [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> A frequência de falha consistente recomendada para configurar é 15 minutos.
>

O instantâneo abaixo mostra o status de proteção de VMs componentes do Dynamics no cenário de proteção 'Site de VMware para Azure'.
![Itens protegidos ](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Configurar Rede
Definir as Configurações de Rede e de Computação de VM

Para as VMs do AOS e cliente AX, defina configurações de rede no ASR para que as redes de VMs sejam anexadas à rede de DR correta após o failover. Verifique se a rede de DR para essas camadas é roteável para a camada SQL.

Você pode selecionar a VM nos itens replicados para definir as configurações de rede, conforme mostrado no instantâneo a seguir.

* Para servidores AOS, selecione o conjunto de disponibilidade correto.

* Se você estiver usando um IP estático, especifique o IP que você deseja usar na máquina virtual nas ![Configurações de Rede](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png) do campo **IP de Destino**



### <a name="5-creating-a-recovery-plan"></a>5. Criar um plano de recuperação

Você pode criar um plano de recuperação no ASR para automatizar o processo de failover. Adicione a camada de aplicativos e a camada da Web no plano de recuperação. Ordene-as em grupos diferentes, para que o front-end se desligue antes da camada de aplicativos.

1)  Selecione o cofre do ASR em sua assinatura e clique no bloco 'Planos de Recuperação'.

2)  Clique em '+ Plano de recuperação' e especifique um nome.

3)  Selecione a 'Origem' e o 'Destino'. O destino pode ser o Azure ou um site secundário. Caso escolha o Azure, você deverá especificar o modelo de implantação

![Criar Plano de Recuperação](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4)  Selecione as VMs do AOS e de cliente para o plano de recuperação e clique em ✓.
![Criar Plano de Recuperação](./media/site-recovery-dynamics-ax/selectvms.png)


![Plano de Recuperação](./media/site-recovery-dynamics-ax/recoveryplan.png)

Você pode personalizar o plano de recuperação do aplicativo Dynamics AX adicionando várias etapas, conforme detalhado abaixo. O instantâneo acima mostra o plano de recuperação completo depois de adicionar todas as etapas.

*Etapas:*

*1. Etapas de failover do SQL Server*

Consulte o guia complementar ['Solução de recuperação de desastres do SQL Server'](site-recovery-sql.md) para obter detalhes sobre etapas de recuperação específicas para o SQL Server.

*2. Grupo de failover 1: fazer failover das VMs do AOS*

Verifique se o ponto de recuperação selecionado está tão próximo quanto possível do PIT do banco de dados, mas não à frente dele.

*3. Script: adicionar balanceador de carga (apenas E-A)* Adicione um script (por meio da Automação do Azure) depois do aparecimento do grupo de VMs do AOS para adicionar um balanceador de carga para ele. Você pode usar um script para realizar essa tarefa. Consulte o artigo [como adicionar balanceador de carga para DR de aplicativos de várias camadas](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/)

*4. Grupo de Failover 2: faça failover das VMs de cliente do AX.*
Faça failover das VMs da camada da web como parte do plano de recuperação.


### <a name="doing-a-test-failover"></a>Executar um failover de teste

Consulte os guias complementares 'Solução de recuperação de desastres do AD' e 'Solução de recuperação de desastre do SQL Server' para considerações específicas para o AD e o SQL Server, respectivamente, durante o failover de teste.

1.  Vá para o Portal do Azure e selecione seu cofre do Site Recovery.
2.  Clique no plano de recuperação criado para o Dynamics AX.
3.  Clique em 'Failover de Teste'.
4.  Selecione a rede virtual para iniciar o processo de failover de teste.
5.  Quando o ambiente secundário estiver funcionando, você poderá executar sua validações.
6.  Após a conclusão das validações, selecione 'Validações concluídas', e o ambiente do failover de teste será limpo.

Siga [este guia](site-recovery-test-failover-to-azure.md) fazer um failover de teste.

### <a name="doing-a-failover"></a>Executar um failover

1.  Vá para o Portal do Azure e selecione seu cofre do Site Recovery.
2.  Clique no plano de recuperação criado para o Dynamics AX.
3.  Clique em 'Failover' e selecione 'Failover'.
4.  Selecione a rede de destino e clique no ícone de verificação ✓ para iniciar o processo de failover.

Siga [este guia](site-recovery-failover.md) quando estiver realizando um failover.

### <a name="perform-a-failback"></a>Executar um Failback

Consulte o guia complementar 'Solução de DR do SQL Server' para considerações específicas para o SQL Server durante o failback.

1.  Vá para o Portal do Azure e selecione seu cofre do Site Recovery.
2.  Clique no plano de recuperação criado para o Dynamics AX.
3.  Clique em 'Failover' e selecione o failover.
4.  Clique em 'Alterar Direção'.
5.  Selecione as opções apropriadas – de sincronização de dados e de criação de VMs
6.  Clique em ✓ para iniciar o processo de 'Failback'.


Siga [estas diretrizes](site-recovery-failback-azure-to-vmware.md) quando estiver realizando um failback.

##<a name="summary"></a>Resumo
Com o Azure Site Recovery, você pode criar um plano totalmente automatizado de recuperação de desastre para seu aplicativo do Dynamics AX. Você poderá iniciar o failover em segundos e de qualquer lugar, caso haja uma interrupção e fazer o aplicativo funcionar em questão de minutos.

## <a name="next-steps"></a>Próximas etapas
Leia [Quais cargas de trabalho posso proteger?](site-recovery-workload.md) para saber mais sobre como proteger as cargas de trabalho corporativas com o Azure Site Recovery.

