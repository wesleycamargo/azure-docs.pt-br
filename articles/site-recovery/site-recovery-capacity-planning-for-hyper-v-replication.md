---
title: Ferramenta de planejamento de capacidade do Hyper-V para o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como executar a ferramenta de planejamento de capacidade do Hyper-V para o Azure Site Recovery
services: site-recovery
documentationcenter: na
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 2bc3832f-4d6e-458d-bf0c-f00567200ca0
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: nisoneji
ms.openlocfilehash: 80c3dcb65bd74bcfa3acc5f12dd5c45cd1c06455
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="hyper-v-capacity-planning-tool-for-site-recovery"></a>Ferramenta de planejamento de capacidade do Hyper-V para o Azure Site Recovery

A nova versão aprimorada do [Planejador de Implantações do Azure Site Recovery do Hyper-V para implantação do Azure](site-recovery-hyper-v-deployment-planner.md) já está disponível. Ele substitui a ferramenta antiga. Use a nova ferramenta para o planejamento da implantação. A ferramenta oferece as seguintes orientações:

* Avaliação de qualificação de VM com base em número de discos, tamanho de disco, IOPS, variações e algumas características da VM
* Avaliação de largura de banda de rede necessária versus RPO
* Requisitos de infraestrutura do Azure
* Requisitos de infraestrutura local
* Diretriz de envio em lote da replicação inicial
* Custo estimado total de recuperação de desastre para Azure

Como parte da implantação do Azure Site Recovery, você precisa descobrir seus requisitos de replicação e largura de banda. A ferramenta de planejamento de capacidade do Hyper-V para Site Recovery ajuda a determinar esses requisitos para replicação de VM do Hyper-V.

Este artigo descreve como executar a ferramenta de planejamento de capacidade do Hyper-V. Essa ferramenta deve ser usada com as informações em [Planejamento de capacidade para Site Recovery](site-recovery-capacity-planner.md).

## <a name="before-you-start"></a>Antes de começar
Execute a ferramenta em um nó de cluster ou de servidor do Hyper-V no site primário. Para executar a ferramenta, os servidores host do Hyper-V precisam de:

* Sistema operacional: Windows Server 2012 ou 2012 R2
* Memória: 20 MB (mínimo)
* CPU: sobrecarga de 5% (mínimo)
* Espaço em disco: 5 MB (mínimo)

Antes de executar a ferramenta, é necessário preparar o site primário. Se você estiver replicando entre dois sites locais e deseja verificar a largura de banda, também será necessário preparar um servidor de réplica.

## <a name="step-1-prepare-the-primary-site"></a>Etapa 1: preparar o site primário

1. No site primário, crie uma lista de todas as VMs do Hyper-V que você deseja replicar. Liste os hosts ou clusters do Hyper-V em que as VMs estão localizadas. A ferramenta pode ser executada para vários hosts independentes ou para um único cluster, mas não para ambos. Ela também precisa ser executada separadamente para cada sistema operacional. Colete as seguintes informações sobre servidores do Hyper-V:

   * Servidores independentes Windows Server 2012
   * Clusters do Windows Server 2012
   * Servidores independentes Windows Server 2012 R2
   * Clusters do Windows Server 2012 R2

2. Habilite o acesso remoto à Instrumentação de Gerenciamento do Windows em todos os hosts e clusters do Hyper-V. Execute este comando em cada servidor ou cluster para ter certeza de que as regras de firewall e as permissões de usuário estão definidas:

        netsh firewall set service RemoteAdmin enable
3. Habilite o monitoramento de desempenho nos servidores e clusters da seguinte maneira:

   a. Abra o Firewall do Windows com o snap-in **Segurança Avançada**. 
   
   b. Selecione a regra de entrada **Acesso à Rede COM+ (DCOM-IN)**. Selecione todas as regras no grupo **Gerenciamento Remoto do Log de Eventos**.

## <a name="step-2-prepare-a-replica-server-on-premises-to-on-premises-replication"></a>Etapa 2: preparar um servidor de réplica (replicação local para o local)
Não será necessário fazer isso se você estiver replicando para o Azure.

