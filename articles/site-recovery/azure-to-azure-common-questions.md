---
title: Perguntas comuns - recuperação de desastre de Azure para Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo resume as perguntas comuns ao configurar a recuperação de desastre de VMs do Azure para outra região do Azure usando o Azure Site Recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: asgang
ms.openlocfilehash: 6fe7152e43640a809ab9f4de39b1c6b599975a20
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969940"
---
# <a name="common-questions---azure-to-azure-replication"></a>Perguntas comuns - replicação de Azure para Azure

Este artigo fornece respostas para as perguntas comuns que surgem ao implantar a recuperação de desastre de VMs do Azure para outra região do Azure. Se você tiver dúvidas após a leitura deste artigo, publique-as no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Geral
### <a name="how-is-site-recovery-priced"></a>Como é o Site Recovery é precificado?
Analise os detalhes em [Preços do Azure Site Recovery](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-to-configure-site-recovery-on-azure-vms-what-are-the-best-practices"></a>Como configurar o Site Recovery em VMs do Azure. Quais são as melhores práticas?
1. [Reconhecer a arquitetura de Azure para Azure](azure-to-azure-architecture.md)
2. [Examinar as configurações com suporte e sem suporte](azure-to-azure-support-matrix.md)
3. [Configurar a recuperação de desastre para VMs do Azure](azure-to-azure-how-to-enable-replication.md)
4. [Executar um failover de teste](azure-to-azure-tutorial-dr-drill.md)
5. [Failover e failback para a região primária](azure-to-azure-tutorial-failover-failback.md)

## <a name="replication"></a>Replicação

### <a name="can-i-replicate-azure-disk-encryption-enabled-vms"></a>É possível replicar VMs habilitadas com criptografia de disco do Azure?
Sim, é possível replicá-las. Consulte o [artigo](azure-to-azure-how-to-enable-replication-ade-vms.md) para habilitar replicação de VMs habilitadas com ADE (Azure Disk Encryption). Observe que somente as VMs do Azure executando sistema operacional Windows, e habilitadas para criptografia com o aplicativo Azure AD, são atualmente compatíveis com o Azure Site Recovery.

### <a name="can-i-replicate-vms-to-another-subscription"></a>É possível replicar VMs para outra assinatura?
Sim, é possível pode replicar VMs do Azure para uma assinatura diferente no mesmo locatário do Azure Active Directory.
Configurar a recuperação de desastre [entre assinaturas](https://azure.microsoft.com/blog/cross-subscription-dr) é simples. É possível selecionar outra assinatura no momento da replicação.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>É possível replicar VMs do Azure fixadas em zonas para outra região?
Sim, é possível [replicar VMs fixadas em zonas](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) para outra região.

### <a name="can-i-exclude-disks"></a>É possível excluir discos?

Sim, é possível excluir discos no momento da proteção usando o PowerShell. Consulte [Diretrizes do PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine) para excluir disco

###<a name="how-often-can-i-replicate-to-azure"></a>Com que frequência é possível replicar para o Azure?
A replicação é contínua ao replicar VMs do Azure para outra região do Azure. Verifique a arquitetura de replicação [Azure para Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process) para compreender os detalhes.

### <a name="can-i-replicate-virtual-machines-within-a-same-region-i-need-this-to-migrate-vms"></a>É possível replicar máquinas virtuais em uma mesma região? É necessário migrar VMs?
Não é possível usar a solução DR de Azure para Azure para replicar VMs em uma mesma região.

### <a name="can-i-replicate-vms-to-any-azure-region"></a>É possível replicar VMs para qualquer região do Azure?
Com o Site Recovery, é possível replicar e recuperar VMs entre quaisquer duas regiões dentro do mesmo cluster geográfico. Clusters geográficos são definidos mantendo a latência de dados e a soberania em mente. Para obter mais informações, consulte [Matriz de suporte regional](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) do Site Recovery.

### <a name="does-site-recovery-require-internet-connectivity"></a>O Site Recovery requer conectividade com a Internet?

Não, o Site Recovery não requer conectividade com a internet, mas sim acesso aos intervalos de IP e URLs do Site Recovery conforme mencionado neste [artigo](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)

## <a name="replication-policy"></a>Política de replicação

### <a name="what-is-a-replication-policy"></a>O que é uma política de replicação?
Define as configurações para o histórico de retenção do ponto de recuperação e frequência de instantâneo consistente do aplicativo. Por padrão, o Azure Site Recovery cria uma nova política de replicação com configurações padrão de “24 horas” para retenção de pontos de recuperação e “60 minutos” para a frequência de instantâneo consistente do aplicativo.

### <a name="what-is-crash-consistent-recovery-point"></a>O que é ponto de recuperação de falha consistente?
Ponto de recuperação de falha consistente representa os dados em disco, como se a VM falhou ou o cabo de alimentação foi retirado do servidor no momento em que o instantâneo foi tirado. Ele não inclui tudo o que estava na memória quando o instantâneo foi tirado. Atualmente, a maioria dos aplicativos pode recuperar-se bem a partir de instantâneos de falha consistente. Um ponto de recuperação de falha consistente é geralmente suficiente para sistemas operacionais sem banco de dados e aplicativos como servidores, servidores DHCP, servidores de impressão e assim por diante.

### <a name="what-is-application-consistent-recovery-point"></a>O que é ponto de recuperação de aplicativo consistente? 
Pontos de recuperação de aplicativo consistente são criados a partir de instantâneos de aplicativo consistente que capturam os mesmos dados de instantâneos de falha consistente, incluindo todos os dados na memória e todas as transações em andamento. Devido a seu conteúdo extra, instantâneos de aplicativo consistente são os mais envolvidos e levam mais tempo para executar. Pontos de recuperação de aplicativo consistente são recomendados para aplicativos e sistemas operacionais de banco de dados, como SQL.

### <a name="how-are-recovery-points-generated-and-saved"></a>Como os pontos de recuperação são gerados e salvos?
Para entender como o Site Recovery gear pontos de recuperação, vamos usar um exemplo da política de Replicação, que tem janela de retenção de ponto de recuperação de 24 horas e frequência de instantâneo de aplicativo consistente de 1 hora.
O Site Recovery cria um ponto de falha consistente a cada 5 minutos e o usuário não tem qualquer controle para alterar essa frequência. Portanto, durante a última uma hora, o usuário terá 12 pontos de falha consistente e 1 ponto de aplicativo consistente à sua escolha. Conforme o tempo decorre, o Site Recovery remove todos os pontos de recuperação além da última 1 hora e salva apenas uma recuperação de ponto por hora.
Para fins de ilustração, no instantâneo abaixo:


1. Para o período inferior à última 1 hora, há pontos de recuperação com a frequência de 5 minutos.
2. Para o período superior à última 1 hora, podemos ver que somente um ponto de recuperação por hora é mantido.

  ![geração de pontos de recuperação](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)


### <a name="how-far-back-can-i-recover"></a>Quanto tempo atrás é possível fazer a recuperação?
O ponto de recuperação mais antigo que você pode usar é de 72 horas.

### <a name="what-will-happen-if-i-have-a-replication-policy-of-24-hours-and-there-is-no-recovery-points-generation-due-to-some-issue-for-more-than-24-hours-does-my-previous-recovery-points-will-be-pruned"></a>O que acontecerá se eu tiver uma política de replicação de 24 horas e não houver nenhuma geração de pontos de recuperação devido a algum problema de mais de 24 horas. Meus pontos de recuperação anteriores serão removidos?
Não, o Site Recovery manterá todos os pontos de recuperação anteriores nesse caso. 

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Após a replicação ser habilitada em uma VM, como fazer para alterar a política de replicação? 
Vá para Cofre do Site Recovery > Infraestrutura do Site Recovery > Políticas de replicação. Selecione a política que você deseja editar e salve as alterações. Qualquer alteração também será aplicada a todas as replicações existentes. 

### <a name="are-all-the-recovery-points-complete-copy-of-the-vm-or-differential"></a>Todos os pontos de recuperação são uma cópia completa da VM ou diferencial?
No caso da replicação inicial, o primeiro ponto de recuperação gerado terá a cópia completa e todos os pontos de recuperação sucessivos terão alterações delta.

### <a name="does-increasing-recovery-points-retention-windows-increases-the-storage-cost"></a>Aumentar as janelas de retenção de pontos de recuperação aumenta o custo de armazenamento?
Sim, se você aumentar o período de retenção de 24 horas para 72 horas, o Site Recovery salvará os pontos de recuperação por 48 horas adicionais, o que será cobrado nos seus encargos de armazenamento. Por exemplo se um ponto de recuperação único tem alterações delta de 10 GB e custo por GB é de US $0,16 por mês, então, seria cobrado US $1,6 * 48 encargos adicionais por mês.

## <a name="failover"></a>Failover

### <a name="is-failover-automatic"></a>O failover é automático?

O failover não é automático. Você inicia failovers com um único clique no portal ou pode usar o [PowerShell](azure-to-azure-powershell.md) do Site Recovery para disparar um failover. 

### <a name="can-i-retain-public-ip-address-after-failover"></a>É possível reter o endereço IP público após o failover?

O endereço IP público do aplicativo de produção **não pode ser retido em failover**. As cargas de trabalho colocadas em operação como parte do processo de failover devem receber um recurso de IP público do Azure disponível na região de destino. Esta etapa pode ser realizada manualmente ou é automatizada com planos de recuperação. Consulte o [artigo](https://docs.microsoft.com/azure/site-recovery/concepts-public-ip-address-with-site-recovery#public-ip-address-assignment-using-recovery-plan) para atribuir o endereço IP Público usando o plano de recuperação.  

### <a name="can-i-retain-private-ip-address-during-failover"></a>É possível reter o endereço IP privado durante o failover?
Sim, é possível reter o endereço IP privado. Por padrão, quando você habilita a recuperação de desastre para VMs do Azure, o Site Recovery cria recursos de destino com base nas configurações do recurso de origem. Para VMs do Azure configuradas com endereços IP estáticos, o Site Recovery tentará provisionar o mesmo endereço IP para a VM de destino se ele não estiver em uso. Consulte o [artigo](site-recovery-retain-ip-azure-vm-failover.md) para reter o endereço IP privado em diferentes condições.

### <a name="after-failover-the-server-does-not-have-the-same-ip-address-as-the-source-vm-why-is-it-assigned-with-a-new-ip-address"></a>Após o failover, o servidor não tem o mesmo endereço IP que a VM de origem. Por que ele é atribuído com um novo endereço IP?

O Site Recovery tenta fornecer o endereço IP com base no melhor esforço no momento do failover. Se o endereço IP estiver sendo usado por alguma outra máquina virtual, o próximo endereço IP disponível será definido como o destino. Para obter uma explicação completa sobre como o Site Recovery trata o endereçamento, [examine este artigo](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-network-mapping#set-up-ip-addressing-for-target-vms).

### <a name="what-does-latestlowest-rpo-recovery-points-means"></a>O que significam pontos de recuperação mais recentes (menor RPO)?
Essa opção primeiro processa todos os dados que foram enviados ao serviço do Site Recovery, para criar um ponto de recuperação para cada VM antes do failover da VM. Essa opção fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM criada após o failover tem todos os dados replicados para o Site Recovery quando o failover for disparado.

### <a name="does-latest-lowest-rpo-recovery-points-have-impact-on-failover-rto"></a>Os pontos de recuperação mais recentes (menor RPO) afetam RTO de Failover?
Sim, como o Site Recovery processará todos os dados pendentes antes de fazer failover, essa opção terá um RTO superior em comparação com outros.

### <a name="what-does-latest-processed-option-in-recovery-points-mean"></a>O que significa a opção Mais recente processado nos pontos de recuperação?
Essa opção faz failover de todas as VMs no plano para o ponto de recuperação mais recente processado pelo Site Recovery. Para ver o ponto de recuperação mais recente de uma VM específica, verifique os Pontos de Recuperação Mais Recentes nas configurações da VM. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.

### <a name="if-im-replicating-between-two-azure-regions-what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>Se estiver replicando entre duas regiões do Azure, o que acontece se minha região primária passar por um interrupção inesperada?
É possível disparar um failover após a interrupção. O Site Recovery não precisa de conectividade da região primária para realizar o failover.

## <a name="recovery-plan"></a>Plano de Recuperação

### <a name="what-is-a-recovery-plan-"></a>O que é um Plano de Recuperação?
Os planos de recuperação no Site Recovery orquestram a recuperação de failover de VMs. Os planos de recuperação também ajudam a tornar a recuperação precisa, repetível e automatizada de forma consistente. Um plano de recuperação atende às seguintes necessidades do usuário:

- Definir um grupo de máquinas virtuais que fazem failover em conjunto.
- Definir as dependências entre as máquinas virtuais para que o aplicativo seja executado com precisão.
- Automatizar a recuperação, juntamente com ações manuais personalizadas para que as tarefas diferentes do failover das máquinas virtuais também possam ser realizadas.

[Saiba mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.

### <a name="how-does-sequencing-is-achieved-in-a-recovery-plan"></a>Como o sequenciamento é realizado em um Plano de Recuperação?

No Plano de Recuperação, é possível criar vários grupos para realizar o sequenciamento. Cada failover de grupo por vez, o que significa que as VMs que fazem parte do mesmo grupo farão failover juntas e, na sequência, outro grupo. Verifique como [modelar o aplicativo usando o plano de recuperação.](https://review.docs.microsoft.com/azure/site-recovery/recovery-plan-overview?branch=pr-en-us-61681#model-apps) 

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Como localizar o RTO de um plano de recuperação?
Para verificar o RTO de um plano de Recuperação, teste o failover do plano de recuperação e vá para os Trabalhos do Site Recovery.
Por exemplo, conforme mostrado abaixo, Plano de Recuperação de Teste do SAP levou 8 minutos e 59 segundos para fazer failover de todas as máquinas virtuais e executar as ações especificadas.

  ![com-erro](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>É possível adicionar runbooks de automação ao Plano de Recuperação?
Sim, é possível integrar runbooks de automação do Azure ao plano de recuperação. [Saiba mais](site-recovery-runbook-automation.md)

## <a name="reprotect-and-failback"></a>Proteger novamente e fazer Failback 

### <a name="after-doing-a-failover-from-primary-region-to-disaster-recovery-region-does-vms-in-a-dr-region-gets-protected-automatically"></a>Depois de fazer um failover de região primária para região de recuperação de desastre, as VMs em uma região de DR são protegidas automaticamente?
Não, ao fazer [failover](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) das VMs do Azure de uma região para outra, as VMs inicializam na região de DR em um estado desprotegido. Para fazer failback das VMs na região primária, é necessário [proteger novamente](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) as VMs na região secundária.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-secondary-region-to-primary-region"></a>No momento da nova proteção, o Site Recovery replica dados completos da região secundária para a região primária?
Isso depende da situação, por exemplo, se a VM da região de origem existir, somente as alterações entre o disco de origem e o disco de destino serão sincronizadas. Os diferenciais são computados comparando ambos os discos e, em seguida, transferidos. Isso geralmente levará algumas horas para ser concluído. Consulte o [artigo]( https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) para obter detalhes sobre o que acontece durante a nova proteção.

### <a name="how-much-time-does-it-take-to-failback"></a>Quanto tempo demora para fazer failback?
Depois que nova proteção for feita, geralmente é quantidade de tempo semelhante para o failover da região primária para região secundária. 

## <a name="security"></a>Segurança
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço do Site Recovery?
Não, o Site Recovery não intercepta dados replicados e não tem informações sobre o que está sendo executado nas máquinas virtuais. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.  
O Site Recovery é certificado pela ISO 27001:2013, 27018, HIPAA, DPA e está em processo de conclusão de avaliação dos padrões SOC2 e FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>O Site Recovery criptografa a replicação?
Sim, tanto criptografia em trânsito como [criptografia no Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) têm suporte.

## <a name="next-steps"></a>Próximas etapas
* [Analisar](azure-to-azure-support-matrix.md) os requisitos de suporte.
* [Configurar](azure-to-azure-tutorial-enable-replication.md) replicação de Azure para Azure.