Recomendamos a configuração de um único host do Hyper-V como servidor de recuperação, para que uma VM fictícia possa ser replicada nele para verificar a largura de banda. Você pode ignorar essa etapa, mas só é possível medir a largura de banda se fizer isso.

1. Se você quiser usar um nó de cluster como a réplica, configure o Agente de Réplica do Hyper-V:

   a. Em **Gerenciador do Servidor**, abra **Gerenciador de Cluster de Failover**.

   b. Conecte-se ao cluster e realce o nome dele. Selecione **Ações** > **Configurar função** para abrir o Assistente para Alta Disponibilidade.

   c. Em **Selecionar Função**, clique em **Agente de Réplica do Hyper-V**. No assistente, digite um nome para **Nome NetBIOS**. Digite um endereço para o **Endereço IP** que possa ser usado como ponto de conexão com o cluster (chamado de ponto de acesso para cliente). O **Agente de Réplica do Hyper-V** é configurado, resultando em um nome de ponto de acesso para cliente. Anote esse nome.

   d. Verifique se a função de Agente de Réplica do Hyper-V fica online com êxito e se pode fazer o failover entre todos os nós do cluster. Clique com o botão direito do mouse na função e selecione **Mover** > **Selecionar nó**. Selecione um nó e escolha **OK**.

   e. Se você estiver usando a autenticação baseada em certificado, verifique se cada nó do cluster e o ponto de acesso para cliente têm os certificados instalados.

2. Para habilitar um servidor de réplica, siga estas etapas:

   a. Para um cluster, abra **Gerenciador de Cluster de Falha** e conecte-se ao cluster. Selecione **Funções** e uma função. Selecione **Configurações de Replicação** > **Habilitar este cluster como Servidor de Réplica**. Se você usar um cluster como a réplica, também será necessário ter a função do Agente de Réplica do Hyper-V no cluster no site primário.

   b. Para um servidor autônomo, abra o **Gerenciador Hyper-V**. No painel **Ações**, selecione **Configurações do Hyper-V** para o servidor que você deseja habilitar. Em **Configuração de Replicação**, selecione **Habilitar este computador como Servidor de Réplica**.

3. Para configurar a autenticação, siga estas etapas:

   a. Em **Autenticação e portas**, selecione como autenticar o servidor primário e as portas de autenticação. Se você usa um certificado, selecione **Selecionar Certificado**. Use o Kerberos se os hosts primário e de recuperação do Hyper-V estiverem no mesmo domínio ou em domínios confiáveis. Use certificados para domínios diferentes ou para uma implantação de grupo de trabalho.

   b. Na seção **Autorização e armazenamento**, permita que **qualquer** servidor autenticado (primário) envie dados de replicação para este servidor de réplica.

   ![Configuração de replicação](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

   c. Execute **netsh http show servicestate** para verificar se o ouvinte está em execução para o protocolo ou a porta especificados. 

4. Configure os firewalls. Durante a instalação do Hyper-V, as regras de firewall são criadas para permitir o tráfego nas portas padrão (HTTPS na 443 e Kerberos na 80). Habilite essas regras da seguinte maneira:

    - Autenticação de certificado no cluster (443): ``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}``
    - Autenticação Kerberos no cluster (80): ``Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}``
    - Autenticação de certificado no servidor independente: ``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"``
    - Autenticação Kerberos no servidor independente: ``Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"``

## <a name="step-3-run-the-capacity-planning-tool"></a>Etapa 3: executar a ferramenta de planejamento de capacidade
Após a preparação do site primário e da configuração de um servidor de recuperação, você pode executar a ferramenta.

1. [Baixe](https://www.microsoft.com/download/details.aspx?id=39057) a ferramenta do Centro de Download da Microsoft.

2. Execute a ferramenta em um dos servidores primários (ou em um dos nós do cluster primário). Clique com o botão direito do mouse no arquivo .exe e selecione **Executar como administrador**.

3. Em **Antes de começar**, especifique por quanto tempo você deseja coletar dados. Recomendamos a execução da ferramenta durante as horas de produção para garantir que os dados sejam representativos. Se estiver apenas tentando validar a conectividade da rede, escolha coletar por apenas um minuto.

    ![Antes de começar](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. Em **Detalhes do site primário**, especifique o nome do servidor ou o FQDN para um host autônomo. Para um cluster, especifique o FQDN do ponto de acesso para cliente, nome do cluster ou qualquer nó no cluster. Selecione **Avançar**. A ferramenta detecta automaticamente o nome do servidor no qual está em execução. A ferramenta seleciona as VMs que podem ser monitoradas para os servidores especificados.

    ![Detalhes do site primário](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. Em **Detalhes do site de réplica**, se você estiver replicando para o Azure ou para um data center secundário e ainda não tiver configurado um servidor de réplica, selecione **Ignorar os testes que envolvem o site de réplica**. Se você estiver replicando para um data center secundário e tiver configurado um tipo de réplica, insira o FQDN do servidor autônomo ou o ponto de acesso para cliente para o cluster em **Nome do servidor (ou) CAP do Agente de Réplica do Hyper-V**.

    ![Detalhes do site de réplica](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. Em **Detalhes de réplica estendida**, habilite **Ignorar os testes que envolvem o site de réplica estendida**. Não há suporte para eles no Site Recovery.

7. Em **Escolha as VMs a replicar**, a ferramenta se conecta ao servidor ou cluster. Isso exibe as máquinas virtuais e os discos em execução no servidor primário, com base nas configurações que você especificou na página **Detalhes do site primário**. As VMs que já foram habilitadas para replicação não serão exibidas, nem as que não estão em execução. Selecione as VMs das quais você deseja coletar métricas. A seleção dos VHDs também coleta automaticamente os dados para as VMs.

8. Se você configurou um servidor ou um cluster de réplica, em **Informações da rede**, especifique a largura de banda WAN aproximada a ser usada entre os sites primário e de réplica. Se você configurou a autenticação com certificado, selecione os certificados.

    ![Informações da rede](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

9. Em **Resumo**, verifique as configurações. Selecione **Próximo** para começar a coletar métricas. O progresso e o status da ferramenta são exibidos na página **Calcular Capacidade**. Após a conclusão da execução da ferramenta, clique em **Exibir Relatório** para exibir o resultado. Por padrão, os relatórios e logs são armazenados em **%systemdrive%\Users\Public\Documents\Capacity Planner**.

   ![Calcular Capacidade](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

## <a name="step-4-interpret-the-results"></a>Etapa 4: interpretar os resultados

Estas são as métricas importantes. Você pode ignorar as métricas que não estão listadas aqui. Elas não são relevantes para a Recuperação de Site.

### <a name="on-premises-to-on-premises-replication"></a>Replicação de local para local

* Impacto da replicação na computação e na memória do host primário
* Impacto da replicação no espaço em disco de armazenamento e no IOPS do host primário e do host de recuperação
* Largura de banda total necessária para a replicação delta (Mbps)
* Largura de banda de rede observada entre o host primário e o host de recuperação (Mbps)
* Sugestão para o número ideal de transferências paralelas ativas entre os dois hosts ou clusters

### <a name="on-premises-to-azure-replication"></a>Replicação de local para o Azure

* Impacto da replicação na computação e na memória do host primário
* Impacto da replicação no espaço do disco de armazenamento e no IOPS do host primário
* Largura de banda total necessária para a replicação delta (Mbps)

## <a name="more-resources"></a>Mais recursos

* Para saber mais sobre a ferramenta, leia o documento que acompanha o download da ferramenta.
* Assista a um passo a passo da ferramenta no artigo de Keith Mayer no [Blog TechNet](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx).
* [Obtenha os resultados](site-recovery-performance-and-scaling-testing-on-premises-to-on-premises.md) do nosso teste de desempenho para replicação de Hyper-V de local para local.

## <a name="next-steps"></a>Próximas etapas

Após concluir o planejamento de capacidade, você pode implantar o Site Recovery:
* [Replicar VMs Hyper-V em nuvens VMM para o Azure](site-recovery-vmm-to-azure.md)
* [Replicar VMs do Hyper-V (sem VMM) para o Azure](site-recovery-hyper-v-site-to-azure.md)
* [Replicar VMs do Hyper-V entre sites do VMM](site-recovery-vmm-to-vmm.md)
